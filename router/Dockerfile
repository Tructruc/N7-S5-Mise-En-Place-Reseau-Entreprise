# Use a lightweight base image with networking tools
FROM ubuntu:latest

# Install necessary networking tools
RUN apt-get update && apt-get install -y iproute2 iptables iputils-ping && apt-get clean

# Copy the persistent iptables rules file into the container
COPY iptables.rules /etc/iptables.rules

#RUN iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -d 192.168.200.0/24 -j MASQUERADE
#RUN iptables -t nat -A POSTROUTING -s 192.168.200.0/24 -d 192.168.100.0/24 -j MASQUERADE

#iptables-restore < /etc/iptables.rules
# Ensure the rules are loaded at startup
CMD sh -c "\
    iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -d 192.168.200.0/24 -j MASQUERADE && \
    iptables -t nat -A POSTROUTING -s 192.168.200.0/24 -d 192.168.100.0/24 -j MASQUERADE && \
    while true; do sleep 3600; done"
