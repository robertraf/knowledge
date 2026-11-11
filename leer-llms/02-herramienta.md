# 02 · Propuesta: Lente

Una capa entre el agente y tú que convierte cada respuesta en una **tarjeta de decisiones**. El texto original queda a un clic.

## Por qué no basta con otro skill de estilo

Caveman, i-have-adhd y los output styles actúan sobre el **modelo que escribe**. Tienen tres problemas:

- **No hay garantía de cumplimiento.** La propia documentación de Claude Code dice que un output style es una instrucción, no un contrato.
- **Comprimir pierde información** y nadie lo comprueba.
- **El formato sigue siendo texto lineal.** No hay tabla de opciones, ni diff entre turnos, ni evidencia enlazada.

Lente actúa sobre **lo que lees**: extrae una estructura validada y la dibuja.

## El esquema

Toda respuesta se reduce a esto (JSON validado; los campos vacíos no se muestran):

```
decisiones[]                 ← lo que TÚ tienes que hacer
  pregunta
  recomendación + confianza (alta/media/baja + por qué, en primera persona)
  opciones[]: ganas | pierdes | reversible (puerta de 1 o 2 vías)
  por defecto si no respondes   ← solo en puertas de 2 vías
  qué cambiaría mi recomendación
sorpresas[]                  ← lo que NO salió como esperabas
afirmaciones[]               ← hecho | inferencia | suposición, con evidencia (archivo:línea, comando, URL)
hecho[]                      ← lo completado, colapsado por defecto
original                     ← el texto completo, siempre accesible
```

El tamaño de la tarjeta lo fija el número de decisiones (D), no el trabajo:

- **D = 0:** una línea ("Hecho. Nada que decidir.") y las sorpresas, si las hay.
- **D = 1–3:** una tarjeta por decisión.
- **D ≥ 4:** "decide ahora" (máximo 3) y el resto en cola.

## Arquitectura

```
Claude Code / Codex / chat
        │  respuesta en texto
        ▼
[1] Extractor (modelo barato, salida estructurada → esquema)
        │
[2] Verificador de fidelidad
        │   ¿se conservan negaciones, advertencias, números, rutas?
        │   ¿cada afirmación "hecho" tiene evidencia?
        ▼
[3] Render: tarjeta HTML / panel lateral / terminal
        │   + diff de decisiones respecto al turno anterior
        ▼
[4] Tu respuesta: clic en una opción → vuelve al agente como mensaje
```

**Dónde se engancha.** Un hook `Stop` de Claude Code recibe cada respuesta final. Pasa el texto por el extractor y abre o actualiza la tarjeta. El paso [4] cierra el ciclo: decidir es un clic, no escribir un párrafo.

**Por qué un extractor aparte y no pedir JSON al agente.** No hace falta cambiar el agente y funciona con cualquier fuente (Claude, Codex, un chat pegado). Además, el verificador puede comparar contra el original. Más adelante, un output style "decision-first" puede hacer que el extractor casi no tenga trabajo.

## Plan por fases

Cada fase termina en algo que se puede usar y medir.

1. **Esquema + output style** (1–2 días). Escribir el esquema y un output style `decision-first` que lo pida en markdown. Usarlo una semana. *Pregunta:* ¿el esquema captura lo que de verdad decido?
2. **Extractor + tarjeta en terminal** (2–3 días). Un CLI `lente` que lee texto (stdin o el último turno del transcript) y produce la tarjeta. *Pregunta:* ¿el extractor encuentra las decisiones que yo encontraría?
3. **Verificador de fidelidad** (2 días). Comprobaciones deterministas (negaciones, números, rutas, advertencias) más un pase con un LLM juez. *Pregunta:* ¿cuánto se pierde?
4. **Hook + tarjeta HTML interactiva** (3–4 días). Panel con opciones clicables, evidencia expandible, diff entre turnos y el original colapsado.
5. **Medir** de forma continua. No medir tokens ni velocidad de lectura, sino:
   - tiempo hasta la decisión;
   - decisiones revertidas después;
   - veces que abriste el original porque la tarjeta no bastaba.

## Riesgos

- **Lo pulido parece correcto aunque no lo sea** (Bansal 2021). Mitigación: la evidencia va al lado de cada afirmación y el original queda a un clic.
- **El extractor puede inventar decisiones u omitirlas.** Mitigación: el verificador de fidelidad y la métrica de "abrí el original".
- **Una latencia alta mata el flujo.** Mitigación: modelo pequeño, *streaming* y la tarjeta en terminal primero.
