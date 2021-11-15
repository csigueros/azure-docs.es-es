---
title: Introducción a Microsoft Azure Data Box Heavy | Microsoft Docs en datos
description: Describe Azure Data Box, una solución híbrida que permite transferir grandes cantidades de datos en Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 10/20/2021
ms.author: alkohli
ms.openlocfilehash: 57a5ccb94f94fdc12083f49394235123b0e26d96
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251877"
---
# <a name="what-is-azure-data-box-heavy"></a>¿Qué es Azure Data Box Heavy?

Azure Data Box Heavy permite enviar cientos de terabytes de datos a Azure de manera rápida, económica y confiable. Para transferir los datos a Azure, recibirá un dispositivo Data Box Heavy con una capacidad de 1 PB de almacenamiento que podrá llenar con los datos y enviar de vuelta a Microsoft. El dispositivo tiene una carcasa resistente para proteger los datos durante el envío.

Una vez que reciba el dispositivo en el centro de datos, configúrelo con la interfaz de usuario local. Copie los datos de los servidores en el dispositivo y envíelo de nuevo a Azure. En el centro de datos de Azure, los datos se cargan a las cuentas de Azure Storage. Puede hacer un seguimiento de todo el proceso de un extremo a otro en Azure Portal.


> [!IMPORTANT]
> - Para solicitar un dispositivo, regístrese en [Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Casos de uso

Data Box Heavy es ideal para tamaños de datos del orden de los cientos de terabytes, donde la conectividad de red no es suficiente para cargar los datos a Azure. El movimiento de datos puede ser único, periódico o una transferencia de datos masiva inicial seguida de transferencias periódicas. Estos son los distintos escenarios donde se puede usar Data Box Heavy para la transferencia de datos.

 - **Migración única**: cuando se mueve gran cantidad de datos locales a Azure.
     - Traslade una biblioteca multimedia desde cintas sin conexión a Azure para crear una biblioteca multimedia en línea.
     - Migre la granja de máquinas virtuales, el servidor SQL Server y las aplicaciones a Azure.
     - Traslade los datos históricos a Azure para realizar un análisis exhaustivo y generar informes con HDInsight.

 - **Transferencia masiva inicial**: cuando se realiza una transferencia masiva inicial con Data Box Heavy (inicialización) seguida de transferencias incrementales a través de la red.
     - Por ejemplo, se usa Data Box Heavy y un asociado de soluciones de copia de seguridad para trasladar a Azure una copia de seguridad histórica de gran tamaño inicial. Una vez completado el proceso, los datos incrementales se transfieren a través de la red a Azure Storage.

 - **Cargas periódicas**: cuando se genera periódicamente una gran cantidad de datos y es necesario moverlos a Azure. Por ejemplo, en la exploración de energía, donde el contenido de vídeo se genera en plataformas petrolíferas y parques eólicos.

## <a name="benefits"></a>Ventajas

Data Box Heavy está pensado para mover grandes cantidades de datos a Azure sin que afecte casi a la red. La solución tiene las siguientes ventajas:

- **Velocidad**: Data Box Heavy utiliza interfaces de red de alto rendimiento de 40 Gbps.

- **Seguridad**: Data Box Heavy tiene protecciones de seguridad integradas para el dispositivo, los datos y el servicio.
    - El dispositivo tiene un sólido uso de mayúsculas y minúsculas protegido por tornillos resistentes a alteraciones y adhesivos de alteración evidente.
    - Los datos en el dispositivo se protegen con un cifrado AES de 256 bits en todo momento.
    - El dispositivo solo se puede desbloquear con una contraseña proporcionada en Azure Portal.
    - El servicio está protegido por las características de seguridad de Azure.
    - Una vez que los datos se cargan a Azure, se borran los discos del dispositivo según las normas 800-88r1 del Instituto Nacional de Normas y Tecnología (NIST).


## <a name="features-and-specifications"></a>Características y especificaciones

El dispositivo Data Box Heavy tiene las siguientes características en esta versión.

| Especificaciones                                          | Descripción              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | Alrededor de 500 libras. <br>Dispositivo en ruedas de bloqueo para su transporte|
| Dimensions                                              | Ancho: 26 pulgadas Alto: 28 pulgadas Largo: 48 pulgadas |
| Espacio en bastidor                                              | No se puede montar en bastidor|
| Se necesitan cables                                         | 4 cables de alimentación de 120 V/10 A (NEMA 5-15) con conexión a tierra incluidos <br> El dispositivo admite una potencia de hasta 240 V con tomacorrientes C-13 <br> Use cables de red compatibles con [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Power                                                    | 4 fuentes de alimentación (PSU) integradas compartidas entre ambos nodos del dispositivo <br> Consumo de potencia típico de 1200 vatios|
| Capacidad de almacenamiento                                        | Alrededor de 1 PB sin procesar, 70 discos de 14 TB cada uno <br> 770 TB de capacidad utilizable|
| Número de nodos                                          | 2 nodos independientes por dispositivo (500 TB cada uno) |
| Interfaces de red por nodo                             | 4 interfaces de red por nodo <br><br> MGMT, DATA3 <ul><li> 2 interfaces de 1 GbE </li><li> MGMT es para la administración y la configuración inicial y no es configurable por el usuario. </li><li> DATA3 es un Protocolo de configuración dinámica de host (DHCP) y configurable por el usuario de manera dinámica.</li></ul>Interfaces de datos DATA1, DATA2 <ul><li>2 interfaces de 40 GbE </li><li> Estáticas o configurables por el usuario para DHCP (valor predeterminado).</li></ul>|


## <a name="components"></a>Componentes

Data Box Heavy incluye los siguientes componentes:

* **Dispositivo de Data Box Heavy**: un dispositivo físico con una sólida estructura exterior que almacena los datos de forma segura. Este dispositivo tiene una capacidad de almacenamiento utilizable de 770 TB.
    
* **Servicio Data Box**: es una extensión de Azure Portal que le permite administrar un dispositivo Data Box Heavy desde una interfaz web a la cual puede acceder desde diferentes ubicaciones geográficas. Use el servicio Data Box para administrar el dispositivo Data Box Heavy. Las tareas del servicio incluyen cómo crear y administrar pedidos, ver y administrar alertas y administrar recursos compartidos.  

* **Interfaz de usuario web local**: es una interfaz de usuario basada en web que se usa para configurar el dispositivo; de esta manera, se podrá conectar a la red local y registrar el dispositivo con el servicio Data Box. Use también la interfaz de usuario web local para apagar y reiniciar el dispositivo, ver registros de copia y ponerse en contacto con el Soporte técnico de Microsoft para realizar una solicitud de servicio.


## <a name="the-workflow"></a>El flujo de trabajo

Un flujo típico incluye los siguientes pasos:

1. **Solicitar**: cree un pedido en Azure Portal, proporcione la información de envío y la cuenta de almacenamiento de Azure de destino para los datos. Si el dispositivo está disponible, Azure lo prepara y lo envía con un identificador de seguimiento del envío.

2. **Recibir**: una vez entregado el dispositivo, conéctelo a la red y enciéndalo con los cables especificados. Active el dispositivo y conéctese a él. Configure la red del dispositivo y monte los recursos compartidos en el equipo host desde donde desea copiar los datos.

3. **Copiar los datos**: copie los datos en los recursos compartidos de Data Box Heavy.

4. **Devolver**: prepare, desactive y devuelva el dispositivo a los centros de datos de Azure.

5. **Cargar**: los datos se copian automáticamente del dispositivo a Azure. Los discos del dispositivo se borran de forma segura según las directrices del Instituto Nacional de Normas y Tecnología (NIST).

Durante este proceso, se le notificará por correo electrónico de todos los cambios de estado.

## <a name="region-availability"></a>Disponibilidad en regiones

Data Box Heavy puede transferir datos en función de la región en la que se implementa el servicio, de la región o el país al que se envía el dispositivo y de la cuenta de Azure Storage en la que transfiere los datos.

- **Disponibilidad del servicio**: Data Box Heavy está disponible en Estados Unidos y la UE.


- **Cuentas de almacenamiento de destino**: las cuentas de almacenamiento que almacenan los datos están disponibles en todas las regiones de Azure donde está disponible el servicio.

Para la información más actualizada sobre la disponibilidad de región de Data Box Heavy, vaya a los [productos de Azure por región](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

<!--## Sign up

Take the following steps to sign up for Data Box Heavy:

1. [Sign into the Azure portal](https://portal.azure.com).
2. Click **+ Create a resource** to create a new resource. Search for **Azure Data Box**. Select **Azure Data Box** service.
3. Click **Create**.
4. Pick the subscription that you want to use for Data Box Heavy. Select the region where you want to deploy the Data Box Heavy resource. In the **Data Box Heavy** option, click **Sign up**.
5. Answer the questions regarding data residence country/region, time-frame, target Azure service for data transfer, network bandwidth, and data transfer frequency. Review Privacy and terms and select the checkbox against Microsoft can use your email address to contact you.

Once you're signed up, you can order a Data Box Heavy.-->

    
