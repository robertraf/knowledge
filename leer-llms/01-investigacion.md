# 01 · Investigación

Fecha: 11 de noviembre de 2026. Cuatro frentes investigados en paralelo y sintetizados aquí.

Marcas: **[V]** verificado en la fuente primaria · **[S]** solo vía fuente secundaria · **[NV]** no verificado.

## Resumen

| Frente | Hallazgo principal |
|---|---|
| Líderes de opinión | Karpathy: la gente odia leer texto; el "GUI de los LLMs" está por inventarse. El cuello de botella es verificar. |
| Herramientas | Todas comprimen o reordenan texto. Ninguna estructura la decisión ni verifica que la compresión no perdió nada. |
| Academia | Más explicación produce más aceptación ciega. Lo que reduce la sobreconfianza es **abaratar la verificación**. |
| Formatos de decisión | BLUF, Minto, puertas de una/dos vías, pre-mortem, ADR: llevan décadas resolviendo esto para humanos. |

---

## 1. Qué dicen los líderes de opinión

### Karpathy

- **"Year in review 2025"** [V], 19 dic 2025, <https://karpathy.bearblog.dev/year-in-review-2025/>
  - "People actually dislike reading text - it is slow and effortful."
  - "people love to consume information visually and spatially"; "LLMs should speak to us in our favored format".
  - "But who is actually going to build the LLM GUI?" Ve en nano banana "a first early hint". Compara el chat actual con una consola de los años 80.
