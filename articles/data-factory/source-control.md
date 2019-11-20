---
title: Azure Data Factory kaynak denetimi
description: Azure Data Factory 'da kaynak denetimini yapılandırmayı öğrenin
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 5f497bd06868d586f8378cb81e870a750b8a1670
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122889"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory kaynak denetimi

Azure Data Factory Kullanıcı arabirimi deneyimi (UX), Visual Authoring için iki deneyimle sahiptir:

- Data Factory hizmeti ile doğrudan yazar
- Azure Repos git veya GitHub tümleştirmesiyle yazar

> [!NOTE]
> Azure Kamu bulutunda yalnızca Data Factory hizmeti ile doğrudan yazma desteklenir.

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory hizmeti ile doğrudan yazar

Data Factory hizmetiyle doğrudan yazma sırasında, değişiklikleri kaydetmek için tek yol **Tümünü Yayımla** düğmesi aracılığıyla yapılır. Tıklatıldıktan sonra, yaptığınız tüm değişiklikler doğrudan Data Factory hizmetine yayımlanır. 

![Yayımlama modu](media/author-visually/data-factory-publish.png)

Data Factory hizmeti ile doğrudan yazma aşağıdaki sınırlamalara sahiptir:

- Data Factory hizmeti, değişikliklerinizin JSON varlıklarını depolamak için bir depo içermez.
- Data Factory hizmeti işbirliği veya sürüm denetimi için en iyi duruma getirilmemiştir.

> [!NOTE]
> Git deposu yapılandırıldığında Data Factory hizmeti ile doğrudan yazma Azure Data Factory UX içinde devre dışıdır. Değişiklikler doğrudan hizmete PowerShell veya SDK aracılığıyla yapılabilir.

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos git tümleştirmesiyle yazar

Azure Repos git tümleştirmesi ile görsel yazma, Veri Fabrikası işlem hatlarınız üzerinde iş için kaynak denetimi ve işbirliğini destekler. Kaynak denetimi, işbirliği, sürüm oluşturma vb. için bir Azure Repos git kuruluş deposu ile bir veri fabrikası ilişkilendirebilirsiniz. Tek bir Azure Repos git kuruluşunda birden çok depo bulunabilir, ancak bir Azure Repos git deposu yalnızca bir veri fabrikası ile ilişkilendirilebilir. Azure Repos kuruluşunuz veya deponuz yoksa kaynaklarınızı oluşturmak için [Bu yönergeleri](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) izleyin.

> [!NOTE]
> Betik ve veri dosyalarını bir Azure Repos git deposunda saklayabilirsiniz. Ancak, dosyaları Azure depolama 'ya el ile yüklemeniz gerekir. Data Factory işlem hattı, bir Azure Repos git deposunda depolanan betiği veya veri dosyalarını Azure depolama 'ya otomatik olarak yüklemez.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory ile Azure Repos git deposu yapılandırma

Bir Azure Repos Git deposunu, veri fabrikasının iki yöntemi aracılığıyla yapılandırabilirsiniz.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Yapılandırma yöntemi 1: Azure Data Factory giriş sayfası

Azure Data Factory giriş sayfasında **kod deposunu ayarla**' yı seçin.

