---
title: Azure PowerShell kullanarak Azure AD Domain Services kaynak ormanı oluşturma | Microsoft Docs
description: Bu makalede Azure PowerShell kullanarak şirket içi Active Directory Domain Services ortamına Azure Active Directory Domain Services kaynak ormanı ve giden orman oluşturma ve yapılandırma hakkında bilgi edinin.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619976"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Azure PowerShell kullanarak bir şirket içi etki alanına Azure Active Directory Domain Services kaynak ormanı ve giden orman güveni oluşturma

Parola karmalarını eşitleyemeyen ortamlarda veya parolasını bilmiyor olmaları için akıllı kartlar kullanarak özel olarak oturum açma kullanıcılarına sahip olduğunuz ortamlarda, Azure Active Directory Domain Services (Azure AD DS) içinde bir kaynak ormanı kullanabilirsiniz. Kaynak ormanı, Azure AD DS bir veya daha fazla şirket içi AD DS ortamına tek yönlü bir giden güven kullanır. Bu güven ilişkisi, kullanıcıların, uygulamaların ve bilgisayarların Azure AD DS yönetilen etki alanındaki şirket içi bir etki alanında kimlik doğrulaması yapmasını sağlar. Bir kaynak ormanında, şirket içi parola karmaları hiçbir zaman eşitlenmez.

![Azure AD DS şirket içi AD DS orman güveninin diyagramı](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure PowerShell kullanarak Azure AD DS kaynak ormanı oluşturma
> * Yönetilen etki alanında Azure PowerShell kullanarak tek yönlü bir giden orman güveni oluşturun
> * Yönetilen etki alanı bağlantısını desteklemek için bir şirket içi AD DS ortamında DNS yapılandırma
> * Şirket içi AD DS ortamında tek yönlü bir gelen orman güveni oluşturma
> * Kimlik doğrulama ve kaynak erişimi için güven ilişkisini test edin ve doğrulayın

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> [!IMPORTANT]
> Yönetilen etki alanı kaynak ormanları Şu anda Azure HDInsight veya Azure dosyalarını desteklememektedir. Varsayılan yönetilen etki alanı kullanıcı ormanları, bu ek hizmetlerden her ikisini de destekler.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].

* Azure PowerShell'i yükleyip yapılandırın.
    * Gerekirse, [Azure PowerShell modülünü yüklemek ve Azure aboneliğinize bağlanmak](/powershell/azure/install-az-ps)için yönergeleri izleyin.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet 'Ini kullanarak Azure aboneliğinizde oturum açın.
