---
title: 'API Pagos'
description: 'Documentación detallada para las operaciones de Pagos en la API de SIRO'
---

# API Pagos

## Descripción General

Este documento detalla las operaciones relacionadas con la gestión de pagos en la API de SIRO. Incluye funcionalidades para subir bases de pagos y consultar el estado de las transacciones.

## Operaciones

### 1. Subir Base de Pagos

- **Endpoint**: `/siro/Pagos`
- **Método**: `POST`
- **Descripción**: Permite subir una base de pagos para su posterior procesamiento.

#### Parámetros de Entrada

```json
{
  "base_pagos": "string",
  "confirmar_automaticamente": boolean
}
```

- `base_pagos`: Cadena de texto con el contenido de la base de pagos en el formato especificado.
- `confirmar_automaticamente`: Indica si se debe confirmar automáticamente la base de pagos.

#### Respuesta Exitosa

```json
{
  "nro_transaccion": int
}
```

### 2. Consultar Estado de Base de Pagos

- **Endpoint**: `/siro/Pagos/{nro_transaccion}`
- **Método**: `GET`
- **Descripción**: Permite consultar el estado de una base de pagos previamente subida.

#### Parámetros
- `nro_transaccion`: Número de transacción obtenido al subir la base de pagos.
- `obtener_informacion_base`: (Opcional) Determina si se incluye la información de la base de pagos en la respuesta.

#### Respuesta Exitosa

```json
{
  "cantidad_registros_correctos": int,
  "cantidad_registros_erroneos": int,
  "cantidad_registros_procesados": int,
  "confirmar_automaticamente": boolean,
  "error_descripcion": "string",
  "errores": [
    {
      "base_id": int,
      "id": int,
      "linea_registro": int,
      "descripcion_error": "string",
      "registro": "string"
    }
  ],
  "estado": "string",
  "fecha_envio": "string",
  "fecha_proceso": "string",
  "fecha_registro": "string",
  "nro_transaccion": int,
  "registro": "string",
  "total_primer_vencimiento": float,
  "total_segundo_vencimiento": float,
  "total_tercer_vencimiento": float,
  "usuario_id": int,
  "via_ingreso": "string"
}
```

## Estados de Pago

| Estado     | Descripción                                                                                    |
|------------|------------------------------------------------------------------------------------------------|
| GENERADA   | Creación de la intención de pago. Dura 10 minutos hasta que venza el hash o el pagador actúe.  |
| REGISTRADA | Creación de operación a partir de intención. Dura 1 hora o hasta que el pagador actúe.         |
| PROCESADA  | El pago se realizó correctamente.                                                              |
| CANCELADA  | El pagador canceló el pago o salió de la página.                                               |
| ERROR      | Ocurrió un error durante el pago.                                                              |

## Tarjetas de Prueba para Validación

Para realizar pruebas en el entorno de homologación, se proporcionan las siguientes tarjetas de prueba:

| Tarjeta | Número | CVV | Vencimiento | Operación |
|---------|--------|-----|-------------|-----------|
| Visa Crédito | 4507990000004905 | 123 | 2030-08 | Compra exitosa |
| Mastercard Crédito | 5299910010000015 | 123 | 2030-08 | Compra Exitosa |
| Visa Crédito | 4546400044997331 | 123 | 2030-08 | Rechazo-Denegada |
| Mastercard Crédito | 5455330200000016 | 123 | 2030-08 | Rechazo - Denegada |
| Visa Crédito | 4258210000474094 | 123 | 2030-08 | Rechazo- Fondos Insuficientes |
| Visa Débito | 4517721004856075 | 123 | 2030-08 | Compra exitosa |
| Visa Débito | 4517720194823929 | 123 | 2030-08 | Rechazo – Fondos Insuficientes |

**Nota**: 
- Estas tarjetas son solo para homologación y no deben utilizarse en producción.
- Las transacciones en el entorno de homologación pueden ser desde $1,00 hasta $999,00.
- Si alguna tarjeta de prueba no funciona, puede intentar con otra, ya que al ser de homologación no siempre están disponibles.

## Notas Importantes

1. **Procesamiento de Pagos**: Las bases de pagos se procesan una vez al día, los días hábiles, antes de las 14:00 horas.
2. **Confirmación de Archivos**: Los archivos confirmados después de las 14:00 horas quedarán para el día hábil siguiente.
3. **Disponibilidad de Pagos**: 
   - Link Pagos y Pago Mis Cuentas: Disponibles a partir de las 00:00 hs. del día hábil siguiente.
   - Pago sin factura: Disponible a partir de las 15:00 hs. del día del proceso.
   - Botón de Pagos: Disponible con la confirmación de la base.
4. **Consulta de Estado**: Se recomienda consultar el estado de la transacción después de subir una base de pagos para verificar posibles errores.
5. **Tiempo de Espera**: El tiempo máximo de respuesta para las llamadas a la API es de 25 segundos, tras lo cual se puede reintentar la operación.
6. **Borrado de Bases Erróneas**: Para borrar una base errónea, se debe hacer desde la plataforma de Siro en un lapso no mayor a una hora.