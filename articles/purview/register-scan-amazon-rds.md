---
title: Multi-Cloud Scanning Connector de Amazon RDS para Azure Purview
description: En esta guía paso a paso se describen los detalles de cómo examinar bases de datos de Amazon RDS, incluidos los datos de Microsoft SQL y PostgreSQL.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fab51bd25489527c36e37d5f60233fa62256367c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722589"
---
# <a name="amazon-rds-multi-cloud-scanning-connector-for-azure-purview-public-preview"></a>Multi-Cloud Scanning Connector de Amazon RDS para Azure Purview (versión preliminar pública)

Multi-Cloud Scanning Connector para Azure Purview permite explorar los datos de la organización entre proveedores de nube, incluidos Amazon Web Services, además de los servicios de almacenamiento de Azure.

En este artículo se describe cómo usar Azure Purview para examinar los datos estructurados almacenados actualmente en Amazon RDS, incluidas las bases de datos de Microsoft SQL y PostgreSQL, y descubrir qué tipos de información confidencial existen en los datos. También descubrirá cómo identificar las bases de datos de Amazon RDS donde se almacenan actualmente los datos para facilitar la protección de la información y el cumplimiento de los datos.

Para este servicio, use Purview a fin de proporcionar una cuenta de Microsoft con acceso seguro a AWS, donde se ejecutarán los conectores de Multi-Cloud Scanning Connector para Azure Purview. Los conectores de Multi-Cloud Scanning Connector para Azure Purview usan este acceso a las bases de datos de Amazon RDS para leer los datos y, después, notifica los resultados del examen, incluidos solo los metadatos y la clasificación, a Azure. Use los informes de clasificación y etiquetado de Purview para analizar y revisar los resultados del examen de los datos.

> [!IMPORTANT]
> Los conectores de Multi-Cloud Scanning Connector para Azure Purview son complementos independientes de Azure Purview. Los términos y condiciones de los conectores de Multi-Cloud Scanning Connector para Azure Purview están incluidos en el contrato con el que obtuvo los servicios de Microsoft Azure. Para obtener más información, vea Información legal de Microsoft Azure en https://azure.microsoft.com/support/legal/.
>

> [!IMPORTANT]
> La compatibilidad de Purview con Amazon RDS se encuentra actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="purview-scope-for-amazon-rds"></a>Ámbito de Purview para Amazon RDS

- **Motores de base de datos compatibles**: el almacenamiento de datos estructurados de Amazon RDS admite varios motores de base de datos. Azure Purview admite Amazon RDS con Microsoft SQL y PostgreSQL, o basado en estos.

- **Número máximo de columnas admitidas**: el examen de tablas de RDS no se admite con más de 300 columnas.

- **Compatibilidad con el acceso público**: Purview solo admite el examen con la VPC Private Link en AWS y no incluye el examen de acceso público.

- **Regiones admitidas**: Purview solo admite bases de datos de Amazon RDS que se encuentren en las regiones de AWS siguientes:

    - Este de EE. UU. (Ohio)
    - Este de EE. UU. (N. Virginia)
    - Oeste de EE. UU. (N. California)
    - Oeste de EE. UU. (Oregon)
    - Europa (Frankfurt)
    - Asia Pacífico (Tokio)
    - Asia Pacífico (Singapur)
    - Asia Pacífico (Sídney)
    - Europa (Irlanda)
    - Europa (Londres)
    - Europa (París)

- **Requisitos de dirección IP**: la base de datos de RDS debe tener una dirección IP estática. La dirección IP estática se usa para configurar AWS PrivateLink, tal como se describe en este artículo.

- **Problemas conocidos**: actualmente no se admite la siguiente función:

    - El botón **Prueba de conexión**. Los mensajes de estado del examen indicarán los errores relacionados con la configuración de la conexión.
    - Seleccionar tablas específicas de la base de datos que se examinarán.
    - [Linaje de datos](concept-data-lineage.md).

Para más información, consulte:

- [Administración y aumento de las cuotas de los recursos con Azure Purview](how-to-manage-quotas.md)
- [Tipos de archivo y orígenes de datos admitidos en Azure Purview](sources-and-scans.md)
- [Uso de puntos de conexión privados para la cuenta de Purview](catalog-private-link.md)

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de que ha completado los siguientes requisitos previos antes de agregar la base de datos de Amazon RDS como orígenes de datos de Purview y examinar los datos de RDS.

> [!div class="checklist"]
> * Tenga en cuenta que debe ser Administrador de orígenes de datos de Azure Purview.
> * Necesita una cuenta de Purview. [Cree una instancia de cuenta de Azure Purview](create-catalog-portal.md) si aún no tiene una.
> * Necesita una base de datos PostgreSQL de Amazon RDS o Microsoft SQL, con datos.


## <a name="configure-aws-to-allow-purview-to-connect-to-your-rds-vpc"></a>Configuración de AWS para permitir la conexión de Purview a la VPC de RDS

