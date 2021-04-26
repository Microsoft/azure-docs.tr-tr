---
title: 'Öğretici: Azure Işlevlerini çağırmak için yönetilen kimlik'
description: Azure Spring Cloud uygulamasından Azure İşlevleri çağırmak için yönetilen kimlik kullanma
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792516"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Öğretici: Azure yay bulut uygulamasından Azure Işlevleri çağırmak için yönetilen bir kimlik kullanma

Bu makalede, bir Azure yay bulutu uygulaması için yönetilen kimlik oluşturma ve http tarafından tetiklenen Işlevleri çağırmak için kullanma Işlemleri gösterilmektedir.

Hem Azure Işlevleri hem de uygulama hizmetleri Azure Active Directory (Azure AD) kimlik doğrulaması için yerleşik desteğe sahiptir. Bu yerleşik kimlik doğrulama özelliğinden yararlanarak, Azure Spring Cloud için Yönetilen kimlikler sayesinde modern OAuth semantiğini kullanarak yeniden tasarlanan Hizmetleri çağırabiliriz. Bu yöntem, kodda parolaların depolanmasını gerektirmez ve dış kaynaklara erişimi denetlemek için daha ayrıntılı denetimler sağlar. 


## <a name="prerequisites"></a>Önkoşullar

* [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)
* [Azure CLı sürüm 2.0.67 veya üstünü yükler](/cli/azure/install-azure-cli)
* [Maven 3,0 veya üstünü yükler](https://maven.apache.org/download.cgi)
* [Azure Functions Core Tools sürüm 3.0.2009 veya üstünü yükler](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [Az Group Create](/cli/azure/group#az_group_create)komutunu kullanarak hem uygulama hem de yay bulutu içeren bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>İşlev Uygulaması oluşturma
Bir Işlev uygulaması oluşturmak için önce bir yedekleme depolama hesabı oluşturmanız gerekir, [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create)komutunu kullanın.

> [!Important]
> Her Işlev uygulaması ve depolama hesabı benzersiz bir ada sahip olmalıdır. <-functionapp-Name> Işlevini Işlev uygulamanızın adıyla değiştirin ve aşağıdaki örneklerde depolama hesabınızın adı ile-storageaccount-Name> <.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Depolama hesabı oluşturulduktan sonra, Işlev uygulaması oluşturabilirsiniz.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Döndürülen **ana bilgisayar adlarını**, "https://<-functionapp-Name>. azurewebsites.net" biçiminde olacak şekilde bir yere unutmayın. Bu, aşağıdaki adımda kullanılacaktır.


## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulamasını etkinleştir

[Azure Portal](https://portal.azure.com) yeni oluşturulan işlev uygulamasına erişin ve Ayarlar menüsünden "kimlik doğrulaması/yetkilendirme" seçeneğini belirleyin. App Service kimlik doğrulamasını etkinleştirin ve "istek kimlik doğrulaması olmadığında gerçekleştirilecek eylem" i "Azure Active Directory ile oturum aç" olarak ayarlayın. Bu ayar, tüm kimliği doğrulanmamış isteklerin reddedildiğini (401 yanıt) güvence altına alınır.

![Varsayılan sağlayıcı olarak Azure Active Directory gösteren kimlik doğrulama ayarları](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Kimlik doğrulama sağlayıcıları altında, uygulama kaydını yapılandırmak için Azure Active Directory ' yi seçin. Hızlı yönetim modunun seçilmesi, Azure AD kiracınızda doğru yapılandırmayla otomatik olarak bir uygulama kaydı oluşturacaktır.

![Azure Active Directory sağlayıcı hızlı yönetim moduna ayarlanmış](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Ayarları kaydettikten sonra, işlev uygulaması yeniden başlatılır ve sonraki tüm isteklerin Azure AD aracılığıyla oturum açması istenir. Kimliği doğrulanmamış isteklerin Şu anda reddedildiğini test edebilirsiniz (yukarıdaki adımdaki **ana bilgisayar adları** çıktısında döndürülür). Kuruluşunuzun Azure AD oturum açma ekranına yönlendirilmelisiniz.


## <a name="create-an-http-triggered-function"></a>Http ile tetiklenen bir Işlev oluşturma

Boş bir yerel dizinde, yeni bir işlev uygulaması oluşturun ve http ile tetiklenen bir işlev ekleyin.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Varsayılan olarak Işlevler, HTTP uç noktalarını güvenli hale getirmek için anahtar tabanlı kimlik doğrulaması kullanır. Işlevlerine erişim sağlamak için Azure AD kimlik doğrulamasını etkinleştiriyoruz, bu [işlev kimlik doğrulama düzeyini anonim olarak ayarlamak](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)istiyoruz.

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Uygulama artık önceki adımda oluşturulan [işlev uygulaması](#create-a-function-app) örneğine yayımlanabilir.

```console
func azure functionapp publish <your-functionapp-name>
```

Yayımla komutunun çıktısı, yeni oluşturulan işlevinizin URL 'sini listelemelidir.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Azure yay bulut hizmeti ve uygulaması oluşturma
Yay-bulut uzantısını yükledikten sonra, Azure CLı komutu ile bir Azure yay bulutu örneği oluşturun `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Aşağıdaki örnek, `msiapp` parametresi tarafından istenen şekilde sistem tarafından atanan yönetilen kimlik adlı bir uygulama oluşturur `--assign-identity` .

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Işlevi çağırmak için örnek yay önyükleme uygulaması oluşturma

Bu örnek, önce [MSI uç noktasından](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) bir erişim belirteci Isteyerek ve işlev http isteğinin kimliğini doğrulamak için bu belirteci kullanarak http tarafından tetiklenen işlevi çağıracaktır.

1. Örnek projeyi kopyalayın. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Uygulama özelliklerinde işlev URI 'nizi ve tetikleyici adını belirtin. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Azure yay bulut uygulamaları için yönetilen kimlik ' i kullanmak için, *src/Main/Resources/Application. Properties* öğesine aşağıdaki içerikle özellikler ekleyin.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Örnek uygulamanızı paketleyin. 

    ```console
    mvn clean package
    ```

4. Şimdi Azure CLı komutuyla uygulamayı Azure 'a dağıtın  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Uygulamanızı test etmek için genel uç noktaya veya test uç noktasına erişin. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Yanıt gövdesinde aşağıdaki iletiyi görürsünüz.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Yol parametresini değiştirerek işleve farklı değerler geçirmeyi deneyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure yay bulut uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [İstemci uygulamalarını App Service erişmek için yapılandırma](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
