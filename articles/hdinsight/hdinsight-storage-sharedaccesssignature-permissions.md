---
title: Paylaşılan erişim Imzalarını kullanarak erişimi kısıtlama-Azure HDInsight
description: Azure Blob depolama alanında depolanan verilere HDInsight erişimini kısıtlamak için paylaşılan erişim Imzalarını nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 647ac363dd81604f0a5a1d750d98a3349beb19c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931731"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>HDInsight 'taki verilere erişimi kısıtlamak için Azure Blob depolama paylaşılan erişim Imzalarını kullanma

HDInsight, kümeyle ilişkili Azure Blob depolama hesaplarında bulunan verilere tam erişim sağlar. Verilere erişimi kısıtlamak için blob kapsayıcısında paylaşılan erişim Imzalarını kullanabilirsiniz. Paylaşılan erişim Imzaları (SAS), Azure Blob depolama hesaplarının verilere erişimi sınırlandırmanıza olanak tanıyan bir özelliktir. Örneğin, verilere salt okunurdur erişim sağlama.

> [!IMPORTANT]  
> Apache Ranger kullanan bir çözüm için, etki alanına katılmış HDInsight kullanmayı düşünün. Daha fazla bilgi için bkz. [etki alanına katılmış HDInsight belgesi yapılandırma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) .

> [!WARNING]  
> HDInsight, küme için varsayılan depolamaya tam erişime sahip olmalıdır.

## <a name="prerequisites"></a>Önkoşullar

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Var olan bir [depolama kapsayıcısı](../storage/blobs/storage-quickstart-blobs-portal.md).  

* PowerShell kullanıyorsanız [az Module](/powershell/azure/)gerekecektir.

* Azure CLı 'yi kullanmak istiyorsanız ve henüz yüklemediyseniz, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

