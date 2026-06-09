# Threat Model Workshop Template

Use this during architecture reviews. For each system component, walk through:

```
┌─────────────────────────────────────────────────────────────┐
│ Component: _______________________________________________  │
│ WAR Workload: ____________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ S - Who/what can impersonate this component?                │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 2, REL 3, OPS 5): _____________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ T - Who/what can modify this component's data or code?      │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 6, OPS 5/6, REL 8): ___________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ R - Can actions on this component be denied?                │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 4, OPS 4/8): ___________________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ I - What sensitive data can this component expose?           │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 7/8/9, REL 10, COST 4): _______________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ D - How can this component be made unavailable?             │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 5, REL 1-13, PERF 4): _________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ E - How can privileges be escalated through this component? │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 3, REL 3, COST 2): ____________________  │
│     AWS services: ________________________________________  │
└─────────────────────────────────────────────────────────────┘
```
