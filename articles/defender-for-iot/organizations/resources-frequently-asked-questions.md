---
title: Preguntas más frecuentes sobre Defender para IoT
description: Encuentre respuestas a las preguntas más frecuentes sobre las características y servicios de Azure Defender para IoT.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: e73096dbdee070a74daf700578e59f4ac181db42
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018418"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Preguntas más frecuentes sobre Azure Defender para IoT

En este artículo se proporciona una lista de las preguntas más frecuentes y las respuestas sobre Defender para IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>¿Cuál es la propuesta de valor propia de Azure para la seguridad de IoT?

Defender para IoT permite a las empresas ampliar su vista existente de la seguridad cibernética a toda la solución de IoT. Azure proporciona una vista integral de su solución empresarial, lo que le permite realizar acciones relacionadas con la empresa y tomar decisiones según su posición de seguridad empresarial y los datos recopilados. La seguridad combinada mediante Azure IoT, Azure IoT Edge y Azure Security Center le permite crear la solución que quiere con la seguridad que necesita.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Nuestra organización usa protocolos industriales patentados que no son estándar. ¿Se admiten? 

Azure Defender para IoT ofrece compatibilidad completa con protocolos. Además de la compatibilidad con el protocolo incrustado, puede proteger los dispositivos IoT y OT que ejecutan protocolos patentados y personalizados, o protocolos que se desvían de cualquier estándar. Con el SDK de Horizon Open Development Environment (ODE), los desarrolladores pueden crear complementos de disección que descodifican el tráfico basado en protocolos definidos. Los servicios analizan el tráfico para ofrecer supervisión, alertas e informes completos. Use Horizon para:
- Ampliar la visibilidad y control sin necesidad de actualizar a nuevas versiones.
- Proteger la información de su propiedad al desarrollar el protocolo del sitio como complemento externo. 
- Localizar el texto de las alertas, eventos y parámetros del protocolo.

Esta solución única para desarrollar protocolos como complementos no requiere de equipos de desarrolladores dedicados ni lanzamientos de versión para admitir un nuevo protocolo. Los desarrolladores, los socios y los clientes pueden desarrollar protocolos y compartir información y conclusiones con Horizon de forma segura. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>¿Tengo que comprar dispositivos de hardware de los socios de Microsoft?
El sensor de Azure Defender para IoT se ejecuta con especificaciones de hardware específicas, como se describe en la [guía de especificaciones de hardware](./how-to-identify-required-appliances.md). Los clientes pueden adquirir hardware certificado de los socios de Microsoft o usar la lista de materiales (BOM) suministrada y adquirirlos por su cuenta. 

El hardware certificado se ha probado en nuestros laboratorios para garantizar la estabilidad del controlador, la colocación de paquetes y el tamaño de la red.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>La normativa no nos permite conectar nuestro sistema a Internet. ¿De todos modos podemos usar Defender para IoT?

Sí, puede hacerlo. La solución en el entorno local de la plataforma de Azure Defender para IoT se implementa como un dispositivo de sensor físico o virtual que ingiere de manera pasiva el tráfico (a través de SPAN, RSPAN o TAP) para analizar, detectar y supervisar continuamente redes de TI, OT y IoT. En el caso de las empresas de mayor tamaño, varios sensores pueden agregar sus datos a una consola de administración local.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>¿En qué parte de la red deberían conectarse los puertos de supervisión?

El sensor de Azure Defender para IoT se conecta a un puerto SPAN o a un punto de acceso de terminal de red e inmediatamente comienza a recopilar el tráfico de ICS mediante supervisión pasiva (sin agente). No tiene ningún impacto en las redes OT, porque no se coloca en la ruta de acceso de datos y no examina de manera activa los dispositivos OT.

