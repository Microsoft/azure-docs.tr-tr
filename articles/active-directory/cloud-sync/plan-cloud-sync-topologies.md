---
title: Azure AD Connect bulut eşitleme desteklenen topolojiler ve senaryolar
description: Azure AD Connect bulut eşitlemesini kullanan çeşitli şirket içi ve Azure Active Directory (Azure AD) topolojileri hakkında bilgi edinin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614033"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect bulut eşitleme desteklenen topolojiler ve senaryolar
Bu makalede bulut eşitlemesini Azure AD Connect kullanan çeşitli şirket içi ve Azure Active Directory (Azure AD) topolojileri açıklanmaktadır. Bu makalede yalnızca desteklenen yapılandırma ve senaryolar bulunur.

> [!IMPORTANT]
> Microsoft, resmi olarak belgelenen yapılandırmaların veya eylemlerin dışında Azure AD Connect bulut eşitlemesini değiştirmeyi veya desteklemeyi desteklemez. Bu yapılandırmaların veya eylemlerin herhangi biri, Azure AD Connect bulut eşitlemesine yönelik tutarsız veya desteklenmeyen bir duruma neden olabilirler. Sonuç olarak, Microsoft bu tür dağıtımlar için teknik destek sağlayamaz.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Tüm senaryolar ve topolojiler hakkında hatırlamaları gerekenler
Aşağıda bir çözüm seçerken aklınızda bulundurmanız gereken bilgilerin bir listesi verilmiştir.

- Kullanıcılar ve gruplar tüm ormanlarda benzersiz şekilde tanımlanmalıdır
- Ormanlar arasında eşleştirme, bulut eşitleme ile gerçekleşmez
- Bir kullanıcı veya grup tüm ormanlarda yalnızca bir kez temsil etmelidir
- Nesneler için kaynak bağlantısı otomatik olarak seçilir.  Varsa, ms-DS-ımbu GUID kullanır, aksi halde Objectguıd kullanılır.
- Kaynak Bağlayıcısı için kullanılan özniteliği değiştiremezsiniz.

## <a name="single-forest-single-azure-ad-tenant"></a>Tek orman, tek Azure AD kiracısı
![Tek bir orman için topolojiyi ve tek bir kiracıyı gösteren diyagram.](media/tutorial-single-forest/diagram-2.png)

En basit topoloji, bir veya birden çok etki alanı ve tek bir Azure AD kiracısı içeren tek bir şirket içi ormandır.  Bu senaryonun bir örneği için bkz [. Öğretici: tek bir Azure AD kiracısıyla tek bir orman](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Çoklu orman, tek Azure AD kiracısı
![Çok ormanlı ve tek bir kiracı için topoloji](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Ortak topoloji, bir veya birden çok etki alanı ve tek bir Azure AD kiracısı olan birden çok AD ormandır.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Azure AD Connect, bulut sağlaması ile yeni bir ormana sahip olan orman
![Mevcut bir orman için topolojiyi ve yeni bir ormanı gösteren diyagram.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Bu senaryo, çok ormanlı senaryoya benzerdir, ancak bu, var olan bir Azure AD Connect ortamını içerir ve sonra Azure AD Connect bulut eşitlemesi kullanarak yeni bir ormana getirme.  Bu senaryonun bir örneği için bkz [. Öğretici: tek bir Azure AD kiracısına sahip mevcut bir orman](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Mevcut bir karma AD ormanında bulut eşitlemesini Azure AD Connect pilot
![Tek bir orman için topoloji ve tek bir kiracı ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) , pilolama senaryosu, hem Azure AD Connect hem de aynı ormandaki bulut eşitlemesine Azure AD Connect ve Kullanıcı ve grupların uygun şekilde kapsamını içerir. NOTE: bir nesnenin yalnızca bir araç kapsamında olması gerekir. 

Bu senaryonun bir örneği için bkz [. Öğretici: Pilot Azure AD Connect var olan eşitlenmiş BIR ad ormanında bulut eşitleme](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)

