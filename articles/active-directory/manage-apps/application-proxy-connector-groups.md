---
title: Bağlayıcı grupları aracılığıyla uygulamaları ayrı ağlarda yayımlama-Azure AD
description: Azure AD Uygulama Ara Sunucusu 'de bağlayıcı gruplarının nasıl oluşturulacağını ve yönetileceğini ele alır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca42137192fefbc669fca775b0deff2f5226c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253841"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Bağlayıcı gruplarını kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama

Müşteriler, daha fazla senaryo ve uygulama için Azure AD 'nin uygulama proxy 'Sini kullanır. Bu nedenle, daha fazla topoloji etkinleştirerek uygulama proxy 'Si daha da esnek hale getirildik. Belirli uygulamalara yönelik belirli bağlayıcılar atayabilmeniz için, uygulama proxy Bağlayıcısı grupları oluşturabilirsiniz. Bu özellik, uygulama proxy dağıtımınızı iyileştirmek için size daha fazla denetim ve yol sağlar.

Her uygulama proxy Bağlayıcısı bir bağlayıcı grubuna atanır. Aynı bağlayıcı grubuna ait olan tüm bağlayıcılar, yüksek kullanılabilirlik ve yük dengeleme için ayrı bir birim işlevi görür. Tüm bağlayıcılar bir bağlayıcı grubuna aittir. Grupları oluşturmazsanız, tüm bağlayıcılar varsayılan grupta bulunur. Yöneticiniz yeni gruplar oluşturup Azure portal bunlara bağlayıcılar atayabilir.

Tüm uygulamalar bir bağlayıcı grubuna atanır. Grup oluşturmazsanız tüm uygulamalarınız varsayılan bir gruba atanır. Ancak bağlayıcıları gruplar halinde düzenlediğinizde, her uygulamayı belirli bir bağlayıcı grubuyla çalışacak şekilde ayarlayabilirsiniz. Bu durumda, yalnızca bu gruptaki bağlayıcılar istek üzerine uygulamayı sunar. Uygulamalarınızın farklı konumlarda barındırıldığı durumlarda bu özellik faydalıdır. Konuma göre bağlayıcı grupları oluşturabilirsiniz, böylece uygulamalar her zaman bunlara fiziksel olarak yakın bağlayıcılar tarafından sunulur.

> [!TIP]
> Büyük bir uygulama proxy dağıtımınız varsa, varsayılan bağlayıcı grubuna hiçbir uygulama atamayın. Bu şekilde, yeni bağlayıcılar bir etkin bağlayıcı grubuna atamaz kadar canlı trafik almaz. Bu yapılandırma ayrıca, kullanıcıları etkilemeden bakım yapabilmek için bağlayıcıları bir boşta moduna geri taşıyarak bunları bir boşta moduna koymanızı sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bağlayıcılarınızı gruplandırmak için [birden çok bağlayıcı yüklediğinizden](application-proxy-add-on-premises-application.md)emin olmanız gerekir. Yeni bir bağlayıcı yüklediğinizde, otomatik olarak **varsayılan** bağlayıcı grubuna katılır.

## <a name="create-connector-groups"></a>Bağlayıcı grupları oluşturma

İstediğiniz sayıda bağlayıcı grubu oluşturmak için bu adımları kullanın.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **uygulama proxy 'si**' ni seçin.
1. **Yeni bağlayıcı grubu**' nu seçin. Yeni bağlayıcı grubu dikey penceresi görünür.

   ![Yeni bir bağlayıcı grubu seçmek için ekranı gösterir](./media/application-proxy-connector-groups/new-group.png)

1. Yeni bağlayıcı grubunuza bir ad verin ve ardından açılan menüyü kullanarak bu gruba ait olan bağlayıcıları seçin.
1. **Kaydet**’i seçin.

## <a name="assign-applications-to-your-connector-groups"></a>Bağlayıcı gruplarınızı uygulama atama

Uygulama proxy 'Si ile yayımladığınız her bir uygulama için bu adımları kullanın. Bir uygulamayı ilk yayımladığınızda bir bağlayıcı grubuna atayabilir veya bu adımları kullanarak atamayı dilediğiniz zaman değiştirebilirsiniz.

