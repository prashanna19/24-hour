# ThavanAI — Cash Intelligence

**An AI-assisted cash-flow management and financial decision-support dashboard for small businesses.**

ThavanAI is a full-stack web application prototype that brings business cash position, upcoming obligations, expected receivables, informal borrowing, transaction imports, and cash-flow what-if analysis into one workspace. It is designed to help a small-business owner understand upcoming cash pressure and explore possible responses before making payment decisions.

> **Project status:** Prototype / demonstration application. The included data is mock data, and records are held in server memory rather than a persistent database. Do not use the displayed calculations or GST estimates as professional financial, tax, or accounting advice.

## Features

- **Cash-flow dashboard** — view the current bank-balance figure, cash runway indicators, obligations, receivables, and loan information.
- **Obligation tracking** — view and add payments such as rent, supplier payments, utilities, loan repayments, and GST obligations. New obligations receive a priority score based on the application's scoring formula.
- **Receivables tracking** — view and add expected incoming payments, including expected dates and confidence values.
- **Loan tracking** — record informal funding sources and available amounts.
- **Transaction import** — upload PDF documents, images, CSV files, and Excel spreadsheets for transaction extraction; manually add transactions as well.
- **Document extraction** — use Gemini when configured for supported document/spreadsheet extraction, with local parsing/OCR fallback paths in the backend.
- **Cash-flow what-if simulator** — adjust the starting balance, expected payment timing, a possible loan draw, and the safety-buffer threshold to recalculate the projected days-to-zero (D2Z).
- **Natural-language scenario input** — describe a cash-flow change in plain language; the backend uses Gemini when configured and a basic keyword-based demo fallback otherwise.
- **Scenario comparison** — compare three predefined payment-ordering examples.
- **AI-assisted alerts and email drafts** — generate cash-flow alert output and draft payment/collection messages. Some demo responses are template/mock based.
- **GST overview** — view the prototype's GST liability estimate and filing reminders. Values are illustrative and must be verified independently.
- **Dark and light themes** — switch the dashboard appearance.

## Tech stack

### Frontend

- React 19
- TypeScript
- Vite
- Chart.js and react-chartjs-2
- CSS

### Backend

- Node.js
- Express 5
- TypeScript (run with `tsx`)
- Google Gemini API (`@google/generative-ai`)
- Multer for file uploads
- Tesseract.js for OCR fallback
- `pdf-parse` for PDF text extraction
- `xlsx` for spreadsheet parsing

## Repository structure

```text
24-hour/
├── backend/
│   ├── data/
│   │   └── mockData.ts          # Demonstration data held in memory
│   ├── routes/
│   │   └── api.ts               # REST API routes and transaction import
│   ├── services/
│   │   ├── alertService.ts      # Alert generation
│   │   └── engine.ts            # D2Z and obligation scoring logic
│   ├── .env.example
│   ├── index.ts                 # Express server entry point
│   └── package.json
└── frontend/
    ├── src/
    │   ├── components/          # Shared layout and UI components
    │   ├── context/              # Shared application state
    │   ├── pages/                # Dashboard and feature pages
    │   ├── App.tsx
    │   └── main.tsx
    ├── package.json
    └── vite.config.ts
```

## Getting started

### Prerequisites

- Node.js and npm
- A Google Gemini API key for Gemini-backed functionality (optional; some features have demo fallbacks)

### 1. Clone the repository

```bash
git clone https://github.com/prasuhhh/24-hour.git
cd 24-hour
```

### 2. Configure and start the backend

```bash
cd backend
npm install
cp .env.example .env
```

Open `backend/.env` and add your Gemini API key if you want to enable the configured Gemini integrations:

```env
GEMINI_API_KEY=your_gemini_api_key_here
PORT=5001
```

Start the backend in development mode:

```bash
npm run dev
```

The API runs at `http://localhost:5001` by default.

### 3. Start the frontend

Open a second terminal from the repository root:

```bash
cd frontend
npm install
npm run dev
```

Open the local URL printed by Vite in your browser (typically `http://localhost:5173`).

### 4. Production build (frontend)

From the `frontend` directory:

```bash
npm run build
```

To preview the generated frontend build:

```bash
npm run preview
```

## API reference

All routes are mounted under `/api` on the backend server (default port `5001`).

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/dashboard` | Retrieve dashboard state, obligations, receivables, and loans |
| GET | `/api/alerts` | Generate/retrieve alert results |
| GET | `/api/obligations` | List obligations |
| POST | `/api/obligations` | Add an obligation |
| GET | `/api/receivables` | List receivables |
| POST | `/api/receivables` | Add a receivable |
| GET | `/api/loans` | List loans |
| POST | `/api/loans` | Add a loan/funding source |
| GET | `/api/transactions` | List parsed and manually entered transactions |
| POST | `/api/transactions/parse` | Upload and parse a transaction document (`multipart/form-data`, field name `file`) |
| POST | `/api/transactions/manual` | Add a transaction manually |
| POST | `/api/dashboard/whatif` | Recalculate projected days-to-zero from scenario parameters |
| POST | `/api/ai/draft-email` | Generate a payment or collection email draft |
| POST | `/api/ai/whatif-nl` | Convert a natural-language scenario into simulator parameters |

### Example: recalculate a what-if scenario

```bash
curl -X POST http://localhost:5001/api/dashboard/whatif \
  -H "Content-Type: application/json" \
  -d '{"bal":80000,"muraliEarly":0,"karimDelay":0,"loanDraw":0,"buf":10000}'
```

## How the prototype calculates cash runway

The backend's `recalculateD2Z` function starts with a cash balance plus any simulated loan draw, then applies a fixed set of example inflow and outflow events over a 45-day horizon. It returns the first day on which the simulated cash balance falls below the configured safety buffer. This is a prototype scenario model based on hard-coded example events, not a forecast trained on a user's historical bank data.

The obligation priority score is calculated by the `calculateObligationScore` function using the relationship and flexibility inputs together with the amount. The formula and sample inputs are implemented in `backend/services/engine.ts`.

## Configuration

| Variable | Description | Default |
|---|---|---|
| `PORT` | Backend HTTP port | `5001` |
| `GEMINI_API_KEY` | Google Gemini API key for configured AI features | Not set |

Never commit your real `.env` file or API keys. Keep secrets in local environment configuration.

## Current limitations

- The application currently uses in-memory mock data in `backend/data/mockData.ts`; changes are lost when the backend restarts.
- The cash-flow simulation uses a fixed set of sample events and should not be interpreted as a production-grade financial forecast.
- The scenario comparator contains predefined example scenarios.
- Gemini-backed features depend on a valid API key, network access, and an available model. Demo fallbacks are not equivalent to a live AI result.
- Imported files are processed by the backend; validate extracted transaction descriptions, amounts, and types before relying on them.
- GST values and reminders are illustrative UI calculations, not tax advice or an official filing integration.
- Authentication, user accounts, database persistence, and production deployment configuration are not included in the inspected project version.

## Contributing

Contributions, bug reports, and suggestions are welcome. For substantial changes, open an issue first to discuss the proposed update.

## License

No project-level open-source license was included in the supplied repository files. Unless a license is added, standard copyright restrictions apply; please contact the repository owner before reusing or redistributing the code.

---

**Built as a financial technology project prototype.**
