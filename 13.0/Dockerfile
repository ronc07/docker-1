FROM debian:buster-slim
MAINTAINER Odoo S.A. <info@odoo.com>

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            apt-utils \
            ca-certificates \
            curl \
            git-core \
            build-essential \
            openssl \
            libssl-dev \
            dirmngr \
            fonts-noto-cjk \
            gnupg \
            libssl-dev \
            nodejs \
#            node-less \
#            npm \
            python3-num2words \
            python3-pip \
            python3-phonenumbers \
            python3-pyldap \
            python3-qrcode \
            python3-renderpm \
            python3-setuptools \
            python3-slugify \
            python3-vobject \
            python3-watchdog \
            python3-xlrd \
            python3-xlwt \
            python3-babel \
            python3-decorator \
            python3-docutils \
            python3-feedparser \
            python3-gevent \
            python3-html2text \
            python3-jinja2 \
            python3-libsass \
            python3-lxml \
            python3-mako \
            python3-mock \
            python3-ofxparse \
            python3-passlib \
            python3-polib \
            python3-psutil \
            python3-psycopg2 \
            python3-pydot \
            python3-pyparsing \
            python3-pypdf2 \
            python3-reportlab \
            python3-requests \
            python3-serial \
            python3-tz \
            python3-usb \
            python3-vatnumber \
            python3-werkzeug \
            python3-xlsxwriter \
            python3-zeep \
            python3-chardet \
            python3-suds \
            xz-utils \
            wkhtmltopdf \
        && rm -rf /var/lib/apt/lists/*

# install latest postgresql-client
RUN set -x; \
#        echo 'deb http://apt.postgresql.org/pub/repos/apt/ buster-pgdg main' > /etc/apt/sources.list.d/pgdg.list \
#        && export GNUPGHOME="$(mktemp -d)" \
#        && repokey='B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8' \
#        && gpg --batch --keyserver keyserver.ubuntu.com --recv-eys "${repokey}" \
#        && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/pgdg.gpg.asc \
#        && gpgconf --kill all \
#        && rm -rf "$GNUPGHOME" \
#        && apt-get update  \
#        && apt-get install -y postgresql-client \
         apt-get update && apt-get install -y postgresql-client \
         && rm -rf /var/lib/apt/lists/*

# Install rtlcss (on Debian buster)
RUN set -x; \
#    npm install -g npm@latest \
    curl -L https://npmjs.org/install.sh | sh \
    && npm install -g rtlcss

# Install Odoo
ENV ODOO_VERSION 13.0
ARG ODOO_RELEASE=20200229
ARG ODOO_SHA=9f98ff7e1239cbfbe0ff5d0d8c6d181edad44905
RUN set -x; \
        curl -o odoo.deb -sSL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo "${ODOO_SHA} odoo.deb" | sha1sum -c - \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update && apt-get install -y -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Copy entrypoint script and Odoo configuration file
RUN pip3 install unicodecsv unicode pysftp==0.2.8
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/

# Set permissions and Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN chown odoo /etc/odoo/odoo.conf \
    && mkdir -p /mnt/extra-addons \
    && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
