---
title: Protección de red adaptable en Azure Security Center | Microsoft Docs
description: Obtenga información sobre cómo usar patrones de tráfico reales para proteger las reglas de los grupos de seguridad de red (NSG) y mejorar aún más la posición de seguridad.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 05/26/2021
ms.author: memildin
ms.openlocfilehash: 7bb65cf7e63dcb6f31eef73ccb3ad3186a56f0c5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734952"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Mejora de la posición de seguridad de red con la protección de redes adaptativa

La protección de red adaptable es una característica sin agente de Azure Security Center. No es necesario instalar nada en las máquinas para beneficiarse de esta herramienta de protección de red.

En esta página se explica cómo configurar y administrar la protección de red adaptable en Security Center.

## <a name="availability"></a>Disponibilidad
|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Requiere [Azure Defender para servidores](defender-for-servers-introduction.md).|
|Roles y permisos necesarios:|Permisos de escritura en los NSG de la máquina|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||

## <a name="what-is-adaptive-network-hardening"></a>¿Qué es la protección de red adaptable?
La aplicación de [grupos de seguridad de red (NSG)](../virtual-network/network-security-groups-overview.md) para filtrar el tráfico hacia y desde los recursos mejora la postura de seguridad de red. Sin embargo, aún puede haber algunos casos en los que el tráfico real que fluye a través del NSG es un subconjunto de las reglas de NSG definidas. En estos casos, puede mejorar la postura de seguridad al proteger aún más las reglas de NSG, según los patrones de tráfico real.

La protección de red adaptable proporciona recomendaciones para proteger mejor las reglas de NSG. Usa un algoritmo de aprendizaje automático que tiene en cuenta el tráfico real, la configuración de confianza conocida, la inteligencia de amenazas y otros indicadores de riesgo, y luego proporciona recomendaciones para permitir el tráfico solo desde tuplas IP y puerto específicas.

