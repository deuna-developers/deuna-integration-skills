# DEUNA Integration Skill

An AI skill for any AI coding agent that provides context-aware guidance for integrating [DEUNA](https://deuna.com) payment and checkout solutions.

When installed, your AI assistant automatically knows how to:
- Create and tokenize orders
- Process payments (purchase, capture, void, refund)
- Set up the Web SDK (Payment Widget, Checkout Widget, Card Vault)
- Configure webhooks and verify signatures
- Handle errors with DEUNA-specific error codes
- Follow security and PCI best practices

## Installation

```bash
npx skills add https://github.com/deuna-developers/deuna-integration-skills
```

That's it. The skill is now available in your project. Open your AI coding agent and ask anything about DEUNA.

## Update

```bash
npx skills update
```

## Verify installation

After installing, test it by asking your AI coding agent:

- *"How do I create a DEUNA order?"*
- *"Set up the DEUNA payment widget"*
- *"How do I verify a DEUNA webhook signature?"*

If you get DEUNA-specific answers with correct endpoints, order types (`DEUNA-CHECKOUT`, `DEUNA-NOW`), and code examples — the skill is working.

## Example prompt

Try this prompt in your AI coding agent to generate a full integration:

> Create an integration with DEUNA for my e-commerce. I want to:
> 1. Create an order on the backend and tokenize it
> 2. Display the Payment Widget on the frontend using the Web SDK
> 3. Listen for webhooks when the order is completed, cancelled, or refunded
> 4. Verify the webhook signature before processing events
>

Your AI agent will use the skill's rules to generate code with the correct endpoints, order structure, SDK initialization, callback handling, and HMAC signature verification.

## What's included

| Rule file | Content |
|---|---|
| `rules/getting-started.md` | Authentication, environments, rate limits, setup |
| `rules/orders.md` | Order creation, tokenization, lifecycle |
| `rules/payments.md` | Purchase, capture, void, refund flows |
| `rules/sdk-web.md` | Web SDK, Payment Widget, Checkout Widget |
| `rules/sdk-mobile.md` | iOS, Android, React Native SDKs |
| `rules/webhooks.md` | Events, statuses, HMAC signature verification |
| `rules/error-handling.md` | Error code tables (API, payments, orders) |
| `rules/security.md` | PCI compliance, key management, best practices |
| `rules/testing.md` | Sandbox environment, testing strategies |
| `rules/api-reference.md` | REST API endpoints quick reference |

## How it works

The skill uses a **hybrid approach**:

1. **Static rules** — Curated markdown files with stable API contracts, code examples, and best practices
2. **Live docs** — The skill instructs the AI to fetch the [DEUNA docs](https://docs.deuna.com/reference/deuna-api) when it needs the latest information

This means the AI always has a solid baseline, and can verify against live documentation when needed.

## Contributing

Found an error or want to improve the skill?

1. Fork this repo
2. Edit the relevant file in `rules/`
3. Test locally: `npx skills add /path/to/your/fork --force`
4. Open a PR

## License

MIT
