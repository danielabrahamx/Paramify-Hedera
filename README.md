# Paramify: Decentralized Flood Insurance on Hedera

![alt text](image.png)

---

## **Project Title & Track**

**Paramify: Decentralized Flood Insurance Platform**  
**Track:** Real World Assets (RWA)

### Pitch Deck & Certification
- **Pitch Deck:** [View on Canva](https://www.canva.com/design/DAG3MqV19Lo/8k4qNlvMrjcGshvr6sMyzg/edit?utm_content=DAG3MqV19Lo&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)
- **Demo Video:** [Watch on YouTube](https://www.youtube.com/watch?v=_TT9YZo9st0)

---

## Overview

**Paramify** is a decentralized flood insurance platform built on Hedera Testnet, demonstrating automated insurance purchases and payouts triggered by real-world flood level data via oracle integration. This system showcases a smart contract (`Paramify.sol`) that allows users to buy flood insurance policies and claim payouts when flood levels exceed a predefined threshold, with role-based access control for secure administration.

Paramify demonstrates the viability of decentralized parametric insurance on Hedera's high-throughput, low-cost network. The architecture leverages Hedera's predictable fees and ABFT finality to ensure reliable, cost-effective insurance operations suitable for at-risk communities. This README provides instructions to set up, deploy, and demo the PoC locally on Hedera Testnet, along with steps to test key features.

---

## Hedera Integration Summary

### Why Hedera?

**Hedera Consensus Service & Smart Contracts (HCS + HEV):**  
We chose Hedera's EVM-compatible smart contracts layer combined with the Consensus Service for its predictable transaction fee and **ABFT finality**. This guarantees operational cost stability essential for parametric insurance platforms serving communities in Africa and other regions with thin margins. Traditional blockchain platforms (e.g., Ethereum) charge volatile gas fees that can fluctuate 10-100x, making financial projections impossible for insurance providers. Hedera's deterministic pricing allows us to build sustainable business models where insurance premiums can be reliably calculated and users experience predictable transaction costs.

**Transaction Types Executed:**
- **Smart Contract Deployment**: Creation of `Paramify.sol` on Hedera EVM 
- **Policy Purchases**: Users call `buyInsurance()` to create flood insurance policies 
- **Payout Triggering**: Call `checkAndPayout()` when flood levels exceed threshold
- **Oracle Updates**: Backend calls contract's payout function to update flood levels via oracle role 
- **Admin Operations**: Role-based setters for thresholds and contract parameters (minimal, on-demand)

**Economic Justification:**  
In regions with limited capital (e.g., small farmers in East Africa managing flooding risk), a $0.10 insurance premium is meaningful. On Ethereum, gas fees alone can consume 5-50% of small premiums, leaving nothing for actual coverage. Hedera's fixed fees ensure:
- **Cost Predictability**: Customers know exact transaction costs upfront
- **Scalability**: Hundreds of policies can be processed per second with predictable $0.0001 cost
- **Accessibility**: Even micro-payments for insurance become viable
- **Finality**: ABFT finality (~7 seconds) provides immediate confirmation, critical for emergency payout scenarios

---

### Features
- **Insurance Purchase**: Users buy policies by paying a premium (10% of coverage), e.g., 0.1 HBAR for 1 HBAR coverage.
- **Automated Payouts**: Payouts are triggered when the flood level exceeds 3 feet, sending coverage to the policyholder.
- **Real-Time Flood Data**: Backend server fetches live USGS water level data every 5 minutes and updates the blockchain oracle.
- **Role-Based Access**: Admins manage the contract, oracle updaters set flood levels, and insurance admins configure parameters.
- **Frontend Interface**: A React-based UI allows users to connect wallets, buy insurance, view flood levels, and trigger payouts.
- **Backend API**: Node.js server provides real-time flood data integration with automatic oracle updates.

---

## Prerequisites

- **Node.js**: Version 18.x or 23.x (tested with 23.9.0).
- **MetaMask**: Browser extension for wallet interactions.
- **Hedera Account**: Testnet HBAR for gas fees (faucet: https://portal.hedera.com/faucet)
- **Git**: To clone the repository.
- **Hardhat**: For contract deployment and testing.

---

## Quick Start on Hedera Testnet

### Setup in Under 10 Minutes

**Prerequisites:**
1. Get Testnet HBAR from faucet: https://portal.hedera.com/faucet
2. Add MetaMask to your browser
3. Have Node.js 18+ and Git installed

**Steps:**

```bash
# 1. Clone and install
git clone https://github.com/danielabrahamx/paramify-hedera.git
cd paramify-hedera
npm install

# 2. Configure environment
# Add your Hedera Testnet account credentials to .env:
# HEDERA_OPERATOR_KEY=your_private_key_here

# 3. Deploy contracts to Hedera Testnet
npx hardhat run scripts/deploy.js --network hederaTestnet

# 4. Fund the contract with HBAR
npx hardhat run scripts/fund-contract.js --network hederaTestnet

# 5. Start backend server
cd backend
npm start

# 6. Start frontend (from another terminal)
cd frontend
npm run dev
```

**Expected Running Environment:**
- **Frontend**: http://localhost:8080 (Vite dev server)
- **Backend**: http://localhost:3001 (Express server with USGS data integration)
- **Smart Contract**: Deployed on Hedera Testnet (Chain ID: 296)

---

## Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│                 PARAMIFY SYSTEM ARCHITECTURE                     │
└──────────────────────────────────────────────────────────────────┘

┌─────────────────────────┐
│   USER BROWSER          │
│  ┌───────────────────┐  │
│  │ React Frontend    │  │  ◄────────► http://localhost:8080
│  │ (Vite Dev)        │  │
│  └───────────────────┘  │
└──────────────┬──────────┘
               │ (MetaMask / ethers.js)
               │
               ▼
    ┌─────────────────────────────┐
    │   MetaMask Wallet Connector │ (Hedera Testnet - Chain 296)
    └──────────────┬──────────────┘
                   │
                   │ (Smart Contract Calls)
                   │
                   ▼
        ╔═════════════════════════════════════════════════╗
        ║      HEDERA TESTNET (CHAIN ID: 296)            ║
        ║                                                 ║
        ║  ┌───────────────────────────────────────────┐ ║
        ║  │   Paramify.sol Smart Contract            │ ║
        ║  │   - buyInsurance()                        │ ║
        ║  │   - checkAndPayout()                      │ ║
        ║  │   - Role-Based Access Control            │ ║
        ║  │                                           │ ║
        ║  │   Key Functions:                          │ ║
        ║  │   • buyInsurance(coverage, premium)       │ ║
        ║  │   • checkAndPayout()                      │ ║
        ║  │   • setFloodThreshold(level)              │ ║
        ║  └───────────────────────────────────────────┘ ║
        ║                                                 ║
        ║  (~$0.0001 HBAR per transaction)                ║
        ║                                                 ║
        ╚═════════════════════════════════════════════════╝
                   ▲                    ▲
                   │ (JSON-RPC)         │ (Oracle Updates)
                   │                    │
    ┌──────────────┴────────┐  ┌────────┴─────────────┐
    │  Hedera RPC Endpoint  │  │ USGS Data Oracle     │
    │  (hashio.io)          │  │ (via Backend Server) │
    └─────────┬─────────────┘  └────────┬─────────────┘
              │ (Real-time data)        │
              │                         │
              ▼                         ▼
    ┌─────────────────────────┐ ┌──────────────────────┐
    │ Hedera Mirror Node      │ │ Backend Server       │
    │ (Block Explorer)        │ │ (Node.js/Express)    │
    │ hashscan.io             │ │                      │
    └─────────────────────────┘ │ ┌────────────────┐  │
                                │ │ USGS API Client│  │
                                │ │ (Flood Levels) │  │
                                │ └────────────────┘  │
                                │                      │
                                │ http://localhost:3001
                                └──────────────────────┘

═══════════════════════════════════════════════════════════════════

DATA FLOW:

1. User connects MetaMask → Frontend (Hedera Testnet)
2. User buys insurance → Frontend calls buyInsurance() on contract
3. Contract executes on Hedera → $0.0001 fee deducted
4. Backend periodically fetches USGS flood data
5. Backend calls checkAndPayout() when levels exceed threshold
6. Payout executed on Hedera → $0.0001 fee deducted
7. User receives coverage (HBAR) in wallet
8. Frontend updates UI with real-time flood levels
```

---

## Deployment & Setup Instructions

### Deployed Hedera IDs

All deployments are on **Hedera Testnet (Chain ID: 296)**. Update these in `frontend/src/lib/networkConfig.ts` and `backend/.env` after deployment.

#### Current Testnet Deployment

| Component | ID | Link |
|-----------|-----|------|
| **Paramify Smart Contract** | `0x8588fF70697EE423f8aF94Dc2CB33068406A6729` | [HashScan](https://hashscan.io/testnet/contract/0x8588fF70697EE423f8aF94Dc2CB33068406A6729) |
| **Mock Oracle Contract** | `0x1e2cF257676561e2088Abc3777bc1f011b450013` | [HashScan](https://hashscan.io/testnet/contract/0x1e2cF257676561e2088Abc3777bc1f011b450013) |
| **Network Name** | Hedera Testnet | - |
| **Chain ID** | 296 | - |
| **RPC URL** | https://testnet.hashio.io/api | - |
| **Block Explorer** | https://hashscan.io/testnet | - |

#### Deployer & Test Accounts

**PLACEHOLDER: INSERT TESTNET ACCOUNT DETAILS HERE**
- **Deployer Address**: [PLACEHOLDER: YOUR_DEPLOYER_ADDRESS]
- **Test User Address**: [PLACEHOLDER: YOUR_TEST_USER_ADDRESS]

---

## Security & Secrets

⚠️ **CRITICAL: Never commit private keys or sensitive credentials to GitHub.**

### Environment Configuration

Create a `.env` file in the root directory (do NOT commit):

```bash
# .env (NEVER COMMIT THIS FILE - add to .gitignore)
HEDERA_OPERATOR_KEY=your_private_key_here
PARAMIFY_ADDRESS=0x8588fF70697EE423f8aF94Dc2CB33068406A6729
MOCK_ORACLE_ADDRESS=0x1e2cF257676561e2088Abc3777bc1f011b450013
RPC_URL=https://testnet.hashio.io/api
PORT=3001
```

### Example Configuration

Refer to `.env.example` for the structure of required environment variables (safe to commit):

```bash
# .env.example (SAFE TO COMMIT - no real secrets)
PORT=3001
PARAMIFY_ADDRESS=your_contract_address_here
MOCK_ORACLE_ADDRESS=your_oracle_address_here
RPC_URL=https://testnet.hashio.io/api
HEDERA_OPERATOR_KEY=your_operator_key_here
```

### Judge Credentials

**For DoraHacks/Hackathon Judges:**

To verify the deployment and test the system:

1. **Contract Addresses** (on Hedera Testnet):
   - Paramify: `0x8588fF70697EE423f8aF94Dc2CB33068406A6729`
   - Mock Oracle: `0x1e2cF257676561e2088Abc3777bc1f011b450013`

2. **To Test**:
   ```bash
   # 1. Import credentials into MetaMask on Hedera Testnet
   # 2. Visit http://localhost:8080
   # 3. Buy insurance and verify transaction on HashScan
   # 4. Check payout when flood levels exceed threshold
   ```

---

## Live Flood Data Monitoring

The Paramify system includes real-time flood level monitoring:

- **Backend Terminal**: Shows timestamped flood level updates every 5 minutes (e.g., "Latest water level: 4.11 ft at 2025-06-25T04:45:00.000-04:00")
- **Frontend Dashboard**: Displays current flood levels in feet with automatic updates
- **API Endpoint**: Access flood data at `http://localhost:3001/api/flood-data`
- **Blockchain Oracle**: Automatically updated with scaled flood values for smart contract integration

---

## Demo Instructions

### 1. Setup MetaMask for Hedera Testnet

1. Open MetaMask and click "Add Network Manually"
2. Enter:
   - **Network Name**: Hedera Testnet
   - **RPC URL**: https://testnet.hashio.io/api
   - **Chain ID**: 296
   - **Currency Symbol**: HBAR
   - **Block Explorer**: https://hashscan.io/testnet
3. Switch to Hedera Testnet network
4. Get Testnet HBAR from faucet: https://portal.hedera.com/faucet

### 2. Buy Insurance (User Flow)

- Navigate to http://localhost:8080
- Click "Connect Wallet" and approve MetaMask connection
- Enter coverage amount (e.g., `0.5` HBAR)
- Click "Buy Insurance" and approve in MetaMask
- **Expected Result**: Premium charged (10% of coverage), policy marked Active, transaction visible on HashScan

### 3. Trigger Payout (When Flood Level Exceeds Threshold)

- Backend automatically monitors USGS flood data
- When flood level > threshold (default: 3 feet), backend calls `checkAndPayout()`
- User receives payout amount in their wallet
- Transaction visible on [HashScan Testnet Explorer](https://hashscan.io/testnet)

---

## Testing

Run unit tests to verify contract functionality:

```bash
npx hardhat test
```

Tests cover:
- Policy creation and validation
- Payout triggering above/below threshold
- Role-based access control
- Contract funding and withdrawal

To verify the current state on Hedera Testnet:

```bash
npx hardhat run scripts/check-policy.js --network hederaTestnet
```

---

## Project Structure

```
paramify/
├── contracts/
│   ├── Paramify.sol                    # Main insurance smart contract
│   └── mocks/
│       └── MockV3Aggregator.sol        # Mock oracle for testing
├── backend/
│   ├── server.js                       # Node.js/Express backend with USGS integration
│   ├── package.json                    # Backend dependencies
│   └── .env                            # Backend configuration (DO NOT COMMIT)
├── frontend/
│   ├── src/
│   │   ├── InsuracleDashboard.tsx      # User dashboard component
│   │   ├── InsuracleDashboardAdmin.tsx # Admin dashboard component
│   │   ├── lib/
│   │   │   ├── contract.ts            # Contract addresses and ABIs
│   │   │   ├── networkConfig.ts       # Hedera network configuration
│   │   │   └── networkUtils.ts        # Network utility functions
│   │   └── components/ui/             # shadcn/ui components
│   ├── package.json                    # Frontend dependencies
│   └── vite.config.ts                  # Vite configuration
├── scripts/
│   ├── deploy.js                       # Deploy contracts to Hedera
│   ├── fund-contract.js                # Fund contract with HBAR
│   ├── check-policy.js                 # Check policy and balances
│   └── deployMock.js                   # Deploy mock oracle
├── test/
│   ├── Paramify.js                     # Contract unit tests
│   └── Paramify-buyInsurance.test.js   # Insurance flow tests
├── hardhat.config.js                   # Hardhat configuration for Hedera
├── package.json                        # Root dependencies
├── paramify-abi.json                   # Compiled contract ABI
├── mock-aggregator-abi.json            # Oracle ABI
└── README.md                           # This file
```

---

## Code Quality & Auditability

### Clean Code Standards

- **Function Names**: Clear, descriptive names (e.g., `buyInsurance`, `checkAndPayout`)
- **Consistent Styling**: ESLint configured for TypeScript and JavaScript
- **Inline Comments**: Complex logic documented for judges
- **Type Safety**: Full TypeScript support in frontend and tests

### Running Linters

```bash
# Lint frontend code
cd frontend
npm run lint

# Fix linting issues
npm run lint -- --fix
```

### Verification

All core logic files are auditable:
- **Smart Contracts**: `contracts/Paramify.sol` - Clean, role-based access
- **Frontend Logic**: `frontend/src/lib/contract.ts` - Clear contract interaction
- **Backend Logic**: `backend/server.js` - Transparent USGS data fetching

---

## Security and Dependencies

**Dependencies**:
- `@openzeppelin/contracts@5.0.2` - For AccessControl
- `@chainlink/contracts@1.2.0` - For AggregatorV3Interface
- `ethers@v6` - For blockchain interactions
- `hardhat` & `@nomicfoundation/hardhat-toolbox` - Development tools

**Vulnerability Check**:
```bash
npm audit fix
npm audit
```
Address any high-severity issues before deployment.

---

## Future Enhancements

- **Additional EVM Networks**: Deploy on Polygon, Avalanche, or other Hedera-compatible chains
- **Real Oracle Data**: Replace MockV3Aggregator with live Chainlink flood level feeds
- **Multi-Policy Support**: Allow users to hold multiple simultaneous policies
- **Enhanced Frontend**: Mobile responsiveness, custom branding, improved UX
- **Advanced Analytics**: Dashboard for insurance providers to track claims and payouts
- **Tokenized Insurance**: Support for insurance as transferable NFTs

---

## Troubleshooting

### Backend Server Issues

- **Server not starting**:
  ```bash
  # Ensure Node.js is installed
  npm install
  cd backend && npm start
  ```
  
- **Port 3001 in use**:
  ```bash
  # Windows: Find and kill process
  netstat -ano | findstr :3001
  taskkill /PID <PID> /F
  
  # Mac/Linux:
  lsof -i :3001
  kill -9 <PID>
  ```

### Frontend Issues

- **Frontend not loading**:
  ```bash
  cd frontend
  npm install
  npm run dev
  ```

- **Port 8080 in use**: See backend troubleshooting above

### Hedera Testnet Issues

- **MetaMask network not found**:
  - Re-add Hedera Testnet manually in MetaMask
  - RPC: https://testnet.hashio.io/api, Chain ID: 296

- **No HBAR in wallet**:
  - Get testnet HBAR from: https://portal.hedera.com/faucet

- **Contract deployment fails**:
  - Verify HEDERA_OPERATOR_KEY in `.env`
  - Check account has sufficient HBAR

---

## License

MIT License. See [LICENSE](./LICENSE) for details.

---

*Hackathon Submission - Paramify: Decentralized Flood Insurance on Hedera Testnet*  
*Built with Hedera's predictable $0.0001 fees and ABFT finality for sustainable, global insurance access.*
