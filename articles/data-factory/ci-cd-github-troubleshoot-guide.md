---
title: ADF 'de CI-CD, Azure DevOps ve GitHub sorunlarını giderme
description: ADF 'de CI-CD sorunlarını gidermek için farklı yöntemler kullanın.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 2b6f97f0966cb2c92dbd88c4a70188282ed3ed27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802042"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>ADF 'de CI-CD, Azure DevOps ve GitHub sorunlarını giderme 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Data Factory ' de sürekli Integration-Continuous dağıtım (CI-CD), Azure DevOps ve GitHub sorunları için genel sorun giderme yöntemleri incelenmektedir.

Kaynak denetimi veya DevOps tekniklerini kullanarak sorularınız veya sorunlarınız varsa, yararlı bulabileceğiniz birkaç makale aşağıda verilmiştir:

- Kaynak denetimi 'nin ADF 'de nasıl çalıştığını öğrenmek için [ADF 'de kaynak denetimi '](source-control.md) ne bakın. 
- DevOps CI-CD ' nin ADF 'de nasıl çalıştığı hakkında daha fazla bilgi edinmek için  [ADF 'de CI-CD '](continuous-integration-deployment.md) ye başvurun.
 
## <a name="common-errors-and-messages"></a>Sık karşılaşılan hatalar ve mesajlar

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Farklı kiracı nedeniyle git deposuna bağlanma başarısız oldu

#### <a name="issue"></a>Sorun
    
Bazen HTTP durumu 401 gibi kimlik doğrulama sorunlarıyla karşılaşabilirsiniz. Özellikle Konuk hesabı olan birden çok kiracı varsa, bu şeyler daha karmaşık hale gelebilir.

#### <a name="cause"></a>Nedeni

Ne gözlemlediğimiz, belirtecin özgün kiracıdan alındığı, ancak ADF 'nin Konuk kiracısında olduğu ve konuk kiracısındaki DevOps 'u ziyaret etmek için belirteci kullanmaya çalıştığı bir şeydir. Bu beklenen davranış değildir.

#### <a name="recommendation"></a>Öneri

Bunun yerine Konuk kiracıdan verilen belirteci kullanmanız gerekir. Örneğin, aynı Azure Active Directory Konuk kiracınızın yanı sıra DevOps olarak atamanız gerekir, bu sayede belirteç davranışını doğru şekilde ayarlayabilir ve doğru kiracıyı kullanabilirsiniz.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Parametre dosyasındaki şablon parametreleri geçerli değil

#### <a name="issue"></a>Sorun

Geliştirme dalındaki bir tetikleyiciyi, **aynı** yapılandırmaya (sıklık ve Aralık gibi) sahip test veya üretim dalında zaten mevcut olan bir tetikleyiciyi sileriyorsa, yayın işlem hattı dağıtımı başarılı olur ve ilgili tetikleyici ilgili ortamlarda silinir. Ancak, test/üretim ortamlarında tetikleyici için **farklı** yapılandırmanız (sıklık ve aralığa benzer) varsa ve dev 'de aynı tetikleyiciyi silerseniz, dağıtım bir hata vererek başarısız olur.

#### <a name="cause"></a>Nedeni

CI/CD işlem hattı şu hata ile başarısız oluyor:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Öneri

Bu, parametreleştirilen bir tetikleyiciyi genellikle sildiğimiz için hata oluşur, bu nedenle parametreler ARM şablonunda kullanılamaz (tetikleyici artık mevcut olmadığından). Parametre artık ARM şablonunda olmadığından, DevOps ardışık düzeninde geçersiz kılınan parametreleri güncelleştirmemiz gerekir. Aksi takdirde, ARM şablonundaki parametrelerin her değiştirilişinde, DevOps ardışık düzeninde geçersiz kılınan parametreleri güncelleştirmelidir (dağıtım görevinde).

### <a name="updating-property-type-is-not-supported"></a>Özellik türünü güncelleştirme desteklenmiyor

#### <a name="issue"></a>Sorun

CI/CD yayın işlem hattı aşağıdaki hatayla başarısız oldu:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Nedeni

Bunun nedeni, hedef fabrikada aynı ada sahip ancak farklı türde bir tümleştirme çalışma zamanı. Integration Runtime, dağıtım sırasında aynı türde olmalıdır.

#### <a name="recommendation"></a>Öneri

