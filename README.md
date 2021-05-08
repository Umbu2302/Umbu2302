- 👋 Hi, I’m @Umbu2302
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Umbu2302/Umbu2302 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
import { signTypedData_v4 } from 'eth-sig-util'
import { fromRpcSig } from 'ethereumjs-util'

// ... other imports

import aTokenAbi from "./aTokenAbi.json"

// ... setup your web3 provider

const aTokenAddress = "ATOKEN_ADDRESS"
const aTokenContract = new web3.eth.Contract(aTokenAbi, aTokenAddress)

const privateKey = "YOUR_PRIVATE_KEY_WITHOUT_0x"
const chainId = 1
const owner = "OWNER_ADDRESS"
const spender = "SPENDER_ADDRESS"
const value = 100 // Amount the spender is permitted
const nonce = 1 // The next valid nonce, use `_nonces()`
const deadline = 1600093162

const permitParams = {
  types: {
    EIP712Domain: [
      { name: "name", type: "string" },
      { name: "version", type: "string" },
      { name: "chainId", type: "uint256" },
      { name: "verifyingContract", type: "address" },
    ],
    Permit: [
      { name: "owner", type: "address" },
      { name: "spender", type: "address" },
      { name: "value", type: "uint256" },
      { name: "nonce", type: "uint256" },
      { name: "deadline", type: "uint256" },
    ],
  },
  primaryType: "Permit",
  domain: {
    name: "aTOKEN_NAME",
    version: "1",
    chainId: chainId,
    verifyingContract: aTokenAddress,
  },
  message: {
    owner,
    spender,
    value,
    nonce,
    deadline,
  },
}

const signature = signTypedData_v4(
  Buffer.from(privateKey, "hex"),
  { data: permitParams }
)

// The signature can now be used to execute the transaction

const { v, r, s } = fromRpcSig(signature)

await aTokenContract.methods
    .permit({
      owner,
      spender,
      value,
      deadline,
      v,
      r,
      s
    })
    .send()
    .catch((e) => {
        throw Error(`Error permitting: ${e.message}`)
    })



