---
title: Öğretici-.NET ' te Azure Web App ile Azure Key Vault kullanma
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir ASP.NET Core uygulamasında bir Azure Web uygulaması yapılandıracaksınız.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 91467d1de3a1543736115ef7a25281733c8eb85d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819223"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Öğretici: .NET 'teki bir Azure Web uygulamasına Key Vault bağlamak için yönetilen bir kimlik kullanın

[Azure Key Vault](./overview.md) , artırılmış güvenlik ile kimlik bilgilerini ve diğer gizli dizileri depolamanın bir yolunu sağlar. Ancak kodunuzun bunları alabilmesi için Key Vault kimlik doğrulaması gerekir. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md) , azure hizmetlerine Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik vererek bu sorunu çözmeye yardımcı olur. Kodunuzda kimlik bilgilerini görüntülemesi gerekmeden Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için bu kimliği kullanabilirsiniz.

Bu öğreticide, [Azure App Service](../../app-service/overview.md)için Azure Web uygulaması oluşturup dağıtacaksınız. [.NET için Azure Key Vault gizli istemci kitaplığı](/dotnet/api/overview/azure/key-vault) ve [Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak Azure Anahtar Kasası ile Azure Web uygulamanızın kimliğini doğrulamak için yönetilen bir kimlik kullanacaksınız. Tercih ettiğiniz geliştirme dilini, Azure PowerShell ve/veya Azure portal kullandığınızda aynı temel ilkeler geçerlidir.

Bu öğreticide sunulan Azure App Service Web uygulamaları ve dağıtımı hakkında daha fazla bilgi için bkz.:
- [App Service’e genel bakış](../../app-service/overview.md)
- [Azure App Service bir ASP.NET Core Web uygulaması oluşturma](../../app-service/quickstart-dotnetcore.md)
- [Azure App Service için yerel git dağıtımı](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. [Ücretsiz bir tane oluşturun.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3,1 SDK (veya üzeri)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Sürüm 2.28.0 veya üstünün [Git](https://www.git-scm.com/downloads) yüklemesi.
* [Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)veya [Azure PowerShell](quick-create-powershell.md)kullanarak bir Anahtar Kasası oluşturabilirsiniz.
* [Gizli](../secrets/about-secrets.md)dizi Key Vault. [Azure Portal](../secrets/quick-create-portal.md), [POWERSHELL](../secrets/quick-create-powershell.md)veya [Azure CLI](../secrets/quick-create-cli.md)kullanarak bir gizli dizi oluşturabilirsiniz.

Zaten Azure App Service Web uygulamanız dağıtılmışsa, [bir Anahtar Kasası için Web uygulaması erişimini yapılandırma](#create-and-assign-a-managed-identity) ve [Web uygulaması kod bölümlerini değiştirme](#modify-the-app-to-access-your-key-vault) aşamasına geçebilirsiniz.

## <a name="create-a-net-core-app"></a>.NET Core uygulaması oluşturma
Bu adımda, yerel .NET Core projesini ayarlayacaksınız.

Makinenizde bir Terminal penceresinde adlı bir dizin oluşturun `akvwebapp` ve geçerli dizin yapın:

```bash
mkdir akvwebapp
cd akvwebapp
```

[DotNet New Web](/dotnet/core/tools/dotnet-new) komutunu kullanarak bir .NET Core uygulaması oluşturun:

```bash
dotnet new web
```

Uygulamayı Azure 'a dağıtırken nasıl bakacağınızı öğrenmek için uygulamayı yerel olarak çalıştırın:

```bash
dotnet run
```

Bir Web tarayıcısında, konumundaki uygulamaya gidin `http://localhost:5000` .

Uygulamanızdan "Merhaba Dünya!" sayfada görünen örnek uygulamadaki ileti.

Azure için Web uygulamaları oluşturma hakkında daha fazla bilgi için bkz. [Azure App Service ASP.NET Core Web uygulaması oluşturma](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Bu adımda, .NET Core uygulamanızı yerel Git kullanarak Azure App Service dağıtacaksınız. Uygulamaları oluşturma ve dağıtma hakkında daha fazla bilgi için bkz. [Azure 'da ASP.NET Core Web uygulaması oluşturma](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Yerel git dağıtımını yapılandırma

Terminal penceresinde, **CTRL + C** ' yi seçerek Web sunucusunu kapatın.  .NET Core projesi için bir git deposu başlatın:

```bash
git init --initial-branch=main
git add .
git commit -m "first commit"
```

Bir Azure Web uygulamasını bir *dağıtım kullanıcısı* kullanarak DAĞıTMAK için FTP ve yerel git ' i kullanabilirsiniz. Dağıtım kullanıcısını yapılandırdıktan sonra tüm Azure dağıtımlarınız için kullanabilirsiniz. Hesap düzeyinde dağıtım Kullanıcı adınız ve parolanız, Azure aboneliği kimlik bilgilerinizden farklı. 

Dağıtım kullanıcısını yapılandırmak için [az WebApp Deployment User set](/cli/azure/webapp/deployment/user?#az_webapp_deployment_user_set) komutunu çalıştırın. Bu yönergelere uygun bir Kullanıcı adı ve parola seçin: 

- Kullanıcı adı Azure içinde benzersiz olmalıdır. Yerel git gönderimleri için, at işareti simgesine (@) sahip olamaz. 
- Parola en az sekiz karakter uzunluğunda olmalı ve şu üç öğeden ikisini içermelidir: harfler, rakamlar ve semboller. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON çıktısı parolayı olarak gösterir `null` . Bir `'Conflict'. Details: 409` hata alırsanız, Kullanıcı adını değiştirin. `'Bad Request'. Details: 400` hatası alırsanız daha güçlü bir parola kullanın. 

Kullanıcı adınızı ve parolanızı, Web uygulamalarınızı dağıtmak için kullanabilmek üzere kaydedin.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarını dağıttığınız ve yönettiğiniz mantıksal bir kapsayıcıdır. [Az Group Create](/cli/azure/group?#az_group_create) komutunu kullanarak hem anahtar kasanızı hem de Web uygulamanızı içerecek bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

Azure CLı [az appservice plan Create](/cli/azure/appservice/plan) komutunu kullanarak bir [App Service planı](../../app-service/overview-hosting-plans.md) oluşturun. Aşağıdaki örnek `myAppServicePlan` , fiyatlandırma katmanında adlı bir App Service planı oluşturur `FREE` :

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service planı oluşturulduğunda Azure CLı, burada gördüklerinize benzer bilgiler görüntüler:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Daha fazla bilgi için bkz. [Azure'da App Service planı yönetme](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

App Service planında bir [Azure Web uygulaması](../../app-service/overview.md) oluşturun `myAppServicePlan` . 

> [!Important]
> Bir anahtar kasası gibi, bir Azure Web uygulamasının benzersiz bir adı olmalıdır. `<your-webapp-name>`Aşağıdaki örneklerde Web uygulamanızın adıyla değiştirin.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Web uygulaması oluşturulduğunda Azure CLı, burada gördüklerinize benzer bir çıktı gösterir:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Git Remote 'in URL 'SI, `deploymentLocalGitUrl` özelliğinde, biçiminde gösterilir `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Bu URL 'YI kaydedin. Buna daha sonra ihtiyacınız olacak.

Şimdi Web uygulamanızı daldan dağıtılacak şekilde yapılandırın `main` :

```azurecli-interactive
 az webapp config appsettings set -g MyResourceGroup -name "<your-webapp-name>"--settings deployment_branch=main
```

Aşağıdaki komutu kullanarak yeni uygulamanıza gidin. `<your-webapp-name>`Uygulamanızın adıyla değiştirin.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Yeni bir Azure Web uygulaması için varsayılan Web sayfasını görürsünüz.

### <a name="deploy-your-local-app"></a>Yerel uygulamanızı dağıtma

Yerel terminal penceresine dönüp yerel Git deponuza bir Azure uzak deposu ekleyin. Aşağıdaki komutta, ' yi, `<deploymentLocalGitUrl-from-create-step>` [Web uygulaması oluşturma](#create-a-web-app) bölümünde kaydettiğiniz GIT uzak öğesinin URL 'siyle değiştirin.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Uygulamanızı dağıtmak için Azure uzak uygulamasına göndermek üzere aşağıdaki komutu kullanın. Git kimlik bilgileri Yöneticisi kimlik bilgilerini sizden isterse, [Yerel git dağıtımını yapılandırma](#configure-the-local-git-deployment) bölümünde oluşturduğunuz kimlik bilgilerini kullanın.

```bash
git push azure main
```

Bu komutun çalıştırılması birkaç dakika sürebilir. Çalışma sırasında, burada gördüklerinize benzer bilgiler görüntüler:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Web tarayıcınızı kullanarak dağıtılan uygulamayı (veya yenilemeyi) (veya yenilemeyin) gidin:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Uygulamanızdan "Merhaba Dünya!" daha önce ziyaret ettiğinizde gördüğünüz ileti `http://localhost:5000` .

Git kullanarak Web uygulaması dağıtma hakkında daha fazla bilgi için bkz. [Yerel git dağıtımı Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Web uygulamasını Key Vault bağlanacak şekilde yapılandırma

Bu bölümde, Key Vault bir gizli dizi almak için Key Vault ve uygulama kodunuzu güncelleştirmek üzere Web erişimini yapılandıracaksınız.

### <a name="create-and-assign-a-managed-identity"></a>Yönetilen kimlik oluşturma ve atama

Bu öğreticide, Key Vault kimlik doğrulaması için [yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md) kullanacağız. Yönetilen kimlik, uygulama kimlik bilgilerini otomatik olarak yönetir.

Azure CLı 'da, uygulamanın kimliğini oluşturmak için [az WebApp-Identity Assign](/cli/azure/webapp/identity?#az_webapp_identity_assign) komutunu çalıştırın:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Komut bu JSON kod parçacığını döndürür:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Web uygulamanıza, anahtar kasanıza yönelik **Get** ve **Listeleme** işlemlerine Izin vermek için, `principalId` Azure CLI [az keykasası Set-Policy](/cli/azure/keyvault?#az_keyvault_set_policy) komutunu geçirin:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Ayrıca, [Azure Portal](./assign-access-policy-portal.md) veya [PowerShell](./assign-access-policy-powershell.md)kullanarak erişim ilkeleri de atayabilirsiniz.

### <a name="modify-the-app-to-access-your-key-vault"></a>Anahtar kasanıza erişmek için uygulamayı değiştirin

Bu öğreticide, tanıtım amacıyla [Azure Key Vault gizli istemci kitaplığı](/dotnet/api/overview/azure/security.keyvault.secrets-readme) kullanacaksınız. [Azure Key Vault sertifikası istemci kitaplığı](/dotnet/api/overview/azure/security.keyvault.certificates-readme)'nı veya [Azure Key Vault anahtar istemci kitaplığı](/dotnet/api/overview/azure/security.keyvault.keys-readme)' nı da kullanabilirsiniz.

#### <a name="install-the-packages"></a>Paketleri yükler

Terminal penceresinde, .NET ve Azure Identity istemci kitaplığı paketleri için Azure Key Vault gizli istemci Kitaplığı ' nı yükledikten sonra:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Kodu güncelleştirme

The. cs dosyasını akvwebapp projenizde bulun ve açın. 

Bu satırları üstbilgiye ekleyin:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Çağrıdan önce aşağıdaki satırları ekleyin `app.UseEndpoints` , URI 'yi anahtar kasasının yansıtacak şekilde güncelleyerek `vaultUri` . Bu kod, kimlik doğrulaması için yönetilen kimliğin bir belirtecini kullanan Key Vault kimlik doğrulaması yapmak için  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) kullanır. Key Vault kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](./developers-guide.md#authenticate-to-key-vault-in-code). Kod, Key Vault kısıtlanmakta olması durumunda yeniden denemeler için üstel geri alma kullanır. Key Vault işlem limitleri hakkında daha fazla bilgi için bkz. [Azure Key Vault azaltma Kılavuzu](./overview-throttling.md).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Satırı aşağıdaki `await context.Response.WriteAsync("Hello World!");` satıra benzeymek üzere güncelleştirin:

```csharp
await context.Response.WriteAsync(secretValue);
```

Sonraki adıma geçmeden önce değişikliklerinizi kaydettiğinizden emin olun.

#### <a name="redeploy-your-web-app"></a>Web uygulamanızı yeniden dağıtma

Artık kodunuzu güncelleştirmiş olduğunuza göre, şu git komutlarını kullanarak Azure 'a yeniden dağıtabilirsiniz:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Tamamlanmış Web uygulamanıza gidin

```bash
http://<your-webapp-name>.azurewebsites.net
```

"Merhaba Dünya!" öğesini gördüğünüz, artık gizli anahtar değerinin görüntülendiğini görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [.NET 'teki bir sanal makineye dağıtılan uygulamalarla Azure Key Vault kullanma](./tutorial-net-virtual-machine.md)
- [Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md) hakkında daha fazla bilgi edinin
- [Geliştirici kılavuzunu](./developers-guide.md) görüntüleme
- [Anahtar kasasına erişimin güvenliğini sağlama](./security-features.md)