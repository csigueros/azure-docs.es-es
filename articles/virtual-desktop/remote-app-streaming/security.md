---
title: 'Guía de seguridad para aplicaciones entre organizaciones de Azure Virtual Desktop: Azure'
description: Guía para proteger las aplicaciones que hospeda en Azure Virtual Desktop en varias organizaciones.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 6ddb30832db4e504e57296d00db45125f8514afd
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178525"
---
# <a name="security-guidelines-for-cross-organizational-apps"></a>Directrices de seguridad para aplicaciones entre organizaciones

Al hospedar o transmitir aplicaciones en Azure Virtual Desktop, se llega a una amplia variedad de usuarios dentro y fuera de la organización. Como resultado, es muy importante saber cómo mantener la implementación segura para que pueda proteger su organización y sus clientes. Esta guía ofrece una descripción básica de los posibles problemas de seguridad y cómo abordarlos.

## <a name="shared-responsibility"></a>Responsabilidad compartida

Antes de Azure Virtual Desktop, las soluciones de virtualización locales como los servicios de Escritorio remoto requieren conceder a los usuarios acceso a roles como Puerta de enlace, Agente o Acceso web, entre otros. Estos roles tenían que ser totalmente redundantes y ser capaces de controlar la capacidad máxima. Los administradores instalarían estos roles como parte del sistema operativo Windows Server y tenían que estar unidos a un dominio con puertos específicos accesibles para las conexiones públicas. Para mantener las implementaciones seguras, los administradores tenían que asegurarse constantemente de que todo lo que había en la infraestructura se mantiene y está actualizado.

En una implementación de Azure Virtual Desktop, Microsoft administra parte de los servicios en nombre del cliente. En concreto, Microsoft hospeda y administra los elementos de infraestructura como parte del servicio. Los asociados y clientes ya no tienen que administrar manualmente la infraestructura necesaria para permitir que los usuarios accedan a las máquinas virtuales (VM) del host de sesión. El servicio también tiene funcionalidades de seguridad avanzadas integradas, como la conexión inversa, que reduce el riesgo que implica permitir a los usuarios acceder a sus escritorios remotos desde cualquier lugar.

Para mantener el servicio flexible, los hosts de sesión se hospedan en la suscripción de Azure del asociado o de los clientes. Esto permite a los clientes integrar el servicio con otros servicios de Azure y les permite conectar la infraestructura de red local con ExpressRoute o una red privada virtual (VPN).

Al igual que muchos servicios en la nube, hay un [conjunto de responsabilidades de seguridad](../../security/fundamentals/shared-responsibility.md) compartidas entre usted y Microsoft. Al usar Azure Virtual Desktop, es importante comprender que, aunque algunas partes del servicio están protegidas por usted, hay otras que deberá configurar según las necesidades de seguridad de su organización.

Deberá configurar la seguridad en las siguientes áreas:

- Dispositivos de usuario final
- Seguridad de la aplicación
- Sistemas operativos de host de sesión
- Configuración de la implementación
- Controles de red

Para más información sobre cómo configurar cada una de estas áreas, consulte nuestros [procedimientos recomendados de seguridad](./../security-guide.md).

## <a name="combined-microsoft-security-platform"></a>Plataforma de seguridad de Microsoft combinada

Puede proteger las cargas de trabajo mediante características de seguridad y controles de Microsoft 365, Azure y Azure Virtual Desktop.

Cuando el usuario se conecta al servicio a través de Internet, Azure Active Directory (Azure AD) autentica las credenciales del usuario, habilitando características de protección como [Acceso condicional](../../active-directory/conditional-access/overview.md) y la [autenticación multifactor](../../active-directory/authentication/concept-mfa-howitworks.md). Estas características reducen considerablemente el riesgo de que las identidades de usuario estén el peligro.

