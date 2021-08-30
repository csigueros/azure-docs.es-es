---
title: Uso de una instancia de App Service Environment
description: Aprenda a usar su instancia de App Service Environment para hospedar aplicaciones aisladas.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 07/06/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1d51fff9739ecb928ff2f11f53b58b190122d69d
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432831"
---
# <a name="using-an-app-service-environment"></a>Uso de App Service Environment

> [!NOTE]
> En este artículo se aborda App Service Environment v3, que se usa con planes de App Service v2 aislados.
> 

App Service Environment (ASE) es una implementación de inquilino único de Azure App Service que se inserta directamente en la instancia de Azure Virtual Network (red virtual) de su elección. Se trata de un sistema que solo un cliente usa. Las aplicaciones implementadas en el ASE están sujetas a las características de red que se aplican a la subred del ASE. Para estar sujetas a esas características de red, no hay características adicionales que deban habilitarse en ellas. 

## <a name="create-an-app-in-an-ase"></a>Creación de una aplicación en un entorno ASE

Para crear una aplicación en un entorno ASE, se usa el mismo proceso que al crearla normalmente, pero con pequeñas diferencias. Al crear un nuevo plan de App Service:

- En lugar de elegir una ubicación geográfica en la que implementar la aplicación, elija un entorno ASE como su ubicación.
- Todos los planes de App Service que se creen en un ASE solo pueden estar en un plan de tarifa v2 aislado.

Si no tiene un entorno ASE, puede crear uno según las instrucciones que aparecen en [Creación de un entorno en App Service Environment][MakeASE].
Para crear una aplicación en un entorno ASE:

1. Seleccione **Crear un recurso** > **Web y móvil** > **Aplicación web**.
1. Seleccione una suscripción.
1. Especifique un nombre para un grupo de recursos nuevo o seleccione **Usar existente** y seleccione uno en la lista desplegable.
1. Escriba un nombre para la aplicación. Si ya seleccionó un plan de App Service en un ASE, el nombre de dominio de la aplicación refleja el nombre de dominio del ASE: ![creación de una aplicación en un ASE][1]
1. Seleccione el tipo de publicación, la pila y el sistema operativo.
1. Seleccione la región. Aquí debe seleccionar una instancia ya existente de App Service Environment v3.  No se puede crear una instancia de ASEv3 durante la creación de la aplicación. 
1. Seleccione un plan de App Service ya existente en el ASE o cree uno. Si va a crear una aplicación, seleccione el tamaño que quiere para el plan de App Service. La única SKU que puede seleccionar para la aplicación es una SKU con el precio de v2 aislada. La creación de un plan de App Service normalmente llevará menos de 20 minutos. 
![Planes de tarifa de v2 aislada][2]
1. Seleccione **Siguiente: Supervisión**. Si quiere habilitar App Insights con la aplicación, puede hacerlo aquí durante el flujo de creación. 
1.  Seleccione **Siguiente: Etiquetas**: agregue las etiquetas que desee a la aplicación.  
1. Seleccione **Revisar y crear**, asegúrese de que la información es correcta y luego seleccione **Crear**.

Las aplicaciones de Windows y Linux pueden estar en el mismo ASE, pero no pueden estar en el mismo plan de App Service.

## <a name="how-scale-works"></a>Cómo funciona escalar

Cada aplicación de App Service se ejecuta en un plan de App Service. Las instancias de App Service Environment contienen planes de App Service, y estos incluyen aplicaciones. Cuando escala una aplicación, también escala el plan de App Service y todas las aplicaciones de ese mismo plan.

Cuando se escala horizontalmente un plan de App Service, se agrega la infraestructura necesaria de forma automática. Hay un retraso para escalar las operaciones mientras se agrega la infraestructura. Al escalar un plan de App Service, las demás operaciones de escalado que soliciten el mismo sistema operativo y tamaño esperarán hasta que se complete la primera. Una vez que finaliza la operación que impide el escalado, todas las solicitudes en cola se procesan al mismo tiempo. Una operación de escalado con un tamaño y un sistema operativo no impedirá el escalado de las otras combinaciones de tamaño y sistema operativo. Por ejemplo, si escaló un plan de App Service de Windows I2v2, todas las demás solicitudes de escalado de Windows I2v2 en ese ASE se pondrán en cola hasta que se complete. El escalado normalmente llevará menos de 20 minutos. 

