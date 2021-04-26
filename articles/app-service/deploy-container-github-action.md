---
title: GitHub eylemlerinden özel kapsayıcı CI/CD 'si
description: Özel Linux kapsayıcınızı bir CI/CD işlem hattından App Service dağıtmak için GitHub eylemlerini nasıl kullanacağınızı öğrenin.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789438"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub eylemlerini kullanarak App Service özel kapsayıcı dağıtma

[GitHub eylemleri](https://docs.github.com/en/actions) , otomatik yazılım geliştirme iş akışı oluşturma esnekliği sağlar. [Azure Web dağıtımı eylemiyle](https://github.com/Azure/webapps-deploy), GitHub eylemlerini kullanarak [App Service](overview.md) için özel kapsayıcılar dağıtmak üzere iş akışınızı otomatik hale getirebilirsiniz.

Bir iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışındaki çeşitli adımları ve parametreleri içerir.

Azure App Service kapsayıcı iş akışı için, dosyanın üç bölümü vardır:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu alın veya profili yayımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Derleme** | 1. ortamı oluşturun. <br /> 2. kapsayıcı görüntüsünü oluşturun. |
|**Dağıtma** | 1. kapsayıcı görüntüsünü dağıtın. |

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub hesabı. Bir hesabınız yoksa [ücretsiz](https://github.com/join)kaydolun. Azure App Service dağıtmak için bir GitHub deposunda kodunuz olması gerekir. 
- Kapsayıcılar için çalışan bir kapsayıcı kayıt defteri ve Azure App Service uygulaması. Bu örnek Azure Container Registry kullanır. Kapsayıcılar için Azure App Service için tam dağıtımı tamamladığınızdan emin olun. Normal web uygulamalarından farklı olarak, kapsayıcılar için Web Apps varsayılan bir giriş sayfasına sahip değildir. Kapsayıcıyı çalışan bir örneğe sahip olacak şekilde yayımlayın.
    - [Docker kullanarak Kapsayıcılı Node.js bir uygulama oluşturmayı, kapsayıcı görüntüsünü bir kayıt defterine göndermeyi ve sonra da görüntüyü dağıtmayı öğrenin Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

GitHub eylemleri için Azure App Services ile kimlik doğrulamak için önerilen yol, bir yayımlama profiliyle birlikte bulunur. Hizmet sorumlusu ile de kimlik doğrulaması yapabilirsiniz ancak işlem daha fazla adım gerektirir. 

Yayımlama profili kimlik bilgilerinizi veya hizmet sorumlunuzu Azure ile kimlik doğrulamak için [GitHub gizli anahtarı](https://docs.github.com/en/actions/reference/encrypted-secrets) olarak kaydedin. Gizli anahtar, iş akışınız dahilinde. 

# <a name="publish-profile"></a>[Profili Yayımla](#tab/publish-profile)

Yayımlama profili, uygulama düzeyinde bir kimlik bilgileridir. Yayımlama profilinizi GitHub gizli anahtarı olarak ayarlayın. 

1. Azure portal App Service 'e gidin. 

1. **Genel bakış** sayfasında, **Yayımlama profili al**' ı seçin.

    > [!NOTE]
    > 2020 Ekim itibariyle Linux Web Apps, `WEBSITE_WEBDEPLOY_USE_SCM` `true` **dosyanın indirilmeden önce** uygulama ayarının ayarlanmış olması gerekir. Bu gereksinim gelecekte kaldırılacak. Ortak Web uygulaması ayarlarını yapılandırma hakkında bilgi edinmek için [Azure portal App Service uygulama yapılandırma](./configure-common.md)konusuna bakın.  

1. İndirdiğiniz dosyayı kaydedin. Dosyanın içeriğini bir GitHub parolası oluşturmak için kullanacaksınız.

# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/service-principal)

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Örnekte, yer tutucuları abonelik KIMLIĞINIZ, kaynak grubu adı ve uygulama adınızla değiştirin. Çıktı, App Service uygulamanıza erişim sağlayan rol atama kimlik bilgilerine sahip bir JSON nesnesidir. Bu JSON nesnesini daha sonra kopyalayın.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> En az erişim sağlamak her zaman iyi bir uygulamadır. Önceki örnekteki kapsam, kaynak grubunun tamamı değil, belirli App Service uygulamasıyla sınırlandırılmıştır.

---
## <a name="configure-the-github-secret-for-authentication"></a>Kimlik doğrulaması için GitHub gizliliğini yapılandırma

# <a name="publish-profile"></a>[Profili Yayımla](#tab/publish-profile)

[GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

[Uygulama düzeyi kimlik bilgilerini](#generate-deployment-credentials)kullanmak için, indirilen yayımlama profili dosyasının içeriğini gizli dizinin değer alanına yapıştırın. Parolayı adlandırın `AZURE_WEBAPP_PUBLISH_PROFILE` .

GitHub iş akışınızı yapılandırırken, `AZURE_WEBAPP_PUBLISH_PROFILE` Azure Web uygulaması dağıtma eyleminde öğesini kullanırsınız. Örnek:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/service-principal)

[GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

[Kullanıcı düzeyi kimlik bilgilerini](#generate-deployment-credentials)kullanmak IÇIN Azure CLI KOMUTUNDAN tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi adını gibi verin `AZURE_CREDENTIALS` .

Daha sonra iş akışı dosyasını yapılandırdığınızda, `creds` Azure oturum açma eyleminin girişi için gizli anahtarı kullanırsınız. Örnek:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Kayıt defteriniz için GitHub gizli dizilerini yapılandırma

Docker oturum açma eylemiyle kullanılacak gizli dizileri tanımlayın. Bu belgedeki örnek, kapsayıcı kayıt defteri için Azure Container Registry kullanır. 

1. Azure portal veya Docker 'daki kapsayıcınıza gidin ve Kullanıcı adını ve parolayı kopyalayın. Azure Container Registry Kullanıcı adı ve parolayı,   >  kayıt defteriniz için ayarlar **erişim tuşları** altında Azure Portal bulabilirsiniz. 

2. Adlı kayıt defteri Kullanıcı adı için yeni bir gizli dizi tanımlayın `REGISTRY_USERNAME` . 

3. Adlı kayıt defteri parolası için yeni bir gizli dizi tanımlayın `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Kapsayıcı görüntüsünü oluşturma

Aşağıdaki örnek, Node.JS bir Docker görüntüsü oluşturan iş akışının bir parçasını gösterir. Bir özel kapsayıcı kayıt defterinde oturum açmak için [Docker oturum açma](https://github.com/azure/docker-login) kullanın. Bu örnek Azure Container Registry kullanır, ancak aynı eylem diğer kayıt defterleri için de geçerlidir. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Aynı anda birden çok kapsayıcı kayıt defterlerine oturum açmak için [Docker oturum açma](https://github.com/azure/docker-login) bilgilerini de kullanabilirsiniz. Bu örnek, docker.io ile kimlik doğrulaması için iki yeni GitHub parolası içerir. Örnek, kayıt defterinin kök düzeyinde bir Dockerfile olduğunu varsayar. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>App Service kapsayıcısına dağıtma

Görüntünüzü App Service özel bir kapsayıcıya dağıtmak için `azure/webapps-deploy@v2` eylemini kullanın. Bu eylem yedi parametreye sahiptir:

| **Parametre**  | **Açıklama**  |
|---------|---------|
| **uygulama adı** | Istenir App Service uygulamasının adı | 
| **Yayımlama profili** | Seçim Web Apps (Windows ve Linux) ve Web uygulaması kapsayıcıları (Linux) için geçerlidir. Çoklu kapsayıcı senaryosu desteklenmiyor. \*Web dağıtımı gizli dizileri ile profil (. publishsettings) dosya içeriğini yayımlama | 
| **yuva adı** | Seçim Üretim yuvası dışında mevcut bir yuva girin |
| **leyebilir** | Seçim Yalnızca Web uygulaması için geçerlidir: paket veya klasör yolu. \*. zip, \* . war, \* . jar veya dağıtılacak bir klasör |
| **yansımasını** | Istenir Yalnızca Web uygulaması kapsayıcıları için geçerlidir: tamamen nitelenmiş kapsayıcı görüntüsü adını belirtin. Örneğin, ' myregistry.azurecr.io/nginx:latest ' veya ' Python: 3.7.2-alçam/'. Çok kapsayıcılı bir uygulama için birden çok kapsayıcı görüntüsü adı sağlanmış olabilir (çok satırlı ayrılmış) |
| **yapılandırma-dosya** | Seçim Yalnızca Web uygulaması kapsayıcıları için geçerlidir: Docker-Compose dosyanın yolu. Tam olarak nitelenmiş bir yol olmalıdır veya varsayılan çalışma dizinine göre değişir. Çok Kapsayıcılı uygulamalar için gereklidir. |
| **başlangıç-komut** | Seçim Başlangıç komutunu girin. For ex. DotNet Run veya DotNet filename.dll |

# <a name="publish-profile"></a>[Profili Yayımla](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki farklı depolarda gruplanmış eylem listemizi, her biri, CI/CD için GitHub kullanmanıza ve uygulamalarınızı Azure 'a dağıtmanıza yardımcı olacak belgeler ve örnekler içeren bir şekilde bulabilirsiniz.

- [Azure 'a dağıtılacak eylemler iş akışları](https://github.com/Azure/actions-workflow-samples)

- [Azure oturum açma](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker oturum açma/kapatma](https://github.com/Azure/docker-login)

- [İş akışlarını tetikleyen olaylar](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s dağıtımı](https://github.com/Azure/k8s-deploy)

- [Başlangıç Iş akışları](https://github.com/actions/starter-workflows)