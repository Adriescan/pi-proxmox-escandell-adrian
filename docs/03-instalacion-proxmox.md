# 03-instalacion-proxmox

## Objetivo
Configurar la maquina virtual de Proxmox e instalar Proxmox en ella

## Procedimiento y Evidencias
### Comprobar anfitrion
El proceso de investigacion de la maquina anfitrion ya lo realice en el ejercicio anterior de manera bastante detallada. Para acceder a el puede usar este enlace: [Inventario](01-inventario-hardware.md)

### Crear VM exterior
Primero crearemos la maquina virtual de Proxmox, insertando los parametros en la maquina que ya indicamos en la practica anterior de [inventariado](01-inventario-hardware.md).

- Nombre maquina virtual, Info del S.O y ISO utilizada
![ajustes-vm-1](../evidencias/capturas-0-salidas/03-instalacion-proxmox/01-Ajustes-maquina.png)

- Asignacion de RAM y vCPU
![ajustes-vm-2](../evidencias/capturas-0-salidas/03-instalacion-proxmox/02-Ajustes-maquina.png)
Justificacion mayor tamaño vCPU: en mi caso asigno mas de dos vCPU ya que, el equipo que voy a utilizar (indicado en la practica anterior de [inventariado](01-inventario-hardware.md)); cuenta con suficientes recursos como para ampliar un poco mas la cantidad de vCPU

- Creacion de disco virtual
![ajustes-vm-3](../evidencias/capturas-0-salidas/03-instalacion-proxmox/03-Ajustes-maquina.png)

- Luego de crear la maquina virtual, asignaremos el adaptador NAT a la maquina
![NAT-vm](../evidencias/capturas-0-salidas/03-instalacion-proxmox/04-Asignacion-NAT-maquina.png)

### Comprobar virtualizacion anidada


### Red local



## Comandos usados

```bash

```

## Incidencias

## Conclusion

[Volver al indice](../README.md)
