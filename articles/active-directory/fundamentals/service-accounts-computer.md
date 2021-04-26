---
title: Bilgisayar hesaplarının güvenliğini sağlama | Azure Active Directory
description: Şirket içi bilgisayar hesaplarının güvenliğini sağlamaya yönelik bir kılavuz.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417984"
---
# <a name="securing-computer-accounts"></a>Bilgisayar hesaplarının güvenliğini sağlama

Bilgisayar hesabı veya LocalSystem hesabı, yerel bilgisayardaki neredeyse tüm kaynaklara erişimi olan yerleşik, yüksek ayrıcalıklı bir hesaptır. Bu hesap, oturum açmış herhangi bir kullanıcı hesabı ile ilişkilendirilmemiş. LocalSystem olarak çalışan hizmetler, bilgisayarın kimlik bilgilerini uzak sunuculara sunarak ağ kaynaklarına erişir. <,>$ domain_name formda kimlik bilgilerini sunar \<computer_name> . Bilgisayar hesabının önceden tanımlanmış adı NT AUTHORıTY\SYSTEM' dır. Hizmet başlatmak ve bu hizmet için güvenlik bağlamı sağlamak üzere kullanılabilir.

![[Resim 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Bilgisayar hesabını kullanmanın avantajları

Bilgisayar hesabı aşağıdaki avantajları sağlar.

* **Sınırsız yerel erişim**: bilgisayar hesabı, makinenin yerel kaynaklarına tamamen erişim sağlar.

* **Otomatik parola yönetimi**: bilgisayar hesabı, parolaları el ile değiştirmenize gerek gereksinimini ortadan kaldırır. Bunun yerine, bu hesap Active Directory üyesidir ve hesap parolası otomatik olarak değişir. Ayrıca hizmet için hizmet sorumlusu adının kaydedilmesi gereksinimini ortadan kaldırır.

* **Sınırlı erişim hakları-makine**: Active Directory Domain Services ' deki varsayılan Access Control LISTESI (ACL), bilgisayar hesapları için en az erişime izin verir. Bu hizmetin korsan olması, yalnızca ağınızdaki kaynaklara sınırlı erişime sahip olur.

## <a name="assess-security-posture-of-computer-accounts"></a>Bilgisayar hesaplarının güvenlik duruşunu değerlendir

Bilgisayar hesapları kullanılırken olası sorunlar ve ilgili azaltmaları. 

| Sorunlar| Risk Azaltıcı Etkenler |
| - | - |
| Bilgisayar hesapları, bilgisayar ayrıldığında ve etki alanına yeniden katıldığında silme ve yeniden oluşturma konularına tabidir.| Bir AD grubuna bilgisayar ekleme gereksinimini doğrulayın ve bu sayfada sunulan örnek betikleri kullanarak bir gruba eklenen bilgisayar hesabını doğrulayın.| 
| Bir gruba bilgisayar hesabı eklerseniz, bu bilgisayarda LocalSystem olarak çalışan tüm hizmetlere grubun erişim hakları verilir.| Bilgisayar hesabınızın grup üyeliklerinin seçmeli olması. İlişkili hizmetin Active Directory Domain Services için tüm erişimi olduğundan, herhangi bir etki alanı Yöneticisi grubunun bilgisayar hesabı üyelerini yapmaktan kaçının. |
| LocalSystem için yanlış ağ Varsayılanları| Bilgisayar hesabının ağ kaynaklarına varsayılan sınırlı erişimi olduğunu varsaymayın. Bunun yerine, bu hesap için grup üyeliklerini dikkatle inceleyin. |
| LocalSystem olarak çalışan bilinmeyen hizmetler| LocalSystem hesabı altında çalışan tüm hizmetlerin, Microsoft Hizmetleri veya üçüncü taraflardan güvenilen hizmetler olduğundan emin olun. |


## <a name="find-services-running-under-the-computer-account"></a>Bilgisayar hesabı altında çalışan Hizmetleri bulma

LocalSystem bağlamı altında çalışan hizmetleri bulmak için aşağıdaki PowerShell cmdlet 'ini kullanın

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Belirli bir grubun üyesi olan bilgisayar hesaplarını bulma**

Belirli bir grubun üyesi olan bilgisayar hesaplarını bulmak için aşağıdaki PowerShell cmdlet 'ini kullanın.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Ayrıcalıklı grupların üyesi olan bilgisayar hesaplarını bulma**

Kimlik yöneticileri gruplarının üyesi olan bilgisayar hesaplarını bulmak için aşağıdaki PowerShell cmdlet 'ini kullanın (Domain Admins, Enterprise Admins, Administrators)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Bilgisayar hesaplarından taşı

> [!IMPORTANT]
> Bilgisayar hesapları, yüksek ayrıcalıklı hesaplardır ve yalnızca hizmetinizin makinedeki yerel kaynaklara sınırsız erişime ihtiyacı olduğunda kullanılmalıdır ve yönetilen hizmet hesabı (MSA) kullanamazsınız.

* Hizmetin bir MSA kullanarak çalıştırılabilmeleri ve hizmetiniz destekliyorsa bir grup yönetilen hizmet hesabı (gMSA) veya tek başına yönetilen hizmet hesabı (sMSA) kullanmak için hizmet sahibine danışın.

* Yalnızca hizmetinizi çalıştırmak için gereken ayrıcalıklara sahip bir etki alanı kullanıcı hesabı kullanın.

## <a name="next-steps"></a>Sonraki Adımlar 

Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)

 

 
