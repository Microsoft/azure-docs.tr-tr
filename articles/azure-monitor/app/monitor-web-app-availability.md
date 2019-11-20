---
title: Web sitelerinin kullanılabilirlik ve yanıt hızını izleme | Microsoft Docs
description: Application Insights’ta web testleri ayarlayın. Web sitesi kullanılamaz duruma gelirse veya yavaş yanıt verirse uyarı alın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 074b5c175305131cd67cc6660d13756a83386c11
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819287"
---
# <a name="monitor-the-availability-of-any-website"></a>Herhangi bir Web sitesinin kullanılabilirliğini izleme

Web uygulamanızı/Web sitenizi dağıttıktan sonra, kullanılabilirliği ve yanıt hızını izlemek için yinelenen testler ayarlayabilirsiniz. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md), dünyanın her yerindeki noktalarından uygulamanıza düzenli aralıklarla web istekleri gönderir. Uygulamanız yanıt vermiyorsa veya çok yavaş yanıt verirse sizi uyarır.

Genel İnternet'ten erişilebilen herhangi bir HTTP veya HTTPS uç noktası için kullanılabilirlik testleri ayarlayabilirsiniz. Test ettiğiniz web sitesinde herhangi bir değişiklik yapmanız gerekmez. Aslında, sahip olduğunuz bir site bile olması gerekmez. Hizmetinizin bağlı olduğu REST API kullanılabilirliğini test edebilirsiniz.

### <a name="types-of-availability-tests"></a>Kullanılabilirlik testlerinin türleri:

Üç tür kullanılabilirlik testi vardır:

