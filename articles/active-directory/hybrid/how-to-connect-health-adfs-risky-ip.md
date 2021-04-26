---
title: AD FS riskli IP raporuyla Azure AD Connect Health | Microsoft Docs
description: Azure AD Connect Health AD FS riskli IP raporunu açıklar.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b76d2f943f254eb06208e2c190bae4d4088030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746079"
---
# <a name="risky-ip-report-public-preview"></a>Riskli IP raporu (Genel Önizleme)
AD FS müşteriler, son kullanıcıların Microsoft 365 gibi SaaS uygulamalarına erişmesi için kimlik doğrulama hizmetleri sağlamak üzere internet 'e parola kimlik doğrulama uç noktaları sunabilir. Bu durumda kötü bir aktör, bir son kullanıcı parolasını tahmin etmek ve uygulama kaynaklarına erişmek amacıyla AD FS sisteminize karşı oturum açma girişimlerinde bulunabilir. AD FS, Windows Server 2012 R2'de AD FS’den itibaren bu tür saldırıları önlemek için extranet hesap kilitleme işlevselliği sağlamaktadır. Daha düşük bir sürüm kullanıyorsanız, AD FS sisteminizi Windows Server 2016’ya yükseltmeniz kesinlikle önerilir. <br />

Ayrıca, tek bir IP adresinin birden fazla kullanıcıya karşı birden çok oturum açma girişiminde bulunması mümkündür. Böyle durumlarda kullanıcı başına deneme sayısı, AD FS’deki hesap kilitleme korumasına ilişkin eşiğin altında olabilir. Azure AD Connect Health artık bu durumu algılayıp durum gerçekleştiğinde yöneticileri bilgilendiren "Riskli IP raporu" özelliğini sağlamaktadır. Bu raporun başlıca yararları şunlardır: 
- Parola tabanlı başarısız girişimler eşiğini aşan IP adreslerini algılama
- Hatalı parola veya extranet kilitleme durumu nedeniyle başarısız olan oturum açma işlemlerini destekler
- Bu durum oluşur oluşmaz özelleştirilebilir e-posta ayarlarıyla yöneticileri uyarmak üzere e-posta bildirimi gönderme
- Bir kuruluşun güvenlik ilkesi ile eşleşen özelleştirilebilir eşik ayarları
- Otomasyon aracılığıyla çevrimdışı analiz ve diğer sistemlerle tümleştirme için indirilebilir raporlar

