---
title: Azure Data Factory’de sürekli tümleştirme ve teslim
description: Data Factory işlem hatlarını bir ortamdan (geliştirme, test, üretim) diğerine taşımak için sürekli tümleştirme ve teslimi nasıl kullanacağınızı öğrenin.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 7ffcb93493ada36df59c0a8305e941176e8320b8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121481"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory’de sürekli tümleştirme ve teslim

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Genel Bakış

Sürekli tümleştirme, kod tabanınızda yapılan her değişikliği otomatik olarak ve mümkün olduğunca erken test etme yöntemidir. Sürekli teslim, sürekli tümleştirme sırasında yapılan testleri izler ve değişiklikleri hazırlama veya üretim sistemine gönderir.

Azure Data Factory'de sürekli tümleştirme ve sürekli teslim (CI/CD) Data Factory işlem hatlarını bir ortamdan (geliştirme, test, üretim) diğerine taşıma anlamına gelir. Azure Data Factory, çeşitli ADF varlıklarınızın yapılandırmasını depolamak için [Azure Resource Manager şablonlarından](../azure-resource-manager/templates/overview.md) yararlanır (işlem hatları, veri kümeleri, veri akışları vb.). Bir veri fabrikasını başka bir ortama yükseltmek için önerilen iki yöntem vardır:

-    Data Factory [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) tümleştirme kullanılarak otomatik dağıtım
-    Azure Resource Manager ile Data Factory UX tümleştirmesi kullanarak Kaynak Yöneticisi şablonunu el ile karşıya yükleyin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD yaşam döngüsü

Aşağıda, Azure Repos git ile yapılandırılmış bir Azure Data Factory 'deki CI/CD yaşam döngüsüne örnek bir genel bakış sunulmaktadır. Git deposunu yapılandırma hakkında daha fazla bilgi için bkz. [Azure Data Factory kaynak denetimi](source-control.md).

1.  Bir geliştirme Veri Fabrikası oluşturulup Azure Repos git ile yapılandırılır. Tüm geliştiricilerin işlem hatları ve veri kümeleri gibi Data Factory kaynaklarını yazmak için izni olmalıdır.

