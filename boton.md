---
title: 'API Payment Button'
description: 'Documentación detallada para la API de Botón de Pago de SIRO'
---

# SIRO API REST Payment Button

## 1. Descripción General de la API

Este documento detalla las especificaciones para los métodos de API requeridos para la homologación del botón de pago.

## 2. Autenticación (Sesión)

- **Endpoint**: `/Sesion`
- **Método**: `POST`
- **Content-Type**: `application/json` sobre HTTPS
- **Cuerpo**:
  ```json
  {
    "Usuario": "string",
    "Password": "string"
  }
  ```
- **Respuesta**:
  - **Éxito**:
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

## 3. Creación de Intención de Pago (Pago/Comprobante)

- **Descripción**: Crea una intención de pago para una factura existente registrada en Siro.
- **Endpoint**: `/Pago/Comprobante`
- **Método**: `POST`
- **Cuerpo**:
  ```json
  {
    "nro_cliente_empresa": "string",
    "nro_comprobante": "string",
    "URL_OK": "string",
    "URL_ERROR": "string",
    "IdReferenciaOperacion": "string"
  }
  ```
- **Importante**: Almacene el `Hash` devuelto para futuras llamadas a la API como verificaciones de estado de pago.
- **Respuestas**:
  - **Éxito**:
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

## 4. Creación de Pago (Pago)

- **Descripción**: Crea una nueva intención de pago cuando no existe deuda precargada.
- **Endpoint**: `/Pago`
- **Método**: `POST`
- **Cuerpo**:
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
- **Respuestas**:
  - **Éxito**:
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

## 5. Verificación de Estado de Pago (Pago/{hash}/{idResultado})

- **Descripción**: Verifica el estado de una operación de pago.
- **Endpoint**: `/Pago/{hash}/{idResultado}`
- **Método**: `GET`
- **Respuesta**:
  ```json
  {
    "PagoExitoso": bool,
    "MensajeResultado": "string",
    "FechaOperacion": "string",
    "Estado": "string"
  }
  ```
- **Estados**:
  - `PROCESADA`: Exitoso
  - `CANCELADA`: Cancelado
  - `ERROR`: Ocurrió un error

## 6. Verificación de Estado de Pago por Rango (Pago/Consulta)

- **Descripción**: Permite consultar intenciones de pago por rango de fechas o estado.
- **Endpoint**: `/Pago/Consulta`
- **Método**: `POST`
- **Cuerpo**:
  ```json
  {
    "FechaDesde": "string",
    "FechaHasta": "string",
    "estado": "string"
  }
  ```
- **Respuesta**:
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

## 7. Configuración del Entorno de Pruebas

- **Host de Prueba**: `https://siropagosh.bancoroela.com.ar/api/`
- **Host de Producción**: `https://siropagos.bancoroela.com.ar/api/`
- **Usuario de Prueba**: `UsuarioTestSiro` (Contraseña: `Hola123`)

## 8. Tarjetas de Prueba para Validación

Tarjetas de prueba proporcionadas para diferentes escenarios:

| Tipo de Tarjeta   | Número           | CVV | Vencimiento | Escenario          |
|-------------------|------------------|-----|-------------|-------------------|
| Visa Crédito      | 4507990000004905 | 123 | 2030-08     | Pago Exitoso       |
| Mastercard Crédito| 5299910010000015 | 123 | 2030-08     | Pago Exitoso       |
| Cabal Crédito     | 5896570000000008 | 123 | 2030-08     | Pago Exitoso       |
| Visa Débito       | 4517721004856075 | 123 | 2030-08     | Pago Exitoso       |
| Visa Crédito      | 4546400044997331 | 123 | 2030-08     | Tarjeta Rechazada  |
| Mastercard Crédito| 5455330200000016 | 123 | 2030-08     | Tarjeta Rechazada  |
| Visa Crédito      | 4258210000474094 | 123 | 2030-08     | Fondos Insuficientes |
| Mastercard Crédito| 5204230010000012 | 123 | 2030-08     | Fondos Insuficientes |
| Visa Débito       | 4517720194823929 | 123 | 2030-08     | Fondos Insuficientes |

**Nota**: Estas tarjetas de prueba son solo para homologación y no deben utilizarse en producción.

## 9. Manejo de Errores en la API

La API utiliza códigos de estado HTTP estándar:
- 200: OK
- 400: Bad Request – Problemas de sintaxis
- 401: Unauthorized – Autenticación incorrecta o permisos insuficientes
- 404: Not Found – Recurso no disponible
- 500: Internal Server Error – Problema del servidor
- 501: Not Implemented – Método no reconocido

Ejemplo de Respuesta de Error:
```json
{
  "Message": "Mensaje de error",
  "ModelState": {
    "NombreCampo": ["Descripción del error"]
  }
}
```

## 10. Detalles de Conexión de Datos

| Configuración     | Homologación                                       | Producción                                        |
|-------------------|----------------------------------------------------|----------------------------------------------------|
| Host API Sesión   | `https://apisesionh.bancoroela.com.ar/auth/sesion` | `https://apisesion.bancoroela.com.ar/Auth/Sesion` |
| Host Otras APIs   | `https://apisiroh.bancoroela.com.ar/siro/`         | `https://apisiro.bancoroela.com.ar/siro/`         |
| Usuario de Prueba | `UsuarioTestSiro`                                  | CUIT/CUIL del usuario                              |
| Contraseña        | `Hola123`                                          | Credenciales de producción (solicitar a soporte)   |

## 11. Ejemplos de Respuestas

### Pago Exitoso

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

## 12. Notas Adicionales

- Siempre use HTTPS para una comunicación segura.
- Almacene el `Hash` devuelto de la creación de pago para futuras llamadas a la API.
- Consulte al equipo de soporte de Siro para problemas específicos o al realizar la transición de homologación a producción.
- Las tarjetas de prueba son solo para homologación y no deben utilizarse en producción.
- Los límites de tiempo de espera para las llamadas a la API están establecidos en 25 segundos, después de los cuales se puede reintentar la solicitud.

## 13. Flujo de Integración Recomendado

1. Obtener token de autenticación mediante el endpoint `/Sesion`.
2. Crear una intención de pago usando `/Pago/Comprobante` o `/Pago`.
3. Redirigir al usuario a la URL proporcionada en la respuesta.
4. Esperar la redirección del usuario a su `URL_OK` o `URL_ERROR`.
5. Verificar el estado final del pago usando `/Pago/{hash}/{idResultado}`.

## 14. Soporte y Contacto

Para cualquier consulta técnica o soporte durante la integración, por favor contacte al equipo de soporte de SIRO:

- Email: soporte@siro.com.ar
- Teléfono: +54 11 1234-5678
- Horario de atención: Lunes a Viernes, 9:00 a 18:00 (GMT-3)

Asegúrese de tener a mano su identificador de usuario y cualquier mensaje de error relevante al contactar con soporte.
