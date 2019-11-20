---
title: REST API-Azure HDInsight ile Hadoop izleme ve yönetme
description: Azure HDInsight 'ta Hadoop kümelerini izlemek ve yönetmek için ambarı 'nı nasıl kullanacağınızı öğrenin. Bu belgede, HDInsight kümelerine eklenen REST API ambarı 'nı nasıl kullanacağınızı öğreneceksiniz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: 1d684957939c5cb83aae05962c1694f7a8d8da23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498241"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Apache ambarı 'nı kullanarak HDInsight kümelerini yönetme REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache ambarı REST API kullanarak Azure HDInsight 'ta Apache Hadoop kümelerini yönetme ve izleme hakkında bilgi edinin.

## <a id="whatis"></a>Apache ambarı nedir?

[Apache ambarı](https://ambari.apache.org) , [REST API 'leri](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)tarafından desteklenen kullanımı kolay bir Web Kullanıcı arabirimi sağlayarak Hadoop kümelerinin yönetimini ve izlenmesini basitleştirir.  Ambarı, Linux tabanlı HDInsight kümeleri ile varsayılan olarak sağlanır.

## <a name="prerequisites"></a>Önkoşullar

* **HDInsight üzerinde bir Hadoop kümesi**. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Windows 10 ' da Ubuntu üzerinde Bash**.  Bu makaledeki örneklerde, Windows 10 ' da bash kabuğu kullanılır. Yükleme adımları için bkz. [Windows 10 Linux Için Windows alt sistemi yükleme kılavuzu](https://docs.microsoft.com/windows/wsl/install-win10) .  Diğer [UNIX kabukları](https://www.gnu.org/software/bash/) de çalışacaktır.  Bazı küçük değişikliklere sahip örnekler, Windows komut istemi üzerinde çalışabilir.  Alternatif olarak, Windows PowerShell kullanabilirsiniz.

* bir komut satırı JSON işlemcisi olan **JQ**.  Bkz. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Alternatif olarak, [Bash](https://www.gnu.org/software/bash/)'i de kullanabilirsiniz.

## <a name="base-uri-for-ambari-rest-api"></a>Ambarı REST API 'si için temel URI

 HDInsight üzerinde REST API ambarı için temel Tekdüzen Kaynak tanımlayıcısı (URI), `CLUSTERNAME` kümenizin adıdır `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`.  URI 'Lerinde küme adları **büyük/küçük harfe duyarlıdır**.  URI (`CLUSTERNAME.azurehdinsight.net`) tam etki alanı adı (FQDN) bölümünde bulunan küme adı büyük/küçük harfe duyarsızdır, URI 'deki diğer oluşumlar büyük/küçük harfe duyarlıdır.

## <a name="authentication"></a>Kimlik Doğrulaması

HDInsight üzerinde ambarı 'na bağlanmak için HTTPS gerekir. Küme oluşturma sırasında verdiğiniz yönetici hesabı adını (varsayılan **yönetici**) ve parolayı kullanın.

Kurumsal Güvenlik Paketi kümeler için `admin`yerine `username@domain.onmicrosoft.com`gibi tam bir Kullanıcı adı kullanın.

## <a name="examples"></a>Örnekler

### <a name="setup-preserve-credentials"></a>Kurulum (kimlik bilgilerini koru)
Her bir örneğe yeniden girmemek için kimlik bilgilerinizi koruyun.  Küme adı ayrı bir adımda korunacaktır.

**A. bash**  
`PASSWORD` gerçek parolanızla değiştirerek aşağıdaki betiği düzenleyin.  Sonra komutunu girin.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Doğru şekilde oluşturulmuş küme adını tanımla
Kümenin nasıl oluşturulduğuna bağlı olarak, küme adının gerçek büyük küçük harfleri beklediğinizden farklı olabilir.  Buradaki adımlarda gerçek büyük küçük harf görüntülenir ve ardından sonraki tüm örnekler için bir değişkende depoda yer verilmiştir.

`CLUSTERNAME`, küme adınızla değiştirmek için aşağıdaki betikleri düzenleyin. Sonra komutunu girin. (FQDN için küme adı, büyük/küçük harfe duyarlı değildir.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>JSON verilerini ayrıştırma

Aşağıdaki örnek, JSON yanıt belgesini ayrıştırmak ve sonuçlardan yalnızca `health_report` bilgileri göstermek için [JQ](https://stedolan.github.io/jq/) veya [ConvertFrom-JSON](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) ' u kullanır.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="example-get-the-fqdn-of-cluster-nodes"></a>Küme düğümlerinin FQDN 'sini alın

HDInsight ile çalışırken, bir küme düğümünün tam etki alanı adını (FQDN) bilmeniz gerekebilir. Aşağıdaki örnekleri kullanarak kümedeki çeşitli düğümler için FQDN 'yi kolayca alabilirsiniz:

**Tüm düğümler**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Baş düğümler**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Çalışan düğümleri**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper düğümleri**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Küme düğümlerinin iç IP adresini al

Bu bölümdeki örneklerde döndürülen IP adreslerine internet üzerinden doğrudan erişilemez. Bunlar yalnızca HDInsight kümesini içeren Azure sanal ağı içinde erişilebilir.

HDInsight ve sanal ağlarla çalışma hakkında daha fazla bilgi için bkz. [HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

IP adresini bulmak için, küme düğümlerinin iç tam etki alanı adını (FQDN) bilmeniz gerekir. FQDN 'ye sahip olduktan sonra konağın IP adresini alabilirsiniz. Aşağıdaki örnekler ilk olarak tüm konak düğümlerinin FQDN 'sini sorgular ve ardından her konağın IP adresi için ambarı sorgulayın.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Varsayılan depolamayı al

Bir HDInsight kümesi oluşturduğunuzda, küme için varsayılan depolama alanı olarak bir Azure depolama hesabı veya Data Lake Storage kullanmanız gerekir. Küme oluşturulduktan sonra bu bilgileri almak için ambarı 'nı kullanabilirsiniz. Örneğin, HDInsight dışındaki kapsayıcıya veri okumak/yazmak istiyorsanız.

Aşağıdaki örneklerde, varsayılan depolama yapılandırması kümeden alınır:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Bu örnekler, bu bilgileri içeren sunucuya (`service_config_version=1`) uygulanan ilk yapılandırmayı döndürür. Küme oluşturulduktan sonra değiştirilmiş bir değeri alırsanız, yapılandırma sürümlerini listeetmeniz ve en son olanı almanız gerekebilir.

Dönüş değeri aşağıdaki örneklerden birine benzerdir:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-bu değer, kümenin varsayılan depolama alanı için bir Azure depolama hesabı kullandığını gösterir. `ACCOUNTNAME` değeri, depolama hesabının adıdır. `CONTAINER` bölümü, depolama hesabındaki blob kapsayıcısının adıdır. Kapsayıcı, küme için uyumlu depolamanın köküdür.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`-bu değer, kümenin varsayılan depolama Azure Data Lake Storage 2. kullandığını gösterir. `ACCOUNTNAME` ve `CONTAINER` değerleri, daha önce bahsedilen Azure Storage ile aynı anlamlara sahiptir.

* `adl://home`-bu değer, kümenin varsayılan depolama Azure Data Lake Storage 1. kullandığını gösterir.

    Data Lake Storage hesap adını bulmak için aşağıdaki örnekleri kullanın:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Dönüş değeri `ACCOUNTNAME.azuredatalakestore.net`benzerdir; burada `ACCOUNTNAME` Data Lake Storage hesabının adıdır.

    Kümenin depolama alanını içeren Data Lake Storage dizinde bulunan dizini bulmak için aşağıdaki örnekleri kullanın:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Dönüş değeri `/clusters/CLUSTERNAME/`benzerdir. Bu değer Data Lake Storage hesabı içindeki bir yoldur. Bu yol, kümenin uyumlu dosya sisteminin köküdür.  

> [!NOTE]  
> [Azure PowerShell](/powershell/azure/overview) tarafından sunulan [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet 'i, kümenin depolama bilgilerini de döndürür.

### <a name="get-all-configurations"></a>Tüm konfigürasyonları al

Kümeniz için kullanılabilir olan konfigürasyonları alın.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Bu örnek, kümede yüklü olan bileşenlerin geçerli yapılandırmasını ( *etiket* değeri ile tanımlanır) IÇEREN bir JSON belgesi döndürür. Aşağıdaki örnek, bir Spark kümesi türünden döndürülen verilerden oluşan bir alıntıdır.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Belirli bir bileşen için yapılandırmayı al

İlgilendiğiniz bileşen için yapılandırmayı alın. Aşağıdaki örnekte `INITIAL`, önceki istekten döndürülen etiket değeriyle değiştirin.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Bu örnek `livy2-conf` bileşeni için geçerli yapılandırmayı içeren bir JSON belgesi döndürür.

### <a name="update-configuration"></a>Güncelleştirme yapılandırması

1. `newconfig.json`oluşturun.  
   Değiştirin ve ardından aşağıdaki komutları girin:

   * `livy2-conf` istenen bileşen ile değiştirin.
   * `INITIAL` [Tüm yapılandırmaların al](#get-all-configurations)`tag` için alınan gerçek değerle değiştirin.

     **A. bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShell betiği [JQ](https://stedolan.github.io/jq/)kullanır.  `C:\HD\jq\jq-win64`, [JQ](https://stedolan.github.io/jq/)'ın gerçek yolunu ve sürümünü yansıtacak şekilde düzenleyin.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ, HDInsight 'tan alınan verileri yeni bir yapılandırma şablonuna dönüştürmek için kullanılır. Özellikle, bu örnekler aşağıdaki eylemleri gerçekleştirir:

   * "Version" dizesini ve `newtag`depolanan tarihi içeren benzersiz bir değer oluşturur.

   * İstenen yeni yapılandırma için bir kök belge oluşturur.

   * `.items[]` dizisinin içeriğini alır ve **desired_config** öğesinin altına ekler.

   * `href`, `version`ve `Config` öğelerini siler, bu öğeler yeni bir yapılandırma göndermek için gerekli değildir.

   * `version#################`değeri olan bir `tag` öğesi ekler. Sayısal Bölüm, geçerli tarihi temel alır. Her yapılandırmanın benzersiz bir etiketi olmalıdır.

     Son olarak, veriler `newconfig.json` belgesine kaydedilir. Belge yapısı aşağıdaki örneğe benzer görünmelidir:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. `newconfig.json`düzenleyin.  
   `newconfig.json` belgeyi açın ve `properties` nesnesindeki değerleri değiştirin/ekleyin. Aşağıdaki örnek `"livy.server.csrf_protection.enabled"` değerini `"true"` `"false"`olarak değiştirir.

        "livy.server.csrf_protection.enabled": "false",

    Değişiklikleri yaptıktan sonra dosyayı kaydedin.

3. `newconfig.json`gönder.  
   Güncelleştirilmiş yapılandırmayı ambarı 'na göndermek için aşağıdaki komutları kullanın.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Bu komutlar **newconfig. JSON** dosyasının içeriğini, istenen yeni yapılandırma olarak kümeye gönderir. İstek bir JSON belgesi döndürüyor. Bu belgedeki **VersionTag** öğesi gönderdiğiniz sürümle eşleşmelidir ve **yapılandırmalarını** nesnesi istediğiniz yapılandırma değişikliklerini içerir.

### <a name="restart-a-service-component"></a>Hizmet bileşenini yeniden Başlat

Bu noktada, ambarı Web Kullanıcı arabirimine baktığınızda Spark hizmeti, yeni yapılandırmanın etkili olabilmesi için yeniden başlatılması gerektiğini gösterir. Hizmeti yeniden başlatmak için aşağıdaki adımları kullanın.

1. Spark2 hizmetinin bakım modunu etkinleştirmek için aşağıdakileri kullanın:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Bakım modunu doğrula  

    Bu komutlar, bakım modunu etkinleştiren sunucuya bir JSON belgesi gönderir. Aşağıdaki isteği kullanarak hizmetin Şu anda bakım modunda olduğunu doğrulayabilirsiniz:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Dönüş değeri `ON`.

3. Sonra, Spark2 hizmetini kapatmak için aşağıdakileri kullanın:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Yanıt aşağıdaki örneğe benzer:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > Bu URI tarafından döndürülen `href` değeri, küme düğümünün iç IP adresini kullanıyor. Küme dışından kullanmak için `10.0.0.18:8080` bölümünü kümenin FQDN 'siyle değiştirin.  

4. İsteği doğrulayın.  
    `29`, önceki adımdan döndürülen `id` gerçek değer ile değiştirerek aşağıdaki komutu düzenleyin.  Aşağıdaki komutlar isteğin durumunu alır:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    `COMPLETED` yanıtı isteğin bittiğini gösterir.

5. Önceki istek tamamlandıktan sonra, Spark2 hizmetini başlatmak için aşağıdakileri kullanın.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Hizmet şimdi yeni yapılandırmayı kullanıyor.

6. Son olarak, bakım modunu kapatmak için aşağıdakileri kullanın.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Sonraki adımlar

REST API için tüm bir başvuru için bkz. [Apache AMBARı API başvurusu v1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ayrıca bkz. [kullanıcıları Apache ambarı görünümleri Için Yetkilendir](./hdinsight-authorize-users-to-ambari.md)
