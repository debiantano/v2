---
layout: post
title: Powershell - Scripting
subtitle: "Powershell - Scripting"
tags: [powershell,windows]
---

**-nop**: Abreviatura de `noProfile`, permite que Powershell no ejecute scripts de perfil e inmediatamente inicie su script en un entorno intacto  
**-exec bypass**: si la ejecución del script no está permitida, asegúrese de permitirla explicitamente para ejecutar nuestro script de powershell  
**-c**: comando para ejecutar desde Powershell

## ForEach

```
$bosque = @("Aliso", "Frenco", "Abedul", "Cedro", "Castaño", "Olmo")

foreach($arbol in $bosque){
    "$arbol = " + $arbol.length
}
```

```
PS /home/noroot> ./for_each.ps1
Aliso = 5
Frenco = 6
Abedul = 6
Cedro = 5
Castaño = 7
Olmo = 4
```

----

```
foreach($archivo in get-ChildItem *.txt){
    echo $archivo.name
}
```

```
PS /home/noroot> ./file.ps1
admin.txt
driver.txt
test.txt
```

-----

## Parámetros

```
param(
        [Parameter()]
        [string]$Parameter1,

        [Parameter()]
        [string]$Parameter2
)

Write-Host "El valor de parámetro 1 es $Parameter1"
Write-Host "El valor de parámetro 2 es $Parameter2"
```

```
PS /home/noroot> ./parameter.ps1 -Parameter1 "VALOR1" -Parameter2 "VALOR2"
El valor de parámetro 1 es VALOR1
El valor de parámetro 2 es VALOR2
```

Parámetros posicionales

```
PS /home/noroot> ./parameter.ps1 "VALOR1" "VALOR2"
El valor de parámetro 1 es VALOR1
El valor de parámetro 2 es VALOR2
```

----

## Entradas

```
Write-Host "Bienvenido a PowerShell" -ForegroundColor Green

$name = Read-Host -Prompt "Ingrese su nombre"
$age = Read-Host -Prompt "Ingrese su edad"

Write-Host "El nombre ingresado es" $name -ForegroundColor Green
Write-Host "La edad ingresada es" $age -ForegroundColor Green
```

```
PS /home/noroot> ./input.ps1
Bienvenido a PowerShell
Ingrese su nombre: test
Ingrese su edad: 123
El nombre ingresado es test
La edad ingresada es 123
```

