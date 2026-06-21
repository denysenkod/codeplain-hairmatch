# HairMatch

HairMatch is a web app for finding realistic haircut and styling ideas from a personal profile, a guided quiz, or an uploaded selfie. It helps people move from vague inspiration to a practical style shortlist they can save, compare, share, and take to a barber or stylist.

## Problem

Choosing a haircut is usually messy. People collect screenshots from social media, guess whether a style will work with their face shape or hair texture, and often arrive at the salon without a clear brief. HairMatch solves that by turning preference answers, hair attributes, inspiration images, and saved styles into a structured recommendation flow.

The app is designed to answer three practical questions:

- What styles match my hair type, length, gender expression, maintenance preference, and visual taste?
- What would this haircut look like on me before I commit?
- How can I save a useful brief that a stylist can understand quickly?

## What It Does

- Guides users through a style quiz.
- Loads hairstyle recommendations from a D1-backed gallery.
- Filters styles by hair type, length, upkeep, gender, subtype, and other classification metadata.
- Lets users upload a selfie and save profile references.
- Supports favorites and saved style briefs.
- Provides a try-on flow that sends a selfie plus a hairstyle reference to OpenAI image editing.
- Stores structured quiz responses, uploaded photo metadata, favorites, and gallery classifications.
- Serves the frontend and backend from one Cloudflare Worker.

## Tech Stack

- Frontend: vanilla HTML, CSS, and JavaScript.
- Backend: Cloudflare Workers.
- Database: Cloudflare D1.
- Hosting: Cloudflare Workers static assets with Worker-first API routing.
- AI: OpenAI image editing for haircut try-on.
- Tooling: Wrangler for local development, migrations, secrets, and deployment.
- Tests: Node.js built-in test runner.

## Architecture

The app is a single-page frontend served from the Worker assets binding. Runtime screens are rendered into the page by JavaScript, while API requests are handled by `server.ts`.

Core API routes:

- `GET /api/status` checks Worker and D1 health.
- `GET /api/gallery` returns paginated hairstyle recommendations.
- `POST /api/gallery` adds gallery records.
- `GET /api/quiz-responses` and `POST /api/quiz-responses` manage quiz data.
- `GET /api/user-photos` and `POST /api/user-photos` manage uploaded photo records.
- `GET /api/favorites` and `POST /api/favorites` manage saved styles.
- `GET /api/try-on/usage` checks the try-on generation limit.
- `POST /api/try-on` generates a haircut try-on image.

The main persistent store is D1. The important tables include:

- `gallery_images` for style images and classification metadata.
- `quiz_responses` for user quiz answers.
- `user_photos` for uploaded selfie/profile image data.
- `favorite_images` for saved hairstyle IDs.
- `try_on_generations` for try-on usage tracking.

## Codeplain Usage

HairMatch was generated with Codeplain end to end. Codeplain was used to create the project structure, Cloudflare Worker backend, frontend screens, D1 schema, migrations, API routes, gallery flow, quiz logic, favorites, profile uploads, and the OpenAI-powered try-on feature.

After the Codeplain generation step, general coding agents were used only for small finishing changes: style polish, landing-page refinements, cache-busting updates, deployment fixes, and Cloudflare configuration cleanup. The core application, data model, and feature set came from Codeplain.

This workflow made Codeplain especially useful because it produced a complete full-stack prototype quickly: not just a static mockup, but a deployable Cloudflare app with real persistence, API endpoints, image classification metadata, and an AI try-on path.

## Local Development

Install dependencies:

```bash
npm install
```

Start Wrangler dev:

```bash
npm start
```

Fallback local server:

```bash
node local-dev.mjs
```

Run tests:

```bash
npm test
```

## D1 Database

Apply local migrations:

```bash
npm run db:migrate:local
```

Apply remote migrations:

```bash
npm run db:migrate:remote
```

List databases:

```bash
npm run db:list
```

The deployed HairMatch database is bound in `generated/hairmatch-expanded/wrangler.toml` as:

```toml
[[d1_databases]]
binding = "DB"
database_name = "hairmatch"
database_id = "361684ac-6e4a-4ac7-bfa9-83c4306fe92b"
migrations_dir = "migrations"
```

## OpenAI Try-On Setup

The deployed Worker reads the OpenAI key from a Cloudflare secret named:

```bash
OPENAI_API_KEY
```

Set it with Wrangler from the generated app folder:

```bash
cd generated/hairmatch-expanded
npx wrangler secret put OPENAI_API_KEY
```

The secret must be attached to the `hairmatch` Worker. Do not put the API key in frontend JavaScript, D1, or committed config files.

## Deployment

Deploy manually from the generated app folder:

```bash
cd generated/hairmatch-expanded
npm run deploy
```

That command applies remote D1 migrations and deploys the Worker.

Deploy only the Worker:

```bash
npx wrangler deploy
```

## Project Structure

```text
generated/hairmatch-expanded/server.ts            Cloudflare Worker API and asset routing
generated/hairmatch-expanded/wrangler.toml        Worker, assets, and D1 binding configuration
generated/hairmatch-expanded/migrations/          D1 schema and seed migrations
generated/hairmatch-expanded/frontend/index.html  Single-page app shell
generated/hairmatch-expanded/frontend/styles.css  CSS entrypoint
generated/hairmatch-expanded/frontend/app.js      JavaScript entrypoint
generated/hairmatch-expanded/frontend/css/        Split styles by feature area
generated/hairmatch-expanded/frontend/js/         Split frontend logic by feature area
```

## Status

HairMatch is deployed on Cloudflare Workers and uses D1 for production data. The app is structured as a prototype that can be extended with R2-backed image storage, stronger admin controls, and larger-scale recommendation logic as usage grows.
