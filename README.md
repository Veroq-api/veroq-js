# @veroq/sdk

Official TypeScript SDK for [VEROQ](https://veroq.ai) — verified intelligence for AI agents.

> **Migrating from `polaris-news-api`?** This is the same SDK, rebranded. Drop-in replacement — just change your import.

## Installation

```bash
npm install @veroq/sdk
```

## Quick Start

```typescript
import { VeroqClient } from '@veroq/sdk';

const client = new VeroqClient(); // uses VEROQ_API_KEY env var

// Ask anything
const answer = await client.ask("How is NVDA doing?");
console.log(answer.summary);
console.log(answer.trade_signal);

// Verify anything
const result = await client.verify("NVIDIA beat Q4 earnings");
console.log(result.verdict, result.confidence);
console.log(result.evidence_chain);

// Stream in real-time
for await (const event of client.askStream("AAPL technicals")) {
  if (event.type === "summary_token") process.stdout.write(event.data.token);
}
```

### Authenticate via CLI

```bash
veroq login    # opens GitHub in your browser — API key saved automatically
veroq whoami   # check your auth status
veroq logout   # remove saved credentials
```

You can also pass a key explicitly or set the `VEROQ_API_KEY` (or `POLARIS_API_KEY`) environment variable.

### Universal Agent

```typescript
import { Agent } from '@veroq/sdk';

const agent = new Agent({ apiKey: 'pr_live_...' });
const result = await agent.ask("What's happening with NVDA?");
console.log(result.summary);

const full = await agent.full('AAPL');
console.log(full.price, full.technicals, full.earnings);
```

## Methods

| Method | Description |
|--------|-------------|
| `ask(question, options?)` | Ask any financial question (routes to 40+ endpoints) |
| `askStream(question)` | Stream financial intelligence via SSE (async generator) |
| `verify(claim, options?)` | Fact-check a claim against briefs |
| `feed(options?)` | Get the news feed |
| `brief(id, options?)` | Get a single brief by ID |
| `search(query, options?)` | Search briefs |
| `generate(topic, category?)` | Generate a brief on a topic |
| `entities(options?)` | List entities |
| `entityBriefs(name, options?)` | Get briefs for an entity |
| `trendingEntities(limit?)` | Get trending entities |
| `similar(id, options?)` | Get similar briefs |
| `clusters(options?)` | Get brief clusters |
| `data(options?)` | Get structured data points |
| `agentFeed(options?)` | Get agent-optimized feed |
| `compareSources(briefId)` | Compare sources for a brief |
| `trending(options?)` | Get trending briefs |
| `verify(claim, options?)` | Fact-check a claim against briefs |
| `stream(options?)` | Stream briefs via SSE |

## Error Handling

```typescript
import { VeroqClient, AuthenticationError, RateLimitError, NotFoundError } from "@veroq/sdk";

const client = new VeroqClient();

try {
  const brief = await client.brief("abc123");
} catch (e) {
  if (e instanceof AuthenticationError) {
    console.log("Invalid API key");
  } else if (e instanceof NotFoundError) {
    console.log("Brief not found");
  } else if (e instanceof RateLimitError) {
    console.log(`Rate limited. Retry after: ${e.retryAfter}s`);
  }
}
```

## Streaming

```typescript
const client = new VeroqClient();

const stream = client.stream({ categories: "technology,science" });
stream.start(
  (brief) => console.log(`[${brief.category}] ${brief.headline}`),
  (error) => console.error("Stream error:", error)
);

// Later: stream.stop();
```

## Backward Compatibility

Both `VeroqClient` and `PolarisClient` are exported. The SDK reads credentials from both `VEROQ_API_KEY` and `POLARIS_API_KEY`, and checks both `~/.veroq/` and `~/.polaris/` for saved credentials.

## Documentation

Full API documentation: https://veroq.ai/docs