- Aşağıdaki CI/CD için En Iyi yöntemlere başvurun:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Tümleştirme çalışma zamanları sıklıkla değişmez ve CI/CD 'nizin tüm aşamalarında benzerdir, bu nedenle Data Factory CI/CD 'nin tüm aşamalarında aynı ad ve türde tümleştirme çalışma zamanı olmasını bekler. Ad ve türler & Özellikler farklıysa, kaynak ve hedef tümleştirme çalışma zamanı yapılandırmasıyla eşleştiğinden emin olun ve ardından yayın işlem hattını dağıtın.
- Tümleştirme çalışma zamanlarını tüm aşamalarda paylaşmak istiyorsanız, paylaşılan tümleştirme çalışma zamanlarını içerecek şekilde, Üçlü bir fabrika kullanmayı düşünün. Bu paylaşılan fabrikası tüm ortamlarınızda bağlantılı tümleştirme çalışma zamanı türü olarak kullanabilirsiniz.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Belge oluşturma veya güncelleştirme geçersiz başvuru nedeniyle başarısız oldu

#### <a name="issue"></a>Sorun

Data Factory değişiklikleri yayımlamaya çalışırken şu hata iletisini alırsınız:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Nedeni

Git yapılandırmasını ayırdıktan sonra, Data Factory "eşitleme sırasında" olarak ayarlayan "kaynakları Içeri aktar" bayrağı seçili olacak şekilde yeniden ayarlayın. Bu, yayımlanacak hiçbir değişiklik olmadığı anlamına gelir.

#### <a name="resolution"></a>Çözüm

Git yapılandırmasını ayırın ve yeniden ayarlayın ve "var olan kaynakları içeri aktar" onay kutusunu denetlediğinizden emin olun.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory bir kaynak grubundan diğerine geçiş başarısız

#### <a name="issue"></a>Sorun

Data Factory bir kaynak grubundan diğerine taşınamıyor, şu hata ile başarısız oluyor:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Çözüm

Taşıma işlemine izin vermek için SSIS-IR ve paylaşılan IRS 'yi silmeniz gerekir. Tümleştirme çalışma zamanlarını silmek istemiyorsanız en iyi yol, kopyalama ve kopyalama belgesini izleyerek kopyayı, sonra da eski Data Factory silmez.

###  <a name="unable-to-export-and-import-arm-template"></a>ARM şablonu dışarı ve içeri aktarılamıyor

#### <a name="issue"></a>Sorun

ARM şablonu dışarı ve içeri aktarılamıyor. Portalda hata yoktu, ancak tarayıcı izlemede aşağıdaki hatayı görebilirsiniz:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Nedeni

Kullanıcı olarak bir müşteri rolü oluşturdunuz ve gerekli izne sahip değil. Fabrika Kullanıcı arabirimine yüklendiğinde, fabrika için bir dizi pozlama denetim değeri denetlenir. Bu durumda, kullanıcının erişim rolü *Queryfeaturesvalue* API 'sine erişim iznine sahip değildir. Bu API 'ye erişmek için genel parametreler özelliği kapalıdır. ARM dışa aktarma kodu yolu kısmen genel parametreler özelliğine bağlıdır.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için şu izni rolünüze eklemeniz gerekir: *Microsoft. DataFactory/Factory/queryFeaturesValue/Action*. Bu izin, varsayılan olarak "Data Factory katkıda bulunan" rolünde yer almalıdır.

###  <a name="cannot-automate-publishing-for-cicd"></a>CI/CD için yayımlama otomatikleştirilemez 

#### <a name="cause"></a>Nedeni

Son olarak, dağıtımlar için ADF işlem hattını yayımlamanın yalnızca bir yolu ADF Portal düğmesine tıklamıştı. Artık işlemi otomatik hale getirebilirsiniz. 

#### <a name="resolution"></a>Çözüm

CI/CD işlemi geliştirilmiştir. **Otomatik yayımlama** ÖZELLIĞI, ADF UX 'deki tüm Azure Resource Manager (ARM) şablonu özelliklerini doğrular ve dışarı aktarır. Genel kullanıma açık bir NPM paketi aracılığıyla mantıksal tüketilebilir hale gelir [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Bu, ADF Kullanıcı arabirimine gidip bir düğme tıklamasına gerek kalmadan, bu eylemleri programlı bir şekilde tetiklemeniz sağlar. Bu, CI/CD işlem hatlarınızı **gerçek** bir sürekli tümleştirme deneyimi sağlar. Ayrıntılar için lütfen [ADF CI/CD yayımlama geliştirmelerini](./continuous-integration-deployment-improvements.md) izleyin. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>4mb ARM şablon sınırı nedeniyle yayımlanamıyor  