* Azure AD PowerShell 'i yükleyip yapılandırın.
    * Gerekirse, [Azure AD PowerShell modülünü yüklemek ve Azure AD 'ye bağlanmak](/powershell/azure/active-directory/install-adv2)için yönergeleri izleyin.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açarak emin olun.
* Azure AD DS 'yi etkinleştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.
* Gerekli Azure AD DS kaynaklarını oluşturmak için Azure aboneliğinizde *katılımcı* ayrıcalıklarına sahip olmanız gerekir.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu makalede, Azure portal kullanarak, yönetilen bir etki alanından giden orman güvenini oluşturup yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="deployment-process"></a>Dağıtım süreci

Yönetilen bir etki alanı kaynak ormanı ve şirket içi AD DS güven ilişkisini oluşturmak için çok parçalı bir işlemdir. Aşağıdaki üst düzey adımlarda güvenilir, karma ortamınız oluşturun:

1. Yönetilen bir etki alanı hizmet sorumlusu oluşturun.
1. Yönetilen bir etki alanı kaynak ormanı oluşturun.
1. Siteden siteye VPN veya Express Route kullanarak karma ağ bağlantısı oluşturun.
1. Güven ilişkisinin yönetilen etki alanı tarafını oluşturun.
1. Güven ilişkisinin şirket içi AD DS tarafını oluşturun.

Başlamadan önce, [ağ konularını, orman adlandırmayı ve DNS gereksinimlerini](tutorial-create-forest-trust.md#networking-considerations)anladığınızdan emin olun. Yönetilen etki alanı orman adını dağıtıldıktan sonra değiştiremezsiniz.

## <a name="create-the-azure-ad-service-principal"></a>Azure AD hizmet sorumlusu oluşturma

Azure AD DS, Azure AD 'den bir hizmet sorumlusu veri eşitlemesi gerektirir. Yönetilen etki alanı kaynak ormanı 'nı oluşturmadan önce bu sorumlunun Azure AD kiracınızda oluşturulması gerekir.

Azure AD DS ile iletişim kurmak ve kimliğini doğrulamak için bir Azure AD hizmet sorumlusu oluşturun. Belirli bir uygulama KIMLIĞI, adı *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* olan *etki alanı denetleyicisi Hizmetleri* olarak kullanılır. Bu uygulama KIMLIĞINI değiştirmeyin.

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 'ini kullanarak BIR Azure AD hizmet sorumlusu oluşturun:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Yönetilen etki alanı kaynak ormanı oluşturma

Yönetilen bir etki alanı kaynak ormanı oluşturmak için `New-AzureAaddsForest` betiği kullanın. Bu komut dosyası, aşağıdaki bölümde tek yönlü bir bağlanan orman oluşturma dahil olmak üzere, yönetilen etki alanı kaynak ormanları oluşturmayı ve yönetmeyi destekleyen, daha geniş bir komut kümesinin bir parçasıdır. Bu komut dosyaları [PowerShell Galerisi](https://www.powershellgallery.com/) kullanılabilir ve Azure AD Mühendisliği ekibi tarafından dijital olarak imzalanır.

1. İlk olarak, [New-AzResourceGroup][New-AzResourceGroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnekte, kaynak grubu *Myresourcegroup* olarak adlandırılır ve *westus* bölgesinde oluşturulur. Kendi adınızı ve istediğiniz bölgeyi kullanın:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. `New-AaddsResourceForestTrust` [Install-Script][Install-Script] cmdlet 'ini kullanarak [PowerShell Galerisi][powershell-gallery] betiği yükler:

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Betiği için gereken aşağıdaki parametreleri gözden geçirin `New-AzureAaddsForest` . Önkoşul **Azure PowerShell** ve **Azure AD PowerShell** modüllerine de sahip olduğunuzdan emin olun. Uygulama ve şirket içi bağlantı sağlamak için sanal ağ gereksinimlerini planladığınızdan emin olun.

    | Name                         | Betik parametresi          | Açıklama |
    |:-----------------------------|---------------------------|:------------|
    | Abonelik                 | *-Azuyeniden gönderilirken Scriptionıd*    | Azure AD DS faturalandırma için kullanılan abonelik KIMLIĞI. [Get-AzureRMSubscription][Get-AzureRMSubscription] cmdlet 'ini kullanarak Aboneliklerin listesini alabilirsiniz. |
    | Kaynak Grubu               | *-aaddsResourceGroupName* | Yönetilen etki alanı ve ilişkili kaynaklar için kaynak grubunun adı. |
    | Konum                     | *-aaddsLocation*          | Yönetilen etki alanınızı barındıracak Azure bölgesi. Kullanılabilir bölgeler için bkz [. Azure AD DS için desteklenen bölgeler.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Azure AD DS Yöneticisi    | *-aaddsAdminUser*         | İlk yönetilen etki alanı yöneticisinin Kullanıcı asıl adı. Bu hesabın Azure Active Directory mevcut bir bulut Kullanıcı hesabı olması gerekir. Kullanıcı ve betiği çalıştıran kullanıcı *AAD DC Administrators* grubuna eklenir. |
    | Azure AD DS etki alanı adı      | *-aaddsDomainName*        | Bir orman adının nasıl kullanılacağına ilişkin önceki kılavuza bağlı olarak, yönetilen etki alanının FQDN 'SI. |

    `New-AzureAaddsForest`Bu kaynaklar zaten mevcut değilse betik Azure sanal ağını ve azure AD DS alt ağını oluşturabilir. Komut dosyası, belirtildiğinde, isteğe bağlı olarak iş yükü alt ağlarını oluşturabilir:

    | Name                              | Betik parametresi                  | Description |
    |:----------------------------------|:----------------------------------|:------------|
    | Sanal ağın adı              | *-Aaddsvbir ağ adı*                  | Yönetilen etki alanı için sanal ağın adı.|
    | Adres alanı                     | *-Aaddsvnetcıdraddressspace*      | CıDR gösteriminde sanal ağın adres aralığı (sanal ağ oluşturuluyoruz).|
    | Azure AD DS alt ağ adı           | *-aaddsSubnetName*                | Yönetilen etki alanını barındıran *Aaddsvbir* sanal ağın alt ağının adı. Kendi sanal makinelerinizi ve iş yüklerinizi bu alt ağa dağıtmayın. |
    | Azure AD DS adres aralığı         | *-aaddsSubnetCIDRAddressRange*    | Örneğin *192.168.1.0/24* gıbı AAD DS ÖRNEĞI için CIDR gösteriminde alt ağ adres aralığı. Adres aralığı, sanal ağın adres aralığı ve diğer alt ağlardan farklı olmalıdır. |
    | İş yükü alt ağ adı (isteğe bağlı)   | *-workloadSubnetName*             | Kendi uygulama iş yükleriniz için oluşturulacak *Aaddsv,* sanal ağındaki bir alt ağın isteğe bağlı adı. VM 'Ler ve uygulamalar, bunun yerine eşlenmiş bir Azure sanal ağına da bağlanır. |
    | İş yükü adres aralığı (isteğe bağlı) | *-workloadSubnetCIDRAddressRange* | *192.168.2.0/24* gibi uygulama iş yükü için CIDR gösteriminde isteğe bağlı alt ağ adres aralığı. Adres aralığı, sanal ağın adres aralığı ve diğer alt ağlardan farklı olmalıdır.|

1. Şimdi betiği kullanarak bir yönetilen etki alanı kaynak ormanı oluşturun `New-AzureAaaddsForest` . Aşağıdaki örnek, *addscontoso.com* adlı bir orman oluşturur ve bir iş yükü alt ağı oluşturur. Kendi parametre adlarınızı ve IP adresi aralıklarını veya var olan sanal ağları sağlayın.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Yönetilen etki alanı kaynak ormanı ve destekleyici kaynakları oluşturmak biraz zaman alır. Betiğin tamamlanmasına izin verin. Azure AD kaynak ormanı arka planda sağlarken şirket içi ağ bağlantınızı yapılandırmak için sonraki bölüme geçin.

## <a name="configure-and-validate-network-settings"></a>Ağ ayarlarını yapılandırma ve doğrulama

Yönetilen etki alanı, şirket içi veri merkezine karma ağ bağlantısını dağıtmaya, yapılandırmaya ve doğrulamaya devam etmektedir. Ayrıca, düzenli bakım için yönetilen etki alanı ile birlikte kullanmak üzere bir yönetim sanal makinesi gerekir. Karma bağlantının bir kısmı ortamınızda zaten bulunabilir veya bağlantıları yapılandırmak için ekibinizdeki diğer kullanıcılarla çalışmanız gerekebilir.

Başlamadan önce, [ağ konularını ve önerileri](tutorial-create-forest-trust.md#networking-considerations)anladığınızdan emin olun.

1. Azure VPN veya Azure ExpressRoute bağlantısı kullanarak şirket içi ağınız ile Azure arasında karma bağlantı oluşturun. Karma ağ yapılandırması bu belge kapsamının ötesinde ve ortamınızda zaten var olabilir. Belirli senaryolar hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

    * [Azure siteden sıteye VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Azure ExpressRoute 'A genel bakış](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Bağlantıyı, yönetilen etki alanının sanal ağına doğrudan oluşturursanız, ayrı bir ağ geçidi alt ağı kullanın. Yönetilen etki alanının alt ağında ağ geçidini oluşturmayın.

1. Yönetilen bir etki alanını yönetmek için bir yönetim sanal makinesi oluşturun, onu yönetilen etki alanına ekleyin ve gerekli AD DS yönetim araçlarını yüklersiniz.

    Yönetilen etki alanı kaynak ormanı dağıtılırken, [bir Windows Server VM oluşturun](./join-windows-vm.md) , ardından gerekli yönetim araçlarını yüklemek için [çekirdek AD DS yönetim araçlarını yükler](./tutorial-create-management-vm.md) . Etki alanı başarıyla dağıtıldıktan sonra aşağıdaki adımlardan birine kadar yönetim sanal makinesini yönetilen etki alanına katılmayı bekleyin.

1. Şirket içi ağınız ve Azure sanal ağı arasındaki ağ bağlantısını doğrulayın.

    * Şirket içi etki alanı denetleyicinizin, veya Uzak Masaüstü kullanarak yönetilen VM 'ye bağlanabildiğini onaylayın ( `ping` Örneğin,).
    * Yönetim sanal makinenizin, gibi bir yardımcı programı kullanarak şirket içi etki alanı denetleyicilerinize bağlanıp bağlanamadiğini doğrulayın `ping` .

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin ve durumu **çalışıyor** olarak raporlamak için bekleyin.

    Çalışırken, [Azure sanal ağı IÇIN DNS ayarlarını güncelleştirin](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) ve ardından, yönetilen etki alanı kaynak ormanınızın yapılandırmasını sonuçlandırmak için [Azure AD DS kullanıcı hesaplarını etkinleştirin](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) .

1. Genel Bakış sayfasında gösterilen DNS adreslerini bir yere göz atın. Aşağıdaki bölümde güven ilişkisinin şirket içi Active Directory tarafını yapılandırırken bu adreslere ihtiyacınız vardır.
1. Yeni DNS ayarlarını almak için yönetim VM 'sini yeniden başlatın ve ardından VM 'yi [yönetilen etki alanına ekleyin](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Yönetim sanal makinesi yönetilen etki alanına katıldıktan sonra, Uzak Masaüstü kullanarak yeniden bağlanın.

    `nslookup`Kaynak ormanı için ad çözümlemesini doğrulamak üzere bir komut isteminden ve yönetilen etki alanı kaynak ormanı adı ' nı kullanın.

    ```console
    nslookup aaddscontoso.com
    ```

    Komut, kaynak ormanı için iki IP adresi döndürmelidir.

## <a name="create-the-forest-trust"></a>Orman güvenini oluşturma

Orman güveninin iki bölümü vardır-yönetilen etki alanı kaynak ormanında tek yönlü bir orman güveni ve şirket içi AD DS ormanında tek yönlü gelen orman güveni. Bu güven ilişkisinin her iki tarafını da el ile oluşturursunuz. Her iki taraf oluşturulduğunda, kullanıcılar ve kaynaklar orman güvenini kullanarak kimlik doğrulaması başarılı olabilir. Yönetilen bir etki alanı kaynak ormanı, şirket içi ormanlarda en fazla 5 1 yönlü giden orman güvenlerini destekler.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Güven ilişkisinin yönetilen etki alanı tarafını oluşturma

`Add-AaddsResourceForestTrust`Güven ilişkisinin yönetilen etki alanı tarafını oluşturmak için betiği kullanın. İlk olarak, `Add-AaddsResourceForestTrust` [Install-Script][Install-Script] cmdlet 'ini kullanarak [PowerShell Galerisi][powershell-gallery] betiği yüklemeniz gerekir:

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Şimdi aşağıdaki bilgileri komut dosyasına girin:

| Name                               | Betik parametresi     | Description |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS etki alanı adı            | *-ManagedDomainFqdn* | Yönetilen etki alanının FQDN 'SI (örneğin, *aaddscontoso.com* ) |
| Şirket içi AD DS etki alanı adı      | *-TrustFqdn*         | *OnPrem.contoso.com* gibi güvenilir ormanın FQDN 'si |
| Güven kolay adı                | *-TrustFriendlyName* | Güven ilişkisinin kolay adı. |
| Şirket içi AD DS DNS IP adresleri | *-TrustDnsIPs*       | Listelenen güvenilen etki alanı için DNS sunucusu IPv4 adreslerinin virgülle ayrılmış listesi. |
| Güven parolası                     | *-TrustPassword*     | Güven ilişkisi için karmaşık bir parola. Bu parola, şirket içi AD DS tek yönlü gelen güveni oluştururken de girilir. |
| Kimlik bilgileri                        | *-Kimlik bilgileri*       | Azure 'da kimlik doğrulamak için kullanılan kimlik bilgileri. Kullanıcı *AAD DC Administrators grubunda* olmalıdır. Sağlanmazsa, betik kimlik doğrulaması ister. |

Aşağıdaki örnek, *OnPrem.contoso.com* Için *Myazureaddstrust* adlı bir güven ilişkisi oluşturur. Kendi parametre adlarınızı ve parolalarınızı kullanın:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Güven parolanızı unutmayın. Güvenin şirket içi tarafını oluştururken aynı parolayı kullanmanız gerekir.

## <a name="configure-dns-in-the-on-premises-domain"></a>Şirket içi etki alanında DNS 'yi yapılandırma

Yönetilen etki alanını şirket içi ortamdan doğru bir şekilde çözümlemek için, mevcut DNS sunucularına ileticiler eklemeniz gerekebilir. Şirket içi ortamı yönetilen etki alanıyla iletişim kuracak şekilde yapılandırmadıysanız, şirket içi AD DS etki alanı için bir yönetim iş istasyonundan aşağıdaki adımları uygulayın:

1. Başlat 'ı seçin **| Yönetim Araçları | DNS**
1. *MyAD01* gibi DNS sunucusunu sağ seçin, **Özellikler** ' i seçin
1. **İleticiler**' i seçin ve ardından başka ileticiler eklemek için **düzenleyin** .
1. *10.0.1.4* ve *10.0.1.5* gıbı yönetilen etki alanının IP adreslerini ekleyin.
1. Yerel bir komut isteminden, yönetilen etki alanı kaynak ormanı etki alanı adının **nslookup** 'ı kullanarak ad çözümlemesini doğrulayın. Örneğin, `Nslookup aaddscontoso.com` yönetilen etki alanı kaynak ormanı için ıkı IP adresi döndürmelidir.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Şirket içi etki alanında gelen orman güveni oluşturma

Şirket içi AD DS etki alanı, yönetilen etki alanı için gelen bir orman güveni gerektirir. Bu güvenin şirket içi AD DS etki alanında el ile oluşturulması gerekir, bu, Azure portal oluşturulamıyor.

Şirket içi AD DS etki alanında gelen güveni yapılandırmak için, şirket içi AD DS etki alanı için bir yönetim iş istasyonundan aşağıdaki adımları uygulayın:

1. Başlat 'ı seçin **| Yönetim Araçları | Active Directory etki alanları ve Güvenleri**
1. *OnPrem.contoso.com* gibi etki alanını sağ seçin, **Özellikler** ' i seçin
1. **Güvenler** sekmesini ve ardından **yeni güven** ' i seçin
1. Yönetilen etki alanının adını (örneğin, *aaddscontoso.com*) girin ve ardından **İleri** ' yi seçin.
1. Bir **orman güveni** oluşturma, sonra bir **yol oluşturma: gelen** güven seçeneğini belirleyin.
1. **Yalnızca bu etki alanı** için güven oluşturmayı seçin. Bir sonraki adımda, yönetilen etki alanı için Azure portal güveni oluşturursunuz.
1. **Orman genelinde kimlik doğrulaması** kullanmayı seçin, ardından bir güven parolasını girip onaylayın. Aynı parola, sonraki bölümde Azure portal de girilir.
1. Varsayılan seçeneklerde bir sonraki birkaç pencere arasında ilerleyin, ardından **Hayır, giden güveni onaylama** seçeneğini belirleyin. Yönetilen etki alanı kaynak ormanına atanan yönetici hesabınız gerekli izinlere sahip olmadığından güven ilişkisini doğrulayamazsınız. Bu davranış tasarım gereğidir.
1. **Son** ' u seçin

## <a name="validate-resource-authentication"></a>Kaynak kimlik doğrulamasını doğrulama

Aşağıdaki yaygın senaryolar, orman güveninin kullanıcıları ve kaynaklara erişimi doğru şekilde doğruladığını doğrulamanızı sağlar:

* [Azure AD DS kaynak ormanında şirket içi kullanıcı kimlik doğrulaması](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Şirket içi kullanıcı kullanarak Azure AD DS kaynak ormanındaki kaynaklara erişme](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Dosya ve yazıcı paylaşımını etkinleştir](#enable-file-and-printer-sharing)
    * [Güvenlik grubu oluşturma ve üye ekleme](#create-a-security-group-and-add-members)
    * [Ormanlar arası erişim için bir dosya paylaşma oluşturma](#create-a-file-share-for-cross-forest-access)
    * [Ormanlar arası kimlik doğrulamasını bir kaynağa doğrulama](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS kaynak ormanında şirket içi kullanıcı kimlik doğrulaması

Yönetilen etki alanı kaynak etki alanına katılmış Windows Server sanal makinenizin olması gerekir. Şirket içi kullanıcılarınızın bir sanal makinede kimlik doğrulaması yapabilmeleri için bu sanal makineyi kullanın.

1. Uzak Masaüstü ve yönetilen etki alanı yönetici kimlik bilgilerinizi kullanarak yönetilen etki alanı kaynak ormanına katılmış Windows Server VM 'sine bağlanın. Ağ Düzeyinde Kimlik Doğrulama (NLA) hatası alırsanız, kullandığınız kullanıcı hesabının bir etki alanı kullanıcı hesabı olmadığını kontrol edin.

    > [!TIP]
    > Azure AD Domain Services ' a katılmış sanal makinelerinize güvenli bir şekilde bağlanmak için, desteklenen Azure bölgelerinde [Azure savunma ana bilgisayarı hizmetini](../bastion/bastion-overview.md) kullanabilirsiniz.

1. Bir komut istemi açın ve `whoami` Şu anda kimliği doğrulanmış kullanıcının ayırt edici adını göstermek için komutunu kullanın:

    ```console
    whoami /fqdn
    ```

1. `runas`Şirket içi etki alanından bir kullanıcı olarak kimlik doğrulaması yapmak için komutunu kullanın. Aşağıdaki komutta, yerine güvenilen Şirket `userUpn@trusteddomain.com` içi etki alanındaki bir kullanıcının UPN 'si ile değiştirin. Komut sizden kullanıcının parolasını ister:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Kimlik doğrulaması başarılı olursa yeni bir komut istemi açılır. Yeni komut isteminin başlığı içerir `running as userUpn@trusteddomain.com` .
1. `whoami /fqdn`Şirket içi Active Directory kimliği doğrulanmış kullanıcının ayırt edici adını görüntülemek için yeni komut isteminde kullanın.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Şirket içi kullanıcı kullanarak Azure AD DS kaynak ormanındaki kaynaklara erişme

Yönetilen etki alanı kaynak ormanına katılmış Windows Server VM 'sini kullanarak, kullanıcıların şirket içi etki alanındaki kullanıcılarla şirket içi etki alanındaki bilgisayarlardan kimlik doğrulamasından geçmesini sağlamak için kaynak ormanında barındırılan bir senaryoya test edebilirsiniz. Aşağıdaki örneklerde çeşitli yaygın senaryolar oluşturma ve test etme işlemlerinin nasıl yapılacağı gösterilmektedir.

#### <a name="enable-file-and-printer-sharing"></a>Dosya ve yazıcı paylaşımını etkinleştir

1. Uzak Masaüstü ve yönetilen etki alanı yönetici kimlik bilgilerinizi kullanarak yönetilen etki alanı kaynak ormanına katılmış Windows Server VM 'sine bağlanın. Ağ Düzeyinde Kimlik Doğrulama (NLA) hatası alırsanız, kullandığınız kullanıcı hesabının bir etki alanı kullanıcı hesabı olmadığını kontrol edin.

    > [!TIP]
    > Azure AD Domain Services ' a katılmış sanal makinelerinize güvenli bir şekilde bağlanmak için, desteklenen Azure bölgelerinde [Azure savunma ana bilgisayarı hizmetini](../bastion/bastion-overview.md) kullanabilirsiniz.

1. **Windows ayarları**' nı açın ve **Ağ ve Paylaşım Merkezi**' ni arayıp seçin.
1. **Gelişmiş paylaşım ayarlarını değiştir** seçeneğini belirleyin.
1. **Etki alanı profili** altında **dosya ve yazıcı paylaşımını aç** ' ı seçin ve **değişiklikleri kaydedin**.
1. **Ağ ve Paylaşım Merkezi 'ni** kapatın.

#### <a name="create-a-security-group-and-add-members"></a>Güvenlik grubu oluşturma ve üye ekleme

1. **Active Directory Kullanıcıları ve Bilgisayarları**'nı açın.
1. Etki alanı adını sağ seçin, **Yeni**' yi seçin ve ardından **kuruluş birimi**' ni seçin.
1. Ad kutusuna *Localobjects* yazın ve ardından **Tamam**' ı seçin.
1. Gezinti bölmesinde **Localobjects** ' i seçin ve sağ tıklayın. **Yeni** ' yi ve ardından **Grup**' u seçin.
1. **Grup adı** kutusuna *fileserveraccess* yazın. **Grup kapsamı** Için **etki alanı yerel**' i seçin ve ardından **Tamam**' ı seçin.
1. İçerik bölmesinde, **Fileserveraccess**' e çift tıklayın. **Üyeler**' i seçin, **eklemeyi** seçin ve ardından **konumlar**' ı seçin.
1. **Konum** görünümünden şirket içi Active Directory seçin ve ardından **Tamam**' ı seçin.
1. **Seçilecek nesne adlarını girin** kutusunda *etki alanı kullanıcıları* yazın. **Adları denetle**' yi seçin, şirket içi Active Directory kimlik bilgilerini sağlayın ve ardından **Tamam**' ı seçin.

    > [!NOTE]
    > Güven ilişkisi yalnızca tek bir yol olduğundan kimlik bilgilerini sağlamanız gerekir. Bu, yönetilen etki alanındaki kullanıcıların kaynaklara erişemeyeceği veya güvenilir (Şirket içi) etki alanındaki kullanıcıları veya grupları araymayacağı anlamına gelir.

1. Şirket içi Active Directory **etki alanı kullanıcıları** grubu, **fileserveraccess** grubunun bir üyesi olmalıdır. Grubu kaydetmek ve pencereyi kapatmak için **Tamam** ' ı seçin.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Ormanlar arası erişim için bir dosya paylaşma oluşturma

1. Yönetilen etki alanı kaynak ormanına katılmış Windows Server VM 'de, bir klasör oluşturun ve *Crossforestshare* gibi bir ad sağlayın.
1. Klasörü sağ seçin ve **Özellikler**' i seçin.
1. **Güvenlik** sekmesini seçin ve ardından **Düzenle**' yi seçin.
1. *Çapraz Forestshare izinleri* Iletişim kutusunda **Ekle**' yi seçin.
1. **Seçilecek nesne adlarını girin** alanına *fileserveraccess* yazın ve ardından **Tamam**' ı seçin.
1. **Gruplar veya Kullanıcı adları** listesinden *fileserveraccess* ' i seçin. **FileServerAccess izinleri** listesinde, **değiştirme** ve **yazma** izinleri için *Izin ver* ' i seçin ve ardından **Tamam**' ı seçin.
1. **Paylaşım** sekmesini seçin ve **Gelişmiş paylaşım...** seçeneğini belirleyin.
1. **Bu klasörü paylaşma**' yı seçin, sonra da *çapraz Forestshare* gibi **Share adında** dosya paylaşımının hatırlayabileceğiniz bir adını girin.
1. **İzinler**'i seçin. **Herkes Için izinler** listesinde, **Değiştir** izni için **izin ver** ' i seçin.
1. **Tamam** ' ı iki kez seçin ve ardından **kapatın**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Ormanlar arası kimlik doğrulamasını bir kaynağa doğrulama

1. Şirket içi Active Directory bir kullanıcı hesabı kullanarak şirket içi Active Directory katılmış bir Windows bilgisayarda oturum açın.
1. **Windows Gezgini**'ni kullanarak, tam ana bilgisayar adını ve gibi bir paylaşma kullanarak oluşturduğunuz paylaşıma bağlanın `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Yazma iznini doğrulamak için, klasörde sağ seçin, **Yeni**' yi seçin ve ardından **metin belgesi**' ni seçin. Varsayılan ad **Yeni metin belgesi ' ni** kullanın.

    Yazma izinleri doğru ayarlandıysa yeni bir metin belgesi oluşturulur. Aşağıdaki adımlar, dosyayı uygun şekilde açar, düzenleyebilir ve siler.
1. Oku iznini doğrulamak için **Yeni metin belgesi** açın.
1. Değiştirme iznini doğrulamak için, dosyaya metin ekleyin ve **Not defteri 'ni** kapatın. Değişiklikleri kaydetmeniz istendiğinde **Kaydet**' i seçin.
1. Silme iznini doğrulamak için **Yeni metin belgesi** ' ni sağ seçin ve **Sil**' i seçin. Dosya silmeyi onaylamak için **Evet** ' i seçin.

## <a name="update-or-remove-outbound-forest-trust"></a>Giden orman güvenini güncelleştirme veya kaldırma

Yönetilen etki alanından mevcut bir tek yönlü giden ormanı güncelleştirmeniz gerekiyorsa, `Get-AaddsResourceForestTrusts` ve `Set-AaddsResourceForestTrust` betiklerini kullanabilirsiniz. Bu betikler, orman güveni kolay adını veya güven parolasını güncelleştirmek istediğiniz senaryolarda yardımcı olur. Yönetilen etki alanından tek yönlü bir giden güveni kaldırmak için `Remove-AaddsResourceForestTrust` betiği kullanabilirsiniz. İlişkili şirket içi AD DS ormanında tek yönlü gelen orman güvenini el ile kaldırmanız gerekir.

### <a name="update-a-forest-trust"></a>Orman güvenini güncelleştirme

Normal işlemde, yönetilen etki alanı ormanı ve şirket içi orman, kendileri arasında düzenli bir parola güncelleştirme süreci üzerinde anlaşır. Bu, normal AD DS güven ilişkisi güvenlik sürecinin bir parçasıdır. Güven ilişkisi bir sorunla karşılaşmamışsa ve bilinen bir parolaya el ile sıfırlamak istiyorsanız güven parolasını el ile döndürmeniz gerekmez. Daha fazla bilgi için bkz. [Güvenilen etki alanı nesne parolası değişiklikleri](concepts-forest-trust.md#tdo-password-changes).

Aşağıdaki örnek adımlarda, giden güven parolasını el ile sıfırlamanız gerekiyorsa mevcut bir güven ilişkisinin nasıl güncelleşeceği gösterilmektedir:

1. `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` [Install-Script][Install-Script] cmdlet 'ini kullanarak [PowerShell Galerisi][powershell-gallery] ve betiklerini yükler:

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Mevcut bir güveni güncelleştirebilmeniz için önce betiği kullanarak güven kaynağını alın `Get-AaddsResourceForestTrusts` . Aşağıdaki örnekte, var olan güven *existingtrust* adlı bir nesneye atanır. Güncelleştirmek için kendi yönetilen etki alanı orman adınızı ve şirket içi orman adını belirtin:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Mevcut güven parolasını güncelleştirmek için `Set-AaddsResourceForestTrust` betiği kullanın. Önceki adımdan mevcut güven nesnesini, ardından yeni bir güven ilişkisi parolasını belirtin. PowerShell tarafından hiçbir parola karmaşıklığı zorlanmaz, bu nedenle ortamınız için güvenli bir parola oluşturup kullandığınızdan emin olun.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Orman güvenini silme

Yönetilen etki alanından şirket içi AD DS ormanına tek yönlü giden orman güveni yoksa, bunu kaldırabilirsiniz. Güveni kaldırmadan önce, şirket içi AD DS ormanında kimlik doğrulaması yapması gereken hiçbir uygulama veya hizmet olmadığından emin olun. Şirket içi AD DS ormanında tek yönlü gelen güveni de el ile kaldırmanız gerekir.

1. `Remove-AaddsResourceForestTrust` [Install-Script][Install-Script] cmdlet 'ini kullanarak [PowerShell Galerisi][powershell-gallery] betiği yükler:

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Şimdi betiği kullanarak orman güvenini kaldırın `Remove-AaddsResourceForestTrust` . Aşağıdaki örnekte, *aaddscontoso.com* ve şirket içi orman *OnPrem.contoso.com* adlı yönetilen etki alanı ormanı arasında *Myazureaddstrust* adlı güven kaldırılır. Kaldırmak için kendi yönetilen etki alanı orman adınızı ve şirket içi orman adını belirtin:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Şirket içi AD DS ormanından tek yönlü gelen güveni kaldırmak için, şirket içi AD DS ormanına erişimi olan bir yönetim bilgisayarına bağlanın ve aşağıdaki adımları uygulayın:

1. Başlat 'ı seçin **| Yönetim Araçları | Active Directory etki alanları ve Güvenleri**
1. *OnPrem.contoso.com* gibi etki alanını sağ seçin, **Özellikler** ' i seçin
1. **Güvenler** sekmesini seçin ve ardından yönetilen etki alanı ormanınızdan mevcut gelen güveni seçin.
1. **Kaldır**' ı seçin, ardından gelen güveni kaldırmak istediğinizi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Azure PowerShell kullanarak yönetilen etki alanı kaynak ormanı oluşturma
> * Yönetilen etki alanında Azure PowerShell kullanarak tek yönlü bir giden orman güveni oluşturun
> * Yönetilen etki alanı bağlantısını desteklemek için bir şirket içi AD DS ortamında DNS yapılandırma
> * Şirket içi AD DS ortamında tek yönlü bir gelen orman güveni oluşturma
> * Kimlik doğrulama ve kaynak erişimi için güven ilişkisini test edin ve doğrulayın

Azure AD DS orman türleri hakkında daha fazla kavramsal bilgi için bkz. [kaynak ormanları nelerdir?][concepts-forest] ve [orman güvenleri Azure AD DS nasıl çalışır?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/