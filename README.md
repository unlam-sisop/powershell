# PowerShell
##  Guia téorica y práctica sobre PowerShell
### Sistemas Operativos

Última revisión Agosto 2023


## Contenido
  - [Introducción a Scripting con PowerShell](#introducción-a-scripting-con-powershell)
    - [Variables](#variables)
    - [Uso de comillas](#uso-de-comillas)
    - [Comentarios](#comentarios)
    - [Condiciones y ciclos](#condiciones-y-ciclos)
    - [Funciones](#funciones)
    - [Parámetros](#parámetros)
    - [Uso de la ayuda](#uso-de-la-ayuda)
    - [Manejo de errores](#manejor-de-errores)
  - [Ejecución en segundo plano](#ejecucion-segundo-plano)

## Introducción a Scripting con PowerShell
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
El siguiente ejemplo muestra la sintaxis de las estructuras de condiciones y ciclos:

```powershell
# Condiciones
if ($variable -eq 'valor')
{ ... }
elseif ($variable -lt 'valor')
{ ... }
else
{ ... }

# Ciclo for
for ($i = 0; $i -lt 10; $i++)
{ ... }

# Ciclo foreach
foreach ($item in $array)
{ ... }

# Ciclo while
while ($variable -lt 5)
{ ... }

# Ciclo do while
do
{ ... } while ($variable -lt 10)
```

## Parámetros
A pesar de que se puede acceder a los parámetros del script con la variable $Args, también es posible accederlos al igual que hacemos en Shell scripting con $1, $2, etc. y adicionalmente, podemos declarar parámetros con nombre y asignarle un tipo de datos y validaciones. En el siguiente ejemplo se declaran dos parámetros con nombre. El primero, llamado *name*, es obligatorio ```(Mandatory=$True)```, de tipo String y si no se lo pasa por nombre, será el primer parámetro sin nombre ```(Position=1)```. El segundo parámetro, *firstName*, no es obligatorio y al no tener especificada una posición, si no se lo pasa por nombre no tendrá valor.

```powershell
Param(
 [Parameter(Mandatory=$True, Position=1)] [string] $name,
 [Parameter(Mandatory=$False)] $firstName
)
Write-Output "Hola $name $firstName"
```

Se ejecuta de la siguiente manera:

```powershell
# Los dos parámetros se pasan por nombre.
.\script.ps1 -name 'Nombre' -firstName 'Apellido'
# Un parámetro es pasado por nombre y el otro por posición.
.\script.ps1 -firstName 'Apellido' 'Nombre'
# No se puede ejecutar de esta manera, ya que dará error porque no se especificó el parámetro "name" que era obligatorio.
.\script.ps1 -firstName 'Apellido'
```

Salida:

```powershell
Hola Nombre Apellido
```

### Opciones de parámetros
La definición de parámetros en PowerShell nos permite especificar una gran variedad de opciones y validaciones, que el Engine se encargará de resolver automáticamente al recibir los valores en los parámetros. Además de las opciones ya vistas, se pueden añadir validaciones a los parámetros, ya sea de tipo, rango, largo, vacío o null, patrones (con expresiones regulares), entre otras.

```powershell
Param
(
 [parameter(Mandatory = $true)]
 [ValidateNotNullOrEmpty()]
 [ValidateLength(5, 8)] # De 5 a 8 caracteres de largo
 [ValidatePattern('^.*@.*\.[a-z]{3}$')]
 [string]$cadena,
 [parameter(Mandatory = $true)]
 [ValidateNotNullOrEmpty()]
 [ValidateRange(1, 10)]
 [int]$entero
)
```

## Funciones
En los scripts de PowerShell se pueden definir funciones para facilitar el armado de scripts. El siguiente es un script que utiliza una función de manera recursiva para calcular el factorial de un número.

```powershell
function Get-Factorial()
{
  Param ([int]$value)
  if ($value -lt 0) {
    $result = 0
  }
  elseif($value -le 1)
  {
    $result = 1
  }
  else
  {
    $result = $value * (Get-Factorial($value - 1))
  }
  return $result
}
$value = Read-Host 'Ingrese un número'
$result = Get-Factorial $value # $result = Get-Factorial -value $value
Write-Output "$value! = $result"
```
El uso de funciones, nos permite crear funcionalidades similares a los cmdlets y por tal motivo, se brinda la directiva ```CmdLetBinding```, que transforma nuestras funciones en una especie de CmdLets proporcionando los parámetros comunes como -Verbose o -Debug.

De la función anterior obtenemos el siguiente comportamiento:
```powershell
Get-Factorial -value
```

Pero al aplicar la directiva ```[CmdLetBinding()]``` el comportamiento cambia a:
```powershell
Get-Factorial -value [-Verbose] [-Debug] [-ErrorAction] [-WarningAction] [-ErrorVariable] [-WarningVariable] [-OutVariable] [-OutBuffer]
# Los parámetros entre corchetes son opcionales
```

### Estructura de las funciones
Ya hemos visto la definición de los parámetros y ahora veremos las secciones que se pueden definir dentro de las funciones:

```powershell
Begin { <# Code #> }
Process { <# Code #> }
End { <# Code #> }
```

Esta estructura es similar a la de AWK, donde tenemos el bloque Begin, que se ejecutará por única vez al comienzo de la invocación y el bloque End que se ejecutará por única vez, al finalizar la ejecución. En cambio, el bloque Process veremos que se puede ejecutar más de una vez si utilizamos la función en un pipeline de ejecución. En el siguiente ejemplo veremos la iteración del bloque Process:

```powershell
function Get-Multiplicacion()
{
 [CmdletBinding()]
 param(
   [Parameter(ValueFromPipeline=$true,
   ValueFromPipelineByPropertyName=$true)]
   [int]$value
 )
 Begin { $result = 0 }
 Process {
   Write-Output "Resultado parcial: $result"
   if ($value -lt 0)
   {
     $result = 0
   }
   elseif ($value -le 1)
   {
     $result = 1
   }
   else
   {
     $result = $value * $result
   }
 }
 End { Write-Output "Resultado Final: $result" }
}

<#
Salida:
PS C:\Users\usuario> 1..8 | Get-Multiplicacion
Resultado parcial: 0
Resultado parcial: 1
Resultado parcial: 2
Resultado parcial: 6
Resultado parcial: 24
Resultado parcial: 120
Resultado parcial: 720
Resultado parcial: 5040
Resultado parcial: 40320
#>
```

## Uso de la ayuda
Para obtener ayuda sobre los cmdlet existe el Get-Help, que nos brindará la información funcional, sobre los parámetros, el modo de uso, entre otros temas. PowerShell genera de forma automática, una hoja de ayuda con las funciones que creamos, con la información básica. Por ejemplo, de la función anterior Get-Factorial, obtenemos lo siguiente:
*imagen*

Esto nos brinda una ayuda estándar para todos los cmdlet y funciones de usuario que se desarrollan. A continuación, veremos cómo explotar un poco más la utilización de la ayuda.

La recopilación de la información de ayuda de la función se realiza mediante metadata que se debe especificar inmediatamente antes de la palabra reservada function, sin dejar espacios ni saltos de línea, ya que de lo contrario no se identificará como parte de la ayuda sino como un comentario del script. Dentro de la especificación existen varias secciones que son las que luego se mostraran con el Get-Help. Las mismas son:

```powershell
.Synopsis
.Description
.Example (puede ser más de 1)
.Inputs
.Outputs
```

A continuación se muestra un ejemplo básico de estas secciones:

```powershell
<#
.Synopsis
 Short description
.DESCRIPTION
 Long description
.EXAMPLE
 Example of how to use this cmdlet
.EXAMPLE
 Another example of how to use this cmdlet
.INPUTS
 Inputs to this cmdlet (if any)
.OUTPUTS
 Output from this cmdlet (if any)
.NOTES
 General notes
.COMPONENT
 The component this cmdlet belongs to
.ROLE
 The role this cmdlet belongs to
.FUNCTIONALITY
 The functionality that best describes this cmdlet
#>
function Verb-Noun {
  <# CODE #>
}
```

Agregando la sección de ayuda a la función Get-Factorial obtenemos el siguiente resultado:

```powershell
<#
  .Synopsis
  Esta función calcula el factorial de un número de manera recursiva
  .Description
  Esta función calcula el factorial de un número de manera recursiva
  .Parameter value
  Este parámetro indicará cuál el número al cuál calcular el factorial
  .Example
  Get-Factorial -value 5
  120
#>
function Get-Factorial()
{
```
*imagen*

Note que para obtener diferentes grados de información podemos utilizar los parámetros –Example, –Detailed o –Full, los cuales irán mostrando un mayor detalle de la ayuda informada en la metadata de la función.

## Manejo de errores
Para el manejo de errores, tendremos la opción de capturarlos y darles un tratamiento adecuado, al igual que podemos hacer en lenguajes como .NET o Java, entre otros, y adicionalmente, definir un comportamiento por defecto ante la aparición de alguna excepción. La sintaxis es la siguiente:

```powershell
try {
 <# Código con posibilidad de error #>
}
catch {
 <# Acciones a tomar ante la excepción #>
}
finally {
 <# Acciones que siempre se ejecutarán #>
}
```

Al incorporar la directiva CmdLetBinding, que ya hemos mencionado anteriormente, se nos habilitan los siguientes argumentos a las funciones: ErrorAction y ErrorVarible.
* **ErrorAction (EA)**: es una enumeración que nos permite setear el comportamiento ante una excepción en la ejecución del CmdLet o la función. Si queremos que el error encontrado sea capturado, entonces se debe setear el argumento con "Stop".
* **ErrorVariable (EV)**, nos permite crear una variable donde se almacenaran los errores obtenidos para inspeccionarlos y poder analizar las causas y determinar las acciones a tomar.

Adicionalmente, a estos argumentos, es importante resaltar la existencia y diferencia en el uso de los cmdLets para la escritura de información:
* **Write-Error**: Muestra el mensaje especificado y finaliza la ejecución de la función.
* **Write-Warning**: Muestra el mensaje en un color diferente al normal a la consola, con la leyenda "WARNING" o "ADVERTENCIA", dependiendo del lenguaje.
* **Write-Output**: Muestra el mensaje especificado en el estándar output.
* **Write-Debug**: Muestra el mensaje en un color diferente al normal con la leyenda "Debug", solamente si la variable -Debug ha sido seteada en la ejecución del CmdLet.

```powershell
function Detener-Proceso()
{
 [CmdLetBinding()]
 Param(
   [Parameter(Mandatory=$True,
   ValueFromPipeline=$true,
   ValueFromPipelineByPropertyName=$true)]
   [Int[]]$Id
 )
 Process{
   foreach ($proc in $Id)
   {
     try {
       kill -Id $proc
       Write-Output "El proceso $proc fue detenido exitosamente"
     }
     catch {
       Write-Warning "El proceso $proc no existe en el sistema"
     }
   }
 }
}
```

Ejemplos de uso:

1. Ejecutar la función con IDs inexistentes
```powershell
PS> Detener-Proceso -Id 3324, 3325 -ErrorVarialbe errorKill -ErrorAction Stop

# Salida
ADVERTENCIA: El proceso 3324 no existe en el sistema
ADVERTENCIA: El proceso 3325 no existe en el sistema
```

2. Uso del pipeline
Verificamos que existan procesos:
*imagen*

Ejecución de la instrucción:
*imagen*

3. Ejecución de la función para IDs existentes e inexistentes
*imagen*

## Ejecución en segundo plano
*En desarrollo*