En las instancias multiinquilino de App Service, el escalado es inmediato, ya que hay un grupo de destino disponible al instante para realizarlo. En un entorno ASE, no hay ningún búfer de este tipo y los recursos se asignan según sea necesario.

## <a name="app-access"></a>Acceso a las aplicaciones

En un ASE con una VIP interna, el sufijo del dominio que se utiliza para la creación de aplicaciones es *.&lt;asename&gt;.appserviceenvironment.net*. Si el ASE se llama _my-ase_ y hospeda una aplicación llamada _contoso_ en ese ASE, puede comunicarse con ella en las siguientes direcciones URL:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Las aplicaciones que se hospedan en un ASE que usa una VIP interna solo serán accesibles si se encuentra en la misma red virtual que el ASE o está conectado de algún modo a esa red virtual. La publicación también está restringida a ser posible solo si está en la misma red virtual o está conectado de algún modo a esa red virtual. 

En un ASE con una VIP externa, el sufijo del dominio que se utiliza para la creación de aplicaciones es *.&lt;asename&gt;.p.azurewebsites.net*. Si el ASE se llama _my-ase_ y hospeda una aplicación llamada _contoso_ en ese ASE, puede comunicarse con ella en las siguientes direcciones URL:

- contoso.my-ase.p.azurewebsites.net
- contoso.scm.my-ase.p.azurewebsites.net

Para información sobre cómo crear un ASE, consulte [Creación de una instancia de App Service Environment][MakeASE].

La dirección URL del SCM se utiliza para obtener acceso a la consola de Kudu o para publicar la aplicación mediante Web Deploy. Para información sobre la consola Kudu, consulte el artículo sobre la [consola Kudu para Azure App Service][Kudu]. La consola Kudu ofrece una UI web para depurar, cargar archivos, editar archivos y mucho más.

### <a name="dns-configuration"></a>Configuración de DNS 

Si el ASE se crea con una VIP externa, las aplicaciones se colocan automáticamente en el DNS público. Si el ASE se crea con una VIP interna, es posible que tenga que configurar el DNS para ella. Si ha seleccionado tener zonas privadas de Azure DNS configuradas automáticamente durante la creación del ASE, el DNS se configura en la red virtual del ASE. Si ha seleccionado configurar manualmente el DNS, debe usar su propio servidor DNS o configurar zonas privadas de Azure DNS. Para encontrar la dirección de entrada del ASE; vaya a la interfaz de usuario **Portal de ASE > Direcciones IP**. 

![Interfaz de usuario de direcciones IP][6]

Si quiere usar su propio servidor DNS, debe agregar los siguientes registros:

1. Cree una zona para el &lt;nombre de ASE&gt;.appserviceenvironment.net.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada usada por el ASE.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada usada por el ASE.
1. Cree una zona en el &lt;nombre de ASE&gt;.appserviceenvironment.net denominada SCM.
1. Cree un registro D en la zona SCM que apunte * a la dirección IP de entrada usada por el ASE.

Para configurar DNS en las zonas privadas de Azure DNS:

1. Cree una zona privada de Azure DNS denominada &lt;ASE name&gt;.appserviceenvironment.net.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte *.scm a la dirección IP de entrada.

La configuración de DNS para el sufijo de dominio predeterminado del ASE no restringe las aplicaciones a solo aquellas que son accesibles por esos nombres. Puede establecer un nombre de dominio personalizado sin ninguna validación en las aplicaciones de un ASE. Si quiere crear una zona denominada *contoso.net*, puede hacerlo y que apunte a la dirección IP de entrada. El nombre de dominio personalizado funciona para las solicitudes de aplicación, pero no para el sitio SCM. El sitio SCM solo está disponible en *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net*. 

## <a name="publishing"></a>Publicación

En una instancia de ASE, al igual que lo que ocurre con App Service multiinquilino, puede publicar con estos métodos:

- Implementación web
- Integración continua (CI)
- Operación de arrastrar y soltar en la consola Kudu
- Un IDE, como Visual Studio, Eclipse o IntelliJ IDEA

Con un ASE de VIP interna, los puntos de conexión de publicación solo están disponibles mediante la dirección de entrada. Si no tiene acceso de red a la dirección de entrada, no puede publicar ninguna aplicación en ese ASE.  Los IDE también deben tener acceso de red a la dirección de entrada en el ASE para publicar directamente en él.

