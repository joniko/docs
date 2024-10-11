---
title: 'API'
description: 'Description of your new file.'
---
# API GESTIÓN REST Manual - Detailed Documentation

## Version

- **Version**: 1.0
- **Author**: AF
- **Creation Date**: December 2021

## Purpose

The document details the specifications necessary for the homologation of the following API methods:

### API Methods Overview

| Method                       | Description                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------- |
| Sesión                       | Session initiation.                                                          |
| siro/Administradores         | Consults the administrators SIRO managed by the user.                        |
| siro/Convenios               | Consults the active agreements of administrators managed by the user.        |
| siro/Listados/Proceso        | Retrieves the settlement of collections.                                     |
| siro/Pagos                   | Uploads a payment base for processing.                                       |
| siro/Pagos/{nro_transaccion} | Consults a previously uploaded payment base.                                 |
| siro/Adhesiones              | Manages debit or credit card (Visa, Mastercard) or direct debit memberships. |
| siro/Adhesiones/Desactivar   | Deactivates an existing membership.                                          |
| siro/Adhesiones/Modificar    | Modifies an existing membership.                                             |

## API Endpoints and Details

### 1. Sesión (Login)

- **Endpoint**: `/Sesion`
- **Method**: `POST`
- **Request Content Type**: `application/json` over HTTPS
- **Parameters**:
  ```json
  {
    "Usuario": "string",
    "Password": "string"
  }
  ```
- **Responses**:
  - **Success**:
    ```json
    {
      "access_token": "string",
      "token_type": "bearer",
      "expires_in": int
    }
    ```
  - **Error**:
    ```json
    {
      "Message": "string",
      "ModelState": {
        "LoginError": ["string"]
      }
    }
    ```

### 2. siro/Administradores

- **Endpoint**: `/siro/Administradores`
- **Method**: `GET`
- **Parameters**: `Authorization` (Bearer token)
- **Responses**:
  - **Success**: Array of CUITs managed by the user.
  - **Error**: Various error messages indicating issues with the request or authorization.

### 3. siro/Convenios

- **Endpoint**: `/siro/Convenios`
- **Method**: `GET`
- **Parameters**:
  - `cuit_administrador`: CUIT of the administrator
  - `nro_empresa`: Identification number for the agreement
- **Responses**:
  - **Success**: List of agreements.
  - **Error**: Various error messages for unauthorized or invalid requests.

### 4. siro/Listados/Proceso

- **Endpoint**: `/siro/Listados/Proceso`
- **Method**: `POST`
- **Request Content Type**: `application/json` over HTTPS
- **Parameters**:
  ```json
  {
    "fecha_desde": "string",
    "fecha_hasta": "string",
    "cuit_administrador": "string",
    "nro_empresa": "string"
  }
  ```
- **Responses**:
  - **Success**: Array of processed records.
  - **Error**: Error messages indicating invalid or missing fields.

### 5. siro/Pagos

- **Endpoint**: `/siro/Pagos`
- **Method**: `POST`
- **Parameters**: Payment base upload details.
- **Responses**:
  - **Success**: Returns a transaction number.
  - **Error**: Error messages indicating issues with the uploaded payment base.

### 6. siro/Adhesiones

- **Endpoint**: `/siro/Adhesiones`
- **Method**: `POST`
- **Parameters**: Membership details (credit card, direct debit, etc.)
- **Responses**:
  - **Success**: Returns the membership details.
  - **Error**: Error messages indicating validation or format issues.

### 7. siro/Adhesiones/Desactivar

- **Endpoint**: `/siro/Adhesiones/Desactivar/{nro_cpe}`
- **Method**: `DELETE`
- **Parameters**: Membership number.
- **Responses**:
  - **Success**: Confirmation of deactivation.
  - **Error**: Error messages indicating issues with the deactivation process.

### 8. siro/Adhesiones/Modificar

- **Endpoint**: `/siro/Adhesiones/Modificar`
- **Method**: `PUT`
- **Parameters**: Updated membership details (e.g., new credit card or CBU).
- **Responses**:
  - **Success**: Returns the modified membership details.
  - **Error**: Error messages for invalid parameters.

## Connection Details

| Configuration    | Homologation                                       | Production                                        |
| ---------------- | -------------------------------------------------- | ------------------------------------------------- |
| Host API Session | `https://apisesionh.bancoroela.com.ar/auth/sesion` | `https://apisesion.bancoroela.com.ar/Auth/Sesion` |
| Host Other APIs  | `https://apisiroh.bancoroela.com.ar/siro/`         | `https://apisiro.bancoroela.com.ar/siro/`         |
| User             | `UsuarioTestAPI`                                   | User’s CUIT/CUIL                                  |
| Password         | `Hola123` (test)                                   | Production credentials (request from support)     |

## Examples

### Example Request for `/Sesion`

```bash
curl -X POST "https://apisesionh.bancoroela.com.ar/auth/sesion" -H "accept: application/json" -H "Content-Type: application/json" -d '{
  "Usuario": "UsuarioTestAPI",
  "Password": "Hola123"
}'
```

### Example Response for `/Sesion`

```json
{
  "access_token": "example_access_token",
  "token_type": "bearer",
  "expires_in": 3599
}
```

### Example Error Response

```json
{
  "Message": "Invalid request.",
  "ModelState": {
    "LoginError": ["The user is invalid or the password is incorrect"]
  }
}
```

### Example Request for `/siro/Administradores`

```bash
curl -X GET "https://apisiroh.bancoroela.com.ar/siro/Administradores" -H "authorization: Bearer <token>" -H "accept: application/json" -H "content-type: application/json"
```

### Example Successful Response for `/siro/Administradores`

```json
["20084974073", "20111904600", "20119749698", "20123419910"]
```

### Error Response for `/siro/Administradores`

```json
{
  "Message": "Authorization has been denied for this request."
}
```

### Example for `/siro/Convenios`

```bash
curl -X GET "https://apisiroh.bancoroela.com.ar/siro/Convenios?cuit_administrador=20233953270" -H "authorization: Bearer <token>" -H "accept: application/json" -H "content-type: application/json"
```

## Additional Notes

- Ensure API calls follow the format requirements and use proper authentication tokens.
- Timeout limits for API calls are set at 25 seconds, after which the request can be retried.
- Consult Siro's support team for any specific issues or when transitioning from homologation to production.
