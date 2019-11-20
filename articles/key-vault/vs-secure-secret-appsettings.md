---
title: Web uygulaması için gizli uygulama ayarlarını güvenli bir şekilde kaydetme-Azure Key Vault | Microsoft Docs
description: ASP.NET Core Key Vault sağlayıcısı, Kullanıcı parolası veya .NET 4.7.1 yapılandırma oluşturucuları kullanarak Azure kimlik bilgileri veya üçüncü taraf API anahtarları gibi gizli uygulama ayarlarını güvenli bir şekilde kaydetme
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: d5662fa3cae8ba0cec0fd76965597ccac7c83889
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639474"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Gizli uygulama ayarlarını bir Web uygulaması için güvenli bir şekilde Kaydet

## <a name="overview"></a>Genel Bakış
Bu makalede, Azure uygulamaları için gizli uygulama yapılandırma ayarlarının güvenli bir şekilde nasıl kaydedileceği açıklanır.

Geleneksel olarak tüm Web uygulaması yapılandırma ayarları, Web. config gibi yapılandırma dosyalarına kaydedilir. Bu uygulama, GitHub gibi genel kaynak denetimi sistemlerine bulut kimlik bilgileri gibi gizli ayarları denetlemeye yol açar. Bu arada, kaynak kodu değiştirmek ve geliştirme ayarlarını yeniden yapılandırmak için gerekli olan ek yük nedeniyle en iyi güvenlik uygulamasını izlemek zor olabilir.

Geliştirme sürecinin güvenli olduğundan emin olmak için, uygulama gizli ayarlarını minimum veya kaynak kodu değişikliğiyle güvenli bir şekilde kaydetmek üzere araç ve çatı kitaplıkları oluşturulmuştur.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET ve .NET Core Uygulamaları

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Gizli dizi ayarlarını, kaynak denetimi klasörü dışında Kullanıcı gizli deposunda Kaydet
Hızlı bir prototip yapıyorsanız veya İnternet erişiminiz yoksa, gizli ayarlarınızı kaynak denetimi klasörü dışında Kullanıcı gizli deposuna taşımaya başlayın. Kullanıcı gizli dizisi, Kullanıcı Profil Oluşturucu klasörü altında kaydedilmiş bir dosyadır, bu nedenle gizli dizileri kaynak denetiminde iade edilmez. Aşağıdaki diyagramda [Kullanıcı gizliliğinin](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) nasıl çalıştığı gösterilmektedir.

![Kullanıcı gizli dizisi, kaynak denetimi dışında gizli ayarları tutar](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

.NET Core konsol uygulaması çalıştırıyorsanız, gizli anahtarı güvenli bir şekilde kaydetmek için Key Vault kullanın.

### <a name="save-secret-settings-in-azure-key-vault"></a>Gizli ayarları Azure Key Vault Kaydet
Bir proje geliştirmekte ve kaynak kodunu güvenli bir şekilde paylaşmanız gerekiyorsa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)kullanın.

1. Azure aboneliğinizde bir Key Vault oluşturun. Kullanıcı arabirimindeki tüm gerekli alanları doldurun ve dikey pencerenin alt kısmındaki *Oluştur* ' a tıklayın

    ![Azure Key Vault oluştur](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Size ve takım üyelerinize Key Vault erişimi verin. Büyük bir ekibiniz varsa, bir [Azure Active Directory grubu](../active-directory/active-directory-groups-create-azure-portal.md) oluşturabilir ve bu güvenlik grubuna Key Vault erişimi ekleyebilirsiniz. *Gizli izinler* açılan menüsünde, *gizli yönetim işlemleri*altında *Al* ve *Listele* ' yi işaretleyin.

    ![Key Vault erişim ilkesi Ekle](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Azure portal Key Vault için gizli anahtarı ekleyin. İç içe yapılandırma ayarları için ': ' değerini '--' ile değiştirin, bu nedenle Key Vault gizli adı geçerli olur. ': ' Key Vault gizli dizi adında olmasına izin verilmiyor.

    ![Key Vault gizli dizi ekleyin](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE] 
    > Visual Studio 2017 V 15.6 öncesinde, Visual Studio için Azure Hizmetleri kimlik doğrulama uzantısı 'nı yüklemenizi öneririz. Ancak işlevsellik Visual Studio içinde tümleştirildiği için artık kullanım dışıdır. Visual Studio 2017 ' nin eski bir sürümü kullanıyorsanız, bu işlevselliği yerel olarak kullanabilmeniz ve Visual Studio oturum açma kimliğini kullanarak anahtar kasasına erişmek için en az VS 2017 15,6 veya daha güncel güncelleştirmeleri güncelleştirmeniz önerilir.
    >
 
4. Aşağıdaki NuGet paketlerini projenize ekleyin:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
5. Aşağıdaki kodu Program.cs dosyasına ekleyin:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Key Vault URL 'nizi launchsettings. JSON dosyasına ekleyin. Ortam değişkeni adı *KEYVAULT_ENDPOINT* , adım 6 ' da eklediğiniz kodda tanımlanmıştır.

    ![Key Vault URL 'sini bir proje ortamı değişkeni olarak ekleyin](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Projede hata ayıklamayı başlatın. Başarıyla çalışmalıdır.

## <a name="aspnet-and-net-applications"></a>ASP.NET ve .NET uygulamaları

.NET 4.7.1, parolaların, kod değişikliği olmadan kaynak denetimi klasörünün dışına taşınabilmesini sağlayan Key Vault ve gizli dizi yapılandırma oluşturucularını destekler.
Devam etmek için [.NET 4.7.1 indirin](https://www.microsoft.com/download/details.aspx?id=56115) ve .NET Framework 'ün daha eski bir sürümünü kullanıyorsa uygulamanızı geçirin.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Gizli dizi ayarlarını kaynak denetimi klasörü dışında bir gizli dosyada Kaydet
Hızlı bir prototip yazıyorsanız ve Azure kaynaklarını sağlamak istemiyorsanız, bu seçenekle birlikte gidin.

1. Aşağıdaki NuGet paketini projenize yükler
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Aşağıdakine benzer bir dosya oluşturun. Bunu proje klasörünüzün dışında bir konuma kaydedin.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Gizli dosyayı, Web. config dosyanızda bir yapılandırma Oluşturucusu olacak şekilde tanımlayın. Bu bölümü *appSettings* bölümünden önce koyun.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. AppSettings bölümünde gizli dizi yapılandırma oluşturucusunun kullanılması. Gizli bir değere sahip gizli dizi ayarı için bir giriş olduğundan emin olun.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Uygulamanızda hata ayıklayın. Başarıyla çalışmalıdır.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Gizli ayarları bir Azure Key Vault kaydetme
Projeniz için bir Key Vault yapılandırmak üzere ASP.NET Core bölümündeki yönergeleri izleyin.

1. Aşağıdaki NuGet paketini projenize yükler
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Web. config 'de Key Vault Configuration Builder tanımlayın. Bu bölümü *appSettings* bölümünden önce koyun. Key Vault Genel Azure veya Sovereign bulutu kullanıyorsanız, *VAULTNAME* değerini Key Vault adı olacak şekilde değiştirin.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. AppSettings bölümünün Key Vault yapılandırma oluşturucusunu kullandığını belirtin. Gizli bir değere sahip gizli dizi ayarı için herhangi bir giriş olduğundan emin olun.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Projede hata ayıklamayı başlatın. Başarıyla çalışmalıdır.
