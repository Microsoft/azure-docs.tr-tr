---
title: Azure Otomasyonu 'nda kaynak denetimi tümleştirmesini kullanma
description: Bu makalede, Azure Otomasyonu kaynak denetimini diğer depolarla nasıl eşitleyeceğiniz açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d94da9792d40a389e3981163e565d85d82a9cdc9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831249"
---
# <a name="use-source-control-integration"></a>Kaynak denetimi tümleştirmesini kullanma

 Azure Otomasyonu 'nda kaynak denetimi tümleştirmesi, kaynak denetimi deponuzdan tek yönlü eşitlemeyi destekler. Kaynak denetimi, GitHub veya Azure Repos kaynak denetimi deponuzdaki betiklerle Otomasyon hesabınızda runbook 'larınızı güncel tutmanızı sağlar. Bu özellik, geliştirme ortamınızda test edilmiş kodu Üretim otomasyon hesabınıza yükseltmeyi kolaylaştırır.

 Kaynak denetimi tümleştirmesi, ekibinizle kolayca işbirliği yapmanıza, değişiklikleri izlemenize ve Runbook 'larınızın önceki sürümlerine geri döndürmenize olanak tanır. Örneğin, kaynak denetimi, kaynak denetimindeki farklı dalları geliştirme, test ve üretim Otomasyon hesaplarınızla eşitlemenize olanak tanır.

## <a name="source-control-types"></a>Kaynak Denetim türleri

Azure Otomasyonu üç tür kaynak denetimi destekler:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Önkoşullar

