---
title: Azure Sentinel uyarılarında yüzey özel ayrıntıları | Microsoft Docs
description: Daha iyi ve daha kapsamlı olay bilgileri için, Azure Sentinel Analytics kuralları 'ndaki uyarılarda özel olay ayrıntılarını ayıklayın
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
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456321"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Azure Sentinel 'de uyarılarda özel olay ayrıntıları 

> [!IMPORTANT]
>
> - Özel ayrıntılar özelliği **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="introduction"></a>Giriş

[Zamanlanan sorgu analizi kuralları](tutorial-detect-threats-custom.md) , Azure Sentinel 'e bağlı veri kaynaklarından **olayları** analiz eder ve bu olayların içeriği bir güvenlik perspektifinden önemli olduğunda **Uyarılar** oluşturur. Bu uyarılar daha fazla analiz edilir, gruplandırılır ve Azure Sentinel 'in çeşitli altyapılarına göre filtrelenmiştir ve bir SOC analistinin ilgilenmesini sağlayan **olaylara** ayrılabilir. Ancak analist olayı görüntülediğinde yalnızca bileşen uyarılarının özellikleri hemen görünür. Gerçek içeriğe alma-olaylarda yer alan bilgiler, biraz biraz daha fazlasını gerektirir.

**Analiz kuralı sihirbazındaki** **özel ayrıntılar** özelliğini kullanarak, olay verilerini bu olaylardan oluşturulan uyarılarda, uyarı özelliklerinin olay verilerini parçasını yaparak bırakabilirsiniz. Aslında bu, olaylarınızın anında olay içeriği görünürlüğünü sunarak, ekibinizle ve daha fazla hız ve verimlilik elde etmenizi sağlar.

Aşağıda ayrıntılı yordam, analiz kuralı oluşturma sihirbazının bir parçasıdır. Mevcut bir analiz kuralında özel ayrıntıları ekleme veya değiştirme senaryosuna yönelik olarak burada ele alınır.

## <a name="how-to-surface-custom-event-details"></a>Özel olay ayrıntılarını yüzey olarak belirleme

1. Azure Sentinel gezinti menüsünde **analiz**' ı seçin.

1. Zamanlanmış bir sorgu kuralı seçin ve **Düzenle**' ye tıklayın. Ya da ekranın üst kısmındaki **&#10132; zamanlanmış sorgu kuralı oluştur** ' a tıklayarak yeni bir kural oluşturun.

1. **Kural mantığını ayarla** sekmesine tıklayın.

1. **Uyarı geliştirme** bölümünde **özel ayrıntılar**' ı seçin.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Özel ayrıntıları bul ve Seç":::

1. Şimdi genişletilmiş **özel ayrıntılar** bölümünde, yüzeyine eklemek istediğiniz ayrıntılara karşılık gelen anahtar-değer çiftlerini ekleyin:

    1. **Anahtar** alanına, uyarılarda alan adı olarak görünecek seçtiğiniz bir adı girin.

    1. **Değer** alanında, açılan listeden uyarılarda bırakmak istediğiniz olay parametresini seçin. Bu liste, kural sorgusunun konusu olan tablolardaki alanlara karşılık gelen değerlere göre doldurulur.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Özel ayrıntılar Ekle":::

1. Daha fazla ayrıntı için **Yeni Ekle** ' ye tıklayın, son adımları yineleyerek anahtar-değer çiftlerini tanımlayın. 

    Fikrinizi değiştirirseniz veya bir hata yaptıysanız, bu ayrıntı için **değer** açılan listesinin yanındaki çöp kutusu simgesine tıklayarak özel bir ayrıntıyı kaldırabilirsiniz.

1. Özel ayrıntıları tanımlamayı bitirdiğinizde, **gözden geçir ve oluştur** sekmesine tıklayın. Kural doğrulaması başarılı olduktan sonra **Kaydet**' e tıklayın.

    > [!NOTE]
    > 
    >**Hizmet sınırlamaları**
    > - Tek bir analiz kuralında, **en fazla 20 özel ayrıntı** tanımlayabilirsiniz.
    >
    > - Tüm özel ayrıntılar için boyut sınırı, toplu olarak **2 KB**'tır.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Sentinel Analytics kurallarını kullanarak uyarılarda özel ayrıntıların nasıl yüzey alınacağını öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Zamanlanan sorgu Analizi kurallarında](tutorial-detect-threats-custom.md)tüm resmi alın.
- [Azure Sentinel 'de varlıklar](entities-in-azure-sentinel.md)hakkında daha fazla bilgi edinin.
