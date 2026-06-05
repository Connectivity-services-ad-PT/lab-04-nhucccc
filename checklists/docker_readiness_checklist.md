# Docker Readiness Checklist — Lab 04
# Team: team-core (A6 — Core Business)
# Service: IoT Ingestion (adapted for Lab 04 Docker packaging)
# Date: 2026-06-05

## Dockerfile

- [x] Có base image hợp lý — python:3.11-slim multi-stage
- [x] Có `WORKDIR` — /app
- [x] Có copy dependency trước source để tận dụng cache — COPY requirements.txt trước COPY src/
- [x] Có `EXPOSE` — EXPOSE 8000
- [x] Có `CMD` — uvicorn iot_app.main:app
- [x] Có `HEALTHCHECK` — GET /health timeout 3s interval 30s
- [x] Có user non-root — appuser trong appgroup
- [x] Không chứa secret thật — AUTH_TOKEN dùng placeholder, .env.example không commit secret

## Runtime

- [x] Container chạy được — docker run pass
- [x] Port map đúng — -p 8000:8000
- [x] `/health` trả `200` — {"status":"ok","service":"iot-ingestion","version":"0.4.0"}
- [x] Log khởi động rõ ràng — uvicorn log hiển thị Uvicorn running on http://0.0.0.0:8000
- [x] Cấu hình qua ENV — APP_HOST, APP_PORT, AUTH_TOKEN, SERVICE_NAME, SERVICE_VERSION

## Testing

- [x] Chạy lại Postman Collection từ Lab 03 — Newman pass trên local/container
- [x] Newman report sinh ra trong `reports/` — newman-lab04-local.xml + html
- [x] Functional test pass — GET /health, POST /readings, GET /readings/latest, GET /readings/{id}
- [x] Auth test pass trên local/container — thiếu token → 401, sai token → 401 với ProblemDetails
- [x] Negative test pass trên local/container — thiếu device_id → 422, value sai kiểu → 422
- [x] Boundary test pass — value=80 → 201 + X-Warning header, value=81 → 422 ProblemDetails

## Evidence

- [x] Có log `docker build` — reports/docker-build.log
- [x] Có log `docker run` + `curl /health` — reports/docker-evidence.txt
- [x] Có Newman HTML/XML report — reports/newman-lab04-local.xml + html
- [x] Có tag image — fit4110/iot-ingestion:lab04 và v0.1.0-team-core
