FROM ubuntu:latest

RUN apt-get update && apt-get install -y iproute2 iputils-ping curl isc-dhcp-client && apt-get clean

CMD sh -c "ip route del default && \
            ip route add default via 192.168.100.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            dhclient eth0 && \
            while true; do sleep 3600; done"