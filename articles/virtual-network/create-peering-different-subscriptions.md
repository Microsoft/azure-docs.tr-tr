---
title: VNet eşlemesi oluşturma-farklı abonelikler
titlesuffix: Azure Virtual Network
description: Aynı veya farklı Azure Active Directory kiracısında farklı Azure aboneliklerinde bulunan Kaynak Yöneticisi ile oluşturulan sanal ağlar arasında sanal ağ eşlemesi oluşturmayı öğrenin.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518254"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Sanal ağ eşlemesi oluşturma-Kaynak Yöneticisi, farklı abonelikler ve Azure Active Directory kiracılar

Bu öğreticide, Kaynak Yöneticisi ile oluşturulan sanal ağlar arasında bir sanal ağ eşlemesi oluşturmayı öğreneceksiniz. Sanal ağlar, farklı Azure Active Directory (Azure AD) kiracılarına ait olabilecek farklı aboneliklerde mevcuttur. İki sanal ağı eşleme, farklı sanal ağlardaki kaynakların aynı bant genişliği ve aynı sanal ağ içinde olmasına karşın gecikme süresiyle birbirleriyle iletişim kurmasını sağlar. [Sanal ağ eşlemesi](virtual-network-peering-overview.md)hakkında daha fazla bilgi edinin.

