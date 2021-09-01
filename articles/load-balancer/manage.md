---
title: Configuración del portal para Azure Load Balancer
description: Introducción a la configuración del portal para Azure Load Balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/16/2021
ms.author: allensu
ms.openlocfilehash: 1b13a4cde0d4c0278698100cde0d5cd9993465a3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252971"
---
# <a name="azure-load-balancer-portal-settings"></a>Configuración del portal para Azure Load Balancer

Al crear una instancia de Azure Load Balancer, la información de este artículo lo ayudará a obtener más información sobre los valores de configuración y a saber cuál es la configuración adecuada.

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

Azure Load Balancer es un equilibrador de carga de red que distribuye el tráfico entre las instancias de máquina virtual del grupo de back-end. Para crear un equilibrador de carga en el portal, en la parte superior de la página, seleccione el cuadro de búsqueda. Escriba **equilibrador de carga**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda. Seleccione **+ Crear** en la página **Equilibradores de carga**.

### <a name="basics"></a>Aspectos básicos

En la pestaña **Conceptos básicos** de la página del portal Crear equilibrador de carga, verá la siguiente información:

| Configuración |  Detalles |
| ---------- | ---------- |
| Subscription  | Seleccione su suscripción. Esta selección es la suscripción en la que desea implementar el equilibrador de carga. |
| Resource group | Seleccione **Crear nuevo** y escriba el nombre del grupo de recursos en el cuadro de texto. Si tiene un grupo de recursos, selecciónelo. |
| Nombre | Este valor es el nombre de la instancia de Azure Load Balancer. |
| Region | Seleccione una región de Azure en la que desee implementar el equilibrador de carga. |
| Tipo | Un equilibrador de carga tiene dos tipos: </br> **Interno (privado)** </br> **Público (externo)** .</br> Un equilibrador de carga interno (ILB) enruta el tráfico a los miembros del grupo de back-end a través de una dirección IP privada.</br> Un equilibrador de carga público dirige las solicitudes de los clientes por Internet al grupo de back-end.</br> Aprenda más sobre los [tipos de equilibradores de carga](components.md#frontend-ip-configuration-).|
| SKU  | Seleccione **Estándar**. </br> Un equilibrador de carga tiene dos SKU: **Básico** y **Estándar**. </br> Básico tiene una funcionalidad limitada. </br> Se recomienda usar **Estándar** para cargas de trabajo de producción. </br> Obtenga más información sobre las [SKU](skus.md). |
| Nivel | El equilibrador de carga tiene dos niveles: </br> **Regional** </br> **Global** </br> Un equilibrador de carga regional está restringido al equilibrio de carga dentro de una región. Global hace referencia a un equilibrador de carga entre regiones que equilibra la carga entre ellas. </br> Para más información sobre el nivel **Global**, consulte [Equilibrador de carga entre regiones (versión preliminar)](cross-region-overview.md).

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Captura de pantalla de creación de un equilibrador de carga público." border="true":::

### <a name="frontend-ip-configuration"></a>Configuración de direcciones IP de front-end

En la pestaña **Configuración de IP de front-end** de la página del portal de creación de equilibradores de carga, seleccione **+ Agregar dirección IP de front-end** para abrir la página de creación.

:::image type="content" source="./media/manage/create-frontend.png" alt-text="Captura de pantalla de creación de una configuración de IP de front-end." border="true":::

#### <a name="-add-a-frontend-ip"></a>**+ Agregar dirección IP de front-end**
##### <a name="public"></a>Público

Si selecciona **Público** como tipo, verá la siguiente información:

| Configuración | Detalles |
| ------- | ------- |
| Nombre | Nombre del front-end que se agregará al equilibrador de carga. |
| Versión de la dirección IP | **IPv4** </br> **IPv6** </br> Un equilibrador de carga admite servidores front-end IPv4 e IPv6. </br> Para más información, consulte [Información general de IPv6 para Azure Load Balancer](load-balancer-ipv6-overview.md). |
| Tipo de IP | **Dirección IP** </br> **Prefijo IP** </br> El equilibrador de carga admite una dirección IP o un prefijo IP para la dirección IP de front-end. Para más información, consulte [Prefijo de dirección IP pública de Azure](../virtual-network/public-ip-address-prefix.md). |

:::image type="content" source="./media/manage/add-frontend-public.png" alt-text="Captura de pantalla de cómo se agrega la configuración de IP de front-end." border="true":::

Si selecciona **Dirección IP** en **Tipo de IP**, verá la siguiente información:

| Configuración | Detalles |
| ------- | ------- |
| Dirección IP pública | Seleccione **Crear nuevo** con el fin de crear una dirección IP pública para el equilibrador de carga público. </br> Si tiene una dirección IP pública existente, selecciónela en el cuadro desplegable. |
| Nombre | nombre del recurso de la dirección IP pública. |
| SKU | Las direcciones IP públicas tienen dos SKU: **Básico** y **Estándar**. </br> Básico no admite atributos de zona ni resistencia de zona. </br> Se recomienda usar **Estándar** para cargas de trabajo de producción. </br> Las SKU del equilibrador de carga y de la dirección IP pública **deben coincidir**. |
| Nivel | **Regional** </br> **Global** </br> El tipo de nivel del equilibrador de carga determinará lo que se selecciona. Regional para equilibrador de carga tradicional, global para equilibrador de carga entre regiones. |
| Asignación | **Estática** se selecciona automáticamente para Estándar. </br> Las direcciones IP públicas básicas tienen dos tipos: **Dinámica** y **Estática**. </br> Las direcciones IP públicas dinámicas no se asignan hasta la creación. </br> Las direcciones IP se pueden perder si se elimina el recurso. </br> Se recomiendan las direcciones IP estáticas. |
| Zona de disponibilidad | Seleccione **Con redundancia de zona** para crear un equilibrador de carga resistente. </br> Para crear un equilibrador de carga de zona, seleccione una zona específica entre **1**, **2** o **3**. </br> El equilibrador de carga estándar y las direcciones IP públicas admiten zonas. </br> Obtenga más información sobre los [equilibradores de carga y las zonas de disponibilidad](load-balancer-standard-availability-zones.md). </br> No verá la selección de zona para los equilibradores de carga básicos. Basic Load Balancer no admite zonas. |
| Preferencia de enrutamiento | Seleccione **Microsoft Network**. </br> Con Microsoft Network, el tráfico se enruta a través de la red global de Microsoft. </br> Con Internet, el tráfico se enruta a través de la red del proveedor de servicios de Internet. </br> Obtenga más información sobre las [preferencias de enrutamiento](../virtual-network/routing-preference-overview.md).|

:::image type="content" source="./media/manage/create-public-ip.png" alt-text="Captura de pantalla de creación de una dirección IP pública." border="true":::

Si selecciona **Prefijo IP** en **Tipo de IP**, verá la siguiente información:

| Configuración | Detalles |
| ------- | ------- |
| Prefijo de IP pública | Seleccione **Crear nuevo** para crear un prefijo de dirección IP pública para el equilibrador de carga público. </br> Si tiene un prefijo público existente, selecciónelo en el cuadro desplegable. |
| Nombre | El nombre del recurso de prefijo de IP pública. |
| SKU | Los prefijos de IP pública tienen una SKU, **Standard**. |
| Versión de la dirección IP | **IPv4** o **IPv6**. </br> La versión mostrada corresponderá a la versión elegida anteriormente. |
| Tamaño del prefijo | Se muestran los prefijos IPv4 o IPv6 en función de la selección anterior. </br> **IPv4** </br> /24 (256 direcciones) </br> /25 (128 direcciones) </br> /26 (64 direcciones) </br> /27 (32 direcciones) </br> /28 (16 direcciones) </br> /29 (8 direcciones) </br> /30 (4 direcciones) </br> /31 (2 direcciones) </br> **IPv6** </br> /124 (16 direcciones) </br> /125 (8 direcciones) </br> /126 (4 direcciones) </br> /127 (2 direcciones) |
| Zona de disponibilidad | Seleccione **Con redundancia de zona** para crear un equilibrador de carga resistente. </br> Para crear un equilibrador de carga de zona, seleccione una zona específica entre **1**, **2** o **3**. </br> El equilibrador de carga estándar y las direcciones IP públicas admiten zonas. </br> Obtenga más información sobre los [equilibradores de carga y las zonas de disponibilidad](load-balancer-standard-availability-zones.md).

:::image type="content" source="./media/manage/create-public-ip-prefix.png" alt-text="Captura de pantalla de creación del prefijo de IP pública." border="true":::

##### <a name="internal"></a>Interno

Si selecciona **Interno** como tipo en la pestaña **Aspectos básicos**, verá la siguiente información:

| Configuración |  Detalles |
| ---------- | ---------- |
| Virtual network | La red virtual que quiere para el equilibrador de carga interno. </br> La dirección IP de front-end privada que seleccione para el equilibrador de carga interno será de esta red virtual. |
| Subred | Las subredes disponibles para la dirección IP de la dirección IP de front-end se muestran aquí. |
| Asignación | Las opciones son **Estática** o **Dinámica**. </br> Estática garantiza que la dirección IP no cambie. Mientras que una dirección IP dinámica podría cambiar. |
| Zona de disponibilidad | Las opciones son: </br> **Con redundancia de zona** </br> **Zona 1** </br> **Zona 2** </br> **Zona 3** </br> Para crear un equilibrador de carga de alta disponibilidad y resistente a errores de zona de disponibilidad, seleccione una dirección IP **con redundancia de zona**. |

:::image type="content" source="./media/manage/add-frontend-internal.png" alt-text="Captura de pantalla de cómo se agrega un front-end interno." border="true":::
### <a name="backend-pools"></a>Grupos back-end

En la pestaña **Grupos de back-end** de la página del portal de creación de equilibradores de carga, seleccione **+ Agregar un grupo de back-end** para abrir la página de creación.

:::image type="content" source="./media/manage/create-backend-pool.png" alt-text="Captura de pantalla de la pestaña Crear un grupo de back-end." border="true":::

#### <a name="-add-a-backend-pool"></a>**+ Agregar un grupo de back-end**

La siguiente información se muestra en la página de creación **+ Agregar un grupo de back-end**:

| Configuración | Detalles |
| ---------- |  ---------- |
| Nombre | Nombre del grupo de back-end. |
| Virtual network | La red virtual en la que se encuentran las instancias de back-end. |
| Configuración del grupo de back-end | Las opciones son: </br> **NIC** </br> **Dirección IP** </br> NIC configura el grupo de back-end para usar la tarjeta de interfaz de red de las máquinas virtuales. </br> La dirección IP configura el grupo de back-end para usar la dirección IP de las máquinas virtuales. </br> Para más información sobre la configuración del grupo de back-end, consulte [Administración del grupo de back-end](backend-pool-management.md).
| Versión de la dirección IP | Las opciones son **IPv4** o **IPv6**. |

Puede agregar máquinas virtuales o conjuntos de escalado de máquinas virtuales al grupo de back-end de su instancia de Azure Load Balancer. Cree primero las máquinas virtuales o los conjuntos de escalado de máquinas virtuales. 

:::image type="content" source="./media/manage/add-backend-pool.png" alt-text="Captura de pantalla de cómo se agrega un grupo de back-end." border="true":::

### <a name="inbound-rules"></a>Reglas de entrada

Hay dos secciones en la pestaña **Reglas de entrada**, **Regla de equilibrio de carga** y **Regla NAT de entrada**.

En la pestaña **Reglas de entrada** de la página del portal de creación de equilibradores de carga, seleccione **Add a load balancing rule** (+ Agregar una regla de equilibrio de carga) para abrir la página de creación.

:::image type="content" source="./media/manage/inbound-rules.png" alt-text="Captura de pantalla de cómo se agrega una regla de entrada." border="true":::

#### <a name="-add-a-load-balancing-rule"></a>**+ Add a load balancing rule** (+ Agregar una regla de equilibrio de carga)

La siguiente información se muestra en la página de creación **+ Add a load balancing rule** (+ Agregar una regla de equilibrio de carga):

| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre de la regla de equilibrio de carga. |
| Versión de la dirección IP | Las opciones son **IPv4** o **IPv6**.  |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea asociar la regla de equilibrio de carga.|
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: **TCP** o **UDP**. |
| Port | Este valor es el puerto asociado a la dirección IP de front-end en la que quiere que se distribuya el tráfico en función de esta regla de equilibrio de carga. |
| Puerto back-end | Este valor es el puerto de las instancias del grupo de back-end al que desea que el equilibrador de carga envíe el tráfico. El valor puede ser el mismo que el del puerto de front-end o diferente si necesita flexibilidad para su aplicación. |
| Grupo back-end | El grupo de back-end en el que desea aplicar esta regla de equilibrio de carga. |
| Sondeo de mantenimiento | Seleccione **Crear nuevo** para crear un sondeo.  </br> Solo las instancias correctas recibirán nuevo tráfico. |
| Persistencia de la sesión |  Las opciones son: </br> **None** </br> **IP de cliente** </br> **IP y protocolo del cliente**</br> </br> Mantenga el tráfico de un cliente a la misma máquina virtual del grupo de back-end. Este tráfico se mantendrá mientras dure la sesión. </br> **Ninguno**: especifica que cualquier máquina virtual puede controlar las solicitudes sucesivas del mismo cliente. </br> **IP de cliente**: especifica que la misma máquina virtual controlará las solicitudes sucesivas de la misma dirección IP de cliente. </br> **IP y protocolo del cliente**: especifica que la misma máquina virtual controlará las solicitudes sucesivas de la misma dirección IP de cliente y protocolo. </br> Obtenga más información sobre los [modos de distribución](load-balancer-distribution-mode.md). |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión **TCP** o **HTTP** abierta sin depender de los clientes para enviar mensajes persistentes. |  
| Restablecimiento de TCP | Un equilibrador de carga puede enviar **restablecimientos de TCP** para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md).|
| Dirección IP flotante | La dirección IP flotante es el término de Azure para referirse a una parte de lo que se conoce como **Direct Server Return (DSR)** . </br> DSR consta de dos partes: <br> 1. Topología de flujo </br> 2. Un esquema de asignación de direcciones IP en el nivel de plataforma. </br></br> Azure Load Balancer siempre funciona en una topología de flujo DSR, independientemente de si la dirección IP flotante está habilitada o no. </br> Esto significa que la parte de salida de un flujo siempre se reescribe correctamente para que se dirija de nuevo al origen. </br> Sin una dirección IP flotante, Azure expone un esquema de asignación de direcciones IP de equilibrio (la IP de las instancias de máquina virtual). </br> Habilitar la dirección IP flotante cambia el esquema de asignación de direcciones IP a la IP de servidor front-end del equilibrador de carga para permitir más flexibilidad. </br> Para obtener más información, consulte [Varios front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).|
| Traducción de direcciones de red de origen (SNAT) de salida | Las opciones son: </br> **(Recomendado) Usar reglas de salida para proporcionar acceso a Internet a los miembros del grupo de back-end**. </br> **Usar la regla de salida implícita. No es recomendable porque puede provocar el agotamiento de puertos SNAT.** </br> Seleccione la opción **Recomendado** para evitar el agotamiento de puertos SNAT. Se necesita una **puerta de enlace NAT** o **reglas de salida** para proporcionar SNAT a los miembros del grupo de back-end. Para más información sobre **NAT Gateway**, consulte [¿Qué es Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md) </br> Para más información sobre las conexiones salientes en Azure, consulte [Uso de la Traducción de direcciones de red de origen (SNAT) para conexiones salientes](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/add-load-balancing-rule.png" alt-text="Captura de pantalla de cómo se agrega una regla de equilibrio de carga." border="true":::

#### <a name="create-health-probe"></a>Creación de un sondeo de estado

Si seleccionó **Crear nuevo** en la configuración del sondeo de estado de la regla de equilibrio de carga anterior, se muestran las siguientes opciones:

| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre del sondeo de estado. |
| Protocolo | El protocolo que seleccione determina el tipo de comprobación que se usa para determinar si las instancias de back-end son correctas. </br> Las opciones son: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Asegúrese de que esté utilizando la dirección URL correcta. Esta selección dependerá de la naturaleza de la aplicación. </br> La configuración del sondeo de estado y las respuestas de sondeo determinan qué instancias del grupo de back-end recibirán nuevos flujos. </br> Puede usar los sondeos de estado para detectar el error de una aplicación en un punto de conexión de back-end. </br> Obtenga más información sobre los [sondeos de estado](load-balancer-custom-probe-overview.md). |
| Port | El puerto de destino del sondeo de estado. </br> Este valor es el puerto de la instancia de back-end que el sondeo de estado usará para determinar el estado de la instancia. |
| Intervalo | El número de segundos entre los intentos de sondeo. </br> El intervalo determinará la frecuencia con la que el sondeo de estado intentará comunicarse con la instancia de back-end. </br> Si selecciona 5, el segundo intento de sondeo se realizará después de 5 segundos, y así sucesivamente. |
| Umbral incorrecto | El número de errores de sondeo consecutivos que tienen que producirse para que se considere que una máquina virtual no funciona correctamente.</br> Si selecciona 2, no se establecerán nuevos flujos en esta instancia de back-end después de 2 errores consecutivos. |

:::image type="content" source="./media/manage/add-health-probe.png" alt-text="Captura de pantalla de cómo se agrega un sondeo de estado." border="true":::

En la pestaña **Reglas de entrada** de la página del portal de creación de equilibradores de carga, seleccione **+ Add a an inbound nat rule** (+ Agregar una regla NAT de entrada) para abrir la página de creación.

#### <a name="-add-a-an-inbound-nat-rule"></a>**+ Add a an inbound nat rule** (+ Agregar una regla NAT de entrada)

La siguiente información se muestra en la página de creación **+ Add inbound NAT rule** (+ Agregar una regla NAT de entrada):

| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre de la regla NAT de entrada. |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea que se asocie la regla NAT de entrada. |
| Versión de la dirección IP | Las opciones son IPv4 e IPv6. |
| Servicio | El tipo de servicio en el que se va a ejecutar Azure Load Balancer. </br> Según lo que seleccione aquí, actualizará la información de puertos adecuadamente. |
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: TCP o UDP. |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión TCP o HTTP abierta sin depender de los clientes para enviar mensajes persistentes. |
| Restablecimiento de TCP | Un equilibrador de carga puede enviar restablecimientos de TCP para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md). |
| Port | Este valor es el puerto asociado a la dirección IP de front-end en la que quiere que se distribuya el tráfico en función de esta regla NAT de entrada. |
| Máquina virtual de destino | La parte de la máquina virtual del grupo de back-end al que le gustaría asociar esta regla. |
| Asignación de puertos | Este valor puede ser el predeterminado o personalizada según las preferencias de la aplicación. |

