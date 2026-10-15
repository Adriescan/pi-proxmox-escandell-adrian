# Inventario investigado y viabilidad

## Contexto de la observacion

- Fecha y sistema utilizado:
    El 13/10/2026 se realizó una investigación de las características de un ordenador físico utilizando Hiren's BootCD PE. Para obtener la información se utilizaron las herramientas CPU-Z, HWiNFO y CrystalDiskInfo, con las que se analizaron el procesador, la memoria RAM, la placa base, el almacenamiento y las interfaces de red.
    
- Modalidad, equipo anfitrion y VM VirtualBox si procede: 
    Inicialmente estaba previsto instalar Proxmox VE directamente en este ordenador, utilizándolo como servidor físico. Sin embargo, finalmente se ha decidido realizar la práctica mediante virtualización, utilizando Oracle VirtualBox sobre Fedora Linux en un portátil Dell Latitude 3450.

    Por tanto, se analizará la viabilidad del ordenador investigado originalmente y también las características del equipo anfitrión que se utilizará para la instalación virtualizada.

- Version de Proxmox prevista: Utilizaremos la version 9.2 de Proxmox.

## Inventario inicial
### Equipo inicial
| Area | Dato observado y evidencia | Especificacion y fuente | Limite o diferencia | Justificacion |
|---|---|---|---|---|
| CPU | Intel Pentium G4560, 2 núcleos, 4 hilos, 3,50 GHz. [CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-inicial.JPG) | Kaby Lake, arquitectura de 64 bits, Intel VT-x, TDP 54 W.[CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-inicial.JPG) | Procesador antiguo de dos núcleos físicos | Permite instalar Proxmox, pero limita las cargas simultáneas |
| RAM | 8 GB DDR4 en doble canal. [CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Memoria-inicial.JPG) |DDR4-2400, CL17, 1200MHz. [CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Memoria2-inicial.JPG) | Capacidad limitada para varias máquinas virtuales | Se necesita reservar RAM para el hipervisor(KVM) y los invitados(VM o contenedores) |
| Almacenamiento | Toshiba DT01ACA050, 500,1 GB, estado Good. [CrystalDiskInfo](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco-inicial.JPG) | HDD SATA 600 7200RPM [CrystalDiskInfo](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco-inicial.JPG) | Disco mecánico, menor rendimiento que un SSD, sin redundancia identificada | Tiene capacidad suficiente para un laboratorio pequeño, pero puede limitar las operaciones de disco |
| Red | Realtek RTL8168/8111, velocidad de enlace 1 Gbps. [HWiNFO](../evidencias/capturas-o-salidas/01-inventario-hardware/Red-inicial.JPG) | Ethernet Gigabit PCIe. Realtek [HWiNFO](../evidencias/capturas-o-salidas/01-inventario-hardware/Red-inicial.JPG) | Una interfaz física identificada. Mayor estabilidad que una interfaz inalambrica | Permite conectar el servidor a la red local |
| Plataforma | ASUS PRIME B250M-A, BIOS 0810 de 2017. [CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Mainbord-inicial.JPG) | Chipset Intel B250, UEFI. [CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Masinfo-inicial.JPG) | Plataforma antigua, con posibilidades de ampliación condicionadas por la placa | Dispone de los componentes necesarios para una instalación básica |
### Equipo anfitrion para la modalidad virtualizada
| Area | Dato observado y evidencia | Especificacion y fuente | Limite o diferencia | Justificacion |
|---|---|---|---|---|
| CPU | Intel Core Ultra 5 125U, 12 nucleos, 14 hilos, 4,30GHz. [Centro Informacion Fedora](..../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-definitivo.png)| arquitectura de 64 bits, Intel VT-x [Centro Informacion Fedora](../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-definitivo.png) | Procesador moderno de 12 nucleos hibridos: 2 potentes, 8 eficientes y 2 bajo consumo de la arquitectura Meteor Lake [Intel](https://www.intel.la/content/www/xl/es/products/sku/237330/intel-core-ultra-5-processor-125u-12m-cache-up-to-4-30-ghz/specifications.html) | Dispone de suficiente capacidad de procesamiento para un laboratorio pequeño, aunque será necesario comprobar que VirtualBox permite utilizar KVM dentro de Proxmox. |
| RAM | 16 GB DDR5 instalados, con módulos de 8 GB a 5600 MT/s [Centro Informacion Fedora](../evidencias/capturas-o-salidas/01-inventario-hardware/RAM-definitivo.png)| DDR5-5600, formato SODIMM, 1,1 V. [Fedora](../evidencias/capturas-o-salidas/01-inventario-hardware/RAM-definitivo.png) | La memoria debe compartirse entre Fedora y la máquina virtual Proxmox, que a su vez tendrá que repartirla entre los sistemas invitados. | Se propone asignar 8 GiB a Proxmox, dejando el resto de la memoria para Fedora y sus aplicaciones. |
| Almacenamiento | SSD Kingston NV3 NVMe de 1 TB (931,5 GiB), dividido mediante dual boot entre Windows y Fedora. Fedora dispone de una partición LUKS de 452,6 GiB, con aproximadamente 377 GiB disponibles. [lsblk](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco-definitivo.png) y [df -h](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco2-definitivo.png)| SSD Kingston NV3, interfaz NVMe PCIe 4.0. [Kingston](https://www.kingston.com/es/ssd/nv3-nvme-pcie-ssd) | Aunque el disco físico tiene 1 TB, Fedora solo dispone de aproximadamente 453 GiB para su sistema principal, compartidos con sus aplicaciones y archivos. | Los 377 GiB libres permiten crear un disco virtual de 120 GiB para Proxmox sin modificar las particiones de Windows. El almacenamiento NVMe también favorece el rendimiento de las máquinas virtuales. |
| Red | Controladora Ethernet Realtek RTL8111/8168/8211/8411 Gigabit y controladora Wi-Fi Intel Meteor Lake CNVi. [lspci](../evidencias/capturas-o-salidas/01-inventario-hardware/Red-definitivo.png) | Ethernet Gigabit PCIe de la familia RTL8111/8168, compatible con conexiones de hasta 1 Gbps. [Realtek](https://www.realtek.com/) | La conectividad de Proxmox dependerá de la configuración de los adaptadores virtuales de VirtualBox. El uso de Wi-Fi puede limitar determinadas configuraciones de red puente. | Permite conectar Proxmox a la red local y establecer comunicación entre el servidor ASO y el cliente Linux. |
| Plataforma | Dell Latitude 3450 con Fedora Linux y Oracle VirtualBox 7.2.20. [VirtualBox](../evidencias/capturas-o-salidas/01-inventario-hardware/VirtualBox-definitivo.png) | VirtualBox es un software de virtualización compatible con anfitriones Linux y permite configurar virtualización anidada. [Oracle](https://www.virtualbox.org/manual/) | Proxmox no tendrá acceso directo a todo el hardware físico y dependerá de VirtualBox y del sistema anfitrión. | Permite ejecutar Proxmox VE como máquina virtual sin sustituir Fedora y crear dentro el servidor ASO y el cliente Linux. |

NOTA: Aunque en la captura de la RAM conste 8GB, es realmente porque se le hizo captura a uno de los dos modulos (ya que no vi necesario hacer captura del otro, ya que son identicos y ademas arriba ya se indican los 16 totales) 

## 1. Puedo instalar Proxmox en este equipo?

Contrastar el inventario con requisitos oficiales de la version prevista.
Responder por separado y justificar con datos y fuentes:

- Puedo instalar y arrancar Proxmox:
    El ordenador físico investigado cumple los requisitos básicos para instalar Proxmox VE directamente, ya que dispone de un procesador de 64 bits compatible con Intel VT-x, 8 GB de RAM, un disco duro de 500 GB y una tarjeta de red Gigabit Ethernet.

    Según los requisitos oficiales de Proxmox, para una instalación destinada a producción se recomiendan al menos 2 GB de memoria RAM para el sistema y sus servicios, además de la memoria que necesiten las máquinas virtuales. También se recomienda utilizar almacenamiento rápido, preferiblemente SSD.

    Por tanto, el ordenador investigado permitiría una instalación básica de Proxmox, aunque con limitaciones de rendimiento.

    En la modalidad definitiva se utilizará un Dell Latitude 3450 con Fedora Linux y Oracle VirtualBox. Proxmox se instalará como máquina virtual, sin sustituir el sistema operativo del portátil.

- Puedo ejecutar el servidor ASO y el cliente del proyecto: 
    Considero que es posible ejecutar las máquinas srv-aso y cli-linux, siempre que se asignen suficientes recursos a Proxmox y se configure correctamente la virtualización anidada.

    El equipo anfitrión dispone de 16 GB de RAM y un procesador de 12 núcleos, por lo que ofrece mejores posibilidades que el ordenador investigado originalmente.

    La configuración propuesta consiste en asignar 8 GiB de RAM y 4 vCPU a Proxmox, repartiendo posteriormente los recursos entre el servidor y el cliente.

    Aunque esta configuración debería ser suficiente para realizar prácticas de administración y servicios básicos, será necesario comprobar su funcionamiento real.

- Condiciones o comprobaciones pendientes: 
    Antes de comenzar las prácticas será necesario comprobar que la virtualización anidada funciona correctamente en VirtualBox, verificar que Proxmox puede utilizar KVM y confirmar que existe suficiente espacio disponible en el SSD.

    También habrá que configurar las interfaces de red para permitir la comunicación entre el servidor, el cliente y el equipo anfitrión.

    Finalmente, se deberán comprobar los recursos consumidos cuando las dos máquinas virtuales estén encendidas simultáneamente.

## 2. Que limitaciones tendre con este hardware?

Analizar RAM, CPU, disco y red para las cargas previstas. Identificar lo
medido y lo estimado. En VirtualBox, investigar host real y VM: version,
RAM reservada al anfitrion, vCPU, VDI, espacio fisico y red local.
No se utiliza el Proxmox del centro como recurso.

### Reparto de recursos

| Elemento | CPU/vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Proxmox | 4 vCPU | 8 GiB totales | VDI 120 GiB | Recursos que recibirá el entorno Proxmox completo |
| srv-aso | 2 vCPU | 3 GiB | 35 GiB | Servidor de prácticas, con servicios de red y administración |
| cli-linux | 2 vCPU | 2 GiB | 25 GiB | Cliente para realizar conexiones y pruebas |
| Plantilla, ISOs y copias | No aplica | No aplica | Reserva lógica de 15 GiB | Almacenar imágenes y archivos auxiliares |
| Margen de operacion | CPU compartida | 3 GiB dentro de Proxmox; aproximadamente 8 GB fuera para Fedora | | 45 GiB restantes del VDI, incluyendo sistema, almacenamiento y margen | Evitar agotar los recursos disponibles |

Aclaración: los 45 GiB restantes no son todos espacio libre utilizable. Deberán cubrir la instalación de Proxmox, sus particiones y el margen del almacenamiento.

Mostrar calculos de RAM y disco, reparto de vCPU, unidades y supuestos.
Explicar las cargas que pueden funcionar simultaneamente.

### Cálculo de memoria RAM

El equipo anfitrión dispone de 16 GB de RAM, de los cuales se propone asignar 8 GiB a VirtualBox para ejecutar Proxmox.

Dentro de Proxmox, la memoria se repartiría de la siguiente forma:

Proxmox y margen para sus servicios: 3 GiB.

Servidor srv-aso: 3 GiB.

Cliente cli-linux: 2 GiB.

Total: 3 + 3 + 2 = 8 GiB.

De esta manera, aproximadamente la otra mitad de la memoria física quedaría disponible para Fedora y sus aplicaciones. La cantidad exacta dependerá de la memoria reconocida por el sistema y de otros procesos en ejecución.

### Cálculo de almacenamiento

Se propone crear un disco virtual VDI de 120 GiB, distribuido de la siguiente manera:

Servidor srv-aso: 35 GiB.

Cliente cli-linux: 25 GiB.

Imágenes ISO, plantillas y pequeñas copias: reserva de 15 GiB.

Instalación de Proxmox, almacenamiento del sistema y margen: 45 GiB.

Total: 35 + 25 + 15 + 45 = 120 GiB.

Este reparto es una planificación inicial y deberá ajustarse a la distribución real del almacenamiento que se configure durante la instalación de Proxmox. Si el VDI se crea con tamaño dinámico, su ocupación física podrá aumentar a medida que se almacenen datos, hasta alcanzar el límite configurado.

### Reparto de procesador

La máquina virtual de Proxmox tendrá asignadas 4 vCPU. Dentro de ella, srv-aso utilizará 2 vCPU y cli-linux otras 2.

Las vCPU no son núcleos físicos exclusivos, sino recursos de procesamiento compartidos. Por tanto, el rendimiento dependerá también de la carga que tenga Fedora y del funcionamiento de la virtualización anidada.

Se espera poder utilizar simultáneamente el servidor y el cliente para las prácticas, siempre que no se ejecuten servicios especialmente exigentes.

### Limitaciones
| Limitacion | Evidencia o estimacion | Consecuencia | Medida y limite que permanece |
|---|---|---|---|
| RAM | Dell con 16 GB, asignación propuesta de 8 GiB a Proxmox | Recursos limitados para aumentar el número de VM | Ajustar memoria, cerrar aplicaciones en Fedora |
| CPU | Core Ultra 5 125U, 12 núcleos/14 hilos | Sobrecarga por virtualización anidada | Asignar 4 vCPU y controlar el uso de CPU |
| Disco | SSD NVMe de 512 GB; VDI propuesto de 120 GiB | El espacio disponible depende también de Fedora y sus archivos | Controlar el crecimiento del VDI |
| Red | Adaptador de red virtual de VirtualBox pendiente de configurar | Problemas de comunicación entre máquinas | Configurar la red virtual y probar conectividad |
| Virtualización anidada | Proxmox se ejecutará dentro de VirtualBox | Posible incompatibilidad o pérdida de rendimiento | Activar VT-x anidado y comprobar KVM; no garantiza rendimiento idéntico al nativo |
| PC investigado | Pentium G4560, 8 GB RAM, HDD de 500 GB | Menor capacidad de ejecutar cargas simultáneas en instalación física | El cambio de modalidad evita depender de ese equipo |

## 3. Que componentes son mas criticos en mi servidor y por que?

Ordenar al menos tres componentes para este equipo y su servicio.
Distinguir cuello de botella, disponibilidad e integridad de los datos.

| Prioridad y componente | Funcion | Efecto si es insuficiente | Efecto si falla | Evidencia y justificacion |
|---|---|---|---|---|
| 1. Memoria RAM| Alojar Proxmox y las máquinas invitadas | Ralentizaciones, uso de swap e imposibilidad de iniciar más VM | Interrupción de servicios, errores y pérdida de disponibilidad | El Dell tiene 16 GB y se propone asignar 8 GiB a Proxmox |
| 2. Almacenamiento SSD-NVMe| Guardar el sistema, discos virtuales y datos | Falta de espacio para VM o copias | Pérdida de acceso a los datos y posible corrupción | SSD NVMe de 512 GB, compartido entre Fedora y VirtualBox |
| 3. Procesador| Ejecutar Fedora, VirtualBox, Proxmox y los sistemas invitados | Saturación y pérdida de rendimiento | Interrupción de todas las máquinas virtuales | Core Ultra 5 125U con 12 núcleos y 14 hilos |

Componente que mejoraria primero y motivo para priorizarlo frente a otro:
El primer componente que ampliaría sería la memoria RAM, ya que disponer de mayor capacidad permitiría asignar más memoria a Proxmox y ejecutar otras máquinas virtuales sin afectar tanto al sistema anfitrión.

Actualmente el equipo dispone de 16 GB, suficientes como punto de partida para el proyecto, aunque con un margen limitado si se utilizan varias máquinas virtuales y otras aplicaciones simultáneamente.

Como segunda prioridad consideraría ampliar el almacenamiento SSD, especialmente si se van a conservar varias imágenes ISO, copias de seguridad y discos virtuales.

El procesador no sería mi primera prioridad de mejora, ya que el Intel Core Ultra 5 125U dispone de suficientes núcleos para las cargas previstas, aunque será necesario comprobar el rendimiento de la virtualización anidada. Ademas que al ser un procesador de portatil, no es posbile su reemplazo

## Fuentes tecnicas

Consultar al menos tres fuentes primarias, incluyendo Proxmox y
fabricante o plataforma virtual. Relacionarlas con las tres respuestas.

| Fuente y URL | Version/modelo y fecha de consulta | Afirmacion respaldada | Aplicacion al nodo |
|---|---|---|---|
| [Proxmox - Requisitos oficiales](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements) | Proxmox VE, 15/10/2026 | Requisitos de CPU, RAM, almacenamiento y virtualización | Valorar la viabilidad de ambas modalidades |
| [Intel Pentium G4560](https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html) | G4560, 15/10/2026 | Características del procesador, Intel VT-x y EPT | Comprobar el soporte de virtualización del PC original |
| [Oracle - Maquinas virtuales](https://docs.oracle.com/en/virtualization/virtualbox/7.2/user/working-with-vms.html) | VirtualBox 7.2, 15/10/2026 | Configuración y soporte de virtualización anidada | Justificar el funcionamiento de Proxmox dentro de VirtualBox |
| [Oracle - Redes virtuales](https://docs.oracle.com/en/virtualization/virtualbox/7.2/user/networkingdetails.html) | VirtualBox 7.2, 15/10/2026 | Configuraciones de red y adaptadores virtuales | Planificar la comunicación de Proxmox y sus invitados |

## Dictamen de viabilidad

Apto, apto con condiciones o no apto para la propuesta actual. Sintetizar las tres respuestas y las condiciones pendientes de comprobar: 

### Dictamen: APTO CON CONDICIONES.

Tras investigar el hardware del ordenador físico y compararlo con los requisitos oficiales de Proxmox VE, considero que el equipo original sería apto para una instalación básica directamente sobre el hardware, aunque con limitaciones importantes.

El Intel Pentium G4560 permite utilizar virtualización por hardware y los 8 GB de RAM serían suficientes para un entorno de pruebas reducido. Sin embargo, los dos núcleos físicos y el disco duro mecánico podrían limitar considerablemente el rendimiento de varias máquinas virtuales simultáneas.

Finalmente se ha decidido utilizar la modalidad virtualizada en un Dell Latitude 3450 con Fedora Linux, 16 GB de RAM, un Intel Core Ultra 5 125U y almacenamiento SSD NVMe.

Este equipo ofrece mejores prestaciones que el ordenador investigado, aunque el uso de Proxmox dentro de VirtualBox requiere disponer de virtualización anidada, que todavía debe comprobarse.

Por tanto, considero que la propuesta es viable para realizar las prácticas del proyecto, siempre que se configure correctamente VirtualBox, se compruebe el funcionamiento de KVM y se disponga de suficiente memoria y almacenamiento para ejecutar simultáneamente el servidor ASO y el cliente Linux.

## Evidencias y revision docente

Enlazar salidas revisadas desde ../evidencias/. Un listado automatico no
sustituye la investigacion. Registrar la pregunta de defensa y respuesta.

### Evidencias del equipo fisico
- [Procesador - CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-inicial.JPG)
- [Placa base - CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Mainboard-incial.JPG)
- [Memoria RAM - CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Memoria-inicial.JPG)
- [Módulo de memoria - CPU-Z](../evidencias/capturas-o-salidas/01-inventario-hardware/Memoria2-inicial.JPG)
- [Almacenamiento - CrystalDiskInfo](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco-incial.JPG)
- [Tarjeta de red - HWiNFO](../evidencias/capturas-o-salidas/01-inventario-hardware/Red-inicial.JPG)
- [Tarjeta gráfica - HWiNFO](../evidencias/capturas-o-salidas/01-inventario-hardware/GPU-inicial.JPG)
- [Información adicional](../evidencias/capturas-o-salidas/01-inventario-hardware/Masinfo-inicial.JPG)
- [Datos de Linux del equipo original](../evidencias/01-inventario-hardware/datos-equipo.txt)

### Evidencias del anfitrion y VirtualBox
- [Procesador - Centro Informacion Fedora](../evidencias/capturas-o-salidas/01-inventario-hardware/CPU-definitiva.png): Intel Core Ultra 5 125U, 12 núcleos, 14 hilos y soporte para Intel VT-x.
- [Memoria RAM - Centro Informacion Fedora](../evidencias/capturas-o-salidas/01-inventario-hardware/RAM-definitivo.png): memoria DDR5-5600 en formato SODIMM. La captura muestra uno de los módulos de 8 GiB instalados.
- [Almacenamiento - lsblk](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco-definitivo.png): SSD Kingston NV3 de 1 TB, particionado para arranque dual entre Windows y Fedora.
- [Espacio disponible - df -h](../evidencias/capturas-o-salidas/01-inventario-hardware/Disco2-definitivo.png): Fedora dispone de aproximadamente 377 GiB libres en su sistema de archivos principal.
- [Interfaces de red - lspci](../evidencias/capturas-o-salidas/01-inventario-hardware/Red-definitivo.png): controladora Ethernet Realtek Gigabit y controladora Wi-Fi Intel.
- [Versión de VirtualBox](../evidencias/capturas-o-salidas/01-inventario-hardware/VirtualBox-definitivo.png): Oracle VirtualBox 7.2.20 instalado en Fedora.
- [Informacion adicional](../evidencias/capturas-o-salidas/01-inventario-hardware/Masinfo-definitivo.png)


[Volver al indice](../README.md)
