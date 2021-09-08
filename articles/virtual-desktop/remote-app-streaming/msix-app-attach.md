---
title: Implementación de aplicaciones con la asociación de aplicaciones MSIX para Azure Virtual Desktop - Azure
description: Implementación de aplicaciones con la asociación de aplicaciones MSIX para Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730250"
---
# <a name="deploy-apps-with-msix-app-attach"></a>Implementación de aplicaciones con la asociación de aplicaciones MSIX

Este artículo es un esquema básico de cómo publicar una aplicación en Azure Virtual Desktop con la característica de asociación de aplicaciones MSIX. En este artículo, también le proporcionaremos vínculos a recursos que pueden ofrecer instrucciones y explicaciones más detalladas.

## <a name="what-is-msix-app-attach"></a>¿Qué es la asociación de aplicaciones en formato .MSIX?

La asociación de aplicaciones MSIX es una solución de capas de aplicaciones que permite entregar aplicaciones a sesiones de usuario activas en Azure Virtual Desktop. El sistema de paquetes MSIX separa las aplicaciones del sistema operativo, lo que facilita la creación de imágenes para las máquinas virtuales. Los paquetes MSIX también proporcionan un mayor control sobre las aplicaciones a las que los usuarios pueden acceder en sus máquinas virtuales. Incluso puede separar las aplicaciones de la imagen maestra y proporcionarlas a los usuarios posteriormente.

Para obtener más información, vea [¿Qué es la asociación de aplicaciones en formato .MSIX?](../what-is-app-attach.md).

## <a name="requirements"></a>Requisitos

Necesitará lo siguiente para usar la asociación de aplicaciones en formato MSIX en Azure Virtual Desktop:

- Una aplicación empaquetada en formato MSIX
- Una imagen MSIX realizada a partir de la aplicación MSIX expandida
- Un recurso compartido MSIX, que es la ubicación de red donde se almacenan imágenes MSIX
- Al menos un host de sesión activo y correcto en el grupo de host que usará
- Si la aplicación empaquetada MSIX tiene un certificado privado, ese certificado debe estar disponible en todos los hosts de sesión del grupo hosts.
- Configuración de Azure Virtual Desktop para la asociación de aplicaciones en formato MSIX (asignación de usuarios, asociación de aplicaciones MSIX con grupo de aplicaciones, adición de imágenes MSIX al grupo de hosts)

## <a name="create-an-msix-package-from-an-existing-installer"></a>Creación de un paquete MSIX desde un instalador existente

Para empezar a usar la asociación de aplicaciones en formato MSIX, deberá colocar la aplicación dentro de un paquete MSIX. Algunas aplicaciones ya tienen el formato MSIX, pero si usa un instalador heredado como MSI, ClickOnce o similares, deberá convertir la aplicación al formato de paquete MSIX. Obtenga información sobre cómo convertir las aplicaciones existentes al formato MSIX en el [artículo de información general de MSIX](/windows/msix/packaging-tool/create-an-msix-overview).

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>Prueba de la fidelidad de la aplicación empaquetada 

Después de volver a empaquetar la aplicación como un paquete MSIX, debe asegurarse de que la fidelidad de la aplicación sea alta. La fidelidad de la aplicación es el comportamiento y el rendimiento de la aplicación antes y después de volver a empaquetarla. Un paquete de aplicación con alta fidelidad tiene un rendimiento similar antes y después.

Si detecta que la fidelidad de la aplicación disminuye después de volver a empaquetarla, la organización debe probar la aplicación para asegurarse de que su rendimiento cumple los estándares del usuario. Si no es así, es posible que tenga que actualizar la aplicación para evitar el problema o intentar empaquetarla de nuevo.

## <a name="create-an-msix-image"></a>Creación de una imagen MSIX

Después, deberá crear una imagen MSIX desde la aplicación empaquetada. Una imagen MSIX es lo que sucede cuando se expande un paquete de aplicación MSIX y se almacena la aplicación resultante en un almacenamiento VHD(X) o CIM. Para obtener información sobre cómo crear una imagen MSIX, consulte [Creación de una imagen MSIX](../app-attach-msixmgr.md#create-an-msix-image).

## <a name="configure-an-msix-file-share"></a>Configuración de un recurso compartido MSIX

Después, deberá configurar un recurso compartido de red MSIX para almacenar imágenes MSIX. Una vez configurado, los hosts de sesión usarán el recurso compartido MSIX para adjuntar paquetes MSIX a sesiones de usuario activas, entregando aplicaciones a los usuarios. Obtenga información sobre cómo configurar un recurso compartido MSIX en [Configuración de un recurso compartido de archivos para asociar aplicaciones en formato MSIX](../app-attach-file-share.md).

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>Configuración de implementación de aplicaciones con la asociación de aplicaciones MSIX para Azure Virtual Desktop

Después de cargar una imagen MSIX en el recurso compartido MSIX, deberá abrir Azure Portal y configurar el grupo de hosts que va a usar para aceptar la asociación de aplicaciones MSIX. Obtenga información sobre cómo configurar el grupo de hosts en [Configuración de la asociación de aplicaciones en formato .MSIX con Azure Portal](../app-attach-azure-portal.md).
