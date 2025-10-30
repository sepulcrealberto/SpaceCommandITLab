# Fase 1 — Implementación del Gateway de Red (pfSense)

## Objetivo
Diseñar e implementar un cortafuegos y router principal con **pfSense CE** que actúe como punto central de conexión entre las distintas redes internas del laboratorio.

El objetivo es construir la base de la infraestructura para el proyecto **Space Command IT Lab**, sobre la que luego se desplegarán redes de misión, administración y servicios.

---

## Infraestructura de Virtualización

**Hipervisor:** Oracle VM VirtualBox  
**Sistema anfitrión:** Windows 10 Pro  

### Máquinas virtuales iniciales

| Nombre | Rol | Sistema Operativo | Red principal | Estado |
|--------|------|-------------------|----------------|--------|
| `pfSense_Gateway` | Cortafuegos / Router | pfSense CE 2.7.2 (FreeBSD 64-bit) | WAN, INT-CTRL, INT-MISSION, INT-DMZ | ✅ Instalado y operativo |
| `CTRL-WS01` | Estación de control / Administración | Windows 10 Pro | INT-CTRL | ✅ Instalado, sin acceso a Internet |

---

## Configuración de `pfSense_Gateway`

### Especificaciones de la VM

| Parámetro | Valor |
|------------|--------|
| Memoria RAM | 2048 MB |
| CPU | 2 núcleos |
| Disco duro | 10 GB (VDI, dinámico) |
| Sistema base | FreeBSD (64-bit) |
| Controlador de red | Intel PRO/1000 MT Desktop |

### Adaptadores de red en VirtualBox

| Adaptador | Tipo | Nombre en VirtualBox | Descripción |
|------------|------|----------------------|--------------|
| 1 | NAT | (por defecto) | Salida a Internet (WAN) |
| 2 | Red interna | `INT-CTRL` | Red de administración |
| 3 | Red interna | `INT-MISSION` | Red de misión (pendiente) |
| 4 | Red interna | `INT-DMZ` | Red perimetral (pendiente) |

---

## Instalación paso a paso

1. **Descarga de ISO**  
   - Desde la web oficial de Netgate: `pfSense-CE-2.7.2-RELEASE-amd64.iso.gz`  
   - Extraer el `.iso` del `.gz`.

2. **Creación de la VM**  
   - Tipo: FreeBSD (64-bit)  
   - RAM: 2 GB  
   - Disco: 10 GB  
   - Adjuntar la ISO en la unidad óptica.

3. **Instalación de pfSense**  
   - Opción: `Install pfSense`  
   - Método de partición: `Auto (UFS)`  
   - Instalar → Reiniciar → Quitar ISO.

4. **Primera configuración (CLI)**  
   - Interfaz **em0 (WAN)** → DHCP (automática)  
   - Interfaz **em1 (LAN)** → 192.168.1.1/24  
   - **DHCP Server** activado: rango `192.168.1.100 - 192.168.1.200`  
   - Acceso web habilitado: `https://192.168.1.1`  
   - Modo seguro (HTTPS) mantenido.

---

## Configuración de `CTRL-WS01`

### Especificaciones
| Parámetro | Valor |
|------------|--------|
| Sistema Operativo | Windows 10 Pro |
| Memoria RAM | 2 GB |
| CPU | 2 núcleos |
| Disco | 20 GB |
| Red VirtualBox | `INT-CTRL` |

### Configuración de red
- IP automática por DHCP (asignada por pfSense)
- IP obtenida: `192.168.1.100`
- Máscara: `255.255.255.0`
- Puerta de enlace: `192.168.1.1`
- DNS: `192.168.1.1`

Comprobaciones:
bash
ping 192.168.1.1    # OK

Acceso web:

https://192.168.1.1


Credenciales iniciales:
Utilizar las credenciales por defecto de pfSense (consultar documentación oficial).

### Estado de la Fase 1

| **Elemento** | **Estado** | **Observaciones** |
|---------------|-------------|--------------------|
| **pfSense instalado** |  OK | Configuración base completada |
| **DHCP funcional** |  OK | Asigna IP correctamente |
| **Acceso web pfSense** |  OK | HTTPS activo y operativo |
| **Cliente Windows conectado** |  OK | Comunicación LAN confirmada |
| **Acceso a Internet desde cliente** |  Pendiente | Se habilitará en la Fase 2 |
