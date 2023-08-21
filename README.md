# Uniswap V3 Subgraph for Manta Pacific

### Bring up Graph Node for Testnet

We deploy Docker containers on Amazon Elastic Container Service (ECS).

First, install (Docker Desktop)[https://www.docker.com/products/docker-desktop/].

Second, add Docker bin directory to `PATH`. On Mac with zsh, add the following line to `~/.zprofile`:
```shell
export PATH="$PATH:/Applications/Docker.app/Contents/Resources/bin/"
```

Third, if haven't already, create an ECS context with Docker by running
```shell
docker context create ecs ecscontext
```
which creates an ECS context named 'ecscontext'.

That's all the preparation steps. To bring up the graph node, or to update the graph node, run
```shell
cd docker/
docker-compose --context ecscontext --project-name manta-pacific-graph-node up
```

To bring down the graph node, run
```shell
cd docker/
docker-compose --context ecscontext --project-name manta-pacific-graph-node down
```

To see graph node logs, run
```shell
cd docker/
docker-compose --context ecscontext --project-name manta-pacific-graph-node logs
```

You may also make the docker context persist by running
```shell
docker context use ecscontext
```
and then you won't have to pass `--context ecscontext` to `docker-compose` commands.

### Deploy Manta Pacific Testnet Subgraph to the Graph Node

Once the Graph Node is up and runnning, update `package.json` with the graph node url for `create-remote` and `deploy-remote`.
The node url can be obtained by the AWS ECS dashboard. Select the stack and go into one of the three services, e.g. GraphNode.
Go to the 'Networking' tab and grab the url from the 'DNS name' section. It looks like `manta-LoadB-NVW76ZGFHXPO-e5139453fef6a975.elb.us-west-2.amazonaws.com`.

Then, deploy the subgraph to the node by running
```shell
yarn
yarn run codegen
yarn run build
yarn run create-remote
yarn run deploy-remote
```

To query the subgraph for a list of pools, try `http://manta-loadb-nvw76zgfhxpo-e5139453fef6a975.elb.us-west-2.amazonaws.com:8000/subgraphs/name/aperture/uniswap-v3/graphql?query=query+getPools+%7B%0A++pools+%7B%0A++++id%0A++++feeTier%0A++++token0+%7B%0A++++++id%0A++++++name%0A++++%7D%0A++++token1+%7B%0A++++++id%0A++++++name%0A++++%7D%0A++%7D%0A%7D`.

Finally, inspect the graph node logs to make sure that the subgraph has started indexing correctly.
Run `docker-compose --context ecscontext --project-name manta-pacific-graph-node logs` from the `docker-compose.yaml` directory of the `uniswap-v3-subraph` repository, and you should see logs like the one below.

```
graph-node  | Aug 09 21:11:11.384 INFO Received subgraph_create request, params: SubgraphCreateParams { name: SubgraphName("aperture/uniswap-v3") }, component: JsonRpcServer
graph-node  | Aug 09 21:11:12.735 INFO Syncing 1 blocks from Ethereum, code: BlockIngestionStatus, blocks_needed: 1, blocks_behind: 1, latest_block_head: 318504, current_block_head: 318503, provider: manta-pacific-testnet-rpc-0, component: EthereumPollingBlockIngestor
graph-node  | Aug 09 21:11:22.531 INFO Syncing 1 blocks from Ethereum, code: BlockIngestionStatus, blocks_needed: 1, blocks_behind: 1, latest_block_head: 318505, current_block_head: 318504, provider: manta-pacific-testnet-rpc-0, component: EthereumPollingBlockIngestor
graph-node  | Aug 09 21:11:32.230 INFO Syncing 1 blocks from Ethereum, code: BlockIngestionStatus, blocks_needed: 1, blocks_behind: 1, latest_block_head: 318506, current_block_head: 318505, provider: manta-pacific-testnet-rpc-0, component: EthereumPollingBlockIngestor
graph-node  | Aug 09 21:11:34.833 INFO Received subgraph_deploy request, params: SubgraphDeployParams { name: SubgraphName("aperture/uniswap-v3"), ipfs_hash: DeploymentHash("QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU"), node_id: None, debug_fork: None, history_blocks: None }, component: JsonRpcServer
graph-node  | Aug 09 21:11:35.633 INFO Set subgraph start block, block: Some(#261466 (eaaea7cb690dbbe9649a48cf359b3fe9e4e1d97ae7d9b2e81a000d7438c8edd5)), sgd: 0, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphRegistrar
graph-node  | Aug 09 21:11:35.633 INFO Graft base, block: None, base: None, sgd: 0, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphRegistrar
graph-node  | Aug 09 21:11:37.049 INFO Resolve subgraph files using IPFS, n_templates: 1, n_data_sources: 2, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:37.052 INFO Successfully resolved subgraph files using IPFS, features: nonFatalErrors, n_templates: 1, n_data_sources: 2, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:37.140 INFO Starting subgraph writer, queue_size: 5, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:37.171 INFO Data source count at start: 2, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:37.757 INFO Scanning blocks [261467, 261467], range_size: 1, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:37.968 INFO Scanning blocks [261468, 261477], range_size: 10, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:38.195 INFO Scanning blocks [261478, 261482], range_size: 100, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:38.396 INFO Scanning blocks [261483, 262482], range_size: 1000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:38.603 INFO Scanning blocks [262483, 264482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:38.831 INFO Scanning blocks [264483, 266482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:39.067 INFO Scanning blocks [266483, 268482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:39.291 INFO Scanning blocks [268483, 270482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:39.554 INFO Scanning blocks [270483, 272482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:39.775 INFO Scanning blocks [272483, 274482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:40.000 INFO Scanning blocks [274483, 276482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:40.217 INFO Scanning blocks [276483, 278482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:40.451 INFO Scanning blocks [278483, 280482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:40.671 INFO Scanning blocks [280483, 282482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:40.909 INFO Scanning blocks [282483, 284482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:41.166 INFO Scanning blocks [284483, 286482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:41.413 INFO Scanning blocks [286483, 288482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:41.662 INFO Scanning blocks [288483, 290482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:41.784 INFO Create data source, params: 0xe1b4f77e664a3f57decc66e71b3dd7868842363d, name: Pool, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:41.786 INFO Done processing trigger, gas_used: 40516062533, data_source: Factory, handler: handlePoolCreated, total_ms: 683, transaction: 0xbc4b…4518, address: 0x8844…b28b, signature: PoolCreated(indexed address,indexed address,indexed uint24,int24,address), sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:41.897 INFO Scanning blocks [290483, 292482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:41.994 INFO Syncing 1 blocks from Ethereum, code: BlockIngestionStatus, blocks_needed: 1, blocks_behind: 1, latest_block_head: 318507, current_block_head: 318506, provider: manta-pacific-testnet-rpc-0, component: EthereumPollingBlockIngestor
graph-node  | Aug 09 21:11:42.036 INFO Done processing trigger, gas_used: 10292504202, data_source: NonfungiblePositionManager, handler: handleTransfer, total_ms: 249, transaction: 0xbc4b…4518, address: 0x2dc1…49e1, signature: Transfer(indexed address,indexed address,indexed uint256), sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.042 INFO Done processing trigger, gas_used: 5290979741, data_source: NonfungiblePositionManager, handler: handleIncreaseLiquidity, total_ms: 6, transaction: 0xbc4b…4518, address: 0x2dc1…49e1, signature: IncreaseLiquidity(indexed uint256,uint128,uint256,uint256), sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.237 INFO Scanning blocks [292483, 294482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:42.505 INFO Scanning blocks [294483, 296482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:42.535 INFO 2 triggers found in this block for the new data sources, block_hash: 0xd62857172da2da4cd1994e339ad8f2d0d7e282c257609a870b9afb5a9b2b6299, block_number: 284230, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.549 INFO Done processing trigger, gas_used: 564135901, data_source: Pool, handler: handleInitialize, total_ms: 12, transaction: 0xbc4b…4518, address: 0xe1b4…363d, signature: Initialize(uint160,int24), block_hash: 0xd62857172da2da4cd1994e339ad8f2d0d7e282c257609a870b9afb5a9b2b6299, block_number: 284230, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.711 INFO Scanning blocks [296483, 298482], range_size: 2000, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: BlockStream
graph-node  | Aug 09 21:11:42.780 INFO Done processing trigger, gas_used: 11613183230, data_source: Pool, handler: handleMint, total_ms: 231, transaction: 0xbc4b…4518, address: 0xe1b4…363d, signature: Mint(address,indexed address,indexed int24,indexed int24,uint128,uint256,uint256), block_hash: 0xd62857172da2da4cd1994e339ad8f2d0d7e282c257609a870b9afb5a9b2b6299, block_number: 284230, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.787 INFO Applying 21 entity operation(s), block_hash: 0xd62857172da2da4cd1994e339ad8f2d0d7e282c257609a870b9afb5a9b2b6299, block_number: 284230, sgd: 1, subgraph_id: QmUxDEZ91MqUyPCfk1EGeBZ1GWb8nMBWHinyRPz9mfswtU, component: SubgraphInstanceManager
graph-node  | Aug 09 21:11:42.917 INFO Scanning blocks [284231, 284231], range_size: 1, sgd: 1, subgraph_id: QmUxDEZ91MqUyPC
```
