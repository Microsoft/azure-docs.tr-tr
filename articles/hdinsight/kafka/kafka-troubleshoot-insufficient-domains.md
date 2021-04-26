---
title: Azure HDInsight 'ta bölge hatasında yeterli hata etki alanı yok
description: Azure HDInsight 'taki bölgede yeterli hata etki alanı olmadığından küme oluşturma başarısız oldu
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: ed9e98750a469f78855096c3149254cf92c2788f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501354"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Senaryo: `not sufficient fault domains in region` Azure HDInsight 'Ta küme oluşturma işlemi başarısız oldu

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Kafka kümesi oluşturulmaya çalışılırken aşağıdakine benzer bir hata iletisi alın `not sufficient fault domains in region` .

## <a name="cause"></a>Nedeni

Hata etki alanı, bir Azure veri merkezinde temel donanımlardan oluşan mantıksal bir gruplandırmadır. Her hata etki alanı ortak bir güç kaynağı ve ağ anahtarına sahiptir. Bir HDInsight kümesi içindeki düğümleri uygulayan sanal makineler ve yönetilen diskler, bu hata etki alanlarına dağıtılır. Bu mimari, fiziksel donanım hatalarının olası etkisini sınırlar.

Her Azure bölgesinde belirli sayıda hata etki alanı bulunur. Etki alanlarının listesi ve içerdikleri hata etki alanlarının sayısı için [kullanılabilirlik kümeleri](../../virtual-machines/availability.md)hakkındaki belgelere bakın.

HDInsight 'ta, Kafka kümelerinin en az üç hata etki alanına sahip bir bölgede sağlanması gerekir.

## <a name="resolution"></a>Çözüm

Kümeyi oluşturmak istediğiniz bölgede yeterli hata etki alanı yoksa, üç hata etki alanı olmasa bile kümeyi sağlamaya izin vermek için ürün ekibine ulaşın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.