1. Dizininizin Yönetim panosundan **Kurumsal uygulamalar**' ı seçin  >  **tüm uygulamalar** > bağlayıcı grubuna atamak istediğiniz uygulamayı > **uygulama proxy 'si**.
1. Uygulamanın kullanmasını istediğiniz grubu seçmek için **bağlayıcı grubu** açılır menüsünü kullanın.
1. Değişikliği uygulamak için **Kaydet** ' i seçin.

## <a name="use-cases-for-connector-groups"></a>Bağlayıcı grupları için kullanım örnekleri

Bağlayıcı grupları aşağıdakiler dahil çeşitli senaryolar için yararlıdır:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Birden çok bağlantılı veri merkezine sahip siteler

Birçok kuruluşun birbirine bağlı çeşitli veri merkezleri vardır. Bu durumda, çoklu veri merkezi bağlantıları pahalı ve yavaş olduğu için veri merkezinde çok sayıda trafik olmasını istemeniz gerekir. Her bir veri merkezinde bağlayıcı dağıtarak yalnızca veri merkezinde bulunan uygulamalara erişebilirsiniz. Bu yaklaşım, platformlar arası bağlantıları en aza indirir ve kullanıcılarınız için tamamen saydam bir deneyim sağlar.

### <a name="applications-installed-on-isolated-networks"></a>Yalıtılmış ağlarda yüklü uygulamalar

Uygulamalar, ana şirket ağının parçası olmayan ağlarda barındırılabilir. Ayrıca, uygulamaları ağ üzerinde yalıtmak için yalıtılmış ağlara adanmış bağlayıcılar yüklemek üzere bağlayıcı gruplarını kullanabilirsiniz. Bu genellikle, üçüncü taraf bir satıcı kuruluşunuz için belirli bir uygulamayı koruduğu zaman gerçekleşir.

Bağlayıcı grupları, yalnızca belirli uygulamaları yayınlayan ağlar için adanmış bağlayıcılar yüklemenize olanak tanır. Bu, dış uygulama yönetiminin üçüncü taraf satıcılara daha kolay ve daha güvenli olmasını sağlar.

### <a name="applications-installed-on-iaas"></a>IaaS üzerinde yüklü uygulamalar

Buluta erişim için IaaS üzerinde yüklü uygulamalar için bağlayıcı grupları, tüm uygulamalara erişimin güvenliğini sağlamak için ortak bir hizmet sağlar. Bağlayıcı grupları, kurumsal ağınızda ek bağımlılık oluşturmaz veya uygulama deneyimini parçalara parçalar. Bağlayıcılar, her bulut veri merkezinde yüklenebilir ve yalnızca o ağda bulunan uygulamalara sunabilir. Yüksek kullanılabilirlik elde etmek için birkaç bağlayıcı yükleyebilirsiniz.

Bir örnek olarak, kendi IaaS barındırılan sanal ağına bağlı birden fazla sanal makine içeren bir kuruluş yapın. Çalışanların bu uygulamaları kullanmasına izin vermek için, bu özel ağlar siteden siteye VPN kullanılarak kurumsal ağa bağlanır. Bu, şirket içinde bulunan çalışanlar için iyi bir deneyim sağlar. Ancak, aşağıdaki diyagramda görebileceğiniz gibi, erişimi yönlendirmek için ek şirket içi altyapı gerektirdiğinden, uzak çalışanlar için ideal olmayabilir:

