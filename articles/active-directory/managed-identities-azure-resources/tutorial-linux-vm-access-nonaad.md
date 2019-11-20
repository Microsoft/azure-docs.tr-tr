---
title: Azure Key Vault'a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma
description: Linux VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure Resource Manager’a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d434069870fbfa52687a3abc7913fd121687a1
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000069"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Öğretici: Azure Key Vault'a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide Azure Key Vault'a erişmek amacıyla bir Linux sanal makinesi (VM) için sistem tarafından atanmış yönetilen bir kimliği nasıl kullanacağınız gösterilmektedir. Önyükleme işlevi gören Key Vault, istemci uygulamanızın gizli diziyi kullanarak Azure Active Directory (AD) tarafından güvenlik altına alınmamış kaynaklara erişmenize olanak sağlar. Azure kaynaklarına yönelik yönetilen kimlikler Azure tarafından otomatik olarak yönetilir ve kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmanıza olanak tanır. 

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM'nize Key Vault'ta depolanan gizli diziye erişim verme 
> * VM'nin kimliğini kullanarak erişim belirteci alma ve Key Vault'tan gizli diziyi almak için bunu kullanma 
 
## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>VM'nize Key Vault'ta depolanan Gizli Diziye erişim verme  

Azure kaynakları için yönetilen hizmet kimlikleri kullanıldığında kodunuz Azure Active Directory kimlik doğrulamasını destekleyen kaynaklarda kimlik doğrulaması yapmak için belirteçlere erişebilir. Bununla birlikte, Azure hizmetlerinin tümü Azure AD kimlik doğrulamasını desteklemez. Söz konusu hizmetlerle Azure kaynakları için yönetilen kimlikleri kullanmak için, hizmet kimlik bilgilerini Azure Key Vault'ta depolayın ve Azure kaynakları için yönetilen kimlikleri kullanarak Key Vault'a erişip kimlik bilgilerini alın. 

İlk olarak, Key Vault'u oluşturmalı ve VM'mize Key Vault üzerinde sistem tarafından atanan yönetilen kimlik erişimi vermeliyiz.   

1. Sol gezinti çubuğunun üst kısmında **Kaynak oluştur** > **Güvenlik + Kimlik** > **Key Vault**'u seçin.  
2. Yeni Key Vault için bir **Ad** belirtin. 
3. Key Vault'u daha önce oluşturduğunuz VM'yle aynı aboneliğe ve kaynak grubuna yerleştirin. 
4. **Erişim ilkeleri**’ni seçin ve **Yeni ekle**'ye tıklayın. 
5. Şablondan yapılandır'da **Gizli Dizi Yönetimi**'ni seçin. 
6. **Sorumlu Seç**'i seçin ve arama alanına daha önce oluşturduğunuz VM'nin adını girin.  Sonuç listesinde VM'yi seçin ve **Seç**'e tıklayın. 
7. Yeni erişim ilkesini ekleme işlemini tamamlamak için **Tamam**'a tıklayın ve erişim ilkesi seçimini bitirmek için de **Tamam**'a tıklayın. 
8. Key Vault oluşturmayı tamamlamak için **Oluştur**'a tıklayın. 

    ![Alternatif resim metni](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Ardından, Key Vault'a bir gizli dizi ekleyin; böylelikle VM'nizde çalıştırılan kodu kullanarak daha sonra gizli diziyi alabilirsiniz: 

1. **Tüm Kaynaklar**'ı seçin, sonra da oluşturduğunuz Key Vault'u bulun ve seçin. 
2. **Gizli Diziler**'i seçin ve **Ekle**'ye tıklayın. 
3. **Karşıya yükleme seçenekleri**'nden **El ile** seçeneğini belirtin. 
4. Gizli dizi için bir ad ve değer girin.  Değer, istediğiniz herhangi bir şey olabilir. 
5. Etkinleştirme tarihi ile sona erme tarihini boş bırakın ve **Etkin** seçeneğini **Evet** değerinde bırakın. 
6. Gizli diziyi oluşturmak için **Oluştur**'a tıklayın. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>VM'nin kimliğini kullanarak erişim belirteci alma ve Key Vault'tan gizli diziyi almak için bunu kullanma  

Bu adımları tamamlamak bir SSH istemciniz olmalıdır.  Windows kullanıyorsanız, [Linux için Windows Alt Sistemi](https://msdn.microsoft.com/commandline/wsl/about)'ndeki SSH istemcisini kullanabilirsiniz. SSSH istemcinizin anahtarlarını yapılandırmak için yardıma ihtiyacınız olursa, bkz. [Azure'da Windows ile SSH anahtarlarını kullanma](../../virtual-machines/linux/ssh-from-windows.md) veya [Azure’da Linux VM’ler için SSH ortak ve özel anahtar çifti oluşturma](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Portalda Linux VM’nize gidin ve **Genel Bakış**’ta **Bağlan**’a tıklayın. 
2. Tercih ettiğiniz SSH istemcisiyle VM’ye bağlanmak için **Bağlan**’ı seçin. 
3. Terminal penceresinde, KıVRıMLı ' ı kullanarak, Azure Key Vault için bir erişim belirteci almak üzere Azure kaynakları uç noktası için yerel yönetilen kimliklere bir istek oluşturun.  
 
    Erişim belirteci için CURL isteği aşağıda yer alır.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Yanıt, Resource Manager'a erişebilmeniz için gereken erişim belirtecini içerir. 
    
    Yanıt:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Azure Key Vault’ta kimlik doğrulamak için bu erişim belirtecini kullanabilirsiniz.  Sonraki CURL isteği, CURL ve Key Vault REST API kullanarak Key Vault’tan nasıl gizli dizi okunacağını gösterir.  Key Vault'unuzun URL'sine ihtiyacınız olacaktır. Bu URL, Key Vault'un **Genel Bakış** sayfasındaki **Temel Parçalar** bölümünde yer alır.  Ayrıca önceki çağrıda aldığınız erişim belirtecine de ihtiyacınız olur. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Yanıt şöyle görünür: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault'tan gizli diziyi aldıktan sonra, bunu kullanarak ad ve parola gerektiren bir hizmette kimlik doğrulaması yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Key Vault'a erişmek için Linux VM sistem tarafından atanan yönetilen kimlik kullanmayı öğrendiniz.  Azure Key Vault hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Anahtar Kasası.](/azure/key-vault/key-vault-overview)




