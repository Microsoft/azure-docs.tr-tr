---
title: Kaynak denetimi
description: Azure Data Factory 'da kaynak denetimini yapılandırmayı öğrenin
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: e5cb9090025fe541ad3f70cdac876aca0552f154
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553167"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory kaynak denetimi
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Varsayılan olarak, Azure Data Factory Kullanıcı arabirimi deneyimi (UX) yazarları doğrudan Data Factory hizmetine göre yapılır. Bu deneyim aşağıdaki sınırlamalara sahiptir:

- Data Factory hizmeti, değişikliklerinizin JSON varlıklarını depolamak için bir depo içermez. Değişiklikleri kaydetmek için tek yol **Tümünü Yayımla** düğmesi ile tüm değişiklikler doğrudan Data Factory hizmetine yayımlanır.
- Data Factory hizmeti işbirliği ve sürüm denetimi için en iyi duruma getirilmemiştir.
- Data Factory kendisini dağıtmak için gereken Azure Resource Manager şablonu dahil değildir.

Daha iyi bir yazma deneyimi sağlamak için Azure Data Factory, bir Git deposunu Azure Repos veya GitHub ile yapılandırmanıza olanak tanır. Git değişiklik izlemeyi ve işbirliğini kolaylaştıran bir sürüm denetimi sistemidir. Bu makale, en iyi yöntemler ve sorun giderme kılavuzu ile birlikte bir git deposunda yapılandırma ve çalışmayı özetler.

> [!NOTE]
> Azure Kamu Bulutu için yalnızca *GitHub Enterprise Server* kullanılabilir.

Azure Data Factory git ile tümleştirilme hakkında daha fazla bilgi edinmek için aşağıdaki 15 dakikalık öğretici videosunu görüntüleyin:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Git tümleştirmesinin avantajları

Aşağıda, git tümleştirmesinin yazma deneyimine sağladığı avantajların bir listesi verilmiştir:

-   **Kaynak denetimi:** Data Factory iş yükleriniz önemli olmaya yönelik olarak, aşağıdaki gibi çeşitli kaynak denetimi avantajlarından yararlanmak için fabrikanızı git ile tümleştirmeniz gerekir:
    -   Değişiklikleri izleme/denetleme özelliği.
    -   Hataları ortaya çıkan değişiklikleri döndürmenize olanak tanır.
-   **Kısmi kaydetme:** Data Factory hizmetine göre yazarken, değişiklikleri taslak olarak kaydedemez ve tüm yayınlar Veri Fabrikası doğrulaması ' nı iletmelidir. İşlem hatlarınız bitmediği veya yalnızca bilgisayarınız kilitlenirse değişiklikleri kaybetmek istemediğinizde git tümleştirmesi, Veri Fabrikası kaynakları üzerinde bulundukları durum ne olursa olsun artımlı değişikliklere izin verir. Bir git deposunun yapılandırılması, değişiklikleri kaydetmenizi sağlayarak yalnızca yaptığınız değişiklikleri sizin için test ettiğinizde yayımlamanıza izin verir.
-   **İşbirliği ve denetim:** Aynı fabrikaya katkıda bulunan birden fazla ekip üyesine sahipseniz, bir kod inceleme işlemi aracılığıyla ekip malarınızın birbirleriyle işbirliği yapmasına izin vermek isteyebilirsiniz. Ayrıca, fabrikanızın her katkıda bulunan eşit izinleri olmadığı şekilde ayarlayabilirsiniz. Bazı takım üyelerinin yalnızca git aracılığıyla değişiklik yapmasına izin verilebilir ve yalnızca ekipteki belirli kişilerin değişiklikleri fabrikada yayımlamasına izin verilir.
-   **Daha ıyı CI/CD:**  [Sürekli teslim işlemiyle](continuous-integration-deployment.md)birden çok ortama dağıtıyorsanız, git tümleştirmesi belirli eylemleri daha kolay hale getirir. Bu eylemlerin bazıları şunlardır:
    -   Yayın işlem hattınızı, ' dev ' fabrikasında yapılan herhangi bir değişiklik olduğu anda otomatik olarak tetiklenecek şekilde yapılandırın.
    -   Fabrikanızın Kaynak Yöneticisi şablonunda parametreler olarak kullanılabilen özellikleri özelleştirin. Yalnızca gerekli özellikler kümesini parametre olarak tutmak ve her şeyin sabit olarak kodlanmasını sağlamak yararlı olabilir.
