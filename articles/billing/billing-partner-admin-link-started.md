---
title: Azure hesabını iş ortağı kimliğine bağlama | Microsoft Docs
description: İş ortağı kimliğini, müşterinin kaynaklarını yönetmek için kullandığınız kullanıcı hesabına bağlayarak Azure müşterileriyle etkileşimleri izleyin.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 10/01/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 00b44b62e5ce666a8cd6d4b02270e6d9de6897e8
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719823"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>İş ortağı kimliğini Azure hesaplarınıza bağlama

Microsoft iş ortakları, müşterilerin Microsoft ürünlerini kullanarak iş ve görev hedeflerini başarmasına yardımcı olan hizmetler sağlar. Azure hizmetlerini yöneten, yapılandıran ve destekleyen müşteri adına hareket ederken iş ortağı kullanıcılarının müşterinin ortamına erişmesi gerekir. İş ortakları, İş Ortağı Yönetici Bağlantısını kullanarak iş ortağı ağı kimliğini, hizmet teslimi için kullanılan kimlik bilgileriyle ilişkilendirebilir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Müşterinizden erişim elde etme

Siz iş ortağı kimliğinizi bağlamadan önce müşteriniz aşağıdaki seçeneklerden birini kullanarak Azure kaynaklarına erişmenizi sağlamalıdır:

- **Konuk kullanıcı**: Müşteriniz, sizi konuk kullanıcı olarak ekleyip rol tabanlı erişim denetimi (RBAC) rolleri atayabilir. Daha fazla bilgi için bkz. [Başka bir dizinden konuk kullanıcılar ekleme](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Dizin hesabı**: Müşteriniz, kendi dizininde sizin için bir kullanıcı hesabı oluşturabilir ve RBAC rolü atayabilir.

- **Hizmet sorumlusu**: Müşteriniz, kendi dizininde kuruluşunuzdan bir uygulama veya betik ekleyebilir ve RBAC rolü atayabilir. Uygulamanın veya betiğin kimliği, hizmet sorumlusu olarak bilinir.

## <a name="link-to-a-partner-id"></a>İş ortağı kimliğine bağlantı

Müşterinin kaynaklarına erişiminiz olduğunda, Microsoft İş Ortağı Ağı Kimliğinizi (MPN Kimliği) kullanıcı kimliğinize veya hizmet sorumlunuza bağlamak için Azure portalı, PowerShell ya da Azure CLI’yı kullanın. Her müşteri kiracısında iş ortağı kimliğini bağlayın.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Yeni bir iş ortağı kimliğine bağlantı için Azure portalını kullanma

1. Azure portalında [İş ortağı kimliğine bağlantı](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) bölümüne gidin.

2. Azure Portal’da oturum açın.

3. Microsoft iş ortağı kimliğini girin. İş ortağı kimliği, kuruluşunuzun [Microsoft İş Ortağı Ağı](https://partner.microsoft.com/) kimliğidir.

   ![İş ortağı kimliğine bağlantıyı gösteren ekran görüntüsü](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Başka bir müşterinin iş ortağı kimliğine bağlantı için dizini değiştirin. **Dizini değiştir** bölümünde dizininizi seçin.

   ![Dizini değiştir seçeneğini gösteren ekran görüntüsü](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Yeni bir iş ortağı kimliğine bağlanmak için PowerShell’i kullanma

1. [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell modülünü yükleyin.

2. Kullanıcı hesabı veya hizmet sorumlusu ile müşterinin kiracısında oturum açın. Daha fazla bilgi için bkz [PowerShell ile oturum açma](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Yeni iş ortağı kimliğine bağlantı oluşturun. İş ortağı kimliği, kuruluşunuzun [Microsoft İş Ortağı Ağı](https://partner.microsoft.com/) kimliğidir.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini alma
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini güncelleştirme
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini silme
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Yeni bir iş ortağı kimliğine bağlantı için Azure CLI’yı kullanma
1. Azure CLI uzantısını yükleyin.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Kullanıcı hesabı veya hizmet sorumlusu ile müşterinin kiracısında oturum açın. Daha fazla bilgi için bkz. [Azure CLI ile oturum açma](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Yeni iş ortağı kimliğine bağlantı oluşturun. İş ortağı kimliği, kuruluşunuzun [Microsoft İş Ortağı Ağı](https://partner.microsoft.com/) kimliğidir.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini alma
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini güncelleştirme
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Bağlı iş ortağı kimliğini silme
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Sonraki adımlar

Güncelleştirmeleri almak veya geri bildirim göndermek için [Microsoft İş Ortağı Topluluğu](https://aka.ms/PALdiscussion)’ndaki tartışmaya katılın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**İş ortağı kimliğini kim bağlayabilir?**

Müşterinin Azure kaynaklarını yöneten iş ortağı kuruluşundaki tüm kullanıcılar, iş ortağı kimliğini hesaba bağlayabilir.

**Bir iş ortağı kimliği bağlandıktan sonra değiştirilebilir mi?**

Evet. Bağlı bir iş ortağı kimliği değiştirilebilir, eklenebilir veya kaldırılabilir.

**Bir kullanıcının birden fazla müşteri kiracısında hesabı varsa ne olur?**

Her müşteri kiracısı için iş ortağı kimliği ve hesap arasında bağlantı oluşturulur. Her müşteri kiracısında iş ortağı kimliğini bağlayın.

**Diğer iş ortakları veya müşteriler, iş ortağı kimliğine bağlantıyı düzenleyebilir ya da kaldırabilir mi?**

Bağlantı, kullanıcı hesabı düzeyinde ilişkilendirilir. Yalnızca siz iş ortağı kimliğine bağlantıyı düzenleyebilir veya kaldırabilirsiniz. Müşteri ve diğer iş ortakları, iş ortağı kimliğine bağlantıyı değiştiremez.


**Şirketimin birden çok MPN kimliği varsa hangisini kullanmalıyım?**

İş ortağı kimliğini bağlamak için İş Ortağı Konumu Hesapları ve ilişkili MPN kimlikleri kullanılmalıdır.  [İş Ortağı Hesapları](https://docs.microsoft.com/partner-center/account-structure) hakkında daha fazla bilgi edinin

**Bağlı iş ortağı kimliği için etkilenen gelir raporlamasını nereden bulabilirim?**

Bulut Ürün Performansı raporlaması, İş Ortağı Merkezinde [İçgörülerim panosunda](https://partner.microsoft.com/membership/reports/myinsights) iş ortaklarının kullanımına sunulur. İş ortağı ilişkilendirme türü olarak İş Ortağı Yönetici Bağlantısını seçmeniz gerekir.

**Neden raporlarda müşterimi göremiyorum?**

Aşağıdaki nedenlerle raporlarda müşteriyi göremezsiniz

1. Bağlantılı kullanıcı hesabının herhangi bir müşteri Azure aboneliğinde veya kaynağında [Rol Tabanlı Erişimi](https://docs.microsoft.com/azure/role-based-access-control/overview) yoktur.

2. Kullanıcının [Rol Tabanlı Erişim](https://docs.microsoft.com/azure/role-based-access-control/overview) erişimine sahip olduğu Azure aboneliğinde herhangi bir kullanım yoktur.

**İş ortağı kimliği bağlantısı, Azure Stack ile çalışır mı?**

Evet, Azure Stack için iş ortağı kimliğinizi bağlayabilirsiniz.
