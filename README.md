# Dapr pub/sub

In this quickstart, you'll create a publisher microservice and a subscriber microservice to demonstrate how Dapr enables a publish-subscribe pattern. The publisher will generate messages of a specific topic, while subscribers will listen for messages of specific topics. See [Why Pub-Sub](#why-pub-sub) to understand when this pattern might be a good choice for your software architecture.

For more details about this quickstart example please see the [Pub-Sub Quickstart documentation](https://docs.dapr.io/getting-started/quickstarts/pubsub-quickstart/).

Visit [this](https://docs.dapr.io/developing-applications/building-blocks/pubsub/) link for more information about Dapr and Pub-Sub.

> **Note:** This example leverages the Dapr client SDK.  If you are looking for the example using only HTTP `requests` [click here](../http).

### Prerequisites

The following prerequisites are required to use this application.  Please ensure that you have them all installed locally.

- [Azure Developer CLI](https://aka.ms/azure-dev/install)
  - Windows:
    ```powershell
    powershell -ex AllSigned -c "Invoke-RestMethod 'https://aka.ms/install-azd.ps1' | Invoke-Expression"
    ```
  - Linux/MacOS:
    ```
    curl -fsSL https://aka.ms/install-azd.sh | bash
    ```
- [Azure CLI (2.37.0+)](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Dapr CLI](https://docs.dapr.io/getting-started).
- [Latest Node.js (v14.16.1 +)](https://nodejs.org/download/).
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

## Quickstart

The fastest way for you to get this application up and running on Azure is to use the `azd up` command. This single command will create and configure all necessary Azure & local resources.

Run the following command to initialize the project, provision the core Azure resources, build the application code in containers and push to a private Azure Container Registry to create a working application.

```bash
azd up -t pubsub-dapr-nodejs-servicebus
```

## Dapr application overview

This sample includes one publisher:

- Node client message generator `checkout` 

And one subscriber: 
 
- Node subscriber `order-processor`


### Run Node message subscriber with Dapr

1. Install dependencies in a new terminal: 

<!-- STEP
name: Install Node dependencies
-->

```bash
cd ./order-processor
npm install
```
<!-- END_STEP -->
2. Run the Node subscriber app with Dapr: 

<!-- STEP
name: Run Node subscriber
expected_stdout_lines:
  - '== APP == Subscriber received: {"orderId":2}'
  - "Exited App successfully"
expected_stderr_lines:
working_dir: ./order-processor
output_match_mode: substring
background: true
sleep: 10
-->
    
```bash
dapr run --app-port 5001 --app-id order-processing --app-protocol http --dapr-http-port 3501 --components-path ../components -- npm run start
```

<!-- END_STEP -->

### Run Node message publisher with Dapr

3. Install dependencies in a new terminal: 

<!-- STEP
name: Install Node dependencies
-->

```bash
cd ./checkout
npm install
```
<!-- END_STEP -->
4. Run the Node publisher app with Dapr: 

<!-- STEP
name: Run Node publisher
expected_stdout_lines:
  - '== APP == Published data: {"orderId":2}'
  - '== APP == Published data: {"orderId":3}'
  - "Exited App successfully"
expected_stderr_lines:
working_dir: ./checkout
output_match_mode: substring
background: true
sleep: 10
-->
    
```bash
dapr run --app-id checkout --app-protocol http --components-path ../components -- npm run start
```

<!-- END_STEP -->

```bash
dapr stop --app-id checkout
dapr stop --app-id order-processor
```

5. Re-Deploy to Azure for dev-test

NOTE: make sure you have Azure Dev CLI pre-reqs [here](https://github.com/Azure-Samples/todo-python-mongo-aca)

```bash
azd deploy
```
