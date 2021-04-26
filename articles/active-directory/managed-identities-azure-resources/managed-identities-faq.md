---
title: Azure kaynakları için Yönetilen kimlikler sık sorulan sorular-Azure AD "
description: Yönetilen kimlikler hakkında sık sorulan sorular
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534115"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Azure kaynakları için Yönetilen kimlikler sık sorulan sorular-Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="administration"></a>Yönetim

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Yönetilen bir kimliğe sahip kaynakları nasıl bulabilirsiniz?

Aşağıdaki Azure CLı komutunu kullanarak, sistem tarafından atanan yönetilen kimliğe sahip kaynakların listesini bulabilirsiniz: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Bir kaynaktaki yönetilen kimlik için hangi Azure RBAC izinleri gerekir? 

- Sistem tarafından atanan yönetilen kimlik: kaynak üzerinde yazma izinlerine sahip olmanız gerekir. Örneğin sanal makineler için Microsoft.Compute/virtualMachines/write iznine ihtiyaç duyulur. Bu eylem, [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)gibi kaynağa özgü yerleşik rollere dahildir.
- Kullanıcı tarafından atanan yönetilen kimlik: kaynak üzerinde yazma izinlerine sahip olmanız gerekir. Örneğin sanal makineler için Microsoft.Compute/virtualMachines/write iznine ihtiyaç duyulur. Yönetilen kimliğe göre [yönetilen kimlik operatörü](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rolü atamaya ek olarak.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Nasıl yaparım? Kullanıcı tarafından atanan yönetilen kimliklerin oluşturulmasını engelliyor musunuz?

[Azure ilkesini](../../governance/policy/overview.md) kullanarak kullanıcılarınızın Kullanıcı tarafından atanan Yönetilen kimlikler oluşturmasını koruyabilirsiniz

1. [Azure Portal](https://portal.azure.com) gidin ve **ilkeye** gidin.
2. **Tanımları** seçin
3. **+ İlke tanımı** ' nı seçin ve gerekli bilgileri girin.
4. İlke kuralı bölümünde yapıştırma
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

İlkeyi oluşturduktan sonra, kullanmak istediğiniz kaynak grubuna atayın.

1. Kaynak grupları ' na gidin.
2. Test için kullandığınız kaynak grubunu bulun.
3. Sol menüden **ilkeler** ' i seçin.
4. **Ilke ata** ' yı seçin
5. **Temel bilgiler** bölümünde şunları belirtin:
    1. **Kapsam** Test için kullandığımız kaynak grubu
    1. **İlke tanımı**: daha önce oluşturduğumuz ilke.
6. Tüm diğer ayarları varsayılan olarak bırakın ve **gözden geçir + oluştur** seçeneğini belirleyin.

Bu noktada, kaynak grubunda Kullanıcı tarafından atanan yönetilen kimlik oluşturma girişimleri başarısız olur.

  ![İlke ihlali](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Kavramlar

### <a name="do-managed-identities-have-a-backing-app-object"></a>Yönetilen kimliklerin bir yedekleme uygulaması nesnesi var mı?

Hayır. Yönetilen kimlikler ve Azure AD Uygulaması kayıtları, dizinde aynı şey değildir.

Uygulama kayıtları iki bileşeni vardır: uygulama nesnesi + bir hizmet sorumlusu nesnesi.
Azure kaynakları için Yönetilen kimlikler şu bileşenlerden yalnızca birine sahiptir: bir hizmet sorumlusu nesnesi.

Yönetilen kimliklerin dizinde uygulama nesnesi yoktur, bu da genellikle MS Graph için uygulama izinleri vermek için kullanılır. Bunun yerine, Yönetilen kimlikler için MS Graph izinlerinin doğrudan hizmet sorumlusuna verilmesi gerekir.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Yönetilen bir kimlikle ilişkili kimlik bilgileri nedir? Ne kadar geçerlidir ve ne sıklıkta döndürülür?

> [!NOTE]
> Yönetilen kimliklerin kimlik doğrulaması, bildirimde bulunulmadan değişikliğe tabi olan dahili bir uygulama ayrıntısıyla yapılır.

Yönetilen kimlikler sertifika tabanlı kimlik doğrulaması kullanır. Her yönetilen kimliğin kimlik bilgileri 90 gün süre sonu içerir ve 45 gün sonra alınır.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>İstekte kimliği belirtmezseniz hangi kimlik varsayılan olarak silinecek?

- Sistem tarafından atanan yönetilen kimlik etkinse ve istekte hiçbir kimlik belirtilmemişse, ıDS, sistem tarafından atanan yönetilen kimliğe varsayılan olarak ayarlanır.
- Sistem tarafından atanan yönetilen kimlik etkin değilse ve yalnızca bir Kullanıcı yönetilen kimliği varsa, ıDS varsayılan olarak bu tek kullanıcı tarafından atanan yönetilen kimliği alır.
- Sistem tarafından atanan yönetilen kimlik etkinleştirilmemişse ve birden çok kullanıcı atanmış Yönetilen kimlikler varsa, istekte bir yönetilen kimlik belirtmeniz gerekir.

## <a name="limitations"></a>Sınırlamalar

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Aynı yönetilen kimlik birden çok bölgede kullanılabilir mi?

Kısaca Evet olarak, birden fazla Azure bölgesinde Kullanıcı tarafından atanan Yönetilen kimlikler kullanabilirsiniz. Daha uzun yanıt, Kullanıcı tarafından atanan Yönetilen kimlikler, Azure AD 'de oluşturulan ilişkili [hizmet sorumlusu](../develop/app-objects-and-service-principals.md#service-principal-object) (SP), genel olarak kullanılabilir. Hizmet sorumlusu herhangi bir Azure bölgesinden kullanılabilir ve kullanılabilirliği Azure AD 'nin kullanılabilirliğine bağlıdır. Örneğin, South-Central bölgesinde bir kullanıcı tarafından atanan yönetilen kimlik oluşturduysanız ve bu bölge kullanılamaz duruma gelirse bu sorun yalnızca yönetilen kimliğin üzerinde [Denetim düzlemi](../../azure-resource-manager/management/control-plane-and-data-plane.md) etkinliklerini etkiler.  Yönetilen kimlikleri kullanmak için önceden yapılandırılmış kaynaklar tarafından gerçekleştirilen etkinlikler bundan etkilenmez.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure kaynakları için Yönetilen kimlikler Azure Cloud Services ile çalışır mi?

Hayır, Azure Cloud Services Azure kaynakları için yönetilen kimlikleri desteklemeye yönelik bir plan yoktur.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimliklerin güvenlik sınırı nedir?

Kimliğin güvenlik sınırı, eklendiği kaynaktır. Örneğin, Azure kaynakları için yönetilen kimliklere sahip bir sanal makine için güvenlik sınırı, sanal makinedir. Bu VM üzerinde çalışan herhangi bir kod, Azure kaynakları uç noktası ve istek belirteçleri için yönetilen kimlikleri çağırabilir. Azure kaynakları için yönetilen kimlikleri destekleyen diğer kaynaklarla benzer deneyimdir.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Bir aboneliği başka bir dizine taşıdığımda, Yönetilen kimlikler otomatik olarak yeniden oluşturulur mi?

Hayır. Bir aboneliği başka bir dizine taşırsanız, bunları el ile yeniden oluşturmanız ve Azure rol atamalarını yeniden sağlamanız gerekir.
- Sistem tarafından atanan Yönetilen kimlikler için: devre dışı bırakın ve yeniden etkinleştirin. 
- Kullanıcı tarafından atanan Yönetilen kimlikler için: silin, yeniden oluşturun ve bunları gereken kaynaklara yeniden ekleyin (örneğin, sanal makineler)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Farklı bir dizin/Kiracıdaki bir kaynağa erişmek için yönetilen bir kimlik kullanabilir miyim?

Hayır. Yönetilen kimlikler Şu anda çapraz dizin senaryolarını desteklemez. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Yönetilen kimlikler için uygulanan herhangi bir hız sınırı var mı?

Yönetilen kimlikler sınırları, Azure hizmet limitleri, Azure Instance Metadata Service (IMDS) sınırları ve hizmet sınırlarına Azure Active Directory bağımlılıkları vardır.

- **Azure hizmet limitleri** , kiracı ve abonelik düzeylerinde gerçekleştirilebilecek oluşturma işlemlerinin sayısını tanımlar. Kullanıcı tarafından atanan Yönetilen kimlikler Ayrıca adlandırılmaları konusunda [sınırlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) da vardır.
- **IDS 'ler** Genel olarak, ıDS istekleri saniyede beş istek ile sınırlıdır. Bu eşiği aşan istekler 429 yanıt olarak reddedilir. Yönetilen kimlik kategorisine yönelik istekler saniyede 20 istek ve 5 eşzamanlı istek ile sınırlıdır. [Azure Instance Metadata Service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) makalesinde daha fazla bilgi edinebilirsiniz.
- **Azure Active Directory hizmeti** Her yönetilen kimlik, Azure [ad hizmeti sınırları ve kısıtlamaları](../enterprise-users/directory-service-limits-restrictions.md)bölümünde açıklandığı gibi BIR Azure AD kiracısındaki nesne kotası sınırına doğru sayılır.


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Kullanıcı tarafından atanan bir yönetilen kimliği farklı bir kaynak grubuna/aboneliğe taşımak mümkün mü?

Kullanıcı tarafından atanan bir yönetilen kimliğin farklı bir kaynak grubuna taşınması desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen kimliklerin sanal makinelerle nasıl](how-managed-identities-work-vm.md) çalıştığını öğrenin