:::image type="content" source="./media/manage/add-inbound-nat-rule.png" alt-text="Captura de pantalla de cómo se agrega una regla NAT de entrada." border="true":::

### <a name="outbound-rules"></a>Reglas de salida

En la pestaña **Reglas de salida** de la página del portal de creación de equilibradores de carga, seleccione **+ Add an outbound rule** (+ Agregar una regla de salida) para abrir la página de creación.

> [!NOTE]
> La pestaña Reglas de salida solo es válida con un equilibrador de carga estándar público. Las reglas de salida no se admiten en un equilibrador de carga interno o básico. Azure Virtual Network NAT es la manera recomendada de proporcionar acceso saliente a Internet para el grupo de back-end. Para más información sobre **Azure Virtual Network NAT** y el recurso NAT Gateway, consulte **[¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)**

:::image type="content" source="./media/manage/create-outbound-rule.png" alt-text="Captura de pantalla de creación de una regla de salida." border="true":::

#### <a name="-add-an-outbound-rule"></a>**+ Add an outbound rule** (+Agregar una regla de salida)

La siguiente información se muestra en la página de creación **+ Add an outbound rule** (+ Agregar una regla de salida):

| Configuración | Detalles |
| ------- | ------ |
| Nombre | El nombre de la regla de salida. |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea que se asocie la regla de salida. |
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: **Todos**, **TCP** o **UDP**. |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión **TCP** o **HTTP** abierta sin depender de los clientes para enviar mensajes persistentes. |
| Restablecimiento de TCP | Un equilibrador de carga puede enviar **restablecimientos de TCP** para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md). |
| Grupo back-end | El grupo de back-end en el que desea aplicar esta regla de salida. |
| **Asignación de puertos** |   |
| Asignación de puertos | Las opciones son: </br> **Elegir manualmente el número de puertos de salida**. </br> **Usar el número predeterminado de puertos de salida** </br> Se recomienda seleccionar el valor predeterminado, **Elegir manualmente el número de puertos de salida**, para evitar el agotamiento de puertos SNAT. Si elige **Usar el número predeterminado de puertos de salida**, la selección de **Puertos de salida** se deshabilitará. |
| Puertos de salida | Las opciones son: </br> **Puertos por instancia** </br> **Número máximo de instancias de back-end**. </br> Se recomienda seleccionar **Puertos por instancia** y escribir **10 000**. |

