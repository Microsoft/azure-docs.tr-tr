---
title: "Azure AD Connect eşitleme: AD geri dönüşüm kutusu 'nu etkinleştir | Microsoft Docs"
description: Bu konu, AD geri dönüşüm kutusu özelliğinin kullanımını Azure AD Connect ile önerir.
services: active-directory
keywords: AD geri dönüşüm kutusu, yanlışlıkla silme, kaynak bağlantısı
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12073a75cd248c9226c7ce5ecc21b64617823b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279644"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect eşitleme: AD geri dönüşüm kutusu 'nu etkinleştir
Azure AD ile eşitlenen şirket içi Active Directory 'niz için AD geri dönüşüm kutusu özelliğini etkinleştirmeniz önerilir. 

Yanlışlıkla bir şirket içi AD Kullanıcı nesnesini sildiyseniz ve özelliğini kullanarak geri yüklerseniz, Azure AD karşılık gelen Azure AD Kullanıcı nesnesini geri yükler.  AD geri dönüşüm kutusu özelliği hakkında daha fazla bilgi için, [silinen Active Directory nesneleri geri yükleme bölümüne genel bakış](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379542(v=ws.10))makalesine bakın.

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>AD geri dönüşüm kutusu 'nu etkinleştirmenin avantajları
Bu özellik, aşağıdaki işlemleri yaparak Azure AD Kullanıcı nesnelerinin geri yüklenmesine yardımcı olur:

* Şirket içi bir AD Kullanıcı nesnesini yanlışlıkla sildiyseniz, ilgili Azure AD Kullanıcı nesnesi bir sonraki eşitleme döngüsüne silinir. Varsayılan olarak, Azure AD, silinen Azure AD Kullanıcı nesnesini, 30 gün boyunca geçici olarak silinen durumda tutar.

* Şirket içi AD geri dönüşüm kutusu özelliği etkinse, silinen şirket içi AD Kullanıcı nesnesini kaynak bağlantı değerini değiştirmeden geri yükleyebilirsiniz. Kurtarılan şirket içi AD Kullanıcı nesnesi Azure AD ile eşitlendiğinde, Azure AD ilgili geçici olarak silinen Azure AD Kullanıcı nesnesini geri yükler. Kaynak Bağlayıcısı özniteliği hakkında daha fazla bilgi için, [Azure AD Connect: Design Concepts](./plan-connect-design-concepts.md#sourceanchor)makalesine başvurun.

* Şirket içi AD geri dönüşüm kutusu özelliği etkinleştirilmemişse, silinen nesneyi değiştirmek için bir AD Kullanıcı nesnesi oluşturmanız gerekebilir. Azure AD Connect eşitleme hizmeti, kaynak Bağlayıcısı özniteliği için sistem tarafından oluşturulan AD özniteliği (Objectguıd gibi) kullanmak üzere yapılandırıldıysa, yeni oluşturulan AD Kullanıcı nesnesi silinen AD Kullanıcı nesnesiyle aynı kaynak bağlayıcı değerine sahip olmayacaktır. Yeni oluşturulan AD Kullanıcı nesnesi Azure AD ile eşitlendiğinde, Azure AD, geçici olarak silinen Azure AD Kullanıcı nesnesini geri yüklemek yerine yeni bir Azure AD Kullanıcı nesnesi oluşturur.

> [!NOTE]
> Varsayılan olarak, Azure AD, kalıcı olarak silinmeden önce 30 gün boyunca geçici olarak silinen eyalette Azure AD Kullanıcı nesnelerini silme sırasında tutar. Ancak, Yöneticiler bu nesnelerin silinmesini hızlandırabilir. Nesneler kalıcı olarak silindikten sonra, şirket içi AD geri dönüşüm kutusu özelliği etkin olsa bile, bunlar artık kurtarılamaz.

## <a name="next-steps"></a>Sonraki adımlar
**Genel Bakış konuları**

* [Azure AD Connect eşitleme: eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)

* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)