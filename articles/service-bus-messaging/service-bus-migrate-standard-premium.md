---
title: 'Migración de espacios de nombres de Azure Service Bus: Estándar a Prémium'
description: Guía para permitir la migración de espacios de nombre estándar de Azure Service Bus existentes al nivel premium.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: eea34edddf641e3ee1c07bea92b20364e7aeaf34
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602141"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migración de espacios de nombres estándar de Azure Service Bus existentes

Anteriormente, Azure Service Bus ofrecía espacios de nombres solo en el nivel Estándar. Los espacios de nombres son configuraciones multiinquilino optimizadas para entornos de desarrollador y de rendimiento bajo. El nivel premium ofrece recursos dedicados por espacio de nombres para la latencia predecible y el rendimiento aumentado a un precio fijo. El nivel premium está optimizado para entornos de producción y de alto rendimiento que requieren características empresariales adicionales.

En este artículo se describe cómo migrar los espacios de nombre de nivel Estándar existentes al nivel premium.  

>[!WARNING]
> La migración está pensada para que los espacios de nombres de Service Bus se actualicen al nivel premium. La herramienta de migración no permite cambiar a una versión anterior.

Algunos de los puntos que debe tener en cuenta:

- La migración está pensada para realizarse en contexto, lo que significa que las aplicaciones remitente y receptora **no requieren hacer ningún cambio en el código ni en la configuración**. La cadena de conexión existente apuntará automáticamente al espacio de nombres premium nuevo.
- El espacio de nombres **Premium** no debe tener **ninguna entidad** para que la migración se realice correctamente.
- Todas las **entidades** del espacio de nombres estándar se **copiar** al espacio de nombres premium durante el proceso de migración.
- La migración admite **1000 entidades por unidad de mensajería** en el nivel premium. Para identificar cuántas unidades de mensajería necesita, empiece con el número de entidades que tiene en el espacio de nombres estándar actual.
- No es posible migrar directamente desde el **nivel básico** al **nivel premium**, pero puede hacerlo de manera indirecta si migra primero desde el nivel básico al nivel estándar y, luego, desde el nivel estándar al premium en el paso siguiente.

## <a name="migration-steps"></a>Pasos de migración

El proceso de migración tiene asociadas algunas condiciones. Familiarícese con estos pasos para disminuir la posibilidad de errores. En estos pasos se describe el proceso de migración y los detalles paso a paso aparecen en las secciones siguientes.

1. Cree un espacio de nombres premium nuevo.
1. Empareje los espacios de nombres estándar y premium entre sí.
1. Sincronice (copie) las entidades del espacio de nombres estándar en el espacio de nombres premium.
1. Confirme la migración.
1. Para purgar las entidades del espacio de nombres estándar, use el nombre del espacio de nombres posterior a la migración.
1. Elimine el espacio de nombres estándar.

>[!IMPORTANT]
> Una vez confirmada la migración, acceda al espacio de nombres estándar anterior y purgue las colas y las suscripciones. Una vez drenados los mensajes, se pueden enviar al espacio de nombres premium nuevo para que las aplicaciones receptoras los procesen. Una vez drenadas las colas y las suscripciones, se recomienda eliminar el espacio de nombres estándar anterior.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migración mediante la CLI de Azure o PowerShell

Para migrar el espacio de nombres estándar de Service Bus a premium mediante la herramienta de PowerShell o la CLI de Azure, siga estos pasos.

1. Cree un espacio de nombres premium de Service Bus nuevo. Puede hacer referencia a las [plantillas de Azure Resource Manager](service-bus-resource-manager-namespace.md) o [use Azure Portal](service-bus-create-namespace-portal.md). Asegúrese de usar **Premium** para el parámetro **serviceBusSku**.

1. Establezca las variables de entorno siguientes para simplificar los comandos de migración.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > El alias/nombre posterior a la migración (post_migration_dns_name) se usará para acceder al espacio de nombres estándar anterior después de la migración. Úselo para purgar las colas y las suscripciones y, luego, elimine el espacio de nombres.

1. Empareje los espacios de nombres estándar y premium y use el comando siguiente para iniciar la sincronización:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Compruebe el estado de la migración con el comando siguiente:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migración se considera completa cuando ve los valores siguientes:

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    Esta comando también muestra la configuración de migración. Compruebe que los valores están establecidos correctamente. Compruebe también el espacio de nombres Premium en el portal para asegurarse de que se crearon todos los temas y colas y que coinciden con el contenido que existía en el espacio de nombres estándar.

1. Ejecute el comando completo siguiente para confirmar la migración:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migración mediante Azure Portal

La migración mediante Azure Portal tiene el mismo flujo lógico que la migración con los comandos. Siga estos pasos para migrar mediante Azure Portal.

1. En el menú de **navegación** de la izquierda, seleccione **Migrar a premium**. Haga clic en el botón **Comenzar** para pasar a la siguiente página.
    :::image type="content" source="./media/service-bus-standard-premium-migration/migrate-premium-page.png" alt-text="Imagen que muestra la página Migrar a la versión premium.":::
