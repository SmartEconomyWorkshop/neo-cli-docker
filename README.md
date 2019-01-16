# neo-cli-docker
Using neo-cli for a private network

```
# Update the Git submodules
git submodule update --remote

# Build the Docker container
docker build -t smarteconomyworkshop/neo-cli:2.9.4 .
docker tag smarteconomyworkshop/neo-cli:2.9.4 smarteconomyworkshop/neo-cli:latest

# Copy the local configuration files to a persistent location
mkdir -p /usr/local/etc/neo
cp -r local/[1234] /usr/local/etc/neo/

# Create a Docker network bridge
docker network create -d bridge neo

# Create 4 NEO consensus nodes for our private network
docker create -it --name=neo-node-1 -v /usr/local/etc/neo/1/config.json:/app/config.json -v /usr/local/etc/neo/1/protocol.json:/app/protocol.json -v /usr/local/etc/neo/1/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-2 -v /usr/local/etc/neo/2/config.json:/app/config.json -v /usr/local/etc/neo/2/protocol.json:/app/protocol.json -v /usr/local/etc/neo/2/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-3 -v /usr/local/etc/neo/3/config.json:/app/config.json -v /usr/local/etc/neo/3/protocol.json:/app/protocol.json -v /usr/local/etc/neo/3/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-4 -v /usr/local/etc/neo/4/config.json:/app/config.json -v /usr/local/etc/neo/4/protocol.json:/app/protocol.json -v /usr/local/etc/neo/4/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli

# Start the nodes
for node in neo-node-1 neo-node-2 neo-node-3 neo-node-4; do docker start ${node}; done

# Create a regular neo-cli container that is not a consensus node and expose the RPC and P2P ports
docker run -it --name=neo-cli --network=neo -p 10332-10333:10332-10333 --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
```
