# Origo_CrossSelling

### Setup

1. Clone the repo

```
git clone xxx
```

2. Install global blockchain modules

```
npm i -g truffle
npm i -g ganache-cli
```

### Regular Ethereum Implementation

```
cd public_cs
```

**README for Usage is in the public_cs directory**

### ZoKrates Implementation

```
cd zero_knowledge
```

**README for Usage is in the zero_knowledge directory**

###Proof Generation:

1. Download [Zokrates](https://github.com/JacobEberhardt/ZoKrates) and run pre-built docker instance

```
docker run -ti zokrates/zokrates /bin/bash
```

2. Move into the proper directory

```
cd target/releases
```

3. Compile, Setup, and Export Verifier

```
./zokrates compile -i 'vip.code'
./zokrates setup
./zokrates export-verifier
```

4. Copy code needed into a blank truffle project

```
docker cp <DOCKER ID>:root/ZoKrates/verifier.sol ./contracts
docker cp <DOCKER ID>:root/ZoKrates/proving.key ./
docker cp <DOCKER ID>:root/ZoKrates/out ./
docker cp <DOCKER ID>:root/ZoKrates/variables.inf ./
```

5. For each user Compute Witness and Generate the Proof

```
./zokrates compute-witness -a <INPUTS>
./zokrates generate-proof > <FILE that contains Proof Information>
```

6. Copy the Proof File to Current Project 'proofs' Directory
7. Run ./convertproof.py inside the 'proofs' directory. This outputs the proof information as JSON object in the form of userID: [A, A_p, B, B_p, C, C_p, H, K] as well as correct types to be used in Web3.
8. Javascript in apps.js parses this json and has associated proof variables to user

### Issues

1. **userHash as input to ZoKrates High-Level Code**: We talked about how it would be good if we had a userHash to be the input to verifyTx, so the third-party could confirm the identity of the person they were confirming the VIP Score of. However, .code functions that are compiled by ZoKrates only took in integers as parameters and therefore the userHash was not accepted. I settled on just inputting userID which simply the number of total users when they were added
2. **call() vs sendTransaction()**: SendTransaction invoked verifyTx and modifies the state of the contract, and puts the transaction on chain to be mined. However, it doesn't return the value of the call just a transactionHash and in the complex promises I didn't want catch the event and then return the event value down the line.
