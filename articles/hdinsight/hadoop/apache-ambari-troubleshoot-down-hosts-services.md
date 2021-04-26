---
title: Apache ambarı Kullanıcı arabirimi, Azure HDInsight 'ta konakları ve Hizmetleri gösterir
description: Azure HDInsight 'ta konakları ve Hizmetleri gösterdiği bir Apache ambarı Kullanıcı arabirimi sorununu giderme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943268"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Senaryo: Apache ambarı Kullanıcı arabirimi, Azure HDInsight 'ta konakları ve Hizmetleri gösterir

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ambarı Kullanıcı arabirimine erişilebilir, ancak kullanıcı arabirimi neredeyse tüm hizmetleri gösteriyor, sinyal kaybı gösteren tüm konaklar.

## <a name="cause"></a>Nedeni

Çoğu senaryoda, bu, ambarı sunucusu etkin yayın düğümünde çalıştırmayan bir sorundur. Hangi baş düğümüne 'un etkin bir yayın düğümü olduğunu kontrol edin ve tek başına ambarı-sunucunuzun çalıştığından emin olun. Ambarı-sunucu ' ı el ile başlatmayın, yük devretme denetleyicisi hizmeti 'nin, doğru baş düğümde ambarı-sunucu başlatmasından sorumlu olmasına izin verin. Yük devretmeyi zorlamak için etkin yayın düğümünü yeniden başlatın.

Ağ sorunları da bu soruna neden olabilir. Her küme düğümünden, ping işlemi yapabiliyor musunuz? bölümüne bakın `headnodehost` . Küme düğümünün bağlanamabileceği nadir bir durum vardır `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Çözüm

Genellikle etkin baş düğümüne 'un yeniden başlatılması bu sorunu azaltır. Değilse lütfen HDInsight destek ekibine başvurun.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]