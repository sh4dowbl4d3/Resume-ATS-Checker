# ResumeLint

ResumeLint is an in-browser resume checker and ATS analyzer powered by WebAssembly. All parsing and scoring run locally on your machine without server requests.

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-brightgreen)](https://sh4dowbl4d3.github.io/ResumeLint/)
[![Deploy to GitHub Pages](https://github.com/sh4dowbl4d3/ResumeLint/actions/workflows/deploy.yml/badge.svg)](https://github.com/sh4dowbl4d3/ResumeLint/actions/workflows/deploy.yml)
[![WebAssembly](https://img.shields.io/badge/WebAssembly-Rust-purple?logo=webassembly)](https://webassembly.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react)](https://react.dev/)
[![License](https://img.shields.io/badge/License-MIT-emerald.svg)](LICENSE)

![ResumeLint Interface](screenshots/screenshot1.png)

## Live application

Try the app in your browser: [sh4dowbl4d3.github.io/ResumeLint](https://sh4dowbl4d3.github.io/ResumeLint/)

It requires no installation or account. Everything runs locally in your browser.

## Privacy

Resume scanners often upload documents to remote servers for processing. ResumeLint performs all analysis on your machine:

- Files (PDF, DOCX, TXT, and Markdown) parse directly in browser memory.
- The scoring engine runs in a dedicated Web Worker via WebAssembly compiled from Rust.
- Network requests are not used during analysis. You can disconnect your network after the page loads and continue using the app.

## How ATS scoring works

ResumeLint calculates a deterministic score from 0 to 100 across five weighted categories:

| Category | Weight | Criteria |
|:---|:---:|:---|
| Keyword match | 30% | Unigram and bigram frequency ratios between the resume and job description |
| Skills match | 25% | Match against 120+ technical and domain skills, including aliases and variants |
| Experience relevance | 20% | Action verb density, chronological markers, and role terminology |
| Formatting and readability | 15% | Layout checks for tables, multi-column text, text boxes, and icon bullets |
| Section completeness | 10% | Presence of standard resume sections (Summary, Experience, Education, Skills) |

## Architecture

```
ResumeLint/
├── wasm/                     # Core Rust ATS scoring engine (compiled to WebAssembly)
│   ├── src/
│   │   ├── lib.rs            # WASM bridge & exported functions
│   │   ├── scorer.rs         # 5-dimension scoring orchestrator
│   │   ├── keywords.rs       # Tokenization & unigram/bigram keyword extractor
│   │   ├── skills.rs         # 120+ skill taxonomy & variant matcher
│   │   ├── sections.rs       # Standard resume section detectors
│   │   ├── formatting.rs     # ATS parsing hazard and layout checker
│   │   └── types.rs          # Data models & Serde serialization
│   └── tests/                # Baseline deterministic parity tests
├── frontend/                 # React 18 SPA (Vite + Tailwind CSS)
│   ├── src/
│   │   ├── components/       # UI components (ScoreGauge, Navbar, Footer, PrivacyBanner)
│   │   ├── pages/            # Home (Analyzer/Dropzone) and Results (Report & Breakdown)
│   │   ├── lib/
│   │   │   ├── ats-engine/   # WebAssembly JS loader & wrapper
│   │   │   ├── ats-worker/   # Web Worker manager with monotonic sequence tracking
│   │   │   ├── parsers/      # Geometry-aware PDF, DOCX, and TXT in-browser parsers
│   │   │   └── capabilities.js # Browser capability detector & input sanitizer
│   │   └── api.js            # Unified local-first ATS API adapter
│   └── public/               # Static assets, Web App Manifest, SEO sitemap
├── docs/                     # System architecture & contract specifications
│   └── architecture.md
├── scripts/                  # Build scripts
│   └── build_wasm.sh
└── .github/workflows/        # Automated CI/CD for GitHub Pages
    └── deploy.yml
```

The system separates document extraction, worker communication, and scoring:
- Rust with `wasm-bindgen` and `serde-wasm-bindgen` powers the WebAssembly scoring engine.
- React 18 and React Router v7 handle UI routing and state, styled with Tailwind CSS and Lucide icons.
- In-browser document extraction uses `pdfjs-dist` for PDF layout extraction, `mammoth` for DOCX files, and `FileReader` for plain text or Markdown.
- GitHub Pages hosts the static client assets.

## Development and testing

- Run Rust tests: `cargo test --manifest-path wasm/Cargo.toml`
- Run frontend tests: `npm test` (from `frontend/`)
- Rebuild WebAssembly binary: `bash scripts/build_wasm.sh`

## Export options

You can export evaluation results from the results screen:
- Markdown (`.md`): copy to clipboard or download as a formatted report.
- JSON (`.json`): structured scoring breakdown and extracted attributes.
- Print or PDF: printer-friendly styling for saving a PDF report.

## License

Distributed under the [MIT License](LICENSE).
