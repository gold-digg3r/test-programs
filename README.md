# test-programs
Gold Digger Rust and Anchor Programs

Deploying the updated program to Solana and ensuring the front-end integrates the staking features requires multiple steps. Deploying the program, integrating the front-end, and updating the rewards system for Gold Digger NFTs.

### **Step 1: Deploy the Updated Solana Program**

Before deploying the program, ensure you have the following setup:

* Solana CLI installed: [Solana Docs](https://docs.solana.com/cli/install-solana-cli-tools)
* Anchor framework installed: [Anchor Docs](https://project-serum.github.io/anchor/getting-started/installation.html)

#### **1.1 Build the Program**

Start by building the Solana program.

1. Navigate to the root of your Solana project.
2. Build the program with `Anchor`:

   ```bash
   anchor build
   ```

   This command compiles your Solana program and prepares it for deployment.

#### **1.2 Deploy the Program to Solana**

To deploy the program, you will need to have Solana's CLI configured to the correct network (Devnet, Testnet, or Mainnet). Ensure your Solana wallet is set up with enough SOL to pay for transaction fees.

1. Set the Solana cluster to your desired network (e.g., Devnet):

   ```bash
   solana config set --url devnet
   ```

2. Deploy your Anchor program:

   ```bash
   anchor deploy
   ```

   After the deployment, the CLI will provide you with the program ID (`ProgramId`), which is used to interact with the program.

#### **1.3 Update Environment Variables**

Update the environment variables in your project (`.env` file) to reflect the new program ID:

```bash
NEXT_PUBLIC_SOLANA_PROGRAM_ID=YourProgramID
```

### **Step 2: Update the Front-End to Interact with the Staking Program**

Now that the Solana program is deployed, we need to integrate the front-end to interact with the staking functions.

#### **2.1 Install Required Dependencies**

Ensure the front-end project has the necessary dependencies for interacting with Solana and the staking program:

```bash
npm install @solana/web3.js @solana/wallet-adapter-react @solana/wallet-adapter-react-ui @solana/wallet-adapter-wallets @solana/spl-token
```

#### **2.2 Set Up Wallet Integration**

Use the `@solana/wallet-adapter-react` and `@solana/wallet-adapter-wallets` packages to allow users to connect their Solana wallets (e.g., Phantom, Solflare, Backpack).

```tsx
// In your `App.tsx` or `App.js` file
import { WalletProvider, ConnectionProvider } from '@solana/wallet-adapter-react';
import { PhantomWalletAdapter, SolflareWalletAdapter, BackpackWalletAdapter } from '@solana/wallet-adapter-wallets';
import { WalletMultiButton } from '@solana/wallet-adapter-react-ui';

const App = () => {
    const wallets = [
        new PhantomWalletAdapter(),
        new SolflareWalletAdapter(),
        new BackpackWalletAdapter(),
    ];

    return (
        <ConnectionProvider endpoint="https://api.devnet.solana.com">
            <WalletProvider wallets={wallets} autoConnect>
                <div>
                    <WalletMultiButton />
                    {/* Your App Components */}
                </div>
            </WalletProvider>
        </ConnectionProvider>
    );
};
```

#### **2.3 Integrate Staking Functions**

To stake NFTs and tokens, create functions that interact with the Solana program using the `@solana/web3.js` and `@solana/spl-token` libraries.

Here’s an example of how you can integrate NFT staking:

```tsx
import { useWallet } from '@solana/wallet-adapter-react';
import { PublicKey, SystemProgram, Transaction } from '@solana/web3.js';
import { TOKEN_PROGRAM_ID, Token } from '@solana/spl-token';

const stakeNFT = async (nftMint: PublicKey) => {
    const { publicKey, sendTransaction } = useWallet();
    
    if (!publicKey) {
        alert("Please connect your wallet.");
        return;
    }

    const connection = new Connection("https://api.devnet.solana.com", "confirmed");

    // Fetch NFT token account
    const nftTokenAccount = await Token.getAssociatedTokenAddress(
        ASSOCIATED_TOKEN_PROGRAM_ID,
        TOKEN_PROGRAM_ID,
        nftMint,
        publicKey
    );

    const stakingPool = new PublicKey('YourProgramID'); // Staking program ID
    const userAccount = new PublicKey('UserAccountPublicKey'); // User account PublicKey

    // Create the transaction
    const tx = new Transaction();

    // Add NFT staking instruction
    tx.add(
        // Staking logic here - specify instruction for your custom staking logic
    );

    try {
        // Send the transaction
        const signature = await sendTransaction(tx, connection);
        console.log("Transaction sent:", signature);
    } catch (error) {
        console.error("Error staking NFT:", error);
    }
};
```

Make sure to replace the appropriate placeholders such as `YourProgramID`, `UserAccountPublicKey`, etc.

#### **2.4 Staking and Unstaking Tokens**

Use the same approach to stake and unstake Solana tokens, using the `@solana/spl-token` package to interact with token accounts:

```tsx
const stakeTokens = async (amount: number) => {
    const { publicKey, sendTransaction } = useWallet();
    
    if (!publicKey) {
        alert("Please connect your wallet.");
        return;
    }

    const connection = new Connection("https://api.devnet.solana.com", "confirmed");

    // Token account for staking (replace with actual address)
    const stakingTokenAccount = new PublicKey('YourStakingTokenAccount');
    
    // Your staking logic for tokens
    const tx = new Transaction();
    tx.add(
        // Token transfer instruction to staking pool
    );

    try {
        const signature = await sendTransaction(tx, connection);
        console.log("Transaction sent:", signature);
    } catch (error) {
        console.error("Error staking tokens:", error);
    }
};
```

#### **2.5 Display Staked NFTs and Tokens**

You can display the NFTs and tokens that have been staked by querying the blockchain or by keeping track of staked assets in the user's wallet.

Here’s an example of how you could display a list of staked NFTs:

```tsx
const StakedNFTs = ({ userAccount }) => {
    const [stakedNFTs, setStakedNFTs] = useState([]);

    useEffect(() => {
        // Fetch the list of staked NFTs from the userAccount
        fetchStakedNFTs(userAccount);
    }, [userAccount]);

    const fetchStakedNFTs = async (userAccount) => {
        // Call to Solana program to fetch staked NFTs
        // Use `userAccount.staked_nfts` from the program state
    };

    return (
        <div>
            <h3>Staked NFTs</h3>
            <ul>
                {stakedNFTs.map((nft, index) => (
                    <li key={index}>
                        {/* Render NFT details */}
                    </li>
                ))}
            </ul>
        </div>
    );
};
```

### **Step 3: Update the Rewards Distribution and Rarity System**

The **NFT rarity-based reward multiplier** can be updated based on the metadata or other criteria.

For example, if you use `Metaplex` to mint NFTs, you can fetch the metadata and check the rarity:

```tsx
const getNFTRarity = async (nftMint) => {
    const metadataUrl = `https://api.metaplex.com/metadata/${nftMint.toBase58()}`;
    const response = await fetch(metadataUrl);
    const metadata = await response.json();
    
    // Example: Check metadata for rarity
    return metadata?.properties?.rarity;
};
```

Based on the fetched rarity, you can update the reward multiplier and adjust the rewards accordingly.

### **Step 4: Test the Staking System**

Make sure to thoroughly test the staking system before going live:

* Check the staking and unstaking functions to ensure that the user can correctly stake and unstake NFTs and tokens.
* Verify that rewards are calculated properly and that the front-end correctly reflects the staked assets.

### **Step 5: Deploy and Monitor**

After testing, deploy the front-end and ensure that it is properly integrated with your deployed Solana staking program. You can deploy the front-end to platforms like Vercel, Netlify, or your preferred hosting provider.

