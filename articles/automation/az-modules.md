---
title: Azure Otomasyonu 'nda az modüller kullanma
description: Bu makalede, Azure Otomasyonu 'nda az modüller kullanılarak bilgiler sağlanmaktadır
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f81c0affb78d5944b8ba910cccfa0be655f1a6f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097945"
---
# <a name="az-module-support-in-azure-automation"></a>Azure Otomasyonu 'nda az Module desteği

Azure Otomasyonu, runbook 'larınızda [Azure PowerShell az modülünü](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) kullanma yeteneğini destekler. Az Module, yeni veya mevcut Otomasyon hesaplarında otomatik olarak içeri aktarılmaz. Bu makalede, az modüllerin Azure Otomasyonu ile nasıl kullanılacağı açıklanır.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Azure Otomasyonu 'nda az Module kullanırken dikkate alınması gereken pek çok şey vardır. Runbook 'lar ve modüller, Otomasyon hesabınızdaki daha üst düzey çözümler tarafından kullanılabilir. Runbook 'ları veya modülleri yükseltmek, runbook 'larınızla ilgili sorunlara neden olabilir. Yeni `Az` modülleri içeri aktarmadan önce, tüm runbook 'ları ve çözümleri ayrı bir Otomasyon hesabında dikkatle sınamanız gerekir. Modüllerle ilgili değişiklikler, [Başlat/Durdur](automation-solution-vm-management.md) çözümünü olumsuz yönde alabilir. Herhangi bir çözüm içeren Otomasyon hesaplarında modüller ve Runbook 'ların değiştirmemeleri önerilir. Bu davranış az modüllere özgü değildir. Otomasyon hesabınızda herhangi bir değişikliğe giriş yaparken bu davranış göz önünde bulundurulmalıdır.

Bir `Az` modülün Otomasyon hesabınıza aktarılması runbook 'ların kullandığı PowerShell oturumunda modülü otomatik olarak içeri aktarmaz. Modüller aşağıdaki durumlarda PowerShell oturumuna aktarılır:

* Bir modülden bir cmdlet bir runbook 'tan çağrıldığında
* Bir runbook onu `Import-Module` cmdlet ile açıkça içeri aktardığında
* Modüle bağlı başka bir modül bir PowerShell oturumuna içeri aktarılmışsa

> [!IMPORTANT]
> Bir Otomasyon hesabındaki runbook 'ların yalnızca runbook 'lar tarafından kullanılan PowerShell oturumlarına içeri aktarıldığından `Az` veya `AzureRM` modüllerden her ikisi de olmadığından emin olmak önemlidir. Bir runbook 'tan önce `AzureRM` içeri aktarıldıysa runbook tamamlanır, ancak [get_SerializationSettings yöntemine başvuruda](troubleshoot/runbooks.md#get-serializationsettings) bulunan bir hata iş akışlarında görünür ve cmdlet 'ler düzgün şekilde yürütülmeyebilir. `Az` İçeri aktarırsanız `AzureRM` ve sonra `Az` runbook hala tamamlanacaktır, ancak iş akışlarında her ikisini de `Az` belirten ve `AzureRM` aynı oturumda veya aynı runbook 'ta kullanılan bir hata görürsünüz.

## <a name="migrating-to-az-modules"></a>Az modüllere geçme

Bir test Otomasyonu hesabındaki Azurerd modülleri yerine az modüller kullanarak geçişi test etmeniz önerilir. Otomasyon hesabı oluşturulduktan sonra geçişinizin sorunsuz şekilde çalışmasını sağlamak için aşağıdaki adımları kullanabilirsiniz:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Azurerd modülleri kullanan tüm runbook 'u durdurma ve zamanlamayı kaldırma

`AzureRM` Cmdlet 'leri kullanan mevcut runbook 'ları çalıştırmazsanız emin olmak için, `AzureRM` modüller kullanan tüm runbook 'ları durdurup zamanlamayı durdurmanız gerekir. Hangi zamanlamaların mevcut olduğunu ve hangi zamanlamaların kaldırılması gerektiğini aşağıdaki örnek çalıştırılarak görebilirsiniz:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Gerekirse runbook 'larınız için bu planı ileride yeniden zamanlayamazsınız emin olmak için her bir zamanlamayı ayrı olarak gözden geçirmeniz önemlidir.

### <a name="import-the-az-modules"></a>Az modülleri içeri aktar

Yalnızca runbook 'larınız için gereken az modülleri içeri aktarın. İçeri aktarılacak tüm `Az.*` modülleri `Az` içerdiğinden, toplama modülünü içeri aktarmayın. Bu kılavuz tüm modüller için aynıdır.

[Az. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modülü, diğer `Az.*` modüller için bir bağımlılıkdır. Bu nedenle, diğer modülleri içeri aktarmadan önce Bu modülün Otomasyon hesabınıza aktarılması gerekir.

Otomasyon hesabınızdan, **paylaşılan kaynaklar**altında **modüller** ' i seçin. **Galeriye gözatamıyorum** sayfasını açmak Için **gözatamıyorum** ' ı tıklatın.  Arama çubuğuna modül adını (örneğin `Az.Accounts`,) girin. PowerShell modülü sayfasında, modülü Otomasyon hesabınıza aktarmak için **Içeri aktar** ' a tıklayın.

![Otomasyon hesabından modülleri içeri aktar](media/az-modules/import-module.png)

Bu içeri aktarma işlemi, modül aranarak [PowerShell Galerisi](https://www.powershellgallery.com) aracılığıyla da yapılabilir. Modülü bulduktan sonra seçin ve **Azure Otomasyonu** sekmesinde **Azure Otomasyonu 'na dağıt**' a tıklayın.

![Modülleri doğrudan Galeriden içeri aktar](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Runbook 'larınızı test edin

`Az` Modüller Otomasyon hesabınıza alındıktan sonra, artık runbook 'larınızı daha az modülünü kullanacak şekilde düzenleyebilirsiniz. Cmdlet 'lerinin çoğu, `AzureRM` öğesinin `Az`dışında aynı ada sahiptir. Bu işlemi takip eden modüllerin bir listesi için bkz. [özel durumlar listesi](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Runbook 'unuzu yeni cmdlet 'leri `Enable-AzureRMAlias -Scope Process` kullanacak şekilde değiştirmeden önce runbook 'larınızı test etmenin bir yolu, bir runbook 'un başlangıcında kullanmaktır. Bunu runbook 'a ekleyerek, runbook 'niz değişiklik yapılmadan çalıştırılabilir.

## <a name="after-migration-details"></a>Geçiş ayrıntılarından sonra

Geçiş tamamlandıktan sonra, hesapta daha fazla modül kullanarak `AzureRM` runbook 'ları başlatmayın. Bu hesapta `AzureRM` modülleri içeri veya dışarı aktarmayın de önerilir. Bu andan itibaren, bu hesabı ' a geçirilmiş ve `Az`yalnızca modüllerle `Az` çalışacak şekilde değerlendirin. Yeni bir Otomasyon hesabı oluşturulduğunda mevcut `AzureRM` modüller yine de yüklenir ve öğretici runbook 'lar yine de cmdlet 'ler ile `AzureRM` yazılacaktır. Bu runbook 'lar çalıştırılmamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Az modüller kullanma hakkında daha fazla bilgi edinmek için bkz. [az Module ile çalışmaya](/powershell/azure/get-started-azureps?view=azps-1.1.0)başlama.
