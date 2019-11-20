---
title: Azure AD Yetkilendirme Yönetimi 'nde erişim paketi için değişiklik isteği ve onay ayarları-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde erişim paketi için istek ve onay ayarlarını değiştirmeyi öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 649a4a50766d26f73584dae4481652c8007b2e9e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174665"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde bir erişim paketi için değişiklik isteği ve onay ayarları

Erişim paketi Yöneticisi olarak, ilkeyi düzenleyerek veya yeni bir ilke ekleyerek herhangi bir zamanda erişim paketi talep edebilen kullanıcıları değiştirebilirsiniz. Ayrıca onay ayarlarını da değiştirebilirsiniz.

Bu makalede, var olan bir erişim paketinin istek ve onay ayarlarının nasıl değiştirileceği açıklanır.

## <a name="choose-between-one-or-multiple-polices"></a>Bir veya birden çok ilke arasında seçim yapın

Bir ilke ile bir erişim paketi talep edebilen kim tarafından belirtilme yöntemi. Bir erişim paketi oluşturduğunuzda, ilke oluşturan istek ve onay ayarını belirtirsiniz. Çoğu erişim paketi tek bir ilkeye sahip olur, ancak tek bir erişim paketinde birden çok ilke olabilir. Farklı Kullanıcı kümelerine farklı istek ve onay ayarları içeren atamalar verilmesini sağlamak istiyorsanız, bir erişim paketi için birden çok ilke oluşturabilirsiniz. Örneğin, tek bir ilke, aynı erişim paketine iç ve dış kullanıcı atamak için kullanılamaz. Bununla birlikte, aynı erişim paketinde bir tane olmak üzere, biri iç kullanıcılar ve diğeri dış kullanıcılar için olmak üzere iki ilke oluşturabilirsiniz. Bir kullanıcı için uygulanan birden çok ilke varsa, kendilerine atanması gereken ilkeyi seçme istekleri sırasında istenir. Aşağıdaki diyagramda, iki ilkeye sahip bir erişim paketi gösterilmektedir.

![Erişim paketinde birden çok ilke](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Senaryo | İlke sayısı |
| --- | --- |
| Dizinimde tüm kullanıcıların bir erişim paketi için aynı istek ve onay ayarlarına sahip olmasını istiyorum | Biriyle |
| Belirli bağlı kuruluşlardaki tüm kullanıcıların bir erişim paketi isteyebilmesini istiyorum | Biriyle |
| Dizinimde bulunan kullanıcılara ve ayrıca dizinimin dışındaki kullanıcıların erişim paketi istemesine izin vermek istiyorum | Birden çok |
| Bazı kullanıcılar için farklı onay ayarları belirtmek istiyorum | Birden çok |
| Diğer kullanıcıların erişimini genişletecek bazı kullanıcıların paket atamalarına erişmesini istiyorum | Birden çok |

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>İstek ve onay ayarlarının mevcut bir ilkesini açın

Bir erişim paketinin istek ve onay ayarlarını değiştirmek için, ilgili ilkeyi açmanız gerekir. Bir erişim paketinin istek ve onay ayarlarını açmak için aşağıdaki adımları izleyin.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İlkeler** ' e tıklayın ve ardından düzenlemek istediğiniz ilkeye tıklayın.

    Ilke ayrıntıları bölmesi sayfanın altında açılır.

    ![Erişim paketi-Ilke ayrıntıları bölmesi](./media/entitlement-management-shared/policy-details.png)

1. İlkeyi düzenlemek için **Düzenle** ' ye tıklayın.

    ![Erişim paketi-ilkeyi Düzenle](./media/entitlement-management-shared/policy-edit.png)

1. İstek ve onay ayarlarını açmak için **istekler** sekmesine tıklayın.

1. Aşağıdaki istek bölümlerinden birindeki adımları gerçekleştirin.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>İstek ve onay ayarları için yeni bir ilke ekleyin

Farklı istek ve onay ayarlarına sahip olması gereken bir kullanıcı kümesine sahipseniz, muhtemelen yeni bir ilke oluşturmanız gerekecektir. Var olan bir erişim paketine yeni bir ilke eklemeye başlamak için bu adımları izleyin.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İlkeler** ' e ve ardından **ilke Ekle**' ye tıklayın.

1. İlke için bir ad ve açıklama yazın.

    ![Ad ve açıklama ile ilke oluştur](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **İleri** ' ye tıklayarak **istekler** sekmesini açın.

1. Aşağıdaki istek bölümlerinden birindeki adımları gerçekleştirin.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Bir ilkeyi düzenliyorsanız **Güncelleştir**' e tıklayın. Yeni bir ilke ekliyorsanız **Oluştur**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketi için yaşam döngüsü ayarlarını değiştirme](entitlement-management-access-package-lifecycle-policy.md)
- [Erişim paketi isteklerini görüntüleme](entitlement-management-access-package-requests.md)