---
title: Sanal Ağlar
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler kaynaklarınız için katmanlı ağ güvenliğini yapılandırın.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1ae3caa2d1f90bbbae1070d95d676eb206a361a0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647342"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure bilişsel hizmetler sanal ağlarını yapılandırma

Azure bilişsel hizmetler, katmanlı bir güvenlik modeli sağlar. Bu model, bilişsel hizmetler hesaplarınızı belirli bir ağ alt kümesiyle korumanıza olanak sağlar. Ağ kuralları yapılandırıldığında, yalnızca belirtilen ağ kümesi üzerinde veri isteyen uygulamalar hesaba erişebilir. İstek filtreleme ile kaynaklarınızın erişimini sınırlayabilirsiniz. Yalnızca belirtilen IP adreslerinden, IP aralıklarından veya [Azure sanal ağlarındaki](../virtual-network/virtual-networks-overview.md)alt ağlar listesinden kaynaklanan isteklere izin verme. Bu teklif ile ilgileniyorsanız, [Önizleme erişimi istemeniz](https://aka.ms/cog-svc-vnet-signup)gerekir.

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

Bilişsel hizmetler için sanal ağ desteği, *Canary-Orta ABD EUAP*, *Batı Avrupa*ve *Batı ABD 2* Azure bölgeleriyle sınırlıdır. Ayrıca, bilişsel hizmet tekliflerinin hepsi sanal ağları desteklemez. Aşağıdaki bilişsel hizmet teklifleri, sanal ağların kullanılmasına izin verir. Hizmet teklifi burada listelenmiyorsa, sanal ağları desteklemez.

> [!div class="checklist"]
> * [Anomali algılayıcısı](./anomaly-detector/index.yml)
> * [Görüntü İşleme](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Özel Görüntü İşleme](./custom-vision-service/index.yml)
> * [Ünüzü](./face/index.yml)
> * [Form tanıyıcı](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Kişiselleştirici](./personalizer/index.yml)
> * [Metin Analizi](./text-analytics/index.yml)

## <a name="change-the-default-network-access-rule"></a>Varsayılan ağ erişim kuralını değiştirme

Bilişsel hizmetler kaynakları, varsayılan olarak herhangi bir ağdaki istemcilerden gelen bağlantıları kabul eder. Seçilen ağlara erişimi sınırlandırmak için, önce varsayılan eylemi değiştirmeniz gerekir.

> [!WARNING]
> Ağ kurallarında değişiklik yapmak, uygulamalarınızın Azure bilişsel hizmetler 'e bağlanma yeteneğini etkileyebilir. Varsayılan ağ kuralını **Reddet** olarak ayarlamak, erişim **izni** veren belirli ağ kuralları da uygulanmamışsa veriye tüm erişimi engeller. Erişimi reddetmek üzere varsayılan kuralı değiştirmeden önce ağ kurallarını kullanarak izin verilen ağlara erişim izni vermediğinizden emin olun. Şirket içi Ağınızın IP adreslerini listelemesine izin verirseniz, tüm olası giden genel IP adreslerini şirket içi ağınızdan eklediğinizden emin olun.

### <a name="managing-default-network-access-rules"></a>Varsayılan ağ erişim kurallarını yönetme

Bilişsel hizmetler kaynakları için Azure portal, PowerShell veya Azure CLı aracılığıyla varsayılan ağ erişim kurallarını yönetebilirsiniz.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ**ADLı **kaynak yönetimi** menüsünü seçin.

   ![Sanal ağ seçeneği](media/vnet/virtual-network-blade.png)

1. Erişimi varsayılan olarak reddetmek için, **Seçili ağlardan**erişime izin ver ' i seçin. Yalnızca **Seçili ağlar** ayarıyla, yapılandırılmış **sanal ağlar** veya **adres aralıkları** tarafından açıklanarak, tüm erişim etkin bir şekilde reddedilir. Tüm erişim reddedildiğinde, bilişsel hizmetler kaynağını tüketmeye çalışan isteklere izin verilmez. Azure portal, Azure PowerShell veya Azure CLı bilişsel hizmetler kaynağını yapılandırmak için yine de kullanılabilir.
1. Tüm ağlardan gelen trafiğe izin vermek için **tüm ağlardan**erişime izin ver ' i seçin.

   ![Sanal ağlar reddetme](media/vnet/virtual-network-deny.png)

1. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ**ADLı **kaynak yönetimi** menüsünü seçin.

1. **Seçili ağlardan**erişime izin vermeyi seçtiğinizden emin olun.

1. Var olan bir ağ kuralıyla bir sanal ağa erişim vermek için, **sanal ağlar**altında **var olan sanal ağı ekle**' yi seçin.

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

1. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet**olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

## <a name="grant-access-from-an-internet-ip-range"></a>İnternet IP aralığından erişim izni verme

Bilişsel hizmetler kaynaklarını belirli genel İnternet IP adresi aralıklarından erişime izin verecek şekilde yapılandırabilirsiniz. Bu yapılandırma, belirli hizmetlere ve şirket içi ağlara erişim verir ve genel İnternet trafiğini etkin bir şekilde engeller.

`16.17.18.0/24` formunda [CIDR gösterimini](https://tools.ietf.org/html/rfc4632) kullanarak izin verilen Internet adresi aralıklarını veya `16.17.18.19`gıbı ayrı IP adresleri sağlayın.

   > [!Tip]
   > "/31" veya "/32" önek boyutları kullanılarak küçük adres aralıkları desteklenmez. Bu aralıklar tek tek IP adresi kuralları kullanılarak yapılandırılmalıdır.

IP ağ kurallarına yalnızca **genel İnternet** IP adresleri için izin verilir. Özel ağlar için ayrılan IP adresi aralıklarına ( [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)' de tanımlandığı gıbı) IP kurallarında izin verilmez. Özel ağlarda `10.*`, `172.16.*` - `172.31.*`ve `192.168.*`ile başlayan adresler bulunur.

   > [!NOTE]
   > IP ağ kuralları, bilişsel hizmetler kaynağı ile aynı Azure bölgesinden kaynaklanan isteklere hiçbir etkiye sahip değildir. Aynı bölge isteklerine izin vermek için [sanal ağ kurallarını](#grant-access-from-a-virtual-network) kullanın.

Şu anda yalnızca ıPV4 adresleri destekleniyor. Her bilişsel hizmetler kaynağı, [sanal ağ kurallarıyla](#grant-access-from-a-virtual-network)birleştirilebilir en fazla 100 IP ağ kuralını destekler.

### <a name="configuring-access-from-on-premises-networks"></a>Şirket içi ağlardan erişimi yapılandırma

Şirket içi ağlarınızdan bir IP ağ kuralıyla bilişsel hizmetler kaynağınızın erişimine izin vermek için ağınız tarafından kullanılan internet 'e yönelik IP adreslerini belirlemeniz gerekir. Yardım için ağ yöneticinize başvurun.

Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../expressroute/expressroute-introduction.md) KULLANıYORSANıZ NAT IP adreslerini belirlemeniz gerekir. Genel eşleme için, her ExpressRoute bağlantı hattı varsayılan olarak iki NAT IP adresi kullanır. Trafik Microsoft Azure ağ omurgasına girdiğinde Azure hizmet trafiğine her biri uygulanır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanmış veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute genel ve Microsoft eşlemesi için NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.

### <a name="managing-ip-network-rules"></a>IP ağ kurallarını yönetme

Bilişsel hizmetler kaynakları için Azure portal, PowerShell veya Azure CLı aracılığıyla IP ağ kurallarını yönetebilirsiniz.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Güvenli hale getirmek istediğiniz bilişsel hizmetler kaynağına gidin.

1. **Sanal ağ**ADLı **kaynak yönetimi** menüsünü seçin.

1. **Seçili ağlardan**erişime izin vermeyi seçtiğinizden emin olun.

1. Bir internet IP aralığına erişim izni vermek için, **güvenlik duvarı** > **adres aralığı**altında IP adresini veya adres aralığını ( [CIDR biçiminde](https://tools.ietf.org/html/rfc4632)) girin. Yalnızca geçerli genel IP (ayrılmamış) adresleri kabul edilir.

   ![IP aralığı Ekle](media/vnet/virtual-network-add-ip-range.png)

1. Bir IP ağ kuralını kaldırmak için, adres aralığının yanındaki çöp <span class="docon docon-delete x-hidden-focus"></span> kutusu simgesini seçin.

   ![IP aralığını Sil](media/vnet/virtual-network-delete-ip-range.png)

1. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet**olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

* Çeşitli Azure bilişsel [Hizmetler](welcome.md) 'i inceleyin
* [Azure sanal ağ hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) hakkında daha fazla bilgi edinin