---
title: Azure Automation 'da metin runbook 'larını düzenleme
description: Bu makalede, PowerShell ve PowerShell Iş akışı runbook 'ları ile çalışmak için Azure Otomasyonu metinsel Düzenleyicisi 'nin nasıl kullanılacağı açıklanır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829395"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Azure Automation 'da metin runbook 'larını düzenleme

[PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 'Larını ve [PowerShell iş akışı runbook 'Larını](automation-runbook-types.md#powershell-workflow-runbooks)düzenlemek için Azure Otomasyonu 'ndaki metinsel düzenleyiciyi kullanabilirsiniz. Bu düzenleyici, IntelliSense gibi diğer kod düzenleyicilerinin tipik özelliklerine sahiptir. Ayrıca, runbook 'larda ortak kaynaklara erişirken size yardımcı olmak için ek özel özelliklerle birlikte renk kodlaması kullanır. 

Metinsel düzenleyici, bir runbook 'a cmdlet 'ler, varlıklar ve alt runbook 'lar için kod ekleme özelliğini içerir. Kodu kendiniz yazmak yerine, kullanılabilir kaynaklar listesinden seçim yapabilirsiniz ve düzenleyici ilgili kodu runbook 'a ekler.

Azure Otomasyonu 'ndaki her runbook 'un taslak ve yayımlanan iki sürümü vardır. Runbook 'un taslak sürümünü düzenleyin ve ardından yürütülebilmesi için yayımlayın. Yayımlanan sürüm düzenlenemez. Daha fazla bilgi için bkz. [runbook yayımlama](manage-runbooks.md#publish-a-runbook).

Bu makalede, bu düzenleyiciyle farklı işlevler gerçekleştirmeye yönelik ayrıntılı adımlar sağlanmaktadır. Bunlar [grafik runbook 'lar](automation-runbook-types.md#graphical-runbooks)için geçerli değildir. Bu runbook 'larla çalışmak için bkz. [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Azure portal ile runbook 'u düzenleme

1. Azure portal Otomasyon hesabınızı seçin.
2. **Işlem Otomasyonu** altında runbook 'ların listesini açmak Için **runbook 'lar** ' ı seçin.
3. Düzenlenecek runbook 'u seçin ve ardından **Düzenle**' ye tıklayın.
4. Runbook 'u düzenleyin.
5. Düzenlemeleriniz tamamlandığında **Kaydet** 'e tıklayın.
6. Runbook 'un en son taslak sürümünü yayımlamak istiyorsanız **Yayımla** ' ya tıklayın.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Runbook 'a bir cmdlet ekleme

1. Metinsel düzenleyicinin tuvalinde, cmdlet 'ini yerleştirmek istediğiniz yere imleci konumlandırın.
2. Kitaplık denetimindeki **cmdlet 'ler** düğümünü genişletin.
3. Kullanılacak cmdlet 'i içeren modülü genişletin.
4. Eklemek için cmdlet adına sağ tıklayın ve **tuvale Ekle**' yi seçin. Cmdlet 'in birden fazla parametre kümesi varsa, düzenleyici varsayılan kümesini ekler. Ayrıca cmdlet 'i genişleterek farklı bir parametre kümesi seçebilirsiniz.
5. Cmdlet kodunun tüm parametre listesiyle birlikte eklendiğini unutmayın.
6. Gerekli parametreler için açılı ayraç (<>) içine alınmış değerin yerine uygun bir değer sağlayın. İhtiyacınız olmayan parametreleri kaldırın.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Runbook 'a bir alt runbook için kod ekleme

1. Metinsel düzenleyicinin tuvalinde, [alt runbook](automation-child-runbooks.md)için kodu yerleştirmek istediğiniz yere imleci konumlandırın.
2. Kitaplık denetimindeki **runbook 'lar** düğümünü genişletin.
3. Eklemek için Runbook 'a sağ tıklayın ve **tuvale Ekle**' yi seçin.
4. Alt runbook kodu herhangi bir runbook parametresi için herhangi bir yer tutucu ile birlikte eklenir.
5. Yer tutucuları her parametre için uygun değerlerle değiştirin.

### <a name="insert-an-asset-into-a-runbook"></a>Runbook 'a varlık ekleme

1. Metinsel düzenleyicinin tuval denetiminde, alt runbook için kodu yerleştirmek istediğiniz yere imleci konumlandırın.
2. Kitaplık denetimindeki **varlıklar** düğümünü genişletin.
3. İstenen varlık türü için düğümü genişletin.
4. Eklemek için varlık adına sağ tıklayın ve **tuvale Ekle**' yi seçin. Değişken [varlıklar](./shared-resources/variables.md)için, değişkeni almak veya ayarlamak istediğinize bağlı olarak " **değişkeni al" seçeneğini tuvale ekleyin** veya **"set variable" öğesini tuvale ekleyin**.
5. Varlık kodunun runbook 'a eklendiğini unutmayın.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell kullanarak bir Azure Otomasyonu runbook 'unu düzenleme

Windows PowerShell ile bir runbook 'u düzenlemek için, seçtiğiniz düzenleyiciyi kullanın ve Runbook 'u bir **. ps1** dosyasına kaydedin. Runbook 'un içeriğini almak için [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet 'ini kullanabilirsiniz. Var olan taslak runbook 'unu değiştirilmiş bir ile değiştirmek için  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet 'ini kullanabilirsiniz.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell kullanarak bir runbook 'un içeriğini alma

Aşağıdaki örnek komutlarda bir runbook için betiğin nasıl alınacağı ve bir betik dosyasına kaydedileceği gösterilmektedir. Bu örnekte Taslak sürümü alınmaktadır. Runbook 'un yayımlanan sürümünü almak mümkün olsa da bu sürüm değiştirilemez.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell kullanarak bir runbook 'un içeriğini değiştirme

Aşağıdaki örnek komutlarda bir runbook 'un mevcut içeriğinin bir betik dosyasının içeriğiyle nasıl değiştirileceği gösterilmektedir. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Otomasyonu 'nda runbook 'Ları yönetin](manage-runbooks.md).
* [PowerShell iş akışını öğrenme](automation-powershell-workflow.md).
* [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md).
* [Sertifikalar](./shared-resources/certificates.md).
* [Bağlantı](automation-connections.md).
* [Kimlik bilgileri](./shared-resources/credentials.md).
* [Zamanlamalar](./shared-resources/schedules.md).
* [Değişkenler](./shared-resources/variables.md).
* [PowerShell cmdlet başvurusu](/powershell/module/az.automation).