Sin otros cambios, los sistemas de CI basados en Internet, como GitHub y Azure DevOps, no funcionan con un ASE de VIP interna, ya que el punto de conexión de publicación no es accesible desde Internet. Puede habilitar la publicación en un ASE de VIP interna desde Azure DevOps instalando un agente de versiones autohospedado en la red virtual que contiene el ASE. 

## <a name="storage"></a>Storage

Una instancia de ASE dispone de 1 TB de almacenamiento para todas las aplicaciones que contiene. El plan de App Service de la SKU del plan de tarifa Aislado tiene un límite de 250 GB. En un ASE, se agregan 250 GB de almacenamiento por plan de App Service hasta el límite de 1 TB. Puede tener más de cuatro planes de App Service, pero no se agregará más espacio de almacenamiento más allá del límite de 1 TB.

## <a name="logging"></a>Registro

Puede integrar la instancia de ASE con Azure Monitor para enviar registros sobre esta instancia de ASE a Azure Storage, Azure Event Hubs o Log Analytics. Estos elementos se registran hoy:

|Situación |Message |
|----------|--------|
|La subred de ASE casi no tiene espacio | La instancia de ASE especificada está en una subred que casi no tiene espacio. Quedan {0} direcciones. Una vez que se agoten estas direcciones, la instancia de ASE no se podrá escalar.  |
|ASE está a punto de alcanzar el límite total de instancias | La instancia de ASE especificada está a punto de alcanzar al límite total de instancias. Actualmente contiene {0} instancias de plan de App Service, de un máximo de 200. |
|La instancia de ASE está suspendida | La instancia de ASE especificada está suspendida. Esta suspensión puede deberse a que hay pocas cuentas o a que una configuración de red virtual no es válida. Resuelva la causa principal del problema y reanude la instancia de ASE para que siga atendiendo el tráfico. |
|Se ha iniciado una actualización de ASE | Se ha iniciado una actualización de la plataforma a la instancia de ASE especificada. Es posible que se produzcan retrasos en las operaciones de escalado. |
|Se ha completado una actualización de ASE | Ha finalizado una actualización de plataforma de la instancia de ASE especificada. |
|La creación del plan de App Service ha comenzado | Se ha iniciado la creación de un plan de App Service ({0}). Estado deseado: trabajos de {1} I{2}v2.
|Se han completado las operaciones de escalado | Ha finalizado la creación de un plan de App Service ({0}). Estado actual: trabajos de {1} I{2}v2. |
|Se han producido errores en las operaciones de escalado | Se ha producido un error en la creación de un plan de App Service ({0}). Puede deberse a que el ASE funciona en el número máximo de instancias o se ha quedado sin direcciones de subred. |
|Se han iniciado operaciones de escalado | Se ha comenzado a escalar un plan de App Service ({0}). Estado actual: {1} I(2)v2. Estado deseado: trabajos de {3} I{4}v2.|
|Se han completado las operaciones de escalado | Ha finalizado el escalado de un plan de App Service ({0}). Estado actual: trabajos de {1} I{2}v2. |
|Se interrumpieron las operaciones de escalado | Se interrumpió un plan de App Service ({0}) durante el escalado. Estado deseado anterior: trabajos de {1} I{2}v2. Nuevo estado deseado: trabajos de {3} I{4}v2. |
|Se han producido errores en las operaciones de escalado | No se ha podido escalar un plan de App Service ({0}). Estado actual: trabajos de {1} I{2}v2. |

Puede habilitar el registro en la instancia de ASE:

1. En el portal, vaya a **Configuración de diagnóstico**.
1. Seleccione **Agregar configuración de diagnóstico**.
1. Especifique un nombre para la integración de registros.
1. Seleccione y configure los destinos de registro que desee.
1. Seleccione **AppServiceEnvironmentPlatformLogs**.
![Configuración de registros de diagnóstico de ASE][4]

