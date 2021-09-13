---
title: Entidades con nombre para la asistencia sanitaria
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: 1477e81d3809a0712913c95f54d4c073f08bb362
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550774"
---
[Text Analytics for Health](../../how-tos/text-analytics-for-health.md) procesa y extrae información de datos médicos no estructurados. El servicio detecta y expone conceptos médicos, asigna aserciones a los conceptos, deduce las relaciones semánticas entre los conceptos y las vincula a ontologías médicas comunes.

Text Analytics for Health detecta conceptos médicos en las siguientes categorías. En la actualidad solo se admite texto en inglés.

| Category  | Descripción  |
|---------|---------|
| [ANATOMÍA](#anatomy) | conceptos que capturan información sobre el cuerpo y los sistemas anatómicos, los sitios, las ubicaciones o las regiones. |
 | [DATOS DEMOGRÁFICOS](#demographics) | conceptos que capturan información sobre el sexo y la edad. |
 | [EXAMEN MÉDICO](#examinations) | conceptos que capturan información acerca de los procedimientos de diagnóstico y las pruebas. |
 | [INFLUENCIA EXTERNA](#external-influence) | conceptos relacionados con factores externos pertinentes desde el aspecto médico, como los alérgenos.|
 | [ATRIBUTOS GENERALES](#general-attributes) | conceptos que proporcionan más información sobre otros conceptos de las categorías anteriores. |
 | [GENÓMICA](#genomics) | conceptos que capturan información sobre los genes y las variantes. |
 | [ATENCIÓN SANITARIA](#healthcare) | conceptos que capturan información sobre eventos administrativos, entornos sanitarios y profesiones sanitarias. |
 | [ENFERMEDADES](#medical-condition) | conceptos que capturan información sobre diagnósticos, síntomas o signos. |
 | [MEDICACIÓN](#medication) | conceptos que capturan información sobre la medicación, incluidos los nombres de la misma, sus clases, la dosis y la forma de administración. |
 | [SOCIAL](#social) | conceptos que capturan información sobre aspectos sociales de interés médico, como la relación de la familia. |
 | [TRATAMIENTO](#treatment) | conceptos que capturan información acerca de los procedimientos terapéuticos. |

Obtenga más información y ejemplos a continuación.

## <a name="anatomy"></a>Anatomía

### <a name="entities"></a>Entidades

**BODY_STRUCTURE**: sistemas del cuerpo, ubicaciones o regiones anatómicas y sitios de cuerpo. Por ejemplo, brazo, rodilla, abdomen, nariz, hígado, cabeza, sistema respiratorio, linfocitos.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Ejemplo de la entidad de la estructura del cuerpo." lightbox="../../media/ta-for-health/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>Demografía

### <a name="entities"></a>Entidades

**AGE**: todos los términos y las frases de edad, incluidos los de pacientes, familiares y otros usuarios. Por ejemplo, 40 años, 51 años, 3 meses, adulto, lactante, anciano, joven, menor de edad, mediana edad.

**GENDER**: términos que divulgan el sexo del sujeto. Por ejemplo, masculino, femenino, mujer, señor, señora.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Ejemplo de una entidad de edad." lightbox="../../media/ta-for-health/age-entity.png":::

## <a name="examinations"></a>Exámenes médicos

### <a name="entities"></a>Entidades

**EXAMINATION_NAME**: procedimientos y pruebas de diagnóstico, incluidos los signos vitales y las medidas del cuerpo. Por ejemplo, resonancia magnética, electrocardiograma, test de VIH, hemoglobina, recuento de plaquetas, sistemas de escala como la *Escala de heces de Bristol*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Ejemplo de una entidad de examen." lightbox="../../media/ta-for-health/exam-name-entities.png":::

## <a name="external-influence"></a>Influencia externa

### <a name="entities"></a>Entidades

**ALLERGEN**: antígeno que desencadena una reacción alérgica. Por ejemplo, gatos o cacahuetes.

:::image type="content" source="../../media/ta-for-health/external-influence-allergen.png" alt-text="Ejemplo de una entidad de influencia externa." lightbox="../../media/ta-for-health/external-influence-allergen.png":::


## <a name="general-attributes"></a>Atributos generales

### <a name="entities"></a>Entidades

**COURSE**: descripción de un cambio en otra entidad a lo largo del tiempo, como la progresión de la condición (por ejemplo, mejora, regresión, resolución, remisión), el transcurso de un tratamiento o medicación (por ejemplo, aumento de la regresión de los síntomas). 

:::image type="content" source="../../media/ta-for-health/course-entity.png" alt-text="Ejemplo de una entidad de transcurso." lightbox="../../media/ta-for-health/course-entity.png":::

**DATE**: fecha completa relativa a una enfermedad, un examen, un tratamiento, una medicación o un evento administrativo.

:::image type="content" source="../../media/ta-for-health/date-entity.png" alt-text="Ejemplo de una entidad de fecha." lightbox="../../media/ta-for-health/date-entity.png":::

**DIRECTION**: términos direccionales que pueden referirse a una estructura corporal, una enfermedad, un examen o un tratamiento, por ejemplo: izquierda, lateral, superior, posterior.

:::image type="content" source="../../media/ta-for-health/direction-entity.png" alt-text="Ejemplo de una entidad de dirección." lightbox="../../media/ta-for-health/direction-entity.png":::

**FREQUENCY**: describe la frecuencia con la que se produce una enfermedad, un examen, un tratamiento o una medicación.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Ejemplo de un atributo de frecuencia de medicación." lightbox="../../media/ta-for-health/medication-form.png":::


**TIME**: términos temporales relacionados con el inicio o la longitud (duración) de una enfermedad, un examen, un tratamiento, una medicación o un evento administrativo. 

**MEASUREMENT_UNIT**: la unidad de medida relacionada con la medición de un examen o una enfermedad.

**MEASUREMENT_VALUE**: el valor relacionado con la medición de un examen o una enfermedad.

:::image type="content" source="../../media/ta-for-health/measurement-value-entity.png" alt-text="Ejemplo de una entidad de valor de medida." lightbox="../../media/ta-for-health/measurement-value-entity.png":::

**RELATIONAL_OPERATOR**: frases que expresan la relación cuantitativa entre una entidad e información adicional.

:::image type="content" source="../../media/ta-for-health/measurement-unit.png" alt-text="Ejemplo de una entidad de valor de medida." lightbox="../../media/ta-for-health/measurement-unit.png"::: 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entidades

**VARIANT**: todas las menciones de las variaciones y mutaciones genéticas. Por ejemplo, `c.524C>T`, `(MTRR):r.1462_1557del96`
  
**GENE_OR_PROTEIN**: todas las menciones de los nombres y símbolos de los genes humanos, así como los cromosomas y las partes de los cromosomas y las proteínas. Por ejemplo, MTRR, F2.

**MUTATION_TYPE**: descripción de la mutación, incluido su tipo, efecto y ubicación. Por ejemplo, trisomía, mutación de la línea germinal o pérdida de función.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Ejemplo de una entidad génica." lightbox="../../media/ta-for-health/genomics-entities.png":::

**EXPRESSION**: nivel de expresión del gen. Por ejemplo, positivo, negativo, sobreexpresado, detectado en niveles altos o bajos, elevado.

:::image type="content" source="../../media/ta-for-health/expression.png" alt-text="Ejemplo de una entidad de expresión del gen." lightbox="../../media/ta-for-health/expression.png":::


## <a name="healthcare"></a>Salud

### <a name="entities"></a>Entidades
  
**ADMINISTRATIVE_EVENT**: eventos relacionados con el sistema sanitario pero con una naturaleza administrativa o semiadministrativa. Por ejemplo, registro, admisión, evaluación, entrada de estudio, transferencia, alta, hospital, estancia en el hospital. 

**CARE_ENVIRONMENT**: un entorno o una ubicación donde se proporciona atención a los pacientes. Por ejemplo, sala de emergencias, oficina del doctor, unidad de cardio, hospital de cuidados paliativos, hospital.

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Ejemplo de una entidad de eventos de atención sanitaria." lightbox="../../media/ta-for-health/healthcare-event-entity.png" :::

**HEALTHCARE_PROFESSION**: un médico con licencia o sin licencia. Por ejemplo, dentista, patólogo, neurólogo, radiólogo, farmacéutico, nutricionista, fisioterapeuta, quiropráctico.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Otro ejemplo de una entidad de entorno de atención sanitaria." lightbox="../../media/ta-for-health/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>Enfermedades

### <a name="entities"></a>Entidades

**DIAGNOSIS**: enfermedad, síndrome, intoxicación. Por ejemplo, cáncer de mama, Alzheimer, hipertensión, insuficiencia cardíaca, lesiones en la espina dorsal.

**SYMPTOM_OR_SIGN**: evidencia subjetiva u objetiva de la enfermedad u otros diagnósticos. Por ejemplo, dolor de pecho, dolor de cabeza, mareos, sarpullido, disnea, el abdomen está relajado, buen sonido abdominal, bien nutrido.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Ejemplo de una entidad de enfermedad." lightbox="../../media/ta-for-health/medical-condition-entity.png":::

**CONDITION_QUALIFIER**: términos cualitativos que se usan para describir una enfermedad. Todas las subcategorías siguientes se consideran calificadores:

1.  Expresiones relacionadas con el tiempo: son los términos que describen la dimensión de tiempo cualitativamente, como repentino, agudo, crónico y duradero. 
2.  Expresiones de calidad:  Son términos que describen la "naturaleza" de la enfermedad, como quemaduras o intenso.
3.  Expresiones de gravedad: grave, leve, moderado, incontrolado.
4.  Expresiones de extensión: local, focal, difuso.

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Esta captura de pantalla muestra otro ejemplo de un atributo calificador de enfermedad con una entidad de diagnóstico." lightbox="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" :::

**CONDITION_SCALE**: términos cualitativos que caracterizan la condición por una escala, que es una lista de valores finita y ordenada.

:::image type="content" source="../../media/ta-for-health/condition-scale-example.png" alt-text="Otro ejemplo de un atributo calificador de enfermedad y una entidad de diagnóstico." lightbox="../../media/ta-for-health/condition-scale-example.png":::

## <a name="medication"></a>Medicación

### <a name="entities"></a>Entidades

**MEDICATION_CLASS**: conjunto de medicamentos que tienen un mecanismo de acción similar, un modo de acción relacionado, una estructura química similar o que se usan para tratar la misma enfermedad. Por ejemplo, el inhibidor de ACE, opioides, antibióticos y analgésicos.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Ejemplo de una entidad de clase de medicación." lightbox="../../media/ta-for-health/medication-entities-class.png":::

**MEDICATION_NAME**: menciones a la medicación, incluidos los nombres de marca con copyright y los nombres sin marca. Por ejemplo, Ibuprofeno.

**DOSAGE**: cantidad de medicación solicitada. Por ejemplo, inocular *1000 mL* de una solución de cloruro sódico.

**MEDICATION_FORM**: la forma del medicamento. Por ejemplo, solución, pastilla, cápsula, tableta, parche, gel, pasta, espuma, aerosol, gotas, crema y jarabe.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Ejemplo de un atributo de dosis de medicación." lightbox="../../media/ta-for-health/medication-dosage.png":::

**MEDICATION_ROUTE**: el método de administración de la medicación. Por ejemplo, oral, tópica, inhalada.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Ejemplo de un atributo de forma de medicación." lightbox="../../media/ta-for-health/medication-form.png"::: 

## <a name="social"></a>Redes sociales

### <a name="entities"></a>Entidades

**FAMILY_RELATION**: menciones de familiares relativas al sujeto. Por ejemplo, padre, hija, hermanos, progenitores.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Ejemplo de una entidad de relación de familia." lightbox="../../media/ta-for-health/family-relation.png":::

## <a name="treatment"></a>Tratamiento

### <a name="entities"></a>Entidades

**TREATMENT_NAME**: procedimientos terapéuticos. Por ejemplo, cirugía de sustitución de rodilla, trasplante de médula ósea, implante valvular aórtico transcatéter, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Ejemplo de una entidad de nombre de tratamiento." lightbox="../../media/ta-for-health/treatment-entities-name.png":::


## <a name="supported-assertions"></a>Aserciones admitidas

Los modificadores de aserción se dividen en tres categorías, cada una de las cuáles se centra en un aspecto diferente.
Cada categoría contiene un conjunto de valores mutuamente excluyentes. Solo se asigna un valor por categoría a cada entidad. El valor más común de cada categoría es el valor predeterminado. La respuesta de salida del servicio solo contiene modificadores de aserciones que son diferentes del valor predeterminado.

### <a name="certainty"></a>Certeza  

Proporciona información sobre la presencia (presencia frente a ausencia) del concepto y la certeza del texto en relación con su presencia (cierta frente a posible).

**Positive** (valor predeterminado): el concepto existe o se ha producido.

**Negative**: el concepto no existe o no se ha producido nunca.

:::image type="content" source="../../media/ta-for-health/negative-entity.png" alt-text="Ejemplo de una entidad negativa." lightbox="../../media/ta-for-health/negative-entity.png":::

**Positive_Possible**: probablemente el concepto existe, pero existe alguna incertidumbre.

:::image type="content" source="../../media/ta-for-health/positive-possible-entity.png" alt-text="Ejemplo de una entidad posible positiva." lightbox="../../media/ta-for-health/positive-possible-entity.png" :::

**Negative_Possible**: la existencia del concepto es improbable pero existe alguna incertidumbre.

:::image type="content" source="../../media/ta-for-health/negative-possible-entity.png" alt-text="Ejemplo de una entidad posible negativa." lightbox="../../media/ta-for-health/negative-possible-entity.png" :::

**Neutral_Possible**: el concepto puede existir o no, sin tener a ninguna de estas opciones.

:::image type="content" source="../../media/ta-for-health/neutral-possible-entity.png" alt-text="Ejemplo de una entidad posible neutra." lightbox="../../media/ta-for-health/neutral-possible-entity.png":::

### <a name="conditionality"></a>Condicionalidad

Proporciona información sobre si la existencia de un concepto depende de determinadas condiciones. 

**None** (valor predeterminado): el concepto es un hecho, no hipotético y no depende de determinadas condiciones.

**Hypothetical**: el concepto puede desarrollarse o producirse en el futuro.

:::image type="content" source="../../media/ta-for-health/hypothetical-entity.png" alt-text="Ejemplo de una entidad hipotética." lightbox="../../media/ta-for-health/hypothetical-entity.png":::

**Conditional**: el concepto existe o solo se produce en determinadas condiciones.

:::image type="content" source="../../media/ta-for-health/conditional-entity.png" alt-text="Ejemplo de una entidad condicional." lightbox="../../media/ta-for-health/conditional-entity.png":::

### <a name="association"></a>Asociación

Describe si el concepto está asociado al sujeto del texto o a otra persona.

**Subject** (valor predeterminado): el concepto está asociado al sujeto del texto, normalmente el paciente.

**Someone_Else**: el concepto está asociado a alguien que no es el sujeto del texto.

:::image type="content" source="../../media/ta-for-health/association-entity.png" alt-text="Ejemplo de una entidad de asociación." lightbox="../../media/ta-for-health/association-entity.png":::

