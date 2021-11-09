# Creating your Cryptocurrency using Javascript

Welcome quest masters. In this quest, you will be creating your first own token on Solana using Javascript by creating a web application. Solana is the fastest blockchain in the world which boasts sub-second transaction speeds.

By the end of this quest, you will have your very own cryptocurrency over the Solana blockchain. It is like creating your own digital currency. The possibilities are endless on how to use these tokens. You can share these with your friends or use these tokens to raise funds for your next startup.

A prerequisite for the quest is knowing the basics of React (a JS frontend framework). 

# Initial Setup

Moving forward, you need to have a working installation of node and npm (Node Package Manager) is used to manage packages. Having a code editor like VS Code will be very helpful during the development of the application. 

We will be calling our application solCurrency. Open the terminal from your desired folder location and write down the following command:

```
npx create-react-app solCurrency
```

After sometime, a whole react-boilerplate project is created which can be run locally using the command:
```
npm start
```

A new tab in your default browser will open with the following webpage.

![1](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/1.png)

Congratulations. You have successfully created your first react-app.

Normally, like in other programming languages we have classes and functions, in React we have two types of components , Functional and Class-Based. In this quest, we will be only creating functional components.

Delete all the files in the src folder now. After deleting, create a file in src/ called index.js. Copy these lines into the new file:
```
import React from 'react'
import ReactDOM from "react-dom";
 
import App from "./App";
 
ReactDOM.render(<App />,document.getElementById('root'));
```
Create another file called App.js in the same folder . Put in these code:
```
import React from 'react'
 
const App = () => {
   return (
       <div>
           <h1>Create your own token using JavaScript</h1>
       </div>
   )
};
 
export default App;
```

![2](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/2.png)

For creating our product, we’ll be using two packages. Please install the mentioned two npm packages, using the following command.
```
npm i --save @solana/web3.js @solana/spl-token
```
Solana web3.js will be used for interacting and minting our new 
The @solana/web3.js is a typescript library that can be installed using npm. This library is developed by Solana labs itself to perform common interactions with the Solana blockchain using only Typescript.
The spl-token library is also a typescript library by Solaba Labs to perform the most common operations on the Solana blockchain related to spl tokens using Typescript.

# Installing Phantom Wallet 

Like your personal wallets, there are various software wallets which can hold your tokens. We will be using a crypto wallet called phantom wallet in this quest. 

To add Phantom wallet to your browser, visit https://phantom.app/ and click on “Add to Chrome/Firefox”. Once the extension is installed, click on “Create a new wallet”. It’ll ask you to create a new password for your Phantom wallet extension and ask you to copy the “seed phrase”. Store this seed phrase somewhere safe and never share this seed phrase with anyone. 
This seed phrase is a string that is used to give full access to sending and receiving tokens from your wallet. In case you change your browser or laptop, you can use this seed phrase to recover your original wallet. Anyone who has your seed phrase, can access your wallet and then perform transactions through it - so keep it safe!

Every wallet on the Solana blockchain has a unique identifier, called the public key (shortened as pubkey) which is a cryptographic string of alpha-numeric characters. This pubkey can be universally shared with anyone and acts as a “receiving address” for your wallet. To check the public key and the balance of our newly created wallet, use the following commands.

The original Solana blockchain where the actual transactions happen is known as the mainnet. To perform any transaction on the Solana blockchain, you’re charged a transaction fee. These payments happen in a currency called SOL. Solana also maintains clusters called devnet and testnet. Devnet is the replica of the Solana’s mainnet, and serves as a playground for anyone who wants to try out the features of Solana. Cryptos that reside in devnet don’t have any value.
Since in this quest we are transacting in devnet, we can change the network in Phantom to devnet. To do so, click on the cog wheel in the bottom right corner of your Phantom wallet, click on “Change network” and select “Devnet”.

![3](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/3.png) ![4](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/4.png)

You can find out the wallet address of your Phantom wallet by clicking on the wallet’s name. This wallet address is the public address of your wallet that can be used to receive funds.

