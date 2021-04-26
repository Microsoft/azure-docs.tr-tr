---
title: HBase hbck, Azure HDInsight 'ta tutarsızlıklar döndürüyor
description: HBase hbck, Azure HDInsight 'ta tutarsızlıklar döndürüyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: cbe4231bbecdf279c637cd334336437a020188d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936987"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Senaryo: `hbase hbck` komut Azure HDInsight 'ta tutarsızlıklar döndürüyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue-region-is-not-in-hbasemeta"></a>Sorun: bölge içinde değil `hbase:meta`

Xxx bölgesi, her bölge sunucusunda ' de listelenmemiş `hbase:meta` veya dağıtılan bölge.

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

## <a name="issue-region-is-offline"></a>Sorun: bölge çevrimdışı

Bölge XXX, hiçbir RegionServer üzerinde dağıtılmadı. Bu, bölgenin içinde olduğu ancak çevrimdışıyken olduğu anlamına gelir `hbase:meta` .

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

Aşağıdakileri çalıştırarak bölgeleri çevrimiçi duruma getirin:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Sorun: bölgeler aynı başlangıç/bitiş anahtarlarına sahip

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

Bu örtüşen bölgeleri el ile birleştirin. HBase HMaster Web UI tablosu bölümüne gidin, sorunu içeren tablo bağlantısını seçin. Bu tabloya ait her bir bölgenin başlangıç anahtarı/bitiş anahtarını görürsünüz. Ardından bu çakışan bölgeleri birleştirin. HBase kabuğu 'nda bunu yapın `merge_region 'xxxxxxxx','yyyyyyy', true` . Örnek:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Bu senaryoda, RegionA ve RegionC 'yi birleştirmeniz ve regionb ile RegionD 'yi, regionb ve RegionD ile birleştirmek gerekir. xxxxxxx ve yyyyyy her bölge adının sonundaki karma dizedir. Burada iki kesintili bölgeyi birleştirmemeye dikkat edin. Birleştirme A ve C gibi her birleştirme sonrasında, HBase, RegionD üzerinde bir sıkıştırma başlatır. RegionD ile başka bir birleştirme yapmadan önce sıkıştırma işleminin bitmesini bekleyin. Sıkıştırma durumunu, HBase HMaster Kullanıcı arabirimindeki bu bölge sunucusu sayfasında bulabilirsiniz.

---

## <a name="issue-cant-load-regioninfo"></a>Sorun: yüklenemiyor `.regioninfo`

`.regioninfo`Bölge için yüklenemiyor `/hbase/data/default/tablex/regiony` .

### <a name="cause"></a>Nedeni

Bu büyük olasılıkla, RegionServer kilitleniyor veya VM yeniden başlatıldığında bölge kısmi silme nedenidir. Şu anda Azure depolama, düz bir BLOB dosya sistemidir ve bazı dosya işlemleri atomik değildir.

### <a name="resolution"></a>Çözüm

Bu kalan dosyaları ve klasörleri el ile temizleyin:

1. `hdfs dfs -ls /hbase/data/default/tablex/regiony`Hangi klasör/dosyaların hala altında olduğunu denetlemek için yürütün.

1. `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez`Tüm alt dosyaları/klasörleri silmek için Yürüt

1. `hdfs dfs -rmr /hbase/data/default/tablex/regiony`Bölge klasörünü silmek için yürütün.

---

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.