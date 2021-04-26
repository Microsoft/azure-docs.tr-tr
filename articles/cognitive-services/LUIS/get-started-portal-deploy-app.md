---
title: 'Hızlı başlangıç: LUO portalı ile uygulama dağıtma'
description: Bu hızlı başlangıçta, bir tahmin uç noktası kaynağı oluşturarak, kaynak, eğitim ve uygulamayı yayımlamaya göre bir uygulamanın nasıl dağıtılacağı gösterilmektedir.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: f7d5d473b8fe24d8dd5018a718ea96f286b1c030
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275847"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Hızlı başlangıç: Lua portalında uygulama dağıtma

Lusıs uygulamanız, bir istemci uygulamasına söylenişi tahminleri (örneğin, bir sohbet bot) döndürmeye hazırsanız uygulamayı tahmin uç noktasına dağıtmanız gerekir.

Bu hızlı başlangıçta, bir uygulamayı dağıtmayı öğreneceksiniz. Bir tahmin uç noktası kaynağı oluşturur, uygulamayı uygulamaya atayabilir, uygulamayı eğitme ve uygulamayı yayımlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Bir [Azure aboneliği](https://azure.microsoft.com/free)alın.
* [Önceki Portal Hızlı](get-started-portal-build-app.md) başlangıcını tamamlayıp [uygulamayı indirip içeri aktarın](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Azure Kaynak kimlik doğrulamasının önceden güncel olduğu uygulamalarınız varsa, [bir Azure kaynağına geçiş](luis-migration-authoring.md)yapın. E-posta kimlik doğrulaması etkin olduğunda bazı portal sayfaları farklı görünür.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUE portalında LUO uygulamasına kaynak anahtarı atama

LUHER yeni yazma veya sorgu tahmini kaynağı oluşturduğunuzda, kaynağı LUO uygulamasına atamanız gerekir. Atandıktan sonra, yeni bir kaynak oluşturmadığınız takdirde bu adımı tekrar yapmanız gerekmez. Uygulamanızın bölgelerini genişletmek veya daha yüksek sayıda tahmin sorgusu desteklemek için yeni bir kaynak oluşturabilirsiniz.

1. [Luo portalında](https://www.luis.ai) oturum açın ve uygulamalar listesinden **Myenglishapp** uygulamasını seçin.

1. Sağ üst menüdeki **Yönet** ' i seçin ve ardından **Azure kaynakları**' nı seçin.

1. LUO eklemek için **tahmin kaynağı Ekle**' yi seçin.

    ![LUSıS tahmin kaynağını eklemek için tahmin kaynağı Ekle ' yi seçin.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Kiracınızı, aboneliğinizi ve kaynak adınızı seçin. **Kaynak ata**' yı seçin.

   > [!div class="mx-imgBorder"]
   > ![Uygulamanıza bir kaynak atayın](./media/get-started-portal-deploy-app/assign-resource.png)

1. Uygulamanıza yazma anahtarını eklemek için aynı adımları izleyin.

1. Yeni tahmin kaynağı için tablodaki yeni satırı bulun ve uç nokta URL 'sini kopyalayın. `HTTP GET`Bir tahmine yönelik olarak LUSıS API uç noktasına bir istek oluşturmak için doğru bir şekilde oluşturulur.

> [!TIP]
> LUSıS uygulamanızı geliştirmek için etkin öğrenme kullanmak istiyorsanız **sorgu parametrelerini değiştir** ' i seçin ve **günlükleri kaydet**' i seçin. Bu eylem QueryString parametresini ekleyerek örnek URL 'YI değiştirir `log=true` . Çalışma zamanı uç noktasına tahmin sorguları yaparken değiştirilen örnek sorgu URL 'sini kopyalayın ve kullanın.

## <a name="train-the-app"></a>Uygulamayı eğitme

Uygulamayı en son eğitilen bu yana değiştirdiyseniz uygulamayı [eğitebilirsiniz](get-started-portal-build-app.md) .

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Uygulamayı tahmin uç noktasına yayımlayın

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Tahmin uç noktası isteği

Portalda, `query=` URL 'nin sonunda kullanıcının utterine, GET isteğine eklendiği yerdir. Sonrasında, `query=` önceki hızlı başlangıç noktasında kullanılan aynı kullanıcı adını girin:

```Is there a form named hrf-234098```

Sorgu dizesinin aşağıdaki çiftleri içerdiğinden emin olun:

* `show-all-intents=true`
* `verbose=true`

Tarayıcı, yanıtı gösterir:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Test bölmesinde aynı bilgi düzeyini görmek için uygulamayı yayımlamanız gerekir. Uygulama yayımlandıktan sonra test bölmesinde **Yayımlanla Karşılaştır** ' ı seçin. Önceki adımla aynı JSON 'ı görmek için yayımlanan test bölmesinde **JSON görünümünü göster** ' i kullanın. Bu şekilde, üzerinde çalışmakta olduğunuz geçerli uygulamayla ilgili değişiklikleri, uç noktada yayınlanan bir uygulamayla karşılaştırabilirsiniz.

[![Şu anda düzenleyen uygulamanın yayımlanmış sürümüne göre karşılaştır](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç ile işiniz bittiğinde, üst gezinti menüsünden **uygulamalarım** ' ı seçin. Listeden uygulamanın onay kutusunu seçin ve sonra listenin üzerindeki bağlam araç çubuğundan **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [LUSıS için yinelemeli uygulama geliştirme](./luis-concept-app-iteration.md)