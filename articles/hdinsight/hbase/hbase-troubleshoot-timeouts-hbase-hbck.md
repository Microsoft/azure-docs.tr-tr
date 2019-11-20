---
title: Azure HDInsight 'ta ' HBase hbck ' komutuyla zaman aşımları
description: Bölge atamalarını çözmede ' HBase hbck ' komutuyla zaman aşımı sorunu
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091572"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta ' HBase hbck ' komutuyla zaman aşımları

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Bölge atamalarını düzelterek `hbase hbck` komutla birlikte zaman aşımları ile karşılaşırsınız.

## <a name="cause"></a>Nedeni

`hbck` Komutu kullandığınızda zaman aşımı sorunları için olası bir neden, "geçiş sürüyor" durumunda birkaç bölgenin uzun bir süre olması olabilir. Bu bölgeleri HBase Master Kullanıcı arabiriminde çevrimdışı olarak görebilirsiniz. Çok sayıda bölge geçişe çalıştığından HBase Master zaman aşımına uğrar ve bu bölgeleri yeniden çevrimiçi getiremeyebilirsiniz.

## <a name="resolution"></a>Çözüm

1. SSH kullanarak HDInsight HBase kümesinde oturum açın.

1. Apache ZooKeeper `hbase zkcli` Shell ile bağlanmak için komutunu çalıştırın.

1. `rmr /hbase/regions-in-transition` Veya`rmr /hbase-unsecure/regions-in-transition` komutunu çalıştırın.

1. Komutunu kullanarak `hbase zkcli` `exit` kabuktan çıkın.

1. Apache ambarı kullanıcı arabiriminden, etkin HBase Master hizmetini yeniden başlatın.

1. `hbase hbck -fixAssignments` komutunu çalıştırın.

1. Hiçbir bölgenin takılı olmadığından emin olmak için, bu bölümdeki HBase Master Kullanıcı arabirimi "geçiş sırasında bölgesi" ni izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
