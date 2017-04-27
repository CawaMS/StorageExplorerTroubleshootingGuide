---
title: Storage Explorer Troubleshooting Guide | Microsoft Docs
description: Common issues encountered when using Storage Explorer and solutions to resolve the issues
services: storage
documentationcenter: ''
author:
manager:
editor:

ms.assetid:
ms.service: storage explorer
ms.workload: storage explorer
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date:
ms.author:

---

# Storage Explorer Troubleshooting Guide

## Overview
[Microsoft Azure Storage Explorer (Preview)](http://storageexplorer.com/) is a standalone app that allows you to easily work with Azure Storage data on Windows, macOS and Linux. The app can connect to Storage accounts hosted on Azure, Sovereign Clouds, and Azure Stack.

This guide summarizes solutions for common issues seen in Storage Explorer.

* [Sign in Issues]
    * [Self-Signed Certificate in Certificate Chain]
    * [Unable to retrieve subscriptions]
    * [Unable to see the authentication page]
    * [Cannot remove account]
* [Proxy Issues]
    * [Common solutions]
    * [Tools for diagnosing issues]
    * [Contact proxy server admin]
* [“Unable to retrieve children” Error Message]
    * [Issues with SAS URL]
* [Submit your issue to our Feedback Tool]

## <a name="sign-in-issues"></a>Sign in Issues
Before proceeding further, try restarting your application and see if the problems can be fixed.

### <a name="self-signed-certificate-in-certificate-chain"></a>Self-Signed Certificate in Certificate Chain
There a few reasons you may be seeing this error, the two most common ones are:
1.	You are behind a “transparent proxy”, which means someone (such as your IT department) is intercepting HTTPS traffic, decrypting it, and then encrypting it using a self-signed certificate
2.	You are running software, such as anti-virus software, which is injecting a self-signed SSL certificates into the HTTPS messages you receive

When Storage Explorer encounters one of these "self-signed certificates", it can no longer know if the HTTPS message it is receiving has been tampered with. If you have a copy of the self-signed certificate though, then you can tell Storage Explorer to trust it. If you are unsure of who is injecting the certificate, then you can try to find it yourself by doing the following:

1.	Install Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (any of the light versions should suffice)
    * Mac and Linux: Should be included with your operating system
2. Run Open SSL
    * Windows: Go to the install directory, then /bin/, then double click on openssl.exe
    * Mac and Linux: execute "openssl" from a terminal
3.	Execute `s_client -showcerts -connect microsoft.com:443`
4.	Look for self-signed certificates. If you're unsure which are self-signed, then look for any where the subject ("s:") and issuer ("i:") are the same. 
5.	Once you have found any self-signed certificates, then for each one, copy and paste everything from and including `-----BEGIN CERTIFICATE-----` to `-----END CERTIFICATE-----` to a new .cer file.
6.	Open Storage Explorer and then go to Edit -> SSL Certificates -> Import Certificates. Using the file picker, find, select, and open the .cer files you created.

If you are unable to find any self-signed certificates using the above steps, then reach out to us via the feedback tool for more help.

### <a name="unable-to-retrieve-subscriptions"></a>Unable to retrieve subscriptions
If you are unable to retrieve your subscriptions after you successfully signed in:
* Verify your account has access to the subscriptions by signing into the [Azure Portal](http://portal.azure.com/)
* Make sure you have signed in using the correct environment ([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/), or Custom Environment/Azure Stack)
* If you are behind a proxy, make sure that you have configured the Storage Explorer proxy properly
* Try removing and re-adding the account
* Try deleting the following files from your home directory (i.e. C:\Users\ContosoUser), and then re-adding the account:
    * .adalcache
    * .devaccounts
    * .extaccounts
* Watch the developer tools console (f12) while signing in for any error messages

![][0]

### <a name="unable-to-see-auth-page"></a>Unable to see the authentication page
If you are unable to see the authentication page:
* Depending on the speed of your connection, it may take a while for the sign in page to load, wait at least 1 minute before closing the authentication dialog
* If you are behind a proxy, make sure that you have configured the Storage Explorer proxy properly
* Bring up the developer console by pressing F12 key. Watch the responses from developer console and see if you can find any clue for why authentication is not working

### <a name="unable-to-remove-account"></a>Cannot remove account
If you are unable to remove an account, or if the re-authenticate link does not do anything
* Try deleting the following files from your home directory, and then re-adding the account:
    * .adalcache
    * .devaccounts
    * .extaccounts
* If you want to remove SAS attached Storage resources, delete:
    * %AppData%/StorageExplorer folder for Windows
    * /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer for Mac
    * ~/.config/StorageExplorer for Linux
    * **You will have to reenter all your credentials** if you delete these files



## <a name="proxy-issues"></a>Proxy Issues
First of all, please make sure the following information you entered are all correct:
    * The proxy URL and port number
    * Username and password if required by the proxy

### <a name="common-solutions"></a>Common solutions
If you are still experiencing issues:
* If you can connect to the internet without using your proxy, verify that Storage Explorer works without proxy settings enabled. If so, there may be an issue with your proxy settings. Please work with your proxy administrator to identify the problems.
* Verify that other applications using the proxy server work as expected
* Verify that you can connect to the Microsoft Azure portal using your web browser
* Verify that you can receive responses from your service endpoints. Enter one of your endpoint URLs into your browser. If you can connect, you should receive an InvalidQueryParameterValue or similar XML response
* If someone else is also using Storage Explorer with your proxy server, verify that they are able to connect. If they can connect, you may need to contact your proxy server admin

### <a name="tools"></a>Tools for diagnosing issues
If you have networking tools, such as Fiddler for Windows, you may be able to diagnose the problems:
* If you need to work through your proxy, you may need to configure your networking tool to connect through the proxy.
* Check the port number used by your networking tool.
* Enter the local host URL and the networking tool's port number as proxy settings in Storage Explorer. If done correctly, your networking tool will start logging network requests made by Storage Explorer to management and service endpoints. For example, enter *https://cawablobgrs.blob.core.windows.net/* for your blob endpoint in a browser and you will receive a response similar to below, which suggests the resource exists, although you can't access it

![][2]


### <a name="contact-admin"></a>Contact proxy server admin
If your proxy settings are correct, you may need to contact your proxy server admin
* Make sure that your proxy doesn't block traffic to Azure management or resource endpoints.
* Verify the authentication protocol used by your proxy server. Storage Explorer does not currently support NTLM proxies.

## <a name="unable-to-retrieve-children"></a>"Unable to Retrieve Children" Error Message
If you are connected to Azure through a proxy, verify that your proxy settings are correct. If you were granted access to a resource from the owner of the subscription or account, verify that you have read or list permissions for that resource.

### <a name="sas-url"></a>Issues with SAS URL
If you are connecting to a service using a SAS URL and experiencing this error:
    * Verify that the URL provides the necessary permissions to read or list resources.
	* Verify that the URL has not expired.
	* If the SAS URL is based on an access policy, verify that the access policy has not been revoked.

## <a name="submit-issues"></a>Submit Your Issue to our Feedback tool
If none of the solutions work for you, submit your issue via the feedback tool with your email included and as much details about the issue as you can, so we can contact you for fixing the issues.

![][1]


<!--Anchors-->
[Sign in Issues]: #sign-in-issues
[Self-Signed Certificate in Certificate Chain]: #self-signed-certificate-in-certificate-chain
[Unable to retrieve subscriptions]: #unable-to-retrieve-subscriptions
[Unable to see the authentication page]:#unable-to-see-auth-page
[Cannot remove account]: #unable-to-remove-account
[Proxy Issues]: #proxy-issues
[Common solutions]: #common-solutions
[Tools for diagnosing issues]: #tools
[Contact proxy server admin]: #contact-admin
[“Unable to retrieve children” Error Message]: #unable-to-retrieve-children
[Issues with SAS URL]: #sas-url
[Submit your issue to our Feedback Tool]: #submit-issues

<!--Image references-->
[0]: ./media/se-troubleshooting-guide/f12.png
[1]: ./media/se-troubleshooting-guide/send-feedback.png
[2]: ./media/se-troubleshooting-guide/blob-endpoint.png
