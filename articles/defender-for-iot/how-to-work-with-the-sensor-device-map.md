---
title: Algılayıcı cihaz eşlemesiyle çalışma
description: Cihaz eşlemesi, algılanan ağ cihazlarının grafik gösterimini sağlar. Cihaz bilgilerini ve ağ dilimlerini çözümlemek ve yönetmek için haritayı kullanın ve rapor oluşturun.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784603"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Cihaz eşlemesindeki algılayıcı algılamalarını araştırın

Cihaz eşlemesi, algılanan ağ cihazlarının grafik gösterimini sağlar. Haritayı kullanarak şunları yapın:

  - Cihaz bilgilerini alın, çözümleyin ve yönetin.

  - Ağ dilimlerini analiz edin, örneğin belirli ilgi alanları veya vade dışı katmanlar.

  - Rapor oluşturma, örneğin cihaz ayrıntılarını ve özetlerini dışarı aktarma.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Cihaz eşlemesinin ekran görüntüsü.":::

Haritaya erişmek için:

  - Konsol ana ekranından **cihaz Haritası** ' nı seçin.

## <a name="map-search-and-layout-tools"></a>Harita arama ve Düzen araçları

Haritada çalışmak için aşağıdaki araçlar kullanılır.

Kullanıcı rolünüz, cihaz Haritası penceresinde hangi araçların kullanılabildiğini belirler. Kullanıcı rolleri hakkındaki ayrıntılar için bkz. Kullanıcı [oluşturma ve yönetme](how-to-create-and-manage-users.md) .

| Sembol | Description |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Belirli bir cihaz için IP adresi veya MAC adresiyle arama yapın. Metin kutusuna IP adresini veya MAC adresini girin. Harita, aradığınız cihazı, kendisine bağlı cihazlarla görüntüler. |
| Grup vurgulaması ve filtreleri <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Grup vurguları ve filtreleri ekran görüntüsü."::: | Haritayı varsayılan ve özel cihaz gruplarına göre filtreleyin veya vurgulayın. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Bir cihaz üzerinde odaklanmış bir görünümü etkinleştirmek ve BT cihazlarını gruplamak için görünümü daraltın.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Geçerli cihaz düzenlemesini haritada saklayın. Örneğin, cihazları haritadaki yeni konumlara sürüklerseniz, haritada çıkarken cihazlar bu konumlarda kalır. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Ekrana sığdır |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -Bu cihaz için tanımlanan, otomatik, işlem denetimi, süper yönetici ve kurumsal dahil olmak üzere, belirtilen adım katmanını görüntüleyin <br /> -Cihazlar arasındaki bağlantıları görüntüleyin.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Yayın ve çok noktaya yayın arasında göster veya gizle. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Haritadaki cihazları diğer cihazlarla en son iletişim kurdukları zamana göre filtreleyin. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="bildirimi" border="false"::: | Bir cihaz hakkındaki bildirimleri görüntüleyin. Örneğin, var olan bir MAC adresini kullanan bir cihaz için yeni bir IP algılanırsa |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Dışarı Aktarma" border="false"::: | Cihaz bilgilerini Içeri/dışarı aktarma. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="özelliklerinin" border="false"::: | Seçili cihazlar için temel cihaz özelliklerini görüntüleyin. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Yakınlaştır" border="false"::: veya :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="uzaklaştır" border="false"::: | Haritadaki cihazları yakınlaştırın veya uzaklaştırın. |

## <a name="view-ot-elements-only"></a>Yalnızca OT öğelerini görüntüle

Varsayılan olarak, It cihazları alt ağ tarafından otomatik olarak toplanır, böylece harita görünümü OT ve ICS ağlarına odaklanır. BT ağı öğelerinin sunumu en az bir olarak daraltılır ve bu, haritada sunulan cihazların toplam sayısını azaltır ve OT ve ICS ağ öğelerinin net bir resmini sağlar.

Her alt ağ, bir BT alt ağının ayrıntılarına bakmak için etkileşimli daraltma ve genişletme özelliği de dahil olmak üzere cihaz eşlemesinde tek bir varlık olarak sunulur.

Aşağıdaki şekilde, 27 BT ağ öğeleriyle daraltılmış bir BT alt ağı gösterilmektedir.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="27 BT ağ öğeleriyle daraltılmış BT alt ağı":::

BT ağlarını daraltma özelliğini etkinleştirmek için:

- **Sistem ayarları** PENCERESINDE, BT ağlarını gruplama özelliğinin etkinleştirildiğinden emin olun.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Sistem ayar penceresi":::

Bir BT alt ağını genişletmek için:

1. BT ve OT ağlarını birbirinden ayırt etmek için, sistem ayarları ekranından **alt ağlar**' ı seçin.

   > [!NOTE]
   > Her alt ağın anlamlı adlara sahip olması önerilir ve bu ağlar arasında ayrım yapmak için Kullanıcı kolayca tanımlayabilir.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Alt ağlar yapılandırması":::

2. Alt ağ **yapılandırmasını düzenle** penceresinde, bir BT alt ağı olarak tanımlamak istediğiniz her alt ağ Için **ICS alt ağ** onay kutusunu temizleyin. BT alt ağları, BT ağlarındaki Controller veya PLC gibi ICS cihazlarıyla birlikte cihaz eşlemesinde daraltılmış olarak görünür.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Alt ağ yapılandırmasını düzenle":::

3. Haritadaki BT ağını genişletmek için, cihazlar penceresinde, sağ tıklayın ve **ağ Genişlet**' i seçin.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Ağınızın görünümünü genişletin.":::