* [URL ping testi](#create-a-url-ping-test): Azure portalında oluşturabileceğiniz basit bir test.
* [Çok adımlı Web testi](availability-multistep.md): daha karmaşık senaryoları test etmek için oynatılabilecek Web istekleri dizisinin bir kaydı. Çok adımlı Web testleri Visual Studio Enterprise oluşturulur ve yürütülmek üzere portala yüklenir.
* [Özel Izleme kullanılabilirlik testleri](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): kullanılabilirlik testlerini çalıştırmak için özel bir uygulama oluşturmaya karar verirseniz, sonuçları Application Insights göndermek için `TrackAvailability()` yöntemi kullanılabilir.

**Application Insights kaynak başına en çok 100 kullanılabilirlik testi oluşturabilirsiniz.**

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Bir kullanılabilirlik testi oluşturmak için önce bir Application Insights kaynağı oluşturmanız gerekir. Zaten bir kaynak oluşturduysanız, [URL ping testi oluşturmak](#create-a-url-ping-test)için sonraki bölüme ilerleyin.

Azure portal > **Geliştirici Araçları** > **kaynak oluştur** **' u** seçin ve [Application Insights bir kaynak oluşturun](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>URL ping testi oluşturma

"URL ping test" adı, bir hatalı Nomer 'nin bir bitidir. Bu sınama, sayfanın kullanılabilirliğini denetlemek için ıCMP (Internet Denetim Iletisi Protokolü) kullanımını açık hale getirmez. Bunun yerine, bir uç noktanın yanıt verip vermediğini doğrulamak için daha gelişmiş HTTP isteği işlevselliği kullanır. Ayrıca, bu Yanıtla ilişkili performansı ölçer ve bağımlı istekleri ayrıştırma ve yeniden denemeye izin verme gibi daha gelişmiş özelliklerle birlikte özel başarı ölçütlerini ayarlama yeteneğini ekler.

İlk kullanılabilirlik isteğinizi oluşturmak için kullanılabilirlik bölmesini açın ve **Test oluştur**' u seçin.

![En azından web sitenizin URL'sini doldurma](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Test oluşturma

|Ayar| Açıklama
|----|----|----|
|**URL** |  URL, test etmek istediğiniz herhangi bir Web sayfası olabilir, ancak genel İnternet 'ten görünür olmalıdır. URL bir sorgu dizesi içerebilir. Bu nedenle, örneğin, veritabanınızla biraz alıştırma yapabilirsiniz. URL yeniden yönlendirme adresine çözümlenirse, en fazla 10 yeniden yönlendirmeyi izleriz.|
|**Bağımlı istekleri Ayrıştır**| Test istekleri görüntüleri, betikleri, stil dosyaları ve test edilen Web sayfasının parçası olan diğer dosyalar. Kayıtlı yanıt süresi, bu dosyaları almak için geçen süreyi içerir. Bu kaynaklardan herhangi biri, tüm test için zaman aşımı süresi içinde başarıyla indirilemezse test başarısız olur. Seçenek işaretlenmezse, test yalnızca belirttiğiniz URL’deki dosyayı ister. Bu seçeneğin etkinleştirilmesi, daha sıkı bir denetim elde ediyor. Test, siteye el ile gözatarken dikkat çekici olmayan durumlar için başarısız olabilir.
|**Yeniden denemeleri etkinleştir**|test başarısız olduğunda, kısa bir aralıktan sonra yeniden denenir. Art arda üç deneme başarısız olursa bir hata bildirilir. Sonraki testler bundan sonra her zamanki test sıklığında gerçekleştirilir. Bir sonraki başarılı olana kadar yeniden deneme geçici olarak askıya alınır. Bu kural her test konuma bağımsız olarak uygulanır. **Bu seçeneği öneririz**. Ortalama olarak hataların yaklaşık %80’i yeniden deneme sırasında kaybolur.|
|**Sınama sıklığı**| Testin her test konumundan ne sıklıkla çalıştırılacağını ayarlar. Beş dakikalık varsayılan sıklıkta ve beş test konumuyla, siteniz ortalama olarak dakikada bir test edilir.|
|**Test konumları**| , Sunucularımızın URL 'nize Web istekleri gönderdiğimiz yerdir. Web sitenizdeki sorunları ağ sorunlarından ayırabilmeniz için **en az önerilen test konumu sayısı beş ' dir** . En fazla 16 konum seçebilirsiniz.

**URL 'niz genel İnternet 'ten görünmüyorsa, yalnızca test işlemlerine izin vermek için güvenlik duvarınızı seçmeli olarak açmayı seçebilirsiniz**. Kullanılabilirlik testi aracılarımız için güvenlik duvarı özel durumları hakkında daha fazla bilgi edinmek için [IP adresi kılavuzuna](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)bakın.

> [!NOTE]
> **En az beş konum**içeren birden çok konumdan test etmenizi önemle öneririz. Bu, belirli bir konum ile geçici sorunlardan kaynaklanan yanlış alarmları önlemektir. Ayrıca en iyi yapılandırmanın, **Test konumları sayısının, uyarı konum eşiğine + 2 ' ye eşit**olduğunu bulduk.

### <a name="success-criteria"></a>Başarı ölçütleri

|Ayar| Açıklama
|----|----|----|
| **Test zaman aşımı** |Yavaş yanıtlar hakkında uyarı almak için bu değeri azaltın. Yanıtlar sitenizden bu süre içinde alınmadıysa test başarısız sayılır. **Bağımlı istekleri ayrıştır**’ı seçtiyseniz; tüm görüntüler, stil dosyaları, betikler ve diğer bağımlı kaynaklar bu süre içinde alınmış olmalıdır.|
| **HTTP yanıtı** | Başarılı olarak sayılan döndürülen durum kodu. 200, normal web sayfası döndürüldüğünü belirten koddur.|
| **İçerik eşleşmesi** | "Hoş geldiniz!" gibi bir dize Her yanıtta büyük küçük harfe duyarlı bir tam eşleşme oluştuğunu test edebiliriz. Joker karakter bulunmayan düz bir dize olmalıdır. Sayfanızın içeriği değişirse bunu güncelleştirmeniz gerektiğini unutmayın. **İçerik eşleşmesi ile yalnızca Ingilizce karakterler desteklenir** |

### <a name="alerts"></a>Uyarılar

|Ayar| Açıklama
|----|----|----|
|**Neredeyse gerçek zamanlı (Önizleme)** | Neredeyse gerçek zamanlı uyarıların kullanılması önerilir. Bu tür bir uyarının yapılandırılması, kullanılabilirlik testiniz oluşturulduktan sonra yapılır.  |
|**Klasik** | Artık yeni kullanılabilirlik testleri için klasik uyarıların kullanılması önerilmez.|
|**Uyarı konum eşiği**|En az 3/5 konum önerilir. Uyarı konumu eşiği ve test konumlarının sayısı arasındaki en iyi ilişki,**en az beş test konumu ile, test konumu sayısı-2**  =  **Uyarı konum eşiği** ' dir.|

## <a name="see-your-availability-test-results"></a>Kullanılabilirlik testi sonuçlarınızı görme

Kullanılabilirlik testi sonuçları hem çizgi hem de dağılım çizim görünümleriyle görselleştirilebilir.

Birkaç dakika sonra, test sonuçlarınızı görmek için **Yenile** ' ye tıklayın.

![Satır görünümü](./media/monitor-web-app-availability/availability-refresh-002.png)

Dağınık terçiz görünümü, tanılama testi adım ayrıntıları olan test sonuçlarının örneklerini gösterir. Test altyapısı, hata içeren testler için tanılama ayrıntılarını depolar. Başarılı testlerde, yürütmelerin bir alt kümesi için tanılama ayrıntıları depolanır. Test, test adı ve konumu görmek için yeşil/kırmızı noktalardan herhangi birinin üzerine gelin.

![Satır görünümü](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Belirli bir testi veya konumu seçin ya da ilgilendiğiniz dönemle ilgili daha fazla sonuç görmek için zaman dilimini küçültün. Arama Gezgini’ni kullanarak tüm yürütmelerden alınan sonuçları görün veya Analytics sorgularını kullanarak bu veriler üzerinde özel raporlar çalıştırın.

## <a name="inspect-and-edit-tests"></a>Testleri İnceleme ve düzenleme

Bir testi düzenlemek, geçici olarak devre dışı bırakmak veya silmek için, bir sınama adının yanındaki üç noktaya tıklayın. Değişiklik yapıldıktan sonra yapılandırma değişikliklerinin tüm test aracılarına yayılması 20 dakikaya kadar sürebilir.

![Test ayrıntılarını görüntüleyin. Web testini düzenleme ve devre dışı bırakma](./media/monitor-web-app-availability/edit.png)

Hizmetinizde bakım gerçekleştirdiğiniz sırada kullanılabilirlik testlerini veya bunlarla ilişkili uyarıları devre dışı bırakmak isteyebilirsiniz.

## <a name="if-you-see-failures"></a>Hata görürseniz

Kırmızı noktaya tıklayın.

![Kırmızı noktaya tıklama](./media/monitor-web-app-availability/open-instance-3.png)

Bir kullanılabilirlik testi sonucundan, işlem ayrıntılarını tüm bileşenler arasında görebilirsiniz. Şunları yapabilirsiniz:

* Sunucunuzdan alınan yanıtı denetleme.
* Başarısız kullanılabilirlik testi işlenirken toplanan bağıntılı sunucu tarafı telemetrisi ile hatayı tanılayın.
* Sorunu izlemek için git veya Azure Boards 'de bir sorun veya iş öğesi kaydedin. Hata, bu olayın bir bağlantısını içerir.
* Web testi sonucunu Visual Studio’da açın.

Uçtan uca işlem [Tanılama deneyimi hakkında](../../azure-monitor/app/transaction-diagnostics.md)daha fazla bilgi edinin.

Yapay kullanılabilirlik testinin başarısız olmasına neden olan sunucu tarafı özel durumunun ayrıntılarını görmek için özel durum satırına tıklayın. Ayrıca, daha zengin kod düzeyi Tanılama için [hata ayıklama anlık görüntüsünü](../../azure-monitor/app/snapshot-debugger.md) alabilirsiniz.

![Sunucu tarafı tanılama](./media/monitor-web-app-availability/open-instance-4.png)

Ham sonuçlara ek olarak, [Ölçüm Gezgini](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)' de Iki temel kullanılabilirlik ölçümü de görebilirsiniz:

1. Kullanılabilirlik: Tüm test yürütmelerinde başarılı olan testlerin yüzdelik oranı.
2. Test Süresi: Tüm test yürütmelerinde ortalama test süresi.

## <a name="automation"></a>Otomasyon

* Otomatik olarak [kullanılabilirlik testi ayarlamak için PowerShell betiklerini kullanın](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Bir uyarı ortaya çıktığında çağrılan bir [web kancası](../../azure-monitor/platform/alerts-webhooks.md) ayarlayın.

## <a name="troubleshooting"></a>Sorun giderme

Adanmış [sorun giderme makalesi](troubleshoot-availability.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılabilirlik uyarıları](availability-alerts.md)
* [Çok adımlı Web testleri](availability-multistep.md)


