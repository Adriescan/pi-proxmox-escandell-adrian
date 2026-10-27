# 03-instalacion-proxmox

## Objetivo
Configurar la maquina virtual de Proxmox e instalar Proxmox en ella

## Procedimiento y Evidencias
### Comprobar anfitrion
El proceso de investigacion de la maquina anfitrion ya lo realice en el ejercicio anterior de manera bastante detallada. Para acceder a el puede usar este enlace: [Inventario](01-inventario-hardware.md)

### Crear VM exterior
Primero crearemos la maquina virtual de Proxmox, insertando los parametros en la maquina que ya indicamos en la practica anterior de [inventariado](01-inventario-hardware.md).

- Nombre maquina virtual, Info del S.O y ISO utilizada
![ajustes-vm-1](../evidencias/capturas-o-salidas/03-instalacion-proxmox/01-Ajustes-maquina.png)

- Asignacion de RAM y vCPU

![ajustes-vm-2](../evidencias/capturas-o-salidas/03-instalacion-proxmox/02-Ajustes-maquina.png)

Justificacion mayor tamaño vCPU: en mi caso asigno mas de dos vCPU ya que, el equipo que voy a utilizar (indicado en la practica anterior de [inventariado](01-inventario-hardware.md)); cuenta con suficientes recursos como para ampliar un poco mas la cantidad de vCPU

- Creacion de disco virtual

![ajustes-vm-3](../evidencias/capturas-o-salidas/03-instalacion-proxmox/03-Ajustes-maquina.png)

### Configuracion de interfaces de red
Tendiendo la maquina creada, configuraremos las interfaces de red de nuestra maquina Proxmox, siguiendo el esquema de red que hicimos en la anterior practica [Diagrama de red](02-diagrama-red.md). Dejaremos una interfaz en NAT y la otra la configuraremos como una interfaz Host-Only

**NAT**

![NAT-vm](../evidencias/capturas-o-salidas/03-instalacion-proxmox/04-Asignacion-NAT-maquina.png)

**Host-Only**

![Host-Only-vm](../evidencias/capturas-o-salidas/03-instalacion-proxmox/05-Configuracion-Hostonly-maquina.png)

(Aunque el adaptador tenga DHCP habilitado, en nuestra maquina le asignaremos un IP fija; que eso se realizara mas adelante)

![Host-Only-vm](../evidencias/capturas-o-salidas/03-instalacion-proxmox/06-Asignacion-Hostonly-maquina.png)

### Comprobar virtualizacion anidada
Ahora comprobaremos que podemos activar la virtualizacion anidada en nuestra maquina virtual. Esto esta indicado como Nested-VTx/AMD-V; que en mi caso si lo he podido activar
![virtanidada-vm](../evidencias/capturas-o-salidas/03-instalacion-proxmox/07-Virtualizacion-anidada-maquina.png)

Tambien deberemos tener activada otra opcion llamada "Hardware Virtualization" para poder tener un mejor rendimento de virtualizacion en nuestra maquina virtual. En mi caso, puede activar esta opcion sin problema
![virthardware-vm](../evidencias/capturas-o-salidas/03-instalacion-proxmox/08-Virtualizacion-hardware-maquina)]

### Instalacion Proxmox
Al tener ya todo configurado perfectamente, inciaremos la instalacion de proxmox. Primero indicaremos el disco donde instalaremos Proxmox (en nuestro caso, el VDI que creamos antes)
![disco-instalacion](../evidencias/capturas-o-salidas/03-instalacion-proxmox/09-Disco-instalacion-Proxmox.png)

(Entre la configuracion del disco y la red, se configuran otros parametros como la region y la contraseña que usaremos; pero no lo vi interesante como para añadir capturas. Ademas en el resumen final vemos un poco esta configuracion)

Luego configuraremos la interfaz de red tal y como indicamos en nuestro [diagrama de red](02-diagrama-red.md) del ejercicio anterior(cuando este instalado, tendremos que configurar la de host-only)
![red-instalacion](../evidencias/capturas-o-salidas/03-instalacion-proxmox/10-Red-instalacion-Proxmox.png)

Y aqui un resumen de la configuracion que se ha puesto en Proxmox:
![resumen-instalacion](../evidencias/capturas-o-salidas/03-instalacion-proxmox/11-Resumen-instalacion-Proxmox)

### Configuraciones posteriores y comprobacion de que funciona
Al terminal la instalacion de Proxmox, tendremos que configurar la interfaz Host-Only desde su archivo de configuracion, tal como indicamos en nuestro [diagrama de red](02-diagrama-red.md); para poder acceder a Proxmox desde la web (que en mi caso, la interfaz Host-Only es la nic1)
![configuracion-red](../evidencias/capturas-o-salidas/03-instalacion-proxmox/12-Configuracion-interfaz-HostOnly.png)

### Comprobacion de acceso a red
Por ultimo, comprobaremos que mediante la IP que acabamos de configurar podemos acceder a nuestro Proxmox desde la web
![acceso-web-proxmox](../evidencias/capturas-o-salidas/03-instalacion-proxmox/13-Acceso-Proxmox-Web.png)

## Comandos usados
**Configuracion de red**
```bash
nano /etc/network/interfaces/
```

## Incidencias
No llego a ocurrir ninguna incidencia durante la instalacion

[Volver al indice](../README.md)
