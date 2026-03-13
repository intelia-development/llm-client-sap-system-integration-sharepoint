FROM golang:1.24-bookworm AS build

RUN apt-get update \
  && apt-get install -y --no-install-recommends git ca-certificates \
  && rm -rf /var/lib/apt/lists/*

ARG REPO=https://github.com/SaurabhVC/ABAPDocMCP.git
ARG REF=v1.0.0

WORKDIR /src
RUN git clone --depth 1 --branch ${REF} ${REPO} abapdocmcp

WORKDIR /src/abapdocmcp
RUN go build -o /out/abapdocmcp ./cmd/vsp/main.go

FROM node:22-bookworm-slim

RUN apt-get update \
  && apt-get install -y --no-install-recommends iptables ca-certificates \
  && rm -rf /var/lib/apt/lists/* \
  && npm install -g mcp-proxy@6.2.0

COPY --from=build /out/abapdocmcp /usr/local/bin/abapdocmcp
RUN chmod +x /usr/local/bin/abapdocmcp

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENV MCP_PROXY_PORT=3000

EXPOSE 3000

ENTRYPOINT ["/entrypoint.sh"]
