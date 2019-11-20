---
title: Mantıksal uygulamalarınızı ve Runbook 'larınızı güncelleştirerek Azure Izleyici klasik uyarılar geçişine hazırlanma
description: Web kancaları, Logic Apps ve Runbook 'larınızı gönüllü geçiş için hazırlamak üzere nasıl değiştireceğinizi öğrenin.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5235db5cab39be6e36bdf145d3edc7c73fe9da54
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827399"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Mantıksal uygulamalarınızı ve Runbook 'larınızı klasik uyarı kurallarının geçirilmesi için hazırlama

[Daha önce duyurulduğu](monitoring-classic-retirement.md)gibi, Azure izleyici 'deki klasik uyarılar Eylül 2019 ' de kullanımdan kaldırılıyor (ilk olarak 2019 Temmuz idi). Klasik uyarı kuralları kullanan ve geçiş yapmak isteyen müşterilere Azure portal bir geçiş aracı vardır.

> [!NOTE]
> Geçiş Aracı 'nın çıkış gecikmesi nedeniyle, klasik uyarılar geçişinin devre dışı bırakılması tarihi 31 Haziran 2019 tarihinde ilk bildirilen tarihten itibaren 31 Ağustos 2019 tarihine kadar genişletilmiştir.

Klasik uyarı kurallarınızı yeni uyarı kurallarına göre gönüllü olarak geçirmeyi tercih ederseniz, iki sistem arasında bazı farklılıklar olduğunu unutmayın. Bu makalede bu farklılıklar ve değişikliğe nasıl hazırlanacağı açıklanmaktadır.

## <a name="api-changes"></a>API değişiklikleri

Klasik uyarı kuralları (`microsoft.insights/alertrules`) oluşturan ve yöneten API 'ler, yeni ölçüm uyarıları (`microsoft.insights/metricalerts`) oluşturan ve yöneten API 'lerden farklıdır. Hemen klasik uyarı kuralları oluşturup yönetiyorsanız, dağıtım betiklerinizi yeni API 'lerle çalışacak şekilde güncelleştirin.

Aşağıdaki tabloda hem klasik hem de yeni uyarılar için programlı arabirimlerin bir başvurusu verilmiştir:

