---
layout: post
title: Using Azure Bearer Tokens In Postman
category: Postman
tags: [how-to]
---

There was a recent change in an application I've been working on to move away from basic authentication for service to service communication locally in favor of an Azure-backed Managed Identity that stores the credentials.

This meant all the previously used Postman scripts had to be updated to reflect the change in Authentication. Unfortunately, the new authentication method used a temporary bearer token that had to be regenerated after a short while. Meaning the postman scripts had to keep being updated with a new bearer token. The workflow was thus:

1. Generate a new bearer token using the `az` [command-line tool](https://docs.microsoft.com/en-us/cli/azure/)
2. Copy this value and put it into postman
3. Continue making requests until the previous token expired, then go back to 1

## Automatically Generating The Token
The first thought was to use the [Azure Rest Api](https://docs.microsoft.com/en-us/rest/api/azure/) to retrieve the Bearer token. Unfortunately, this was unable to work as it required registering a new application inside of Azure to associate these keys with, and Azure access to perform this was not available at the time.

Had this been viable, it would have been easy enough to simply call the Rest Api with the app credentials and retrieve a token each time before making a request.
### Creative Workaround Using Node
Instead, a workaround would need to be performed using the `az` command-line tool. However, Postman only supports javascript and Http requests in the Pre-Request Scripts feature. An approach would have to convert the command line call to an Http call. 

Fortunately, node handles this in a very straightforward and simple way. Using the built-in `child_process` module, an arbitrary command can be run on the server. Putting this together, the below script spins up a node server that returns the bearer token:

```js
const http = require('http'),
  { exec } = require('child_process');

http.createServer((_req, res) => {
  var get_token = "az account get-access-token --query 'accessToken' -o tsv --resource <Application Id Uri>";
  exec(get_token, (err, stdout, _stderr) => {
    if (err) {
      return res.writeHead(500).end(JSON.stringify(err));
    }

    // Output of the script in stdout
    res.end(stdout);
  });
// An unused port to run on
}).listen(7200);
```

Once the above is running, it is only a matter of retrieving this data in Postman. To do that, each request needs to be updated with the following:
{% raw %}
1. Change the Authorization to `Bearer Token` and the token be the global variable `{{bearer_token}}`.
2. Add this script to the Pre-Request Script section to retrieve the bearer token and assign it to the global variable.
{% endraw %}

```js
pm.sendRequest('http://localhost:7200', (_, response) => {
    // This will have the output of the az CLI
    pm.globals.set('bearer_token', response.text().trim());
})
```

This allows each postman request to retrieve a token and make its request properly.