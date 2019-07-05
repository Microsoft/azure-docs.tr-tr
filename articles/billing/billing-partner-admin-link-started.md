---
title: Azure hesaplarınızı bir iş ortağı kimliği ile bağla | Microsoft Docs
description: Müşteri'nin kaynakları yönetmek için kullandığınız kullanıcı hesabı için iş ortağı kimliği bağlayarak katılımlar Azure müşteri ile izleyebilirsiniz.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a5b6bb683538bd5359ebcbbe2640216e574b3e00
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/14/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Azure hesaplarınıza bağlantı iş ortağı kimliği

İş ortağı olarak, müşterinin kaynakları yönetmek için kullanılan hesaplar için iş ortağı Kimliğinizi bağlayarak, etkisi, müşteri katılımlar izleyebilirsiniz.

Bu özellik, genel önizleme olarak kullanılabilir.

## <a name="get-access-from-your-customer"></a>Müşteriden erişin

İş ortağı Kimliğinizi bağlamadan önce müşterinizin Azure kaynaklarını aşağıdaki seçeneklerden birini kullanarak erişmenizi gerekir:

- **Konuk kullanıcı:** müşteri Konuk kullanıcı olarak ekleyin ve herhangi bir RBAC rolü atayın. Daha fazla bilgi için bkz: [başka bir dizinden Konuk kullanıcılar eklemek](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Dizin hesabı:** müşteri directory'lerinde kuruluşunuzdaki yeni bir kullanıcı oluşturun ve herhangi bir RBAC rolü atayın.

- **Hizmet sorumlusu:** müşteri kuruluşunuzdaki directory'lerinde bir uygulama ya da komut dosyasını ekleyin ve herhangi bir RBAC rolü atayın. Uygulama veya betik kimliğini hizmet sorumlusu bilinir.

## <a name="link-partner-id"></a>Bağlantı iş ortağı kimliği

Müşteri'nin kaynaklarına erişimi varsa, Azure portal, PowerShell veya CLI, Microsoft iş ortağı ağ kimliği (MPN kimliği) bağlamak için kullanıcı kimliği veya hizmet sorumlusu kullanın. Her bir müşterinin Kiracı İş ortağı kimliği bağlamak zorunda.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Yeni iş ortağı kimliği bağlamak için Azure portalını kullanma

1. Git [ilişkilendirilecek bir iş ortağı kimliği](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) Azure portalında.

2. Azure Portal’da oturum açın.

3. Microsoft iş ortağı kimliğini girin. Kimliktir iş ortağı [Microsoft iş ortağı Network(MPN)](https://partner.microsoft.com/) , kuruluşunuzun kimliği.

  ![Bağlantı iş ortağı kimliği gösteren ekran görüntüsü](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Başka bir müşteri için iş ortağı kimliği bağlamak için dizin değiştiricisini kullanın. Anahtar dizini altında dizininizi seçin.

  ![Bağlantı iş ortağı kimliği gösteren ekran görüntüsü](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Yeni iş ortağı kimliği bağlamak için PowerShell kullanın

1. Yükleme [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) PowerShell modülü.

2. Oturum açtığınızda müşterinin Kiracı, kullanıcı hesabı veya hizmet sorumlusu, daha fazla bilgi için bkz: [Powershell ile oturum açma](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Yeni iş ortağı kimliğini bağlantı Kimliktir iş ortağı [Microsoft iş ortağı Network(MPN)](https://partner.microsoft.com/) , kuruluşunuzun kimliği.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini Al
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini güncelleştir
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini Sil
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Yeni iş ortağı kimliği bağlamak için CLI kullanın
1.  CLI uzantısını yükleyin.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Müşteri'nin Kiracı hizmet sorumlusu ve kullanıcı hesabı ile oturum açın. Daha fazla bilgi için bkz: [oturum Azure CLI 2.0 oturum](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Yeni iş ortağı kimliğini bağlantı Kimliktir iş ortağı [Microsoft iş ortağı Network(MPN)](https://partner.microsoft.com/) , kuruluşunuzun kimliği.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini Al
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini güncelleştir
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Bağlantılı iş ortağı Kimliğini Sil
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Sonraki adımlar

Tartışma katılın [Microsoft iş ortağı topluluk](https://aka.ms/PALdiscussion) güncelleştirmeleri almak ya da geri bildirim göndermek için.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

**Kimin iş ortağı Kimliğini bağlayabilir miyim?**

Müşteri'nin kaynak yöneten iş ortağı kuruluştan herhangi bir kullanıcı hesabına iş ortağı Kimliğini bağlayabilirsiniz.

**Bir iş ortağı kimliği bağlı sonra değiştirilebilir mi?**

Evet, bağlı iş ortağı kimliği değişti, eklenen veya kaldırılabilir.

**Ne bir kullanıcı bir hesabı birden çok müşteri kiracılar var?**

İş ortağı Kimliğini ve hesap arasındaki bağlantıyı, her bir müşterinin Kiracı için yapılır.  Her bir müşterinin Kiracı İş ortağı kimliği bağlamak zorunda.

**Diğer ortak veya müşteri düzenleyebilir veya iş ortağı Kimliğini bağlantısını kaldırmak?**

Bağlantı hesap düzeyinde ilişkili değil. Yalnızca Düzenle veya iş ortağı kimliğini bağlantısını Kaldır Müşteri ve diğer iş ortağı bağlantı iş ortağı kimliğini değiştirilemiyor 
