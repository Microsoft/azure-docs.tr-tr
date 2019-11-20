---
title: Azure AD Yetkilendirme Yönetimi 'nde işlem ve e-posta bildirimleri isteme-Azure Active Directory
description: Erişim paketi için istek süreci ve Azure Active Directory yetkilendirme yönetimi 'nde e-posta bildirimleri gönderildiğinde öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200010"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde işlem ve e-posta bildirimleri iste

Bir Kullanıcı bir erişim paketine istek gönderdiğinde, bir işlem o erişim isteğini sunmaya başlar. Azure AD Yetkilendirme Yönetimi, işlem sırasında önemli olaylar gerçekleştiğinde onaylayanlara ve istek Lara e-posta bildirimleri gönderir. Bu makalede, istek işlemi ve gönderilen e-posta bildirimleri açıklanmaktadır.

## <a name="request-process"></a>İstek işlemi

Erişim paketine erişmesi gereken bir Kullanıcı, erişim isteği gönderebilir. İlkenin yapılandırmasına bağlı olarak istek için onay gerekebilir. Bir istek onaylandığında bir işlem, erişim paketindeki her kaynağa Kullanıcı erişimini atamaya başlar. Aşağıdaki diyagramda, işleme ve farklı durumlara genel bir bakış gösterilmektedir:

![Onay işlemi diyagramı](./media/entitlement-management-process/request-process.png)

| Eyalet | Açıklama |
| --- | --- |
| Gönderme | Kullanıcı bir istek gönderir. |
| Onay bekleniyor | Bir erişim paketinin ilkesi onay gerektiriyorsa, bir istek bekleyen onaya gider. |
| Süresi doldu | Onay isteği zaman aşımı içinde bir onaylayan isteği onaylamadıysanız, isteğin süresi dolar. Yeniden denemek için, kullanıcının isteklerini yeniden göndermek zorunda kalır. |
| Reddedildiğinde | Onaylayan bir isteği reddeder. |
| Onaylandı | Onaylayan bir isteği onaylar. |
| Sunma | Kullanıcıya erişim paketindeki tüm kaynaklara **erişim atanmamış.** Bu bir dış Kullanıcı ise, Kullanıcı henüz kaynak dizinine erişmemiş ve onay isteğini kabul etmiş olabilir. |
| Teslim Edildi | Kullanıcıya erişim paketindeki tüm kaynaklara erişim atandı. |
| Genişletilmiş erişim | İlkede uzantılara izin veriliyorsa Kullanıcı atamayı genişletti. |
| Erişim zaman aşımına uğradı | Kullanıcının erişim paketine erişiminin süresi doldu. Yeniden erişim sağlamak için kullanıcının bir istek göndermesi gerekir. |

## <a name="email-notifications"></a>E-posta bildirimleri

Bir onaylıyorsanız, bir erişim isteğini onaylamanız gerektiğinde ve bir erişim isteği tamamlandığında e-posta bildirimleri gönderilir. İstek sahibi olduğunuzda, isteğiniz durumunu belirten e-posta bildirimleri gönderilir.

