---
title: Multi-Cloud Scanning Connector de Amazon S3 para Azure Purview
description: En esta guía paso a paso se describen los detalles sobre cómo examinar cubos de Amazon S3 en Azure Purview.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: 738ee7b01831574af32c44dd00c8b1d9b56513c3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210161"
---
# <a name="amazon-s3-multi-cloud-scanning-connector-for-azure-purview"></a>Multi-Cloud Scanning Connector de Amazon S3 para Azure Purview

Multi-Cloud Scanning Connector para Azure Purview permite explorar los datos de la organización entre proveedores de nube, incluidos Amazon Web Services además de los servicios de almacenamiento de Azure.

En este artículo se describe cómo usar Azure Purview para examinar los datos no estructurados almacenados actualmente en cubos estándar de Amazon S3 y detectar qué tipos de información confidencial existen en los datos. En esta guía de procedimientos también se describe cómo identificar los cubos de Amazon S3 en los que los datos están almacenados actualmente para facilitar la protección de la información y el cumplimiento de datos.

Para este servicio, use Purview a fin de proporcionar una cuenta Microsoft con acceso seguro a AWS, donde se ejecutará Multi-Cloud Scanning Connector para Azure Purview. Multi-Cloud Scanning Connector para Azure Purview usa este acceso a los cubos de Amazon S3 para leer los datos y, después, notifica a Azure los resultados del examen, solo con los metadatos y la clasificación. Use los informes de clasificación y etiquetado de Purview para analizar y revisar los resultados del examen de los datos.

> [!IMPORTANT]
> Multi-Cloud Scanning Connector para Azure Purview es un complemento independiente de Azure Purview. Los términos y condiciones de Multi-Cloud Scanning Connector para Azure Purview se incluyen en el contrato por el que ha obtenido Microsoft Azure Services. Para obtener más información, vea Información legal de Microsoft Azure en https://azure.microsoft.com/support/legal/.
>

## <a name="purview-scope-for-amazon-s3"></a>Ámbito de Purview para Amazon S3

Para obtener información sobre los límites de Purview, consulte:

- [Administración y aumento de las cuotas de los recursos con Azure Purview](how-to-manage-quotas.md)
- [Tipos de archivo y orígenes de datos admitidos en Azure Purview](sources-and-scans.md)
- [Uso de puntos de conexión privados para la cuenta de Purview](catalog-private-link.md)

### <a name="storage-and-scanning-regions"></a>Regiones de almacenamiento y examen

El conector de Purview para el servicio Amazon S3 está implementado actualmente solo en regiones específicas. En la tabla siguiente se asignan las regiones en las que se almacenan los datos a la región en la que se examinarán con Azure Purview.

> [!IMPORTANT]
> A los clientes se les cobrará por todos los cargos de transferencia de datos relacionados en función de la región de su cubo.
>

| Región de almacenamiento | Región de examen |
| ------------------------------- | ------------------------------------- |
| Este de EE. UU. (Ohio)                  | Este de EE. UU. (Ohio)                        |
| Este de EE. UU. (N. Virginia)           | Este de EE. UU. (N. Virginia)                       |
| Oeste de EE. UU. (N. California)         | Oeste de EE. UU. (N. California)                        |
| Oeste de EE. UU. (Oregon)                | Oeste de EE. UU. (Oregon)                      |
| África (Ciudad del cabo)              | Europa (Frankfurt)                    |
| Asia Pacífico (Hong Kong)        | Asia Pacífico (Tokio)                |
| Asia Pacífico (Mumbai)           | Asia Pacífico (Singapur)                |
| Asia Pacífico (Osaka-local)      | Asia Pacífico (Tokio)                 |
| Asia Pacífico (Seúl)            | Asia Pacífico (Tokio)                 |
| Asia Pacífico (Singapur)        | Asia Pacífico (Singapur)                 |
| Asia Pacífico (Sídney)           | Asia Pacífico (Sídney)                  |
| Asia Pacífico (Tokio)            | Asia Pacífico (Tokio)                |
| Canada (Central)                | Este de EE. UU. (Ohio)                        |
| China (Pekín)                 | No compatible                    |
| China (Ningxia)                 | No compatible                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irlanda)                | Europa (Irlanda)                   |
| Europa (Londres)                 | Europa (Londres)                 |
| Europa (Milán)                  | Europa (París)                    |
| Europa (París)                  | Europa (París)                   |
| Europa (Estocolmo)              | Europa (Frankfurt)                    |
| Oriente Medio (Bahrein)           | Europa (Frankfurt)                    |
| Sudamérica (Sao Paulo)       | Este de EE. UU. (Ohio)                        |
| | |

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de que ha completado los siguientes requisitos previos antes de agregar los cubos de Amazon S3 como orígenes de datos de Purview y examinar los datos de S3.

