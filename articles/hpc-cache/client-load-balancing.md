---
title: Equilibrio de carga de las conexiones de cliente a Azure HPC Cache
description: Configuración de un servidor DNS para el equilibrio de carga round robin para Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: v-erkel
ms.openlocfilehash: 0aa704b44a7a61472b3d10c3b7cc94f5e95dd9ff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699460"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>Equilibrio de carga del tráfico de cliente de HPC Cache

En este artículo se explican algunos métodos básicos para equilibrar el tráfico de cliente a todos los puntos de montaje de Azure HPC Cache.

Cada instancia de HPC Cache tiene al menos tres direcciones IP diferentes y las memorias caché con valores de rendimiento mayores pueden tener hasta doce. Es importante usar todas las direcciones IP para obtener todas las ventajas de Azure HPC Cache.

Hay varias opciones para equilibrar la carga de los montajes de cliente:

* Elegir manualmente una dirección IP de montaje diferente para cada cliente
* Incluir rotación de direcciones IP en los scripts de montaje de cliente
* Configurar un sistema DNS para enrutar automáticamente las solicitudes de cliente entre todas las direcciones disponibles (DNS round robin)

El sistema de equilibrio de carga adecuado para cada usuario depende de la complejidad del flujo de trabajo, el número de direcciones IP de la caché y muchos otros factores. Hable con su asesor de Azure si necesita ayuda para decidir qué método es el mejor en su caso.

## <a name="assign-ip-addresses-manually"></a>Asignación manual de direcciones IP

Las direcciones IP de montaje de la caché se muestran en las páginas **Información general** e **Instrucciones de montaje** de Azure Portal y en el mensaje de operación correcta que se imprime al crear una caché con la CLI de Azure o PowerShell.

Puede usar la página **Instrucciones de montaje** para generar un comando de montaje personalizado para cada cliente. Seleccione todos los valores de **dirección de montaje de caché** cuando cree varios comandos.

Lea [Montaje de la instancia de Azure HPC Cache](hpc-cache-mount.md) para obtener detalles.

## <a name="use-scripted-load-balancing"></a>Uso de equilibrio de carga con scripts

Hay varias maneras de rotar montajes de cliente mediante programación entre las direcciones IP disponibles.

Este comando de montaje de ejemplo usa la función hash ``cksum`` y el nombre de host de cliente para distribuir automáticamente las conexiones de cliente entre todas las direcciones IP disponibles en HPC Cache. Si todas las máquinas cliente tienen nombres de host únicos, puede ejecutar este comando en cada cliente para asegurarse de que se usen todos los puntos de montaje disponibles.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

Para usar este ejemplo en el flujo de trabajo, personalice estos términos:

