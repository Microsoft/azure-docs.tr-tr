---
title: Azure HDInsight 'ta Hadoop ile Data Lake Storage 1. kullanma
description: Azure Data Lake Storage 1. verileri sorgulamayı ve analizinizdeki sonuçları nasıl depolayacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 309af904f0dbfc0664c4341803cb6a4dc8a2c8a4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839278"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma

> [!Note]
> Gelişmiş performans ve yeni özellikler için [Azure Data Lake Storage 2.](hdinsight-hadoop-use-data-lake-storage-gen2.md) kullanarak yeni HDInsight kümeleri dağıtın.

HDInsight kümesindeki verileri çözümlemek için verileri [Azure depolama](../storage/common/storage-introduction.md)'da, [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)' e veya [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)' de saklayabilirsiniz. Tüm depolama seçenekleri, hesaplama için kullanılan HDInsight kümelerini Kullanıcı verilerini kaybetmeden güvenle silmenizi sağlar.

Bu makalede, Data Lake Storage 1. HDInsight kümeleri ile nasıl çalıştığını öğrenirsiniz. Azure Depolama’nın HDInsight kümeleriyle nasıl çalıştığı hakkında bilgi edinmek için bkz. [Azure Depolama’yı Azure HDInsight kümeleri ile kullanma](hdinsight-hadoop-use-blob-storage.md). HDInsight kümesi oluşturma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage 1. her zaman güvenli bir kanaldan erişilir, bu nedenle `adls` dosya sistemi Düzen adı yoktur. Her zaman `adl` kullanırsınız.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight kümeleri için kullanılabilirlik

Apache Hadoop, varsayılan dosya sisteminin bir kavramını destekler. Varsayılan dosya sistemi varsayılan şema ve yetkilisi anlamına gelir. Bu göreceli yolları çözümlemek için de kullanılabilir. HDInsight kümesi oluşturma işlemi sırasında Azure depolama 'da bir blob kapsayıcısını varsayılan dosya sistemi olarak belirtebilir veya HDInsight 3,5 ve daha yeni sürümlerde Azure Storage veya Azure Data Lake Storage 1. ile varsayılan dosya sistemi olarak seçebilirsiniz. birkaç özel durum.

HDInsight kümeleri, Data Lake Storage 1. iki şekilde kullanabilir:

* Varsayılan depolama alanı olarak
* Azure Depolama Blobunun varsayılan depolama alanı olduğu durumlarda ek depolama alanı olarak.

Şu andan itibaren, HDInsight küme türleri/sürümleri, varsayılan depolama ve ek depolama hesapları olarak Data Lake Storage 1. kullanmayı destekler:

| HDInsight küme türü | Varsayılan depolama alanı olarak Data Lake Storage 1. | Ek depolama alanı olarak Data Lake Storage 1.| Notlar |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight sürüm 4,0 | Hayır | Hayır |HDInsight 4,0 ADLS 1. desteklenmez |
| HDInsight sürümü 3.6 | Evet | Evet | HBase dışında|
| HDInsight sürümü 3.5 | Evet | Evet | HBase dışında|
| HDInsight sürümü 3.4 | Hayır | Evet | |
| HDInsight sürümü 3.3 | Hayır | Hayır | |
| HDInsight sürümü 3.2 | Hayır | Evet | |
| Storm | | |Data Lake Storage 1., bir fırtınası topolojisinden veri yazmak için kullanabilirsiniz. Daha sonra, bir fırtınası topolojisi tarafından okunabilen başvuru verileri için Data Lake Storage de kullanabilirsiniz.|

> [!WARNING]  
> HDInsight HBase Azure Data Lake Storage 1. ile desteklenmiyor

Ek depolama hesabı olarak Data Lake Storage 1. kullanmak, performansı veya kümeden Azure depolama alanını okuma veya yazma özelliğini etkilemez.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage 1. varsayılan depolama olarak kullan

