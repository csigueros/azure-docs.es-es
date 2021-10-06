---
title: ¿Qué es Azure AD Connect v2.0? | Microsoft Docs
description: Conozca la próxima versión de Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management, has-adal-ref
ms.openlocfilehash: 5f70c401799fb0b67f9f4013713eb89525072f45
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670735"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Introducción a Azure AD Connect v2.0 

Azure AD Connect se lanzó hace varios años.  Desde ese momento, se ha programado la retirada de varios de los componentes que Azure AD Connect utiliza y se han actualizado a versiones más recientes.  Intentar actualizar todos estos componentes individualmente llevaría mucho tiempo y planeamiento.  

Para solucionarlo, hemos querido agrupar el máximo posible de estos componentes más recientes en una nueva y única versión, para que solo tenga que llevar a cabo una actualización. Esta versión será Azure AD Connect v2.0.  Se trata de una nueva versión del mismo software que usa para lograr sus objetivos de identidad híbrida, pero con los componentes fundamentales más recientes. 

## <a name="what-are-the-major-changes"></a>¿Cuáles son los cambios principales? 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 LocalDB 

Las versiones anteriores de Azure AD Connect incluían SQL Server 2012 LocalDB. La versión 2.0 incluye SQL Server 2019 LocalDB, que ofrece una estabilidad y un rendimiento mejorados y tiene varias correcciones de errores relacionadas con la seguridad. SQL Server 2012 dejará de tener soporte extendido en julio de 2022. Para obtener más información, vea [Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019).

### <a name="msal-authentication-library"></a>Biblioteca de autenticación MSAL 

Las versiones anteriores de Azure AD Connect incluían la biblioteca de autenticación ADAL. Esta biblioteca dejará de utilizarse en junio de 2022. La versión v2.0 incluye la biblioteca más reciente MSAL. Para obtener más información, vea [Introducción a la Biblioteca de autenticación de Microsoft (MSAL)](../../active-directory/develop/msal-overview.md).

### <a name="visual-c-redist-14"></a>Visual C++ Redist 14 

SQL Server 2019 requiere el entorno de ejecución Visual C++ Redist 14, por lo que estamos actualizando la biblioteca en tiempo de ejecución de C++ para que use esta versión. Se instalará con el paquete Azure AD Connect v2.0, por lo que no tiene que hacer nada para actualizar el entorno en tiempo de ejecución de C++. 

### <a name="tls-12"></a>TLS 1.2 

TLS 1.0 y TLS 1.1 son protocolos que Microsoft no considera seguros y está dejando de usarlos. Esta versión de Azure AD Connect solo admitirá TLS 1.2. Todas las versiones de Windows Server compatibles con Azure AD Connect V2.0 ya tienen TLS 1.2 como valor predeterminado. Si el servidor no admite TLS 1.2, deberá habilitarlo para poder implementar Azure AD Connect v2.0. Para obtener más información, consulte [Aplicar TLS 1.2 para Azure AD Connect](reference-connect-tls-enforcement.md).

### <a name="all-binaries-signed-with-sha2"></a>Todos los archivos binarios firmados con SHA2 

Hemos observado que algunos componentes tenían archivos binarios firmados con SHA1. Ya no se admite SHA1 para los archivos binarios descargables y se han actualizado todos a la firma SHA2. Las firmas digitales se usan para asegurarse de que las actualizaciones proceden directamente de Microsoft y no se han alterado durante la entrega. Debido a los puntos débiles del algoritmo SHA-1 y con el fin de cumplir los estándares del sector, hemos cambiado la firma de las actualizaciones de Windows para usar el algoritmo SHA-2, que es más seguro.  

Usted no tiene que hacer nada al respecto. 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Windows Server 2012 y Windows Server 2012 R2 ya no se admiten 

SQL Server 2019 requiere Windows Server 2016 o posterior como sistema operativo de servidor. Puesto que AAD Connect v2 contiene componentes de SQL Server 2019, ya no se admiten versiones anteriores de Windows Server.  

No puede instalar esta versión en una versión anterior de Windows Server. Se recomienda actualizar el servidor de Azure AD Connect a Windows Server 2019, que es la versión más reciente del sistema operativo Windows Server. 

En este [artículo](/windows-server/get-started-19/install-upgrade-migrate-19), se describe la actualización de versiones anteriores de Windows Server a Windows Server 2019. 

### <a name="powershell-50"></a>PowerShell 5.0 

Esta versión de Azure AD Connect contiene varios cmdlets que requieren PowerShell 5.0, por lo que este es un nuevo requisito previo de Azure AD Connect.  

