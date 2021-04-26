---
title: En iyi yöntemler-Soru-Cevap Oluşturma
description: Bilgi bankaınızı geliştirmek ve uygulamanıza/sohbet bot 'ın son kullanıcılarına daha iyi sonuçlar sağlamak için bu en iyi yöntemleri kullanın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b09c36f1dce3c394ff24e3e601d773279a6ac510
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102123318"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Soru-Cevap Oluşturma Bilgi Bankası 'nın en iyi yöntemleri

[Bilgi Bankası geliştirme yaşam döngüsü](../Concepts/development-lifecycle-knowledge-base.md) , KB 'nizi baştan sona nasıl yönetebileceğinizi size rehberlik eder. Bilgi bankanızı geliştirmek ve istemci uygulamanıza veya sohbet bot 'un son kullanıcılarına daha iyi sonuçlar sağlamak için bu en iyi yöntemleri kullanın.

## <a name="extraction"></a>Ayıklama

Soru-Cevap Oluşturma hizmet, içerikten QnAs çıkaran ve desteklenen dosya ve HTML biçimlerinin listesini genişlettirecek algoritmaları sürekli olarak geliştirir. Belge türüne göre veri ayıklama [yönergelerini](../Concepts/data-sources-and-content.md) izleyin.

Genel olarak, SSS sayfaları tek başına olmalıdır ve diğer bilgilerle birleştirilmemelidir. Ürün kılavuzları, açık başlıklar ve tercihen bir dizin sayfası içermelidir.

### <a name="configuring-multi-turn"></a>Çoklu açmayı yapılandırma

