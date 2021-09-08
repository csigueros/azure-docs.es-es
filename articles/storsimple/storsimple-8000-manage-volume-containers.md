---
title: Administración de contenedores de volúmenes para dispositivos StorSimple serie 8000
description: Explica cómo se puede usar la página de contenedores de volúmenes del servicio Administrador de dispositivos de StorSimple para agregar, modificar o eliminar contenedores de volúmenes.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: d48f1cf99b22c091f069e7e5f572941a234eaebe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449871"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Uso del servicio Administrador de dispositivos de StorSimple para administrar contenedores de volúmenes de StorSimple

## <a name="overview"></a>Información general
En este tutorial se explica cómo usar el servicio Administrador de dispositivos de StorSimple para crear y administrar contenedores de volúmenes de StorSimple.

En un dispositivo Microsoft Azure StorSimple, los contenedores de volúmenes contienen uno o varios volúmenes que comparten la configuración de cuentas de almacenamiento, cifrado y consumo de ancho de banda. Un dispositivo puede tener varios contenedores de volúmenes para todos sus volúmenes. 

Los contenedores de volúmenes tienen los siguientes atributos:

* **Volúmenes** : Los volúmenes de StorSimple en niveles o anclados localmente que se encuentran dentro del contenedor de volúmenes. 
* **Cifrado**: una clave de cifrado que se puede definir para cada contenedor de volúmenes. Esta clave se utiliza para cifrar los datos que se envían desde un dispositivo de StorSimple a la nube. Se utiliza una clave de grado militar AES de 256 bits con la clave especificada por el usuario. Para proteger los datos, se recomienda habilitar siempre el cifrado de almacenamiento en la nube.
* **Cuenta de almacenamiento** : cuenta de Azure Storage que se usa para almacenar los datos. Todos los volúmenes que se encuentran en un contenedor de volúmenes comparten esta cuenta de almacenamiento. Al crear el contenedor de volúmenes puede elegir una cuenta de almacenamiento de una lista existente, o bien crear una cuenta nueva y, a continuación, especificar las credenciales de acceso de dicha cuenta.
* **Ancho de banda en la nube**: el ancho de banda utilizado por el dispositivo cuando los datos del dispositivo se envían a la nube. Si quiere que el dispositivo use todo el ancho de banda, establezca este campo en **Ilimitado**. También puede crear y aplicar una plantilla de ancho de banda para asignar el ancho de banda según una programación.

Los siguientes procedimientos explican cómo usar la hoja **Contenedores de volúmenes** de StorSimple para realizar las siguientes operaciones comunes:

* Agregar un contenedor de volúmenes
* Modificar un contenedor de volúmenes
* Eliminar un contenedor de volúmenes

## <a name="add-a-volume-container"></a>Agregar un contenedor de volúmenes
Realice los siguientes pasos para agregar un contenedor de volúmenes.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar un contenedor de volúmenes
Realice los siguientes pasos para modificar una contenedor de volúmenes.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminar un contenedor de volúmenes
Los contenedores de volúmenes contienen volúmenes. Solo se puede eliminar si primero se eliminan todos los volúmenes que contiene. Realice los siguientes pasos para eliminar una contenedor de volúmenes.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [administración de volúmenes de StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Obtenga más información sobre el [uso del servicio Administrador de dispositivos de StorSimple para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