# Connecting with Phantom Wallet

Let’s add a “connect to wallet” functionality to our application, that’ll allow your users to connect to solCurrency through their Phantom wallets.
We’ll be creating a useState hook to store information regarding whether the wallet is connected or not, and we’ll be creating another state (called provider) to store the wallet info with which the user logs in. A loading state is also created.
```
const [walletConnected,setWalletConnected]=useState(false);
const [provider, setProvider] = useState();
const [loading, setLoading] = useState();
```
By default, the wallet is disconnected. Now, using this state, to show buttons and public key on the return statement:
```
{
    walletConnected?(
<p><strong>Public Key:</strong> {provider.publicKey.toString()}</p>                   
    ):<p></p>
 }
 
 <button onClick={walletConnectionHelper} disabled={loading}>
{!walletConnected?"Connect Wallet":"Disconnect Wallet"}
 </button> 
```
If the wallet is successfully connected, the publicKey string of the wallet is displayed on the screen. A button to connect or disconnect from the wallet is also available and based on the state of walletConnected, the content of the button is displayed.
A function of walletConnectionHelper is used to do the desired action. Another function which is used to connect to the phantom wallet is created whose name is getProvider
```
const getProvider = async () => {
       if ("solana" in window) {
         const provider = window.solana;
         if (provider.isPhantom) {
           return provider;
         }
       } else {
         window.open("https://www.phantom.app/", "_blank");
       }
   };
 
   const walletConnectionHelper=async ()=>{
       if(walletConnected){
           //Disconnect Wallet
           setProvider()
           setWalletConnected(false)
       }else{
           const userWallet=await getProvider();
           if(userWallet){
               await userWallet.connect();
               userWallet.on("connect",async ()=>{
                   setProvider(userWallet);
                   setWalletConnected(true);
               })
           }
       }
   }
```
The “walletConnectionHelper” checks whether the wallet is connected or not using the state. If connected, then it updates the state of “walletConnected” and removes the wallet info stored in the provider state. If the wallet is not connected (in the else block), the user wallet instance is fetched from the function “getProvider”. 
Due to installing @solana/web3.js , we now have access to the “solana” variable globally in the window. So, it fetches the wallet info from the global variable or opens the phantom wallet instance. After getting the instance from the getProvider function, on successful connection, the wallet is stored in the provider state. 

![5](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/5.png)

And now, you can connect to your wallet and get your Public key address on the screen like this. 

# Sending yourself some SOL

To perform any kind of action on the blockchain, we are charged some fees. For the Solana blockchain, the fees are paid in Solana’s native currency called SOL.
Since we’ll be performing multiple transactions while developing our application, we’ll perform all the transactions over the devnet.
Cryptos that reside in devnet don’t have any value, and hence we can transfer some “toy SOL” to our wallet for performing any transaction that we want. This process of receiving some crypto currency without paying for it is known as “airdrop”.

A function called “airDropHelper” helps transfer SOL to our wallet after creation. It will also require us to import certain things from the @solana/web3.js package
The import statement:
```
import { Connection,clusterApiUrl, PublicKey, LAMPORTS_PER_SOL } from '@solana/web3.js';

```
We will be creating a button on the UI to airDrop 1 SOL and it will only be displayed when the wallet is connected
```
 {
   walletConnected ? (
    <p>Airdrop 1 SOL into your wallet 
<button disabled={loading} onClick={airDropHelper}>AirDrop SOL </button>
    </p>):<></>
}
```

