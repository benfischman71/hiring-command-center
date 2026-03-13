import React, { useState, useEffect, useRef } from 'react';

// ============================================================================
// HIRING COMMAND CENTER - COMPLETE VERSION
// ============================================================================
// All modules included:
// - Dashboard & Analytics
// - Positions with AI JD Builder
// - Candidates & Pipeline
// - Requirements Library
// - Scorecards with Weighted Scoring
// - Interview Notes & Recordings
// - Reference Check Tracking
// - Interviewer Portal (shareable)
// - Candidate Comparison
// - Email Notifications
// ============================================================================

// ----- CONFIGURATION -----
const SUPABASE_URL = 'https://your-project.supabase.co';
const SUPABASE_ANON_KEY = 'your-anon-key';
const DEMO_MODE = true;

// ----- COLORS & STYLES -----
const colors = {
  bg: '#0f172a',
  bgCard: 'rgba(30, 41, 59, 0.6)',
  bgInput: 'rgba(15, 23, 42, 0.5)',
  border: 'rgba(148, 163, 184, 0.1)',
  borderLight: 'rgba(148, 163, 184, 0.2)',
  text: '#e2e8f0',
  textMuted: '#94a3b8',
  textDim: '#64748b',
  primary: '#3b82f6',
  primaryGradient: 'linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%)',
  success: '#22c55e',
  warning: '#eab308',
  error: '#ef4444',
  purple: '#8b5cf6',
};

const css = {
  app: {
    minHeight: '100vh',
    background: `linear-gradient(135deg, ${colors.bg} 0%, #1e293b 50%, ${colors.bg} 100%)`,
    fontFamily: "'DM Sans', -apple-system, BlinkMacSystemFont, sans-serif",
    color: colors.text,
  },
  container: { maxWidth: '1400px', margin: '0 auto', padding: '24px' },
  card: {
    background: colors.bgCard,
    borderRadius: '16px',
    border: `1px solid ${colors.border}`,
    padding: '24px',
    marginBottom: '24px',
    backdropFilter: 'blur(10px)',
  },
  input: {
    width: '100%',
    padding: '12px 16px',
    borderRadius: '8px',
    border: `1px solid ${colors.borderLight}`,
    background: colors.bgInput,
    color: colors.text,
    fontSize: '14px',
    outline: 'none',
    boxSizing: 'border-box',
  },
  textarea: {
    width: '100%',
    padding: '12px 16px',
    borderRadius: '8px',
    border: `1px solid ${colors.borderLight}`,
    background: colors.bgInput,
    color: colors.text,
    fontSize: '14px',
    outline: 'none',
    resize: 'vertical',
    minHeight: '120px',
    fontFamily: 'inherit',
    boxSizing: 'border-box',
  },
  button: {
    padding: '10px 20px',
    borderRadius: '8px',
    border: 'none',
    background: colors.primaryGradient,
    color: '#fff',
    fontSize: '14px',
    fontWeight: '500',
    cursor: 'pointer',
    display: 'inline-flex',
    alignItems: 'center',
    gap: '8px',
  },
  buttonSecondary: {
    padding: '10px 20px',
    borderRadius: '8px',
    border: `1px solid ${colors.borderLight}`,
    background: 'rgba(30, 41, 59, 0.5)',
    color: colors.textMuted,
    fontSize: '14px',
    fontWeight: '500',
    cursor: 'pointer',
  },
  buttonSmall: {
    padding: '6px 12px',
    borderRadius: '6px',
    border: 'none',
    background: colors.primaryGradient,
    color: '#fff',
    fontSize: '12px',
    fontWeight: '500',
    cursor: 'pointer',
  },
  buttonDanger: {
    padding: '10px 20px',
    borderRadius: '8px',
    border: 'none',
    background: 'linear-gradient(135deg, #ef4444 0%, #dc2626 100%)',
    color: '#fff',
    fontSize: '14px',
    fontWeight: '500',
    cursor: 'pointer',
  },
  label: { display: 'block', fontSize: '13px', fontWeight: '500', color: colors.textMuted, marginBottom: '8px' },
  table: { width: '100%', borderCollapse: 'collapse' },
  th: {
    textAlign: 'left',
    padding: '12px 16px',
    fontSize: '12px',
    fontWeight: '600',
    color: colors.textDim,
    textTransform: 'uppercase',
    letterSpacing: '0.5px',
    borderBottom: `1px solid ${colors.border}`,
  },
  td: { padding: '16px', borderBottom: `1px solid rgba(148, 163, 184, 0.05)`, fontSize: '14px' },
  badge: (color) => ({
    display: 'inline-block',
    padding: '4px 12px',
    borderRadius: '20px',
    fontSize: '12px',
    fontWeight: '500',
    background: color === 'green' ? 'rgba(34, 197, 94, 0.1)' :
                color === 'blue' ? 'rgba(59, 130, 246, 0.1)' :
                color === 'yellow' ? 'rgba(234, 179, 8, 0.1)' :
                color === 'purple' ? 'rgba(139, 92, 246, 0.1)' :
                color === 'red' ? 'rgba(239, 68, 68, 0.1)' :
                'rgba(148, 163, 184, 0.1)',
    color: color === 'green' ? colors.success :
           color === 'blue' ? colors.primary :
           color === 'yellow' ? colors.warning :
           color === 'purple' ? colors.purple :
           color === 'red' ? colors.error :
           colors.textMuted,
  }),
  modal: {
    position: 'fixed',
    inset: 0,
    background: 'rgba(0, 0, 0, 0.8)',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    zIndex: 1000,
    backdropFilter: 'blur(4px)',
  },
  modalContent: {
    background: '#1e293b',
    borderRadius: '16px',
    border: `1px solid ${colors.border}`,
    padding: '32px',
    width: '90%',
    maxWidth: '800px',
    maxHeight: '90vh',
    overflow: 'auto',
  },
  nav: {
    display: 'flex',
    gap: '4px',
    background: 'rgba(30, 41, 59, 0.5)',
    padding: '6px',
    borderRadius: '12px',
    border: `1px solid ${colors.border}`,
    flexWrap: 'wrap',
  },
  navBtn: (active) => ({
    padding: '8px 16px',
    borderRadius: '8px',
    border: 'none',
    background: active ? colors.primaryGradient : 'transparent',
    color: active ? '#fff' : colors.textMuted,
    fontSize: '13px',
    fontWeight: '500',
    cursor: 'pointer',
    whiteSpace: 'nowrap',
  }),
  statCard: {
    background: colors.bgCard,
    borderRadius: '12px',
    border: `1px solid ${colors.border}`,
    padding: '20px',
    textAlign: 'center',
  },
  statValue: {
    fontSize: '28px',
    fontWeight: '700',
    background: colors.primaryGradient,
    WebkitBackgroundClip: 'text',
    WebkitTextFillColor: 'transparent',
  },
  avatar: (size = 40) => ({
    width: `${size}px`,
    height: `${size}px`,
    borderRadius: '50%',
    background: colors.primaryGradient,
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    fontSize: `${size * 0.4}px`,
    fontWeight: '600',
    color: '#fff',
    flexShrink: 0,
  }),
  ratingBtn: (active, score) => ({
    width: '36px',
    height: '36px',
    borderRadius: '8px',
    border: active ? 'none' : `1px solid ${colors.borderLight}`,
    background: active
      ? score >= 4 ? 'linear-gradient(135deg, #22c55e, #16a34a)'
      : score >= 3 ? 'linear-gradient(135deg, #3b82f6, #2563eb)'
      : score >= 2 ? 'linear-gradient(135deg, #eab308, #ca8a04)'
      : 'linear-gradient(135deg, #ef4444, #dc2626)'
      : 'rgba(30, 41, 59, 0.5)',
    color: active ? '#fff' : colors.textDim,
    fontSize: '13px',
    fontWeight: '600',
    cursor: 'pointer',
  }),
  chip: (selected) => ({
    display: 'inline-flex',
    alignItems: 'center',
    gap: '8px',
    padding: '8px 14px',
    borderRadius: '8px',
    border: selected ? `2px solid ${colors.primary}` : `1px solid ${colors.borderLight}`,
    background: selected ? 'rgba(59, 130, 246, 0.1)' : 'rgba(30, 41, 59, 0.5)',
    color: selected ? colors.primary : colors.textMuted,
    fontSize: '13px',
    cursor: 'pointer',
    marginRight: '8px',
    marginBottom: '8px',
  }),
  tabs: {
    display: 'flex',
    gap: '4px',
    marginBottom: '24px',
    borderBottom: `1px solid ${colors.border}`,
    paddingBottom: '12px',
  },
  tab: (active) => ({
    padding: '8px 16px',
    background: active ? 'rgba(59, 130, 246, 0.1)' : 'transparent',
    border: 'none',
    borderRadius: '8px',
    color: active ? colors.primary : colors.textMuted,
    fontSize: '14px',
    fontWeight: '500',
    cursor: 'pointer',
  }),
  timeline: {
    position: 'relative',
    paddingLeft: '24px',
  },
  timelineItem: {
    position: 'relative',
    paddingBottom: '20px',
    paddingLeft: '20px',
    borderLeft: `2px solid ${colors.border}`,
  },
  timelineDot: {
    position: 'absolute',
    left: '-7px',
    top: '4px',
    width: '12px',
    height: '12px',
    borderRadius: '50%',
    background: colors.primary,
    border: '2px solid #1e293b',
  },
};

// ============================================================================
// UTILITY COMPONENTS
// ============================================================================

