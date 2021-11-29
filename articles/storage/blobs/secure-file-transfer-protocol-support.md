---
title: Compatibilidad con el protocolo de transferencia de archivos seguro (SFTP) en Azure Blob Storage (versión preliminar) | Microsoft Docs
description: El almacenamiento de blobs ahora admite el protocolo de transferencia de archivos segura (SFTP).
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 83a0ffc2b12c884a4d5cba4147cd02365f6fde0a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720518"
---
# <a name="secure-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Compatibilidad con el protocolo de transferencia de archivos seguro (SFTP) en Azure Blob Storage (versión preliminar)

El almacenamiento de blobs ahora admite el protocolo de transferencia de archivos segura (SFTP). Esta compatibilidad proporciona la capacidad de conectarse de forma segura a las cuentas de Blob Storage a través de un punto de conexión de SFTP, lo que le permite aprovechar SFTP para obtener acceso a los archivos, la transferencia de archivos y la administración de archivos.  

> [!IMPORTANT]
> La compatibilidad con SFTP se encuentra actualmente en versión preliminar y está disponible en [estas regiones](secure-file-transfer-protocol-support.md#regional-availability).
>
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
> Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/r/gZguN0j65Y).

Azure permite la transferencia de datos segura a cuentas de Blob Storage mediante la API de REST de Azure Blob service, los SDK de Azure y herramientas como AzCopy. Sin embargo, las cargas de trabajo heredadas suelen usar protocolos de transferencia de archivos tradicionales, como SFTP. Puede actualizar aplicaciones personalizadas para usar la API de REST y los SDK de Azure, pero solo realizando cambios significativos en el código.

Antes del lanzamiento de esta característica, si quería usar SFTP para transferir datos a Azure Blob Storage tenía que comprar un producto de terceros o crear su propia solución. Para ello, tendría que crear una máquina virtual (VM) en Azure para hospedar un servidor SFTP y, a continuación, averiguar una manera de mover datos a la cuenta de almacenamiento. 

Ahora, con la compatibilidad de SFTP para Azure Blob Storage, puede habilitar un punto de conexión SFTP para las cuentas de Blob Storage con una sola configuración. A continuación, puede configurar identidades de usuario locales para la autenticación con el objeto de transferir datos de forma segura sin necesidad de realizar ningún trabajo adicional. 

En este artículo se describe la compatibilidad de SFTP con Azure Blob Storage. Para obtener información sobre cómo habilitar SFTP para la cuenta de almacenamiento, consulte [Conectar a Azure Blob Storage mediante el protocolo de transferencia segura de archivos (versión preliminar)](secure-file-transfer-protocol-support-how-to.md).

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP y el espacio de nombres jerárquico

La compatibilidad con SFTP requiere que los blobs se organicen en un espacio de nombres jerárquico. La capacidad de usar un espacio de nombres jerárquico se introdujo con Azure Data Lake Storage Gen2. Organiza objetos (archivos) en una jerarquía de directorios y subdirectorios de la misma manera en que se organiza el sistema de archivos en el equipo. El espacio de nombres jerárquico se escala linealmente y no degrada el rendimiento ni la capacidad de datos. 

Los diferentes protocolos se extienden desde el espacio de nombres jerárquico. SFTP es uno de estos protocolos disponibles.

