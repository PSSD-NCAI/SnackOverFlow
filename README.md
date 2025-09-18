## SnackOverFlow
<img src="./SnackOverFlow.png" alt="SnackOverFlow" width="200" align="right">

### 🎯 Objective
Build a RESTful API for a restaurant order management system that demonstrates your ability to:
- Design scalable backend architectures
- Implement message-driven patterns using RabbitMQ
- Handle object storage using MinIO
- Write clean, maintainable code

### 📋 Business Requirements

#### 1. Menu Management System
The restaurant needs a comprehensive order management system with the following capabilities:

#### Menu Item Properties
- **Basic Information:** name, description, price, category
- **Operational Data:** preparation time (minutes), availability status
- **Nutritional Information:** calories per serving
- **Menu Images:** images stored in object storage

#### Categories
- `APPETIZER` - Starters and small plates
- `MAIN` - Main courses
- `DESSERT` - Sweet items
- `BEVERAGE` - Drinks (hot/cold)

### 2. Order Processing Pipeline
Implement a complete order lifecycle management system:

#### Order Flow
```
PENDING → CONFIRMED → PREPARING → READY
              ↓
          CANCELLED (allowed only before PREPARING)
```

#### Business Rules
- **Minimum Order Value:** 15 SAR
- **Auto-cancellation:** Orders not confirmed within 10 minutes
- **Preparation Time Calculation:** Maximum prep time among items, plus a 5 minute buffer
- **Concurrent Order Limit:** Maximum 20 active orders (CONFIRMED/PREPARING states)

### 3. Message Queue Processing
Every state change must publish a message to RabbitMQ for async processing:

#### Message Types
- `ORDER_PLACED` - New order created
- `ORDER_CONFIRMED` - Order confirmed
- `ORDER_STATUS_CHANGED` - Any status transition
- `ORDER_CANCELLED` - Order cancelled (include the reason in notes)

### 🔧 Technical Requirements

### Core Technology Stack
|     Component    |        Technology       |
|------------------|-------------------------|
|     Language     |           Java          |
|     Framework    |        Spring Boot      |
|     Database     |        PostgreSQL       |
|  Message Broker  |         RabbitMQ        |
|  Object Storage  |          MinIO          |
| Containerization | Docker & Docker Compose |

### API Endpoints
```yaml
# Menu Management
GET    /api/v1/menu                    # List items (paginated, filterable)
GET    /api/v1/menu/{id}               # Get item details
POST   /api/v1/menu                    # Create item
PUT    /api/v1/menu/{id}               # Update item
DELETE /api/v1/menu/{id}               # Soft delete
PATCH  /api/v1/menu/{id}/availability  # Toggle availability

# Order Management  
POST   /api/v1/orders                  # Place order
GET    /api/v1/orders/{id}             # Get order details
GET    /api/v1/orders                  # List orders (filtered)
PATCH  /api/v1/orders/{id}/status      # Update status
POST   /api/v1/orders/{id}/cancel      # Cancel order

# Customer Operations
GET    /api/v1/customers/{id}/orders   # Order history
POST   /api/v1/customers               # Register customer
```

### RabbitMQ Configuration

### Exchange & Queue Setup
**Name:** `orders.exchange`  
**Type:** topic  
**Durable:** true  
**Queues:**
```
- orders.placed.queue       # Routing key: order.placed
- orders.status.queue       # Routing key: order.status.*
- orders.notification.queue # Routing key: order.#
```

### Sample Message
```json
{
  "messageId": "uuid",
  "messageType": "ORDER_STATUS_CHANGED",
  "timestamp": "2025-09-12T10:50:00Z",
  "payload": {
    "orderId": "uuid",
    "customerId": "uuid",
    "previousStatus": "PENDING",
    "currentStatus": "CONFIRMED",
    "metadata": {
      "preparationTime": 35,
      "amount": 28.00,
      "itemCount": 3
    }
  }
}
```
### RabbitMQ Requirement:
- Implement at least one consumer to handle order processing
- Use acknowledgment modes properly
- Implement Dead Letter Queue (DLQ) for failed messages
- Handle duplicate messages (idempotency)

### Object Storage Requirements
- Max file size: 5MB
- Supported formats: JPEG, PNG, SVG

### 📝 Submission
1. Push your code to a GitHub **private** repository, and assign this account as a collaborator. To assign, go to repository's settings > Collaborators > Add people and search for **PSSD-NCAI**.
2. Provide a project documentation, and include any assumptions made.
3. Ensure `docker-compose up` starts the entire system.
4. Provide API requests collection.

Remember, we value **quality over quantity**. A well-designed **partial** solution with clear documentation of what remains is better than a complete but **poorly** architected system.

### Good Luck! 🚀
