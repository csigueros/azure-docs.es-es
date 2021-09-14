---
title: Archivo de novedades del agente de servidores habilitados para Azure Arc
description: Las notas de la versión de novedades de la sección Información general del agente de servidores habilitados para Azure Arc abarcan seis meses de actividad. Después, los elementos se quitan del artículo principal y se colocan en este artículo.
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 45f7ed97cf9e0fbb389ccf893f2674e2601ee7f9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441651"
---
# <a name="archive-for-whats-new-with-azure-arc-enabled-servers-agent"></a>Archivo de novedades del agente de servidores habilitados para Azure Arc

El artículo principal [Novedades del agente de servidores habilitados para Azure Arc](agent-release-notes.md) incluye actualizaciones de los últimos seis meses, mientras que este artículo contiene toda la información anterior.

El agente de Connected Machine de los servidores habilitados para Azure Arc se mejora constantemente. En este artículo se proporciona información acerca de lo siguiente:

- Versiones anteriores
- Problemas conocidos
- Corrección de errores

## <a name="march-2021"></a>Marzo de 2021

Versión 1.4

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con puntos de conexión privados, que actualmente se encuentra en versión preliminar limitada.
- Lista expandida de códigos de salida para azcmagent.
- Los parámetros de configuración del agente ahora se pueden leer a partir desde un archivo mediante el parámetro `--config`.
- Recopilación de nuevos metadatos de instancia para determinar si Microsoft SQL Server está instalado en el servidor

### <a name="fixed"></a>Fijo

Las comprobaciones de punto de conexión de red son ahora más rápidas.

## <a name="december-2020"></a>Diciembre de 2020

Versión: 1.3

### <a name="new-features"></a>Nuevas características

Se ha agregado compatibilidad con Windows Server 2008 R2 SP1.

### <a name="fixed"></a>Fijo

Problema resuelto que impide que la extensión de script personalizado de Linux se instale correctamente.

## <a name="november-2020"></a>Noviembre de 2020

Versión: 1.2

### <a name="fixed"></a>Fijo

Se resolvió un problema en el que la configuración del proxy se podía perder después de actualizar las distribuciones basadas en RPM.

## <a name="october-2020"></a>Octubre de 2020

Versión: 1.1

### <a name="fixed"></a>Fijo

- Se corrigió un script de proxy para controlar la ubicación alternativa del archivo de unidad de demonio GC.
- Cambios de confiabilidad del agente GuestConfig.
- Compatibilidad del agente GuestConfig con la región US Gov Virginia.
- Los mensajes de informes de la extensión del agente GuestConfig son más detallados en caso de error.

## <a name="september-2020"></a>Septiembre de 2020

Versión: 1.0 (Disponibilidad general)

### <a name="plan-for-change"></a>Plan de cambio

- La compatibilidad con las versiones preliminares de agentes (todas las versiones anteriores a 1.0) se quitará en una actualización de servicio futura.
- Se ha quitado la compatibilidad con el punto de conexión de reserva `.azure-automation.net`. Si tiene un proxy, debe permitir el punto de conexión `*.his.arc.azure.com`.
- Si el agente de Connected Machine está instalado en una máquina virtual hospedada en Azure, las extensiones de máquina virtual no se podrán instalar ni modificar desde el recurso de servidores habilitados para Arc. Esto es así para evitar que se realicen operaciones de extensión en conflicto desde los recursos **Microsoft.Compute** y **Microsoft.HybridCompute** de la máquina virtual. Use el recurso **Microsoft.Compute** de la máquina para todas las operaciones de extensión.
- El nombre del proceso de configuración de invitado ha cambiado de *gcd* a *gcad* en Linux; y de *gcservice* a *gcarcservice* en Windows.

### <a name="new-features"></a>Nuevas características

- Se ha agregado la opción `azcmagent logs` para recopilar información de soporte técnico.
- Se ha agregado la opción `azcmagent license` para mostrar el CLUF.
- Se ha agregado la opción `azcmagent show --json` al estado del agente de salida en un formato fácilmente analizable.
- Se ha agregado una marca en la salida `azcmagent show` para indicar si el servidor está en una máquina virtual hospedada en Azure.
- Se ha agregado la opción `azcmagent disconnect --force-local-only` para permitir el restablecimiento del estado del agente local cuando no se puede acceder al servicio de Azure.
- Se ha agregado la opción `azcmagent connect --cloud` para admitir otras nubes. En esta versión, solo Azure es compatible con el servicio en el momento de la versión del agente.
- El agente se ha localizado en idiomas compatibles con Azure.

### <a name="fixed"></a>Fijo

- Mejoras en la comprobación de conectividad.
- Se ha corregido la incidencia con la configuración del servidor proxy que se había perdido al actualizar el agente en Linux.
- Se han resuelto incidencias al intentar instalar el agente en el servidor que ejecuta Windows Server 2012 R2.
- Mejoras en la fiabilidad de la instalación de extensiones

## <a name="august-2020"></a>Agosto de 2020

Versión: 0,11

- Esta versión ha anunciado anteriormente la compatibilidad con Ubuntu 20.04. Dado que algunas extensiones de máquina virtual de Azure no admiten Ubuntu 20.04, se está quitando la compatibilidad con esta versión de Ubuntu.

- Mejoras de la confiabilidad para las implementaciones de extensiones.

### <a name="known-issues"></a>Problemas conocidos

Si usa una versión anterior del agente de Linux y la ha configurado para usar un servidor proxy, debe volver a configurar los valores del servidor proxy después de la actualización. Para ello, ejecute `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Pasos siguientes

- Antes de evaluar o habilitar los servidores habilitados para Arc en varias máquinas híbridas, consulte la [introducción al agente de Connected Machine](agent-overview.md) para conocer los requisitos y detalles técnicos del agente y los métodos de implementación.

- Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.