1.  Geliştirici bir değişiklik yapmak için [bir özellik dalı oluşturur](source-control.md#creating-feature-branches) . Bunların işlem hattı çalıştırmalarının en son değişiklikleriyle hata ayıklaması yapılır. İşlem hattı çalıştırmasında hata ayıklama hakkında daha fazla bilgi için, bkz. [Azure Data Factory yinelemeli geliştirme ve hata ayıklama](iterative-development-debugging.md).

1.  Bir geliştirici değişiklikleri ile memnun olduktan sonra, yaptıkları değişikliklerin eşler tarafından gözden geçirilmesini sağlamak için özellik dalından ana veya işbirliği dalına bir çekme isteği oluşturur.

1.  Bir çekme isteği onaylandıktan ve değişiklikler ana dalda birleştirildikten sonra, değişiklikler geliştirme fabrikasına yayımlanır.

1.  Takım, değişiklikleri bir test veya UAT (Kullanıcı kabul testi) fabrikasında dağıtmaya hazırsa, takım Azure Pipelines sürümüne gider ve geliştirme fabrikasının istenen sürümünü UıAT 'ya dağıtır. Bu dağıtım Azure Pipelines görevin bir parçası olarak gerçekleşir ve uygun yapılandırmayı uygulamak için Kaynak Yöneticisi Şablon parametrelerini kullanır.

1.  Test fabrikasında değişiklikler doğrulandıktan sonra, işlem hattı sürümünün sonraki görevini kullanarak üretim fabrikasına dağıtın.

> [!NOTE]
> Yalnızca geliştirme fabrikası bir git deposu ile ilişkilendirilir. Test ve üretim fabrikalarının kendileriyle ilişkili bir git deposu olmaması ve yalnızca bir Azure DevOps işlem hattı veya bir kaynak yönetimi şablonu aracılığıyla güncelleştirilmeleri gerekir.

Aşağıdaki görüntüde bu yaşam döngüsünün farklı adımları vurgulanmıştır.

![Azure Pipelines ile sürekli tümleştirme diyagramı](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure Pipelines sürümlerini kullanarak sürekli tümleştirmeyi otomatikleştirin

Aşağıda, bir veri fabrikasının birden çok ortama dağıtımını otomatikleştiren bir Azure Pipelines sürümü ayarlamaya yönelik bir kılavuz verilmiştir.

### <a name="requirements"></a>Gereksinimler

-   [Azure Resource Manager hizmeti uç noktasını](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)kullanan Visual Studio Team Foundation Server veya Azure Repos bağlantılı bir Azure aboneliği.

-   Azure Repos git tümleştirmesi ile yapılandırılmış bir veri fabrikası.

-   Her ortam için gizli dizileri içeren bir [Azure Anahtar Kasası](https://azure.microsoft.com/services/key-vault/) .

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines yayını ayarlama

1.  [Azure DevOps](https://dev.azure.com/)'da, Data Factory 'niz ile yapılandırılmış projeyi açın.

1.  Sayfanın sol tarafında, işlem **hatları**' nı seçin ve ardından **yayınlar**' ı seçin.

    ![İşlem hatları, yayınlar seçin](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **Yeni işlem hattı**' nı seçin veya mevcut işlem hatlarınız varsa **Yeni** ' yi ve ardından **Yeni yayın** işlem hattını seçin.

1.  **Boş iş** şablonunu seçin.

    ![Boş işi seçin](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **Aşama adı** kutusuna ortamınızın adını girin.

1.  **Yapıt Ekle**' yi seçin ve ardından geliştirme veri fabrikayla yapılandırılmış Git deposunu seçin. **Varsayılan dal** için deponun [Yayımla dalını](source-control.md#configure-publishing-settings) seçin. Bu yayın dalı varsayılan olarak `adf_publish` . **Varsayılan sürüm** için **varsayılan daldan en son**' u seçin.

    ![Yapıt ekleme](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager Dağıtım görevi ekleyin:

    a.  Aşama görünümünde, **aşama görevlerini görüntüle**' yi seçin.

    ![Aşama görünümü](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Yeni bir görev oluşturun. **ARM şablon dağıtımı** araması yapın ve ardından **Ekle**' yi seçin.

    c.  Dağıtım görevinde, hedef veri fabrikası için abonelik, kaynak grubu ve konum ' u seçin. Gerekirse kimlik bilgilerini sağlayın.

    d.  **Eylem** listesinde, **kaynak grubunu oluştur veya Güncelleştir**' i seçin.

    e.  **Şablon** kutusunun yanındaki üç nokta düğmesini (**...**) seçin. Yapılandırılmış git deposunun Yayımla dalınızda oluşturulan Azure Resource Manager şablonuna gözatamazsınız. `ARMTemplateForFactory.json` <FactoryName> Adf_publish dalının klasöründe dosyayı arayın.

    f.  Seç **...** **şablon parametreleri** kutusunun yanındaki parametreler dosyasını seçin. `ARMTemplateParametersForFactory.json` <FactoryName> Adf_publish dalının klasöründe dosyayı arayın.

    örneğin:  Seç **...** **şablon parametrelerinin üzerine yaz** kutusunun yanında, hedef veri fabrikası için istenen parametre değerlerini girin. Azure Key Vault gelen kimlik bilgileri için, çift tırnak işaretleri arasında gizli dizi adını girin. Örneğin, gizli dizinin adı cred1 ise, bu değer için **"$ (cred1)"** girin.

    h. **Dağıtım modu** için **artımlı** ' ı seçin.

    > [!WARNING]
    > Tüm dağıtım modunda, kaynak grubunda bulunan ancak yeni Kaynak Yöneticisi şablonunda belirtilmeyen kaynaklar **silinir**. Daha fazla bilgi için lütfen [Azure Resource Manager dağıtım modlarına](../azure-resource-manager/templates/deployment-modes.md) başvurun

    ![Data Factory üretim dağıtımı](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Yayın ardışık düzenini kaydedin.

1. Bir yayını tetiklemek için **yayın oluştur**' u seçin. Yayınların oluşturulmasını otomatikleştirmek için bkz. [Azure DevOps yayın Tetikleyicileri](/azure/devops/pipelines/release/triggers)

   ![Yayın oluştur ' u seçin](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD senaryolarında, farklı ortamlardaki Integration Runtime (IR) türü aynı olmalıdır. Örneğin, geliştirme ortamında şirket içinde barındırılan bir IR varsa, aynı IR, test ve üretim gibi diğer ortamlarda da kendi kendine barındırılan türde olmalıdır. Benzer şekilde, tümleştirme çalışma zamanlarını birden çok aşamada paylaşıyorsanız, tümleştirme çalışma zamanlarını, geliştirme, test ve üretim gibi tüm ortamlarda bağlanmış bir şekilde yapılandırmanız gerekir.

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault parolaları al

Azure Resource Manager şablonunda geçiş yapmak için gizli dizileri varsa, Azure Pipelines sürümü ile Azure Key Vault kullanmanızı öneririz.

Gizli dizileri ele almanın iki yolu vardır:

1.  Gizli dizileri parametreler dosyasına ekleyin. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/templates/key-vault-parameter.md).

    Yayımla dalına yüklenen parametreler dosyasının bir kopyasını oluşturun. Key Vault almak istediğiniz parametrelerin değerlerini şu biçimi kullanarak ayarlayın:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Bu yöntemi kullandığınızda, gizli anahtar, anahtar kasasından otomatik olarak çekilir.

    Parametre dosyasının da yayımlama dalında olması gerekir.

1. Önceki bölümde açıklanan Azure Resource Manager dağıtım görevinin önüne bir [Azure Key Vault görevi](/azure/devops/pipelines/tasks/deploy/azure-key-vault) ekleyin:

    1.  **Görevler** sekmesinde yeni bir görev oluşturun. **Azure Key Vault** arayın ve ekleyin.

    1.  Key Vault görevinde, anahtar kasasını oluşturduğunuz aboneliği seçin. Gerekirse kimlik bilgilerini sağlayın ve ardından anahtar kasasını seçin.

    ![Key Vault görevi ekleme](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines aracısına izin verme

Doğru izinler ayarlanmamışsa Azure Key Vault görev erişim reddedildi hatasıyla başarısız olabilir. Yayın için günlükleri indirin ve Azure Pipelines aracısına izin vermek için komutunu içeren. ps1 dosyasını bulun. Komutu doğrudan çalıştırabilirsiniz. Ya da asıl KIMLIĞI dosyadan kopyalayabilir ve Azure portal erişim ilkesini el ile ekleyebilirsiniz. `Get` ve `List` gereken en düşük izinlerdir.

### <a name="updating-active-triggers"></a>Etkin tetikleyiciler güncelleştiriliyor

Etkin Tetikleyicileri güncelleştirmeye çalışırsanız dağıtım başarısız olabilir. Etkin Tetikleyicileri güncelleştirmek için, bunları el ile durdurmanız ve dağıtımdan sonra yeniden başlatmanız gerekir. Bunu bir Azure PowerShell görevi kullanarak yapabilirsiniz:

1.  Sürümün **Görevler** sekmesinde **Azure PowerShell** bir görev ekleyin. Görev sürümü 4. * öğesini seçin. 

1.  Fabrikanızın bulunduğu aboneliği seçin.

1.  Betik türü olarak **betik dosyası yolunu** seçin. Bu, PowerShell betiğinizi deponuza kaydetmenizi gerektirir. Tetikleyicileri durdurmak için aşağıdaki PowerShell betiği kullanılabilir:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

`Start-AzDataFactoryV2Trigger`Dağıtımdan sonra Tetikleyicileri yeniden başlatmak için benzer adımları (işleviyle birlikte) tamamlayabilirsiniz.

Data Factory ekibi, bu makalenin alt kısmında bulunan [örnek bir ön ve dağıtım sonrası betiği](#script) sağladı. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Her ortam için Kaynak Yöneticisi şablonunu el ile yükseltme

1. **ARM şablon** listesinde, geliştirme ortamında veri fabrikanızın Kaynak Yöneticisi şablonunu dışarı aktarmak Için **ARM şablonunu dışarı aktar** ' ı seçin.

   ![Kaynak Yöneticisi şablonu dışarı aktarma](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Test ve üretim verileri fabrikalarınız içinde **ARM şablonunu Içeri aktar**' ı seçin. Bu eylem, dışarı aktarılan şablonu içeri aktarabileceğiniz Azure portal sizi yönlendirir. Kaynak Yöneticisi Şablon düzenleyicisini açmak için **düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.

   ![Kendi şablonunuzu oluşturun](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **Dosya Yükle**' yi seçin ve ardından oluşturulan kaynak yöneticisi şablonunu seçin. Bu, 1. adımda içe aktarılmış. zip dosyasında bulunan dosya **arm_template.js** .

   ![Şablonu Düzenle](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Ayarlar bölümünde, bağlı hizmet kimlik bilgileri gibi yapılandırma değerlerini girin. İşiniz bittiğinde Kaynak Yöneticisi şablonunu dağıtmak için **satın al** ' ı seçin.

   ![Ayarlar bölümü](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager şablonuyla özel parametreler kullanma

Geliştirme fabrikasında ilişkili bir git deposu varsa, şablonu yayımlayarak veya dışarı aktararak oluşturulan Kaynak Yöneticisi şablonunun varsayılan Kaynak Yöneticisi şablonu parametrelerini geçersiz kılabilirsiniz. Bu senaryolarda varsayılan Kaynak Yöneticisi parametre yapılandırmasını geçersiz kılmak isteyebilirsiniz:

* Otomatik CI/CD kullanıyorsunuz ve Kaynak Yöneticisi dağıtımı sırasında bazı özellikleri değiştirmek istiyorsunuz, ancak özellikler varsayılan olarak parametreleştirimez.
* Fabrikanızın izin verilen en fazla sayıda parametreye (256) sahip olduğu için varsayılan Kaynak Yöneticisi şablonunun geçersiz olması çok büyük.

    Özel parametre 256 sınırını işlemek için üç seçenek vardır:    
  
    * Özel parametre dosyasını kullanın ve Parametreleştirme gerektirmeyen özellikleri kaldırın, yani, varsayılan değeri tutan ve bu nedenle parametre sayısını azaltan Özellikler.
    * Parametreleri azaltmak için veri akışındaki mantığı yeniden düzenleyin, örneğin, işlem hattı parametrelerinin hepsi aynı değere sahiptir, bunun yerine yalnızca genel parametreleri kullanabilirsiniz.
    * Bir veri fabrikasını birden çok veri akışına ayırın.

Varsayılan Kaynak Yöneticisi parametre yapılandırmasını geçersiz kılmak için, hub 'ı **Yönet** ' e gidin ve "kaynak denetimi" bölümünde **ARM şablonu** ' nu seçin. **ARM parametre yapılandırması** bölümünde, Kaynak Yöneticisi parametresi yapılandırma kodu düzenleyicisini açmak için "parametre yapılandırmasını düzenle" içinde **Düzenle** simgesi ' ne tıklayın.

![Özel parametreleri Yönet](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> **ARM parametre yapılandırması** yalnızca "gıt modunda" etkindir. Şu anda "canlı modda" veya "Data Factory" modunda devre dışı bırakılmıştır.

Özel bir Kaynak Yöneticisi parametre yapılandırması oluşturmak git dalınızın kök klasöründe **arm-template-parameters-definition.js** adında bir dosya oluşturur. Bu tam dosya adını kullanmanız gerekir.

![Özel parametreler dosyası](media/continuous-integration-deployment/custom-parameters.png)

Data Factory, işbirliği dalından yayımlarken, bu dosyayı okur ve hangi özelliklerin parametreli olduğunu oluşturmak için yapılandırmasını kullanacaktır. Dosya bulunamazsa, varsayılan şablon kullanılır.

Bir Kaynak Yöneticisi şablonu dışarı aktarırken, Data Factory, bu dosyayı, üzerinde çalışmakta olduğunuz daldan, işbirliği dalını değil, bu dosyayı okur. Bir özel daldan dosya oluşturabilir veya düzenleyebilirsiniz, burada, Kullanıcı arabiriminde **ARM şablonunu dışarı aktar** ' ı seçerek yaptığınız değişiklikleri test edebilirsiniz. Daha sonra dosyayı işbirliği dalında birleştirebilirsiniz.

> [!NOTE]
> Özel bir Kaynak Yöneticisi parametre yapılandırması, 256 ARM şablon parametresi sınırını değiştirmez. Parametreli özellik sayısını seçmenizi ve azaltmanızı sağlar.

### <a name="custom-parameter-syntax"></a>Özel parametre sözdizimi

Aşağıda, **arm-template-parameters-definition.js** özel parametre dosyasını oluştururken izlenecek bazı yönergeler verilmiştir. Dosya her varlık türü için bir bölümden oluşur: tetikleyici, işlem hattı, bağlı hizmet, veri kümesi, tümleştirme çalışma zamanı ve veri akışı.

* İlgili varlık türünün altında özellik yolunu girin.
* İçin bir özellik adının ayarlanması `*` , altındaki tüm özellikleri parametreleştirmek istediğinizi (özyinelemeli değil, yalnızca ilk düzeye doğru değil) gösterir. Bu yapılandırmaya özel durumlar da sağlayabilirsiniz.
* Bir özelliğin değerini dize olarak ayarlamak, özelliği parametreleştirmek istediğinizi gösterir. `<action>:<name>:<stype>` biçimini kullanın.
   *  `<action>` Şu karakterlerden biri olabilir:
      * `=` , geçerli değeri parametresi için varsayılan değer olarak tutacağı anlamına gelir.
      * `-` parametresi için varsayılan değeri saklama anlamına gelir.
      * `|` , bağlantı dizeleri veya anahtarlar için Azure Key Vault parolalar için özel bir durumdur.
   * `<name>` parametrenin adıdır. Boşsa, özelliğin adını alır. Değer bir `-` karakterle başlıyorsa, ad kısaltılmıştır. Örneğin, `AzureStorage1_properties_typeProperties_connectionString` olarak kısaltılacak `AzureStorage1_connectionString` .
   * `<stype>` parametrenin türüdür. `<stype>`Boşsa, varsayılan tür olur `string` . Desteklenen değerler: `string` , `securestring` , `int` , `bool` , `object` `secureobject` ve `array` .
* Tanım dosyasında bir dizi belirtilmesi, şablondaki eşleşen özelliğin bir dizi olduğunu gösterir. Data Factory, dizinin tümleştirme çalışma zamanı nesnesinde belirtilen tanımı kullanarak dizideki tüm nesneler arasında yinelenir. İkinci nesne, bir dize, her yineleme için parametresinin adı olarak kullanılan özelliğin adı olur.
* Bir tanım, kaynak örneğine özgü olamaz. Herhangi bir tanım, bu türdeki tüm kaynaklar için geçerlidir.
* Varsayılan olarak, Key Vault gizli dizileri ve bağlantı dizeleri, anahtarlar ve belirteçler gibi güvenli dizeler gibi tüm güvenli dizeler parametrelenir.
 
### <a name="sample-parameterization-template"></a>Örnek Parametreleştirme şablonu

Kaynak Yöneticisi parametre yapılandırmasının nasıl görünebileceğini aşağıda görebilirsiniz:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
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
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Yukarıdaki şablonun nasıl oluşturulduğu ve kaynak türüne göre nasıl bölündüğü hakkında bir açıklama aşağıda verilmiştir.

#### <a name="pipelines"></a>Pipelines
    
* Yoldaki herhangi bir özellik `activities/typeProperties/waitTimeInSeconds` parametrelenir. Bir işlem hattındaki (örneğin, etkinlik) bir kod düzeyi özelliği olan herhangi bir etkinlik, `waitTimeInSeconds` `Wait` varsayılan bir ada sahip bir sayı olarak parametrelendirilir. Ancak Kaynak Yöneticisi şablonunda varsayılan bir değere sahip olmaz. Kaynak Yöneticisi dağıtımı sırasında zorunlu bir giriş olacaktır.
* Benzer şekilde, adlı bir özellik `headers` (örneğin, bir `Web` etkinlikte) türü `object` (JObject) ile parametrelenir. Kaynak fabrikasının değeriyle aynı değer olan varsayılan bir değere sahiptir.

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

> [!NOTE]
> İşlem hattı için yapılandırılmışsa **Azure uyarıları ve matrisleri** , ARM dağıtımları için parametre olarak şu anda desteklenmemektedir. Yeni ortamdaki uyarıları ve matrisleri yeniden uygulamak için lütfen [izleme, uyarılar ve matrisleri Data Factory izleyin.](./monitor-using-azure-monitor.md#data-factory-metrics)
> 

### <a name="default-parameterization-template"></a>Varsayılan parameterleştirme şablonu

Geçerli varsayılan parameterleştirme şablonu aşağıda verilmiştir. Yalnızca birkaç parametre eklemeniz gerekiyorsa, varolan parameterleştirme yapısını kaybetmemeniz nedeniyle bu şablonu doğrudan düzenlemeniz iyi bir fikir olabilir.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Örnek: varolan Azure Databricks etkileşimli küme KIMLIĞINI parametrize etme

Aşağıdaki örnek, varsayılan parameterleştirme şablonuna tek bir değerin nasıl ekleneceğini gösterir. Yalnızca bir Databricks bağlı hizmeti için mevcut Azure Databricks etkileşimli küme KIMLIĞINI parametreler dosyasına eklemek istiyoruz. Bu dosyanın, `existingClusterId` öğesinin Özellikler alanının altına eklenmesi hariç önceki dosyayla aynı olduğunu unutmayın `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Bağlı Kaynak Yöneticisi şablonları

Veri fabrikalarınız için CI/CD ayarladıysanız, fabrikanızın daha Büyük büyüdüğü için Azure Resource Manager şablonu sınırlarını aşabilirsiniz. Örneğin, bir sınır Kaynak Yöneticisi şablonundaki en fazla kaynak sayısıdır. Fabrika için tam Kaynak Yöneticisi şablonu oluştururken büyük fabrikalara uyum sağlamak için, Data Factory artık bağlantılı Kaynak Yöneticisi şablonları oluşturuyor. Bu özellikle, tüm fabrika yükü, sınırlara göre sınırlandırılmaması için çeşitli dosyalara bölünür.

Git 'i yapılandırdıysanız, bağlantılı şablonlar oluşturulur ve adf_publish dalındaki tam Kaynak Yöneticisi şablonlarıyla birlikte linkedTemplates adlı yeni bir klasöre kaydedilir:

![Bağlı Kaynak Yöneticisi şablonları klasörü](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Bağlantılı Kaynak Yöneticisi şablonları genellikle ana şablondan ve ana şablon kümesinden oluşur. Üst şablon üzerinde ArmTemplate_master.jsçağrılır ve alt şablonlar, üzerinde ArmTemplate_0.jsdüzeniyle, ArmTemplate_1.jsaçık ve bu şekilde adlandırılır. 

Tam Kaynak Yöneticisi şablonu yerine bağlantılı şablonlar kullanmak için, CI/CD görevinizi ArmTemplateForFactory.json (tam Kaynak Yöneticisi şablonu) yerine ArmTemplate_master.jsüzerine işaret etmek üzere güncelleştirin. Kaynak Yöneticisi ayrıca, Azure 'un dağıtım sırasında erişebilmesi için bağlantılı şablonları bir depolama hesabına yüklemenizi gerektirir. Daha fazla bilgi için bkz. [VSTS ile bağlantılı kaynak yöneticisi şablonlarını dağıtma](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Dağıtım görevinden önce ve sonra, CI/CD işlem hattınızda Data Factory betikleri eklemeyi unutmayın.

Git 'in yapılandırılıp yapılandırılmadığını, **ARM şablon** listesindeki **ARM şablonunu dışarı aktar** aracılığıyla bağlı şablonlara erişebilirsiniz.

## <a name="hotfix-production-environment"></a>Düzeltme üretim ortamı

Bir üretime fabrika dağıtımı yaptıysanız ve hemen düzeltilmesi gereken bir hata olduğunu fark ederseniz, ancak geçerli işbirliği dalını dağıtamazsınız, bir düzeltme dağıtmanız gerekebilir. Bu yaklaşım, hızlı çözüm Mühendisliği veya QFE olarak bilinir.

1.    Azure DevOps 'da üretime dağıtılan sürüme gidin. Dağıtılan son yürütmeyi bulun.

2.    Tamamlama iletisinden işbirliği dalının kayıt KIMLIĞINI alın.

3.    Bu işlemeden yeni bir düzeltme dalı oluşturun.

4.    Azure Data Factory UX ' e gidin ve düzeltme dalına geçiş yapın.

5.    Azure Data Factory UX kullanarak hatayı düzeltemedi. Değişikliklerinizi test edin.

6.    Düzeltme doğrulandıktan sonra, düzeltme Kaynak Yöneticisi şablonunu almak için **ARM şablonunu dışarı aktar** ' ı seçin.

7.    Bu derlemeyi adf_publish dalına el ile kontrol edin.

8.    Yayın işlem hattınızı adf_publish iadelerine göre otomatik olarak tetiklemek üzere yapılandırdıysanız, yeni bir yayın otomatik olarak başlatılır. Aksi takdirde, bir yayını el ile sıraya alın.

9.    Düzeltme sürümünü test ve üretim fabrikasına dağıtın. Bu sürüm, önceki üretim yükünü ve 5. adımda yaptığınız çözümü içerir.

10.   Sonraki sürümlerin aynı hatayı içermemesi için düzeltmeden değişiklikleri geliştirme dalına ekleyin.

Ortamlarınızı nasıl sık düzelceğiniz hakkında ayrıntılı bir video öğreticisinin altındaki videoya bakın. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Pozlama denetimi ve özellik bayrakları

Bir ekip üzerinde çalışırken, değişiklikleri birleştirebileceğiniz ancak ÜRETIM ve QA gibi yükseltilmiş ortamlarda çalıştırılmalarını istemediğiniz örnekler vardır. ADF ekibi, bu senaryoyu işlemek için [özellik bayraklarını kullanma DevOps kavramını](/azure/devops/migrate/phase-features-with-feature-flags)önerir. ADF 'de, bu ortam bayraklarını temel alan mantık kümelerini gizlemek için [genel parametreleri](author-global-parameters.md) ve [If koşulu etkinliğini](control-flow-if-condition-activity.md) birleştirebilirsiniz.

Bir özellik bayrağını ayarlamayı öğrenmek için aşağıdaki video öğreticisine bakın:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>CI/CD için en iyi yöntemler

Veri fabrikanınızla git tümleştirmesi kullanıyorsanız ve değişikliklerinizi geliştirmeden test ve daha sonra üretime taşıyan bir CI/CD işlem hattına sahipseniz, bu en iyi yöntemleri öneririz:

-   **Git tümleştirmesi**. Yalnızca geliştirici veri fabrikanızı git tümleştirmesiyle yapılandırın. Test ve üretimde yapılan değişiklikler CI/CD aracılığıyla dağıtılır ve git tümleştirmesi gerekmez.

-   **Dağıtım öncesi ve sonrası betiği**. CI/CD 'deki Kaynak Yöneticisi dağıtım adımından önce, Tetikleyicileri durdurma ve yeniden başlatma ve temizleme işlemlerini yapma gibi belirli görevleri gerçekleştirmeniz gerekir. Dağıtım görevinden önce ve sonra PowerShell betikleri kullanmanızı öneririz. Daha fazla bilgi için bkz. [etkin Tetikleyicileri güncelleştirme](#updating-active-triggers). Data Factory ekibi, bu sayfanın en altında bulunan [bir komut dosyası sağladı](#script) .

-   **Tümleştirme çalışma zamanları ve paylaşma**. Tümleştirme çalışma zamanları sıklıkla değişmez ve CI/CD 'inizdeki tüm aşamalar arasında benzerdir. Data Factory, CI/CD 'nin tüm aşamalarında aynı ad ve türde tümleştirme çalışma zamanı olmasını bekler. Tümleştirme çalışma zamanlarını tüm aşamalarda paylaşmak istiyorsanız, paylaşılan tümleştirme çalışma zamanlarını içerecek şekilde, Üçlü bir fabrika kullanmayı düşünün. Bu paylaşılan fabrikası tüm ortamlarınızda bağlantılı tümleştirme çalışma zamanı türü olarak kullanabilirsiniz.

-   **Yönetilen özel uç nokta dağıtımı**. Özel bir uç nokta zaten bir fabrikada varsa ve aynı ada ancak değiştirilmiş özelliklerle özel bir uç nokta içeren bir ARM şablonu dağıtmaya çalışırsanız, dağıtım başarısız olur. Diğer bir deyişle, fabrikada zaten var olan özelliklerle aynı özelliklere sahip olduğu sürece özel bir uç noktayı başarıyla dağıtabilirsiniz. Herhangi bir özellik ortamlar arasında farklıysa, bu özelliği parametreleyerek ve dağıtım sırasında ilgili değeri sağlayarak geçersiz kılabilirsiniz.

-   **Key Vault**. Bağlantı bilgileri Azure Key Vault depolanan bağlı hizmetleri kullandığınızda, farklı ortamlarda ayrı anahtar kasalarının tutulması önerilir. Ayrıca, her Anahtar Kasası için ayrı izin düzeyleri yapılandırabilirsiniz. Örneğin, ekip üyelerinizin üretim gizli dizileri için izinleri olmasını istemeyebilirsiniz. Bu yaklaşımı izlerseniz, tüm aşamalar genelinde aynı gizli adları tutmanız önerilir. Aynı gizli dizi adlarını tutarsanız, tek şey yalnızca bir parametre olan Anahtar Kasası adı olduğundan, her bir bağlantı dizesini CI/CD ortamları arasında parametreleştirmek zorunda kalmazsınız.

-  **Kaynak adlandırma** ARM şablon kısıtlamaları nedeniyle, kaynaklarınız adda boşluk içeriyorsa dağıtımdaki sorunlar ortaya çıkabilir. Azure Data Factory ekibi, kaynaklar için boşluklar yerine ' _ ' veya '-' karakterlerini kullanmanızı öneriyor. Örneğin, ' Pipeline_1 ', ' Pipeline 1 ' üzerinden tercih edilen bir ad olabilir.

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

- Tasarım yaparak Data Factory işleme veya kaynakların seçmeli yayımlamasına izin vermez. Yayınlar, veri fabrikasında yapılan tüm değişiklikleri içerir.

    - Data Factory varlıkları birbirlerine bağlıdır. Örneğin, tetikler, işlem hatlarına ve işlem hatları, veri kümelerine ve diğer işlem hattına bağlıdır. Bir kaynak alt kümesinin seçmeli olarak yayımlanması beklenmeyen davranışlara ve hatalara neden olabilir.
    - Seçmeli yayımlamaya ihtiyacınız olduğunda nadir olarak bir düzeltme kullanmayı düşünün. Daha fazla bilgi için bkz. [Düzeltme üretim ortamı](#hotfix-production-environment).

- Azure Data Factory ekibi, bir veri fabrikasında tek tek varlıklara (işlem hatları, veri kümeleri, vb.) Azure RBAC denetimleri atamayı önermez. Örneğin geliştiricinin bir işlem hattına veya veri kümesine erişimi varsa, veri fabrikasındaki tüm işlem hatlarına veya veri kümelerine erişebilmelidir. Bir veri fabrikası içinde birçok Azure rolü uygulamanız gerektiğini düşünüyorsanız, ikinci bir veri fabrikası dağıtmaya bakın.

-   Özel dallardan yayımlayamazsınız.

-   Şu anda Bitbucket üzerinde projeler barındıramıyoruz.

-   Şu anda uyarıları ve matrisleri parametre olarak dışarı ve içeri aktaramazsınız. 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Örnek ön ve dağıtım sonrası betiği

Aşağıdaki örnek betik, dağıtımdan önce Tetikleyicileri durdurmak ve daha sonra yeniden başlatmak için kullanılabilir. Betik Ayrıca kaldırılan kaynakları silmek için kod içerir. Betiği bir Azure DevOps git deposuna kaydedin ve sürüm 4. * kullanarak bir Azure PowerShell görevi aracılığıyla buna başvurun.

Dağıtım öncesi betiği çalıştırırken, **betik bağımsız değişkenleri** alanında aşağıdaki parametrelerin bir çeşidini belirtmeniz gerekecektir.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Dağıtım sonrası betiği çalıştırırken, **betik bağımsız değişkenleri** alanında aşağıdaki parametrelerin bir çeşidini belirtmeniz gerekecektir.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

> [!NOTE]
> `-deleteDeployment`Bayrak, ARM 'de dağıtım GEÇMIŞINDEN ADF dağıtım girişinin silinmesini belirtmek için kullanılır.

![Azure PowerShell görevi](media/continuous-integration-deployment/continuous-integration-image11.png)

Dağıtım öncesi ve sonrası için kullanılabilecek komut dosyası aşağıda verilmiştir. Silinen kaynaklar ve kaynak başvuruları için BT hesapları.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```