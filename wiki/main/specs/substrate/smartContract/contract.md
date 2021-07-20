# Smart Contract for IT on the blockchain

## Proposed architecture

Two main components will play a role in achieving a decentralised consensus between a user and a farmer.

1: TFGrid Substrate Database Pallet TFGrid

2: TFGrid Smart Contract

The TFGrid Substrate Database will keep a record of all Entities, Twins, Nodez and Farmers in the TF Grid network. This makes it easy to integrate the Smart Contract on Substrate as well since we can read from that storage in Runtime.

check flow diagram: [flow](flow.png)
![flow](img/flow.png)

The Smart Contract on Substrate will work as following:

## 1: The user wants to deploy a workload, he interacts with this smart contract pallet and calls: `create_contract` with the input being:

The user must instruct his twin to create the contract. *This program containing his digital twin is yet to be defined.* A contract will always belong to a twin and to a node. This relationship is important because only the user's twin and target node's twin can update the contract.


```js
contract = {
    version: contractVersion,
    contract_id: contractID,
    twin_id: NumericTwinID for the contract,
    // node_address is the node address.
    node_address: "<node address>"
    // data is the encrypted deployment body. This encrypted the deployment with the **USER** public key. So only the user can read this data later on (or any other key that he keeps safe).
    // this data part is read only by the user and can actually hold any information to help him reconstruct his deployment or can be left empty.
    data: encrypted(deployment) // optional
    // hash: is the deployment predictable hash. the node must use the same method to calculate the challenge (bytes) to compute this same hash.
    //used for validating the deployment from node side.
    deployment_hash: hash(deployment),
    // public_ips: number of ips that need to be reserved by the contract and used by the deployment
    public_ips: 0,
    state: ContractState (created, deployed),
    last_updated: timestamp in second from last update,
    previous_nu_reported: previous reported used network units to calculate usage,
    public_ips_list: list of public ips on this contract
}
```
The `node_address` field is the target node's address. A user can do lookup for a node to find it's corresponding address.

The workload data is encrypted by the user and contains the workload definition for the node.

If `public_ips` is specified, the contract will reserve the number of public ips requested on the node's corresponding farm. If there are not enough ips available an error will be returned. If the contract is canceled by either the user or the node, the ips for that contract will be freed.

This pallet saves this data to storage and returns the user a `contract_id`.

## 2: The user sends the contractID and workload through the RMB to the destination Node.

The Node reads from the [RMB](https://github.com/threefoldtech/rmb) and sees a deploy command, it reads the contractID and workload definition. It decodes the workload and does validation before it deploys the contents. If successfull it sets the Contract to state `deployed` on the chain. Else the contract is removed.

## 3: The Node sends consumption reports to the chain

The Node periodically sends consumption reports back to the chain for each deployed contract. The chain will compute how much is being used and will bill the user based on the farmers prices (the chain can read these prices by quering the farmers storage and reading the pricing data). See [PricingPolicy](https://github.com/threefoldtech/substrate-pallets/blob/03a5823ce79200709d525ec182036b47a60952ef/pallet-tfgrid/src/types.rs#L120).

A report looks like:

json
```
{
	"contract_id": contractID,
    "timestamp": "timestampOfReport",
	"cru": cpus,
	"sru": ssdInBytes,
	"hru": hddInBytes,
	"mru": memInBytes,
	"nru": trafficInBytes
}
```

The node can call `add_reports` on this module to submit reports in batches.

Usage of SU, CU and NU will be computed based on the prices and the rules that Threefold set out for cloud pricing.

Billing will be done in Database Tokens and will be send to the corresponding farmer. If the user runs out of funds the chain will set the contract state to `canceled` or it will be removed from storage. The Node needs to act on this contact canceled event and decomission the workload. 

The main currency of this chain. More information on this is explained here: TODO

## Footnote

Sending the workloads encrypted to the chain makes sure that nobody except the destination Node can read the deployment's information as this can contain sensitive data. This way we also don't need to convert all the Zero OS primitive types to a Rust implementation and we can keep it relatively simple.