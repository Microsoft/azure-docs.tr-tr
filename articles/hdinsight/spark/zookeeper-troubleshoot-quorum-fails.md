---
title: Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor
description: Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929211"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper sunucusu Azure HDInsight 'ta çekirdek oluşturmuyor

Bu makalede, Azure HDInsight kümelerinde zookeepers ile ilgili sorunlar için sorun giderme adımları ve olası çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

* Hem kaynak yöneticileri bekleme moduna gider
* Süs 'lar her ikisi de bekleme modunda
* Spark, Hive ve Yarn işleri ya da Hive sorguları Zookeeper bağlantı hatalarından dolayı başarısız oluyor
* LLAP Daemon 'ları, güvenli Spark veya güvenli etkileşimli Hive kümelerinde başlatılamaz

## <a name="sample-log"></a>Örnek günlük

Yarn günlüklerinde aşağıdakine benzer bir hata iletisi görebilirsiniz (Bu durum, headnodes üzerinde/var/log/Hadoop-Yarn/Yarn/Yarn-yarn *. log):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>İlgili sorunlar

* Yarn, süs Yot ve Livy gibi yüksek kullanılabilirlik Hizmetleri pek çok nedenden dolayı aşağı gidebilirler.
* Zookeeper bağlantılarıyla ilgili olan günlüklerden onaylama
* Sorunun sürekli olduğundan emin olun (Bu çözümleri bir kapalı servis talebi için kullanmayın)
* Zookeeper bağlantı sorunları nedeniyle işler geçici olarak başarısız olabilir

## <a name="common-causes-for-zookeeper-failure"></a>Zookeeper hatasının yaygın nedenleri

* Zookeeper sunucularında yüksek CPU kullanımı
  * Ambarı Kullanıcı arabiriminde, Zookeeper sunucularında yaklaşık %100 sürekli CPU kullanımı görürseniz, bu süre içinde açılan Zookeeper oturumlarının süresi sona erer ve zaman aşımına uğrar
* Zookeeper istemcileri sıklıkla zaman aşımlarını bildiriyor
  * Kaynak Yöneticisi, süs kodu ve diğerleri için günlüklerde sık görülen istemci bağlantı zaman aşımlarını görürsünüz
  * Bu, çekirdek kaybına, sık yük devretmesine ve diğer sorunlara yol açabilir

## <a name="check-for-zookeeper-status"></a>Zookeeper durumunu denetle

* /Etc/hosts dosyasından veya ambarı kullanıcı arabiriminden Zookeeper sunucularını bulma
* Aşağıdaki komutu çalıştırın
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (veya 2182)  
  * 2181 numaralı bağlantı noktası Apache Zookeeper örneğidir
  * 2182 numaralı bağlantı noktası, HDInsight Zookeeper tarafından kullanılır (yerel olarak HA olmayan hizmetler için HA sağlamak üzere)
  * Komutta hiçbir çıkış gösterilmediğinden, Zookeeper sunucularının çalışmadığı anlamına gelir.
  * Sunucular çalışıyorsa, sonuç istemci bağlantılarının ve diğer istatistiklerin bir listesini içerir

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>Her saat için CPU yükü üst düzeye

* Zookeeper sunucusunda oturum açın ve/etc/crontab denetleyin
* Şu anda çalışan saatlik iş varsa, farklı Zookeeper sunucularındaki başlangıç saatini rastgele yapın.
  
## <a name="purging-old-snapshots"></a>Eski anlık görüntüleri Temizleme

* Zookeepers, eski anlık görüntüleri otomatik olarak temizlemek için yapılandırılır
* Varsayılan olarak, son 30 anlık görüntü korunur
* Korunan anlık görüntü sayısı, yapılandırma anahtarı tarafından denetlenir `autopurge.snapRetainCount` . Bu özellik aşağıdaki dosyalarda bulunabilir:
  * `/etc/zookeeper/conf/zoo.cfg` Hadoop Zookeeper için
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` HDInsight için Zookeeper
* `autopurge.snapRetainCount`3 değerine ayarlayın ve Zookeeper sunucularını yeniden başlatın
  * Hadoop Zookeeper yapılandırması güncelleştirilebilen ve hizmet, ambarı aracılığıyla yeniden başlatılabilir
  * HDInsight Zookeeper 'i el ile durdurun ve yeniden başlatın
    * `sudo lsof -i :2182` Bu işlem KIMLIĞINI sonlandırmak için
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Anlık görüntüleri el ile temizleme-anlık görüntülerin el ile silinmesi veri kaybına neden olabilir

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>Zookeeper sunucu günlüğündeki CancelledKeyException, anlık görüntü Temizleme gerektirmez

* Bu özel durum Zookeeper sunucularında (/var/log/Zookeeper/Zookeeper-Zookeeper-* veya/var/log/hdinsight-Zookeeper/Zookeeper * dosyaları) görünür
* Bu özel durum genellikle istemcinin artık etkin olmadığı ve sunucunun ileti gönderemediği anlamına gelir
* Bu özel durum ayrıca Zookeeper istemcisinin oturumu erken sonlandırdığını gösterir
* Bu belgede özetlenen diğer belirtileri arayın

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.
- [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.
- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.