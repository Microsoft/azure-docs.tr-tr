---
title: SYNAPSE çalışma alanı için sürekli tümleştirme ve teslim
description: Çalışma alanındaki değişiklikleri bir ortamdan (geliştirme, test, üretim) diğerine dağıtmak için sürekli tümleştirme ve teslimi nasıl kullanacağınızı öğrenin.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739683"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure SYNAPSE çalışma alanı için sürekli tümleştirme ve teslim

## <a name="overview"></a>Genel Bakış

Sürekli tümleştirme (CI), bir takım üyesinin sürüm denetimine değişiklik yaptığı her seferinde kodun derlemesini ve test edilmesini otomatikleştirme işlemidir. Sürekli dağıtım (CD), üretim ortamına birden çok test veya hazırlık ortamından derleme, test etme, yapılandırma ve dağıtma işlemidir.

Azure SYNAPSE Analytics çalışma alanında sürekli tümleştirme ve teslim (CI/CD), tüm varlıkları bir ortamdan (geliştirme, test, üretim) başka bir ortama taşıdır. Çalışma alanınızı başka bir çalışma alanına yükseltmek için iki bölüm vardır. İlk olarak, çalışma alanı kaynaklarını (havuzlar ve çalışma alanı) oluşturmak veya güncelleştirmek için bir [Azure Resource Manager şablonu (ARM şablonu)](../../azure-resource-manager/templates/overview.md) kullanın. Ardından, yapıtları (SQL betikleri, Not defteri, Spark iş tanımı, işlem hatları, veri kümeleri, veri akışları vb.) Azure DevOps 'daki Azure SYNAPSE Analytics CI/CD araçlarıyla geçirin. 

Bu makalede, Azure SYNAPSE çalışma alanının birden çok ortama dağıtımını otomatikleştirmek için bir Azure DevOps sürüm işlem hattının nasıl kullanılacağı özetlenmektedir.

## <a name="prerequisites"></a>Önkoşullar

Azure SYNAPSE çalışma alanının birden çok ortama dağıtımını otomatikleştirmek için bu ön koşullar ve yapılandırmaların yerinde olması gerekir.

### <a name="azure-devops"></a>Azure DevOps

- Yayın işlem hattını çalıştırmaya yönelik bir Azure DevOps projesi hazırlandı.
- [Kuruluş düzeyinde kod "temel" erişimini iade edecek tüm kullanıcılara](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page), depoyu görebilmesi Için izin verin.
- Azure SYNAPSE depoya sahip hakları verin.
- Şirket içinde barındırılan bir Azure DevOps VM Aracısı oluşturmuş olduğunuzdan emin olun veya bir Azure DevOps barındırılan Aracısı kullanın.
- [Kaynak grubu için Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)izinleri.
- Azure Active Directory (Azure AD) yöneticisinin Azure DevOps [SYNAPSE çalışma alanı Deployment Agent uzantısını Azure DevOps kuruluşunda yüklemesi](/azure/devops/marketplace/install-extension)gerekir.
- İşlem hattının çalışması için mevcut bir hizmet hesabı oluşturun veya aday yapın. Bir hizmet hesabı yerine kişisel erişim belirteci kullanabilirsiniz, ancak kullanıcı hesabı silindikten sonra işlem hatlarınız çalışmaz.

### <a name="azure-active-directory"></a>Azure Active Directory

- Azure AD 'de, dağıtım için kullanılacak bir hizmet sorumlusu oluşturun. SYNAPSE çalışma alanı Dağıtım görevi, bir yönetilen kimliği 1 * ve daha önceki bir sürümde kullanmayı desteklemez.
- Bu eylem için Azure AD yönetici hakları gereklidir.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Aynı işlem hattını, ARM şablonunu veya Azure CLı 'yı kullanarak bu önkoşulları otomatikleştirebilir ve dağıtabilirsiniz, ancak işlem bu makalede açıklanmamaktadır.

