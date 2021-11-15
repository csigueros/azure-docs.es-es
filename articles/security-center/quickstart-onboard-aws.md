---
title: Conexión de una cuenta de AWS a Microsoft Defender for Cloud
description: Defensa de los recursos de AWS con Microsoft Defender for Cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: connect-aws-accounts
ms.openlocfilehash: 9877ec3b69829d8210eed18577a3d0738dcef889
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428551"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>Conexión de cuentas de AWS a Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Las cargas de trabajo de nube abarcan normalmente varias plataformas de nube, por lo que los servicios de seguridad de la nube deben hacer lo mismo.

Microsoft Defender for Cloud protege las cargas de trabajo de Azure, Amazon Web Services (AWS) y Google Cloud Platform (GCP).

Para proteger los recursos basados en AWS, puede conectar una cuenta con uno de estos dos mecanismos:

- **Experiencia de conectores clásicos en la nube**: como parte de la oferta inicial de varias nubes, presentamos estos conectores en la nube como una forma de conectar las cuentas de AWS y GCP. Si ya ha configurado un conector de AWS mediante la experiencia de conectores clásicos en la nube, se recomienda volver a conectar la cuenta con el mecanismo más reciente. Cuando haya agregado la cuenta mediante la página de configuración del entorno, quite el conector antiguo para evitar ver recomendaciones duplicadas.

- **Página de configuración del entorno (en versión preliminar)** (recomendado): esta página en versión preliminar proporciona una experiencia de incorporación mejorada y más sencilla (incluido el aprovisionamiento automático). Este mecanismo también amplía las características de seguridad mejoradas de Defender for Cloud a los recursos de AWS.

    - **Las características de CSPM de Defender for Cloud** se extienden a los recursos de AWS. Este plan sin agente evalúa los recursos de AWS según las recomendaciones de seguridad específicas de AWS, que se incluyen en la puntuación de seguridad. También se evaluará el cumplimiento de los recursos de los estándares integrados específicos de AWS (AWS CIS, AWS PCI DSS y Procedimientos recomendados de seguridad fundamentales de AWS). La [página de inventario de recursos](asset-inventory.md) de Defender for Cloud es una característica habilitada para varias nubes, que permite administrar los recursos de AWS junto con los de Azure.
    - **Microsoft Defender para Kubernetes** amplía la detección de amenazas de contenedores y defensas avanzadas a los **clústeres Linux de Amazon EKS**.
    - **Microsoft Defender para servidores** proporciona la detección de amenazas y defensas avanzadas a las instancias de EC2 con Windows y Linux. Este plan incluye la licencia integrada de Microsoft Defender para punto de conexión, líneas base de seguridad y evaluaciones de nivel de sistema operativo, análisis de evaluación de vulnerabilidades, controles de aplicaciones adaptables (AAC), supervisión de la integridad de los archivos (FIM) y mucho más.

En esta captura de pantalla se muestran las cuentas de AWS que aparecen en el [panel de información general](overview-page.md) de Defender for Cloud.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Cuatro proyectos de AWS enumerados en el panel de información general de Defender for Cloud" lightbox="./media/quickstart-onboard-aws/aws-account-in-overview.png":::


::: zone pivot="env-settings"

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Versión preliminar.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Precios:|El plan CSPM es gratuito.<br>El plan Defender para Kubernetes es gratuito durante la versión preliminar. Después, se facturará al mismo precio que el plan Defender para Kubernetes para recursos de Azure.<br>Para cada máquina de AWS conectada a Azure con [servidores habilitados para Azure Arc](../azure-arc/servers/overview.md), el plan Defender para servidores se factura al mismo precio que el plan Defender para servidores para máquinas de Azure. Si una instancia de AWS EC2 no tiene implementado Azure Arc, no se le cobrará por esa máquina.|
|Roles y permisos necesarios:|**Propietario** en la suscripción de Azure en cuestión<br>Un **colaborador** también puede conectar una cuenta de AWS si un propietario proporciona los detalles de la entidad de servicio (necesario para el plan Defender para servidores).|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Prerrequisitos

- Para conectar una cuenta de AWS a su suscripción de Azure, obviamente necesitará acceso a una cuenta de AWS.

- **Para habilitar el plan Defender para Kubernetes**, necesitará:
    - Al menos un clúster de Amazon EKS con permiso para acceder al servidor de API de EKS K8s.
    - La capacidad de recursos para crear una nueva cola de SQS, un flujo de entrega de Kinesis Fire Hose y un cubo S3 en la región del clúster.
    
    > [!TIP]
    > Para crear un nuevo clúster de EKS, siga las instrucciones de [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) (Introducción a Amazon EKS – eksctl).

