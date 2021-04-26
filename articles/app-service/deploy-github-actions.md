---
title: GitHub eylemleri ile CI/CD 'yi yapılandırma
description: Bir CI/CD işlem hattından GitHub eylemleriyle Azure App Service kodunuzu dağıtmayı öğrenin. Yapı görevlerini özelleştirin ve karmaşık dağıtımları yürütün.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: aa4475ccedfb19ece540337f493bcc5ed64af035
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832473"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions kullanarak App Service'e dağıtma

İş akışınızı otomatik hale getirmek ve GitHub 'dan [Azure App Service](overview.md) dağıtmak Için [GitHub eylemleri](https://docs.github.com/en/actions/learn-github-actions) ile çalışmaya başlayın. 

## <a name="prerequisites"></a>Önkoşullar 

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub hesabı. Bir hesabınız yoksa [ücretsiz](https://github.com/join)kaydolun.  
- Çalışan bir Azure App Service uygulaması. 
    - .NET: [Azure 'da ASP.NET Core Web uygulaması oluşturma](quickstart-dotnetcore.md)
    - ASP.NET: [Azure 'da ASP.NET Framework Web uygulaması oluşturma](quickstart-dotnet-framework.md)
    - JavaScript: [Azure App Service Node.js Web uygulaması oluşturma](quickstart-nodejs.md)  
    - Java: [Azure App Service bir Java uygulaması oluşturma](quickstart-java.md)
    - Python: [Azure App Service bir Python uygulaması oluşturma](quickstart-python.md)

## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

Bir iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Dosyada üç bölüm bulunur:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu veya yayımlama profili tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Derleme** | 1. ortamı ayarlayın. <br /> 2. Web uygulamasını oluşturun. |
|**Dağıtma** | 1. Web uygulamasını dağıtın. |

## <a name="use-the-deployment-center"></a>Dağıtım merkezini kullanma

App Service dağıtım merkezini kullanarak GitHub eylemlerini hızlıca kullanmaya başlayın. Bu işlem, uygulama yığınınıza göre otomatik olarak bir iş akışı dosyası oluşturur ve bunu doğru dizindeki GitHub deponuza işleyin.

1. Azure portal WebApp 'e gidin
1. Sol tarafta **Dağıtım Merkezi** ' ne tıklayın.
1. **Sürekli dağıtım (CI/CD)** altında **GitHub** ' ı seçin.
1. Sonra, **GitHub eylemleri** ' ni seçin.
1. GitHub deponuzu, dalı ve uygulama yığınınızı seçmek için açılan listeleri kullanın
    - Seçili Dal korunuyorsa iş akışı dosyasını eklemeye devam edebilirsiniz. Devam etmeden önce dal korumalarını gözden geçirdiğinizden emin olun.
1. Son ekranda, seçimlerinizi gözden geçirebilir ve depoya uygulanacak iş akışı dosyasını önizleyebilirsiniz. Seçimler doğruysa **son** ' a tıklayın.

Bu işlem, iş akışı dosyasını depoya teslim eder. Uygulamanızı derlemek ve dağıtmak için iş akışı hemen başlayacaktır.

## <a name="set-up-a-workflow-manually"></a>Bir iş akışını el ile ayarlama

Ayrıca, dağıtım merkezini kullanmadan bir iş akışı dağıtabilirsiniz. Bunu yapmak için öncelikle dağıtım kimlik bilgileri oluşturmanız gerekir. 

## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

GitHub eylemleri için Azure App Services ile kimlik doğrulamak için önerilen yol, bir yayımlama profiliyle birlikte bulunur. Hizmet sorumlusu ile de kimlik doğrulaması yapabilirsiniz ancak işlem daha fazla adım gerektirir. 

Yayımlama profili kimlik bilgilerinizi veya hizmet sorumlunuzu Azure ile kimlik doğrulamak için [GitHub gizli anahtarı](https://docs.github.com/en/actions/reference/encrypted-secrets) olarak kaydedin. Gizli anahtar, iş akışınız dahilinde. 

# <a name="publish-profile"></a>[Profili Yayımla](#tab/applevel)

Yayımlama profili, uygulama düzeyinde bir kimlik bilgileridir. Yayımlama profilinizi GitHub gizli anahtarı olarak ayarlayın. 

1. Azure portal App Service 'e gidin. 

1. **Genel bakış** sayfasında, **Yayımlama profili al**' ı seçin.

1. İndirdiğiniz dosyayı kaydedin. Dosyanın içeriğini bir GitHub parolası oluşturmak için kullanacaksınız.

> [!NOTE]
> 2020 Ekim itibariyle Linux Web Apps, `WEBSITE_WEBDEPLOY_USE_SCM` `true` **Yayımlama profilini indirmeden önce** uygulama ayarının olarak ayarlanmış olması gerekecektir. Bu gereksinim gelecekte kaldırılacak.

# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/userlevel)

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Yukarıdaki örnekte yer tutucuları abonelik KIMLIĞINIZ, kaynak grubu adı ve uygulama adınızla değiştirin. Çıktı, aşağıda gösterilene benzer App Service uygulamanıza erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Bu JSON nesnesini daha sonra kopyalayın.

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

## <a name="configure-the-github-secret"></a>GitHub gizliliğini yapılandırma


# <a name="publish-profile"></a>[Profili Yayımla](#tab/applevel)

[GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

[Uygulama düzeyi kimlik bilgilerini](#generate-deployment-credentials)kullanmak için, indirilen yayımlama profili dosyasının içeriğini gizli dizinin değer alanına yapıştırın. Parolayı adlandırın `AZURE_WEBAPP_PUBLISH_PROFILE` .

GitHub iş akışınızı yapılandırırken, `AZURE_WEBAPP_PUBLISH_PROFILE` Azure Web uygulaması dağıtma eyleminde öğesini kullanırsınız. Örnek:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/userlevel)

[GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

[Kullanıcı düzeyi kimlik bilgilerini](#generate-deployment-credentials)kullanmak IÇIN Azure CLI KOMUTUNDAN tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_CREDENTIALS` .

Daha sonra iş akışı dosyasını yapılandırdığınızda, `creds` Azure oturum açma eyleminin girişi için gizli anahtarı kullanırsınız. Örnek:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Ortamı ayarlama

Ortamı ayarlamak, kurulum eylemlerinden biri kullanılarak yapılabilir.

|**Dil**  |**Kurulum eylemi**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Aşağıdaki örneklerde, desteklenen farklı diller için ortamın nasıl ayarlanacağı gösterilmektedir:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Web uygulamasını oluşturma

Bir Web uygulaması oluşturma ve dile göre Azure App Service için dağıtma işlemi. 

Aşağıdaki örneklerde, farklı desteklenen dillerde Web uygulamasını oluşturan iş akışının bölümü gösterilmektedir.

Tüm diller için, Web uygulaması kök dizinini ile ayarlayabilirsiniz `working-directory` . 

**.NET**

Ortam değişkeni, `AZURE_WEBAPP_PACKAGE_PATH` Web uygulaması projenizin yolunu ayarlar. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

NuGet bağımlılıklarını geri yükleyebilir ve ile MSBuild 'i çalıştırabilirsiniz `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.2

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Node.js için, `working-directory` içinde NPM dizinini ayarlayabilir veya değiştirebilirsiniz `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>App Service’e dağıtma

Kodunuzu bir App Service uygulamasına dağıtmak için `azure/webapps-deploy@v2` eylemini kullanın. Bu eylemin dört parametresi vardır:

| **Parametre**  | **Açıklama**  |
|---------|---------|
| **uygulama adı** | Istenir App Service uygulamasının adı | 
| **Yayımlama profili** | Seçim Web Dağıtımı gizli dizileri ile profil dosyası içeriğini yayımlama |
| **leyebilir** | Seçim Paket veya klasörün yolu. Yol, *. zip, *. war, *. jar veya dağıtılacak bir klasör içerebilir |
| **yuva adı** | Seçim Üretim [yuvası](deploy-staging-slots.md) dışında mevcut bir yuva girin |


# <a name="publish-profile"></a>[Profili Yayımla](#tab/applevel)

### <a name="net-core"></a>.NET Core

Azure yayımlama profili kullanarak bir .NET Core uygulamasını derleyin ve Azure 'a dağıtın. `publish-profile`Giriş, `AZURE_WEBAPP_PUBLISH_PROFILE` daha önce oluşturduğunuz gizliliğe başvurur.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

NuGet ve kimlik doğrulaması için kullanılan bir ASP.NET MVC uygulaması derleyin ve dağıtın `publish-profile` . 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Azure yayımlama profili kullanarak bir Java Spring uygulamasını derleyin ve Azure 'a dağıtın. `publish-profile`Giriş, `AZURE_WEBAPP_PUBLISH_PROFILE` daha önce oluşturduğunuz gizliliğe başvurur.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Yerine bir olarak dağıtmak için `war` `jar` , `package` değerini değiştirin. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Uygulamanın yayımlama profilini kullanarak Azure 'da bir Node.js uygulaması oluşturun ve dağıtın. `publish-profile`Giriş, `AZURE_WEBAPP_PUBLISH_PROFILE` daha önce oluşturduğunuz gizliliğe başvurur.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Uygulamanın yayımlama profilini kullanarak bir Python uygulamasını derleyin ve Azure 'a dağıtın. `publish-profile`Girişin `AZURE_WEBAPP_PUBLISH_PROFILE` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Hizmet sorumlusu](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Azure hizmet sorumlusu kullanarak bir .NET Core uygulamasını derleyin ve Azure 'a dağıtın. `creds`Girişin `AZURE_CREDENTIALS` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Azure hizmet sorumlusu kullanarak bir ASP.NET MVC uygulamasını derleyin ve Azure 'a dağıtın. `creds`Girişin `AZURE_CREDENTIALS` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Azure hizmet sorumlusu kullanarak bir Java Spring uygulamasını derleyin ve Azure 'a dağıtın. `creds`Girişin `AZURE_CREDENTIALS` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Azure hizmet sorumlusu kullanarak Azure 'da bir Node.js uygulaması oluşturun ve dağıtın. `creds`Girişin `AZURE_CREDENTIALS` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Azure hizmet sorumlusu kullanarak bir Python uygulamasını derleyin ve Azure 'a dağıtın. `creds`Girişin `AZURE_CREDENTIALS` daha önce oluşturduğunuz gizli dizi ile nasıl başvurdığına göz önünde unutmayın.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki farklı depolarda gruplanmış eylem listemizi, her biri, CI/CD için GitHub kullanmanıza ve uygulamalarınızı Azure 'a dağıtmanıza yardımcı olacak belgeler ve örnekler içeren bir şekilde bulabilirsiniz.

- [Azure 'a dağıtılacak eylemler iş akışları](https://github.com/Azure/actions-workflow-samples)

- [Azure oturum açma](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Kapsayıcılar için Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker oturum açma/kapatma](https://github.com/Azure/docker-login)

- [İş akışlarını tetikleyen olaylar](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s dağıtımı](https://github.com/Azure/k8s-deploy)

- [Başlangıç Iş akışları](https://github.com/actions/starter-workflows)
