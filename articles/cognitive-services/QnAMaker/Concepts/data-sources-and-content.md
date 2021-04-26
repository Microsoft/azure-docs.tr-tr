---
title: Veri kaynakları ve içerik türleri-Soru-Cevap Oluşturma
description: PDF, DOCX ve TXT-Soru-Cevap Oluşturma gibi birçok standart yapılandırılmış belgeyi içeren veri kaynaklarından ve desteklenen içerik türlerinden soru ve yanıt çiftlerini içeri aktarmayı öğrenin.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99556048"
---
# <a name="importing-from-data-sources"></a>Veri kaynaklarından içeri aktarma

Bilgi Bankası, ortak URL 'Ler ve dosyalar tarafından getirilen sorulardan ve yanıt çiftlerinden oluşur.

## <a name="data-source-locations"></a>Veri kaynağı konumları

İçerik bir veri kaynağından bilgi tabanına getirilir. Veri kaynağı konumları, kimlik doğrulaması gerektirmeyen **Genel URL 'ler veya dosyalardır**.

Kimlik doğrulamasıyla güvenli hale getirilen [SharePoint dosyaları](../how-to/add-sharepoint-datasources.md)özel durumdur. SharePoint kaynakları, Web sayfaları değil, dosyalar olmalıdır. URL'nin .ASPX gibi bir web uzantısıyla bitmesi durumunda SharePoint'ten Soru-Cevap Oluşturma hizmetine aktarma gerçekleştirilmez.

## <a name="chit-chat-content"></a>Chit-sohbet içeriği

CHIT-chat içerik kümesi, çeşitli dillerde ve konuşma stillerinde tam içerik veri kaynağı olarak sunulur. Bu, bot 'ın kişiliğine yönelik bir başlangıç noktası olabilir ve bu, bunları sıfırdan yazmanın zaman ve maliyetini kaydeder. Bilgi tabanınız için [CHIT-chat içeriği eklemeyi](../how-to/chit-chat-knowledge-base.md) öğrenin.

## <a name="structured-data-format-through-import"></a>İçeri aktarma yoluyla yapılandırılmış veri biçimi

Bilgi bankasını içeri aktarmak, mevcut bilgi bankasındaki içeriğin yerini alır. İçeri aktarma `.tsv` , sorular ve yanıtlar içeren yapılandırılmış bir dosya gerektirir. Bu bilgiler, soru-cevap çiftlerini ve onları belirli bir veri kaynağına göre gruplamak Soru-Cevap Oluşturma yardımcı olur.

| Soru  | Yanıt  | Kaynak| Meta veriler (1 anahtar: 1 değer) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Düzenleme|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>İçeri aktarma aracılığıyla yapılandırılmış çok yönlü biçim

Çoklu açma konuşmalarını bir `.tsv` dosya biçiminde oluşturabilirsiniz. Biçim, önceki sohbet günlüklerini çözümleyerek (Soru-Cevap Oluşturma kullanmayan diğer işlemlerle) birden çok yönlü konuşmaları oluşturma olanağı sağlar ve ardından `.tsv` dosyayı Otomasyon aracılığıyla oluşturabilir. Mevcut bilgi bankasını değiştirmek için dosyayı içeri aktarın.

> [!div class="mx-imgBorder"]
> ![3 düzey çoklu çift soruda kavramsal model](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Birden çok açma için özel, birden çok yönlü bir sütun, `.tsv` **ister**. `.tsv`Excel 'de gösterilen, çoklu çift alt öğeleri tanımlamak için dahil edilecek bilgileri gösteren bir örnek:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**Displayorder** sayısal ve **metinmetni** markaşağı içermemelidir.

> [!div class="mx-imgBorder"]
> ![Excel 'de gösterildiği gibi çok yönlü soru örneği](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Örnek olarak dışarı aktar

Dosyada QnA çiftini nasıl temsil ettiğini bilmiyorsanız `.tsv` :
* Bu [indirilebilir örneği GitHub 'dan](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true) kullan
* Veya Soru-Cevap Oluşturma portalında çift oluşturun, bu çiftin nasıl kullanılacağına ilişkin bir örnek için Bilgi Bankası 'nı kaydedin ve dışarı aktarın.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Bilgi tabanına ekleyebileceğiniz belgelerin içerik türleri
İçerik türleri arasında PDF, DOC ve TXT gibi birçok standart yapılandırılmış belge bulunur.

### <a name="file-and-url-data-types"></a>Dosya ve URL veri türleri

Aşağıdaki tabloda, Soru-Cevap Oluşturma tarafından desteklenen içerik ve dosya biçimlerinin türleri özetlenmektedir.

|Kaynak Türü|İçerik Türü| Örnekler|
|--|--|--|
|URL|SSS<br> (Düz, bölümler ile veya konu başlığı ile)<br>Destek sayfaları <br> (Tek sayfalı nasıl yapılır makaleleri, sorun giderme makaleleri vb.)|[Düz SSS](../troubleshooting.md), <br>[Bağlantılarla Ilgili SSS](https://www.microsoft.com/en-us/software-download/faq),<br> [Konularda SSS giriş sayfası](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Destek makalesi](./best-practices.md)|
|PDF/BELGE|Sık<br> Ürün el kitabı,<br> Broşürler<br> T<br> El ilanı Ilkesi,<br> Destek Kılavuzu,<br> Yapılandırılmış QnA,<br> vb.|**Çoklu açma olmadan**<br>[Yapılandırılmış QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Örnek ürün Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Örnek semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Örnek beyaz paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Çoklu açma**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso avantajları (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso avantajları (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Yapılandırılmış QnA dosyası<br> (RTF, HTML desteği dahil)|**Çoklu açma olmadan**:<br>[Örnek QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Çoklu açma**:<br>[Yapılandırılmış basit FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface dizüstü bilgisayar FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Yapılandırılmış QnA dosyası|[Örnek Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Veri kaynağınız için kimlik doğrulaması gerekiyorsa, bu içeriği Soru-Cevap Oluşturma almak için aşağıdaki yöntemleri göz önünde bulundurun:

* Dosyayı el ile indirip Soru-Cevap Oluşturma alın
* Kimliği doğrulanmış [SharePoint konumundan](../how-to/add-sharepoint-datasources.md) dosyayı Ekle

### <a name="url-content"></a>URL içeriği

Soru-Cevap Oluşturma **URL** aracılığıyla iki tür belge içeri aktarılabilir:

* SSS URL 'Leri
* Destek URL 'Leri

Her tür beklenen bir biçimi gösterir.

### <a name="file-based-content"></a>Dosya tabanlı içerik

[Soru-cevap oluşturma portalında](https://www.qnamaker.ai)ortak bir kaynaktan veya yerel dosya sisteminizden bir bilgi tabanına dosya ekleyebilirsiniz.

### <a name="content-format-guidelines"></a>İçerik biçimi yönergeleri

Farklı dosyalar için [Biçim yönergeleri](../reference-document-format-guidelines.md) hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

[QnAs 'yi nasıl düzenleyeceğinizi](../how-to/edit-knowledge-base.md)öğrenin.
