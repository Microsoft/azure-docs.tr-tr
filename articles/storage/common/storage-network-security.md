---
title: Azure depolama güvenlik duvarlarını ve sanal ağları yapılandırma | Microsoft Docs
description: Azure Storage güvenlik duvarları ve Azure sanal ağını kullanarak depolama hesabınız için katmanlı ağ güvenliğini yapılandırın.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5e8123c252d99b2999eeef42fecae189a05e382b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778130"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma

Azure Depolama, katmanlı bir güvenlik modeli sağlar. Bu model, kullandığınız ağların veya kaynakların türüne ve alt kümelerine göre uygulamalarınızın ve kurumsal ortamların talep ettiği depolama hesaplarınıza erişim düzeyini güvence altına almanıza ve denetlemenize olanak sağlar. Ağ kuralları yapılandırıldığında, yalnızca belirtilen ağ kümesi veya belirtilen Azure kaynakları kümesi üzerinden veri isteyen uygulamalar bir depolama hesabına erişebilir. Depolama hesabınıza erişimi, belirtilen IP adreslerinden, IP aralıklarından, bir Azure sanal ağındaki (VNet) alt ağlardan veya bazı Azure hizmetlerinin kaynak örneklerinde bulunan isteklerle sınırlayabilirsiniz.

Depolama hesaplarının internet üzerinden erişilebilen genel bir uç noktası vardır. Depolama hesabınız için, sanal ağınızdan depolama hesabına özel bir IP adresi atayan ve sanal ağınız ile depolama hesabı arasındaki tüm trafiği özel bir bağlantı üzerinden güvenlik altına alarak, [depolama hesabınız Için özel uç noktalar](storage-private-endpoints.md)da oluşturabilirsiniz. Azure Storage güvenlik duvarı, depolama hesabınızın genel uç noktası için erişim denetimi sağlar. Özel uç noktaları kullanırken genel uç nokta aracılığıyla tüm erişimi engellemek için güvenlik duvarını da kullanabilirsiniz. Depolama güvenlik duvarınızın yapılandırması, depolama hesabına güvenli bir şekilde erişmek için güvenilen Azure platform Hizmetleri ' ni de sağlar.

Ağ kuralları etkin olduğunda depolama hesabına erişen bir uygulama, istek için uygun yetkilendirme gerektirir. Yetkilendirme, geçerli bir hesap erişim anahtarı veya SAS belirteci ile blob 'lar ve kuyruklar için Azure Active Directory (Azure AD) kimlik bilgileri ile desteklenir.

> [!IMPORTANT]
> İstekler bir Azure sanal ağı (VNet) içinde veya izin verilen ortak IP adreslerinden bir hizmetten kaynaklanmadığı takdirde, depolama hesabınız için Güvenlik Duvarı kurallarının etkinleştirilmesi, varsayılan olarak gelen istekleri engeller. Engellenen istekler diğer Azure hizmetlerinden, Azure portal, günlük ve ölçüm hizmetlerinden ve bu şekilde devam eder.
>
> Hizmet örneğini barındıran alt ağdan gelen trafiğe izin vererek VNet içinden çalışan Azure hizmetlerine erişim izni verebilirsiniz. Ayrıca, aşağıda açıklanan özel durum mekanizmasıyla sınırlı sayıda senaryoyu etkinleştirebilirsiniz. Depolama hesabındaki verilere Azure portal aracılığıyla erişmek için, ayarladığınız güvenilir sınır (IP veya VNet) içinde bir makinede olmanız gerekir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Senaryolar

Depolama hesabınızın güvenliğini sağlamak için, varsayılan olarak genel uç noktasındaki tüm ağlardan (internet trafiği dahil) trafiğe erişimi reddetmek üzere bir kural yapılandırmanız gerekir. Ardından, belirli sanal ağlardan gelen trafiğe erişim veren kurallar yapılandırmanız gerekir. Ayrıca, belirli bir internet veya şirket içi istemcilerden gelen bağlantıları etkinleştiren seçili genel İnternet IP adresi aralıklarından gelen trafiğe erişim vermek için kurallar da yapılandırabilirsiniz. Bu yapılandırma, uygulamalarınız için güvenli bir ağ sınırı oluşturmanıza olanak sağlar.

Belirli sanal ağlardan ve aynı depolama hesabındaki genel IP adres aralıklarından erişime izin veren güvenlik duvarı kurallarını birleştirebilirsiniz. Depolama güvenlik duvarı kuralları mevcut depolama hesaplarına veya yeni depolama hesapları oluştururken uygulanabilir.