:::image type="content" source="./media/manage/add-outbound-rule.png" alt-text="Captura de pantalla de cómo se agrega una regla de salida." border="true":::

## <a name="portal-settings"></a>Configuración del portal
### <a name="frontend-ip-configuration"></a>Configuración de direcciones IP de front-end

La dirección IP de Azure Load Balancer. Es el punto de contacto de los clientes. 

Puede tener una o varias configuraciones de direcciones IP de front-end. Si completó la sección de creación anterior, ya habrá creado un front-end para el equilibrador de carga. 

Si desea agregar una configuración de IP de front-end al equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Configuración de IP de front-end** y, a continuación, seleccione **+ Agregar**.

| Configuración |  Detalles |
| ---------- | ---------- |
| Nombre | El nombre de la configuración de IP de front-end. |
| Versión de la dirección IP | La versión de la dirección IP que desea que tenga el front-end. </br> Un equilibrador de carga admite configuraciones IP de front-end IPv4 e IPv6. |
| Tipo de IP | Tipo de IP determina si una única dirección IP está asociada a su front-end o a un intervalo de direcciones IP mediante un prefijo IP. </br> Un [prefijo de IP pública](../virtual-network/public-ip-address-prefix.md) sirve de ayuda para cuando necesita conectarse al mismo punto de conexión repetidamente. El prefijo garantiza que se proporcionan suficientes puertos para ayudar a solucionar los problemas del puerto SNAT. |
| Dirección IP pública (o prefijo si eligió un prefijo antes) | Seleccione o cree una dirección IP pública (o prefijo) para el front-end del equilibrador de carga. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Creación de una configuración de IP de front-end" border="true":::

