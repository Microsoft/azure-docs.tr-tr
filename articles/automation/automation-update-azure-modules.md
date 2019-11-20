---
title: Azure Otomasyonu 'nda Azure modüllerini güncelleştirme
description: Bu makalede, Azure Automation 'da varsayılan olarak sunulan ortak Azure PowerShell modüllerini nasıl güncelleştirebileceğinizi açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 23475fb77210eeea0568bb996529c81458db9c6c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382772"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme

Otomasyon hesabınızda Azure modüllerini güncelleştirmek için açık kaynak olarak kullanılabilen [Azure modüllerini Güncelleştir runbook 'unu](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)kullanmanız gerekir. Azure modüllerinizi güncelleştirmek için **Update-AutomationAzureModulesForAccount** runbook 'unu kullanmaya başlamak için GitHub 'daki [Azure modüllerini Güncelleştir runbook deposundan](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) indirin. Daha sonra Otomasyon hesabınıza aktarabilir veya bir betik olarak çalıştırabilirsiniz. Otomasyon hesabınızda bir runbook 'un nasıl içeri aktarılacağını öğrenmek için bkz. [runbook 'U Içeri aktarma](manage-runbooks.md#import-a-runbook).

En yaygın Azurerd PowerShell modülleri, her Otomasyon hesabında varsayılan olarak sağlanır. Azure ekibi, Azure modüllerini düzenli olarak güncelleştirir. bu nedenle, Otomasyon hesaplarınızdaki modülleri güncelleştirmek için [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook 'unu kullanmak isteyeceksiniz.

Modüller ürün grubu tarafından düzenli olarak güncelleştirildiğinden, değişiklikler dahil edilen cmdlet 'ler ile gerçekleştirilebilir. Bu eylem, runbook 'larınızı bir parametreyi yeniden adlandırma ya da bir cmdlet 'i tamamen kullanımdan kaldırma gibi değişiklik türüne bağlı olarak olumsuz etkileyebilir.

Runbook 'larınızı ve otomatikleştirdikleri işlemleri etkilememek için devam etmeden önce test edin ve doğrulayın. Bu amaçla tasarlanan adanmış bir Otomasyon hesabınız yoksa, runbook 'larınızın geliştirilmesi sırasında birçok farklı senaryoyu test edebilmeniz için bir tane oluşturmayı düşünün. Bu test, PowerShell modüllerini güncelleştirme gibi yinelemeli değişiklikler içermelidir.

Betikleri yerel olarak geliştirirseniz, aynı sonuçları elde etdiğinizden emin olmak için test edilirken Otomasyon hesabınızda aynı modül sürümlerinin yerel olarak olması önerilir. Sonuçlar doğrulandıktan sonra ve gerekli değişiklikleri uyguladıysanız, değişiklikleri üretime taşıyabilirsiniz.

> [!NOTE]
> Yeni bir Otomasyon hesabı en son modülleri içermeyebilir.

> [!NOTE]
> Genel modülleri silemeyeceksiniz-Automation 'ın kullanıma hazır olarak sağladığı modüller.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Azure modüllerinizi güncelleştirmek için bu işlemi kullanırken dikkate alınması gereken bazı noktalar aşağıda verilmiştir:

* Bu runbook, **Azure** ve **Azurerd** modüllerinin varsayılan olarak güncelleştirilmesini destekler. Bu runbook **az** modüllerin güncelleştirilmesini de destekler. Bu runbook ile modülleri güncelleştirme `Az` hakkında daha fazla bilgi için [Azure modüllerini Güncelleştir runbook Benioku dosyasını](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) gözden geçirin. Otomasyon hesabınızda `Az` modüller kullanılırken dikkate almanız gereken ek önemli etmenler vardır. daha fazla bilgi edinmek için bkz. [Otomasyon hesabınızda az modüller kullanma](az-modules.md).

* Bu runbook 'u başlatmadan önce Otomasyon hesabınızda bir [Azure farklı çalıştır hesabı kimlik bilgisi](manage-runas-account.md) oluşturulduğundan emin olun.

* Bu kodu, runbook yerine normal bir PowerShell betiği olarak kullanabilirsiniz: önce yalnızca [Connect-azurermaccount](/powershell/module/azurerm.profile/connect-azurermaccount) komutunu kullanarak Azure 'da oturum açın, sonra betiğe geçiş `-Login $false` yapın.

* Bu runbook 'u bağımsız bulutlarında kullanmak için, doğru ortamı `AzureRmEnvironment` runbook 'a geçirmek üzere parametresini kullanın.  Kabul edilebilir değerler **Azurecsesli**, **AzureChinaCloud**, **AzureGermanCloud**ve **AzureUSGovernment**. Bu değerler, kullanılarak `Get-AzureRmEnvironment | select Name`alınabilir. Bu parametreye bir değer geçirmezseniz, runbook varsayılan olarak Azure genel bulutu **Azurecyüksek** ' i

* PowerShell Galerisi en son kullanılabilir yerine belirli bir Azure PowerShell modülü sürümünü kullanmak istiyorsanız, bu sürümleri **Update-automationazuremodulesforaccount** runbook `ModuleVersionOverrides` 'unun isteğe bağlı parametresine geçirin. Örnekler için bkz [. Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. `ModuleVersionOverrides` Parametresinde bahsedilen Azure PowerShell modüller, PowerShell Galerisi en son modül sürümleriyle güncellenir. `ModuleVersionOverrides` Parametreye hiçbir şey geçirirseniz, tüm modüller PowerShell Galerisi en son modül sürümleriyle güncellenir. Bu davranış, **Azure modüllerini Güncelleştir** düğmesi ile aynıdır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için açık kaynak [güncelleştirme Azure modülleri runbook 'unu](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) ziyaret edin.
