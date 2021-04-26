---
title: Java Spring Boot uygulamasında başvuruları Key Vault Azure Uygulama yapılandırması kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide, bir Java Spring Boot uygulamasından Azure Uygulama yapılandırması Key Vault başvurularını kullanmayı öğreneceksiniz
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7c5534ab836968bc4e72a54db1ddb9667d366558
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768858"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Öğretici: Java Spring uygulamasındaki Key Vault başvurularını kullanma

Bu öğreticide, Azure Key Vault ile birlikte Azure uygulama yapılandırma hizmetini kullanmayı öğreneceksiniz. Uygulama yapılandırması ve Key Vault, çoğu uygulama dağıtımında yan yana kullanılan tamamlayıcı hizmetlerdir.

Uygulama yapılandırması, Key Vault depolanan değerlere başvuruda bulunan anahtarlar oluşturarak Hizmetleri birlikte kullanmanıza yardımcı olur. Uygulama yapılandırması böyle anahtarlar oluşturduğunda, değerlerinin kendileri yerine Key Vault değerlerinin URI 'Lerini depolar.

Uygulamanız, uygulama yapılandırmasında depolanan diğer anahtarların yaptığı gibi Key Vault başvurularını almak için uygulama yapılandırma istemci sağlayıcısını kullanır. Bu durumda, uygulama yapılandırmasında depolanan değerler Key Vault değerlere başvuran URI 'Lerdir. Bunlar Key Vault değerler veya kimlik bilgileri değildir. İstemci sağlayıcısı anahtarları Key Vault başvuru olarak tanıdığından, değerlerini almak için Key Vault kullanır.

Uygulamanız hem uygulama yapılandırması hem de Key Vault için düzgün şekilde kimlik doğrulaması yapmaktan sorumludur. İki hizmet doğrudan iletişim kurmaz.

