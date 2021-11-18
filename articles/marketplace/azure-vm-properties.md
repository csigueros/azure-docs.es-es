---
title: Configuración de las propiedades de la oferta de máquinas virtuales en Azure Marketplace
description: Configure las propiedades de la oferta de máquinas virtuales en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
msreviewer: amhindma
ms.date: 11/10/2021
ms.openlocfilehash: d83e3d6f7d773eb1294f1c9519ad03bc750ef475
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725395"
---
# <a name="configure-virtual-machine-offer-properties"></a>Configuración de las propiedades de la oferta de máquinas virtuales

En la página **Propiedades** (selecciónela en el menú de navegación de la izquierda del Centro de partners), se definen las categorías que se usan para agrupar la oferta de máquinas virtuales (VM) en Azure Marketplace y los contratos legales que respaldan la oferta.

## <a name="select-a-category"></a>Seleccionar una categoría

Seleccione las categorías y subcategorías para colocar la oferta en las áreas de búsqueda adecuadas de Marketplace. Más adelante, en la descripción de la oferta, asegúrese de indicar cómo la oferta admite estas categorías.

- Seleccione una categoría principal.
- Para agregar una segunda categoría opcional (Secundaria), seleccione el vínculo **+Categorías**.
- Seleccione hasta dos subcategorías para la categoría principal o secundaria. Use Ctrl + clic para seleccionar una segunda subcategoría. Si no hay ninguna subcategoría aplicable a la oferta, se selecciona automáticamente **No aplicable**.

Consulte la lista completa de categorías y subcategorías en [Categorías y subcategorías en Marketplace comercial](categories.md). Las ofertas de máquina virtual siempre aparecen en la categoría **Proceso** de Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Adición de términos y condiciones

En **Legal**, indique los términos y condiciones de la oferta. Tiene dos opciones:

- [Uso del Contrato estándar con modificaciones opcionales](#use-the-standard-contract)
- [Uso de términos y condiciones propios](#use-your-own-terms-and-conditions)

Para más información sobre el Contrato estándar y las modificaciones opcionales, consulte [Contrato estándar para marketplace comercial de Microsoft](standard-contract.md). Puede descargar el [Contrato estándar](https://go.microsoft.com/fwlink/?linkid=2041178) en formato PDF (asegúrese de que el bloqueador de elementos emergentes está desactivado).

### <a name="use-the-standard-contract"></a>Uso del Contrato estándar

Para simplificar el proceso de adquisición para los clientes y reducir la complejidad legal para los proveedores de software, Microsoft ofrece un Contrato estándar que se puede usar con las ofertas del marketplace comercial. Al ofrecer el software bajo el contrato estándar, los clientes solo tienen que leerlo y aceptarlo, sin necesidad de crear términos y condiciones personalizados. Para más información sobre la experiencia del cliente durante el proceso de detección y compra, consulte [Contrato estándar para el Marketplace comercial de Microsoft](standard-contract.md#customer-experience).

1. Active la casilla **Use the Standard Contract for Microsoft's commercial marketplace** (¿Quiere usar el Contrato estándar para el marketplace comercial de Microsoft?).

1. En el cuadro de diálogo **Confirmación**, seleccione **Aceptar**. En función del tamaño de la pantalla, es posible que tenga que desplazarse hacia arriba para verlo.
1. Seleccione **Guardar borrador** antes de continuar.

   > [!NOTE]
   > Después de publicar una oferta con el Contrato estándar para el marketplace comercial, no puede usar sus propios términos y condiciones personalizados. Puede ofrecer la solución con el Contrato estándar con modificaciones opcionales o con sus propios términos y condiciones.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Adición de modificaciones al Contrato estándar (opcional)

Hay dos tipos de modificaciones disponibles: *universal* y *personalizada*.

##### <a name="add-universal-amendment-terms"></a>Adición de términos de modificación universal

En la casilla **Universal amendment terms to the standard contract for Microsoft's commercial marketplace** (Términos de modificación universales del Contrato estándar de marketplace comercial de Microsoft), escriba los términos de modificación universal en esta casilla. Puede escribir un número ilimitado de caracteres en este cuadro. Estos términos se muestran a los clientes en AppSource, Azure Marketplace o Azure Portal durante el flujo de detección y compra.

##### <a name="add-one-or-more-custom-amendments"></a>Adición de una o varias modificaciones personalizadas

Estas modificaciones personalizadas son modificaciones especiales del Contrato estándar que están dirigidas a clientes específicos con los identificadores de inquilino de Azure. Solo los clientes del inquilino recibirán los términos con modificaciones personalizadas cuando compren la oferta.

> [!NOTE]
> Los clientes específicos con modificaciones personalizadas también obtendrán la modificación universal (si la hubiera) del Contrato estándar durante la compra.

1. En **Custom amendments terms to the Standard Contract for Microsoft's commercial marketplace** (Términos de modificación personalizados del Contrato estándar de Marketplace comercial de Microsoft), seleccione **Agregar término de modificación personalizado (máximo 10)** .
2. En el cuadro, escriba los **términos de modificación personalizados**.
3. En el cuadro, escriba el **identificador de inquilino**.

   > [!TIP]
   > El identificador de inquilino identifica al cliente en Azure. Puede pedir a su cliente este identificador y encontrarlo; para ello, vaya a [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **Propiedades**. El valor del identificador de directorio es el identificador de inquilino (por ejemplo, `50c464d3-4930-494c-963c-1e951d15360e`). También puede buscar el identificador de inquilino de la organización de su cliente mediante su dirección URL de nombre de dominio en [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/) (¿Cuál es mi identificador de inquilino de Microsoft Azure y Office 365?).

4. También puede escribir una **descripción** detallada del identificador de inquilino. Esta descripción le ayudará a identificar al cliente al que está destinada la modificación.
5. Para agregar otro identificador de inquilino, seleccione el vínculo **Agregar un identificador de inquilino del cliente (máx. 10)** y repita los pasos 3 y 4. Puede agregar hasta 10 identificadores de inquilino.
6. Para agregar otro término de modificación, repita los pasos 1 a 5. Puede proporcionar hasta 10 términos de modificación personalizados por oferta.
7. Seleccione **Guardar borrador** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Uso de términos y condiciones propios

También puede usar sus propios términos y condiciones en lugar del contrato estándar. Los clientes deben aceptar estos términos para poder probar la oferta.

1. En **Legal**, desactive la casilla **Use the Standard Contract for Microsoft's commercial marketplace** (¿Quiere usa el Contrato estándar para el marketplace comercial de Microsoft?).
1. En el cuadro **Términos y condiciones**, escriba hasta 10 000 caracteres de texto.

   > [!NOTE]
   > Si los términos y condiciones requieren una descripción más larga, escriba una única dirección web en la que se puedan encontrar. A los clientes les aparecerá como un vínculo activo.

1. Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña del menú de navegación izquierdo, **Descripción de la oferta**.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de un listado de ofertas de máquina virtual](azure-vm-offer-listing.md)
