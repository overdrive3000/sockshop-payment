FROM golang:1.16-buster AS build

WORKDIR /app
COPY go.mod ./
COPY go.sum ./
RUN go env -w GOPROXY=direct && go mod download
COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /payment cmd/paymentsvc/main.go
RUN chmod +x /payment && chown 65534:65534 /payment && \
        setcap CAP_NET_BIND_SERVICE=+eip /payment

FROM debian:11

WORKDIR /
COPY --from=build /payment /payment

USER 65534

CMD ["/payment", "-port=8080"]

EXPOSE 8080