---
title: Arquitectura para implementar aplicaciones de Oracle en Azure Virtual Machines | Microsoft Docs
description: Arquitecturas de aplicaciones para implementar aplicaciones de Oracle como E-Business Suite, JD Edwards EnterpriseOne y PeopleSoft en Microsoft Azure Virtual Machines con bases de datos en Azure o en Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: ac9c699addf996178b1d6aabdf010267bda75964
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690073"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquitecturas para implementar aplicaciones de Oracle en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

Microsoft y Oracle han trabajado en conjunto para permitir que los clientes implementen aplicaciones de Oracle como E-Business Suite, JD Edwards EnterpriseOne y PeopleSoft en la nube. Con la introducción de la [interconectividad de red privada](configure-azure-oci-networking.md) entre Microsoft Azure y Oracle Cloud Infrastructure (OCI), las aplicaciones de Oracle ahora se pueden implementar en Azure con sus bases de datos de back-end en Azure o en OCI. Las aplicaciones de Oracle también se pueden integrar con Azure Active Directory, lo que le permite configurar el inicio de sesión único para que los usuarios puedan iniciar sesión en la aplicación de Oracle con sus credenciales de Azure Active Directory (Azure AD).

OCI ofrece varias opciones de bases de datos de Oracle para las aplicaciones de Oracle, como DBaaS, Exadata Cloud Service, Oracle RAC e Infraestructura como servicio (IaaS). Actualmente, Autonomous Database no es un back-end compatible con las aplicaciones de Oracle.

Existen [varias opciones](oracle-overview.md) para implementar las aplicaciones de Oracle en Azure, incluso de una manera altamente disponible y segura. Azure también ofrece [imágenes de VM de bases de datos de Oracle](oracle-vm-solutions.md) que puede implementar si elige ejecutar las aplicaciones de Oracle completamente en Azure.

En las secciones siguientes se describen las recomendaciones de arquitectura tanto de Microsoft como de Oracle para implementar Oracle E-Business Suite, JD Edwards EnterpriseOne y PeopleSoft en una configuración entre nubes o completamente en Azure. Microsoft y Oracle han probado estas aplicaciones y confirmaron que el rendimiento cumple con los estándares que Oracle establece para estas aplicaciones.

## <a name="architecture-considerations"></a>Consideraciones sobre la arquitectura

Las aplicaciones de Oracle constan de varios servicios, los que pueden estar hospedados en la misma máquina virtual o en varias máquinas virtuales en Azure y, de manera opcional, en OCI. 

Las instancias de las aplicaciones se pueden configurar con puntos de conexión privados o públicos. Microsoft y Oracle recomiendan configurar una *máquina virtual host bastión* con una dirección IP pública en una subred independiente para la administración de la aplicación. Luego, asigne solo direcciones IP privadas a las otras máquinas, incluido el nivel de base de datos. 

Al configurar una aplicación en una arquitectura entre nubes, es necesario planear para garantizar que el espacio de direcciones IP de la red virtual de Azure no se superponga con el espacio de direcciones IP privadas de la red de la nube virtual de OCI.

Para mayor seguridad, configure los grupos de seguridad de red en un nivel de subred para garantizar que solo se permita el tráfico en direcciones IP y puertos específicos. Por ejemplo, las máquinas en el nivel intermedio solo deberían recibir tráfico desde dentro de la red virtual. Ningún tráfico externo debería llegar directamente a las máquinas del nivel intermedio.

Para lograr una alta disponibilidad, puede configurar instancias redundantes en los distintos servidores del mismo conjunto de disponibilidad o en distintas zonas de disponibilidad. Las zonas de disponibilidad le permiten alcanzar un SLA con un tiempo de actividad del 99,99 %, mientras que los conjuntos de disponibilidad le permiten alcanzar un SLA con un tiempo de actividad del 99,95 % en la región. Las arquitecturas de ejemplo que se muestran en este artículo se implementan en dos zonas de disponibilidad.

Al implementar una aplicación mediante la interconexión entre nubes, puede seguir usando un circuito ExpressRoute existente para conectar el entorno de Azure a la red local. Sin embargo, necesita un circuito ExpressRoute independiente para la interconexión con OCI del circuito que se conecta a la red local.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) es un conjunto de aplicaciones entre las que se incluyen la administración de cadenas de suministros (SCM) y la gestión de relaciones con el cliente (CRM). Para aprovechar la cartera de bases de datos administradas de OCI, EBS también se puede implementar mediante la interconexión entre nubes entre Microsoft Azure y OCI. En esta configuración, los niveles de presentación y de aplicación se ejecutan en Azure y el nivel de base de datos, en OCI, tal como se muestra en el diagrama de arquitectura siguiente (Figura 1).

