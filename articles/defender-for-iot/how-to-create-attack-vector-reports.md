---
title: Saldırı vektör raporları oluşturma
description: Saldırı vektör raporları, açıktan yararlanılabilecek bir cihaz güvenlik açığı zincirinin grafik gösterimini sağlar.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784382"
---
# <a name="attack-vector-reporting"></a>Saldırı vektörü raporlaması

## <a name="about-attack-vector-reports"></a>Saldırı vektörü raporları hakkında

Saldırı vektör raporları, açıktan yararlanılabilecek bir cihaz güvenlik açığı zincirinin grafik gösterimini sağlar. Bu güvenlik açıkları, bir saldırganın ana ağ cihazlarına erişmesini sağlayabilir. Saldırı vektörü simülatörü, saldırı vektörlerini gerçek zamanlı olarak hesaplar ve belirli bir hedefin tüm saldırı vektörlerini analiz eder.

Saldırı vektörü ile çalışma, saldırı sırasındaki risk azaltma etkinliklerinin etkisini değerlendirmenize imkan tanır. Daha sonra, örneğin, bir sistem yükseltmesinin saldırı zincirini bozarak saldırganın yolunu kesintiye uğramadığını veya alternatif bir saldırı yolu kalmadığını belirleyebilirsiniz. Bu bilgiler, düzeltme ve risk azaltma etkinliklerini önceliklendirmenize yardımcı olur.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Denetim Merkezi 'nde uyarılarınızı görüntüleyin.":::

> [!NOTE]
> Yöneticiler ve Güvenlik analistleri bu bölümde açıklanan yordamları gerçekleştirebilir.

## <a name="create-an-attack-vector-report"></a>Saldırı vektörü raporu oluşturma

Bir saldırı vektörü simülasyonu oluşturmak için:

1. Simülasyon eklemek için yan menüdeki :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="artı işaretini":::seçin.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Saldırı vektörü benzetimi.":::

2. Simülasyon özelliklerini girin:

   - **Ad**: benzetim adı.

   - **En fazla vektör**: tek bir benzetimde en fazla vektör sayısı.

   - **Cihaz haritasında göster**: saldırı vektörünü cihaz haritasında bir filtre olarak göster.

   - **Tüm kaynak cihazlar**: saldırı vektörü tüm cihazları bir saldırı kaynağı olarak kabul edecektir.

   - **Saldırı kaynağı**: saldırı vektörü yalnızca belirtilen cihazları bir saldırı kaynağı olarak kabul eder.

   - **Tüm hedef cihazlar**: saldırı vektörü tüm cihazları bir saldırı hedefi olarak kabul edecektir.

   - **Saldırı hedefi**: saldırı vektörü yalnızca belirtilen cihazları bir saldırı hedefi olarak kabul eder.

   - **Cihazları hariç tut**: belirtilen cihazlar saldırı vektörü benzetimine dahil edilecek.

   - **Alt ağları hariç tut**: belirtilen alt ağlar saldırı vektörü benzetimine dahil edilmeyecek.

3. **Benzetim Ekle**' yi seçin. Simülasyonu benzetim listesine eklenir.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Yeni bir benzetim ekleyin.":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::Benzetimi düzenlemek istiyorsanız seçin.

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::Benzetimi silmek istiyorsanız seçin.

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::Simülasyonu bir sık kullanılan olarak işaretlemek istiyorsanız seçin.

5. Saldırı vektörlerine ait bir liste görünür ve vektör puanı (100 dışında), saldırı kaynak cihazı ve saldırı hedef cihazı içerir. Saldırı vektörlerine yönelik grafik gösterimi için belirli bir saldırı seçin.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Saldırı vektörleri.":::

## <a name="see-also"></a>Ayrıca bkz.

[Saldırı vektörü raporlaması](how-to-create-attack-vector-reports.md)