Por ejemplo, supongamos que la regla de NSG existente es permitir el tráfico desde 140.20.30.10/24 en el puerto 22. En función del análisis del tráfico, la protección de red adaptable podría recomendar la limitación del intervalo para permitir el tráfico desde 140.23.30.10/29 y denegar todo el tráfico restante en ese puerto. Para obtener la lista completa de puertos admitidos, consulte la entrada de preguntas más frecuentes [¿Qué puertos se admiten?](#which-ports-are-supported)

## <a name="view-hardening-alerts-and-recommended-rules"></a>Visualización de alertas de protección y reglas recomendadas

1. En el menú de Security Center, abra el panel de **Azure Defender** y seleccione el icono de protección de red adaptable (1) o el elemento del panel de conclusiones relacionado con la protección de red adaptable (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Acceso a las herramientas de protección de red adaptable." lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > En el panel de conclusiones se muestra el porcentaje de las máquinas virtuales que se defienden actualmente con la protección de red adaptable. 

1. La página de detalles de la recomendación **Las recomendaciones de protección de redes adaptables se deben aplicar en las máquinas virtuales orientadas a Internet** se abre con las máquinas virtuales de red agrupadas en tres pestañas:
   * **Recursos con estado incorrecto** : VM que tienen actualmente recomendaciones y alertas que se desencadenaron mediante la ejecución del algoritmo de protección de red adaptable. 
   * **Recursos con estado correcto**: VM sin alertas ni recomendaciones.
   * **Recursos sin analizar** : VM en las que no se puede ejecutar el algoritmo de protección de red adaptable debido a uno de los motivos siguientes:
      * **Las VM son VM clásicas**: solo se admiten VM de Azure Resource Manager.
      * **No hay suficientes datos disponibles**: para generar recomendaciones de protección de tráfico precisas, Security Center requiere al menos 30 días de datos de tráfico.
      * **La VM no está protegida por Azure Defender**: Solo las máquinas virtuales protegidas con [Azure Defender para servidores](defender-for-servers-introduction.md) son válidas para esta característica.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Página de detalles de la recomendación Las recomendaciones de protección de redes adaptables se deben aplicar en las máquinas virtuales orientadas a Internet.":::

1. En la pestaña **Recursos con estado incorrecto**, seleccione una VM para ver sus alertas y las reglas de protección recomendadas que se deben aplicar.

    - En la pestaña **Reglas** se enumeran las reglas que recomiendan la protección de red adaptable que agregue.
    - En la pestaña **Alertas** se enumeran las alertas que se generaron debido al tráfico, que fluye al recurso y que no está dentro del intervalo de IP permitidas en las reglas recomendadas.

1. Si lo desea, edite las reglas:

    - [Modificación de una regla](#modify-rule)
    - [Eliminar una regla](#delete-rule) 
    - [Adición de una regla](#add-rule)

3. Elija las reglas que quiere aplicar en el NSG y seleccione **Aplicar**.

    > [!TIP]
    > Si los intervalos de direcciones IP de origen permitidos se muestran como "Ninguno", significa que la regla recomendada es una regla de *denegación*; de lo contrario, es una regla de *permiso*.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Administración de reglas de protección de red adaptable.":::

      > [!NOTE]
      > Las reglas aplicadas se agregan a los NSG que protegen la VM. (Una VM podría estar protegida por un NSG asociado a su NIC, la subred en la que reside la VM o ambos).

## <a name="modify-a-rule"></a>Modificación de una regla <a name ="modify-rule"> </a>

Es posible modificar los parámetros de una regla que se haya recomendado. Por ejemplo, quizás querrá cambiar los intervalos de IP recomendados.

Instrucciones importantes a la hora de modificar una regla de protección de red adaptable:

- No puede cambiar reglas de tipo **permitir** para convertirlas en reglas de tipo **denegar**. 

- Solo puede modificar los parámetros de las reglas de tipo **permitir**. 

    La creación y modificación de reglas de tipo "denegar" se realiza directamente en el NSG. Para más información, consulte [Creación, modificación o eliminación de un grupo de seguridad de red ](../virtual-network/manage-network-security-group.md).

- Una regla de tipo **Denegar todo el tráfico** es el único tipo de regla "denegar" que figuraría aquí, y no se puede modificar. Sin embargo, puede eliminar la regla (consulte [Eliminación de una regla](#delete-rule)). Para obtener información sobre este tipo de regla, consulte la entrada de preguntas más frecuentes [¿Cuándo debo usar una regla "Denegar todo el tráfico"?](#when-should-i-use-a-deny-all-traffic-rule)

Para modificar una regla de protección de red adaptable:

1. Para modificar algunos de los parámetros de una regla, en la pestaña **Reglas**, seleccione los puntos suspensivos (…) al final de la fila de la regla y seleccione **Editar**.

   ![Edición de reglas.](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. En la ventana **Editar regla**, actualice los detalles que quiere cambiar y seleccione **Guardar**.

   > [!NOTE]
   > Después de seleccionar **Guardar**, habrá cambiado la regla correctamente. *Sin embargo, no la ha aplicado al NSG.* Para ello, debe seleccionar la regla de la lista y seleccionar en **Exigir** (tal como se explica en el paso siguiente).

   ![Selección de Guardar.](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar la regla actualizada, en la lista, elija la regla actualizada y seleccione **Aplicar**.

    ![Aplicación de la regla.](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

## <a name="add-a-new-rule"></a>Adición de una nueva regla <a name ="add-rule"> </a>

Puede agregar una regla de tipo "permitir" no recomendada por Security Center.

> [!NOTE]
> Aquí solo se pueden agregar reglas de tipo "permitir". Si quiere agregar reglas de tipo "denegar", puede hacerlo directamente en el NSG. Para más información, consulte [Creación, modificación o eliminación de un grupo de seguridad de red ](../virtual-network/manage-network-security-group.md).

Para agregar una regla de protección de red adaptable:

1. En la barra de herramientas superior, seleccione **Agregar regla**.

   ![Adición de la regla.](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. En la ventana **Nueva regla**, especifique los detalles y seleccione **Agregar**.

   > [!NOTE]
   > Después de seleccionar **Agregar**, habrá agregado correctamente la regla y esta se mostrará con las demás reglas recomendadas, pero no la ha *aplicado* al NSG. Para ello, debe elegir la regla de la lista y seleccionar **Aplicar** (tal como se explica en el paso siguiente).

3. Para aplicar la nueva regla, elíjala en la lista y seleccione **Aplicar**.

    ![Aplicación de la regla.](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


## <a name="delete-a-rule"></a>Eliminación de una regla <a name ="delete-rule"> </a>

Cuando sea necesario, puede eliminar una regla recomendada de la sesión actual. Por ejemplo, puede determinar que aplicar una regla sugerida podría bloquear tráfico legítimo.

Para eliminar una regla de protección de red adaptable de la sesión actual:

- En la pestaña **Reglas**, seleccione los puntos suspensivos (…) al final de la fila de la regla y seleccione **Eliminar**.  

    ![Eliminación de una regla.](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)


## <a name="faq---adaptive-network-hardening"></a>Preguntas frecuentes sobre la protección de red adaptable

- [¿Qué puertos se admiten?](#which-ports-are-supported)
- [¿Hay requisitos previos o extensiones de máquina virtual necesarias para la protección de red adaptable?](#are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening)

### <a name="which-ports-are-supported"></a>¿Qué puertos se admiten?

Las recomendaciones de la protección de red adaptable solo se admiten en los siguientes puertos (para UDP y TCP): 

13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017 y 37215.

### <a name="are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening"></a>¿Hay requisitos previos o extensiones de máquina virtual necesarias para la protección de red adaptable?

La protección de red adaptable es una característica sin agente de Azure Security Center. No es necesario instalar nada en las máquinas para beneficiarse de esta herramienta de protección de red.

### <a name="when-should-i-use-a-deny-all-traffic-rule"></a>¿Cuándo debo usar una regla de tipo "Denegar todo el tráfico"?

Una regla tipo **Denegar todo el tráfico** se recomienda cuando, como resultado de la ejecución del algoritmo, Security Center no identifica el tráfico que se debe permitir, según la configuración de NSG existente. Por lo tanto, la regla recomendada es denegar todo el tráfico al puerto especificado. El nombre de este tipo de regla se muestra como "*Generada por el sistema*". Después de aplicar esta regla, su nombre real en el NSG será una cadena compuesta por el protocolo, la dirección del tráfico, "DENY" y un número aleatorio.