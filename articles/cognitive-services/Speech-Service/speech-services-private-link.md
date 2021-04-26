---
title: Konuşma hizmetleriyle özel uç noktaları kullanma
titleSuffix: Azure Cognitive Services
description: Azure özel bağlantı tarafından sunulan özel uç noktalarla konuşma hizmetlerini nasıl kullanacağınızı öğrenin
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: 6971c6f0959135c7de1f41bcd49adde514f87941
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625492"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Özel bir uç nokta aracılığıyla konuşma hizmetlerini kullanma

[Azure özel bağlantısı](../../private-link/private-link-overview.md) , [özel bir uç nokta](../../private-link/private-endpoint-overview.md)kullanarak Azure 'daki hizmetlere bağlanmanızı sağlar. Özel uç nokta, yalnızca belirli bir [sanal ağ](../../virtual-network/virtual-networks-overview.md) ve alt ağ içinde erişilebilen özel bir IP adresidir.

Bu makalede, Azure bilişsel hizmetler 'de konuşma hizmetleriyle özel bağlantı ve özel uç noktaların nasıl ayarlanacağı ve kullanılacağı açıklanmaktadır.
Bu makale daha sonra özel uç noktaların nasıl kaldırılacağını açıklar, ancak hala konuşma kaynağını kullanır.

> [!NOTE]
> Devam etmeden önce bilişsel [Hizmetler ile sanal ağların nasıl kullanılacağını](../cognitive-services-virtual-networks.md)inceleyin.



## <a name="create-a-custom-domain-name"></a>Özel etki alanı adı oluşturma

Özel uç noktalar bilişsel [Hizmetler için özel bir alt etki alanı adı](../cognitive-services-custom-subdomains.md)gerektirir. Konuşma kaynağınız için bir tane oluşturmak üzere aşağıdaki yönergeleri kullanın.

