---
title: Azure kaynakları için yönetilen kimlikler
description: Azure kaynakları için yönetilen kimliklere genel bakış.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/07/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fabb8bbdb42212dffd3781f4e98204abb518e6b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105587"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler nelerdir?

Geliştiriciler için ortak bir zorluk, bir çözüm oluşturan farklı bileşenler arasındaki iletişimin güvenliğini sağlamak için kullanılan gizli dizi ve kimlik bilgilerinin Yönetisidir. Yönetilen kimlikler, geliştiricilerin kimlik bilgilerini yönetme gereksinimini ortadan kaldırır. Yönetilen kimlikler, uygulamalar için Azure Active Directory (Azure AD) kimlik doğrulamasını destekleyen kaynaklara bağlanırken kullanılacak bir kimlik sağlar. Uygulamalar, Azure AD belirteçlerini almak için yönetilen kimliği kullanabilir. Örneğin, bir uygulama, geliştiricilerin kimlik bilgilerini güvenli bir şekilde depolayabileceği veya depolama hesaplarına erişebileceği [Azure Key Vault](../../key-vault/general/overview.md) gibi kaynaklara erişmek için yönetilen bir kimlik kullanabilir.

Yönetilen bir kimlik ne için kullanılabilir?</br>

> [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Yönetilen kimlikler kullanmanın avantajlarından bazıları şunlardır:

- Kimlik bilgilerini yönetmeniz gerekmez. Kimlik bilgilerine, sizin için de erişilebilir.
- Kendi uygulamalarınız dahil [Azure Active Directory kimlik doğrulamasını](../authentication/overview-authentication.md) destekleyen herhangi bir kaynak için kimlik doğrulaması yapmak üzere yönetilen kimlikler ' i kullanabilirsiniz.
- Yönetilen kimlikler herhangi bir ek maliyet olmadan kullanılabilir.

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="managed-identity-types"></a>Yönetilen kimlik türleri

İki tür yönetilen kimlik vardır:

- **Sistem tarafından atanan** Bazı Azure Hizmetleri, yönetilen bir kimliği doğrudan bir hizmet örneği üzerinde etkinleştirmenizi sağlar. Sistem tarafından atanan bir yönetilen kimliği etkinleştirdiğinizde, Azure AD 'de bu hizmet örneğinin yaşam döngüsüne bağlı bir kimlik oluşturulur. Bu nedenle, kaynak silindiğinde Azure kimliği sizin için otomatik olarak siler. Tasarım yaparak, yalnızca bu Azure kaynağı Azure AD 'den belirteç istemek için bu kimliği kullanabilir.
- **Kullanıcı tarafından atanan** Ayrıca, tek başına Azure kaynağı olarak yönetilen bir kimlik de oluşturabilirsiniz. [Kullanıcı tarafından atanan bir yönetilen kimlik oluşturabilir](how-to-manage-ua-identity-portal.md) ve bunu bir Azure hizmetinin bir veya daha fazla örneğine atayabilirsiniz. Kullanıcı tarafından atanan Yönetilen kimlikler söz konusu olduğunda, kimlik onu kullanan kaynaklardan ayrı olarak yönetilir. </br></br>

> [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

Aşağıdaki tabloda, iki tür yönetilen kimliğin arasındaki farklar gösterilmektedir.

|  Özellik    | Sistem tarafından atanan yönetilen kimlik | Kullanıcı tarafından atanan yönetilen kimlik |
|------|----------------------------------|--------------------------------|
| Oluşturma |  Bir Azure kaynağının parçası olarak oluşturulur (örneğin, bir Azure sanal makinesi veya Azure App Service) | Tek başına Azure kaynağı olarak oluşturulur |
| Yaşam döngüsü | Yönetilen kimliğin birlikte oluşturulduğu Azure kaynağıyla paylaşılan yaşam döngüsü. <br/> Üst kaynak silindiğinde, yönetilen kimlik de silinir. | Bağımsız yaşam döngüsü. <br/> Açıkça silinmelidir. |
| Azure kaynakları arasında paylaşım | Paylaştırılamaz. <br/> Yalnızca tek bir Azure kaynağıyla ilişkilendirilebilir. | Paylaşılabilir <br/> Aynı kullanıcı tarafından atanan yönetilen kimlik birden fazla Azure kaynağıyla ilişkilendirilebilir. |
| Genel kullanım örnekleri | Tek bir Azure kaynağı içinde yer alan iş yükleri <br/> Bağımsız kimliklere ihtiyacınız olan iş yükleri. <br/> Örneğin, tek bir sanal makinede çalışan bir uygulama | Birden çok kaynak üzerinde çalışan ve tek bir kimliği paylaşabilen iş yükleri. <br/> Sağlama akışının bir parçası olarak güvenli bir kaynağa ön kimlik doğrulaması gerektiren iş yükleri. <br/> Kaynakların sık geri dönüştürüleceği iş yükleri, ancak izinlerin tutarlı kalması gerekir. <br/> Örneğin, birden çok sanal makinenin aynı kaynağa erişmesi gereken bir iş yükü |

> [!IMPORTANT]
> Bir yönetilen kimliği seçtiğiniz kimlik türünden bağımsız olarak, yalnızca Azure kaynaklarıyla kullanılabilecek özel bir türün hizmet sorumlusu olur. Yönetilen kimlik silindiğinde, buna karşılık gelen hizmet sorumlusu otomatik olarak kaldırılır.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri nasıl kullanabilirim?

![bir geliştiricinin kimlik doğrulama bilgilerini yönetmeksizin koddan kaynaklara erişim sağlamak için yönetilen kimlikleri nasıl kullanabileceği hakkında bazı örnekler](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Bu özelliği hangi Azure hizmetleri destekliyor?<a name="which-azure-services-support-managed-identity"></a>

Azure kaynakları için yönetilen kimlikler, Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmak için kullanılabilir. Azure kaynakları için yönetilen kimlikler özelliğini destekleyen Azure hizmetlerinin listesi için bkz. [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](./services-support-managed-identities.md).

## <a name="which-operations-can-i-perform-using-managed-identities"></a>Yönetilen kimlikleri kullanarak hangi işlemleri gerçekleştirebilirim?

Sistem tarafından atanan yönetilen kimlikleri destekleyen kaynaklar şunları yapmanıza olanak sağlar:

- Kaynak düzeyinde yönetilen kimlikleri etkinleştirin veya devre dışı bırakın.
- [İzinleri vermek](howto-assign-access-portal.md)için RBAC rollerini kullanın.
- [Azure etkinlik günlüklerinde](../../azure-resource-manager/management/view-activity-logs.md)oluşturma, okuma, güncelleştirme, SILME (CRUD) işlemlerini görüntüleyin.
- Azure AD [oturum açma günlüklerinde](../reports-monitoring/concept-sign-ins.md)oturum açma etkinliğini görüntüleyin.

Bunun yerine, bir kullanıcı tarafından atanmış yönetilen kimliği seçerseniz:

- Kimlikleri [oluşturabilir, okuyabilir, güncelleştirebilir, silebilirsiniz](how-to-manage-ua-identity-portal.md) .
- [İzinleri vermek](howto-assign-access-portal.md)için RBAC rol atamalarını kullanabilirsiniz.
- Kullanıcı tarafından atanan Yönetilen kimlikler, birden fazla kaynakta kullanılabilir.
- [Azure etkinlik günlüklerinde](../../azure-resource-manager/management/view-activity-logs.md)CRUD işlemleri gözden geçirilmek üzere kullanılabilir.
- Azure AD [oturum açma günlüklerinde](../reports-monitoring/concept-sign-ins.md)oturum açma etkinliğini görüntüleyin.

Yönetilen kimliklerdeki işlemler Azure Resource Manager (ARM) şablonu, Azure portalı, Azure CLı, PowerShell ve REST API 'Leri kullanılarak gerçekleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Resource Manager’a erişmek için Windows VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-windows-vm-access-arm.md)
* [Resource Manager’a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-linux-vm-access-arm.md)
* [App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](../../app-service/overview-managed-identity.md)
* [Azure Container Instances ile yönetilen kimlikleri kullanma](../../container-instances/container-instances-managed-identity.md)
* [Microsoft Azure kaynakları Için Yönetilen kimlikler uygulama](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).
