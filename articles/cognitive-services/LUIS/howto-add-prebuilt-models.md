---
title: Language Understanding için önceden oluşturulmuş modeller
titleSuffix: Azure Cognitive Services
description: LUO, yaygın olarak kullanılan, konuşma Kullanıcı senaryolarını hızlıca eklemek için önceden oluşturulmuş bir modeller kümesi içerir.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541926"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Yaygın kullanım senaryoları için önceden oluşturulmuş modeller ekleyin

LUO, yaygın olarak kullanılan, konuşma Kullanıcı senaryolarını hızlıca eklemek için önceden oluşturulmuş bir modeller kümesi içerir. Bu yetenekler için modeller tasarlamaya gerek kalmadan, konuşma istemci uygulamanıza yetenekler eklemenin hızlı ve kolay bir yoludur.

## <a name="add-a-prebuilt-domain"></a>Önceden oluşturulmuş bir alan ekleme

1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.

1. Sol araç çubuğundan **önceden oluşturulmuş etki alanlarını** seçin.

1. Uygulamaya eklenmesini istediğiniz etki alanını bulun ve ardından **etki alanı Ekle** düğmesini seçin.

    > [!div class="mx-imgBorder"]
    > ![Takvim önceden oluşturulmuş etki alanı Ekle](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Önceden oluşturulmuş bir amaç ekleyin

1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.

1. **Amaçlar** sayfasında, amaçlar listesinin üzerindeki araç çubuğundan **önceden oluşturulmuş etki alanı hedefi Ekle** ' yi seçin.

1. Açılır iletişim kutusundan bir amaç seçin.

    > [!div class="mx-imgBorder"]
    > ![Önceden oluşturulmuş amaç Ekle](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. **Bitti** düğmesini seçin.

## <a name="add-a-prebuilt-entity"></a>Önceden oluşturulmuş bir varlık ekleyin
1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.
1. Sol taraftaki **varlıkları** seçin.

1. **Varlıklar** sayfasında, **önceden oluşturulmuş varlık Ekle**' yi seçin.

1. **Önceden oluşturulmuş varlıklar Ekle** iletişim kutusunda, önceden oluşturulmuş varlığı seçin.

    > [!div class="mx-imgBorder"]
    > ![Önceden oluşturulmuş varlık Ekle iletişim kutusu](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. **Bitti** seçeneğini belirleyin. Varlık eklendikten sonra, uygulamayı eğmenize gerek kalmaz.

## <a name="add-a-prebuilt-domain-entity"></a>Önceden oluşturulmuş bir etki alanı varlığı ekleme
1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Uygulamalarım** sayfasında adını seçerek uygulamanızı açın.
1. Sol taraftaki **varlıkları** seçin.

1. **Varlıklar** sayfasında, **önceden oluşturulmuş etki alanı varlığı Ekle**' yi seçin.

1. **Önceden oluşturulmuş etki alanı modelleri ekle** iletişim kutusunda, önceden oluşturulmuş etki alanı varlığını seçin.

1. **Bitti** seçeneğini belirleyin. Varlık eklendikten sonra, uygulamayı eğmenize gerek kalmaz.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Ön derlenmiş modeli tahmin uç noktasından görüntülemek için Yayımla

Önceden oluşturulmuş bir modelin değerini görüntülemenin en kolay yolu, yayımlanan uç noktadan sorgu kullanmaktır.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Önceden oluşturulmuş bir varlık belirteci içeren varlıklar

Önceden oluşturulmuş bir varlık için gerekli bir özelliğe ihtiyacı olan bir makine öğrenimi varlığınız varsa, makine öğrenimi varlığına bir alt varlık ekleyin ve ardından önceden oluşturulmuş bir varlığın _gerekli_ bir özelliğini ekleyin.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [REST API 'lerle. csv 'den model oluşturma](./luis-tutorial-node-import-utterances-csv.md)
