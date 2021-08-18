---
title: Procedimientos recomendados de Red Hat JBoss EAP en Azure
description: La guía proporciona información sobre los procedimientos recomendados para usar Red Hat JBoss Enterprise Application Platform en Microsoft Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: e640f65707fd4ae8745426e00253cbc9c66192ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285836"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Procedimientos recomendados de Red Hat JBoss EAP en Azure

Los procedimientos recomendados de Red Hat JBoss EAP en Azure proporcionan una guía para el uso de Red Hat JBoss Enterprise Application Platform (EAP) en Microsoft Azure. JBoss EAP se puede usar con la plataforma de Microsoft Azure, siempre y cuando se use dentro de las configuraciones específicas admitidas para ejecutar JBoss EAP en Azure. Si va a configurar manualmente un entorno de JBoss EAP en clúster, aplique las configuraciones específicas necesarias para usar las características de agrupación en clústeres de JBoss EAP en Azure. En esta guía se detallan las configuraciones admitidas del uso de JBoss EAP en Microsoft Azure.

JBoss EAP es una implementación compatible con Jakarta Enterprise Edition (EE) 8 para las especificaciones de perfil web y plataforma completa. También es una implementación certificada de la especificación Java EE 8. Las versiones principales de JBoss EAP se bifurcan desde el proyecto de la comunidad de WildFly en determinados puntos en los que el proyecto de comunidad ha alcanzado el nivel de integridad de características deseado. Después de ese punto, se produce un largo período de pruebas y desarrollo de productos en el que JBoss EAP se estabiliza, certifica y mejora para su uso en producción. Durante la vigencia de una versión principal de JBoss EAP, se puede realizar una selección exclusiva de las características seleccionadas, así como volver a trasladarlas desde el proyecto de la comunidad. A continuación, estas características están disponibles en una serie de características que mejoran las versiones secundarias dentro de la misma familia de versiones principales.

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Configuraciones admitidas y no admitidas de JBoss EAP en Azure

