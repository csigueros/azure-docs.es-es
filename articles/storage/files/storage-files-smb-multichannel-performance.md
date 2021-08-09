---
title: 'Rendimiento de SMB multicanal: Azure Files'
description: Obtenga información sobre el rendimiento de SMB multicanal.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e8b2f64b0249cfe3b8a0a2e1bebcf7daabfe94dc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094625"
---
# <a name="smb-multichannel-performance"></a>Rendimiento de SMB multicanal

SMB multicanal de Azure Files (versión preliminar) habilita un cliente SMB 3.x para establecer varias conexiones de red en los recursos compartidos de archivos Premium de una cuenta de FileStorage. El protocolo SMB 3.x presentó la característica SMB multicanal en clientes de Windows Server 2012 y Windows 8. Por este motivo, cualquier cliente SMB 3.x de Azure Files que admita SMB multicanal podrá aprovechar la característica para sus recursos compartidos de archivos Premium de Azure. No hay ningún costo adicional por habilitar SMB multicanal en una cuenta de almacenamiento.

## <a name="benefits"></a>Ventajas

SMB multicanal de Azure Files permite a los clientes usar varias conexiones de red que proporcionan un mayor rendimiento a la vez que se reduce el costo de propiedad. El mayor rendimiento se logra a través de la agregación de ancho de banda en varias NIC y el uso de la compatibilidad con Ajuste de escala en lado de recepción (RSS) para que las NIC distribuyan la carga de E/S entre varias CPU.

- **Aumento de rendimiento**: varias conexiones permiten la transferencia de los datos a través de varias rutas de acceso en paralelo y, por tanto, benefician significativamente a las cargas de trabajo que usan tamaños de archivo más grandes con tamaños de E/S más grandes, y requieren un alto rendimiento de una sola máquina virtual o un conjunto de máquinas virtuales más pequeño. Entre algunas de estas cargas de trabajo se incluyen medios y entretenimiento para la creación de contenido o la transcodificación, la genómica y el análisis de riesgos de servicios financieros.
- **E/S por segundo más alta**: la funcionalidad RSS de NIC permite una distribución de carga eficaz entre varias CPU con varias conexiones. Esto ayuda a lograr una escala de IOPS más alta y un uso eficaz de las CPU de VM. Esto es útil para las cargas de trabajo que tienen tamaños de E/S pequeños, como las aplicaciones de base de datos.
- **Tolerancia a errores de la red**:   varias conexiones mitigan el riesgo de interrupción, ya que los clientes ya no dependen de una conexión individual.
- **Configuración automática**:   cuando SMB multicanal está habilitado en los clientes y las cuentas de almacenamiento, permite la detección dinámica de las conexiones existentes y puede crear rutas de acceso de conexión adicionales según sea necesario.
- **Optimización de costos**: las cargas de trabajo pueden alcanzar una mayor escala de una sola máquina virtual o un conjunto pequeño de máquinas virtuales mientras se conectan a recursos compartidos Premium. Esto podría reducir el costo total de propiedad al reducir el número de máquinas virtuales necesarias para ejecutar y administrar una carga de trabajo.

