# Next.js LangChain File Chatbot

A Next.js application that lets you upload a PDF and chat with its contents using LangChain and the Groq LLM. No vector database or embeddings required — documents are processed on-the-fly for simplicity.

## How It Works

1. **Upload a PDF** — the file is saved to the `/uploads` directory on the server.
2. **Ask a question** — on each chat request, the PDF is loaded, extracted, and chunked into 1000-character segments (200-character overlap).
3. **Get an answer** — all chunks are passed as context to the Groq LLM, which returns a Markdown-formatted response.

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (App Router) |
| UI | React 19, Tailwind CSS v4 |
| LLM | Groq via `@langchain/groq` |
| PDF Parsing | `@langchain/community` PDFLoader, `pdf-parse` |
| Text Splitting | `@langchain/textsplitters` |
| File Upload | Multer |
| Markdown Rendering | `react-markdown` |

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/upload` | Accepts a PDF via FormData, saves to `/uploads/`, returns `{ filename }` |
| POST | `/api/process-document` | Validates the filename and returns ready status |
| POST | `/api/chat` | Loads PDF, chunks text, queries Groq LLM, returns response |

## Project Structure

```
src/
├── app/
│   ├── api/
│   │   ├── chat/route.ts           # On-the-fly PDF loading + LLM query
│   │   ├── embed/route.ts          # (unused/reserved)
│   │   ├── process-document/route.ts # Filename validation
│   │   └── upload/route.ts         # File upload handler
│   ├── layout.tsx
│   └── page.tsx                    # Main page wiring upload + chat
├── components/
│   ├── ChatInterface.tsx            # Chat UI with message history
│   └── FileUploadComponent.tsx      # Drag-and-drop / click upload
├── hooks/
│   ├── fileUploadConfig.ts
│   └── useFileUpload.ts
└── lib/
    ├── langchainSetup.ts            # Groq LLM init + text chunking
    ├── multerConfig.ts              # Multer file upload config
    ├── pdfLoader.ts                 # PDF extraction via LangChain
    ├── textSplitter.ts              # Text splitting utilities
    └── vectorStore.ts               # (unused in current flow)
uploads/                             # Uploaded PDFs stored here
```

## Getting Started

### Prerequisites

- Node.js 18+
- A [Groq API key](https://console.groq.com/)

### Setup

1. Clone the repository and install dependencies:

```bash
npm install
```

2. Create a `.env.local` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

3. Start the development server:

```bash
npm run dev
```

4. Open [http://localhost:3000](http://localhost:3000) in your browser.

## Text Chunking

- **Chunk size**: 1000 characters
- **Overlap**: 200 characters
- **Strategy**: Breaks at sentence boundaries (periods) when possible
- **Timing**: On-the-fly per chat request — not pre-computed

## Trade-offs

| Benefit | Trade-off |
|---|---|
| No vector database needed | PDF reloaded on every question |
| No embedding cost | All chunks sent to LLM (no filtering) |
| Simple, readable code | May hit context limits on very large PDFs |
| Easy to debug and extend | Single document per session |

## Environment Variables

| Variable | Description |
|---|---|
| `GROQ_API_KEY` | Required. Your Groq API key. |
