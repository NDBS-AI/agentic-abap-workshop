# Booster - Reviewer Evidence Checklist

The reviewer must remain independent and read-only.

Check:
- Correctness and RAP transaction semantics
- Clean ABAP and project naming conventions
- No SAP standard dependency that is incompatible with the connected S/4HANA target or its configured ABAP language version
- Authorization/security concerns relevant to writes/actions
- No hardcoded secrets
- SQL/data access risks (potential risk only unless measured)
- ABAP Unit result evidence
- ATC result evidence
- No writes outside participant package

Do not call a potential performance risk a measured bottleneck without runtime evidence.