> [!NOTE]
> Bu raporu kullanmak için AD FS denetiminin etkin olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [AD FS için Denetimi Etkinleştirme](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Erişmek için önizleme, Genel Yönetici veya [Güvenlik Okuyucusu](../../role-based-access-control/built-in-roles.md#security-reader) izni gereklidir.  
>

## <a name="what-is-in-the-report"></a>Raporda ne var?
Başarısız oturum açma etkinliği istemci IP adresleri, Web uygulaması ara sunucuları aracılığıyla toplanır. Riskli IP raporundaki her bir öğe, belirlenmiş eşiği aşan başarısız AD FS oturum açma etkinlikleri hakkında toplu bilgiler gösterir. Aşağıdaki bilgileri sağlar: ![ sütun başlıkları vurgulanmış riskli BIR IP raporu gösteren ekran görüntüsü.](./media/how-to-connect-health-adfs/report4a.png)

| Rapor Öğesi | Description |
| ------- | ----------- |
| Zaman Damgası | Algılama zaman penceresi başladığında Azure portalı yerel saatini temel alan zaman damgasını gösterir.<br /> Tüm günlük olaylar UTC saat diliminde gece yarısı oluşturulur. <br />Saatlik olayların zaman damgası saat başına yuvarlanır. Birinci etkinlik başlangıç saatini dışarı aktarılan dosyadaki "firstAuditTimestamp" içinde bulabilirsiniz. |
| Tetikleyici Türü | Algılama zaman penceresinin türünü gösterir. Toplama tetikleyici türleri saat veya gün başınadır. Bu türler, yüksek sıklıktaki bir deneme yanılma saldırısı ile deneme sayısının gün geneline dağıtıldığı yavaş bir saldırı arasında karşılaştırmalı algılamaya yardımcı olur. |
| IP Adresi | Hatalı parola veya extranet kilitleme oturum açma etkinlikleri olan tek riskli IP adresi. Bu bir IPv4 veya IPv6 adresi olabilir. |
| Hatalı Parola Hata Sayısı | Algılama zaman penceresi boyunca IP adresinden kaynaklanan Hatalı Parola hatalarının sayısı. Hatalı Parola hataları belirli kullanıcılar için birden çok kez gerçekleşebilir. Buna süresi dolan parolalar nedeniyle başarısız olan denemelerin dahil olmadığına dikkat edin. |
| Extranet Kilitleme Hatası Sayısı | Algılama zaman penceresi boyunca IP adresinden kaynaklanan Extranet Kilitleme hatalarının sayısı. Extranet Kilitleme hataları belirli kullanıcılar için birden çok kez gerçekleşebilir. Bu durum yalnızca AD FS’de Extranet Kilitleme yapılandırılmışsa (sürüm 2012 R2 veya üstü) görülür. <b>Not</b> Parola kullanarak extranet oturumu açmaya izin veriyorsanız bu özelliği açmanız kesinlikle önerilir. |
| Deneme Yapan Benzersiz Kullanıcı Sayısı | Algılama zaman penceresi boyunca IP adresinden deneme yapan benzersiz kullanıcı hesaplarının sayısı. Tek ullanıcı saldırı deseni ile çoklu kullanıcı saldırı desenini birbirinden ayırt etmeye yönelik bir mekanizma sağlar.  |

Örneğin, aşağıdaki rapor öğesi 28.02.2018 tarihinde 18 ile 19 arasındaki saat penceresinde <i>104.2XX.2XX.9</i> IP adresinin parola hatası içermediğini ve 284 extranet kilitleme hatası içerdiğini gösterir. Ölçütler dahilinde 14 benzersiz kullanıcı etkilenmiştir. Etkinlik olayı, belirlenen saatlik rapor eşiğini aşmıştır. 

![Riskli bir IP rapor girişi örneği gösteren ekran görüntüsü.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Rapor listesinde yalnızca belirlenen eşiği aşan etkinlikler gösterilecektir. 
> - Bu rapor en fazla 30 gün geriye kadar izlenebilir.
> - Bu uyarı raporu, Exchange IP adreslerini veya özel IP adreslerini göstermez. Bunlar yine de dışarı aktarma listesine eklenir. 
>

!["Indirme", "bildirim ayarları" ve "eşik ayarları" vurgulanmış riskli IP raporunu gösteren ekran görüntüsü.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Listedeki yük dengeleyici IP adresleri
Yük dengeleyici, başarısız oturum açma etkinliklerini topladı ve uyarı eşiğine ulaştı. Yük dengeleyici IP adreslerini görüyorsanız, dış yük dengeleyicinizin Web Uygulaması Ara sunucusuna isteği geçirdiğinde istemci IP adresini göndermeme olasılığı yüksektir. Lütfen, iletme istemci IP adresini geçirmek için yük dengeleyicinizi doğru şekilde yapılandırın. 

## <a name="download-risky-ip-report"></a>Riskli IP raporu indir 
**İndirme** işlevi kullanılarak, son 30 gün içindeki tüm riskli IP adresi listesi Connect Health Portalından dışarı aktarılabilir. Dışarı aktarma sonucu, her bir algılama zaman penceresindeki tüm başarısız AD FS oturum açma girişimlerini içerir, böylece dışarı aktarma sonrasında filtrelemeyi özelleştirebilirsiniz. Dışarı aktarma sonucunda, portalda vurgulanan toplamaların yanı sıra her bir IP adresi için başarısız oturum açma etkinliklerine ilişkin daha fazla ayrıntı gösterilmektedir:

|  Rapor Öğesi  |  Description  | 
| ------- | ----------- | 
| firstAuditTimestamp | Algılama zaman penceresi sırasında başarısız etkinlikler başlatıldığında ilk zaman damgasını gösterir.  | 
| lastAuditTimestamp | Algılama zaman penceresi sırasında başarısız etkinlikler sonlandırıldığında son zaman damgasını gösterir.  | 
| attemptCountThresholdIsExceeded | Geçerli etkinlikler uyarı eşiğini aşıyorsa gösterilen bayrak.  | 
| isWhitelistedIpAddress | IP adresine uyarı ve raporlama filtresi uygulanmışsa gösterilen bayrak. Özel IP adresleri (<i>10.x.x.x, 172.x.x.x ve 192.168.x.x</i>) ile Exchange IP adresleri filtrelenir ve True olarak işaretlenir. Özel IP adresi aralıkları görüyorsanız, dış yük dengeleyicinizin Web Uygulaması Ara sunucusuna isteği geçirdiğinde istemci IP adresini göndermeme olasılığı yüksektir.  | 

## <a name="configure-notification-settings"></a>Bildirim ayarlarını yapılandırma
Raporun yönetim kişileri **Bildirim Ayarları** üzerinden güncelleştirilebilir. Varsayılan olarak, riskli IP uyarısı e-posta bildirimi kapalı durumdadır. “Başarısız etkinlik eşiği raporunu aşan IP adresleri için e-posta bildirimleri alın” altındaki düğmeyi açarak bildirimi etkinleştirebilir. Connect Health uygulamasındaki genel uyarı bildirimi ayarları gibi bu seçenek de riskli IP raporu hakkında belirlenmiş bildirim alıcısını özelleştirmenize olanak tanır. Ayrıca, değişikliği yaparken tüm genel yöneticilere bildirebilirsiniz. 

## <a name="configure-threshold-settings"></a>Eşik ayarlarını yapılandırma
Uyarı eşiği, Eşik Ayarları üzerinden güncelleştirilebilir. Başlangıç için, sistemin varsayılan olarak ayarlanmış bir eşiği vardır. Varsayılan değerler aşağıda verilmiştir. Riskli IP raporu eşik ayarları dört kategoriye ayrılır:

![Azure AD Connect Health Portalı](./media/how-to-connect-health-adfs/report4d.png)

| Eşik Öğesi | Description |
| --- | --- |
| (Hatalı U/P + Extranet Kilitleme) / Gün  | Hatalı Parola sayısı ile Extranet Kilitleme sayısının **gün** başına toplamı daha fazla olduğunda etkinliği bildirme ve uyarı bildirimini tetikleme eşiği ayarı. Varsayılan değer 100 ' dir.|
| (Hatalı U/P + Extranet Kilitleme) / Saat | Hatalı Parola sayısı ile Extranet Kilitleme sayısının **saat** başına toplamı daha fazla olduğunda etkinliği bildirme ve uyarı bildirimini tetikleme eşiği ayarı. Varsayılan değer 50 ' dir.|
| Extranet Kilitleme / Gün | **Gün** başına Extranet Kilitleme sayısı daha fazla olduğunda etkinliği bildirme ve uyarı bildirimini tetikleme eşiği ayarı. Varsayılan değer 50’dir.|
| Extranet Kilitleme / Saat| **Saat** başına Extranet Kilitleme sayısı daha fazla olduğunda etkinliği bildirme ve uyarı bildirimini tetikleme eşiği ayarı. Varsayılan değer 25 ' tir|

> [!NOTE]
> - Rapor eşiği değişikliği, ayar değişikliğinden bir saat sonra uygulanır. 
> - Bildirilmiş mevcut öğeler eşik değişikliğinden etkilenmez. 
> - Ortamınızda görülen olayların sayısının çözümlemeniz ve eşiği buna uygun şekilde ayarlamanız önerilir. 
>
>

## <a name="faq"></a>SSS
**Neden raporda özel IP adresi aralıkları görüyorum?**  <br />
Özel IP adresleri (<i>10. x. x. x, 172. x. x. x & 192.168. x. x</i>) Ile Exchange IP adresleri FILTRELENMIŞ ve IP onaylı listesinde doğru olarak işaretlenir. Özel IP adresi aralıkları görüyorsanız, dış yük dengeleyicinizin Web Uygulaması Ara sunucusuna isteği geçirdiğinde istemci IP adresini göndermeme olasılığı yüksektir.

**Neden raporda yük dengeleyici IP adreslerini görüyorum?**  <br />
Yük dengeleyici IP adreslerini görüyorsanız, dış yük dengeleyicinizin Web Uygulaması Ara sunucusuna isteği geçirdiğinde istemci IP adresini göndermeme olasılığı yüksektir. Lütfen, iletme istemci IP adresini geçirmek için yük dengeleyicinizi doğru şekilde yapılandırın. 

**IP adresini engellemek için ne yapmalıyım?**  <br />
Tanımlanmış kötü amaçlı IP adreslerini güvenlik duvarına eklemeniz veya Exchange’de engellemeniz gerekir.   <br />

**Neden bu raporda hiçbir öğe görmüyorum?** <br />
- Başarısız oturum açma etkinlikleri eşik ayarlarını aşmıyor.
- AD FS sunucu listenizde etkin bir "Sistem durumu hizmeti güncel değil" uyarısı olmadığından emin olun.  [Bu uyarıyla ilgili sorunları giderme](how-to-connect-health-data-freshness.md) hakkında daha fazla bilgi edinin.
- AD FS gruplarınde denetimler etkin değildir.

**Neden rapora erişim olmadığını görüyorum?**  <br />
Genel Yönetici veya [Güvenlik Okuyucusu](../../role-based-access-control/built-in-roles.md#security-reader) izni gereklidir. Erişim elde etmek için lütfen genel yöneticinize başvurun.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Aracı yüklemesini Azure AD Connect Health](how-to-connect-health-agent-install.md)
