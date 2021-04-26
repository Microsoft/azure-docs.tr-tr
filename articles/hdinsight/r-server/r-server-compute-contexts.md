---
title: HDInsight 'ta ML Hizmetleri için işlem bağlamı seçenekleri-Azure
description: HDInsight üzerinde ML Hizmetleri olan kullanıcılara sunulan farklı işlem bağlamı seçenekleri hakkında bilgi edinin
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 71ce0d87faa33bd7d533242edfcf3b131c8f7e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943959"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri için işlem bağlamı seçenekleri

Azure HDInsight üzerinde ML Hizmetleri, işlem bağlamını ayarlayarak çağrıların nasıl yürütüleceğini denetler. Bu makalede, yürütmenin kenar düğümünün veya HDInsight kümesinin çekirdekleri arasında paralel olup olmadığını belirtmek için kullanılabilecek seçenekler özetlenmektedir.

Bir kümenin kenar düğümü, kümeye bağlanmak ve R betiklerinizi çalıştırmak için uygun bir yer sağlar. Edge düğümü ile, uç düğüm sunucusunun çekirdekleri arasında geri alınamaz şekilde dağıtılmış işlevleri çalıştırma seçeneğiniz vardır. Ayrıca, Iptal edilebilir ' in Hadoop haritasını azaltma veya Apache Spark işlem bağlamlarını kullanarak bunları kümenin düğümleri arasında çalıştırabilirsiniz.

## <a name="ml-services-on-azure-hdinsight"></a>Azure HDInsight 'ta ML Hizmetleri

