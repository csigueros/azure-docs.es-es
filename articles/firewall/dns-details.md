---
title: Detalles del proxy DNS de Azure Firewall
description: Aprenda cómo funciona el proxy DNS de Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: victorh
ms.openlocfilehash: f6f17823b51200c21ee0975f50487ba1e3f58ced
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547981"
---
# <a name="azure-firewall-dns-proxy-details"></a>Detalles del proxy DNS de Azure Firewall

Puede configurar Azure Firewall para que actúe como proxy DNS. Un proxy DNS es un intermediario para las solicitudes DNS de las máquinas virtuales cliente a un servidor DNS.

A continuación, se describen algunos detalles de implementación del proxy DNS de Azure Firewall.

## <a name="fqdns-with-multiple-a-records"></a>Nombres de dominio completos con varios registros D

Azure Firewall funciona como cliente DNS estándar. Si hay varios registros D en la respuesta, el firewall almacena todos los registros en la caché. Si hay un registro por respuesta, el firewall almacena solo un registro. No hay manera de que un cliente sepa con antelación si debe esperar uno o varios registros D en las respuestas.

## <a name="fqdn-time-to-live-ttl"></a>Período de vida del FQDN (TTL)

Cuando el TTL (período de vida) de un nombre de dominio completo está a punto de expirar, los registros se almacenan en caché y expiran según sus valores de TTL. No se usa la captura previa, por lo que el firewall no realiza una búsqueda antes de la expiración del TTL para actualizar el registro.

## <a name="clients-not-configured-to-use-the-firewall-dns-proxy"></a>Clientes no configurados para usar el proxy DNS del firewall

Si un equipo cliente está configurado para usar un servidor DNS que no es el proxy DNS del firewall, los resultados pueden ser impredecibles.

Por ejemplo, suponga que una carga de trabajo cliente está en la región Este de EE. UU. y usa un servidor DNS principal hospedado en esta región. El servidor DNS de Azure Firewall está configurado para un servidor DNS secundario hospedado en la región Oeste de EE. UU. El servidor DNS del firewall hospedado en la región Oeste de EE. UU. da como resultado una respuesta diferente a la del cliente de la región Este de EE. UU.

Este es un escenario común y el motivo de que los clientes deban usar la funcionalidad de proxy DNS del firewall. Los clientes deben usar el firewall como solucionador si se utilizan nombres de dominio completos en reglas de red. Puede garantizar la coherencia de la resolución de direcciones IP por parte de los clientes y el propio firewall.

En este ejemplo, si se configura un FQDN en las reglas de red, el firewall resuelve el FQDN en IP1 (dirección IP 1) y actualiza las reglas de red para permitir el acceso a IP1. Si, y cuando, el cliente resuelve el mismo FQDN en IP2 debido a una diferencia en la respuesta DNS, su intento de conexión no coincidirá con las reglas del firewall y se denegará. 

En el caso de los FQDN de HTTP/S de las reglas de aplicación, el firewall analiza el FQDN del host o del encabezado SNI, lo resuelve y, luego, se conecta a esa dirección IP. Se omite la dirección IP de destino a la que el cliente estaba intentando conectarse.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración DNS de Azure Firewall](dns-settings.md)