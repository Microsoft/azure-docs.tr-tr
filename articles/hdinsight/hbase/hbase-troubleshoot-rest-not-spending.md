---
title: Apache HBase REST, Azure HDInsight 'taki isteklere yanıt vermiyor
description: Azure HDInsight 'taki isteklere yanıt vermeyen Apache HBase REST ile ilgili sorunu çözün.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 81126e0d2b87a84c81a3be8682ad41813991dc81
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091596"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Senaryo: Apache HBase REST, Azure HDInsight 'taki isteklere yanıt vermiyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache HBase REST hizmeti, Azure HDInsight 'taki isteklere yanıt vermez.

## <a name="cause"></a>Nedeni

Buradaki olası neden Apache HBase REST hizmeti, hizmet uzun bir süre (örneğin, aylar) üzerinde çalışırken yaygın olan yuvalara sızıyor olabilir. İstemci SDK 'Sı ' nden şuna benzer bir hata iletisi görebilirsiniz:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Çözüm

Ana bilgisayara Ssholduktan sonra aşağıdaki komutu kullanarak HBase REST 'i yeniden başlatın. Bu hizmeti tüm çalışan düğümlerinde yeniden başlatmak için betik eylemlerini de kullanabilirsiniz:

```bash
sudo service hdinsight-hbrest restart
```

Bu komut, aynı konaktaki HBase bölge sunucusunu durdurur. HBase bölge sunucusunu ambarı aracılığıyla el ile başlatabilir veya ambarı otomatik yeniden başlatma işlevselliğinin HBase bölge sunucusunu otomatik olarak kurtarmasına izin verebilirsiniz.

Sorun devam ederse, aşağıdaki risk azaltma betiğini her çalışan düğümünde 5 dakikada bir çalışan bir CRON işi olarak yükleyebilirsiniz. Bu risk azaltma betiği REST hizmetine ping atar ve REST hizmetinin yanıt vermediği durumlarda yeniden başlatır.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* Azure Community [@AzureSupport](https://twitter.com/azuresupport) 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
