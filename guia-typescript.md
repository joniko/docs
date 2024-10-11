---
title: 'Guía de TypeScript para SIRO API'
description: 'Implementación y mejores prácticas para usar la API de SIRO con TypeScript'
---

# Guía de TypeScript para SIRO API

Esta guía proporciona información detallada sobre cómo implementar y utilizar la API de SIRO utilizando TypeScript, incluyendo ejemplos de código, mejores prácticas y respuestas a preguntas frecuentes.

## Configuración del Proyecto

Primero, asegúrese de tener TypeScript instalado en su proyecto:

```bash
npm install -D typescript @types/node
npm install axios
```

## Definición de Tipos

Cree un archivo `types.ts` para definir los tipos de datos que usará con la API:

```typescript
// types.ts
export interface LoginCredentials {
  Usuario: string;
  Password: string;
}

export interface LoginResponse {
  access_token: string;
  token_type: string;
  expires_in: number;
}

export interface PagoRequest {
  nro_cliente_empresa: string;
  nro_comprobante: string;
  URL_OK: string;
  URL_ERROR: string;
  IdReferenciaOperacion: string;
}

export interface PagoResponse {
  Url: string;
  Hash: string;
}

export interface EstadoPago {
  PagoExitoso: boolean;
  MensajeResultado: string;
  FechaOperacion: string;
  Estado: string;
}
```

## Implementación de Funciones Principales

### Autenticación

```typescript
// auth.ts
import axios from 'axios';
import { LoginCredentials, LoginResponse } from './types';

async function obtenerToken(credentials: LoginCredentials): Promise<string> {
  const url = "https://apisesionh.bancoroela.com.ar/auth/sesion";
  try {
    const response = await axios.post<LoginResponse>(url, credentials);
    return response.data.access_token;
  } catch (error) {
    console.error('Error en la autenticación:', error);
    throw error;
  }
}
```

### Crear una Intención de Pago

```typescript
// pago.ts
import axios from 'axios';
import { PagoRequest, PagoResponse } from './types';

async function crearIntencionPago(token: string, pagoData: PagoRequest): Promise<PagoResponse> {
  const url = "https://siropagosh.bancoroela.com.ar/api/Pago/Comprobante";
  try {
    const response = await axios.post<PagoResponse>(url, pagoData, {
      headers: { Authorization: `Bearer ${token}` }
    });
    return response.data;
  } catch (error) {
    console.error('Error al crear la intención de pago:', error);
    throw error;
  }
}
```

### Verificar el Estado del Pago

```typescript
// verificacion.ts
import axios from 'axios';
import { EstadoPago } from './types';

async function verificarEstadoPago(token: string, hash: string): Promise<EstadoPago> {
  const url = `https://siropagosh.bancoroela.com.ar/api/Pago/${hash}/1`;
  try {
    const response = await axios.get<EstadoPago>(url, {
      headers: { Authorization: `Bearer ${token}` }
    });
    return response.data;
  } catch (error) {
    console.error('Error al verificar el estado del pago:', error);
    throw error;
  }
}
```

## Mejores Prácticas con TypeScript

1. **Uso de Interfaces**: Define interfaces para todas las estructuras de datos que uses con la API. Esto mejora la legibilidad y permite la detección temprana de errores.

2. **Manejo de Errores**: Utiliza try-catch para manejar errores de manera efectiva. Considera crear un tipo personalizado para los errores de la API:

```typescript
interface APIError {
  message: string;
  code: string;
}

function isAPIError(error: any): error is APIError {
  return error && typeof error.message === 'string' && typeof error.code === 'string';
}

// Uso
try {
  // Llamada a la API
} catch (error) {
  if (isAPIError(error)) {
    console.error(`Error de API: ${error.code} - ${error.message}`);
  } else {
    console.error('Error desconocido:', error);
  }
}
```

3. **Configuración de Axios**: Crea una instancia configurada de Axios para todas las llamadas a la API:

```typescript
// api.ts
import axios, { AxiosInstance } from 'axios';

const createAPIClient = (token: string): AxiosInstance => {
  return axios.create({
    baseURL: 'https://siropagosh.bancoroela.com.ar/api',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
  });
};

export default createAPIClient;
```

4. **Uso de Enums**: Para valores que tienen un conjunto fijo de opciones, usa enums:

```typescript
enum EstadoPagoEnum {
  PROCESADA = 'PROCESADA',
  CANCELADA = 'CANCELADA',
  ERROR = 'ERROR',
}
```

5. **Validación de Datos**: Usa bibliotecas como `zod` para validar los datos de entrada y salida:

```typescript
import { z } from 'zod';

const PagoRequestSchema = z.object({
  nro_cliente_empresa: z.string(),
  nro_comprobante: z.string(),
  URL_OK: z.string().url(),
  URL_ERROR: z.string().url(),
  IdReferenciaOperacion: z.string(),
});

// Uso
const pagoData = PagoRequestSchema.parse(inputData);
```

## Preguntas Frecuentes (FAQ)

1. **¿Cómo manejo la expiración del token en TypeScript?**
   
Implementa un interceptor de Axios para renovar automáticamente el token:

```typescript
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response.status === 401) {
      const newToken = await refreshToken();
      error.config.headers['Authorization'] = `Bearer ${newToken}`;
      return api.request(error.config);
    }
    return Promise.reject(error);
  }
);
```

2. **¿Cómo puedo asegurarme de que estoy usando el entorno correcto?**
   
Usa variables de entorno para configurar las URLs de la API:

```typescript
const API_BASE_URL = process.env.NODE_ENV === 'production'
  ? 'https://siropagos.bancoroela.com.ar/api'
  : 'https://siropagosh.bancoroela.com.ar/api';
```

3. **¿Cómo puedo manejar las reconexiones en caso de fallo de red?**
   
Implementa una lógica de reintento usando una biblioteca como `axios-retry`:

```typescript
import axiosRetry from 'axios-retry';

axiosRetry(api, {
  retries: 3,
  retryDelay: axiosRetry.exponentialDelay,
  retryCondition: (error) => {
    return axiosRetry.isNetworkOrIdempotentRequestError(error) || error.response.status === 429;
  },
});
```

## Conclusión

Esta guía proporciona una base sólida para trabajar con la API de SIRO utilizando TypeScript. Asegúrese de seguir las mejores prácticas y utilizar los tipos adecuados para garantizar un código robusto y fácil de mantener. Si tiene más preguntas o necesita ayuda adicional, no dude en consultar la documentación oficial de SIRO o ponerse en contacto con el soporte técnico.

