---
title: 'Botón de Pago'
description: 'Description of your new file.'
---
# SIRO API REST Payment Button Manual - Detailed Documentation

## 1. API Overview

This document details the specifications for API methods required for homologation.

## 2. Authentication (Sesión)

- **Endpoint**: `/Sesion`
- **Method**: `POST`
- **Content-Type**: `application/json` over HTTPS
- **Body**:
  ```json
  {
    "Usuario": "string",
    "Password": "string"
  }
  ```
- **Response**:
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

## 3. Payment Intent Creation (Pago/Comprobante)

- **Description**: Creates a payment intent for an existing invoice registered in Siro.
- **Endpoint**: `/Pago/Comprobante`
- **Method**: `POST`
- **Body**:
  ```json
  {
    "nro_cliente_empresa": "string",
    "nro_comprobante": "string",
    "URL_OK": "string",
    "URL_ERROR": "string",
    "IdReferenciaOperacion": "string"
  }
  ```
- **Important**: Store the returned `Hash` for future API calls like payment status checks.
- **Responses**:
  - **Success**:
    ```json
    {
      "Url": "string",
      "Hash": "string"
    }
    ```
  - **Error**:
    ```json
    {
      "Message": "string",
      "ModelState": {
        "pago_request.nro_comprobante": ["string"]
      }
    }
    ```

## 4. Payment Creation (Pago)

- **Description**: Creates a new payment intent when no preloaded debt exists.
- **Endpoint**: `/Pago`
- **Method**: `POST`
- **Body**:
  ```json
  {
    "nro_cliente_empresa": "string",
    "nro_comprobante": "string",
    "Concepto": "string",
    "Importe": float,
    "URL_OK": "string",
    "URL_ERROR": "string",
    "IdReferenciaOperacion": "string",
    "Detalle": [{"Descripcion": "string", "Importe": float}]
  }
  ```
- **Responses**:
  - **Success**:
    ```json
    {
      "Url": "string",
      "Hash": "string"
    }
    ```
  - **Error**:
    ```json
    {
      "Message": "string",
      "ModelState": {
        "pago_request.nro_comprobante": ["string"]
      }
    }
    ```

## 5. Payment Status Check (Pago/{hash}/{idResultado})

- **Description**: Checks the status of a payment operation.
- **Endpoint**: `/Pago/{hash}/{idResultado}`
- **Method**: `GET`
- **Response**:
  ```json
  {
    "PagoExitoso": bool,
    "MensajeResultado": "string",
    "FechaOperacion": "string",
    "Estado": "string"
  }
  ```
- **States**:
  - `PROCESADA`: Successful
  - `CANCELADA`: Cancelled
  - `ERROR`: Error occurred

## 6. Payment Status Range Check (Pago/Consulta)

- **Description**: Allows querying payment intents by date range or status.
- **Endpoint**: `/Pago/Consulta`
- **Method**: `POST`
- **Body**:
  ```json
  {
    "FechaDesde": "string",
    "FechaHasta": "string",
    "estado": "string"
  }
  ```
- **Response**:
  ```json
  [
    {
      "PagoExitoso": "bool",
      "MensajeResultado": "string",
      "FechaOperacion": "string",
      "FechaRegistro": "string",
      "IdOperacion": "string",
      "Estado": "string",
      "idReferenciaOperacion": "string"
    }
  ]
  ```

## 7. Test Environment Configuration

- **Test Host**: `https://siropagosh.bancoroela.com.ar/api/`
- **Production Host**: `https://siropagos.bancoroela.com.ar/api/`
- **Example Test User**: `UsuarioTestSiro` (Password: `Hola123`)

## 8. Test Cards for Validation

Test cards provided for different scenarios:

| Scenario             | Card Type       | Card Number        | CVV  | Expiry   |
|----------------------|-----------------|--------------------|------|----------|
| **Successful Payment** | Visa Credit    | 4507990000004905   | 123  | 2030-08  |
|                      | Mastercard Credit | 5299910010000015 | 123  | 2030-08  |
|                      | Cabal Credit    | 5896570000000008   | 123  | 2030-08  |
|                      | Visa Debit      | 4517721004856075   | 123  | 2030-08  |
| **Card Denied**      | Visa Credit     | 4546400044997331   | 123  | 2030-08  |
|                      | Mastercard Credit | 5455330200000016 | 123  | 2030-08  |
| **Insufficient Funds** | Visa Credit   | 4258210000474094   | 123  | 2030-08  |
|                      | Mastercard Credit | 5204230010000012 | 123  | 2030-08  |
|                      | Visa Debit      | 4517720194823929   | 123  | 2030-08  |


**Note**: Test cards are for homologation only and should not be used in production.

## 9. Additional Notes

- Ensure API calls follow format requirements and use proper tokens.
- Timeout limits are set at 25 seconds, with retries allowed.
- Consult Siro's support for issues or production transition.

## 10. Data Connection Details

| Configuration     | Homologation                                       | Production                                        |
| ----------------- | -------------------------------------------------- | ------------------------------------------------- |
| Host API Session  | `https://apisesionh.bancoroela.com.ar/auth/sesion` | `https://apisesion.bancoroela.com.ar/Auth/Sesion` |
| Host Other APIs   | `https://apisiroh.bancoroela.com.ar/siro/`         | `https://apisiro.bancoroela.com.ar/siro/`         |
| Example Test User | `UsuarioTestSiro`                                  | User’s CUIT/CUIL                                  |
| Example Password  | `Hola123`                                          | Production credentials (request from support)     |

## 11. API Error Handling

- The API provides specific error codes:
  - **200**: OK
  - **400**: Bad Request – Syntax issues.
  - **401**: Unauthorized – Incorrect authentication or permissions.
  - **404**: Not Found – Resource unavailable.
  - **500**: Internal Server Error – Server issue.
  - **501**: Not Implemented – Unrecognized method.

## 12. Example Responses

### Successful Payment

```json
{
  "PagoExitoso": true,
  "MensajeResultado": "Pago exitoso",
  "FechaOperacion": "2021-11-13T11:29:21.04",
  "FechaRegistro": "2021-11-13T11:22:54.39",
  "IdOperacion": "4ed54f5c-0412-406b-a4bb-3fc6607d0d05",
  "Estado": "PROCESADA",
  "idReferenciaOperacion": "87459",
  "Request": {
    "nro_cliente_empresa": "0000000295150058293",
    "nro_comprobante": "00000000000000011221",
    "Concepto": "Agua",
    "Importe": 100,
    "URL_OK": "https://www.google.com.ar/",
    "URL_ERROR": "https://www.google.com.ar/",
    "IdReferenciaOperacion": "87459",
    "Detalle": []
  },
  "Rendicion": {
    "Cuotas": 1,
    "Tarjeta": "VISA"
  }
}
```

### Error Response

```json
{
  "Message": "Se ha denegado la autorización para esta solicitud."
}
```
