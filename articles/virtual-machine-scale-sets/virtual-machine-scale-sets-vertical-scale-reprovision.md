---
title: Azure sanal makine ölçek kümelerini dikey ölçeklendirme | Microsoft Docs
description: Azure Otomasyonu ile uyarıları izlemeye yanıt olarak bir sanal makineyi dikey olarak ölçeklendirme
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: 87d2b19f6143f567782778e35c8511f233d8b0e8
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958135"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri ile dikey otomatik ölçeklendirme

Bu makalede, Azure [sanal makine ölçek kümelerinin](https://azure.microsoft.com/services/virtual-machine-scale-sets/) yeniden sağlama ile veya yeniden sağlamaya gerek kalmadan dikey olarak ölçeklendirilmesi açıklanmaktadır. 

*Ölçek* genişletme ve *ölçeği azaltma*olarak da bilinen dikey ölçekleme, bir iş yüküne yanıt olarak sanal makine (VM) boyutlarının artırılması veya azaltılması anlamına gelir. Bu davranışı, *Ölçek* genişletme ve *ölçekleme*olarak da adlandırılan [yatay ölçeklendirmeyle](virtual-machine-scale-sets-autoscale-overview.md)karşılaştırın, burada VM sayısı iş yüküne göre değiştirilir.

Yeniden sağlama, var olan bir sanal makineyi kaldırıp yeni bir VM 'yi değiştirme anlamına gelir. Bir sanal makine ölçek kümesindeki VM 'lerin boyutunu artırdığınızda veya azaltırsanız, bazı durumlarda mevcut VM 'Leri yeniden boyutlandırmak ve verilerinizi sürdürmek istediğinizde, yeni boyuttaki yeni VM 'Leri dağıtmanız gerekir. Bu belge her iki durumu da içerir.

Dikey ölçeklendirme şu durumlarda yararlı olabilir:

* Sanal makineler üzerinde oluşturulmuş bir hizmet (örneğin, hafta sonları) kullanılıyor. VM boyutunu azaltmak, aylık maliyetleri azaltabilir.
* Ek VM 'Ler oluşturmadan daha büyük talebe sahip VM boyutunu sanal makineye artırma.

Sanal makine ölçek kümesinden ölçüm tabanlı uyarılara dayalı olarak tetiklenecek dikey ölçeklendirmeyi ayarlayabilirsiniz. Uyarı etkinleştirildiğinde, ölçek kurulumunu yukarı veya aşağı ölçeklendirebilen bir runbook 'u tetikleyen bir Web kancasını tetikler. Dikey ölçeklendirme aşağıdaki adımları izleyerek yapılandırılabilir:

1. Farklı Çalıştır özelliği ile bir Azure Otomasyonu hesabı oluşturun.
2. Sanal Makine Ölçek Kümeleri için Azure Otomasyonu dikey ölçek runbook 'larını aboneliğinize aktarın.
3. Runbook 'na bir Web kancası ekleyin.
4. Web kancası bildirimi kullanarak sanal makine ölçek kümesine bir uyarı ekleyin.

> [!NOTE]
> İlk sanal makinenin boyutu nedeniyle, ölçeklendirilebilen Boyutlar, geçerli sanal makinedeki kümedeki diğer boyutların kullanılabilirliğine bağlı olarak, ' de sınırlı olabilir. Bu makalede kullanılan yayımlanan Otomasyon Runbook 'larında bu durumu ele aldık ve yalnızca aşağıdaki VM boyut çiftleri içinde ölçeklendirdik. Bu, Standard_D1v2 bir sanal makinenin aniden Standard_G5 'e kadar ölçeklendirilmesi veya Basic_A0 olarak ölçeklendirilmesi anlamına gelir. Ayrıca, kısıtlanmış sanal makine boyutları ölçeği artırma/azaltma desteklenmez. Aşağıdaki boyut çiftleri arasında ölçeklendirmeyi seçebilirsiniz:
> 
> | VM boyutları ölçeklendirme çifti |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Farklı Çalıştır özelliği ile bir Azure Otomasyonu hesabı oluşturma
Yapmanız gereken ilk şey, sanal makine ölçek kümesi örneklerini ölçeklendirmek için kullanılan runbook 'ları barındıran bir Azure Otomasyonu hesabı oluşturmaktır. Son [Azure Otomasyonu](https://azure.microsoft.com/services/automation/) , bir kullanıcı adına runbook 'ları otomatik olarak çalıştırmak Için hizmet sorumlusu ayarlamayı sağlayan "farklı çalıştır hesabı" özelliğini kullanıma sunmuştur. Daha fazla bilgi için bkz.:

* [Azure farklı çalıştır hesabıyla runbook 'Ların kimliğini doğrulama](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Otomasyonu dikey ölçek runbook 'larını aboneliğinize aktarma

Sanal makine ölçek kümelerinizi dikey olarak ölçeklendirmek için gereken runbook 'lar Azure Otomasyonu runbook galerisinde zaten yayımlandı. Bu makaledeki adımları izleyerek aboneliğinize içeri aktarabilirsiniz:

* [Azure Otomasyonu için Runbook ve modül galerileri](../automation/automation-runbook-gallery.md)

Runbook 'Lar menüsünden galeriye gözatamıyorum seçeneğini belirleyin:

![İçeri aktarılacak runbook 'lar][runbooks]

İçeri aktarılması gereken runbook 'lar gösterilir. Yeniden sağlamaya dayalı veya olmadan dikey ölçeklendirmeyi isteyip istemediğinizi belirlemek için Runbook 'u seçin:

![Runbook 'lar Galerisi][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Runbook 'na bir Web kancası ekleyin

Runbook 'ları içeri aktardıktan sonra, bir sanal makine ölçek kümesinden bir uyarı tarafından tetiklenmesi için Runbook 'a bir Web kancası ekleyin. Runbook 'larınız için Web kancası oluşturma ayrıntıları şu makalede açıklanmıştır:

* [Azure Otomasyonu web kancaları](../automation/automation-webhooks.md)

> [!NOTE]
> Web kancası iletişimini kapatmadan önce Web kancası URI 'sini kopyalamadığınızdan emin olun. bu adres sonraki bölümde gerekecektir.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Sanal makine ölçek kümesine bir uyarı ekleyin

Aşağıda, bir sanal makine ölçek kümesine nasıl uyarı ekleneceğini gösteren bir PowerShell betiği verilmiştir. Uyarının tetikleneceği ölçüm adını almak için aşağıdaki makaleye bakın: [Azure izleyici otomatik ölçeklendirme ortak ölçümleri](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Dikey ölçeklendirmeyi ve ayrıca ilişkili hizmet kesintisini tetiklemeyi önlemek için uyarı için makul bir zaman penceresi yapılandırmanız önerilir. En az 20-30 dakika veya daha fazla bir pencere düşünün. Herhangi bir kesinti olmaması gerekiyorsa yatay ölçeklendirmeyi göz önünde bulundurun.
> 
> 

Uyarı oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Izleyici PowerShell hızlı başlangıç örnekleri](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Izleyici platformlar arası CLı hızlı başlangıç örnekleri](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Özet

Bu makalede basit dikey ölçeklendirme örnekleri gösterildi. Bu derleme blokları ile Otomasyon hesabı, runbook 'lar, Web kancaları, uyarılar ile, özelleştirilmiş bir eylem kümesiyle zengin çeşitli olaylar bağlayabilirsiniz.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
