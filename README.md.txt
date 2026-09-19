# SmartCharge AI – EcoSync

A responsive full-stack prototype for AI-assisted EV charging energy management. All live values and forecasts are clearly presented as **prototype simulation/demo data**, not measured field performance.

## Stack
- Frontend: React + Vite, responsive CSS, Recharts, Lucide React
- Backend: Node.js + Express REST API + WebSocket (`ws`)
- Persistence: booking data in backend memory; settings in browser localStorage and mirrored to API
- Production path: Firebase/AWS/MQTT, ESP32/smart meters/OCPP, Python + XGBoost/LSTM

## Run locally
Requires Node.js 18+.

```bash
cd ecosync-smartcharge
npm install
npm run install:all
npm run dev
```

Open the Vite URL shown in the terminal (normally `http://localhost:5173`). API defaults to `http://localhost:4000`.

You can also run separately:
```bash
cd backend && npm install && npm run dev
cd frontend && npm install && npm run dev
```

## Environment
Copy `.env.example` values as needed. For a separately deployed backend, set `VITE_API_URL` for the frontend build.

## API documentation
- `GET /api/status` — current simulated KPIs + prototype decision
- `GET /api/forecast` — 24-hour simulated solar, demand, temperature, irradiance, cloud data
- `GET /api/bookings` — list prototype bookings
- `POST /api/bookings` — create booking; returns HTTP 409 for duplicate station/date/time
- `GET /api/settings` — current station settings
- `POST /api/settings` — update station settings
- WebSocket `/ws` — simulated status packet approximately every 3.5 seconds

### Example booking body
```json
{"station":"EcoSync Hub – Station 01","connector":"CCS2","date":"2026-09-19","time":"10:30 AM – 11:30 AM","duration":"60"}
```

## Database schema for production
No external database is required for this prototype. Suggested production collections/tables:
- `stations(id, name, location, max_grid_kw, min_battery_pct, preferred_renewable_pct)`
- `telemetry(id, station_id, timestamp, solar_kw, ev_demand_kw, battery_pct, grid_kw, grid_load_pct)`
- `forecasts(id, station_id, timestamp, horizon, solar_kw, ev_demand_kw, model_version)`
- `bookings(id, station_id, connector, user_id, start_at, duration_min, estimated_cost, renewable_pct, status)`
- `alerts(id, station_id, created_at, type, severity, message, read_at)`

## Deployment
### Frontend
Build with `npm run build` in `frontend/` and deploy `frontend/dist/` to Vercel, Netlify, Firebase Hosting, AWS Amplify, or S3 + CloudFront. Configure `VITE_API_URL` before building.

### Backend
Deploy `backend/` to Render, Railway, Fly.io, AWS ECS/Elastic Beanstalk, or another Node host. Expose the configured `PORT`, enable HTTPS/WSS, and restrict CORS to the frontend domain in production.

### Production integration notes
Replace simulated telemetry with authenticated MQTT/device ingestion, persist data in a database, validate charger control commands, add user authentication/authorization, and validate any trained ML model on pilot data before presenting performance claims as measured results.

## Responsive coverage
CSS breakpoints support phone, tablet, laptop and desktop layouts, including ~320px, 375px, 768px, 1024px and 1440px widths. Navigation collapses to a hamburger menu; cards/tables/booking/energy-flow layouts adapt without horizontal page overflow.
