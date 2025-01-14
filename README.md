# peercoin/peercoind

[![](https://images.microbadger.com/badges/image/peercoin/peercoind.svg)](https://microbadger.com/images/peercoin/peercoind "Size/Layers")

Peercoind docker image. There are two tags: `latest` - which provides classic peercoin binary built from github/peercoin repository.

## Supported tags and respective `Dockerfile` links

- `0.8.3` ([0.8.3/Dockerfile](https://github.com/peercoin/docker-peercoind/blob/master/0.8.3/Dockerfile))

## Usage
### How to use this image

It behaves like a binary, so you can pass any arguments to the image and they will be forwarded to the `ppcoind` binary:

```sh
$ docker run --name peercoind -d peercoin/peercoind \
  -rpcallowip=* \
  -rpcpassword=bar \
  -rpcuser=foo
```

Due to this you can use the same command to start the testnet container:

```sh
$ docker run --name ppctestnet -d peercoin/peercoind \
  -rpcallowip=* \
  -rpcpassword=bar \
  -rpcuser=foo \
  -testnet=1
```

By default, `peercoin` will run as as user `sunny` for security reasons and with its default data dir (`~/.peercoin`). If you'd like to customize where `peercoin` stores its data, you must use the `PPC_DATA` environment variable. The directory will be automatically created with the correct permissions for the `sunny` user and `peercoin` automatically configured to use it.

```sh
$ docker run --env PPC_DATA=/var/lib/peercoin --name peercoind -d peercoin/peercoind
```

You can also mount a directory it in a volume under `/home/sunny/.peercoin` in case you want to access it on the host:

```sh
$ docker run -v /opt/peercoin:/home/sunny/.peercoin --name peercoind -d peercoin/peercoind
```
That will allow to access containers `~/.peercoin` directory in `/opt/peercoin` on the host.


```sh
$ docker run -v ${PWD}/data:/home/sunny/.peercoin --name peercoind -d peercoin/peercoind
```
will mount current directory in containers `~/.peercoin`

To map container RPC ports to localhost start container with following command:

```sh
$ docker run -v /opt/ppc:/home/sunny/.peercoin -p 9902:9902 --name peercoind -d peercoin/peercoind -rpcallowip=*
```
You may want to change the port that it is being mapped to if you already run a peercoin instance on the localhost.
For example: `-p 9999:9902` will map port 9902 from container to localhost:9999.

Now you will be able to `curl` the peercoin RPC in the container:

`curl --user bitcoin:password --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getinfo", "params": [] }'  -H 'content-type: text/plain;' localhost:9902/`

> {"result":{"version":"PARS:0.5.4(v0.5.4.0-ge520ebd)","protocolversion":60006,"walletversion":60000,"balance":0.00000000,"newmint":0.00000000,"stake":0.00000000,"blo
cks":110392,"moneysupply":21378261.50852500,"connections":8,"proxy":"","ip":"83.161.111.125","difficulty":150011848.56944141,"testnet":false,"keypoololdest":1475227
858,"keypoolsize":101,"paytxfee":0.01000000,"errors":"WARNING: Checkpoint is too old. Wait for block chain to download, or notify developers of the issue."},"error"
:null,"id":"curltest"}
