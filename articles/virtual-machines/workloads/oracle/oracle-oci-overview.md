---
title: Microsoft Azure Oracle bulut altyapısıyla tümleştirin | Microsoft Docs
description: Microsoft Azure üzerinde çalışan Oracle uygulamalarını Oracle bulut altyapısı 'ndaki (OCı) veritabanları ile tümleştiren çözümler hakkında bilgi edinin.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: 04a3fb9e4e7dd1d498714cd3b2ebd4c5f6b55bec
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210355"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Microsoft Azure ve Oracle bulut altyapısını tümleştirerek Oracle uygulama çözümleri

Microsoft ve Oracle, düşük gecikme süresi, yüksek aktarım hızı arası bağlantı sağlamaya ve her iki bulutdan en iyi şekilde yararlanmanıza olanak tanıyacak. 

Bu platformlar arası bağlantıyı kullanarak, veritabanı katmanınızı Oracle bulut altyapısında (OCı), uygulama ve Microsoft Azure üzerindeki diğer katmanlara çalıştırmak için çok katmanlı bir uygulamayı bölümleyebilirsiniz. Deneyim, tüm çözüm yığınını tek bir bulutta çalıştırmaya benzer. 

Web Logic Server dahil olmak üzere, Azure altyapısına, ancak OCı içinde Oracle veritabanını çalıştıran bir ara yazılımı çalıştırmaya ilgileniyorsanız, bkz. [WebLogic Server Azure uygulamaları](oracle-weblogic.md).

Oracle çözümlerini tamamen Azure altyapısına dağıtmaya ilgileniyorsanız, [Microsoft Azure Oracle VM görüntüleri ve bunların dağıtımı](oracle-vm-solutions.md)' na bakın.

## <a name="scenario-overview"></a>Senaryoya genel bakış

Platformlar arası bağlantı, Oracle 'ın sektör lideri uygulamalarını ve kendi özel uygulamalarınızı, OCı 'daki barındırılan veritabanı hizmetlerinin avantajlarından faydalanarak Azure sanal makinelerinde çalıştırmanıza yönelik bir çözüm sağlar. 

Mayıs 2020 itibariyle aşağıdaki uygulamalar, platformlar arası bir yapılandırmada sertifikalandırilmiştir:

* E-Iş paketi
* JD edi EnterpriseOne
* PeopleSoft
* Oracle perakende uygulamaları
* Oracle Hyperion finans yönetimi

Aşağıdaki diyagramda bağlı çözüme üst düzey bir genel bakış sunulmaktadır. Basitlik için diyagram yalnızca bir uygulama katmanını ve bir veri katmanını gösterir. Uygulama mimarisine bağlı olarak, çözümünüz Azure 'da WebLogic Server kümesi veya Web katmanı gibi ek katmanlar içerebilir. Daha fazla bilgi için aşağıdaki bölümlere bakın.