Çoklu açma ayıklama özelliği etkinken [Bilgi Bankası 'Nizi oluşturun](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) . Bilgi tabanınız soru hiyerarşisini destekliyorsa veya destekliyorsa, bu hiyerarşi belgeden ayıklanamaz veya belge ayıklandıktan sonra oluşturulmuş olabilir.

## <a name="creating-good-questions-and-answers"></a>İyi sorular ve yanıtlar oluşturma

### <a name="good-questions"></a>İyi sorular

En iyi sorular basittir. Her soru için anahtar sözcük veya tümceciği göz önünde bulundurun ve ardından bu anahtar sözcük veya tümceciği için basit bir soru oluşturun.

İhtiyaç duyduğunuz sayıda alternatif soru ekleyin, ancak değişiklikleri basit tutun. Sorunun ana hedefinin parçası olmayan daha fazla sözcük veya amaç eklemek Soru-Cevap Oluşturma bir eşleşme bulmaya yardımcı olmaz.


### <a name="add-relevant-alternative-questions"></a>İlgili alternatif soruları ekleyin

Kullanıcılarınız bir metin stili `How do I add a toner cartridge to my printer?` veya gibi bir anahtar sözcük araması ile soru girebilir `toner cartridge` . Bilgi Bankası, en iyi yanıtı doğru bir şekilde döndürmek için her iki türde soruya sahip olmalıdır. Müşterinin hangi anahtar kelimelerle girileceği emin değilseniz, sorguları çözümlemek için Application Insights veri kullanın.

### <a name="good-answers"></a>İyi yanıtlar

En iyi yanıtlar basit yanıtlardan oluşur ancak çok basittir. Ve gibi yanıtları kullanmayın `yes` `no` . Yanıtlarınızın diğer kaynaklara bağlanması veya medya ve bağlantılarla zengin bir deneyim sağlaması gerekiyorsa, yanıtlar arasında ayrım yapmak için [meta veri etiketlemesini](../how-to/edit-knowledge-base.md#add-metadata) kullanın, ardından [](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) `strictFilters` doğru yanıt sürümünü almak için sorguyu özelliğindeki meta veri etiketleriyle birlikte gönderebilirsiniz.

|Yanıt|İzleme istemleri|
|--|--|
|Klavye üzerindeki güç düğmesiyle yüzey dizüstü bilgisayarınızı kapatın.|* Uyku, kapatma ve yeniden başlatma için anahtar birleşimleri.<br>* Bir yüzey dizüstü bilgisayarı nasıl sabit bir şekilde Önyükle<br>* Yüzey dizüstü bilgisayar için BIOS 'U değiştirme<br>* Uyku, kapatma ve yeniden başlatma arasındaki farklar|
|Müşteri Hizmetleri, günde 24 saat boyunca telefon, Skype ve kısa mesaj yoluyla kullanılabilir.|* Sales için iletişim bilgileri.<br> * Office ve mağaza konumları ve saatleri bir kişi için ziyaret edin.<br> * Bir yüzey dizüstü bilgisayar için aksesuarlar.|

## <a name="chit-chat"></a>Chit-Chat
Botunuza, düşük çabayla daha fazla konuşma ve ilgi çekici hale getirmek için Chit sohbeti ekleyin. KB 'nizi oluştururken önceden tanımlanmış kişiselleştirimler aracılığıyla kolayca CHIT-chat veri kümeleri ekleyebilir ve bunları dilediğiniz zaman değiştirebilirsiniz. [KB 'nize Chit sohbeti ekleme](../How-To/chit-chat-knowledge-base.md)hakkında bilgi edinin.

Chit-sohbet [birçok dilde](../how-to/chit-chat-knowledge-base.md#language-support)desteklenmektedir.

### <a name="choosing-a-personality"></a>Kişilik seçme
Chit-sohbet, önceden tanımlanmış birkaç kişisel için desteklenir:

|Kişilik |Soru-Cevap Oluşturma veri kümesi dosyası |
|---------|-----|
|Professional |[qna_chitchat_professional. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Uygun |[qna_chitchat_friendly. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Wtty |[qna_chitchat_witty. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Giriş |[qna_chitchat_caring. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Heyecanlandırdı |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Yanıtlar, resmi olmayan ve resmi olmayan ve geri alınamaz olarak değişir. Bot için istediğiniz ton ile en yakın olan kişiliğinizi seçmelisiniz. [Veri kümelerini](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)görüntüleyebilir ve botunuzun temeli olarak işlev gören bir tane seçebilir ve ardından yanıtları özelleştirebilirsiniz.

### <a name="edit-bot-specific-questions"></a>Bot 'a özgü soruları Düzenle
CHIT-chat veri kümesinin parçası olan ve genel yanıtlarla doldurulmuş bazı bot 'a özgü sorular vardır. Bu yanıtları, bot ayrıntılarınızı en iyi şekilde yansıtacak şekilde değiştirin.

Aşağıdaki CHIT-chat QnAs ' i daha belirgin yapmanızı öneririz:

* Kimsiniz?
* Ne yapabilirsiniz?
* Kaç yaşındasın?
* Kim oluşturdunuz?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Meta veri etiketiyle özel chit-sohbet ekleme

Kendi CHIT-chat QnA çiftlerinden birini eklerseniz, Bu yanıtların döndürüldüğünden meta veri eklediğinizden emin olun. Meta veri adı/değer çifti `editorial:chitchat` .

## <a name="searching-for-answers"></a>Yanıtlar aranıyor

GenerateAnswer API 'SI, bir kullanıcının sorgusuna en iyi yanıtları aramak için hem soruları hem de yanıtı kullanır.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Yalnızca yanıt ilgili olmadığında soruları arama

Yanıtları aramak istemiyorsanız öğesini kullanın [`RankerType=QuestionOnly`](#choosing-ranker-type) .

Bu, Bilgi Bankası 'nın, yanıt olarak tam biçimiyle soru olarak kısaltmalar olarak bir katalog olduğu bir örnektir. Yanıtın değeri, uygun yanıtı aramanıza yardımcı olmaz.

## <a name="rankingscoring"></a>Derecelendirme/Puanlama
Soru-Cevap Oluşturma, ' nin desteklediği derecelendirme özelliklerinin en iyi kullanımını yaptığınızdan emin olun. Bunun yapılması, belirli bir Kullanıcı sorgusunun uygun bir Yanıtla cevaplanması olasılığını artırır.

### <a name="choosing-a-threshold"></a>Eşik seçme

Eşik olarak kullanılan varsayılan [Güvenirlik puanı](confidence-score.md) 0 ' dır, ancak GEREKSINIMLERINIZE göre KB 'nizin [eşiğini değiştirebilirsiniz](confidence-score.md#set-threshold) . Her KB farklı olduğundan, KB 'niz için en uygun eşiği test etmeniz ve seçmeniz gerekir.

### <a name="choosing-ranker-type"></a>Ranker türü seçiliyor
Varsayılan olarak, Soru-Cevap Oluşturma sorular ve yanıtlar arasında arama yapar. Yalnızca sorulardan arama yapmak istiyorsanız, yanıt oluşturmak için `RankerType=QuestionOnly` GenerateAnswer ISTEĞININ gönderi gövdesinde öğesini kullanın.

### <a name="add-alternate-questions"></a>Alternatif sorular ekleyin
[Alternatif sorular](../How-To/edit-knowledge-base.md) , bir kullanıcı sorgusuyla eşleşme olasılığını artırır. Diğer sorular, aynı sorunun sorulabileceği birden çok yol olduğunda faydalıdır. Bu, tümce yapısında ve Word stilinde değişiklikler içerebilir.

|Özgün sorgu|Alternatif sorgular|Değiştir|
|--|--|--|
|Park, kullanılabilir mi?|Araba Park musunuz?|tümce yapısı|
 |N|Hecesi<br>Merhaba!|sözcük stili veya argo|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Soruları ve yanıtları filtrelemek için meta veri etiketlerini kullanma

[Meta veriler](../How-To/edit-knowledge-base.md) , bir istemci uygulamanın tüm yanıtları getirmemelidir, ancak meta veri etiketlerine göre bir Kullanıcı sorgusunun sonuçlarını daraltmak için bu özelliği ekler. Bilgi Bankası yanıtı, sorgu aynı olsa bile meta veri etiketine göre farklılık gösterebilir. Örneğin, *"nerede park yeri"* , Restoran dalının konumu farklıysa farklı bir yanıta sahip olabilir. Bu, meta veriler *Konum: Seattle* ve *Konum: Redmond*.

### <a name="use-synonyms"></a>Eş anlamlıları kullan
# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)
Ingilizce dilde eş anlamlı sözcükler için bazı destek olsa da, farklı formlar kullanan anahtar sözcüklere eş anlamlılar eklemek için [alterler API 'si](/rest/api/cognitiveservices/qnamaker/alterations/replace) aracılığıyla büyük/küçük harf duyarsız sözcük değişikliklerini kullanın. Eş anlamlılar Soru-Cevap Oluşturma hizmet düzeyinde eklenir ve hizmetteki **tüm bilgi tabanları tarafından paylaşılır**.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
Ingilizce dilde eş anlamlı sözcükler için bazı destek olsa da, farklı formlar kullanan anahtar sözcüklere eş anlamlılar eklemek için [alterler API 'si](/rest/api/cognitiveservices/qnamaker/alterations/replace) aracılığıyla büyük/küçük harf duyarsız sözcük değişikliklerini kullanın. Soru-Cevap Oluşturma yönetilen (Önizleme) eş anlamlılar **bilgileri her Bilgi Bankası için eklenmiştir**.

|Özgün sözcük|Eş Anlamlı Sözcükler|
|--|--|
|satıcıdan|satın alma<br>net-bankacılık<br>net bankacılık|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Soruları ayırt etmek için ayrı sözcükler kullanın
Bilgi bankasındaki bir soru ile bir kullanıcı sorgusuyla eşleşen derecelendirme algoritması, her bir soru farklı bir gereksinim ele alıyorsa en iyi şekilde geçerlidir. Soru-Cevap Oluşturma Sorular arasındaki aynı sözcük kümesinin yinelemesi, bu sözcüklerin bulunduğu belirli bir Kullanıcı sorgusu için doğru yanıtın seçilme olasılığını azaltır.

Örneğin, aşağıdaki sorularla iki ayrı QnAs olabilir:

|QnAs|
|--|
|Park *konumu* nerede|
|Burada ATM *konumudur*|

Bu iki QnAs, çok benzer sözcüklerle phrased olduğundan, bu benzerlik,  *"konum olduğu yer `<x>` "* gibi phrased çok sayıda kullanıcı sorgusunun çok benzer puanlarını oluşmasına neden olabilir. Bunun yerine, KB 'unuzda birçok soruda olabilecek "konum" gibi kelimeleri önleyerek "  *Park partisi olan* " ve *"ATM* olduğu" gibi sorguları açıkça ayırt etmeye çalışın.

## <a name="collaborate"></a>İşbirliği
Soru-Cevap Oluşturma, kullanıcıların Bilgi Bankası 'nda işbirliği yapmasına olanak sağlar. Kullanıcıların bilgi tabanlara erişebilmesi için Azure Soru-Cevap Oluşturma kaynak grubuna erişmesi gerekir. Bazı kuruluşlar Bilgi Bankası düzenlemesini ve bakımını dış olarak almak isteyebilir ve yine de Azure kaynaklarına erişimi koruyabilecek. Bu düzenleyici-onaylayan modeli, farklı aboneliklerde iki özdeş [soru-cevap oluşturma hizmeti](../How-to/set-up-qnamaker-service-azure.md) ayarlanarak ve düzenleme testi döngüsüne yönelik bir seçim yaparak yapılır. Sınama tamamlandıktan sonra Bilgi Bankası içerikleri, son olarak Bilgi Bankası 'nı yayımlayacak ve uç noktayı güncelleştiren, onaylayanın Soru-Cevap Oluşturma hizmetine bir [içeri aktarma](../Tutorials/migrate-knowledge-base.md) işlemi ile aktarılır.



## <a name="active-learning"></a>Etkin öğrenme

[Etkin öğrenme](../How-to/use-active-learning.md) , çok sayıda kalite ve Kullanıcı tabanlı sorgu miktarına sahip olduğunda alternatif soruların önermesinin en iyi İşlidir. İstemci uygulamalarının Kullanıcı sorgularının, censorgemesiz etkin öğrenme geri bildirim döngüsüne katılmasını sağlamak önemlidir. Soru-Cevap Oluşturma portalında sorular Önerildikten sonra, **[önerilere göre filtreleyerek](../How-To/improve-knowledge-base.md)** bu önerileri inceleyip kabul edebilir veya reddedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi bankası düzenleme](../How-to/edit-knowledge-base.md)