HDInsight, varsayılan depolama alanı olarak Data Lake Storage 1. dağıtıldığında, küme ile ilgili dosyalar `adl://mydatalakestore/<cluster_root_path>/`depolanır; burada `<cluster_root_path>` Data Lake Storage içinde oluşturduğunuz klasörün adıdır. Her küme için bir kök yolu belirterek, birden fazla küme için aynı Data Lake Storage hesabını kullanabilirsiniz. Bunu yaptığınızda şöyle bir durum olabilir:

* Cluster1 `adl://mydatalakestore/cluster1storage` yolunu kullanabilir.
* Cluster2 `adl://mydatalakestore/cluster2storage` yolunu kullanabilir.

Her iki küme de aynı Data Lake Storage 1. hesabını kullanır **mydatalakestore**. Her kümenin, Data Lake Storage içinde kendi kök dosya sistemine erişimi vardır. Özellikle Azure portalı dağıtımı deneyimi sizden kök yol olarak **/clusters/\<clustername>** gibi bir klasör adı kullanmanızı ister.

Data Lake Storage 1. varsayılan depolama alanı olarak kullanabilmek için, hizmet sorumlusu erişimini aşağıdaki yollara vermeniz gerekir:

* Data Lake Storage 1. hesabı kökü.  Örneğin: adl://mydatalakestore/.
* Tüm küme klasörlerine yönelik klasör.  Örneğin: adl://mydatalakestore/clusters.
* Kümenin klasörü.  Örneğin: adl://mydatalakestore/clusters/cluster1storage.

Hizmet sorumlusu oluşturma ve erişim verme hakkında daha fazla bilgi için bkz. Configure Data Lake Storage Access.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Küme oluşturmada kullanılmak üzere Azure Keykasasından bir sertifika ayıklama

Yeni bir küme için varsayılan depolama alanı olarak Azure Data Lake Storage 1. ayarlamak istiyorsanız ve hizmet sorumlusuna yönelik sertifika Azure Key Vault ' de depolanıyorsa, sertifikayı doğru biçime dönüştürmek için gereken birkaç ek adım vardır. Aşağıdaki kod parçacıkları, dönüştürmenin nasıl gerçekleştirileceğini gösterir.

İlk olarak, Key Vault sertifikayı indirin ve `SecretValueText`ayıklayın.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Sonra `SecretValueText` bir sertifikaya dönüştürün.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Ardından, aşağıdaki kod parçacığında olduğu gibi yeni bir küme dağıtmak için `$identityCertificate` kullanabilirsiniz:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Ek depolama alanı olarak Data Lake Storage 1. kullan

Data Lake Storage 1., küme için ek depolama alanı olarak da kullanabilirsiniz. Bu gibi durumlarda, küme varsayılan depolaması bir Azure Depolama Blobu ya da Data Lake Storage hesabı olabilir. HDInsight işlerini ek depolama alanı olarak Data Lake Storage depolanan verilere karşı çalıştırıyorsanız, dosyaların tam yolunu kullanmanız gerekir. Örneğin:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Artık URL'de **cluster_root_path** olmadığını unutmayın. Bunun nedeni, bu durumda Data Lake Storage bir varsayılan depolama olmadığından, tüm yapmanız gereken dosyaların yolunu sağlamaktır.

Bir Data Lake Storage 1. ek depolama alanı olarak kullanabilmek için, yalnızca Dosyalarınızın depolandığı yollara hizmet sorumlusu erişimi vermeniz gerekir.  Örneğin:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Hizmet sorumlusu oluşturma ve erişim verme hakkında daha fazla bilgi için bkz. Configure Data Lake Storage Access.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Birden fazla Data Lake Storage hesabı kullan

Bir Data Lake Storage hesabını ek olarak eklemek ve birden fazla Data Lake Storage hesabı eklemek, bir veya daha fazla Data Lake Storage hesabında veri üzerinde HDInsight kümesi izni vererek gerçekleştirilir. Bkz. Data Lake Storage erişimi yapılandırma.

## <a name="configure-data-lake-storage-access"></a>Data Lake Storage erişimini yapılandırma

