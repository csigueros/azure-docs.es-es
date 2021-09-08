---
title: Diseño e implementación de una base de datos de Oracle en Azure | Microsoft Docs
description: Diseño e implementación de una base de datos de Oracle en el entorno de Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 28c4e7ce7b6910ec1950350ee301e16fa54d4c02
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461612"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Diseño e implementación de una base de datos de Oracle en Azure

## <a name="assumptions"></a>Supuestos

- Planea la migración de una base de datos Oracle del entorno local a Azure.
- Tiene el [paquete de diagnósticos](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) o el [repositorio automático de carga de trabajo](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) (AWR) para la instancia de Oracle Database que quiere migrar.
- Conoce las diversas métricas de Oracle.
- Tiene conocimientos básicos sobre el rendimiento de la aplicación y el uso de la plataforma.

## <a name="goals"></a>Objetivos

- Entender cómo optimizar la implementación de Oracle en Azure.
- Explorar las opciones de optimización del rendimiento de la base de datos de Oracle en el entorno de Azure.
- Tener expectativas claras entre los límites de la optimización física a través de la arquitectura y las ventajas o la optimización lógica del código de la base de datos (SQL) y el diseño general de la base de datos.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Diferencias entre una implementación local y la implementación en Azure 

Las siguientes son algunas cuestiones importantes a tener en cuenta cuando se realiza la migración de aplicaciones locales a Azure. 

Una diferencia importante es que en una implementación en Azure, los recursos, como las máquinas virtuales, discos y redes virtuales, se comparten con otros clientes. Además, los recursos pueden estar limitados en función de los requisitos. En lugar de centrarse en evitar errores (MTBF), Azure se centra más bien en superar los errores (MTTR).

En la tabla siguiente se enumeran algunas de las diferencias entre una implementación local y una implementación en Azure de una base de datos de Oracle.


