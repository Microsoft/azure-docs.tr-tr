---
title: Azure Güvenlik Duvarı Yöneticisi güvenlik iş ortağı sağlayıcısı dağıtma
description: Azure portal kullanarak bir Azure Güvenlik Duvarı Yöneticisi güvenlik iş ortağı sağlayıcısını dağıtmayı öğrenin.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: b8e10eef89df12807cabd96d64d9c7d659f91d6c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109518"
---
# <a name="deploy-a-security-partner-provider"></a>Güvenlik iş ortağı sağlayıcısı dağıtma

Azure Güvenlik Duvarı Yöneticisi 'ndeki *güvenlik iş ortağı sağlayıcıları* , kullanıcılarınız için Internet erişimini korumak üzere tanıdık, en iyi bir hizmet olarak üçüncü taraf güvenlik (SECaaS) tekliflerinizi kullanmanıza olanak sağlar.

Desteklenen senaryolar ve en iyi yöntem yönergeleri hakkında daha fazla bilgi edinmek için bkz. [güvenlik iş ortağı sağlayıcıları nelerdir?](trusted-security-partners.md)


Hizmet olarak tümleşik üçüncü taraf güvenlik (SECaaS) iş ortakları artık kullanılabilir: 

- **Zscaler**
- **[Denetim Noktası](check-point-overview.md)**
- **ıpatron**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Yeni bir hub 'da üçüncü taraf güvenlik sağlayıcısı dağıtma

Mevcut bir hub 'a üçüncü taraf bir sağlayıcı dağıtıyorsanız bu bölümü atlayın.

1. https://portal.azure.com adresinden Azure portalında oturum açın.
2. **Ara** bölümünde **güvenlik duvarı Yöneticisi** yazın ve **Hizmetler** altında seçin.
3. **Başlarken**' e gidin. **Güvenli sanal hub 'Ları görüntüle**' yi seçin.
4. **Yeni güvenli sanal hub oluştur**' u seçin.
5. Aboneliğinizi ve kaynak grubunu girin, desteklenen bir bölge seçin ve hub ve sanal WAN bilgilerinizi ekleyin. 
6. **Güvenlik Iş ortağı sağlayıcıları 'nı etkinleştirmek IÇIN VPN Gateway Ekle**' yi seçin.
7. Gereksinimlerinize uygun **ağ geçidi ölçek birimlerini** seçin.
8. Ileri 'yi seçin **: Azure Güvenlik Duvarı**
   > [!NOTE]
   > Güvenlik ortağı sağlayıcıları VPN Gateway tünellerini kullanarak hub 'ınıza bağlanır. VPN Gateway silerseniz, güvenlik iş ortağı sağlayıcılarınızın bağlantıları kaybedilir.
