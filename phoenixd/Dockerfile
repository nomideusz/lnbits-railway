# Wrapper around the official phoenixd image for Railway:
# - Railway mounts volumes root-owned; the stock image runs as uid 1000 and
#   crashes with "Permission denied" writing seed.dat/phoenix.conf. Run as root.
# - PHOENIX_DATADIR pins the data dir (root's HOME would move it off the volume).
# - CMD goes through bash so $PHOENIXD_API_PASSWORD (a Railway variable) expands;
#   --http-bind-ip :: is required for Railway's IPv6-only private network.
FROM acinq/phoenixd:latest
USER root
ENV PHOENIX_DATADIR=/phoenix/.phoenix
ENTRYPOINT []
CMD ["/bin/bash", "-c", "exec /phoenix/phoenixd --agree-to-terms-of-service --http-bind-ip '::' --http-password \"$PHOENIXD_API_PASSWORD\""]
