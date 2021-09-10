---
title: Herramienta de diagnóstico para el entorno de ejecución de integración autohospedado
description: Herramienta de diagnóstico para el entorno de ejecución de integración autohospedado
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b8aa070759ea29c9c2853d0c8a6af184bc169569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780800"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>Herramienta de diagnóstico para el entorno de ejecución de integración autohospedado
El entorno de ejecución de integración autohospedado es la infraestructura de proceso que Azure Data Factory usa para proporcionar funcionalidades de integración de datos en distintos entornos de red. La instalación de un entorno de ejecución de integración autohospedado debe realizarse en una máquina local o en una máquina virtual dentro de una red privada. A veces, es difícil investigar incidencias en máquinas locales, por ejemplo, relacionadas con la red, el firewall, la dependencia o el sistema operativo. En este artículo se describe una nueva herramienta de diagnóstico para solucionar problemas en entornos locales.

La herramienta ejecuta una serie de escenarios de prueba en la máquina del entorno de ejecución de integración autohospedado. Cada escenario tiene casos de comprobación de estado típicos para incidencias comunes. Los clientes pueden desencadenar la característica "solucionar problemas" si se producen incidencias. La herramienta recopila la información del entorno del cliente y ejecuta los casos de comprobación de estado. 

## <a name="get-started"></a>Introducción 
Hay dos maneras de ejecutar la herramienta de diagnóstico para detectar posibles incidencias:

- Si instala un entorno de ejecución de integración autohospedado en una máquina local, puede acceder a la característica de solución de problemas desde Configuration Manager. Si se producen incidencias, seleccione **Solucionar problemas** para ejecutar la herramienta de diagnóstico. También puede seleccionar esta misma opción en la pestaña **Diagnóstico** tras la instalación.

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="Captura de pantalla en la que se muestra cómo solucionar problemas mediante Configuration Manager en el entorno de ejecución":::
   
- También puede iniciar la herramienta de diagnóstico desde la línea de comandos:

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>Resultado del diagnóstico
El resultado de la ejecución y los mensajes de registro de detalles se generan como informe HTML. Puede revisar el error y obtener los métodos de mitigación sugeridos o la dirección URL de documentos públicos del informe.

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="Captura de pantalla en la que se muestra el proceso de diagnóstico":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="Captura de pantalla en la que se muestra el informe del resultado del diagnóstico":::

## <a name="next-steps"></a>Pasos siguientes

- Revise los [conceptos del entorno de ejecución de integración en Azure Data Factory](./concepts-integration-runtime.md).

- Aprenda a [crear un entorno de ejecución de integración autohospedado en Azure Portal](./create-self-hosted-integration-runtime.md).
