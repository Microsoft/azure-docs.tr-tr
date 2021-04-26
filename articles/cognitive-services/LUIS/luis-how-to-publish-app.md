---
title: App-LUSıS Yayımla
titleSuffix: Azure Cognitive Services
description: Etkin LUSıS uygulamanızı oluşturma ve test etmeyi bitirdiğinizde, uç noktada yayımlayarak istemci uygulamanız için kullanılabilir hale getirin.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8e78fc5bd49aaf2b31fdc83ced132e2a39ca83d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100558930"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Etkin, eğitilen uygulamanızı bir hazırlama veya üretim uç noktasına yayımlayın

Etkin LUSıS uygulamanızı oluşturma, eğitim ve test etmeyi bitirdiğinizde, uç noktada yayımlayarak istemci uygulamanız için kullanılabilir hale getirin.

## <a name="publishing"></a>Yayımlama
1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.
1. Uç noktaya yayımlamak için, üstteki sağ panelde **Yayımla** ' yı seçin.

    ![En üstte Yayımla düğmesi, sağ gezinti çubuğu](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Yayımlanan tahmin uç noktası için ayarlarınızı seçip **Yayımla**' yı seçin.

    ![Yayınlama Ayarları ' nı ve ardından Yayınla düğmesini seçin](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Yayımlama Yuvaları

Açılır pencere görüntülendiğinde doğru yuvayı seçin:

* Hazırlama
* Üretim

Bu, yayımlama yuvalarını iki farklı uç noktada veya aynı sürüme sahip olmak üzere, hem yayımlama yuvaları hem de uygulamanızın iki farklı sürümünün kullanılabilmesini sağlar.

### <a name="publishing-regions"></a>Yayımlama bölgeleri

Uygulama,   ->  **[Azure kaynaklarını](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** Yönet sayfasından halsıs portalında eklenen Lue tahmin uç noktası kaynaklarıyla ilişkili tüm bölgelerde yayımlanır.

Örneğin, [www.Luis.ai](https://www.luis.ai)üzerinde oluşturulan bir uygulama için, iki bölgede bir Luo kaynağı oluşturursanız, **westus** ve **eastus** ve bunları uygulamaya kaynak olarak eklerseniz, uygulama her iki bölgede da yayımlanır. LUO bölgeleri hakkında daha fazla bilgi için bkz. [bölgeler](luis-reference-regions.md).

> [!TIP]
> 3 yazma bölgesi vardır. Yayımlamak istediğiniz bölgede yazmanız gerekir. Tüm bölgelerde yayımlamanız gerekiyorsa, yazma işleminizi ve elde edilen eğitilen modeli tüm 3 yazma bölgelerinde yönetmeniz gerekir.


## <a name="configuring-publish-settings"></a>Yayımlama ayarlarını yapılandırma

Yuvayı seçtikten sonra, yayımlama ayarlarını şu şekilde yapılandırın:

* Yaklaşım analizi
* Konuşma primi

Yayımladıktan sonra bu ayarlar, bölümün **Yayımlama ayarlarını** **Yönet** sayfasından gözden geçirilmek üzere kullanılabilir. Ayarları her Yayımla ile değiştirebilirsiniz. Bir yayımlamayı iptal ederseniz, yayımlama sırasında yaptığınız tüm değişiklikler de iptal edilir.

### <a name="when-your-app-is-published"></a>Uygulamanız yayımlandığında

Uygulamanız başarıyla yayımlandığında, tarayıcının en üstünde bir başarı bildirimi görüntülenir. Bildirim Ayrıca uç noktalara bir bağlantı içerir.

Uç nokta URL 'sine ihtiyacınız varsa bağlantıyı seçin. Üst menüden **Yönet** ' i seçip sol menüden **Azure kaynakları** ' nı seçerek uç nokta URL 'lerine de ulaşabilirsiniz.

## <a name="sentiment-analysis"></a>Yaklaşım analizi

<a name="enable-sentiment-analysis"></a>

Yaklaşım analizi, LUTO 'NıN yaklaşım ve anahtar tümceciği analizi sağlamak için [metin analizi](https://azure.microsoft.com/services/cognitive-services/text-analytics/) ile tümleştirilemesine olanak tanır.

Bir Metin Analizi anahtarı sağlamanız gerekmez ve Azure hesabınızda bu hizmet için faturalandırma ücreti yoktur.

Yaklaşım verileri, 1 ile 0 arasında pozitif (1 ' e yakın) veya negatif (0 ' a yakın) verilerin yaklaşımını belirten bir puandır. , Ve ' nin yaklaşım etiketi `positive` `neutral` `negative` desteklenen kültür başına. Şu anda yalnızca Ingilizce yaklaşım etiketlerini destekler.

Yaklaşım analizine sahip JSON uç noktası yanıtı hakkında daha fazla bilgi için bkz. yaklaşım [Analizi](luis-reference-prebuilt-sentiment.md)

## <a name="speech-priming"></a>Konuşma primi

Konuşma primi, metin konuşmaya dönüştürme işleminden önce, konuşma Hizmetleri için LUSıS modelinin gönderilmesini kullanma sürecinden oluşur. Bu, konuşma hizmetinin modelinize daha doğru konuşma dönüştürmesi sağlamasına izin verir. Bu, bir konuşma araması yaparak ve bir LUO yanıtı geri alarak tek bir çağrıda botu ve LUSıS isteklerini ve yanıtlarını sağlar. Genel olarak daha az gecikme sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* Bkz. Azure abonelik anahtarına anahtar eklemek için [anahtarları yönetme](./luis-how-to-azure-subscription.md) ve Bing yazım denetimi anahtarı ayarlama ve sonuçlara tüm amaçları ekleme.
* Yayımlanan uygulamanızı test konsolunda test etme hakkında yönergeler için bkz. [uygulamanızı eğitme ve test](luis-interactive-test.md) etme.

