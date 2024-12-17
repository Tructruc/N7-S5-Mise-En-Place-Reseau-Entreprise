FROM alpine:latest

# Install all needed packages
RUN apk add --no-cache \
        iproute2 \
        w3m \
        w3m-image \
        bash 

# The command that is executed when the container starts
# Delete the Docker default route
# Add the new default route pointing to the router
# Delete the address on eth0
# Get a new address on eth0 via DHCP
# Make the container stay up
CMD sh -c "ip route del default && \
            ip route add default via 192.168.200.250 dev eth0 && \
            ip addr flush dev eth0 && \
            udhcpc eth0 -s /udhcpc.script && \
            tail -f /dev/null"
