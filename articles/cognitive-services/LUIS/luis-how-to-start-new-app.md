---
title: Yeni bir uygulama oluşturun-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS) Web sayfasında uygulamalarınızı oluşturun ve yönetin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904256"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUSıS portalında yeni bir LUO uygulaması oluşturma
Bir LUSıS uygulaması oluşturmanın birkaç yolu vardır. LUIS portalında veya LUıS yazma [API 'leri](developer-reference-resource.md)aracılığıyla bir LUIS uygulaması oluşturabilirsiniz.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>LUO portalını kullanma

Önizleme portalında çeşitli yollarla yeni bir uygulama oluşturabilirsiniz:

* Boş bir uygulamayla başlayın ve amaçlar, utbotslar ve varlıklar oluşturun.
* Boş bir uygulama ile başlayın ve [önceden oluşturulmuş bir etki alanı](luis-how-to-use-prebuilt-domains.md)ekleyin.
* Bir `.lu` veya `.json` bir, amaç, utde ve varlık içeren bir dosya dosyasından bir LUO uygulamasını içeri aktarın.

## <a name="using-the-authoring-apis"></a>Yazma API 'Lerini kullanma
Yazma API 'Leriyle birkaç yolla yeni bir uygulama oluşturabilirsiniz:

* [Uygulama ekleyin](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) -boş bir uygulamayla başlayın ve amaçlar, utbotslar ve varlıklar oluşturun.
* [Önceden oluşturulmuş uygulama ekleme](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) -amaçlar, utterslar ve varlıklar dahil önceden oluşturulmuş bir etki alanıyla başlayın.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUSıS 'de yeni uygulama oluşturma

1. **Uygulamalarım** sayfasında aboneliğinizi seçin ve kaynağı yazıp **+ Oluştur**' a tıklayın. Ücretsiz deneme anahtarı kullanıyorsanız, [yazma kaynağı oluşturmayı](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)öğrenin.

    ![LUIS uygulamalarının listesi](./media/create-app-in-portal.png)


1. İletişim kutusuna uygulamanızın adını girin (örneğin, `Pizza Tutorial`).

    ![Yeni uygulama oluştur iletişim kutusu](./media/create-pizza-tutorial-app-in-portal.png)

1. Uygulamanızın kültür ' i seçin ve **bitti**' yi seçin. Açıklama ve tahmin kaynağı bu noktada isteğe bağlıdır. Daha sonra portalın **Yönet** bölümünde istediğiniz zaman ayarlayabilirsiniz.

    > [!NOTE]
    > Uygulama oluşturduktan sonra kültür değiştirilemez. 

    Uygulama oluşturulduktan sonra, Lua portalı, sizin için zaten oluşturulan `None` amacını içeren **amaçlar** listesini gösterir. Artık boş bir uygulamanız var. 
    
    > [!div class="mx-imgBorder"]
    > hiçbir örnek olmadan oluşturulan amaç listesi ![.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Diğer eylemler kullanılabilir

Bağlam araç çubuğu diğer eylemleri sağlar:

* Uygulamayı yeniden adlandır
* `.lu` veya `.json` kullanarak dosyadan içeri aktar
* Uygulamayı `.lu` ( [LUI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)için), `.json`veya `.zip` ( [lusıs kapsayıcısı](luis-container-howto.md)için) olarak dışa aktarma
* Uç nokta utslerini gözden geçirmek için kapsayıcı uç nokta günlüklerini içeri aktarma
* Çevrimdışı analiz için `.csv`olarak uç nokta günlüklerini dışarı aktar
* Uygulamayı Sil

## <a name="next-steps"></a>Sonraki adımlar

Uygulama tasarımınızda amaç algılama, [yeni amaçlar oluşturma](luis-how-to-add-intents.md)ve örnek ekleme dahil olmak üzere. Uygulama tasarımınız yalnızca veri ayıklama ise, None amacına örnek bir değer ekleyin, daha sonra [varlıklar oluşturun](luis-how-to-add-example-utterances.md)ve bu varlıklarla örnek utbotları etiketleyin. 
