---
title: 'Tutorial: Optimización de la administración centralizada de Ventaja híbrida de Azure para SQL Server'
description: Este tutorial le guía a través de la asignación proactiva de licencias de SQL Server en Azure para administrar y optimizar Ventaja híbrida de Azure.
author: bandersmsft
ms.author: banders
ms.date: 11/11/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: a8db95d33ae6398898108dfbf62a57b5b5f48d9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708317"
---
# <a name="tutorial-optimize-centrally-managed-azure-hybrid-benefit-for-sql-server"></a>Tutorial: Optimización de la administración centralizada de Ventaja híbrida de Azure para SQL Server

Este tutorial le guía en la asignación proactiva de licencias de SQL Server en Azure para optimizar la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) a medida que la administra de forma centralizada. La optimización de las ventajas reduce los costos de ejecución de Azure SQL.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Recopilar detalles de disponibilidad y uso de licencias
> * Comprar más licencias si es necesario
> * Asignar licencias a Azure
> * Supervisar el uso y ajustarlo
> * Establecer una programación de administración

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de lo siguiente:

Ha leído y comprendido el artículo [¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?](overview-azure-hybrid-benefit-scope.md) En el artículo se explican los tipos de licencias de SQL Server que son aptas para la Ventaja híbrida de Azure. También se explica cómo habilitar la ventaja para los ámbitos de suscripción o cuenta de facturación que seleccione.

> [!NOTE]
> La administración centralizada de la Ventaja híbrida de Azure en un nivel de ámbito se encuentra actualmente en versión preliminar pública, y está limitada a clientes empresariales y clientes que compran directamente desde Azure.com con un Contrato de cliente de Microsoft.

Compruebe que las máquinas virtuales instaladas automáticamente que ejecutan SQL Server en Azure están registradas antes de empezar a usar la nueva experiencia. De este modo, se garantiza que los recursos de Azure que ejecutan SQL Server sean visibles para los usuarios y Azure. Para más información sobre el registro de máquinas virtuales SQL en Azure, consulte [Registro de VM con SQL Server con la extensión Agente de IaaS de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) y [Registro de varias máquinas virtuales con SQL en Azure con la extensión Agente de IaaS de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).

## <a name="gather-license-usage-and-availability-details"></a>Recopilación de los detalles de disponibilidad y uso de licencias

_El primer paso es la preparación._ Interactúe con otros departamentos de su organización para comprender dos cosas:

- ¿Qué uso de SQL Server en Azure se espera durante el próximo período de tiempo de planeación?
- ¿Cuántas licencias principales de SQL Server con Software Assurance (o en suscripción) se compraron y están disponibles para asignarse a Azure?

Los detalles de uso reciente de Azure SQL detectados por el sistema se muestran al [crear asignaciones de licencias de SQL Server para la Ventaja híbrida de Azure](create-sql-license-assignments.md).

Se recomienda que consulte a las personas adecuadas de su organización para validar esa información y confirmar cualquier crecimiento planificado en el uso de SQL Server.

Un método opcional, pero útil, para investigar el uso de Azure SQL (incluido el uso de Ventaja híbrida de Azure en el nivel de recurso) consiste en usar el [script de PowerShell sql-license-usage](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) de Ventaja híbrida de Azure. Analiza y realiza un seguimiento del uso combinado de licencia de SQL Server de todos los recursos de SQL de una suscripción específica o de una cuenta completa.

### <a name="determine-the-number-of-eligible-sql-server-core-licenses-available-to-assign-to-azure"></a>Determinación del número de licencias principales de SQL Server aptas disponibles para asignar a Azure

La cantidad depende de cuántas licencias, con Software Assurance o suscripción, ha adquirido y cuántas ya están en uso fuera de Azure, normalmente en el entorno local.

Es probable que el departamento de administración de recursos de software o adquisición de software tenga esta información.

> [!TIP]
> Al migrar una carga de trabajo del entorno local a Azure, las licencias asociadas están disponibles para asignarse a Azure. Esto se debe a que, mientras usa la Ventaja híbrida de Azure, se le conceden 180 días de derechos de uso dual (en el entorno local + en Azure) para la licencia de SQL Server durante la migración. Esto le ayuda a asegurarse de que se ejecuten sin problemas.

## <a name="buy-more-licenses-if-needed"></a>Compra de más licencias si es necesario

Después de revisar la información recopilada, si decide que el número de licencias de SQL Server disponibles no es suficiente para cubrir el uso planeado de Azure SQL, hable con el Departamento de Adquisiciones para comprar más licencias básicas de SQL Server con Software Assurance (o licencias de suscripción).

Comprar licencias de SQL Server y aplicar Ventaja híbrida de Azure es menos costoso que pagar SQL Server por hora en Azure. Al comprar licencias suficientes para cubrir todo el uso planeado de Azure SQL, su organización maximizará el ahorro de costos de la ventaja.

## <a name="assign-licenses-to-azure"></a>Asignación de licencias a Azure

1. Siga las instrucciones de Azure Portal y la documentación para seleccionar al menos un ámbito y asignarle licencias de SQL Server. Para obtener más información, vea [Creación de licencias de SQL Server para Ventaja híbrida de Azure](create-sql-license-assignments.md).
2. A medida que asigne licencias, revise de nuevo el uso de Azure SQL detectado para comprobar que los detalles son coherentes con otra información recopilada.

## <a name="monitor-usage-and-adjust"></a>Supervisión del uso y ajuste

