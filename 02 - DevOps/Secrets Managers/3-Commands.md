## Commands

### Development Environment Commands
```
  set -a; source .env.development; set +a
  doppler run -- docker compose -f docker-compose.development.yml up -d
```

### Production Environment Commands
```
  set -a; source .env.production; set +a
  doppler run -- docker compose -f docker-compose.production.yml up -d
```