const Modal = ({ isOpen, onClose, title, children, wide, extraWide }) => {
  if (!isOpen) return null;
  return (
    <div style={css.modal} onClick={onClose}>
      <div 
        style={{ ...css.modalContent, maxWidth: extraWide ? '1200px' : wide ? '900px' : '600px' }} 
        onClick={e => e.stopPropagation()}
      >
        <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '24px' }}>
          <h2 style={{ fontSize: '20px', fontWeight: '600', color: '#f1f5f9' }}>{title}</h2>
          <button onClick={onClose} style={{ ...css.buttonSecondary, padding: '8px 12px' }}>✕</button>
        </div>
        {children}
      </div>
    </div>
  );
};

const RatingScale = ({ value, onChange, size = 'normal' }) => {
  const labels = ['', 'Does Not Meet', 'Partially Meets', 'Meets', 'Exceeds', 'Far Exceeds'];
  const btnSize = size === 'small' ? 28 : 36;
  return (
    <div style={{ display: 'flex', gap: '6px', alignItems: 'center' }}>
      {[1, 2, 3, 4, 5].map(r => (
        <button 
          key={r} 
          style={{ ...css.ratingBtn(value === r, r), width: `${btnSize}px`, height: `${btnSize}px` }}
          onClick={() => onChange(r)} 
          title={labels[r]}
        >
          {r}
        </button>
      ))}
      {value && size !== 'small' && (
        <span style={{ marginLeft: '8px', fontSize: '12px', color: colors.textDim }}>{labels[value]}</span>
      )}
    </div>
  );
};

const ScoreDisplay = ({ score, showBar = true }) => {
  if (!score && score !== 0) return <span style={{ color: colors.textDim }}>—</span>;
  const color = score >= 4 ? colors.success : score >= 3 ? colors.primary : score >= 2 ? colors.warning : colors.error;
  return (
    <div style={{ display: 'flex', alignItems: 'center', gap: '10px' }}>
      {showBar && (
        <div style={{ width: '60px', height: '6px', borderRadius: '3px', background: 'rgba(148, 163, 184, 0.1)', overflow: 'hidden' }}>
          <div style={{ width: `${(score / 5) * 100}%`, height: '100%', background: color, borderRadius: '3px' }} />
        </div>
      )}
      <span style={{ fontWeight: '600', color, fontSize: '14px' }}>{typeof score === 'number' ? score.toFixed(1) : score}</span>
    </div>
  );
};

const Tabs = ({ tabs, active, onChange }) => (
  <div style={css.tabs}>
    {tabs.map(t => (
      <button key={t.id} style={css.tab(active === t.id)} onClick={() => onChange(t.id)}>
        {t.icon && <span style={{ marginRight: '6px' }}>{t.icon}</span>}
        {t.label}
      </button>
    ))}
  </div>
);

const EmptyState = ({ icon, title, description, action }) => (
  <div style={{ textAlign: 'center', padding: '60px 20px' }}>
    <div style={{ fontSize: '48px', marginBottom: '16px' }}>{icon}</div>
    <h3 style={{ fontSize: '18px', fontWeight: '600', color: '#f1f5f9', marginBottom: '8px' }}>{title}</h3>
    <p style={{ color: colors.textDim, marginBottom: '20px', maxWidth: '400px', margin: '0 auto 20px' }}>{description}</p>
    {action}
  </div>
);

