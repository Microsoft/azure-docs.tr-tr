---
title: Kimlik doğrulamasını ayarlama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning çeşitli kaynaklar ve iş akışları için kimlik doğrulamasını ayarlamayı ve yapılandırmayı öğrenin.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: ee767cbeafcce604ecb4a79ce0fdf21c70bbb47c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219646"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama


Azure Machine Learning çalışma alanınıza nasıl kimlik doğrulaması ayarlayacağınızı öğrenin. Azure Machine Learning çalışma alanınıza yönelik kimlik doğrulaması, çoğu şey için __Azure Active Directory__ (Azure AD) temel alır. Genel olarak, çalışma alanına bağlanırken kullanabileceğiniz üç kimlik doğrulama iş akışı vardır:

* __Etkileşimli__: hesabınızı doğrudan kimlik doğrulamak veya kimlik doğrulama için kullanılan bir belirteç almak üzere Azure Active Directory ' de kullanırsınız. Etkileşimli kimlik doğrulaması _deneme ve yinelemeli geliştirme_ sırasında kullanılır. Etkileşimli kimlik doğrulaması, kaynaklara erişimi (örneğin, bir Web hizmeti) Kullanıcı başına temelinde denetlemenize olanak sağlar.

* __Hizmet sorumlusu__: Azure Active Directory ' de bir hizmet sorumlusu hesabı oluşturur ve bir belirteç almak veya bir belirteci almak için kullanın. Hizmet sorumlusu, Kullanıcı etkileşimi gerektirmeden hizmette _kimlik doğrulaması yapmak için otomatik bir işlem_ gerektiğinde kullanılır. Örneğin, eğitim kodu her değiştiğinde bir modeli gösteren ve test eden bir sürekli tümleştirme ve dağıtım betiği.

* __Yönetilen kimlik__: _bir azure sanal makinesinde_ Azure Machine Learning SDK kullanırken, Azure için yönetilen bir kimlik kullanabilirsiniz. Bu iş akışı, kimlik bilgilerini Python kodunda depolamadan veya kullanıcıdan kimlik doğrulaması yapmasını istemeden, VM 'nin yönetilen kimliği kullanarak çalışma alanına bağlanmasına izin verir. Azure Machine Learning işlem kümeleri, _eğitim modelleri_ sırasında çalışma alanına erişmek için yönetilen bir kimlik kullanmak üzere de yapılandırılabilir.

> [!IMPORTANT]
> Kullanılan kimlik doğrulama iş akışından bağımsız olarak, kaynaklara izin verilen erişim düzeyini (yetkilendirme) kapsamı için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanılır. Örneğin, bir yönetici veya Otomasyon işleminin bir işlem örneği oluşturmak için erişimi olabilir, ancak bunu kullanmayabilir, bir veri bilimconu onu kullanabilir ancak silemez veya oluşturamaz. Daha fazla bilgi için bkz. [Azure Machine Learning erişimi yönetme](how-to-assign-roles.md).

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md)oluşturun.
* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure Machine Learning işlem örneği](concept-azure-machine-learning-architecture.md#compute-instance) kullanın.

## <a name="azure-active-directory"></a>Azure Active Directory

Çalışma alanınızın tüm kimlik doğrulama iş akışları Azure Active Directory kullanır. Kullanıcıların, bireysel hesapları kullanarak kimlik doğrulaması yapmasını istiyorsanız, Azure AD 'de hesapların olması gerekir. Hizmet sorumlularını kullanmak istiyorsanız, Azure AD 'de mevcut olmaları gerekir. Yönetilen kimlikler aynı zamanda Azure AD 'nin bir özelliğidir. 

Azure AD hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması nedir](..//active-directory/authentication/overview-authentication.md).

Azure AD hesaplarını oluşturduktan sonra, Azure Machine Learning çalışma alanına ve diğer işlemlere erişim verme hakkında bilgi için bkz. [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md) .

## <a name="configure-a-service-principal"></a>Hizmet sorumlusu yapılandırma

Hizmet sorumlusu (SP) kullanmak için, önce SP 'yi oluşturmanız ve çalışma alanınıza erişim vermeniz gerekir. Daha önce belirtildiği gibi, erişimi denetlemek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanılır, bu nedenle SP 'ye verilecek erişime de karar vermelisiniz.

> [!IMPORTANT]
> Hizmet sorumlusu kullanırken, için kullanılan __görev için gereken en düşük erişimi__ verin. Örneğin, için tümü bir Web dağıtımı için erişim belirtecini okumasından, bir hizmet sorumlusu sahibine veya katkıda bulunan erişime izin vermezsiniz.
>
> En az erişim verme nedeni, hizmet sorumlusunun kimlik doğrulaması için bir parola kullanması ve parolanın bir Otomasyon betiğinin parçası olarak depolanması olabilir. Parolanın sızmış olması halinde, belirli bir görev için gereken en düşük erişime sahip olmak, SP 'nin kötü amaçlı kullanımını en aza indirir.

SP oluşturmanın en kolay yolu [Azure CLI](/cli/azure/install-azure-cli)'yı kullanarak çalışma alanınıza erişim izni vermenizi sağlar. Bir hizmet sorumlusu oluşturmak ve çalışma alanınıza erişim sağlamak için aşağıdaki adımları kullanın:

> [!NOTE]
> Bu adımların tümünü gerçekleştirmek için abonelikte yönetici olmanız gerekir.

1. Azure aboneliğinizde kimlik doğrulaması yapın:

    ```azurecli-interactive
    az login
    ```

    CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) bir yetkilendirme koduna göz atmaya ve girmeye yönelik bilgiler içerir.

    Birden çok Azure aboneliğiniz varsa, `az account set -s <subscription name or ID>` aboneliği ayarlamak için komutunu kullanabilirsiniz. Daha fazla bilgi için bkz. [birden çok Azure aboneliği kullanma](/cli/azure/manage-azure-subscriptions-azure-cli).

    Diğer kimlik doğrulama yöntemleri için bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli).