* [Python](https://www.python.org/downloads/), sürüm 2,7 veya üstünü kullanıyorsanız.

* C# kullanıyorsanız, Visual Studio sürüm 2013 veya üzeri olmalıdır.

* Depolama hesabınızın URI şeması. Bu düzen `wasb://` , `abfs://` Azure Data Lake Storage 2. veya Azure Data Lake Storage 1. Için Azure Blob depolama alanı olacaktır `adl://` . Azure Blob depolama için güvenli aktarım etkinse URI olur `wasbs://` .

* Paylaşılan erişim Imzası eklemek için var olan bir HDInsight kümesi. Aksi takdirde, küme oluşturmak ve küme oluşturma sırasında paylaşılan erişim Imzası eklemek için Azure PowerShell kullanabilirsiniz.

* Örnek dosyaları [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Bu depo aşağıdaki öğeleri içerir:

  * HDInsight ile kullanılmak üzere depolama kapsayıcısı, saklı ilke ve SAS oluşturabileceğiniz bir Visual Studio projesi
  * HDInsight ile kullanılmak üzere depolama kapsayıcısı, saklı ilke ve SAS oluşturabilecek bir Python betiği
  * HDInsight kümesi oluşturup SAS kullanacak şekilde yapılandırasağlayan bir PowerShell betiği. Aşağıda daha fazla güncelleştirilmiş sürüm kullanılıyor.
  * Örnek dosya: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Paylaşılan Erişim İmzaları

Paylaşılan erişim Imzalarının iki biçimi vardır:

* `Ad hoc`: SAS URI 'sinde başlangıç saati, süre sonu zamanı ve SAS izinleri belirtilmiştir.

* `Stored access policy`: Depolanan bir erişim ilkesi, bir blob kapsayıcısı gibi bir kaynak kapsayıcısında tanımlanmıştır. Bir ilke, bir veya daha fazla paylaşılan erişim imzalarının kısıtlamalarını yönetmek için kullanılabilir. Bir SAS 'yi depolanan erişim ilkesiyle ilişkilendirdiğinizde SAS, depolanan erişim ilkesi için başlangıç zamanı, süre sonu zamanı ve izinler tanımlı olan kısıtlamaları devralır.

İki form arasındaki fark, bir anahtar senaryosu için önemlidir: iptal. SAS bir URL 'dir, bu nedenle SAS 'YI alan herkes tarafından kullanılabilir. Kimin ile başlamasını istediğine bakılmaksızın. Bir SAS herkese açık bir şekilde yayımlanıyorsa, dünyanın herkes tarafından kullanılabilir. Dağıtılan bir SAS, dört şeyin gerçekleşene kadar geçerlidir:

1. SAS üzerinde belirtilen süre sonu zamanına ulaşıldı.

2. SAS tarafından başvurulan saklı erişim ilkesinde belirtilen süre sonu zamanına ulaşıldı. Aşağıdaki senaryolar süre sonu zamanına ulaşılmasına neden olur:

    * Zaman aralığı geçti.
    * Depolanan erişim ilkesi, geçmişteki bir süre sonu süresine sahip olacak şekilde değiştirilir. Süre sonu süresini değiştirmek, SAS 'yi iptal etmenin bir yoludur.

3. SAS tarafından başvurulan saklı erişim ilkesi silinir, bu da SAS 'yi iptal etmenin başka bir yoludur. Depolanan erişim ilkesini aynı adla yeniden oluşturursanız, önceki ilkeye yönelik tüm SAS belirteçleri geçerlidir (SAS üzerindeki süre sonu zamanı geçirilmemişse). SAS iptal etmeyi düşünüyorsanız, daha sonra bir süre sonu süresi ile erişim ilkesini yeniden oluşturursanız farklı bir ad kullandığınızdan emin olun.

4. SAS oluşturmak için kullanılan hesap anahtarı yeniden oluşturulur. Anahtarın yeniden oluşturulması, önceki anahtarı kullanan tüm uygulamaların kimlik doğrulaması başarısız olmasına neden olur. Tüm bileşenleri yeni anahtara güncelleştirin.

> [!IMPORTANT]  
> Paylaşılan erişim imzası URI 'SI, imzayı oluşturmak için kullanılan hesap anahtarıyla ve ilişkili depolanan erişim ilkesi (varsa) ile ilişkilendirilir. Depolanmış erişim ilkesi belirtilmemişse, paylaşılan erişim imzasını iptal etmenin tek yolu hesap anahtarını değiştirkullanmaktır.

Her zaman depolanan erişim ilkelerini kullanmanızı öneririz. Saklı ilkeleri kullanırken, imzaları iptal edebilir ya da süre sonu tarihini gerektiği gibi genişletebilirsiniz. Bu belgedeki adımlarda SAS oluşturmak için saklı erişim ilkeleri kullanılır.

Paylaşılan erişim Imzaları hakkında daha fazla bilgi için bkz. [SAS modelini anlama](../storage/common/storage-sas-overview.md).

## <a name="create-a-stored-policy-and-sas"></a>Depolanan ilke ve SAS oluşturma

Her yöntemin sonunda üretilen SAS belirtecini kaydedin. Belirteç aşağıdaki çıktıya benzer şekilde görünür:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>PowerShell’i kullanma

`RESOURCEGROUP`, `STORAGEACCOUNT` Ve `STORAGECONTAINER` değerlerini mevcut depolama Kapsayıcınız için uygun değerlerle değiştirin. Dizini değiştirin `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` veya `-File` parametresi için mutlak yolu içerecek şekilde düzeltin `Set-AzStorageblobcontent` . Aşağıdaki PowerShell komutunu girin:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

Bu bölümdeki değişkenlerin kullanımı bir Windows ortamını temel alır. Bash ve diğer ortamlar için hafif Çeşitlemeler gerekecektir.

1. `STORAGEACCOUNT`Ve değerlerini, `STORAGECONTAINER` mevcut depolama Kapsayıcınız için uygun değerlerle değiştirin.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Alınan birincil anahtarı daha sonra kullanmak üzere bir değişkene ayarlayın. `PRIMARYKEY`Önceki adımda alınan değerle değiştirin ve sonra aşağıdaki komutu girin:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Dizini değiştirin `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` veya `--file` parametresi için mutlak yolu içerecek şekilde düzeltin `az storage blob upload` . Kalan komutları yürütün:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Python’u kullanma

Dosyasını açın `SASToken.py` ve `storage_account_name` , `storage_account_key` , ve öğesini `storage_container_name` mevcut depolama Kapsayıcınız için uygun değerlerle değiştirin ve betiği çalıştırın.

`pip install --upgrade azure-storage`Hata iletisini alırsanız yürütmeniz gerekebilir `ImportError: No module named azure.storage` .

### <a name="using-c"></a>C kullanma\#

1. Visual Studio 'da çözümü açın.

2. Çözüm Gezgini ' de, **Sasexbol** projesine sağ tıklayın ve **Özellikler**' i seçin.

3. **Ayarlar** ' ı seçin ve aşağıdaki girişler için değerleri ekleyin:

    |Öğe |Açıklama |
    |---|---|
    |StorageConnectionString|İçin depolanan ilke ve SAS oluşturmak istediğiniz depolama hesabının bağlantı dizesi. Biçim, `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` `myaccount` depolama hesabınızın adıdır ve `mykey` depolama hesabının anahtarıdır.|
    |ContainerName|Erişimi kısıtlamak istediğiniz depolama hesabındaki kapsayıcı.|
    |SASPolicyName|Oluşturulacak saklı ilke için kullanılacak ad.|
    |FileToUpload|Kapsayıcıya yüklenen bir dosyanın yolu.|

4. Projeyi çalıştırın. SAS ilke belirtecini, depolama hesabı adını ve kapsayıcı adını kaydedin. Bu değerler, depolama hesabı HDInsight kümeniz ile ilişkilendirilirken kullanılır.

## <a name="use-the-sas-with-hdinsight"></a>HDInsight ile SAS kullanma

HDInsight kümesi oluştururken, birincil depolama hesabı belirtmeniz gerekir. Ek depolama hesapları da belirtebilirsiniz. Bu depolama alanı ekleme yöntemlerinin her ikisi de, kullanılan depolama hesaplarına ve kapsayıcılara tam erişim gerektirir.

Kapsayıcı erişimini sınırlandırmak için, paylaşılan erişim Imzası kullanın. Küme için **çekirdek site** yapılandırmasına özel bir giriş ekleyin. Girişi, PowerShell kullanarak veya ambarı kullanarak küme oluşturulduktan sonra, küme oluşturma sırasında ekleyebilirsiniz.

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS kullanan bir küme oluşturma

,,,, `CLUSTERNAME` `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` Ve `TOKEN` değerlerini uygun değerlerle değiştirin. PowerShell komutlarını girin:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> HTTP/s veya SSH Kullanıcı adı ve parola istendiğinde, aşağıdaki ölçütleri karşılayan bir parola sağlamalısınız:
>
> * En az 10 karakter uzunluğunda olmalıdır.
> * En az bir rakam içermelidir.
> * En az bir alfasayısal olmayan karakter içermelidir.
> * En az bir büyük veya küçük harf içermelidir.

Genellikle 15 dakika boyunca bu betiğin tamamlanmasıyla ilgili bir süre sürer. Betik herhangi bir hata olmadan tamamlandığında, küme oluşturulmuştur.

### <a name="use-the-sas-with-an-existing-cluster"></a>Mevcut bir kümeyle SAS kullanma

Mevcut bir kümeniz varsa, aşağıdaki adımları kullanarak SAS 'yi **çekirdek site** yapılandırmasına ekleyebilirsiniz:

1. Kümeniz için ambarı Web Kullanıcı arabirimini açın. Bu sayfanın adresi `https://YOURCLUSTERNAME.azurehdinsight.net` . İstendiğinde, kümeyi oluştururken kullandığınız yönetici adı (yönetici) ve parolayı kullanarak kümede kimlik doğrulaması yapın.

1. Bu   >    >  **Gelişmiş**  >  **özel çekirdek sitesine** gidin.

1. **Özel çekirdek-site** bölümünü genişletin, sonuna ilerleyin ve sonra **Özellik Ekle...** seçeneğini belirleyin. **Anahtar** ve **değer** için aşağıdaki değerleri kullanın:

    * **Anahtar**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Değer**: daha önce yürütülen yöntemlerin biri tarafından döndürülen sas.

    `CONTAINERNAME`C# veya SAS uygulamasıyla kullandığınız kapsayıcı adıyla değiştirin. `STORAGEACCOUNTNAME`Kullandığınız depolama hesabı adıyla değiştirin.

    Bu anahtarı ve değeri kaydetmek için **Ekle** 'yi seçin

1. Yapılandırma değişikliklerini kaydetmek için **Kaydet** düğmesini seçin. İstendiğinde, değişikliğin açıklamasını ekleyin (örneğin, "SAS depolama erişimi ekleme") ve ardından **Kaydet**' i seçin.

    Değişiklikler tamamlandığında **Tamam ' ı** seçin.

   > [!IMPORTANT]  
   > Değişiklik yürürlüğe girmeden önce birkaç hizmeti yeniden başlatmanız gerekir.

1. **Yeniden başlatma** açılan listesi görüntülenir. Açılan listeden **etkilenen tümünü yeniden Başlat** ' ı seçin ve ardından __Tümünü Yeniden Başlat__' ı onaylayın.

    **MapReduce2** ve **Yarn** için bu işlemi tekrarlayın.

1. Hizmetler yeniden başlatıldıktan sonra, **hizmet eylemleri** açılan listesinden her birini seçin ve bakım modunu devre dışı bırakın.

## <a name="test-restricted-access"></a>Kısıtlı erişimi test etme

Yalnızca SAS depolama hesabındaki öğeleri okuyabildiğinizi ve listebildiğinizi doğrulamak için aşağıdaki adımları kullanın.

1. Kümeye bağlanın. `CLUSTERNAME`Kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Kapsayıcının içeriğini listelemek için, komut isteminde aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    `SASCONTAINER`SAS depolama hesabı için oluşturulan kapsayıcının adıyla değiştirin. `SASACCOUNTNAME`SAS için kullanılan depolama hesabının adıyla değiştirin.

    Bu liste, kapsayıcı ve SAS oluşturulduğunda karşıya yüklenen dosyayı içerir.

3. Dosyanın içeriğini okuyabildiğinizi doğrulamak için aşağıdaki komutu kullanın. `SASCONTAINER`Önceki adımda ve ile değiştirin `SASACCOUNTNAME` . `sample.log`Önceki komutta görünen dosyanın adıyla değiştirin:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Bu komut, dosyanın içeriğini listeler.

4. Dosyayı yerel dosya sistemine indirmek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Bu komut, dosyayı **testfile.txt** adlı yerel bir dosyaya indirir.

5. Yerel dosyayı SAS depolamada **testupload.txt** adlı yeni bir dosyaya yüklemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Aşağıdaki metne benzer bir ileti alırsınız:

    ```output
    put: java.io.IOException
    ```

    Bu hata, depolama konumu salt okuma + liste olduğundan oluşur. Verileri küme için varsayılan depolama alanına (yazılabilir) koymak için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Bu kez, işlem başarıyla tamamlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Artık HDInsight kümenize sınırlı erişimli depolama ekleme hakkında bilgi edindiğinize göre, kümenizde verilerle çalışmanın diğer yollarını öğrenin:

* [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Kullanıcıları Apache Ambari Görünümleri için yetkilendirme](hdinsight-authorize-users-to-ambari.md)
