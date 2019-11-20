---
title: .NET kullanarak Azure Key Vault için hizmetten hizmete kimlik doğrulaması
description: .NET kullanarak Azure Key Vault kimlik doğrulaması yapmak için Microsoft. Azure. Services. AppAuthentication kitaplığını kullanın.
keywords: Azure Anahtar Kasası kimlik doğrulaması yerel kimlik bilgileri
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 6996a5965454dfd5997f0c0404e0c348c68b626f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177463"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET kullanarak Azure Key Vault için hizmetten hizmete kimlik doğrulaması

Azure Key Vault kimlik doğrulaması yapmak için, paylaşılan bir gizli dizi ya da sertifika olan bir Azure Active Directory (Azure AD) kimlik bilgisine sahip olmanız gerekir.

Bu kimlik bilgilerini yönetmek zor olabilir. Kimlik bilgilerini kaynak veya yapılandırma dosyalarına dahil ederek bir uygulamaya paketlemekte olması mümkündür. .NET kitaplığı için `Microsoft.Azure.Services.AppAuthentication` Bu sorunu basitleştirir. Yerel geliştirme sırasında kimlik doğrulaması yapmak için geliştiricinin kimlik bilgilerini kullanır. Çözüm daha sonra Azure 'a dağıtıldığında, kitaplık otomatik olarak uygulama kimlik bilgilerine geçer. Geliştirici kimlik bilgilerini yerel geliştirme sırasında kullanmak daha güvenlidir, çünkü Azure AD kimlik bilgileri oluşturmanız veya geliştiriciler arasında kimlik bilgileri paylaşmanız gerekmez.

@No__t-0 kitaplığı, kimlik doğrulamasını otomatik olarak yönetir, bu da kimlik bilgileriniz yerine çözümünüze odaklanmanıza olanak tanır. Microsoft Visual Studio, Azure CLı veya Azure AD Tümleşik kimlik doğrulamasıyla yerel geliştirmeyi destekler. Yönetilen bir kimliği destekleyen bir Azure kaynağına dağıtıldığında, kitaplık otomatik olarak [Azure kaynakları için Yönetilen kimlikler](../active-directory/msi-overview.md)kullanır. Kod veya yapılandırma değişikliği gerekli değildir. Kitaplık Ayrıca, yönetilen bir kimlik olmadığında veya geliştiricinin güvenlik bağlamı yerel geliştirme sırasında belirlenemediğinde Azure AD [istemci kimlik bilgilerinin](../azure-resource-manager/resource-group-authenticate-service-principal.md) doğrudan kullanımını destekler.

## <a name="prerequisites"></a>Önkoşullar