Sanal ağ eşlemesi oluşturma adımları, sanal ağların aynı veya farklı, aboneliklerde ve sanal ağların hangi [Azure dağıtım modelinde](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturulduğuna bağlı olarak farklıdır. Aşağıdaki tablodan gelen senaryoyu seçerek diğer senaryolarda bir sanal ağ eşlemesi oluşturmayı öğrenin:

|Azure dağıtım modeli  | Azure aboneliği  |
|--------- |---------|
|[Her ikisi de Resource Manager](tutorial-connect-virtual-networks-portal.md) |Aynı|
|[Biri Resource Manager, diğeri klasik](create-peering-different-deployment-models.md) |Aynı|
|[Biri Resource Manager, diğeri klasik](create-peering-different-deployment-models-subscriptions.md) |Farklı|

Klasik dağıtım modeli aracılığıyla dağıtılan iki sanal ağ arasında sanal ağ eşlemesi oluşturulamaz. Hem klasik dağıtım modeliyle oluşturulan sanal ağlara bağlanmanız gerekiyorsa, sanal ağları bağlamak için bir Azure [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanabilirsiniz.

Bu öğretici, aynı bölgedeki sanal ağları eşler. Ayrıca, sanal ağları farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region)de eşler. Sanal ağları eşlemeden önce [eşleme gereksinimlerini ve kısıtlamalarını](virtual-network-manage-peering.md#requirements-and-constraints) öğrenmeniz önerilir.

Bir sanal ağ eşlemesi oluşturmak için [Azure Portal](#portal), Azure [komut satırı arabirimini](#cli) (CLI), Azure [PowerShell](#powershell)'i veya bir [Azure Resource Manager şablonunu](#template) kullanabilirsiniz. Seçtiğiniz aracı kullanarak bir sanal ağ eşlemesi oluşturma adımlarına doğrudan gitmek için önceki araç bağlantılarından herhangi birini seçin.

Sanal ağlar farklı aboneliklerdeyse ve abonelikler farklı Azure Active Directory kiracılar ile ilişkiliyse, devam etmeden önce aşağıdaki adımları uygulayın:
1. Kullanıcıyı, her bir Active Directory kiracısından, karşı Azure Active Directory kiracısında [Konuk Kullanıcı](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) olarak ekleyin.
1. Her kullanıcı, karşı Azure Active Directory kiracısından gelen konuk kullanıcı davetini kabul etmelidir.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Eşleme oluşturma-Azure portal

Aşağıdaki adımlarda her abonelik için farklı hesaplar kullanılır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, portaldan oturum açmak için adımları atlayabilir ve sanal ağlara başka bir Kullanıcı izinleri atama adımlarını atlayabilirsiniz.

1. [Azure Portal](https://portal.azure.com) *UserA* olarak oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
2. **+ Kaynak oluştur**' u seçin, **ağ**' ı seçin ve ardından **sanal ağ**' ı seçin.
3. Aşağıdaki ayarlar için aşağıdaki örnek değerleri seçin veya girin, ardından **Oluştur**' u seçin.
    - **Ad**: *myvneta*
    - **Adres alanı**: *10.0.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.0.0.0/24*
    - **Abonelik**: abonelik A ' yı seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve *myresourcegroupa* girin
    - **Konum**: *Doğu ABD*
4. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvneta* yazın. Arama sonuçlarında göründüğünde **Myvneta** öğesini seçin.
5. Sol taraftaki seçenekler dikey listesinden **erişim denetimi (IAM)** seçeneğini belirleyin.
6. **Myvneta-Access Control (IAM)** altında **+ rol ataması Ekle**' yi seçin.
7. **Rol** kutusunda **ağ katılımcısı** ' nı seçin.
8. **Seç** kutusunda, *UserB*' yi seçin veya aramak için UserB 'nin e-posta adresini yazın.
9. **Kaydet**’i seçin.
10. **Myvneta-Access Control (IAM)** altında, sol taraftaki seçenekler dikey listesinden **Özellikler** ' i seçin. Daha sonraki bir adımda kullanılan **kaynak kimliğini** kopyalayın. Kaynak KIMLIĞI Şu örneğe benzer: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Portaldan UserA olarak oturum açın, sonra UserB olarak oturum açın.
12. Adım 2-3, aşağıdaki değerleri girerek veya seçerek 3. Adım:

    - **Ad**: *myvnetb*
    - **Adres alanı**: *10.1.0.0/16*
    - **Alt ağ adı**: *varsayılan*
    - **Alt ağ adres aralığı**: *10.1.0.0/24*
    - **Abonelik**: abonelik B ' yi seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve *myresourcegroupb* girin
    - **Konum**: *Doğu ABD*

13. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvnetb* yazın. Arama sonuçlarında göründüğünde **Myvnetb** ' yi seçin.
14. **Myvnetb** altında, sol taraftaki seçenekler dikey listesinden **Özellikler** ' i seçin. Daha sonraki bir adımda kullanılan **kaynak kimliğini** kopyalayın. Kaynak KIMLIĞI Şu örneğe benzer: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. **Myvnetb** altında **ERIŞIM denetimi (IAM)** öğesini seçin ve ardından adım 8 ' de **KullanıcıA** 'yı girerek myvnetb için 5-10 adımlarını doldurun.
16. Portaldan UserB olarak oturum açın ve UserA olarak oturum açın.
17. Portalın üst kısmındaki **kaynakları ara** kutusuna *Myvneta* yazın. Arama sonuçlarında göründüğünde **Myvneta** öğesini seçin.
18. **Myvneta** öğesini seçin.
19. **Ayarlar** altında, eşlemeler ' **i seçin.**
20. **Myvneta-Peerler** altında **+ Ekle** ' yi seçin.
21. **Eşleme Ekle**' nin altında, aşağıdaki seçenekleri girin veya seçin, ardından **Tamam**' ı seçin:
     - **Ad**: *myvnetatomyvnetb*
     - **Sanal ağ dağıtım modeli**: **Kaynak Yöneticisi** seçin.
     - **Kaynak kimliğimi biliyorum**: Bu kutuyu işaretleyin.
     - **Kaynak kimliği**: 14. ADıMDAKI kaynak kimliğini girin.
     - **Sanal ağ erişimine Izin ver:** **Etkin** ' in seçildiğinden emin olun.
    Bu öğreticide başka hiçbir ayar kullanılmaz. Tüm eşleme ayarları hakkında daha fazla bilgi edinmek için [sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md#create-a-peering)makalesini okuyun.
22. Oluşturduğunuz eşleme, önceki adımda **Tamam ' ı** seçtikten sonra kısa bir bekleme durumunda görünür. **Başlatıldı** , oluşturduğunuz **Myvnetatomyvnetb** eşlemesi için **eşleme durumu** sütununda listelenir. MyVnetB 'e Nvneta olarak ulaştınız, ancak artık myVnetB ile myVnetA arası bir bağlantı oluşturmanız gerekir. Sanal ağlardaki kaynakların birbirleriyle iletişim kurmasını sağlamak için eşlemenin her iki yönde de oluşturulması gerekir.
23. Portaldan UserA olarak oturum açın ve UserB olarak oturum açın.
24. 17-21 adımlarını, myVnetB için yeniden doldurun. 21. adımda, *Myvnetbtomyvneta* eşlemesini adlandırın, **sanal ağ** Için *myvneta* öğesini seçin ve **kaynak kimliği** kutusuna 10. adımdan kimliği girin.
25. Bir süre sonra, myVnetB **için eşleme oluşturmak üzere birkaç** saniye sonra, yeni oluşturduğunuz **Myvnetbtomyvneta** eşlemesi, **eşleme durumu** sütununda **bağlı** olarak listelenir.
26. Portaldan UserB olarak oturum açın ve UserA olarak oturum açın.
27. Adım 17-19 ' i yeniden doldurun. **Myvnetatovnetb** eşlemesi Için de **eşleme durumu** artık **bağlı**. Eşleme, eşteki her iki sanal ağ için de **eşleme durumu** sütununa **bağlı** olduktan sonra başarılı bir şekilde oluşturulur. Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.
28. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturmak, her bir sanal ağ için bir sanal makine oluşturabilir ve bağlantıyı doğrulamak için bir sanal makineden diğerine bağlanabilirsiniz.
29. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makalenin [kaynakları silme](#delete-portal) bölümünde yer alan adımları izleyin.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Eşleme oluşturma-Azure CLı

Bu öğretici her abonelik için farklı hesaplar kullanır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, Azure oturumunu kapatmak için gereken adımları atlayabilir ve Kullanıcı rolü atamaları oluşturan komut dosyası satırlarını kaldırabilirsiniz. UserA@azure.com UserB@azure.com Aşağıdaki betiklerin tümünde ve KullanıcıA ve KullanıcıB için kullandığınız kullanıcı adlarıyla değiştirin.

Aşağıdaki betikler:

- Azure CLı sürüm 2.0.4 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Bash kabuğu 'nda işe yarar. Windows istemcisi üzerinde Azure CLI betiklerini çalıştırma seçenekleri için bkz. [Windows’da Azure CLI'yi yükleme](/cli/azure/install-azure-cli-windows).

CLı ve bağımlılıklarını yüklemek yerine Azure Cloud Shell kullanabilirsiniz. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. İle Azure hesabınızda oturum açabilmenize olanak tanıyan bir Cloud Shell çağıran aşağıdaki komut dosyasında bulunan **deneyin** düğmesini seçin.

1. Bir CLı oturumu açın ve komutunu kullanarak Azure 'da UserA olarak oturum açın `azure login` . Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
2. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın,, `<SubscriptionA-Id>` subscriptiona 'NıN kimliğiyle değiştirin, ardından değiştirilen betiği kopyalayın, CLI oturumunuza yapıştırın ve ' a basın `Enter` . Abonelik kimliğinizi bilmiyorsanız `az account show` komutunu girin. Çıkışdaki **ID** değeri, abonelik kimliğiniz olur.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Komutunu kullanarak Azure oturumunu oturumu açın `az logout` ve ardından Azure 'Da UserB olarak oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
4. MyVnetB oluştur. 2. adımdaki betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. `<SubscriptionA-Id>`, SubscriptionB kimliği ile değiştirin. 10.0.0.0/16 ' yı 10.1.0.0/16 olarak değiştirin, tümünü B olarak ve tüm BS ' A değiştirin. değiştirilen betiği kopyalayın, CLı oturumunuza yapıştırın ve ' A basın `Enter` .
5. Azure oturumunu UserB olarak kapatıp Azure 'da UserA olarak oturum açın.
6. MyVnetA 'dan myVnetB 'ye bir sanal ağ eşlemesi oluşturun. Aşağıdaki betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. `<SubscriptionB-Id>`, SubscriptionB kimliği ile değiştirin. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, CLı oturumunuza yapıştırın ve ENTER tuşuna basın.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. MyVnetA öğesinin eşleme durumunu görüntüleyin.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Durum **başlatıldı**. MyVnetB 'den myVnetA eşlemesini oluşturduktan sonra **bağlı** olarak değişir.

8. Azure 'dan oturum açın ve Azure 'da UserB olarak oturum açın.
9. MyVnetB öğesinden myVnetA öğesine eşleme oluşturun. Adım 6 ' da betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. `<SubscriptionB-Id>`, SubscriptionA 'NıN kimliğiyle değiştirin ve tümünü B olarak ve tüm BS ' A değiştirin. Değişiklikleri yaptıktan sonra, değiştirilen betiği kopyalayın, CLı oturumunuza yapıştırın ve ' a basın `Enter` .
10. MyVnetB öğesinin eşleme durumunu görüntüleyin. Adım 7 ' de betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. Kaynak grubu ve sanal ağ adları için A 'yı B olarak değiştirin, betiği kopyalayın, değiştirilen betiği CLı oturumunuza yapıştırın ve ardından ' A basın `Enter` . Eşleme durumu **bağlı**. MyVnetB 'den myVnetA eşlemesini oluşturduktan sonra myVnetA ' ın eşleme durumu **bağlı** olarak değişir. KullanıcıA 'yı Azure 'a kaydedebilir ve myVnetA eşleme durumunu doğrulamak için 7. adımı tekrar tamamlayabilirsiniz.

    > [!NOTE]
    > Eşleme durumu her iki sanal ağ için de **bağlanana** kadar eşleme kurulmaz.

11. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturmak, her bir sanal ağ için bir sanal makine oluşturabilir ve bağlantıyı doğrulamak için bir sanal makineden diğerine bağlanabilirsiniz.
12. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-cli) bölümündeki adımları uygulayın.

Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Eşleme oluşturma-PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğretici her abonelik için farklı hesaplar kullanır. Her iki abonelik için izinleri olan bir hesap kullanıyorsanız, tüm adımlar için aynı hesabı kullanabilir, Azure oturumunu kapatmak için gereken adımları atlayabilir ve Kullanıcı rolü atamaları oluşturan komut dosyası satırlarını kaldırabilirsiniz. UserA@azure.com UserB@azure.com Aşağıdaki betiklerin tümünde ve KullanıcıA ve KullanıcıB için kullandığınız kullanıcı adlarıyla değiştirin.

1. Sürüm 1.0.0 veya daha yüksek Azure PowerShell olduğunu doğrulayın. Bunu çalıştırarak bunu yaparak `Get-Module -Name Az` PowerShell [az modülünün](/powershell/azure/install-az-ps)en son sürümünü yüklemenizi öneririz. Azure PowerShell'i kullanmaya yeni başladıysanız [Azure PowerShell'e genel bakış](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json) sayfasını inceleyin.
2. Bir PowerShell oturumu başlatın.
3. PowerShell 'de komutunu girerek KullanıcıA olarak Azure 'da oturum açın `Connect-AzAccount` . Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
4. A kaynak grubu ve sanal ağ oluşturun. aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. `<SubscriptionA-Id>`, SubscriptionA 'NıN kimliğiyle değiştirin. Abonelik kimliğinizi bilmiyorsanız, `Get-AzSubscription` görüntülemek için komutunu girin. Döndürülen çıktıda **kimliği** için olan değer abonelik kimliğiniz olur. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Azure 'dan oturumu kapatıp KullanıcıB 'de oturum açın. Oturum açarken kullandığınız hesabın, sanal ağ eşlemesi oluşturmak için gerekli izinlere sahip olması gerekir. İzinlerin listesi için bkz. [sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).
6. 4. adımdaki betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. `<SubscriptionA-Id>`Abonelik B 'nın kimliğiyle değiştirin. 10.0.0.0/16 değerini 10.1.0.0/16 olarak değiştirin. Tümünü B olarak ve tüm BS 'Leri olarak değiştirin. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter` .
7. Azure 'dan oturumu kapatıp KullanıcıA 'da oturum açın.
8. MyVnetA 'dan myVnetB öğesine eşleme oluşturun. Aşağıdaki betiği bilgisayarınızdaki bir metin düzenleyicisine kopyalayın. `<SubscriptionB-Id>`B ABONELIĞININ kimliğiyle değiştirin. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. MyVnetA öğesinin eşleme durumunu görüntüleyin.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Durum **başlatıldı**. MyVnetB 'den myVnetA eşlemesini ayarladıktan sonra **bağlı** olarak değişir.

10. Azure 'dan oturumu kapatıp KullanıcıB 'de oturum açın.
11. MyVnetB öğesinden myVnetA öğesine eşleme oluşturun. Adım 8 ' deki betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. `<SubscriptionB-Id>`A ABONELIĞININ kimliğiyle değiştirin ve tümünü B olarak ve tüm BS ' a değiştirin. Betiği yürütmek için, değiştirilmiş betiği kopyalayın, PowerShell 'e yapıştırın ve ardından ' a basın `Enter` .
12. MyVnetB öğesinin eşleme durumunu görüntüleyin. Adım 9 ' da betik içeriğini BILGISAYARıNıZDAKI bir metin düzenleyicisine kopyalayın. Kaynak grubu ve sanal ağ adları için A ile B 'yi değiştirin. Betiği yürütmek için, değiştirilmiş betiği PowerShell 'e yapıştırın ve ardından ' a basın `Enter` . Durum **bağlı**. **Myvnetb** 'den **myvneta** eşlemesini oluşturduktan sonra **myvneta** ' ın eşleme durumu **bağlı** olarak değişir. KullanıcıA 'yı Azure 'a kaydedebilir ve myVnetA eşleme durumunu doğrulamak için 9. adımı tekrar tamamlayabilirsiniz.

    > [!NOTE]
    > Eşleme durumu her iki sanal ağ için de **bağlanana** kadar eşleme kurulmaz.

    Her iki sanal ağda oluşturduğunuz tüm Azure kaynakları, IP adresleri aracılığıyla birbirleriyle iletişim kurabiliyor. Sanal ağlar için varsayılan Azure ad çözümlemesi kullanıyorsanız, sanal ağlardaki kaynaklar sanal ağlardaki adları çözemeyebilir. Bir eşteki sanal ağlarda adları çözümlemek istiyorsanız kendi DNS sunucunuzu oluşturmanız gerekir. [Kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ayarlama hakkında bilgi edinin.

13. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturmak, her bir sanal ağ için bir sanal makine oluşturabilir ve bağlantıyı doğrulamak için bir sanal makineden diğerine bağlanabilirsiniz.
14. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için, bu makaledeki [kaynakları silme](#delete-powershell) bölümündeki adımları uygulayın.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Eşleme oluşturma-Kaynak Yöneticisi şablonu

1. Bir sanal ağ oluşturmak ve uygun [izinleri](virtual-network-manage-peering.md#permissions)atamak için, bu makalenin [Portal](#portal), [Azure CLI](#cli)veya [PowerShell](#powershell) bölümlerindeki adımları izleyin.
2. Aşağıdaki metni yerel bilgisayarınızdaki bir dosyaya kaydedin. `<subscription ID>`UserA 'nın ABONELIK kimliği ile değiştirin. Dosyayı, örneğin vnetpeeringA.jsolarak kaydedebilirsiniz.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Azure 'da UserA olarak oturum açın ve [portalı](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [POWERSHELL](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)veya [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)kullanarak şablonu dağıtın. 2. adımda örnek JSON metnini kaydettiğiniz dosya adını belirtin.
4. 2. adımdaki örnek json dosyasını bilgisayarınızdaki bir dosyaya kopyalayın ve Şununla başlayan satırlarda değişiklik yapın:
   - **ad**: *Myvneta/Myvmakinetomyvnetb* ' i *Myvnetb/myVnetBToMyVnetA* olarak değiştirin.
   - **kimlik**: `<subscription ID>` UserB 'nin abonelik kimliğiyle değiştirin ve *Myvnetb* öğesini *myvneta* ile değiştirin.
5. Adım 3 ' ü yeniden tamamladıktan sonra, Azure 'da UserB olarak oturum açarsınız.
6. **Isteğe bağlı**: Bu öğreticide sanal makineler oluşturmak, her bir sanal ağ için bir sanal makine oluşturabilir ve bağlantıyı doğrulamak için bir sanal makineden diğerine bağlanabilirsiniz.
7. **Isteğe bağlı**: Bu öğreticide oluşturduğunuz kaynakları silmek için Azure Portal, PowerShell veya Azure CLI kullanarak bu makalenin [kaynakları silme](#delete) bölümünde yer alan adımları uygulayın.

## <a name="delete-resources"></a><a name="delete"></a>Kaynakları silme
Bu öğreticiyi tamamladığınızda, öğreticide oluşturduğunuz kaynakları silmek isteyebilirsiniz, bu sayede kullanım ücretlerine tabi kalmazsınız. Kaynak grubunun silinmesi, kaynak grubundaki tüm kaynakları da siler.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure portal

1. Azure portal UserA olarak oturum açın.
2. Portal arama kutusuna **Myresourcegroupa** yazın. Arama sonuçlarında **Myresourcegroupa**' yı seçin.
3. **Sil**’i seçin.
4. Silmeyi onaylamak için, **kaynak grubu adını yazın** kutusuna **Myresourcegroupa** girin ve **Sil**' i seçin.
5. Portaldan UserA olarak oturum açın ve UserB olarak oturum açın.
6. MyResourceGroupB için 2-4 adımlarını izleyin.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI’si

1. Azure 'da UserA olarak oturum açın ve aşağıdaki komutu yürütün:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Azure oturumunu UserA olarak kapatıp UserB olarak oturum açın.
3. Aşağıdaki komutu yürütün:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Azure 'da UserA olarak oturum açın ve aşağıdaki komutu yürütün:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Azure oturumunu UserA olarak kapatıp UserB olarak oturum açın.
3. Aşağıdaki komutu yürütün:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Sonraki adımlar

- Üretim kullanımı için bir sanal ağ eşlemesi oluşturmadan önce önemli [sanal ağ eşleme kısıtlamalarını ve davranışları](virtual-network-manage-peering.md#requirements-and-constraints) hakkında ayrıntılı bilgi edinin.
- Tüm [sanal ağ eşleme ayarları](virtual-network-manage-peering.md#create-a-peering)hakkında bilgi edinin.
- Sanal ağ eşlemesi ile [hub ve bağlı ağ topolojisi oluşturmayı](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) öğrenin.