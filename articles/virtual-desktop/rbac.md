---
title: Yerleşik roller Windows sanal masaüstü-Azure
description: Azure RBAC için kullanılabilen Windows sanal masaüstü 'nün yerleşik rollerine genel bakış.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1094004bc523a59c3fcf7eb77c6f11bc4554080
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445865"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Windows sanal masaüstü için yerleşik roller

Windows sanal masaüstü, kullanıcılara ve yöneticilere roller atamak için Azure rol tabanlı erişim denetimleri (RBAC) kullanır. Bu roller, yöneticilere belirli görevleri yürütmek için izin verir. Azure RBAC için yerleşik roller hakkında daha fazla bilgi edinmek için bkz. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md).

Azure için standart yerleşik roller sahip, katkıda bulunan ve okuyucudur. Ancak, Windows sanal masaüstü, konak havuzları, uygulama grupları ve çalışma alanları için yönetim rollerini ayırmanızı sağlayan ek roller içerir. Bu ayrım, yönetim görevleri üzerinde daha ayrıntılı denetime sahip olmanızı sağlar. Bu roller, Azure 'un standart rolleriyle ve en az ayrıcalık yöntemiyle uyumlu olarak adlandırılır.

Windows sanal masaüstü 'Nün belirli bir sahip rolü yoktur. Ancak, hizmet nesneleri için standart bir sahip rolü kullanabilirsiniz.

## <a name="desktop-virtualization-contributor"></a>Masaüstü Sanallaştırma Katılımcısı

Masaüstü Sanallaştırma katılımcısı rolü, dağıtımın tüm yönlerini yönetmenize olanak sağlar. Ancak, size işlem kaynaklarına erişim vermez. Ayrıca, kullanıcılara veya Kullanıcı gruplarına uygulama grupları yayımlamak için Kullanıcı erişimi Yöneticisi rolüne de ihtiyacınız olacaktır.


- Microsoft. DesktopVirtualization/\* 
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="desktop-virtualization-reader"></a>Masaüstü Sanallaştırma okuyucusu

Masaüstü Sanallaştırma okuyucu rolü, dağıtımdaki her şeyi görüntülemenize izin verir, ancak herhangi bir değişiklik yapmanıza izin vermez.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-contributor"></a>Konak havuzu Katılımcısı

Konak havuzu katılımcısı rolü, ana bilgisayar havuzlarının tüm yönlerini, kaynaklara erişim de dahil olmak üzere yönetmenizi sağlar. Sanal makineler oluşturmak için ek bir katkıda bulunan rolü, sanal makine katılımcısı olması gerekir. Portalı kullanarak konak havuzu oluşturmak için AppGroup ve Workspace katkıda bulunan rollerine ihtiyacınız olacak veya masaüstü sanallaştırma katılımcısı rolünü kullanabilirsiniz.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-reader"></a>Konak havuzu okuyucusu

Konak havuzu okuyucu rolü, konak havuzundaki her şeyi görüntülemenize izin verir, ancak herhangi bir değişiklik yapmanıza izin vermez.

- Microsoft. DesktopVirtualization/hosthavuzlar/ \* /Read
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-contributor"></a>Uygulama grubu Katılımcısı

Uygulama grubu katılımcısı rolü, uygulama gruplarının tüm yönlerini yönetmenizi sağlar. Uygulama gruplarını kullanıcılara veya Kullanıcı gruplarına yayımlamak istiyorsanız, Kullanıcı erişimi Yöneticisi rolüne sahip olmanız gerekir.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hosthavuzlar/Read
- Microsoft. DesktopVirtualization/hosthavuzlar/oturumkonakları/okuma
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-reader"></a>Uygulama grubu okuyucusu

Uygulama grubu okuyucu rolü, uygulama grubundaki her şeyi görüntülemenizi sağlar ve herhangi bir değişiklik yapmanıza izin vermez.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. DesktopVirtualization/hosthavuzlar/Read
- Microsoft. DesktopVirtualization/hosthavuzlar/oturumkonakları/okuma
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-contributor"></a>Çalışma alanı Katılımcısı

Çalışma alanı katılımcısı rolü, çalışma alanlarının tüm yönlerini yönetmenizi sağlar. Uygulama gruplarına eklenen uygulamalar hakkında bilgi almak için, uygulama grubu okuyucu rolüne de atanmalıdır.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/çalışma alanları/\*
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-reader"></a>Çalışma alanı okuyucusu

Çalışma alanı okuyucu rolü, çalışma alanındaki her şeyi görüntülemenize izin verir, ancak herhangi bir değişiklik yapmanıza izin vermez.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/çalışma alanları/okuma
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="user-session-operator"></a>Kullanıcı oturumu Işleci

Kullanıcı oturumu operatörü rolü, oturum ana bilgisayarının oturumlarını kapatmak için ileti göndermenize, oturumların bağlantısını kesmenize ve "oturum kapatma" işlevini kullanmanıza olanak sağlar. Ancak bu rol, oturum konağını kaldırma, boşaltma modunu değiştirme gibi oturum ana bilgisayar yönetimi gerçekleştirmenize izin vermez. Bu rol, atamaları görebilir, ancak yöneticileri değiştiremez. Bu rolü belirli konak havuzlarına atamanız önerilir. Bu izni bir kaynak grubu düzeyinde verirseniz, yönetici, bir kaynak grubu altındaki tüm konak havuzları üzerinde okuma iznine sahip olur.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/hosthavuzlar/Read
- Microsoft. DesktopVirtualization/hosthavuzlar/oturumkonakları/okuma
- Microsoft. DesktopVirtualization/hostpools/sessionkonakları/usersessions/\*
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="session-host-operator"></a>Oturum Ana bilgisayar Işleci

Oturum Ana bilgisayar Işletmeni rolü, oturum ana bilgisayarlarını görüntülemenize ve kaldırmanıza ve boşaltma modunu değiştirmenize olanak sağlar. Konak havuzu nesneleri için yazma iznine sahip olmadıkları için Azure portal kullanan oturum ana bilgisayarları ekleyemez. Kayıt belirteci geçerliyse (oluşturulup süresi dolmamışsa), yöneticinin sanal makine katılımcısı rolü aracılığıyla izinleri hesaplama izni varsa, bu rolü konak havuzuna Azure portal dışında oturum konakları eklemek için kullanabilirsiniz.

Aşağıdaki listede, bu rolün erişebileceği izinler açıklanmaktadır:

- Microsoft. DesktopVirtualization/hosthavuzlar/Read
- Microsoft. DesktopVirtualization/hosthavuzlar/sessionhosts/\*
- Microsoft. resources/abonelikler/resourceGroups/Read
- Microsoft. resources/dağıtımlar/okuma
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*