- [Visual studio 2019](https://www.visualstudio.com/downloads/) veya [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Visual Studio için uygulama kimlik doğrulama uzantısı, Visual Studio 2017 güncelleştirme 5 için ayrı bir uzantı olarak kullanılabilir ve güncelleştirme 6 ve sonraki sürümlerinde ürünle birlikte paketlenmiştir. Güncelleştirme 6 veya sonraki sürümlerde, Visual Studio yükleyicisi içinden Azure Geliştirme Araçları ' nı seçerek uygulama kimlik doğrulaması uzantısının yüklenmesini doğrulayabilirsiniz.

## <a name="using-the-library"></a>Kitaplığı kullanma

.NET uygulamaları için, yönetilen kimlik ile çalışmanın en kolay yolu `Microsoft.Azure.Services.AppAuthentication` paketini kullanmaktır. Kullanmaya başlamak için şu adımları uygulayın:

1. **Araçlar** > **nuget Paket Yöneticisi** >  çözüm için [Microsoft. Azure. Services. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ve [Microsoft. Azure. Keykasa](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet paketlerine başvurular eklemek için**NuGet Paketlerini Yönet** ' i seçin. projeniz.

1. Aşağıdaki kodu ekleyin:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

@No__t-0 sınıfı, belirteci bellekte önbelleğe alır ve süresi dolmadan hemen önce Azure AD 'den alır. Bu nedenle, `GetAccessTokenAsync` yöntemini çağırmadan önce artık süre sonunu denetlemeniz gerekmez. Yalnızca belirtecini kullanmak istediğinizde yöntemi çağırın.

@No__t-0 yöntemi bir kaynak tanımlayıcısı gerektirir. Microsoft Azure hizmetleri hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Yerel geliştirme kimlik doğrulaması

Yerel geliştirme için iki birincil kimlik doğrulama senaryosu vardır: [Azure hizmetlerinde kimlik doğrulaması](#authenticating-to-azure-services)ve [özel hizmetlere kimlik doğrulama](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Azure hizmetlerinde kimlik doğrulaması

Yerel makineler, Azure kaynakları için yönetilen kimlikleri desteklemez. Sonuç olarak, `Microsoft.Azure.Services.AppAuthentication` kitaplığı, geliştirici kimlik bilgilerinizi yerel geliştirme ortamınızda çalıştırmak için kullanır. Çözüm Azure 'a dağıtıldığında, kitaplık bir OAuth 2,0 istemci kimlik bilgisi verme akışına geçiş yapmak için yönetilen bir kimlik kullanır. Bu yaklaşım, endişelenmeden aynı kodu yerel olarak ve uzaktan sınaymanız anlamına gelir.

Yerel geliştirme için `AzureServiceTokenProvider`, **Visual Studio**, **Azure komut satırı arabirimi** (CLı) veya **Azure AD Tümleşik kimlik doğrulaması**kullanarak belirteçleri getirir. Her seçenek sırayla denenir ve kitaplık başarılı olan ilk seçeneği kullanır. Hiçbir seçenek çalışmadıysanız, ayrıntılı bilgilerle `AzureServiceTokenProviderException` özel durumu oluşturulur.

#### <a name="authenticating-with-visual-studio"></a>Visual Studio ile kimlik doğrulama

Visual Studio kullanarak kimlik doğrulaması yapmak için:

1. Visual Studio 'da oturum açın ve **seçenekleri**açmak **için @no__t-** 1 @ no__t-2 @ no__t-3**seçeneklerini** kullanın.

1. **Azure hizmeti kimlik doğrulaması**' nı seçin, yerel geliştirme için bir hesap seçin ve **Tamam**' ı seçin.

Belirteç sağlayıcı dosyasını içeren hatalar gibi Visual Studio 'Yu kullanarak sorunlarla karşılaşırsanız, önceki adımları dikkatle gözden geçirin.

Geliştirici belirtecinizi yeniden kimlik doğrulaması yapmanız gerekebilir. Bunu yapmak için **araçlar**&nbsp; @ no__t-2 @ no__t-3**seçeneklerini**belirleyin ve ardından **Azure @ no__t-6Service @ no__t-7authentication**' ı seçin. Seçili hesap altında bir **yeniden kimlik doğrulaması** bağlantısı arayın. Kimlik doğrulamak için seçin.

#### <a name="authenticating-with-azure-cli"></a>Azure CLı ile kimlik doğrulama

Azure CLı 'yı yerel geliştirme için kullanmak için [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) veya sonraki bir sürümü kullandığınızdan emin olun.

Azure CLı 'yi kullanmak için:

1. Windows görev çubuğunda Azure CLı araması yapın **Microsoft Azure komut istemi**' ni açın.

1. Azure portal oturum açın: Azure 'da oturum açmak için *az oturum* açın.

1. *Az Account Get-Access-Token--resource https://vault.azure.net* girerek erişimi doğrulayın. Bir hata alırsanız, doğru Azure CLı sürümünün düzgün yüklendiğini kontrol edin.

   Azure CLı varsayılan dizine yüklenmemişse, `AzureServiceTokenProvider` ' ın Azure CLı yolunu bulamadığı bir hata bildirimi alabilirsiniz. Azure CLı yükleme klasörünü tanımlamak için **Azureclipath** ortam değişkenini kullanın. `AzureServiceTokenProvider`, gerekli olduğunda, **Azureclipath** ortam değişkeninde belirtilen dizini **yol** ortam değişkenine ekler.

1. Azure CLı 'de birden çok hesap kullanarak oturum açtıysanız veya hesabınızın birden çok aboneliğe erişimi varsa, kullanılacak aboneliği belirtmeniz gerekir. *Abonelik kimliği > az Account set--subscription <* komutunu girin.

Bu komut yalnızca hata durumunda çıktı üretir. Geçerli hesap ayarlarını doğrulamak için `az account list` komutunu girin.

#### <a name="authenticating-with-azure-ad-authentication"></a>Azure AD kimlik doğrulamasıyla kimlik doğrulama

Azure AD kimlik doğrulamasını kullanmak için şunları doğrulayın:

- Şirket içi Active Directory Azure AD 'ye eşitlenir. Daha fazla bilgi için bkz. [Azure Active Directory karma kimliği nedir?](../active-directory/connect/active-directory-aadconnect.md).

- Kodunuz, etki alanına katılmış bir bilgisayarda çalışıyor.

### <a name="authenticating-to-custom-services"></a>Özel hizmetlerde kimlik doğrulaması

Bir hizmet Azure hizmetlerini çağırdığında, Azure hizmetleri hem kullanıcılara hem de uygulamalara erişim izni sağladığından önceki adımlar çalışır.

Özel bir hizmet çağıran bir hizmet oluştururken yerel geliştirme kimlik doğrulaması için Azure AD istemci kimlik bilgilerini kullanın. İki seçenek vardır:

- Azure 'da oturum açmak için hizmet sorumlusu kullanın:

    1. Hizmet sorumlusu oluşturun. Daha fazla bilgi için bkz. [Azure CLI Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Azure CLı kullanarak aşağıdaki komutla oturum açın:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Hizmet sorumlusu bir aboneliğe erişebileceğinden, `--allow-no-subscriptions` bağımsız değişkenini kullanın.

- Hizmet sorumlusu ayrıntılarını belirtmek için ortam değişkenlerini kullanın. Daha fazla bilgi için bkz. [bir hizmet sorumlusu kullanarak uygulamayı çalıştırma](#running-the-application-using-a-service-principal).

Azure 'da oturum açtıktan sonra, `AzureServiceTokenProvider` yerel geliştirme için bir belirteç almak üzere hizmet sorumlusunu kullanır.

Bu yaklaşım yalnızca yerel geliştirme için geçerlidir. Çözümünüz Azure 'a dağıtıldığında, kitaplık kimlik doğrulaması için yönetilen bir kimliğe geçer.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Yönetilen kimlik veya Kullanıcı tarafından atanan kimlik kullanılarak uygulamayı çalıştırma

Kodunuzu bir Azure App Service veya yönetilen bir kimlik etkin bir Azure VM 'de çalıştırdığınızda, kitaplık yönetilen kimliği otomatik olarak kullanır. Kod değişikliği gerekmez, ancak yönetilen kimliğin Anahtar Kasası için *Al* izinleri olmalıdır. Anahtar kasasının *erişim ilkeleri*aracılığıyla yönetilen kimliğe *Al* izinleri verebilirsiniz.

Alternatif olarak, Kullanıcı tarafından atanan bir kimlikle kimlik doğrulaması yapabilirsiniz. Kullanıcı tarafından atanan kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler hakkında](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Kullanıcı tarafından atanan bir kimlikle kimlik doğrulamak için, bağlantı dizesinde kullanıcı tarafından atanan kimliğin Istemci KIMLIĞINI belirtmeniz gerekir. Bağlantı dizesi [bağlantı dizesi desteğiyle](#connection-string-support)belirtilir.

## <a name="running-the-application-using-a-service-principal"></a>Bir hizmet sorumlusu kullanarak uygulamayı çalıştırma

Kimlik doğrulaması için bir Azure AD Istemci kimlik bilgisi oluşturulması gerekebilir. Bu durum aşağıdaki örneklerde ortaya çıkabilir:

- Kodunuz yerel bir geliştirme ortamında çalışıyor, ancak geliştiricinin kimliği altında değil. Örneğin, Service Fabric, yerel geliştirme için [NetworkService hesabını](../service-fabric/service-fabric-application-secret-management.md) kullanır.

- Kodunuz yerel bir geliştirme ortamında çalışır ve özel bir hizmette kimlik doğrulaması yaptığınızda geliştirici kimliğinizi kullanamazsınız.

- Kodunuz, Azure Batch gibi Azure kaynakları için yönetilen kimlikleri henüz desteklemeyen bir Azure işlem kaynağı üzerinde çalışıyor.

Uygulamanızı çalıştırmak için hizmet sorumlusu kullanmanın üç birincil yöntemi vardır. Bunlardan herhangi birini kullanmak için, önce bir hizmet sorumlusu oluşturmanız gerekir. Daha fazla bilgi için bkz. [Azure CLI Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Azure AD 'de oturum açmak için yerel anahtar deposunda bir sertifika kullanın

1. Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutunu kullanarak bir hizmet sorumlusu sertifikası oluşturun.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Bu komut, giriş dizininizde depolanan bir. ped dosyası (özel anahtar) oluşturur. Bu sertifikayı *LocalMachine* ya da *CurrentUser* deposuna dağıtın.

    > [!Important]
    > CLı komutu bir. ped dosyası oluşturur, ancak Windows yalnızca PFX sertifikaları için yerel destek sağlar. Bunun yerine bir PFX sertifikası oluşturmak için burada gösterilen PowerShell komutlarını kullanın: [otomatik olarak imzalanan sertifikayla hizmet sorumlusu oluşturun](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Bu komutlar sertifikayı da otomatik olarak dağıtır.

1. **AzureServicesAuthConnectionString** adlı bir ortam değişkenini aşağıdaki değere ayarlayın:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    *{AppID}* , *{tenantıd}* ve *{parmak izi}* değerlerini adım 1 ' de oluşturulan değerlerle değiştirin. Dağıtım planınıza bağlı olarak, *{certificatestore}* öğesini *LocalMachine*' veya *CurrentUser*ile değiştirin.

1. Uygulamayı çalıştırın.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Azure AD 'de oturum açmak için paylaşılan gizli kimlik bilgileri kullanın

1. --SDK-auth parametresiyle Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutunu kullanarak bir parola ile hizmet sorumlusu sertifikası oluşturun.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. **AzureServicesAuthConnectionString** adlı bir ortam değişkenini aşağıdaki değere ayarlayın:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    _{AppID}_ , _{tenantıd}_ ve _{ClientSecret}_ değerlerini 1. adımda oluşturulan değerlerle değiştirin.

1. Uygulamayı çalıştırın.

Her şey doğru şekilde ayarlandıktan sonra daha fazla kod değişikliği yapmanız gerekmez. `AzureServiceTokenProvider`, Azure AD kimlik doğrulaması için ortam değişkenini ve sertifikayı kullanır.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Azure AD 'de oturum açmak için Key Vault bir sertifika kullanın

Bu seçenek, hizmet sorumlusunun istemci sertifikasını Key Vault depolamanızı ve hizmet sorumlusu kimlik doğrulaması için kullanmanızı sağlar. Bu seçeneği aşağıdaki senaryolar için kullanabilirsiniz:

- Açık hizmet sorumlusu kullanarak kimlik doğrulaması yapmak istediğiniz ve hizmet sorumlusu kimlik bilgilerini bir anahtar kasasında güvenli bir şekilde tutmak istediğiniz yerel kimlik doğrulaması. Geliştirici hesabının, anahtar kasasına erişimi olmalıdır.

- Açık kimlik bilgilerini kullanmak istediğiniz ve hizmet sorumlusu kimlik bilgilerini bir anahtar kasasında güvenli bir şekilde tutmak istediğiniz Azure kimlik doğrulaması. Bu seçeneği bir çapraz kiracı senaryosu için kullanabilirsiniz. Yönetilen kimliğin anahtar kasasına erişimi olmalıdır.

Yönetilen kimliğin veya geliştirici kimliğinizin Key Vault istemci sertifikasını alma izni olması gerekir. AppAuthentication kitaplığı, hizmet sorumlusunun istemci kimlik bilgileri olarak alınan sertifikayı kullanır.

Hizmet sorumlusu kimlik doğrulaması için bir istemci sertifikası kullanmak için:

1. Bir hizmet sorumlusu sertifikası oluşturun ve Key Vault otomatik olarak depolayın. Azure CLı 'yi kullanın [az ad SP Create-for-RBAC--keykasa\<keyvaultname >--cert \<certificatename >--Create-CERT--Skip-atama](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutu:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Sertifika tanımlayıcısı @no__t biçiminde bir URL olacaktır-0

1. Bu bağlantı dizesindeki `{KeyVaultCertificateSecretIdentifier}` ' yı sertifika tanımlayıcısı ile değiştirin:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Örneğin, anahtar kasanıza *Mykeykasası* çağrılırsa ve *mycert*adlı bir sertifika oluşturduysanız, sertifika tanımlayıcısı şöyle olacaktır:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Bağlantı dizesi desteği

Varsayılan olarak, `AzureServiceTokenProvider` bir belirteci almak için birden çok yöntem kullanır.

İşlemi denetlemek için `AzureServiceTokenProvider` oluşturucusuna geçirilmiş veya *AzureServicesAuthConnectionString* ortam değişkeninde belirtilen bir bağlantı dizesi kullanın.

Aşağıdaki seçenekler desteklenir:

| Bağlantı dizesi seçeneği | Senaryo | Açıklamalar|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Yerel geliştirme | `AzureServiceTokenProvider`, belirteci almak için AzureCli kullanır. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Yerel geliştirme | `AzureServiceTokenProvider` belirteci almak için Visual Studio 'Yu kullanır. |
| `RunAs=CurrentUser` | Yerel geliştirme | `AzureServiceTokenProvider` belirteci almak için Azure AD Tümleşik kimlik doğrulamasını kullanır. |
| `RunAs=App` | [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` belirteci almak için yönetilen bir kimlik kullanır. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure kaynakları için Kullanıcı tarafından atanan kimlik](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider` belirteci almak için Kullanıcı tarafından atanan bir kimlik kullanır. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Özel Hizmetler kimlik doğrulaması | `KeyVaultCertificateSecretIdentifier`, sertifikanın gizli tanımlayıcısıdır. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Hizmet sorumlusu | `AzureServiceTokenProvider` Azure AD 'den belirteç almak için sertifikayı kullanır. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Hizmet sorumlusu | `AzureServiceTokenProvider` Azure AD 'den belirteç almak için sertifikayı kullanır|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Hizmet sorumlusu |`AzureServiceTokenProvider`, Azure AD 'den belirteç almak için gizli anahtar kullanır. |

## <a name="samples"></a>Lerinizi

@No__t-0 kitaplığını eylemde görmek için aşağıdaki kod örneklerine bakın.

- [Çalışma zamanında Azure Key Vault bir gizli dizi almak için yönetilen bir kimlik kullanın](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Yönetilen kimliğe sahip bir Azure VM 'Den program aracılığıyla bir Azure Resource Manager şablonu dağıtın](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- Azure [LINUX VM 'Den Azure hizmetlerini çağırmak için .NET Core örneğini ve yönetilen bir kimliği kullanın](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>AppAuthentication sorunlarını giderme

### <a name="common-issues-during-local-development"></a>Yerel geliştirme sırasında sık karşılaşılan sorunlar

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLı yüklü değil, oturum açmamışsınız veya en son sürüme sahip değilsiniz

Azure CLı 'nın sizin için bir belirteç gösterdiğini görmek için *az Account Get-Access-Token* ' i çalıştırın. **Böyle bir program bulunmazsa**, [Azure CLI 'nın en son sürümünü](/cli/azure/install-azure-cli?view=azure-cli-latest)yükler. Oturum açmanız istenebilir.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider Azure CLı yolunu bulamıyor

AzureServiceTokenProvider, Azure CLı 'yi varsayılan yüklemesi konumlarında arar. Azure CLı bulamazsa, **Azureclipath** ortam DEĞIŞKENINI Azure CLI yükleme klasörüne ayarlayın. AzureServiceTokenProvider, ortam değişkenini PATH ortam değişkenine ekler.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Azure CLı 'de birden çok hesap kullanarak oturum açtınız, aynı hesabın birden çok Kiracıdaki aboneliğe erişimi vardır veya yerel geliştirme sırasında çağrı yapmaya çalışırken bir erişim reddedildi hatası alırsınız

Azure CLı kullanarak, varsayılan aboneliği kullanmak istediğiniz hesabı olan bir hesaba ayarlayın. Aboneliğin erişmek istediğiniz kaynakla aynı kiracıda olması gerekir: **az hesap kümesi--abonelik [abonelik-kimliği]** . Hiçbir çıkış görülemeyen için başarılı oldu. Doğru hesabın artık **az Account List**kullanılarak varsayılan olduğunu doğrulayın.

### <a name="common-issues-across-environments"></a>Ortamlar arasında yaygın sorunlar

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Yetkisiz erişim, erişim engellendi, yasak veya benzer hata

Kullanılan sorumlunun erişmeye çalıştığı kaynağa erişimi yok. Bir kaynağa kullanıcı hesabınızı veya App Service MSI "katkıda bulunan" erişimini verin. Bunlardan biri, örneği yerel bilgisayarınızda çalıştırıp çalıştırdığınıza veya Azure 'da App Service Azure 'da dağıttığınıza bağlı olarak değişir. Anahtar kasaları gibi bazı kaynaklarda, kullanıcılar, uygulamalar ve gruplar gibi sorumlular için erişim izni vermek üzere kendi [erişim ilkeleri](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) de vardır.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Azure App Service dağıtılan yaygın sorunlar

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Yönetilen kimlik App Service ayarlanmadı

MSI_ENDPOINT ve MSI_SECRET ortam değişkenlerini [kudu hata ayıklama konsolunu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)kullanarak denetleyin. Bu ortam değişkenleri yoksa, App Service yönetilen kimlik etkinleştirilmez.

### <a name="common-issues-when-deployed-locally-with-iis"></a>IIS ile yerel olarak dağıtılan yaygın sorunlar

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>IIS 'de uygulamada hata ayıklarken belirteçler alınamıyor

Varsayılan olarak, AppAuth IIS 'de farklı bir kullanıcı bağlamında çalışır. Bu nedenle, erişim belirteçlerini almak için geliştirici kimliğinizi kullanma erişimi yoktur. Aşağıdaki iki adımla IIS 'yi Kullanıcı bağlamla çalışacak şekilde yapılandırabilirsiniz:
- Web uygulaması için uygulama havuzunu geçerli kullanıcı hesabınız olarak çalışacak şekilde yapılandırın. Daha fazla bilgi için [buraya](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities) bakın
- "SetProfileEnvironment" öğesini "true" olarak yapılandırın. Daha fazla bilgi için [buraya](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)bakın. 

    - %Windir%\System32\inetsrv\config\applicationHost,config adresine gidin
    - "SetProfileEnvironment" araması yapın. "False" olarak ayarlanırsa, "true" olarak değiştirin. Mevcut değilse, processModel öğesine (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) bir öznitelik olarak ekleyin ve "true" olarak ayarlayın.

- [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/index.yml)hakkında daha fazla bilgi edinin.
- [Azure AD kimlik doğrulama senaryoları](../active-directory/develop/active-directory-authentication-scenarios.md)hakkında daha fazla bilgi edinin.
