---
title: SYNAPSE Studio 'da kaynak denetimi
description: Azure SYNAPSE Studio 'da kaynak denetimini yapılandırma hakkında bilgi edinin
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567851"
---
# <a name="source-control-in-azure-synapse-studio"></a>Azure SYNAPSE Studio 'da kaynak denetimi

Varsayılan olarak, Azure SYNAPSE Studio yazarları doğrudan SYNAPSE hizmetine karşı yazar. Kaynak denetimi için Git kullanarak işbirliği yapmanız gerekiyorsa SYNAPSE Studio, çalışma alanınızı bir git deposu, Azure DevOps veya GitHub ile ilişkilendirmenize olanak tanır. 

Bu makale, git deposunun etkin olduğu bir Synapse çalışma alanında nasıl yapılandırılacağını ve çalıştığını özetler. Ayrıca, bazı en iyi uygulamalar ve sorun giderme kılavuzu da vurgularız.

> [!NOTE]
> Azure SYNAPSE Studio git tümleştirmesi, Azure Kamu Bulutu 'nda bulunmamaktadır.

## <a name="configure-git-repository-in-synapse-studio"></a>SYNAPSE Studio 'da Git deposunu yapılandırma 

SYNAPSE Studio 'Yu başlattıktan sonra, çalışma alanınızda bir git deposu yapılandırabilirsiniz. Bir Synapse Studio çalışma alanı tek seferde yalnızca bir git deposuyla ilişkilendirilebilir. 

### <a name="configuration-method-1-global-bar"></a>Yapılandırma yöntemi 1: genel çubuk

SYNAPSE Studio genel çubuğunda **SYNAPSE Live** açılır menüsünü ve ardından **kod deposunu ayarla**' yı seçin.

