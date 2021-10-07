---
title: Protocolos compatibles con Azure Defender para IoT
description: Obtenga información sobre los protocolos que admite Azure Defender para IoT.
ms.date: 09/20/2021
ms.topic: article
ms.openlocfilehash: 9320b43cf645e62d68baac39cdf4f06dd1825305
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644436"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>Compatibilidad con los protocolos IoT, OT, ICS y SCADA

Azure Defender para IoT proporciona una plataforma de ciberseguridad abierta e interoperable de tecnología de operaciones (OT). Defender para IoT se implementa en muchas ubicaciones diferentes y reduce el riesgo de IoT, TI e ICS con implementaciones en entornos de OT exigentes y complejos en todas las zonas verticales y geográficas del sector.

## <a name="supported-protocols"></a>Protocolos admitidos

Azure Defender para IoT admite una amplia gama de protocolos en una empresa diversa e incluye equipos de automatización industrial en todos los sectores industriales, redes empresariales y entornos del sistema de administración de edificios (BMS). En el caso de los protocolos personalizados o propietarios, Microsoft ofrece un SDK que facilita el desarrollo, la prueba y la implementación de disectores de protocolo personalizados como complementos. El SDK hace todo esto sin divulgar información de su propiedad; por ejemplo, cómo se diseñan los protocolos o evitando compartir PCAP que pueden contener información confidencial. Los protocolos admitidos se indican a continuación.

### <a name="supported-protocols-passive-monitoring"></a>Protocolos admitidos (supervisión pasiva)

En esta sección se enumeran los protocolos que se detectan mediante la supervisión pasiva.

**ABB:** IEC61850 MMS (extensión ABB incluida)

**ASHRAE:** BACnet, BACnet BACapp, BACnet BVLC

**Beckhoff:** AMS (ADS), Twincat 

**Cisco:** CAPWAP Control, CAPWAP Data, CDP, LWAPP

**DNP. org :**  DNP3

**Emerson:** DeltaV, Emerson OpenBSI/BSAP, Ovation DCS ADMD,Ovation DCS DPUSTAT, Ovation DCS SSRPC

**Emerson Fischer:**  ROC

**Eurocontrol:** ASTERIX

**GE:**  Bentley Nevada (System 1),  EGD,  GSM (GE MarkVI yMarkVIe), SRTP (GE)

**Honeywell:** ENAP, Experion DCS CDA, Experion DCS FDA

**IEC:** Codesys V3, ICCP TASE.2/IEC-60870, IEC60870-5 (IEC104/101), IEC60870-5-103 (número de serie encapsulado), IEC61850 GOOSE, IEC61850 MMS, IEC61850 SMV (SAMPLED-VALUES), LonTalk (LonWorks)

**IEEE**: LLC, STP, VLAN

**IETF:** ARP, DCE RPC, DNS, FTP (FTP_ADAT, FTP_DATA), GSSAPI (RFC2743), HTTP, ICMP, IPv4, IPv6, LLDP, MDNS, NBNS, NTLM (NTLMSSP Auth Protocol), RPC, SMB / Browse / NBDGM, SMB / CIFS, SNMP, SPNEGO (RFC4178), SSH, Syslog, Telnet, TFTP, TPKT, UDP

**ISO:** CLNP (ISO 8473), COTP (ISO 8073), Protocolo del sector ISO, MQTT (IEC 20922)

**Medical:** ASTM, HL7

**Microsoft:** disectores de la comunidad de Horizon, disectores de su propiedad en Horizon (desarrollados por clientes). Para obtener más detalles, consulte [Disector de protocolo de su propiedad en Horizon](references-horizon-sdk.md).

**Mitsubishi:** Melsoft / Melsec (Mitsubishi Electric)

**Omron:** FINS

**Oracle:** TDS, TNS

**Rockwell Automation:** ENIP, EtherNet/IP CIP (extensión Rockwell incluida), EtherNet/IP CIP FW, versión 27 y superior

