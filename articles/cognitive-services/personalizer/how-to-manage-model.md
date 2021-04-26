---
title: Model ve öğrenme ayarlarını yönetme-kişiselleştirici
description: Makine tarafından öğrenilen model ve öğrenme ayarları, yedekleme için kendi kaynak denetimi sisteminizde aktarılabilir.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 48e954eab9da5dfc638f93a7c4e55c675c4f2ac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97797256"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Model ve öğrenme ayarlarını yönetme

Makine tarafından öğrenilen model ve öğrenme ayarları, yedekleme için kendi kaynak denetimi sisteminizde aktarılabilir.

## <a name="export-the-personalizer-model"></a>Kişiselleştirici modelini dışarı aktarma

**Model ve öğrenme ayarları** için kaynak yönetiminin bölümünde, model oluşturma ve son güncelleme tarihi ' ni gözden geçirin ve geçerli modeli dışarı aktarın. Arşiv amaçlarıyla bir model dosyasını dışarı aktarmak için Azure portal veya kişiselleştirici API 'Leri kullanabilirsiniz.

![Geçerli kişiselleştirici modelini dışarı aktar](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Öğrenme döngünüz için verileri temizleme

1. Azure portal, kişiselleştirici kaynağınız için **model ve öğrenme ayarları** sayfasında, **verileri temizle**' yi seçin.
1. Tüm verileri temizlemek ve öğrenme döngüsünü özgün durumuna sıfırlamak için 3 onay kutusunu seçin.

    ![Azure portal, kişiselleştirici kaynaktaki verileri temizleyin.](./media/settings/clear-data-from-personalizer-resource.png)

    |Değer|Amaç|
    |--|--|
    |Günlüğe kaydedilen kişiselleştirme ve yeniden dengeleme verileri.|Bu günlüğe kaydetme verileri, çevrimdışı değerlendirmelere göre kullanılır. Kaynağı sıfırlarken verileri temizleyin.|
    |Kişiselleştirici modelini sıfırlayın.|Bu model her yeniden eğitimine göre değişir. Bu eğitim sıklığı **yapılandırma** sayfasında **model yükleme sıklığı** ' nda belirtilmiştir. |
    |Öğrenme ilkesini varsayılan olarak ayarlayın.|Öğrenme ilkesini çevrimdışı değerlendirmenin bir parçası olarak değiştirdiyseniz, bu, özgün öğrenme ilkesine sıfırlanır.|

1. Temizleme işlemini başlatmak için **seçili verileri temizle** ' yi seçin. Durum, Azure bildirimlerinde sağ üst gezinti bölmesinde raporlanır.

## <a name="import-a-new-learning-policy"></a>Yeni bir öğrenme ilkesini içeri aktar

[Öğrenme ilkesi](concept-active-learning.md#understand-learning-policy-settings) ayarları, model eğitiminin _hiper parametrelerini_ belirlenir. Yeni bir öğrenme ilkesi bulmak için [çevrimdışı bir değerlendirme](how-to-offline-evaluation.md) gerçekleştirin.

1. [Azure Portal](https://portal.azure.com)açın ve kişiselleştirici kaynağı ' nı seçin.
1. **Kaynak yönetimi** bölümünde **model ve öğrenme ayarları** ' nı seçin.
1. **Içeri aktarma öğrenimi ayarları** için yukarıda belirtilen JSON biçimiyle oluşturduğunuz dosyayı seçin, ardından **karşıya yükle** düğmesini seçin.

    Öğrenme ilkesinin başarıyla karşıya yüklendiğini bildirimin bekleyin.

## <a name="export-a-learning-policy"></a>Bir öğrenme ilkesini dışarı aktarma

1. [Azure Portal](https://portal.azure.com)açın ve kişiselleştirici kaynağı ' nı seçin.
1. **Kaynak yönetimi** bölümünde **model ve öğrenme ayarları** ' nı seçin.
1. **Içeri aktarma öğrenimi ayarları** için **öğrenme ayarlarını dışarı aktar** düğmesini seçin. Bu `json` dosya yerel bilgisayarınıza kaydedilir.

## <a name="next-steps"></a>Sonraki adımlar

[Çevrimdışı değerlendirmede öğrenme döngünüzü çözümleyin](how-to-offline-evaluation.md)
