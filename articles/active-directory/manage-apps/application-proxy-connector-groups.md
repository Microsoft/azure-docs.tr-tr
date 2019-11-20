---
title: Azure AD Uygulaması Proxy bağlayıcı gruplarıyla ayrı ağlarda uygulama yayımlama | Microsoft Docs
description: Azure AD uygulama proxy'sinde bağlayıcı gruplarını oluşturma ve yönetme konusunu kapsar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae4eea3e08818d43482c995595cc9fbc3f91910
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381491"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Ayrı ağlarda ve konumları bağlayıcı grupları kullanarak uygulama yayımlama

Müşteriler, daha da fazla senaryoları için Azure AD uygulama proxy'si ve uygulamalar kullanır. Bu nedenle uygulama proxy'si daha esnek daha fazla topolojileri etkinleştirerek yaptık. Belirli uygulamalar sunmak için özel bağlayıcılar atayabilirsiniz böylece uygulama Proxy Bağlayıcısı grupları oluşturabilirsiniz. Bu özellik, daha fazla denetim ve uygulama proxy'si dağıtımınızın iyileştirilmesine yönelik yollar sunar.

Her uygulama Proxy Bağlayıcısı, bir bağlayıcı grubuna atanır. Aynı bağlayıcı grubuna ait tüm bağlayıcılar, yüksek kullanılabilirlik için ayrı bir birim olarak davranır ve Yük Dengeleme. Tüm bağlayıcılar, bağlayıcı grubuna ait. Grupları oluşturmazsanız, tüm bağlayıcılar varsayılan bir grupta olan. Yöneticiniz, yeni grup oluşturabilir ve Azure Portalı'nda bağlayıcılar atamanız.

Tüm uygulamalar için bir bağlayıcı grubu olarak atanır. Ardından grupları oluşturmazsanız, tüm uygulamalarınızı varsayılan gruba atanır. Ancak, bağlayıcılarınızı gruplar halinde düzenlemek, her bir uygulama bir özel bağlayıcı grubu ile çalışacak şekilde ayarlayabilirsiniz. Bu durumda, yalnızca o gruptaki bağlayıcılar, uygulama istek üzerine işlevi görür. Bu özellik, farklı konumlarda barındırılan uygulamalarınız varsa yararlıdır. Bağlayıcı grupları konumuna bağlı uygulamaları her zaman fiziksel olarak bunları yakın olan bağlayıcılar tarafından sunulan oluşturabilirsiniz.

> [!TIP]
> Büyük bir uygulama ara sunucusu dağıtım varsa, tüm uygulamaları varsayılan bağlayıcı grubuna atamayın. Bu şekilde, bir etkin bağlayıcı grubuna atama kadar yeni bağlayıcılar herhangi bir canlı trafik almaz. Bu yapılandırma, bakım kullanıcılarınız etkilemeden gerçekleştirebilmeleri için bağlayıcılar bir boşta modunda geri varsayılan grubuna taşıyarak yerleştirilmesine olanak sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bağlayıcılarınızı grubuna emin olmak sahip [yüklü birden fazla bağlayıcıyı](application-proxy-add-on-premises-application.md). Yeni bir bağlayıcı yükleme sırasında otomatik olarak katılır **varsayılan** bağlayıcı grubu.

## <a name="create-connector-groups"></a>Bağlayıcı grupları oluşturma

İstediğiniz sayıda bağlayıcı grubu oluşturmak için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Seçin **Azure Active Directory** > **kurumsal uygulamalar** > **uygulama proxy'si**.
1. Seçin **yeni bağlayıcı grubu**. Yeni bağlayıcı grubu dikey penceresi görüntülenir.

   ![Yeni bir bağlayıcı grubu seçmek için ekranı gösterir](./media/application-proxy-connector-groups/new-group.png)

1. Yeni bağlayıcı grubunuz bir ad verin ve ardından bu gruba hangi bağlayıcılar ait seçmek için açılan menüyü kullanın.
1. **Kaydet**’i seçin.

## <a name="assign-applications-to-your-connector-groups"></a>Bağlayıcı gruplarınızı uygulamaları atama

Uygulama proxy'si ile yayımladığınız her uygulama için bu adımları kullanın. Bir bağlayıcı grubu uygulamaya ilk yayımlamak ya da istediğiniz zaman atamasını değiştirmek için aşağıdaki adımları kullanabilirsiniz atayabilirsiniz.

