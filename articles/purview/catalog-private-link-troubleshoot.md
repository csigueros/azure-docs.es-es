---
title: Solución de problemas de configuración de punto de conexión privado de cuentas de Purview
description: En este artículo se describe cómo solucionar problemas con la cuenta de Purview relacionados con las configuraciones de puntos de conexión privados.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c077eb0c1639089fcc7196693a617e32c01d9a9a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230463"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>Solución de problemas de configuración de punto de conexión privado de cuentas de Purview

> [!IMPORTANT]
> Si creó un punto de conexión privado del _portal_ para su cuenta de Purview **antes del 27 de septiembre de 2021 a las 15:30 UTC**, deberá realizar las acciones necesarias como se detalla en [Reconfiguración de DNS para puntos de conexión privados del portal](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints). **Estas acciones deben realizarse antes del 11 de octubre de 2021. Si no lo hace, los puntos de conexión privados del portal existentes dejarán de funcionar**.

En esta guía se resumen las limitaciones conocidas relacionadas con el uso de puntos de conexión privados para Azure Purview y se proporciona una lista de pasos y soluciones para resolver algunos de los problemas relevantes más comunes. 

## <a name="known-limitations"></a>Limitaciones conocidas

- Actualmente no se admiten los puntos de conexión privados de ingesta que funcionan con orígenes de AWS.
- No se admite el análisis de varios orígenes de Azure mediante el entorno de ejecución de integración autohospedado.
- No se admite el uso del entorno de ejecución de integración de Azure para examinar orígenes de datos detrás de un punto de conexión privado.
- Con Azure Portal, los puntos de conexión privados de la ingesta se pueden crear a través de la experiencia del portal de Azure Purview que se describe en los pasos anteriores. No se pueden crear desde Private Link Center.
- No se permite la creación de registros de DNS A para la ingesta de puntos de conexión privados dentro de las zonas DNS de Azure existentes a través de la experiencia del portal de Azure Purview si las zonas DNS privadas de Azure se encuentran en una suscripción diferente que los puntos de conexión privados. Se pueden agregar registros A manualmente en las zonas DNS de destino de la otra suscripción. 
- La máquina del entorno de ejecución de integración autohospedado debe implementarse en la misma red virtual donde está implementado el punto de conexión privado de ingesta de Azure Purview.
- Actualmente no se admite el examen de un inquilino Power BI que tenga un punto de conexión privado configurado con el acceso público bloqueado.
- Para conocer la limitación en relación con el servicio Private Link, consulte [Límites de Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="recommended-troubleshooting-steps"></a>Pasos recomendados para la solución de problemas  

1. Una vez que implemente puntos de conexión privados para la cuenta de Purview, revise el entorno de Azure para asegurarse de que los recursos de puntos de conexión privados están implementados correctamente. En función de su escenario, uno o varios de los siguientes puntos de conexión privados de Azure deben implementarse en su suscripción de Azure:

    |Punto de conexión privado  |Punto de conexión privado asignado a | Ejemplo|
    |---------|---------|---------|
    |Cuenta  |Cuenta de Azure Purview         |mypurview-private-account  |
    |Portal     |Cuenta de Azure Purview         |mypurview-private-portal  |
    |Ingesta de datos     |Cuenta de almacenamiento administrada (blob)         |mypurview-ingestion-blob  |
    |Ingesta de datos     |Cuenta de almacenamiento administrada (cola)         |mypurview-ingestion-queue  |
    |Ingesta de datos     |Espacio de nombres de Event Hubs administrado         |mypurview-ingestion-namespace  |

2. Si el punto de conexión privado del portal está implementado, asegúrese de implementar también el punto de conexión privado de la cuenta.

3. Si el punto de conexión privado del portal está implementado y el acceso a la red pública está establecido para denegarse en la cuenta de Azure Purview, asegúrese de iniciar [Azure Purview Studio](https://web.purview.azure.com/resource/) desde la red interna.
  <br>
    - Para comprobar la resolución de nombres correcta, puede usar la herramienta de línea de comandos **NSlookup.exe** para consultar `web.purview.azure.com`. El resultado debe devolver una dirección IP privada que pertenezca al punto de conexión privado del portal. 
    - Para comprobar la conectividad de red, puede usar cualquier herramienta de prueba de red para probar la conectividad de salida al punto de conexión `web.purview.azure.com` con el puerto **443**. La conexión debe establecerse correctamente.    

3. Si se usan zonas DNS privadas de Azure, asegúrese de que se implementan las zonas DNS de Azure necesarias y de que hay un registro DNS (A) para cada punto de conexión privado.

4. Pruebe la conectividad de red y la resolución de nombres de la máquina de administración al punto de conexión de Purview y a la dirección URL web de Purview. Si se implementan puntos de conexión privados de la cuenta y del portal, los puntos de conexión deben resolverse a través de direcciones IP privadas.


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    Ejemplo de conexión de salida correcta a través de una dirección IP privada:

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    Ejemplo de conexión de salida correcta a través de una dirección IP privada:

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. Si ha creado la cuenta de Azure Purview después del 18 de agosto de 2021, asegúrese de descargar e instalar la versión más reciente del entorno de ejecución de integración autohospedado desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
   
6. Desde una VM de entorno de ejecución de integración autohospedado, pruebe la conectividad de red y la resolución de nombres en el punto de conexión de Purview.

7. Desde el entorno de ejecución de integración autohospedado, pruebe la conectividad de red y la resolución de nombres en recursos administrados de Azure Purview, como la cola de blobs y el centro de eventos, a través del puerto 443 y las direcciones IP privadas. (Reemplace la cuenta de almacenamiento administrada y el espacio de nombres de Event Hubs por el nombre de recurso administrado correspondiente asignado a la cuenta de Azure Purview).

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    Ejemplo de conexión de salida correcta al servicio Blob Storage administrado a través de una dirección IP privada:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    Ejemplo de conexión de salida correcta al servicio Queue Storage administrado a través de una dirección IP privada:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    Ejemplo de conexión de salida correcta al espacio de nombres de EventHub a través de una dirección IP privada:

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. Desde la red donde se encuentra el origen de datos, pruebe la conectividad de red y la resolución de nombres en el punto de conexión de Purview y los puntos de conexión de recursos administrados.

9.  Si los orígenes de datos se encuentran en la red local, revise la configuración del reenviador de DNS. Pruebe la resolución de nombres desde la misma red en la que se encuentran los orígenes de datos en el entorno de ejecución de integración autohospedado, los puntos de conexión de Azure Purview y los recursos administrados. Se espera obtener una dirección IP privada válida de la consulta DNS para cada punto de conexión.
    
    Para más información, consulte [Cargas de trabajo de red virtual sin un servidor DNS personalizado](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) y [Cargas de trabajo locales que utilizan un reenviador DNS](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) en [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

10. Si la máquina de administración y las VM del entorno de ejecución de integración autohospedado están implementadas en la red local y ha configurado el reenviador DNS en su entorno, compruebe la configuración de DNS y de red en su entorno. 

11. Si se usa el punto de conexión privado de ingesta, asegúrese de que el entorno de ejecución de integración autohospedado esté registrado correctamente en la cuenta de Purview y de que se muestre en ejecución tanto en la VM del entorno de ejecución de integración autohospedado como en [Purview Studio](https://web.purview.azure.com/resource/).

## <a name="common-errors-and-messages"></a>Errores habituales y mensajes

### <a name="issue"></a>Problema 
Recibe el siguiente mensaje de error al ejecutar un examen:

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### <a name="cause"></a>Causa 
Puede indicar problemas relacionados con la conectividad o la resolución de nombres entre la VM que ejecuta el entorno de ejecución de integración autohospedado y la cuenta de almacenamiento o el centro de eventos de los recursos administrados de Azure Purview.

### <a name="resolution"></a>Solución 
Valide la resolución de nombres entre la VM que ejecuta el entorno de ejecución de integración autohospedado.


### <a name="issue"></a>Problema 
Recibe el siguiente mensaje de error al ejecutar un nuevo examen:

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### <a name="cause"></a>Causa 
Puede indicar la ejecución de una versión anterior del entorno de ejecución de integración autohospedado. Si ha creado la cuenta de Azure Purview después del 18 de agosto de 2021, debe usar la versión 5.9.7885.3 del entorno de ejecución de integración autohospedado.

### <a name="resolution"></a>Solución 
Actualice el entorno de ejecución de integración autohospedado a la versión 5.9.7885.3.


### <a name="issue"></a>Problema 
Error de implementación de la cuenta de Azure Purview con el punto de conexión privado y error de validación de Azure Policy durante la implementación.

### <a name="cause"></a>Causa
Este error sugiere que puede haber una asignación de Azure Policy en la suscripción de Azure que impide la implementación de cualquiera de los recursos de Azure necesarios. 

### <a name="resolution"></a>Solución 
Revise las asignaciones de Azure Policy existentes y asegúrese de que se permite la implementación de los siguientes recursos de Azure en la suscripción de Azure. 
   
> [!NOTE]
> En función de su escenario, es posible que tenga que implementar uno o varios de los siguientes tipos de recursos de Azure: 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   Punto de conexión privado (Microsoft.Network/privateEndpoints)
>  -   Zonas DNS privadas (Microsoft.Network/privateDnsZones)
>  -   Espacio de nombres del Centro de eventos (Microsoft.EventHub/namespaces)
>  -   Cuenta de almacenamiento (Microsoft.Storage/storageAccounts)


### <a name="issue"></a>Problema
No tiene autorización para acceder a esta cuenta de Purview. Esta cuenta de Purview está detrás de un punto de conexión privado. Acceda a la cuenta desde un cliente de la misma red virtual (VNet) que se haya configurado para el punto de conexión privado de la cuenta de Purview.

### <a name="cause"></a>Causa 
El usuario está intentando conectarse a Azure Purview desde un punto de conexión público o mediante puntos de conexión públicos de Azure Purview con el **acceso a la red pública** establecido en **Denegar**.

### <a name="resolution"></a>Solución
En este caso, para abrir Azure Purview Studio, use una máquina que esté implementada en la misma red virtual que el punto de conexión privado del portal de Azure Purview, o bien use una VM que esté conectada a CorpNet en la que se permita la conectividad híbrida.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece en este artículo o no lo puede resolver, visite uno de los siguientes canales para obtener ayuda:

- Obtenga respuestas de expertos a través de [Preguntas y respuestas de Microsoft](/answers/topics/azure-purview.html).
- Conectar con [@AzureSupport](https://twitter.com/azuresupport). Este recurso oficial de Microsoft Azure en Twitter ayuda a mejorar la experiencia del cliente al conectar la comunidad de Azure a las respuestas, el soporte técnico y los expertos adecuados.
- Si sigue necesitando ayuda, vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Enviar una solicitud de soporte técnico**.
