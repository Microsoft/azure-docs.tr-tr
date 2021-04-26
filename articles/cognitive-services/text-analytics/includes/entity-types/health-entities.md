---
title: Sağlık için adlandırılmış varlıklar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599344"
---
[Sistem durumu işlemlerine metin analizi](../../how-tos/text-analytics-for-health.md) ve yapılandırılmamış tıp verilerinden öngörüleri ayıklar. Hizmet, tıp kavramlarını algılar ve yüzeyler, kavramlara onaylar atar ve kavramlar arasında semantik ilişkiler sağlar ve bunları ortak tıbbi ontoloyenlere bağlar.

Sistem durumu için Metin Analizi aşağıdaki kategorilerdeki tıp kavramlarını algılar. Bu önizlemede yalnızca Ingilizce metin desteklenir ve yalnızca tek bir model sürümü kullanılabilir.

| Kategori  | Açıklama  |
|---------|---------|
| [ANATOMISI](#anatomy) | gövde ve anamemik sistemleri, siteleri, konumları veya bölgeleri hakkındaki bilgileri yakalayan kavramlar. |
 | [DEMOGRAFIK BILGILERE](#demographics) | cinsiyet ve yaş hakkındaki bilgileri yakalayan kavramlar. |
 | [INCELEMESI](#examinations) | tanılama yordamları ve testler hakkında bilgi yakalayan kavramlar. |
 | [GENEL ÖZNITELIKLER](#general-attributes) | yukarıdaki kategorilerden diğer kavramlar hakkında daha fazla bilgi sağlayan kavramlar. |
 | [GENOMIKS](#genomics) | genes ve varyantlar hakkındaki bilgileri yakalayan kavramlar. |
 | [SAĞLıK](#healthcare) | yönetim olayları, ortamlar ve sağlık hizmetleri ile ilgili bilgileri yakalayan kavramlar. |
 | [TıBBI KOŞUL](#medical-condition) | tanılar, belirtiler veya imzalar hakkındaki bilgileri yakalayan kavramlar. |
 | [YOLLARA](#medication) | yollara adlarını, sınıfları, dozu ve yönetim yolunu içeren yollara hakkındaki bilgileri yakalayan kavramlar. |
 | [Ağ](#social) | aile ilişkisi gibi medically ilgili sosyal yönleri hakkında bilgi yakalayan kavramlar. |
 | [Düzeltme](#treatment) | karşılayan yordamlar hakkında bilgi yakalayan kavramlar. |

Aşağıda daha fazla bilgi ve örnek bulacaksınız.

## <a name="anatomy"></a>Anatomisi

### <a name="entities"></a>Varlıklar

**BODY_STRUCTURE** gövde sistemleri, anatomik konumlar veya bölgeler ve gövde siteleri. Örneğin, ARM, Knee, abdomen, burun, liver, baş, havemen sistemi, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Gövde yapısı varlığına bir örnek.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Gövde yapısı varlığının genişletilmiş bir örneği.":::

## <a name="demographics"></a>Demografik bilgiler

### <a name="entities"></a>Varlıklar

**Yaş** -hastalar, Aile üyeleri ve diğerleri dahil olmak üzere tüm yaş hüküm ve tümceleri. Örneğin, 40-yıl-eski, 51 Yo, 3 ay eski, Yetişkin, Kant, ELDERLY, Başak, Minor, orta-eski.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Yaş varlığına bir örnek.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Bir yaş varlığına ait başka bir örnek.":::


**Cinsiyeti** -konunun cinsiyetini açığa çıkarmaz. Örneğin, Erkek, kadın, kadın, genttaman, anım.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Cinsiyet varlığına bir örnek.":::

## <a name="examinations"></a>Examinations

### <a name="entities"></a>Varlıklar

**EXAMINATION_NAME** – önemli imzalar ve gövde ölçümleri dahil olmak üzere tanılama yordamları ve testleri. Örneğin, MRI, ECG, IV test, hemoglobin, platelets sayısı, *Bristol stool Scale* gibi sistemleri ölçeklendirin.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Bir sınav varlığına bir örnek.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="İnceleme adı varlığına ait başka bir örnek.":::

## <a name="general-attributes"></a>Genel öznitelikler

### <a name="entities"></a>Varlıklar

Sağlık koşulunun, İnceleme, işleme, yollara veya yönetim olayından ilgili **Tarih** -tam tarih.

**Yön** : sol, yan yana, büyük, posterior gibi bir gövde yapısı, tıbbi koşul, inceleme veya işleme ile ilişkilendirileme yönlü şartlar.

**Sıklık** -bir tıbbi koşulun, ince, işleme veya yollara oluşma, oluşma veya oluşma sıklığını açıklar.

**MEASUREMENT_VALUE** : bir inceleme veya tıbbi koşul ölçümüne ilişkin değer.

**MEASUREMENT_UNIT** : bir inceleme veya tıbbi koşul ölçümünün ilişkili ölçü birimi.

**RELATIONAL_OPERATOR** -bir varlık ve bazı ek bilgiler arasındaki nicel ilişkisini ifade eden ifadeler.

Sağlık koşulunun, incelemenizin, yollara veya yönetim olayının başlangıcı ve/veya uzunluğu (süresi) ile ilgili **zamana** bağlı şartlar. 

## <a name="genomics"></a>Genomiks

### <a name="entities"></a>Varlıklar

**GENE_OR_PROTEIN** : insan genlerinin adlarının ve simgelerinin yanı sıra, bermosomes ve protesto ve protestolu kısımlarından oluşan tüm bahsetmeler. Örneğin, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Bir gene varlığına bir örnek.":::

**Varyant** : gene varyasyonların ve mutasyonlarının tüm bahsetmeleri. Örneğin, `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Sağlık

### <a name="entities"></a>Varlıklar
  
**ADMINISTRATIVE_EVENT** – sağlık sistemiyle ilgili, yönetim/yarı yönetim doğası olan olaylar. Örneğin kayıt, giriş, deneme, çalışma girişi, aktarım, Boşalma, barındırma, barındırma 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Bir sağlık olayı varlığına bir örnek.":::

**CARE_ENVIRONMENT** : hastalara önem verilen bir ortam veya konum. Örneğin, acil bir oda, doktor ofisi, Cardio birimi, Hospice, hosplik.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Bu ekran görüntüsünde bir sağlık ortamı varlığının örneği gösterilmektedir.":::

**HEALTHCARE_PROFESSION** – bir sağlık hizmeti lisanslanmış veya lisanslı değil. Örneğin, dentist, pathologist, neurologist, Radiologist, ilaç macist, nutritionist, fiziksel terist, chirouygutor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Bu ekran görüntüsünde bir sağlık ortamı varlığının başka bir örneği gösterilmektedir.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Bir sağlık ortamı varlığına yönelik başka bir örnek.":::

## <a name="medical-condition"></a>Tıbbi koşul

### <a name="entities"></a>Varlıklar

**Tanılama** – dimevsimo, Syndrome, Kirme. Örneğin, breakst Cancer, Alzheimer, HTN, CHF, spuinal kablo yaralama.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Tıbbi koşul varlığına bir örnek.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Tıbbi koşul varlığına ait başka bir örnek.":::

**SYMPTOM_OR_SIGN** – öznel veya amaç bulgusunda veya diğer tanılar. Örneğin, çest sorunlu, headache, acsh, SOB, abdomen, yumuşak, sorunsuz sesler, iyi bir sorun oluştu.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Tıbbi koşul işareti veya belirti varlığına bir örnek.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Tıbbi koşul işareti veya belirti varlığına yönelik başka bir örnek.":::

Tıbbi bir koşulu tanımlamakta kullanılan **CONDITION_QUALIFIER** nitel terimleri. Aşağıdaki tüm alt kategoriler niteleyiciler olarak kabul edilir:

1.  Zaman ile ilgili ifadeler: qualitatively, ani, dar, tarih ve longgibi zaman boyutunu tanımlayan koşullardır. 
2.  Kalite ifadeleri: Bunlar, yazma, Sharp gibi tıbbi koşulun "yapısını" tanımlayan koşullardır.
3.  Önem derecesi ifadeleri: ciddi, hafif, bir bit, denetlenmeyen.
4.  Extensivity ifadeleri: yerel, odak, dağıtma.
5.  Taban ayırma ifadeleri: radyan, aydınlık.
6.  Koşul ölçeği: bazı durumlarda bir koşul, sınırlı sıralı değerler listesi olan bir ölçeğe göre belirlenir. Örneğin, III pantik Cancer adlı hastalar.
7.  Koşul kursu: geliştirme, çalışma, çözümleme, yeniden görev gibi bir koşulun kursla veya ilerleyişine ilişkin bir terim. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Koşul niteleyicisi özniteliği ve bir tanılama varlığına bir örnek.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Bir koşul niteleyicisi özniteliği ve bir tanılama varlığına ait başka bir örnek.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Belirti ve yollara varlıkları içeren bir koşul niteleyicisi özniteliği örneği.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Bu ekran görüntüsünde, bir tanılama varlığına sahip bir koşul niteleyicisi özniteliği örneği gösterilmektedir.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Bu ekran görüntüsünde, bir tanılama varlığına sahip bir koşul niteleyicisi özniteliğinin ek bir örneği gösterilir.":::

## <a name="medication"></a>Yollara

### <a name="entities"></a>Varlıklar

**MEDICATION_CLASS** : benzer bir eylem mekanizmasına sahip bir medications kümesi, ilgili bir eylem mekanizması, benzer bir cheyasal yapısı ve/veya aynı şekilde değerlendirmek için kullanılır. Örneğin, ACE, opioıd, antibiotik, sorun rahatlıcılar.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Yollara sınıf varlığına bir örnek.":::

**MEDICATION_NAME** – telif haklı marka adları ve marka olmayan adlar dahil olmak üzere, yollara bahsetmeler. Örneğin, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Bir yollara Name varlığına bir örnek.":::

**Dozu** -sıralanan yollara miktarı. Örneğin, Kordium Chlorıde çözümü *1000 ml*'yi kullanın.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Bir yollara dozu özniteliği örneği.":::

**MEDICATION_FORM** -yollara formu. Örneğin, çözüm, Pill, kapsül, tablet, Patch, gel, Paste, fohar, püskürtme, düşme, kremi, Syrup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Bir yollara form özniteliği örneği.":::

**MEDICATION_ROUTE** -yollara yönetim yöntemi. Örneğin, Oral, vaginal, IV, epidural, topical, intarled.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Bir yollara Route özniteliği örneği.":::

## <a name="social"></a>Sosyal

### <a name="entities"></a>Varlıklar

**FAMILY_RELATION** – ilgilinin aile akrabalarının bahsetme. Örneğin, Baba, kız, eşdüzey öğeler, ebeveynler.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Ekran görüntüsü bir işleme süresi özniteliğinin başka bir örneğini gösterir.":::

## <a name="treatment"></a>Düzeltme

### <a name="entities"></a>Varlıklar

**TREATMENT_NAME** – terxeutik yordamlar. Örneğin, Knee değiştirme Surgery, kemik Marrow transplant, TAVI, Diet.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Bir işleme adı varlığına bir örnek.":::