Azure Purview solo admite el examen cuando la base de datos se hospeda en una nube privada virtual (VPC), donde solo se puede acceder a la base de datos de RDS desde la misma VPC.

Los conectores de Azure Multi-Cloud Scanning Connector para el servicio Azure Purview se ejecuta en una cuenta de Microsoft independiente en AWS. Para examinar las bases de datos de RDS, la cuenta de AWS de Microsoft debe poder acceder a estas bases de datos en la VPC. Para permitir este acceso, deberá configurar [AWS PrivateLink](https://aws.amazon.com/privatelink/) entre la VPC de RDS (en la cuenta de cliente) a la VPC donde se ejecutan los conectores de Multi-Cloud Scanning Connector para Azure Purview (en la cuenta de Microsoft).

En el diagrama siguiente se muestran los componentes de la cuenta de Microsoft y la de cliente. En amarillo se resaltan los componentes que deberá crear para habilitar la conectividad de la VPC de RDS en su cuenta con la VPC donde se ejecutan los conectores de Multi-Cloud Scanning Connector para Azure Purview en la cuenta de Microsoft.

:::image type="content" source="media/register-scan-amazon-rds/vpc-architecture.png" alt-text="Diagrama de los conectores de Multi-Cloud Scanning Connector para el servicio Azure Purview en una arquitectura de VPC." border="false":::


> [!IMPORTANT]
> Los recursos de AWS creados para la red privada de un cliente incurrirán en costos adicionales en la factura de AWS del cliente.
>

### <a name="configure-aws-privatelink-using-a-cloudformation-template"></a>Configuración de AWS PrivateLink mediante una plantilla de CloudFormation

En el procedimiento siguiente se describe cómo usar una plantilla de CloudFormation de AWS para configurar AWS PrivateLink, lo que permite a Purview conectarse a la VPC de RDS. Este procedimiento se realiza en AWS y está destinado a un administrador de AWS.

Esta plantilla de CloudFormation está disponible para su descarga desde el [repositorio de Azure GitHub](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) y permitirá crear un grupo de destino, un equilibrador de carga y un servicio de punto de conexión.

- **Si tiene varios servidores de RDS en la misma VPC**, realice este procedimiento una vez y [especifique todas las direcciones IP del servidor de RDS y los puertos](#parameters). En este caso, la salida de CloudFormation incluirá puertos distintos para cada servidor de RDS.

    Al [registrar estos servidores de RDS como orígenes de datos en Purview](#register-an-amazon-rds-data-source), debe usar los puertos incluidos en la salida en lugar de los puertos reales del servidor RDS.

- **Si tiene servidores de RDS en varias VPC**, realice este procedimiento para cada una de ellas.


> [!TIP]
> También puede realizar este procedimiento manualmente. Para obtener más información, vea [Configuración manual de AWS PrivateLink (avanzado)](#configure-aws-privatelink-manually-advanced).
>

**Para preparar la base de datos de RDS con una plantilla de CloudFormation**:

1. Descargue la plantilla de CloudFormation [RDSPrivateLink_CloudFormation.yaml](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) necesaria para este procedimiento desde el repositorio de Azure GitHub:

    1. A la derecha de la [página de GitHub vinculada](https://github.com/Azure/Azure-Purview-Starter-Kit/blob/main/Amazon/AWS/RDS/Amazon_AWS_RDS_PrivateLink_CloudFormation.zip), seleccione **Download** (Descargar) para descargar el archivo ZIP.

    1. Extraiga el archivo ZIP en una ubicación local para que pueda acceder al archivo **RDSPrivateLink_CloudFormation.yaml**.

1. En el portal de AWS, vaya al servicio **CloudFormation**. En la parte superior derecha de la página, seleccione **Create Stack** > **With new resources (standard)** (Crear pila > Con recursos nuevos (estándar)).

1. En la página **Prerequisite - Prepare Template** (Requisito previo: preparar plantilla), seleccione **Template is ready** (La plantilla está lista).

1. En la sección **Specify template** (Especificar plantilla), seleccione **Upload a template file** (Cargar un archivo de plantilla). Seleccione **Choose file** (Elegir archivo), vaya al archivo **RDSPrivateLink_CloudFormation.yaml** que descargó anteriormente y, después, seleccione **Next** (Siguiente) para continuar.

1. En la sección **Stack name** (Nombre de pila), escriba un nombre para la pila. Este nombre se usará, junto con un sufijo agregado automáticamente, para los nombres de recursos creados más adelante en el proceso. Por lo tanto:

    - Asegúrese de usar un nombre significativo para la pila.
    - Asegúrese de que el nombre de la pila no tiene más de 19 caracteres.

1. <a name="parameters"></a>En el área **Parameters** (Parámetros), escriba los valores siguientes con los datos disponibles en la página de la base de datos de RDS en AWS:

    |Nombre  |Descripción  |
    |---------|---------|
    |**Punto de conexión y puerto**    | Escriba la dirección IP resuelta de la dirección URL y el puerto del punto de conexión de RDS. Por ejemplo: `192.168.1.1:5432` <br><br>- **Si se configura un proxy de RDS**, use la dirección IP del punto de conexión de lectura y escritura del proxy para la base de datos pertinente. Se recomienda usar un proxy de RDS cuando se trabaja con Purview, ya que la dirección IP es estática.<br><br>- **Si tiene varios puntos de conexión detrás de la misma VPC**, escriba hasta 10 puntos de conexión separados por comas.  En este caso, se crea un único equilibrador de carga a la VPC, lo que permite una conexión desde Multi-Cloud Scanning Connector de Amazon RDS para Azure Purview en AWS a todos los puntos de conexión de RDS en la VPC.       |
    |**Redes**     | Escriba su id. de VPC.        |
    |**VPC IPv4 CIDR**     | Escriba el valor CIDR de la VPC. Para encontrar este valor, seleccione el vínculo de la VPC en la página de la base de datos de RDS. Por ejemplo: `192.168.0.0/16`        |
    |**Subredes**     |Seleccione todas las subredes asociadas a la VPC.         |
    |**Seguridad**     | Seleccione el grupo de seguridad de la VPC asociado a la base de datos de RDS.         |
    |     |         |

    Cuando finalice, seleccione **Next** (Siguiente) para continuar.

1. La configuración de las **opciones de Configurar pila** es opcional para este procedimiento.

    Defina la configuración según sea necesario para su entorno. Para obtener más información, seleccione los vínculos **Learn more** (Más información) a fin de acceder a la documentación de AWS. Cuando finalice, seleccione **Next** (Siguiente) para continuar.

1. En la página **Review** (Revisar), compruebe que los valores seleccionados son correctos para su entorno. Realice los cambios necesarios y, después, seleccione **Create stack** (Crear pila) cuando haya terminado.

1. Observe los recursos que se van a crear. Cuando haya finalizado, los datos pertinentes de este procedimiento se muestran en las pestañas siguientes:

    - **Events** (Eventos): muestra los eventos o actividades que realiza la plantilla de CloudFormation.
    - **Resources** (Recursos): muestra el grupo de destino, el equilibrador de carga y el servicio de punto de conexión recientemente creados.
    - **Outputs** (Salidas): muestra el valor de **ServiceName**, y la dirección IP y el puerto de los servidores de RDS.

        Si tiene varios servidores de RDS configurados, se muestra otro puerto. En este caso, use el puerto que se muestra aquí en lugar del puerto real del servidor de RDS al [registrar la base de datos de RDS](#register-an-amazon-rds-data-source) como origen de datos de Purview.

1. En la pestaña **Outputs** (Salidas), copie el valor de clave **ServiceName** en el portapapeles.

    Usará el valor de la clave **ServiceName** en el portal de Azure Purview al [registrar la base de datos de RDS](#register-an-amazon-rds-data-source) como origen de datos de Purview. Aquí, escriba la clave **ServiceName** en el campo **Connect to private network via endpoint service** (Conectarse a una red privada mediante el servicio de punto de conexión).

## <a name="register-an-amazon-rds-data-source"></a>Registro de un origen de datos de Amazon RDS

**Para agregar el servidor de Amazon RDS como origen de datos de Azure Purview, haga lo siguiente**:

1. Vaya a la página **Mapa de datos** de Azure Purview y seleccione **Registrar** ![Icono de Registrar.](./media/register-scan-amazon-s3/register-button.png).

1.  En la página **Orígenes**, seleccione **Registrar**. En la página **Register sources** (Registrar orígenes) que aparece a la derecha, seleccione la pestaña **Base de datos** y, después, **Amazon RDS (PostgreSQL)** o **Amazon RDS (SQL)** .

    :::image type="content" source="media/register-scan-amazon-rds/register-amazon-rds.png" alt-text="Captura de pantalla de la página Registrar orígenes para seleccionar Amazon RDS (PostgreSQL).":::

1. Escriba los detalles del origen:

    |Campo  |Descripción  |
    |---------|---------|
    |**Nombre**     |  Escriba un nombre significativo para el origen, como `AmazonPostgreSql-Ups`.       |
    |**Nombre del servidor**     | Escriba el nombre de la base de datos de RDS en la sintaxis siguiente: `<instance identifier>.<xxxxxxxxxxxx>.<region>.rds.amazonaws.com`  <br><br>Se recomienda copiar esta dirección URL desde el portal de Amazon RDS y asegurarse de que la dirección URL incluye la región de AWS.      |
    |**Puerto**     |  Escriba el puerto que se usa para conectarse a la base de datos de RDS:<br><br>        - PostgreSQL: `5432`<br> - Microsoft SQL: `1433`<br><br>        Si ha [configurado AWS PrivateLink mediante una plantilla de CloudFormation](#configure-aws-privatelink-using-a-cloudformation-template) y tiene varios servidores de RDS en la misma VPC, use los puertos que aparecen en la pestaña **Salidas** de CloudFormation en lugar de los puertos de servidor de RDS de lectura.       |
    |**Conectarse a una red privada mediante el servicio de punto de conexión**     |  Escriba el valor de clave **ServiceName** obtenido al final del [procedimiento anterior](#configure-aws-privatelink-using-a-cloudformation-template). <br><br>Si ha preparado la base de datos de RDS manualmente, use el valor de **Nombre del servicio** obtenido al final del [Paso 5: Creación de un servicio de punto de conexión](#step-5-create-an-endpoint-service).       |
    |**Colección** (opcional)     |  Seleccione una colección a la cual agregar el origen de datos. Para más información, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md).       |
    |     |         |

1. Seleccione **Registrar** cuando esté a punto para continuar.

El origen de datos de RDS aparece en la lista o mapa Orígenes. Por ejemplo:

:::image type="content" source="media/register-scan-amazon-rds/amazon-rds-in-sources.png" alt-text="Captura de pantalla de un origen de datos de Amazon RDS en la página Orígenes.":::

## <a name="create-purview-credentials-for-your-rds-scan"></a>Creación de credenciales de Purview para el examen de RDS

Las credenciales admitidas para los orígenes de datos de Amazon RDS incluyen solo la autenticación de nombre de usuario y contraseña, con una contraseña almacenada en un secreto de Azure KeyVault.

### <a name="create-a-secret-for-your-rds-credentials-to-use-in-purview"></a>Creación de un secreto para las credenciales de RDS a fin de usarlo en Purview

1.  Agregue la contraseña a una instancia de Azure KeyVault como secreto. Para obtener más información, vea [Establecimiento y recuperación de un secreto de Key Vault mediante Azure Portal](../key-vault/secrets/quick-create-portal.md).

1.  Agregue una directiva de acceso a la instancia de Key Vault con los permisos **Get** y **List**. Por ejemplo:

    :::image type="content" source="media/register-scan-amazon-rds/keyvault-for-rds.png" alt-text="Captura de pantalla de una directiva de acceso para RDS en Purview.":::

    Al definir la entidad de seguridad de la directiva, seleccione la cuenta de Purview. Por ejemplo:

    :::image type="content" source="media/register-scan-amazon-rds/select-purview-as-principal.png" alt-text="Captura de pantalla de la selección de la cuenta de Purview como entidad de seguridad.":::

    Seleccione **Guardar** para guardar la actualización de la directiva de acceso. Para obtener más información, vea [Asignación de una directiva de acceso de Azure Key Vault](/azure/key-vault/general/assign-access-policy-portal).

1. En Azure Purview, agregue una conexión de KeyVault para conectar la instancia de KeyVault con el secreto de RDS a Purview. Para obtener más información, vea [Credenciales para la autenticación de origen en Azure Purview](manage-credentials.md).

### <a name="create-your-purview-credential-object-for-rds"></a>Creación del objeto de credencial de Purview para RDS

En Azure Purview, debe crear un objeto de credenciales para usarlo al examinar la cuenta de Amazon RDS.

1. En el área **Administración** de Purview, seleccione **Security and access** (Seguridad y acceso)  > **Credenciales** > **Nuevas**.

1. Seleccione **Autenticación de SQL** como método de autenticación. Después, escriba los detalles de la instancia de Key Vault donde se almacenan las credenciales de RDS, incluidos los nombres de la instancia de Key Vault y del secreto.

    Por ejemplo:

    :::image type="content" source="media/register-scan-amazon-rds/new-credential-for-rds.png" alt-text="Captura de pantalla de una nueva credencial para RDS.":::

Para obtener más información, vea [Credenciales para la autenticación de origen en Azure Purview](manage-credentials.md).

## <a name="scan-an-amazon-rds-database"></a>Examen de una base de datos de Amazon RDS

A fin de configurar un examen de Azure Purview para la base de datos de RDS, haga lo siguiente:

1.  En la página **Orígenes** de Purview, seleccione el origen de datos de Amazon RDS que quiere examinar.

1.  Seleccione :::image type="icon" source="media/register-scan-amazon-s3/new-scan-button.png" border="false"::: **New scan** (Nuevo examen) para empezar a definir el examen. En el panel que se abre a la derecha, escriba los detalles siguientes y, después, seleccione **Continuar**.

    - **Nombre**: escriba un nombre significativo para el examen.
    - **Nombre de la base de datos**: escriba el nombre de la base de datos que quiere examinar. Deberá buscar los nombres disponibles desde fuera de Purview y crear un examen independiente para cada base de datos en el servidor de RDS registrado.
    - **Credenciales**: seleccione las credenciales que creó anteriormente para que los conectores de Multi-Cloud Scanning Connector para Azure Purview accedan a la base de datos de RDS.

1.  En el panel **Select a scan rule set** (Seleccionar un conjunto de reglas de examen), seleccione el conjunto de reglas de examen que quiera usar o cree uno. Para obtener más información, consulte [Creación de un conjunto de reglas de examen](create-a-scan-rule-set.md).

1.  En el panel **Set a scan trigger** (Establecer un desencadenador de examen), seleccione si quiere ejecutar el examen una vez o de forma periódica y luego **Continuar**.

1.  En el panel **Review your scan** (Revisar el examen), revise los detalles y luego seleccione **Guardar y ejecutar** o bien **Guardar** para ejecutarlo más adelante.

Mientras ejecuta el examen, seleccione **Actualizar** para supervisar el progreso de este.

> [!NOTE]
> Al trabajar con bases de datos PostgreSQL de Amazon RDS, solo se admiten exámenes completos. No se admiten exámenes incrementales, ya que PostgreSQL no tiene un valor de **Hora de última modificación**. 
>

## <a name="explore-scanning-results"></a>Exploración de los resultados del examen

Una vez que se completa un examen de Purview en las bases de datos de Amazon RDS, debe explorar en profundidad en el área **Mapa de datos** de Purview para ver el historial de exámenes. Seleccione un origen de datos para ver sus detalles y, a continuación, seleccione la pestaña **Scans** (Exámenes) para ver los exámenes que se están ejecutando o que se han completado.

Use el resto de áreas de Purview para obtener información detallada sobre el contenido del patrimonio de datos, incluidas las bases de datos de Amazon RDS:

- **Explore los datos de RDS en el catálogo**. En el catálogo de Purview se muestra una vista unificada en todos los tipos de origen y los resultados del examen de RDS se muestran de forma similar a Azure SQL. Puede examinar el catálogo mediante filtros, o examinar los recursos y navegar por la jerarquía. Para más información, consulte:

    - [Tutorial: Examen de recursos en Azure Purview (versión preliminar) y visualización de su linaje](tutorial-browse-and-view-lineage.md)
    - [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
    - [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md)

- **Consulte los informes de Insights** para ver las estadísticas de la clasificación, las etiquetas de confidencialidad, los tipos de archivo y más detalles sobre el contenido.

    Todos los informes de conclusiones de Purview incluyen los resultados del examen de Amazon RDS, junto con el resto de los resultados de los orígenes de datos de Azure. Si procede, se agrega un tipo de recurso de **Amazon RDS** a las opciones de filtrado del informe.

    Para más información, consulte [Descripción de Insights en Azure Purview](concept-insights.md).

- **Vea los datos de RDS en otras características de Purview**, como las áreas **Exámenes** y **Glosario**. Para más información, consulte:

    - [Creación de un conjunto de reglas de examen](create-a-scan-rule-set.md)
    - [Tutorial: Creación e importación de términos de glosario en Azure Purview (versión preliminar)](tutorial-import-create-glossary-terms.md)


## <a name="configure-aws-privatelink-manually-advanced"></a>Configuración manual de AWS PrivateLink (avanzado)

En este procedimiento se describen los pasos manuales necesarios para preparar la base de datos de RDS en una VPC a fin de conectarse a Azure Purview.

De manera predeterminada, se recomienda usar una plantilla de CloudFormation en su lugar, tal como se describió anteriormente en este artículo. Para obtener más información, vea [Configuración de AWS PrivateLink mediante una plantilla de CloudFormation](#configure-aws-privatelink-using-a-cloudformation-template).

### <a name="step-1-retrieve-your-amazon-rds-endpoint-ip-address"></a>Paso 1: Recuperar la dirección IP del punto de conexión de Amazon RDS

Busque la dirección IP del punto de conexión de Amazon RDS, hospedado dentro de una VPC de Amazon. Esta dirección IP se usará más adelante en el proceso al crear el grupo de destino.

**Para recuperar la dirección IP del punto de conexión de RDS, haga lo siguiente**:

1.  En Amazon RDS, vaya a la base de datos de RDS e identifique la dirección URL del punto de conexión. Se encuentra en **Connectivity & security** (Conectividad y seguridad), como el valor del **Endpoint** (Punto de conexión).

    > [!TIP]
    > Use el comando siguiente para obtener una lista de las bases de datos del punto de conexión: `aws rds describe-db-instances`
    >

1.  Use la dirección URL del punto de conexión para buscar la dirección IP de la base de datos de Amazon RDS. Por ejemplo, use uno de los métodos siguientes:

    - **Ping**: `ping <DB-Endpoint>`

    - **nslookup**: `nslookup <Db-Endpoint>`

    - **nslookup en línea**. Escriba el valor de **Endpoint** (Punto de conexión) de la base de datos en el cuadro de búsqueda y seleccione **Find DNS records** (Buscar registros DNS). **NSLookup.io** muestra la dirección IP en la pantalla siguiente.

### <a name="step-2-enable-your-rds-connection-from-a-load-balancer"></a>Paso 2: Habilitar la conexión RDS desde un equilibrador de carga

Para garantizar que se permitirá la conexión RDS desde el equilibrador de carga que cree más adelante en el proceso, haga lo siguiente:

1.  **Busque el intervalo IP de la VPC**.

    En Amazon RDS, vaya a la base de datos de RDS. En el área **Connectivity & security** (Conectividad y seguridad), seleccione el vínculo de la **VPC** para buscar su intervalo IP (CIDR IPv4).

    En el área **Your VPCs** (Sus VPC), el intervalo IP se muestra en la columna **IPv4 CIDR** (CIDR IPv4).

    > [!TIP]
    > Para realizar este paso mediante la CLI, use el comando siguiente: `aws ec2 describe-vpcs`.
    >
    > Para obtener más información, vea [ec2: Referencia de comandos de la CLI 1.19.105 de AWS (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/).
    >

1.  <a name="security-group"></a>**Cree un grupo de seguridad para este intervalo IP**.

    1. Abra la consola de Amazon EC2 en https://console.aws.amazon.com/ec2/ y vaya a **Security Groups** (Grupos de seguridad).

    1. Seleccione **Create security group** (Crear grupo de seguridad) y, después, cree el grupo de seguridad y asegúrese de incluir los detalles siguientes:

        - **Nombre del grupo de seguridad**: escriba un nombre significativo.
        - **Descripción**: escriba una descripción para el grupo de seguridad.
        - **VPC**: seleccione la VPC de la base de datos de RDS.

    1.  En **Inbound rules** (Reglas de entrada), seleccione **Add rule** (Agregar regla) y escriba los detalles siguientes:

        - **Tipo**: seleccione **Custom TCP** (TCP personalizado).
        - **Intervalo de puertos**: escriba el puerto de la base de datos de la RDS.
        - **Origen**: seleccione **Custom** (Personalizado) y escriba el intervalo IP de la VPC del paso anterior.

    1.  Desplácese a la parte inferior de la página y seleccione **Create security group** (Crear grupo de seguridad).

1.  **Asocie el grupo de seguridad nuevo a RDS**.

    1.  En Amazon RDS, vaya a la base de datos de RDS y seleccione **Modify** (Modificar).

    1.  Desplácese hacia abajo hasta la sección **Connectivity** (Conectividad) y, en el campo **Security group** (Grupo de seguridad), agregue el grupo de seguridad que creó en el [paso anterior](#security-group). Desplácese hasta la parte inferior de la página y seleccione **Continue** (Continuar).

    1.  En la sección **Scheduling of modifications** (Programación de modificaciones), seleccione **Apply immediately** (Aplicar inmediatamente) para actualizar el grupo de seguridad de inmediato.

    1.  Seleccione **Modify DB instance** (Modificar instancia de DB).

> [!TIP]
> Para realizar este paso mediante la CLI, use los comandos siguientes:
>
> - `aws  ec2 create-security-group--description <value>--group-name <value>[--vpc-id <value>]`
>
>     Para obtener más información, vea [create-security-group: Referencia de comandos de la CLI 1.19.105 de AWS (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html).
>
> - `aws rds --db-instance-identifier <value> --vpc-security-group-ids <value>`
>
>     Para obtener más información, vea [modify-db-instance: Referencia de comandos de la CLI 1.19.105 de AWS (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html).
>

### <a name="step-3-create-a-target-group"></a>Paso 3: Crear un grupo de destino

**Para crear el grupo de destino en AWS, haga lo siguiente**:

1.  Abra la consola de Amazon EC2 en https://console.aws.amazon.com/ec2/ y vaya a **Load Balancing** > **Target Groups** (Equilibrio de carga > Grupos de destino).

1.  Seleccione **Create target group** (Crear grupo de destino), cree el grupo de destino y asegúrese de incluir los siguientes detalles:

    - **Target type** (Tipo de destino): seleccione **IP addresses** (Direcciones IP) (opcional).
    - **Protocol** (Protocolo): seleccione **TCP** (TCP).
    - **Port** (Puerto): escriba el puerto de la base de datos de la RDS.
    - **VPC** (VPC): escriba la VPC de la base de datos de la RDS.

    > [!NOTE]
    > Puede encontrar el puerto de la base de datos de RDS y los valores de la VPC en la página de la base de datos de RDS, en **Connectivity & security** (Conectividad y seguridad).

    Cuando finalice, seleccione **Next** (Siguiente) para continuar.

1.  En la página **Register targets** (Registrar destinos), escriba la dirección IP de la base de datos de RDS y seleccione **Include as pending below** (Incluir como pendiente a continuación).

1.  Después de ver el nuevo destino en la tabla \**Targets** (Destinos), seleccione **Create target group** (Crear grupo de destino) en la parte inferior de la página.

> [!TIP]
> Para realizar este paso mediante la CLI, use el comando siguiente:
>
> - `aws elbv2 create-target-group --name <tg-name> --protocol <db-protocol> --port <db-port> --target-type ip --vpc-id <db-vpc-id>`
>
>     Para obtener más información, vea [create-target-group: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-target-group.html).
>
> - `aws elbv2 register-targets --target-group-arn <tg-arn> --targets Id=<db-ip>,Port=<db-port>`
>
>     Para obtener más información, vea [register-targets: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/register-targets.html).
>

### <a name="step-4-create-a-load-balancer"></a>Paso 4: Creación de un equilibrador de carga

Puede [crear un nuevo equilibrador de carga de red](#create-load-balancer) para reenviar el tráfico a la dirección IP de RDS o bien [agregar un nuevo cliente de escucha](#listener) a un equilibrador de carga existente.

<a name="create-load-balancer"></a>**Para crear un equilibrador de carga de red a fin de reenviar el tráfico a la dirección IP de RDS, haga lo siguiente**:

1.  Abra la consola de Amazon EC2 en https://console.aws.amazon.com/ec2/ y vaya a **Load Balancing** > **Load Balancers** (Equilibrio de carga > Equilibradores de carga).

1.  Seleccione **Create Load Balancer** > **Network Load Balancer** (Crear equilibrador de carga > Equilibrador de carga de red) y luego seleccione o escriba los valores siguientes:

    - **Scheme** (Esquema): seleccione **Internal** (Interno).

    - **VPC** (VPC): seleccione la VPC de la base de datos de RDS.

    - **Mapping** (Asignación): asegúrese de que RDS está definido para todas las regiones de AWS y, después, asegúrese de seleccionar todas esas regiones. Puede encontrar esta información en el valor **Availability zone** (Zona de disponibilidad) en la página [RDS database](#step-1-retrieve-your-amazon-rds-endpoint-ip-address) (Base de datos de RDS), en la pestaña **Connectivity & security** (Conectividad y seguridad).

    - **Listeners and Routing** (Clientes de escucha y enrutamiento):

        - *Protocol* (Protocolo): seleccione **TCP** (TCP).
        - *Port* (Puerto): seleccione **RDS DB port** (Puerto de BD de RDS).
        - *Default action* (Acción predeterminada): seleccione el grupo de destino creado en el [paso anterior](#step-3-create-a-target-group).

1.  En la parte inferior de la página, seleccione **Create Load Balancer** > **View Load Balancers** (Crear equilibrador de carga > Ver equilibradores de carga).

1.  Espere unos minutos y actualice la pantalla hasta que la columna **State** (Estado) del nuevo equilibrador de carga esté **Active** (Activo).


> [!TIP]
> Para realizar este paso mediante la CLI, use los comandos siguientes:
> - `aws elbv2 create-load-balancer --name <lb-name> --type network --scheme internal --subnet-mappings SubnetId=<value>`
>
>    Para obtener más información, vea [create-load-balancer: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-load-balancer.html).
>
> - `aws elbv2 create-listener --load-balancer-arn <lb-arn> --protocol TCP --port 80 --default-actions Type=forward,TargetGroupArn=<tg-arn>`
>
>    Para obtener más información, vea [create-listener: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-listener.html).
>

<a name="listener"></a>**Para agregar un cliente de escucha a un equilibrador de carga existente, haga lo siguiente**:

1. Abra la consola de Amazon EC2 en https://console.aws.amazon.com/ec2/ y vaya a **Load Balancing** > **Load Balancers** (Equilibrio de carga > Equilibradores de carga).

1. Seleccione el equilibrador de carga > **Listeners** > **Add listener** (Clientes de escucha > Agregar cliente de escucha).

1. En la pestaña **Listeners** (Clientes de escucha), en el área **Protocol : port** (Protocolo: puerto), seleccione **TCP** (TCP) y escriba un nuevo puerto para el cliente de escucha.


> [!TIP]
> Para realizar este paso mediante la CLI, use el comando siguiente: `aws elbv2  create-listener --load-balancer-arn <value> --protocol <value>  --port <value> --default-actions Type=forward,TargetGroupArn=<target_group_arn>`.
>
> Para obtener más información, vea la [documentación de AWS](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-listener.html).
>

### <a name="step-5-create-an-endpoint-service"></a>Paso 5: Crear un servicio de punto de conexión

Después de [crear el equilibrador de carga](#step-4-create-a-load-balancer) y de que su estado sea **Active** (Activo), puede crear el servicio de punto de conexión.

**Para crear el servicio de punto de conexión, haga lo siguiente**:

1.  Abra la consola de la VPC de Amazon en https://console.aws.amazon.com/vpc/ y vaya a **Virtual Private Cloud > Endpoint Services** (Nube privada virtual > Servicios de punto de conexión).

1.  Seleccione **Create Endpoint Service** (Crear servicio de punto de conexión) y, en la lista desplegable **Available Load Balancers** (Equilibradores de carga disponibles), seleccione el equilibrador de carga creado en el [paso anterior](#step-4-create-a-load-balancer) o el equilibrador de carga donde se agregó un nuevo cliente de escucha.

1.  En la página **Create endpoint service** (Crear servicio de punto de conexión), desactive la selección de la opción **Require acceptance for endpoint** (Requerir aceptación para el punto de conexión).

1.  En la parte inferior de la página, seleccione **Create Service** > **Close** (Crear servicio > Cerrar).

1.  De nuevo en la página **Endpoint services** (Servicios de punto de conexión), haga lo siguiente:

    1. Seleccione el servicio de punto de conexión que ha creado.
    1. En la pestaña **Allow principals** (Permitir entidades de seguridad), seleccione **Add principals** (Agregar entidades de seguridad).
    1. En el campo **Principals to add > ARN** (Entidades de seguridad que se quiere agregar > ARN), escriba `arn:aws:iam::181328463391:root`.
    1. Seleccione **Add principals** (Agregar entidades de seguridad).

    > [!NOTE]
    > Al agregar una identidad, use un asterisco (*****) a fin de agregar permisos para todas las entidades de seguridad. Esto permite que todas las entidades de seguridad, en todas las cuentas de AWS, creen un punto de conexión para el servicio de punto de conexión. Para obtener más información, vea la [documentación de AWS](https://docs.aws.amazon.com/vpc/latest/privatelink/add-endpoint-service-permissions.html).

> [!TIP]
> Para realizar este paso mediante la CLI, use los comandos siguientes:
>
> - `aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns  <lb-arn>  --no-acceptance-required`
>
>    Para obtener más información, vea [create-vpc-endpoint-service-configuration: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc-endpoint-service-configuration.html).
>
> - `aws ec2 modify-vpc-endpoint-service-permissions --service-id <endpoint-service-id> --add-allowed-principals <purview-scanner-arn>`
>
>    Para obtener más información, vea [modify-vpc-endpoint-service-permissions: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html).
>

<a name="service-name"></a>**Para copiar el nombre del servicio a fin de usarlo en Azure Purview, haga lo siguiente**:

Una vez creado el servicio de punto de conexión, puede copiar el valor de **Nombre del servicio** en el portal de Azure Purview al [registrar la base de datos de RDS](#register-an-amazon-rds-data-source) como origen de datos de Purview.

Busque el **Nombre del servicio** en la pestaña **Detalles** del servicio de punto de conexión seleccionado.

> [!TIP]
> Para realizar este paso mediante la CLI, use el comando siguiente: `Aws ec2 describe-vpc-endpoint-services`.
>
> Para obtener más información, vea [describe-vpc-endpoint-services: Referencia de comandos de la CLI 2.2.7 de AWS (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpc-endpoint-services.html).
>

## <a name="troubleshoot-your-vpc-connection"></a>Solución de problemas de la conexión de la VPC

En esta sección se describen los errores comunes que pueden producirse al configurar la conexión de la VPC con Azure Purview y cómo solucionarlos y resolverlos.

### <a name="invalid-vpc-service-name"></a>Nombre del servicio de la VPC no válido

Si aparece un error de `Invalid VPC service name` o `Invalid endpoint service` en Azure Purview, siga estos pasos para solucionar los problemas:

1. Asegúrese de que el nombre del servicio de la VPC es correcto. Por ejemplo:

    :::image type="content" source="media/register-scan-amazon-rds/locate-service-name.png" alt-text="Captura de pantalla del nombre del servicio de la VPC en AWS.":::

1. Asegúrese de que el ARN de Microsoft aparece en las entidades de seguridad permitidas: `arn:aws:iam::181328463391:root`.

    Para obtener más información, vea [Paso 5: Crear un servicio de punto de conexión](#step-5-create-an-endpoint-service).

1. Asegúrese de que la base de datos de RDS aparece en una de las regiones admitidas. Para obtener más información, vea [Ámbito de Purview para Amazon RDS](#purview-scope-for-amazon-rds).

### <a name="invalid-availability-zone"></a>Zona de disponibilidad no válida

Si aparece un error de `Invalid Availability Zone` en Azure Purview, asegúrese de que RDS está definido para al menos una de las tres regiones siguientes:

- **us-east-1a**
- **us-east-1b**
- **us-east-1c**

Para obtener más información, vea la [documentación de AWS](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html).

### <a name="rds-errors"></a>Errores de RDS

Los errores siguientes pueden aparecer en Azure Purview:

- `Unknown database`. En este caso, la base de datos definida no existe. Compruebe que el nombre de la base de datos configurada es correcto.

- `Failed to login to the Sql data source. The given auth credential does not have permission on the target database.`. En este caso, el nombre de usuario y la contraseña son incorrectos. Compruebe las credenciales y actualícelas según sea necesario.


## <a name="next-steps"></a>Pasos siguientes

Más información sobre los informes de Insights de Azure Purview:

> [!div class="nextstepaction"]
> [Descripción de Insights en Azure Purview](concept-insights.md)