---
title: Bilgi Bankası 'nın yaşam döngüsü-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-cevap oluşturma, bir model değişikliği, söylenişi örnekleri, yayımlama ve uç nokta sorgularından veri toplamayı en iyi şekilde öğrenir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794852"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Soru-Cevap Oluşturma Bilgi Bankası yaşam döngüsü
Soru-cevap oluşturma, bir model değişikliği, söylenişi örnekleri, yayımlama ve uç nokta sorgularından veri toplamayı en iyi şekilde öğrenir. 

![Yazma döngüsü](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Soru-Cevap Oluşturma bilgi tabanı oluşturma
Soru-Cevap Oluşturma Bilgi Bankası (KB) uç noktası, KB içeriğine göre bir Kullanıcı sorgusuna en iyi eşleşme yanıtı sağlar. Bilgi Bankası oluşturma, soruların, yanıtların ve ilişkili meta verilerin bir içerik deposunu ayarlamaya yönelik tek seferlik bir işlemdir. SSS sayfaları, ürün el kitapları veya yapılandırılmış Q-A çiftleri gibi önceden var olan içerikler gezilirken bir Bilgi Bankası oluşturulabilir. [Bilgi Bankası oluşturma](../quickstarts/create-publish-knowledge-base.md)hakkında bilgi edinin.

## <a name="testing-and-updating-the-knowledge-base"></a>Bilgi Bankası 'nı test etme ve güncelleştirme

Bilgi Bankası, içeriğe göre veya otomatik ayıklama yoluyla doldurulduktan sonra test etmeye hazırdır. Etkileşimli test, **Test** paneli aracılığıyla soru-cevap oluşturma portalında, ortak kullanıcı sorguları girerek ve yanıtların doğru yanıt ve yeterli Güvenirlik puanı ile döndürülen yanıtları doğrulayarak yapılabilir. 

* **Düşük güvenirlik puanlarını onarmak için**: alternatif sorular ekleyin. 
* **Sorgu yanlış bir şekilde [varsayılan yanıtı](confidence-score.md#change-default-answer)döndürdüğünde**: doğru soruya yeni yanıtlar ekleyin. 

Bu sıkı test-güncelleştirme döngüsü, sonuçlardan memnun olana kadar devam eder. [Bilgi Bankalarınızı test](../How-To/test-knowledge-base.md)etme hakkında bilgi edinin.

Büyük KBs için, [Generateanswer API 'si](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) ile otomatik test ve yayımlanan bilgi tabanı yerine `test` Bilgi Bankası ' nı sorgulayan `isTest` Body özelliğini kullanın. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Bilgi bankasını yayımlama
Bilgi bankasını test etmeyi tamamladıktan sonra yayımlayabilirsiniz. Yayımlama, sınanan Bilgi Bankası 'nın en son sürümünü **yayınlanan** Bilgi Bankası 'nı temsil eden adanmış bir Azure bilişsel arama dizinine gönderir. Ayrıca uygulamanızda veya sohbet botunuzda çağrılabilecek bir uç nokta da oluşturulur.

Bu şekilde, Bilgi Bankası 'nın test sürümünde yapılan tüm değişiklikler, bir üretim uygulamasında canlı olabilecek yayımlanmış sürümü etkilemez.

Bu bilgi tabanlarının her biri, test etmek için ayrı olarak hedeflenmelidir. API 'Leri kullanarak, Bilgi Bankası 'nın test sürümünü generateAnswer çağrısında `isTest` Body özelliği ile hedefleyebilirsiniz.

[Bilgi tabanınızı yayımlamayı](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)öğrenin.

## <a name="monitor-usage"></a>Kullanımı izleme
Hizmetinizin sohbet günlüklerini günlüğe kaydedebilmek için, [soru-cevap oluşturma hizmetinizi](../How-To/set-up-qnamaker-service-azure.md)oluştururken Application Insights etkinleştirmeniz gerekir.

Hizmet kullanımınızın çeşitli analizlerini alabilirsiniz. Application Insights 'ı kullanarak [soru-cevap oluşturma hizmetiniz için analiz](../How-To/get-analytics-knowledge-base.md)alma hakkında daha fazla bilgi edinin.

Analizinizden öğrendiklerinize bağlı olarak, [bilgi tabanınızdan uygun güncelleştirmeleri](../How-To/edit-knowledge-base.md)yapın.

## <a name="version-control-of-a-knowledge-base"></a>Bilgi Bankası 'nın sürüm denetimi

Sürüm denetimi Soru-Cevap Oluşturma tarafından sağlanmaz. Bilgi bankanızı **Ayarlar** sayfasından dışa aktarmanız ve kendi Yöntemlerinizi ve araçlarınızı kullanmanız gerekir.

Bilgi Bankası 'nı TSV veya XLS biçimine aktarmak, **Ayarlar** sayfasından tamamlanır. 

Belirli bir sürüme geri dönmelisiniz, bu dosyayı yerel sisteminizden içeri aktarmanız gerekir. **Ayarlar** SAYFASıNDAN, TSV veya xls dosyasını içeri aktarın. Bu, bilgi bankasındaki soruların ve yanıtların içeri aktarılan dosyanın içeriğiyle yerini alır.   

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenirlik puanı](./confidence-score.md)