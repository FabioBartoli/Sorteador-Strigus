FROM cgr.dev/chainguard/python:latest-dev AS python-builder
WORKDIR /app
COPY requirements.txt .
RUN python -m pip install --no-cache-dir --user -r requirements.txt

FROM cgr.dev/chainguard/redis:latest AS redis-builder

FROM cgr.dev/chainguard/go:latest AS go-builder
WORKDIR /src
COPY ./go-bin-exec/main.go .
RUN go mod init go-bin-exec && CGO_ENABLED=0 go build -o /out/go-bin-exec .

FROM cgr.dev/chainguard/python:latest
WORKDIR /opt/app
ENV REDIS_HOST=localhost
ENV PATH="/home/nonroot/.local/bin:${PATH}"

COPY app.py .
COPY static/ static/
COPY templates/ templates/

COPY --from=python-builder /home/nonroot/.local /home/nonroot/.local
COPY --from=redis-builder /usr/bin/redis-server /usr/bin/redis-server
COPY --from=go-builder /out/go-bin-exec ./go-bin-exec

EXPOSE 5000
ENTRYPOINT ["./go-bin-exec"]