// ============================================================================
// JD BUILDER COMPONENT
// ============================================================================
const JDBuilder = ({ onSave, onClose, requirements }) => {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({
    title: '',
    department: '',
    level: 'senior',
    location: '',
    remote: 'hybrid',
    reportingTo: '',
    teamSize: '',
    companyDescription: '',
    roleOverview: '',
    responsibilities: [''],
    requiredQualifications: [''],
    preferredQualifications: [''],
    benefits: [''],
    salary: '',
  });
  const [generatedJD, setGeneratedJD] = useState('');
  const [extractedReqs, setExtractedReqs] = useState([]);
  const [isGenerating, setIsGenerating] = useState(false);

  const updateArray = (field, index, value) => {
    const arr = [...formData[field]];
    arr[index] = value;
    setFormData({ ...formData, [field]: arr });
  };

  const addToArray = (field) => {
    setFormData({ ...formData, [field]: [...formData[field], ''] });
  };

  const removeFromArray = (field, index) => {
    const arr = formData[field].filter((_, i) => i !== index);
    setFormData({ ...formData, [field]: arr.length ? arr : [''] });
  };

  const generateJD = async () => {
    setIsGenerating(true);
    
    // Simulate AI generation (in production, call Claude API)
    await new Promise(r => setTimeout(r, 1500));
    
    const jd = `# ${formData.title}

## About the Role
${formData.roleOverview || `We are seeking an experienced ${formData.title} to join our ${formData.department} team. This ${formData.level}-level position reports to ${formData.reportingTo || 'the department head'} and will play a critical role in driving our company's success.`}

## Location
${formData.location || 'Flexible'} - ${formData.remote === 'remote' ? 'Fully Remote' : formData.remote === 'hybrid' ? 'Hybrid (2-3 days in office)' : 'On-site'}

${formData.teamSize ? `## Team\nYou will be leading/joining a team of ${formData.teamSize} professionals.\n` : ''}

## Key Responsibilities
${formData.responsibilities.filter(r => r).map(r => `• ${r}`).join('\n')}

## Required Qualifications
${formData.requiredQualifications.filter(r => r).map(r => `• ${r}`).join('\n')}

## Preferred Qualifications
${formData.preferredQualifications.filter(r => r).map(r => `• ${r}`).join('\n')}

## What We Offer
${formData.benefits.filter(b => b).map(b => `• ${b}`).join('\n')}

${formData.salary ? `## Compensation\n${formData.salary}\n` : ''}

${formData.companyDescription ? `## About Us\n${formData.companyDescription}` : ''}
`;
    
    setGeneratedJD(jd);
    
    // Extract requirements from JD
    const patterns = [
      { match: /\d+\+?\s*years?/i, req: '3+ years in similar role' },
      { match: /early.?stage|startup/i, req: 'Early stage company experience' },
      { match: /dtc|direct.?to.?consumer/i, req: 'Direct-to-consumer experience' },
      { match: /ecommerce|e-commerce/i, req: 'E-commerce background' },
      { match: /team|manage|leadership/i, req: 'Has managed teams of 5+' },
      { match: /cross.?functional/i, req: 'Cross-functional leadership' },
      { match: /board|investor/i, req: 'Board presentation experience' },
      { match: /financial|finance|fp&a/i, req: 'Financial modeling proficiency' },
      { match: /cac|ltv|customer acquisition/i, req: 'Familiarity with CAC/LTV metrics' },
      { match: /budget/i, req: 'Budgeting experience' },
      { match: /strategic/i, req: 'Strategic thinking' },
      { match: /communication/i, req: 'Strong communication skills' },
    ];
    
    const found = patterns
      .filter(p => p.match.test(jd))
      .map(p => requirements.find(r => r.name === p.req) || { id: Date.now() + Math.random(), name: p.req, category: 'Experience' })
      .filter((r, i, arr) => arr.findIndex(x => x.name === r.name) === i);
    
    setExtractedReqs(found);
    setIsGenerating(false);
    setStep(3);
  };

  return (
    <div>
      {/* Progress Steps */}
      <div style={{ display: 'flex', gap: '8px', marginBottom: '32px' }}>
        {[1, 2, 3].map(s => (
          <div key={s} style={{ flex: 1, height: '4px', borderRadius: '2px', background: s <= step ? colors.primary : colors.border }} />
        ))}
      </div>

      {/* Step 1: Basic Info */}
      {step === 1 && (
        <div>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '20px', color: '#f1f5f9' }}>📝 Basic Information</h3>
          
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Job Title *</label>
              <input style={css.input} placeholder="VP of Finance" value={formData.title} onChange={e => setFormData({ ...formData, title: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Department</label>
              <input style={css.input} placeholder="Finance" value={formData.department} onChange={e => setFormData({ ...formData, department: e.target.value })} />
            </div>
          </div>

          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Level</label>
              <select style={css.input} value={formData.level} onChange={e => setFormData({ ...formData, level: e.target.value })}>
                <option value="entry">Entry Level</option>
                <option value="mid">Mid Level</option>
                <option value="senior">Senior</option>
                <option value="lead">Lead</option>
                <option value="director">Director</option>
                <option value="vp">VP</option>
                <option value="c-level">C-Level</option>
              </select>
            </div>
            <div>
              <label style={css.label}>Location</label>
              <input style={css.input} placeholder="New York, NY" value={formData.location} onChange={e => setFormData({ ...formData, location: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Remote Policy</label>
              <select style={css.input} value={formData.remote} onChange={e => setFormData({ ...formData, remote: e.target.value })}>
                <option value="onsite">On-site</option>
                <option value="hybrid">Hybrid</option>
                <option value="remote">Fully Remote</option>
              </select>
            </div>
          </div>

          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Reports To</label>
              <input style={css.input} placeholder="CEO" value={formData.reportingTo} onChange={e => setFormData({ ...formData, reportingTo: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Team Size</label>
              <input style={css.input} placeholder="5-10" value={formData.teamSize} onChange={e => setFormData({ ...formData, teamSize: e.target.value })} />
            </div>
          </div>

          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Role Overview</label>
            <textarea style={{ ...css.textarea, minHeight: '80px' }} placeholder="Brief description of the role and its impact..." value={formData.roleOverview} onChange={e => setFormData({ ...formData, roleOverview: e.target.value })} />
          </div>

          <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px', marginTop: '24px' }}>
            <button style={css.buttonSecondary} onClick={onClose}>Cancel</button>
            <button style={css.button} onClick={() => setStep(2)}>Next: Details →</button>
          </div>
        </div>
      )}

      {/* Step 2: Responsibilities & Qualifications */}
      {step === 2 && (
        <div>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '20px', color: '#f1f5f9' }}>📋 Responsibilities & Qualifications</h3>
          
          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>Key Responsibilities</label>
            {formData.responsibilities.map((r, i) => (
              <div key={i} style={{ display: 'flex', gap: '8px', marginBottom: '8px' }}>
                <input style={css.input} placeholder="Own the full P&L..." value={r} onChange={e => updateArray('responsibilities', i, e.target.value)} />
                <button style={{ ...css.buttonSecondary, padding: '8px 12px' }} onClick={() => removeFromArray('responsibilities', i)}>×</button>
              </div>
            ))}
            <button style={{ ...css.buttonSecondary, fontSize: '13px' }} onClick={() => addToArray('responsibilities')}>+ Add Responsibility</button>
          </div>

          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>Required Qualifications</label>
            {formData.requiredQualifications.map((r, i) => (
              <div key={i} style={{ display: 'flex', gap: '8px', marginBottom: '8px' }}>
                <input style={css.input} placeholder="5+ years of experience..." value={r} onChange={e => updateArray('requiredQualifications', i, e.target.value)} />
                <button style={{ ...css.buttonSecondary, padding: '8px 12px' }} onClick={() => removeFromArray('requiredQualifications', i)}>×</button>
              </div>
            ))}
            <button style={{ ...css.buttonSecondary, fontSize: '13px' }} onClick={() => addToArray('requiredQualifications')}>+ Add Qualification</button>
          </div>

          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>Preferred Qualifications</label>
            {formData.preferredQualifications.map((r, i) => (
              <div key={i} style={{ display: 'flex', gap: '8px', marginBottom: '8px' }}>
                <input style={css.input} placeholder="MBA preferred..." value={r} onChange={e => updateArray('preferredQualifications', i, e.target.value)} />
                <button style={{ ...css.buttonSecondary, padding: '8px 12px' }} onClick={() => removeFromArray('preferredQualifications', i)}>×</button>
              </div>
            ))}
            <button style={{ ...css.buttonSecondary, fontSize: '13px' }} onClick={() => addToArray('preferredQualifications')}>+ Add Qualification</button>
          </div>

          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>Benefits & Perks</label>
            {formData.benefits.map((b, i) => (
              <div key={i} style={{ display: 'flex', gap: '8px', marginBottom: '8px' }}>
                <input style={css.input} placeholder="Competitive salary..." value={b} onChange={e => updateArray('benefits', i, e.target.value)} />
                <button style={{ ...css.buttonSecondary, padding: '8px 12px' }} onClick={() => removeFromArray('benefits', i)}>×</button>
              </div>
            ))}
            <button style={{ ...css.buttonSecondary, fontSize: '13px' }} onClick={() => addToArray('benefits')}>+ Add Benefit</button>
          </div>

          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Salary Range (optional)</label>
            <input style={css.input} placeholder="$150,000 - $200,000 + equity" value={formData.salary} onChange={e => setFormData({ ...formData, salary: e.target.value })} />
          </div>

          <div style={{ display: 'flex', justifyContent: 'space-between', marginTop: '24px' }}>
            <button style={css.buttonSecondary} onClick={() => setStep(1)}>← Back</button>
            <button style={css.button} onClick={generateJD} disabled={isGenerating}>
              {isGenerating ? '🤖 Generating...' : '🤖 Generate JD'}
            </button>
          </div>
        </div>
      )}

      {/* Step 3: Review & Save */}
      {step === 3 && (
        <div>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '20px', color: '#f1f5f9' }}>✨ Review Generated JD</h3>
          
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '24px' }}>
            <div>
              <label style={css.label}>Generated Job Description</label>
              <textarea 
                style={{ ...css.textarea, minHeight: '400px', fontSize: '13px' }} 
                value={generatedJD} 
                onChange={e => setGeneratedJD(e.target.value)} 
              />
            </div>
            
            <div>
              <label style={css.label}>🎯 Extracted Requirements</label>
              <p style={{ fontSize: '12px', color: colors.textDim, marginBottom: '12px' }}>
                AI detected these requirements from your JD. Select the ones to include:
              </p>
              <div style={{ display: 'flex', flexWrap: 'wrap' }}>
                {extractedReqs.map(r => (
                  <div key={r.id} style={css.chip(true)}>
                    {r.name}
                  </div>
                ))}
              </div>
              
              {extractedReqs.length === 0 && (
                <p style={{ color: colors.textDim, fontSize: '13px', padding: '20px', textAlign: 'center' }}>
                  No requirements automatically detected. You can add them manually.
                </p>
              )}
            </div>
          </div>

          <div style={{ display: 'flex', justifyContent: 'space-between', marginTop: '24px' }}>
            <button style={css.buttonSecondary} onClick={() => setStep(2)}>← Back to Edit</button>
            <button style={css.button} onClick={() => onSave({ 
              title: formData.title, 
              department: formData.department, 
              jd: generatedJD, 
              requirements: extractedReqs.map(r => ({ ...r, weight: 2, mustHave: false }))
            })}>
              Save Position →
            </button>
          </div>
        </div>
      )}
    </div>
  );
};

// ============================================================================
// INTERVIEW NOTES COMPONENT
// ============================================================================
const InterviewNotes = ({ candidate, interview, onSave, onClose }) => {
  const [notes, setNotes] = useState(interview?.notes || []);
  const [currentNote, setCurrentNote] = useState('');
  const [isRecording, setIsRecording] = useState(false);
  const [elapsedTime, setElapsedTime] = useState(0);
  const [interviewType, setInterviewType] = useState(interview?.type || 'video');
  const timerRef = useRef(null);

  useEffect(() => {
    if (isRecording) {
      timerRef.current = setInterval(() => {
        setElapsedTime(t => t + 1);
      }, 1000);
    } else {
      clearInterval(timerRef.current);
    }
    return () => clearInterval(timerRef.current);
  }, [isRecording]);

  const formatTime = (seconds) => {
    const m = Math.floor(seconds / 60);
    const s = seconds % 60;
    return `${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
  };

  const addNote = (isHighlight = false) => {
    if (!currentNote.trim()) return;
    const note = {
      id: Date.now(),
      text: currentNote,
      timestamp: elapsedTime,
      isHighlight,
      createdAt: new Date().toISOString(),
    };
    setNotes([...notes, note]);
    setCurrentNote('');
  };

  const deleteNote = (id) => {
    setNotes(notes.filter(n => n.id !== id));
  };

  return (
    <div>
      {/* Interview Header */}
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '24px', padding: '16px', background: 'rgba(59, 130, 246, 0.1)', borderRadius: '12px' }}>
        <div style={{ display: 'flex', alignItems: 'center', gap: '16px' }}>
          <div style={css.avatar(48)}>{candidate.name.split(' ').map(n => n[0]).join('')}</div>
          <div>
            <h3 style={{ fontSize: '16px', fontWeight: '600', color: '#f1f5f9' }}>{candidate.name}</h3>
            <p style={{ fontSize: '13px', color: colors.textDim }}>{candidate.email}</p>
          </div>
        </div>
        <div style={{ display: 'flex', alignItems: 'center', gap: '16px' }}>
          <select style={{ ...css.input, width: 'auto' }} value={interviewType} onChange={e => setInterviewType(e.target.value)}>
            <option value="phone">📞 Phone Screen</option>
            <option value="video">📹 Video Call</option>
            <option value="onsite">🏢 On-site</option>
            <option value="technical">💻 Technical</option>
            <option value="culture">🤝 Culture Fit</option>
          </select>
        </div>
      </div>

      {/* Timer & Recording Controls */}
      <div style={{ display: 'flex', justifyContent: 'center', alignItems: 'center', gap: '24px', marginBottom: '24px', padding: '20px', background: colors.bgCard, borderRadius: '12px' }}>
        <div style={{ fontSize: '48px', fontWeight: '700', fontFamily: 'monospace', color: isRecording ? colors.primary : colors.textMuted }}>
          {formatTime(elapsedTime)}
        </div>
        <div style={{ display: 'flex', gap: '12px' }}>
          <button 
            style={{ ...css.button, background: isRecording ? 'linear-gradient(135deg, #ef4444, #dc2626)' : colors.primaryGradient }}
            onClick={() => setIsRecording(!isRecording)}
          >
            {isRecording ? '⏸ Pause' : '▶ Start Interview'}
          </button>
          {elapsedTime > 0 && (
            <button style={css.buttonSecondary} onClick={() => { setIsRecording(false); setElapsedTime(0); }}>
              ↺ Reset
            </button>
          )}
        </div>
      </div>

      {/* Note Input */}
      <div style={{ marginBottom: '24px' }}>
        <div style={{ display: 'flex', gap: '12px' }}>
          <input 
            style={{ ...css.input, flex: 1 }}
            placeholder="Type a note... (Press Enter to add, Shift+Enter for highlight)"
            value={currentNote}
            onChange={e => setCurrentNote(e.target.value)}
            onKeyDown={e => {
              if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                addNote(false);
              } else if (e.key === 'Enter' && e.shiftKey) {
                e.preventDefault();
                addNote(true);
              }
            }}
          />
          <button style={css.button} onClick={() => addNote(false)}>+ Note</button>
          <button style={{ ...css.button, background: 'linear-gradient(135deg, #eab308, #ca8a04)' }} onClick={() => addNote(true)}>⭐ Highlight</button>
        </div>
        <p style={{ fontSize: '11px', color: colors.textDim, marginTop: '8px' }}>
          💡 Tip: Press Enter for regular note, Shift+Enter for highlight
        </p>
      </div>

      {/* Notes Timeline */}
      <div style={{ marginBottom: '24px' }}>
        <h4 style={{ fontSize: '14px', fontWeight: '600', color: colors.textMuted, marginBottom: '16px' }}>📝 Interview Notes</h4>
        
        {notes.length === 0 ? (
          <p style={{ color: colors.textDim, textAlign: 'center', padding: '40px' }}>
            No notes yet. Start the interview and add notes as you go!
          </p>
        ) : (
          <div style={css.timeline}>
            {notes.sort((a, b) => a.timestamp - b.timestamp).map(note => (
              <div key={note.id} style={{ ...css.timelineItem, borderLeftColor: note.isHighlight ? colors.warning : colors.border }}>
                <div style={{ ...css.timelineDot, background: note.isHighlight ? colors.warning : colors.primary }} />
                <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'flex-start' }}>
                  <div>
                    <span style={{ fontSize: '12px', color: colors.primary, fontWeight: '600', marginRight: '12px' }}>
                      {formatTime(note.timestamp)}
                    </span>
                    {note.isHighlight && <span style={{ ...css.badge('yellow'), marginRight: '8px' }}>⭐ Key Moment</span>}
                    <span style={{ color: '#f1f5f9' }}>{note.text}</span>
                  </div>
                  <button 
                    style={{ background: 'none', border: 'none', color: colors.textDim, cursor: 'pointer', fontSize: '16px' }}
                    onClick={() => deleteNote(note.id)}
                  >
                    ×
                  </button>
                </div>
              </div>
            ))}
          </div>
        )}
      </div>

      {/* Recording Upload */}
      <div style={{ marginBottom: '24px', padding: '20px', border: `2px dashed ${colors.borderLight}`, borderRadius: '12px', textAlign: 'center' }}>
        <div style={{ fontSize: '32px', marginBottom: '8px' }}>🎙️</div>
        <p style={{ color: colors.textMuted, marginBottom: '12px' }}>Drop recording file here or click to upload</p>
        <input type="file" accept="audio/*,video/*" style={{ display: 'none' }} id="recording-upload" />
        <label htmlFor="recording-upload" style={{ ...css.buttonSecondary, cursor: 'pointer', display: 'inline-block' }}>
          Upload Recording
        </label>
      </div>

      {/* Save */}
      <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <button style={css.buttonSecondary} onClick={onClose}>Cancel</button>
        <button style={css.button} onClick={() => onSave({ notes, duration: elapsedTime, type: interviewType })}>
          Save Interview Notes
        </button>
      </div>
    </div>
  );
};

