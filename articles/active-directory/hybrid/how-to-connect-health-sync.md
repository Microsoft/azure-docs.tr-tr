---
title: Eşitleme için Azure AD Connect Health'i kullanma| Microsoft Belgeleri
description: Bu Azure AD Connect Health sayfasında, Azure AD Connect eşitlemesinin nasıl izleneceği ele alınmıştır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e803614a02e76d179579a2258abd563b5c58e63a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016992"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Azure AD Connect eşitlemesini Azure AD Connect Health ile izleme
Aşağıdaki belgeler Azure AD Connect Health ile Azure AD Connect’in (Eşitleme) izlenmesine özgüdür.  Azure Connect Health ile AD FS'yi izleme hakkında bilgi almak için bkz. [Azure AD Connect Health'i AD FS ile kullanma](how-to-connect-health-adfs.md). Ayrıca, Azure AD Connect Health ile Active Directory Domain Services izleme hakkında bilgi için [AD DS ile Azure AD Connect Health kullanma](how-to-connect-health-adds.md)konusuna bakın.

![Eşitleme sayfasının Azure AD Connect Health ekran görüntüsü.](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Eşitleme için Azure AD Connect Health uyarıları
Eşitleme için Azure AD Connect Health Uyarıları bölümünde etkin uyarıların listesi sağlanmıştır. Her uyarı için ilgili bilgiler, çözüm adımları ve ilgili belgelere yönelik bağlantılar verilmiştir. Etkin veya çözümlenmiş bir uyarıyı seçtiğinizde, alarmı çözümlemek için uygulayabileceğiniz adımların ve ek belgelere yönelik bağlantıların yanı sıra ek bilgiler içeren yeni bir dikey pencere görürsünüz. Ayrıca, geçmişte çözümlenen uyarılara ilişkin geçmiş verileri de görüntüleyebilirsiniz.

Bir uyarıyı seçtiğinizde, uyarıyı çözümlemek için uygulayabileceğiniz adımlar ve ek belgelere yönelik bağlantıların yanı sıra ek bilgiler alırsınız.

![Azure AD Connect eşitleme hatası](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Sınırlı Uyarı Değerlendirmesi
Azure AD Connect, varsayılan yapılandırmayı (örneğin, Öznitelik Filtrelemesi için varsayılan yapılandırma özel bir yapılandırma olarak değiştirildiyse) KULLANMIYORSA Azure AD Connect Health aracısı, Azure AD Connect ile ilgili hata olaylarını karşıya yüklemez.

Bu, uyarıların değerlendirilmesini hizmete göre sınırlar. Bu koşulu hizmetinizdeki Azure Portal’da gösteren bir başlık görürsünüz.

![Uyarı başlığının, uyarı değerlendirmesinin sınırlı olduğunu belirten ekran görüntüsü. Tüm uyarıları etkinleştirmek için ayarlarınızı güncelleştirin.](./media/how-to-connect-health-sync/banner.png)

Bunu, "Ayarlar"a tıklayarak ve Azure AD Connect Health aracısının tüm hata günlüklerini karşıya yüklemesine izin vererek değiştirebilirsiniz.

![Çağrılan Ayarlar seçeneğinin ekran görüntüsü ve Kaydet seçeneğiyle ayarlar bölümü ve açık seçeneği.](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Eşitleme Öngörüsü
Yöneticiler, sık sık değişikliklerin Azure AD ile eşitlenmesi için gereken süreyi ve yapılan değişikliklerin miktarını öğrenmek ister. Bu özellik, aşağıdaki grafikler yoluyla bunların görselleştirilmesi için kolay bir yol sağlar:   

* Eşitleme işlemlerinin gecikme süresi
* Nesne Değişikliği eğilimi

### <a name="sync-latency"></a>Eşitleme Gecikme Süresi
Bu özellik, bağlayıcılara ilişkin eşitleme işlemlerinin (içeri aktarma, dışarı aktarma vb.) gecikme sürelerinin grafik eğilimini sağlar.  Bu, yalnızca işlemlerinizin gecikme süresini (çok sayıda değişikliğiniz varsa daha fazladır) anlamak için değil, aynı zamanda gecikme süresindeki daha fazla incelenmesi gerekebilecek anormalliklerin algılanması için de hızlı ve kolay bir yol sunar.

![Son 3 günden itibaren profil gecikme süresini Çalıştır grafının ekran görüntüsü.](./media/how-to-connect-health-sync/synclatency02.png)

Varsayılan olarak, Azure AD bağlayıcısı için yalnızca "Dışarı Aktarma" işleminin gecikme süresi gösterilir.  Bağlayıcı üzerinde gerçekleşen diğer işlemleri görmek veya diğer bağlayıcılara ilişkin işlemleri görüntülemek için grafik üzerinde sağ tıklayın, Grafiği Düzenle’yi seçin veya “Gecikme Süresi Grafiğini Düzenle” düğmesine tıklayın ve belirli bir işlemi ve bağlayıcıları seçin.

### <a name="sync-object-changes"></a>Eşitleme Nesnesi Değişiklikleri
Bu özellik, değerlendirilen ve Azure AD'ye aktarılan değişiklik sayısının grafik eğilimini sağlar.  Günümüzde bu bilgileri eşitleme günlüklerinden toplamak kolay değil.  Bu grafik sayesinde ortamınızda oluşan değişiklik sayısını izlemenin yanı sıra, oluşan hataların görsel görünümünü de daha basit bir şekilde izleyebilirsiniz.

![Son 3 günden itibaren Azure AD 'ye yönelik Ihracat Istatistiklerinin ekran görüntüsü.](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Nesne düzeyinde eşitleme hata raporu
Bu özellik, kimlik verileri Azure AD Connect kullanılarak Windows Server AD ile Azure AD arasında eşitlenirken oluşabilecek eşitleme hataları hakkında bir rapor sağlar.

* Rapor, eşitleme istemcisi tarafından kaydedilen hataları kapsar (Azure AD Connect 1.1.281.0 veya üzeri)
* Eşitleme altyapısındaki son eşitleme işlemi sırasında oluşan hataları içerir. (Azure AD Bağlayıcısı üzerinde “dışarı aktarma”.)
* Raporun en son verileri içermesi için eşitlemeye yönelik Azure AD Connect Health aracısının gerekli uç noktalara giden bağlantısının olması gerekir.
* Rapor, eşitleme için Azure AD Connect Health Aracısı tarafından karşıya yüklenen veriler kullanılarak **her 30 dakikada bir güncelleştirilir** . Aşağıdaki temel özellikleri sağlar

  * Hataların kategorilere ayrılması
  * Kategoriye göre hatalı nesnelerin listesi
  * Hatalarla ilgili tüm veriler tek bir yerde bulunur
  * Çakışma nedeniyle hata oluşan Nesnelerin yan yana karşılaştırılması
  * Hata raporunu CVS olarak indirin

### <a name="categorization-of-errors"></a>Hataların Kategorilere Ayrılması
Rapor, mevcut eşitleme hatalarını aşağıdaki kategorilere ayırır:

| Kategori | Açıklama |
| --- | --- |
| Yinelenen Öznitelik |Azure AD Connect, bir Kiracıda benzersiz olması gereken ve Azure AD Connect’te bulunan bir veya daha fazla özniteliğin (proxyAddresses ve UserPrincipalName gibi) yinelenen değerleri ile nesneler oluşturmaya veya güncelleştirmeye çalıştığında oluşan hatalar. |
| Veri Uyuşmazlığı |Geçici eşleştirme, nesneleri eşleştiremeyerek eşitleme hatalarına neden olduğunda görülen hatalar. |
| Veri Doğrulama Hatası |UserPrincipalName gibi kritik özniteliklerde desteklenmeyen karakterler bulunması ve Azure AD’ye yazılmadan önce doğrulanamayan biçim hataları gibi geçersiz verilerden kaynaklanan hatalar. |
| Federasyon Etki Alanı Değiştirme | Hesaplar farklı bir federasyon etki alanı kullandığında oluşan hatalar. |
| Büyük Öznitelik |Bir veya daha fazla öznitelik izin verilen boyuttan, uzunluktan veya sayıdan daha büyük olduğunda görülen hatalar. |
| Diğer |Yukarıdaki kategorilere uymayan diğer tüm hatalar. Bu kategori, geri bildirimleriniz temel alınarak alt kategorilere ayrılacaktır. |

![Eşitleme Hata Raporu Özeti](./media/how-to-connect-health-sync/errorreport01.png)
![Eşitleme Hata Raporu Kategorileri](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Kategoriye göre hatalı nesnelerin listesi
Her kategori ayrıntılı olarak incelendiğinde ilgili kategoride hata içeren nesnelerin bir listesi görülebilir.
![Eşitleme Hata Raporu Listesi](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Hata Ayrıntıları
Hataların ayrıntılı görünümünde aşağıdaki veriler bulunur

* Vurgulanmış çakışan öznitelik
* Dahil olan *AD Nesnesi* için tanımlayıcılar
* Dahil olan *Azure AD Nesnesi* tanımlayıcıları (geçerli olduğunda)
* Hata açıklaması ve düzeltme yolu

![Eşitleme Hata Raporu Ayrıntıları](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Hata raporunu CSV olarak indirme
"Dışarı aktar" düğmesini seçerek tüm hataların tüm ayrıntılarını içeren bir CSV dosyası indirebilirsiniz.

### <a name="diagnose-and-remediate-sync-errors"></a>Eşitleme hatalarını tanılama ve düzeltme 
Kullanıcı Kaynak Bağlantısı güncelleştirmesiyle ilgili yinelenen öznitelik eşitleme hatası senaryosunda düzeltmeyi doğrudan portalda gerçekleştirebilirsiniz. [Yinelenen öznitelik eşitleme hatalarını tanılama ve düzeltme](how-to-connect-health-diagnose-sync-errors.md) hakkında daha fazla bilgi edinin

## <a name="related-links"></a>İlgili bağlantılar
* [Eşitleme sırasında karşılaşılan Hataları giderme](tshoot-connect-sync-errors.md)
* [Yinelenen Öznitelik Dayanıklılığı](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Aracı yüklemesini Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health İşlemleri](how-to-connect-health-operations.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Azure AD Connect Health'i AD DS ile Kullanma](how-to-connect-health-adds.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health sürümü geçmişi](reference-connect-health-version-history.md)