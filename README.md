#  Token Api Documentation


<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>

  <ol>
    <li>
      <a href="#database-design">Database Design</a>
    </li>
    <li>
      <a href="#endpoint-of-api">Endpoint of Api</a>
      <ul>
        <li><a href="#store-token">Store Token</a></li>
      </ul>
    </li>
    <li><a href="#params">Params</a></li>
    <li><a href="#definition-of-api">Definition of Api</a></li>
    <li><a href="#business-logic-of-api">Business logic of Api</a></li>
        <ul>
            <li><a href="#code-blocks">Code Blocks</a></li>
        </ul>
  </ol>
</details>

---

## Database Design
 > Below Mention is a database design for Token Api-:



```shell
+-------------+--------------+------------------------------+
|                         companies                         |
+-------------+--------------+------------------------------+
| id            | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| name          | varchar(255) |                            |
+-------------+--------------+------------------------------+

+-------------+--------------+-----------------------------------+
|                          tokens                                |
+-------------+--------------+-----------------------------------+
| id            | int(11)           | PRIMARY KEY AUTO_INCREMENT |
| company_id    | int(11)           | FOREIGN KEY                |
| name          | varchar(255)      |                            |
| is_active     | boolean           | DEFAULT 1                  |
+-------------+--------------+-----------------------------------+
```

> Table **companies**  has one-many relationship with **tokens** table.


## Endpoint of Api

*   ### Store-Token
    > This endpoint allows you to generate unique token.

    ```curl
    POST--> http://127.0.0.1:8000/api/store_token
    ```

    Code example payload-:
    ```
    curl -vX POST 
        --url http://127.0.0.1:8000/api/store_token -d "{ \"company_id\": 1,\"name\": \"farhan\"}"
        --header 'Accept: application/json'
    ```

    
    * if Successful "201 created":
        ```json
        {
            "name": "farhan",
            "company_id": "1",
            "token": "4b2f5d6d-1e17-4c1d-8713-c2078405c0d5",
            "updated_at": "2021-09-14T10:54:59.000000Z",
            "created_at": "2021-09-14T10:54:59.000000Z",
            "id": 1
        }
        ```

    * if Unsuccessful "422 unprocessable entity":
        > one or combination of company_id and name encounter 


        ```json
        {
            "message": "The given data was invalid.",
            "errors": {
                "company_id": [
                    "The company id field is required."
                    "The company id must be a number."
                    "The selected company id is invalid."
                ]
                "name": [
                    "The name field is required."
                    "The name format is invalid."
                    "Unsuccessful = Unique combination of name and company_id already exists."
                ]
            }   
        }
        ```

## Params
> Body Parameter 
    
    
| Parameter | Type | Description |
| :--- | :--- | :--- |
| `company_id` | `numeric` | **Required**.Must be valid "id" in the "companies" table.|
| `name` | `alpha_numeric` | **Required**.  |
| `custom rule` |  | **Should Follow**. Combination of company_id and name must be unique. |
    
    

## Definition of Api
* Help you to create a unique "Token"
* "Token" company-wise unique may help you to run campaign

## Business logic of Api
* Generate a unique UUID("Token") of 36 characters per valid payload and not changable once it was generated. The following information will store in "tokens" table in database.
* ### Code Blocks
    Generating the unique token:
    ```php
    public static function boot() {
            parent::boot();
            static::creating(function($item) {
                $item->token = (string) Str::uuid();
            });
    }
    ```