Por ejemplo:
- Un único dispositivo (virtual o físico) puede estar en el nivel de la red perimetral de la planta, de modo que todo el tráfico de las celdas de la planta se enrutan a este nivel.
- También puede colocar sensores pequeños en cada celda de la planta mediante la administración local o en la nube que residirá en el nivel de la red perimetral de la planta. Otro dispositivo (virtual o físico) puede supervisar el tráfico en el nivel de la red perimetral de la planta (para SCADA, Historian o MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>¿Cómo es Defender para IoT en relación con la competencia?

Azure Defender para IoT ofrece seguridad completa en todos los dispositivos IoT/OT. Para las **organizaciones de usuarios finales**, Azure Defender para IoT ofrece una seguridad de capa de red sin agentes que se implementa rápidamente, funciona con diversos equipos de OT propietarios y sistemas Windows heredados, e interopera con Azure Sentinel y otras herramientas de SOC. Se puede implementar de forma local o en entornos conectados de Azure. En el caso de los **compiladores de dispositivos IoT**, Azure Defender para IoT ofrece agentes ligeros para insertar seguridad de nivel de dispositivo en nuevas iniciativas IoT/OT.

## <a name="do-i-have-to-be-an-azure-customer"></a>¿Tengo que ser cliente de Azure?

No, para la versión sin agente de Azure Defender para IoT no es necesario ser cliente de Azure. Sin embargo, si desea enviar alertas a Azure Sentinel, aprovisionar sensores de red y supervisar su estado desde la nube y beneficiarse de las actualizaciones automáticas de software e inteligencia sobre amenazas, tendrá que conectar el sensor a Azure a través de Azure IoT Hub.

Para la versión basada en agente de Azure Defender para IoT, debe ser cliente de Azure.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>¿Qué ocurre cuando la conexión a Internet deja de funcionar?

Los sensores y agentes siguen ejecutándose y almacenan los datos, siempre y cuando el dispositivo esté en ejecución. Los datos se almacenan en la caché de mensajes de seguridad según la configuración de tamaño. Cuando el dispositivo vuelve a tener conectividad, se reanuda el envío de los mensajes de seguridad.

## <a name="how-can-i-change-a-users-passwords"></a>Procedimiento para cambiar las contraseñas de un usuario

Aprenda a [cambiar la contraseña de un usuario](how-to-create-and-manage-users.md#change-a-users-password) para el sensor o la consola de administración local.

También puede [recuperar la contraseña de la consola de administración local o el sensor](how-to-create-and-manage-users.md#recover-the-password-for-the-on-premises-management-console-or-the-sensor).

## <a name="how-do-i-activate-the-sensor-and-on-premises-management-console"></a>Procedimiento para activar la consola de administración local y el sensor

Para obtener información sobre cómo activar el sensor, vea [Inicio de sesión y activación del sensor](how-to-activate-and-set-up-your-sensor.md#sign-in-and-activate-the-sensor).

Para obtener información sobre cómo activar la consola de administración local, vea [Activación de la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md#activate-the-on-premises-management-console).

## <a name="how-to-change-the-network-configuration"></a>Procedimiento para cambiar la configuración de red

Puede ver cómo [actualizar la configuración de red del sensor antes de la activación](how-to-activate-and-set-up-your-sensor.md#update-sensor-network-configuration-before-activation).

También puede [actualizar la configuración de red del sensor](how-to-manage-individual-sensors.md#update-the-sensor-network-configuration) después de la activación.

Puede trabajar con [comandos](references-work-with-defender-for-iot-cli-commands.md#network-configuration) de la CLI para [cambiar las configuraciones de red](references-work-with-defender-for-iot-cli-commands.md#network-configuration).

## <a name="how-do-i-check-the-sanity-of-my-deployment"></a>Procedimiento para comprobar la integridad de la implementación

Después de instalar el software para el sensor o la consola de administración local, querrá realizar la [validación posterior a la instalación](how-to-install-software.md#post-installation-validation). Allí aprenderá a [comprobar el estado del sistema mediante la CLI](how-to-install-software.md#check-system-health-by-using-the-cli), a realizar una comprobación de [integridad](how-to-install-software.md#sanity) y a revisar las [estadísticas generales del sistema](how-to-install-software.md#system).

Puede seguir estos vínculos si [el dispositivo no responde ](how-to-install-software.md#the-appliance-isnt-responding) o [no se puede conectar mediante una interfaz web](how-to-install-software.md#you-cant-connect-by-using-a-web-interface).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los primeros pasos con Defender para IoT, consulte los siguientes artículos:

- Lea la [información general](overview.md) de Defender para IoT.
- Compruebe los [requisitos previos del sistema](quickstart-system-prerequisites.md).
- Obtenga más información sobre cómo [comenzar a usar Defender para IoT](getting-started.md).
