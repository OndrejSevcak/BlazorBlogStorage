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
- Or generated from code after development

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

To be continued...