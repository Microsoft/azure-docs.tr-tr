---
title: Veri işlemlerini yetkilendirme
titleSuffix: Azure Storage
description: Azure Active Directory, paylaşılan anahtar yetkilendirmesi veya paylaşılan erişim imzaları (SAS) dahil olmak üzere Azure depolama 'ya erişim yetkisi vermenin farklı yolları hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a8733723fdc59634609a4a79c3b12f12208ab63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470294"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Azure depolama 'daki verilere erişimi yetkilendirme

Depolama hesabınızdaki verilere her eriştiğinizde istemciniz, HTTP/HTTPS üzerinden Azure depolama 'ya bir istek yapar. Hizmetin, istemcinin verilere erişmek için gerekli izinlere sahip olduğundan emin olabilmesi için güvenli bir kaynağa gönderilen her istek yetkilendirilmelidir.

Aşağıdaki tabloda, Azure depolama 'nın kaynaklara erişimi yetkilendirmek için sunduğu seçenekler açıklanmaktadır:

| Azure yapıtı | Paylaşılan anahtar (depolama hesabı anahtarı) | Paylaşılan erişim imzası (SAS) | Azure Active Directory (Azure AD) | Şirket içi Active Directory Domain Services | Anonim genel okuma erişimi |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Azure Blobları     |[Desteklenir](/rest/api/storageservices/authorize-with-shared-key/)         |[Desteklenir](storage-sas-overview.md)         |[Desteklenir](storage-auth-aad.md)         |Desteklenmez|[Desteklenir](../blobs/anonymous-read-access-configure.md)         |
|Azure dosyaları (SMB)     |[Desteklenir](/rest/api/storageservices/authorize-with-shared-key/)         |Desteklenmez         |[Yalnızca AAD etki alanı Hizmetleri ile desteklenir](../files/storage-files-active-directory-overview.md)         |[Desteklenir, kimlik bilgilerinin Azure AD ile eşitlenmesi gerekir](../files/storage-files-active-directory-overview.md)|Desteklenmez         |
|Azure dosyaları (REST)     |[Desteklenir](/rest/api/storageservices/authorize-with-shared-key/)         |[Desteklenir](storage-sas-overview.md)         |Desteklenmez         |Desteklenmez |Desteklenmez         |
|Azure Kuyrukları     |[Desteklenir](/rest/api/storageservices/authorize-with-shared-key/)         |[Desteklenir](storage-sas-overview.md)         |[Desteklenir](storage-auth-aad.md)         |Desteklenmiyor | Desteklenmez         |
|Azure Tabloları     |[Desteklenir](/rest/api/storageservices/authorize-with-shared-key/)         |[Desteklenir](storage-sas-overview.md)         |Desteklenmez         |Desteklenmez| Desteklenmez         |

Her yetkilendirme seçeneği kısaca aşağıda açıklanmıştır:

- Blob 'lar ve kuyruklar için **Azure Active Directory (Azure AD) Tümleştirmesi** . Azure, bir istemcinin depolama hesabındaki kaynaklara erişimi üzerinde denetim için Azure rol tabanlı erişim denetimi (Azure RBAC) sağlar. Blob 'lar ve kuyruklar için Azure AD tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

- Azure dosyaları için **Azure Active Directory Domain Services (azure AD DS) kimlik doğrulaması** . Azure dosyaları, Azure AD DS aracılığıyla sunucu Ileti bloğu (SMB) üzerinden kimlik tabanlı yetkilendirmeyi destekler. Bir depolama hesabındaki Azure dosyaları kaynaklarına erişim için Azure RBAC 'yi ayrıntılı denetim için kullanabilirsiniz. Etki alanı Hizmetleri 'ni kullanarak Azure dosyaları kimlik doğrulaması hakkında daha fazla bilgi için [genel bakış](../files/storage-files-active-directory-overview.md)bölümüne bakın.

- Azure dosyaları için şirket **içi Active Directory Domain Services (AD DS veya şirket içi AD DS) kimlik doğrulaması** . Azure dosyaları, AD DS aracılığıyla SMB üzerinden kimlik tabanlı yetkilendirmeyi destekler. AD DS ortamınız, şirket içi makinelerde veya Azure VM 'lerinde barındırılabilir. Dosyalara SMB erişimi, şirket içinde ya da Azure 'da etki alanına katılmış makinelerden AD DS kimlik bilgileri kullanılarak desteklenir. Dizin/dosya düzeyi izin zorlaması için Azure RBAC 'nin bir birleşimini, Share düzeyinde erişim denetimi ve NTFS DACL 'Leri için kullanabilirsiniz. Etki alanı Hizmetleri 'ni kullanarak Azure dosyaları kimlik doğrulaması hakkında daha fazla bilgi için [genel bakış](../files/storage-files-active-directory-overview.md)bölümüne bakın.

- Bloblar, dosyalar, kuyruklar ve tablolar için **paylaşılan anahtar yetkilendirmesi** . Paylaşılan anahtar kullanan bir istemci, depolama hesabı erişim anahtarı kullanılarak imzalanan her isteği içeren bir üst bilgi geçirir. Daha fazla bilgi için bkz. [paylaşılan anahtarla yetkilendirme](/rest/api/storageservices/authorize-with-shared-key/).
- Bloblar, dosyalar, kuyruklar ve tablolar için **paylaşılan erişim imzaları** . Paylaşılan erişim imzaları (SAS), bir depolama hesabındaki kaynaklara sınırlı temsilci erişimi sağlar. İmzanın geçerli olduğu zaman aralığına veya izin verdiği izinlere yönelik kısıtlamalar, erişimi yönetme konusunda esneklik sağlar. Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](storage-sas-overview.md).
- Kapsayıcılar ve BLOB 'lar için **anonim genel okuma erişimi** . Yetkilendirme gerekli değildir. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../blobs/anonymous-read-access-configure.md).  

Varsayılan olarak, Azure Storage 'daki tüm kaynaklar güvenli hale getirilir ve yalnızca hesap sahibi tarafından kullanılabilir. İstemcilere Depolama hesabınızdaki kaynaklara erişim izni vermek için yukarıda özetlenen yetkilendirme stratejilerinden birini kullanabilseniz de, Microsoft en yüksek güvenlik ve kullanım kolaylığı için mümkün olduğunda Azure AD 'nin kullanılmasını önerir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](storage-auth-aad.md)
- [Paylaşılan Anahtar ile yetkilendirme](/rest/api/storageservices/authorize-with-shared-key/)
- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md)
