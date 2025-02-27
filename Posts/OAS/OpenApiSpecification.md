---
title: "How to write your first OAS"
description: "How to write your first Open API Specification"
author: "Ondrej Sevcak"
date: "2025-02-23"
category: "OAS"
hashtags: "#OAS #API #Backend"
---

### What is OpenAPI Specification

- An industry standard to design and document HTTP APIs
- Language-angnostic API interface
- Can be written-first by analyst or API designers 
- Or generated from code by developers

### How to write you first OpenAPI Specification

 - OpenAPI specification are written in JSON or YAML formats. 
 - The syntax for writing the specification is called JSON Schema (Yes, you can write JSON Schema in YAML file)

 ### JSON Schema

JSON Schema is a tool for validating, structuring, and documenting JSON data. It provides a standard way to define the expected structure, data types, and constraints for JSON objects.

 [json-schema-reference](https://json-schema.org/understanding-json-schema/basics)

**Example JSON Schema:**

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Car",
  "type": "object",
  "properties": {
    "id": {
      "type": "string",
      "format": "uuid"
    },
    "make": {
      "type": "string"
    },
    "model": {
      "type": "string"
    },
    "year": {
      "type": "integer",
      "minimum": 1886
    },
    "available": {
      "type": "boolean"
    }
  },
  "required": ["id", "make", "model", "year", "available"]
}
```

### JSON Schema basics

Basic JSON Schema structure looks like this:

```yaml
$schema: "https://json-schema.org/draft/2020-12/schema"

title: "MenuItem"
description: "A schema representing a menu item."

type: "object"

properties:
  property1:
    type: "string" #(string, number, boolean, object, array)
    description: "Description of property 1"
    example: "Example value"
required:
  - property1
  - property2
```

**Simple arrays:**

```yaml
type: "object"
properties:
  arrayProperty:
    type: "array"
    minItems: 0
    maxItems: 500
    items:
      # definition of the item in the array
```

**Complex arrays:**

```yaml
type: "object"
properties:
  arrayProperty:
    type: "array"
    items:
      type: "object"
      properties:
        firstProperty:
          type: "string"
        secondProperty:
          type: "string"
      required:
        - firstProperty
        - secondProperty
    uniqueItems: true
```

**Enums:**

```yaml
type: "object"
properties:
  # Enum values
  enumProperty:
    type: "string"
    default: "value2"
    enum:
      - "value1"
      - "value2"
      - "value3"
```

<br>

### How is the OAS document structured?

The OpenAPI Specification document contains following sections:

1. **OpenAPI** - version number
2. **Info** - API metadata
3. **Servers** - connectivity to target servers
4. **Paths** - API endpoints with their HTTP methods
5. **Components** - reusable object definitions
6. **Security** - declaration of security mechanisms
7. **Tags** - list of tags with additional metadata 


### Lets write out first OpenAPI Specification

- what tools do we need? Lets start in Visual Studio Code by creating a .yaml file
- what API will we design? We have been asked to design an API layer for **Car rental company**
- [OpenAPI Specification reference](https://swagger.io/specification/v3/)
- [example OAS in swagger editor](https://editor.swagger.io/)

### 1. Defining schemas in OAS

- lets start by creating schemas for our objects
- schemas belong to section called *components*

#### Car object schema

```yaml
components:
    schemas:
        Car:
            type: object
            properties:
                id:
                    type: string
                make: 
                    type: string
                model: 
                    type: string
                category:
                    type: string
                    default: SUV
                    enum:
                     - SUV
                     - HotHatch
                     - Sport
                available:
                    type: boolean
```

- Next we will create schema definition for object representing a *Customer* object

### Customer schema  

```yaml
Customer:
    type: object
    properties:
    id:
        type: string
    name:
        type: string
    email:
        type: string
```
And finally the...

#### Reservation schema

```yaml
Reservation:
    type: object
    properties:
    id:
        type: string
    customerId:
        type: string
    carId:
        type: string
    startDate:
        type: string
        format: date
    endDate:
        type: string
        format: date
```

#### Error reponse schema

```yaml
    Error:
      type: object
      properties:
        code:
          type: integer
        message:
          type: string
```
<br>

### 2. Defining the API paths

- lets add a new section to our .yaml file called **paths:**

#### Get a list of available cars | Add new car

```yaml
paths:
  /cars:    #this defines the API path 

    #used HTTP method within the /cars path
    get:   
      summary: Get a list of available cars
      operationId: getCars
      #below we define each reponse type with status code, content type and link to object schema that will be returned
      responses:
        '200':
          description: A list of available cars
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Car'
        "500":
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"

    #used HTTP method within the /cars path
    post:
      summary: Add a new car to the fleet
      operationId: addCar
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Car'
      responses:
        '201':
          description: Car added successfully
          content:
            application/json:
                schema:
                    type: string
                    example: SkodaOctavia54832132sds
                    description: Id of added car
```

#### Get all customers | Register new customer

```yaml  
/customers:
    get:
      summary: Get a list of customers
      operationId: getCustomers
      responses:
        '200':
          description: A list of customers
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Customer'
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"

    post:
      summary: Register a new customer
      operationId: registerCustomer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Customer'
      responses:
        '201':
          description: Customer registered successfully
            content:
            application/json:
                schema:
                    type: string
                    example: uid56sd5fsdfs
                    description: Id of new customer
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"
```

#### Reservations

```yaml
  /reservations:
    post:
      summary: Create a new car reservation
      operationId: createReservation
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Reservation'
      responses:
        '201':
          description: Reservation created successfully
          content:
            application/json:
              schema:
                  type: string
                  example: uid56sd5fsdfs
                  description: Id of the created reservation
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
    get:
      summary: Get a list of reservations
      operationId: getReservations
      responses:
        '200':
          description: A list of reservations
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Reservation'
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

## The whole specification:

- link to file

If you copy the whole yaml file and paste it to editor.swagger.io, you will see following:

![swagger editor](https://github.com/OndrejSevcak/BlazorBlogStorage/blob/main/Posts/OAS/image.png)