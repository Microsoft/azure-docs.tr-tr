---
title: Azure AD Connect'i kaldırma
description: Bu belgede Azure AD Connect nasıl kaldırılacağı açıklanır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934217"
---
# <a name="uninstall-azure-ad-connect"></a>Azure AD Connect'i kaldırma

Bu belgede Azure AD Connect nasıl doğru şekilde kaldırılacağı açıklanmaktadır.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Azure AD Connect sunucudan kaldır
Yapmanız gereken ilk şey, Azure AD Connect üzerinde çalıştığı sunucudan kaldırılır.  Aşağıdaki adımları kullanın:

 1. Azure AD Connect çalıştıran sunucuda, **Denetim Masası**' na gidin.
 2. **Program Kaldır ' a** tıklayın program 
  ![ Kaldır](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. **Azure AD Connect** seçin.
 ![Azure AD Connect seçin](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. İstendiğinde, onaylamak için **Evet** ' e tıklayın.
 5. Bu onay Azure AD Connect ekranını getirir.  **Kaldır**’a tıklayın.
 ![Kaldır](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Bu eylem tamamlandığında **Çıkış**' a tıklayın.
 7. ![Çıkış](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. **Denetim Masası** 'na geri dön **Yenile** ' ye tıklayın ve tüm bileşenler kaldırılmıştır.


## <a name="next-steps"></a>Sonraki adımlar

- [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
- [Azure AD Connect'i mevcut bir AD Eşitleme veritabanını kullanarak yükleme](how-to-connect-install-existing-database.md)
- [SQL yönetici temsilcisi izinlerini kullanarak Azure AD Connect'i yükleme](how-to-connect-install-sql-delegation.md)

