---
title: Azure rol tabanlı erişim denetimine geçiş | Microsoft Docs
description: Kasa erişim ilkelerinden Azure rollerine geçiş yapmayı öğrenin.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749087"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Kasa erişim ilkesinden Azure rol tabanlı erişim denetimi izin modeline geçiş

Kasa erişim ilkesi modeli, anahtarlar, gizlilikler ve sertifikalara erişim sağlamak için Key Vault yerleşik olarak bulunan mevcut bir yetkilendirme sistemidir. Key Vault kapsamındaki güvenlik sorumlusuna (Kullanıcı, Grup, hizmet sorumlusu, yönetilen kimlik) bireysel izinler atayarak erişimi denetleyebilirsiniz. 

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynakları üzerinde ayrıntılı erişim yönetimi sağlayan [Azure Resource Manager](../../azure-resource-manager/management/overview.md) yerleşik bir yetkilendirme sistemidir. Azure RBAC ile, üç öğeden oluşan, rol atamaları oluşturarak kaynaklara erişimi kontrol edersiniz: güvenlik sorumlusu, rol tanımı (önceden tanımlanmış izinler kümesi) ve kapsam (kaynak grubu veya ayrı kaynak). Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md).

Azure RBAC 'e geçmeden önce avantajlarının ve sınırlamaların anlaşılması önemlidir.

Kasa erişim ilkeleri üzerinde Azure RBAC önemli avantajları:
- Azure hizmetleri genelinde aynı API ile Azure kaynakları için Birleşik erişim denetimi modeli sağlar
- Yöneticiler için merkezi erişim yönetimi-tüm Azure kaynaklarını tek bir görünümde yönetin
- Zamana dayalı erişim denetimi için [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) ile tümleşik
- Atamaları Reddet-belirli bir kapsamdaki güvenlik sorumlusunu dışarıda bırakma özelliği. Daha fazla bilgi için bkz. [Azure reddetme atamalarını anlama](../../role-based-access-control/deny-assignments.md)

Azure RBAC dezavantajları:
- Rol atamaları için gecikme-rol atamasının uygulanması birkaç dakika sürebilir. Kasa erişim ilkeleri anında atanır.
- Key Vault başına sınırlı sayıda rol ataması-abonelik başına 2000 rol ataması ve her biri için 1024 erişim ilkeleri

## <a name="access-policies-to-azure-roles-mapping"></a>Azure rollerine eşleme erişimi ilkeleri

Azure RBAC, kullanıcılara, gruplara, hizmet sorumlularına ve yönetilen kimliklere atayabileceğiniz birkaç Azure yerleşik rolüne sahiptir. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi [Azure özel rollerinizi](../../role-based-access-control/custom-roles.md)de oluşturabilirsiniz.

Anahtarlar, sertifikalar ve gizli dizi erişim yönetimi için yerleşik roller Key Vault:
- Key Vault Yöneticisi
- Key Vault okuyucu
- Key Vault sertifikası müdürü
- Key Vault şifre müdürü
- Key Vault şifrelenmiş Kullanıcı
- Key Vault şifreleme hizmeti şifreleme kullanıcısı
- Key Vault gizlilikler müdürü
- Key Vault gizli dizi kullanıcısı

Mevcut yerleşik roller hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md)

Kasa erişim ilkeleri, tek tek seçili izinlerle veya önceden tanımlanmış izin şablonlarıyla atanabilir.

Erişim ilkeleri önceden tanımlanmış izin şablonları:
- Anahtar, gizli, sertifika yönetimi
- Anahtar & gizli dizi yönetimi
- Gizli & sertifika yönetimi
- Anahtar yönetimi
- Gizli dizi yönetimi
- Sertifika Yönetimi
- SQL Server Bağlayıcısı
- Azure Data Lake Storage veya Azure depolama
- Azure Backup
- Exchange Online müşteri anahtarı
- SharePoint Online müşteri anahtarı
- Azure bilgileri BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>İlke şablonlarına Azure rolleri eşleme erişimi
| Erişim ilkesi şablonu | Operations | Azure rolü |
| --- | --- | --- |
| Anahtar, gizli, sertifika yönetimi | Anahtarlar: tüm işlemler <br>Sertifikalar: tüm işlemler<br>Gizlilikler: tüm işlemler | Key Vault Yöneticisi |
| Anahtar & gizli dizi yönetimi | Anahtarlar: tüm işlemler <br>Gizlilikler: tüm işlemler| Key Vault şifre müdürü <br> Key Vault gizlilikler müdürü |
| Gizli & sertifika yönetimi | Sertifikalar: tüm işlemler <br>Gizlilikler: tüm işlemler| Key Vault sertifikaları müdürü <br> Key Vault gizlilikler müdürü|
| Anahtar yönetimi | Anahtarlar: tüm işlemler| Key Vault şifre müdürü|
| Gizli dizi yönetimi | Gizlilikler: tüm işlemler| Key Vault gizlilikler müdürü|
| Sertifika Yönetimi | Sertifikalar: tüm işlemler | Key Vault sertifikaları müdürü|
| SQL Server Bağlayıcısı | Anahtarlar: Al, Listele, sarmalama tuşu, sarmalama tuşu | Key Vault şifreleme hizmeti şifreleme kullanıcısı|
| Azure Data Lake Storage veya Azure depolama | Anahtarlar: get, List, Unwrap tuşu | Yok<br> Özel rol gerekli|
| Azure Backup | Anahtarlar: Al, Listele, Yedekle<br> Sertifika: get, List, Backup | Yok<br> Özel rol gerekli|
| Exchange Online müşteri anahtarı | Anahtarlar: Al, Listele, sarmalama tuşu, sarmalama tuşu | Key Vault şifreleme hizmeti şifreleme kullanıcısı|
| Exchange Online müşteri anahtarı | Anahtarlar: Al, Listele, sarmalama tuşu, sarmalama tuşu | Key Vault şifreleme hizmeti şifreleme kullanıcısı|
| Azure bilgileri BYOK | Anahtarlar: get, şifre çöz, imzala | Yok<br>Özel rol gerekli|