### <a name="backend-pools"></a>Grupos back-end

Un grupo de direcciones de back-end contiene las direcciones IP de las interfaces de red virtuales del grupo de back-end. 

Si desea agregar un grupo de back-end al equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Grupos de back-end** y, a continuación, seleccione **+ Agregar**.

| Configuración | Detalles |
| ---------- |  ---------- |
| Nombre | Nombre del grupo de back-end. |
| Virtual network | La red virtual en la que se encuentran las instancias de back-end. |
| Configuración del grupo de back-end | Las opciones son: </br> **NIC** </br> **Dirección IP** </br> NIC configura el grupo de back-end para usar la tarjeta de interfaz de red de las máquinas virtuales. </br> La dirección IP configura el grupo de back-end para usar la dirección IP de las máquinas virtuales. </br> Para más información sobre la configuración del grupo de back-end, consulte [Administración del grupo de back-end](backend-pool-management.md). |
| Versión de la dirección IP | Las opciones son **IPv4** o **IPv6**. |

Puede agregar máquinas virtuales o conjuntos de escalado de máquinas virtuales al grupo de back-end de su instancia de Azure Load Balancer. Cree primero las máquinas virtuales o los conjuntos de escalado de máquinas virtuales. A continuación, agréguelos al equilibrador de carga en el portal.