```
const airDropHelper=async ()=>{
   try{
       setLoading(true);
       const connection = new Connection(
           clusterApiUrl("devnet"),
           "confirmed"
       );
       var fromAirDropSignature=await connection.requestAirdrop(new PublicKey(provider.publicKey),LAMPORTS_PER_SOL);
       await connection.confirmTransaction(fromAirDropSignature,{commitment:"confirmed"})
       console.log(`1 SOL airdropped to your wallet ${provider.publicKey.toString()} successfully`)
       setLoading(false);
   }catch(err){
       console.log(err);
       setLoading(false);
   }
}
```
Initially we are instantiating a new connection instance and also specifying which cluster to be connected to (“devnet” in this case).  We then create a fromAirDropSignature, using the requestAirDrop function. It takes in two arguments, the publicKey of the wallet and the amount of SOLs to be airdropped.
The confirmTransaction  allows us to pass in a signed transaction as an argument and have the program wait until it has been confirmed before moving on to other portions of the code.
 
A console.log in the browser console can be seen upon successful airdrop of 1 SOL. The loading state is used before and after the transactions so that during that time no CTAs are done.
You can also confirm this by checking the Phantom wallet.

# Minting your Tokens

It's time, you are ready to create your own token. There are a couple of other things which need to be imported first for executing the initial mint.

![6](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/6.png)

```
import { Keypair, Transaction, sendAndConfirmTransaction } from '@solana/web3.js';
import {Token,TOKEN_PROGRAM_ID} from "@solana/spl-token";
```

We put a button on the UI for doing the Initial Mint and this button is visible only when the wallet is connected. 
```
{
   walletConnected ? (
    <p>Create your own token 
<button disabled={loading} onClick={initialMintHelper}>Initial Mint </button>
    </p>):<></>
}
```

The initialMintHelper function along with some associated states is as follows:
```
const [isTokenCreated,setIsTokenCreated]=useState(false);
const [createdTokenPublicKey,setCreatedTokenPublicKey]=useState(null)	
const [mintingWalletSecretKey,setMintingWalletSecretKey]=useState(null)

const initialMintHelper=async ()=>{
   try{
       setLoading(true);
       const connection = new Connection(
           clusterApiUrl("devnet"),
           "confirmed"
       );
       const mintRequester=await provider.publicKey;
       const mintingFromWallet=await Keypair.generate();
       setMintingWalletSecretKey(JSON.stringify(mintingFromWallet.secretKey))
       var fromAirDropSignature=await connection.requestAirdrop(mintingFromWallet.publicKey,LAMPORTS_PER_SOL);
       await connection.confirmTransaction(fromAirDropSignature,{commitment:"confirmed"})
       const creatorToken=await Token.createMint(connection,mintingFromWallet,mintingFromWallet.publicKey,null,6,TOKEN_PROGRAM_ID);
       const fromTokenAccount=await creatorToken.getOrCreateAssociatedAccountInfo(mintingFromWallet.publicKey);
       await creatorToken.mintTo(fromTokenAccount.address,mintingFromWallet.publicKey,[],1000000)
       const toTokenAccount=await creatorToken.getOrCreateAssociatedAccountInfo(mintRequester);
       const transaction=new Transaction().add(
           Token.createTransferInstruction(
               TOKEN_PROGRAM_ID,
               fromTokenAccount.address,
               toTokenAccount.address,
               mintingFromWallet.publicKey,
               [],
               1000000
           )
       )
       const signature=await sendAndConfirmTransaction(connection,transaction,[mintingFromWallet],{commitment:"confirmed"})
       console.log("SIGNATURE:",signature);
       setCreatedTokenPublicKey(creatorToken.publicKey.toString())
       setIsTokenCreated(true);
       setLoading(false);
   }catch(err){
       console.log(err)
       setLoading(false);
   }
}
```
Let’s try to understand what’s happening over here. At first, we are establishing a secure connection and setting the loading state to true. 
Then, we create a mintingWallet, i.e., a whole new solana wallet which will be used to store the tokens which will be minted. We can also directly mint tokens in our wallet but it requires the secret key of the wallet, thus we create a new wallet. We store the secret key of the newly created wallet in a state.
In the @solana/web3.js library there is a function called createMint, that will help us create the on-chain mint object that will be used to create our coins/tokens. To create this mint object, some funds will be required, thus we airdrop some sol to our account initially. Now, the createMint function from the Token class, that was imported from the spl-token library, is used to actually create the mint object for our tokens. It takes in 6 arguments:
- The connection to the Solana network
- The account that will be paying the fee(mintingFromWallet)
- The public key of the account that has the authority to mint Tokens of such type(mintingFromWallet.publicKey)
- The public key of the account that has the authority to freeze tokens of this type(null as we don’t need any freezing authority now)
- Amount of decimal points allowed in this token (1 BTC is divisible upto 8 decimal places)
- Program id of the token (TOKEN_PROGRAM_ID)

