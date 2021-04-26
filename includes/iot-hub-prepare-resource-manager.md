---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558475"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Azure Resource Manager isteklerinin kimliğini doğrulamak için hazırlanma
Azure Active Directory (AD) ile [Azure Resource Manager][lnk-authenticate-arm] kullanarak kaynaklarda gerçekleştirdiğiniz tüm işlemlerin kimlik doğrulamasını yapmanız gerekir. Bunu yapılandırmanın en kolay yolu PowerShell veya Azure CLı kullanmaktır.

Devam etmeden önce [Azure PowerShell cmdlet 'lerini][lnk-powershell-install] yükleyebilirsiniz.

Aşağıdaki adımlarda, PowerShell kullanarak bir AD uygulaması için parola kimlik doğrulamasının nasıl ayarlanacağı gösterilmektedir. Bu komutları, standart bir PowerShell oturumunda çalıştırabilirsiniz.

1. Aşağıdaki komutu kullanarak Azure aboneliğinizde oturum açın:

    ```powershell
    Connect-AzAccount
    ```

1. Birden çok Azure aboneliğiniz varsa Azure 'da oturum açmak, kimlik bilgilerinizle ilişkili tüm Azure aboneliklerine erişmenizi sağlar. Kullanabileceğiniz Azure aboneliklerini listelemek için aşağıdaki komutu kullanın:

    ```powershell
    Get-AzSubscription
    ```

    IoT Hub 'ınızı yönetmek üzere komutları çalıştırmak için kullanmak istediğiniz aboneliği seçmek için aşağıdaki komutu kullanın. Önceki komutun çıkışında yer alan abonelik adını veya kimliği kullanabilirsiniz:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. **Tenantıd** ve **SubscriptionID**'nizi bir yere getirin. Bunlara daha sonra ihtiyacınız olacak.
3. Yer tutucuları değiştirerek aşağıdaki komutu kullanarak yeni bir Azure Active Directory uygulaması oluşturun:
   
   * **{Display Name}:** uygulamanız Için **MySampleApp** gibi bir görünen ad
   * **{Giriş sayfası URL 'si}:** uygulamanızın ana sayfasının **http: \/ /MySampleApp/Home** gibi URL 'si. Bu URL 'nin gerçek bir uygulamayı göstermesi gerekmez.
   * **{Uygulama tanımlayıcısı}:** **Http: \/ /MySampleApp** gibi benzersiz bir tanımlayıcı. Bu URL 'nin gerçek bir uygulamayı göstermesi gerekmez.
   * **{Password}:** Uygulamanızda kimlik doğrulaması için kullandığınız bir parola.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Oluşturduğunuz uygulamanın **ApplicationId** 'sini bir yere getirin. Buna daha sonra ihtiyacınız vardır.
5. Aşağıdaki komutu kullanarak yeni bir hizmet sorumlusu oluşturun ve **{MyApplicationId}** öğesini önceki adımdaki **ApplicationId** ile değiştirin:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. **{MyApplicationId}** öğesini **ApplicationId**'lerinizle değiştirerek aşağıdaki komutu kullanarak bir rol ataması ayarlayın.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Artık özel C# uygulamanızdan kimlik doğrulaması yapmanızı sağlayan Azure AD uygulamasını oluşturmayı tamamladınız. Bu öğreticide daha sonra aşağıdaki değerlere ihtiyacınız vardır:

* TenantId
* SubscriptionId
* ApplicationID
* Parola

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps