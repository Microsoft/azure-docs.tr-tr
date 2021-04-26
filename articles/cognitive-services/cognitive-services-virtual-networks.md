---
title: Sanal Ağlar
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler kaynaklarınız için katmanlı ağ güvenliğini yapılandırın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: eaffa535b51b786a53f1e6cc35233c55dd837233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99989084"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure Bilişsel Hizmetler sanal ağlarını yapılandırma

Azure bilişsel hizmetler, katmanlı bir güvenlik modeli sağlar. Bu model, Bilişsel Hizmetler hesaplarınızı belirli bir ağ alt kümesinde güvenlik altına almanızı sağlar. Ağ kuralları yapılandırıldığında yalnızca belirli bir ağ kümesi üzerinden veri isteğinde bulunan uygulamalar hesaba erişim sağlayabilir. İstek filtreleme ile kaynaklarınıza erişimi sınırlayabilirsiniz. Yalnızca belirtilen IP adreslerinden, IP aralıklarından veya [Azure sanal ağlarındaki](../virtual-network/virtual-networks-overview.md)alt ağlar listesinden kaynaklanan isteklere izin verme.

Ağ kuralları etkin olduğunda bilişsel hizmetler kaynağına erişen bir uygulama yetkilendirme gerektirir. Yetkilendirme [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) kimlik bilgileriyle veya GEÇERLI bir API anahtarı ile desteklenir.

> [!IMPORTANT]
> Bilişsel hizmetler hesabınız için güvenlik duvarı kurallarını açmak, varsayılan olarak gelen istekleri engeller. Üzerinden isteklere izin vermek için aşağıdaki koşullardan birinin karşılanması gerekir:

> * İstek, hedef bilişsel hizmetler hesabının izin verilen alt ağ listesindeki bir Azure sanal ağı (VNet) içinde çalışan bir hizmetten kaynaklanmalıdır. VNet 'ten kaynaklı isteklerdeki uç noktanın, bilişsel hizmetler hesabınızın [özel alt etki alanı](cognitive-services-custom-subdomains.md) olarak ayarlanması gerekir.
> * Ya da istek, izin verilen bir IP adresi listesinden geliyor olmalıdır.
>
> Engellenen istekler diğer Azure hizmetlerinden, Azure portal, günlük ve ölçüm hizmetlerinden ve bu şekilde devam eder.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Senaryolar

Bilişsel hizmetler kaynağınızı güvenli hale getirmek için, önce varsayılan olarak tüm ağlardan (internet trafiği dahil) trafiğe erişimi reddetmek üzere bir kural yapılandırmanız gerekir. Ardından, belirli sanal ağlardan gelen trafiğe erişim veren kurallar yapılandırmanız gerekir. Bu yapılandırma, uygulamalarınız için güvenli bir ağ sınırı oluşturmanıza olanak sağlar. Ayrıca, belirli internet veya şirket içi istemcilerden gelen bağlantıları etkinleştirerek, genel İnternet IP adresi aralıklarından gelen trafiğe erişim izni vermek için kurallar da yapılandırabilirsiniz.

Ağ kuralları, REST ve WebSocket dahil olmak üzere Azure bilişsel hizmetler 'e yönelik tüm ağ protokollerinde zorlanır. Azure test konsolları gibi araçları kullanarak verilere erişmek için açık ağ kurallarının yapılandırılması gerekir. Mevcut bilişsel hizmetler kaynaklarına ağ kuralları uygulayabilir veya yeni bilişsel hizmetler kaynakları oluşturabilirsiniz. Ağ kuralları uygulandıktan sonra, tüm istekler için zorlanır.

## <a name="supported-regions-and-service-offerings"></a>Desteklenen bölgeler ve hizmet teklifleri

