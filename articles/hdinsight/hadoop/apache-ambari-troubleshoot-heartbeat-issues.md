---
title: Azure HDInsight'ta Apache Ambari sinyal sorunları
description: Azure HDInsight 'ta Apache ambarı sinyal sorunları için çeşitli nedenleri gözden geçirme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946727"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight'ta Apache Ambari sinyal sorunları

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="scenario-high-cpu-utilization"></a>Senaryo: yüksek CPU kullanımı

### <a name="issue"></a>Sorun

Ambarı Aracısı yüksek CPU kullanımına sahiptir ve bu, bazı düğümlerde ambarı aracı sinyalinin kaybolduğu bir şekilde, ambarı kullanıcı arabiriminden gelen uyarılarla sonuçlanır. Sinyal kayıp uyarısı genellikle geçicidir.

### <a name="cause"></a>Nedeni

Çeşitli ambarı Aracısı hataları nedeniyle, ender durumlarda, ambarı aracılarınız yüksek (100 ' e kadar) CPU kullanımına sahip olabilir.

### <a name="resolution"></a>Çözüm

1. Ambarı aracısının işlem KIMLIĞINI (PID) tanımla:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Ardından CPU kullanımını göstermek için şu komutu çalıştırın:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Sorunu azaltmak için ambarı-aracıyı yeniden başlatın:

    ```bash
    service ambari-agent restart
    ```

1. Yeniden başlatma çalışmazsa, ambarı Aracısı işlemini sonlandırın ve sonra başlatın:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Senaryo: ambarı aracısı başlatılmadı

### <a name="issue"></a>Sorun

Ambarı Aracısı, bazı düğümlerde, ambarı Aracısı sinyalinin kaybolduğu bazı düğümlere yönelik olarak, hangi düğümlerin uyarı ile sonuçlandığına neden olur.

### <a name="cause"></a>Nedeni

Uyarılar, ambarı aracısının çalışmadığı kaynaklardır.

### <a name="resolution"></a>Çözüm

1. Ambarı-aracısının durumunu onaylayın:

    ```bash
    service ambari-agent status
    ```

1. Yük devretme denetleyicisi hizmetlerinin çalışıp çalışmadığını onaylayın:

    ```bash
    ps -ef | grep failover
    ```

    Yük devretme denetleyicisi Hizmetleri çalışmıyorsa, muhtemelen bir sorun nedeniyle HDInsight-Agent ' ın yük devretme denetleyicisi 'ni başlatmasını engelliyor olabilir. HDInsight-Agent günlük dosyasını denetleyin `/var/log/hdinsight-agent/hdinsight-agent.out` .

## <a name="scenario-heartbeat-lost-for-ambari"></a>Senaryo: ambarı için Sinyal kayboldu

### <a name="issue"></a>Sorun

Ambarı sinyal Aracısı kayboldu.

### <a name="cause"></a>Nedeni

OMS günlükleri yüksek CPU kullanımına neden oluyor.

### <a name="resolution"></a>Çözüm

* [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell cmdlet 'Ini kullanarak Azure izleyici günlüğünü devre dışı bırakın.
* `mdsd.warn`Günlük dosyasını Sil

---

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]