HDInsight kümenizdeki Data Lake Storage erişimi yapılandırmak için bir Azure Active Directory (Azure AD) hizmet sorumlusuna sahip olmanız gerekir. Hizmet sorumlusu yalnızca bir Azure AD yöneticisi tarafından oluşturulabilir. Hizmet sorumlusunun bir sertifika ile oluşturulması gerekir. Daha fazla bilgi edinmek için bkz. [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) ve [Otomatik olarak imzalanan sertifika ile hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> HDInsight kümesi için ek depolama alanı olarak Azure Data Lake Storage 1. kullanacaksanız, bu makalede açıklandığı gibi kümeyi oluştururken bunu yapmanızı önemle öneririz. Mevcut bir HDInsight kümesine ek depolama alanı olarak Azure Data Lake Storage 1. eklemek, desteklenen bir senaryo değildir.

## <a name="access-files-from-the-cluster"></a>Kümeden dosyalara erişme

HDInsight kümesinden Data Lake Storage dosyalara erişmek için çeşitli yollar vardır.

* **Tam adı kullanarak**. Bu yöntemle, erişmek istediğiniz dosyanın tam yolunu girersiniz.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Kısaltılmış yol biçimi kullanarak**. Bu yaklaşımda, yolu küme köküne kadar değiştirirsiniz:

    ```
    adl:///<file path>
    ```

* **Göreli yolu kullanarak**. Bu yöntemle, erişmek istediğiniz dosyanın yalnızca göreli yolunu girersiniz.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Veri erişimi örnekleri

Örnekler, kümenin baş düğümüne bir [ssh bağlantısını](./hdinsight-hadoop-linux-use-ssh-unix.md) temel alır. Örneklerde, üç URI şeması da kullanılır. `DATALAKEACCOUNT` ve `CLUSTERNAME` ilgili değerleri ile değiştirin.

#### <a name="a-few-hdfs-commands"></a>Birkaç bir bu komut

1. Yerel depolamada basit bir dosya oluşturun.

    ```bash
    touch testFile.txt
    ```

1. Küme depolamada dizinler oluşturun.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Yerel depolamadan küme depolamaya veri kopyalama.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Küme depolamada dizin içeriğini listeleyin.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive tablosu oluşturma

Üç dosya konumu tanım amacıyla gösterilmiştir. Gerçek yürütme için `LOCATION` girdilerden yalnızca birini kullanın.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-abmari"></a>Abmarı 'den depolama yolunu tanımla

Yapılandırılmış varsayılan deponun tüm yolunu belirlemek için, > **config** 'ler ' **e gidin ve** filtre girişi kutusuna `fs.defaultFS` girin.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma

Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma hakkında ayrıntılı yönergeler için aşağıdaki bağlantıları kullanın.

* [Portal’ı kullanma](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [PowerShell 'i kullanma (Data Lake Storage 1. varsayılan depolama alanı olarak)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell 'i kullanma (Data Lake Storage 1. ek depolama alanı olarak)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure şablonlarını kullanma](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Data Lake Storage 1. erişim için HDInsight sertifikasını yenileme

Aşağıdaki örnek PowerShell kodu bir sertifikayı yerel bir dosyadan veya Azure Key Vault okur ve HDInsight kümenizi, Azure Data Lake Storage 1. erişmek için yeni sertifikayla güncelleştirir. Kendi HDInsight küme adınızı, kaynak grubu adını, abonelik KIMLIĞINI, uygulama KIMLIĞINI, sertifikaya yönelik yerel yolu belirtin. İstendiğinde parolayı yazın.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bkz. HDInsight ile uyumlu Azure Data Lake Storage 1. kullanmayı öğrendiniz. Bu, ölçeklenebilir, uzun vadeli, arşivlemeli veri edinme çözümleri oluşturmanıza ve depolanan yapılandırılmış ve yapılandırılmamış verilerdeki bilgilerin kilidini açmak için HDInsight kullanmanıza olanak sağlar.

Daha fazla bilgi için bkz.

* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Azure PowerShell kullanarak Data Lake Storage 1. kullanmak için HDInsight kümesi oluşturma](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [HDInsight 'a veri yükleme](hdinsight-upload-data.md)
* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile verilere erişimi kısıtlamak için Azure depolama paylaşılan erişim Imzalarını kullanma](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Öğretici: Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
