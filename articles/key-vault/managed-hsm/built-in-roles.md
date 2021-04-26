---
title: Yönetilen HSM yerel RBAC yerleşik rolleri-Azure Key Vault | Microsoft Docs
description: Kullanıcılara, hizmet sorumlularına, gruplara ve yönetilen kimliklere atanabilecek, yönetilen HSM yerleşik rollerine genel bakış
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 01e96922d9c0c47eaf4d430e92eafcd9d0964e13
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557233"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Yönetilen HSM yerel RBAC yerleşik rolleri

Yönetilen HSM yerel RBAC çeşitli yerleşik rollere sahiptir. Bu rolleri kullanıcılara, hizmet sorumlularına, gruplara ve yönetilen kimliklere atayabilirsiniz. Bir sorumlunun bir işlem gerçekleştirmesine izin vermek için, onlara bu işlemleri gerçekleştirme izni veren bir rol atamanız gerekir. Tüm bu roller ve işlemler yalnızca veri düzlemi işlemleri için izinleri yönetmenizi sağlar. Yönetilen HSM kaynağı için denetim düzlemi izinlerini yönetmek için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)kullanmanız gerekir. Denetim düzlemi işlemlerine bazı örnekler yeni bir yönetilen HSM oluşturma veya güncelleştirme, taşıma, silme.

## <a name="built-in-roles"></a>Yerleşik roller

|Rol Adı|Description|ID|
|---|---|---|
|Yönetilen HSM Yöneticisi| Güvenlik etki alanı, tam yedekleme/geri yükleme ve rol yönetimiyle ilgili tüm işlemleri gerçekleştirme izinleri verir. Herhangi bir anahtar yönetim işlemi gerçekleştirmesine izin verilmiyor.|a290e904-7015-4bba-90c8-60543313cdb4|
|Yönetilen HSM şifre müdürü|Tüm rol yönetimini gerçekleştirmeye, silinen anahtarları temizlemeye veya kurtarmaya ve anahtarları dışarı aktarmaya yönelik izinler verir. Diğer anahtar yönetim işlemlerini gerçekleştirmesine izin verilmiyor.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Yönetilen HSM şifre kullanıcısı|Silinen anahtarları Temizleme veya kurtarma dışında tüm anahtar yönetim işlemlerini gerçekleştirme izinleri verir ve anahtarları dışarı aktarır.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Yönetilen HSM Ilkesi Yöneticisi| Rol atamaları oluşturma ve silme izni verir|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Yönetilen HSM şifre denetleyicisi|Anahtar özniteliklerini okumak (ancak kullanmak değil) için okuma izni verir.|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Yönetilen HSM şifre hizmeti şifrelemesi| Hizmet şifrelemesi için bir anahtar kullanma izni verir. |33413926-3206-4CDD-b39a-83574fe37a17|
|Yönetilen HSM Yedeklemesi| Tek bir anahtar veya tam HSM yedeklemesi gerçekleştirme izni verir.|7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>İzin verilen işlemler
> [!NOTE]  
> - Bir ' X ', bir rolün veri eylemini gerçekleştirmesine izin verildiğini gösterir. Boş hücre, rolün bu veri eylemini gerçekleştirmek için pegörevi olmadığını gösterir.
> - Tüm veri eylemi adlarında, kısaltma için aşağıdaki tablolarda atlanan bir ' Microsoft. Keykasası/managedHsm ' öneki vardır.
> - Tüm rol adlarında, kısaltma için aşağıdaki tabloda Atlanan "yönetilen HSM" öneki vardır.

|Veri eylemi | Yönetici | Şifre müdürü | Şifrelenmiş Kullanıcı | İlke Yöneticisi | Şifreleme hizmeti şifrelemesi | Backup | Şifre denetleyicisi|
|---|---|---|---|---|---|---|---|
|**Güvenlik etki alanı yönetimi**|
/SecurityDomain/Download/Action|<center>Sayı</center>||||||
/SecurityDomain/upload/Action|<center>Sayı</center>||||||
/SecurityDomain/upload/Read|<center>Sayı</center>||||||
/SecurityDomain/transferkey/Read|<center>Sayı</center>||||||
|**Anahtar yönetimi**|
|/Keys/Read/action|||<center>Sayı</center>||<center>Sayı</center>||<center>Sayı</center>|
|/Keys/Write/Action|||<center>Sayı</center>||||
|/Keys/Create|||<center>Sayı</center>||||
|/Keys/Delete|||<center>Sayı</center>||||
|/keys/deletedKeys/read/action||<center>Sayı</center>|||||
|/keys/deletedKeys/recover/action||<center>Sayı</center>|||||
|/keys/deletedKeys/delete||<center>Sayı</center>|||||<center>Sayı</center>|
|/Keys/Backup/Action|||<center>Sayı</center>|||<center>Sayı</center>|
|/Keys/restore/Action|||<center>Sayı</center>||||
|/Keys/Export/Action||<center>Sayı</center>|||||
|/Keys/Release/Action|||<center>Sayı</center>||||
|/Keys/Import/Action|||<center>Sayı</center>||||
|**Anahtar şifreleme işlemleri**|
|/Keys/Encrypt/Action|||<center>Sayı</center>||||
|/Keys/Decrypt/Action|||<center>Sayı</center>||||
|/keys/wrap/action|||<center>Sayı</center>||<center>Sayı</center>||
|/keys/unwrap/action|||<center>Sayı</center>||<center>Sayı</center>||
|/Keys/Sign/Action|||<center>Sayı</center>||||
|/Keys/Verify/Action|||<center>Sayı</center>||||
|**Rol yönetimi**|
|/Roleatama/Read/action|<center>Sayı</center>|<center>Sayı</center>|<center>Sayı</center>|<center>Sayı</center>|||<center>Sayı</center>
|/Roleatama\ Write/Action|<center>Sayı</center>|<center>Sayı</center>||<center>Sayı</center>|||
|/Roleatama/Delete/Action|<center>Sayı</center>|<center>Sayı</center>||<center>Sayı</center>|||
|/roleDefinitions/read/action|<center>Sayı</center>|<center>Sayı</center>|<center>Sayı</center>|<center>Sayı</center>|||<center>Sayı</center>
|/roleDefinitions/write/action|<center>Sayı</center>|<center>Sayı</center>||<center>Sayı</center>|||
|/roleDefinitions/delete/action|<center>Sayı</center>|<center>Sayı</center>||<center>Sayı</center>|||
|**Yedekleme/geri yükleme yönetimi**|
|/Backup/start/Action|<center>Sayı</center>|||||<center>Sayı</center>|
|/Backup/Status/Action|<center>Sayı</center>|||||<center>Sayı</center>|
|/restore/start/Action|<center>Sayı</center>||||||
|/restore/Status/Action|<center>Sayı</center>||||||
||||||||

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure rol tabanlı erişim denetimine genel bakış (Azure RBAC)](../../role-based-access-control/overview.md).
- [YÖNETILEN HSM rol yönetimi](role-management.md) hakkında öğreticiye bakın
