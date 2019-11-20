---
title: Azure operasyonel güvenlik denetim listesi | Microsoft Docs
description: Bu makalede, Azure operasyonel güvenlik için bir dizi denetim listesi sunulmaktadır.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 05dea50daabb37fbaa38c29e9267e7ff06e27a2e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827212"
---
# <a name="azure-operational-security-checklist"></a>Azure operasyonel güvenlik denetim listesi
Azure 'da uygulama dağıtmak hızlı, kolay ve ekonomik bir işlemdir. Üretim ortamında bulut uygulamasını dağıtmaya başlamadan önce, uygulamanızı değerlendirmek için gereken önemli ve önerilen İşletimsel Güvenlik eylemleri listesine karşı değerlendirmede size yardımcı olacak bir denetim listesi sağlamak yararlı olur.

## <a name="introduction"></a>Giriş

Azure, uygulamalarınızı dağıtmak için kullanabileceğiniz bir altyapı hizmetleri paketi sağlar. Azure operasyonel güvenlik, kullanıcıların verilerini, uygulamalarını ve diğer varlıklarını Microsoft Azure korumak için kullanıcılara sunulan hizmetleri, denetimleri ve özellikleri ifade eder.

-   Bulut platformundan en yüksek avantajdan yararlanmak için Azure hizmetlerinden yararlanmanızı ve denetim listesini izlemenizi öneririz.
-   Süresi ve kaynakları yatırım yapan kuruluşlar, başlamadan önce uygulamalarının işlemsel hazırlığını değerlendirmesine göre çok daha yüksek bir memnuniyet kazanmadan daha yüksektir. Bu çalışmayı gerçekleştirirken, denetim listeleri uygulamaların tutarlı ve holistik olarak değerlendirildiğinden emin olmak için değerli bir mekanizma olabilir.
-   İşletimsel değerlendirme düzeyi, kuruluşun bulut vade düzeyine ve uygulamanın geliştirme aşamasına, kullanılabilirlik ihtiyaçlarına ve veri duyarlılığı gereksinimlerine bağlı olarak farklılık gösterir.

## <a name="checklist"></a>Denetim Listesi

Bu denetim listesi, kuruluşların Azure 'da gelişmiş kurumsal uygulamalar dağıttıkları gibi çeşitli işlemsel güvenlik konuları konusunda dolaşmasına yardımcı olmak için tasarlanmıştır. Kuruluşunuz için güvenli bir bulut geçişi ve işlem stratejisi oluşturmanıza yardımcı olması için de kullanılabilir.

