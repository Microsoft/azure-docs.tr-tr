---
title: Azure kaynakları için Yönetilen kimlikler Azure sanal makineleri ile nasıl çalışır?
description: Azure kaynakları için yönetilen kimliklerin açıklaması Azure sanal makinelerle çalışır.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc8f89720dddbee3f69cb5d933268edb17acfd2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055629"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Azure kaynakları için Yönetilen kimlikler Azure sanal makineleri ile nasıl çalışır?

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.

Bu makalede, yönetilen kimliklerin Azure sanal makineleri (VM 'Ler) ile nasıl çalıştığı hakkında bilgi edineceksiniz.


## <a name="how-it-works"></a>Nasıl çalışır?

Dahili olarak, Yönetilen kimlikler yalnızca Azure kaynaklarıyla kullanılabilen özel bir türün hizmet ilkeleridir. Yönetilen kimlik silindiğinde, buna karşılık gelen hizmet sorumlusu otomatik olarak kaldırılır.
Ayrıca, bir User-Assigned veya System-Assigned kimliği oluşturulduğunda, yönetilen kimlik kaynak sağlayıcısı (MSRP) bir sertifikayı bu kimlik ile dahili olarak yayınlar. 

Kodunuzda Azure AD kimlik doğrulamasını destekleyen hizmetler için erişim belirteci istemek üzere yönetilen kimlik kullanılabilir. Hizmet örneği tarafından kullanılan kimlik bilgilerinin dağıtımıyla Azure ilgilenir. 

Aşağıdaki diyagramda yönetilen hizmet kimliklerinin Azure sanal makineleriyle (VM) nasıl çalıştığı gösterilmektedir:

![Yönetilen hizmet kimlikleri ve Azure VM’leri](media/how-managed-identities-work-vm/data-flow.png)

|  Özellik    | Sistem tarafından atanan yönetilen kimlik | Kullanıcı tarafından atanan yönetilen kimlik |
|------|----------------------------------|--------------------------------|
| Oluşturma |  Bir Azure kaynağının parçası olarak oluşturulur (örneğin, bir Azure sanal makinesi veya Azure App Service). | Tek başına bir Azure kaynağı olarak oluşturulur. |
| Yaşam döngüsü | Yönetilen kimliğin birlikte oluşturulduğu Azure kaynağıyla paylaşılan yaşam döngüsü. <br/> Üst kaynak silindiğinde, yönetilen kimlik de silinir. | Bağımsız yaşam döngüsü. <br/> Açıkça silinmelidir. |
| Azure kaynakları arasında paylaşım | Paylaştırılamaz. <br/> Yalnızca tek bir Azure kaynağıyla ilişkilendirilebilir. | Paylaşılabilir. <br/> Aynı kullanıcı tarafından atanan yönetilen kimlik birden fazla Azure kaynağıyla ilişkilendirilebilir. |
| Genel kullanım örnekleri | Tek bir Azure kaynağı içinde yer alan iş yükleri. <br/> Bağımsız kimliklere ihtiyacınız olan iş yükleri. <br/> Örneğin, tek bir sanal makinede çalışan bir uygulama | Birden çok kaynak üzerinde çalışan ve tek bir kimliği paylaşabilen iş yükleri. <br/> Sağlama akışının bir parçası olarak güvenli bir kaynağa ön kimlik doğrulaması gerektiren iş yükleri. <br/> Kaynakların sık geri dönüştürüleceği iş yükleri, ancak izinlerin tutarlı kalması gerekir. <br/> Örneğin, birden çok sanal makinenin aynı kaynağa erişmesi gereken bir iş yükü |

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

1. Azure Resource Manager sanal makinede sistem tarafından atanan yönetilen kimliği etkinleştirmek için bir istek alır.

2. Azure Resource Manager, Azure AD'de sanal makinenin kimliği için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu, bu abonelik tarafından güvenilen Azure AD kiracısında oluşturulur.

3. Azure Resource Manager, Azure Instance Metadata Service Identity uç noktasını hizmet sorumlusu istemci KIMLIĞI ve sertifikasıyla güncelleştirerek VM 'deki kimliği yapılandırır.

4. VM bir kimliğe sahip olduktan sonra hizmet sorumlusunu kullanarak Azure kaynaklarına VM erişimi sağlayabilirsiniz. Azure Resource Manager çağırmak için, VM hizmet sorumlusuna uygun rolü atamak üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın. Key Vault'u çağırmak için kodunuza Key Vault'ta belirli bir gizli diziye veya anahtara erişim verebilirsiniz.

5. VM üzerinde çalışan kodunuz Azure örnek meta veri hizmeti uç noktasından yalnızca VM 'nin içinden erişilebilen bir belirteç isteyebilir: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resource parametresi belirtecin gönderildiği hizmeti belirtir. Azure Resource Manager ile kimlik doğrulaması için `resource=https://management.azure.com/` kullanın.
    - API version parametresi IMDS sürümünü belirtir; api-version=2018-02-01 veya üstünü kullanın.

6. Azure AD'ye erişim belirteci isteyen bir çağrı yapılır (5. adımda belirtildiği gibi) ve bu çağrıda 3. adımda yapılandırılan istemci kimliği ve sertifikası kullanılır. Azure AD bir JSON Web Token (JWT) erişim belirteci döndürür.

7. Kodunuz erişim belirtecini bir çağrıda Azure AD kimlik doğrulamasını destekleyen hizmete gönderir.

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

1. Azure Resource Manager kullanıcı tarafından atanan bir yönetilen kimlik oluşturma isteği alır.

2. Azure Resource Manager, Azure AD'de kullanıcı tarafından atanan yönetilen kimlik için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu, bu abonelik tarafından güvenilen Azure AD kiracısında oluşturulur.

3. Azure Resource Manager, Kullanıcı tarafından atanan yönetilen kimliği bir VM üzerinde yapılandırma ve Kullanıcı tarafından atanan yönetilen kimlik hizmet sorumlusu istemci KIMLIĞI ve sertifikasıyla Azure Instance Metadata Service Identity uç noktasını güncelleştirme isteği alır.

4. Kullanıcı tarafından atanan yönetilen kimlik oluşturulduktan sonra hizmet sorumlusu bilgilerini kullanarak Azure kaynaklarına erişim verir. Azure Resource Manager çağırmak için, Kullanıcı tarafından atanan kimliğin hizmet sorumlusuna uygun rolü atamak üzere Azure RBAC kullanın. Key Vault'u çağırmak için kodunuza Key Vault'ta belirli bir gizli diziye veya anahtara erişim verebilirsiniz.

   > [!Note]
   > Bu adımı 3. adımdan önce de gerçekleştirebilirsiniz.

5. VM üzerinde çalışan kodunuz Azure Instance Metadata Service Identity uç noktasından yalnızca VM içinden erişilebilen bir belirteç isteyebilir: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resource parametresi belirtecin gönderildiği hizmeti belirtir. Azure Resource Manager ile kimlik doğrulaması için `resource=https://management.azure.com/` kullanın.
    - Client ID parametresi belirtecin hangi kimlik için istendiğini belirtir. Tek bir sanal makinede birden çok kullanıcı tarafından atanan kimlik olduğunda, belirsizliği ortadan kaldırmak için bu değer gereklidir.
    - API version parametresi, Azure Instance Metadata Service sürümünü belirtir. `api-version=2018-02-01` veya daha yüksek bir değer kullanın.

6. Azure AD'ye erişim belirteci isteyen bir çağrı yapılır (5. adımda belirtildiği gibi) ve bu çağrıda 3. adımda yapılandırılan istemci kimliği ve sertifikası kullanılır. Azure AD bir JSON Web Token (JWT) erişim belirteci döndürür.
7. Kodunuz erişim belirtecini bir çağrıda Azure AD kimlik doğrulamasını destekleyen hizmete gönderir.


## <a name="next-steps"></a>Sonraki adımlar

Azure kaynakları için yönetilen kimlikler özelliğini kullanmaya başlamak için aşağıdaki hızlı başlangıçlardan yararlanın:

* [Resource Manager’a erişmek için Windows VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-windows-vm-access-arm.md)
* [Resource Manager’a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-linux-vm-access-arm.md)
