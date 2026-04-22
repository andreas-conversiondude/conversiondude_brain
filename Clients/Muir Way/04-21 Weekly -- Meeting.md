---
date: 2026-04-21
attendees: [Andreas, Jared]
duration: 20 min
---

## Tests

| Status | Test | Notes |
|--------|------|-------|
| ✅ Running | Homepage A/B test | Simplification showing improved conversions + increased orders — keep running |
| ✅ Running | Badges test | Inconclusive so far — keep running for more data |
| 🚀 Just started | Collection page test | Optimize product presentation — monitor |
| 📋 Planned | Homepage hero video A/B test | Launch once current homepage test concludes; waiting on video asset from Jared |

## Tasks

**Andreas**
- [ ] Monitor Cloudflare bot fighting mode performance and report anomalies to Jared
- [ ] Continue running homepage, badges, and collection page tests until sufficient data
- [ ] Prepare homepage hero video A/B test (design + setup) once current homepage test concludes
- [ ] Send reminder to Jared if video asset for hero test is not received in time

**Jared**
- [ ] Provide video asset for homepage hero video A/B test
- [ ] Consult ads manager post-call to assess Cloudflare bot filtering impact on paid ads + email tracking

## Decisions

- **Cloudflare bot fighting mode → activated.** Significantly reduces harmful bot traffic, improving analytics accuracy and sales tracking. Legitimate users may face occasional challenges but cleaner data outweighs that.
- **All current tests → keep running.** Longer test durations needed for conclusive results. Do not call winners early.
- **Homepage hero video A/B test → next up** once current homepage test wraps. Template-based approach (not URL split).
- **Bot traffic reduction impact on marketing performance** to be assessed by Jared's ads manager.
