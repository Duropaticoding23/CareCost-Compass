# CareCost Compass

AI-powered tool that reads a prescription, estimates what the medicines cost, shows the share of household income they take, and lists lower-priced options with the same active ingredient and strength. Built for the NYLP AI Hackathon 2026.

## Problem

Families who pay for medicines out of pocket often cannot see what a prescription costs per month, what share of household income it takes, or that products with the same active ingredient and strength may cost less. High out-of-pocket health spending can push households into financial hardship.

## Solution

The user uploads a photo of a prescription or types the medicines. The app reads the prescription with AI, lets the user review and correct what was read, matches each medicine to a price table, and shows the estimated cost, the share of household income, possible savings and a yearly comparison. It does not recommend a switch. Every screen tells the user that a pharmacist or doctor must confirm any change.

## Features

- Prescription photo or typed input
- AI extraction of brand, active ingredient, strength, doses per day and duration
- Review and edit step before any calculation
- Price matching and cost maths in plain code (no AI in the numbers)
- Share of household income, flagged above 10 percent
- Yearly cost chart for current product vs lowest-priced product
- Plain-language summary and three questions for the pharmacist
- Price catalog viewer with the source of each price

## Tech Stack

React, TypeScript, Vite, Tailwind CSS, Express (Node.js), built and deployed with Google AI Studio (Build mode) on Cloud Run.

## AI / API Usage

- Google Gemini API through `@google/genai`, called only from `server.ts`. The key is a server-side environment variable and is never sent to the browser.
- `POST /api/extract-prescription`: sends the image or text with a strict JSON schema. Returns `brand`, `active_ingredient`, `strength`, `doses_per_day`, `duration_days`. Temperature 0.1.
- `POST /api/consultation-summary`: sends the computed costs and returns a short summary and three pharmacist questions. The prompt forbids dosing advice and advice to switch or stop a medicine.
- Matching, cost estimates, savings and income share are computed in `src/utils/pricingEngine.ts`, so they can be checked by hand.
- Uploaded images are held in memory for the request and are not saved by the app's server. They are sent to the Gemini API for reading.

## Setup Instructions

1. Clone the repository.
2. Run `npm install`.
3. Copy `.env.example` to `.env.local` and set `GEMINI_API_KEY` (do NOT commit real keys).
4. Run `npm run dev` and open http://localhost:3000.

## Data

The price table is `src/data/priceDatabase.ts`. Each row has `brand_name`, `active_ingredient`, `strength`, `pack_size`, `price`, `currency` and `source`.

## Known Limitations

- The price table is small ([NUMBER] medicines) and prices change over time.
- Costs are estimated from unit price and do not round up to whole packs.
- If a brand is not in the table, the app assumes the higher-priced product and says so.
- AI can misread handwriting, which is why the user reviews the extracted list.
- The tool gives price information only. It is not medical advice.
