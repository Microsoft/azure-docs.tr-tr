---
title: Azure HDInsight sürüm notları
description: Azure HDInsight için en son sürüm notları. Hadoop, Spark, R Server, Hive ve daha fazlası için geliştirme ipuçları ve ayrıntıları alın.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 838eb517697c0625139058a19c7def764e869ed5
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588182"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight sürüm notları

Bu makalede **en son** Azure HDInsight sürüm güncelleştirmeleri hakkında bilgi sağlanır. Önceki sürümler hakkında daha fazla bilgi için bkz. [HDInsight sürüm notları Arşivi](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Özet

Azure HDInsight, Azure üzerinde açık kaynaklı analizler için kurumsal müşteriler arasındaki en popüler hizmetlerden biridir.

Sürüm notlarında abone olmak istiyorsanız, [Bu GitHub deposundaki](https://github.com/hdinsight/release-notes/releases)yayınları izleyin.

## <a name="release-date-03242021"></a>Yayın tarihi: 03/24/2021

Bu sürüm hem HDInsight 3,6 hem de HDInsight 4,0 için geçerlidir. HDInsight yayını, birkaç gün boyunca tüm bölgeler için kullanılabilir hale getirilir. Burada Yayımlanma tarihi, ilk bölgenin yayın tarihini gösterir. Değişiklikleri aşağıda görmüyorsanız, bölgenin bölgeniz için birkaç gün içinde canlı olmasını bekleyin.

## <a name="new-features"></a>Yeni özellikler
### <a name="spark-30-preview"></a>Spark 3,0 Önizleme
HDInsight, HDInsight 4,0 'e bir önizleme özelliği olarak [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) desteği ekledi. 

### <a name="kafka-24-preview"></a>Kafka 2,4 Önizleme
HDInsight, [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) desteğini HDInsight 4,0 ' ye önizleme özelliği olarak ekledi.

### <a name="eav4-series-support"></a>Eav4 serisi desteği
HDInsight bu sürümde Eav4 serisi destek ekledi. [Burada Dav4-Series](../virtual-machines/eav4-easv4-series.md)hakkında daha fazla bilgi edinin. Seri, aşağıdaki bölgelerde kullanılabilir hale getirilir: 

* Doğu Avustralya
* Güney Brezilya
* Central US
* Doğu Asya
* Doğu ABD
* Doğu Japonya
* Güneydoğu Asya
* Güney Birleşik Krallık
* West Europe
* Batı ABD 2

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümelerine geçme
HDInsight artık kümeyi sağlamak için Azure sanal makinelerini kullanır. Hizmet giderek [Azure sanal makine ölçek kümelerine geçiş yapar](../virtual-machine-scale-sets/overview.md). İşlemin tamamı ayda sürebilir. Bölgelerinizden ve abonelikleriniz geçirildikten sonra, yeni oluşturulan HDInsight kümeleri, müşteri eylemleri olmadan sanal makine ölçek kümelerinde çalışır. Hiçbir bölme değişikliği beklenmez.

## <a name="deprecation"></a>Kullanımdan kaldırma
Bu yayında kullanımdan kaldırma yok.

## <a name="behavior-changes"></a>Davranış değişiklikleri
### <a name="default-cluster-version-is-changed-to-40"></a>Varsayılan küme sürümü 4,0 olarak değiştirilmiştir
HDInsight kümesinin varsayılan sürümü 3,6 ' den 4,0 ' e değiştirilmiştir. Kullanılabilir sürümler hakkında daha fazla bilgi için bkz. [kullanılabilir sürümler](./hdinsight-component-versioning.md). [Hdınsight 4,0](./hdinsight-version-release.md)' deki yenilikler hakkında daha fazla bilgi edinin.

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Varsayılan küme VM boyutları Ev3-Series olarak değiştirilir 
Varsayılan küme VM boyutları D serisinden Ev3 serisi olarak değiştirilir. Bu değişiklik, baş düğümler ve çalışan düğümleri için geçerlidir. Bu değişikliğin test edilen iş akışlarınızı etkileyerek etkilenmesinden kaçınmak için ARM şablonunda kullanmak istediğiniz VM boyutlarını belirtin.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümelerinde çalışan kümeler için ağ arabirimi kaynağı görünmüyor
HDInsight, giderek Azure sanal makine ölçek kümelerine geçiş yapar. Sanal makineler için ağ arabirimleri artık, Azure sanal makine ölçek kümeleri kullanan kümeler için müşterilere görünmez.

## <a name="upcoming-changes"></a>Yaklaşan değişiklikler
Gelecek sürümlerde aşağıdaki değişiklikler olur.

### <a name="os-version-upgrade"></a>İşletim sistemi sürümü yükseltme
HDInsight kümeleri Şu anda Ubuntu 16,04 LTS üzerinde çalışıyor. [Ubuntu 'ın yayın döngüsünde](https://ubuntu.com/about/release-cycle)başvurulduğundan, ubuntu 16,04 çekirdeği, 2021 Nisan 'Daki yaşam sonuna (EOL) ulaşacaktır. Mayıs 2021 ' de Ubuntu 18,04 üzerinde çalışan yeni HDInsight 4,0 kümesi görüntüsünü kullanıma sunacağız. Yeni oluşturulan HDInsight 4,0 kümeleri, varsayılan olarak, varsa Ubuntu 18,04 üzerinde çalışacaktır. Ubuntu 16,04 üzerindeki mevcut kümeler tam destek ile olduğu gibi çalışacaktır.

HDInsight 3,6, Ubuntu 16,04 ' de çalışmaya devam edecektir. 30 Haziran 2021 ' e kadar standart destek sonuna ulaşacak ve 1 Temmuz 2021 ' de başlayan Temel destek olarak değişecektir. Tarihler ve destek seçenekleri hakkında daha fazla bilgi için bkz. [Azure HDInsight sürümleri](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Ubuntu 18,04, HDInsight 3,6 için desteklenmez. Ubuntu 18,04 kullanmak isterseniz, kümelerinizi HDInsight 4,0 ' ye geçirmeniz gerekir. 

Mevcut kümeleri Ubuntu 18,04 ' ye taşımak istiyorsanız, kümelerinizi bırakıp yeniden oluşturmanız gerekir. Ubuntu 18,04 desteği kullanılabilir hale geldikten sonra lütfen kümenizi oluşturmayı veya yeniden oluşturmayı planlayın. Yeni görüntü tüm bölgelerde kullanılabilir hale geldikten sonra başka bir bildirim göndereceğiz.

Bir Ubuntu 18,04 sanal makinesi (VM) üzerinde Edge düğümlerinde dağıtılan özel uygulamalarınızı ve komut dosyası eylemlerinizi test etmeniz önemle önerilir. [18,04-LTS üzerinde basit bir Ubuntu LINUX VM oluşturabilir](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/), ardından, komut dosyası eylemlerinizi ve kenar düğümlerinde dağıtılan özel uygulamalarınızı çalıştırmak ve test etmek için VM 'niz üzerinde bir [GÜVENLI kabuk (SSH) anahtar çifti](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) oluşturup kullanabilirsiniz.

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>HDInsgiht 4,0 için baş düğüm olarak Stardard_A5 VM boyutunu devre dışı bırak
HDInsight kümesi baş düğümü, kümeyi başlatmaktan ve yönetmekten sorumludur. Standard_A5 VM boyutunun, HDInsight 4,0 için baş düğüm olarak güvenilirlik sorunları vardır. 2021 Mayıs 'ta bir sonraki sürümden itibaren, müşteriler baş düğüm olarak Standard_A5 VM boyutu ile yeni kümeler oluşturamayacak. E2_v3 veya E2s_v3 gibi diğer 2 çekirdekli VM 'Leri kullanabilirsiniz. Mevcut kümeler olduğu gibi çalışır. 4 çekirdekli bir VM, üretim HDInsight kümelerinizin yüksek kullanılabilirliğini ve güvenilirliğini sağlamak için baş düğüm için son derece önerilir.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>HDInsight 3,6, 1 Temmuz 2021 tarihinden itibaren Temel destek
Microsoft, 1 Temmuz 2021 ' den itibaren belirli HDInsight 3,6 küme türleri için [temel destek](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) sunacaktır. Temel destek planı, 3 Nisan 2022 ' ye kadar kullanılabilir olacaktır. 1 Temmuz 2021 tarihinden itibaren otomatik olarak Temel destek kaydedilir. Kabul etmeniz için herhangi bir işlem yapmanız gerekmez. Temel destek altına eklenen küme türleri için [belgelerimize](hdinsight-36-component-versioning.md) bakın. 

HDInsight 3,6 ' de yeni çözümler oluşturmanızı önermeyiz, var olan 3,6 ortamlarındaki değişiklikleri donduruyoruz. [Kümelerinizi hdınsight 4,0 ' ye geçirmeniz](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)önerilir. [Hdınsight 4,0 ' deki](hdinsight-version-release.md#whats-new-in-hdinsight-40)yenilikler hakkında daha fazla bilgi edinin.

## <a name="bug-fixes"></a>Hata düzeltmeleri
HDInsight, küme güvenilirliği ve performans iyileştirmeleri yapmaya devam eder. 

## <a name="component-version-change"></a>Bileşen sürümü değişikliği
Inceleme olarak Spark 3.0.0 ve Kafka 2.4.1 desteği eklendi. HDInsight 4,0 ve HDInsight 3,6 için geçerli bileşen sürümlerini [Bu belgede](./hdinsight-component-versioning.md)bulabilirsiniz.

## <a name="recommanded-features"></a>Yeniden konumlandırılan Özellikler
### <a name="service-tags"></a>Hizmet etiketleri
Hizmet etiketleri, Azure sanal makineleri ve Azure sanal ağları için Azure hizmetlerine ağ erişimini kısıtlamayı basitleştirir. Ağ güvenlik grubu (NSG) kurallarınızın hizmet etiketleri belirli bir Azure hizmetine giden trafiğe izin verir veya bu trafiği reddedebilir. Kural, genel olarak veya Azure bölgesi başına ayarlanabilir. Azure, her etiketi temel alan IP adreslerinin bakımını sağlar. Ağ güvenlik grupları (NSG 'ler) için HDInsight hizmet etiketleri, sistem durumu ve Yönetim Hizmetleri için IP adresi gruplarıdır. Bu gruplar, güvenlik kuralı oluşturma karmaşıklığına en aza indirmenize yardımcı olur. HDInsight müşterileri, Service Tag 'i Azure portal, PowerShell ve REST API aracılığıyla etkinleştirebilir. Daha fazla bilgi için bkz. [Azure HDInsight Için ağ güvenlik grubu (NSG) hizmet etiketleri](./hdinsight-service-tags.md).
