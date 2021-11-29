---
title: Conexión a Azure Blob Storage mediante el protocolo SFTP (versión preliminar) | Microsoft Docs
description: Aprenda a habilitar la compatibilidad con SFTP para su cuenta de Azure Blob Storage para que pueda conectarse directamente a su cuenta de Azure Storage mediante un cliente SFTP.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: b6094d86b8f0e2a75d41312b31d1f829d18601b5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720689"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>Conexión a Azure Blob Storage mediante el protocolo de transferencia de archivos segura (SFTP) (versión preliminar)

Puede conectarse de forma segura al punto de conexión de Blob Storage de una cuenta de Azure Storage mediante un cliente SFTP y, a continuación, cargar y descargar archivos. En este artículo se muestra cómo habilitar la compatibilidad con el protocolo SFTP y, a continuación, conectarse a Blob Storage mediante un cliente SFTP. 

Para obtener más información sobre la compatibilidad con el protocolo SFTP en Azure Blob Storage, consulte [Compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage](secure-file-transfer-protocol-support.md).

> [!IMPORTANT]
> La compatibilidad con el protocolo SFTP se encuentra actualmente en versión preliminar y está disponible en [estas regiones](secure-file-transfer-protocol-support.md#regional-availability).
>
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
> Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/r/gZguN0j65Y).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta estándar de almacenamiento de blobs en bloques Premium o v2 de uso general. Para más información sobre estos tipos de cuentas de almacenamiento, consulte [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md).

- La opción de redundancia de cuenta de la cuenta de almacenamiento está configurada para el almacenamiento con redundancia local (LRS) o el almacenamiento con redundancia de zona (ZRS).

