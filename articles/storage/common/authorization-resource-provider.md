---
title: Yönetim kaynaklarına erişmek için Azure depolama kaynak sağlayıcısını kullanma
description: Azure depolama kaynak sağlayıcısı, Azure depolama için yönetim kaynaklarına erişim sağlayan bir hizmettir. Depolama hesapları, Özel uç noktaları ve hesap erişim anahtarları gibi kaynakları oluşturmak, güncelleştirmek, yönetmek ve silmek için Azure depolama kaynak sağlayıcısını kullanabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789684"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Yönetim kaynaklarına erişmek için Azure depolama kaynak sağlayıcısını kullanma

Azure Resource Manager, Azure için dağıtım ve yönetim hizmetidir. Azure depolama kaynak sağlayıcısı, Azure Resource Manager dayalı ve Azure Storage yönetim kaynaklarına erişim sağlayan bir hizmettir. Depolama hesapları, Özel uç noktaları ve hesap erişim anahtarları gibi kaynakları oluşturmak, güncelleştirmek, yönetmek ve silmek için Azure depolama kaynak sağlayıcısını kullanabilirsiniz. Azure Resource Manager hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/management/overview.md).

Bir depolama hesabı oluşturma veya silme veya bir abonelikteki depolama hesaplarının bir listesini alma gibi eylemleri gerçekleştirmek için Azure depolama kaynak sağlayıcısını kullanabilirsiniz. Azure depolama kaynak sağlayıcısı 'na karşı istekleri yetkilendirmek için Azure Active Directory (Azure AD) kullanın. Bu makalede, yönetim kaynaklarına izinlerin nasıl atanacağı açıklanır ve Azure depolama kaynak sağlayıcısı 'na karşı isteklerin nasıl yapılacağını gösteren örneklere işaret eder.

## <a name="management-resources-versus-data-resources"></a>Yönetim kaynakları ve veri kaynakları

Microsoft, Azure depolama kaynaklarıyla çalışmaya yönelik iki REST API sağlar. Bu API 'Ler, Azure depolama 'da gerçekleştirebileceğiniz tüm eylemlerin temelini oluşturur. Azure depolama REST API, depolama hesabınızda blob, kuyruk, dosya ve tablo verileri dahil olmak üzere verilerle çalışmanıza olanak sağlar. Azure depolama kaynak sağlayıcısı REST API, depolama hesabıyla ve ilgili kaynaklarla çalışmanıza olanak sağlar.

Blob verilerini okuyan veya yazan bir istek, yönetim işlemi gerçekleştiren bir istekten farklı izinler gerektirir. Azure RBAC, her iki türdeki kaynak için izinlerin üzerinde ayrıntılı denetim sağlar. Bir Azure rolünü bir güvenlik sorumlusuna atadığınızda, sorumlunun hangi izinlere sahip olacağını anladığınızdan emin olun. Her Azure yerleşik rolüyle hangi eylemlerin ilişkilendirildiğini açıklayan ayrıntılı bir başvuru için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

Azure depolama, istekleri blob ve kuyruk depolamaya göre yetkilendirmek için Azure AD 'nin kullanılmasını destekler. Blob ve kuyruk verileri işlemlerine yönelik Azure rolleri hakkında daha fazla bilgi için, bkz. [Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Azure rol tabanlı erişim denetimi (Azure RBAC) ile yönetim izinleri atama

Her Azure aboneliğinin kullanıcıları, grupları ve uygulamaları yöneten ilişkili bir Azure Active Directory vardır. Bir Kullanıcı, Grup veya uygulama [Microsoft Identity platform](../../active-directory/develop/index.yml)bağlamında güvenlik sorumlusu olarak da adlandırılır. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Active Directory tanımlı bir güvenlik sorumlusu için bir abonelikteki kaynaklara erişim izni verebilirsiniz.

