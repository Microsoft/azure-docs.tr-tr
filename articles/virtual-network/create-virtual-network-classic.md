---
title: Birden çok alt ağ içeren bir Azure sanal ağı (klasik) oluşturun | Microsoft Docs
description: Azure 'da birden çok alt ağa sahip bir sanal ağ (klasik) oluşturmayı öğrenin.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059003"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Birden çok alt ağ içeren bir sanal ağ (klasik) oluşturma

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki [farklı dağıtım modeli](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vardır: Kaynak Yöneticisi ve klasik. Bu makale klasik dağıtım modelini incelemektedir. Microsoft, [Kaynak Yöneticisi](quick-create-portal.md) dağıtım modeli aracılığıyla birçok yeni sanal ağın oluşturulmasını önerir.

Bu öğreticide, ayrı genel ve özel alt ağlara sahip olan temel bir Azure sanal ağı (klasik) oluşturmayı öğrenin. Sanal makineler ve bulut hizmetleri gibi bir alt ağda Azure kaynakları oluşturabilirsiniz. Sanal ağlarda oluşturulan kaynaklar (klasik) birbirleriyle ve bir sanal ağa bağlı diğer ağlardaki kaynaklarla iletişim kurabilir.

Tüm [sanal ağ](manage-virtual-network.md) ve [alt ağ](virtual-network-manage-subnet.md) ayarları hakkında daha fazla bilgi edinin.

> [!WARNING]
> Bir [abonelik devre dışı bırakıldığında,](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit)sanal ağlar (klasik) Azure tarafından hemen silinir. Sanal ağlar (klasik), kaynakların sanal ağda mevcut olup olmamasına bakılmaksızın silinir. Daha sonra aboneliği yeniden etkinleştirirseniz, sanal ağda var olan kaynakların yeniden oluşturulması gerekir.

[Azure Portal](#portal), [Azure komut SATıRı arabirimi (CLI) 1,0](#azure-cli)veya [PowerShell](#powershell)kullanarak bir sanal ağ (klasik) oluşturabilirsiniz.

## <a name="portal"></a>Portal

1. Bir Internet tarayıcısında [Azure Portal](https://portal.azure.com)gidin. [Azure hesabınızı](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)kullanarak oturum açın. Azure hesabınız yoksa, oturum açabileceğiniz bir [ücretsiz deneme sürümü](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Portalda **kaynak oluştur ' a** tıklayın.
3. Görüntülenen **Yeni** bölmenin en üstündeki **Market** 'te bulunan *sanal ağı* girin. Arama sonuçlarında göründüğünde **sanal ağ ' a** tıklayın.
4. Görüntülenen **sanal ağ** bölmesindeki **bir dağıtım modeli seçin** kutusunda **Klasik** ' i seçin ve ardından **Oluştur**' a tıklayın. 
5. **Sanal ağ oluştur (klasik)** bölmesinde aşağıdaki değerleri girin ve **Oluştur**' a tıklayın:

    |Ayar|Value|
    |---|---|
    |Name|myVnet|
    |Adres alanı|10.0.0.0/16|
    |Alt ağ adı|Genel|
    |Alt ağ adres aralığı|10.0.0.0/24|
    |Resource group|**Yeni oluştur** ' u seçili bırakın ve **myresourcegroup**girin.|
    |Abonelik ve konum|Aboneliğinizi ve konumunuzu seçin.

    Azure 'u yeni kullanıyorsanız [kaynak grupları](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [abonelikler](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)ve [konumlar](https://azure.microsoft.com/regions) ( *bölge*olarak da bilinir) hakkında daha fazla bilgi edinin.
4. Portalda, bir sanal ağ oluşturduğunuzda yalnızca bir alt ağ oluşturabilirsiniz. Bu öğreticide, sanal ağı oluşturduktan sonra ikinci bir alt ağ oluşturursunuz. Daha sonra **genel** alt ağda Internet erişimli kaynaklar oluşturabilirsiniz. Ayrıca, **özel** alt ağda Internet 'ten erişilemeyen kaynaklar da oluşturabilirsiniz. İkinci alt ağı oluşturmak için, sayfanın üst kısmındaki **kaynakları ara** kutusuna **myvnet** girin. Arama sonuçlarında göründüğünde **Myvnet** ' e tıklayın.
5. Görüntülenen **sanal ağ oluştur (klasik)** bölmesindeki **alt ağlar** ( **Ayarlar** bölümünde) seçeneğine tıklayın.
6. Açılan **Myvnet-alt ağları** bölmesinde **+ Ekle** ' ye tıklayın.
7. **Alt ağ ekle** bölmesine **ad** için **özel** ' i girin. **Adres aralığı**için **10.0.1.0/24** girin.  **Tamam**'ı tıklatın.
8. **Myvnet-alt ağları** bölmesinde, oluşturduğunuz **ortak** ve **özel** alt ağları görebilirsiniz.
9. **Isteğe bağlı**: Bu öğreticiyi tamamladığınızda, kullanım ücretleri ödemeniz için oluşturduğunuz kaynakları silmek isteyebilirsiniz:
    - **Myvnet** bölmesinde **Genel Bakış ' a** tıklayın.
    - **Myvnet** bölmesindeki **Sil** simgesine tıklayın.
    - Silme işlemini onaylamak için **sanal ağı Sil** kutusunda **Evet** ' e tıklayın.

## <a name="azure-cli"></a>Azure CLI

1. [Azure CLI 'yı yükleyebilir ve yapılandırabilir](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ya da Azure Cloud Shell içinde CLI kullanabilirsiniz. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. CLı komutları hakkında yardım almak için, yazın `azure <command> --help`. 
2. Bir CLı oturumunda, aşağıdaki komutla Azure 'da oturum açın. Aşağıdaki kutudan **dene** ' ye tıklarsanız bir Cloud Shell açılır. Aşağıdaki komutu girmeden Azure aboneliğinizde oturum açabilirsiniz:

    ```azurecli-interactive
    azure login
    ```

3. CLı 'nın hizmet yönetimi modunda olduğundan emin olmak için aşağıdaki komutu girin:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Özel bir alt ağa sahip bir sanal ağ oluşturun:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Sanal ağ içinde ortak bir alt ağ oluşturun:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Sanal ağı ve alt ağları gözden geçirin:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Isteğe bağlı**: Bu öğreticiyi tamamladığınızda oluşturduğunuz kaynakları silmek isteyebilirsiniz, bu sayede kullanım ücretleri ödemeniz gerekmez:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> CLı kullanarak bir sanal ağ (klasik) oluşturmak için bir kaynak grubu belirtemezsiniz ancak Azure, sanal ağı *varsayılan ağ*adlı bir kaynak grubunda oluşturur.

## <a name="powershell"></a>PowerShell

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modülünün en son sürümünü yükler. Azure PowerShell'i kullanmaya yeni başladıysanız [Azure PowerShell'e genel bakış](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) sayfasını inceleyin.
2. Bir PowerShell oturumu başlatın.
3. PowerShell'de `Add-AzureAccount` komutunu girerek Azure oturumu açın.
4. Aşağıdaki yolu ve dosya adını uygun şekilde değiştirin ve var olan ağ yapılandırma dosyanızı dışarı aktarın:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Ortak ve özel alt ağlarla bir sanal ağ oluşturmak için, ağ yapılandırma dosyasına aşağıdaki **Virtualnetworksite** öğesini eklemek için herhangi bir metin düzenleyicisini kullanın.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Tam [ağ yapılandırma dosyası şemasını](https://msdn.microsoft.com/library/azure/jj157100.aspx)gözden geçirin.

6. Ağ yapılandırma dosyasını içeri aktarın:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Değiştirilen bir ağ yapılandırma dosyasını içeri aktarmak, aboneliğinizde var olan sanal ağlarda (klasik) değişikliklere neden olabilir. Yalnızca önceki sanal ağı eklendiğinden ve aboneliğinizden var olan sanal ağları değiştirmemenizi veya kaldırmayın. 

7. Sanal ağı ve alt ağları gözden geçirin:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Isteğe bağlı**: Bu öğreticiyi tamamladığınızda oluşturduğunuz kaynakları silmek isteyebilirsiniz, bu sayede kullanım ücretleri ödemeniz gerekmez. Sanal ağı silmek için, adım 5 ' te eklediğiniz **Virtualnetworksite** öğesini kaldırmak için yeniden 4-6 adımlarını tekrar doldurun.
 
> [!NOTE]
> PowerShell kullanarak bir sanal ağ (klasik) oluşturmak için bir kaynak grubu belirtemezsiniz ancak Azure, sanal ağı *varsayılan ağ*adlı bir kaynak grubunda oluşturur.

---

## <a name="next-steps"></a>Sonraki adımlar

- Tüm sanal ağ ve alt ağ ayarları hakkında bilgi edinmek için bkz. [sanal ağları yönetme](manage-virtual-network.md) ve [sanal ağ alt ağlarını yönetme](virtual-network-manage-subnet.md). Farklı gereksinimleri karşılamak için bir üretim ortamında sanal ağları ve alt ağları kullanmaya yönelik çeşitli seçenekleriniz vardır.
- Bir [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi oluşturun ve var olan bir sanal ağa bağlayın.
- Aynı Azure konumunda iki sanal ağı bağlamak için sanal ağlar arasında bir [sanal ağ eşlemesi](create-peering-different-deployment-models.md) oluşturun. Sanal ağ (Kaynak Yöneticisi) sanal ağa (klasik) eşler, ancak iki sanal ağ (klasik) arasında bir eşleme oluşturamazsınız.
- [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bağlantı hattını kullanarak sanal ağı şirket içi ağa bağlayın.