[Aquí](/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements#windows-powershell-50) puede consultar más detalles sobre los requisitos previos de PowerShell.

 >[!NOTE]
 >PowerShell 5 ya forma parte de Windows Server 2016, por lo que, probablemente, no tendrá que hacer nada, siempre y cuando esté usando una versión reciente de Windows Server. 

## <a name="what-else-do-i-need-to-know"></a>¿Qué más necesito saber? 


**¿Por qué es importante esta actualización para mí?** </br>
El año que viene, varios de los componentes de sus instalaciones de servidor de Azure AD Connect actuales dejarán de tener soporte técnico. Si utiliza productos que ya no tienen soporte técnico, será más difícil para nuestro equipo proporcionarle la experiencia de soporte técnico que su organización requiere. Por tanto, se recomienda a todos los clientes que actualicen a esta versión más reciente tan pronto como puedan. 

Esta actualización es especialmente importante, ya que hemos tenido que actualizar los requisitos previos de Azure AD Connect y es posible que necesite más tiempo para planear y actualizar sus servidores a las versiones más recientes de estos requisitos previos. 

**¿Hay alguna funcionalidad nueva que deba conocer?** </br>
No, esta versión no incluye ninguna funcionalidad nueva. Solo contiene actualizaciones de algunos de los componentes fundamentales de Azure AD Connect. 

**¿Se puede actualizar cualquier versión anterior a la versión v2.0?** </br>
Sí, cualquier versión anterior de Azure AD Connect se puede actualizar a Azure AD Connect v2.0. Siga las instrucciones que se dan en este artículo para determinar cuál es la mejor estrategia de actualización para usted. 

**¿Puedo exportar la configuración de mi servidor actual e importarla en Azure AD Connect v2.0?** </br>
Sí, puede hacerlo y es una excelente manera de migrar a Azure AD Connect v2.0, especialmente si también va a actualizar el sistema operativo a una nueva versión. En este [artículo](how-to-connect-import-export-config.md), puede consultar más información sobre la característica de importación y exportación de la configuración y cómo usarla. 

**Tengo habilitadas las actualizaciones automáticas de Azure AD Connect. ¿Obtendré esta nueva versión de forma automática?** </br> No. Azure AD Connect v2.0 no estará disponible para la actualización automática esta vez. 

**Aún no estoy preparado para actualizar. ¿De cuánto tiempo dispongo?** </br>
Debe actualizar a Azure AD Connect v2.0 tan pronto como sea posible. **__Todas las versiones de Azure AD Connect V1 se retirarán el 31 de agosto de 2022.__** Por el momento, seguiremos admitiendo versiones anteriores de Azure AD Connect, pero puede resultar difícil ofrecer una buena experiencia de soporte técnico si algunos de los componentes de Azure AD Connect ya no cuentan con soporte técnico. Esta actualización es especialmente importante para ADAL y TLS 1.0/1.1, ya que estos servicios pueden dejar de funcionar inesperadamente cuando dejen de usarse. 

**Yo utilizo una base de datos SQL externa y no uso SQL 2012 LocalDb. ¿Debo llevar a cabo la actualización igualmente?** </br>
Sí, debe realizar la actualización para mantener la compatibilidad, incluso si no utiliza SQL Server 2012, debido a la retirada de TLS 1.0/1.1 y ADAL. Tenga en cuenta que SQL Server 2012 aún se puede usar como base de datos SQL externa con Azure AD Connect V2.0 (los controladores de SQL 2019 de Azure AD Connect V2.0 son compatibles con SQL Server 2012).

**Después de actualizar mi instancia de Azure AD Connect a V2.0, ¿se desinstalarán automáticamente los componentes de SQL 2012?** </br>
No, la actualización a SQL 2019 no elimina ningún componente de SQL 2012 del servidor. Si ya no necesita estos componentes, debe seguir las [instrucciones de desinstalación de SQL Server](/sql/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup).

**¿Qué ocurre si no actualizo Azure AD Connect?** </br>
Hasta que deje de usarse efectivamente alguno de los componentes que se van a retirar, no notará nada. Azure AD Connect seguirá funcionando. 

La retirada de TLS 1.0/1.1 está prevista para enero de 2022. Debe asegurarse de que no usa estos protocolos en esa fecha, ya que el servicio puede dejar de funcionar de forma inesperada. Sin embargo, puede configurar manualmente el servidor para que use TLS 1.2 y esto no requiere la actualización de Azure AD Connect a la versión v2.0. 

En junio de 2022, ADAL dejará de tener soporte técnico. Cuando finalice el soporte técnico de ADAL, la autenticación puede dejar de funcionar de forma inesperada y esto impedirá que el servidor de Azure AD Connect funcione correctamente. Se recomienda encarecidamente actualizar a Azure AD Connect v2.0 antes de junio de 2022. No puede actualizar a una biblioteca de autenticación admitida con la versión de Azure AD Connect que tiene actualmente. 

**Después de actualizar a la versión 2.0, ¿no funcionan los cmdlets ADSync de PowerShell?** </br>
Se trata de un problema conocido.  Para resolverlo, reinicie la sesión de PowerShell después de instalar o actualizar a la versión 2.0 y, a continuación, vuelva a importar el módulo.  Siga estas instrucciones para importar el módulo:
 
 1.  Abra Windows PowerShell con privilegios de administrador.
 2.  Escriba o copie y pegue lo siguiente: 
    ``` powershell
              Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
    ```
 

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>Requisitos de licencia para usar Azure AD Connect v2.0

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Requisitos de licencia para usar Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Hardware y requisitos previos](how-to-connect-install-prerequisites.md) 
- [Configuración rápida](how-to-connect-install-express.md)
- [Configuración personalizada](how-to-connect-install-custom.md)

En este artículo, se describe la actualización de versiones anteriores de Windows Server a Windows Server 2019.
