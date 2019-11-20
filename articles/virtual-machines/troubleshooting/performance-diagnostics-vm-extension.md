---
title: Windows için Azure Performans Tanılama VM Uzantısı | Microsoft Docs
description: Windows için Azure Performans Tanılama VM uzantısını tanıtır.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057539"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows için Azure Performans Tanılama VM Uzantısı

Azure Performans Tanılama VM uzantısı, Windows VM 'lerinden Performans Tanılama verilerinin toplanmasını sağlar. Uzantı analiz gerçekleştirir ve sanal makinede performans sorunlarını belirlemek ve çözmek için bulgular ve öneriler hakkında bir rapor sağlar. Bu uzantı, [Perfinsıghts](https://aka.ms/perfinsights)adlı bir sorun giderme aracı yüklüyor.

> [!NOTE]
> Klasik olmayan VM 'Ler için Azure portal sanal makinelerinizdeki tanılamayı çalıştırmak istiyorsanız, yeni deneyim kullanılması önerilir. Daha fazla bilgi için bkz. [Azure sanal makineler Için Performans Tanılama](performance-diagnostics.md) 

## <a name="prerequisites"></a>Önkoşullar

Bu uzantı Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 ve Windows Server 2016 üzerine yüklenebilir. Ayrıca, Windows 8.1 ve Windows 10 ' da de yüklenebilir.

## <a name="extension-schema"></a>Uzantı şeması
Aşağıdaki JSON, Azure Performans Tanılama VM Uzantısı şemasını gösterir. Bu uzantı, tanılama çıkışını ve raporunu depolamak için bir depolama hesabının adını ve anahtarını gerektirir. Bu değerler duyarlıdır. Depolama hesabı anahtarı korumalı ayar Yapılandırması içinde depolanmalıdır. Azure VM Uzantısı korumalı ayar verileri şifrelenir ve yalnızca hedef sanal makinede şifresi çözülür. **StorageAccountName** ve **storageaccountkey** 'ın büyük/küçük harfe duyarlı olduğunu unutmayın. Aşağıdaki bölümde, diğer gerekli parametreler listelenmiştir.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Özellik değerleri

|   **Name**   |**Değer/örnek**|       **Açıklama**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API sürümü.
|publisher|Microsoft. Azure. performance. Diagnostics|Uzantı için yayımcı ad alanı.
|type|AzurePerformanceDiagnostics|VM uzantısının türü.
|typeHandlerVersion|1.0|Uzantı işleyicisinin sürümü.
|Performanslı Orceni|temel|Verilerin yakalanması için performans senaryosu. Geçerli değerler şunlardır: **Basic**, **vmslow**, **azurefiles**ve **Custom**.
|traceDurationInSeconds|300|İzleme seçeneklerinden herhangi biri seçilirse izlemelerin süresi.
|perfCounterTrace|p|Performans sayacı Izlemesini etkinleştirme seçeneği. Geçerli değerler **p** veya boş değerdir. Bu izlemeyi yakalamak istemiyorsanız, değeri boş bırakın.
|networkTrace|n|Ağ Izlemesini etkinleştirme seçeneği. Geçerli değerler **n** veya boş değer. Bu izlemeyi yakalamak istemiyorsanız, değeri boş bırakın.
|xperfTrace|x|XPerf Izlemesini etkinleştirme seçeneği. Geçerli değerler **x** veya boş değer. Bu izlemeyi yakalamak istemiyorsanız, değeri boş bırakın.
|storPortTrace|s|StorPort Izlemesini etkinleştirme seçeneği. Geçerli değerler **s** veya boş değer. Bu izlemeyi yakalamak istemiyorsanız, değeri boş bırakın.
|srNumber|123452016365929|Varsa destek bileti numarası. Değer yoksa boş bırakın.
|requestTimeUtc|2017-09-28T22:08:53.736Z|UTC olarak geçerli tarih saat. Bu uzantıyı yüklemek için portalını kullanıyorsanız, bu değeri sağlamanız gerekmez.
|resourceId|/Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/Providers/{resourceprovidernamespace}/{resourcettypeınfo}/{resourceName}|Bir VM 'nin benzersiz tanımlayıcısı.
|storageAccountName|mystorageaccount|Tanılama günlüklerini ve sonuçlarını depolamak için depolama hesabının adı.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Depolama hesabı için anahtar.

## <a name="install-the-extension"></a>Uzantıyı yükleme

