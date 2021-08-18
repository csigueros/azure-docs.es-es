---
title: 'Implementación de un equilibrador de carga con conexión a Internet con IPv6: plantilla de Azure'
titleSuffix: Azure Load Balancer
description: Aprenda a implementar la compatibilidad de IPv6 con Azure Load Balancer y máquinas virtuales con equilibrio de carga mediante una plantilla de Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, pila doble, dirección ip pública, ipv6 nativo, móvil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 1f0c4f7f70dc186c29ffa5d90c6a7ea2f245f431
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298086"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementación de una solución de equilibrador de carga orientado a Internet con IPv6 mediante el uso de una plantilla

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI de Azure](load-balancer-ipv6-internet-cli.md)
> * [Plantilla](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>En este artículo se describe una característica de IPv6 introductoria que permite que los equilibradores de carga básicos proporcionen conectividad IPv4 e IPv6. Ahora hay disponible conectividad IPv6 completa con [IPv6 para redes virtuales de Azure](../virtual-network/ipv6-overview.md) que integra conectividad IPv6 con las redes virtuales e incluye características clave como las reglas de grupo de seguridad de red IPv6, el enrutamiento definido por el usuario IPv6, el equilibrio de carga de IPv6 básico y estándar, y mucho más.  IPv6 para redes virtuales de Azure es el estándar recomendado para las aplicaciones IPv6 en Azure. Vea [IPv6 para la implementación de PowerShell de red virtual de Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).  

Azure Load Balancer es un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye proporcionando una alta disponibilidad el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales de un conjunto de carga equilibrada. Azure Load Balancer también pueden presentar prestar servicios en varios puertos, varias direcciones IP o ambos.

## <a name="example-deployment-scenario"></a>Escenario de implementación de ejemplo

En el siguiente diagrama se ilustra la solución de equilibrio de carga que se implementa mediante la plantilla de ejemplo descrita en este artículo.

![En el diagrama se muestra un escenario de ejemplo que se usa en este artículo, que incluye un cliente de estación de trabajo conectado a una instancia de Azure Load Balancer a través de Internet, conectado a su vez a dos máquinas virtuales.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

En este escenario creará los siguientes recursos de Azure:

* una interfaz de red virtual para cada máquina virtual con las direcciones IPv4 e IPv6 asignadas
* un equilibrador de carga orientado a Internet con una dirección IP pública IPv4 e IPv6
* dos reglas de equilibrio de carga para asignar las VIP públicas a los puntos de conexión privados
* un conjunto de disponibilidad con las dos máquinas virtuales
* dos máquinas virtuales (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementación de la plantilla con Azure Portal

En este artículo se hace referencia a una plantilla publicada en la galería de [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/load-balancer-ipv6-create/) . Puede descargar la plantilla desde la galería o iniciar la implementación en Azure directamente desde la galería. En este artículo se da por hecho que ha descargado la plantilla en el equipo local.

1. Abra Azure Portal e inicie sesión con una cuenta que tenga permisos para crear máquinas virtuales y recursos de red en una suscripción a Azure. Además, salvo que use recursos existentes, la cuenta necesita permiso para crear un grupo de recursos y una cuenta de almacenamiento.
2. Haga clic en "+Nuevo" desde el menú y, a continuación, escriba "plantilla" en el cuadro de búsqueda. Seleccione "Implementación de plantillas" en los resultados de la búsqueda.

    ![Captura de pantalla que muestra Azure Portal con las opciones Nuevo y Template Deployment seleccionadas.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. En la hoja Todo, haga clic en "Implementación de plantillas".

    ![Captura de pantalla que muestra Template Deployment en Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Haga clic en "Crear".

    ![Captura de pantalla que muestra la descripción de Template Deployment en Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Haga clic en "Editar plantilla". Elimine el contenido existente y copie y pegue todo el contenido del archivo de plantilla (para incluir la llave de apertura y la de cierre { }) y, a continuación, haga clic en "Guardar".

    > [!NOTE]
    > Si usa Microsoft Internet Explorer, al pegar recibirá un cuadro de diálogo pidiéndole que permita el acceso al Portapapeles de Windows. Haga clic en "Permitir acceso".

    ![Captura de pantalla que muestra el primer paso de una implementación personalizada, que es Editar plantilla.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Haga clic en "Editar parámetros". En la hoja Parámetros, especifique los valores según las instrucciones que se proporcionan en la sección Parámetros de plantilla y, a continuación, haga clic en "Guardar" para cerrar la hoja Parámetros. En la hoja Implementación personalizada, seleccione su suscripción, un grupo de recursos existente o cree uno. Si crea un grupo de recursos, seleccione una ubicación para este. A continuación, haga clic en **Términos legales** y después en **Comprar** los términos legales. Azure empieza a implementar los recursos. Tarda varios minutos en implementar todos los recursos.

    ![Captura de pantalla que muestra los pasos implicados en la implementación personalizada, empezando por la especificación de valores de parámetro de plantilla.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obtener más información sobre estos parámetros, consulte la sección [Template parameters and variables](#template-parameters-and-variables) (Variables y parámetros de plantilla) más adelante en este artículo.

7. Para ver los recursos creados por la plantilla, haga clic en Examinar, desplácese hacia abajo en la lista hasta que vea "Grupos de recursos" y, a continuación, haga clic ahí.

    ![Captura de pantalla que muestra Azure Portal con las opciones Examinar y Grupos de recursos seleccionadas.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. En la hoja Grupos de recursos, haga clic en el nombre del grupo de recursos que especificó en el paso 6. Verá una lista de todos los recursos que se implementaron. Si todo ha ido bien, debe poner "Correcto" en "Última implementación". Si no es así, asegúrese de que la cuenta que usa tiene permisos para crear los recursos necesarios.

    ![Captura de pantalla que muestra el estado de la última implementación de un grupo de recursos, en este ejemplo, Correcto.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Si examina sus grupos de recursos inmediatamente después de completar el paso 6, "Última implementación" mostrará el estado de "Implementación" mientras se implementan los recursos.

9. Haga clic en "myIPv6PublicIP" en la lista de recursos. Verá que tiene una dirección IPv6 en la dirección IP y que su nombre DNS es el valor que especificó para el parámetro dnsNameforIPv6LbIP en el paso 6. Este recurso es la dirección IPv6 pública y el nombre de host al que pueden tener acceso los clientes de Internet.

    ![Captura de pantalla que muestra la dirección IPv6 pública.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar conectividad

Cuando la plantilla se haya implementado correctamente, puede validar la conectividad completando las siguientes tareas:

1. Inicie sesión en Azure Portal y conéctese a cada una de las máquinas virtuales creadas por la implementación de plantilla. Si implementó una máquina virtual de Windows Server, ejecute ipconfig /all desde un símbolo del sistema. Verá que las máquinas virtuales tienen direcciones IPv4 e IPv6. Si implementó máquinas virtuales con Linux, debe configurar el sistema operativo Linux para recibir direcciones IPv6 dinámicas con las instrucciones proporcionadas para la distribución de Linux.
2. Desde un cliente conectado a Internet por IPv6, inicie una conexión a la dirección IPv6 pública del equilibrador de carga. Para confirmar que el equilibrador de carga se equilibra entre las dos máquinas virtuales, podría instalar un servidor web como Microsoft Internet Information Services (IIS) en cada una de las máquinas virtuales. La página web predeterminada de cada servidor podría contener el texto "Servidor0" o "Servidor1" para identificarlo de forma exclusiva. A continuación, abra un explorador de Internet en un cliente conectado a Internet por IPv6 y vaya al nombre de host que especificó para el parámetro dnsNameforIPv6LbIP del equilibrador de carga a fin de confirmar la conectividad de IPv6 completa a cada máquina virtual. Si solo ve la página web desde un único servidor, es posible que tenga que borrar su caché de exploración. Abra varias sesiones de exploración privadas. Debería obtener una respuesta de cada servidor.
3. Desde un cliente conectado a Internet por IPv4, inicie una conexión a la dirección IPv4 pública del equilibrador de carga. Para confirmar que el equilibrador de carga equilibra la carga de las dos máquinas virtuales, podría probar a usar IIS, tal como se detalla en el paso 2.
4. En cada máquina virtual, inicie una conexión saliente a un dispositivo de Internet conectado a IPv6 o IPv4. En ambos casos, la dirección IP de origen vista por el dispositivo de destino es la dirección IPv4 o IPv6 pública del equilibrador de carga.

> [!NOTE]
> En la red de Azure se bloquea ICMP para IPv4 e IPv6. Como resultado, las herramientas ICMP tales como ping siempre producen algún error. Para probar la conectividad, use una alternativa a TCP como TCPing o el cmdlet Test-NetConnection de PowerShell. Tenga en cuenta que las direcciones IP que se muestran en el diagrama son ejemplos de valores que puede ver. Puesto que las direcciones IPv6 se asignan dinámicamente, las direcciones que reciba serán diferentes y pueden variar según la región. Además, es habitual que la dirección IPv6 pública del equilibrador de carga empiece por un prefijo distinto al de las direcciones IPv6 privadas del grupo de direcciones de back-end.

## <a name="template-parameters-and-variables"></a>Template parameters and variables

Una plantilla de Azure Resource Manager contiene varias variables y parámetros que puede personalizar según sus necesidades. Las variables se usan para valores fijos que no desea que un usuario cambie. Los parámetros se usan para valores que desea que un usuario proporcione al implementar la plantilla. La plantilla de ejemplo está configurada para el escenario descrito en este artículo. Puede personalizarla para adaptarla a las necesidades de su entorno.

En la plantilla de ejemplo usada en este artículo se incluyen las variables y los parámetros siguientes:

| Parámetro / Variable | Notas |
| --- | --- |
| adminUsername |Especifique el nombre de la cuenta de administrador usada para iniciar sesión en las máquinas virtuales. |
| adminPassword |Especifique la contraseña de la cuenta de administrador usada para iniciar sesión en las máquinas virtuales. |
| dnsNameforIPv4LbIP |Especifique el nombre de host DNS que desea asignar como nombre público del equilibrador de carga. Este nombre se resuelve como la dirección IPv4 pública del equilibrador de carga. El nombre debe estar en minúscula y coincidir con la regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Especifique el nombre de host DNS que desea asignar como nombre público del equilibrador de carga. Este nombre se resuelve como la dirección IPv6 pública del equilibrador de carga. El nombre debe estar en minúscula y coincidir con la regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Puede tratarse del mismo nombre que el de la dirección IPv4. Cuando un cliente envía una consulta de DNS para este nombre, Azure devolverá los registros A y AAAA al compartirse el nombre. |
| vmNamePrefix |Especifique el prefijo de nombre de la máquina virtual. La plantilla anexa un número (0, 1, etc.) al nombre cuando se crean las máquinas virtuales. |
| nicNamePrefix |Especifique el prefijo de nombre de la interfaz de red. La plantilla anexa un número (0, 1, etc.) al nombre cuando se crean las interfaces de red. |
| storageAccountName |Escriba el nombre de una cuenta de almacenamiento existente o especifique el nombre de una nueva que va a crear la plantilla. |
| availabilitySetName |Escriba el nombre del conjunto de disponibilidad que se va a usar con las máquinas virtuales |
| addressPrefix |El prefijo de dirección usado para definir el intervalo de direcciones de Virtual Network |
| subnetName |Se crea el nombre de la subred para la red virtual |
| subnetPrefix |El prefijo de dirección usado para definir el intervalo de direcciones de la subred |
| vnetName |Especifique el nombre para la red virtual que usan las máquinas virtuales. |
| ipv4PrivateIPAddressType |El método de asignación usado para la dirección IP privada (estática o dinámica) |
| ipv6PrivateIPAddressType |El método de asignación usado para la dirección IP privada (dinámica). IPv6 solo admite la asignación dinámica. |
| numberOfInstances |El número de instancias con equilibrio de carga que implementa la plantilla |
| ipv4PublicIPAddressName |Especifique el nombre DNS que desea usar para comunicarse con la dirección IPv4 pública del equilibrador de carga. |
| ipv4PublicIPAddressType |El método de asignación usado para la dirección IP pública (estática o dinámica) |
| Ipv6PublicIPAddressName |Especifique el nombre DNS que desea usar para comunicarse con la dirección IPv6 pública del equilibrador de carga. |
| ipv6PublicIPAddressType |El método de asignación usado para la dirección IP pública (dinámica). IPv6 solo admite la asignación dinámica. |
| lbName |Especifique el nombre del equilibrador de carga. Este nombre se muestra en el portal o se usa al hacer referencia a él con un comando de la CLI o de PowerShell. |

Las variables restantes de la plantilla contienen valores derivados que se asignan cuando Azure crea los recursos. No cambie esas variables.

## <a name="next-steps"></a>Pasos siguientes

Para la sintaxis JSON y las propiedades de un equilibrador de carga en una plantilla, consulte [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
