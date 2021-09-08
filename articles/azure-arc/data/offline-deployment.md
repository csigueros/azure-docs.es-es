---
title: Implementación sin conexión
description: La implementación sin conexión permite extraer imágenes de contenedor de un registro de contenedor privado en lugar de extraerlas de Microsoft Container Registry.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e81ccc8ef133cdf7c454a7a50610a1bfdf2be442
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731277"
---
# <a name="offline-deployment-overview"></a>Introducción a la implementación sin conexión

Normalmente, las imágenes de contenedor usadas en la creación del controlador de datos de Azure Arc, las instancias de SQL Managed Instance y los grupos de servidores Hiperescala de PostgreSQL se extraen directamente de Microsoft Container Registry (MCR). En algunos casos, el entorno en el que se implementa no tendrá conectividad con Microsoft Container Registry.  En situaciones como esta, puede extraer las imágenes de contenedor desde un equipo que _tenga_ acceso a Microsoft Container Registry y, a continuación, etiquetarlas e insertarlas en un registro de contenedor privado al que _pueda_ conectarse desde el entorno en el que desea implementar los servicios de datos habilitados para Azure Arc.

Dado que se proporcionan actualizaciones mensuales para los servicios de datos habilitados para Azure Arc y hay un gran número de imágenes de contenedor, es mejor realizar este proceso de extracción, etiquetado e inserción de las imágenes de contenedor en un registro de contenedor privado mediante un script.  El script se puede automatizar o ejecutar manualmente.

Puede encontrar un [script de ejemplo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) en el repositorio de GitHub de Azure Arc.

> [!NOTE]
> Este script requiere la instalación de Python y la [CLI de Docker](https://docs.docker.com/install/).

El script solicitará de forma interactiva la información siguiente.  Como alternativa, si desea que el script se ejecute sin mensajes interactivos, puede establecer las variables de entorno correspondientes antes de ejecutar el script.

|Prompt|Variable de entorno|Notas|
|---|---|---|
|Proporcione el registro de contenedor de origen: pulse Entrar para usar `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Normalmente, las imágenes se extraen de Microsoft Container Registry, pero si participa en una versión preliminar privada con otro registro, puede usar la información proporcionada como parte del programa de versión preliminar.|
|Proporcione el repositorio del registro de contenedor de origen: pulse Entrar para usar `arcdata`:|SOURCE_DOCKER_REPOSITORY|Si va a extraer desde Microsoft Container Registry, el repositorio será `arcdata`.|
|Proporcione el nombre de usuario del registro de contenedor de origen: pulse Entrar para no usar ninguno:|SOURCE_DOCKER_USERNAME|Proporcione un valor solo si va a extraer imágenes de contenedor desde un origen que requiere inicio de sesión.  Microsoft Container Registry no requiere un inicio de sesión.|
|Proporcione la contraseña del registro de contenedor de origen: pulse Entrar para no usar ninguna:|SOURCE_DOCKER_PASSWORD|Proporcione un valor solo si va a extraer imágenes de contenedor desde un origen que requiere inicio de sesión.  Microsoft Container Registry no requiere un inicio de sesión. Es un mensaje de contraseña con máscara.  No podrá ver la contraseña si la escribe o la pega.|
|Proporcione una etiqueta de imagen de contenedor para las imágenes del origen: presione Entrar para usar "`<current monthly release tag>`":|SOURCE_DOCKER_TAG|El nombre de etiqueta predeterminado se actualizará mensualmente para reflejar el mes y el año de la versión actual en Microsoft Container Registry.|
|Proporcione el nombre DNS o la dirección IP del registro de contenedor de destino:|TARGET_DOCKER_REGISTRY|Nombre DNS o dirección IP del registro de destino.  Este es el registro en el que se _insertarán_ las imágenes.|
|Proporcione el repositorio del registro del contenedor de destino:|TARGET_DOCKER_REPOSITORY|Repositorio del registro de destino en el que se van a insertar las imágenes.|
|Proporcione el nombre de usuario del registro de contenedor de destino: pulse Entrar para no usar ninguno:|TARGET_DOCKER_USERNAME|Nombre de usuario, si lo hay, que se usa para iniciar sesión en el registro de contenedor de destino.|
|Proporcione la contraseña del registro de contenedor de destino: pulse Entrar para no usar ninguna:|TARGET_DOCKER_PASSWORD|Contraseña, si la hay, que se usa para iniciar sesión en el registro de contenedor de destino. Es un mensaje de contraseña con máscara.  No podrá ver la contraseña si la escribe o la pega.|
|Proporcione una etiqueta de imagen de contenedor para las imágenes en el destino:|TARGET_DOCKER_TAG|Normalmente, se usaría la misma etiqueta que en el origen para evitar confusiones.|