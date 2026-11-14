# Restricciones y políticas del servicio

## Frecuencia de actualización

El servicio se actualiza cada 2 horas. Esto implica que los consumidores deben considerar una ventana de actualización razonable al mostrar información climática.

## Restricción geográfica

El acceso está limitado a direcciones IP ubicadas en Perú. En implementación real, esto normalmente se valida a nivel de infraestructura, proxy, firewall o middleware de la aplicación.

## Rate limiting

La política de uso indica un límite de 1 solicitud por segundo. Este control ayuda a prevenir abusos y garantizar la estabilidad del servicio.

## Buenas prácticas

- Mantener un cache local de las respuestas.
- Respetar el límite de 1 rps.
- Evitar solicitudes en loops o polling agresivo.
- Monitorear errores de acceso y bloqueos por IP.

## Importante

Debido a que el servicio puede depender de una fuente externa, es recomendable documentar claramente la naturaleza informativa del endpoint y no asumir SLA ni garantías de disponibilidad.
