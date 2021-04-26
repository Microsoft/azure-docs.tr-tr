---
title: PowerShell-Data Lake Storage 1.-eklenti depolama ile HDInsight-Azure
description: Ek depolama alanı olarak Azure Data Lake Storage 1. bir HDInsight kümesini yapılandırmak için Azure PowerShell nasıl kullanacağınızı öğrenin.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b7cb9d5c5c2ca850678d3f3194a9af8de526ada4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103399"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Azure Data Lake Storage 1. ile bir HDInsight kümesi oluşturmak için Azure PowerShell kullanma (ek depolama olarak)

> [!div class="op_single_selector"]
> * [Portalı kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 'i kullanma (varsayılan depolama için)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 'i kullanma (ek depolama için)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Kaynak Yöneticisi kullanma](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

**Ek depolama alanı olarak** Azure Data Lake Storage 1. Ile bir HDInsight kümesini yapılandırmak için Azure PowerShell nasıl kullanacağınızı öğrenin. Varsayılan depolama alanı olarak Data Lake Storage 1. HDInsight kümesi oluşturma hakkında yönergeler için, bkz. [varsayılan depolama alanı olarak Data Lake Storage 1. Ile HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> HDInsight kümesi için ek depolama alanı olarak Data Lake Storage 1. kullanacaksanız, bu makalede açıklandığı gibi kümeyi oluştururken bunu yapmanızı önemle öneririz. Mevcut bir HDInsight kümesine ek depolama alanı olarak Data Lake Storage 1. eklemek karmaşık bir işlemdir ve hatalara açıktır.
>

Desteklenen küme türleri için Data Lake Storage 1. varsayılan depolama veya ek depolama hesabı olarak kullanılabilir. Data Lake Storage 1. ek depolama alanı olarak kullanıldığında, kümeler için varsayılan depolama hesabı hala Azure Blob depolama (işb) olacaktır ve kümeyle ilgili dosyalar (örneğin Günlükler vb.) varsayılan depolama alanına yazılır, ancak işlemek istediğiniz veriler bir Data Lake Storage 1. depolanabilir. Ek depolama hesabı olarak Data Lake Storage 1. kullanmak, performansı veya kümeden depolama alanını okuma/yazma özelliğini etkilemez.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight küme depolaması için Data Lake Storage 1. kullanma

HDInsight 'ı Data Lake Storage 1. ile kullanmayla ilgili bazı önemli noktalar şunlardır:

* HDInsight sürümleri 3,2, 3,4, 3,5 ve 3,6 sürümleri için ek depolama alanı olarak Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma seçeneği.

HDInsight 'ı PowerShell kullanarak Data Lake Storage 1. çalışacak şekilde yapılandırmak aşağıdaki adımları içerir:

* Data Lake Storage 1. Nesil hesabı oluşturma 
* Data Lake Storage 1. için rol tabanlı erişim için kimlik doğrulamasını ayarlama
* Data Lake Storage 1. kimlik doğrulamasıyla HDInsight kümesi oluşturma
* Kümede bir test işi çalıştırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 veya üstü**. Bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/).
* **Windows SDK**. [Buradan](https://dev.windows.com/en-us/downloads) yükleyebilirsiniz. Bunu bir güvenlik sertifikası oluşturmak için kullanırsınız.
* **Hizmet sorumlusu Azure Active Directory**. Bu öğreticideki adımlarda, Azure AD 'de hizmet sorumlusu oluşturma hakkında yönergeler sağlanmaktadır. Ancak, bir hizmet sorumlusu oluşturabilmeniz için bir Azure AD yöneticisi olmanız gerekir. Bir Azure AD yöneticisiyseniz, bu önkoşulu atlayıp öğreticiye devam edebilirsiniz.

    **Bir Azure AD yöneticisi** değilseniz, hizmet sorumlusu oluşturmak için gereken adımları gerçekleştiremezsiniz. Böyle bir durumda, Data Lake Storage 1. bir HDInsight kümesi oluşturabilmeniz için önce Azure AD yöneticinizin bir hizmet sorumlusu oluşturması gerekir. Ayrıca hizmet sorumlusu, [sertifikayla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)bölümünde açıklandığı gibi bir sertifika kullanılarak oluşturulmalıdır.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. Nesil hesabı oluşturma 
Data Lake Storage 1. hesabı oluşturmak için bu adımları izleyin.

1. Masaüstünüzde yeni bir Azure PowerShell penceresi açın ve aşağıdaki kod parçacığını girin. Oturum açmanız istendiğinde, abonelik Yöneticisi/sahibinin bir üyesi olarak oturum seçtiğinizden emin olun:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Data Lake Storage 1. kaynak sağlayıcısını kaydederken buna benzer bir hata alırsanız `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , aboneliğiniz Data Lake Storage 1. için onaylanmamış olabilir. Bu [yönergeleri](data-lake-store-get-started-portal.md)izleyerek Data Lake Storage 1. için Azure aboneliğinizi etkinleştirdiğinizden emin olun.
   >
   >
2. Data Lake Storage 1. olan bir depolama hesabı bir Azure Kaynak grubuyla ilişkilendirilir. Azure Kaynak Grubu oluşturma işlemiyle başlayın.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Şunun gibi bir çıktı görmeniz gerekir:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Data Lake Storage 1. bir depolama hesabı oluşturun. Belirttiğiniz hesap adı yalnızca küçük harf ve rakam içermelidir.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Aşağıdaki gibi bir çıktı görmeniz gerekir:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Data Lake Storage 1. örnek verileri karşıya yükleyin. Bu makalenin ilerleyen kısımlarında, verileri bir HDInsight kümesinden erişilebilir olduğunu doğrulamak için kullanacağız. Karşıya yüklenecek örnek veri arıyorsanız [Azure Data Lake Git Deposu](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)'ndan **Ambulance Data** klasörünü alabilirsiniz.

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Data Lake Storage 1. için rol tabanlı erişim için kimlik doğrulamasını ayarlama

Her Azure aboneliği bir Azure Active Directory ilişkilendirilir. Azure portal veya Azure Resource Manager API 'sini kullanarak aboneliğin kaynaklarına erişen kullanıcı ve hizmetler öncelikle bu Azure Active Directory kimlik doğrulamasından uymalıdır. Azure abonelikleri ve Hizmetleri 'ne, Azure kaynağına uygun rol atanarak erişim verilir.  Hizmetler için, hizmet sorumlusu Azure Active Directory (Azure AD) hizmetini tanımlar. Bu bölümde, uygulama için bir hizmet sorumlusu oluşturup Azure PowerShell aracılığıyla roller atayarak, HDInsight gibi bir uygulama hizmetinin (daha önce oluşturduğunuz Data Lake Storage 1. depolama hesabı) nasıl yapılacağı gösterilmektedir.

Data Lake Storage 1. için Active Directory kimlik doğrulaması ayarlamak için aşağıdaki görevleri gerçekleştirmeniz gerekir.

* Otomatik olarak imzalanan sertifika oluşturma
* Azure Active Directory ve hizmet sorumlusu 'nda uygulama oluşturma

### <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Bu bölümdeki adımlarla devam etmeden önce [Windows SDK](https://dev.windows.com/en-us/downloads) yüklü olduğundan emin olun. Ayrıca, sertifikanın oluşturulacağı **C:\mycertdir** gibi bir dizin de oluşturmuş olmanız gerekir.

1. PowerShell penceresinden Windows SDK yüklediğiniz konuma gidin (genellikle `C:\Program Files (x86)\Windows Kits\10\bin\x86` ve [MakeCert][makecert] yardımcı programını kullanarak otomatik olarak imzalanan bir sertifika ve özel anahtar oluşturun. Aşağıdaki komutları kullanın.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Özel anahtar parolasını girmeniz istenir. Komut başarıyla yürütüldükten sonra, belirttiğiniz sertifika dizininde bir **SertifikaDosyası. cer** ve **MyKey. PVK** görmeniz gerekir.
2. MakeCert tarafından oluşturulan. PVK ve. cer dosyalarını bir. pfx dosyasına dönüştürmek için [Pvk2pfx][pvk2pfx] yardımcı programını kullanın. Aşağıdaki komutu çalıştırın.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    İstendiğinde, daha önce belirttiğiniz özel anahtar parolasını girin. **-Po** parametresi için belirttiğiniz değer,. pfx dosyasıyla ilişkili paroladır. Komut başarıyla tamamlandıktan sonra, belirttiğiniz sertifika dizininde bir SertifikaDosyası. pfx de görmeniz gerekir.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory ve hizmet sorumlusu oluşturma

Bu bölümde, bir Azure Active Directory uygulaması için hizmet sorumlusu oluşturma, hizmet sorumlusuna rol atama ve bir sertifika sağlayarak hizmet sorumlusu olarak kimlik doğrulama adımlarını gerçekleştirirsiniz. Azure Active Directory bir uygulama oluşturmak için aşağıdaki komutları çalıştırın.

1. Aşağıdaki cmdlet 'leri PowerShell konsol penceresine yapıştırın. **-DisplayName** özelliği için belirttiğiniz değerin benzersiz olduğundan emin olun. Ayrıca **-giriş sayfası** ve **-ıdentıeruri** değerleri, yer tutucu değerlerdir ve doğrulanmaz.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Uygulama KIMLIĞINI kullanarak bir hizmet sorumlusu oluşturun.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Data Lake Storage 1. klasöre ve HDInsight kümesinden erişebileceğiniz dosyaya hizmet sorumlusu erişimi verin. Aşağıdaki kod parçacığı, Data Lake Storage 1. (örnek veri dosyasını kopyaladığınız) depolama hesabının köküne ve dosyanın kendisini erişim sağlar.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Ek depolama alanı olarak Data Lake Storage 1. bir HDInsight Linux kümesi oluşturma

Bu bölümde, ek depolama alanı olarak Data Lake Storage 1. bir HDInsight Hadoop Linux kümesi oluşturacağız. Bu sürümde, Data Lake Storage 1. HDInsight kümesi ve depolama hesabı aynı konumda olmalıdır.

1. Abonelik kiracı KIMLIĞINI almaya başlayın. Daha sonra ihtiyacınız olacak.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Bu sürümde, bir Hadoop kümesi için Data Lake Storage 1. yalnızca küme için ek depolama alanı olarak kullanılabilir. Varsayılan depolama alanı, Azure Blob depolama alanı (. GB) olmaya devam edecektir. Bu nedenle, önce küme için gereken depolama hesabını ve depolama kapsayıcılarını oluşturacağız.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. HDInsight kümesini oluşturun. Aşağıdaki cmdlet 'leri kullanın.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    Cmdlet başarıyla tamamlandıktan sonra, küme ayrıntılarının listelendiği bir çıktı görmeniz gerekir.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Data Lake Storage 1. kullanmak için HDInsight kümesinde test işleri çalıştırın
Bir HDInsight kümesini yapılandırdıktan sonra, HDInsight kümesinin Data Lake Storage 1. erişebileceğini sınamak için test işlerini kümede çalıştırabilirsiniz. Bunu yapmak için, daha önce Data Lake Storage 1. ile depolama hesabınıza yüklediğiniz örnek verileri kullanarak bir tablo oluşturan örnek bir Hive işi çalıştıracağız.

Bu bölümde, oluşturduğunuz HDInsight Linux kümesine SSH oluşturacak ve örnek Hive sorgusunu çalıştıracaksınız.

* Kümeye SSH için bir Windows istemcisi kullanıyorsanız, bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
* Kümeye SSH için bir Linux istemcisi kullanıyorsanız bkz. [Linux 'Tan HDInsight 'ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Bağlandıktan sonra, aşağıdaki komutu kullanarak Hive CLı 'yı başlatın:

    ```azurepowershell
    hive
    ```

2. CLı kullanarak, Data Lake Storage 1. ' deki örnek verileri kullanarak **Araçlar** adlı yeni bir tablo oluşturmak için aşağıdaki deyimlerini girin:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    ```output
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage 1., bu komutları kullanarak erişim
HDInsight kümesini Data Lake Storage 1. kullanacak şekilde yapılandırdıktan sonra, mağazaya erişmek için,

Bu bölümde, oluşturduğunuz HDInsight Linux kümesine SSH oluşturacak ve bu komutu çalıştıracaksınız.

* Kümeye SSH için bir Windows istemcisi kullanıyorsanız, bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
* Kümeye SSH için bir Linux istemcisi kullanıyorsanız bkz. [Linux 'Tan HDInsight 'ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Bağlandıktan sonra, Data Lake Storage 1. depolama hesabındaki dosyaları listelemek için aşağıdaki bir bu dosya sistemi komutunu kullanın.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Bu, daha önce karşıya yüklediğiniz dosyayı Data Lake Storage 1. olarak listelemelidir.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Ayrıca, `hdfs dfs -put` Data Lake Storage 1. ' ye bazı dosyaları yüklemek için komutunu kullanabilir ve ardından `hdfs dfs -ls` dosyaların başarıyla karşıya yüklenip yüklenmediğini doğrulamak için kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca Bkz.
* [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal: Data Lake Storage 1. kullanmak için HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx