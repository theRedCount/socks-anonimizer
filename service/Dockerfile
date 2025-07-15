FROM debian:stable-slim
ARG PROXY_PASSWORD

# Install dependencies (including PAM dev libraries)
RUN apt-get update && \
    apt-get install -y \
      iptables \
      iputils-ping \
      iproute2 \
      openvpn \
      libpam0g \
      libpam0g-dev \
      build-essential \
      wget \
      curl \
      net-tools && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Create non-privileged user for Dante
RUN useradd -m -s /usr/sbin/nologin proxyuser && \
    echo "proxyuser:${PROXY_PASSWORD}" | chpasswd

# Download, compile and install Dante with PAM support
WORKDIR /opt
RUN wget https://www.inet.no/dante/files/dante-1.4.4.tar.gz && \
    tar zxvf dante-1.4.4.tar.gz && \
    cd dante-1.4.4 && \
    ./configure --with-pam && \
    make && \
    make install && \
    cd .. && rm -rf dante-1.4.4*

# Write PAM config for Dante authentication
RUN printf "auth required pam_unix.so nullok\naccount required pam_unix.so\n" \
    > /etc/pam.d/sockd

# Copy OpenVPN configs and Dante configuration
COPY configs/*.ovpn /etc/openvpn/
COPY sockd.conf      /etc/sockd.conf
COPY startup.sh      /usr/local/bin/startup.sh

RUN chmod +x /usr/local/bin/startup.sh

EXPOSE 1080/tcp

CMD ["/usr/local/bin/startup.sh"]