[Azure HDInsight üzerinde ml Hizmetleri](r-server-overview.md) , R tabanlı analizler için en son özellikleri sağlar. [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob depolama") depolama hesabınızda, bir Data Lake Store veya yerel Linux dosya sisteminde Apache Hadoop bir kapsayıcıda depolanan verileri kullanabilir. ML Hizmetleri açık kaynak R üzerinde oluşturulmuş olduğundan, oluşturduğunuz R tabanlı uygulamalar 8000 + açık kaynaklı R paketlerinden herhangi birini uygulayabilir. Ayrıca, Microsoft 'un ML Hizmetleri 'ne dahil olan büyük veri analizi paketindeki, [iptal edilebilir](/machine-learning-server/r-reference/revoscaler/revoscaler)olan bu yordamları da kullanabilirler.  

## <a name="compute-contexts-for-an-edge-node"></a>Kenar düğümü için işlem bağlamları

Genel olarak, kenar düğümündeki ML Hizmetleri kümesinde çalıştırılan bir R betiği, o düğümdeki R yorumlayıcısı içinde çalışır. Özel durumlar, Iptal edilmiş bir işlevi çağıran bu adımlardır. Iptal etme işlemi, geri alınamaz işlem bağlamını nasıl ayarlayacağınızla belirlenen bir işlem ortamında çalışır.  R betiğini bir kenar düğümünden çalıştırdığınızda, işlem bağlamının olası değerleri şunlardır:

- Yerel sıralı (*Yerel*)
- Yerel paralel (*localpar*)
- Harita azalt
- Spark

*Yerel* ve *localpar* seçenekleri yalnızca **rxexec** çağrılarının nasıl yürütüldüğünden farklılık gösterir. Bunlar, örneğin, Iptal edilmiş bir **Numscaler Numcorestouse** seçeneği kullanılmadıkça, diğer tüm mevcut çekirdekler genelinde paralel bir biçimde yürütülür `rxOptions(numCoresToUse=6)` . Paralel yürütme seçenekleri en iyi performansı sunar.

Aşağıdaki tablo, çağrıların nasıl yürütüleceğini ayarlamak için çeşitli işlem bağlamı seçeneklerini özetler:

| İşlem bağlamı  | Nasıl ayarlanır                      | Yürütme bağlamı                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Yerel sıralı | rxSetComputeContext (' yerel ')    | Uç düğüm sunucusunun çekirdekleri arasında paralel yürütme, sanal olarak yürütülen rxExec çağrıları dışında |
| Yerel paralel   | rxSetComputeContext (' localpar ') | Uç düğüm sunucusunun çekirdekleri genelinde paralel yürütme |
| Spark            | RxSpark ()                       | HDI kümesinin düğümleri genelinde Spark aracılığıyla paralel olarak dağıtılmış yürütme |
| Harita azalt       | RxHadoopMR()                    | Harita aracılığıyla paralel olarak dağıtılmış yürütme, HDI kümesinin düğümleri genelinde azalmış |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>İşlem bağlamına karar verme yönergeleri

Her tercih ettiğiniz üç seçenekten hangisinin analiz çalışmalarınızın yapısına, boyutuna ve verilerinizin konumuna bağlıdır. Size, hangi işlem bağlamını kullanacağınızı belirten basit bir formül yoktur. Ancak, doğru seçimi yapmanıza yardımcı olabilecek bazı temel ilkeler, en azından, bir kıyaslama çalıştırmadan önce seçimlerinizi daraltmanıza yardımcı olur. Bu temel ilkeler şunları içerir:

- Yerel Linux dosya sistemi, bir daha hızlıdır.
- Veriler yerel ise ve XDF 'da ise yinelenen çözümlemeler daha hızlıdır.
- Bir metin veri kaynağından küçük miktarlarda veri akışı yapmak tercih edilir. Veri miktarı büyükse, analiz etmeden önce XDF öğesine dönüştürün.
- Verileri analiz için kenar düğümüne kopyalama veya akışa alma yükü çok büyük miktarlarda veri için yönetilemez hale gelir.
- ApacheSpark, Hadoop 'daki analizler için haritanın azalmasına kıyasla daha hızlıdır.

Bu ilkeler verildiğinde, aşağıdaki bölümlerde bir işlem bağlamı seçmek için bazı genel Thumb kuralları sunulur.

### <a name="local"></a>Yerel

- Analiz edilecek veri miktarı küçükse ve yinelenen analiz gerektirmiyorsa, *Yerel* veya *localpar* kullanarak doğrudan çözümleme yordamına akış yapın.
- Analiz edilecek veri miktarı küçük veya orta ölçekli ise ve yinelenen analiz gerektiriyorsa, bunu yerel dosya sistemine kopyalayın, XDF dosyasına aktarın ve *Yerel* veya *localpar* aracılığıyla çözümleyin.

### <a name="apache-spark"></a>Apache Spark

- Analiz edilecek veri miktarı büyükse, **Rxhivedata** veya **Rxparquetdata** kullanarak bir Spark veri çerçevesine veya (depolama bir sorun değilse), bu dosyayı Spark işlem bağlamını kullanarak analiz edin.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop eşleme azaltma

- Eşleme, genellikle daha yavaş olduğundan Spark işlem bağlamındaki bir sorun ortaya çıkardıysanız işlem bağlamını azaltır.  

## <a name="inline-help-on-rxsetcomputecontext"></a>RxSetComputeContext üzerinde satır içi yardım
Daha fazla bilgi ve Iptal işlemi bağlamlarının örnekleri için, rxSetComputeContext yönteminde R 'deki satır içi yardıma bakın, örneğin:

```console
> ?rxSetComputeContext
```

Ayrıca, [Machine Learning Server belgelerine](/machine-learning-server/) [dağıtılmış bilgi işlem genel bakış](/machine-learning-server/r/how-to-revoscaler-distributed-computing) bölümüne de bakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, yürütmenin kenar düğümünün veya HDInsight kümesinin çekirdekleri arasında paralel olup olmadığını belirtmek için kullanılabilecek seçenekler hakkında bilgi edindiniz. ML hizmetlerini HDInsight kümeleriyle kullanma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

- [Apache Hadoop için ML hizmetlerine genel bakış](r-server-overview.md)
- [HDInsight üzerinde ML Hizmetleri için Azure depolama seçenekleri](r-server-storage.md)