1. Hizmet sorumlusu oluşturun. Aşağıdaki örnekte, **ml-auth** ADLı bir SP oluşturulur:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Çıktı aşağıdakine benzer bir JSON olacaktır. `clientId`, `clientSecret` , Ve `tenantId` alanlarını, bu makaledeki diğer adımlar için ihtiyaç duyduğunuz gibi bir yere göz atın.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Önceki adımda döndürülen değeri kullanarak hizmet sorumlusunun ayrıntılarını alın `clientId` :

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Aşağıdaki JSON, komuttan gelen çıktının basitleştirilmiş bir örneğidir. Bir `objectId` sonraki adım için değerine ihtiyacınız olacak şekilde, alanı buraya göz atın.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. SP 'nin Azure Machine Learning çalışma alanınıza erişmesine izin verin. , Ve parametreleri için çalışma alanınızın adı ve kaynak grubu adı gerekir `-w` `-g` . Parametresi için `--user` , `objectId` önceki adımdaki değeri kullanın. `--role`Parametresi, hizmet sorumlusu için erişim rolünü ayarlamanıza olanak sağlar. Aşağıdaki örnekte, SP, **sahip** rolüne atanır. 

    > [!IMPORTANT]
    > Sahip erişimi, hizmet sorumlusunun çalışma alanınızdaki neredeyse her türlü işlemi yapmasına izin verir. Bu belgede erişim izni vermeyi göstermek için kullanılır; bir üretim ortamında, Microsoft Hizmet sorumlusuna, istediğiniz rolü gerçekleştirmek için gereken en düşük erişimi vermeyi önerir. Senaryonuz için gerekli erişime sahip özel bir rol oluşturma hakkında bilgi için bkz. [Azure Machine Learning erişimi yönetme](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Bu çağrı, başarı durumunda herhangi bir çıktı oluşturmaz.

## <a name="configure-a-managed-identity"></a>Yönetilen kimliği yapılandırma

> [!IMPORTANT]
> Yönetilen kimlik yalnızca bir Azure sanal makinesinden veya Azure Machine Learning işlem kümesiyle Azure Machine Learning SDK kullanılırken desteklenir. Bir işlem kümesi ile yönetilen bir kimlik kullanılması şu anda önizlemededir.

### <a name="managed-identity-with-a-vm"></a>VM ile yönetilen kimlik

1. [VM 'de Azure kaynakları için sistem tarafından atanan yönetilen kimliği](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)etkinleştirin.

1. [Azure Portal](https://portal.azure.com), çalışma alanınızı seçin ve sonra da __Access Control (IAM)__, __rol ataması Ekle__' yi seçin __ve açılan menüden__ __sanal makine__ ' yi seçin. Son olarak, sanal makinenizin kimliğini seçin.

1. Bu kimliğe atanacak rolü seçin. Örneğin, katkıda bulunan veya özel bir rol. Daha fazla bilgi için bkz. [kaynaklara erişimi denetleme](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>İşlem kümesi ile yönetilen kimlik

Daha fazla bilgi için bkz. [işlem kümesi için yönetilen kimlik ayarlama](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Etkileşimli kimlik doğrulaması kullan

> [!IMPORTANT]
> Etkileşimli kimlik doğrulaması tarayıcınızı kullanır ve tanımlama bilgileri gerektirir (3. taraf tanımlama bilgileri dahil). Tanımlama bilgilerini devre dışı bırakırsanız, "oturum açılamadı" gibi bir hata alabilirsiniz. Bu hata, [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)etkinleştirdiyseniz da oluşabilir.

Belgelerde ve örneklerde birçok örnek etkileşimli kimlik doğrulaması kullanır. Örneğin, SDK kullanırken, otomatik olarak Kullanıcı arabirimi tabanlı kimlik doğrulama akışı isteyen iki işlev çağrısı vardır:

* İşlevi çağırmak `from_config()` , istem olarak verilecek.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` işlevi, çalışma alanı bağlantı bilgilerinizi içeren bir JSON dosyası arar.

* `Workspace`Aboneliği, kaynak grubunu ve çalışma alanı bilgilerini sağlamak için Oluşturucuyu kullanmak etkileşimli kimlik doğrulaması için de istemde yer alacak.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Birden çok kiracıya erişiminiz varsa, sınıfı içeri aktarmanız ve hedeflediğiniz kiracıyı açıkça tanımlamanız gerekebilir. Oluşturucusunun çağrılması, `InteractiveLoginAuthentication` Yukarıdaki çağrılara benzer bir oturum açma bilgileri de ister.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Azure CLı kullanırken, `az login` CLI oturumunun kimliğini doğrulamak için komutu kullanılır. Daha fazla bilgi için bkz. [Azure CLI kullanmaya başlama](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Daha önce Azure CLı kullanarak daha önce kimlik doğrulaması yapmış olduğunuz bir ortamdan SDK kullanıyorsanız, `AzureCliAuthentication` CLI tarafından önbelleğe alınan kimlik bilgilerini kullanarak çalışma alanında kimlik doğrulaması yapmak için sınıfını kullanabilirsiniz:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulamasını kullanma

SDK 'dan çalışma alanınızda kimlik doğrulaması gerçekleştirmek için hizmet sorumlusu kullanarak `ServicePrincipalAuthentication` sınıf oluşturucusunu kullanın. Hizmet sağlayıcısını parametre olarak oluştururken aldığınız değerleri kullanın. `tenant_id`Parametresi, `tenantId` yukarıdakiyle eşlenir, ile `service_principal_id` eşlenir `clientId` ve ile `service_principal_password` eşlenir `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Değişken artık doğrudan SDK 'da kullandığınız bir kimlik doğrulama nesnesini barındırır. Genel olarak, aşağıdaki kodda gösterildiği gibi, yukarıda kullanılan kimlikleri/gizli dizileri ortam değişkenlerinde depolamak iyi bir fikirdir. Ortam değişkenlerinde depolama, bilgilerin yanlışlıkla bir GitHub deposuna denetlenmesini önler.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python 'da çalışan ve SDK 'Yı ilk olarak kullanan otomatik iş akışları için bu nesneyi, kimlik doğrulamalarınız için çoğu durumda olduğu gibi kullanabilirsiniz. Aşağıdaki kod, oluşturduğunuz auth nesnesini kullanarak çalışma alanınızın kimliğini doğrular.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Azure CLı 'dan bir hizmet sorumlusu kullanma

Azure CLı komutları için hizmet sorumlusu kullanabilirsiniz. Daha fazla bilgi için bkz. [hizmet sorumlusu kullanarak oturum açma](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>REST API (Önizleme) ile hizmet sorumlusu kullanma

Hizmet sorumlusu Ayrıca Azure Machine Learning [REST API](/rest/api/azureml/) (Önizleme) kimlik doğrulaması için de kullanılabilir. Otomatik iş akışlarında gözetimsiz kimlik doğrulaması için hizmetten hizmete çağrılara izin veren Azure Active Directory [istemci kimlik bilgileri verme akışı](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)' nı kullanırsınız. Örnekler hem Python hem de Node.js için [adal kitaplığı](../active-directory/azuread-dev/active-directory-authentication-libraries.md) ile uygulanır, ancak OpenID Connect 1,0 'yi destekleyen herhangi bir açık kaynak kitaplığı da kullanabilirsiniz.

> [!NOTE]
> MSAL.js, ADAL 'dan daha yeni bir kitaplıktır, ancak özellikle belirli bir kullanıcıya bağlı etkileşimli/UI kimlik doğrulaması için tasarlanan bir istemci tarafı kitaplığı olduğundan, MSAL.js istemci kimlik bilgilerini kullanarak hizmetten hizmete kimlik doğrulaması yapılamaz. REST API ile otomatikleştirilmiş iş akışları oluşturmak için aşağıda gösterildiği gibi ADAL kullanmanızı öneririz.

#### <a name="nodejs"></a>Node.js

Node.js kullanarak bir kimlik doğrulama belirteci oluşturmak için aşağıdaki adımları kullanın. Ortamınızda öğesini çalıştırın `npm install adal-node` . Daha sonra, `tenantId` `clientId` ve `clientSecret` Yukarıdaki adımlarda oluşturduğunuz hizmet sorumlusunun aşağıdaki betikteki eşleşen değişkenlerin değerleri olarak değerini kullanın.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Değişkeni, `tokenResponse` belirteci ve sona erme saati gibi ilişkili meta verileri içeren bir nesnedir. Belirteçler 1 saat için geçerlidir ve yeni bir belirteç almak için aynı çağrı çalıştırılarak yenilenebilir. Aşağıdaki kod parçacığı örnek bir yanıttır.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

`accessToken`Kimlik doğrulama belirtecini getirmek için özelliğini kullanın. API çağrıları yapmak için belirteci kullanma hakkında örnekler için [REST API belgelerine](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) bakın.

#### <a name="python"></a>Python

Python kullanarak bir kimlik doğrulama belirteci oluşturmak için aşağıdaki adımları kullanın. Ortamınızda öğesini çalıştırın `pip install adal` . Daha sonra, `tenantId` `clientId` ve `clientSecret` Yukarıdaki adımlarda oluşturduğunuz hizmet sorumlusunun, aşağıdaki komut dosyasında uygun değişkenlerin değerleri olarak değerlerini kullanın.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Değişken, `token_response` belirteci ve sona erme saati gibi ilişkili meta verileri içeren bir sözlüktür. Belirteçler 1 saat için geçerlidir ve yeni bir belirteç almak için aynı çağrı çalıştırılarak yenilenebilir. Aşağıdaki kod parçacığı örnek bir yanıttır.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]`Kimlik doğrulama belirtecini getirmek için kullanın. API çağrıları yapmak için belirteci kullanma hakkında örnekler için [REST API belgelerine](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) bakın.

#### <a name="java"></a>Java

Java 'da, standart bir REST çağrısını kullanarak taşıyıcı belirtecini alın:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Yukarıdaki kodun dışındaki özel durumları ve durum kodlarını işlemesi gerekir, ancak bu, şu `200 OK` kalıbı gösterir: 

- Programınızın erişimi olması gerektiğini doğrulamak için istemci KIMLIĞINI ve gizli anahtarını kullanın
- Nerede aranması gerektiğini belirtmek için kiracı KIMLIĞINIZI kullanın `login.microsoftonline.com`
- Yetkilendirme belirtecinin kaynağı olarak Azure Resource Manager kullanın

## <a name="use-managed-identity-authentication"></a>Yönetilen kimlik kimlik doğrulamasını kullanma

Yönetilen bir kimlikle yapılandırılmış bir VM veya hesaplama kümesinden çalışma alanında kimlik doğrulaması yapmak için, `MsiAuthentication` sınıfını kullanın. Aşağıdaki örnek, bir çalışma alanında kimlik doğrulaması yapmak için bu sınıfın nasıl kullanılacağını gösterir:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Sonraki adımlar

* [Eğitiminde gizli dizileri kullanma](how-to-use-secrets-in-runs.md).
* [Web hizmeti olarak dağıtılan modeller için kimlik doğrulamasını yapılandırma](how-to-authenticate-web-service.md).
* [Web hizmeti olarak dağıtılan bir Azure Machine Learning modelini kullanma](how-to-consume-web-service.md).
