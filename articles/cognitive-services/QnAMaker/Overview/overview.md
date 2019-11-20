---
title: Soru-Cevap Oluşturma Hizmeti nedir?
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma, verileriniz üzerinde kolayca doğal bir konuşma katmanı oluşturan bulut tabanlı bir NLP hizmetidir. Özel bilgi tabanınızdan (KB), belirli bir doğal dil girişi için en uygun yanıtı bulmak üzere kullanılabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
ms.openlocfilehash: d647875895e33254b51fb8c3d11aa40c6c1ed71f
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973800"
---
# <a name="what-is-the-qna-maker-service"></a>Soru-Cevap Oluşturma Hizmeti nedir?

Soru-Cevap Oluşturma, verileriniz üzerinde kolayca doğal bir konuşma katmanı oluşturan bulut tabanlı doğal dil Işleme (NLP) hizmetidir. Özel bilgi tabanınızdan (KB), belirli bir doğal dil girişi için en uygun yanıtı bulmak üzere kullanılabilir.

Soru-Cevap Oluşturma istemci uygulaması, bir soruyu yanıtlamak için doğal dilde bir kullanıcıyla iletişim kuran herhangi bir konuşma uygulamasıdır. İstemci uygulamalarına örnek olarak sosyal medya uygulamaları, sohbet botları ve konuşma özellikli masaüstü uygulamaları verilebilir.

## <a name="when-to-use-qna-maker"></a>Ne zaman kullanılacağı Soru-Cevap Oluşturma

* **Statik bilgileriniz varsa** -bilgi yanıtlarınızın bilgi bankasında statik bilgileriniz varsa soru-cevap oluşturma kullanın. Bu Bilgi Bankası, [PDF 'ler ve URL 'ler](../concepts/data-sources-supported.md)gibi belgelerle oluşturduğunuz gereksinimlerinize göre özel olarak tasarlanmıştır.
* **Bir istek, soru veya komuta aynı yanıtı sağlamak istediğinizde** , farklı kullanıcılar aynı soruyu gönderdiğinde, her ikisine de aynı yanıt döndürülür. 
* **Meta bilgilere göre statik bilgileri filtrelemek istediğinizde,** istemci uygulamanızın kullanıcılarına ve bilgilerine uygun ek filtreleme seçenekleri sağlamak için [meta veri](../how-to/metadata-generateanswer-usage.md) etiketleri ekleyin. Ortak meta veri bilgileri, [CHIT-chat](../how-to/chit-chat-knowledge-base.md), içerik türü veya biçimi, içerik amacı ve içerik yeniliği içerir.
* **Statik bilgiler içeren bir bot iletişimini yönetmek istediğinizde,** bilginiz bir kullanıcının konuşma metin veya komutunu alır ve yanıt verir. Yanıt, bilgi bankasında [Çoklu oturum açma](../how-to/multiturn-conversation.md)ile temsil edilen önceden belirlenmiş bir konuşma akışının parçasıysa, bu akışı kolayca sağlayabilir.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Sohbet bot 'ta Soru-Cevap Oluşturma Bilgi Bankası 'nı kullanma

Bir Soru-Cevap Oluşturma Bilgi Bankası yayımlandığında, istemci uygulaması Bilgi Bankası uç noktanıza soru gönderir ve sonuçları JSON yanıtı olarak alır. Soru-Cevap Oluşturma için ortak bir istemci uygulaması, sohbet bot ' dır.

