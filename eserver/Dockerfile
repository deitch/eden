FROM golang:1.15.7-alpine3.13 AS build

ENV CGO_ENABLED=0
ENV GO111MODULE=on

RUN apk --no-cache add git=2.30.2-r0 openssh-keygen=8.4_p1-r2

RUN ssh-keygen -t rsa -q -P "" -f /root/.ssh/id_rsa

RUN mkdir -p /eserver/src && mkdir -p /eserver/bin
WORKDIR /eserver/src
COPY go.mod .
COPY go.sum .
RUN go mod download

COPY . /eserver/src

ARG GOOS=linux
ARG GOARCH=amd64

RUN go build -ldflags "-s -w" -o /eserver/bin/eserver main.go

FROM alpine:3.13

COPY --from=build /eserver/bin/eserver /bin/
COPY --from=build /root/.ssh/ /root/.ssh/
WORKDIR /eserver
ENTRYPOINT ["/bin/eserver"]
