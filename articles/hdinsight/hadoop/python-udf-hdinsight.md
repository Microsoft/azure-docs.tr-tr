---
title: Apache Hive ve Apache Pig ile Python UDF-Azure HDInsight
description: Azure 'da Apache Hadoop teknoloji yığını olan HDInsight 'ta Apache Hive ve Apache Pig 'tan Python Kullanıcı tanımlı Işlevleri (UDF) kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 593b809813f949cd1d0bcc17e1d1b7255ea19130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944278"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>HDInsight 'ta Apache Hive ve Apache Pig ile Python Kullanıcı tanımlı Işlevleri (UDF) kullanma

Azure HDInsight 'ta Apache Hadoop Apache Hive ve Apache Pig ile Python Kullanıcı tanımlı işlevleri (UDF) kullanmayı öğrenin.

## <a name="python-on-hdinsight"></a><a name="python"></a>HDInsight üzerinde Python

Python 2.7, HDInsight 3,0 ve üzeri sürümlerde varsayılan olarak yüklüdür. Apache Hive, akış işleme için Python 'un bu sürümü ile kullanılabilir. Akış işleme, Hive ve UDF arasında veri geçirmek için STDOUT ve STDIN kullanır.

HDInsight Ayrıca, Java 'da yazılmış bir Python uygulama olan Jyıthon ' u de içerir. Jyıthon, doğrudan Java Sanal Makinesi çalışır ve akış kullanmaz. Jyıthon, Pig ile Python kullanılırken önerilen Python yorumlayıcı.

## <a name="prerequisites"></a>Önkoşullar

