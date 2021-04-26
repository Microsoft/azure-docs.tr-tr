---
title: Linux tabanlı HDInsight 'ta Hadoop kullanmaya yönelik ipuçları-Azure
description: Azure bulutunda çalışan tanıdık bir Linux ortamında Linux tabanlı HDInsight (Hadoop) kümelerini kullanmaya yönelik uygulama ipuçları alın.
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 2d2619c7bd7bc09eeab3845599758db7134b4134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945650"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Linux’ta HDInsight kullanma ile ilgili bilgiler

Azure HDInsight kümeleri, Azure bulutu 'nda çalışan tanıdık bir Linux ortamında Apache Hadoop sağlar. Çoğu şey için, tüm Linux üzerinde Hadoop yüklemesi gibi çalışır. Bu belge, bilmeniz gereken belirli farklılıkları çağırır.

## <a name="prerequisites"></a>Önkoşullar

Bu belgedeki adımların birçoğu, sisteminizde yüklü olması gerekebilecek aşağıdaki yardımcı programları kullanır.

* [kıvrımlı](https://curl.haxx.se/) -Web tabanlı hizmetlerle iletişim kurmak için kullanılır.
* bir komut satırı JSON işlemcisi olan **JQ**.  Bkz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) ..
* [Azure CLI](/cli/azure/install-azure-cli) -Azure hizmetlerini uzaktan yönetmek için kullanılır.
* **Bir SSH istemcisi**. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Kullanıcılar

[Etki alanına katılmış](./domain-joined/hdinsight-security-overview.md)değilse, HDInsight **tek kullanıcılı** bir sistem olarak kabul edilmelidir. Küme ile, yönetici düzeyindeki izinlerle tek bir SSH kullanıcı hesabı oluşturulur. Ek SSH hesapları oluşturulabilir, ancak kümeye yönetici erişimi de vardır.

