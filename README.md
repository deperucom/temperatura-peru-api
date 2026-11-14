# Temperatura actual de ciudades del Perú — API de DePeru.com

Repositorio informativo sobre el servicio de temperatura actual de [DePeru.com](https://www.deperu.com/clima/) y su endpoint JSON. Reúne la URL, el formato de respuesta conocido, las restricciones de acceso y ejemplos sencillos para consultar los datos desde distintas plataformas.

> **Este repositorio no es la API ni está afiliado oficialmente a DePeru.com.** Los datos y el servicio son proporcionados por DePeru.com. Consulta el [sitio de clima](https://www.deperu.com/clima/) como fuente original.

## Enlaces

- **Página de clima:** https://www.deperu.com/clima/
- **Endpoint JSON:** https://deperu.com/api/rest/temperaturaAhora.json

## ¿Qué ofrece el servicio?

El endpoint entrega una respuesta JSON con información de temperatura actual para ciudades del Perú. La respuesta de ejemplo contiene el nombre del servicio, la fuente, un enlace al sitio de clima y una lista de ciudades con sus valores.

### Características conocidas

| Propiedad | Detalle |
| --- | --- |
| Servicio | Temperatura actual |
| Fuente | DePeru.com |
| Actualización | Cada 2 horas |
| Restricción geográfica | Acceso permitido solo desde IP del Perú |
| Límite de solicitudes | 1 solicitud por segundo (1 req/s) |
| Formato observado | JSON |

Estas características se documentan a partir de la información disponible para este proyecto. La disponibilidad, el contenido y las políticas pueden cambiar; verifica la respuesta del endpoint y la información publicada por el proveedor.

## Ejemplo de respuesta

```json
{
  "servicio": "Temperatura actual",
  "fuente": "DePeru.com",
  "enlace": "https://www.deperu.com/clima/",
  "ciudades": [
    { "lima": 27 },
    { "arequipa": "pronto" },
    { "trujillo": "pronto" }
  ]
}
```

El ejemplo es ilustrativo; los valores pueden variar. `ciudades` es una lista de objetos. El valor puede ser una temperatura numérica o una cadena como `"pronto"` cuando el dato no está disponible todavía. Por ello, no asumas que todos los valores son números.

## Identifica tu aplicación con User-Agent

Se recomienda enviar un encabezado `User-Agent` personalizado en las solicitudes. Incluye el dominio o nombre de la aplicación que consulta los datos para que el proveedor pueda identificar el origen de las peticiones. Por ejemplo:

```http
User-Agent: GuiaPlaza.com_EuroxDolar/1.0
```

Sustituye `GuiaPlaza.com_EuroxDolar/1.0` por el dominio o nombre y versión de tu propio sitio o aplicación. No uses un identificador que no te pertenezca. Los ejemplos de la [guía técnica](docs/guia-tecnica.md) muestran cómo configurarlo.

## Consulta rápida

Desde una IP permitida, puedes probar con `curl` y un User-Agent identificable:

```bash
curl --fail --silent --show-error \
  --user-agent 'GuiaPlaza.com_EuroxDolar/1.0' \
  https://deperu.com/api/rest/temperaturaAhora.json
```

Para ejemplos con manejo de errores en PHP, Python y Node.js, consulta la [guía técnica](docs/guia-tecnica.md).

## Recomendaciones de consumo

- Envía un `User-Agent` que identifique tu dominio o aplicación y su versión; reemplaza el ejemplo por tus propios datos.
- Respeta el límite documentado de **1 solicitud por segundo**; no hagas polling rápido ni solicitudes en bucle.
- Dado que los datos se actualizan cada 2 horas, normalmente es mejor consultar como máximo cada 2 horas y guardar la respuesta en caché.
- El acceso está restringido a IPs del Perú. Una aplicación alojada fuera del país podría no poder consultar el endpoint directamente.
- Maneja errores de red, respuestas HTTP no exitosas y JSON inválido.
- Comprueba el tipo del valor antes de mostrarlo o procesarlo como temperatura.
- No uses el servicio para aplicaciones críticas ni asumas garantías de disponibilidad (SLA).

## Uso responsable y atribución

Al mostrar los datos, conserva una atribución visible a **DePeru.com** y, cuando corresponda, enlaza a https://www.deperu.com/clima/. Antes de redistribuir los datos o incorporarlos a un producto, revisa las condiciones de uso y los derechos aplicables del proveedor. Este repositorio documenta el endpoint; no concede derechos sobre los datos ni ofrece una garantía de acceso.

## Documentación

- [Guía técnica de uso: cURL, PHP, Python, Node.js y Java](docs/guia-tecnica.md)
- [Descripción del servicio y estructura de la respuesta](docs/servicio.md)
- [Restricciones y buenas prácticas](docs/limitaciones.md)

## Aviso

Este proyecto es independiente y tiene fines informativos. No es un proxy, cliente oficial ni servicio de DePeru.com. La URL y las características aquí descritas pueden cambiar sin previo aviso; confirma la información con el proveedor antes de depender de ella.
