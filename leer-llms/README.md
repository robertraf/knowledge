# Leer (y decidir con) el output de un LLM

Leer lo que escribe un LLM cuesta demasiado, y decidir a partir de ello cuesta aún más. Quiero entender por qué pasa y construir una herramienta que lo resuelva.

## La conclusión en una línea

El problema no es la longitud. **El output está organizado según el trabajo que hizo el modelo, no según las decisiones que tiene que tomar el lector.** Comprimir el texto (caveman, modo conciso) ayuda poco. Lo que sirve es reorganizarlo alrededor de las decisiones y abaratar la verificación.

## Recorrido

1. [Investigación](01-investigacion.md): qué dicen Karpathy y otros, qué herramientas existen, qué dice la evidencia académica y qué formatos de decisión ya funcionan.
2. [Propuesta de herramienta](02-herramienta.md): diseño de *Lente*, una capa que convierte el output en una tarjeta de decisiones.

## Preguntas abiertas

- ¿Una UI generada mejora la *calidad* de la decisión, o solo la preferencia? Nadie lo ha medido (Google 2025 midió preferencia).
- ¿Cuánta información se pierde al comprimir? Un experimento informal con ASD-STE100 perdió el 46.8 % de los hechos (fuente secundaria, sin verificar).
- ¿Bullets, prosa o tablas? No encontramos evidencia sólida para ninguno.
