---
title: Öğretici `:` Azure Key Vault erişmek için yönetilen bir kimlik kullanma-Linux-Azure AD
description: Linux VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure Resource Manager’a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/16/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61673210dafbdbdbc56477ed58fb0cbe9335efcf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012699"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Öğretici: Azure Key Vault'a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, bir Linux sanal makinesinin (VM) [Azure Key Vault](../../key-vault/general/overview.md)erişmek için sistem tarafından atanan yönetilen kimlik nasıl kullanabileceği gösterilmektedir. Önyükleme görevi gören Key Vault, istemci uygulamanızın Azure Active Directory (AD) ile güvenliği olmayan kaynaklara erişmek için gizli dizi kullanmasını mümkün kılar. Yönetilen hizmet kimlikleri Azure tarafından otomatik olarak yönetilir ve kodunuzda kimlik doğrulama bilgilerini eklemeden Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması uygulamanızı sağlar.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM'nize Key Vault'ta depolanan gizli diziye erişim verme 
> * VM'nin kimliğini kullanarak erişim belirteci alma ve Key Vault'tan gizli diziyi almak için bunu kullanma 
 
## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin temel olarak anlaşılmasıdır. Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](overview.md) inceleyin. 
- Azure hesabı, [ücretsiz bir hesap için kaydolun](https://azure.microsoft.com/free/).
- Gerekli kaynak oluşturma ve rol yönetimi adımlarını gerçekleştirmek için uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "sahip" izinleri. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)konusuna bakın.
- Ayrıca, sistem tarafından atanmış Yönetilen kimlikler etkinleştirilmiş bir Linux sanal makinesine ihtiyacınız vardır.
  - Bu öğretici için bir sanal makine oluşturmanız gerekiyorsa, [Azure Portal Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) başlıklı makaleyi takip edebilirsiniz.


## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma  

Bu bölümde, VM 'nizin Key Vault depolanan bir gizli dizi erişimine nasıl izin vereceğiniz gösterilmektedir. Azure kaynakları için yönetilen kimlikler kullanıldığında kodunuz Azure AD kimlik doğrulamasını destekleyen kaynaklarda kimlik doğrulaması yapmak için erişim belirteçleri alabilir.Bununla birlikte, Azure hizmetlerinin tümü Azure AD kimlik doğrulamasını desteklemez. Söz konusu hizmetlerle Azure kaynakları için yönetilen kimlikleri kullanmak için, hizmet kimlik bilgilerini Azure Key Vault'ta depolayın ve VM’nin yönetilen kimliğini kullanarak Key Vault'a erişip kimlik bilgilerini alın.

İlk olarak, Key Vault'u oluşturmalı ve VM'mize Key Vault üzerinde sistem tarafından atanan yönetilen kimlik erişimi vermeliyiz.