1. Management panosunda dizininizin **kurumsal uygulamalar** > **tüm uygulamaları** > için bir bağlayıcı grubu atamak istediğiniz uygulamayı > **Uygulama proxy'si**.
1. Kullanma **bağlayıcı grubu** açılan menüsüne uygulamanın kullanmasını istediğiniz grubu seçin.
1. Seçin **Kaydet** değişikliği uygulamak için.

## <a name="use-cases-for-connector-groups"></a>Bağlayıcı grupları için kullanım örnekleri

Bağlayıcı grupları dahil olmak üzere çeşitli senaryolar için kullanışlıdır:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Birbirine bağlı birden çok veri merkezinde siteleriyle

Birçok kuruluşun, birbirine bağlı veri merkezlerinden oluşan bir sayı vardır. Bu durumda, veri merkezli bağlantıları pahalı ve yavaş olduğundan mümkün olduğu kadar trafik veri merkezi içinde saklamak istediğiniz. Bağlayıcılar veri merkezi içinde bulunan uygulamalar sunmak için her veri merkezinde dağıtabilirsiniz. Bu yaklaşım, veri merkezli bağlantılarını en aza indirir ve kullanıcılarınız için tamamen saydam bir deneyim sağlar.

### <a name="applications-installed-on-isolated-networks"></a>Yalıtılmış ağlarda yüklü uygulamalar

Uygulamalar, ana Kurumsal ağın parçası olmayan ağlara barındırılabilir. Özel bağlayıcılar da ağ uygulamaları yalıtmak için yalıtılmış ağlarda yüklemek için bağlayıcı gruplarını kullanabilirsiniz. Bu durum, genellikle belirli bir uygulama için kuruluşunuzun bir üçüncü taraf satıcı olduğunda gerçekleşir.

Bağlayıcı grupları, özel bağlayıcılar için yalnızca belirli uygulamaları yayımlama bu ağları yüklemek daha kolay ve uygulama yönetimi için üçüncü taraf satıcılarla dış kaynaklara daha güvenli hale getirme sağlar.

### <a name="applications-installed-on-iaas"></a>Iaas üzerinde yüklü uygulamalar

Bulut erişimi için Iaas üzerinde yüklü uygulamalar için bağlayıcı grupları tüm uygulamalara erişimi güvenli hale getirmek için ortak bir hizmet sağlar. Bağlayıcı grupları kuruluşunuzun ağında ek bağımlılık oluşturmak veya uygulama deneyimi parçalara kullanmayın. Bağlayıcılar, her bir bulut veri merkezine yüklenebilir ve bu ağda bulunan uygulamalar sunmak. Yüksek kullanılabilirlik elde etmek için birkaç bağlayıcı yükleyebilirsiniz.

Sanal ağ için kendi Iaas bağlı bazı sanal makineleri olan bir kuruluş barındırılan bir örnek olarak alın. Bu uygulamaları kullanmak çalışanların izin vermek için bu özel ağların siteden siteye VPN kullanarak şirket ağına bağlanır. Bu, şirket içinde olan çalışanlar için iyi bir deneyim sağlar. Ancak, aşağıdaki diyagramda görüldüğü gibi erişimi yönlendirmek için şirket içinde ek altyapı gerektirdiğinden, uzak çalışanlar için ideal olmayabilir:

