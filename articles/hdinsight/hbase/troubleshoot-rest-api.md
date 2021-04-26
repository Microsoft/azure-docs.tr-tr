---
title: Azure HDInsight 'ta Apache HBase 'i sorgulamak için REST API
description: Bu makalede, Azure HDInsight kümelerinde Apache HBase bileşenleriyle etkileşim kurarken sorun giderme adımları açıklanmaktadır.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942871"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase 'i sorgulamak için REST API

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Bir çalışma zamanı hatası içindeki varsayılan sonuçlardan başka bir ad alanı altında bir tabloyu sorgulamak için Apache HBase REST arabirimini kullanma (HTTP durum 500).

## <a name="cause"></a>Nedeni

HBase REST API yalnızca varsayılan ad alanı kullanılırken desteklenir. Bu, HBase ad alanlarını kullanmanın veya HDInsight üzerinde REST sunucusu olan sütun aileleri içeren sütunları alır. Bunun nedeni, HDInsight Gateway ile ilgili bir güvenlik sorunudur. Bir ad alanı olan ve sütun aileleri aracılığıyla sütunlara erişen bir tablo oluşturmak için API kullanırken, `:` IIS ağ geçidi modülündeki bir güvenlik sorunu olarak kabul edilen karakteri belirtmeniz gerekir.

## <a name="mitigation"></a>Risk azaltma

Uygulamanızı HDInsight kümesiyle aynı Azure VNet 'te bulunan bir VM 'ye dağıtarak ağ geçidi/REST sunucusunu atlayın. Daha sonra doğrudan RPC aracılığıyla (REST sunucusunu tamamen atlayarak) veya HDInsight ağ geçitlerini atlayarak çalışan düğümlerinde çalışan tek tek REST sunucularının üzerinden iletişim kurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.