![Kod deposu ayarlarını yazma işleminden yapılandırma](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Yapılandırma yöntemi 2: hub 'ı yönetme

SYNAPSE Studio 'nun yönetim merkezine gidin. **Kaynak denetimi** bölümünde **Git yapılandırması** ' nı seçin. Bağlı depo yoksa, **Yapılandır**' a tıklayın.

![Kod deposu ayarlarını yönetim hub 'ından yapılandırma](media/configure-repo-2.png)

> [!NOTE]
> Çalışma alanı katılımcısı, sahip veya daha yüksek düzey roller olarak verilen kullanıcılar, Azure SYNAPSE Studio 'da Git deposunu yapılandırabilir, düzenleyebilir ve bağlantısını kesebilir 

Çalışma alanınızda Azure DevOps veya GitHub Git deposunu bağlayabilirsiniz.

## <a name="connect-with-azure-devops-git"></a>Azure DevOps git ile bağlantı 

Kaynak denetimi, işbirliği, sürüm oluşturma vb. için bir Synapse çalışma alanını Azure DevOps deposu ile ilişkilendirebilirsiniz. Azure DevOps deponuz yoksa, depo kaynaklarınızı oluşturmak için [Bu yönergeleri](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) izleyin.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps git deposu ayarları

Git deponuza bağlanırken önce depo türü olarak Azure DevOps git ' i seçin ve açılan listeden bir Azure AD kiracısı seçin ve **devam**' a tıklayın.

![Kod deposu ayarlarını yapılandırma](media/connect-with-azuredevops-repo-selected.png)

Yapılandırma bölmesi aşağıdaki Azure DevOps git ayarlarını gösterir:

| Ayar | Açıklama | Değer |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü.<br/> | Azure DevOps git veya GitHub |
| **Azure Active Directory** | Azure AD kiracı adınız. | `<your tenant name>` |
| **Azure DevOps hesabı** | Azure Repos kuruluşunuzun adı. Azure Repos kuruluşunuzun adını adresinde bulabilirsiniz `https://{organization name}.visualstudio.com` . Visual Studio profilinize erişmek ve depolarınızı ve projelerinizi görmek için [Azure Repos kuruluşunuzda oturum](https://www.visualstudio.com/team-services/git/) açabilirsiniz. | `<your organization name>` |
| **ProjectName** | Azure Repos projenizin adı. Azure Repos projenizin adını adresinde bulabilirsiniz `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos kod deposu adınız. Azure Repos projeler, projeniz büyüdükçe kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya projenizde zaten var olan bir depoyu kullanabilirsiniz. | `<your Azure Repos code repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan Azure Repos işbirliği dalı. Varsayılan olarak, `master` . Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. Mevcut dalları seçebilir veya yeni oluştur | `<your collaboration branch name>` |
| **Kök klasör** | Azure Repos işbirliği dalınızdaki kök klasörünüz. | `<your root folder name>` |
| **Mevcut kaynakları depoya aktar** | Mevcut kaynakların SYNAPSE Studio 'dan Azure Repos git deposuna aktarılmayacağını belirtir. Çalışma alanı kaynaklarınızı (havuzlar hariç) JSON biçimindeki ilişkili git deposuna aktarmak için kutuyu işaretleyin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır. Bu kutu işaretli olmadığında, mevcut kaynaklar içeri aktarılmaz. | Checked (varsayılan) |
| **Kaynağı bu dala al** | Kaynakları (SQL betiği, Not defteri, Spark iş tanımı, veri kümesi, veri akışı vb.) hangi dala içeri aktarılacağını seçin. 

Ayrıca, bağlanmak istediğiniz git deposuna hızlıca işaret etmek için depo bağlantısını kullanabilirsiniz. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Farklı bir Azure Active Directory kiracı kullanın

Azure Repos git deposu, farklı bir Azure Active Directory kiracısında olabilir. Farklı bir Azure AD kiracısı belirtmek için kullandığınız Azure aboneliğinde yönetici izinlerine sahip olmanız gerekir. Daha fazla bilgi için bkz. [abonelik yöneticisini değiştirme](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Başka bir Azure Active Directory bağlanmak için, oturum açmış olan kullanıcının bu Active Directory 'nin bir parçası olması gerekir. 

### <a name="use-your-personal-microsoft-account"></a>Kişisel Microsoft hesabı kullanın

Git tümleştirmesi için kişisel Microsoft hesabı kullanmak istiyorsanız, kişisel Azure deponuzu kuruluşunuzun Active Directory bağlayabilirsiniz.

1. Kişisel Microsoft hesabı kuruluşunuzun Active Directory Konuk olarak ekleyin. Daha fazla bilgi için, bkz. [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../../active-directory/external-identities/add-users-administrator.md).

2. Kişisel Microsoft hesabı Azure portal oturum açın. Sonra kuruluşunuzun Active Directory geçin.

3. Kişisel deponuzu şimdi gördüğünüz Azure DevOps bölümüne gidin. Depoyu seçin ve Active Directory bağlanın.

Bu yapılandırma adımlarında sonra, SYNAPSE Studio 'da git tümleştirmesini ayarlarken kişisel deponuz kullanılabilir.

Kuruluşunuzun Active Directory Azure Repos bağlama hakkında daha fazla bilgi için bkz. [kuruluşunuzu Azure Active Directory 'e bağlama](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>GitHub ile bağlanma 

 Kaynak denetimi, işbirliği ve sürüm oluşturma için bir GitHub deposu ile bir çalışma alanı ilişkilendirebilirsiniz. GitHub hesabınız veya deponuz yoksa kaynaklarınızı oluşturmak için [Bu yönergeleri](https://github.com/join) izleyin.

SYNAPSE Studio ile GitHub tümleştirmesi hem genel GitHub (yani,) hem de [https://github.com](https://github.com) GitHub Enterprise 'u destekler. GitHub 'daki depoya okuma ve yazma iznine sahip olduğunuz sürece hem genel hem de özel GitHub depolarını kullanabilirsiniz.

### <a name="github-settings"></a>GitHub ayarları

Git deponuza bağlanırken önce depo türünü GitHub olarak seçin ve ardından GitHub Enterprise Server kullanıyorsanız GitHub hesabınızı veya GitHub Enterprise Server URL 'nizi sağlayın ve **devam**' a tıklayın.

![GitHub depo ayarları](media/connect-with-github-repo-1.png)

Yapılandırma bölmesinde aşağıdaki GitHub deposu ayarları gösterilmektedir:

| **Ayar** | **Açıklama**  | **Değer**  |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü. | GitHub |
| **GitHub Enterprise kullanma** | GitHub Enterprise 'ı seçme onay kutusu | seçilmemiş (varsayılan) |
| **GitHub Enterprise URL 'SI** | GitHub Enterprise root URL 'SI (yerel GitHub Enterprise Server için HTTPS olmalıdır). Örneğin: `https://github.mydomain.com`. Yalnızca **GitHub Enterprise kullan** seçilirse gereklidir | `<your GitHub enterprise url>` |                                                           
| **GitHub hesabı** | GitHub hesabınızın adı. Bu ad https: \/ /GitHub.exe com/{Account Name}/{Repository Name} öğesinden bulunabilir. Bu sayfaya gidildiğinde GitHub hesabınıza GitHub OAuth kimlik bilgilerini girmeniz istenir. | `<your GitHub account name>` |
| **Depo adı**  | GitHub kod deposu adınız. GitHub hesapları, kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya hesabınızda zaten var olan bir depoyu kullanabilirsiniz. | `<your repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan GitHub işbirliği dalınız. Varsayılan olarak, ana. Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. | `<your collaboration branch>` |
| **Kök klasör** | GitHub işbirliği dalınızdaki kök klasörünüz. |`<your root folder name>` |
| **Mevcut kaynakları depoya aktar** | SYNAPSE Studio 'dan bir git deposuna mevcut kaynakların içeri aktarılmayacağını belirtir. Çalışma alanı kaynaklarınızı (havuzlar hariç) JSON biçimindeki ilişkili git deposuna aktarmak için kutuyu işaretleyin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır. Bu kutu işaretli olmadığında, mevcut kaynaklar içeri aktarılmaz. | Seçili (varsayılan) |
| **Kaynağı bu dala al** | Kaynakların (SQL betiği, Not defteri, Spark iş tanımı, veri kümesi, veri akışı vb.) içe aktarıldığı dalı seçin. 

### <a name="github-organizations"></a>GitHub kuruluşları

Bir GitHub kuruluşuna bağlanmak, kuruluşun SYNAPSE Studio 'ya izin vermesini gerektirir. Kuruluş üzerinde yönetıcı izinlerine sahip bir kullanıcının aşağıdaki adımları gerçekleştirmesi gerekir.

#### <a name="connecting-to-github-for-the-first-time"></a>İlk kez GitHub’a bağlanma

SYNAPSE Studio 'dan ilk kez GitHub 'a bağlanıyorsanız, GitHub kuruluşuna bağlanmak için aşağıdaki adımları izleyin.

1. Git yapılandırması bölmesinde, *GitHub hesabı* alanına kuruluş adını girin. GitHub 'da oturum açmak için bir istem görüntülenir. 

1. Kullanıcı kimlik bilgilerinizi kullanarak oturum açın.

1. SYNAPSE *Azure SYNAPSE* adlı bir uygulama olarak yetkilendirmeniz istenir. Bu ekranda, SYNAPSE için kuruluşa erişim izni verme seçeneği görüntülenir. İzin verme seçeneğini görmüyorsanız, yöneticiden GitHub aracılığıyla izni el ile vermesini isteyin.

Bu adımları izledikten sonra, çalışma alanınız kuruluşunuzda hem genel hem de özel depolara bağlanabilir. Bağlanamıyorsanız, tarayıcı önbelleğini temizlemeyi ve yeniden denemeyi deneyin.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Kişisel hesap kullanılarak GitHub 'a zaten bağlanıldı

GitHub 'a zaten bağlandıysanız ve yalnızca kişisel hesaba erişim izni verdiyseniz, bir kuruluşa izin vermek için aşağıdaki adımları izleyin.

1. GitHub ve açık **Ayarlar**' a gidin.

    ![GitHub ayarlarını aç](media/github-settings.png)

1. **Uygulamalar**' ı seçin. **Yetkili OAuth uygulamaları** sekmesinde *Azure SYNAPSE*' i görmeniz gerekir.

    ![OAuth uygulamalarını yetkilendir](media/authorize-app.png)

1. *Azure SYNAPSE* ' ı seçin ve kuruluşunuza erişim izni verin.

    ![Kuruluşa izin ver](media/grant-organization-permission.png)

Bu adımları tamamladıktan sonra, çalışma alanınız kuruluşunuzda hem genel hem de özel depolara bağlanabilir.

## <a name="version-control"></a>Sürüm denetimi

Sürüm denetimi sistemleri ( _kaynak denetimi_ olarak da bilinir), geliştiricilerin kod üzerinde işbirliği yapmasına ve değişiklikleri izlemesine olanak sağlar. Kaynak denetimi, çoklu geliştirici projelerine yönelik temel bir araçtır.

### <a name="creating-feature-branches"></a>Özellik dalları oluşturma

Bir Synapse Studio ile ilişkili her git deposunun işbirliği dalı vardır. ( `main` veya `master` varsayılan işbirliği daldır). Kullanıcılar, dal açılan menüsünde **+ yeni dal** ' a tıklayarak da özellik dalları oluşturabilir. Yeni dal bölmesi göründüğünde, özellik dalınızın adını girin.

![Yeni dal oluştur](media/create-new-branch.png)

Özellik dalınızdaki değişiklikleri işbirliği dalınızla birleştirmeye hazırsanız, dal açılan listesine tıklayın ve **çekme Isteği oluştur**' u seçin. Bu eylem, çekme istekleri oluşturabileceğiniz, kod İncelemeleri yapabileceğiniz ve değişiklikleri işbirliği dalınızda birleştirebileceğiniz git sağlayıcısına götürür. Yalnızca SYNAPSE hizmetine işbirliği dalınızdan yayımlama izni verilir. 

![Yeni bir çekme isteği oluştur](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Yayımlama ayarlarını yapılandırma

Varsayılan olarak, SYNAPSE Studio çalışma alanı şablonları oluşturur ve bunları adlı bir dala kaydeder `workspace_publish` . Özel bir yayımlama dalı yapılandırmak için, `publish_config.json` işbirliği dalında kök klasöre bir dosya ekleyin. Yayımlarken, SYNAPSE Studio bu dosyayı okur, alanı arar `publishBranch` ve çalışma alanı şablonu dosyalarını belirtilen konuma kaydeder. Dal yoksa, SYNAPSE Studio tarafından otomatik olarak oluşturulur. Ve bu dosyanın nasıl göründüğüne ilişkin örnek aşağıda verilmiştir:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure SYNAPSE Studio tek seferde yalnızca bir yayımlama dalına sahip olabilir. Yeni bir yayımlama dalı belirttiğinizde, önceki yayımlama dalı silinmez. Önceki yayımlama dalını kaldırmak istiyorsanız el ile silin.


### <a name="publish-code-changes"></a>Kod değişikliklerini Yayımla

İşbirliği dalındaki değişiklikleri birleştirdikten sonra **Yayımla** ' ya tıklayarak kod değişikliklerinizi Işbirliği dalında SYNAPSE hizmetine el ile yayımlayın.

![Değişiklikleri yayımlama](media/gitmode-publish.png)

Yayın dalının ve bekleyen değişikliklerin doğru olduğunu onaylamak için bir yan bölme açılır. Değişikliklerinizi doğruladıktan sonra, yayımlamayı onaylamak için **Tamam** ' ı tıklatın.

![Doğru yayımlama dalını onaylayın](media/publish-change.png)

> [!IMPORTANT]
> İşbirliği dalı, hizmette dağıtılan özellikler için temsilci değildir. İşbirliği dalındaki değişiklikler el ile hizmet *yayımlamalıdır* .

## <a name="switch-to-a-different-git-repository"></a>Farklı bir git deposuna geçiş yap

Farklı bir git deposuna geçiş yapmak için, **kaynak denetimi** altındaki Yönetim hub 'ındaki git yapılandırma sayfasına gidin. **Bağlantıyı kes**' i seçin. 

![Git simgesi](media/remove-repository.png)

Çalışma alanınız ile ilişkili Git deposunu kaldırmak için çalışma alanı adınızı girip **bağlantıyı kes** ' e tıklayın.

Geçerli depoyla ilişkilendirmeyi kaldırdıktan sonra, git ayarlarınızı farklı bir depoyu kullanacak şekilde yapılandırabilir ve ardından mevcut kaynakları yeni depoya aktarabilirsiniz.

> [!IMPORTANT]
> Git yapılandırmasını çalışma alanından kaldırma, depodan herhangi bir şeyi silmez. SYNAPSE çalışma alanı, yayımlanan tüm kaynakları içerecektir. Çalışma alanını doğrudan hizmete karşı düzenlemeye devam edebilirsiniz.

## <a name="best-practices-for-git-integration"></a>Git tümleştirmesi için en iyi uygulamalar

-   **İzinler**. Çalışma alanınıza bağlı bir git deposu olduktan sonra, çalışma alanınızdaki herhangi bir rolle git deponuza erişebilen herkes, git modundaki SQL betiği, Not defteri, Spark iş tanımı, veri kümesi, veri akışı ve işlem hattı gibi yapıtları güncelleştirebilir. Genellikle her takım üyesinin çalışma alanını güncelleştirme izinlerine sahip olmasını istemezsiniz. Yalnızca SYNAPSE çalışma alanı yapıt yazarları için Git deposu izni verin. 
-   **İşbirliği** yapın. İşbirliği dalında doğrudan iadelere izin vermeniz önerilir. Bu kısıtlama, her iade etme işleminin [özellik dalları oluşturma](source-control.md#creating-feature-branches)bölümünde açıklanan bir çekme isteği gözden geçirme süreci boyunca ilerlemesinin önlenmesi için hataları önlemeye yardımcı olabilir.
-   **SYNAPSE canlı modu**. Git modunda yayımladıktan sonra tüm değişiklikler SYNAPSE canlı modda yansıtılır. SYNAPSE Live modunda yayımlama devre dışıdır. Ve doğru izin verdiyseniz yapıtları canlı modda görüntüleyebilirsiniz. 
-   **Studio 'da yapıtları düzenleyin**. SYNAPSE Studio, çalışma alanı kaynak denetimini etkinleştirebileceğiniz ve değişiklikleri otomatik olarak git 'e eşitleyebileceğiniz tek yerdir. SDK, PowerShell ile yapılan herhangi bir değişiklik git ile eşitlenmez. Git etkinleştirildiğinde Studio 'daki yapıtları her zaman düzenlemenizi öneririz.

## <a name="troubleshooting-git-integration"></a>Git tümleştirmesi sorunlarını giderme

### <a name="access-to-git-mode"></a>Git moduna erişim 

Çalışma alanınız ile bağlantılı GitHub git deposuna izin verdiyseniz ancak git moduna erişemezsiniz: 

1. Önbelleğinizi temizleyin ve sayfayı yenileyin. 

1. GitHub hesabınızı oturum açın.

### <a name="stale-publish-branch"></a>Eski yayımlama dalı

Yayımla dalı işbirliği dalında eşitlenmemiş ve yeni bir yayımlamaya rağmen güncel kaynaklar içeriyorsa, aşağıdaki adımları izleyin:

1. Geçerli git deponuzu kaldırma

1. Git 'i aynı ayarlarla yeniden yapılandırın, ancak **mevcut kaynakları depoya Içeri aktarma** işleminin işaretli olduğundan emin olun ve aynı dalı seçin.  

1. Değişiklikleri işbirliği dalıyla birleştirmek için çekme isteği oluşturun 

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

- SYNAPSE Studio, işleme ya da kaynakların seçmeli yayımlamasına izin vermez. 
- SYNAPSE Studio, COMMIT iletisini özelleştirmeyi desteklemiyor.
- Tasarıma göre, Studio 'daki silme eylemi doğrudan git 'e kaydedilecek

## <a name="next-steps"></a>Sonraki adımlar

* Sürekli tümleştirme ve dağıtım uygulamak için bkz. [sürekli tümleştirme ve teslim (CI/CD)](continuous-integration-deployment.md).