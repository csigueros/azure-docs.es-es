---
title: Recuperación ante desastres para Azure API for FHIR
description: En este artículo, aprenderá a habilitar las características de recuperación ante desastres para Azure API for FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780935"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Recuperación ante desastres para Azure API for FHIR

Azure API for FHIR® es un servicio totalmente administrado, basado en Recursos Rápidos de Interoperabilidad en Salud (FHIR®). Para satisfacer los requisitos empresariales y de cumplimiento, puede usar la característica de recuperación ante desastres (DR) para Azure API for FHIR.

La característica de recuperación ante desastres proporciona un objetivo de punto de recuperación (RPO) de 15 minutos y un objetivo de tiempo de recuperación (RTO) de 60 minutos.

 ## <a name="how-to-enable-dr"></a>Habilitación de la recuperación ante desastres 
  
Para habilitar la característica de recuperación ante desastres, cree una incidencia única de soporte técnico. Puede elegir una región emparejada de Azure u otra región donde se admita Azure API for FHIR. El equipo de soporte técnico de Microsoft habilitará la característica de DR en función de la prioridad de soporte técnico.

## <a name="how-the-dr-process-works"></a>Funcionamiento del proceso de recuperación ante desastres

El proceso de DR consta de los siguientes pasos: 
* Replicación de datos
* Conmutación por error automática
* Recuperación de las regiones afectadas
* Conmutación por recuperación manual

### <a name="data-replication-in-the-secondary-region"></a>Replicación de datos en la región secundaria

De forma predeterminada, Azure API for FHIR ofrece protección de datos mediante copia de seguridad y restauración. Cuando se habilita la característica de recuperación ante desastres, comienza la replicación de datos. En la región secundaria de Azure se crea una réplica de datos que se sincroniza automáticamente. La replicación inicial de los datos puede tardar entre unos minutos y unas horas, o más, en función de la cantidad de datos. La réplica secundaria de datos es una replicación de los datos principales. Se usa directamente para recuperar el servicio y ayuda a acelerar el proceso de recuperación.

Conviene indicar que las RU/s de capacidad de proceso deben tener los mismos valores en las regiones primaria y secundaria.

