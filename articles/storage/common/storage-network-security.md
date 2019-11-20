---
title: Azure depolama güvenlik duvarlarını ve sanal ağları yapılandırma | Microsoft Docs
description: Depolama hesabınız için katmanlı ağ güvenliğini yapılandırın.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c4ce0d4ecd64273bcb3226b4b543ba378aad538c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078947"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure depolama güvenlik duvarlarını ve sanal ağları yapılandırma

Azure depolama, katmanlı güvenlik modeli sağlar. Bu model, kullanılan ağların türüne ve alt kümelerine göre uygulamalarınızın ve kurumsal ortamların talep ettiği depolama hesaplarınıza erişim düzeyini sağlamanıza ve denetlemenize olanak sağlar. Ağ kuralları yapılandırıldığında, yalnızca belirtilen ağ kümesi üzerinde veri isteyen uygulamalar bir depolama hesabına erişebilir. Depolama hesabınıza erişimi, belirtilen IP adreslerinden, IP aralıklarından veya bir Azure sanal ağındaki (VNet) bir alt ağ listesinden kaynaklanan isteklerle sınırlayabilirsiniz.

Depolama hesaplarının internet üzerinden erişilebilen genel bir uç noktası vardır. Depolama hesabınız için, sanal ağınızdan depolama hesabına özel bir IP adresi atayan ve sanal ağınız ile depolama hesabı arasındaki tüm trafiği özel bir bağlantı üzerinden güvenlik altına alarak, [depolama hesabınız Için özel uç noktalar](storage-private-endpoints.md)da oluşturabilirsiniz. Azure Storage güvenlik duvarı, depolama hesabınızın genel uç noktası için erişim denetimi erişimi sağlar. Özel uç noktaları kullanırken genel uç nokta aracılığıyla tüm erişimi engellemek için güvenlik duvarını da kullanabilirsiniz. Depolama güvenlik duvarınızın yapılandırması, depolama hesabına güvenli bir şekilde erişmek için güvenilen Azure platform Hizmetleri ' ni de sağlar.

Ağ kuralları etkin olduğunda depolama hesabına erişen bir uygulama, istek için uygun yetkilendirme gerektirir. Yetkilendirme, geçerli bir hesap erişim anahtarı veya SAS belirteci ile blob 'lar ve kuyruklar için Azure Active Directory (Azure AD) kimlik bilgileri ile desteklenir.