Etki alanına katılmış HDInsight birden çok kullanıcıyı ve daha ayrıntılı izin ve rol ayarlarını destekler. Daha fazla bilgi için bkz. [etki alanına katılmış HDInsight kümelerini yönetme](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Etki alanı adları

Internet 'ten kümeye bağlanılırken kullanılacak tam etki alanı adı (FQDN) `CLUSTERNAME.azurehdinsight.net` veya `CLUSTERNAME-ssh.azurehdinsight.net` (yalnızca SSH için).

Dahili olarak, kümedeki her düğümün küme yapılandırması sırasında atanan bir adı vardır. Küme adlarını bulmak için, bkz. ambarı Web Kullanıcı arabirimindeki **konaklar** sayfası. Ayrıca, ambarı REST API konağın bir listesini döndürmek için aşağıdakileri de kullanabilirsiniz:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

`CLUSTERNAME` değerini kümenizin adıyla değiştirin. İstendiğinde, yönetici hesabının parolasını girin. Bu komut, kümedeki ana bilgisayarların listesini içeren bir JSON belgesi döndürür. [JQ](https://stedolan.github.io/jq/) , `host_name` her ana bilgisayar için öğe değerini ayıklamak üzere kullanılır.

Belirli bir hizmet için düğümün adını bulmanız gerekiyorsa, bu bileşen için ambarı sorgulayabilirsiniz. Örneğin,,,,,,,,,,,,,,,,,,,,,

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Bu komut, hizmeti açıklayan bir JSON belgesi döndürür ve ardından [JQ](https://stedolan.github.io/jq/) yalnızca `host_name` konaklar için değer alır.

## <a name="remote-access-to-services"></a>Hizmetlere uzaktan erişim

* **Ambarı (Web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Küme Yöneticisi Kullanıcı ve parolasını kullanarak kimlik doğrulaması yapın ve ardından ambarı 'nda oturum açın.

    Kimlik doğrulaması düz metin-bağlantının güvenli olduğundan emin olmak için her zaman HTTPS kullanın.

    > [!IMPORTANT]  
    > Web 'den bazıları, iç etki alanı adı kullanılarak ambarı erişim düğümleri aracılığıyla kullanılabilir. İç etki alanı adlarına internet üzerinden genel olarak erişilemez. Internet üzerinden bazı özelliklere erişmeye çalışırken "sunucu bulunamadı" hataları alabilirsiniz.
    >
    > Ambarı Web Kullanıcı arabiriminin tüm işlevlerini kullanmak için, küme baş düğümünde proxy Web trafiği için bir SSH tüneli kullanın. Bkz. [Apache ambarı Web UI, ResourceManager, JobHistory, süs Code, Oozie ve diğer Web UG erişimi IÇIN SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambarı (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Küme Yöneticisi Kullanıcı ve parolasını kullanarak kimlik doğrulaması yapın.
    >
    > Kimlik doğrulaması düz metin-bağlantının güvenli olduğundan emin olmak için her zaman HTTPS kullanın.

* **WebHCat (Temptaton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Küme Yöneticisi Kullanıcı ve parolasını kullanarak kimlik doğrulaması yapın.
    >
    > Kimlik doğrulaması düz metin-bağlantının güvenli olduğundan emin olmak için her zaman HTTPS kullanın.

* 22 veya 23 numaralı bağlantı noktasında **SSH** -clustername-SSH.azurehdinsight.net. 22 numaralı bağlantı noktası, ikincil sunucuya bağlanmak için 23 kullanıldığında birincil headnode 'a bağlanmak için kullanılır. Baş düğümler hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop kümelerinin kullanılabilirliği ve güvenilirliği](./hdinsight-business-continuity.md).

    > [!NOTE]  
    > Küme baş düğümlerine yalnızca bir istemci makinesinden SSH aracılığıyla erişebilirsiniz. Bağlandıktan sonra, bir headnode 'dan SSH kullanarak çalışan düğümlerine erişebilirsiniz.

Daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop Services tarafından kullanılan bağlantı noktaları](hdinsight-hadoop-port-settings-for-services.md) .

## <a name="file-locations"></a>Dosya konumları

Hadoop ile ilgili dosyalar konumundaki küme düğümlerinde bulunabilir `/usr/hdp` . Bu dizin aşağıdaki alt dizinleri içerir:

* **2.6.5.3009-43**: Dizin adı, HDInsight tarafından kullanılan Hadoop platformunun sürümüdür. Kümenizdeki sayı burada listelenenden farklı olabilir.
* **geçerli**: Bu dizin, **2.6.5.3009-43** dizininde bulunan alt dizinlere bağlantılar içerir. Bu dizin, sürüm numarasını anımsamanıza gerek kalmayacak şekilde bulunur.

Örnek veri ve JAR dosyaları, ve ' de Hadoop Dağıtılmış Dosya Sistemi bulunabilir `/example` `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>, Azure depolama ve Data Lake Storage

Çoğu Hadoop dağıtımlarında veriler, Bu, kümedeki makinelerde yerel depolama alanı tarafından desteklenir. Yerel depolama kullanmak, saatlik olarak veya işlem kaynakları için dakikaya göre ücretlendirilebilen bulut tabanlı bir çözüm için maliyetli olabilir.

HDInsight kullanılırken, veri dosyaları Azure Blob depolama ve isteğe bağlı olarak Azure Data Lake Storage 1./Gen2 kullanarak bulutta Uyarlamalı ve dayanıklı bir şekilde depolanır. Bu hizmetler aşağıdaki avantajları sağlar:

* Cheap uzun süreli depolama.
* Web siteleri, dosya yükleme/indirme yardımcı programları, çeşitli dil SDK 'Ları ve Web tarayıcıları gibi dış hizmetlerden erişilebilirlik.
* Büyük dosya kapasitesi ve büyük uyarlanabilir depolama.

Daha fazla bilgi için bkz. [Azure Blob depolama](../storage/common/storage-introduction.md), [Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md)veya [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md).

Azure Blob depolama veya Data Lake Storage 1./Gen2 kullanılırken, HDInsight 'tan verilere erişmek için özel bir şey yapmanız gerekmez. Örneğin, aşağıdaki komut, `/example/data` klasördeki dosyaları Azure depolama 'da mi yoksa Data Lake Storage, mi depolandığını listeler:

```console
hdfs dfs -ls /example/data
```

HDInsight 'ta veri depolama kaynakları (Azure Blob depolama ve Azure Data Lake Storage) işlem kaynaklarından ayrılır. Gerektiğinde hesaplamayı yapmak için HDInsight kümeleri oluşturabilir ve daha sonra iş bittiğinde kümeyi silebilirsiniz. Bu arada, veri dosyalarınızı, ihtiyacınız olduğu sürece Bulut depolamada güvenli bir şekilde tutmaya devam ediyor.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI ve şema

Bazı komutlar bir dosyaya erişirken, şemayı URI 'nin bir parçası olarak belirtmenizi gerektirebilir. Örneğin, fırtınası-TI bileşeni, düzeni belirtmenizi gerektirir. Varsayılan olmayan depolama (depolama alanı, kümeye "ek" depolama olarak eklendi) kullanılırken, her zaman şemayı URI 'nin bir parçası olarak kullanmanız gerekir.

[**Azure depolama**](./hdinsight-hadoop-use-blob-storage.md)'yı KULLANıRKEN aşağıdaki URI düzenlerinden birini kullanın:

* `wasb:///`: Şifrelenmemiş iletişim kullanarak varsayılan depolamaya erişin.

* `wasbs:///`: Şifrelenmiş iletişim kullanarak varsayılan depolamaya erişin.  Inbs şeması yalnızca HDInsight sürüm 3,6 ve sonraki sürümlerde desteklenir.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Varsayılan olmayan bir depolama hesabıyla iletişim kurulurken kullanılır. Örneğin, ek bir depolama hesabınız olduğunda veya genel olarak erişilebilen bir depolama hesabında depolanan verilere erişirken.

[**Azure Data Lake Storage 2.**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)KULLANıRKEN aşağıdaki URI şemasını kullanın:

* `abfs://`: Şifrelenmiş iletişim kullanarak varsayılan depolamaya erişin.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Varsayılan olmayan bir depolama hesabıyla iletişim kurulurken kullanılır. Örneğin, ek bir depolama hesabınız olduğunda veya genel olarak erişilebilen bir depolama hesabında depolanan verilere erişirken.

[**Azure Data Lake Storage 1.**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)kullanırken, aşağıdaki URI düzenlerinden birini kullanın:

* `adl:///`: Küme için varsayılan Data Lake Storage erişin.

* `adl://<storage-name>.azuredatalakestore.net/`: Varsayılan olmayan bir Data Lake Storage iletişim kurulurken kullanılır. Ayrıca HDInsight kümenizin kök dizini dışındaki verilere erişmek için de kullanılır.

> [!IMPORTANT]  
> HDInsight için varsayılan depo olarak Data Lake Storage kullanırken, mağaza dahilinde HDInsight depolamanın kökü olarak kullanılacak bir yol belirtmeniz gerekir. Varsayılan yol `/clusters/<cluster-name>/` .
>
> Verileri kullanırken `/` veya `adl:///` veri erişimi için, yalnızca kümenin kökünde (örneğin,) depolanan verilere erişebilirsiniz `/clusters/<cluster-name>/` . Mağazadaki her yerden veriye erişmek için `adl://<storage-name>.azuredatalakestore.net/` biçimini kullanın.

### <a name="what-storage-is-the-cluster-using"></a>Hangi depolama alanı kullanılarak küme

Kümenin varsayılan depolama yapılandırmasını almak için ambarı 'nı kullanabilirsiniz. Kıvrımlı yapılandırma bilgilerini kıvrımlı kullanarak almak ve [JQ](https://stedolan.github.io/jq/)kullanarak filtrelemek için aşağıdaki komutu kullanın:

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Bu komut `service_config_version=1` , bu bilgileri içeren () sunucusuna uygulanan ilk yapılandırmayı döndürür. En güncel olanı bulmak için tüm yapılandırma sürümlerini listeetmeniz gerekebilir.

Bu komut aşağıdaki URI 'lere benzer bir değer döndürür:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` bir Azure depolama hesabı kullanıyorsanız.

    Hesap adı, Azure depolama hesabının adıdır. Kapsayıcı adı, küme depolamanın kökü olan blob kapsayıcısıdır.

* `adl://home` Azure Data Lake Storage kullanıyorsanız. Data Lake Storage adı almak için aşağıdaki REST çağrısını kullanın:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Bu komut şu ana bilgisayar adını döndürür: `<data-lake-store-account-name>.azuredatalakestore.net` .

    HDInsight için kök olan mağaza içindeki dizini almak için aşağıdaki REST çağrısını kullanın:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Bu komut aşağıdaki yola benzer bir yol döndürür: `/clusters/<hdinsight-cluster-name>/` .

Ayrıca, aşağıdaki adımları kullanarak Azure portal kullanarak depolama bilgilerini bulabilirsiniz:

1. [Azure Portal](https://portal.azure.com/)HDInsight kümenizi seçin.

2. **Özellikler** bölümünde **depolama hesapları**' nı seçin. Küme için depolama bilgileri görüntülenir.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>HDInsight dışından Dosya Nasıl yaparım? erişim

HDInsight kümesi dışından verilere erişmenin çeşitli yolları vardır. Aşağıda, verilerle çalışmak için kullanılabilecek yardımcı programlar ve SDK 'ların birkaç bağlantısı verilmiştir:

__Azure Blob depolama__ kullanılıyorsa verilerinize erişebilmenizin yolları için aşağıdaki bağlantılara bakın:

* [Azure CLI](/cli/azure/install-az-cli2): Azure ile çalışmaya yönelik arabirim komutlarını Command-Line. Yükledikten sonra, `az storage` depolamayı kullanma hakkında yardım için veya `az storage blob` BLOB 'a özgü komutlar için komutunu kullanın.
* [blobxfer.py](https://github.com/Azure/blobxfer): Azure depolama 'da bloblarla çalışmaya yönelik bir Python betiği.
* Çeşitli SDK 'lar:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Depolama REST API’si](/rest/api/storageservices/Blob-Service-REST-API)

__Azure Data Lake Storage 1.__ kullanıyorsanız verilerinize erişebilmenizin yolları için aşağıdaki bağlantılara bakın:

* [Web tarayıcısı](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Web REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Visual Studio için Data Lake araçları](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Kümenizi ölçeklendirme

Küme ölçekleme özelliği, bir küme tarafından kullanılan veri düğümlerinin sayısını dinamik olarak değiştirmenize olanak sağlar. Bir kümede diğer işler veya işlemler çalışırken ölçeklendirme işlemleri yapabilirsiniz.  Bkz. [HDInsight kümelerini ölçeklendirme](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Nasıl yaparım? ton (veya başka bir Hadoop bileşeni) yüklensin mi?

HDInsight Yönetilen bir hizmettir. Azure, kümeyle ilgili bir sorun algılarsa, başarısız olan düğümü silebilir ve bunun yerine geçecek bir düğüm oluşturabilirsiniz. Bunları kümeye el ile yüklediğinizde, bu işlem gerçekleştiğinde kalıcı olmaz. Bunun yerine, [HDInsight betik eylemlerini](hdinsight-hadoop-customize-cluster-linux.md)kullanın. Bir betik eylemi, aşağıdaki değişiklikleri yapmak için kullanılabilir:

* Bir hizmet veya Web sitesi yükleyip yapılandırın.
* Kümedeki birden fazla düğümde yapılandırma değişiklikleri gerektiren bir bileşeni yükler ve yapılandırın.

Komut dosyası eylemleri Bash betikleridir. Betikler, küme oluşturma sırasında çalışır ve ek bileşenleri yüklemek ve yapılandırmak için kullanılır. Kendi Betik Eylemlerinizi geliştirme hakkında daha fazla bilgi için bkz. [HDInsight ile Betik Eylemi geliştirme](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Jar dosyaları

Bazı Hadoop teknolojileri, kendi içinde bulunan jar dosyaları sağlar. Bu dosyalar, MapReduce işinin bir parçası olarak veya Pig ya da Hive içinden kullanılan işlevleri içerir. Bunlar genellikle kurulum gerektirmez ve oluşturulduktan ve doğrudan kullanıldıktan sonra kümeye yüklenebilirler. Bileşenin kümeyi yeniden görüntülemeye çalıştığından emin olmak istiyorsanız jar dosyasını küme varsayılan deposunda depolayın.

Örneğin, en son [Apache DataFu](https://datafu.incubator.apache.org/)sürümünü kullanmak istiyorsanız, projeyi içeren bir jar indirebilir ve bunu HDInsight kümesine yükleyebilirsiniz. Daha sonra Pig veya Hive 'den nasıl kullanılacağına ilişkin veri Fu belgelerini izleyin.

> [!IMPORTANT]  
> Tek başına jar dosyaları olan bazı bileşenler HDInsight ile sağlanır, ancak yolda değildir. Belirli bir bileşeni arıyorsanız, kümenizi kümenizde aramak için aşağıdaki adımları kullanabilirsiniz:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Bu komut, eşleşen her bir jar dosyasının yolunu döndürür.

Bir bileşenin farklı bir sürümünü kullanmak için, ihtiyacınız olan sürümü yükleyin ve bunu işleriniz içinde kullanın.

> [!IMPORTANT]
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenir ve Microsoft Desteği bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Bu durum sorunu çözmeye veya bu teknolojinin derin uzmanlığı bulunan açık kaynaklı teknolojiler için kullanılabilir kanalları size sormaya neden olur. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [Microsoft Q&HDInsight için soru sayfası](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Ayrıca Apache projelerinin üzerinde proje siteleri vardır [https://apache.org](https://apache.org) , örneğin: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache ambarı 'nı kullanarak HDInsight kümelerini yönetme REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile MapReduce işleri kullanma](hadoop/hdinsight-use-mapreduce.md)