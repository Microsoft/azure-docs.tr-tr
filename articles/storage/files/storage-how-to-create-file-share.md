---
title: Azure dosya paylaşımı oluşturma | Microsoft Docs
description: Azure portalı, PowerShell ve Azure CLI kullanarak Azure Dosyaları'nda bir Azure dosya paylaşımı oluşturma.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3b30df7410f56c203eb600089cce130a8de23d1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514613"
---
# <a name="create-a-file-share-in-azure-files"></a>Azure Dosyaları'nda bir dosya paylaşımı oluşturma
Azure dosya paylaşımlarını [Azure Portal](https://portal.azure.com/), Azure Storage PowerShell cmdlet 'Lerini, Azure depolama istemci kitaplıklarını veya azure Storage REST API kullanarak oluşturabilirsiniz. Bu öğreticide şunları öğreneceksiniz:
* Azure portal kullanarak Azure dosya paylaşma oluşturma
* [PowerShell kullanarak Azure dosya paylaşımı oluşturma](#create-file-share-through-powershell)
* [CLı kullanarak Azure dosya paylaşma oluşturma](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Önkoşullar
Azure dosya paylaşımı oluşturmak için zaten var olan bir Depolama Hesabı kullanabilir veya [yeni bir Azure Depolama Hesabı oluşturabilirsiniz](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). PowerShell ile Azure dosya paylaşımı oluşturmak için depolama hesabınızın hesap anahtarı ve adı gerekir. PowerShell veya CLı kullanmayı planlıyorsanız bir depolama hesabı anahtarına ihtiyaç duyarsınız.

> [!NOTE]
> 5 TiB 'den büyük dosya paylaşımları oluşturmak istiyorsanız, [büyük dosya paylaşımlarını etkinleştirme](storage-files-how-to-create-large-file-share.md)makalemize bakın.

## <a name="create-a-file-share-through-the-azure-portal"></a>Azure portal aracılığıyla bir dosya paylaşma oluşturma
1. **Azure Portal depolama hesabı dikey penceresine gidin**:    
    ![Depolama Hesabı dikey penceresi](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Dosya Paylaşımı ekleme düğmesine tıklayın**:    
    ![Dosya Paylaşımı ekleme düğmesine tıklayın](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Ad ve kota belirtin**:    
    ![Yeni dosya paylaşımı için ad ve istenen kotayı sağlayın](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Yeni dosya paylaşımınızı görüntüleyin**: ![Yeni dosya paylaşımınızı görüntüleyin](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Dosyayı karşıya yükleyin**: ![Dosyayı karşıya yükleyin](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Dosya paylaşımınıza göz atın ve dizinlerinizle dosyalarınız yönetin**: ![Dosya paylaşımına göz atın](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>PowerShell üzerinden dosya paylaşımı oluşturma
PowerShell’i kullanmaya hazırlamak için Azure PowerShell cmdlet’lerini indirin ve yükleyin. Yükleme noktası ve yükleme yönergelerini  for [Azure PowerShell yükleme ve yapılandırma](https://docs.microsoft.com/powershell/azure/overview) konusuna bakın.

> [!Note]  
> En güncel Azure PowerShell modülünü indirmeniz ve yüklemeniz veya yükseltmeniz önerilir.

1. **Yeni bir depolama hesabı oluşturun:** Depolama hesabı, Azure dosya paylaşımlarını ve BLOB 'lar ya da kuyruklar gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzudur.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. **Yeni dosya paylaşımını oluşturun**:    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Dosya paylaşımınızın adı küçük harflerden oluşmalıdır. Dosya paylaşımlarını ve dosyaları adlandırma hakkında tüm ayrıntılar için bkz. [adlandırma ve başvuru paylaşımları, dizinler, dosyalar ve meta veriler](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Komut Satırı Arabirimi (CLI) üzerinden dosya paylaşımı oluşturma
1. **Komut satırı arabirimini (CLı) kullanmaya hazırlanmak için Azure CLı 'yı indirip yükleyin.**  
    Bkz. [Azure CLI 'Yı yüklemeyi](https://docs.microsoft.com/cli/azure/install-azure-cli) ve [Azure CLI kullanmaya başlama](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Paylaşımı oluşturmak istediğiniz depolama hesabına bir bağlantı dizesi oluşturun.**  
    Aşağıdaki örnekte, depolama hesabı adı ve kaynak grubunuz  with  ```<storage-account>```  and  ```<resource_group>``` değiştirin:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Dosya paylaşma oluşturma**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Sonraki adımlar
* [Dosya Paylaşımını Bağlama - Windows](storage-how-to-use-files-windows.md)
* [Dosya Paylaşımını Bağlama - Linux](../storage-how-to-use-files-linux.md)
* [Dosya Paylaşımını Bağlama - macOS](storage-how-to-use-files-mac.md)

Azure Dosyaları hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın.

* [SSS](../storage-files-faq.md)
* [Windows’da sorun giderme](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux’ta sorun giderme](storage-troubleshoot-linux-file-connection-problems.md)   
