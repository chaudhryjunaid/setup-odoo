FROM ubuntu:20.04

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Retrieve the target architecture to install the correct wkhtmltopdf package
ARG TARGETARCH

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git \
    python3-pip \
    build-essential \
    wget \
    python3-dev \
    python3-venv \
    python3-wheel \
    libxslt-dev \
    libzip-dev \
    libldap2-dev \
    libsasl2-dev \
    python3-setuptools \
    node-less \
    libjpeg-dev \
    gdebi \
    libpq-dev

RUN if [ -z "${TARGETARCH}" ]; then \
        TARGETARCH="$(dpkg --print-architecture)"; \
    fi; \
    wget -O wkhtmltox.deb "https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.bionic_${TARGETARCH}.deb" && \
    apt-get install -y --no-install-recommends ./wkhtmltox.deb && \
    rm -rf /var/lib/apt/lists/* wkhtmltox.deb

WORKDIR /opt/odoo
RUN git clone --depth 1 --branch 13.0 --single-branch https://github.com/odoo/odoo.git odoo

RUN sed -i "s/gevent==1.5.0 ; python_version >= '3.7'/gevent==21.12.0 ; python_version >= '3.7'/" odoo/requirements.txt
RUN sed -i "s/greenlet==0.4.15 ; python_version >= '3.7'/greenlet==1.1.3 ; python_version >= '3.7'/" odoo/requirements.txt
RUN pip3 install -r odoo/requirements.txt

COPY ./wait-for-psql.py /usr/local/bin/wait-for-psql.py
COPY ./entrypoint.sh /opt/odoo/entrypoint.sh
COPY ./odoo.conf /etc/odoo/odoo.conf

RUN groupadd -r odoo && useradd -r -g odoo odoo

RUN chown odoo /etc/odoo/odoo.conf \
    && mkdir -p /mnt/extra-addons \
    && chown -R odoo /opt/odoo \
    && chown -R odoo /mnt/extra-addons \
    && chmod +x /opt/odoo/entrypoint.sh \
    && chmod +x /usr/local/bin/wait-for-psql.py \
    && ln -s /opt/odoo/odoo/odoo-bin odoo-bin \
    && chown odoo odoo-bin \
    && mkdir -p /var/lib/odoo \
    && chown -R odoo /var/lib/odoo

USER odoo

VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

EXPOSE 8069 8071 8072

ENV ODOO_RC /etc/odoo/odoo.conf

ENTRYPOINT ["/opt/odoo/entrypoint.sh"]
CMD ["./odoo-bin"]
