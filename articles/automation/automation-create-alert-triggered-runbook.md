---
title: Bir Azure Otomasyonu runbook 'unu tetiklemek için uyarı kullanma
description: Bir Azure uyarısı başlatıldığında runbook 'un çalışmasını nasıl tetikleyeceğinizi öğrenin.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c818114df436dbbd3ac1a51b6eeec00b9eec4d3
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915721"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Bir Azure Otomasyonu runbook 'unu tetiklemek için uyarı kullanma

Azure [izleyici](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) 'Yi, Azure 'daki birçok hizmetin temel düzey ölçümlerini ve günlüklerini izlemek için kullanabilirsiniz. [Eylem gruplarını](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) kullanarak veya uyarıları temel alarak görevleri otomatikleştirmek için klasik uyarıları kullanarak Azure Otomasyonu runbook 'larını çağırabilirsiniz. Bu makalede, uyarıları kullanarak bir runbook 'un nasıl yapılandırılacağı ve çalıştırılacağı gösterilmektedir.

## <a name="alert-types"></a>Uyarı türleri

Otomasyon Runbook 'larını üç uyarı türüyle kullanabilirsiniz:

* Sık karşılaşılan uyarılar
* Etkinlik günlüğü uyarıları
* Neredeyse gerçek zamanlı ölçüm uyarıları

> [!NOTE]
> Ortak uyarı şeması, bugün Azure 'daki uyarı bildirimleri için tüketim deneyimini standartlaştırır. Geçmişte, bugün Azure 'daki üç uyarı türü (ölçüm, günlük ve etkinlik günlüğü) kendi e-posta şablonlarına, Web kancası şemalarına vb. sahip. Daha fazla bilgi için bkz. [ortak uyarı şeması](../azure-monitor/platform/alerts-common-schema.md)

Bir uyarı runbook 'u çağırdığında, gerçek çağrı Web kancasına yönelik bir HTTP POST isteğidir. POST isteğinin gövdesi, uyarıyla ilgili yararlı özelliklere sahip olan JSON biçimli bir nesne içerir. Aşağıdaki tabloda her uyarı türü için yük şemasının bağlantıları listelenmektedir:

