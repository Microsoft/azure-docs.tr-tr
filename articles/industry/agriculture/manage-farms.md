---
title: Grupları Yönet
description: Grupların nasıl yönetileceğini açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 929672dceabc7d9b591f05ef3f4f716f2117e09c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128682"
---
# <a name="manage-farms"></a>Grupları yönetme

Grupları Azure Farmtempts 'de yönetebilirsiniz. Bu makalede, grupları nasıl oluşturacağınız, grupların nasıl yükleneceğine ilişkin bilgiler, bir veya daha fazla cihaz, sensörler ve gruplar hakkında bilgi sağlanır.

## <a name="create-farms"></a>Gruplar oluşturma

Aşağıdaki adımları kullanın:

1. Grup hızlandırıcısında oturum açın, **gruplar** sayfası görüntülenir.
    **Gruplar** sayfasında, zaten abonelikte oluşturulmuş olmaları durumunda grupların listesi görüntülenir.

    Örnek görüntü aşağıda verilmiştir:

    ![Proje grubu ları](./media/create-farms/create-farm-main-page-1.png)


2. **Grup Oluştur** ' u seçin **ve ad**, **kırpı** ve **Adres**sağlayın.
3. **Grup sınırını tanımla**, (zorunlu alan) **haritada Işaretle** veya **geojson kodunu Yapıştır**' ı seçin.

Aşağıda, bir grup sınırı tanımlamanın iki yolu verilmiştir:

1. **Haritada işaretle**: harita denetim aracını kullanarak grubun sınırını çizin ve işaretleyin. Sınırları işaretlemek için, proje grubu ![](./media/create-farms/pencil-icon-1.png) ve tam sınırları işaretleyin.

    ![Proje grubu ları](./media/create-farms/create-farm-mark-on-map-1.png)

2. **Geojson kodunu Yapıştır**: geojson, JAVASCRIPT nesne GÖSTERIMI (JSON) kullanarak coğrafi veri yapılarını kodlamaya yönelik bir biçimdir. Bu seçenek, Grup sınırlarını işaretlemek için GeoJSON dizesinin girilebileceği bir metin kutusu görüntüler. Ayrıca, GeoJSON.io adresinden coğrafi JSON kodu da oluşturabilirsiniz.
Bilgileri doldurmaya yardımcı olması için araç ipuçlarını kullanın.

    ![Proje grubu ları](./media/create-farms/create-new-farm-1.png)

4.  Bir grup oluşturmak için **Gönder** ' i seçin. **Gruplar** sayfasında yeni bir grup oluşturulur ve görüntülenir.

## <a name="view-farm"></a>Grubu görüntüle

Grup listesi sayfası oluşturulan grupların bir listesini görüntüler. Listesini görüntülemek için bir grup seçin:

 - **Cihaz sayısı** — grup içinde dağıtılan cihazların sayısını ve durumunu görüntüler.
 - **Map** — grubun, grupta dağıtılan cihazlarla eşleyin.
 - **Telemetri** : grupta dağıtılan sensörlerden telemetri görüntüler.
 - **En son duyarlık haritaları** — en son uydu dizinleri HARITASıNı (EVI, ndwi), SOIL Moisture heatmap ve algılayıcı yerleştirme haritasını görüntüler.

## <a name="edit-farm"></a>Grubu Düzenle

**Gruplar** sayfası oluşturulan grupların bir listesini görüntüler.

1.  Grubu görüntülemek ve düzenlemek için bir grup seçin.
2.  Grup bilgilerini düzenlemek için **grubu Düzenle** ' yi seçin. **Grup ayrıntıları** penceresinde **adı**düzenleyebilir, **kırpıyor**, **adreslenecek**ve **Grup sınır** alanlarını tanımlayabilir.

    ![Proje grubu ları](./media/create-farms/edit-farm-1.png)

3. Düzenlenen ayrıntıları kaydetmek için **Gönder** ' i seçin.

## <a name="delete-farm"></a>Grubu Sil

**Gruplar** sayfası oluşturulan grupların listesini görüntüler. Bir grubu silmek için aşağıdaki adımları kullanın:

1.  Grup ayrıntılarını silmek için listeden bir grup seçin.
2.  Grubu silmek için **Grubu Sil** ' i seçin.

    ![Proje grubu ları](./media/create-farms/delete-farm-1.png)

    > [!NOTE]
    > Bir grubu sildiğinizde, grupla ilişkili cihazlar ve haritalar silinmez. Cihazla ve eşlemlerle ilişkili tüm grup bilgileri ilgili olmaz. Cihaz, telemetri ve haritaları Farmtts hizmetinden görüntülemeye devam edebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Grubunuzu oluşturduğunıza göre, [sensöre veri](get-sensor-data-from-sensor-partner.md) akışını grubunuza nasıl alabileceğinizi öğrenin.
