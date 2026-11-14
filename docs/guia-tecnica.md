# Guía técnica de uso

Esta guía muestra cómo consultar el endpoint desde una aplicación. La URL documentada es:

```text
https://deperu.com/api/rest/temperaturaAhora.json
```

El acceso está limitado a IPs del Perú, el servicio se actualiza cada 2 horas y el límite indicado es 1 solicitud por segundo. Ejecuta los ejemplos desde un entorno con una IP de Perú autorizada.

## User-Agent personalizado

Se recomienda enviar un encabezado `User-Agent` que identifique el dominio o nombre de la aplicación cliente, y su versión, para que el proveedor pueda reconocer el origen de las solicitudes. Por ejemplo:

```http
User-Agent: GuiaPlaza.com_ClimaLatam/1.0
```

Los ejemplos usan ese valor como referencia. Reemplázalo por el dominio o nombre y versión de tu propio sitio o aplicación; no copies un identificador que no te pertenezca.

## cURL

```bash
curl --fail --silent --show-error \
  --max-time 10 \
  --user-agent 'GuiaPlaza.com_ClimaLatam/1.0' \
  https://deperu.com/api/rest/temperaturaAhora.json
```

`--user-agent` configura el encabezado `User-Agent`. `--fail` indica error para respuestas HTTP no exitosas y `--max-time` limita el tiempo de espera del cliente.

## Python

Requiere Python 3.8 o posterior. Usa solo la biblioteca estándar:

```python
import json
from urllib.error import HTTPError, URLError
from urllib.request import Request, urlopen

URL = "https://deperu.com/api/rest/temperaturaAhora.json"
USER_AGENT = "GuiaPlaza.com_ClimaLatam/1.0"  # Cámbialo por el dominio/nombre de tu app
request = Request(
    URL,
    headers={
        "Accept": "application/json",
        "User-Agent": USER_AGENT,
    },
)

try:
    with urlopen(request, timeout=10) as response:
        data = json.loads(response.read().decode("utf-8"))
except HTTPError as error:
    raise SystemExit(f"El servidor respondió HTTP {error.code}") from error
except (URLError, TimeoutError) as error:
    raise SystemExit(f"No se pudo consultar el servicio: {error}") from error
except json.JSONDecodeError as error:
    raise SystemExit(f"La respuesta no contiene JSON válido: {error}") from error

print("Servicio:", data.get("servicio", "sin nombre"))
print("Fuente:", data.get("fuente", "sin fuente"))

for ciudad in data.get("ciudades", []):
    if not isinstance(ciudad, dict):
        continue
    for nombre, valor in ciudad.items():
        if isinstance(valor, (int, float)) and not isinstance(valor, bool):
            print(f"{nombre}: {valor} °C")
        else:
            print(f"{nombre}: {valor}")
```

## PHP

Requiere PHP con la extensión cURL habilitada:

```php
<?php
$url = 'https://deperu.com/api/rest/temperaturaAhora.json';
$userAgent = 'GuiaPlaza.com_ClimaLatam/1.0'; // Cámbialo por el dominio/nombre de tu app
$curl = curl_init($url);

curl_setopt_array($curl, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_CONNECTTIMEOUT => 5,
    CURLOPT_TIMEOUT => 10,
    CURLOPT_USERAGENT => $userAgent,
    CURLOPT_HTTPHEADER => ['Accept: application/json'],
]);

$body = curl_exec($curl);
$status = curl_getinfo($curl, CURLINFO_HTTP_CODE);
$error = curl_error($curl);
curl_close($curl);

if ($body === false) {
    throw new RuntimeException("Error de conexión: {$error}");
}
if ($status < 200 || $status >= 300) {
    throw new RuntimeException("El servidor respondió HTTP {$status}");
}

$data = json_decode($body, true);
if (!is_array($data) || json_last_error() !== JSON_ERROR_NONE) {
    throw new RuntimeException('La respuesta no contiene JSON válido.');
}

printf("Servicio: %s\n", $data['servicio'] ?? 'sin nombre');
printf("Fuente: %s\n", $data['fuente'] ?? 'sin fuente');
foreach (($data['ciudades'] ?? []) as $ciudad) {
    if (!is_array($ciudad)) {
        continue;
    }
    foreach ($ciudad as $nombre => $valor) {
        printf("%s: %s%s\n", $nombre, (string) $valor,
            is_numeric($valor) ? ' °C' : '');
    }
}
```