// ============================================================================
// REFERENCE CHECK COMPONENT
// ============================================================================
const ReferenceCheckManager = ({ candidate, references, onSave, onClose }) => {
  const [refs, setRefs] = useState(references || []);
  const [showAddForm, setShowAddForm] = useState(false);
  const [newRef, setNewRef] = useState({
    name: '',
    title: '',
    company: '',
    email: '',
    phone: '',
    relationship: 'manager',
  });
  const [selectedRef, setSelectedRef] = useState(null);
  const [refNotes, setRefNotes] = useState('');
  const [refRating, setRefRating] = useState(null);
  const [wouldRehire, setWouldRehire] = useState(null);
  const [redFlags, setRedFlags] = useState('');

  const addReference = () => {
    const ref = {
      id: Date.now().toString(),
      ...newRef,
      status: 'pending',
      createdAt: new Date().toISOString(),
    };
    setRefs([...refs, ref]);
    setNewRef({ name: '', title: '', company: '', email: '', phone: '', relationship: 'manager' });
    setShowAddForm(false);
  };

  const updateRefStatus = (id, status) => {
    setRefs(refs.map(r => r.id === id ? { ...r, status } : r));
  };

  const saveRefCheck = () => {
    setRefs(refs.map(r => r.id === selectedRef.id ? {
      ...r,
      status: 'completed',
      notes: refNotes,
      rating: refRating,
      wouldRehire,
      redFlags,
      completedAt: new Date().toISOString(),
    } : r));
    setSelectedRef(null);
    setRefNotes('');
    setRefRating(null);
    setWouldRehire(null);
    setRedFlags('');
  };

  const statusColors = {
    pending: 'yellow',
    scheduled: 'blue',
    completed: 'green',
    unable: 'red',
  };

  return (
    <div>
      {/* Candidate Header */}
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '24px', padding: '16px', background: 'rgba(59, 130, 246, 0.1)', borderRadius: '12px' }}>
        <div style={{ display: 'flex', alignItems: 'center', gap: '16px' }}>
          <div style={css.avatar(48)}>{candidate.name.split(' ').map(n => n[0]).join('')}</div>
          <div>
            <h3 style={{ fontSize: '16px', fontWeight: '600', color: '#f1f5f9' }}>{candidate.name}</h3>
            <p style={{ fontSize: '13px', color: colors.textDim }}>Reference Checks</p>
          </div>
        </div>
        <button style={css.button} onClick={() => setShowAddForm(true)}>+ Add Reference</button>
      </div>

      {/* Add Reference Form */}
      {showAddForm && (
        <div style={{ ...css.card, marginBottom: '24px' }}>
          <h4 style={{ fontSize: '14px', fontWeight: '600', marginBottom: '16px', color: '#f1f5f9' }}>Add New Reference</h4>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Name *</label>
              <input style={css.input} placeholder="John Smith" value={newRef.name} onChange={e => setNewRef({ ...newRef, name: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Relationship</label>
              <select style={css.input} value={newRef.relationship} onChange={e => setNewRef({ ...newRef, relationship: e.target.value })}>
                <option value="manager">Former Manager</option>
                <option value="peer">Peer / Colleague</option>
                <option value="direct_report">Direct Report</option>
                <option value="client">Client</option>
                <option value="other">Other</option>
              </select>
            </div>
          </div>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Title</label>
              <input style={css.input} placeholder="VP of Engineering" value={newRef.title} onChange={e => setNewRef({ ...newRef, title: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Company</label>
              <input style={css.input} placeholder="Acme Corp" value={newRef.company} onChange={e => setNewRef({ ...newRef, company: e.target.value })} />
            </div>
          </div>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '16px' }}>
            <div>
              <label style={css.label}>Email</label>
              <input style={css.input} type="email" placeholder="john@example.com" value={newRef.email} onChange={e => setNewRef({ ...newRef, email: e.target.value })} />
            </div>
            <div>
              <label style={css.label}>Phone</label>
              <input style={css.input} placeholder="+1 555 123 4567" value={newRef.phone} onChange={e => setNewRef({ ...newRef, phone: e.target.value })} />
            </div>
          </div>
          <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
            <button style={css.buttonSecondary} onClick={() => setShowAddForm(false)}>Cancel</button>
            <button style={css.button} onClick={addReference}>Add Reference</button>
          </div>
        </div>
      )}

      {/* References List */}
      {refs.length === 0 ? (
        <EmptyState
          icon="📋"
          title="No References Yet"
          description="Add references for this candidate to track your reference checks."
          action={<button style={css.button} onClick={() => setShowAddForm(true)}>+ Add First Reference</button>}
        />
      ) : (
        <div style={{ display: 'grid', gap: '16px' }}>
          {refs.map(ref => (
            <div key={ref.id} style={{ ...css.card, margin: 0 }}>
              <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'flex-start' }}>
                <div>
                  <div style={{ display: 'flex', alignItems: 'center', gap: '12px', marginBottom: '8px' }}>
                    <h4 style={{ fontSize: '15px', fontWeight: '600', color: '#f1f5f9' }}>{ref.name}</h4>
                    <span style={css.badge(statusColors[ref.status])}>{ref.status}</span>
                  </div>
                  <p style={{ fontSize: '13px', color: colors.textMuted, marginBottom: '4px' }}>
                    {ref.title} at {ref.company}
                  </p>
                  <p style={{ fontSize: '12px', color: colors.textDim }}>
                    {ref.relationship === 'manager' ? '👔 Former Manager' :
                     ref.relationship === 'peer' ? '🤝 Peer' :
                     ref.relationship === 'direct_report' ? '👥 Direct Report' :
                     ref.relationship === 'client' ? '💼 Client' : '📋 Other'}
                  </p>
                  
                  {ref.status === 'completed' && (
                    <div style={{ marginTop: '12px', padding: '12px', background: 'rgba(34, 197, 94, 0.1)', borderRadius: '8px' }}>
                      <div style={{ display: 'flex', gap: '24px', marginBottom: '8px' }}>
                        <div>
                          <span style={{ fontSize: '11px', color: colors.textDim }}>Rating:</span>
                          <ScoreDisplay score={ref.rating} showBar={false} />
                        </div>
                        <div>
                          <span style={{ fontSize: '11px', color: colors.textDim }}>Would Rehire:</span>
                          <span style={{ marginLeft: '8px', color: ref.wouldRehire ? colors.success : colors.error }}>
                            {ref.wouldRehire ? '✓ Yes' : '✗ No'}
                          </span>
                        </div>
                      </div>
                      {ref.notes && <p style={{ fontSize: '13px', color: colors.textMuted }}>{ref.notes}</p>}
                      {ref.redFlags && (
                        <p style={{ fontSize: '13px', color: colors.error, marginTop: '8px' }}>
                          🚩 {ref.redFlags}
                        </p>
                      )}
                    </div>
                  )}
                </div>
                
                <div style={{ display: 'flex', gap: '8px' }}>
                  {ref.status === 'pending' && (
                    <>
                      <button style={css.buttonSmall} onClick={() => updateRefStatus(ref.id, 'scheduled')}>Schedule</button>
                      <button style={css.buttonSmall} onClick={() => setSelectedRef(ref)}>Complete</button>
                    </>
                  )}
                  {ref.status === 'scheduled' && (
                    <button style={css.buttonSmall} onClick={() => setSelectedRef(ref)}>Record Results</button>
                  )}
                  {ref.email && (
                    <a href={`mailto:${ref.email}`} style={{ ...css.buttonSecondary, padding: '6px 12px', textDecoration: 'none', fontSize: '12px' }}>
                      ✉️ Email
                    </a>
                  )}
                </div>
              </div>
            </div>
          ))}
        </div>
      )}

      {/* Reference Check Form Modal */}
      {selectedRef && (
        <Modal isOpen={true} onClose={() => setSelectedRef(null)} title={`Reference Check: ${selectedRef.name}`}>
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Overall Rating</label>
            <RatingScale value={refRating} onChange={setRefRating} />
          </div>
          
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Would They Rehire?</label>
            <div style={{ display: 'flex', gap: '12px' }}>
              <button 
                style={{ ...css.chip(wouldRehire === true), padding: '12px 24px' }}
                onClick={() => setWouldRehire(true)}
              >
                ✓ Yes
              </button>
              <button 
                style={{ ...css.chip(wouldRehire === false), padding: '12px 24px' }}
                onClick={() => setWouldRehire(false)}
              >
                ✗ No
              </button>
            </div>
          </div>
          
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Notes</label>
            <textarea style={css.textarea} placeholder="Key points from the conversation..." value={refNotes} onChange={e => setRefNotes(e.target.value)} />
          </div>
          
          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>🚩 Red Flags (if any)</label>
            <textarea style={{ ...css.textarea, minHeight: '80px' }} placeholder="Any concerns raised..." value={redFlags} onChange={e => setRedFlags(e.target.value)} />
          </div>
          
          <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
            <button style={css.buttonSecondary} onClick={() => setSelectedRef(null)}>Cancel</button>
            <button style={css.button} onClick={saveRefCheck}>Save Reference Check</button>
          </div>
        </Modal>
      )}

      {/* Save All */}
      <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px', marginTop: '24px' }}>
        <button style={css.buttonSecondary} onClick={onClose}>Close</button>
        <button style={css.button} onClick={() => onSave(refs)}>Save All References</button>
      </div>
    </div>
  );
};

