# PowerShell
##  Guia téorica y práctica sobre PowerShell
### Sistemas Operativos

Última revisión Agosto 2023


## Contenido
  - [Introducción a Scripting con PowerShell](#introducción)
    - [Variables](#variables)
    - [Uso de comillas](#uso-de-comillas)
    - [Comentarios](#comentarios)
    - [Condiciones y ciclos](#condiciones-y-ciclos)
    - [Funciones](#funciones)
    - [Parámetros](#parámetros)
    - [Uso de la ayuda](#uso-de-la-ayuda)
    - [Manejo de errores](#manejor-de-errores)
  - [Ejecución en segundo plano](#ejecucion-segundo-plano)

## Introducción
PowerShell (originalmente llamada Windows PowerShell) es una interfaz de consola (CLI) con posibilidad de escritura y unión de comandos por medio de instrucciones (scripts en inglés). Esta interfaz de consola está diseñada para su uso por parte de administradores de sistemas con el propósito de automatizar tareas o realizarlas de forma más controlada. Originalmente denominada como MONAD en 2003, su nombre oficial cambió al actual cuando fue lanzada al público el 25 de abril de 2006. El 15 de agosto de 2016, Microsoft publicó el código fuente de PowerShell en GitHub y cambió su nombre a PowerShell Core. La versión 6 se ofrece con licencia MIT.

Durante la cursada de la materia, utilizaremos PowerShell 7. Para verificar la versión de PowerShell instalada, ejecutar en la consola el siguiente comando:
```powershell
pwsh --version
```

Si la versión instalada no es 7.x.x o superior, se puede instalar siguiendo los pasos de la documentación oficial: [Instalación de PowerShell en Linux](https://learn.microsoft.com/es-es/powershell/scripting/install/installing-powershell-on-linux?view=powershell-7.3).

## Variables
PowerShell soporta el uso de variables en scripts. Todos los nombres de variables empiezan con el signo $, pueden ser dinámicas o de un tipo específico y no son case sensitive (al igual que todos los comandos).

Las variables pertenecen al ámbito en donde fueron declaradas. Esto quiere decir que si se declara una variable dentro de una función, sólo será accesible dentro de ella.

Las siguientes son variables reservadas por PowerShell:

| Variable        | Descricpión                                                                      |
|-----------------|----------------------------------------------------------------------------------|
| $_              | Objeto proveniente del pipeline                                                  |
| $?              | Estado del último comando ejecutado: si fue exitoso devuelve $true, si no $false |
| $true           | Constante para el valor booleano true                                            |
| $false          | Constante para el valor booleano false                                           |
| $null           | Constante para el valor null                                                     |
| $pwd            | Ruta o path actual                                                               |
| $error          | Array con los últimos errores, siendo $error\[0] el más reciente                  |
| $PsVersionTable | Versión de PowerShell                                                            |


## Uso de comillas
Es importante señalar que al momento de trabajar con texto, el uso de las comillas varía dependiendo del comportamiento que se espera. Existen 2 tipos de comillas a utilizar:
- Apostrofos (comillas simples, ''):también denominadas comillas fuertes, generan que el texto delimitado entre ellas se utilice de forma literal, lo que evita que se interpreten las variables.
- Comillas (comillas dobles, ""):  también denominadas comillas débiles, permiten utilizar texto e interpretar variables al mismo tiempo.
```powershell
$variable = 2023
Write-Output "El año es $variable" # Salida esperada: El año es 2023
Write-Output 'El año es $variable' # Salida esperada: El año es $variable
```

## Comentarios
Para la utilización de comentarios dentro del código tenemos 2 opciones, los comentarios de línea o los de múltiples líneas.

```powershell
# Este es un comentario de una sola línea. Tiene un numeral (#) al inicio de la línea.

<# Este es
un comentario multilínea,
se encierra entre <# y #>
#>
```

## Condiciones y ciclos

## Funciones

## Parámetros

## Uso de la ayuda

# Manejo de errores

# Ejecución en segundo plano
