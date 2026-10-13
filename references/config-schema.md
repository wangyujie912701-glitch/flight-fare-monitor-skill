# Route configuration schema

`routes.json` is deliberately plain JSON so another AI or human can edit it without a custom program.

## Route identity

- `id`: unique, stable, lowercase identifier. Change it when the route structure or travel dates change enough to require a new price-history baseline.
- `active`: only `true` entries are monitored.
- `journey_type`: `round_trip` or `open_jaw`.

## Round trip

Use:

```json
{
  "journey_type": "round_trip",
  "outbound": { "date": "YYYY-MM-DD", "from": ["AAA"], "to": ["BBB", "BBC"] },
  "inbound": { "date": "YYYY-MM-DD", "from": ["BBB", "BBC"], "to": ["AAA"] }
}
```

Airport arrays mean “any listed airport is acceptable.” Use city codes such as `MOW` or `LON` only when all airports in that metropolitan area are acceptable.

## Open jaw

Use explicit air segments:

```json
{
  "journey_type": "open_jaw",
  "air_segments": [
    { "date": "YYYY-MM-DD", "from": ["AAA"], "to": ["HRG"] },
    { "date": "YYYY-MM-DD", "from": ["CAI", "SPX"], "to": ["AAA"] }
  ],
  "ground_transfer": { "from": "HRG", "to": "Cairo", "include_in_airfare": false }
}
```

Keep the ground transfer separate from airfare unless the user explicitly changes that accounting rule.

## Constraints

- `max_stops_per_direction`: integer, normally `0` or `1`.
- `connection_preference`: `any_region`, `prefer_non_middle_east`, or another clear natural-language token documented in `change_notes`.
- `compare_split_tickets`: optional boolean for round trips. Open-jaw routes are always compared as multi-city versus separate one-ways.

## Alerts

`alert.currency` is required when alert limits exist. Supported limit keys are descriptive rather than computationally fixed, for example:

- `maximum_total`
- `maximum_total_direct`
- `excellent_total_direct`
- `maximum_total_one_stop`
- `maximum_total_direct_or_mixed`
- `preferred_max_duration_hours_per_direction`

An AI must explain which limit it applied. Alert limits are decision aids, not proof that today is the historical minimum.

## Editing checklist

After modifying `routes.json`:

1. Parse it as JSON.
2. Confirm route IDs are unique.
3. Confirm every airport token is three uppercase letters.
4. Confirm all dates are ISO `YYYY-MM-DD` and inbound/second-segment date is not earlier than outbound/first-segment date.
5. Confirm every active route has an alert rule or explicitly documents why it does not.
6. Update `configuration.updated_at` and append `configuration.change_notes`.
7. Do not edit historical report files to make old routes look comparable with new ones.


