---
title: Azure HDInsight 'ta Apache ambarı kullanımı
description: Apache ambarı 'nın Azure HDInsight 'ta nasıl kullanıldığına ilişkin tartışma.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 6233c42d7fbf8dc7821d26f77171c44485fb8d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946934"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache ambarı kullanımı

HDInsight, Küme dağıtımı ve yönetimi için Apache ambarı 'nı kullanır. Ambarı aracıları her düğüm üzerinde çalışır (headnode, çalışan düğümü, Zookeeper ve edgenode varsa). Ambarı sunucusu yalnızca baş düğümüne üzerinde çalışır (hn0 veya hn1). Tek seferde yalnızca bir adet bir örnek ambarı sunucusu çalışır. Bu, HDInsight yük devretme denetleyicisi tarafından denetlenir. Yayın düğümlerinden biri yeniden başlatma veya bakım için kapalıysa, diğer bir baş düğüm etkin hale gelir ve ikinci baş düğümüne üzerinde ambarı sunucusu başlatılır.

Tüm küme yapılandırması, [ambarı kullanıcı arabiriminden](./hdinsight-hadoop-manage-ambari.md)yapılmalıdır, düğüm yeniden başlatıldığında tüm yerel değişikliklerin üzerine yazılır.

## <a name="failover-controller-services"></a>Yük devretme denetleyicisi Hizmetleri

HDInsight yük devretme denetleyicisi, geçerli etkin baş düğümüne işaret eden, baş düğümüne konağının IP adresini güncelleştirmeden de sorumludur. Tüm ambarı aracıları durum ve sinyali, baş düğümüne konağına bildirmek üzere yapılandırılır. Yük devretme denetleyicisi kümedeki her düğüm üzerinde çalışan bir hizmetler kümesidir; bunlar çalışmıyorsa, yayın düğümü yük devretmesi doğru çalışmayabilir ve Ambarı sunucusuna erişmeye çalışırken HTTP 502 ile karşılaşırsınız.

Hangi baş düğümüne 'un etkin olduğunu denetlemek için, bir yol kümedeki düğümlerden birine SSH olarak çalışır `ping headnodehost` ve IP 'yi iki baş düğümüne ile aynı şekilde karşılaştırın.

Yük devretme denetleyicisi Hizmetleri çalışmıyorsa, yayın düğümü yük devretmesi doğru şekilde gerçekleşmeyebilir ve bu, ambarı sunucusu çalıştırmayan anlamına gelebilir. Yük devretme denetleyicisi hizmetlerinin çalışıp çalışmadığını denetlemek için şunu yürütün:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Günlükler

Etkin headnode 'da, şu konumda yer alan ambarı sunucusu günlüklerini kontrol edebilirsiniz:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Kümedeki herhangi bir düğümde, şu adreste bulunan ambarı Aracısı günlüklerini denetleyebilirsiniz:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Hizmet başlatma dizileri

Bu, önyükleme zamanında hizmetin başlangıç dizisidir:

1. HDInsight-Agent yük devretme denetleyicisi hizmetlerini başlatır.
1. Yük devretme denetleyicisi Hizmetleri, etkin yayın düğümündeki her düğümde ve ambarı sunucusunda bulunan ambarı Aracısı 'nı başlatır.

## <a name="ambari-database"></a>Ambarı veritabanı

HDInsight, SQL veritabanı 'nda, ambarı sunucusu için veritabanı olarak kullanılacak bir veritabanı oluşturur. Varsayılan [hizmet katmanı S0 ' dir](../azure-sql/database/elastic-pool-scale.md).

Küme oluştururken, çalışan düğüm sayısı 16 ' dan büyük herhangi bir küme için, S2 veritabanı hizmet katmandır.

## <a name="takeaway-points"></a>Kalkış noktaları

Bir sorunu geçici olarak çözmek için hizmeti yeniden başlatmaya çalışmadığınız müddetçe, ambarı-sunucu veya ambarı-Aracısı hizmetlerini hiçbir şekilde el ile başlatmayın/durdurmayın. Yük devretmeyi zorlamak için etkin yayın düğümünü yeniden başlatabilirsiniz.

Herhangi bir küme düğümünde herhangi bir yapılandırma dosyasını hiçbir şekilde el ile değiştirmeyin, bu iş için ambarı Kullanıcı arabirimine izin verin.

## <a name="property-values-in-esp-clusters"></a>ESP kümelerindeki özellik değerleri

HDInsight 4,0 Kurumsal Güvenlik Paketi kümelerinde, `|` değişken sınırlayıcıları olarak virgüller yerine kanallar kullanın. Aşağıda bir örnek gösterilmiştir:

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache Ambari Web arabiriminden yönetme](hdinsight-hadoop-manage-ambari.md)
* [Apache ambarı 'nı kullanarak HDInsight kümelerini yönetme REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
