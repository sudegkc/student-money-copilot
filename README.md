# Student Money Copilot

An AI-powered financial copilot for students, built with
local AI, Bitcoin Lightning, and RGB assets.

## The Problem

Digital asset wallets and payment tools can be difficult
for non-technical users to understand and operate.

Student Money Copilot aims to make digital asset management
more accessible through natural-language interactions,
with a focus on students.

## Our Solution

We aim to build a student-focused financial assistant
that combines:

- Local AI for understanding natural-language requests
- RGB for digital asset operations on Bitcoin
- Lightning for payment functionality
- A simple, accessible user experience

The student-focused product is currently in development.

## Team

| Member | GitHub |
|---|---|
| Sudenur Gökçe | @sudegkc |
| Elif Sude Şahin | @ElifSudeSahin |
| Zeynep Akçınar | @zeynakcn |

## Starting Point and Attribution

We followed the RGB Hackathon technical guide shared
by the organizers:

https://kaleidoswap.github.io/rgb-hackathon/

Our pre-hackathon development includes setting up
the agent environment and integrating Kaleido Mind
with QVAC local AI and RGB Lightning Node tools.

## Technology Stack

- TypeScript / Node.js
- QVAC SDK — Qwen3.5 2B local AI
- Kaleido Mind — agent framework
- RGB Lightning Node — RGB and Lightning operations
- Git / GitHub — version control and collaboration

## Pre-Hackathon Snapshot — October 16, 2026

The following work existed before the official start
of the hackathon:

- TypeScript agent scaffold and configuration
- Kaleido Mind integration
- QVAC SDK and local model configuration
- Mock RGB Lightning Node environment
- Wallet balance and asset listing tools
- RGB asset issuance tool
- RGB and Lightning invoice creation tools
- RGB asset sending tool
- Evaluation script for six wallet scenarios

### Verified Results

`npm run eval:mock` completed successfully:

**6/6 mock evaluation scenarios passed.**

These results demonstrate the mock agent workflow,
not verified real-network RGB transactions.

### Current Limitations

- Wallet evaluations use a simulated RGB node.
- Real signet transactions have not been verified
  by the reported mock tests.
- A student-focused graphical interface is not
  yet implemented in this snapshot.
- Student-specific product features remain planned.

## Hackathon Development Goals

During October 17–18, we aim to:

1. Develop the Student Money Copilot user experience.
2. Build student-oriented financial interaction flows.
3. Connect AI-driven requests to meaningful RGB operations.
4. Test and demonstrate an end-to-end working product.

These are development goals, not completed features.

## Technical Setup and Usage

A local LLM that operates an RGB Lightning Node (RLN) through
[@kaleidorg/mind](https://www.npmjs.com/package/@kaleidorg/mind): it reads
balances, issues and lists RGB assets, creates RGB and Lightning invoices and
sends assets. Live spending tools require your confirmation. Offline mode auto-approves simulated operations.

| Command | Tools | Model |
|---|---|---|
| `npm run start:offline` | fake RLN node | scripted, no download |
| `npm run start:mock` | fake RLN node (`MockWallet`) | Qwen3.5 2B on-device via `@qvac/sdk` |
| `npm start` | [kaleido-mcp](https://www.npmjs.com/package/kaleido-mcp) on signet | Qwen3.5 2B on-device |
| `npm run eval:mock` / `npm run eval` | fake / real node | checks the wallet scenarios in `src/eval.ts`, exits 1 on failure |

## First run

```bash
npm install
npm run start:offline
```

You should see sample balances, a mock invoice and a simulated transfer. This
checks the application wiring without a node or model download. Next run
`npm run start:mock` for local inference, or connect a signet node below.

Requires Node.js 20+. QVAC is needed only for the on-device provider and requires
native support for your platform. See the [package requirements](https://github.com/kaleidoswap/kaleido-mind/blob/main/packages/core/README.md#install).

## Pick a model

On-device (default): `@qvac/sdk` downloads the model on the first run. Pick
another with `MODEL`, e.g. `MODEL=QWEN3_5_4B_MULTIMODAL_Q4_K_M` (~2.7 GB,
better at multi-step requests).

Any OpenAI-compatible server instead (Ollama, LM Studio, llama.cpp, vLLM, a
hosted API):

```bash
ollama pull qwen3.5:2b
OPENAI_BASE_URL=http://localhost:11434/v1 OPENAI_MODEL=qwen3.5:2b npm run start:mock
```

`OPENAI_API_KEY` is sent as a bearer token when set.

## Against your node (signet)

1. Run an RGB Lightning Node on signet, unlocked, with some signet sats.
2. Point the agent at it:

```bash
RLN_NODE_URL=http://localhost:3001 RECIPIENT_INVOICE='rgb:...' npm start
```

`RECIPIENT_INVOICE` is an RGB invoice from another wallet; without it the send
step is skipped. Spends prompt `[y/N]`; `--yes` approves them automatically
(fake node only, please). Issuing an asset or receiving RGB uses a free colored
UTXO, so keep a few sats on the node.

## What to change

- `src/index.ts`: the requests the agent runs and the system prompt.
- `src/setup.ts`: which tools it sees (`allow` list) and the model settings.
- `src/eval.ts`: the scenarios and what counts as a pass.
- Skills: the agent loads every skill packaged with `@kaleidorg/mind`. Write
  your own as a `SKILL.md` and add it to the `SkillRegistry`.

A request takes from tens of seconds to a few minutes on a laptop, mostly
reasoning time. `THINK` sets the reasoning budget in tokens (default 128).
