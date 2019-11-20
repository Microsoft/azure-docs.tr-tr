---
title: Azure sanal ağ eşlemesi oluşturma-farklı dağıtım modelleri-aynı abonelik | Microsoft Docs
description: Aynı Azure aboneliğinde bulunan farklı Azure dağıtım modelleriyle oluşturulan sanal ağlar arasında sanal ağ eşlemesi oluşturmayı öğrenin.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 720351463a9f8d5712c76401f3fbba64c3177e84
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871965"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Sanal ağ eşlemesi oluşturma-farklı dağıtım modelleri, aynı abonelik

Bu öğreticide, farklı dağıtım modelleriyle oluşturulan sanal ağlar arasında bir sanal ağ eşlemesi oluşturmayı öğreneceksiniz. Aynı abonelikte her iki sanal ağ de mevcuttur. İki sanal ağı eşleme, farklı sanal ağlardaki kaynakların aynı bant genişliği ve aynı sanal ağ içinde olmasına karşın gecikme süresiyle birbirleriyle iletişim kurmasını sağlar. [Sanal ağ eşlemesi](virtual-network-peering-overview.md)hakkında daha fazla bilgi edinin.

Sanal ağ eşlemesi oluşturma adımları, sanal ağların aynı veya farklı, aboneliklerde ve sanal ağların hangi [Azure dağıtım modelinde](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturulduğuna bağlı olarak farklıdır. Aşağıdaki tablodan senaryoya tıklayarak diğer senaryolarda bir sanal ağ eşlemesi oluşturmayı öğrenin:

|Azure dağıtım modeli  | Azure aboneliği  |
|--------- |---------|
|[Her iki Kaynak Yöneticisi](tutorial-connect-virtual-networks-portal.md) |Naklettiğiniz|
|[Her iki Kaynak Yöneticisi](create-peering-different-subscriptions.md) |Farklı|
|[Bir Kaynak Yöneticisi, klasik bir](create-peering-different-deployment-models-subscriptions.md) |Farklı|

Klasik dağıtım modeli aracılığıyla dağıtılan iki sanal ağ arasında sanal ağ eşlemesi oluşturulamaz. Hem klasik dağıtım modeliyle oluşturulan sanal ağlara bağlanmanız gerekiyorsa, sanal ağları bağlamak için bir Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanabilirsiniz.

Bu öğretici, aynı bölgedeki sanal ağları eşler. Ayrıca, sanal ağları farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region)de eşler. Sanal ağları eşlemeden önce [eşleme gereksinimlerini ve kısıtlamalarını](virtual-network-manage-peering.md#requirements-and-constraints) öğrenmeniz önerilir.

Bir sanal ağ eşlemesi oluşturmak için Azure portal, Azure [komut satırı arabirimini](#cli) (CLI), Azure [PowerShell](#powershell)'i veya bir Azure Resource Manager şablonunu kullanabilirsiniz. Seçtiğiniz aracı kullanarak bir sanal ağ eşlemesi oluşturma adımlarına doğrudan gitmek için önceki araç bağlantılarından herhangi birine tıklayın.

## <a name="create-peering---azure-portal"></a>Eşleme oluşturma-Azure portal

1. [Azure Portal](https://portal.azure.com) oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#requirements-and-constraints).
2. **+ Yeni**' ye, **ağ**' a ve ardından **sanal ağ ' a**tıklayın.
3. **Sanal ağ oluştur** dikey penceresinde, aşağıdaki ayarlara ait değerleri girin veya seçin ve ardından **Oluştur**' a tıklayın:
    - **Ad**: *myVnet1*
    - **Adres alanı**: *10.0.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.0.0.0/24*
    - **Abonelik**: Aboneliğinizi seçme
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve *myresourcegroup* girin
    - **Konum**: *Doğu ABD*
4. **+ Yeni** öğesine tıklayın. Market 'te **Ara** kutusuna *sanal ağ*yazın. Arama sonuçlarında göründüğünde **sanal ağ ' a** tıklayın.
5. **Sanal ağ** dikey penceresinde, **bir dağıtım modeli seçin** kutusunda **Klasik** ' i seçin ve ardından **Oluştur**' a tıklayın.
6. **Sanal ağ oluştur** dikey penceresinde, aşağıdaki ayarlara ait değerleri girin veya seçin ve ardından **Oluştur**' a tıklayın:
    - **Ad**: *myVnet2*
    - **Adres alanı**: *10.1.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.1.0.0/24*
    - **Abonelik**: Aboneliğinizi seçme
    - **Kaynak grubu**: **Mevcut olanı kullan** ' ı seçin ve *myresourcegroup* öğesini seçin
    - **Konum**: *Doğu ABD*
7. Portalın üst kısmındaki **kaynakları ara** kutusuna *myresourcegroup*yazın. Arama sonuçlarında göründüğünde **Myresourcegroup** öğesine tıklayın. **Myresourcegroup** kaynak grubu için bir dikey pencere görünür. Kaynak grubu, önceki adımlarda oluşturulan iki sanal ağı barındırır.
8. **MyVNet1**tıklayın.
9. Görüntülenen **myVnet1** dikey penceresinde, dikey pencerenin sol tarafındaki Seçenekler ' **e tıklayın.**
10. Görünen **myVnet1-Peerler** dikey penceresinde **+ Ekle** ' ye tıklayın.
11. Görüntülenen **eşleme Ekle** dikey penceresinde, aşağıdaki seçenekleri girin veya seçin ve ardından **Tamam**' a tıklayın:
     - **Ad**: *myVnet1ToMyVnet2*
     - **Sanal ağ dağıtım modeli**:  **Klasik**' i seçin.
     - **Abonelik**: Aboneliğinizi seçme
     - **Sanal ağ**:  **Sanal ağ Seç**' e tıklayın ve ardından **myVnet2**' ye tıklayın.
     - **Sanal ağ erişimine izin ver:** **Etkin** ' in seçildiğinden emin olun.
    Bu öğreticide başka hiçbir ayar kullanılmaz. Tüm eşleme ayarları hakkında daha fazla bilgi edinmek için [sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md#create-a-peering)makalesini okuyun.
12. Önceki adımda **Tamam** ' a tıkladıktan sonra **eşleme Ekle** dikey penceresi kapanır ve **myVnet1-peerler** dikey penceresini yeniden görürsünüz. Birkaç saniye sonra oluşturduğunuz eşleme dikey pencerede görüntülenir. **Bağlantı** , oluşturduğunuz **MYVNET1TOMYVNET2** eşlemenin **eşleme durumu** sütununda listelenir.

    Eşleme artık oluşturulmuştur. Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.
13. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturulmamış olsa da, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
14. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makalenin [kaynakları silme](#delete-portal) bölümünde yer alan adımları izleyin.

## <a name="cli"></a>Eşleme oluşturma-Azure CLı

Azure klasik CLı ve Azure CLı 'yı kullanarak aşağıdaki adımları uygulayın. Azure Cloud Shell adımları yalnızca aşağıdaki adımlardan herhangi birine **deneyin** düğmesini seçerek veya [Klasik CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) yükleyerek ve komutları yerel bilgisayarınızda çalıştırarak çalıştırabilirsiniz.

1. Cloud Shell kullanılıyorsa, Cloud Shell otomatik olarak Azure 'da oturum açtığından 2. adıma atlayın. Bir komut oturumu açın ve `azure login` komutunu kullanarak Azure 'da oturum açın.
2. `azure config mode asm` Komutu girerek CLI 'yı hizmet yönetimi modunda çalıştırın.
3. Sanal ağı (klasik) oluşturmak için aşağıdaki komutu girin:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Aşağıdaki Bash CLı betiğini, klasik CLı değil CLı kullanarak yürütün. Windows bilgisayarda Bash CLı betikleri çalıştırma seçenekleri için bkz. [Windows 'Da Azure CLI 'Yı yüklemeyi](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. CLı kullanılarak farklı dağıtım modelleriyle oluşturulan iki sanal ağ arasında bir sanal ağ eşlemesi oluşturun. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. Abonelik `<subscription id>` Kimliğinizle değiştirin. Abonelik kimliğinizi bilmiyorsanız `az account show` komutunu girin. Çıkışdaki **ID** değeri, abonelik kimliğiniz olur. Değiştirilen betiği CLı oturumunuza yapıştırın ve ardından ' a basın `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Betik yürütüldükten sonra, sanal ağın eşlemesini gözden geçirin (Kaynak Yöneticisi). Aşağıdaki komutu kopyalayın, CLı oturumunuza yapıştırın ve ardından şunu tuşuna basın `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Çıkış, **Peeringstate** sütununda **bağlı** ' yı gösterir.

   Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.
7. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturulmamış olsa da, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
8. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-cli) bölümündeki adımları uygulayın.

## <a name="powershell"></a>Eşleme oluşturma-PowerShell

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) ve [az](https://www.powershellgallery.com/packages/Az/) modules 'ın en son sürümünü yükler. Azure PowerShell'i kullanmaya yeni başladıysanız [Azure PowerShell'e genel bakış](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) sayfasını inceleyin.
2. Bir PowerShell oturumu başlatın.
3. PowerShell 'de `Add-AzureAccount` komutunu girerek Azure 'da oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#requirements-and-constraints).
4. PowerShell ile bir sanal ağ (klasik) oluşturmak için yeni bir oluşturmanız veya var olan bir ağ yapılandırma dosyasını değiştirmeniz gerekir. [Ağ yapılandırma dosyalarını dışarı aktarmayı, güncelleştirmeyi ve içeri aktarmayı](virtual-networks-using-network-configuration-file.md)öğrenin. Dosya, bu öğreticide kullanılan sanal ağ için aşağıdaki **Virtualnetworksite** öğesini içermelidir:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Değiştirilen bir ağ yapılandırma dosyasını içeri aktarmak, aboneliğinizde var olan sanal ağlarda (klasik) değişikliklere neden olabilir. Yalnızca önceki sanal ağı eklendiğinden ve aboneliğinizden var olan sanal ağları değiştirmemenizi veya kaldırmayın.
5. `Connect-AzAccount` Komutu girerek sanal ağı oluşturmak için Azure 'da oturum açın (Kaynak Yöneticisi). Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#requirements-and-constraints).
6. Bir kaynak grubu ve bir sanal ağ (Kaynak Yöneticisi) oluşturun. Betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter`.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Farklı dağıtım modelleriyle oluşturulan iki sanal ağ arasında bir sanal ağ eşlemesi oluşturun. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. Abonelik `<subscription id>` Kimliğinizle değiştirin. Abonelik kimliğinizi bilmiyorsanız, görüntülemek için `Get-AzSubscription` komutunu girin. Döndürülen çıktıda **kimliği** için olan değer abonelik kimliğiniz olur. Betiği yürütmek için, değiştirilen betiği metin düzenleyicinizden kopyalayın, ardından PowerShell oturumunuzu sağ tıklatın ve ardından ' a basın `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Betik yürütüldükten sonra, sanal ağın eşlemesini gözden geçirin (Kaynak Yöneticisi). Aşağıdaki komutu kopyalayın, PowerShell oturumunuza yapıştırın ve ardından şunu tuşuna basın `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Çıkış, **Peeringstate** sütununda **bağlı** ' yı gösterir.

    Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

9. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturulmamış olsa da, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
10. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-powershell) bölümündeki adımları uygulayın.

## <a name="delete"></a>Kaynakları Sil

Bu öğreticiyi tamamladığınızda, öğreticide oluşturduğunuz kaynakları silmek isteyebilirsiniz, bu sayede kullanım ücretlerine tabi kalmazsınız. Kaynak grubunun silinmesi, kaynak grubundaki tüm kaynakları da siler.

### <a name="delete-portal"></a>Azure portal

1. Portal arama kutusuna **Myresourcegroup**yazın. Arama sonuçlarında **Myresourcegroup**' a tıklayın.
2. **Myresourcegroup** dikey penceresinde **Sil** simgesine tıklayın.
3. Silmeyi onaylamak için, **kaynak grubu adını yazın** kutusuna **myresourcegroup**yazın ve ardından **Sil**' e tıklayın.

### <a name="delete-cli"></a>Azure CLI

1. Aşağıdaki komutla sanal ağı (Kaynak Yöneticisi) silmek için Azure CLı 'yi kullanın:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Aşağıdaki komutlarla sanal ağı (klasik) silmek için klasik CLı 'yi kullanın:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Sanal ağı (Kaynak Yöneticisi) silmek için aşağıdaki komutu girin:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Sanal ağı (klasik) PowerShell ile silmek için, var olan bir ağ yapılandırma dosyasını değiştirmeniz gerekir. [Ağ yapılandırma dosyalarını dışarı aktarmayı, güncelleştirmeyi ve içeri aktarmayı](virtual-networks-using-network-configuration-file.md)öğrenin. Bu öğreticide kullanılan sanal ağ için aşağıdaki VirtualNetworkSite öğesini kaldırın:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Değiştirilen bir ağ yapılandırma dosyasını içeri aktarmak, aboneliğinizde var olan sanal ağlarda (klasik) değişikliklere neden olabilir. Yalnızca önceki sanal ağı kaldırmanız ve aboneliğinizden diğer mevcut sanal ağları değiştirmemenizi veya kaldırmayın olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- Üretim kullanımı için bir sanal ağ eşlemesi oluşturmadan önce önemli [sanal ağ eşleme kısıtlamalarını ve davranışları](virtual-network-manage-peering.md#requirements-and-constraints) hakkında ayrıntılı bilgi edinin.
- Tüm [sanal ağ eşleme ayarları](virtual-network-manage-peering.md#create-a-peering)hakkında bilgi edinin.
- Sanal ağ eşlemesi ile [hub ve bağlı ağ topolojisi oluşturmayı](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) öğrenin.
