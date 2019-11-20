---
title: Azure Otomasyonu 'nda runbook 'ları yönetme
description: Bu makalede, Azure Otomasyonu 'nda runbook 'ların nasıl yönetileceği açıklanır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a477811e46d97375d4dce4d83072dda60ca797c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717226"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Otomasyonu 'nda runbook 'ları yönetme

Bir runbook 'u [Yeni bir tane oluşturarak](#create-a-runbook) veya mevcut bir runbook 'u bir dosyadan veya [runbook galerisinden](automation-runbook-gallery.md)içeri aktararak Azure Otomasyonu 'na ekleyebilirsiniz. Bu makalede, runbook 'ları bir dosyadan oluşturma ve içeri aktarma hakkında bilgi sağlanır.  [Azure Otomasyonu Için runbook ve modül galerilerinde](automation-runbook-gallery.md)topluluk runbook 'larına ve modüllerine erişme hakkında tüm ayrıntıları edinebilirsiniz.

## <a name="create-a-runbook"></a>Runbook oluştur

Azure portalında veya Windows PowerShell 'den birini kullanarak Azure Otomasyonu 'nda yeni bir runbook oluşturabilirsiniz. Runbook oluşturulduktan sonra, [öğrenme PowerShell Iş akışı](automation-powershell-workflow.md) ve [Azure Otomasyonu 'nda grafik yazma](automation-graphical-authoring-intro.md)bilgilerini kullanarak düzenleyebilirsiniz.

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure portal runbook oluşturma

1. Azure portalında, Otomasyon hesabınızı açın.
2. Hub 'dan runbook 'ların listesini açmak için **runbook 'lar** ' ı seçin.
3. **Runbook Ekle** düğmesine tıklayın ve ardından **Yeni bir runbook oluşturun**.
4. Runbook için bir **ad** yazın ve [türünü](automation-runbook-types.md)seçin. Runbook adı bir harfle başlamalıdır ve harfler, rakamlar, alt çizgiler ve kısa çizgiler içerebilir.
5. Runbook 'u oluşturmak için **Oluştur** ' a tıklayın ve düzenleyiciyi açın.

### <a name="create-a-runbook-with-powershell"></a>PowerShell ile runbook oluşturma

Boş bir [PowerShell Iş akışı runbook 'u](automation-runbook-types.md#powershell-workflow-runbooks)oluşturmak için [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) cmdlet 'ini kullanabilirsiniz. Dört Runbook türünden birini belirtmek için **tür** parametresini kullanın.

Aşağıdaki örnek komutlarda yeni bir boş runbook 'un nasıl oluşturulacağı gösterilmektedir.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook 'u içeri aktarma

Azure Otomasyonu 'nda bir PowerShell betiği veya PowerShell Iş akışı (. ps1 uzantısı), aktarılan bir grafik runbook (. graphrunbook) veya Python 2 betiği (. çıya uzantısı) içeri aktararak yeni bir runbook oluşturabilirsiniz.  İçeri aktarma sırasında oluşturulan [runbook 'un türünü](automation-runbook-types.md) belirtmeniz gerekir ve aşağıdaki noktaları dikkate alın.

* Bir `.graphrunbook` dosya yalnızca yeni bir [grafik runbook 'una](automation-runbook-types.md#graphical-runbooks)aktarılabilir ve grafik runbook 'lar yalnızca bir `.graphrunbook` dosyadan oluşturulabilir.
* PowerShell `.ps1` iş akışı içeren bir dosya, yalnızca bir [PowerShell iş akışı runbook 'una](automation-runbook-types.md#powershell-workflow-runbooks)aktarılabilir. Dosya birden çok PowerShell Iş akışı içeriyorsa içeri aktarma başarısız olur. Her bir iş akışını kendi dosyasına kaydetmeli ve ayrı olarak içeri aktarmanız gerekir.
* PowerShell `.ps1` komut dosyası altyapısı tarafından tanınamadığından, PowerShell iş akışı içeren bir dosya [PowerShell runbook 'una](automation-runbook-types.md#powershell-runbooks)aktarılmamalıdır.
* Bir iş akışı içermeyen bir [](automation-runbook-types.md#powershell-runbooks) Dosya,PowerShellrunbook'unaveyaPowerShellişakışırunbook'una`.ps1` aktarılabilir [](automation-runbook-types.md#powershell-workflow-runbooks).  Bir PowerShell Iş akışı runbook 'una aktarılmışsa, bu, bir iş akışına dönüştürülür ve yapılan değişiklikleri belirten runbook 'a açıklama eklenir.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure portal bir dosyadaki runbook 'u içeri aktarmak için

Bir betik dosyasını Azure Otomasyonu 'na aktarmak için aşağıdaki yordamı kullanabilirsiniz.

> [!NOTE]
> Portalı kullanarak bir PowerShell Iş akışı runbook 'unda yalnızca bir. ps1 dosyasını içeri aktarabileceğinizi unutmayın.

1. Azure portalında, Otomasyon hesabınızı açın.
2. Hub 'dan runbook 'ların listesini açmak için **runbook 'lar** ' ı seçin.
3. **Runbook Ekle** düğmesine tıklayın ve ardından **içe aktarın**.
4. İçeri aktarılacak dosyayı seçmek için **runbook dosyası** ' na tıklayın
5. **Ad** alanı etkinleştirilirse, bunu değiştirme seçeneğiniz vardır.  Runbook adı bir harfle başlamalıdır ve harfler, rakamlar, alt çizgiler ve kısa çizgiler içerebilir.
6. [Runbook türü](automation-runbook-types.md) otomatik olarak seçilir, ancak ilgili kısıtlamaları hesaba aldıktan sonra türü değiştirebilirsiniz.
7. Yeni runbook, Otomasyon hesabı için Runbook 'lar listesinde görüntülenir.
8. Çalıştırmak için [runbook 'u yayımlamanız](#publish-a-runbook) gerekir.

> [!NOTE]
> Bir grafik runbook 'unu veya bir grafik PowerShell iş akışı runbook 'unu içeri aktardıktan sonra, isterseniz diğer türüne dönüştürme seçeneğiniz vardır. Metinsel runbook 'a dönüştüremezsiniz.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell ile bir betik dosyasından bir runbook'u içeri aktarma

Bir betik dosyasını taslak PowerShell Iş akışı runbook 'u olarak içeri aktarmak için [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) cmdlet 'ini kullanabilirsiniz. Runbook zaten mevcutsa, *-zorlama* parametresini kullanmadığınız takdirde içeri aktarma başarısız olur.

Aşağıdaki örnek komutlarda bir betik dosyasının bir runbook 'a nasıl aktarılacağı gösterilmektedir.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Bir runbook'u test etme

Bir runbook 'u test ettiğinizde [taslak sürümü](#publish-a-runbook) yürütülür ve gerçekleştirdiği tüm işlemler tamamlanır. İş geçmişi oluşturulmaz, ancak [Çıkış](automation-runbook-output-and-messages.md#output-stream) ve [uyarı ve hata](automation-runbook-output-and-messages.md#message-streams) akışları test çıkış bölmesinde görüntülenir. [Ayrıntılı akışa](automation-runbook-output-and-messages.md#message-streams) iletiler yalnızca [$VerbosePreference değişkeni](automation-runbook-output-and-messages.md#preference-variables) devam olarak ayarlandıysa çıkış bölmesinde görüntülenir.

Taslak sürümü çalıştırılsa da runbook hala normal şekilde yürütülür ve ortamdaki kaynaklara karşı herhangi bir eylem gerçekleştirir. Bu nedenle, yalnızca üretim dışı kaynaklarda runbook 'ları test etmelisiniz.

Her [runbook türünü](automation-runbook-types.md) test etme yordamı aynıdır ve Azure Portal metinsel düzenleyici ile grafik Düzenleyicisi arasındaki sınamada fark yoktur.

1. Runbook 'un taslak sürümünü [metin düzenleyicisinde](automation-edit-textual-runbook.md) veya [grafik düzenleyicide](automation-graphical-authoring-intro.md)açın.
1. Sınama sayfasını açmak için **Test** düğmesine tıklayın.
1. Runbook 'ta parametreler varsa, test için kullanılacak değerleri sağlayabileceğiniz sol bölmede listelenir.
1. Bir [karma runbook çalışanında](automation-hybrid-runbook-worker.md)testi çalıştırmak Istiyorsanız, **çalıştırma ayarlarını** **karma çalışan** olarak değiştirin ve hedef grubun adını seçin.  Aksi takdirde, testi bulutta çalıştırmak için varsayılan **Azure** 'u saklayın.
1. Testi başlatmak için **Başlat** düğmesine tıklayın.
1. Runbook [PowerShell Iş akışı](automation-runbook-types.md#powershell-workflow-runbooks) veya [grafik](automation-runbook-types.md#graphical-runbooks)ise, çıkış bölmesinin altındaki düğmelerle test edilirken bunu durdurabilir veya askıya alabilirsiniz. Bir runbook'u askıya aldığınızda, askıya alınmadan önce geçerli etkinliği tamamlar. Runbook askıya alındığında, durdurabilir veya yeniden başlatabilirsiniz.
1. Çıkış bölmesinde runbook 'tan çıktıyı inceleyin.

## <a name="publish-a-runbook"></a>Runbook yayımlama

Yeni bir runbook oluşturduğunuzda veya içeri aktardığınızda, çalıştırmadan önce onu yayımlamanız gerekir.  Otomasyon 'daki her runbook 'ta taslak ve yayımlanmış bir sürüm bulunur. Yalnızca Yayımlanan sürüm çalıştırılabilir ve yalnızca Taslak sürüm düzenlenebilir. Yayımlanan sürüm Taslak sürümdeki herhangi bir değişiklikten etkilenmez. Taslak sürümü kullanılabilir hale getirilmeli hale geldiğinde, yayımlanan sürümün taslağını taslak sürümle üzerine yazan onu yayımlayın.

### <a name="azure-portal"></a>Azure portal

1. Runbook 'u Azure portal açın.
2. **Düzenle** düğmesine tıklayın.
3. **Yayımla** düğmesine ve ardından doğrulama iletisine **Evet** ' e tıklayın.

### <a name="powershell"></a>PowerShell

Windows PowerShell ile bir runbook yayımlamak için [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) cmdlet 'ini kullanabilirsiniz. Aşağıdaki örnek komutlarda örnek bir runbook 'un nasıl yayımlanacağı gösterilmektedir.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Sonraki adımlar

* Runbook ve PowerShell modül galerisinden nasıl avantaj sağlayabileceğinizi öğrenmek için bkz. [Azure Otomasyonu Için runbook ve modül galerileri](automation-runbook-gallery.md)
* Bir metinsel düzenleyiciyle PowerShell ve PowerShell Iş akışı runbook 'larını düzenleme hakkında daha fazla bilgi için bkz. [Azure Automation 'da metin runbook 'Larını düzenleme](automation-edit-textual-runbook.md)
* Grafik runbook 'u yazma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md)
