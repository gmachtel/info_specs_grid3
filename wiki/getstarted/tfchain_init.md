

## TF-Chain

For deployments, you'll need a TF Chain acount and a twin registered. Visit [this page](https://vgrid.staging2.threefold.io/#/vgrid__grid_substrate_getting_started) for instructions. You'll need both your mnemonic phrase and Twin id to pass to the Terraform plugin.

Important notes:

You must choose `ed25519` as the "crypto type" when setting up your account
When funding your wallet from the provided faucet accounts, you can transfer more than the shown balance of those accounts. Take a few thousand tokens to make sure you don't run out while testing
The guide advises to find your twin ID by checking the `twinID()` method. However, this will only correspond to your ID until a new twin is created and the value is incremented. To make sure you're seeing the ID associated with your account, use `twinIdByAccountID(AccountId)` instead, and select your account name from the drop down.

## Create twin

### Create account on substrate using polkadot

- Go to polkadot UI
![image](https://user-images.githubusercontent.com/64129/125321199-bc340200-e33c-11eb-80c9-d37f4c843f28.png)


- Click on `Add an account` in [polkadot accounts](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fexplorer.devnet.grid.tf%2Fws#/accounts)
- Save the mnemonic seed -needs to be ed25519- in a safe place
- Add a name and password for your account (remember the password for future usage)
- Fund the account with test funds (Click on send funds from the account of Alice to your account name)


### Add types in the UI
- add types https://github.com/threefoldtech/tfgrid-api-client/blob/master/types.json in developer settings 
![image](https://user-images.githubusercontent.com/64129/125321714-441a0c00-e33d-11eb-8546-09704d5ceffd.png)


### Create twin on substrate using polkadot

- Select the options to create the twin in [polkadot developer extrinsics](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fexplorer.devnet.grid.tf%2Fws#/extrinsics)

  - Selected account -> your Account name

  - Extrinsic module to be submitted(from drop down menu) -> tfgridModule

  - Extrinsic method to be submitted -> createTwin(ip)

  - ip -> Ipv6 obtained from your yggdrasil
- Submit transaction and enter password selected when creating the account

- To get your twin ID, select the options required in [polkadot developer chainstate](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fexplorer.devnet.grid.tf%2Fws#/chainstate) and click on the +
  - Module -> tfgridModule
  - Method -> twinID(): u32t



