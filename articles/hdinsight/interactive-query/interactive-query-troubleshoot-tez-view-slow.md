---
title: Apache ambarı tez görünümü, Azure HDInsight 'ta yavaş yükleniyor
description: Apache ambarı tez görünümü yavaş yükleme veya Azure HDInsight 'ta hiç yüklenmeyebilir
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 218f1b66152354cbf7f23b48e0ebf01be62e5550
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091262"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Senaryo: Apache ambarı tez görünümü, Azure HDInsight 'ta yavaş yükleniyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ambarı tez görünümü yavaş çalışabilir veya hiç yüklenmeyebilir. Ambarı tez görünümü yüklenirken, yük düğümlerinde işlemlerin yanıt vermemesine neden olabilir.

## <a name="cause"></a>Nedeni

Yarn ATS API 'Lerine erişmek, küme üzerinde çok sayıda Hive işi çalıştırılan durumlarda 2017 eki olmadan önce oluşturulan kümeler üzerinde düşük performansa sahip olabilir.

## <a name="resolution"></a>Çözüm

Bu, Ekim 2017 ' de düzeltilen bir sorundur. Kümenizi yeniden oluşturmak bu sorunu tekrar çalıştırmayacak.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* Azure Community [@AzureSupport](https://twitter.com/azuresupport) 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
