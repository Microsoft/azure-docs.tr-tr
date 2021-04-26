---
title: HDInsight 'ta WebHCat hatalarını anlama ve çözme-Azure
description: HDInsight 'ta WebHCat tarafından döndürülen yaygın hataları ve bunların nasıl çözümleneceğini öğrenin.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ce7a21a2b5401c68c6b58bc934fb20b0225ba8ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945461"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>HDInsight’ta WebHCat’ten alınan hataları anlama ve düzeltme

HDInsight ile WebHCat kullanırken alınan hatalar ve bunları çözme hakkında bilgi edinin. WebHCat, Azure PowerShell gibi istemci tarafı araçları ve Visual Studio için Data Lake araçları tarafından dahili olarak kullanılır.

## <a name="what-is-webhcat"></a>WebHCat nedir?

[Webhcat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) , Apache Hadoop Için bir [hcatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tablo ve depolama yönetimi katmanı REST API. WebHCat, HDInsight kümelerinde varsayılan olarak etkindir ve kümede oturum açmadan işleri göndermek, iş durumunu almak vb. gibi çeşitli araçlar tarafından kullanılır.

## <a name="modifying-configuration"></a>Yapılandırmayı değiştirme

Yapılandırılan en büyük değer aşıldığından bu belgede listelenen hataların birkaçı meydana gelir. Çözüm adımı bir değeri değiştiremeyeceğiniz konusunda bahsetme yaparken, değeri değiştirmek için Apache ambarı 'nı (Web veya REST API) kullanın. Daha fazla bilgi için bkz. [Apache ambarı kullanarak HDInsight 'ı yönetme](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Varsayılan yapılandırma

Aşağıdaki varsayılan değerler aşılırsa, WebHCat performansını düşürebilir veya hatalara neden olabilir:

| Ayar | Ne yapar? | Varsayılan değer |
| --- | --- | --- |
| [Yarn. Scheduler. kapasite. maksimum-uygulamalar][maximum-applications] |Aynı anda etkin olabilen en fazla iş sayısı (bekliyor veya çalışıyor) |10,000 |
| [templeton.exec. Max-procs][max-procs] |Eşzamanlı olarak sunulabilecek en fazla istek sayısı |20 |
| [MapReduce. jobhistory. max-age-MS][max-age-ms] |İş geçmişinin tutulacağı gün sayısı |7 gün |

## <a name="too-many-requests"></a>Çok fazla istek

**Http durum kodu**: 429

| Nedeni | Çözüm |
| --- | --- |
| Web Hcat tarafından dakikada sunulan en fazla eşzamanlı istek sayısını aştık (varsayılan 20) |En fazla eşzamanlı istek sayısından daha fazla bilgi göndermemesini sağlamak için iş yükünüzü azaltın veya değiştirerek eşzamanlı istek sınırını artırın `templeton.exec.max-procs` . Daha fazla bilgi için bkz. [yapılandırmayı değiştirme](#modifying-configuration) |

## <a name="server-unavailable"></a>Sunucu kullanılamıyor

**Http durum kodu**: 503

| Nedeni | Çözüm |
| --- | --- |
| Bu durum kodu genellikle küme için birincil ve ikincil HeadNode arasında yük devretme sırasında oluşur |İki dakika bekleyip işlemi yeniden deneyin |

## <a name="bad-request-content-could-not-find-job"></a>Hatalı istek Içeriği: iş bulunamadı

**Http durum kodu**: 400

| Nedeni | Çözüm |
| --- | --- |
| İş ayrıntıları, iş geçmişi temizleyici tarafından temizlendi |İş geçmişi için varsayılan saklama süresi 7 gündür. Varsayılan saklama süresi değiştirilerek değiştirilebilir `mapreduce.jobhistory.max-age-ms` . Daha fazla bilgi için bkz. [yapılandırmayı değiştirme](#modifying-configuration) |
| Yük devretme nedeniyle iş sonlandırıldı |İş gönderimini iki dakikaya kadar yeniden deneyin |
| Geçersiz bir iş KIMLIĞI kullanıldı |İş KIMLIĞININ doğru olup olmadığını denetle |

## <a name="bad-gateway"></a>Hatalı ağ geçidi

**Http durum kodu**: 502

| Nedeni | Çözüm |
| --- | --- |
| Web Hcat işleminde iç çöp toplama işlemi yapılıyor |Çöp toplamanın bitmesini bekleyin veya WebHCat hizmetini yeniden başlatın |
| ResourceManager hizmetinden yanıt beklerken zaman aşımı. Etkin uygulama sayısı yapılandırılmış en yüksek değeri geçtiğinde bu hata oluşabilir (varsayılan 10.000) |Çalışmakta olan işlerin tamamlanmasını bekleyin veya değiştirerek eşzamanlı iş sınırını artırın `yarn.scheduler.capacity.maximum-applications` . Daha fazla bilgi için [yapılandırma değiştirme](#modifying-configuration) bölümüne bakın. |
| Olarak ayarlandığında, [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) çağrısı aracılığıyla tüm işleri alma girişimi `Fields``*` |*Tüm* iş ayrıntılarını alma. Bunun yerine `jobid` yalnızca belirli Iş kimliğinden daha büyük işlerin ayrıntılarını almak için kullanın. Veya kullanma `Fields` |
| WebHCat hizmeti, yayın düğümü yük devretmesi sırasında çalışmıyor |İki dakika bekleyip işlemi yeniden deneyin |
| WebHCat aracılığıyla en çok 500 bekleyen iş gönderildi |Daha fazla iş göndermeden önce Şu anda bekleyen işlerin tamamlanmasını bekleyin |

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml