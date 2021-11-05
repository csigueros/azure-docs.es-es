---
title: Creación de suscripciones de desarrollo/pruebas de Enterprise de Azure
description: Cree suscripciones de desarrollo/pruebas de Azure para Enterprise y organizaciones para equipos y grandes organizaciones.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 5897e60a2c3c5d46dac48973d78072364af27336
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093045"
---
# <a name="creating-enterprise-and-organization-azure-devtest-subscriptions"></a>Creación de suscripciones de desarrollo/pruebas de Azure para Enterprise y organizaciones

Las suscripciones de desarrollo/pruebas de Enterprise están disponibles para el desarrollo de equipos en organizaciones grandes

- Para el desarrollo en equipo en grandes empresas  
- Facturación para toda la empresa: sin pagos separados  
- Creación de una suscripción de oferta de desarrollo/pruebas a través del EA Portal de Azure  

## <a name="prerequisites"></a>Prerrequisitos

Para crear una suscripción empresarial de desarrollo/pruebas de Azure, debe tener un rol de propietario de la cuenta en una cuenta de inscripción para crear la suscripción. Hay dos maneras de acceder a este rol:  

- El administrador del Enterprise de la inscripción puede [convertirlo en propietario de la cuenta](../../cost-management-billing/manage/grant-access-to-create-subscription.md)  
- Un propietario existente de la cuenta [puede concederle acceso](../../cost-management-billing/manage/grant-access-to-create-subscription.md).  

### <a name="important-information-before-you-add-account-owners"></a>Información importante antes de agregar propietarios de la cuenta

El propietario de una cuenta EA **no** puede usar la misma cuenta de inicio de sesión para el EA Portal y otras ofertas de Azure. Supongamos que se agregó al EA Portal como propietario de la cuenta y usó las mismas credenciales que para su cuenta Visual Studio Benefits. En ese caso, esa suscripción de Visual Studio se convertirá a la oferta desarrollo/pruebas de EA.  

> [!Note]  
> La primera vez que inicie sesión en el EA Portal como propietario de la cuenta, verá una ventana emergente de advertencia. Es esencial leer y comprender. Las suscripciones existentes se convertirán en una oferta de EA facturable. Si es un suscriptor de Visual Studio agregado como propietario de la cuenta, perderá su crédito mensual individual de Azure hasta que tome medidas adicionales.

### <a name="to-recover-your-individual-visual-studio-azure-benefits"></a>Para recuperar las ventajas individuales de Azure de Visual Studio  

Después de autenticar el rol como propietario de la cuenta de EA, puede hacer lo siguiente:  

- Quitar o trasladar las suscripciones de Azure que posee  
- Elimine el rol como propietario de la cuenta en el EA Portal  
- Regístrese para obtener de nuevo sus ventajas individuales de Azure de Visual Studio  
- O bien, elimine el suscriptor del sitio de administrador de Visual Studio en VLSC y vuelva a asignar la suscripción con un inicio de sesión diferente y, a continuación, puede registrarse para obtener una nueva ventaja de Azure de Visual Studio individual  

## <a name="create-your-subscription"></a>Creación de la suscripción

Para crear las suscripciones de desarrollo/pruebas empresariales, elija primero una cuenta de inscripción como una cuenta de desarrollo/pruebas.  

1. Para empezar, vaya a [Ea.azure.com](https://ea.azure.com). Azure Enterprise Portal es donde puede administrar las inscripciones de su empresa  
2. En la pestaña Administrar, seleccione el número de contrato  

    > [!Note]
    > La inscripción puede cambiar según el ciclo de renovación de su contrato de Enterprise.
    
    ![Una captura de pantalla de la página del portal de administración del Contrato Enterprise.](media/quickstart-create-enterprise-devtest-subscriptions/ea-manage-portal.png "Administre las inscripciones de su empresa desde Enterprise Portal de Azure.")
    
3. Una vez que haya seleccionado el número de inscripción adecuado, seleccione la pestaña "Cuenta"  
4. Desde aquí, puede editar las cuentas y alternar la marca de desarrollo/pruebas a "Sí".  

Para agregar una cuenta:  

1. En Azure Enterprise Portal, seleccione Administrar en el área de navegación de la izquierda.  
2. Seleccione la pestaña Cuenta en la página Cuenta y seleccione +Agregar cuenta.  
3. Puede elegir un departamento o dejarlo en blanco.
4. Elija el tipo de autenticación deseado.  
5. Escriba el nombre descriptivo para identificar la cuenta en los informes.  
6. Escriba la dirección de correo electrónico del propietario de la cuenta que se va a asociar a la nueva cuenta.  
7. Confirme la dirección de correo electrónico y seleccione Agregar.  

![Una captura de pantalla de la página de administración de Enterprise Portal de Azure con +Agregar cuenta seleccionado.](media/quickstart-create-enterprise-devtest-subscriptions/add-account.png "Haga clic en Agregar cuenta.")

## <a name="add-your-azure-enterprise-devtest-subscription"></a>Agregue la suscripción de desarrollo/pruebas - Enterprise de Azure

Después de elegir la cuenta para crear una suscripción empresarial de desarrollo/pruebas de Azure, agregue la oferta de suscripción adecuada. Existen dos formas. Independientemente de lo que elija, la experiencia es la misma.  

1. La pestaña de suscripción de cada portal será un botón "+/Agregar". 
    1. Irá a una página de inicio de sesión.
    1. Inicie sesión con sus credenciales de Microsoft Azure.
    1. Después de iniciar sesión, se le dirigirá a una página en la que se enumeran las ofertas disponibles. Alterne **sí** en el portal de EA para ver las ofertas empresariales.
    
1. Seleccione la oferta de desarrollo/pruebas empresariales y cree la suscripción.
    1. Debe tener acceso y permisos asociados a su identidad.
    1. Debe designar la cuenta como una cuenta de desarrollo/pruebas en el portal de inscripción.

### <a name="next-steps"></a>Pasos siguientes  

- [Administración del portal del Contrato Enterprise de Azure](../../cost-management-billing/manage/ea-portal-administration.md)
- [Introducción a Azure Enterprise Portal](../../cost-management-billing/manage/ea-portal-get-started.md)
