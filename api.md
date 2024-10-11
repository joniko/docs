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

Es crucial distinguir entre los entornos de homologación (pruebas) y producción. Use las URLs apropiadas según el entorno en el que esté trabajando.

### Entorno de Homologación (Pruebas)

| Configuración    | URL/Datos                                           |
|------------------|-----------------------------------------------------|
| Host API Session | `https://apisesionh.bancoroela.com.ar/auth/sesion`  |
| Host Other APIs  | `https://apisiroh.bancoroela.com.ar/siro/`          |
| Usuario          | `UsuarioTestAPI`                                    |
| Contraseña       | `Hola123` (prueba)                                  |

### Entorno de Producción

| Configuración    | URL/Datos                                           |
|------------------|-----------------------------------------------------|
| Host API Session | `https://apisesion.bancoroela.com.ar/Auth/Sesion`   |
| Host Other APIs  | `https://apisiro.bancoroela.com.ar/siro/`           |
| Usuario          | CUIT/CUIL del usuario                               |
| Contraseña       | Credenciales de producción (solicitar a soporte)    |

**Importante**: Asegúrese de usar las URLs y credenciales correctas según el entorno en el que esté trabajando. Nunca use credenciales de prueba en producción.

## Notas Adicionales

- Asegúrese de que las llamadas a la API sigan los requisitos de formato y utilicen los tokens de autenticación adecuados.
- Los límites de tiempo de espera para las llamadas a la API están establecidos en 25 segundos, después de los cuales se puede reintentar la solicitud.
- Consulte al equipo de soporte de Siro para cualquier problema específico o al realizar la transición de homologación a producción.
- **Importante**: Siempre verifique que está utilizando las URLs correctas según el entorno (homologación o producción) en el que esté trabajando.

