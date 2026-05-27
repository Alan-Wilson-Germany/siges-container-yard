# SIGES: Sistema de Gestión de Ingreso, Salida y Stacking de Contenedores

Rediseño del sistema operativo de control de patio para **Logística Honu Matua**, empresa de almacenamiento y custodia de contenedores marítimos ubicada en Placilla, Valparaíso.

Desarrollado en el curso *Taller de Gestión de Operaciones* — Ingeniería Civil Industrial, Universidad de Concepción (2025-2).

---

## Problema

Honu Matua operaba con un sistema completamente manual:

- **Registro en Excel lento y propenso a errores** — digitación libre sin validaciones, provocaba filas y tiempos de espera excesivos en gate in/out.
- **Stacking sin criterio** — los contenedores se ubicaban "al ojo", sin sistema claro, dificultando el retiro y generando remanejos innecesarios.
- **Sin métricas operativas** — sin posibilidad de analizar rotación, permanencia ni eficiencia. Las decisiones se tomaban por intuición.
- **Sin trazabilidad** — una planilla única mezclaba ingresos, salidas y vigentes, generando inconsistencias frecuentes entre la planilla y la realidad del patio.

---

## Objetivo

Rediseñar el sistema de control de ingreso, salida y stacking del patio de almacenamiento, transformando la operación manual en un modelo guiado por datos, con trazabilidad en tiempo real y reglas de ubicación basadas en el indicador TGO.

---

## Solución desarrollada

### Solución 1 — Semi-automatización del registro

- Formulario de ingreso con **listas desplegables** vinculadas a base de clientes y transportistas
- Formulario de salida con **vínculo automático** al registro de ingreso correspondiente
- **Generación automática de voucher** con datos del cliente, conductor, contenedor, ubicación y valor

### Solución 2 — Base de datos estructurada

Se reemplazó la planilla única por tres tablas independientes y vinculadas:

| Tabla | Función |
|-------|---------|
| **INGRESOS** | Registra entradas con datos limpios y validados |
| **SALIDAS** | Registra retiros, vinculados automáticamente a su ingreso |
| **VIGENTES** | Se actualiza automáticamente y muestra el estado real del patio |

### Solución 3 — Reglas de stacking basadas en TGO

**¿Qué es el TGO (Time Gate Out)?**

Indicador que estima cuántos días permanecerá un contenedor en el patio, basado en el historial real de cada empresa cliente:

```
TGO_j = Moda(X_j)
```

Donde X_j es el conjunto de días de permanencia histórica de los contenedores de la empresa j. Si la empresa no tiene historial, se usa la moda global de todos los registros.

**TGO Vigente** — días restantes estimados, se actualiza automáticamente:

```
TGO_vigente(i) = TGO_j(i) - (t_actual - t_ingreso(i))
```

**Reglas de apilamiento:**
- Un contenedor entrante solo puede apilarse sobre otro si su TGO es **menor** al TGO vigente del contenedor base
- Para nivel 1 (suelo), se priorizan contenedores con TGO **mayor** al TGO global

> *"Suelo para larga estadía, altura para alta rotación"*

---

## Resultados

| Métrica | Resultado |
|---------|-----------|
| Reducción tiempo de ingreso (gate in) | **−50%** |
| Reducción tiempo de salida (gate out) | **−58%** |
| Reducción tiempo de emisión de voucher | **−80%** |
| Búsquedas manuales de contenedores | Eliminadas |
| Errores por digitación libre | Eliminados (listas desplegables + validaciones) |
| Trazabilidad entre ingresos, salidas y vigentes | Automática en tiempo real |

---

## Herramientas

- **Microsoft Excel** con macros VBA (`.xlsm`)
- Listas desplegables dinámicas y validación de datos
- Fórmulas para cálculo automático de TGO vigente, días de permanencia y valor final con IVA
- Mapa visual del patio con bahías, zonas, lados y alturas

---

## Estructura del repositorio

```
siges-container-yard/
│
├── PLANILLA_TGO.xlsm          # Sistema completo: ingresos, salidas, vigentes, mapa de patio
├── Presentación_TGO.pdf       # Presentación del proyecto
└── README.md
```

---

## Cómo usar la planilla

1. Abrir `PLANILLA_TGO.xlsm` en Microsoft Excel (habilitar macros)
2. **Hoja INGRESOS**: completar formulario de ingreso — los campos con lista desplegable se autocompletarán
3. Click en **"Agregar Ingreso"** — el registro se añade a la base de datos y se actualiza VIGENTES
4. **Hoja SALIDAS**: ingresar N° de contenedor — los datos del ingreso se vinculan automáticamente
5. Click en **"Agregar Salida"** — el contenedor sale de VIGENTES y se genera el voucher
6. **Hoja VIGENTES**: muestra el estado real del patio con TGO vigente de cada contenedor
7. **Plano del patio**: visualización por bahía, zona, lado y altura con TGO vigente por posición

---

## Autores

Proyecto grupal — Grupo 3:
- **Alan Wilson Germany** — diseño de la planilla Excel (formularios, TGO, mapa de patio y automatizaciones)
- Juan José Altamirano Paredes - Estudio de literatura y armado de base de datos
- Sebastián Leal Sporman - Análsis de datos y estudio de literatura

Universidad de Concepción · Ingeniería Civil Industrial · 2025