Depolama güvenlik duvarı kuralları, bir depolama hesabının genel uç noktası için geçerlidir. Bir depolama hesabının özel uç noktaları için trafiğe izin vermek üzere herhangi bir güvenlik duvarı erişim kuralına gerek yoktur. Özel bir uç noktanın oluşturulmasını onaylama işlemi, Özel uç noktasını barındıran alt ağdan gelen trafiğe örtülü erişim verir.

Ağ kuralları, REST ve SMB dahil olmak üzere Azure depolama için tüm ağ protokollerinde zorlanır. Azure portal, Depolama Gezgini ve AZCopy gibi araçları kullanarak verilere erişmek için açık ağ kurallarının yapılandırılması gerekir.

Ağ kuralları uygulandıktan sonra, tüm istekler için zorlanır. Belirli bir IP adresine erişim izni veren SAS belirteçleri, belirteç tutucusuna erişimi sınırlandırmaya, ancak yapılandırılan ağ kurallarından daha fazla yeni erişim vermemelidir.

Sanal makine disk trafiği (bağlama ve çıkarma işlemleri ve disk GÇ dahil) ağ kurallarından etkilenmez. Sayfa bloblarına REST erişimi ağ kuralları tarafından korunur.

Klasik depolama hesapları, güvenlik duvarlarını ve sanal ağları desteklemez.

VM 'Leri yedeklemek ve geri yüklemek için bir özel durum oluşturarak, ağ kuralları uygulanmış depolama hesaplarında yönetilmeyen diskleri kullanabilirsiniz. Bu işlem, bu makalenin [özel durumları Yönet](#manage-exceptions) bölümünde belgelenmiştir. Güvenlik Duvarı özel durumları, zaten Azure tarafından yönetildikleri için yönetilen disklerle ilgili değildir.

## <a name="change-the-default-network-access-rule"></a>Varsayılan ağ erişim kuralını değiştirme

Varsayılan olarak, depolama hesapları herhangi bir ağ üzerindeki istemcilerden gelen bağlantıları kabul eder. Seçili ağlara erişimi sınırlamak için öncelikle varsayılan eylemi değiştirmeniz gerekir.

> [!WARNING]
> Ağ kurallarında değişiklikler yapmak uygulamalarınızın Azure Depolamaya bağlanma yeteneğini etkileyebilir. Varsayılan ağ kuralını **Reddet** olarak ayarlamak, erişim **izni** veren belirli ağ kuralları da uygulanmamışsa veriye tüm erişimi engeller. Varsayılan kuralı erişimi reddedecek şekilde değiştirmeden önce ağ kurallarını kullanarak izin verilen ağlara erişim verdiğinizden emin olun.

### <a name="managing-default-network-access-rules"></a>Varsayılan ağ erişim kurallarını yönetme

Azure portal, PowerShell veya CLIv2 aracılığıyla depolama hesapları için varsayılan ağ erişim kurallarını yönetebilirsiniz.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Güvenli hale getirmek istediğiniz depolama hesabına gidin.

2. **Ağ iletişimi** adlı ayarlar menüsünde öğesini seçin.

3. Erişimi varsayılan olarak reddetmek için, **Seçili ağlardan** erişime izin ver ' i seçin. Tüm ağlardan gelen trafiğe izin vermek için **Tüm ağlardan** erişime izin vermeyi seçin.

4. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps).

2. Depolama hesabı için varsayılan kuralın durumunu görüntüleyin.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Varsayılan olarak ağ erişimini reddetmek için varsayılan kuralı ayarlayın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Varsayılan olarak ağ erişimine izin vermek için varsayılan kuralı ayarlayın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli).

2. Depolama hesabı için varsayılan kuralın durumunu görüntüleyin.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Varsayılan olarak ağ erişimini reddetmek için varsayılan kuralı ayarlayın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Varsayılan olarak ağ erişimine izin vermek için varsayılan kuralı ayarlayın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Bir sanal ağdan erişim izni verme

Depolama hesaplarını yalnızca belirli alt ağlardan erişime izin verecek şekilde yapılandırabilirsiniz. İzin verilen alt ağlar, farklı bir Azure Active Directory kiracısına ait abonelikler de dahil olmak üzere, aynı abonelikte bulunan bir VNet 'e veya farklı bir aboneliğe ait olabilir.

