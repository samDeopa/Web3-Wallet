# Web3 Wallet Project - README

## Project Overview

This is a web-based wallet application that allows users to generate seed phrases (mnemonics), create Solana and Ethereum wallets, and manage multiple accounts on each blockchain. The project is built using React and leverages the `bip39` library for mnemonic generation and blockchain SDKs to manage keys for both Solana and Ethereum.

## Prerequisites

- **Node.js**
- **npm or yarn** for dependency management
- **React** for front-end development
- **Vite** for faster builds and development
- **Solana SDK (`@solana/web3.js`)** and **Ethereum SDK (`ethers.js`)** for blockchain interactions

### Key Libraries:

- `vite-plugin-node-polyfills` to include polyfills in a Vite-based React project.
- `bip39` to generate and manage mnemonic seed phrases.
- `@solana/web3.js` and `nacl` for Solana wallet management.
- `ethers` for Ethereum wallet management.

## Setup Instructions

### 1. Initialize the Project

First, create a new React project using Vite:

```bash
npm create vite@latest
```

### 2. Install Dependencies

```bash
npm install
npm install vite-plugin-node-polyfills bip39 @solana/web3.js ethers
```

### 3. Add Polyfills for Node.js

Since Vite doesn’t include Node.js polyfills by default, install and configure `vite-plugin-node-polyfills`.

Add the following to your `vite.config.js`:

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { nodePolyfills } from "vite-plugin-node-polyfills";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), nodePolyfills()],
});
```

### 4. Clean Up App Component

In `App.jsx`, clean up the default state and create the base for the project:

```js
import { useState } from "react";
import "./App.css";

function App() {
  const [mnemonic, setMnemonic] = useState("");

  return (
    <div>
      <h1>Web3 Wallet</h1>
      <button
        onClick={async function () {
          const mn = await generateMnemonic();
          setMnemonic(mn);
        }}
      >
        Create Seed Phrase
      </button>
      <input type="text" value={mnemonic} readOnly />
      {/* Add your wallet components here */}
    </div>
  );
}

export default App;
```

### 5. Solana Wallet Component

Add a `SolanaWallet` component for creating and managing Solana wallets:

```js
import { useState } from "react";
import { mnemonicToSeed } from "bip39";
import { derivePath } from "ed25519-hd-key";
import { Keypair } from "@solana/web3.js";
import nacl from "tweetnacl";

export function SolanaWallet({ mnemonic }) {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [publicKeys, setPublicKeys] = useState([]);

  return (
    <div>
      <button
        onClick={function () {
          const seed = mnemonicToSeed(mnemonic);
          const path = `m/44'/501'/${currentIndex}'/0'`;
          const derivedSeed = derivePath(path, seed.toString("hex")).key;
          const secret = nacl.sign.keyPair.fromSeed(derivedSeed).secretKey;
          const keypair = Keypair.fromSecretKey(secret);
          setCurrentIndex(currentIndex + 1);
          setPublicKeys([...publicKeys, keypair.publicKey.toBase58()]);
        }}
      >
        Add Solana Wallet
      </button>

      {publicKeys.map((p, idx) => (
        <div key={idx}>Solana Public Key: {p}</div>
      ))}
    </div>
  );
}
```

### 6. Ethereum Wallet Component

Add an `EthWallet` component for creating and managing Ethereum wallets:

```js
import { useState } from "react";
import { mnemonicToSeed } from "bip39";
import { Wallet, HDNodeWallet } from "ethers";

export const EthWallet = ({ mnemonic }) => {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [addresses, setAddresses] = useState([]);

  return (
    <div>
      <button
        onClick={async function () {
          const seed = await mnemonicToSeed(mnemonic);
          const derivationPath = `m/44'/60'/${currentIndex}'/0'`;
          const hdNode = HDNodeWallet.fromSeed(seed);
          const child = hdNode.derivePath(derivationPath);
          const wallet = new Wallet(child.privateKey);
          setCurrentIndex(currentIndex + 1);
          setAddresses([...addresses, wallet.address]);
        }}
      >
        Add Ethereum Wallet
      </button>

      {addresses.map((p, idx) => (
        <div key={idx}>Ethereum Address: {p}</div>
      ))}
    </div>
  );
};
```

### 7. Run the Project

After setting up the components, you can start your Vite project with:

```bash
npm run dev
```

## Features

1. **Generate Mnemonics**: Create a seed phrase for the user with a simple click.
2. **Solana Wallet**: Create and manage multiple Solana wallets from the same seed.
3. **Ethereum Wallet**: Generate and manage Ethereum wallets with their public addresses.
4. **Responsive Design**: React-based, allowing for further scalability and integration with blockchain features like sending and receiving assets.

## Future Enhancements

- **Integration with Solana and Ethereum networks** for real-time balance checks.
- **Send/Receive transactions** for Solana and Ethereum wallets.
- **UI Improvements** for better user interaction.
- **Multichain support** to include more blockchain networks.

## References

- [Bip39 Mnemonic](https://www.npmjs.com/package/bip39)
- [Solana Web3.js](https://solana-labs.github.io/solana-web3.js/)
- [Ethers.js](https://docs.ethers.org/v5/)

## License

This project is open-source and available under the [MIT License](LICENSE).
