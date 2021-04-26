---
title: Azure HDInsight 'ta Apache HBase Master başlatılamadı
description: Apache HBase Master (HMaster) Azure HDInsight 'ta başlatılamıyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936894"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) Azure HDInsight 'ta başlatılamıyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="scenario-atomic-renaming-failure"></a>Senaryo: atomik yeniden adlandırma hatası

### <a name="issue"></a>Sorun

Başlangıç işlemi sırasında beklenmeyen dosyalar tanımlandı.

### <a name="cause"></a>Nedeni

Başlangıç işlemi sırasında, HMaster verileri sıfırdan (. tmp) klasörden veri klasörüne taşıma gibi birçok başlatma adımı gerçekleştirir. HMaster Ayrıca, yanıt vermeyen bölge sunucuları olup olmadığını görmek için yazma günlükleri (WAL) klasörüne de bakar.

HMaster, WAL klasörlerinde temel bir liste komutu yapar. Her zaman, HMaster bu klasörlerin hiçbirinde beklenmeyen bir dosya görür ve bir özel durum oluşturur ve başlamaz.

### <a name="resolution"></a>Çözüm

Çağrı yığınını denetleyin ve soruna neden olabilecek klasörü belirlemeyi deneyin (örneğin, WAL klasörü veya. tmp klasörü olabilir). Ardından, Cloud Explorer 'da veya,,, sorun dosyasını bulmaya çalışın. Genellikle bu bir `*-renamePending.json` dosyadır. ( `*-renamePending.json` Dosya, IDB sürücüsünde atomik yeniden adlandırma işlemini uygulamak için kullanılan bir günlük dosyasıdır. Bu uygulamadaki hatalar nedeniyle, bu dosyalar işlem kilitlenmelerinden sonra bırakılabilir ve bu şekilde devam eder.) Zorla-bu dosyayı bulut Gezgini ' nde veya bir.

Bazen, bu konumda olduğu gibi bir geçici dosya da olabilir `$$$.$$$` . `ls`Bu dosyayı görmek için, bu dosyayı kullanmanız gerekir; dosyayı Cloud Explorer 'da göremezsiniz. Bu dosyayı silmek için, `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` .

Bu komutları çalıştırdıktan sonra, HMaster hemen başlamalıdır.

---

## <a name="scenario-no-server-address-listed"></a>Senaryo: sunucu adresi listelenmedi

### <a name="issue"></a>Sorun

Tablonun çevrimiçi olmadığını belirten bir ileti görebilirsiniz `hbase: meta` . Çalıştırmak `hbck` `hbase: meta table replicaId 0 is not found on any region.` , hmaster günlüklerinde şunu rapor edebilir: iletisini görebilirsiniz `No server address listed in hbase: meta for region hbase: backup <region name>` .  

### <a name="cause"></a>Nedeni

HMaster, HBase yeniden başlatıldıktan sonra başlatılamadı.

### <a name="resolution"></a>Çözüm

1. HBase kabuğunda, aşağıdaki komutları girin (gerçek değerleri uygun olarak değiştirin):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Girişi silin `hbase: namespace` . Bu giriş, tablo tarandığında bildirilen aynı hata olabilir `hbase: namespace` .

1. Çalışan durumunda HBase 'i açmak için, etkin HMaster 'ı ambarı kullanıcı arabiriminden yeniden başlatın.

1. HBase kabuğunda tüm çevrimdışı tabloları görüntülemek için aşağıdaki komutu çalıştırın:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Senaryo: Java. IO. IOException: zaman aşımına uğradı

### <a name="issue"></a>Sorun

HMaster, şuna benzer önemli özel durum ile zaman aşımına uğruyor: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` .

### <a name="cause"></a>Nedeni

Bu sorunla, HMaster hizmetlerinizi yeniden başlattığınızda temizlenen çok sayıda tablonuz ve bölgesi varsa karşılaşabilirsiniz. Zaman aşımı, HMaster ile bilinen bir sorundur. Genel küme başlangıç görevleri uzun zaman alabilir. Ad alanı tablosu henüz atanmamışsa HMaster kapanır. Uzun başlangıç görevleri, büyük miktarda temizlenen verilerin mevcut olduğu ve beş dakikalık bir zaman aşımı yeterli olmadığı durumlarda gerçekleşir.

### <a name="resolution"></a>Çözüm

1. Apache ambarı kullanıcı arabiriminden, **HBase**  >  **configs**' a gidin. Özel `hbase-site.xml` dosyada aşağıdaki ayarı ekleyin:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Gerekli hizmetleri (HMaster ve belki de diğer HBase hizmetlerini) yeniden başlatın.

---

## <a name="scenario-frequent-region-server-restarts"></a>Senaryo: sık kullanılan bölge sunucusu yeniden başlatmaları

### <a name="issue"></a>Sorun

Düğümler düzenli aralıklarla yeniden başlatılır. Bölge sunucusu günlüklerinden şuna benzer girdilerle karşılaşabilirsiniz:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Nedeni

Uzun `regionserver` JVM GC duraklatma. Duraklamanın `regionserver` yanıt vermemesine ve ZK oturum zaman aşımı 40s Içinde HMaster 'a sinyal gönderememesine neden olur. HMaster ölü olduğunu düşünmeyecek ve `regionserver` `regionserver` yeniden başlatılacak.

### <a name="resolution"></a>Çözüm

Yalnızca ayarı değil, Zookeeper oturum zaman aşımını değiştirin, `hbase-site` `zookeeper.session.timeout` ancak aynı zamanda Zookeeper `zoo.cfg` ayarının `maxSessionTimeout` değiştirilmesi gerekir.

1. Ambarı Kullanıcı arabirimine erişin, **HBase-> configs-> ayarlar**' a gidin, zaman aşımları bölümünde Zookeeper oturum zaman aşımı değerini değiştirin.

1. Ambarı Kullanıcı arabirimine erişin, **Zookeeper-> configs sayfasına gidin, özel >** `zoo.cfg` , aşağıdaki ayarı ekleyin/değiştirin. Değerin HBase ile aynı olduğundan emin olun `zookeeper.session.timeout` .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Gerekli hizmetleri yeniden başlatın.

---

## <a name="scenario-log-splitting-failure"></a>Senaryo: günlük bölme hatası

### <a name="issue"></a>Sorun

Hmaster, bir HBase kümesine dönelemedi.

### <a name="cause"></a>Nedeni

İkincil depolama hesabı için yanlış yapılandırılmış ve HBase ayarları.

### <a name="resolution"></a>Çözüm

HBase. rootdir: wasb://@.blob.core.windows.net/hbase ' i ayarlayın ve ambarı 'nda hizmetleri yeniden başlatın.

---

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.