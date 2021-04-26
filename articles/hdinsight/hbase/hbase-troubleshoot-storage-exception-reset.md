---
title: Azure HDInsight 'ta bağlantı sıfırlama sonrasında depolama özel durumu
description: Azure HDInsight 'ta bağlantı sıfırlama sonrasında depolama özel durumu
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936747"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta bağlantı sıfırlandıktan sonra depolama özel durumu

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Yeni Apache HBase tablosu oluşturulamıyor.

## <a name="cause"></a>Nedeni

Bir tablo kesme işlemi sırasında, bir depolama bağlantı sorunu oluştu. Tablo girdisi HBase meta veri tablosu 'nda silindi. Yalnızca bir blob dosyası silindi.

`/hbase/data/default/ThatTable`Depolama alanında oturmuş bir klasör blobu yoktu. Bu sürücü, yukarıdaki blob dosyasının varlığını buldu ve `/hbase/data/default/ThatTable` üst klasörlerin var olduğunu varsaydığı için adlı blob oluşturmaya izin vermiyor, bu nedenle tablo oluşturma başarısız olur.

## <a name="resolution"></a>Çözüm

1. Apache ambarı kullanıcı arabiriminden, etkin HMaster ' ı yeniden başlatın. Bu, iki bekleme modundan birinin etkin hale gelmesine izin verir ve yeni etkin HMaster, meta veri tablosu bilgilerini yeniden yükler. Bu nedenle, `already-deleted` tabloyu HMaster Kullanıcı arabiriminde görmezsiniz.

1. Artık blob dosyasını Cloud Explorer gibi kullanıcı arabirimi araçlarından veya gibi çalışan bir komutla bulabilirsiniz `hdfs dfs -ls /xxxxxx/yyyyy` . `hdfs dfs -rmr /xxxxx/yyyy`Bu blobu silmek için ' i çalıştırın. Örneğin, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Artık HBase 'de aynı ada sahip yeni bir tablo oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.