---
title: Windows sanal masaüstü sözlüğünü izleme-Azure
description: Windows sanal masaüstü için Azure Izleyici ile ilgili hüküm ve kavramların sözlüğü.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 8ed4c2cffffca79ea545358b8ad639118e87e013
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448211"
---
# <a name="azure-monitor-for-windows-virtual-desktop-glossary"></a>Windows sanal masaüstü sözlüğü için Azure Izleyici

Bu makalede, Windows sanal masaüstü için Azure Izleyici (Önizleme) ile ilgili temel hüküm ve kavramlar listelenmektedir ve kısaca açıklanmaktadır.

## <a name="alerts"></a>Uyarılar

Abonelikte yapılandırdığınız ve [önem derecesi 0](#severity-0-alerts) olarak sınıflandırdığınız etkin Azure Izleyici uyarıları Genel Bakış sayfasında görünür. Uyarıları ayarlamayı öğrenmek için bkz. [Azure Izleyici uyarıları ile olaylara yanıt verme](../azure-monitor/alerts/tutorial-response.md).

## <a name="available-sessions"></a>Kullanılabilir oturumlar

Kullanılabilir Oturumlar, konak havuzundaki kullanılabilir oturumların sayısını gösterir. Bu sayı, sanal makine başına izin verilen en fazla oturum sayısı ile sanal makine (VM) sayısını çarparak toplam oturumu çıkararak bu sayıyı hesaplar.

## <a name="connection-success"></a>Bağlantı başarılı

Bu öğe, bağlantı durumunu gösterir. "Bağlantı başarısı", bağlantının konağa, sanal makinedeki yığın tarafından onaylanarak ulaşabilme anlamına gelir. Başarısız bağlantı, bağlantının konağa ulaşamamasıdır.

## <a name="daily-active-users-dau"></a>Günlük etkin kullanıcılar (DUU)

Son 24 saat içinde bir oturum başlatan Toplam Kullanıcı sayısı.

## <a name="daily-alerts"></a>Günlük uyarılar

Her gün tetiklenen toplam uyarı sayısı.

## <a name="daily-connections-and-reconnections"></a>Günlük bağlantılar ve yeniden bağlantılar

Son 24 saat içinde başlatılan veya tamamlanan toplam bağlantı sayısı ve yeniden bağlantı sayısı.

## <a name="daily-connected-hours"></a>Günlük bağlı saatler

Son 24 saat içindeki kullanıcılar arasında bir oturuma bağlı olarak harcanan toplam saat sayısı.

## <a name="diagnostics-and-errors"></a>Tanılama ve hatalar

Windows sanal masaüstü için Azure Izleyici 'de bir hata veya uyarı göründüğünde, üç şey tarafından kategorilere ayrılır:

- Etkinlik türü: Bu kategori, hatanın Windows sanal masaüstü tanılama tarafından nasıl sınıflandırıldığından yapılır. Kategoriler yönetim etkinlikleri, akışlar, bağlantılar, ana bilgisayar kayıtları, hatalar ve kontrol noktaları. Bu Kategoriler hakkında daha fazla bilgi [için tanılama özelliği Log Analytics kullanın](diagnostics-log-analytics.md).

- Tür: Bu kategori, hatanın konumunu gösterir. 

     - Windows sanal masaüstü hizmetinde "hizmet" veya "ServiceError = TRUE" olarak işaretlenen hatalar oluştu.
     - "Dağıtım" veya etiketli "ServiceError = FALSE" olarak işaretlenen hatalar Windows sanal masaüstü hizmetinin dışında gerçekleşti.
     - ServiceError etiketi hakkında daha fazla bilgi edinmek için bkz. [Genel hata senaryoları](diagnostics-role-service.md#common-error-scenarios).

- Kaynak: Bu kategori, hatanın nerede oluştuğunu daha belirgin bir açıklama sağlar.

     - Tanılama: kullanıcıların dağıtım sorunlarını gözlemlemek ve tanılamak için izleme ve raporlama hizmeti etkinliğinden sorumlu hizmet rolü.

     - RDBroker: dağıtım etkinliklerini düzenleme, nesnelerin durumunu koruma, kimlik doğrulamasını doğrulama ve daha fazlasını yapmaktan sorumlu hizmet rolü.

     - RDGateway: son kullanıcılar ve sanal makineler arasındaki ağ bağlantısını işlemekten sorumlu hizmet rolü.

     - RDStack: Windows sanal masaüstü hizmetiyle iletişim kurmasına izin vermek için sanal makinelerinize yüklenmiş bir yazılım bileşeni.

     - İstemci: Windows sanal masaüstü hizmetine arabirimi sağlayan Son Kullanıcı makinesinde çalışan yazılım. Bir seçim yaptıktan sonra yayımlanan kaynakların listesini görüntüler ve uzak masaüstü bağlantısını barındırır.

Her bir tanılama sorunu veya hatası, neyin yanlış olduğunu açıklayan bir ileti içerir. Sorun giderme hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü sorunlarını belirlemek ve tanılamak](diagnostics-role-service.md).

## <a name="input-delay"></a>Giriş gecikmesi

Windows sanal masaüstü için Azure Izleyici 'de "giriş gecikmesi", her oturum için işlem performans sayacı başına giriş gecikmesi anlamına gelir. [Aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)adresindeki konak performansı sayfasında, bu performans sayacı, her 30 saniyede bir raporu hizmete gönderecek şekilde yapılandırılmıştır. Bu 30 saniyelik aralıklar "örnekler" olarak adlandırılır ve bu pencerede en kötü durumu raporlar. Ortanca ve P95 değerleri, tüm örneklerde medyan ve 95. yüzdebirlik değerini yansıtır.

**Ana bilgisayara göre giriş gecikmesi** altında, sayfadaki diğer tüm görselleri bu konağa filtrelemek için bir oturum ana bilgisayar satırı seçebilirsiniz. Ayrıca, zaman içinde ortanca giriş gecikmesini filtrelemek için bir işlem adı seçebilirsiniz.

Aşağıdaki kategorilerde gecikmeler yaptık:

- İyi: 150 milisaniyede bir süre sonra.
- Kabul edilebilir: 150-500 milisaniyesi.
- Zayıf: 500-2000 milisaniye (2 saniyenin altında).
- Hatalı: 2.000 milisaniyelik (2 saniye ve üzeri).

Giriş gecikmesi sayacının nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı girişi gecikmesi performans sayaçları](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Aylık etkin kullanıcılar (MAU)

Son 28 gün içinde bir oturum başlatan Toplam Kullanıcı sayısı. Verileri 30 gün veya daha kısa bir süre içinde depolurseniz, 28 günden daha az veri kullanılabilir olduğunda, dönem boyunca beklenen en düşük MAU ve bağlantı değerlerini görebilirsiniz.

## <a name="performance-counters"></a>Performans sayaçları

Performans sayaçları, donanım bileşenlerinin, işletim sistemlerinin ve uygulamaların performansını gösterir.

Aşağıdaki tabloda, Azure Izleyicisinin Windows sanal masaüstü için kullandığı önerilen performans sayaçları ve zaman aralıkları listelenmektedir:

|Performans sayacı adı|Zaman aralığı|
|---|---|
|Mantıksal disk (C:) \\ Ort. disk kuyruğu uzunluğu|30 saniye|
|Mantıksal disk (C:) \\ Ortalama Disk sn/Aktarım|60 saniye|
|Mantıksal disk (C:) \\ geçerli disk kuyruğu uzunluğu|30 saniye|
|Bellek ( \* ) \\ kullanılabilir MBayt|30 saniye|
|Bellek ( \* ) \\ sayfa hatası/sn|30 saniye|
|Bellek ( \* ) \\ Sayfa/sn|30 saniye|
|Bellek ( \* ) \\ % kullanılan kaydedilmiş bayt|30 saniye|
|\*FizikselDisk () \\ Ort. disk kuyruğu uzunluğu|30 saniye|
|\*FizikselDisk () \\ Ortalama Disk sn/okuma|30 saniye|
|\*FizikselDisk () \\ Ortalama Disk sn/Aktarım|30 saniye|
|\*FizikselDisk () \\ Ortalama Disk sn/yazma|30 saniye|
|İşlemci bilgileri (_Total) \\ % Işlemci zamanı|30 saniye|
|Terminal Hizmetleri ( \* ) \\ etkin oturumlar|60 saniye|
|Terminal Hizmetleri ( \* ) \\ etkin olmayan oturumlar|60 saniye|
|Terminal Hizmetleri ( \* ) \\ Toplam oturum sayısı|60 saniye|
|\*Işlem başına kullanıcı girişi gecikmesi ( \* ) \\ en fazla giriş gecikmesi|30 saniye|
|\*Oturum başına kullanıcı girişi gecikmesi ( \* ) \\ en fazla giriş gecikmesi|30 saniye|
|RemoteFX ağı ( \* ) \\ GEÇERLI TCP RTT|30 saniye|
|RemoteFX ağı ( \* ) \\ geçerli UDP bant genişliği|30 saniye|

Performans sayaçlarını okuma hakkında daha fazla bilgi için bkz. [performans sayaçlarını yapılandırma](../azure-monitor/agents/data-sources-performance-counters.md).

Giriş gecikmesi performans sayaçları hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı girişi gecikmesi performans sayaçları](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Olası bağlantı sorunları

Potansiyel bağlantı sorunları, yüksek bağlantı hatası oranı olan Konakları, kullanıcıları, yayımlanan kaynakları ve istemcileri gösterir. "Raporla" filtresini seçtiğinizde, bu sütunlardaki değerleri denetleyerek sorunun önem derecesini değerlendirebilirsiniz:

- Denemeler (bağlantı denemesi sayısı)
- Kaynaklar (yayımlanan uygulama veya Masaüstü sayısı)
- Konaklar (VM sayısı)
- İstemciler

Örneğin, **Kullanıcı** filtresini seçerseniz, **denemeler** sütunundaki her bir kullanıcının bağlantı denemelerini görmeyi denetleyebilirsiniz.

Bir bağlantı sorununun birden çok ana bilgisayar, Kullanıcı, kaynak veya istemciye yayıldığını fark ederseniz, sorun sistemin tamamını etkilemektedir. Değilse, daha düşük öncelikli bir sorundur.

Ek bilgileri görüntülemek için de girdileri seçebilirsiniz. Sorunla hangi konaklara, kaynaklara ve istemci sürümlerine dahil olduğunu görüntüleyebilirsiniz. Bu ekranda, bağlantı girişimleri sırasında bildirilen hatalar da gösterilir.

## <a name="round-trip-time-rtt"></a>Gidiş dönüş süresi (RTT)

Gidiş dönüş süresi (RTT), bağlantının, son kullanıcının konumu ile oturum ana bilgisayarının Azure bölgesi arasındaki gidiş dönüş süresinin tahminidir. Hangi konumların en iyi gecikme süresine sahip olduğunu görmek için, [Windows sanal masaüstü deneyimi Estimator aracında](https://azure.microsoft.com/services/virtual-desktop/assessment/)istediğiniz konumu bulun.

## <a name="session-history"></a>Oturum geçmişi

**Oturumlar** öğesi, bağlı ve bağlantısı kesilen tüm oturumların durumunu gösterir. **Boştaki oturumlar** yalnızca bağlantısı kesilen oturumları gösterir.

## <a name="severity-0-alerts"></a>Önem derecesi 0 uyarıları

Hemen yapmanız gereken en acil öğeler. Bu sorunları ele mazsanız, Windows sanal masaüstü dağıtımınızın çalışmayı durdurmasına neden olabilir.

## <a name="time-to-connect"></a>Bağlanma zamanı

Bağlanma zamanı, bir kullanıcının oturumunu başlattığı ve hizmette oturum açtıkları zaman olarak sayıldıkları süredir. Yeni bağlantıların oluşturulması, mevcut bağlantıların yeniden hazırlanmasından daha uzun sürme eğilimi gösterir.

## <a name="user-report"></a>Kullanıcı raporu

Kullanıcı raporu sayfası, belirli bir kullanıcının bağlantı geçmişini ve tanılama bilgilerini görüntülemenize olanak sağlar. Her kullanıcı raporu kullanım düzenlerini, Kullanıcı geri bildirimini ve kullanıcıların oturumları sırasında karşılaştığı hataları gösterir. En küçük sorunlar, Kullanıcı geri bildirimlerine göre çözülebilir. Daha derin bir bilgiye ihtiyacınız varsa, belirli bir bağlantı KIMLIĞI veya zaman dilimi hakkındaki bilgileri de filtreleyebilirsiniz.

## <a name="users-per-core"></a>Çekirdek başına Kullanıcı sayısı

Bu, her bir sanal makine çekirdeğinizdeki kullanıcı sayısıdır. Zamana göre çekirdek başına en fazla kullanıcı sayısını izlemek, ortamın sürekli olarak yüksek, düşük veya bir çekirdek başına kaç kullanıcı sayısına göre çalıştığını belirlemenize yardımcı olabilir. Kaç kullanıcının etkin olduğunu bilmek, ortamı verimli bir şekilde sağlamanıza ve ölçeklendirmenize yardımcı olur.

## <a name="windows-event-logs"></a>Windows Olay Günlükleri

Windows olay günlükleri, Windows sanal makinelerinde Log Analytics aracıları tarafından toplanan veri kaynaklarıdır. Sistem ve uygulama gibi standart günlüklerdeki olayları, izlemeniz gereken uygulamalar tarafından oluşturulan özel günlükleri de toplayabilirsiniz.

Aşağıdaki tabloda, Windows sanal masaüstü için Azure Izleyici için gerekli Windows olay günlükleri listelenmektedir:

|Olay adı|Olay türü|
|---|---|
|Uygulama|Hata ve uyarı|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Hata, uyarı ve bilgi|
|Microsoft-Windows-TerminalServices-LocalSessionManager/operasyonel|Hata, uyarı ve bilgi|
|Sistem|Hata ve uyarı|
| Microsoft-FSLogix-Apps/Işletimsel|Hata, uyarı ve bilgi|
|Microsoft-FSLogix-uygulamalar/yönetici|Hata, uyarı ve bilgi|

Windows olay günlükleri hakkında daha fazla bilgi için bkz. [Windows olay kayıtları özellikleri](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [dağıtımınızı izlemek Için Windows sanal masaüstü Için Azure izleyici 'Yi kullanma](azure-monitor.md).
- Veri depolama maliyetlerinizi tahmin etmek, ölçmek ve yönetmek için bkz. [Azure izleyici maliyetlerini tahmin](azure-monitor-costs.md)etme.
- Bir sorunla karşılaşırsanız, yardım ve bilinen sorunlar için [sorun giderme kılavuzumuzu](troubleshoot-azure-monitor.md) inceleyin.


Azure Advisor 'ı, yaygın sorunların nasıl çözümleneceğini veya engellenmesini belirlemenize yardımcı olmak için de ayarlayabilirsiniz. [Azure Advisor 'ı Windows sanal masaüstü Ile kullanma](azure-advisor.md)hakkında daha fazla bilgi edinin.

Yardıma ihtiyacınız varsa veya sorularınız varsa topluluk kaynaklarımıza göz atın:

- [Windows sanal masaüstü teknik topluluğu](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)'nda soru sorun veya topluluk önerileri oluşturun.
   
- Geri bildirimleri nasıl bırakacağınızı öğrenmek için bkz. [sorun giderme genel bakış, geri bildirim ve Windows sanal masaüstü desteği](troubleshoot-set-up-overview.md#report-issues).

- Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) Windows sanal masaüstü için geri bildirimde bulunda bırakabilirsiniz
