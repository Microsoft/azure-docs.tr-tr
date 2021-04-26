---
title: Eğilimler ve istatistik raporları oluşturma
description: IoT eğilimleri ve Istatistik pencere öğeleri için Defender 'ı kullanarak ağ etkinlikleri, istatistikler ve eğilimler hakkında öngörüler elde edin.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779010"
---
# <a name="sensor-trends-and-statistics-reports"></a>Algılayıcı eğilimleri ve istatistik raporları

Ağ eğilimleri ve istatistikleriyle ilgili bilgi edinmek için pencere öğesi grafikleri ve pasta grafikleri oluşturabilirsiniz. Pencere öğeleri, Kullanıcı tanımlı panolar altında gruplandırılabilir.

> [!NOTE]
> Yönetici ve Güvenlik analistleri, eğilimler ve Istatistik raporları oluşturabilir.

Pano, aşağıdaki bilgi türlerini grafiksel olarak tanımlayan pencere öğeleri içerir:

- Bağlantı noktasına göre trafik
- Bağlantı noktasına göre popüler trafik
- Kanal bant genişliği
- Toplam bant genişliği
- Etkin TCP bağlantısı
- VLAN tarafından en yüksek bant genişliği
- Cihazlarınız
  - Yeni cihazlar
  - Meşgul cihazlar
  - Satıcıya göre cihazlar
  - İşletim sistemine göre cihazlar
  - VLAN başına cihaz sayısı
  - Bağlantısı kesilen cihazlar
- Saatlere göre bağlantı düşme
- Türe göre olay uyarıları
- Veritabanı tablosu erişimi
- Protokol açıklaması pencere öğeleri
- DELTAV
  - DeltaV Roc işlemleri dağıtımı
  - DeltaV Roc olayları ada göre
  - Zamana göre DeltaV olayları
- AMS
  - Sunucu bağlantı noktasına göre AMS trafiği
  - Komutuna göre AMS trafiği
- Ethernet ve IP adresi:
  - CıP hizmetine göre Ethernet ve IP adresi trafiği
  - CıP sınıfına göre Ethernet ve IP adresi trafiği
  - Komuta göre Ethernet ve IP adresi trafiği
- OPC
  - OPC üst yönetim işlemleri
  - OPC Top g/ç işlemleri
- Siemens S7:
  - Denetim işlevine göre S7 trafiği
  - Alt işleve göre S7 trafiği
- VLAN
  - VLAN başına cihaz sayısı
  - VLAN tarafından en yüksek bant genişliği
- 60870-5-104
  - IEC-ASDU 'e göre 60870 trafiği
- BACNET
  - BACnet Hizmetleri
- DNP3
  - İşleve göre DNP3 trafiği
- SRTP:
  - Hizmet koduna göre SRTP trafiği
  - Güne göre SRTP hataları
- SuiteLink:
  - SuiteLink üst sorgulanan Etiketler
  - SuiteLink sayısal etiket davranışı
- IEC-ASDU 'e göre 60870 trafiği
- İşleve göre DNP3 trafiği
- Hizmete göre MMS trafiği
- İşleve göre Modbus trafiği
- Hizmete göre OPC-UA trafiği

> [!NOTE]
>  Pencere öğelerinin zaman algılayıcısı, algılayıcı zamanına göre ayarlanır.

## <a name="create-reports"></a>Rapor oluşturma

Panoları ve pencere öğelerini görmek için:

Yan menüdeki **eğilim & istatistikleri** ' ni seçin.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Bir araştırmanın ekran görüntüsü.":::

Varsayılan olarak, sonuçlar son yedi gün içinde algılamalar için görüntülenir. Bu aralığı değiştirmek için filtre araçlarını kullanabilirsiniz. Örneğin, ücretsiz bir metin araması.

## <a name="create-a-dashboard"></a>Pano oluşturma

**Pano** açılır menüsünü seçerek yeni bir pano oluşturun. Bir panoda birçok pencere öğesi oluşturabilir ve ekleyebilirsiniz.

Aşağıdaki seçenekleri kullanarak özelleştirilmiş panolar oluşturabilirsiniz:

- Panoya pencere öğesi ekleme

- Panodan pencere öğesi silme

- Pencere öğesinin filtresini değiştirme

- Pencere öğesini yeniden boyutlandırma

- Pencere öğesinin konumunu değiştirme

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Pencere öğesinin konumunu değiştirin.":::

Özelleştirilmiş bir pano oluşturmak için:

1. Sol paneldeki **eğilimleri ve istatistikleri** seçin.

1. **Pano seç** açılan menüsünü seçin ve **Pano Oluştur** düğmesini seçin.

1. Yeni Pano için anlamlı bir ad girin ve **Oluştur**' u seçin.

