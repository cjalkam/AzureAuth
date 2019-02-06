# AzureAuth

AzureAuth provides Azure Active Directory (AAD) authentication functionality for R users of Microsoft's Azure cloud. Use this package to obtain OAuth 2.0 tokens for Azure services including Azure Resource Manager, Azure Storage and others.

You can install the development version of the package from GitHub, with `devtools::install_github("cloudyr/AzureAuth")`.

## Obtaining tokens

The main function in AzureAuth is `get_azure_token`, which obtains an OAuth token from AAD. The token is cached in a user-specific directory using the [rappdirs](https://github.com/r-lib/rappdirs) package, and future requests will use the cached token without needing you to reauthenticate.

```r
library(AzureAuth)

token <- get_azure_token(resource="myresource", tenant="contoso", app="app_id", ...)
```

Other supplied functions include `list_azure_tokens`, `delete_azure_token` and `clean_token_directory`, to let you manage the token cache.

AzureAuth supports four distinct methods for authenticating with AAD: **authorization_code**, **device_code**, **client_credentials** and **resource_owner**.

1. Using the **authorization_code** method is a 3-step process. First, `get_azure_token` contacts the AAD authorization endpoint to obtain a temporary access code. It then contacts the AAD access endpoint, passing it the code. The access endpoint sends back a login URL which `get_azure_token` opens in your browser, where you can enter your credentials. Once this is completed, the endpoint returns the OAuth token via a HTTP redirect URI.

2. The **device_code** method is similar in concept to authorization_code, but is meant for situations where you are unable to browse the Internet -- for example if you don't have a browser installed or your computer has input constraints. First, `get_azure_token` contacts the AAD devicecode endpoint, which responds with a login URL and an access code. You then visit the URL and enter the code, possibly using a different computer. Meanwhile, `get_azure_token` polls the AAD access endpoint for a token, which is provided once you have successfully entered the code.

3. The **client_credentials** method is much simpler than the above methods, requiring only one step. `get_azure_token` contacts the access endpoint, passing it the app secret (which you supplied in the `password` argument). Assuming the secret is valid, the endpoint then returns the OAuth token.

4. The **resource_owner** method also requires only one step. In this method, `get_azure_token` passes your (personal) username and password to the AAD access endpoint, which validates your credentials and returns the token.

Methods 1 and 3 are currently provided via the httr `Token2.0` class, while methods 2 and 4 are new in this package.

---
[![cloudyr project logo](https://i.imgur.com/JHS98Y7.png)](https://github.com/cloudyr)

