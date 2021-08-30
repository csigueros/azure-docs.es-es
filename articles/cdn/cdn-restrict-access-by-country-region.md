---
title: Restricción del contenido de Azure CDN por país o región
description: Aprenda cómo restringir el acceso por país o región a su contenido de Azure CDN mediante la característica de filtrado geográfico.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 07/07/2021
ms.author: allensu
ms.openlocfilehash: 58282d9880f1140def0e941bb8fb3d17356544d3
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221604"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restricción del contenido de Azure CDN por país o región

## <a name="overview"></a>Información general
Cuando un usuario solicita su contenido, este se sirve a los usuarios de todas las ubicaciones. No obstante, se puede restringir el acceso al contenido por país o región. 

Con la característica de *filtrado geográfico*, se pueden crear reglas sobre rutas de acceso específicas en su punto de conexión de CDN. Se puede establecer que las reglas permitan o bloqueen el contenido en determinados países o regiones.

> [!IMPORTANT]
> Los perfiles **Azure CDN Estándar de Microsoft** no admiten el filtrado geográfico basado en rutas.
> 

## <a name="standard-profiles"></a>Perfiles estándar

Estas instrucciones se aplican a los perfiles de **Azure CDN Estándar de Akamai** y **Azure CDN Estándar de Verizon**.

Con los perfiles de **Azure CDN Premium de Verizon**, debe usar el **Portal de administración** para activar el filtrado geográfico. Para más información, consulte [Perfiles de Azure CDN Premium de Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definición de la ruta de acceso a directorio
Para acceder a la característica de filtrado geográfico, seleccione el punto de conexión de CDN en el portal y, luego, seleccione **Filtrado geográfico** en Configuración en el menú izquierdo. 

![Captura de pantalla que muestra el filtrado geográfico seleccionado en el menú de un punto de conexión.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

En el cuadro **Ruta de acceso**, especifique la ruta de acceso relativa a la ubicación a la que se les permitirá o denegará el acceso a los usuarios. 

Puede aplicar el filtrado geográfico a todos los archivos mediante una barra diagonal (/), o seleccionar carpetas concretas mediante la especificación de rutas de acceso a directorio (por ejemplo, */pictures/* ). También puede aplicar el filtrado geográfico a un único archivo (por ejemplo, */pictures/city.png*). Se permiten varias reglas. Después de especificar una regla, aparece una fila en blanco para que escriba la siguiente regla.

Por ejemplo, todos los filtros siguientes de ruta de acceso a directorio son válidos:   
*/*                                 
*/Photos/*      
*/Photos/Strasbourg/*      
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definición del tipo de acción

En la lista **Acción**, seleccione **Permitir** o **Bloquear**: 

- **Permitir**: solo los usuarios de los países o regiones especificados pueden acceder a los recursos solicitados desde la ruta recursiva.

- **Bloquear**: a los usuarios de los países o regiones especificados se les deniega el acceso a los recursos solicitados desde esa ruta recursiva. Si no se han configurado otras opciones de filtrado de país o región para esa ubicación, todos los demás usuarios podrán acceder al contenido.

Por ejemplo, una regla de filtrado geográfico para bloquear la ruta de acceso */Photos/Strasbourg/* filtra los siguientes archivos:     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definición de los países o regiones

En la lista **CÓDIGOS DE PAÍS O REGIÓN**, seleccione los países o regiones para los que quiere bloquear o permitir la ruta de acceso. 

Cuando haya terminado de seleccionar los países o regiones, seleccione **Guardar** para activar la nueva regla de filtrado geográfico. 

![Captura de pantalla que muestra los CÓDIGOS DE PAÍS O REGIÓN que se usan para bloquear o permitir países o regiones.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar una regla, selecciónela de la lista en la página **Filtrado geográfico** y luego elija **Eliminar**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Perfiles de Azure CDN Premium de Verizon

En los perfiles de **Azure CDN Prémium de Verizon**, la interfaz de usuario para crear una regla de filtrado geográfico es diferente:

1. En el menú superior del perfil de Azure CDN, seleccione **Administrar**.

2. En el portal de Verizon, seleccione **HTTP Large** (HTTP grande) y, luego, **Country Filtering** (Filtrado de países).

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Captura de pantalla que muestra cómo seleccionar el filtrado de países en Azure CDN" border="true":::
  
3. Seleccione **Add Country Filter** (Agregar filtro de país).

4. En el **paso uno:** , escriba la ruta de acceso del directorio. Seleccione **Bloquear** o **Agregar** y, después, seleccione **Siguiente**.

    > [!IMPORTANT]
    > El nombre del punto de conexión debe estar en la ruta de acceso.  Ejemplo: **/myendpoint8675/MyFolder**.  Reemplace **myendpoint8675** por el nombre del punto de conexión.
    > 
    
5. En el **paso dos**, seleccione uno o varios países o regiones de la lista. Seleccione **Finalizar** para activar la regla. 
    
    La nueva regla aparece en la tabla en la página **Country Filtering** (Filtrado de países).
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Captura de pantalla que muestra dónde aparece la regla en el filtrado de países." border="true":::
 
### <a name="clean-up-resources"></a>Limpieza de recursos
En la tabla de reglas de filtrado de países o regiones, seleccione el icono de eliminación situado junto a una regla para eliminarla o el icono de edición para modificarla.

## <a name="considerations"></a>Consideraciones
* Los cambios en la configuración de filtrado geográfico no surten efecto inmediatamente:
   * En los perfiles de **Azure CDN Estándar de Microsoft**, la propagación se completa normalmente en 10 minutos. 
   * En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
   * En los perfiles **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**, la propagación se completa normalmente en 10 minutos. 
 
* Esta característica no admite caracteres comodín (por ejemplo, *).

* La configuración de filtrado geográfico asociada a la ruta de acceso relativa se aplica de forma recursiva a esa ruta.

* Se puede aplicar solo una regla a la misma ruta de acceso relativa. Es decir, no se pueden crear varios filtros de países o regiones que apunten a la misma ruta de acceso relativa. Sin embargo, dado que los filtros de país o región son recursivos, una carpeta puede tener varios filtros de país o región. En otras palabras, se puede asignar un filtro de país o región diferente a una subcarpeta de una carpeta configurada previamente.

* La característica de filtrado geográfico usa códigos de país o región para definir los países o regiones desde los que se permite o bloquea una solicitud en un directorio protegido.  Los pefiles **Azure CDN de Verizon** y **Azure CDN de Akamai** usan códigos de país ISO 3166-1 alpha-2 para definir los países desde los que se permitirá o bloqueará una solicitud en un directorio protegido. 

