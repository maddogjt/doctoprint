# Intermediate build container.
FROM alpine:3 as build_deps

ARG TARGETPLATFORM
ARG VERSION

RUN apk --no-cache add build-base git
RUN apk --no-cache add cmake
RUN apk --no-cache add libjpeg-turbo-dev
RUN apk --no-cache add linux-headers
RUN [[ "${TARGETPLATFORM:6}" != "arm64" ]] && apk --no-cache add raspberrypi-dev || true

FROM build_deps as build

# Download packages
RUN git clone --depth=1 https://github.com/jacksonliam/mjpg-streamer.git

# Install mjpg-streamer
WORKDIR /mjpg-streamer/mjpg-streamer-experimental
RUN make
RUN make install

# Build final image
FROM alpine:3

COPY --from=build /usr/local/bin /usr/local/bin
COPY --from=build /usr/local/lib /usr/local/lib

RUN apk --no-cache add libjpeg

COPY start-mjpg-streamer /usr/local/bin/start-mjpg-streamer

ENV CAMERA_DEV /dev/video0
ENV MJPEG_STREAMER_INPUT input_uvc.so -y -n -r 640x480 -d $CAMERA_DEV
ENV MJPEG_STREAMER_OUTPUT output_http.so -w /usr/local/share/mjpg-streamer/www -p 8080

EXPOSE 8080

#CMD ["start-mjpg-streamer"]
ENV LD_LIBRARY_PATH /usr/local/lib/mjpg-streamer
CMD ["mjpg_streamer", "-i", "\"$MJPEG_STREAMER_INPUT\""]

# exec mjpg_streamer \
#   -i "$MJPEG_STREAMER_INPUT -d $CAMERA_DEV" \
#   -o "output_http.so -w /usr/local/share/mjpg-streamer/www -p 8080"

