---
title: Identificación de los dispositivos necesarios
description: Obtenga información sobre las aplicaciones virtuales y dispositivos de hardware para los sensores certificador de Defender para IoT y la consola de administración local.
ms.date: 06/21/2021
ms.topic: how-to
ms.openlocfilehash: b22f1dc81aee7ee20f4490d716b1e2af953b4553
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020322"
---
# <a name="identify-required-appliances"></a>Identificación de los dispositivos necesarios

En este artículo se proporciona información sobre los dispositivos de sensor certificados de Defender para IoT. Defender para IoT se puede implementar en aplicaciones virtuales y dispositivos físicos.

Esto incluye dispositivos certificados *preconfigurados*, en los que el software ya está instalado, así como dispositivos certificados no configurados en los que puede descargar e instalar el software necesario.

Este artículo también proporciona las especificaciones para un dispositivo de consola de administración local. La consola de administración local no está disponible como dispositivo preconfigurado.

- Si quiere adquirir un sensor preconfigurado, revise los modelos disponibles en la sección [Dispositivos de sensor](#sensor-appliances) y, después, realice la compra.

- Si quiere comprar su propio dispositivo, revise los modelos disponibles en la sección [Dispositivos de sensor](#sensor-appliances) y en la sección [Dispositivos certificados adicionales](#additional-certified-appliances). Después de adquirir el dispositivo, podrá descargar e instalar el software.

- Si quiere comprar la consola de administración local, revise la información de la sección [Dispositivo de la consola de administración local](#on-premises-management-console-appliance). Después de adquirir el dispositivo, podrá descargar e instalar el software.

Después de completar estas tareas, puede instalar el software y configurar la red.

## <a name="sensor-appliances"></a>Dispositivos de sensor

Defender para IoT admite implementaciones físicas y virtuales.

### <a name="physical-sensors"></a>Sensores físicos

En esta sección se proporciona información general sobre los modelos de sensores físicos que están disponibles. Puede adquirir sensores con software preconfigurado, o bien puede adquirir sensores que no estén preconfigurados.

| Tipo de implementación | Corporativos | Enterprise | Montaje en bastidor SMB| SMB resistente |
|--|--|--|--|--|
| Imagen | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Modelo de nivel corporativo."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modelo de nivel Enterprise."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modelo de nivel SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="El modelo de nivel de SMB resistente."::: |
| Modelo | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Puertos de supervisión | Hasta 15 RJ45 u 8 OPT | Hasta 8 RJ45 o 6 OPT | Hasta 4 RJ45 | Hasta 5 RJ45 |
| Ancho de banda máximo [1](#anchortext) | 3 Gb/s | 1 Gb/s | 200 Mb/s | 100 Mb/s |
| Número máximo de dispositivos protegidos | 30,000 | 15,000 | 1,000 | 800 |

Consulte [Especificaciones del dispositivo](#appliance-specifications) para más detalles del proveedor.

Acerca de los sensores preconfigurados: Microsoft se ha asociado con Arrow para proporcionar sensores preconfigurados. Si quiere comprar un sensor preconfigurado, póngase en contacto con Arrow en la siguiente dirección: <hardware.sales@arrow.com>.

Acerca de cómo traer su propio dispositivo: Revise los modelos compatibles que se describen aquí. Después de adquirir el dispositivo, vaya a **Defender para IoT** > **Network Sensors ISO** > **Installation** (Defender para IoT > ISO de sensores de red > Instalación) para descargar el software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO de sensores de red.":::

<a id="anchortext">1</a> La capacidad de ancho de banda puede variar en función de la distribución de los protocolos.

### <a name="virtual-sensors"></a>Sensores virtuales

En esta sección se proporciona información general sobre los sensores virtuales que están disponibles.

| Tipo de implementación | Corporativos | Enterprise | SMB |
|--|--|--|--|
| Ancho de banda máximo | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Número máximo de dispositivos protegidos | 30,000 | 10 000 | 800 |

## <a name="on-premises-management-console-appliance"></a>Dispositivo de consola de administración local

La consola de administración está disponible como una implementación virtual.

| Tipo de implementación | Enterprise |
|--|--|
| Tipo de dispositivo | HPE DL20, máquina virtual |
| Número de sensores administrados | Hasta 300 |

Después de adquirir una consola de administración local, vaya a **Defender para IoT** > **On-premises management console** > **ISO Installation** (Defender para IoT > Consola de administración local > Instalación de ISO) para descargar el archivo ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Consola de administración local.":::

## <a name="appliance-specifications"></a>Especificaciones del dispositivo

En esta sección se describen las especificaciones de hardware de los siguientes dispositivos:

- Implementación corporativa: HPE ProLiant DL360

- Implementación Enterprise: HPE ProLiant DL20

- Implementación SMB: HPE ProLiant DL20

- Especificaciones de la aplicación virtual

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Implementación corporativa: HPE ProLiant DL360

| Componente | Especificaciones técnicas |
|--|--|
| Chasis | Servidor en bastidor 1U |
| Dimensions | 42,9 x 43,46 x 70,7 (cm)/1,69 x 17,11 x 27,83 (pulgadas) |
| Peso | Máximo 16,27 kg (35,86 lb) |
| Procesador | Intel Xeon Silver 4215 R de 3,2 GHz, caché de 11 MB, 8 núclos/16 subprocesos, 130 W |
| Conjunto de chips | Intel C621 |
| Memoria | 32 GB = 2 unidades de 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 6 discos duros hot-plug SAS de 1,2 TB 12G Enterprise 10 k SFF (2,5 pulgadas) - RAID 5 |
| Controladora de red | Incorporada: 2 puertos Broadcom BCM5720 de 1 Gb<br>LOM incorporado: tarjeta de puerto iDRAC de 1 Gb Broadcom BCM5720<br><br>Externa: 1 adaptador de servidor Intel Ethernet i350 QP de 1 Gb, perfil bajo |
| Administración | HPE iLO Advanced |
| Acceso del dispositivo | Dos puertos USB 3.0 en la parte posterior<br>Un puerto USB 2.0 en la parte frontal<br>Un puerto USB 3.0 interno |
| Power | 2 kits de fuente de alimentación hot-plug HPE Platinum con bajo contenido en halógenos y ranura flexible de 500 W |
| Compatibilidad con bastidores | Kit de rieles de fácil instalación HPE 1U Gen10 SFF |

### <a name="appliance-bom"></a>Lista de materiales de dispositivo

| PN | Descripción | Cantidad |
|--|--|--|
| P19766-B21 | Servidor CTO HPE DL360 Gen10 8SFF NC | 1 |
| P19766-B21 | Europa: localización multilingüe | 1 |
| P24479-L21 | Kit FIO Intel Xeon-S 4215 R para DL360 G10 | 1 |
| P24479-B21 | Kit Intel Xeon-S 4215 R para DL360 Gen10 | 1 |
| P00922-B21 | Kit Smart HPE de 16 GB 2Rx8 PC4-2933Y-R | 2 |
| 872479-B21 | Disco duro HPE de 1,2 TB SAS 10 k SFF SC DS | 6 |
| 811546-B21 | Adaptador HPE de 1 GbE BASE-T I350 de 4 puertos | 1 |
| P02377-B21 | Condensador híbrido HPE Smart con\_ cable de 145 mm | 1 |
| 804331-B21 | Controlador HPE Smart Array P408i-a SR Gen10 | 1 |
| 665240-B21 | Adaptador HPE de 1 GbE FLR-T I350 de 4 puertos | 1 |
| 871244-B21 | Kit de ventilador de alto rendimiento HPE DL360 Gen10 | 1 |
| 865408-B21 | Kit de fuente de alimentación hot-plug HPE Platinum con bajo contenido en halógenos y ranura flexible de 500 W | 2 |
| 512485-B21 | 1 licencia de servidor de HPE iLO Advanced con 1 año de soporte técnico | 1 |
| 874543-B21 | Kit de rieles de fácil instalación HPE 1U Gen10 SFF | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Implementación Enterprise: HPE ProLiant DL20

| Componente | Especificaciones técnicas |
|--|--|
| Chasis | Servidor en bastidor 1U |
| Dimensiones (alto x ancho x profundidad) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 pulgadas |
| Peso | 7,9 kg/17,41 lb |
| Procesador | Intel Xeon E-2234, 3,6 GHz, 4 núcleos/8 subprocesos, 71 W |
| Conjunto de chips | Intel C242 |
| Memoria | 2 memorias de 16 GB Dual Rank x8 DDR4-2666 |
| Storage | 3 discos de 1 TB SATA 6G Midline 7,2 k SFF (2,5 Pulgadas) – RAID 5 con un controlador Smart Array P408i-a SR |
| Controladora de red | Incorporada: 2 puertos de 1 GB <br>Incorporada: tarjeta de puerto iLO de 1 GB <br>Externa: 1 adaptador HPE Ethernet de 1 GB 366FLR de 4 puertos |
| Administración | HPE iLO Advanced |
| Acceso del dispositivo | Frontal: 1 puerto USB 3.0, 3,0 USB, 1 puerto de servicio iLO USB <br>Parte posterior: 2 puertos USB 3.0 <br>Interno: 1 puerto USB 3.0 |
| Power | Dos fuentes de alimentación hot-plug de 500 W |
| Compatibilidad con bastidores | Kit de rieles Short Friction 1U de HPE |

### <a name="appliance-bom"></a>Lista de materiales de dispositivo

| PN | Descripción: gama alta | Cantidad |
|--|--|--|
| P06963-B21 | Servidor CTO HPE DL20 Gen10 4SFF | 1 |
| P06963-B21 | Servidor CTO HPE DL20 Gen10 4SFF | 1 |
| P17104-L21 | Kit FIO HPE DL20 Gen10 E-2234 | 1 |
| 879507-B21 | Kit estándar HPE de 16 GB 2Rx8 PC4-2666V-E | 2 |
| 655710-B21 | Disco duro HPE de 1 TB SATA 7,2 k SFF SC DS | 3 |
| P06667-B21 | Kit elevador HPE DL20 Gen10 x8x16 FLOM | 1 |
| 665240-B21 | Adaptador HPE Ethernet de 1 GB 366FLR de 4 puertos | 1 |
| 782961-B21 | Batería Smart Storage HPE de 12 W | 1 |
| 869081-B21 | Controlador HPE Smart Array P408i-a SR G10 con bajo contenido en halógenos | 1 |
| 865408-B21 | Kit de fuente de alimentación hot-plug HPE Platinum con bajo contenido en halógenos y ranura flexible de 500 W | 2 |
| 512485-B21 | 1 licencia de servidor de HPE iLO Advanced con 1 año de soporte técnico | 1 |
| P06722-B21 | Kit FIO de habilitación de fuentes de alimentación redundantes HPE DL20 Gen10 | 1 |
| 775612-B21 | Kit de rieles Short Friction 1U de HPE | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Implementación SMB: HPE ProLiant DL20

| Componente | Especificaciones técnicas |
|--|--|
| Chasis | Servidor en bastidor 1U |
| Dimensiones (alto x ancho x profundidad) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15,05 pulgadas |
| Peso | 7,88 kg/17,37 lb |
| Procesador | Intel Xeon E-2224, 3,4 GHz, 4 núcleos, 71 W |
| Conjunto de chips | Intel C242 |
| Memoria | 1 memoria de 8 GB Dual Rank x8 DDR4-2666 |
| Storage | 2 discos de 1 TB SATA 6G Midline 7,2 k SFF (2,5 Pulgadas) – RAID 1 con Smart Array P208i-a |
| Controladora de red | Incorporada: 2 puertos de 1 GB <br>Incorporada: tarjeta de puerto iLO de 1 GB <br>Externa: 1 adaptador HPE Ethernet de 1 GB 366FLR de 4 puertos |
| Administración | HPE iLO Advanced |
| Acceso del dispositivo | Frontal: 1 puerto USB 3.0, 3,0 USB, 1 puerto de servicio iLO USB <br>Parte posterior: 2 puertos USB 3.0 <br>Interno: 1 puerto USB 3.0 |
| Power | Fuente de alimentación hot-plug de 290 W |
| Compatibilidad con bastidores | Kit de rieles Short Friction 1U de HPE |

### <a name="appliance-bom"></a>Lista de materiales de dispositivo

| PN | Descripción | Cantidad |
|--|--|--|
| P06961-B21 | Servidor CTO HPE DL20 Gen10 NHP con 2 bahías LFF | 1 |
| P06961-B21 | Servidor CTO HPE DL20 Gen10 NHP con 2 bahías LFF | 1 |
| P17102-L21 | Kit FIO HPE DL20 Gen10 E-2224 | 1 |
| 879505-B21 | Kit estándar HPE de 8 GB 1Rx8 PC4-2666V-E | 1 |
| 801882-B21 | Disco duro HPE de 1 TB SATA 7,2 k con una bahía LFF RW | 2 |
| P06667-B21 | Kit elevador HPE DL20 Gen10 x8x16 FLOM | 1 |
| 665240-B21 | Adaptador HPE Ethernet de 1 GB 366FLR de 4 puertos | 1 |
| 869079-B21 | Controlador HPE Smart Array E208i-a SR G10 con bajo contenido en halógenos | 1 |
| P21649-B21 | Kit de fuente de alimentación FIO HPE DL20 Gen10n de 290 W | 1 |
| P06683-B21 | Kit de cables AROC M.2 SATA/LFF para HPE DL20 Gen10 | 1 |
| 512485-B21 | 1 licencia de servidor de HPE iLO Advanced con 1 año de soporte técnico | 1 |
| 775612-B21 | Kit de rieles Short Friction 1U de HPE | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB resistente: HPE Edgeline EL300

| Componente | Especificaciones técnicas |
|--|--|
| Construcción | Diseño de aluminio, sin ventilador y a prueba de polvo |
| Dimensiones (alto x ancho x profundidad) | 200,5 mm (7,9") de alto, 232 mm (9,14") de ancho por 100 mm (3,9") de profundidad |
| Peso | 4,91 kg (10,83 lb) |
| CPU | Intel Core i7-8650U (1,9 GHz/4 núcleos/15 W) |
| Conjunto de chips | Concentrador de controlador de plataforma Intel Q170 |
| Memoria | DDR4 de 8 GB a 2133 MHz SODIMM de temperatura extendida |
| Almacenamiento | Disco SSD de 128 GB 3ME3 mSATA de temperatura extendida |
| Controladora de red | 6 puertos Gigabit Ethernet de Intel® I219 |
| Acceso del dispositivo  | Cuatro puertos USB: dos delanteros y dos traseros; uno interno |
| Adaptador de alimentación | 250 V/10 A |
| Montaje | Kit de montaje, carril DIN |
| Temperatura de funcionamiento | De 0 ºC a +70 ºC  |
| Humedad | 10 % - 90 %, sin condensación |
| Vibración | 0,3 gramos, de 10 Hz a 300 Hz, 15 minutos por eje: raíl Din   |
| Choque | 10G 10 ms, semisinusoidal, tres para cada eje. (pulso positivo y negativo): carril DIN |

### <a name="appliance-bom"></a>Lista de materiales de dispositivo
| Producto | Descripción |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 Converged Edge System |
| P25828-B21 B19 | HPE EL300 v2 Converged Edge System |
| P25833-B21 | Kit de procesador básico FIO Intel Core i7-8650U (1,9 GHz/4 núcleos/15W) FIO para HPE Edgeline EL300 |
| P09176-B21 | Kit FIO de memoria registrada CAS-19-19-19 SODIMM de 8 GB (un módulo de 8 GB) HPE Edgeline 8GB (1x8GB) de rango dual x8 DDR4-2666 |
| P09188-B21 | Disco SSD SATA con amplitud térmica y tres años de garantía de 256 GB 6G con lectura intensiva M.2 2242 HPE Edgeline |
| P04054-B21 | Kit de habilitación HPE Edgeline EL300 SFF a M.2 |
| P08120-B21 | Placa de transferencia FIO HPE Edgeline EL300 de 12 V de corriente continua |
| P08641-B21 | Fuente de alimentación de 12 V de corriente continua y 80 W HPE Edgeline EL300 |
| AF564A | Cable de alimentación de 1,83 m 10 A y 250 V HPE C13-SI-32 IL |
| P25835-B21 | Placa base FIO HPE EL300 v2 |
| R1P49AAE | ELTU de 3 años ininterrumpida Sup_Upd HPE EL300 iSM |
| P08018-B21 opcional | Kit de soporte de perfil bajo HPE Edgeline EL300  |
| P08019-B21 opcional | Kit de montaje en carril DIN HPE Edgeline EL300 |
| P08020-B21 opcional | Kit de montaje en pared HPE Edgeline EL300 |
| P03456-B21 opcional | Tarjeta secundaria FIO TSN de 4 puertos y 1 GbE HPE Edgeline |

## <a name="virtual-appliance-specifications"></a>Especificaciones de la aplicación virtual

### <a name="sensors"></a>Sensores

| Tipo | Corporativos | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memoria | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Dispositivo de consola de administración local

| Tipo | Enterprise |
|--|--|
| Descripción | Tipos de aplicación virtual para implementaciones Enterprise |
| vCPU | 8 |
| Memoria | 32 GB |
| Storage | 1,8 TB |

Hipervisores admitidos: VMware ESXi versión 5.0 y versiones posteriores, Hyper-V

## <a name="additional-certified-appliances"></a>Dispositivos certificados adicionales

En esta sección se detallan los dispositivos adicionales que están certificados por Microsoft, pero que no se ofertan como dispositivos preconfigurados.

| Tipo de implementación | Enterprise |
|--|--|
| Imagen | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Tipo de implementación Enterprise."::: |
| Modelo | Dell PowerEdge R340 XL |
| Puertos de supervisión | Hasta nueve RJ45 o seis OPT |
| Ancho de banda máximo [1](#anchortext2)| 1 Gb/s |
| Número máximo de dispositivos protegidos | 10 000 |

<a id="anchortext2">Uno</a> La capacidad de ancho de banda puede variar en función de la distribución de protocolos.

Después de comprar el dispositivo, vaya a **Defender para IoT** > **Network Sensors ISO** > **Installation** (Defender para IoT > ISO de sensores de red > Instalación) para descargar el software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO de sensores de red.":::

## <a name="next-steps"></a>Pasos siguientes

[Acerca de la instalación de Azure Defender para IoT](how-to-install-software.md)

[Acerca de la configuración de red de Azure Defender para IoT](how-to-set-up-your-network.md)

