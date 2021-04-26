---
title: Belirli bir algılayıcı tarafından bulunan cihazlarla ilgili Öngörüler elde edin
description: Cihaz envanteri, bir algılayıcının algıladığı çok sayıda cihaz özniteliği görüntüler.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 4daec83f44a545d7837a7e73e847f56b1f5770e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782104"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Bir cihaz envanterinde algılayıcı algılamalarını araştırma

Cihaz envanteri, bir algılayıcının algıladığı çok sayıda cihaz özniteliği görüntüler. Seçenekler kullanılabilir:

 - Bilgileri kolayca filtreleyin.

 - Bilgileri bir CSV dosyasına aktarın.

 - Windows kayıt defteri ayrıntılarını içeri aktarın.

 - Cihaz eşlemesinde görüntülenmek üzere gruplar oluşturun.

## <a name="view-device-attributes-in-the-device-inventory"></a>Cihaz envanterinde cihaz özniteliklerini görüntüleme

Aşağıdaki öznitelikler cihaz envanter tablosunda görünür.

| Parametre | Açıklama |
|--|--|
| Ad | Algılayıcısı bulduğu veya Kullanıcı tarafından girildiği haliyle cihazın adı. |
| Tür | Algılayıcı tarafından belirlendiği veya Kullanıcı tarafından girilen cihaz türü. |
| Satıcı | MAC adresinde tanımlandığı şekilde cihazın satıcısının adı. |
| Operating System | Algılanan cihaz işletim sistemi. |
| Üretici yazılımı sürümü | Algılanan cihaz üretici yazılımı. |
| IP Adresi | Tanımlanan cihazın IP adresi. |
| VLAN | Cihazın VLAN 'ı. VLAN 'Ları bulma algılayıcısı hakkında daha fazla bilgi için bkz. [VLAN adlarını tanımlama](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (nasıl yapılır-tanımlama-yönetim-konsol-ağ-ayarları. MD # define-VLAN-Names). |
| MAC Adresi | Cihazın MAC adresi. |
| Protokoller | Cihazın kullandığı protokoller. |
| Kabul edilmemiş uyarılar | Bu cihazla ilişkili kabul edilmemiş uyarı sayısı. |
| Yetkilendirildi | Kullanıcı tarafından tanımlanan yetkilendirme durumu:<br />- **Doğru**: cihaz yetkilendirildi.<br />- **Yanlış**: cihaz yetkilendirilmedi. |
| Tarayıcı olarak bilinir | Kullanıcı tarafından bir ağ tarama cihazı olarak tanımlanır. |
| Programlama aygıtı | Kullanıcı tarafından yetkilendirilmiş bir programlama cihazı olarak tanımlanır. <br />- **Doğru**: cihaz, mühendislik istasyonlarıyla Ilgili olan PLCs, DTU 'lar ve denetleyiciler için programlama etkinlikleri gerçekleştirir. <br />- **Yanlış**: cihaz bir programlama aygıtı değildir. |
| Gruplar | Bu cihazın katıldığı gruplar. |
| Son etkinlik | Cihazın gerçekleştirdiği son etkinlik. |
| Bulundu | Bu cihaz ağda ilk kez görüldüğünde. |

Cihaz envanterini görüntülemek için:

1. Sol bölmede **cihazlar**' ı seçin. Sağ tarafta **aygıtlar** bölmesi açılır.

2. **Cihazlar** bölmesinde, öğesini seçin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Sütunları gizlemek ve göstermek için cihaz envanter tablosunu özelleştirin:

1. Cihaz envanterinin sağ üst menüsünde öğesini seçin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Cihaz envanteri ayarları ekranı.":::

2. **Cihaz envanteri ayarları** penceresinde, cihaz envanter tablosunda görüntülenmesini istediğiniz sütunları seçin.

3. Okları kullanarak tablodaki sütunların konumunu değiştirin.

4. **Kaydet**’i seçin. **Cihaz envanteri ayarları** penceresi kapanır ve yeni ayarlar tabloda görüntülenir.

### <a name="create-temporary-device-inventory-filters"></a>Geçici cihaz envanter Filtreleri oluştur

Tablonun görüntüleyeceği bilgileri tanımlayan bir filtre ayarlayabilirsiniz. Örneğin, yalnızca PLC cihazının bilgilerini görüntülemek istediğinize karar verebilirsiniz.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Cihazları öğreniyor.":::

Stoktan ayrıldığınızda filtre kaydedilmez.

### <a name="save-device-inventory-filters"></a>Cihaz envanter filtrelerini Kaydet

İhtiyaç duyduğunuz bir filtreyi veya filtre birleşimini kaydedebilir ve bunları cihaz envanterinde yeniden uygulayabilirsiniz. Belirli bir cihaz türüne veya belirli bir tür ve belirli bir protokole göre daha fazla dar filtreye göre daha geniş filtreler oluşturun.

Kaydettiğiniz filtreler Ayrıca cihaz eşleme grupları olarak kaydedilir. Bu özellik, haritada ağ cihazlarını görüntülerken ek bir ayrıntı düzeyi sağlar.

Filtre oluşturmak için:

1. Filtrelemek istediğiniz sütunda öğesini seçin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. **Filtre** iletişim kutusunda filtre türünü seçin:

   - **Eşittir**: sütunu filtrelemek istediğiniz değere göre tam değer. Örneğin, protokol sütununu **Equals** ve şuna göre filtreleyerek `value=ICMP` , sütun yalnızca ICMP protokolünü kullanan cihazları sunar.

   - **Contains**: sütundaki diğer değerler arasında yer alan değer. Örneğin, protokol sütununu ve **içerir** öğesine göre filtreleyerek `value=ICMP` , sütun, ICMP protokolünü kullanan cihazları cihazın kullandığı protokoller listesinin bir parçası olarak sunar.

3. Sütun bilgilerini alfabetik sıraya göre düzenlemek için öğesini seçin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Ve oklarını seçerek sırayı düzenleyin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Yeni bir filtre kaydetmek için filtreyi tanımlayın ve **farklı kaydet**' i seçin.

5. Filtre tanımlarını değiştirmek için tanımları değiştirin ve **Değişiklikleri Kaydet**' i seçin.

Filtreleri görüntülemek için:

- Sol Bölmeyi açın ve kaydettiğiniz filtreleri görüntüleyin:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Sol taraftaki bölmedeki filtreleri görüntüleyin.":::

### <a name="view-filtered-information-as-a-map-group"></a>Filtrelenmiş bilgileri bir harita grubu olarak görüntüle

Harita görünümüne geçtiğinizde filtrelenmiş cihazlar vurgulanır ve filtrelenir. Kaydettiğiniz filtre grubu, **cihaz envanteri filtreleri** grubu altındaki yan menüde görünür.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Harita görünümünde filtreleri görüntüleyin.":::

## <a name="learn-windows-registry-details"></a>Windows kayıt defteri ayrıntılarını öğrenin

Cihazların öğrenimine ek olarak, Microsoft Windows iş istasyonlarını ve sunucularını bulabilirsiniz. Bu cihazlar Ayrıca cihaz envanterinde de görüntülenir. Cihazları öğrendikten sonra, cihaz envanterini ayrıntılı Windows bilgileriyle zenginleştirebilirsiniz, örneğin:

- Windows sürümü yüklendi

- Yüklü uygulamalar

- Düzeltme eki düzeyi bilgileri

- Bağlantı noktalarını açma

- İşletim sistemi sürümleri hakkında daha sağlam bilgiler

Bu bilgileri almak için iki seçenek mevcuttur:

- Zamanlanan WMI taramaları kullanılarak etkin yoklama. 

- Cihaza bir betiği dağıtarak ve çalıştırarak yerel araştırma. Yerel betiklerle çalışma bir uç noktada WMI yoklaması çalıştırmanın risklerini atlar. Ayrıca, su ve tek yönlü öğeleri olan düzenlenmiş ağlarda de yararlıdır.

Bu makalede, Windows uç noktası kayıt defterinde bir komut dosyası ile yerel olarak nasıl anket yapılacağı açıklanır. Bu bilgiler, uyarı, bildirim, veri araştırma raporları, risk değerlendirmeleri ve saldırı vektörü raporları oluşturmak için kullanılacaktır.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Veri madenciliği ekran görüntüsü.":::

Aşağıdaki Windows işletim sistemlerini ankete ekleyebilirsiniz:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Başlamadan önce

Komut dosyasıyla çalışmak için aşağıdaki gereksinimleri karşılamanız gerekir:

- Betiği cihazda çalıştırmak için yönetici izinleri gereklidir.

- Algılayıcı Windows cihazını zaten öğrenmiş olmalıdır. Bu, cihaz zaten mevcutsa betiğin bilgilerini alacağını gösterir.

- Bir algılayıcı, Windows BILGISAYARıN bağlı olduğu ağı izliyor.

### <a name="acquire-the-script"></a>Betiği al

Betiği almak için [müşteri desteği 'ne başvurun](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Betiği dağıtma

Standart otomatik dağıtım yöntemleri ve araçları kullanarak betiği bir kez dağıtabilir veya devam eden sorgular zamanlayabilirsiniz.

### <a name="about-the-script"></a>Betiği hakkında

- Betik, yüklü bir program değil, bir yardımcı program olarak çalıştırılır. Betiği çalıştırmak uç noktayı etkilemez.

- Betiğin oluşturduğu dosyalar, siz silene kadar yerel sürücüde kalır.

- Betiğin oluşturduğu dosyalar birbirlerinin yanında bulunur. Onları ayırın.

- Betiği aynı konumda yeniden çalıştırırsanız, bu dosyaların üzerine yazılır.

Betiği çalıştırmak için:  

1. Betiği yerel bir sürücüye kopyalayın ve sıkıştırmayı açın. Aşağıdaki dosyalar görüntülenir:

    - start.bat

    - Üzerinde settings.js

    - Data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Dosya Gezgini 'nde dosyaların görünümü.":::

2. Dosyasını çalıştırın `run.bat` .

3. Kayıt defteri araştırdıktan sonra, CX-Snapshot dosyası kayıt defteri bilgileriyle görüntülenir.

4. Dosya adı, anlık görüntünün sistem adını ve tarihini ve saatini gösterir. Örnek bir dosya adı `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Cihaz bilgilerini içeri aktar

Her bir uç noktada öğrenilen bilgiler sensöre aktarılmalıdır.

Sorgulardan oluşturulan dosyalar sensörlerden erişebileceğiniz bir klasöre yerleştirilebilir. Her bir Windows uç noktasından dosyaları sensöre içeri aktardığınız konuma taşımak için standart, otomatik yöntemler ve araçlar kullanın.

Dosya adlarını güncelleştirmeyin.

İçeri aktarmak için:

1. **Windows yapılandırması Içeri aktar** Iletişim kutusundan **Ayarları içeri aktar** ' ı seçin.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Windows yapılandırmalarınızı içeri aktarın.":::

2. **Ekle**' yi seçin ve ardından tüm dosyalar ' ı (CTRL + A) seçin.

3. **Kapat**’ı seçin. Cihaz kayıt bilgileri içeri aktarılır. Dosyalardan birini karşıya yüklerken bir sorun oluşursa, hangi dosyanın karşıya yüklenemeyecekleri hakkında bilgi edineceksiniz.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Eklenen dosyalar başarıyla karşıya yüklendi.":::

## <a name="export-device-inventory-information"></a>Cihaz envanter bilgilerini dışarı aktar

Cihaz envanter bilgilerini bir Excel dosyasına dışarı aktarabilirsiniz.

Bir CSV dosyasını dışarı aktarmak için:

- Cihaz envanterinin sağ üst menüsünde öğesini seçin :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . CSV raporu oluşturulup indirilir.

## <a name="see-also"></a>Ayrıca bkz.

[Bir cihaz envanterinde tüm kurumsal algılayıcı algılamalarını araştırma](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Site Haritası görünümleriyle çalışma](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
