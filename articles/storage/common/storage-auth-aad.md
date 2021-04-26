---
title: Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme
titleSuffix: Azure Storage
description: Azure Active Directory (Azure AD) kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verin. Erişim hakları için Azure rolleri atayın. Verileri bir Azure AD hesabıyla erişin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94635407"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme

Azure depolama, istekleri blob ve kuyruk depolamaya yetkilendirmek için Azure Active Directory (Azure AD) kullanılmasını destekler. Azure AD ile, bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izinler vermek üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz. Güvenlik sorumlusunun bir OAuth 2,0 belirteci döndürmesi için Azure AD tarafından kimliği doğrulanır. Belirteç daha sonra blob veya kuyruk depolamaya karşı bir isteği yetkilendirmek için kullanılabilir.

Azure AD ile Azure depolama 'da istekleri yetkilendirmek, paylaşılan anahtar yetkilendirmesi üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Microsoft, paylaşılan anahtardaki olası güvenlik açıklarını en aza indirmek mümkün olduğunda blob ve kuyruk uygulamalarınızla Azure AD yetkilendirmesi kullanılmasını önerir.

Azure AD ile yetkilendirme, tüm genel bölgelerde ve ulusal bulutlarda tüm genel amaçlı ve BLOB depolama hesapları için kullanılabilir. Yalnızca Azure Resource Manager dağıtım modeliyle oluşturulan depolama hesapları Azure AD yetkilendirmesini destekler.

Blob Storage Ayrıca Azure AD kimlik bilgileriyle imzalanmış paylaşılan erişim imzaları (SAS) oluşturulmasını destekler. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları ile verilere sınırlı erişim verme](storage-sas-overview.md).

Azure dosyaları, AD (Önizleme) veya Azure AD DS (GA) ile yalnızca etki alanına katılmış VM 'Ler için SMB üzerinden yetkilendirmeyi destekler. Azure dosyaları için SMB üzerinden AD (Önizleme) veya Azure AD DS (GA) kullanma hakkında bilgi edinmek için bkz. [Azure dosyalarına genel bakış SMB erişimi için kimlik tabanlı kimlik doğrulama desteği](../files/storage-files-active-directory-overview.md).

Azure AD ile yetkilendirme, Azure Tablo depolama için desteklenmez. İstekleri tablo depolamaya yetkilendirmek için paylaşılan anahtar kullanın.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure AD 'ye Genel Bakış

Bir güvenlik sorumlusu (Kullanıcı, Grup veya uygulama) bir blob veya kuyruk kaynağına erişmeyi denediğinde, anonim erişim için kullanılabilir bir blob olmadığı takdirde isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Ardından, belirteç blob veya Kuyruk hizmeti bir isteğin parçası olarak geçirilir ve hizmet tarafından belirtilen kaynağa erişimi yetkilendirmek için kullanılır.

Kimlik doğrulama adımı, bir uygulamanın çalışma zamanında bir OAuth 2,0 erişim belirteci istemesini gerektirir. Bir uygulama, Azure VM, sanal makine ölçek kümesi veya Azure Işlevleri uygulaması gibi bir Azure varlığı içinden çalışıyorsa, bloblara veya kuyruklara erişmek için [yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md) kullanabilir. Yönetilen bir kimlik tarafından Azure Blob 'Una veya Kuyruk hizmeti yapılan isteklerin nasıl yetkilendirdiği hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimliklerle blob 'lara ve kuyruklara erişim yetkisi verme](storage-auth-aad-msi.md).

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla Azure rolünün atanmasını gerektirir. Azure depolama, blob ve kuyruk verileri için ortak izin kümelerini çevreleyen Azure rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure depolama için Azure rolleri atama hakkında daha fazla bilgi edinmek için bkz. [Azure RBAC ile depolama verileri için erişim haklarını yönetme](./storage-auth-aad-rbac-portal.md).