Azure Virtual Desktop tiene características como [Conexión inversa](../network-connectivity.md#reverse-connect-transport) que permiten a los usuarios acceder al host de sesión sin tener que abrir puertos de entrada. Esta característica está diseñada pensando en la escalabilidad y el servicio, por lo que tampoco debe limitar la capacidad de expandir los hosts de sesión. También puede usar GPO existentes con esta característica para aplicar seguridad adicional con compatibilidad con máquinas virtuales unidas Active Directory o, para hosts de sesión de Windows 10 que podrían implicar escenarios de Azure Active Directory Join, [Microsoft Endpoint Manager](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

## <a name="defense-in-depth"></a>Defensa en profundidad

El panorama de amenazas actual requiere diseños con enfoques de seguridad en mente. Lo ideal es crear una serie de mecanismos de seguridad y controles en capas en toda la red del equipo para proteger los datos y la red frente a ataques o amenazas. Este tipo de diseño de seguridad es lo que la Agencia de ciberseguridad y seguridad de infraestructuras de Estados Unidos (CISA) llama "defensa en profundidad". Para obtener más información sobre la defensa en profundidad, vaya al [sitio web de CISA](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth).

## <a name="session-host-operating-systems"></a>Sistemas operativos de host de sesión

Azure Virtual Desktop admite las imágenes de SO x64 siguientes:

- Windows 7
- Windows 10 Enterprise
- Windows Server 2012 R2, 2016 y 2019
- Sesión múltiple de Windows 10 Enterprise

Windows 10 Enterprise para sesiones múltiples es un sistema operativo exclusivo de Azure que funciona como un host de sesión de Escritorio remoto (RDSH) que permite varias sesiones interactivas simultáneas, de forma muy parecida a Windows Server. El entorno de Windows 10 proporciona a los usuarios una experiencia familiar y coherente. Las licencias de Windows por usuario del servicio también reducen los costes de toda la organización, ya que el departamento de TI no tiene que seguir comprando licencias de acceso de cliente (CAL) de RDS. Debido a estas ventajas, Windows 10 Enterprise para sesiones múltiples es el sistema operativo más implementado para hosts de sesión en Azure Virtual Desktop.

## <a name="security-boundaries"></a>Límites de seguridad

Los límites de seguridad separan el código y los datos de los dominios de seguridad con distintos niveles de confianza. Por ejemplo, normalmente hay un límite de seguridad entre el modo kernel y el modo de usuario. La mayoría de los servicios y software de Microsoft dependen de varios límites de seguridad para aislar dispositivos en redes, máquinas virtuales y aplicaciones en dispositivos. En la tabla siguiente se enumeran los límites de seguridad de Windows y lo que hacen para la seguridad general.

| Límite de seguridad             | Lo que hace el límite      |
|-----------------------------------|--------------------------------------|
| Límite de red              | Un punto de conexión de red no autorizado no puede alterar ni acceder al código ni a los datos del dispositivo de un cliente.                                                                   |
| Límite de kernel               | Un proceso en modo usuario no administrativo no puede acceder ni manipular el código y los datos del núcleo. La relación entre el administrador y el núcleo no es un límite de seguridad.                             |
| Límite de proceso               | Un proceso en modo usuario no autorizado no puede alterar ni acceder al código ni a los datos de otro proceso.                                                                      |
| Límite de espacio aislado de AppContainer  | Un proceso de espacio aislado basado en AppContainer no puede alterar ni acceder al código ni a los datos que hay fuera del espacio aislado en función de las características del contenedor.                               |
| Límite de usuario                  | Un usuario no puede alterar ni acceder al código ni a los datos de otro usuario sin autorización.                                                                           |
| Límite de sesión               | Una sesión de un usuario no puede alterar ni acceder a la sesión de otro usuario sin autorización.                                                                    |
| Límite de explorador web           | Un sitio web no autorizado no puede infringir la directiva de mismo origen y tampoco puede alterar ni acceder al código nativo ni a los datos del espacio aislado del explorador web Microsoft Edge.       |
| Límite de máquina virtual       | Una máquina virtual invitada de Hyper-V no autorizada no puede alterar ni acceder al código ni a los datos de otra máquina virtual invitada. Esto incluye los contenedores aislados de Hyper-V. |
| Límite del modo de seguridad virtual (VSM)  | El código que se ejecuta fuera del enclave o proceso de confianza de VSM no puede acceder a los datos y el código dentro del proceso de confianza ni alterarlo.                              |

## <a name="security-features"></a>Características de seguridad

Las características de seguridad se basa en los límites de seguridad para reforzar la protección contra amenazas específicas. Sin embargo, en algunos casos, puede haber limitaciones por diseño que impidan que la característica de seguridad proteja completamente la implementación.

Azure Virtual Desktop admite la mayoría de las características de seguridad disponibles en Windows. Las características de seguridad que se basan en características de hardware, como (v)TPM o virtualización anidada, pueden requerir una instrucción de soporte técnico independiente del equipo de Azure Virtual Desktop.

Para obtener más información sobre la compatibilidad y el mantenimiento de las características de seguridad, consulte nuestros [criterios de mantenimiento de seguridad de Microsoft Windows](https://www.microsoft.com/msrc/windows-security-servicing-criteria).

## <a name="recommended-security-boundaries-for-azure-virtual-desktop-scenarios"></a>Límites de seguridad recomendados para escenarios de Azure Virtual Desktop

También deberá tomar determinadas decisiones sobre los límites de seguridad caso por caso. Por ejemplo, si un usuario de su organización necesita privilegios administrativos locales para instalar aplicaciones, deberá darles un escritorio personal en lugar de un RDSH compartido. No se recomienda conceder privilegios de administrador local a los usuarios en escenarios de varias sesiones agrupadas, ya que estos usuarios pueden cruzar los límites de seguridad para sesiones o permisos de datos NTFS, apagar máquinas virtuales de varias sesiones o realizar otras cosas que podrían interrumpir el servicio o provocar pérdidas de datos.

Los usuarios de la misma organización, como los trabajadores del conocimiento con aplicaciones que no requieren privilegios de administrador, son excelentes candidatos para los hosts de sesión de Escritorio remoto de varias sesiones, como Windows 10 Enterprise para sesiones múltiples. Estos hosts de sesión reducen los costes de la organización porque varios usuarios pueden compartir una sola máquina virtual, con solo los costes de sobrecarga de un único sistema operativo. Con tecnología de perfiles como FSLogix, a los usuarios se les puede asignar cualquier máquina virtual de un grupo host sin que noten ninguna interrupción del servicio. Esta característica también le permite optimizar los costes haciendo cosas como apagar las máquinas virtuales durante las horas de menos actividad.

Si su situación requiere que usuarios de distintas organizaciones se conecten a la implementación, se recomienda tener un inquilino independiente para servicios de identidad como Active Directory y Azure AD. También se recomienda tener una suscripción independiente para hospedar recursos de Azure como Azure Virtual Desktop y máquinas virtuales.

En la tabla siguiente se enumeran nuestras recomendaciones para cada escenario.

| Escenario de nivel de confianza                                 | Solución recomendada                |
|------------------------------------------------------|-------------------------------------|
| Usuarios de una organización con privilegios estándar | Sesión múltiple de Windows 10 Enterprise |
| Usuarios que requieren privilegios administrativos             | Escritorios personales (VDI)             |
| Usuarios de diferentes organizaciones que se conectan        | Suscripción independiente de Azure         |

Echemos un vistazo a nuestras recomendaciones para algunos escenarios de ejemplo.

### <a name="should-i-share-identity-resources-to-reduce-costs"></a>¿Debo compartir recursos de identidad para reducir los costes?

Actualmente no se recomienda usar un sistema de identidad compartido en Azure Virtual Desktop. Se recomienda tener recursos de identidad independientes que implemente en una suscripción de Azure independiente. Estos recursos incluyen Active Directories, Azure AD y cargas de trabajo de máquina virtual. Cada usuario que trabaje para una organización individual necesitará infraestructura adicional y costes de mantenimiento asociados, pero esta es actualmente la solución más factible por motivos de seguridad. 

### <a name="should-i-share-a-multi-session-remote-desktop-rd-session-host-vm-to-reduce-costs"></a>¿Debo compartir una máquina virtual de host de sesión de Escritorio remoto para reducir los costes?

Los hosts de sesión de Escritorio remoto de varias sesiones ahorran costes al compartir recursos de hardware como CPU y memoria entre los usuarios. Este uso compartido de recursos permite diseñar la capacidad máxima, incluso si es poco probable que todos los usuarios necesiten el máximo de recursos al mismo tiempo. En un entorno compartido de varias sesiones, los recursos de hardware se comparten y asignan para reducir la brecha entre el uso y los costes.

En muchos casos, el uso de varias sesiones es una manera aceptable de reducir los costes, pero si se recomienda depende del nivel de confianza entre los usuarios con acceso simultáneo a una instancia compartida de varias sesiones. Normalmente, los usuarios que pertenecen a la misma organización tienen una relación de confianza suficiente y acordada. Por ejemplo, un departamento o grupo de trabajo en el que las personas colaboren y puedan acceder a la información personal de los demás es una organización con un alto nivel de confianza.

Windows usa controles y límites de seguridad para garantizar que los procesos y los datos de los usuarios estén aislados entre sesiones. Sin embargo, Windows proporciona acceso a la instancia en la que está trabajando el usuario.

Esta implementación se beneficiaría de una estrategia de seguridad en profundidad que agrega más límites de seguridad que impiden que los usuarios dentro y fuera de la organización obtengan acceso no autorizado a la información personal de otros usuarios. El acceso no autorizado a los datos se produce debido a un error en el proceso de configuración por parte del administrador del sistema, como una vulnerabilidad de seguridad no revelada o una vulnerabilidad conocida que aún no se ha puesto en revisión.

Por otro lado, Microsoft no admite la concesión de acceso al mismo entorno de varias sesiones a los usuarios que trabajan para empresas diferentes o en la competencia. Estos escenarios tienen varios límites de seguridad que pueden ser atacados o aprovechados, como la red, el kernel, el proceso, el usuario o las sesiones. Una única vulnerabilidad de seguridad podría provocar el robo de datos y credenciales no autorizados, pérdidas de información personal, robo de identidades y otros problemas. Los proveedores de entornos virtualizados son responsables de ofrecer sistemas bien diseñados con varios límites de seguridad seguros y características de seguridad adicionales habilitadas siempre que sea posible.

Reducir estas posibles amenazas requiere una configuración a prueba de errores, un proceso de diseño de administración de revisiones y programaciones periódicas de implementación de revisiones. Es mejor seguir los principios de defensa en profundidad y mantener los entornos separados.

## <a name="next-steps"></a>Pasos siguientes

Busque nuestras directrices recomendadas para configurar la seguridad para la implementación de Azure Virtual Desktop en nuestros [procedimientos recomendados de seguridad](./../security-guide.md).