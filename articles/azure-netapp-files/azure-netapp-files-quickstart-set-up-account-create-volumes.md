---
title: 'Hızlı başlangıç: Azure NetApp Files ve NFS birimi ayarlama'
description: Hızlı başlangıç-Azure NetApp Files hızlı bir şekilde ayarlama ve birim oluşturma işlemlerinin nasıl yapılacağını açıklar.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 77d5ce2cc903be51b7a38d6edc34bb8424c52ddb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786108"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Hızlı başlangıç: Azure NetApp Files ayarlama ve NFS birimi oluşturma

Bu makalede, Azure NetApp Files hızlı bir şekilde ayarlama ve bir NFS birimi oluşturma işlemlerinin nasıl yapılacağı gösterilir. 

Bu hızlı başlangıçta, aşağıdaki öğeleri ayarlayacaksınız:

- Azure NetApp Files ve NetApp kaynak sağlayıcısı için kayıt
- NetApp hesabı
- Kapasite havuzu
- Azure NetApp Files için NFS birimi

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

NFS birimi ve ilgili konular için etkinleştirebileceğiniz tüm özellikleri görmek için bkz. [NFS birimi oluşturma](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Başlamadan önce

> [!IMPORTANT]
> Azure NetApp Files hizmetine erişim verilmesi gerekir. Hizmete erişim istemek için, [Azure NetApp Files eklenebileceğinizi gönderim sayfasına](https://aka.ms/azurenetappfiles)bakın.  Devam etmeden önce Azure NetApp Files ekipten bir resmi onay e-postası beklemeniz gerekir.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Azure NetApp Files ve NetApp kaynak sağlayıcısına kaydolun

> [!NOTE]
> Kayıt işleminin tamamlanması biraz zaman alabilir.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Portal 'ı kullanarak kayıt adımları için yukarıda belirtilen şekilde bir Cloud Shell oturumu açın ve şu Azure CLı adımlarını izleyin:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu nasıl yapılır makalesi Azure PowerShell modülünü az Version 2.6.0 veya üstünü gerektirir. Geçerli sürümünüzü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps). Tercih ederseniz, bunun yerine bir PowerShell oturumunda Cloud Shell konsolunu kullanabilirsiniz.

1. PowerShell komut isteminde (veya PowerShell Cloud Shell oturumunda), Azure NetApp Files için onaylanan aboneliği belirtin:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Azure Kaynak sağlayıcısını kaydedin:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Şablon](#tab/template)

Yok.

Azure NetApp Files ve NetApp kaynak sağlayıcısına kaydolmak için Azure portal, PowerShell veya Azure CLı 'yi kullanın.

Daha fazla bilgi için bkz. [Azure NetApp Files Için kaydolun](azure-netapp-files-register.md) .

---

## <a name="create-a-netapp-account"></a>NetApp hesabı oluşturma

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal arama kutusuna **Azure NetApp Files** girin ve görüntülenen listeden **Azure NetApp Files** ' yı seçin.

      ![Azure NetApp Files seçin](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Yeni bir NetApp hesabı oluşturmak için **+ Ekle**'ye tıklayın.

     ![Yeni NetApp hesabı oluştur](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Yeni NetApp hesabı penceresinde, aşağıdaki bilgileri sağlayın:
   1. Hesap adı için **myaccount1** girin.
   2. Aboneliğinizi seçin.
   3. Yeni kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin. Kaynak grubu adı için **myRG1** girin. **Tamam**'a tıklayın.
   4. Hesap konumunuzu seçin.

      ![Yeni NetApp hesabı penceresi](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Kaynak grubu penceresi](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Yeni NetApp hesabınızı oluşturmak için **Oluştur** ' a tıklayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Örneklerin geri kalanı boyunca onlara başvurabilmeniz için bazı değişkenler tanımlayın:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > Desteklenen bölgelerin listesi için lütfen [bölgeye göre kullanılabilen ürünlere](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) bakın.
    > Komut satırı araçlarımız tarafından desteklenen bölge adını almak için lütfen şunu kullanın `Get-AzLocation | select Location`
    >

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak yeni bir kaynak grubu oluşturun:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) komutuyla Azure NetApp Files hesabı oluşturun:

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Örneklerin geri kalanı boyunca onlara başvurabilmeniz için bazı değişkenler tanımlayın:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > Desteklenen bölgelerin listesi için lütfen [bölgeye göre kullanılabilen ürünlere](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) bakın.
    > Komut satırı araçlarımız tarafından desteklenen bölge adını almak için lütfen şunu kullanın `az account list-locations --query "[].{Region:name}" --out table`
    >

2. [Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak yeni bir kaynak grubu oluşturun:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. [Az netappfiles hesabı Create](/cli/azure/netappfiles/account#az_netappfiles_account_create) komutuyla Azure NetApp Files hesabı oluşturun:

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Şablon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aşağıdaki kod parçacığı, [Microsoft. NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) kaynağını kullanarak bir Azure Resource Manager ŞABLONUNDA (ARM şablonu) bir NetApp hesabı oluşturmayı gösterir. Kodu çalıştırmak için GitHub deponuzdan [tam ARM şablonunu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) indirin.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Kapasite havuzunu ayarlama

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure NetApp Files yönetimi dikey penceresinde NetApp hesabınızı (**myaccount1**) seçin.

    ![NetApp hesabı seçin](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. NetApp hesabınızın Azure NetApp Files yönetim dikey penceresinde **Kapasite havuzları**' na tıklayın.

    ![Kapasite havuzları ' na tıklayın](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. **+ Havuz Ekle**' ye tıklayın.

    ![Havuz Ekle 'ye tıklayın](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kapasite havuzu için bilgi sağlayın:
    * Havuz adı olarak **mypool1** girin.
    * Hizmet düzeyi için **Premium** ' u seçin.
    * Havuz boyutu olarak **4 (TiB)** belirtin.
    * **Otomatik** QoS türünü kullanın.

5. **Oluştur**’a tıklayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Daha sonra başvurmak üzere bazı yeni değişkenler tanımlama

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool) kullanarak yeni bir kapasite havuzu oluşturma

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Daha sonra başvurmak üzere bazı yeni değişkenler tanımlama

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. [Az netappfiles Havuz oluştur](/cli/azure/netappfiles/pool#az_netappfiles_pool_create) kullanarak yeni bir kapasite havuzu oluşturun

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Şablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Aşağıdaki kod parçacığı, [Microsoft. NetApp/netAppAccounts/Capacityhavuzlar](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) kaynağını kullanarak bir Azure Resource Manager ŞABLONUNDA (ARM şablonu) bir kapasite havuzu oluşturmayı gösterir. Kodu çalıştırmak için GitHub deponuzdan [tam ARM şablonunu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) indirin.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files için NFS birimi oluşturma

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. NetApp hesabınızın Azure NetApp Files yönetim dikey penceresinde **birimler**' e tıklayın.

    ![Birimler'e tıklayın](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. **+ Birim ekle**’ye tıklayın.

    ![Birim Ekle 'ye tıklayın](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. Birim Oluştur penceresinde, birim için bilgi sağlayın:
   1. Birim adı olarak **myvol1** girin.
   2. Kapasite havuzunuzu (**mypool1**) seçin.
   3. Kota için varsayılan değeri kullanın.
   4. Yeni bir Azure sanal ağı (VNet) oluşturmak için sanal ağ altında **Yeni oluştur** ' a tıklayın.  Ardından aşağıdaki bilgileri girin:
       * VNET adı olarak **myvnet1** girin.
       * Ayarınız için bir adres alanı belirtin, örneğin, 10.7.0.0/16
       * Alt ağ adı olarak **Myanfsubnet** girin.
       * Alt ağ adres aralığını belirtin, örneğin, 10.7.0.0/24. Ayrılmış alt ağı diğer kaynaklarla paylaşamazsınız.
       * Alt ağ temsili için **Microsoft. NetApp/birimler** ' i seçin.
       * VNET oluşturmak için **Tamam** ' ı tıklatın.
   5. Alt ağ ' da, temsilci alt ağı olarak yeni oluşturulan VNET 'i (**myvnet1**) seçin.

      ![Birim penceresi oluşturma](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Sanal Ağ penceresi oluştur](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. **Protokol**' e tıklayın ve ardından aşağıdaki eylemleri tamamlamayı seçin:
    * Birimin protokol türü olarak **NFS** ' yi seçin.
    * Birimin dışa aktarma yolunu oluşturmak için kullanılacak dosya yolu olarak **myfilepath1** girin.
    * Birim için NFS sürümünü (**NFSv3** veya **nfsv 4.1**) seçin.
      Bkz. NFS sürümleri hakkında [konular](azure-netapp-files-create-volumes.md#considerations) ve [en iyi uygulamalar](azure-netapp-files-create-volumes.md#best-practice) .

    ![Hızlı başlangıç için NFS protokolünü belirtin](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. **Gözden geçir ve oluştur**’a tıklayın.

    ![İnceleme ve pencere oluşturma](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Birimle ilgili bilgileri gözden geçirin ve ardından **Oluştur**' a tıklayın.
    Oluşturulan birim birimler dikey penceresinde görünür.

    ![Birim oluşturuldu](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [New-Aztemsilciyi](/powershell/module/az.network/new-azdelegation) komutuyla "Microsoft. NetApp/Volumes" için bir alt ağ temsili oluşturun.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) komutunu kullanarak bir alt ağ yapılandırması oluşturun.

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) komutunu kullanarak sanal ağı oluşturun.

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) komutunu kullanarak birimi oluşturun.

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Daha sonraki kullanımlar için bazı değişkenler tanımlama.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. [Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create) komutunu kullanarak alt ağ olmadan sanal ağ oluşturun.

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. [Az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) komutunu kullanarak Temsilcili bir alt ağ oluşturun.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. [Az netappfiles Volume Create](/cli/azure/netappfiles/volume#az_netappfiles_volume_create) komutunu kullanarak birimi oluşturun.

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Şablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Aşağıdaki kod parçacıkları, bir sanal ağı ayarlamayı ve bir Azure Resource Manager şablonunda (ARM şablonunda) Azure NetApp Files birim oluşturmayı gösterir. VNet kurulumu, [Microsoft. Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) kaynağını kullanır. Birim oluşturma, [Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/Volumes](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) kaynağını kullanır. Kodu çalıştırmak için GitHub deponuzdan [tam ARM şablonunu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) indirin.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

# <a name="portal"></a>[Portal](#tab/azure-portal)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz.

1. Azure portal arama kutusuna **Azure NetApp Files** girin ve görüntülenen listeden **Azure NetApp Files** ' yı seçin.

2. Abonelikler listesinde, silmek istediğiniz kaynak grubuna (myRG1) tıklayın.

    ![Kaynak gruplarına gidin](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Kaynak grubu sayfasında, **kaynak grubunu sil**' e tıklayın.

    ![Kaynak grubunu Sil düğmesini vurgulayan ekran görüntüsü.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Bir pencere açılır ve kaynak grubuyla birlikte silinecek kaynaklar hakkında bir uyarı görüntüler.

4. Kaynak grubunu ve içindeki tüm kaynakları kalıcı olarak silmek istediğinizi onaylamak için kaynak grubunun adını (myRG1) girin ve ardından **Sil**' e tıklayın.

    ![Kaynak grubunu silmeyi onayla](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz.

1. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu silin.

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz.

1. [Az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu silin.

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Şablon](#tab/template)

Yok.

Kaynak grubunu silmek için Azure portal, PowerShell veya Azure CLı kullanın.

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure NetApp Files’ın depolama hiyerarşisi](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Azure NetApp Files için hizmet düzeyleri](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [NFS birimi oluşturma](azure-netapp-files-create-volumes.md)
