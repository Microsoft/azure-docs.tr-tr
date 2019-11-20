---
title: Azure HDInsight kümesinde yavaş veya başarısız bir iş sorunlarını giderme
description: Azure HDInsight kümesinde yavaş veya başarısız bir işi tanılayın ve sorun giderin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 69711f7ac20882617de175b1b90d8df4f2858c4d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498089"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>HDInsight kümesinde yavaş veya başarısız bir iş sorunlarını giderme

HDInsight kümesindeki bir uygulama işlem verileri yavaş çalışıyorsa veya hata kodu ile başarısız olursa, birkaç sorun giderme seçeneğiniz vardır. İşlerinizin beklenenden daha uzun sürmesi veya genel olarak yavaş yanıt süreleri gördüğünüzü, kümenizle ilgili olarak kümenin çalıştırıldığı hizmetler gibi hatalardan oluşan bir akış olabilir. Ancak, bu yavaşlamalara ilişkin en yaygın neden, ölçeklendirmenin yetersizdir. Yeni bir HDInsight kümesi oluşturduğunuzda, uygun [sanal makine boyutlarını](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)seçin.

Yavaş veya başarısız bir kümeyi tanılamak için, ortamın ilişkili Azure Hizmetleri, küme yapılandırması ve iş yürütme bilgileri gibi tüm yönleri hakkında bilgi toplayın. Yararlı bir tanılama, hata durumunu başka bir kümede yeniden oluşturmaya çalışır.

* 1\. Adım: sorunla ilgili verileri toplayın.
* 2\. Adım: HDInsight küme ortamını doğrulayın.
* 3\. Adım: kümenizin sistem durumunu görüntüleyin.
* 4\. Adım: ortam yığınını ve sürümlerini gözden geçirin.
* 5\. Adım: küme günlüğü dosyalarını Inceleyin.
* 6\. Adım: yapılandırma ayarlarını denetleyin.
* 7\. Adım: hatayı farklı bir kümede yeniden oluşturma.

## <a name="step-1-gather-data-about-the-issue"></a>1\. Adım: sorunla ilgili verileri toplama

HDInsight, kümelerle ilgili sorunları belirlemek ve gidermek için kullanabileceğiniz birçok araç sağlar. Aşağıdaki adımlar bu araçlara kılavuzluk eder ve sorunu işaret eden öneriler sağlar.

### <a name="identify-the-problem"></a>Sorunu tanımla

Sorunu belirlemenize yardımcı olması için aşağıdaki soruları göz önünde bulundurun:

* Gerçekleşmem beklendim? Bunun yerine ne oldu?
* İşlemin çalıştırılması ne kadar sürer? Ne kadar süreyle çalışıyor?
* Görevlerim bu kümede her zaman yavaş çalışacak mı? Farklı bir kümede daha hızlı çalışıyor mu?
* Bu sorun ilk kez ne zaman oluşuyor? Bu tarihten bu yana ne sıklıkta gerçekleşti?
* Küme yapılandırmanızda her şey değişti mi?

### <a name="cluster-details"></a>Küme ayrıntıları

Önemli küme bilgileri şunları içerir:

