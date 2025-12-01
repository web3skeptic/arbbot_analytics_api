# Arbbot Analytics API

REST API for Circles Arbbot analytics - provides endpoints for price snapshots and liquidity observations.

## Features

- 📊 Price snapshot endpoints with statistics
- 💧 Liquidity observation heatmaps and time series
- 🚀 Optimized for Digital Ocean App Platform
- 🔒 Secure database connections with SSL
- ✅ Health check endpoint for monitoring
- 🌐 CORS enabled for frontend integration

## API Endpoints

### Health Check
- `GET /health` - Health status

### Price Snapshots
- `GET /api/snapshots?limit=5&offset=0` - List snapshots (paginated)
- `GET /api/snapshot/:id` - Get specific snapshot with statistics
- `GET /api/latest-snapshot` - Redirect to latest snapshot

### Liquidity Analytics
- `GET /api/liquidity/heatmap?hours=24&min_observations=3` - Aggregated liquidity heatmap
- `GET /api/liquidity/top-pairs?limit=20&sort=avg_liquidity` - Top performing pairs
- `GET /api/liquidity/timeseries?source=0x...&target=0x...&hours=24` - Time series data
- `GET /api/liquidity/stats?hours=24` - Overall statistics

## Development

### Prerequisites
- Node.js >= 18
- PostgreSQL database (Digital Ocean managed)

### Installation

```bash
# Install dependencies
npm install

# Copy environment variables
cp .env.example .env

# Edit .env with your database credentials
nano .env

# Build the project
npm run build

# Run in development
npm run dev

# Run in production
npm start
```

### Environment Variables

Create a `.env` file with:

```env
DB_HOST=your-db-host
DB_PORT=25060
DB_NAME=bot_activity
DB_USER=bot
DB_PASSWORD=your-password
DB_SSL=true
PORT=8080
```

## Deployment to Digital Ocean

### Option 1: Using doctl CLI

```bash
# Install doctl
brew install doctl  # macOS
# or snap install doctl  # Linux

# Authenticate
doctl auth init

# Create app from spec
doctl apps create --spec .do/app.yaml
```

### Option 2: Using Digital Ocean Console

1. Go to [Digital Ocean Apps](https://cloud.digitalocean.com/apps)
2. Click "Create App"
3. Connect your GitHub repository
4. Select the `arbbot-analytics-api` repository
5. Digital Ocean will auto-detect Node.js
6. Set environment variables:
   - `DB_HOST`
   - `DB_PORT` = 25060
   - `DB_NAME` = bot_activity
   - `DB_USER` = bot
   - `DB_PASSWORD` (mark as secret)
   - `DB_SSL` = true
7. Configure:
   - Build Command: `npm install && npm run build`
   - Run Command: `npm start`
   - HTTP Port: 8080
8. Deploy!

### Option 3: Using App Spec

Upload `.do/app.yaml` via the console or doctl.

## Database Schema

The API expects two tables:

### `price_snapshot`
```sql
CREATE TABLE price_snapshot (
  snapshot_id INTEGER,
  token VARCHAR(42),
  pool_id VARCHAR(66),
  pool_type VARCHAR(50),
  price NUMERIC,
  ref_token VARCHAR(42),
  swap_amount NUMERIC,
  timestamp TIMESTAMP
);
```

### `liquidity_observations`
```sql
CREATE TABLE liquidity_observations (
  id SERIAL PRIMARY KEY,
  timestamp TIMESTAMP NOT NULL,
  source_avatar VARCHAR(42) NOT NULL,
  target_avatar VARCHAR(42) NOT NULL,
  measured_liquidity NUMERIC NOT NULL,
  required_amount NUMERIC NOT NULL,
  edge_id VARCHAR(255),
  edge_score NUMERIC,
  success BOOLEAN NOT NULL,
  source_token_price NUMERIC,
  target_token_price NUMERIC,
  ref_token VARCHAR(42),
  failure_reason TEXT,
  execution_time_ms INTEGER
);
```

## Monitoring

- Health endpoint: `/health`
- Digital Ocean provides automatic monitoring
- Logs available in Digital Ocean console

## Architecture

```
┌─────────────────┐
│   Frontend      │
│  (HTML/React)   │
└────────┬────────┘
         │ HTTPS
         ▼
┌─────────────────┐
│   Digital Ocean │
│   App Platform  │
│  (This API)     │
└────────┬────────┘
         │ SSL
         ▼
┌─────────────────┐
│   PostgreSQL    │
│ (DO Managed DB) │
└─────────────────┘
```

## Performance

- Response time: < 100ms (typical)
- Concurrent requests: Handles 100+ req/s
- Caching: Add Redis for improved performance
- Rate limiting: Consider adding for production

## Security

- ✅ CORS enabled (configurable)
- ✅ SSL/TLS for database connections
- ✅ Environment variables for secrets
- ✅ Input validation
- ✅ SQL injection prevention (parameterized queries)

## License

MIT

## Support

For issues, please contact the Circles arbbot team.