**Schneider Electric:** extensiones unitarias Modbus/TCP, Modbus TCP–Schneider, OASYS (Schneider Electric Telvant) 

**Schneider Electric / Invensys:** Foxboro Evo, Foxboro I/A, Trident, TriGP, TriStation

**Schneider Electric / Modicon:** Modbus RTU

**Schneider Electric / Wonderware:** Wonderware Suitelink

**Siemens:** CAMP, PCS7, PCS7 WinCC – Historian, Profinet DCP, Profinet Realtime, Siemens PHD, Siemens S7, Siemens S7-Plus, Siemens S7-Plus, Siemens SICAM, Siemens WinCC

**Toshiba:** Toshiba Computer Link

**Yokogawa:** Centum ODEQ (Centum / ProSafe DCS), HIS Equalize, Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>Protocolos admitidos (supervisión pasiva)

En esta sección se enumeran los protocolos que se detectan mediante sondeos activos como, por ejemplo, barridos de ping y consultas.

**IETF:** barrido de ping, consulta de diseño de red de SNMP, consulta de SNMP

**Microsoft:** consulta de Windows WMI (req. WMI/WinRM): hardware, BIOS, versión, software, revisiones

**Rockwell Automation:** consulta de ENIP, examen de ENIP, EtherNet/IP CIP (consulta de CIP)

**Siemens:** Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>Incorporación rápida de compatibilidad con protocolos de su propiedad y restringidos

La digitalización está impulsando la implementación de miles de millones de dispositivos de IoT, además de obtener un aumento significativo de la conectividad entre las redes de IT y OT. Esto significa que las superficies de ataque están creciendo y que existe un mayor riesgo de ciberataques peligrosos en sistemas de control industrial.

Para reducir la superficie de ataque, el SDK del protocolo Horizon permite realizar una compatibilidad rápida y segura de cualquier protocolo que se use en entornos de IoT e ICS.

Horizon ofrece las siguientes soluciones para clientes y asociados tecnológicos:

- Compatibilidad total e ilimitada con los protocolos propietarios y personalizados comunes, y con aquellos protocolos que se desvían de cualquier estándar.

- Un nuevo nivel de flexibilidad y ámbito para el desarrollo de DPI.

- Una herramienta que amplía exponencialmente la visibilidad y el control de OT sin necesidad de actualizar las versiones de la plataforma de Defender para IoT.

- La seguridad de permitir el desarrollo propietario sin divulgar información confidencial.

El SDK de Horizon le permite escribir complementos que permiten realizar una inspección profunda de paquetes (PPP) en el tráfico y detectar amenazas en tiempo real. El SDK de Horizon también permite crear personalizaciones adicionales. Por ejemplo, el SDK de Horizon permite que los proveedores de recursos, asociados o propietarios de la plataforma localicen y personalicen el texto de alertas, eventos y parámetros de protocolo.

[![El SDK de Horizon ofrece una compatibilidad rápida para el 100 % de los protocolos que se usan en entornos de IoT e ICS.](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>Colaboración con la comunidad de Horizon

Forme parte de una comunidad que lidera el camino hacia la transformación digital y la colaboración en todo el sector para la compatibilidad con protocolos. La comunidad de Horizon ICS permite el uso compartido de conocimiento para aquellos expertos en dominios de infraestructuras críticas, administración de edificios, líneas de producción, sistemas de transporte y otros líderes del sector.

Asimismo, esta comunidad proporciona tutoriales, foros de discusión, cursos dirigidos por instructores, documentos técnicos educativos, seminarios web, etc.

Únase a nuestra comunidad aquí: <horizon-community@microsoft.com>

## <a name="next-steps"></a>Pasos siguientes

Obtenga más detalles en [Disector de protocolo de su propiedad en Horizon](references-horizon-sdk.md).
Eche un vistazo a nuestra [API de Horizon](references-horizon-api.md).
