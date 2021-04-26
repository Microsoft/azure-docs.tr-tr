---
title: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak ağ güvenlik grubu akış günlüklerini yapılandırma (ARM şablonu)'
description: Ağ güvenlik grubu (NSG) akış günlüklerinin bir Azure Resource Manager şablonu (ARM şablonu) ve Azure PowerShell kullanarak programlı bir şekilde nasıl etkinleştirileceğini öğrenin.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532446"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak ağ güvenlik grubu akış günlüklerini yapılandırma

Bu hızlı başlangıçta [ağ güvenlik grubu (NSG) akış günlüklerini](network-watcher-nsg-flow-logging-overview.md) bir [Azure Resource Manager](../azure-resource-manager/management/overview.md) şablonu (ARM şablonu) ve Azure PowerShell kullanarak nasıl etkinleştireceğinizi öğreneceksiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

NSG akış günlüğü nesnesinin özelliklerine ilişkin bir genel bakış ile başlayacağız. Örnek Şablonlar sunuyoruz. Ardından, şablonu dağıtmak için yerel bir Azure PowerShell örneği kullanıyoruz.

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portal açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullandığımız şablon [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Bu kaynaklar şablonda tanımlanmıştır:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. resources/dağıtımlar](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG akış günlükleri nesnesi

Aşağıdaki kod bir NSG akış günlükleri nesnesini ve parametrelerini gösterir. Bir kaynak oluşturmak için `Microsoft.Network/networkWatchers/flowLogs` , bu kodu şablonunuzun kaynaklar bölümüne ekleyin:

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

NSG akış günlükleri nesne özelliklerine yönelik kapsamlı bir genel bakış için bkz. [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Şablonunuzu oluşturma

ARM şablonlarını ilk kez kullanıyorsanız ARM şablonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [ARM şablonları ve Azure PowerShell kaynak dağıtma](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Aşağıdaki örnek, tamamlanmış bir şablondur. Ayrıca, şablonun en basit sürümüdür. Örnek, NSG akış günlüklerini ayarlamak için geçirilen minimum parametreleri içerir. Daha fazla örnek için, [bir Azure Resource Manager şablonundan NSG akış günlüklerini yapılandırma](network-watcher-nsg-flow-logging-azure-resource-manager.md)genel bakış makalesine bakın.

### <a name="example"></a>Örnek

Aşağıdaki şablon bir NSG için akış günlüklerini etkinleştirerek günlükleri belirli bir depolama hesabında depolar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - Kaynak adı _ParentResource_ChildResource_ biçimini kullanır. Örneğimizde, üst kaynak bölgesel Azure ağ Izleyicisi örneğidir:
>    - **Biçim**: NetworkWatcher_RegionName
>    - **Örnek**: NetworkWatcher_centraluseuap
> - `targetResourceId` hedef NSG 'nin kaynak KIMLIĞIDIR.
> - `storageId` , hedef depolama hesabının kaynak KIMLIĞIDIR.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Bu öğreticide, akış günlüğünü etkinleştirebildiğiniz mevcut bir kaynak grubunuz ve NSG olduğunu varsaymaktadır.

Bu makalede görüntülenen örnek şablonlardan herhangi birini, *azuredeploy.js* için yerel olarak kaydedebilirsiniz. Özellik değerlerini aboneliğinizdeki geçerli kaynaklara işaret eden şekilde güncelleştirin.

Şablonu dağıtmak için Azure PowerShell ' de aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Bu komutlar, NSG 'yi içeren kaynak grubuna değil, örnek NetworkWatcherRG kaynak grubuna bir kaynak dağıtır.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Dağıtımınızın başarılı olup olmadığını görmek için iki seçeneğiniz vardır:

- PowerShell konsolunuz `ProvisioningState` olarak gösterilir `Succeeded` .
- Değişikliklerinizi onaylamak için [NSG akış günlükleri portalı sayfasına](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) gidin.

Dağıtım ile ilgili sorunlar varsa bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure kaynaklarını, tüm dağıtım modunu kullanarak silebilirsiniz. Bir akış günlükleri kaynağını silmek için, silmek istediğiniz kaynağı dahil etmeden bir dağıtımı tamamlanmış modda belirtin. [Dağıtım modundan tamamen](../azure-resource-manager/templates/deployment-modes.md#complete-mode)ilgili daha fazla bilgi edinin.

Ayrıca Azure portal bir NSG akış günlüğünü devre dışı bırakabilirsiniz:

1. Azure portalında oturum açın.
1. **Tüm Hizmetler**’i seçin. **Filtre** kutusuna **Ağ İzleyicisi**' ni girin. Arama sonuçlarında **Ağ İzleyicisi**' ni seçin.
1. **Günlükler** altında **NSG akış günlükleri**' ni seçin.
1. NSG 'ler listesinde, akış günlüklerini devre dışı bırakmak istediğiniz NSG 'yi seçin.
1. **Akış günlükleri ayarları** altında **kapalı**' yı seçin.
1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir ARM şablonu kullanarak NSG akış günlüklerini etkinleştirmeyi öğrendiniz. Ardından, aşağıdaki seçeneklerden birini kullanarak NSG akış verilerinizi görselleştirmeyi öğrenin:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Açık kaynaklı Araçlar](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Trafik Analizi](traffic-analytics.md)
