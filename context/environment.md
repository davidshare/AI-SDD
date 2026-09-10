# Environment
<!-- Owner: DevOps Engineer. SECURITY RULE: this file lists variable NAMES and PURPOSES only.
     Real secret values live in `.env` (gitignored) which NO agent ever reads or is given in
     context — this fixes the v1.0.0 gap where secrets sat directly in agent-readable context. -->

## Ports
- Backend API: [port]
- Frontend: [port]
- Database: [port]

## Environment Variables

### Backend
| Name | Purpose | Required |
|---|---|---|
| DATABASE_URL | Postgres connection string | yes |
| JWT_SECRET | Signs auth tokens — generate with `openssl rand -hex 32`, never reuse across envs | yes |

### Frontend
| Name | Purpose | Required |
|---|---|---|
| VITE_API_URL | Backend base URL for this environment | yes |

## Dependencies
- [Runtime] [version+]
- [Database engine] [version+]
- Docker [version+]

## Setup
```
cp .env.example .env   # fill in real values locally — never commit .env, never paste its
                        # contents into an agent prompt
```
