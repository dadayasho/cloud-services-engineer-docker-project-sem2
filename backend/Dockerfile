FROM golang:1.22 AS builder
WORKDIR /backend
COPY . /backend
RUN go mod download && CGO_ENABLED=0 GOOS=linux go build -o main ./cmd/api/
RUN ls -la .

FROM alpine:3.20 AS runner
WORKDIR /app
COPY --from=builder /backend/main /app/main
EXPOSE 8081
RUN adduser -D -u 1000 -g "app" app
USER app
CMD ["/app/main"]