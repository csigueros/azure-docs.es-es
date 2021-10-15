---
title: Preguntas más frecuentes sobre Azure Automation
description: En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dfe04bb2fcb7022934ac699bb5c3bd90005bf3b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356595"
---
# <a name="azure-automation-frequently-asked-questions"></a>Preguntas más frecuentes sobre Azure Automation

En este artículo de preguntas más frecuentes de Microsoft, se presenta una lista con las preguntas frecuentes sobre Azure Automation. Si tiene alguna otra pregunta sobre sus funcionalidades, vaya al foro de discusión y publíquela. Si una pregunta es frecuente, se agrega a este artículo para que se encuentre de forma rápida y sencilla.

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>¿Update Management puede evitar actualizaciones inesperadas de nivel de sistema operativo?

Sí. Para obtener más información, consulte [Exclusión de actualizaciones](./update-management/manage-updates-for-vm.md#exclude-updates).

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>¿Por qué no se aplican actualizaciones críticas o de seguridad a una máquina Linux con Update Management?

Al implementar actualizaciones en una máquina Linux, puede seleccionar clasificaciones de actualizaciones. Esta opción filtra las actualizaciones que cumplen los criterios especificados. Para más información, consulte [Clasificaciones de actualizaciones de Linux](./update-management/manage-updates-for-vm.md#linux-update-classifications).

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>¿Update Management puede implementar actualizaciones en los inquilinos de Azure?

Sí. Para obtener instrucciones, consulte [Implementación de actualizaciones en los inquilinos de Azure](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants).

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>¿Qué versión de Python 3 se admite en Azure Automation?

En el caso de los trabajos en la nube, se admite Python 3.8. Es posible que los scripts y paquetes de cualquier versión 3.x funcionen si el código es compatible con distintas versiones.

En el caso de los trabajos híbridos de Hybrid Runbook Worker para Windows, puede optar por instalar cualquier versión 3.x que desee usar. En el caso de los trabajos híbridos de Hybrid Runbook Worker para Linux, dependemos de la versión de Python 3 instalada en el equipo para ejecutar DSC OMSConfig y Hybrid Worker para Linux. Se recomienda instalar la versión 3.6; sin embargo, las distintas versiones también deben funcionar si no hay cambios importantes en los contratos o las firmas de método entre las versiones de Python 3.

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>¿Pueden los runbooks de Python 2 y Python 3 ejecutarse en la misma cuenta de Automation?

Sí, no hay ninguna limitación para usar runbooks de Python 2 y Python 3 en la misma cuenta de Automation.  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>¿Cuál es el plan para migrar los paquetes y runbooks existentes de Python 2 a Python 3?

Azure Automation no pretende migrar los runbooks y paquetes de Python 2 a Python 3. Tendrá que realizar esta migración usted mismo. Los runbooks y paquetes nuevos y existentes de Python 2 seguirán funcionando.

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>¿Cuáles son los paquetes admitidos de manera predeterminada en el entorno de Python 3?

El paquete 4.0.0 de Azure. Para más información, consulte [Administración de paquetes de Python 3](python-3-packages.md).

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>¿Qué ocurre si ejecuto un runbook de Python 3 que haga referencia a un paquete de Python 2 o viceversa?

Python 2 y Python 3 tienen entornos de ejecución diferentes. Mientras se ejecuta un runbook de Python 2, solo se pueden importar paquetes de Python 2 y similares para Python 3.

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>¿Cómo se diferencian los paquetes y runbooks de Python 2 y Python 3?

Python 3 es una nueva definición de runbook, que distingue entre los runbooks de Python 2 y Python 3. Del mismo modo, se introduce otro tipo de paquete para los paquetes de Python 3.

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>¿Cómo sabe Hybrid Runbook Worker la versión de Python que se debe ejecutar cuando se instalan Python 2 y Python 3?

En el caso de Runbook Worker de Windows, al ejecutar un runbook de Python 2, este busca primero la variable de entorno `PYTHON_2_PATH` y valida si apunta a un archivo ejecutable válido. Por ejemplo, si la carpeta de instalación es `C:\Python2`, comprobaría si `C:\Python2\python.exe` es una ruta de acceso válida. Si no se encuentra, busca la variable de entorno `PATH` para realizar una comprobación similar.

Para Python 3, primero busca la variable env `PYTHON_3_PATH` y, a continuación, recurre a la variable de entorno `PATH`.

Consulte [Varias versiones de Python](automation-runbook-types.md#multiple-python-versions).

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>¿Cómo localiza una instancia de Hybrid Runbook Worker al intérprete de Python?

La búsqueda del módulo de Python se controla mediante variables de entorno, como se explicó anteriormente.

## <a name="is-python-3-supported-in-source-control"></a>¿Se admite Python 3 en el control de código fuente?

No. Actualmente, el control de código fuente no se admite para Python 3. De forma predeterminada, los runbooks de Python se sincronizan como runbooks de Python 2.

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>¿Cómo puede un autor de runbook saber qué paquetes de Python están disponibles en un espacio aislado de Azure?

Consulte [Identificación de los paquetes disponibles en el espacio aislado](python-3-packages.md#identify-available-packages-in-sandbox).

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>¿Cómo puede establecer un autor de runbook qué versión de un módulo de paquetes se va a usar si hay varios módulos?

Consulte [Administración de paquetes de Python 3](python-3-packages.md).

## <a name="next-steps"></a>Pasos siguientes

Si su pregunta no se ha respondido aquí, puede consultar las fuentes siguientes para preguntas y respuestas adicionales.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Foro de comentarios](https://feedback.azure.com/forums/905242-update-management)