We are initializing our wallet’s creator token account by calling upon the getOrCreateAssociatedAccountInfo function from the mint object so that our account can handle our tokens inside of our wallet. It only requires the public key of the wallet. Now, using the mint object (we’ve called it creatorToken in our case), along with its mintTo function, we can go ahead and create the token. The mintTo function takes 4 arguments:
- The address of the account to send it to ( fromTokenAccount.address)
- The public key of the person that has the minting authority over the token (mintingFromWallet.publicKey)
- Multiple-signers (null since we don’t require validation from anyone else)
- Number of tokens to be minted( Since the number of decimal places mentioned earlier is 6, now 1000000 will mean 1 token mint)

Congratulations, you have successfully minted your first token on Solana. Where can we see it? Since it is in the wallet which was created on the go (minting wallet), we need to transfer it to our wallet. To do so, we have to create a transfer instruction using the transferInstruction function from the Token class and attach it to the Transaction constructor using the add function. WE’ll name this object transaction.The toTokenAccount contains information about our loggedIn wallet. The transaction  is created by passing the desired 6 parameters. After having the transaction object, we need to authorize the transaction by signing it with our secret key. We declare the signature constant, which stores the signature (transaction hash) of the transaction that we created earlier and sent. 
At the end, we store the publicKey of the creatorToken to recreate the coin while minting again, or transferring tokens. We also store in a state that the token has been created successfully (in isTokenCreated). 
In the Phantom wallet you can see, one unknown token has been created.

![7](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/7.png)

# Minting more tokens

Now that we’ve created and minted our tokens for the very first time, let’s see how we can mint more tokens. It is totally similar to the initial mint with the only difference being, the mint object is being created here using the Token constructor (instead of using createMint again) and we also create the tokenMintingWallet. 
The UI will have one Mint Again button after the isTokenCreated state is true, i.e., after the Initial Mint is done.
```
<li>Mint More 100 tokens: <button disabled={loading || supplyCapped} onClick={mintAgainHelper}>Mint Again</button></li>
```
Also, you cannot mint more, when the supply for the tokens is capped. Thus, for that we will be using the supplyCapped state.
```
const [supplyCapped,setSupplyCapped]=useState(false)	
```
The mintAgainHelper is used to mint more tokens (100 in our case). 
```
const mintAgainHelper=async ()=>{
   try{
       setLoading(true);
       const connection = new Connection(
           clusterApiUrl("devnet"),
           "confirmed"
       );
       const createMintingWallet=await Keypair.fromSecretKey(Uint8Array.from(Object.values(JSON.parse(mintingWalletSecretKey))));
       const mintRequester=await provider.publicKey;
       var fromAirDropSignature=await connection.requestAirdrop(createMintingWallet.publicKey,LAMPORTS_PER_SOL);
       await connection.confirmTransaction(fromAirDropSignature,{commitment:"confirmed"})
       const creatorToken=new Token(connection,createdTokenPublicKey,TOKEN_PROGRAM_ID,createMintingWallet)
       const fromTokenAccount=await creatorToken.getOrCreateAssociatedAccountInfo(createMintingWallet.publicKey);
       const toTokenAccount=await creatorToken.getOrCreateAssociatedAccountInfo(mintRequester);
       await creatorToken.mintTo(fromTokenAccount.address,createMintingWallet.publicKey,[],100000000);
       const transaction=new Transaction().add(
           Token.createTransferInstruction(
               TOKEN_PROGRAM_ID,
               fromTokenAccount.address,
               toTokenAccount.address,
               createMintingWallet.publicKey,
               [],
               100000000
           )
       )
       await sendAndConfirmTransaction(connection,transaction,[createMintingWallet],{commitment:"confirmed"})
       setLoading(false);
   }catch(err){
       console.log(err);
       setLoading(false);
   }
}
```
First, we establish the connection. Then, we create the tokenMintingWallet using the function KeyPair.fromSecretKey. It will require the secretKey of the wallet which we stored in the state mintingWalletSecretKey during the initial Mint. Hence, the createMintingWallet is created. Now, we airdrop some sols to ensure we have funds for the upcoming minting. 
The mint needs to be recreated now from the Token constructor which is provided by the @solana/spl-token package. It takes in 4 parameters:
- Connection to the solana devnet
- The public key of the created Token during the initial Mint( in the state createdTokenPublicKey)
- The program id (TOKEN_PROGRAM_ID here)
- The payer ( the createMintingWallet in this case)
The constructor returns the exact mint which is referred to as creatorToken and the rest of the steps similar to Initial Mint is followed. 
We set up the fromTokenAccount, and then use the mintTo functions. It mints more tokens.
Later, we transfer the minted tokens to our actual phantom wallet accounts to view. 

