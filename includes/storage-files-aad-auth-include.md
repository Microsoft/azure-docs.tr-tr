---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854568"
---
[Azure dosyaları](../articles/storage/files/storage-files-introduction.md) , sunucu ileti bloğu (SMB) üzerinden [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md)üzerinden kimlik tabanlı kimlik doğrulamasını destekler. Etki alanına katılmış Windows sanal makineleriniz (VM), [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir.

Azure dosyalarını [rol tabanlı erişim denetimi (RBAC)](../articles/role-based-access-control/overview.md)kullanarak Azure AD 'deki bir kullanıcı veya grup gibi bir kimlikle yönetebilirsiniz. Azure dosyalarına erişmek için kullanılan ortak izin kümelerini içeren özel RBAC rolleri tanımlayabilirsiniz. Özel RBAC rolünüzü bir Azure AD kimliğine atadığınızda, bu kimliğe bu izinlere göre bir Azure dosya paylaşımının erişimi verilir.

Azure dosyaları ayrıca bir dosya paylaşımındaki tüm dosya ve dizinlerde bulunan [NTFS DACL 'lerini](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) koruma, devralma ve zorunlu tutmayı destekler. Verileri bir dosya paylaşımından Azure dosyalarına kopyalayabilir veya bunun tersini yaparsanız, NTFS DACL 'lerin korunmasını belirtebilirsiniz. Bu şekilde, Azure dosyalarını kullanarak yedekleme senaryolarını uygulayabilir ve şirket içi dosya paylaşımınız ile bulut dosya paylaşımınız arasındaki NTFS DACL 'larınızı koruma altına alabilirsiniz. 

> [!NOTE]
> - Sunucu Ileti bloğu (SMB) erişimi için Azure AD DS kimlik doğrulaması, Linux VM 'Leri için desteklenmez. Yalnızca Windows sanal makineleri desteklenir.
> - SMB erişimi için Azure AD DS kimlik doğrulaması, Active Directory etki alanına katılmış makineler için desteklenmez. Bu arada, verilerinizi Azure dosyalarına geçirmeye başlamak ve şirket içi Active Directory etki alanına katılmış makinelerinizden Active Directory kimlik bilgilerini kullanarak erişim denetimini uygulamaya devam etmek için [Azure dosya eşitleme](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) kullanmayı göz önünde bulundurun. 
> - SMB erişimi için Azure AD DS kimlik doğrulaması yalnızca 24 Eylül 2018 ' den sonra oluşturulan depolama hesapları için kullanılabilir.
> - SMB erişimi için Azure AD DS kimlik doğrulaması ve NTFS DACL kalıcılığı, Azure Dosya Eşitleme tarafından yönetilen Azure dosya paylaşımlarında desteklenmez.
> - Azure AD DS kimlik doğrulaması, Azure AD DS oluşturulan makine hesaplarında kimlik doğrulamasını desteklemez.