![Azure AD IaaS ağını gösteren diyagram](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Azure AD Uygulama Ara Sunucusu bağlayıcı gruplarıyla, ortak bir hizmetin kurumsal ağınızda ek bağımlılık oluşturmadan tüm uygulamalara erişimi güvenli hale getirmek için bu hizmeti etkinleştirebilirsiniz:

![Azure AD IaaS birden çok bulut satıcısı](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Çoklu orman – her orman için farklı bağlayıcı grupları

Uygulama proxy 'Si dağıtan müşterilerin çoğu, Kerberos kısıtlanmış temsili (KCD) gerçekleştirerek tek oturum açma (SSO) yeteneklerini kullanıyor. Bunu başarmak için bağlayıcının makineler, uygulamaya doğru kullanıcı temsilcisi olabilecek bir etki alanına katılması gerekir. KCD, ormanlar arası özellikleri destekler. Ancak, aralarında güven olmadan farklı çok ormanlı ortamları olan şirketler için, tüm ormanlarda tek bir bağlayıcı kullanılamaz. 

Bu durumda, belirli bağlayıcılar her orman için dağıtılabilir ve yalnızca söz konusu ormanın kullanıcılarına yönelik olarak yayınlanan uygulamalara servis vermek üzere ayarlanabilir. Her bağlayıcı grubu farklı bir ormanı temsil eder. Kiracı ve bu deneyimin çoğu orman için Birleşik olsa da, kullanıcılar Azure AD grupları kullanılarak orman uygulamalarına atanabilir.

### <a name="disaster-recovery-sites"></a>Olağanüstü durum kurtarma siteleri

Sitelerinizin nasıl uygulandığına bağlı olarak bir olağanüstü durum kurtarma (DR) sitesiyle gerçekleştirebileceğiniz iki farklı yaklaşım vardır:

* DR siteniz, tam olarak ana siteye benzer ve aynı ağ ve AD ayarlarına sahip olan etkin-etkin modda yerleşiktir, ana siteyle aynı bağlayıcı grubunda bulunan DR sitesinde bağlayıcılar oluşturabilirsiniz. Bu, Azure AD 'nin sizin için yük devretme algılamasını sağlar.
* DR siteniz ana siteden ayrı ise, DR sitesinde farklı bir bağlayıcı grubu oluşturabilir ve 1) yedekleme uygulamaları veya 2) var olan uygulamayı DR Bağlayıcısı grubuna gerektiği şekilde el ile el ile el ile kullanabilirsiniz.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Tek bir kiracıya ait birden çok şirket sunar

Tek bir hizmet sağlayıcının birden çok şirket için Azure AD ile ilgili hizmetleri dağıttığı ve koruduğu bir modeli uygulamak için birçok farklı yol vardır. Bağlayıcı grupları, yöneticinin bağlayıcıları ve uygulamaları farklı gruplar halinde ayırt etmenize yardımcı olur. Küçük şirketler için uygun olan bir şekilde, farklı şirketlerin kendi etki alanı adı ve ağlarına sahip olduğu sürece tek bir Azure AD kiracısının olması gerekir. Bu aynı zamanda tek bir BT bölümünün yasal veya iş nedenleriyle çeşitli şirketlere hizmet verdiği bir senaryo ve durum&için de geçerlidir.

## <a name="sample-configurations"></a>Örnek yapılandırma

Uygulayabileceğiniz bazı örnekler aşağıdaki bağlayıcı gruplarını içerir.

### <a name="default-configuration--no-use-for-connector-groups"></a>Varsayılan yapılandırma – bağlayıcı grupları için kullanım yok

Bağlayıcı gruplarını kullanmıyorsanız, yapılandırmanız şu şekilde görünür:

![Örnek Azure AD bağlayıcı grubu yok](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Bu yapılandırma, küçük dağıtımlar ve testler için yeterlidir. Kuruluşunuzun düz bir ağ topolojisi varsa, bu da iyi çalışacaktır.

### <a name="default-configuration-and-an-isolated-network"></a>Varsayılan yapılandırma ve yalıtılmış ağ

Bu yapılandırma, varsayılan olarak, IaaS sanal ağı gibi yalıtılmış bir ağda çalışan belirli bir uygulama olan bir evrimdir:

![Örnek Azure AD hiçbir bağlayıcı grubu ve yalıtılmış ağ](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Önerilen yapılandırma – boşta için birkaç belirli Grup ve varsayılan Grup

Büyük ve karmaşık kuruluşların önerilen yapılandırması, varsayılan bağlayıcı grubuna hiçbir uygulama hizmeti olmayan ve boşta veya yeni yüklenen bağlayıcılar için kullanılan bir grup olarak sahip olmalıdır. Tüm uygulamalar özelleştirilmiş bağlayıcı grupları kullanılarak sunulur. Bu, yukarıda açıklanan senaryoların tüm karmaşıklığına izin vermez.

Aşağıdaki örnekte, şirketin her siteye hizmeti sunan iki bağlayıcı içeren iki veri merkezi, A ve B vardır. Her sitenin üzerinde çalışan farklı uygulamaları vardır.

![2 veri merkezi ve 2 bağlayıcısıyla şirket örneği](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)
* [Çoklu oturum açmayı etkinleştirme](what-is-single-sign-on.md)