:::image type="content" source="./media/manage/backend.png" alt-text="Página de creación de un grupo de back-end" border="true":::

### <a name="health-probes"></a>Sondeos de estado

Un sondeo de estado se usa para supervisar el estado de las instancias o máquinas virtuales de back-end. El estado de sondeo determina cuándo se envían nuevas conexiones a una instancia en función de las comprobaciones de estado. 

Si desea agregar un sondeo de estado al equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Sondeos de estado** y, a continuación, seleccione **+ Agregar**.

| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre del sondeo de estado. |
| Protocolo | El protocolo que seleccione determina el tipo de comprobación que se usa para determinar si las instancias de back-end son correctas. </br> Las opciones son: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Asegúrese de que esté utilizando la dirección URL correcta. Esta selección dependerá de la naturaleza de la aplicación. </br> La configuración del sondeo de estado y las respuestas de sondeo determinan qué instancias del grupo de back-end recibirán nuevos flujos. </br> Puede usar los sondeos de estado para detectar el error de una aplicación en un punto de conexión de back-end. </br> Obtenga más información sobre los [sondeos de estado](load-balancer-custom-probe-overview.md). |
| Port | El puerto de destino del sondeo de estado. </br> Este valor es el puerto de la instancia de back-end que el sondeo de estado usará para determinar el estado de la instancia. |
| Intervalo | El número de segundos entre los intentos de sondeo. </br> El intervalo determinará la frecuencia con la que el sondeo de estado intentará comunicarse con la instancia de back-end. </br> Si selecciona 5, el segundo intento de sondeo se realizará después de 5 segundos, y así sucesivamente. |
| Umbral incorrecto | El número de errores de sondeo consecutivos que tienen que producirse para que se considere que una máquina virtual no funciona correctamente.</br> Si selecciona 2, no se establecerán nuevos flujos en esta instancia de back-end después de 2 errores consecutivos. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Captura de pantalla de creación de un sondeo de estado." border="true":::

