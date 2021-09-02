---
title: Exportación de un conector de Microsoft Identity Manager para su uso con el host del conector ECMA de Azure AD
description: Describe cómo crear y exportar un conector desde el servicio de sincronización de MIM para su uso con el host del conector ECMA de Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0731e35eab3a2872cd3c07e5401577fd51c33323
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437303"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-the-azure-ad-ecma-connector-host"></a>Exportación de un conector de Microsoft Identity Manager para su uso con el host del conector ECMA de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

Puede importar en el host del conector ECMA de Azure Active Directory (Azure AD) una configuración para un conector específico desde una instalación del servicio de sincronización de Forefront Identity Manager o del servicio de sincronización de Microsoft Identity Manager (MIM Sync). La instalación de MIM Sync solo se usa para la configuración, no para la sincronización continua desde Azure AD.

>[!IMPORTANT]
>Actualmente, solo se admite el conector SQL genérico para su uso con el host del conector ECMA de Azure AD.

## <a name="create-and-export-a-connector-configuration-in-mim-sync"></a>Creación y exportación de una configuración de conector en MIM Sync
Si ya tiene MIM Sync con el conector ECMA configurado, vaya al paso 10.

 1. Prepare un servidor Windows Server 2016, que es distinto del servidor que se usará para ejecutar el host del conector ECMA de Azure AD. Este servidor host debe tener una base de datos SQL Server 2016 o tener conectividad de red con una base de datos SQL Server 2016. Una manera de configurar este servidor es implementar una máquina virtual de Azure con la imagen **SQL Server 2016 SP1 Standard en Windows Server 2016**. Este servidor no necesita conectividad a Internet que no sea el acceso a Escritorio remoto con fines de configuración.
 1. Cree una cuenta para usarla durante la instalación de MIM Sync. Puede ser una cuenta local de esa instancia de Windows Server. Para crear una cuenta local, abra el **Panel de control** > **Cuentas de usuario** y agregue la cuenta de usuario **mimsync**.
 1. Agregue la cuenta creada en el paso anterior al grupo de administradores local.
 1. Conceda a la cuenta creada anteriormente la capacidad de ejecutar un servicio. Inicie **Directiva de seguridad local** y seleccione **Directivas locales** > **Asignación de derechos de usuario** > **Iniciar sesión como servicio**. Agregue la cuenta mencionada anteriormente.
 1. Instale MIM Sync en este host. Si no tiene los archivos binarios de sincronización de MIM, puede instalar una evaluación; para ello, descargue el archivo ZIP desde el [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=48244), monte la imagen ISO y copie la carpeta **Synchronization Service** (Servicio de sincronización) en el host de Windows Server. A continuación, ejecute el programa de instalación contenido en esa carpeta. El software de evaluación tiene un tiempo limitado y expirará. No está pensado para su uso en producción.
 1. Una vez completada la instalación de MIM Sync, cierre la sesión y vuelva a iniciarla.
 1. Instale el conector en el mismo servidor que MIM Sync. Con fines ilustrativos, esta guía del laboratorio de pruebas ilustrará el uso de uno de los conectores proporcionados por Microsoft para su descarga desde el [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=51495).
 1. Inicie la interfaz de usuario del servicio de sincronización. Seleccione **Management Agents** (Agentes de administración). Seleccione **Create** (Crear) y especifique el agente de administración del conector. Asegúrese de seleccionar un agente de administración de un conector basado en ECMA.
 1. Asigne un nombre al conector y configure los parámetros necesarios para importar y exportar datos al conector. Asegúrese de configurar que el conector pueda importar y exportar atributos de cadena con un solo valor de un tipo de objeto de usuario o persona.
 1. En el equipo del servidor de MIM Sync, inicie la interfaz de usuario del servicio de sincronización, si aún no se está ejecutando. Seleccione **Management Agents** (Agentes de administración).
 1. Seleccione el conector y seleccione **Export Management Agent** (Exportar agente de administración). Guarde el archivo XML, el archivo DLL y el software relacionado del conector en el servidor Windows que va a contener el host del conector ECMA.

En este punto, ya no es necesario el servidor de MIM Sync.

 1. Inicie sesión en el servidor Windows con la cuenta en la que se ejecutará el host del conector ECMA de Azure AD.
 1. Cambie al directorio C:\Archivos de programa\Microsoft ECMA2host\Service\ECMA. Asegúrese de que ya haya uno o varios archivos DLL en ese directorio. Esos archivos DLL corresponden a los conectores entregados por Microsoft.
 1. Copie el archivo DLL de MA del conector y sus archivos DLL de requisitos previos en ese mismo subdirectorio ECMA del directorio del servicio.
 1. Cambie al directorio C:\Archivos de programa\Microsoft ECMA2Host\Wizard. Ejecute el programa Microsoft.ECMA2Host.ConfigWizard.exe para instalar la configuración del host del conector ECMA.
 1. Aparece una nueva ventana con una lista de conectores. De manera predeterminada, no habrá conectores presentes. Seleccione **New connector** (Nuevo conector).
 1. Especifique el archivo XML del agente de administración que se exportó desde MIM Sync anteriormente. Continúe con las instrucciones de configuración y asignación de esquemas de la sección "Configurar un conector".

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
- [Configuración del conector SQL genérico del host del conector ECMA de Azure AD](on-premises-sql-connector-configure.md)
