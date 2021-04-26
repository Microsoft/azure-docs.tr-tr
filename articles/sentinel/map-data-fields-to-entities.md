---
title: Veri alanlarını Azure Sentinel varlıklarına eşleme | Microsoft Docs
description: Daha iyi olay bilgileri için tablolardaki veri alanlarını analiz kurallarında Azure Sentinel varlıklarına eşleyin
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456334"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Azure Sentinel 'de veri alanlarını varlıklara eşleme 

> [!IMPORTANT]
>
> - Varlık eşleme özelliğinin yeni sürümü **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

> [!IMPORTANT]
>
> - Geriye dönük uyumluluk ve varlık eşlemesinin yeni ve eski sürümleri arasındaki farklılıklar hakkında önemli bilgiler için bu belgenin sonundaki [yeni sürümdeki notlara](#notes-on-the-new-version) bakın.

## <a name="introduction"></a>Giriş

Varlık eşleme, [Zamanlanmış sorgu Analizi kurallarının](tutorial-detect-threats-custom.md)yapılandırmasının ayrılmaz bir parçasıdır. Herhangi bir araştırma işleminin yapı taşları ve takip eden eylemleri sunan önemli bilgilerle kuralları çıkış (Uyarılar ve olaylar) ile zenginleştirir.

Aşağıda ayrıntılı yordam, analiz kuralı oluşturma sihirbazının bir parçasıdır. Mevcut bir analiz kuralında varlık eşlemelerini ekleme veya değiştirme senaryosuna yönelik olarak burada ele alınır.

## <a name="how-to-map-entities"></a>Varlıkları eşleme

1. Azure Sentinel gezinti menüsünde **analiz**' ı seçin.

1. Zamanlanmış bir sorgu kuralı seçin ve **Düzenle**' ye tıklayın. Ya da ekranın üst kısmındaki **&#10132; zamanlanmış sorgu kuralı oluştur** ' a tıklayarak yeni bir kural oluşturun.

1. **Kural mantığını ayarla** sekmesine tıklayın.

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Alanları varlıklara eşleme":::

1. **Uyarı geliştirme** bölümünde, **varlık eşleme** altında **varlık türü** aşağı açılan listesinden bir varlık türü seçin.

1. Varlık için bir **tanımlayıcı** seçin. Tanımlayıcılar, onu yeterince belirleyebilen bir varlığın öznitelikleridir. **Tanımlayıcı** açılan listesinden birini seçin ve ardından tanımlayıcıya karşılık gelen **değer** açılan listesinden bir veri alanı seçin. Bazı özel durumlar ile, **değer** listesi kural sorgusunun konusu olarak tanımlanan tablodaki veri alanları tarafından doldurulur.

    Belirli bir varlık için **en fazla üç tanımlayıcı** tanımlayabilirsiniz. Bazı tanımlayıcılar gereklidir, diğerleri isteğe bağlıdır. En az bir gerekli tanımlayıcı seçmeniz gerekir. Aksi takdirde, hangi tanımlayıcıların gerekli olduğunu size bildiren bir uyarı mesajı görüntülenir. En iyi sonuçlar için, en fazla benzersiz kimlik için mümkün olduğunda **güçlü tanımlayıcılar** kullanmanız gerekir ve birden çok güçlü tanımlayıcı kullanmak veri kaynakları arasında daha fazla bağıntı sağlar. Kullanılabilir [varlıkların ve tanımlayıcıların](entities-reference.md)tam listesine bakın.

1. Daha fazla varlık eşlemek için **yeni varlık Ekle** ' ye tıklayın. Tek bir analiz kuralında **beş adede kadar varlığı** eşleyebilirsiniz. Aynı türden birden fazla tane de eşleyebilirsiniz. Örneğin, biri *kaynak IP adresi* alanından ve BIR *hedef IP adresi* alanından iki **IP** varlığını eşleyebilirsiniz. Bu şekilde, bunları her ikisini de izleyebilirsiniz.

    Fikrinizi değiştirirseniz veya bir hata yaptıysanız, varlık açılan listesinin yanındaki çöp kutusu simgesine tıklayarak bir varlık eşlemesini kaldırabilirsiniz.

1. Varlıkları eşlemeyi bitirdiğinizde, **gözden geçir ve oluştur** sekmesine tıklayın. Kural doğrulaması başarılı olduktan sonra **Kaydet**' e tıklayın.

## <a name="notes-on-the-new-version"></a>Yeni sürüm notları

- Bu analiz kuralı için eski sürümü kullanarak daha önce varlık eşlemelerini tanımladıysanız, Bu eşlemeler sorgu kodunda görüntülenir. Yeni sürüm altında tanımlanan varlık eşlemeleri **sorgu kodunda görünmez**. Analiz kuralları tek seferde yalnızca bir varlık eşlemeleri sürümünü destekleyebilir ve yeni sürüm önceliklidir. Bu nedenle, burada tanımladığınız herhangi bir tek eşleme, sorgu çalıştırıldığında sorgu kodunda tanımlanan **Tüm eşlemelerin yok** **sayılana** neden olur. 

- Hala varlık eşlemesinin **eski sürümünü** kullanmanız gerekiyorsa (yeni sürüm Önizleme sürümünde olduğu sürece), URL 'deki bir özellik bayrağını kullanarak buna erişmeye devam edebilirsiniz. İmlecinizi ve arasına yerleştirin `https://portal.azure.com/` `#blade` ve metni ekleyin `?feature.EntityMapping=false` .

  - Eski sürümün limitleri uygulanmaya devam edecektir. Yalnızca Kullanıcı, ana bilgisayar, IP adresi, URL ve dosya karma varlıklarını ve yalnızca birini eşleyebilirsiniz.

  - Eski sürüme döndürmeden **önce** yeni sürüm kullanılarak oluşturulan tüm varlık eşlemelerini **kaldırmanız** gerekir; Aksi takdirde, eski sürümü kullanan tüm varlık eşlemeleri **çalışmaz**.

- Varlık eşlemesinin yeni sürümü genel kullanıma sunulduktan sonra eski sürümü kullanmak artık mümkün olmayacaktır. Eski varlık eşlemelerinizi yeni sürüme geçirmeniz önemle önerilir.


## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel Analytics kurallarında veri alanlarını varlıklara nasıl eşleneceğini öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Zamanlanan sorgu Analizi kurallarında](tutorial-detect-threats-custom.md)tüm resmi alın.
- [Azure Sentinel 'de varlıklar](entities-in-azure-sentinel.md)hakkında daha fazla bilgi edinin.