4. Düzen değişikliğinin redone olduğunu bildiren bir onay kutusu görünür.

5. **Tamam**’ı seçin. It alt ağı öğeleri haritada görüntülenir.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="Tamam":::

Bir BT alt ağını daraltmak için:

1.  Sol bölmeden **cihazlar**' ı seçin.

2. Cihazlar penceresinde daraltma simgesini seçin. Kırmızı renkte sayı, şu anda haritada kaç tane genişletilmiş BT alt ağı göründüğünü gösterir.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Cihaz penceresi":::

3. Daraltmak istediğiniz alt ağı seçin veya **Tümünü Daralt**' ı seçin. Seçili alt ağ haritada daraltılmış olarak görünür.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Tümünü Daralt":::

Daraltma simgesi, güncelleştirilmiş genişletilmiş BT alt ağlarının sayısıyla güncelleştirilir.

## <a name="view-or-highlight-device-groups"></a>Cihaz gruplarını görüntüleme veya vurgulama

Harita görüntüsünü cihaz gruplarına göre özelleştirebilirsiniz. Örneğin, belirli bir OT protokolü, VLAN veya alt ağ ile ilişkili cihaz grupları. Önceden tanımlanmış gruplar kullanılabilir ve özel gruplar oluşturulabilir.

Grupları şu şekilde görüntüle:

  - **Vurgulama:** Belirli bir gruba ait olan cihazları mavi olarak vurgulayın.

  - **Filtreleme:** Yalnızca belirli bir gruba ait olan cihazları görüntüler.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Bağlantı noktanızın standart görünümü":::

Aşağıdaki önceden tanımlanmış gruplar kullanılabilir:

| Grup adı | Description |
|--|--|
| **Bilinen uygulamalar** | TCP gibi ayrılmış bağlantı noktaları kullanan cihazlar.  |
| **Standart olmayan bağlantı noktaları (varsayılan)** | Standart olmayan bağlantı noktaları veya bir diğer ad atanmamış bağlantı noktaları kullanan cihazlar. |
| **OT protokolleri (varsayılan)** | Bilinen OT trafiğini işleyen cihazlar. |
| **Yetkilendirme (varsayılan)** | Öğrenme işlemi sırasında ağda bulunan veya ağda bir şekilde yetkilendirilmiş olan cihazlar. |
| **Cihaz envanteri filtreleri** | Cihazlar, filtre ölçütüne göre gruplandırılan cihaz envanter tablosuna kaydedilir. |
| **Yoklama aralıkları** | Yoklama aralıklarına göre gruplandırılan cihazlar. Yoklama aralıkları, döngüsel kanallara veya dönemlere göre otomatik olarak oluşturulur. Örneğin, 15,0 saniye, 3,0 saniye, 1,5 saniye veya herhangi bir Aralık. Bu bilgilerin incelenmesi, sistemlerin çok hızlı veya yavaş bir şekilde yoklanmasını öğrenmenize yardımcı olur. |
| **Program** | Mühendislik istasyonları ve programlama makineleri. |
| **Alt ağlar** | Belirli bir alt ağa ait olan cihazlar. |
| **VLAN** | Belirli bir VLAN KIMLIĞIYLE ilişkili cihazlar. |
| **Çapraz alt ağ bağlantıları** | Bir alt ağdan başka bir alt ağa iletişim kuran cihazlar. |
| **Sabitlenmiş uyarılar** | Kullanıcının bir uyarıyı sabitlediği cihazlar. |
| **Saldırı vektörü benzetimleri** | Saldırı vektörü raporlarında güvenlik açığı bulunan cihazlar algılandı. Bu cihazları haritada görüntülemek için, saldırı vektörünü oluştururken **cihaz eşlemesinde görüntüle** onay kutusunu seçin. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Saldırı vektörü benzetimleri ekleyin":::. |
| **Son görülme** | Son görtikleri zaman dilimine göre gruplandırılan cihazlar, örneğin: bir saat, altı saat, bir gün, yedi gün. |
| **Active Directory değil** | Active Directory ile iletişim kurmayan tüm PLC olmayan cihazlar. |

Cihazları vurgulamak veya filtrelemek için:

1. Yan menüden **cihaz Haritası** ' nı seçin.

2. Filtre simgesini seçin. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menü":::

3. Gruplar bölmesinden, cihazları vurgulamak veya filtrelemek istediğiniz grubu seçin.

4. **Vurgula** veya **filtre**' yi seçin. Vurgulamayı kaldırmak için aynı seçimi değiştirin veya filtre uygulayın.

## <a name="define-custom-groups"></a>Özel grupları tanımlama

Önceden tanımlanmış grupları görüntülemenin yanı sıra özel gruplar tanımlayabilirsiniz. Gruplar cihaz haritasında, cihaz envanterinde ve veri araştırma raporlarında görüntülenir.

> [!NOTE]
> Ayrıca cihaz envanterinden gruplar oluşturabilirsiniz.

Bir grup oluşturmak için:

1. Yan menüdeki **cihazlar** ' ı seçin. Cihaz haritası görüntülenir.

1. Gruplar ayarlarını göstermek için :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Grup ayarı"::: ' nı seçin.

1. Yeni bir özel grup oluşturmak için :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="gruplar"::: ' ı seçin.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Özel grup ekranı oluşturma":::

1. Grubun adını ekleyin, en fazla 30 karakter kullanın.

1. İlgili cihazları aşağıdaki gibi seçin:

   - Bu menüden cihazları listeden seçerek ekleyin (ok düğmesini seçin),<br /> Veya <br /> 
   - Bu menüden cihazları seçilen bir gruptan kopyalayarak ekleyin (ok düğmesini seçin)

1. Varolan grupları özel gruplara eklemek için **Grup Ekle** ' yi seçin.

### <a name="add-devices-to-a-custom-group"></a>Özel bir gruba cihaz ekleme

Özel bir gruba cihaz ekleyebilir veya yeni bir özel grup ve cihaz oluşturabilirsiniz.

1. Haritadaki bir cihaza sağ tıklayın.

1. **Gruba ekle**' yi seçin.

1. Grup alanına bir grup adı girin ve + seçeneğini belirleyin. Yeni Grup görüntülenir. Grup zaten varsa, var olan özel gruba eklenir.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Grup adı":::

1. 1-3 adımlarını yineleyerek bir gruba cihaz ekleyin.

## <a name="map-zoom-views"></a>Harita yakınlaştırma görünümleri

Harita görünümleriyle çalışma, büyük ağları çözümlemede, adli hızlandırılmasına yardımcı olur.

Üç cihaz ayrıntı görünümü gösterilebilir:

  - [Kuşbakışı görünümü](#birds-eye-view)

  - [Cihaz türü ve bağlantı görünümü](#device-type-and-connection-view)

  - [Ayrıntılı görünüm](#detailed-view)

### <a name="birds-eye-view"></a>Kuşbakışı görünümü

Bu görünüm aşağıdaki gibi gösterilen cihazların bir bakışta görünümünü sağlar:

  - Kırmızı noktalar, uyarı (ler) i olan cihazları gösterir

  - Starred noktaları, önemli olarak işaretlenmiş cihazları gösterir

  - Siyah noktalar, uyarı içermeyen cihazları gösterir

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Kedi bakışı görünümü":::

### <a name="device-type-and-connection-view"></a>Cihaz türü ve bağlantı görünümü 

Bu görünüm, cihazları uyarılar, cihaz türleri ve bağlı cihazlarla vurgulamak için haritada simgeler olarak temsil edilen cihazları gösterir.

  - Uyarıları olan cihazlar kırmızı bir halkayla görüntülenir

  - Uyarı olmayan cihazlar gri halka ile görüntülenir

  - Yıldız olarak görünen cihazlar önemli olarak işaretlendi

Cihaz türü simgesi bağlı cihazlarla gösterilir.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="bağlantı görünümü":::

### <a name="detailed-view"></a>Ayrıntılı görünüm 

Ayrıntılı görünüm, cihazları ve cihaz etiketlerini ve göstergelerini aşağıdaki bilgilerle sunar:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Ayrıntılı görünüm":::

### <a name="control-the-zoom-view"></a>Yakınlaştırma görünümünü denetleme

Görüntülenecek harita görünümü harita yakınlaştırma düzeyine bağlıdır. Harita görünümleri arasında geçiş yapmak, yakınlaştırma düzeyleri değiştirilerek yapılır.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Yakınlaştırma görünümünü denetleme":::

### <a name="enable-simplified-zoom-views"></a>Basitleştirilmiş yakınlaştırma görünümlerini etkinleştir

Güvenlik analistleri ve Kullanıcı kullanıcıları 'nın kuş bakışı ve cihaza erişmesini ve bağlantı görünümlerini yazalım yöneticileri, Basitleştirilmiş görünüm seçeneğini etkinleştirmelidir.

Basitleştirilmiş harita görünümlerini etkinleştirmek için:

  - **Sistem ayarları** ' nı seçin ve **Basitleştirilmiş harita görünümü** seçeneğini değiştirin.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Harita görünümünü basitleştirme":::

## <a name="learn-more-about-devices"></a>Cihazlar hakkında daha fazla bilgi edinin

Cihazlar hakkında daha fazla bilgi edinmek için cihaz haritasını oluşturan çok sayıda araç mevcuttur:

- [Cihaz Etiketleri ve göstergeleri](#device-labels-and-indicators)

- [Cihaz hızlı görünümleri](#device-quick-views)

- [Cihaz özelliklerini görüntüleme ve yönetme](#view-and-manage-device-properties)

- [Cihaz türlerini görüntüle](#view-device-types)

- [Devre kartı](#backplane-properties)

- [Cihaz için olay zaman çizelgesini görüntüleme](#view-a-timeline-of-events-for-the-device)

- [Programlama ayrıntılarını ve değişikliklerini çözümleyin](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Cihaz Etiketleri ve göstergeleri

Haritadaki cihazlarda aşağıdaki Etiketler ve göstergeler görünebilir:

| Cihaz etiketi | Description |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP ana bilgisayar adı"::: | IP adresi ana bilgisayar adı ve IP adresi veya alt ağ adresleri |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Uyarı sayısı"::: | Cihazla ilişkili uyarı sayısı |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Cihaz türü simgesi (örneğin, depolama, PLC veya histora). |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="gruplandırılmış cihazlar"::: | BT ağındaki bir alt ağda gruplandırılan cihaz sayısı. Bu örnekte 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="cihaz öğrenme dönemi"::: | Öğrenme süresinden sonra algılanan ve ağ aygıtı olarak yetkilendirilmeyen bir cihaz. |
| Düz çizgi | Cihazlar arasında mantıksal bağlantı |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Yeni cihaz"::: | Öğrendikten sonra yeni cihaz bulundu. |

### <a name="device-quick-views"></a>Cihaz hızlı görünümleri

Haritadan cihaz özelliklerine ve bağlantılarına erişin.

Hızlı Özellikler menüsünü açmak için:

  - Hızlı Özellikler menü :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="Hızlı Özellikler menüsünü":::seçin.

#### <a name="quick-device-properties"></a>Hızlı cihaz özellikleri

Bu cihazların vurgularını görmek için hızlı Özellikler ekranı açıkken bir cihaz veya birden çok cihaz seçin:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Hızlı cihaz özellikleri":::

#### <a name="quick-connection-properties"></a>Hızlı bağlantı özellikleri

Bu bağlantıda kullanılan ve en son görüldüğü protokolleri görmek için hızlı Özellikler ekranı açıkken bir bağlantı seçin:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Hızlı bağlantı özellikleri":::

## <a name="view-and-manage-device-properties"></a>Cihaz özelliklerini görüntüleme ve yönetme

Haritada görünen her bir cihaz için Device özellikleri 'ı görüntüleyebilirsiniz. Örneğin, cihaz adı, türü veya işletim sistemi veya üretici yazılımı ya da satıcı.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Cihaz özelliklerini görüntüleme ve yönetme":::

Aşağıdaki bilgiler el ile güncelleştirilebilen olabilir. El ile girilen bilgiler, IoT için Defender tarafından bulunan bilgileri geçersiz kılar.

  - Ad

  - Tür

  - İşletim Sistemi

  - Takip tarihi katmanı

  - Description

| Öğe | Açıklama |
|--|--|
| Temel Bilgiler | Gerekli temel bilgiler. |
| Name | Cihaz adı. <br /> Varsayılan olarak, algılayıcı, ağ içinde tanımlanan cihaz adını bulur. Örneğin, DNS sunucusunda tanımlı bir ad. <br /> Böyle bir ad tanımlanmamışsa, bu alanda cihaz IP adresi görüntülenir. <br /> Bir cihaz adını el ile değiştirebilirsiniz. Cihazlarınıza işlevlerini yansıtan anlamlı adlar verin. |
| Tür | Algılayıcı tarafından algılanan cihaz türü. <br /> Daha fazla bilgi için bkz. [cihaz türlerini görüntüleme](#view-device-types). |
| Satıcı | Cihaz satıcısı. Bu, cihaz MAC adresinin baştaki karakterleriyle belirlenir. Bu alan salt okunur durumdadır. |
| Operating System | Algılayıcı tarafından algılanan cihaz işletim sistemi. |
| Takip tarihi katmanı | Bu cihaz için algılayıcı tarafından tanımlanan, bu cihaz için gereken, aşağıdaki gibi bir katman: <br /> -Otomatik <br /> -İşlem denetimi <br /> -Supervizör <br /> - Enterprise |
| Description | Ücretsiz metin alanı. <br /> Cihaz hakkında daha fazla bilgi ekleyin. |
| Öznitelikler | Öğrenme döneminde cihaz hakkında keşfedilen ve diğer kategorilere ait olmayan ek bilgiler, Öznitelikler bölümünde görünür. <br /> Bilgi RO. |
| Ayarlar | Hatalı pozitif durumları engellemek için cihaz ayarlarını el ile değiştirebilirsiniz: <br /> - **Yetkili cihaz**: öğrenme döneminde, ağda bulunan tüm cihazlar yetkili cihaz olarak tanımlanır. Bir cihaz öğrenme süresinden sonra bulunduğunda, varsayılan olarak yetkisiz bir cihaz olarak görünür. Bu tanımı el ile değiştirebilirsiniz. <br /> - **Tarayıcı olarak bilinen**: Bu cihazın tarayıcı olarak bilinmediğini ve sorun hakkında sizi uyarmak gerekmediğini biliyorsanız bu seçeneği etkinleştirin. <br /> - **Programlama aygıtı**: Bu cihazın bir programlama aygıtı olarak bilinmediğini ve programlama değişiklikleri yapmak için kullanıldığını biliyorsanız bu seçeneği etkinleştirin. Bunu bir programlama cihazı olarak tanımlamak, bu varlıktan kaynaklanan değişikliklerin programlanması uyarılarını engeller. |
| Özel gruplar | Bu cihazın katıldığı cihaz eşlemesindeki özel gruplar. |
| Durum | Cihazın güvenliği ve yetkilendirme durumu: <br /> -Durum, uyarı olmadığında olur `Secured` <br /> -Cihaz hakkında uyarılar olduğunda, uyarı sayısı görüntülenir <br /> -Durum, `Unauthorized` öğrenme süresinden sonra ağa eklenen cihazlar için görüntülenir. Cihazı ayarlarda el ile tanımlayabilirsiniz `Authorized Device` <br /> -Bu cihazın adresinin dinamik bir adres olarak tanımlanması durumunda `DHCP` duruma eklenir. |


| Ağ | Description |
|--|--|
| Arabirimler | Cihaz arabirimleri. Bir RO alanı. |
| Protokoller | Cihaz tarafından kullanılan protokoller. Bir RO alanı. |
| Üretici yazılımı | Arka düzlem bilgileri varsa, bellenim bilgileri görüntülenmez. |
| Adres | Cihazın IP adresi. |
| Ardışık | Cihaz seri numarası. |
| Modül adresi | Cihaz modeli ve yuva numarası veya KIMLIĞI. |
| Modelleme | Cihaz modeli numarası. |
| Üretici Yazılımı Sürümü | Üretici yazılımı sürüm numarası. |

Cihaz bilgilerini görüntülemek için:

1. Yan menüdeki **cihazlar** ' ı seçin.

2. Bir cihaza sağ tıklayın ve **özellikleri görüntüle**' yi seçin. Cihaz Özellikler penceresi görüntülenir.

3. Bu cihazdaki uyarılarla ilgili ayrıntılı bilgileri görüntülemek için bu pencerenin altındaki gerekli uyarıyı seçin.

### <a name="view-device-types"></a>Cihaz türlerini görüntüle

Cihaz türü, cihaz bulma işlemi sırasında algılayıcı tarafından otomatik olarak tanımlanır. Türü el ile değiştirebilirsiniz.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Cihaz türlerini görüntüle":::

Aşağıdaki tablo, sistemdeki tüm türleri gösterir:

| Kategori | Cihaz Türü |
|--|--|
| ICS | Mühendislik Istasyonu <br /> PLC <br />Histora <br />HMı <br />DURUMUNU <br />DC denetleyici <br />RTU <br />Endüstriyel paketleme sistemi <br />Endüstriyel ölçek <br />Endüstriyel robot <br />Yuva <br />Ölçüm <br />Değişken sıklık sürücüsü  <br />Robot denetleyici <br />Servo Sürücüsü <br />Pnömatik cihazı <br />Kayan yazı |
| BT | Etki Alanı Denetleyicisi <br />DB sunucusu <br />İş İstasyonu <br />Sunucu <br />Terminal Istasyonu <br />Depolama <br />Akıllı telefon <br />Tablet <br />Yedekleme sunucusu |
| IoT | IP kamerası <br />Yazıcı  <br />Delme saati <br />ATM <br />Akıllı TV <br />Oyun konsolu <br />UN <br />Kapı Denetim Masası <br />HVAC <br />Saatiniz <br />Yangın alarmı <br />Akıllı ışık <br />Akıllı anahtar <br />Yangın algılayıcısı <br />IP telefonu <br />Alarm sistemi <br />Uyarı SIREN <br />Hareket Algılayıcısı <br />Geliştir <br />Nem algılayıcısı <br />Barkod Tarayıcı <br />Kesintisiz güç kaynağı <br />Kişiler sayaç sistemi <br />Intercom <br />Turn |
| Ağ | Kablosuz erişim noktası <br />Yönlendirici <br />Anahtar <br />Güvenlik Duvarı <br />VPN Gateway <br />NTP sunucusu <br />WiFi Pineapple <br />Fiziksel konum <br />G/ç bağdaştırıcısı <br /> Protokol Dönüştürücüsü |

Cihaz bilgilerini görüntülemek için:

1.  Yan menüdeki **cihazlar** ' ı seçin.

2. Bir cihaza sağ tıklayın ve **özellikleri görüntüle**' yi seçin. Cihaz Özellikler penceresi görüntülenir.

3. Bu cihazdaki uyarılarla ilgili ayrıntılı bilgileri görüntülemek için gerekli uyarıyı seçin.

### <a name="backplane-properties"></a>Arka düzlem özellikleri

Bir PLC, raflara ve yuvalara ayrılmış birden çok modül içeriyorsa, Özellikler modül kartları arasında farklılık gösterebilir. Örneğin, IP adresi ve MAC adresi aynıysa, bellenim farklı olabilir.

Birden çok denetleyicisi/kartı ve iç içe cihazlarını çeşitli tanımlarla tek bir varlık olarak gözden geçirmek için geri düzlemi seçeneğini kullanabilirsiniz. Arka plan görünümündeki her bir yuva temel alınan cihazları (arkasında bulunan cihazlar) temsil eder.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Arka düzlem özellikleri":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Arka düzlem cihaz özellikleri":::

Bir sırt, en fazla 30 denetleyici kartı ve en fazla 30 raf birimi içerebilir. Birden çok düzeyde bulunan cihazların toplam sayısı en fazla 200 cihaz olabilir.

Arka düzlem ayrıntıları algılandığında cihaz Özellikler penceresi geri dönüşlü bölmesi görüntülenir.

Her yuva, temeldeki cihazların sayısıyla ve modül türünü gösteren simgeyle birlikte görüntülenir.

| Simge | Modül Türü |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Güç kaynağı"::: | Güç kaynağı |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analog g/ç"::: | Analog g/ç |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="İletişim bağdaştırıcısı"::: | İletişim bağdaştırıcısı |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Dijital g/ç"::: | Dijital g/ç |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMı"::: | HMı |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Genel"::: | Genel |

Bir yuva seçtiğinizde, yuva ayrıntıları görüntülenir:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="yuva seçin":::

Yuvanın arkasındaki temeldeki cihazları görüntülemek için **haritada görüntüle**' yi seçin. Yuva, cihaz eşlemesinde, bağlı olan tüm modüller ve cihazlarla birlikte sunulur.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="HARITADA GÖRÜNTÜLE":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Cihaz için olay zaman çizelgesini görüntüleme

Bir cihazla ilişkili olayların zaman çizelgesini görüntüleyin.

Zaman çizelgesini görüntülemek için:

1. Haritadaki bir cihaza sağ tıklayın.

2. **Olayları göster**' i seçin. Olay zaman çizelgesi penceresi, seçilen cihaz için algılanan olaylar hakkında bilgi ile açılır.

Ayrıntılar için bkz. [olay zaman çizelgesi](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Programlama ayrıntılarını ve değişikliklerini çözümleyin

Ağ cihazlarınızda yürütülen programlama olaylarını görüntüleyerek ve kod değişikliklerini çözümleyerek, Forli ICS 'yi geliştirin. Bu bilgiler şüpheli programlama etkinliğini keşfetmenize yardımcı olur, örneğin:

  - İnsan hatası: bir mühendis yanlış cihazı programlarken.

  - Bozuk programlama Otomasyonu: Otomasyon hatası nedeniyle programlama yanlışlıkla yürütülüyor.

  - Korsan sistemler: bir programlama cihazında oturum açan yetkisiz kullanıcılar.

Programlanmış bir cihaz görüntüleyebilir ve diğer cihazlar tarafından gerçekleştirilen çeşitli programlama değişikliklerinden gezinebilirsiniz.

Programlama aygıtı tarafından eklenen, değiştirilen, kaldırılan veya yeniden yüklenen kodu görüntüleyin. Dosya türlerine, tarihlere veya ilgilendiğiniz saatlere göre değişiklikleri programlama araması yapın.

### <a name="when-to-review-programming-activity"></a>Programlama etkinliğinin ne zaman incelenmesi 

Programlama etkinliğini gözden geçirmeniz gerekebilir:

  - Yetkisiz programlama ile ilgili bir uyarı görüntüledikten sonra

  - Denetleyicilere planlı güncelleştirmeden sonra

  - Bir işlem veya makine düzgün çalışmadığında (son güncelleştirmeyi kimin ne zaman yapıldığını görmek için)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Değişiklik günlüğü 'Nü programlama":::

Diğer seçenekler şunları yapmanızı sağlar:

  - Bir yıldız ile ilgilendiğiniz olayları işaretleyin.

  - Geçerli kodla bir *. txt dosyası indirin.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Yetkili ve yetkisiz programlama olayları hakkında 

Yetkisiz programlama olayları, programlama cihazları olarak öğrenilmeyen veya el ile tanımlanmayan cihazlar tarafından yürütülür. Yetkili programlama olayları, programlama cihazları olarak çözümlenen veya el ile tanımlanan cihazlar tarafından yürütülür.

Programlama Analizi penceresinde hem yetkili hem de yetkisiz programlama olayları görüntülenir.

### <a name="accessing-programming-details-and-changes"></a>Programlama ayrıntılarına ve değişikliklere erişme

' Dan programlama analizi penceresine erişin.

- [Olay zaman çizelgesi](#event-timeline)

- [Yetkisiz programlama uyarıları](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Olay zaman çizelgesi

Programlama değişikliklerinin algılandığı olay zaman çizelgesini göstermek için olay zaman çizelgesini kullanın.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Olay zaman çizelgesinin görünümü.":::

### <a name="unauthorized-programming-alerts"></a>Yetkisiz programlama uyarıları

Yetkisiz programlama cihazları programlama etkinliklerini yaparken uyarılar tetiklenir.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Yetkisiz programlama uyarıları":::

> [!NOTE]
> Ayrıca, temel programlama bilgilerini cihaz Özellikler penceresi ve cihaz envanterinde görüntüleyebilirsiniz.

### <a name="working-in-the-programming-timeline-window"></a>Programlama zaman çizelgesi penceresinde çalışma

Bu bölümde, programlama dosyalarının nasıl görüntüleneceği ve sürümlerin nasıl karşılaştırılacağı açıklanmaktadır. Programlanmış bir cihaza gönderilen belirli dosyaları arayın. Dosya arama temeli:

  - Tarih

  - Dosya türü

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="programlama zaman çizelgesi penceresi":::

|Programlama zaman çizelgesi türü | Description |
|--|--|
| Programlanmış cihaz | Ana bilgisayar adı ve dosya dahil olmak üzere programlanmış cihaz hakkında ayrıntılar sağlar. |
| Son olaylar | Algılayıcı tarafından algılanan 50 en son olayları görüntüler. <br />Bir olayı vurgulamak için, üzerine gelin ve yıldıza tıklayın. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Son 50 olay görüntülenebilir. |
| Dosyalar | Programlanmış cihazdaki seçilen tarih ve dosya boyutu için algılanan dosyaları görüntüler. <br /> Varsayılan olarak, cihaz başına ekran için kullanılabilen en fazla dosya sayısı 300 ' dir. <br /> Varsayılan olarak, her bir dosya için en büyük dosya boyutu 15 MB 'tır. |
| Dosya durumu :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Dosya etiketleri cihazdaki dosyanın durumunu belirtir; Örneğin: <br /> **Eklendi**: dosya, seçilen tarih veya saatte uç noktaya eklendi. <br /> **Güncelleştirildi**: dosya seçili tarih veya saatte güncelleştirildi. <br /> **Silindi**: Bu dosya kaldırıldı. <br /> **Etiket yok**: dosya değiştirilmedi.   |
| Programlama aygıtı | Programlama değişikliğini yapan cihaz. Birden çok cihaz, programlanan tek bir cihazda programlama değişiklikleri yapmış olabilir. Değişiklik ve oturum açan kullanıcının ana bilgisayar adı, tarihi veya saati görüntülenir. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Programlanmış cihazda yüklü olan geçerli dosyayı görüntüler. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Görüntülenmiş kodun metin dosyasını indirin. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Geçerli dosyayı seçili bir tarihte algılanan dosyayla karşılaştırın. |

### <a name="choose-a-file-to-review"></a>Gözden geçirilecek dosyayı seçin

Bu bölümde, gözden geçirmek için bir dosyayı seçme açıklanmaktadır.

Gözden geçirilecek bir dosya seçmek için:

1. **Son olaylar** bölmesinden bir olay seçin

2. Dosya bölmesini bir dosya biçiminde seçin. Dosya geçerli bölmede görüntülenir.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Birlikte çalışmak için dosyayı seçin.":::

### <a name="compare-files"></a>Dosyaları Karşılaştır

Bu bölümde, programlama dosyalarının nasıl karşılaştırılacağı açıklanmaktadır.

Karşılaştırmak için:

1. Son olaylar bölmesinden bir olay seçin.

2. Dosya bölmesinden bir dosya seçin. Dosya geçerli bölmede görüntülenir. Bu dosyayı diğer dosyalarla karşılaştırabilirsiniz.

3. Karşılaştırma göstergesini seçin.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Karşılaştırma göstergesi":::

   Pencerede, seçilen dosyanın programlanmış cihazda algıladığı tüm tarihler görüntülenir. Dosya, programlanmış cihazda birden çok programlama aygıtı tarafından güncelleştirilmiş olabilir.

   Algılanan farklar sayısı pencerenin sağ üst köşesinde görüntülenir. Farkları görüntülemek için aşağı kaydırmanız gerekebilir.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="seçiminize doğru kaydırın":::

   Sayı değiştirilen metnin bitişik satırlarıyla hesaplanır. Örneğin, art arda sekiz kod satırı değiştirilirse (silindi, güncelleştirildi veya eklendiyse), bu bir fark olarak hesaplanır.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Programlama zaman çizelgesi görünümize bakın.":::

4. Bir tarih seçin. Seçilen tarihte algılanan dosya pencerede görüntülenir.

5. Son olaylar/dosyalar bölmesinden seçilen dosya her zaman sağ tarafta görüntülenir.

### <a name="device-programming-information-other-locations"></a>Cihaz programlama bilgileri: diğer konumlar

Programlama zaman çizelgesindeki ayrıntıları gözden geçirmeye ek olarak, cihaz Özellikler penceresi ve cihaz envanterindeki programlama bilgilerine de erişebilirsiniz.

| Cihaz Türü | Description |
|--|--|
| Cihaz özellikleri | Cihaz özellikleri penceresi, aygıt aygıtında algılanan son programlama olayı hakkında bilgi sağlar. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Cihazınızın Özellikleri"::: |
| Cihaz envanteri | Cihaz envanteri, cihazın bir programlama aygıtı olup olmadığını gösterir. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Cihazların envanteri"::: |

## <a name="manage-device-information-from-the-map"></a>Eşlemden cihaz bilgilerini yönetme

Algılayıcı, cihazları doğrudan ağda güncelleştirmez veya etkilemez. Burada yapılan değişiklikler, cihazın nasıl analiz olduğunu etkiler.

### <a name="delete-devices"></a>Cihazları Sil

Öğrenilmiş bilgiler ilgili değilse bir cihazı silmek isteyebilirsiniz. Örneğin,

  - Mühendislik iş istasyonundaki iş ortağı yüklenicisi, yapılandırma güncelleştirmelerini gerçekleştirmek için geçici olarak bağlanır. Görev tamamlandıktan sonra cihaz kaldırılır.

  - Ağdaki değişiklikler nedeniyle, bazı cihazlar artık bağlı değil.

Cihazı silmeyin, algılayıcı onu izlemeye devam eder. 60 gün sonra, silmenizi öneren bir bildirim görüntülenir.

Başka bir cihaz erişmeye çalışırsa cihazın yanıt vermemesine işaret eden bir uyarı alabilirsiniz. Bu durumda, ağınız yanlış yapılandırılmış olabilir.

Cihaz, cihaz eşlemesinden, cihaz envanterinden ve veri araştırma raporlarından kaldırılır. Diğer bilgiler, örneğin: pencere öğelerinde depolanan bilgiler korunur.

Cihazın silinmesi en az 10 dakika boyunca etkin değil olmalıdır.

Cihaz eşlemesinden bir cihazı silmek için:

1. Yan menüdeki **cihazlar** ' ı seçin.

2. Bir cihaza sağ tıklayın ve **Sil**' i seçin.

### <a name="merge-devices"></a>Cihazları birleştirme

Belirli koşullarda cihazları birleştirmeniz gerekebilir. Algılayıcı, tek bir benzersiz cihazla ilişkili ayrı ağ varlıkları tespit edildiğinde bu gerekli olabilir. Örneğin,

  - Dört ağ kartına sahip bir PLC.

  - WIFI ve fiziksel kart içeren bir dizüstü bilgisayar.
  
  - İki veya daha fazla ağ kartına sahip bir Iş Istasyonu.

Birleştirme sırasında, sensöre iki cihazın cihaz özelliklerini tek tek birleştirmek için talimat alırsınız. Bunu yaptığınızda cihaz Özellikler penceresi ve algılayıcı raporları yeni cihaz özelliği ayrıntıları ile güncelleştirilir.

Örneğin, her biri bir IP adresi olan iki cihazı birleştirirseniz, her iki IP adresi de cihaz Özellikler penceresi ayrı arabirimler olarak görünür. Yalnızca yetkili cihazları birleştirebilirsiniz.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Cihaz Özellikler penceresi":::

Olay zaman çizelgesi birleştirme olayını gösterir.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Birleştirilmiş olaylar içeren olay zaman çizelgesi.":::

Bir cihaz birleştirme işlemini geri alamazsınız. Yanlışlıkla iki cihaz birleştiriyorsunuz, cihazı silin ve algılayıcının her ikisini de yeniden bulmasını bekleyin.

Cihazları birleştirmek için:

1. İki cihaz seçin (Shift tuşuna basın) ve sonra bunlardan birine sağ tıklayın.

2. Cihazları birleştirmek için **Birleştir** ' i seçin. Birleştirme işleminin tamamlanması 2 dakikaya kadar sürebilir.

3. Birleştirme cihazı özniteliklerini Ayarla iletişim kutusunda bir cihaz adı seçin.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="öznitelikler iletişim kutusu":::

4. **Kaydet**’i seçin.

### <a name="authorize-and-unauthorize-devices"></a>Cihazların yetkisini verme ve yetkisini kaldırma

Öğrenme döneminde, ağda bulunan tüm cihazlar yetkili cihaz olarak tanımlanır. **Yetkili** etiket, cihaz eşlemesindeki bu cihazlarda görünmez.

Öğrenme süresinden sonra bir cihaz bulunduğunda, bu, yetkisiz bir cihaz olarak görünür. Eşlemdeki yetkisiz cihazları görmenin yanı sıra, bunları cihaz envanterinde de görebilirsiniz.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Cihaz envanteri":::

**Yeni cihaz yetkisiz**

Öğrenme döneminin bir ve etiketiyle görünmesi sonrasında yeni cihazlar algılandı `New` `Unauthorized` .

Bir cihazı haritada taşırsanız veya cihaz özelliklerini el ile değiştirirseniz, `New` etiket cihaz simgesinden kaldırılır.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Yetkisiz cihazlar-saldırı vektörleri ve risk değerlendirmesi raporları

Yetkisiz cihazlar, risk değerlendirmesi raporlarına ve saldırı vektörleri raporlarına dahildir.

- **Saldırı vektörü raporları:** Yetkisiz olarak işaretlenen cihazlar, ağ için tehdit olabilecek yanlış bir cihaz olduğundan şüphelenildiği için saldırı vektörü içinde çözümlenir.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Saldırı vektör raporlarınızı vew":::

- **Risk değerlendirmesi raporları:** Yetkisiz olarak işaretlenen cihazlar şunlardır:

  - Risk değerlendirmesi raporlarında tanımlanmıştır

Cihazların yetkisini el ile yetkilendirmek veya yetkilendirmeyi kaldırmak için:

1. Haritada cihaza sağ tıklayın ve **Yetkilendirmeyi kaldır** ' ı seçin.

### <a name="mark-devices-as-important"></a>Cihazları önemli olarak işaretle

Önemli ağ aygıtlarını önemli olarak işaretleyebilirsiniz; Örneğin, iş açısından kritik sunucular. Bu cihazlar haritada bir yıldız ile işaretlenir. Yıldız, haritanın yakınlaştırma düzeyine göre farklılık gösterir.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Önemli cihazlar-saldırı vektörleri ve risk değerlendirmesi raporları

Risk değerlendirmesi raporları ve saldırı vektörleri raporları oluşturulurken önemli cihazlar hesaplanır.

  - Saldırı vektörü raporları önemli olarak işaretlenen cihazlar saldırı vektörü olarak saldırı vektörü olarak çözümlenir. 

  - Risk değerlendirmesi raporları: risk değerlendirmesi raporunda güvenlik puanı sağlanırken önemli olarak işaretlenen cihazlar hesaplanır.

## <a name="generate-activity-reports-from-the-map"></a>Haritadan etkinlik raporları oluşturma

Seçilen bir cihaz için 1, 6, 12 veya 24 saat üzerinden bir etkinlik raporu oluşturun. Aşağıdaki bilgiler kullanılabilir:

  - Kategori: trafik senaryolarına dayalı temel algılama bilgileri.

  - Kaynak ve hedef cihazlar

  - Veri: ek bilgi bozuldu.

  - Son görülen saat ve tarih.

Raporu bir Microsoft Excel veya Word dosyası olarak kaydedebilirsiniz.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="En son etkinlikler":::

Bir cihaz için etkinlik raporu oluşturmak için:

1. Haritadaki bir cihaza sağ tıklayın.

2. Etkinlik raporu seçin.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Etkinliğinizi bir rapor görüntüleyin.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Haritadan saldırı vektör raporları oluşturma

Seçtiğiniz haritadaki bir cihazın savunmasız saldırı hedefi olup olmadığını öğrenmek için bir saldırı vektörü raporunun benzetimini yapın.

Saldırı vektör raporları, açıktan yararlanılabilecek bir cihaz güvenlik açığı zincirinin grafik gösterimini sağlar. Bu güvenlik açıkları, bir saldırganın ana ağ cihazlarına erişmesini sağlayabilir. Saldırı vektörü simülatörü, saldırı vektörlerini gerçek zamanlı olarak hesaplar ve belirli bir hedefe göre tüm saldırı vektörlerini çözümler.

Bir cihazı saldırı vektörü raporlarında görüntülemek için:

1. Haritadaki bir cihaza sağ tıklayın.

2. **Saldırı vektörlerini Benzet**' i seçin. Saldırı vektörü iletişim kutusu, saldırı hedefi olarak seçtiğiniz cihazla açılır.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Saldırı vektörü simülasyonu ekleme":::

3. İletişim kutusuna kalan parametreleri ekleyin ve **Benzetim Ekle**' yi seçin.

## <a name="export-device-information-from-the-map"></a>Haritadan cihaz bilgilerini dışarı aktar

Aşağıdaki cihaz bilgilerini haritadan dışarı aktarın.

  - Cihaz ayrıntıları (Microsoft Excel)

  - Bir cihaz Özeti (Microsoft Excel)

  - Gruplar içeren bir kelime dosyası (Microsoft Word)

Dışarı aktarmak için:

1. Haritadan dışa aktarma simgesini seçin.

1. Dışarı aktarma seçeneği belirleyin.

## <a name="see-also"></a>Ayrıca bkz.

[Bir cihaz envanterinde algılayıcı algılamalarını araştırın](how-to-investigate-sensor-detections-in-a-device-inventory.md)