Bir Azure rolünü bir güvenlik sorumlusuna atadığınızda, rol tarafından verilen izinlerin etkin olduğu kapsamı da belirtirsiniz. Yönetim işlemleri için abonelik, kaynak grubu veya depolama hesabı düzeyinde bir rol atayabilirsiniz. [Azure Portal](https://portal.azure.com/), [Azure CLI araçları](/cli/azure/install-classic-cli), [PowerShell](/powershell/azure/)veya [Azure depolama kaynak sağlayıcısı REST API](/rest/api/storagerp)kullanarak bir Azure rolünü güvenlik sorumlusuna atayabilirsiniz.

Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md) ve [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Yönetim işlemleri için yerleşik roller

Azure, arama yönetimi işlemlerine izin veren yerleşik roller sağlar. Azure depolama, Azure depolama kaynak sağlayıcısı ile birlikte kullanılmak üzere yerleşik roller de sağlar.

Depolama yönetimi işlemlerini çağırma izinleri veren yerleşik roller, aşağıdaki tabloda açıklanan rolleri içerir:

|    Azure rolü    |    Description    |    Hesap anahtarlarına erişim de dahil edilsin mi?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Sahibi** | , Tüm depolama kaynaklarını yönetebilir ve kaynaklara erişebilir.  | Evet, depolama hesabı anahtarlarını görüntülemek ve yeniden oluşturmak için izinler sağlar. |
| **Katkıda Bulunan**  | Tüm depolama kaynaklarını yönetebilir, ancak kaynaklara erişimi yönetemez. | Evet, depolama hesabı anahtarlarını görüntülemek ve yeniden oluşturmak için izinler sağlar. |
| **Okuyucu** | Depolama hesabı hakkında bilgi görüntüleyebilir, ancak hesap anahtarlarını görüntüleyemez. | Hayır. |
| **Depolama Hesabı Katılımcısı** | Depolama hesabını yönetebilir, aboneliğin kaynak grupları ve kaynakları hakkında bilgi alabilir ve abonelik kaynak grubu dağıtımlarını oluşturabilir ve yönetebilir. | Evet, depolama hesabı anahtarlarını görüntülemek ve yeniden oluşturmak için izinler sağlar. |
| **Kullanıcı Erişimi Yöneticisi** | , Depolama hesabına erişimi yönetebilir.   | Evet, bir güvenlik sorumlusunun kendilerine ve başkalarına herhangi bir izin atamasını sağlar. |
| **Sanal Makine Katılımcısı** | , Bağlı oldukları depolama hesabını değil, sanal makineleri yönetebilir.   | Evet, depolama hesabı anahtarlarını görüntülemek ve yeniden oluşturmak için izinler sağlar. |

Tablodaki üçüncü sütun, yerleşik rolün **Microsoft. Storage/storageAccounts/ListKeys/Action**' i destekleyip desteklemediğini gösterir. Bu eylem, depolama hesabı anahtarlarını okuma ve yeniden oluşturma izinleri verir. Azure depolama yönetimi kaynaklarına erişim izinleri, verilere erişim izinlerini de içermez. Ancak, bir kullanıcının hesap anahtarlarına erişimi varsa, paylaşılan anahtar yetkilendirmesi aracılığıyla Azure Storage verilerine erişmek için hesap anahtarlarını kullanabilirler.

### <a name="custom-roles-for-management-operations"></a>Yönetim işlemleri için özel roller

Azure, yönetim kaynaklarına erişim için Azure özel rollerinin tanımlanmasını da destekler. Özel roller hakkında daha fazla bilgi için bkz. [Azure özel rolleri](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Kod örnekleri

Azure depolama yönetim kitaplıklarından yönetim işlemlerini yetkilendirme ve çağırma işlemlerinin nasıl yapılacağını gösteren kod örnekleri için aşağıdaki örneklere bakın:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Klasik dağıtımlara karşı Azure Resource Manager

Resource Manager ve klasik dağıtım modelleri, Azure çözümlerinizi dağıtmanın ve yönetmenin iki farklı yolunu temsil eder. Yeni bir depolama hesabı oluşturduğunuzda, Microsoft Azure Resource Manager dağıtım modelinin kullanılmasını önerir. Mümkünse, Microsoft, Kaynak Yöneticisi modeliyle mevcut klasik depolama hesaplarını yeniden oluşturmanızı da önerir. Klasik dağıtım modelini kullanarak bir depolama hesabı oluşturabilseniz de, klasik model daha az esnektir ve sonunda kullanım dışı kalır.

Azure dağıtım modelleri hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi ve klasik dağıtım](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Resource Manager genel bakış](../../azure-resource-manager/management/overview.md)
- [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md)
- [Azure depolama kaynak sağlayıcısı için ölçeklenebilirlik hedefleri](scalability-targets-resource-provider.md)