# Sistema Integrado de Asistencia Lectora y Evaluación (Semillero de Escritura)

## Visión y Objetivos del Proyecto

El desarrollo de este sistema dota al semillero de escritura de una infraestructura técnica autónoma que automatiza la extracción, clasificación y consulta de figuras literarias, separando el análisis de obras canónicas de la evaluación de ejercicios estudiantiles.

### Objetivos Centrales:
1. **Democratización del Análisis Estilístico:** Proveer una biblioteca interactiva donde los estudiantes consulten cómo diversos autores emplean estructuras gramaticales complejas. La visualización superpone etiquetas dinámicas sobre párrafos inmutables.
2. **Evaluación Objetiva y Escalable:** Integrar un bot de Discord que procese las entregas de los estudiantes y determine el cumplimiento de restricciones estilísticas específicas.
3. **Optimización de Costos, Cero Censura y Privacidad:** Utilizar modelos de lenguaje abiertos (vía OpenRouter) para evitar censura en textos literarios, delegar el enrutamiento, vectorización y protección de datos a Cloudflare Workers, y mantener a Supabase como una bóveda de almacenamiento estricta y privada.

---

## Arquitectura y Flujo de Datos

```text
                            [ESTUDIANTES Y ADMINISTRADORES]
                                          │
                  ┌───────────────────────┴───────────────────────┐
                  ▼                                               ▼
      [App 1: Formulario Web / Visualizador]            [App 2: Bot Discord]
      (Ingesta Atómica y Exploración)                   (Evaluador de Ejercicios)
                  │                                               │
                  │ 1. Verificación Previa (Dropdown)             │
                  │    ¿Existe la obra/autor?                     │
                  ├───────────────────┐                           │
                  │ NO EXISTE         │ SÍ EXISTE                 │
                  ▼                   ▼                           │
        [Módulo de Subida]    [Módulo de Visualización]           │
        - Fragmentación       - Selección de "Toggles"            │
          estructural ciega   - Resaltado dinámico en frontend    │
        - Sin uso de LLM      - Sin uso de LLM                    │
                  │                   │                           │
                  └─────────┬─────────┘                           │
                            ▼                                     ▼
                      ┌────────────────────────────────────────┐
                      │    CLOUDFLARE WORKERS (SECURITY PROXY) │
                      │  - Enrutamiento y control de acceso    │
                      │  - Sanitización y devolución de JSON   │
                      │  - Embedding gratuito (Workers AI)     │
                      └───────────────────┬────────────────────┘
                                          │
                  ┌───────────────────────┴───────────────────────┐
                  ▼                                               ▼
      [SUPABASE (BASE DE DATOS RELACIONAL)]            [OPENROUTER API]
      - `authors`                                      - Modelos Sin Censura (DeepSeek/Llama)
      - `works`                                        - Análisis semántico asíncrono
      - `chunks` (raw_text + pgvector)                 - Extracción de "matched_substrings"
      - `literary_figures` (anotaciones)               - Evaluación de entregas
      - `student_submissions`
