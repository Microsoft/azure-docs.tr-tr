---
title: Azure Güvenlik Duvarı Yöneticisi güvenilen güvenlik iş ortağı dağıtma
description: Azure portal kullanarak bir Azure Güvenlik Duvarı Yöneticisi güvenilen güvenliği dağıtmayı öğrenin.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931314"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Güvenilen güvenlik iş ortağı dağıtma (önizleme)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Güvenlik Duvarı Yöneticisi 'ndeki *güvenilir güvenlik iş ortakları* , kullanıcılarınız için Internet erişimini korumak üzere tanıdık, en uygun, yerleşik üçüncü taraf güvenlik (SECaaS) tekliflerini kullanmanıza olanak sağlar.

Desteklenen senaryolar ve en iyi yöntem yönergeleri hakkında daha fazla bilgi edinmek için bkz. [Güvenilen güvenlik ortakları (Önizleme) nedir?](trusted-security-partners.md).

Desteklenen güvenlik iş ortakları, bu önizleme için **Zscaler** ve **ıpatron** . Desteklenen bölgeler şunlardır WestCentralUS, kuzeydoğu, WestUS, WestUS2 ve EastUS.

## <a name="prerequisites"></a>Önkoşullar

> [!IMPORTANT]
> Azure Güvenlik Duvarı Yöneticisi önizlemenin `Register-AzProviderFeature` PowerShell komutu kullanılarak açıkça etkinleştirilmesi gerekir.

PowerShell komut isteminden aşağıdaki komutları çalıştırın:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. Kayıt durumunuzu denetlemek için şu komutu çalıştırın:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Yeni bir hub 'da üçüncü taraf güvenlik sağlayıcısı dağıtma

1. https://portal.azure.com adresinden Azure portalında oturum açın.
2. **Ara**bölümünde **güvenlik duvarı Yöneticisi** yazın ve **Hizmetler**altında seçin.
3. **Başlarken**' e gidin. **Güvenli sanal hub oluştur**' u seçin. 
4. Aboneliğinizi ve kaynak grubunu girin, desteklenen bir bölge seçin ve hub ve sanal WAN bilgilerinizi ekleyin. 
5. **VPN ağ geçidini dağıtma** varsayılan olarak etkindir. Bir güvenilen güvenlik ortağını hub 'a dağıtmak için bir VPN Gateway gereklidir. 
6. Ileri 'yi seçin **: Azure Güvenlik Duvarı**
   > [!NOTE]
   > Güvenilen güvenlik iş ortakları, VPN Gateway tünellerini kullanarak hub 'ınıza bağlanır. VPN Gateway silerseniz, güvenilen güvenlik iş ortaklarınıza olan bağlantılar kaybedilir.
