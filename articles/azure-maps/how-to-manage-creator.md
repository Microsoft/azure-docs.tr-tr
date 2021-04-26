---
title: Microsoft Azure haritaları oluşturucuyu yönetme (Önizleme)
description: Bu makalede Microsoft Azure haritaları oluşturucunun (Önizleme) nasıl yönetileceğini öğreneceksiniz.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559113"
---
# <a name="manage-azure-maps-creator-preview"></a>Azure haritalar oluşturucuyu yönetme (Önizleme) 

> [!IMPORTANT]
> Azure haritalar Creator Hizmetleri şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure haritalar Creator, özel ınkapılı harita verileri oluşturmanızı sağlar. Azure Haritalar API 'sini ve ınkapıharitaları modülünü kullanarak etkileşimli ve dinamik bir ınkapılı harita Web uygulamaları geliştirebilirsiniz. Şu anda, Oluşturucu yalnızca S1 fiyatlandırma katmanını kullanarak Birleşik Devletler kullanılabilir.

Bu makale, bir Azure haritalar hesabındaki bir Oluşturucu kaynağı oluşturma ve silme adımlarında size kılavuzluk ediyor.

## <a name="create-creator-preview-resource"></a>Oluşturucu (Önizleme) kaynağı oluşturma

1. [Azure portalda](https://portal.azure.com) oturum açma

2. Azure haritalar hesabınızı seçin. Azure haritalar hesabınızı **son kaynaklar** altında göremiyorsanız Azure Portal menüsüne gidin. **Tüm kaynaklar**’ı seçin. Azure haritalar hesabınızı bulun ve seçin.

    ![Azure haritalar Portalı giriş sayfası](./media/how-to-manage-creator/select-maps-account.png)

3. Azure haritalar hesabı sayfasından sonra **Oluşturucu** altındaki **genel bakış** seçeneğine gidin. Azure haritalar Oluşturucu kaynağı oluşturmak için  **Oluştur**  ' u seçin.

    ![Azure haritalar Oluşturucu oluşturma sayfası](./media/how-to-manage-creator/creator-blade-settings.png)

4. Oluşturucu kaynağınız için ad ve konum girin. Şu anda, Oluşturucu yalnızca Birleşik Devletler desteklenir. **Gözden geçir ve oluştur**’u seçin.

   ![Oluşturucu hesap bilgilerini girin sayfası](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Ayarlarınızı gözden geçirin ve **Oluştur**' u seçin.

    ![Oluşturucu hesabı ayarları sayfasını Onayla](./media/how-to-manage-creator/creator-create-dialog.png)

6. Dağıtım tamamlandığında, başarı veya hata iletisi içeren bir sayfa görürsünüz.

   ![Kaynak dağıtım durumu sayfası](./media/how-to-manage-creator/creator-resource-created.png)

7. **Kaynağa git**’i seçin. Oluşturucu kaynak görünümü sayfanız, Oluşturucu kaynağınızın ve seçilen demografik bölgesinin durumunu gösterir.

    ![Oluşturan durum sayfası](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Oluşturan kaynak sayfasından, Azure Maps hesabı ' nı seçerek ait olduğu Azure Maps hesabına geri gidebilirsiniz.

## <a name="delete-creator-preview-resource"></a>Oluşturucu (Önizleme) kaynağını Sil

Oluşturan kaynağını silmek için Azure haritalar hesabınıza gidin. **Oluşturucu** altında **Genel Bakış ' ı** seçin. **Sil** düğmesini seçin.

>[!WARNING]
>Azure haritalar hesabınızın Oluşturucu kaynağını sildiğinizde, Oluşturucu Hizmetleri kullanılarak oluşturulan veri kümelerini, tilekümelerini ve özellik statesets 'leri de silersiniz.

![Sil düğmesi olan Oluşturucu sayfası](./media/how-to-manage-creator/creator-delete.png)

**Sil** düğmesini seçin ve silme işlemini onaylamak için Oluşturucu adınızı yazın. Kaynak silindikten sonra aşağıdaki görüntüde olduğu gibi bir onay sayfası görürsünüz:

![Silme onayı içeren Oluşturucu sayfası](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Kimlik Doğrulaması

Oluşturucu (Önizleme), Azure Maps Access Control (ıAM) ayarlarını devralır. Veri erişimi için tüm API çağrılarının kimlik doğrulama ve yetkilendirme kurallarıyla gönderilmesi gerekir.

Oluşturucu kullanım verileri, Azure Haritalar kullanım grafiklerinize ve etkinlik günlüğüne eklenir.  Daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Oluşturucu hizmetlerine erişim

Oluşturan Hizmetleri (Önizleme) ve Oluşturucu 'da barındırılan verileri (örneğin, Işleme hizmeti) kullanan hizmetlere coğrafi bir URL üzerinden erişilebilir. Coğrafi URL, oluşturma sırasında seçilen konuma göre belirlenir. Örneğin, Oluşturucu Birleşik Devletler coğrafi konumunda oluşturulduysa, dönüştürme hizmetine yapılan tüm çağrılar ' a gönderilmelidir `us.atlas.microsoft.com/conversion/convert` .

Ayrıca, Oluşturucu ile içeri aktarılan tüm veriler, oluşturan kaynakla aynı coğrafi konuma yüklenmelidir. Örneğin, Oluşturucu Birleşik olarak belirtilmişse, tüm ham verilerin aracılığıyla yüklenmesi gerekir `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Sonraki adımlar

Inkapıeşleme için Oluşturucu Hizmetleri 'ne (Önizleme) giriş:

> [!div class="nextstepaction"]
> [Karşıya veri yükleme](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Veri dönüştürme](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Veri kümesi](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Özellik durumu kümesi](creator-indoor-maps.md#feature-statesets)

Uygulamanızdaki ınkapılı haritaları işlemek için Oluşturucu Hizmetleri 'ni (Önizleme) nasıl kullanacağınızı öğrenin:

> [!div class="nextstepaction"]
> [Azure haritalar Oluşturucu öğreticisi](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Inkapılı harita dinamik stili](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Inkapıharitaları modülünü kullanma](how-to-use-indoor-module.md)