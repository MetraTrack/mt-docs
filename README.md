# MetraTrack — Personal Training & Activity Intelligence System
Automated training plans, calorie intelligence, and activity tracking powered by Samsung Health, Telegram, and OpenAI.

## Why MetraTrack Exists
I created this project to achieve my personal goals — lose weight, become fit, and track my calories and training more intelligently. Because I use a Samsung Galaxy Watch, Telegram as my primary communication tool, and OpenAI for insights, the tech stack may look unusual for a typical fitness app. MetraTrack started as a personal tool, but is designed to scale into a clean, open-source solution that anyone can use or host themselves.

## Overview
MetraTrack connects:
- Samsung Galaxy Watch activity and health data
- Telegram bot as the main communication interface
- A minimal Angular mini-app for structured inputs
- Image- and text-based calorie estimation using OpenAI
- Adaptive weekly training plans that are regenerated based on the previous week's results
- A lightweight Android companion app for background data sync

![MetraTrack Context Diagram](/diagrams/context.png)

The architecture is intentionally simple but robust enough for multi-user hosting from day one.

## System Architecture

## 1. Telegram Bot
The unified conversational interface.

### Responsibilities:
- Natural-language reminders and daily nudges
- Weekly and monthly summaries
- Receiving:
    - Food photos
    - Text descriptions of food
    - Weight entries
- Launching the Angular Mini App
- Handling first-time user onboarding
- Supporting multi-user registration
- Upload of external training plans (JSON)

All business logic and analytics are handled by the backend.

## 2. Angular Mini App (Telegram WebApp)
Used **only for input**, including onboarding and structured data collection.

### Responsibilities:
- Registration form
- Initial user setup
- User preferences (API keys etc.)
- Weight input

(In future versions the mini-app may expand to show charts, weekly stats, weight trends, and dashboards.)

## 3. Backend (NestJS)
The core intelligence and orchestration layer.

### Key modules:

### User & Auth
- Creates new users automatically
- Handles authentication for Telegram and mini-app
- Generates API keys for Android companion app
- Supports multi-user hosting

### Plan Engine
- Follow first generated personal training plans
- Plans could be regenerated weekly based on last week's metrics if needed
- Supports external plan import via JSON

### Food Intelligence
- Receives meal photos
- OR receives text descriptions if the photo is unavailable
- Uses OpenAI Vision or text model to estimate calories
- Stores food data for analytics

### Samsung Sync Layer
- Receives background sync from Android companion
- Handles:
    - Steps
    - Workouts
    - Burned calories
    - Heart rate
    - Sleep data
    - Recovery metrics

### Analytics
- Weekly and monthly metrics
- Insight generation
- Uses OpenAI optionally for natural-language summaries

### Cron Jobs
Used for:
- Daily reminders
- Sync checks
- Report generation

## 4. Android Companion App (Angular Native)
A minimal background application that bridges Samsung Health and the backend.

### Responsibilities:
- Requests Samsung Health / Health Connect permissions
- Asks for backend URL and API key
- Periodically syncs:
    - Steps
    - Workouts
    - Burned calories
    - Heart rate
    - Sleep data
    - Recovery metrics
- Minimal UI:
    - Permission state
    - Connection state
- Sends local notifications if connection fails

## 5. Infrastructure (Docker)
MetraTrack uses a clean dockerized setup:

- Caddy — Reverse Proxy AND automatic certificates
- NestJS API
- Postgres
- MinIO — for food images
- Dozzle — for container logs

## Data Flows

### Training & Activity
Watch → Samsung Health → Android App → Backend → Reports

### Food Entry
Telegram → Backend →
- If photo: OpenAI Vision
- If text: OpenAI text model  
  Backend → Summary → Database

### Weight
Telegram Mini App → Backend → Analytics

### Training Plan Revision
Backend → Analytics → OpenAI → Backend → User

### New User Setup
Telegram Bot → Backend → Mini App → Companion App → Complete onboarding

## Technology Stack

| Component | Technology |
|----------|------------|
| Backend | NestJS (TypeScript) |
| Telegram Bot | Telegraf / NestJS bot module |
| Mini App | Angular |
| Companion App | Angular Native (NativeScript / Capacitor) |
| Database | Postgres |
| Object Storage | MinIO |
| Reverse Proxy & Certs | Caddy |
| Logs | Dozzle |
| AI Services | OpenAI GPT-5 + Vision + Text models |

## Current Realization Plan (v1)
v1 includes:
- Multi-user hosting
- First-time user onboarding
- External plan import (JSON)
- Weekly adaptive plan regeneration
- Samsung Health data sync (activity, calories, HR, sleep, recovery)
- Food calorie estimation from photo or text
- Weight tracking
- Daily and weekly reminders
- Angular mini-app for structured inputs
- Android companion app for background sync
- Full Docker infrastructure with Caddy + MinIO + Dozzle
- Cron-based scheduling

## Future Plans
- Mini-app dashboards and charts
- Weekly/monthly visual reports
- Weight trend graphs
- Calorie balance visualization
- Integrations with additional device ecosystems:
    - Apple Watch
    - Google Fit
    - Fitbit
    - Garmin
    - Polar
    - Oura Ring
- Long-term fitness projections
- Social/group workout support
- Backup/export system
- Plugin architecture for more AI-generated insights

## 🤝 Contributing
If you'd like to help enhance MetraTrack (not just the docs), contributions are welcome!  
You can improve:
- Backend logic and new modules
- Mini-app UX
- Android companion integrations
- Infrastructure and automation
- Device integrations (Apple Watch, Fitbit, Garmin, etc.)
- AI-based insights and training algorithms

To contribute:
1. Open an issue describing what you want to improve.
2. Follow the project structure and coding conventions.
3. Submit clear and focused pull requests.

A full `CONTRIBUTING.md` may be added as the project grows.

## 📄 License
MetraTrack is released under the **MIT License**.  
You are free to use, modify, and distribute the project for personal or commercial purposes.

See the `LICENSE` file for details.