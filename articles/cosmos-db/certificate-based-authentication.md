---
title: Azure Cosmos DB ile sertifika tabanlı kimlik doğrulamayı Azure Active Directory
description: Azure Cosmos DB 'ten anahtarlara erişmek üzere sertifika tabanlı kimlik doğrulaması için bir Azure AD kimliği yapılandırmayı öğrenin.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 594367db838266a6d11f4fbf3b763b024f8e14d4
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882850"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Bir Azure AD kimliği için Azure Cosmos DB hesabından anahtarlara erişmek üzere sertifika tabanlı kimlik doğrulaması

Sertifika tabanlı kimlik doğrulaması, istemci uygulamanızın Azure Active Directory (Azure AD) kullanarak istemci sertifikası ile doğrulanmasını sağlar. Sertifika tabanlı kimlik doğrulamasını, Azure 'da şirket içi makine ya da sanal makine gibi bir kimliğiniz olması gereken bir makinede gerçekleştirebilirsiniz. Uygulamanız daha sonra anahtarları doğrudan uygulamada kullanmadan Azure Cosmos DB anahtarlarını okuyabilir. Bu makalede örnek bir Azure AD uygulaması oluşturma, sertifika tabanlı kimlik doğrulaması için yapılandırma, yeni uygulama kimliğini kullanarak Azure 'da oturum açma ve ardından Azure Cosmos hesabınızdan anahtarları alma açıklanır. Bu makale, kimlikleri ayarlamak için Azure PowerShell kullanır ve Azure Cosmos C# hesabınızdan anahtarların kimliğini doğrulayan ve erişen örnek bir uygulama sağlar.  

## <a name="prerequisites"></a>Önkoşullar

* [En son Azure PowerShell sürümünü](/powershell/azure/install-az-ps) yükler.