> [!div class="checklist"]
> * Tenga en cuenta que debe ser Administrador de orígenes de datos de Azure Purview.
> * [Cree una cuenta de Purview](#create-a-purview-account) si aún no tiene una.
> * [Cree un nuevo rol de AWS para usar con Purview](#create-a-new-aws-role-for-purview).
> * [Creación de una credencial de Purview para el examen del cubo de AWS](#create-a-purview-credential-for-your-aws-s3-scan)
> * [Configure el examen de cubos de Amazon S3 cifrados](#configure-scanning-for-encrypted-amazon-s3-buckets), si corresponde.
> * Al agregar los cubos como recursos de Purview, necesitará los valores del [ARN de AWS](#retrieve-your-new-role-arn), el [nombre de cubo](#retrieve-your-amazon-s3-bucket-name) y, en ocasiones, el [identificador de cuenta de AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Creación de una cuenta de Purview

- **Si ya tiene una cuenta de Purview,** puede continuar con las configuraciones necesarias para la compatibilidad con AWS S3. Comience por [Creación de una credencial de Purview para el examen del cubo de AWS](#create-a-purview-credential-for-your-aws-s3-scan).

- **Si necesita crear una cuenta de Purview,** siga las instrucciones descritas en [Creación de una cuenta de Azure Purview en Azure Portal](create-catalog-portal.md). Después de crear la cuenta, vuelva aquí para completar la configuración y comenzar a usar el conector de Purview para Amazon S3.

### <a name="create-a-new-aws-role-for-purview"></a>Creación de un nuevo rol de AWS para Purview

En este procedimiento se describe cómo buscar los valores de un identificador de cuenta de Azure y de un identificador externo, crear un rol de AWS y, después, escribir el valor del ARN de rol en Purview.

**Para buscar el identificador de la cuenta Microsoft y el identificador externo**:

1. En Purview, vaya a **Management Center** > **Security and access** > **Credentials** (Centro de administración > Seguridad y acceso > Credenciales).

1. Seleccione **New** (Nueva) para crear una credencial.

    En el panel **New credential** (Nueva credencial) que aparece a la derecha, copie los valores de **identificador de cuenta Microsoft** y de **identificador externo** en un archivo independiente, o bien téngalos a mano para pegarlos en el campo correspondiente de AWS.

    Por ejemplo:

    [![Busque los valores de identificador de la cuenta Microsoft e identificador externo.](./media/register-scan-amazon-s3/locate-account-id-external-id.png) ](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Para crear el rol de AWS para Purview**:

1.  Abra la consola de **Amazon Web Services** y, en **Security, Identity, and Compliance** (Seguridad, identidad y cumplimiento), seleccione **IAM**.

1. Seleccione **Roles** (Roles) y **Create role** (Crear rol).

1. Seleccione **Another AWS account** (Otra cuenta de AWS) y escriba los siguientes valores:

    |Campo  |Descripción  |
    |---------|---------|
    |**Id. de cuenta**     |    Escriba el identificador de la cuenta de Microsoft. Por ejemplo: `181328463391`     |
    |**Id. externo**     |   En opciones, seleccione **Require external ID...** (Requerir id. externo...) y, a continuación, escriba el identificador externo en el campo designado. <br>Por ejemplo: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Por ejemplo:

    ![Agregue el identificador de cuenta de Microsoft a la cuenta de AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. En el área **Create role > Attach permissions policies** (Crear rol > Asociar directiva de permisos), filtre los permisos que se muestran por **S3**. Seleccione **AmazonS3ReadOnlyAccess** y, a continuación, seleccione **Next: Tags** (Siguiente: Etiquetas).

    ![Seleccione la directiva ReadOnlyAccess para el nuevo rol de examen de Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > La directiva **AmazonS3ReadOnlyAccess** brinda los permisos mínimos necesarios para examinar los cubos S3, y también puede incluir otros permisos.
    >
    >Para aplicar solo los permisos mínimos necesarios para examinar los cubos, cree una nueva directiva con los permisos enumerados en [Permisos mínimos para la directiva de AWS](#minimum-permissions-for-your-aws-policy), en función de si quiere examinar un único cubo o todos los cubos de la cuenta. 
    >
    >Aplique la nueva directiva al rol en lugar de a **AmazonS3ReadOnlyAccess**.

1. En el área **Add tags (optional)** (Agregar etiquetas [opcional]), puede optar por crear una etiqueta descriptiva para este nuevo rol. Las etiquetas útiles permiten organizar, realizar un seguimiento y controlar el acceso de cada rol que se crea.

    Escriba una nueva clave y un valor para la etiqueta según sea necesario. Cuando haya terminado, o si desea omitir este paso, seleccione **Next: Review** (Siguiente: Revisar) para revisar los detalles del rol y completar la creación del rol.

    ![Agregue una etiqueta descriptiva para organizar, realizar un seguimiento o controlar el acceso del nuevo rol.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. En el área **Review** (Revisar), siga estos pasos:

    - En el campo **Role name** (Nombre de rol), escriba un nombre descriptivo para el rol.
    - En el cuadro **Role description** (Descripción de rol), escriba una descripción opcional para identificar el propósito del rol.
    - En la sección **Policies** (Directivas), confirme que está asociada al rol la directiva correcta (**AmazonS3ReadOnlyAccess**).

    Seleccione **Create role** (Crear rol) para completar el proceso.

    Por ejemplo:

    ![Revise los detalles antes de crear el rol.](./media/register-scan-amazon-s3/review-role.png)


### <a name="create-a-purview-credential-for-your-aws-s3-scan"></a>Creación de una credencial de Purview para un examen AWS S3

En este procedimiento se describe cómo crear una nueva credencial de Purview para usarla al examinar los cubos de AWS.

> [!TIP]
> Si continúa directamente desde [Creación de un nuevo rol de AWS para Purview](#create-a-new-aws-role-for-purview),es posible que ya tenga abierto el panel **New credential** (Nueva credencial) en Purview.
>
> También puede crear una nueva credencial en medio del proceso, durante la [configuración del examen](#create-a-scan-for-one-or-more-amazon-s3-buckets). En ese caso, en el campo **Credential** (Credencial), seleccione **New** (Nuevo).
>

1. En Purview, vaya a **Management Center** (Centro de administración) y, en **Security and access** (Seguridad y acceso), seleccione **Credentials** (Credenciales).

1. Seleccione **New** (Nuevo) y, en el panel **New credential** (Nueva credencial) que aparece a la derecha, use los campos siguientes para crear la credencial de Purview:

    |Campo |Descripción  |
    |---------|---------|
    |**Nombre**     |Escriba un nombre descriptivo para esta credencial.        |
    |**Descripción**     |Escriba una descripción opcional para esta credencial, como `Used to scan the tutorial S3 buckets`.         |
    |**Método de autenticación**     |Seleccione **Role ARN** (ARN de rol), ya que va a usar un ARN de rol para acceder al cubo.         |
    |**Id. de cuenta Microsoft**     |Selecciónelo para copiar este valor en el Portapapeles. Use este valor para **Microsoft account ID** (Id. de cuenta Microsoft) al [crear el ARN de rol en AWS](#create-a-new-aws-role-for-purview).           |
    |**Id. externo**     |Selecciónelo para copiar este valor en el Portapapeles. Use este valor para **External ID** (Id. externo) al [crear el ARN de rol en AWS](#create-a-new-aws-role-for-purview).        |
    |**ARN de rol**     | Una vez que haya [creado el rol de IAM de Amazon](#create-a-new-aws-role-for-purview), vaya al mismo en el área IAM de AWS, copie el valor de **ARN de rol** y escríbalo aquí. Por ejemplo: `arn:aws:iam::181328463391:role/S3Role`. <br><br>Para más información, consulte [Recuperación del nuevo ARN de rol](#retrieve-your-new-role-arn). |
    | | |

    Seleccione **Create** (Crear) cuando termine de crear la credencial.

1. Si aún no lo ha hecho, copie y pegue los valores del **identificador de la cuenta Microsoft** y del **identificador externo** para usarlos al [crear un rol de AWS para Purview](#create-a-new-aws-role-for-purview), que es el siguiente paso.

Para más información sobre las credenciales de Purview, consulte [Credenciales para la autenticación de origen en Azure Purview](manage-credentials.md).


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Configuración del examen de cubos de Amazon S3 cifrados

Los cubos de AWS admiten varios tipos de cifrado. Para los cubos que usan el cifrado **AWS-KMS**, se requiere una configuración especial para habilitar el examen.

> [!NOTE]
> En el caso de cubos que no usan cifrado o usan cifrado AES-256 o AWS-KMS S3, omita esta sección y continúe en [Recuperación del nombre del cubo de Amazon S3](#retrieve-your-amazon-s3-bucket-name).
>

**Para comprobar el tipo de cifrado que se usa en los cubos de Amazon S3:**

1. En AWS, vaya a **Storage** > **S3** (Almacenamiento > S3) y seleccione **Buckets** (Cubos) en e menú de la izquierda.

    ![Seleccione la pestaña de cubos de Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Seleccione el cubo que desea comprobar. En la página de detalles del cubo, seleccione la pestaña **Properties** (Propiedades) y desplácese hacia abajo hasta el área **Default encryption** (Cifrado predeterminado).

    - Si el cubo que ha seleccionado tiene una configuración que no sea el cifrado **AWS-KMS**, incluido si el cifrado predeterminado para el cubo está **deshabilitado**, omita el resto de este procedimiento y continúe en [Recuperación del nombre del cubo de Amazon S3](#retrieve-your-amazon-s3-bucket-name).

    - Si el cubo seleccionado está configurado para el cifrado **AWS-KMS**, continúe como se describe a continuación para agregar una nueva directiva que permita el examen de un cubo con cifrado **AWS-KMS** personalizado.

    Por ejemplo:

    ![Visualización de un cubo de Amazon S3 configurado con cifrado AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Para agregar una nueva directiva que permita el examen de un cubo con cifrado AWS-KMS personalizado:**

1. En AWS, vaya a **Services** >  **IAM** >  **Policies** (Servicios > IAM > Directivas) y seleccione **Create policy** (Crear directiva).

1. En la pestaña **Create policy** > **Visual editor** (Crear directiva > Editor visual), defina la directiva con los siguientes valores:

    |Campo  |Descripción  |
    |---------|---------|
    |**Servicio**     |  Escriba y seleccione **KMS**.       |
    |**Acciones**     | En **Access level** (Nivel de acceso), seleccione **Write** (Escritura) para expandir la sección **Write** (Escritura).<br>Una vez expandida, seleccione solo la opción **Decrypt** (Descifrar).        |
    |**Recursos**     |Seleccione un recurso específico o **All resources** (Todos los recursos).         |
    | | |

    Cuando haya terminado, seleccione **Review policy** (Revisar directiva) para continuar.

    ![Cree una directiva para examinar un cubo con cifrado AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. En la página **Review policy** (Revisar directiva), escriba un nombre descriptivo para la directiva y una descripción opcional y, a continuación, seleccione **Create policy** (Crear directiva).

    La directiva recién creada se agrega a la lista de directivas.

1. Adjunte la nueva directiva al rol que ha agregado para el examen.

    1. Vuelva a la página **IAM** > **Roles** y seleccione el rol que agregó [anteriormente](#create-a-new-aws-role-for-purview).

    1. En la pestaña **Permissions** (Permisos), seleccione **Attach policies** (Asociar directivas).

        ![En la pestaña Permissions (Permisos) del rol, seleccione Attach policies (Asociar directivas).](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. En la página **Attach Permissions** (Asociar permisos), busque y seleccione la nueva directiva que creó anteriormente. Seleccione **Attach policy** (Asociar directiva) para adjuntar la directiva al rol.

        La página **Summary** (Resumen) se actualiza con la nueva directiva adjunta al rol.

        ![Visualización de una página de resumen actualizada con la nueva directiva asociada al rol.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Recuperación del nuevo ARN de rol

Deberá anotar el ARN de rol de AWS y copiarlo en Purview al [crear un examen para el cubo de Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**Para recuperar el ARN de rol:**

1. En el área **Identity and Access Management (IAM)**  > **Roles** (Administración de identidad y acceso > Roles) de AWS, busque y seleccione el nuevo rol que [creó para Purview](#create-a-purview-credential-for-your-aws-s3-scan).

1. En la página **Summary** (Resumen) del rol, seleccione el botón **Copy to clipboard** (Copiar al portapapeles) a la derecha del valor de **ARN de rol** (ARN de rol).

    ![Copie el valor del ARN de rol en el portapapeles.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

En Purview, puede editar la credencial de AWS S3 y pegar el rol recuperado en el campo **ARN de rol**. Para más información, vaya a [Creación de un examen para uno o varios cubos de Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Recuperación del nombre del cubo de Amazon S3

Necesitará el nombre del cubo de Amazon S3 para copiarlo en Purview al [crear un examen para el cubo de Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**Para recuperar el nombre del cubo:**

1. En AWS, vaya a **Storage** > **S3** (Almacenamiento > S3) y seleccione **Buckets** (Cubos) en e menú de la izquierda.

    ![Consulte la pestaña de cubos de Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Busque y seleccione el cubo para ver la página de detalles del cubo y, a continuación, copie el nombre del cubo en el portapapeles.

    Por ejemplo:

    ![Recupere y copie la dirección URL del cubo de S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Pegue el nombre del cubo en un archivo seguro y agregue el prefijo `s3://` para crear el valor que debe especificar al configurar el cubo como un recurso de Purview.

    Por ejemplo: `s3://purview-tutorial-bucket`

> [!TIP]
> Solo se admite el nivel raíz del cubo como origen de datos de Purview. Por ejemplo, *no* se admite la siguiente dirección URL, que incluye una subcarpeta: `s3://purview-tutorial-bucket/view-data`.
>
> Sin embargo, si configura un examen para un cubo de S3 concreto, puede seleccionar una o varias carpetas específicas para el examen. Para más información, consulte el paso para el [ámbito del examen](#create-a-scan-for-one-or-more-amazon-s3-buckets).
>

### <a name="locate-your-aws-account-id"></a>Búsqueda del identificador de cuenta de AWS

Necesitará el identificador de la cuenta de AWS para registrar la cuenta de AWS como un origen de datos de Purview, junto con todos sus cubos.

El identificador de la cuenta de AWS es el identificador que se usa para iniciar sesión en la consola de AWS. También puede encontrarlo una vez que haya iniciado sesión en el panel IAM, a la izquierda bajo las opciones de navegación y en la parte superior, como la parte numérica de la dirección URL de inicio de sesión:

Por ejemplo:

![Recupere el identificador de cuenta de AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Adición de un único cubo de Amazon S3 como recurso de Purview

Use este procedimiento si solo tiene un único cubo de S3 que desee registrar en Purview como origen de datos, o si tiene varios cubos en la cuenta de AWS, pero no desea registrarlos todos en Purview.

**Para agregar un cubo**:

1. Inicie el portal de Purview con la dirección URL dedicada del conector de Purview para Amazon S3. Esta dirección URL se la proporcionó el equipo de administración del producto del conector de Purview de Amazon S3.

    ![Inicie el portal de Purview.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Vaya a la página **Mapa de datos** de Azure Purview y seleccione **Registrar** ![Icono Registrar.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3** > **Continue** (Amazon S3 > Continuar).

    ![Agregue un cubo de Amazon AWS como un origen de datos de Purview.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Si tiene varias [colecciones](manage-data-sources.md#manage-collections) y desea agregar Amazon S3 a una colección específica, seleccione **Map view** (Vista de mapa) en la parte superior derecha y, a continuación, seleccione el icono **Register** (Registrar). ![Icono Registrar.](./media/register-scan-amazon-s3/register-button.png) Botón dentro de la colección.
    >

1. En el panel **Register sources (Amazon S3)** (Registrar orígenes [Amazon S3]) que se abre, escriba los detalles siguientes:

    |Campo  |Descripción  |
    |---------|---------|
    |**Nombre**     |Escriba un nombre descriptivo o utilice el valor predeterminado proporcionado.         |
    |**Dirección URL del cubo**     | Escriba la dirección URL del cubo de AWS con la siguiente sintaxis: `s3://<bucketName>`     <br><br>**Nota**: Asegúrese de usar solo el nivel de raíz del cubo. Para más información, consulte [Recuperación del nombre del cubo de Amazon S3](#retrieve-your-amazon-s3-bucket-name). |
    |**Selección de una colección** |Si ha seleccionado registrar un origen de datos desde una colección, esa colección ya aparece en la lista. <br><br>Seleccione una colección diferente según sea necesario, **None** (Ninguna) para no asignar ninguna colección o **New** (Nueva) para crear una nueva colección ahora. <br><br>Para más información sobre las colecciones de Purview, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md#manage-collections).|
    | | |

    Cuando haya terminado, seleccione **Finish** (Finalizar) para completar el registro.

Continúe con [Creación de un examen para uno o varios cubos de Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Adición de una cuenta de Amazon como un recurso de Purview

Use este procedimiento si tiene varios cubos de S3 en la cuenta de Amazon y desea registrarlos todos como orígenes de datos de Purview.

Al [configurar el examen](#create-a-scan-for-one-or-more-amazon-s3-buckets), podrá seleccionar los cubos específicos que desea examinar, si no desea examinarlos todos juntos.

**Para agregar una cuenta de Amazon**:
1. Inicie el portal de Purview con la dirección URL dedicada del conector de Purview para Amazon S3. Esta dirección URL se la proporcionó el equipo de administración del producto del conector de Purview de Amazon S3.

    ![Inicio del conector para Amazon S3 en el portal de Purview dedicado](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Vaya a la página **Mapa de datos** de Azure Purview y seleccione **Registrar** ![Icono Registrar.](./media/register-scan-amazon-s3/register-button.png) > **Amazon accounts** > **Continue** (Cuentas de Amazon > Continuar).

    ![Agregue una cuenta de Amazon como un origen de datos de Purview.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Si tiene varias [colecciones](manage-data-sources.md#manage-collections) y desea agregar Amazon S3 a una colección específica, seleccione **Map view** (Vista de mapa) en la parte superior derecha y, a continuación, seleccione el icono **Register** (Registrar). ![Icono Registrar.](./media/register-scan-amazon-s3/register-button.png) Botón dentro de la colección.
    >

1. En el panel **Register sources (Amazon S3)** (Registrar orígenes [Amazon S3]) que se abre, escriba los detalles siguientes:

    |Campo  |Descripción  |
    |---------|---------|
    |**Nombre**     |Escriba un nombre descriptivo o utilice el valor predeterminado proporcionado.         |
    |**Identificador de cuenta de AWS**     | Escriba el identificador de cuenta de AWS. Para más información, consulte [Búsqueda del identificador de cuenta de AWS](#locate-your-aws-account-id).|
    |**Selección de una colección** |Si ha seleccionado registrar un origen de datos desde una colección, esa colección ya aparece en la lista. <br><br>Seleccione una colección diferente según sea necesario, **None** (Ninguna) para no asignar ninguna colección o **New** (Nueva) para crear una nueva colección ahora. <br><br>Para más información sobre las colecciones de Purview, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md#manage-collections).|
    | | |

    Cuando haya terminado, seleccione **Finish** (Finalizar) para completar el registro.

Vaya a [Creación de un examen para uno o varios cubos de Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Creación de un examen para uno o varios cubos de Amazon S3

Una vez que haya agregado los cubos como orígenes de datos de Purview, puede configurar un examen para que se ejecute a intervalos programados o inmediatamente.

1. Seleccione la pestaña **Mapa de datos** del panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/) y, luego, realice uno de los siguientes procedimientos:

    - En **Map view** (Vista de mapa), seleccione **New scan** (Nuevo examen) ![Icono Nuevo examen.](./media/register-scan-amazon-s3/new-scan-button.png) en el cuadro del origen de datos.
    - En **List view** (Vista de lista), mantenga el puntero sobre la fila del origen de datos y seleccione **New scan** (Nuevo examen) ![Icono Nuevo examen.](./media/register-scan-amazon-s3/new-scan-button.png).

1. En el panel **Scan...** (Examinar...) que se abre a la derecha, defina los campos siguientes y, a continuación, seleccione **Continue** (Continuar):

    |Campo  |Descripción  |
    |---------|---------|
    |**Nombre**     |  Escriba un nombre descriptivo para el examen o utilice el valor predeterminado.       |
    |**Tipo** |Solo se muestra si ha agregado la cuenta de AWS, con todos los cubos incluidos. <br><br>Las opciones actuales incluyen únicamente **All** > **Amazon S3** (Todos > Amazon S3). Manténgase atento sobre más opciones para seleccionar a medida que se amplíe la matriz de compatibilidad de Purview. |
    |**Credential:**     |  Seleccione una credencial de Purview con el ARN de rol. <br><br>**Sugerencia**: Si desea crear una credencial nueva en este momento, seleccione **New** (Nueva). Para más información, consulte [Creación de una credencial de Purview para el examen del cubo de AWS](#create-a-purview-credential-for-your-aws-s3-scan).     |
    | **Amazon S3**    |   Solo se muestra si ha agregado la cuenta de AWS, con todos los cubos incluidos. <br><br>Seleccione uno o varios cubos para examinarlos, o bien elija **Seleccionar todos** para examinar todos los cubos de su cuenta.      |
    | | |

    Purview comprueba automáticamente que el ARN de rol es válido y que se puede acceder a los cubos y a los objetos dentro de los cubos y, después, continúa si la conexión se realiza correctamente.

    > [!TIP]
    > Para especificar valores diferentes y probar la conexión antes de continuar, seleccione **Test connection** (Probar la conexión) en la parte inferior derecha antes de seleccionar **Continue** (Continuar).
    >

1. <a name="scope-your-scan"></a>En el panel **Scope your scan** (Ámbito del examen),seleccione los cubos o carpetas concretos que desea incluir en el examen.

    Al crear un examen de toda una cuenta de AWS, puede seleccionar los cubos específicos que desea que se examinen. Al crear un examen de un cubo S3 de AWS específico, puede seleccionar las carpetas concretas que desea que se examinen.

1. En el panel **Select a scan rule set** (Seleccionar un conjunto de reglas de examen), seleccione el conjunto de reglas predeterminado **AmazonS3** o seleccione **New scan rule set** (Nuevo conjunto de reglas de examen) para crear un nuevo conjunto de reglas personalizado. Una vez que haya seleccionado el conjunto de reglas, seleccione **Continue** (Continuar).

    Si selecciona crear un nuevo conjunto de reglas de examen personalizado, use el asistente para definir la configuración siguiente:

    |Panel  |Descripción  |
    |---------|---------|
    |**New scan rule set** / (Nuevo conjunto de reglas de examen)<br>**Scan rule description** (Descripción de la regla de examen)    |   Escriba un nombre descriptivo y una descripción opcional para el conjunto de reglas.      |
    |**Select file types** (Seleccionar tipos de archivo)     | Seleccione todos los tipos de archivo que desea incluir en el examen y, a continuación, seleccione **Continue** (Continuar).<br><br>Para agregar un nuevo tipo de archivo, seleccione **New file type** (Nuevo tipo de archivo) y defina lo siguiente: <br>- La extensión de archivo que desea agregar. <br>- Una descripción opcional.  <br>- Si el contenido del archivo tiene un delimitador personalizado o si es un tipo de archivo del sistema. A continuación, escriba el delimitador personalizado o seleccione el tipo de archivo del sistema. <br><br>Seleccione **Create** (Crear) para crear el tipo de archivo personalizado.     |
    |**Select classification rules** (Seleccionar reglas de clasificación)     |   Vaya a las reglas de clasificación y seleccione las que quiere ejecutar en el conjunto de datos.      |
    |     |         |

    Seleccione **Create** (Crear) cuando termine de crear el conjunto de reglas.

1. En el panel **Set a scan trigger** (Establecer un desencadenador del examen), seleccione una de las siguientes opciones y, a continuación, seleccione **Continue** (Continuar):

    - **Recurring** (Periódico) para configurar una programación para un examen recurrente.
    - **Once** (Una vez) para configurar un examen que se inicia inmediatamente.

1. En el panel **Review your scan** (Revisar el examen), compruebe los detalles del examen para confirmar que son correctos y, a continuación, seleccione **Save** (Guardar) o **Save and Run** (Guardar y ejecutar) si seleccionó **Once** (Una vez) en el panel anterior.

    > [!NOTE]
    > Una vez iniciado, el examen puede tardar hasta 24 horas en completarse. Podrá revisar los **informes de Insights** y buscar el catálogo 24 horas después de iniciar cada examen.
    >

Para más información, consulte [Exploración de los resultados del examen de Purview](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Exploración de los resultados del examen de Purview

Una vez que se completa un examen de Purview en los cubos de Amazon S3, puede explorar en profundidad en el área **Mapa de datos** de Purview para ver el historial de exámenes.

Seleccione un origen de datos para ver sus detalles y, a continuación, seleccione la pestaña **Scans** (Exámenes) para ver los exámenes que se están ejecutando o que se han completado.
Si ha agregado una cuenta de AWS con varios cubos, el historial de exámenes de cada cubo se muestra en la cuenta.

Por ejemplo:

![Muestre los exámenes de cubos de AWS S3 en el origen de la cuenta de AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Use las otras áreas de Purview para obtener información detallada sobre el contenido del patrimonio de datos, incluidos los cubos de Amazon S3:

- **Busque el catálogo de datos de Purview** y filtre por un cubo específico. Por ejemplo:

    ![Busque recursos de AWS S3 en el catálogo.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Consulte los informes de Insights** para ver las estadísticas de la clasificación, las etiquetas de confidencialidad, los tipos de archivo y más detalles sobre el contenido.

    Todos los informes de Insights de Purview incluyen los resultados del examen de Amazon S3, junto con el resto de los resultados de los orígenes de datos de Azure. Cuando procede, se ha agregado un tipo de recurso adicional **Amazon S3** a las opciones de filtrado del informe.

    Para más información, consulte [Descripción de Insights en Azure Purview](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Permisos mínimos para la directiva de AWS

El procedimiento predeterminado para [crear un rol de AWS para Purview](#create-a-new-aws-role-for-purview) para usar al examinar los cubos S3 usa la directiva **AmazonS3ReadOnlyAccess**.

La directiva **AmazonS3ReadOnlyAccess** brinda los permisos mínimos necesarios para examinar los cubos S3, y también puede incluir otros permisos.

Para aplicar solo los permisos mínimos necesarios para examinar los cubos, cree una nueva directiva con los permisos enumerados en las secciones siguientes, en función de si quiere examinar un único cubo o todos los cubos de la cuenta.

Aplique la nueva directiva al rol en lugar de a **AmazonS3ReadOnlyAccess**.

### <a name="individual-buckets"></a>Cubos individuales

Al examinar cubos S3 individuales, los permisos de AWS mínimos incluyen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Asegúrese de definir el recurso con el nombre de cubo específico. Por ejemplo:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Todos los cubos en su cuenta

Al examinar todos los cubos en la cuenta de AWS, los permisos de AWS mínimos incluyen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Asegúrese de definir el recurso con un carácter comodín. Por ejemplo:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los informes de Insights de Azure Purview:

> [!div class="nextstepaction"]
> [Descripción de Insights en Azure Purview](concept-insights.md)