### <a name="load-balancing-rules"></a>Reglas de equilibrio de carga.

Define cómo se distribuye el tráfico entrante a todas las instancias del grupo de back-end. Las reglas de equilibrio de carga asignan una configuración de IP de front-end y un puerto determinados a varios puertos y direcciones IP de back-end.

Si desea agregar una regla de equilibrio de carga al equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Reglas de equilibrio de carga** y, a continuación, seleccione **+ Agregar**.
    
| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre de la regla de equilibrio de carga. |
| Versión de la dirección IP | Las opciones son **IPv4** o **IPv6**.  |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea asociar la regla de equilibrio de carga.|
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: **TCP** o **UDP**. |
| Port | Este valor es el puerto asociado a la dirección IP de front-end en la que quiere que se distribuya el tráfico en función de esta regla de equilibrio de carga. |
| Puerto back-end | Este valor es el puerto de las instancias del grupo de back-end al que desea que el equilibrador de carga envíe el tráfico. El valor puede ser el mismo que el del puerto de front-end o diferente si necesita flexibilidad para su aplicación. |
| Grupo back-end | El grupo de back-end en el que desea aplicar esta regla de equilibrio de carga. |
| Sondeo de mantenimiento | El sondeo de estado que creó para comprobar el estado de las instancias del grupo de back-end. </br> Solo las instancias correctas recibirán nuevo tráfico. |
| Persistencia de la sesión |  Las opciones son: </br> **None** </br> **IP de cliente** </br> **IP y protocolo del cliente**</br> </br> Mantenga el tráfico de un cliente a la misma máquina virtual del grupo de back-end. Este tráfico se mantendrá mientras dure la sesión. </br> **Ninguno**: especifica que cualquier máquina virtual puede controlar las solicitudes sucesivas del mismo cliente. </br> **IP de cliente**: especifica que la misma máquina virtual controlará las solicitudes sucesivas de la misma dirección IP de cliente. </br> **IP y protocolo del cliente**: especifica que la misma máquina virtual controlará las solicitudes sucesivas de la misma dirección IP de cliente y protocolo. </br> Obtenga más información sobre los [modos de distribución](load-balancer-distribution-mode.md). |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión **TCP** o **HTTP** abierta sin depender de los clientes para enviar mensajes persistentes. |  
| Restablecimiento de TCP | Un equilibrador de carga puede enviar **restablecimientos de TCP** para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md).|
| Dirección IP flotante | La dirección IP flotante es el término de Azure para referirse a una parte de lo que se conoce como **Direct Server Return (DSR)** . </br> DSR consta de dos partes: <br> 1. Topología de flujo </br> 2. Un esquema de asignación de direcciones IP en el nivel de plataforma. </br></br> Azure Load Balancer siempre funciona en una topología de flujo DSR, independientemente de si la dirección IP flotante está habilitada o no. </br> Esto significa que la parte de salida de un flujo siempre se reescribe correctamente para que se dirija de nuevo al origen. </br> Sin una dirección IP flotante, Azure expone un esquema de asignación de direcciones IP de equilibrio (la IP de las instancias de máquina virtual). </br> Habilitar la dirección IP flotante cambia el esquema de asignación de direcciones IP a la IP de servidor front-end del equilibrador de carga para permitir más flexibilidad. </br> Para obtener más información, consulte [Varios front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).|
| Traducción de direcciones de red de origen (SNAT) de salida | Las opciones son: </br> **(Recomendado) Usar reglas de salida para proporcionar acceso a Internet a los miembros del grupo de back-end**. </br> **Usar la regla de salida implícita. No es recomendable porque puede provocar el agotamiento de puertos SNAT.** </br> Seleccione la opción **Recomendado** para evitar el agotamiento de puertos SNAT. Se necesita una **puerta de enlace NAT** o **reglas de salida** para proporcionar SNAT a los miembros del grupo de back-end. Para más información sobre **NAT Gateway**, consulte [¿Qué es Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md) </br> Para más información sobre las conexiones salientes en Azure, consulte [Uso de la Traducción de direcciones de red de origen (SNAT) para conexiones salientes](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Captura de cómo se agrega una regla de equilibrio de carga." border="true":::

### <a name="inbound-nat-rules"></a>Reglas NAT de entrada

Una regla NAT de entrada reenvía el tráfico entrante enviado a la combinación de dirección IP y puerto de front-end. 

El tráfico se envía a una máquina virtual o instancia específica en el grupo de back-end. El reenvío de puertos se realiza mediante la misma distribución basada en hash que el equilibrio de carga.

Por ejemplo, si desea que las sesiones de Secure Shell (SSH) o del Protocolo de escritorio remoto (RDP) separen las instancias de máquina virtual de un grupo de back-end. Se pueden asignar varios puntos de conexión internos a puertos de la misma dirección IP de front-end. 

Las direcciones IP de front-end se pueden usar para administrar de forma remota máquinas virtuales sin un jumpbox adicional.

Si desea agregar una regla NAT de entrada a su equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Reglas NAT de entrada** y, a continuación, seleccione **+ Agregar**.

| Configuración | Detalles |
| ---------- | ---------- |
| Nombre | El nombre de la regla NAT de entrada. |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea que se asocie la regla NAT de entrada. |
| Versión de la dirección IP | Las opciones son IPv4 e IPv6. |
| Servicio | El tipo de servicio en el que se va a ejecutar Azure Load Balancer. </br> Según lo que seleccione aquí, actualizará la información de puertos adecuadamente. |
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: TCP o UDP. |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión TCP o HTTP abierta sin depender de los clientes para enviar mensajes persistentes. |
| Restablecimiento de TCP | Un equilibrador de carga puede enviar restablecimientos de TCP para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md). |
| Port | Este valor es el puerto asociado a la dirección IP de front-end en la que quiere que se distribuya el tráfico en función de esta regla NAT de entrada. |
| Máquina virtual de destino | La parte de la máquina virtual del grupo de back-end al que le gustaría asociar esta regla. |
| Asignación de puertos | Este valor puede ser el predeterminado o personalizada según las preferencias de la aplicación. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Captura de pantalla de cómo se agrega una regla NAT de entrada." border="true":::

