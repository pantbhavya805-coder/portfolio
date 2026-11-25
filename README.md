# Bhavya Pant — Portfolio

A personal portfolio site for a backend / distributed-systems engineer. It opens with a scroll-driven hero animation, then walks through a career story, case studies with animated architecture diagrams, an experience timeline, and a tech-stack grid. Visitors can reach out through a contact form that logs the message to Google Sheets, emails it to the owner, and sends the visitor an automatic reply.

Built with **React 18 + TypeScript + Vite 6 + Tailwind CSS**. It deploys to **Vercel** as a static site with one serverless function.

---

## Features

- **Scroll-scrubbed hero.** A 75-frame image sequence is drawn on a `<canvas>` and moves with the scroll position across a 550vh sticky section. A text overlay changes across five scroll stages.
- **Career scrollytelling.** Cards that cover the Java, Kafka, AI and DevOps parts of the journey.
- **Case studies.** Project cards open a tabbed modal (architecture / highlights / tech) with live SVG flow diagrams:
  - Fortivance: AI third-party risk and incident response platform for Indian banks (GoDaddy Hackathon)
  - Amcom AI Enterprise Platform: generative AI WhatsApp automation
  - Solar & EV Charge IoT Platform: high-throughput telemetry and OCPI-based EV charging
  - Resource Management System (Visa): secure bank-offer APIs with RBAC
- **Experience timeline** with metric badges and tech pills.
- **Filterable skills grid** (Backend, Messaging, AI, Cloud, DevOps, Frontend).
- **Resume modal** with a quick summary and a PDF download.
- **Contact form** backed by a serverless API (see [Contact API](#contact-api)).
- Responsive from 320px to 1440px+, with a dark glassmorphic theme and Framer Motion animations.

## Tech Stack

| Layer      | Tools                                                   |
|------------|---------------------------------------------------------|
| UI         | React 18, TypeScript, Framer Motion, Lucide icons       |
| Styling    | Tailwind CSS 3.4, PostCSS, custom utilities in `globals.css` |
| Build      | Vite 6, `tsc` strict type checking                      |
| Backend    | Vercel serverless function (`api/contact.ts`), Nodemailer (Gmail SMTP), Google Apps Script webhook |

## Getting Started

### Prerequisites

- Node.js 18+ (the contact API relies on the built-in `fetch`)
- npm

### Install and run

```bash
npm install
npm run dev        # http://localhost:3000
```

> `npm run dev` serves only the front end. To try the contact form locally, run the project with the [Vercel CLI](https://vercel.com/docs/cli) so that `/api/contact` is served too:
>
> ```bash
> npm i -g vercel
> vercel dev
> ```

### Scripts

| Command           | What it does                                   |
|-------------------|------------------------------------------------|
| `npm run dev`     | Starts the Vite dev server on port 3000        |
| `npm run build`   | Type-checks with `tsc`, then builds to `dist/` |
| `npm run preview` | Serves the production build locally            |

Run `npm run build` after structural changes. It fails on any TypeScript error.

## Environment Variables

Create a `.env` file for local use (it is git-ignored) and set the same values in your Vercel project settings:

```env
# Google Apps Script web-app URL that appends form submissions to a sheet
GOOGLE_SHEETS_WEBHOOK_URL=https://script.google.com/macros/s/XXXX/exec

# Gmail App Password for the account that sends the emails
EMAIL_ACCESS_TOKEN=xxxx xxxx xxxx xxxx
```

`EMAIL_TOKEN` also works in place of `EMAIL_ACCESS_TOKEN`. Use a Gmail [App Password](https://support.google.com/accounts/answer/185833), not your normal account password.

## Contact API

`POST /api/contact` with a JSON body:

```json
{ "name": "Jane Doe", "email": "jane@example.com", "message": "Hi there!" }
```

The handler in [`api/contact.ts`](api/contact.ts):

1. Checks that `name`, `email` and `message` are all present (if not, it returns `400`).
2. Logs the submission to Google Sheets through the webhook, if one is configured.
3. Emails the inquiry to the portfolio owner through Gmail SMTP, with `replyTo` set to the visitor.
4. Sends the visitor an automatic "thank you" reply.
5. Returns `200` if either the sheet log or the owner email succeeded, and `500` otherwise:

```json
{ "success": true, "sheetLogged": true, "emailSent": true, "message": "Inquiry processed and auto-reply sent" }
```

The sender and recipient Gmail address is set directly in `api/contact.ts`. Change it there if you fork the project.

## Project Structure

```
portfolio/
├── api/
│   └── contact.ts              # Serverless contact handler (Sheets + Gmail)
├── public/
│   ├── Resume_Bhavya_Pant.pdf  # Resume served at /Resume_Bhavya_Pant.pdf
│   └── sequence/               # 75 hero animation frames (ezgif-frame-001…075.jpg)
├── src/
│   ├── components/
│   │   ├── Navbar/             # Fixed glass navbar and mobile drawer
│   │   ├── HeroCanvas/         # Canvas frame renderer and lighting
│   │   ├── HeroOverlay/        # Five-stage scroll overlay and CTAs
│   │   ├── ScrollStory/        # Career scrollytelling cards
│   │   ├── Projects/           # Case study grid, modal, SVG diagrams
│   │   ├── Timeline/           # Experience timeline
│   │   ├── Skills/             # Filterable tech-stack grid
│   │   ├── Footer/             # Footer with live IST clock
│   │   ├── ContactModal.tsx
│   │   └── ResumeModal.tsx
│   ├── hooks/
│   │   ├── useFrameSequence.ts # Preloads the hero frames
│   │   └── useScrollProgress.ts# Scroll progress from 0 to 1 for a section
│   ├── styles/globals.css      # Tailwind base, glass utilities, scrollbar
│   ├── App.tsx                 # Puts the sections and modals together
│   └── main.tsx                # React entry point
├── index.html
├── tailwind.config.js          # Theme colors, fonts, keyframes
└── vite.config.ts              # Dev server on :3000, "@" → src alias
```

## Customizing

- **Content.** Projects, timeline entries and skills are defined as arrays at the top of their component files (for example `src/components/Projects/ProjectsSection.tsx`).
- **Resume.** Replace `public/Resume_Bhavya_Pant.pdf`, or update `RESUME_URL` in `ResumeModal.tsx`.
- **Hero animation.** Put new frames in `public/sequence/` using the same naming pattern, then update `TOTAL_FRAMES` in `src/hooks/useFrameSequence.ts`.
- **Hero length.** The hero section height (`h-[550vh]` in `App.tsx`) is tied to the stage thresholds in `HeroOverlay.tsx`. If you change one, change the other.
- **Diagrams.** On mobile, the SVG connector lines are hidden (`hidden md:block`) and the diagram nodes stack vertically.

## Deployment

The project is set up for Vercel:

1. Import the repository in Vercel. It detects the Vite framework preset automatically (build: `npm run build`, output: `dist`).
2. Add `GOOGLE_SHEETS_WEBHOOK_URL` and `EMAIL_ACCESS_TOKEN` under **Project Settings → Environment Variables**.
3. Deploy. Files in `api/` are deployed as serverless functions automatically.

## Contact

**Bhavya Pant**. Use the contact form on the live site, or open an issue in this repository.