-   **Daha Iyi performans:** Git tümleştirmesinin bulunduğu ortalama bir fabrika, Data Factory hizmetinde bir yazma işlemiyle 10 kat daha hızlı yükler. Bu performans geliştirmesi, kaynakların git aracılığıyla indirilmesinden kaynaklanır.

> [!NOTE]
> Git deposu yapılandırıldığında Data Factory hizmeti ile doğrudan yazma Azure Data Factory UX içinde devre dışıdır. PowerShell veya SDK aracılığıyla yapılan değişiklikler doğrudan Data Factory hizmetine yayımlanır ve git 'e girilmez.

## <a name="connect-to-a-git-repository"></a>Git deposuna Bağlan

Hem Azure Repos hem de GitHub için bir Git deposunu veri fabrikanıza bağlanmanın dört farklı yolu vardır. Bir git deposuna bağlandıktan sonra, **kaynak denetim** bölümünde **Git yapılandırması** altındaki [Yönetim hub 'ında](author-management-hub.md) yapılandırmanızı görüntüleyebilir ve yönetebilirsiniz.

### <a name="configuration-method-1-home-page"></a>Yapılandırma yöntemi 1: giriş sayfası

Azure Data Factory giriş sayfasında **kod deposunu ayarla**' yı seçin.

![Giriş sayfasından bir kod deposu yapılandırma](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Yapılandırma yöntemi 2: yazı tuvali yazma

Azure Data Factory UX yazma tuvalinde **Data Factory** açılan menüsünü ve ardından **kod deposunu ayarla**' yı seçin.

![Kod deposu ayarlarını yazma işleminden yapılandırma](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Yapılandırma yöntemi 3: Yönetim Merkezi

ADF UX 'teki yönetim merkezine gidin. **Kaynak denetimi** bölümünde **Git yapılandırması** ' nı seçin. Bağlı depo yoksa, **Yapılandır**' a tıklayın.

![Kod deposu ayarlarını yönetim hub 'ından yapılandırma](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Yapılandırma yöntemi 4: fabrika oluşturma sırasında

Azure portal yeni bir veri fabrikası oluştururken git **yapılandırma** sekmesinden git deposu bilgilerini yapılandırabilirsiniz.

> [!NOTE]
> Azure portalında git yapılandırılırken, bir açılan listenin parçası olmak yerine proje adı ve depo adı gibi ayarların el ile girilmesi gerekir.

![Azure portalından kod deposu ayarlarını yapılandırma](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git tümleştirmesi ile yazma

Azure Repos git tümleştirmesi ile görsel yazma, Veri Fabrikası işlem hatlarınız üzerinde iş için kaynak denetimi ve işbirliğini destekler. Kaynak denetimi, işbirliği, sürüm oluşturma vb. için bir Azure Repos git kuruluş deposu ile bir veri fabrikası ilişkilendirebilirsiniz. Tek bir Azure Repos git kuruluşunda birden çok depo bulunabilir, ancak bir Azure Repos git deposu yalnızca bir veri fabrikası ile ilişkilendirilebilir. Azure Repos kuruluşunuz veya deponuz yoksa kaynaklarınızı oluşturmak için [Bu yönergeleri](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) izleyin.

> [!NOTE]
> Betik ve veri dosyalarını bir Azure Repos git deposunda saklayabilirsiniz. Ancak, dosyaları Azure depolama 'ya el ile yüklemeniz gerekir. Data Factory işlem hattı, Azure Repos git deposunda depolanan betiği veya veri dosyalarını Azure depolama 'ya otomatik olarak yükler.

### <a name="azure-repos-settings"></a>Azure Repos ayarları

![Kod deposu ayarlarını yapılandırma](media/author-visually/repo-settings.png)

Yapılandırma bölmesinde aşağıdaki Azure Repos kod deposu ayarları gösterilmektedir:

| Ayar | Açıklama | Değer |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü.<br/> | Azure DevOps git veya GitHub |
| **Azure Active Directory** | Azure AD kiracı adınız. | `<your tenant name>` |
| **Azure Repos kuruluş** | Azure Repos kuruluşunuzun adı. Azure Repos kuruluşunuzun adını adresinde bulabilirsiniz `https://{organization name}.visualstudio.com` . Visual Studio profilinize erişmek ve depolarınızı ve projelerinizi görmek için [Azure Repos kuruluşunuzda oturum](https://www.visualstudio.com/team-services/git/) açabilirsiniz. | `<your organization name>` |
| **ProjectName** | Azure Repos projenizin adı. Azure Repos projenizin adını adresinde bulabilirsiniz `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos kod deposu adınız. Azure Repos projeler, projeniz büyüdükçe kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya projenizde zaten var olan bir depoyu kullanabilirsiniz. | `<your Azure Repos code repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan Azure Repos işbirliği dalı. Varsayılan olarak, bu `main` . Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. | `<your collaboration branch name>` |
| **Kök klasör** | Azure Repos işbirliği dalınızdaki kök klasörünüz. | `<your root folder name>` |
| **Mevcut Data Factory kaynaklarını depoya aktar** | UX **yazma tuvalinden** bir Azure Repos git deposuna var olan veri fabrikası kaynaklarının içeri aktarılmayacağını belirtir. Veri Fabrikası kaynaklarınızı JSON biçiminde ilişkili git deposuna aktarmak için kutuyu seçin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır (yani, bağlı hizmetler ve veri kümeleri ayrı Jdönemleri içine aktarılır). Bu kutu seçili olmadığında, mevcut kaynaklar içeri aktarılmaz. | Seçili (varsayılan) |
| **Kaynağı içeri aktarma dalı** | Veri Fabrikası kaynaklarının (işlem hatları, veri kümeleri, bağlı hizmetler vs.) içe aktarıldığı dalı belirtir. Kaynakları şu dallardan birine içeri aktarabilirsiniz: a. İşbirliği b. Yeni c oluştur. Mevcut olanı kullan |  |

> [!NOTE]
> Microsoft Edge kullanıyorsanız ve Azure DevOps hesabı açılır listenizde herhangi bir değer görmüyorsanız, güvenilen siteler listesine https://*. VisualStudio. com ekleyin.

### <a name="use-a-different-azure-active-directory-tenant"></a>Farklı bir Azure Active Directory kiracı kullanın

Azure Repos git deposu, farklı bir Azure Active Directory kiracısında olabilir. Farklı bir Azure AD kiracısı belirtmek için kullandığınız Azure aboneliğinde yönetici izinlerine sahip olmanız gerekir. Daha fazla bilgi için bkz. [abonelik yöneticisini değiştirme](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> Başka bir Azure Active Directory bağlanmak için, oturum açmış olan kullanıcının bu Active Directory 'nin bir parçası olması gerekir. 

### <a name="use-your-personal-microsoft-account"></a>Kişisel Microsoft hesabı kullanın

Git tümleştirmesi için kişisel Microsoft hesabı kullanmak istiyorsanız, kişisel Azure deponuzu kuruluşunuzun Active Directory bağlayabilirsiniz.

1. Kişisel Microsoft hesabı kuruluşunuzun Active Directory Konuk olarak ekleyin. Daha fazla bilgi için, bkz. [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../active-directory/external-identities/add-users-administrator.md).

2. Kişisel Microsoft hesabı Azure portal oturum açın. Sonra kuruluşunuzun Active Directory geçin.

3. Kişisel deponuzu şimdi gördüğünüz Azure DevOps bölümüne gidin. Depoyu seçin ve Active Directory bağlanın.

Bu yapılandırma adımlarında sonra, Data Factory Kullanıcı arabiriminde git tümleştirmesini ayarlarken kişisel deponuz kullanılabilir.

Kuruluşunuzun Active Directory Azure Repos bağlama hakkında daha fazla bilgi için bkz. [Azure DevOps kuruluşunuzu Azure Active Directory 'e bağlama](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>GitHub tümleştirmesi ile yazma

GitHub tümleştirmesiyle Visual Authoring, Veri Fabrikası işlem hatlarınız üzerinde iş için kaynak denetimi ve işbirliğini destekler. Kaynak denetimi, işbirliği ve sürüm oluşturma için bir GitHub hesap deposuyla bir veri fabrikasını ilişkilendirebilirsiniz. Tek bir GitHub hesabının birden çok deposu olabilir, ancak bir GitHub deposu yalnızca bir veri fabrikası ile ilişkilendirilebilir. GitHub hesabınız veya deponuz yoksa kaynaklarınızı oluşturmak için [Bu yönergeleri](https://github.com/join) izleyin.

Data Factory ile GitHub tümleştirmesi hem genel GitHub (yani,) hem de [https://github.com](https://github.com) GitHub Enterprise 'u destekler. GitHub 'daki depoya okuma ve yazma izniniz olduğu sürece hem ortak hem de özel GitHub depolarını Data Factory ile birlikte kullanabilirsiniz.

Bir GitHub deposunu yapılandırmak için, kullanmakta olduğunuz Azure aboneliği için yönetici izinlerinizin olması gerekir.

### <a name="github-settings"></a>GitHub ayarları

![GitHub depo ayarları](media/author-visually/github-integration-image2.png)

Yapılandırma bölmesinde aşağıdaki GitHub deposu ayarları gösterilmektedir:

| **Ayar** | **Açıklama**  | **Değer**  |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü. | GitHub |
| **GitHub Enterprise kullanma** | GitHub Enterprise 'ı seçme onay kutusu | seçilmemiş (varsayılan) |
| **GitHub Enterprise URL 'SI** | GitHub Enterprise root URL 'SI (yerel GitHub Enterprise Server için HTTPS olmalıdır). Örneğin: `https://github.mydomain.com`. Yalnızca **GitHub Enterprise kullan** seçilirse gereklidir | `<your GitHub enterprise url>` |                                                           
| **GitHub hesabı** | GitHub hesabınızın adı. Bu ad https: \/ /GitHub.exe com/{Account Name}/{Repository Name} öğesinden bulunabilir. Bu sayfaya gidildiğinde GitHub hesabınıza GitHub OAuth kimlik bilgilerini girmeniz istenir. | `<your GitHub account name>` |
| **Depo adı**  | GitHub kod deposu adınız. GitHub hesapları, kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya hesabınızda zaten var olan bir depoyu kullanabilirsiniz. | `<your repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan GitHub işbirliği dalınız. Varsayılan olarak, bu ana. Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. | `<your collaboration branch>` |
| **Kök klasör** | GitHub işbirliği dalınızdaki kök klasörünüz. |`<your root folder name>` |
| **Mevcut Data Factory kaynaklarını depoya aktar** | UX yazma tuvalinden bir GitHub deposuna var olan veri fabrikası kaynaklarının içeri aktarılmayacağını belirtir. Veri Fabrikası kaynaklarınızı JSON biçiminde ilişkili git deposuna aktarmak için kutuyu seçin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır (yani, bağlı hizmetler ve veri kümeleri ayrı Jdönemleri içine aktarılır). Bu kutu seçili olmadığında, mevcut kaynaklar içeri aktarılmaz. | Seçili (varsayılan) |
| **Kaynağı içeri aktarma dalı** | Veri Fabrikası kaynaklarının (işlem hatları, veri kümeleri, bağlı hizmetler vs.) içe aktarıldığı dalı belirtir. Kaynakları şu dallardan birine içeri aktarabilirsiniz: a. İşbirliği b. Yeni c oluştur. Mevcut olanı kullan |  |

### <a name="github-organizations"></a>GitHub kuruluşları

Bir GitHub kuruluşuna bağlanmak, kuruluşun Azure Data Factory izin vermesini gerektirir. Kuruluş üzerinde yönetıcı izinlerine sahip bir kullanıcının, Data Factory 'nin bağlanmasına izin vermek için aşağıdaki adımları gerçekleştirmesi gerekir.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Azure Data Factory ilk kez GitHub 'a bağlanılıyor

İlk kez Azure Data Factory GitHub 'a bağlanıyorsanız, GitHub kuruluşuna bağlanmak için aşağıdaki adımları izleyin.

1. Git yapılandırması bölmesinde, *GitHub hesabı* alanına kuruluş adını girin. GitHub 'da oturum açmak için bir istem görüntülenir. 
1. Kullanıcı kimlik bilgilerinizi kullanarak oturum açın.
1. Azure Data Factory, *AzureDataFactory* adlı bir uygulama olarak yetkilendirmeniz istenir. Bu ekranda, ADF 'nin kuruluşa erişmesi için izin verme seçeneği görüntülenir. İzin verme seçeneğini görmüyorsanız, yöneticiden GitHub aracılığıyla izni el ile vermesini isteyin.

Bu adımları izledikten sonra, fabrikanızın kuruluşunuzda hem genel hem de özel depolara bağlanabilmesi mümkün olacaktır. Bağlanamıyorsanız, tarayıcı önbelleğini temizlemeyi ve yeniden denemeyi deneyin.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Kişisel hesap kullanılarak GitHub 'a zaten bağlanıldı

GitHub 'a zaten bağlandıysanız ve yalnızca kişisel hesaba erişim izni verdiyseniz, bir kuruluşa izin vermek için aşağıdaki adımları izleyin. 

1. GitHub ve açık **Ayarlar**' a gidin.

    ![GitHub ayarlarını aç](media/author-visually/github-settings.png)

1. **Uygulamalar**' ı seçin. **Yetkili OAuth uygulamaları** sekmesinde *AzureDataFactory* görmeniz gerekir.

    ![OAuth uygulamalarını seçin](media/author-visually/github-organization-select-application.png)

1. Uygulamayı seçin ve uygulamaya kuruluşunuza erişim izni verin.

    ![Erişim verme](media/author-visually/github-organization-grant.png)

Bu adımları izledikten sonra, fabrikanızın kuruluşunuzda hem genel hem de özel depolara bağlanabilmesi mümkün olacaktır. 

### <a name="known-github-limitations"></a>Bilinen GitHub sınırlamaları

- Betik ve veri dosyalarını bir GitHub deposunda saklayabilirsiniz. Ancak, dosyaları Azure depolama 'ya el ile yüklemeniz gerekir. Data Factory işlem hattı, GitHub deposunda depolanan betiği veya veri dosyalarını Azure depolama 'ya otomatik olarak yüklemez.

- 2.14.0 'den eski bir sürüme sahip GitHub Enterprise, Microsoft Edge tarayıcısında çalışmaz.

- Data Factory görsel yazma araçları ile GitHub tümleştirmesi yalnızca, Data Factory genel kullanıma sunulan sürümünde çalışmaktadır.


- Her kaynak türü için (işlem hatları ve veri kümeleri gibi) en fazla 1.000 varlık tek bir GitHub dalında getirilebilir. Bu sınıra ulaşıldığında, kaynaklarınızı ayrı fabrikalar halinde bölmek için önerilir. Azure DevOps Git'te bu sınırlama yoktur.

## <a name="version-control"></a>Sürüm denetimi

Sürüm denetimi sistemleri ( _kaynak denetimi_ olarak da bilinir), geliştiricilerin kod üzerinde işbirliği yapmasına ve kod tabanında yapılan değişiklikleri izlemesine izin verir. Kaynak denetimi, çoklu geliştirici projelerine yönelik temel bir araçtır.

### <a name="creating-feature-branches"></a>Özellik dalları oluşturma

Bir veri fabrikası ile ilişkili her bir Azure Repos git deposu bir işbirliği dalına sahiptir. ( `main` ) varsayılan işbirliği daldır). Kullanıcılar, dal açılan menüsünde **+ yeni dal** ' a tıklayarak da özellik dalları oluşturabilir. Yeni dal bölmesi göründüğünde, özellik dalınızın adını girin.

![Yeni dal oluştur](media/author-visually/new-branch.png)

Özellik dalınızdaki değişiklikleri işbirliği dalınızla birleştirmeye hazırsanız, dal açılan listesine tıklayın ve **çekme Isteği oluştur**' u seçin. Bu eylem, çekme istekleri oluşturabileceğiniz, kod İncelemeleri yapabileceğiniz ve değişiklikleri işbirliği dalınızda birleştirebileceğiniz git Azure Repos için sizi yönlendirir. ( `main` varsayılan). Yalnızca Data Factory hizmetine işbirliği dalınızdan yayımlama izni verilir. 

![Yeni bir çekme isteği oluştur](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Yayımlama ayarlarını yapılandırma

Data Factory, varsayılan olarak, yayımlanan fabrikasının Kaynak Yöneticisi şablonlarını oluşturur ve bunları adlı bir dala kaydeder `adf_publish` . Özel bir yayımlama dalı yapılandırmak için, `publish_config.json` işbirliği dalında kök klasöre bir dosya ekleyin. Yayımlarken, ADF bu dosyayı okur, alanı arar `publishBranch` ve tüm kaynak yöneticisi şablonlarını belirtilen konuma kaydeder. Dal yoksa, Data Factory tarafından otomatik olarak oluşturulur. Ve bu dosyanın nasıl göründüğüne ilişkin örnek aşağıda verilmiştir:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Tek seferde yalnızca bir yayımlama dalı olabilir Azure Data Factory. Yeni bir yayımlama dalı belirttiğinizde, Data Factory önceki yayımlama dalını silmez. Önceki yayımlama dalını kaldırmak istiyorsanız el ile silin.

> [!NOTE]
> Data Factory yalnızca `publish_config.json` fabrikası yüklediğinde dosyayı okur. Portalda zaten fabrika yüklüyse, değişikliklerinizin geçerli olması için tarayıcıyı yenileyin.

### <a name="publish-code-changes"></a>Kod değişikliklerini Yayımla

Çalışma dalındaki değişiklikleri ( `main` varsayılan değer) birleştirdikten sonra, kod değişikliklerinizi ana dalda Data Factory hizmetine el ile yayımlamak Için **Yayımla** ' ya tıklayın.

![Değişiklikleri Data Factory hizmette yayımlayın](media/author-visually/publish-changes.png)

Yayın dalının ve bekleyen değişikliklerin doğru olduğunu onaylamak için bir yan bölme açılır. Değişikliklerinizi doğruladıktan sonra, yayımlamayı onaylamak için **Tamam** ' ı tıklatın.

![Doğru yayımlama dalını onaylayın](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Ana dal, Data Factory hizmetine dağıtılan Özellikler temsilcisidir. Ana dalın Data Factory hizmetine el ile yayımlanması *gerekir* .

## <a name="best-practices-for-git-integration"></a>Git tümleştirmesi için en iyi uygulamalar

### <a name="permissions"></a>İzinler

Genellikle her takım üyesinin Data Factory güncelleştirme izinlerine sahip olmasını istemezsiniz. Aşağıdaki izin ayarları önerilir:

*   Tüm takım üyelerinin Data Factory okuma izinleri olmalıdır.
*   Yalnızca bir seçim kümesinin Data Factory yayımlamasına izin verilmelidir. Bunu yapmak için, Data Factory içeren **kaynak grubunda** **Data Factory katkıda** bulunan rolüne sahip olmaları gerekir. İzinler hakkında daha fazla bilgi için bkz. [Azure Data Factory roller ve izinler](concepts-roles-permissions.md).

İşbirliği dalında doğrudan iadelere izin vermeniz önerilir. Bu kısıtlama, her iade etme işleminin [özellik dalları oluşturma](source-control.md#creating-feature-branches)bölümünde açıklanan bir çekme isteği gözden geçirme süreci boyunca ilerlemesinin önlenmesi için hataları önlemeye yardımcı olabilir.

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault parolaları kullanma

Data Factory bağlı hizmetler için herhangi bir bağlantı dizesini veya parolayı ya da yönetilen kimlik kimlik doğrulamasını depolamak üzere Azure Key Vault kullanmanız önerilir. Veri Fabrikası, güvenlik nedenleriyle gizli dizileri git 'te depolamaz. Bağlı hizmetlerde parolalar gibi gizli dizileri içeren tüm değişiklikler Azure Data Factory hizmetine anında yayımlanır.

Key Vault veya MSI kimlik doğrulamasının kullanılması, Kaynak Yöneticisi Şablon dağıtımı sırasında bu gizli dizileri sağlamanız gerektiği için sürekli tümleştirme ve dağıtımı kolaylaştırır.

## <a name="troubleshooting-git-integration"></a>Git tümleştirmesiyle ilgili sorunları giderme

### <a name="stale-publish-branch"></a>Eski yayımlama dalı

Yayımla dalı ana Dalla eşitlenmemiş ve son yayımlama sırasında güncel kaynaklar içeriyorsa, aşağıdaki adımları izleyin:

1. Geçerli git deponuzu kaldırma
1. Git 'i aynı ayarlarla yeniden yapılandırın, ancak **mevcut Data Factory kaynaklarını depoya Içeri aktarma** işleminin seçili olduğundan emin olun ve **yeni dal** seçin
1. Değişiklikleri işbirliği dalıyla birleştirmek için çekme isteği oluşturun 

Aşağıda, eski bir yayımlama dalına neden olabilecek durumlara ilişkin bazı örnekler verilmiştir:
- Bir kullanıcının birden çok dalı vardır. Tek bir özellik dalında, AKV ile ilişkili olmayan bağlı bir hizmeti sildi (gitmeyen veya olmayan bağlı hizmetler, git ' te olduklarından ve özellik dalından işbirliği dalına birleştirildiğine bakılmaksızın hemen yayımlanır).
- Kullanıcı SDK veya PowerShell kullanarak veri fabrikasını değiştirdi
- Bir Kullanıcı tüm kaynakları yeni bir dala taşıdı ve ilk kez yayımlamayı denedi. Bağlı hizmetler, kaynakları içeri aktarırken el ile oluşturulmalıdır.
- Kullanıcı, bir AKG bağlı hizmetini veya bir JSON Integration Runtime el ile karşıya yükler. Veri kümesi, bağlantılı hizmet veya işlem hattı gibi başka bir kaynaktan bu kaynağa başvurur. UX aracılığıyla oluşturulan bir AKıŞ olmayan bağlantılı hizmet, kimlik bilgilerinin şifrelenmesi gerektiği için hemen yayımlanır. Bağlı hizmete başvuran bir veri kümesini karşıya yükler ve yayımlamayı denerseniz, git ortamında mevcut olduğundan UX buna izin verir. Veri Fabrikası hizmetinde mevcut olmadığından, yayımlama sırasında reddedilir.

## <a name="switch-to-a-different-git-repository"></a>Farklı bir git deposuna geçiş yap

Farklı bir git deposuna geçiş yapmak için, **kaynak denetimi** altındaki Yönetim hub 'ındaki git yapılandırma sayfasına gidin. **Bağlantıyı kes**' i seçin. 

![Git simgesi](media/author-visually/remove-repository.png)

Data Factory adınızı girin ve veri fabrikanızın ilişkili Git deposunu kaldırmak için **Onayla** ' ya tıklayın.

![Geçerli git deposundan ilişkilendirmeyi kaldırma](media/author-visually/remove-repository-2.png)

Geçerli depoyla ilişkilendirmeyi kaldırdıktan sonra, git ayarlarınızı farklı bir depoyu kullanacak şekilde yapılandırabilir ve ardından mevcut Data Factory kaynaklarını yeni depoya içeri aktarabilirsiniz.

> [!IMPORTANT]
> Bir veri fabrikasından git yapılandırmasını kaldırma, depodan herhangi bir şeyi silmez. Fabrika, yayımlanan tüm kaynakları içerecektir. Fabrikaya doğrudan hizmete karşı düzenleme yapmaya devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* İşlem hatlarını izleme ve yönetme hakkında daha fazla bilgi edinmek için bkz. işlem [hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md).
* Sürekli tümleştirme ve dağıtım uygulamak için [Azure Data Factory Içinde sürekli tümleştirme ve teslim (CI/CD)](continuous-integration-deployment.md)konusuna bakın.
