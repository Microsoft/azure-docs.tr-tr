---
title: HDP yükseltme & Azure HDInsight 'ta Apache Phoenix görünümlerde veri yok
description: HDP yükseltmesi Azure HDInsight 'ta Apache Phoenix görünümlerde veri oluşmasına neden oluyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: f68e227e10a85b7e1d98dcabbfb870933059ec04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936935"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>Senaryo: HDP yükseltmesi Azure HDInsight 'ta Apache Phoenix görünümlerde veri oluşmasına neden oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Phoenix bir görünüm, HDP 2,4 ' den HDP 2,5 ' ye yükselttikten sonra bir tarih içermez.

## <a name="cause"></a>Nedeni

Görünümler için dizin tablosu (görünüm için tüm dizinler tek bir fiziksel Apache HBase tablosunda depolanır) yükseltme sırasında kesilir

## <a name="resolution"></a>Çözüm

Yükseltmeden sonra tüm görünüm dizinlerini bırakıp yeniden oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.