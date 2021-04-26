---
title: Azure portal bir pano oluşturun
description: Bu makalede, Azure portal bir panonun nasıl oluşturulacağı ve özelleştirileceği açıklanır.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726104"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Azure portal bir pano oluşturun

Panolar Azure portal bulut kaynaklarınızın odaklanmış ve düzenlenmiş görünümüdür. Panoları, kaynakları izleyebileceğiniz ve günlük işlemlere yönelik görevleri hızlı bir şekilde başlatabileceği bir çalışma alanı olarak kullanın. Örneğin, projeler, görevler veya Kullanıcı rollerine dayalı özel panolar oluşturun.

Azure portal, başlangıç noktası olarak varsayılan bir pano sağlar. Varsayılan panoyu düzenleyebilir, ek panolar oluşturabilir ve özelleştirebilirsiniz.

> [!NOTE]
> Her Kullanıcı, en çok 100 özel Pano oluşturabilir. [Panoyu yayımlayıp paylaşırsanız](azure-portal-dashboard-share-access.md), aboneliğiniz abonelikte bir Azure kaynağı olarak uygulanır ve bu sınıra dahil sayılmaz.

Bu makalede, yeni bir panonun nasıl oluşturulacağı ve özelleştirileceği açıklanır. Panoları paylaşma hakkında daha fazla bilgi için bkz. Azure ['un rol tabanlı erişim denetimi kullanarak Azure panoları paylaşma](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Yeni pano oluşturma

Bu örnek, atanan bir ada sahip yeni bir özel panonun nasıl oluşturulacağını gösterir. Tüm panolar oluşturulduğunda özeldir, ancak isterseniz panonuzu kuruluşunuzdaki diğer kullanıcılarla yayımlamayı ve paylaşmayı seçebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal menüsünde, **Pano**' yı seçin. Varsayılan görünüm zaten pano olarak ayarlanmış olabilir.

    ![Pano seçiliyken Azure portal ekran görüntüsü.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. **Yeni Pano** ' yı ve **boş Pano**' yı seçin.

    ![Yeni Pano seçeneklerinin ekran görüntüsü.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Bu eylem, kutucukları seçebileceğiniz **kutucuk galerisini** ve kutucukları düzenlediğiniz boş bir Kılavuzu açar.

1. Pano etiketindeki **My Dashboard** metnini seçin ve özel panoyu kolayca belirlemenize yardımcı olacak bir ad girin.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Kutucuk galerisiyle boş bir kılavuzun ekran görüntüsü.":::

1. Panoyu olduğu gibi kaydetmek için sayfa üstbilgisindeki **özelleştirmeyi bitti** ' yi seçin. Ya da, kutucuk eklemek ve panonuzu kaydetmek için sonraki bölüme geçin.

Pano görünümü artık yeni panonuzu gösterir. Kullanılabilir panoları görmek için Pano adının yanındaki oku seçin. Listede, diğer kullanıcıların oluşturduğu ve paylaştığı panolar bulunabilir.

## <a name="edit-a-dashboard"></a>Bir panoyu düzenleme

Şimdi, Azure kaynaklarınızı temsil eden kutucukları eklemek, yeniden boyutlandırmak ve düzenlemek için panoyu düzenleyelim.

### <a name="add-tiles-from-the-tile-gallery"></a>Kutucuk galerisinden kutucuk ekleme

Bir panoya kutucuk eklemek için şu adımları izleyin:

1. ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) Panonun sayfa başlığından Düzenle simgesini **Düzenle** ' yi seçin.

    ![Düzenleme seçeneğinin vurgulandığı panonun ekran görüntüsü.](./media/azure-portal-dashboards/dashboard-edit.png)

1. **Kutucuk galerisine** gidin veya belirli bir kutucuğu bulmak için arama alanını kullanın. Panonuza eklemek istediğiniz kutucuğu seçin.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Kutucuk galerisinin ekran görüntüsü.":::

1. Kutucuğu varsayılan bir boyut ve konum ile panoya eklemek için **Ekle** ' yi seçin. Ya da kutucuğu kılavuza sürükleyin ve istediğiniz yere yerleştirin. İstediğiniz kutucukları ekleyin, ancak birkaç fikir aşağıda verilmiştir:

    - Önceden oluşturduğunuz kaynakları görmek için **tüm kaynakları** ekleyin.

    - Birden fazla kuruluşla çalışıyorsanız, kaynakların hangi kuruluşa ait olduğunu açıkça göstermek için panonuza **kuruluş kimliği** kutucuğunu ekleyin.

1. İsterseniz, kutucuğun sağ alt köşesini sürükleyip bırakarak kutucuğu yeniden boyutlandırın.

1. Değişikliklerinizi kaydetmek için sayfa üst bilgisinde **Kaydet** ' i seçin. Ayrıca, sayfa üstbilgisinde **Önizleme** ' i seçerek değişiklikleri kaydetmeden da önizleyebilirsiniz. Önizleme ekranından, değişiklikleri korumak için **Kaydet** ' i seçebilir, Kaldır ' ı **atabilir** veya düzenleme seçeneklerine geri dönüp başka değişiklikler yapmanız için **düzenleme** yapabilirsiniz.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Önizleme, kaydetme ve atma seçeneklerinin ekran görüntüsü.":::

### <a name="pin-content-from-a-resource-page"></a>Kaynak sayfasından içerik sabitleme

Panolarınıza kutucuk eklemenin başka bir yolu doğrudan bir kaynak sayfasından yapılır.

Birçok kaynak sayfası, komut çubuğunda bir pin simgesi içerir. Bu simgeyi seçerseniz, kaynak sayfayı temsil eden bir kutucuğu var olan bir panoya veya oluşturduğunuz yeni bir panoya sabitleyebilir.

![PIN simgesiyle sayfa komut çubuğunun ekran görüntüsü](./media/azure-portal-dashboards/dashboard-pin-blade.png)

Bazı durumlarda bir pin simgesi sayfanın içindeki belirli içerik tarafından da görünebilir, bu da sayfanın tamamı yerine söz konusu içerik için bir kutucuk sabitleyebilir.

### <a name="resize-or-rearrange-tiles"></a>Kutucukları yeniden boyutlandır veya yeniden Düzenle

Bir kutucuğun boyutunu değiştirmek veya bir panodaki kutucukları yeniden düzenlemek için aşağıdaki adımları izleyin:

1. ![Sayfa başlığından Düzenle simgesini ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Düzenle** ' yi seçin.

1. Bir kutucuğun sağ üst köşesindeki bağlam menüsünü seçin. Sonra bir kutucuk boyutu seçin. Herhangi bir boyutu destekleyen Kutucuklar, Kutucuğu istediğiniz boyuta sürüklemenize imkan tanıyan sağ alt köşedeki bir "tanıtıcı" da içerir.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Kutucuk boyutu menüsü açık olan panonun ekran görüntüsü.":::

1. Bir kutucuk seçin ve panonuzu düzenlemek için Kılavuzu kılavuz üzerinde yeni bir konuma sürükleyin.

### <a name="additional-tile-configuration"></a>Ek kutucuk yapılandırması

Bazı Kutucuklar, istediğiniz bilgileri göstermek için daha fazla yapılandırma gerektirebilir. Örneğin, **ölçüm grafiği** kutucuğunun Azure izleyici 'den bir ölçüm görüntülemesi için ayarlanması gerekir. Ayrıca, kutucuk verilerini panonun varsayılan saat ayarlarını geçersiz kılmak için özelleştirebilirsiniz.

Ayarlanması gereken her kutucuk, kutucuğu özelleştirene kadar bir başlık görüntüler. **Ölçüm grafiğinde** başlık, **Ölçümler ' de düzenlenir**. Kutucuğu özelleştirmek için:

1. Sayfa üstbilgisinde, düzenleme modundan çıkmak için **Kaydet** ' i seçin.

1. Başlığı seçin, sonra gerekli Kurulumu yapın.

    ![Yapılandırma gerektiren kutucuğun ekran görüntüsü](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markaşağı kutucuk, panonuzda özel ve statik içerik görüntülemenize imkan tanır. Bu temel yönergeler, bir görüntü, bir köprü kümesi veya hatta iletişim bilgileri olabilir. Markaşağı kutucuğun kullanımı hakkında daha fazla bilgi için bkz. [Azure panoları üzerinde markaşağı kutucuk kullanarak özel içerik gösterme](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Kutucuk verilerini özelleştirme

Panodaki veriler, son 24 saat için etkinliği otomatik olarak gösterir. Yalnızca bu kutucuk için farklı bir zaman aralığı göstermek için şu adımları izleyin:

1. Bağlam  menüsünden veya ![ ](./media/azure-portal-dashboards/dashboard-filter.png) kutucuğun sol üst köşesindeki filtre simgesi filtresinden kutucuk verilerini Özelleştir ' i seçin.

    ![Kutucuk bağlam menüsünün ekran görüntüsü.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. **Kutucuk düzeyinde Pano zaman ayarlarını geçersiz kılmak** için onay kutusunu seçin.

    ![Kutucuk zaman ayarlarını yapılandırmak için iletişim kutusunun ekran görüntüsü.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Bu kutucuk için gösterilecek zaman aralığını seçin. Son 30 dakika ile son 30 gün arasında seçim yapabilir veya özel bir Aralık tanımlayabilirsiniz.

1. Görüntülenecek zaman parçalı yapısını seçin. Tek dakikalık artışlarla bir ay boyunca herhangi bir yere gösterebilirsiniz.

1. **Uygula**’yı seçin.

## <a name="delete-a-tile"></a>Kutucukları silme

Bir panodaki kutucuğu kaldırmak için aşağıdakilerden birini yapın:

- Kutucuğun sağ üst köşesindeki bağlam menüsünü seçin ve **panodan kaldır**' ı seçin.

- ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png)  Özelleştirme modunu girmek için Düzenle simgesini Düzenle ' yi seçin. Kutucuğun sağ üst köşesine gelin, sonra ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png) kutucuğu panodan kaldırmak için simgeyi Sil simgesini seçin.

   ![Panodan kutucuğun nasıl kaldırılacağını gösteren ekran görüntüsü.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Panoyu kopyalama

Mevcut bir panoyu yeni bir Pano için şablon olarak kullanmak için şu adımları izleyin:

1. Pano görünümünün kopyalamak istediğiniz panoyu göstermesini sağlayın.

1. Sayfa üstbilgisinde ![ Kopyala simgesini Kopyala ' yı seçin ](./media/azure-portal-dashboards/dashboard-clone.png) .

1. Panonun **bir kopyası,** *Pano adınızın* kopyası adlı düzenleme modunda açılır. Panoyu yeniden adlandırmak ve özelleştirmek için bu makaledeki yukarıdaki adımları kullanın.

## <a name="publish-and-share-a-dashboard"></a>Bir panoyu yayımlama ve paylaşma

Bir pano oluşturduğunuzda, bu, varsayılan olarak özeldir. Bu, onu görebilen tek bir tane olduğu anlamına gelir. Panoları başkaları için kullanılabilir hale getirmek için, bunları yayımlayabilir ve paylaşabilirsiniz. Daha fazla bilgi için bkz. [Azure 'un rol tabanlı erişim denetimi kullanarak Azure panoları paylaşma](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Paylaşılan bir panoyu açma

Paylaşılan bir panoyu bulup açmak için şu adımları izleyin:

1. Pano adının yanındaki oku seçin.

1. Görüntülenmiş Pano listesinden seçim yapın. Açmak istediğiniz Pano listede yoksa:

    1. **Tüm panolara gözatamıyorum**' ı seçin.

        ![Pano seçim menüsünün ekran görüntüsü](./media/azure-portal-dashboards/dashboard-browse.png)

    1. **Tür** alanında, **paylaşılan panolar**' ı seçin.

        ![Tüm panolar seçim menüsünün ekran görüntüsü](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Bir veya daha fazla abonelik seçin. Panoları ada göre filtrelemek için de metin girebilirsiniz.

    1. Paylaşılan panolar listesinden bir pano seçin.

## <a name="delete-a-dashboard"></a>Panoyu silme

Özel veya paylaşılan bir panoyu kalıcı olarak silmek için şu adımları izleyin:

1. Pano adının yanındaki listeden silmek istediğiniz panoyu seçin.

1. Sayfa üstbilgisindeki Sil simgesini Sil ' i seçin ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png)  .

1. Özel bir Pano için onay iletişim kutusunda **Tamam** ' ı seçerek panoyu kaldırın. Paylaşılan bir Pano için, onay iletişim kutusunda, yayımlanan panonun artık başkaları tarafından görüntülenemeyeceği onaylanacak onay kutusunu seçin. Ardından **Tamam**'ı seçin.

    ![Silme onayı ekran görüntüsü.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Silinen bir panoyu kurtarma

Küresel Azure bulutunuz varsa ve Azure portal _yayımlanmış_ bir panoyu silerseniz, bu panoyu silmenin 14 gün içinde kurtarabilirsiniz. Daha fazla bilgi için, [Azure Portal silinen bir panoyu kurtarma](recover-shared-deleted-dashboard.md)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure rol tabanlı erişim denetimi kullanarak Azure panoları paylaşma](azure-portal-dashboard-share-access.md)
- [Program aracılığıyla Azure panoları oluşturma](azure-portal-dashboards-create-programmatically.md)
