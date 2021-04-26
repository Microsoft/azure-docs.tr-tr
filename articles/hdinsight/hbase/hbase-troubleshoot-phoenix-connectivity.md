---
title: Azure HDInsight 'ta bağlantı sorunlarını Apache Phoenix
description: Azure HDInsight 'ta Apache HBase ve Apache Phoenix arasındaki bağlantı sorunları
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 2cc6556f681ece170bdfe02b985f56274c0faa1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936965"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta bağlantı sorunlarını Apache Phoenix

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Phoenix ile Apache HBase bağlantısı kurulamıyor. Nedenler farklılık gösterebilir.

## <a name="cause-incorrect-ip"></a>Neden: yanlış IP

Etkin Zookeeper düğümünün yanlış IP 'si.

### <a name="resolution"></a>Çözüm

Active Zookeeper düğümünün IP 'si, **HBase**  >  **hızlı bağlantılar**  >  **ZK (etkin)**  >  **Zookeeper Info** bağlantılarını izleyerek, ambarı kullanıcı arabiriminden belirlenebilir. IP 'yi gerektiği gibi düzeltin.

---

## <a name="cause-systemcatalog-table-offline"></a>Neden: SISTEM. Katalog tablosu çevrimdışı

Gibi komutları çalıştırırken `!tables` Şuna benzer bir hata iletisi alırsınız:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Gibi komutları çalıştırırken `count 'SYSTEM.CATALOG'` Şuna benzer bir hata iletisi alırsınız:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Çözüm

Apache ambarı kullanıcı arabiriminden, tüm ZooKeeper düğümlerinde HMaster hizmetini yeniden başlatmak için aşağıdaki adımları izleyin:

1. HBase 'in **Özet** bölümünden **HBase**  >  **etkin HBase Master**' a gidin.

1. **Bileşenler** bölümünden HBase Master hizmetini yeniden başlatın.

1. Kalan tüm **bekleme HBase Master** Hizmetleri için bu adımları yineleyin.

HBase Master hizmetin kurtarma işleminin kararlı olması beş dakika kadar sürebilir. `SYSTEM.CATALOG`Tablo normal 'e geri yüklendikten sonra Apache Phoenix bağlantı sorunu otomatik olarak çözümlenmelidir.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.