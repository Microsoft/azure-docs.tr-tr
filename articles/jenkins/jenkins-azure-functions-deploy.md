---
title: Jenkins Azure Işlevleri eklentisini kullanarak Azure Işlevlerine dağıtma
description: Jenkins Azure Işlevleri eklentisini kullanarak Azure Işlevlerine dağıtmayı öğrenin
keywords: Jenkins, Azure, DevOps, Java, Azure işlevleri
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: af3e8dfd6e2bfc676e659a03d92658af66b5bcde
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158780"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Jenkins Azure Işlevleri eklentisini kullanarak Azure Işlevlerine dağıtma

[Azure işlevleri](/azure/azure-functions/) , sunucusuz bir işlem hizmetidir. Azure Işlevleri 'ni kullanarak, altyapıyı sağlamadan veya yönetmeksizin isteğe bağlı kod çalıştırabilirsiniz. Bu öğreticide, Azure Işlevleri eklentisini kullanarak bir Java işlevinin Azure Işlevlerine nasıl dağıtılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- **Jenkins sunucusu**: bir Jenkins sunucunuz yüklü değilse, [Azure 'Da Jenkins sunucusu oluşturma](./install-jenkins-solution-template.md)makalesine bakın.

  > [!TIP]
  > Bu öğretici için kullanılan kaynak kodu, [Visual Studio Çin GitHub](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)deposunda bulunur.

## <a name="create-a-java-function"></a>Java işlevi oluşturma

Java çalışma zamanı yığını ile bir Java işlevi oluşturmak için [Azure Portal](https://portal.azure.com) veya [Azure CLI](/cli/azure/?view=azure-cli-latest)kullanın.

Aşağıdaki adımlarda, Azure CLı kullanarak bir Java işlevinin nasıl oluşturulacağı gösterilmektedir:

1. **&lt;resource_group >** yer tutucusunu kaynak grubu adınızla değiştirerek bir kaynak grubu oluşturun.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Yer tutucuları uygun değerlerle değiştirerek bir Azure depolama hesabı oluşturun.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Yer tutucuları uygun değerlerle değiştirerek test işlevi uygulamasını oluşturun.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Jenkins sunucusunu hazırlama

Aşağıdaki adımlarda, Jenkins sunucusunun nasıl hazırlanacağı açıklanmaktadır:

1. Azure 'da bir [Jenkins sunucusu](https://aka.ms/jenkins-on-azure) dağıtın. Jenkins sunucusunun bir örneği zaten yüklü değilse, [Azure 'da bir Jenkins sunucusu oluşturun](./install-jenkins-solution-template.md) , süreç boyunca size kılavuzluk eder.

1. SSH ile Jenkins örneğinde oturum açın.

1. Jenkins örneğinde, Maven 'yi aşağıdaki komutu kullanarak yüklemelisiniz:

    ```terminal
    sudo apt install -y maven
    ```

1. Jenkins örneğinde, bir Terminal isteminde aşağıdaki komutları vererek [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) yüklersiniz:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Jenkins panosunda aşağıdaki eklentileri yüklersiniz:

    - Azure Işlevleri eklentisi
    - Enveklemesine eklentisi

1. Jenkins 'in kimlik doğrulaması ve Azure kaynaklarına erişmesi için bir Azure hizmet sorumlusu olması gerekir. Adım adım yönergeler için [Azure App Service dağıtma](./tutorial-jenkins-deploy-web-app-azure-app-service.md) bölümüne bakın.

1. Azure hizmet sorumlusu ' nı kullanarak Jenkins 'te bir "Microsoft Azure hizmet sorumlusu" kimlik bilgisi türü ekleyin. [Azure App Service dağıtma](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) öğreticisine bakın.

## <a name="fork-the-sample-github-repo"></a>Örnek GitHub deposunun çatalını oluşturma

1. [Tek veya hatta örnek uygulama Için GitHub deposunda oturum açın](https://github.com/VSChina/odd-or-even-function.git).

1. GitHub 'daki sağ üst köşede **çatal**' ı seçin.

1. GitHub hesabınızı seçmek ve bu adımları izlemek için istemleri izleyin.

## <a name="create-a-jenkins-pipeline"></a>Jenkins işlem hattı oluşturma

Bu bölümde, [Jenkins](https://jenkins.io/doc/book/pipeline/)işlem hattını oluşturursunuz.

1. Jenkins panosunda bir işlem hattı oluşturun.

1. **Çalıştırma için bir ortam**hazırlamayı etkinleştirin.

1. **Özellikler içeriğine**aşağıdaki ortam değişkenlerini ekleyin ve yer tutucuları, ortamınız için uygun değerlerle değiştirin:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. İşlem **hattı-> tanımı** bölümünde **SCM 'den işlem hattı betiği**' ni seçin.

1. [Jenkinsfile örneğinde](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)kullanmak için GitHub ÇATALıNıZıN URL 'sini ve betik yolunu ("doc/Resources/Jenkins/JenkinsFile") girin.

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Oluşturma ve dağıtma

Jenkins işini çalıştırmak artık zaman alabilir.

1. İlk olarak, [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) makalesindeki yönergeler aracılığıyla yetkilendirme anahtarını edinin.

1. Tarayıcınızda uygulamanın URL 'sini girin. Yer tutucuları uygun değerlerle değiştirin ve Java işlevi için girdi olarak **&lt;input_number >** için sayısal bir değer belirtin.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Aşağıdaki örnek çıktıya benzer sonuçlar görürsünüz (tek bir sayı-365-test olarak kullanılır):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımla oluşturduğunuz kaynakları silin:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri hakkında daha fazla bilgi edinmek için aşağıdaki kaynağa bakın:
> [!div class="nextstepaction"]
> [Azure Işlevleri belgeleri](/azure/azure-functions/)
