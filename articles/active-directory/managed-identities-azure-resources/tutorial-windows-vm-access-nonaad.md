---
title: 'Öğretici: Azure Key Vault erişmek için yönetilen bir kimlik kullanma-Windows-Azure AD'
description: Windows VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure Key Vault’a erişme işleminde size yol gösteren bir öğretici.
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
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ded2d327fd7c75633bf7ef6b7dd3041725c921
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378270"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Öğretici: Azure Key Vault'a erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, bir Windows sanal makinesinin (VM) [Azure Key Vault](../../key-vault/general/overview.md)erişmek için sistem tarafından atanan yönetilen kimliği nasıl kullanabileceği gösterilmektedir. Önyükleme görevi gören Key Vault, istemci uygulamanızın Azure Active Directory (AD) ile güvenliği olmayan kaynaklara erişmek için gizli dizi kullanmasını mümkün kılar. Yönetilen hizmet kimlikleri Azure tarafından otomatik olarak yönetilir ve kodunuzda kimlik doğrulama bilgilerini eklemeden Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması uygulamanızı sağlar.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM'nize Key Vault'ta depolanan gizli diziye erişim verme
> * VM kimliği kullanarak erişim belirteci alma ve Key Vault'tan gizli diziyi almak için bunu kullanma 

## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin anlaşılmasıdır. Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](overview.md) inceleyin. 
- Azure hesabı, [ücretsiz bir hesap için kaydolun](https://azure.microsoft.com/free/).
- Gerekli kaynak oluşturma ve rol yönetimi adımlarını gerçekleştirmek için uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "sahip" izinleri. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)konusuna bakın.
- Ayrıca, sistem tarafından atanmış Yönetilen kimlikler etkinleştirilmiş bir Windows sanal makinesine de ihtiyacınız vardır.
  - Bu öğretici için bir sanal makine oluşturmanız gerekiyorsa, [sistem tarafından atanan kimliğin etkin olduğu bir sanal makine oluşturma](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) başlıklı makaleyi izleyebilirsiniz.

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma  

Bu bölümde, VM 'nizin Key Vault depolanan bir gizli dizi erişimine nasıl izin vereceğiniz gösterilmektedir. Azure kaynakları için yönetilen kimlikler kullanıldığında kodunuz Azure AD kimlik doğrulamasını destekleyen kaynaklarda kimlik doğrulaması yapmak için erişim belirteçleri alabilir.Bununla birlikte, Azure hizmetlerinin tümü Azure AD kimlik doğrulamasını desteklemez. Söz konusu hizmetlerle Azure kaynakları için yönetilen kimlikleri kullanmak için, hizmet kimlik bilgilerini Azure Key Vault'ta depolayın ve VM’nin yönetilen kimliğini kullanarak Key Vault'a erişip kimlik bilgilerini alın.

İlk olarak, Key Vault'u oluşturmalı ve VM'mize Key Vault üzerinde sistem tarafından atanan yönetilen kimlik erişimi vermeliyiz.

1. Azure [portalını](https://portal.azure.com/) açın
1. Sol gezinti çubuğunun üst kısmında **kaynak oluştur** ' u seçin.  
1. Market 'te **Ara** kutusuna **Key Vault** yazın ve **ENTER** tuşuna basın.  
1. Sonuçlardan **Key Vault** seçin.
1. **Oluştur**’u seçin
1. Yeni Key Vault için bir **Ad** belirtin.

    ![Anahtar Kasası ekranı oluşturma](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

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

   ![Gizli anahtar oluşturma](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Erişim verme

Sanal makine tarafından kullanılan yönetilen kimliğe, Key Vault depolayabilmemiz için gizli dizi okumak üzere erişim verilmesi gerekir.

1. Yeni oluşturduğunuz Key Vault gidin
1. Sol taraftaki menüden **erişim ilkesi** ' ni seçin.
1. **Erişim Ilkesi Ekle** ' yi seçin

   ![Anahtar Kasası erişim ilkesi oluşturma ekranı](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. Şablondan Yapılandır altındaki **erişim Ilkesi Ekle** bölümünde **(isteğe bağlı)** aşağı açılan menüden **gizli yönetim** ' i seçin.
1. **Sorumlu Seç**'i seçin ve arama alanına daha önce oluşturduğunuz VM'nin adını girin.  Sonuç listesinde VM 'yi seçin ve **Seç**' i seçin.
1. **Ekle**’yi seçin
1. **Kaydet**’i seçin.


## <a name="access-data"></a>Verilere erişme  

Bu bölüm, VM kimliğini kullanarak bir erişim belirtecinin nasıl alınacağını ve Key Vault gizli anahtarı almak için nasıl kullanılacağını gösterir. PowerShell 4.3.1 veya üstünü yüklemediyseniz, [en son sürümü indirip yüklemeniz gerekir](/powershell/azure/).

İlk olarak, Key Vault'ta kimlik doğrulaması yapmak üzere erişim belirteci almak için VM’nin sistem tarafından atanan yönetilen kimliğini kullanırız:
 
1. Portalda, **Sanal Makineler**'e ve Windows sanal makinenize gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın.
2. **Windows VM'sini** oluştururken eklendiğiniz hesabın **Kullanıcı adı** ve **Parola** değerlerini girin.  
3. Artık sanal makineyle **Uzak Masaüstü Bağlantısı**'nı oluşturduğunuza göre, uzak oturumda PowerShell'i açın.  
4. PowerShell'de, VM için belirtilen bağlantı noktasında yerel konağın belirtecini almak üzere kiracıda web isteğini çağırın.  

PowerShell isteği:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Yanıtın aşağıdaki gibi göründüğünü görebilirsiniz:

![belirteç yanıtıyla istek](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Ardından, yanıttan erişim belirtecini ayıklayın.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Son olarak, PowerShell’in Invoke-WebRequest komutunu kullanarak Authorization üst bilgisindeki erişim belirtecini geçirerek Key Vault'ta daha önce oluşturmuş olduğunuz gizli bilgiyi alın.  Key Vault'unuzun URL'sine ihtiyacınız olacaktır. Bu URL, Key Vault'un **Genel Bakış** sayfasındaki **Temel Parçalar** bölümünde yer alır.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

Yanıt şöyle görünür: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Key Vault'tan gizli diziyi aldıktan sonra, bunu kullanarak ad ve parola gerektiren bir hizmette kimlik doğrulaması yapabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynakları temizlemek istediğinizde, [Azure Portal](https://portal.azure.com)ziyaret edin, **kaynak grupları**' nı seçin, Bu öğreticinin işleminde oluşturulan kaynak grubunu bulun (gibi `mi-test` ) ve ardından **kaynak grubunu sil** komutunu kullanın.

Alternatif olarak bunu [PowerShell veya CLI](../../azure-resource-manager/management/delete-resource-group.md) aracılığıyla da yapabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Key Vault erişmek için Windows VM sistem tarafından atanan bir yönetilen kimlik kullanmayı öğrendiniz.  Azure Key Vault hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)