- **Escalera de formatos** [S], hilo en X de ~oct 2026, citado por [Search Engine Journal](https://searchenginejournal.com/karpathy-llm-aircraft-manual-writing/591813). Propone subir en abstracción para entender y supervisar el output: inglés técnico controlado (ASD-STE100), diagramas, páginas HTML interactivas, vídeo. Pide "large, custom, discardable software artifacts".
  - Contrapeso [NV]: un experimento de Lucian Ghinda halló que ASD-STE100 estricto perdió el 46.8 % de los hechos frente al 8.5 % del inglés llano.
- **"Voice in, visuals out"** [S], may 2026, vía [Allen Pike](https://allenpike.com/2026/voice-in-visuals-out): "Audio is the human-preferred input to AIs, but vision is the preferred output from them".
- **Software 3.0** [S], YC AI Startup School, jun 2025: autonomía parcial, "autonomy slider" y GUIs que aceleran el ciclo generar → verificar ("the faster the loop the better").
- **"Verifiability"** [V], nov 2025, <https://karpathy.bearblog.dev/verifiability/>: lo verificable es lo que se automatiza. Ojo: no dice que el humano sea el cuello de botella.

### Otros

- **Amelia Wattenberger**, "Why Chatbots Are Not the Future" [V], <https://wattenberger.com/thoughts/boo-chatbots>: "it's laborious to figure out what concretely has changed" entre respuestas; falta un "working buffer"; "There is no 'flow' state if I'm stopping every few seconds to read a response."
- **Geoffrey Litt**, "Malleable software in the age of LLMs" [V], <https://www.geoffreylitt.com/2023/03/25/llm-end-user-programming>: "chat is an essentially limited interaction mode, regardless of the quality of the bot."
- **Sean Goedecke**, "The OpenAI house style is exhausting" [V], <https://seangoedecke.com/chatgpt-house-style/>: el estilo de remate en cada frase viene de las preferencias de los evaluadores de RLHF.
- **Simon Willison**, "slop" [S], <https://simonwillison.net/tags/slop>: contenido de IA no pedido y no revisado.
- **OpenAI**, parámetro `verbosity` de GPT-5 (low/medium/high) [S], <https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_new_params_and_tools>.

---

## 2. Qué herramientas existen

### Claude Code: output styles [V]

<https://code.claude.com/docs/en/output-styles>

- Instrucciones persistentes de rol, tono y formato. Estilos incluidos: Default, Proactive, Concise, Explanatory, Learning.
- Custom: archivos `.md` en `~/.claude/output-styles` o `.claude/output-styles`, con frontmatter `name`, `description`, `keep-coding-instructions`.
- **Límite clave, según la propia documentación:** son instrucciones, no garantías. Para garantizar algo hay que usar un hook. Tampoco se aplican a los subagentes.

### Skills comunitarios (SKILL.md leídos)

| Skill | Reglas clave | Qué optimiza |
|---|---|---|
| [caveman](https://github.com/JuliusBrussee/caveman) [V] | Respuesta primero; sin saludos ni relleno; ≤20 palabras por frase; nunca omitir `not/never/only`; código y errores literales | Tokens y costo. Su propio README admite que "be concise" ya da casi todo el ahorro (`/caveman` solo un 3 % extra en la mediana) |
| [i-have-adhd](https://github.com/ayghri/i-have-adhd) [V] | Primera línea = acción; pasos numerados; cerrar con UNA acción de <2 min; tangentes fuera; "paso 3 de 5" | Empezar y no perder el hilo |
| [i-really-have-adhd](https://github.com/alexh/i-really-have-adhd) [V] | **La longitud escala con las decisiones del lector, nunca con el trabajo hecho.** Presupuesto D: 0 decisiones → 1 línea; 1 → 1–3 líneas; 2–4 → lista; 5+ → "hacer ahora" (máx. 3) y "el resto". Lo que salió como se esperaba es invisible | Reportes inflados |
| [bluf](https://github.com/note89/bluf) [V] | Lo que necesitas antes de actuar va primero; una advertencia gana a una respuesta; no confirmar suposiciones, decir cómo verificarlas | Decisiones rápidas |

La idea más valiosa de todo el barrido es el **presupuesto de decisiones** de i-really-have-adhd.

### Productos

- **Gemini generative UI** [S] (nov 2025, [Google Research](https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/)): genera una interfaz por cada prompt. Se prefiere al markdown, pero tarda hasta un minuto y a veces se equivoca.
- **AG-UI / CopilotKit** [S] (<https://github.com/ag-ui-protocol/ag-ui>): protocolo de eventos agente ↔ app, con tres niveles de UI generativa.
- **NotebookLM** [S]: mapas mentales, audio y vídeo con citas.
- **AskUserQuestion** de Claude Code [S]: opciones estructuradas con previews. Resuelve ambigüedad, pero no explica consecuencias.
- **Claude Artifacts**: páginas HTML con estado. Es la forma más directa de dar un "GUI" a una respuesta.

### Lectura asistida

**Bionic Reading no funciona:** un estudio revisado por pares ([Acta Psychologica 2024](https://pubmed.ncbi.nlm.nih.gov/38723450/)) no encontró diferencias en velocidad de lectura. Descartado como base de la herramienta.

### Huecos que nada cubre

1. Ninguna herramienta impone una estructura de **consecuencias por opción** (trade-offs, reversibilidad, costo de equivocarse).
2. Nadie **verifica que la compresión no perdió** una negación o una advertencia.
3. Nadie separa **hecho / inferencia / suposición** en el output cotidiano de un agente.
4. No hay **diff de decisiones**: "qué cambió en lo que debes decidir desde el último turno".
5. Todo es un modo persistente on/off. Nada lo aplica con un hook.
6. Nadie mide **calidad de la decisión**, solo tokens o preferencia.

---

## 3. Qué dice la evidencia

### La verbosidad es un artefacto, no calidad

- **Singhal et al., COLM 2024** [V], <https://arxiv.org/abs/2310.03716>: los reward models de RLHF correlacionan fuerte con la longitud. Una recompensa que solo mide longitud reproduce la mayor parte de la "mejora" de RLHF.
- **Zhang et al., "Verbosity ≠ Veracity", 2024** [V], <https://arxiv.org/abs/2411.07858>: las respuestas verbosas tienen **más incertidumbre**. El relleno es una señal de que el modelo no sabe.
- **Zheng et al., MT-Bench, NeurIPS 2023** [V], <https://arxiv.org/abs/2306.05685>: los jueces LLM caen en el "repetitive list attack".

### Leemos escaneando

- **Nielsen Norman Group, patrón F** [V parcial], <https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/>: un bloque sin formato se lee en F y se pierden partes grandes. Lo decisivo debe ir al inicio de cada bloque.

### Decidir con IA: el hallazgo central

- **Bansal et al., CHI 2021** [V], <https://arxiv.org/abs/2006.14779>: las explicaciones **no** mejoraron al equipo humano+IA. Aumentaron la aceptación de la recomendación, fuera correcta o no.
- **Vasconcelos et al., CSCW 2023** [V], <https://arxiv.org/abs/2212.06823>: 5 estudios, N=731. La gente decide si verificar según costo y beneficio. Las explicaciones reducen la sobreconfianza **solo cuando abaratan la verificación**.
- **Buçinca et al., CSCW 2021** [V], <https://arxiv.org/abs/2102.09692>: las "cognitive forcing functions" (checklists, pausas) reducen la sobreconfianza, pero los usuarios las odian. Hay que usarlas solo en lo importante.
- **Kim et al., FAccT 2024** [V], <https://arxiv.org/abs/2405.00623>: la incertidumbre en primera persona y en la afirmación concreta ("no estoy seguro, pero…") mejora la precisión. La impersonal y global no.
- **Lee et al. (Microsoft Research), CHI 2025** [V], <https://doi.org/10.1145/3706598.3713778>: más confianza en la IA se asocia con menos pensamiento crítico. El trabajo se desplaza a verificar e integrar.

### Comparar es más fácil que leer

- **Gero et al., CHI 2024** [V], <https://arxiv.org/abs/2401.13726>: mostrar similitudes y diferencias entre muchas respuestas hizo tratables tareas imposibles de leer.
- **Graphologue** y **Sensecape**, UIST 2023 [V parcial]: convertir la respuesta en grafo o jerarquía alivia la memoria de trabajo. Muestras pequeñas.
- **Google, Generative UI 2025** [S]: la UI generada gana al markdown en preferencia, pero no se midió si se decide mejor.

---

## 4. Formatos de decisión que ya funcionan

- **BLUF** [V], ejército de EE. UU. (AR 25-50): la acción requerida primero, el contexto después.
- **Pirámide de Minto / SCQA** [V parcial], <https://lethain.com/pyramid-principle/>: respuesta primero, debajo 2–3 argumentos que no se solapen.
- **Puertas de una y dos vías** [V], carta de Bezos de 2016, <https://www.aboutamazon.com/news/company-news/2016-letter-to-shareholders>: "Many decisions are reversible, two-way doors. Those decisions can use a light-weight process". Decidir con ~70 % de la información.
- **ADR / MADR** [V parcial], <https://adr.github.io/>: decisión, opciones con pros y contras, consecuencias.
- **DACI / RAPID** [V parcial]: un solo decisor por decisión.
- **Pre-mortem** [V], Klein, HBR 2007: "asume que falló, ¿por qué?". Da permiso para nombrar riesgos.
- **Copilot Workspace** [V]: tarea → spec → plan editable → diff, con un punto de edición humana en cada nivel.
- **Curse of knowledge** [V parcial]: el agente conoce su contexto y omite lo que el lector no tiene.

---

## 5. Principios de diseño

Cada principio sale de la evidencia anterior.

1. **La longitud sigue a las decisiones, no al trabajo.** (i-really-have-adhd, Singhal 2024)
2. **Decisión primero, detalle bajo demanda.** (BLUF, Minto, patrón F)
3. **Abaratar la verificación en vez de explicar más.** Cada afirmación lleva su evidencia a un clic. (Vasconcelos 2023, Bansal 2021)
4. **Incertidumbre local y en primera persona.** (Kim 2024, Zhang 2024)
5. **Opciones lado a lado, con reversibilidad.** Las puertas de dos vías llevan una opción por defecto y no bloquean. (Bezos, MADR, Gero 2024)
6. **Fricción solo en las puertas de una vía.** (Buçinca 2021)
7. **El original siempre a mano.** Lo pulido puede estar mal, y comprimir pierde hechos. (Karpathy, experimento ASD-STE100)
8. **Mostrar qué cambió, no todo de nuevo.** (Wattenberger)
9. **Imponerlo con estructura, no con un prompt.** Un output style no da garantías; un hook o un esquema validado sí. (docs de Claude Code)

## Lo que no cubrimos

- Swyx, Dwarkesh, Mollick, Hamel Husain; diseñadores de Linear; UIs de Devin y Cursor; Superhuman y el triage tipo inbox; cultura de escritura de Stripe y Shopify.
- El texto oficial de ASD-STE100 y la evidencia sobre memoria de trabajo (Cowan 2001) y lenguaje claro (ISO 24495-1, W3C COGA).
- Algunos números (cifras de estrellas, porcentajes de los README de skills) son autorreportados.