* Küme adı.
* Küme bölgesi- [bölge kesintileri](https://azure.microsoft.com/status/)olup olmadığını denetleyin.
* HDInsight küme türü ve sürümü.
* Baş ve çalışan düğümleri için belirtilen HDInsight örneklerinin türü ve sayısı.

Azure portal bu bilgileri verebilir:

![HDInsight Azure portal bilgileri](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)'yi de kullanabilirsiniz:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Başka bir seçenek PowerShell kullanıyor. Daha fazla bilgi için bkz. [HDInsight 'ta Azure PowerShell Apache Hadoop kümelerini yönetme](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2\. Adım: HDInsight küme ortamını doğrulama

Her HDInsight kümesi çeşitli Azure hizmetlerini ve Apache HBase ve Apache Spark gibi açık kaynaklı yazılımları kullanır. HDInsight kümeleri, Azure sanal ağları gibi diğer Azure hizmetlerinde de çağrı yapabilir.  Küme hatası, kümenizde çalışan hizmetlerden herhangi birinin veya bir dış hizmetin oluşmasına neden olabilir.  Küme hizmeti yapılandırma değişikliği de kümenin başarısız olmasına neden olabilir.

### <a name="service-details"></a>Hizmet ayrıntıları

* Açık kaynaklı kitaplık yayın sürümlerini denetleyin.
* [Azure hizmet kesintilerini](https://azure.microsoft.com/status/)denetleyin.  
* Azure hizmeti kullanım sınırlarını denetleyin. 
* Azure sanal ağ alt ağ yapılandırmasını denetleyin.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ambarı Kullanıcı arabirimi ile küme yapılandırma ayarlarını görüntüleme

Apache ambarı, bir Web Kullanıcı arabirimi ve REST API bir HDInsight kümesinin yönetimini ve izlenmesini sağlar. Ambarı, Linux tabanlı HDInsight kümelerine dahildir. Azure portal HDInsight sayfasında **küme Pano** bölmesini seçin.  Bu Kullanıcı arabirimini açmak için **HDInsight kümesi Pano** bölmesini seçin ve küme oturum açma kimlik bilgilerini girin.  

![Apache ambarı panosuna genel bakış](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Hizmet görünümlerinin bir listesini açmak için Azure portal sayfasındaki **ambarı görünümleri** ' ni seçin.  Bu liste, hangi kitaplıkların yüklü olduğuna bağlıdır. Örneğin, YARN kuyruğu Yöneticisi, Hive görünümü ve tez görünümü ' ne bakabilirsiniz.  Yapılandırma ve hizmet bilgilerini görmek için bir hizmet bağlantısı seçin.

#### <a name="check-for-azure-service-outages"></a>Azure hizmet kesintilerini denetle

HDInsight çeşitli Azure hizmetlerini kullanır. Azure HDInsight üzerinde sanal sunucular çalıştırır, verileri ve betikleri Azure Blob depolama veya Azure Data Lake Storage depolar ve Azure Tablo depolamadaki günlük dosyalarını dizinler. Nadir olarak bu hizmetlere yönelik kesintiler, HDInsight 'ta sorunlara neden olabilir. Kümenizde beklenmedik yavaşlamalar veya hatalardan sahipseniz [Azure Durum Panosu](https://azure.microsoft.com/status/)' nu kontrol edin. Her hizmetin durumu bölgeye göre listelenir. Tüm ilgili hizmetler için kümenizin bölgesini ve ayrıca bölgelerini denetleyin.

#### <a name="check-azure-service-usage-limits"></a>Azure hizmeti kullanım sınırlarını denetle

Büyük bir küme veya aynı anda birçok küme oluşturduysanız, bir Azure hizmet sınırını aştıysanız bir küme başarısız olabilir. Hizmet limitleri, Azure aboneliğinize bağlı olarak farklılık gösterir. Daha fazla bilgi için bkz. [Azure aboneliği ile hizmet limitleri, kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Microsoft 'un kullanılabilir HDInsight kaynakları sayısını (VM çekirdekleri ve VM örnekleri gibi) [Kaynak Yöneticisi çekirdek kota artışı isteği](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)ile artırmayı isteyebilirsiniz.

#### <a name="check-the-release-version"></a>Yayın sürümünü denetleyin

Küme sürümünü en son HDInsight sürümüyle karşılaştırın. Her HDInsight sürümü, yeni uygulamalar, özellikler, düzeltme ekleri ve hata düzeltmeleri gibi iyileştirmeler içerir. Kümenizi etkileyen sorun en son sürüm sürümünde giderilmiş olabilir. Mümkünse, HDInsight 'ın en son sürümünü ve Apache HBase, Apache Spark ve diğerleri gibi ilişkili kitaplıkları kullanarak kümenizi yeniden çalıştırın.

#### <a name="restart-your-cluster-services"></a>Küme hizmetlerinizi yeniden başlatın

Kümenizde yavaşlamalar yaşıyorsanız, hizmet ambarı Kullanıcı arabirimi veya Azure klasik CLı aracılığıyla hizmetlerinizi yeniden başlatmayı düşünün. Küme geçici hatalar yaşıyor olabilir ve yeniden başlatma, ortamınızı sabitetmenin en hızlı yoludur ve büyük olasılıkla performansı geliştirir.

## <a name="step-3-view-your-clusters-health"></a>3\. Adım: kümenizin sistem durumunu görüntüleme

HDInsight kümeleri, sanal makine örneklerinde çalışan farklı düğüm türlerinden oluşur. Her düğüm kaynak sorunu, ağ bağlantısı sorunları ve kümeyi yavaşlatabilecek diğer sorunlar için izlenebilir. Her küme iki baş düğüm içerir ve çoğu küme türü, çalışan ve kenar düğümlerinin bir birleşimini içerir. 

Her küme türünün kullandığı çeşitli düğümlerin bir açıklaması için bkz. [HDInsight 'ta Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasını içeren kümeleri ayarlama](hdinsight-hadoop-provision-linux-clusters.md).

Aşağıdaki bölümlerde, her bir düğümün sistem durumunun ve toplam kümenin nasıl kontrol edilecek anlatılmaktadır.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Ambarı Kullanıcı arabirimi panosunu kullanarak küme durumunun anlık görüntüsünü alma

[Ambarı Kullanıcı arabirimi panosu](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) çalışma süresi, bellek, ağ ve CPU kullanımı, sudisk kullanımı vb. gibi küme durumuna genel bir bakış sağlar. Kaynakları bir konak düzeyinde görüntülemek için, ambarı 'nın konaklar bölümünü kullanın. Ayrıca, hizmetleri durdurup yeniden başlatabilirsiniz.

### <a name="check-your-webhcat-service"></a>WebHCat hizmetinizi denetleyin

Apache Hive, Apache Pig veya Apache Sqoop işleri için bir yaygın senaryo, [Webhcat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (veya *tempkaton*) hizmeti ile başarısız oluyor. WebHCat, Hive, Pig, Scoop ve MapReduce gibi uzak iş yürütmesi için bir REST arabirimidir. WebHCat, iş gönderme isteklerini Apache Hadoop YARN uygulamalarına çevirir ve YARN uygulama durumundan türetilmiş bir durum döndürür.  Aşağıdaki bölümlerde ortak WebHCat HTTP durum kodları açıklanır.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 durum kodu)

Bu kod, ağ geçidi düğümlerinden gelen genel bir iletidir ve en sık karşılaşılan hata durum kodlarıdır. Bunun olası nedenlerinden biri, etkin baş düğümünde WebHCat hizmeti 'nin kapanmasına neden olur. Bu olasılığa yönelik denetlemek için aşağıdaki KıVRıMLı komutunu kullanın:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambarı, WebHCat hizmetinin altında çalıştığı Konakları gösteren bir uyarı görüntüler. Hizmeti ana bilgisayarında yeniden başlatarak WebHCat hizmetini geri getirmeyi deneyebilirsiniz.

![Apache ambarı yeniden başlatma WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Bir WebHCat sunucusu hala gelmezse, hata iletileri için işlem günlüğünü kontrol edin. Daha ayrıntılı bilgi için, düğümünde başvurulan `stderr` ve `stdout` dosyalarını denetleyin.

#### <a name="webhcat-times-out"></a>WebHCat zaman aşımına uğruyor

İki dakikadan uzun bir süre sonra `502 BadGateway`döndüren ağ geçidi zaman aşımı yanıtları An HDInsight. WebHCat iş durumları için YARN hizmetlerini sorgular ve YARN 'nin yanıt vermesi iki dakikadan uzun sürerse, bu istek zaman aşımına uğrar.

Bu durumda `/var/log/webhcat` dizininde aşağıdaki günlükleri gözden geçirin:

* **webhcat. log** , sunucunun günlük yazma Log4J günlüğüne
* **webhcat-Console. log** , başlatıldığında sunucunun stdout
* **webhcat-Console-Error. log** , sunucu işleminin stderr 'i

> [!NOTE]  
> Her `webhcat.log` her gün alınır ve `webhcat.log.YYYY-MM-DD`adlı dosyalar oluşturulur. Araştırmakta olduğunuz zaman aralığı için uygun dosyayı seçin.

Aşağıdaki bölümlerde, WebHCat zaman aşımları için olası bazı nedenler açıklanır.

##### <a name="webhcat-level-timeout"></a>WebHCat düzeyi zaman aşımı

WebHCat yük altındayken, 10 ' dan fazla açık yuvalarla, yeni yuva bağlantıları kurmak daha uzun sürer ve bu da zaman aşımına neden olabilir. WebHCat 'e ve öğesinden ağ bağlantılarını listelemek için, geçerli etkin headnode üzerinde `netstat` kullanın:

```bash
netstat | grep 30111
```

30111, WebHCat 'in dinlediği bağlantı noktasıdır. Açık yuva sayısı 10 ' dan az olmalıdır.

Açık yuva yoksa, önceki komut sonuç üretmez. Templeton 'in bağlantı noktası 30111 ' te up dinlemediğini denetlemek için şunu kullanın:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN düzey zaman aşımı

Templeton, işleri çalıştırmak için YARN çağırır ve Templeton ile YARN arasındaki iletişim zaman aşımına neden olabilir.

YARN düzeyinde iki zaman aşımı türü vardır:

1. YARN işi göndermek zaman aşımına neden olacak kadar uzun sürebilir.

    `/var/log/webhcat/webhcat.log` günlük dosyasını açıp "sıraya alınan iş" arıyorsanız, yürütme zamanının çok uzun (> 2000 MS) olduğu ve artan bekleme sürelerinin gösterildiği girişlerle birlikte birden fazla giriş görebilirsiniz.

    Sıraya alınan işlerin süresi artmaya devam eder, çünkü yeni işlerin gönderildiği oran eski işlerin tamamlandığı hızdan daha yüksektir. YARN belleği kullanılan %100 olduğunda, *joblauncher kuyruğu* artık *varsayılan kuyruktan*kapasite ödünç alabilir. Bu nedenle, joblauncher kuyruğuna daha fazla yeni iş kabul edilemez. Bu davranış, bekleme süresinin daha uzun sürmesine neden olur ve genellikle bir çok zaman aşımı hatasına neden olur.

    Aşağıdaki görüntüde,% 714,4 fazla kullanılan iş başlatıcısı sırası gösterilmektedir. Bu, varsayılan kuyrukta içinden ödünç almak için hala ücretsiz kapasite olduğu için kabul edilebilir. Ancak, küme tam olarak kullanıldığında ve YARN belleği %100 kapasiteye sahip olduğunda, yeni işlerin beklenmesi gerekir ve bu süre sonunda zaman aşımına neden olur.

    ![HDInsight Iş başlatıcısı sıra görünümü](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Bu sorunu çözmek için iki yol vardır: gönderilen yeni işlerin hızını azaltın ya da kümeyi ölçeklendirerek eski işlerin tüketim hızını artırın.

2. YARN işleme uzun sürebilir, bu da zaman aşımları oluşmasına neden olabilir.

    * Tüm işleri Listele: Bu, zaman alıcı bir çağrıdır. Bu çağrı YARN ResourceManager 'dan uygulamaları numaralandırır ve tamamlanan her uygulama için YARN Jobgeçmişini sunucusundan durumu alır. Daha yüksek iş sayısı ile bu çağrı zaman aşımına uğrar.

    * Yedi günden eski olan işleri Listele: HDInsight YARN Jobınsıserver, tamamlanan iş bilgilerini yedi gün (`mapreduce.jobhistory.max-age-ms` değeri) için koruyacak şekilde yapılandırılmıştır. Temizlenen işlerin numaralandırılması girişimi zaman aşımı ile sonuçlanır.

Bu sorunları tanılamak için:

1. Sorun gidermek için UTC zaman aralığını belirleme
2. Uygun `webhcat.log` dosyalarını seçin
3. Bu süre boyunca uyarı ve hata iletilerini arayın

#### <a name="other-webhcat-failures"></a>Diğer WebHCat sorunları

1. HTTP durum kodu 500

    WebHCat 500 döndüğü çoğu durumda, hata iletisi hata hakkındaki ayrıntıları içerir. Aksi takdirde, uyarı ve hata iletileri için `webhcat.log` bakın.

2. İş arızaları

    WebHCat etkileşimlerinin başarılı olduğu, ancak işlerin başarısız olduğu durumlar olabilir.

    Temptaton, iş konsolu çıkışını `statusdir``stderr` olarak toplar ve bu genellikle sorun giderme için kullanışlıdır. `stderr`, gerçek sorgunun YARN uygulama tanımlayıcısını içerir.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4\. Adım: ortam yığınını ve sürümlerini gözden geçirme

Ambarı Kullanıcı arabirimi **yığını ve sürümü** sayfası, Küme Hizmetleri Yapılandırması ve hizmet sürümü geçmişi hakkında bilgi sağlar.  Hatalı Hadoop hizmeti kitaplık sürümleri, küme hatasının nedeni olabilir.  Ambarı Kullanıcı arabiriminde, **yönetici** menüsünü ve ardından **yığınlar ve sürümler**' i seçin.  Hizmet sürümü bilgilerini görmek için sayfada **sürümler** sekmesini seçin:

![Apache ambarı yığını ve sürümleri](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>5\. Adım: günlük dosyalarını Inceleme

Bir HDInsight kümesini oluşturan birçok hizmetten ve bileşenden oluşturulan çok sayıda günlük türü vardır. [Webhcat günlük dosyaları](#check-your-webhcat-service) daha önce açıklanmıştır. Aşağıdaki bölümlerde açıklandığı gibi, kümeinizdeki sorunları daraltmak için araştırıp birkaç kullanışlı günlük dosyası vardır.

* HDInsight kümeleri, büyük bir olasılıkla gönderilen işleri çalıştırmak için birden çok düğümden oluşur. İşler eşzamanlı olarak çalışır, ancak günlük dosyaları sonuçları yalnızca daha erken görüntüleyebilir. HDInsight, ilk olarak tamamlanmayan diğerlerini sonlandırarak yeni görevleri yürütür. Tüm bu etkinlik `stderr` ve `syslog` dosyalarına kaydedilir.

* Betik eylemi günlük dosyaları, kümenizin oluşturulma sürecinde hataları veya beklenmeyen yapılandırma değişikliklerini gösterir.

* Hadoop adımı günlükleri, hata içeren bir adımın parçası olarak başlatılan Hadoop işlerini belirler.

### <a name="check-the-script-action-logs"></a>Betik eylemi günlüklerini denetleyin

HDInsight [betik eylemleri](hdinsight-hadoop-customize-cluster-linux.md) küme üzerinde el ile veya belirtilen durumlarda betikleri çalıştırır. Örneğin, betik eylemleri kümeye ek yazılım yüklemek veya varsayılan değerlerden yapılandırma ayarlarını değiştirmek için kullanılabilir. Betik eylemi günlüklerinin denetlenmesi, küme kurulumu ve yapılandırması sırasında oluşan hatalara ilişkin öngörüler sağlayabilir.  Bir betik eyleminin durumunu, ambarı Kullanıcı arabirimindeki **Ops** düğmesini seçerek veya varsayılan depolama hesabından günlüklere erişerek görüntüleyebilirsiniz.

Betik eylemi günlükleri `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` dizininde bulunur.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ambarı hızlı bağlantıları kullanarak HDInsight günlüklerini görüntüleme

HDInsight ambarı Kullanıcı arabirimi, bir dizi **hızlı bağlantı** bölümünü içerir.  HDInsight kümenizdeki belirli bir hizmet için günlük bağlantılarına erişmek üzere kümeniz için ambarı Kullanıcı arabirimini açın ve soldaki listeden hizmet bağlantısını seçin. **Hızlı bağlantılar** açılan listesini, ardından ilgilendiğiniz HDInsight düğümünü ve ardından ilgili günlüğün bağlantısını seçin.

Örneğin, bu günlük için:

![Günlük dosyaları için ambarı hızlı bağlantıları](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop tarafından oluşturulan günlük dosyalarını görüntüle

An HDInsight küme, Azure tablolarına ve Azure Blob depolamaya yazılan günlükler oluşturur. YARN kendi yürütme günlüklerini oluşturur. Daha fazla bilgi için bkz. [HDInsight kümesi için günlükleri yönetme](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Yığın dökümlerini gözden geçirme

Yığın dökümleri, çalışma zamanında oluşan sorunları tanılamak için yararlı olan, bu zaman değişkenlerin değerleri de dahil olmak üzere uygulama belleğinin bir anlık görüntüsünü içerir. Daha fazla bilgi için bkz. [Linux tabanlı HDInsight 'ta Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirme](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>6\. Adım: yapılandırma ayarlarını denetleme

HDInsight kümeleri, Hadoop, Hive, HBase gibi ilgili hizmetler için varsayılan ayarlarla önceden yapılandırılmıştır. Kümenin türüne, donanım yapılandırmasına, düğüm sayısına, çalıştırdığınız iş türlerine ve birlikte çalıştığınız verilere (ve bu verilerin nasıl işlenediğine) bağlı olarak, yapılandırmanızı iyileştirmeniz gerekebilir.

Çoğu senaryo için performans yapılandırmalarının en iyi duruma getirilmesi hakkında ayrıntılı yönergeler için bkz. [Apache ambarı ile küme yapılandırmasını iyileştirme](hdinsight-changing-configs-via-ambari.md). Spark kullanırken bkz. [performans için Apache Spark Işlerini iyileştirme](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7\. Adım: hatayı farklı bir kümede yeniden oluşturma

Bir küme hatasının kaynağını tanılamaya yardımcı olmak için, aynı yapılandırmaya sahip yeni bir küme başlatın ve sonra başarısız işin adımlarını tek tek yeniden gönderin. Bir sonrakini işlemeden önce her adımın sonuçlarını denetleyin. Bu yöntem, tek bir başarısız adımı düzeltmek ve yeniden çalıştırmak için fırsat sağlar. Bu yöntem aynı zamanda giriş verilerinizi yalnızca bir kez yükleme avantajına sahiptir.

1. Başarısız kümeyle aynı yapılandırmaya sahip yeni bir test kümesi oluşturun.
2. İlk iş adımını test kümesine gönder.
3. Adım işlemeyi tamamladığında, adım günlük dosyalarındaki hataları kontrol edin. Test kümesinin ana düğümüne bağlanın ve günlük dosyalarını burada görüntüleyin. Adım günlük dosyaları yalnızca adım çalıştıktan sonra bir süre sonra görüntülenir, tamamlanır veya başarısız olur.
4. İlk adım başarılı olduysa, sonraki adımı çalıştırın. Hatalar varsa, günlük dosyalarındaki hatayı araştırın. Kodunuzda bir hata olduysa, düzeltmeyi yapın ve adımı yeniden çalıştırın.
5. Tüm adımlar hatasız olarak çalıştırılıncaya kadar devam edin.
6. Test kümesinde hata ayıklamayı bitirdiğinizde silin.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache Ambari Web arabiriminden yönetme](hdinsight-hadoop-manage-ambari.md)
* [HDInsight günlüklerini çözümleme](hdinsight-debug-jobs.md)
* [Linux tabanlı HDInsight 'ta Apache Hadoop YARN uygulama oturum açma erişimi](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Linux tabanlı HDInsight 'ta Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirme](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight üzerinde Apache Spark kümesi için bilinen sorunlar](hdinsight-apache-spark-known-issues.md)
