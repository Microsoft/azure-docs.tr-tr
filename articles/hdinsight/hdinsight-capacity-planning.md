---
title: Azure HDInsight 'ta küme kapasitesi planlama
description: Azure HDInsight kümesinin kapasite ve performans planlamasına ilişkin önemli soruları belirler.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933230"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight kümeleri için kapasite planlaması

HDInsight kümesi dağıtılmadan önce, gerekli performans ve ölçeği belirleyerek amaçlanan küme kapasitesini planlayın. Bu planlama, her iki kullanılabilirliği ve maliyeti iyileştirmenize yardımcı olur. Bazı küme kapasitesi kararları dağıtımdan sonra değiştirilemez. Performans parametreleri değiştiğinde bir küme, depolanan veriler kaybedilmeden bir şekilde açılıp yeniden oluşturulabilir.

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

Mevcut bir depolama hesabını ya da kümenizin varsayılan depolama alanı olarak Data Lake Storage kullanmak istiyorsanız, kümenizi aynı konumda dağıtmanız gerekir.

### <a name="storage-size"></a>Depolama boyutu

Dağıtılan bir kümede, ek Azure depolama hesapları ekleyebilir veya diğer Data Lake Storage erişebilirsiniz. Tüm depolama hesaplarınız, kümenizle aynı konumda bulunmalıdır. Data Lake Storage farklı bir konumda olabilir, ancak harika uzaklıklar biraz gecikme süresine neden olabilir.

Azure depolama 'nın bazı [Kapasite limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)vardır, Data Lake Storage 1. neredeyse sınırsızdır.

Bir küme, farklı depolama hesaplarının birleşimine erişebilir. Tipik örnekler şunlardır:

* Veri miktarı, tek bir BLOB depolama kapsayıcısının depolama kapasitesini aşmaya olasılığı yüksektir.
* Blob kapsayıcısına erişim oranı, azaltma işleminin gerçekleştiği eşiği aşabilir.
* Veri oluşturmak istediğinizde, küme için kullanılabilir bir blob kapsayıcısına zaten yüklediniz.
* Güvenliğin nedenlerle depolamanın farklı parçalarını yalıtmak veya yönetimi basitleştirmek istediğinizde.

Daha iyi performans için, depolama hesabı başına yalnızca bir kapsayıcı kullanın.

## <a name="choose-a-cluster-type"></a>Bir küme türü seçin

