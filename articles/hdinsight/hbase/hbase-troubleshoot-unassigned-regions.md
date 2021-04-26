---
title: Azure HDInsight 'ta bölge sunucularıyla ilgili sorunlar
description: Azure HDInsight 'ta bölge sunucularıyla ilgili sorunlar
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936616"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 'ta bölge sunucularıyla ilgili sorunlar

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="scenario-unassigned-regions"></a>Senaryo: atanmamış bölgeler

### <a name="issue"></a>Sorun

Komut çalıştırılırken `hbase hbck` Şuna benzer bir hata iletisi görürsünüz:

```
multiple regions being unassigned or holes in the chain of regions
```

Apache HBase Master kullanıcı arabiriminden, tüm bölge sunucularında dengesiz olan bölge sayısını görebilirsiniz. Ardından, `hbase hbck` bölge zincirindeki delikleri görmek için komutunu çalıştırabilirsiniz.

### <a name="cause"></a>Nedeni

Boşluklar, çevrimdışı bölgelerin sonucu olabilir.

### <a name="resolution"></a>Çözüm

Atamaları düzeltir. Atanmamış bölgeleri normal duruma geri getirmek için aşağıdaki adımları izleyin:

1. SSH kullanarak HDInsight HBase kümesinde oturum açın.

1. `hbase zkcli`ZooKeeper Shell ile bağlanmak için komutunu çalıştırın.

1. `rmr /hbase/regions-in-transition`Veya `rmr /hbase-unsecure/regions-in-transition` komutunu çalıştırın.

1. Komutu kullanarak Zookeeper kabuğu 'ndan çıkın `exit` .

1. Apache ambarı Kullanıcı arabirimini açın ve ardından etkin HBase Master hizmetini yeniden başlatın.

1. `hbase hbck`Komutu yeniden çalıştırın (başka herhangi bir seçenek olmadan). Çıktıyı denetleyin ve tüm bölgelerin atandığından emin olun.

---

## <a name="scenario-dead-region-servers"></a>Senaryo: ölü bölge sunucuları

### <a name="issue"></a>Sorun

Bölge sunucuları başlatılamadı.

### <a name="cause"></a>Nedeni

Birden çok bölme dizini.

1. Geçerli WALs listesini al: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Dosyayı inceleyin `wals.out` . Çok fazla sayıda bölme dizini varsa (*-bölme ile başlayarak), bu dizinler nedeniyle bölge sunucusu muhtemelen başarısız olur.

### <a name="resolution"></a>Çözüm

1. Ambarı portalından HBase 'i durdurun.

1. `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`WALs 'in yeni listesini almak için yürütün.

1. *-Bölme dizinlerini geçici bir klasöre taşıyın `splitWAL` ve *-bölme dizinlerini silin.

1. `hbase zkcli`Zookeeper Shell ile bağlanmak için komutunu yürütün.

1. Yürütün `rmr /hbase-unsecure/splitWAL` .

1. HBase hizmetini yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.