---
title: HDInsight 'ta Azure depolama 'ya genel bakış
description: HDInsight 'ta Azure depolama 'ya genel bakış.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 419b4939c19ee03651a406db44244897e17087f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865477"
---
# <a name="azure-storage-overview-in-hdinsight"></a>HDInsight 'ta Azure depolama 'ya genel bakış

Azure depolama, HDInsight ile sorunsuz bir şekilde tümleşen sağlam bir genel amaçlı depolama çözümüdür. HDInsight, Azure Depolama’daki bir blob kapsayıcıyı kümenin varsayılan dosya sistemi olarak kullanabilir. Bir bir bir, bir bir bir bir bir bir bir işlem arabirimi aracılığıyla, HDInsight 'taki tüm bileşen kümesi blob olarak depolanan yapılandırılmış veya yapılandırılmamış verilerde doğrudan çalışabilir

Varsayılan küme depolama alanı ve iş verileriniz için ayrı depolama kapsayıcıları kullanmanızı öneririz. Ayrım, HDInsight günlüklerini ve geçici dosyaları kendi iş verilerinden yalıtmaktır. Ayrıca, her bir depolama maliyetini azaltmak için, uygulama ve sistem günlükleri içeren varsayılan blob kapsayıcısını silmenizi öneririz. Kapsayıcıyı silmeden önce günlükleri aldığınızdan emin olun.

Depolama hesabınızı **Seçili ağlardaki** **güvenlik duvarları ve sanal ağlar** kısıtlamalarına göre güvenli hale getirmek istiyorsanız, **Güvenilen Microsoft hizmetlerine izin ver** özel durumunu etkinleştirdiğinizden emin olun... Özel durum, HDInsight 'ın depolama hesabınıza erişebilmesi için kullanılır.

## <a name="hdinsight-storage-architecture"></a>HDInsight depolama mimarisi

Aşağıdaki diyagramda Azure Storage 'ın HDInsight mimarisinin soyut bir görünümü sunulmaktadır:

:::image type="content" source="./media/overview-azure-storage/storage-architecture.png" alt-text="HDInsight depolama mimarisi" border="false":::

HDInsight, işlem düğümlerine yerel olarak bağlı olan dağıtılmış dosya sistemine erişim imkanı sağlar. Bu dosya sistemine tam uygun URI kullanılarak erişilebilir, örneğin:

`hdfs://<namenodehost>/<path>`

HDInsight aracılığıyla Azure Storage 'daki verilere de erişebilirsiniz. Söz dizimi şu şekildedir:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

HDInsight kümeleri ile Azure depolama hesabı kullanırken aşağıdaki ilkeleri göz önünde bulundurun:

* **Bir kümeye bağlı depolama hesaplarındaki kapsayıcılar:** Oluşturma sırasında hesap adı ve anahtar kümeyle ilişkilendirildiğinden, bu kapsayıcılardaki blob'lara tam erişiminiz vardır.

* **Bir kümeye bağlı olmayan depolama hesaplarındaki genel kapsayıcılar veya genel Bloblar:** Kapsayıcılarda bloblara salt okuma izninizin olması gerekir.
  
  > [!NOTE]
  > Ortak kapsayıcılar, bu kapsayıcıda kullanılabilen tüm Blobların bir listesini almanızı ve kapsayıcı meta verilerini almanızı sağlar. Genel blob'lar, yalnızca tam URL'yi biliyorsanız blob erişiminize izin verir. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../storage/blobs/anonymous-read-access-configure.md).

* **Bir kümeye bağlı olmayan depolama hesaplarındaki özel kapsayıcılar:** Web Hcat işlerini gönderdiğinizde depolama hesabını tanımlamadığınız müddetçe kapsayıcılardaki bloblara erişemezsiniz.

Oluşturma işleminde tanımlanan depolama hesapları ve bunların anahtarların %HADOOP_HOME%/conf/core-site.xml küme düğümlerinde depolanır. HDInsight, varsayılan olarak core-site.xml dosyasında tanımlanan depolama hesaplarını kullanır. Bu ayarı, [Apache ambarı](./hdinsight-hadoop-manage-ambari.md)'nı kullanarak değiştirebilirsiniz.