# Transferring tokens to your friends

Now that we’ve created our tokens, let’s try sending these tokens to our friend’s wallet.
You can think of an SPL wallet (wallet that stores tokens on Solana) as a bank and all other tokens as the accounts in that bank. Unless you send an instruction to your friend to create an account in their wallet for your token, they won’t really be able to see it or even receive it.

const transferTokenHelper=async ()=>{
       try{
           setLoading(true);
           const connection = new Connection(
               clusterApiUrl("devnet"),
               "confirmed"
           );
           const createMintingWallet=await Keypair.fromSecretKey(Uint8Array.from(Object.values(JSON.parse(mintingWalletSecretKey))));
           const receiverWallet=new PublicKey("5eaFQvgJgvW4rDjcAaKwdBb6ZAJ6avWimftFyjnQB3Aj")
           var fromAirDropSignature=await connection.requestAirdrop(createMintingWallet.publicKey,LAMPORTS_PER_SOL);
           await connection.confirmTransaction(fromAirDropSignature,{commitment:"confirmed"})
           console.log('1 SOL airdropped to the wallet for fee')
           const creatorToken=new Token(connection,createdTokenPublicKey,TOKEN_PROGRAM_ID,createMintingWallet)
           const fromTokenAccount =await creatorToken.getOrCreateAssociatedAccountInfo(provider.publicKey);
           const toTokenAccount=await creatorToken.getOrCreateAssociatedAccountInfo(receiverWallet);
           const transaction=new Transaction().add(
               Token.createTransferInstruction(TOKEN_PROGRAM_ID,fromTokenAccount.address,toTokenAccount.address,provider.publicKey,[],10000000)
           );
           transaction.feePayer=provider.publicKey;
           let blockhashObj = await connection.getRecentBlockhash();
           console.log("blockhashObj", blockhashObj);
           transaction.recentBlockhash = await blockhashObj.blockhash;
      
           if (transaction) {
             console.log("Txn created successfully");
           }
      
           let signed = await provider.signTransaction(transaction);
           let signature = await connection.sendRawTransaction(signed.serialize());
           await connection.confirmTransaction(signature);
           console.log("SIGNATURE: ", signature);
           setLoading(false);
       }catch(err){
           console.log(err)
           setLoading(false);
       }
   }

This is the function that will enable us to transfer our own tokens to our friend’s wallets. Let’s analyse what is happening in the code above.

