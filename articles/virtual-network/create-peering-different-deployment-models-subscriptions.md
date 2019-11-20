---
title: Azure sanal ağ eşlemesi oluşturma-farklı dağıtım modelleri-farklı abonelikler
titlesuffix: Azure Virtual Network
description: Farklı Azure aboneliklerinde bulunan farklı Azure dağıtım modelleriyle oluşturulan sanal ağlar arasında sanal ağ eşlemesi oluşturmayı öğrenin.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: fa647da6764ca61679aade2acc2849b474912278
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871980"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Sanal ağ eşlemesi oluşturma-farklı dağıtım modelleri ve abonelikler

Bu öğreticide, farklı dağıtım modelleriyle oluşturulan sanal ağlar arasında bir sanal ağ eşlemesi oluşturmayı öğreneceksiniz. Sanal ağlar farklı aboneliklerde mevcuttur. İki sanal ağı eşleme, farklı sanal ağlardaki kaynakların aynı bant genişliği ve aynı sanal ağ içinde olmasına karşın gecikme süresiyle birbirleriyle iletişim kurmasını sağlar. [Sanal ağ eşlemesi](virtual-network-peering-overview.md)hakkında daha fazla bilgi edinin.

Sanal ağ eşlemesi oluşturma adımları, sanal ağların aynı veya farklı, aboneliklerde ve sanal ağların hangi [Azure dağıtım modelinde](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturulduğuna bağlı olarak farklıdır. Aşağıdaki tablodan senaryoya tıklayarak diğer senaryolarda bir sanal ağ eşlemesi oluşturmayı öğrenin:

|Azure dağıtım modeli  | Azure aboneliği  |
|--------- |---------|
|[Her iki Kaynak Yöneticisi](tutorial-connect-virtual-networks-portal.md) |Naklettiğiniz|
|[Her iki Kaynak Yöneticisi](create-peering-different-subscriptions.md) |Farklı|
|[Bir Kaynak Yöneticisi, klasik bir](create-peering-different-deployment-models.md) |Naklettiğiniz|

Klasik dağıtım modeli aracılığıyla dağıtılan iki sanal ağ arasında sanal ağ eşlemesi oluşturulamaz. Bu öğretici, aynı bölgede bulunan sanal ağları kullanır. Bu öğretici, aynı bölgedeki sanal ağları eşler. Ayrıca, sanal ağları farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region)de eşler. Sanal ağları eşlemeden önce [eşleme gereksinimlerini ve kısıtlamalarını](virtual-network-manage-peering.md#requirements-and-constraints) öğrenmeniz önerilir.

Farklı aboneliklerde bulunan sanal ağlar arasında bir sanal ağ eşlemesi oluştururken, her ikisinin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir. Zaten bir Azure Active Directory kiracınız yoksa hızlı [bir şekilde bir tane oluşturabilirsiniz](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)kullanarak farklı aboneliklerdeki ve farklı Azure Active Directory kiracılardaki sanal ağları bağlayabilirsiniz.

Bir sanal ağ eşlemesi oluşturmak için [Azure Portal](#portal), Azure [komut satırı arabirimi](#cli) 'ni (CLI) veya Azure [PowerShell](#powershell) 'i kullanabilirsiniz. Seçtiğiniz aracı kullanarak bir sanal ağ eşlemesi oluşturma adımlarına doğrudan gitmek için önceki araç bağlantılarından herhangi birine tıklayın.

## <a name="portal"></a>Eşleme oluşturma-Azure portal

Bu öğretici her abonelik için farklı hesaplar kullanır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, portaldan oturum açmak için adımları atlayabilir ve sanal ağlara başka bir Kullanıcı izinleri atama adımlarını atlayabilirsiniz.

1. [Azure Portal](https://portal.azure.com) UserA olarak oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
2. **+ Yeni**' ye, **ağ**' a ve ardından **sanal ağ ' a**tıklayın.
3. **Sanal ağ oluştur** dikey penceresinde, aşağıdaki ayarlara ait değerleri girin veya seçin ve ardından **Oluştur**' a tıklayın:
    - **Ad**: *myvneta*
    - **Adres alanı**: *10.0.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.0.0.0/24*
    - **Abonelik**: Abonelik A ' yı seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve *Myresourcegroupa* girin
    - **Konum**: *Doğu ABD*
4. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvneta*yazın. Arama sonuçlarında göründüğünde **Myvneta** ' ya tıklayın. **Myvneta** sanal ağı için bir dikey pencere görünür.
5. Görüntülenen **Myvneta** dikey penceresinde, dikey pencerenin sol tarafındaki seçenekler listesinden **ERIŞIM denetimi (IAM)** öğesine tıklayın.
6. Görüntülenen **Myvneta-Access Control (IAM)** dikey penceresinde **+ rol ataması Ekle**' ye tıklayın.
7. Görüntülenen **rol ataması Ekle** dikey penceresinde, **rol** kutusunda **ağ katılımcısı** ' nı seçin.
8. **Seç** kutusunda, UserB ' yi seçin veya aramak Için UserB 'nin e-posta adresini yazın. Gösterilen kullanıcı listesi, eşlemesini ayarladığınız sanal ağla aynı Azure Active Directory kiracıya ait. Listede göründüğünde UserB ' ye tıklayın.
9. **Kaydet**’e tıklayın.
10. Portaldan UserA olarak oturum açın, sonra UserB olarak oturum açın.
11. **+ Yeni**' ye tıklayın, **Market 'Te ara** kutusuna *sanal ağ* yazın ve arama sonuçlarında **sanal ağ ' a** tıklayın.
12. Görüntülenen **sanal ağ** dikey penceresinde, **bir dağıtım modeli seçin** kutusunda **Klasik** ' i seçin ve ardından **Oluştur**' a tıklayın.
13. Görüntülenen sanal ağ oluştur (klasik) kutusunda aşağıdaki değerleri girin:

    - **Ad**: *myvnetb*
    - **Adres alanı**: *10.1.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.1.0.0/24*
    - **Abonelik**: B aboneliğini seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve *Myresourcegroupb* girin
    - **Konum**: *Doğu ABD*

14. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvnetb*yazın. Arama sonuçlarında göründüğünde **Myvnetb** ' ye tıklayın. **Myvnetb** sanal ağı için bir dikey pencere görüntülenir.
15. Görüntülenen **Myvnetb** dikey penceresinde dikey pencerenin sol tarafındaki Seçenekler ' in dikey listesinden **Özellikler** ' e tıklayın. Daha sonraki bir adımda kullanılan **kaynak kimliğini**kopyalayın. Kaynak KIMLIĞI aşağıdaki örneğe benzer:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Adım 8 ' de **KullanıcıA** 'Yı girerek myVnetB için 5-9 adımlarını izleyin.
17. Portaldan UserB olarak oturum açın ve UserA olarak oturum açın.
18. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvneta*yazın. Arama sonuçlarında göründüğünde **Myvneta** ' ya tıklayın. **Myvnet** sanal ağı için bir dikey pencere görünür.
19. **Myvneta**' ya tıklayın.
20. Görüntülenen **Myvneta** dikey penceresinde, dikey pencerenin sol tarafındaki Seçenekler ' **e tıklayın.**
21. Görünen **Myvneta-Peerler** dikey penceresinde **+ Ekle** ' ye tıklayın.
22. Görüntülenen **eşleme Ekle** dikey penceresinde, aşağıdaki seçenekleri girin veya seçin ve ardından **Tamam**' a tıklayın:
     - **Ad**: *myvnetatomyvnetb*
     - **Sanal ağ dağıtım modeli**:  **Klasik**' i seçin.
     - **Kaynak kimliğimi biliyorum**: Bu kutuyu işaretleyin.
     - **Kaynak kimliği**: 15. adımdaki myVnetB kaynak KIMLIĞINI girin.
     - **Sanal ağ erişimine izin ver:** **Etkin** ' in seçildiğinden emin olun.
    Bu öğreticide başka hiçbir ayar kullanılmaz. Tüm eşleme ayarları hakkında daha fazla bilgi edinmek için [sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md#create-a-peering)makalesini okuyun.
23. Önceki adımda **Tamam** ' a tıkladıktan sonra **eşleme Ekle** dikey penceresi kapanır ve **Myvneta-peerler** dikey penceresini yeniden görürsünüz. Birkaç saniye sonra oluşturduğunuz eşleme dikey pencerede görüntülenir. **Bağlantı** , oluşturduğunuz **Myvnetatomyvnetb** eşlemesi için **eşleme durumu** sütununda listelenir. Eşleme artık oluşturulmuştur. Sanal ağın (klasik) sanal ağa (Kaynak Yöneticisi) eşdüzey olması gerekmez.

    Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

24. **Isteğe bağlı**: Sanal makinelerin oluşturulması bu öğreticide kapsanmadığından, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
25. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makalenin [kaynakları silme](#delete-portal) bölümünde yer alan adımları izleyin.

## <a name="cli"></a>Eşleme oluşturma-Azure CLı

Bu öğretici her abonelik için farklı hesaplar kullanır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, Azure oturumunu kapatmak için gereken adımları atlayabilir ve Kullanıcı rolü atamaları oluşturan komut dosyası satırlarını kaldırabilirsiniz. Aşağıdaki UserA@azure.com betiklerin tümünde ve UserB@azure.com KullanıcıA ve KullanıcıB için kullandığınız kullanıcı adlarıyla değiştirin. Azure klasik CLı ve Azure CLı 'yı kullanarak aşağıdaki adımları uygulayın. Azure Cloud Shell adımları yalnızca aşağıdaki adımlardan herhangi birine **deneyin** düğmesini seçerek veya [Klasik CLI](/cli/azure/install-classic-cli) ve [CLI](/cli/azure/install-azure-cli) yükleyerek ve komutları yerel bilgisayarınızda çalıştırarak çalıştırabilirsiniz.

1. Cloud Shell kullanılıyorsa, Cloud Shell otomatik olarak Azure 'da oturum açtığından 2. adıma atlayın. Bir komut oturumu açın ve `azure login` komutunu kullanarak Azure 'da oturum açın.
2. `azure config mode asm` Komutu girerek klasik CLI 'yı hizmet yönetimi modunda çalıştırın.
3. Sanal ağı (klasik) oluşturmak için aşağıdaki klasik CLı komutunu girin:

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. Kalan adımların Azure CLı (klasik CLı değil) ile bir bash kabuğu kullanılarak tamamlanması gerekir.
5. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. Abonelik `<SubscriptionB-Id>` Kimliğinizle değiştirin. Abonelik kimliğinizi bilmiyorsanız `az account show` komutunu girin. Çıkışdaki **ID** değeri, abonelik kimliğiniz olur. Değiştirilen betiği kopyalayın, CLı oturumunuza yapıştırın ve ardından ' a basın `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Adım 4 ' te sanal ağ (klasik) oluşturduğunuzda Azure, *varsayılan ağ* kaynak grubundaki sanal ağı oluşturmuştur.
6. Azure 'dan Kullanıcı oturumunu açın ve CLı 'de UserA olarak oturum açın.
7. Bir kaynak grubu ve bir sanal ağ (Kaynak Yöneticisi) oluşturun. Aşağıdaki betiği kopyalayın, CLı oturumunuza yapıştırın ve ardından ' a basın `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Farklı dağıtım modelleriyle oluşturulan iki sanal ağ arasında bir sanal ağ eşlemesi oluşturun. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. Abonelik `<SubscriptionB-id>` Kimliğinizle değiştirin. Abonelik kimliğinizi bilmiyorsanız `az account show` komutunu girin. Çıkışdaki **ID** değeri, abonelik kimliğiniz olur. Azure, 4. adımda oluşturduğunuz sanal ağı (klasik) *varsayılan ağ*adlı bir kaynak grubunda oluşturdu. Değiştirilen betiği CLı oturumunuza yapıştırın ve ardından ' a basın `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Betik yürütüldükten sonra, sanal ağın eşlemesini gözden geçirin (Kaynak Yöneticisi). Aşağıdaki betiği kopyalayın ve ardından CLı oturumunuza yapıştırın:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Çıkış, **Peeringstate** sütununda **bağlı** ' yı gösterir.

    Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

10. **Isteğe bağlı**: Sanal makinelerin oluşturulması bu öğreticide kapsanmadığından, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
11. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-cli) bölümündeki adımları uygulayın.

## <a name="powershell"></a>Eşleme oluşturma-PowerShell

Bu öğretici her abonelik için farklı hesaplar kullanır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, Azure oturumunu kapatmak için gereken adımları atlayabilir ve Kullanıcı rolü atamaları oluşturan komut dosyası satırlarını kaldırabilirsiniz. Aşağıdaki UserA@azure.com betiklerin tümünde ve UserB@azure.com KullanıcıA ve KullanıcıB için kullandığınız kullanıcı adlarıyla değiştirin. 

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) ve [az](https://www.powershellgallery.com/packages/Az) modules 'ın en son sürümünü yükler. Azure PowerShell'i kullanmaya yeni başladıysanız [Azure PowerShell'e genel bakış](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) sayfasını inceleyin.
2. Bir PowerShell oturumu başlatın.
3. PowerShell 'de, `Add-AzureAccount` komutunu girerek UserB 'nin aboneliğinde UserB olarak oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
4. PowerShell ile bir sanal ağ (klasik) oluşturmak için yeni bir oluşturmanız veya var olan bir ağ yapılandırma dosyasını değiştirmeniz gerekir. [Ağ yapılandırma dosyalarını dışarı aktarmayı, güncelleştirmeyi ve içeri aktarmayı](virtual-networks-using-network-configuration-file.md)öğrenin. Dosya, bu öğreticide kullanılan sanal ağ için aşağıdaki **Virtualnetworksite** öğesini içermelidir:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. `Connect-AzAccount` Komutu girerek Kaynak Yöneticisi komutlarını kullanmak için UserB 'nin aboneliğine oturum açın.
6. KullanıcıA izinlerini sanal ağ B 'ye atayın. aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın ve b aboneliğinin kimliğiyle değiştirin `<SubscriptionB-id>` . Abonelik kimliğini bilmiyorsanız, görüntülemek için `Get-AzSubscription` komutunu girin. Döndürülen çıktıda **kimliği** için olan değer abonelik kimliğiniz olur. Azure, 4. adımda oluşturduğunuz sanal ağı (klasik) *varsayılan ağ*adlı bir kaynak grubunda oluşturdu. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter`.
    
    ```powershell 
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Azure oturumunu Kullanıcı oturumu açın ve `Connect-AzAccount` komutu girerek UserA 'nın aboneliğine oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
8. Aşağıdaki betiği kopyalayıp PowerShell 'e yapıştırarak ve ardından şunu tuşlarına basarak `Enter`sanal ağı (Kaynak Yöneticisi) oluşturun:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. MyVnetA öğesine UserB izinleri atama. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın ve abonelik kimliği ile değiştirin `<SubscriptionA-Id>` . Abonelik kimliğini bilmiyorsanız, görüntülemek için `Get-AzSubscription` komutunu girin. Döndürülen çıktıda **kimliği** için olan değer abonelik kimliğiniz olur. Betiğin değiştirilen sürümünü PowerShell 'e yapıştırın ve ardından yürütmek için ' a basın `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın ve B aboneliğinin kimliği ile değiştirin `<SubscriptionB-id>` . MyVnetA 'yı myVNetB 'e bağlamak için, değiştirilen betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter`.

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Aşağıdaki betiği kopyalayarak, PowerShell 'e yapıştırarak ve tuşlarına basarak `Enter`myVnetA öğesinin eşleme durumunu görüntüleyin.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Durum **bağlı**. MyVnetB 'den myVnetA eşlemesini ayarladıktan sonra **bağlı** olarak değişir.

    Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

12. **Isteğe bağlı**: Sanal makinelerin oluşturulması bu öğreticide kapsanmadığından, bağlantıyı doğrulamak için her bir sanal ağda bir sanal makine oluşturabilir ve bir sanal makineden diğerine bağlanabilirsiniz.
13. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-powershell) bölümündeki adımları uygulayın.

## <a name="delete"></a>Kaynakları Sil
Bu öğreticiyi tamamladığınızda, öğreticide oluşturduğunuz kaynakları silmek isteyebilirsiniz, bu sayede kullanım ücretlerine tabi kalmazsınız. Kaynak grubunun silinmesi, kaynak grubundaki tüm kaynakları da siler.

### <a name="delete-portal"></a>Azure portal

1. Portal arama kutusuna **Myresourcegroupa**yazın. Arama sonuçlarında **Myresourcegroupa**' ya tıklayın.
2. **Myresourcegroupa** dikey penceresinde **Sil** simgesine tıklayın.
3. Silme işlemini onaylamak için, **kaynak grubu adını yazın** kutusuna **Myresourcegroupa**girin ve ardından **Sil**' e tıklayın.
4. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvnetb*yazın. Arama sonuçlarında göründüğünde **Myvnetb** ' ye tıklayın. **Myvnetb** sanal ağı için bir dikey pencere görüntülenir.
5. **Myvnetb** dikey penceresinde **Sil**' e tıklayın.
6. Silme işlemini onaylamak için **sanal ağı Sil** kutusunda **Evet** ' e tıklayın.

### <a name="delete-cli"></a>Azure CLI

1. Aşağıdaki komutla sanal ağı (Kaynak Yöneticisi) silmek için CLı kullanarak Azure 'da oturum açın:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Aşağıdaki komutlarla sanal ağı (klasik) silmek için klasik CLı kullanarak Azure 'da oturum açın:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. PowerShell komut isteminde, sanal ağı (Kaynak Yöneticisi) silmek için aşağıdaki komutu girin:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Sanal ağı (klasik) PowerShell ile silmek için, var olan bir ağ yapılandırma dosyasını değiştirmeniz gerekir. [Ağ yapılandırma dosyalarını dışarı aktarmayı, güncelleştirmeyi ve içeri aktarmayı](virtual-networks-using-network-configuration-file.md)öğrenin. Bu öğreticide kullanılan sanal ağ için aşağıdaki VirtualNetworkSite öğesini kaldırın:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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