VNet içinde Azure depolama için bir [hizmet uç noktası](../../virtual-network/virtual-network-service-endpoints-overview.md) etkinleştirin. Hizmet uç noktası, trafiği VNet 'ten Azure depolama hizmetine en uygun bir yol üzerinden yönlendirir. Alt ağın ve sanal ağın kimlikleri de her istekle birlikte iletilir. Yöneticiler daha sonra depolama hesabı için ağ kurallarını, isteklerin bir sanal ağdaki belirli alt ağlardan alınmasına izin veren ağ kurallarını yapılandırabilir. Bu ağ kuralları aracılığıyla erişim izni verilen istemciler, verileri erişmek için depolama hesabının yetkilendirme gereksinimlerini karşılamaya devam etmelidir.

Her depolama hesabı, [IP ağ kuralları](#grant-access-from-an-internet-ip-range)ile birleştirilebilir en fazla 200 sanal ağ kuralını destekler.

### <a name="available-virtual-network-regions"></a>Kullanılabilir sanal ağ bölgeleri

Genel olarak, hizmet uç noktaları aynı Azure bölgesindeki sanal ağlar ve hizmet örnekleri arasında çalışır. Azure depolama ile hizmet uç noktaları kullanılırken, bu kapsam [eşleştirilmiş bölgeyi](../../best-practices-availability-paired-regions.md)dahil etmek için büyür. Hizmet uç noktaları bölgesel yük devretme sırasında sürekliliği ve salt Erişimli Coğrafi olarak yedekli depolama (RA-GRS) örneklerine erişim sağlar. Bir sanal ağdan bir depolama hesabına erişim izni veren ağ kuralları da herhangi bir RA-GRS örneğine erişim sağlar.

Bölgesel bir kesinti sırasında olağanüstü durum kurtarma için planlama yaparken, sanal ağları eşleştirilmiş bölgede önceden oluşturmalısınız. Bu alternatif sanal ağlardan erişim sağlayan ağ kuralları ile Azure depolama için hizmet uç noktalarını etkinleştirin. Ardından bu kuralları coğrafi olarak yedekli depolama hesaplarınıza uygulayın.

> [!NOTE]
> Hizmet uç noktaları, sanal ağın ve belirtilen bölge çiftinin bölgesi dışındaki trafiğe uygulanmaz. Yalnızca bir depolama hesabının birincil bölgesindeki veya belirtilen eşlenmiş bölgedeki depolama hesaplarına sanal ağlardan erişim sağlayan ağ kuralları uygulayabilirsiniz.

### <a name="required-permissions"></a>Gerekli izinler

Depolama hesabına bir sanal ağ kuralı uygulamak için, kullanıcının eklenen alt ağlar üzerinde uygun izinleri olmalıdır. Bir kuralı uygulamak, bir [depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) veya `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure Kaynak sağlayıcısı Işlemi](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) için özel bir Azure rolü aracılığıyla izin verilen bir kullanıcı tarafından gerçekleştirilebilir.

Depolama hesabı ve erişim verilen sanal ağlar, farklı bir Azure AD kiracısının parçası olan abonelikler de dahil olmak üzere farklı aboneliklerde olabilir.

> [!NOTE]
> Farklı bir Azure Active Directory kiracının parçası olan sanal ağlardaki alt ağlara erişim izni veren kuralların yapılandırılması Şu anda yalnızca PowerShell, CLı ve REST API 'Leri aracılığıyla desteklenir. Bu kurallar, portalda görüntülenebilse de Azure portal aracılığıyla yapılandırılamaz.

### <a name="managing-virtual-network-rules"></a>Sanal ağ kurallarını yönetme

Azure portal, PowerShell veya CLIv2 aracılığıyla depolama hesapları için sanal ağ kurallarını yönetebilirsiniz.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Güvenli hale getirmek istediğiniz depolama hesabına gidin.

2. **Ağ iletişimi** adlı ayarlar menüsünde öğesini seçin.

3. **Seçili ağlardan** erişime izin vermeyi seçtiğinizden emin olun.

4. Yeni bir ağ kuralıyla bir sanal ağa erişim vermek için, **sanal ağlar** altında **var olan sanal ağı ekle**' yi seçin, **sanal ağlar** ve **alt ağlar** seçeneklerini belirleyin ve ardından **Ekle**' yi seçin. Yeni bir sanal ağ oluşturmak ve bu ağa erişim vermek için **Yeni sanal ağ ekle**' yi seçin. Yeni sanal ağı oluşturmak için gereken bilgileri girin ve ardından **Oluştur**' u seçin.

    > [!NOTE]
    > Azure Storage için bir hizmet uç noktası, seçilen sanal ağ ve alt ağlar için önceden yapılandırılmadıysa, bu işlemin bir parçası olarak yapılandırabilirsiniz.
    >
    > Şu anda, kural oluşturma sırasında seçim için yalnızca aynı Azure Active Directory kiracıya ait olan sanal ağlar gösterilir. Başka bir kiracıya ait bir sanal ağdaki bir alt ağa erişim vermek için lütfen PowerShell, CLı veya REST API 'Leri kullanın.

5. Bir sanal ağ veya alt ağ kuralını kaldırmak için **...** öğesini seçerek sanal ağ veya alt ağ için bağlam menüsünü açın ve **Kaldır**' ı seçin.

6. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps).

2. Sanal ağ kurallarını listeleyin.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Mevcut bir sanal ağ ve alt ağ üzerinde Azure depolama için hizmet uç noktasını etkinleştirin.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait bir sanal ağa ait bir ağ kuralı eklemek için, "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" biçiminde tam nitelikli bir **Virtualnetworkresourceıd** parametresi kullanın.

5. Bir sanal ağ ve alt ağ için bir ağ kuralını kaldırın.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli).

2. Sanal ağ kurallarını listeleyin.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Mevcut bir sanal ağ ve alt ağ üzerinde Azure depolama için hizmet uç noktasını etkinleştirin.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Başka bir Azure AD kiracısına ait olan bir sanal ağa yönelik bir kural eklemek için, "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/" biçiminde tam bır alt ağ kimliği kullanın \<subnet-name\> .
    >
    > Başka bir Azure AD kiracısına ait olan bir sanal ağın alt ağ KIMLIĞINI almak için **abonelik** parametresini kullanabilirsiniz.

5. Bir sanal ağ ve alt ağ için bir ağ kuralını kaldırın.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

---

## <a name="grant-access-from-an-internet-ip-range"></a>İnternet IP aralığından erişim izni verme

IP ağ kuralları oluşturarak, belirli genel İnternet IP adresi aralıklarından erişime izin vermek için IP ağ kurallarını kullanabilirsiniz. Her depolama hesabı en fazla 200 kuralı destekler. Bu kurallar, belirli internet tabanlı hizmetlere ve şirket içi ağlara erişim verir ve genel İnternet trafiğini engeller.

Aşağıdaki kısıtlamalar IP adres aralıkları için geçerlidir.

- IP ağ kurallarına yalnızca **genel İnternet** IP adresleri için izin verilir. 

  Özel ağlar için ayrılan IP adresi aralıklarına ( [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)' de tanımlandığı gıbı) IP kurallarında izin verilmez. Özel ağlarda, _10. *_, _172,16. *_  -  _172,31. *_ ve _192,168. *_ ile başlayan adresler bulunur.

- *16.17.18.0/24* biçiminde veya *16.17.18.19* gibi ayrı IP adresleri olarak [CIDR gösterimini](https://tools.ietf.org/html/rfc4632) kullanarak izin verilen internet adres aralıklarını sağlamanız gerekir. 

- "/31" veya "/32" önek boyutları kullanılarak küçük adres aralıkları desteklenmez. Bu aralıklar tek tek IP adresi kuralları kullanılarak yapılandırılmalıdır. 

- Depolama Güvenlik Duvarı kurallarının yapılandırılması için yalnızca ıPV4 adresleri desteklenir.

IP ağ kuralları aşağıdaki durumlarda kullanılamaz:

- Depolama hesabı ile aynı Azure bölgesindeki istemcilerle erişimi kısıtlamak için.
  
  IP ağ kurallarının, depolama hesabıyla aynı Azure bölgesinden kaynaklanan isteklere etkisi yoktur. Aynı bölge isteklerine izin vermek için [sanal ağ kurallarını](#grant-access-from-a-virtual-network) kullanın. 

- Bir hizmet uç noktası olan VNet 'te bulunan [eşleştirilmiş bir bölgedeki](../../best-practices-availability-paired-regions.md) istemcilere erişimi kısıtlamak için.

- Depolama hesabıyla aynı bölgede dağıtılan Azure hizmetlerine erişimi kısıtlamak için.

  Depolama hesabı ile aynı bölgede dağıtılan hizmetler, iletişim için özel Azure IP adreslerini kullanır. Bu nedenle, belirli Azure hizmetlerine erişimi, genel giden IP adresi aralığına göre kısıtlayamazsınız.

### <a name="configuring-access-from-on-premises-networks"></a>Şirket içi ağlardan erişimi yapılandırma

Şirket içi ağlarınızdan bir IP ağ kuralıyla depolama hesabınıza erişim izni vermek için ağınız tarafından kullanılan internet 'e yönelik IP adreslerini belirlemeniz gerekir. Yardım için ağ yöneticinize başvurun.

Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../../expressroute/expressroute-introduction.md) kullanıyorsanız, kullanılan NAT IP adreslerini belirlemeniz gerekir. Ortak eşleme için, her bir ExpressRoute varsayılan olarak bağlantı hattında trafik Microsoft Azure omurga ağına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanılır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanmış veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute genel ve Microsoft eşlemesi için NAT](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.

### <a name="managing-ip-network-rules"></a>IP ağ kurallarını yönetme

Azure portal, PowerShell veya CLIv2 aracılığıyla depolama hesapları için IP ağ kurallarını yönetebilirsiniz.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Güvenli hale getirmek istediğiniz depolama hesabına gidin.

2. **Ağ iletişimi** adlı ayarlar menüsünde öğesini seçin.

3. **Seçili ağlardan** erişime izin vermeyi seçtiğinizden emin olun.

4. Bir internet IP aralığına erişim vermek için, **güvenlik duvarı**  >  **adres aralığı** altına IP adresini veya adres aralığını (CIDR biçiminde) girin.

5. Bir IP ağ kuralını kaldırmak için, adres aralığının yanındaki çöp kutusu simgesini seçin.

6. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps).

2. IP ağ kurallarını listeleyin.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Bir IP adresi aralığı için ağ kuralı ekleyin.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Tek bir IP adresi için bir ağ kuralını kaldırın.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Bir IP adresi aralığı için ağ kuralını kaldır.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli).

1. IP ağ kurallarını listeleyin.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Tek bir IP adresi için bir ağ kuralı ekleyin.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Bir IP adresi aralığı için ağ kuralı ekleyin.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Tek bir IP adresi için bir ağ kuralını kaldırın.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Bir IP adresi aralığı için ağ kuralını kaldır.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Azure Kaynak örneklerinden (Önizleme) erişim izni verme

Bazı durumlarda, bir uygulama bir sanal ağ veya bir IP adresi kuralıyla yalıtıtılmamış Azure kaynaklarına bağlı olabilir. Ancak, hala yalnızca uygulamanızın Azure kaynaklarına yönelik depolama hesabı erişimini güvenli hale getirmek ve kısıtlamak istersiniz. Depolama hesaplarını, bir kaynak örneği kuralı oluşturarak bazı Azure hizmetlerinin belirli kaynak örneklerine erişime izin verecek şekilde yapılandırabilirsiniz. 

Kaynak örneğinin depolama hesabı verilerinde gerçekleştirebileceği işlem türleri, kaynak örneğinin [Azure rol atamaları](storage-auth-aad.md#assign-azure-roles-for-access-rights) tarafından belirlenir. Kaynak örnekleri, depolama hesabınızla aynı kiracıdan olmalıdır, ancak Kiracıdaki herhangi bir aboneliğe ait olabilir.

> [!NOTE]
> Bu özellik genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde kullanılabilir.

> [!NOTE]
> Kaynak örneği kuralları şu anda yalnızca Azure SYNAPSE için desteklenmektedir. Bu makalenin [sistem tarafından atanan yönetilen kimlik ' i temel alan güvenilir erişim](#trusted-access-system-assigned-managed-identity) bölümünde listelenen diğer Azure hizmetleri için destek, önümüzdeki haftalarda kullanıma sunulacaktır.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kaynak ağ kuralları ekleyebilir veya kaldırabilirsiniz.

1. Başlamak için [Azure Portal](https://portal.azure.com/) oturum açın.

2. Depolama hesabınızı bulun ve hesaba genel bakış ' ı görüntüleyin.

3. Ağ iletişimi için yapılandırma sayfasını göstermek üzere **ağ** ' ı seçin.

4. **Kaynak türü** aşağı açılan listesinde, kaynak örneğinizin kaynak türünü seçin. 

5. **Örnek adı** aşağı açılan listesinden kaynak örneğini seçin. Ayrıca, tüm kaynak örneklerini etkin kiracı, abonelik veya kaynak grubuna dahil etme seçeneğini de belirleyebilirsiniz.

6. Değişikliklerinizi uygulamak için **Kaydet**’i seçin. Kaynak örneği, ağ ayarları sayfasının **kaynak örnekleri** bölümünde görünür. 

Kaynak örneğini kaldırmak için :::image type="icon" source="media/storage-network-security/delete-icon.png"::: kaynak örneğinin yanındaki Sil simgesini () seçin.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kaynak ağ kuralları eklemek veya kaldırmak için PowerShell komutlarını kullanabilirsiniz.

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya ağ kurallarının hiçbir etkisi yoktur.

#### <a name="install-the-preview-module"></a>Önizleme modülünü yükler

PowershellGet modülünün en son sürümünü yükler. Ardından, PowerShell konsolunu kapatıp yeniden açın.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Install **az. Storage** Preview Module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

PowerShell modüllerinin nasıl yükleneceği hakkında daha fazla bilgi için bkz [. Azure PowerShell modülünü Install](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Erişim verme

Bir kaynak örneğinden erişim izni veren bir ağ kuralı ekleyin.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Ağ kuralı kümesini değiştirerek aynı anda birden çok kaynak örneği belirtin.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Erişimi kaldırma

Bir kaynak örneğinden erişim izni veren bir ağ kuralını kaldırın.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Kaynak örneklerinden erişim izni veren tüm ağ kurallarını kaldırın.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>İzin verilen kaynak örneklerinin listesini görüntüleme

Depolama hesabına erişim izni verilen kaynak örneklerinin tüm listesini görüntüleyin.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kaynak ağ kuralları eklemek veya kaldırmak için Azure CLı komutlarını kullanabilirsiniz.

#### <a name="install-the-preview-extension"></a>Önizleme uzantısını yükler

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Daha sonra, yüklediğiniz Azure CLı sürümünün `2.13.0` aşağıdaki komutu kullanarak veya daha yüksek olduğunu doğrulayın.

   ```azurecli
   az --version
   ```

   Azure CLı sürümünüz daha düşükse `2.13.0` , daha sonra yeni bir sürüm yüklersiniz. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

3. Önizleme uzantısını yüklemek için aşağıdaki komutu yazın.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Erişim verme

Bir kaynak örneğinden erişim izni veren bir ağ kuralı ekleyin.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Erişimi kaldırma

Bir kaynak örneğinden erişim izni veren bir ağ kuralını kaldırın.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>İzin verilen kaynak örneklerinin listesini görüntüleme

Depolama hesabına erişim izni verilen kaynak örneklerinin tüm listesini görüntüleyin.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Güvenilen Azure hizmetlerine erişim izni verme 

Bazı Azure Hizmetleri, ağ kurallarınıza dahil olmayan ağlardan çalışır. Diğer uygulamalar için ağ kurallarını koruyarak, bu tür güvenilir Azure hizmetlerinin depolama hesabına erişimine izin verebilirsiniz. Bu güvenilen hizmetler, depolama hesabınıza güvenli bir şekilde bağlanmak için güçlü kimlik doğrulama kullanır.

Ağ kuralı özel durumu oluşturarak güvenilir Azure hizmetlerine erişim izni verebilirsiniz. Adım adım yönergeler için, bu makalenin [özel durumları yönetme](#manage-exceptions) bölümüne bakın.

Güvenilen Azure hizmetlerine erişim izni verdiğinizde, aşağıdaki erişim türlerini verirsiniz:

- Aboneliğinizde kayıtlı kaynaklara yönelik seçme işlemlerine yönelik güvenilen erişim.
- Sistem tarafından atanan yönetilen kimliğe göre kaynaklara güvenilir erişim.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Aboneliğinizde kayıtlı kaynaklar için güvenilir erişim

**Aboneliğinizde kayıtlı** olan bazı hizmetlerin kaynakları, günlük veya yedekleme yazma gibi seçim işlemleri için **aynı abonelikte** depolama hesabınıza erişebilir.  Aşağıdaki tabloda, her bir hizmet ve izin verilen işlemler açıklanmaktadır. 

| Hizmet                  | Kaynak sağlayıcısı adı     | İzin verilen işlemler                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | IAAS sanal makinelerinde yedeklemeleri çalıştırın ve yönetilmeyen diskleri geri yükler. (yönetilen diskler için gerekli değildir). [Daha fazla bilgi edinin](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft. DataBox          | Data Box kullanarak Azure 'a veri aktarmayı sağlar. [Daha fazla bilgi edinin](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Özel görüntü oluşturma ve yapıt yüklemesi. [Daha fazla bilgi edinin](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Blob Storage olay yayımlamayı etkinleştirin ve Event Grid depolama sıralarında yayımlamaya izin verin. [BLOB depolama olayları](../../event-grid/overview.md#event-sources) ve [kuyruklarda yayımlama](../../event-grid/event-handlers.md)hakkında bilgi edinin. |
| Azure Event Hubs         | Microsoft. EventHub         | Event Hubs yakalama ile verileri arşivleme. [Daha Fazla Bilgi Edinin](../../event-hubs/event-hubs-capture-overview.md). |
| Azure Dosya Eşitleme          | Microsoft. Storagessync      | Şirket içi dosya sunucunuzu Azure dosya paylaşımları için bir önbelleğe dönüştürmenizi sağlar. Çok siteli eşitleme, hızlı olağanüstü durum kurtarma ve bulut tarafı yedekleme için izin verme. [Daha fazla bilgi edinin](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Yeni bir HDInsight kümesi için varsayılan dosya sisteminin başlangıçtaki içeriğini sağlayın. [Daha fazla bilgi edinin](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Içeri aktarma dışarı aktarma      | Microsoft. ımportexport     | Azure Storage Içeri/dışarı aktarma hizmeti kullanılarak verilerin Azure depolama 'ya aktarılmasını veya Azure Storage 'dan dışarı aktarılmasını sağlar. [Daha fazla bilgi edinin](../../import-export/storage-import-export-service.md).  |
| Azure İzleyici            | Microsoft.Insights         | Kaynak günlükleri, Azure Active Directory oturum açma ve denetim günlükleri ve Microsoft Intune Günlükler dahil olmak üzere, izleme verilerinin güvenli bir depolama hesabına yazılmasına izin verir. [Daha fazla bilgi edinin](../../azure-monitor/roles-permissions-security.md). |
| Azure ağı         | Microsoft.Network          | Ağ Izleyicisi ve Trafik Analizi hizmetleri dahil olmak üzere ağ trafiği günlüklerini depolayın ve çözümleyin. [Daha fazla bilgi edinin](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft. Sıterecovery     | Güvenlik Duvarı özellikli önbellek, kaynak veya hedef depolama hesapları kullanırken Azure IaaS sanal makinelerinin olağanüstü durum kurtarma için çoğaltmayı etkinleştirin.  [Daha fazla bilgi edinin](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimliğe dayalı güvenilen erişim

Aşağıdaki tabloda, bu hizmetlerin kaynak örneklerine uygun izin verilirse, depolama hesabı verilerinize erişebilen hizmetler listelenmektedir. İzin vermek için, her kaynak örneği için [sistem tarafından atanan yönetilen kimliğe](../../active-directory/managed-identities-azure-resources/overview.md) açık [bir şekilde Azure rolü atamanız](storage-auth-aad.md#assign-azure-roles-for-access-rights) gerekir. Bu durumda, örnek için erişim kapsamı yönetilen kimliğe atanan Azure rolüne karşılık gelir. 

> [!TIP]
> Belirli kaynaklara erişim izni vermek için önerilen yol, kaynak örneği kurallarını kullanmaktır. Belirli kaynak örneklerine erişim vermek için, bu makalenin [Azure Kaynak örnekleri 'nden (Önizleme) erişim Izni verme](#grant-access-specific-instances) bölümüne bakın.


| Hizmet                        | Kaynak sağlayıcısı adı                 | Amaç            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | İlkeler kullanılarak güvenlik duvarının arkasındaki depolama hesaplarına API Yönetimi hizmeti erişimi sağlar. [Daha fazla bilgi edinin](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Bilişsel Arama         | Microsoft. Search/searchServices        | Bilişsel Arama hizmetlerinin dizin oluşturma, işleme ve sorgulama için depolama hesaplarına erişmesine olanak sağlar. |
| Azure Bilişsel Hizmetler       | Microsoft. Biliveservıce/hesapları    | Bilişsel hizmetler 'in depolama hesaplarına erişmesine olanak sağlar. |
| Azure Container Registry Görevleri | Microsoft. ContainerRegistry/kayıt defterleri | ACR görevleri, kapsayıcı görüntüleri oluştururken depolama hesaplarına erişebilir. |
| Azure Data Factory             | Microsoft. DataFactory/Factory        | ADF çalışma zamanı aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure Veri Paylaşımı               | Microsoft. DataShare/hesapları           | Veri paylaşımından depolama hesaplarına erişime izin verir. |
| Azure DevTest Labs             | Microsoft. DevTestLab/Labs              | DevTest Labs aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | IoT Hub 'ından gelen verilerin blob depolamaya yazılmasına izin verir. [Daha fazla bilgi edinin](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/iş akışları              | Mantıksal uygulamaların depolama hesaplarına erişmesini sağlar. [Daha fazla bilgi edinin](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning Hizmeti | Microsoft.MachineLearningServices      | Yetkili Azure Machine Learning çalışma alanları BLOB depolama alanına deneme çıkış, model ve Günlükler yazar ve verileri okur. [Daha fazla bilgi edinin](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft. Media/mediaservices          | Media Services aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure Geçişi                  | Microsoft. Migrate/migrateprojects      | Azure geçişi aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure Purview                  | Microsoft. purview/hesapları             | Takiview 'ın depolama hesaplarına erişmesine izin verir. |
| Azure Remote Rendering         | Microsoft. MixedReality/remoteRenderingAccounts | Uzaktan Işleme aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure Site Recovery            | Microsoft. RecoveryServices/kasa      | Site Recovery aracılığıyla depolama hesaplarına erişime izin verir. |
| Azure SQL Veritabanı             | Microsoft.Sql                          | Güvenlik duvarının arkasındaki depolama hesaplarına denetim verileri [yazılmasına](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) izin verir. |
| Azure Synapse Analytics        | Microsoft.Sql                          | COPY ifadesini veya PolyBase 'i (adanmış havuzda) veya `openrowset` sunucusuz havuzdaki işlev ve dış tabloları kullanarak belırlı SQL veritabanlarından verilerin içeri ve dışarı aktarılmasını sağlar. [Daha fazla bilgi edinin](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft. StreamAnalytics              | Akış işindeki verilerin blob depolamaya yazılmasına izin verir. [Daha fazla bilgi edinin](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft. SYNAPSE/çalışma alanları           | Azure SYNAPSE Analytics 'ten Azure Storage verilerine erişim sağlar. |

## <a name="grant-access-to-storage-analytics"></a>Depolama analizlere erişim izni verme

Bazı durumlarda, ağ sınırının dışında kaynak günlüklerine ve ölçümlere erişimi de gereklidir. Depolama hesabına güvenilen hizmetler erişimi yapılandırılırken, bir ağ kuralı özel durumu oluşturarak günlük dosyaları, ölçüm tabloları veya her ikisi için okuma erişimine izin verebilirsiniz. Adım adım yönergeler için aşağıdaki **özel durumları Yönet** bölümüne bakın. Depolama analiziyle çalışma hakkında daha fazla bilgi edinmek için bkz. [Azure Storage Analytics 'ı kullanarak günlükleri ve ölçüm verilerini toplama](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Özel durumları yönetme

Ağ kuralı özel durumlarını Azure portal, PowerShell veya Azure CLı v2 aracılığıyla yönetebilirsiniz.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Güvenli hale getirmek istediğiniz depolama hesabına gidin.

2. **Ağ iletişimi** adlı ayarlar menüsünde öğesini seçin.

3. **Seçili ağlardan** erişime izin vermeyi seçtiğinizden emin olun.

4. **Özel durumlar**' ın altında, vermek istediğiniz özel durumları seçin.

5. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) yükleyip [oturum açın](/powershell/azure/authenticate-azureps).

2. Depolama hesabı ağ kuralları için özel durumları görüntüleyin.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Özel durumları depolama hesabı ağ kuralları için yapılandırın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Depolama hesabı ağ kuralları için özel durumları kaldırın.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya özel durumların kaldırılması etkisizdir.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli).

2. Depolama hesabı ağ kuralları için özel durumları görüntüleyin.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Özel durumları depolama hesabı ağ kuralları için yapılandırın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Depolama hesabı ağ kuralları için özel durumları kaldırın.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> [Varsayılan kuralı](#change-the-default-network-access-rule) **Reddet** olarak ayarladığınızdan emin olun veya özel durumların kaldırılması etkisizdir.

---

## <a name="next-steps"></a>Sonraki adımlar

[Hizmet uç noktalarında](../../virtual-network/virtual-network-service-endpoints-overview.md)Azure ağ hizmeti uç noktaları hakkında daha fazla bilgi edinin.

Azure [Storage Güvenlik Kılavuzu](../blobs/security-recommendations.md)'Nda Azure Storage Security 'de daha ayrıntılı bilgi edinin.