|         |Klasik uyarılar  |Yeni ölçüm uyarıları |
|---------|---------|---------|
|REST API     | [Microsoft. Insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [Microsoft. Insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az Monitor Alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az Monitor ölçümleri uyarısı](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Başvuru](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Başvuru](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager şablonu | [Klasik uyarılar için](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Yeni ölçüm uyarıları için](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Bildirim yükü değişiklikleri

Bildirim yükü biçimi, [Klasik uyarı kuralları](alerts-webhooks.md) ve [yeni ölçüm uyarıları](alerts-metric-near-real-time.md#payload-schema)arasında biraz farklıdır. Klasik uyarı kuralları tarafından tetiklenen bir Web kancası, mantıksal uygulama veya Runbook eylemleriniz varsa, bu bildirim uç noktalarını yeni ölçüm uyarılarının yük biçimini kabul edecek şekilde güncelleştirmeniz gerekir.

Web kancası yük alanlarını klasik biçimden yeni biçime eşlemek için aşağıdaki tabloyu kullanın:

|  |Klasik uyarılar  |Yeni ölçüm uyarıları |
|---------|---------|---------|
|Uyarı etkinleştirildi veya çözüldü mı?    | **status**       | **Data. Status** |
|Uyarı hakkında bağlamsal bilgiler     | **bağlam**        | **Data. Context**        |
|Uyarının etkinleştirildiği veya çözümlendiği zaman damgası     | **Context. Timestamp**       | **Data. Context. Timestamp**        |
| Uyarı kuralı KIMLIĞI | **context.id** | **data.context.id** |
| Uyarı kuralı adı | **context.name** | **data.context.name** |
| Uyarı kuralının açıklaması | **Context. Description** | **Data. Context. Description** |
| Uyarı kuralı koşulu | **Context. Condition** | **Data. Context. Condition** |
| Ölçüm adı | **Context. Condition. metricName** | **Data. Context. Condition. allOf [0]. metricName** |
| Zaman toplama (ölçüm değerlendirme penceresinde nasıl toplanır)| **Context. Condition. Timetoplamasını** | **Context. Condition. Timetoplamasını** |
| Değerlendirme süresi | **Context. Condition. windowSize** | **Data. Context. Condition. windowSize** |
| İşleç (toplanan ölçüm değeri eşiğe göre nasıl karşılaştırılır) | **Context. Condition. işleci** | **Data. Context. Condition. işleci** |
| Eşik | **Context. Condition. Threshold** | **Data. Context. Condition. allOf [0]. eşik** |
| Ölçüm değeri | **Context. Condition. metricValue** | **Data. Context. Condition. allOf [0]. metricValue** |
| Abonelik Kimliği | **Context. SubscriptionID** | **Data. Context. SubscriptionID** |
| Etkilenen kaynağın kaynak grubu | **Context. resourceGroup** | **Data. Context. resourceGroup** |
| Etkilenen kaynağın adı | **Context. resourceName** | **Data. Context. resourceName** |
| Etkilenen kaynağın türü | **Context. resourceType** | **Data. Context. resourceType** |
| Etkilenen kaynağın kaynak KIMLIĞI | **Context. ResourceID** | **Data. Context. ResourceID** |
| Portal kaynağı özet sayfasına doğrudan bağlantı | **Context. portalLink** | **Data. Context. portalLink** |
| Web kancası veya mantıksal uygulamaya geçirilecek özel yük alanları | **Özellikleri** | **Data. Properties** |

, Görebileceğiniz gibi yükleri benzerdir. Aşağıdaki bölümde şunlar sunulmaktadır:

- Mantıksal uygulamaları yeni biçimle çalışacak şekilde değiştirme hakkında ayrıntılar.
- Yeni uyarılar için bildirim yükünü çözümleyen bir runbook örneği.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Bir mantıksal uygulamayı ölçüm Uyarısı bildirimi alacak şekilde değiştirme

Klasik uyarılarla Logic Apps kullanıyorsanız, yeni ölçüm uyarıları yükünü ayrıştırmak için mantıksal uygulama kodunuzu değiştirmeniz gerekir. Şu adımları uygulayın:

1. Yeni bir mantıksal uygulama oluşturun.

1. "Azure Izleyici-ölçüm uyarısı Işleyicisi" şablonunu kullanın. Bu şablonda uygun şema tanımlı bir **http isteği** tetikleyicisi vardır.

    ![Logic-App-şablon](media/alerts-migration/logic-app-template.png "Ölçüm uyarısı şablonu")

1. İşlem mantığınızı barındırmak için bir eylem ekleyin.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Ölçüm uyarı bildirimi alan bir Otomasyon Runbook 'u kullanma

Aşağıdaki örnek, runbook 'unuzla kullanmak için PowerShell kodu sağlar. Bu kod, hem klasik ölçüm uyarı kuralları hem de yeni ölçüm uyarısı kuralları için yükleri ayrıştırabilirler.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Bir uyarı tetiklendiğinde bir sanal makineyi durduran runbook 'un tam bir örneği için bkz. [Azure Otomasyonu belgeleri](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Web kancaları aracılığıyla iş ortağı tümleştirmesi

[Klasik uyarılarla tümleştirilen iş ortaklarımızın](https://docs.microsoft.com/azure/azure-monitor/platform/partners) çoğu, tümleştirmelerine göre zaten yeni ölçüm uyarılarını desteklemektedir. Yeni ölçüm uyarıları ile zaten çalışan bilinen tümleştirmeler şunlardır:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Sıgnl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Burada listelenmeyen bir iş ortağı tümleştirmesi kullanıyorsanız, tümleştirme sağlayıcısı 'nın yeni ölçüm uyarıları ile çalıştığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Geçiş aracını kullanma](alerts-using-migration-tool.md)
- [Geçiş aracının nasıl çalıştığını anlama](alerts-understand-migration.md)
