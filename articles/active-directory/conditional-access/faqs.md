---
title: Koşullu erişim SSS Azure Active Directory | Microsoft Docs
description: Azure Active Directory ' de koşullu erişim hakkında sık sorulan soruların yanıtlarını alın.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2cc562f155fe3b84e5d28b2e0e22d50238d480c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816207"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory Koşullu erişim SSS

## <a name="which-applications-work-with-conditional-access-policies"></a>Hangi uygulamalar koşullu erişim ilkeleriyle çalışır?

Koşullu erişim ilkeleriyle çalışan uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory Koşullu erişim kuralları kullanan uygulamalar ve tarayıcılar](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>B2B işbirliği ve Konuk kullanıcılar için koşullu erişim ilkeleri uygulandı mi?

İlkeler, işletmeler arası (B2B) işbirliği kullanıcıları için zorlanır. Ancak, bazı durumlarda, bir kullanıcı ilke gereksinimlerini karşılayamayabilir. Örneğin, bir Konuk kullanıcının organizasyonu çok faktörlü kimlik doğrulamasını desteklemeyebilir. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>OneDrive Iş için bir SharePoint Online ilkesi de geçerlidir mı?

Evet. Bir SharePoint Online ilkesi, OneDrive Iş için de geçerlidir.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Neden bir ilkeyi Word veya Outlook gibi istemci uygulamalarında doğrudan ayarlayamıyorum?

Koşullu erişim ilkesi, bir hizmete erişmek için gereksinimleri ayarlar. Bu hizmet için kimlik doğrulaması gerçekleştiğinde zorlanır. İlke doğrudan bir istemci uygulamasında ayarlanmadı. Bunun yerine, istemci bir hizmet çağırdığında uygulanır. Örneğin, SharePoint 'te ayarlanan bir ilke, SharePoint 'i çağıran istemciler için geçerlidir. Exchange üzerinde ayarlanan bir ilke Outlook için geçerlidir.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Koşullu erişim ilkesi hizmet hesaplarına uygulanabilir mi?

Koşullu erişim ilkeleri tüm Kullanıcı hesapları için geçerlidir. Bu, hizmet hesapları olarak kullanılan Kullanıcı hesaplarını içerir. Genellikle, katılımsız çalıştıran bir hizmet hesabı, koşullu erişim ilkesinin gereksinimlerini karşılayamaz. Örneğin, çok faktörlü kimlik doğrulaması gerekebilir. Hizmet hesapları, koşullu erişim ilkesi yönetim ayarları kullanılarak bir ilkeden dışlanamaz. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Grafik API 'Leri, koşullu erişim ilkelerini yapılandırmak için kullanılabilir mi?

Şu anda, hayır. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Desteklenmeyen cihaz platformları için varsayılan dışlama ilkesi nedir?

Şu anda, koşullu erişim ilkeleri iOS ve Android cihazlarının kullanıcıları için seçmeli olarak zorlanır. Diğer cihaz platformlarındaki uygulamalar, varsayılan olarak iOS ve Android cihazlar için koşullu erişim ilkesinden etkilenmez. Bir kiracı yöneticisi, desteklenmeyen platformlardaki kullanıcılara erişim izni vermemek için genel ilkeyi geçersiz kılmayı tercih edebilir.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Koşullu erişim ilkeleri Microsoft ekipleri için nasıl çalışır?

Microsoft ekipleri, toplantılar, takvimler ve dosya paylaşımı gibi temel üretkenlik senaryoları için Exchange Online ve SharePoint Online 'ı yoğun bir şekilde kullanır. Bu bulut uygulamaları için ayarlanan koşullu erişim ilkeleri, bir kullanıcı doğrudan Microsoft ekiplerine kaydolduğunda Microsoft ekipleri için geçerlidir.

Microsoft ekipleri Ayrıca bir bulut uygulaması olarak Azure Active Directory Koşullu erişim ilkelerinde de desteklenir. Bir bulut uygulaması için ayarlanan koşullu erişim ilkeleri, bir Kullanıcı oturum açtığında Microsoft ekipleri için geçerlidir. Ancak, Exchange Online ve SharePoint Online kullanıcıları gibi diğer uygulamalarda doğru ilkeler olmadan bu kaynaklara doğrudan erişebiliyor olabilir.

Windows ve Mac için Microsoft ekipleri masaüstü istemcileri modern kimlik doğrulamayı destekler. Modern kimlik doğrulaması, platformlar arası istemci uygulamalarını Microsoft Office için Azure Active Directory kimlik doğrulama kitaplığı 'nı (ADAL) temel alan oturum açma olanağı sunar.

## <a name="next-steps"></a>Sonraki adımlar

- Ortamınız için koşullu erişim ilkelerini yapılandırmak üzere [Azure Active Directory ' de koşullu erişim Için en iyi yöntemlere](best-practices.md)bakın. 
