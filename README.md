# Career-Ops: Academy Edition (Multi-Tenant) 🚀

Esta es una evolución profesional del proyecto original [career-ops](https://github.com/santifer/career-ops) de Santi Fer, diseñada específicamente para **Agencias de Reclutamiento y Mentores**.

## 🌟 ¿Qué es esta versión?
Mientras que el proyecto original es una herramienta "Single-Player" excepcional para la búsqueda de empleo individual, la **Academy Edition** transforma el motor de IA en una plataforma **Multi-Tenant**. 

Esto permite que un solo mentor gestione a múltiples alumnos de forma aislada, garantizando que la IA "conozca" el perfil de cada estudiante (Stack, Salario, Inglés) sin mezclar contextos.

## 🧠 Arquitectura de "Skills Hermanas"
Hemos implementado una arquitectura de habilidades dual que separa la estrategia de carrera de la gestión de alumnos:

1.  **/career-ops**: Modo personal. Utiliza los datos globales de config/profile.yml.
2.  **/career-ops-agency**: Modo Academia. Inyecta dinámicamente el contexto de cada alumno ubicado en data/alumnos/{{nombre}}.

Esta separación garantiza **Zero Data Leakage** y permite escalar el servicio de mentoría de forma masiva.

## 🛠️ Nuevas Capacidades
- **Modo Agency-Hunter**: Un agente autónomo que escanea, filtra y califica ofertas basándose estrictamente en el workbook.md de cada alumno.
- **Aislamiento de Contexto**: Cada alumno tiene su propia carpeta con su CV y sus filtros de éxito inamovibles.
- **Reportes de Afinidad**: Generación automática de ecomendaciones.md con porcentajes de match justificados técnicamente.

## 📥 Instalación y Setup

Para comenzar a gestionar tu academia de talentos, sigue estos pasos:

### 1. Clonar e Instalar
`ash
git clone https://github.com/FACUTUCCI10/career-ops-Agency-.git
cd career-ops-Agency-
npm install
npx playwright install chromium
`

### 2. Configuración Inicial
Copia la plantilla de perfil base (para el mentor):
`ash
cp config/profile.example.yml config/profile.yml
`

### 3. Añadir Alumnos
Crea una carpeta por alumno en data/alumnos/ siguiendo esta estructura:
`	ext
data/alumnos/nombre_del_alumno/
├── cv.md         # Experiencia profesional en Markdown
└── workbook.md   # Filtros: Salario mínimo, stack técnico, nivel de inglés
`

### 4. Ejecutar el Modo Agencia
Usa tu CLI compatible con Skills (Claude Code / Gemini CLI) y ejecuta:
`ash
/career-ops-agency agency-hunter all
`

---

## 📄 Documentación Oficial
Para un desglose técnico profundo de los cambios arquitectónicos, consulta el archivo:
docs/ACADEMY_EDITION.md

---
*Basado en el trabajo original de [santifer/career-ops](https://github.com/santifer/career-ops). Evolucionado por Facundo Tucci para la gestión de mentorías a escala.*