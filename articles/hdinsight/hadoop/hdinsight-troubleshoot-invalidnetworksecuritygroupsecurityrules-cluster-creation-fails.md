---
title: Invalidnetworksecuritygroupsecurityrules hatası-Azure HDInsight
description: Küme oluşturma, ErrorCode ınvalidnetworksecuritygroupsecurityrules ile başarısız oluyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 749cfbb3aeb0d82a81a8383919b9a6568419e967
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044750"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Senaryo: ınvalidnetworksecuritygroupsecurityrules-Azure HDInsight 'ta küme oluşturma başarısız oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

"Alt ağ ile yapılandırılan ağ güvenlik grubundaki güvenlik kuralları, gerekli gelen ve/veya giden bağlantılara izin vermediği" ile benzer bir açıklamayla hata kodu `InvalidNetworkSecurityGroupSecurityRules` alıyorsunuz. "

## <a name="cause"></a>Nedeni

Kümeniz için yapılandırılmış gelen [ağ güvenlik grubu](../../virtual-network/virtual-network-vnet-plan-design-arm.md) kuralları ile ilgili bir sorun olabilir.

## <a name="resolution"></a>Çözünürlük

Azure portal gidin ve kümenin dağıtıldığı alt ağla ilişkili NSG 'yi tanımla. **Gelen güvenlik kuralları** bölümünde, kuralların [burada](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)bahsedilen IP adresleri için 443 numaralı bağlantı noktasına gelen erişime izin verecek şekilde emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
