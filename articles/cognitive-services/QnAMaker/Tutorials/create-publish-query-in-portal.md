---
title: 'Öğretici: Soru-Cevap Oluşturma oluşturun, yayımlayın ve yanıtlayın'
titleSuffix: Azure Cognitive Services
description: Genel bir Web tabanlı SSS ile ilgili sorular ve yanıtlarla yeni bir Bilgi Bankası oluşturun. Bilgi Bankası 'nı kaydedin, eğitme ve yayımlayın. Bilgi Bankası yayımlandıktan sonra bir soru gönderin ve bir kıvrımlı komutla yanıt alın. Ardından bir bot oluşturun ve aynı soru ile bot 'ı test edin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 51d051fee1da1f9bb0c89ea9123748b512f84007
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327975"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Öğretici: Soru-Cevap Oluşturma portalından bir Bilgi Bankası oluşturun

Genel bir Web tabanlı SSS ile ilgili sorular ve yanıtlarla yeni bir Bilgi Bankası oluşturun. Bilgi Bankası 'nı kaydedin, eğitme ve yayımlayın. Bilgi Bankası yayımlandıktan sonra bir soru gönderin ve bir kıvrımlı komutla yanıt alın. Ardından bir bot oluşturun ve aynı soru ile bot 'ı test edin. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz: 

> [!div class="checklist"]
> * Soru-Cevap Oluşturma portalında bir Bilgi Bankası oluşturun.
> * Bilgi Bankası 'nı gözden geçirin, kaydedin ve eğitme.
> * Bilgi Bankası 'nı yayımlayın.
> * Bilgi Bankası 'nı sorgulamak için kıvrımlı kullanın.
> * Bir bot oluşturun.
 

> [!NOTE]
> Bu öğreticinin programlı sürümü, [ **Azure-Samples/bilişsel hizmetler-qnamaker-CSharp** GitHub deposundan](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)tam bir çözüm ile kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için var olan bir [Soru-Cevap Oluşturma hizmetini](../How-To/set-up-qnamaker-service-azure.md) kullanmanız gerekir. 

## <a name="create-a-knowledge-base"></a>Bilgi bankası oluşturun 

