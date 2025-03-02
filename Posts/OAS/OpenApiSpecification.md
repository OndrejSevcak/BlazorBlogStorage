---
title: "Jak napsat svou první OAS"
description: "Jak napsat svou první Open API Specification"
author: "Ondrej Sevcak"
date: "2025-02-23"
category: "OAS"
hashtags: "#OAS #API #Backend"
---

### Co je OpenAPI specifikace

- Průmyslový standard pro návrh a dokumentaci HTTP API
- Jazykově nezávislé API rozhraní
- Může být napsáno před zahájením vývoje - design first
- Nebo generováno z kódu - code first

### Jak napsat svou první OpenAPI Specifikaci

- OpenAPI specifikace jsou psány ve formátech JSON nebo YAML.
- Syntaxe pro psaní specifikace se nazývá JSON Schema (Ano, JSON Schema můžete psát v YAML souboru)

### JSON Schema

JSON Schema je nástroj pro validaci, strukturování a dokumentaci JSON dat. Poskytuje standardní způsob definování očekávané struktury, datových typů a omezení pro JSON objekty.

[json-schema-reference](https://json-schema.org/understanding-json-schema/basics)

**Příklad JSON Schema:**

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

### Základy JSON Schema

Základní struktura JSON Schema vypadá takto:

```yaml
$schema: "https://json-schema.org/draft/2020-12/schema"

title: "MenuItem"
description: "Schéma reprezentující položku menu."

type: "object"

properties:
  property1:
    type: "string" #(string, number, boolean, object, array)
    description: "Popis vlastnosti 1"
    example: "Příklad hodnoty"
required:
  - property1
  - property2
```

**Jednoduché pole:**

```yaml
type: "object"
properties:
  arrayProperty:
    type: "array"
    minItems: 0
    maxItems: 500
    items:
      # definice položky v poli
```

**Komplexní pole:**

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
  # Hodnoty výčtu
  enumProperty:
    type: "string"
    default: "value2"
    enum:
      - "value1"
      - "value2"
      - "value3"
```

<br>

### Jak je strukturován dokument OAS?

Dokument OpenAPI Specification obsahuje následující sekce:

1. **OpenAPI** - číslo verze
2. **Info** - metadata API
3. **Servers** - připojení k cílovým serverům
4. **Paths** - API koncové body s jejich HTTP metodami
5. **Components** - znovupoužitelné definice objektů
6. **Security** - deklarace bezpečnostních mechanismů
7. **Tags** - seznam tagů s dalšími metadata

### Pojďme napsat naši první OpenAPI Specifikaci

- jaké nástroje potřebujeme? Začněme ve Visual Studio Code vytvořením .yaml souboru
- jaké API budeme navrhovat? Byli jsme požádáni o návrh API vrstvy pro **CAR Rental - autopůjčovnu**
- [OpenAPI Specification reference](https://swagger.io/specification/v3/)
- [příklad OAS ve swagger editoru](https://editor.swagger.io/)

### 1. Definování schémat v OAS

- začněme vytvořením schémat pro naše objekty
- schémata patří do sekce nazvané *components*

#### Schéma objektu auto - Car

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

- Dále vytvoříme definici schématu pro objekt reprezentující *Customer* objekt

### Schéma Customer - zákazník

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
A nakonec...

#### Schéma Reservation - Reservace

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

#### Schéma pro chybovu hlášku - Error reponse

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

### 2. Definování API endpointů

- přidejme novou sekci do našeho .yaml souboru nazvanou **paths:**

#### Endpoint pro vrácení seznamu dostupných aut | Endpoint pro přidání nového auta

```yaml
paths:
  /cars:    #definuje API cestu 

    #použitá HTTP metoda v rámci /cars cesty
    get:   
      summary: Vrítí všechna dostupná auta
      operationId: getCars
      #níže definujeme každý typ odpovědi s kódem stavu, typem obsahu a odkazem na schéma objektu, který bude vrácen
      responses:
        '200':
          description: Vrátí seznam všech dostupných aut
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Car'
        "500":
          description: Interní chyba serveru
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"

    #použitá HTTP metoda v rámci /cars cesty
    post:
      summary: Přidání nového auta do flotily
      operationId: addCar
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Car'
      responses:
        '201':
          description: Auto úspěšně přidáno
          content:
            application/json:
                schema:
                    type: string
                    example: SkodaOctavia54832132sds
                    description: Id přidaného auta
```

#### Získat všechny zákazníky | Registrovat nového zákazníka

```yaml  
/customers:
    get:
      summary: Získat seznam zákazníků
      operationId: getCustomers
      responses:
        '200':
          description: Seznam zákazníků
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Customer'
        '500':
          description: Interní chyba serveru
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"

    post:
      summary: Registrovat nového zákazníka
      operationId: registerCustomer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Customer'
      responses:
        '201':
          description: Zákazník úspěšně registrován
            content:
            application/json:
                schema:
                    type: string
                    example: uid56sd5fsdfs
                    description: Id nového zákazníka
        '500':
          description: Interní chyba serveru
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Error"
```

#### Rezervace

```yaml
  /reservations:
    post:
      summary: Vytvořit novou rezervaci auta
      operationId: createReservation
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Reservation'
      responses:
        '201':
          description: Rezervace úspěšně vytvořena
          content:
            application/json:
              schema:
                  type: string
                  example: uid56sd5fsdfs
                  description: Id vytvořené rezervace
        '500':
          description: Interní chyba serveru
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
    get:
      summary: Získat seznam rezervací
      operationId: getReservations
      responses:
        '200':
          description: Seznam rezervací
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Reservation'
        '500':
          description: Interní chyba serveru
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

## Celá specifikace:

- [odkaz na soubor](https://raw.githubusercontent.com/OndrejSevcak/BlazorBlogStorage/39662514a0982648296559bf57c342163a352b30/Posts/OAS/car-rental-oas.yaml)

Pokud zkopírujete celý yaml soubor a vložíte jej do editor.swagger.io, uvidíte následující swagger pro naše API:

![!\[swagger editor\](https://github.com/OndrejSevcak/BlazorBlogStorage/blob/39662514a0982648296559bf57c342163a352b30/Posts/OAS/image.png)](https://raw.githubusercontent.com/OndrejSevcak/BlazorBlogStorage/39662514a0982648296559bf57c342163a352b30/Posts/OAS/image.png)