* En la expresión ```X=```, use una lista separada por espacios de todas las direcciones de montaje de la caché, ordenada.

  La expresión ``(X=(10.0.0.{7..9})`` establece la variable X como este conjunto de direcciones de montaje: {10.0.0.7, 10.0.0.8, 10.0.0.9}. Use la dirección IP base de la caché y las direcciones exactas que se muestran en la página de información general de la caché. Si las direcciones no son consecutivas, indíquelas en orden numérico.

* En el término ```%3```, use el número real de direcciones IP de montaje que tiene la caché (normalmente 3, 6, 9 o 12).

  Por ejemplo, use ``%9`` si la caché expone nueve direcciones IP de montaje de cliente.

* En la expresión ``${NAMESPACE}``, use la ruta de acceso del espacio de nombres de destino de almacenamiento al que va a acceder el cliente.

  Puede usar una variable que haya definido (*NAMESPACE* en el ejemplo) o pasar el valor literal en su lugar.
  
  En el ejemplo de comando del final de esta sección se usa un valor literal para la ruta de acceso del espacio de nombres, ``/blob-target-1``.

* Si quiere usar una ruta de acceso local personalizada en los equipos cliente, cambie el valor ``/mnt`` por la ruta de acceso que quiera.

Este es un ejemplo de un comando de montaje de cliente relleno:

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

## <a name="use-dns-load-balancing"></a>Uso de equilibrio de carga de DNS

En esta sección se explican los aspectos básicos de la configuración de un sistema DNS para distribuir las conexiones de cliente a todos los puntos de montaje en Azure HPC Cache. Este método no tiene en cuenta la cantidad de tráfico que genera cada cliente, pero se asegura de que los clientes se repartan uniformemente entre todas las interfaces de la caché en lugar de usar solo una o dos.

Este documento no incluye instrucciones para configurar y administrar un servidor DNS para los clientes en el entorno de Azure.

DNS no es necesario para montar clientes mediante el protocolo NFS y direcciones IP. DNS *es* necesario si quiere usar nombres de dominio en lugar de direcciones IP para acceder a sistemas NAS de hardware, o si el flujo de trabajo incluye cierta configuración de protocolo avanzada.

El sistema DNS que usa para distribuir direcciones a los clientes no necesita que HPC Cache acceda a él. En algunas situaciones, es posible que quiera usar un sistema DNS personalizado para la propia caché, pero configurar ese sistema es mucho más complicado que configurar este tipo de sistema round robin de cliente. Debe hablar con Soporte técnico de Azure si está pensando en [cambiar el servidor DNS de *HPC Cache*](configuration.md#set-a-custom-dns-configuration) por un sistema personalizado.

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>Configuración de distribución round robin para puntos de montaje de caché

Un sistema DNS round robin (RRDNS) enruta automáticamente las solicitudes de cliente entre varias direcciones.

Para configurar este sistema, debe personalizar el archivo de configuración del servidor DNS para que, cuando reciba solicitudes de montaje en la dirección de dominio principal de HPC Cache, asigne el tráfico entre todos los puntos de montaje del sistema HPC Cache. Los clientes montan HPC Cache con su nombre de dominio como argumento de servidor, y se enrutan a la siguiente dirección IP de montaje automáticamente.

Hay dos pasos principales para configurar RRDNS:

1. Modifique el archivo ``named.conf`` del servidor DNS para establecer un orden cíclico para las consultas a HPC Cache. Esta opción hace que el servidor pase por todos los valores IP disponibles. Agregue una instrucción como la siguiente:

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. Configure registros A y registros de puntero (PTR) para cada dirección IP disponible como en el ejemplo siguiente.

   Estos comandos ``nsupdate`` proporcionan un ejemplo de configuración correcta de DNS para HPC Cache con el nombre de dominio hpccache.contoso.com y tres direcciones de montaje (10.0.0.10, 10.0.0.11 y 10.0.0.12):

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Estos comandos crean un registro A para cada una de las direcciones de montaje de HPC Cache y, además, configuran registros de puntero para admitir adecuadamente comprobaciones inversas de DNS.

   En el diagrama siguiente se muestra la estructura básica de esta configuración.

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="Diagrama que muestra la configuración de DNS del punto de montaje de cliente.":::
   <El diagrama muestra conexiones entre tres categorías de elementos: el nombre de dominio único de HPC Cache (a la izquierda), tres direcciones IP (columna central) y tres interfaces de cliente DNS de uso interno (columna derecha). Un único óvalo a la izquierda con la etiqueta "hpccache.contoso.com" está conectado mediante flechas que apuntan a tres óvalos etiquetados con las direcciones IP: 10.0.0.10, 10.0.0.11 y 10.0.0.12. Las flechas del óvalo hpccache.contoso.com a los tres óvalos de IP están etiquetadas como "A". Cada uno de los óvalos de dirección IP está conectado mediante dos flechas a un óvalo etiquetado como una interfaz de cliente: el óvalo con la IP 10.0.0.10 está conectado a "client-IP-10.contoso.com", el óvalo con la IP 10.0.0.11 está conectado a "client-IP-11.contoso.com" y el óvalo con la IP 10.0.0.12 está conectado a "client-IP-11.contoso.com". Las conexiones entre los óvalos de dirección IP y los de interfaz de cliente son dos flechas: una flecha con la etiqueta "PTR" que apunta desde el óvalo de dirección IP al de interfaz de cliente, y una flecha con la etiqueta "A" que apunta desde el óvalo de interfaz de cliente al de dirección IP.> :::image-end:::

Una vez configurado el sistema RRDNS, indique a los equipos cliente que lo usen para resolver la dirección de HPC Cache en sus comandos de montaje.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener ayuda para equilibrar la carga del cliente, [póngase en contacto con soporte técnico](hpc-cache-support-ticket.md).
* Para mover datos a los destinos de almacenamiento de la caché, lea [Rellenado del nuevo almacenamiento de blobs de Azure](hpc-cache-ingest.md).
