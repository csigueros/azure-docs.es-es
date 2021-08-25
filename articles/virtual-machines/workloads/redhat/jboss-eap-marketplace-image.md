---
title: Oferta de Azure Marketplace para Red JBoss EAP en máquinas virtuales y conjuntos de escalado de máquinas virtuales de Red Hat Enterprise Linux de Azure
description: Cómo implementar Red Hat JBoss EAP en máquinas virtuales y conjuntos de escalado de máquinas virtuales de Red Hat Enterprise Linux (RHEL) de Azure con la oferta de Azure Marketplace.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 3c819367f35cb4a8174abaac1380eb439ace206a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769710"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>Implementación de Red Hat JBoss Enterprise Platform (EAP) en máquinas virtuales y conjuntos de escalado de máquinas virtuales de Azure con la oferta de Azure Marketplace

Las ofertas de Azure Marketplace para [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) en [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) de Azure son una solución conjunta de [Red Hat](https://www.redhat.com/) y Microsoft. Red Hat es un proveedor y colaborador líder de soluciones de código abierto que incluye los estándares de [Java](https://www.java.com/), [OpenJDK](https://openjdk.java.net/), [MicroProfile](https://microprofile.io/), [Jakarta EE](https://jakarta.ee/) y [Quarkus](https://quarkus.io/). JBoss EAP es una plataforma de servidor líder de aplicaciones Java con certificado Java EE y compatible con Jakarta EE tanto para perfil web como plataforma completa. Cada versión de JBoss EAP se prueba y admite en varios sistemas operativos líderes del mercado, máquinas virtuales Java (JVM) y combinaciones de bases de datos.  JBoss EAP y RHEL incluyen todo lo necesario para compilar, ejecutar, implementar y administrar aplicaciones empresariales Java en cualquier entorno.  Incluye entornos locales y virtuales, y nubes privadas, públicas o híbridas. La solución conjunta de Red Hat y Microsoft incluye soporte técnico integrado y flexibilidad de licencias de software.

## <a name="jboss-eap-on-azure-integrated-support"></a>JBoss EAP en soporte integrado de Azure

La oferta de Azure Marketplace para JBoss EAP es una solución conjunta de Red Hat y Microsoft e incluye soporte técnico integrado y flexibilidad de licencias de software. Puede comunicarse con Microsoft y Red Hat para presentar sus incidencias de soporte técnico.  Compartiremos y resolveremos las incidencias de forma conjunta para que no tenga que presentarlas a ambos proveedores. El soporte técnico integrado cubre todas las incidencias de nivel de servidor de aplicaciones de Red Hat e infraestructuras de Azure.    

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa: si no tiene una suscripción de Azure, puede activar las [ventajas de suscriptor de Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (anteriormente MSDN) o [crear una cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).

* Instalación de JBoss EAP: debe tener una cuenta de Red Hat con el derecho de administración de suscripciones de Red Hat (RHSM) para el JBoss EAP. El derecho le permitirá descargar la versión de JBoss EAP probada y certificada por Red Hat.  Si no tiene derechos de EAP, regístrese para obtener una suscripción gratuita para desarrolladores a través de la [suscripción para desarrolladores de Red Hat para usuarios.](https://developers.redhat.com/register) Después de registrarse, puede encontrar las credenciales necesarias (los identificadores de grupo) en el [portal de clientes de Red Hat](https://access.redhat.com/management/).

* Opciones de RHEL: elija entre Pago por uso (PAYG) o Traiga su propia suscripción (BYOS). Con BYOS, debe activar su [imagen RHEL Gold](https://access.redhat.com/) de [Red Hat Cloud Access](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) antes de implementar la oferta de Marketplace con la plantilla de soluciones. Siga [estas instrucciones](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) para habilitar las imágenes RHEL Gold y usarlas en Microsoft Azure.

* Interfaz de la línea de comandos (CLI) de Azure.

## <a name="azure-marketplace-offer-subscription-options"></a>Opciones de suscripción de la oferta de Azure Marketplace

La oferta de Azure Marketplace de JBoss EAP en RHEL se instalará y aprovisionará una máquina virtual o conjuntos de escalado de máquinas virtuales de Azure en menos de 10 minutos. Puede acceder a estas ofertas desde [Azure Marketplace](https://azuremarketplace.microsoft.com/)

La oferta de Marketplace incluye varias combinaciones de versiones de EAP y RHEL para satisfacer sus requisitos. JBoss EAP siempre utiliza el modelo BYOS, pero para el sistema operativo RHEL puede elegir entre BYOS o Pago por uso. La oferta incluye opciones de configuración del plan para JBoss EAP en RHEL como máquinas virtuales independientes en clúster o conjuntos de escalado de máquinas virtuales en clúster. 

Los seis planes disponibles son: 

- Boss EAP 7.3 en RHEL 8.3 como máquina virtual independiente (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual independiente (BYOS)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual en clúster (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como máquina virtual en clúster (BYOS)
- JBoss EAP 7.3 en RHEL 8.3 como conjunto de escalado de máquinas virtuales en clúster (Pago por uso)
- JBoss EAP 7.3 en RHEL 8.3 como conjunto de escalado de máquinas virtuales en clúster (BYOS)

## <a name="using-rhel-os-with-payg-model"></a>Uso del sistema operativo RHEL con el modelo Pago por uso

La oferta de Azure Marketplace le permite implementar RHEL como máquinas virtuales o conjuntos de escalado de máquinas virtuales de Pago por uso a petición. Los planes PAYG tendrán un cargo de suscripción de RHEL adicional por hora sobre los costos de proceso, red y almacenamiento de infraestructura corrientes.

Consulte los [precios de Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) para más información sobre el modelo PAYG. Para usar RHEL en el modelo PAYG, necesitará una suscripción de Azure. ([RHEL en las ofertas de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605) requiere que se especifique un método de pago en la suscripción de Azure).

## <a name="using-rhel-os-with-byos-model"></a>Uso del sistema operativo RHEL con el modelo BYOS

Para usar RHEL como máquinas virtuales o conjuntos de escalado de máquinas virtuales BYOS, es necesario tener una suscripción válida a Red Hat con derechos para usar RHEL en Azure. Estos planes de JBoss EAP de BYOS en RHEL están disponibles como [ofertas privadas de Azure](../../../marketplace/private-offers.md). DEBE completar los siguientes requisitos previos para implementar un plan de oferta de BYOS en RHEL desde el Azure Marketplace. 

1. Asegúrese de que tiene los derechos del sistema operativo RHEL y JBoss EAP asignados en la suscripción de Red Hat.
2. Autorice su identificador de suscripción de Azure para usar imágenes de BYOS en RHEL. Siga la [documentación de administración de suscripciones de Red Hat (RHSM)](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) para completar el proceso, que incluye estos pasos:
    1. Habilite Microsoft Azure como proveedor en el panel de Red Hat Cloud Access.
    2. Agregue sus identificadores de suscripción de Azure.
    3. Habilite nuevos productos para Cloud Access en Microsoft Azure.
    4. Active las imágenes Gold de Red Hat para su suscripción de Azure. Para más información, lea el capítulo sobre cómo [habilitar y mantener suscripciones para Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access). 
    5. Espere a que las imágenes de Red Hat Gold estén disponibles en su suscripción de Azure. Estas imágenes Gold suelen estar disponibles en un plazo de 3 horas a partir del envío o menos como ofertas privadas de Azure.

3. Acepte los términos y condiciones (T&C) de Azure Marketplace para las imágenes de BYOS en RHEL. Para aceptarlos, ejecute los comandos de [Interfaz de la línea de comandos de Azure (CLI)](/cli/azure/install-azure-cli) como se indica a continuación. Para más información, consulte la documentación de las [imágenes Gold de BYOS en RHEL en Azure](./byos.md). Es importante que ejecute la versión más reciente de la CLI de Azure.
    1. Inicie una sesión de la CLI de Azure y autentíquese con su cuenta de Azure. Consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli) para obtener ayuda. Asegúrese de ejecutar la versión más reciente de la CLI de Azure antes de continuar.
    2. Compruebe que los planes de BYOS en RHEL están disponibles en su suscripción mediante la ejecución del siguiente comando de la CLI. Si no obtiene ningún resultado aquí, consulte el paso 2. Asegúrese de que la suscripción de Azure está activada con derechos para JBoss EAP en planes de BYOS en RHEL.

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. Ejecute el siguiente comando para aceptar los términos y condiciones de Azure Marketplace según sea necesario para el plan de BYOS en RHEL de JBoss EAP.

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. Donde `$PLANID` es uno de los siguientes (repita el paso 3 para cada plan de oferta de Marketplace que desee utilizar):

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. La suscripción ya está lista para implementar EAP en planes de BYOS en RHEL. Durante la implementación, las suscripciones se adjuntarán automáticamente mediante `subscription-manager` con las credenciales proporcionadas durante la implementación.

## <a name="using-jboss-eap-with-byos-model"></a>Uso de JBoss EAP con el modelo BYOS

JBoss EAP solo está disponible en Azure mediante el modelo BYOS. Al implementar su plan de JBoss EAP en RHEL, debe proporcionar sus credenciales de RHSM junto con el identificador de grupo de RHSM con derechos de EAP válidos. Si no tiene derechos de EAP, obtenga una [suscripción para desarrolladores de Red Hat para usuarios](https://developers.redhat.com/register). Después de hacer el registro, puede encontrar las credenciales necesarias (identificadores de grupo) en el menú de navegación de la suscripción. Durante la implementación, las suscripciones se adjuntarán automáticamente mediante `subscription-manager` con las credenciales proporcionadas durante la implementación.

## <a name="template-solution-architectures"></a>Arquitecturas de soluciones para plantilla

Estos planes de oferta crean todos los recursos de proceso de Azure para ejecutar la configuración de JBoss EAP en RHEL. La solución de plantilla creará estos recursos:

- Máquina virtual de RHEL
- Un equilibrador de carga (para la configuración en clúster)
- Una red virtual con una única subred
- Configuración de JBoss EAP en una máquina virtual o conjuntos de escalado de máquinas virtuales de RHEL (independiente o en clúster)
- Una aplicación Java de ejemplo
- Cuenta de almacenamiento

## <a name="after-a-successful-deployment"></a>Después de la implementación correcta

1. [Cree una máquina virtual de salto con emparejamiento de red virtual](../../windows/quick-create-portal.md#create-virtual-machine) en una red virtual distinta, acceda al servidor y exponga la aplicación mediante [emparejamiento de red virtual](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks).
2. [Cree una dirección IP pública](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) para acceder al servidor y a la aplicación.
3. [Cree una máquina virtual de salto en la misma red virtual (VNet)](../../windows/quick-create-portal.md#create-virtual-machine) en una subred diferente (nueva subred) en la misma red virtual y acceda al servidor a través de una máquina virtual de salto. La máquina virtual de salto se puede usar para exponer la aplicación.
4. Exponga la aplicación mediante una [puerta de enlace de aplicación](../../../application-gateway/quick-create-portal.md#create-an-application-gateway).
5. Exponga la aplicación mediante equilibrador de carga externo (ELB).
6. [Use Azure Bastion](../../../bastion/bastion-overview.md) para acceder a las máquinas virtuales de RHEL mediante el explorador y Azure Portal. 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. Cree una máquina virtual de salto en una red virtual distinta y acceda a la máquina virtual de RHEL mediante emparejamiento de red virtual (método recomendado)

1. [Cree una máquina virtual de Windows](../../windows/quick-create-portal.md#create-virtual-machine): en un nuevo grupo de recursos de Azure, cree una máquina virtual de Windows, que DEBE estar en la misma región que la máquina virtual de RHEL. Proporcione los detalles necesarios y deje otras configuraciones como predeterminadas, ya que creará la máquina virtual de salto en una red virtual nueva.
2. [Emparejar las redes virtuales](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks): el emparejamiento es la forma de asociar la máquina virtual de RHEL con la máquina virtual de salto. Una vez se hace el emparejamiento de la red virtual de manera correcta, ambas máquinas virtuales pueden comunicarse entre sí.
3. Vaya a la página de detalles de la máquina virtual de salto y copie la dirección IP pública. Inicie sesión en la máquina virtual de salto mediante la dirección IP pública.
4. Copie la dirección IP privada de la máquina virtual de RHEL de la página de salida y úsela para iniciar sesión en la máquina virtual de RHEL desde la máquina virtual de salto.
5. Pegue la dirección URL de la aplicación que copió de la página de salida en un explorador dentro de la máquina virtual de salto. Consulte la página web de JBoss EAP en Azure desde este explorador.
6. Acceda a la consola de administrador de JBoss EAP: pegue la dirección URL de la consola de administrador copiada de la página de salida en un explorador dentro de la máquina virtual de salto, escriba el nombre de usuario y la contraseña de JBoss EAP para iniciar sesión.

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2. Cree una dirección IP pública para acceder a la máquina virtual de RHEL y a la consola de administración de JBoss EAP.

1. La máquina virtual de RHEL que creó no tiene asociada una dirección IP pública. Puede [crear una dirección IP pública](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) para acceder a la máquina virtual y [asociar la dirección IP pública a la máquina virtual](../../../virtual-network/associate-public-ip-address-vm.md). La creación de una dirección IP pública se puede realizar con comandos de Azure Portal, de [Azure PowerShell](/powershell/) o de [la CLI de Azure](/cli/azure/install-azure-cli).
2. Obtenga la dirección IP pública de una máquina virtual: vaya a la página de detalles de la máquina virtual y copie la dirección IP pública. Use una dirección IP pública para acceder a la máquina virtual y a la consola de administrador de JBoss EAP.
3. Vea la página web de JBoss EAP en Azure: abra un explorador web y vaya a *http://<PUBLIC_HOSTNAME>:8080/* y debería ver la página de bienvenida de EAP predeterminada.
4. Inicie sesión en la consola de administrador de JBoss EAP: abra un explorador web y vaya a *http://<PUBLIC_HOSTNAME>:9990*. Escriba el nombre de usuario y la contraseña de JBoss EAP para iniciar sesión.

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. Cree una máquina virtual de salto en una subred diferente (nueva subred) en la misma red virtual y acceda a la máquina virtual de RHEL a través de una máquina virtual de salto.

1. [Agregue una nueva subred](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) en la red virtual existente, que contiene la máquina virtual de RHEL.
2. [Cree una máquina virtual Windows](../../windows/quick-create-portal.md#create-virtual-machine) en Azure en el mismo grupo de recursos (RG) que la máquina virtual de RHEL. Proporcione los detalles necesarios y deje otras configuraciones como predeterminadas, excepto la red virtual y la subred. Asegúrese de seleccionar la red virtual existente en el grupo de recursos y seleccione la subred que creó en el paso anterior, ya que será la máquina virtual de salto.
3. Acceda a la dirección IP pública de la máquina virtual de salto: una vez implementada correctamente, vaya a la página de detalles de la máquina virtual y copie la dirección IP pública. Inicie sesión en la máquina virtual de salto mediante la dirección IP pública.
4. Inicie sesión en la máquina virtual de RHEL: copie la dirección IP privada de la máquina virtual de RHEL de la página de salida y úsela para iniciar sesión en la máquina virtual de RHEL desde la máquina virtual de salto.
5. Acceda a la página principal de JBoss EAP: en la máquina virtual de salto, abra un explorador y pegue la dirección URL de la aplicación que copió de la página de salida de la implementación.
6. Acceda a la consola de administrador de JBoss EAP: pegue la dirección URL de la consola de administrador que copió de la página de salida en un explorador dentro de la máquina virtual de salto para acceder a la consola de administrador JBoss EAP y escriba el nombre de usuario y la contraseña de JBoss EAP para iniciar sesión.

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. Exponga la aplicación mediante un equilibrador de carga externo

1. [Cree una puerta de enlace de aplicación](../../../application-gateway/quick-create-portal.md#create-an-application-gateway): para acceder a los puertos de la máquina virtual de RHEL, cree una puerta de enlace de aplicación en una subred diferente. La subred solo debe contener la puerta de enlace de aplicación.
2. Establezca parámetros de *front-end*: asegúrese de seleccionar la dirección IP pública o ambos y proporcione los detalles necesarios. En la sección *Back-ends*, seleccione la opción **Agregar un grupo de back-end** y agregue la máquina virtual de RHEL al grupo de back-end de la puerta de enlace de aplicación.
3. Establezca puertos de acceso: en la sección *Configuración*, agregue reglas de enrutamiento para acceder a los puertos 8080 y 9990 de la máquina virtual de RHEL.
4. Copiar la dirección IP pública de la puerta de enlace de aplicación: una vez creada  la puerta de enlace de aplicación con las configuraciones necesarias, vaya a la página de información general y copie la dirección IP pública de la puerta de enlace de aplicación.
5. Para ver la página web de JBoss EAP en Azure: abra un explorador web y vaya a *http://<PUBLIC_IP_AppGateway>:8080* y debería ver la página principal de EAP predeterminada.
6. Para iniciar sesión en la consola de administrador de JBoss EAP: abra un explorador web y vaya a *http://<PUBLIC_IP_AppGateway>:9990*. Escriba el nombre de usuario y la contraseña de JBoss EAP para iniciar sesión.

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. Use un equilibrador de carga externo (ELB) para acceder a la máquina virtual o a los conjuntos de escalado de máquinas virtuales de RHEL.

1. [Cree un equilibrador de carga](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) para acceder a los puertos de la máquina virtual de RHEL. Proporcione los detalles necesarios para implementar el equilibrador de carga externo y deje otras configuraciones como valor predeterminado. Deje la SKU como básico para la configuración del ELB.
2. Agregue reglas de equilibrador de carga: una vez que el equilibrador de carga se haya creado correctamente, [cree recursos de equilibrador de carga](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) y, a continuación, agregue reglas de equilibrador de carga para acceder a los puertos 8080 y 9990 de la máquina virtual RHEL.
3. Agregue la máquina virtual de RHEL al grupo de back-end del equilibrador de carga: haga clic en *Grupos de back-end* en la sección de configuración y, a continuación, seleccione el grupo de back-end que creó en el paso anterior. Seleccione la máquina virtual correspondiente a la opción *Asociado a* y agregue la máquina virtual de RHEL.
4. Para obtener la dirección IP pública del equilibrador de carga: vaya a la página de información general del equilibrador de carga y copie la dirección IP pública del equilibrador de carga.
5. Para ver la página web de JBoss EAP en Azure: abra un explorador web y vaya a *http://<PUBLIC_IP_LoadBalancer>:8080/* y debería ver la página principal de EAP predeterminada.
6. Para iniciar sesión en la consola de administrador de JBoss EAP: abra un explorador web y vaya a *http://<PUBLIC_IP_LoadBalancer>:9990*. Escriba el nombre de usuario y la contraseña de JBoss EAP para iniciar sesión.

### <a name="6-use-azure-bastion-to-connect"></a>6. Use Azure Bastion para conectarse 

1. Cree un host de Azure Bastion para la red virtual en la que se encuentra la máquina virtual de RHEL. El servicio Bastion se conectará automáticamente a la máquina virtual de RHEL mediante SSH.
2. Cree los roles de lector en la máquina virtual, NIC con dirección IP privada de la máquina virtual y recurso de Azure Bastion.
3. El puerto de entrada de RHEL está abierto para SSH (22). 
4. Conéctese con su nombre de usuario y contraseña en Azure Portal. Haga clic en "Conectar" y seleccione "Bastion" en la lista desplegable. A continuación, seleccione "Conectar" para conectarse a la máquina virtual de RHEL. Puede conectarse mediante un archivo de clave privada o una clave privada almacenada en Azure Key Vault.

## <a name="azure-platform"></a>Plataforma Azure

- **Error de validación**: la implementación no se iniciará si no se cumplen los criterios de parámetro (por ejemplo, no se cumplieron los criterios de contraseña de administrador) o si no se proporcionan alguno de los parámetros obligatorios en la sección de parámetros. Puede revisar los detalles de los parámetros antes de hacer clic en *Crear*.
- **Error de aprovisionamiento de recursos**: una vez que se inicia la implementación, los recursos que se implementan estarán visibles en la página de implementación. Si se produce un error de implementación después del proceso de validación de parámetros, hay disponible un mensaje de error más detallado. 
- **Error de aprovisionamiento de máquinas virtuales**: si se produce un error en la implementación en el punto de recursos de la **extensión de script personalizado de la máquina virtual**, hay un mensaje de error más detallado disponible en el archivo de registro de la máquina virtual. Consulte la sección siguiente para obtener más información sobre la solución de problemas.

## <a name="troubleshooting-eap-deployment-extension"></a>Solución de problemas de la extensión de implementación de EAP

Estas ofertas usan extensiones de script personalizado de máquina virtual para implementar JBoss EAP y configurar el usuario de administración de JBoss EAP. La implementación puede producir un error aquí por varios motivos, como por ejemplo:

- Derechos de RHSM o EAP no válidos
- Identificador de grupo de derechos de sistema operativo de JBoss EAP o RHEL no válido
- No se aceptan los términos y condiciones de Azure Marketplace para máquinas virtuales de RHEL

Siga los pasos que se indican a continuación para continuar con la solución de la incidencia:

1. Inicie sesión en la máquina virtual aprovisionada a través de SSH. Puede recuperar la dirección IP pública de la máquina virtual desde la página de información general de máquina virtual de Azure Portal.
1. Cambie a usuario de raíz

    ```cli
    sudo su -
    ```

1. Escriba la contraseña de administrador de la máquina virtual cuando se le pida.
1. Cambie el directorio al directorio de registro

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. Revise los registros en el archivo de registro eap.log.

    ```cli
    more eap.log
    ```

El archivo de registro tendrá detalles que incluyen el motivo del error de implementación y posibles soluciones. Si la implementación no se pudo realizar debido a derechos o cuentas RHSM, consulte la sección "Notas de soporte técnico y suscripción" para completar los requisitos previos. A continuación, inténtelo de nuevo. Al implementar EAP en el plan en clúster de RHEL, asegúrese de que la implementación no alcanza el límite de cuota. Es importante comprobar las cuotas de CPU virtuales de la serie de VM y vCPU regionales antes de proporcionar el recuento de instancias para la implementación. Si la suscripción o región no tiene límite de cuota suficiente, [solicite cuota](../../../azure-portal/supportability/regional-quota-requests.md) de su Azure Portal.

Consulte [Uso de la extensión de la versión 2 de script personalizado de Azure con máquinas virtuales Linux](../../extensions/custom-script-linux.md) para más información sobre la solución de problemas de las extensiones de script personalizado de máquina virtual.

## <a name="resource-links-and-support"></a>Vínculos de recursos y soporte técnico

Para cualquier pregunta relacionada con el soporte técnico, incidencias o requisitos de personalización, póngase en contacto con el [soporte técnico de Red Hat](https://access.redhat.com/support) o el [soporte técnico de Microsoft Azure](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* Más información sobre [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/)
* [JBoss EAP en Red Hat OpenShift en Azure](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP en Azure App Service](/azure/developer/java/ee/jboss-on-azure) 
* [Ventajas híbridas de Azure](../../windows/hybrid-use-benefit-licensing.md)
* [Administración de la suscripción de Red Hat](https://access.redhat.com/products/red-hat-subscription-management)
* [Documentación de Microsoft para Red Hat en Azure](./overview.md)

## <a name="next-steps"></a>Pasos siguientes

* Implementación de JBoss EAP en máquinas virtuales o en un conjunto de escalado de máquinas virtuales de RHEL desde [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Implementación de JBoss EAP en máquinas virtuales o en un conjunto de escalado de máquinas virtuales de RHEL desde [Inicio rápido de Azure](https://aka.ms/Quickstart-JBoss-EAP)
* Configuración de una aplicación Java para [Azure App Service](../../../app-service/configure-language-java.md)
* Tutorial "Cómo implementar [JBoss EAP en Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)"
* Use la [asistencia para la migración de Azure App Service](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Uso del [kit de herramientas de migración para aplicaciones](https://developers.redhat.com/products/mta) de Red Hat
