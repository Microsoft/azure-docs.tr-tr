---
title: Karma kimlik tasarımı-Dizin eşitleme gereksinimleri Azure | Microsoft Docs
description: Kuruluş için şirket içi ve bulut arasındaki tüm kullanıcıları eşitlemek için hangi gereksinimlerin gerekli olduğunu belirler.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278403"
---
# <a name="determine-directory-synchronization-requirements"></a>Dizin eşitleme gereksinimlerini belirleme
Eşitleme, kullanıcılara şirket içi kimlik bilgilerini temel alarak bulutta bir kimlik sağlamaya yönelik bir bütün dır. Kimlik doğrulaması veya federal kimlik doğrulaması için eşitlenmiş hesap kullanıp kullanmayacağınızı, kullanıcıların bulutta bir kimliğe sahip olmaları gerekir.  Bu kimliğin düzenli aralıklarla tutulması ve güncelleştirilmeleri gerekir.  Güncelleştirmeler, başlık değişikliklerinden parola değişikliklerine kadar birçok form alabilir.  

Kuruluşların şirket içi kimlik çözümünü ve Kullanıcı gereksinimlerini değerlendirerek başlayın. Bu değerlendirme, kullanıcı kimliklerinin bulutta nasıl oluşturulup korunacağı konusunda teknik gereksinimleri tanımlamak için önemlidir.  Çoğu kuruluş için Active Directory Şirket içi ve kullanıcıların içinden eşitlenecek şirket içi dizin olacaktır, ancak bazı durumlarda bu durum böyle olmaz.  

Aşağıdaki soruları cevaplamadığınızdan emin olun:

* Bir AD ormanınız, birden çok veya hiçbiri mi var?
  
  * Kaç Azure AD dizinini eşitlemektir?
    
    1. Filtreleme kullanıyor musunuz?
    2. Birden çok Azure AD Connect sunucunuz planlanmış mı?
* Şu anda şirket içi bir eşitleme aracınız var mı?
  
  * Yanıt Evet ise, kullanıcılarınız kullanıcıların sanal bir dizin/kimlik tümleştirmesi varsa, kullanıcılarınızı ister misiniz?
* Şirket içinde, eşitlenmesini istediğiniz başka bir dizininiz var mı (örneğin, LDAP dizini, HR veritabanı vb.)?
  * Herhangi bir GALSync yapmaya devam etmek istiyor musunuz?
  * Kuruluşunuzdaki UPN 'ler geçerli durumu nedir? 
  * Kullanıcıların kimlik doğrulaması yapan farklı bir dizininiz mı var?
  * Şirketiniz Microsoft Exchange kullanıyor mu?
    * Karma bir Exchange dağıtımına sahip olmayı planlıyor musunuz?

Eşitleme gereksinimleriniz hakkında bir fikriniz olduğuna göre, bu gereksinimleri karşılamak için doğru bir araç olduğunu belirlemeniz gerekir.  Microsoft, Dizin tümleştirmesi ve eşitleme gerçekleştirmek için çeşitli araçlar sağlar.  Daha fazla bilgi için [karma kimlik dizini tümleştirme araçları karşılaştırma tablosuna](plan-hybrid-identity-design-considerations-tools-comparison.md) bakın. 

Eşitleme gereksinimlerinize ve bunu şirketiniz için gerçekleştirecek olan araca sahip olduğunuza göre, bu dizin hizmetlerini kullanan uygulamaları değerlendirmeniz gerekir. Bu değerlendirme, bu uygulamaları buluta bütünleştirmek için teknik gereksinimleri tanımlamak açısından önemlidir. Aşağıdaki soruları cevaplamadığınızdan emin olun:

* Bu uygulamalar buluta taşınacak ve dizini kullanacak mı?
* Bu uygulamaların başarıyla kullanabilmesi için buluta eşitlenmesi gereken özel öznitelikler var mı?
* Bu uygulamaların, bulut kimlik doğrulaması avantajlarından yararlanmak için yeniden yazılması gerekir mi?
* Kullanıcılar bulut kimliğini kullanarak bunlara erişirken bu uygulamalar Şirket içinde canlı olmaya devam edecek mı?

Ayrıca güvenlik gereksinimleri ve kısıtlamalar dizin eşitlemesini belirlemeniz gerekir. Bu değerlendirme, Kullanıcı kimliklerini bulutta oluşturmak ve korumak için gerekli olan gereksinimlerin bir listesini almak için önemlidir. Aşağıdaki soruları cevaplamadığınızdan emin olun:

* Eşitleme sunucusu nerede bulunur?
* Etki alanına katılmış mi?
* Sunucu, DMZ gibi bir güvenlik duvarının arkasındaki kısıtlı bir ağda yer alacak mı?
  * Eşitlemeyi desteklemek için gerekli güvenlik duvarı bağlantı noktalarını açabiliyor musunuz?
* Eşitleme sunucusu için bir olağanüstü durum kurtarma planınız var mı?
* Eşitleme yapmak istediğiniz tüm ormanlar için doğru izinlere sahip bir hesabınız var mı?
  * Şirketiniz bu sorunun yanıtını bilmez ise, [Azure Active Directory eşitleme hizmetini yüklemek](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) ve bu izinlere sahip bir hesabınız olup olmadığını veya bir hesap oluşturmanız gerekip gerekmediğini öğrenmek için "parola eşitleme izinleri" bölümünü gözden geçirin.
* Çoklu orman eşitlemesi varsa eşitleme sunucusu her bir ormana alabilir mi?

> [!NOTE]
> Her yanıtı not almayı unutmayın ve yanıtın arkasındaki mantığı anladığınızdan emin olun. [Olay yanıtı gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-incident-response-requirements.md) , kullanılabilir seçeneklerin üzerine gider. Bu sorulara yanıt vererek, iş gereksinimlerinize en uygun seçeneği seçersiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
[Multi-Factor Authentication gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Ayrıca bkz.
[Tasarım konularına genel bakış](plan-hybrid-identity-design-considerations-overview.md)