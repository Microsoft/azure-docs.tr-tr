---
title: Azure Özel Bağlantı Hizmeti ile tümleştirme
description: Azure Key Vault Azure özel bağlantı hizmeti ile tümleştirme hakkında bilgi edinin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749555"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Key Vault'u Azure Özel Bağlantı ile tümleştirme

Azure özel bağlantı hizmeti, Azure hizmetlerine (örneğin, Azure Key Vault, Azure depolama ve Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar.

Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.

Daha fazla bilgi için bkz. [Azure özel bağlantısı nedir?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Önkoşullar

Bir anahtar kasasını Azure özel bağlantısıyla bütünleştirmek için şunlar gerekir:

- Bir Anahtar Kasası.
- Bir Azure sanal ağı.
- Sanal ağdaki bir alt ağ.
- Hem Anahtar Kasası hem de sanal ağ için sahip veya katkıda bulunan izinleri.

Özel uç noktanız ve sanal ağınız aynı bölgede olmalıdır. Portalı kullanarak özel uç nokta için bir bölge seçtiğinizde bu, yalnızca o bölgedeki sanal ağları otomatik olarak filtreleyecek. Anahtar kasanızın farklı bir bölgede olması olabilir.

Özel uç noktanız sanal ağınızda özel bir IP adresi kullanıyor.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Key Vault Azure portal kullanarak bir özel bağlantı bağlantısı kurun 

İlk olarak, [Azure Portal kullanarak sanal ağ oluşturma](../../virtual-network/quick-create-portal.md) bölümündeki adımları izleyerek bir sanal ağ oluşturun

Daha sonra yeni bir Anahtar Kasası oluşturabilir veya var olan bir anahtar kasası ile özel bağlantı kurabilirsiniz.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Yeni bir Anahtar Kasası oluşturma ve özel bağlantı bağlantısı kurma

[Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)veya [Azure PowerShell](../general/quick-create-powershell.md)ile yeni bir Anahtar Kasası oluşturabilirsiniz.

Anahtar Kasası temel bilgilerini yapılandırdıktan sonra, ağ sekmesini seçin ve şu adımları izleyin:

1. Ağ sekmesinde Özel uç nokta radyo düğmesini seçin.
1. Özel uç nokta eklemek için "+ Ekle" düğmesine tıklayın.

    ![' Anahtar Kasası oluşturma ' sayfasında ' ağ ' sekmesini gösteren ekran görüntüsü.](../media/private-link-service-1.png)
 
1. Özel uç nokta oluştur dikey penceresinin "konum" alanında, sanal ağınızın bulunduğu bölgeyi seçin. 
1. "Ad" alanında, bu özel uç noktayı tanımlamanızı sağlayacak açıklayıcı bir ad oluşturun. 
1. Bu özel bitiş noktasının, açılan menüden oluşturulmasını istediğiniz sanal ağı ve alt ağı seçin. 
1. "Özel bölge DNS ile tümleştirin" seçeneğini değiştirmeden bırakın.  
1. "Tamam" ı seçin.

    ![Ayarlar seçiliyken ' özel uç nokta oluştur ' sayfasını gösteren ekran görüntüsü.](../media/private-link-service-8.png)
 
Artık yapılandırılmış özel uç noktayı görebileceksiniz. Artık bu özel uç noktayı silme ve düzenleme seçeneğiniz vardır. "Gözden geçir + oluştur" düğmesini seçin ve Anahtar Kasası oluşturun. Dağıtımın tamamlanması 5-10 dakika sürer. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Mevcut bir anahtar kasası ile özel bağlantı kurun

Zaten bir anahtar kasanız varsa, aşağıdaki adımları izleyerek bir özel bağlantı bağlantısı oluşturabilirsiniz:

1. Azure portalında oturum açın. 
1. Arama çubuğuna "Anahtar Kasası" yazın
1. Özel uç nokta eklemek istediğiniz listeden anahtar kasasını seçin.
1. Ayarlar altında "ağ" sekmesini seçin
1. Sayfanın üst kısmındaki özel uç nokta bağlantıları sekmesini seçin
1. Sayfanın üst kısmındaki "+ özel uç nokta" düğmesini seçin.

    ![' Ağ ' sayfasında ' + özel uç nokta ' düğmesini gösteren ekran görüntüsü.](../media/private-link-service-3.png)
    ![' Özel uç nokta (Önizleme) oluştur sayfasında ' temel bilgiler ' sekmesini gösteren ekran görüntüsü.](../media/private-link-service-4.png)

Bu dikey pencereyi kullanarak, içindeki herhangi bir Azure kaynağı için özel bir uç nokta oluşturmayı tercih edebilirsiniz. Açılır menüleri kullanarak bir kaynak türü seçebilir ve dizininizdeki bir kaynağı seçebilir ya da kaynak KIMLIĞI kullanarak herhangi bir Azure kaynağına bağlanabilirsiniz. "Özel bölge DNS ile tümleştirin" seçeneğini değiştirmeden bırakın.  

![Geçerli dikey pencereyi kullanarak özel uç nokta eklemeyi gösteren ekran görüntüsü. ](../media/private-link-service-3.png)
 ![ ' Özel uç nokta (Önizleme) oluşturma sayfasına bir örnek gösteren ekran görüntüsü.](../media/private-link-service-4.png)

Özel bir uç nokta oluşturduğunuzda bağlantının onaylanması gerekir. Özel bir uç noktası oluşturduğunuz kaynak dizininizdeki ise, yeterli izinlere sahip olduğunuz için bağlantı isteğini onaylayabileceksiniz; başka bir dizindeki bir Azure kaynağına bağlanıyorsanız, bu kaynağın sahibinin bağlantı isteğinizi onaylamasını beklemeniz gerekir.

Dört sağlama durumu vardır:

| Hizmet eylemi sağla | Hizmet tüketicisi özel uç nokta durumu | Açıklama |
|--|--|--|
| Hiçbiri | Beklemede | Bağlantı el ile oluşturulur ve özel bağlantı kaynağı sahibinden onay bekliyor. |
| Onaylama | Onaylandı | Bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir. |
| Reddet | Reddedildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından reddedildi. |
| Kaldır | Bağlantı kesildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından kaldırıldı, Özel uç nokta bilgilendirici hale gelir ve temizlik için silinmelidir. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Azure portal kullanarak Key Vault özel bir uç nokta bağlantısını yönetme 

1. Azure portalında oturum açın.
1. Arama çubuğuna "Anahtar Kasası" yazın
1. Yönetmek istediğiniz anahtar kasasını seçin.
1. "Ağ" sekmesini seçin.
1. Bekleyen bağlantı varsa, sağlama durumunda "bekliyor" ile listelenmiş bir bağlantı görürsünüz. 
1. Onaylamak istediğiniz özel uç noktayı seçin
1. Onayla düğmesini seçin.
1. Reddetmek istediğiniz özel uç nokta bağlantıları varsa, bekleyen bir istek ya da var olan bağlantı olup olmadığına bakılmaksızın bağlantıyı seçin ve "Reddet" düğmesine tıklayın.

    ![Görüntü](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>CLı (Ilk kurulum) kullanarak Key Vault özel bağlantı bağlantısı kurma

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Özel uç nokta oluştur (otomatik olarak Onayla) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Özel uç nokta oluşturma (El Ile onay ISTEME) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Özel bağlantı bağlantılarını yönetme

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Özel DNS kayıtları ekleme
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Özel bağlantı bağlantısının çalışıp çalışmadığını doğrulama

Özel uç nokta kaynağının aynı alt ağı içindeki kaynakların, özel bir IP adresi üzerinden anahtar Kasanıza bağlı olduğunu ve doğru özel DNS bölge tümleştirmesini olduğunu doğrulamanız gerekir.

İlk olarak, [Azure Portal Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-portal.md) bölümündeki adımları izleyerek bir sanal makine oluşturun

"Ağ" sekmesinde:

1. Sanal ağ ve alt ağ belirtin. Yeni bir sanal ağ oluşturabilir veya var olan bir sanal ağı seçebilirsiniz. Mevcut bir tane seçilirse, bölgenin eşleştiğinden emin olun.
1. Genel bir IP kaynağı belirtin.
1. "NIC ağ güvenlik grubu" nda "hiçbiri" ni seçin.
1. "Yük Dengeleme" içinde "Hayır" ı seçin.

Komut satırını açın ve şu komutu çalıştırın:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Ortak bir uç nokta üzerinden bir anahtar kasasının IP adresini çözümlemek için NS arama komutunu çalıştırırsanız şuna benzer bir sonuç görürsünüz:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Özel bir uç nokta üzerinden bir anahtar kasasının IP adresini çözümlemek için NS arama komutunu çalıştırırsanız şuna benzer bir sonuç görürsünüz:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Sorun Giderme Kılavuzu

* Özel uç noktanın onaylanmış durumda olduğundan emin olmak için denetleyin. 
    1. Bunu Azure portalda denetleyebilir ve gerekirse düzeltebilirsiniz. Key Vault kaynağını açıp Ağ İletişimi seçeneğine tıklayın. 
    2. Ardından özel uç nokta bağlantıları sekmesini seçin. 
    3. Bağlantı durumunun onaylanmış ve sağlama durumunun başarılı olduğundan emin olun. 
    4. Ayrıca, Özel uç nokta kaynağına gidebilir ve aynı özellikleri orada gözden geçirebilir ve sanal ağın kullandığınız bir ile eşleşip eşleşmediğini iki kez kontrol edebilirsiniz.

* Özel DNS bölge kaynağınız olduğundan emin olun. 
    1. Tam adı olan bir Özel DNS Zone kaynağınız olmalıdır: privatelink.vaultcore.azure.net. 
    2. Bu ayarı nasıl ayarlayacağınızı öğrenmek için lütfen aşağıdaki bağlantıya bakın. [Özel DNS bölgeler](../../dns/private-dns-privatednszone.md)
    
* Özel DNS Bölgesini denetleyerek Sanal Ağ ile bağlantılı olmadığından emin olun. Hala genel IP adresini almaya devam ediyorsanız bu sorun olabilir. 
    1. Özel bölge DNS sanal ağa bağlı değilse, sanal ağdan kaynaklanan DNS sorgusu, anahtar kasasının genel IP adresini döndürür. 
    2. Azure portal Özel DNS bölgesi kaynağına gidin ve sanal ağ bağlantıları seçeneğine tıklayın. 
    4. Anahtar kasasına çağrı gerçekleştirecek sanal ağın listelenmesi gerekir. 
    5. Bu yoksa, ekleyin. 
    6. Ayrıntılı adımlar için, [sanal ağın özel DNS bölgeye bağlanması için](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network) aşağıdaki belgeye bakın

* Özel DNS bölgesinde Anahtar Kasası için bir kaydın eksik olmadığından emin olun. 
    1. Özel DNS bölgesi sayfasına gidin. 
    2. Genel Bakış'a tıklayıp anahtar kasanızın basit adının (örn. fabrikam) bulunduğu bir A kaydı olup olmadığını denetleyin. Herhangi bir son ek belirtmeyin.
    3. Yazımı denetleyin ve A kaydını düzeltin veya yeni bir kayıt oluşturun. TTL değeri olarak 3600 (1 saat) belirtebilirsiniz. 
    4. Doğru özel IP adresini belirttiğinizden emin olun. 
    
* Bir kaydın doğru IP adresine sahip olduğundan emin olun. 
    1. Azure portal ' de özel uç nokta kaynağını açarak IP adresini doğrulayabilirsiniz.
    2. Azure portalında Microsoft.Network/privateEndpoints kaynağına (Key Vault kaynağına değil) gidin
    3. Genel Bakış sayfasında ağ arabirimi ' ne bakın ve bu bağlantıya tıklayın. 
    4. Bağlantı, özel IP adresi özelliğini içeren NIC kaynağına genel bakışı gösterir. 
    5. Bunun, A kaydında belirtilen doğru IP adresi olduğunu doğrulayın.

## <a name="limitations-and-design-considerations"></a>Sınırlamalar ve tasarım konuları

> [!NOTE]
> Abonelik başına etkin olan özel uç noktaları olan anahtar kasalarının sayısı, ayarlanabilir bir kısıtlamadır. Aşağıda gösterilen sınır varsayılan sınırdır. Hizmetiniz için sınır artışı istemek istiyorsanız lütfen adresine bir e-posta gönderin akv-privatelink@microsoft.com . Bu istekleri durum temelinde onaylayacağız.

**Fiyatlandırma**: fiyatlandırma bilgileri için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).

**Sınırlamalar**: Azure Key Vault Için özel uç nokta yalnızca Azure genel bölgelerinde kullanılabilir.

**Key Vault başına en fazla özel uç nokta sayısı**: 64.

**Abonelik başına özel uç noktalara sahip anahtar kasalarının varsayılan sayısı**: 400.

Daha fazla bilgi için bkz [. Azure özel bağlantı hizmeti: sınırlamalar](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure özel bağlantısı](../../private-link/private-link-service-overview.md) hakkında daha fazla bilgi edinin
- [Azure Key Vault](overview.md) hakkında daha fazla bilgi edinin
