# NFT ERC721 Collection with partial free mint option

A fork of the [hashlips ERC721 collection](https://github.com/hashlips-lab/nft-erc721-collection) repo modified public sale option to include a hybrid of free and paid mints.

## Disclaimer

This project was created for educational purposes, please refer to the [LICENCE](LICENSE) file for further information.

## Main features

A public sale with free and paid mints. When the free mint supply is exhausted the contract switches to paid mints.

## Requirements

### Software

Please refer to the [hashlips ERC721 collection](https://github.com/hashlips-lab/nft-erc721-collection) for excellent video's.

### Services

Please refer to the [hashlips ERC721 collection](https://github.com/hashlips-lab/nft-erc721-collection) for excellent video's.

### Partial Free Mint Setup

This project follows the hashlips ERC721 collection set-up and deployment process for a seamless free mint to paid mint set-up.

Before deploying the contract update `CollectionFonfig.ts`

|Step|Description|Field to update|
|---|---|---|
|`01`|Set the free mint supply|`maxFreeMintSupply: 10`|
|`02`|Set the mint cost to be used after free mints are exhausted| `publicSale: {`**`price: 0.0042069,`**|
|`03`|Set the maximum mints per transaction to be used after free mints are exhausted| `publicSale: {` **`maxMintAmountPerTx: 2,`**|

#### Deploy the contract with partial free mints set-up, **in this order:**

|Step|Description|
|---|---|
|`01`|Open the public sale, run script: `public-sale-open`|
|`02`|Head over to your contract on etherscan|
|`03`|Set the number of max free mints per transaction using function: `setMaxFreeMintAmountPerTxn`|
|`04`|Active the free mint option using function: `setHasFreeMint` to set `_state` to `true`|

The contract is now setup for partial free mints. When the free mint supply is exhausted the contract will switch to the paid mint model

For a detailed start to finish set-up, deploy, and demo checkout [the you tube video located at: https://www.youtube.com/watch?v=NizcL16A1co](https://www.youtube.com/watch?v=NizcL16A1co)

### After downloading the repo, to test locally

1. Ensure you are familiar with the  [hashlips ERC721 collection](https://github.com/hashlips-lab/nft-erc721-collection) repo install and startup process

1. Ensure both the contract and DAPP are compiled
   1. Open a Terminal at the smart contract node
      1. in the terminal enter: `npx hardhat compile`
   2. open a terminal at the DAPP node
      1. in the terminal enter: `npm run build`
1. in the smart contract terminal enter: `truffle dashboard`
1. go to the browser that the dash board has opened
   1. connect to your wallet
   1. Click the 'Confirm' Button
1. in the DAPP terminal enter: `yarn dev-server`
   1. when it completes (webpack complied successfully)
      - go up a few lines. Find the text `<i> [webpack-dev-server] Loopback: <http://localhost:8080/>, http://[::1]:8080/`

      - Click on the link to <http://localhost:8080/>

      OR

      - Open a new browser tab and enter `http://localhost:8080`

## 11-09-2025 - Upgraded Node and npm

<details>
<summary>Upgrade and build sequence — step-by-step</summary>

1. Check current Node and npm versions (why: confirm environment)
   - What: Verify Node.js and npm versions before changing anything.
   - Why: Make sure we know the starting state and confirm compatibility decisions.

      `node -v`

      `npm -v`

1. Back up existing lockfiles (why: preserve reproducible installs)
    - What: Copy package-lock.json files before making dependency changes.
    - Why: Allows rolling back to previous lockfiles if something breaks.
    - Command (PowerShell):

    Copy-Item package-lock.json package-lock.json.backup

    Run in each subproject if they have lockfiles, e.g

        `Copy-Item smart-contract\package-lock.json smart-contract\package-lock.json.backup`

        `Copy-Item minting-dapp\package-lock.json minting-dapp\package-lock.json.backup`

1. Upgrade npm (if required) (why: use newer npm features & avoid deprecation warnings)
   - What: Upgrade npm to a more recent version (we upgraded from 8.x to 9.x).
   - Why: Some operations and dependency resolutions are improved in newer npm.
   - Command (PowerShell):

      `npm install -g npm@latest`

      `npm -v`

1. Decide Node.js strategy (why: Hardhat compatibility)
   - What: We had Node v20 installed but Hardhat in the repo initially required ^12/14/16. We - chose to upgrade Hardhat and related dev packages to be compatible with Node 20 rather than downgrading Node.
   - Why: Upgrading dependencies is safer for local environment parity and for taking advantage of newer Node features; downgrading Node can break other global tools.

1. Update package.json devDependencies for Node 20 compatibility (why: resolve Hardhat engine mismatch)
    - What: Update Hardhat and Hardhat-related packages to newer versions that support Node 20 (and bump TypeScript/TypeChain/OpenZeppelin versions accordingly).
    - Why: Hardhat warned about unsupported Node version; upgrading Hardhat and related packages lets us compile on Node 20.
    - Note: This is an edit to package.json. Example changed packages: hardhat, @typechain/hardhat, typechain, ethers (minting-dapp\sr\scripts\react\Dapp.txt), @openzeppelin/contracts, typescript, @types/node, eslint, etc.
    - No direct command (this is a package.json edit). After editing, see install commands below.

1. Clean the install and install updated dependencies (why: ensure a fresh dependency tree)
   - What: Remove node_modules and package-lock.json (in the smart-contract folder), then reinstall.
   - Why: Ensures npm resolves to versions compatible with the updated package.json.
   Commands (PowerShell):

     `cd .\smart-contract`

     `Remove-Item -Recurse -Force node_modules`

     `Remove-Item package-lock.json`

     `npm install --legacy-peer-deps`

   - Notes:
       - We used --legacy-peer-deps to bypass strict peer dependency resolution failures (safer to do this for older repos). If you prefer, resolve peers manually instead.
       - You may see many deprecation warnings and security notices; these do not always block the install but should be reviewed.

1. Install frontend dependencies (minting dapp) (why: required to compile the dapp)
   - What: Install the minting-dapp dependencies.
   - Commands (PowerShell)

     `cd ..\minting-dapp`

     `npm install`

   - Result: Dependencies installed for building the frontend.

1. Compile smart contracts with Hardhat (why: generate bytecode + TypeChain types)
   - What: Compile the solidity contracts and generate TypeScript typings.
   - Why: Needed for tests and for the frontend to interact with contract artifacts.
   - Commands (PowerShell)

      `cd ..\smart-contract`

      `npx hardhat compile --show-stack-traces`

   - Notes:
     - If Hardhat complains about unsupported Node versions, update Hardhat per step 5.
     - If it cannot download Solidity compilers, ensure your internet connection and try again; Hardhat downloads specific solc versions.

1. Run smart-contract tests (why: verify contract behavior)
   - What: Execute the test suite to confirm behavior.
   - Why: Catch regressions after dependency upgrades.

      `npx hardhat test`

   - Results we observed:
      - The compile succeeded and type definitions were generated, but several tests failed. (This indicates either test assumptions or contract logic need adjusting.)
   - Next action: Investigate and fix failing tests (see troubleshooting section).

1. Fix TypeScript build errors in the frontend (why: ensure the dapp compiles)
   - What: The frontend initially failed to build with a TS error when assigning an ethers.Contract (minting-dapp\node-modules\ethers\src.ts\ethers.ts) to the project's typed contract (NftContractType (smart-contract\typechain\NotoriousRugs.d.ts)).
   - Change made: Use a safe cast through unknown:
   - Example (file): Dapp.tsx (minting-dap\src\scripts\react\Dapp.tsx)
     - Replace casting line:
         - from: ) as NftContractType;
         - to: ) as unknown as NftContractType;
   - Why: TypeScript structural typing complained the raw Contract (minting-dapp\node-modules\@ethersproject\contracts\src.ts\index.ts) didn’t match the generated NotoriousRugs contract type; casting via unknown is a minimal safe fix to allow build without reworking typings.

1. Build the minting dapp (production) (why: produce static assets to deploy)
   - What: Run Webpack Encore to produce compiled assets in build.
   - Commands (PowerShell):

     `cd ..\minting-dapp`

     `npm run build`

   - Result: Build completed successfully; output written to build:
     - entrypoints.json
     - manifest.json
     - main.css
     - main.js
     - main.js.LICENSE.txt
     - images/

   - ls .\public\build

1. Serve the built dapp locally for a smoke check (why: quick validation)
   - What: Serve static public to check the app loads in browser.
   - What: Serve static public to check the app loads in browser.
   - Commands (PowerShell examples):

    Using npx http-server

     `cd .\minting-dapp\public`

     `npx http-server -p 8080`

     Or, if Python 3 is available

     `python -m http.server 8080`

   - Then open: <http://localhost:8080>

1. Commit the minimal source change (optional) (why: persist the TS fix)
   - What: If the TypeScript cast fix is acceptable, commit Dapp.tsx change so other devs can build locally.
   - Commands (PowerShell/git):

     `git add minting-dapp\src\scripts\react\Dapp.tsx`

     `git commit -m "fix(typescript): cast ethers.Contract to NftContractType via unknown to satisfy TS check"`

     `git push`

1. Optional: Audit and fix vulnerabilities (why: reduce security risks)
   - What: Run npm audit and consider fixes.
   - Commands:

     `npm audit`

     `npm audit fix`

     To attempt full fixes (may introduce breaking changes)

      `npm audit fix --force`

   - Note: Use --force cautiously; it can upgrade packages in ways that break the build. Prefer manual dependency updates if required.

Troubleshooting & notes

- Hardhat engine errors (Node too new)
  - Fix: Upgrade Hardhat and relevant dev packages (step 5) or temporarily use Node 16 via nvm to run Hardhat.
- Dependency resolution errors (peer deps)
  - Fix: Use npm install --legacy-peer-deps to bypass strict peer resolution. Alternatively, upgrade or pin the peer dependencies.
- Compiler download failures (Hardhat HH502)
  - Cause: Temporary network or firewall blocking; Hardhat downloads specific solc versions.
  - Fix: Check internet connectivity, proxy/firewall settings, and retry. Running with --show-stack-traces helps surface details.
- TypeScript contract typing mismatch
  - Cause: ethers.Contract (minting-dapp\node-modules\ethers\src.ts\ethers.ts)  does not structurally match generated TypeChain contract types.
  - Fixes:
    - Minimal: cast with as unknown as NftContractType (smart-contract\typechain\NotoriousRugs.d.ts) (we applied this).
    - Better: import TypeChain-generated factory usage or generated types directly and create typed contract instances via factories (safer long-term).
- Failing smart-contract tests after upgrades
  - Cause: Tests assume certain contract state or behavior; changes to contract code or environment can surface different behavior.
  - Fix: Reproduce failing tests, inspect the revert reasons or assertions, and either update tests or adjust contracts.

Short checklist to re-run everything (copy/paste)

   1. Smart-contract clean install & compile

      `cd .\smart-contract`

      `Remove-Item -Recurse -Force node_modules`

      `Remove-Item package-lock.json`

      `npm install --legacy-peer-deps`

      `npx hardhat compile --show-stack-traces`

      `npx hardhat test`

   1. Frontend build

      `cd ..\minting-dapp`

      `npm install`

      `npm run build`

   1. Serve for manual check

      `cd .\minting-dapp\public`

      `npx http-server -p 8080`

      Open <http://localhost:8080>

</details>