1. Vaya a **Cost Management + Billing** > **Reservas + Ventajas híbridas**.
1. Se muestra una tabla que incluye las asignaciones de licencias de Ventaja híbrida de Azure que ha realizado y el porcentaje de uso de cada una.
1. Si alguno de los porcentajes de uso es del 100 %, significa que su organización paga tarifas por hora por algunos recursos de SQL Server. Vuelva a interactuar con otros grupos de su organización para confirmar si los niveles de uso actuales son temporales o si continuarán. En este último caso, la organización debe considerar la posibilidad de adquirir más licencias y asignarlas a Azure para reducir el costo.
1. Si el uso se aproxima al 100 %, pero no lo supera, determine si se espera que el uso aumente a corto plazo. Si es así, puede adquirir y asignar más licencias de forma proactiva.

## <a name="establish-a-management-schedule"></a>Establecimiento de una programación de administración

En la sección anterior se describe la supervisión en curso. También se recomienda establecer una programación anual o trimestral que siga repetidamente. La programación incluye los pasos principales descritos en el artículo:

- Recopilación de los detalles de disponibilidad y uso de licencias.
- Compra de más licencias si es necesario para cubrir el próximo crecimiento del uso.
- Asignación de licencias a Azure.
- Supervisión del uso y ajuste sobre la marcha, según sea necesario.
- Repita el proceso cada año o con la frecuencia que mejor se adapte a sus necesidades.

## <a name="example-walkthrough"></a>Tutorial de ejemplo

En el ejemplo siguiente, suponga que es el administrador de facturación de la compañía de seguros de Contoso. Puede administrar Ventaja híbrida de Azure de Contoso para SQL Server.

El departamento de adquisiciones le informa de que puede administrar Ventaja híbrida de Azure para SQL Server en el nivel de cuenta general. El departamento de adquisiciones recibió esta información del equipo de cuentas de Microsoft. Le interesa porque ha sido difícil administrar Ventaja híbrida de Azure últimamente. En parte, porque los desarrolladores han estado habilitando la ventaja (o no) arbitrariamente en los recursos a medida que comparten scripts entre sí.

Busque la nueva experiencia de Ventaja híbrida de Azure en el área Cost Management + Billing de Azure Portal.

Después de leer las instrucciones anteriores del artículo, comprenda lo siguiente:

  - Contoso debe registrar las máquinas virtuales con SQL Server antes de realizar otras acciones.
  - La manera ideal de usar la nueva funcionalidad es asignar licencias de forma proactiva para cubrir el uso esperado.

Luego, siga estos pasos.

1. Use las instrucciones anteriores para asegurarse de que las máquinas virtuales con SQL están registradas. Se incluye hablar con los propietarios de la suscripción para completar el registro de las suscripciones en las que no tiene permisos suficientes.
1. Revise los datos de uso de recursos de Azure de los últimos meses y hable con otros usuarios de Contoso. Se determina que, para cubrir el uso esperado de Azure SQL para el próximo año, son necesarias 2000 licencias principales de SQL Server Enterprise Edition y 750 de SQL Server Standard Edition, u 8750 licencias principales normalizadas. El uso esperado también incluye la migración de cargas de trabajo (1500 de SQL Server Enterprise Edition + 750 de SQL Server Standard Edition = 6750 normalizadas) y cargas de trabajo nuevas de Azure SQL (otras 500 licencias principales de SQL Server Enterprise Edition o 2000 normalizadas).
1. A continuación, confirme con el equipo de adquisiciones que las licencias necesarias ya están disponibles o que pronto se comprarán. La confirmación garantiza que las licencias están disponibles para asignarse a Azure.
   - Las licencias que tiene en uso local se pueden considerar disponibles para asignarse a Azure si las cargas de trabajo asociadas se migran a Azure. Como se mencionó anteriormente, Ventaja híbrida de Azure permite el uso dual durante un máximo de 180 días.
   - Se determina que hay 1800 licencias de SQL Server Enterprise Edition y 2000 licencias de SQL Server Standard Edition disponibles para asignar a Azure. Las licencias disponibles son iguales a 9200 licencias principales normalizadas. Esto es un poco más que las 8750 licencias necesarias (2000 x 4 + 750 = 8750).
1. A continuación, asigne 1800 licencias de SQL Server Enterprise Edition y 2000 licencias de SQL Server Standard Edition a Azure. Esta acción da como resultado 9200 licencias principales normalizadas que el sistema puede aplicar a los recursos de Azure SQL a medida que se ejecutan cada hora. La asignación de más licencias de las necesarias ahora proporciona un búfer si el uso crece más rápido de lo esperado.

Después, debe supervisar el uso de licencias asignadas periódicamente, idealmente mensualmente. Después de 10 meses, el uso se aproxima al 95 %, lo que indica que el crecimiento del uso de Azure SQL va más rápido de lo esperado. Hable con el equipo de adquisiciones para obtener más licencias para poder asignarlas.

Por último, adopte una programación de revisión de licencias anual. En el proceso de revisión,debe realizar lo siguiente:

- Recopilar y analizar los datos de uso de licencias.
- Confirmar la disponibilidad de licencias.
- Trabajar con el equipo de adquisiciones para obtener más licencias, si es necesario.
- Actualizar asignaciones de licencia.
- Supervisar a lo largo del tiempo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre la [Transición de la administración del nivel de ámbito de la Ventaja híbrida de Azure](transition-existing.md).
- Consulte las [Preguntas frecuentes sobre la administración centralizada de Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).