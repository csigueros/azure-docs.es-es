---
title: Diagnóstico de problemas de configuración de vínculos privados en Azure Key Vault
description: Resuelva problemas comunes de vínculos privados con Key Vault y obtenga más detalles de la configuración
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 85760971b8f4eb8bfbb431193636532a498d7236
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665608"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnóstico de problemas de configuración de vínculos privados en Azure Key Vault

## <a name="introduction"></a>Introducción

Gracias a este artículo los usuarios podrán diagnosticar y corregir problemas relacionados con Key Vault y la característica de vínculos privados. En esta guía obtendrá ayuda acerca de los aspectos de la configuración, como la obtención de vínculos privados en funcionamiento por primera vez, o cómo corregir una situación en la que los vínculos privados dejan de funcionar debido a algún cambio.

Si no está familiarizado con esta característica, consulte [Integración de Key Vault con Azure Private Link](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problemas descritos en este artículo

- Las consultas de DNS siguen devolviendo una dirección IP pública del almacén de claves, en lugar de la dirección IP privada que se tiene que devolver al usar la característica de vínculos privados.
- Todas las solicitudes que haya realizado un cliente determinado que esté usando un vínculo privado producen errores de tiempo de espera o de red, y el problema no es intermitente.
- El almacén de claves tiene una dirección IP privada, pero las solicitudes todavía obtienen la respuesta `403` con el código de error interno `ForbiddenByFirewall`.
- Está usando vínculos privados, pero el almacén de claves sigue aceptando solicitudes de la red pública de Internet.
- El almacén de claves tiene dos puntos de conexión privados. Las solicitudes que usan un punto de conexión funcionan bien, pero las solicitudes que usan el otro punto de conexión devuelven errores.
- Tiene otra suscripción, un almacén de claves o una red virtual que usa vínculos privados. Quiere crear una implementación similar, pero no puede obtener vínculos privados para trabajar con ella.

### <a name="problems-not-covered-by-this-article"></a>Problemas NO descritos en este artículo

- Existen un problema de conectividad intermitente. En un cliente determinado, verá que algunas solicitudes funcionan y otras no. *Normalmente, los problemas intermitentes no los causa un problema en la configuración de vínculos privados; estos son un signo de carga de red o de cliente.*
- Está usando un producto de Azure que admite BYOK (Bring Your Own Key) o CMK (claves administradas por el cliente), o accede a los secretos almacenados en el almacén de claves. Cuando habilita el firewall en la configuración del almacén de claves, ese producto no puede acceder a su almacén de claves. *Consulte la documentación específica del producto. Asegúrese de que indica explícitamente la compatibilidad de los almacenes de claves con el firewall habilitado. Póngase en contacto con el servicio de soporte técnico de ese producto específico, si es necesario.*

### <a name="how-to-read-this-article"></a>Cómo leer este artículo

Si no está familiarizado con los vínculos privados o está evaluando realizar una implementación compleja, se recomienda que lea todo el artículo. En caso contrario, no dude en elegir la sección que mejor se adapte al problema al que se enfrenta.

Comencemos.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Confirme que usted es el propietario de la conexión de cliente

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Confirme que el cliente se ejecuta en la red virtual

Esta guía está pensada para ayudarle a corregir las conexiones al almacén de claves que se originan a partir del código de la aplicación. Algunos ejemplos son las aplicaciones y los scripts que se ejecutan en Azure Virtual Machines, los clústeres de Azure Service Fabric, Azure App Service, Azure Kubernetes Service (AKS) y otros similares. Esta guía también se aplica a los accesos a través de la interfaz de usuario basada en web de Azure Portal, donde el explorador accede directamente al almacén de claves.

Al usar vínculos privados, la aplicación, el script o el portal deben ejecutarse en la máquina, clúster o entorno conectado a Virtual Network en el que se implementó el [recurso del punto de conexión privado](../../private-link/private-endpoint-overview.md).

Si la aplicación, el script o el portal se ejecuta en una red arbitraria conectada a Internet, esta guía NO es aplicable y es probable que no se puedan usar vínculos privados. Esta limitación también se aplica a los comandos que se ejecutan en Azure Cloud Shell, ya que se ejecutan en una máquina remota de Azure que se proporciona a petición en lugar del explorador del usuario.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Si usa una solución administrada, consulte la documentación específica

Esta guía NO es aplicable a las soluciones que administra Microsoft, donde se tiene acceso al almacén de claves mediante un producto de Azure que existe independientemente de la instancia de Virtual Network de cliente. Algunos ejemplos de estos escenarios son Azure Storage o Azure SQL, los cuales se han configurado para realizar el cifrado en reposo; también se utiliza el Centro de eventos de Azure para cifrar datos con claves proporcionadas por el cliente, Azure Data Factory para acceder a las credenciales de servicio almacenadas en el almacén de claves, Azure Pipelines para recuperar secretos del almacén de claves y otros escenarios similares. En estos casos, *debe comprobar si el producto es admite los almacenes de claves con el firewall habilitado*. Esta compatibilidad se realiza normalmente con la característica [Servicios de confianza](overview-vnet-service-endpoints.md#trusted-services) del firewall de Key Vault. Sin embargo, muchos de otros productos no se incluyen en la lista de servicios de confianza por diversos motivos. En ese caso, póngase en contacto con el soporte técnico específico del producto.

Un pequeño número de productos de Azure admite el concepto de *inyección de redes virtuales*. Esto quiere decir que el producto agrega un dispositivo de red en la instancia de Virtual Network del cliente, lo que le permite enviar solicitudes como si se hubiera implementado en la misma instancia de Virtual Network. Un ejemplo importante es [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Productos como este pueden realizar solicitudes al almacén de claves mediante los vínculos privados; por ello, esta guía de solución de problemas puede serle útil.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Confirme que la conexión se ha aprobado y realizado correctamente

En los pasos siguientes se confirma que la conexión del punto de conexión privado está aprobada y que se ha realizado correctamente:

1. Abra Azure Portal y el recurso del almacén de claves.
2. En el menú de la izquierda, seleccione la opción **Redes**.
3. Haga clic en la pestaña **Conexiones de punto de conexión privado**. Se mostrarán todas las conexiones del punto de conexión privado y sus estados respectivos. Si no hay ninguna conexión o si falta la conexión de Virtual Network, tendrá que crear un nuevo punto de conexión privado. Este tema se tratará más adelante.
4. En la misma pestaña **Conexiones de punto de conexión privado**, busque el que está diagnosticando y confirme que el "Estado de conexión" esté **Aprobado** y que el "Estado de aprovisionamiento" esté establecido en **Correcto**.
    - Si la conexión está en estado "Pendiente", es posible que pueda aprobarla.
    - Si la conexión tiene el estado "Rechazada", "Error", "Desconectada" u otro similar, entonces quiere decir que no funciona y deberá crear un nuevo recurso de punto de conexión privado.

Es buena idea eliminar las conexiones ineficaces con el fin de mantener todo organizado.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Confirme que el firewall del almacén de claves esté correctamente configurado

>[!IMPORTANT]
> Si cambiar la configuración del firewall puede quitar el acceso de los clientes legítimos que todavía no usan vínculos privados. Asegúrese de que conoce las implicaciones de cada cambio en la configuración del firewall.

Una noción importante es que la característica de vínculos privados solo *da* acceso a su almacén de claves en una instancia de Virtual Network que esté cerca, a fin de evitar la filtración de datos. No *quite* ningún acceso existente. Para bloquear de forma eficaz el acceso desde la red pública de Internet, debe habilitar el firewall del almacén de claves explícitamente:

1. Abra Azure Portal y el recurso del almacén de claves.
2. En el menú de la izquierda, seleccione la opción **Redes**.
3. Asegúrese de que la pestaña **Firewalls y redes virtuales** está seleccionada en la parte superior.
4. Asegúrese de que la opción **Punto de conexión privado y redes seleccionadas** está seleccionada. Si la opción **Todas las redes** está seleccionada, esto explica por qué los clientes externos todavía pueden acceder al almacén de claves.

Las siguientes instrucciones también se aplican a la configuración del firewall:

- La característica de vínculos privados no requiere que se especifique ninguna "red virtual" en la configuración de firewall del almacén de claves. Todas las solicitudes que usen la dirección IP privada del almacén de claves (consulte la siguiente sección) deben funcionar, incluso si no se especifica ninguna red virtual en la configuración de firewall del almacén de claves.
- La característica de vínculos privados no requiere que se especifique ninguna dirección IP en la configuración de firewall del almacén de claves. Asimismo, todas las solicitudes que usen la dirección IP privada del almacén de claves deben funcionar, aunque no se haya especificado ninguna dirección IP en la configuración del firewall.

Si usa vínculos privados, los únicos motivos para especificar la red virtual o la dirección IP en el firewall del almacén de claves son los siguientes:

- Tiene un sistema híbrido en el que algunos clientes usan vínculos privados, otros usan puntos de conexión de servicio y otros usan una dirección IP pública.
- Está cambiando sus clientes a los vínculos privados. En este caso, una vez que confirme que todos los clientes usan vínculos privados, debe quitar las redes virtuales y las direcciones IP de la configuración de firewall del almacén de claves.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Asegúrese de que el almacén de claves tiene una dirección IP privada

### <a name="difference-between-private-and-public-ip-addresses"></a>Diferencia entre las direcciones IP privadas y públicas

Una dirección IP privada siempre tiene uno de los siguientes formatos:

- 10.x.x.x: Ejemplos: `10.1.2.3`, `10.56.34.12`.
- 172.16.x.x to 172.32.x.x: Ejemplos: `172.20.1.1`, `172.31.67.89`.
- 192.168.x.x: Ejemplos: `192.168.0.1`, `192.168.100.7`

Ciertos intervalos y direcciones IP están reservados:

- 224.x.x.x: Multidifusión
- 255.255.255.255: Difusión
- 127.x.x.x: Bucle invertido
- 169.254.x.x: Local de vínculo
- 168.63.129.16: DNS interno

Todas las demás direcciones IP son públicas.

Cuando examine el portal o ejecute un comando que muestre la dirección IP, asegúrese de saber si esa dirección IP es privada, pública o reservada. Para que los vínculos privados funcionen, el nombre de host del almacén de claves debe resolverse en una dirección IP privada que pertenezca al espacio de direcciones de Virtual Network.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Búsqueda de la dirección IP privada del almacén de claves en la red virtual

Tendrá que diagnosticar la resolución del nombre de host y, para ello, debe conocer la dirección IP privada exacta del almacén de claves con vínculos privados habilitados. Para encontrar esa dirección, siga este procedimiento:

1. Abra Azure Portal y el recurso del almacén de claves.
2. En el menú de la izquierda, seleccione la opción **Redes**.
3. Haga clic en la pestaña **Conexiones de punto de conexión privado**. Se mostrarán todas las conexiones del punto de conexión privado y sus estados respectivos.
4. Busque el que esté diagnosticando y confirme que el "Estado de conexión" está **Aprobado** y que el estado de aprovisionamiento es **Correcto**. Si no lo ve, vuelva a las secciones anteriores de este documento.
5. Cuando encuentre el elemento adecuado, haga clic en el vínculo de la columna **Punto de conexión privado**. Se abrirá el recurso del punto de conexión privado.
6. En la página de información general puede ver una sección denominada **Configuración DNS personalizada**. Confirme que solo exista una entrada que coincida con el nombre de host del almacén de claves. Esa entrada muestra la dirección IP privada del almacén de claves.
7. También puede hacer clic en el vínculo de **Interfaz de red** y confirmar que la dirección IP privada es la misma que se muestra en el paso anterior. La interfaz de red es un dispositivo virtual que representa el almacén de claves.

La dirección IP es aquella que las VM y otros dispositivos que se *ejecutan en la misma instancia de Virtual Network* usarán para conectarse al almacén de claves. Tome nota de la dirección IP o mantenga abierta la pestaña del explorador y no la cierre mientras busca más detalles.

>[!NOTE]
> Si el almacén de claves tiene varios puntos de conexión privados, entonces tendrá varias direcciones IP privadas. Esto solo es útil si tiene varias instancias de Virtual Network que acceden al mismo almacén de claves, cada una a través de su propio punto de conexión privado (cada punto de conexión privado pertenece a una sola instancia de Virtual Network). Asegúrese de diagnosticar el problema de la instancia de Virtual Network correcta y seleccione la conexión del punto de conexión privado correcto en el procedimiento anterior. Asimismo, **no** debe crear varios puntos de conexión privados para el mismo almacén de claves de la misma instancia de Virtual Network. Esto no es necesario y podría ser confuso.

## <a name="5-validate-the-dns-resolution"></a>5. Valide la resolución DNS

La resolución de DNS es el proceso de traducir el nombre de host del almacén de claves (por ejemplo: `fabrikam.vault.azure.net`) en una dirección IP (por ejemplo: `10.1.2.3`). En las siguientes subsecciones se muestran los resultados esperados de la resolución de DNS en cada escenario.

### <a name="key-vaults-without-private-link"></a>Almacenes de claves sin vínculo privado

Esta sección está pensada para fines de aprendizaje. Cuando el almacén de claves no tiene una conexión de punto de conexión privado en estado aprobado, al resolver el nombre de host se obtiene un resultado similar al siguiente:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Puede ver que el nombre se resuelve en una dirección IP pública y que no hay ningún alias `privatelink`. El alias se explica más adelante, no es necesario preocuparse por él ahora.

Se espera el resultado anterior, independientemente de que el equipo esté conectado a la instancia de Virtual Network o sea un equipo arbitrario con conexión a Internet. Esto sucede porque el almacén de claves no tiene una conexión de punto de conexión privado en estado aprobado y, por lo tanto, no es necesario que el almacén de claves admita vínculos privados.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Almacén de claves con vínculo privado que se resuelve desde un equipo de Internet arbitrario

Cuando el almacén de claves tiene una o más conexiones de punto de conexión privadas en estado aprobado y se resuelve el nombre de host desde una máquina arbitraria conectada a Internet (recuerde que debe ser una máquina que *no esté* conectada a la instancia de Virtual Network en la cual reside el punto de conexión privado), encontrará lo siguiente:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Una diferencia notable del ejemplo anterior es que hay un nuevo alias con el valor `{vaultname}.privatelink.vaultcore.azure.net`. Esto significa que el plano de datos del almacén de claves está listo para aceptar solicitudes de vínculos privados.

Asimismo, esto no quiere decir que las solicitudes realizadas desde máquinas que estén *fuera* de la instancia de Virtual Network (como la que acaba de usar) vayan a usar vínculos privados; no lo harán. Puede confirmar que esto es así, ya que el nombre de host se resuelve en una dirección IP pública. Solo los equipos *conectados a Virtual Network* pueden usar vínculos privados. A continuación encontrará más información sobre este tema.

Si no ve el alias `privatelink`, esto significa que el almacén de claves no tiene conexiones de punto de conexión privado con el estado `Approved`. Vuelva a [esta sección](#2-confirm-that-the-connection-is-approved-and-succeeded) antes de volver a intentarlo.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Almacén de claves con vínculo privado que se resuelve desde una instancia de Virtual Network

Cuando el almacén de claves tiene una o más conexiones de punto de conexión privado en estado "aprobado" y se resuelve el nombre de host desde una máquina conectada a la instancia de Virtual Network en la que se creó el punto de conexión privado, esta es la respuesta esperada:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Hay dos diferencias importantes. En primer lugar, el nombre se resuelve como una dirección IP privada. Debe ser la dirección IP que encontramos en la [sección correspondiente](#find-the-key-vault-private-ip-address-in-the-virtual-network) de este artículo. En segundo lugar, no hay ningún otro alias después del alias `privatelink`. Esto sucede porque los servidores DNS de Virtual Network *interceptan* la cadena de alias y devuelven la dirección IP privada directamente desde el nombre `fabrikam.privatelink.vaultcore.azure.net`. Esa entrada es realmente un registro de `A` en una zona DNS privada. A continuación encontrará más información sobre este tema.

>[!NOTE]
> El resultado anterior solo se produce en una máquina virtual conectada a Virtual Network, en la que se creó el punto de conexión privado. Si no tiene una VM implementada en Virtual Network que contenga el punto de conexión privado, implemente una y conéctese de forma remota a ella; a continuación, ejecute el comando `nslookup` (Windows) o el comando `host` (Linux) anterior.

Si ejecuta estos comandos en una máquina virtual conectada a Virtual Network en la que se creó el punto de conexión privado y **no** se muestra la dirección IP privada del almacén de claves, en la siguiente sección encontrará información para solucionar el problema.

## <a name="6-validate-the-private-dns-zone"></a>6. Valide la zona DNS privada

Si la resolución DNS no funciona tal como se describe en la sección anterior, es posible que haya un problema con la zona DNS privada; igualmente, podrá encontrar información útil en esta sección. Si la resolución de DNS muestra la dirección IP privada correcta del almacén de claves, es probable que la zona DNS privada sea correcta. Si es así, puede omitir esta sección.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirmación de que existe el recurso de zona DNS privada requerido

La suscripción de Azure debe tener una [zona DNS privada](../../dns/private-dns-privatednszone.md) con este nombre exacto:

`privatelink.vaultcore.azure.net`

Puede comprobar la presencia de este recurso yendo a la página de suscripción en el portal y seleccionando "Recursos" en el menú de la izquierda. El nombre del recurso debe ser `privatelink.vaultcore.azure.net`, y el tipo del mismo debe ser **zona DNS privada**.

Normalmente, este recurso se crea automáticamente cuando se crea un punto de conexión privado mediante un procedimiento común. Sin embargo, hay casos en los que este recurso no se crea automáticamente, por lo que tendría que hacerlo manualmente. Este recurso también podría haberse eliminado por accidente.

Si no tiene este recurso, cree un nuevo recurso de zona DNS privada en la suscripción. Recuerde que el nombre debe ser exactamente `privatelink.vaultcore.azure.net`, sin espacios ni puntos adicionales. Si especifica un nombre incorrecto, la resolución de nombres que se explica en este artículo no funcionará. Para obtener más información sobre cómo crear este recurso, consulte [Creación de una zona DNS privada de Azure mediante Azure Portal](../../dns/private-dns-getstarted-portal.md). Si sigue los pasos de esa página, puede omitir la creación de Virtual Network, ya que en este momento debe tener uno disponible. También puede omitir los procedimientos de validación con Virtual Machines.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Confirme que la zona DNS privada esté vinculada a Virtual Network.

Recuerde que no es suficiente tener una zona DNS privada sin más. También debe estar vinculada a la instancia de Virtual Network que contiene el punto de conexión privado. Si la zona DNS privada no está vinculada a la instancia de Virtual Network correcta, cualquier resolución DNS de esa instancia de Virtual Network omitirá la zona DNS privada.

Abra el recurso de la zona DNS privada y haga clic en la opción **Vínculos de red virtual** en el menú de la izquierda. Esta opción mostrará una lista de vínculos, cada uno con el nombre de una instancia de Virtual Network de la suscripción. La instancia de Virtual Network que contiene el recurso de punto de conexión privado debe aparecer aquí. Si no está, agréguela. Para obtener los pasos detallados, consulte [Vinculación de la de red virtual](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Puede dejar desactivada la opción "Habilitar registro automático", ya que esta característica no está relacionada con vínculos privados.

Una vez que la zona DNS privada esté vinculada a la instancia de Virtual Network, las solicitudes DNS procedentes de esa instancia de Virtual Network buscarán nombres en la zona DNS privada. Esto es necesario para la correcta resolución de direcciones que se realiza en Virtual Machines y que está conectada a la instancia Virtual Network donde se creó el punto de conexión privado.

>[!NOTE]
> Si acaba de guardar el vínculo, este puede tardar algún tiempo en aplicarse, incluso después de que el portal indique que la operación se ha completado. También podría ser necesario reiniciar la VM que está usando para probar la resolución de DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Confirmación de que la zona DNS privada contiene el registro A correcto

Mediante el portal, abra la zona DNS privada con el nombre `privatelink.vaultcore.azure.net`. En la página de información general verá todos los registros. De forma predeterminada, habrá un registro con el nombre `@` y el tipo `SOA`, que indica el inicio de la autoridad. No lo toque.

Para que funcione la resolución de nombres del almacén de claves, debe haber un registro `A` que tenga un nombre de almacén simple, sin sufijo ni puntos. Por ejemplo, si el nombre de host es `fabrikam.vault.azure.net`, debe haber un registro `A` con el nombre `fabrikam`, sin ningún sufijo ni punto.

Asimismo, el valor del registro `A` (la dirección IP) debe ser [la dirección IP privada del almacén de claves](#find-the-key-vault-private-ip-address-in-the-virtual-network). Si encuentra el registro `A`, pero contiene la dirección IP incorrecta, debe quitar la dirección IP equivocada y agregar una nueva. Así pues, se recomienda quitar todo el registro `A` y agregar uno nuevo.

>[!NOTE]
> Cada vez que se quita o se modifica un registro `A`, es posible que la máquina siga resolviendo la dirección IP anterior porque el valor de TTL (período de vida) no ha expirado todavía. Por ello, se recomienda especificar siempre un valor de TTL inferior a 10 segundos y no superior a 600 segundos (10 minutos). Si especifica un valor demasiado grande, los clientes pueden tardar demasiado tiempo en recuperarse de interrupciones.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Resolución DNS para más de una instancia de Virtual Network

Si existen varias instancias de Virtual Network y cada una tiene su propio recurso de punto de conexión privado que hace referencia al mismo almacén de claves, el nombre de host del almacén de claves debe resolverse en una dirección IP privada diferente, en función de la red. Esto significa que también se necesitan varias zonas DNS privadas, cada una vinculada a una instancia de Virtual Network diferente y que use una dirección IP diferente en el registro `A`.

En escenarios más avanzados, las instancias de Virtual Network pueden tener el emparejamiento habilitado. En este caso, solo una instancia de Virtual Network necesita tener el recurso de punto de conexión privado, aunque es posible que sea necesario vincular ambos al recurso de la zona DNS privada. Este ejemplo no se incluye directamente en este documento.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Garantía de que tiene control sobre la resolución de DNS

Tal como se explica en la [sección anterior](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine), un almacén de claves con vínculos privados tiene el alias `{vaultname}.privatelink.vaultcore.azure.net` en su registro *público*. El servidor DNS que usa la instancia de Virtual Network usa el registro público, pero comprueba todos los alias de un registro *privado* y, si encuentra alguno, detendrá los siguientes alias definidos en el registro público.

Esta lógica significa que, si la instancia de Virtual Network está vinculada a una zona DNS privada denominada `privatelink.vaultcore.azure.net` y el registro DNS público del almacén de claves tiene el alias `fabrikam.privatelink.vaultcore.azure.net` (tenga en cuenta que el sufijo del nombre de host del almacén de claves coincide exactamente con el nombre de la zona DNS privada), la consulta de DNS buscará un registro `A` con el nombre `fabrikam` *en la zona DNS privada*. Si se encuentra el registro `A`, su dirección IP se devuelve en la consulta de DNS y no se realiza ninguna búsqueda adicional en el registro de DNS público.

Como puede ver, la resolución de nombres está bajo su control. Las razones de este diseño son las siguientes:

- Puede tener un escenario complejo que implique los servidores DNS personalizados y la integración con redes locales. En ese caso, debe controlar la forma de traducir los nombres en las direcciones IP.
- Es posible que necesite obtener acceso a un almacén de claves sin vínculos privados. Si ese fuera el caso, la resolución del nombre de host de la instancia de Virtual Network debe devolver una dirección IP pública; esto sucederá porque los almacenes de claves sin vínculos privados no tienen el alias `privatelink` en el registro de nombres.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Valide que las solicitudes al almacén de claves usan el vínculo privado

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Consultar el punto de conexión `/healthstatus` del almacén de claves

El almacén de claves proporciona el punto de conexión `/healthstatus`, que se puede usar para realizar el diagnóstico. Los encabezados de respuesta incluyen la dirección IP de origen, tal como la detecta el servicio del almacén de claves. Puede llamar a ese punto de conexión con el siguiente comando (**recuerde usar el nombre de host del almacén de claves**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux, o una versión reciente de Windows 10 que incluye `curl`:

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Si no obtiene un resultado similar al siguiente o si recibe un error de red, esto significa que no se puede acceder al almacén de claves a través del nombre de host que especificó (`fabrikam.vault.azure.net` en el ejemplo). O bien el nombre de host no se está resolviendo en la dirección IP correcta o bien tiene un problema de conectividad en el nivel de transporte. Igualmente, el error puede deberse a problemas de enrutamiento, caídas de paquetes u otras razones. Tendrá que investigar más para saber qué ha pasado.

La respuesta debe incluir el encabezado `x-ms-keyvault-network-info`:

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

En el campo `addr` del encabezado de `x-ms-keyvault-network-info` se muestra la dirección IP del origen de la solicitud. Esta dirección IP puede ser una de las siguientes:

- La dirección IP privada de la máquina que realiza la solicitud. Esto indica que la solicitud utiliza vínculos privados o puntos de conexión de servicio. Este es el resultado esperado de los vínculos privados.
- Otra dirección IP privada, que *no* sea del equipo que realiza la solicitud. Esto indica que algún enrutamiento personalizado está funcionando. Asimismo, sigue indicando que la solicitud usa vínculos privados o puntos de conexión de servicio.
- Una dirección IP pública. Esto indica que la solicitud se está enrutando a Internet a través de un dispositivo de puerta de enlace (NAT). Esto indica que la solicitud NO usa vínculos privados y que es necesario solucionar algún problema. Los motivos comunes para esto son: 1) el punto de conexión privado no está en estado "aprobado" y "correcto"; y 2) el nombre de host no se resuelve en la dirección IP privada del almacén de claves. En este artículo se incluyen acciones para solucionar ambos casos.

>[!NOTE]
> Si la solicitud de `/healthstatus` funciona, pero falta el encabezado `x-ms-keyvault-network-info`, es probable que el almacén de claves no administre el punto de conexión. Dado que los comandos anteriores también validan el certificado HTTPS, el encabezado que falta podría ser un signo de alteración.

### <a name="query-the-key-vault-ip-address-directly"></a>Consultar la dirección IP del almacén de claves directamente

>[!IMPORTANT]
> Acceder al almacén de claves sin validar los certificados HTTPS es peligroso y solo se puede realizar con fines de aprendizaje. El código de producción NUNCA debe obtener acceso al almacén de claves sin esta validación del lado cliente. Incluso si solo está diagnosticando problemas, es posible que sufra intentos de alteración que no se mostrarán si con frecuencia tiene deshabilitada la validación de certificados HTTPS en las solicitudes que se realizan al almacén de claves.

Si ha instalado una versión reciente de PowerShell, puede usar `-SkipCertificateCheck` para omitir las comprobaciones de certificados HTTPS y, a continuación, puede establecer como destino la [dirección IP del almacén de claves](#find-the-key-vault-private-ip-address-in-the-virtual-network) directamente:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Si está utilizando `curl`, puede hacer lo mismo con el argumento `-k`:

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

Las respuestas deben ser las mismas que en la sección anterior, lo que significa que debe incluir el encabezado `x-ms-keyvault-network-info` con el mismo valor. El punto de conexión `/healthstatus` no le será útil si usa el nombre de host o la dirección IP del almacén de claves.

Si ve que `x-ms-keyvault-network-info` devuelve un valor para la solicitud que usa el nombre de host del almacén de claves y otro valor para aquella que usa la dirección IP, cada solicitud estará dirigida a un punto de conexión diferente. Consulte la explicación del campo `addr` de `x-ms-keyvault-network-info` en la sección anterior, para decidir qué caso es incorrecto y debe corregirse.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. Otros cambios y personalizaciones que afectan al proceso

Los elementos siguientes son acciones de investigación no exhaustivas. Le indicarán dónde buscar otros problemas, pero debe tener conocimientos de red avanzados para solucionar problemas en estos escenarios.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnóstico de servidores DNS personalizados en Virtual Network

Abra el recurso de Virtual Network en el portal. En el menú de la izquierda, abra la opción **Servidores DNS**. Si está usando el modo "personalizado", es posible que la resolución de DNS no sea como se describe en este documento. Tiene que diagnosticar el modo en que los servidores DNS resuelven el nombre de host del almacén de claves.

Si está usando los servidores DNS predeterminados que proporciona Azure, puede aplicar todo lo que se indica en este documento.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnóstico de invalidación de hosts o servidores DNS personalizados en la máquina virtual

Muchos sistemas operativos permiten establecer una dirección IP fija explícita en función del nombre de host; normalmente, solo es necesario cambiar el archivo `hosts`. También pueden permitir la invalidación de los servidores DNS. Si usa uno de estos escenarios, puede continuar con las opciones de diagnóstico específicas del sistema.

### <a name="promiscuous-proxies-fiddler-etc"></a>Proxies promiscuos (Fiddler, etc.)

Salvo cuando se indique explícitamente, las opciones de diagnóstico de este artículo solo funcionan si no hay ningún proxy promiscuo presente en el entorno. Aunque estos proxies suelen instalarse exclusivamente en el equipo que se está diagnosticando (Fiddler es el ejemplo más común), los administradores avanzados pueden sobrescribir las Entidades de certificación raíz (CA) e instalar un proxy promiscuo en los dispositivos de puerta de enlace que atienden a varios equipos de la red. Estos proxies pueden afectar considerablemente a la seguridad y la confiabilidad. Microsoft no admite las configuraciones que usan estos productos.

### <a name="other-things-that-may-affect-connectivity"></a>Otras cosas que pueden afectar a la conectividad

Esta es una lista no exhaustiva de elementos que se pueden encontrar en escenarios avanzados o complejos:

- Configuración del firewall, ya sea Azure Firewall conectado a Virtual Network o una solución de firewall personalizada que se implementa en Virtual Network o en el equipo.
- Emparejamiento de redes, que puede afectar a los servidores DNS que se usan y a cómo se enruta el tráfico.
- Soluciones de puerta de enlace personalizada (NAT), que pueden afectar a la forma en que se enruta el tráfico, incluido el tráfico de las consultas de DNS.
