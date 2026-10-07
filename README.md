# Cache Memory Simulator

Simulador de una memoria caché de **mapeo directo** desarrollado en **C**.

El programa simula el acceso de una CPU a memoria RAM a través de una caché, procesando una secuencia de direcciones de memoria y determinando para cada acceso si se produce un **cache hit** o un **cache miss**.

El objetivo del proyecto es comprender de forma práctica el funcionamiento de la jerarquía de memoria, el direccionamiento de caché y el impacto de los fallos de caché sobre el tiempo de acceso.

## Características

- Simulación de una memoria RAM de **4096 bytes**.
- Caché de **8 líneas**.
- Tamaño de línea de caché de **16 bytes**.
- Caché de **mapeo directo**.
- Procesamiento de direcciones de memoria de **12 bits**.
- Separación de cada dirección en:
  - **Tag / Etiqueta:** 5 bits.
  - **Cache line / Línea:** 3 bits.
  - **Word offset / Desplazamiento:** 4 bits.
- Detección de **cache hits** y **cache misses**.
- Carga automática de bloques desde RAM cuando ocurre un fallo de caché.
- Cálculo del número total de fallos.
- Simulación del coste temporal de los accesos.
- Volcado del estado final de la caché a fichero.

## Arquitectura simulada

La configuración utilizada es:

| Componente | Tamaño |
|---|---:|
| Memoria RAM | 4096 bytes |
| Espacio de direcciones | 12 bits |
| Líneas de caché | 8 |
| Tamaño de cada línea | 16 bytes |
| Capacidad total de caché | 128 bytes |
| Tag | 5 bits |
| Índice de línea | 3 bits |
| Offset | 4 bits |

Una dirección se divide de la siguiente forma:

```text
+-------------+-----------+-------------+
|    TAG      |   LINE    |   OFFSET    |
|   5 bits    |  3 bits   |   4 bits    |
+-------------+-----------+-------------+
```

Por ejemplo, para una dirección de memoria:

```text
XXXXXXXXXXXX
```

los primeros 5 bits identifican la **etiqueta**, los siguientes 3 seleccionan la **línea de caché** y los últimos 4 indican la posición del dato dentro del bloque de 16 bytes.

## Funcionamiento

El programa recibe los accesos a memoria desde:

```text
accesos_memoria.txt
```

y carga el contenido inicial de la RAM desde:

```text
CONTENTS_RAM.bin
```

Para cada dirección de memoria:

1. La dirección hexadecimal se convierte a su representación numérica.
2. Se divide en **tag, línea y offset**.
3. Se consulta la línea correspondiente de la caché.
4. Si la etiqueta almacenada coincide con la dirección solicitada, se produce un **cache hit**.
5. Si la etiqueta no coincide, se produce un **cache miss**.
6. En caso de fallo, se carga desde RAM el bloque correspondiente de 16 bytes.
7. Se obtiene el dato solicitado desde la caché.

## Gestión de fallos de caché

Cuando se produce un fallo:

```c
if (ETQ != cache[linea].ETQ)
```

el simulador actualiza la etiqueta y copia desde memoria RAM el bloque correspondiente:

```c
TratarFallo(cache, Simul_RAM, ETQ, linea, bloque);
```

Al tratarse de una caché de mapeo directo, cada bloque de memoria solo puede almacenarse en una línea concreta de la caché.

## Simulación temporal

El programa mantiene un contador de tiempo global.

Un acceso normal incrementa el tiempo en:

```text
1 unidad
```

mientras que un fallo de caché añade una penalización adicional de:

```text
20 unidades
```

Esto permite observar el impacto de los cache misses sobre el rendimiento del sistema.

Al finalizar la ejecución se muestran estadísticas como:

```text
Accesos totales
Número de fallos de caché
Tiempo medio de acceso
```

## Ejemplo de salida

Durante la ejecución se muestra información sobre cada acceso:

```text
T: 1, Fallo de CACHE 1, ADDR 02AF, Label 0, linea 02, palabra 0F, bloque 2A
Se ha cargado en el bloque 2A, en la linea 02.
T: 1, Acierto de CACHE, ADDR 02AF, Label 0, linea 02, palabra 0F, DATO A
```

También se muestra el estado completo de las líneas de caché después de cada acceso.

## Compilación

El proyecto puede compilarse con GCC:

```bash
gcc cache.c -o cache
```

## Ejecución

Antes de ejecutar el programa deben estar disponibles en el mismo directorio:

```text
accesos_memoria.txt
CONTENTS_RAM.bin
```

Después:

```bash
./cache
```

Al finalizar se genera:

```text
CONTENTS_CACHE.bin
```

con el estado final de la memoria caché.

## Estructuras principales

Cada línea de caché contiene una etiqueta y un bloque de datos:

```c
typedef struct
{
    unsigned char ETQ;
    unsigned char Data[TAM_LINEA + 1];
} T_CACHE_LINE;
```

La configuración principal está definida mediante:

```c
#define TAM_LINEA 16
#define NUM_LINEAS_CACHE 8
#define TAM_RAM 4096
```

## Conceptos trabajados

Este proyecto aplica conceptos de:

- Arquitectura de computadores.
- Jerarquía de memoria.
- Memoria caché.
- Mapeo directo.
- Cache hits y cache misses.
- Tags, índices y offsets.
- Manipulación de direcciones de memoria.
- Programación en C.
- Punteros.
- Gestión dinámica de memoria.
- Lectura y escritura de ficheros.
- Conversión hexadecimal, decimal y binaria.
- Estructuras de datos de bajo nivel.

## Tecnologías

- **C**
- **GCC**
- Programación de bajo nivel
- Arquitectura de computadores

## Autor

**Francisco José Jaraba Estévez**

Estudiante del Doble Grado en Ingeniería del Software y Física Computacional — U-tad.