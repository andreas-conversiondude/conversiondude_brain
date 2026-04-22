---
date: 2026-04-21
attendees: [Andreas, Claudio, Ryan, Lauren]
duration: 47 min
---

## Tests

| Status | Test | Notes |
|--------|------|-------|
| ❌ Off | Performance case PDP A/B test | Ryan redirected all FB + Google Shopping ads directly to performance case PDP; test lift was ad-traffic-specific |
| 🚀 Launching | Minimalist PDP — phone model selector UX test | Gray out inapplicable options with "not applicable to your phone model" label; restrict to minimalist PDP traffic only |
| 📋 Planned | Homepage hero image A/B test | Lauren to duplicate homepage in backup theme; Andreas to wire into Intelligence |

## Tasks

**Andreas**
- [ ] Turn on minimalist case A/B test (restricted to minimalist PDP landing traffic only)
- [ ] Finalize design for grayed-out non-applicable options with "not applicable to your phone model" label
- [ ] Check and verify control of "You may also like" sidecart section — update to search & discovery if manually set
- [ ] Notify team (Slack) when minimalist A/B test is ready for review
- [ ] Continue quantity-based inventory infrastructure for Grace Coffee subscription (variant → quantity)
- [ ] Update video block to support YouTube embed links (+ fix aspect ratio if needed)
- [ ] Await Claudio's design file (one-time purchase + perk structure) before building perks display

**Ryan**
- [x] Switch all Facebook + Google Shopping ads to performance case PDP
- [x] Turn off performance case PDP A/B test
- [ ] Add issue to marketing list: sponsorship counter display (accounting for one-time purchases)

**Claudio**
- [ ] Provide decision after Grace Coffee meeting: bundle perks display, 3-month subscription rules, which perks to show
- [ ] Include one-time purchase design + perk structure in design files for Andreas

**Lauren**
- [ ] Pick and install checkout character restriction app (English characters only for shipping addresses)
- [ ] Duplicate homepage template in backup theme for hero image A/B test → hand off to Andreas

## Decisions

- **Performance case PDP test → off.** Ad traffic (FB + Google Shopping) now routes directly to performance case PDP; test lift was ad-traffic-specific, not organic.
- **Minimalist PDP UX test → approved.** Gray out inapplicable options with "not applicable to your phone model" label. Scoped to minimalist PDP traffic only as first test; contemporary pages to follow if results are good.
- **Phone model selector UX:** Colors stay visible on load; irrelevant variant options hidden/grayed until phone model is chosen.
- **Grace Coffee child ID metafield** → working and confirmed; test order forwarded to Tessa for NetSuite sync.
- **Grace Coffee subscriptions:** Switching from variant-based to quantity-based inventory (1/3/5 bags). Will be bundled with the annual attribution bug fix.
- **One-time purchases** will be enabled for Grace Coffee. Child sponsorship handled internally by Grace Coffee (not via app logic) — they use one-time purchase revenue to sponsor additional children.
- **Sponsorship counter:** Decision deferred — how to display count when one-time purchases are included. Added to issues list.
- **Video blocks** will support YouTube embed links (not just MP4 uploads).
- **Weekly call** moved off Wednesday, now back-to-back with Grace Coffee meeting.
