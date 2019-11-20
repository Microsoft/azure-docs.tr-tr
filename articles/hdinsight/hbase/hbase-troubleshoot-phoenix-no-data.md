---
title: HDP yükseltme & Azure HDInsight 'ta Apache Phoenix görünümlerde veri yok
description: HDP yükseltmesi Azure HDInsight 'ta Apache Phoenix görünümlerde veri oluşmasına neden oluyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 148c7ea4bb3eef3fff7c1a8fd3b865791613704f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498035"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>Senaryo: HDP yükseltmesi Azure HDInsight 'ta Apache Phoenix görünümlerde veri oluşmasına neden oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Phoenix bir görünüm, HDP 2,4 ' den HDP 2,5 ' ye yükselttikten sonra bir tarih içermez.

## <a name="cause"></a>Nedeni

Görünümler için dizin tablosu (görünüm için tüm dizinler tek bir fiziksel Apache HBase tablosunda depolanır) yükseltme sırasında kesilir

## <a name="resolution"></a>Çözünürlük

Yükseltmeden sonra tüm görünüm dizinlerini bırakıp yeniden oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabı. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik Yönetimi’ne ve faturalandırma desteğine erişim Microsoft Azure aboneliğinize dahildir, Teknik Destek ise herhangi bir [Azure Destek Planı](https://azure.microsoft.com/support/plans/) üzerinden sağlanır.