![Azure Repos kod deposu yapılandırma](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Yapılandırma yöntemi 2: UX yazma tuvali
Azure Data Factory UX yazma tuvalinde **Data Factory** açılan menüsünü ve ardından **kod deposunu ayarla**' yı seçin.

![UX yazma için kod deposu ayarlarını yapılandırma](media/author-visually/configure-repo-2.png)

Her iki yöntem de depo ayarları yapılandırma bölmesini açar.

![Kod deposu ayarlarını yapılandırma](media/author-visually/repo-settings.png)

Yapılandırma bölmesinde aşağıdaki Azure Repos kod deposu ayarları gösterilmektedir:

| Ayar | Açıklama | Değer |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü.<br/> | Azure DevOps git veya GitHub |
| **Azure Active Directory** | Azure AD kiracı adınız. | `<your tenant name>` |
| **Azure Repos kuruluş** | Azure Repos kuruluşunuzun adı. Azure Repos kuruluşunuzun adını `https://{organization name}.visualstudio.com`adresinde bulabilirsiniz. Visual Studio profilinize erişmek ve depolarınızı ve projelerinizi görmek için [Azure Repos kuruluşunuzda oturum](https://www.visualstudio.com/team-services/git/) açabilirsiniz. | `<your organization name>` |
| **ProjectName** | Azure Repos projenizin adı. Azure Repos projenizin adını `https://{organization name}.visualstudio.com/{project name}`adresinde bulabilirsiniz. | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos kod deposu adınız. Azure Repos projeler, projeniz büyüdükçe kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya projenizde zaten var olan bir depoyu kullanabilirsiniz. | `<your Azure Repos code repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan Azure Repos işbirliği dalı. Varsayılan olarak, `master`. Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. | `<your collaboration branch name>` |
| **Kök klasör** | Azure Repos işbirliği dalınızdaki kök klasörünüz. | `<your root folder name>` |
| **Mevcut Data Factory kaynaklarını depoya aktar** | UX **yazma tuvalinden** bir Azure Repos git deposuna var olan veri fabrikası kaynaklarının içeri aktarılmayacağını belirtir. Veri Fabrikası kaynaklarınızı JSON biçiminde ilişkili git deposuna aktarmak için kutuyu seçin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır (yani, bağlı hizmetler ve veri kümeleri ayrı Jdönemleri içine aktarılır). Bu kutu seçili olmadığında, mevcut kaynaklar içeri aktarılmaz. | Seçili (varsayılan) |
| **Kaynağı içeri aktarma dalı** | Veri Fabrikası kaynaklarının (işlem hatları, veri kümeleri, bağlı hizmetler vs.) içe aktarıldığı dalı belirtir. Kaynakları şu dallardan birine içeri aktarabilirsiniz: a. İşbirliği b. Yeni c oluştur. Mevcut olanı kullan |  |

> [!NOTE]
> Microsoft Edge kullanıyorsanız ve Azure DevOps hesabı açılır listenizde herhangi bir değer görmüyorsanız, güvenilen siteler listesine https://*. VisualStudio. com ekleyin.

### <a name="use-a-different-azure-active-directory-tenant"></a>Farklı bir Azure Active Directory kiracı kullanın

Azure Repos Git deposunu farklı bir Azure Active Directory kiracısında oluşturabilirsiniz. Farklı bir Azure AD kiracısı belirtmek için, kullanmakta olduğunuz Azure aboneliği için yönetici izinlerinizin olması gerekir.

### <a name="use-your-personal-microsoft-account"></a>Kişisel Microsoft hesabı kullanın

Git tümleştirmesi için kişisel Microsoft hesabı kullanmak istiyorsanız, kişisel Azure deponuzu kuruluşunuzun Active Directory bağlayabilirsiniz.

1. Kişisel Microsoft hesabı kuruluşunuzun Active Directory Konuk olarak ekleyin. Daha fazla bilgi için, bkz. [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../active-directory/b2b/add-users-administrator.md).

2. Kişisel Microsoft hesabı Azure portal oturum açın. Sonra kuruluşunuzun Active Directory geçin.

3. Kişisel deponuzu şimdi gördüğünüz Azure DevOps bölümüne gidin. Depoyu seçin ve Active Directory bağlanın.

Bu yapılandırma adımlarında sonra, Data Factory Kullanıcı arabiriminde git tümleştirmesini ayarlarken kişisel deponuz kullanılabilir.

Kuruluşunuzun Active Directory Azure Repos bağlama hakkında daha fazla bilgi için bkz. [Azure DevOps kuruluşunuzu Azure Active Directory 'e bağlama](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>GitHub tümleştirmesiyle yazar

GitHub tümleştirmesiyle Visual Authoring, Veri Fabrikası işlem hatlarınız üzerinde iş için kaynak denetimi ve işbirliğini destekler. Kaynak denetimi, işbirliği ve sürüm oluşturma için bir GitHub hesap deposuyla bir veri fabrikasını ilişkilendirebilirsiniz. Tek bir GitHub hesabının birden çok deposu olabilir, ancak bir GitHub deposu yalnızca bir veri fabrikası ile ilişkilendirilebilir. GitHub hesabınız veya deponuz yoksa kaynaklarınızı oluşturmak için [Bu yönergeleri](https://github.com/join) izleyin.

Data Factory ile GitHub tümleştirmesi hem genel GitHub (yani, [https://github.com](https://github.com)) hem de GitHub Enterprise 'u destekler. GitHub 'daki depoya okuma ve yazma izniniz olduğu sürece hem ortak hem de özel GitHub depolarını Data Factory ile birlikte kullanabilirsiniz.

Bir GitHub deposunu yapılandırmak için, kullanmakta olduğunuz Azure aboneliği için yönetici izinlerinizin olması gerekir.

Bu özelliği dokuz dakikalık bir giriş ve tanıtım için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Azure Data Factory ile GitHub deposu yapılandırma

Bir GitHub deposunu, veri fabrikasının iki yöntemi aracılığıyla yapılandırabilirsiniz.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Yapılandırma yöntemi 1: Azure Data Factory giriş sayfası

Azure Data Factory giriş sayfasında **kod deposunu ayarla**' yı seçin.

![Azure Repos kod deposu yapılandırma](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Yapılandırma yöntemi 2: UX yazma tuvali

Azure Data Factory UX yazma tuvalinde **Data Factory** açılan menüsünü ve ardından **kod deposunu ayarla**' yı seçin.

![UX yazma için kod deposu ayarlarını yapılandırma](media/author-visually/configure-repo-2.png)

Her iki yöntem de depo ayarları yapılandırma bölmesini açar.

![GitHub depo ayarları](media/author-visually/github-integration-image2.png)

Yapılandırma bölmesinde aşağıdaki GitHub deposu ayarları gösterilmektedir:

| **Ayar** | **Açıklama**  | **Değer**  |
|:--- |:--- |:--- |
| **Depo türü** | Azure Repos kod deposunun türü. | GitHub |
| **GitHub Enterprise kullanma** | GitHub Enterprise 'ı seçme onay kutusu | seçilmemiş (varsayılan) |
| **GitHub Enterprise URL 'SI** | GitHub Enterprise root URL 'SI. Örneğin: https://github.mydomain.com. Yalnızca **GitHub Enterprise kullan** seçilirse gereklidir | `<your GitHub enterprise url>` |                                                           
| **GitHub hesabı** | GitHub hesabınızın adı. Bu ad https 'den bulunabilir:\//GitHub.exe com/{Account Name}/{Repository Name}. Bu sayfaya gidildiğinde GitHub hesabınıza GitHub OAuth kimlik bilgilerini girmeniz istenir. | `<your GitHub account name>` |
| **Depo adı**  | GitHub kod deposu adınız. GitHub hesapları, kaynak kodunuzu yönetmek için Git depoları içerir. Yeni bir depo oluşturabilir veya hesabınızda zaten var olan bir depoyu kullanabilirsiniz. | `<your repository name>` |
| **İşbirliği dalı** | Yayımlamak için kullanılan GitHub işbirliği dalınız. Varsayılan olarak, ana. Başka bir daldan kaynak yayınlamak istiyorsanız bu ayarı değiştirin. | `<your collaboration branch>` |
| **Kök klasör** | GitHub işbirliği dalınızdaki kök klasörünüz. |`<your root folder name>` |
| **Mevcut Data Factory kaynaklarını depoya aktar** | UX yazma tuvalinden bir GitHub deposuna var olan veri fabrikası kaynaklarının içeri aktarılmayacağını belirtir. Veri Fabrikası kaynaklarınızı JSON biçiminde ilişkili git deposuna aktarmak için kutuyu seçin. Bu eylem, her kaynağı ayrı ayrı dışarı aktarır (yani, bağlı hizmetler ve veri kümeleri ayrı Jdönemleri içine aktarılır). Bu kutu seçili olmadığında, mevcut kaynaklar içeri aktarılmaz. | Seçili (varsayılan) |
| **Kaynağı içeri aktarma dalı** | Veri Fabrikası kaynaklarının (işlem hatları, veri kümeleri, bağlı hizmetler vs.) içe aktarıldığı dalı belirtir. Kaynakları şu dallardan birine içeri aktarabilirsiniz: a. İşbirliği b. Yeni c oluştur. Mevcut olanı kullan |  |

### <a name="known-github-limitations"></a>Bilinen GitHub sınırlamaları

- Betik ve veri dosyalarını bir GitHub deposunda saklayabilirsiniz. Ancak, dosyaları Azure depolama 'ya el ile yüklemeniz gerekir. Data Factory işlem hattı, GitHub deposunda depolanan betiği veya veri dosyalarını Azure depolama 'ya otomatik olarak yüklemez.

- 2\.14.0 'den eski bir sürüme sahip GitHub Enterprise, Microsoft Edge tarayıcısında çalışmaz.

- Data Factory görsel yazma araçları ile GitHub tümleştirmesi yalnızca, Data Factory genel kullanıma sunulan sürümünde çalışmaktadır.

## <a name="switch-to-a-different-git-repo"></a>Farklı bir git deposuna geç

Farklı bir git deposuna geçiş yapmak için, Data Factory Genel Bakış sayfasının sağ üst köşesindeki **Git deposu ayarları** simgesine tıklayın. Simgeyi göremiyorsanız, yerel tarayıcı önbelleğinizi temizleyin. Geçerli depoyla ilişkilendirmeyi kaldırmak için simgeyi seçin.

![Git simgesi](media/author-visually/remove-repo.png)

Depo ayarları bölmesi göründüğünde **Git 'ı kaldır**' ı seçin. Data Factory adınızı girin ve veri fabrikanızın ilişkili Git deposunu kaldırmak için **Onayla** ' ya tıklayın.

![Geçerli git deposundan ilişkilendirmeyi kaldırma](media/author-visually/remove-repo2.png)

Geçerli depoyla ilişkilendirmeyi kaldırdıktan sonra, git ayarlarınızı farklı bir depoyu kullanacak şekilde yapılandırabilir ve ardından mevcut Data Factory kaynaklarını yeni depoya içeri aktarabilirsiniz. 

## <a name="version-control"></a>Sürüm denetimi

Sürüm denetimi sistemleri ( _kaynak denetimi_olarak da bilinir), geliştiricilerin kod üzerinde işbirliği yapmasına ve kod tabanında yapılan değişiklikleri izlemesine izin verir. Kaynak denetimi, çoklu geliştirici projelerine yönelik temel bir araçtır.

### <a name="creating-feature-branches"></a>Özellik dalları oluşturma

Bir veri fabrikası ile ilişkili her bir Azure Repos git deposu bir işbirliği dalına sahiptir. (`master` varsayılan işbirliği daldır). Kullanıcılar, dal açılan menüsünde **+ yeni dal** ' a tıklayarak da özellik dalları oluşturabilir. Yeni dal bölmesi göründüğünde, özellik dalınızın adını girin.

![Yeni dal oluştur](media/author-visually/new-branch.png)

Özellik dalınızdaki değişiklikleri işbirliği dalınızla birleştirmeye hazırsanız, dal açılan listesine tıklayın ve **çekme Isteği oluştur**' u seçin. Bu eylem, çekme istekleri oluşturabileceğiniz, kod İncelemeleri yapabileceğiniz ve değişiklikleri işbirliği dalınızda birleştirebileceğiniz git Azure Repos için sizi yönlendirir. (`master` varsayılandır). Yalnızca Data Factory hizmetine işbirliği dalınızdan yayımlama izni verilir. 

![Yeni bir çekme isteği oluştur](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Yayımlama ayarlarını yapılandırma

Yayımla dalını yapılandırmak için-diğer bir deyişle, Kaynak Yöneticisi şablonlarının kaydedildiği dal-işbirliği dalında kök klasöre bir `publish_config.json` dosyası ekleyin. Data Factory, bu dosyayı okur, alanı `publishBranch`arar ve belirtilen değere sahip yeni bir dal (zaten yoksa) oluşturur. Ardından, tüm Kaynak Yöneticisi şablonlarını belirtilen konuma kaydeder. Örneğin:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Yeni bir yayımlama dalı belirttiğinizde, Data Factory önceki yayımlama dalını silmez. Önceki yayımlama dalını kaldırmak istiyorsanız el ile silin.

> [!NOTE]
> Data Factory, fabrikası yüklerken yalnızca `publish_config.json` dosyayı okur. Portalda zaten fabrika yüklüyse, değişikliklerinizin geçerli olması için tarayıcıyı yenileyin.

### <a name="publish-code-changes"></a>Kod değişikliklerini Yayımla

İşbirliği dalındaki değişiklikleri (`master` varsayılan) birleştirdikten sonra, kod değişikliklerinizi ana dalda Data Factory hizmetine el ile yayımlamak için **Yayımla** ' ya tıklayın.

![Değişiklikleri Data Factory hizmette yayımlayın](media/author-visually/publish-changes.png)

Yayın dalının ve bekleyen değişikliklerin doğru olduğunu onaylamak için bir yan bölme açılır. Değişikliklerinizi doğruladıktan sonra, yayımlamayı onaylamak için **Tamam** ' ı tıklatın.

![Doğru yayımlama dalını onaylayın](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Ana dal, Data Factory hizmetine dağıtılan özellikleri temsil etmez. Ana dalın Data Factory hizmetine el ile yayımlanması *gerekir* .

## <a name="advantages-of-git-integration"></a>Git tümleştirmesinin avantajları

-   **Kaynak denetimi**. Data Factory iş yükleriniz önemli olmaya yönelik olarak, aşağıdaki gibi çeşitli kaynak denetimi avantajlarından yararlanmak için fabrikanızı git ile tümleştirmeniz gerekir:
    -   Değişiklikleri izleme/denetleme özelliği.
    -   Hataları ortaya çıkan değişiklikleri döndürmenize olanak tanır.
-   **Kısmi olarak kaydedilir**. Fabrikanızdaki çok sayıda değişiklik yaparken, sizin için yaptığınız değişiklikleri taslak olarak kaydedememeniz veya bilgisayarınızın çöktüğü durumunda yaptığınız değişiklikleri kaybetmek istemediğiniz fark edersiniz. Git tümleştirmesi sayesinde yaptığınız değişiklikleri artımlı olarak kaydetmeye devam edebilir ve yalnızca hazırsanız fabrika üzerinde yayımlayabilirsiniz. Git, yaptığınız değişiklikleri sizin için test edinceye kadar çalışmanız için bir hazırlama yeri görevi görür.
-   **İşbirliği ve denetim**. Aynı fabrikaya katılan birden fazla ekip üyesine sahipseniz, bir kod inceleme işlemi aracılığıyla ekip malarınızın birbirleriyle işbirliği yapmasına izin vermek isteyebilirsiniz. Fabrikanızın, fabrikaya katkıda bulunan her katkıda bulunan fabrika için dağıtım iznine sahip olduğu şekilde de ayarlayabilirsiniz. Takım üyelerinin yalnızca git aracılığıyla değişiklik yapmasına izin verilebilir, ancak yalnızca ekipteki belirli kişilerin fabrika değişikliklerini "yayımlamasına" izin verilir.
-   **Difüzler gösteriliyor**. Git modunda, fabrikada yayımlanmakta olan yükün iyi bir farkını görmeniz gerekir. Bu fark, fabrikanıza son kez yayımladığınızdan beri değiştirilen/eklenen/silinen tüm kaynakları/varlıkları gösterir. Bu fark temelinde, yayımlama ile devam edebilir veya geri dönüp değişikliklerinizi denetleyebilir ve sonra daha sonra geri dönebilirsiniz.
-   **Daha ıyı CI/CD**. Git modunu kullanıyorsanız, geliştirme fabrikasında yapılan herhangi bir değişiklik olduğu anda yayın işlem hattınızı otomatik olarak tetikleyebilmeniz için yapılandırabilirsiniz. Ayrıca, fabrikanızın Kaynak Yöneticisi şablonunda parametreler olarak kullanılabilen özellikleri de özelleştirmeniz gerekir. Yalnızca gerekli özellikler kümesini parametre olarak tutmak ve her şeyin sabit olarak kodlanmasını sağlamak yararlı olabilir.
-   **Daha Iyi performans**. Bir ortalama fabrika, kaynaklar git aracılığıyla indirildiğinden git modunda, normal canlı moddan çok daha hızlı yüklenir.

## <a name="best-practices-for-git-integration"></a>Git tümleştirmesi için en iyi uygulamalar

### <a name="permissions"></a>İzinler

Genellikle, her bir takım üyesinin fabrikası güncelleştirme izinlerine sahip olmasını istemezsiniz. Aşağıdaki izin ayarları önerilir:

*   Tüm takım üyeleri, Veri Fabrikası için okuma izinlerine sahip olmalıdır.
*   Yalnızca bir SELECT kişinin fabrikada yayımlamasına izin verilmelidir. Bunu yapmak için, fabrikada **Data Factory katkıda** bulunan rolüne sahip olmaları gerekir. İzinler hakkında daha fazla bilgi için bkz. [Azure Data Factory roller ve izinler](concepts-roles-permissions.md).
   
işbirliği dalında doğrudan iadelere izin verme önerilir. Bu kısıtlama, her iade etme işlemi bir çekme Isteği sürecinden geçmesi durumunda hataları önlemeye yardımcı olabilir.

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault parolaları kullanma

Data Factory bağlı hizmetler için bağlantı dizelerini veya parolaları depolamak üzere Azure Key Vault kullanılması önerilir. Güvenlik nedenleriyle, bu tür gizli bilgileri git 'te depolamadığımızda, bağlantılı hizmetlerde yapılan tüm değişiklikler Azure Data Factory hizmetine anında yayımlanır.

Key Vault kullanmak, Kaynak Yöneticisi Şablon dağıtımı sırasında bu gizli dizileri sağlamanız gerektiği için sürekli tümleştirme ve dağıtımı kolaylaştırır.

## <a name="troubleshooting-git-integration"></a>Git tümleştirmesi sorunlarını giderme

### <a name="stale-publish-branch"></a>Eski yayımlama dalı

Yayımla dalı ana Dalla eşitlenmemiş ve son bir yayımlamaya rağmen güncel olmayan kaynaklar içeriyorsa, aşağıdaki adımları izleyin:

1. Geçerli git deponuzu kaldırma
1. Git 'i aynı ayarlarla yeniden yapılandırın, ancak **mevcut Data Factory kaynaklarını depoya Içeri aktarma** işleminin seçili olduğundan emin olun ve **yeni dal** seçin
1. İşbirliği dalınızdan tüm kaynakları silme
1. İşbirliği dalında yapılan değişiklikleri birleştirmek için bir çekme Isteği oluşturun 

## <a name="provide-feedback"></a>Geri bildirimde bulunma
Özellikler hakkında yorum yapmak veya araçla ilgili sorunları bildirmek için **geri bildirim** ' ı seçin:

![Geri Bildirim](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Sonraki adımlar

* İşlem hatlarını izleme ve yönetme hakkında daha fazla bilgi edinmek için bkz. işlem [hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md).
* Sürekli tümleştirme ve dağıtım uygulamak için [Azure Data Factory Içinde sürekli tümleştirme ve teslim (CI/CD)](continuous-integration-deployment.md)konusuna bakın.
