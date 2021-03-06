################################################################################
# Dockerfile for a Customer James Server
#
# Use this as a sandbox to build your own customer James Server
#
################################################################################

# some global args

ARG install_prefix="/opt/apache-james"

# Stage 1 - unpack james
FROM adoptopenjdk:11-jre-hotspot as unpack

ARG install_prefix

COPY james-cli/build/distributions/james-cli.tar /tmp/james-cli.tar
COPY james-custom-server/build/distributions/james-custom-server.tar /tmp/james-server.tar

RUN mkdir -p "${install_prefix}" \
    && tar -xvf /tmp/james-cli.tar --directory "${install_prefix}" \
    && tar -xvf /tmp/james-server.tar --directory "${install_prefix}" \
    && apt-get update && apt-get install -y tree && tree "${install_prefix}"

# Stage 2 Apache James Server and CLI
FROM adoptopenjdk:11-jre-hotspot as james_server_and_cli

ARG install_prefix

# Ports:
#
#   25   SMTP without authentication
#   110  POP3
#   143  IMAP with startTLS enabled
#   465  SMTP with authentication and socketTLS enabled
#   587  SMTP with authentication and startTLS enabled
#   993  IMAP with socketTLS enabled
#   8000 Web Admin

EXPOSE 25 110 143 465 587 993 8000

COPY --from=unpack $install_prefix ${install_prefix}

# We are going to mount the configuraton as a volume
# COPY conf ${install_prefix}/james-custom-server/conf

WORKDIR ${install_prefix}/james-custom-server

# Add start scripts to path
ENV PATH="${PATH}:${install_prefix}/james-custom-server/bin:${install_prefix}/james-cli/bin"

ENTRYPOINT james-custom-server
