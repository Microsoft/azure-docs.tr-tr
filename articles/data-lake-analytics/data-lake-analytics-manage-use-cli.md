---
title: Azure CLı kullanarak Azure Data Lake Analytics yönetme
description: Bu makalede Data Lake Analytics işleri, veri kaynaklarını & kullanıcıları yönetmek için Azure CLı 'nin nasıl kullanılacağı açıklanır.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: d66926d8ba87096537800d22a9c116b7b10d23cf
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309734"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Azure komut satırı arabirimi 'ni (CLı) kullanarak Azure Data Lake Analytics yönetme

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure CLı kullanarak Azure Data Lake Analytics hesaplarını, veri kaynaklarını, kullanıcıları ve işleri yönetmeyi öğrenin. Diğer araçları kullanarak yönetim konularını görmek için yukarıdaki sekmeye tıklayın.


**Önkoşullar**

Bu öğreticiye başlamadan önce aşağıdaki kaynaklara sahip olmanız gerekir:

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLı. Bkz. [Azure CLI'yı yükleme ve yapılandırma](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Bu demoyu tamamlamak için **yayın öncesi sürüm** [Azure CLI araçlarını](https://github.com/MicrosoftBigData/AzureDataLake/releases) indirip yükleyin.

* `az login` Komutunu kullanarak kimlik doğrulaması yapın ve kullanmak istediğiniz aboneliği seçin. Bir iş veya okul hesabı kullanarak kimlik doğrulama gerçekleştirme konusunda daha fazla bilgi için bkz. [Azure CLI'dan Azure aboneliğine bağlanma](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Artık Data Lake Analytics ve Data Lake Store komutlarına erişebilirsiniz. Data Lake Store ve Data Lake Analytics komutlarını listelemek için aşağıdaki komutu çalıştırın:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Hesapları yönetme

Herhangi bir Data Lake Analytics işini çalıştırmadan önce bir Data Lake Analytics hesabınız olması gerekir. Azure HDInsight 'tan farklı olarak, bir iş çalıştırmayan bir analiz hesabı için ödeme yapmazsınız. Yalnızca bir iş çalıştığı zaman için ödeme yaparsınız.  Daha fazla bilgi için bkz. [Azure Data Lake Analytics genel bakış](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Hesap oluşturma

Data Lake bir hesap oluşturmak için aşağıdaki komutu çalıştırın, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Hesapları Güncelleştir

Aşağıdaki komut, mevcut bir Data Lake Analytics hesabının özelliklerini güncelleştirir

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Hesapları listeleme

Belirli bir kaynak grubu içindeki Data Lake Analytics hesaplarını listeleme

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Hesap ayrıntılarını al

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Hesap silme

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Veri kaynaklarını yönetme

Data Lake Analytics Şu anda aşağıdaki iki veri kaynağını desteklemektedir:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Depolama](../storage/common/storage-introduction.md)

Bir analiz hesabı oluşturduğunuzda, bir Azure Data Lake Storage hesabını varsayılan depolama hesabı olacak şekilde atamanız gerekir. Varsayılan Data Lake depolama hesabı, iş meta verilerini ve iş denetim günlüklerini depolamak için kullanılır. Bir analiz hesabı oluşturduktan sonra, ek Data Lake Storage hesapları ve/veya Azure depolama hesabı ekleyebilirsiniz. 

### <a name="find-the-default-data-lake-store-account"></a>Varsayılan Data Lake Store hesabını bulun

`az dla account show` Komutunu çalıştırarak kullanılan varsayılan Data Lake Store hesabını görüntüleyebilirsiniz. Varsayılan hesap adı defaultDataLakeStoreAccount özelliği altında listelenir.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Ek BLOB depolama hesapları ekleme

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Yalnızca BLOB depolama kısa adları desteklenir. FQDN kullanmayın, örneğin "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Ek Data Lake Store hesapları ekleme

Aşağıdaki komut, belirtilen Data Lake Analytics hesabını ek bir Data Lake Store hesabıyla güncelleştirir:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Mevcut veri kaynağını güncelleştir

Var olan bir BLOB depolama hesabı anahtarını güncelleştirmek için:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Veri kaynaklarını listeleyin:

Data Lake Store hesaplarını listelemek için:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

BLOB depolama hesabını listelemek için:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics listesi veri kaynağı](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Veri kaynaklarını Sil:
Bir Data Lake Store hesabını silmek için:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

BLOB depolama hesabını silmek için:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>İşleri yönet
Bir iş oluşturabilmeniz için önce bir Data Lake Analytics hesabınızın olması gerekir.  Daha fazla bilgi için bkz. [Data Lake Analytics hesaplarını yönetme](#manage-accounts).

### <a name="list-jobs"></a>İşleri listeleme

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics listesi veri kaynağı](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>İş ayrıntılarını al

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>İşleri gönder

> [!NOTE]
> Bir işin varsayılan önceliği 1000 ' dir ve bir iş için varsayılan paralellik derecesi 1 ' dir.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>İşleri iptal et
İş kimliğini bulmak için List komutunu kullanın ve ardından işlemi iptal etmek için iptal 'i kullanın.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>İşlem hatları ve tekrarlar

**İşlem hatları ve tekrarlar hakkında bilgi edinin**

Önceden gönderilmiş işler hakkında işlem hatları bilgilerini görmek için `az dla job pipeline` komutlarını kullanın.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Önceden gönderilmiş işler hakkında tekrar bilgilerini görmek için `az dla job recurrence` komutlarını kullanın.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Ayrıca bkz.
* [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
* [Azure portalını kullanarak Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics'i Azure portalını kullanarak yönetme](data-lake-analytics-manage-use-portal.md)
* [Azure portalını kullanarak Azure Data Lake Analytics işlerini izleme ve sorun giderme](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

