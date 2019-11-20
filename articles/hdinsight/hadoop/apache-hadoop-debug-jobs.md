---
title: "Hata ayıklama Apache Hadoop: Azure HDInsight 'ta Günlükler ve hata iletileri"
description: PowerShell kullanarak HDInsight yönetirken alacağınız hata iletileri ve kurtarmak için uygulayabileceğiniz adımlar hakkında bilgi edinin.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 2e5cb1676670642121caec71a973374063fe4320
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044850"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hadoop günlüklerini analiz etme

Azure HDInsight 'taki her Apache Hadoop kümesi, varsayılan dosya sistemi olarak kullanılan bir Azure depolama hesabına sahiptir. Depolama hesabına varsayılan depolama hesabı denir. Küme, günlüklerini depolamak için varsayılan depolama hesabındaki Azure Tablo depolama ve BLOB depolama alanını kullanır.  Kümenizin varsayılan depolama hesabını bulmak için bkz. [HDInsight 'ta Apache Hadoop kümelerini yönetme](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Günlükler, küme silindikten sonra bile depolama hesabında kalır.

## <a name="logs-written-to-azure-tables"></a>Azure tablolarına yazılan Günlükler

Azure tablolarına yazılan Günlükler, bir HDInsight kümesiyle neler olduğunu gösteren bir öngörü düzeyi sağlar.

Bir HDInsight kümesi oluşturduğunuzda, varsayılan tablo depolamada Linux tabanlı kümeler için altı tablo otomatik olarak oluşturulur:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Tablo dosya adları **u\<ClusterName > Ddmonyyyıyathhmmsssss\<TableName >** .

Bu tablolar aşağıdaki alanları içerir:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Ana bilgisayar
* MALoggingHash
* İleti
* N
* Ön IO zaman damgası
* Rol
* RowIndex
* Kiracı
* ILIŞKIN
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Günlüklere erişim araçları
Bu tablolardaki verilere erişmek için kullanabileceğiniz birçok araç vardır:

* Visual Studio
* Azure Depolama Gezgini
* Excel için Power Query

#### <a name="use-power-query-for-excel"></a>Excel için Power Query kullanma
Power Query, [Excel için Microsoft Power Query](https://www.microsoft.com/en-us/download/details.aspx?id=39379)yüklenebilir. Sistem gereksinimleri için indirme sayfasına bakın.

**Hizmet günlüğünü açmak ve analiz etmek için Power Query kullanma**

1. **Microsoft Excel**'i açın.
2. **Power Query** menüsünde **Azure**' a ve ardından **Microsoft Azure Tablo depolama**' ya tıklayın.
   
    ![HDInsight Hadoop Excel PowerQuery Azure Tablo depolama 'yı açma](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Depolama hesabı adını, kısa ad veya FQDN girin.
4. Depolama hesabı anahtarını girin. Tabloların bir listesini görürsünüz:
   
    ![Azure Tablo depolamada depolanan HDInsight Hadoop günlükleri](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. **Gezgin** bölmesinde hadoopservicelog tablosuna sağ tıklayın ve **Düzenle**' yi seçin. Dört sütun görürsünüz. İsteğe bağlı olarak, **bölüm anahtarını**, **satır anahtarını**ve **zaman damgası** sütunlarını seçerek ve Şeritteki seçeneklerden **sütunları kaldır** ' a tıklayarak silin.
6. Excel elektronik tablosuna aktarmak istediğiniz sütunları seçmek için Içerik sütunundaki Genişlet simgesine tıklayın. Bu gösterim için TraceLevel ve ComponentName ' i seçtim: Bu, hangi bileşenlere sorun olduğunu belirten bir temel bilgi verebilir.
   
    ![HDInsight Hadoop günlükleri sütunları seç Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop günlükleri sütunları seç Excel")
7. Verileri içeri aktarmak için **Tamam** ' ı tıklatın.
8. **TraceLevel**, role ve **ComponentName** sütunlarını seçin ve ardından şeritte **grupla** ' ya tıklayın.
9. Gruplandırma ölçütü iletişim kutusunda **Tamam** ' a tıklayın.
10. \* * Uygula & Kapat * * öğesine tıklayın.

Artık gerektiğinde filtrelemek ve sıralamak için Excel 'i kullanabilirsiniz. Oluşan sorunları gidermek için diğer sütunları (örneğin, Ileti) dahil etmek isteyebilirsiniz, ancak yukarıda açıklanan sütunları seçip gruplandırmak, Hadoop hizmetlerinde neler olduğunu belirten bir resim sunar. Aynı fikir Setuplog ve hadoopinstalllog tablolarına uygulanabilir.

#### <a name="use-visual-studio"></a>Visual Studio'yu kullanma
**Visual Studio kullanma**

1. Visual Studio'yu açın.
2. **Görünüm** menüsünde, **bulut Gezgini**' ne tıklayın. Ya da **CTRL +\, CTRL + X**tuşlarına tıklamaktır.
3. **Bulut Gezgini**' nden **kaynak türleri**' ni seçin.  Diğer kullanılabilir seçeneği **kaynak gruplarıdır**.
4. **Depolama hesapları**, kümenizin varsayılan depolama hesabı ve ardından **Tablolar**' ı genişletin.
5. **Hadoopservicelog**çift tıklayın.
6. Bir filtre ekleyin. Örnek:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop günlükleri sütunları seçme vs](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop günlükleri sütunları seçme vs")
   
    Filtre oluşturma hakkında daha fazla bilgi için bkz. [Tablo Tasarımcısı Için Filtre dizeleri](../../vs-azure-tools-table-designer-construct-filter-strings.md)oluşturma.

## <a name="logs-written-to-azure-blob-storage"></a>Azure Blob depolamaya yazılan Günlükler

Azure tablolarına yazılan Günlükler, bir HDInsight kümesiyle neler olduğunu gösteren bir öngörü düzeyi sağlar. Ancak, bu tablolar, ne zaman ortaya çıkabilecek sorunları ele alırken faydalı olabilecek görev düzeyi günlükleri sağlamaz. Bu sonraki ayrıntı düzeyini sağlamak için HDInsight kümeleri, Temptaton aracılığıyla gönderilen herhangi bir iş için BLOB depolama hesabınıza görev günlükleri yazacak şekilde yapılandırılmıştır. Bu şekilde, bu, kümeye RDP/komut satırı erişimi aracılığıyla gönderilen işleri değil, Microsoft Azure PowerShell cmdlet 'leri veya .NET Iş gönderme API 'Leri kullanılarak gönderilen işlerin ortalamadığını gösterir. 

Günlükleri görüntülemek için bkz. [Linux tabanlı HDInsight 'TA YARN uygulama günlüklerine erişim Apache Hadoop](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

## <a name="view-cluster-health-and-job-logs"></a>Küme durumunu ve iş günlüklerini görüntüleme

### <a name="access-the-ambari-ui"></a>Ambarı Kullanıcı arabirimine erişin

Azure portal, küme bölmesini açmak için bir HDInsight küme adına tıklayın. Küme bölmesinden **Pano**' ya tıklayın.

![HDInsight başlatma kümesi panosu](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Yarn Kullanıcı arabirimine erişin
Azure portal, küme bölmesini açmak için bir HDInsight küme adına tıklayın. Küme bölmesinden **Pano**' ya tıklayın. İstendiğinde, Küme Yöneticisi kimlik bilgilerini girin. Ambarı ' nda, sol taraftaki hizmetler listesinden **Yarn** ' yi seçin. Görüntülenen sayfada **hızlı bağlantılar**' ı seçin ve ardından etkin baş düğüm girişini ve Kaynak Yöneticisi Kullanıcı arabirimini seçin.

YARN Kullanıcı arabirimini kullanarak şunları yapabilirsiniz:

* **Küme durumunu Al**. Sol bölmeden **küme**' ı genişletin ve **hakkında**' yı tıklatın. Bu durum, ayrılan toplam bellek, kullanılan çekirdekler, küme kaynak yöneticisi, küme sürümü vb. gibi küme durumu ayrıntılarını sunar.
  
    ![HDInsight başlatma kümesi Pano Yarn](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "HDInsight başlatma kümesi Pano Yarn")
* **Düğüm durumunu Al**. Sol bölmeden **küme**' ı genişletin ve **düğümler**' e tıklayın. Bu, kümedeki tüm düğümleri, her düğümün HTTP adresini, her bir düğüme ayrılan kaynakları vb. listeler.
* **İş durumunu izleme**. Sol bölmeden **küme**' ı genişletin ve ardından kümedeki tüm işleri listelemek için **uygulamalar** ' a tıklayın. İşlerin belirli bir durumda (örneğin, yeni, gönderilen, çalışan, vb.) görünmesini istiyorsanız, **uygulamalar**altında uygun bağlantıya tıklayın. İşle ilgili daha fazla bilgi edinmek için iş adına tıklayabilirsiniz. Örneğin, çıkış, Günlükler vb. dahil olmak üzere iş hakkında daha fazla bilgi edinebilirsiniz.

### <a name="access-the-hbase-ui"></a>HBase Kullanıcı arabirimine erişme
Azure portal, küme bölmesini açmak için bir HDInsight HBase küme adına tıklayın. Küme bölmesinden **Pano**' ya tıklayın. İstendiğinde, Küme Yöneticisi kimlik bilgilerini girin. Ambarı ' nda, hizmetler listesinden HBase ' i seçin. Sayfanın üst kısmındaki **hızlı bağlantılar** ' ı seçin, etkin Zookeeper düğümü bağlantısına gelin ve HBase Master Kullanıcı arabirimi ' ne tıklayın.

## <a name="hdinsight-error-codes"></a>HDInsight hata kodları
Bu bölümde yer alan hata iletileri, Azure HDInsight 'ta Hadoop kullanıcılarının, Azure PowerShell kullanarak hizmeti yönetirken karşılaşabilecekleri olası hata koşullarını anlamalarına ve bu kullanıcılara alınabilecek adımları dikkate almanıza yardımcı olmak için verilmiştir hatayı kurtarmak için.

Bu hata iletilerinin bazıları HDInsight kümelerini yönetmek için kullanıldığında Azure portal de görülebilir. Ancak karşılaşabileceğiniz diğer hata iletileri, bu bağlamda olası düzeltme eylemleriyle ilgili kısıtlamalar nedeniyle daha ayrıntılı bir şekilde yapılır. Diğer hata iletileri, azaltma 'nın belirgin olduğu bağlamlarda sağlanır. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>Atleastonessınmetastoremustbesaðlanan
* **Açıklama**: Hive ve Oozie meta veri ayarlarını kullanmak için lütfen en az bir bileşen IÇIN Azure SQL veritabanı ayrıntılarını sağlayın.
* **Risk azaltma**: kullanıcının geçerli bir SQL Azure meta veri sağlaması ve isteği yeniden denemesi gerekir.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Açıklama**: bölge *nameofyourregion*içinde oluşturulamadı. Geçerli bir HDInsight bölgesi kullanın ve isteği yeniden deneyin.
* **Risk azaltma**: müşterinin Şu anda bunları destekleyen küme bölgesini oluşturması gerekir: güneydoğu asya, Batı Avrupa, Kuzey Avrupa, Doğu ABD veya Batı ABD.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Açıklama**: Sunucu istenen küme kaydını bulamadı.  
* **Risk azaltma**: işlemi yeniden deneyin.

### <a id="ClusterDnsNameInvalidReservedWord"></a>Clusterdnsnameınvalidreservedword
* **Açıklama**: küme DNS adı, *kullanıcıadı* geçersiz. Adın, alfasayısal karakterlerle başladığı ve bittiği ve yalnızca '-' özel karakterini içerebildiği için lütfen emin olun  
* **Risk azaltma**: kümeniz için, alfasayısal ile başlayan ve biten ve '-' Dash dışında özel karakter içermeyen, ve sonra işlemi yeniden denemeden önce GEÇERLI bir DNS adı kullandığınızdan emin olun.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Açıklama**: *adclustername* adlı küme adı kullanılamıyor. Lütfen başka bir ad seçin.  
* **Risk azaltma**: Kullanıcı benzersiz bir clustername belirtmeli ve yok ve yeniden dene. Kullanıcı Portalı kullanıyorsa, oluşturma adımları sırasında bir küme adı zaten kullanılıyorsa Kullanıcı arabirimi onlara bildirim gönderir.

### <a id="ClusterPasswordInvalid"></a>Clusterpasswordgeçersiz
* **Açıklama**: küme parolası geçersiz. Parola en az 10 karakter uzunluğunda olmalı ve boşluk olmadan en az bir sayı, büyük harf, küçük harf ve özel karakter içermeli ve bunun bir parçası olarak Kullanıcı adını içermemelidir.  
* **Risk azaltma**: geçerli bir küme parolası sağlayın ve işlemi yeniden deneyin.

### <a id="ClusterUserNameInvalid"></a>Clusterusernamegeçersiz
* **Açıklama**: küme Kullanıcı adı geçersiz. Lütfen Kullanıcı adının özel karakterler veya boşluklar içermediğinden emin olun.  
* **Risk azaltma**: geçerli bir küme Kullanıcı adı sağlayın ve işlemi yeniden deneyin.

### <a id="ClusterUserNameInvalidReservedWord"></a>Clusterusernameınvalidreservedword
* **Açıklama**: küme DNS adı *Yourdnsclustername* geçersiz. Adın, alfasayısal karakterlerle başladığı ve bittiği ve yalnızca '-' özel karakterini içerebildiği için lütfen emin olun  
* **Risk azaltma**: GEÇERLI bir DNS kümesi Kullanıcı adı sağlayın ve işlemi yeniden deneyin.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Açıklama**: Uri *ncontaineruri* ve *DNS adı içindeki* kapsayıcı adı, istek gövdesinde aynı olmalıdır.  
* **Risk azaltma**: kapsayıcı ADıNıZıN ve DNS adınızın aynı olduğundan emin olun ve işlemi yeniden deneyin.

### <a id="DataNodeDefinitionNotFound"></a>Davtanodedefinitionnotfound
* **Açıklama**: geçersiz küme yapılandırması. Düğüm boyutunda hiçbir veri düğümü tanımı bulunamıyor.  
* **Risk azaltma**: işlemi yeniden deneyin.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Açıklama**: küme için dağıtımın silinmesi başarısız oldu  
* **Risk azaltma**: silme işlemini yeniden deneyin.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Açıklama**: hizmet yapılandırma hatası. Gerekli DNS eşleme bilgileri bulunamadı.  
* **Risk azaltma**: kümeyi silin ve yeni bir küme oluşturun.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Açıklama**: Yinelenen küme kapsayıcısı oluşturma girişimi. *Nameofyourcontainer* için kayıt var, ancak ETags eşleşmiyor.
* **Risk azaltma**: kapsayıcı için benzersiz bir ad sağlayın ve oluşturma işlemini yeniden deneyin.

### <a id="DuplicateClusterInHostedService"></a>Duplicateclusterınhostedservice
* **Açıklama**: barındırılan hizmet *Nameofyourhostedservice* zaten bir küme içeriyor. Barındırılan bir hizmet birden çok küme içeremez  
* **Risk azaltma**: kümeyi başka bir barındırılan hizmette barındırın.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Açıklama**: sunucu, küme dağıtımının durumunu güncelleştiremedi.  
* **Risk azaltma**: işlemi yeniden deneyin. Bu birden çok kez oluşursa, CSS ile iletişim kurun.

### <a id="HdiRestoreClusterAltered"></a>Hdırestoreclusterdeğiştirilmiştir
* **Açıklama**: *kümeleyici* , bakım kapsamında silindi. Lütfen kümeyi yeniden oluşturun.
* **Risk azaltma**: kümeyi yeniden oluşturun.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Açıklama**: geçersiz küme yapılandırması. Düğüm boyutlarında gerekli baş düğüm yapılandırması bulunamadı.
* **Risk azaltma**: işlemi yeniden deneyin.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Açıklama**: barındırılan hizmet *Adımyourhostedservice*oluşturulamıyor. Lütfen isteği yeniden deneyin.  
* **Risk azaltma**: isteği yeniden deneyin.

### <a id="HostedServiceHasProductionDeployment"></a>Hostedservicehasüretim dağıtımı
* **Açıklama**: barındırılan hizmet *Nameofyourhostedservice* zaten bir üretim dağıtımına sahip. Barındırılan bir hizmet birden fazla üretim dağıtımı içeremez. Farklı bir küme adına sahip isteği yeniden deneyin.
* **Risk azaltma**: farklı bir küme adı kullanın ve isteği yeniden deneyin.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Açıklama**: küme Için barındırılan hizmet *Nameofyourhostedservice* bulunamadı.  
* **Risk azaltma**: küme hata durumundaysa, silin ve yeniden deneyin.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Açıklama**: barındırılan hizmet *Nameofyourhostedservice* 'in ilişkili bir dağıtımı yok.  
* **Risk azaltma**: küme hata durumundaysa, silin ve yeniden deneyin.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Açıklama**: SubscriptionID *depolamasındaki* *bir küme oluşturmak*için çekirdekler yok. Gerekli: *resourcesrequired*, kullanılabilir: *resourcesavailable*.  
* **Risk azaltma**: aboneliğinizdeki kaynakları boşaltın veya aboneliğin kullanabildiği kaynakları artırın ve kümeyi yeniden oluşturmayı deneyin.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Açıklama**: abonelik kimliği, bir yeni hostedservice 'In kendi *clustername*kümesi oluşturması için *Kota içermez.*  
* **Risk azaltma**: aboneliğinizdeki kaynakları boşaltın veya aboneliğin kullanabildiği kaynakları artırın ve kümeyi yeniden oluşturmayı deneyin.

### <a id="InternalErrorRetryRequest"></a>Internalerrorretryrequest
* **Açıklama**: sunucu bir iç hatayla karşılaştı. Lütfen isteği yeniden deneyin.  
* **Risk azaltma**: isteği yeniden deneyin.

### <a id="InvalidAzureStorageLocation"></a>Invalidazurestoraygelocation
* **Açıklama**: Azure depolama konumu *Dataregionname* geçerli bir konum değil. Bölgenin doğru olduğundan emin olun ve isteği yeniden deneyin.
* **Risk azaltma**: HDInsight 'ı destekleyen bir depolama konumu seçin, kümenizin birlikte bulunduğundan emin olun ve işlemi yeniden deneyin.

### <a id="InvalidNodeSizeForDataNode"></a>Invalidnodesizefordadtanode
* **Açıklama**: veri düğümleri IÇIN geçersiz VM boyutu. Tüm veri düğümlerinde yalnızca ' büyük VM ' boyutu desteklenir.  
* **Risk azaltma**: veri düğümü için desteklenen düğüm boyutunu belirtin ve işlemi yeniden deneyin.

### <a id="InvalidNodeSizeForHeadNode"></a>Invalidnodesizeforheadnode
* **Açıklama**: baş düğüm IÇIN geçersiz VM boyutu. Baş düğüm için yalnızca ' Extrabüyük VM ' boyutu desteklenir.  
* **Risk azaltma**: baş düğüm için desteklenen düğüm boyutunu belirtin ve işlemi yeniden deneyin

### <a id="InvalidRightsForDeploymentDeletion"></a>Invalidrightsfordeploymentsilinmeye
* **Açıklama**: kullanılan abonelik *kimliği,* küme *adı*için silme işlemini yürütmek için yeterli izinlere sahip değil.  
* **Risk azaltma**: küme hata durumundaysa, bırakın ve sonra yeniden deneyin.  

### <a id="InvalidStorageAccountBlobContainerName"></a>Invalidstorageaccountblobcontainername
* **Açıklama**: dış depolama hesabı blob kapsayıcısı adı kendi *kapsayıcıadı* geçersiz. Adın bir harfle başladığı ve yalnızca küçük harf, rakam ve tire içerdiğinden emin olun.  
* **Risk azaltma**: geçerli bir depolama hesabı blob kapsayıcı adı belirtip işlemi yeniden deneyin.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>Invalidstorageaccountconfigurationsecretkey
* **Açıklama**: dış depolama hesabının yapılandırması, gizli anahtar ayrıntılarının ayarlanmış olması *için gereklidir.*  
* **Risk azaltma**: depolama hesabı için geçerli bir gizli anahtar belirtip işlemi yeniden deneyin.

### <a id="InvalidVersionHeaderFormat"></a>Invalidversionheaderformat
* **Açıklama**: sürüm üstbilgisi *yourversionheader* geçerli bir YYYY-AA-GG biçiminde değil.  
* **Risk azaltma**: sürüm üst bilgisi için geçerli bir biçim belirtip isteği yeniden deneyin.

### <a id="MoreThanOneHeadNode"></a>Daha fazla yer alan
* **Açıklama**: geçersiz küme yapılandırması. Birden fazla baş düğüm yapılandırması bulundu.  
* **Risk azaltma**: yapılandırmayı yalnızca bir baş düğüm belirtilen şekilde düzenleyin.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Açıklama**: işlem, izin verilen süre veya en fazla yeniden deneme denemesi içinde tamamlanamadı. Lütfen isteği yeniden deneyin.  
* **Risk azaltma**: isteği yeniden deneyin.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Açıklama** *: parametreli parametre* null veya boş olamaz.  
* **Risk azaltma**: parametre için geçerli bir değer belirtin.

### <a id="PreClusterCreationValidationFailure"></a>Prelustercreationvalidationfailure
* **Açıklama**: bir veya daha fazla küme oluşturma isteği girişi geçerli değil. Giriş değerlerinin doğru olduğundan emin olun ve isteği yeniden deneyin.  
* **Risk azaltma**: giriş değerlerinin doğru olduğundan emin olun ve isteği yeniden deneyin.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Açıklama**: *BÖLGEADı* *ve abonelik kimliği*olan bölge adı için bölge özelliği kullanılamaz.  
* **Risk azaltma**: HDInsight kümelerini destekleyen bir bölge belirtin. Genel olarak desteklenen bölgeler şunlardır: Güneydoğu Asya, Batı Avrupa, Kuzey Avrupa, Doğu ABD veya Batı ABD.

### <a id="StorageAccountNotColocated"></a>Storageaccountnotbirlikte
* **Açıklama**: *yourstorageaccountname* depolama hesabı, *currentregionname*bölgesinde yer alan. Bu, kendi *Clusterregionname*küme bölgesiyle aynı olmalıdır.  
* **Risk azaltma**: kümenizin bulunduğu bölgede bir depolama hesabı belirtin ya da verileriniz zaten depolama hesabında bulunuyorsa, mevcut depolama hesabıyla aynı bölgede yeni bir küme oluşturun. Portalı kullanıyorsanız, Kullanıcı arabirimi bu sorunu önceden bilgilendirir.

### <a id="SubscriptionIdNotActive"></a>Subscriptionıdnotactive
* **Açıklama**: BELIRTILEN abonelik kimliği, *SubscriptionID* etkin değil.  
* **Risk azaltma**: aboneliğinizi yeniden etkinleştirin veya yeni bir geçerli abonelik alın.

### <a id="SubscriptionIdNotFound"></a>Subscriptionıdnotfound
* **Açıklama**: abonelik kimliği bir *SubscriptionID* bulunamadı.  
* **Risk azaltma**: abonelik kimliğinizin geçerli olup olmadığını denetleyin ve işlemi yeniden deneyin.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Açıklama**: DNS *yourdnsurl 'si*çözümlenemiyor. Lütfen blob uç noktasının tam URL 'sinin sağlandığından emin olun.  
* **Risk azaltma**: geçerli bir blob URL 'si sağlayın. URL, *http://* ile başlayıp *. com*ile biten dahil olmak üzere tam olarak geçerli olmalıdır.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Açıklama**: *dnsurl 'sinin*kaynak konumu doğrulanamıyor. Lütfen blob uç noktasının tam URL 'sinin sağlandığından emin olun.  
* **Risk azaltma**: geçerli bir blob URL 'si sağlayın. URL, *http://* ile başlayıp *. com*ile biten dahil olmak üzere tam olarak geçerli olmalıdır.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Açıklama**: sürüm özelliği, sürüm tarafından *belirtilmediedversion* ve *abonelik kimliği için*kullanılabilir değil.  
* **Risk azaltma**: kullanılabilir bir sürüm seçin ve işlemi yeniden deneyin.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Açıklama**: Version *belirtilme* sürümü desteklenmiyor.
* **Risk azaltma**: desteklenen bir sürüm seçin ve işlemi yeniden deneyin.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Açıklama**: bir Version *belirtilmeditıon* , bir Azure Region *belirtilmediregion*içinde kullanılamaz.  
* **Risk azaltma**: belirtilen bölgede desteklenen bir sürüm seçin ve işlemi yeniden deneyin.

### <a id="WasbAccountConfigNotFound"></a>' Te Baccountconfignotfound
* **Açıklama**: geçersiz küme yapılandırması. Dış hesaplarda gerekli olan% s hesap yapılandırması bulunamadı.  
* **Risk azaltma**: hesabın var olduğunu ve yapılandırmada düzgün bir şekilde belirtildiğinden emin olun ve işlemi yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Linux tabanlı HDInsight 'ta Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirme](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight kümelerini Apache Ambari Web arabiriminden yönetme](../hdinsight-hadoop-manage-ambari.md)