Sanal ağlar (VNet 'ler) bilişsel [Hizmetler 'in kullanılabildiği bölgelerde](https://azure.microsoft.com/global-infrastructure/services/)desteklenir. Bilişsel hizmetler, ağ kuralları yapılandırması için hizmet etiketlerini destekler. Aşağıda listelenen hizmetler, **Biliveservicesmanagement** hizmeti etiketinde bulunur.

> [!div class="checklist"]
> * Anomali Algılayıcısı
> * Görüntü İşleme
> * Content Moderator
> * Özel Görüntü
> * Yüz
> * Form Tanıma
> * Tam Ekran Okuyucu
> * Language Understanding (LUIS)
> * Kişiselleştirme
> * Konuşma Hizmetleri
> * Metin Analizi
> * Soru-Cevap Oluşturucu
> * Translator Metin Çevirisi


> [!NOTE]
> LUYA veya konuşma Hizmetleri kullanıyorsanız, **Biliveservicesmanagement** etiketi yalnızca SDK 'yı veya REST API kullanarak hizmeti kullanmanıza izin verebilir. Bir sanal ağdan HALSıS portalına ve/veya konuşma Studio 'ya erişmek ve bunları kullanmak için aşağıdaki etiketleri kullanmanız gerekir:  
> * **AzureActiveDirectory**
> * **Azurefrontkapısı. ön uç**
> * **AzureResourceManager** 
> * **Biliveservicesmanagement**



## <a name="change-the-default-network-access-rule"></a>Varsayılan ağ erişim kuralını değiştirme

Bilişsel hizmetler kaynakları, varsayılan olarak herhangi bir ağdaki istemcilerden gelen bağlantıları kabul eder. Seçili ağlara erişimi sınırlamak için öncelikle varsayılan eylemi değiştirmeniz gerekir.

> [!WARNING]
> Ağ kurallarında değişiklik yapmak, uygulamalarınızın Azure bilişsel hizmetler 'e bağlanma yeteneğini etkileyebilir. Varsayılan ağ kuralını **Reddet** olarak ayarlamak, erişim **izni** veren belirli ağ kuralları da uygulanmamışsa veriye tüm erişimi engeller. Varsayılan kuralı erişimi reddedecek şekilde değiştirmeden önce ağ kurallarını kullanarak izin verilen ağlara erişim verdiğinizden emin olun. Şirket içi Ağınızın IP adreslerini listelemesine izin verirseniz, tüm olası giden genel IP adreslerini şirket içi ağınızdan eklediğinizden emin olun.

### <a name="managing-default-network-access-rules"></a>Varsayılan ağ erişim kurallarını yönetme

Bilişsel hizmetler kaynakları için Azure portal, PowerShell veya Azure CLı aracılığıyla varsayılan ağ erişim kurallarını yönetebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ** ADLı **kaynak yönetimi** menüsünü seçin.

   ![Sanal ağ seçeneği](media/vnet/virtual-network-blade.png)

1. Erişimi varsayılan olarak reddetmek için, **Seçili ağlardan** erişime izin ver ' i seçin. Yalnızca **Seçili ağlar** ayarıyla, yapılandırılmış **sanal ağlar** veya **adres aralıkları** tarafından açıklanarak, tüm erişim etkin bir şekilde reddedilir. Tüm erişim reddedildiğinde, bilişsel hizmetler kaynağını tüketmeye çalışan isteklere izin verilmez. Azure portal, Azure PowerShell veya Azure CLı bilişsel hizmetler kaynağını yapılandırmak için yine de kullanılabilir.
1. Tüm ağlardan gelen trafiğe izin vermek için **Tüm ağlardan** erişime izin vermeyi seçin.

   ![Sanal ağlar reddetme](media/vnet/virtual-network-deny.png)

1. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps)veya **deneyin**' i seçin.

1. Bilişsel hizmetler kaynağı için varsayılan kuralın durumunu görüntüleyin.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Varsayılan olarak ağ erişimini reddetmek için varsayılan kuralı ayarlayın.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Varsayılan olarak ağ erişimine izin vermek için varsayılan kuralı ayarlayın.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli)veya **deneyin**' i seçin.

1. Bilişsel hizmetler kaynağı için varsayılan kuralın durumunu görüntüleyin.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Varsayılan olarak ağ erişimini reddetmek için varsayılan kuralı ayarlayın.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Varsayılan olarak ağ erişimine izin vermek için varsayılan kuralı ayarlayın.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Bir sanal ağdan erişim izni verme

Bilişsel hizmetler kaynaklarını yalnızca belirli alt ağlardan erişime izin verecek şekilde yapılandırabilirsiniz. İzin verilen alt ağlar, farklı bir Azure Active Directory kiracısına ait abonelikler de dahil olmak üzere aynı abonelikte veya farklı bir abonelikte bulunan bir VNet 'e ait olabilir.

