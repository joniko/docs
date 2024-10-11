---
title: 'API Listados de Proceso'
description: 'Documentación detallada para la operación de Listados de Proceso en la API de SIRO'
---

# API Listados de Proceso

## Descripción General

Este documento detalla la operación para obtener la rendición de las cobranzas en la API de SIRO. Esta funcionalidad permite a los usuarios obtener información detallada sobre las transacciones procesadas en un período específico.

## Operación: Siro/Listados/Proceso

### Descripción

Permite obtener la rendición de las cobranzas en un rango de tiempo determinado. Esta operación es equivalente a la acción "Generar Archivo" del listado por fecha de proceso en la plataforma web de SIRO.

### Detalles del Endpoint

- **URL**: `/siro/Listados/Proceso`
- **Método**: `POST`
- **Tipo de Contenido**: `application/json` sobre HTTPS

### Parámetros de Entrada

```json
{
  "fecha_desde": "string",
  "fecha_hasta": "string",
  "cuit_administrador": "string",
  "nro_empresa": "string"
}
```

- `fecha_desde`: Fecha desde la cual se desea obtener la rendición.
- `fecha_hasta`: Fecha hasta la cual se desea obtener la rendición.
- `cuit_administrador`: CUIT del administrador SIRO (11 dígitos).
- `nro_empresa`: Número de identificación del convenio (10 dígitos, opcional).

### Respuesta Exitosa

```json
[
  "string"
]
```

La respuesta es un array de strings, donde cada string representa un registro procesado en el rango de fechas consultado.

### Ejemplo de Solicitud

```bash
curl -X POST "https://apisiroh.bancoroela.com.ar/siro/Listados/Proceso" \
-H "authorization: Bearer [TOKEN]" \
-H "accept: application/json" \
-H "content-type: application/json" \
-d '{
  "fecha_desde": "2020-12-01T23:00:00.000Z",
  "fecha_hasta": "2020-12-31T23:00:00.000Z",
  "cuit_administrador": "20233953270",
  "nro_empresa": "5150058293"
}'
```

### Ejemplo de Respuesta Exitosa

```json
[
  "20201208202012112021070900000560000000127230044700001272321070900560000060058000015006050005150058293370000000000000000000000PF",
  "20201201202012041900010100000001500999999990044709999999900000000001500000000000000000000005150058293000000000000000000001220BPD",
  "20201201202012042020123100000001000000252525044750002525220123100001000000000000000000000005150058293120000000000000000051120BPD"
]
```

## Notas Importantes

1. **Procesamiento de Pagos**: Los pagos se procesan entre las 8:30 y 9:30 hs. Se recomienda consultar una vez al día o reintentar en caso de error, ya que las bocas no están en línea.

2. **Acreditación de Pagos**: Si un pago se acredita fuera del horario de procesamiento (por ejemplo, a las 12:00), se informará al día siguiente.

3. **Múltiples Cuentas**: Si un administrador tiene varias cuentas, no es necesario consumir el servicio por cada convenio. Se puede hacer una única consulta con la cuenta del administrador.

4. **Límite de Rango de Tiempo**: El rango de tiempo para la consulta tiene un límite máximo de tres meses.

5. **Autenticación**: Todas las solicitudes deben incluir un token Bearer en el encabezado de autorización.

6. **Tiempo de Espera**: El tiempo máximo de respuesta es de 25 segundos, después del cual se puede reintentar la operación.

## Recomendaciones

- Realice consultas regulares, preferiblemente una vez al día, para mantener sus registros actualizados.
- En caso de errores, especialmente durante el horario de procesamiento, intente la consulta nuevamente más tarde.
- Para grandes volúmenes de datos o múltiples convenios, considere agrupar las consultas por administrador en lugar de por convenio individual.
- Mantenga un registro de las consultas realizadas para evitar solicitudes duplicadas y optimizar el uso de la API.