Si integra esta solución con Log Analytics, podrá ver los registros seleccionando **Registros** en el portal de ASE y creando una consulta en **AppServiceEnvironmentPlatformLogs**. Los registros solo se emiten cuando el ASE tiene un evento que lo desencadenará. Si el ASE no tiene ese tipo de evento, no habrá ningún registro. Para ver rápidamente un ejemplo de registros en el área de trabajo de Log Analytics, realice una operación de escalado con uno de los planes de App Service del ASE. Después, puede ejecutar una consulta en **AppServiceEnvironmentPlatformLogs** para ver esos registros. 

### <a name="creating-an-alert"></a>Creación de una alerta

Para crear una alerta sobre los registros, siga las instrucciones de [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../../azure-monitor/alerts/alerts-log.md). En resumen:

* Abra la página Alertas en el portal de ASE.
* Seleccione **Nueva regla de alertas**.
* Seleccione el recurso para que sea el área de trabajo de Log Analytics.
* Establezca la condición con una búsqueda de registros personalizada que use una consulta como "AppServiceEnvironmentPlatformLogs | donde ResultDescription contiene "ha empezado el escalado" o cualquier cosa que desee. Establezca el umbral según corresponda. 
* Agregue o cree un grupo de acciones según sus preferencias. En el grupo de acciones se define la respuesta a la alerta como, por ejemplo, enviar un correo electrónico o un mensaje SMS.
* Asigne un nombre a la alerta y guárdela.

## <a name="internal-encryption"></a>Cifrado interno

App Service Environment funciona como sistema de caja negra en el que no se pueden ver los componentes internos ni la comunicación dentro del sistema. Para permitir mayor capacidad de proceso, no se habilita el cifrado de forma predeterminada entre los componentes internos. El sistema es seguro, ya que no se puede acceder al tráfico ni supervisarlo. En cambio, si tiene un requisito de cumplimiento normativo que exige un cifrado completo de la ruta de acceso a los datos a partir del cifrado de un extremo a otro, puede habilitarlo en la interfaz de usuario de **configuración** del ASE.

![Habilitación del cifrado interno][5]

Esta función cifrará el tráfico de red interno en el ASE entre los servidores front-end y los trabajos, y también el archivo de paginación y los discos de trabajo. La habilitación de clusterSetting de InternalEncryption puede afectar al rendimiento del sistema. Al realizar el cambio para habilitar InternalEncryption, ASE tendrá un estado inestable hasta que se propague el cambio totalmente. La propagación completa del cambio puede tardar unas horas en completarse, en función de cuántas instancias tenga en ASE. Es muy recomendable no habilitar esto en ASE si este se encuentra en uso. Si es necesaria la hablitación en un ASE en uso, lo mejor es desviar el tráfico a un entorno de copia de seguridad hasta que se complete la operación.

## <a name="upgrade-preference"></a>Preferencia de actualización

Si tiene varias instancias de ASE, es posible que prefiera que unas se actualicen antes que otras. En el objeto **HostingEnvironment del ASE de Resource Manager**, puede establecer un valor en **upgradePreference**. El valor de **upgradePreference** se puede configurar utilizando una plantilla, ARMClient o https://resources.azure.com. Estos son los tres valores posibles:

- **Ninguna**: Azure actualizará la instancia de ASE en alguno de los lotes. Este es el valor predeterminado.
- **Early**: la instancia de ASE se actualizará durante la primera mitad de las actualizaciones de App Service.
- **Late**: la instancia de ASE se actualizará durante la segunda mitad de las actualizaciones de App Service.

Para configurar las preferencias de actualización, vaya a la interfaz de usuario de **configuración** del ASE. Tiene más sentido utilizar la característica **upgradePreferences** cuando hay varias instancias de ASE, porque las que estén configuradas con el valor "Early" (Pronto) se actualizarán antes de las que tienen el valor "Late" (Tarde). Si tiene varias instancias de ASE, los entornos de ASE de desarrollo y pruebas deben establecerse en "Early", mientras que los entornos de ASE de producción deben establecerse en "Late".

## <a name="delete-an-ase"></a>Eliminar un entorno ASE

Para eliminar un entorno ASE:

1. Seleccione **Eliminar** en la parte superior del panel **App Service Environment**.
1. Escriba el nombre de su ASE para confirmar que desea eliminarlo. Cuando se elimina un entorno ASE, también se elimina todo su contenido.
![Eliminación de ASE][3]
1. Seleccione **Aceptar**.

<!--Image references-->

[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png
[6]: ./media/using/using-ip-addresses.png

<!--Links-->

[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
