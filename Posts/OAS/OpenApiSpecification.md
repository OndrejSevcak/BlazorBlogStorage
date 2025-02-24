---
title: "How to write your first OAS"
description: "How to write your first Open API Specification"
author: "Ondrej Sevcak"
date: "2025-02-23"
category: "OAS"
hashtags: "#OAS #API #Backend"
---

## What is OpenAPI Specification

- An industry standard to design and document HTTP APIs
- Language-angnostic API interface
- Can be written-first by analyst or API designers 
- Or generated from code by developers

## How to write you first OpenAPI Specification

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
# Specifies the version of JSON Schema being used
$schema: "https://json-schema.org/draft/2020-12/schema"

# Title and description provide metadata about the schema
title: "MenuItem"
description: "A schema representing a menu item."

# Indicates that the data should be an object
type: "object"

# Defines the properties of the object
properties:
  # Property 1
  property1:
    # Property type (string, number, boolean, object, array)
    type: "string"
    # Property description
    description: "Description of property 1"
    # Property example value should be valid for the property type
    example: "Example value"
# Defines that the object is required to have the following properties
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

### Lets write out first OpenAPI Specification

- what tools do we need? Lets start in Visual Studio Code by creating a .yaml file

- what API will we design? We have been asked to design an API layer for **Car rental company**

- the whole OpenAPI Specification document is available for reference: [here](https://swagger.io/specification/v3/)

## 1. Defining schemas in OAS

- lets start by creating schemas for our objects
- schemas belong to section called *components*

### Car object schema

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

## Customer schema  

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

## Reservation schema

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

So far so good!

## 2. Defining the API paths

- lets add a new section to our .yaml file called **paths:**

### Get a list of available cars

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

**To be continued...**