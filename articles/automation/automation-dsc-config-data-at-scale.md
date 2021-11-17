---
title: Configurar datos a gran escala para Azure Automation State Configuration
description: En este artículo se explica cómo configurar los datos a gran escala para Azure Automation State Configuration.
keywords: dsc,powershell,configuration,setup
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd1c7eceb86d1153d08414a5c81ed09b395916c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484546"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Configurar datos a gran escala para Azure Automation State Configuration

> Se aplica a: Windows PowerShell 5.1

Administrar cientos o miles de servidores puede ser un reto.
De los comentarios de los clientes se deduce que el aspecto más difícil es realmente administrar los [datos de configuración](/powershell/scripting/dsc/configurations/configdata).
Organizar la información en construcciones lógicas como la ubicación, el tipo y el entorno.

> [!NOTE]
> En este artículo se hace referencia a una solución que se mantiene en la comunidad de código abierto.
> La compatibilidad solo está disponible en forma de colaboración en GitHub, no con Microsoft.

## <a name="community-project-datum"></a>Proyecto de la comunidad: Datum

Para resolver este reto, se ha creado una solución mantenida por la comunidad llamada [Datum](https://github.com/gaelcolas/Datum).
Datum se basa en las grandes ideas de otras plataformas de administración de la configuración e implementa el mismo tipo de solución para DSC de PowerShell.
La información se [organiza en archivos de texto](https://github.com/gaelcolas/Datum#3-intended-usage) basados en ideas lógicas.
Algunos ejemplos serían:

- Configuración que se debe aplicar globalmente
- Configuración que se debe aplicar a todos los servidores de una ubicación
- Configuración que se debe aplicar a todos los servidores de bases de datos
- Configuración de un solo servidor

Esta información se organiza en el formato de archivo que prefiera (JSON, YAML o PSD1).
A continuación, se proporcionan cmdlets para generar archivos de datos de configuración mediante la [consolidación de la información](https://github.com/gaelcolas/Datum#datum-tree) de cada archivo en una vista única de un servidor o rol de servidor.

Una vez que se han generado los archivos de datos, puede usarlos con [scripts de configuración de DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para generar archivos MOF y [cargarlos en Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
A continuación, registre los servidores desde una [ubicación local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) o [en Azure](./automation-dsc-onboarding.md#enable-azure-vms) para extraer las configuraciones.

Para probar Datum, visite la [Galería de PowerShell](https://www.powershellgallery.com/packages/datum/) y descargue la solución o haga clic en "Project Site" (Sitio del proyecto) para ver la [documentación](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Pasos siguientes

- Para comprender DSC de PowerShell, vea [Información general sobre Desired State Configuration de PowerShell](/powershell/scripting/dsc/overview/overview).
- Obtenga información sobre los recursos de DSC de PowerShell en [Recursos de DSC](/powershell/scripting/dsc/resources/resources).
- Para obtener información detallada sobre la configuración de Configuration Manager, vea [Configuración de Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