![Azure OCı çözümüne genel bakış](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Bölge kullanılabilirliği 

Çapraz bulut bağlantısı aşağıdaki bölgelerle sınırlıdır:
* Azure Doğu ABD (EastUS) & OCı Ashburn, VA (ABD Doğu)
* Azure UK Güney (UKGüney) & OCı Londra (UK Güney)
* Azure Kanada Orta (Canadaorta) & OCı Tora (Kanada Güneydoğu)
* Azure Batı Avrupa (WestEurope) & OCı Amsterdam (Hollanda kuzeybatı)
* Azure Japonya Doğu (JapanEast) & OCı Tokyo (Japonya Doğu)
* Azure Batı ABD (WestUS) & OCı San Jode (ABD Batı)
* Almanya Orta Batı (Frankfurt) & OCı Almanya Orta (Frankfurt)


## <a name="networking"></a>Ağ

Kurumsal müşteriler genellikle çeşitli iş ve işletimsel nedenlerle birden fazla bulutta iş yüklerini birbirinden ayırmak ve dağıtmak için seçer. Sıfy, müşteriler Internet, IPSec VPN 'i kullanarak bulut ağları ve şirket içi ağınız aracılığıyla bulut sağlayıcısının doğrudan bağlantı çözümünü kullanarak bağlantı sağlar. Bulut ağlarının birbirine bağlanması, zaman, para, tasarım, tedarik, yükleme, test ve işlemler için önemli yatırımlar gerektirebilir. 

Oracle ve Microsoft, bu müşteri sorunlarını gidermek için tümleşik bir çoklu bulut deneyimini etkinleştirdi. Platformlar arası ağ, Microsoft Azure ' de bir [ExpressRoute](../../../expressroute/expressroute-introduction.md) bağlantı hattı, OCI 'Daki [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) devresine bağlanarak oluşturulur. Bu bağlantı, bir Azure ExpressRoute eşleme konumunun, OCı FastConnect ile aynı eşleme konumuna eşit veya daha yakın olduğu durumlarda mümkündür. Bu kurulum, bir ara hizmet sağlayıcısına gerek olmadan iki bulut arasında güvenli ve hızlı bağlantı sağlar.

ExpressRoute ve FastConnect kullanarak, müşteriler, özel IP adresi alanının çakışmadığından, OCı 'daki sanal bir bulut ağıyla Azure 'daki sanal bir ağı eşleyebilir. İki ağı eşleme, sanal ağdaki bir kaynağın, her ikisi de aynı ağda olduğu gibi, OCı sanal bulut ağındaki bir kaynakla iletişim kurmasına izin verir.

## <a name="network-security"></a>Ağ güvenliği

Ağ güvenliği, tüm kurumsal uygulamaların önemli bir bileşenidir ve bu çoklu bulut çözümüne yönelik olarak tasarlanmıştır. ExpressRoute ve FastConnect üzerinden geçen tüm trafik özel bir ağ üzerinden geçer. Bu yapılandırma, bir Azure sanal ağı ile Oracle sanal bulut ağı arasında güvenli iletişim sağlar. Azure 'daki tüm sanal makinelere genel IP adresi sağlamanız gerekmez. Benzer şekilde, OCı 'da internet ağ geçidine gerek kalmaz. Tüm iletişimler, makinelerin özel IP adresi aracılığıyla yapılır.

Ayrıca, OCı sanal bulut ağınız ve güvenlik kurallarınız (Azure [ağ güvenlik grupları](../../../virtual-network/network-security-groups-overview.md)'na bağlı) üzerinde [güvenlik listelerini](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) de ayarlayabilirsiniz. Sanal ağlardaki makineler arasında akan trafiği denetlemek için bu kuralları kullanın. Ağ güvenlik kuralları bir makine düzeyinde, alt ağ düzeyinde ve sanal ağ düzeyinde eklenebilir.

[WebLogic Server Azure uygulamalarının](oracle-weblogic.md) her biri, WebLogic Server 'ın bağlantı noktası yapılandırmalarıyla çalışacak şekilde önceden yapılandırılmış bir ağ güvenlik grubu oluşturur.
 
## <a name="identity"></a>Kimlik

Kimlik, Microsoft ve Oracle arasındaki iş ortaklığının temel sıra sayısının biridir. [Oracle kimlik bulut hizmeti](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) 'ni (ıdcs) [Azure ACTIVE DIRECTORY](../../../active-directory/index.yml) (Azure AD) ile bütünleştirmek için önemli çalışmalar yapıldı. Azure AD, Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Kullanıcılarınız oturum açabilir ve Azure AD 'deki yardım ile çeşitli kaynaklara erişebilir. Azure AD Ayrıca kullanıcılarınızı ve bunların izinlerini yönetmenizi sağlar.

Şu anda bu tümleştirme, Azure Active Directory tek bir merkezi konumda yönetmenizi sağlar. Azure AD, dizindeki tüm değişiklikleri karşılık gelen Oracle diziniyle eşitler ve platformlar arası Oracle çözümlerinde çoklu oturum açma için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Azure ile OCı arasında [platformlar arası bir ağ](configure-azure-oci-networking.md) kullanmaya başlayın. 

OCı hakkında daha fazla bilgi ve teknik bilgiler için bkz. [Oracle bulut](https://docs.cloud.oracle.com/iaas/Content/home.htm) belgeleri.
