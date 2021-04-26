---
title: What If aracını kullanarak koşullu erişim sorunlarını giderme-Azure Active Directory
description: Uygulanan koşullu erişim ilkelerinin nerede bulunacağı ve neden
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837255"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>What If aracını kullanarak koşullu erişim sorunlarını giderme

Koşullu erişim 'teki [What If aracı](what-if-tool.md) , bir ilkenin belirli bir durumdaki bir kullanıcıya veya bir ilkenin bilinen bir durumda uygulanabileceğini anlamaya çalışırken etkili olur.

What If Aracı, **Azure Portal**  >  **Azure Active Directory**  >  **koşullu erişim**  >  **What If** bulunur.

![Koşullu erişim What If aracı varsayılan durumda](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If aracı şu anda ilkeleri yalnızca rapor modunda değerlendirmez.

## <a name="gathering-information"></a>Bilgi toplanıyor

What If aracı yalnızca bir **kullanıcının** çalışmaya başlamanızı gerektirir. 

Aşağıdaki ek bilgiler isteğe bağlıdır, ancak belirli durumlar için kapsamı daraltmanıza yardımcı olur.

* Bulut uygulamaları veya eylemleri
* IP Adresi 
* Ülke/Bölge
* Cihaz platformu
* İstemci uygulamaları (Önizleme)
* Cihaz durumu (Önizleme) 
* Oturum açma riski

Bu bilgiler, kullanıcıdan, cihazlarından veya Azure AD oturum açma günlüğünden toplanabilir.

## <a name="generating-results"></a>Sonuçlar üretiliyor

Önceki bölümde toplanan ölçütleri girin ve sonuçların listesini oluşturmak için **What If** ' ı seçin. 

Herhangi bir noktada, herhangi bir ölçüt girişini temizlemek ve varsayılan duruma geri dönmek için **Sıfırla** ' yı seçebilirsiniz.

## <a name="evaluating-results"></a>Sonuçlar değerlendiriliyor

### <a name="policies-that-will-apply"></a>Uygulanacak ilkeler

Bu liste, koşullar verildiğinde hangi koşullu erişim ilkelerinin uygulanacağını gösterir. Liste, uygulanan izin ve oturum denetimlerini içerir. Örnek olarak, belirli bir uygulamaya erişmek için çok faktörlü kimlik doğrulamasının gerekli olması dahildir.

### <a name="policies-that-will-not-apply"></a>Uygulanmayacak ilkeler

Bu liste, koşullar uygulandığında geçerli olmayan koşullu erişim ilkelerini gösterir. Listede tüm ilkeler ve neden uygulanmamaları yer alır. Bir ilkeden dışlanması gereken kullanıcıları ve grupları örnekler arasında yer alır.

## <a name="use-case"></a>Kullanım örneği

Birçok kuruluş, erişim olmaması gereken güvenilen konumlara ve engelleme konumlarına izin veren ağ konumlarına dayalı ilkeler oluşturur.

Bir yapılandırmanın uygun şekilde yapıldığını doğrulamak için, bir yönetici What If aracını kullanarak, izin verilmesi gereken bir konumdan ve reddedilmesi gereken bir konumdan erişimi taklit etmek için kullanabilir.

[![Blok erişimi olan sonuçları gösteren What If araç](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

Bu örnekte, contoso 'nun bu konumdan erişimi engellediği için kullanıcının, yolculuğuna yönelik tüm bulut uygulamalarını Kuzey Kore 'ye erişmesi engellenir.

Bu test, kapsamı daraltmak için diğer veri noktalarını içerecek şekilde genişletilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Koşullu Erişim nedir?](overview.md)
* [Azure Active Directory Kimlik Koruması nedir?](../identity-protection/overview-identity-protection.md)
* [Cihaz kimliği nedir?](../devices/overview.md)
* [Nasıl kullanılır: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)