> [!div class="mx-imgBorder"]
> ![Espacio de nombres jerárquico](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>Modelo de permisos de SFTP

Azure Storage no admite la firma de acceso compartido (SAS) o la autenticación de Azure Active Directory (Azure AD) para conectar clientes SFTP. En su lugar, los clientes SFTP deben usar una contraseña o una credencial de clave privada de Shell segura (SSH).

Para conceder acceso a un cliente que se conecta, la cuenta de almacenamiento debe tener una identidad asociada a esa credencial. Esa identidad se denomina usuario local. Los usuarios locales son una nueva forma de administración de identidades que se proporciona con la compatibilidad con SFTP. Puede agregar hasta 1000 usuarios locales a una cuenta de almacenamiento.

Para configurar los permisos de acceso, debe crear un usuario local y elegir los métodos de autenticación. A continuación, para cada contenedor de la cuenta, puede especificar el nivel de acceso que quiere proporcionar a ese usuario.
 
> [!NOTE]
> Una vez que los datos se ingieren en Azure Storage, puede usar toda la gama de opciones de seguridad de Azure Storage. Aunque los mecanismos de autorización como el control de acceso basado en roles (RBAC) y las listas de control de acceso no se admiten como medio para autorizar a un cliente SFTP que se conecta, se pueden usar para autorizar el acceso a través de herramientas de Azure (como Azure Portal, la CLI de Azure, los comandos de Azure PowerShell y AzCopy), así como el SDK de Azure y la API de REST de Azure. 
> 
> Para obtener más información, consulte [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

## <a name="authentication-methods"></a>Métodos de autenticación

Puede autenticar un cliente SFTP que se conecta mediante una contraseña o un par de claves pública o privada de Secure Shell (SSH). Puede configurar ambas formas de autenticación y dejar que los clientes que se conectan elijan cuál usar. Sin embargo, no se admite la autenticación multifactor, por lo que son necesarias una contraseña válida y un par de claves pública y privada válidas para realizar una autenticación correcta. 

#### <a name="passwords"></a>Contraseñas

Las contraseñas se generan automáticamente. Si elige la autenticación de contraseña, la contraseña se proporciona una vez que termine de configurar un usuario local. Asegúrese de copiar esa contraseña y guárdela en una ubicación donde pueda encontrarla más adelante. Recuerde que no podrá recuperar esa contraseña de Azure de nuevo. Si pierde la contraseña, tendrá que generar una nueva. Por motivos de seguridad, no puede establecer la contraseña usted mismo.   

#### <a name="ssh-key-pairs"></a>Par de claves SSH

Un par de claves pública y privada es la forma más común de autenticación para Secure Shell (SSH). La clave privada es un secreto y solo la conoce usted. La primera se almacena en Azure. Cuando un cliente SSH se conecta a la cuenta de almacenamiento, envía un mensaje con la clave privada y la firma. Azure valida el mensaje y comprueba que la cuenta de almacenamiento reconoce el usuario y la clave. Para obtener más información, consulte [Información general de SSH y las claves](../../virtual-machines/linux/ssh-from-windows.md#).

Si decide autenticarse con un par de claves pública y privada, puede generar una, usar una ya almacenada en Azure o proporcionar a Azure la clave pública de un par de claves pública-privada existente. 

## <a name="container-permissions"></a>Permisos del contenedor

En la versión actual, solo puede especificar permisos de nivel de contenedor. No se admiten permisos de nivel de directorio. Puede elegir a qué contenedores quiere conceder acceso y qué nivel de acceso quiere proporcionar (lectura, escritura, lista, eliminación y creación). Estos permisos se aplican a todos los directorios y subdirectorios del contenedor. Puede conceder a cada usuario local acceso a hasta 100 contenedores. Los permisos de contenedor también se pueden actualizar después de crear un usuario local. En la tabla siguiente se describe cada permiso con más detalle.

| Permiso | Código de permiso | Descripción |
|---|---|---|
| Leer | r | <li>Lectura de contenidos de archivo</li> |
| Escritura | w | <li>Carga de archivo</li><li>Creación del directorio</li><li>Carga de directorios</li> |
| List | l | <li>Enumeración del contenido del contenedor</li><li>Enumeración del contenido de los directorios</li> |
| Eliminar | d | <li>Eliminación de directorios y archivos</li> |
| Crear | c | <li>Carga de un archivo si este no existe</li><li>Creación del directorio si aún no existe</li><li>Crear directorios</li>|

## <a name="home-directory"></a>Directorio principal

A medida que configura los permisos, tiene la opción de establecer un directorio principal para el usuario local. Si no se especifica ningún otro contenedor en una solicitud de conexión SFTP, este es el directorio al que se conecta el usuario de forma predeterminada. Por ejemplo, considere la siguiente solicitud realizada mediante [Open SSH](/windows-server/administration/openssh/openssh_overview). Esta solicitud no especifica un nombre de contenedor o directorio como parte del comando `sftp`.

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

Si establece el directorio principal de un usuario en `mycontainer/mydirectory`, el cliente se conectará a ese directorio. A continuación, el archivo `logfile.txt` se carga en `mycontainer/mydirectory`. Si no estableció el directorio principal, se producirá un error en el intento de conexión. En su lugar, los clientes que se conectan tendrán que especificar un contenedor junto con la solicitud y, a continuación, usar comandos SFTP para ir al directorio de destino antes de cargar un archivo. Esto se muestra en el ejemplo siguiente:

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos

Consulte el artículo [Problemas conocidos](secure-file-transfer-protocol-known-issues.md) para obtener una lista completa de problemas y limitaciones de la compatibilidad con SFTP.

## <a name="regional-availability"></a>Disponibilidad regional

La compatibilidad con SFTP está disponible en las regiones siguientes: 

- Centro-Norte de EE. UU
- Este de EE. UU. 2
- EUAP de Este de EE. UU. 2
- EUAP del centro de EE. UU.
- Este de Canadá
- Centro de Canadá
- Norte de Europa
- Este de Australia
- Norte de Suiza
- Centro-oeste de Alemania
- Este de Asia
- Centro de Francia

## <a name="pricing-and-billing"></a>Precios y facturación

> [!IMPORTANT]
> Durante la versión preliminar pública, el uso de SFTP no supone ningún cargo adicional. Sin embargo, todavía se aplican los precios estándar de transacción, almacenamiento y red para la cuenta de Azure Data Lake Store Gen2 subyacente. SFTP puede incurrir en cargos adicionales cuando la característica pasa a estar disponible con carácter general.  

Los costos de transacción y almacenamiento se basan en factores como el tipo de cuenta de almacenamiento y el punto de conexión que se usa para transferir datos a la cuenta de almacenamiento. Para obtener más información, consulte [Descripción del modelo de facturación completo de Azure Blob Storage](../common/storage-plan-manage-costs.md#understand-the-full-billing-model-for-azure-blob-storage).

## <a name="see-also"></a>Vea también

- [Conexión a Azure Blob Storage mediante el protocolo de transferencia de archivos segura (SFTP) (versión preliminar)](secure-file-transfer-protocol-support-how-to.md)
- [Problemas conocidos de la compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage (versión preliminar)](secure-file-transfer-protocol-known-issues.md)