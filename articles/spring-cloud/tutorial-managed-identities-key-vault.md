---
title: 'Öğretici: bağlanmak için yönetilen kimlik Key Vault'
description: Key Vault Azure yay bulutu uygulamasına bağlamak için yönetilen kimlik ayarlama
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 870a04af244d18826e1041316895f746e27870eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786594"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Öğretici: Azure Spring Cloud uygulamasına Key Vault bağlamak için yönetilen bir kimlik kullanın

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Bu makalede, Azure yay bulutu uygulaması için yönetilen kimlik oluşturma ve Azure Key Vault erişmek için kullanma hakkında yönergeler verilmektedir.

Azure Key Vault, uygulamanızın belirteçleri, parolaları, sertifikaları, API anahtarlarını ve diğer gizli dizileri güvenli bir şekilde depolamak ve güvenle denetlemek için kullanılabilir. Azure Active Directory (AAD) ' de yönetilen bir kimlik oluşturabilir ve kodunuzda kimlik bilgilerini görüntülemeye gerek kalmadan Key Vault dahil AAD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)
* [Azure CLı sürüm 2.0.67 veya üstünü yükler](/cli/azure/install-azure-cli)
* [Maven 3,0 veya üstünü yükler](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [Az Group Create](/cli/azure/group#az_group_create)komutunu kullanarak hem Key Vault hem de yay bulutunu içerecek bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Key Vault ayarlama
Key Vault oluşturmak için [az keykasacreate](/cli/azure/keyvault#az_keyvault_create)komutunu kullanın.

> [!Important]
> Her Key Vault benzersiz bir adı olmalıdır. Aşağıdaki örneklerde <-keykasasının adı> Key Vault adıyla değiştirin.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Döndürülen `vaultUri` "https://<-keykasa-adı>. Vault.Azure.net" biçiminde olacak döndürülen bir örneği yapın. Bu işlem, aşağıdaki adımda kullanılacaktır.

Artık, [az keykasasecret set](/cli/azure/keyvault/secret#az_keyvault_secret_set)komutuyla Key Vault bir gizli dizi yerleştirebilirsiniz:

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure yay bulut hizmeti ve uygulaması oluşturma
Karşılık gelen uzantıyı yükledikten sonra Azure CLı komutu ile bir Azure yay bulutu örneği oluşturun `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Aşağıdaki örnek, `springapp` parametresi tarafından istenen şekilde sistem tarafından atanan yönetilen kimlik adlı bir uygulama oluşturur `--assign-identity` .

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Döndürülen, biçiminde olacak şekilde bir değişiklik yapın `url` `https://<your-app-name>.azuremicroservices.io` . Bu işlem, aşağıdaki adımda kullanılacaktır.


## <a name="grant-your-app-access-to-key-vault"></a>Uygulamanıza Key Vault erişim izni verin
`az keyvault set-policy`Uygulamanız için Key Vault uygun erişim vermek üzere kullanın.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}`Sistem tarafından atanan yönetilen kimlik devre dışı bırakıldıktan sonra uygulamanıza erişimi kaldırmak için kullanın.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Spring Boot Starter ile örnek Spring Boot uygulaması oluşturma
Bu uygulamanın Azure Key Vault parolaları almak için erişimi olacak. Başlangıç uygulaması: [Azure Key Vault gizlilikler Spring Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)' i kullanın.  Azure Key Vault yay **PropertySource** örneği olarak eklenir.  Azure Key Vault depolanan gizli dizileri kolay bir şekilde erişilebilir ve dosyalardaki özellikler gibi herhangi bir externalized yapılandırma özelliği gibi kullanılabilir. 

1. Azure Key Vault Spring Starter ile start.spring.io 'tan örnek bir proje oluşturun. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Uygulamanızda Key Vault belirtin. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Azure yay bulut uygulamaları için yönetilen kimlik kullanmak için, aşağıdaki içeriğe sahip özellikleri src/Main/Resources/Application. Properties öğesine ekleyin.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Anahtar Kasası URL 'si `application.properties` yukarıdaki gibi eklenmelidir. Aksi takdirde, Anahtar Kasası URL 'si çalışma zamanı sırasında yakalanmayabilir.

3. Kod örneğini src/Main/Java/com/example/demo/DemoApplication. Java öğesine ekleyin. Bağlantı dizesini Key Vault alır. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    pom.xml açarsanız, bağımlılığı görürsünüz `azure-keyvault-secrets-spring-boot-starter` . Bu bağımlılığı pom.xml projenize ekleyin. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Örnek uygulamanızı paketleyin. 

    ```azurecli
    mvn clean package
    ```

5. Artık Azure CLı komutuyla uygulamanızı Azure 'a dağıtabilirsiniz  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Uygulamanızı test etmek için genel uç noktaya veya test uç noktasına erişin.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    -Keykasasının adı> <Key Vault https://' den gizli connectionString değeri başarıyla alındı. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; Database = DATABASE; "iletisini görürsünüz.

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Java SDK ile örnek Spring Boot uygulaması oluşturma

Bu örnek Azure Key Vault parolaları ayarlayıp alabilir. [Java için Azure Key Vault gizli istemci kitaplığı](/java/api/overview/azure/security-keyvault-secrets-readme) , Azure SDK 'sı genelinde Azure Active Directory belirteç kimlik doğrulama desteği sağlar. AAD belirteci kimlik doğrulamasını desteklemek üzere Azure SDK istemcileri oluşturmak için kullanılabilecek bir **TokenCredential** uygulamaları kümesi sağlar.

Azure Key Vault gizli istemci kitaplığı, belirteçlere, parolalara, API anahtarlarına ve diğer gizli anahtarlara erişimi güvenli bir şekilde depolamanıza ve denetlemenize olanak tanır. Kitaplık, gizli dizileri ve sürümlerini oluşturma, alma, güncelleştirme, silme, Temizleme, yedekleme, geri yükleme ve listeme işlemleri sunar.

1. Örnek bir proje klonlayın. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Uygulamanızda Key Vault belirtin. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Azure yay bulut uygulamaları için yönetilen kimlik ' i kullanmak için, *src/Main/Resources/Application. Properties* öğesine aşağıdaki içerikle özellikler ekleyin.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Kodunuzda Key Vault gizli dizileri ayarlamak veya almak için Azure Active Directory ve [Secretclientbuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) 'dan belirteç almak üzere [Managedıdentitycredentialbuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) 'ı ekleyin.

    Kopyalanan örnek projenin [Maincontroller. Java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) dosyasından örneği alın.

    Ayrıca `azure-identity` , pom.xml bağımlılığı da dahil edin `azure-security-keyvault-secrets` . Kopyalanan örnek projenin [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) örneğini alın. 

4. Örnek uygulamanızı paketleyin. 

    ```azurecli
    mvn clean package
    ```

5. Şimdi Azure CLı komutuyla uygulamayı Azure 'a dağıtın  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Uygulamanızı test etmek için genel uç noktaya veya test uç noktasına erişin. 

    İlk olarak, Azure Key Vault ayarladığınız gizli anahtar değerini alın. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    -Keykasasının adı> <Key Vault https://' den gizli connectionString değeri başarıyla alındı. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; Database = DATABASE; "iletisini görürsünüz.

    Şimdi bir gizli dizi oluşturup Java SDK 'sını kullanarak alın. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    "Anahtar Kasası adı>. vault.azure.net: Success <Key Vault https://' den gizli test değeri başarıyla alındı" iletisini görürsünüz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Spring Cloud 'da yönetilen kimlikle Depolama Blobu 'ne erişme](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Azure yay bulut uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub eylemlerinde Key Vault Azure Spring Cloud kimlik doğrulaması yapma](./spring-cloud-github-actions-key-vault.md)
