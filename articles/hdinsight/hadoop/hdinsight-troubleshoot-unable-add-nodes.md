---
title: Azure HDInsight kümesine düğüm eklenemiyor
description: Azure HDInsight 'ta Apache Hadoop kümesine neden düğüm ekleyemedik sorunlarını giderme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944329"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Senaryo: Azure HDInsight kümesine düğüm eklenemiyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Azure HDInsight kümesine düğüm eklenemiyor.

## <a name="cause"></a>Nedeni

Nedenler farklılık gösterebilir.

## <a name="resolution"></a>Çözüm

[Küme boyutu](../hdinsight-scaling-best-practices.md) özelliğini kullanarak, küme için gereken ek çekirdek sayısını hesaplayın. Bu, yeni çalışan düğümlerindeki toplam çekirdek sayısına bağlıdır. Ardından aşağıdaki adımlardan birini veya daha fazlasını deneyin:

* Kümenin bulunduğu konumda kullanılabilir çekirdek olup olmadığını denetleyin.

* Diğer konumlardaki kullanılabilir çekirdek sayısına göz atın. Kümenizi yeterli sayıda kullanılabilir çekirdek içeren farklı bir konumda yeniden oluşturmayı deneyin.

* Belirli bir konumun çekirdek kotasını artırmak istiyorsanız lütfen, HDInsight çekirdek kotasını artırma isteği için bir destek bileti oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]