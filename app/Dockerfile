FROM python:3.12-slim AS builder

WORKDIR /build

COPY requirements.txt .

RUN pip install --no-cache-dir --prefix=/build/install -r requirements.txt


FROM python:3.12-slim

WORKDIR /app

COPY --from=builder /build/install /usr/local

COPY app.py .

ENV FLASK_ENV=production
ENV REDIS_HOST=redis
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=5s \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:5000/health')"

# create non-root user (works in slim Debian image)
RUN useradd -m appuser

USER appuser

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "2", "app:app"]
