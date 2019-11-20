---
title: Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme | Microsoft Docs
description: Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: aed61e5cf32ed0148e8b9eee6edf44daa03acd17
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598102"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme

Azure depolama, istekleri blob ve kuyruk depolamaya yetkilendirmek için Azure Active Directory (AD) kullanılmasını destekler. Azure AD ile rol tabanlı erişim denetimi 'ni (RBAC), bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusuna izinler vermek için kullanabilirsiniz. Güvenlik sorumlusunun bir OAuth 2,0 belirteci döndürmesi için Azure AD tarafından kimliği doğrulanır. Belirteç, blob veya kuyruk depolama alanındaki bir kaynağa erişme isteğine yetki vermek için kullanılabilir.

Azure AD tarafından döndürülen bir OAuth 2,0 belirtecini kullanarak kullanıcıları veya uygulamaları yetkilendirmek, paylaşılan anahtar yetkilendirmesi ve paylaşılan erişim imzaları (SAS) üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Azure AD ile, hesap erişim anahtarını kodunuzla depolamanız ve potansiyel güvenlik açıklarına karşı risk altında olması gerekmez. Uygulamalarınızla paylaşılan anahtar yetkilendirmesi kullanmaya devam edebilirsiniz, ancak Azure AD atlama 'yi kullanarak hesap erişim anahtarınızı kodunuzla depolama gereksinimini ortadan kaldırabilirsiniz. Ayrıca, Depolama hesabınızdaki kaynaklara ayrıntılı erişim sağlamak için paylaşılan erişim imzaları (SAS) kullanmaya devam edebilirsiniz, ancak Azure AD SAS belirteçlerini yönetmeye gerek kalmadan veya güvenliği aşılmış bir SAS iptal etme gereksinimi olmadan benzer yetenekler sunmaktadır. Microsoft, mümkün olduğunda Azure depolama uygulamalarınızla Azure AD yetkilendirmesi kullanılmasını önerir.

Azure AD ile yetkilendirme, tüm genel bölgelerde ve ulusal bulutlarda tüm genel amaçlı ve BLOB depolama hesapları için kullanılabilir. Yalnızca Azure Resource Manager dağıtım modeliyle oluşturulan depolama hesapları Azure AD yetkilendirmesini destekler. Azure AD ile yetkilendirme, Azure Tablo depolama için desteklenmez.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure AD 'ye Genel Bakış

Bir güvenlik sorumlusu (Kullanıcı, Grup veya uygulama) bir blob veya kuyruk kaynağına erişmeyi denediğinde, anonim erişim için kullanılabilir bir blob olmadığı takdirde isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Ardından, belirteç blob veya Kuyruk hizmeti bir isteğin parçası olarak geçirilir ve hizmet tarafından belirtilen kaynağa erişimi yetkilendirmek için kullanılır.

Kimlik doğrulama adımı, bir uygulamanın çalışma zamanında bir OAuth 2,0 erişim belirteci istemesini gerektirir. Bir uygulama, Azure VM, sanal makine ölçek kümesi veya Azure Işlevleri uygulaması gibi bir Azure varlığı içinden çalışıyorsa, bloblara veya kuyruklara erişmek için [yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md) kullanabilir. Yönetilen bir kimlik tarafından Azure Blob 'Una veya Kuyruk hizmeti yapılan isteklerin nasıl yetkilendirdiği hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimliklerle blob 'lara ve kuyruklara erişim yetkisi verme](storage-auth-aad-msi.md).

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla RBAC rolünün atanmasını gerektirir. Azure depolama, blob ve kuyruk verileri için ortak izin kümelerini çevreleyen RBAC rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure depolama için RBAC rolleri atama hakkında daha fazla bilgi edinmek için bkz. [RBAC ile depolama verileri için erişim haklarını yönetme](storage-auth-aad-rbac.md).

