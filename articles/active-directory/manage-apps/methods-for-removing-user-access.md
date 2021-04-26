---
title: Kullanıcının Azure Active Directory bir uygulamaya erişimini kaldırma
description: Kullanıcının Azure Active Directory bir uygulamaya erişiminin nasıl kaldırılacağını anlayın
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379596"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Kullanıcının bir uygulamaya erişimini kaldırma

Bu makale, bir kullanıcının uygulamaya erişimini nasıl kaldırabileceğinizi anlamanıza yardımcı olur.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Belirli bir kullanıcının veya grubun atamasını bir uygulamaya kaldırmak istiyorum

Bir uygulamaya Kullanıcı veya grup atamasını kaldırmak için, Azure Active Directory makalesinde bir [Kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma](./assign-user-or-group-access-portal.md) bölümünde listelenen adımları izleyin.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Her Kullanıcı için bir uygulamaya tüm erişimi devre dışı bırakmak istiyorum

Bir uygulamaya tüm Kullanıcı oturum açma işlemlerini devre dışı bırakmak için, [Azure Active Directory makalesinde bir kurumsal uygulama için Kullanıcı oturum açma Işlemlerini devre dışı bırak](./disable-user-sign-in-portal.md) ' da listelenen adımları izleyin.

## <a name="i-want-to-delete-an-application-entirely"></a>Bir uygulamayı tamamen silmek istiyorum

[Uygulama yönetiminde hızlı başlangıç serisi](delete-application-portal.md) , Azure Active Directory kiracınızdan bir uygulamayı silmeye yönelik yönergeler içerir.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Tüm gelecek Kullanıcı onay işlemlerini herhangi bir uygulamaya devre dışı bırakmak istiyorum

Tüm dizininiz için Kullanıcı onayını devre dışı bırakmak, son kullanıcıların herhangi bir uygulamaya yarışmasını önler. Yöneticiler kullanıcı adına yine de izin verebilir. Uygulama onayı hakkında daha fazla bilgi edinmek ve bunu yapmak istememenizin nedeni, [Kullanıcı ve yönetici onayını anlama](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)konusunu okuyun. Ayrıca bkz. [izinler ve onay](../develop/v2-permissions-and-consent.md).

Tüm **dizininizde tüm gelecek Kullanıcı onay işlemlerini devre dışı bırakmak** için şu yönergeleri izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  **Azure Active Directory uzantısını** açın 

3.  Gezinti menüsünde **Kurumsal uygulamalar** ' a tıklayın.

5.  **Kullanıcı ayarları**' na tıklayın.

6.  Kullanıcılar, **uygulamaların kendi adına şirket verilerine erişmesine izin** verebilir **Hayır** ' a geçiş yapar ve Kaydet düğmesine tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

[Uygulamalara erişimi yönetme](what-is-access-management.md)