9. Internet trafiğini filtrelemek için üçüncü taraf hizmet sağlayıcısıyla birlikte özel trafiği filtrelemek üzere Azure Güvenlik Duvarı dağıtmak istiyorsanız, Azure Güvenlik Duvarı için bir ilke seçin. [Desteklenen senaryolar](trusted-security-partners.md#key-scenarios)bölümüne bakın.
10. Hub 'da yalnızca bir üçüncü taraf güvenlik sağlayıcısını dağıtmak istiyorsanız, **Azure Güvenlik Duvarı: etkin/devre** **dışı olarak** ayarlamak için devre dışı seçeneğini belirleyin. 
11. **İleri ' yi seçin: güvenlik Iş ortağı sağlayıcısı**.
12. **Güvenlik Iş ortağı sağlayıcısını** **etkin** olarak ayarlayın. 
13. Bir iş ortağı seçin. 
14. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin. 
15. İçeriği gözden geçirin ve ardından **Oluştur**' u seçin.

VPN Gateway dağıtımı 30 dakikadan uzun sürebilir.

Hub 'ın oluşturulduğunu doğrulamak için Azure Güvenlik Duvarı Yöneticisi->güvenli hub 'Lara gidin. İş ortağı adını ve durumu **güvenlik bağlantısı beklemede** olarak göstermek için hub->genel bakış sayfasını seçin.

Hub oluşturulduktan ve güvenlik ortağı kurulduktan sonra, güvenlik sağlayıcısını hub 'a bağlamak için devam edin.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Mevcut bir hub 'da üçüncü taraf güvenlik sağlayıcısı dağıtma

Ayrıca, bir sanal WAN 'da var olan bir hub 'ı seçip *güvenli bir sanal hub*'a dönüştürebilirsiniz.

1. **Başlarken** bölümünde, **güvenli sanal hub 'ları görüntüle**' yi seçin.
2. **Varolan hub 'Ları Dönüştür**' ü seçin.
3. Bir abonelik ve var olan bir hub seçin. Yeni bir hub 'da üçüncü taraf bir sağlayıcı dağıtmak için adımların geri kalanını izleyin.

Mevcut bir hub 'ı, üçüncü taraf sağlayıcılardan oluşan güvenli hub 'a dönüştürmek için bir VPN ağ geçidinin dağıtılması gerektiğini unutmayın.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Güvenli bir hub 'a bağlanmak için üçüncü taraf güvenlik sağlayıcılarını yapılandırma

Sanal hub 'ın VPN Gateway tünellerini ayarlamak için, üçüncü taraf sağlayıcılarının hub 'ınız için erişim hakları olması gerekir. Bunu yapmak için, bir hizmet sorumlusunu aboneliğiniz veya kaynak grubuyla ilişkilendirin ve erişim hakları verin. Daha sonra bu kimlik bilgilerini, portalını kullanarak üçüncü tarafa vermeniz gerekir.

### <a name="create-and-authorize-a-service-principal"></a>Hizmet sorumlusu oluşturma ve yetkilendirme

1. Azure Active Directory (AD) hizmet sorumlusu oluşturma: yeniden yönlendirme URL 'sini atlayabilirsiniz. 

   [Nasıl yapılır: Kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Hizmet sorumlusu için erişim hakları ve kapsamı ekleyin.
   [Nasıl yapılır: Kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Daha ayrıntılı denetim için, erişimi yalnızca kaynak grubunuza göre sınırlayabilirsiniz.

### <a name="visit-partner-portal"></a>İş ortağı portalını ziyaret edin

1. Kurulumu tamamladıktan sonra iş ortağınızdan gelen yönergeleri izleyin. Bu, hubı algılayıp bağlanmak, çıkış ilkelerini güncelleştirmek ve bağlantı durumunu ve günlükleri denetlemek için AAD bilgilerini göndermeyi içerir.

   - [Zscaler: Microsoft Azure sanal WAN tümleştirmesini yapılandırın](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: Microsoft Azure sanal WAN tümleştirmesini yapılandırın](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [ıpatron: Microsoft Azure sanal WAN tümleştirmesini yapılandırın](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Azure 'da Azure sanal WAN portalındaki tünel oluşturma durumuna bakabilirsiniz. Tüneller hem Azure 'da hem de iş ortağı portalında **bağlı** olduktan sonra, hangi dalların ve sanal ağların Iş ortağına Internet trafiği gönderebilmelidir.

## <a name="configure-security-with-firewall-manager"></a>Güvenlik Duvarı Yöneticisi ile güvenliği yapılandırma

1. Azure Güvenlik Duvarı Yöneticisi-> güvenli hub 'Lara gidin. 
2. Bir hub seçin. Hub durumu artık **güvenlik bağlantısı bekleyen** yerine **sağlanmış** olarak gösterilmelidir.

   Üçüncü taraf sağlayıcının hub 'a bağlanabildiğinden emin olun. VPN ağ geçidinde bulunan tünellerin **bağlı** durumda olması gerekir. Bu durum, önceki durum ile karşılaştırıldığında merkez ve üçüncü taraf iş ortağı arasındaki bağlantı durumunun daha yansıtısıdır.
3. Hub ' ı seçin ve **Güvenlik Yapılandırması**' na gidin.

   Hub 'a bir üçüncü taraf sağlayıcı dağıttığınızda, hub 'ı *güvenli bir sanal hub*'a dönüştürür. Bu, üçüncü taraf sağlayıcının hub 'a bir 0.0.0.0/0 (varsayılan) yolu tanıtmasını sağlar. Ancak, bu varsayılan yolu almak zorunda kalmadığınız müddetçe hub 'a bağlı VNet bağlantıları ve siteleri bu yolu almaz.
4. **Internet trafiğini** , güvenilir bir güvenlik iş ortağı aracılığıyla Azure Güvenlik Duvarı ve **özel trafik** aracılığıyla ayarlayarak sanal WAN güvenliğini yapılandırın. Bu, sanal WAN 'daki tek tek bağlantıları otomatik olarak korur.

   :::image type="content" source="media/deploy-trusted-security-partner/security-configuration.png" alt-text="Güvenlik yapılandırması":::
5. Ayrıca, kuruluşunuz sanal ağlarda ve şubelerde ortak IP aralıkları kullanıyorsa, **özel trafik öneklerini** kullanarak bu IP öneklerini açıkça belirtmeniz gerekir. Genel IP adresi önekleri ayrı ayrı veya toplamalar olarak belirtilebilir.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Üçüncü taraf hizmet aracılığıyla şube veya VNet Internet trafiği

Daha sonra, VNet sanal makinelerinin veya şube sitesinin Internete erişip erişemayacağını denetleyebilir ve trafiğin üçüncü taraf hizmetine akan olduğunu doğrulayabilirsiniz.

Yol ayarı adımlarını tamamladıktan sonra, sanal ağ sanal makineleri ve şube siteleri, üçüncü taraf hizmet rotasında 0/0 olarak gönderilir. RDP veya SSH 'yi bu sanal makinelere kaydedemezsiniz. Oturum açmak için, [Azure](../bastion/bastion-overview.md) savunma hizmetini eşlenmiş bir sanal ağa dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure portal kullanarak bulut ağınızı Azure Güvenlik Duvarı Yöneticisi ile güvenli hale getirin](secure-cloud-network.md)