Azure Blob veya Kuyruk hizmeti istek yapan yerel uygulamalar ve Web uygulamaları da Azure AD ile erişimi yetkilendirebilirler. Erişim belirteci isteme ve BLOB veya kuyruk verileri için istekleri yetkilendirmek üzere kullanma hakkında bilgi edinmek için bkz. Azure depolama uygulamasından Azure [ad Ile Azure depolama 'ya erişim yetkisi verme](storage-auth-aad-app.md).

## <a name="assign-rbac-roles-for-access-rights"></a>Erişim hakları için RBAC rolleri atama

Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure depolama, blob ve kuyruk verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar. Ayrıca, blob ve kuyruk verilerine erişim için özel roller de tanımlayabilirsiniz.

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için yerleşik RBAC rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Bir güvenlik sorumlusuna yerleşik RBAC rolü atamayı öğrenmek için aşağıdaki makalelerden birine bakın:

- [Azure portalında RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](storage-auth-aad-rbac-portal.md)
- [Azure CLI kullanarak RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](storage-auth-aad-rbac-cli.md)
- [PowerShell kullanarak RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](storage-auth-aad-rbac-powershell.md)

Yerleşik rollerin Azure depolama için nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../../role-based-access-control/role-definitions.md#management-and-data-operations). Özel RBAC rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı Access Control için özel roller oluşturma](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Veri işlemleri için erişim izinleri

Belirli Blobu veya Kuyruk hizmeti işlemlerini çağırmak için gereken izinlerle ilgili ayrıntılı bilgi için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Kaynak kapsamı

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD hesabı ile verilere erişme

Azure portal, PowerShell veya Azure CLı aracılığıyla blob veya kuyruk verilerine erişim, kullanıcının Azure AD hesabı kullanılarak veya hesap erişim anahtarları (paylaşılan anahtar yetkilendirmesi) kullanılarak yetkilendirilenebilir.

### <a name="data-access-from-the-azure-portal"></a>Azure portal veri erişimi

Azure portal, bir Azure depolama hesabındaki blob ve kuyruk verilerine erişmek için Azure AD hesabınızı veya hesap erişim anahtarlarını kullanabilir. Azure portal kullandığı yetkilendirme şeması size atanan RBAC rollerine bağlıdır.

Blob veya kuyruk verilerine erişmeyi denediğinizde, Azure portal öncelikle **Microsoft. Storage/storageAccounts/ListKeys/Action**ile RBAC rolü atanıp atanmadığını denetler. Bu eylemle bir rol atanmışsa Azure portal, paylaşılan anahtar yetkilendirmesi aracılığıyla blob ve kuyruk verilerine erişmek için hesap anahtarını kullanır. Bu eylemle bir rol atamadıysanız, Azure portal Azure AD hesabınızı kullanarak verilere erişmeye çalışır.

Azure AD hesabınızı kullanarak Azure portal blob veya kuyruk verilerine erişmek için, blob ve kuyruk verilerine erişim izinleriniz olmalıdır ve ayrıca Azure portal depolama hesabı kaynakları arasında gezinmek için izinlerinizin olması gerekir. Azure depolama tarafından sunulan yerleşik roller, blob ve kuyruk kaynaklarına erişim izni verir, ancak depolama hesabı kaynaklarına izin vermez. Bu nedenle, portala erişim, Ayrıca, depolama hesabı düzeyi veya daha yüksek olan [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü gibi Azure Resource Manager rolün atanmasını da gerektirir. **Okuyucu** rolü, en kısıtlı izinleri verir, ancak depolama hesabı yönetim kaynaklarına erişim izni veren başka bir Azure Resource Manager rolü de kabul edilebilir. Azure AD hesabıyla Azure portal veri erişimi için kullanıcılara izin atama hakkında daha fazla bilgi için, bkz. [Azure Blob 'a erişim verme ve Azure Portal RBAC ile kuyruk verileri](storage-auth-aad-rbac-portal.md)oluşturma.

Azure portal, bir kapsayıcıya veya kuyruğa gittiğinizde hangi yetkilendirme şemasının kullanımda olduğunu gösterir. Portalda veri erişimi hakkında daha fazla bilgi için bkz. [BLOB veya kuyruk verilerine erişmek için Azure Portal kullanma](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell veya Azure CLı 'dan veri erişimi

Azure CLı ve PowerShell Azure AD kimlik bilgileriyle oturum açmayı destekler. Oturum açtıktan sonra oturumunuz bu kimlik bilgileri altında çalışır. Daha fazla bilgi için bkz. [BLOB veya kuyruk verilerine erişmek için Azure AD kimlik bilgileriyle Azure CLI veya PowerShell komutlarını çalıştırma](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Azure dosyaları için SMB üzerinden Azure AD yetkilendirmesi

Azure dosyaları, yalnızca etki alanına katılmış VM 'Ler (Önizleme) için SMB üzerinden Azure AD ile yetkilendirmeyi destekler. Azure dosyaları için SMB üzerinden Azure AD kullanma hakkında bilgi edinmek için bkz. [Azure dosyaları IÇIN SMB üzerinden Azure Active Directory yetkilendirmeyi genel bakış (Önizleme)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad-msi.md)
- [Bloblara ve kuyruklara erişim sağlamak için bir uygulamadan Azure Active Directory yetkilendirme](storage-auth-aad-app.md)
- [Azure Active Directory tabanlı erişim denetimi için Azure depolama desteği genel kullanıma sunuldu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
