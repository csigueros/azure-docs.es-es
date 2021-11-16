---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/13/2021
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ae085270a28bfd27542585d5df6c1dd918943d9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421650"
---
| Resource | Descripción|
|----------|------------|
| **Origen de imagen** | Se trata de un recurso que se puede usar para crear una **versión de imagen** en una galería. Un origen de imagen puede ser una máquina virtual de Azure existente, ya sea [generalizada o especializada](../shared-image-galleries.md#generalized-and-specialized-images), una imagen administrada, una instantánea o una versión de imagen de otra galería. |
| **Galería** | Al igual que Azure Marketplace, una **galería** es un repositorio para administrar y compartir imágenes y [aplicaciones de máquina virtual](../vm-applications.md), pero con control sobre quién tiene acceso. |
| **Definición de la imagen** | Las definiciones de imagen se crean dentro de una galería y contienen información sobre la imagen y los requisitos para usarla internamente. Esto incluye si la imagen es Windows o Linux, notas de la versión y los requisitos de memoria mínima y máxima. Es una definición de un tipo de imagen. |
| **Versión de la imagen** | Una **versión de la imagen** es lo que se usa para crear una VM cuando se usa una galería. Puede tener varias versiones de una imagen según sea necesario para su entorno. Al igual que una imagen administrada, cuando se usa una **versión de la imagen** para crear una VM, la versión de la imagen se usa para crear nuevos discos para la VM. Las versiones de las imágenes pueden usarse varias veces. |