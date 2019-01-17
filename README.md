# neo-cli-docker
Using neo-cli for a private network

```
# Update the Git submodules
git submodule update --remote

# Build the Docker container
docker build -t smarteconomyworkshop/neo-cli:2.9.4 .
docker tag smarteconomyworkshop/neo-cli:2.9.4 smarteconomyworkshop/neo-cli:latest

# Choose a local directory for persistent data
location=/usr/local/etc/neo

# Copy the local configuration files to a persistent location
mkdir -p ${location}
mkdir -p local/1/Index local/1/Chain local/2/Index local/2/Chain local/3/Index local/3/Chain local/4/Index local/4/Chain
cp -r local/[1234] ${location}/

# Create a Docker network bridge
docker network create -d bridge neo

# Create 4 NEO consensus nodes for our private network
docker create -it --name=neo-node-1 -v ${location}/1/Chain:/app/Chain -v ${location}/1/Index:/app/Index -v ${location}/1/config.json:/app/config.json -v ${location}/1/protocol.json:/app/protocol.json -v ${location}/1/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-2 -v ${location}/2/Chain:/app/Chain -v ${location}/2/Index:/app/Index -v ${location}/2/config.json:/app/config.json -v ${location}/2/protocol.json:/app/protocol.json -v ${location}/2/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-3 -v ${location}/3/Chain:/app/Chain -v ${location}/3/Index:/app/Index -v ${location}/3/config.json:/app/config.json -v ${location}/3/protocol.json:/app/protocol.json -v ${location}/3/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
docker create -it --name=neo-node-4 -v ${location}/4/Chain:/app/Chain -v ${location}/4/Index:/app/Index -v ${location}/4/config.json:/app/config.json -v ${location}/4/protocol.json:/app/protocol.json -v ${location}/4/wallet.json:/app/wallet.json --network=neo --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli

# Start the nodes
for node in neo-node-1 neo-node-2 neo-node-3 neo-node-4; do docker start ${node}; done

# Create a regular neo-cli container that is not a consensus node and expose the RPC and P2P ports
docker run -it --name=neo-cli --network=neo -p 10332-10333:10332-10333 --restart=unless-stopped --log-opt max-size=128m smarteconomyworkshop/neo-cli
```
