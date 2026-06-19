# trashdb-ts-client

SDK oficial de TrashDB para Node.js/TypeScript. Permite crear y destruir contenedores efimeros de bases de datos (como `chromadb`, `qdrant`, `redis` o `postgres`) desde tu codigo.

## Requisitos

- Node.js 18+
- Una API key de TrashDB

## Instalacion

```bash
npm install trashdb
```

## Uso rapido

```ts
import { TrashDB } from "trashdb";

const client = new TrashDB({
  apiKey: process.env.TRASHDB_API_KEY!,
  // Opcional para entorno local:
  // baseUrl: "http://localhost:5000/api/v1",
});

async function main() {
  const container = await client.createContainer({
    engine: "postgres",
    ttlMinutes: 10,
    name: "integration-test-db",
  });

  console.log("Container ID:", container.id);
  console.log("Connection string:", container.connectionString);

  const running = await client.getRunningContainers();
  console.log("Running containers:", running.length);

  await client.destroyContainer(container.id);
  console.log("Container destroyed");
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

## API

### `new TrashDB(options)`

Crea una instancia del cliente.

- `options.apiKey: string` (requerido)
- `options.baseUrl?: string` (opcional, por defecto `http://localhost:5000/api/v1`)

### `createContainer(params)`

Crea un contenedor efimero.

- `params.engine: string`
- `params.ttlMinutes?: number`
- `params.name?: string`

Devuelve un `ContainerResponse`:

```ts
{
  id: string;
  engine: string;
  port: number;
  connectionString: string;
  createdAt: string;
  ttlMinutes: number;
  name?: string;
}
```

### `getRunningContainers()`

Lista los contenedores activos para tu tenant.

### `destroyContainer(containerId)`

Destruye inmediatamente un contenedor por ID.

### `getEngines()`

Devuelve los motores soportados por la API.

## Manejo de errores

Las respuestas de error de la API lanzan `TrashDBAPIError` con:

- `status`: codigo HTTP
- `code`: codigo interno de TrashDB
- `message`: mensaje legible

```ts
import { TrashDB, TrashDBAPIError } from "trashdb";

try {
  // ...
} catch (error) {
  if (error instanceof TrashDBAPIError) {
    console.error(error.status, error.code, error.message);
  }
}
```

## Desarrollo local

```bash
npm install
npm run build
```

## Licencia

MIT

