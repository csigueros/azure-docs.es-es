---
title: Novedades del agente de servidores habilitados para Azure Arc
description: En este artículo se incluyen las notas de la versión del agente de servidores habilitados para Azure Arc. Muchos de los problemas resumidos incluyen vínculos para obtener detalles adicionales.
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d4008a41629ac2e71e1abdb91e30f2d6b9350538
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431611"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Novedades del agente de servidores habilitados para Azure Arc

El agente de Connected Machine de los servidores habilitados para Azure Arc se mejora de manera continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. Si busca elementos que tengan más de seis meses de antigüedad, puede encontrarlos en el [archivo de novedades del agente de servidores habilitados para Azure Arc](agent-release-notes-archive.md).

## <a name="august-2021"></a>Agosto de 2021

Versión 1.10

### <a name="fixed"></a>Fijo

- El agente de directiva de configuración de invitado ahora puede configurar y corregir la configuración del sistema. Las asignaciones de directivas existentes siguen siendo de solo auditoría. Obtenga más información sobre las [opciones de corrección de la configuración de invitado](../../governance/policy/concepts/guest-configuration-policy-effects.md) de Azure Policy.
- El agente de directiva de la configuración de invitado ahora se reinicia cada 48 horas en lugar de cada 6 horas.

## <a name="july-2021"></a>Julio de 2021

Versión 1.9

## <a name="new-features"></a>Nuevas características

Se ha agregado compatibilidad con el idioma indonesio.

### <a name="fixed"></a>Fijo

Se ha corregido un error que impedía la administración de extensiones en la región Oeste de EE. UU. 3

Versión 1.8

### <a name="new-features"></a>Nuevas características

- Confiabilidad mejorada al instalar la extensión del agente de Azure Monitor en los sistemas Red Hat y CentOS
- Se ha agregado el cumplimiento del lado agente de la longitud máxima del nombre de recurso (54 caracteres)
- Mejoras de la directiva de configuración de invitado:
  - Se ha agregado compatibilidad con directivas de configuración de invitado basada en PowerShell en sistemas operativos Linux
  - Se ha agregado compatibilidad para varias asignaciones de la misma directiva de configuración de invitado en el mismo servidor
  - Se ha actualizado PowerShell Core a la versión 7.1 en sistemas operativos Windows

### <a name="fixed"></a>Fijo

- El agente seguirá ejecutándose si no puede escribir eventos de inicio o detención del servicio en el registro de eventos de la aplicación Windows

## <a name="june-2021"></a>Junio de 2021

Versión 1.7

### <a name="new-features"></a>Nuevas características

- Confiabilidad mejorada durante la incorporación:
  - Lógica de reintento mejorada cuando HIMDS no está disponible
  - Ahora, la incorporación continuará en lugar de anularse si no se puede obtener información del sistema operativo
- Confiabilidad mejorada al instalar la extensión del agente de OMS en los sistemas Red Hat y CentOS

## <a name="may-2021"></a>Mayo de 2021

Version 1.6

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con SUSE Enterprise Linux 12
- Se ha actualizado el agente de configuración de invitado a la versión 1.26.12.0 para incluir lo siguiente:

   - Las directivas se ejecutan en un proceso independiente.
   - Compatibilidad adicional con la firma de V2 para la validación de extensiones.
   - Actualización secundaria para el registro de datos.

## <a name="april-2021"></a>Abril de 2021

Versión 1.5

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con Red Hat Enterprise Linux 8 y CentOS Linux 8.
- Nuevo parámetro `-useStderr` para dirigir la salida de error y detallada a stderr.
- El nuevo parámetro `-json` para dirigir la salida da lugar al formato JSON (cuando se usa con -useStderr).
- Recopile otros metadatos de instancia: fabricante, modelo y el identificador de recurso del clúster (para nodos de Azure Stack HCI).
 
## <a name="next-steps"></a>Pasos siguientes

- Antes de evaluar o habilitar los servidores habilitados para Arc en varias máquinas híbridas, consulte la [introducción al agente de Connected Machine](agent-overview.md) para conocer los requisitos y detalles técnicos del agente y los métodos de implementación.

- Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.
