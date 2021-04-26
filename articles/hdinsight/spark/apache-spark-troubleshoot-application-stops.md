---
title: Azure HDInsight 'ta 24 gün sonra akış uygulaması Apache Spark durduruluyor
description: Bir Apache Spark akış uygulaması 24 gün boyunca yürütüldükten sonra duraklar ve günlük dosyalarında hata yoktur.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929457"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta 24 gün boyunca yürütmeden sonra akış uygulaması duraklarının Apache Spark

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Bir Apache Spark akış uygulaması 24 gün boyunca yürütüldükten sonra duraklar ve günlük dosyalarında hata yoktur.

## <a name="cause"></a>Nedeni

`livy.server.session.timeout`Değer, Apache Livy 'ın bir oturumun tamamlanmasını ne kadar bekleyeceğini denetler. Oturumun uzunluğu `session.timeout` değere ulaştığında, Livy oturumu ve uygulama otomatik olarak sonlandırıldı.

## <a name="resolution"></a>Çözüm

Uzun süre çalışan işler için, `livy.server.session.timeout` ambarı Kullanıcı arabirimini kullanma değerini artırın. URL 'YI kullanarak, ambarı kullanıcı arabiriminden Livy yapılandırmasına erişebilirsiniz `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

`<yourclustername>`, Portalda gösterilen HDInsight kümenizin adıyla değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]