---
title: Actualización de Azure Load Balancer de público básico a público estándar
description: En este artículo se muestra cómo actualizar Azure Public Load Balancer de una SKU básica a una estándar.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 98a74903f5472807ff7eb562ea9db9e12ceeb4be
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108700"
---
# <a name="upgrade-azure-public-load-balancer"></a>Actualización de Azure Load Balancer público
[Azure Standard Load Balancer](load-balancer-overview.md) ofrece un amplio conjunto de funcionalidades y alta disponibilidad gracias a la redundancia de zona. Para más información acerca de la SKU de Load Balancer, consulte la [tabla de comparación](./skus.md#skus).

Hay dos fases en una actualización:

1. Cambiar el método de asignación de direcciones IP de dinámico a estático.
2. Ejecutar el script de PowerShell para completar la actualización y la actualización del tráfico.

## <a name="upgrade-overview"></a>Información general sobre la actualización

Existe un script de Azure PowerShell que hace lo siguiente:

* Crea una SKU estándar de Load Balancer con la ubicación que especifique en el mismo grupo de recursos de la instancia de Standard Load Balancer básica.
* Actualiza la dirección IP pública de una SKU de nivel básico a una SKU estándar localmente.
* Copia perfectamente las configuraciones de Load Balancer de SKU básica en la instancia de Standard Load Balancer recién creada.
* Crea una regla de salida predeterminada que permite la conectividad saliente.

### <a name="caveatslimitations"></a>Advertencias y limitaciones

* El script solo admite la actualización de Public Load Balancer. En el caso de la actualización interna de la instancia básica de Load Balancer, puede encontrar instrucciones en [esta página](./upgrade-basicinternal-standard.md).
* El método de asignación de la dirección IP pública debe cambiarse a "estático" antes de ejecutar el script. 
* Si Load Balancer no tiene ninguna configuración de IP de front-end ni grupo de back-end, es probable que se produzca un error al ejecutar el script. Asegúrese de que no están vacíos

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Cambio del método de asignación de dirección IP pública a estático

* **Estos son los pasos que recomendados:

    1. Para realizar las tareas de esta guía de inicio rápido, inicie sesión en [Azure Portal](https://portal.azure.com).
 
    1. Seleccione **Todos los recursos** en el menú de la izquierda y luego seleccione la **Basic Public IP Address associated with Basic Load Balancer** (Dirección IP pública básica asociada con Basic Load Balancer) en la lista de recursos.
   
    1. En **Settings** (Configuración), seleccione **Configurations** (Configuraciones).
   
    1. En **Asignación**, seleccione **Estática**.
    1. Seleccione **Guardar**.
    >[!NOTE]
    >En el caso de las máquinas virtuales que tienen direcciones IP públicas, deberá crear direcciones IP estándar primero en aquellos casos en los que no se garantice la misma dirección IP. Desasocie las máquinas virtuales de las direcciones IP básicas y asócielas con las direcciones IP estándar recién creadas. A continuación, podrá seguir las instrucciones para agregar máquinas virtuales al grupo de back-end de Standard Load Balancer. 

* **Creación de nuevas máquinas virtuales para agregarlas a los grupos de back-end de la instancia de Standard Public Load Balancer recién creada**.
    * [Aquí](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines) encontrará más instrucciones sobre cómo crear una máquina virtual y cómo asociarla con Standard Load Balancer.


## <a name="download-the-script"></a>Descarga del script

Descargue el script de migración de la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/5.0).
## <a name="use-the-script"></a>Uso del script

Dispone de dos opciones en función de sus preferencias y de la configuración del entorno de PowerShell local:

* Si no tiene instalados los módulos de Azure Az, o si no le importa desinstalarlos, la mejor alternativa es usar la opción `Install-Script` para ejecutar el script.
* Si necesita conservar los módulos de Azure Az, lo mejor es que descargue el script y lo ejecute directamente.

Para determinar si tiene instalados los módulos de Azure Az, ejecute `Get-InstalledModule -Name az`. Si no ve ningún módulo de Az instalado, puede usar el método `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Instalación con el método Install-Script

Para usar esta opción, los módulos de Azure Az no deben estar instalados en el equipo. En caso de que lo estén, el comando siguiente mostrará un error. Puede desinstalar los módulos de Azure Az o usar la otra opción para descargar manualmente el script y ejecutarlo.
  
Ejecute el script con el siguiente comando:

`Install-Script -Name AzurePublicLBUpgrade`

Este comando también instala los módulos de Az necesarios.  

### <a name="install-using-the-script-directly"></a>Instalación directamente con el script

Si tiene instalados algunos módulos de Azure Az y no puede desinstalarlos (o no le interesa hacerlo), puede descargar manualmente el script mediante la pestaña **Descarga manual** en el vínculo de descarga del script. El script se descarga como un archivo nupkg sin procesar. Para instalar el script desde este archivo nupkg, consulte [Descarga manual del paquete](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para ejecutar el script:

1. Use `Connect-AzAccount` para conectarse a Azure.

1. Use `Import-Module Az` para importar los módulos de Az.

1. Examine los parámetros obligatorios:

   * **oldRgName: [String]: Required**: es el grupo de recursos de la instancia existente de Basic Load Balancer que desea actualizar. Para encontrar este valor de cadena, vaya a Azure Portal, seleccione el origen Basic Load Balancer y haga clic en la sección **Información general** del equilibrador de carga. El grupo de recursos se encuentra en esa página.
   * **oldLBName: [String]: Required**: es el nombre de la instancia de Basic Load Balancer que desea actualizar. 
   * **newLBName: [String]: Required**: es el nombre de la instancia de Standard Load Balancer que se va a crear.
1. Ejecute el script con los parámetros adecuados. Podría tardar entre cinco y siete minutos en finalizar.

    **Ejemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Creación de una regla de salida para la conexión de salida

Siga las [instrucciones ](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) para crear una regla de salida para poder:
* Definir la NAT de salida desde cero.
* Escalar y ajustar el comportamiento de la NAT de salida existente.

## <a name="common-questions"></a>Preguntas frecuentes

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>¿Hay alguna limitación en el script de Azure PowerShell para migrar la configuración de v1 a v2?

Sí. Consulte [Advertencias y limitaciones](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>¿Cuánto tarda la actualización?

Normalmente, el script tarda entre 5 y 10 minutos en finalizar y podría tardar más en función de la complejidad de la configuración de Load Balancer. Por lo tanto, debe tener en cuenta el tiempo de inactividad y planear la conmutación por error si es necesario.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>¿Puede el script de Azure PowerShell cambiar el tráfico de la instancia de Basic Load Balancer a la instancia de Standard Load Balancer recién creada?

Sí. El script de Azure PowerShell no solo actualiza la dirección IP pública y copia la configuración de Basic Load Balancer a Standard Load Balancer, sino que también migra la máquina virtual enviándola detrás de la instancia pública de Standard Load Balancer recién creada. 

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre Load Balancer estándar](load-balancer-overview.md)