- Geliştirme için kullanılan "kaynak" çalışma alanının, SYNAPSE Studio 'daki bir git deposu ile yapılandırılması gerekir. Daha fazla bilgi için bkz. [SYNAPSE Studio 'Da kaynak denetimi](source-control.md#configuration-method-2-manage-hub).

- Dağıtılacak boş bir çalışma alanı. Boş çalışma alanını ayarlamak için:

  1. Yeni bir Azure SYNAPSE Analytics çalışma alanı oluşturun.
  1. Yeni çalışma alanının barındırıldığı kaynak grubuna VM aracısına ve hizmet sorumlusu katkıda bulunan haklarına izin verin.
  1. Yeni çalışma alanında git deposu bağlantısını yapılandırmayın.
  1. Azure portal, yeni Azure SYNAPSE Analytics çalışma alanını bulun ve kendinize ve Azure DevOps işlem hattı Azure SYNAPSE Analytics çalışma alanı sahibi haklarını çalıştırın. 
  1. Azure DevOps VM aracısını ve hizmet sorumlusunu çalışma alanı için katkıda bulunan rolüne ekleyin (Bu devralınmalıdır, ancak olduğunu doğrulayın).
  1. Azure SYNAPSE Analytics çalışma alanında, **Studio**  >  **Yönet**  >  **IAM** bölümüne gidin. Azure DevOps VM aracısını ve hizmet sorumlusunu çalışma alanı yöneticileri grubuna ekleyin.
  1. Çalışma alanı için kullanılan depolama hesabını açın. IAM 'de, VM aracısını ve hizmet sorumlusunu, Depolama Blobu veri katılımcısı rolüne ekleyin.
  1. Destek aboneliğinde bir Anahtar Kasası oluşturun ve hem var olan çalışma alanının hem de yeni çalışma alanının kasada en azından GET ve LIST izinlerine sahip olduğundan emin olun.
  1. Otomatik dağıtımın çalışması için, bağlı hizmetlerinize belirtilen bağlantı dizelerinin anahtar kasasında olduğundan emin olun.

### <a name="additional-prerequisites"></a>Ek önkoşullar
 
 - Spark havuzları ve şirket içinde barındırılan tümleştirme çalışma zamanları bir ardışık düzende oluşturulmaz. Şirket içinde barındırılan tümleştirme çalışma zamanı kullanan bağlı bir hizmetiniz varsa, bunu yeni çalışma alanında el ile oluşturun.
 - Not defterleri geliştirmekte ve bunları bir Spark havuzuna bağladıysanız, Spark havuzunu çalışma alanında yeniden oluşturun.
 - Bir ortamda mevcut olmayan bir Spark havuzuna bağlı olan Not defterleri dağıtılır.
 - Spark havuzu adları her iki çalışma alanında da aynı olmalıdır.
 - Tüm veritabanları, SQL havuzları ve diğer kaynakları her iki çalışma alanında da adlandırın.
 - Dağıtmaya çalıştığınızda sağlanan SQL havuzlarınız duraklatıldıysa, dağıtım başarısız olabilir.

Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics Bölüm 4 ' te CI CD-yayın Işlem hattı](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Yayın işlem hattı ayarlama

1.  [Azure DevOps](https://dev.azure.com/)'da, yayın için oluşturulan projeyi açın.

1.  Sayfanın sol tarafında, işlem **hatları**' nı seçin ve ardından **yayınlar**' ı seçin.

    ![İşlem hatları, yayınlar seçin](media/create-release-1.png)

1.  **Yeni işlem hattı**' nı seçin veya mevcut işlem hatlarınız varsa **Yeni** ' yi ve ardından **Yeni yayın** işlem hattını seçin.

1.  **Boş iş** şablonunu seçin.

    ![Boş işi seçin](media/create-release-select-empty.png)

1.  **Aşama adı** kutusuna ortamınızın adını girin.

1.  **Yapıt Ekle**' yi seçin ve ardından geliştirme SYNAPSE Studio ile yapılandırılmış Git deposunu seçin. Havuzların ve çalışma alanının ARM şablonunu yönetmek için kullandığınız Git deposunu seçin. Kaynak olarak GitHub kullanıyorsanız, GitHub hesabınız ve çekme depolarınız için bir hizmet bağlantısı oluşturmanız gerekir. [Hizmet bağlantısı](/azure/devops/pipelines/library/service-endpoints) hakkında daha fazla bilgi için 

    ![Yayın dalı Ekle](media/release-creation-github.png)

1.  Kaynak güncelleştirmesi için ARM şablonunun dalını seçin. **Varsayılan sürüm** için **varsayılan daldan en son**' u seçin.

    ![ARM Şablonu Ekle](media/release-creation-arm-branch.png)

1.  **Varsayılan dal** için deponun [Yayımla dalını](source-control.md#configure-publishing-settings) seçin. Bu yayın dalı varsayılan olarak `workspace_publish` . **Varsayılan sürüm** için **varsayılan daldan en son**' u seçin.

    ![Yapıt ekleme](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Kaynak oluşturmak ve güncelleştirmek için ARM şablonu için bir aşama görevi ayarlama 

Bir Azure SYNAPSE çalışma alanı, Spark ve SQL havuzları veya bir anahtar kasası gibi bir kaynağı dağıtmaya yönelik ARM şablonunuz varsa, bu kaynakları oluşturmak veya güncelleştirmek için bir Azure Resource Manager Dağıtım görevi ekleyin:

1. Aşama görünümünde, **aşama görevlerini görüntüle**' yi seçin.

    ![Aşama görünümü](media/release-creation-stage-view.png)

1. Yeni bir görev oluşturun. **ARM şablon dağıtımı** araması yapın ve ardından **Ekle**' yi seçin.

1. Dağıtım görevinde, hedef çalışma alanı için abonelik, kaynak grubu ve konum ' u seçin. Gerekirse kimlik bilgilerini sağlayın.

1. **Eylem** listesinde, **kaynak grubunu oluştur veya Güncelleştir**' i seçin.

1. **Şablon** kutusunun yanındaki üç nokta düğmesini (**...**) seçin. Hedef çalışma alanınızın Azure Resource Manager şablonuna gözatamazsınız

1. Seç **...** **şablon parametreleri** kutusunun yanındaki parametreler dosyasını seçin.

1. Seç **...** **şablon parametrelerini geçersiz kıl** kutusunun yanında, hedef çalışma alanı için istenen parametre değerlerini girin. 

1. **Dağıtım modu** için **artımlı** ' ı seçin.
    
    ![çalışma alanı ve havuzlar dağıtım](media/pools-resource-deploy.png)

1. Seçim Çalışma alanı rol atamasını verme ve güncelleştirme için **Azure PowerShell** ekleyin. Bir Synapse çalışma alanı oluşturmak için yayın işlem hattını kullanıyorsanız, işlem hattının hizmet sorumlusu varsayılan çalışma alanı yöneticisi olarak eklenir. Diğer hesaplara çalışma alanına erişim vermek için PowerShell 'i çalıştırabilirsiniz. 
    
    ![izin ver](media/release-creation-grant-permission.png)

 > [!WARNING]
> Tüm dağıtım modunda, kaynak grubunda bulunan ancak yeni Kaynak Yöneticisi şablonunda belirtilmeyen kaynaklar **silinir**. Daha fazla bilgi için lütfen [Azure Resource Manager dağıtım modlarına](../../azure-resource-manager/templates/deployment-modes.md) başvurun

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>SYNAPSE yapıtlar dağıtımı için bir aşama görevi ayarlama 

SYNAPSE çalışma alanı, SQL betiği, Not defteri, Spark iş tanımı, dataflow, işlem hattı, bağlantılı hizmet, kimlik bilgileri ve IR (Integration Runtime) gibi diğer öğeleri dağıtmak için [SYNAPSE çalışma alanı dağıtım](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) uzantısı ' nı kullanın.  

1. **Azure DevOps marketi**'nden uzantıyı arayın ve alın (https://marketplace.visualstudio.com/azuredevops) 

     ![Uzantıyı al](media/get-extension-from-market.png)

1. Uzantıyı yüklemek için bir kuruluş seçin. 

     ![Uzantıyı yükleme](media/install-extension.png)

1. Azure DevOps işlem hattının hizmet sorumlusuna abonelik izni verildiğinden ve aynı zamanda hedef çalışma alanı için çalışma alanı yöneticisi olarak atanmış olduğundan emin olun. 

1. Yeni bir görev oluşturun. **SYNAPSE çalışma alanı dağıtımı** araması yapın ve ardından **Ekle**' yi seçin.

     ![Uzantı Ekle](media/add-extension-task.png)

1.  Görevde **..** . seçeneğini belirleyin. şablon dosyasını seçmek için **şablon** kutusunun yanındaki öğesini seçin.

1. Seç **...** **şablon parametreleri** kutusunun yanındaki parametreler dosyasını seçin.

1. Hedef çalışma alanının bağlantısını, kaynak grubunu ve adını seçin. 

1. Seç **...** **şablon parametrelerini geçersiz kıl** kutusunun yanında, bağlantı dizeleri ve bağlı hizmetleriniz için kullanılan hesap anahtarları dahil olmak üzere hedef çalışma alanı için istenen parametre değerlerini girin. [Daha fazla bilgi için buraya tıklayın] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![SYNAPSE çalışma alanı dağıtma](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD senaryolarında, farklı ortamlardaki Integration Runtime (IR) türü aynı olmalıdır. Örneğin, geliştirme ortamında şirket içinde barındırılan bir IR varsa, aynı IR, test ve üretim gibi diğer ortamlarda da kendi kendine barındırılan türde olmalıdır. Benzer şekilde, tümleştirme çalışma zamanlarını birden çok aşamada paylaşıyorsanız, tümleştirme çalışma zamanlarını, geliştirme, test ve üretim gibi tüm ortamlarda bağlanmış bir şekilde yapılandırmanız gerekir.

## <a name="create-release-for-deployment"></a>Dağıtım için yayın oluştur 

Tüm değişiklikleri kaydettikten sonra bir yayını el ile oluşturmak için **yayın oluştur** ' u seçebilirsiniz. Yayınların oluşturulmasını otomatikleştirmek için bkz. [Azure DevOps yayın Tetikleyicileri](/azure/devops/pipelines/release/triggers)

   ![Yayın oluştur ' u seçin](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Çalışma alanı şablonunun özel parametrelerini kullan 

Otomatik CI/CD kullanıyorsunuz ve dağıtım sırasında bazı özellikleri değiştirmek istiyorsunuz, ancak özellikler varsayılan olarak parametreli değildir. Bu durumda, varsayılan parametre şablonunu geçersiz kılabilirsiniz.

Varsayılan parametre şablonunu geçersiz kılmak için, git işbirliği dalınızın kök klasöründetemplate-parameters-definition.jsadlı bir dosya **olan** özel bir parametre şablonu oluşturmanız gerekir. Bu tam dosya adını kullanmanız gerekir. SYNAPSE çalışma alanı, işbirliği dalından yayımlarken, bu dosyayı okur ve parametrelerini oluşturmak için yapılandırmasını kullanır. Dosya bulunamazsa, varsayılan parametre şablonu kullanılır.

### <a name="custom-parameter-syntax"></a>Özel parametre sözdizimi

Özel parametreler dosyası oluşturmaya yönelik bazı yönergeler aşağıda verilmiştir:

* İlgili varlık türünün altında özellik yolunu girin.
* İçin bir özellik adının ayarlanması `*` , altındaki tüm özellikleri parametreleştirmek istediğinizi (özyinelemeli değil, yalnızca ilk düzeye doğru değil) gösterir. Bu yapılandırmaya özel durumlar da sağlayabilirsiniz.
* Bir özelliğin değerini dize olarak ayarlamak, özelliği parametreleştirmek istediğinizi gösterir. `<action>:<name>:<stype>` biçimini kullanın.
   *  `<action>` Şu karakterlerden biri olabilir:
      * `=` , geçerli değeri parametresi için varsayılan değer olarak tutacağı anlamına gelir.
      * `-` parametresi için varsayılan değeri saklama anlamına gelir.
      * `|` , bağlantı dizeleri veya anahtarlar için Azure Key Vault parolalar için özel bir durumdur.
   * `<name>` parametrenin adıdır. Boşsa, özelliğin adını alır. Değer bir `-` karakterle başlıyorsa, ad kısaltılmıştır. Örneğin, `AzureStorage1_properties_typeProperties_connectionString` olarak kısaltılacak `AzureStorage1_connectionString` .
   * `<stype>` parametrenin türüdür. `<stype>`Boşsa, varsayılan tür olur `string` . Desteklenen değerler: `string` , `securestring` , `int` , `bool` , `object` `secureobject` ve `array` .
* Dosyada bir dizi belirtilmesi, şablondaki eşleşen özelliğin bir dizi olduğunu gösterir. SYNAPSE, belirtilen tanımı kullanarak dizideki tüm nesneler arasında yinelenir. İkinci nesne, bir dize, her yineleme için parametresinin adı olarak kullanılan özelliğin adı olur.
* Bir tanım, kaynak örneğine özgü olamaz. Herhangi bir tanım, bu türdeki tüm kaynaklar için geçerlidir.
* Varsayılan olarak, Key Vault gizli dizileri ve bağlantı dizeleri, anahtarlar ve belirteçler gibi güvenli dizeler gibi tüm güvenli dizeler parametrelenir.

### <a name="parameter-template-definition-samples"></a>Parametre şablonu tanım örnekleri 

Aşağıda bir parametre şablonu tanımının neye benzediklerine bir örnek verilmiştir:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Yukarıdaki şablonun nasıl oluşturulduğu ve kaynak türüne göre nasıl bölündüğü hakkında bir açıklama aşağıda verilmiştir.

#### <a name="notebooks"></a>Notebooks 

* Yoldaki herhangi bir özellik `properties/bigDataPool/referenceName` varsayılan değeri ile parametrelenir. Her bir not defteri dosyası için ekli Spark havuzunu parametreleştirebilirsiniz. 

#### <a name="sql-scripts"></a>SQL Betikleri 

* Yoldaki Özellikler (poolName ve databaseName), `properties/content/currentConnection` şablondaki varsayılan değerler olmadan dize olarak parametrelenir. 

#### <a name="pipelines"></a>Pipelines

* Yoldaki herhangi bir özellik `activities/typeProperties/waitTimeInSeconds` parametrelenir. Bir işlem hattındaki (örneğin, etkinlik) bir kod düzeyi özelliği olan herhangi bir etkinlik, `waitTimeInSeconds` `Wait` varsayılan bir ada sahip bir sayı olarak parametrelendirilir. Ancak Kaynak Yöneticisi şablonunda varsayılan bir değere sahip olmaz. Kaynak Yöneticisi dağıtımı sırasında zorunlu bir giriş olacaktır.
* Benzer şekilde, adlı bir özellik `headers` (örneğin, bir `Web` etkinlikte) türü `object` (nesne) ile parametrelenir. Kaynak fabrikasının değeriyle aynı değer olan varsayılan bir değere sahiptir.

#### <a name="integrationruntimes"></a>Tümleştirme çalışma zamanları

* Yolun altındaki tüm özellikler, kendi `typeProperties` varsayılan değerleriyle parametrelenir. Örneğin, tür özellikleri altında iki özellik vardır `IntegrationRuntimes` : `computeProperties` ve `ssisProperties` . Her iki özellik türü de ilgili varsayılan değerleri ve türleri (nesne) ile oluşturulur.

#### <a name="triggers"></a>Tetikleyiciler

* Altında `typeProperties` iki özellik parametrelenir. Birincisi, `maxConcurrency` varsayılan bir değere sahip ve türünde olan bir ' dır `string` . Varsayılan parametre adı vardır `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Özelliği de parametrelenir. Bu düzeyin altında, bu düzeydeki tüm özellikler, varsayılan değerler ve parametre adlarıyla dize olarak parametreleştirime olarak belirtilir. Özel durum `interval` , tür olarak parametreleştirilen özelliktir `int` . Parametre adı ile sondüzeltildi `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Benzer şekilde, `freq` özelliği bir dizedir ve dize olarak parametrelenir. Ancak, `freq` özelliği varsayılan değer olmadan parametrelenir. Ad kısaltılmıştır ve Sonya düzeltildi. Örneğin, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Bağlı hizmetler benzersizdir. Bağlı hizmetler ve veri kümelerinin çok sayıda türü olduğundan, türe özgü özelleştirme sağlayabilirsiniz. Bu örnekte, türündeki tüm bağlı hizmetler için `AzureDataLakeStore` belirli bir şablon uygulanır. Tüm diğerleri için (aracılığıyla `*` ), farklı bir şablon uygulanır.
* `connectionString`Özelliği bir değer olarak parametrelendirilecektir `securestring` . Varsayılan bir değere sahip olmayacaktır. Bu, ile Sonekli bir kısaltılmış parametre adı olacaktır `connectionString` .
* Özelliği `secretAccessKey` bir `AzureKeyVaultSecret` (örneğin, bir Amazon S3 bağlantılı hizmetinde) olur. Otomatik olarak Azure Key Vault gizli dizi olarak parametrelenir ve yapılandırılan anahtar kasasından alınır. Ayrıca, anahtar kasasının kendisini parametreleştirebilirsiniz.

#### <a name="datasets"></a>Veri kümeleri

* Veri kümeleri için türe özgü özelleştirme kullanılabilir olsa da, açıkça bir düzeyi yapılandırması olmadan yapılandırma sağlayabilirsiniz \* . Yukarıdaki örnekte, altındaki tüm veri kümesi özellikleri `typeProperties` parametrelenir.


## <a name="best-practices-for-cicd"></a>CI/CD için en iyi yöntemler

Azure SYNAPSE çalışma alanınız ile git tümleştirmesi kullanıyorsanız ve değişikliklerinizi geliştirmeden test ve daha sonra üretime taşıyan bir CI/CD işlem hattına sahipseniz, bu en iyi yöntemleri öneririz:

-   **Git tümleştirmesi**. Git tümleştirmesi ile yalnızca geliştirme Azure SYNAPSE çalışma alanınızı yapılandırın. Test ve üretim çalışma alanlarındaki değişiklikler CI/CD aracılığıyla dağıtılır ve git tümleştirmesi gerekmez.
-   **Yapıları yapıtları geçişten önce hazırlayın**. Geliştirme çalışma alanındaki havuzlara eklenmiş SQL betiği veya Not defteriniz varsa, farklı ortamlardaki havuzların adı da beklenmektedir. 
-   **Kod olarak altyapı (IAC)**. Bir açıklayıcı modelde altyapının (ağlar, sanal makineler, yük dengeleyiciler ve bağlantı topolojisi) yönetimi, DevOps ekibinin kaynak kodu için kullandığı sürüm oluşturma 'yı kullanır. 
-   **Diğerleri**. Bkz. [ADF yapıtları için en iyi uygulamalar](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Yapıt dağıtımı sorunlarını giderme 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Azure SYNAPSE Analytics çalışma alanı dağıtım görevini kullanma

Azure SYNAPSE Analytics 'te ARM kaynakları olmayan bir dizi yapıt vardır. Bu Azure Data Factory farklıdır. ARM şablonu Dağıtım görevi Azure SYNAPSE Analytics yapıtları dağıtmak için düzgün çalışmaz.
 
### <a name="unexpected-token-error-in-release"></a>Yayında beklenmeyen belirteç hatası

Parametre dosyanızda, kaçış olmayan parametre değerleri varsa, yayın işlem hattı dosyayı ayrıştırmaz ve "beklenmeyen belirteç" hatasını üretir. Parametre değerlerini almak için, parametreleri geçersiz kılmayı veya Azure Key Vault kullanmanızı öneririz. Geçici çözüm olarak çift kaçış karakterleri de kullanabilirsiniz.
