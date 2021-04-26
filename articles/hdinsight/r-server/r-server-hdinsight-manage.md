---
title: HDInsight 'ta ML hizmetleri kümesini yönetme-Azure
description: Azure HDInsight 'ta ML Hizmetleri kümesindeki çeşitli görevleri yönetmeyi öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/19/2019
ms.openlocfilehash: a5e623b0429194db6d03beb674679bd10e337844
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869488"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight 'ta ML hizmetleri kümesini yönetme

Bu makalede, birden çok eşzamanlı kullanıcı ekleme, bir ML Hizmetleri kümesine uzaktan bağlanma, işlem bağlamını değiştirme gibi görevleri gerçekleştirmek için Azure HDInsight 'ta mevcut bir ML hizmetleri kümesini yönetmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir ML Hizmetleri kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü** için **ml Hizmetleri** seçme.

* Güvenli Kabuk (SSH) istemcisi: HDInsight kümesine uzaktan bağlanmak ve komutları doğrudan küme üzerinde çalıştırmak için bir SSH istemcisi kullanılır. Daha fazla bilgi için bkz [. HDInsight Ile SSH kullanma.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Birden çok eş zamanlı kullanıcı etkinleştirme

RStudio Community sürümünün çalıştığı Edge düğümüne daha fazla kullanıcı ekleyerek HDInsight üzerinde ML Hizmetleri kümesi için birden fazla eşzamanlı kullanıcıyı etkinleştirebilirsiniz. Bir HDInsight kümesi oluşturduğunuzda bir HTTP kullanıcısı ve bir SSH kullanıcısı olmak üzere iki kullanıcı sağlamanız gerekir:

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users1.png" alt-text="HDI Azure portal oturum açma parametreleri" border="true":::

- **Küme oturum açma kullanıcı adı**: Oluşturduğunuz HDInsight kümelerini korumak için kullanılan HDInsight ağ geçidinden kimlik doğrulaması yapmak için kullanılan HTTP kullanıcısı. Bu HTTP kullanıcısı, Apache ambarı Kullanıcı arabirimine, Apache Hadoop YARN Kullanıcı arabirimine ve diğer UI bileşenlerine erişmek için kullanılır.
- **Secure Shell (SSH) Kullanıcı adı**: güvenli kabuk aracılığıyla kümeye erişmek IÇIN bir SSH kullanıcısı. Bu kullanıcı Linux sisteminde tüm baş düğümler, çalışan düğümleri ve kenar düğümler için kullanılan kullanıcıdır. Bu sayede uzak kümedeki düğümlere erişmek için Secure Shell kullanabilirsiniz.

HDInsight 'ta ML Hizmetleri kümesinde kullanılan R Studio Server Community sürümü, oturum açma mekanizması olarak yalnızca Linux Kullanıcı adı ve parolasını kabul eder. Belirteç iletmeyi desteklemez. Bu nedenle, bir ML Hizmetleri kümesinde R Studio 'Yu ilk kez erişmeye çalıştığınızda iki kez oturum açmanız gerekir.

- İlk olarak HDInsight ağ geçidi üzerinden HTTP Kullanıcı kimlik bilgilerini kullanarak oturum açın.

- Ardından, RStudio 'da oturum açmak için SSH kullanıcı kimlik bilgilerini kullanın.
  
Şu anda bir HDInsight kümesi sağlanırken yalnızca bir SSH kullanıcı hesabı oluşturulabilir. Bu nedenle, HDInsight 'ta ML Hizmetleri kümesine birden çok kullanıcının erişmesini sağlamak için Linux sisteminde ek kullanıcılar oluşturmanız gerekir.

RStudio kümenin Edge düğümünde çalıştığı için burada birkaç adım vardır:

1. Edge düğümünde oturum açmak için mevcut SSH kullanıcısını kullanma
2. Kenar düğümüne daha fazla Linux kullanıcısı ekleme
3. RStudio Topluluk sürümünü oluşturulan kullanıcıyla kullanma

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>1. Adım: kenar düğümünde oturum açmak için oluşturulan SSH kullanıcısını kullanma

Edge düğümüne erişmek için [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) konusundaki yönergeleri izleyin. HDInsight üzerinde ML Hizmetleri kümesi için kenar düğümü adresi `CLUSTERNAME-ed-ssh.azurehdinsight.net` .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2. Adım: Kenar düğümüne daha fazla Linux kullanıcısı ekleme

Kenar düğümüne bir kullanıcı eklemek için şu komutları çalıştırın:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

Aşağıdaki ekran görüntüsünde çıktılar gösterilmektedir.

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users2.png" alt-text="ekran görüntüsü çıkışı eşzamanlı kullanıcıları" border="true":::

"Geçerli Kerberos parolası:" sorulduğunda, bunu yoksaymak için yalnızca **ENTER** tuşuna basın. `useradd` komutundaki `-m` seçeneği, sistemin RStudio Topluluk sürümü için gerekli olan kullanıcı ana klasörünü oluşturacağını belirtir.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3. Adım: RStudio Topluluk sürümünü oluşturulan kullanıcıyla kullanma

RStudio 'dan ' ye erişin `https://CLUSTERNAME.azurehdinsight.net/rstudio/` . Kümeyi oluşturduktan sonra ilk kez oturum açıyorsanız, Küme Yöneticisi kimlik bilgilerini ve ardından oluşturduğunuz SSH kullanıcı kimlik bilgilerini girin. İlk oturum açma işlemi değilse, yalnızca oluşturduğunuz SSH kullanıcısına ait kimlik bilgilerini girin.

Ayrıca, başka bir tarayıcı penceresinden aynı anda özgün kimlik bilgilerini (varsayılan olarak *sshuser*) kullanarak da oturum açabilirsiniz.

Ayrıca yeni eklenen kullanıcıların Linux sisteminde kök ayrıcalıklarına sahip olmadığına ancak uzak HDFS ve WASB depolama alanındaki tüm dosyalara aynı düzeyde erişime sahip olduğuna dikkat edin.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Microsoft ML hizmetlerine uzaktan bağlanma

Masaüstünüzde çalışan bir ML Istemcisinin uzak örneğinden HDInsight Spark işlem bağlamına erişimi ayarlayabilirsiniz. Bunu yapmak için, masaüstünüzde RxSpark işlem bağlamını tanımlarken (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches ve sshProfileScript) seçeneklerini belirtmeniz gerekir: Örneğin:

```r
myNameNode <- "default"
myPort <- 0

mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
mySshUsername  <- '<sshuser>'# HDI SSH username
mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

mySparkCluster <- RxSpark(
    hdfsShareDir = myhdfsShareDir,
    shareDir     = myShareDir,
    sshUsername  = mySshUsername,
    sshHostname  = mySshHostname,
    sshSwitches  = mySshSwitches,
    sshProfileScript = '/etc/profile',
    nameNode     = myNameNode,
    port         = myPort,
    consoleOutput= TRUE
)
```

Daha fazla bilgi için, [Apache Spark işlem bağlamında](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) geri alma kullanma konusunun "Apache Hadoop istemci olarak Microsoft Machine Learning Server kullanma" bölümüne bakın.

## <a name="use-a-compute-context"></a>İşlem bağlamı kullanma

Bir işlem bağlamı, hesaplamanın kenar düğümünde yerel olarak yapılmasını veya HDInsight kümesindeki düğümlere dağıtılmasını denetlemenize olanak tanır.  RStudio Server ile bir işlem bağlamı ayarlamaya ilişkin bir örnek için bkz. [Azure HDInsight 'ta BIR ml Hizmetleri kümesinde rstudio sunucusu kullanarak R betiği yürütme](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>R kodunu birden fazla düğüme dağıtma

HDInsight üzerinde ML hizmetleri sayesinde, var olan R kodunu alabilir ve kullanarak kümedeki birden çok düğüm arasında çalıştırabilirsiniz `rxExec` . Bu işlev bir parametre tarama veya benzetme işlemi sırasında yararlıdır. `rxExec` kullanımını gösteren bir kod örneği aşağıda verilmiştir:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Spark bağlamını hala kullanıyorsanız, bu komut kodun çalıştırıldığı çalışan düğümlerinin düğüler değerini döndürür `(Sys.info()["nodename"])` . Örneğin, dört düğümlü bir kümede aşağıdaki kod parçacığına benzer bir çıktı almayı düşünüyorsunuz:

```r
$rxElem1
    nodename
"wn3-mymlser"

$rxElem2
    nodename
"wn0-mymlser"

$rxElem3
    nodename
"wn3-mymlser"

$rxElem4
    nodename
"wn3-mymlser"
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Apache Hive ve Parquet içindeki verilere erişin

HDInsight ML Hizmetleri, verileri, Spark işlem bağlamındaki ScaleR işlevleri tarafından kullanılmak üzere Hive ve Parquet içindeki verilere doğrudan erişime olanak tanır. Bu özellikler, ScaleR tarafından analiz edilmek üzere bir Spart DataFrame’e doğrudan veri yüklemek için Spark SQL kullanarak çalışan RxHiveData ve RxParquetData adlı yeni ScaleR veri kaynağı işlevleriyle kullanılabilir.

Yeni işlevlerin kullanımına ilişkin bazı örnek kodlar aşağıdaki kod ile verilmiştir:

```r
#Create a Spark compute context:
myHadoopCluster <- rxSparkConnect(reset = TRUE)

#Retrieve some sample data from Hive and run a model:
hiveData <- RxHiveData("select * from hivesampletable",
                       colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)

#Retrieve some sample data from Parquet and run a model:
rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                        colInfo = list(
                            age    = list(type = "factor"),
                            car.age = list(type = "factor"),
                            type = list(type = "factor")
                        ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)

#Check on Spark data objects, cleanup, and close the Spark session:
lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Bu yeni işlevlerin kullanımına ilişkin ek bilgi için, ve komutlarının kullanımıyla ML hizmetlerindeki çevrimiçi yardıma bakın `?RxHivedata` `?RxParquetData` .  

## <a name="install-additional-r-packages-on-the-cluster"></a>Kümeye ek R paketleri yükler

### <a name="to-install-r-packages-on-the-edge-node"></a>Uç düğümüne R paketleri yüklemek için

Kenar düğümüne ek R paketleri yüklemek isterseniz, `install.packages()` SSH aracılığıyla Edge düğümüne bağlandıktan sonra doğrudan r konsolu içinden kullanabilirsiniz. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Çalışan düğümüne R paketleri yüklemek için

Kümenin çalışan düğümlerine R paketleri yüklemek için bir betik eylemi kullanmanız gerekir. Betik Eylemleri, HDInsight kümesinde yapılandırma değişiklikleri yapmak veya ek R paketleri gibi ek yazılımlar yüklemek için kullanılan Bash betikleridir. 

> [!IMPORTANT]  
> Ek R paketleri yüklemek için Betik Eylemleri yalnızca küme oluşturulduktan sonra kullanılabilir. Betik, tam olarak yapılandırılmış olan ML hizmetlerini temel aldığından, bu yordamı küme oluşturma sırasında kullanmayın.

1. [Betiği kullanarak kümeleri özelleştirme](../hdinsight-hadoop-customize-cluster-linux.md)bölümündeki adımları izleyin.

3. **Betiği gönder eylemi** için aşağıdaki bilgileri sağlayın:

   * **Betik türü** için **özel**' i seçin.

   * **Ad** için, betik eylemi için bir ad sağlayın.

     * **Bash betiği URI 'si** için girin `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` . Bu, çalışan düğümüne ek R paketleri yükleyen betiğtir

   * Yalnızca **çalışan** için onay kutusunu seçin.

   * **Parametreler**: Yüklenecek R paketleri. Örneğin, `bitops stringr arules`

   * **Bu betik eylemini kalıcı hale** getirmek için onay kutusunu işaretleyin.  

   > [!NOTE]
   > 1. Varsayılan olarak, tüm R paketleri Microsoft MRAN deposunun yüklü olan ML Server sürümüyle tutarlı bir anlık görüntüsünden yüklenir. Paketlerin daha yeni sürümlerini yüklemek istiyorsanız uyumsuzluk riskiyle karşı karşıya kalabilirsiniz. Ancak bu tür yüklemeleri paket listesinin ilk öğesi olarak `useCRAN` belirleyerek mümkün kılabilirsiniz, örneğin `useCRAN bitops, stringr, arules`.  
   > 2. Bazı R paketleri için ek Linux sistem kitaplıkları gerekir. HDInsight ML Hizmetleri, kolaylık sağlaması için en popüler 100 R paketi için gereken bağımlılıklarla önceden yüklenmiş olarak sunulur. Ancak, yüklediğiniz R paketleri bunların dışında kitaplıklar gerektirirse, burada kullanılan temel betiği indirmeniz ve adımları ekleyerek sistem kitaplıklarını yüklemeniz gerekir. Ardından, değiştirilmiş betiği Azure depolama hizmetindeki ortak bir blob kapsayıcıya yüklemeniz ve değiştirilmiş betiği kullanarak paketleri yüklemeniz gerekir.
   >    Betik eylemleri geliştirme hakkında daha fazla bilgi için bkz. [betik eylemi geliştirme](../hdinsight-hadoop-script-actions-linux.md).

   :::image type="content" source="./media/r-server-hdinsight-manage/submit-script-action.png" alt-text="Betik eylemini Azure portal gönder" border="true":::

4. Betiği çalıştırmak için **Oluştur**’u seçin. Betik tamamlandıktan sonra R paketleri tüm çalışan düğümlerinde kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde ML Services kümesini kullanıma hazır hale getirme](r-server-operationalize.md)
* [HDInsight üzerinde ML hizmeti kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [HDInsight üzerinde ML Services kümesi için Azure Depolama seçenekleri](r-server-storage.md)