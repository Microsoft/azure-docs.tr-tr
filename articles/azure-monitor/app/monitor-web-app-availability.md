---
title: Tüm Web sitelerinin kullanılabilirlik ve yanıt hızını izleme-Azure Izleyici
description: Application Insights ' de ping testleri ayarlayın. Web sitesi kullanılamaz duruma gelirse veya yavaş yanıt verirse uyarı alın.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: 60698862e26175425221940a4b69867cb414fe86
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598882"
---
# <a name="monitor-the-availability-of-any-website"></a>Bir web sitesinin kullanılabilirliğini izleme

"URL ping test" adı, bir hatalı Nomer 'nin bir bitidir. Bu sınamalar, bu testler sitenizin kullanılabilirliğini denetlemek için ıCMP (Internet Denetim Iletisi Protokolü) kullanmaz. Bunun yerine, bir uç noktanın yanıt verip vermediğini doğrulamak için daha gelişmiş HTTP isteği işlevselliği kullanırlar. Ayrıca, bu Yanıt ile ilişkili performansı ölçer ve bağımlı istekleri ayrıştırma ve yeniden denemeye izin verme gibi daha gelişmiş özelliklerle birlikte özel başarı ölçütlerini ayarlama özelliğini ekler.

Bir kullanılabilirlik testi oluşturmak için, var olan bir Application Insight kaynağı kullanmanız veya [bir Application Insights kaynağı oluşturmanız](create-new-resource.md)gerekir.

İlk kullanılabilirlik isteğinizi oluşturmak için kullanılabilirlik bölmesini açın ve  **Test oluştur**' u seçin.

:::image type="content" source="./media/monitor-web-app-availability/availability-create-test-001.png" alt-text="Test oluşturma oluşturma ekranının ekran görüntüsü.":::

## <a name="create-a-test"></a>Test oluşturma

|Ayar| Açıklama
|----|----|----|
|**URL** |  URL, test etmek istediğiniz herhangi bir web sayfası olabilir, ancak ortak İnternet’te görünür olmalıdır. URL bir sorgu dizesi içerebilir. Bu nedenle, örneğin, veritabanınızla biraz alıştırma yapabilirsiniz. URL yeniden yönlendirme adresine çözümlenirse, en fazla 10 yeniden yönlendirmeyi izleriz.|
|**Bağımlı istekleri Ayrıştır**| Test istekleri görüntüleri, betikleri, stil dosyaları ve test edilen Web sayfasının parçası olan diğer dosyalar. Kayıtlı yanıt süresi, bu dosyaları almak için geçen süreyi içerir. Bu kaynaklardan herhangi biri, tüm test için zaman aşımı süresi içinde başarıyla indirilemezse test başarısız olur. Seçenek işaretlenmezse, test yalnızca belirttiğiniz URL’deki dosyayı ister. Bu seçeneğin etkinleştirilmesi, daha sıkı bir denetim elde ediyor. Test, siteye el ile gözatarken dikkat çekici olmayan durumlar için başarısız olabilir.
|**Yeniden denemeleri etkinleştir**|test başarısız olduğunda, kısa bir aralıktan sonra yeniden denenir. Art arda üç deneme başarısız olursa bir hata bildirilir. Sonraki testler bundan sonra her zamanki test sıklığında gerçekleştirilir. Bir sonraki başarılı olana kadar yeniden deneme geçici olarak askıya alınır. Bu kural her test konuma bağımsız olarak uygulanır. **Bu seçeneği öneririz**. Ortalama olarak hataların yaklaşık %80’i yeniden deneme sırasında kaybolur.|
|**Sınama sıklığı**| Testin her test konumundan ne sıklıkla çalıştırılacağını ayarlar. Beş dakikalık varsayılan sıklıkta ve beş test konumuyla, siteniz ortalama olarak dakikada bir test edilir.|
|**Test konumları**| , Sunucularımızın URL 'nize Web istekleri gönderdiğimiz yerdir. Web sitenizdeki sorunları ağ sorunlarından ayırabilmeniz için **en az önerilen test konumu sayısı beş ' dir** . En fazla 16 konum seçebilirsiniz.