1. [Soru-Cevap Oluşturma](https://www.qnamaker.ai) portalında oturum açın. 

1. Üstteki menüden **Create a knowledge base** (Bilgi bankası oluştur) öğesini seçin.

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Mevcut Soru-Cevap Oluşturma hizmetinizi kullanacağınız için ilk adımı atlayın. 

1. Var olan ayarlarınızı seçin:  

    |Ayar|Amaç|
    |--|--|
    |Microsoft Azure Directory KIMLIĞI|Bu KIMLIK, Azure portal ve Soru-Cevap Oluşturma portalında oturum açmak için kullandığınız hesapla ilişkilidir. |
    |Azure Aboneliği adı|Soru-Cevap Oluşturma kaynağını oluşturduğunuz faturalandırma hesabı.|
    |Azure Soru-Cevap Oluşturma Hizmeti|Var olan Soru-Cevap Oluşturma kaynağınızdır.|

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Bilgi Bankası adınızı girin, `My Tutorial kb`.

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Bilgi tabanınızı aşağıdaki ayarlarla doldurun:  

    |Ayar adı|Ayar değeri|Amaç|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |URL'deki SSS içeriği, soru-cevap şeklinde biçimlendirilir. Soru-Cevap Oluşturma hizmeti bu biçimi yorumlayarak soruları ve cevaplarını ayıklayabilir.|
    |Dosya |_bu öğreticide kullanılmayacaktır_|Bu işlem sorular ve cevaplar için gerekli dosyaları karşıya yükler. |
    |Chit-chat personality (Konuşma kişiliği)|Uygun|Bu, yaygın soruların ve yanıtların kolay ve rastgele bir [kişiliğine](../Concepts/best-practices.md#chit-chat) sahip olmanızı sağlar. Bu soruları ve cevapları daha sonra düzenleyebilirsiniz. |

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Oluşturma işlemini tamamlamak için **Create your KB** (Bilgi bankanızı oluşturun) öğesini seçin.

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Bilgi bankasını gözden geçirme, kaydetme ve eğitme

1. Soruları ve cevapları gözden geçirin. İlk sayfada, URL'den alınan sorular ve cevapları bulunur. 

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Tablonun en altından son soru-cevap sayfasını seçin. Bu sayfada Chit-chat personality (Konuşma kişiliği) ayarı ile birlikte gelen sorular ve cevapları bulunur. 

1. Soru-cevap listesinin üzerindeki araç çubuğundan **Görünüm seçenekleri** simgesini seçin ve ardından **meta verileri göster**' i seçin. Bu, her soru ve yanıt için meta veri etiketlerini gösterir. Tek yönlü sohbet sorularının **düzenleme: CHIT-chat** meta verileri zaten ayarlanmış. Bu meta veriler, seçili yanıtla birlikte istemci uygulamasına döndürülür. Bir sohbet bot gibi istemci uygulaması, bu filtrelenmiş meta verileri kullanarak ek işleme veya Kullanıcı etkileşimi sağlayabilir.

    ![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Üst menü çubuğundan **Save and train** (Kaydet ve eğit) öğesini seçin.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Bilgi Bankası uç noktalarını almak için yayımlayın

Üst menü çubuğundan **Publish** (Yayımla) düğmesini seçin. Yayımlama sayfasında **Yayımla**'yı seçin.

![Soru-Cevap Oluşturma portalının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Bilgi Bankası yayımlandıktan sonra, uç nokta görüntülenir.

![Uç nokta ayarlarının ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Bu **Yayımla** sayfasını kapatmayın. Bir bot oluşturmak için öğreticide daha sonra ihtiyacınız vardır. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Bir SSS yanıtı sorgulamak için kıvrımlı kullanın

1. **Curl** sekmesini seçin. 

    ![Kıvrımlı sekmesinin ekran görüntüsü](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. **Kıvrımlı** sekmesinin metnini kopyalayın ve kıvrımlı özellikli bir terminalde veya komut satırında çalıştırın. Yetkilendirme üst bilgisi değeri, sonunda boşluk ve sonra anahtar `Endpoint` metnini içerir.

1. `<Your question>` yerine `How large can my KB be?` yazın. Bu ifade `How large a knowledge base can I create?` sorusuna yakındır ancak tam olarak aynısı değildir. Soru-Cevap Oluşturma, doğal dil işleme süreçlerini kullanarak iki sorunun aynı olduğunu belirler.     

1. Kıvrımlı komutunu çalıştırın ve puan ve yanıt dahil olmak üzere JSON yanıtını alın. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    Soru-Cevap Oluşturma, %42,81 puanla cevaptan emin sayılır.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Bir GIT-chat yanıtı sorgulamak için kıvrımlı kullanın

1. Kıvrımlı özellikli terminalde, `How large can my KB be?` ' ı kullanıcının `Thank you` gibi bir bot konuşma bitiş bildirimiyle değiştirin.   

1. Kıvrımlı komutunu çalıştırın ve puan ve yanıt dahil olmak üzere JSON yanıtını alın. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    `Thank you` sorusu bir genel konuşma sorusuyla tam olarak eşleştiği için Soru-Cevap Oluşturma tam olarak emindir ve 100 puan döndürür. Soru-Cevap Oluşturma Ayrıca, tüm ilgili soruların yanı sıra CHIT-chat meta veri etiketi bilgilerini içeren meta veri özelliği de döndürülür.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Varsayılan yanıtı sorgulamak için kıvrımlı kullanın

Soru-Cevap Oluşturma herhangi bir soru, varsayılan yanıtı alır. Bu cevap Azure portalda yapılandırılır. 

1. Kıvrımlı özellikli terminalde `Thank you` ' ı `x` ile değiştirin. 

1. Kıvrımlı komutunu çalıştırın ve puan ve yanıt dahil olmak üzere JSON yanıtını alın. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    Soru-Cevap Oluşturma `0` puanı döndürdü, bu, güven anlamına gelir. Ayrıca varsayılan yanıtı da döndürür. 

## <a name="create-a-knowledge-base-bot"></a>Bilgi Bankası bot oluştur

Daha fazla bilgi için [Bu Bilgi Bankası ile sohbet bot oluşturma](create-qna-bot.md)konusuna bakın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilgi Bankası bot ile işiniz bittiğinde, bot işleminde oluşturulan tüm Azure kaynaklarını kaldırmak için `my-tutorial-rg` kaynak grubunu kaldırın.

Bilgi Bankası ile işiniz bittiğinde, Soru-Cevap Oluşturma portalında, **bilgi tabanlarım**' ı seçin. Ardından Bilgi Bankası 'nı, **Öğreticimin BB**'yi seçin ve bu satırın en sağdaki Sil simgesini seçin.  

## <a name="next-steps"></a>Sonraki adımlar

Desteklenen dosya biçimleri hakkında daha fazla bilgi için bkz. [Desteklenen veri kaynakları](../Concepts/data-sources-supported.md). 

Genel konuşma [kişilikleri](../Concepts/best-practices.md#chit-chat) hakkında daha fazla bilgi edinin.

Varsayılan cevap hakkında daha fazla bilgi için bkz. [Eşleşme bulunamadı](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Bu Bilgi Bankası ile bir sohbet bot oluşturun](create-qna-bot.md)