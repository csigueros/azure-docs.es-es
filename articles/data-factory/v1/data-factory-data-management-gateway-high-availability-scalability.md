---
title: Alta disponibilidad con puerta de enlace de administración de datos en Azure Data Factory
description: Este artículo explica cómo puede escalar horizontalmente una puerta de enlace de administración de datos mediante la adición de más nodos y escalar verticalmente aumentando el número de trabajos simultáneos que se pueden ejecutar en un nodo.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: a76479a358366591d1c4edef0755dd26ce23cd81
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112289880"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - Alta disponibilidad y escalabilidad (versión preliminar)
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory consulte el artículo sobre [IR autohospedado en](../create-self-hosted-integration-runtime.md). 


Este artículo le ayuda a configurar una solución de alta disponibilidad y escalabilidad con Data Management Gateway/Integration.    

> [!NOTE]
> En este artículo se da por supuesto que ya está familiarizado con los conceptos básicos de Integration Runtime (anteriormente, Data Management Gateway). Para más información, consulte [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **La versión preliminar de esta característica es compatible oficialmente con Data Management Gateway versión 2.12.xxxx.x y superior**. Asegúrese de que está utilizando la versión 2.12.xxxx.x o superior. Descargue la versión más reciente de Data Management Gateway [aquí](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Información general
Puede asociar puertas de enlace de administración de datos instaladas en varios equipos de forma local con una sola puerta de enlace lógica desde el portal. Estos equipos se llaman **nodos**. Puede tener hasta **cuatro nodos** asociados con una puerta de enlace lógica. Las ventajas de tener varios nodos (máquinas locales con la puerta de enlace instalada) para una puerta de enlace lógica son:  

- Mejorar el rendimiento del movimiento de datos entre los equipos locales y los almacenes de datos en la nube.  
- Si uno de los nodos deja de funcionar por algún motivo, los otros nodos siguen estando disponibles para mover los datos. 
- Si uno de los nodos tiene que ser desconectado por mantenimiento, los otros nodos siguen estando disponibles para mover los datos.

También puede configurar el número de **trabajos de movimiento de datos simultáneos** que puede ejecutar en un nodo para escalar verticalmente la funcionalidad de mover datos entre los equipos locales y los almacenes de datos en la nube. 

Mediante Azure Portal, puede supervisar el estado de estos nodos, lo que ayuda a decidir si agregar o quitar un nodo de la puerta de enlace lógica. 

## <a name="architecture"></a>Architecture 
El diagrama siguiente proporciona información general de la arquitectura de la característica de escalabilidad y disponibilidad de Data Management Gateway: 

![Data Management Gateway - Alta disponibilidad y escalabilidad](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Una **puerta de enlace lógica** es la puerta de enlace que agrega a una factoría de datos en Azure Portal. Anteriormente, solo era posible asociar un único equipo de Windows local con Data Management Gateway instalado con una puerta de enlace lógica. Ese equipo de puerta de enlace local se llama nodo. Ahora, puede asociar hasta **cuatro nodos físicos** con una puerta de enlace lógica. Una puerta de enlace lógica con varios nodos se denomina **puerta de enlace multi-nodo**.  

Todos estos nodos están **activos**. Todos ellos pueden procesar trabajos de movimiento de datos para mover datos entre los equipos locales y los almacenes de datos en la nube. Uno de los nodos actúa como distribuidor y como trabajo. Los otros nodos en los grupos son nodos de trabajo. Un nodo **distribuidor** extrae tareas y trabajos de movimiento de datos desde el servicio en la nube y los envía a los nodos de trabajo (incluido él mismo). Un nodo de **trabajo** ejecuta trabajos de movimiento de datos para mover datos entre los equipos locales y los almacenes de datos en la nube. Todos los nodos son de trabajo. Únicamente un nodo puede ser a la vez distribuidor y nodo de trabajo.    

Normalmente, puede comenzar con un nodo y **escalar horizontalmente** para agregar más nodos cuando los nodos existentes se desborden por la carga de movimiento de datos. También puede **escalar verticalmente** la capacidad de movimiento de datos de un nodo de puerta de enlace si aumenta el número de trabajos simultáneos que se pueden ejecutar en el nodo. Esta funcionalidad también está disponible con una puerta de enlace de un único nodo (incluso cuando no está habilitada la característica de escalabilidad y disponibilidad). 

Una puerta de enlace con varios nodos mantiene las credenciales del almacén de datos sincronizadas en todos los nodos. Si hay un problema de conectividad de nodo a nodo, las credenciales podrían no estar sincronizadas. Al establecer las credenciales para un almacén de datos local que usa una puerta de enlace, se guardan las credenciales en el nodo distribuidor y de trabajo. El nodo distribuidor se sincroniza con los otros nodos de trabajo. Este proceso se conoce como **sincronización de credenciales**. El canal de comunicación entre los nodos puede estar **cifrado** por un certificado público de SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Configuración de una puerta de enlace de varios nodos
En esta sección se da por supuesto que ha consultado o está familiarizado con los conceptos de los siguientes artículos: 

- [Data Management Gateway](data-factory-data-management-gateway.md): ofrece una información general detallada de la puerta de enlace.
- [Movimiento de datos entre equipos locales y almacenes de datos en la nube](data-factory-move-data-between-onprem-and-cloud.md): contiene un tutorial con instrucciones paso a paso para usar una puerta de enlace con un único nodo.  

> [!NOTE]
> Antes de instalar una puerta de enlace de administración de datos en un equipo de Windows local, consulte los requisitos previos descritos en [el artículo principal](data-factory-data-management-gateway.md#prerequisites).

1. En el [tutorial](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), al crear una puerta de enlace lógica, se habilita la característica de **alta disponibilidad y escalabilidad**. 

    ![Data Management Gateway: habilitar alta disponibilidad y escalabilidad](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. En la página **Configurar**, utilice el vinculo **Instalación rápida** o **Instalación manual** para instalar una puerta de enlace en el primer nodo (un equipo Windows local).

    ![Data Management Gateway: instalación rápida o manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Si utiliza la opción de configuración rápida, la comunicación de nodo a nodo se realiza sin cifrado. El nombre del nodo es igual que el nombre del equipo. Use la instalación manual si la comunicación de nodo a nodo debe cifrarse o si desea especificar un nombre de nodo de su elección. Los nombres de nodo no se puede editar más adelante.
3. Si elige **instalación rápida**
    1. Podrá ver el siguiente mensaje después de que la puerta de enlace se ha instalado correctamente:

        ![Data Management Gateway: instalación rápida correcta](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Puede iniciar Configuration Manager de Data Management Gateway siguiendo [estas instrucciones](data-factory-data-management-gateway.md#configuration-manager). Verá el nombre de la puerta de enlace, el nombre de nodo, el estado, etcetera.

        ![Captura de pantalla que muestra dónde puede ver el nombre de la puerta de enlace, el nombre del nodo y el estado.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Si elige **instalación manual**:
    1. Descargue el paquete de instalación de Microsoft Download Center y ejecútelo para instalar la puerta de enlace en el equipo.
    2. Use la **clave de autenticación** desde la página **Configurar** para registrar la puerta de enlace.
    
        ![Captura de pantalla que muestra dónde usar la clave de autenticación.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. En la página **Nuevo nodo de puerta de enlace**, puede proporcionar un **nombre** personalizado al nodo de la puerta de enlace. De forma predeterminada, el nombre de nodo es igual que el nombre del equipo.    

        ![Data Management Gateway: especificar nombre](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. En la página siguiente, puede elegir si desea **habilitar el cifrado para la comunicación de nodo a nodo**. Haga clic en **Omitir** para deshabilitar el cifrado (valor predeterminado).

        ![Data Management Gateway: habilitar cifrado](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Solo se admite el cambio del modo de cifrado cuando se tiene un único nodo de puerta de enlace en la puerta de enlace lógica. Para cambiar el modo de cifrado cuando una puerta de enlace tiene varios nodos, realice los pasos siguientes: elimine todos los nodos excepto uno, cambie el modo de cifrado y, a continuación, vuelva a agregar los nodos.
        > 
        > Consulte la sección [requisitos de los certificados TLS/SSL](#tlsssl-certificate-requirements) para obtener una lista de los requisitos para utilizar un certificado TLS/SSL. 
    5. Después de que la puerta de enlace se ha instalado correctamente, haga clic en Iniciar Configuration Manager:
    
        ![Instalación manual: iniciar Configuration Manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)     
    6. Puede ver Configuration Manager de Data Management Gateway en el nodo (máquina de Windows local), que muestra el estado de conectividad, **nombre de puerta de enlace** y **nombre de nodo**.  

        ![Data Management Gateway: instalación correcta](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Si va a aprovisionar la puerta de enlace en una máquina virtual de Azure, puede usar [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.datafactory/mutiple-vms-with-data-management-gateway). Este script crea una puerta de enlace lógica, configura las máquinas virtuales con el software de Data Management Gateway instalado y las registra en la puerta de enlace lógica. 
6. En Azure Portal, abra la página **Puerta de enlace**: 
    1. En la página de inicio de factoría de datos en el portal, haga clic en **Servicios vinculados**.
    
        ![Captura de pantalla en la que se resalta el icono Servicios vinculados.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Seleccione **Puerta de enlace** para ver la página **Puerta de enlace**:
    
        ![Página principal Factoría de datos](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Verá la página **Puerta de enlace**:   

        ![Puerta de enlace con la vista de un solo nodo](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Haga clic en **Agregar nodo** en la barra de herramientas para agregar un nodo a la puerta de enlace lógica. Si va a usar la instalación rápida, realice este paso desde la máquina local que va a agregar como un nodo a la puerta de enlace. 

    ![Data Management Gateway: menú agregar nodo](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Los pasos son similares a los de la configuración del primer nodo. La interfaz de usuario de Configuration Manager permite establecer el nombre del nodo si elige la opción de instalación manual: 

    ![Configuration Manager: instalación de la segunda puerta de enlace](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Después de que la puerta de enlace se ha instalado correctamente en el nodo, la herramienta de Configuration Manager muestra la siguiente pantalla:  

    ![Configuration Manager: instalación correcta de la segunda puerta de enlace](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Si abre la página **Puerta de enlace** en el portal, verá dos nodos de puerta de enlace ahora: 

    ![Puerta de enlace con dos nodos en el portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para eliminar un nodo de puerta de enlace, haga clic en **Eliminar nodo** en la barra de herramientas, seleccione el nodo que desea eliminar y, a continuación, haga clic en **Eliminar** desde la barra de herramientas. Esta acción elimina el nodo seleccionado del grupo. Tenga en cuenta que esta acción no desinstala el software de la puerta de enlace de administración de datos del nodo (máquina Windows local). Use **Agregar o quitar programas** de forma local en el Panel de Control para desinstalar la puerta de enlace. Cuando se desinstala la puerta de enlace del nodo, se elimina automáticamente en el portal.   

## <a name="upgrade-an-existing-gateway"></a>Actualización de una puerta de enlace existente
Puede actualizar una puerta de enlace existente para usar la característica de alta disponibilidad y escalabilidad. Esta característica solo funciona con los nodos que tengan una versión de Data Management Gateway igual o posterior a la 2.12.xxxx. Puede ver la versión de Data Management Gateway instalada en un equipo en la pestaña **Ayuda** de Configuration Manager de Data Management Gateway. 

1. Para actualizar la puerta de enlace en el equipo local a la versión más reciente debe descargar y ejecutar un paquete de instalación MSI de [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte la sección [Instalación](data-factory-data-management-gateway.md#installation) para más información.  
2. Acceda a Azure Portal. Abra la página **Factoría de datos** de la factoría de datos. Haga clic en el icono Servicios vinculados para abrir la página **Servicios vinculados**. Seleccione la puerta de enlace para abrir la página **Puerta de enlace**. Haga clic para habilitar la **Característica de versión preliminar** tal como se muestra en la siguiente imagen: 

    ![Data Management Gateway: habilitar característica versión preliminar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Una vez habilitada la característica de versión preliminar en el portal, cierre todas las páginas. Vuelva a abrir la página **Puerta de enlace** para ver la nueva interfaz de usuario (UI) de la versión preliminar.
 
    ![Data Management Gateway: habilitación correcta de la característica versión preliminar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway: interfaz de usuario de la versión preliminar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante la actualización, el nombre del primer nodo es el nombre de la máquina. 
3. Ahora, agregue un nodo. En la página **Puerta de enlace**, haga clic en **Agregar nodo**.  

    ![Data Management Gateway: menú agregar nodo](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga las instrucciones de la sección anterior para configurar el nodo. 

### <a name="installation-best-practices"></a>Procedimientos recomendados de instalación

- Configurar el plan de energía en el equipo host para la puerta de enlace, de forma que el equipo no hiberne. Si la máquina host está en hibernación, la puerta de enlace no responde a las solicitudes de datos.
- Se debe hacer una copia de seguridad del certificado asociado a la puerta de enlace.
- Asegúrese de que todos los nodos son de una configuración similar (recomendada) para un rendimiento ideal. 
- Agregue al menos dos nodos para asegurar la alta disponibilidad.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos del certificado TLS/SSL
Estos son los requisitos para el certificado TLS/SSL que se usa para proteger las comunicaciones entre los nodos del entorno de ejecución de integración:

- El certificado debe ser un certificado de confianza pública X509 v3. Se recomienda que utilice certificados emitidos por una entidad de certificación pública (CA).
- Cada nodo de Integration Runtime debe confiar en este certificado, así como en el equipo cliente que ejecuta la aplicación del Administrador de credenciales. 
  > [!NOTE]
  > La aplicación del Administrador de credenciales se usa al configurar de forma segura las credenciales en el Asistente para copia o en Azure Portal. Además, puede activarse en cualquier equipo que se encuentre en la misma red que el almacén de datos local o privado.
- Se admiten certificados comodín. Si el nombre FQDN es **node1.domain.contoso.com**, puede utilizar **_.domain.contoso.com_* como nombre del firmante del certificado.
- No se recomienda usar certificados de SAN, ya que solo se utilizará el último elemento de los nombres alternativos del firmante y los demás se ignorarán debido a la limitación actual. Por ejemplo, tiene un certificado de SAN cuyos SAN son **node1.domain.contoso.com** y **node2.domain.contoso.com**, pero solo puede usar este certificado en el equipo cuyo FQDN es **node2.domain.contoso.com**.
- Se admite cualquier tamaño de clave compatible con Windows Server 2012 R2 para los certificados TLS/SSL.
- El certificado que usa claves CNG no es compatible.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>PREGUNTAS MÁS FRECUENTES: ¿Cuándo no debería habilitar este cifrado?
Habilitar el cifrado puede agregar determinados costos a la infraestructura (propiedad de certificado público), por lo que puede omitir el paso para habilitar el cifrado en los casos siguientes:
- Cuando se ejecuta Integration Runtime en una red de confianza o en una red con cifrado transparente como IP/SEC. Puesto que esta comunicación del canal solo está limitada en su red de confianza, no se necesita cifrado adicional.
- Cuando Integration Runtime no se ejecuta en un entorno de producción. Esto puede ayudar a reducir el costo del certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Supervisión de una puerta de enlace de varios nodos
### <a name="multi-node-gateway-monitoring"></a>Supervisión de una puerta de enlace de varios nodos
En Azure Portal, puede ver una instantánea casi en tiempo real de la utilización de los recursos (CPU, memoria, red (entrada/salida), etc.) en cada nodo junto con los estados de los nodos de la puerta de enlace. 

![Data Management Gateway: supervisión de varios nodos](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Puede habilitar la **Configuración avanzada** en la página **Puerta de enlace** para ver métricas avanzadas como **Red**(entrada/salida), **Rol y estado de credenciales**, que es útil para depurar problemas de puerta de enlace y **Trabajos simultáneos** (en ejecución / límite) que puede ser modificado según corresponda durante la optimización del rendimiento. En la tabla siguiente se proporcionan las descripciones de las columnas de la lista **Nodos de la puerta de enlace**:  

Propiedad de supervisión | Descripción
:------------------ | :---------- 
Nombre | Nombre de la puerta de enlace lógica y de los nodos asociados a la puerta de enlace.  
Status | Estado de la puerta de enlace lógica y de los nodos de la puerta de enlace. Ejemplo: En línea, Sin conexión, Limitado, etc. Para obtener información acerca de estos estados, consulte la sección [Estado de la puerta de enlace](#gateway-status). 
Versión | Muestra la versión de la puerta de enlace lógica y de cada nodo de la puerta de enlace. La versión de la puerta de enlace lógica se determina basándose en la versión de la mayoría de los nodos del grupo. Si hay nodos con versiones diferentes en la configuración de la puerta de enlace lógica, solo los nodos con el mismo número de versión que la puerta de enlace lógica funcionan correctamente. Los otros están en el modo limitado y deben actualizarse manualmente (solo en caso de que se produzca un error en la actualización automática). 
Memoria disponible | Memoria disponible en un nodo de la puerta de enlace. Este valor es una instantánea casi en tiempo real. 
Uso de CPU | Uso de CPU de un nodo de la puerta de enlace. Este valor es una instantánea casi en tiempo real. 
Redes (Entrada/Salida) | Uso de red de un nodo de la puerta de enlace. Este valor es una instantánea casi en tiempo real. 
Trabajos simultáneos (En ejecución / Límite) | Número de trabajos o tareas que se ejecutan en cada nodo. Este valor es una instantánea casi en tiempo real. Límite significa el máximo número de trabajos simultáneos para cada nodo. Este valor se define basándose en el tamaño de la máquina. Puede aumentar el límite para escalar verticalmente la ejecución de trabajos simultáneos en escenarios avanzados, donde la CPU, la memoria o la red están infrautilizadas pero se agota el tiempo de espera de las actividades. Esta funcionalidad también está disponible con una puerta de enlace de un único nodo (incluso cuando no está habilitada la característica de escalabilidad y disponibilidad). Para más información, consulte la sección [Consideraciones para el escalado](#scale-considerations). 
Role | Hay dos tipos de roles: distribuidor y de trabajo. Todos los nodos son trabajos, lo que significa que pueden usarse para ejecutar los trabajos. Hay solo un nodo distribuidor, que se usa para extraer los trabajos y tareas de los servicios en la nube y enviarlos a los diferentes nodos de trabajo (incluido él mismo). 

![Data Management Gateway: supervisión avanzada de varios nodos](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Estado de la puerta de enlace

La tabla siguiente proporciona los estados posibles de un **nodo de la puerta de enlace**: 

Status    | Comentarios/Escenarios
:------- | :------------------
En línea | Nodo conectado al servicio Data Factory.
Sin conexión | El nodo está sin conexión.
Actualizando | El nodo se está actualizando automáticamente.
Limitado | Debido a un problema de conectividad. Puede ser debido a un problema en el puerto HTTP 8050, a un problema de conectividad del bus de servicio o un problema de sincronización de credenciales. 
Inactivo | El nodo tiene una configuración diferente de la configuración de la mayoría de los otros nodos.<br/><br/> Un nodo puede estar inactivo cuando no se puede conectar a otros nodos. 


La tabla siguiente proporciona los estados posibles de una **puerta de enlace lógica**. El estado de la puerta de enlace depende de los estados de los nodos de puerta de enlace. 

Status | Comentarios
:----- | :-------
Debe registrarse | Aún no se ha registrado ningún nodo en esta puerta de enlace lógica
En línea | Los nodos de la puerta de enlace están en línea
Sin conexión | Ningún nodo está en línea.
Limitado | No todos los nodos de la puerta de enlace están en buen estado. Este estado es una advertencia de que alguno de los nodos podría estar inactivo. <br/><br/>Podría ser debido a un problema de sincronización de credenciales en el nodo distribuidor o de trabajo. 

### <a name="pipeline-activities-monitoring"></a>Supervisión de canalización y actividades
Azure Portal proporciona una experiencia de supervisión de canalización con detalle granular de nivel de nodo específico. Por ejemplo, muestra las actividades que se ejecutaron en cada nodo. Esta información puede ser útil para comprender los problemas de rendimiento en un nodo determinado, por ejemplo, debido a una limitación de red. 

![Data Management Gateway: supervisión de varios nodos para canalizaciones](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway: detalles de canalización](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Consideraciones de escala

### <a name="scale-out"></a>Escalado horizontal
Cuando la **memoria disponible es baja** y el **uso de CPU es alto**, agregar un nuevo nodo ayuda a escalar horizontalmente la carga en los equipos. Si se producen errores en las actividades debido a tiempos de espera excedidos o a un nodo de la puerta de enlace sin conexión, será útil agregar un nodo a la puerta de enlace.
 
### <a name="scale-up"></a>Escalado vertical
Cuando la memoria disponible y la CPU no se utilizan correctamente, pero la capacidad de inactividad es 0, debe escalar verticalmente aumentando el número de trabajos simultáneos que se pueden ejecutar en un nodo. También puede escalar verticalmente cuando se agota el tiempo de espera de las actividades porque la puerta de enlace está sobrecargada. Como se muestra en la siguiente imagen, puede aumentar la capacidad máxima para un nodo. Se recomienda comenzar duplicándolo.  

![Data Management Gateway: consideraciones de escala](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Problemas conocidos y cambios relevantes

- Actualmente, puede tener hasta cuatro nodos físicos de puerta de enlace para una sola puerta de enlace lógica. Si necesita más de cuatro nodos por motivos de rendimiento, envíe un correo electrónico a [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com).
- No se puede volver a registrar un nodo de puerta de enlace con la clave de autenticación de otra puerta de enlace lógica para cambiar la puerta de enlace lógica actual. Para volver a registrarlo, desinstale la puerta de enlace del nodo, vuelva a instalar la puerta de enlace y regístrela con la clave de autenticación de la otra puerta de enlace lógica. 
- Si es necesario un proxy HTTP para todos los nodos de la puerta de enlace, establezca el proxy en diahost.exe.config y diawp.exe.config y use el administrador del servidor para asegurarse de que todos los nodos tienen el mismo diahost.exe.config y diawip.exe.config. Consulte la sección [configurar el proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) para más información. 
- Para cambiar el modo de cifrado para la comunicación de nodo a nodo en Configuration Manager de la puerta de enlace, elimine todos los nodos desde el portal excepto uno. A continuación, agregue nodos después de cambiar el modo de cifrado.
- Utilice un certificado TLS oficial si elige cifrar el canal de comunicación de nodo a nodo. Un certificado autofirmado puede producir problemas de conectividad ya que el mismo certificado puede no ser de confianza en la lista de entidades de certificación de otros equipos. 
- No se puede registrar un nodo de puerta de enlace en una puerta de enlace lógica si la versión del nodo es menor que la versión de la puerta de enlace lógica. Elimine todos los nodos de la puerta de enlace lógica desde Azure Portal para poder registrar un nodo con una versión anterior. Si elimina todos los nodos de una puerta de enlace lógica, instale y registre manualmente los nuevos nodos en esa puerta de enlace lógica. La instalación rápida no se admite en este caso.
- No se puede usar la instalación rápida para instalar nodos en una puerta de enlace lógica existente, que todavía está usando credenciales de la nube. Puede comprobar dónde se almacenan las credenciales en la pestaña Configuración de Configuration Manager de Data Management Gateway.
- No se puede usar la instalación rápida para instalar nodos en una puerta de enlace lógica existente que tenga habilitado el cifrado nodo a nodo. Dado que establecer el modo de cifrado implica agregar manualmente los certificados, la instalación rápida no es una opción. 
- Para la copia de archivos desde el entorno local, no debe usar \\localhost o C:\archivos puesto que localhost o la unidad local podrían no ser accesibles en todos los nodos. En su lugar, use \\ServerName\files para especificar la ubicación de los archivos.


## <a name="rolling-back-from-the-preview"></a>Revertir desde la versión preliminar 
Para revertir desde la versión preliminar, elimine todos los nodos excepto uno. No importa qué nodos elimine, pero asegúrese de tener al menos un nodo en la puerta de enlace lógica. Puede eliminar un nodo mediante la desinstalación de la puerta de enlace en el equipo o a través de Azure Portal. En Azure Portal, en la página **Factoría de datos**, haga clic en Servicios vinculados para abrir la página **Servicios vinculados**. Seleccione la puerta de enlace para abrir la página **Puerta de enlace**. En la página Puerta de enlace puede ver los nodos asociados a la puerta de enlace. La página permite eliminar un nodo de la puerta de enlace.
 
Después de eliminarlo, haga clic en **Característica de versión preliminar** en la misma página de Azure Portal y deshabilite la característica de versión preliminar. Ha restablecido la puerta de enlace como puerta de enlace de un nodo GA (disponibilidad general).


## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes:
- [Data Management Gateway](data-factory-data-management-gateway.md): ofrece una información general detallada de la puerta de enlace.
- [Movimiento de datos entre equipos locales y almacenes de datos en la nube](data-factory-move-data-between-onprem-and-cloud.md): contiene un tutorial con instrucciones paso a paso para usar una puerta de enlace con un único nodo. 