> [!IMPORTANT]
> İstekler bir Azure sanal ağı (VNet) içinde veya izin verilen ortak IP adreslerinden bir hizmetten kaynaklanmadığı takdirde, depolama hesabınız için Güvenlik Duvarı kurallarının etkinleştirilmesi, varsayılan olarak gelen istekleri engeller. Engellenen istekleri, Azure portalından, günlük ve ölçüm hizmetlerden, diğer Azure hizmetlerinden gelen içerir ve benzeri.
>
> Hizmet örneğini barındıran alt ağdan gelen trafiğe izin vererek VNet içinden çalışan Azure hizmetlerine erişim izni verebilirsiniz. Ayrıca, aşağıda açıklanan [özel durum](#exceptions) mekanizmasıyla sınırlı sayıda senaryoyu etkinleştirebilirsiniz. Depolama hesabındaki verilere Azure portal aracılığıyla erişmek için, ayarladığınız güvenilir sınır (IP veya VNet) içinde bir makinede olmanız gerekir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Senaryolar

Depolama hesabınızın güvenliğini sağlamak için, varsayılan olarak genel uç noktasındaki tüm ağlardan (internet trafiği dahil) trafiğe erişimi reddetmek üzere bir kural yapılandırmanız gerekir. Ardından, belirli sanal ağlardan gelen trafiğe erişim veren kurallar yapılandırmanız gerekir. Ayrıca, belirli internet veya şirket içi istemcilerden gelen bağlantıları etkinleştirerek, genel İnternet IP adresi aralıklarından gelen trafiğe erişim izni vermek için kurallar da yapılandırabilirsiniz. Bu yapılandırma, uygulamalarınız için bir güvenli ağ sınırı oluşturmanıza olanak sağlar.

Belirli sanal ağlardan ve aynı depolama hesabındaki genel IP adres aralıklarından erişime izin veren güvenlik duvarı kurallarını birleştirebilirsiniz. Depolama güvenlik duvarı kuralları mevcut depolama hesaplarına veya yeni depolama hesapları oluştururken uygulanabilir.

Depolama güvenlik duvarı kuralları, bir depolama hesabının genel uç noktası için geçerlidir. Bir depolama hesabının özel uç noktaları için trafiğe izin vermek üzere herhangi bir güvenlik duvarı erişim kuralına gerek yoktur. Özel bir uç noktanın oluşturulmasını onaylama işlemi, Özel uç noktasını barındıran alt ağdan gelen trafiğe örtülü erişim verir.

Azure depolama REST ve SMB dahil olmak üzere, tüm ağ protokolleri üzerinde ağ kuralları uygulanır. Azure portal, Depolama Gezgini ve AZCopy gibi araçları kullanarak verilere erişmek için açık ağ kurallarının yapılandırılması gerekir.

Bir kez ağ kuralları uygulandığında, tüm istekler için zorunlu. Erişim belirli bir IP adresi için SAS belirteçleri, belirteç sahibinin erişimi sınırlamak için hizmet, ancak yapılandırılmış ağ kuralları ötesinde yeni erişim izni yok.

Sanal makine diski trafiği (ve işlemleri ve disk g/ç çıkarın bağlama dahil), ağ kuralları tarafından etkilenmez. REST erişimini sayfa BLOB'ları, ağ kuralları tarafından korunur.

Klasik depolama hesapları, güvenlik duvarları ve sanal ağlar'ı desteklemez.

Bir özel durum oluşturarak yedekleme ve geri yükleme Vm'lere uygulanan ağ kuralları ile depolama hesaplarında yönetilmeyen diskler kullanabilirsiniz. Bu işlem belgelenen [özel durumları](#exceptions) bu makalenin. Zaten Azure tarafından yönetildikleri gibi güvenlik duvarı özel durumlarını yönetilen disklerle ilgili değildir.

## <a name="change-the-default-network-access-rule"></a>Varsayılan ağ erişim kuralını değiştirme

Varsayılan olarak, depolama hesapları herhangi bir ağ üzerinde istemci bağlantılarını kabul edin. Seçili ağlar erişimi sınırlamak için önce varsayılan eylem değiştirmeniz gerekir.

> [!WARNING]
> Ağ kuralları için değişiklik yapmadan uygulamalarınızın Azure depolamaya bağlanma yeteneğini etkileyebilir. Varsayılan ağ kuralını **Reddet** olarak ayarlamak, erişim **izni** veren belirli ağ kuralları da uygulanmamışsa veriye tüm erişimi engeller. İzin verilen erişimi engellemek için varsayılan Kuralı değiştirmeden önce ağ kurallarını kullanarak herhangi bir ağa erişim vermek emin olun.

### <a name="managing-default-network-access-rules"></a>Varsayılan ağ erişim kurallarını yönetme

Azure portalı, PowerShell veya CLIv2 aracılığıyla depolama hesapları için varsayılan ağ erişim kurallarını yönetebilirsiniz.

#### <a name="azure-portal"></a>Azure portal

1. Korumak istediğiniz depolama hesabına gidin.

1. Adlı ayarları menüsünde **güvenlik duvarları ve sanal ağlar**.

1. Varsayılan olarak erişimi reddetmek için erişime izin verecek şekilde seçin: **seçili ağlar**. Tüm ağlar gelen trafiğe izin vermek için erişime izin verecek şekilde seçin: **tüm ağlar**.

1. Tıklayın **Kaydet** yaptığınız değişiklikleri uygulamak için.

#### <a name="powershell"></a>PowerShell

1. Yükleme [Azure PowerShell](/powershell/azure/install-Az-ps) ve [oturum](/powershell/azure/authenticate-azureps).

1. Depolama hesabı için varsayılan kuralın durumunu görüntüler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Varsayılan olarak ağ erişimini engellemek için varsayılan kuralı ayarlayın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Varsayılan olarak ağ erişim izni vermek için varsayılan kuralı ayarlayın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Yükleme [Azure CLI](/cli/azure/install-azure-cli) ve [oturum](/cli/azure/authenticate-azure-cli).

1. Depolama hesabı için varsayılan kuralın durumunu görüntüler.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Varsayılan olarak ağ erişimini engellemek için varsayılan kuralı ayarlayın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Varsayılan olarak ağ erişim izni vermek için varsayılan kuralı ayarlayın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Bir sanal ağdan erişim izni ver

Depolama hesaplarını yalnızca belirli alt ağlardan erişime izin verecek şekilde yapılandırabilirsiniz. İzin verilen alt ağlar, farklı bir Azure Active Directory kiracısına ait abonelikler de dahil olmak üzere, aynı abonelikte bulunan bir VNet 'e veya farklı bir aboneliğe ait olabilir.

Etkinleştirme bir [hizmet uç noktası](/azure/virtual-network/virtual-network-service-endpoints-overview) Azure depolama sanal ağ içinden. Hizmet uç noktası, trafiği VNet 'ten Azure depolama hizmetine en uygun bir yol üzerinden yönlendirir. Alt ağın ve sanal ağın kimlikleri de her istekle birlikte iletilir. Yöneticiler daha sonra depolama hesabı için ağ kurallarını, isteklerin bir sanal ağdaki belirli alt ağlardan alınmasına izin veren ağ kurallarını yapılandırabilir. İstemcilerin bu ağ kuralları üzerinden erişim verilere erişmek için depolama hesabı yetkilendirme gereksinimlerini karşılamak devam etmelidir verildi.

Her Depolama hesabı birleştirilebilir 100'e kadar sanal ağ kuralları desteklediği [IP ağ kuralları](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Kullanılabilir sanal ağ bölgeleri

Genel olarak, hizmet uç noktaları sanal ağları ve aynı Azure bölgesinde hizmet örnekleri arasında çalışır. Hizmet uç noktaları Azure Depolama'yı kullanırken, bu kapsamı içerecek şekilde büyür [eşleştirilmiş bölge](/azure/best-practices-availability-paired-regions). Hizmet uç noktaları sürekliliği sırasında bölgesel yük devretme ve salt okunur coğrafi olarak yedekli depolama (RA-GRS) örnekleri için erişim verin. Sanal ağdan bir depolama hesabına erişim izni ağ kuralları da herhangi bir RA-GRS örneğine erişim.

Bölgesel bir kesinti sırasında olağanüstü durum kurtarma için planlama yaparken, sanal ağlar eşlenen bölgesi içinde önceden oluşturmanız gerekir. Hizmet uç noktaları Azure depolama için bu diğer sanal ağlardan erişim verme ağ kurallarıyla etkinleştirin. Ardından bu kurallar, coğrafi olarak yedekli depolama hesaplarınıza uygulayın.

> [!NOTE]
> Hizmet uç noktaları için trafiği sanal ağ ve belirtilen bölge çiftine bölgesi dışında geçerli değildir. Yalnızca depolama hesaplarına birincil bölgede bir depolama hesabı veya belirlenen eşleştirilmiş bölge sanal ağdan erişim verme ağ kuralları uygulayabilirsiniz.

### <a name="required-permissions"></a>Gerekli izinler

Bir depolama hesabı için bir sanal ağ kuralı uygulamak için kullanıcının eklenen alt ağlarda için uygun izinleri olmalıdır. Gerekli izni *bir alt ağa Hizmeti'ne Katıl* ve dahildir *depolama hesabı Katılımcısı* yerleşik rolü. Özel rol tanımları da eklenebilir.

Depolama hesabı ve erişim verilen sanal ağlar, farklı bir Azure AD kiracısının parçası olan abonelikler de dahil olmak üzere farklı aboneliklerde olabilir.

> [!NOTE]
> Farklı bir Azure Active Directory kiracının parçası olan sanal ağlardaki alt ağlara erişim izni veren kuralların yapılandırılması Şu anda yalnızca PowerShell, CLı ve REST API 'Leri aracılığıyla desteklenir. Bu kurallar, portalda görüntülenebilse de Azure portal aracılığıyla yapılandırılamaz.

### <a name="managing-virtual-network-rules"></a>Sanal ağ kurallarını yönetme

Azure portalı, PowerShell veya CLIv2 aracılığıyla depolama hesapları için sanal ağ kuralları yönetebilirsiniz.

#### <a name="azure-portal"></a>Azure portal

1. Korumak istediğiniz depolama hesabına gidin.

1. Adlı ayarları menüsünde **güvenlik duvarları ve sanal ağlar**.

1. Erişime izin verecek şekilde seçtiğiniz denetleyin **seçili ağlar**.

1. Altında yeni bir ağ kuralı ile bir sanal ağa erişim vermek için **sanal ağlar**, tıklayın **var olan sanal ağı Ekle**seçin **sanal ağlar** ve **Alt ağlar** seçenekleri ve ardından **Ekle**. Yeni bir sanal ağ oluşturma ve erişim vermek için **Ekle yeni sanal ağ**. Yeni bir sanal ağ oluşturun ve ardından için gereken bilgileri sağlayan **Oluştur**.

    > [!NOTE]
    > Azure depolama için hizmet uç noktası seçilen sanal ağ ve alt ağlar için önceden yapılandırılmış değildi, bu işlemin bir parçası yapılandırabilirsiniz.
    >
    > Şu anda, kural oluşturma sırasında seçim için yalnızca aynı Azure Active Directory kiracıya ait olan sanal ağlar gösterilir. Başka bir kiracıya ait bir sanal ağdaki bir alt ağa erişim vermek için lütfen PowerShell, CLı veya REST API 'Leri kullanın.

1. Bir sanal ağ veya alt ağ kuralı kaldırmak için tıklayın **...**  sanal ağ veya alt ağ için bağlam menüsünü açın ve **Kaldır**.

1. Tıklayın **Kaydet** yaptığınız değişiklikleri uygulamak için.

#### <a name="powershell"></a>PowerShell

1. Yükleme [Azure PowerShell](/powershell/azure/install-Az-ps) ve [oturum](/powershell/azure/authenticate-azureps).

1. Sanal ağ kuralları listesi.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Hizmet uç noktası, bir var olan sanal ağı ve alt ağ üzerinde Azure depolama için etkinleştirin.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait bir sanal ağa ait bir ağ kuralı eklemek için, "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" biçiminde tam nitelikli bir **Virtualnetworkresourceıd** parametresi kullanın.

1. Bir sanal ağ ve alt ağ için bir ağ kuralı kaldırın.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya ağ kuralları etkilemez.

#### <a name="cliv2"></a>CLIv2

1. Yükleme [Azure CLI](/cli/azure/install-azure-cli) ve [oturum](/cli/azure/authenticate-azure-cli).

1. Sanal ağ kuralları listesi.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Hizmet uç noktası, bir var olan sanal ağı ve alt ağ üzerinde Azure depolama için etkinleştirin.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait olan bir sanal ağa yönelik bir kural eklemek için, "/Subscriptions/\<abonelik-KIMLIĞI\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-adı\>/Subnets/\<alt ağ-adı\>" biçiminde tam olarak nitelenmiş bir alt ağ KIMLIĞI kullanın.
    > 
    > Başka bir Azure AD kiracısına ait olan bir sanal ağın alt ağ KIMLIĞINI almak için **abonelik** parametresini kullanabilirsiniz.

1. Bir sanal ağ ve alt ağ için bir ağ kuralı kaldırın.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya ağ kuralları etkilemez.

## <a name="grant-access-from-an-internet-ip-range"></a>İnternet'ten erişim ver IP aralığı

Depolama hesapları, belirli genel internet'ten erişim izni vermek için yapılandırdığınız IP adresi aralıkları. Bu yapılandırma belirli internet tabanlı hizmetlere erişim verir ve şirket içi ağlara ve genel internet trafiği engeller.

Adres aralıkları kullanarak izin verilen internet sağlamak [CIDR gösteriminde](https://tools.ietf.org/html/rfc4632) biçiminde *16.17.18.0/24* tek tek IP adresleri gibi veya *16.17.18.19*.

   > [!NOTE]
   > Küçük adres aralıkları kullanarak "/ 31" veya "/ 32" öneki boyutları desteklenmez. Bu aralıklar, tek tek IP adresi kurallarını kullanarak yapılandırılmalıdır.

IP ağ kuralları için yalnızca verilir **genel internet** IP adresleri. IP adres aralıkları özel ağlar için ayrılmış (sınıfında tanımlandığı gibi [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) IP kurallarında izin verilmez. Özel ağlar ile başlayan bir adres dahil _10.*_ , _172.16. *_  - _172.31. *_ , ve _192.168. *_ .

   > [!NOTE]
   > IP ağ kuralları için aynı Azure bölgesindeki depolama hesabı kaynaklanan taleplere üzerinde etkisi yoktur. Kullanım [sanal ağ kuralları](#grant-access-from-a-virtual-network) aynı bölgede isteklere izin vermek için.

  > [!NOTE]
  > Depolama hesabı ile aynı bölgede dağıtılan hizmetler, iletişim için özel Azure IP adreslerini kullanır. Bu nedenle, belirli Azure hizmetlerine erişimi, genel gelen IP adresi aralığına göre kısıtlayamazsınız.

Depolama Güvenlik Duvarı kurallarının yapılandırılması için yalnızca ıPV4 adresleri desteklenir.

Her depolama hesabı en fazla 100 IP ağ kuralını destekler.

### <a name="configuring-access-from-on-premises-networks"></a>Şirket içi ağlardan erişimi yapılandırma

Depolama hesabınıza bir IP ağ kuralı ile şirket içi ağlarınızı erişim vermek için internet'e yönelik ağınız tarafından kullanılan IP adreslerini tanımlamanız gerekir. Yardım için ağ yöneticinize başvurun.

Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](/azure/expressroute/expressroute-introduction) kullanıyorsanız, kullanılan NAT IP adreslerini belirlemeniz gerekir. Ortak eşleme için, her bir ExpressRoute varsayılan olarak bağlantı hattında trafik Microsoft Azure omurga ağına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanılır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanmış veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute genel ve Microsoft eşlemesi için NAT](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.

### <a name="managing-ip-network-rules"></a>IP ağ kurallarını yönetme

Azure portalı, PowerShell veya CLIv2 aracılığıyla depolama hesapları için IP ağ kuralları yönetebilirsiniz.

#### <a name="azure-portal"></a>Azure portal

1. Korumak istediğiniz depolama hesabına gidin.

1. Adlı ayarları menüsünde **güvenlik duvarları ve sanal ağlar**.

1. Erişime izin verecek şekilde seçtiğiniz denetleyin **seçili ağlar**.

1. Vermek için IP aralığı bir internet'e erişmek için IP adresi veya adres aralığı (CIDR biçiminde) altında girin **Güvenlik Duvarı** > **adres aralığı**.

1. Bir IP ağ kuralı kaldırmak için adres aralığı yanındaki çöp kutusu simgesine tıklayın.

1. Tıklayın **Kaydet** yaptığınız değişiklikleri uygulamak için.

#### <a name="powershell"></a>PowerShell

1. Yükleme [Azure PowerShell](/powershell/azure/install-Az-ps) ve [oturum](/powershell/azure/authenticate-azureps).

1. IP ağ kuralları listesi.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Bir IP adresi aralığı için bir ağ kuralı ekleyin.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Tek bir IP adresi için bir ağ kuralı kaldırın.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ağ kuralı için bir IP adresi aralığı kaldırın.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya ağ kuralları etkilemez.

#### <a name="cliv2"></a>CLIv2

1. Yükleme [Azure CLI](/cli/azure/install-azure-cli) ve [oturum](/cli/azure/authenticate-azure-cli).

1. IP ağ kuralları listesi.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Bir IP adresi aralığı için bir ağ kuralı ekleyin.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Tek bir IP adresi için bir ağ kuralı kaldırın.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ağ kuralı için bir IP adresi aralığı kaldırın.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya ağ kuralları etkilemez.

## <a name="exceptions"></a>Özel durumlar

Ağ kuralları, çoğu senaryo için Uygulamalarınız ve verileriniz arasındaki bağlantılar için güvenli bir ortam oluşturmaya yardımcı olur. Ancak bazı uygulamalar, sanal ağ veya IP adresi kuralları aracılığıyla benzersiz olarak yalıtılmamış Azure hizmetlerine bağımlıdır. Ancak, tam uygulama işlevselliğini etkinleştirmek için bu hizmetler depolama alanına verilmelidir. Bu gibi durumlarda, bu hizmetlerin verilerinize, günlüklerine veya analizlerinize erişmesini sağlamak için ***güvenilir Microsoft hizmetlerine Izin ver...*** ayarını kullanabilirsiniz.

### <a name="trusted-microsoft-services"></a>Güvenilen Microsoft Hizmetleri

Bazı Microsoft Hizmetleri, ağ kurallarınıza dahil olmayan ağlardan çalışır. Diğer uygulamalar için ağ kurallarını koruyarak, bu tür güvenilen Microsoft hizmetlerinin depolama hesabına erişiminin bir alt kümesine izin verebilirsiniz. Bu güvenilen hizmetler, depolama hesabınıza güvenli bir şekilde bağlanmak için güçlü kimlik doğrulama kullanır. Microsoft Hizmetleri için iki adet güvenilen erişim modunu etkinleştirdik.

- **Aboneliğinizde kayıtlı**olan bazı hizmetlerin kaynakları, günlük veya yedekleme yazma gibi seçim işlemleri için **aynı abonelikte** depolama hesabınıza erişebilir.
- Bazı hizmetlerin kaynaklarına, sistem tarafından atanan yönetilen kimliğe **BIR RBAC rolü atayarak** depolama hesabınıza açık erişim verilebilir.


**Güvenilen Microsoft hizmetlerine Izin ver...** ayarını etkinleştirdiğinizde, depolama hesabınızla aynı abonelikte kayıtlı olan aşağıdaki hizmetlerin kaynaklarına, açıklandığı şekilde sınırlı bir işlem kümesi erişimi verilir:

| Hizmet                  | Kaynak sağlayıcı adı     | İzin verilen işlemler                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Yedekleme ve geri yüklemeler yönetilmeyen diskler, IAAS sanal makinelerde çalıştırır. (yönetilen diskler için gerekli değildir). [Daha fazla bilgi edinin](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft. DataBox          | Data Box kullanarak Azure 'a veri aktarmayı sağlar. [Daha fazla bilgi edinin](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Özel görüntü oluşturma ve yapıt yükleme. [Daha fazla bilgi edinin](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | BLOB Depolama olayı yayımlamayı etkinleştirme ve depolama kuyrukları yayımlamak Event Grid sağlar. Hakkında bilgi edinin [blob depolama olayları](/azure/event-grid/event-sources) ve [sıralara yayımlama](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs yakalama ile verileri arşivleme. [Daha fazla bilgi edinin](/azure/event-hubs/event-hubs-capture-overview). |
| Azure Dosya Eşitleme          | Microsoft.StorageSync      | Şirket içi dosya sunucunuzu Azure dosya paylaşımları için bir önbelleğe dönüştürmenizi sağlar. Çok siteli eşitleme, hızlı olağanüstü durum kurtarma ve bulut tarafı yedekleme için izin verme. [Daha fazla bilgi](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Yeni bir HDInsight kümesi için varsayılan dosya sisteminin başlangıçtaki içeriğini sağlayın. [Daha fazla bilgi edinin](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure İzleyici            | Microsoft.Insights         | İzleme verilerinin bir güvenli depolama hesabına yazma sağlayan [daha fazla bilgi edinin](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure Ağı         | Microsoft.Network          | Store ve ağ trafik günlüklerini analiz edin. [Daha fazla bilgi edinin](/azure/network-watcher/network-watcher-packet-capture-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Güvenlik Duvarı özellikli önbellek, kaynak veya hedef depolama hesapları kullanırken Azure IaaS sanal makinelerinin olağanüstü durum kurtarma için çoğaltmayı etkinleştirin.  [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

**Güvenilen Microsoft hizmetlerine Izin ver...** ayarı aynı zamanda bu kaynak örneği için [sistem tarafından atanan YÖNETILEN kimliğe](../../active-directory/managed-identities-azure-resources/overview.md) [bir RBAC rolü atarsanız](storage-auth-aad.md#assign-rbac-roles-for-access-rights) , aşağıdaki hizmetlerin belirli bir örneğinin depolama hesabına erişmesine izin verir. Bu durumda, örnek için erişim kapsamı yönetilen kimliğe atanan RBAC rolüne karşılık gelir.

| Hizmet                        | Kaynak sağlayıcı adı          | Amaç                            |
| :----------------------------- | :------------------------------ | :--------------------------------- |
| Azure Data Factory             | Microsoft. DataFactory/Factory | ADF çalışma zamanı aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure Logic Apps               | Microsoft.Logic/workflows       | Mantıksal uygulamaların depolama hesaplarına erişmesini sağlar. [Daha fazla bilgi edinin](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning Hizmeti | Microsoft.MachineLearningServices | Yetkili Azure Machine Learning çalışma alanları BLOB depolama alanına deneme çıkış, model ve Günlükler yazar. [Daha fazla bilgi edinin](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Veri Ambarı       | Microsoft.Sql                   | PolyBase kullanarak belirli SQL veritabanı örneklerinden verilerin içeri ve dışarı aktarılmasını sağlar. [Daha fazla bilgi edinin](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics       | Akış işindeki verilerin blob depolamaya yazılmasına izin verir. Bu özellik şu anda önizleme sürümündedir. [Daha fazla bilgi edinin](/azure/stream-analytics/blob-output-managed-identity). |


### <a name="storage-analytics-data-access"></a>Depolama analizi veri erişimi

Bazı durumlarda, tanılama günlükleri ve ölçümleri okuma erişimi ağ sınırları dışından gelen gerekli değildir. Depolama hesabına güvenilen hizmetler erişimi yapılandırılırken, günlük dosyaları, ölçüm tabloları veya her ikisi için okuma erişimine izin verebilirsiniz. [Depolama Analizi ile çalışma hakkında daha fazla bilgi edinin.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Özel durumları yönetme

Ağ kuralı özel durumlarını Azure portal, PowerShell veya Azure CLI aracılığıyla yönetebileceğiniz v2.

#### <a name="azure-portal"></a>Azure portal

1. Korumak istediğiniz depolama hesabına gidin.

1. Adlı ayarları menüsünde **güvenlik duvarları ve sanal ağlar**.

1. Erişime izin verecek şekilde seçtiğiniz denetleyin **seçili ağlar**.

1. Altında **özel durumları**, istediğiniz vermek için özel durumlar'ı seçin.

1. Tıklayın **Kaydet** yaptığınız değişiklikleri uygulamak için.

#### <a name="powershell"></a>PowerShell

1. Yükleme [Azure PowerShell](/powershell/azure/install-Az-ps) ve [oturum](/powershell/azure/authenticate-azureps).

1. Depolama hesabı ağ kuralları özel durumlarını görüntüler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Depolama hesabı ağ kuralları için özel durumlar yapılandırın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Depolama hesabı ağ kurallarına özel durumları kaldırın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya kaldırılırken özel durum etkilemez.

#### <a name="cliv2"></a>CLIv2

1. Yükleme [Azure CLI](/cli/azure/install-azure-cli) ve [oturum](/cli/azure/authenticate-azure-cli).

1. Depolama hesabı ağ kuralları özel durumlarını görüntüler.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Depolama hesabı ağ kuralları için özel durumlar yapılandırın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Depolama hesabı ağ kurallarına özel durumları kaldırın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Mutlaka [varsayılan kural kümesi](#change-the-default-network-access-rule) için **Reddet**, veya kaldırılırken özel durum etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

Azure ağ hizmet uç noktalarına hakkında daha fazla bilgi [hizmet uç noktalarını](/azure/virtual-network/virtual-network-service-endpoints-overview).

Azure depolama güvenlik ile daha derine inin [Azure depolama Güvenlik Kılavuzu](storage-security-guide.md).
