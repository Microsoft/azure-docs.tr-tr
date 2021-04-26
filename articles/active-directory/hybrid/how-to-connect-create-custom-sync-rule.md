---
title: Azure AD Connect bir eşitleme kuralını özelleştirme | Microsoft Docs '
description: Eşitleme kuralı Düzenleyicisi 'ni kullanarak yeni bir eşitleme kuralı düzenleme veya oluşturma hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530497"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Eşitleme kuralını özelleştirme

## <a name="recommended-steps"></a>**Önerilen Adımlar**

Eşitleme kuralı Düzenleyicisi 'ni kullanarak yeni bir eşitleme kuralı düzenleyebilir veya oluşturabilirsiniz. Eşitleme kurallarında değişiklik yapmak için gelişmiş bir kullanıcı olmanız gerekir. Yanlış değişiklikler, hedef dizininizden nesnelerin silinmesine neden olabilir. Eşitleme kurallarında uzmanlığa ulaşmak için lütfen [Önerilen belgeleri](#recommended-documents) okuyun. Bir eşitleme kuralını değiştirmek için aşağıdaki adımları izleyin:

* Aşağıda gösterildiği gibi, masaüstündeki uygulama menüsünden eşitleme düzenleyicisini başlatın:

    ![Eşitleme kuralı Düzenleyicisi menüsü](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Varsayılan eşitleme kuralını özelleştirmek için, eşitleme kuralları düzenleyicisinde "Düzenle" düğmesine tıklayarak mevcut kuralı kopyalayın, bu, standart varsayılan kuralın bir kopyasını oluşturur ve devre dışı bırakır. Kopyalanmış kuralı 100 ' dan düşük bir öncelik ile kaydedin.  Öncelik, bir öznitelik akışı çakışması varsa, hangi kuralın WINS (düşük sayısal değer) Çakışma çözümlemesi olduğunu belirler.

    ![Eşitleme kuralı Düzenleyicisi](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Belirli bir özniteliği değiştirirken, en ideal olarak, kopyalanan kuralda yalnızca değişiklik özniteliğini tutmanız gerekir.  Ardından varsayılan kuralı, değiştirilen özniteliğin kopyalanmış kuraldan geldiği ve diğer özniteliklerin varsayılan standart kuraldan çekilmesi için etkinleştirin. 

* Değiştirilen özniteliğin hesaplanan değerinin klonlanan kuralda NULL olduğu ve varsayılan standart kuralda NULL olmadığı durumlarda, Not NULL değeri kazanacağı ve NULL değeri değiştirecek olduğunu lütfen unutmayın. NULL değer olmayan bir değer ile değiştirmek istemiyorsanız, kopyalanmış kuralınıza AuthoritativeNull atayın.

* **Giden** bir kuralı değiştirmek için, eşitleme kuralı düzenleyicisinden filtreyi değiştirin.

## <a name="recommended-documents"></a>**Önerilen Belgeler**
* [Azure AD Connect eşitleme: teknik kavramlar](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect eşitleme: mimariyi anlama](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect eşitleme: bildirime dayalı sağlamayı anlama](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect eşitleme: bildirim temelli sağlama Ifadelerini anlama](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect eşitleme: Varsayılan yapılandırmayı anlama](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect eşitleme: kullanıcıları, grupları ve kişileri anlama](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect eşitleme: gölge öznitelikleri](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Sonraki Adımlar
- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md).
- [Karma kimlik nedir?](whatis-hybrid-identity.md).