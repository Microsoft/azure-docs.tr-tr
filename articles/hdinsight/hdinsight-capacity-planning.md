---
title: Azure HDInsight 'ta küme kapasitesi planlama
description: Azure HDInsight kümesinin kapasite ve performans planlamasına ilişkin önemli soruları belirler.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: d8d5ecd64ba689dc9cce342513702d8359038162
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682249"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight kümeleri için kapasite planlaması

HDInsight kümesi dağıtılmadan önce gerekli performans ve ölçeği belirleyerek istenen küme kapasitesini planlayın. Bu planlama, her iki kullanılabilirliği ve maliyeti iyileştirmenize yardımcı olur. Bazı küme kapasitesi kararları dağıtımdan sonra değiştirilemez. Performans parametreleri değiştiğinde bir küme, depolanan veriler kaybedilmeden bir şekilde açılıp yeniden oluşturulabilir.

Kapasite planlamasına sorulacak önemli sorular şunlardır:

* Kümenizi hangi coğrafi bölgede dağıtmanız gerekir?
* Ne kadar depolama alanı gerekiyor?
* Hangi küme türünü dağıtmanız gerekir?
* Küme düğümleriniz hangi boyutta ve sanal makine (VM) kullanmalıdır?
* Kümeniz kaç tane çalışan düğümü olmalıdır?

## <a name="choose-an-azure-region"></a>Bir Azure bölgesi seçin

Azure bölgesi, kümenizin fiziksel olarak sağlandığını belirler. Okuma ve yazma gecikmesini en aza indirmek için, kümenin verilerinize yakın olması gerekir.

HDInsight birçok Azure bölgesinde kullanılabilir. En yakın bölgeyi bulmak için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Depolama konumu ve boyutunu seçin

### <a name="location-of-default-storage"></a>Varsayılan depolama konumu