![Azure AD IaaS ağını gösteren diyagram](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Azure AD uygulama ara sunucusu Bağlayıcısı gruplarıyla şirket ağınızda ek bağımlılık oluşturmadan tüm uygulamalara erişimi güvenli hale getirmek ortak bir hizmet etkinleştirebilirsiniz:

![Azure AD IaaS birden çok bulut satıcısı](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Çok ormanlı – her orman için farklı bir bağlayıcı grupları

Uygulama Ara sunucusu dağıtan çoğu müşteri, Kerberos Kısıtlı temsilci (KCD) uygulayarak, çoklu oturum açma (SSO) özellikleri kullanıyor. Bunu başarmak için bağlayıcının makinelerin uygulamaya yönelik kullanıcı temsilcileri seçebilir bir etki alanına katılması gerekir. KCD, orman özelliklerini destekler. Ancak, aralarında hiçbir güven ile ayrı çok ormanlı ortamları olan şirketler için tek bir bağlayıcıyı tüm ormanlar için kullanılamaz. 

Bu durumda, özel bağlayıcılar orman dağıtılabilir ve yalnızca belirli söz konusu ormanın kullanıcılara hizmet vermesi için yayımlanan uygulamalar sunmak için ayarlayın. Her bir bağlayıcı grubu farklı bir ormana temsil eder. Tüm ormanlar için birleşik Kiracı ve en iyi deneyimi sırasında Azure AD grupları kullanarak orman uygulamalarını kullanıcılara atanabilir.

### <a name="disaster-recovery-sites"></a>Olağanüstü durum kurtarma sitelerinde

Sitelerinizi nasıl uygulandığını bağlı olarak bir olağanüstü durum kurtarma (DR) site ile yapabileceğiniz iki farklı yaklaşım vardır:

* DR siteniz nerede gibi tam olarak ana site ve aynı ağ ve AD ayarları etkin-etkin modda oluşturulduysa, ana site ile aynı bağlayıcı grubunda DR sitesinde bağlayıcılar oluşturabilirsiniz. Bu, yük devretme işlemleri sizin yerinize algılamak Azure AD sağlar.
* DR sitenizin ana siteden ayrı, DR sitedeki farklı bağlayıcı grubu oluşturabilirsiniz ve 1) yedekleme uygulamaları sahip olmanız veya 2) el ile gerektiği gibi varolan bir DR bağlayıcı grubu uygulamaya yöneltmektir.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Tek bir kiracıdan birden çok şirket hizmet

İlgili birden çok şirket Hizmetleri tek hizmet sağlayıcısı dağıtır ve Azure AD tutan bir modeli uygulamak için birçok farklı yolu vardır. Bağlayıcı grupları bağlayıcılar ve uygulamaları farklı gruplar halinde ayırmak yönetici yardımcı olur. Küçük şirketler için uygun olan bir şekilde tek bir Azure AD farklı şirketlerin kendi etki alanı adı ve ağları varken Kiracı sağlamaktır. Bu da tek bir BT bölümü birkaç şirketin yasal veya iş nedenleriyle nerede hizmet M & A senaryoları ve durumlar için geçerlidir.

## <a name="sample-configurations"></a>Örnek yapılandırmaları

Aşağıdaki bağlayıcı grupları uygulayabileceğiniz, örnek olarak verilebilir.

### <a name="default-configuration--no-use-for-connector-groups"></a>Varsayılan yapılandırma – bağlayıcı grupları için herhangi bir kullanıma

Bağlayıcı grupları kullanmazsanız, yapılandırmanızı şöyle görünebilir:

![Örnek Azure AD bağlayıcı grubu yok](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Bu yapılandırma, küçük dağıtımları ve testler için yeterlidir. İyi kuruluşunuzun bir düz ağ topolojisi varsa da çalışır.

### <a name="default-configuration-and-an-isolated-network"></a>Varsayılan yapılandırma ve yalıtılmış ağ

Bu yapılandırma, varsayılan bir Iaas sanal ağ gibi yalıtılmış bir ağda çalışan belirli bir uygulama olduğu bir halidir şöyledir:

![Örnek Azure AD hiçbir bağlayıcı grubu ve yalıtılmış ağ](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Birkaç belirli grupları ve varsayılan grup için önerilen yapılandırması – boş

Büyük ve karmaşık kuruluşlar için önerilen yapılandırma, tüm uygulamaları görmese ve boşta veya yeni yüklenen bağlayıcıları için kullanılan bir grup olarak varsayılan bağlayıcı grubu sağlamaktır. Tüm uygulamaları, özelleştirilmiş bağlayıcı grupları kullanarak sunulur. Bu, yukarıda açıklanan senaryolardan tüm karmaşıklığı sağlar.

Aşağıdaki örnekte, iki veri merkezleri, A ve B ile hizmet her site iki bağlayıcı şirket sahiptir. Her sitenin üzerinde çalışan farklı uygulamaları vardır.

![2 veri merkezi ve 2 bağlayıcısıyla şirket örneği](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD uygulama ara sunucusu bağlayıcıları anlama](application-proxy-connectors.md)
* [Çoklu oturum açmayı etkinleştirme](what-is-single-sign-on.md)
