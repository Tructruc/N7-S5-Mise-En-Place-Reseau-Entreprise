FROM ubuntu:latest

RUN apt-get update && apt-get install -y iproute2 iputils-ping curl wget isc-dhcp-client && apt-get clean

RUN wget https://github.com/static-web-server/static-web-server/releases/download/v2.33.1/static-web-server-v2.33.1-x86_64-unknown-linux-gnu.tar.gz
RUN tar -xvzf static-web-server-v2.33.1-x86_64-unknown-linux-gnu.tar.gz
RUN cp static-web-server-v2.33.1-x86_64-unknown-linux-gnu/static-web-server /usr/bin

#RUN ip route del default
#RUN ip route add default via 192.168.100.1 dev eth0

CMD sh -c "ip route del default && \
            ip route add default via 192.168.100.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            dhclient eth0 && \
            static-web-server --root /public"