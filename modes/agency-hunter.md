# Modo: Agency-Hunter (Multi-Tenant)

## Objetivo
Evaluar y filtrar ofertas de trabajo a gran escala para uno o múltiples alumnos, actuando como una agencia de reclutamiento automatizada.

## Flujo de Ejecución

1. **Recepción de Ofertas:**
   - Lee las ofertas provistas (ya sea a través del portapapeles, de un archivo `inbox.md` general o extraídas mediante web search en el prompt).

2. **Evaluación Multi-Tenant:**
   - Si el argumento `{{alumno}}` es distinto de 'all':
     - Analiza las ofertas cargadas cruzándolas **estrictamente** con el contenido del archivo `workbook.md` inyectado en memoria.
     - Aplica como filtros excluyentes: Stack Técnico, Banda Salarial y Nivel de Inglés.
     - Genera un archivo `recomendaciones.md` en `data/alumnos/{{alumno}}/recomendaciones.md`.
     - En el archivo, detalla los mejores "matches", incluyendo un porcentaje estimado de afinidad justificado. Excluye explícitamente aquellas ofertas que no cumplen con los requisitos mínimos del `workbook.md`.

   - Si el argumento `{{alumno}}` es 'all':
     - Utiliza herramientas para listar todas las carpetas dentro de `data/alumnos/`.
     - Por cada carpeta de alumno encontrada:
       - Carga dinámicamente su `cv.md` y `workbook.md`.
       - Evalúa todas las ofertas disponibles contra ese alumno específico.
       - Crea un archivo `recomendaciones.md` en la carpeta de ese alumno con los "matches" correspondientes.

3. **Salida:**
   - Una vez finalizada la evaluación (para uno o todos los alumnos), proporciona un resumen breve en la terminal indicando cuántas ofertas se emparejaron exitosamente para cada alumno procesado.