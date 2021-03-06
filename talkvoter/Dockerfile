FROM python:3.6
LABEL maintainer=""

# install OS dependencies
RUN apt-get update && apt-get install -y libpq-dev && rm -rf /var/lib/apt/lists/*
RUN pip install --upgrade pip

ENV DATABASE_URL=postgres://postgres@db/postgres

# set an environment variable for a base directory for everything to live under
ENV SITE_DIR=/app/
ENV PYTHONPATH=${SITE_DIR}proj/talkvoter/:${PYTHONPATH}
ENV PATH="/app/.local/bin/:${PATH}"
ENV FLASK_APP="talkvoter.app"

# add application user and group
RUN groupadd -r app && \
    useradd -r -g app -d ${SITE_DIR} -s /sbin/nologin -c "Docker image user" app

# create that base directory with proper permissions
RUN install -g app -o app -d ${SITE_DIR}

# set the default working directory to the app dir
WORKDIR ${SITE_DIR}

# Create a few more directories for use by the app and set permissions
RUN install -g app -o app -d proj/ htdocs/ htdocs/static/
RUN find ${SITE_DIR} -type d -exec chmod g+s {} \;
RUN chmod -R g+w ${SITE_DIR}

# upgrade pip
RUN pip install pip==10.0.0

# from here on, run everything as the "app" user
USER app

RUN pip install --user uwsgi

COPY talkvoter/requirements.txt requirements.txt
RUN pip install --no-cache-dir --user -r requirements.txt

COPY docker-utils/ docker-utils/

COPY . proj/

WORKDIR ${SITE_DIR}proj/

EXPOSE 8000
CMD [".\docker-utils\run-app.sh"]
ENTRYPOINT [".\docker-utils\entrypoint-app.sh"]
