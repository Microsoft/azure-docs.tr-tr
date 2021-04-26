---
title: Azure Key Vault güvenlik duvarlarını ve sanal ağları Yapılandırma-Azure Key Vault
description: Key Vault güvenlik duvarlarını ve sanal ağları yapılandırmak için adım adım yönergeler
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1f2df113b855ef85906e00a0316b09870ff20fb7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814379"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma

Bu makale, Azure Key Vault güvenlik duvarının nasıl yapılandırılacağı hakkında rehberlik sağlar. Bu belge, Key Vault güvenlik duvarının farklı yapılandırmalarının ayrıntılarını kapsar ve Azure Key Vault diğer uygulamalarla ve Azure hizmetleriyle çalışmak üzere nasıl yapılandırılacağına ilişkin adım adım yönergeler sağlar.

Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Güvenlik Duvarı ayarları

Bu bölüm Azure Key Vault güvenlik duvarının yapılandırılabilme farklı yollarını kapsar.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault güvenlik duvarı devre dışı (varsayılan)

Varsayılan olarak, yeni bir Anahtar Kasası oluşturduğunuzda Azure Key Vault güvenlik duvarı devre dışı bırakılır. Tüm uygulamalar ve Azure hizmetleri anahtar kasasına erişebilir ve istekleri anahtar kasasına gönderebilir. Bu yapılandırma, herhangi bir kullanıcının anahtar kasasında işlem gerçekleştirebileceği anlamına gelmez. Anahtar Kasası hala Azure Active Directory kimlik doğrulaması ve erişim ilkesi izinleri gerektirerek Anahtar Kasası 'nda depolanan gizli dizileri, anahtarları ve sertifikaları kısıtlar. Anahtar Kasası kimlik doğrulamasını daha ayrıntılı olarak anlamak için, [burada](./authentication-fundamentals.md)Anahtar Kasası kimlik doğrulaması temelleri belgesine bakın. Daha fazla bilgi için bkz. [erişim Azure Key Vault bir güvenlik duvarı arkasında](./access-behind-firewall.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault Güvenlik Duvarı etkin (yalnızca güvenilir hizmetler)

Key Vault güvenlik duvarını etkinleştirdiğinizde, ' güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına Izin ver ' seçeneği sunulur. Güvenilen hizmetler listesi, her bir Azure hizmetini kapsamaz. Örneğin, Azure DevOps güvenilir Hizmetler listesinde değildir. **Bu, güvenilir Hizmetler listesinde görünmeyen hizmetlerin güvenilir veya güvenli olmayan olduğunu göstermez.** Güvenilen hizmetler listesi, Microsoft 'un hizmette çalışan tüm kodu denetlediği hizmetleri kapsar. Kullanıcılar Azure DevOps gibi Azure hizmetlerinde özel kod yazabileceği için, Microsoft bu hizmet için bir paket onayı oluşturma seçeneği sağlamaz. Ayrıca, bir hizmet Güvenilen hizmet listesinde göründüğünden, tüm senaryolarda izin verilmediği anlamına gelmez. 

