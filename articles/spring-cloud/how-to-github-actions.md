---
title: GitHub eylemleri ile Azure yay bulut CI/CD
description: GitHub eylemleriyle Azure Spring bulutu için CI/CD iş akışını oluşturma
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791364"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>GitHub eylemleri ile Azure yay bulut CI/CD

GitHub eylemleri otomatik yazılım geliştirme yaşam döngüsü iş akışını destekler. Azure Spring Cloud için GitHub eylemleri sayesinde deponuzda oluşturmak, test etmek, paketlemek, yayınlamak ve Azure 'a dağıtmak için iş akışları oluşturabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar
Bu örnek, [Azure CLI](/cli/azure/install-azure-cli)gerektirir.

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub deposunu ayarlama ve kimlik doğrulama
Azure oturum açma eylemini yetkilendirmek için bir Azure hizmet sorumlusu kimlik bilgisine ihtiyacınız vardır. Azure kimlik bilgilerini almak için yerel makinenizde aşağıdaki komutları yürütün:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Belirli bir kaynak grubuna erişmek için kapsamı azaltabilirsiniz:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Komut bir JSON nesnesinin çıktısını almalıdır:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Bu örnek, [GitHub 'daki steeltoe örneğini](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)kullanır.  Depoyu çatalla, çatal için GitHub deposu sayfasını açın ve **Ayarlar** sekmesini seçin. **Gizlilikler** menüsünü açın ve **Yeni Gizlilik**' i seçin:

 ![Yeni gizli dizi Ekle](./media/github-actions/actions1.png)

Gizli adını `AZURE_CREDENTIALS` ve değerini, *GitHub deponuzu ayarlama ve kimlik doğrulama* altında bulduğunuz JSON dizesine ayarlayın.

 ![Gizli verileri ayarlama](./media/github-actions/actions2.png)

GitHub [eylemlerinde Key Vault Ile Azure Spring kimlik doğrulama konusunda](./spring-cloud-github-actions-key-vault.md)açıklandığı gibi, Azure oturum açma kimlik bilgilerini GitHub eylemleriyle Key Vault de alabilirsiniz.

## <a name="provision-service-instance"></a>Hizmet örneği sağlama
Azure Spring Cloud Service örneğinizi sağlamak için Azure CLı 'yı kullanarak aşağıdaki komutları çalıştırın.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>İş akışını oluşturma
İş akışı aşağıdaki seçenekler kullanılarak tanımlanır.

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLı ile dağıtıma hazırlanma

Komut `az spring-cloud app create` Şu anda ıdempotent değil. Bir kez çalıştırdıktan sonra aynı komutu yeniden çalıştırırsanız bir hata alırsınız. Bu iş akışını mevcut Azure Spring Cloud Apps ve örnekleri üzerinde öneririz.

Hazırlama için aşağıdaki Azure CLı komutlarını kullanın:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Doğrudan Azure CLı ile dağıtma

`.github/workflows/main.yml`Dosyayı depoda aşağıdaki içerikle oluşturun. `<your resource group name>`Ve `<your service name>` değerlerini doğru değerlerle değiştirin.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub deposunu ayarlama ve kimlik doğrulama
Azure oturum açma eylemini yetkilendirmek için bir Azure hizmet sorumlusu kimlik bilgisine ihtiyacınız vardır. Azure kimlik bilgilerini almak için yerel makinenizde aşağıdaki komutları yürütün:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Belirli bir kaynak grubuna erişmek için kapsamı azaltabilirsiniz:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Komut bir JSON nesnesinin çıktısını almalıdır:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Bu örnek, GitHub 'da [Pımetrimetrik](https://github.com/Azure-Samples/piggymetrics) örneğini kullanır.  Örnek çatalla, GitHub deposu sayfasını açın ve **Ayarlar** sekmesi ' ne tıklayın. **Gizli** dizileri menüsünü açın ve **yeni gizli dizi Ekle**' ye tıklayın:

 ![Yeni gizli dizi Ekle](./media/github-actions/actions1.png)

Gizli adını `AZURE_CREDENTIALS` ve değerini, *GitHub deponuzu ayarlama ve kimlik doğrulama* altında bulduğunuz JSON dizesine ayarlayın.

 ![Gizli verileri ayarlama](./media/github-actions/actions2.png)

GitHub [eylemlerinde Key Vault Ile Azure Spring kimlik doğrulama konusunda](./spring-cloud-github-actions-key-vault.md)açıklandığı gibi, Azure oturum açma kimlik bilgilerini GitHub eylemleriyle Key Vault de alabilirsiniz.

## <a name="provision-service-instance"></a>Hizmet örneği sağlama
Azure Spring Cloud Service örneğinizi sağlamak için Azure CLı 'yı kullanarak aşağıdaki komutları çalıştırın.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>İş akışını oluşturma
İş akışı aşağıdaki seçenekler kullanılarak tanımlanır.

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLı ile dağıtıma hazırlanma
Komut `az spring-cloud app create` Şu anda ıdempotent değil.  Bu iş akışını mevcut Azure Spring Cloud Apps ve örnekleri üzerinde öneririz.

Hazırlama için aşağıdaki Azure CLı komutlarını kullanın:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Doğrudan Azure CLı ile dağıtma
`.github/workflow/main.yml`Dosyayı depoda oluşturun:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Azure CLı eylemiyle Dağıtma eylemi
Az `run` komut en son Azure CLI sürümünü kullanacaktır. Büyük değişiklikler varsa, Azure/CLı ile belirli bir Azure CLı sürümünü de kullanabilirsiniz `action` . 

> [!Note] 
> Bu komut yeni bir kapsayıcıda çalışacak, bu nedenle `env` çalışmayacak ve çapraz işlem dosya erişimi ek kısıtlamalara sahip olabilir.

Depoda. GitHub/Workflow/Main. yml dosyasını oluşturun:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Maven eklentisi ile dağıtma
Diğer bir seçenek de Jar 'yi dağıtmak ve uygulama ayarlarını güncelleştirmek için [Maven eklentisini](./spring-cloud-quickstart.md) kullanmaktır. Komut `mvn azure-spring-cloud:deploy` ıdempotent ' dir ve gerekirse otomatik olarak uygulamalar oluşturur. İlgili uygulamaları önceden oluşturmanız gerekmez.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>İş akışını çalıştırma

GitHub 'a gönderim yapıldıktan sonra GitHub **eylemleri** otomatik olarak etkinleştirilmelidir `.github/workflow/main.yml` . Yeni bir işleme gönderdiğinizde bu eylem tetiklenir. Bu dosyayı tarayıcıda oluşturursanız, eyleminiz zaten çalıştırılmış olmalıdır.

Eylemin etkinleştirildiğini doğrulamak için GitHub Deposu sayfasında **Eylemler** sekmesi ' ne tıklayın:

![Etkin doğrulama eylemi](./media/github-actions/actions3.png)

Eylemleriniz hatada çalışıyorsa, örneğin, Azure kimlik bilgisini ayarlamadıysanız, hatayı düzelttikten sonra denetimleri yeniden çalıştırabilirsiniz. GitHub Deposu sayfasında, **Eylemler**' e tıklayın, belirli bir iş akışı görevini seçin ve **sonra denetimleri yeniden çalıştır düğmesine tıklayarak** denetimleri yeniden çalıştırın:

![Denetimleri yeniden çalıştır](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Spring Cloud GitHub eylemleri için Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Hizmet sorumlularını Azure Active Directory](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [Azure için GitHub Actions](https://github.com/Azure/actions/)
