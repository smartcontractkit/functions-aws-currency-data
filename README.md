# Chainlink Functions <> Amazon Web Services Data Exchange Sample Application

This use case involves creating a universal connector Chainlink Function that can be used to connect to any [AWS Data Exchange data](https://aws.amazon.com/data-exchange/), allowing developers to seamlessly integrate third-party data in AWS together with smart contracts. This enables the creation of advanced Web3 applications that can make use of the vast array of data sets available in AWS.

In this specific example, the universal connector will be used to connect and obtain currency exchange data from the [Rearc Currency Exchange API](https://aws.amazon.com/marketplace/pp/prodview-eufl3hymozfae?sr=0-1&ref_=beagle&applicationId=AWSMPContessa#offers), then return the data to an on-chain smart contract.

Chainlink Functions allows users to request data from almost any API and perform custom computation using JavaScript. This project is currently in a closed beta. Request access to use Chainlink Functions at https://functions.chain.link

## Requirements

- Node.js version [18](https://nodejs.org/en/download/)

## Instructions to run this sample

1. If you don't already have one, sign up for an account at [AWS](https://aws.amazon.com/) (you can use a free-tier)
2. Subscribe to the [Currency Exchange API](https://aws.amazon.com/marketplace/pp/prodview-eufl3hymozfae?sr=0-1&ref_=beagle&applicationId=AWSMPContessa#offers) Rearc data provider
3. Clone this repository to your local machine<br>
4. Open this directory in your command line, then run `npm install` to install all dependencies.<br>
5. Set the required environment variables.

   This repo uses the NPM package `@chainlink/env-enc` for keeping environment variables such as wallet private keys, RPC URLs, and other secrets encrypted at rest. This reduces the risk of credential exposure by ensuring credentials are not visible in plaintext.

   By default, all encrypted environment variables will be stored in a file named `.env.enc` in the root directory of this repo.

   > **Warning**
   >
   > **DO NOT COMMIT YOUR `.env.enc` FILE**

   1. Aquire a Github personal access token which allows reading and writing Gists

   - Visit https://github.com/settings/tokens?type=beta and click "Generate new token"
   - Name the token and enable read & write access for Gists from the "Account permissions" drop-down menu. Do not enable any additional permissions.
   - Click "Generate token" and copy the resulting personal access token for the next step

   2. Set an encryption password for your environment variables to a secure password

   ```
   npx env-enc set-pw
   ```

   The password must be set at the beginning of each new session. If this password is lost, there will be no way to recover the encrypted environment variables.

   3. To set the required environment variables use the following command

   ```
   npx env-enc set
   ```

   Run the command `npx env-enc set` to set and save environment variables. These variables will be loaded into your environment when the `config()` method is called at the top of `hardhat.config.js`. Use `npx env-enc view` to view all currently saved environment variables. When pressing _ENTER_, the terminal will be cleared to prevent these values from remaining visible. Running `npx env-enc remove VAR_NAME_HERE` deletes the specified environment variable. The command `npx env-enc remove-all` deletes the entire saved environment variable file.

   Then, set the following values:

   - _PRIVATE_KEY_ for your development wallet
   - _MUMBAI_RPC_URL_ or _SEPOLIA_RPC_URL_ for the network that you intend to use

   4. If desired, the _ETHERSCAN_API_KEY_ or _POLYGONSCAN_API_KEY_ can be set in order to verify contracts, along with any values used in the _secrets_ object in _Functions-request-config.js_ such as _COINMARKETCAP_API_KEY_.<br><br>
   5. You will also need to set the following additional AWS Environment Variables. Please see [this guide](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.html) for setting up a secret key and access key. To obtain the DataSet ID, Reivion ID and Asset ID, select 'Entitled Data' from the left nativation panel on the AWS Data Exchange console. Choose the Currency Exchange API | Rearc, and then choose the Currency Exchange API | Rearc data set. Expand the API data set to see the Revisions and API assets. Use the latest revision. Values for Data set ID, Revision ID, and Asset ID are located in the Asset overview section<br>

   ```
     SECRET_KEY="insert AWS secret key"
     ACCESS_KEY="insert AWS access key"
     DATASET_ID="8d76790e3d69d9610a9288aa75069592"
     REVISION_ID="6ffe379389e039941bbbdaa879b932e2"
     ASSET_ID="a670e5e4fb46e536e457efc0adcdb310"%
   ```

6. Test an end-to-end request and fulfillment locally by simulating it using:<br>`npx hardhat functions-simulate`<br>
7. Deploy and verify the smart contract to an actual blockchain network by running:<br>`npx hardhat functions-deploy-client --network network_name_here --verify true`<br>**Note**: Make sure _ETHERSCAN_API_KEY_ or _POLYGONSCAN_API_KEY_ are set if using `--verify true`, depending on which network is used.<br><br> Network_name_here should be replaced with the network you are deploying to (Sepolia or Mumbai) in this step, as well as all steps after this one
8. Create, fund & authorize a new Functions billing subscription by running:<br> `npx hardhat functions-sub-create --network network_name_here --amount LINK_funding_amount_here --contract 0xDeployed_client_contract_address_here`<br>**Note**: Ensure your wallet has a sufficient LINK balance before running this command. Testnet LINK can be obtained at <a href="https://faucets.chain.link/">faucets.chain.link</a>.<br><br> A suitable amount of LINK to fund for most requests is 0.5 - 1 LINK. You should replace 0xDeployed_client_contract_address_here with your deployed contract address from the previous step.
9. Make an on-chain request by running:<br>`npx hardhat functions-request --network network_name_here --contract 0xDeployed_client_contract_address_here --subid subscription_id_number_here`, replacing subscription_id_number_here with the subscription ID you received from the previous step
10. Read the result in the on-chain smart contract by running :<br>`npx hardhat functions-read --contract 0xDeployed_client_contract_address_here`<br>

# Functions Command Glossary

The Functions and Functions subscription management commands commands can be executed in the following format:
`npx hardhat command_here --parameter1 parameter_1_value_here --parameter2 parameter_2_value_here`

Example: `npx hardhat functions-read --network polygonMumbai --contract 0x787Fe00416140b37B026f3605c6C72d096110Bb8`

## Functions Commands

| Command                            | Description                                                                                                                      | Parameters                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `compile`                          | Compiles all smart contracts                                                                                                     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `functions-simulate`               | Simulates an end-to-end fulfillment locally for the _FunctionsConsumer_ contract                                                 | `gaslimit` (optional): Maximum amount of gas that can be used to call _fulfillRequest_ in the client contract (defaults to 100,000 & must be less than 300,000)                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `functions-deploy-client`          | Deploys the _FunctionsConsumer_ contract                                                                                         | `network`: Name of blockchain network, `verify` (optional): Set to `true` to verify the deployed _FunctionsConsumer_ contract (defaults to `false`)                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `functions-request`                | Initiates a request from a _FunctionsConsumer_ client contract using data from _Functions-request-config.js_                     | `network`: Name of blockchain network, `contract`: Address of the client contract to call, `subid`: Billing subscription ID used to pay for the request, `gaslimit` (optional): Maximum amount of gas that can be used to call _fulfillRequest_ in the client contract (defaults to 100,000 & must be less than 300,000), `requestgas` (optional): Gas limit for calling the _executeRequest_ function (defaults to 1,500,000), `simulate` (optional): Flag indicating if simulation should be run before making an on-chain request (defaults to true)                                                              |
| `functions-read`                   | Reads the latest response (or error) returned to a _FunctionsConsumer_ or _AutomatedFunctionsConsumer_ client contract           | `network`: Name of blockchain network, `contract`: Address of the client contract to read                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `functions-deploy-auto-client`     | Deploys the _AutomatedFunctionsConsumer_ contract and sets the Functions request using data from _Functions-request-config.js_   | `network`: Name of blockchain network, `subid`: Billing subscription ID used to pay for Functions requests, `gaslimit` (optional): Maximum amount of gas that can be used to call _fulfillRequest_ in the client contract (defaults to 250000), `interval` (optional): Update interval in seconds for Chainlink Automation to call _performUpkeep_ (defaults to 300), `verify` (optional): Set to `true` to verify the deployed _AutomatedFunctionsConsumer_ contract (defaults to `false`), `simulate` (optional): Flag indicating if simulation should be run before making an on-chain request (defaults to true) |
| `functions-check-upkeep`           | Checks if _checkUpkeep_ returns true for an Automation compatible contract                                                       | `network`: Name of blockchain network, `contract`: Address of the contract to check, `data` (optional): Hex string representing bytes that are passed to the _checkUpkeep_ function (defaults to empty bytes)                                                                                                                                                                                                                                                                                                                                                                                                        |
| `functions-perform-upkeep`         | Manually call _performUpkeep_ in an Automation compatible contract                                                               | `network`: Name of blockchain network, `contract`: Address of the contract to call, `data` (optional): Hex string representing bytes that are passed to the _performUpkeep_ function (defaults to empty bytes)                                                                                                                                                                                                                                                                                                                                                                                                       |
| `functions-set-auto-request`       | Updates the Functions request in deployed _AutomatedFunctionsConsumer_ contract using data from _Functions-request-config.js_    | `network`: Name of blockchain network, `contract`: Address of the contract to update, `subid`: Billing subscription ID used to pay for Functions requests, `interval` (optional): Update interval in seconds for Chainlink Automation to call _performUpkeep_ (defaults to 300), `gaslimit` (optional): Maximum amount of gas that can be used to call _fulfillRequest_ in the client contract (defaults to 250,000)                                                                                                                                                                                                 |
| `functions-set-oracle-addr`        | Updates the oracle address for a client contract using the _FunctionsOracle_ address from _network-config.js_                    | `network`: Name of blockchain network, `contract`: Address of the client contract to update                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `functions-build-request`          | Creates a JSON file with Functions request parameters including encrypted secrets, using data from _Functions-request-config.js_ | `network`: Name of blockchain network, `output` (optional): Output JSON file name (defaults to _Functions-request.json_), `simulate` (optional): Flag indicating if simulation should be run before building the request JSON file (defaults to true)                                                                                                                                                                                                                                                                                                                                                                |
| `functions-build-offchain-secrets` | Builds an off-chain secrets object that can be uploaded and referenced via URL                                                   | `network`: Name of blockchain network, `output` (optional): Output JSON file name (defaults to _offchain-secrets.json_)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

## Functions Subscription Management Commands

| Command                      | Description                                                                                                                              | Parameters                                                                                                                                                                                                                 |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `functions-sub-create`       | Creates a new Functions billing subscription for Functions client contracts                                                              | `network`: Name of blockchain network, `amount` (optional): Initial amount used to fund the subscription in LINK (decimals are accepted), `contract` (optional): Address of the client contract to add to the subscription |
| `functions-sub-info`         | Gets the Functions billing subscription balance, owner, and list of authorized client contract addresses                                 | `network`: Name of blockchain network, `subid`: Subscription ID                                                                                                                                                            |
| `functions-sub-fund`         | Funds a Functions billing subscription with LINK                                                                                         | `network`: Name of blockchain network, `subid`: Subscription ID, `amount`: Amount to fund subscription in LINK (decimals are accepted)                                                                                     |
| `functions-sub-cancel`       | Cancels a Functions billing subscription and refunds the unused balance. Cancellation is only possible if there are no pending requests. | `network`: Name of blockchain network, `subid`: Subscription ID, `refundaddress` (optional): Address where the remaining subscription balance is sent (defaults to caller's address)                                       |
| `functions-sub-add`          | Authorizes a client contract to use the Functions billing subscription                                                                   | `network`: Name of blockchain network, `subid`: Subscription ID, `contract`: Address of the client contract to authorize for billing                                                                                       |
| `functions-sub-remove`       | Removes a client contract from a Functions billing subscription                                                                          | `network`: Name of blockchain network, `subid`: Subscription ID, `contract`: Address of the client contract to remove from billing subscription                                                                            |
| `functions-sub-transfer`     | Request ownership of a Functions subscription be transferred to a new address                                                            | `network`: Name of blockchain network, `subid`: Subscription ID, `newowner`: Address of the new owner                                                                                                                      |
| `functions-sub-accept`       | Accepts ownership of a Functions subscription after a transfer is requested                                                              | `network`: Name of blockchain network, `subid`: Subscription ID                                                                                                                                                            |
| `functions-timeout-requests` | Times out expired requests                                                                                                               | `network`: Name of blockchain network, `requestids`: 1 or more request IDs to timeout separated by commas                                                                                                                  |

# Request Configuration

Chainlink Functions requests can be configured by modifying values in the `requestConfig` object found in the _Functions-request-config.js_ file located in the root of this repository.

| Setting Name         | Description                                                                                                                                                                                                                                                                                                                                                           |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `codeLocation`       | This specifies where the JavaScript code for a request is located. Currently, only the `Location.Inline` option is supported (represented by the value `0`). This means the JavaScript string is provided directly in the on-chain request instead of being referenced via a URL.                                                                                     |
| `codeLanguage`       | This specifies the language of the source code which is executed in a request. Currently, only `JavaScript` is supported (represented by the value `0`).                                                                                                                                                                                                              |
| `source`             | This is a string containing the source code which is executed in a request. This must be valid JavaScript code that returns a Buffer. See the [JavaScript Code](#javascript-code) section for more details.                                                                                                                                                           |
| `secrets`            | This is an object which contains secret values that are injected into the JavaScript source code and can be accessed using the name `secrets`. This object can only contain string values. This object will be automatically encrypted by the tooling using the DON public key before making request. Any DON member can use these secrets when processing a request. |
| `perNodeSecrets`     | This is an array of `secrets` objects that enables the optional ability to assign a separate set of secrets for each node in the DON. DON members can only use the set of secrets which they have been assigned.                                                                                                                                                      |
| `walletPrivateKey`   | This is the EVM private key. It is used to generate a signature for the encrypted secrets such that the secrets cannot be reused by an unauthorized 3rd party.                                                                                                                                                                                                        |
| `args`               | This is an array of strings which contains values that are injected into the JavaScript source code and can be accessed using the name `args`. This provides a convenient way to set modifiable parameters within a request.                                                                                                                                          |
| `expectedReturnType` | This specifies the expected return type of a request. It has no on-chain impact, but is used by the CLI to decode the response bytes into the specified type. The options are `uint256`, `int256`, `string`, or `Buffer`.                                                                                                                                             |
| `secretsURLs`        | This is an array of URLs where encrypted secrets can be fetched when a request is executed. This array is converted into a space-separated string, encrypted using the DON public key, and used as the `secrets` parameter on-chain. If any URLs are provided, automatic Gist uploading will be disabled in favor of the provided URLs.                               |

## JavaScript Code

The JavaScript source code for a Functions request can use vanilla Node.js features, but _cannot_ use any `require` statements or imported modules other than the built-in modules `buffer`, `crypto`, `querystring`, `string_decoder`, `url`, and `util`.

It must return a JavaScript Buffer which represents the response bytes that are sent back to the requesting contract.
Encoding functions are provided in the [Functions library](#functions-library).
Additionally, the script must return in **less than 10 seconds** or it will be terminated and send back an error to the requesting contract.

In order to make HTTP requests, the source code must use the `Functions.makeHttpRequest` function from the exposed [Functions library](#functions-library).
Asynchronous code with top-level `await` statements is supported, as shown in the file _API-request-example.js_.

### Functions Library

The `Functions` library is injected into the JavaScript source code and can be accessed using the name `Functions`.

In order to make HTTP requests, only the `Functions.makeHttpRequest` function can be used. All other methods of accessing the Internet are restricted.
The function takes an object with the following parameters.

```
{
  url: String with the URL to which the request is sent,
  method (optional): String specifying the HTTP method to use which can be either 'GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'HEAD', or 'OPTIONS' (defaults to 'GET'),
  headers (optional): Object with headers to use in the request,
  params (optional): Object with URL query parameters,
  data (optional): Object which represents the body sent with the request,
  timeout (optional): Number with the maximum request duration in ms (defaults to 5000 ms),
  responseType (optional): String specifying the expected response type which can be either 'json', 'arraybuffer', 'document', 'text' or 'stream' (defaults to 'json'),
}
```

The function returns a promise that resolves to either a success response object or an error response object.

A success response object will have the following parameters.

```
{
  error: false,
  data: Response data sent by the server,
  status: Number representing the response status,
  statusText: String representing the response status,
  headers: Object with response headers sent by the server,
}
```

An error response object will have the following parameters.

```
{
  error: true,
  message (may be undefined): String containing error message,
  code (may be undefined): String containing an error code,
  response (may be undefined): Object containing response sent from the server,
}
```

This library also exposes functions for encoding JavaScript values into Buffers which represent the bytes that a returned on-chain.

- `Functions.encodeUint256` takes a positive JavaScript integer number and returns a Buffer of 32 bytes representing a `uint256` type in Solidity.
- `Functions.encodeInt256` takes a JavaScript integer number and returns a Buffer of 32 bytes representing a `int256` type in Solidity.
- `Functions.encodeString` takes a JavaScript string and returns a Buffer representing a `string` type in Solidity.

Remember, it is not required to use these encoding functions. The JavaScript code must only return a Buffer which represents the `bytes` array that is returned on-chain.

## Modifying Contracts

Client contracts which initiate a request and receive a fulfillment can be modified for specific use cases. The only requirements are that the contract successfully calls _sendRequest_ in the _FunctionsOracle_ contract and correctly implements their own _handleOracleFulfillment_ function. At this time, the maximum amount of gas that _handleOracleFulfillment_ can use is 300,000. See _FunctionsClient.sol_ for details.

## Simulating Requests

An end-to-end request initiation and fulfillment can be simulated for the default _FunctionsConsumer_ contract using the `functions-simulate` command. This command will report the total estimated gas use.
If the _FunctionsConsumer_ client contract is modified, this task must also be modified to accomodate the changes. See `tasks/Functions-client/simulate` for details.

**Note:** The actual gas use on-chain can vary, so it is recommended to set a higher fulfillment gas limit when making a request to account for any differences.

## Off-chain Secrets

Instead of using encrypted secrets written directly on the blockchain, encrypted secrets are hosted off-chain and be fetched by DON nodes via HTTP when a request is initiated. This allows encrypted secrets to be deleted when they are no longer in use. By default, the tooling automatically uploads secrets to private Github Gists and deletes them once a request is fulfilled unless the secrets are being used for an `AutomatedFunctionsConsumer.sol` contract. If integrating with Chainlink Automation, it is recommended to delete the secrets Gist manually once it is not longer in use. Note that if there are URL(s) provided for the `secretsURLs` parameter in _Functions_request_config.js_, automatic Gist uploading will be disabled in favor of using the provided URL(s).

Additionally, per-node secrets allow a separate set of secrets to be assigned to each node in the DON. Each node will not be able to decrypt the set of secrets belonging to another node. Optionally, a set of default secrets encrypted with the DON public key can be used as a fallback by any DON member who does not have a set of secrets assigned to them. This handles the case where a new member is added to the DON, but the assigned secrets have not yet been updated.

To use per-node assigned secrets, enter a list of secrets objects into `perNodeSecrets` in _Functions-request-config.js_. The number of objects in the array must correspond to the number of nodes in the DON. Default secrets can be entered into the `secrets` parameter of `Functions-request-config.js`. Each secrets object must have the same set of entries, but the values for each entry can be different (ie: `[ { apiKey: '123' }, { apiKey: '456' }, ... ]`). If the per-node secrets feature is not desired, `perNodeSecrets` can be left empty and a single set of secrets can be entered for `secrets`.

If you prefer to host secrets elsewhere instead of having them automatically uploaded to a Github Gist, generate the encrypted secrets JSON file by running the command `npx hardhat functions-build-offchain-secrets --network network_name_here`. This will output the file _offchain-secrets.json_ which can be uploaded to any other hosting service that allows the JSON file to be fetched via URL.
Once the JSON file is uploaded, enter the URL(s) where the JSON file is hosted into `secretsURLs`. Multiple URLs can be entered as a fallback in case any of the URLs are offline. Each URL should host the exact same JSON file. The tooling will automatically pack the secrets URL(s) into a space-separated string and encrypt the string using the DON public key so no 3rd party can view the URLs. Finally, this encrypted string of URLs is used in the `secrets` parameter when making an on-chain request.

URLs which host secrets must be available every time a request is executed by DON nodes. For optimal security, it is recommended to expire the URLs when the off-chain secrets are no longer in use.