|  | Implementación local | Implementación en Azure |
| --- | --- | --- |
| **Redes** |LAN/WAN  |SDN (Redes definidas por software)|
| **Grupo de seguridad** |Herramientas de restricción de IP y puerto |[Grupo de seguridad de red (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Resistencia** |MTBF (tiempo medio entre errores) |MTTR (tiempo medio para recuperación)|
| **Mantenimiento planeado** |Aplicación de revisiones/actualizaciones|[Conjuntos de disponibilidad](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (aplicación de revisiones o actualizaciones administradas por Azure) |
| **Recurso** |Dedicado  |Compartido con otros clientes|
| **Regiones** |Centros de datos |[Pares de región](../../regions.md#region-pairs)|
| **Storage** |SAN/discos físicos |[Almacenamiento administrado por Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Escala** |Escalado vertical |Escalado horizontal|


### <a name="requirements"></a>Requisitos

- Determine el uso real de la CPU, ya que la licencia de Oracle se basa en el núcleo: el ajuste de tamaño de las necesidades de vCPU puede ser un ejercicio esencial para el ahorro de costos. 
- Determine el tamaño de la base de datos, el almacenamiento de copia de seguridad y la tasa de crecimiento.
- Determine los requisitos de E/S, que puede calcular en función de los informes de Oracle STATSPACK y AWR o de las herramientas de supervisión de almacenamiento de nivel de sistema operativo.

## <a name="configuration-options"></a>Opciones de configuración

Hay cuatro áreas posibles que se pueden ajustar para mejorar el rendimiento en el entorno de Azure:

- Tamaño de la máquina virtual
- Capacidad de proceso de la red
- Tipos y configuraciones de disco
- Configuración de la caché de disco

### <a name="generate-an-awr-report"></a>Generar un informe de AWR

Si tiene una base de datos de Oracle Enterprise Edition y está planeando migrar a Azure, tiene varias opciones. Si tiene el [paquete de diagnósticos](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) para sus instancias de Oracle, puede ejecutar el informe de AWR de Oracle para obtener las métricas (IOPS, Mbps, GiBs, etc.). En el caso de las bases de datos sin la licencia del paquete de diagnósticos o de una base de datos Standard Edition, se pueden recopilar las mismas métricas importantes con un informe de Statspack después de recopilar instantáneas manuales.  La diferencia principal entre estos dos métodos de creación de informes es que AWR se recopila automáticamente y proporciona más información sobre la base de datos que la opción de creación de informes predecesora de Statspack.

Puede considerar la posibilidad de ejecutar el informe de AWR durante la carga de trabajo normal y máxima, para poder comparar ambas. Para recopilar la carga de trabajo más precisa, considere la posibilidad de usar un informe de ventana extendida de una semana, en comparación con una ejecución de 24 horas, y tenga en cuenta que AWR proporciona promedios como parte de sus cálculos en el informe.  En el caso de una migración del centro de datos, se recomienda recopilar informes para el ajuste de tamaño en los sistemas de producción y calcular las copias de base de datos restantes utilizadas para las pruebas de usuario, las pruebas, el desarrollo, etc. por porcentajes (UAT igual a producción, prueba y desarrollo 50 % del tamaño de la producción, etc.).

De forma predeterminada, el repositorio de AWR conserva 8 días de datos y toma instantáneas en intervalos de una hora.  Para ejecutar un informe de AWR desde la línea de comandos, se puede realizar lo siguiente desde un terminal:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Métricas clave

El informe le pedirá la siguiente información:
- Tipo de informe: HTML o TEXT (HTML en 12.1 y proporciona más información que el formato TEXT).
- El número de días de las instantáneas que se van a mostrar (en intervalos de una hora, un informe de una semana tendría 168 valores distintos en los identificadores de instantánea).
- SnapshotID inicial de la ventana de informe.
- SnapshotId final de la ventana de informe.
- Nombre del informe que va a crear el script de AWR.

Si ejecuta el comando AWR en un clúster de aplicaciones real (RAC), el informe de la línea de comandos es awrgrpt.sql en lugar de awrrpt.sql.  El informe "g" creará un informe para todos los nodos de la base de datos RAC en un solo informe y tendrá que ejecutar uno en cada nodo RAC.

Estas son las métricas que puede obtener del informe de AWR:

- Nombre de la base de datos, nombre de la instancia y nombre del host
- Versión de base de datos (compatibilidad con Oracle)
- Núcleos de CPU
- SGA/PGA (y asesores para informarle de si está subdimensionado)
- Memoria total en GB
- % ocupación de la CPU
- CPU DE BD
- IOPS (lectura y escritura)
- Mbps (lectura/escritura)
- Capacidad de proceso de la red
- Tasa de latencia de red (baja/alta)
- Principales eventos de espera 
- Configuración de parámetros para la base de datos
- Es RAC de base de datos, Exadata, con características o configuraciones avanzadas.

### <a name="virtual-machine-size"></a>Tamaño de la máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. La estimación del tamaño de la máquina virtual se basa en el uso de CPU, memoria y E/S según el informe de AWR

Puede consultar los primeros cinco eventos de primer plano cronometrados, que indican dónde están los cuellos de botella del sistema.

Por ejemplo, en el diagrama siguiente, la sincronización de archivos de registro está en la primera posición. Indica el número de esperas necesarias para que LGWR escriba el búfer de registro en el archivo de registro de puesta al día. Estos resultados indican que son necesarios almacenamiento o discos de mejor rendimiento. Además, también se muestra en el diagrama el número de CPU (núcleos) y la memoria.

![Captura de pantalla que muestra la sincronización del archivo de registro en la parte superior de la tabla.](./media/oracle-design/cpu_memory_info.png)

El siguiente diagrama muestra el total de E/S de lectura y escritura. Hubo 59 GB de lectura y 247,3 GB de escritura durante la realización del informe.

![Captura de pantalla que muestra el total de E/S de lectura y escritura.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Elegir una máquina virtual

En función de la información recopilada en el informe de AWR, el paso siguiente consiste en elegir una máquina virtual de un tamaño similar que cumpla los requisitos. Puede encontrar una lista de máquinas virtuales disponibles en el artículo [Optimización de memoria](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ajustar el tamaño de la máquina virtual con una serie de máquina virtual similar en función del valor de ACU

Una vez que haya elegido la máquina virtual, preste atención a la ACU de la máquina virtual. Puede elegir una máquina virtual diferente en función del valor de ACU que mejor se adapte a sus necesidades. Para más información, consulte [Unidad de proceso de Azure (ACU)](../../acu.md).

![Captura de pantalla de la página de ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Capacidad de proceso de la red

El diagrama siguiente muestra la relación que existe entre el rendimiento y las E/S por segundo (IOPS):

![Captura de pantalla del rendimiento](./media/oracle-design/throughput.png)

El rendimiento total de la red se calcula en función de la siguiente información:
- Tráfico de SQL*Net
- MBps multiplicado por el número de servidores (secuencia de salida, como Oracle Data Guard)
- Otros factores, como la replicación de aplicaciones

![Captura de pantalla del rendimiento de SQL*Net](./media/oracle-design/sqlnet_info.png)

En función de los requisitos de ancho de banda de red, puede elegir diferentes tipos de puerta de enlace. Entre los tipos se incluyen Básica, VpnGw y Azure ExpressRoute. Para más información, consulte la [página de precios de VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recomendaciones**

- La latencia de red es superior en comparación con una implementación local. El rendimiento puede mejorar considerablemente si se reducen los recorridos de ida y vuelta de red.
- Consolide las aplicaciones que tengan transacciones elevadas o estén "fragmentadas" en la misma máquina virtual para reducir los recorridos de ida y vuelta.
- Use una instancia de Virtual Machines con las [redes aceleradas](../../../virtual-network/create-vm-accelerated-networking-cli.md) para mejorar el rendimiento de la red.
- En cuanto a ciertas distribuciones de Linux, habilite la [compatibilidad con TRIM/UNMAP](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Instale [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) en una máquina virtual individual.
- Las páginas de gran tamaño no están habilitadas en Linux de forma predeterminada. Habilite las páginas de gran tamaño y establezca `use_large_pages = ONLY` en Oracle DB. Esto puede ayudarle a mejorar el rendimiento. Para obtener más información, consulte [USE_LARGE_PAGES](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Tipos y configuraciones de disco

- *Discos de sistema operativo predeterminados*: estos tipos de disco ofrecen datos persistentes y almacenamiento en caché. Estos discos están optimizados para el acceso del sistema operativo en tiempo de inicio y no están diseñados para cargas de trabajo transaccionales o de almacenamiento de datos (analíticas).

- *Managed Disks*: Azure administra las cuentas de almacenamiento utilizadas para los discos de la máquina virtual. Especifique el tipo de disco (a menudo SSD prémium para cargas de trabajo de Oracle) y el tamaño del disco que necesita. Azure crea y administra el disco en su nombre.  El disco administrado SSD prémium solo está disponible para la serie de máquinas virtuales optimizadas para memoria y específicamente diseñadas. Después de elegir un tamaño de máquina virtual determinado, el menú muestra solo las SKU de Premium Storage disponibles que se basan en ese tamaño de máquina virtual.

![Captura de pantalla de la página de disco administrado](./media/oracle-design/premium_disk01.png)

Una vez configurado el almacenamiento en una máquina virtual, tal vez le interese realizar una prueba de carga en los discos antes de crear una base de datos. Puede ser útil conocer la velocidad de E/S en términos de latencia y rendimiento para ayudarle a determinar si las máquinas virtuales admiten el rendimiento esperado con objetivos de latencia.

Existen diversas herramientas para realizar pruebas de carga de aplicaciones, como Oracle Orion, SLOB y Fio.

Vuelva a ejecutar la prueba de carga después de implementar una base de datos de Oracle. Inicie las cargas de trabajo normales y máximas y los resultados mostrarán la línea de base de su entorno.  Sea realista en la prueba de carga de trabajo: no tiene sentido ejecutar una carga de trabajo que no se parece en nada a lo que se ejecutará en la máquina virtual en realidad.

Como Oracle es una base de datos intensiva en E/S para muchos, es muy importante dimensionar el almacenamiento en función de la tasa de IOPS en lugar del tamaño del almacenamiento. Por ejemplo, si los IOPS necesarios son 5000, pero solo necesita 200 GB, puede elegir el disco Premium P30 aunque se ofrece con más de 200 GB de almacenamiento.

La tasa de IOPS puede obtenerse de los informes de AWR. Viene determinado por el registro de recuperación, las lecturas físicas y la velocidad de escritura.  Compruebe siempre que la serie de máquinas virtuales elegida tiene la capacidad de controlar también la demanda de E/S de la carga de trabajo.  Si la VM tiene un límite de E/S más bajo que el almacenamiento, el límite máximo lo establecerá la VM.

![Captura de pantalla de la página del informe de AWR](./media/oracle-design/awr_report.png)

Por ejemplo: el tamaño del registro de recuperación es 12 200 000 bytes por segundo, lo que equivale a 11,63 MBPs.
El número de IOPS es 12 200 000 / 2 358 = 5 174.

Una vez que se haya hecho una idea de los requisitos de E/S, puede elegir la combinación de unidades más adecuada para cumplirlos.

**Recomendaciones**

- Para el espacio de tabla de datos, reparta la carga de trabajo de E/S entre varios discos mediante el almacenamiento administrado u Oracle ASM.
- Use la compresión avanzada de Oracle para reducir la E/S (para datos e índices).
- Separe los registros de rehacer y los espacios de tabla temporales y de deshacer en discos de datos independientes.
- No colocar ningún archivo de aplicación en el disco predeterminado del sistema operativo (/dev/sda). Estos discos están optimizados para un tiempo de arranque rápido de la máquina virtual y podría no proporcionar un buen rendimiento para la aplicación.
- Al usar VM de la serie M en el almacenamiento Premium, habilite el [acelerador de escritura](../../how-to-enable-write-accelerator.md) en el disco de registros de fase de puesta al día.
- Considere la posibilidad de mover los registros de rehacer con una latencia alta a un disco Ultra.

### <a name="disk-cache-settings"></a>Configuración de la caché de disco

Hay tres opciones para el almacenamiento en caché de host, pero para una base de datos Oracle, solo se recomienda el almacenamiento en caché de solo lectura para una carga de trabajo de base de datos.  La opción de lectura y escritura puede introducir vulnerabilidades significativas en un archivo de datos, donde el objetivo de la escritura de una base de datos es registrarla en el archivo de datos, no almacenar en caché la información.

A diferencia de un sistema de archivos o una aplicación, para una base de datos, la recomendación para el almacenamiento en caché de host es *solo lectura*: todas las solicitudes se almacenan en caché para lecturas futuras. Todas las escrituras se seguirán escribiendo en el disco.

**Recomendaciones**

Para maximizar el rendimiento, se recomienda comenzar con **solo lectura** para el almacenamiento en caché del host siempre que sea posible. En el caso de Premium Storage, es importante tener en cuenta que debe deshabilitar las "barreras" al montar el sistema de archivos con las opciones de **solo lectura**. Actualice el archivo/etc/fstab con el UUID en los discos.

![Captura de pantalla de la página del disco administrado que muestra las opciones Solo lectura y Ninguno.](./media/oracle-design/premium_disk02.png)

- En el caso de los discos de sistema operativo, use el almacenamiento en caché de **lectura/escritura** predeterminado y SSD prémium para máquinas virtuales de carga de trabajo.  Asegúrese también de que el volumen usado para el intercambio también esté en una SSD prémium.
- En el caso de todos los archivos DATAFILES, use **solo lectura** para el almacenamiento en caché. El almacenamiento en caché de solo lectura solo está disponible para el disco administrado prémium, P30 y versiones posteriores.  Existe un límite de un volumen de 4095 GiB que se puede usar con el almacenamiento en caché de solo lectura.  Cualquier asignación mayor deshabilitará el almacenamiento en caché de host de forma predeterminada.

Si las cargas de trabajo varían considerablemente entre el día y la noche, y la carga de trabajo de E/S puede admitirlo, la SSD prémium P1-P20 con ráfagas puede proporcionar el rendimiento necesario durante las cargas por lotes de la noche o las demandas de E/S limitadas.  

## <a name="security"></a>Seguridad

Después de que se instala y configura el entorno de Azure, el siguiente paso es proteger la red. Estas son algunas recomendaciones:

- *Directiva del NSG*: el grupo de seguridad de red (NSG) se puede definir como una subred o NIC. Es más fácil controlar el acceso en el nivel de subred para garantizar la seguridad y forzar el enrutamiento de elementos como el firewall de aplicaciones.

- *Jumpbox*: Para un acceso más seguro, los administradores no deben conectarse directamente con el servicio de aplicación o la base de datos. Se usa un Jumpbox como un medio entre la máquina del administrador y los recursos de Azure.
![Captura de pantalla de la página de topología de Jumpbox](./media/oracle-design/jumpbox.png)

    La máquina del administrador debería tener un acceso restringido a la dirección IP del Jumpbox únicamente. El Jumpbox debe tener acceso a la aplicación y la base de datos.

- *Red privada* (subredes): se recomienda que tenga el servicio de aplicación y la base de datos en subredes independientes, para que pueda establecerse un mejor control con la directiva de NSG.


## <a name="additional-reading"></a>Lecturas adicionales

- [Configuración de ASM de Oracle](configure-oracle-asm.md)
- [Configuración de Oracle Data Guard](configure-oracle-dataguard.md)
- [Configuración de Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Copia de seguridad y recuperación de Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)
- [Ejemplos de la CLI de Azure para implementación de máquinas virtuales](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
