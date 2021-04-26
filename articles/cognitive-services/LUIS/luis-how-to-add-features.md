---
title: LUSıS sözcük listesini geliştirmek için özellikleri kullanma
titleSuffix: Azure Cognitive Services
description: Bu kategorilerin ve desenlerin ve varlıklarının kullanımını ve tahminini iyileştirebilecek uygulama özellikleri eklemek için Language Understanding (LUO) kullanın
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c1a0fb15be92e25b576cd29e671eed3c6a5b948f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313275"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Sözcük listesinin sinyalini artırmak için özellikleri kullanın

Doğruluğunu artırmak için LUSıS uygulamanıza özellikler ekleyebilirsiniz. Özellikler, belirli sözcüklerin ve deyimlerin bir uygulama etki alanı sözlüğü 'nün parçası olduğunu gösteren ipuçları sunarak LUYA yardımcı olur.

Bir özelliğin ne zaman ve neden kullanılacağını anlamak için [kavramları](luis-concept-feature.md) gözden geçirin.

## <a name="add-phrase-list-as-a-feature"></a>Özellik olarak tümcecik listesi ekleme

1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.
1. **Oluştur**' u ve ardından uygulamanızın sol panelinden **Özellikler** ' i seçin.

1. **Özellikler** sayfasında **+ Oluştur**' u seçin.

1. **Yeni tümcecik listesi özelliği oluştur** iletişim kutusunda, gibi bir ad girin `Cities` . **Değer** kutusuna, gibi şehirlerin örneklerini girin `Seattle` . Tek seferde bir değer veya virgülle ayrılmış bir değerler kümesi yazabilir ve ardından **ENTER** tuşuna basabilirsiniz.

    > [!div class="mx-imgBorder"]
    > ![Özellik (tümcecik listesi) şehirleri ekleme ekran görüntüsü](./media/luis-add-features/add-phrase-list-cities.png)

    LUO için yeterli değer girdikten sonra öneriler görünür. Önerilen değerlerin **Tümünü ekleyebilir** veya tek tek terimleri seçebilirsiniz.

1. Deyimlerin birbirlerinin yerine kullanılabilir olması halinde **Bu değerleri koru değiştirilebilir** . Değiştirilebilir ifade listesi özelliği, eğitim için eş anlamlıların bir listesi olarak görev yapar. Çapraz olmayan tümcecik listesi, eğitimlere yönelik ayrı özellikler görevi görür (Özellikler benzer ancak tümcecikleri değiştirdiğinizde hedef değişir).

1. Tümcecik listesi, **genel** ayarı olan tüm uygulamaya veya belirli bir modele (amaç veya varlık) uygulanabilir. Tümcecik listesini bir amaç veya varlıktan bir _özellik_ olarak oluşturursanız, geçiş işlemi genel için ayarlanmadı. Bu durumda, iki durumlu özelliğin anlamı, uygulamanın _genel değil_ , bu modelde yalnızca yerel olduğu bir modeldir.

1. **Bitti** seçeneğini belirleyin. Yeni özellik **ml özellikleri** sayfasına eklenir.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Bir tümcecik listesini, **ml özellikleri** sayfasındaki bağlamsal araç çubuğundan silebilir veya devre dışı bırakabilirsiniz.

## <a name="global-phrase-list-applies-to-entire-app"></a>Genel ifade listesi tüm uygulama için geçerlidir

Bir tümcecik listesi, yardım almak için tasarlanan amaç veya varlığa uygulanmalıdır, ancak bir tümcecik listesinin tüm uygulamaya **genel** bir özellik olarak uygulanması gerektiği zamanlar olabilir.

ML özellikleri sayfasında, tümcecik listesini seçin ve ardından üst bağlamsal araç çubuğunda **genel yap** ' ı seçin.

## <a name="model-as-a-feature"></a>Özellik olarak model oluşturma

Bir varlık bir [amaç veya varlığa yönelik bir özellik](luis-concept-feature.md)olabilir.

Bir amaca özellik olarak bir varlık eklemek için amaçlar sayfasından amacı seçin ve bağlam araç çubuğunun üstünde **+ Özellik Ekle** ' yi seçin. Liste, özellik olarak uygulanabilecek tüm tümcecik listelerini ve varlıkları içerir.

Bir varlığı başka bir varlığa bir özellik olarak eklemek için, [varlık paletini](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) kullanarak amaç ayrıntısı sayfasında özelliğini ekleyebilir veya özelliği varlık ayrıntısı sayfasına [ekleyebilirsiniz](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) .

## <a name="next-steps"></a>Sonraki adımlar

Bir özelliği ekledikten, düzenledikten, sildikten veya devre dışı bırakadıktan sonra, performansın gelişip artmediğini görmek için [uygulamayı yeniden eğitin ve test](luis-interactive-test.md) edin.
