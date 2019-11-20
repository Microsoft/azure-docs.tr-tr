---
title: 'Hızlı başlangıç: Soru-Cevap Oluşturma portalına soru ve yanıt ekleme'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 92735d8982fb1364d5ebfe0494f5ee51f4302469
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794001"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Hızlı başlangıç: Soru-Cevap Oluşturma portalı ile soru ve yanıt ekleme

Bir Bilgi Bankası oluşturulduktan sonra, kullanıcılarınızın sorularını doğru yanıtı bulabilmesi için meta verilerle soru ve yanıt kümeleri ekleyin.

Doğru yanıt tek bir yanıttır ancak bir müşterinin söz konusu tek yanıta neden olan soruyu sormasına birçok yol olabilir.

Örneğin, aşağıdaki tablodaki sorular Azure hizmet limitleriyle ilgilidir, ancak her birinin farklı bir Azure hizmeti ile yapması gerekir. 

<a name="qna-table"></a>


|Ayarla|Sorular|Yanıt|Meta Veriler|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Bir soru-cevap kümesine meta veriler eklendikten sonra istemci uygulama şunları yapabilir:

* Yalnızca belirli meta verilerle eşleşen yanıt isteyin.
* Tüm yanıtları al, ancak her yanıt için meta verilere bağlı olarak yanıtları son işle.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Ön koşullar

* Bir Soru-Cevap Oluşturma Hizmeti
* Bu Soru-Cevap Oluşturma hizmetinde oluşturulmuş bir bilgi tabanı

Her ikisi de [ilk hızlı](../how-to/create-knowledge-base.md)başlangıçta oluşturulmuştur.

## <a name="sign-in-to-the-qna-maker-portal"></a>Soru-Cevap Oluşturma portalında oturum açın

1. [Soru-cevap oluşturma portalında](https://www.qnamaker.ai)oturum açın.

1. Mevcut bilgi bankasını seçin. Bilgi tabanınız yoksa, [önceki hızlı](../how-to/create-knowledge-base.md) Başlangıca dönün ve bilgi tabanınızı oluşturma adımlarını izleyin.

## <a name="add-additional-alternatively-phrased-questions"></a>Ek alternatif olarak phrased soruları ekleyin 

[Önceki hızlı](../how-to/create-knowledge-base.md)başlangıçta bulunan soru-cevap oluşturma sorun giderme sorusu ve yanıt kümelerine sahip geçerli bilgi tabanı. Bu kümeler, oluşturma işlemi sırasında URL Bilgi Bankası 'na eklendiğinde oluşturulmuştur. 

Bu URL içeri aktarıldığında, yalnızca bir yanıtla birlikte tek bir soru oluşturulur. 

Bu yordamda, ek sorular ekleyin.

1. **Düzenleme** sayfasında, soruyu bulmak için soru ve yanıt kümelerinin üzerindeki arama metin kutusunu kullanın `How large a knowledge base can I create?`

1. **Soru** sütununda **+ Alternatif ifade Ekle** ' yi seçin ve aşağıdaki tabloda belirtilen her yeni bir ifade ekleyin.

    |Alternatif ifade|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Bilgi Bankası 'nı yeniden eğitmek için **Kaydet ve eğitme '** yi seçin. 

1. **Test**' i seçin, ardından yeni alternatif Phen ' dan birine yakın bir soru girin, ancak tamamen aynı şekilde değildir:

    `What GB size can a knowledge base be?`

    Doğru yanıt markaşağı biçiminde döndürülür: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Döndürülen yanıtın altında **İncele** ' yi seçerseniz, soruyu karşılayan daha fazla yanıt görebilirsiniz, ancak aynı yüksek düzeyde güvenle bilgi alabilirsiniz. 

    Alternatif özelliklerin her olası birleşimini eklemeyin. Soru-Cevap Oluşturma [etkin öğrenimini](../how-to/improve-knowledge-base.md)açın, bu, bilgi tabanınızın kullanıcılarınızın ihtiyaçlarını karşılayacak en iyi şekilde yardımcı olacak alternatif duymalları bulur.

1. Test penceresini kapatmak için yeniden **Test** ' i seçin.

## <a name="add-metadata-to-filter-the-answers"></a>Yanıtları filtrelemek için meta veriler ekleyin

Bir soru ve yanıt kümesine meta veri eklemek, istemci uygulamanızın filtrelenmiş yanıtlar istemesine izin verir. Bu filtre, [ilk ve ikinci ranranlar](../concepts/knowledge-base.md#ranker-process) uygulanmadan önce uygulanır.

1. İkinci soruyu ve yanıt kümesini meta veriler olmadan, [Bu hızlı başlangıçtaki ilk tablodan](#qna-table)ekleyin ve ardından aşağıdaki adımlarla devam edin. 

1. **Görünüm seçeneklerini**belirleyin ve **meta verileri göster**' i seçin. 

1. Yeni eklediğiniz soru ve yanıt kümesi için, **meta veri etiketleri Ekle**' yi seçin, ardından `service` adı ve `search`değeri `service:search`ekleyin.

1. `link_in_answer` adı ve `false`değeri `link_in_answer:false`olan başka bir meta veri etiketi ekleyin.

1. Tablodaki ilk yanıtı arayın, `How large a knowledge base can I create?`. 
1. Aynı iki meta veri etiketi için meta veri çiftleri ekleyin:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Artık, farklı değerlerle aynı meta veri etiketlerine sahip iki sorunuz vardır. 

1. Bilgi Bankası 'nı yeniden eğitmek için **Kaydet ve eğitme '** yi seçin. 

1. Yayımla sayfasına gitmek için üstteki menüden **Yayımla** ' yı seçin. 
1. Geçerli Bilgi Bankası 'nı sorgulanabilir bir uç noktaya yayımlamak için **Yayımla** düğmesini seçin. 
1. Bilgi Bankası yayımlandıktan sonra, Bilgi Bankası 'ndan bir yanıt oluşturmak için kullanılan örnek bir kıvrımlı komutu görmek için **kıvrımlı** sekmesini seçin.
1. Komutu düzenlemek için bir not defteri veya başka bir düzenlenebilir ortama komutu kopyalayın. Kendi kaynak adınız, Bilgi Bankası KIMLIĞI ve uç nokta anahtarınız için düzenleme yapın:

    |Değiştir|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Sorunun yalnızca tek bir kelime, `size`olduğunu ve soru ve yanıt kümesi döndürebileceğini fark edebilirsiniz. `strictFilters` dizisi, yanıta yalnızca `qna_maker` yanıtlara karşı azaltmasını söyler. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Yanıt yalnızca filtre ölçütlerini karşılayan yanıtı içerir. 

    Aşağıdaki kıvrımlı yanıtı okunabilirlik için biçimlendirildi:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Arama terimini karşılamayan ancak filtreye uyan bir soru ve yanıt kümesi varsa, bu geri döndürülemez. Bunun yerine, genel yanıt `No good match found in KB.` döndürülür.

    Meta veri adınızı ve değer çiftlerini gerekli limitlerin içinde tutduğunuzdan emin olun. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Postman ile yanıt alın](get-answer-from-kb-using-postman.md)