* Kaynak denetimi deposu (GitHub veya Azure Repos)
* [Farklı Çalıştır hesabı](automation-security-overview.md#run-as-accounts)
* [ `AzureRM.Profile` Modülün](/powershell/module/azurerm.profile/) Otomasyon hesabınıza aktarılması gerekir. Eşdeğer az Module ( `Az.Accounts` ) Otomasyon kaynak denetimi ile çalışmayacaktır.

> [!NOTE]
> Kaynak denetimi eşitleme işleri, kullanıcının Otomasyon hesabı altında çalışır ve diğer otomasyon işleriyle aynı hızda faturalandırılır.

## <a name="configure-source-control"></a>Kaynak denetimini yapılandırma

Bu bölümde Otomasyon hesabınız için kaynak denetiminin nasıl yapılandırılacağı açıklanır. Azure portal veya PowerShell kullanabilirsiniz.

### <a name="configure-source-control-in-azure-portal"></a>Azure portal kaynak denetimini yapılandırma

Azure portal kullanarak kaynak denetimini yapılandırmak için bu yordamı kullanın.

1. Otomasyon hesabınızda, **kaynak denetimi** ' ni seçin ve **Ekle**' ye tıklayın.

    ![Kaynak denetimi seçin](./media/source-control-integration/select-source-control.png)

2. **Kaynak denetim türünü** seçin ve ardından **kimlik doğrula**' ya tıklayın.

3. Bir tarayıcı penceresi açılır ve oturum açmanızı ister. Kimlik doğrulamasını tamamlamaya yönelik istemleri izleyin.

4. Kaynak denetimi Özeti sayfasında, alanları, aşağıda tanımlanan kaynak denetimi özelliklerini dolduracak şekilde kullanın. İşiniz bittiğinde **Kaydet** ' e tıklayın.

    |Özellik  |Açıklama  |
    |---------|---------|
    |Kaynak Denetim adı     | Kaynak denetimi için kolay bir ad. Bu ad yalnızca harf ve rakam içermelidir.        |
    |Kaynak Denetim türü     | Kaynak denetimi mekanizmasının türü. Kullanılabilen seçenekler:</br> * GitHub</br>* Azure Repos (git)</br> * Azure Repos (TFVC)        |
    |Depo     | Deponun veya projenin adı. İlk 200 depo alınır. Bir depoyu aramak için alana adı yazın ve **GitHub 'Da ara**' yı tıklatın.|
    |Dal     | Kaynak dosyaları çekilecek dal. TFVC kaynak denetimi türü için dal hedefleme kullanılamıyor.          |
    |Klasör yolu     | Eşitlenmesi gereken runbook 'ları içeren klasör, örneğin, **/runbook**'lar. Yalnızca belirtilen klasördeki runbook 'lar eşitlenir. Özyineleme desteklenmiyor.        |
    |Otomatik eşitleme<sup>1</sup>     | Kaynak denetim deposunda bir kayıt yapıldığında otomatik eşitlemeyi açan veya kapatan ayar.        |
    |Runbook 'U Yayımla     | Runbook 'lar kaynak denetiminden eşitlemeden sonra otomatik olarak yayımlanıyorsa ve aksi takdirde, ' nin ayarlanması.           |
    |Description     | Kaynak denetimi ile ilgili ek ayrıntıları belirten metin.        |

    <sup>1</sup> Azure Repos ile kaynak denetimi tümleştirmesini yapılandırırken otomatik eşitlemeyi etkinleştirmek Için bir proje yöneticisi olmanız gerekir.

   ![Kaynak denetimi Özeti](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Kaynak denetimi deponuz için oturum açma Azure portal, oturum açınızdan farklı olabilir. Kaynak denetimini yapılandırırken kaynak denetimi deponuzun doğru hesabıyla oturum açtığınızdan emin olun. Şüpheli bir sorun varsa, tarayıcınızda yeni bir sekme açın, **dev.Azure.com**, **VisualStudio.com** veya **GitHub.com** oturumunu açın ve kaynak denetimine yeniden bağlanmayı deneyin.

### <a name="configure-source-control-in-powershell"></a>PowerShell 'de kaynak denetimini yapılandırma

Azure Otomasyonu 'nda kaynak denetimi yapılandırmak için PowerShell de kullanabilirsiniz. Bu işlem için PowerShell cmdlet 'lerini kullanmak için bir kişisel erişim belirteci (PAT) gereklidir. Kaynak denetimi bağlantısını oluşturmak için [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) cmdlet 'ini kullanın. Bu cmdlet, PAT için güvenli bir dize gerektirir. Güvenli dize oluşturma hakkında bilgi edinmek için bkz. [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Aşağıdaki alt bölümlerde, GitHub, Azure Repos (git) ve Azure Repos (TFVC) için kaynak denetimi bağlantısının PowerShell oluşturması gösterilmektedir.

#### <a name="create-source-control-connection-for-github"></a>GitHub için kaynak denetimi bağlantısı oluşturma

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure Repos için kaynak denetimi bağlantısı oluşturma (git)

> [!NOTE]
> Azure Repos (git), önceki biçimlerde kullanılan **VisualStudio.com** yerine **dev.Azure.com** 'e erişen bir URL kullanır. Eski URL biçimi `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` kullanım dışıdır ancak hala desteklenir. Yeni biçim tercih edilir.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure Repos için kaynak denetimi bağlantısı oluşturma (TFVC)

> [!NOTE]
> Azure Repos (TFVC), önceki biçimlerde kullanılan **VisualStudio.com** yerine **dev.Azure.com** erişen bir URL kullanır. Eski URL biçimi `https://<accountname>.visualstudio.com/<projectname>/_versionControl` kullanım dışıdır ancak hala desteklenir. Yeni biçim tercih edilir.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Kişisel erişim belirteci (PAT) izinleri

Kaynak denetimi, PATs için bazı minimum izinleri gerektirir. Aşağıdaki alt bölümler, GitHub ve Azure Repos için gereken en düşük izinleri içerir.

##### <a name="minimum-pat-permissions-for-github"></a>GitHub için en düşük PAT izinleri

Aşağıdaki tabloda, GitHub için gereken en düşük PAT izinleri tanımlanmaktadır. GitHub 'da bir PAT oluşturma hakkında daha fazla bilgi için, bkz. [komut satırı için kişisel erişim belirteci oluşturma](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Kapsam  |Açıklama  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Erişim yapma durumu         |
|`repo_deployment`      | Erişim dağıtım durumu         |
|`public_repo`     | Genel depolara erişin         |
|`repo:invite` | Depo davetlerini erişim |
|`security_events` | Güvenlik olaylarını okuma ve yazma |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Depo kancalarını Yaz         |
|`read:repo_hook`|Depo kancalarını okuma|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos için en düşük PAT izinleri

Aşağıdaki liste Azure Repos için gereken en düşük PAT izinlerini tanımlar. Azure Repos bir PAT oluşturma hakkında daha fazla bilgi için bkz. [kişisel erişim belirteçleriyle erişim kimlik doğrulaması](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Kapsam  |  Erişim türü  |
|---------| ----------|
| `Code`      | Okuma  |
| `Project and team` | Okuma |
| `Identity` | Okuma     |
| `User profile` | Okuma     |
| `Work items` | Okuma    |
| `Service connections` | Oku, sorgula, Yönet<sup>1</sup>    |

<sup>1</sup> `Service connections` izin yalnızca, oto eşitlemesini etkinleştirdiyseniz gereklidir.

## <a name="synchronize-with-source-control"></a>Kaynak denetimiyle eşitler

Kaynak denetimiyle eşitlenmek için bu adımları izleyin.

1. Kaynak denetimi sayfasındaki tablodan kaynağı seçin.

2. Eşitleme işlemini başlatmak için **Eşitlemeyi Başlat** ' a tıklayın.

3. **Eşitleme işleri** sekmesine tıklayarak geçerli eşitleme işinin durumunu veya önceki olanları görüntüleyin.

4. **Kaynak denetimi** açılan menüsünde, bir kaynak denetim mekanizması seçin.

    ![Eşitleme durumu](./media/source-control-integration/sync-status.png)

5. Bir işi tıklatmak, iş çıktısını görüntülemenize izin verir. Aşağıdaki örnek, bir kaynak denetimi eşitleme işinin çıktıdır.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

    ==================================================================

    ```

6. Kaynak denetimi eşitleme Işi Özeti sayfasında **Tüm Günlükler** seçilerek ek günlüğe kaydetme kullanılabilir. Bu ek günlük girişleri, kaynak denetimi kullanılırken oluşabilecek sorunları gidermenize yardımcı olabilir.

## <a name="disconnect-source-control"></a>Kaynak denetimi bağlantısını kes

Kaynak denetim deposundan bağlantıyı kesmek için:

1. Otomasyon hesabınızda **Hesap ayarları** altında **kaynak denetimi** ' ni açın.

2. Kaldırılacak kaynak denetim mekanizmasını seçin.

3. Kaynak denetimi Özeti sayfasında **Sil**' e tıklayın.

## <a name="handle-encoding-issues"></a>Kodlama sorunlarını işle

Farklı düzenleyiciler kullanarak kaynak denetim deponuzdaki runbook 'ları birden çok kişi düzenliyorsanız, kodlama sorunları oluşabilir. Bu durumla ilgili daha fazla bilgi edinmek için bkz. [kodlama sorunlarının yaygın nedenleri](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>PAT 'yi güncelleştirme

Şu anda, kaynak denetimindeki PAT 'yi güncelleştirmek için Azure portal kullanamazsınız. PAT süreniz dolduğunda veya iptal edildiğinde, kaynak denetimini şu yollarla yeni bir erişim belirteciyle güncelleştirebilirsiniz:

* [REST API](/rest/api/automation/sourcecontrol/update)kullanın.
* [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet 'ini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Automation 'da kaynak denetimini tümleştirmek için bkz. Azure Otomasyonu [: Azure Otomasyonu 'Nda kaynak denetimi tümleştirmesi](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Runbook kaynak denetimini Visual Studio Codespaces ile tümleştirmek için bkz. [Azure Otomasyonu: Visual Studio codespaces kullanarak runbook kaynak denetimini tümleştirme](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
