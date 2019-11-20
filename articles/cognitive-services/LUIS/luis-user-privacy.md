---
title: Dışarı aktarma & verileri silme-LUSıS
titleSuffix: Azure Cognitive Services
description: Verilerini görüntüleme, dışarı aktarma ve silme üzerinde tam denetime sahip olursunuz. Gizlilik ve uyumluluk sağlamak için müşteri verilerini silin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 061f019fe36e4d5495a41fc81e56d9673ad595fc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953451"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Language Understanding (LUIS) Bilişsel hizmetler, müşteri verilerini silebilir ve dışarı aktarma

Gizlilik ve uyumluluk sağlamak için müşteri verilerini silin. 

## <a name="summary-of-customer-data-request-features"></a>Müşteri verilerini talep özelliklerin özeti
Language Understanding Intelligent Service (LUIS) hizmeti çalıştırmak için müşteri içerik korur, ancak LUIS kullanıcı görüntüleme, verme ve verileri silme üzerinde tam denetime sahiptir. Bu, LUıS Web [portalı](luis-reference-regions.md) veya [LUIS yazma (programlı olarak da bilinir) API 'leri](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)aracılığıyla yapılabilir.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Müşteri içeriği, Microsoft bölgesel Azure depolamada şifrelenmiş olarak depolanır ve içerir:

- Kayıt sırasında toplanan kullanıcı hesabı içerik
- Modelleri derlemek için gereken eğitim verileri
- Modeli iyileştirmenize yardımcı olmak için [etkin öğrenme](luis-concept-review-endpoint-utterances.md) tarafından kullanılan günlüğe kaydedilmiş kullanıcı sorguları
  - Kullanıcılar, sorgu günlüğünü devre dışı ekleyerek kapatabilir `&log=false` isteğine ayrıntıları [burada](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Müşteri verileri silme
LUıS kullanıcıları, LUıS Web portalı ya da LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla herhangi bir Kullanıcı içeriğini silmek için tam denetime sahiptir. Aşağıdaki tabloda, her ikisi de Yardım bağlantıları görüntüler:

| | **Kullanıcı hesabı** | **Uygulama** | **Örnek söyleyler** | **Son kullanıcı sorguları** |
| --- | --- | --- | --- | --- |
| **Portal** | [Bağlantı](luis-concept-data-storage.md#delete-an-account) | [Bağlantı](luis-how-to-start-new-app.md#delete-app) | [Bağlantı](luis-concept-data-storage.md#utterances-in-an-intent) | [Etkin öğrenimi](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Günlüğe kaydedilen Utterslar](luis-concept-data-storage.md#disable-logging-utterances) |
| **API'ler** | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Müşteri verilerini dışarı aktarma
LUıS kullanıcıları portalda verileri görüntülemek için tam denetime sahiptir, ancak bu, LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla verilmelidir. Aşağıdaki tabloda, LUıS yazma (programlı olarak da bilinir) API 'Leri aracılığıyla veri dışarı aktarmaları ile ilgili bağlantılar gösterilmektedir:

| | **Kullanıcı hesabı** | **Uygulama** | **Utterance(s)** | **Son kullanıcı sorguları** |
| --- | --- | --- | --- | --- |
| **API'ler** | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Bağlantı](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Etkin öğrenimi konumu

[Etkin öğrenimi](luis-how-to-review-endpoint-utterances.md#enable-active-learning)etkinleştirmek için, yayımlanan Luo uç noktalarında alınan kullanıcıların günlüğe kaydettiği, aşağıdaki Azure coğrafi bölgelerde depolanır:

* [Avrupa](#europe)
* [Avustralya](#australia)
* [Birleşik Devletler](#united-states)

Etkin öğrenme verileri (aşağıda ayrıntılı) dışında, Lua, [Bölgesel hizmetler için veri depolama uygulamalarına](https://azuredatacentermap.azurewebsites.net/)uyar. 

### <a name="europe"></a>Avrupa

[Eu.Luis.ai](https://eu.luis.ai) portalı ve Avrupa yazma (programlı API 'ler olarak da bilinir), Azure 'un Avrupa coğrafi konumunda barındırılır. Eu.luis.ai portalı ve Avrupa yazma (programlı API 'Ler olarak da bilinir), uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avrupa
* Fransa
* Birleşik Krallık

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan utmalslar, etkin öğrenme için Azure Avrupa coğrafi konumunda depolanır. Etkin öğrenmeyi devre dışı bırakabilirsiniz, bkz. [etkin öğrenmeyi devre dışı](luis-how-to-review-endpoint-utterances.md#disable-active-learning)bırakma. Depolanan söyleymeleri yönetmek için bkz. [söylenişi 'i silme](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Avustralya

[Au.Luis.ai](https://au.luis.ai) portalı ve Avustralya yazma (programlı API 'ler olarak da bilinir), Azure 'un Avustralya coğrafi konumunda barındırılır. Au.luis.ai portalı ve Avustralya yazma (programlı API 'Ler olarak da bilinir), uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avustralya

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan araslar, etkin öğrenme için Azure 'ın Avustralya Coğrafya 'da depolanır. Etkin öğrenmeyi devre dışı bırakabilirsiniz, bkz. [etkin öğrenmeyi devre dışı](luis-how-to-review-endpoint-utterances.md#disable-active-learning)bırakma. Depolanan söyleymeleri yönetmek için bkz. [söylenişi 'i silme](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Amerika Birleşik Devletleri

[Luis.ai](https://www.luis.ai) portalı ve Birleşik Devletler yazma (programlı API 'ler olarak da bilinir), Azure 'un Birleşik Devletler Coğrafya içinde barındırılır. Luis.ai portalı ve Birleşik Devletler yazma (programlı API 'Ler olarak da bilinir), uç noktaların aşağıdaki Azure coğrafi noktalara dağıtımını destekler:

* Avrupa veya Avustralya yazma bölgeleri tarafından desteklenmeyen Azure coğrafi bölgeler

Bu Azure coğrafi noktalara dağıtım yaparken, uygulamanızın son kullanıcılarından gelen uç nokta tarafından alınan araslar, etkin öğrenme için Azure Birleşik Devletler Coğrafya 'da depolanır. Etkin öğrenmeyi devre dışı bırakabilirsiniz, bkz. [etkin öğrenmeyi devre dışı](luis-how-to-review-endpoint-utterances.md#disable-active-learning)bırakma. Depolanan söyleymeleri yönetmek için bkz. [söylenişi 'i silme](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [LUIS bölgeleri başvurusu](./luis-reference-regions.md)
