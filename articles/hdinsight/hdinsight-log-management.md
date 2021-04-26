---
title: HDInsight kümesi için günlükleri yönetme-Azure HDInsight
description: HDInsight etkinlik günlüğü dosyaları için türleri, boyutları ve bekletme ilkelerini belirleme.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 20a7a7392070a6f888add630398516b2f872cd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865494"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>HDInsight kümesi için günlükleri yönetme

An HDInsight küme çeşitli günlük dosyaları oluşturur. Örneğin, Apache Spark gibi Apache Hadoop ve ilgili hizmetler ayrıntılı iş yürütme günlükleri üretir. Günlük dosyası yönetimi, sağlıklı bir HDInsight kümesinin bakımının bir parçasıdır. Günlük arşivleme için yasal gereksinimler de olabilir.  Günlük dosyalarının sayısı ve boyutu nedeniyle, günlük depolama ve arşivlemeyi iyileştirmek hizmet maliyeti yönetimine yardımcı olur.

HDInsight küme günlüklerinin yönetilmesi, küme ortamının tüm yönleri hakkında bilgi saklamaya dahildir. Bu bilgiler, tüm ilişkili Azure hizmet günlüklerini, küme yapılandırmasını, iş yürütme bilgilerini, tüm hata durumlarını ve gerektiğinde diğer verileri içerir.

HDInsight günlük yönetiminde tipik adımlar şunlardır:

* 1. Adım: günlük tutma ilkelerini belirleme
* 2. Adım: küme hizmeti sürümlerini yapılandırma günlüklerini yönetme
* 3. Adım: küme işi yürütme günlük dosyalarını yönetme
* 4. Adım: günlük birimi depolama boyutlarını ve maliyetlerini tahmin etme
* 5. Adım: günlük Arşivi ilkelerini ve süreçlerini belirleme

## <a name="step-1-determine-log-retention-policies"></a>1. Adım: günlük tutma ilkelerini belirleme

HDInsight küme günlüğü yönetim stratejisi oluşturmanın ilk adımı, iş senaryoları ve iş yürütme geçmişi depolama gereksinimleri hakkında bilgi toplamaktır.

### <a name="cluster-details"></a>Küme ayrıntıları

Aşağıdaki küme ayrıntıları, günlük yönetimi stratejinizde bilgi toplamaya yardımcı olması için kullanışlıdır. Bu bilgileri belirli bir Azure hesabında oluşturduğunuz tüm HDInsight kümelerinden toplayın.

* Küme adı
* Küme bölgesi ve Azure kullanılabilirlik bölgesi
* Son durum değişikliğinin ayrıntıları dahil olmak üzere küme durumu
* Ana, çekirdek ve görev düğümleri için belirtilen HDInsight örneklerinin türü ve sayısı