Para obtener más información sobre SMB multicanal, consulte la [documentación de Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Esta característica proporciona mayores ventajas de rendimiento a las aplicaciones multiproceso, pero normalmente no ayuda a las aplicaciones de un único subproceso. Consulte la sección [Comparación del rendimiento](#performance-comparison) para obtener más detalles.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidad regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configuración

SMB multicanal solo funciona cuando la característica está habilitada tanto en el lado del cliente (su cliente) como en el lado del servicio (su cuenta de almacenamiento de Azure).

En los clientes Windows, SMB multicanal está habilitado de manera predeterminada. Puede comprobar la configuración ejecutando el siguiente comando de PowerShell: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
En la cuenta de almacenamiento de Azure, deberá habilitar SMB multicanal. Consulte [Habilitar SMB multicanal (versión preliminar)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Deshabilitar SMB multicanal
En la mayoría de los escenarios, concretamente las cargas de trabajo multiproceso, los clientes deben ver un rendimiento mejorado con SMB multicanal. Sin embargo, algunos escenarios específicos como las cargas de trabajo de un único subproceso o con fines de prueba, es posible que desee deshabilitar SMB multicanal. Consulte [Comparación del rendimiento](#performance-comparison) para obtener más detalles.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Comprobar que SMB multicanal está configurado correctamente

1. Cree un recurso compartido de archivos Premium o use uno existente.
1. Asegúrese de que el cliente admite SMB multicanal (uno o varios adaptadores de red tienen habilitado el ajuste de escala en lado de recepción). Consulte la [documentación de Windows](/azure-stack/hci/manage/manage-smb-multichannel) para obtener más detalles.
1. Monte un recurso compartido de archivos en el cliente.
1. Genere carga con la aplicación.
    Una herramienta de copia como robocopy o MT, o bien cualquier herramienta de rendimiento como Deskspd para archivos de lectura/escritura, pueden generar carga.
1. Abra PowerShell como administrador y use el siguiente comando: `Get-SmbMultichannelConnection |fl`
1. Busque las propiedades **MaxChannels** y **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Captura de pantalla de los resultados de Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Comparación del rendimiento

Hay dos categorías de patrones de carga de trabajo de lectura/escritura: de un único subproceso y multiproceso. La mayoría de las cargas de trabajo usan varios archivos, pero podría haber casos de uso específicos en los que la carga de trabajo funcione con un solo archivo en un recurso compartido. En esta sección se tratan diversos casos de uso y el impacto en el rendimiento de cada uno de ellos. En general, la mayoría de las cargas de trabajo son multiproceso y distribuyen la carga de trabajo por varios archivos, por lo que deberían poder observar mejoras significativas en el rendimiento con SMB multicanal.

- **Archivos multiproceso o varios archivos**: según el patrón de carga de trabajo, debería poder observar una mejora significativa en el rendimiento en E/S de lectura y escritura a través de varios canales. Las mejoras de rendimiento varían desde cualquier punto entre 2x y 4x en términos de IOPS, rendimiento y latencia. En esta categoría, SMB multicanal debe habilitarse para obtener el mejor rendimiento.
- **Archivo multiproceso o un solo archivo**: En la mayoría de los casos de uso de esta categoría, las cargas de trabajo se beneficiarán de tener habilitado SMB multicanal, especialmente si la carga de trabajo tiene un tamaño medio de E/S superior a 16 k. Algunos escenarios de ejemplo que se benefician de SMB multicanal son la copia de seguridad o la recuperación de un solo archivo grande. Una excepción en la que puede que desee deshabilitar SMB multicanal es si la carga de trabajo son pequeñas E/S pesadas. En ese caso, tal vez observe una ligera pérdida de rendimiento (10 %). Dependiendo del caso de uso, considere la posibilidad de distribuir la carga entre varios archivos o deshabilitar la característica. Consulte la sección [Configuración](#configuration) para obtener más información.
- **Archivo de un único subproceso/varios archivos o un solo archivo**: en la mayoría de las cargas de trabajo de un único subproceso, existen unas ventajas de rendimiento mínimas debido a la falta de paralelismo. Normalmente, hay una ligera degradación del rendimiento (10 %) si SMB multicanal está habilitado. En este caso, es ideal deshabilitar SMB multicanal, con una excepción. Si la carga de trabajo de un único subproceso puede distribuir la carga entre varios archivos y usa un tamaño medio de E/S más grande (superior a 16 k), deben existir unas ventajas de rendimiento mínimas de SMB multicanal.

### <a name="performance-test-configuration"></a>Configuración de prueba de rendimiento

En los gráficos de este artículo, se usó la siguiente configuración: una sola máquina virtual D32s v3 Estándar con una sola NIC habilitada para RSS con cuatro canales. La carga se generó mediante diskspd.exe, multiproceso con una profundidad de E/S de 10 y E/S aleatorias con diversos tamaños de E/S.

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/Mbps (tamaño de caché en GiB) | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Captura de pantalla que muestra la configuración de la prueba de rendimiento." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Archivos multiproceso o varios archivos con SMB multicanal

La carga se realizó con respecto a 10 archivos con diversos tamaños de E/S. Los resultados de la prueba de escalado vertical mostraron mejoras significativas en los resultados de la prueba de rendimiento e IOPS con SMB multicanal habilitado. En los diagramas siguientes se describen los resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagrama del rendimiento" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama del rendimiento." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- En una sola NIC, en el caso de las lecturas, el rendimiento aumentó entre el doble y el triple, mientras que en el caso de las escrituras, una mejora en términos de IOPS y rendimiento se multiplicó por 3 y hasta por 4.
- SMB multicanal permitió que IOPS y el rendimiento alcanzaran los límites de VM incluso con una sola NIC y el límite de cuatro canales.
- Dado que la salida (o las lecturas en el almacenamiento) no se mide, el rendimiento de lectura pudo superar el límite publicado de VM de 16 000 Mbps (2 GiB/s). La prueba alcanzó más de 2,7 GiB/s. La entrada (o las escrituras en el almacenamiento) sigue estando sujeta a límites de VM.
- Distribución de la carga por varios archivos permitidos para mejoras sustanciales.

Un comando de ejemplo que se usó en esta prueba es: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Cargas de trabajo multiproceso o de un solo archivo con SMB multicanal

La carga se realizó con respecto a un solo archivo de 128 GiB. Con SMB multicanal habilitado, la prueba de escalado vertical con archivos multiproceso o únicos mostró mejoras en la mayoría de los casos. En los diagramas siguientes se describen los resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagrama del rendimiento de IOPS." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama del rendimiento de un solo archivo." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- En una sola NIC con un tamaño medio de E/S mayor (superior a 16 k), había mejoras significativas tanto en las lecturas como en las escrituras.
- En el caso de los tamaños de E/S más pequeños, hubo un ligero impacto del 10 % en el rendimiento cuando se habilitó SMB multicanal. Esto podría mitigarse mediante la distribución de la carga por varios archivos, o la deshabilitación de la característica.
- El rendimiento sigue estando sujeto a los [límites de un solo archivo](storage-files-scale-targets.md#file-scale-targets).

## <a name="optimizing-performance"></a>Optimización del rendimiento

Las siguientes sugerencias pueden ayudarle a optimizar el rendimiento:

- Asegúrese de que la cuenta de almacenamiento y el cliente se colocan en la misma región de Azure para reducir la latencia de red.
- Use aplicaciones multiproceso y distribuya la carga entre varios archivos.
- Las ventajas de rendimiento de SMB multicanal aumentan con el número de archivos que distribuyen la carga.
- El rendimiento del recurso compartido Premium está limitado por el tamaño del recurso compartido aprovisionado (IOPS/salida/entrada) y los límites de un solo archivo. Para obtener detalles, consulte [Descripción del aprovisionamiento de recursos compartidos de archivos Premium](understanding-billing.md#provisioned-model).
- El rendimiento máximo de un solo cliente de VM sigue estando enlazado a los límites de máquina virtual. Por ejemplo, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) puede admitir un ancho de banda máximo de 16 000 MBps (o 2 GBps), la salida de la máquina virtual (escrituras en el almacenamiento) se mide, la entrada (lecturas del almacenamiento) no. El rendimiento de los recursos compartidos de archivos está sujeto a los límites de red de la máquina, las CPU, el almacenamiento interno, el ancho de banda de red disponible, los tamaños de E/S, el paralelismo, así como otros factores.
- La prueba inicial normalmente es una preparación. Descarte sus resultados y repita la prueba.
- Si el rendimiento está limitado por un solo cliente y la carga de trabajo aún se encuentra por debajo de los límites de recursos compartidos aprovisionados, se puede conseguir un rendimiento más alto mediante la distribución de la carga por varios clientes.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>La relación entre IOPS, el rendimiento y los tamaños de E/S

**Rendimiento = tamaño de E/S * IOPS**

Los tamaños de E/S superiores aumentan el rendimiento y tendrán latencias más altas, lo que dará lugar a un número de IOPS de red inferior. Los tamaños de E/S inferiores aumentan las IOPS, pero esto da lugar a un rendimiento y a latencias de red inferiores.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar SMB multicanal en una cuenta de FileStorage (versión preliminar)](storage-files-enable-smb-multichannel.md)
- Consulte la [documentación de Windows](/azure-stack/hci/manage/manage-smb-multichannel) para obtener más información sobre SMB multicanal.
