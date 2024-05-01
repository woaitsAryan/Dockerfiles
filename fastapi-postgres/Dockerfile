FROM python:3.11-slim AS builder

RUN apt-get update && apt-get install -y libpq-dev gcc

WORKDIR /app

COPY requirements.txt /app

RUN pip3 install --no-cache-dir -r requirements.txt

FROM python:3.11-slim AS runner

WORKDIR /app

RUN apt-get update && apt-get install -y libpq-dev gcc wget

COPY --from=builder /usr/local/lib /usr/local/lib
COPY . /app

ENTRYPOINT ["python3"]
CMD ["main.py"]