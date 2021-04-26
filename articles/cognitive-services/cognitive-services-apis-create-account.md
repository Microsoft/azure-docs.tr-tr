---
title: Azure portal bilişsel hizmetler kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Azure portal bir kaynağı oluşturup abone olarak Azure bilişsel hizmetler 'i kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
keywords: bilişsel hizmetler, bilişsel zeka, bilişsel çözümler, AI Hizmetleri
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: af957758918b99dcb44732eb536c0ca031231a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868231"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bilişsel hizmetler kaynağı oluşturma

Azure bilişsel hizmetler 'i kullanmaya başlamak için bu hızlı başlangıcı kullanın. Azure portal bilişsel hizmet kaynağı oluşturduktan sonra, uygulamalarınızın kimliğini doğrulamak için bir uç nokta ve anahtar alırsınız.

Azure bilişsel hizmetler, REST API 'lerle bulut tabanlı hizmetlerdir ve geliştiricilerin doğrudan yapay zeka (AI) veya veri bilimi becerileri veya bilgi sahibi olmadan uygulamalara bilişsel zeka bilgileri oluşturmalarına yardımcı olacak istemci kitaplığı SDK 'Ları. Azure bilişsel hizmetler, geliştiricilerin kendi uygulamalarına kolayca bilişsel özellikler eklemesine, duymasına, konuşmasına, anlamasına ve hatta başlayabilmesine olanak tanıyan bilişsel çözümler sunar.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Önkoşullar

* Geçerli bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Yeni bir Azure bilişsel hizmetler kaynağı oluşturma

1. Kaynak oluşturma.

### <a name="multi-service-resource"></a>[Çoklu hizmet kaynağı](#tab/multiservice)

Çoklu hizmet kaynağı, portalda bilişsel **Hizmetler** olarak adlandırılır. Bilişsel [Hizmetler kaynağı oluşturun](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

Şu anda, çok hizmet kaynağı aşağıdaki bilişsel hizmetlere erişim sağlar:

* **Vision** -Görüntü İşleme, özel görüntü işleme, form tanıyıcı, yüz
* **Konuşma** tanıma
* **Dil** -Language UNDERSTANDING (Luu), metin analizi, çevirmen
* **Karar** -kişiselleştirici, Content moderator

### <a name="single-service-resource"></a>[Tek hizmet kaynağı](#tab/singleservice)

Kullanılabilir bilişsel hizmetler için bir kaynak oluşturmak üzere aşağıdaki bağlantıları kullanın:

| Görsel                      | Konuşma                  | Dil                          | Karar             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Bilgisayar vizyonu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Konuşma Hizmetleri](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Modern okuyucu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomali Algılayıcısı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Özel Vision hizmeti](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Yüz Tanıma](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [Soru-Cevap Oluşturma](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Kişiselleştirme](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Form Tanıma](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)        |                         | [Metin Analizi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Ölçüm Danışmanı](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. **Oluştur** sayfasında, aşağıdaki bilgileri sağlayın:
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Çoklu hizmet kaynağı](#tab/multiservice)

|Proje ayrıntıları| Açıklama   |
|--|--|
| **Abonelik** | Kullanılabilir Azure aboneliklerinizden birini seçin. |
| **Kaynak grubu** | Bilişsel hizmetler kaynağını içerecek Azure Kaynak grubu. Yeni bir grup oluşturabilir veya önceden var olan bir gruba ekleyebilirsiniz. |
| **Bölge** | Bilişsel hizmet örneğinizin konumu. Farklı konumlar gecikme gösterebilir, ancak kaynağınızın çalışma zamanı kullanılabilirliğini etkilemez. |
| **Ad** | Bilişsel hizmetler kaynağınız için açıklayıcı bir ad. Örneğin, *Mybiliveservicesresource*. |
| **Fiyatlandırma katmanı** | Bilişsel hizmetler hesabınızın maliyeti, seçtiğiniz seçeneklere ve kullanımınıza bağlıdır. Daha fazla bilgi için bkz. API [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cognitive-services/).

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="Çoklu hizmet kaynağı oluşturma ekranı":::

Koşulları (sizin için geçerli olacak şekilde) okuyup kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

### <a name="single-service-resource"></a>[Tek hizmet kaynağı](#tab/singleservice)

|Proje ayrıntıları| Açıklama   |
|--|--|
| **Abonelik** | Kullanılabilir Azure aboneliklerinizden birini seçin. |
| **Kaynak grubu** | Bilişsel hizmetler kaynağını içerecek Azure Kaynak grubu. Yeni bir grup oluşturabilir veya önceden var olan bir gruba ekleyebilirsiniz. |
| **Bölge** | Bilişsel hizmet örneğinizin konumu. Farklı konumlar gecikme gösterebilir, ancak kaynağınızın çalışma zamanı kullanılabilirliğini etkilemez. |
| **Ad** | Bilişsel hizmetler kaynağınız için açıklayıcı bir ad. Örneğin, *Mybiliveservicesresource*. |
| **Fiyatlandırma katmanı** | Bilişsel hizmetler hesabınızın maliyeti, seçtiğiniz seçeneklere ve kullanımınıza bağlıdır. Daha fazla bilgi için bkz. API [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cognitive-services/).

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="Tek hizmet kaynak oluşturma ekranı":::

**İleri** ' yi seçin ve kaynağınız için izin vermek istediğiniz ağ erişiminin türünü seçin ve ardından **gözden geçir + oluştur**' u seçin.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Kaynağınızın anahtarlarını alın

1. Kaynağınız başarıyla dağıtıldıktan sonra, **sonraki adımlar** altında **Kaynağa Git ' e** tıklayın.

    ![Bilişsel hizmetler için arama](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Açılan hızlı başlangıç bölmesinden, anahtarınız ve uç noktanıza erişebilirsiniz.

    ![Anahtar ve uç nokta al](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, grupta yer alan diğer tüm kaynakları da siler.

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve **Kaynak Grupları**'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynağı içeren kaynak grubunu bulun
3. Kaynak grubu listesine sağ tıklayın. **Kaynak grubunu sil**'i seçip onaylayın.

## <a name="see-also"></a>Ayrıca bkz.

* Bilişsel hizmetlerle güvenli bir şekilde çalışmak için bkz. Azure bilişsel **[Hizmetler 'e yönelik kimlik doğrulama istekleri](authentication.md)** .
* Bilişsel hizmetler 'deki farklı kategorilerin listesini almak için bkz. **[Azure bilişsel hizmetler nedir?](./what-are-cognitive-services.md)** .
* Bilişsel hizmetlerin desteklediği doğal dillerin listesini görmek için bkz. **[doğal dil desteği](language-support.md)** .
* Bilişsel hizmetler 'in şirket içi hizmetlerini nasıl kullanacağınızı anlamak için bkz. bilişsel **[Hizmetler 'i kapsayıcı olarak kullanma](cognitive-services-container-support.md)** .
* Bilişsel hizmetler 'in kullanım maliyetini tahmin etmek için bilişsel **[Hizmetler 'in maliyetlerini planlayın ve yönetin](plan-manage-costs.md)** bölümüne bakın.
