# Bitacora de tecnicas avanzadas

Laboratorio 07: Tecnicas Avanzadas de Prompting.

Herramienta de IA usada: Gemini

## Ejercicio 2: Zero-shot, one-shot y few-shot

| Tipo | Aciertos (de 5) | Formato de la respuesta | Todas con el mismo formato (Si/No) |
|------|-----------------|-------------------------|------------------------------------|
| Zero-shot | 5 | Tabla con columnas #, comentario, sentimiento y un "tema principal" que no pedi, mas una pregunta al final | No |
| One-shot | 5 | Lista numerada "texto" -> etiqueta, con la etiqueta en negrita | Si |
| Few-shot | 5 | Solo lineas "texto" -> etiqueta, igual que los ejemplos | Si |

Las tres versiones acertaron las 5 etiquetas; la diferencia estuvo en el formato.
En zero-shot la IA eligio su propio formato y agrego informacion extra; con ejemplos copio el formato, y en few-shot lo respeto exactamente, sin numeracion ni negritas.

## Ejercicio 3: Chain of Thought

| Pedido | Respuesta de la IA | Muestra los pasos (Si/No) | Correcta (Si/No) |
|--------|--------------------|---------------------------|------------------|
| Directo | 318.6 | No | Si |
| Paso a paso | S/ 318.60 (descuento 30, precio 90, IGV 16.20, unitario 106.20, total 318.60) | Si | Si |

Aunque la respuesta directa fue correcta, solo con el numero no hay forma de saber si el razonamiento fue bueno o si acerto por casualidad.
Con los pasos pude comparar cada calculo con mi calculadora y, si hubiera un error, sabria exactamente en que paso esta; ademas, la IA comprobo el resultado por otro camino (360 x 0,75 x 1,18).

## Ejercicio 4: Role prompting

| Version | Vocabulario (sencillo/tecnico) | Usa ejemplos o codigo | A quien le sirve mas |
|---------|-------------------------------|-----------------------|----------------------|
| A. Sin rol | Intermedio: define la variable como espacio en memoria | Analogia de la caja etiquetada y un ejemplo practico con codigo | A alguien que ya tiene nociones basicas |
| B. Rol docente | Sencillo, habla a un grupo ("Imaginen...") | Ejemplo de la vida diaria: una caja de carton con la etiqueta "Edad" y el numero 25 | A estudiantes que nunca han programado |
| C. Rol senior | Tecnico: memoria RAM, fuertemente tipado, tipo de dato, identificador | Codigo Java y conceptos propios del lenguaje | A un desarrollador que ya programa |

Las tres respuestas usaron la analogia de la caja, pero cada rol cambio el vocabulario y el enfoque.
El rol no le dio conocimientos nuevos a la IA: solo cambio desde que punto de vista y para quien explico.

## Ejercicio 5: Descomposicion

**Pedido de una sola vez** ("Crea un sistema de inventario para una tienda."): la IA decidio todo por su cuenta. Genero una aplicacion web en HTML con Tailwind CSS llamada "StockMaster Pro", con soles y dolares, exportacion a CSV y guardado en localStorage. No uso Java y no se podia revisar por partes.

**Pedido por pasos (mismo chat):**

- **Paso 1 (requisitos):** listo 5 requisitos de un inventario en Java, entre ellos la gestion de productos (CRUD y categorias) y el control de entradas y salidas de stock con historial.
- **Paso 2 (diseno de clases):** separo el sistema en clases de dominio (Producto, Categoria, Proveedor) y enums para los tipos de movimiento, indicando el tipo de dato de cada atributo. Uso BigDecimal para los precios para evitar errores de redondeo.
- **Paso 3 (codigo de Producto):** escribio la clase Producto con los mismos atributos del paso 2, un constructor y sus get y set. Agrego por su cuenta un constructor vacio, un metodo requiereReabastecimiento() y un toString().
- **Paso 4 (mejoras):** propuso validar los setters para evitar stock o precios negativos, implementar equals() y hashCode() con el sku o el id, y agregar metodos para calcular el valor del inventario y el margen de ganancia.

**Comparacion:** el pedido de una sola vez produjo algo generico que no se podia revisar ni controlar. Por pasos, cada respuesta uso la anterior como contexto: el codigo de Producto respeto exactamente el diseno de clases, y pude revisar cada parte antes de seguir.

**Observacion:** la clase Producto usa Categoria y Proveedor, pero la IA nunca escribio esas clases, asi que Producto no compila sola. Hay que pedirlas en un paso adicional.

## Ejercicio 6: Prompt estructurado y autocritica

**Prompt basico** ("Dame casos de prueba para un login."): la IA respondio con una lista larga por categorias, sin tabla, e incluyo casos que no pedi como "Recordarme" o "Mostrar contrasena".

**Prompt estructurado y autocritica:**

```text
<rol>Actua como analista de pruebas de software.</rol>
<contexto>Login web con correo y contrasena. La cuenta se bloquea
despues de 3 intentos fallidos.</contexto>
<tarea>Piensa paso a paso que puede fallar y escribe 6 casos de prueba.</tarea>
<formato>Tabla con las columnas: ID, escenario, datos de entrada,
resultado esperado.</formato>

Revisa tu tabla: faltan casos limite como campos vacios, correo sin @
o contrasena con espacios? Agrega los que falten e indica cuales agregaste.
```

Con el prompt estructurado, la IA primero analizo paso a paso que podia fallar y luego entrego la tabla con 6 casos. En la autocritica amplio el CP-06 y agrego el CP-07 y el CP-08, marcando cada uno como "Ampliado" o "Nuevo".

| Que revisar | Cumple (Si/No) |
|-------------|----------------|
| Tiene las 4 columnas pedidas? | Si |
| Incluye el bloqueo despues de 3 intentos? | Si (CP-04 y CP-05) |
| Incluye casos con campos vacios? | Si (CP-06 ampliado) |
| Indica que casos agrego en la autocritica? | Si (CP-06 ampliado, CP-07 y CP-08 nuevos) |
| Hay algun caso repetido o que no tenga sentido? | No hay repetidos, pero el CP-08 asume una regla que no le di: que el sistema no debe recortar los espacios de la contrasena |

**Observacion:** la primera vez envie la autocritica en un chat nuevo por error. Sin la tabla en su ventana de contexto, la IA invento una tabla distinta con una columna extra. Al repetirla en el mismo chat, si reviso su propia tabla.