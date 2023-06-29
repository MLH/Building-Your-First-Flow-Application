<h1>Building Your First Flow Application</h1>


<h2>Account Signup</h2>


Those who want to participate in the Flow prize category have the option of using [Flow’s free-to-use Testnet](https://hackp.ac/flow-testnet-deploymentguidelines) to qualify. All you need to do is [sign up for a testnet account](https://hackp.ac/flow-testnet-signup) and start developing. 

<h3>Step 1) First install [Flow CLI](https://developers.flow.com/tooling/flow-cli/install) </h3>




* For Mac Users
* For Windows Users

<h3>Step 2) Generate a [public key](https://developers.flow.com/tooling/flow-cli/keys/generate-keys)</h3>


<h3>Step 3) Signup for a testnet account using your public key</h3>




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

Let’s create a new folder in our application called flow. Within this folder, we’ll have a config.js file. 

We’ll use this folder to keep all of our customizations centralized. Go ahead and run the following commands. 

Your config.js file will contain configuration information for FCL, such as what Access Node and wallet discovery endpoint to use (e.g. testnet or a local emulator). Add the following code to the file:

_The **accessNode.api** key specifies the address of a Flow access node. _

**_discovery.wallet is an address that points to a service that lists FCL compatible wallets. _**

<h3 id="authentication">Authentication</h3>


Now that the config.js file is set up and we have the framework necessary to start interacting with the Flow API, let’s go ahead and finish configuration by importing the Flow config file into the application’s main page.  

(**Optional**: you can also remove the default styling of the app by heading over to **flow-app/app/global.css** and commenting out all of the contents)

Head over to **flow-app/app/page.js** and replace the existing code with what we have below:  

Localhost:3000 should now display the text 'Flow App' with login and signup buttons like the picture to the right. 

Now we can sign up and log in users to our application using the Flow Client Library on testnet. 

When you attempt to sign up, you’ll be prompted with several different wallet options. Since we are on testnet, we can generate accounts in a low-stakes environment very easily.  \
 \
For the purposes of this demo, I chose the Blotco option and input an email address I have access to for verification purposes. Feel free to do the same! _The Blotco option includes subsidized transactions so you won’t need to use any of your testnet Flow Token to run this sample application. _

Once you’ve signed up and authenticated, you’ll be given an address for your app-generated Flow account. Your address will display on the application page and you should now see a ‘log out’ button, since the _loggedIn_ state of the application now shows as _true_.  

We can quickly check what the user object looks like by console logging the currentUser variable our application is subscribing to. Let’s go ahead and make a quick edit to the _useEffect_ function using the following code. 

Open up your browser’s console, log out and log back in using the same credentials as before (your user info should still be cached). You should be able to find a user object that shows your user address, a CID (customer ID), along with the state of your user displayed as loggedIn: true.

Since reading off of the Flow blockchain is not considered a transaction, we can log the user object without using any currency or ‘gas’.  

<h3 id="querying-the-blockchain">Querying the Blockchain</h3>


Now that we’ve got the authentication set up for our application, we can start querying the blockchain for user profile data.  \
 \
In this case, our friends at Flow have already set up an account address that has all sorts of user profile contracts we can use for our application. Of course, you’ll be able to customize this data in your personal account using the same file structure we’re working with now, but for the time being, the sample profile can show us what querying the blockchain and using these profile-based functionalities looks like. 

If you head to **flow-view-source.com** and plug in the account address: **0xba1132bc08f82fe2**, you should be able to view the sample profile we’ll be using moving forward. 

Go ahead and click into ‘Profile’ beneath the ‘Contracts’ tab. There you’ll find all of the profile contracts associated with this address including getName, getAvatar, getInfo, and so on. 

In order for our application to reference the contracts in this sample profile, let’s jump back into the config.js file we made during the configuration step and add a line of code referencing the account address I provided above (don’t forget the comma after the second line of code). 

Now we can start grabbing some of that sample profile data and displaying it on our application’s frontend! 

Let’s go back to the page.js file and add some new functionality. As a heads up, we’ll be adding a new _name_ and _setName_ variables to display the sample profile’s name. In addition to that, we’ll be creating a _sendQuery_ function that fires when we click the Send Query button. 

Go ahead and copy the code below into your page.js file. You’ll see the new lines of code labeled highlighted and commented as // NEW. 

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

When you refresh your application browser, you should see an Init Account button. When you hit it, you’ll be prompted with a Confirm Transaction window that contains a script which should be identical to your _initAccount_ transaction script. 

This script is essentially checking for a profile and if there is no profile, a new profile is created. From there, we will move and save the profile into the user account. The last line of the script then allows the profile data to be queried. 

Once the _initAccount_ script runs, that should allow us to query the existing name of the 0xProfile account.  Now, when you hit the send query button, the profile name should update to ‘Anon’ which is the default name within the profile contract.  \
 \
Mutating the Blockchain

Now that our _initAccount_ script is running, let’s set up some functionality that allows us to update or mutate the profile name resource again. We’ll be setting a new name and subscribing to the resource in order to read it to the application interface. In this case, we are updating the profile name to read “MLH.”  \
 \
In order to do this, we’ll be adding an _executeTransaction_ function as well as an execution transaction button, along with a transaction status field that will update us on how far along our profile name update is coming. You can find all of the transaction statuses enumerated [here](https://developers.flow.com/concepts/nodes/access-api#transaction-status). 

Let’s go ahead and update our existing code with the following 

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