## <a name="assignment-scopes-mapping"></a>Atama kapsamları eşleme  

Key Vault için Azure RBAC, aşağıdaki kapsamlardaki rol atamasına izin verir:
- Yönetim grubu
- Abonelik
- Kaynak grubu
- Key Vault kaynağı
- Bireysel anahtar, gizli ve sertifika

Kasa erişimi ilkesi izin modeli, ilke yalnızca Key Vault kaynak düzeyinde atama ile sınırlıdır ve bu 

Genel olarak, uygulama başına bir Anahtar Kasası olması ve Anahtar Kasası düzeyinde erişimi yönetmeniz en iyi uygulamadır. Diğer kapsamlardaki erişimi yönetirken, erişim yönetimini basitleştirecek senaryolar vardır.

- * * Altyapı, güvenlik yöneticileri ve işleçler: yönetim grubu, abonelik veya kaynak grubu düzeyinde, kasa erişim ilkeleriyle ilgili anahtar kasalarının grubunu yönetmek, her Anahtar Kasası için ilkelerin bakım yapılmasını gerektirir. Azure RBAC, yönetim grubunda, abonelikte veya kaynak grubunda bir rol ataması oluşturulmasına olanak sağlar. Bu atama, aynı kapsam altında oluşturulan tüm yeni anahtar kasaları için de geçerlidir. Bu senaryoda, kalıcı erişim sağlamak için tam zamanında erişim ile Privileged Identity Management kullanılması önerilir.
 
- * * Uygulamalar: uygulamanın diğer uygulamayla gizli anahtar paylaşması gerektiğinde senaryolar vardır. Kasa erişimi ilkelerinin kullanılması, tüm gizli anahtarlara erişim vermemek için ayrı anahtar kasasının oluşturulması gerekiyordu. Azure RBAC tek bir anahtar kasası kullanmak yerine bireysel gizlilik için kapsama sahip rol atamaya izin verir.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Azure RBAC geçiş adımlarına kasa erişimi ilkesi
Azure RBAC ve kasa erişim ilkesi izin modeli arasında birçok fark vardır. Sırasıyla, geçiş sırasında kesintilerden kaçınmak için adımların aşağıdaki adımları izlemeniz önerilir.
 
1. **Rolleri tanımla ve ata**: yukarıdaki eşleme tablosuna göre yerleşik rolleri tanımla ve gerektiğinde özel roller oluştur. Kapsamlara kapsamlar eşleme kılavuzlarına göre roller atayın. Anahtar kasasına rol atama hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi ile Key Vault erişim sağlama](rbac-guide.md)
1. **Rol atamasını doğrula**: Azure RBAC 'de rol atamalarının yayılması birkaç dakika sürebilir. Rol atamalarını denetleme Kılavuzu için bkz. [kapsamdaki rol atamalarını listeleme](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Anahtar kasasında izleme ve uyarı yapılandırma**: erişim reddedildi özel durumları için günlüğe kaydetmeyi etkinleştirmek ve uyarı ayarlamak önemlidir. Daha fazla bilgi için bkz. [Azure Key Vault Için izleme ve uyarma](./alert.md)
1. **Key Vault Azure rol tabanlı erişim denetimi izin modelini ayarlama**: Azure RBAC izin modelinin etkinleştirilmesi, mevcut tüm erişim ilkelerini geçersiz kılar. Bir hata varsa, izin modeli mevcut tüm erişim ilkeleriyle birlikte geri dönebilir.

> [!NOTE]
> İzin modelinin değiştirilmesi, [sahip](../../role-based-access-control/built-in-roles.md#owner) ve [Kullanıcı erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) rollerinin bir parçası olan ' Microsoft. Authorization/roleatamalar/Write ' iznini gerektirir. ' Hizmet Yöneticisi ' ve ' ortak yönetici ' gibi klasik abonelik yöneticisi rolleri desteklenmez.

> [!NOTE]
> Azure RBAC izin modeli etkinleştirildiğinde, erişim ilkelerini güncelleştirmeyi deneyen tüm betikler başarısız olur. Bu betiklerin Azure RBAC kullanmak için güncelleştirilmesi önemlidir.

## <a name="troubleshooting"></a>Sorun giderme
-  Rol ataması birkaç dakika sonra çalışmıyor-rol atamalarının daha uzun sürebileceği durumlar vardır. Bu servis taleplerini kapsayan kodda yeniden deneme mantığı yazmak önemlidir.
- Key Vault silindiğinde rol atamaları yok (geçici silme) ve kurtarılan-bu, şu anda tüm Azure hizmetlerinde geçici silme özelliği kısıtlamasıdır. Kurtarma sonrasında tüm rol atamalarının yeniden oluşturulması gerekir.    

## <a name="learn-more"></a>Daha fazla bilgi edinin

- [Azure RBAC genel bakış](../../role-based-access-control/overview.md)
- [Özel roller öğreticisi](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)