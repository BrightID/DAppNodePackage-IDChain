# BrightID IDChain DAppNode package

General information about IDChain is available at https://github.com/BrightID/IDChain.

## Installation
If you just want to run a node, there are no extra actions necessary. Install the DAppNodePackage as usual.
Once the node is running you will see the sync status on the Dashboard.

## Connecting to your IDChain instance
The rpc API of your personal IDChain node is available via:
 - http: `http://idchain.dappnode:8545`
 - Websocket: `ws://idchain.dappnode:8545`

## Become IDChain validator
> Note: `$EXTRA_OPTS` and `$SYNCMODE` variables can be modified on your DAppNode at http://my.dappnode/#/packages/idchain.dnp.dappnode.eth/config.

### Preparation
 - Change `$EXTRA_OPTS` to include `--rpcapi personal,clique`
 - Attach to your IDChain instance on the dappnode: `geth attach http://idchain.dappnode:8545`
 - Create or import an account to be used for validating, using geth `personal` namespace functions 
 (see https://geth.ethereum.org/docs/rpc/ns-personal)
 - If you created a new account, make sure to backup the keystore. Go to 
 http://my.dappnode/#/packages/idchain.dnp.dappnode.eth/file-manager and enter `/idchain/keystore` 
 in the _DOWNLOAD FILE FROM PACKAGE_ form. Download keystore.zip and store in a secure location.
 - Inform other validators to propose your account for validating
 - Check clique status to see if you are approved using `clique.status()`

### Start validating
 Once you are approved you have to restart idchain with mining enabled and your validating account unlocked:
 - Create a txtfile with the passphrase required to unlock the validator account: `echo <PASSPHRASE> > passphrase.txt`
 - Upload `passphrase.txt` to the dappnode filesystem into `/idchain/` folder (Use http://my.dappnode/#/packages/idchain.dnp.dappnode.eth/file-manager)
 - Change `$EXTRA_OPTS` to unlock your signer account and start mining. It should include `--miner.gasprice 10000000000 
 --mine --unlock <your signer account address> --password /idchain/passphrase.txt --allow-insecure-unlock`. (`--allow-insecure-unlock`
 is a tolerable risk, as the rpc api is only exposed over the DappNode VPN. See [Security section](#Security) below for alternative setup)
 - Change `$SYNCMODE` to `full`
 - Save new config. Your IDChain node will restart and should start validating blocks!

### Security
To further lockdown the IDChain instance you can do the following:
- Disable rpc access by removing `--rpcapi <modules>` and `-allow-insecure-unlock` from `$EXTRA_OPTS`

Now you can only attach to your IDChain instance via SSH:
1. SSH into your dappnode host
1. Execute ```docker exec -ti DAppNodePackage-idchain.dnp.dappnode.eth /usr/local/bin/geth attach /idchain/geth.ipc```

The downside of this change is that you no longer can connect to your IDChain instance with Metamask.

# DNP Development

### Preconditions
 - Install dappnode SDK: `npm install -g @dappnode/dappnodesdk`

### Build and Publish
1. commit changes to `dev` branch. 
1. *Optional*: Update `upstreamVersion` in `dappnode_package.json` to match the IDChain version
1. Build the DAppNode package (`dappnodesdk build`) and test installation on your DAppNode 
1. If package works as expected, merge changes to `master` branch (via pull request from `dev` to `master`)
1. Checkout master branch
1. Build the DAppNode package: `dappnodesdk build`
1. Publish the package: `npx @dappnode/dappnodesdk publish <major|minor|patch> --developer_address <developer ethereum address>`
1. Above step will update some files with the new version and ipfs hashes. Commit all these changes to the master branch.
