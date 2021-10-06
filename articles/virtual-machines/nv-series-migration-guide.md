---
title: Guía de migración de la serie NV
description: Guía de migración de la serie NV
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3f37a9c14a66156b908eb1d046f98301add07c20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562390"
---
# <a name="nv-series-migration-guide"></a>Guía de migración de la serie NV

A medida que haya tamaños de máquina virtual de GPU más eficaces disponibles en los centros de datos de Azure, debe evaluar las cargas de trabajo y migrar las máquinas virtuales (VM) de la series NV y NV_Promo. Estas máquinas virtuales heredadas se pueden migrar a nuevas series de máquinas virtuales, como NVsv3 y NVasv4, para mejorar el rendimiento con un costo reducido. La serie de máquinas virtuales NVsv3 tiene tecnología de GPU Nvidia M60. La serie NVasv4 cuenta con la tecnología de GPU AMD Radeon Instinct MI25.

Las principales diferencias entre las series NV y NV_Promo, y las series NVsv3 y NVasv4 más recientes son las siguientes:
- Rendimiento mejorado.
- Soporte técnico para Premium Storage.
- La opción para elegir entre configuraciones de tamaño fraccionario de GPU y configuraciones de varias GPU.

Las series NVsv3 y NVasv4 tienen núcleos más modernos y una mayor capacidad.

En la sección siguiente se resumen las diferencias entre la serie NV heredada y las series NVsv3 y NVv4.
 
 ## <a name="nvsv3-series"></a>Serie NVsv3 

Las máquinas virtuales de la serie NVv3 cuentan con la tecnología de las GPU de NVIDIA Tesla M60, la tecnología GRID de NVIDIA con CPU Intel E5-2690 v4 (Broadwell) y la tecnología Intel Hyper-Threading. Estas máquinas virtuales están orientadas a escritorios virtuales y aplicaciones gráficas aceleradas mediante GPU donde los clientes quieren:
- Visualizar sus datos.
- Simular resultados para verlos.
- Trabajar en CAD.
- Representar y transmitir contenido. 

Estas máquinas virtuales también pueden ejecutar cargas de trabajo de precisión única, como las de codificación y representación.

Las máquinas virtuales NVv3 admiten Premium Storage y cuentan con el doble de memoria del sistema (RAM) que la serie NV anterior. Para obtener las especificaciones más actualizadas, vea [Tamaños de máquina virtual de proceso acelerado por GPU: serie NVsv3](nvv3-series.md).

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12 (+6) <br> GiB de memoria: 112 (+56) <br> GiB de almacenamiento temporal (SSD): 320 (-20) <br> Número máximo de discos de datos: 12 (-12) <br> Redes aceleradas: Sí <br> Premium Storage: Sí  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24 (+12) <br>GiB de memoria: 224 (+112) <br>GiB de almacenamiento temporal (SSD): 640 (-40)<br>Número máximo de discos de datos: 24 (-24)<br>Redes aceleradas: Sí <br>Premium Storage: Sí   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48 (+24) <br>GiB de memoria: 448 (+224) <br>GiB de almacenamiento temporal (SSD): 1280 (-160) <br>Número máximo de discos de datos: 32 (-32) <br>Redes aceleradas: Sí <br>Premium Storage: Sí   |

## <a name="nvv4-series"></a>Serie NVv4 

Las máquinas virtuales de la serie NVv4 usan la tecnología de las GPU de AMD Radeon Instinct MI25 y las CPU de AMD EPYC 7V12 (Rome). Con la serie NVv4, Azure presenta máquinas virtuales con GPU parciales. Elija la máquina virtual del tamaño adecuado para aplicaciones con aceleración gráfica por GPU y escritorios virtuales que empiezan con un octavo de una GPU con un búfer de cuadros de 2 GiB hasta una GPU completa con un búfer de cuadros de 16 GiB.

