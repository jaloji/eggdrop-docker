FROM alpine:3.20
LABEL org.opencontainers.image.authors="Geo Van O <geo@eggheads.org>" \
      org.opencontainers.image.url="https://www.eggheads.org"

RUN addgroup -S -g 3333 eggdrop \
    && adduser -S -u 3333 eggdrop eggdrop

# grab su-exec for easy step-down from root
RUN apk add --no-cache 'su-exec>=0.2'

RUN apk add --no-cache tzdata tcl bash openssl tcl-dev wget ca-certificates make tar gnupg build-base openssl-dev \
  libcurl curl curl-dev mariadb-dev tor torsocks \
  && wget "https://prdownloads.sourceforge.net/tcl/tcl8.6.13-src.tar.gz" -O tcl8.6.13-src.tar.gz --progress=dot:giga \
  && tar -zxf tcl8.6.13-src.tar.gz \
  && ( cd tcl8.6.13 \
# Fix Tcl UTF Emoji support
    && sed -i "/define TCL_UTF_MAX/c\#define TCL_UTF_MAX 6" generic/tcl.h \
    && cd unix \
    && ./configure \
    && nproc="$(nproc)" \
    && make -j"$nproc" \
    && make install ) \
  && rm -rf tcl8.6.13-src.tar.gz \
  && rm -rf tcl8.6.13 \
  && wget ftp://ftp.eggheads.org/pub/eggdrop/source/1.10/eggdrop-1.10.0.tar.gz \
  && wget ftp://ftp.eggheads.org/pub/eggdrop/source/1.10/eggdrop-1.10.0.tar.gz.asc \
  && gpg --keyserver keyserver.ubuntu.com --recv-key E01C240484DE7DBE190FE141E7667DE1D1A39AFF \
  && gpg --batch --verify eggdrop-1.10.0.tar.gz.asc eggdrop-1.10.0.tar.gz \
  && command -v gpgconf > /dev/null \
  && gpgconf --kill all \
  && rm eggdrop-1.10.0.tar.gz.asc \
  && tar -zxvf eggdrop-1.10.0.tar.gz \
  && rm eggdrop-1.10.0.tar.gz \
  && ( cd eggdrop-1.10.0 \
    && ./configure \
    && make config \
    && nproc="$(nproc)" \
    && make -j"$nproc" \
    && make install DEST=/home/eggdrop/eggdrop ) \
  && rm -rf eggdrop-1.10 \
  && mkdir /home/eggdrop/eggdrop/data \
  && chown -R eggdrop /home/eggdrop/eggdrop

RUN wget https://github.com/flightaware/tclcurl-fa/archive/refs/heads/master.zip \
  && unzip master.zip \
  && rm master.zip \
  && ( cd tclcurl-fa-master \
  && ./configure \
  && make \
  && make install ) \
  && rm -rf tclcurl-fa-master \
  && cp -r /usr/local/lib/TclCurl7.22.0 /usr/lib/TclCurl7.22.0
  
RUN wget http://www.xdobry.de/mysqltcl/mysqltcl-3.052.tar.gz \
  && tar -zxvf mysqltcl-3.052.tar.gz \
  && rm mysqltcl-3.052.tar.gz \
  && ( cd mysqltcl-3.052 \
  && ./configure \
  && make \
  && make install ) \
  && rm -rf mysqltcl-3.052 \
  && cp -r /usr/local/lib/mysqltcl-3.052 /usr/lib/mysqltcl-3.052
  
RUN wget https://core.tcl-lang.org/tcllib/uv/tcllib-2.0.tar.gz \
  && tar -zxvf tcllib-2.0.tar.gz \
  && rm tcllib-2.0.tar.gz \
  && ( cd tcllib-2.0 \
  && ./configure \
  && make install ) \
  && rm -rf tcllib-2.0 \
  && cp -r /usr/local/lib/tcllib2.0/ /usr/lib/tcllib2.0/

ENV NICK=""
ENV SERVER=""
ENV LISTEN="3333"
ENV USERFILE="eggdrop.user"
ENV CHANFILE="eggdrop.chan"
ENV TZ=""

WORKDIR /home/eggdrop/eggdrop
EXPOSE 3333
COPY entrypoint.sh ./
COPY docker.tcl ./scripts/

ENTRYPOINT ["/home/eggdrop/eggdrop/entrypoint.sh"]
CMD ["eggdrop.conf"]
