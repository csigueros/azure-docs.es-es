---
title: Solución de problemas y supervisión desde el lado de HANA en SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Aprenda a supervisar y solucionar problemas de SAP HANA en Azure (instancias grandes) mediante los recursos proporcionados por SAP HANA.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/18/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 691f9390d291d63b1263ecbcd77d1aa7bed48167
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580969"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Supervisión y solución de problemas en el lado HANA

En este artículo, aprenderá a supervisar y solucionar problemas de SAP HANA en Azure (instancias grandes) mediante los recursos proporcionados por SAP HANA. 

Para analizar los problemas relacionados con SAP HANA en Azure (instancias grandes), querrá identificar la causa raíz de un problema. SAP ha publicado una gran cantidad de documentación para ayudarle. Las preguntas más frecuentes relacionadas con el rendimiento de SAP HANA se pueden encontrar en las siguientes notas de SAP:

- [SAP Note #2222200 – FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200) (Nota de SAP 2222200: preguntas más frecuentes sobre la red de SAP HANA)
- [SAP Note #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040) (Nota de SAP 2100040: preguntas más frecuentes sobre la CPU de SAP HANA)
- [SAP Note #199997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064) (Nota de SAP 199997: preguntas más frecuentes sobre la memoria de SAP HANA)
- [SAP Note #200000 – FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000) (Nota de SAP 200000: preguntas más frecuentes sobre la optimización del rendimiento de SAP HANA)
- [SAP Note #199930 – FAQ: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930) (Nota de SAP 199930: preguntas más frecuentes sobre el análisis de E/S de SAP HANA)
- [SAP Note #2177064 – FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064) (Nota de SAP 2177064: preguntas más frecuentes sobre el reinicio y los bloqueos del servicio de SAP HANA)

## <a name="sap-hana-alerts"></a>Alertas de SAP HANA

En primer lugar, compruebe los registros de alerta de SAP HANA actuales. En SAP HANA Studio, vaya a **Consola de administración: Alertas: Mostrar: Todas las alertas**. En esta pestaña se mostrarán todas las alertas de SAP HANA para valores (memoria física libre, uso de CPU, etc.) que se encuentran fuera de los umbrales mínimo y máximo establecidos. De forma predeterminada, las comprobaciones se actualizan automáticamente cada 15 minutos.

![En SAP HANA Studio, vaya a Consola de administración: Alertas: Mostrar: Todas las alertas.](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

En el caso de una alerta desencadenada por una configuración incorrecta del umbral, restablezca al valor predeterminado o a uno más razonable.

![Restablecer el umbral al valor predeterminado o a un valor más razonable](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Las siguientes alertas pueden indicar problemas de recursos de CPU:

- Uso de CPU del host (alerta 5)
- Operación del punto de retorno más reciente (alerta 28)
- Duración del punto de retorno (alerta 54)

Puede observar el consumo de CPU elevado en la base de datos de SAP HANA desde:

- La alerta 5 (Uso de CPU del host) se desencadena para el uso de CPU actual o antiguo
- El uso de CPU mostrado en la pantalla de información general

![Uso de CPU mostrado en la pantalla de información general](./media/troubleshooting-monitoring/image3-cpu-usage.png)

El gráfico Carga podría mostrar un uso de CPU elevado o un uso de elevado en el pasado:

![El gráfico Carga podría mostrar un uso de CPU elevado o un uso de elevado en el pasado](./media/troubleshooting-monitoring/image4-load-graph.png)

Una alerta desencadenada por un uso elevado de la CPU podría deberse a varias razones:
- Ejecución de determinadas transacciones
- Carga de datos
- Trabajos que no responden
- Instrucciones SQL de larga duración
- Rendimiento de consultas incorrecto (por ejemplo, con BW en cubos de HANA)

Para obtener los pasos detallados de solución de problemas de utilización de la CPU, vea [Solución de problemas de SAP HANA: causas relacionadas con la CPU y soluciones](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/4fbc915462db406aa2fe92b708b95189.html?q=%20SAP%20HANA%20Troubleshooting:%20CPU%20Related%20Causes%20and%20Solutions).

## <a name="operating-system-os"></a>Sistema operativo (SO)

Una comprobación importante para SAP HANA en Linux consiste en asegurarse de que Transparent Huge Pages están deshabilitadas. Para obtener más información, vea [Nota de SAP n.º2131662: Transparent Huge Pages (THP) en servidores de SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

Puede comprobar si Transparent Huge Pages está habilitado con el siguiente comando de Linux: **cat /sys/kernel/mm/transparent\_hugepage/enabled**.
- Si _always_ está entre corchetes, significa que Transparent Huge Pages está habilitado: [always] madvise never.
- Si _never_ está entre corchetes, significa que Transparent Huge Pages está deshabilitado: always madvise [never].

El siguiente comando de Linux no debe devolver nada: **rpm -qa | grep ulimit.** Si indica que _ulimit_ está instalado, desinstálelo inmediatamente.

## <a name="memory"></a>Memoria

Puede observar que la cantidad de memoria asignada a la base de datos de SAP HANA es mayor de la esperada. Las siguientes alertas indican problemas con un uso de memoria elevado:

- Uso de memoria física del host (alerta 1)
- Uso de memoria del servidor de nombres (alerta 12)
- Uso total de la memoria de las tablas de almacenamiento de columnas (alerta 40)
- Uso de memoria de los servicios (alerta 43)
- Uso de memoria del almacenamiento principal de las tablas de almacenamiento de columnas (alerta 45)
- Archivos de volcado de tiempo de ejecución (alerta 46)

Para obtener los pasos detallados de solución de problemas de memoria, vea [Solución de problemas de SAP HANA: causas principales de problemas de memoria](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/3a2ea5c4593b4b8d823b5b48152bd1d4.html).

## <a name="network"></a>Red

Consulte [SAP Note #2081065 – Troubleshooting SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065) (Nota de SAP n.º 2081065: Solución de problemas de red de SAP HANA) y realice los pasos de solución de problemas de red que se indican.

1. Analice el tiempo de ida y vuelta entre el cliente y el servidor.
    - Ejecute el script SQL [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700) _._
  
2. Analice la comunicación entre nodos.
    - Ejecute el script SQL [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700) _._

3. Ejecute el comando **ifconfig** de Linux (en la salida se muestra si se hay pérdidas de paquetes).
4. Ejecute el comando de Linux **tcpdump**.

Además, use la herramienta de código abierto [IPERF](https://iperf.fr/) (u otra similar) para medir el rendimiento de red real de la aplicación.

Para obtener los pasos de solución de problemas de red, vea [Solución de problemas de SAP HANA: problemas de conectividad y de rendimiento de red](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/a3ccdff1aedc4720acb24ed8826938b6.html?q=Networking%20Performance%20and%20Connectivity%20Problems).

## <a name="storage"></a>Almacenamiento

Imagine que hay problemas con el rendimiento de E/S. Los usuarios finales pueden encontrar que las aplicaciones, o el sistema en su conjunto, se ejecutan lentamente, no responden o incluso pueden dejar de responder. En la pestaña **Volúmenes** de SAP HANA Studio, puede ver los volúmenes conectados y los utilizados por cada servicio.

![En la pestaña Volúmenes de SAP HANA Studio, puede ver los volúmenes conectados y los utilizados por cada servicio](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

En la parte inferior de la pantalla (en la pestaña Volúmenes) puede ver los detalles de los volúmenes, como los archivos y las estadísticas de E/S.

![En la parte inferior de la pantalla puede ver los detalles de los volúmenes, como los archivos y las estadísticas de E/S.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Para obtener los pasos de solución de problemas de E/S, vea [Solución de problemas de SAP HANA: causas principales relacionadas con E/S y soluciones](https://help.sap.com/viewer/4e9b18c116aa42fc84c7dbfd02111aba/2.0.05/en-US/dc6ff98fa36541e997e4c719a632cbd8.html?q=I%2FO%20Related%20Root%20Causes%20and%20Solutions). Para obtener los pasos de solución de problemas relacionados con el disco, vea [Solución de problemas de SAP HANA: causas principales relacionadas con el disco y soluciones](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/474cb08a715c42fe9f7cc5efdc599959.html?q=Disk%20Related%20Root%20Causes%20and%20Solutions).

## <a name="diagnostic-tools"></a>Herramientas de diagnóstico

Realice una comprobación del estado de SAP HANA mediante HANA\_Configuration\_Minichecks. Esta herramienta devuelve problemas técnicos potencialmente críticos que ya deberían haber aparecido como alertas en SAP HANA Studio.

1. Consulte [SAP Note #1969700 – SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) (Nota de SAP #1969700: recopilación de instrucciones SQL para SAP HANA) y descargue el archivo SQL Statements.zip adjunto a esa nota. Almacene este archivo .zip en el disco duro local.

2. En SAP HANA Studio, en la pestaña **System Information** (Información del sistema), haga clic en la columna **Name** (Nombre) y seleccione **Import SQL Statements** (Instrucciones SQL de importación).

    ![En SAP HANA Studio, en la pestaña System Information (Información del sistema), haga clic en la columna Name (Nombre) y seleccione Import SQL Statements (Instrucciones SQL de importación)](./media/troubleshooting-monitoring/image7-import-statements-a.png)

3. Seleccione el archivo SQL Statements.zip que está almacenado localmente; se importará una carpeta con las instrucciones SQL correspondientes. En este punto, se pueden ejecutar las numerosas comprobaciones de diagnóstico con estas instrucciones SQL.

    Por ejemplo, para probar los requisitos de ancho de banda de la replicación del sistema de SAP HANA, haga clic en la instrucción **Ancho de banda** en **Replicación: Ancho de banda** y seleccione **Abrir** en la consola de SQL.

    La instrucción SQL completa se abre, lo que permite cambiar y, después, ejecutar los parámetros de entrada (sección de modificación).

    ![La instrucción SQL completa se abre, lo que permite cambiar y, después, ejecutar los parámetros de entrada (sección de modificación)](./media/troubleshooting-monitoring/image8-import-statements-b.png)

4. Otro ejemplo consiste en hacer clic en las instrucciones en **Replication: Overview** (Replicación: Información general). Seleccione **Execute** (Ejecutar) en el menú contextual:

    ![Otro ejemplo consiste en hacer clic en las instrucciones en Replication: Overview (Replicación: Información general). Seleccione Execute (Ejecutar) en el menú contextual](./media/troubleshooting-monitoring/image9-import-statements-c.png)

    Verá información útil para solucionar problemas:

    ![Verá información útil para solucionar problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

5. Haga lo mismo para HANA\_Configuration\_Minichecks y compruebe si hay alguna marca _X_ en la columna _C_ (Crítico).

    Ejemplo de salidas:

    **HANA\_Configuration\_MiniChecks\_Rev102.01+1** para comprobaciones general de SAP HANA.

    ![HANA\_Configuration\_MiniChecks\_Rev102.01+1 para comprobaciones general de SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

    **HANA\_Services\_Overview** para obtener información general sobre los servicios de SAP HANA en ejecución.

    ![HANA\_Services\_Overview para obtener información general sobre los servicios de SAP HANA en ejecución](./media/troubleshooting-monitoring/image12-services-overview.png)

    **HANA\_Services\_Statistics** para obtener información sobre los servicios de SAP HANA (CPU, memoria, etc.)

    ![HANA\_Services\_Statistics para información sobre los servicio de SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

    **HANA\_Configuration\_Overview\_Rev110+** para información general sobre la instancia de SAP HANA.

    ![HANA\_Configuration\_Overview\_Rev110+ para información general sobre la instancia de SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

    **HANA\_Configuration\_Parameters\_Rev70+** para comprobar los parámetros de SAP HANA.

    ![HANA\_Configuration\_Parameters\_Rev70+ para comprobar los parámetros de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar la alta disponibilidad en el sistema operativo SUSE mediante el dispositivo STONITH.

> [!div class="nextstepaction"]
> [Configuración de alta disponibilidad en SUSE mediante STONITH](ha-setup-with-stonith.md)