Las máquinas virtuales NVv4 actualmente solo admiten el sistema operativo invitado Windows. Para obtener las especificaciones más actualizadas, vea [Tamaños de máquina virtual de proceso acelerado por GPU: serie NVv4](nvv4-series.md).

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16 (+10) <br>GiB de memoria: 56  <br>GiB de almacenamiento temporal (SSD): 352 (+12) <br>Número máximo de discos de datos: 16 (-8) <br>Redes aceleradas: Sí <br>Premium Storage: Sí   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32 (+20) <br>GiB de memoria: 112 <br>GiB de almacenamiento temporal (SSD): 704 (+24) <br>Número máximo de discos de datos: 32 (+16)<br>Redes aceleradas: Sí <br>Premium Storage: Sí   |
|Standard_NV24 <br> Standard_NV24_Promo |N/D  | N/A  |

## <a name="migration-steps-for-general-changes"></a>Pasos de migración para cambios generales

Para tratar los cambios generales:

1. Elija una serie y un tamaño para la migración. 

1. Obtenga la cuota de la serie de máquinas virtuales de destino.

1. Cambie el tamaño actual de la máquina virtual de la serie NV al tamaño de destino.

  Si el tamaño de destino es NVv4, asegúrese de quitar el controlador de GPU de Nvidia e instalar el controlador de GPU de AMD.

## <a name="migration-steps-for-breaking-changes"></a>Pasos de migración para cambios importantes

Para abordar los cambios importantes, siga los pasos de las secciones siguientes.

### <a name="select-a-target-size-for-migration"></a>Selección de un tamaño de destino para la migración

Después de evaluar la utilización actual, decida qué tipo de máquina virtual de GPU necesita. En función de los requisitos de carga de trabajo, tiene algunas opciones diferentes. A continuación, se indica cómo elegir:

- Si la carga de trabajo es de gráficos o visualización, y depende en gran parte del uso de GPU de Nvidia, realice la migración a la serie NVsv3.
- Si la carga de trabajo es de gráficos o visualización, y no depende en gran parte del uso de un tipo GPU concreto, realice la migración a la serie NVsv3 o NVVasv4.
 
> [!Note]
>Un procedimiento recomendado es seleccionar un tamaño de máquina virtual en función del costo y el rendimiento. Las recomendaciones de este artículo se basan en una comparación individualizada de las métricas de rendimiento de los tamaños NV y NV_Promo, y la coincidencia más cercana en otra serie de máquinas virtuales.
>Antes de decidir el tamaño correcto, obtenga una comparación de costos mediante la calculadora de precios de Azure.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtención de la cuota de la familia de máquinas virtuales de destino 

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales](../azure-portal/supportability/per-vm-quota-requests.md). Seleccione la serie NVSv3 o NVv4 como nombre de la familia de máquinas virtuales, en función del tamaño de máquina virtual de destino que haya seleccionado para la migración.

### <a name="resize-the-current-vm"></a>Cambio de tamaño de la máquina virtual actual

Puede [cambiar el tamaño de la máquina virtual](resize-vm.md).

## <a name="faq"></a>Preguntas más frecuentes
**P:** ¿Qué controlador de GPU debo usar para el tamaño de máquina virtual de destino? 

**R:** En el caso de la serie NVsv3, use el [controlador GRID de Nvidia](./windows/n-series-driver-setup.md). Para la serie NVv4, use los [controladores de GPU de AMD](./windows/n-series-amd-driver-setup.md). 

**P:** En la actualidad uso la extensión del controlador de GPU de Nvidia. ¿Funcionará para el tamaño de máquina virtual de destino? 

**R:** La [extensión de controlador de Nvidia](./extensions/hpccompute-gpu-windows.md) actual funcionará para NVsv3. Use las [extensiones del controlador de GPU de AMD](./extensions/hpccompute-amd-gpu-windows.md) si el tamaño de la máquina virtual de destino es NVv4. 
 
**P:** ¿Qué serie de máquinas virtuales de destino debo usar si tengo dependencia de CUDA? 

 **R:** NVv3 admite CUDA. La serie de máquinas virtuales NVv4 con las GPU de AMD no admite CUDA.
