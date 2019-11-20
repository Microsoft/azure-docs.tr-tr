---
title: 'Hızlı başlangıç: LUO portalı ile uygulama dağıtma'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, bir tahmin uç noktası kaynağı oluşturarak, kaynak, eğitim ve uygulamayı yayımlamaya göre bir uygulamanın nasıl dağıtılacağı gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 279ec4e8a6d9a9d473cc511b4ec690391cdbd634
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669416"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Hızlı başlangıç: Lua portalında uygulama dağıtma

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Lusıs uygulamanız, bir istemci uygulamasına söylenişi tahminleri (örneğin, bir sohbet bot) döndürmeye hazırsanız uygulamayı tahmin uç noktasına dağıtmanız gerekir.

Bu hızlı başlangıçta, bir uygulamayı dağıtmayı öğreneceksiniz. Bir tahmin uç noktası kaynağı oluşturur, uygulamayı uygulamaya atayabilir, uygulamayı eğitme ve uygulamayı yayımlayabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

* Bir [Azure aboneliği](https://azure.microsoft.com/free)alın.
* [Önceki Portal Hızlı](get-started-portal-build-app.md) başlangıcını tamamlayıp [uygulamayı indirip içeri aktarın](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Uç nokta kaynağı oluşturma

Tahmin uç noktası kaynağını Azure portal oluşturursunuz. Bu kaynak yalnızca Endpoint tahmin sorguları için kullanılmalıdır. Uygulamada değişiklik yazmak için bu kaynağı kullanmayın.

1. Oturum açın ve [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)bir kaynak oluşturun.

1. Aboneliği aşağıdaki ayarlarla yapılandırın:

   |Ayar|Değer|Amaç|
   |--|--|--|
   |Ad|`my-luis-resource`|Azure kaynağının adı. Bu adı, kaynağı LUE portalında uygulamaya atarken kullanmanız gerekir.|
   |Abonelik|Aboneliğiniz|Hesabınızla ilişkili aboneliklerden birini seçin.|
   |Kaynak grubu|`my-resource-group`|Tüm bilişsel hizmet kaynaklarınız için yeni bir kaynak grubu oluşturun. Kaynaklarla işiniz bittiğinde, aboneliğinizi temizlemek için kaynak grubunu silebilirsiniz. |
   |Yazma konumu|**Batı ABD**|Yazma için Azure bölgesi.|
   |Fiyatlandırma Katmanı yazma|**F0**|Yazma için varsayılan fiyatlandırma katmanı.|
   |Çalışma zamanı konumu|**Batı ABD**|Tahmin uç noktası sorguları için Azure bölgesi.|
   |Çalışma zamanı Fiyatlandırma Katmanı|**S0**|Bu fiyatlandırma katmanı, yüksek trafikli web siteleri sağlar.|
   | | | |


   ![Azure API seçimi](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Azure kaynağını oluşturmak için **Oluştur** ' u seçin.

   Sonraki bölümde, bu yeni kaynağı LUU portalındaki bir Luo uygulamasına bağlamayı öğreneceksiniz.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUE portalında LUO uygulamasına kaynak anahtarı atama

LUHER yeni kaynak oluşturduğunuzda, kaynağı LUO uygulamasına atamanız gerekir. Atandıktan sonra, yeni bir kaynak oluşturmadığınız takdirde bu adımı tekrar yapmanız gerekmez. Uygulamanızın bölgelerini genişletmek veya daha yüksek sayıda tahmin sorgusu desteklemek için yeni bir kaynak oluşturabilirsiniz.

1. [Luo portalında](https://www.luis.ai) oturum açın ve uygulamalar listesinden **Myenglishapp** uygulamasını seçin.

1. Sağ üst menüdeki **Yönet** ' i seçin ve ardından **Azure kaynakları**' nı seçin.

1. LUO eklemek için **tahmin kaynağı Ekle**' yi seçin.

    ![LUSıS tahmin kaynağını eklemek için tahmin kaynağı Ekle ' yi seçin.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Kiracınızı, aboneliğinizi ve kaynak adınızı seçin. **Kaynak ata**' yı seçin.

   ![Uygulamanıza bir kaynak atayın](./media/get-started-portal-deploy-app/assign-resource.png)

1. Uygulamanıza yazma anahtarını eklemek için aynı adımları izleyin.

1. Yeni tahmin kaynağı için tablodaki yeni satırı bulun ve uç nokta URL 'sini kopyalayın. Bir tahmine yönelik olarak LUSıS API uç noktası için `HTTP GET` isteği oluşturmak üzere doğru bir şekilde oluşturulur.

## <a name="train-the-app"></a>Uygulamayı eğitme

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Uygulamayı tahmin uç noktasına yayımlayın

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Tahmin uç noktası isteği

URL 'nin sonundaki `query=`, kullanıcının utterine GET isteğine eklendiği yerdir. `query=`sonra, önceki hızlı başlangıç için kullanılan Kullanıcı utterliğini girin:

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

[![Şu anda düzenleyen uygulamanın yayımlanmış sürümü karşılaştırması](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç ile işiniz bittiğinde, üst gezinti menüsünden **uygulamalarım** ' ı seçin. Listeden uygulamanın onay kutusunu seçin ve sonra listenin üzerindeki bağlam araç çubuğundan **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ortak amaçları ve varlıkları tanımla](luis-tutorial-prebuilt-intents-entities.md)
