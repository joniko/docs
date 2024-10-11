---
title: 'API Gestión REST'
description: 'Documentación detallada para la API de Gestión REST de SIRO'
---

# API Gestión REST

## Información General

- **Versión**: 1.0
- **Autor**: AF
- **Fecha de Creación**: Diciembre 2021

## Propósito

Este documento detalla las especificaciones necesarias para la homologación de los siguientes métodos de API:

## Métodos de API

| Método                       | Descripción                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------- |
| Sesión                       | Iniciación de sesión.                                                        |
| siro/Administradores         | Consulta los administradores SIRO gestionados por el usuario.                |
| siro/Convenios               | Consulta los convenios activos de administradores gestionados por el usuario.|
| siro/Listados/Proceso        | Recupera la liquidación de cobranzas.                                        |
| siro/Pagos                   | Carga una base de pagos para su procesamiento.                               |
| siro/Pagos/{nro_transaccion} | Consulta una base de pagos previamente cargada.                              |
| siro/Adhesiones              | Gestiona adhesiones de débito o tarjeta de crédito (Visa, Mastercard) o débito directo. |
| siro/Adhesiones/Desactivar   | Desactiva una adhesión existente.                                            |
| siro/Adhesiones/Modificar    | Modifica una adhesión existente.                                             |

## Detalles de Endpoints

### Sesión (Login)

- **Endpoint**: `/Sesion`
- **Método**: `POST`
- **Tipo de Contenido de la Solicitud**: `application/json` sobre HTTPS
- **Parámetros**:
  ```json
  {
    "Usuario": "string",
    "Password": "string"
  }
  ```
- **Respuestas**:
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

### siro/Administradores

- **Endpoint**: `/siro/Administradores`
- **Método**: `GET`
- **Parámetros**: `Authorization` (token Bearer)
- **Respuestas**:
  - **Éxito**: Array de CUITs gestionados por el usuario.
  - **Error**: Varios mensajes de error indicando problemas con la solicitud o autorización.

### siro/Convenios

- **Endpoint**: `/siro/Convenios`
- **Método**: `GET`
- **Parámetros**:
  - `cuit_administrador`: CUIT del administrador
  - `nro_empresa`: Número de identificación para el convenio
- **Respuestas**:
  - **Éxito**: Lista de convenios.
  - **Error**: Varios mensajes de error para solicitudes no autorizadas o inválidas.

### siro/Listados/Proceso

- **Endpoint**: `/siro/Listados/Proceso`
- **Método**: `POST`
- **Tipo de Contenido de la Solicitud**: `application/json` sobre HTTPS
- **Parámetros**:
  ```json
  {
    "fecha_desde": "string",
    "fecha_hasta": "string",
    "cuit_administrador": "string",
    "nro_empresa": "string"
  }
  ```
- **Respuestas**:
  - **Éxito**: Array de registros procesados.
  - **Error**: Mensajes de error indicando campos inválidos o faltantes.

### siro/Pagos

- **Endpoint**: `/siro/Pagos`
- **Método**: `POST`
- **Parámetros**: Detalles de carga de base de pagos.
- **Respuestas**:
  - **Éxito**: Devuelve un número de transacción.
  - **Error**: Mensajes de error indicando problemas con la base de pagos cargada.

### siro/Adhesiones

- **Endpoint**: `/siro/Adhesiones`
- **Método**: `POST`
- **Parámetros**: Detalles de la adhesión (tarjeta de crédito, débito directo, etc.)
- **Respuestas**:
  - **Éxito**: Devuelve los detalles de la adhesión.
  - **Error**: Mensajes de error indicando problemas de validación o formato.

### siro/Adhesiones/Desactivar

- **Endpoint**: `/siro/Adhesiones/Desactivar/{nro_cpe}`
- **Método**: `DELETE`
- **Parámetros**: Número de adhesión.
- **Respuestas**:
  - **Éxito**: Confirmación de desactivación.
  - **Error**: Mensajes de error indicando problemas con el proceso de desactivación.

### siro/Adhesiones/Modificar

- **Endpoint**: `/siro/Adhesiones/Modificar`
- **Método**: `PUT`
- **Parámetros**: Detalles actualizados de la adhesión (por ejemplo, nueva tarjeta de crédito o CBU).
- **Respuestas**:
  - **Éxito**: Devuelve los detalles de la adhesión modificada.
  - **Error**: Mensajes de error para parámetros inválidos.

## Detalles de Conexión

| Configuración    | Homologación                                       | Producción                                        |
| ---------------- | -------------------------------------------------- | ------------------------------------------------- |
| Host API Session | `https://apisesionh.bancoroela.com.ar/auth/sesion` | `https://apisesion.bancoroela.com.ar/Auth/Sesion` |
| Host Other APIs  | `https://apisiroh.bancoroela.com.ar/siro/`         | `https://apisiro.bancoroela.com.ar/siro/`         |
| Usuario          | `UsuarioTestAPI`                                   | CUIT/CUIL del usuario                             |
| Contraseña       | `Hola123` (prueba)                                 | Credenciales de producción (solicitar a soporte)  |

## Ejemplos

### Ejemplo de Solicitud para `/Sesion`

```bash
curl -X POST "https://apisesionh.bancoroela.com.ar/auth/sesion" -H "accept: application/json" -H "Content-Type: application/json" -d '{
  "Usuario": "UsuarioTestAPI",
  "Password": "Hola123"
}'
```

### Ejemplo de Respuesta para `/Sesion`

```json
{
  "access_token": "example_access_token",
  "token_type": "bearer",
  "expires_in": 3599
}
```

### Ejemplo de Respuesta de Error

```json
{
  "Message": "Solicitud inválida.",
  "ModelState": {
    "LoginError": ["El usuario no es válido o la contraseña es incorrecta"]
  }
}
```

### Ejemplo de Solicitud para `/siro/Administradores`

```bash
curl -X GET "https://apisiroh.bancoroela.com.ar/siro/Administradores" -H "authorization: Bearer <token>" -H "accept: application/json" -H "content-type: application/json"
```

### Ejemplo de Respuesta Exitosa para `/siro/Administradores`

```json
["20084974073", "20111904600", "20119749698", "20123419910"]
```

### Respuesta de Error para `/siro/Administradores`

```json
{
  "Message": "Se ha denegado la autorización para esta solicitud."
}
```

### Ejemplo para `/siro/Convenios`

```bash
curl -X GET "https://apisiroh.bancoroela.com.ar/siro/Convenios?cuit_administrador=20233953270" -H "authorization: Bearer <token>" -H "accept: application/json" -H "content-type: application/json"
```

## Notas Adicionales

- Asegúrese de que las llamadas a la API sigan los requisitos de formato y utilicen los tokens de autenticación adecuados.
- Los límites de tiempo de espera para las llamadas a la API están establecidos en 25 segundos, después de los cuales se puede reintentar la solicitud.
- Consulte al equipo de soporte de Siro para cualquier problema específico o al realizar la transición de homologación a producción.