1. Sayfanın sol üst kısmındaki **pencere öğesi Ekle** ' yi seçin.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Pencere öğesi deposundaki pencere öğesini seçin.":::

1. **Güvenlik** ve **İşlemsel** pencere öğeleri pencerenin sağ üst kısmında bulunur. Çeşitli kategoriler ve protokoller arasından seçim yapın. Her pencere öğesi ile küçük bir grafik içeren kısa bir açıklama görüntülenir. Tüm kullanılabilir pencere öğelerini görmek için kaydırma çubuğunu kullanın.

1. **Eklemek Için tıklayın** düğmesini kullanarak bir pencere öğesi seçin. Pencere öğesi hemen panoda görüntülenir.

Bir panoyu silmek için:

1. Açılır menüden panonun adını seçin.

1. **Sil** simgesini seçin ve ardından **Tamam**' ı seçin.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Panoyu silmek için Sil simgesini seçin.":::

Bir pano adını düzenlemek için:

1. Açılır menüden panonun adını seçin.

1. **Düzenle** simgesini seçin.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Panonuzun adını düzenlemek için Düzenle simgesini seçin.":::

1. Pano için yeni bir ad girin ve **Kaydet**' i seçin.
 
Varsayılan panoyu ayarlamak için:

1. Açılır menüden panonun adını seçin.

1. Varsayılan pano olarak ayarlanacak panoyu seçmek için **yıldız** simgesini seçin.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Varsayılan panonuzu seçmek için yıldız simgesini seçin."::: 

Bir pencere öğesinde filtreleme verilerini değiştirmek için:

1. **Filtre** simgesini seçin.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Pencere öğesinin parametrelerini ayarlamak için filtre simgesini seçin.":::

1. Gerekli parametreleri düzenleyin.

1. **Tamam**’ı seçin.

Pencere öğesini silmek için:

- Simgesini seçin :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Pencere öğesini silmek için X seçeneğini belirleyin.":::

## <a name="creating-widgets"></a>Pencere öğeleri oluşturma 

Pencere öğesi deposu, kategoriye ve protokole göre pencere öğeleri seçmenizi sağlar. **Güvenlik** veya kullanılabilir **İşlemsel** pencere öğelerini seçerek görüntüleyebilirsiniz.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Pencere öğesi deposundan pencere öğesini seçin.":::

Her pencere öğesi sistem trafiği, ağ istatistikleri, protokol istatistikleri, cihaz ve uyarı bilgileri hakkında özel bilgiler içerir. Pencere öğesi için veri olmadığında bir ileti görüntülenir.

Kalan dilimlerin göreli önemini daha net bir şekilde görmek için pasta grafiğinde pasta grafiğinden bir bölümü kaldırabilirsiniz. Bunu yapmak için ekranın altındaki göstergede dilimin adını seçin.

Aşağıdaki bölümler, bazı pencere öğeleri için kullanım örneklerinin örneklerini sunar.

### <a name="busy-devices-widget"></a>Meşgul cihazlar pencere öğesi

Bu pencere öğesi, en çok kullanılan beş bir cihazı listeler. **Düzenleme** modunda, bilinen protokollere göre filtreleyebilirsiniz.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Meşgul cihaz pencere öğesinin bir görünümü.":::

### <a name="total-bandwidth-widget"></a>Toplam bant genişliği pencere öğesi

Bu pencere öğesi, MB/sn cinsinden bant genişliğini izler (saniye başına megabit). Bant genişliği y ekseni üzerinde, x ekseninde görünen tarih ile belirtilir. **Düzenleme** modu, sonuçları Istemci, sunucu, sunucu bağlantı noktası veya alt ağa göre filtrelemenize izin verir. İmleci grafiğin üzerine getirdiğinizde bir araç ipucu görünür.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Toplam bant genişliği pencere öğesinin bir görünümü.":::

### <a name="channels-bandwidth-widget"></a>Kanallar bant genişliği pencere öğesi

Bu pencere öğesi, en iyi beş trafik kanalını görüntüler. Adrese göre filtre uygulayabilir ve sunulan sonuçların sayısını ayarlayabilirsiniz. Daha fazla kanal göstermek için aşağı oku seçin.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Kanallar bant genişliği pencere öğesinin bir görünümü.":::

### <a name="traffic-by-port-widget"></a>Bağlantı noktası pencere öğesine göre trafik

Bu pencere öğesi, her bağlantı noktası farklı bir renkle belirlenen bir pasta grafik tarafından belirtilen bağlantı noktasına göre trafiği görüntüler. Her bağlantı noktasındaki trafik miktarı, pastanın bölümünün boyutuyla orantılıdır.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Bağlantı noktası pencere öğesinin trafik görünümü.":::

### <a name="new-devices-widget"></a>Yeni cihazlar pencere öğesi