Azure Blob veya Kuyruk hizmeti istek yapan yerel uygulamalar ve Web uygulamaları da Azure AD ile erişimi yetkilendirebilirler. Erişim belirteci isteme ve BLOB veya kuyruk verileri için istekleri yetkilendirmek üzere kullanma hakkında bilgi edinmek için bkz. Azure depolama uygulamasından Azure [ad Ile Azure depolama 'ya erişim yetkisi verme](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Erişim hakları için Azure rolleri atama

Azure Active Directory (Azure AD), [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure depolama, blob ve kuyruk verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen Azure yerleşik rollerinin bir kümesini tanımlar. Ayrıca, blob ve kuyruk verilerine erişim için özel roller de tanımlayabilirsiniz.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure yerleşik rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Bir güvenlik sorumlusuna Azure yerleşik rolünü atamayı öğrenmek için aşağıdaki makalelerden birine bakın:

- [Azure portalı kullanarak blob ve kuyruk verilerine erişim için Azure rolü atama](storage-auth-aad-rbac-portal.md)
- [Blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure CLı 'yi kullanma](storage-auth-aad-rbac-cli.md)
- [Blob ve kuyruk verilerine erişim için bir Azure rolü atamak üzere Azure PowerShell modülünü kullanın](storage-auth-aad-rbac-powershell.md)

Yerleşik rollerin Azure depolama için nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../../role-based-access-control/role-definitions.md#management-and-data-operations). Azure özel rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure özel roller](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Veri işlemleri için erişim izinleri

Belirli Blobu veya Kuyruk hizmeti işlemlerini çağırmak için gereken izinlerle ilgili ayrıntılı bilgi için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Kaynak kapsamı

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD hesabı ile verilere erişme

Azure portal, PowerShell veya Azure CLı aracılığıyla blob veya kuyruk verilerine erişim, kullanıcının Azure AD hesabı kullanılarak veya hesap erişim anahtarları (paylaşılan anahtar yetkilendirmesi) kullanılarak yetkilendirilenebilir.

### <a name="data-access-from-the-azure-portal"></a>Azure portal veri erişimi

Azure portal, bir Azure depolama hesabındaki blob ve kuyruk verilerine erişmek için Azure AD hesabınızı veya hesap erişim anahtarlarını kullanabilir. Azure portal kullandığı yetkilendirme şeması size atanan Azure rollerine bağlıdır.

Blob veya kuyruk verilerine erişmeyi denediğinizde, Azure portal öncelikle **Microsoft. Storage/storageAccounts/ListKeys/Action** Ile bir Azure rolü atanıp atanmadığını denetler. Bu eylemle bir rol atanmışsa Azure portal, paylaşılan anahtar yetkilendirmesi aracılığıyla blob ve kuyruk verilerine erişmek için hesap anahtarını kullanır. Bu eylemle bir rol atamadıysanız, Azure portal Azure AD hesabınızı kullanarak verilere erişmeye çalışır.

Azure AD hesabınızı kullanarak Azure portal blob veya kuyruk verilerine erişmek için, blob ve kuyruk verilerine erişim izinleriniz olmalıdır ve ayrıca Azure portal depolama hesabı kaynakları arasında gezinmek için izinlerinizin olması gerekir. Azure Depolama tarafından sunulan yerleşik roller, blob ve kuyruk kaynaklarına erişim izni verir, ancak depolama hesabı kaynaklarına izin vermez. Bu nedenle, portala erişim için de [Okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü gibi depolama hesabı kapsamında veya daha geniş kapsamda bir Azure Resource Manager rolü atanması gerekir. **Okuyucu** rolü, en kısıtlı izinleri verir, ancak depolama hesabı yönetim kaynaklarına erişim izni veren başka bir Azure Resource Manager rolü de kabul edilebilir. Azure AD hesabıyla Azure portal veri erişimi için kullanıcılara izin atama hakkında daha fazla bilgi edinmek için bkz. [BLOB ve kuyruk verilerine erişim Için Azure rolü atamak üzere Azure Portal kullanma](storage-auth-aad-rbac-portal.md).

Azure portal, bir kapsayıcıya veya kuyruğa gittiğinizde hangi yetkilendirme şemasının kullanımda olduğunu gösterir. Portalda veri erişimi hakkında daha fazla bilgi için bkz. [Azure Portal blob verilerine erişim yetkisi verme](../blobs/authorize-data-operations-portal.md) ve [Azure Portal kuyruk verilerine erişim yetkisi verme seçme](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell veya Azure CLı 'dan veri erişimi

Azure CLı ve PowerShell Azure AD kimlik bilgileriyle oturum açmayı destekler. Oturum açtıktan sonra oturumunuz bu kimlik bilgileri altında çalışır. Daha fazla bilgi için bkz. [BLOB veya kuyruk verilerine erişmek için Azure AD kimlik bilgileriyle Azure CLI veya PowerShell komutlarını çalıştırma](../blobs/authorize-data-operations-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad-msi.md)
- [Bloblara ve kuyruklara erişim sağlamak için bir uygulamadan Azure Active Directory yetkilendirme](storage-auth-aad-app.md)
- [Azure Active Directory tabanlı erişim denetimi için Azure depolama desteği genel kullanıma sunuldu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)