1. Verá la página **Configurar espacios de nombres**.

    :::image type="content" source="./media/service-bus-standard-premium-migration/setup-namespaces-page.png" alt-text="Imagen que muestra la página de configuración de espacios de nombres.":::
1. En las páginas de **Configuración de espacios de nombres**, siga uno de estos pasos: 
    1. Si selecciona **Crear un nuevo espacio de nombres prémium**:
        1. En la página **Crear espacio de nombres**, escriba un nombre para el espacio de nombres y seleccione **Revisar y crear**.
        1. En la página **Revisar y crear**, seleccione **Crear**.

            :::image type="content" source="./media/service-bus-standard-premium-migration/create-premium-namespace.png" alt-text="Imagen que muestra la página Crear un espacio de nombres.":::
    1. Si selecciona **Seleccionar un espacio de nombres prémium vacío ya existente**:
        1. Seleccione la suscripción de Azure y el grupo de recursos que tiene el espacio de nombres.
        1. A continuación, seleccione el espacio de nombres prémium.
        1. Después, haga clic en **Seleccionar**.
        
            :::image type="content" source="./media/service-bus-standard-premium-migration/select-existing-namespace.png" alt-text="Imagen que muestra la selección de un espacio de nombres prémium existente.":::
1. Escriba un **Nombre posterior a la migración** y, a continuación, seleccione **Siguiente**. Usará este nombre para acceder al espacio de nombres estándar una vez que se complete la migración.

    :::image type="content" source="./media/service-bus-standard-premium-migration/enter-post-migration-name.png" alt-text="Imagen que muestra el nombre posterior a la migración para el espacio de nombres estándar.":::
1. Seleccione **Iniciar sincronización** para sincronizar entidades entre los espacios de nombres estándar y prémium.

    :::image type="content" source="./media/service-bus-standard-premium-migration/start-sync-button.png" alt-text="Imagen que muestra el botón Iniciar sincronización.":::
1. Seleccione **Sí** en el cuadro de diálogo para confirmar e iniciar la sincronización. Espere hasta que se haya completado. Después, seleccione **Siguiente**.

    >[!IMPORTANT]
    > Si por algún motivo tiene que anular la migración, revise el flujo de anulación en la sección de preguntas más frecuentes de este documento.    
1. Seleccione **Completar migración** en la página **Cambiar**. 

    :::image type="content" source="./media/service-bus-standard-premium-migration/complete-migration.png" alt-text="Imagen que muestra la página **Cambiar** del asistente de migración.":::
1. Seleccione **Sí** para confirmar el cambio del espacio de nombres estándar a prémium. Una vez completado el cambio, el nombre DNS del espacio de nombres estándar apuntará al espacio de nombres prémium. Esta operación no se puede deshacer. Verá la página **Correcto** cuando se complete la migración.

    :::image type="content" source="./media/service-bus-standard-premium-migration/success-page.png" alt-text="Imagen que muestra la página Correcto.":::

## <a name="caveats"></a>Advertencias

En el nivel Premium de Azure Service Bus no se admiten algunas de las características que proporciona el nivel Estándar de Azure Service Bus. Esto es así por diseño, ya que el nivel Premium ofrece recursos dedicados para una latencia y un rendimiento predecibles.

Aquí tiene una lista de las características que no admite el nivel Premium y su mitigación:

### <a name="express-entities"></a>Entidades exprés

   Las entidades exprés que no confirman datos de mensajes en el almacenamiento no se admiten en el nivel Premium. Los recursos dedicados proporcionan una mejora significativa en el rendimiento a la vez que garantizan la persistencia de los datos, como se espera de cualquier sistema de mensajería empresarial.

   Durante la migración, cualquiera de las entidades exprés del espacio de nombres estándar se creará en el espacio de nombres Premium como una entidad no exprés.

   Si usa plantillas de Azure Resource Manager (ARM), asegúrese de quitar la marca "enableExpress" de la configuración de implementación para que los flujos de trabajo automatizados se ejecuten sin errores.

### <a name="partitioned-entities"></a>Entidades con particiones

   Las entidades con particiones se admitían en el nivel Estándar para proporcionar una mejor disponibilidad en una configuración de varios inquilinos. Con el aprovisionamiento de recursos dedicados disponibles por espacio de nombres en el nivel Premium, ya no es necesario.

   Durante la migración, cualquier entidad con particiones del espacio de nombres estándar se crea en el espacio de nombres Premium como una entidad sin particiones.

   Si la plantilla de ARM establece "enablePartitioning" en "true" para una cola o un tema en concreto, el agente lo omitirá.

## <a name="faqs"></a>Preguntas más frecuentes

### <a name="what-happens-when-the-migration-is-committed"></a>¿Qué pasa cuando se confirma la migración?

Una vez que se confirma la migración, la cadena de conexión que apunta al espacio de nombres estándar apuntará al espacio de nombres premium.

Las aplicaciones remitente y receptora se desconectarán del espacio de nombres estándar y se reconectarán automáticamente al espacio de nombres premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>¿Qué se hace una vez que se completa la migración de estándar a premium?