Bu pencere öğesi, belirli bir tarihte kaç tane yeni cihaz keşfedildiğini gösteren yeni aygıtlar çubuk grafiğini görüntüler.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Yeni cihazlar pencere öğesinin bir görünümü.":::

### <a name="protocol-dissection-widgets"></a>Protokol açıklaması pencere öğeleri

Bu pencere öğesi, protokol başına trafiğe, işlev kodlarına ve hizmetlere göre bir bakış sağlayan bir pasta grafiği görüntüler. Pastanın her diliminin boyutu, diğer dilimlerle ilgili trafik miktarıyla orantılıdır.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Protokol ayırıcı pencere öğesinin bir görünümü.":::

### <a name="active-tcp-connections-widget"></a>Etkin TCP bağlantıları pencere öğesi

Bu pencere öğesi, sistemdeki etkin TCP bağlantılarının sayısını gösteren bir grafik görüntüler.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Etkin TCP bağlantıları pencere öğesinin bir görünümü.":::

### <a name="incident-by-type-widget"></a>Türe göre olay pencere öğesi

Bu pencere öğesi, türüne göre olayların sayısını gösteren bir pasta grafiği görüntüler. Bu, her bir altyapının tarafından önceden tanımlanmış bir süre boyunca oluşturulan uyarı sayısıdır.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Olay pencere öğesi türü tarafından bir görünüm.":::

## <a name="devices-by-vendor-widget"></a>Satıcı pencere öğesine göre cihazlar

Bu pencere öğesi, satıcıya göre cihaz sayısını gösteren bir pasta grafiği görüntüler. Belirli bir satıcının cihaz sayısı, diğer cihaz satıcılarıyla bağlantılı olarak diskin satıcı satıcısı bölümünün boyutuyla orantılıdır.

## <a name="number-of-devices-per-vlan-widget"></a>VLAN pencere öğesi başına cihaz sayısı

Bu pencere öğesi, VLAN başına bulunan cihaz sayısını gösteren bir pasta grafiği görüntüler. Pastanın her diliminin boyutu, diğer dilimlerle ilişkili bulunan cihazların sayısıyla orantılıdır.

Her VLAN, el ile eklediğiniz algılayıcı veya ad tarafından atanan VLAN etiketiyle görüntülenir.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Cihaz pencere öğesi sayısının görünümü.":::

### <a name="top-bandwidth-by-vlan-widget"></a>VLAN pencere öğesine göre en yüksek bant genişliği

Bu pencere öğesi, VLAN tarafından bant genişliği tüketimini görüntüler. Varsayılan olarak, pencere öğesi en yüksek bant genişliği kullanımıyla beş VLAN gösterir.

Verileri pencere öğesinde sunulan döneme göre filtreleyebilirsiniz. Daha fazla sonuç göstermek için aşağı oku seçin.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="VLAN pencere öğesinin en yüksek bant genişliğinin görünümü.":::

## <a name="system-report"></a>Sistem raporu

Sistem raporunu indirmek için: 

1. Yan menüdeki **eğilim & istatistikleri** ' ni seçin.

1. Sağ üst köşedeki **Sistem raporu** ' nu seçin. Rapor otomatik olarak indirilecek.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Sistem raporunun bir kopyasını indirmek için sistem raporu düğmesini seçin.":::

Sistem raporu, sistemdeki tüm verileri içeren bir PDF dosyasıdır:

  - Cihazlar

  - Uyarılar

  - Ağ Ilkesi bilgileri

## <a name="devices-in-a-system-report"></a>Bir sistem raporundaki cihazlar 

Sistem raporu tüm cihazların bir listesini ve bunların bilgilerini gösterir. Örneğin, kullanılan tür, ad ve protokoller. Sistem raporu ayrıca satıcı başına cihazların bir listesini gösterir.

## <a name="alerts-in-system-report"></a>Sistem raporundaki uyarılar 

Sistem raporu, tarih ve önem derecesi gibi bilgileri içeren tüm uyarıların listesini gösterir.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Sistem raporlarındaki uyarıların bir görünümü.":::

## <a name="network-information-in-system-report"></a>Sistem raporundaki ağ bilgileri 

Sistem raporu, ağ temelinizi ayrıntılı olarak gösterir. Örneğin, DNP3 işlev kodu ve bağlantı başına açık bağlantı noktaları.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Sistem raporundan DNP3 işlevinin bir görünümü.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Bağlantı başına açık bağlantı noktası raporunun görünümü.":::

## <a name="see-also"></a>Ayrıca bkz.

[Risk değerlendirmesi raporlaması](how-to-create-risk-assessment-reports.md) 
 [Algılayıcı veri araştırma sorguları](how-to-create-data-mining-queries.md) 
 [Saldırı vektörü raporlaması](how-to-create-attack-vector-reports.md)
