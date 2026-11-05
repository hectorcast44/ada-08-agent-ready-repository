# AI Usage Log

## Entrada 1: Auditoría inicial BEFORE
- Contexto: Evaluación del baseline funcional derivado de ADA-05.
- Propuesta del asistente: Asignar una calificación inicial de 3/5 a Automation & Guardrails argumentando que la presencia de pruebas con pytest contaba como automatización.
- Decisión tomada: Rechacé la sugerencia y reduje el puntaje a 1/5. Pytest verifica lógica de negocio de pruebas individuales, pero no valida la integridad del repositorio, contratos de documentación, variables de entorno ni revisiones de formato. El baseline carecía por completo de hooks, scripts unificados o pipelines de CI.
- Resultado: La auditoría BEFORE reflejó una puntuación realista de 47/100, identificando la ausencia de guardrails ejecutables como fricción principal.

## Entrada 2: Diseño y estructura de AGENTS.md
- Contexto: Redacción del contrato operativo en `AGENTS.md`.
- Propuesta del asistente: Incluir un resumen completo de los requisitos funcionales (`FR-01` a `FR-06`) dentro de `AGENTS.md` para evitar que el agente tuviera que abrir `REQUIREMENTS.md`.
- Decisión tomada: Rechacé duplicar requisitos en `AGENTS.md`. Duplicar especificaciones genera divergencias cuando los requisitos cambian. Diseñé `AGENTS.md` para actuar como índice operativo y directriz de ejecución, apuntando a `REQUIREMENTS.md` y `SPEC.md` como fuentes únicas de verdad.
- Resultado: `AGENTS.md` se mantuvo conciso (45 líneas) y enfocado en orden de lectura, comandos oficiales, reglas de cambio y condiciones de detención.

## Entrada 3: Conversión de reglas a software (Guardrails)
- Contexto: Protección de rutas sensibles y detección de archivos prohibidos.
- Propuesta del asistente: Agregar una advertencia textual en el prompt indicando al modelo que no modifique `data/customers.json` ni agregue archivos de configuración local.
- Decisión tomada: Convertí la regla en código verificable mediante `scripts/check_repo_contract.py`. El script inspecciona el sistema de archivos buscando extensiones prohibidas (`.env`, `.key`, `.pem`), comprueba que `data/customers.json` exista y sea un JSON válido, y valida que `SPEC.md` referencie identificadores de requisitos.
- Resultado: La restricción se volvió determinista y verificable por máquina, emitiendo un código de salida distinto de cero si se viola la regla.

## Entrada 4: Descubribilidad y conocimiento implícito
- Contexto: Análisis de conocimiento tribal detectado en la ejecución BEFORE.
- Propuesta del asistente: Crear múltiples archivos de documentación detallada explicando la arquitectura de cada módulo de Python.
- Decisión tomada: Rechacé crear documentación redundante. Sinteticé la navegación en un único archivo operativo: `docs/REPO_MAP.md`. El mapa especifica los puntos de entrada exactos, la ubicación de las pruebas correspondientes, una guía de cambio de cinco pasos y la lista de rutas protegidas.
- Resultado: El agente pasó de recorrer directorios mediante búsquedas ciegas a consultar directamente los archivos señalados en la guía de cambio.

## Entrada 5: Contrato compartido de verificación y CI
- Contexto: Configuración de la verificación en integración continua.
- Propuesta del asistente: Escribir comandos independientes dentro de `.github/workflows/verify.yml` para instalar dependencias y ejecutar pytest con parámetros específicos de CI.
- Decisión tomada: Unifiqué el contrato de verificación. El flujo de trabajo de GitHub Actions delega la validación completa al mismo script local: `./scripts/verify_repo.sh`.
- Resultado: Desarrolladores humanos, agentes de codificación y el pipeline de CI comparten exactamente el mismo criterio de aceptación y los mismos códigos de salida.

## Entrada 6: Comparación de ejecución AFTER
- Contexto: Ejecución de la tarea controlada `TASK_UNDER_TEST.md` en el repositorio mejorado.
- Observación del asistente: El agente consultó `AGENTS.md` y `docs/REPO_MAP.md`, procedió directamente a editar `src/customer_search/service.py` y `src/customer_search/cli.py`, añadió pruebas en los archivos indicados y validó todo con `./scripts/verify_repo.sh`.
- Evaluación: Cero comandos fallidos y cero suposiciones erróneas sobre nombres de fixtures. La ejecución completó los 44 tests, la verificación de sintaxis y el contrato del repositorio en un único intento.

