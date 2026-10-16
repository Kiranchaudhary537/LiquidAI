# LiquidAI

An AI agent for managing DeFi liquidity through chat. Describe what you want in plain language, such as "add liquidity to the USDC/ETH pool on Polygon", and LiquidAI works out the parameters, asks for anything missing, and builds the transaction for you to sign in your wallet.

**Live demo:** https://liquid-ai-kappa.vercel.app

## Features

- **Add liquidity**: zap into a pool from a single token.
- **Remove liquidity**: withdraw from an existing position.
- **Migrate liquidity**: move a position between pools or providers.
- **Portfolio view**: fetches your open positions from The Graph.
- **Multi-chain**: includes Polygon support.

## Architecture

```
frontend/   Next.js 15 + React 19, wagmi/viem, RainbowKit, Tailwind
            API routes under src/app/api proxy chat, add/remove/migrate, pools and positions
ai/         FastAPI service using the OpenAI API (gpt-4o) with structured JSON output
            Keeps per-session chat context and prompt files in ai/prompts/
```

The frontend sends each chat message to the AI service. The service replies with either a follow-up question or a structured payload. The frontend turns that payload into a transaction.

## Getting started

### AI service

```bash
cd ai
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
echo "API_KEY=<your-openai-key>" > .env
uvicorn app:app --reload --port 8080
```

Or with Docker:

```bash
docker build -t liquidai-ai ai
docker run -p 8080:8080 -e API_KEY=<your-openai-key> liquidai-ai
```

Endpoints: `POST /chat` (`session_id`, `message`, `wallet_address`) and `POST /clear_context` (`session_id`).

### Frontend

```bash
cd frontend
pnpm install
```

Create `frontend/.env.local`:

```
NEXT_PUBLIC_ALCHEMY_KEY=<alchemy-key>
NEXT_PUBLIC_BLASTAPI_KEY=<blastapi-key>
```

```bash
pnpm dev
```

Open http://localhost:3000. The chat endpoint is configured in `frontend/src/app/api/chat/route.ts`. Point it at your local AI service (`http://localhost:8080/chat`) for development.
