FROM python:2.7-alpine as build
#LABEL maintainer "gaetancollaud@gmail.com"

#ENV CURA_VERSION=15.04.6
ARG tag=1.3.12

WORKDIR /opt/octoprint

# In case of alpine
RUN apk add --no-cache git bash gcc openssh linux-headers build-base

#Install Octoprint
RUN git clone --depth=1 --branch $tag https://github.com/foosel/OctoPrint.git /opt/octoprint
RUN pip install -r requirements.txt
RUN python setup.py install
WORKDIR /opt

RUN mv /opt/octoprint /opt/octoprint_backup

FROM python:2.7-alpine

RUN apk add --no-cache bash ffmpeg

COPY --from=build /usr/local/bin /usr/local/bin
COPY --from=build /usr/local/lib /usr/local/lib

#Create an octoprint user
RUN adduser -D octoprint && adduser octoprint dialout
#RUN mkdir /data && chown octoprint:octoprint /data
USER octoprint
#RUN ln -s /data /home/octoprint/.octoprint
#VOLUME /data
#WORKDIR /data

CMD ["octoprint", "serve"]
