---
title: Hızlı başlangıç-Azure NetApp Files ayarlama ve bir NFS birimi oluşturma | Microsoft Docs
description: Hızlı başlangıç-Azure NetApp Files hızlı bir şekilde ayarlama ve birim oluşturma işlemlerinin nasıl yapılacağını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 06ebe8ed22453289fa02c238d9b2e6ef13191f2a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888543"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Hızlı başlangıç: Azure NetApp Files ayarlama ve NFS birimi oluşturma 

Bu makalede, Azure NetApp Files hızlı bir şekilde ayarlama ve birim oluşturma işlemlerinin nasıl yapılacağı gösterilir. 

Bu hızlı başlangıçta, aşağıdaki öğeleri ayarlayacaksınız:

- Azure NetApp Files ve NetApp kaynak sağlayıcısı için kayıt
- NetApp hesabı
- Kapasite havuzu
- Azure NetApp Files için NFS birimi

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce 

> [!IMPORTANT] 
> Azure NetApp Files hizmetine erişim verilmesi gerekir.  Hizmete erişim istemek için, [Azure NetApp Files eklenebileceğinizi gönderim sayfasına](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)bakın.  Devam etmeden önce Azure NetApp Files ekipten bir resmi onay e-postası beklemeniz gerekir. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Azure NetApp Files ve NetApp kaynak sağlayıcısına kaydolun

> [!NOTE]
> Kayıt işleminin tamamlanması biraz zaman alabilir.
>

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Portal 'ı kullanarak kayıt adımları için yukarıda belirtilen şekilde bir Cloud Shell oturumu açın ve şu Azure CLı adımlarını izleyin:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Bu nasıl yapılır makalesi Azure PowerShell modülünü az Version 2.6.0 veya üstünü gerektirir. Geçerli sürümünüzü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps). Tercih ederseniz, bunun yerine bir PowerShell oturumunda Cloud Shell konsolunu kullanabilirsiniz.

1. PowerShell komut isteminde (veya PowerShell Cloud Shell oturumunda), Azure NetApp Files için beyaz listeye alınmış aboneliği belirtin:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Azure Kaynak sağlayıcısını kaydedin:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>NetApp hesabı oluşturma

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Azure portal arama kutusuna **Azure NetApp Files** girin ve görüntülenen listeden **Azure NetApp Files** ' yı seçin.

      ![Azure NetApp Files seçin](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Yeni bir NetApp hesabı oluşturmak için **+ Ekle**'ye tıklayın.

     ![Yeni NetApp hesabı oluştur](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Yeni NetApp hesabı penceresinde, aşağıdaki bilgileri sağlayın: 
   1. Hesap adı için **myaccount1** girin. 
   2. Aboneliğinizi seçin.
   3. Yeni kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin. Kaynak grubu adı için **myRG1** girin. **Tamam** düğmesine tıklayın. 
   4. Hesap konumunuzu seçin.  

      ![Yeni NetApp hesabı penceresi](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Kaynak grubu penceresi](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Yeni NetApp hesabınızı oluşturmak için **Oluştur** ' a tıklayın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Örneklerin geri kalanı boyunca onlara başvurabilmeniz için bazı değişkenler tanımlayın:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Desteklenen bölgelerin listesi için lütfen [bölgeye göre kullanılabilen ürünlere](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) bakın.
    > Komut satırı araçlarımız tarafından desteklenen bölge adını almak için lütfen `Get-AzLocation | select Location` kullanın
    >

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak yeni bir kaynak grubu oluşturun:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) komutuyla Azure NetApp Files hesabı oluşturun:
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Örneklerin geri kalanı boyunca onlara başvurabilmeniz için bazı değişkenler tanımlayın:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Desteklenen bölgelerin listesi için lütfen [bölgeye göre kullanılabilen ürünlere](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) bakın.
    > Komut satırı araçlarımız tarafından desteklenen bölge adını almak için lütfen `az account list-locations -query "[].{Region:name}" --out table` kullanın
    >

2. [Az Group Create](/cli/azure/group#az-group-create) komutunu kullanarak yeni bir kaynak grubu oluşturun:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. [Az netappfiles hesabı Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) komutuyla Azure NetApp Files hesabı oluşturun:
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Kapasite havuzunu ayarlama

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Azure NetApp Files yönetimi dikey penceresinde NetApp hesabınızı (**myaccount1**) seçin.

    ![NetApp hesabı seçin](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. NetApp hesabınızın Azure NetApp Files yönetim dikey penceresinde **Kapasite havuzları**' na tıklayın.

    ![Kapasite havuzları ' na tıklayın](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. **+ Havuz Ekle**' ye tıklayın. 

    ![Havuz Ekle 'ye tıklayın](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Kapasite havuzu için bilgi sağlayın: 
    1. Havuz adı olarak **mypool1** girin.
    2. Hizmet düzeyi için **Premium** ' u seçin. 
    3. Havuz boyutu olarak **4 (TiB)** belirtin. 

5. **Tamam** düğmesine tıklayın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Daha sonra başvurmak üzere bazı yeni değişkenler tanımlama

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. [Az netappfiles Havuz oluştur](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) kullanarak yeni bir kapasite havuzu oluşturun 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files için NFS birimi oluşturma

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. NetApp hesabınızın Azure NetApp Files yönetim dikey penceresinde **birimler**' e tıklayın.

    ![Birimler 'e tıklayın](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

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
      
> [!IMPORTANT] 
> NFSv 4.1 özelliğine erişim için beyaz liste gerekir.  Beyaz liste istemek için <anffeedback@microsoft.com>bir istek gönderebilirsiniz. 

  ![Hızlı başlangıç için NFS protokolünü belirtin](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. **Gözden geçir ve oluştur**’a tıklayın.

    ![İnceleme ve pencere oluşturma](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Birimle ilgili bilgileri gözden geçirin ve ardından **Oluştur**' a tıklayın.  
    Oluşturulan birim birimler dikey penceresinde görünür.

    ![Birim oluşturuldu](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Daha sonraki kullanımlar için bazı değişkenler tanımlama.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. [Az Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create) komutunu kullanarak alt ağ olmadan sanal ağ oluşturun.
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. [Az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) komutunu kullanarak Temsilcili bir alt ağ oluşturun.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. [Az netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) komutunu kullanarak birimi oluşturun.
   
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

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.  

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz. 

1. Azure portal arama kutusuna **Azure NetApp Files** girin ve görüntülenen listeden **Azure NetApp Files** ' yı seçin.

2. Abonelikler listesinde, silmek istediğiniz kaynak grubuna (myRG1) tıklayın. 

    ![Kaynak gruplarına gidin](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Kaynak grubu sayfasında, **kaynak grubunu sil**' e tıklayın.

    ![Kaynak grubunu silme](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Bir pencere açılır ve kaynak grubuyla birlikte silinecek kaynaklar hakkında bir uyarı görüntüler.

4. Kaynak grubunu ve içindeki tüm kaynakları kalıcı olarak silmek istediğinizi onaylamak için kaynak grubunun adını (myRG1) girin ve ardından **Sil**' e tıklayın.

    ![Kaynak grubunu silme](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.  

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz.

1. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu silin.
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

İşiniz bittiğinde ve isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunu silme eylemi geri alınamaz.  

> [!IMPORTANT]
> Kaynak grupları içindeki tüm kaynaklar kalıcı olarak silinecek ve geri alınamaz.

1. [Az Group Delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu silin.
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Sonraki adımlar  

> [!div class="nextstepaction"]
> [Azure NetApp Files kullanarak birimleri yönetme](azure-netapp-files-manage-volumes.md)  
