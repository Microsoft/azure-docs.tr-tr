---
title: Azure HDInsight 'ta Apache ambarı Hive görünümünden sorgu çalıştırırken oluşan özel durum
description: Azure HDInsight 'ta Apache ambarı Hive görünümü aracılığıyla Apache Hive sorguları çalıştırırken sorun giderme adımları.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933002"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache ambarı Hive görünümünden sorgu çalıştırırken oluşan özel durum

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ambarı Hive görünümünden bir Apache Hive sorgusu çalıştırırken, zaman zaman aşağıdaki hata iletisini alırsınız:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Nedeni

Ağ geçidi zaman aşımı.

Ağ geçidi zaman aşımı değeri 2 dakikadır. Ambarı Hive görünümündeki sorgular `/hive2` , ağ geçidi üzerinden uç noktaya gönderilir. Sorgu başarıyla derlendikten ve kabul edildikten sonra, HiveServer bir döndürür `queryid` . İstemciler daha sonra sorgunun durumunu yoklamaya devam edin. Bu işlem sırasında, HiveServer, 2 dakika içinde bir HTTP yanıtı döndürmezse, HDI ağ geçidi, çağırana bir 502,3 ağ geçidi zaman aşımı hatası atar. Sorgu işleme için gönderildiğinde (daha büyük olasılıkla) ve ayrıca Get durum çağrısında (daha az olası) hatalar gerçekleşebilir. Kullanıcılar bunlardan birini görebilir.

Http işleyici iş parçacığının hızlı olması gerekir: işi hazırlayın ve bir döndürür `queryid` . Bununla birlikte, birkaç nedenden dolayı tüm işleyici iş parçacıkları meşgul olabilir ve yeni sorgular ve Get durum çağrıları için zaman aşımları ortaya çıkabilir.

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP işleyici iş parçacığının sorumlulukları

İstemci HiveServer 'a bir sorgu gönderdiğinde, ön plan iş parçacığında aşağıdakiler olur:

* İsteği Ayrıştır, anlam doğrulaması yapın
* Kilit al
* Gerekirse, meta veri deposu arama
* Sorguyu derleme (DDL veya DML)
* Sorgu planı hazırlama
* Yetkilendirme gerçekleştirme (güvenli kümelerdeki tüm geçerli Ranger ilkelerini Çalıştır)

## <a name="resolution"></a>Çözüm

Durumu iyileştirecek bazı genel öneriler:

* Dış Hive meta veri deposu kullanıyorsanız, DB ölçümlerini denetleyin ve veritabanının aşırı yüklü olmadığından emin olun. Meta veri deposu veritabanı katmanını ölçeklendirmeyi düşünün.

* Paralel Ops 'un açık olduğundan emin olun (Bu, HTTP işleyici iş parçacıklarının paralel olarak çalışmasını sağlar). Değeri doğrulamak için [Apache ambarı](../hdinsight-hadoop-manage-ambari.md) 'nı başlatın ve **Hive**  >  **configs**  >  **Gelişmiş**  >  **özel Hive-site** bölümüne gidin. Değeri `hive.server2.parallel.ops.in.session` olmalıdır `true` .

* Kümenin VM SKU 'sunun yük için çok küçük olmadığından emin olun. İşi birden çok küme arasında bölmeyi göz önünde bulundurun. Daha fazla bilgi için bkz. [küme türü seçme](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Küme üzerinde Ranger yüklüyse, lütfen her bir sorgu için değerlendirilmesi gereken çok fazla sayıda Ranger ilkesi olup olmadığını kontrol edin. Yinelenen veya gerekmeyen ilkeleri arayın.

* **HiveServer2 yığın boyutu** değerini, ambarı 'ndan denetleyin. **Hive**  >  **configs**  >  **ayarları**  >  **iyileştirmesi**' na gidin. Değerin 10 GB 'tan büyük olduğundan emin olun. Performansı iyileştirmek için gereken şekilde ayarlayın.

* Hive sorgusunun iyi ayarlanmış olduğundan emin olun. Daha fazla bilgi için bkz. [Azure HDInsight 'ta Apache Hive sorgularını iyileştirme](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.