---
title: Azure Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme
description: Bu makalede, Azure Automation 'da varsayılan olarak sunulan ortak Azure PowerShell modüllerinin nasıl güncelleşeceklerini açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c1632da35864fc6822b385adac06d7f124aea061
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830421"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell modüllerini güncelleştirme

En yaygın PowerShell modülleri, her Otomasyon hesabında varsayılan olarak sağlanır. Bkz. [varsayılan modüller](shared-resources/modules.md#default-modules). Azure ekibi Azure modüllerini düzenli olarak güncelleştirdiği için, dahil edilen cmdlet 'ler ile değişiklikler oluşabilir. Bu değişiklikler örneğin, bir parametreyi yeniden adlandırma veya bir cmdlet 'i kullanımdan kaldırma gibi runbook 'larınızı olumsuz etkileyebilir. 

> [!NOTE]
> Automation 'ın kullanıma hazır olarak sağladığı modüller olan genel modülleri silemezsiniz.

## <a name="set-up-an-automation-account"></a>Otomasyon hesabı ayarlama

Runbook 'larınızı ve otomatikleştiredikleri işlemleri etkilememek için, güncelleştirme yaparken test ve doğrulama yaptığınızdan emin olun. Bu amaçla tasarlanan adanmış bir Otomasyon hesabınız yoksa, runbook 'larınızın geliştirilmesi sırasında birçok farklı senaryoyu test edebilmeniz için bir tane oluşturmayı düşünün. Bu test, PowerShell modüllerini güncelleştirme gibi yinelemeli değişiklikler içermelidir.

Otomasyon hesabınızda bir [Azure farklı çalıştır hesabı](automation-security-overview.md#run-as-accounts) oluşturulduğundan emin olun.

Betikleri yerel olarak geliştirirseniz, aynı sonuçları aldığınızdan emin olmak için test edilirken Otomasyon hesabınızda aynı modül sürümlerinin yerel olarak olması önerilir. Sonuçlar doğrulandıktan sonra ve gerekli değişiklikleri uyguladıysanız, değişiklikleri üretime taşıyabilirsiniz.

> [!NOTE]
> Yeni bir Otomasyon hesabı en son modülleri içermeyebilir.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Güncelleştirmeler için kullanılacak bir runbook alın

Otomasyon hesabınızda Azure modüllerini güncelleştirmek için, açık kaynak olarak kullanılabilen [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook 'unu kullanmanız gerekir. Azure modüllerinizi güncelleştirmek üzere bu runbook 'u kullanmaya başlamak için GitHub deposundan indirin. Daha sonra Otomasyon hesabınıza aktarabilir veya bir betik olarak çalıştırabilirsiniz. Otomasyon hesabınızda bir runbook 'un nasıl içeri aktarılacağını öğrenmek için bkz. [runbook 'U Içeri aktarma](manage-runbooks.md#import-a-runbook).

**Update-AutomationAzureModulesForAccount** runbook, varsayılan olarak Azure, Azurerd ve az modules güncelleştirilmesini destekler. Bu runbook ile az. Automation modüllerini güncelleştirme hakkında daha fazla bilgi için [Azure modüllerini Güncelleştir runbook Benioku dosyasını](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) gözden geçirin. Otomasyon hesabınızda az modüller kullanırken dikkate almanız gereken ek önemli etmenler vardır. Daha fazla bilgi için bkz. [Azure Automation 'da modülleri yönetme](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Runbook kodunu güncelleştirme 'yi düzenli bir PowerShell betiği olarak kullanın

Runbook kodunu runbook yerine normal bir PowerShell betiği olarak kullanabilirsiniz. Bunu yapmak için önce [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure 'da oturum açın, sonra `-Login $false` betiğe geçirin.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Bağımsız bulutlarda runbook güncelleştirme 'yi kullanma

Bu runbook 'u bağımsız bulutlarında kullanmak için, `AzEnvironment` doğru ortamı runbook 'a geçirmek üzere parametresini kullanın. Kabul edilebilir değerler Azurecyüksek (Azure genel bulutu), AzureChinaCloud, AzureGermanCloud ve AzureUSGovernment. Bu değerler kullanılarak alınabilir `Get-AzEnvironment | select Name` . Bu cmdlet 'e bir değer geçirmezseniz, runbook varsayılan olarak Azurecum olur.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Belirli bir modül sürümünü güncelleştirmek için güncelleştirme runbook 'unu kullanın

PowerShell Galerisi kullanılabilir en son modül yerine belirli bir Azure PowerShell modülü sürümünü kullanmak istiyorsanız, bu sürümleri `ModuleVersionOverrides` **Update-AutomationAzureModulesForAccount** runbook 'unun isteğe bağlı parametresine geçirin. Örnekler için bkz. runbook  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) . Parametresinde bahsedilen Azure PowerShell modüller, `ModuleVersionOverrides` PowerShell Galerisi en son modül sürümleriyle güncellenir. Parametreye hiçbir şey geçirirseniz `ModuleVersionOverrides` , tüm modüller PowerShell Galerisi en son modül sürümleriyle güncellenir. Bu davranış, Azure portal **Azure modüllerini Güncelleştir** düğmesi için aynıdır.

## <a name="next-steps"></a>Sonraki adımlar

* Modül kullanma hakkında ayrıntılı bilgi için bkz. [Azure Automation 'da modülleri yönetme](shared-resources/modules.md).
* Runbook 'u güncelleştirme hakkında daha fazla bilgi için bkz. [Azure modules runbook 'U güncelleştirme](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
