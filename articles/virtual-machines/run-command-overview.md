---
title: Ejecución de scripts en una máquina virtual Windows o Linux en Azure con Ejecutar comando
description: En este tema se proporciona información general sobre la ejecución de scripts en una máquina virtual de Azure mediante la característica Ejecutar comando.
ms.service: virtual-machines
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0442c97e07e89238bed6b670c1529a862ddb1ab0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478212"
---
# <a name="run-scripts-in-your-vm-by-using-run-command"></a>Ejecución de scripts en la máquina virtual mediante Ejecutar comando

Ejecutar comando usa el agente de máquina virtual (VM) para ejecutar scripts en una máquina virtual Windows o Linux de Azure. Puede usar estos scripts para la administración general de máquinas o aplicaciones. Pueden ayudarle a diagnosticar y corregir rápidamente el acceso a la máquina virtual y los problemas de red, así como a revertir la máquina virtual a un buen estado. Los scripts se pueden insertar en las propiedades o hacer referencia a un script de la galería publicado previamente. 


El conjunto original de comandos está orientado a la acción. El conjunto de comandos actualizado, actualmente en versión preliminar pública, está orientado a la administración y le permite ejecutar varios scripts y tiene menos restricciones. En este artículo se explica la diferencia entre los dos conjuntos de comandos de ejecución y se proporciona ayuda para decidir qué conjunto conviene usar en su escenario.  

> [!IMPORTANT]
> Actualmente la característica administrada **Ejecutar comando** está en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="when-to-use-action-or-managed-commands"></a>Cuándo usar los comandos de acción y administrados

El conjunto original de comandos está orientado a la acción. Considere la posibilidad de usar este conjunto de comandos para situaciones en las que necesite ejecutar:
- Un script pequeño para obtener contenido de una máquina virtual 
- Un script para configurar una máquina virtual (establecer claves del Registro o cambiar la configuración) 
- Un script único para diagnósticos

Vea [Comandos de ejecución de acciones para Linux](./linux/run-command.md) y [Comandos de ejecución de acciones para Windows](./windows/run-command.md) para saber qué comandos de acción están disponibles y obtener instrucciones sobre cómo aplicarlos. 

El conjunto de comandos actualizado, actualmente en versión preliminar pública, está orientado a la administración. Considere la posibilidad de usar comandos de ejecución administrados si sus necesidades coinciden con los ejemplos siguientes:
- El script debe ejecutarse como parte de la implementación de máquinas virtuales 
- Se necesita una ejecución de script recurrente 
- Varios scripts deben ejecutarse secuencialmente 
- Arranque de una máquina virtual mediante la ejecución de scripts de instalación 
- Publicación de un script personalizado que se va a compartir y reutilizar 

Vea [Comando de ejecución administrado para Linux](./linux/run-command-managed.md) y [Comando de ejecución administrado para Windows](./windows/run-command-managed.md) para obtener información sobre cómo usar estos comandos.


## <a name="compare-feature-support"></a>Comparación de la compatibilidad con características

| Compatibilidad de características  | Comando de ejecución de acción  | Comando de ejecución administrado  |
|---|---|---|
| Plantilla ARM  | No, es una acción POST  | Sí, es un tipo de recurso  |
| Larga duración  | Límite de 90 minutos  | Tiempo de espera especificado por el cliente  |
| Cuenta de ejecución  | Raíz/cuenta del sistema  | Tiempo de espera especificado por el cliente  |
| Varios comandos de ejecución  | Solo uno activa  | Varios en paralelo o en secuencia  |
| Salida grande  | Limitado a 4000 (en blob de estado)  | Cargado en el blob de anexos del cliente  |
| Seguimiento del progreso  | Solo notifica el estado final  | Notifica el progreso y la última salida de 4000 durante la ejecución  |
| Ejecución asincrónica  | El aprovisionamiento o el estado objetivo esperan a que se complete el script  | Marca asincrónica especificada por el cliente si el aprovisionamiento espera el script  |
| Compatibilidad con los conjuntos de escalado de máquinas virtuales  | Solo en la instancia de máquina virtual  | Compatibilidad con el modelo de conjunto de escalado de máquinas virtuales y el escalado horizontal  |
| Generación de SAS  | Incompatibilidad con blobs  | Automatizado, CRP genera SAS para blobs de clientes y los administra  |
| Galería (comandos personalizados)  | Solo commandIds integrados  | El cliente puede publicar scripts y compartirlos  |


## <a name="next-steps"></a>Pasos siguientes

Introducción a [Comando de ejecución administrado para Linux](./linux/run-command-managed.md) o [Comando de ejecución administrado para Windows](./windows/run-command-managed.md) 