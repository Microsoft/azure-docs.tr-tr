---
title: Language Understanding için önceden oluşturulmuş modeller
titleSuffix: Azure Cognitive Services
description: LUO, yaygın olarak kullanılan, konuşma Kullanıcı senaryolarını hızlıca eklemek için önceden oluşturulmuş bir modeller kümesi içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013545"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Yaygın kullanım senaryoları için önceden oluşturulmuş modeller ekleyin 

LUO, yaygın olarak kullanılan, konuşma Kullanıcı senaryolarını hızlıca eklemek için önceden oluşturulmuş bir modeller kümesi içerir. Bu yetenekler için modeller tasarlamaya gerek kalmadan, konuşma istemci uygulamanıza yetenekler eklemenin hızlı ve kolay bir yoludur. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Önceden oluşturulmuş bir alan ekleme

1. Üzerinde **uygulamalarım** sayfasında, uygulamanızı seçin. Uygulamanıza açılır **derleme** uygulama bölümü. 

1. Sol araç çubuğundan **önceden oluşturulmuş etki alanlarını** seçin. 

1. Uygulamaya eklenmesini istediğiniz etki alanını bulun ve ardından **etki alanı Ekle** düğmesini seçin.

    > [!div class="mx-imgBorder"]
    > ![takvim önceden oluşturulmuş etki alanı Ekle](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Önceden oluşturulmuş bir hedefi ekleyin

1. Üzerinde **uygulamalarım** sayfasında, uygulamanızı seçin. Uygulamanıza açılır **derleme** uygulama bölümü. 

1. **Amaçlar** sayfasında, amaçlar listesinin üzerindeki araç çubuğundan **önceden oluşturulmuş etki alanı hedefi Ekle** ' yi seçin. 

1. Seçin **Utilities.Cancel** açılan iletişim kutusundan hedefi. 

    > [!div class="mx-imgBorder"]
    > ![önceden oluşturulmuş amaç ekleme](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Seçin **Bitti** düğmesi.

## <a name="add-a-prebuilt-entity"></a>Önceden oluşturulmuş bir varlık ekleme

1. Adını tıklayarak uygulamanızı açın **uygulamalarım** sayfasında ve ardından **varlıkları** sol tarafındaki. 

1. Üzerinde **varlıkları** sayfasında **önceden oluşturulmuş bir varlık ekleme**.

1. **Önceden oluşturulmuş varlıklar Ekle** iletişim kutusunda, önceden oluşturulmuş varlığı seçin. 

    > [!div class="mx-imgBorder"]
    > ![önceden oluşturulmuş varlık Ekle iletişim kutusu](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. **Done** (Bitti) öğesini seçin. Varlık eklendikten sonra uygulamayı eğitme gerekmez. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Ön derlenmiş modeli tahmin uç noktasından görüntülemek için Yayımla

Önceden oluşturulmuş bir modelin değerini görüntülemenin en kolay yolu, yayımlanan uç noktadan sorgu kullanmaktır. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Önceden oluşturulmuş bir varlık belirteci içeren varlıklar
 
Önceden oluşturulmuş bir varlık tarafından kısıtlanan makine tarafından öğrenilen bir varlığınız varsa, makine tarafından öğrenilen varlığa bir alt bileşen ekleyin ve ardından önceden oluşturulmuş bir varlığın kısıtlamasını ekleyin.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [REST API 'lerle. csv 'den model oluşturma](./luis-tutorial-node-import-utterances-csv.md)
