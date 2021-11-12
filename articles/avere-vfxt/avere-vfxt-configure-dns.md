---
title: 'DNS de Avere vFXT: Azure'
description: Configuración de un servidor DNS para el equilibrio de carga round robin con Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: 26f85b4d3a2cc49e55d228fbd73ae7cb3b66bf5d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337877"
---
# <a name="avere-cluster-dns-configuration"></a>Configuración de DNS del clúster de Avere

En esta sección se explican los conceptos básicos de la configuración de un sistema DNS para el equilibrio de carga de su clúster de Avere vFXT.

Este documento *no incluye* instrucciones para configurar y administrar un servidor DNS en el entorno de Azure.

En lugar de usar DNS round robin para el equilibrio de carga de un clúster de vFXT en Azure, considere la posibilidad de usar métodos manuales para asignar direcciones IP de manera uniforme entre los clientes cuando se monten. Se describen varios métodos en el tema sobre el [montaje del clúster de Avere](avere-vfxt-mount-clients.md).

Tenga esto en mente la hora de decidir si quiere usar un servidor DNS:

* Si solo acceden a su sistema los clientes de NFS, no es necesario utilizar DNS (es posible especificar todas las direcciones de red mediante direcciones IP numéricas).

* Si el sistema admite el acceso de SMB (CIFS), DNS es necesario, ya que debe especificar un dominio DNS para el servidor de Active Directory.

* DNS es necesario si quiere utilizar la autenticación Kerberos.

## <a name="load-balancing"></a>Equilibrio de carga

Para distribuir la carga global, configure el dominio de DNS para usar la distribución de carga round robin para las direcciones IP orientadas al cliente.

## <a name="configuration-details"></a>Detalles de configuración

Cuando los clientes acceden al clúster, el sistema DNS round robin (RRDNS) equilibra automáticamente sus solicitudes entre todas las interfaces disponibles.

Para configurar este sistema, debe personalizar el archivo de configuración del servidor DNS para que, cuando reciba solicitudes de montaje en la dirección del dominio principal del clúster de vFXT, asigne el tráfico entre todos los puntos de montaje del clúster de vFXT. Los clientes montan el clúster de vFXT con su nombre de dominio como argumento de servidor, y se enrutan a la siguiente dirección IP de montaje automáticamente.

Hay dos pasos principales para configurar RRDNS:

1. Modifique el archivo ``named.conf`` del servidor DNS para establecer un orden cíclico para las consultas al clúster de vFXT. Esta opción hace que el servidor pase por todos los valores IP disponibles. Agregue una instrucción como la siguiente:

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. Configure registros A y registros de puntero (PTR) para cada dirección IP disponible como en el ejemplo siguiente.

   Estos comandos ``nsupdate`` proporcionan un ejemplo de configuración correcta de DNS para un clúster de vFXT con el nombre de dominio vfxt.contoso.com y tres direcciones de montaje (10.0.0.10, 10.0.0.11 y 10.0.0.12):

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Estos comandos crean un registro A para cada una de las direcciones de montaje del clúster y, además, configuran registros de puntero para admitir adecuadamente comprobaciones inversas de DNS.

   En el diagrama siguiente se muestra la estructura básica de esta configuración.

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="Diagrama que muestra la configuración de DNS del punto de montaje de cliente.":::
   <El diagrama muestra conexiones entre tres categorías de elementos: el nombre de dominio único del clúster de vFXT (a la izquierda), tres direcciones IP (columna central) y tres interfaces de cliente DNS inverso de uso interno (columna derecha). Un único óvalo a la izquierda con la etiqueta "vfxt.contoso.com" está conectado mediante flechas que apuntan a tres óvalos etiquetados con las direcciones IP: 10.0.0.10, 10.0.0.11 y 10.0.0.12. Las flechas del óvalo vfxt.contoso.com a los tres óvalos de IP están etiquetadas como "A". Cada uno de los óvalos de dirección IP está conectado mediante dos flechas a un óvalo etiquetado como una interfaz de cliente: el óvalo con la IP 10.0.0.10 está conectado a "client-IP-10.contoso.com", el óvalo con la IP 10.0.0.11 está conectado a "client-IP-11.contoso.com" y el óvalo con la IP 10.0.0.12 está conectado a "client-IP-11.contoso.com". Las conexiones entre los óvalos de dirección IP y los de interfaz de cliente son dos flechas: una flecha con la etiqueta "PTR" que apunta desde el óvalo de dirección IP al de interfaz de cliente, y una flecha con la etiqueta "A" que apunta desde el óvalo de interfaz de cliente al de dirección IP.> :::image-end:::

Una vez configurado el sistema RRDNS, indique a los equipos cliente que lo usen para resolver la dirección del clúster en sus comandos de montaje.

## <a name="cluster-dns-settings"></a>Configuración de DNS del clúster

Especifique el servidor DNS que usa el clúster de vFXT en la página de configuración **Clúster** > **Red administrativa**. La configuración de esta página incluye:

* Dirección del servidor DNS
* Nombre de dominio DNS
* Dominios de búsqueda DNS

Lea [DNS Settings](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns) (Configuración de DNS) en la guía de configuración del clúster de Avere para obtener más detalles sobre el uso de esta página.