![Arquitectura entre nubes de E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Arquitectura entre nubes de E-Business Suite* 

En esta arquitectura, la red virtual de Azure está conectada a una red de nube virtual en OCI mediante la interconexión entre nubes. La capa de aplicación está configurada en Azure, mientras que la base de datos se configura en OCI. Se recomienda implementar cada componente en su propia subred con grupos de seguridad de red para permitir el tráfico solo proveniente de subredes específicas en puertos específicos.

La arquitectura también se puede adaptar para la implementación completamente en Azure con bases de datos de Oracle altamente disponibles configuradas mediante Oracle Data Guard en dos zonas de disponibilidad en una región. El diagrama siguiente (Figura 2) es un ejemplo de este patrón de arquitectura:

![Arquitectura de E-Business Suite solo para Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Arquitectura de E-Business Suite solo para Azure*

En las secciones siguientes se describen los distintos componentes en un nivel alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Capa de aplicación (intermedia)

La capa de aplicación está aislada en su propia subred. Hay varias máquinas virtuales configuradas para la tolerancia a errores y una administración de revisiones sencilla. Estas máquinas virtuales pueden tener el respaldo del almacenamiento compartido ofrecido por Azure NetApp Files y el almacenamiento en discos Ultra. Esta configuración permite simplificar la implementación de las revisiones sin tiempo de inactividad. Las máquinas de la capa de aplicación deben estar dirigidas por un equilibrador de carga público para que se procesen las solicitudes a la capa de aplicación de EBS incluso si una máquina de la capa está sin conexión debido a un error.

### <a name="load-balancer"></a>Equilibrador de carga

Un equilibrador de carga de Azure le permite distribuir el tráfico entre varias instancias de la carga de trabajo para garantizar una alta disponibilidad. En este caso, se configura un equilibrador de carga público porque los usuarios pueden acceder a la aplicación de EBS a través de la Web. El equilibrador de carga distribuye la carga en ambas máquinas en el nivel intermedio. Para mayor seguridad, permita el tráfico solo de los usuarios que acceden al sistema desde la red corporativa mediante una VPN de sitio a sitio o ExpressRoute y grupos de seguridad de red.

### <a name="database-tier"></a>Nivel de base de datos

Este nivel hospeda la base de datos de Oracle y se encuentra aislado en su propia subred. Se recomienda agregar grupos de seguridad de red que solo permitan el tráfico desde la capa de aplicación al nivel de base de datos en el puerto 1521 de la base de datos específica de Oracle.

Microsoft y Oracle recomienda una configuración de alta disponibilidad. La alta disponibilidad en Azure se puede lograr mediante la configuración de dos bases de datos de Oracle en dos zonas de disponibilidad con Oracle Data Guard, o bien mediante Oracle Database Exadata Cloud Service en OCI. Al usar Oracle Database Exadata Cloud Service, la base de datos se implementa en dos subredes. También puede configurar Oracle Database en VM en OCI en dos dominios de disponibilidad con Oracle Data Guard.


### <a name="identity-tier"></a>Nivel de identidad

El nivel de identidad contiene la máquina virtual de EBS Asserter. EBS Asserter permite sincronizar identidades de Oracle Identity Cloud Service (IDCS) y Azure AD. EBS Asserter es necesario porque EBS no admite protocolos de inicio de sesión único como SAML 2.0 ni OpenID Connect. EBS Asserter usa el token de OpenID Connect (generado por IDCS), lo valida y luego crea una sesión para el usuario en EBS. 

Si bien esta arquitectura muestra la integración de IDCS, también se puede habilitar el inicio de sesión único y el acceso unificado de Azure AD con Oracle Access Manager con Oracle Internet Directory u Oracle Unified Directory. Para más información, consulte las notas sobre la [implementación de Oracle EBS con la integración de IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) o la [implementación de Oracle EBS con la integración de OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para lograr una alta disponibilidad, se recomienda implementar servidores redundantes de EBS Asserter en varias zonas de disponibilidad con un equilibrador de carga delante.

Una vez configurada la infraestructura, es posible instalar E-Business Suite según la guía de instalación que proporciona Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

JD Edwards EnterpriseOne de Oracle es un conjunto integrado de aplicaciones de software de planeamiento de recursos empresariales integral. Se trata de una aplicación de varios niveles que se puede configurar con un back-end de base de datos de Oracle o de SQL Server. En esta sección se describen los detalles de la implementación de JD Edwards EnterpriseOne con un back-end de base de datos de Oracle en OCI o en Azure.

En la arquitectura recomendada siguiente (Figura 3), los niveles de administración, presentación e intermedio se implementan en la red virtual de Azure. La base de datos se implementa en una red de nube virtual en OCI.

Al igual que con E-Business Suite, puede configurar un nivel de bastión opcional con fines administrativos seguros. Use el host de máquina virtual como servidor de salto para acceder a las instancias de aplicación y base de datos.

![Arquitectura entre nubes de JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Arquitectura entre nubes de JD Edwards EnterpriseOne*

En esta arquitectura, la red virtual de Azure está conectada a la red de nube virtual en OCI mediante la interconexión entre nubes. La capa de aplicación está configurada en Azure, mientras que la base de datos se configura en OCI. Se recomienda implementar cada componente en su propia subred con grupos de seguridad de red para permitir el tráfico solo proveniente de subredes específicas en puertos específicos.

La arquitectura también se puede adaptar para la implementación completamente en Azure con bases de datos de Oracle altamente disponibles configuradas mediante Oracle Data Guard en dos zonas de disponibilidad en una región. El diagrama siguiente (Figura 4) es un ejemplo de este patrón de arquitectura:

![Arquitectura de JD Edwards EnterpriseOne solo para Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Arquitectura de JD Edwards EnterpriseOne solo para Azure*

En las secciones siguientes se describen los distintos componentes en un nivel alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Nivel de administración

Tal como sugiere el nombre, este nivel se usa para las tareas administrativas. Puede establecer una subred independiente para el nivel de administración. Los servicios y servidores de este nivel se usan principalmente para la instalación y la administración de la aplicación. Por lo tanto, es suficiente con instancias únicas de estos servidores. No se requieren instancias redundantes para la alta disponibilidad de la aplicación.

Los componentes de este nivel son los siguientes:
    
 - **Servidor de aprovisionamiento**: este servidor se usa para la implementación total de los distintos componentes de la aplicación. Se comunica con las instancias en los otros niveles, incluidas las instancias en el nivel de base de datos, a través del puerto 22. Hospeda la consola del Administrador del servidor para JD Edwards EnterpriseOne.
 - **Servidor de implementación**: este servidor se requiere principalmente para la instalar de JD Edwards EnterpriseOne. Durante el proceso de instalación, este servidor actúa como el repositorio central de los paquetes de instalación y archivos requeridos. El software se distribuye o implementa en los demás servidores y clientes desde este servidor.
 - **Cliente de desarrollo**: este servidor contiene componentes que se ejecutan en un explorador web, así como también aplicaciones nativas.

### <a name="presentation-tier"></a>Nivel de presentación

Este nivel contiene varios componentes, como AIS (servicios de interfaz de aplicaciones), ADF (marco de desarrollo de aplicaciones) y JAS (servidores de aplicaciones de Java). Los servidores de este nivel se comunican con los servidores del nivel intermedio. Son dirigidos por un equilibrador de carga que enruta el tráfico al servidor necesario según el número de puerto y la dirección URL en que se recibe el tráfico. Se recomienda implementar varias instancias de cada tipo de servidor para lograr la alta disponibilidad.

Los componentes de este nivel son los siguientes:
    
- **AIS, servicios de interfaz de aplicaciones**: el servidor de AIS proporciona la interfaz de comunicación entre las aplicaciones empresariales móviles de JD Edwards EnterpriseOne y JD Edwards EnterpriseOne.
- **JAS, servidor de aplicaciones de Java**: JAS recibe las solicitudes del equilibrador de carga y las transmite al nivel intermedio para ejecutar tareas complicadas. JAS tiene la capacidad de ejecutar una lógica de negocios sencilla.
- **BIP, servidor de publicador de BI**: este servidor presenta informes basados en los datos que recopila la aplicación JD Edwards EnterpriseOne. Puede diseñar y controlar la manera en que el informe presenta los datos en función de distintas plantillas.
- **BSS, servidor de servicios de negocio**: BSS permite el intercambio de información y la interoperabilidad con otras aplicaciones de Oracle.
- **RTE, servidor de eventos en tiempo real**: el servidor RTE permite configurar notificaciones para los sistemas externos sobre las transacciones que se realizan en el sistema de JDE EnterpriseOne. Usa un modelo de suscriptor y permite que los sistemas de terceros se suscriban a los eventos. Para equilibrar la carga de las solicitudes a ambos servidores de RTE, asegúrese de que los servidores estén en un clúster.
- **Servidor ADF, marco de desarrollo de aplicaciones**: el servidor ADF se usa para ejecutar aplicaciones de JD Edwards EnterpriseOne desarrolladas con Oracle ADF. Se implementa en un servidor Oracle WebLogic con runtime de ADF.

### <a name="middle-tier"></a>Nivel intermedio

El nivel intermedio contiene el servidor lógico y el servidor de procesos por lotes. En este caso, ambos servidores están instalados en la misma máquina virtual. Sin embargo, en los escenarios de producción, se recomienda implementar el servidor lógico y el servidor de procesos por lotes en servidores separados. Son varios los servidores implementados en el nivel intermedio en dos zonas de disponibilidad para una mayor disponibilidad. Se debe crear un equilibrador de carga de Azure y estos servidores se deben colocar en su grupo de back-end para garantizar que ambos servidores estén activos y procesen las solicitudes.

Los servidores del nivel intermedio solo reciben solicitudes provenientes de los servidores en el nivel de presentación y del equilibrador de carga público. Es necesario configurar reglas de los grupos de seguridad de red para denegar el tráfico proveniente de cualquier dirección distinta de la subred del nivel de presentación y del equilibrador de carga. También se puede configurar una regla de NSG para permitir el tráfico en el puerto 22 proveniente del host bastión con fines administrativos. Es posible usar el equilibrador de carga público para equilibrar la carga de las solicitudes entre las máquinas virtuales del nivel intermedio.

Estos dos componentes se encuentran en el nivel intermedio:

- **Servidor lógico**: contiene la lógica de negocios o las funciones de negocios.
- **Servidor de procesos por lotes**: se usa para el procesamiento por lotes.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

El conjunto de aplicaciones PeopleSoft de Oracle contiene software para la administración financiera y de recursos humanos. El conjunto de aplicaciones tiene varios niveles y las aplicaciones incluyen sistemas de administración de recursos humanos (HRMS), administración de finanzas y cadenas de suministros (FSCM) y administración del rendimiento empresarial (EPM).

Se recomienda implementar cada nivel del conjunto de software en su propia subred. Se necesita una base de datos de Oracle o una instancia de Microsoft SQL Server como la base de datos de back-end para la aplicación. En esta sección se analizan los detalles de la implementación de PeopleSoft con un back-end de base de datos de Oracle.

A continuación, se muestra una arquitectura canónica para implementar el conjunto de aplicaciones de PeopleSoft en una arquitectura entre nubes (Figura 5).

![Arquitectura entre nubes de PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitectura entre nubes de PeopleSoft*

En esta arquitectura de ejemplo, la red virtual de Azure está conectada a la red de nube virtual en OCI mediante la interconexión entre nubes. La capa de aplicación está configurada en Azure, mientras que la base de datos se configura en OCI. Se recomienda implementar cada componente en su propia subred con grupos de seguridad de red para permitir el tráfico solo proveniente de subredes específicas en puertos específicos.

La arquitectura también se puede adaptar para la implementación completamente en Azure con bases de datos de Oracle altamente disponibles configuradas mediante Oracle Data Guard en dos zonas de disponibilidad en una región. El diagrama siguiente (Figura 6) es un ejemplo de este patrón de arquitectura:

![Arquitectura de PeopleSoft solo para Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitectura de PeopleSoft solo para Azure*

En las secciones siguientes se describen los distintos componentes en un nivel alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Nivel de aplicación

La capa de aplicación contiene instancias de los servidores de aplicación de PeopleSoft, servidores web de PeopleSoft, búsqueda elástica y el programador de procesos de PeopleSoft. Un equilibrador de carga de Azure está configurado para aceptar solicitudes de los usuarios que se enrutan al servidor adecuado en la capa de aplicación.

Para lograr una alta disponibilidad, considere la posibilidad de configurar instancias redundantes de cada servidor en la capa de aplicación en distintas zonas de disponibilidad. El equilibrador de carga de Azure se puede configurar con varios grupos de back-end para dirigir cada solicitud al servidor correcto.

### <a name="peopletools-client"></a>Cliente de PeopleTools

El cliente de PeopleTools se usa para realizar las actividades de administración, como desarrollo, migración y actualización. Como el cliente de PeopleTools no es necesario para lograr la alta disponibilidad de la aplicación, los servidores redundantes del cliente de PeopleTools no son necesarios.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Pasos siguientes

Use [scripts de Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicaciones de Oracle en Azure y establecer una conectividad entre nubes con OCI.

Para más información y las notas del producto acerca de OCI, consulte la documentación de [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm).