#### <a name="issue"></a>Sorun

4mb toplam şablon boyutu Azure Resource Manager sınırına ulaşırsanız dağıtamazsınız. Sınırı geçtikten sonra dağıtım için bir çözüme ihtiyacınız vardır. 

#### <a name="cause"></a>Nedeni

Azure Resource Manager şablon boyutunu 4mb olarak kısıtlar. Şablonunuzun boyutunu 4 MB ve her bir parametre dosyası 64 KB olarak sınırlandırın. 4 MB sınırı, yineleme, yinelemeli kaynak tanımları ve değişkenler ve parametreler için genişletildikten sonra şablonun son durumuna uygulanır. Ancak sınırı geçtin. 

#### <a name="resolution"></a>Çözüm

Küçük ve orta ölçekli çözümler için tek bir şablonun anlaşılması ve bakımının yapılması daha kolay olacaktır. Tüm kaynakları ve değerleri tek bir dosyada görebilirsiniz. Gelişmiş senaryolarda bağlantılı şablonlar çözümü hedeflenen bileşenlere ayırmanıza sağlar. Lütfen [bağlı ve Iç Içe Şablonlar kullanma](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)konusunda en iyi uygulamaları izleyin.

### <a name="cannot-connect-to-git-enterprise"></a>GIT Enterprise 'a bağlanılamıyor  

##### <a name="issue"></a>Sorun

İzin sorunları nedeniyle GIT Enterprise 'a bağlanamazsınız. **422-Proceslabilen varlık** gibi bir hata görebilirsiniz.

#### <a name="cause"></a>Nedeni

* ADF için OAuth yapılandırmadı. 
* URL 'niz yanlış yapılandırılmış.

##### <a name="resolution"></a>Çözüm

İlk olarak ADF 'ye OAuth erişimi verirsiniz. Ardından, GIT Enterprise 'a bağlanmak için doğru URL 'yi kullanmanız gerekir. Yapılandırma, müşteri organizasyonları olarak ayarlanmalıdır. Örneğin, ADF ilk olarak *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ....* ve başarısız olur. Sonra, *https://hostname/api/v3/orgs/ <org> / <repo> ...* ve başarılı olur. 
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>Silinen bir veri fabrikasından kurtarılamıyor

#### <a name="issue"></a>Sorun
Müşteri tarafından silinen veri fabrikası veya Data Factory içeren kaynak grubu. Silinen bir veri fabrikasını geri yüklemeyi öğrenmek ister misiniz?

#### <a name="cause"></a>Nedeni

Data Factory yalnızca müşterinin yapılandırılmış kaynak denetimi varsa (DevOps veya git) kurtarılması mümkündür. Bu, yayımlanan en son kaynağı getirir ve yayımlanmamış işlem hattını, veri kümesini ve bağlı hizmeti geri **yüklenmeyecektir** .

Kaynak denetimi yoksa, hizmet silinen komutu aldığından, örnek silindiğinden ve hiçbir yedekleme depolanmadığı için arka uçta silinen Data Factory kurtarma mümkün değildir.

#### <a name="resolution"></a>Çözüm

Kaynak denetimine sahip Silinen Data Factory kurtarmak için aşağıdaki adımları izleyin:

 * Yeni bir Azure Data Factory oluşturun.

 * Git 'i aynı ayarlarla yeniden yapılandırın, ancak mevcut Data Factory kaynaklarını seçili depoya içeri aktardığınızdan emin olun ve yeni dal ' ı seçin.

 * İşbirliği dalında yapılan değişiklikleri birleştirmek ve yayımlamak için bir çekme isteği oluşturun.

 * Müşterinin, silinen ADF 'de şirket içinde barındırılan bir Integration Runtime varsa, yeni ADF 'de yeni bir örnek oluşturması, ayrıca örneği de yeni bir anahtar ile kendi şirket içi makinesinde/VM 'de yüklemesi ve yeniden yüklenmesi gerekecektir. IR kurulumu tamamlandıktan sonra, müşteri bağlantılı hizmeti yeni IR 'ye işaret edecek şekilde değiştirmek ve bağlantıyı test etmek zorunda kalacak ve hata **geçersiz başvurusuyla** başarısız olur.



## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme konusunda daha fazla yardım için aşağıdaki kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory için yığın taşma Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
