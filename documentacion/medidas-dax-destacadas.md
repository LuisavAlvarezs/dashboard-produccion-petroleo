# Medidas DAX destacadas

Esta sección presenta algunas de las medidas desarrolladas para el dashboard
de producción y operatividad.

Las fórmulas fueron seleccionadas para demostrar el uso de contexto de filtro,
acumulados históricos, clasificación de estados operacionales y comparación
contra objetivos.

> **Nota de confidencialidad:** los nombres y datos utilizados en la versión
> pública del proyecto fueron modificados o anonimizados.

---

## 1. Horas operativas

Calcula la cantidad de registros clasificados con el estado operativo `ON`.
`COALESCE` permite mostrar cero cuando no existen registros en el contexto
seleccionado.

```DAX
Horas ON =
COALESCE(
    CALCULATE(
        COUNTROWS(Consolidado_CertificLiquidos),
        Consolidado_CertificLiquidos[Valor] = "ON"
    ),
    0
)
```

**Aplicación:** seguimiento del tiempo efectivo de operación por fecha,
periodo y planta.

---

## 2. Horas de parada

Suma las horas correspondientes a paradas operacionales y administrativas.

```DAX
Horas SB =
[Horas SBA] + [Horas SBP]
```

**Aplicación:** análisis del tiempo total durante el cual una planta estuvo
fuera de operación.

---

## 3. Operatividad porcentual

Calcula la proporción de horas operativas respecto del total de horas
registradas.

```DAX
Operatividad % =
DIVIDE(
    [Horas ON],
    [Horas ON] + [Horas SB],
    0
)
```

**Formato:** porcentaje.

**Aplicación:** comparación del desempeño operacional con el objetivo
establecido del 95 %.

---

## 4. Producción bruta

Calcula el volumen total de líquidos producido mediante la suma de la
producción de petróleo y agua.

```DAX
Producción Bruta =
SUM(Consolidado_Produccion[ProduccionPetroleo])
    + SUM(Consolidado_Produccion[ProduccionAgua])
```

**Formato:** volumen en metros cúbicos.

**Aplicación:** evaluación del volumen total procesado por planta y periodo.

---

## 5. Producción acumulada hasta la fecha

Calcula la producción histórica desde el inicio de los registros hasta la
última fecha visible en el contexto seleccionado.

```DAX
Producción Acumulada =
VAR FechaLimite =
    MAX(Calendario[Date])

RETURN
    CALCULATE(
        SUM(Consolidado_Produccion[ProduccionPetroleo]),
        FILTER(
            ALL(Calendario[Date]),
            Calendario[Date] <= FechaLimite
        )
    )
```

**Aplicación:** permite consultar la producción acumulada hasta una fecha
determinada manteniendo los filtros de planta.

---

## 6. Producción del último día disponible

Identifica la última fecha con información dentro del contexto seleccionado
y devuelve la producción correspondiente a ese día.

```DAX
Producción Último Día =
VAR UltimaFechaVisible =
    MAX(Calendario[Date])

RETURN
    CALCULATE(
        SUM(Consolidado_Produccion[ProduccionPetroleo]),
        Calendario[Date] = UltimaFechaVisible
    )
```

**Aplicación:** muestra automáticamente el resultado del último día reportado
o del último día incluido en la selección.

---

## 7. Variación respecto del objetivo

Calcula la desviación porcentual entre la producción real y el objetivo
establecido para cada planta.

```DAX
Variación vs. Objetivo % =
DIVIDE(
    [Producción Último Día] - [Objetivo Producción],
    [Objetivo Producción],
    0
)
```

**Interpretación:**

- Un resultado positivo indica producción superior al objetivo.
- Un resultado negativo indica producción inferior al objetivo.
- Un resultado igual a cero indica cumplimiento exacto.

---

## Competencias demostradas

Estas medidas permiten demostrar conocimientos en:

- Creación de medidas con DAX.
- Manejo del contexto de filtro.
- Uso de variables.
- Cálculos acumulados.
- Evaluación de última fecha disponible.
- Uso de `CALCULATE`, `FILTER`, `ALL`, `DIVIDE` y `COALESCE`.
- Construcción de indicadores operacionales.
