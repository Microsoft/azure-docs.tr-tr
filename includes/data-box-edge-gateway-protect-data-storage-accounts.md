---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82562142"
---
Cihazınız, Azure’daki verileriniz için hedef olarak kullanılan bir depolama hesabıyla ilişkilendirilir. Depolama hesabına erişim, abonelikle ve bu depolama hesabıyla ilişkili iki 512 bit depolama hesabı anahtarıyla denetlenir.

Anahtarlardan biri Azure Stack Edge cihazı depolama hesabına erişirken kullanılır. Diğer anahtar ayrılmış olarak tutulur, bu sayede düzenli olarak anahtarları döndürebilirsiniz.

Güvenlik nedenleriyle birçok veri merkezi anahtar döndürmeyi gerektirir. Anahtar dönüşü için aşağıdaki en iyi yöntemleri izlemeniz önerilir:

- Depolama hesabı anahtarınız depolama hesabınızın kök parolasına benzer. Hesap anahtarınızı dikkatle koruyun. Parolayı diğer kullanıcılara dağıtmayın, sabit olarak kodlamayın veya başkalarının erişebileceği bir konumda düz metin olarak kaydetmeyin.
- Tehlikede olduğunu düşünüyorsanız, Azure portal aracılığıyla hesap anahtarınızı yeniden oluşturun. Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../articles/storage/common/storage-account-keys-manage.md).
- Azure yöneticiniz, depolama hesabına doğrudan erişmek için Azure portal depolama bölümünü kullanarak birincil veya ikincil anahtarı düzenli aralıklarla değiştirmeli veya yeniden üretmelidir.