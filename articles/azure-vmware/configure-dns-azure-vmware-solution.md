---
title: Configuración de un reenviador DNS para Azure VMware Solution
description: Aprenda a configurar un reenviador DNS para Azure VMware Solution desde Azure Portal.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323945"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configuración de un reenviador DNS en Azure Portal

>[!IMPORTANT]
>En las nubes privadas de Azure VMware Solution a partir del 1 de julio de 2021, ahora tiene la capacidad de configurar la resolución DNS privada. En el caso de las nubes privadas creadas antes del 1 de julio de 2021, y que necesitan una resolución DNS privada, abra una [solicitud de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support) y pida la configuración de DNS privado. 

De forma predeterminada, los componentes de administración de Azure VMware Solution, como vCenter, solo pueden resolver los registros de nombres disponibles a través de DNS público. Sin embargo, algunos casos de uso híbridos requieren que los componentes de administración de Azure VMware Solution resuelvan los registros de nombres desde un DNS hospedado de forma privada para funcionar correctamente, incluidos los sistemas administrados por el cliente, como vCenter y Active Directory.

DNS privado para los componentes de administración de Azure VMware Solution permite definir reglas de reenvío condicional para el nombre de dominio deseado a un conjunto seleccionado de servidores DNS privados a través del servicio DNS en NSX-T. 

Esta funcionalidad usa el servicio reenviador DNS en NSX-T. Se proporcionan un servicio DNS y una zona DNS predeterminada como parte de la nube privada. Para habilitar los componentes de la administración de Azure VMware Solution para resolver registros de los sistemas DNS privados, debe definir una zona FQDN y aplicarla al servicio DNS en NSX-T. El servicio DNS reenvía condicionalmente las consultas DNS de cada zona en función de los servidores DNS externos definidos en la zona.

>[!NOTE]
>El servicio DNS está asociado a un máximo de cinco zonas FQDN. Cada zona FQDN está asociada a un máximo de tres servidores DNS.

>[!TIP]
>Si lo desea, también puede usar las reglas de reenvío condicional para los segmentos de la carga de trabajo mediante la configuración de máquinas virtuales en esos segmentos para que usen la dirección IP del servicio DNS en NSX-T como servidor DNS.


## <a name="architecture"></a>Architecture

En el diagrama se muestra que el servicio DNS en NSX-T puede reenviar consultas de DNS a sistemas DNS hospedados tanto en Azure como en entornos locales.

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="En el diagrama se muestra que el servicio DNS en NSX-T puede reenviar consultas de DNS a sistemas DNS hospedados tanto en Azure como en entornos locales." border="false":::


## <a name="configure-dns-forwarder"></a>Configuración de reenviador de DNS

1. En la nube privada de Azure VMware Solution, en **Workload Networking** (Red de la carga de trabajo), seleccione **DNS** > **DNS Zones** (Zonas DNS). A continuación, seleccione **Agregar**.

   >[!NOTE]
   >En el caso de las nubes privadas creadas a partir del 1 de julio de 2021, la zona DNS predeterminada se crea automáticamente durante la creación de la nube privada.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="Captura de pantalla que muestra cómo agregar zonas DNS a una nube privada de Azure VMware Solution.":::

1. Seleccione **FQDN zone** (Zona FQDN), especifique un nombre y un máximo de tres direcciones IP de servidor DNS con el formato **10.0.0.53**. Después, seleccione **Aceptar**.

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Captura de pantalla que muestra la información necesaria para agregar una zona FQDN.":::

   >[!IMPORTANT]
   >Aunque NSX-T permite espacios y otros caracteres no alfanuméricos en los nombres de las zonas DNS, determinados recursos de NSX, como una zona DNS, se asignan a un recurso de Azure cuyos nombres no permiten ciertos caracteres. 
   >
   >En consecuencia, es posible sea necesario realizar ajustes en los nombres de zona DNS que serían válidos en NSX-T para cumplir las [convenciones de nomenclatura de recursos de Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftresources).

      Tarda varios minutos en completarse y el progreso se puede seguir en **Notificaciones**. Cuando se haya creado la zona DNS verá un mensaje en Notificaciones.