Uzantıyı Windows sanal makinelerine yüklemek için aşağıdaki yönergeleri izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Bu uzantıyı yüklemek istediğiniz sanal makineyi seçin.

    ![Sanal makineler vurgulanmış Azure portal ekran görüntüsü](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. **Uzantılar** dikey penceresini seçin ve **Ekle**' yi seçin.

    ![Vurgulanan eklenti ile uzantılar dikey penceresinin ekran görüntüsü](media/performance-diagnostics-vm-extension/select-extensions.png)
4. **Azure performans tanılaması**' nı seçin, hüküm ve koşulları gözden geçirin ve **Oluştur**' u seçin.

    ![Azure performans tanısı vurgulanmış şekilde yeni kaynak ekranının ekran görüntüsü](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Yükleme için parametre değerlerini sağlayın ve uzantıyı yüklemek için **Tamam** ' ı seçin. Desteklenen senaryolar hakkında daha fazla bilgi için bkz. [Perfinsıghts kullanma](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Uzantı Install iletişim kutusunun ekran görüntüsü](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Yükleme başarılı olduğunda, bu durumu belirten bir ileti görürsünüz.

    ![Sağlama başarılı iletisinin ekran görüntüsü](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > , Sağlama başarılı olduğunda uzantı çalışır. Temel senaryo için iki dakika veya daha kısa sürer. Diğer senaryolarda, yükleme sırasında belirtilen süre boyunca çalışır.

## <a name="remove-the-extension"></a>Uzantıyı kaldırın
Uzantıyı bir sanal makineden kaldırmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)oturum açın, bu uzantıyı kaldırmak istediğiniz sanal makineyi seçin ve ardından **Uzantılar** dikey penceresini seçin. 
2. Listeden Performans Tanılama uzantısı girişi için ( **...** ) öğesini seçin ve **Kaldır**' ı seçin.

    ![Kaldırma vurgulanmış olarak uzantılar dikey penceresinin ekran görüntüsü](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Uzantı girişini de seçebilir ve **Kaldır** seçeneğini belirleyebilirsiniz.

## <a name="template-deployment"></a>Şablon dağıtımı
Azure sanal makine uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Önceki bölümde ayrıntılı olan JSON şeması bir Azure Resource Manager şablonunda kullanılabilir. Bu, Azure Resource Manager şablon dağıtımı sırasında Azure Performans Tanılama VM uzantısını çalıştırır. Örnek bir şablon aşağıda verilmiştir:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell dağıtımı
Bu `Set-AzVMExtension` komut, Azure Performans Tanılama VM uzantısını var olan bir sanal makineye dağıtmak için kullanılabilir.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Yakalanan veriler hakkında bilgi
Perfinsıghts Aracı, seçilen senaryoya bağlı olarak çeşitli günlükleri, yapılandırmaları ve tanılama verilerini toplar. Daha fazla bilgi için bkz. [Perfinsıghts belgeleri](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Sonuçları görüntüleme ve paylaşma

Uzantının çıktısı, yükleme sırasında belirtilen depolama hesabına yüklenen ve [paylaşılan erişim imzaları (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)kullanılarak 30 gün boyunca paylaşılabilecek bir ZIP dosyasında bulunabilir. Bu ZIP dosyası tanılama günlükleri ve bulguları ve önerilerle bir rapor içerir. Çıkış ZIP dosyasına bir SAS bağlantısı, **C:\Packages\Plugins\Microsoft.Azure.performance.Diagnostics.AzurePerformanceDiagnostics\\ \< klasörü altındaki zipfilename _saslink. txt adlı bir metin dosyası içinde bulunabilir sürüm >** . Bu bağlantıya sahip olan herkes ZIP dosyasını indirebiliyor.

Destek mühendisinize yönelik destek Mühendisinize yardımcı olmak için, Microsoft bu SAS bağlantısını kullanarak tanılama verilerini indirebilir.

Raporu görüntülemek için, ZIP dosyasını ayıklayın ve **Perfinsıghts Report. html** dosyasını açın.

Ayrıca, uzantıyı seçerek ZIP dosyasını doğrudan portaldan indirebilirsiniz.

![Performans Tanılama ayrıntılı durumunun ekran görüntüsü](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Portalda görünen SAS bağlantısı bazen çalışmayabilir. Bunun nedeni, kodlama ve kod çözme işlemleri sırasında hatalı biçimlendirilmiş bir URL olabilir. Bunun yerine, bağlantıyı doğrudan VM 'deki * _saslink. txt dosyasından edinebilirsiniz.

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

- Uzantı başarılı bir şekilde sağlanmış olsa bile uzantı dağıtım durumu (bildirim alanında) "dağıtım sürüyor" gösterebilir.

    Uzantı durumu uzantının başarıyla sağlandığını gösterdiği sürece bu sorun güvenle yoksayılabilir.
- Uzantı günlüklerini kullanarak yükleme sırasında bazı sorunları ele alabilirsiniz. Uzantı yürütme çıkışı aşağıdaki dizinde bulunan dosyalara kaydedilir:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
