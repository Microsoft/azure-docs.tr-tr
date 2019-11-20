---
title: Azure HDInsight 'ta küme düğümünün disk alanı tükeniyor
description: Azure HDInsight 'ta küme düğümü disk alanı sorunlarını giderme Apache Hadoop.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a9d82e0465e555d4959e549e04565399d423c1ee
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087358"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta küme düğümünün disk alanı tükeniyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Bir iş şuna benzer bir hata iletisiyle başarısız olabilir:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Ya da şuna benzer Apache ambarı uyarısı alabilirsiniz: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Nedeni

Apache YARN uygulama önbelleği, kullanılabilir tüm disk alanını tüketilebilir. Spark uygulamanız muhtemelen yeterince çalışıyor.

## <a name="resolution"></a>Çözüm

1. Hangi düğümün disk alanının tükençalıştığını anlamak için, ambarı Kullanıcı arabirimini kullanın.

1. Araba düğümündeki hangi klasörün disk alanının çoğuna katkıda bulunduğunu belirleme. Önce bir düğüme SSH, sonra tüm takar `df` disk kullanımını listelemek için çalıştırın. Genellikle, `/mnt` OSS tarafından kullanılan bir geçici disktir. Bir klasöre girip, ardından bir klasör altında özetlenen `sudo du -hs` dosya boyutlarını göstermek için yazabilirsiniz. Benzer bir klasör görürseniz `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, bu, uygulamanın hala çalıştığı anlamına gelir. Bunun nedeni, RDD kalıcılığı veya ara karıştırma dosyalarından kaynaklanabilir.

1. Sorunu azaltmak için, uygulamayı sonlandırın, bu uygulama tarafından kullanılan disk alanını serbest bırakacaktır.

1. Sorunu en sonunda çözmek için uygulamanızı iyileştirin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* Azure Community [@AzureSupport](https://twitter.com/azuresupport) 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
