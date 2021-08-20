## Playground for Ethereum on StreamingFast

This doc holds instructions to setup your first Firehose on Ethereum.

As of August 9th 2021, there is a binary release available in the *Releases* section of this repository. Source code will follow in the new few days, and will be available at https://github.com/streamingfast/sf-ethereum

Please read https://forum.thegraph.com/t/introducing-the-firehose/2329 and the included doc to have an idea of what the Firehose is.

NOTE: This short intro is the simplest deployment, but does not provide all the guarantees of a production deployment, like high-availability and quick recovery.


## Setup

Checkout this repository

Download the binaries here: https://github.com/streamingfast/playground-ethereum-firehose/releases/tag/v0.0.1 and put them at the root of this repo.

`chmod +x` them


## Run the node and extract data

Run this:

```bash
./sfeth -c bsc-mainnet.yaml start mindreader-node
```

This launch the `bsc-geth` process underneath. It should write a bunch of stuff in `./bsc-data`.

Running with `sfeth start -c eth-mainnet.yaml` will run `eth-geth` to sync with Ethereum Mainnet and write stuff to `eth-data`.

The process can be interrupted and restarted.


## Inspect the data

You will find under `eth-data` or `bsc-data` the `storage/merged-blocks` directory. Run:

```bash
./sfeth tools print blocks --store ./eth-data/storage/merged-blocks 100000
```

once your node has sync'd at least 100000 blocks.


## Run the Firehose independently:

```bash
./sfeth -c bsc-mainnet.yaml start relayer firehose
```

## Connect to your Firehose

Once the `firehose` is running and is listening on its port, continue on.

With `grpcurl`, by [installing grpcurl](https://github.com/fullstorydev/grpcurl), a simple `curl`-like program to communicate via gRPC, then connect to the stream with:

```
grpcurl -insecure -import-path protos -proto codec.proto -proto bstream.proto -d '{"start_block_num": 10}' localhost:13042 dfuse.bstream.v1.BlockStreamV2.Blocks
```

> Another option would be to use the SF Client https://github.com/streamingfast/streamingfast-client.

```
git clone https://github.com/streamingfast/streamingfast-client.git
cd streamingfast-client

go run ./cmd/sf -s -e localhost:13042 "*" 10 20
```
