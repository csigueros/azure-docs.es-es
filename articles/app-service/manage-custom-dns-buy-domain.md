---
title: Compra de un nombre de dominio personalizado
description: Aprenda a comprar un dominio de App Service y úselo como un dominio personalizado para una aplicación de Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: c15c4e2f269791dfce80d89edf22d5041be636b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734473"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar un nombre de dominio personalizado para Azure App Service

Los dominios de App Service son dominios personalizados que se administran directamente en Azure. Facilitan la administración de dominios personalizados para [Azure App Service](overview.md). En este tutorial se muestra cómo comprar un dominio de App Service y asignar nombres DNS a Azure App Service.

Para Azure Virtual Machines o Azure Storage, vea [Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage) (Asignación del dominio de App Service a Azure Virtual Machines o Azure Storage). Para Cloud Services, vea [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* [Cree una aplicación de App Service](./index.yml) o use alguna aplicación que haya creado para otro tutorial. La aplicación debe encontrarse en una región pública de Azure. Tenga en cuenta que, en este momento, no se admiten las nubes nacionales de Azure.
* [Elimine el límite de gasto en mi suscripción](../cost-management-billing/manage/spending-limit.md#remove). No puede comprar dominios de App Service con créditos de una suscripción gratuita.

## <a name="buy-an-app-service-domain"></a>Compra de un dominio de App Service

Para obtener información sobre precios de los dominios de App Service, visite la [página de precios de App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) y desplácese hasta el dominio de App Service.

1. Abra [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

1. En la barra de búsqueda, busque y seleccione **Dominios de App Service**.

    ![Navegación en el portal a los dominios de Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. En la vista **Dominios de App Service**, haga clic en **Agregar**.

    ![Hacer clic en Agregar en Dominios de App Service](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Seleccione **Haga clic aquí para probar la versión más reciente de la experiencia de creación de dominios de App Service**.

    ![Crear un dominio de App Service con la nueva experiencia](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Pestaña Aspectos básicos

1. En la pestaña **Aspectos básicos**, configure los valores según la tabla siguiente:  

   | Configuración  | Descripción |
   | -------- | ----------- |
   | **Suscripción** | Suscripción que se va a usar para comprar el dominio. |
   | **Grupo de recursos** | Grupo de recursos en el que se va a colocar el dominio. Por ejemplo, el grupo de recursos en el que se encuentra la aplicación. |
   | **Dominio** | Escriba el dominio que desee. Por ejemplo, **contoso.com**. Si el dominio que desea no está disponible, puede seleccionar uno en una lista de sugerencias de dominios disponibles o probar un dominio diferente. |

    > [!NOTE]
    > Los siguientes [dominios de primer nivel](https://wikipedia.org/wiki/Top-level_domain) son compatibles con los dominios de App Service: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ y _co.in_.
    >
    >
    
2. Cuando termine, haga clic en **Siguiente: Información de contacto**.

### <a name="contact-information-tab"></a>Pestaña Información de contacto

1. Proporcione la información que requiere [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) para el registro de dominio. 

    Es importante que rellene todos los campos obligatorios con la mayor precisión posible. Los datos incorrectos de la información de contacto pueden producir un error al comprar el dominio.

1. Cuando termine, haga clic en **Siguiente: Avanzado**.

### <a name="advanced-tab"></a>Ficha Opciones avanzadas

1. En la pestaña **Opciones avanzadas**, configure los ajustes opcionales:  

   | Configuración  | Descripción |
   | -------- | ----------- |
   | **Renovación automática** | De forma predeterminada está habilitado. Su dominio de App Service se registra automáticamente en incrementos de un año. La renovación automática garantiza que el registro del dominio no expire y que se conserve la propiedad del dominio. En el momento de la renovación, se carga automáticamente la cuota de registro del dominio anual en su suscripción de Azure. Para cancelar esta opción, seleccione **Deshabilitar**. Si la renovación automática está deshabilitada, puede [renovarla manualmente](#renew-the-domain). |
   | **Protección de la privacidad** | De forma predeterminada está habilitado. La protección de la privacidad oculta la información de contacto de registro del dominio de la base de datos WHOIS. La protección de la privacidad ya está incluida en la cuota anual de registro del dominio. Para cancelar esta opción, seleccione **Deshabilitar**. |

2. Cuando termine, haga clic en **Siguiente: Etiquetas**.

### <a name="finish"></a>Finalizar

1. En la pestaña **Etiquetas**, establezca las etiquetas que desee para su dominio de App Service. El etiquetado no es necesario para usar los dominios de App Service, pero es una [característica de Azure que le ayuda a administrar los recursos](../azure-resource-manager/management/tag-resources.md).

1. Haga clic en **Siguiente: Review + create** (Revisar y crear).

1. En la pestaña **Revisar y crear**, revise el pedido del dominio. Cuando haya terminado, haga clic en **Crear**.

    > [!NOTE]
    > Los dominios de App Service usan GoDaddy para el registro de dominio y Azure DNS para hospedar los dominios. Además de la cuota anual de registro del dominio, se aplican cargos por el uso de Azure DNS. Para obtener más información, vea [DNS de Azure Precios](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Una vez completado el registro del dominio, verá un botón **Ir al recurso**. Selecciónelo para ver la página de administración correspondiente.

    ![Dominio de App Service creado. Ir al recurso](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Ahora está listo para asignar una aplicación de App Service a este dominio personalizado.

## <a name="prepare-the-app"></a>Preparación de la aplicación

Para asignar un nombre DNS personalizado a una aplicación web, el [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) de la aplicación web debe ser un nivel de pago (Compartido, Básico, Estándar, Premium o Consumo para Azure Functions). En este paso, asegúrese de que la aplicación de App Service se encuentra en el plan de tarifa compatible.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegue hasta la aplicación en Azure Portal

1. En la barra de búsqueda superior, busque y seleccione **App Services**.

    ![Búsqueda de App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Seleccione el nombre de la aplicación.

    ![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Consulte la página de administración de la aplicación de App Service.  

### <a name="check-the-pricing-tier"></a>Comprobar el plan de tarifa

1. En el panel de navegación izquierdo de la página de la aplicación, desplácese hasta la sección **Configuración** y seleccione **Escalar verticalmente (plan de App Service)** .

    ![Menú Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. El nivel actual de la aplicación aparece resaltado con un cuadro azul. Asegúrese de que la aplicación web no está en el nivel **F1**. No se admiten DNS personalizados en el nivel **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Captura de pantalla del menú de navegación izquierdo de la página de la aplicación con la opción de escalar verticalmente (plan de App Service) seleccionada.":::

1. Si el plan de App Service no es **F1**, cierre la página **Escalar verticalmente** y vaya directamente a [Buy the domain](#buy-an-app-service-domain) (Comprar el dominio).

### <a name="scale-up-the-app-service-plan"></a>Escalado vertical del plan de App Service

1. Seleccione cualquiera de los niveles no gratuitos (**D1**, **B1**, **B2**, **B3**, o cualquier nivel de la categoría **Producción**). Para ver opciones adicionales, haga clic en **Ver opciones adicionales**.

1. Haga clic en **Aplicar**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Captura de pantalla de los planes de tarifa de dominio personalizado en la categoría producción con la pestaña producción, el plan B1 y el botón Aplicar resaltado.":::

    Cuando vea la siguiente notificación, significará que la operación de escalado se habrá completado.

    ![Confirmación de la operación de escalado](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Asignación de un dominio de App Service a la aplicación

Es fácil asignar un nombre de host del dominio de App Service a una aplicación de App Service, siempre que esté en la misma suscripción. Asigne el dominio de App Service o cualquiera de sus subdominios directamente en la aplicación, y Azure creará automáticamente los registros de DNS necesarios.

> [!NOTE]
> Si el dominio y la aplicación se encuentran en distintas suscripciones, asigne el dominio de App Service a la aplicación igual que [asignaría un dominio adquirido externamente](app-service-web-tutorial-custom-domain.md). En este caso, Azure DNS es el proveedor de dominios externo, y debe [agregar manualmente los registros de DNS necesarios](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Asignación del dominio

1. En el panel de navegación izquierdo de la página de la aplicación, desplácese hasta la sección **Configuración** y seleccione **Dominios personalizados**.

    ![Captura de pantalla que muestra el menú Dominios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Seleccione **Agregar dominio personalizado**.

    ![Captura de pantalla que muestra el elemento Agregar nombre de host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Escriba el dominio de App Service (por ejemplo, **contoso.com**) o un subdominio (por ejemplo, **www.contoso.com**) y haga clic en **Validar**.

    > [!NOTE]
    > Si comete un error tipográfico en el dominio de App Service, aparece un error de comprobación en la parte inferior de la página para indicar que faltan algunos registros de DNS. No es necesario agregar estos registros manualmente para un dominio de App Service. Solo tiene que asegurarse de escribir el nombre de dominio correctamente y de volver a hacer clic en **Validar**.
    >
    > ![Captura de pantalla que muestra un error de comprobación.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Acepte el **Tipo de registro de nombre de host** y haga clic en **Agregar dominio personalizado**.

    ![Captura de pantalla que muestra el botón Agregar dominio personalizado.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. El nuevo dominio personalizado puede tardar un tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Actualice el explorador para actualizar los datos.

    ![Captura de pantalla que muestra la adición del registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Una etiqueta **No seguro** para el dominio personalizado significa que aún no está enlazado a un certificado TLS/SSL. Todas las solicitudes HTTPS al dominio personalizado desde un explorador recibirán un error o una advertencia, en función del explorador. Para agregar un enlace TLS, consulte [Protección de un nombre DNS personalizado con un enlace TLS/SSL en Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Prueba del dominio personalizado

Para probar el dominio personalizado, desplácese a este en el explorador.

## <a name="renew-the-domain"></a>Renovación del dominio

El dominio de App Service que compró es válido durante un año a partir del momento de la compra. De forma predeterminada, el dominio está configurado para renovarse automáticamente, y el cargo se realizará el próximo año según el método de pago especificado. También puede renovar manualmente el nombre de dominio.

Si desea desactivar la renovación automática, o si desea renovar manualmente su dominio, siga estos pasos.

1. En la barra de búsqueda, busque y seleccione **Dominios de App Service**.

    ![Navegación en el portal a los dominios de Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. En la sección **Dominios de App Service**, seleccione el dominio que desea configurar.

1. En el panel de navegación izquierdo del dominio, seleccione **Renovación de dominios**. Para detener la renovación automática del dominio, seleccione **Desactivar**. La configuración surte efecto inmediatamente.

    ![Captura de pantalla de la opción de renovación automática del dominio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Al desplazarse fuera de la página, haga clic en **Aceptar** para omitir el error "Se descartarán los cambios que no haya guardado".
    >

Para renovar el dominio manualmente, seleccione **Renovar dominio**. Sin embargo, este botón no está activo hasta [noventa días antes de la expiración del dominio](#when-domain-expires).

Si la renovación del dominio se realiza correctamente, recibirá una notificación por correo electrónico dentro de un plazo de 24 horas.

## <a name="when-domain-expires"></a>Cuando expira el dominio

Azure procesa los dominios de App Service expirados o que están por expirar del modo siguiente:

* Si la renovación automática está deshabilitada: 90 días antes de la expiración del dominio, se le envía un correo electrónico de notificación de renovación y el botón **Renovar dominio** se activa en el portal.
* Si la renovación automática está habilitada: el día después de la fecha de expiración del dominio, Azure intenta cobrarle por la renovación del nombre de dominio.
* Si se produce un error durante la renovación automática (por ejemplo, expiró la tarjeta en el archivo) o si la renovación automática está deshabilitada y permite que el dominio expire, Azure le avisa de la expiración del dominio y aparca el nombre de dominio. También puede [renovar manualmente](#renew-the-domain) su dominio.
* 4 y 12 días después de la expiración, Azure envía correos electrónicos de notificación adicionales. También puede [renovar manualmente](#renew-the-domain) su dominio. El quinto día después de la expiración, la resolución DNS se detiene para el dominio expirado.
* 19 días después de la expiración, el dominio permanece en espera, pero queda sujeta a un precio de canje. Puede llamar al soporte al cliente para renovar su nombre de dominio, sujeto a cuotas de canje y renovación aplicables.
* 25 días después de la expiración, Azure pone al dominio en subasta con un servicio de subasta de nombres de dominio del sector. Puede llamar al soporte al cliente para renovar su nombre de dominio, sujeto a cuotas de canje y renovación aplicables.
* 30 días después de la expiración, ya no podrá canjear su dominio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Administrar los registros DNS personalizados

En Azure, los registros DNS para un dominio de App Service se administran mediante [DNS de Azure](https://azure.microsoft.com/services/dns/). Puede agregar, quitar y actualizar los registros DNS, al igual que para un dominio comprado de forma externa. Para administrar los registros de DNS personalizados:

1. En la barra de búsqueda, busque y seleccione **Dominios de App Service**.

    ![Navegación en el portal a los dominios de Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. En la sección **Dominios de App Service**, seleccione el dominio que desea configurar.

1. En la página **Información general**, seleccione **Administrar los registros DNS**.

    ![Captura de pantalla que muestra dónde acceder a los registros de DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Para obtener información sobre cómo editar los registros DNS, vea [Administración de zonas DNS en Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar la compra (eliminar el dominio)

Después de comprar el dominio de App Service, dispone de cinco días para cancelar la compra para obtener un reembolso completo. Después de cinco días, puede eliminar el dominio de App Service, pero no puede recibir un reembolso.

1. En la barra de búsqueda, busque y seleccione **Dominios de App Service**.

    ![Navegación en el portal a los dominios de Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. En la sección **Dominios de App Service**, seleccione el dominio que desea configurar.

1. En el panel de navegación izquierdo del dominio, seleccione **Bloqueos**. 

    Se ha creado un bloqueo de eliminación para el dominio. Siempre que exista un bloqueo de eliminación, no se puede eliminar el dominio de App Service.

1. Haga clic en **Eliminar** para quitar el bloqueo.

1. En el panel de navegación de la izquierda del dominio, seleccione **Información general**. 

1. Si no ha transcurrido el período de cancelación en el dominio comprado, haga clic en **Cancelar compra**. De lo contrario, en su lugar verá el botón **Eliminar**. Para eliminar el dominio sin un reembolso, haga clic en **Eliminar**.

    ![Captura de pantalla que muestra dónde se debe eliminar o cancelar un dominio adquirido.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Para confirmar la operación, seleccione **Sí**.

    Una vez completada la operación, el dominio se libera de la suscripción y vuelve a estar disponible para que otros usuarios lo compren. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direccionamiento de una dirección URL predeterminada a un directorio personalizado

De forma predeterminada, App Service dirige las solicitudes web al directorio raíz del código de la aplicación. Para dirigirlas a un subdirectorio, como `public`, consulte [Redirección a un directorio personalizado](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo enlazar un certificado TLS/SSL a App Service.

> [!div class="nextstepaction"]
> [Protección de un nombre DNS personalizado con un enlace TLS en Azure App Service](configure-ssl-bindings.md)
