# Vibe Coding techniques

1. Do you need a full AI development team?
├─ YES → Use BMAD
│ (Complex systems, enterprise scale)
└─ NO → Continue...
2. Do you need persistent documentation?
├─ NO → Use AskUserQuestion
│ (Quick tasks, tactical questions)
└─ YES → Continue...
3. Is this a brand new project (0→1)?
├─ YES → Choose between:
│ • Spec-Kit (comprehensive, structured)
│ • BMAD (if need multi-agent team)
└─ NO → Continue...
4. Are you modifying existing code (1→n)?
├─ YES → Use OpenSpec
│ (Brownfield, change tracking)
└─ MAYBE → Evaluate complexity:
• Simple feature → OpenSpec
• Complex system → BMAD
• Need governance → Spec-Kit