Apache Hive dahil olmak üzere birden çok WebHCat işi. Ve MapReduce, Apache Hadoop streaming ve Apache Pig, depolama hesaplarının ve meta verilerin bir açıklamasını taşır. (Bu boyut Şu anda depolama hesapları olan Pig için geçerlidir, ancak meta veriler için değil.) Daha fazla bilgi için bkz. [alternatif depolama hesaplarıyla HDInsight kümesi ve meta depolar kullanma](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Bloblar yapılandırılmış ve yapılandırılmamış veriler için kullanılabilir. Blob kapsayıcıları, verileri anahtar/değer çiftleri olarak depolar ve Dizin hiyerarşisine sahip değildir. Bununla birlikte, anahtar adı, bir dosyanın dizin yapısında depolanmış gibi görünmesini sağlamak için eğik çizgi karakteri (/) içerebilir. Örneğin, bir Blobun anahtarı olabilir `input/log1.txt` . Gerçek `input` Dizin yok, ancak anahtar adındaki eğik çizgi karakteri nedeniyle anahtar bir dosya yolu gibi görünüyor.

## <a name="benefits-of-azure-storage"></a>Azure Depolamanın yararları

Birlikte bulunan işlem kümeleri ve depolama kaynakları, kapsanan performans maliyetlerine sahiptir. Bu maliyetler, işlem kümelerinin Azure bölgesindeki depolama hesabı kaynaklarına yakın şekilde oluşturulduğu şekilde azaltıldığında. Bu bölgede, işlem düğümleri Azure Storage içindeki yüksek hızlı ağ üzerinden verilere verimli bir şekilde erişebilir.

Verileri, Azure Storage 'da, her ne kadar çok avantaj elde edersiniz:

* **Verileri yeniden kullanma ve paylaşma:** HDFS’deki veriler işlem kümesi içinde bulunur. Yalnızca işlem kümesi erişimi olan uygulamalar HDFS API'lerini kullanarak verileri kullanabilir. Azure depolama 'daki verilere, buna karşılık olarak,,, bu,,,,,,,,,,,,,,,,, Bu düzenleme nedeniyle, verileri oluşturmak ve kullanmak için daha büyük bir uygulamalar kümesi (diğer HDInsight kümeleri dahil olmak üzere) ve araçları kullanılabilir.

* **Veri arşivleme:** Veriler Azure depolama 'da depolandığında, hesaplama için kullanılan HDInsight kümeleri, Kullanıcı verilerini kaybetmeden güvenle silinebilir.

* **Veri depolama maliyeti:** Verilerin DFS 'de uzun süreli depolanması, verilerin Azure depolama 'da depolanmadan daha maliyetlidir. İşlem kümesinin maliyeti, Azure depolama maliyetinden daha yüksek olduğundan. Ayrıca, verilerin her işlem kümesi üretimi için yeniden yüklenmesi gerektiğinden, veri yükleme maliyetlerini de kaydediyorsunuz.

* **Esnek ölçeklendirme:** HDFS size ölçeklendirilmiş dosya sistemi sağlamakla birlikte, ölçek, kümeniz için oluşturduğunuz düğüm sayısı tarafından belirlenir. Ölçeği değiştirmek, Azure depolama 'da otomatik olarak aldığınız esnek ölçeklendirme özelliğinden daha karmaşık olabilir.

* **Coğrafi çoğaltma:** Azure depolama, coğrafi olarak çoğaltılabilir. Coğrafi çoğaltma size coğrafi kurtarma ve veri yedekliliği sağlasa da, coğrafi olarak çoğaltılan konuma yönelik bir yük devretme performansı önemli ölçüde etkiler ve ek ücret ödemeniz gerekebilir. Bu nedenle, coğrafi çoğaltma ve yalnızca verilerin değeri ek maliyeti ortaya çıkarabiliyorsanız seçin.

Belirli MapReduce işleri ve paketleri, Azure depolama 'da depolamak istemediğiniz ara sonuçlar oluşturabilir. Bu durumda, verileri yerel olarak depolamayı tercih edebilirsiniz. HDInsight, Hive işlerinde ve diğer işlemlerde bu ara sonuçlardan birkaçı için DFS kullanır.

> [!NOTE]  
> En çok bu komut (örneğin,,, `ls` `copyFromLocal` ve `mkdir` ) Azure depolama 'da beklendiği gibi çalışır. Yalnızca yerel bir IBU uygulamaya özgü komutlar (DFS olarak adlandırılır), `fschk` ve gibi `dfsadmin` Azure depolama 'da farklı davranışlar gösterir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Storage 2. Nesil'e giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Depolama’ya giriş](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage 1. Nesil’e genel bakış](./overview-data-lake-storage-gen1.md)