## Node.js

Requiere Node.js 18 o posterior, que incluye `fetch`:

```js
const url = "https://deperu.com/api/rest/temperaturaAhora.json";
const userAgent = "GuiaPlaza.com_ClimaLatam/1.0"; // Cámbialo por el dominio/nombre de tu app

async function main() {
  let response;
  try {
    response = await fetch(url, {
      headers: {
        Accept: "application/json",
        "User-Agent": userAgent,
      },
      signal: AbortSignal.timeout(10_000),
    });
  } catch (error) {
    throw new Error(`No se pudo consultar el servicio: ${error.message}`);
  }

  if (!response.ok) {
    throw new Error(`El servidor respondió HTTP ${response.status}`);
  }

  let data;
  try {
    data = await response.json();
  } catch {
    throw new Error("La respuesta no contiene JSON válido.");
  }

  console.log(`Servicio: ${data.servicio ?? "sin nombre"}`);
  console.log(`Fuente: ${data.fuente ?? "sin fuente"}`);
  for (const ciudad of data.ciudades ?? []) {
    if (!ciudad || typeof ciudad !== "object" || Array.isArray(ciudad)) continue;
    for (const [nombre, valor] of Object.entries(ciudad)) {
      const temperatura = typeof valor === "number" ? " °C" : "";
      console.log(`${nombre}: ${valor}${temperatura}`);
    }
  }
}

main().catch((error) => {
  console.error(error.message);
  process.exitCode = 1;
});
```

> Algunos entornos gestionados pueden controlar o restringir encabezados de solicitud. Si tu runtime no permite establecer `User-Agent` con `fetch`, utiliza un cliente HTTP que permita configurarlo y revisa la documentación de ese entorno.

## Java

Ejemplo para Java 11 o posterior usando `HttpClient`:

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;

public class TemperaturaPeru {
    public static void main(String[] args) throws Exception {
        var client = HttpClient.newBuilder()
                .connectTimeout(Duration.ofSeconds(5))
                .build();
        var request = HttpRequest.newBuilder()
                .uri(URI.create("https://deperu.com/api/rest/temperaturaAhora.json"))
                .timeout(Duration.ofSeconds(10))
                .header("Accept", "application/json")
                .header("User-Agent", "GuiaPlaza.com_ClimaLatam/1.0") // Sustituye por tu dominio/app
                .GET()
                .build();

        var response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() < 200 || response.statusCode() >= 300) {
            throw new IllegalStateException("El servidor respondió HTTP " + response.statusCode());
        }
        // La respuesta es JSON. Usa una biblioteca JSON (por ejemplo, Jackson o Gson)
        // para analizarla y contempla que el valor de una ciudad puede ser texto o número.
        System.out.println(response.body());
    }
}
```

## Manejo de respuesta y errores

- Comprueba el código HTTP antes de interpretar el cuerpo.
- Usa un timeout para que una conexión lenta no bloquee indefinidamente tu aplicación.
- Valida que el JSON tenga la estructura esperada y que `ciudades` sea una lista.
- Los valores de ciudad pueden ser números o textos (por ejemplo, `"pronto"`); no conviertas todos los valores directamente a números.
- Si recibes una respuesta de bloqueo o error, verifica la IP de salida y reduce la frecuencia de solicitudes. No intentes evadir restricciones geográficas o de acceso.
- Configura un `User-Agent` que identifique tu dominio o aplicación y su versión; sustituye el valor de ejemplo por el tuyo.

## Caché y frecuencia

Como el dato se actualiza cada 2 horas, una integración informativa puede almacenar localmente la respuesta durante ese período y reutilizarla. Si haces varias consultas por otros motivos, no excedas 1 solicitud por segundo. La caché reduce tráfico y mejora la experiencia cuando el proveedor no está disponible.

Los ejemplos son demostrativos y no incluyen una biblioteca de caché. Añade almacenamiento adecuado para tu aplicación y respeta las condiciones del proveedor.
