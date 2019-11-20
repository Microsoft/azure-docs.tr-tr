---
title: 'Öğretici: Soru-cevap Robotu - Azure robot hizmeti - soru-cevap Oluşturucu'
titleSuffix: Azure Cognitive Services
description: Mevcut bir Bilgi Bankası için Yayımla sayfasından bir QnA chat bot oluşturun. Bu bot, bot Framework SDK v4 kullanır. Bot oluşturmak için herhangi bir kod yazmanız gerekmez, tüm kod sizin için sağlanır.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ea6e0d266c181d930f3d18171b09d222e53da7ab
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390905"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Öğretici: Azure bot hizmeti v4 ile QnA bot oluşturma

Mevcut bir Bilgi Bankası için **Yayımla** sayfasından bir QNA chat bot oluşturun. Bu bot, bot Framework SDK v4 kullanır. Bot oluşturmak için herhangi bir kod yazmanız gerekmez, tüm kod sizin için sağlanır.

**Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Mevcut bir Bilgi Bankası 'ndan bir Azure bot hizmeti oluşturun
> * Kod çalışıp çalışmadığını denetlemek için bot ile sohbet edin 

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için yayımlanan Bilgi Bankası olması gerekir. Bir tane yoksa, soru ve yanıtlarla ilgili Soru-Cevap Oluşturma Bilgi Bankası oluşturmak için [KB Ile oluşturma ve yanıtlama](create-publish-query-in-portal.md) bölümündeki adımları izleyin.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Soru-cevap Robotu oluşturun

Bilgi Bankası için istemci uygulaması olarak bir bot oluşturun. 

1. Soru-Cevap Oluşturma portalında **Yayımla** sayfasına gidin ve bilgi tabanınızı yayımlayın. **Bot oluştur**' u seçin. 

    ![Soru-Cevap Oluşturma portalında Yayımla sayfasına gidin ve bilgi tabanınızı yayımlayın. Bot oluştur ' u seçin.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure portal, bot oluşturma yapılandırması ile açılır.

1.  Bot 'ı oluşturmak için ayarları girin:

    |Ayar|Value|Amaç|
    |--|--|--|
    |Robot adı|`my-tutorial-kb-bot`|Bu, bot için Azure Kaynak adıdır.|
    |Subscription|Amaca bakın.|Soru-Cevap Oluşturma kaynaklarını oluştururken kullandığınız aboneliği seçin.|
    |Resource group|`my-tutorial-rg`|Tüm bot ile ilgili Azure kaynakları için kullanılan kaynak grubu.|
    |Location|`west us`|Bot 'un Azure Kaynak konumu.|
    |Fiyatlandırma katmanı|`F0`|Azure bot hizmeti için ücretsiz katman.|
    |Uygulama adı|`my-tutorial-kb-bot-app`|Bu, yalnızca bot uygulamanızı destekleyecek bir Web uygulamasıdır. Soru-Cevap Oluşturma hizmetiniz zaten kullandığı için bu uygulama adı aynı olmamalıdır. Soru-Cevap Oluşturma Web uygulamasını başka bir kaynakla paylaşma desteklenmez.|
    |SDK dili|C#|Bu, bot Framework SDK 'Sı tarafından kullanılan temel programlama dilidir. Seçenekleriniz veya [C#](https://github.com/Microsoft/botbuilder-dotnet) [Node. js](https://github.com/Microsoft/botbuilder-js)' dir.|
    |QnA auth anahtarı|**Değiştirme**|Bu değer sizin için doldurulur.|
    |Uygulama hizmeti planı/konumu|**Değiştirme**|Bu öğreticide, konum önemli değildir.|
    |Azure Storage|**Değiştirme**|Konuşma verileri Azure depolama tablolarında depolanır.|
    |Application Insights|**Değiştirme**|Günlüğe kaydetme Application Insights gönderilir.|
    |Microsoft uygulama KIMLIĞI|**Değiştirme**|Active Directory kullanıcısı ve parolası gereklidir.|

    ![Bilgi Bankası bot 'ı bu ayarlarla oluşturun.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Bot oluşturma işlemi bildirimi başarılı olana kadar birkaç dakika bekleyin.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Bot ile sohbet edin

1. Azure portal, bildirimdeki yeni bot kaynağını açın. 

    ![Azure portal, bildirimdeki yeni bot kaynağını açın.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. **Bot Management**'Tan **Web sohbeti ' nda test** ' i seçin ve `How large can my KB be?`şunu girin:. Bot şu şekilde yanıt verir: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Yeni Bilgi Bankası bot 'ı test edin.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Azure botları hakkında daha fazla bilgi için bkz. [soru-cevap oluşturma kullanarak soruları cevaplayın](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin bot ile işiniz bittiğinde, Azure portalında bot kaldırın. 

Bot 'un kaynakları için yeni bir kaynak grubu oluşturduysanız, kaynak grubunu silin. 

Yeni bir kaynak grubu oluşturmadıysanız, bot ile ilişkili kaynakları bulmanız gerekir. En kolay yol, bot ve bot uygulamasının adına göre arama kullanmaktır. Bot kaynakları şunları içerir:

* App Service planı
* Arama hizmeti
* Bilişsel hizmet
* App service
* İsteğe bağlı olarak, bu da application ınsights hizmeti ve depolama için application ınsights verilerini içerebilir


## <a name="related-to-qna-maker-bots"></a>Soru-Cevap Oluşturma botları ile ilgili

* Soru-Cevap Oluşturma portalında kullanılan Soru-Cevap Oluşturma yardım bot, bir [bot örneği](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)olarak sunulmaktadır.
    ![Soru-Cevap Oluşturma yardım bot simgesi kırmızı robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Sağlık botları](https://docs.microsoft.com/HealthBot/qna_model_howto) [dil modellerinden](https://docs.microsoft.com/HealthBot/qna_model_howto)biri olarak soru-cevap oluşturma kullanır.


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kavramı: Bilgi Bankası](../concepts/knowledge-base.md)

## <a name="see-also"></a>Ayrıca bkz.

- [Bilgi bankanızı yönetme](https://qnamaker.ai)
- [Farklı kanallar botunuzun insana](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