**URL 'niz genel İnternet 'ten görünmüyorsa, yalnızca test işlemlerine izin vermek için güvenlik duvarınızı seçmeli olarak açmayı seçebilirsiniz**. Kullanılabilirlik testi aracılarımız için güvenlik duvarı özel durumları hakkında daha fazla bilgi edinmek için [IP adresi kılavuzuna](./ip-addresses.md#availability-tests)bakın.

> [!NOTE]
> **En az beş konum** içeren birden çok konumdan test etmenizi önemle öneririz. Bu, belirli bir konum ile geçici sorunlardan kaynaklanan yanlış alarmları önlemektir. Ayrıca en iyi yapılandırmanın, **Test konumları sayısının, uyarı konum eşiğine + 2 ' ye eşit** olduğunu bulduk.

## <a name="success-criteria"></a>Başarı ölçütleri

|Ayar| Açıklama
|----|----|----|
| **Test zaman aşımı** |Yavaş yanıtlar hakkında uyarı almak için bu değeri azaltın. Yanıtlar sitenizden bu süre içinde alınmadıysa test başarısız sayılır. **Bağımlı Istekleri Ayrıştır**' ı seçtiyseniz tüm görüntüler, stil dosyaları, betikler ve diğer bağımlı kaynaklar bu süre içinde alınmış olmalıdır.|
| **HTTP yanıtı** | Başarılı olarak sayılan döndürülen durum kodu. 200, normal web sayfası döndürüldüğünü belirten koddur.|
| **İçerik eşleşmesi** | "Hoş geldiniz!" gibi bir dize Her yanıtta büyük küçük harfe duyarlı bir tam eşleşme oluştuğunu test edebiliriz. Joker karakter bulunmayan düz bir dize olmalıdır. Sayfanızın içeriği değişirse bunu güncelleştirmeniz gerektiğini unutmayın. **İçerik eşleşmesi ile yalnızca Ingilizce karakterler desteklenir** |

## <a name="alerts"></a>Uyarılar

|Ayar| Açıklama
|----|----|----|
|**Neredeyse gerçek zamanlı (Önizleme)** | Neredeyse gerçek zamanlı uyarıların kullanılması önerilir. Bu tür bir uyarının yapılandırılması, kullanılabilirlik testiniz oluşturulduktan sonra yapılır.  |
|**Uyarı konum eşiği**|En az 3/5 konum önerilir. Uyarı konumu eşiği ve test konumlarının sayısı arasındaki en iyi ilişki,   =  **en az beş test konumu ile test konumlarından oluşan uyarı konum eşiği sayısıdır-2.**|

## <a name="location-population-tags"></a>Konum popülasyonu etiketleri

Azure Resource Manager kullanarak bir kullanılabilirlik URL 'SI ping testi dağıtıldığında, coğrafi konum özniteliği için aşağıdaki popülasyon etiketleri kullanılabilir.

### <a name="azure-gov"></a>Azure devleti

| Görünen Ad   | Popülasyon adı     |
|----------------|---------------------|
| USGov Virginia | usgov-VA-azr        |
| USGov Arizona  | usgov-PHX-azr       |
| USGov Texas    | usgov-TX-azr        |
| USDoD Doğu     | usgov-ddeast-azr    |
| USDoD orta  | usgov-ddorta-azr |

#### <a name="azure"></a>Azure

| Görünen Ad                           | Popülasyon adı   |
|----------------------------------------|-------------------|
| Avustralya Doğu                         | EMEA-au-Syd-Edge  |
| Güney Brezilya                           | LATAM-br-Gru-Edge |
| Central US                             | ABD-fl-Mia-Edge    |
| Doğu Asya                              | APAC-HK-hkn-azr   |
| Doğu ABD                                | ABD-VA-Ash-azr     |
| Fransa Güney (eski adıyla Fransa Orta) | EMEA-ch-ZRH-Edge  |
| Orta Fransa                         | EMEA-fr-PRA-Edge  |
| Doğu Japonya                             | APAC-JP-KAW-Edge  |
| Kuzey Avrupa                           | EMEA-GB-DB3-azr   |
| Orta Kuzey ABD                       | US-Il-CH1-azr     |
| Orta Güney ABD                       | ABD-TX-SN1-azr     |
| Güneydoğu Asya                         | APAC-SG-sin-azr   |
| Batı Birleşik Krallık                                | EMEA-i-STO-Edge  |
| West Europe                            | EMEA-nl-AMS-azr   |
| Batı ABD                                | ABD-CA-SJC-azr     |
| Güney Birleşik Krallık                               | EMEA-ru-MSA-Edge  |

## <a name="see-your-availability-test-results"></a>Kullanılabilirlik testi sonuçlarınızı görme

Kullanılabilirlik testi sonuçları hem çizgi hem de dağılım çizim görünümleriyle görselleştirilebilir.

Birkaç dakika sonra, test sonuçlarınızı görmek için **Yenile** ' ye tıklayın.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Ekran görüntüsü, Yenile düğmesinin vurgulandığı kullanılabilirlik sayfasını gösterir.":::

Dağınık terçiz görünümü, tanılama testi adım ayrıntıları olan test sonuçlarının örneklerini gösterir. Test altyapısı, hata içeren testler için tanılama ayrıntılarını depolar. Başarılı testlerde, yürütmelerin bir alt kümesi için tanılama ayrıntıları depolanır. Test, test adı ve konumu görmek için yeşil/kırmızı noktalardan herhangi birinin üzerine gelin.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Satır görünümü." border="false":::

Belirli bir testi veya konumu seçin ya da ilgilendiğiniz dönemle ilgili daha fazla sonuç görmek için zaman dilimini küçültün. Arama Gezgini’ni kullanarak tüm yürütmelerden alınan sonuçları görün veya Analytics sorgularını kullanarak bu veriler üzerinde özel raporlar çalıştırın.

## <a name="inspect-and-edit-tests"></a> Testleri inceleme ve düzenleme

Bir testi düzenlemek, geçici olarak devre dışı bırakmak veya silmek için, bir sınama adının yanındaki üç noktaya tıklayın. Değişiklik yapıldıktan sonra yapılandırma değişikliklerinin tüm test aracılarına yayılması 20 dakikaya kadar sürebilir.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Test ayrıntılarını görüntüleyin. Bir Web testini düzenleyin ve devre dışı bırakın." border="false":::

Hizmetinizde bakım gerçekleştirdiğiniz sırada kullanılabilirlik testlerini veya bunlarla ilişkili uyarıları devre dışı bırakmak isteyebilirsiniz.

## <a name="if-you-see-failures"></a>Hata görürseniz

Kırmızı bir nokta seçin.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Uçtan uca işlem ayrıntıları sekmesinin ekran görüntüsü." border="false":::

Bir kullanılabilirlik testi sonucundan, işlem ayrıntılarını tüm bileşenler arasında görebilirsiniz. Şunları yapabilirsiniz:

* Testinizin başarısız olmasına neden olabilecek ancak uygulamanız hala kullanılabilir olduğunu belirlemek için sorun giderme raporunu gözden geçirin.
* Sunucunuzdan alınan yanıtı denetleme.
* Başarısız kullanılabilirlik testi işlenirken toplanan bağıntılı sunucu tarafı telemetrisi ile hatayı tanılayın.
* Sorunu izlemek için git veya Azure Boards 'de bir sorun veya iş öğesi kaydedin. Hata, bu olayın bir bağlantısını içerir.
* Web testi sonucunu Visual Studio’da açın.

Uçtan uca işlem tanılama deneyimi hakkında daha fazla bilgi edinmek için, [işlem tanılama belgelerini](./transaction-diagnostics.md)ziyaret edin.

Yapay kullanılabilirlik testinin başarısız olmasına neden olan sunucu tarafı özel durumunun ayrıntılarını görmek için özel durum satırına tıklayın. Ayrıca, daha zengin kod düzeyi Tanılama için [hata ayıklama anlık görüntüsünü](./snapshot-debugger.md) alabilirsiniz.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Sunucu tarafı tanılama.":::

Ham sonuçlara ek olarak, [Ölçüm Gezgini](../essentials/metrics-getting-started.md)' de Iki temel kullanılabilirlik ölçümü de görebilirsiniz:

1. Kullanılabilirlik: Tüm test yürütmelerinde başarılı olan testlerin yüzdelik oranı.
2. Test Süresi: Tüm test yürütmelerinde ortalama test süresi.

## <a name="automation"></a>Otomasyon

* Otomatik olarak [kullanılabilirlik testi ayarlamak için PowerShell betiklerini kullanın](./powershell.md#add-an-availability-test).
* Bir uyarı ortaya çıktığında çağrılan bir [web kancası](../alerts/alerts-webhooks.md) ayarlayın.


## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılabilirlik uyarıları](availability-alerts.md)
* [Çok adımlı Web testleri](availability-multistep.md)
* [Sorun giderme](troubleshoot-availability.md)
