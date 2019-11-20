---
title: Azure Data Lake Storage 1.-PowerShell ile çalışmaya başlama | Microsoft Docs
description: Bir Azure Data Lake Storage 1. hesabı oluşturmak ve temel işlemleri gerçekleştirmek için Azure PowerShell kullanın.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837887"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Azure PowerShell kullanarak Azure Data Lake Storage 1. kullanmaya başlama

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Bir Azure Data Lake Storage 1. hesabı oluşturmak ve klasör oluşturma, veri dosyalarını karşıya yükleme ve indirme, hesabınızı silme gibi temel işlemleri gerçekleştirmek için Azure PowerShell nasıl kullanacağınızı öğrenin. Data Lake Storage 1. hakkında daha fazla bilgi için bkz. [Data Lake Storage 1. genel bakış](data-lake-store-overview.md).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 veya üstü**. Bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview).

## <a name="authentication"></a>Kimlik Doğrulaması

Bu makalede, Azure hesabı kimlik bilgilerinizi girmeniz istendiğinde Data Lake Storage 1. daha basit bir kimlik doğrulama yaklaşımı kullanılmaktadır. Data Lake Storage 1. hesaba ve dosya sistemine yönelik erişim düzeyi, oturum açmış kullanıcının erişim düzeyine göre yönetilir. Ancak, son kullanıcı kimlik doğrulaması veya hizmetten hizmete kimlik doğrulama olan Data Lake Storage 1. kimlik doğrulaması için başka yaklaşımlar vardır. Kimlik doğrulaması gerçekleştirmeyle ilgili yönergeler ve daha fazla bilgi için [Son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md) veya [Hizmetten hizmete kimlik doğrulaması](data-lake-store-authenticate-using-active-directory.md) bölümlerine göz atın.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabı oluşturma

1. Masaüstünüzde yeni bir Windows PowerShell penceresi açın. Azure hesabınızda oturum açmak, aboneliği ayarlamak ve Data Lake Storage 1. sağlayıcıyı kaydetmek için aşağıdaki kod parçacığını girin. Oturum açmanız istendiğinde, abonelik yöneticileri/sahibinden biri olarak oturum çalıştırdığınızdan emin olun:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Bir Data Lake Storage 1. hesabı bir Azure Kaynak grubuyla ilişkilendirilir. Bir kaynak grubu oluşturarak başlayın.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Azure Kaynak grubu oluşturma](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure Kaynak Grubu oluşturma")

1. Data Lake Storage 1. hesabı oluşturun. Belirttiğiniz ad yalnızca küçük harflerden ve rakamlardan oluşmalıdır.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Data Lake Storage 1. hesabı oluşturma](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Data Lake Storage 1. hesabı oluşturma")

1. Hesabın başarıyla oluşturulduğunu doğrulayın.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    Bu cmdlet'in çıktısı **True** olmalıdır.

## <a name="create-directory-structures"></a>Dizin yapıları oluşturma

Verileri yönetmek ve depolamak için Data Lake Storage 1. hesabınızın altında dizinler oluşturabilirsiniz.

1. Bir kök dizin belirtin.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Belirtilen kökün altında **mynewdirectory** adlı yeni bir dizin oluşturun.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Yeni dizinin başarıyla oluşturulduğunu doğrulayın.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Aşağıdaki ekran görüntüsünde gösterildiği gibi bir çıkış göstermelidir:

    ![Dizini doğrula](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Dizini Doğrulama")

## <a name="upload-data"></a>Karşıya veri yükleme

Verilerinizi doğrudan kök düzeyinde veya hesap içinde oluşturduğunuz bir dizine Data Lake Storage 1. yükleyebilirsiniz. Bu bölümdeki kod parçacıkları, birtakım örnek verilerin önceki bölümde oluşturduğunuz dizine (**mynewdirectory**) nasıl yükleneceğini göstermektedir.

Karşıya yüklenecek örnek veri arıyorsanız **Azure Data Lake Git Deposu**'ndan [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) klasörünü alabilirsiniz. Dosyayı indirin ve bilgisayarınızda C:\sampledata\ gibi yerel bir dizinde depolayın.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Verileri yeniden adlandırma, indirme ve silme

Bir dosyayı yeniden adlandırmak için aşağıdaki komutu kullanın:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Bir dosyayı indirmek için aşağıdaki komutu kullanın.

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Bir dosyayı silmek için aşağıdaki komutu kullanın:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

İstendiğinde, öğeyi silmek için **Y** yazın. Birden fazla dosyayı silmek istiyorsanız tüm yolları virgülle ayrılmış olarak sağlayabilirsiniz.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Hesabınızı silme

Data Lake Storage 1. hesabınızı silmek için aşağıdaki komutu kullanın.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

İstendiğinde, hesabı silmek için **Y** yazın.

## <a name="next-steps"></a>Sonraki adımlar

* [PowerShell 'i Azure Data Lake Storage 1. kullanmaya yönelik performans ayarlama Kılavuzu](data-lake-store-performance-tuning-powershell.md)
* [Büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma](data-lake-store-data-scenarios.md)
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
