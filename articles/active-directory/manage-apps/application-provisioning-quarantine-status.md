---
title: Karantina uygulama sağlama durumu | Microsoft Docs
description: Otomatik Kullanıcı sağlama için bir uygulama yapılandırdığınızda, karantinanın sağlama durumunun ne anlama geldiğini ve nasıl temizleyeceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ad689fb57c51d0deb698a723b93e6175bdbb5c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882887"
---
# <a name="application-provisioning-in-quarantine-status"></a>Karantina durumunda uygulama sağlama

Azure AD sağlama hizmeti yapılandırmanızın sistem durumunu izler ve sağlıksız uygulamaları bir "Karantina" durumuna koyar. Hedef sisteme karşı yapılan çağrıların çoğu veya hepsi bir hata nedeniyle sürekli olarak başarısız olursa, örneğin, geçersiz yönetici kimlik bilgileri gibi, sağlama işi karantinada olarak işaretlenir.

Karantinaya alma sırasında, artımlı döngülerin sıklığı günde bir kez yavaş şekilde azaltılır. Hazırlama işi, tüm hatalar düzeltildikten ve sonraki eşitleme döngüsünün başlamasından sonra karantinadan kaldırılır. Sağlama işi dört haftadan uzun bir süreyle karantinayla kalırsa, sağlama işi devre dışı bırakılır (çalışmayı sonlandırır).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Nasıl yaparım? uygulamamın karantinaya alınsın mı?

Bir uygulamanın karantinada olup olmadığını denetlemek için üç yol vardır:
  
- Azure portal **Azure Active Directory** > **Kurumsal uygulamalar** > &lt;*uygulama adı*&gt; > **sağlama** ve alt kısımdaki ilerleme çubuğuna kaydırma yapın.  

  ![Karantina durumunu gösteren sağlama durumu çubuğu](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Sağlama işinin durumunu programlı bir şekilde almak için Microsoft Graph isteği [Al işini](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) kullanın:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- E-postanızı kontrol edin. Bir uygulama karantinada yerleştirildiğinde, bir kerelik bildirim e-postası gönderilir. Karantina nedeni değişirse, karantinanın yeni nedenini gösteren güncelleştirilmiş bir e-posta gönderilir. E-posta görmüyorsanız:

  - Uygulamanın sağlama yapılandırmasında geçerli bir **bildirim e-postası** belirttiğinizden emin olun.
  - Bildirim e-postası gelen kutusunda istenmeyen posta filtrelemesi olmadığından emin olun.
  - E-postalardan aboneliği kaldırdığınızdan emin olun.

## <a name="why-is-my-application-in-quarantine"></a>Uygulamamın neden karantinaya alınsın?

Sağlama işinin durumunu almak için bir Microsoft Graph isteği, karantinaya alma işleminin aşağıdaki nedenini gösterir:

- `EncounteredQuarantineException` geçersiz kimlik bilgilerinin sağlandığını gösterir. Sağlama Hizmeti, kaynak sistemle hedef sistem arasında bir bağlantı kuramıyor.

- `EncounteredEscrowProportionThreshold`, sağlama, Emanet eşiğini aşmış olduğunu gösterir. Bu durum, sağlama olaylarının %60 ' inden fazlası başarısız olduğunda meydana gelir.

- `QuarantineOnDemand`, uygulamanızla ilgili bir sorun tespit ettiğimiz ve bunu el ile karantinaya alacak şekilde ayarlamış olduğumuz anlamına gelir.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Uygulamamı karantinaya alma Nasıl yaparım??

İlk olarak, uygulamanın karantinada yerleştirilmesine neden olan sorunu çözün.

- [Geçerli yönetici kimlik bilgilerini girdiğinizden](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)emin olmak için uygulamanın sağlama ayarlarını kontrol edin. Azure AD, hedef uygulamayla bir güven kurabilmesi gerekir. Geçerli kimlik bilgilerini girdiğinizden ve hesabınızda gerekli izinlere sahip olduğunuzdan emin olun.

- Hangi hataların karantinaya neden olduğunu araştırmak ve hatayı gidermek için [sağlama günlüklerini](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gözden geçirin. **Etkinlik** bölümündeki **Azure Active Directory** &gt; **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)** Azure Portal giderek sağlama günlüklerine erişin.

Sorunu çözdükten sonra, sağlama işini yeniden başlatın. Uygulamanın sağlama ayarlarında öznitelik eşlemeleri veya kapsam filtreleri gibi bazı değişiklikler, sağlamayı sizin için otomatik olarak yeniden başlatacak. Uygulamanın **sağlama** sayfasındaki ilerleme çubuğu, sağlamanın en son ne zaman başlatıldığını gösterir. Sağlama işini el ile yeniden başlatmanız gerekiyorsa aşağıdaki yöntemlerden birini kullanın:  

- Sağlama işini yeniden başlatmak için Azure portal kullanın. Uygulamanın **sağlama** sayfasında, **Ayarlar**' ın altında, **durumu temizle ve eşitlemeyi yeniden Başlat** ' ı seçin ve **sağlama durumunu** **Açık**olarak ayarlayın. Bu eylem, sağlama hizmetini tamamen yeniden başlatır ve bu işlem biraz zaman alabilir. Tam bir başlangıç döngüsünün yeniden çalışması için, escrows 'yi temizler, uygulamayı karantinadan kaldırır ve tüm filigranları temizler.

- [Sağlama işini yeniden başlatmak](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)için Microsoft Graph kullanın. Yeniden başlatdıklarınız üzerinde tam denetime sahip olacaksınız. Et 'ları kaldırmayı seçebilirsiniz (karantina durumuna göre tahakkuk eden Emanet sayacını yeniden başlatmak için), karantinayı temizleyebilir (uygulamayı karantinadan kaldırmak için) veya filigranları temizleyebilirsiniz. Aşağıdaki isteği kullanın:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`