### <a name="outbound-rules"></a>Reglas de salida

Las reglas de salida del equilibrador de carga configuran SNAT saliente para las máquinas virtuales del grupo de back-end.

Si desea agregar una regla de salida al equilibrador de carga, vaya a su equilibrador de carga en Azure Portal, seleccione **Reglas de salida** y, a continuación, seleccione **+ Agregar**.

| Configuración | Detalles |
| ------- | ------ |
| Nombre | El nombre de la regla de salida. |
| Dirección IP del front-end | Seleccione la dirección IP de front-end. </br> La dirección IP de front-end del equilibrador de carga al que desea que se asocie la regla de salida. |
| Protocolo | Azure Load Balancer es un equilibrador de carga de nivel 4. </br> Tendrá las siguientes opciones: **Todos**, **TCP** o **UDP**. |
| Tiempo de espera de inactividad (minutos) | Mantenga una conexión **TCP** o **HTTP** abierta sin depender de los clientes para enviar mensajes persistentes. |
| Restablecimiento de TCP | Un equilibrador de carga puede enviar **restablecimientos de TCP** para ayudar a crear un comportamiento de aplicación más predecible en el momento en que la conexión está inactiva. </br> Obtenga más información sobre los [restablecimientos de TCP](load-balancer-tcp-reset.md). |
| Grupo back-end | El grupo de back-end en el que desea aplicar esta regla de salida. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Captura de pantalla de cómo se agrega una regla de salida." border="true":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido los distintos términos y configuraciones de Azure Portal para Azure Load Balancer.

* Obtenga [más información](./load-balancer-overview.md) sobre Azure Load Balancer.
* [Preguntas más frecuentes](./load-balancer-faqs.yml) sobre Azure Load Balancer.