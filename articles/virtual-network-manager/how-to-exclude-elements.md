---
title: Exclusión de elementos de la pertenencia dinámica en Azure Virtual Network Manager (versión preliminar)
description: Aprenda a excluir elementos de la pertenencia dinámica en Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 318a2eba29ee7641219e8c970c3dd3a51407ddf3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092899"
---
# <a name="excluding-elements-from-dynamic-membership-in-azure-virtual-network-manager-preview"></a>Exclusión de elementos de la pertenencia dinámica en Azure Virtual Network Manager (versión preliminar)

En este artículo, aprenderá a usar instrucciones condicionales para excluir las redes virtuales de la pertenencia dinámica. Estas instrucciones condicionales se crean mediante el editor básico al seleccionar parámetros y operadores en un menú desplegable. También aprenderá a usar el editor avanzado para actualizar las instrucciones condicionales de un grupo de red existente.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="parameters-and-operators"></a><a name="parameters"></a> Parámetros y operadores

Las redes virtuales con pertenencias dinámicas se seleccionan mediante instrucciones condicionales. Puede definir más de una instrucción condicional mediante *operadores lógicos* como **AND** y **OR** para escenarios en los que necesite restringir aún más las redes virtuales seleccionadas. 

Lista de parámetros admitidos:

| Parámetros | Campo de editor avanzado |
| ---------- | ------------------------- |
| Nombre | `Name` |
| id | `Id` |
| Etiquetas| `tag['tagName']` |
| Nombre de suscripción | `[subscription().Name]` |
| Id. de suscripción | `[subscription().Id]` |
| Etiquetas de suscripción | `[subscription().tags['tagName']]` |
| Nombre del grupo de recursos | `[resourceGroup().Name]` |
| Id. de grupo de recursos | `[resourceGroup().Id]` |
| Etiquetas de grupo de recursos | `[resourceGroup().tags['tagName']]` |

Lista de operadores admitidos:

| Operadores | Editor avanzado |
| --------- | --------------- |
| Contains | `"contains": <>` |
| No contiene | `"notcontains": <>` |
| En | `"in": <>` |
| No en el | `"notin": <>` |
| Equals | `"equals": <>` |
| No es igual que | `"notequals": <>` |
| Contiene cualquiera de estos elementos | `"contains": <>` |
| Contiene todos estos elementos | `"contains": <>` |
| No contiene ninguno de estos elementos | `"notcontains": <>` |
| Exists | `"exists": true` |
| No existe | `"exists": false` |

> [!NOTE]
> Los operadores *Existe* y *No existe* solo se usan con el parámetro **Etiquetas**.

## <a name="basic-editor"></a>Editor básico

Suponga que tiene las siguientes redes virtuales en su suscripción. Cada red virtual tiene asociada una etiqueta *Producción* o *Prueba*. Es recomendable seleccionar solo las redes virtuales con la etiqueta Producción que contengan **VNet-A** en el nombre.

* VNet-A-EastUS: *Producción*
* VNet-A-WestUS: *Producción*
* VNet-B-WestUS: *Prueba*
* VNet-C-WestUS: *Prueba*
* VNetA: *Producción*
* VNetB: *Prueba*

Para empezar a usar el editor básico para crear la instrucción condicional, debe crear un nuevo grupo de red.

1. Vaya a la instancia de Azure Virtual Network Manager y seleccione **Grupos de red** en *Configuración*. Luego, seleccione **+ Agregar** para crear un grupo de redes nuevo.

1. Escriba un **Nombre** para el grupo de red. A continuación, seleccione la pestaña **Conditional statements** (Instrucciones condicionales).

1. Seleccione **Etiquetas** en la lista desplegable bajo *Parámetro* y, a continuación, seleccione **Existe** en la lista desplegable *Operador*.

1. Escriba **Prod** en *Condición* y, a continuación, seleccione **Evaluar**. Solo debería ver VNet-A-EastUS, VNet-B-WestUS y VNetA en la lista.

1. Seleccione el operador lógico **AND** para agregar otra instrucción condicional. Seleccione **Nombre** como *Parámetro* y **Contiene** para el *Operador*. Escriba **VNet-A** en el campo *Condición*. Seleccione **Evaluar** para ver qué red virtual aparece en la lista. Solo debería ver VNet-A-EastUS y VNet-A-WestUS.

1. Seleccione **Revisar y crear** y, luego, seleccione **Crear** una vez superada la validación.

> [!NOTE] 
> El **editor básico** solo está disponible durante la creación de un grupo de redes. 

## <a name="advanced-editor"></a>Editor avanzado

El editor avanzado se puede usar para seleccionar una red virtual durante la creación de un grupo de redes o al actualizar un grupo de redes existente. 

1. Seleccione el grupo de redes creado en la sección anterior. A continuación, seleccione la pestaña **Conditional statements** (Instrucciones condicionales).

1. Verá las instrucciones condicionales para el grupo de redes en la vista del editor avanzado como se muestra a continuación:

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          }
       ]
    }
    ```

    El parámetro `"allOf"` contiene ambas instrucciones condicionales, separadas por el operador lógico **AND**.

1. Para agregar otra instrucción condicional para un campo *Nombre* que *no contenga* **WestUS**, escriba lo siguiente en el editor avanzado:

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "notcontains": "WestUS"
          }
       ]
    }
    ```

1. A continuación, seleccione **Evaluar**. Solo debería ver la red virtual VNet-A-EastUS en la lista.

1. Seleccione **Revisar y guardar** y, luego, seleccione **Guardar** una vez superada la validación.

Consulte [Parámetros y operadores](#parameters) para obtener la lista completa de parámetros y operadores que puede usar con el editor avanzado. Consulte más ejemplos a continuación:

## <a name="more-examples"></a>Más ejemplos

### <a name="example-1-or-operator-only"></a>Ejemplo 1: solo operador OR

En este ejemplo se usa el operador lógico **OR** para separar dos instrucciones condicionales.

* Editor básico:

    :::image type="content" source="./media/how-to-exclude-elements/or-operator.png" alt-text="Captura de pantalla de la instrucción condicional del grupo de redes mediante el operador lógico OR.":::

* Operador avanzado:

    ```json
    {
       "anyOf": [
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "contains": "VNetA"
          }
       ]
    }
    ```

El parámetro `"anyOf"` contiene ambas instrucciones condicionales, separadas por el operador lógico **OR**.

### <a name="example-2-and-and-or-operator-at-the-same-time"></a>Ejemplo 2: operador AND y OR al mismo tiempo

* Editor básico:

    :::image type="content" source="./media/how-to-exclude-elements/both-operator.png" alt-text="Captura de pantalla de la instrucción condicional del grupo con los operadores lógicos OR y AND.":::

* Editor avanzado:

```json
{
   "allOf": [
      {
         "anyOf": [
            {
               "field": "Name",
               "contains": "VNet-A"
            },
            {
               "field": "Name",
               "contains": "VNetA"
            }
         ]
      },
      {
         "field": "Name",
         "notcontains": "West"
      }
   ]
}
```

En el código, se usa tanto `"allOf"` como `"anyOf"`. Puesto que el operador **AND** es el último de la lista, se encuentra en la parte externa del código que contiene las dos instrucciones condicionales con el operador **OR**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los [grupos de redes](concept-network-groups.md).
- Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
