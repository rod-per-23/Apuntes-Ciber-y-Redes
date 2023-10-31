Volver a [README](../../README.md)
# Utilizando Herramientas de Whois en Kali Linux

## Obtención de IP según un Domain Name

Primero se usa el comando `host wikipedia.org` para obtener la IP de la página de Wikipedia.

![Pasted image 20231031184913.png](./Im%C3%A1genes/Pasted%20image%2020231031184913.png)

Con esto, podemos ver la IPv4 y la IPv6 del dominio.
## whois

Teniendo la IP, se utiliza el comando `whois 208.80.153.224` para obtener más datos, como por ejemplo, el rango de direcciones a la que pertenece la IP y la organización que está a cargo.

![Pasted image 20231031185025.png](./Im%C3%A1genes/Pasted%20image%2020231031185025.png) 


# theHarvester

Esta herramienta viene instalada por defecto en Kali, sólo hay que escribir el comando `theHarvester` para ver si es que funciona.
Por ejemplo, con el comando `theHarverster -h` obtenemos una ventana de ayuda que nos indica qué cosas se pueden hacer.

## Utilización

Por ejemplo, se usan las opciones `-d` para el dominio, `-l` para limitar la cantidad de cosas que buscamos, `-b` para el source de la información.
Para los sources, si se pone `-b all`, busca en todas las fuentes, aunque para algunas, hay que tener API keys.
Se usa el comando `theHarvester -d wikipedia.org -b all -f harvestWiki` para buscar en todos los sources, sin límite, y guardar lo encontrado en archivos .json y .xml de nombre `harvestWiki` con su respectiva extensión.

En la imagen de abajo se muestra por ejemplo IPs (v4 y v6) asociadas al dominio de Wikipedia.

![Pasted image 20231031185622.png](./Im%C3%A1genes/Pasted%20image%2020231031185622.png)



# recon-ng

Simplemente se utiliza el comando `recon-ng` para entrar a la consola.
Dentro de la consola, se escribe `help` para ver qué se puede hacer.

## Crear ambiente de trabajo

Esto es como los ambientes de anaconda en python, para que no se mezclen paquetes y dependencias que pueden no ser compatibles unas con otras.
Para crear un nuevo ambiente, simplemente se escribe `workspaces create NOMBREespacio`.

Para ver cuales espacios de trabajos hay, se usa el comando `workspaces list`

![Pasted image 20231031185753.png](./Im%C3%A1genes/Pasted%20image%2020231031185753.png)

Para entrar a un espacio de trabajo cuando se está **fuera** de la consola recon-ng se usa el comando `recon-ng -w NOMBREespacio`

## Marketplace e instalar módulos

El marketplace es donde podemos instalar módulos para el recon-ng.

Usando el comando `marketplace help` podemos observer todas las opciones para el marketplace.

Para buscar todos los módulos, se utliza el comando `marketplace search`

Para instalar un módulo se usa el comando `marketplace install NOMBRE`

## Cargar Módulos

Para cargar un módulo, se usa el comando `modules load NOMBREcompleto`.

![Pasted image 20231031185909.png](./Im%C3%A1genes/Pasted%20image%2020231031185909.png)

Utilizando el comando `info` dentro del módulo podemos ver más información

Para seleccionar un dominio que queramos analizar, utilizamos el comando `options set SOURCE NOMBREdominio`
Para este módulo, es importante dejar en True al menos uno de las opciones del info. Por ejemplo, para activar el primero se usa `options set GHBG_ADVISORIES_AND_VULNERABILITIES True`

También podemos usar el comando `show options` para ver las opciones que hay.

![Pasted image 20231031185926.png](./Im%C3%A1genes/Pasted%20image%2020231031185926.png)

## Ejecutar el módulo


Simplemente se utiliza el comando `run`
Para el caso del ghdb, llega un momento en el que se activan los Google Captcha y no deja seguir avanzando, en ese caso, simplemente detener todo con Control + C.

Para ver la lista de vulnerabilidades, usar `show vulnerabilities`.
También se puede ver un resumen con `dashboard`

## Recon Web

Primero, hay que salir de la terminal de recon-ng con `exit`
Estando fuera, se puede usar el comando `recon-web -h` para ver la ayuda de recon-web

Para iniciar recon-web, usamos el comando `recon-web --host localhost`.
Por defecto se abre el puerto 5000.

Para visualizar esto, se abre un navegador web, y en la barra de URL se escribe `localhost:5000`.


# Escaneo con WireShark

Para escanear tramas con WireShark hay que iniciar el escaneo y hacer algo que involucre el envío de tramas del tipo que quiero analizar, por ejemplo, si quiero ver tramas ICMP con dicho filtro activado, obviamente tengo que hacer un ping a alguna IP X para poder ver lo que captura el WireShark.

## Ejemplo ICMP

Se hace un ping a la dirección `8.8.8.8` y se utiliza WireShark para analizar alguna trama, por ejemplo, la mostrada a continuación:

![Pasted image 20231031191132.png](./Im%C3%A1genes/Pasted%20image%2020231031191132.png)

![Pasted image 20231031191352.png](./Im%C3%A1genes/Pasted%20image%2020231031191352.png)

WireShark normalmente muestra los datos por capas (capas del modelo OSI o equivalente).

### Capa de enlace
Por ejemplo, a nivel de capa de enlace hay lo siguiente. Principalmente datos de las direcciones MAC de origen y destino.

![Pasted image 20231031191301.png](./Im%C3%A1genes/Pasted%20image%2020231031191301.png)

![Pasted image 20231031191306.png](./Im%C3%A1genes/Pasted%20image%2020231031191306.png)


### Capa de red

En la capa de red se observan principalmente información de las IP de origen `192.168.0.7` y destino `8.8.8.8`. Recordar que la IP de origen es una IP privada de la red interna del WiFi que tengo acá y no es necesariamente la IP "verdadera" con la que salgo a internet.

![Pasted image 20231031191606.png](./Im%C3%A1genes/Pasted%20image%2020231031191606.png)

![Pasted image 20231031191610.png](./Im%C3%A1genes/Pasted%20image%2020231031191610.png)


La última parte de la capa de red, que corresponde a lo del protocolo ICMP propiamente tal, se encuentran datos relacionados al ping, como checksum, identificadores, números de secuencia, y los mismos datos enviados.

![Pasted image 20231031191720.png](./Im%C3%A1genes/Pasted%20image%2020231031191720.png)

![Pasted image 20231031191725.png](./Im%C3%A1genes/Pasted%20image%2020231031191725.png)

## Tramas TCP

Para analizar tramas TCP, es lo mismo, pero obviamente cambiando el filtro de ICMP a TCP. Ahora hay que usar el navegador web (yo uso Firefox) y entrar a alguna página cualquiera.

- Siguiendo el modelo OSI, en la capa física (capa 1), se observan todos los datos en formato hexadecimal que son transmitidos.
- En la capa de enlaces, vemos las MAC del computador y del Router.
- En la capa de red se aprecian las direcciones IP de origen y destino en formato hexadecimal, además de información sobre la versión del protocolo IP, tamaño del Header, tamaño total, tiempo de vida, y el protocolo TCP.
- Finalmente, en la última capa, la de transmisión, vemos detalles del mismo protocolo TCP, el puerto de origen y destino, número de secuencia, número de acknowledgment, flags, checksum, entre otros.


Volver a [README](../../README.md)