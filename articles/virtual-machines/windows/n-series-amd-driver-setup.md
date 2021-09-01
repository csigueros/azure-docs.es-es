---
title: Instalación de controladores de GPU de AMD de la serie N de Azure para Windows
description: Instalación de controladores de GPU de NVIDIA para máquinas virtuales de la serie N que se ejecutan en Windows Server o Windows en Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: ec4f09249bda14eb29d7dc9704a6096848a78557
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122194987"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalación de controladores de GPU de AMD en máquinas virtuales de la serie N con Windows

Para aprovechar las funcionalidades de GPU de las nuevas máquinas virtuales de la serie NVv4 de Azure que ejecutan Windows, deben instalarse controladores de GPU de AMD. La [extensión de controladores de GPU de AMD](../extensions/hpccompute-amd-gpu-windows.md) instala los controladores de GPU de AMD en una VM de la serie NVv4. Instale o administre la extensión mediante Azure Portal o con herramientas como las plantillas de Azure PowerShell o Azure Resource Manager. Consulte la [documentación de la extensión de controladores de GPU de AMD](../extensions/hpccompute-amd-gpu-windows.md) para conocer los sistemas operativos compatibles y los pasos de implementación.

Si decide instalar manualmente los controladores de GPU de AMD, este artículo proporciona pasos de instalación y verificación, controladores y los sistemas operativos compatibles.

Solo los controladores de GPU publicados por Microsoft se admiten en VM NVv4. NO instale controladores de GPU desde ningún otro origen.

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaño de máquinas virtuales para GPU Windows](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos y controladores compatibles

| SO | Controlador |
| -------- |------------- |
| Windows 10: compilación 2009, 2004, 1909 <br/><br/>Sesión múltiple de Windows 10 Enterprise: compilación 2009, 2004, 1909 <br/><br/>Windows Server 2016 (versión 1607)<br/><br/>Windows Server 2019 (versión 1909) | [21.Q2](https://download.microsoft.com/download/3/4/8/3481cf8d-1706-49b0-aa09-08c9468305ab/AMD-Azure-NVv4-Windows-Driver-21Q2.exe) (.exe) |

La versión anterior del controlador compatible en compilaciones de Windows hasta 1909 es [20.Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (.exe)

 > [!NOTE]
   >  Si usa la compilación 1903/1909, puede que tenga que actualizar la siguiente directiva de grupo para conseguir el rendimiento óptimo. Estos cambios no son necesarios en ninguna otra compilación de Windows.
   >  
   >  [Configuración del equipo->Directivas->Configuración de Windows->Plantillas administrativas->Componentes de Windows->Servicios de Escritorio remoto->Host de sesión de Escritorio remoto->Entorno de sesión remota], establezca la directiva [Use WDDM graphics display driver for Remote Desktop Connections] (Usar controlador de gráficos WDDM para conexiones de Escritorio remoto) como deshabilitada.
   >  

 
## <a name="driver-installation"></a>Instalación del controlador

1. Conéctese mediante Escritorio remoto a cada máquina virtual de la serie NVv4.

2. Si necesita desinstalar la versión anterior del controlador, descargue la [utilidad de limpieza de AMD ](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). No use la utilidad que se incluye con la versión anterior del controlador.

3. Descargue e instale el controlador más reciente.

4. Reinicie la máquina virtual.

## <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador

Puede comprobar la instalación del controlador en el Administrador de dispositivos. En el ejemplo siguiente se muestra la configuración correcta de la tarjeta Radeon Instinct MI25 en una máquina virtual NVv4 de Azure.
<br />

![Captura de pantalla en la que se muestra la configuración correcta de la tarjeta Radeon Instinct MI25 en una máquina virtual NVv4 de Azure.](./media/n-series-amd-driver-setup/device-manager.png)

Puede usar dxdiag para comprobar las propiedades de presentación de GPU, incluida la RAM de vídeo. En el ejemplo siguiente, se muestra la partición de 1/2 de la tarjeta Radeon Instinct MI25 en una máquina virtual NVv4 de Azure.
<br />
![Captura de pantalla en la que se muestra una partición 1/2 de la tarjeta Radeon Instinct MI25 en una máquina virtual NVv4 de Azure.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Si ejecuta la compilación 1903 de Windows 10 o posterior, dxdiag no mostrará ninguna información en la pestaña "Mostrar". Use la opción "Guardar la información" de la parte inferior, y el archivo de salida mostrará la información relacionada con la GPU MI25 de AMD.

![Propiedades del controlador de GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