Bu öğretici kodunuzda Key Vault başvuruların nasıl uygulanacağını gösterir. Hızlı başlangıçlarda tanıtılan web uygulamasında oluşturulur. Devam etmeden önce, önce [uygulama yapılandırmasıyla bir Java Spring uygulaması oluşturun](./quickstart-java-spring-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. Örneğin, [Visual Studio Code](https://code.visualstudio.com/) Windows, MacOS ve Linux işletim sistemleri için kullanılabilen platformlar arası bir kod düzenleyicisidir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Key Vault depolanan bir değere başvuran bir uygulama yapılandırma anahtarı oluşturun.
> * Bu anahtarın değerine bir Java Spring uygulamasından erişin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Sürüm 8 ile desteklenen bir [Java Geliştirme Seti (JDK)](/java/azure/jdk) .
* [Apache Maven](https://maven.apache.org/download.cgi) sürüm 3,0 veya üzeri.

## <a name="create-a-vault"></a>Kasa oluşturma

1. Azure portal, sol üst köşedeki **kaynak oluştur** seçeneğini belirleyin:

    ![Ekran görüntüsü Azure portal kaynak oluştur seçeneğini gösterir.](./media/quickstarts/search-services.png)
1. Arama kutusuna **Key Vault** yazın.
1. Sonuçlar listesinden sol taraftaki **Anahtar kasaları** ' nı seçin.
1. **Anahtar kasaları**' nda **Ekle**' yi seçin.
1. **Anahtar Kasası oluşturma**' da sağ tarafta aşağıdaki bilgileri sağlayın:
    * Abonelik seçmek için **abonelik** ' ı seçin.
    * **Kaynak grubu**' nda **Yeni oluştur** ' u seçin ve bir kaynak grubu adı girin.
    * **Anahtar Kasası adında**, benzersiz bir ad gereklidir. Bu öğretici için **contoso-vault2** girin.
    * **Bölge** açılan listesinde bir konum seçin.
1. Diğer **Anahtar Kasası oluşturma** seçeneklerini varsayılan değerleriyle bırakın.
1. **Oluştur**’u seçin.

Bu noktada, Azure hesabınız, bu yeni kasaya erişme yetkisine sahip tek bir hesaptır.

![Ekran görüntüsü anahtar kasanızı gösterir.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için yalnızca birkaç ek adım gerçekleştirmeniz gerekir. Bu durumda, Key Vault alımı test etmek için kullanabileceğiniz bir ileti ekleyin. İleti **ileti** olarak adlandırılır ve "Hello 'dan Key Vault" değerini depolar.

1. Key Vault Özellikler sayfalarında **gizli** dizileri ' ni seçin.
1. **Oluştur/Içeri aktar**' ı seçin.
1. Gizli dizi **Oluştur** bölmesinde aşağıdaki değerleri girin:
    * **Karşıya yükleme seçenekleri**: **el ile** girin.
    * **Ad**: **ileti** girin.
    * **Değer**: **Key Vault Merhaba** yazın.
1. Diğerini varsayılan değerleriyle **gizli bir özellikler oluşturmamaya** ayrılın.
1. **Oluştur**’u seçin.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Uygulama yapılandırmasına Key Vault başvurusu ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve ardından hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin.

1. **+**  >  **Anahtar Kasası başvurusu** oluştur ' u seçin ve ardından aşağıdaki değerleri belirtin:
    * **Anahtar**: **/Application/config.asp keyvaultmessage** seçin
    * **Etiket**: Bu değeri boş bırakın.
    * **Abonelik**, **kaynak grubu** ve **Anahtar Kasası**: önceki bölümde oluşturduğunuz anahtar kasasındaki değerlere karşılık gelen değerleri girin.
    * **Gizli**: önceki bölümde oluşturduğunuz gizli anahtar adlı **iletiyi** seçin.

## <a name="connect-to-key-vault"></a>Key Vault Bağlan

1. Bu öğreticide, Key Vault kimlik doğrulaması için bir hizmet sorumlusu kullanırsınız. Bu hizmet sorumlusunu oluşturmak için Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutunu kullanın:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Bu işlem bir dizi anahtar/değer çiftini döndürür:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Hizmet sorumlusunun anahtar kasanıza erişmesine izin vermek için aşağıdaki komutu çalıştırın:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Nesne kimliğinizi almak için aşağıdaki komutu çalıştırın ve uygulama yapılandırmasına ekleyin.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** ve **AZURE_TENANT_ID** ortam değişkenlerini oluşturun. Önceki adımlarda görüntülenen hizmet sorumlusunun değerlerini kullanın. Komut satırında aşağıdaki komutları çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Bu Key Vault kimlik bilgileri yalnızca uygulamanız içinde kullanılır.  Uygulamanız, uygulama yapılandırma hizmeti 'ni eklemeden bu kimlik bilgilerini kullanarak Key Vault doğrudan kimlik doğrulaması yapar.  Key Vault, anahtarları paylaşmadan veya göstermeden hem uygulamanız hem de uygulama yapılandırma hizmetiniz için kimlik doğrulaması sağlar.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Kodunuzu Key Vault bir başvuru kullanacak şekilde güncelleştirin

1. **APP_CONFIGURATION_ENDPOINT** adlı bir ortam değişkeni oluşturun. Değerini, uygulama yapılandırma deponuzın uç noktasına ayarlayın. Uç noktayı Azure portal **erişim tuşları** dikey penceresinde bulabilirsiniz. Değişikliğin etkili olması için komut istemi ' ni yeniden başlatın. 


1. *Resources* klasöründe *Bootstrap. Properties* ' i açın. Bu dosyayı **APP_CONFIGURATION_ENDPOINT** değerini kullanacak şekilde güncelleştirin. Bu dosyadaki bir bağlantı dizesine olan başvuruları kaldırın. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. *MessageProperties. Java*' yı açın. *Keyvaultmessage* adlı yeni bir değişken ekleyin:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. *Hellocontroller. Java*' yı açın. Key Vault alınan iletiyi dahil etmek için *GetMessage* yöntemini güncelleştirin.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. *AzureCredentials. Java* adlı yeni bir dosya oluşturun ve aşağıdaki kodu ekleyin.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. *Appconfiguration. Java* adlı yeni bir dosya oluşturun. Ve aşağıdaki kodu ekleyin.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Kaynaklarınızın META-INF dizininde *Spring. Factory* adlı yeni bir dosya oluşturun ve aşağıdaki kodu ekleyin.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Maven ile Spring Boot uygulamanızı derleyin ve çalıştırın, örneğin:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Uygulamanız çalıştırıldıktan sonra, uygulamanızı test etmek için *kıvrımlı* kullanın, örneğin:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Uygulama yapılandırma deposuna girdiğiniz iletiyi görürsünüz. Ayrıca, Key Vault girdiğiniz iletiyi görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Key Vault depolanan bir değere başvuran bir uygulama yapılandırma anahtarı oluşturdunuz. Java Spring uygulamanızda Özellik bayraklarını nasıl kullanacağınızı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./quickstart-feature-flag-spring-boot.md)
