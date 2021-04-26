---
title: Azure müşteri kullanım atısyonu
description: Ticari Market 'teki Azure uygulamalarının ve iş ortakları tarafından geliştirilen diğer dağıtılabilir IP 'nin müşteri kullanımını izlemeye genel bakış alın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 04/12/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 7f09e744dfdd7be73c62c70bcfa91804f14aaa68
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313683"
---
# <a name="azure-customer-usage-attribution"></a>Azure müşteri kullanım atısyonu

Müşteri kullanımı attributıon, IP 'nizi sizinle bir iş ortağı olarak dağıttığınızda oluşturulan müşteri aboneliklerindeki Azure kaynaklarından kullanımı ilişkilendirir. Bu ilişkilerin iç Microsoft sistemlerinde oluşi, yazılımınızı çalıştıran Azure ayak izine daha fazla görünürlük getirir. [Ticari Market 'Teki Azure Uygulama teklifleri](#commercial-marketplace-azure-apps)için, bu Izleme özelliği Microsoft satış ekipleriyle uyum sağlamanıza ve Microsoft iş ortağı programları için kredi elde etmenize yardımcı olur. Müşteri kullanım atısyonu [, ticari Market 'Teki Azure sanal makine teklifleri](./azure-vm-create.md)için geçerli değildir. Azure tüketiminin son müşteri aboneliklerinde izlenmesini sağlamak için bir market yayımcısının sanal makine teklifleri için yapması gereken bir şey yoktur.

Müşteri kullanım attributıon üç dağıtım seçeneğini destekler:

1. Azure Resource Manager şablonlar (ticari markette "çözüm şablonları" veya "yönetilen uygulamalar" olarak da adlandırılan Azure uygulamalarının ortak underpinnings): iş ortakları, Azure çözümlerinin altyapısını ve yapılandırmasını tanımlamak için Kaynak Yöneticisi şablonları oluşturur. Kaynak Yöneticisi şablonu, müşterilerinizin çözümünüzün kaynaklarını tutarlı ve yinelenebilir bir durumda dağıtmasını sağlar.
1. Azure Resource Manager API 'Leri: iş ortakları, bir Kaynak Yöneticisi şablonunu dağıtmak veya doğrudan Azure hizmetleri sağlamak için Kaynak Yöneticisi API 'Leri çağırabilir.
1. Terrayform: iş ortakları Terrayform 'u bir Kaynak Yöneticisi şablonu dağıtmak veya doğrudan Azure hizmetlerini dağıtmak için kullanabilir.

İşbu [makalenin ilerleyen kısımlarında](#other-use-cases)açıklanan ticari Market dışında müşteri kullanımı atımı için ikincil kullanım örnekleri vardır.

>[!IMPORTANT]
>- Müşteri kullanımı attributıon, birincil olarak Azure kaynaklarını dağıtmak ve yönetmek için tasarlanan sistem tümleştiricileri, yönetilen hizmet sağlayıcılarının veya araçların çalışmasını izlemek üzere tasarlanmamıştır.
>- Müşteri kullanım attributıon Yeni dağıtımlar içindir ve zaten dağıtılmış olan kaynakları izlemeyi desteklemez.
>- Tüm Azure Hizmetleri Müşteri kullanımı attribuile uyumlu değildir. Azure Kubernetes Hizmetleri (AKS), VM Ölçek Kümeleri ve Azure Batch, kullanım bildiriminin altında olmasına neden olan bilinen sorunlardır.

## <a name="commercial-marketplace-azure-apps"></a>Ticari Market Azure uygulamaları

Ticari Market 'te yayımlanan Azure uygulamalarından Azure kullanımını izlemek büyük ölçüde otomatiktir. [Market Azure uygulamanızın planına ait teknik yapılandırmanın](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration)bir parçası olarak bir kaynak yöneticisi şablonu yüklediğinizde, Iş ortağı merkezi Azure Resource Manager tarafından okunabilen BIR izleme kimliği ekler.

Azure Resource Manager API 'Leri kullanıyorsanız, kodunuz, kaynak dağıttığı için Azure Resource Manager iletmek üzere izleme KIMLIĞINIZI [Aşağıdaki yönergeler](#use-resource-manager-apis) temelinde eklemeniz gerekecektir. Bu KIMLIK, planınızın teknik yapılandırma sayfasında Iş Ortağı Merkezi 'nde görünür. 

> [!NOTE]
> Mevcut Azure uygulamaları için, her planın teknik yapılandırmasındaki izleme kimliklerini güncelleştirmek üzere Mart 2021 ' de bir kerelik geçiş başladı. Bu tekliflerin geçmiş dağıtımlarından kullanım, Microsoft sistemlerinde izlenmeye devam edecektir.
>
>Tekliflerinizi güncelleştirdiğinizde, artık ana şablon dosyanıza **Microsoft. resources/dağıtımlar** kaynak türünü eklemeniz gerekmez.

## <a name="other-use-cases"></a>Diğer kullanım örnekleri 

Ticari Market 'te kullanılamayan çözümlerin Azure kullanımını izlemek için müşteri kullanımı atısyonu ' nı kullanabilirsiniz. Bu çözümler genellikle hızlı başlangıç deposunda, özel GitHub depolarında bulunur veya dayanıklı IP (dağıtılabilir ve ölçeklenebilir bir uygulama gibi) oluşturan 1:1 müşteri görevlendirmelerden gelir.

Birkaç el ile gerekli adım vardır:

1. İzleme kimlikleriniz olarak kullanılacak bir veya daha fazla GUID oluşturun.
1. Bu GUID 'Leri Iş Ortağı Merkezi 'ne kaydedin.
1. Kayıtlı GUID 'larınızı Azure uygulamanıza ve/veya Kullanıcı Aracısı dizelerinizle ekleyin.

### <a name="create-guids"></a>GUID 'Ler oluştur

Iş Ortağı Merkezi 'nin ticari Market 'teki Azure uygulamaları için sizin adınıza oluşturduğu izleme kimliklerinden farklı olarak CUA 'nın diğer kullanımları, izleme KIMLIĞINIZ olarak kullanılacak bir GUID oluşturmanızı gerektirir. GUID, 32 onaltılık basamak içeren benzersiz bir başvuru tanımlayıcısıdır. İzleme için GUID 'Ler oluşturmak üzere bir GUID Oluşturucu kullanmanız gerekir, örneğin, PowerShell aracılığıyla:

```powershell
[guid]::NewGuid()
```

Her ürün ve dağıtım kanalı için benzersiz bir GUID oluşturmalısınız. Raporlama 'nın bölünmesini istemiyorsanız, bir ürünün birden çok dağıtım kanalı için tek bir GUID kullanabilirsiniz. Raporlama Microsoft İş Ortağı Ağı KIMLIĞI ve GUID tarafından gerçekleştirilir.

### <a name="register-guids"></a>GUID 'Leri Kaydet

GUID 'lerin bir iş ortağı olarak sizinle ilişkilendirilebilen Iş Ortağı Merkezi 'ne kaydedilmesi gerekir:

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard)' nde oturum açın.

1. [Ticari Market yayımcısı](https://aka.ms/JoinMarketplace)olarak kaydolun.

1. Sağ üst köşedeki **ayarları** (dişli simgesi) ve ardından **Hesap ayarlarını** seçin.

1. **Kuruluş profili**  >  **tanımlayıcıları**  >  **izleme GUID Ekle**' yi seçin.

1. **GUID** kutusuna izleme GUID 'nizi girin. Ön ek olmadan yalnızca GUID girin `pid-` . **Açıklama** kutusuna çözümünüzün adını veya açıklamasını girin.

1. Birden fazla GUID kaydetmek için, **izleme GUID 'i yeniden Ekle** ' yi seçin. Sayfada ek kutular görüntülenir.

1. **Kaydet**’i seçin.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuna GUID ekleme

Kayıtlı GUID 'nizi bir Kaynak Yöneticisi şablonuna eklemek için, ana şablon dosyasında tek bir değişiklik yapın:

1. Kaynak Yöneticisi şablonunu açın.

1. Ana şablon dosyasında [Microsoft. resources/dağıtımlar](/azure/templates/microsoft.resources/deployments) türünde yeni bir kaynak ekleyin. Kaynak, iç içe veya bağlı şablonlarda değil, yalnızca dosya üzerinde **mainTemplate.js** veya **azuredeploy.js** .

1. `pid-`Kaynak adı olarak önekden sonra GUID değerini girin. Örneğin, GUID eb7927c8-dd66-43e1-b0cf-c346a422063 ise, kaynak adı **PID-eb7927c8-dd66-43e1-b0cf-c346a422063** olur. Örnek:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Şablonda hata olup olmadığını denetleyin.

1. Şablonu uygun depolarda yeniden yayımlayın.

1. [Şablon DAĞıTıMıNDA GUID başarısını doğrulayın](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Kaynak Yöneticisi şablonları oluşturma ve yayımlama hakkında daha fazla bilgi için, bkz: [ilk kaynak yöneticisi şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Bir GUID ile izlenen dağıtımları doğrulama

Şablonunuzu değiştirdikten ve bir test dağıtımı çalıştırdıktan sonra, dağıttığınız ve etiketlediğiniz kaynakları almak için aşağıdaki PowerShell betiğini kullanın.

GUID 'nin Kaynak Yöneticisi şablonunuza başarıyla eklendiğini doğrulamak için betiği kullanabilirsiniz. Betik Kaynak Yöneticisi API 'sine veya Tercaform dağıtımına uygulanmaz.

Azure'da oturum açın. Betiği çalıştırmadan önce doğrulamak istediğiniz dağıtıma sahip aboneliği seçin. Betiği, dağıtımın abonelik bağlamı içinde çalıştırın.

**GUID** ("DeploymentName" olarak adlandırılır) ve dağıtım **resourcegroupname** adı gerekli parametrelerdir.

[Asıl betiği](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) GitHub ' da alabilirsiniz.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Müşterilerinize bildirme

İş ortakları, müşterilerine müşteri kullanımı attributıon kullanan dağıtımlar hakkında bilgi sağlamalıdır. Microsoft, bu dağıtımlarla ilişkili Azure kullanımını iş ortağıyla raporlar. Aşağıdaki örneklerde, bu dağıtımlar hakkında müşterilerinizi bilgilendirmek için kullanabileceğiniz içerikler yer alır. Örneklerde, \<PARTNER> şirketinizin adıyla değiştirin. İş ortakları, müşterilerin izlemeye dışlanması seçenekleri de dahil olmak üzere, veri gizliliğiyle ve koleksiyon ilkeleriyle hizalandığından emin olmalıdır.

#### <a name="notification-for-resource-manager-template-deployments"></a>Kaynak Yöneticisi Şablon dağıtımları için bildirim

Bu şablonu dağıttığınızda, Microsoft, \<PARTNER> dağıtılan Azure kaynaklarıyla yazılım yüklemeyi tanımlayabilir. Microsoft, yazılımı desteklemek için kullanılan kaynakları ilişkilendirebilirler. Microsoft bu bilgileri, ürünleriyle ilgili en iyi deneyimleri sağlamak ve işlerini işletmek için toplar. Veriler, Microsoft 'un adresinde bulunan gizlilik ilkelerine göre toplanır ve yönetilir [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>SDK veya API dağıtımları için bildirim

\<PARTNER>Yazılım dağıttığınızda, Microsoft, \<PARTNER> dağıtılan Azure kaynaklarıyla yazılım yüklemeyi tanımlayabilir. Microsoft, yazılımı desteklemek için kullanılan kaynakları ilişkilendirebilirler. Microsoft bu bilgileri, ürünleriyle ilgili en iyi deneyimleri sağlamak ve işlerini işletmek için toplar. Veriler, Microsoft 'un adresinde bulunan gizlilik ilkelerine göre toplanır ve yönetilir [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Kaynak Yöneticisi API 'Leri kullanma

Bazı durumlarda, Azure hizmetlerini dağıtmak için Kaynak Yöneticisi REST API 'Lerine doğrudan çağrı yapabilirsiniz. Azure, bu çağrıları etkinleştirmek için [birden çok SDK 'yi destekler](../index.yml?pivot=sdkstools) . Kaynak dağıtmak için SDK 'Lardan birini kullanabilir veya REST API 'Lerini doğrudan çağırabilirsiniz.

Müşteri kullanımı atısyonu 'nı etkinleştirmek için, API aramalarınızı tasarlarken, istek içindeki Kullanıcı Aracısı başlığına izleme KIMLIĞINIZI dahil edin. Dizeyi `pid-` önekiyle biçimlendirin. Örnekler:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Ticari Market 'te bir Azure uygulamasıyla Kaynak Yöneticisi API 'Leri kullanıyorsanız, Iş Ortağı Merkezi 'nde sunulan izleme KIMLIĞINI kullanın. GUID kullanmayın.

Çeşitli SDK 'Lar Kaynak Yöneticisi API 'Leriyle farklı şekilde etkileşim kurar ve kodunuzda bazı farklılıklar gerektirecektir. Aşağıdaki örneklerde, bir GUID kullanarak ticari olmayan Market yaklaşımı ve daha popüler Azure SDK 'larının çeşitli özellikleri ele alınmaktadır.

#### <a name="example-python-sdk"></a>Örnek: Python SDK 'Sı

Python için, **config** özniteliğini kullanın. Özniteliği yalnızca bir UserAgent öğesine ekleyebilirsiniz. Örnek:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Her istemci için özniteliğini ekleyin. Genel statik yapılandırma yoktur. Her istemcinin izlenmesini sağlamak için bir istemci fabrikası etiketleyebilir. Daha fazla bilgi için [GitHub 'daki bu istemci fabrikası örneğine](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)bakın.

#### <a name="example-net-sdk"></a>Örnek: .NET SDK

.NET için Kullanıcı aracısını ayarladığınızdan emin olun. Kullanıcı aracısını aşağıdaki kodla ayarlamak için [Microsoft. Azure. Management. Floent](/dotnet/api/microsoft.azure.management.fluent) kitaplığını kullanın (C# dilinde örnek):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Örnek: Azure PowerShell

Kaynakları Azure PowerShell aracılığıyla dağıtırsanız, bu yöntemi kullanarak GUID 'nizi ekleyin:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Örnek: Azure CLı

GUID 'nizi eklemek için Azure CLı kullandığınızda, bir betiğin kapsamındaki **AZURE_HTTP_USER_AGENT** ortam değişkenini ayarlayın. Değişkeni kabuk kapsamı için de genel olarak ayarlayabilirsiniz:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Daha fazla bilgi için bkz. [Go için Azure SDK](/azure/developer/go/).

## <a name="use-terraform"></a>Terrayform kullanma

Terrayform için destek, Azure sağlayıcının 1.21.0 sürümü: ile kullanılabilir [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Bu, çözümünü Terrayform aracılığıyla dağıtan tüm iş ortakları ve Azure sağlayıcısı tarafından dağıtılan ve ölçülen tüm kaynaklar için geçerlidir (sürüm 1.21.0 veya üzeri).

Terrayform için Azure sağlayıcısı, çözümünüz için kullanılan izleme GUID 'sini belirtmek için [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) adlı yeni bir isteğe bağlı alan ekledi. Bu alanın değeri, *ARM_PARTNER_ID* ortam değişkeninden de kaynak oluşturulabilir.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
*Partner_id* değerini KAYıTLı bir GUID olarak ayarlayın. GUID 'yi "pid-" ile önemeyin, gerçek GUID olarak ayarlamanız yeterlidir.

> [!IMPORTANT]
> Ticari Market 'te Azure uygulamasıyla Terkform kullanıyorsanız, Iş Ortağı Merkezi ' nde sunulan tüm izleme KIMLIĞINI kullanın. GUID kullanmayın.

## <a name="get-support"></a>Destek alın

Ticari Market 'teki destek seçenekleri hakkında bilgi edinmek [Için Iş Ortağı Merkezi 'nde ticari Market programı desteği](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Teknik bir danışmandaki istek gönderme

1. [Iş ortağı teknik hizmetlerini](https://aka.ms/TechnicalJourney)ziyaret edin.
1. Teknik yolculuğa bakmak için **bulut altyapısını ve yönetimini** seçin.
1. **Dağıtım Hizmetleri**  >  **bir istek gönder**' i seçin.
1. MSA (MPN hesabı) veya AAD 'nizi (Iş ortağı Pano hesabı) kullanarak oturum açın.
1. Açılan formdaki iletişim bilgilerini doldurun/gözden geçirin. Danışmandaki ayrıntılar önceden doldurulmuş olabilir veya açılan seçenekleriniz olabilir.
1. Sorun için bir başlık ve ayrıntılı bir açıklama girin.
1. **Gönder**’i seçin.

[Teknik satış ve dağıtım hizmetlerini kullanırken](https://aka.ms/TechConsultInstructions)ekran görüntüleriyle birlikte adım adım yönergeleri görüntüleyin.

İhtiyaçlarınızı karşılamak için bir çağrı kurmak üzere Microsoft Iş ortağı teknik danışman ile iletişim kurulacaksınız.

## <a name="report"></a>Rapor
Müşteri kullanımı attributıon aracılığıyla izlenen Azure kullanımı için raporlama, bugün ISV iş ortakları için kullanılamaz. Iş Ortağı Merkezi 'nde ticari Market programına, müşteri kullanımı atışsını kapsayacak şekilde raporlama eklemek, 2021 'in ikinci yarısında hedeflenmelidir.

## <a name="faq"></a>SSS

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>İzleme KIMLIĞI eklendikten sonra değişiklik yapılabilir mi?

Ticari Market 'teki Azure uygulamalarının izleme kimlikleri, Iş Ortağı Merkezi tarafından otomatik olarak yönetilir. Müşteri, bir şablonu indirebilir ve izleme KIMLIĞINI değiştirebilir veya kaldırabilir. İş ortakları, kaldırma veya düzenlemelerinin önlenmesi için izleme KIMLIĞI rolünü önceden, müşterilerine göre tanımlamaya yönelik olmalıdır. İzleme KIMLIĞINI değiştirmek, yalnızca yeni dağıtımları ve kaynakları etkiler, mevcut olanları etkilemez.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>GitHub gibi Microsoft olmayan bir depodan dağıtılan şablonları izleyebilir miyim?

Evet, izleme KIMLIĞI şablon dağıtıldığında bulunduğu sürece kullanım izlenir. Bir yayımcı ve şablonunuz hakkında Microsoft olmayan bir depodan dağıtılan bir ilişkiyi korumak için, önce yayınlanmış şablonunuz (izleme KIMLIĞINI içeren) bir kopyasını, Azure portal teklifinizin ticari Market listesinden indirin. Bu sürümü GitHub veya Microsoft olmayan başka bir depoya yayımlayın.

Şablonunuz ticari Market 'te listelenmiyorsa ve kayıtlı bir GUID içeriyorsa, GUID 'nin GitHub 'da veya başka bir Microsoft dışı depoya yayımladığınız sürümde bulunduğundan emin olun.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Müşteri Raporlama da alıyor mu?

Hayır. Müşteriler Azure portal içindeki tüm kaynakların veya kaynak gruplarının kullanımını izleyebilir. Müşteriler, CUA izleme KIMLIĞI tarafından kesilen kullanımı görmez.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Müşteri kullanımı, dijital kayıt ortağı (DPOR) veya iş ortağı yönetici bağlantısı (PAL) ile benzerdir mi?

Müşteri kullanımı attributıon, Azure kullanımını bir iş ortağının tekrarlanabilir, dağıtılabilir IP 'si ile, dağıtım sırasında ilişkilendirmeyi oluşturan bir mekanizmadır. DPOR ve PAL, bir danışmanlık (Sistem Tümleştirici) veya yönetim (yönetilen hizmet sağlayıcısı) ortağını bir müşterinin ilgili Azure parmak izine, iş ortağı müşteriyle çalışırken geçen süre ile ilişkilendirmek üzere tasarlanmıştır.