* **HDInsight üzerinde bir Hadoop kümesi**. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).
* **Bir SSH istemcisi**. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Kümelerinizin birincil depolama alanı için [URI şeması](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Bu `wasb://` , `abfs://` Azure Data Lake Storage 1. için Azure Data Lake Storage 2. veya adl://Için Azure Storage 'a yöneliktir. Azure depolama için güvenli aktarım etkinleştirilirse, URI wasbs://olur.  Ayrıca bkz. [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md).
* **Depolama yapılandırmasında olası değişiklik.**  Depolama hesabı türü kullanılıyorsa [depolama yapılandırması](#storage-configuration) bölümüne bakın `BlobStorage` .
* İsteğe bağlı.  PowerShell 'i kullanmayı planlıyorsanız [az Module](/powershell/azure/new-azureps-module-az) yüklü olmalıdır.

> [!NOTE]  
> Bu makalede kullanılan depolama hesabı, [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md) özellikli Azure depolama idi ve bu nedenle `wasbs` makale genelinde kullanılır.

## <a name="storage-configuration"></a>Depolama yapılandırması

Kullanılan depolama hesabı tür veya ise herhangi bir eylem gerekmez `Storage (general purpose v1)` `StorageV2 (general purpose v2)` .  Bu makaledeki işlem en az çıkış oluşturacak `/tezstaging` .  Varsayılan bir Hadoop yapılandırması `/tezstaging` , `fs.azure.page.blob.dir` hizmet için içindeki yapılandırma değişkeninde bulunur `core-site.xml` `HDFS` .  Bu yapılandırma, Dizin çıkışının, depolama hesabı türü için desteklenmeyen sayfa Blobları olmasına neden olur `BlobStorage` .  `BlobStorage`Bu makalede kullanmak için `/tezstaging` `fs.azure.page.blob.dir` yapılandırma değişkeninden kaldırın.  Bu yapılandırmaya, [ambarı kullanıcı arabiriminden](../hdinsight-hadoop-manage-ambari.md)erişilebilir.  Aksi takdirde, şu hata iletisini alırsınız: `Page blob is not supported for this account type.`

> [!WARNING]  
> Bu belgedeki adımlarda aşağıdaki varsayımlar yapılır:  
>
> * Yerel geliştirme ortamınızda Python betikleri oluşturun.
> * Komutları veya belirtilen PowerShell betiğini kullanarak, betikleri HDInsight 'a yüklersiniz `scp` .
>
> HDInsight ile çalışmak için [Azure Cloud Shell (Bash)](../../cloud-shell/overview.md) kullanmak istiyorsanız şunları yapmanız gerekir:
>
> * Cloud Shell ortamı içinde betikleri oluşturun.
> * `scp`Dosyaları Cloud Shell 'Den HDInsight 'a yüklemek için kullanın.
> * `ssh`Cloud Shell 'den kullanarak HDInsight 'a bağlanın ve örnekleri çalıştırın.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python, HiveQL ifadesiyle Hive 'den bir UDF olarak kullanılabilir `TRANSFORM` . Örneğin, aşağıdaki HiveQL, `hiveudf.py` küme için varsayılan Azure depolama hesabında depolanan dosyayı çağırır.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

İşte bu örnek:

1. `add file`Dosyanın başındaki ifade `hiveudf.py` dosyayı dağıtılmış önbelleğe ekler, bu nedenle kümedeki tüm düğümler tarafından erişilebilir.
2. `SELECT TRANSFORM ... USING`İfade, öğesinden veri seçer `hivesampletable` . Ayrıca, ClientID, devicemake ve devicemodel değerlerini `hiveudf.py` betiğe geçirir.
3. `AS`Yan tümcesi, öğesinden döndürülen alanları açıklar `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Dosya oluştur

Geliştirme ortamınızda adlı bir metin dosyası oluşturun `hiveudf.py` . Aşağıdaki kodu dosyanın içeriğiyle kullanın:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Bu betik aşağıdaki eylemleri gerçekleştirir:

1. STDIN 'den bir veri satırı okur.
2. Son yeni satır karakteri kullanılarak kaldırılır `string.strip(line, "\n ")` .
3. Akış işleme yaparken, tek bir satır her bir değer arasında sekme karakteri olan tüm değerleri içerir. Bu nedenle `string.split(line, "\t")` , her sekmedeki girişi ayırmak için yalnızca alanları döndürerek kullanılabilir.
4. İşlem tamamlandığında, her bir alan arasında sekme ile, çıktının STDOUT 'a tek bir çizgi olarak yazılması gerekir. Örneğin, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while`Döngü, hiç `line` okununcaya kadar yinelenir.

Komut dosyası çıkışı, ve için giriş değerlerinin `devicemake` `devicemodel` ve birleştirilmiş değerin bir karmasından oluşan bir bitiştirilir.

### <a name="upload-file-shell"></a>Dosyayı karşıya yükle (Shell)

Aşağıdaki komutlarda, `sshuser` farklı ise gerçek kullanıcı adıyla değiştirin.  `mycluster`Gerçek küme adıyla değiştirin.  Çalışma dizininizin dosyanın bulunduğu yer olduğundan emin olun.

1. `scp`Dosyaları HDInsight kümenize kopyalamak için kullanın. Aşağıdaki komutu düzenleyin ve girin:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Kümeye bağlanmak için SSH kullanın.  Aşağıdaki komutu düzenleyin ve girin:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH oturumunda, daha önce küme için depolama alanına yüklenen Python dosyalarını ekleyin.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF (Shell) kullanma

1. Hive 'e bağlanmak için, Open SSH oturumunınızdan aşağıdaki komutu kullanın:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Bu komut, Beeline istemcisini başlatır.

2. Komut istemine aşağıdaki sorguyu girin `0: jdbc:hive2://headnodehost:10001/>` :

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Son satırı girdikten sonra iş başlamalıdır. İş tamamlandıktan sonra, aşağıdaki örneğe benzer bir çıktı döndürür:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Beeline çıkmak için aşağıdaki komutu girin:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Dosyayı karşıya yükle (PowerShell)

Ayrıca, PowerShell, Hive sorgularını uzaktan çalıştırmak için de kullanılabilir. Çalışma dizininizin bulunduğu yer olduğundan emin olun `hiveudf.py` .  Komut dosyasını kullanan bir Hive sorgusu çalıştırmak için aşağıdaki PowerShell betiğini kullanın `hiveudf.py` :

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Dosyaları karşıya yükleme hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop işleri için verileri karşıya yükleme](../hdinsight-upload-data.md) .

#### <a name="use-hive-udf"></a>Hive UDF kullanma

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Hive** işi çıkışı aşağıdaki örneğe benzer görünmelidir:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Pig UDF

Python betiği, Pig öğesinden bir UDF aracılığıyla kullanılabilir `GENERATE` . Betiği Jyıthon veya C Python kullanarak çalıştırabilirsiniz.

* Jyıthon, JVM üzerinde çalışır ve Pig adresinden yerel olarak çağrılabilir.
* C Python bir dış işlemdir, bu nedenle JVM üzerindeki Pig verileri bir Python işleminde çalışan betiğe gönderilir. Python betiğinin çıktısı Pig 'e geri gönderilir.

Python yorumlayıcı 'yı belirtmek için `register` Python betiğine başvururken kullanın. Aşağıdaki örnekler betikleri Pig ile kaydeder `myfuncs` :

* **Jython kullanmak için**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python kullanmak için**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Jyıthon kullanırken pig_jython dosyanın yolu bir yerel yol veya bir WASBS://yolu olabilir. Ancak, C Python kullanırken, Pig işini göndermek için kullandığınız düğümün yerel dosya sistemindeki bir dosyaya başvurmanız gerekir.

Kayıt sonrasında, bu örnek için Pig Latin her ikisi için de aynıdır:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

İşte bu örnek:

1. İlk satır, örnek veri dosyasını `sample.log` içine yükler `LOGS` . Ayrıca, her bir kaydı bir olarak tanımlar `chararray` .
2. Sonraki satır, işlemin sonucunu içine depolayarak null değerlerini filtreler `LOG` .
3. Daha sonra, ' deki kayıtları yineler `LOG` ve `GENERATE` `create_structure` olarak yüklenen Python/Jyıthon betikte bulunan yöntemi çağırmak için kullanır `myfuncs` . `LINE` , geçerli kaydı işleve geçirmek için kullanılır.
4. Son olarak, çıktılar komutu kullanılarak STDOUT 'a dökülür `DUMP` . Bu komut, işlem tamamlandıktan sonra sonuçları görüntüler.

### <a name="create-file"></a>Dosya oluştur

Geliştirme ortamınızda adlı bir metin dosyası oluşturun `pigudf.py` . Aşağıdaki kodu dosyanın içeriğiyle kullanın:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Giriş için tutarlı bir şema olmadığından, Pig Latin örneğinde, `LINE` Giriş bir CharArray olarak tanımlanır. Python betiği, verileri çıkış için tutarlı bir şemaya dönüştürür.

1. `@outputSchema`İfade, Pig 'e döndürülen verilerin biçimini tanımlar. Bu durumda, Pig veri türü olan bir **veri torbudur**. Paket, hepsi CharArray (dizeler) olan aşağıdaki alanları içerir:

   * Tarih-günlük girişinin oluşturulduğu tarih
   * zaman-günlük girişinin oluşturulduğu saat
   * ClassName-girdinin oluşturulduğu sınıf adı
   * düzey-günlük düzeyi
   * ayrıntı-günlük girdisi için ayrıntılı Ayrıntılar

2. Sonra, `def create_structure(input)` Pig tarafından satır öğelerini geçiren işlevi tanımlar.

3. Örnek veriler, `sample.log` genellikle Date, Time, ClassName, Level ve Detail şemasına uyar. Ancak, ile başlayan birkaç satır içerir `*java.lang.Exception*` . Bu satırların şemayla eşleşecek şekilde değiştirilmesi gerekir. `if`İfade, bunları denetler, ardından dizeyi sona erdirmek için giriş verilerini mastine, `*java.lang.Exception*` verileri de beklenen çıkış şemasıyla hareket etiyorlar.

4. Ardından komut, `split` verileri ilk dört alan karakteri içine ayırmak için kullanılır. Çıktı,,, `date` ve içine `time` atanır `classname` `level` `detail` .

5. Son olarak, değerler Pig olarak döndürülür.

Veriler Pig 'e döndürüldüğünde, bildiriminde tanımlandığı gibi tutarlı bir şemaya sahip olur `@outputSchema` .

### <a name="upload-file-shell"></a>Dosyayı karşıya yükle (Shell)

Aşağıdaki komutlarda, `sshuser` farklı ise gerçek kullanıcı adıyla değiştirin.  `mycluster`Gerçek küme adıyla değiştirin.  Çalışma dizininizin dosyanın bulunduğu yer olduğundan emin olun.

1. `scp`Dosyaları HDInsight kümenize kopyalamak için kullanın. Aşağıdaki komutu düzenleyin ve girin:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Kümeye bağlanmak için SSH kullanın.  Aşağıdaki komutu düzenleyin ve girin:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH oturumunda, daha önce küme için depolama alanına yüklenen Python dosyalarını ekleyin.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Pig UDF (Shell) kullanma

1. Pig 'e bağlanmak için, Open SSH oturumunınızdan aşağıdaki komutu kullanın:

    ```bash
    pig
    ```

2. Komut istemine aşağıdaki deyimleri girin `grunt>` :

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Aşağıdaki satırı girdikten sonra iş başlamalıdır. İş tamamlandıktan sonra, aşağıdaki verilere benzer bir çıktı döndürür:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. `quit`Grupshell 'den çıkmak için kullanın ve ardından yerel dosya sisteminde pigudf.py dosyasını düzenlemek için aşağıdakileri kullanın:

    ```bash
    nano pigudf.py
    ```

5. Düzenleyiciden bir kez, satırın başından itibaren karakteri kaldırarak aşağıdaki satırın açıklamasını kaldırın `#` :

    ```bash
    #from pig_util import outputSchema
    ```

    Bu satır, Python betiğini Jyıthon yerine C Python ile çalışacak şekilde değiştirir. Değişiklik yapıldıktan sonra, düzenleyiciden çıkmak için **CTRL + X** tuşlarını kullanın. **Y**' yi seçin ve ardından değişiklikleri kaydetmek için **girin** .

6. `pig`Kabuğu yeniden başlatmak için komutunu kullanın. `grunt>`İstemde olduktan sonra, C Python yorumlayıcı kullanarak Python betiğini çalıştırmak için aşağıdakileri kullanın.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Bu iş tamamlandıktan sonra, daha önce Jyıthon kullanarak betiği çalıştırdığındaki çıktıyı görmeniz gerekir.

### <a name="upload-file-powershell"></a>Dosyayı karşıya yükle (PowerShell)

Ayrıca, PowerShell, Hive sorgularını uzaktan çalıştırmak için de kullanılabilir. Çalışma dizininizin bulunduğu yer olduğundan emin olun `pigudf.py` .  Komut dosyasını kullanan bir Hive sorgusu çalıştırmak için aşağıdaki PowerShell betiğini kullanın `pigudf.py` :

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Pig UDF (PowerShell) kullanma

> [!NOTE]  
> PowerShell kullanarak bir işi uzaktan gönderirken, yorumlayıcı olarak C Python kullanılması mümkün değildir.

PowerShell, Pig Latin işleri çalıştırmak için de kullanılabilir. Betiği kullanan bir Pig Latin işini çalıştırmak için `pigudf.py` aşağıdaki PowerShell betiğini kullanın:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Pig** işi çıktısı aşağıdaki verilere benzer görünmelidir:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Sorun giderme

### <a name="errors-when-running-jobs"></a>İşlerin çalıştırıldığı hata

Hive işini çalıştırırken aşağıdaki metne benzer bir hatayla karşılaşabilirsiniz:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Bu sorun, Python dosyasındaki satır sonları nedeniyle oluşabilir. Birçok Windows Düzenleyicisi, satır sonu olarak CRLF 'yi varsayılan olarak kullanmaktır, ancak Linux uygulamaları genellikle LF bekler.

Dosyayı HDInsight 'a yüklemeden önce CR karakterlerini kaldırmak için aşağıdaki PowerShell deyimlerini kullanabilirsiniz:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell komut dosyaları

Örnekleri çalıştırmak için kullanılan örnek PowerShell betikleri her ikisi de iş için hata çıktısını görüntüleyen açıklamalı bir çizgi içerir. İş için beklenen çıktıyı görmüyorsanız aşağıdaki satırın açıklamasını kaldırın ve hata bilgilerinde bir sorun olup olmadığını görün.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Hata bilgileri (STDERR) ve işin sonucu (STDOUT) HDInsight depolama alanına da kaydedilir.

| Bu iş için... | Blob kapsayıcısında bu dosyalara bakın |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Sonraki adımlar

Varsayılan olarak sağlanmayan Python modüllerini yüklemeniz gerekiyorsa bkz. [Azure HDInsight 'a modül dağıtma](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Pig, Hive kullanmanın diğer yolları ve MapReduce kullanma hakkında bilgi edinmek için aşağıdaki belgelere bakın:

* [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hdinsight-use-mapreduce.md)