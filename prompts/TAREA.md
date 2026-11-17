# Tarea: Mi prompt avanzado

Herramienta de IA usada: Gemini, cada version en un chat nuevo.

## Tarea elegida

**Casos de prueba para el registro de salidas de un almacen.** El sistema registra la salida de materiales de un almacen minero: el usuario ingresa el codigo del material, la cantidad y el responsable, y el sistema descuenta la cantidad del stock. Elegi esta tarea porque continua el programa de almacen de mi tarea del Laboratorio 06 y porque un error en las salidas deja el inventario mal.

## Version 1: prompt basico

```text
Dame casos de prueba para registrar salidas de un almacen.
```

**Que paso:** la IA invento todo el contexto: uso datos genericos ("Producto A con stock = 50", "motivo de salida: Venta") y eligio sus propias columnas (precondiciones y pasos de ejecucion). No sabia que era un almacen minero ni que reglas debia validar.

## Version 2

```text
Actua como analista de pruebas de software con experiencia en sistemas de inventario. El sistema registra salidas de materiales de un almacen minero: el usuario ingresa el codigo del material, la cantidad y el responsable, y el sistema descuenta la cantidad del stock. Escribe casos de prueba en una tabla con las columnas: ID, escenario, datos de entrada y resultado esperado.
```

**Tecnica agregada:** role prompting (analista de pruebas con experiencia en inventarios), mas contexto y formato.

**Por que:** en la v1 la IA tuvo que adivinar el sistema y el formato.

**Que mejoro:** uso exactamente las 4 columnas pedidas y los campos reales (codigo, cantidad y responsable), e incluyo casos limite como sacar todo el stock o mas de lo disponible.

**Que falta:** invento sus propios datos de prueba (MAT-001 con 100 unidades, MAT-002 con 50), no conocia las reglas del sistema y entrego la tabla sin explicar como eligio los casos.

## Version 3: prompt final

```text
<rol>
Actua como analista de pruebas de software senior con experiencia en sistemas de inventario de almacen.
</rol>

<contexto>
El sistema registra salidas de materiales de un almacen minero. El usuario ingresa el codigo del material, la cantidad y el responsable, y el sistema descuenta la cantidad del stock.
Reglas: el codigo debe existir, la cantidad debe ser un numero entero mayor a cero, una salida no puede dejar el stock en negativo y el responsable es obligatorio.
Dato de prueba: el material M001 (Casco de seguridad) tiene 25 unidades en stock.
</contexto>

<tarea>
Piensa paso a paso que puede fallar en cada regla, incluyendo los casos limite, y escribe 8 casos de prueba.
</tarea>

<ejemplo>
| CP-01 | Salida valida | Codigo: M001, Cantidad: 5, Responsable: Juan Perez | Se registra la salida y el stock baja de 25 a 20 | Positivo |
</ejemplo>

<formato>
Primero escribe en una lista breve tu analisis paso a paso. Luego presenta una tabla con las columnas: ID, escenario, datos de entrada, resultado esperado y tipo (Positivo, Negativo o Limite), siguiendo el estilo del ejemplo.
</formato>

<revision>
Antes de entregar, revisa tu tabla: comprueba que cubra cada regla y los casos limite (cantidad igual al stock, cantidad mayor al stock, cero, negativos, decimales y campos vacios). Agrega los que falten e indica cuales agregaste.
</revision>
```

**Tecnicas agregadas:** prompt estructurado, chain of thought, few-shot y autocritica, ademas de las reglas del sistema y un dato de prueba fijo.

**Por que:** en la v2 la IA no conocia las reglas, inventaba los datos y no se podia saber como eligio los casos.

**Que mejoro:** primero analizo paso a paso que podia fallar en cada una de las 4 reglas; luego entrego la tabla con la columna Tipo, usando siempre M001 con 25 unidades, asi cada resultado esperado se puede comprobar (25 a 20, 25 a 0, se mantiene en 25). En la revision agrego 2 casos que faltaban (CP-09 codigo vacio y CP-10 cantidad vacia) e indico cuales eran, hasta llegar a 10 casos.

## Tecnicas usadas en el prompt final

| Tecnica | Parte del prompt final |
|:--------|:-----------------------|
| Role prompting | `<rol>` Analista de pruebas de software senior con experiencia en sistemas de inventario de almacen |
| Prompt estructurado | Las etiquetas `<rol>`, `<contexto>`, `<tarea>`, `<ejemplo>`, `<formato>` y `<revision>` separan cada parte |
| Chain of Thought | `<tarea>` "Piensa paso a paso que puede fallar en cada regla" y `<formato>` "Primero escribe tu analisis paso a paso" |
| Few-shot | `<ejemplo>` con una fila modelo de la tabla (CP-01) |
| Autocritica | `<revision>` "Revisa tu tabla, agrega los que falten e indica cuales agregaste" |

## Evaluacion del resultado

| Criterio | Version 1 | Version 3 |
|:---------|:---------:|:---------:|
| Usa el contexto de un almacen minero con codigo, cantidad y responsable | No | Si |
| Usa un dato de prueba fijo que permite verificar los resultados (M001 con 25) | No | Si |
| Muestra el analisis paso a paso antes de la tabla | No | Si |
| La tabla tiene las columnas pedidas, incluida la de Tipo | No | Si |
| Cubre los limites de cantidad: igual al stock, mayor al stock, cero, negativos y decimales | No | Si |
| Indica que casos agrego en la revision | No | Si |
| Convierte en casos de prueba todo lo que encontro en su analisis | No | No |

**Observacion:** en su analisis la IA menciono riesgos como caracteres no numericos en la cantidad ("abc"), un responsable con solo espacios en blanco o un codigo escrito en minusculas, pero no los convirtio en casos de prueba, y la autocritica tampoco los agrego. La revision de la IA no reemplaza mi propia revision.

## Por que elegi estas tecnicas

Elegi estas tecnicas porque cada una resuelve un problema concreto de los casos de prueba. El rol de analista de pruebas hace que la IA piense en errores y casos limite, no solo en el camino feliz. Chain of thought obliga a revisar regla por regla antes de escribir, y me deja comprobar si su razonamiento cubre todo. El ejemplo (few-shot) fija el formato exacto de cada fila, y el dato de prueba M001 con 25 unidades hace que cada resultado esperado sea verificable. La autocritica sirve como segunda revision para encontrar casos olvidados. No use descomposicion porque la tarea cabe en un solo pedido: dividirla en varios mensajes no aportaba nada y alargaba el proceso.