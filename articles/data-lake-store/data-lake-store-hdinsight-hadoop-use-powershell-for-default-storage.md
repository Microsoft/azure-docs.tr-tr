---
title: PowerShell-Azure Data Lake Storage 1. ile HDInsight kümesi
description: Azure Data Lake Storage 1. ile Azure HDInsight kümelerini oluşturmak ve kullanmak için Azure PowerShell kullanın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606785"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>PowerShell kullanarak Azure Data Lake Storage 1. varsayılan depolama alanı olarak HDInsight kümeleri oluşturma

> [!div class="op_single_selector"]
> * [Azure portal kullanın](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell kullanma (varsayılan depolama için)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell kullanma (ek depolama için)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Kaynak Yöneticisi kullan](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Azure HDInsight kümelerini varsayılan depolama alanı olarak Azure Data Lake Storage 1. ile yapılandırmak için Azure PowerShell nasıl kullanacağınızı öğrenin. Ek depolama alanı olarak Data Lake Storage 1. HDInsight kümesi oluşturma yönergeleri için bkz. [ek depolama alanı olarak Data Lake Storage 1. Ile HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-powershell.md).

HDInsight 'ı Data Lake Storage 1. ile kullanmayla ilgili bazı önemli noktalar şunlardır:

* HDInsight sürüm 3,5 ve 3,6 ' de varsayılan depolama alanı olarak Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma seçeneği vardır.

* HDInsight Premium kümelerinde varsayılan depolama Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma *seçeneği yoktur.*

HDInsight 'ı PowerShell kullanarak Data Lake Storage 1. çalışacak şekilde yapılandırmak için sonraki beş bölümde yer alarak bulunan yönergeleri izleyin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiye başlamadan önce, aşağıdaki gereksinimleri karşıladığınızdan emin olun:

* **Azure aboneliği**: [Azure Ücretsiz deneme sürümünü kullanmaya başlayın](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 veya üzeri**: [PowerShell 'i yüklemek ve yapılandırmak için](/powershell/azure/)bkz..
* **Windows yazılım geliştirme seti (SDK)**: Windows SDK yüklemek Için, [Windows 10 için indirmeler ve araçlar](https://dev.windows.com/downloads)'a gidin. SDK, bir güvenlik sertifikası oluşturmak için kullanılır.
* **Hizmet sorumlusu Azure Active Directory**: Bu öğretici, Azure Active Directory (Azure AD) içinde hizmet sorumlusu oluşturma işlemini açıklar. Bununla birlikte, bir hizmet sorumlusu oluşturmak için bir Azure AD yöneticisi olmanız gerekir. Yöneticiyseniz, bu önkoşulu atlayıp öğreticiye devam edebilirsiniz.

    >[!NOTE]
    >Yalnızca bir Azure AD yöneticisiyseniz bir hizmet sorumlusu oluşturabilirsiniz. Data Lake Storage 1. ile bir HDInsight kümesi oluşturabilmeniz için Azure AD yöneticinizin bir hizmet sorumlusu oluşturması gerekir. Hizmet sorumlusu, [sertifikayla birlikte hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)bölümünde açıklandığı gibi bir sertifikayla oluşturulmalıdır.
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage 1. hesabı oluşturma

Data Lake Storage 1. bir hesap oluşturmak için aşağıdakileri yapın:

1. Masaüstünüzde bir PowerShell penceresi açın ve ardından aşağıdaki kod parçacıklarını girin. Oturum açmanız istendiğinde, abonelik yöneticileri veya sahiplerinden biri olarak oturum açın. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Data Lake Storage 1. kaynak sağlayıcısını kaydeder ve şuna benzer bir hata alırsanız `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , aboneliğiniz Data Lake Storage 1. onaylanmayabilir. Data Lake Storage 1. için Azure aboneliğinizi etkinleştirmek üzere [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)bölümündeki yönergeleri izleyin.
    >

2. Bir Data Lake Storage 1. hesabı bir Azure Kaynak grubuyla ilişkilendirilir. Bir kaynak grubu oluşturarak başlayın.

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

3. Data Lake Storage 1. hesabı oluşturun. Belirttiğiniz hesap adı yalnızca küçük harf ve rakam içermelidir.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Varsayılan depolama alanı olarak Data Lake Storage 1. kullanmak için küme oluşturma sırasında kümeye özgü dosyaların kopyalandığı bir kök yol belirtmeniz gerekir. Kod parçacığında **/Clusters/hdiadlcluster** olan bir kök yol oluşturmak için aşağıdaki cmdlet 'leri kullanın:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Data Lake Storage 1. için rol tabanlı erişim için kimlik doğrulamasını ayarlama
Her Azure aboneliği bir Azure AD varlığı ile ilişkilendirilir. Azure portal veya Azure Resource Manager API 'sini kullanarak abonelik kaynaklarına erişen kullanıcı ve hizmetler öncelikle Azure AD ile kimlik doğrulaması yapılmalıdır. Azure abonelikleri ve Hizmetleri 'ne, Azure kaynağına uygun rol atanarak erişim verilir. Hizmet sorumlusu, hizmetler için Azure AD 'de hizmeti tanımlar.

Bu bölümde, HDInsight gibi bir uygulama hizmetinin bir Azure kaynağına erişimi (daha önce oluşturduğunuz Data Lake Storage 1. hesabı) nasıl vereceğiniz gösterilmektedir. Bunu, uygulama için bir hizmet sorumlusu oluşturup PowerShell aracılığıyla ona roller atayarak yapabilirsiniz.

Data Lake Storage 1. için Active Directory kimlik doğrulaması ayarlamak için aşağıdaki iki bölümde bulunan görevleri gerçekleştirin.

### <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma
Bu bölümdeki adımlarla devam etmeden önce [Windows SDK](https://dev.windows.com/en-us/downloads) yüklü olduğundan emin olun. Ayrıca, sertifikayı oluşturduğunuz *C:\mycertdir* gibi bir dizin oluşturmuş olmanız gerekir.

1. PowerShell penceresinden Windows SDK yüklediğiniz konuma gidin (genellikle, *C:\Program Files (x86) \Windows Kits\10\bin\x86*) ve otomatik olarak imzalanan bir sertifika ve özel anahtar oluşturmak için [MakeCert][makecert] yardımcı programını kullanın. Aşağıdaki komutları kullanın:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Özel anahtar parolasını girmeniz istenir. Komut başarıyla yürütüldükten sonra, belirttiğiniz sertifika dizininde **SertifikaDosyası. cer** ve **MyKey. PVK** görmeniz gerekir.
2. MakeCert tarafından oluşturulan. PVK ve. cer dosyalarını bir. pfx dosyasına dönüştürmek için [Pvk2pfx][pvk2pfx] yardımcı programını kullanın. Şu komutu çalıştırın:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    İstendiğinde, daha önce belirttiğiniz özel anahtar parolasını girin. **-Po** parametresi için belirttiğiniz değer,. pfx dosyasıyla ilişkili paroladır. Komut başarıyla tamamlandıktan sonra, belirttiğiniz sertifika dizininde bir **SertifikaDosyası. pfx** de görmeniz gerekir.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Azure AD ve hizmet sorumlusu oluşturma
Bu bölümde, bir Azure AD uygulaması için hizmet sorumlusu oluşturur, hizmet sorumlusuna bir rol atayabilir ve bir sertifika sağlayarak hizmet sorumlusu olarak kimlik doğrulaması yapabilirsiniz. Azure AD 'de bir uygulama oluşturmak için aşağıdaki komutları çalıştırın:

1. Aşağıdaki cmdlet 'leri PowerShell konsol penceresine yapıştırın. **DisplayName** özelliği için belirttiğiniz değerin benzersiz olduğundan emin olun. **-Giriş sayfası** ve **-tanımlayıcı** ile ilgili değerler yer tutucu değerlerdir ve doğrulanmaz.

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

3. Data Lake Storage 1. köküne ve daha önce belirttiğiniz kök yolundaki tüm klasörlere hizmet sorumlusu erişimi verin. Aşağıdaki cmdlet 'leri kullanın:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Varsayılan depolama alanı olarak Data Lake Storage 1. bir HDInsight Linux kümesi oluşturma

Bu bölümde, varsayılan depolama alanı olarak Data Lake Storage 1. bir HDInsight Hadoop Linux kümesi oluşturacaksınız. Bu sürümde, HDInsight kümesi ve Data Lake Storage 1. aynı konumda olmalıdır.

1. Abonelik kiracı KIMLIĞINI alın ve daha sonra kullanmak üzere saklayın.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Aşağıdaki cmdlet 'leri kullanarak HDInsight kümesini oluşturun:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Cmdlet başarıyla tamamlandıktan sonra, küme ayrıntılarını listeleyen bir çıktı görmeniz gerekir.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Data Lake Storage 1. kullanmak için HDInsight kümesinde test işleri çalıştırın
Bir HDInsight kümesini yapılandırdıktan sonra, Data Lake Storage 1. erişebildiğinden emin olmak için test işlerini üzerinde çalıştırabilirsiniz. Bunu yapmak için, *\<cluster root> /example/Data/Sample.log* konumundaki Data Lake Storage 1. ' de zaten bulunan örnek verileri kullanan bir tablo oluşturmak Için örnek bir Hive işi çalıştırın.

Bu bölümde, oluşturduğunuz HDInsight Linux kümesine bir Secure Shell (SSH) bağlantısı yaparsınız ve sonra örnek bir Hive sorgusu çalıştırırsınız.

* Kümeye SSH bağlantısı oluşturmak için bir Windows istemcisi kullanıyorsanız, bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Kümeye SSH bağlantısı oluşturmak için bir Linux istemcisi kullanıyorsanız, bkz. [Linux 'Tan HDInsight 'ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Bağlantıyı yaptıktan sonra, aşağıdaki komutu kullanarak Hive komut satırı arabirimini (CLı) başlatın:

    ```powershell
    hive
    ```

2. Data Lake Storage 1. ' deki örnek verileri kullanarak **Araçlar** adlı yeni bir tablo oluşturmak için aşağıdaki deyimleri gırmek üzere CLI 'yi kullanın:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Sorgu çıkışını SSH konsolunda görmeniz gerekir.

>[!NOTE]
>Yukarıdaki CREATE TABLE komutundaki örnek verilerin yolu `adl:///example/data/` , burada `adl:///` küme köküdür. Bu öğreticide belirtilen küme köküne örnek olarak, komut olur `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Daha kısa bir alternatif kullanabilir ya da küme köküne yönelik yolun tamamını sağlayabilirsiniz.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Data Lake Storage 1., bu komutları kullanarak erişim
HDInsight kümesini Data Lake Storage 1. kullanacak şekilde yapılandırdıktan sonra, mağazaya erişmek için Hadoop Dağıtılmış Dosya Sistemi (,) kabuk komutlarını kullanabilirsiniz.

Bu bölümde, oluşturduğunuz HDInsight Linux kümesine bir SSH bağlantısı yaparsınız ve sonra, bu komutu çalıştırırsınız.

* Kümeye SSH bağlantısı oluşturmak için bir Windows istemcisi kullanıyorsanız, bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Kümeye SSH bağlantısı oluşturmak için bir Linux istemcisi kullanıyorsanız, bkz. [Linux 'Tan HDInsight 'ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Bağlantıyı yaptıktan sonra, aşağıdaki bir dosya sistemi komutunu kullanarak Data Lake Storage 1. dosyaları listeleyin.

```azurepowershell
hdfs dfs -ls adl:///
```

Ayrıca, `hdfs dfs -put` Data Lake Storage 1. ' ye bazı dosyaları yüklemek için komutunu kullanabilir ve ardından `hdfs dfs -ls` dosyaların başarıyla karşıya yüklenip yüklenmediğini doğrulamak için kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure portal: kullanmak için HDInsight kümesi oluşturma Data Lake Storage 1.](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx