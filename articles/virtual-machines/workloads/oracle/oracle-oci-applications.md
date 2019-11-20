---
title: Azure sanal makinelerinde Oracle uygulamaları dağıtmaya yönelik mimariler | Microsoft Docs
description: Azure 'daki veya Oracle bulut altyapısında (OCı) veritabanları ile E-Business Suite, JD Edliks EnterpriseOne ve PeopleSoft üzerinde Microsoft Azure sanal makinelerde Oracle uygulamaları dağıtmak için uygulama mimarileri.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100079"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Azure 'da Oracle uygulamaları dağıtmaya yönelik mimariler

Microsoft ve Oracle, müşterilerin Oracle E-Business Suite, JD EDBOL EnterpriseOne ve PeopleSoft gibi Oracle uygulamalarını buluta dağıtmasına olanak tanımak için birlikte çalıştık. Microsoft Azure ile Oracle bulut altyapısı (OCı) arasında Önizleme [özel ağ bağlantısı](configure-azure-oci-networking.md) 'nın kullanıma sunulmasıyla birlikte, Oracle uygulamaları artık Azure 'da Azure veya OCI 'daki arka uç veritabanlarıyla birlikte dağıtılabilir. Oracle uygulamaları Azure Active Directory ile tümleştirilebilir, böylece kullanıcıların, Azure Active Directory (Azure AD) kimlik bilgilerini kullanarak Oracle uygulamasında oturum açmasını sağlamak için çoklu oturum açma ayarlamanızı sağlayabilirsiniz.

OCı, Oracle uygulamaları için DBaaS, Sınavveri bulut hizmeti, Oracle RAC ve hizmet olarak altyapı (IaaS) dahil olmak üzere birden çok Oracle veritabanı seçeneği sunar. Şu anda, otonom veritabanı Oracle uygulamaları için desteklenen bir arka uç değildir.

Azure 'da yüksek oranda kullanılabilir ve güvenli bir şekilde Oracle uygulamaları dağıtmaya yönelik [birden çok seçenek](oracle-overview.md) vardır. Azure, Oracle uygulamalarınızı tamamen Azure 'da çalıştırmayı seçerseniz, dağıtabileceğiniz [Oracle VERITABANı VM görüntülerini](oracle-vm-solutions.md) da sunar.

Aşağıdaki bölümlerde, Oracle E-Business Suite, JD EDBOL EnterpriseOne ve PeopleSoft bir çoklu bulut yapılandırmasında veya tamamen Azure 'da dağıtmak için hem Microsoft hem de Oracle 'ın mimari önerileri ana hatlarıyla verilmiştir. Microsoft ve Oracle, bu uygulamaları test etmiştir ve performansın bu uygulamalar için Oracle tarafından ayarlanan standartları karşıladığını onaylamıştır.

## <a name="architecture-considerations"></a>Mimari konuları

Oracle uygulamaları, Azure 'daki aynı veya birden çok sanal makinede veya isteğe bağlı olarak OCı 'da barındırılabilen birden çok hizmetten oluşur. 

Uygulama örnekleri, özel veya genel uç noktalarla ayarlanabilir. Microsoft ve Oracle, uygulamanın yönetimi için ayrı bir alt ağda ortak IP adresi olan bir savunma *ana BILGISAYAR VM* 'si ayarlamayı önerir. Daha sonra, veritabanı katmanı da dahil olmak üzere diğer makinelere yalnızca özel IP adresleri atayın. 

Bir uygulamayı platformlar arası mimaride ayarlarken, Azure sanal ağındaki IP adresi alanının OCı sanal bulut ağındaki özel IP adresi alanıyla çakışmadığından emin olmak için planlama yapılması gerekir.

Ek güvenlik için, yalnızca belirli bağlantı noktalarında ve IP adreslerinde trafiğe izin verildiğinden emin olmak için ağ güvenlik gruplarını alt ağ düzeyinde ayarlayın. Örneğin, orta katmandaki makineler yalnızca sanal ağ içinden trafik almalıdır. Dış trafik, orta katman makinelere doğrudan ulaşmamalıdır.

Yüksek kullanılabilirlik için, aynı Kullanılabilirlik kümesinde veya farklı kullanılabilirlik bölgelerinde farklı sunucuların yedekli örneklerini ayarlayabilirsiniz. Kullanılabilirlik alanları% 99,99 çalışma süresi SLA 'Sı elde etmenizi sağlar, ancak kullanılabilirlik kümeleri bölgeye göre% 99,95 çalışma süresi SLA 'Sı elde etmenizi sağlar. Bu makalede gösterilen örnek mimarilerin iki kullanılabilirlik bölgesine dağıtılması sağlanır.

Platformlar arası Interconnect kullanarak bir uygulamayı dağıttığınızda, Azure ortamınızı şirket içi ağınıza bağlamak için mevcut bir ExpressRoute devresini kullanmaya devam edebilirsiniz. Bununla birlikte, Interconnect 'in şirket içi ağınıza bağlanılarak OCı için ayrı bir ExpressRoute devresi olması gerekir.

## <a name="e-business-suite"></a>E-Iş paketi

Oracle E-Iş paketi (EBS), tedarik zinciri yönetimi (SCM) ve Müşteri Ilişkileri yönetimi (CRM) dahil olmak üzere bir uygulamalar paketidir. OCı 'nın yönetilen veritabanı portföyünden yararlanmak için, Microsoft Azure ve OCı arasındaki platformlar arası bağlantı kullanılarak EBS dağıtılabilir. Bu yapılandırmada, sunu ve uygulama katmanları, aşağıdaki mimari diyagramında gösterildiği gibi, Azure 'da ve OCı 'daki veritabanı katmanında çalışır (Şekil 1).

![E-Iş paketi platformlar arası mimari](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Şekil 1: E-Iş paketi platformlar arası mimari* 

Bu mimaride, Azure 'daki sanal ağ, platformlar arası Interconnect kullanılarak OCı 'daki bir sanal bulut ağına bağlanır. Uygulama katmanı Azure 'da ayarlanır, ancak veritabanı OCı 'da ayarlanır. Belirli bağlantı noktalarında yalnızca belirli alt ağlardan gelen trafiğe izin vermek için, her bir bileşeni ağ güvenlik gruplarıyla kendi alt ağına dağıtmanız önerilir.

Mimari, bir bölgedeki iki kullanılabilirlik bölgesinde Oracle Data Guard kullanılarak yapılandırılmış yüksek oranda kullanılabilir Oracle veritabanları ile tamamen Azure üzerinde dağıtım için de uyarlanabilir. Aşağıdaki diyagram (Şekil 2) bu mimari deseninin bir örneğidir:

![E-Iş paketi yalnızca Azure mimarisi](media/oracle-oci-applications/ebs-arch-azure.png)

*Şekil 2: E-Iş paketi yalnızca Azure mimarisi*

Aşağıdaki bölümlerde, farklı bileşenler yüksek düzeyde açıklanır.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Uygulama (orta) katmanı

Uygulama katmanı kendi alt ağında yalıtılmıştır. Hata toleransı ve kolay yama yönetimi için ayarlanmış birden çok sanal makine vardır. Bu sanal makineler, Azure NetApp Files ve ultra SSD 'ler tarafından sunulan paylaşılan depolama alanı tarafından yönetilebilir. Bu yapılandırma, devre dışı kalma süresi olmadan düzeltme eklerinin daha kolay dağıtılmasını sağlar. Uygulama katmanındaki makinelerin bir genel yük dengeleyici tarafından kullanılması gerekir. böylece, bir hata nedeniyle katmandaki bir makine çevrimdışı olsa bile EBS uygulama katmanına yönelik isteklerin işlenmesi sağlanır.

### <a name="load-balancer"></a>Yük dengeleyici

Azure yük dengeleyici, yüksek kullanılabilirlik sağlamak için iş yükünüzün birden çok örneğine trafiği dağıtmanızı sağlar. Bu durumda, kullanıcıların Web üzerinden EBS uygulamasına erişmesine izin verildiğinden ortak yük dengeleyici ayarlanır. Yük dengeleyici, yükü orta katmandaki her iki makineye dağıtır. Ek güvenlik için, siteden siteye VPN veya ExpressRoute ve ağ güvenlik grupları kullanarak yalnızca sisteme şirket ağınızdan erişen kullanıcılardan gelen trafiğe izin verin.

### <a name="database-tier"></a>Veritabanı katmanı

Bu katman Oracle veritabanını barındırır ve kendi alt ağına ayrılmıştır. Yalnızca uygulama katmanından, Oracle 'a özgü veritabanı bağlantı noktası 1521 ' deki veritabanı katmanına giden trafiğe izin veren ağ güvenlik grupları eklemeniz önerilir.

Microsoft ve Oracle, yüksek oranda kullanılabilir bir kurulum önerir. Azure 'da yüksek kullanılabilirlik, Oracle Data Guard ile iki kullanılabilirlik alanında iki Oracle veritabanı ayarlayarak ya da OCı 'daki Oracle Database sınavı veri bulut hizmeti kullanılarak elde edilebilir. Oracle Database Sınavveri bulut hizmeti kullanılırken veritabanınız iki alt ağda dağıtılır. Ayrıca, Oracle Data Guard ile iki kullanılabilirlik etki alanında OCı 'daki sanal makinelerde Oracle Database da ayarlayabilirsiniz.


### <a name="identity-tier"></a>Kimlik katmanı

Kimlik katmanı, EBS Asserter VM 'sini içerir. EBS Asser, Oracle Identity Cloud Service (ıDCS) ve Azure AD 'den kimlikleri eşitlemenize olanak tanır. EBS, SAML 2,0 veya OpenID Connect gibi çoklu oturum açma protokollerini desteklemediğinden, EBS Asserter gereklidir. EBS tatter, OpenID Connect belirtecini (ıDCS tarafından oluşturulan) kullanır, doğrular ve ardından EBS içinde Kullanıcı için bir oturum oluşturur. 

Bu mimaride ıDCS tümleştirmesi gösteriliyor olsa da, Azure AD birleştirilmiş erişimi ve çoklu oturum açma özelliği Oracle Internet dizini veya Oracle Birleşik dizini ile Oracle Erişim Yöneticisi ile etkinleştirilebilir. Daha fazla bilgi için bkz. [ıDCS tümleştirmesi Ile Oracle EBS dağıtmaya](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) yönelik teknik incelemeler veya [Ohar TÜMLEŞTIRMESIYLE Oracle EBS dağıtımı](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Yüksek kullanılabilirlik için, en önünde bir yük dengeleyici olan birden çok kullanılabilirlik alanında EBS Asserter sunucularının yedek sunucularını dağıtmanız önerilir.

Altyapınız kurulduktan sonra, Oracle tarafından sunulan yükleme kılavuzunu izleyerek E-Iş paketi yükleyebilirsiniz.

## <a name="jd-edwards-enterpriseone"></a>JD edi EnterpriseOne

Oracle 'ın JD Edlelemi EnterpriseOne, kapsamlı kurumsal kaynak planlama yazılımı tümleşik bir uygulamalar paketidir. Oracle veya SQL Server veritabanı arka ucu ile ayarlanabilir çok katmanlı bir uygulamadır. Bu bölümde, Azure 'da veya Azure 'da bir Oracle veritabanı arka ucu ile JD EDBOL EnterpriseOne dağıtımı hakkında ayrıntılar açıklanmaktadır.

Aşağıdaki önerilen mimaride (Şekil 3), yönetim, sunu ve orta katmanlar Azure 'daki sanal ağa dağıtılır. Veritabanı, OCı 'daki bir sanal bulut ağında dağıtılır.

E-Iş paketinde olduğu gibi, güvenli yönetim amaçları için isteğe bağlı bir savunma katmanı da ayarlayabilirsiniz. Uygulama ve veritabanı örneklerine erişmek için bir sıçrama sunucusu olarak savunma VM konağını kullanın.

![JD edis EnterpriseOne çapraz bulut mimarisi](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Şekil 3: JD edis EnterpriseOne çapraz bulut mimarisi*

Bu mimaride, Azure 'daki sanal ağ, platformlar arası Interconnect kullanılarak OCı 'daki sanal bulut ağına bağlanır. Uygulama katmanı Azure 'da ayarlanır, ancak veritabanı OCı 'da ayarlanır. Belirli bağlantı noktalarında yalnızca belirli alt ağlardan gelen trafiğe izin vermek için, her bir bileşeni ağ güvenlik gruplarıyla kendi alt ağına dağıtmanız önerilir.

Mimari, bir bölgedeki iki kullanılabilirlik bölgesinde Oracle Data Guard kullanılarak yapılandırılmış yüksek oranda kullanılabilir Oracle veritabanları ile tamamen Azure üzerinde dağıtım için de uyarlanabilir. Aşağıdaki diyagram (Şekil 4) bu mimari deseninin bir örneğidir:

![JD edis EnterpriseOne yalnızca Azure mimarisi](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Şekil 4: JD edis EnterpriseOne yalnızca Azure mimarisi*

Aşağıdaki bölümlerde, farklı bileşenler yüksek düzeyde açıklanır.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Yönetim Katmanı

Adından da anlaşılacağı gibi, bu katman yönetim görevleri için kullanılır. Yönetim katmanı için ayrı bir alt ağ ekleyebilirsiniz. Bu katmandaki hizmetler ve sunucular öncelikle uygulamanın yüklenmesi ve yönetimi için kullanılır. Bu nedenle, bu sunucuların tek örnekleri yeterlidir. Uygulamanızın yüksek kullanılabilirliği için gereksiz örnekler gerekli değildir.

Bu katmanın bileşenleri şunlardır:
    
 - **Sunucu sağlama** -bu sunucu, uygulamanın farklı bileşenlerinin uçtan uca dağıtımı için kullanılır. Veritabanı katmanındaki örnekler de dahil olmak üzere diğer katmanlardaki örneklerle birlikte iletişim kurar. JD Edler EnterpriseOne için Sunucu Yöneticisi konsolunu barındırır.
 - **Dağıtım sunucusu** -bu sunucu öncelikle JD EDBOL EnterpriseOne yüklemesi için gereklidir. Yükleme işlemi sırasında, bu sunucu gerekli dosyalar ve yükleme paketleri için merkezi depo işlevi görür. Yazılım, bu sunucudan diğer sunuculara ve istemcilere dağıtılır veya dağıtılır.
 - **Geliştirme istemcisi** -bu sunucu, yerel uygulamaların yanı sıra bir Web tarayıcısında çalışan bileşenleri içerir.

### <a name="presentation-tier"></a>Sunum katmanı

Bu katman, uygulama arabirimi Hizmetleri (ASıS), uygulama geliştirme çerçevesi (ADF) ve Java uygulama sunucuları (JAS) gibi çeşitli bileşenleri içerir. Bu katmandaki sunucular orta katmandaki sunucularla iletişim kurar. Trafiği, trafiğin alındığı bağlantı noktası numarasına ve URL 'ye göre gereken sunucuya yönlendiren bir yük dengeleyici tarafından alınmıştır. Yüksek kullanılabilirlik için her bir sunucu türünün birden çok örneğini dağıtmanız önerilir.

Bu katmandaki bileşenler aşağıda verilmiştir:
    
- **Uygulama arabirim Hizmetleri (ASIS)** -ASIS sunucusu, JD Edleliks EnterpriseOne mobil kurumsal uygulamaları ve JD EDBOL EnterpriseOne arasında iletişim arabirimi sağlar.
- **Java uygulama sunucusu (JAS)** -Jas, istekleri yük dengeleyiciden alır ve karmaşık görevleri yürütmek için Orta katmana geçirir. JAS 'in basit iş mantığını yürütme yeteneği vardır.
- **Bı Publisher sunucusu (BIP)** -bu sunucu, JD edi EnterpriseOne uygulaması tarafından toplanan verileri temel alan raporlar sunar. Raporun verileri farklı şablonlara göre nasıl sunacağını tasarlayabilir ve denetleyebilirsiniz.
- **Iş Hizmetleri sunucusu (BSS)** -BSS, diğer Oracle uygulamalarıyla bilgi alışverişi ve birlikte çalışabilirliği sunar.
- **Gerçek zamanlı olaylar sunucusu (RTE)** -RTE sunucusu, JDE EnterpriseOne sisteminde gerçekleşen işlemler hakkında dış sistemlere bildirim ayarlamanıza olanak sağlar. Abone modeli kullanır ve üçüncü taraf sistemlerinin olaylara abone olmasına olanak sağlar. İsteklerin her iki RTE sunucusuna yük dengelemesi yapmak için sunucuların bir kümede olduğundan emin olun.
- **Uygulama geliştirme çerçevesi (ADF) sunucusu** -ADF sunucusu, Oracle ADF ile geliştirilen JD Edleliks EnterpriseOne uygulamalarını çalıştırmak için kullanılır. Bu, ADF çalışma zamanına sahip bir Oracle WebLogic sunucusuna dağıtılır.

### <a name="middle-tier"></a>Orta katman

Orta katman, Logic Server ve Batch sunucusunu içerir. Bu durumda, her iki sunucu da aynı sanal makineye yüklenir. Ancak, üretim senaryolarında mantıksal sunucuyu ve Batch sunucusunu ayrı sunuculara dağıtmanız önerilir. Daha yüksek kullanılabilirlik için iki kullanılabilirlik bölgesinde Orta katmanda birden çok sunucu dağıtılır. Her iki sunucunun da etkin ve işleme isteklerini sağlamak için, bir Azure yük dengeleyici oluşturulmalı ve bu sunucuların arka uç havuzunda yerleştirilmesi gerekir.

Orta katmandaki sunucular yalnızca sunu katmanındaki sunuculardan ve ortak yük dengeleyiciden gelen istekleri alır. Ağ güvenlik grubu kurallarının, sunum katmanı alt ağı ve yük dengeleyici dışında herhangi bir adresten gelen trafiği reddedecek şekilde ayarlanması gerekir. Bir NSG kuralı, yönetim amacıyla savunma ana bilgisayarında bağlantı noktası 22 ' de trafiğe izin verecek şekilde ayarlanabilir. Orta katmandaki VM 'Ler arasında yük dengeleme isteklerini dengelemek için ortak yük dengeleyiciyi kullanabilirsiniz.

Aşağıdaki iki bileşen Orta katmanda bulunur:

- **Logic Server** -iş mantığını veya iş işlevlerini içerir.
- **Batch sunucusu** -Batch işleme için kullanılır

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 'ın PeopleSoft Application Suite, insan kaynakları ve finans yönetimi için yazılım içerir. Uygulama paketi çok katmanlı ve uygulamalar insan kaynakları yönetim sistemleri (HRMS), müşteri ilişkileri yönetimi (CRM), mali ve tedarik zinciri yönetimi (FSCM) ve kurumsal performans yönetimi (EPM) içerir.

Yazılım paketinin her katmanının kendi alt ağına dağıtılması önerilir. Uygulamanın arka uç veritabanı olarak bir Oracle veritabanı veya Microsoft SQL Server gereklidir. Bu bölümde, Oracle veritabanı arka ucu ile PeopleSoft dağıtma ile ilgili ayrıntılar açıklanmaktadır.

Aşağıdakiler, PeopleSoft uygulama paketini bir platformlar arası mimariye dağıtmaya yönelik kurallı bir mimaridir (Şekil 5).

![PeopleSoft platformlar arası mimari](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Şekil 5: PeopleSoft platformlar arası mimari*

Bu örnek mimaride, Azure 'daki sanal ağ, platformlar arası Interconnect kullanılarak OCı 'daki sanal bulut ağına bağlanır. Uygulama katmanı Azure 'da ayarlanır, ancak veritabanı OCı 'da ayarlanır. Belirli bağlantı noktalarında yalnızca belirli alt ağlardan gelen trafiğe izin vermek için, her bir bileşeni ağ güvenlik gruplarıyla kendi alt ağına dağıtmanız önerilir.

Mimari, bir bölgedeki iki kullanılabilirlik bölgesinde Oracle Data Guard kullanılarak yapılandırılmış yüksek oranda kullanılabilir Oracle veritabanları ile tamamen Azure üzerinde dağıtım için de uyarlanabilir. Aşağıdaki diyagram (Şekil 6) bu mimari deseninin bir örneğidir:

![PeopleSoft yalnızca Azure mimarisi](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Şekil 6: PeopleSoft yalnızca Azure mimarisi*

Aşağıdaki bölümlerde, farklı bileşenler yüksek düzeyde açıklanır.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Uygulama katmanı

Uygulama katmanı, PeopleSoft uygulama sunucularının, PeopleSoft Web sunucularının, elastik aramanın ve PeopleSoft Işlem zamanlayıcının örneklerini içerir. Azure yük dengeleyici, uygulama katmanındaki uygun sunucuya yönlendirilen kullanıcılardan gelen istekleri kabul edecek şekilde ayarlanır.

Yüksek kullanılabilirlik için, farklı kullanılabilirlik bölgelerinde uygulama katmanındaki her bir sunucunun yedekli örneklerini ayarlamayı düşünün. Her isteği doğru sunucuya yönlendirmek için Azure yük dengeleyici birden fazla arka uç havuzlarıyla ayarlanabilir.

### <a name="peopletools-client"></a>PeopleTools Istemcisi

PeopleTools Istemcisi, geliştirme, geçiş ve yükseltme gibi yönetim etkinliklerini gerçekleştirmek için kullanılır. Uygulamanızın yüksek oranda kullanılabilirliğini sağlamak için PeopleTools Istemcisi gerekli olmadığından, PeopleTools Istemcisinin yedekli sunucuları gerekli değildir.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da Oracle uygulamaları ayarlamak ve OCı ile platformlar arası bağlantı kurmak için [Terrayform betikleri](https://github.com/microsoft/azure-oracle) kullanın.

OCı hakkında daha fazla bilgi ve teknik bilgiler için bkz. [Oracle bulut](https://docs.cloud.oracle.com/iaas/Content/home.htm) belgeleri.
