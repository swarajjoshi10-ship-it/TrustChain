# Satya Protocol: AI-Verified Decentralized Escrow

Satya Protocol is an event-driven, blockchain-based escrow system designed to ensure transparency in non-governmental organization (NGO) and public funding. It integrates decentralized storage and local, vision-based LLMs to automatically verify real-world proof of work before releasing funds to vendors.

## Problem Statement

Traditional philanthropic and public funding mechanisms suffer from opacity and delayed audits. Funds are often distributed without immediate, verifiable proof that the contracted work was executed. Satya Protocol solves this by implementing a trustless, AI-gated milestone system on the Ethereum blockchain, ensuring capital is only deployed when cryptographic and visual proof of completion is validated by an autonomous AI agent.

## Core Features

- **Stateful Smart Contract Orchestration:** Escrow logic implemented via Solidity, managing vendor registries, milestone tracking, and partial fund disbursements (50/50 splits).
- **Event-Driven AI Pipeline:** A Node.js background worker polls the Sepolia Testnet for `ProofSubmitted` events, triggering the verification pipeline.
- **Vision-Language Model (VLM) Verification:** Utilizes local Ollama (`llava-phi3`) to analyze IPFS-hosted image proofs against task descriptions, returning deterministic JSON verdicts.
- **Decentralized Storage Infrastructure:** Proof of work is uploaded to IPFS, ensuring immutable, decentralized access to verification media.
- **Web3 Dashboard:** React/Vite-based frontend featuring MetaMask integration, multi-role access (Donor/NGO/Vendor), and real-time task feeds.

## Architecture & Workflow

The system operates across three core layers:

1. **On-Chain Execution (Ethereum Sepolia):**
   - The NGO creates a funding milestone and assigns a verified vendor.
   - 50% of the allocated funds are released upfront as an initial payment.
2. **Off-Chain Action & Storage:**
   - The vendor completes the real-world task and uploads photographic proof via the decentralized frontend.
   - The image is pinned to IPFS, and the resulting CID (Hash) is submitted back to the smart contract.
3. **AI Oracle Verification (Backend Worker):**
   - The `aiVerifier` service detects the blockchain event and fetches the image via IPFS gateways.
   - The image and task description are processed by the `llava-phi3` vision model.
   - If the AI approves the proof (`"verdict": "YES"`), the backend submits a transaction to `verifyProof()`, automatically releasing the final 50% of the funds.

## Tech Stack

**AI / Machine Learning**
* Ollama (Local LLM Execution)
* `llava-phi3` (Vision-Language Model)

**Backend / Web3 Orchestration**
* Node.js
* Ethers.js (Blockchain Interaction)
* Axios (IPFS Gateway Retrieval)

**Smart Contracts / Network**
* Solidity `^0.8.27`
* Hardhat
* Ethereum Sepolia Testnet

**Frontend**
* React 19 / Vite
* Web3.js
* MetaMask Integration

**Storage**
* IPFS (InterPlanetary File System)

## Project Structure

```text
my-ngo-app/
├── backend/
│   ├── aiVerifier.js       # Core AI Oracle: Polls blockchain, runs VLM inference, triggers payouts
│   ├── package.json        # Backend dependencies (ethers, axios, dotenv)
│   └── .env                # RPC URLs and AI Verifier private keys
├── src/
│   ├── components/         # React UI components (Dashboard, LandingPage)
│   ├── App.jsx             # Main application routing and Web3 login logic
│   ├── web3Service.js      # Ethers/Web3 configuration and wallet connection utilities
│   └── NGOContract.json    # Compiled Smart Contract ABI
├── NGO.sol                 # Core Solidity Smart Contract
├── package.json            # Frontend Vite/React configuration
└── hardhat.config.cjs      # Hardhat deployment configuration
```

## Installation & Setup

### Prerequisites
- Node.js (v18+)
- [Ollama](https://ollama.com/) installed locally with the `llava-phi3` model (`ollama run llava-phi3`)
- MetaMask browser extension

### 1. Smart Contract (Optional / If Redeploying)
Deploy `NGO.sol` using Remix or Hardhat to the Sepolia Testnet and copy the contract address.

### 2. Backend (AI Verifier)
```bash
cd backend
npm install
```
Create a `.env` file in the `backend/` directory:
```env
RPC_URL=https://ethereum-sepolia-rpc.publicnode.com
PRIVATE_KEY=your_wallet_private_key_here
```
Run the AI Oracle:
```bash
node aiVerifier.js
```

### 3. Frontend
```bash
cd ..
npm install
npm run dev
```

## Challenges Addressed

- **IPFS Gateway Reliability:** Implemented a robust multi-gateway fallback mechanism in the backend to ensure reliable image retrieval despite public gateway rate limits.
- **Deterministic AI Outputs:** Prompt-engineered the VLM to strictly output valid JSON format (`{"verdict": "YES"}`) for reliable programmatic parsing and on-chain execution.
- **RPC Event Filtering:** Migrated from fragile WebSocket event listeners to block polling to ensure consistent event capture across unstable public RPC endpoints.

## Future Improvements

- Transition to a decentralized Oracle network (e.g., Chainlink Functions) for trustless AI execution.
- Implement Zero-Knowledge (ZK) proofs for private vendor verification without exposing sensitive imagery to public IPFS.
- Expand multi-modal support for video proof verification.

## License

MIT License
