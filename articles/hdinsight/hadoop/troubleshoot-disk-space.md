---
title: Azure HDInsight 'ta disk alanını yönetme
description: Azure HDInsight kümeleriyle etkileşim kurarken disk alanı sorunlarını yönetmeye yönelik sorun giderme adımları ve olası çözümleri.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944815"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Azure HDInsight 'ta disk alanını yönetme

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="hive-log-configurations"></a>Hive günlük yapılandırması

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.

1. **Hive**  >  **configs**  >  **Gelişmiş**  >  **Gelişmiş Hive-Log4J**' a gidin. Aşağıdaki ayarları gözden geçirin:

    * `hive.root.logger=DEBUG,RFA`. Bu varsayılan değerdir, daha az günlük girişi yazdırmak için [günlük düzeyini](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) olarak değiştirin `INFO` .

    * `log4jhive.log.maxfilesize=1024MB`. Bu varsayılan değerdir, istediğiniz şekilde değiştirin.

    * `log4jhive.log.maxbackupindex=10`. Bu varsayılan değerdir, istediğiniz şekilde değiştirin. Parametresi atlanırsa, oluşturulan günlük dosyaları sonsuz olur.

## <a name="yarn-log-configurations"></a>Yarn günlük yapılandırması

Aşağıdaki konfigürasyonları gözden geçirin:

* Apache ambarı

    1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.

    1. **Hive**  >  **configs**  >  **Gelişmiş**  >  **Kaynak Yöneticisi** gidin. **Günlük toplamayı etkinleştir** ' in işaretli olduğundan emin olun. Devre dışı bırakılırsa, ad düğümleri günlükleri yerel olarak tutar ve uygulama tamamlandığında veya sonlandırmada onları uzak depoda toplamaz.

* Küme boyutunun iş yükü için uygun olduğundan emin olun. İş yükü yakın zamanda değiştirilmiş olabilir veya küme yeniden boyutlandırılmış olabilir. Daha yüksek bir iş yüküyle eşleşecek şekilde kümeyi [ölçeklendirin](../hdinsight-scaling-best-practices.md) .

* `/mnt/resource` Yalnız bırakılmış dosyalarla doldurulmuş olabilir (Resource Manager yeniden başlatma durumunda olduğu gibi). Gerekirse, ve el ile `/mnt/resource/hadoop/yarn/log` temizleyin `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.