## Entrada 7: Evaluación final y límites de autonomía
- Contexto: Identificación de operaciones que continúan exigiendo supervisión humana.
- Análisis: Aunque el repositorio provee guardrails ejecutables, ciertas decisiones no deben automatizarse sin revisión: modificaciones a la arquitectura de capas, cambios en los esquemas de datos de entrada o adición de dependencias de terceros.
- Decisión tomada: Se formalizaron stop conditions explícitas en `AGENTS.md` y `docs/REPO_MAP.md`, obligando al agente a detenerse y solicitar confirmación humana cuando detecte conflictos en requisitos o modificaciones en rutas protegidas.

## ADA-09: Agentic Code Review

### Entrada 8: Definicion y refinamiento del Change Request (CR-02)
- Contexto: Seleccion del cambio para el ciclo de revision agentica.
- Propuesta del asistente: Crear una modificacion para agregar busqueda por telefono o soporte para expresiones regulares en las consultas.
- Decision tomada: Rechace la idea de agregar expresiones regulares o nuevos campos de busqueda porque violaba VR-03 y ampliaba el alcance sin necesidad. Opte por definir CR-02 para anadir un parametro de limite opcional (`limit`) con su bandera en CLI. Esta tarea tocaba validacion, servicio, presentacion y pruebas, manteniendo acotado el alcance.
- Resultado: Se documento `CHANGE_REQUEST.md` referenciando FR-05, FR-06, NFR-02 y los criterios de aceptacion correspondientes, con aprobacion humana previa (Gate 0).

### Entrada 9: Plan del Coder Agent y decision humana
- Contexto: Planificacion de la implementacion inicial del cambio.
- Propuesta del asistente: El coder agent propuso validar el limite dentro del CLI y aplicar el corte de lista antes de crear el objeto `SearchResult`.
- Decision tomada: Autorice proceder con esa implementacion inicial sin intervenir sus defectos sutiles. La meta de ADA-09 es comprobar si la capa de revision independiente y los checks detectan fallas cuando los tests parecen estar en verde.
- Resultado: El coder genero el commit candidato `1e9b45a` y paso el Verification Gate con 46 tests verdes, produciendo falsa confianza.

### Entrada 10: Implementacion inicial y autocomprobacion del Coder
- Contexto: Generacion del reporte `results/coder-report.md`.
- Observacion del asistente: El coder agent noto en sus limitaciones conocidas que no habia usado la clase validadora y que su prueba unitaria comprobaba que `total_matches` fuera igual a 2 (el limite solicitado). A pesar de eso, marco su propio self-check como listo para revision.
- Evaluacion: Se confirmo la regla central de separacion de roles: el coder agent puede autoevaluarse, pero su informe no sustituye una revision independiente ni constituye aprobacion de merge.

### Entrada 11: Deteccion de inconsistencias por el Reviewer Agent
- Contexto: Revision independiente de solo lectura sobre el diff candidato.
- Hallazgos del reviewer: El agente revisor identifico de forma autonoma tres fallas graves (MUST FIX):
  1. RF-01: `SearchResult.total_matches` reportaba la cantidad de elementos cortados en lugar del total de coincidencias del repositorio.
  2. RF-02: La prueba en `test_service.py` enmascaraba el error al esperar 2 coincidencias sobre un conjunto de 3 registros.
  3. RF-03: La validacion del limite se habia colocado en `service.py` con una importacion local, ignorando `src/customer_search/validator.py`.
- Resultado: El reviewer clasifico correctamente el PR como NOT READY y documento evidencia con archivo y linea para cada punto en `results/review-report.md`.

### Entrada 12: Decisiones del Human Reviewer
- Contexto: Evaluacion de los findings por parte del revisor humano en `results/human-review-decisions.md`.
- Decision tomada: Acepte RF-01, RF-02, RF-03, RF-04 y RF-05. Para RF-06 (clasificado inicialmente como OPTIONAL por el reviewer), modifique su severidad a SHOULD FIX. En Python, `bool` es subclase de `int`, por lo que pasar `limit=True` podia saltarse la validacion si no se comprobaba explicitamente.
- Accion requerida: Se ordeno al coder corregir el calculo de coincidencias, mover la validacion a `SearchQueryValidator`, ampliar los casos de prueba y rechazar booleanos.

### Entrada 13: Ciclo de correccion y Re-review independiente
- Contexto: Aplicacion de cambios requeridos y segunda pasada del revisor.
- Accion del coder: Se creo el metodo `validate_limit` en `validator.py`, se capturo `total_matches` antes del corte en `service.py`, se corrigio la asercion en `test_service.py` y se anadieron pruebas para entradas invalidas y comportamiento del CLI. Todo se compilo y verifico con 58 tests verdes en el commit `ca449fb`.
- Evaluacion del re-review: El agente revisor inspecciono el diff actualizado y verifico cada finding: los 6 puntos quedaron clasificados como RESOLVED, sin regresiones nuevas.
- Resultado: Con la evidencia consolidada en `results/PR_EVIDENCE.md`, el revisor humano aprobo el estado final READY FOR HUMAN MERGE.