|Denetim listesi kategorisi| Açıklama|
| ------------ | -------- |
| [<br>güvenlik rollerini & erişim denetimleri](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izin atamak için kullanılan kullanıcıya özel sağlamak üzere [rol tabanlı erişim denetimi 'ni (RBAC)](../../role-based-access-control/role-assignments-portal.md) kullanın.</li></ul> |
| [<br>veri toplama & depolaması](../../storage/common/storage-security-guide.md)|<ul><li>[Rol tabanlı Access Control (RBAC)](../../role-based-access-control/role-assignments-portal.md)kullanarak depolama hesabınızı güvenli hale getirmek Için yönetim düzlemi güvenliğini kullanın.</li><li>[Paylaşılan erişim imzaları (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) ve depolanan erişim Ilkelerini kullanarak verilerinize erişimi güvenli hale getirmek Için veri düzlemi güvenliği.</li><li>[Azure dosya paylaşımları](../../storage/files/storage-dotnet-how-to-use-files.md)için https ve [SMB (sunucu ileti bloğu protokolleri) 3,0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) tarafından kullanılan şifrelemeyi kullanarak aktarım düzeyi şifrelemeyi kullanın.</li><li>Şifreleme anahtarlarının tek bir denetimini istediğinizde depolama hesaplarına gönderilen verilerin güvenliğini sağlamak için [istemci tarafı şifrelemeyi](../../storage/common/storage-client-side-encryption.md) kullanın. </li><li>Azure depolama 'daki verileri otomatik olarak şifrelemek için [depolama hizmeti şifrelemesi (SSE)](../../storage/common/storage-service-encryption.md) ve işletim sistemi ve veri diskleri için sanal makine disk dosyalarını şifrelemek üzere [Azure disk şifrelemesi](../azure-security-disk-encryption-overview.md) kullanın.</li><li>Yetkilendirme türünü izlemek için Azure [depolama Analizi](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) kullanın; BLOB depolama ile olduğu gibi, kullanıcıların bir paylaşılan erişim Imzası veya depolama hesabı anahtarları kullanmışsa de görebilirsiniz.</li><li>Farklı etki alanlarından depolama kaynaklarına erişmek için [çıkış noktaları arası kaynak paylaşımı (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) kullanın.</li></ul> |
|[<br>güvenlik Ilkeleri & önerileri](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Uç nokta çözümlerini dağıtmak için [Azure Güvenlik Merkezi](../../security-center/security-center-install-endpoint-protection.md) 'ni kullanın.</li><li>Web uygulamalarını güvenli hale getirmek için bir [Web uygulaması güvenlik duvarı (WAF)](../../application-gateway/waf-overview.md) ekleyin.</li><li>   Güvenlik korumalarını artırmak için bir Microsoft iş ortağından [güvenlik duvarı](../../sentinel/connect-data-sources.md) kullanın. </li><li>Azure aboneliğiniz için güvenlik iletişim bilgilerini uygulayın; Bu [Microsoft Güvenlik Yanıt Merkezi (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) , Müşteri verilerinize izinsiz veya yetkisiz bir şahıs tarafından erişildiğini belirlerse sizi iletişim kurar.</li></ul> |
| [<br>Kimlik ve Erişim Yönetimi](identity-management-best-practices.md)|<ul><li>Şirket [içi dizininizi Azure AD 'yi kullanarak bulut dizininizle eşitler](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Kullanıcıların Azure AD 'de kurumsal hesaplarına göre SaaS uygulamalarına erişmesini sağlamak için [Çoklu oturum açma](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) özelliğini kullanın.</li><li>Kayıt yapan kullanıcıları izlemek için [parola sıfırlama kaydı etkinlik](../../active-directory/active-directory-passwords-reporting.md) raporunu kullanın.</li><li>Kullanıcılar için [Multi-Factor Authentication 'ı (MFA)](../../active-directory/authentication/multi-factor-authentication.md) etkinleştirin.</li><li>Geliştiriciler [Microsoft Security Development lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379)gibi uygulamalar için güvenli kimlik özellikleri kullanır.</li><li>Azure AD Premium anomali raporlarını ve [Azure AD kimlik koruması özelliğini](../../active-directory/identity-protection/overview.md)kullanarak şüpheli etkinlikleri etkin bir şekilde izleyin.</li></ul> |
|[Devam eden güvenlik Izleme <br>](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Altyapınızda kötü amaçlı yazılımdan koruma korumasının durumunu raporlamak için [Azure izleyici günlüklerini](../../log-analytics/log-analytics-queries.md) kötü amaçlı yazılım değerlendirmesi kullanın.</li><li>Olası güvenlik sorunlarına ilişkin genel pozlandırmayı ve bu güncelleştirmelerin ortamınız için ne kadar kritik olduğunu öğrenmek için [güncelleştirme değerlendirmesini](../../automation/automation-update-management.md) kullanın.</li><li>[Kimlik ve erişim](../../security-center/security-center-monitoring.md) size kullanıcıya ilişkin bir genel bakış sağlar </li><ul><li>Kullanıcı kimliği durumu,</li><li>oturum açmak için başarısız girişim sayısı,</li><li> Bu girişimler sırasında kullanılan Kullanıcı hesabı, kilitlenen hesaplar</li> <li>parola değiştirme veya sıfırlama hesabı </li><li>Şu anda oturum açmış olan hesap sayısı.</li></ul></ul> |
| [<br>Azure Güvenlik Merkezi algılama özellikleri](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Microsoft Azure kaynaklarınızın hedeflediği etkin tehditleri belirlemek için [algılama yeteneklerini](../../security-center/security-center-alerts-overview.md#detect-threats)kullanın.</li><li>Microsoft ürün ve Hizmetleri, Microsoft [Digital Crimes birimi (DCU)](https://www.microsoft.com/trustcenter/security/cybercrime), Microsoft Güvenlik Yanıt MERKEZI (MSRC) ile ilgili küresel tehdit zekasını kullanarak bilinen kötü aktörlerin bulunduğu [tümleşik tehdit zekasını](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) kullanın [ ](response-center.md)ve dış akışlar.</li><li>Kötü amaçlı davranışı bulmaya yönelik bilinen desenler uygulayan [davranış](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) analizini kullanın. </li><li>Bir geçmiş taban çizgisi oluşturmak için istatistiksel profil oluşturmayı kullanan [anomali algılamayı](https://msdn.microsoft.com/library/azure/dn913096.aspx) kullanın.</li></ul> |
| [<br>geliştirici Işlemleri (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Kod olarak altyapı (IAC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) , güvenli, kararlı uygulama barındırma platformları sunmaya yardımcı olmak üzere ağların ve sanal makinelerin oluşturulması ve test edilmesi için otomasyonu ve doğrulamayı sağlayan bir uygulamadır.</li><li>[Sürekli tümleştirme ve dağıtım](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) , kodun sürekli olarak birleştirilmesini ve test edilmesini sağlar ve bu da kusurların erken olarak bulunmasına yol açar. </li><li>[Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) İşlem hattınızdaki her aşama aracılığıyla otomatikleştirilmiş dağıtımları yönetin.</li><li>Uygulama durumu ve müşteri kullanımı için üretim ortamları dahil olmak üzere çalışan uygulamaların [uygulama performansı izlemesi](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) , kuruluşların bir varsayımını ve hızlı bir şekilde doğrulama</li><li>[Otomatik ölçeklendirme & yük testi](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) kullanarak, dağıtım kalitesini artırmak için uygulamamızda performans sorunları bulabilir ve uygulamamız iş ihtiyaçlarına göre her zaman açık veya kullanılabilir durumda olduğundan emin olabilir.</li></ul> |


## <a name="conclusion"></a>Sonuç
Birçok kuruluş, bulut uygulamalarını Azure 'da başarıyla dağıttı ve işletttı. Sağlanan denetim listeleri, gerekli olan birkaç denetim listesini vurgulayın ve başarılı dağıtımlar ve daha fazla ücretsiz işlem olasılığını artırmanıza yardımcı olur. Azure 'da var olan ve yeni uygulama dağıtımlarınız için bu operasyonel ve stratejik konular önerilmektedir.

## <a name="next-steps"></a>Sonraki adımlar
Güvenlik hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Tasarım ve operasyonel güvenlik](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Azure Güvenlik Merkezi planlama ve işlemler](../../security-center/security-center-planning-and-operations-guide.md).
