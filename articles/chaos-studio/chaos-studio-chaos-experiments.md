---
title: Experimentos del caos en Azure Chaos Studio
description: Obtendrá información sobre el concepto de un experimento del caos en Azure Chaos Studio. ¿De qué partes consta un experimento del caos? ¿Cómo se crea un experimento del caos?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: ef71d6d09d1965eefd7008dd8c3d020c0a8ae1ae
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371550"
---
# <a name="chaos-experiments"></a>Experimentos del caos

En Chaos Studio se crean y ejecutan experimentos del caos. Un experimento del caos es un recurso de Azure que describe los errores que se deben ejecutar y los recursos en los que se deben ejecutar esos errores. Un experimento se divide en dos secciones:
- **Selectores**: los selectores son grupos de recursos de destino en los que se ejecutan errores u otras acciones. Un selector permite agrupar de forma lógica los recursos para reutilizarlos en varias acciones. Por ejemplo, puede tener un selector denominado "AllNonProdEastUSVMs" en el que se hayan agregado todas las máquinas virtuales que no son de producción en el Este de EE. UU. Después, puede aplicar presión de CPU seguida de presión de memoria virtual en esas máquinas virtuales; para ello, solo tiene que hacer referencia al selector.
- **Lógica**: el resto del experimento describe cómo y cuándo ejecutar errores. Un experimento se organiza en **pasos** que se ejecutan uno después del otro. Cada paso consta de una o más **ramas** que se ejecutan a la vez. Los pasos y las ramas permiten inyectar varios errores en todos los recursos de su entorno de forma paralela. Cada rama tiene una o más **acciones** que son o los errores que se quieren ejecutar o retrasos de tiempo. Los **errores** son acciones que generan alguna interrupción. La mayoría de los errores toman uno o varios **parámetros**, como la duración, para ejecutar el error o la cantidad de esfuerzo que se debe aplicar.

![Diagrama que muestra el diseño de un experimento del caos.](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>Experimentos entre suscripciones y entre inquilinos

Un experimento del caos es un recurso de Azure implementado en una suscripción, un grupo de recursos y una región. Puede usar Azure Portal o la API REST de Chaos Studio para crear, actualizar, iniciar, cancelar y ver el estado de un experimento.

Los experimentos del caos pueden estar orientados a recursos de una suscripción distinta a la del experimento siempre que la suscripción esté dentro del mismo inquilino de Azure. Dichos experimentos pueden estar orientados a recursos de una suscripción distinta a la del experimento siempre que la región sea una de las compatibles con Chaos Studio.

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe qué es un experimento del caos, puede:
- [Información sobre errores y acciones](chaos-studio-faults-actions.md)
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct-portal.md)
