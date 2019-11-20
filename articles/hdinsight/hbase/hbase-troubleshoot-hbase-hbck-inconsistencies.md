---
title: HBase hbck, Azure HDInsight 'ta tutarsızlıklar döndürüyor
description: HBase hbck, Azure HDInsight 'ta tutarsızlıklar döndürüyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 5fc338e83c172e26d621ef89dcfb047d01d510fa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091681"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Senaryo: `hbase hbck` komut Azure HDInsight 'ta tutarsızlıklar döndürüyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue-region-is-not-in-hbasemeta"></a>Sorun: Bölge içinde değil`hbase:meta`

Xxx bölgesi, her bölge sunucusunda ' de `hbase:meta` listelenmemiş veya dağıtılan bölge.

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

1. Şunu çalıştırarak meta tabloyu düzeltir:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Aşağıdakileri çalıştırarak RegionServers 'a bölge atayın:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Sorun: Bölge çevrimdışı

Bölge XXX, hiçbir RegionServer üzerinde dağıtılmadı. Bu, bölgenin içinde `hbase:meta`olduğu ancak çevrimdışıyken olduğu anlamına gelir.

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

Aşağıdakileri çalıştırarak bölgeleri çevrimiçi duruma getirin:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Sorun: Bölgeler aynı başlangıç/bitiş anahtarlarına sahiptir

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

Bu örtüşen bölgeleri el ile birleştirin. HBase HMaster Web UI tablosu bölümüne gidin, sorunu içeren tablo bağlantısını seçin. Bu tabloya ait her bir bölgenin başlangıç anahtarı/bitiş anahtarını görürsünüz. Ardından bu çakışan bölgeleri birleştirin. HBase kabuğu 'nda bunu yapın `merge_region 'xxxxxxxx','yyyyyyy', true`. Örneğin:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Bu senaryoda, RegionA ve RegionC 'yi birleştirmeniz ve regionb ile RegionD 'yi, regionb ve RegionD ile birleştirmek gerekir. xxxxxxx ve yyyyyy her bölge adının sonundaki karma dizedir. Burada iki kesintili bölgeyi birleştirmemeye dikkat edin. Birleştirme A ve C gibi her birleştirme sonrasında, HBase, RegionD üzerinde bir sıkıştırma başlatır. RegionD ile başka bir birleştirme yapmadan önce sıkıştırma işleminin bitmesini bekleyin. Sıkıştırma durumunu, HBase HMaster Kullanıcı arabirimindeki bu bölge sunucusu sayfasında bulabilirsiniz.

---

## <a name="issue-cant-load-regioninfo"></a>Sorun: Yüklenemiyor`.regioninfo`

`.regioninfo` Bölge`/hbase/data/default/tablex/regiony`için yüklenemiyor.

### <a name="cause"></a>Nedeni

Bu büyük olasılıkla, RegionServer kilitleniyor veya VM yeniden başlatıldığında bölge kısmi silme nedenidir. Şu anda Azure depolama, düz bir BLOB dosya sistemidir ve bazı dosya işlemleri atomik değildir.

### <a name="resolution"></a>Çözüm

Bu kalan dosyaları ve klasörleri el ile temizleyin:

1. Hangi `hdfs dfs -ls /hbase/data/default/tablex/regiony` klasör/dosyaların hala altında olduğunu denetlemek için yürütün.

1. Tüm `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` alt dosyaları/klasörleri silmek için Yürüt

1. Bölge `hdfs dfs -rmr /hbase/data/default/tablex/regiony` klasörünü silmek için yürütün.

---

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