Consulte la documentación de [Configuraciones admitidas de JBoss EAP](https://access.redhat.com/articles/2026253) para obtener más información sobre sistemas operativos (SO), plataformas Java y otras plataformas compatibles en las que se puede usar EAP.

El programa Red Hat Cloud Access le permite usar una suscripción de JBoss EAP para instalar JBoss EAP en la máquina virtual de Azure, que son sistemas operativos de Pago por uso a petición desde Microsoft Azure Marketplace. Las suscripciones del sistema operativo de la máquina virtual, en este caso Red Hat Enterprise Linux (RHEL), son independientes de una suscripción de JBoss EAP. Red Hat Cloud Access es una característica de suscripción de Red Hat que proporciona compatibilidad con JBoss EAP en proveedores de infraestructura en la nube certificados de Red Hat, como Microsoft Azure. Red Hat Cloud Access permite mover las suscripciones entre servidores locales tradicionales y recursos basados en la nube pública de una manera sencilla y rentable.

Puede encontrar más información sobre [Red Hat Cloud Access en el Portal de Clientes](https://www.redhat.com/en/technologies/cloud-computing/cloud-access). Recuerde que no es necesario Red Hat Cloud Access para las ofertas de pago por uso en Azure Marketplace. 

Cada versión de Red Hat JBoss EAP se prueba y admite en varios sistemas operativos líderes del mercado, máquinas virtuales Java (JVM) y combinaciones de bases de datos. Red Hat proporciona compatibilidad de producción y desarrollo para configuraciones admitidas e integraciones probadas según el contrato de suscripción. La compatibilidad se aplica tanto a entornos físicos como virtuales. Aparte de las restricciones anteriores del sistema operativo, compruebe las [configuraciones admitidas para JBoss EAP](https://access.redhat.com/articles/2026253), como las versiones y proveedores de Java Development Kit (JDK) admitidos. Proporciona información sobre las configuraciones admitidas de varias versiones de JBoss EAP, como las versiones 7.0, 7.1, 7.2 y 7.3. Consulte el artículo sobre la [Matriz de configuración o producto de Microsoft Azure](https://access.redhat.com/articles/product-configuration-for-azure) para ver los sistemas operativos de RHEL compatibles, los requisitos de capacidad mínima de la máquina virtual y la información sobre otros productos de Red Hat admitidos. Consulte el artículo sobre el [Proveedor de nube certificado o Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823) para ver los productos de software de Red Hat certificados para operar en Microsoft Azure.

Hay algunas características no admitidas al usar JBoss EAP en un entorno Microsoft Azure, que incluye:

- **Dominios administrados**: permite la administración de varias instancias de JBoss EAP desde un único punto de control. Los dominios administrados de JBoss EAP no se admiten en Microsoft Azure actualmente. Solo se admiten instancias de servidor de JBoss EAP independientes. En Azure se admite la configuración de clústeres de JBoss EAP mediante servidores independientes de JBoss EAP.

- **ActiveMQ Artemis de alta disponibilidad (HA) con un almacenamiento compartido**: la mensajería de JBoss EAP de alta disponibilidad mediante almacenes compartidos de Artemis no es compatible en Microsoft Azure.

- **`mod_custer` Publicidad**: no se puede usar JBoss EAP como equilibrador de carga de proxy `mod_cluster` de reserva, el anuncio de mod_cluster no se admite funcionalmente debido a las limitaciones de multidifusión del Protocolo de datagramas de usuario (UDP) de Azure.

## <a name="other-features-of-jboss-eap-on-azure"></a>Otras características de JBoss EAP en Azure

JBoss EAP proporciona opciones preconfiguradas para características como clústeres de alta disponibilidad (HA), mensajería y almacenamiento en caché distribuido. También permite a los usuarios escribir, implementar y ejecutar aplicaciones con las distintas API y servicios que proporciona JBoss EAP.

- **Compatible con Yakarta EE**: compatible con Yakarta EE 8 para las especificaciones de perfil web y plataforma completa.

- **Compatible con Java EE**: certificado de Java EE 8 para las especificaciones de perfil web y plataforma completa.

- **Consola de administración y CLI de administración**: es para interfaces de administración de servidores independientes. La CLI de administración también incluye un modo por lotes que puede crear scripts y automatizar tareas de administración. No se recomienda editar directamente los archivos de configuración XML de JBoss EAP.

- **Diseño de directorio simplificado**: el directorio de módulos contiene todos los módulos del servidor de aplicaciones. Los directorios independientes contienen los artefactos y los archivos de configuración para las implementaciones independientes.

- **Mecanismo modular de carga de clases**: los módulos se cargan y descargan a petición. El mecanismo modular de carga de clases mejora el rendimiento, tiene ventajas de seguridad y reduce los tiempos de inicio y reinicio.

- **Administración simplificada de orígenes de datos**: los controladores de base de datos se implementan como otros servicios. Además, los orígenes de datos se crean y administran mediante la consola de administración y la CLI de administración.

- **Marco de seguridad unificado**: Elytron proporciona un único marco unificado que puede administrar y configurar el acceso para servidores independientes. También se puede usar para configurar el acceso de seguridad para las aplicaciones implementadas en servidores de JBoss EAP.

## <a name="creating-your-microsoft-azure-environment"></a>Creación del entorno de Microsoft Azure

Cree las máquinas virtuales que hospedarán las instancias de JBoss EAP en el entorno de Microsoft Azure. Use el tamaño de máquina virtual de Azure Standard_A2 o superior. Puede usar las imágenes prémium de Pago por uso a petición de Azure para crear las máquinas virtuales o crear manualmente sus propias máquinas virtuales. Por ejemplo, puede implementar máquinas virtuales de RHEL de la siguiente manera:

* Uso de la imagen de RHEL de Marketplace a petición en Azure: hay varias ofertas en Azure Marketplace desde las que puede seleccionar la máquina virtual de RHEL en la que desea configurar JBoss EAP. Visite [Implementación de máquinas virtuales de RHEL 8 desde Azure Marketplace](https://access.redhat.com/documentation//red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content). Tiene dos opciones para elegir las licencias del sistema operativo RHEL en Azure Marketplace. Elija una suscripción de Pago por uso o Bring-Your-Own-Subscription (BYOS) a través del modelo de Red Hat Gold Image. Tenga en cuenta que si ha implementado una máquina virtual de RHEL mediante el plan de Pago por uso, solo se usarán los detalles de la suscripción de JBoss EAP para suscribir la implementación resultante a una suscripción de Red Hat.

* [Creación y aprovisionamiento manual de una imagen de RHEL para Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Use la versión secundaria más reciente de cada versión principal de RHEL.

Para la máquina virtual de Microsoft Windows Server, consulte la [documentación de Microsoft Azure](../../windows/overview.md) sobre cómo crear una máquina virtual de Windows Server en Azure.

## <a name="jboss-eap-installation"></a>Instalación de JBoss EAP

> [!NOTE]
>  Si usa la oferta de JBoss EAP en RHEL a través de Azure Marketplace, JBoss EAP se instala y configura automáticamente para el entorno de Azure.

Los pasos siguientes se aplican si va a implementar manualmente EAP en una máquina virtual de RHEL implementada en Microsoft Azure.

Una vez configurada la máquina virtual, puede instalar JBoss EAP. Necesita acceso al [Portal de Clientes de Red Hat](https://access.redhat.com), que es la plataforma centralizada para Knowledge Base (KB) y los recursos de suscripción de Red Hat. Si no tiene una suscripción de EAP, regístrese para obtener una suscripción gratuita para desarrolladores a través de la [suscripción para desarrolladores de Red Hat para usuarios](https://developers.redhat.com/register). Una vez registrado, busque las credenciales (identificadores de grupo) en la sección Suscripción del [Portal de Clientes de Red Hat](https://access.redhat.com/management/). Tenga en cuenta que esta suscripción no está pensada para su uso en producción.

Hemos usado la variable *EAP_HOME* para indicar la ruta de acceso a la instalación de JBoss EAP. Reemplace esta variable por la ruta de acceso real a la instalación de JBoss EAP.

> [!IMPORTANT]
> Hay varias maneras diferentes de instalar JBoss EAP. Cada método se usa mejor en determinadas situaciones. Si usa una máquina virtual a petición de RHEL desde Microsoft Azure Marketplace, instale JBoss EAP mediante el archivo ZIP o los métodos del instalador. **No registre una máquina virtual a petición de RHEL en Red Hat Subscription Management (RHSM), ya que se le facturará dos veces por esa máquina virtual porque usa el método de facturación de Pago por uso.

* **Instalación del archivo ZIP**: el archivo ZIP es adecuado para la instalación en todos los sistemas operativos compatibles. Se debe usar el método de instalación del archivo ZIP si desea extraer la instancia manualmente. La instalación del archivo ZIP proporciona una instalación predeterminada de JBoss EAP y todas las configuraciones que se realizarán después de la instalación. Si tiene previsto usar el servidor JBoss Operations Network (ON) para implementar e instalar revisiones de JBoss EAP, las instancias de JBoss EAP de destino deben instalarse mediante el método de instalación del archivo ZIP. Consulte la [instalación del archivo ZIP](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation) para obtener más detalles.

* **Instalador de JAR**: el instalador de JAR se puede ejecutar en una consola o como un asistente gráfico. Ambas opciones proporcionan instrucciones paso a paso para instalar y configurar la instancia del servidor. El instalador de JAR es el método preferido para instalar JBoss EAP en todas las plataformas compatibles. Para obtener más información, consulte la [instalación del instalador de JAR](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation).

* **Instalación de RPM**: JBoss EAP se puede instalar mediante paquetes RPM en instalaciones compatibles de RHEL 6, RHEL 7 y RHEL 8. El método de instalación de RPM es más adecuado cuando se planea automatizar la instalación de EAP en la máquina virtual de RHEL en Azure. La instalación de RPM de JBoss EAP instala todo lo necesario para ejecutar JBoss EAP como servicio. Para obtener más información, consulte la [instalación de RPM](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation).

## <a name="other-offers-by-azure-and-red-hat"></a>Otras ofertas de Azure y Red Hat

Red Hat y Microsoft se han asociado para incorporar a Azure Marketplace un conjunto de plantillas de soluciones de Azure que ofrecen un punto de partida sólido para la migración a Azure. Consulte la documentación para obtener la lista de ofertas y elija un entorno adecuado.

### <a name="azure-marketplace-offers"></a>Ofertas de Azure Marketplace

Puede acceder a estas ofertas desde [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).

Esta oferta de Marketplace incluye varias combinaciones de versiones de JBoss EAP y RHEL con modelos de suscripción de soporte flexible. JBoss EAP siempre utiliza el modelo BYOS, pero para el sistema operativo RHEL puede elegir entre BYOS o Pago por uso. La oferta de Azure Marketplace incluye opciones de configuración del plan para JBoss EAP en RHEL, como máquinas virtuales independientes, máquinas virtuales en clúster o conjuntos de escalado de máquinas virtuales en clúster. 

Los seis planes incluyen:

- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual independiente (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual independiente (BYOS)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual en clúster (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual en clúster (BYOS)
- JBoss EAP 7.3 en RHEL 8.3 como conjuntos de escalado de máquinas virtuales en clúster (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como conjuntos de escalado de máquinas virtuales en clúster (BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="La imagen muestra la imagen con la opción de implementación de Red Hat mediante el vínculo de implementación de GitHub.":::

### <a name="azure-quickstart-templates"></a>Plantillas de inicio rápido de Azure

Junto con las ofertas de Azure Marketplace, hay plantillas de inicio rápido disponibles para realizar la versión de prueba de EAP en Azure. Estos inicios rápidos incluyen scripts y plantillas de ARM precompilados para implementar JBoss EAP en Azure en varias configuraciones y combinaciones de versiones. 

La arquitectura de la solución incluye:

* JBoss EAP en RHEL (máquina virtual independiente)
* JBoss EAP en RHEL (máquinas virtuales en clúster)
* Boss EAP en RHEL (conjuntos de escalado de máquinas virtuales en clúster)

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="La imagen muestra las ofertas de Red Hat disponibles a través de Azure Marketplace.":::

Puede elegir entre RHEL 7.7 y 8.0 y JBoss EAP 7.2 y 7.3. Puede seleccionar una de las siguientes combinaciones para la implementación:

- JBoss EAP 7.2 en RHEL 7.7
- JBoss EAP 7.2 en RHEL 8.0
- JBoss EAP 7.3 en RHEL 8.0

Para empezar, seleccione una plantilla de inicio rápido con una combinación de JBoss EAP en RHEL coincidente que cumpla el objetivo de implementación. A continuación se muestra la lista de plantillas de inicio rápido disponibles.

* [JBoss EAP en RHEL (máquina virtual independiente)](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/): la plantilla de Azure implementa una aplicación web denominada JBoss-EAP en Azure en JBoss EAP (versión 7.2 o 7.3) que se ejecuta en una máquina virtual de RHEL (versión 7.7 u 8.0).

* [JBoss EAP en RHEL (máquinas virtuales en clúster)](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/): la plantilla de Azure implementa una aplicación web denominada eap-session-replication en el clúster de JBoss EAP que se ejecuta en un número "n" de máquinas virtuales de RHEL. "n" es el número inicial de máquinas virtuales que estableció al principio. Todas las máquinas virtuales se agregan al grupo de back-end de un equilibrador de carga.

* [JBoss EAP en RHEL (Virtual Machine Scale Sets en clúster)](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/): la plantilla de Azure implementa una aplicación web denominada eap-session-replication en el clúster de JBoss EAP 7.2 o 7.3 que se ejecuta en instancias de conjuntos de escalado de máquinas virtuales de RHEL 7.7 u 8.0.

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>Configuración de JBoss EAP para trabajar en plataformas en la nube

Una vez instalado JBoss EAP en la máquina virtual, puede configurarlo para que se ejecute como servicio. La configuración de JBoss EAP para que se ejecute como servicio depende del método de instalación de Boss EAP y del tipo de sistema operativo de la máquina virtual. Tenga en cuenta que la instalación de RPM de JBoss EAP instala todo lo necesario para ejecutar JBoss EAP como servicio. Para obtener más información, consulte [Configuración de JBoss EAP para que se ejecute como servicio](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service).

### <a name="starting-and-stopping-jboss-eap"></a>Inicio y detención de JBoss EAP

#### <a name="starting-jboss-eap"></a>Inicio de JBoss EAP

JBoss EAP se admite en RHEL y Windows Server y solo se ejecuta en un modo de funcionamiento de servidor independiente. El comando específico para iniciar JBoss EAP depende de la plataforma subyacente. Los servidores se inician inicialmente en un estado suspendido y no aceptarán ninguna solicitud hasta que se hayan iniciado todos los servicios necesarios. En este momento, los servidores se colocan en un estado de ejecución normal y pueden empezar a aceptar solicitudes. A continuación, se muestra el comando para iniciar JBoss EAP como un servidor independiente:

- Comando para iniciar JBoss EAP (instalado a través del archivo ZIP o el método del instalador) como un servidor independiente en la máquina virtual de RHEL:
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- Para Windows Server, use el script `EAP_HOME\bin\standalone.bat` para iniciar JBoss EAP como servidor independiente.
- El inicio de JBoss EAP es diferente para una instalación de RPM en comparación con una instalación del instalador de ZIP o JAR. Por ejemplo, para RHEL 7 y versiones posteriores, use el siguiente comando:
    ```
    systemctl start eap7-standalone.service
    ```
El script de inicio que se usa para iniciar JBoss EAP (instalado mediante el método ZIP o instalador) usa el archivo `EAP_HOME/bin/standalone.conf`, o `standalone.conf.bat` para Windows Server, para establecer algunas preferencias predeterminadas, como las opciones de JVM. Personalice la configuración de este archivo. JBoss EAP usa el archivo de configuración `standalone.xml` de manera predeterminada, pero se puede iniciar con otro. Para cambiar el archivo de configuración predeterminado que se usa para iniciar JBoss EAP instalado a través del método de RPM, use `/etc/opt/rh/eap7/wildfly/eap7-standalone.conf`. Use el mismo archivo eap7-standalone.conf para realizar otros cambios de configuración, como la dirección de enlace de WildFly.

Para obtener más información sobre los archivos de configuración independientes disponibles y cómo usarlos, consulte [Archivos de configuración del servidor independientes](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files).

Para iniciar JBoss EAP con una configuración diferente, use el argumento --server-config. Para obtener una lista completa de todos los argumentos de script de inicio disponibles y sus propósitos, use el argumento --help o compruebe los [argumentos en tiempo de ejecución del servidor](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime).

#### <a name="stopping-jboss-eap"></a>Detención de JBoss EAP

La forma de detener JBoss EAP depende de cómo se inició. Presione `Ctrl+C` en el terminal donde se inició JBoss EAP para detener una instancia interactiva de JBoss EAP. Para detener la instancia en segundo plano de JBoss EAP, use la CLI de administración para conectarse a la instancia en ejecución y apagar el servidor. Para obtener más información, consulte [Detención de JBoss EAP](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap).

La detención de JBoss EAP es diferente para una instalación de RPM en comparación con una instalación del instalador o de ZIP. El comando para detener una instalación de RPM de JBoss EAP depende del modo de funcionamiento que quiera iniciar y de la versión de RHEL que esté ejecutando. El modo independiente solo es compatible con el modo en Azure. 

- Por ejemplo, para RHEL 7 y versiones posteriores, use el siguiente comando:
    ```
    systemctl stop eap7-standalone.service
    ```
JBoss EAP también se puede suspender o apagar correctamente, lo que permite que las solicitudes activas se completen con normalidad, sin aceptar nuevas solicitudes. Una vez suspendido el servidor, se puede apagar, volver a un estado en ejecución o dejarlo en estado suspendido para realizar el mantenimiento. Mientras se suspende el servidor, las solicitudes de administración se siguen procesando. El servidor se puede suspender y reanudar mediante la consola de administración o la CLI de administración.

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>Configuración de subsistemas de JBoss EAP para trabajar en plataformas en la nube

Muchas de las API y funcionalidades que se exponen a las aplicaciones implementadas en JBoss EAP se organizan en subsistemas. Los administradores pueden configurar estos subsistemas para proporcionar un comportamiento diferente, en función del objetivo de la aplicación. Para obtener más información sobre los subsistemas, consulte [Subsistemas de JBoss EAP](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems).

Algunos subsistemas de JBoss EAP requieren que la configuración funcione correctamente en las plataformas en la nube. La configuración es necesaria porque un servidor de JBoss EAP está enlazado a la dirección IP privada de una máquina virtual en la nube. La dirección IP privada solo es visible desde la plataforma en la nube. Para determinados subsistemas, la dirección IP privada debe asignarse a la dirección IP pública del servidor, que es visible desde fuera de la nube. Para obtener más información sobre cómo modificar estos subsistemas, consulte [Configuración de subsistemas de JBoss EAP para trabajar en plataformas en la nube](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms).

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>Uso de alta disponibilidad de JBoss EAP en Microsoft Azure

Azure no admite protocolos de detección de JGroups basados en multidifusión UDP. JGroups usa la pila de UDP de manera predeterminada, por lo que debe asegurarse de cambiar esa opción a TCP, ya que Azure no admite UDP. Aunque puede usar otros protocolos de detección de JGroups, como TCPPING o JDBC_PING, se recomienda el protocolo de detección de archivos compartidos desarrollado para Azure, que es *Azure_PING*.

*AZURE_PING* usa un contenedor de blobs común en una cuenta de almacenamiento de Microsoft Azure. Si aún no tiene un contenedor de blobs que AZURE_PING pueda usar, cree uno al que pueda acceder la máquina virtual. Para obtener más información, consulte [Configuración de alta disponibilidad de JBoss EAP en Microsoft Azure](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure).

Configure JBoss EAP con el entorno de equilibrio de carga. Asegúrese de que todos los equilibradores y los trabajos están enlazados a direcciones IP accesibles en la instancia interna de Microsoft Azure Virtual Network (VNet). Para obtener más información sobre la configuración de equilibrio de carga, consulte [Instalación y configuración de un clúster de alta disponibilidad de Red Hat Enterprise Linux 7.4 (y versiones posteriores) en Microsoft Azure](https://access.redhat.com/articles/3252491).

## <a name="other-best-practices"></a>Otros procedimientos recomendados

- Como administrador de una configuración de JBoss EAP en la máquina virtual, es importante asegurarse de que la máquina virtual es segura. Disminuirá significativamente el riesgo de que el software malintencionado infecte los sistemas operativos invitados y de host. La protección de la máquina virtual reduce los ataques a JBoss EAP y el funcionamiento incorrecto de las aplicaciones hospedadas en JBoss EAP. Controle el acceso a las máquinas virtuales de Azure mediante características como [Azure Policy](https://azure.microsoft.com/services/azure-policy/) y [roles integrados de Azure](../../../role-based-access-control/built-in-roles.md) en [Control de acceso basado en rol de Azure (RBAC)]/azure/role-based-access-control/overview). Proteja la máquina virtual contra malware mediante la instalación de Microsoft Antimalware o una solución de protección de puntos de conexión de un asociado de Microsoft e integración de la solución antimalware con [Azure Security Center](https://azure.microsoft.com/services/security-center/) para supervisar el estado de la protección. En las máquinas virtuales de RHEL, puede protegerla bloqueando el reenvío de puertos y el inicio de sesión raíz, que se puede deshabilitar en el archivo */ /ssh/sshd_config*.

- Use variables de entorno para facilitar y mejorar la experiencia con JBoss EAP en máquinas virtuales de Azure. Por ejemplo, puede usar EAP_HOME para indicar la ruta de acceso a la instalación de JBoss EAP, que se usará varias veces. En tales casos, las variables de entorno serán útiles. Las variables de entorno también son un medio común para configurar servicios y controlar secretos de aplicaciones web. Cuando se establece una variable de entorno desde el shell mediante el comando export, su existencia finaliza cuando finalizan las sesiones del usuario. Es problemático cuando necesitamos que la variable persista entre sesiones. Para que un entorno sea persistente para el entorno de un usuario, exportemos la variable desde el script de perfil del usuario. Agregue el comando export para cada variable de entorno que desee conservar en bash_profile. Si desea establecer una variable de entorno global permanente para todos los usuarios que tienen acceso a la máquina virtual, puede agregarla al perfil predeterminado. Se recomienda almacenar variables de entorno globales en un directorio denominado `/etc/profile.d`. El directorio contiene una lista de archivos que se usan para establecer variables de entorno para todo el sistema. El uso del comando set para establecer variables de entorno del sistema en un símbolo del sistema de Windows Server no establecerá permanentemente la variable de entorno. Use el comando *setx* o la interfaz de sistema en el panel de control.

- Administre las actualizaciones de la máquina virtual. Use la solución [Update Management](../../../automation/update-management/overview.md) de Azure Automation para administrar las actualizaciones del sistema operativo de los equipos Windows y Linux que se implementan en Azure. Evalúe rápidamente el estado de las actualizaciones disponibles en todos los equipos agente y administre el proceso de instalación de las actualizaciones necesarias para los servidores. La actualización del software de máquina virtual garantiza que revisiones de Microsoft Azure, controladores de hipervisor y paquetes de software importantes permanezcan actualizados. Las actualizaciones locales son posibles para las versiones secundarias. Por ejemplo, de RHEL 6.9 a RHEL 6.10 o RHEL 7.3 a RHEL 7.4. El tipo de actualización local se puede realizar mediante la ejecución del comando *yum update*. Microsoft Azure no admite una actualización local de una versión principal, por ejemplo, de RHEL 6 a RHEL 7.

- Use [Azure Monitor](../../../azure-monitor/data-platform.md) para obtener una mayor visibilidad del estado de los recursos. Las características de Azure Monitor incluyen [archivos de registro de diagnóstico de recursos](../../../azure-monitor/essentials/platform-logs-overview.md). Se usa para supervisar los recursos de la máquina virtual e identifica posibles problemas que podrían poner en peligro la disponibilidad y el rendimiento. La [extensión de Azure Diagnostics](../../../azure-monitor/agents/diagnostics-extension-overview.md) puede proporcionar funcionalidades de supervisión y diagnóstico en máquinas virtuales de Windows. Para habilitar estas funcionalidades, incluya la extensión como parte de la plantilla de Azure Resource Manager. Habilite diagnósticos de arranque, que es una herramienta importante que se debe usar al solucionar problemas de una máquina virtual que no arranca. La salida de la consola y el registro de arranque pueden ayudar en gran medida al soporte técnico de Red Hat al resolver un problema de arranque. Habilite diagnósticos de arranque en Microsoft Azure Portal al crear una máquina virtual o en una máquina virtual existente. Una vez habilitada, puede ver la salida de la consola de la máquina virtual y descargar el registro de arranque para la solución de problemas.

- Otra manera de garantizar una comunicación segura es usar el punto de conexión privado en [Virtual Network (VNet)]/azure/virtual-network/virtual-networks-overview) y [redes privadas virtuales (VPN)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md). Las redes abiertas son accesibles para el mundo exterior y, por tanto, son susceptibles a ataques de usuarios malintencionados. VNet y VPN restringen el acceso a los usuarios seleccionados. La red virtual usa una dirección IP privada para establecer canales de comunicación aislados entre servidores dentro del mismo intervalo. La comunicación aislada permite a varios servidores de la misma cuenta intercambiar información y datos sin exponerse a un espacio público. Conéctese a un servidor remoto si lo hace localmente a través de una red privada. Hay distintos métodos, como usar JumpVM o JumpBox en la misma red virtual que la de la aplicación, o usar [emparejamiento de Azure Virtual Network](../../../virtual-network/virtual-network-peering-overview.md), [Azure Application Gateway](../../../application-gateway/overview.md), [Azure Bastion](https://azure.microsoft.com/services/azure-bastion), y así sucesivamente. Todos estos métodos permiten una conexión completamente segura y privada y pueden conectar varios servidores remotos.

- Use [grupos de seguridad de red (NSG) de Azure](../../../virtual-network/network-security-groups-overview.md) para filtrar el tráfico de red hacia y desde el servidor de aplicaciones en la red virtual de Azure. Los NSG contienen reglas de seguridad que permiten o deniegan el tráfico de red entrante (o el tráfico de red saliente) de varios tipos de recursos de Azure. Para cada regla, puede especificar un origen y destino, un puerto y un protocolo. Proteja la aplicación en JBoss EAP mediante estas reglas de NSG y bloquee o permita puertos a Internet.

- Para mejorar la funcionalidad de la aplicación que se ejecuta en JBoss EAP en Azure, puede usar la característica de alta disponibilidad disponible en Azure. La alta disponibilidad en Azure se puede lograr mediante recursos de Azure, como Load Balancer, Application Gateway o [conjunto de escalado de máquinas virtuales](../../../virtual-machine-scale-sets/overview.md). Estos métodos de alta disponibilidad proporcionarán redundancia y un rendimiento mejorado, lo que le permitirá realizar fácilmente el mantenimiento o actualizar una instancia de aplicación mediante la distribución de la carga a otra instancia de aplicación disponible. Para hacer frente a la demanda adicional de los clientes, puede que tenga que aumentar el número de instancias de aplicación en las que se ejecuta la aplicación. El conjunto de escalado de máquinas virtuales también tiene la característica de escalado automático, que permite a la aplicación escalar o reducir verticalmente automáticamente a medida que cambia la demanda.

## <a name="optimizing-the-jboss-eap-server-configuration"></a>Optimización de la configuración del servidor de JBoss EAP

Una vez que haya instalado el servidor de JBoss EAP y haya creado un usuario de administración, puede optimizar la configuración del servidor. Asegúrese de revisar la información de la [Guía de optimización del rendimiento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index) sobre cómo optimizar la configuración del servidor y evitar problemas comunes al implementar aplicaciones en un entorno de producción.

## <a name="resource-links-and-support"></a>Vínculos de recursos y soporte técnico

Para cualquier pregunta relacionada con el soporte técnico, incidencias o requisitos de personalización, póngase en contacto con el [soporte técnico de Red Hat](https://access.redhat.com/support) o el [soporte técnico de Microsoft Azure](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* Más información sobre [JBoss EAP](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/introduction)
* Red Hat Subscription Manager (RHSM) [Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [Red Hat OpenShift en Azure (ARO)](https://azure.microsoft.com/services/openshift/)
* Microsoft Docs para [Red Hat en Azure](./overview.md)
* [Imágenes Gold de tipo BYOS de RHEL en Azure](./byos.md)
* [Tutorial de vídeo de inicio rápido](https://www.youtube.com/watch?v=3DgpVwnQ3V4) de JBoss EAP en Azure 

## <a name="next-steps"></a>Pasos siguientes

* [Migración a JBoss EAP en la consulta de Azure](https://aka.ms/JavaCloud)
* Ejecución de JBoss EAP en [Azure App Service](/azure/developer/java/ee/jboss-on-azure)
* Implementación de JBoss EAP en máquinas virtuales o en un conjunto de escalado de máquinas virtuales de RHEL desde [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Implementación de JBoss EAP en máquinas virtuales o en un conjunto de escalado de máquinas virtuales de RHEL desde el [inicio rápido de Azure](https://aka.ms/Quickstart-JBoss-EAP)
* Uso de la [asistencia para la migración de Azure App Service](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Uso del [kit de herramientas de migración para aplicaciones](https://developers.redhat.com/products/mta) de Red Hat