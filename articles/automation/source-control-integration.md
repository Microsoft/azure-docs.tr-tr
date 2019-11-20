---
title: Azure Otomasyonu 'nda kaynak denetimi tümleştirmesi
description: Bu makalede, Azure Otomasyonu 'nda GitHub ile kaynak denetimi tümleştirmesi açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 44ab9688471a87e6db3712cc61b8abb194d54ac3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886526"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Otomasyonu’nda kaynak denetimi tümleştirmesi

Kaynak denetimi, runbook 'larınızı GitHub veya Azure Repos kaynak denetimi deponuzdaki betiklerinizde güncel tutmanızı sağlar. Kaynak denetimi, takımınızla kolayca işbirliği yapmanızı, değişiklikleri izlemenizi ve Runbook 'larınızın önceki sürümlerine geri dönmeyi sağlar. Örneğin, kaynak denetimi, kaynak denetimindeki farklı dalları geliştirme, test veya üretim Otomasyon hesaplarınızla eşitlemenize olanak tanır. Bu, geliştirme ortamınızda test edilmiş kodu üretim Otomasyonu hesabınıza yükseltmeyi kolaylaştırır. Otomasyon ile kaynak denetimi tümleştirmesi, kaynak denetimi deponuzdan tek yönlü eşitlemeyi destekler.

Azure Otomasyonu üç tür kaynak denetimi destekler:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Ön koşullar

* Kaynak denetimi deposu (GitHub veya Azure Repos)
* [Farklı Çalıştır hesabı](manage-runas-account.md)
* Otomasyon hesabınızda [en son Azure modüllerine](automation-update-azure-modules.md) sahip olduğunuzdan emin olun

> [!NOTE]
> Kaynak denetimi eşitleme işleri, kullanıcılar Otomasyon hesabı altında çalışır ve diğer otomasyon işleriyle aynı hızda faturalandırılır.

## <a name="configure-source-control---azure-portal"></a>Kaynak denetimini Yapılandırma-Azure portal

Otomasyon hesabınızda, **kaynak denetimi** ' ni seçin ve **+ Ekle** ' ye tıklayın.

![Kaynak denetimi seçin](./media/source-control-integration/select-source-control.png)

**Kaynak denetim türünü**seçin, **kimlik doğrula**' ya tıklayın. Bir tarayıcı penceresi açılır ve oturum açmanız istenir, kimlik doğrulamasını tamamlamaya yönelik istemleri takip edebilirsiniz.

**Kaynak denetimi Özeti** sayfasında, bilgileri doldurun ve **Kaydet**' e tıklayın. Aşağıdaki tabloda kullanılabilir alanların açıklaması gösterilmektedir.

|Özellik  |Açıklama  |
|---------|---------|
|Kaynak Denetim adı     | Kaynak denetimi için kolay bir ad. *Bu ad yalnızca harf ve rakam içermelidir.*        |
|Kaynak Denetim türü     | Kaynak denetim kaynağının türü. Kullanılabilen seçenekler:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
|Depo     | Deponun veya projenin adı. İlk 200 depo döndürülür. Bir depoyu aramak için alana adı yazın ve **GitHub 'Da ara**' yı tıklatın.|
|Şube     | Kaynak dosyaları çekme dalı. TFVC kaynak denetimi türü için dal hedefleme kullanılamıyor.          |
|Klasör yolu     | Eşitlenecek runbook 'ları içeren klasör. Örnek:/runbook 'lar </br>*Yalnızca belirtilen klasördeki runbook 'lar eşitlenir. Özyineleme desteklenmiyor.*        |
|Otomatik eşitleme<sup>1</sup>     | Kaynak denetim deposunda bir kayıt yapıldığında otomatik eşitlemeyi açar veya kapatır         |
|Runbook 'U Yayımla     | **Açık**olarak ayarlandıysa, runbook 'lar kaynak denetiminden eşitlendikten sonra otomatik olarak yayımlanır.         |
|Açıklama     | Ek ayrıntılar sağlayan bir metin alanı        |

<sup>1</sup> Azure Repos ile kaynak denetimi tümleştirmesini yapılandırırken otomatik eşitlemeyi etkinleştirmek Için bir proje yöneticisi olmanız gerekir.

