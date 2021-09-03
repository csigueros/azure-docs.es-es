---
title: Instalar las herramientas de cliente
description: Instale azdata, kubectl, la CLI de Azure, psql, Azure Data Studio (Insider) y la extensión ARC para Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e328c65eb7453a8e80faa315c0b038e8fa6f13e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750283"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalación de las herramientas de cliente para implementar y administrar los servicios de datos habilitados para Azure Arc

En este artículo se lo dirige a los recursos de instalación de las herramientas para administrar los servicios de datos habilitados para Arc.

> [!IMPORTANT]
> Si decide actualizar a otra versión, hágalo a la versión más reciente de Azure Data Studio, la extensión de Azure Arc para Azure Data Studio, la interfaz de la línea de comandos (CLI) de Azure (`az`) y [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)].
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

La [extensión `arcdata` para la CLI de Azure (`az`)](reference/reference-az-arcdata-dc.md) reemplaza `azdata` por los servicios de datos habilitados para Arc.

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Herramientas para crear y administrar los servicios de datos habilitados para Azure Arc

En la tabla siguiente se enumeran las herramientas comunes necesarias para crear y administrar los servicios de datos habilitados para Azure Arc y cómo instalar esas herramientas:

| Herramienta | Obligatorio | Descripción | Instalación |
|---|---|---|---|
| CLI de Azure (`az`)<sup>1</sup> | Sí | Interfaz de línea de comandos moderna para administrar servicios de Azure. Se usa para administrar servicios de Azure en general y también, específicamente, servicios de datos habilitados para Arc mediante la CLI o en scripts para el modo de conexión indirecta (ya disponible) y el modo de conexión directa (disponible próximamente). ([Más información](/cli/azure/)). | [Instalación](/cli/azure/install-azure-cli) |
| Extensión `arcdata` para la CLI de Azure (`az`) | Sí | Herramienta de la línea de comandos para administrar servicios de datos habilitados para Arc como una extensión de la CLI de Azure (`az`) | [Instalación](install-arcdata-extension.md) |
| Azure Data Studio | Sí | Herramienta de experiencia enriquecida para conectarse a una serie de bases de datos y consultarlas, como Azure SQL, SQL Server, PostrgreSQL y MySQL. Las extensiones para Azure Data Studio proporcionan una experiencia de administración para los servicios de datos habilitados para Azure Arc. | [Instalación](/sql/azure-data-studio/download-azure-data-studio) |
| Extensión de Azure Arc para Azure Data Studio | Sí | Extensión para Azure Data Studio que proporciona una experiencia de administración para los servicios de datos habilitados para Azure Arc.| Instalación desde la galería de extensiones en Azure Data Studio.|
| Extensión de PostgreSQL en Azure Data Studio | No | Extensión de PostgreSQL para Azure Data Studio que proporciona capacidades de administración para PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Instalación desde la galería de extensiones en Azure Data Studio.|
| CLI de Kubernetes (kubectl)<sup>2</sup> | Sí | Herramienta de línea de comandos para supervisar el clúster de Kubernetes ([más información](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | Se necesita para algunos scripts de ejemplo. | Herramienta de línea de comandos para transferir datos con direcciones URL. | [Windows](https://curl.haxx.se/windows/) \| Linux: instalación de paquete curl |
| oc | Obligatoria para las implementaciones de Red Hat OpenShift y Red Hat OpenShift en Azure. |`oc` es la interfaz de la línea de comandos (CLI) de Open Shift. | [Instalación de la CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Debe usar la CLI de Azure versión 2.26.0 o posterior. Ejecute `az --version` para encontrar la versión si fuera necesario.

<sup>2</sup> Debe usar la versión 1.19 de `kubectl` o una posterior. Además, la versión de `kubectl` debe ser más o menos una versión secundaria del clúster de Kubernetes. Si quiere instalar una versión concreta en el cliente de `kubectl`, vea [Instalación de `kubectl` binario mediante curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (en Windows 10, use cmd.exe y no Windows PowerShell para ejecutar curl).

<sup>3</sup> Si usa PowerShell, curl es un alias del cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Pasos siguientes

[Creación del controlador de datos de Azure Arc](create-data-controller.md)
