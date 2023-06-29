<h1>Building Your First Flow Application</h1>


<h2>Account Signup</h2>


Those who want to participate in the Flow prize category have the option of using [Flow’s free-to-use Testnet](https://hackp.ac/flow-testnet-deploymentguidelines) to qualify. All you need to do is [sign up for a testnet account](https://hackp.ac/flow-testnet-signup) and start developing. 

### Step 1) First install [Flow CLI](https://developers.flow.com/tooling/flow-cli/install)




* For Mac Users


```
brew install flow-cli

//Alternatively for Mac & Linux users, if you want to download from a pre-built binary, use the command below. Note: this will only work on x86-64. 

sh -ci "$(curl -fsSL https://raw.githubusercontent.com/onflow/flow-cli/master/install.sh)"

```



* For Windows Users


```
//This update method only works on Windows 10, 8.1, or 7 (SP1, with PowerShell 3.0), on x86-64.

//Powershell installation instructions can be found here. 

iex "& { $(irm 'https://raw.githubusercontent.com/onflow/flow-cli/master/install.ps1') }"
```


### Step 2) Generate a [public key](https://developers.flow.com/tooling/flow-cli/keys/generate-keys)



```
//Once you have Flow-CLI downloaded, you can generate a public key using the following command: 

flow keys generate

//Save your private and public keys in a secure space before moving on to the next step. 
```


### Step 3) Signup for a testnet account using your public key




* Head over to the [Flow Testnet signup page](https://hackp.ac/flow-testnet-signup) and paste your public key into the form.  \
Verify that you’re human and proceed with creating an account. 



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")




* Remember to copy your address and save it in a secure space.

 



* Confirm your funds by viewing your account using the _View Account_ link pictured to the right. You should now have 1,000 Flow Token that courtesy of the Flow Testnet Faucet.  Please bear in mind that these tokens are not actual currency, but can be used to build out proof of concepts on the Flow Testnet. 

<h2>Your First Application on Flow</h2>


In this tutorial, we are going to interact with an existing smart contract on Flow's testnet known as the Profile Contract. Using this contract, we will create a new user profile and edit the profile information. 

In order to do this, the Flow Client Library (FCL) concepts we'll cover are:


[TOC]


<h3 id="installation-&-configuration">Installation & Configuration</h3>


Let’s get started by inputting the following commands in a new terminal window. 


```
npx create-next-app@latest flow-app
cd flow-app

npm install @onflow/fcl --save

npm run dev  // the page that populates on localhost:3000 should look something like the image below. 
```


Let’s create a new folder in our application called flow. Within this folder, we’ll have a config.js file. 

We’ll use this folder to keep all of our customizations centralized. Go ahead and run the following commands. 


```
cd flow-app && mkdir flow
cd flow && touch config.js
```


Your config.js file will contain configuration information for FCL, such as what Access Node and wallet discovery endpoint to use (e.g. testnet or a local emulator). Add the following code to the file:


```
import { config } from "@onflow/fcl";

config({
  "accessNode.api": "https://rest-testnet.onflow.org", 
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn" 
})
```


_The **accessNode.api** key specifies the address of a Flow access node. _

**_discovery.wallet is an address that points to a service that lists FCL compatible wallets. _**

<h3 id="authentication">Authentication</h3>


Now that the config.js file is set up and we have the framework necessary to start interacting with the Flow API, let’s go ahead and finish configuration by importing the Flow config file into the application’s main page.  

(**Optional**: you can also remove the default styling of the app by heading over to **flow-app/app/global.css** and commenting out all of the contents)

Head over to **flow-app/app/page.js** and replace the existing code with what we have below:  


```
"use client"; // This is a client component
import Head from 'next/head';
import "../flow/config";
import { useState, useEffect } from "react";
import * as fcl from "@onflow/fcl";

export default function Home() {

  const [user, setUser] = useState({loggedIn: null})
	// sets default value of loggedIn to null

  useEffect(() => fcl.currentUser.subscribe(setUser), [])
	// uses FCL to subscribe the user that logs in or signs up

  const AuthedState = () => {
    // function updates the user variable as true or false based on whether a user is logged in or not. 

    return (
	// generates a log out button if loggedIn is true
      <div>
        <div>Address: {user?.addr ?? "No Address"}</div>
        <button onClick={fcl.unauthenticate}>Log Out</button>
          </div>
        )
      }

      const UnauthenticatedState = () => {
        return (
	// displays default app settings when loggedIn is null
          <div>
            <button onClick={fcl.logIn}>Log In</button>
            <button onClick={fcl.signUp}>Sign Up</button>
          </div>
        )
      }


      return (
        <div>
          <Head>
            <title>FCL Quickstart with NextJS</title>
            <meta name="description" content="My first web3 app on Flow!" />
            <link rel="icon" href="/favicon.png" />
          </Head>
          <h1>Flow App</h1>
          {user.loggedIn
            ? <AuthedState />
            : <UnauthenticatedState />
          }
        </div>
      );
    }
```


Localhost:3000 should now display the text 'Flow App' with login and signup buttons like the picture to the right. 

Now we can sign up and log in users to our application using the Flow Client Library on testnet. 

When you attempt to sign up, you’ll be prompted with several different wallet options. Since we are on testnet, we can generate accounts in a low-stakes environment very easily.  \
 \
For the purposes of this demo, I chose the Blotco option and input an email address I have access to for verification purposes. Feel free to do the same! _The Blotco option includes subsidized transactions so you won’t need to use any of your testnet Flow Token to run this sample application. _

Once you’ve signed up and authenticated, you’ll be given an address for your app-generated Flow account. Your address will display on the application page and you should now see a ‘log out’ button, since the _loggedIn_ state of the application now shows as _true_.  

We can quickly check what the user object looks like by console logging the currentUser variable our application is subscribing to. Let’s go ahead and make a quick edit to the _useEffect_ function using the following code. 


```
  useEffect(() => fcl.currentUser.subscribe((userData) => {
    setUser(userData)
    console.log(userData)
  }), [])
```


Open up your browser’s console, log out and log back in using the same credentials as before (your user info should still be cached). You should be able to find a user object that shows your user address, a CID (customer ID), along with the state of your user displayed as loggedIn: true.

Since reading off of the Flow blockchain is not considered a transaction, we can log the user object without using any currency or ‘gas’.  

<h3 id="querying-the-blockchain">Querying the Blockchain</h3>


Now that we’ve got the authentication set up for our application, we can start querying the blockchain for user profile data.  \
 \
In this case, our friends at Flow have already set up an account address that has all sorts of user profile contracts we can use for our application. Of course, you’ll be able to customize this data in your personal account using the same file structure we’re working with now, but for the time being, the sample profile can show us what querying the blockchain and using these profile-based functionalities looks like. 

If you head to **flow-view-source.com** and plug in the account address: **0xba1132bc08f82fe2**, you should be able to view the sample profile we’ll be using moving forward. 

Go ahead and click into ‘Profile’ beneath the ‘Contracts’ tab. There you’ll find all of the profile contracts associated with this address including getName, getAvatar, getInfo, and so on. 

In order for our application to reference the contracts in this sample profile, let’s jump back into the config.js file we made during the configuration step and add a line of code referencing the account address I provided above (don’t forget the comma after the second line of code). 


```
config({
  "accessNode.api": "https://rest-testnet.onflow.org", 
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",         
  "0xProfile": "0xba1132bc08f82fe2"
})
```


Now we can start grabbing some of that sample profile data and displaying it on our application’s frontend! 

Let’s go back to the page.js file and add some new functionality. As a heads up, we’ll be adding a new _name_ and _setName_ variables to display the sample profile’s name. In addition to that, we’ll be creating a _sendQuery_ function that fires when we click the Send Query button. 

Go ahead and copy the code below into your page.js file. You’ll see the new lines of code labeled highlighted and commented as // NEW. 


```
'use client'; // This is a client component
import Head from 'next/head'
import '../flow/config';
import { useState, useEffect } from 'react';
import * as fcl from '@onflow/fcl';

export default function Home() {

  const [user, setUser] = useState({loggedIn: null})
  const [name, setName] = useState('No Profile') // NEW

  useEffect(() => fcl.currentUser.subscribe(setUser), [])

// NEW
  const sendQuery = async () => {
    const profile = await fcl.query({
      cadence: `
        import Profile from 0xProfile

        pub fun main(address: Address): Profile.ReadOnly? {
          return Profile.read(address)
        }
      `,
      args: (arg, t) => [arg(user.addr, t.Address)]
    })

    setName(profile?.name ?? 'No Profile')
  }

  const AuthedState = () => {
    return (
      <div>
        <div>Address: {user?.addr ?? 'No Address'}</div>
        <div>Profile Name: {name ?? "--"}</div> {/* NEW */}
        <button onClick={sendQuery}>Send Query</button> {/* NEW */}
        <button onClick={fcl.unauthenticate}>Log Out</button>
      </div>
    )
  }

  const UnauthenticatedState = () => {
    return (
      <div>
        <button onClick={fcl.logIn}>Log In</button>
        <button onClick={fcl.signUp}>Sign Up</button>
      </div>
    )
  }

  return (
    <div>
      <Head>
        <title>FCL Quickstart with NextJS</title>
        <meta name="description" content="My first web3 app on Flow!" />
        <link rel="icon" href="/favicon.png" />
      </Head>
      <h1>Flow App</h1>
      {user.loggedIn
        ? <AuthedState />
        : <UnauthenticatedState />
      }
    </div>
  );
}
```


Your application should resemble the image to the right. You may notice that when you attempt to use the Send Query button, you either receive an error message or the Profile Name field simply continues to read ‘No Profile.’  \
 \
The reason for this is simple. While the code is syntactically correct, we have yet to initialize our testnet profile on the blockchain, meaning we are unable to store the 0xProfile data within our current user profile address. 

_“For the Profile contract to store a Profile in a user's account, it does so by initializing what is called a "resource." A resource is an ownable piece of data and functionality that can live in the user's account storage. This paradigm is known as "resource-oriented-programming", a principle that is core to Cadence and differentiates its ownership model from other smart contract languages, [read more here](https://developers.flow.com/cadence/intro#intuiting-ownership-with-resources). Cadence makes it so that resources can only exist in one place at any time, they must be deliberately created, cannot be copied, and if desired, must be deliberately destroyed.”  _

_Since one of the primary use cases for Flow is the representation and transfer of scarce digital assets (such as digital art for example), disabling the ability to duplicate and/or misrepresent the original state of a resource makes Flow’s smart contracts very safe to use. _

Since we are transferring a profile resource to our account in order to display it on our application, we need to ensure that our user address or account has a profile to transfer that resource over to. From there, we’ll be able to change or mutate the resource as we see fit.  

<h3 id="initializing-an-account">Initializing an Account</h3>


So let’s get our application back on line by creating an _initAccount_ function that allows us to add a profile to our account if a profile is not yet present. We do that using something called a "transaction." Transactions occur when you want to change the state of the blockchain, in this case, some data in a resource, in a specific account. And there is a cost (transaction fee) in order to do that; unlike a query.

That's where we jump back into FCL code. Instead of query, we use mutate for transactions. And because there is a cost, we need to add a few fields that tell Flow who is proposing the transaction, who is authorizing it, who is paying for it, and how much they're willing to pay for it. Those fields — not surprisingly — are called: proposer, authorizer, payer, and limit. For more information on these signatory roles, check out this [doc](https://developers.flow.com/concepts/start-here/transaction-signing#signer-roles). 

Update your code in page.js to include the _initAccount_ function as well as the Init Account button. 


```
export default function Home() {

  const [user, setUser] = useState({loggedIn: null})
  const [name, setName] = useState('No Profile')

  useEffect(() => fcl.currentUser.subscribe(setUser), [])

  const sendQuery = async () => {
    const profile = await fcl.query({
      cadence: `
        import Profile from 0xProfile

        pub fun main(address: Address): Profile.ReadOnly? {
          return Profile.read(address)
        }
      `,
      args: (arg, t) => [arg(user.addr, t.Address)]
    })

    setName(profile?.name ?? 'No Profile')
  }

// NEW
const initAccount = async () => {
  const transactionId = await fcl.mutate({
    cadence: `
      import Profile from 0xProfile

      transaction {
        prepare(account: AuthAccount) {
          // Only initialize the account if it hasn't already been initialized
          if (!Profile.check(account.address)) {
            // This creates and stores the profile in the user's account
            account.save(<- Profile.new(), to: Profile.privatePath)

            // This creates the public capability that lets applications read the profile's info
            account.link<&Profile.Base{Profile.Public}>(Profile.publicPath, target: Profile.privatePath)
          }
        }
      }
    `,
    payer: fcl.authz,
    proposer: fcl.authz,
    authorizations: [fcl.authz],
    limit: 50
  })

  const transaction = await fcl.tx(transactionId).onceSealed()
}

console.log(transaction)

  const AuthedState = () => {
    return (
      <div>
        <div>Address: {user?.addr ?? "No Address"}</div>
        <div>Profile Name: {name ?? "--"}</div>
        <button onClick={sendQuery}>Send Query</button>
        <button onClick={initAccount}>Init Account</button> {/* NEW */}
        <button onClick={fcl.unauthenticate}>Log Out</button>
      </div>
    )
  }

  const UnauthenticatedState = () => {
    return (
      <div>
        <button onClick={fcl.logIn}>Log In</button>
        <button onClick={fcl.signUp}>Sign Up</button>
      </div>
    )
  }

  return (
    <div>
      <Head>
        <title>FCL Quickstart with NextJS</title>
        <meta name="description" content="My first web3 app on Flow!" />
        <link rel="icon" href="/favicon.png" />
      </Head>
      <h1>Flow App</h1>
      {user.loggedIn
        ? <AuthedState />
        : <UnauthenticatedState />
      }
    </div>
  )
}
```


When you refresh your application browser, you should see an Init Account button. When you hit it, you’ll be prompted with a Confirm Transaction window that contains a script which should be identical to your _initAccount_ transaction script. 

This script is essentially checking for a profile and if there is no profile, a new profile is created. From there, we will move and save the profile into the user account. The last line of the script then allows the profile data to be queried. 

Once the _initAccount_ script runs, that should allow us to query the existing name of the 0xProfile account.  Now, when you hit the send query button, the profile name should update to ‘Anon’ which is the default name within the profile contract.  \
 \
Mutating the Blockchain

Now that our _initAccount_ script is running, let’s set up some functionality that allows us to update or mutate the profile name resource again. We’ll be setting a new name and subscribing to the resource in order to read it to the application interface. In this case, we are updating the profile name to read “MLH.”  \
 \
In order to do this, we’ll be adding an _executeTransaction_ function as well as an execution transaction button, along with a transaction status field that will update us on how far along our profile name update is coming. You can find all of the transaction statuses enumerated [here](https://developers.flow.com/concepts/nodes/access-api#transaction-status). 

Let’s go ahead and update our existing code with the following 


```
export default function Home() {

  const [user, setUser] = useState({loggedIn: null})
  const [name, setName] = useState('')
  const [transactionStatus, setTransactionStatus] = useState(null) // NEW

  useEffect(() => fcl.currentUser.subscribe(setUser), [])

  const sendQuery = async () => {
    const profile = await fcl.query({
      cadence: `
        import Profile from 0xProfile

        pub fun main(address: Address): Profile.ReadOnly? {
          return Profile.read(address)
        }
      `,
      args: (arg, t) => [arg(user.addr, t.Address)]
    })

    setName(profile?.name ?? 'No Profile')
  }

  const initAccount = async () => {
    const transactionId = await fcl.mutate({
      cadence: `
        import Profile from 0xProfile

        transaction {
          prepare(account: AuthAccount) {
            // Only initialize the account if it hasn't already been initialized
            if (!Profile.check(account.address)) {
              // This creates and stores the profile in the user's account
              account.save(<- Profile.new(), to: Profile.privatePath)

              // This creates the public capability that lets applications read the profile's info
              account.link<&Profile.Base{Profile.Public}>(Profile.publicPath, target: Profile.privatePath)
            }
          }
        }
      `,
      payer: fcl.authz,
      proposer: fcl.authz,
      authorizations: [fcl.authz],
      limit: 50
    })

    const transaction = await fcl.tx(transactionId).onceSealed()

    console.log(transaction) 
  }

 // NEW
  const executeTransaction = async () => {
    const transactionId = await fcl.mutate({
      cadence: `
        import Profile from 0xProfile

        transaction(name: String) {
          prepare(account: AuthAccount) {
            account
              .borrow<&Profile.Base{Profile.Owner}>(from: Profile.privatePath)!
              .setName(name)
          }
        }
      `,
      args: (arg, t) => [arg("MLH", t.String)],
      payer: fcl.authz,
      proposer: fcl.authz,
      authorizations: [fcl.authz],
      limit: 50
    })

    fcl.tx(transactionId).subscribe(res => setTransactionStatus(res.status))
  }

  const AuthedState = () => {
    return (
      <div>
        <div>Address: {user?.addr ?? "No Address"}</div>
        <div>Profile Name: {name ?? "--"}</div>
        <div>Transaction Status: {transactionStatus ?? "--"}</div> {/* NEW */}
        <button onClick={sendQuery}>Send Query</button>
        <button onClick={initAccount}>Init Account</button>
  <button onClick={executeTransaction}>Execute Transaction</button> {/* NEW */}

        <button onClick={fcl.unauthenticate}>Log Out</button>
      </div>
    )
  }

  const UnauthenticatedState = () => {
    return (
      <div>
        <button onClick={fcl.logIn}>Log In</button>
        <button onClick={fcl.signUp}>Sign Up</button>
      </div>
    )
  }

  return (
    <div>
      <Head>
        <title>FCL Quickstart with NextJS</title>
        <meta name="description" content="My first web3 app on Flow!" />
        <link rel="icon" href="/favicon.png" />
      </Head>
      <h1>Flow App</h1>
      {user.loggedIn
        ? <AuthedState />
        : <UnauthenticatedState />
      }
    </div>
  )
}
```


Now you should be able to hit the Execute Transaction button. You will once again be prompted with a pop-up window that runs the executeTransaction script we have added to our code. 

Once you see your transaction status reach 4 (or sealed), you can hit the Send Query button and your new Profile Name will display! 

Feel free to update the executeTransaction function to update the resource with a name of your own choosing. 

<h2>Recap</h2>


Great job! In addition to a new testnet account with $1000 of testnet Flow Token,you now have a fully functional Flow application that can auth, query, init accounts, and mutate the chain. You can take what you’ve learned about Flow’s smart contract transactions and apply them to your own hackathon project ideas. 

Below you will find links to the resources I referenced for this tutorial. Please note that the documentation above is an updated version of the source material. 

[Flow App Quickstart Documentation](https://hackp.ac/flow-appquickstart) 

[Flow App Quickstart Video Walkthrough](https://hackp.ac/flow-appquickstart-video)

To get you on your way, here are some useful resources you can reference. 



* [Developer Portal](hackp.ac/flow)
* [Flow SDKs](hackp.ac/flow-sdk) 
* [Intro to Flow](https://jan-bernatik.medium.com/introduction-to-flow-blockchain-7532977c8af8)
* [Flow Technical Papers](https://flow.com/technical-paper) 
* [Flow Playground](hackp.ac/flow-playground)
* [Youtube Tutorial for Flow Playground](https://hackp.ac/flow-playground-tutorial)
* [Testnet Signup](hackp.ac/flow-testnet-signup) 
* [Testnet Deployment Doc](https://hackp.ac/flow-testnet-deploymentguidelines)
* [Flow discord](https://hackp.ac/flow-discord)
* [Flow-View-Source](https://hackp.ac/flow-view-source)