- **Para habilitar el plan Defender para servidores**, necesitará:
    - Microsoft Defender para servidores habilitado (consulte [Inicio rápido: Habilitación de las características de seguridad mejorada](enable-enhanced-security.md))
    - Una cuenta de AWS activa con instancias de EC2 administradas por AWS Systems Manager (SSM) y que tenga el agente de SSM

    > [!TIP]
    > Algunas Amazon Machine Images (AMI) tienen el agente de SSM preinstalado, y sus AMI aparecen enumeradas en el artículo [AMIs with SSM Agent preinstalled](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-technical-details.html#ami-preinstalled-agent) (AMI con el agente de SSM preinstalado). 

    - Si las instancias de EC2 no tienen el agente SSM, siga las instrucciones pertinentes desde Amazon:
        - [Instalación del agente de SSM para un entorno híbrido (Windows)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-win.html)
        - [Instalación del agente de SSM para un entorno híbrido (Linux)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-linux.html)


## <a name="connect-your-aws-account"></a>Conexión de una cuenta de AWS

Siga los pasos que se indican a continuación para crear su conector de nube de AWS. 

1. En el menú de Defender for Cloud, abra **Parámetros del entorno**.
1. Seleccione **Agregar entorno** > **Amazon Web Services**.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-environment-settings.png" alt-text="Conexión de una cuenta de AWS a una suscripción de Azure.":::

1. Escriba los detalles de la cuenta de AWS, incluida la ubicación donde almacenará el recurso del conector y seleccione **Next: Select plans** (Siguiente: Seleccionar planes).

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-details.png" alt-text="Paso 1 del asistente para agregar cuentas de AWS: escriba los detalles de la cuenta.":::

1. La pestaña Select Plans (Seleccionar planes) es donde puede elegir qué funcionalidades de Defender for Cloud habilitar para esta cuenta de AWS. 

    > [!IMPORTANT]
    > Cada funcionalidad tiene sus propios requisitos de permisos y puede incurrir en cargos.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-plans-selection.png" alt-text="La pestaña Select Plans (Seleccionar planes) es donde puede elegir qué funcionalidades de Defender for Cloud habilitar para esta cuenta de AWS.":::

    - Para ampliar la cobertura de Defender para servidores a AWS EC2, establezca el plan **Servidores** en **Activado** y edite la configuración según sea necesario. 

    - Para ampliar la cobertura de Defender para Kubernetes a los clústeres de Linux de AWS EKS, establezca el plan **Contenedores** en **Activado** y edite la configuración según sea necesario.

1. Complete la instalación:
    1. Seleccione **Siguiente: Configurar acceso**.
    1. Descargue la plantilla de CloudFormation.
    1. Con la plantilla de CloudFormation descargada, cree la pila en AWS tal como se indica en la pantalla.
    1. Seleccione **Next: Review and generate** (Siguiente: Revisar y crear).
    1. Seleccione **Crear**.

Defender for Cloud comenzará de inmediato a examinar los recursos de AWS, y verá recomendaciones de seguridad en unas horas.

::: zone-end


::: zone pivot="classic-connector"

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Requiere [Microsoft Defender para servidores](defender-for-servers-introduction.md).|
|Roles y permisos necesarios:|**Propietario** en la suscripción de Azure en cuestión<br>Un **colaborador** también puede conectar una cuenta de AWS si un propietario proporciona los detalles de la entidad de servicio.|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||


## <a name="connect-your-aws-account"></a>Conexión de una cuenta de AWS

Siga los pasos que se indican a continuación para crear su conector de nube de AWS. 

### <a name="step-1-set-up-aws-security-hub"></a>Paso 1. Configuración de AWS Security Hub:

1. Para ver recomendaciones de seguridad para varias regiones, repita los pasos siguientes para cada región pertinente.

    > [!IMPORTANT]
    > Si utiliza una cuenta de administración de AWS, repita los tres pasos siguientes para configurar la cuenta de administración y todas las cuentas miembros conectadas de todas las regiones pertinentes.

    1. Habilite [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html) (Configuración de AWS).
    1. Habilite [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Compruebe que los datos fluyan al centro de seguridad. La primera vez que se habilita Security Hub, los datos pueden tardar varias horas en estar disponibles.

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>Paso 2. Configuración de la autenticación para Defender for Cloud en AWS

Hay dos maneras de que Defender for Cloud se autentique en AWS:

- **Crear un rol IAM para Defender for Cloud** (recomendado): este es el método más seguro.
- **Usuario de AWS para Defender for Cloud**: es una opción menos segura si IAM no está habilitado.

#### <a name="create-an-iam-role-for-defender-for-cloud"></a>Creación de un rol de IAM para Defender for Cloud
1. En la consola de Amazon Web Services, en **Security, Identity & Compliance** (Seguridad, identidad y cumplimiento), seleccione **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Servicios de AWS.":::

1. Seleccione **Roles** (Roles) y **Create role** (Crear rol).
1. Seleccione **Another AWS account** (Otra cuenta de AWS).
1. Escriba la siguiente información:

    - **Account ID** (Id. de cuenta): escriba el identificador de la cuenta Microsoft (**158177204117**) que aparece en la página del conector de AWS en Defender for Cloud.
    - **Require External ID** (Requerir id. externo): esta opción debe estar seleccionada.
    - **External ID** (Id. externo): escriba el identificador de la suscripción que se muestra en la página del conector de AWS en Defender for Cloud. 

1. Seleccione **Next** (Siguiente).
1. En la sección **Attach permission policies** (Asociar directivas de permisos), seleccione las siguientes [directivas administradas por AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html):

    - SecurityAudit (`arn:aws:iam::aws:policy/SecurityAudit`)
    - AmazonSSMAutomationRole (`arn:aws:iam::aws:policy/service-role/AmazonSSMAutomationRole`)
    - AWSSecurityHubReadOnlyAccess (`arn:aws:iam::aws:policy/AWSSecurityHubReadOnlyAccess`)

1. Tiene la opción de agregar etiquetas. La acción de agregar etiquetas al usuario no afecta a la conexión.
1. Seleccione **Next** (Siguiente).

1. En la lista de roles, seleccione el rol que ha creado.

1. Guarde el nombre del recurso de Amazon (ARN) para más adelante. 

#### <a name="create-an-aws-user-for-defender-for-cloud"></a>Creación de un usuario de AWS para Defender for Cloud 
1. Abra la pestaña **Users** (Usuarios) y seleccione **Add user** (Agregar usuario).
1. En el paso **Details** (Detalles), escriba un nombre de usuario para Defender for Cloud y asegúrese de seleccionar **Programmatic access** (Acceso mediante programación) en el tipo de acceso de AWS. 
1. Seleccione **Next Permissions** (Siguientes permisos).
1. Seleccione **Attach existing policies directly** (Asociar directivas existentes directamente):
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Seleccione **Siguiente: Etiquetas**. Tiene la opción de agregar etiquetas. La acción de agregar etiquetas al usuario no afecta a la conexión.
1. Seleccione **Review** (Revisar).
1. Guarde el archivo CSV de **Access key ID** (Id. de clave de acceso) y **Secret access key** (Clave de acceso secreta) generado automáticamente para más adelante.
1. Revise el resumen y luego seleccione **Crear usuario**.


### <a name="step-3-configure-the-ssm-agent"></a>Paso 3. Configuración del agente SSM

Para automatizar las tareas entre los recursos de AWS, se requiere el administrador de sistemas de AWS. Si las instancias de EC2 no tienen el agente SSM, siga las instrucciones pertinentes desde Amazon:

- [Instalación y configuración del agente SSM en instancias de Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalación y configuración del agente SSM en instancias de Linux de Amazon EC2](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Paso 4. Cumplimiento de los requisitos previos de Azure Arc
1. Asegúrese de que están registrados los [proveedores de recursos de Azure ](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) adecuados:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Cree una entidad de servicio para la incorporación a gran escala. Como **propietario** de la suscripción que desea usar para la incorporación, cree una entidad de servicio para la incorporación de Azure Arc, como se describe en [Creación de una entidad de servicio para la incorporación a gran escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale).


### <a name="step-5-connect-aws-to-defender-for-cloud"></a>Paso 5. Conexión de AWS a Defender for Cloud

1. En el menú de Defender for Cloud, abra **Configuración del entorno** y seleccione la opción para volver a la experiencia de conectores clásicos.

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Volver a la experiencia de conectores clásicos en la nube en Defender for Cloud.":::

1. Seleccione **Add AWS account** (Agregar cuenta de AWS).
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botón Add AWS account (Agregar cuenta de AWS) de la página de conectores de nube múltiple de Defender for Cloud":::
1. Configure las opciones en la pestaña **AWS authentication** (Autenticación de AWS):
    1. Escriba un **nombre para mostrar** para el conector.
    1. Confirme que la suscripción es correcta. Se trata de la suscripción que incluirá las recomendaciones del conector y de AWS Security Hub.
    1. En función de la opción de autenticación que haya elegido en el [Paso 2. Configuración de la autenticación para Defender for Cloud en AWS](#step-2-set-up-authentication-for-defender-for-cloud-in-aws):
        - Seleccione **Assume Role** (Asumir rol) y pegue el ARN de [Creación de un rol de IAM para Defender for Cloud](#create-an-iam-role-for-defender-for-cloud).

            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Pegado del archivo ARN en el campo pertinente del asistente para conexión de AWS en Azure Portal.":::

            O BIEN

        - Seleccione **Credentials** (Credenciales) y pegue la **clave de acceso** y la **clave secreta**, que encontrará en el archivo .csv que guardó en [Creación de un usuario de AWS para Defender for Cloud](#create-an-aws-user-for-defender-for-cloud).
1. Seleccione **Next** (Siguiente).
1. Configure las opciones de la pestaña **Configuración de Azure Arc**:

    Defender for Cloud detecta las instancias de EC2 en la cuenta de AWS conectada y usa SSM para incorporarlas a Azure Arc. 

    > [!TIP]
    > Para ver la lista de sistemas operativos compatibles, consulte la sección [¿Qué sistemas operativos son compatibles con las instancias de EC2?](#what-operating-systems-for-my-ec2-instances-are-supported) en las preguntas frecuentes.

    1. Seleccione el **grupo de recursos** y la **región de Azure** a los que se incorporarán las instancias de AWS EC2 detectadas en la suscripción seleccionada.
    1. Escriba el **identificador de la entidad de servicio** y el **secreto de cliente de la entidad de servicio** de Azure Arc, como se describe en [Creación de una entidad de servicio para la incorporación a escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Si la máquina se conecta a Internet mediante un servidor proxy, especifique la dirección IP del servidor proxy o el nombre y el número de puerto que usará la máquina para comunicarse con él. Escriba el valor con el formato ```http://<proxyURL>:<proxyport>```.
    1. Seleccione **Revisar + crear**.

        Revisión de la información de resumen

        En las secciones de etiquetas se enumeran todas las etiquetas de Azure que se crearán automáticamente para cada instancia de EC2 incorporada con sus propios detalles pertinentes para reconocerla fácilmente en Azure. 

        Más información sobre las etiquetas de Azure en [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Paso 6. Confirmación

Cuando el conector se ha creado correctamente y AWS Security Hub se ha configurado adecuadamente:

- Defender for Cloud examina el entorno en busca de instancias de AWS EC2 y las incorpora a Azure Arc, lo que permite instalar el agente de Log Analytics y proporcionar recomendaciones de seguridad y protección contra amenazas. 
- El servicio Defender for Cloud busca nuevas instancias de AWS EC2 cada seis horas y las incorpora de acuerdo con la configuración.
- El estándar CIS de AWS se mostrará en el panel de cumplimiento de normativas de Defender for Cloud.
- Si está habilitada la directiva de Security Hub, las recomendaciones aparecerán en el portal de Defender for Cloud y en el panel de cumplimiento normativo entre 5 y 10 minutos después de que finalice la incorporación.


::: zone-end

:::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Recursos y recomendaciones de AWS en la página de recomendaciones de Defender for Cloud" lightbox="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png":::


## <a name="monitoring-your-aws-resources"></a>Supervisión de los recursos de AWS

Como puede ver en la captura de pantalla anterior, la página de recomendaciones de seguridad de Defender for Cloud muestra los recursos de AWS. Puede usar el filtro de entornos para disfrutar de las funcionalidades de varias nubes de Defender for Cloud: consulte las recomendaciones para los recursos de Azure, AWS y GCP juntos.

Para ver todas las recomendaciones activas de los recursos por tipo de recurso, use la página de inventario de recursos de Defender for Cloud y filtre por el tipo de recurso de AWS que le interesa:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso de la página de inventario de recursos que muestra las opciones de AWS"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>Preguntas frecuentes: AWS en Defender for Cloud

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>¿Qué sistemas operativos son compatibles con las instancias de EC2?

Sistema operativo compatible con la incorporación automática a Azure Arc para máquinas AWS

- Ubuntu 16.04: el agente SSM está preinstalado de forma predeterminada
- Ubuntu 18.04: el agente SSM está preinstalado de forma predeterminada
- Windows Server: el agente-SSM está preinstalado de forma predeterminada
- CentOS Linux 7: se debe instalar SSM manualmente o incorporarse por separado
- SUSE Linux Enterprise Server (SLES) 15 (x64): se debe instalar SSM manualmente o incorporarse por separado
- Red Hat Enterprise Linux (RHEL) 7 (x64): se debe instalar SSM manualmente o incorporarse por separado


## <a name="next-steps"></a>Pasos siguientes

La conexión de la cuenta de AWS forma parte de la experiencia multinube disponible en Microsoft Defender for Cloud. Para información relacionada, consulte la página siguiente:

- [Conexión de cuentas de GCP a Microsoft Defender for Cloud](quickstart-onboard-gcp.md)
