---
title: Azure Data Lake Storage 1. hesabını yönetme-Azure CLı
description: Data Lake Storage 1. hesabı oluşturmak ve temel işlemleri gerçekleştirmek için Azure CLı 'yi kullanın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103637"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Azure CLı kullanarak Azure Data Lake Storage 1. kullanmaya başlama

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Azure Data Lake Storage 1. hesabı oluşturmak ve klasör oluşturma, veri dosyalarını karşıya yükleme ve indirme, hesabınızı silme gibi temel işlemleri gerçekleştirmek için Azure CLı 'nın nasıl kullanılacağını öğrenin. Data Lake Storage 1. hakkında daha fazla bilgi için bkz. [Data Lake Storage 1. genel bakış](data-lake-store-overview.md).

Azure CLI, Azure kaynaklarını yönetmek için Azure tarafından sunulan komut satırı deneyimidir. MacOS, Linux ve Windows’da kullanılabilir. Daha fazla bilgi için bkz. [Azure CLI 'Ya genel bakış](/cli/azure). Komutların ve sözdiziminin tüm listesi için [Azure Data Lake Storage 1. CLI başvurusuna](/cli/azure/dls) da bakabilirsiniz.


## <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce aşağıdakilere sahip olmanız ve aşağıdaki işlemleri yapmış olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** -yönergeler için bkz. [Azure CLI 'yı yüklemeye](/cli/azure/install-azure-cli) .

## <a name="authentication"></a>Kimlik Doğrulaması

Bu makalede, Son Kullanıcı kullanıcısı olarak oturum açtığınızda Data Lake Storage 1. daha basit bir kimlik doğrulama yaklaşımı kullanılmaktadır. Data Lake Storage 1. hesabının ve dosya sisteminin erişim düzeyi, oturum açmış kullanıcının erişim düzeyine göre yönetilir. Ancak, **Son Kullanıcı kimlik doğrulaması** veya **hizmetten hizmete kimlik doğrulama** olan Data Lake Storage 1. kimlik doğrulaması için başka yaklaşımlar de vardır. Kimlik doğrulaması gerçekleştirmeyle ilgili yönergeler ve daha fazla bilgi için [Son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md) veya [Hizmetten hizmete kimlik doğrulaması](./data-lake-store-service-to-service-authenticate-using-active-directory.md) bölümlerine göz atın.


## <a name="log-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

1. Azure aboneliğinizde oturum açın.

    ```azurecli
    az login
    ```

    Sonraki adımda kullanmak üzere bir kod alırsınız. https://aka.ms/devicelogin sayfasını açmak için bir web tarayıcısı kullanın ve kimlik doğrulaması yapmak için kodu girin. Kimlik bilgilerinizi kullanarak oturum açmanız istenir.

2. Oturum açtığınızda, pencerede hesabınızla ilişkili tüm Azure abonelikleri listelenir. Belirli bir aboneliği kullanmak için aşağıdaki komutu kullanın.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage 1. hesabı oluşturma

1. Yeni bir kaynak grubu oluşturma. Aşağıdaki komut içinde kullanmak istediğiniz parametre değerlerini sağlayın. Konum adı boşluk içeriyorsa adı tırnak işaretleri içine alın. Örneğin, "Doğu ABD 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Data Lake Storage 1. hesabı oluşturun.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesapta klasör oluşturma

Azure Data Lake Storage 1. hesabınızda, verileri yönetmek ve depolamak için klasörler oluşturabilirsiniz. Data Lake Storage 1. hesabının kökünde **myNewFolder** adlı bir klasör oluşturmak için aşağıdaki komutu kullanın.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` parametresi, komutun bir klasör oluşturmasını sağlar. Bu parametre yoksa, komut Data Lake Storage 1. hesabının kökünde myNewFolder adlı boş bir dosya oluşturur.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabına veri yükleme

Data Lake Storage 1. verileri doğrudan kök düzeyinde veya hesap içinde oluşturduğunuz bir klasöre yükleyebilirsiniz. Aşağıdaki kod parçacıkları, birtakım örnek verilerin önceki bölümde oluşturduğunuz klasöre (**mynewfolder**) nasıl yükleneceğini göstermektedir.

Karşıya yüklenecek örnek veri arıyorsanız [Azure Data Lake Git Deposu](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)'ndan **Ambulance Data** klasörünü alabilirsiniz. Dosyayı indirin ve bilgisayarınızda C:\sampledata\ gibi yerel bir dizinde depolayın.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Hedef için dosya adı dahil olmak üzere yolun tamamını belirtmeniz gerekir.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabındaki dosyaları listeleme

Bir Data Lake Storage 1. hesabındaki dosyaları listelemek için aşağıdaki komutu kullanın.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Bunun çıktısının aşağıdakine benzer olması gerekir:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabından verileri yeniden adlandırma, indirme ve silme 

* **Bir dosyayı yeniden adlandırmak için** aşağıdaki komutu kullanın:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Bir dosyayı Indirmek için** aşağıdaki komutu kullanın. Belirttiğiniz hedef yolun önceden var olduğundan emin olun.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Bu komut, henüz mevcut değilse hedef klasörü oluşturur.
    > 
    >

* **Bir dosyayı silmek için** aşağıdaki komutu kullanın:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Hem **mynewfolder** klasörünü hem de **vehicle1_09142014_copy.csv** dosyasını tek bir komutla silmek istiyorsanız --recurse parametresini kullanın

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesap için izinlerle ve ACL 'lerle çalışma

Bu bölümde, Azure CLı kullanarak ACL 'Leri ve izinleri yönetme hakkında bilgi edineceksiniz. ACL 'Lerin Azure Data Lake Storage 1. nasıl uygulandığı hakkında ayrıntılı bir tartışma için bkz. [Azure Data Lake Storage 1. denetim erişimi](data-lake-store-access-control.md).

* **Bir dosya veya klasörün sahibini güncelleştirmek için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Bir dosya veya klasörün izinlerini güncelleştirmek için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Belirli bir yolun ACL’lerini almak için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Çıktının aşağıdakine benzer olması gerekir:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* **ACL’ye yönelik bir giriş ayarlamak için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **ACL’ye yönelik bir girişi kaldırmak için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Varsayılan ACL’nin tamamını kaldırmak için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Varsayılan olmayan bir ACL’nin tamamını kaldırmak için** aşağıdaki komutu kullanın:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabı silme
Bir Data Lake Storage 1. hesabını silmek için aşağıdaki komutu kullanın.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

İstendiğinde, hesabı silmek için **Y** yazın.

## <a name="next-steps"></a>Sonraki adımlar
* [Büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma](data-lake-store-data-scenarios.md) 
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)