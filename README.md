
# Full-Stack Cloud-Native Application for Best Buy

## **Overview**

This project is a cloud-native application developed for Best Buy's online store using microservices architecture, Kubernetes, and Azure-managed services. The application follows the design principles of the **Algonquin Pet Store (On Steroids)** with a key modification: the **Order Queue Service** is implemented using **Azure Service Bus** instead of RabbitMQ.

The application includes several services for different user interactions, such as browsing products, managing orders, and generating AI-powered product descriptions and images using GPT-4 and DALL-E.

---

## **Architecture Diagram**

![Architecture Diagram](8915Final.jpg)

---

## **Application Architecture**

The application architecture is designed using the following microservices:

- **Store-Front**: Customer-facing app for browsing and placing orders.
- **Store-Admin**: Employee-facing app for managing products and viewing orders.
- **Order-Service**: Handles order creation and sends data to the managed order queue (Azure Service Bus).
- **Product-Service**: Handles CRUD operations for product data.
- **Makeline-Service**: Processes and completes orders by reading from the order queue.
- **AI-Service**: Generates product descriptions and images using GPT-4 and DALL-E models.
- **Database**: MongoDB for persisting order and product data.
- **Virtual-customer**: Simulates order creation on a scheduled basis (Rust).
- **Virtual-worker**: Simulates order completion on a scheduled basis (Rust).
  
---

## **Application and Architecture Explanation**

The **Best Buy App** leverages a microservices architecture to separate the concerns of different services such as product management, order management, and order fulfillment. The key components include:

- **Frontend**: The **Store-Front** is the customer-facing application where users can browse products and place orders.
- **Backend**: The **Order-Service** handles order creation and interacts with the **Order Queue Service** (Azure Service Bus) to ensure smooth order processing. The **Makeline-Service** listens to the queue and completes the orders.
- **Product Data**: The **Product-Service** manages CRUD operations for products stored in MongoDB.
- **AI Integration**: The **AI-Service** generates AI-powered product descriptions and images using **Azure OpenAI Services** (GPT-4 and DALL-E).

---

## **Table of Microservice Repositories**