// ============================================================================
// INTERVIEWER PORTAL (Shareable Scorecard)
// ============================================================================
const InterviewerPortal = ({ position, candidate, onSubmit }) => {
  const [evaluatorName, setEvaluatorName] = useState('');
  const [evaluatorEmail, setEvaluatorEmail] = useState('');
  const [ratings, setRatings] = useState({});
  const [comments, setComments] = useState({});
  const [strengths, setStrengths] = useState('');
  const [concerns, setConcerns] = useState('');
  const [recommendation, setRecommendation] = useState('');
  const [submitted, setSubmitted] = useState(false);

  const handleSubmit = () => {
    onSubmit({
      evaluatorName,
      evaluatorEmail,
      ratings,
      comments,
      strengths,
      concerns,
      recommendation,
      submittedAt: new Date().toISOString(),
    });
    setSubmitted(true);
  };

  if (submitted) {
    return (
      <div style={{ ...css.app, display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
        <div style={{ ...css.card, textAlign: 'center', maxWidth: '500px' }}>
          <div style={{ fontSize: '64px', marginBottom: '16px' }}>✅</div>
          <h2 style={{ fontSize: '24px', fontWeight: '700', color: '#f1f5f9', marginBottom: '12px' }}>
            Thank You!
          </h2>
          <p style={{ color: colors.textMuted }}>
            Your scorecard for {candidate.name} has been submitted successfully.
          </p>
        </div>
      </div>
    );
  }

  return (
    <div style={css.app}>
      <div style={{ ...css.container, maxWidth: '800px' }}>
        {/* Header */}
        <div style={{ textAlign: 'center', marginBottom: '32px' }}>
          <div style={{ fontSize: '48px', marginBottom: '16px' }}>🎯</div>
          <h1 style={{ fontSize: '24px', fontWeight: '700', color: '#f1f5f9', marginBottom: '8px' }}>
            Interview Scorecard
          </h1>
          <p style={{ color: colors.textMuted }}>
            {position.title} • {candidate.name}
          </p>
        </div>

        {/* Evaluator Info */}
        <div style={{ ...css.card, marginBottom: '24px' }}>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '16px', color: '#f1f5f9' }}>Your Information</h3>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px' }}>
            <div>
              <label style={css.label}>Your Name *</label>
              <input style={css.input} placeholder="Jane Smith" value={evaluatorName} onChange={e => setEvaluatorName(e.target.value)} />
            </div>
            <div>
              <label style={css.label}>Your Email *</label>
              <input style={css.input} type="email" placeholder="jane@company.com" value={evaluatorEmail} onChange={e => setEvaluatorEmail(e.target.value)} />
            </div>
          </div>
        </div>

        {/* Ratings */}
        <div style={{ ...css.card, marginBottom: '24px' }}>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '16px', color: '#f1f5f9' }}>📊 Rate the Candidate</h3>
          <p style={{ fontSize: '13px', color: colors.textDim, marginBottom: '20px' }}>
            1 = Does Not Meet | 2 = Partially Meets | 3 = Meets | 4 = Exceeds | 5 = Far Exceeds
          </p>
          
          <div style={{ display: 'flex', flexDirection: 'column', gap: '20px' }}>
            {position.requirements.map(req => (
              <div key={req.id} style={{ padding: '16px', background: 'rgba(30, 41, 59, 0.5)', borderRadius: '12px' }}>
                <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'flex-start', marginBottom: '12px' }}>
                  <div>
                    <span style={{ color: '#f1f5f9', fontWeight: '500' }}>{req.name}</span>
                    {req.mustHave && <span style={{ ...css.badge('purple'), marginLeft: '8px' }}>Must Have</span>}
                  </div>
                  <span style={css.badge('blue')}>×{req.weight} weight</span>
                </div>
                <RatingScale value={ratings[req.id]} onChange={v => setRatings({ ...ratings, [req.id]: v })} />
                <input 
                  style={{ ...css.input, marginTop: '12px' }}
                  placeholder="Add comment (optional)..."
                  value={comments[req.id] || ''}
                  onChange={e => setComments({ ...comments, [req.id]: e.target.value })}
                />
              </div>
            ))}
          </div>
        </div>

        {/* Overall Assessment */}
        <div style={{ ...css.card, marginBottom: '24px' }}>
          <h3 style={{ fontSize: '16px', fontWeight: '600', marginBottom: '16px', color: '#f1f5f9' }}>📝 Overall Assessment</h3>
          
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Key Strengths</label>
            <textarea style={css.textarea} placeholder="What stood out positively?" value={strengths} onChange={e => setStrengths(e.target.value)} />
          </div>
          
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Concerns / Development Areas</label>
            <textarea style={css.textarea} placeholder="Any concerns or areas for growth?" value={concerns} onChange={e => setConcerns(e.target.value)} />
          </div>
          
          <div>
            <label style={css.label}>Recommendation</label>
            <div style={{ display: 'flex', gap: '12px', flexWrap: 'wrap' }}>
              {[
                { value: 'strong_yes', label: '💪 Strong Yes', color: 'green' },
                { value: 'yes', label: '👍 Yes', color: 'blue' },
                { value: 'neutral', label: '🤔 Neutral', color: 'yellow' },
                { value: 'no', label: '👎 No', color: 'red' },
                { value: 'strong_no', label: '🚫 Strong No', color: 'red' },
              ].map(opt => (
                <button
                  key={opt.value}
                  style={{
                    ...css.chip(recommendation === opt.value),
                    padding: '12px 20px',
                    borderColor: recommendation === opt.value ? 
                      (opt.color === 'green' ? colors.success : 
                       opt.color === 'blue' ? colors.primary : 
                       opt.color === 'yellow' ? colors.warning : colors.error) : colors.borderLight,
                  }}
                  onClick={() => setRecommendation(opt.value)}
                >
                  {opt.label}
                </button>
              ))}
            </div>
          </div>
        </div>

        {/* Submit */}
        <button 
          style={{ ...css.button, width: '100%', justifyContent: 'center', padding: '16px' }}
          onClick={handleSubmit}
          disabled={!evaluatorName || !evaluatorEmail || Object.keys(ratings).length === 0}
        >
          Submit Scorecard
        </button>
      </div>
    </div>
  );
};