* [Azure aboneliğiniz](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

## <a name="register-an-app-in-azure-ad"></a>Azure AD 'de uygulama kaydetme

Bu adımda, Azure AD hesabınızda örnek bir Web uygulaması kaydedeceğinizi caksınız. Bu uygulama daha sonra Azure Cosmos DB hesabınızdan anahtarları okumak için kullanılır. Bir uygulamayı kaydetmek için aşağıdaki adımları kullanın: 

1. [Azure portal](https://portal.azure.com/) oturum açın.

1. Azure **Active Directory** bölmesini açın, uygulama kayıtları bölmesine gidin ve **Yeni kayıt**' yi seçin. 

   ![Active Directory yeni uygulama kaydı](./media/certificate-based-authentication/new-app-registration.png)

1. **Uygulama kaydet** formunu aşağıdaki ayrıntılarla doldur:  

   * **Ad** – uygulamanız için bir ad sağlayın, bu, "SampleApp" gibi herhangi bir ad olabilir.
   * **Desteklenen hesap türleri** – geçerli dizininizdeki kaynakların bu uygulamaya erişmesine izin vermek için **yalnızca bu kuruluş dizininde (varsayılan dizin) hesaplar '** ı seçin. 
   * **Yeniden yönlendirme URL 'si** – **Web** türünde bir uygulama seçin ve uygulamanızın barındırıldığı bir URL sağlayın, bu, herhangi bir URL olabilir. Bu örnekte, uygulama mevcut olmasa bile bir test URL 'si `https://sampleApp.com` sağlayabilirsiniz.

   ![Örnek bir Web uygulamasını kaydetme](./media/certificate-based-authentication/register-sample-web-app.png)

1. Formu doldurduktan sonra **Kaydet** ' i seçin.

1. Uygulama kaydedildikten sonra, **uygulama (istemci) kimliğini** ve **nesne kimliğini**bir yere getirin ve bu ayrıntıları sonraki adımlarda kullanacaksınız. 

   ![Uygulama ve nesne kimliklerini al](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>AzureAD modülünü yükler

Bu adımda, Azure AD PowerShell modülünü yükleyeceksiniz. Bu modül, önceki adımda kaydettiğiniz uygulamanın KIMLIĞINI almak ve kendinden imzalı bir sertifikayı bu uygulamayla ilişkilendirmek için gereklidir. 

1. Yönetici haklarıyla Windows PowerShell ISE açın. Henüz yapmadıysanız, AZ PowerShell modülünü yükleyip aboneliğinize bağlanın. Birden çok aboneliğiniz varsa, geçerli aboneliğin bağlamını aşağıdaki komutlarda gösterildiği gibi ayarlayabilirsiniz:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. [Azuread](/powershell/module/azuread/?view=azureadps-2.0) modülünü yükleyip içeri aktarma

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Azure AD 'de oturum açın

Uygulamayı kaydettiğiniz Azure AD 'de oturum açın. Hesabınızda oturum açmak için Connect-AzureAD komutunu kullanın, açılır penceredeki Azure hesabı kimlik bilgilerinizi girin. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Windows PowerShell ISE başka bir örneğini açın ve kendinden imzalı bir sertifika oluşturmak ve sertifikayla ilişkili anahtarı okumak için aşağıdaki komutları çalıştırın:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Sertifika tabanlı kimlik bilgilerini oluşturma 

Ardından, uygulamanızın nesne KIMLIĞINI almak ve sertifika tabanlı kimlik bilgisini oluşturmak için aşağıdaki komutları çalıştırın. Bu örnekte, bir yıldan sonra sertifikayı dolacak şekilde ayarlayacağız, gerekli bitiş tarihine ayarlayabilirsiniz.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Yukarıdaki komut, aşağıdaki ekran görüntüsüne benzer bir çıktı ile sonuçlanır:

![Sertifika tabanlı kimlik bilgileri oluşturma çıkışı](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Azure Cosmos hesabınızı yeni kimliği kullanacak şekilde yapılandırın

1. [Azure portal](https://portal.azure.com/) oturum açın.

1. Azure Cosmos hesabınıza gidin, **erişim denetimi (IAM)** dikey penceresini açın.

1. **Rol ataması** **Ekle** ve Ekle ' yi seçin. Aşağıdaki ekran görüntüsünde gösterildiği gibi, önceki adımda oluşturduğunuz sampleApp öğesini **katkıda** bulunan rolüyle birlikte ekleyin:

   ![Azure Cosmos hesabını yeni kimliği kullanacak şekilde yapılandırma](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Formu doldurduktan sonra **Kaydet** ' i seçin


## <a name="access-the-keys-from-powershell"></a>PowerShell 'ten anahtarlara erişme

Bu adımda, uygulamayı ve oluşturduğunuz sertifikayı kullanarak Azure 'da oturum açacaksınız ve Azure Cosmos hesabınızın anahtarlarına erişirsiniz. 

1. Başlangıçta hesabınızda oturum açmak için kullandığınız Azure hesabının kimlik bilgilerini temizleyin. Aşağıdaki komutu kullanarak kimlik bilgilerini temizleyebilirsiniz:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Daha sonra, uygulamanın kimlik bilgilerini kullanarak Azure portal oturum açacağınızı ve Azure Cosmos DB anahtarlarına erişebileceğini doğrulayın:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Önceki komut, Azure Cosmos hesabınızın birincil ve ikincil ana anahtarlarını görüntüler. Anahtarları al isteğinin başarılı olduğunu ve olayın "sampleApp" uygulaması tarafından başlatıldığını doğrulamak için, Azure Cosmos hesabınızın etkinlik günlüğünü görüntüleyebilirsiniz. 
 
![Azure AD 'de anahtarları al çağrısını doğrulama](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Bir C# uygulamadan anahtarlara erişme 

Ayrıca, bir C# uygulamadan anahtarlara erişerek bu senaryoyu doğrulayabilirsiniz. Active Directory kayıtlı C# uygulamayı kullanarak Azure Cosmos DB anahtarlara erişebilen aşağıdaki konsol uygulaması. Kodu çalıştırmadan önce Tenantıd, ClientID, certName, kaynak grubu adı, abonelik KIMLIĞI, Azure Cosmos hesap adı ayrıntılarını güncelleştirdiğinizden emin olun. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Bu betik, aşağıdaki ekran görüntüsünde gösterildiği gibi birincil ve ikincil ana anahtarları verir:

![CSharp uygulama çıkışı](./media/certificate-based-authentication/csharp-application-output.png)

Önceki bölüme benzer şekilde, anahtar al isteği olayının "sampleApp" uygulaması tarafından başlatıldığını doğrulamak için Azure Cosmos hesabınızın etkinlik günlüğünü görüntüleyebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Key Vault kullanarak Azure Cosmos anahtarlarının güvenliğini sağlama](access-secrets-from-keyvault.md)

* [Azure Cosmos DB için güvenlik denetimleri](cosmos-db-security-controls.md)
