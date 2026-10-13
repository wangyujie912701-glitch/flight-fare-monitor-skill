# Flight Fare Monitor Skill

A reusable AI skill for exact-date airfare monitoring with editable origins, destinations, dates, airport sets, baggage rules, stop limits, budgets, payment methods, cashback checks, and comparable price history.

## What it supports

- Round trips and open-jaw itineraries
- Multiple acceptable airports per city
- Checked-baggage minimums
- Direct/one-stop and regional connection preferences
- Multi-city versus split-ticket comparison
- Payment-method and verified cashback checks
- Auditable Markdown reports and like-for-like price history

## Use

1. Copy this folder into your AI agent's skills directory.
2. Edit `routes.json` with your IATA codes, dates, limits, and sources.
3. Ask the agent to use `$flight-fare-monitor` to validate the configuration or run a fare check.

The skill never purchases tickets and never treats an unverified coupon, cashback claim, index price, or baggage-incomplete fare as the final price.

See [`references/config-schema.md`](references/config-schema.md) for configuration details.

## Privacy

The included configuration contains fictional airport codes and dates. Do not commit private booking URLs, session tokens, account data, or personal travel details to a public repository.

## License

MIT