Küme türü, HDInsight kümenizin çalışmak üzere yapılandırıldığı iş yükünü belirler. Türler [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)veya [Apache Spark](./spark/apache-spark-overview.md)içerir. Kullanılabilir küme türlerinin ayrıntılı bir açıklaması için bkz. [Azure HDInsight 'A giriş](hdinsight-overview.md#cluster-types-in-hdinsight). Her küme türünün, düğümlerin boyutu ve sayısı için gereksinimleri içeren belirli bir dağıtım topolojisi vardır.

## <a name="choose-the-vm-size-and-type"></a>VM boyutunu ve türünü seçin

Her küme türü bir dizi düğüm türüne sahiptir ve her düğüm türü, VM boyutu ve türü için özel seçeneklere sahiptir.

Uygulamanız için en uygun küme boyutunu belirleyebilmek için, küme kapasitesini kıyaslama ve boyutu belirtilen şekilde artırabilirsiniz. Örneğin, sanal bir iş yükü veya *kanarya sorgusu* kullanabilirsiniz. Sanal iş yüklerinizi farklı boyut kümelerinde çalıştırın. İstenilen performansa ulaşılıncaya kadar boyutu kademeli olarak artırın. Kümenin yeterli kaynağa sahip olup olmadığını göstermek için, diğer üretim sorguları arasında düzenli aralıklarla bir sorgu eklenebilir.

İş yükünüz için doğru VM ailesini seçme hakkında daha fazla bilgi için, bkz. [kümeniz için doğru VM boyutunu seçme](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Küme ölçeğini seçin

Kümenin ölçeği, VM düğümlerinin miktarına göre belirlenir. Tüm küme türleri için belirli bir ölçeğe sahip düğüm türleri ve ölçeği desteklemeyen düğüm türleri vardır. Örneğin, bir küme tam olarak üç [Apache ZooKeeper](https://zookeeper.apache.org/) düğümü veya iki baş düğüm gerektirebilir. Dağıtılmış bir biçimde veri işleme yapan çalışan düğümleri ek çalışan düğümlerinden faydalanır.

Küme türüne bağlı olarak, çalışan düğümlerinin sayısını artırmak ek hesaplama kapasitesi (daha fazla çekirdek gibi) ekler. Daha fazla düğüm, işlenen verilerin bellek içi depolamasını desteklemek için tüm küme için gereken toplam belleği artırır. VM boyutu ve türü seçiminde olduğu gibi, doğru küme ölçeklendirmesinin seçilmesi genellikle daha önce hale gelmiş olur. Sanal iş yüklerini veya kanarya sorgularını kullanın.

En yoğun yük taleplerini karşılamak için kümenizi ölçeklendirebilirsiniz. Daha sonra bu ek düğümlere artık gerek kalmadığında ölçeği aşağı doğru ölçeklendirin. Otomatik [Ölçeklendirme özelliği](hdinsight-autoscale-clusters.md) , kümenizi önceden belirlenmiş ölçümler ve zamanlamalara göre otomatik olarak ölçeklendirmenize olanak tanır. Kümelerinizi el ile ölçeklendirmeyle ilgili daha fazla bilgi için bkz. [HDInsight kümelerini ölçeklendirme](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Küme yaşam döngüsü

Kümenin ömrü için ücretlendirilirsiniz. Kümeniz için gerekli olan yalnızca belirli zamanlar varsa, [Azure Data Factory kullanarak isteğe bağlı kümeler oluşturun](hdinsight-hadoop-create-linux-clusters-adf.md). Ayrıca kümenizi sağlayan ve silen PowerShell betikleri oluşturabilir ve ardından bu betikleri [Azure Otomasyonu](https://azure.microsoft.com/services/automation/)' nu kullanarak zamanlayabilirsiniz.

> [!NOTE]  
> Bir küme silindiğinde, varsayılan Hive meta veri deposu de silinir. Bir sonraki küme yeniden oluşturma için meta verileri kalıcı hale getirmek için Azure veritabanı veya [Apache Oozie](https://oozie.apache.org/)gibi bir dış meta veri deposu kullanın.

### <a name="isolate-cluster-job-errors"></a>Küme işi hatalarını yalıtma

Bazen birden çok eşleme paralel yürütmesi ve çok düğümlü bir küme üzerindeki bileşenleri azaltmak bazı durumlarda hatalara neden olabilir. Sorunu yalıtmaya yardımcı olmak için dağıtılmış testi deneyin. Aynı anda birden çok işi tek bir çalışan düğüm kümesinde çalıştırın. Daha sonra birden fazla düğüm içeren kümeler üzerinde birden fazla işi eşzamanlı olarak çalıştırmak için bu yaklaşımı genişletin. Azure 'da tek düğümlü HDInsight kümesi oluşturmak için, *`Custom(size, settings, apps)`* seçeneğini kullanın ve portalda yeni bir küme sağlarken **küme boyutu** bölümünde *çalışan düğümü sayısı* için 1 değerini kullanın.

## <a name="quotas"></a>Kotalar

Abonelik kotalarını yönetme hakkında daha fazla bilgi için bkz. [Kota artışı isteme](quota-increase-request.md).

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Hadoop, Spark, Kafka ve daha fazlası ile kümeleri](hdinsight-hadoop-provision-linux-clusters.md)ayarlama: HDInsight 'ta kümeleri ayarlamayı ve yapılandırmayı öğrenin.
* [Küme performansını izleme](hdinsight-key-scenarios-to-monitor.md): HDInsight kümenizi izlemek için kümenizin kapasitesini etkileyebilecek önemli senaryolar hakkında bilgi edinin.