![Kaynak denetimi Özeti](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Kaynak denetimi deponuz için oturum açma bilgileriniz Azure portal oturum açmından farklı olabilir. Kaynak denetimini yapılandırırken kaynak denetimi deponuzun doğru hesabıyla oturum açtığınızdan emin olun. Şüpheli bir sorun varsa, tarayıcınızda yeni bir sekme açın ve visualstudio.com veya github.com adresinden oturumunuzu açıp kaynak denetimini yeniden bağlamayı deneyin.

## <a name="configure-source-control---powershell"></a>Kaynak denetimini yapılandırma-PowerShell

Azure Otomasyonu 'nda kaynak denetimi yapılandırmak için PowerShell de kullanabilirsiniz. Kaynak denetimini PowerShell cmdlet 'leriyle yapılandırmak için bir kişisel erişim belirteci (PAT) gereklidir. Kaynak denetimi bağlantısını oluşturmak için [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) komutunu kullanın. Cmdlet, kişisel erişim belirtecinin güvenli bir dizesini gerektirir, güvenli bir dize oluşturmayı öğrenmek için bkz. [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Kişisel erişim belirteci izinleri

Kaynak denetimi, kişisel erişim belirteçleri için bazı minimum izinleri gerektirir. Aşağıdaki tablolar, GitHub ve Azure Repos için gereken en düşük izinleri içerir.

#### <a name="github"></a>GitHub

GitHub 'da kişisel erişim belirteci oluşturma hakkında daha fazla bilgi için, [komut satırı için kişisel erişim belirteci oluşturma](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)sayfasını ziyaret edin.

|Kapsam  |Açıklama  |
|---------|---------|
|**depo**     |         |
|Depo: durum     | Erişim yapma durumu         |
|repo_deployment      | Erişim dağıtım durumu         |
|public_repo     | Genel depolara erişin         |
|**Yönetici: repo_hook**     |         |
|yazma: repo_hook     | Depo kancalarını Yaz         |
|okuma: repo_hook|Depo kancalarını okuma|

#### <a name="azure-repos"></a>Azure Repos

Azure Repos bir kişisel erişim belirteci oluşturma hakkında daha fazla bilgi için, [kişisel erişim belirteçleriyle erişimi kimlik doğrulaması](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)' nı ziyaret edin.

|Kapsam  |
|---------|
|Kod (okuma)     |
|Proje ve takım (okuma)|
|Kimlik (okuma)      |
|Kullanıcı profili (okuma)     |
|İş öğeleri (okuma)    |
|Hizmet bağlantıları (okuma, sorgulama ve yönetme)<sup>1</sup>    |

<sup>1</sup> hizmet bağlantıları izni yalnızca, oto eşitlemesini etkinleştirdiyseniz gereklidir.

## <a name="syncing"></a>Eşitlenmiyor

**Kaynak denetimi** sayfasındaki tablodan kaynağı seçin. Eşitleme işlemini başlatmak için **Eşitlemeyi Başlat** ' a tıklayın.

**Eşitleme işleri** sekmesine tıklayarak geçerli eşitleme işinin durumunu veya önceki olanları görüntüleyebilirsiniz. **Kaynak denetimi** açılan çubuğunda bir kaynak denetimi seçin.

![Eşitleme durumu](./media/source-control-integration/sync-status.png)

Bir işi tıklatmak, iş çıktısını görüntülemenize izin verir. Aşağıdaki örnek, bir kaynak denetimi eşitleme işinin çıktıdır.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

**Kaynak denetimi eşitleme Işi Özeti** sayfasında **Tüm Günlükler** seçilerek ek günlüğe kaydetme kullanılabilir. Bu ek günlük girişleri, kaynak denetimi kullanılırken oluşabilecek sorunları gidermenize yardımcı olabilir.

## <a name="disconnecting-source-control"></a>Kaynak denetiminin bağlantısı kesiliyor

Kaynak denetim deposundan bağlantıyı kesmek için, Otomasyon hesabınızdaki **Hesap ayarları** altında **kaynak denetimi** ' ni açın.

Kaldırmak istediğiniz kaynak denetimini seçin. **Kaynak denetimi Özeti** sayfasında **Sil**' e tıklayın.

## <a name="encoding"></a>Encoding

Kaynak denetim deponuzda farklı düzenleyicilerle runbook 'ları düzenleyen birden fazla kişi varsa, kodlama sorunlarını çözmek için bir şansınız vardır. Bu durum runbook 'unuzda yanlış karakterlere yol açabilir. Bunun hakkında daha fazla bilgi edinmek için bkz. [kodlama sorunlarının yaygın nedenleri](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Erişim belirteci güncelleştiriliyor

Şu anda, kaynak denetimindeki erişim belirtecini portaldan güncelleştirmenin bir yolu yoktur. Kişisel erişim belirteciniz zaman aşımına uğradı veya iptal edildikten sonra, aşağıdaki yollarla kaynak denetimini yeni bir erişim belirteciyle güncelleştirebilirsiniz:

* , [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)aracılığıyla.
* [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet 'ini kullanarak.

## <a name="next-steps"></a>Sonraki adımlar

Runbook türleri, avantajları ve sınırlamaları hakkında daha fazla bilgi için, bkz. [Azure Automation runbook türleri](automation-runbook-types.md)