7. Internet trafiğini filtrelemek için üçüncü taraf hizmet sağlayıcısıyla birlikte özel trafiği filtrelemek üzere Azure Güvenlik Duvarı dağıtmak istiyorsanız, Azure Güvenlik Duvarı için bir ilke seçin. [Desteklenen senaryolar](trusted-security-partners.md#key-scenarios)bölümüne bakın.
8. Hub 'da yalnızca bir üçüncü taraf güvenlik sağlayıcısını dağıtmak istiyorsanız, **Azure Güvenlik Duvarı: etkin/devre** **dışı olarak**ayarlamak için devre dışı seçeneğini belirleyin. 
9. **İleri ' yi seçin: güvenilen güvenlik Iş ortakları**.
10. **Etkin**olarak ayarlamak Için **güvenilir güvenlik ortağı** ' nı seçin. Bir iş ortağı seçin. 
11. **İleri**’yi seçin. 
12. İçeriği gözden geçirin ve ardından **Oluştur**' u seçin.

VPN Gateway dağıtımı 30 dakikadan uzun sürebilir.

Hub 'ın oluşturulduğunu doğrulamak için Azure Güvenlik Duvarı Yöneticisi-> güvenli hub 'Lara gidin. İş ortağı adını ve durumu **güvenlik bağlantısı beklemede**olarak göstermek için hub-> genel bakış sayfasını seçin.

Hub oluşturulduktan ve güvenlik ortağı kurulduktan sonra, güvenlik sağlayıcısını hub 'a bağlamak için devam edin.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Mevcut bir hub 'da üçüncü taraf güvenlik sağlayıcısı dağıtma

Ayrıca, bir sanal WAN 'da var olan bir hub 'ı seçip *güvenli bir sanal hub*'a dönüştürebilirsiniz.

1. **Başlarken**' de **var olan hub 'ları Dönüştür**' ü seçin.
2. Bir abonelik ve var olan bir hub seçin. Yeni bir hub 'da üçüncü taraf bir sağlayıcı dağıtmak için adımların geri kalanını izleyin.

Mevcut bir hub 'ı, üçüncü taraf sağlayıcılardan oluşan güvenli hub 'a dönüştürmek için bir VPN ağ geçidinin dağıtılması gerektiğini unutmayın.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Güvenli bir hub 'a bağlanmak için üçüncü taraf güvenlik sağlayıcılarını yapılandırma

Sanal hub 'ın VPN Gateway tünellerini ayarlamak için, üçüncü taraf sağlayıcılarının hub 'ınız için erişim hakları olması gerekir. Bunu yapmak için, bir hizmet sorumlusunu aboneliğiniz veya kaynak grubuyla ilişkilendirin ve erişim hakları verin. Daha sonra bu kimlik bilgilerini, portalını kullanarak üçüncü tarafa vermeniz gerekir.

1. Azure Active Directory (AD) hizmet sorumlusu oluşturma: yeniden yönlendirme URL 'sini atlayabilirsiniz. 

   [Nasıl yapılır: kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Hizmet sorumlusu için erişim hakları ve kapsamı ekleyin.
   [Nasıl yapılır: kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Daha ayrıntılı denetim için, erişimi yalnızca kaynak grubunuza göre sınırlayabilirsiniz.
3. [Zscaler: Microsoft Azure sanal WAN tümleştirme yönergelerini yapılandırmak](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) için şu adımları izleyin:

   - İş ortağı portalında oturum açın ve güvenilen hub 'ınıza güvenilir iş ortağı erişimi sağlamak için kimlik bilgilerinizi ekleyin.
   - İş ortağı portalındaki sanal hub 'ları eşitleyin ve sanal hub 'a tüneli ayarlayın. Azure AD kimlik doğrulama kimlik bilgileriniz doğrulandıktan sonra bunu yapabilirsiniz.
   
4. Azure 'da Azure sanal WAN portalındaki tünel oluşturma durumuna bakabilirsiniz. Tüneller hem Azure 'da hem de iş ortağı portalında **bağlı** olduktan sonra, hangi dalların ve sanal ağların Iş ortağına Internet trafiği gönderebilmelidir.

## <a name="configure-route-settings"></a>Yol ayarlarını yapılandırma

1. Azure Güvenlik Duvarı Yöneticisi-> güvenli hub 'Lara gidin. 
2. Bir hub seçin. Hub durumu artık **güvenlik bağlantısı bekleyen**yerine **sağlanmış** olarak gösterilmelidir.

   Üçüncü taraf sağlayıcının hub 'a bağlanabildiğinden emin olun. VPN ağ geçidinde bulunan tünellerin **bağlı** durumda olması gerekir. Bu durum, önceki durum ile karşılaştırıldığında merkez ve üçüncü taraf iş ortağı arasındaki bağlantı durumunun daha yansıtısıdır.
3. Hub ' ı seçin ve **yol ayarları**' na gidin.

   Hub 'a bir üçüncü taraf sağlayıcı dağıttığınızda, hub 'ı *güvenli bir sanal hub*'a dönüştürür. Bu, üçüncü taraf sağlayıcının hub 'a bir 0.0.0.0/0 (varsayılan) yolu tanıtmasını sağlar. Ancak, bu varsayılan yolu almak zorunda kalmadığınız müddetçe hub 'a bağlı VNet bağlantıları ve siteleri bu yolu almaz.
4. **İnternet trafiği**altında, rotalar üçüncü taraf ile **Internet 'e** veya **daldan Internet** ' i veya her ikisini birden seçin.

   Bu yalnızca, hub 'a hangi trafik türünün yönlendirildiğini gösterir, ancak henüz VNET veya dallardaki yolları etkilemez. Bu yollar, varsayılan olarak hub 'a bağlı tüm sanal ağlara/dallara yayılmaz.
5. **Güvenli bağlantılar** ' ı seçmeniz ve bu yolların ayarlanması gereken bağlantıları seçmeniz gerekir. Bu, üçüncü taraf sağlayıcıya Internet trafiği göndermeye başlayabileceği sanal ağlar/dallar olduğunu gösterir.
6. **Yönlendirme ayarları**' ndan Internet trafiği altında **güvenli bağlantılar** ' ı seçin, ardından güvenli hale getirilmekte olan VNET veya dalları (sanal WAN 'da*siteler* ) seçin. **Güvenli Internet trafiği**' ni seçin.
   Güvenli Internet trafiği ![](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Hub 'ları sayfasına geri gidin. Hub 'ın **Güvenilen güvenlik iş ortağı** durumu artık **güvenli**olmalıdır.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Üçüncü taraf hizmet aracılığıyla şube veya VNet Internet trafiği

Daha sonra, VNet sanal makinelerinin veya şube sitesinin Internete erişip erişemayacağını denetleyebilir ve trafiğin üçüncü taraf hizmetine akan olduğunu doğrulayabilirsiniz.

Yol ayarı adımlarını tamamladıktan sonra, sanal ağ ve şube siteleri de bir 0/0 üçüncü taraf hizmet yoluna gönderilir. RDP veya SSH 'yi bu sanal makinelere kaydedemezsiniz. Oturum açmak için, [Azure](../bastion/bastion-overview.md) savunma hizmetini eşlenmiş bir sanal ağa dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure portal kullanarak bulut ağınızı Azure Güvenlik Duvarı Yöneticisi önizleme ile koruyun](secure-cloud-network.md)




