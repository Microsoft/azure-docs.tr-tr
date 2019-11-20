---
title: Microsoft Azure Oracle bulut altyapısıyla tümleştirin | Microsoft Docs
description: Microsoft Azure üzerinde çalışan Oracle uygulamalarını Oracle bulut altyapısı 'ndaki (OCı) veritabanları ile tümleştiren çözümler hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 0f35f22d6bd32d75d5c3b07d8a20b4bb7714e247
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799553"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Microsoft Azure ve Oracle bulut altyapısını tümleştirerek Oracle uygulama çözümleri (Önizleme)

Microsoft ve Oracle, düşük gecikme süresi, yüksek aktarım hızı arası bağlantı sağlamaya ve her iki bulutdan en iyi şekilde yararlanmanıza olanak tanıyacak. 

Bu platformlar arası bağlantıyı kullanarak, veritabanı katmanınızı Oracle bulut altyapısında (OCı), uygulama ve Microsoft Azure üzerindeki diğer katmanlara çalıştırmak için çok katmanlı bir uygulamayı bölümleyebilirsiniz. Deneyim, tüm çözüm yığınını tek bir bulutta çalıştırmaya benzer. 

> [!IMPORTANT]
> Bu platformlar arası özellik şu anda önizleme aşamasındadır ve [sınırlamalar geçerlidir](#preview-limitations). Azure ile OCı arasında düşük gecikmeli bağlantı kurmak için, bu özellik için önce Azure aboneliğinizin etkinleştirilmesi gerekir. Bu kısa [anket formunu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)tamamlayarak önizlemeye kaydolmalısınız. Aboneliğiniz kaydedildiğinde siz de bir e-posta alırsınız. Onay e-postası alınana kadar özelliği kullanamazsınız. Ayrıca, bu önizleme için etkinleştirilecek Microsoft temsilcinize de başvurabilirsiniz. Önizleme özelliğine erişim, Microsoft 'un kendi takdirine bağlı olarak kullanılabilirliğine ve kısıtlanmasını sağlar. Anketin tamamlanması erişimi garanti etmez. Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için Microsoft Azure önizlemeleri için [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) bölümüne bakın. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

Oracle çözümlerini tamamen Azure altyapısına dağıtmaya ilgileniyorsanız, [Microsoft Azure Oracle VM görüntüleri ve bunların dağıtımı](oracle-vm-solutions.md)' na bakın.

## <a name="scenario-overview"></a>Senaryoya genel bakış

Platformlar arası bağlantı, Oracle 'ın sektör lideri uygulamalarını ve kendi özel uygulamalarınızı, OCı 'daki barındırılan veritabanı hizmetlerinin avantajlarından faydalanarak Azure sanal makinelerinde çalıştırmanıza yönelik bir çözüm sağlar. 

Platformlar arası yapılandırmada çalıştırabileceğiniz uygulamalar şunlardır:

* E-Iş paketi
* JD edi EnterpriseOne
* PeopleSoft
* Oracle perakende uygulamaları
* Oracle Hyperion finans yönetimi

Aşağıdaki diyagramda bağlı çözüme üst düzey bir genel bakış sunulmaktadır. Basitlik için diyagram yalnızca bir uygulama katmanını ve bir veri katmanını gösterir. Uygulama mimarisine bağlı olarak, çözümünüz Azure 'da Web katmanı gibi ek katmanlar içerebilir. Daha fazla bilgi için aşağıdaki bölümlere bakın.

![Azure OCı çözümüne genel bakış](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Önizlemede çapraz bulut bağlantısı, Azure Doğu ABD (eastus) & UK Güney (UKGüney) bölgeleriyle ve OCı Ashyakma (ABD Doğu) & Londra (UK Güney) bölgeleriyle sınırlıdır. UK Güney için, daha düşük gecikme süreleriyle bağlantı dağıtmak için lütfen OCı 'da kullanılabilirlik etki alanı 1 (AD 1) kullanın.

## <a name="networking"></a>Networking (Ağ İletişimi)

Kurumsal müşteriler genellikle çeşitli iş ve işletimsel nedenlerle birden fazla bulutta iş yüklerini birbirinden ayırmak ve dağıtmak için seçer. Sıfy, müşteriler Internet, IPSec VPN 'i kullanarak bulut ağları ve şirket içi ağınız aracılığıyla bulut sağlayıcısının doğrudan bağlantı çözümünü kullanarak bağlantı sağlar. Bulut ağlarının birbirine bağlanması, zaman, para, tasarım, tedarik, yükleme, test ve işlemler için önemli yatırımlar gerektirebilir. 

Oracle ve Microsoft, bu müşteri sorunlarını gidermek için tümleşik bir çoklu bulut deneyimini etkinleştirdi. Platformlar arası ağ, Microsoft Azure ' de bir [ExpressRoute](../../../expressroute/expressroute-introduction.md) bağlantı hattı, OCI 'Daki [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) devresine bağlanarak oluşturulur. Bu bağlantı, bir Azure ExpressRoute eşleme konumunun, OCı FastConnect ile aynı eşleme konumuna eşit veya daha yakın olduğu durumlarda mümkündür. Bu kurulum, bir ara hizmet sağlayıcısına gerek olmadan iki bulut arasında güvenli ve hızlı bağlantı sağlar.

ExpressRoute ve FastConnect kullanarak, müşteriler, özel IP adresi alanının çakışmadığından, OCı 'daki sanal bir bulut ağıyla Azure 'daki sanal bir ağı eşleyebilir. İki ağı eşleme, sanal ağdaki bir kaynağın, her ikisi de aynı ağda olduğu gibi, OCı sanal bulut ağındaki bir kaynakla iletişim kurmasına izin verir.

## <a name="network-security"></a>Ağ güvenliği

Ağ güvenliği, tüm kurumsal uygulamaların önemli bir bileşenidir ve bu çoklu bulut çözümüne yönelik olarak tasarlanmıştır. ExpressRoute ve FastConnect üzerinden geçen tüm trafik özel bir ağ üzerinden geçer. Bu yapılandırma, bir Azure sanal ağı ile Oracle sanal bulut ağı arasında güvenli iletişim sağlar. Azure 'daki tüm sanal makinelere genel IP adresi sağlamanız gerekmez. Benzer şekilde, OCı 'da internet ağ geçidine gerek kalmaz. Tüm iletişimler, makinelerin özel IP adresi aracılığıyla yapılır.

Ayrıca, OCı sanal bulut ağınız ve güvenlik kurallarınız (Azure [ağ güvenlik grupları](../../../virtual-network/security-overview.md)'na bağlı) üzerinde [güvenlik listelerini](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) de ayarlayabilirsiniz. Sanal ağlardaki makineler arasında akan trafiği denetlemek için bu kuralları kullanın. Ağ güvenlik kuralları bir makine düzeyinde, alt ağ düzeyinde ve sanal ağ düzeyinde eklenebilir.
 
## <a name="identity"></a>Kimlik

Kimlik, Microsoft ve Oracle arasındaki iş ortaklığının temel sıra sayısının biridir. [Oracle kimlik bulut hizmeti](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) 'ni (ıdcs) [Azure ACTIVE DIRECTORY](../../../active-directory/index.yml) (Azure AD) ile bütünleştirmek için önemli çalışmalar yapıldı. Azure AD, Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Kullanıcılarınızın oturum açmasını ve çeşitli kaynaklara erişmesini sağlar. Azure AD Ayrıca kullanıcılarınızı ve bunların izinlerini yönetmenizi sağlar.

Şu anda bu tümleştirme, Azure Active Directory tek bir merkezi konumda yönetmenizi sağlar. Azure AD, dizindeki tüm değişiklikleri karşılık gelen Oracle diziniyle eşitler ve platformlar arası Oracle çözümlerinde çoklu oturum açma için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Azure ile OCı arasında [platformlar arası bir ağ](configure-azure-oci-networking.md) kullanmaya başlayın. 

OCı hakkında daha fazla bilgi ve teknik bilgiler için bkz. [Oracle bulut](https://docs.cloud.oracle.com/iaas/Content/home.htm) belgeleri.
