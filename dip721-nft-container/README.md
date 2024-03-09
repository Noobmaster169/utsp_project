# DIP721 NFTs

### An Example Of DIP721 NFT Token Standard Provide By Internet Computer

[DIP-721](https://github.com/Psychedelic/DIP721) is an ERC-721 style Non-Fungible Token standard built mirroring its Ethereum counterpart and adapting it to the Internet Computer, maintaining the same interface.
This Token Standard could provide the necessary Interface to interact with UTSP Protocol to Identify and Verify Voters. 

Read More About The DFINITY Code Documentation [Here](https://github.com/dfinity/examples/tree/master/motoko/dip721-nft-container).


### Prerequisites 

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download and install [git.](https://git-scm.com/downloads)

 ### Step 1: Clone the project repo:

```bash
git clone git@github.com:dfinity/examples.git
```

 ### Step 2: Navigate to DIP721 project root:

```bash
cd examples/motoko/dip-721-nft-container
```

 ### Step 3: Run a local instance of the Internet Computer:

```bash
dfx start --background 
```

**If this is not a new installation, you may need to run `start` with the `--clean` flag.**

```bash
dfx start --clean --background
```

### Step 4: Configure The Owner of The DIP721 NFT Canister
```bash
export OWNER=$(dfx identity get-principal)
```

### Step 5: Configure The Token Data of The DIP721 NFT
```bash
export NAME="VOTING TOKEN"
export SYMBOL="\$VOTE"
export MAX_TOKEN=100
```
  
### Step 6: Deploy the DIP721 NFT Canister to Playground:

This command deploys the DIP721 NFT canister to IC Playground with the initialized arguments:

```bash
dfx deploy --argument "(
  principal\"$OWNER\", 
  record {
    logo = record {
      logo_type = \"NFT_logo/png\";
      data = \"\";
    };
    name = \"$NAME\";
    symbol = \"$SYMBOL\";
    maxLimit = $MAX_TOKEN;
  }
)" --playground dip721_nft_container
```

### Step 7: Configure NFT Canister

Configure the NFT Canister using the given link during deployment.

```bash
export CANISTER=aaaaa-bbbbb-ccccc-ddddd-cai
```

### Step 8: Mint NFT To A Receiver:

Use the following command to mint an NFT on the IC Network:

```bash
export RECEIVER=$(dfx identity get-principal)
dfx canister call $CANISTER mintDip721 \
"(
  principal\"$RECEIVER\", 
  vec { 
    record {
      purpose = variant{Rendered};
      data = blob\"hello\";
      key_val_data = vec {
        record { key = \"description\"; val = variant{TextContent=\"The NFT metadata can hold arbitrary metadata\"}; };
        record { key = \"tag\"; val = variant{TextContent=\"anime\"}; };
        record { key = \"contentType\"; val = variant{TextContent=\"text/plain\"}; };
        record { key = \"locationType\"; val = variant{Nat8Content=4:nat8} };
      }
    }
  }
)" --network ic
```

If this succeeds, you should see the following message:

```bash
(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })
```

### Step 9: Transferring an NFT:
The DIP721 interface supports transferring an NFT to some other `principal` values via the `transferFromDip721` or `safeTransferFromDip721` methods.

First, create a different identity using DFX. This will become the principal that you receives the NFT

```bash
dfx identity new --disable-encryption alice
ALICE=$(dfx --identity alice identity get-principal)
```

Verify the identity for `ALICE` was created and set as an environment variable:

```bash
echo $ALICE
```

You should see a principal returned:

```bash
o4f3h-cbpnm-4hnl7-pejut-c4vii-a5u5u-bk2va-e72lb-edvgw-z4wuq-5qe
```

You can Transfer the NFT to `ALICE`'s Address. 

Here the arguments are:
`from`: principal that owns the NFT
`to`: principal to transfer the NFT to
`token_id`: the id of the token to transfer

```bash
dfx canister call dip721_nft_container transferFromDip721 "(principal\"$(dfx identity get-principal)\", principal\"$ALICE\", 0)"
```


## More Documentation on DIP-721 [Here](https://github.com/dfinity/examples/tree/master/motoko/dip721-nft-container)


## Security considerations and best practices

If you base your application on this example, we recommend you familiarize yourself with and adhere to the [security best practices](https://internetcomputer.org/docs/current/references/security/) for developing on the Internet Computer. This example may not implement all the best practices.

For example, the following aspects are particularly relevant for this app:
* [Inter-canister calls and rollbacks](https://internetcomputer.org/docs/current/references/security/rust-canister-development-security-best-practices/#inter-canister-calls-and-rollbacks), since issues around inter-canister calls can e.g. lead to time-of-check time-of-use or double spending security bugs.
* [Certify query responses if they are relevant for security](https://internetcomputer.org/docs/current/references/security/general-security-best-practices#certify-query-responses-if-they-are-relevant-for-security), since this is essential when e.g. displaying important NFT data in the frontend that may be used by users to decide on future transactions.
* [Use a decentralized governance system like SNS to make a canister have a decentralized controller](https://internetcomputer.org/docs/current/references/security/rust-canister-development-security-best-practices#use-a-decentralized-governance-system-like-sns-to-make-a-canister-have-a-decentralized-controller), since decentralizing control is a fundamental aspect when dealing with NFTs.


## Resources
[Rust](https://rustup.rs).
[DIP721](https://github.com/Psychedelic/DIP721).
[Minting tool](https://github.com/dfinity/experimental-minting-tool).
