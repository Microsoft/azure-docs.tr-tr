---
title: HDInsight 'a ek Azure Storage hesapları ekleme
description: Mevcut bir HDInsight kümesine daha fazla Azure depolama hesabı eklemeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665485"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight 'a ek depolama hesapları ekleme

HDInsight 'a ek Azure depolama *hesapları* eklemek için betik eylemlerinin nasıl kullanılacağını öğrenin. Bu belgedeki adımlar, mevcut bir Linux tabanlı HDInsight kümesine bir depolama *hesabı* ekler. Bu makale, depolama *hesapları* (varsayılan küme depolama hesabı değil) için geçerlidir ve [Azure Data Lake Storage 1.](hdinsight-hadoop-use-data-lake-store.md) ve [Azure Data Lake Storage 2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)gibi ek depolama alanı değildir.

> [!IMPORTANT]  
> Bu belgedeki bilgiler, bir kümeye oluşturulduktan sonra ek depolama hesapları ekleme hakkında bilgi içerir. Küme oluşturma sırasında depolama hesapları ekleme hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlası ile kümeleri ayarlama](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Ön koşullar

* HDInsight üzerinde bir Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Depolama hesabı adı ve anahtarı. Bkz. [Azure Portal depolama hesabı ayarlarını yönetme](../storage/common/storage-account-manage.md).
* [Küme adı doğru](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)değil.
* PowerShell kullanıyorsanız AZ Module gerekecektir.  Bkz. [Azure PowerShell genel bakış](https://docs.microsoft.com/powershell/azure/overview).
* Azure CLı 'yı yüklemediyseniz bkz. [Azure komut satırı arabirimi (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Bash veya bir Windows komut istemi kullanıyorsanız, bir komut satırı JSON işlemcisi olan **JQ**da gerekir.  Bkz. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Windows 10 ' da Ubuntu 'da bash için bkz. [Windows 10 Linux Için Windows alt sistemi yükleme kılavuzu](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Nasıl çalışır?

Bu betik aşağıdaki parametreleri alır:

* __Azure depolama hesabı adı__: HDInsight kümesine eklenecek depolama hesabının adı. Betiği çalıştırdıktan sonra, HDInsight bu depolama hesabında depolanan verileri okuyabilir ve yazabilir.

* __Azure depolama hesabı anahtarı__: depolama hesabına erişim izni veren bir anahtar.

* __-p__ (isteğe bağlı): belirtilmişse, anahtar şifrelenmez ve Core-site. xml dosyasında düz metin olarak depolanır.

İşlem sırasında betik aşağıdaki eylemleri gerçekleştirir:

* Depolama hesabı, küme için Core-site. xml yapılandırmasında zaten varsa, komut dosyası çıkar ve başka hiçbir eylem gerçekleştirilmez.

* Depolama hesabının var olduğunu ve anahtar kullanılarak erişilebilir olduğunu doğrular.

* Küme kimlik bilgilerini kullanarak anahtarı şifreler.

* Core-site. xml dosyasına depolama hesabı ekler.

* [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)ve [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) mı hizmetlerini durdurup yeniden başlatır. Bu hizmetleri durdurup başlatmak, yeni depolama hesabını kullanmalarına izin verir.

> [!WARNING]  
> HDInsight kümesinden farklı bir konumda depolama hesabı kullanılması desteklenmez.

## <a name="the-script"></a>Betik

__Betik konumu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Gereksinimler__: betiğin __baş düğümlere__uygulanması gerekir. Bu betiği __kalıcı__olarak işaretlemeniz gerekmez, çünkü bu, küme için ambarı yapılandırmasını doğrudan güncelleştirir.

## <a name="to-use-the-script"></a>Betiği kullanmak için

Bu betik Azure PowerShell, Azure CLı veya Azure portal kullanılabilir.

### <a name="powershell"></a>PowerShell

[Gönderme-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction)kullanma. `CLUSTERNAME`, `ACCOUNTNAME`ve `ACCOUNTKEY` uygun değerlerle değiştirin.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

[Az HDInsight betiği kullanma-eylem yürütme](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`ve `ACCOUNTKEY` uygun değerlerle değiştirin.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure portal

Bkz. [çalışan bir kümeye betik eylemi uygulama](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="storage-firewall"></a>Depolama güvenlik duvarı

Depolama hesabınızı **Seçili ağlardaki** **güvenlik duvarları ve sanal ağlar** kısıtlamalarına göre güvenli hale getirmek isterseniz, HDInsight 'ın depolama verilerinize erişebilmesi Için **güvenilir Microsoft hizmetlerine izin ver...** özel durumunu etkinleştirdiğinizden emin olun. hesabı.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Azure portal veya araçlarda görüntülenmeyen depolama hesapları

Azure portal HDInsight kümesi görüntülenirken, __Özellikler__ altında __depolama hesapları__ girdisi seçildiğinde bu betik eylemiyle eklenen depolama hesapları gösterilmez. Azure PowerShell ve Azure CLı ek depolama hesabını görüntülemez.

Komut dosyası yalnızca küme için Core-site. xml yapılandırmasını değiştirdiği için depolama bilgileri gösterilmez. Bu bilgiler, Azure Yönetim API 'Leri kullanılarak küme bilgileri alınırken kullanılmaz.

Bu betiği kullanarak kümeye eklenen depolama hesabı bilgilerini görüntülemek için REST API ambarı 'nı kullanın. Kümeniz için bu bilgileri almak üzere aşağıdaki komutları kullanın:

### <a name="powershell"></a>PowerShell

`CLUSTERNAME`, doğru şekilde kullanılan küme adıyla değiştirin. `ACCOUNTNAME` gerçek adlarla değiştirin. İstendiğinde, küme oturum açma parolasını girin.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash

`CLUSTERNAME`, doğru şekilde kullanılan küme adıyla değiştirin. `PASSWORD`, Küme Yöneticisi parolasıyla değiştirin. `STORAGEACCOUNT` değerini gerçek depolama hesabı adıyla değiştirin.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

`CLUSTERNAME`, her iki betikte uygun küme adı ile değiştirin. Önce aşağıdaki komutu girerek kullanımda olan hizmet yapılandırma sürümünü belirlemeniz gerekir:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

`ACCOUNTNAME` değerini gerçek depolama hesabı adıyla değiştirin. Sonra `4` gerçek hizmet yapılandırma sürümüyle değiştirin ve şu komutu girin:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Bu komuttan döndürülen bilgiler aşağıdaki metne benzer şekilde görünür:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Bu metin, depolama hesabına erişmek için kullanılan şifrelenmiş anahtara bir örnektir.

### <a name="unable-to-access-storage-after-changing-key"></a>Anahtar değiştirildikten sonra depolamaya erişilemiyor

Bir depolama hesabı için anahtarı değiştirirseniz, HDInsight artık depolama hesabına erişemez. HDInsight, küme için Core-site. xml dosyasında önbelleğe alınmış anahtarın bir kopyasını kullanır. Bu önbelleğe alınan kopyanın yeni anahtarla eşleşecek şekilde güncellenmesi gerekir.

Betik eylemi yeniden çalıştırıldığında anahtar, depolama hesabı için bir girişin zaten mevcut olup olmadığını kontrol eder. Zaten bir giriş varsa, hiçbir değişiklik yapmaz.

Bu sorunu geçici olarak çözmek için depolama hesabı için mevcut girişi kaldırmanız gerekir. Mevcut girişi kaldırmak için aşağıdaki adımları kullanın:

> [!IMPORTANT]  
> Bir kümeye bağlı birincil depolama hesabı için depolama anahtarı döndürülmesi desteklenmez.

1. Bir Web tarayıcısında, HDInsight kümeniz için ambarı Web Kullanıcı arabirimini açın. URI `https://CLUSTERNAME.azurehdinsight.net`. `CLUSTERNAME` değerini kümenizin adıyla değiştirin.

    İstendiğinde, kümenizin HTTP oturum açma kullanıcı ve parolasını girin.

2. Sayfanın solundaki hizmetler listesinden,, __"seçin.__ Ardından sayfanın merkezindeki __Yapılandırma sekmesini seçin__ .

3. __Filter...__ alanına bir __FS. Azure. Account__değeri girin. Bu, kümeye eklenmiş olan ek depolama hesaplarının girişlerini döndürür. İki tür giriş vardır; __keyprovider__ ve __anahtar__. Her ikisi de, anahtar adının bir parçası olarak depolama hesabının adını içerir.

    Aşağıda, __depolamam__adlı bir depolama hesabı için örnek girişler verilmiştir:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Kaldırmanız gereken depolama hesabı için anahtarları tanımladıktan sonra, girişin sağındaki kırmızı '-' simgesini kullanarak silin. Sonra değişikliklerinizi kaydetmek için __Kaydet__ düğmesini kullanın.

5. Değişiklikler kaydedildikten sonra, depolama hesabını ve yeni anahtar değerini kümeye eklemek için betik eylemini kullanın.

### <a name="poor-performance"></a>Düşük performans

Depolama hesabı HDInsight kümesinden farklı bir bölgedeyse, düşük performansla karşılaşabilirsiniz. Farklı bir bölgedeki verilere erişmek, bölgesel Azure veri merkezi dışında ve genel İnternet üzerinden ağ trafiği gönderir ve bu da gecikme süresi ortaya çıkarabilir.

### <a name="additional-charges"></a>Ek ücretler

Depolama hesabı HDInsight kümesinden farklı bir bölgedeyse Azure faturamda ek çıkış ücretleri görebilirsiniz. Veriler bölgesel bir veri merkezinden ayrıldığında çıkış ücreti uygulanır. Bu ücret, trafik farklı bir bölgedeki başka bir Azure veri merkezine gidiyor olsa bile uygulanır.

## <a name="next-steps"></a>Sonraki adımlar

Var olan bir HDInsight kümesine daha fazla depolama hesabı eklemeyi öğrendiniz. Betik eylemleri hakkında daha fazla bilgi için bkz. [betik eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)
