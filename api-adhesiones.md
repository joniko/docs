---
title: 'API Adhesiones'
description: 'Documentación detallada para las operaciones de Adhesiones en la API de SIRO'
---

# API Adhesiones

## Descripción General

Este documento detalla las operaciones relacionadas con la gestión de adhesiones en la API de SIRO. Incluye funcionalidades para crear, consultar, modificar y desactivar adhesiones de débito directo y tarjetas de crédito.

## Operaciones

### 1. Crear Adhesión

- **Endpoint**: `/siro/Adhesiones`
- **Método**: `POST`
- **Descripción**: Permite dar de alta una adhesión de tipo débito directo o tarjeta de crédito (Visa, Mastercard).

#### Parámetros de Entrada

```json
{
  "numeroAdhesion": "string",
  "numeroClienteEmpresa": "string",
  "tipoAdhesion": "string"
}
```

- `numeroAdhesion`: CBU o número de tarjeta de crédito (22 o 16 caracteres).
- `numeroClienteEmpresa`: Número de CPE de 19 dígitos que identifica al cliente.
- `tipoAdhesion`: "VS" para Visa, "MC" para Mastercard, "DD" para débito directo.

#### Respuesta Exitosa

```json
{
  "numeroAdhesion": "string",
  "numeroClienteEmpresa": "string",
  "fechaAlta": "string",
  "fechaBaja": "string",
  "tipoAdhesion": "string"
}
```

### 2. Consultar Adhesiones

- **Endpoint**: `/siro/Adhesiones/{nro_cpe}`
- **Método**: `GET`
- **Descripción**: Consulta las adhesiones vigentes y no vigentes por número de cliente empresa.

#### Parámetros
- `nro_cpe`: Número de cupón de pago electrónico (19 dígitos).

#### Respuesta Exitosa

```json
[
  {
    "numeroAdhesion": "string",
    "numeroClienteEmpresa": "string",
    "fechaAlta": "string",
    "fechaBaja": "string",
    "tipoAdhesion": "string"
  }
]
```

### 3. Consultar Adhesiones Dadas de Baja

- **Endpoint**: `/siro/Adhesiones/Bajas`
- **Método**: `GET`
- **Descripción**: Busca las adhesiones no vigentes, desactivadas entre una fecha mínima y máxima.

#### Parámetros
- `cuit_admin`: CUIT del administrador SIRO.
- `FechaBajaDesde`: Fecha mínima de baja.
- `FechaBajaHasta`: Fecha máxima de baja.
- `nro_pag`: Número de página (opcional, por defecto 50 adhesiones por página).
- `nro_empresa`: Número de identificación del convenio (opcional).

### 4. Consultar Adhesiones Vigentes

- **Endpoint**: `/siro/Adhesiones/Vigentes`
- **Método**: `GET`
- **Descripción**: Busca las adhesiones vigentes hasta la fecha actual.

#### Parámetros
- `cuit_admin`: CUIT del administrador SIRO.
- `nro_pag`: Número de página (opcional).
- `nro_empresa`: Número de identificación del convenio (opcional).

### 5. Desactivar Adhesión

- **Endpoint**: `/siro/Adhesiones/Desactivar/{nro_cpe}`
- **Método**: `DELETE`
- **Descripción**: Permite dar de baja una adhesión.

#### Parámetros
- `nro_cpe`: Número de cupón de pago electrónico.

#### Respuesta Exitosa

```json
[
  {
    "numeroAdhesion": "string",
    "tipoAdhesion": "string"
  }
]
```

### 6. Modificar Adhesión

- **Endpoint**: `/siro/Adhesiones/Modificar`
- **Método**: `PUT`
- **Descripción**: Permite modificar una adhesión existente.

#### Parámetros de Entrada

```json
{
  "numeroAdhesionNuevo": "string",
  "numeroClienteEmpresa": "string",
  "tipoAdhesionNueva": "string"
}
```

#### Respuesta Exitosa

```json
{
  "numeroAdhesion": "string",
  "numeroClienteEmpresa": "string",
  "fechaAlta": "string",
  "tipoAdhesion": "string"
}
```

## Notas Importantes

- No se permiten CVU virtuales para débito directo.
- Verifique que el canal de pago esté disponible en el convenio establecido.
- Todas las operaciones requieren autenticación mediante token Bearer.
- Los errores comunes incluyen: convenio no encontrado, parámetros no válidos, formato incorrecto de tarjeta o CBU, y adhesión no encontrada.

