---
title: Apache Hadoop mimarisi-Azure HDInsight
description: Azure HDInsight kümelerinde Apache Hadoop depolamayı ve işlemeyi açıklar.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 2da9e41323a308782dad509c628a3677ab0cd21f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162884"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>HDInsight’ta Apache Hadoop mimarisi

[Apache Hadoop](https://hadoop.apache.org/) iki çekirdek bileşeni içerir: depolama sağlayan [Apache Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) Apache Hadoop ve Işleme sağlayan [başka bir kaynak Negotiator (Yarn)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) . Depolama ve işleme özellikleri sayesinde bir küme, [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programlarını, istenen veri işlemeyi gerçekleştirecek şekilde çalıştırabilme özelliğine sahip olur.

> [!NOTE]  
> Bir bir bir bir bir bir bir bir bir bir bir şekilde, depolama sağlamak için HDInsight kümesinde Bunun yerine, bir,, Hadoop bileşenleri tarafından bir Gerçek depolama özelliği, Azure depolama veya Azure Data Lake Storage tarafından sağlanır. Hadoop için, HDInsight kümesinde yürütülen MapReduce işleri, bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir bir HDInsight 'ta Hadoop 'da, depolama alanı için kullanılır, ancak YARN işleme bir çekirdek bileşeni kalır. Daha fazla bilgi için bkz. [Azure HDInsight 'A giriş](hadoop/apache-hadoop-introduction.md).

Bu makalede YARN ve HDInsight 'ta uygulamaların yürütülmesini nasıl koordine eden açıklanır.

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN temelleri

YARN, Hadoop 'ta veri işlemeyi yönetir ve düzenler. YARN, kümedeki düğümlerde işlem olarak çalışan iki çekirdek hizmete sahiptir:

* ResourceManager
* NodeManager

ResourceManager, MapReduce işleri gibi uygulamalara küme işlem kaynakları verir. ResourceManager, bu kaynakları her kapsayıcının CPU çekirdekleri ve RAM belleği ayırmadan oluşan kapsayıcılar olarak verir. Bir kümede bulunan tüm kaynakları birleştirilemez ve sonra çekirdekleri ve belleği dağıtırsanız, her kaynak bloğu bir kapsayıcıdır. Kümedeki her düğüm belirli sayıda kapsayıcı için kapasiteye sahiptir, bu nedenle kümenin kullanılabilir kapsayıcı sayısı üzerinde sabit bir sınırı vardır. Bir kapsayıcıdaki kaynak servis birimi yapılandırılabilir.

Bir MapReduce uygulaması bir kümede çalıştığında, ResourceManager uygulamayı yürütülecek kapsayıcıları sağlar. ResourceManager, çalışan uygulamaların durumunu, kullanılabilir küme kapasitesini izler ve kaynakları tamamlayıp serbest bıraktıkları şekilde izler.

ResourceManager Ayrıca uygulamaların durumunu izlemek için bir Web Kullanıcı arabirimi sağlayan bir Web sunucusu işlemi çalıştırır.

Bir kullanıcı küme üzerinde çalışacak bir MapReduce uygulaması gönderdiğinde, uygulama ResourceManager 'ya gönderilir. Sırasıyla, ResourceManager, kullanılabilir NodeManager düğümlerinde bir kapsayıcı ayırır. NodeManager düğümleri uygulamanın aslında çalıştırıldığı yerdir. Ayrılan ilk kapsayıcı, ApplicationMaster adlı özel bir uygulama çalıştırır. Bu ApplicationMaster, gönderilen uygulamayı çalıştırmak için gereken sonraki kapsayıcılar biçiminde kaynakları edinmekten sorumludur. ApplicationMaster, eşleme aşaması ve aşamayı azaltma gibi uygulama aşamalarını ve ne kadar veri işlenmesi gerektiğine ilişkin faktörleri inceler. Daha sonra ApplicationMaster, uygulama adına kaynak üzerinde bulunan kaynakları (*anlaşma*) ister. Ayrıca ResourceManager, kümedeki NodeManager 'dan uygulamayı yürütürken kullanması için kaynak yöneticilerden kaynak sağlar.

Nodeyöneticileri, uygulamayı oluşturan görevleri çalıştırır ve sonra ilerleme durumunu ve durumlarını ApplicationMaster 'a bildirir. İçindeki ApplicationMaster, uygulamanın durumunu ResourceManager 'ya geri bildirir. ResourceManager, istemciye herhangi bir sonuç döndürür.

## <a name="yarn-on-hdinsight"></a>HDInsight üzerinde YARN

Tüm HDInsight küme türleri YARN 'yi dağıtır. ResourceManager, birincil ve ikincil örnekle, sırasıyla küme içindeki birinci ve ikinci baş düğümlerde çalışan yüksek kullanılabilirlik için dağıtılır. Tek seferde yalnızca bir ResourceManager örneği etkindir. NodeManager örnekleri, kümedeki kullanılabilir çalışan düğümleri arasında çalışır.

![Azure HDInsight 'ta Apache YARN](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde Apache Hadoop ile MapReduce'u kullanma](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight 'a giriş](hadoop/apache-hadoop-introduction.md)
