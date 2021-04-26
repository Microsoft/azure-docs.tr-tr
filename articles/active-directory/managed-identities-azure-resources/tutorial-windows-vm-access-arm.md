---
title: Öğretici `:` Azure Resource Manager erişmek için yönetilen kimlik kullanma-Windows-Azure AD
description: Windows VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure Resource Manager’a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4555baf658f720bc92e882e141b71f3b8050a1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093772"
---
# <a name="use-a-windows-vm-system-assigned-managed-identity-to-access-resource-manager"></a>Resource Manager’a erişmek için Windows VM sistem tarafından atanan yönetilen kimliği kullanma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, sistem tarafından atanan yönetilen kimlik etkin bir Windows sanal makinesi kullanarak Azure Resource Manager API 'sine nasıl erişebileceğiniz gösterilmektedir. Azure kaynaklarına yönelik yönetilen kimlikler Azure tarafından otomatik olarak yönetilir ve kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmanıza olanak tanır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"] 
> * Azure Resource Manager’da Kaynak Grubuna VM'niz için erişim verme 
> * VM kimliğini kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma

## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin temel olarak anlaşılmasıdır. Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](overview.md) inceleyin.
- Azure hesabı, [ücretsiz bir hesap için kaydolun](https://azure.microsoft.com/free/).
- Gerekli kaynak oluşturma ve rol yönetimi adımlarını gerçekleştirmek için uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "sahip" izinleri. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)konusuna bakın.
- Ayrıca, sistem tarafından atanmış Yönetilen kimlikler etkinleştirilmiş bir Windows sanal makinesine de ihtiyacınız vardır.
  - Bu öğretici için bir sanal makine oluşturmanız gerekiyorsa, [sistem tarafından atanan kimliğin etkin olduğu bir sanal makine oluşturma](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) başlıklı makaleyi izleyebilirsiniz.

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Resource Manager’da kaynak grubuna VM'niz için erişim verme

Azure kaynakları için yönetilen kimlikler kullanıldığında kodunuz Azure AD kimlik doğrulamasını destekleyen kaynaklarda kimlik doğrulaması yapmak için erişim belirteçleri alabilir.  Azure Resource Manager Azure AD kimlik doğrulamasını destekler.  Öncelikle bu VM’in sistem tarafından atanan yönetilen kimliğine Resource Manager’da bulunan bir kaynak için erişim izni vermemiz gerekir; bu durumda bu kaynak VM’nin içinde yer aldığı Kaynak Grubudur.  

1.  **Kaynak Grupları** sekmesine gidin. 
2.  **Windows VM**’niz için oluşturduğunuz **Kaynak Grubu**’nu seçin. 
3.  Sol paneldeki **Erişim denetimi (IAM)** öğesine gidin. 
4.  Ardından, **rol atamasını** **Windows VM**'niz için yeni bir rol ataması ekleyin.  **Rol** olarak **Okuyucu**'yu seçin. 
5.  Sonraki açılan kutuda, kaynak **sanal makinesine** **erişim atayın** . 
6.  Ardından, **Abonelik** açılan listesinde uygun aboneliğin listelendiğinden emin olun. **Kaynak Grubu** için de **Tüm kaynak grupları**'nı seçin. 
7.  Son olarak **Seç** altındaki açılan listeden Windows VM'nizi seçin ve **Kaydet**’e tıklayın.

    ![Alternatif resim metni](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>VM’nin sistem tarafından atanan yönetilen kimliğini kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma 

Bu bölümde **PowerShell** kullanmanız gerekecektir.  **PowerShell** yüklü değilse [buradan](/powershell/azure/) indirin. 

1.  Portalda, **Sanal Makineler**'e ve Windows sanal makinenize gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın. 
2.  Windows VM 'yi oluştururken eklediğiniz **Kullanıcı adınızı** ve **parolanızı** girin. 
3.  Sanal makineyle bir **Uzak Masaüstü bağlantısı** oluşturduğunuza göre, uzak oturumda **PowerShell** ' i açın. 
4.  Invoke-WebRequest cmdlet 'ini kullanarak, Azure Resource Manager için bir erişim belirteci almak üzere Azure kaynakları uç noktası için yerel yönetilen kimliğe bir istek yapın.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "Resource" parametre değeri Azure AD'nin beklediği değerle tam olarak eşleşmelidir. Azure Resource Manager kaynak kimliği kullanıldığında, URI'nin sonundaki eğik çizgiyi de eklemelisiniz.
    
    Ardından, $response nesnesinde JavaScript Nesne Gösterimi (JSON) biçimlendirilmiş dizesi olarak depolanan tam yanıtı ayıklayın. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ardından, yanıttan erişim belirtecini ayıklayın.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Son olarak erişim belirteci kullanarak Azure Resource Manager çağrısı yapın. Bu örnekte, Azure Resource Manager çağrısını yapmak için Invoke-WebRequest cmdlet 'ini de kullandık ve erişim belirtecini yetkilendirme üst bilgisine dahil ediyoruz.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > URL büyük/küçük harfe duyarlıdır; dolayısıyla daha önce Kaynak Grubunu adlandırırken kullandığınız büyük/küçük harf düzenini kullanmaya dikkat edin ("resourceGroups" adındaki büyük "G" harfine de dikkat edin).
        
    Aşağıdaki komut Kaynak Grubu ayrıntılarını döndürür:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Resource Manager API'sine erişmek amacıyla, sistem tarafından atanmış bir yönetilen kimliği nasıl kullanacağınızı öğrendiniz.  Azure Resource Manager hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)