- La característica de espacio de nombres jerárquico de la cuenta debe estar habilitada. Para habilitar la característica de espacio de nombres jerárquico, consulte [Actualización de Azure Blob Storage con las funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

- Si se va a conectar desde una red local, asegúrese de que el cliente permite la comunicación saliente a través del puertos 22. El protocolo SFTP usa ese puerto.

## <a name="register-the-feature"></a>Registrar la característica

Antes de poder habilitar la compatibilidad con SFTP, debe registrar la característica SFTP con su suscripción.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Abra la página de configuración de la suscripción.

3. Seleccione **Características en vista previa** en **Configuración**.

   > [!div class="mx-imgBorder"]
   > ![Configuración de la versión preliminar](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. En la página **Características de vista previa**, seleccione la característica **AllowSFTP** y, a continuación, seleccione **Registrarse**.

### <a name="verify-feature-registration"></a>Comprobación del registro de características

Compruebe que la característica está registrada antes de continuar con los demás pasos de este artículo. 

1. Abra la página **Características de la versión preliminar** de la suscripción. 

2. Busque la característica **AllowSFTP** y asegúrese de que aparezca el valor **Registrado** en la columna **Estado**.

## <a name="enable-sftp-support"></a>Habilitación de la compatibilidad con SFTP

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento.

2. En **Configuración**, seleccione **SFTP**.

   > [!NOTE]
   > Esta opción solo aparece si se ha habilitado la característica de espacio de nombres jerárquico de la cuenta. Para habilitar la característica de espacio de nombres jerárquico, consulte [Actualización de Azure Blob Storage con las funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

3. Seleccione **Habilitar SFTP**. 

   > [!div class="mx-imgBorder"]
   > ![Botón para habilitar SFTP](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > Si no aparece ningún usuario local en la página de configuración de SFTP, deberá agregar al menos uno de ellos. Para agregar usuarios locales, consulte la sección siguiente.

## <a name="configure-permissions"></a>Configuración de permisos

Azure Storage no admite la firma de acceso compartido (SAS) o la autenticación de Azure Active Directory (Azure AD) para acceder al punto de conexión de SFTP. En su lugar, debe usar una identidad denominada "usuario local" que se pueda proteger con una contraseña generada por Azure o un par de claves de Secure Shell (SSH). Para conceder acceso a un cliente que se conecta, la cuenta de almacenamiento debe tener una identidad asociada a la contraseña o par de claves. Esa identidad se denomina *usuario local*. 

En esta sección, aprenderá a crear un usuario local, elegir un método de autenticación y, a continuación, asignar permisos para ese usuario local. 

Para obtener más información sobre el modelo de permisos SFTP, consulte [Modelo de permisos de SFTP](secure-file-transfer-protocol-support.md#sftp-permissions-model). 

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento.

2. En **Configuración**, seleccione **SFTP** y luego **Agregar usuario local**. 

   > [!div class="mx-imgBorder"]
   > ![Botón para agregar usuarios locales](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. En el panel de configuración **Agregar usuario local**, agregue el nombre de un usuario y, a continuación, seleccione los métodos de autenticación que quiera asociar a ese usuario local. Puede asociar una contraseña o una clave SSH. 

   > [!IMPORTANT]
   > Aunque puede habilitar ambas formas de autenticación, los clientes SFTP pueden conectarse usando solo uno de ellos. Sin embargo, no se admite la autenticación multifactor, por lo que son necesarias una contraseña válida y un par de claves pública y privada válidas para realizar una autenticación correcta.

   Si selecciona **Proteger con una contraseña**, la contraseña aparecerá cuando haya completado todos los pasos del panel de configuración **Agregar usuario local**.

   Si selecciona **Secure with SSH public key** (Proteger con clave pública SSH), seleccione **Agregar origen de clave** para especificar un origen de clave. 

   > [!div class="mx-imgBorder"]
   > ![Panel configuración de usuario local](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   En la tabla siguiente se describe cada opción de origen de clave:

   | Opción | Guía |
   |----|----|
   | Generación de un nuevo par de claves | Use esta opción para crear un nuevo par de claves pública y privada. La clave pública se almacena en Azure con el nombre de clave que proporcione. La clave privada se puede descargar después de que el usuario local se haya agregado correctamente. |
   | Uso de la clave existente almacenada en Azure | Use esta opción si quiere usar una clave pública que ya esté almacenada en Azure. Para buscar claves existentes en Azure, consulte [Enumeración de claves](../../virtual-machines/ssh-keys-portal.md#list-keys). Cuando los clientes SFTP se conectan a Azure Blob Storage, esos clientes deben proporcionar la clave privada asociada a esta clave pública. |
   | Use la clave pública existente. | Use esta opción si quiere cargar una clave pública almacenada fuera de Azure. Si no tiene una clave pública, pero quiere generar una fuera de Azure, consulte [Generación de claves con ssh-keygen](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen). |

4. Seleccione **Siguiente** para abrir la pestaña **Permisos de contenedor** del panel de configuración.

5. En la pestaña **Permisos de contenedor**, seleccione los contenedores que quiere que estén disponibles para este usuario local. A continuación, seleccione los tipos de operaciones que quiere permitir que realice este usuario local.

   > [!div class="mx-imgBorder"]
   > ![Pestaña de permisos del contenedor](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. En el cuadro de edición **Directorio principal**, escriba el nombre del contenedor o la ruta de acceso del directorio (incluido el nombre del contenedor) que será la ubicación predeterminada asociada a este usuario local. 

   Para obtener más información sobre el directorio principal, consulte [Directorio principal](secure-file-transfer-protocol-support.md#home-directory).

7. Seleccione el **botón Agregar** para agregar el usuario local.

   Si ha habilitado la autenticación de contraseña, la contraseña generada por Azure aparece en un cuadro de diálogo después de que se haya agregado el usuario local. 

   > [!IMPORTANT]
   > Tenga en cuenta que no podrá recuperar esta contraseña más adelante, así que asegúrese de copiarla y, a continuación, almacenarla en un lugar donde pueda encontrarla.

   Si decide generar un nuevo par de claves, se le pedirá que descargue la clave privada de ese par de claves después de que se haya agregado el usuario local.

## <a name="connect-an-sftp-client"></a>Conexión de un cliente SFTP

Puede usar cualquier cliente SFTP para conectarse y transferir archivos de forma segura. En la captura de pantalla siguiente se muestra una sesión de Windows PowerShell que usa la autenticación de [Open SSH](/windows-server/administration/openssh/openssh_overview) y una contraseña para conectarse y, a continuación, cargar un archivo denominado `logfile.txt`.  

> [!div class="mx-imgBorder"]
> ![Conexión con Open SSH](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> Es posible que se le pida que confíe en una clave de host. Durante la versión preliminar pública, las claves de host válidas se publican [aquí](secure-file-transfer-protocol-host-keys.md).  

Una vez completada la transferencia, puede ver y administrar el archivo en Azure Portal. 

> [!div class="mx-imgBorder"]
> ![El archivo cargado aparece en la cuenta de almacenamiento](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Azure Portal usa la API de REST de blob y la API de REST de Data Lake Storage Gen2. La capacidad de interactuar con un archivo cargado en Azure Portal muestra la interoperabilidad entre SFTP y REST.

Consulte la documentación del cliente SFTP para obtener instrucciones sobre cómo conectar y transferir archivos.

## <a name="see-also"></a>Vea también

- [Compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage](secure-file-transfer-protocol-support.md)
- [Problemas conocidos de la compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage](secure-file-transfer-protocol-known-issues.md)