Kullanmaya çalıştığınız bir hizmetin güvenilir hizmet listesinde olup olmadığını öğrenmek için lütfen [aşağıdaki belgeye bakın](./overview-vnet-service-endpoints.md#trusted-services).
Nasıl yapılır Kılavuzu için [Portal, Azure CLI ve PowerShell](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal) için buradaki yönergeleri izleyin

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault Güvenlik Duvarı etkin (IPv4 adresleri ve aralıkları-statik IP 'Ler)

Key Vault güvenlik duvarı aracılığıyla anahtar kasasına erişmek üzere belirli bir hizmeti yetkilendirmek isterseniz, Anahtar Kasası güvenlik duvarı izin verilenler listesine IP adresini ekleyebilirsiniz. Bu yapılandırma, statik IP adresleri veya iyi bilinen aralıklar kullanan hizmetler için idealdir. Bu durum için 1000 CıDR aralıklarının bir sınırı vardır.

Bir Web uygulaması veya mantıksal uygulama gibi bir Azure kaynağının IP adresine veya aralığına izin vermek için aşağıdaki adımları gerçekleştirin.

1. Azure portalında oturum açma
1. Kaynağı seçin (hizmetin belirli bir örneği)
1. ' Ayarlar ' altındaki ' Özellikler ' dikey penceresine tıklayın
1. "IP adresi" alanını bulun.
1. Bu değeri veya aralığı kopyalayın ve Anahtar Kasası güvenlik duvarı izin verilenler listesine girin.

Tüm Azure hizmetine izin vermek için Key Vault güvenlik duvarı aracılığıyla Azure için genel olarak belgelenen veri merkezi IP adresleri listesini [kullanın.](https://www.microsoft.com/download/details.aspx?id=41653) İstediğiniz bölgede istediğiniz hizmetle ilişkili IP adreslerini bulun ve yukarıdaki adımları kullanarak bu IP adreslerini Anahtar Kasası güvenlik duvarına ekleyin.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault Güvenlik Duvarı etkin (sanal ağlar-dinamik IP 'Ler)

Anahtar Kasası aracılığıyla bir sanal makine gibi bir Azure kaynağına izin vermeyi deniyorsanız, statik IP adresleri kullanabilmeyebilirsiniz ve Azure sanal makinelerinin tüm IP adreslerinin anahtar kasanıza erişmesine izin vermek isteyebilirsiniz.

Bu durumda, kaynağı bir sanal ağ içinde oluşturmanız ve ardından belirli sanal ağ ve alt ağdan gelen trafiğin anahtar kasanıza erişmesine izin vermeniz gerekir. Bunu yapmak için aşağıdaki adımları uygulayın.

1. Azure portalında oturum açma
1. Yapılandırmak istediğiniz anahtar kasasını seçin
1. ' Ağ ' dikey penceresini seçin
1. ' + Var olan sanal ağı Ekle ' öğesini seçin
1. Anahtar Kasası güvenlik duvarı üzerinden izin vermek istediğiniz sanal ağı ve alt ağı seçin.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault Güvenlik Duvarı etkin (özel bağlantı)

Anahtar Kasanızda bir özel bağlantı bağlantısının nasıl yapılandırılacağını anlamak için [lütfen belgeye bakın](./private-link-service.md).

> [!IMPORTANT]
> Güvenlik duvarı kuralları etkin olduktan sonra, kullanıcılar, istekleri izin verilen sanal ağlardan veya IPv4 adres aralıklarından başlatıldığında yalnızca Key Vault [veri düzlemi](security-features.md#privileged-access) işlemleri gerçekleştirebilir. Bu, Azure portal Key Vault erişmek için de geçerlidir. Kullanıcılar Azure portal bir anahtar kasasına gözatabilse de, istemci makineleri izin verilenler listesinde yoksa anahtarları, parolaları veya sertifikaları listelemeyebilir. Bu, diğer Azure hizmetleri tarafından Key Vault seçiciyi de etkiler. Kullanıcılar, güvenlik duvarı kuralları istemci makinesini engelliyorsa, anahtar kasalarının listesini görebilirler, ancak liste anahtarlarını göremez.

> [!NOTE]
> Aşağıdaki yapılandırma sınırlamalarından haberdar olun:
> * En fazla 127 sanal ağ kuralına ve 127 IPv4 kuralına izin verilir. 
> * IP ağ kurallarına yalnızca genel IP adresleri için izin verilir. Özel ağlar için ayrılan IP adresi aralıklarına (RFC 1918 ' de tanımlandığı gibi) IP kurallarında izin verilmez. Özel ağlarda **10.**, **172.16-31** ve 192,168 ile başlayan adresler bulunur **.** 
> * Şu anda yalnızca IPv4 adresleri destekleniyor.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

Azure portal kullanarak Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma:

1. Güvenli hale getirmek istediğiniz anahtar kasasını inceleyin.
2. **Ağ iletişimi**' ni seçin ve ardından **güvenlik duvarları ve sanal ağlar** sekmesini seçin.
3. **Erişime Izin ver**' ın altında **Seçili ağlar**' ı seçin.
4. Var olan sanal ağları güvenlik duvarları ve sanal ağ kurallarına eklemek için **+ var olan sanal ağları Ekle**' yi seçin.
5. Açılan yeni dikey pencerede, bu Anahtar Kasası 'na erişime izin vermek istediğiniz aboneliği, sanal ağları ve alt ağları seçin. Seçtiğiniz sanal ağlarda ve alt ağlarda hizmet uç noktaları etkinleştirilmemişse, hizmet uç noktalarını etkinleştirmek istediğinizi onaylayın ve **Etkinleştir**' i seçin. Etkili olması 15 dakika kadar sürebilir.
6. **IP ağları** altında [CIDR (sınıfsız etki alanları arası yönlendirme) gösteriminde](https://tools.ietf.org/html/rfc4632) veya tek tek IP adreslerinde IPv4 adresi aralıklarını yazarak IPv4 adres aralıkları ekleyin.
7. Microsoft güvenilen hizmetlerin Key Vault güvenlik duvarını atlamasına izin vermek istiyorsanız ' Evet ' seçeneğini belirleyin. Geçerli Key Vault güvenilen hizmetlerin tam listesi için lütfen aşağıdaki bağlantıya bakın. [Güvenilen Hizmetleri Azure Key Vault](./overview-vnet-service-endpoints.md#trusted-services)
7. **Kaydet**’i seçin.

Ayrıca yeni sanal ağlar ve alt ağlar ekleyebilir ve ardından **+ Yeni sanal ağ ekle**' yi seçerek yeni oluşturulan sanal ağlar ve alt ağlar için hizmet uç noktalarını etkinleştirebilirsiniz. Ardından istemleri izleyin.

## <a name="use-the-azure-cli"></a>Azure CLI kullanma 

Azure CLı kullanarak Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma

1. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [oturum açın](/cli/azure/authenticate-azure-cli).

2. Kullanılabilir sanal ağ kurallarını listeleyin. Bu anahtar kasası için herhangi bir kural ayarlamadıysanız liste boş olur.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Mevcut bir sanal ağ ve alt ağ üzerinde Key Vault için bir hizmet uç noktası etkinleştirin.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Trafiğe izin verilecek bir IP adresi aralığı ekleyin.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Bu anahtar kasasının güvenilir hizmetler tarafından erişilebilir olması gerekiyorsa, olarak ayarlayın `bypass` `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Varsayılan eylemi olarak ayarlayarak ağ kurallarını açın `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell kullanarak Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma:

1. En son [Azure PowerShell](/powershell/azure/install-az-ps)yükleyip [oturum açın](/powershell/azure/authenticate-azureps).

2. Kullanılabilir sanal ağ kurallarını listeleyin. Bu anahtar kasası için herhangi bir kural ayarlanmamışsa liste boş olur.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Mevcut bir sanal ağ ve alt ağ üzerindeki Key Vault için hizmet uç noktasını etkinleştirin.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Bir sanal ağ ve alt ağ için bir ağ kuralı ekleyin.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Trafiğe izin verilecek bir IP adresi aralığı ekleyin.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Bu anahtar kasasının güvenilir hizmetler tarafından erişilebilir olması gerekiyorsa, olarak ayarlayın `bypass` `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Varsayılan eylemi olarak ayarlayarak ağ kurallarını açın `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Başvurular
* ARM şablon başvurusu: [Azure Key Vault ARM şablon başvurusu](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLı komutları: [az keykasa Network-Rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlet 'leri: [Get-Azkeykasa,](/powershell/module/az.keyvault/get-azkeyvault) [Add-azkeyvaultnetworkrule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-azkeyvaultnetworkrule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-azkeyvaultnetworkruleset](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Sonraki adımlar

* [Key Vault için sanal ağ hizmeti uç noktaları](overview-vnet-service-endpoints.md)
* [Azure Key Vault güvenliğe genel bakış](security-features.md)