[ ![Azure Traffic Manager](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>Conmutación por error automática

Durante una interrupción de la región primaria, Azure API for FHIR lleva a cabo automáticamente una conmutación por error a la región secundaria, donde se usa el mismo punto de conexión de servicio. Se espera que el servicio se reanude en una hora o menos, y la posible pérdida de datos es de hasta 15 minutos. Es posible que se requieran otros cambios de configuración. Para más información, consulte [Cambios en la configuración de DR](#configuration-changes-in-dr).

[ ![Conmutación por error en la recuperación ante desastres](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>Recuperación de las regiones afectadas

Una vez que se recupera la región afectada, está disponible automáticamente como región secundaria y se reinicia la replicación de datos. Puede iniciar el proceso de recuperación de datos o esperar hasta que se complete el paso de conmutación por recuperación.

[ ![Replicación en la recuperación ante desastres](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

Si el proceso ha vuelto a la región recuperada y los datos no lo han hecho, puede haber posibles latencias de red. La razón principal es que el proceso y los datos se encuentran en dos regiones diferentes. Las latencias de red deberían desaparecer automáticamente en cuanto los datos conmuten por recuperación a la región recuperada con un desencadenador manual.

[ ![Latencia de red](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>Conmutación por recuperación manual

El proceso conmuta por recuperación automáticamente a la región recuperada. El equipo de soporte técnico de Microsoft cambia de forma manual los datos a la región recuperada mediante el script. 

[ ![Conmutación por recuperación en la recuperación ante desastres](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>Cambios en la configuración de DR

Es posible que se requieran otros cambios de configuración cuando se usen Private Link, la clave administrada por el cliente (CMK), el conector de IoMT (Internet de las cosas médicas) para FHIR y $export.

### <a name="private-link"></a>Private Link

Puede habilitar la característica de vínculo privado antes o después de que se haya aprovisionado Azure API for FHIR. También puede aprovisionarla antes o después de habilitar la característica de recuperación ante desastres. Consulte la siguiente lista cuando esté listo para configurar Private Link para la recuperación ante desastres.

* Configure Azure Private Link en la región primaria. Este paso no es necesario en la región secundaria. Para más información, consulte [Configuración del vínculo privado](/azure/healthcare-apis/fhir/configure-private-link).

* Cree una red virtual de Azure en la región primaria y otra en la región secundaria. Para más información, consulte [Inicio rápido: Creación de una red virtual mediante Azure Portal](../../virtual-network/quick-create-portal.md).

* En la región primaria, la red virtual crea un emparejamiento de VNet con la red virtual de la región secundaria. Para más información, consulte [Emparejamiento de redes virtuales](../../virtual-network/virtual-network-peering-overview.md).

* Use la configuración predeterminada; si lo prefiere, puede adaptar esta configuración según sea necesario. Lo importante es que el tráfico pueda fluir entre las dos redes virtuales.

* Cuando se configura el DNS privado, la red virtual de la región secundaria debe configurarse manualmente como "Vínculos de red virtual". La red virtual principal ya se debe haber agregado como parte del flujo de creación del punto de conexión de Private Link. Para más información, consulte [¿Qué es un vínculo de red virtual?](../../dns/private-dns-virtual-network-links.md)

* Opcionalmente, configure una máquina virtual en la red virtual de la región primaria y otra en la red virtual de la región secundaria. Puede acceder a Azure API for FHIR desde ambas máquinas virtuales.

La característica de vínculo privado debe seguir funcionando durante una interrupción regional y después de que se haya completado la conmutación por recuperación. Para más información, consulte [Configuración del vínculo privado](/azure/healthcare-apis/fhir/configure-private-link).

> [!NOTE]
> La configuración de redes virtuales y de su emparejamiento no afecta a la replicación de datos.

### <a name="cmk"></a>CMK

El acceso a Azure API for FHIR se mantendrá si se puede acceder al almacén de claves que hospeda la clave administrada en la suscripción. Hay un posible tiempo de inactividad temporal debido a que Key Vault puede tardar hasta 20 minutos en restablecer su conexión. Para más información, consulte [Redundancia y disponibilidad de Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md).  

### <a name="export"></a>$export

El trabajo de exportación se recogerá de otra región después de 10 minutos sin que se actualice el estado del trabajo. Siga las instrucciones de Azure Storage para recuperar la cuenta de almacenamiento en caso de una interrupción regional. Para más información, consulte [Recuperación ante desastres y conmutación por error de la cuenta de almacenamiento](../../storage/common/storage-disaster-recovery-guidance.md). 

Asegúrese de conceder los mismos permisos a la identidad del sistema de Azure API for FHIR. Además, si la cuenta de almacenamiento está configurada con redes seleccionadas, consulte [Exportación de datos de FHIR](/azure/healthcare-apis/fhir/export-data).

### <a name="iomt-fhir-connector"></a>Conector de IoMT para FHIR

Las conexiones existentes no funcionarán hasta que se restaure la región con errores. Puede crear una nueva conexión una vez que se haya completado la conmutación por error y se pueda acceder al servidor de FHIR. Esta nueva conexión seguirá funcionando cuando se produzca la conmutación por recuperación.

> [!NOTE]
> El conector de IoMT es una característica en versión preliminar y no ofrece compatibilidad con la recuperación ante desastres. 

## <a name="how-to-test-dr"></a>Prueba de DR

Aunque no es necesario, puede probar la característica de recuperación ante desastres en un entorno que no sea de producción. En la prueba de DR solo se incluirán los datos y no el proceso. 

Lleve a cabo los pasos siguientes para la prueba de DR.

* Prepare un entorno de prueba con datos de prueba. Se recomienda usar una instancia de servicio con pequeñas cantidades de datos para reducir el tiempo de replicación de los datos.
 
* Cree una incidencia de soporte técnico y proporcione la suscripción de Azure y el nombre del servicio de Azure API for FHIR para el entorno de prueba.

* Desarrolle un plan de prueba, como lo haría con cualquier prueba de recuperación ante desastres.
 
* El equipo de soporte técnico de Microsoft habilita la característica de recuperación ante desastres y confirma que se ha realizado la conmutación por error.

* Realice la prueba de recuperación ante desastres y registre los resultados, que deben incluir cualquier problema de pérdida de datos y latencia de red. 

* Para la conmutación por recuperación, notifique al equipo de soporte técnico de Microsoft que complete el paso con esta acción.
 
* (Opcional) Comparta sus comentarios con el equipo de soporte técnico de Microsoft.


> [!NOTE]
> La prueba de recuperación ante desastres duplicará el costo del entorno de prueba mientras se lleva a cabo. No se incurrirá en ningún costo adicional después de que se complete la prueba de recuperación ante desastres y se deshabilite esta característica.

## <a name="cost-of-disaster-recovery"></a>Costo de la recuperación ante desastres

La característica de recuperación ante desastres incurre en costos adicionales debido a los datos de proceso y la réplica de datos que se ejecutan en el entorno de la región secundaria. Para más detalles sobre los precios, consulte la página web [Precios de Azure Healthcare APIs]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir).

> [!NOTE]
> La oferta de recuperación ante desastres está sujeta al [SLA para Azure Healthcare APIs](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/), 1.0.


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo funciona la recuperación ante desastres para Azure API for FHIR y cómo habilitarla. Para obtener información sobre otras características admitidas de Azure API for FHIR, consulte:

>[!div class="nextstepaction"]
>[Características admitidas de FHIR](fhir-features-supported.md)