![Bilgi Bankası içeriğisinden bir yardım alın ve yanıt alın](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Adım|Eylem|
|:--|:--|
|1|İstemci uygulaması, kullanıcının _sorusunu_ (metni kendi kelimelerimi) gönderir, "nasıl yaparım? program aracılığıyla bilgi bankamı Güncelleştir" Bilgi Bankası uç noktanıza.|
|2|Soru-Cevap Oluşturma, doğru yanıtı ve en iyi yanıtı aramayı iyileştirmek için kullanılabilecek herhangi bir izleme istemini sağlamak için eğitilen Bilgi Bankası 'nı kullanır. Soru-Cevap Oluşturma JSON biçimli bir yanıt döndürür.|
|3|İstemci uygulaması, konuşmayı sürdürmek üzere kararlar almak için JSON yanıtını kullanır. Bu kararlar, en iyi yanıt aramasını iyileştirmek için en iyi yanıtı göstermeyi veya daha fazla seçenek sunmayı içerebilir. |
|||

## <a name="what-is-a-knowledge-base"></a>Bilgi Bankası nedir? 

Soru-Cevap Oluşturma [içeriğinizi](../concepts/data-sources-supported.md) soru-cevap kümelerinin bilgi tabanına aktarır. İçeri aktarma işlemi, soru ve yanıt kümeleri arasındaki ilişkileri belirleyen, yapılandırılmış ve yarı yapılandırılmış içeriğinizin parçaları arasındaki ilişki hakkındaki bilgileri ayıklar. Bu soru ve cevap kümelerini düzenleyebilir veya yenilerini ekleyebilirsiniz.  

Soru ve yanıt kümesi içeriği, belirli bir yanıt için diğer tüm soruları, arama sırasında yanıt seçimlerini filtrelemek için kullanılan meta veri etiketlerini ve arama iyileştirmesi devam ettirmek için izleme istemlerini içerir.

![Meta veriler ile örnek soru ve yanıt](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Bilgi tabanınızı yayımladıktan sonra bir istemci uygulaması, bir kullanıcının sorusunu uç noktanıza gönderir. Soru-Cevap Oluşturma hizmetiniz soruyu işler ve en iyi Yanıtla yanıt verir. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Kod olmadan bir bot oluşturun, yönetin ve yayımlayın

Soru-Cevap Oluşturma Portal, tüm Bilgi Bankası yazma deneyimini sağlar. Belge bilgilerini geçerli biçiminde bilgi tabanınızı içeri aktarabilirsiniz. Bu belgeler (SSS, ürün el kitabı, elektronik tablo veya Web sayfası gibi), soru ve yanıt kümelerine dönüştürülür. Her küme, izleme istemleri için çözümlenir ve diğer kümelere bağlanır. Son markaşağı biçimi, görüntüler ve bağlantılar dahil zengin sunumu destekler. 

Bilgi tabanınız düzenlendikten sonra, Bilgi Bankası 'nı herhangi bir kod yazmadan çalışan bir [Azure Web uygulaması bot](https://azure.microsoft.com/services/bot-service/) 'a yayımlayın. [Azure Portal](https://portal.azure.com) , botunuzu test edin veya geliştirmeye devam edin. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Arama kalitesi ve derecelendirmesi olası en iyi yanıtı sağlar

Soru-Cevap Oluşturma sistemi katmanlı bir derecelendirme yaklaşımıdır. Veriler, Azure Search 'te depolanır ve bu da ilk derecelendirme katmanı olarak da kullanılır. Azure Search 'un en iyi sonuçları, son sonuçları ve güvenirlik Puanını üretmek için Soru-Cevap Oluşturma NLP yeniden derecelendirme modelinden geçirilir.

## <a name="qna-maker-improves-the-conversation-process"></a>Soru-Cevap Oluşturma konuşma sürecini geliştirir

Soru-Cevap Oluşturma, basit soru ve yanıt kümelerinizi iyileştirmenize yardımcı olmak için çok yönlü istemler ve etkin öğrenme sağlar. 

**Çoklu açma istemleri** , soru ve yanıt çiftlerini bağlama fırsatı sağlar. Bu bağlantı, istemci uygulamasının en iyi yanıtı sağlamasına ve nihai yanıt aramasını iyileştirmek için daha fazla soru sunmasına olanak tanır. 

Bilgi Bankası, yayımlanan bitiş noktasındaki kullanıcılardan gelen soruları aldıktan sonra Soru-Cevap Oluşturma, bu gerçek dünyadaki sorulara, kaliteyi artırmak için bilgi bankasında değişiklik önermek üzere **etkin öğrenme** uygular. 

## <a name="development-lifecycle"></a>Geliştirme yaşam döngüsü

Soru-Cevap Oluşturma, tam geliştirme yaşam döngüsü ile tümleştirme için birlikte çalışma izinleriyle birlikte yazma, eğitim ve yayımlama olanağı sağlar. 

## <a name="how-do-i-start"></a>Kullanmaya nasıl başlayabilirim?

**1. adım**: [Azure Portal](https://portal.azure.com)soru-cevap oluşturma kaynak oluşturma. 

**2. adım**: [soru-cevap oluşturma](https://www.qnamaker.ai) portalında Bilgi Bankası oluşturma. Bilgi Bankası oluşturmak için [dosya ve URL 'ler](../concepts/data-sources-supported.md) ekleyin.  

**Adım 3**: [kıvrımlı](../quickstarts/get-answer-from-kb-using-curl.md) veya [Postman](../quickstarts/get-answer-from-kb-using-postman.md)kullanarak özel uç noktanıza bilgi bankasını ve testinizi yayımlayın. 

**4. adım**: istemci uygulamanızdan, programlı olarak bilgi tabanınızın bitiş noktasını ÇAĞıRıN ve JSON yanıtını okuyarak kullanıcıya en iyi yanıtı görüntüleyin.  

## <a name="next-steps"></a>Sonraki adımlar
Soru-Cevap Oluşturma, özel bilgi tabanınızı oluşturmak, yönetmek ve dağıtmak için ihtiyacınız olan her şeyi sağlar. 

> [!div class="nextstepaction"]
> [En son değişiklikleri gözden geçirin](../whats-new.md)
