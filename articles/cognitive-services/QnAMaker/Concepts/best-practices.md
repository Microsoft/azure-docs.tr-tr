---
title: En iyi uygulamalar - soru-cevap Oluşturucu
titleSuffix: Azure Cognitive Services
description: Bilgi bankanızı artırmak ve uygulama/sohbet Robotu ait son kullanıcılara daha iyi sonuçlar sağlamak için bu en iyi uygulamaları kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: da587aa51b1a8aee609a5bfd04ce0d7f24ca0e5f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955250"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Soru-cevap Oluşturucu Bilgi Bankası en iyi yöntemleri

[Bilgi Bankası geliştirme yaşam döngüsü](../Concepts/development-lifecycle-knowledge-base.md) başlangıçtan bitişe kadar KB yönetme konusunda size yol gösterir. Bilgi bankanızı geliştirmek ve istemci uygulamanıza veya sohbet bot 'un son kullanıcılarına daha iyi sonuçlar sağlamak için bu en iyi yöntemleri kullanın.

## <a name="extraction"></a>Ayıklama

Soru-cevap Oluşturucu hizmetini sürekli olarak içerik ve desteklenen dosya ve HTML biçimleri listesini genişleterek Bankalarıyla ayıklamak algoritmaları gelişiyor. İzleyin [yönergeleri](../Concepts/data-sources-supported.md) için belge türüne göre veri ayıklama. 

Genel olarak, SSS sayfaları, tek başına ve diğer bilgilerle birleşik olması gerekir. Ürün kılavuzlarını, NET başlıklar ve tercihen dizin sayfası olması gerekir. 

### <a name="configuring-multi-turn"></a>Çoklu açmayı yapılandırma

Çoklu açma ayıklama özelliği etkinken Bilgi Bankası 'nizi oluşturun. Bilgi tabanınız soru hiyerarşisini destekliyorsa veya destekliyorsa, bu hiyerarşi belgeden ayıklanamaz veya belge ayıklandıktan sonra oluşturulmuş olabilir. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>İyi sorular ve yanıtlar oluşturma

### <a name="good-questions"></a>İyi sorular

En iyi sorular basittir. Her soru için anahtar sözcük veya tümceciği göz önünde bulundurun ve ardından bu anahtar sözcük veya tümceciği için basit bir soru oluşturun. 

İhtiyaç duyduğunuz sayıda alternatif soru ekleyin, ancak değişiklikleri basit tutun. Sorunun ana hedefinin parçası olmayan daha fazla sözcük veya amaç eklemek Soru-Cevap Oluşturma bir eşleşme bulmaya yardımcı olmaz. 


### <a name="add-relevant-alternative-questions"></a>İlgili alternatif soruları ekleyin

Kullanıcılarınız bir metin `How do I add a toner cartridge to my printer?` stili veya gibi bir anahtar sözcük araması `toner cartridge`ile soru girebilir. Bilgi Bankası, en iyi yanıtı doğru bir şekilde döndürmek için her iki türde soruya sahip olmalıdır. Müşterinin hangi anahtar kelimelerle girileceği emin değilseniz, sorguları çözümlemek için Application Insights veri kullanın.

### <a name="good-answers"></a>İyi yanıtlar