La migración de estándar a premium garantiza que los metadatos de la entidad, como los temas, las suscripciones y los filtros, se copia del espacio de nombres estándar al espacio de nombres premium. Los datos de mensajes que se confirmaron en el espacio de nombres estándar no se copian desde ahí al espacio de nombres premium.

El espacio de nombres estándar pueden tener algunos mensajes que se enviaron y confirmación mientras se realizaba la migración. Purgue manualmente estos mensajes del espacio de nombres estándar y envíelos de manera manual al espacio de nombres premium. Para purgar manualmente los mensajes, use una aplicación de consola o un script que purgue las entidades del espacio de nombres estándar mediante el uso del nombre DNS posterior a la migración que especificó en los comandos de la migración. Envíe estos mensajes al espacio de nombres premium para que los puedan procesar los receptores.

Una vez purgados los mensajes, elimine el espacio de nombres estándar.

>[!IMPORTANT]
> Una vez purgados los mensajes del espacio de nombres estándar, elimine el espacio de nombres estándar. Esto es importante porque la cadena de conexión que inicialmente hacía referencia al espacio de nombres estándar ahora hace referencia al espacio de nombres premium. Ya no necesitará el espacio de nombres estándar. Eliminar el espacio de nombres estándar que migró ayuda a reducir una confusión a futuro.

### <a name="how-much-downtime-do-i-expect"></a>¿Cuánto tiempo de inactividad debo esperar?

El proceso de migración está pensado para disminuir el tiempo de inactividad esperado para las aplicaciones. El tiempo de inactividad se reduce mediante el uso de la cadena de conexión que las aplicaciones remitente y receptora usan para apuntar al espacio de nombres premium nuevo.

El tiempo de inactividad que experimenta la aplicación se limita al tiempo que tarda la actualización de la entrada DNS para apuntar al espacio de nombres premium. El tiempo de inactividad es de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>¿Es necesario modificar la configuración mientras se realiza la migración?

No, no es necesario ningún cambio de código ni de la configuración durante la migración. La cadena de conexión que las aplicaciones remitente y receptora usan para acceder al espacio de nombres estándar se asigna automáticamente para que actúe como alias del espacio de nombres premium.

### <a name="what-happens-when-i-abort-the-migration"></a>¿Qué pasa si anulo la migración?

La migración se puede anular con el comando `Abort` o con Azure Portal.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portal

Seleccione **Anular** en la página **Entidades de sincronización**. 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort1.png" alt-text="Imagen que muestra la página Anular.":::

Cuando haya terminado, verá la página siguiente: 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort3.png" alt-text="Imagen que muestra la página Anulación completada.":::

Cuando se anula el proceso de migración, se anula el proceso de copiar las entidades (temas, suscripciones y filtros) del espacio de nombres estándar al espacio de nombres premium y se interrumpe el emparejamiento.

La cadena de conexión no se actualiza para que apunte al espacio de nombres premium. Las aplicaciones existentes siguen funcionando como lo hacían antes de iniciar la migración.

Sin embargo, no se eliminan las entidades del espacio de nombres premium ni se elimina este espacio. Elimine manualmente las entidades si decidió no continuar con la migración.

>[!IMPORTANT]
> Si decide anular la migración, elimine el espacio de nombres premium que aprovisionó para la migración para que no le cobren los recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>No quiero purgar los mensajes. ¿Qué puedo hacer?

Puede que haya mensajes enviados por las aplicaciones remitentes y confirmados en el almacenamiento del espacio de nombres estándar mientras se realiza la migración, justo antes de confirmarla.

Durante la migración, la carga o los datos de mensaje reales no se copia del espacio de nombres estándar al premium. Los mensajes se deben purgar de manera manual y, luego, se deben enviar al espacio de nombres premium.

Sin embargo, si puede realizar la migración durante una ventana de mantenimiento planeado y no quiere purgar manualmente los mensajes y enviarlos, siga estos pasos:

1. Detenga las aplicaciones remitentes. Las aplicaciones receptoras procesarán los mensajes que actualmente se encuentran en el espacio de nombres estándar y purgarán la cola.
1. Una vez que las colas y las suscripciones del espacio de nombres estándar están vacías, siga el procedimiento que se describió anteriormente para ejecutar la migración desde el espacio de nombres estándar al premium.
1. Una vez que se completa la migración, puede reiniciar las aplicaciones remitentes.
1. Las aplicaciones remitentes y receptoras ahora se conectarán automáticamente con el espacio de nombres premium.

    >[!NOTE]
    > No es necesario que detenga las aplicaciones receptoras para realizar la migración.
    >
    > Una vez que se complete la migración, las aplicaciones receptoras se desconectarán del espacio de nombres estándar y se conectarán automáticamente al espacio de nombres premium.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre las [diferencias entre mensajería Estándar y Premium](./service-bus-premium-messaging.md).
* Conozca los [aspectos de alta disponibilidad y de recuperación ante desastres geográfica para el nivel premium de Service Bus](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

