# Documentación Técnica: Career-Ops Multi-Tenant (Academy Edition)

## 1. Resumen Ejecutivo (El Qué)
La skill `career-ops` ha evolucionado de ser una herramienta de búsqueda de empleo individual ("Single-Player") a una plataforma multi-usuario (Modo Agencia/Academia) gestionada por un mentor. Se ha añadido la capacidad de recibir el parámetro `{{alumno}}` en el enrutamiento de la skill, permitiendo al sistema procesar, evaluar y generar contenido específico para múltiples perfiles de forma simultánea y aislada.

## 2. Justificación (El Porqué)
La necesidad principal de esta actualización es la **escalabilidad del servicio de mentoría**. Para que un coach técnico (como Ari) pueda gestionar a cientos de alumnos de forma efectiva, es inviable depender de configuraciones globales únicas. El objetivo de la *Academy Edition* es aislar el conocimiento de la IA sobre cada alumno. Al hacerlo, la IA puede "conocer" los antecedentes, el stack técnico y las limitaciones de cada individuo sin mezclar contextos, permitiendo procesar un volumen masivo de vacantes y entregar únicamente aquellas que representen un "match" real para cada estudiante.

## 3. Arquitectura de Directorios (El Cómo)
El sistema ahora se basa en una estructura aislada por cada alumno dentro del directorio de datos.

```text
data/alumnos/
└── nombre_alumno/
    ├── cv.md              # Historia profesional, experiencia y educación
    ├── workbook.md        # Filtros críticos y reglas de negocio
    └── recomendaciones.md # Output generado por el bot (Matches)
```

### Funciones de los archivos:
- **`cv.md`:** Actúa como la fuente de verdad de la experiencia profesional del alumno (proyectos, impacto, métricas).
- **`workbook.md`:** Funciona como las **reglas inamovibles de éxito**. Contiene los filtros críticos que el bot usará para descartar ofertas.

*Ejemplo de estructura de `workbook.md`:*
```markdown
## Stack Técnico (Requisitos Excluyentes)
- Principal: .NET Core, C#, SQL Server
- Secundario: PHP, Laravel

## Banda Salarial Objetivo
- Mínimo (Walk-away): 900 USD
- Deseado: 1200 USD

## Nivel de Inglés
- Conversacional: B2
```

## 4. Lógica de Inyección de Contexto
El núcleo de la transición multi-tenant reside en la modificación del Router principal (`SKILL.md`). El frontmatter ha sido actualizado para aceptar el parámetro `alumno`:

```yaml
args: mode alumno
argument-hint: "[scan | pdf | ... | agency-hunter] [nombre_del_alumno | all]"
```

**Mecanismo de Inyección (Context Loading):**
Cuando se invoca el comando con un alumno específico (ej. `/career-ops agency-hunter facundo_tucci`), el enrutador detecta el argumento y ejecuta una operación obligatoria de `Read` *antes* de iniciar el modo. Carga en la memoria de contexto los archivos `cv.md` y `workbook.md` de la ruta `data/alumnos/{{alumno}}/`. 
Esta inyección temprana garantiza que las directivas del `workbook.md` se establezcan como el marco de referencia **estricto e inamovible** para la evaluación del LLM en ese ciclo, evitando alucinaciones o cruces de datos con el perfil global o el de otros alumnos.

## 5. Modo Agency-Hunter
El `agency-hunter` es un nuevo modo diseñado para operar como un agente de reclutamiento autónomo. 

**Comportamiento Operativo:**
1. **Ingesta de Ofertas:** Recibe un lote de ofertas laborales (desde un archivo `inbox.md`, el portapapeles o búsquedas web previas).
2. **Filtrado Estricto:** Compara cada oferta contra los parámetros del `workbook.md`. Si una oferta ofrece 800 USD y el "Walk-away" del alumno es 900 USD, la oferta es descartada automáticamente.
3. **Generación de Reportes:** Por cada "match" exitoso, genera un reporte justificado con el porcentaje de afinidad en el archivo `recomendaciones.md` del alumno correspondiente.
4. **Ejecución en Lote (`all`):** Si se invoca con `/career-ops agency-hunter all`, el agente itera sobre todos los subdirectorios de `data/alumnos/`, repitiendo el proceso de carga de contexto y evaluación de forma secuencial.

*Ejemplo de uso en terminal:*
```bash
> /career-ops agency-hunter facundo_tucci
> /career-ops agency-hunter all
```

## 6. Resultados Esperados
La implementación de esta arquitectura garantiza:
- **Automatización total del filtrado de vacantes:** Reducción drástica del tiempo manual de revisión de JDs (Job Descriptions) por parte del mentor.
- **Personalización masiva (Mass Personalization):** Capacidad de entregar oportunidades altamente curadas a docenas de alumnos simultáneamente.
- **Generación de reportes de valor:** Los alumnos reciben un documento con las mejores ofertas y la justificación técnica de por qué aplican.
- **Escalabilidad del negocio de mentoría:** El mentor puede enfocarse en el coaching estratégico uno a uno, mientras la IA opera como una agencia de búsqueda en el backend.

---

## 7. Setup y Despliegue (Para GitHub)

Para comenzar a utilizar la plataforma en modo Academia/Agencia, clona este repositorio y sigue estos pasos:

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/tu-usuario/career-ops.git
   cd career-ops
   ```
2. **Instalar dependencias:**
   Asegúrate de tener Node.js instalado (v18+ recomendado).
   ```bash
   npm install
   # Instalar navegadores para Playwright (usado en la generación de PDFs)
   npx playwright install chromium
   ```
3. **Configurar el entorno:**
   - Copia el archivo `config/profile.example.yml` a `config/profile.yml` (para el perfil del mentor o perfil base).
   - Verifica que exista la estructura base `data/alumnos/`. Puedes crear una carpeta para tu primer alumno añadiendo sus archivos `cv.md` y `workbook.md`.
4. **Uso con Gemini CLI / Claude Code:**
   Activa la skill en tu CLI de preferencia y comienza a gestionar alumnos:
   ```bash
   /career-ops agency-hunter all
   ```