1. Azure [portalını](https://portal.azure.com/) açın
1. Sol gezinti çubuğunun üst kısmında **kaynak oluştur** ' u seçin.  
1. Market 'te **Ara** kutusuna **Key Vault** yazın ve **ENTER** tuşuna basın.  
1. Sonuçlardan **Key Vault** seçin.
1. **Oluştur**’u seçin
1. Yeni Key Vault için bir **Ad** belirtin.

    ![Anahtar Kasası ekranı oluşturma](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Bu öğretici için kullandığınız sanal makineyi oluşturduğunuz aboneliği ve kaynak grubunu seçtiğinizden emin olarak tüm gerekli bilgileri doldurun.
1. **Gözden geçir + oluştur** ' u seçin
1. **Oluştur**’u seçin

### <a name="create-a-secret"></a>Gizli anahtar oluşturma

Ardından, Key Vault bir gizli dizi ekleyin, böylece daha sonra sanal makinenizde çalışan kodu kullanarak elde edebilirsiniz. Bu öğreticinin amacı doğrultusunda PowerShell kullanıyoruz, ancak aynı kavramlar bu sanal makinede yürütülen tüm kodlar için geçerlidir.

1. Yeni oluşturduğunuz Key Vault gidin.
1. **Gizli Diziler**'i seçin ve **Ekle**'ye tıklayın.
1. **Oluştur/Içeri aktar** 'ı seçin
1. **Karşıya yükleme seçeneklerinden** **gizli bir ekran oluştur** bölümünde **el ile** seçili bırakın.
1. Gizli dizi için bir ad ve değer girin.  Değer, istediğiniz herhangi bir şey olabilir. 
1. Etkinleştirme tarihi ile sona erme tarihini boş bırakın ve **Etkin** seçeneğini **Evet** değerinde bırakın. 
1. Gizli diziyi oluşturmak için **Oluştur**'a tıklayın.

   ![Gizli anahtar oluşturma](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Erişim verme

Sanal makine tarafından kullanılan yönetilen kimliğe, Key Vault depolayabilmemiz için gizli dizi okumak üzere erişim verilmesi gerekir.

1. Yeni oluşturduğunuz Key Vault gidin
1. Sol taraftaki menüden **erişim ilkesi** ' ni seçin.
1. **Erişim Ilkesi Ekle** ' yi seçin

   ![Anahtar Kasası erişim ilkesi oluşturma ekranı](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. Şablondan Yapılandır altındaki **erişim Ilkesi Ekle** bölümünde **(isteğe bağlı)** aşağı açılan menüden **gizli yönetim** ' i seçin.
1. **Sorumlu Seç**'i seçin ve arama alanına daha önce oluşturduğunuz VM'nin adını girin.  Sonuç listesinde VM 'yi seçin ve **Seç**' i seçin.
1. **Ekle**’yi seçin
1. **Kaydet**’i seçin.

## <a name="access-data"></a>Verilere erişme

Bu adımları tamamlamak bir SSH istemciniz olmalıdır.  Windows kullanıyorsanız, [Linux için Windows Alt Sistemi](/windows/wsl/about)'ndeki SSH istemcisini kullanabilirsiniz. SSSH istemcinizin anahtarlarını yapılandırmak için yardıma ihtiyacınız olursa, bkz. [Azure'da Windows ile SSH anahtarlarını kullanma](../../virtual-machines/linux/ssh-from-windows.md) veya [Azure’da Linux VM’ler için SSH ortak ve özel anahtar çifti oluşturma](../../virtual-machines/linux/mac-create-ssh-keys.md).

>[!IMPORTANT]
> Tüm Azure SDK 'Ları, Azure AD belirteçlerini hedef hizmetlere erişmek için kolayca almanızı sağlayan Azure. Identity kitaplığını destekler. [Azure SDK 'ları](https://azure.microsoft.com/downloads/) hakkında daha fazla bilgi edinin ve Azure. Identity kitaplığından yararlanın.
> - [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet)
> - [JAVA](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable)
> - [Javascript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest)
> - [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python)


1. Portalda Linux VM’nize gidin ve **Genel Bakış**’ta **Bağlan**’a tıklayın. 
2. Tercih ettiğiniz SSH istemciyle VM'ye **bağlanın**. 
3. Terminal penceresinde, Azure Key Vault erişim belirtecini almak için CURL'yi kullanarak Azure kaynaklarının yerel yönetilen kimliklerine bir istek gönderin.  
 
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
    
    Azure Key Vault’ta kimlik doğrulamak için bu erişim belirtecini kullanabilirsiniz.  Sonraki CURL isteği, CURL ve Key Vault REST API kullanarak Key Vault’tan nasıl gizli dizi okunacağını gösterir.  Key Vault'unuzun URL'sine ihtiyacınız olacaktır. Bu URL, Key Vault'un **Genel Bakış** sayfasındaki **Temel Parçalar** bölümünde yer alır.  Ayrıca önceki çağrıda aldığınız erişim belirtecine de ihtiyacınız olur. 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Yanıt şöyle görünür: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault'tan gizli diziyi aldıktan sonra, bunu kullanarak ad ve parola gerektiren bir hizmette kimlik doğrulaması yapabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynakları temizlemek istediğinizde, [Azure Portal](https://portal.azure.com)ziyaret edin, **kaynak grupları**' nı seçin, Bu öğreticinin işleminde oluşturulan kaynak grubunu bulun (gibi `mi-test` ) ve ardından **kaynak grubunu sil** komutunu kullanın.

Alternatif olarak bunu [PowerShell veya CLI](../../azure-resource-manager/management/delete-resource-group.md) aracılığıyla da yapabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Key Vault'a erişmek için Linux VM sistem tarafından atanan yönetilen kimlik kullanmayı öğrendiniz.  Azure Key Vault hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)