<<<<<<< HEAD
# E-commerce Products & Orders API

A FastAPI-based backend application for creating and managing products and orders in an e-commerce platform, similar to Flipkart/Amazon.

## Features

- **Create Products API**: POST endpoint to create new products
- **List Products API**: GET endpoint to list products with filtering and pagination
- **Create Orders API**: POST endpoint to create new orders
- **List Orders API**: GET endpoint to list orders for a specific user with product details
- **MongoDB Integration**: Data persistence using MongoDB
- **Async Operations**: Built with async/await for better performance
- **Input Validation**: Pydantic models for request/response validation
- **Auto-generated Documentation**: Interactive API docs with Swagger UI

## Prerequisites

- Python 3.8+
- MongoDB (running locally or accessible via connection string)

## Installation

1. **Clone the repository** (if applicable) or navigate to the project directory

2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Start MongoDB**:
   - If running locally: `mongod`
   - Or use MongoDB Atlas (cloud service)

## Running the Application

### Development Server
```bash
python main.py
```

### Using Uvicorn directly
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at:
- **API Base URL**: http://localhost:8000
- **Interactive Documentation**: http://localhost:8000/docs
- **Alternative Documentation**: http://localhost:8000/redoc

## API Endpoints

### 1. Create Product
- **Endpoint**: `POST /products`
- **Status Code**: `201 Created`
- **Content-Type**: `application/json`

#### Request Body
```json
{
  "name": "Nike Air Max 270",
  "price": 129.99,
  "sizes": [
    {
      "size": "US 7",
      "quantity": 10
    },
    {
      "size": "US 8",
      "quantity": 15
    },
    {
      "size": "US 9",
      "quantity": 8
    }
  ]
}
```

#### Response Body
```json
{
  "id": "12345678-1234-5678-9abc-123456789abc"
}
```

### 2. List Products
- **Endpoint**: `GET /products`
- **Status Code**: `200 OK`
- **Query Parameters** (all optional):
  - `name`: Filter by product name (supports regex or partial search)
  - `size`: Filter for products that have a specific size
  - `limit`: Number of products to return (1-100, default: 10)
  - `offset`: Number of products to skip for pagination (default: 0)

#### Example Requests
```bash
# Get all products (default pagination)
GET /products

# Get products with pagination
GET /products?limit=5&offset=10

# Filter by name
GET /products?name=Nike

# Filter by size
GET /products?size=US 8

# Combined filters
GET /products?name=Nike&size=US 8&limit=5&offset=0
```

#### Response Body
```json
{
  "data": [
    {
      "id": "12345678-1234-5678-9abc-123456789abc",
      "name": "Nike Air Max 270",
      "price": 129.99
    },
    {
      "id": "87654321-4321-8765-cba9-987654321cba",
      "name": "Adidas Ultraboost",
      "price": 179.99
    }
  ],
  "page": {
    "next": "10",
    "limit": 2,
    "previous": null
  }
}
```

**Note**: The `sizes` field is not included in the list response as per the API specification.

### 3. Create Order
- **Endpoint**: `POST /orders`
- **Status Code**: `201 Created`
- **Content-Type**: `application/json`

#### Request Body
```json
{
  "userId": "user_1",
  "items": [
    {
      "productId": "12345678-1234-5678-9abc-123456789abc",
      "qty": 2
    },
    {
      "productId": "87654321-4321-8765-cba9-987654321cba",
      "qty": 1
    }
  ]
}
```

#### Response Body
```json
{
  "id": "order-12345678-1234-5678-9abc-123456789abc"
}
```

### 4. List Orders
- **Endpoint**: `GET /orders/{user_id}`
- **Status Code**: `200 OK`
- **Path Parameters**:
  - `user_id`: User ID to get orders for
- **Query Parameters** (all optional):
  - `limit`: Number of orders to return (1-100, default: 10)
  - `offset`: Number of orders to skip for pagination (default: 0)

#### Example Requests
```bash
# Get all orders for a user (default pagination)
GET /orders/user_1

# Get orders with pagination
GET /orders/user_1?limit=5&offset=10

# Get orders for different user
GET /orders/user_2?limit=3
```

