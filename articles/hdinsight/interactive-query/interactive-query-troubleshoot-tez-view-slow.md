---
title: Apache ambarı tez görünümü, Azure HDInsight 'ta yavaş yükleniyor
description: Apache ambarı tez görünümü yavaş yükleme veya Azure HDInsight 'ta hiç yüklenmeyebilir
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930767"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache ambarı tez görünümü yavaş yükleniyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ambarı tez görünümü yavaş çalışabilir veya hiç yüklenmeyebilir. Ambarı tez görünümü yüklenirken, yük düğümlerinde işlemlerin yanıt vermemesine neden olabilir.

## <a name="cause"></a>Nedeni

Yarn ATS API 'Lerine erişmek, küme üzerinde çok sayıda Hive işi çalıştırılan durumlarda 2017 eki olmadan önce oluşturulan kümeler üzerinde düşük performansa sahip olabilir.

## <a name="resolution"></a>Çözüm

Bu, Ekim 2017 ' de düzeltilen bir sorundur. Kümenizi yeniden oluşturmak bu sorunu tekrar çalıştırmayacak.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]