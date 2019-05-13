FROM golang:1.12.5

ENV ALLOWED_ORIGIN ${ALLOWED_ORIGIN}
ENV REDIS_ADDR ${REDIS_ADDR}
ENV ENVIRONMENT ${ENVIRONMENT}

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

COPY . .

RUN go get ./...
RUN go build .

ENTRYPOINT ["./backend"]