Azure portal kullanarak bu en üst düzey bilgilerden çoğunu alabilirsiniz.  Alternatif olarak, HDInsight kümeniz hakkında bilgi almak için [Azure CLI](/cli/azure/) 'yi de kullanabilirsiniz:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Bu bilgileri görüntülemek için PowerShell de kullanabilirsiniz.  Daha fazla bilgi için bkz. [Azure PowerShell kullanarak HDInsight 'Ta Hadoop kümelerini yönetme](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Kümelerinizde çalışan iş yüklerini anlayın

Her tür için uygun günlük stratejilerini tasarlamak üzere HDInsight kümenizde çalışan iş yükü türlerinin anlaşılması önemlidir.

* İş yükleri deneysel (geliştirme veya test gibi) veya üretim kalitesi mi?
* Üretim kalitesinde iş yükleri normalde ne sıklıkla çalıştırılır?
* İş yükleri kaynağı yoğun ve/veya uzun süredir çalışıyor mu?
* İş yüklerinden herhangi biri, birden çok günlük türü üretilmekte olan karmaşık bir Hadoop Hizmetleri kümesi kullanıyor mu?
* İş yüklerinden herhangi birinin, mevzuat yürütme kökenini gereksinimlerini ilişkilendirdi mi?

### <a name="example-log-retention-patterns-and-practices"></a>Örnek günlük tutma desenleri ve uygulamaları

* Her günlük girişine bir tanımlayıcı ekleyerek veya diğer teknikler aracılığıyla veri kökenini izlemeyi koruyun. Bu, verilerin özgün kaynağını ve işlemini izlemenizi ve bunların tutarlılığını ve geçerliliğini anlamak için her aşamada verileri izlemenizi sağlar.

* Kümeden veya birden fazla kümeden günlükleri nasıl toplayacağınızı veya denetim, izleme, planlama ve uyarı gibi amaçlarla bunları harmanlamanızı düşünün. Günlük dosyalarına düzenli olarak erişmek ve bunları indirmek için özel bir çözüm kullanabilir ve bunları bir pano görüntüsü sağlamak üzere birleştirebilir ve çözümleyebilirsiniz. Ayrıca, güvenlik veya hata algılama için uyarı için ek yetenekler de ekleyebilirsiniz. Bu yardımcı programları PowerShell, HDInsight SDK 'Ları veya Azure klasik dağıtım modeline erişen kodu kullanarak oluşturabilirsiniz.

* Bir izleme çözümünün veya hizmetinin yararlı bir avantaj olup olmayacağını göz önünde bulundurun. Microsoft System Center bir [HDInsight yönetim paketi](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259)sağlar. Günlükleri toplamak ve merkezileştirmek için Apache Chukwa ve Gana gibi üçüncü taraf araçları da kullanabilirsiniz. Birçok şirket, Hadoop tabanlı büyük veri çözümlerini izlemek için hizmet sunar, örneğin: Centerity, Compuware APM, şeması metin SPM ve zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>2. Adım: küme hizmeti sürümlerini yönetme ve günlükleri görüntüleme

Tipik bir HDInsight kümesi, çeşitli hizmetleri ve açık kaynaklı yazılım paketlerini (Apache HBase, Apache Spark vb.) kullanır. Biyotika gibi bazı iş yükleri için, iş yürütme günlüklerine ek olarak hizmet yapılandırma günlüğü geçmişini saklamanız gerekebilir.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ambarı Kullanıcı arabirimi ile küme yapılandırma ayarlarını görüntüleme

Apache ambarı, bir Web Kullanıcı arabirimi ve REST API sağlayarak bir HDInsight kümesinin yönetimini, yapılandırılmasını ve izlenmesini basitleştirir. Ambarı, Linux tabanlı HDInsight kümelerine dahildir. Azure portal HDInsight sayfasında **küme Pano** bölmesini seçerek **küme panoları** bağlantı sayfasını açın.  Ardından, yeni kullanıcı arabirimi ' ni açmak için **HDInsight kümesi Pano** bölmesini seçin.  Sizden küme oturum açma kimlik bilgileriniz istenir.

Hizmet görünümlerinin listesini açmak için HDInsight için Azure portal sayfasındaki **ambarı görünümleri** bölmesini seçin.  Bu liste, yüklediğiniz kitaplıklara bağlı olarak farklılık gösterir.  Örneğin, YARN kuyruğu Yöneticisi, Hive görünümü ve tez görünümü ' ne bakabilirsiniz.  Yapılandırma ve hizmet bilgilerini görmek için herhangi bir hizmet bağlantısı seçin.  Ambarı Kullanıcı arabirimi **yığını ve sürümü** sayfasında, küme hizmetleri yapılandırma ve hizmet sürümü geçmişi hakkında bilgi sağlanır. Ambarı Kullanıcı arabiriminin bu bölümüne gitmek için **yönetici** menüsünü ve ardından **yığınlar ve sürümler**' i seçin.  Hizmet sürümü bilgilerini görmek için **sürümler** sekmesini seçin.

:::image type="content" source="./media/hdinsight-log-management/ambari-stack-versions.png" alt-text="Apache ambarı yönetici yığını ve sürümleri":::

Ambarı Kullanıcı arabirimini kullanarak, kümedeki belirli bir konakta (veya düğümünde) çalışan tüm (veya tüm) hizmetlerin yapılandırmasını indirebilirsiniz.  **Konaklar** menüsünü ve ardından ilgilendiğiniz konağın bağlantısını seçin. Bu konağın sayfasında, **konak eylemleri** düğmesini seçin ve ardından **istemci yapılandırması**' nı indirin.

:::image type="content" source="./media/hdinsight-log-management/download-client-configs.png" alt-text="Apache ambarı indirme konak istemci yapılandırması":::

### <a name="view-the-script-action-logs"></a>Betik eylemi günlüklerini görüntüleme

HDInsight [betik eylemleri](hdinsight-hadoop-customize-cluster-linux.md) , el ile veya belirtildiğinde bir kümede betikleri çalıştırır. Örneğin, betik eylemleri kümeye ek yazılım yüklemek veya varsayılan değerlerden yapılandırma ayarlarını değiştirmek için kullanılabilir. Betik eylemi günlükleri, kümenin kurulumu sırasında oluşan hatalara ilişkin Öngörüler ve ayrıca küme performansını ve kullanılabilirliğini etkileyebilecek yapılandırma ayarları ' değişikliklerini sağlayabilir.  Bir betik eyleminin durumunu görmek için, ambarı Kullanıcı arabiriminizdeki **Ops** düğmesini seçin veya varsayılan depolama hesabındaki durum günlüklerine erişin. Depolama günlükleri ' nde kullanılabilir `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Ambarı uyarıları durum günlüklerini görüntüle

Apache ambarı, uyarı durumu değişikliklerini öğesine yazar `ambari-alerts.log` . Tam yol `/var/log/ambari-server/ambari-alerts.log` . Günlük için hata ayıklamayı etkinleştirmek üzere, Değiştir ' de bir özelliği şu şekilde değiştirin `/etc/ambari-server/conf/log4j.properties.` `# Log alert state changes` :

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3. Adım: küme işi yürütme günlük dosyalarını yönetme

Sonraki adım çeşitli hizmetlere yönelik iş yürütme günlüğü dosyalarını gözden geçiriyorsunuz.  Hizmetler Apache HBase, Apache Spark ve diğer birçok tane içerebilir. Bir Hadoop kümesi çok sayıda ayrıntılı günlük üretir, bu nedenle hangi günlüklerin yararlı (ve olmayan) zaman alabilir olduğunu belirleyin.  Günlük sistemini anlamak, günlük dosyalarının hedeflenen yönetimi için önemlidir.  Aşağıdaki görüntü, örnek bir günlük dosyasıdır.

:::image type="content" source="./media/hdinsight-log-management/hdi-log-file-example.png" alt-text="HDInsight örnek günlük dosyası örnek çıktısı":::

### <a name="access-the-hadoop-log-files"></a>Hadoop günlük dosyalarına erişin

HDInsight, günlük dosyalarını hem küme dosyası sisteminde hem de Azure Storage 'da depolar. Kümeye bir [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) bağlantısı açıp dosya sistemine gözatıp veya uzak baş düğüm sunucusunda Hadoop Yarn durum portalını kullanarak kümedeki günlük dosyalarını inceleyebilirsiniz. Azure depolama 'daki verileri erişebilecek ve indirebileceğiniz araçlardan herhangi birini kullanarak Azure Storage 'daki günlük dosyalarını inceleyebilirsiniz. Örnekler [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Cloudxplorer](https://clumsyleaf.com/products/cloudxplorer)ve Visual Studio Sunucu Gezgini. Ayrıca, Azure Blob depolama alanındaki verilere erişmek için PowerShell ve Azure depolama Istemci kitaplıklarını veya Azure .NET SDK 'larını de kullanabilirsiniz.

Hadoop, kümedeki çeşitli düğümlerde *görev denemeleri* olarak işlerin çalışmasını çalıştırır. HDInsight, ilk olarak tamamlanmamış diğer görev girişimlerini sonlandırarak, öngörülebilir görev denemeleri başlatabilir. Bu, anında denetleyici, stderr ve Syslog günlük dosyalarına kaydedilen önemli bir etkinlik oluşturur. Ayrıca, birden çok görev denemesi aynı anda çalışır, ancak bir günlük dosyası sonuçları yalnızca daha erken görüntüleyebilir.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Azure Blob depolamaya yazılan HDInsight günlükleri

HDInsight kümeleri, Azure PowerShell cmdlet 'leri veya .NET iş gönderme API 'Leri kullanılarak gönderilen herhangi bir iş için bir Azure Blob depolama hesabına görev günlükleri yazmak üzere yapılandırılır.  Kümeye SSH aracılığıyla iş gönderirseniz, yürütme günlüğü bilgileri önceki bölümde anlatıldığı gibi Azure tablolarında depolanır.

HDInsight tarafından oluşturulan çekirdek günlük dosyalarına ek olarak, YARN gibi yüklü hizmetler de iş yürütme günlük dosyaları oluşturur.  Günlük dosyalarının sayısı ve türü yüklü hizmetlere bağlıdır.  Ortak hizmetler Apache HBase, Apache Spark ve benzeri.  Kümenizdeki tüm günlük dosyalarını anlamak için her hizmet için iş günlüğü yürütme dosyalarını araştırın.  Her hizmetin kendi benzersiz günlük yöntemleri ve günlük dosyalarını depolamak için konumları vardır.  Örnek olarak, en yaygın hizmet günlük dosyalarına (YARN 'den) erişme ayrıntıları aşağıdaki bölümde açıklanmaktadır.

### <a name="hdinsight-logs-generated-by-yarn"></a>YARN tarafından oluşturulan HDInsight günlükleri

YARN, bir çalışan düğümündeki tüm kapsayıcılar arasında günlükleri toplar ve bu günlükleri çalışan düğümü başına bir toplu günlük dosyası olarak depolar. Bu günlük, bir uygulama bittikten sonra varsayılan dosya sisteminde depolanır. Uygulamanız yüzlerce veya binlerce kapsayıcı kullanabilir, ancak tek bir çalışan düğümünde çalıştırılan tüm kapsayıcılar için Günlükler her zaman tek bir dosyaya toplanır. Uygulamanız tarafından kullanılan her çalışan düğümü için yalnızca bir günlük vardır. Günlük toplama, HDInsight kümeleri sürüm 3,0 ve üzerinde varsayılan olarak etkindir. Toplanan Günlükler, küme için varsayılan depolamada bulunur.

```
/app-logs/<user>/logs/<applicationId>
```

Toplanan Günlükler, kapsayıcı tarafından dizini oluşturulmuş bir TFile ikili biçiminde yazıldığı için doğrudan okunabilir değildir. Bu günlükleri uygulamalar veya ilgilendiğiniz kapsayıcılar için düz metin olarak görüntülemek üzere YARN ResourceManager günlüklerini veya CLı araçlarını kullanın.

#### <a name="yarn-cli-tools"></a>YARN CLı araçları

YARN CLı araçlarını kullanmak için önce SSH kullanarak HDInsight kümesine bağlanmanız gerekir. `<applicationId>` `<user-who-started-the-application>` `<containerId>` Bu komutları çalıştırırken,, ve `<worker-node-address>` bilgilerini belirtin. Günlükleri aşağıdaki komutlardan birini kullanarak düz metin olarak görebilirsiniz:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager Kullanıcı arabirimi

YARN ResourceManager Kullanıcı arabirimi küme baş düğümünde çalışır ve bu, ambarı Web Kullanıcı arabirimi üzerinden erişilir. YARN günlüklerini görüntülemek için aşağıdaki adımları kullanın:

1. Bir web tarayıcısında `https://CLUSTERNAME.azurehdinsight.net` sayfasına gidin. CLUSTERNAME değerini HDInsight kümenizin adıyla değiştirin.
2. Soldaki hizmetler listesinden YARN ' yi seçin.
3. Hızlı bağlantılar açılan listesinden küme baş düğümlerinden birini seçin ve ardından **ResourceManager günlükleri**' ni seçin. YARN günlüklerine bağlantıların bir listesini görürsünüz.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4. Adım: günlük birimi depolama boyutlarını ve maliyetlerini tahmin etme

Önceki adımları tamamladıktan sonra, HDInsight kümenizin üretmiş olduğu günlük dosyalarının türlerini ve hacimlerini kavramış olursunuz.

Ardından, bir süre içinde anahtar günlük depolama konumlarında günlük verilerinin hacmini analiz edin. Örneğin, 30-60-90 günlük dönem üzerinden hacmi ve büyümeyi çözümleyebilirsiniz.  Bu bilgileri bir elektronik tabloya kaydedin veya Excel için Visual Studio, Azure Depolama Gezgini veya Power Query gibi diğer araçları kullanın. ```

Artık anahtar günlükleri için bir günlük yönetimi stratejisi oluşturmak üzere yeterli bilgiye sahipsiniz.  Hem günlük boyutu büyümesini hem de günlük depolama Azure hizmeti maliyetlerini tahmin etmek için elektronik tablonuzu (veya seçim aracını) kullanın.  Ayrıca, incelediğiniz Günlükler kümesi için herhangi bir günlük tutma gereksinimini göz önünde bulundurun.  Artık, hangi günlük dosyalarının silinebileceği (varsa) ve hangi günlüklerin korunması ve daha ucuz Azure depolama için arşivlenmesi gerektiğini belirlemekten sonra gelecekteki günlük depolama maliyetlerini yeniden tahmin edebilirsiniz.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5. Adım: günlük Arşivi ilkelerini ve süreçlerini belirleme

Hangi günlük dosyalarının silineceğini belirledikten sonra, belirli bir süre geçtikten sonra günlük dosyalarını otomatik olarak silmek için birçok Hadoop hizmetine ait günlüğe kaydetme parametreleri ayarlayabilirsiniz.

Belirli günlük dosyaları için, düşük fiyatlı günlük dosyası arşivleme yaklaşımını kullanabilirsiniz. Azure Resource Manager etkinlik günlükleri için, Azure portal kullanarak bu yaklaşımı inceleyebilirsiniz.  HDInsight örneğiniz için Azure portal **etkinlik günlüğü** bağlantısını seçerek kaynak yöneticisi günlüklerini arşivlemeyi ayarlayın.  Etkinlik günlüğü arama sayfasının üst kısmında **dışarı** aktar menü öğesini seçerek **etkinlik günlüğünü dışarı aktar** bölmesini açın.  Abonelik, bölge, bir depolama hesabına verip vermeyeceğinizi ve günlüklerin saklanacağı gün sayısını girin. Aynı bölmede, bir olay hub 'ına dışa aktarıp vermeyeceğinizi de belirtebilirsiniz.

:::image type="content" source="./media/hdinsight-log-management/hdi-export-log-files.png" alt-text="Azure portal dışarı aktarma etkinlik günlüğü önizlemesi":::

Alternatif olarak, PowerShell ile günlük arşivleme komut dosyası oluşturabilirsiniz.  Örnek bir PowerShell betiği için bkz. Azure [Otomasyonu günlüklerini Azure Blob depolamaya arşivleme](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Azure depolama ölçümlerine erişme

Azure depolama, depolama işlemlerini ve erişimini günlüğe kaydetmek için yapılandırılabilir. Bu çok ayrıntılı günlükleri kapasite izleme ve planlama için ve depolamaya yönelik denetim istekleri için kullanabilirsiniz. Günlüğe kaydedilen bilgiler, çözümlerinizin performansını izleyip ayarlamanıza olanak tanıyan gecikme süresi ayrıntılarını içerir.
HDInsight kümesi için verileri tutan Azure depolama için oluşturulan günlük dosyalarını incelemek üzere Hadoop için .NET SDK 'sını kullanabilirsiniz.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Eski günlük dosyaları için yedek dizinlerin boyutunu ve sayısını denetleme

Tutulan günlük dosyalarının boyutunu ve sayısını denetlemek için aşağıdaki özellikleri ayarlayın `RollingFileAppender` :

* `maxFileSize` , dosyanın alındığı dosyanın kritik boyutudur. Varsayılan değer 10 MB 'tır.
* `maxBackupIndex` oluşturulacak yedekleme dosyalarının sayısını, varsayılan 1 ' i belirtir.

### <a name="other-log-management-techniques"></a>Diğer günlük yönetimi teknikleri

Disk alanının tükenmesinden kaçınmak için [logrotate](https://linux.die.net/man/8/logrotate) gibi bazı işletim sistemi araçlarını kullanarak günlük dosyalarının işlenmesini yönetebilirsiniz. Günlük `logrotate` dosyalarını sıkıştırmak ve eski olanları kaldırmak için, ' yi günlük olarak çalışacak şekilde yapılandırabilirsiniz. Yaklaşım, yerel düğümlerde günlük dosyalarının saklanacağı süre gibi gereksinimlerinize bağlıdır.  

Ayrıca, çıkış günlüğü boyutunu büyük ölçüde artıran bir veya daha fazla hizmet için hata ayıklama günlüğü 'nün etkinleştirilip etkinleştirilmeyeceğini de denetleyebilirsiniz.  

Tüm düğümlerdeki günlükleri tek bir merkezi konuma toplamak için, tüm günlük girişlerini Solr olarak almak gibi bir veri akışı oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight için izleme ve günlüğe kaydetme uygulaması](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [Linux tabanlı HDInsight 'ta YARN uygulama günlüklerine erişim Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Çeşitli Apache Hadoop bileşenleri için günlük dosyalarının boyutunu denetleme](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