// ============================================================================
// MAIN APP COMPONENT
// ============================================================================
export default function HiringCommandCenter() {
  // Core state
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [user, setUser] = useState(null);
  const [view, setView] = useState('dashboard');
  const [loading, setLoading] = useState(true);

  // Data
  const [requirements, setRequirements] = useState([
    { id: '1', name: '3+ years in similar role', category: 'Experience' },
    { id: '2', name: 'Early stage company experience', category: 'Experience' },
    { id: '3', name: 'Direct-to-consumer experience', category: 'Experience' },
    { id: '4', name: 'E-commerce background', category: 'Experience' },
    { id: '5', name: 'Has managed teams of 5+', category: 'Leadership' },
    { id: '6', name: 'Cross-functional leadership', category: 'Leadership' },
    { id: '7', name: 'Board presentation experience', category: 'Leadership' },
    { id: '8', name: 'Financial modeling proficiency', category: 'Technical' },
    { id: '9', name: 'FP&A experience', category: 'Technical' },
    { id: '10', name: 'Familiarity with CAC/LTV metrics', category: 'Technical' },
    { id: '11', name: 'Strong communication skills', category: 'Culture' },
    { id: '12', name: 'Adaptability and flexibility', category: 'Culture' },
  ]);
  const [positions, setPositions] = useState([]);
  const [candidates, setCandidates] = useState([]);
  
  // UI state
  const [selectedPosition, setSelectedPosition] = useState(null);
  const [selectedCandidate, setSelectedCandidate] = useState(null);
  const [activeTab, setActiveTab] = useState('details');

  // Modals
  const [showJDBuilder, setShowJDBuilder] = useState(false);
  const [showNewCandidate, setShowNewCandidate] = useState(false);
  const [showScorecard, setShowScorecard] = useState(false);
  const [showInterviewNotes, setShowInterviewNotes] = useState(false);
  const [showReferenceChecks, setShowReferenceChecks] = useState(false);
  const [showInterviewerPortal, setShowInterviewerPortal] = useState(false);
  const [showNewRequirement, setShowNewRequirement] = useState(false);

  // Forms
  const [newCandidate, setNewCandidate] = useState({ name: '', email: '', positionId: '' });
  const [newRequirement, setNewRequirement] = useState({ name: '', category: 'Experience' });
  const [scorecardRatings, setScorecardRatings] = useState({});
  const [scorecardComments, setScorecardComments] = useState({});

  // Auth
  const [authEmail, setAuthEmail] = useState('');
  const [authPassword, setAuthPassword] = useState('');
  const [authName, setAuthName] = useState('');
  const [authOrgName, setAuthOrgName] = useState('');
  const [isSignUp, setIsSignUp] = useState(false);

  useEffect(() => {
    // Check for interviewer portal mode (via URL hash)
    if (window.location.hash.startsWith('#/score/')) {
      setShowInterviewerPortal(true);
    }
    setLoading(false);
  }, []);

  // Auth handlers
  const handleAuth = () => {
    setIsAuthenticated(true);
    setUser({ email: authEmail, name: authName || 'Demo User', org: authOrgName || 'Demo Company' });
  };

  const handleSignOut = () => {
    setIsAuthenticated(false);
    setUser(null);
  };

  // Calculations
  const calcWeightedScore = (scores, reqs) => {
    if (!scores || !reqs?.length) return null;
    let sum = 0, weight = 0;
    reqs.forEach(r => {
      if (scores[r.id]) {
        sum += scores[r.id] * r.weight;
        weight += r.weight;
      }
    });
    return weight ? sum / weight : null;
  };

  const stats = {
    positions: positions.filter(p => p.status === 'active').length,
    candidates: candidates.length,
    interviewing: candidates.filter(c => c.stage === 'interview').length,
    offers: candidates.filter(c => c.stage === 'offer').length,
  };

  const categories = [...new Set(requirements.map(r => r.category))];

  // Position handlers
  const savePosition = (posData) => {
    const pos = {
      id: Date.now().toString(),
      ...posData,
      status: 'active',
      shareCode: Math.random().toString(36).substr(2, 8).toUpperCase(),
      createdAt: new Date().toISOString(),
    };
    setPositions([...positions, pos]);
    setShowJDBuilder(false);
  };

  // Candidate handlers
  const addCandidate = () => {
    const cand = {
      id: Date.now().toString(),
      ...newCandidate,
      stage: 'screening',
      scores: {},
      interviews: [],
      references: [],
      scorecards: [],
      overallScore: null,
      createdAt: new Date().toISOString(),
    };
    setCandidates([...candidates, cand]);
    setNewCandidate({ name: '', email: '', positionId: '' });
    setShowNewCandidate(false);
  };

  const saveScorecard = () => {
    const pos = positions.find(p => p.id === selectedCandidate.positionId);
    const score = calcWeightedScore(scorecardRatings, pos?.requirements);
    
    const scorecard = {
      id: Date.now().toString(),
      evaluator: user.name,
      ratings: scorecardRatings,
      comments: scorecardComments,
      score,
      submittedAt: new Date().toISOString(),
    };
    
    setCandidates(candidates.map(c => {
      if (c.id === selectedCandidate.id) {
        const scorecards = [...(c.scorecards || []), scorecard];
        const avgScore = scorecards.reduce((a, s) => a + s.score, 0) / scorecards.length;
        return { ...c, scores: scorecardRatings, overallScore: avgScore, scorecards };
      }
      return c;
    }));
    
    setShowScorecard(false);
    setScorecardRatings({});
    setScorecardComments({});
  };

  const saveInterviewNotes = (notesData) => {
    const interview = {
      id: Date.now().toString(),
      ...notesData,
      interviewer: user.name,
      createdAt: new Date().toISOString(),
    };
    
    setCandidates(candidates.map(c => 
      c.id === selectedCandidate.id 
        ? { ...c, interviews: [...(c.interviews || []), interview] }
        : c
    ));
    setShowInterviewNotes(false);
  };

  const saveReferences = (refs) => {
    setCandidates(candidates.map(c =>
      c.id === selectedCandidate.id
        ? { ...c, references: refs }
        : c
    ));
    setShowReferenceChecks(false);
  };

  // Loading state
  if (loading) {
    return (
      <div style={{ ...css.app, display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
        <div style={{ textAlign: 'center' }}>
          <div style={{ fontSize: '48px', marginBottom: '16px' }}>🎯</div>
          <p style={{ color: colors.textMuted }}>Loading...</p>
        </div>
      </div>
    );
  }

  // Interviewer Portal Mode
  if (showInterviewerPortal && positions.length > 0 && candidates.length > 0) {
    return (
      <InterviewerPortal 
        position={positions[0]}
        candidate={candidates[0]}
        onSubmit={(data) => console.log('Scorecard submitted:', data)}
      />
    );
  }

  // Auth Screen
  if (!isAuthenticated) {
    return (
      <div style={{ ...css.app, display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
        <div style={{ ...css.card, width: '100%', maxWidth: '420px', margin: '24px' }}>
          <div style={{ textAlign: 'center', marginBottom: '32px' }}>
            <div style={{ fontSize: '48px', marginBottom: '16px' }}>🎯</div>
            <h1 style={{ fontSize: '24px', fontWeight: '700', background: colors.primaryGradient, WebkitBackgroundClip: 'text', WebkitTextFillColor: 'transparent' }}>
              Hiring Command Center
            </h1>
            <p style={{ color: colors.textMuted, marginTop: '8px' }}>
              {isSignUp ? 'Create your account' : 'Sign in to continue'}
            </p>
          </div>

          <div style={{ display: 'flex', flexDirection: 'column', gap: '16px' }}>
            {isSignUp && (
              <>
                <div>
                  <label style={css.label}>Your Name</label>
                  <input style={css.input} placeholder="John Smith" value={authName} onChange={e => setAuthName(e.target.value)} />
                </div>
                <div>
                  <label style={css.label}>Organization</label>
                  <input style={css.input} placeholder="Acme Corp" value={authOrgName} onChange={e => setAuthOrgName(e.target.value)} />
                </div>
              </>
            )}
            <div>
              <label style={css.label}>Email</label>
              <input style={css.input} type="email" placeholder="you@company.com" value={authEmail} onChange={e => setAuthEmail(e.target.value)} />
            </div>
            <div>
              <label style={css.label}>Password</label>
              <input style={css.input} type="password" placeholder="••••••••" value={authPassword} onChange={e => setAuthPassword(e.target.value)} />
            </div>
            
            <button style={{ ...css.button, width: '100%', justifyContent: 'center', marginTop: '8px' }} onClick={handleAuth}>
              {isSignUp ? 'Create Account' : 'Sign In'}
            </button>
            
            <button 
              style={{ background: 'none', border: 'none', color: colors.primary, cursor: 'pointer', fontSize: '14px' }}
              onClick={() => setIsSignUp(!isSignUp)}
            >
              {isSignUp ? 'Already have an account? Sign in' : "Don't have an account? Sign up"}
            </button>
          </div>

          {DEMO_MODE && (
            <div style={{ marginTop: '24px', padding: '16px', background: 'rgba(234, 179, 8, 0.1)', borderRadius: '8px', textAlign: 'center' }}>
              <p style={{ color: colors.warning, fontSize: '13px', marginBottom: '12px' }}>🚧 Demo Mode</p>
              <button style={css.button} onClick={handleAuth}>Enter Demo →</button>
            </div>
          )}
        </div>
      </div>
    );
  }

  // ============================================================================
  // MAIN APP RENDER
  // ============================================================================
  return (
    <div style={css.app}>
      <div style={css.container}>
        {/* Header */}
        <header style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '24px', paddingBottom: '20px', borderBottom: `1px solid ${colors.border}` }}>
          <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
            <div style={{ width: '44px', height: '44px', background: colors.primaryGradient, borderRadius: '10px', display: 'flex', alignItems: 'center', justifyContent: 'center', fontSize: '22px' }}>🎯</div>
            <span style={{ fontSize: '20px', fontWeight: '700', background: colors.primaryGradient, WebkitBackgroundClip: 'text', WebkitTextFillColor: 'transparent' }}>
              Hiring Command Center
            </span>
          </div>

          <nav style={css.nav}>
            {[
              { id: 'dashboard', label: '📊 Dashboard' },
              { id: 'positions', label: '📋 Positions' },
              { id: 'candidates', label: '👥 Candidates' },
              { id: 'requirements', label: '📚 Requirements' },
              { id: 'compare', label: '⚖️ Compare' },
            ].map(v => (
              <button key={v.id} style={css.navBtn(view === v.id)} onClick={() => setView(v.id)}>
                {v.label}
              </button>
            ))}
          </nav>

          <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
            <span style={{ fontSize: '13px', color: colors.textMuted }}>{user?.name}</span>
            <div style={css.avatar(36)}>{(user?.name || 'U').charAt(0)}</div>
            <button style={{ ...css.buttonSecondary, padding: '8px 12px' }} onClick={handleSignOut}>Sign Out</button>
          </div>
        </header>

        {/* Dashboard View */}
        {view === 'dashboard' && (
          <>
            <div style={{ display: 'grid', gridTemplateColumns: 'repeat(4, 1fr)', gap: '16px', marginBottom: '24px' }}>
              <div style={css.statCard}><div style={css.statValue}>{stats.positions}</div><div style={{ fontSize: '12px', color: colors.textDim, marginTop: '4px' }}>Active Positions</div></div>
              <div style={css.statCard}><div style={css.statValue}>{stats.candidates}</div><div style={{ fontSize: '12px', color: colors.textDim, marginTop: '4px' }}>Total Candidates</div></div>
              <div style={css.statCard}><div style={css.statValue}>{stats.interviewing}</div><div style={{ fontSize: '12px', color: colors.textDim, marginTop: '4px' }}>Interviewing</div></div>
              <div style={css.statCard}><div style={css.statValue}>{stats.offers}</div><div style={{ fontSize: '12px', color: colors.textDim, marginTop: '4px' }}>Offers Made</div></div>
            </div>

            <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '24px' }}>
              <div style={css.card}>
                <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '16px' }}>
                  <h3 style={{ fontSize: '16px', fontWeight: '600', color: '#f1f5f9' }}>📋 Active Positions</h3>
                  <button style={css.buttonSmall} onClick={() => setShowJDBuilder(true)}>+ New</button>
                </div>
                {positions.length === 0 ? (
                  <EmptyState icon="📋" title="No positions yet" description="Create your first position to start hiring" />
                ) : (
                  <div style={{ display: 'flex', flexDirection: 'column', gap: '12px' }}>
                    {positions.slice(0, 5).map(p => (
                      <div key={p.id} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', padding: '12px', background: 'rgba(30, 41, 59, 0.5)', borderRadius: '8px' }}>
                        <div>
                          <div style={{ fontWeight: '500', color: '#f1f5f9', marginBottom: '2px' }}>{p.title}</div>
                          <div style={{ fontSize: '12px', color: colors.textDim }}>{candidates.filter(c => c.positionId === p.id).length} candidates</div>
                        </div>
                        <span style={css.badge('green')}>Active</span>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              <div style={css.card}>
                <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '16px' }}>
                  <h3 style={{ fontSize: '16px', fontWeight: '600', color: '#f1f5f9' }}>👥 Recent Candidates</h3>
                  <button style={css.buttonSmall} onClick={() => setShowNewCandidate(true)}>+ Add</button>
                </div>
                {candidates.length === 0 ? (
                  <EmptyState icon="👥" title="No candidates yet" description="Add candidates to your pipeline" />
                ) : (
                  <div style={{ display: 'flex', flexDirection: 'column', gap: '12px' }}>
                    {candidates.slice(0, 5).map(c => (
                      <div key={c.id} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', padding: '12px', background: 'rgba(30, 41, 59, 0.5)', borderRadius: '8px' }}>
                        <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                          <div style={css.avatar(32)}>{c.name.split(' ').map(n => n[0]).join('')}</div>
                          <div>
                            <div style={{ fontWeight: '500', color: '#f1f5f9', marginBottom: '2px' }}>{c.name}</div>
                            <div style={{ fontSize: '12px', color: colors.textDim }}>{positions.find(p => p.id === c.positionId)?.title}</div>
                          </div>
                        </div>
                        <ScoreDisplay score={c.overallScore} />
                      </div>
                    ))}
                  </div>
                )}
              </div>
            </div>
          </>
        )}

        {/* Positions View */}
        {view === 'positions' && (
          <div style={css.card}>
            <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '20px' }}>
              <h3 style={{ fontSize: '18px', fontWeight: '600', color: '#f1f5f9' }}>📋 Positions</h3>
              <button style={css.button} onClick={() => setShowJDBuilder(true)}>🤖 JD Builder</button>
            </div>
            
            {positions.length === 0 ? (
              <EmptyState 
                icon="📋" 
                title="No Positions Yet" 
                description="Use the JD Builder to create your first position with AI-powered requirement extraction."
                action={<button style={css.button} onClick={() => setShowJDBuilder(true)}>🤖 Create with JD Builder</button>}
              />
            ) : (
              <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fill, minmax(280px, 1fr))', gap: '16px' }}>
                {positions.map(p => (
                  <div key={p.id} style={{ ...css.card, margin: 0, cursor: 'pointer' }} onClick={() => { setSelectedPosition(p); setView('position-detail'); }}>
                    <div style={{ display: 'flex', justifyContent: 'space-between', marginBottom: '12px' }}>
                      <h4 style={{ fontSize: '15px', fontWeight: '600', color: '#f1f5f9' }}>{p.title}</h4>
                      <span style={css.badge('green')}>Active</span>
                    </div>
                    <p style={{ fontSize: '13px', color: colors.textDim, marginBottom: '12px' }}>{p.department}</p>
                    <div style={{ display: 'flex', gap: '16px', fontSize: '12px', color: colors.textMuted }}>
                      <span>{candidates.filter(c => c.positionId === p.id).length} candidates</span>
                      <span>{p.requirements?.length || 0} requirements</span>
                    </div>
                    <div style={{ marginTop: '12px', padding: '8px', background: 'rgba(59, 130, 246, 0.1)', borderRadius: '6px' }}>
                      <span style={{ fontSize: '11px', color: colors.textDim }}>Share Code: </span>
                      <code style={{ fontSize: '12px', color: colors.primary }}>{p.shareCode}</code>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}

        {/* Candidates View */}
        {view === 'candidates' && (
          <div style={css.card}>
            <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '20px' }}>
              <h3 style={{ fontSize: '18px', fontWeight: '600', color: '#f1f5f9' }}>👥 All Candidates</h3>
              <button style={css.button} onClick={() => setShowNewCandidate(true)}>+ Add Candidate</button>
            </div>
            
            {candidates.length === 0 ? (
              <EmptyState 
                icon="👥" 
                title="No Candidates Yet" 
                description="Add candidates to start tracking your hiring pipeline."
                action={<button style={css.button} onClick={() => setShowNewCandidate(true)}>+ Add First Candidate</button>}
              />
            ) : (
              <table style={css.table}>
                <thead>
                  <tr>
                    <th style={css.th}>Candidate</th>
                    <th style={css.th}>Position</th>
                    <th style={css.th}>Stage</th>
                    <th style={css.th}>Score</th>
                    <th style={css.th}>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  {candidates.map(c => {
                    const pos = positions.find(p => p.id === c.positionId);
                    return (
                      <tr key={c.id}>
                        <td style={css.td}>
                          <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                            <div style={css.avatar(36)}>{c.name.split(' ').map(n => n[0]).join('')}</div>
                            <div>
                              <div style={{ fontWeight: '500', color: '#f1f5f9' }}>{c.name}</div>
                              <div style={{ fontSize: '12px', color: colors.textDim }}>{c.email}</div>
                            </div>
                          </div>
                        </td>
                        <td style={css.td}>{pos?.title || '—'}</td>
                        <td style={css.td}>
                          <select
                            style={{ ...css.input, width: 'auto', padding: '6px 10px', fontSize: '12px' }}
                            value={c.stage}
                            onChange={e => setCandidates(candidates.map(x => x.id === c.id ? { ...x, stage: e.target.value } : x))}
                            onClick={e => e.stopPropagation()}
                          >
                            <option value="screening">Screening</option>
                            <option value="phone">Phone</option>
                            <option value="interview">Interview</option>
                            <option value="final">Final</option>
                            <option value="offer">Offer</option>
                            <option value="hired">Hired</option>
                            <option value="rejected">Rejected</option>
                          </select>
                        </td>
                        <td style={css.td}><ScoreDisplay score={c.overallScore} /></td>
                        <td style={css.td}>
                          <div style={{ display: 'flex', gap: '6px' }}>
                            <button style={{ ...css.buttonSmall, fontSize: '11px' }} onClick={() => { setSelectedCandidate(c); setScorecardRatings(c.scores || {}); setShowScorecard(true); }}>📊 Score</button>
                            <button style={{ ...css.buttonSecondary, padding: '6px 10px', fontSize: '11px' }} onClick={() => { setSelectedCandidate(c); setShowInterviewNotes(true); }}>📝 Notes</button>
                            <button style={{ ...css.buttonSecondary, padding: '6px 10px', fontSize: '11px' }} onClick={() => { setSelectedCandidate(c); setShowReferenceChecks(true); }}>📋 Refs</button>
                          </div>
                        </td>
                      </tr>
                    );
                  })}
                </tbody>
              </table>
            )}
          </div>
        )}

        {/* Requirements View */}
        {view === 'requirements' && (
          <div style={css.card}>
            <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '20px' }}>
              <h3 style={{ fontSize: '18px', fontWeight: '600', color: '#f1f5f9' }}>📚 Requirements Library</h3>
            </div>
            
            <div style={{ display: 'flex', gap: '12px', marginBottom: '24px' }}>
              <input 
                style={{ ...css.input, flex: 2 }} 
                placeholder="New requirement..." 
                value={newRequirement.name}
                onChange={e => setNewRequirement({ ...newRequirement, name: e.target.value })}
              />
              <select 
                style={{ ...css.input, flex: 1 }} 
                value={newRequirement.category}
                onChange={e => setNewRequirement({ ...newRequirement, category: e.target.value })}
              >
                {categories.map(c => <option key={c} value={c}>{c}</option>)}
              </select>
              <button style={css.button} onClick={() => {
                if (newRequirement.name) {
                  setRequirements([...requirements, { id: Date.now().toString(), ...newRequirement }]);
                  setNewRequirement({ name: '', category: 'Experience' });
                }
              }}>+ Add</button>
            </div>

            {categories.map(cat => (
              <div key={cat} style={{ marginBottom: '24px' }}>
                <h4 style={{ fontSize: '13px', fontWeight: '600', color: colors.textMuted, marginBottom: '12px', textTransform: 'uppercase', letterSpacing: '0.5px' }}>
                  📁 {cat}
                </h4>
                <div style={{ display: 'flex', flexWrap: 'wrap' }}>
                  {requirements.filter(r => r.category === cat).map(r => (
                    <div key={r.id} style={{ ...css.chip(false), paddingRight: '8px' }}>
                      {r.name}
                      <button 
                        style={{ background: 'none', border: 'none', color: colors.textDim, cursor: 'pointer', marginLeft: '4px' }}
                        onClick={() => setRequirements(requirements.filter(x => x.id !== r.id))}
                      >
                        ×
                      </button>
                    </div>
                  ))}
                </div>
              </div>
            ))}
          </div>
        )}

        {/* Compare View */}
        {view === 'compare' && (
          <div style={css.card}>
            <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '20px' }}>
              <h3 style={{ fontSize: '18px', fontWeight: '600', color: '#f1f5f9' }}>⚖️ Compare Candidates</h3>
              <select 
                style={{ ...css.input, width: 'auto' }} 
                value={selectedPosition?.id || ''}
                onChange={e => setSelectedPosition(positions.find(p => p.id === e.target.value))}
              >
                <option value="">Select Position</option>
                {positions.map(p => <option key={p.id} value={p.id}>{p.title}</option>)}
              </select>
            </div>
            
            {!selectedPosition ? (
              <EmptyState icon="⚖️" title="Select a Position" description="Choose a position to compare candidates side-by-side" />
            ) : (() => {
              const posCandidates = candidates.filter(c => c.positionId === selectedPosition.id);
              return posCandidates.length === 0 ? (
                <EmptyState icon="👥" title="No Candidates" description="Add candidates to this position to compare them" />
              ) : (
                <div style={{ overflowX: 'auto' }}>
                  <table style={css.table}>
                    <thead>
                      <tr>
                        <th style={css.th}>Requirement</th>
                        {posCandidates.map(c => (
                          <th key={c.id} style={{ ...css.th, textAlign: 'center', minWidth: '120px' }}>{c.name}</th>
                        ))}
                      </tr>
                    </thead>
                    <tbody>
                      {selectedPosition.requirements?.map(req => (
                        <tr key={req.id}>
                          <td style={css.td}>
                            <div style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
                              {req.name}
                              {req.mustHave && <span style={css.badge('purple')}>Must</span>}
                              <span style={{ fontSize: '10px', color: colors.textDim }}>×{req.weight}</span>
                            </div>
                          </td>
                          {posCandidates.map(c => (
                            <td key={c.id} style={{ ...css.td, textAlign: 'center' }}>
                              <ScoreDisplay score={c.scores?.[req.id]} />
                            </td>
                          ))}
                        </tr>
                      ))}
                      <tr style={{ background: 'rgba(59, 130, 246, 0.1)' }}>
                        <td style={{ ...css.td, fontWeight: '600' }}>Overall Score</td>
                        {posCandidates.map(c => (
                          <td key={c.id} style={{ ...css.td, textAlign: 'center' }}>
                            <ScoreDisplay score={c.overallScore} />
                          </td>
                        ))}
                      </tr>
                    </tbody>
                  </table>
                </div>
              );
            })()}
          </div>
        )}

        {/* ============= MODALS ============= */}

        {/* JD Builder Modal */}
        <Modal isOpen={showJDBuilder} onClose={() => setShowJDBuilder(false)} title="🤖 JD Builder" extraWide>
          <JDBuilder requirements={requirements} onSave={savePosition} onClose={() => setShowJDBuilder(false)} />
        </Modal>

        {/* New Candidate Modal */}
        <Modal isOpen={showNewCandidate} onClose={() => setShowNewCandidate(false)} title="Add Candidate">
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Name *</label>
            <input style={css.input} placeholder="Sarah Johnson" value={newCandidate.name} onChange={e => setNewCandidate({ ...newCandidate, name: e.target.value })} />
          </div>
          <div style={{ marginBottom: '16px' }}>
            <label style={css.label}>Email</label>
            <input style={css.input} type="email" placeholder="sarah@example.com" value={newCandidate.email} onChange={e => setNewCandidate({ ...newCandidate, email: e.target.value })} />
          </div>
          <div style={{ marginBottom: '24px' }}>
            <label style={css.label}>Position *</label>
            <select style={css.input} value={newCandidate.positionId} onChange={e => setNewCandidate({ ...newCandidate, positionId: e.target.value })}>
              <option value="">Select position...</option>
              {positions.map(p => <option key={p.id} value={p.id}>{p.title}</option>)}
            </select>
          </div>
          <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
            <button style={css.buttonSecondary} onClick={() => setShowNewCandidate(false)}>Cancel</button>
            <button style={css.button} onClick={addCandidate} disabled={!newCandidate.name || !newCandidate.positionId}>Add Candidate</button>
          </div>
        </Modal>

        {/* Scorecard Modal */}
        <Modal isOpen={showScorecard} onClose={() => setShowScorecard(false)} title={`Scorecard: ${selectedCandidate?.name}`} wide>
          {selectedCandidate && (() => {
            const pos = positions.find(p => p.id === selectedCandidate.positionId);
            if (!pos || !pos.requirements?.length) return <p style={{ color: colors.textDim }}>No requirements defined for this position.</p>;
            
            return (
              <>
                <div style={{ background: 'rgba(59, 130, 246, 0.1)', padding: '16px', borderRadius: '12px', marginBottom: '24px', display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
                  <div>
                    <h4 style={{ fontWeight: '600', color: '#f1f5f9', marginBottom: '4px' }}>{pos.title}</h4>
                    <p style={{ fontSize: '12px', color: colors.textDim }}>{pos.requirements.length} requirements</p>
                  </div>
                  <div style={{ textAlign: 'right' }}>
                    <div style={{ fontSize: '11px', color: colors.textDim }}>Weighted Score</div>
                    <div style={{ fontSize: '28px', fontWeight: '700', color: colors.primary }}>
                      {calcWeightedScore(scorecardRatings, pos.requirements)?.toFixed(1) || '—'}
                    </div>
                  </div>
                </div>

                <div style={{ display: 'flex', flexDirection: 'column', gap: '16px', marginBottom: '24px' }}>
                  {pos.requirements.map(req => (
                    <div key={req.id} style={{ padding: '16px', background: 'rgba(30, 41, 59, 0.5)', borderRadius: '10px' }}>
                      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '12px' }}>
                        <div style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
                          <span style={{ color: '#f1f5f9', fontWeight: '500' }}>{req.name}</span>
                          {req.mustHave && <span style={css.badge('purple')}>Must Have</span>}
                        </div>
                        <span style={css.badge('blue')}>×{req.weight}</span>
                      </div>
                      <RatingScale value={scorecardRatings[req.id]} onChange={v => setScorecardRatings({ ...scorecardRatings, [req.id]: v })} />
                      <input 
                        style={{ ...css.input, marginTop: '12px' }}
                        placeholder="Add comment..."
                        value={scorecardComments[req.id] || ''}
                        onChange={e => setScorecardComments({ ...scorecardComments, [req.id]: e.target.value })}
                      />
                    </div>
                  ))}
                </div>

                <div style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
                  <button style={css.buttonSecondary} onClick={() => setShowScorecard(false)}>Cancel</button>
                  <button style={css.button} onClick={saveScorecard}>Save Scorecard</button>
                </div>
              </>
            );
          })()}
        </Modal>

        {/* Interview Notes Modal */}
        <Modal isOpen={showInterviewNotes} onClose={() => setShowInterviewNotes(false)} title="Interview Notes" extraWide>
          {selectedCandidate && (
            <InterviewNotes
              candidate={selectedCandidate}
              interview={null}
              onSave={saveInterviewNotes}
              onClose={() => setShowInterviewNotes(false)}
            />
          )}
        </Modal>

        {/* Reference Checks Modal */}
        <Modal isOpen={showReferenceChecks} onClose={() => setShowReferenceChecks(false)} title="Reference Checks" extraWide>
          {selectedCandidate && (
            <ReferenceCheckManager
              candidate={selectedCandidate}
              references={selectedCandidate.references || []}
              onSave={saveReferences}
              onClose={() => setShowReferenceChecks(false)}
            />
          )}
        </Modal>

        {/* Demo Mode Banner */}
        {DEMO_MODE && (
          <div style={{ position: 'fixed', bottom: '16px', right: '16px', background: 'rgba(234, 179, 8, 0.9)', color: '#000', padding: '10px 16px', borderRadius: '8px', fontSize: '12px', fontWeight: '500' }}>
            🚧 Demo Mode
          </div>
        )}
      </div>
    </div>
  );
}
