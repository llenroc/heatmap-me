FROM apendergast/hm-docker-base:backend-latest as backend
FROM apendergast/hm-docker-base:frontend-latest as frontend
COPY --from=backend /root/dummy /root/dummy
WORKDIR /root/
ARG GIT_BRANCH=master
ARG TIMESTAMP=0
# build backend
RUN echo "$TIMESTAMP" && \
    git clone https://github.com/aloysius-pgast/heatmap-me.git && cd heatmap-me && \
    git checkout $GIT_BRANCH && \
    npm install --unsafe-perm && \
    cp docker/config.docker.json config/config.json
# build frontend
RUN cd heatmap-me/ui && \
    cp -R /root/dummy/frontend/node_modules . && npm install --unsafe-perm && npm run build && rm -fr node_modules

FROM apendergast/alpine-node:runtime-latest
WORKDIR /root/heatmap-me/
COPY --from=frontend /root/heatmap-me .

VOLUME ["/root/heatmap-me/ssl","/root/heatmap-me/custom_config"]

ENV cfg.logLevel "info"

ENV cfg.gateway.restEndpoint "http://172.17.0.1:8000"
ENV cfg.gateway.wsEndpoint "ws://172.17.0.1:8001"
ENV cfg.gateway.sessionId "mystream.heatmap-me"
ENV cfg.gateway.apiKey ""

ENV cfg.computeInterval "60"
ENV cfg.dataPeriods "xm,15m,1h,4h,1d,5d"

EXPOSE 8002
EXPOSE 8003
CMD ["node", "./index.js"]
