---
name: career-ops-agency
description: Multi-tenant Career-Ops extension for Agency/Mentor mode. Manage multiple students.
user_invocable: true
args: mode alumno
argument-hint: "[agency-hunter | scan | pdf | oferta] [nombre_del_alumno | all]"
---

# career-ops-agency -- Multi-Tenant Router

This skill extends the core **career-ops** functionality by adding student isolation.

## Mode Routing

| Input | Mode |
|-------|------|
| `agency-hunter` | `agency-hunter` |
| `scan` | `scan` |
| `pdf` | `pdf` |
| `oferta` | `oferta` |

## Multi-Tenant Context Injection

**CORE RULE:**
Before any execution, if an `{{alumno}}` is specified and it's not 'all':
1. **LOAD** `data/alumnos/{{alumno}}/cv.md` (as the Primary Professional Source).
2. **LOAD** `data/alumnos/{{alumno}}/workbook.md` (as the Source of Truth for filters).
3. **OVERRIDE** any global config with these student-specific metrics (Stack, Salary, English Level).

## Agency-Hunter Execution Flow

If `{{alumno}}` is 'all':
- Iterate through all directories in `data/alumnos/`.
- For each directory, launch a sub-agent with the specific student context.
- Aggregate matches into individual `recomendaciones.md` files.

## Delegated Capabilities
This skill uses the scripts from the root directory:
- `node scan.mjs`
- `node generate-pdf.mjs`
- `modes/agency-hunter.md`
