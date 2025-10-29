# SimplerTaskTracker Backend

This package contains the standalone JSON Server backend used by the Simpler Task Tracker frontend. The service exposes a REST API at `/tasks` backed by the `db.json` file and is designed for deployment on [Railway](https://railway.app/).

## Prerequisites

- Node.js 18+ (matches Netlify/Railway default runtime)
- npm (ships with Node)
- Railway CLI: `npm install -g @railway/cli`
- Railway account with an active project/plan

## Local Development

```bash
npm install

# Start json-server locally; defaults to http://localhost:3001
npm run start
```

Environment variables:

| Variable | Purpose | Default |
|----------|---------|---------|
| `PORT`   | Port for `json-server` to listen on | `3001` |

Railway automatically injects `PORT`, so no manual configuration is required during deploys.

## Deploying to Railway

1. **Authenticate**
   ```bash
   railway login
   ```
2. **Initialize or link** to a Railway project from this directory:
   ```bash
   railway init    # or `railway link` if the project already exists
   ```
3. **Deploy** with the default start command:
   ```bash
   railway up
   ```
   The CLI will build a Docker image, install dependencies via `npm install`, and run `npm run start` (defined in `package.json`).
4. **Copy the public URL** that Railway prints after a successful deploy (e.g. `https://<service>.up.railway.app`). This exposes the `/tasks` endpoint.

### Post-Deploy Checklist

- `railway logs` – confirm the service booted and is listening on the injected port.
- `curl https://<service>.up.railway.app/tasks` – verify the API responds with JSON.
- Ensure the Railway plan keeps the service awake if you need near-instant cold-starts.

## Connecting the Frontend (Netlify)

The Vite frontend reads the backend URL from the `VITE_API_URL` environment variable.

1. Navigate to your Netlify site settings → **Build & deploy** → **Environment**.
2. Add or update `VITE_API_URL` with the Railway URL from above (include the protocol, e.g. `https://<service>.up.railway.app`).
3. Redeploy the site (trigger through Netlify UI or CLI).
4. After the deploy completes, open the Netlify site and verify create/update/delete operations succeed; network requests should target the Railway host.

## Troubleshooting Tips

- **CORS errors:** `json-server` sends permissive CORS headers by default. If issues arise, check Railway logs and confirm no proxy is stripping headers.
- **404 responses:** Ensure the frontend points to the correct base URL and includes the `/tasks` path when making requests.
- **Cold starts:** On free tiers, Railway may scale to zero. The first request can take longer; consider upgrading plans if latency is unacceptable.
- **Seed data:** Modify `db.json` in this repo before deploying if you need initial tasks. Future changes require a redeploy or direct edits via Railway’s data interface.

## Reference

- `package.json` start command: `json-server --watch db.json --host 0.0.0.0 --port ${PORT:-3001}`
- API schema: tasks contain `id`, `title`, and `description` fields.
