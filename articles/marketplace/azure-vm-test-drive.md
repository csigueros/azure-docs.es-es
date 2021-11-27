---
title: Configuración de una versión de prueba de máquina virtual
description: Configure una versión de prueba de máquina virtual en el Centro de partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2021
ms.openlocfilehash: 74f9017829789f073f8efc5850755e611eb4b788
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725460"
---
# <a name="configure-a-vm-test-drive"></a>Configuración de una versión de prueba de máquina virtual

Una versión de prueba que permite a los clientes probar su oferta antes de realizar la compra al darles acceso a un entorno preconfigurado durante un número fijo de horas, lo que dará lugar a clientes potenciales altamente cualificados y una mayor tasa de conversión.

Para las ofertas de máquina virtual, la implementación de Azure Resource Manager (ARM) es la **única** opción de versión de prueba disponible. La plantilla de implementación debe contener todos los recursos de Azure que componen la solución.

Para ver la pestaña **Versión de prueba** en el menú de navegación izquierdo, active la casilla **Versión de prueba** de la página [Configuración de la oferta](azure-vm-offer-setup.md#test-drive-optional) y conéctese al sistema CRM. Después de seleccionar **Guardar**, aparece la pestaña **Unidad de prueba** con dos subpestañas:

- **[Configuración técnica](#technical-configuration)** : configure la unidad de prueba y proporcione la plantilla de ARM (sección siguiente).
- **[Lista de Marketplace](#marketplace-listing)** : proporcione detalles adicionales de su anuncio y recursos complementarios para los clientes, como manuales de usuario y vídeos.

## <a name="technical-configuration"></a>Configuración técnica

### <a name="regions"></a>Regions

Seleccione **Editar regiones** y active la casilla para cada región en la que desee que la versión de prueba esté disponible. O bien, en la parte superior derecha, use **Seleccionar todo** o **Anular selección** de los vínculos según corresponda. Para obtener el mejor rendimiento, elija solo las regiones en las que espera que se encuentra el mayor número de clientes y asegúrese de que su suscripción puede implementar todos los recursos necesarios allí. Al acabar de seleccionar las regiones, seleccione **Guardar**.

### <a name="instances"></a>Instancias

Escriba valores entre 0 y 99 en los cuadros para indicar cuántas instancias de nivel de acceso frecuente, intermedio o esporádico desea que estén disponibles por región. El número de cada tipo de instancia que especifique se multiplicará por el número de regiones en las que la oferta está disponible.

- **Acceso frecuente**: instancias implementadas previamente que siempre están en ejecución y listas para que los clientes accedan al instante (tiempo de adquisición inferior a 10 segundos) en lugar de tener que esperar una implementación. Dado que la mayoría de los clientes no quieren esperar una implementación completa, se recomienda tener al menos una instancia de acceso frecuente; de lo contrario, puede experimentar un uso reducido del cliente. Dado que las instancias de acceso frecuente siempre se ejecutan en su suscripción de Azure, incurren en un costo de tiempo de actividad mayor.
- **Acceso intermedio**: instancias implementadas previamente que se colocan en almacenamiento. Menos costosas que las instancias de acceso frecuente a la vez que se sigue reiniciando rápidamente para los clientes (tiempo de adquisición de 3 a 10 minutos).
- **Acceso esporádico**: instancias que requieren que la plantilla de ARM de la versión de prueba se implemente cuando lo solicite cada cliente. Las instancias de acceso esporádico son mucho más lentas de cargar con respecto a las instancias de acceso frecuente e intermedio. El tiempo de espera varía considerablemente en función de los recursos necesarios (hasta 1,5 horas). Las instancias de acceso esporádico son más rentables, ya que el costo es solo para la duración de la versión de prueba, en comparación con la ejecución siempre en la suscripción de Azure como con una instancia de acceso frecuente.

### <a name="technical-configuration-of-arm-template"></a>Configuración técnica de la plantilla de ARM

La plantilla de ARM para la versión de prueba es un contenedor codificado de todos los recursos de Azure que componen la solución. Para crear la plantilla de implementación de ARM que necesitará para la versión de prueba, consulte [Versión de prueba de Azure Resource Manager](azure-resource-manager-test-drive.md#write-the-test-drive-template). Una vez completada la plantilla, vuelva aquí para aprender a cargar la plantilla de ARM y completar la configuración.

Para publicar correctamente, es importante validar el formato de la plantilla de Resource Manager. Dos maneras de hacerlo son mediante una [herramienta de API en línea](/rest/api/resources/deployments/validate) o con una [implementación de pruebas](/azure/azure-resource-manager/templates/deploy-portal). Una vez que esté listo para cargar la plantilla, arrastre el archivo .zip al área indicada o **busque** el archivo.

Escriba una **duración de la versión de prueba**, en horas. Este es el número de horas que la versión de prueba permanecerá activa. La versión de prueba termina automáticamente al finalizar este período.

### <a name="deployment-subscription-details"></a>Detalles de suscripción de implementación

Para que Microsoft implemente la versión de prueba en su nombre, conéctese a su suscripción de Azure y Azure Active Directory (AAD) completando los pasos siguientes y, a continuación, seleccione **Guardar borrador**.

1. **Identificador de suscripción de Azure**: concede acceso a servicios de Azure y a Azure Portal. En la suscripción es donde se notifica la utilización de recursos y se facturan los servicios. Considere la posibilidad de [crear una suscripción de Azure independiente](/azure/cost-management-billing/manage/create-subscription) que se use para las versiones de prueba, en caso de que no se tenga ninguna. Para encontrar el identificador de suscripción de Azure, inicie sesión en Azure Portal y busque *Suscripciones* en la barra de búsqueda.
2. **Id. de inquilino de Azure AD**: para escribir el id. de inquilino de Azure Active Directory (AD), vaya a **Azure Active Directory** > **Propiedades** > **Id. de directorio** en Azure Portal. Si no tiene un identificador de inquilino, cree uno en Azure Active Directory. Para obtener ayuda con la configuración de un inquilino, vea [Inicio rápido: Configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant?branch=main).
3. Antes de continuar con los demás campos, aprovisione la aplicación de versión de prueba de Microsoft en el inquilino. Esta aplicación se va a usar para realizar operaciones en los recursos de la versión de prueba.
    1. Si aún no lo tiene, instale el [módulo de PowerShell Azure Az](/powershell/azure/install-az-ps?branch=main&view=azps-6.6.0).
    2. Agregue la entidad de servicio de la aplicación Microsoft Test-Drive.
        1. Ejecute `Connect-AzAccount` y proporcione credenciales para iniciar sesión en la cuenta de Azure, que requiere el [rol integrado](/azure/active-directory/roles/permissions-reference?branch=main) **Administrador global** de Azure Active Directory.
        2. Cree una nueva entidad de servicio: `New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment`.
        3. Asegúrese de que se ha creado la entidad de servicio: `Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'`.
            :::image type="content" source="media/test-drive/commands-to-verify-service-principal.png" alt-text="Muestra cómo asegurarse de que se ha creado la entidad de seguridad.":::
1. **Id. de aplicación de Azure AD**: después de aprovisionar la aplicación de versión de prueba de Microsoft al inquilino, pegue este identificador de aplicación: `d7e39695-0b24-441c-a140-047800a05ede`.  
1. **Secreto de cliente de aplicación de Azure AD** no se requiere ningún secreto. Inserte un secreto ficticio, como "no-secret".
1. Dado que vamos a usar la aplicación para implementar en la suscripción, es necesario agregar la aplicación como colaborador en la suscripción. Para ello, puede usar Azure Portal o PowerShell:

    **Método 1: Azure Portal**

    1. Seleccione la Suscripción que se va a usar para la versión de prueba.
    2. Seleccione **Access Control (IAM)** .
    3. Seleccione la pestaña **Asignaciones de roles** en la ventana principal y, a continuación, **+Agregar** y seleccione **+ Agregar asignación de roles** en el menú desplegable.
    4. Escriba este nombre de aplicación de Azure AD: `Microsoft TestDrive`. Seleccione la aplicación a la que desea asignar el rol **Colaborador**.
    5. Seleccione **Guardar**.

    **Método 2: PowerShell**

    1. Ejecute esto para obtener el identificador de objeto de entidad de servicio: `(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id`.
    2. Ejecute esto con el identificador de objeto y el identificador de suscripción: `New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>`.

Complete la solución de versión de prueba. Para ello, continúe con la siguiente pestaña **Versión de prueba** en el menú de navegación izquierdo, **lista de Marketplace**.

## <a name="marketplace-listing"></a>Lista de Marketplace

Proporcione detalles adicionales de la lista y los recursos para los clientes.

**Descripción**: describa la unidad de prueba, lo que se mostrará, las características que se van a explorar, los objetivos con los que va a experimentar el usuario y otra información pertinente para ayudarle a determinar si la oferta es adecuada para ellos (hasta 3000 caracteres).

**Información de acceso**: consulte un escenario para conocer exactamente lo que el cliente necesita saber para acceder a las características y usarlas en toda la unidad de prueba (hasta 10 000 caracteres).

**Manual de usuario**: describa detalladamente la experiencia de la versión de prueba. El manual debe abarcar exactamente lo que desee que el cliente aprenda al usar la versión de prueba y puede servir como referencia para las preguntas que puedan surgirle. Debe estar en formato PDF con un nombre de menos de 255 caracteres.

**Vídeo de demostración de la versión de prueba** (opcional): haga referencia a un vídeo hospedado en otro lugar con un vínculo y una imagen en miniatura. Los vídeos ayudan a los clientes a comprender mejor la versión de prueba, incluida la forma de usar correctamente las características de la oferta y de descubrir los escenarios que destacan sus ventajas. Seleccione **Agregar vídeo** e incluya la siguiente información:

- **Nombre**
- **Dirección de URL**: solo YouTube o Vimeo.
- **Miniatura**: la imagen debe estar en formato PNG y tener 533 x 324 píxeles.

Seleccione **Guardar borrador** antes de pasar a la sección **Pasos siguientes**.

## <a name="next-steps"></a>Pasos siguientes

- [Revisar y publicar la oferta](review-publish-offer.md).
