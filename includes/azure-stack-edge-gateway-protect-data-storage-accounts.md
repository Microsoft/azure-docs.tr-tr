---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285219"
---
Cihazınız, Azure’daki verileriniz için hedef olarak kullanılan bir depolama hesabıyla ilişkilendirilir. Depolama hesabına erişim, abonelikle ve bu depolama hesabıyla ilişkili iki 512 bit depolama hesabı anahtarıyla denetlenir.

Anahtarlardan biri Azure Stack Edge cihazı depolama hesabına erişirken kullanılır. Diğer anahtar ayrılmış olarak tutulur, bu sayede düzenli olarak anahtarları döndürebilirsiniz.

Güvenlik nedenleriyle birçok veri merkezi anahtar döndürmeyi gerektirir. Anahtar dönüşü için aşağıdaki en iyi yöntemleri izlemeniz önerilir:

- Depolama hesabı anahtarınız depolama hesabınızın kök parolasına benzer. Hesap anahtarınızı dikkatle koruyun. Parolayı diğer kullanıcılara dağıtmayın, sabit olarak kodlamayın veya başkalarının erişebileceği bir konumda düz metin olarak kaydetmeyin.
- Tehlikede olduğunu düşünüyorsanız, Azure portal aracılığıyla [Hesap anahtarınızı yeniden oluşturun](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) .
- Azure yöneticiniz, depolama hesabına doğrudan erişmek için Azure portal depolama bölümünü kullanarak birincil veya ikincil anahtarı düzenli aralıklarla değiştirmeli veya yeniden üretmelidir.
- Azure Depolama hesabınızdaki verileri korumak için kendi şifreleme anahtarınızı da kullanabilirsiniz. Müşteri tarafından yönetilen bir anahtar belirttiğinizde verilerinizi şifrelemek ve verilerinize erişimi denetlemek için bu anahtar kullanılır. Verilerinizin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure depolama hesabınız için müşteri tarafından yönetilen anahtarları etkinleştirme](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
