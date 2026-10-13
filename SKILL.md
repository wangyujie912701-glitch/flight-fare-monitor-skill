---
name: flight-fare-monitor
description: Configure, run, and maintain repeatable airfare monitoring for editable origins, destinations, open-jaw or round-trip dates, baggage rules, budgets, payment methods, cashback, and promotion checks. Use when an AI must change monitored routes or dates, perform a daily/manual fare check, compare like-for-like history, or produce an auditable Markdown airfare report. Do not use for purchasing tickets or predicting exact future prices.
---

# Flight Fare Monitor

Use this skill to monitor airfare from a user-editable route configuration without embedding routes or dates in the workflow.

## Required inputs

1. Read [routes.json](routes.json). Treat it as the only active route list.
2. Read [references/config-schema.md](references/config-schema.md) before changing the configuration.
3. If the user changes an origin, destination, airport set, or date, update `routes.json` first. Preserve unrelated routes and rules.
4. Validate every active route before searching. Reject or flag missing dates, invalid IATA codes, or a return date earlier than the outbound date.

## Monitoring workflow

For each object in `routes` where `active` is `true`:

1. Search the exact configured dates and airport sets for one adult in the configured cabin.
2. Enforce `max_stops_per_direction` and the route's connection preferences.
3. The primary price must include tax and at least `baggage.minimum_checked_kg`; when an airline's standard allowance is higher, use that standard. Never use a bare fare or a 10/12/15kg fare as the primary price.
4. Search sources in `source_policy.required` and record the actual source used. If the first source fails, retry according to `source_policy.retry`, then continue through fallbacks. Do not replace evidence with a platform list.
5. Label prices as live/bookable, public display, login/member, app-only, coupon-after, or index/reference. Only a live or clearly bookable exact-date price may enter the comparable price history.
6. Capture airline, flight number, all airports, local departure/arrival times, total duration, connection airports and waits, baggage, ticket seller, self-transfer/recheck/bag collection requirements, and payment support. Write `待确认` for facts not shown; never infer them.
7. For open-jaw routes, price both:
   - one multi-city/open-jaw order; and
   - separate one-way tickets for each air segment.
   Compare them only with the same baggage, stop, payment, and tax basis. Show split-ticket risk.
8. Keep non-flight transfers in `ground_transfer` separate from airfare. Report their verified time/cost and source, or `待确认`.
9. Apply cashback only when the user is eligible, the booking channel and card match, the actual rate is shown, and stacking is permitted. Platform points are not bank cash cashback. Otherwise show the undiscounted amount.
10. Compare only with historical records having the same route structure, exact travel dates, baggage, stop pattern, and payment basis. A route/date change starts a new baseline.

## Report requirements

Start with query date/time and timezone. Then provide:

- a purchase-timing table for every active route;
- an exact-date fare table sorted by final eligible out-of-pocket price;
- open-jaw or split-ticket comparisons where configured;
- payment-method status for Russian-issued and Chinese-issued cards;
- promotion and cashback checks;
- source failures with last-attempt time;
- a concise recommendation based on evidence, not only the alert threshold.

If prices or promotions did not materially change, still publish the full route list and state `无明显变化`. If evidence is insufficient, state `趋势证据不足，无法预测最佳购票日`. Never invent an exact future fare or cheapest purchase date.

## Configuration changes

When the user asks to modify routes:

1. Identify the route by `id`; do not rewrite unrelated entries.
2. For a round trip, use `journey_type: round_trip` with `outbound` and `inbound` dates.
3. For different arrival and departure cities, use `journey_type: open_jaw` and define two `air_segments` explicitly.
4. Add a new unique `id` when adding another origin such as CAN alongside SZX. Never merge histories for different origins.
5. Update `configuration.updated_at` and add a short entry to `configuration.change_notes`.
6. Restate the resulting active routes to the user before or with the next monitoring run.

## Safety and boundaries

- Searching and reporting do not authorize purchasing, reserving, logging into accounts, applying coupons, or sending messages to third parties.
- Do not request a VPN. Record regional, login, or anti-automation restrictions transparently.
- Do not expose session tokens or persist temporary booking URLs as stable sources. Prefer long-lived public entry URLs.