As earlier, we first established a connection to the Solana devnet. Then we prepare (initialise) our minting wallet using our secret key and the receiving wallet of our friend using their public key. Next we request some SOL in our minting wallet to handle the transaction charges and confirm the airdrop. Next we initialize the account for our tokens in our own wallet and the wallet of our friend.
With this, we head into the most important step of our code: The transaction along with instructions. We fill in the instructions such as who’ll be paying the fee for the transaction and account creation, who will receive the tokens and from whom and we also provide it the most recent block of the blockchain (hash of the block to be precise) to carry forth the transaction. After the transaction instructions have been created, we now sign it with our wallet credentials and send this transaction to our friend and later on confirm whether the transaction took place or not.
Congratulations, you just started transactions in your custom cryptocurrency now.

# Capping Token Supply

Let’s say you are creating tokens to raise money for your next project. You plan on creating 100 tokens and sell it to your investors that represents their ownership of the project. The investors might be sceptical to invest in your project as you possess the ability to always create more tokens and reduce their ownership in the project. Hence, they would want you to “renounce the ownership” of your token. That way, there’ll always be only 100 tokens in circulation forever.   
Solana provides us the ability to disable our minting authority, and never enable it back i.e. “capping the supply” of our token. 
The method to do that again is pretty straightforward, as we just have to set the minting authority of our tokens to *null*. Then no one would be able to mint more of our tokens and it can be seen publicly by viewing our token on https://explorer.solana.com/.

   const capSupplyHelper=async ()=>{
       try{
           setLoading(true);
           const connection = new Connection(
               clusterApiUrl("devnet"),
               "confirmed"
           );
           const createMintingWallet=await Keypair.fromSecretKey(Uint8Array.from(Object.values(JSON.parse(mintingWalletSecretKey))));
           var fromAirDropSignature=await connection.requestAirdrop(createMintingWallet.publicKey,LAMPORTS_PER_SOL);
           await connection.confirmTransaction(fromAirDropSignature);
           const creatorToken=new Token(connection,createdTokenPublicKey,TOKEN_PROGRAM_ID,createMintingWallet)
           await creatorToken.setAuthority(createdTokenPublicKey,null,"MintTokens",createMintingWallet.publicKey,[createMintingWallet])
           setSupplyCapped(true)
           setLoading(false);
       }catch(err){
           console.log(err);
           setLoading(false);
       }
   }

This is the function that will enable us to transfer our own tokens’ minting authority to null or in simple terms to remove the authority of anyone to mint more of our tokens. Let’s analyse what is happening in the code above.

As earlier, we first established a connection to the Solana devnet. Then we prepare (initialise) our minting wallet using our secret key. Next we request some SOL in our minting wallet to handle the transaction charges and confirm the airdrop. Then we initialize the token object of our token using the new Token() constructor after which we set the authority to null in the next function. What follows is the setState hooks used to tell our React frontend that the supply of our token has been capped.

Now you’ve learnt how to fix the supply of your token too, and guess what, if you fixed the supply of your token to 1, then it could very well function as an NFT too!

Your final web page should look like this.

![8](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/8.png)

# Naming your Tokens

In Phantom wallet, we see that our token is listed as an “Unknown Token”. If we want our token to have a widely recognised name, symbol and image, we need to send a pull request to the [solana-labs/token-list: The community maintained Solana token registry repository](https://github.com/solana-labs/token-list).

![9](https://github.com/altsam/create_crypto_with_js/blob/main/learn_assets/9.png)

Add an entry to the “solana.tokenlist.json” file in the “src/tokens” directory.
The JSON entry would have the following structure.
```
{
   "chainId":101,
   "address":"your token address",
   "symbol":"your token symbol",
   "name":"Token Name",
   "decimals":"<number of decimals your token supports>",
   "logoURI":"the CDN link to your token's image",
   "tags":[
      "tags_if_any"
   ],
   "extensions":{
      
   }
}
```

Once the PR gets approved, you’ll be able to view the token image and name on every wallet in the Solana ecosystem.

# Conclusion

Congratulations to all the quest masters on creating their very first personal token minting website. You’re now armed with all the knowledge required to mint your personal tokens and distribute them with your friends!
