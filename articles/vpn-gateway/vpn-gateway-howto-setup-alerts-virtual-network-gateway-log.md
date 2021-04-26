---
title: 'Azure VPN Gateway: Tanılama kaynak günlüğü olaylarında uyarı yapılandırma'
description: Azure Izleyici Log Analytics kullanarak Azure VPN Gateway kaynak günlüğü olaylarına göre uyarı ayarlamayı öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656623"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>VPN Gateway kaynak günlüğü olayları üzerinde uyarı ayarlama

Bu makale Azure Izleyici Log Analytics kullanarak Azure VPN Gateway kaynak günlüğü olaylarına göre uyarı ayarlamanıza yardımcı olur. 

Aşağıdaki kaynak günlükleri Azure 'da kullanılabilir:

|***Ad** _ | _ *_Açıklaması_** |
|--- | --- |
|GatewayDiagnosticLog | Ağ Geçidi yapılandırma olayları, birincil değişiklikler ve bakım olayları için kaynak günlüklerini içerir |
|Tüneldiagnosticlog | Tünel durumu değişikliği olaylarını içerir. Tünel bağlantısı/bağlantı kesme olayları, uygunsa durum değişikliği için özetlenen bir nedene sahiptir |
|RouteDiagnosticLog | Ağ geçidinde gerçekleşen statik rotalara ve BGP olaylarına yapılan değişiklikleri günlüğe kaydeder |
|Ikediagnosticlog | Ağ geçidinde ıKE denetim iletilerini ve olaylarını günlüğe kaydeder |
|P2SDiagnosticLog | Ağ geçidinde Noktadan siteye denetim iletilerini ve olayları günlüğe kaydeder. Yalnızca Ikev2 ve OpenVPN bağlantıları için bağlantı kaynağı bilgisi sağlanır |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Azure portal uyarıları ayarlama

Aşağıdaki örnek adımlarda, siteden siteye VPN tünelini içeren bir bağlantı kesme olayı için uyarı oluşturulur:


1. Azure portal, **tüm hizmetler** altında **Log Analytics** araması yapın ve **Log Analytics çalışma alanları**' nı seçin.

   ![Log Analytics çalışma alanlarına gitmek için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Oluştur")

2. **Log Analytics** sayfasında **Oluştur** ' u seçin.

   ![Oluştur düğmesi içeren Log Analytics sayfası](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Şunu seçin:")

3. **Yeni oluştur** ' u seçin ve ayrıntıları girin.

   ![Log Analytics çalışma alanı oluşturma ayrıntıları](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Şunu seçin:")

4.   >  **Tanılama ayarlarını** izle dikey penceresinde VPN ağ geçidinizi bulun.

   ![Tanılama ayarlarında VPN ağ geçidini bulma seçimleri](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Şunu seçin:")

5. Tanılamayı açmak için, ağ geçidine çift tıklayın ve ardından **tanılamayı aç**' ı seçin.

   ![Tanılamayı açmak için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Şunu seçin:")

6. Ayrıntıları girin ve Log Analytics ve **Tunneldiagnosticlog** ' a **Gönder** ' in seçildiğinden emin olun. Adım 3 ' te oluşturduğunuz Log Analytics çalışma alanını seçin.

   ![Seçili onay kutuları](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Şunu seçin:")

   > [!NOTE]
   > Verilerin başlangıçta gösterilmesi birkaç saat sürebilir.

7. Sanal ağ geçidi kaynağına genel bakış ' a gidin ve **izleme** sekmesinden **Uyarılar** ' ı seçin. Sonra yeni bir uyarı kuralı oluşturun veya var olan bir uyarı kuralını düzenleyin.

   ![Yeni bir uyarı kuralı oluşturmaya yönelik seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Şunu seçin:")

   ![Noktadan siteye](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Şunu seçin:")
8. Log Analytics çalışma alanını ve kaynağı seçin.

   ![Çalışma alanı ve kaynak seçimleri](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Şunu seçin:")

9. **Koşul Ekle** altında, sinyal mantığı olarak **özel günlük araması** ' nı seçin.

   ![Özel günlük araması için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Şunu seçin:")

10. **Arama sorgusu** metin kutusuna aşağıdaki sorguyu girin.  <> ve TimeGenerated değerlerini uygun şekilde değiştirin.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Eşik değerini 0 olarak ayarlayın ve **bitti**' yi seçin.

    ![Bir sorgu girme ve eşik seçme](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Şunu seçin:")

11. **Kural oluştur** sayfasında, **eylem grupları** bölümünde **Yeni oluştur** ' u seçin. Ayrıntıları girin ve **Tamam**' ı seçin.

    ![Yeni bir eylem grubu için Ayrıntılar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Şunu seçin:")

12. **Kural oluştur** sayfasında, **eylem grubu adı** bölümünde doğru adın göründüğünden emin olmak için, ayrıntıları **Özelleştir** ' in ayrıntılarını girin. Kuralı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    ![Kural oluşturmaya yönelik seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Şunu seçin:")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>PowerShell kullanarak uyarıları ayarlama

Aşağıdaki örnek adımlarda, siteden siteye VPN tünelini içeren bir bağlantı kesilmesi olayı için uyarı oluşturulur.

1. Log Analytics çalışma alanı oluşturun:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. VPN ağ geçidi için tanılamayı açın:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Bir eylem grubu oluşturun.

   Bu kod, bir uyarı tetiklendiğinde e-posta bildirimi gönderen bir eylem grubu oluşturur:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Özel günlük aramasını temel alan bir uyarı kuralı oluşturun:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Sonraki adımlar

Tünel ölçümlerinde uyarıları yapılandırmak için bkz. [VPN Gateway ölçümleri üzerinde uyarıları ayarlama](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