|Uyarı  |Açıklama|Yük şeması  |
|---------|---------|---------|
|[Ortak uyarı](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Bugün Azure 'daki uyarı bildirimleri için tüketim deneyimini standartlaştıran ortak uyarı şeması.|Ortak uyarı yük şeması|
|[Etkinlik günlüğü uyarısı](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure etkinlik günlüğündeki yeni bir olay belirli koşullara uyan bir bildirim gönderir. Örneğin, bir `Delete VM` işlem **myüretim resourcegroup** içinde veya **etkin** duruma sahip yeni bir Azure hizmet durumu olayı göründüğünde oluşur.| [Etkinlik günlüğü uyarısı yük şeması](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Neredeyse gerçek zamanlı ölçüm uyarısı](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Bir veya daha fazla platform düzeyi ölçümü belirtilen koşullara uyuyorsa ölçüm uyarılarından daha hızlı bir bildirim gönderir. Örneğin, bir VM üzerindeki **CPU%** değeri **90**' den büyükse ve **içindeki ağ** değeri, son 5 dakika boyunca **500 MB** 'den büyük olur.| [Neredeyse gerçek zamanlı ölçüm uyarısı yük şeması](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Her uyarı türü tarafından belirtilen veriler farklı olduğundan, her uyarı türü farklı şekilde işlenir. Sonraki bölümde, farklı uyarı türlerini işlemek için bir runbook oluşturmayı öğreneceksiniz.

## <a name="create-a-runbook-to-handle-alerts"></a>Uyarıları işlemek için Runbook oluşturma

Otomasyon 'u uyarılarla birlikte kullanmak için, runbook 'a geçirilen uyarı JSON yükünü yöneten mantığı olan bir runbook 'a ihtiyacınız vardır. Aşağıdaki örnek runbook 'un bir Azure uyarısından çağrılması gerekir.

Yukarıdaki bölümde açıklandığı gibi, her uyarı türünün farklı bir şeması vardır. Betik, `WebhookData` runbook giriş parametresindeki Web kancası verilerini bir uyarıdan alır. Ardından, komut dosyası JSON yükünü hangi uyarı türünün kullanıldığını belirleyecek şekilde değerlendirir.

Bu örnek, bir VM 'den bir uyarı kullanır. Yük kaynağından VM verilerini alır ve ardından bu bilgileri kullanarak VM 'yi durdurur. Bağlantı, runbook 'un çalıştırıldığı Otomasyon hesabında ayarlanmalıdır. Runbook 'ları tetiklemek için uyarıları kullanırken, tetiklenen runbook 'taki uyarının durumunu denetlemek önemlidir. Uyarı her değişiklik durumunda runbook tetiklenecek. Uyarıların birden çok durumu vardır, en yaygın iki durum ve `Activated` ' `Resolved`dir. Runbook 'larınızın birden çok kez çalıştırılmadığından emin olmak için Runbook mantığınızdaki bu durumu kontrol edin. Bu makaledeki örnekte yalnızca `Activated` uyarıların nasıl aranacağı gösterilmektedir.

Runbook, VM 'ye karşı yönetim eylemini gerçekleştirmek üzere Azure ile kimlik doğrulaması yapmak için **Azurerunasconnection** [Farklı Çalıştır hesabını](automation-create-runas-account.md) kullanır.

**Stop-AzureVmInResponsetoVMAlert**adlı bir runbook oluşturmak için bu örneği kullanın. PowerShell betiğini değiştirebilir ve birçok farklı kaynakla kullanabilirsiniz.

1. Azure Otomasyonu hesabınıza gidin.
2. **Işlem Otomasyonu**altında **runbook 'lar**' ı seçin.
3. Runbook 'ların listesinin en üstünde **+ runbook oluştur**' u seçin.
4. **Runbook Ekle** sayfasında, Runbook adı için **stop-AzureVmInResponsetoVMAlert** girin. Runbook türü için **PowerShell**' i seçin. Ardından **Oluştur**’u seçin.  
5. Aşağıdaki PowerShell örneğini **düzenleme** sayfasına kopyalayın.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Runbook 'u kaydetmek ve yayımlamak için **Yayımla** ' yı seçin.

## <a name="create-the-alert"></a>Uyarı oluşturma

Uyarılar, uyarı tarafından tetiklenen eylemlerin koleksiyonları olan eylem gruplarını kullanır. Runbook 'lar yalnızca eylem gruplarıyla kullanabileceğiniz birçok eylemden biridir.

1. Otomasyon hesabınızda, **izleme**altında **Uyarılar** ' ı seçin.
1. **+ Yeni uyarı kuralı**' nı seçin.
1. **Kaynak**altında **Seç** ' e tıklayın. **Kaynak seçin** sayfasında, uyarı vermek için sanal makineyi seçin ve **bitti**' ye tıklayın.
1. **Koşul**bölümünde **Koşul Ekle** ' ye tıklayın. Kullanmak istediğiniz sinyali seçin (örneğin **CPU yüzdesi** ) ve **bitti**' ye tıklayın.
1. **Sinyal mantığını Yapılandır** sayfasında, **Uyarı mantığı**altında **eşik değerini** girin ve **bitti**' ye tıklayın.
1. **Eylem grupları**' nın altında **Yeni oluştur**' u seçin.
1. **Eylem grubu Ekle** sayfasında, eylem grubunuza bir ad ve kısa ad verin.
1. Eyleme bir ad verin. Eylem türü için **Otomasyon Runbook 'u**seçin.
1. **Ayrıntıları Düzenle**' yi seçin. Runbook 'u **Yapılandır** sayfasında, **runbook kaynağı**altında **Kullanıcı**' yı seçin.  
1. **Aboneliğinizi** ve **Otomasyon hesabınızı**seçin ve ardından **stop-AzureVmInResponsetoVMAlert** runbook ' u seçin.  
1. **Ortak uyarı şemasını etkinleştirmek**için **Evet** ' i seçin.
1. Eylem grubunu oluşturmak için **Tamam**' ı seçin.

    ![Eylem grubu Ekle sayfası](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Bu eylem grubunu [etkinlik günlüğü uyarılarında](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) ve oluşturduğunuz [neredeyse gerçek zamanlı uyarılarda](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) kullanabilirsiniz.

1. **Uyarı ayrıntıları**' nın altında bir uyarı kuralı adı ve açıklaması ekleyin ve **Uyarı kuralı oluştur**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* Web kancası kullanarak Otomasyon Runbook 'u başlatma hakkında daha fazla bilgi için bkz. [Web kancasından runbook başlatma](automation-webhooks.md).
* Runbook 'u başlatmak için farklı yollar hakkında daha fazla bilgi için bkz. [runbook 'U başlatma](automation-starting-a-runbook.md).
* Etkinlik günlüğü uyarısı oluşturmayı öğrenmek için bkz. [etkinlik günlüğü uyarıları oluşturma](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Neredeyse gerçek zamanlı uyarı oluşturmayı öğrenmek için [Azure Portal bir uyarı kuralı oluşturma](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)bölümüne bakın.
