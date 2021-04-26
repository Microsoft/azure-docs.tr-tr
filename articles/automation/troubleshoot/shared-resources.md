---
title: Azure Otomasyonu paylaşılan kaynak sorunlarını giderme
description: Bu makalede, Azure Otomasyonu paylaşılan kaynaklarıyla ilgili sorunların nasıl giderileceği ve çözüleceği açıklanır.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b497b0a8f34b4310e3f11beed982c4453fc79159
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830836"
---
# <a name="troubleshoot-shared-resource-issues"></a>Paylaşılan kaynak sorunlarını giderme

Bu makalede, Azure Otomasyonu 'nda [paylaşılan kaynaklar](../automation-intro.md#shared-resources) kullandığınızda ortaya çıkabilecek sorunlar ele alınmaktadır.

## <a name="modules"></a>Modül

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Senaryo: bir modül içeri aktarma sırasında takıldı

#### <a name="issue"></a>Sorun

Azure Otomasyonu modüllerinizi içeri aktarırken veya güncelleştirirken bir modül *Içeri aktarma* durumunda takılmış olur.

#### <a name="cause"></a>Nedeni

PowerShell modüllerini içeri aktarma karmaşık, çok adımlı bir işlem olduğundan, bir modül doğru şekilde içeri aktarılmayabilir ve geçici bir durumda kalmış olabilir. İçeri aktarma işlemi hakkında daha fazla bilgi edinmek için bkz. [PowerShell modülünü Içeri aktarma](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) cmdlet 'ini kullanarak takılı olan modülü kaldırmanız gerekir. Daha sonra modülün içeri aktarılmasını yeniden deneyebilirsiniz.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Senaryo: bir güncelleştirme denemesinden sonra Azurerd modülleri içeri aktarma sırasında takılmış

#### <a name="issue"></a>Sorun

Azurere modüllerinizi güncelleştirmeden sonra aşağıdaki iletinin bulunduğu bir başlık hesabınızda kalır:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Nedeni

Bir Otomasyon hesabındaki Azurermmodules güncelleştirilirken oluşan bilinen bir sorun vardır. Özellikle, modüller 0 ile başlayan sayısal ada sahip bir kaynak grubunda yer alıyorsa sorun oluşur.

#### <a name="resolution"></a>Çözüm

Otomasyon hesabınızda Azurere modüllerinizi güncelleştirmek için, hesabın alfasayısal bir ada sahip bir kaynak grubunda olması gerekir. 0 ile başlayan sayısal adlara sahip kaynak grupları Şu anda Azurere modüllerini güncelleştiremiyor.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Senaryo: modül içeri aktarılmazsa veya cmdlet 'ler içeri aktarıldıktan sonra yürütülemez

#### <a name="issue"></a>Sorun

Modül içeri aktarılamıyor veya başarıyla içeri aktarmaz, ancak hiçbir cmdlet ayıklanamaz.

#### <a name="cause"></a>Nedeni

Modülün Azure Otomasyonu 'na başarıyla aktarılamayan bazı yaygın nedenler şunlardır:

* Yapı, Otomasyon gereksinimlerinin yapısıyla eşleşmez.
* Modül, Otomasyon hesabınıza dağıtılmamış başka bir modüle bağımlıdır.
* Modülün içindeki bağımlılıkları eksik.
* [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) cmdlet 'i modülü yüklemek için kullanılıyor ve tam depolama yolu sağlamadınız veya genel olarak ERIŞILEBILEN bir URL kullanarak modülü yüklemediniz.

#### <a name="resolution"></a>Çözüm

Sorunu onarmak için aşağıdaki çözümlerden herhangi birini kullanın:

* Modülün şu biçimde olduğundan emin olun: ModuleName.zip-> ModuleName veya sürüm numarası-> (ModuleName. psm1, ModuleName.psd1).
* **. Psd1** dosyasını açın ve modülün herhangi bir bağımlılığı olup olmadığını görün. Varsa, bu modülleri Otomasyon hesabına yükleyin.
* Başvurulmuş tüm **. dll** dosyalarının modül klasöründe bulunduğundan emin olun.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Senaryo: modüller güncelleştirilirken Update-AzureModule.ps1 askıya alınır

