# 02-diagrama-red

## Objetivo
Diseñar la red que utilizaremos en nuestro Laboratorio de Proxmox: identificando el nodo Proxmox, el servidor ASO y el cliente ASO; realizando todo esto antes de la instalacion de Proxmox

## Procedimiento
- En esta tabla se muestra como sera la jerarquia de red de nuestro laboratorio Proxmox:

| Elemento | Nombre | IP | Funcion |
|---|---|---|---|
| VirtualBox, adaptador 1 | NAT | No asignable | Salida a internet de Proxmox |
| Nodo, vmbr0 sobre adaptador 1 | Adaptador internet | 10.0.2.15/24 | Salida a internet del nodo |
| Gateway NAT de VirtualBox | No asignable | 10.0.2.2 | Salida a internet del servidor y cliente ASO |
| Virtualbox, interfaz solo-anfitrion | Adaptador VM-anfitrion | 192.168.56.1/24 | Permitir conexion entre maquina virtual y el ordenador |
| Nodo, vmbr1 sobre adaptador 2 | No asignable | 192.168.56.10/24 | Gestion del nodo y gateway de invitados |
| srv-aso en vmbr1 | No asignable | 192.168.56.20/24 | Adaptador server ASO |
| cli-linux en vmbr1 | No asignable | 192.168.56.30/24 | Adaptador cliente ASO |

- Y ahora mostrare un diagrama visual donde veremos la planificacion de la red
```mermaid
graph LR
    %% DEFINICIÓN DE ESTILOS DE ALTO CONTRASTE
    classDef ext fill:#1e293b,stroke:#38bdf8,stroke-width:2px,color:#fff;
    classDef host fill:#0f172a,stroke:#ec4899,stroke-width:2px,color:#fff;
    classDef pve fill:#312e81,stroke:#818cf8,stroke-width:2px,color:#fff;
    classDef vms fill:#1e1b4b,stroke:#a78bfa,stroke-width:2px,color:#fff;
    classDef item fill:#f8fafc,stroke:#334155,stroke-width:1px,color:#0f172a;

    %% RED EXTERNA Y NAT
    subgraph RED_NAT [Red NAT - Salida Internet]
        Internet((Internet)) --> VB_NAT[Adaptador 1: NAT VirtualBox]
        VB_NAT --> GW_NAT[Gateway NAT <br> 10.0.2.2]
    end
    class RED_NAT ext;
    class Internet,VB_NAT,GW_NAT item;

    %% ORDENADOR ANFITRION
    subgraph RED_ANFITRION [Red Solo-Anfitrión]
        Host[PC Anfitrión <br> 192.168.56.1/24]
    end
    class RED_ANFITRION host;
    class Host item;

    %% ENTORNO PROXMOX VE
    subgraph NODO_PVE [Nodo Proxmox VE]
        VMBR0[vmbr0: Adaptador Internet <br> 10.0.2.15/24]
        VMBR1[vmbr1: Gestión e Invitados <br> 192.168.56.10/24]

        subgraph INVITADOS [Máquinas Virtuales ASO]
            SRV[srv-aso <br> 192.168.56.20/24]
            CLI[cli-linux <br> 192.168.56.30/24]
        end
    end
    class NODO_PVE pve;
    class INVITADOS vms;
    class VMBR0,VMBR1,SRV,CLI item;

    %% ENLACES Y CONEXIONES lÓGICAS
    GW_NAT ===>|Salida WAN| VMBR0
    Host <===>|Conexión Gestión| VMBR1
    
    %% Conexiones internas de las VMs al puente
    VMBR1 --- SRV
    VMBR1 --- CLI

    %% Nota aclaratoria para del enrutamiento
    VMBR1 -.->|Enruta tráfico hacia| VMBR0
```

[Volver al indice](../README.md)