> [!WARNING]
> Özel bir etki alanı adı kullanan bir konuşma kaynağı, konuşma hizmetleriyle farklı bir şekilde etkileşime girer.
> Bir konuşma kaynağını özel bir uç nokta ile kullanmak ve ayrıca özel uç nokta _olmadan_ bir konuşma kaynağı kullanmak için uygulama kodunuzu ayarlamanız gerekebilir.
> Özel etki alanı adı geçişi _geri alınamaz olduğundan_ her iki senaryo da gerekebilir.
>
> Özel bir etki alanı adını açtığınızda, işlem geri [alınamaz](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). [Bölgesel ada](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) geri dönmenin tek yolu yeni bir konuşma kaynağı oluşturmaktır.
>
> Konuşma kaynağınız, [konuşma Studio](https://speech.microsoft.com/)ile oluşturulmuş çok sayıda ilişkili özel model ve proje içeriyorsa, üretimde kullanılan kaynağı değiştirmeden önce yapılandırmayı bir test kaynağıyla denemeyi kesinlikle öneririz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal kullanarak özel bir etki alanı adı oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalına](https://portal.azure.com/) gidin ve Azure hesabınızla oturum açın.
1. Gerekli konuşma kaynağını seçin.
1. Sol bölmedeki **kaynak yönetimi** grubunda **ağ**' ı seçin.
1. **Güvenlik duvarları ve sanal ağlar** sekmesinde **özel etki alanı adı oluştur**' u seçin. Kaynağınız için benzersiz bir özel alt etki alanı oluşturma yönergeleriyle birlikte yeni bir sağ panel görüntülenir.
1. **Özel etki alanı adı oluştur** panelinde, özel bir etki alanı adı girin. Tam özel etki alanınız şöyle görünür: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Özel bir etki alanı adı oluşturduktan sonra _, bunun değiştirilemeyeceğini_ unutmayın.
    
    Özel etki alanı adınızı girdikten sonra **Kaydet**' i seçin.
1. İşlem bittikten sonra, **kaynak yönetimi** grubunda **anahtarlar ve uç nokta**' ı seçin. Kaynağınızın yeni uç nokta adının bu şekilde başlatıldığını onaylayın: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell kullanarak özel bir etki alanı adı oluşturmak için, bilgisayarınızda Azure PowerShell Module Version 5.1.0 veya üzeri ile PowerShell sürüm 7. x veya üzeri olduğunu doğrulayın. Bu araçların sürümlerini görmek için şu adımları izleyin:

1. Bir PowerShell penceresinde şunu girin:

    `$PSVersionTable`

    `PSVersion`Değerin 7. x veya üzeri olduğunu doğrulayın. PowerShell 'i yükseltmek için, [çeşitli PowerShell sürümlerini yükleme](/powershell/scripting/install/installing-powershell)bölümündeki yönergeleri izleyin.

1. Bir PowerShell penceresinde şunu girin:

    `Get-Module -ListAvailable Az`

    Hiçbir şey görünürse veya Azure PowerShell modülünün bu sürümü 5.1.0 ' den daha eski ise, yükseltmek için [Azure PowerShell modülünü Install](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.

Devam etmeden önce, `Connect-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Özel bir etki alanı adının kullanılabilir olduğunu doğrulama

Kullanmak istediğiniz özel etki alanının kullanılabilir olup olmadığını denetleyin. Aşağıdaki kod, bilişsel hizmetler REST API [etki alanı kullanılabilirliğini denetle](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) işlemini kullanarak etki alanının kullanılabildiğini onaylar.

> [!TIP]
> Aşağıdaki kod *Azure Cloud Shell çalışmaz.*

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
İstenen ad varsa şöyle bir yanıt görürsünüz:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Ad zaten alınmış ise, aşağıdaki yanıtı görürsünüz:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Özel etki alanı adınızı oluşturma

Seçili konuşma kaynağı için özel bir etki alanı adı açmak üzere [set-Azbiliveservicesaccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet 'ini kullanın.

> [!WARNING]
> Aşağıdaki kod başarıyla çalıştıktan sonra, konuşma kaynağınız için özel bir etki alanı adı oluşturacaksınız. Bu *adın değiştirilemeyeceğini* unutmayın.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Bu bölüm, Azure CLı 'nın en son sürümünü gerektirir. Azure Cloud Shell kullanıyorsanız, en son sürüm zaten yüklüdür.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Özel etki alanı adının kullanılabilir olduğunu doğrulama

Kullanmak istediğiniz özel etki alanının serbest olup olmadığını denetleyin. Bilişsel hizmetler REST API [etki alanı kullanılabilirliğini denetle](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) yöntemini kullanın.

Aşağıdaki kod bloğunu kopyalayın, tercih ettiğiniz özel etki alanı adınızı ekleyin ve dosyaya kaydedin `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Dosyayı geçerli klasörünüze kopyalayın veya Azure Cloud Shell karşıya yükleyin ve aşağıdaki komutu çalıştırın. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` öğesini Azure abonelik kimliğinizle değiştirin.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
İstenen ad varsa şöyle bir yanıt görürsünüz:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Ad zaten alınmış ise, aşağıdaki yanıtı görürsünüz:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="turn-on-a-custom-domain-name"></a>Özel bir etki alanı adını aç

Seçilen konuşma kaynağıyla özel bir etki alanı adı kullanmak için [az biliveservices hesabı Güncelleştir](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) komutunu kullanın.

Konuşma kaynağını içeren Azure aboneliğini seçin. Azure hesabınızda yalnızca bir etkin abonelik varsa, bu adımı atlayabilirsiniz. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` öğesini Azure abonelik kimliğinizle değiştirin.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Özel etki alanı adını seçili kaynak olarak ayarlayın. Örnek parametre değerlerini gerçek olanlarla değiştirin ve aşağıdaki komutu çalıştırın.

> [!WARNING]
> Aşağıdaki komutun başarılı bir şekilde yürütülmesinden sonra, konuşma kaynağınız için özel bir etki alanı adı oluşturacaksınız. Bu *adın değiştirilemeyeceğini* unutmayın.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="turn-on-private-endpoints"></a>Özel uç noktaları aç

Sanal ağa bağlı [özel DNS bölgesinin](../../dns/private-dns-overview.md) özel uç noktalar için gerekli güncelleştirmelerle kullanılması önerilir. Sağlama işlemi sırasında özel bir DNS bölgesi oluşturabilirsiniz. Kendi DNS sunucunuzu kullanıyorsanız, DNS yapılandırmanızı de değiştirmeniz gerekebilir.

Bir üretim konuşma kaynağı için özel uç noktalar sağlamadan *önce* DNS stratejisine karar verin. Ve, özellikle kendi DNS sunucunuzu kullanıyorsanız, DNS değişikliklerinizi test edin.

Özel uç noktalar oluşturmak için aşağıdaki makalelerden birini kullanın.
Bu makaleler, Özel uç noktalar aracılığıyla kullanılabilir hale getirmek için bir örnek kaynak olarak bir Web uygulaması kullanır.

- [Azure portal kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-portal.md)
- [Azure PowerShell kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-powershell.md)
- [Azure CLı kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-cli.md)

Seçtiğiniz makaledeki parametreler yerine bu parametreleri kullanın:

| Ayar             | Değer                                    |
|---------------------|------------------------------------------|
| Kaynak türü       | **Microsoft. Biliveservices/hesapları** |
| Kaynak            | **\<your-speech-resource-name>**         |
| Hedef alt kaynak | **açın**                              |

**Özel uç noktalar Için DNS:** Bilişsel [Hizmetler kaynaklarındaki özel uç noktalar Için DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)genel ilkelerini gözden geçirin. Ardından, aşağıdaki bölümlerde açıklanan denetimleri gerçekleştirerek DNS yapılandırmanızın doğru çalıştığını onaylayın.

### <a name="resolve-dns-from-the-virtual-network"></a>DNS 'i sanal ağdan çözümle

Bu denetim *gereklidir*.

Sanal ağınızdan özel DNS girişini sınamak için aşağıdaki adımları izleyin:

1. Özel uç noktanızı eklediğiniz sanal ağda bulunan bir sanal makinede oturum açın. 
1. Bir Windows komut istemi veya bash kabuğu açın, çalıştırın `nslookup` ve kaynağınızın özel etki alanı adını başarıyla çözümlediği onaylayın.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. IP adresinin özel uç noktanızın IP adresiyle eşleştiğinden emin olun.

### <a name="resolve-dns-from-other-networks"></a>Diğer ağlardan DNS 'i çözümleme

Bu denetimi yalnızca kaynağınızın **ağ** bölümünde **tüm ağlar** seçeneğini veya **Seçili ağlar ve özel uç noktalar** erişim seçeneğini açtıysanız gerçekleştirin. 

Kaynağa yalnızca özel bir uç nokta kullanarak erişmeyi planlıyorsanız, bu bölümü atlayabilirsiniz.

1. Kaynağa erişimine izin verilen bir ağa bağlı bir bilgisayarda oturum açın.
2. Bir Windows komut istemi veya bash kabuğu açın, çalıştırın `nslookup` ve kaynağınızın özel etki alanı adını başarıyla çözümlediği onaylayın.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> Çözümlenen IP adresi, ağ trafiğini bilişsel hizmetler kaynağı için özel uç noktaya bağlayan bir sanal ağ proxy uç noktasını işaret eder. Özel bir etki alanı adı olan ancak özel uç noktaları *olmayan* bir kaynak için davranış farklı olacaktır. Ayrıntılar için [Bu bölüme](#dns-configuration) bakın.

## <a name="adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint"></a>Bir uygulamayı özel bir uç noktasıyla konuşma kaynağını kullanacak şekilde ayarlama

Özel etki alanı olan bir konuşma kaynağı farklı bir şekilde konuşma hizmetleriyle etkileşime girer. Bu, Özel uç noktaları ile ve olmayan özel etki alanı etkin bir konuşma kaynağı için geçerlidir. Bu bölümdeki bilgiler her iki senaryo için de geçerlidir.

Mevcut uygulamaları ve çözümleri, özel bir etki alanı adı ve özel bir uç nokta açık olan bir konuşma kaynağını kullanacak şekilde ayarlamak için bu bölümdeki yönergeleri izleyin.

Özel bir etki alanı adı ve özel bir uç noktası açık olan bir konuşma kaynağı, konuşma hizmetleriyle etkileşmek için farklı bir yol kullanır. Bu bölümde, bu tür bir kaynağın konuşma Hizmetleri REST API 'Leri ve [konuşma SDK 'sı](speech-sdk.md)ile nasıl kullanılacağı açıklanmaktadır.

> [!NOTE]
> Özel bir etki alanı adı kullanan özel uç noktaları olmayan bir konuşma kaynağı, konuşma hizmetleriyle etkileşim kurmak için özel bir yol da içerir.
> Bu şekilde, özel bir uç nokta kullanan bir konuşma kaynağının senaryosundan farklıdır. Bu, Özel uç noktaları daha sonra kaldırmaya karar verirken göz önünde bulundurmanız önemlidir.
> Bkz. Bu makalede, _bir uygulamayı özel uç noktaları olmadan bir konuşma kaynağını kullanacak şekilde ayarlama_ .

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Özel etki alanı adı ve özel uç nokta içeren konuşma kaynağı: REST API 'lerle kullanım

`my-private-link-speech.cognitiveservices.azure.com`Bu bölüm için örnek bir konuşma kaynağı DNS adı (özel etki alanı) olarak kullanacağız.

Konuşma Hizmetleri, [konuşmadan metne](rest-speech-to-text.md) ve [METINDEN konuşmaya](rest-text-to-speech.md)kadar REST API 'lerine sahiptir. Özel uç nokta etkin senaryosu için aşağıdaki bilgileri göz önünde bulundurun.

Konuşmadan metne iki REST API 'si vardır. Her API farklı bir amaca hizmet eder, farklı uç noktalar kullanır ve bunu özel uç nokta etkin senaryosunda kullandığınızda farklı bir yaklaşım gerektirir.

Konuşmadan metne REST API 'Leri şunlardır:
- [Toplu iş dökümü](batch-transcription.md) ve [özel konuşma tanıma](custom-speech-overview.md)için kullanılan [konuşmayı metne REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). v 3.0, [v 2.0 'ın bir ardıldır](./migrate-v2-to-v3.md)
- Çevrimiçi döküm için kullanılan [kısa ses Için konuşmaya metne REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 

Kısa ses için konuşmadan metne REST API kullanımı ve özel uç nokta senaryosunda metinden konuşmaya REST API aynı. Bu makalenin ilerleyen kısımlarında açıklanan [konuşma SDK 'nın büyük](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) bölümüne eşdeğerdir. 

Konuşmadan metne REST API v 3.0 farklı bir uç nokta kümesi kullanır, bu nedenle özel uç nokta etkin senaryosu için farklı bir yaklaşım gerektirir.

Sonraki alt bölümlerde her iki durum da açıklanır.

#### <a name="speech-to-text-rest-api-v30"></a>Konuşmayı metne REST API v 3.0

Genellikle, konuşma kaynakları, [konuşmadan metne REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)ile iletişim kurmak için bilişsel [Hizmetler bölgesel uç noktaları](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) kullanır. Bu kaynaklar aşağıdaki adlandırma biçimine sahiptir: <p/>`{region}.api.cognitive.microsoft.com`.

Bu bir örnek istek URL 'sidir:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Azure Kamu ve Azure Çin uç noktaları için [Bu makaleye](sovereign-clouds.md) bakın.

Bir konuşma kaynağı için özel bir etki alanını etkinleştirdikten sonra (Özel uç noktalar için gereklidir), bu kaynak temel REST API uç noktası için aşağıdaki DNS ad modelini kullanır: <p/>`{your custom name}.cognitiveservices.azure.com`

Bu, örneğimizde REST API uç noktası adının olacağı anlamına gelir: <p/>`my-private-link-speech.cognitiveservices.azure.com`

Ve örnek istek URL 'sinin dönüştürülmesi gerekir:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Bu URL 'ye, Özel uç nokta eklenmiş olan sanal ağdan ulaşılamamalıdır ( [doğru DNS çözümlemesi](#resolve-dns-from-the-virtual-network)sağlanmış).

Bir konuşma kaynağı için özel etki alanı adını etkinleştirdikten sonra, genellikle tüm istek URL 'Lerinde ana bilgisayar adını yeni özel etki alanı ana bilgisayar adıyla değiştirirsiniz. İsteğin diğer tüm bölümleri ( `/speechtotext/v3.0/transcriptions` önceki örnekteki yol gibi) aynı kalır.

> [!TIP]
> Bazı müşteriler bölgesel bitiş noktasının DNS adının bölge kısmını kullanan uygulamalar geliştirmektedir (örneğin, isteği belirli bir Azure bölgesinde dağıtılan konuşma kaynağına göndermek için).
>
> Bir konuşma kaynağı için özel bir etki alanı, kaynağın dağıtıldığı bölge *hakkında bilgi içermez* . *Bu nedenle, daha* önce açıklanan uygulama mantığı çalışmayacak ve değiştirilmesi gerekiyor.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Kısa ses ve metinden konuşmaya REST API için konuşmadan metne REST API

[Kısa ses Için konuşmadan metne REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) ve [metinden konuşmaya REST API](rest-text-to-speech.md) iki uç nokta türünü kullanır:
- Bilişsel hizmetler, bir yetkilendirme belirteci almak için bilişsel hizmetler REST API iletişim için [Bölgesel uç noktalar](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)
- Diğer tüm işlemler için özel uç noktalar

> [!NOTE]
> Azure Kamu ve Azure Çin uç noktaları için [Bu makaleye](sovereign-clouds.md) bakın.

Özel uç noktaların ayrıntılı açıklaması ve bir özel uç nokta etkin konuşma kaynağı için URL 'nin nasıl dönüştürülmesi gerektiği, [Bu alt bölümde](#construct-endpoint-url) konuşma SDK 'sı ile kullanım hakkında bilgi sağlanır. SDK için açıklanan ilke, kısa ses için konuşmadan metne REST API ve metinden konuşmaya REST API için geçerlidir.

Önceki paragrafta bahsedilen alt bölümünde bulunan malzemeyi öğrenin ve aşağıdaki örneğe bakın. Örnekte, metinden konuşmaya REST API açıklanmaktadır. Kısa ses için konuşmadan metne REST API kullanımı tamamen eşdeğerdir.

> [!NOTE]
> Özel uç nokta senaryolarında kısa ses ve metinden konuşmaya REST API için konuşmadan metne REST API kullanırken, üst bilgiyle geçirilen bir abonelik anahtarı kullanın `Ocp-Apim-Subscription-Key` . (Kısa ses ve [metinden konuşmaya REST API](rest-text-to-speech.md#request-headers) [için konuşmadan metne REST API](rest-speech-to-text.md#request-headers) yönelik ayrıntılara bakın)
>
> Bir yetkilendirme belirtecinin kullanılması ve üstbilgi aracılığıyla özel uç noktaya geçirilmesi `Authorization` *yalnızca* konuşma kaynağınızın **ağ** bölümünde bulunan **tüm ağlar** erişim seçeneğini açtıysanız çalışır. Diğer durumlarda, `Forbidden` `BadRequest` bir yetkilendirme belirteci almaya çalışırken ya da hata alırsınız.

**Metin okuma REST API kullanım örneği**

Batı Avrupa örnek bir Azure bölgesi olarak ve `my-private-link-speech.cognitiveservices.azure.com` örnek bir konuşma kaynağı DNS adı (özel etki alanı) olarak kullanacağız. Örneğimizde özel etki alanı adı, `my-private-link-speech.cognitiveservices.azure.com` Batı Avrupa bölgesinde oluşturulan konuşma kaynağına aittir.

Bölgede desteklenen seslerin listesini almak için aşağıdaki isteği gerçekleştirin:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
[Metin okuma REST API belgelerinde](rest-text-to-speech.md)daha fazla ayrıntı görüntüleyin.

Özel uç nokta özellikli konuşma kaynağı için, aynı işlem için uç nokta URL 'sinin değiştirilmesi gerekir. Aynı istek şöyle görünür:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Konuşma SDK 'Sı için [uç nokta URL 'si](#construct-endpoint-url) alt bölümünde ayrıntılı bir açıklamaya bakın.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Özel etki alanı adı ve özel uç nokta içeren konuşma kaynağı: konuşma SDK 'Sı ile kullanım

Konuşma SDK 'sını özel bir etki alanı adı ve özel uç nokta özellikli konuşma kaynaklarıyla kullanmak, uygulama kodunuzu incelemenizi ve büyük olasılıkla değiştirmenizi gerektirir.

`my-private-link-speech.cognitiveservices.azure.com`Bu bölüm için örnek bir konuşma kaynağı DNS adı (özel etki alanı) olarak kullanacağız.

#### <a name="construct-endpoint-url"></a>Uç nokta URL 'SI oluştur

Genellikle, SDK senaryolarında (kısa ses ve metinden konuşmaya REST API senaryolar için konuşmadan metne REST API), konuşma kaynakları farklı hizmet teklifleri için adanmış bölgesel uç noktaları kullanır. Bu uç noktaların DNS ad biçimi:

`{region}.{speech service offering}.speech.microsoft.com`

Örnek bir DNS adı:

`westeurope.stt.speech.microsoft.com`

Bölgenin tüm olası değerleri (DNS adının ilk öğesi) [konuşma hizmeti tarafından desteklenen bölgelerde](regions.md)listelenmiştir. (Azure Kamu ve Azure Çin uç noktaları için [Bu makaleye](sovereign-clouds.md) bakın.) Aşağıdaki tabloda, konuşma Hizmetleri Sunumu (DNS adının ikinci öğesi) için olası değerler sunulmaktadır:

| DNS adı değeri | Konuşma hizmeti teklifi                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Özel Komutlar](custom-commands.md)                       |
| `convai`       | [Konuşma dökümü](conversation-transcription.md) |
| `s2s`          | [Konuşma Çevirisi](speech-translation.md)                 |
| `stt`          | [Konuşmayı metne dönüştürme](speech-to-text.md)                         |
| `tts`          | [Metin okuma](text-to-speech.md)                         |
| `voice`        | [Özel ses](how-to-custom-voice.md)                      |

Bu nedenle, önceki örnek ( `westeurope.stt.speech.microsoft.com` ) Batı Avrupa içindeki bir konuşmaya metin uç noktası için temsil eder.

Özel uç nokta etkin uç noktalar, konuşma hizmetleriyle özel bir ara sunucu aracılığıyla iletişim kurar. Bu nedenle, *uç nokta bağlantı URL 'lerini değiştirmeniz gerekir*. 

"Standart" uç nokta URL 'SI şöyle görünür: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Özel bir uç nokta URL 'SI şöyle görünür: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Örnek 1.** Bir uygulama aşağıdaki URL 'YI kullanarak iletişim kuruyor (Batı Avrupa için ABD Ingilizcesi için temel modeli kullanarak konuşma tanıma):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Konuşma kaynağının özel etki alanı adı olduğunda bunu özel uç nokta etkin senaryosunda kullanmak için `my-private-link-speech.cognitiveservices.azure.com` URL 'yi şöyle değiştirmelisiniz:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Ayrıntılara dikkat edin:

- Ana bilgisayar adı, `westeurope.stt.speech.microsoft.com` özel etki alanı ana bilgisayar adı ile değiştirilmiştir `my-private-link-speech.cognitiveservices.azure.com` .
- Özgün DNS adının () ikinci öğesi, `stt` URL yolunun ilk öğesi olur ve özgün yoldan önce gelir. Yani özgün URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` olur `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Örnek 2.** Bir uygulama, özel bir ses modeli kullanarak Batı Avrupa konuşmayı sentezleştirmek için aşağıdaki URL 'YI kullanır:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Aşağıdaki eşdeğer URL, konuşma kaynağının özel etki alanı adının olduğu özel bir uç nokta kullanır `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Örnek 1 ' de aynı ilke uygulanır, ancak bu zaman anahtar öğesi `voice` .

#### <a name="modifying-applications"></a>Uygulamaları değiştirme

Kodunuzu değiştirmek için şu adımları izleyin:

1. Uygulama uç noktası URL 'sini belirleme:

   - [Uygulamanız için günlük kaydını açın](how-to-use-logging.md) ve etkinliği günlüğe kaydetmek için çalıştırın.
   - Günlük dosyasında, araması yapın `SPEECH-ConnectionUrl` . Eşleşen satırlarda `value` parametresi, uygulamanızın konuşma hizmetlerine erişmek için kullandığı tam URL 'yi içerir.

   Örnek:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Bu nedenle, uygulamanın bu örnekte kullanılan URL 'SI:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. `SpeechConfig`Tam uç nokta URL 'si kullanarak bir örnek oluşturun:

   1. Daha önceki [Yapı uç noktası URL 'si](#construct-endpoint-url) bölümünde açıklandığı gibi, az önce belirlediğiniz uç noktayı değiştirin.

   1. Örneğini oluşturma şeklini değiştirin `SpeechConfig` . Büyük olasılıkla uygulamanız şuna benzer bir şey kullanıyor:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Bu, önceki bölümlerde açıklandığımız ana bilgisayar adı ve URL değişikliklerinden dolayı özel uç nokta özellikli bir konuşma kaynağı için çalışmaz. Var olan uygulamanızı özel uç nokta etkin bir kaynağın anahtarını kullanarak herhangi bir değişiklik yapmadan çalıştırmaya çalışırsanız, bir kimlik doğrulama hatası alırsınız (401).

      Bu işlemi gerçekleştirmek için, sınıfın örneğini oluşturma ve "bitiş noktası" ve `SpeechConfig` "bitiş noktası" başlatmasının nasıl kullanılacağı ile değiştirin. Aşağıdaki iki değişkenin tanımlı olduğunu varsayalım:
      - `subscriptionKey` Özel uç nokta özellikli konuşma kaynağının anahtarını içerir.
      - `endPoint` Tam *değiştirilen* uç nokta URL 'sini (karşılık gelen programlama dili için gereken türü kullanarak) içerir. Örneğimizde, bu değişken şunları içermelidir:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Örnek oluşturun `SpeechConfig` :
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Uç nokta URI 'sinde belirtilen sorgu parametreleri, diğer API 'Ler tarafından ayarlansalar bile değiştirilmez. Örneğin, tanıma dili URI 'de sorgu parametresi olarak tanımlanırsa `language=en-US` ve ayrıca `ru-RU` karşılık gelen özelliği aracılığıyla olarak AYARLANDıYSA, URI 'deki dil ayarı kullanılır. Geçerli dil daha sonra `en-US` .
>
> Uç nokta URI 'sinde ayarlanan parametreler her zaman öncelik kazanır. Diğer API 'Ler yalnızca uç nokta URI 'sinde belirtilmeyen parametreleri geçersiz kılabilir.

Bu değişiklikten sonra uygulamanızın özel uç nokta özellikli konuşma kaynaklarıyla çalışması gerekir. Özel uç nokta senaryolarından daha sorunsuz destek üzerinde çalışıyoruz.

## <a name="adjust-an-application-to-use-a-speech-resource-without-private-endpoints"></a>Bir uygulamayı özel uç noktaları olmadan bir konuşma kaynağı kullanacak şekilde ayarlama

Bu makalede, bir konuşma kaynağı için özel bir etki alanı etkinleştirmenin geri *döndürülemez* bir kaç kez kullanıma sunuyoruz. Bu tür bir kaynak, [Bölgesel bitiş noktası adlarını](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)kullanan kişilerle karşılaştırıldığında, konuşma hizmetleriyle iletişim kurmanın farklı bir yolunu kullanacaktır.

Bu bölümde, konuşma Hizmetleri REST API 'Leri ve [konuşma SDK 'sı](speech-sdk.md)ile özel uç noktaları *olmadan* , bir konuşma kaynağının özel bir etki alanı adı ile nasıl kullanılacağı açıklanmaktadır. Bu, özel bir uç nokta senaryosunda kullanılan, ancak özel uç noktaları silinen bir kaynak olabilir.

### <a name="dns-configuration"></a>DNS yapılandırması

Özel uç nokta etkin konuşma kaynağının özel etki alanı DNS adının [Ortak ağlardan nasıl çözümlendiğini](#resolve-dns-from-other-networks)unutmayın. Bu durumda, çözümlenen IP adresi bir sanal ağ için bir proxy uç noktasına işaret eder. Bu uç nokta, ağ trafiğini özel uç nokta etkin bilişsel hizmetler kaynağına dağıtırken kullanılır.

Ancak, *Tüm* kaynak özel uç noktaları kaldırıldığında (veya özel etki alanı adının etkinleştirilmesiyle hemen sonra), konuşma kaynağının CNAME kaydı yeniden sağlanır. Artık ilgili bilişsel [Hizmetler bölgesel uç NOKTASıNıN](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)IP adresine işaret eder.

Bu nedenle komutun çıktısı şöyle görünür `nslookup` :
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
[Bu bölümün](#resolve-dns-from-other-networks)çıkışıyla karşılaştırın.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Özel bir etki alanı adına sahip ve özel uç noktaları olmayan konuşma kaynağı: REST API 'lerle kullanım

#### <a name="speech-to-text-rest-api-v30"></a>Konuşmayı metne REST API v 3.0

Konuşmadan metne REST API v 3.0 kullanımı, [Özel uç nokta özellikli konuşma kaynakları](#speech-to-text-rest-api-v30)ile tamamen eşdeğerdir.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Kısa ses ve metinden konuşmaya REST API için konuşmadan metne REST API

Bu durumda, kısa bir süre için konuşmadan metne REST API kullanımı ve metinden konuşmaya REST API kullanımı, genel durumdan bir özel durumla farklılık içermez. (Aşağıdaki nota bakın.) Kısa ses ve [metinden konuşmaya REST API](rest-text-to-speech.md) belgeleri [için konuşmadan metne REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) , her iki API 'yi de kullanmanız gerekir.

> [!NOTE]
> Özel etki alanı senaryolarında kısa ses ve metinden konuşmaya REST API için konuşmadan metne REST API kullanırken, üst bilgiyle geçirilen bir abonelik anahtarı kullanın `Ocp-Apim-Subscription-Key` . (Kısa ses ve [metinden konuşmaya REST API](rest-text-to-speech.md#request-headers) [için konuşmadan metne REST API](rest-speech-to-text.md#request-headers) yönelik ayrıntılara bakın)
>
> Bir yetkilendirme belirtecinin kullanılması ve üstbilgi aracılığıyla özel uç noktaya geçirilmesi `Authorization` *yalnızca* konuşma kaynağınızın **ağ** bölümünde bulunan **tüm ağlar** erişim seçeneğini açtıysanız çalışır. Diğer durumlarda, `Forbidden` `BadRequest` bir yetkilendirme belirteci almaya çalışırken ya da hata alırsınız.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Özel bir etki alanı adına sahip ve özel uç noktaları olmayan konuşma kaynağı: konuşma SDK 'Sı ile kullanım

Özel uç noktalar *olmadan* , özel etki alanı etkinleştirilmiş konuşma kaynaklarıyla konuşma SDK 'sını kullanmak, [konuşma SDK belgelerinde](speech-sdk.md)açıklandığı gibi genel durum ile eşdeğerdir.

Kodunuzu [Özel uç nokta özellikli bir konuşma kaynağıyla](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)kullanmak üzere değiştirdiyseniz, aşağıdakileri göz önünde bulundurun.

[Özel uç nokta özellikli konuşma kaynaklarında](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)bulunan bölümünde, uç nokta URL 'sini belirlemeyi, değiştirmeyi ve sınıf örneğinin "uç noktasından" bir "başlatmasıyla nasıl çalışacağını açıklıyoruz `SpeechConfig` .

Ancak, tüm özel uç noktaları kaldırıldıktan sonra aynı uygulamayı çalıştırmayı denerseniz (buna karşılık gelen DNS kaydı yeniden sağlanması için bir süre sağlar), bir iç hizmet hatası alırsınız (404). Bunun nedeni, [DNS kaydının](#dns-configuration) artık sanal ağ proxy 'si yerine bölgesel bilişsel hizmetler uç noktasını işaret ettiğini ve gibi URL yollarının `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` burada bulunamayadır.

Aşağıdaki kodun stilinde uygulamanızı standart örneklemeyle geri almanız gerekir `SpeechConfig` :

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Azure Özel Bağlantı](../../private-link/private-link-overview.md)
* [Konuşma SDK'sı](speech-sdk.md)
* [Konuşmayı metne dönüştürme REST API'si](rest-speech-to-text.md)
* [Metin okuma REST API'si](rest-text-to-speech.md)