#### <a name="issue"></a>Sorun

Azure modüllerinizi güncelleştirmek için [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 'u kullanırken modül güncelleştirme işlemi askıya alınır.

#### <a name="cause"></a>Nedeni

Bu runbook için, aynı anda kaç modülün güncelleştirildiğini belirleme varsayılan ayarı 10 ' dur. Aynı anda çok fazla modül güncelleştirilirken güncelleştirme işlemi hatalara açıktır.

#### <a name="resolution"></a>Çözüm

Tüm Azurerd veya az modüllerin aynı Otomasyon hesabında gerekli olduğu yaygın değildir. Yalnızca ihtiyacınız olan belirli modülleri içeri aktarmanız gerekir.

> [!NOTE]
> `Az.Automation` `AzureRM.Automation` Dahil edilen tüm modülleri içeri aktaran tüm veya modülünü içeri aktarmaktan kaçının.

Güncelleştirme işlemi askıya alıyorsa, `SimultaneousModuleImportJobCount` **Update-AzureModules.ps1** betiğine parametresini ekleyin ve varsayılan değer olan 10 ' dan daha düşük bir değer sağlayın. Bu mantığı uygularsanız, 3 veya 5 değeri ile başlatmayı deneyin. `SimultaneousModuleImportJobCount` , Azure modüllerini güncelleştirmek için kullanılan **Update-AutomationAzureModulesForAccount** sistem runbook 'unun bir parametresidir. Bu ayarlamayı yaparsanız, güncelleştirme işlemi daha fazla çalışır, ancak tamamlanması daha iyi olur. Aşağıdaki örnek, parametresini ve Runbook 'a nereye yerleştirileceğini gösterir:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Farklı Çalıştır hesapları

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Senaryo: bir farklı çalıştır hesabı oluşturamaz veya güncelleştiremezsiniz

#### <a name="issue"></a>Sorun

Farklı Çalıştır hesabı oluşturmayı veya güncelleştirmeyi denediğinizde aşağıdakine benzer bir hata alırsınız:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Nedeni

Farklı Çalıştır hesabı oluşturmak veya güncelleştirmek için gerekli izinlere sahip değilsiniz veya kaynak bir kaynak grubu düzeyinde kilitli.

#### <a name="resolution"></a>Çözüm

Farklı Çalıştır hesabı oluşturmak veya güncelleştirmek için, farklı çalıştır hesabı tarafından kullanılan çeşitli kaynaklara uygun [izinlere](../automation-security-overview.md#permissions) sahip olmanız gerekir.

Sorun bir kilit nedeniyle, kilidin kaldırılabildiğini doğrulayın. Ardından Azure portal kilitlenen kaynağa gidin, kilidi sağ tıklatın ve **Sil**' i seçin.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Senaryo: bir runbook yürütürken "iplpapi.dll ' adlı DLL 'de ' Getperadapterınfo ' adlı bir giriş noktası bulunamadı" hatasını alıyorsunuz

#### <a name="issue"></a>Sorun

Bir runbook çalıştırırken aşağıdaki özel durumu alırsınız:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Nedeni

Bu hata büyük olasılıkla yanlış yapılandırılmış bir [Farklı Çalıştır hesabı](../automation-security-overview.md)nedeniyle oluşur.

#### <a name="resolution"></a>Çözüm

Farklı Çalıştır hesabınızın doğru yapılandırıldığından emin olun. Daha sonra Azure ile kimlik doğrulamak için Runbook 'unuzla ilgili doğru koda sahip olduğunuzu doğrulayın. Aşağıdaki örnekte, bir farklı çalıştır hesabı kullanarak bir runbook 'ta Azure 'da kimlik doğrulaması yapmak için bir kod parçacığı gösterilmektedir.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makale sorununuzu gidermezse, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* İle bağlanın [@AzureSupport](https://twitter.com/azuresupport) . Bu, Azure Community 'yi doğru kaynaklara bağlamaya yönelik resmi Microsoft Azure hesabıdır: yanıtlar, destek ve uzmanlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