En iyi yanıtlar basit yanıtlardan oluşur ancak çok basittir. `yes` Ve`no`gibi yanıtları kullanmayın. Yanıtlarınızın diğer kaynaklara bağlanması veya medya ve bağlantılarla zengin bir deneyim sağlaması gerekiyorsa, yanıtlar arasında ayrım yapmak için [meta veri etiketlemesini](./knowledge-base.md#key-knowledge-base-concepts) kullanın, ardından [](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) doğru yanıtı almak için `strictFilters` özelliği özelliğindeki meta veri etiketleriyle birlikte gönder Sürüm.

## <a name="chit-chat"></a>Chit sohbet
Botunuzun daha damıtarak konuşma bağlamında kullanılabilen ve ilgi çekici, botunuzun yapmak için chit sohbet ekleme az çaba. KB 'nizi oluştururken önceden tanımlanmış kişiselleştirimler aracılığıyla kolayca CHIT-chat veri kümeleri ekleyebilir ve bunları dilediğiniz zaman değiştirebilirsiniz. Bilgi edinmek için nasıl [chit sohbet eklemek için KB](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Bir kişilik seçme
Chit-sohbet, önceden tanımlanmış birkaç kişisel için desteklenir: 

|Kişilik |Soru-Cevap Oluşturma veri kümesi dosyası |
|---------|-----|
|Profesyonel |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Uygun |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Wtty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Giriş |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Heyecanlandırdı |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Yanıtları aralıktan biçimsel resmi olmayan ve saygısız. En yakın botunuz için istediğiniz sesi birlikte hizalanır kişilik seçmeniz gerekir. [Veri kümelerini](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)görüntüleyebilir ve botunuzun temeli olarak işlev gören bir tane seçebilir ve ardından yanıtları özelleştirebilirsiniz. 

### <a name="edit-bot-specific-questions"></a>Bot özgü soruları Düzenle
Sohbet chit veri kümesinin parçası olan ve genel yanıtlar oturum doldurulmuş bazı bot özgü soruları vardır. Bu yanıtlar, en iyi bot ayrıntılarınızı yansıtacak şekilde değiştirin. 

Aşağıdaki sohbet chit Bankalarıyla daha belirgin hale öneririz:

* Kimsiniz?
* Ne yapabilirsiniz?
* Kaç yaşındasınız?
* Kim, oluşturduğunuz?
* Merhaba
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Meta veri etiketiyle özel chit-sohbet ekleme

Kendi CHIT-chat QnA çiftlerinden birini eklerseniz, Bu yanıtların döndürüldüğünden meta veri eklediğinizden emin olun. Meta veri adı/değer çifti `editorial:chitchat`.

## <a name="searching-for-answers"></a>Yanıtlar aranıyor

GenerateAnswer API 'SI, bir kullanıcının sorgusuna en iyi yanıtları aramak için hem soruları hem de yanıtı kullanır.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Yalnızca yanıt ilgili olmadığında soruları arama

[`RankerType=QuestionOnly`](#choosing-ranker-type) Yanıtları aramak istemiyorsanız öğesini kullanın. 

Bu, Bilgi Bankası 'nın, yanıt olarak tam biçimiyle soru olarak kısaltmalar olarak bir katalog olduğu bir örnektir. Yanıtın değeri, uygun yanıtı aramanıza yardımcı olmaz.

## <a name="rankingscoring"></a>Derecelendirme ve puanlı
Soru-cevap Oluşturucu desteklediği derecelendirme özellikleri en iyi kullanımı kuran emin olun. Bunun yapılması olasılığına artıracak belirli kullanıcı sorgusu ile uygun bir yanıt yanıtlandığı.

### <a name="choosing-a-threshold"></a>Bir eşiği seçme

Eşik olarak kullanılan varsayılan [Güvenirlik puanı](confidence-score.md) 50 ' dir, ancak GEREKSINIMLERINIZE göre KB 'nizin [eşiğini değiştirebilirsiniz](confidence-score.md#set-threshold) . Her KB farklı olduğundan, test edin ve en iyi eşiği için KB uygun seçin gerekir. 

### <a name="choosing-ranker-type"></a>Ranker türü seçiliyor
Varsayılan olarak, Soru-Cevap Oluşturma sorular ve yanıtlar arasında arama yapar. Yalnızca sorulardan arama yapmak istiyorsanız, yanıt oluşturmak için generateanswer isteğinin gönderi gövdesinde öğesini `RankerType=QuestionOnly` kullanın.

### <a name="add-alternate-questions"></a>Diğer sorular ekleyin
[Diğer sorular](../How-To/edit-knowledge-base.md) bir kullanıcı sorgu ile bir eşleşme olasılığını artırın. Diğer sorular, aynı soruyu istenebilir birden çok yol olduğunda yararlıdır. Word-style ve cümle yapısı içinde bu değişiklikleri içerebilir.

|Özgün sorgu|Diğer sorgular|Değiştir| 
|--|--|--|
|Kullanılabilir park olan?|Araba park var mı?|cümle yapısı|
 |Merhaba|Yo<br>Hey var!|Word stil veya argo kullanımlar|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Soruları ve yanıtları filtrelemek için meta veri etiketlerini kullanma

[Meta veriler](../How-To/edit-knowledge-base.md) , bir istemci uygulamanın tüm yanıtları getirmemelidir, ancak meta veri etiketlerine göre bir Kullanıcı sorgusunun sonuçlarını daraltmak için bu özelliği ekler. Sorgu aynı olsa bile, Bilgi Bankası yanıt meta veri etiketine göre değişebilir. Örneğin, *"nerede park yeri"* , Restoran dalının konumu farklıysa farklı bir yanıt alabilir. Bu, meta veriler konum olur *: Seattle*vekonum:  *Redmond*.

### <a name="use-synonyms"></a>Eş anlamlıları kullanma
Ingilizce dilde eş anlamlı sözcükler için bazı destek olsa da, farklı biçimde olan anahtar sözcüklere eşanlamlı ekler eklemek için [alterler API 'si](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) aracılığıyla büyük/küçük harf duyarsız sözcük değişikliklerini kullanın. Eş anlamlılar Soru-Cevap Oluşturma hizmet düzeyinde eklenir ve hizmetteki tüm bilgi tabanları tarafından paylaşılır.

|Özgün word|Eş anlamlılar|
|--|--|
|Satın alma|satın al<br>net-bankacılık<br>NET bankacılık|

### <a name="use-distinct-words-to-differentiate-questions"></a>Sorular ayırt etmek için farklı sözcükler kullanın
Bilgi bankasındaki bir soru ile bir kullanıcı sorgusuyla eşleşen derecelendirme algoritması, her bir soru farklı bir gereksinim ele alıyorsa en iyi şekilde geçerlidir. Soru-Cevap Oluşturma Yineleme sorular arasında ayarlanmış aynı kelimenin doğru yanıt sözcükleri ile verilen kullanıcı sorgusu için seçilen olasılığını azaltır. 

Örneğin, aşağıdaki soruları ile iki ayrı Bankalarıyla olabilir:

|Bankalarıyla|
|--|
|Park olduğu *konumu*|
|Burada ATM *konumudur*|

Bu iki Bankalarıyla çok benzer kelimelerinizle tümcecik oluşturulmuş olduğundan, bu gibi tümcecik oluşturulmuş çok sayıda kullanıcı sorgu çok benzer puanları neden olabilecek *"nerede olduğunu `<x>` konumu"* . Bunun yerine, KB 'unuzda birçok soruda olabilecek "konum" gibi kelimeleri önleyerek " *Park partisi olan* " ve *"ATM*olduğu" gibi sorguları açıkça ayırt etmeye çalışın. 

## <a name="collaborate"></a>İşbirliği yapın
Soru-cevap Oluşturucu, kullanıcılara sağlayan [işbirliği](../How-to/collaborate-knowledge-base.md) Bilgi Bankası üzerinde. Kullanıcıların bilgi bankalarından erişmek için soru-cevap Oluşturucu Azure kaynak grubuna erişim gerekir. Bazı kuruluşlar Bilgi Bankası düzenleme ve Bakım dış ve Azure kaynaklarına erişim hala koruyabilmesini isteyebilirsiniz. Bu düzenleyici onaylayan modeli iki özdeş ayarlayarak yapılır [soru-cevap Oluşturucu Hizmetleri](../How-to/set-up-qnamaker-service-azure.md) farklı Aboneliklerde ve bir düzenleme test döngüsü için seçme. Test tamamlandıktan sonra Bilgi Bankası içerikleri ile aktarılır bir [içeri / dışarı aktarma](../Tutorials/migrate-knowledge-base.md) son Bilgi Bankası yayımlama ve uç noktayı güncelleştirmek onaylayanın soru-cevap Oluşturucu hizmeti için işlem.



## <a name="active-learning"></a>Etkin öğrenme

[Etkin öğrenme](../How-to/improve-knowledge-base.md) , çok sayıda kalite ve Kullanıcı tabanlı sorgu miktarına sahip olduğunda alternatif soruların önermesinin en iyi İşlidir. İstemci uygulamalarının Kullanıcı sorgularının, censorgemesiz etkin öğrenme geri bildirim döngüsüne katılmasını sağlamak önemlidir. Soru-Cevap Oluşturma portalında sorular Önerildikten sonra, **[önerilere göre filtreleyerek](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** bu önerileri inceleyip kabul edebilir veya reddedebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi Bankası Düzenle](../How-to/edit-knowledge-base.md)