Bir Azure depolama hesabı veya Azure Data Lake Storage varsayılan depolama, kümenizle aynı konumda olmalıdır. Azure depolama, tüm konumlarda kullanılabilir. Data Lake Storage 1. bazı bölgelerde kullanılabilir-geçerli [Data Lake Storage kullanılabilirliğine](https://azure.microsoft.com/global-infrastructure/services/?products=storage)bakın.

### <a name="location-of-existing-data"></a>Mevcut verilerin konumu

Zaten verilerinizi içeren bir depolama hesabınız veya Data Lake Storage varsa ve bu depolama alanını kümenizin varsayılan depolama alanı olarak kullanmak istiyorsanız, kümenizi aynı konumda dağıtmanız gerekir.

### <a name="storage-size"></a>Depolama boyutu

Bir HDInsight kümesi dağıtıldıktan sonra, ek Azure depolama hesapları ekleyebilir veya diğer Data Lake Storage erişebilirsiniz. Tüm depolama hesaplarınız, kümenizle aynı konumda bulunmalıdır. Bir Data Lake Storage farklı bir konumda olabilir, ancak bu, bazı veri okuma/yazma gecikme süresini ortaya çıkarabilir.

Azure depolama 'nın bazı [Kapasite limitleri](../azure-subscription-service-limits.md#storage-limits)vardır, Data Lake Storage 1. neredeyse sınırsız olur.

Bir küme, farklı depolama hesaplarının birleşimine erişebilir. Tipik örnekler şunlardır:

* Veri miktarı, tek bir BLOB depolama kapsayıcısının depolama kapasitesini aşmaya olasılığı yüksektir.
* Blob kapsayıcısına erişim oranı, azaltma işleminin gerçekleştiği eşiği aşabilir.
* Veri oluşturmak istediğinizde, küme için kullanılabilir bir blob kapsayıcısına zaten yüklediniz.
* Güvenliğin nedenlerle depolamanın farklı parçalarını yalıtmak veya yönetimi basitleştirmek istediğinizde.

48 düğümlü bir küme için 4-8 depolama hesabı önerilir. Toplam depolama alanı zaten yeterli olabilir, ancak her depolama hesabı işlem düğümleri için ek ağ bant genişliği sağlar. Birden çok depolama hesabınız olduğunda, ön ek olmadan her depolama hesabı için rastgele bir ad kullanın. Rastgele adlandırma amacı, tüm hesaplarda depolama performans sorunlarını (azaltma) veya genel modlu hataların olasılığını azaltmaktadır. Daha iyi performans için, depolama hesabı başına yalnızca bir kapsayıcı kullanın.

## <a name="choose-a-cluster-type"></a>Bir küme türü seçin

Küme türü, HDInsight kümenizin çalışmak üzere yapılandırıldığı iş yükünü ( [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)veya [Apache Spark](https://spark.apache.org/)gibi) belirler. Kullanılabilir küme türlerinin ayrıntılı bir açıklaması için bkz. [Azure HDInsight 'A giriş](hdinsight-overview.md#cluster-types-in-hdinsight). Her küme türünün, düğümlerin boyutu ve sayısı için gereksinimleri içeren belirli bir dağıtım topolojisi vardır.

## <a name="choose-the-vm-size-and-type"></a>VM boyutunu ve türünü seçin

Her küme türü bir dizi düğüm türüne sahiptir ve her düğüm türü, VM boyutu ve türü için özel seçeneklere sahiptir.

Uygulamanız için en uygun küme boyutunu belirleyebilmek için, küme kapasitesini kıyaslama ve boyutu belirtilen şekilde artırabilirsiniz. Örneğin, sanal bir iş yükü veya *kanarya sorgusu*kullanabilirsiniz. Sanal bir iş yüküyle, beklenen iş yüklerinizi farklı boyut kümelerinde çalıştırırsanız, istenen performansa ulaşılıncaya kadar boyutu kademeli olarak artırabilirsiniz. Kümenin yeterli kaynağa sahip olup olmadığını göstermek için, diğer üretim sorguları arasında düzenli aralıklarla bir sorgu eklenebilir.

İş yükünüz için doğru VM ailesini seçme hakkında daha fazla bilgi için, bkz. [kümeniz için doğru VM boyutunu seçme](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Küme ölçeğini seçin

Kümenin ölçeği, VM düğümlerinin miktarına göre belirlenir. Tüm küme türleri için belirli bir ölçeğe sahip düğüm türleri ve ölçeği desteklemeyen düğüm türleri vardır. Örneğin, bir küme tam olarak üç [Apache ZooKeeper](https://zookeeper.apache.org/) düğümü veya iki baş düğüm gerektirebilir. Dağıtılmış bir biçimde veri işleme yapan çalışan düğümleri, ek çalışan düğümler ekleyerek ölçeklendirmeden faydalanabilir.

Küme türüne bağlı olarak, çalışan düğümlerinin sayısını artırmak ek hesaplama kapasitesi (daha fazla çekirdek gibi) ekler, ancak aynı zamanda, işlenen verilerin bellek içi depolama alanını desteklemek için tüm küme için gereken toplam bellek miktarına da eklenebilir. VM boyutu ve türü seçiminde, doğru küme ölçeğinin seçilmesi genellikle benzetimli iş yükleri veya kanarary sorguları kullanılarak daha fazla yapılır.

En yoğun yük taleplerini karşılamak için kümenizi ölçeklendirebilir, daha sonra bu ek düğümlere artık gerek kalmadığında ölçeği yeniden azaltabilirsiniz. Otomatik [Ölçeklendirme özelliği](hdinsight-autoscale-clusters.md) , kümenizi önceden belirlenmiş ölçümler ve zamanlamalara göre otomatik olarak ölçeklendirmenize olanak tanır. Kümelerinizi el ile ölçeklendirmeyle ilgili daha fazla bilgi için bkz. [HDInsight kümelerini ölçeklendirme](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Küme yaşam döngüsü

Bir kümenin ömrü için ücretlendirilirsiniz. Kümenizin yalnızca belirli zamanları çalışır duruma getirmeniz ve çalıştırmak için, [Azure Data Factory kullanarak isteğe bağlı kümeler oluşturabilirsiniz](hdinsight-hadoop-create-linux-clusters-adf.md). Ayrıca kümenizi sağlayan ve silen PowerShell betikleri oluşturabilir ve ardından bu betikleri [Azure Otomasyonu](https://azure.microsoft.com/services/automation/)' nu kullanarak zamanlayabilirsiniz.

> [!NOTE]  
> Bir küme silindiğinde, varsayılan Hive meta veri deposu de silinir. Bir sonraki küme yeniden oluşturma için meta verileri kalıcı hale getirmek için Azure veritabanı veya [Apache Oozie](https://oozie.apache.org/)gibi bir dış meta veri deposu kullanın.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Küme işi hatalarını yalıtma

Bazen birden çok eşlemelerin paralel yürütülmesi ve çok düğümlü bir küme üzerindeki bileşenlerin azaltılması nedeniyle hatalar oluşabilir. Sorunu yalıtmaya yardımcı olmak için, tek bir çalışan düğümü kümesinde eşzamanlı birden çok iş çalıştırarak dağıtılmış testi deneyin, daha sonra birden fazla iş içeren kümeler üzerinde eşzamanlı olarak birden çok işi çalıştırmak için bu yaklaşımı genişletin. Azure 'da tek düğümlü HDInsight kümesi oluşturmak için *özel (boyut, ayarlar, uygulamalar)* seçeneğini kullanın ve portalda yeni bir küme sağlarken **küme boyutu** bölümünde *çalışan düğümü sayısı* için 1 değerini kullanın.

## <a name="quotas"></a>Kotalar

Hedef küme VM 'nizin boyutunu, ölçeğini ve türünü belirledikten sonra, aboneliğinizin geçerli kota kapasitesi sınırlarını kontrol edin. Bir kota sınırına ulaştığınızda, yeni kümeler dağıtabilir veya daha fazla çalışan düğümü ekleyerek var olan kümelerin ölçeğini ölçeklendiremeyebilirsiniz. Tek kota sınırı, her abonelik için bölge düzeyinde bulunan CPU çekirdekleri kotasına yöneliktir. Örneğin, aboneliğiniz Doğu ABD bölgesinde 30 çekirdek sınırına sahip olabilir. Bir kota artışı istemeniz gerekiyorsa, aşağıdaki adımları uygulayın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Sayfanın sol alt tarafında **Yardım + Destek** ' i seçin.
1. **Yeni destek isteği**’ni seçin.
1. **Yeni destek isteği** sayfasında, **temel bilgiler** sekmesinde aşağıdaki seçenekleri belirleyin:

   - **Sorun türü**: **hizmet ve abonelik limitleri (kotalar)**
   - **Abonelik**: değiştirmek istediğiniz abonelik
   - **Kota türü**: **HDInsight**

     ![HDInsight çekirdek kotasını artırmak için bir destek isteği oluşturma](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. **İleri ' yi seçin: çözümler > >** .
1. **Ayrıntılar** sayfasında, sorun için bir açıklama girin, sorunun önem derecesini, tercih ettiğiniz iletişim yöntemini ve diğer gerekli alanları seçin.
1. Ileri 'yi seçin **: gözden geçir + oluşturma > >** .
1. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.

> [!NOTE]  
> Özel bir bölgedeki HDInsight Core kotasını artırmanız gerekiyorsa, [bir beyaz liste isteği gönderebilirsiniz](https://aka.ms/canaryintwhitelist).

[Bir kota artışı istemek için desteğe başvurabilirsiniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Ancak, bazı bazı kota limitleri vardır, örneğin tek bir Azure aboneliği en fazla 10.000 çekirdeğe sahip olabilir. Bu limitlere ilişkin ayrıntılar için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Hadoop, Spark, Kafka ve daha fazlası ile kümeleri](hdinsight-hadoop-provision-linux-clusters.md)ayarlama: hdınsight 'ta Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ml Hizmetleri veya fırtınası ile kümeleri ayarlama ve yapılandırma hakkında bilgi edinin.
* [Küme performansını izleme](hdinsight-key-scenarios-to-monitor.md): HDInsight kümenizi izlemek için kümenizin kapasitesini etkileyebilecek önemli senaryolar hakkında bilgi edinin.