1. Ignore el mensaje sobre una zona DNS predeterminada porque se crea una automáticamente como parte de la nube privada.

1. Seleccione la pestaña **Servicio DNS** y luego seleccione **Editar**.

   >[!TIP]
   >En el caso de las nubes privadas a partir del 1 de julio de 2021, puede omitir el mensaje sobre una zona DNS predeterminada, ya que se crea una automáticamente durante la creación de la nube privada.


   >[!IMPORTANT]
   >Aunque ciertas operaciones de la nube privada se pueden realizar desde NSX-T Manager, en el caso de las nubes privadas creadas a partir del 1 de julio de 2021, inclusive, _debe_ editar el servicio DNS desde Redes simplificadas, en Azure Portal, para cualquier cambio de configuración realizado en la puerta de enlace de nivel 1 predeterminada.  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="Captura de pantalla que muestra la pestaña Servicio DNS con el botón Editar seleccionado.":::   

1. En la lista desplegable **FQDN zones** (Zonas FQDN), seleccione el nombre de dominio completo recién creado y, después, seleccione **Aceptar**.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="Captura de pantalla que muestra el FQDN seleccionado para el servicio DNS.":::

   Tarda varios minutos en completarse y, una vez finalizado, verá el mensaje *Completado* en **Notificaciones**. En este momento, los componentes de administración de la nube privada deben ser capaces de resolver las entradas DNS de la zona FQDN proporcionada al servicio DNS en NSX-T. 

1. Repita los pasos anteriores para las restantes zonas de FQDN, incluidas las zonas de búsqueda inversa aplicables.


## <a name="verify-name-resolution-operations"></a>Comprobación de las operaciones de resolución de nombres

Después de configurar el reenviador DNS, hay varias operaciones que podrá usar para comprobar las operaciones de resolución de nombres. 

### <a name="nsx-t-manager"></a>NSX-T Manager

NSX-T Manager proporciona las estadísticas del servicio Reenviador DNS en el nivel de servicio global y por zona. 

1. En NSX-T Manager, seleccione **Networking** > **DNS** (Redes > DNS) y, después, expanda el servicio Reenviador DNS.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="Captura de pantalla que muestra la pestaña Servicios DNS en NSX-T Manager.":::

1. Seleccione **Ver estadísticas** y, después, en la lista desplegable **Zone Statistics** (Estadísticas de zona), seleccione la zona FQDN.

   En la mitad superior se muestran las estadísticas de todo el servicio, mientras que en la inferior se muestran las de la zona especificada. En este ejemplo, se pueden ver las consultas reenviadas a los servicios DNS especificados durante la configuración de la zona FQDN.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="Captura de pantalla que muestra las estadísticas de Reenviador DNS.":::


### <a name="powercli"></a>PowerCLI

NSX-T Policy API permite ejecutar comandos nslookup desde el servicio Reenviador DNS NSX-T. Los cmdlets necesarios forman parte del módulo `VMware.VimAutomation.Nsxt` en PowerCLI. En el ejemplo siguiente se muestra la salida de la versión 12.3.0 de ese módulo.

1. Conéctese al servidor NSX-T. 

   >[!TIP]
   >La dirección IP del servidor NSX-T se puede obtener de Azure Portal, en **Administrar** > **Identidad**.
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. Obtenga un proxy para el servicio nslookup de Reenviador DNS.

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. Realice búsquedas desde el servicio Reenviador DNS.

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  El primer parámetro del comando es el identificador de la puerta de enlace T1 de la nube privada, que se puede obtener en la pestaña Servicio DNS de Azure Portal.

1. Obtenga una respuesta sin procesar de la búsqueda mediante las siguientes propiedades de la respuesta.

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   En este ejemplo, puede ver una respuesta a la consulta de vc01.contoso.corp que muestra un registro D con la dirección 172.21.90.2. Además, en este ejemplo se muestra una respuesta almacenada en caché del servicio Reenviador DNS, por lo que la salida puede variar ligeramente.