| **Service**         | **Repository Link**                                                                 |
|---------------------|--------------------------------------------------------------------------------------|
| Store-Front         | [store-front-bestbuy](https://github.com/zhao0294/store-front-bestbuy)              |
| Store-Admin         | [store-admin-bestbuy](https://github.com/zhao0294/store-admin-bestbuy)              |
| Order-Service       | [order-service-bestbuy](https://github.com/zhao0294/order-service-bestbuy)          |
| Product-Service     | [product-service-bestbuy](https://github.com/zhao0294/product-service-bestbuy)      |
| Makeline-Service    | [makeline-service-bestbuy](https://github.com/zhao0294/makeline-service-bestbuy)    |
| AI-Service          | [ai-service-service](https://github.com/zhao0294/ai-service-service)                |
| Mongodb(Database)   | [mongo](https://github.com/docker-library/mongo)                                    |
| Virtual-customer    | [virtual-customer-bestbuy](https://github.com/zhao0294/virtual-customer-bestbuy)    |
| Virtual-worker      | [virtual-worker-bestbuy](https://github.com/zhao0294/virtual-worker-bestbuy)        |

---

## **Table of Docker Images**

| **Service**         | **Docker Image Link**                     |
|---------------------|-------------------------------------------|
| Store-Front         | [congzhao0294/store-front:latest](https://hub.docker.com/r/congzhao0294/store-front)                     |
| Store-Admin         | [congzhao0294/store-admin:latest](https://hub.docker.com/r/congzhao0294/store-admin)                     |
| Order-Service       | [congzhao0294/order-service:latest](https://hub.docker.com/r/congzhao0294/order-service)                      |
| Product-Service     | [congzhao0294/product-service:latest](https://hub.docker.com/r/congzhao0294/product-service)                       |
| Makeline-Service    | [congzhao0294/makeline-service:latest](https://hub.docker.com/r/congzhao0294/makeline-service)                   |
| AI-Service          | [congzhao0294/ai-service:latest](https://hub.docker.com/r/congzhao0294/ai-service)                     |

---

## **Deployment Instructions**

### **Prerequisites**

- Kubernetes cluster (can be set up using Azure AKS or any other Kubernetes provider).
- Docker for building and pushing images.
- Azure account for using Azure Service Bus and Azure OpenAI Services.

### **Steps to Deploy**

1. **Clone the Repository**

    ```bash
    git clone <your-repo-link>
    cd <your-repo-name>
    ```

2. **Use the Azure portal to create an Azure Service Bus**

#### 2.1 Create a Resource Group (Skip if already created)

- Go to [Azure Portal](https://portal.azure.com).
- Search for **"Resource groups"** and open the service.
- Click **+ Create**.
- Fill in:
  - **Resource Group Name**: e.g., `CST8915FinalProject`
  - **Region**: e.g., `Canada Central`
- Click **Review + Create** → **Create**

#### 2.2 Create a Service Bus Namespace

- Search for **"Service Bus"** in the Azure Portal and open the service.
- Click **+ Create**.
- Fill in the required fields:
  - **Subscription**
  - **Resource Group**: Select the one created above
  - **Namespace Name**: e.g., `bestbuynamespace`
  - **Region**
  - **Pricing Tier**: Choose **Standard** or **Premium**
- Click **Review + Create** → **Create**

#### 2.3 Create a Queue

- Go to your **Service Bus Namespace**.
- In the left menu, select **Queues**.
- Click **+ Queue**.
- Fill in:
  - **Queue Name**: e.g., `orders`
- Click **Create**

#### 2.4 Create sender and listener policies

- Click on the queue you just created
- Select **Shared access policies** in the left menu
- Click **+ Add**
  - **Policy name**: `sender`
  - Permissions: Check `Send`
  - Click **Create**
- Click + Add again
  - **Policy name**: `listener`
  - Permissions: Check `Listen`
  - Click **Create**

You can click on each policy to view its Primary key and Connection string for subsequent encryption and configuration.

#### 2.5 Modify Environment Variables in **config-maps.yaml**

Modify the **ORDER_QUEUE_HOSTNAME** field in `metadata` `order-service-config` in **config-maps.yaml**. The field is found under Namespace `Overview` → `Essentials` → `Host name`.
Modify the **ORDER_QUEUE_URI** field in `metadata` `makeline-service-config` in **config-maps.yaml**. The format of this field is: `amqps://<hostname>`, the field `hostname` is found under Namespace `Overview` → `Essentials` → `Host name`.

#### 2.6 Modify Environment Variables in **secrets.yaml**

In secrets.yaml, as the type of the environment variable is set to data, base64-encoded must be filled in. You can use:

```bash
echo -n “string-need-to-base64-encoded” | base64
```

replace `string-need-to-base64-encoded` with the field you need to encrypt, and fill the output of the command into the environment variable that needs to be filled in.

- The **ORDER_QUEUE_USERNAME** field is filled with the encrypted field of the `sender`, and the **ORDER_QUEUE_PASSWORD** field is filled with the encrypted field of the `Primary key`. You can view the Primary key in this path: `Shared access policies` → `sender` → `Primary key`.
- The **ORDER_QUEUE_LISTENER_USERNAME** field is filled with the encrypted field of the `listener`, and the **ORDER_QUEUE_LISTENER_PASSWORD** field is filled with the encrypted field of the `Primary key`. You can view the Primary key in this path: `Shared access policies` → `listener` → `Primary key`.

3. **Use the Azure portal to create Azure OpenAI Service**

#### 3.1 Create an Azure OpenAI servcie

- Go to [Azure Portal](https://portal.azure.com).
- Search for “Kubernetes services” and open the service.
- Fill in the Basics tab:
  - **Resource group**: Select the Resource group you created in 2.1
  - **Region**: same as 2.2
  - **Pricing tier**
  - Click **Next** to continue
  - Click **create** to create

#### 3.2 Deploy gpt-4

- Click the OpenAI you just created
- Click **Explore Azure AI Foundry portal**
- Click **Model catalog**
- **search** `gpt-4` and then click **Deploy**
- click **Create resource and deploy**
- click **Open in playground** to test the deployment
  
#### 3.3 Deploy dall-e-3

- Click **Model catalog**
- **search** `dall-e-3` and then click **Deploy**
- click **Create resource and deploy**
- click **Open in playground** to test the deployment

#### 3.4 Connect to the Azure OpenAI

Modify the environment variable in `ai-service` in `aps-all-in-one.yaml`. Set **AZURE_OPENAI_ENDPOINT** and **AZURE_OPENAI_DALLE_ENDPOINT** to the `Azure Openai Service endpoint`. You can copy the string in **Home**. You can view the corresponding `names` of **AZURE_OPENAI_DEPLOYMENT_NAME** and **AZURE_OPENAI_DALLE_DEPLOYMENT_NAME** in **Deployment**.

Check **API key 1** in **Home**, encrypt it using base64, and fill it in the **OPENAI_API_KEY** field in `openai-api-secret` in `secrets.yaml`.

4. **Use the Azure portal to create Azure Kubernetes Clusters**

#### 4.1 Create a Kubernetes Cluster

- Go to [Azure Portal](https://portal.azure.com).
- Search for “Azure OpenAI” and open the service.
- Click **+ Create Azure OpenAI**.
- Fill in the Basics tab:
  - **Subscription**
  - **Resource Group**: Select the Resource group you created in 2.1
  - **Cluster Name**: e.g., bestbuyakscluster
  - **Region**: e.g., Canada Central
  - **Kubernetes version**: Select a stable version (e.g., 1.29.x)
  - Click **Next** through the tabs, accepting defaults or adjusting as needed.

#### 4.2 Node Pool Configuration

- In the Node pools tab:
- Set up a `systemnodes` and choose the appropriate node size
  - **Scale method**: Manual
  - **Node count**: 1
- Set up two `workernodes` and choose the appropriate node size
  - **Scale method**: Manual
  - Node count: 2

#### 4.3 Review and Create

- Click **Review + Create**
  - After validation, click **Create**

#### 4.4 Connect to the Azure Kubernetes Cluster

- Click the Kubernetes service you just created
- Click **Connect** on the top
- Click **Azure CLI**
- Connect to the Kubernets service according to the steps
  
#### 4.5 Verfiy the connetion

- use the command below:
  
  ```bash
  kubectl get nodes
  ```

  you will see the output like this:

  ```text
    NAME                                STATUS   ROLES   AGE     VERSION
    aks-systemnodes-xxxxx-vmss000000    Ready    agent   10m     v1.29.x
    aks-workernodes-xxxxx-vmss000001    Ready    agent   10m     v1.29.x
    aks-workernodes-xxxxx-vmss000002    Ready    agent   10m     v1.29.x
  ```

5. **Deploy aps-all-in-one.yaml**

- Deploy secrets.yaml
  use the command below:

  ```bash
  kubectl apply -f secrets.yaml
  ```

- Deploy config-maps.yaml
  use the command below:

  ```bash
  kubectl apply -f config-maps.yaml
  ```

- Deploy aps-all-in-one.yaml
  use the command below:

  ```bash
  kubectl apply -f config-maps.yaml
  ```
  
6. **Verify the Deployment**

- Check the **Workloads** at **Kubernetes resources**
- Check the **services and ingressess**
- Open the website of **store-front** and **store-admin**
- Use the website to make some orders and check the function

7. **Verify the CI/CD**

I deployed CI/CD on the following six microservices: order-service, product-service, makeline-service, store-front, store-admin, and ai-servcie.There is a `.github/workflows` folder in the root directory of each microservice, which contains `ci_cd.yaml`.
We can modify one of the configurations (such as store-admin, other microservcies are similar) and verify the results of CI/CD after clicking commit.

---

## **Reminder**

Please delete all resources created in this experiment.
