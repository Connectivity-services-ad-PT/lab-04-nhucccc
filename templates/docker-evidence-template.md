# Docker Evidence – Lab 04

## Team

- Team name: team-core (A6 — Core Business)
- Service: IoT Ingestion (Dockerized for Lab 04)
- Image tag: `fit4110/iot-ingestion:lab04` / `fit4110/iot-ingestion:v0.1.0-team-core`
- Registry: `ghcr.io/connectivity-services-ad-pt/lab-04-nhucccc:v0.1.0-team-core`

## 1. Build evidence

Command:

```bash
docker build -t fit4110/iot-ingestion:lab04 .
```

Result (from reports/docker-build.log):
- Base image: python:3.11-slim (multi-stage)
- Builder stage: installs dependencies into /opt/venv
- Runtime stage: copies venv, sets non-root user appuser
- Build successful: image ID `8dc03be4d9f5`, size 268MB

## 2. Run evidence

Command:

```bash
docker run -d --rm --name fit4110-iot-lab04 -p 8000:8000 --env-file .env.example fit4110/iot-ingestion:lab04
```

Result:
```
Container: fit4110-iot-lab04
Status: Up (healthy)
Ports: 0.0.0.0:8000->8000/tcp
User: appuser (non-root)
```

## 3. Healthcheck evidence

Command:

```bash
curl http://localhost:8000/health
```

Result:

```json
{"status":"ok","service":"iot-ingestion","version":"0.4.0"}
```

HTTP Status: 200 OK

## 4. Newman evidence

Command:

```bash
npm run test:local
```

Result:
- 11 requests | 19 assertions | **0 failures**
- Functional: 4/4 pass (GET /health, POST /readings, GET /readings/latest, GET /readings/{id})
- Auth: 2/2 pass (missing token → 401 ProblemDetails, wrong token → 401 ProblemDetails)
- Negative: 2/2 pass (missing field → 422, wrong type → 422)
- Boundary: 3/3 pass (value=80 → 201 + X-Warning, value=81 → 422, response time < 1000ms)

Report path:

```text
reports/newman-lab04-local.html
reports/newman-lab04-local.xml
```

## 5. Image tag evidence

```bash
# Local tags
fit4110/iot-ingestion:lab04
fit4110/iot-ingestion:v0.1.0-team-core

# Registry push
ghcr.io/connectivity-services-ad-pt/lab-04-nhucccc:v0.1.0-team-core
```

## 6. Notes

- Auth test chạy trên service thật (không dùng Prism Prefer header) — thiếu token → 401 thật
- Boundary X-Warning header được set khi temperature >= 70 (ngưỡng cảnh báo sớm trong 80-range)
- Container dùng non-root user `appuser` để đảm bảo security best practice
- Next step for Lab 05: Docker Compose kết nối multi-service (IoT + Core + AI Vision)
