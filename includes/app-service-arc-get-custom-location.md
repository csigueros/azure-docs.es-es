---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: d4d6783645f719f21d97f18abc9bc900a368fd8f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362241"
---
Obtenga la siguiente información sobre la ubicación personalizada del administrador del clúster (consulte [Creación de una ubicación personalizada](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)).

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName="<name-of-custom-location>"
```

Obtenga el identificador de la ubicación personalizada para el paso siguiente.

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```
