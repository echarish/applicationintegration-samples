We can connect to salesforce using 2 ways:

1. OAUTH2 JWT BEARER  
2. UserName and Password-https://[www.googlecloudcommunity.com/gc/Integration-Services/Application-Integration-Create-Salesforce-Conne...](http://www.googlecloudcommunity.com/gc/Integration-Services/Application-Integration-Create-Salesforce-Connection-using/m-p/844881#M1775)

#### This document explains how to connect to Salesforce using OAuth JWT Bearer 

Make sure to enable the required api’s and grant the necessary roles to the service account. Follow the link below before you begin.

Prerequisites:[https://cloud.google.com/integration-connectors/docs/connectors/salesforce/configure\#before-you-begi...](https://cloud.google.com/integration-connectors/docs/connectors/salesforce/configure#before-you-begin)

We can connect to salesforce using 2 ways:

## OAUTH2 JWT BEARER

Step 1:Creating private key and X509 certificate

Step 2:Creating connected app in Salesforce

Step 3:Prior Approval of the Client App

Step 4:Create Salesforce Connection

### Step 1:Creating private key and X509 certificate

You can use openssl to generate private keys and public certificates.

Sample openssl command to generate the private key. You can open the cloud shell and enter this command. 

```
openssl genrsa \-out privatekey.pem 1024
```

![screenshots/0-1674104441328.png](screenshots/0-1674104441328.png)

```
openssl req \-new \-x509 \-key privatekey.pem \-out publickey.cer \-days 3650
```

![screenshots/1-1674104441561.png](screenshots/1-1674104441561.png)

Now list the files generated:

```
ls \-lrt
```

![screenshots/2-1674104441355.png](screenshots/2-1674104441355.png)
Download these files from the gcloud to your local system by clicking the download and selecting the privatekey.pem and publickey.cer files

![screenshots/3-1674104441444.png](screenshots/3-1674104441444.png)

![screenshots/4-1674104441365.png](screenshots/4-1674104441365.png)

### Step 2:Creating Connected App in Salesforce

Note:Salesforce is always an evolving product, some terminologies and screenshots might differ.

Login to salesforce domain and go to setup under settings

![screenshots/5-1674104441368.png](screenshots/5-1674104441368.png)

Go to Platform Tools-\>Apps-\>App Manager-\>New Connected App

![screenshots/6-1674104441563.png](screenshots/6-1674104441563.png)

Fill in Connected App Name, email, phone, description etc. 

![screenshots/7-1674104441529.png](screenshots/7-1674104441529.png)

Tick ‘Enable OAuth Settings’. Fill Callback URL \= ‘[https://oauthdebugger.com/debug’](https://oauthdebugger.com/debug%E2%80%99) // we are using a great online debugger. Tick ‘Use digital signatures’.Upload \<publickey.cer from Step 1\>

![screenshots/8-1674104441435.png](screenshots/8-1674104441435.png)

Selected OAuth Scopes \= Perform request at any time(refresh\_token, offline access) and Manage user data via APIs (api). If required, then you can add more scopes. 

![screenshots/10-1674104441497.png](screenshots/10-1674104441497.png)


Save the connected app you created. Then click on Manage Consumer Details to save the Client\_ID. 

![screenshots/11-1674104441373.png](screenshots/11-1674104441373.png)

### Step 3:Prior approval of the Client App

This needs to be performed once per salesforce user(or profile) This will be a backend administrative approval action

Note: If we don't perform Step 3, we will get the following error in step 5 {"error":"invalid\_grant","error\_description":"user hasn't approved this consumer"}

Let us pretend to be an admin and approve this app/consumer to access a specific user’s data associated with a specific profile.

Go to Platform Tools-\>Apps-\>Connected Apps-\>Manage Connected Apps and click edit against your app.

![screenshots/12-1674104441440.png](screenshots/12-1674104441440.png)

Choose ‘Permitted Users’ \= ‘Admin approved users are pre-authorized’ and click on Save .

![screenshots/13-1674104441541.png](screenshots/13-1674104441541.png)

Go to Settings \> Setup-\>Administration-\>Users-\>Profiles. Edit the profile associated with the user(In my case the user is associated with the System administrator profile. 

Under profile search for the property “Connected App Access” and “Tick” and then save the profile

![screenshots/14-1674104441363.png](screenshots/14-1674104441363.png)

Under profile search for the property “Connected App Access” and “Tick” and then save the profile

![screenshots/15-1674104441576.png](screenshots/15-1674104441576.png)

### Step 4:Create Salesforce Connection

In your Google Cloud project search for Integration Connector and click on create new connection.

![screenshots/16-1674104441568.png](screenshots/16-1674104441568.png)

Specify the location where your connection will run. Here I’m specifying it as us-west1.

![screenshots/17-1674104441397.png](screenshots/17-1674104441397.png)

Provide the Connection Details(connector, version, connection name, description and service account)

![screenshots/18-1674104441465.png](screenshots/18-1674104441465.png)

Make sure to grant the following roles to the service account that you want to use to create the connection:

* roles/secretmanager.viewer  
* roles/secretmanager.secretAccessor

Click Next and provide destination details. In this case I’m using a trial account and it will default to [https://login,salesforce](https://login,salesforce/) .com. If you are using a custom domain then you need to specify the custom domain login endpoint. 

![screenshots/19-1674104441357.png](screenshots/19-1674104441357.png)

Under Authentication click on OAuth 2.0-JWT Bearer and provide Connected App Client ID under Connected App Consumer Key, The username that is associated with the connected app you created in salesforce and create the secret manager secret by providing the private key generated in the Step 1\. 

![screenshots/20-1674104441547.png](screenshots/20-1674104441547.png)

While creating the secret make sure to copy the complete content of the private key. Below is the expected format.

\-----BEGIN PRIVATE KEY-----

MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCf1nlnhQa5gYX

...

...

\-----END PRIVATE KEY-----

![screenshots/21-1674104441439.png](screenshots/21-1674104441439.png)

Review the connection and create. This might take a couple of minutes to create the connection and activate it. 

![screenshots/22-1674104441492.png](screenshots/22-1674104441492.png)

To verify the connection, you can go to Salesforce-\>Settings-\>Setup-\>Users-\>My user and click on the user email id and scroll down to the login history.

![screenshots/23-1674104441527.png](screenshots/23-1674104441527.png)

Now, you should have the connection status as active under connections. 

![screenshots/24-1674104441495.png](screenshots/24-1674104441495.png)  

