---
title: Uso del generador de expresiones con Azure AD Connect Cloud Sync
description: En este artículo se explica cómo usar el Generador de expresiones con la sincronización en la nube.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef8a039b61a0dd787c65ec66a3acb48a09bc1b2
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506590"
---
# <a name="expression-builder-with-cloud-sync"></a>Generador de expresiones con sincronización en la nube
El generador de expresiones es una nueva función de Azure que se encuentra en Cloud Sync, y le ayuda a crear expresiones complejas. Puede usarlo para probar estas expresiones antes de aplicarlas al entorno de Cloud Sync.

## <a name="use-the-expression-builder"></a>Empleo del Generador de expresiones
Para acceder al generador de expresiones:

 1. En Azure Portal, seleccione **Azure Active Directory**.
 1. Seleccione **Azure AD Connect**.
 1. Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
 1. En **Configuración**, seleccione su configuración.
 1. En **Administrar atributos**, seleccione **Haga clic para editar las asignaciones**.
 1. En el panel **Edit attribute mappings** (Editar asignaciones de atributos), haga clic en **Agregar asignación de atributos**.
 1. En **Tipo de asignación**, seleccione **Expresión**.
 1. Seleccione **Probar Generador de expresiones (versión preliminar)** .
 
    ![Captura de pantalla que muestra el uso del generador de expresiones.](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>Compilación de una expresión
En esta sección, usará la lista desplegable para seleccionar entre las funciones admitidas. Luego, rellenará más cuadros, según la función seleccionada. Después de seleccionar **Apply expression** (Aplicar expresión), la sintaxis aparece en el cuadro **Expression input** (Entrada de la expresión).

Por ejemplo, al seleccionar **Reemplazar** en la lista desplegable, se proporcionan más cuadros. La sintaxis de la función se muestra en el cuadro azul claro. Los cuadros que se muestran corresponden a la sintaxis de la función seleccionada. Replace funciona de forma diferente según los parámetros proporcionados.

En este ejemplo, cuando se proporcionen **oldValue** y **replacementValue**, todos los casos de **oldValue** se reemplazaran en el origen por **replacementValue**.

Para más información, consulte [Replace](reference-expressions.md#replace).

Lo primero que se debe hacer es seleccionar el atributo que constituye el origen de la función Replace. En este ejemplo, se selecciona el atributo **mail**.

A continuación, busque el cuadro del valor **oldValue** y escriba **@fabrikam.com** . Por último, el cuadro **replacementValue** se rellena con el valor **@contoso.com** .

La expresión básicamente indica: reemplazar el atributo mail de los objetos user que tengan un valor de @fabrikam.com por el valor @contoso.com. Al seleccionar **Agregar expresión**, puede ver la sintaxis en el cuadro **Expression input** (Entrada de la expresión).

>[!NOTE]
>Asegúrese de colocar los valores en los cuadros que corresponderían a **oldValue** y **replacementValue** en función de la sintaxis que se genera cuando se ha seleccionado **Replace**.

Para más información sobre las expresiones admitidas, consulte [Escritura de expresiones para la asignación de atributos en Azure Active Directory](reference-expressions.md).

### <a name="information-on-expression-builder-input-boxes"></a>Información sobre los cuadros de entrada del Generador de expresiones
Los cuadros proporcionados por el generador de expresiones aceptan varios valores según la función que se haya seleccionado. Por ejemplo, la función JOIN acepta cadenas o el valor asociado a un atributo determinado. Por ejemplo, se puede usar el valor del atributo **[givenName]** y combinarlo con un valor de cadena **@contoso.com** para crear una dirección de correo electrónico.

  ![Captura de pantalla que muestra los valores del cuadro de entrada.](media/how-to-expression-builder/expression-8.png)

Para obtener más información sobre los valores aceptables y sobre cómo escribir expresiones, vea [Escritura de expresiones para la asignación de atributos en Azure Active Directory](reference-expressions.md).

## <a name="test-an-expression"></a>Prueba de una expresión
En esta sección se pueden probar las expresiones. En la lista desplegable, seleccione el atributo **mail**. Rellene el valor con **@fabrikam.com** y seleccione **Prueba expresión**.

El valor **@contoso.com** aparece en el cuadro **View expression output** (Ver salida de la expresión).

 ![Captura de pantalla que muestra la prueba de la expresión.](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>Implementación de la expresión
Cuando esté satisfecho con la expresión, seleccione **Apply expression** (Aplicar expresión).

![Captura de pantalla que muestra cómo agregar la expresión.](media/how-to-expression-builder/expression-5.png)

Esta acción agrega la expresión a la configuración del agente.

![Captura de pantalla que muestra la configuración del agente.](media/how-to-expression-builder/expression-6.png)

## <a name="set-a-null-value-on-an-expression"></a>Establecimiento de un valor NULL en una expresión
Para establecer el valor de un atributo en NULL, use una expresión con el valor de `""`. Esta expresión envía el valor NULL al atributo de destino.

![Captura de pantalla que muestra un valor NULL.](media/how-to-expression-builder/expression-7.png)


## <a name="next-steps"></a>Pasos siguientes 

- [Escritura de expresiones para la asignación de atributos en Azure Active Directory](reference-expressions.md)
- [Configuración de la sincronización en la nube](how-to-configure.md)
