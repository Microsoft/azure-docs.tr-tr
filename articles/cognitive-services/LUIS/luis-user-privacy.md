---
title: Dışarı aktarma & verileri silme-LUSıS
titleSuffix: Azure Cognitive Services
description: Verilerini görüntüleme, dışarı aktarma ve silme üzerinde tam denetime sahip olursunuz. Gizlilik ve uyumluluk sağlamak için müşteri verilerini silin.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: a7a7bbb8b911f86b81cdeafe3fe57e1fdf820681
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228291"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Bilişsel hizmetler 'de müşteri verilerinizi Language Understanding (LUSıS) olarak dışa ve silme

Gizlilik ve uyumluluk sağlamak için müşteri verilerini silin.

## <a name="summary-of-customer-data-request-features"></a>Müşteri verileri isteme özelliklerinin özeti
Language Understanding Intelligent Service (LUSıS), hizmeti çalıştırmak için müşteri içeriğini korur, ancak LUO kullanıcısı verilerini görüntüleme, dışarı aktarma ve silme üzerinde tam denetime sahiptir. Bu, LUıS Web [portalı](luis-reference-regions.md) veya [LUIS yazma (programlı olarak da bilinir) API 'leri](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)aracılığıyla yapılabilir.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Müşteri içeriği, Microsoft bölgesel Azure depolama 'da şifrelenmiş olarak depolanır ve şunları içerir:

- Kayıt sırasında toplanan kullanıcı hesabı içeriği
- Modelleri derlemek için gereken eğitim verileri
- Modeli iyileştirmenize yardımcı olmak için [etkin öğrenme](luis-concept-review-endpoint-utterances.md) tarafından kullanılan günlüğe kaydedilmiş kullanıcı sorguları
  - Kullanıcılar isteğe ekleyerek sorgu günlüğünü kapatabilir `&log=false` ve [buradaki ayrıntıları burada](troubleshooting.md#how-can-i-disable-the-logging-of-utterances) bulabilirsiniz

## <a name="deleting-customer-data"></a>Müşteri verilerini silme
LUıS kullanıcıları, LUıS Web portalı ya da LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla herhangi bir Kullanıcı içeriğini silmek için tam denetime sahiptir. Aşağıdaki tabloda her ikisiyle yardımcı olan bağlantılar görüntülenmektedir:

| | **Kullanıcı Hesabı** | **Uygulama** | **Örnek söyleyler** | **Son Kullanıcı sorguları** |
| --- | --- | --- | --- | --- |
| **Portal** | [Bağlantısının](luis-concept-data-storage.md#delete-an-account) | [Bağlantısının](luis-how-to-start-new-app.md#delete-app) | [Bağlantısının](luis-concept-data-storage.md#utterances-in-an-intent) | [Etkin öğrenimi](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Günlüğe kaydedilen Utterslar](luis-concept-data-storage.md#disable-logging-utterances) |
| **API'ler** | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Müşteri verilerini dışa aktarma
LUıS kullanıcıları portalda verileri görüntülemek için tam denetime sahiptir, ancak bu, LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla verilmelidir. Aşağıdaki tabloda, LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla veri dışarı aktarmaları ile ilgili bağlantılar gösterilmektedir:

| | **Kullanıcı Hesabı** | **Uygulama** | **Utterance (s)** | **Son Kullanıcı sorguları** |
| --- | --- | --- | --- | --- |
| **API'ler** | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Bağlantısının](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Etkin öğrenimi konumu

[Etkin öğrenimi](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)etkinleştirmek için, yayımlanan Luo uç noktalarında alınan kullanıcıların günlüğe kaydettiği, aşağıdaki Azure coğrafi bölgelerde depolanır:

* [Avrupa](#europe)
* [Avustralya](#australia)
* [Birleşik Devletler](#united-states)

Etkin öğrenme verileri (aşağıda ayrıntılı) dışında, Lua, [Bölgesel hizmetler için veri depolama uygulamalarına](https://azuredatacentermap.azurewebsites.net/)uyar.

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Avrupa

Avrupa yazma (programlama API 'Leri olarak da bilinir) kaynakları Azure 'un Avrupa coğrafi konumunda barındırılır ve uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avrupa
* Fransa
* Birleşik Krallık

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan utmalslar, etkin öğrenme için Azure Avrupa coğrafi konumunda depolanır.

### <a name="australia"></a>Avustralya

Avustralya yazma (programlama API 'Leri olarak da bilinir) kaynakları Azure 'un Avustralya coğrafi konumunda barındırılır ve uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avustralya

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan araslar, etkin öğrenme için Azure 'ın Avustralya Coğrafya 'da depolanır.

### <a name="united-states"></a>Birleşik Devletler

Birleşik Devletler yazma (programlama API 'Leri olarak da bilinir) kaynakları Azure 'un Birleşik Devletler Coğrafya 'da barındırılır ve uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avrupa veya Avustralya yazma bölgeleri tarafından desteklenmeyen Azure coğrafi bölgeler

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan araslar, etkin öğrenme için Azure Birleşik Devletler Coğrafya 'da depolanır.

### <a name="switzerland-north"></a>İsviçre Kuzey

İsviçre Kuzey yazma (programlama API 'Leri olarak da bilinir) kaynakları, Azure 'un Isviçre coğrafi konumunda barındırılır ve uç noktaların aşağıdaki Azure coğrafi bölgelerde dağıtımını destekler:

* İsviçre 

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan söyleyler, etkin öğrenme için Azure 'un Isviçre Coğrafya 'da depolanır.

## <a name="disable-active-learning"></a>Etkin öğrenmeyi devre dışı bırak

Etkin öğrenmeyi devre dışı bırakmak için bkz. [etkin öğrenmeyi devre dışı bırakma](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Depolanan söyleymeleri yönetmek için bkz. [söylenişi 'i silme](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [LUıN bölgeleri başvurusu](./luis-reference-regions.md)