VNet içindeki Azure bilişsel hizmetler için bir [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) etkinleştirin. Hizmet uç noktası, trafiği VNet 'ten Azure bilişsel hizmetler hizmeti için en iyi yol üzerinden yönlendirir. Alt ağın ve sanal ağın kimlikleri de her istekle birlikte iletilir. Yöneticiler daha sonra, isteklerin bir sanal ağdaki belirli alt ağlardan alınmasına izin veren bilişsel hizmetler kaynağı için ağ kurallarını yapılandırabilir. Bu ağ kuralları aracılığıyla erişim izni verilen istemciler, veri erişimi için bilişsel hizmetler kaynağının yetkilendirme gereksinimlerini karşılamaya devam etmelidir.

Her bilişsel hizmetler kaynağı, [IP ağ kuralları](#grant-access-from-an-internet-ip-range)ile birleştirilebilir en fazla 100 sanal ağ kuralını destekler.

### <a name="required-permissions"></a>Gerekli izinler

Bilişsel hizmetler kaynağına bir sanal ağ kuralı uygulamak için, kullanıcının eklenmekte olan alt ağlar için uygun izinlere sahip olması gerekir. Gerekli izin varsayılan *katkıda bulunan* rolü veya bilişsel *Hizmetler katılımcısı* rolü. Gerekli izinler, özel rol tanımlarına da eklenebilir.

Bilişsel hizmetler kaynağı ve erişim verilen sanal ağlar, farklı bir Azure AD kiracısının parçası olan abonelikler de dahil olmak üzere farklı aboneliklerde olabilir.

> [!NOTE]
> Farklı bir Azure Active Directory kiracının parçası olan sanal ağlardaki alt ağlara erişim izni veren kuralların yapılandırılması Şu anda yalnızca PowerShell, CLı ve REST API 'Leri aracılığıyla desteklenir. Bu kurallar, portalda görüntülenebilse de Azure portal aracılığıyla yapılandırılamaz.

### <a name="managing-virtual-network-rules"></a>Sanal ağ kurallarını yönetme

Bilişsel hizmetler kaynakları için sanal ağ kurallarını Azure portal, PowerShell veya Azure CLı aracılığıyla yönetebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ** ADLı **kaynak yönetimi** menüsünü seçin.

1. **Seçili ağlardan** erişime izin vermeyi seçtiğinizden emin olun.

1. Var olan bir ağ kuralıyla bir sanal ağa erişim vermek için, **sanal ağlar** altında **var olan sanal ağı ekle**' yi seçin.

   ![Mevcut vNet 'i Ekle](media/vnet/virtual-network-add-existing.png)

1. **Sanal ağlar** ve **alt ağlar** seçeneklerini belirleyip **Etkinleştir**' i seçin.

   ![Mevcut vNet ayrıntılarını ekle](media/vnet/virtual-network-add-existing-details.png)

1. Yeni bir sanal ağ oluşturmak ve bu ağa erişim vermek için **Yeni sanal ağ ekle**' yi seçin.

   ![Yeni sanal ağ ekle](media/vnet/virtual-network-add-new.png)

1. Yeni sanal ağı oluşturmak için gereken bilgileri girin ve ardından **Oluştur**' u seçin.

   ![VNet oluştur](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Azure bilişsel hizmetler için bir hizmet uç noktası, seçilen sanal ağ ve alt ağlar için daha önce yapılandırılmamışsa, bu işlemin bir parçası olarak yapılandırabilirsiniz.
    >
    > Şu anda, kural oluşturma sırasında seçim için yalnızca aynı Azure Active Directory kiracıya ait olan sanal ağlar gösterilir. Başka bir kiracıya ait bir sanal ağdaki bir alt ağa erişim vermek için lütfen PowerShell, CLı veya REST API 'Leri kullanın.

1. Bir sanal ağ veya alt ağ kuralını kaldırmak için **...** öğesini seçerek sanal ağ veya alt ağ için bağlam menüsünü açın ve **Kaldır**' ı seçin.

   ![VNet 'i kaldır](media/vnet/virtual-network-remove.png)

1. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps)veya **deneyin**' i seçin.

1. Sanal ağ kurallarını listeleyin.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Mevcut bir sanal ağ ve alt ağ üzerinde Azure bilişsel hizmetler için hizmet uç noktası 'nı etkinleştirin.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait bir sanal ağa ait bir ağ kuralı eklemek için, "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" biçiminde tam nitelikli bir **Virtualnetworkresourceıd** parametresi kullanın.

1. Bir sanal ağ ve alt ağ için bir ağ kuralını kaldırın.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli)veya **deneyin**' i seçin.

1. Sanal ağ kurallarını listeleyin.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Mevcut bir sanal ağ ve alt ağ üzerinde Azure bilişsel hizmetler için hizmet uç noktası 'nı etkinleştirin.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait olan bir sanal ağa yönelik bir kural eklemek için, "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" biçiminde tam bir alt ağ KIMLIĞI kullanın.
    > 
    > Başka bir Azure AD kiracısına ait olan bir sanal ağın alt ağ KIMLIĞINI almak için **abonelik** parametresini kullanabilirsiniz.

1. Bir sanal ağ ve alt ağ için bir ağ kuralını kaldırın.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

## <a name="grant-access-from-an-internet-ip-range"></a>İnternet IP aralığından erişim izni verme

Bilişsel hizmetler kaynaklarını belirli genel İnternet IP adresi aralıklarından erişime izin verecek şekilde yapılandırabilirsiniz. Bu yapılandırma, belirli hizmetlere ve şirket içi ağlara erişim verir ve genel İnternet trafiğini etkin bir şekilde engeller.

Formunda [CIDR gösterimini](https://tools.ietf.org/html/rfc4632) `16.17.18.0/24` veya gibi ayrı IP adresleri kullanarak izin verilen internet adresi aralıklarını sağlayın `16.17.18.19` .

   > [!Tip]
   > "/31" veya "/32" önek boyutları kullanılarak küçük adres aralıkları desteklenmez. Bu aralıklar tek tek IP adresi kuralları kullanılarak yapılandırılmalıdır.

IP ağ kurallarına yalnızca **genel İnternet** IP adresleri için izin verilir. Özel ağlar için ayrılan IP adresi aralıklarına ( [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)' de tanımlandığı gıbı) IP kurallarında izin verilmez. Özel ağlarda,, ve ile başlayan adresler bulunur `10.*` `172.16.*`  -  `172.31.*` `192.168.*` .

Şu anda yalnızca ıPV4 adresleri destekleniyor. Her bilişsel hizmetler kaynağı, [sanal ağ kurallarıyla](#grant-access-from-a-virtual-network)birleştirilebilir en fazla 100 IP ağ kuralını destekler.

### <a name="configuring-access-from-on-premises-networks"></a>Şirket içi ağlardan erişimi yapılandırma

Şirket içi ağlarınızdan bir IP ağ kuralıyla bilişsel hizmetler kaynağınızın erişimine izin vermek için ağınız tarafından kullanılan internet 'e yönelik IP adreslerini belirlemeniz gerekir. Yardım için ağ yöneticinize başvurun.

Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../expressroute/expressroute-introduction.md) KULLANıYORSANıZ NAT IP adreslerini belirlemeniz gerekir. Genel eşleme için, her ExpressRoute bağlantı hattı varsayılan olarak iki NAT IP adresi kullanır. Trafik Microsoft Azure ağ omurgasına girdiğinde Azure hizmet trafiğine her biri uygulanır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanmış veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute genel ve Microsoft eşlemesi için NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.

### <a name="managing-ip-network-rules"></a>IP ağ kurallarını yönetme

Bilişsel hizmetler kaynakları için Azure portal, PowerShell veya Azure CLı aracılığıyla IP ağ kurallarını yönetebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ** ADLı **kaynak yönetimi** menüsünü seçin.

1. **Seçili ağlardan** erişime izin vermeyi seçtiğinizden emin olun.

1. Bir internet IP aralığına erişim vermek için, **güvenlik duvarı** adres aralığı altına IP adresini veya adres aralığını ( [CIDR biçiminde](https://tools.ietf.org/html/rfc4632)) girin  >  . Yalnızca geçerli genel IP (ayrılmamış) adresleri kabul edilir.

   ![IP aralığı Ekle](media/vnet/virtual-network-add-ip-range.png)

1. Bir IP ağ kuralını kaldırmak için, <span class="docon docon-delete x-hidden-focus"></span> adres aralığının yanındaki çöp kutusu simgesini seçin.

   ![IP aralığını Sil](media/vnet/virtual-network-delete-ip-range.png)

1. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps)veya **deneyin**' i seçin.

1. IP ağ kurallarını listeleyin.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Bir IP adresi aralığı için ağ kuralı ekleyin.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Tek bir IP adresi için bir ağ kuralını kaldırın.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Bir IP adresi aralığı için ağ kuralını kaldır.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli)veya **deneyin**' i seçin.

1. IP ağ kurallarını listeleyin.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Bir IP adresi aralığı için ağ kuralı ekleyin.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Tek bir IP adresi için bir ağ kuralını kaldırın.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Bir IP adresi aralığı için ağ kuralını kaldır.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

## <a name="use-private-endpoints"></a>Özel uç noktaları kullanma

Bir sanal ağdaki (VNet) istemcilerin [özel bir bağlantı](../private-link/private-link-overview.md)üzerinden güvenli bir şekilde verilere erişmesine izin vermek için bilişsel hizmetler kaynaklarınız için [Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanabilirsiniz. Özel uç nokta, bilişsel hizmetler kaynağınız için VNet adres alanından bir IP adresi kullanır. VNet ve kaynak üzerindeki istemciler arasındaki ağ trafiği, VNet 'ten ve Microsoft omurga ağındaki bir özel bağlantıdan geçer ve bu da genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Bilişsel hizmetler kaynakları için özel uç noktalar şunları yapmanızı sağlar:

* Güvenlik duvarını bilişsel hizmetler hizmeti için genel uç noktada tüm bağlantıları engelleyecek şekilde yapılandırarak bilişsel hizmetler kaynağınızın güvenliğini sağlayın.
* VNET 'ten veri alımını engellemeyi etkinleştirerek VNet için güvenliği artırın.
* [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-locations.md) kullanarak VNET 'e bağlanan şirket içi ağlardan bilişsel hizmetler kaynaklarına güvenli bir şekilde bağlanın.

### <a name="conceptual-overview"></a>Kavramsal genel bakış

Özel uç nokta, [VNET](../virtual-network/virtual-networks-overview.md)'iniz Içindeki bir Azure kaynağı için özel bir ağ arabirimidir. Bilişsel hizmetler kaynağınız için özel bir uç nokta oluşturulması, VNet 'iniz ve kaynağınızın istemcileri arasında güvenli bağlantı sağlar. Özel uç noktaya sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve bilişsel hizmetler hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

VNet 'teki uygulamalar hizmete özel uç nokta üzerinden sorunsuz bir şekilde bağlanıp kullandıkları bağlantı dizelerini ve yetkilendirme mekanizmalarını kullanarak bağlanabilir. Özel durum, ayrı bir uç nokta gerektiren konuşma hizmetdedir. [Konuşma Hizmetleri Ile özel uç noktalar](#private-endpoints-with-the-speech-services)bölümüne bakın. Özel uç noktalar bilişsel hizmetler kaynağı tarafından desteklenen ve REST dahil tüm protokollerle kullanılabilir.

Özel uç noktalar, [hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md)kullanan alt ağlarda oluşturulabilir. Bir alt ağdaki istemciler özel uç nokta kullanarak tek bir bilişsel hizmetler kaynağına bağlanarak diğer kullanıcılara erişmek için hizmet uç noktalarını kullanabilir.

VNet 'iniz içindeki bilişsel hizmetler kaynağı için özel bir uç nokta oluşturduğunuzda, bilişsel hizmetler kaynak sahibine onay için bir izin isteği gönderilir. Özel uç noktanın oluşturulmasını isteyen kullanıcı aynı zamanda kaynağın sahibiyseniz, bu onay isteği otomatik olarak onaylanır.

Bilişsel hizmetler kaynak sahipleri, [Azure Portal](https://portal.azure.com)bilişsel hizmetler kaynağı Için '*Özel uç noktalar*' sekmesi aracılığıyla izin isteklerini ve özel uç noktaları yönetebilir.

### <a name="private-endpoints"></a>Özel uç noktalar

Özel uç nokta oluştururken, bağlandığı bilişsel hizmetler kaynağını belirtmeniz gerekir. Özel uç nokta oluşturma hakkında daha fazla bilgi için bkz.

* [Azure portal özel bağlantı merkezini kullanarak özel bir uç nokta oluşturma](../private-link/create-private-endpoint-portal.md)
* [Azure CLı kullanarak özel uç nokta oluşturma](../private-link/create-private-endpoint-cli.md)
* [Azure PowerShell kullanarak özel uç nokta oluşturma](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Özel uç noktalara bağlanma

Özel uç nokta kullanan bir sanal ağdaki istemciler, ortak uç noktaya bağlanan istemciler için bilişsel hizmetler kaynağı için aynı bağlantı dizesini kullanmalıdır. Özel durum, ayrı bir uç nokta gerektiren konuşma hizmetdedir. [Konuşma Hizmetleri Ile özel uç noktalar](#private-endpoints-with-the-speech-services)bölümüne bakın. DNS çözümlemesini, VNet 'ten gelen bağlantıları özel bir bağlantı üzerinden bilişsel hizmetler kaynağına otomatik olarak yönlendirmek için kullanır. 

Varsayılan olarak, Özel uç noktalara yönelik gerekli güncelleştirmelerle VNet 'e bağlı [Özel BIR DNS bölgesi](../dns/private-dns-overview.md) oluşturacağız. Ancak, kendi DNS sunucunuzu kullanıyorsanız, DNS yapılandırmanızda ek değişiklikler yapmanız gerekebilir. Aşağıdaki [DNS değişikliklerinin](#dns-changes-for-private-endpoints) bölümünde, Özel uç noktalar için gereken güncelleştirmeler açıklanmaktadır.

### <a name="private-endpoints-with-the-speech-services"></a>Konuşma Hizmetleri ile özel uç noktalar

Bkz. [Azure özel bağlantı tarafından sunulan özel uç noktalarla konuşma hizmetlerini kullanma](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri

Özel bir uç nokta oluşturduğunuzda, bilişsel hizmetler kaynağı için DNS CNAME kaynak kaydı, '*Privatelink*' önekine sahip bir alt etki alanındaki diğer ada güncelleştirilir. Varsayılan olarak, Özel uç noktalar için DNS A kaynak kayıtları ile '*Privatelink*' alt etki alanına karşılık gelen [özel bir DNS bölgesi](../dns/private-dns-overview.md)de oluşturacağız.

Uç nokta URL 'sini özel uç noktayla VNet dışından çözümlediğinizde, bilişsel hizmetler kaynağının genel uç noktasına dönüşür. Özel uç noktasını barındıran VNet 'ten çözümlendiğinde, uç nokta URL 'SI özel uç noktanın IP adresine çözümlenir.

Bu yaklaşım, Özel uç noktaları ve VNet dışındaki istemcileri barındıran VNet 'teki istemciler için aynı bağlantı dizesini kullanarak bilişsel hizmetler kaynağına erişim sağlar.

Ağınızda özel bir DNS sunucusu kullanıyorsanız, istemciler bilişsel hizmetler kaynak uç noktasının tam etki alanı adını (FQDN) özel uç nokta IP adresine çözümleyebilmelidir. DNS sunucunuzu özel bağlantı alt etki alanınızı, sanal ağın özel DNS bölgesine devretmek üzere yapılandırın.

> [!TIP]
> Özel veya şirket içi bir DNS sunucusu kullanırken, DNS sunucunuzu, ' Privatelink ' alt etki alanındaki bilişsel hizmetler kaynak adını özel uç nokta IP adresine çözümlemek üzere yapılandırmalısınız. Bunu, sanal ağın özel DNS bölgesine ' Privatelink ' alt etki alanı temsilcisi seçerek veya DNS sunucunuzda DNS bölgesi yapılandırarak ve DNS A kayıtlarını ekleyerek yapabilirsiniz.

Kendi DNS sunucunuzu özel uç noktaları destekleyecek şekilde yapılandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure sanal ağlarındaki kaynaklar için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [Özel uç noktalar için DNS yapılandırması](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Sonraki adımlar

* Çeşitli Azure bilişsel [Hizmetler](./what-are-cognitive-services.md) 'i inceleyin
* [Azure sanal ağ hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) hakkında daha fazla bilgi edinin
