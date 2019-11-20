---
title: BindException-adres Azure HDInsight 'ta zaten kullanılıyor
description: BindException-adres Azure HDInsight 'ta zaten kullanılıyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 52e91b6b5cacef8ed7d0d9b578a8dd4f21e1a271
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091692"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Senaryo: BindException-adres Azure HDInsight 'ta zaten kullanılıyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache HBase bölge sunucusu üzerinde yeniden başlatma işlemi tamamlanamadı. Bölge sunucusunun başlayacağı `/var/log/hbase` çalışan düğümlerinde bulunandizininde,aşağıdakinebenzerbirhatailetisigörebilirsiniz:`region-server.log`

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Nedeni

Ağır iş yükü etkinliği sırasında Apache HBase bölge sunucuları yeniden başlatılıyor. Bir Kullanıcı, HBase bölge sunucusunda Apache ambarı kullanıcı arabiriminden yeniden başlatma işlemini başlattığında, arka planda bu durum aşağıda verilmiştir:

1. Ambarı Aracısı bölge sunucusuna bir durdurma isteği gönderir.

1. Ambarı Aracısı bölge sunucusunun düzgün şekilde kapanması için 30 saniye bekler

1. Uygulamanız bölge sunucusuna bağlanmaya devam ederse, sunucu hemen kapatılmaz. 30 saniyelik zaman aşımı, kapatmadan önce sona erer.

1. 30 saniye sonra, ambarı Aracısı bölge sunucusuna bir zorla-KILL (`kill -9`) komutu gönderir.

1. Bu ani kapatmayla kapatılmış olsa da, bölge sunucusu işlemi sonlandırısa da işlemle ilişkili bağlantı noktası yayımlanmayabilir, sonunda buna yol açar `AddressBindException`.

## <a name="resolution"></a>Çözüm

Yeniden başlatma başlatmadan önce HBase bölge sunucularındaki yükü azaltın. Ayrıca, ilk olarak tüm tabloları temizlemek iyi bir fikirdir. Tabloları temizleme hakkında bir başvuru için bkz [. HDInsight HBase: Tabloları](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)reçeteye göre Apache HBase kümesi yeniden başlatma süresini geliştirme.

Alternatif olarak, aşağıdaki komutları kullanarak çalışan düğümlerinde bölge sunucularını el ile yeniden başlatmayı deneyin:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