#### Response Body
```json
{
  "data": [
    {
      "id": "order-12345678-1234-5678-9abc-123456789abc",
      "items": [
        {
          "productDetails": {
            "name": "Nike Air Max 270",
            "id": "12345678-1234-5678-9abc-123456789abc"
          },
          "qty": 2
        },
        {
          "productDetails": {
            "name": "Adidas Ultraboost",
            "id": "87654321-4321-8765-cba9-987654321cba"
          },
          "qty": 1
        }
      ],
      "total": 439.97
    }
  ],
  "page": {
    "next": "10",
    "limit": 1,
    "previous": null
  }
}
```

**Note**: The `productDetails` field includes product information looked up from the products collection.

### 5. Health Check
- **Endpoint**: `GET /health`
- **Status Code**: `200 OK`

### 6. API Information
- **Endpoint**: `GET /`
- **Status Code**: `200 OK`

## Testing the API

### Using curl
```bash
# Create a product
curl -X POST "http://localhost:8000/products" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Test Product",
       "price": 99.99,
       "sizes": [
         {
           "size": "M",
           "quantity": 5
         }
       ]
     }'

# List products
curl "http://localhost:8000/products"

# Create an order
curl -X POST "http://localhost:8000/orders" \
     -H "Content-Type: application/json" \
     -d '{
       "userId": "user_1",
       "items": [
         {
           "productId": "PRODUCT_ID_HERE",
           "qty": 2
         }
       ]
     }'

# List orders for a user
curl "http://localhost:8000/orders/user_1"
```

### Using Python requests
```python
import requests

# Create a product
url = "http://localhost:8000/products"
data = {
    "name": "Test Product",
    "price": 99.99,
    "sizes": [
        {
            "size": "M",
            "quantity": 5
        }
    ]
}

response = requests.post(url, json=data)
print(response.status_code)  # 201
product_id = response.json()["id"]

# Create an order
order_url = "http://localhost:8000/orders"
order_data = {
    "userId": "user_1",
    "items": [
        {
            "productId": product_id,
            "qty": 2
        }
    ]
}

response = requests.post(order_url, json=order_data)
print(response.status_code)  # 201
print(response.json())  # {"id": "..."}

# List orders
response = requests.get("http://localhost:8000/orders/user_1")
print(response.status_code)  # 200
print(response.json())  # {"data": [...], "page": {...}}
```

### Running the test suite
```bash
python test_api.py
```

## Project Structure

```
Fast-API_task/
├── main.py              # Main FastAPI application
├── requirements.txt     # Python dependencies
├── test_api.py         # Test script for the API
├── README.md           # This file
└── .gitignore          # Git ignore file
```

## Database Schema

The application uses MongoDB with the following collections:

### Products Collection
```json
{
  "id": "uuid-string",
  "name": "Product Name",
  "price": 99.99,
  "sizes": [
    {
      "size": "M",
      "quantity": 5
    }
  ],
  "created_at": "2024-01-01T00:00:00Z",
  "updated_at": "2024-01-01T00:00:00Z"
}
```

### Orders Collection
```json
{
  "id": "uuid-string",
  "userId": "user_1",
  "items": [
    {
      "productId": "product-uuid",
      "qty": 2
    }
  ],
  "total": 199.98,
  "created_at": "2024-01-01T00:00:00Z",
  "updated_at": "2024-01-01T00:00:00Z"
}
```

## Configuration

To change MongoDB connection settings, modify the `MONGODB_URL` variable in `main.py`:

```python
MONGODB_URL = "mongodb://localhost:27017"  # Default local MongoDB
# or
MONGODB_URL = "mongodb+srv://username:password@cluster.mongodb.net/"  # MongoDB Atlas
```

## Error Handling

The API includes comprehensive error handling:
- **400 Bad Request**: Invalid input data or products not found
- **422 Unprocessable Entity**: Validation errors
- **500 Internal Server Error**: Database or server errors

## Development

### Adding New Endpoints

1. Define Pydantic models for request/response
2. Create the endpoint function with appropriate decorators
3. Add error handling
4. Update documentation

### Running Tests

To add tests, create a `tests/` directory and use pytest:

```bash
pip install pytest
pytest tests/
```

## License

This project is for educational purposes.
=======
# hrone-backend
HRoneBackend
>>>>>>> b19de708a8958887e23c7823467f2f738f199098
