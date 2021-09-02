---
title: Tutorial de exportación de datos de Azure Data Box | Microsoft Docs
description: Obtenga información acerca de los requisitos previos de implementación y de cómo exportar datos de Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: d9506ce3f01f5500bab81e8e90c57761bef75805
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254582"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Tutorial: Creación de un pedido de exportación para Azure Data Box

Azure Data Box es una solución híbrida que permite mover datos de Azure a su ubicación. En este tutorial se describe cómo crear un pedido de exportación para Azure Data Box. La razón principal para crear un pedido de exportación es la recuperación ante desastres, en caso de que el almacenamiento local se vea comprometido y sea necesario restaurar una copia de seguridad.

En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
>
> * Requisitos previos para la exportación
> * Pedido de un dispositivo Data Box para la exportación
> * Seguimiento del pedido de exportación
> * Cancelación del pedido de exportación

## <a name="prerequisites"></a>Requisitos previos

Antes de pedir el dispositivo, complete los siguientes requisitos previos de configuración tanto del servicio Data Box como del dispositivo.

### <a name="for-service"></a>Para el servicio

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Asegúrese de tener un grupo de recursos existente que pueda utilizar con Azure Data Box.

* Compruebe que la cuenta de Azure Storage de la que desea exportar datos sea de uno de los tipos de cuenta de almacenamiento admitidos, tal y como se describe en el artículo sobre [cuentas de almacenamiento admitidas para Data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Para el dispositivo

Antes de comenzar, asegúrese de que:

* Tiene un equipo host conectado a la red del centro de datos. Copiará los datos de Azure Data Box en este equipo. El equipo host debe ejecutar un sistema operativo compatible, como se describe en [Azure Data Box system requirements](data-box-system-requirements.md) (Requisitos del sistema de Azure Data Box).

* El centro de datos debe tener una red de alta velocidad. Es muy recomendable tener una conexión de 10 GbE como mínimo. Si no hay disponible una conexión de 10 GbE, se puede usar un vínculo de datos de 1 GbE, pero la velocidad de copia resultará afectada.

## <a name="order-data-box-for-export"></a>Pedido de un dispositivo Data Box para exportación

Para solicitar un dispositivo, siga estos pasos en Azure Portal.

1. Use sus credenciales de Microsoft Azure para iniciar sesión en esta dirección URL: [https://portal.azure.com](https://portal.azure.com).

2. Seleccione **+ Crear un recurso** y busque *Azure Data Box*. Seleccione **Azure Data Box**.

   ![Creación de un recurso](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Seleccione **Crear**.

   ![Crear un recurso de Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Compruebe si el servicio Azure Data Box está disponible en su región. Escriba o seleccione la siguiente información y seleccione **Aplicar**.

    |Configuración  |Value  |
    |---------|---------|
    |Tipo de transferencia     | Seleccione **Export to Azure** (Exportar a Azure).        |
    |Suscripción     | Seleccione una suscripción patrocinada por EA, CSP o Azure para el servicio Data Box. <br> La suscripción está vinculada a la cuenta de facturación.       |
    |Resource group     |    Seleccione un grupo de recursos existente. <br> Un grupo de recursos es un contenedor lógico para los recursos que se pueden administrar o implementar conjuntamente.         |
    |Región de Azure de origen    |    Seleccione la región de Azure en la que estén los datos en este momento.         |
    |País de destino     |     Seleccione el país al que desea enviar el dispositivo.        |

   ![Selección de la configuración de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Seleccione **Data Box**. La capacidad máxima utilizable para un solo pedido es de 80 TB. Para tamaños de datos mayores puede crear varios pedidos.

   ![Selección de la capacidad de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. En **Pedido**, especifique los detalles del pedido **Básico**. Escriba o seleccione la siguiente información.

    |Configuración  |Value  |
    |---------|---------|
    |Suscripción     | La suscripción se rellena automáticamente según la selección anterior.|
    |Resource group | Grupo de recursos especificado anteriormente. |
    |Nombre del pedido de exportación     |  Especifique un nombre descriptivo para hacer un seguimiento del pedido. <br> El nombre puede tener entre 3 y 24 caracteres que pueden ser letras, números y guiones. <br> El nombre debe empezar y terminar con una letra o un número.      |

    ![Detalles del pedido de exportación](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Seleccione **Siguiente: Selección de datos** para continuar.

7. En **Selección de datos**, elija **Add storage account and export type** (Agregar cuenta de almacenamiento y tipo de exportación).

    ![Agregar cuenta de almacenamiento y tipo de exportación](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. En **Seleccionar opción de exportación**, especifique los detalles de la opción de exportación. Escriba o seleccione la siguiente información y, luego, elija **Agregar**.

    |Configuración  |Value  |
    |---------|---------|
    |Cuenta de almacenamiento     | Cuenta de Azure Storage desde la que desea exportar los datos. |
    |Tipo de exportación     | Especifica el tipo de datos que se va a exportar de **Todos los objetos** y **Usar archivo XML**.<ul><li> **Todos los objetos**: especifica que el trabajo exporta todos los datos en función de la selección de **Opciones de transferencia**.</li><li> **Usar archivo XML**: especifica un archivo XML que contiene un conjunto de rutas de acceso y prefijos para los blobs o archivos que se van a exportar desde la cuenta de almacenamiento. El archivo XML debe estar en el contenedor seleccionado de la cuenta de almacenamiento; actualmente no se admite la selección desde recursos compartidos de archivos. Debe ser un archivo .xml que no esté vacío.</li></ul>        |
    |Opciones de transferencia     |  Especifica las opciones de transferencia de datos de **Seleccionar todos**, **Todos los blobs** y **Todos los archivos**. <ul><li> **Seleccionar todos**: especifica que se exportarán todos los blobs y archivos de Azure Files. Si está utilizando una cuenta de almacenamiento que solo admite blobs (cuenta de Blob Storage), no se podrá seleccionar la opción **Todos los archivos**.</li><li> **Todos los blobs**: especifica que solo se exportan los blobs en bloques y en páginas.</li><li> **Todos los archivos**: especifica que se exportan todos los archivos, pero quedan excluidos los blobs. El tipo de cuenta de almacenamiento que tenga (GPv1 y GPv2, Premium Storage o Blob Storage) determinará los tipos de datos que se pueden exportar. Para más información, consulte las [cuentas de almacenamiento admitidas para exportación](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Incluir registro detallado     | Indica si desea un archivo de registro detallado que contenga una lista de todos los archivos que se han exportado correctamente.        |

    > [!NOTE]
    >
    > Si selecciona **Usar archivo XML** para el valor **Tipo de exportación**, debe asegurarse de que el archivo XML contenga rutas de acceso o prefijos válidos. Debe construir y proporcionar el archivo XML.  Si el archivo no es válido o no hay ningún dato que coincida con las rutas de acceso especificadas, el pedido finaliza con una exportación de datos parcial o sin datos exportados.

    Para ver cómo agregar un archivo XML a un contenedor, consulte [Pedido de exportación mediante un archivo XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Selección de opción de exportación](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Para ver un ejemplo de la entrada XML, consulte [Entrada XML de ejemplo](data-box-deploy-export-ordered.md#sample-xml-file).

9. En **Selección de datos**, revise la configuración y seleccione **Siguiente: Seguridad>** para continuar.

   ![Selección de datos para la exportación de pedido](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    La pantalla **Seguridad** le permite usar su propia clave de cifrado y elegir usar el cifrado doble.

    Todos los valores de la pantalla **Security** (Seguridad) son opcionales. Si no cambia ninguna configuración, se aplicará la configuración predeterminada.

    ![Pantalla de seguridad del Asistente para pedidos de importación de Data Box](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Si quiere usar su propia clave administrada por el cliente para proteger la clave de paso de desbloqueo del nuevo recurso, expanda **Tipo de cifrado**.

    La configuración de una clave administrada por el cliente para Azure Data Box es opcional. De manera predeterminada, Data Box usa una clave administrada por Microsoft para proteger la clave de paso de desbloqueo.

    Una clave administrada por el cliente no afecta a cómo se cifran los datos del dispositivo. La clave solo se usa para cifrar la clave de paso de desbloqueo del dispositivo.

    Si no quiere usar una clave administrada por el cliente, vaya al paso 16.

    ![Pantalla de seguridad que muestra la configuración Tipo de cifrado](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Seleccione **Clave administrada por el cliente** como tipo de clave. Elija **Seleccione un almacén de claves y una clave**.
   
    ![Pantalla de seguridad, configuración de una clave administrada por el cliente](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. En la pantalla **Seleccionar clave en Azure Key Vault**, la suscripción se rellena automáticamente.

    - Para **Almacén de claves**, puede seleccionar un almacén de claves existente de la lista desplegable.

      ![Pantalla Seleccione clave de Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - También puede seleccionar **Crear nuevo** para crear un nuevo almacén de claves. En la pantalla **Crear almacén de claves**, indique el grupo de recursos y el nombre del almacén de claves. Asegúrese de que estén habilitadas las opciones **Eliminación temporal** y **Protección de purga**. Acepte los restantes valores predeterminados y seleccione **Revisar y crear**.

      ![Configuración de la creación de un nuevo almacén de claves](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Revise la información del almacén de claves y seleccione **Crear**. Espere un par de minutos hasta que se complete la creación del almacén de claves.

      ![Pantalla Nueva revisión de Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. En la pantalla **Seleccionar clave en Azure Key Vault**, puede seleccionar una clave existente del almacén de claves.

    ![Selección de una clave existente de Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Si quiere crear una clave nueva, seleccione **Crear nuevo**. Debe usar una clave RSA. El tamaño puede ser de 2048 o superior. Escriba un nombre para la clave nueva, acepte los otros valores predeterminados y seleccione **Crear**.

      ![Opción para crear una nueva clave](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Recibirá una notificación cuando se haya creado la clave en el almacén de claves.

14. Seleccione el valor de **Versión** de la clave que va a usar y, a continuación, elija **Seleccionar**.

      ![Nueva clave creada en el almacén de claves](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Si desea crear una nueva versión de la clave, seleccione **Crear nuevo**.

    ![Abrir un cuadro de diálogo para crear una nueva versión de la clave](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    En la pantalla **Crear nueva clave**, elija Configuración para la nueva versión de la clave y seleccione **Crear**.

    ![Creación de una nueva versión de la clave](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    La configuración de **Tipo de cifrado** en la pantalla **Seguridad** muestra el almacén de claves y la clave.

    ![Clave y almacén de claves de una clave administrada por el cliente](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Seleccione una identidad de usuario que vaya a usar para administrar el acceso a este recurso. Elija **Select a user identity** (Seleccione una identidad de usuario). En el panel de la derecha, seleccione la suscripción y la identidad administrada que se va a usar. Luego, elija **Seleccionar**.

    Una identidad administrada asignada por el usuario es un recurso independiente de Azure que se puede usar para administrar varios recursos. Para más información, consulte [Tipos de identidad administrada](../active-directory/managed-identities-azure-resources/overview.md).  

    Si necesita crear una identidad administrada, siga la guía que se proporciona en [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Seleccionar una identidad de usuario](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    La identidad del usuario se muestra en la configuración **Tipo de cifrado**.

    Ahora puede contraer la configuración **Tipo de cifrado**.

    ![Se muestra una identidad de usuario en la configuración Tipo de cifrado](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Si quiere habilitar el cifrado doble basado en software, expanda **Cifrado doble (para entornos de seguridad)** y seleccione **Habilitar el cifrado doble para el pedido**. 

    El cifrado basado en software se realiza junto con el cifrado AES de 256 bits de los datos en Data Box.

    > [!NOTE]
    > La habilitación de esta opción puede hacer que el procesamiento de pedidos y la copia de datos tarden más. Esta opción no se puede cambiar después de crear el pedido.

    ![Pantalla de seguridad para la importación de Data Box, cifrado doble](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Seleccione **Siguiente: Detalles de contacto** para continuar.

11. En **Detalles de contacto**, seleccione **+ Agregar dirección de envío** para especificar la información de envío.

    ![Agregar dirección de envío](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. En **Agregar dirección de envío**, escriba su nombre y apellidos, el nombre y la dirección postal de la empresa, y un número de teléfono válido. Seleccione **Validar**. El servicio valida la dirección de envío para conocer la disponibilidad del servicio. Si el servicio está disponible para la dirección de envío especificada, recibirá una notificación al respecto.

    ![Validación de la dirección de envío](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Si va a realizar un pedido en una región en la que el envío autoadministrado esté disponible, puede seleccionar esta opción. Para obtener más información sobre el envío autoadministrado, consulte [Uso del envío autoadministrado](data-box-portal-customer-managed-shipping.md).

13. Seleccione **Agregar dirección de envío** una vez que se haya comprobado que los detalles sean correctos.

14. En **Detalles de contacto**, revise la dirección de envío y la dirección de correo electrónico. El servicio envía notificaciones por correo electrónico si se produce cualquier actualización en el estado del pedido a las direcciones de correo electrónico especificadas.

    Es aconsejable usar un correo electrónico de grupo, con el fin de que siga recibiendo notificaciones aunque algún administrador deje el grupo.

    ![Detalles de contacto](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Seleccione **Siguiente: Revisar y pedir>** . Debe aceptar los términos y condiciones para continuar con la creación del pedido.

16. Seleccione **Pedido**. El pedido tarda unos minutos en crearse.

    ![Confirmación del pedido](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Pedido de exportación mediante un archivo XML

Si selecciona **Usar archivo XML**, puede especificar los contenedores y blobs concretos (página y bloque) que desee exportar. Tendrá que seguir las especificaciones de la [tabla del archivo XML de ejemplo](#sample-xml-file) para dar formato al archivo XML. En los pasos siguientes se muestra cómo utilizar un archivo XML para exportar los datos:

1. En **Tipo de exportación**, seleccione **Usar archivo XML**. Este es el archivo XML que especifica los blobs y archivos de Azure concretos que quiere exportar. Para agregar el archivo XML, seleccione **Haga clic aquí para seleccionar un archivo XML**.

     ![Selección de opción de exportación, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Seleccione **+ Contenedor** para crear un contenedor.

    ![Selección de opción de exportación, contenedores](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. En la pestaña **Nuevo contenedor** que aparece en el lado derecho de Azure Portal, agregue un nombre para el contenedor. El nombre debe estar en minúsculas y puede incluir números y guiones "-". A continuación, seleccione **Nivel de acceso público** en el cuadro de lista desplegable. Se recomienda elegir **Private (non anonymous access)** [Privado (acceso no anónimo)] para evitar que otros usuarios tengan acceso a los datos. Para más información sobre los niveles de acceso a los contenedores, consulte [Permisos de acceso a contenedores](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Selección de opción de exportación, configuración de un nuevo contenedor](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Seleccione **Crear**.

   ![Selección de opción de exportación, crear un nuevo contenedor.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Si el contenedor se crea correctamente, recibirá el mensaje siguiente:

   ![Contenedor creado correctamente](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Seleccione el contenedor que haya creado y haga doble clic en él.

   ![Ver los detalles del contenedor](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Al hacer doble clic en el contenedor, se abrirá la vista de propiedades del contenedor. Ahora deberá asociar (o desplazarse hasta) el archivo XML que contiene la lista de blobs o archivos de Azure Files que quiere exportar. Seleccione **Cargar**.

   ![Carga del blob en el contenedor](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Ha agregado correctamente el archivo XML al contenedor. Solo se exportarán los blobs y los archivos de Azure Files especificados en este archivo XML.

   ![Archivo XML agregado al contenedor](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Seguimiento del pedido

Tras realizar el pedido, puede hacer un seguimiento del estado del mismo desde Azure Portal. Vaya al pedido de Data Box y, después, vaya a **Información general** para ver el estado. El portal muestra el pedido en estado **Ordered** (Pedido).

Una vez completada la preparación del dispositivo, comenzará la copia de datos desde las cuentas de almacenamiento seleccionadas. El portal muestra el pedido en el estado **Copia de datos en curso**.

![Pedido de exportación de Data Box, copia de datos en curso](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box copia los datos de las cuentas de almacenamiento de origen. Una vez completada la copia de datos, Data Box queda bloqueado y el portal mostrará el pedido en el estado **copia completada**.

![Pedido de exportación de Data Box, copia de datos completada](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

La exportación de datos de Azure Storage a Data Box puede producir un error. Asegúrese de que los blobs no son blobs de archivo, ya que no se admite la exportación de estos blobs. 

> [!NOTE]
> En el caso de los blobs de archivo, debe rehidratar esos blobs antes de que se puedan exportar de la cuenta de Azure Storage a la instancia de Data Box. Para más información, consulte [Rehidratación de un blob de archivo]( ../storage/blobs/storage-blob-rehydration.md).

Si el dispositivo no está disponible, recibirá una notificación. Si el dispositivo está disponible, Microsoft identifica que está listo para el envío y prepara dicho envío. Durante la preparación del dispositivo, se producen las siguientes acciones:

* Se crean recursos compartidos de SMB para cada cuenta de almacenamiento asociada con el dispositivo.
* Para cada cuenta, se generan las credenciales de acceso, como el nombre de usuario y la contraseña.
* El dispositivo está bloqueado y solo se puede acceder a él mediante la contraseña de desbloqueo del dispositivo. Para recuperar la contraseña, debe iniciar sesión en la cuenta de Azure Portal y seleccionar **Detalles del dispositivo**.

Luego, Microsoft prepara y envía el dispositivo a través de un operador regional. Una vez que se realiza el envío, recibe un número de seguimiento. El portal muestra el orden en estado **Dispatched** (Enviado).

![Pedido de exportación de Data Box enviado](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Si se ha seleccionado el envío autoadministrado, recibirá una notificación por correo electrónico con los pasos siguientes cuando el dispositivo esté preparado para su recogida en el centro de datos. Para más información sobre el envío autoadministrado, consulte [Envío autoadministrado](data-box-portal-customer-managed-shipping.md).

![Envío autoadministrado preparado para su recogida](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Cancelar el pedido

Para cancelar el pedido, en Azure Portal, vaya a **Información general** y seleccione **Cancelar** en la barra de comandos.

Después de realizar un pedido, puede cancelarlo en cualquier momento antes de que se inicie su procesamiento.

Para eliminar un pedido cancelado, vaya a **Información general** y seleccione **Eliminar** en la barra de comandos.

## <a name="sample-xml-file"></a>Archivo XML de ejemplo

En el archivo XML siguiente se muestra un ejemplo de nombres de blob, prefijos de blob y archivos de Azure Files contenidos en el formato XML que el pedido de exportación usa cuando se selecciona la opción **Usar archivo XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure Files for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Algunos aspectos importantes en relación con los archivos XML:

* Las etiquetas XML distinguen las mayúsculas de las minúsculas y deben coincidir exactamente, tal y como se ha especificado en el ejemplo anterior.
* Las etiquetas de apertura y de cierre deben coincidir.
* Si el formato o las etiquetas XML no son correctos, pueden provocar un error de exportación de datos.
* No se exportará ningún dato si el prefijo de blob o de archivo no es válido.

### <a name="examples-of-valid-blob-paths"></a>Ejemplos de rutas de acceso del blob válidas

En la siguiente tabla se muestran ejemplos de rutas de acceso del blob válidas:

   | Selector | Ruta del blob | Descripción |
   | --- | --- | --- |
   | Empieza por |/ |Exporta todos los blobs de la cuenta de almacenamiento. |
   | Empieza por |/$root/ |Exporta todos los blobs del contenedor raíz. |
   | Empieza por |/containers |Exporta todos los blobs de cualquier contenedor que empiece por el prefijo **containers**. |
   | Empieza por |/container-name/ |Exporta todos los blobs del contenedor **container-name**. |
   | Empieza por |/container-name/prefix |Exporta todos los blobs del contenedor **container-name** que empiecen por el prefijo **prefix**. |
   | Igual a |$root/logo.bmp |Exporta el blob **logo.bmp** del contenedor raíz. |
   | Igual a |8tbpageblob/mydata.txt |Exporta el blob **mydata.txt** del contenedor **8tbpageblob**. |

## <a name="sample-log-files"></a>Archivos de registro de ejemplo

En esta sección se proporcionan archivos de registro de ejemplo que se generan durante la exportación. Los registros de errores se generan automáticamente. Para generar el archivo de registro detallado, debe seleccionar **Incluir registro detallado** en Azure Portal al configurar el pedido de exportación.
Para más información sobre los registros de copia y detallado, consulte el artículo sobre los [registros de copia](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Azure Data Box, como:

> [!div class="checklist"]
>
> * Requisitos previos para la exportación
> * Pedido de un dispositivo Data Box para la exportación
> * Seguimiento del pedido de exportación
> * Cancelación del pedido de exportación

En el siguiente tutorial aprenderá a configurar Data Box.

> [!div class="nextstepaction"]
> [Configuración de Azure Data Box](./data-box-deploy-set-up.md)
