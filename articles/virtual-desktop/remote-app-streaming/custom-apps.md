---
title: 'Hospedaje de aplicaciones personalizadas en Azure Virtual Desktop: Azure'
description: Procedimiento para proporcionar aplicaciones personalizadas con Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799208"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>Procedimiento para hospedar aplicaciones personalizadas con Azure Virtual Desktop

Azure Virtual Desktop puede hospedar varios tipos de aplicaciones Windows. Se recomienda preparar las aplicaciones según el tipo de paquetes de la aplicación con los que planee implementarlas. En este artículo, se explicará lo que debe hacer para cada tipo de paquete de la aplicación. 

>[!NOTE]
>Se recomienda hospedar las aplicaciones en un host de varias sesiones. También se recomienda probar las aplicaciones para asegurarse de que se comportan según lo previsto mientras se ejecutan en el host de varias sesiones. Por ejemplo, ejecute una prueba para ver si dos o más usuarios del mismo host de sesión pueden ejecutar correctamente la aplicación al mismo tiempo.

## <a name="msix"></a>MSIX

MSIX es el tipo de paquete recomendado para aplicaciones personalizadas en Azure Virtual Desktop, ya que pueden aprovechar la [característica integrada de conexión de aplicaciones MSIX](../app-attach-glossary.md) del servicio. Para obtener información sobre cómo volver a empaquetar las aplicaciones Win32 existentes en formato MSIX, visite [Volver a empaquetar las aplicaciones Win32 existentes al formato MSIX](/windows/application-management/msix-app-packaging-tool).

Una vez que haya empaquetado la aplicación en formato MSIX, podrá usar la característica de para de aplicaciones MSIX de Azure Virtual Desktop para entregar las aplicaciones a los clientes. Obtenga información sobre cómo usar la conexión de aplicaciones MSIX para las aplicaciones en [Implementación de aplicaciones con la conexión de aplicaciones MSIX](msix-app-attach.md).

## <a name="other-options-for-win32-applications"></a>Otras opciones para aplicaciones Win32

También puede ofrecer aplicaciones Win32 a los usuarios sin volver a empaquetarlas en formato MSIX mediante las siguientes opciones.

### <a name="include-the-application-manually-on-session-hosts"></a>Inclusión manual de la aplicación en los hosts de sesión

Siga las instrucciones de [Preparación y personalización de una imagen de disco duro virtual maestro](../set-up-customize-master-image.md) para incluir una aplicación como parte de la imagen de Windows que usa para las máquinas virtuales. En concreto, siga las instrucciones de la sección [Otras aplicaciones y configuración del Registro](../set-up-customize-master-image.md#other-applications-and-registry-configuration) a fin de instalar la aplicación para todos los usuarios.

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>Uso de Microsoft Endpoint Manager para implementar la aplicación a gran escala

Si usa Microsoft Endpoint Manager para administrar los hosts de sesión, puede implementar aplicaciones mediante las instrucciones de [Instalación de aplicaciones en dispositivos Windows 10](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices). Asegúrese de implementar la aplicación en modo "contexto de dispositivo" en todos los hosts de sesión para asegurarse de que todos los usuarios de la implementación puedan acceder a la aplicación.

### <a name="manual-installation"></a>Instalación manual

No se recomienda instalar aplicaciones manualmente porque tendría que repetir el proceso para cada host de sesión. Este método lo usan con más frecuencia los profesionales de TI con fines de prueba.

Si tiene que instalar las aplicaciones manualmente, tendrá que acceder de forma remota al host de sesión con una cuenta de administrador después de configurar el grupo de hosts de Azure Virtual Desktop. Después, instale la aplicación como lo haría en un equipo físico. Tendrá que repetir este proceso para instalar la aplicación en cada host de sesión del grupo de hosts.

>[!NOTE]
>Si el proceso de instalación le ofrece la opción de instalar la aplicación para todos los usuarios, selecciónela.

## <a name="microsoft-store-applications"></a>Aplicaciones de Microsoft Store

Por el momento no se recomienda usar aplicaciones de Microsoft Store para el streaming de aplicaciones remoto en Azure Virtual Desktop.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo empaquetar e implementar aplicaciones mediante la conexión de aplicaciones MSIX, vea [Implementación de aplicaciones con la conexión de aplicaciones MSIX](msix-app-attach.md).