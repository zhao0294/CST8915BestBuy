
# Full-Stack Cloud-Native Application for Best Buy

## **Overview**

This project is a cloud-native application developed for Best Buy's online store using microservices architecture, Kubernetes, and Azure-managed services. The application follows the design principles of the **Algonquin Pet Store (On Steroids)** with a key modification: the **Order Queue Service** is implemented using **Azure Service Bus** instead of RabbitMQ.

The application includes several services for different user interactions, such as browsing products, managing orders, and generating AI-powered product descriptions and images using GPT-4 and DALL-E.

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

---

## **Architecture Diagram**

![Architecture Diagram](path/to/your/architecture-diagram.png)

---

## **Application and Architecture Explanation**

The **Best Buy App** leverages a microservices architecture to separate the concerns of different services such as product management, order management, and order fulfillment. The key components include:

- **Frontend**: The **Store-Front** is the customer-facing application where users can browse products and place orders.
- **Backend**: The **Order-Service** handles order creation and interacts with the **Order Queue Service** (Azure Service Bus) to ensure smooth order processing. The **Makeline-Service** listens to the queue and completes the orders.
- **Product Data**: The **Product-Service** manages CRUD operations for products stored in MongoDB.
- **AI Integration**: The **AI-Service** generates AI-powered product descriptions and images using **Azure OpenAI Services** (GPT-4 and DALL-E).

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

2. **Build Docker Images for All Services**
    - For each service, navigate to the service directory and build the Docker image.

    ```bash
    docker build -t <docker-hub-username>/<service-name>:<tag> .
    docker push <docker-hub-username>/<service-name>:<tag>
    ```

3. **Set Up Azure Service Bus**
    - Create an Azure Service Bus and obtain the connection string.
    - Replace the connection string in the Kubernetes secrets.

4. **Configure Kubernetes Manifests**
    - Modify the Kubernetes manifests for each service to use the Docker images and configure the necessary secrets and environment variables.
    - Deploy using `kubectl`:

    ```bash
    kubectl apply -f k8s/
    ```

5. **Verify the Deployment**
    - Check the status of the deployed services:

    ```bash
    kubectl get pods
    kubectl get services
    ```

---

## **Table of Microservice Repositories**

| **Service** | **Description** | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [store-front](https://github.com/ramymohamed10/store-front-L8) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [store-admin](https://github.com/ramymohamed10/store-admin-L8) |
| `order-service` | This service is used for placing orders (Javascript) | [order-service](https://github.com/zhao0294/order-service-bestbuy) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [product-service](https://github.com/ramymohamed10/product-service-L8) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [makeline-service](https://github.com/zhao0294/makeline-service-bestbuy) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [ai-service](https://github.com/ramymohamed10/ai-service-L8) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [virtual-customer](https://github.com/ramymohamed10/virtual-customer-L8) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [virtual-worker](https://github.com/ramymohamed10/virtual-worker-L8) |

---

## **Table of Docker Images**

| **Service**         | **Docker Image Link**                     |
|---------------------|-------------------------------------------|
| Store-Front         | `<Docker Hub Link>`                       |
| Store-Admin         | `<Docker Hub Link>`                       |
| Order-Service       | `[congzhao0294/order-service]https://hub.docker.com/r/congzhao0294/order-service`                       |
| Product-Service     | `<Docker Hub Link>`                       |
| Makeline-Service    | `[congzhao0294/makeline-service]https://hub.docker.com/r/congzhao0294/makeline-service`                       |
| AI-Service          | `<Docker Hub Link>`                       |
| Database            | `<Docker Hub Link>`                       |

---

## **Issues and Limitations (Optional)**

- **Scaling**: We are using Kubernetes to manage scalability, but scaling specific services (like AI-Service) based on demand may require fine-tuning.
- **Azure Service Bus**: The integration with Azure Service Bus requires setting up appropriate Azure services and network configurations.
- **AI Response Time**: The AI-generated descriptions and images may take time, depending on the request load and API usage limits.

---

## **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
