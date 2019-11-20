---
title: Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor
description: Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 7091e638743fb8cd1488fe7e332378bf89304af1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087076"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ZooKeeper sunucusu sağlıksız, belirtiler şunları içerebilir: hem kaynak yöneticileri/ad düğümleri bekleme modunda, basit Ise işlemleri çalışmaz, `zkFailoverController` durdurulur ve başlatılamaz, Yarn/Spark/Livy işleri ZooKeeper hataları nedeniyle başarısız olur. Şuna benzer bir hata iletisi görebilirsiniz:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>Nedeni

Anlık görüntü dosyaları hacmi büyük veya anlık görüntü dosyaları bozuksa, ZooKeeper sunucusu bir çekirdek oluşturmayacak ve bu da ZooKeeper ilgili hizmetlerin sağlıksız olmasına neden olur. ZooKeeper sunucusu eski anlık görüntü dosyalarını veri dizininden kaldırmayacak, bunun yerine, ZooKeeper 'in sistem durumunu korumak için Kullanıcı tarafından gerçekleştirilecek düzenli bir görevdir. Daha fazla bilgi için bkz. [ZooKeeper güçleri ve sınırlamaları](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Çözüm

ZooKeeper veri dizinini `/hadoop/zookeeper/version-2` denetleyin ve `/hadoop/hdinsight-zookeepe/version-2` anlık görüntülerin dosya boyutunun büyük olup olmadığını öğrenin. Büyük anlık görüntüler varsa aşağıdaki adımları uygulayın:

1. `/hadoop/zookeeper/version-2` Ve`/hadoop/hdinsight-zookeepe/version-2`' de anlık görüntüleri yedekleyin.

1. `/hadoop/zookeeper/version-2` Ve`/hadoop/hdinsight-zookeepe/version-2`' de anlık görüntüleri temizleyin.

1. Tüm ZooKeeper sunucularını Apache ambarı kullanıcı arabiriminden yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