Aşağıdaki diyagramlarda, bu e-posta bildirimlerinin onaylayanlara veya istek sahibine ne zaman gönderildiği gösterilmektedir. Diyagramda görüntülenecek e-posta bildirimlerine karşılık gelen sayıyı bulmak için [e-posta bildirimleri tablosuna](entitlement-management-process.md#email-notifications-table) başvurun.

### <a name="primary-approvers-and-alternate-approvers"></a>Birincil onaylayanlar ve alternatif onaylayanlar
Aşağıdaki diyagramda, birincil onaylayanlar ve alternatif onaylayanlar deneyimi ve istek işlemi sırasında aldıkları e-posta bildirimleri gösterilmektedir:

![Birincil ve alternatif onaylayanlar işlem akışı](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>İstek sahipleri
Aşağıdaki diyagramda istek süreci sırasında istek sahipleri ve aldıkları e-posta bildirimleri gösterilmektedir:

![İstek sahibi işlem akışı](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>E-posta bildirimleri tablosu
Aşağıdaki tabloda, bu e-posta bildirimlerinin her biri hakkında daha ayrıntılı bilgi verilmektedir. Bu e-postaları yönetmek için kurallarını kullanabilirsiniz. Örneğin, Outlook 'ta, konu bu tablodan sözcükler içeriyorsa e-postaları bir klasöre taşımak için kurallar oluşturabilirsiniz:

| # | E-posta konusu | Gönderildiğinde | Gönderildiği yer |
| --- | --- | --- | --- |
| 1 | Eylem gerekiyor: iletilen isteği *[Date]* tarafından onayla veya Reddet | Bu e-posta, 1. aşama (isteğin ilerletilmiş olduktan sonra) işlem yapması için gönderilir. | Aşama-1 alternatif onaylayan |
| 2 | Eylem gerekli: *[Date]* tarafından istekleri onaylama veya reddetme | Bu e-posta, yükseltme devre dışı bırakılmışsa, işlem yapmak için, 1. aşama birincil onaylayanlara gönderilir. | Aşama-1 birincil onaylayan |
| 3 | Anımsatıcı: [ *istek sahibi]* için *[Date]* isteği onaylama veya reddetme | Bu anımsatıcı e-postası, yükseltme devre dışı bırakılmışsa, yalnızca henüz eylem gerçekleştirmedikleri zaman gerçekleştirilecek şekilde, Aşama 1 birincil onaylayanlara gönderilir. | Aşama-1 birincil onaylayan |
| 4 | [ *Date* ] tarihinde *[Time]* isteği onayla veya Reddet | Bu e-posta, 1. aşama birincil onaylayanlara (yükseltme etkinse) işlem yapması için gönderilir. | Aşama-1 birincil onaylayan |
| 5 | Eylem gerekiyor anımsatıcı: *[istek sahibi]* için *[Date]* isteği onaylama veya reddetme | Bu anımsatıcı e-postası, yükseltme etkinse, yalnızca henüz eylem gerçekleştirmedikleri zaman, işlem yapmak için, Aşama 1 birincil onaylayanlara gönderilir. | Aşama-1 birincil onaylayan |
| 6 | *[Access_package]* için isteğin süresi doldu | Bu e-posta, isteğin süresi dolduktan sonra tek aşamalı veya çok aşamalı bir isteğin Aşama 1 birincil onaylayanlara ve/veya Aşama 1 alternatif onaylayanlara gönderilir. | Aşama-1 birincil onaylayan, Aşama 1 alternatif onaylayan |
| 7 | *[İstek sahibi]* için *[access_package]* için onaylanmış istek | Bu e-posta, bir isteğin tamamlanmasından sonra Aşama 1 birincil onaylayanlara ve/veya Aşama 1 alternatif onaylayanlara gönderilir. | Aşama-1 birincil onaylayan, Aşama 1 alternatif onaylayan |
| 8 | *[İstek sahibi]* için *[access_package]* için onaylanmış istek | Bu e-posta, 1. Aşama-1 birincil onaylayanlara ve/veya Aşama 1 alternatif onaylayanlara, yalnızca Aşama-1 onaylandığında, 2 aşamalı bir istek olarak gönderilir. | Aşama-1 birincil onaylayan, Aşama 1 alternatif onaylayan |
| 9 | *[Access_package]* isteği reddedildi | Bu e-posta yalnızca isteği reddedildiğinde istek sahibine gönderilir | Sahibinin |
| 10 | *[Access_package]* için isteğinizin süresi doldu | Bu e-posta, isteğin süresi dolduktan sonra tek aşamalı veya çok aşamalı bir isteğin sonunda 1. aşamada istek sahibine gönderilir. | Sahibinin |
| 18 | Artık *[access_package]* erişimi var | Bu e-posta, erişimini kullanmaya başlamak için son kullanıcılara gönderilir. | Sahibinin |
| 19 | [ *Access_package]* için erişimi *[Date] tarihine* kadar uzat | Bu e-posta, erişiminin süresi dolmadan önce son kullanıcılara gönderilir. | Sahibinin |
| 20 | *[Access_package]* için erişim sona erdi | Bu e-posta, erişiminin süresi dolduktan sonra son kullanıcılara gönderilir. | Sahibinin |

### <a name="access-request-emails"></a>Erişim isteği e-postaları

Bir istek sahibi onay gerektirecek şekilde yapılandırılmış bir erişim paketi için erişim isteği gönderdiğinde, ilkeye eklenen tüm onaylayanlar isteğin ayrıntılarını içeren bir e-posta bildirimi alır. Ayrıntılar, istek sahibinin adını, kuruluşunu, erişim başlangıç ve bitiş tarihini (sağlanmışsa), iş gerekçe, isteğin gönderildiği ve isteğin ne zaman dolacağını içerir.

E-posta, erişim isteğini onaylamak veya reddetmek için Myaccess ' a tıklayarak bir bağlantı onaylayıcıları içerir. İstek sahibi bir erişim isteği gönderdiğinde bir onaylayana gönderilen örnek bir e-posta bildirimi aşağıda verilmiştir:

![Paket e-postasına erişim isteğini onayla](./media/entitlement-management-shared/approver-request-email.png)

Birincil onaylayanlar de bir e-posta bildirimine bir işlem yapması ve isteğe yönelik bir karar vermek için bir anımsatıcı gönderilir. Aşağıda, bir işlem yapması için hatırlatmak üzere birincil onaylayanlara ait alma bildirimi örnek bir e-postası verilmiştir:

![Anımsatıcı erişim isteği e-postası](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Alternatif onaylayan istek e-postaları

Alternatif onaylayanlara iletim etkinse, iletme ilkesi başına, istek hala beklendiğinde istek iletilir. Alternatif onaylayan, isteği onaylamak veya reddetmek için bir bildirim e-postası alır. Diğer onaylayanların aldığı bildirimin örnek bir e-postası aşağıda verilmiştir:

![Alternatif onaylayan isteği e-postası](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Her ikisi de, birincil onaylayanlar ve alternatif onaylayanlar isteği onaylayabilir veya reddedebilir.

### <a name="approved-or-denied-emails"></a>Onaylanan veya reddedilen e-postalar

İstek sahipleri, erişim istekleri onaylandığında veya erişim için kullanılabilir olduğunda veya erişim istekleri reddedildiğinde bilgilendirilir. Bir onaylayan, istek sahibi tarafından gönderilen bir erişim isteği aldığında, erişim isteğini onaylayabilir veya reddedebilirler. Onaylayanın kararlarına yönelik bir iş gerekçe eklemesi gerekir. Bir istek onaylandıktan sonra birincil veya alternatif onaylayanlara gönderilen örnek bir e-posta aşağıda verilmiştir:

![Erişim isteği e-postasını gözden geçir](./media/entitlement-management-process/approver-request-email-approved.png)

Bir erişim isteği onaylandığında ve erişimleri sağlandığında, istek sahibine artık erişim paketine erişimi olan bir e-posta bildirimi gönderilir. Bir erişim paketine erişim verildiğinde bir istek sahibine gönderilen örnek bir e-posta bildirimi aşağıda verilmiştir:

![Son erişim isteği e-postası](./media/entitlement-management-process/requestor-email-approved.png)

Erişim isteği reddedildiğinde, istek sahibine bir e-posta bildirimi gönderilir. Erişim istekleri reddedildiğinde bir istek sahibine gönderilen örnek bir e-posta bildirimi aşağıda verilmiştir:

![İstek sahibi isteği reddedildi e-postası](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Son erişim isteği e-postaları

Bir onaylayan, isteği onaylamamışsa veya reddetmezse erişim istekleri zaman sona erecektir. 

İstek, yapılandırılan süre sonu tarihine ulaştığında ve süresi dolduğunda, bu, artık onaylayanlar tarafından onaylanamaz veya reddedilmez. Tüm birincil ve alternatif onaylayanlara gönderilen bildirimin örnek bir e-postası aşağıda verilmiştir:

 ![Onaylayanların süre dolma erişimi isteği e-postası](./media/entitlement-management-process/approver-request-email-expired.png)

 Ayrıca, istek sahibine bir e-posta bildirimi gönderilir, bunlara erişim istekleri sona ermiştir ve erişim isteğini yeniden gönderebilmeleri gerekir. Erişim istekleri süresi dolduğunda, istek sahibine gönderilen örnek bir e-posta bildirimi aşağıda verilmiştir:

![İstek sahibinin süre dolduğunda erişim isteği e-postası](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketine erişim isteme](entitlement-management-request-access.md)
- [Erişim isteklerini onaylama veya reddetme](entitlement-management-request-approve.md)
