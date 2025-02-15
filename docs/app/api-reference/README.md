<p align="center">
  <img src="_media/banner.png" alt="Daydreams">
</p>

> ⚠️ **Warning**: This is alpha software under active development. Expect
> frequent breaking changes and bugs. The API is not yet stable.

# Cross-chain Generative Agents

[![npm version](https://badge.fury.io/js/%40ai-sdk%2Fcore.svg)](https://badge.fury.io/js/%40ai-sdk%2Fcore)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Node.js CI](https://github.com/ai-sdk/core/actions/workflows/node.js.yml/badge.svg)](https://github.com/ai-sdk/core/actions/workflows/node.js.yml)
[![Status](https://img.shields.io/badge/Status-Alpha-orange.svg)]()
[![Documentation](https://img.shields.io/badge/Documentation-docs-blue.svg)](https://docs.daydreams.ai)
[![Twitter Follow](https://img.shields.io/twitter/follow/daydreamsai?style=social)](https://twitter.com/daydreamsagents)
[![GitHub stars](https://img.shields.io/github/stars/daydreamsai/daydreams?style=social)](https://github.com/daydreamsai/daydreams)

Daydreams is a powerful framework for building generative agents that can
execute tasks across any blockchain or API.

| Feature                | Description                                                          |
| ---------------------- | -------------------------------------------------------------------- |
| 🔗 Chain Agnostic      | Execute transactions and interact with any blockchain network        |
| 👥 Multi-Expert System | Leverage specialized modules working together to solve complex tasks |
| 🧠 Context Management  | Simple yet powerful memory and context handling system               |
| 🎯 Goal-Oriented       | Long-term planning and goal-oriented behavior capabilities           |
| 💾 Persistent Memory   | Built-in support for storing and retrieving long-term information    |
| 🤔 Advanced Reasoning  | Multi-step reasoning using Hierarchical Task Networks                |

Want to contribute? Check our
[issues](https://github.com/daydreamsai/daydreams/issues) for tasks labeled
`good first issue`.

## Supported Chains

<p> 
  <a href="#chain-support">
  <img src="_media/eth-logo.svg" height="30" alt="Ethereum" style="margin: 0 10px;" />
  <img src="_media/arbitrum-logo.svg" height="30" alt="Arbitrum" style="margin: 0 10px;" />
  <img src="_media/optimism-logo.svg" height="30" alt="Optimism" style="margin: 0 10px;" />
  <img src="_media/solana-logo.svg" height="30" alt="Hyperledger" style="margin: 0 10px;" />
  <img src="_media/Starknet.svg" height="30" alt="StarkNet" style="margin: 0 10px;" />
  <img src="_media/hl-logo.svg" height="30" alt="Hyperledger" style="margin: 0 10px;" />
  </a>
</p>

## Quick Start

### Prerequisites

- Node.js 18+ using [nvm](https://github.com/nvm-sh/nvm)
- [bun](https://bun.sh/)

### LLM Keys

You'll need an API key for the LLM you want to use. We recommend using
[Groq](https://groq.com/) for most use cases.

- [OpenAI](https://openai.com/)
- [Anthropic](https://anthropic.com/)
- [Groq](https://groq.com/)
- [Gemini](https://deepmind.google/technologies/gemini/)

### Install

```bash
# Install dependencies
bun install

# Copy environment variables
cp .env.example .env

# Run an example
bun run example:discord
```

## Your First Dreams Agent

Dreams agents are all functional. `createDreams` is a function that returns an
agent object, which can be run with `await agent.run()`. Inject discord,
telegram, or any other input/output to the agent and define your own actions.

```typescript
import { createDreams } from "@daydreamsai/core";
import { createMemoryStore, defaultContext } from "@daydreamsai/core";
import { openai } from "@ai-sdk/openai";
import { input, output } from "@daydreamsai/core";
import { z } from "zod";
import * as readline from "readline/promises";

const groq = createGroq({
  apiKey: process.env.GROQ_API_KEY!,
});

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

const agent = createDreams({
  memory: createMemoryStore(),
  model: openai("gpt-o1-mini"),
  inputs: {
    message: input({
      schema: z.object({
        user: z.string(),
        text: z.string(),
      }),
      handler(params, ctx) {
        console.log("User:" + params.text);
        ctx.memory.inputs.push({
          ref: "input",
          type: "message",
          params: { user: params.user },
          data: params.text,
          timestamp: Date.now(),
          processed: false,
        });
        return true;
      },
      async subscribe(send, agent) {
        while (true) {
          const question = await rl.question(">");

          send(defaultContext, "main", {
            user: "admin",
            text: question,
          });
        }
      },
    }),
  },
  outputs: {
    message: output({
      description: "",
      schema: z.string(),
      handler(content, ctx) {
        console.log("Agent:" + content);
        return true;
      },
      examples: ["Hi!"],
    }),
  },
});

await agent.start();
```

## Contributing

Looking to contribute? We'd love your help!

If you are a developer and would like to contribute with code, please open an
issue to discuss before opening a Pull Request.

### Star History

[![Star History Chart](https://api.star-history.com/svg?repos=daydreamsai/daydreams&type=Date)](https://star-history.com/#daydreamsai/daydreams&Date)
