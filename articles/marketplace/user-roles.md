---
title: Kullanıcı rolleri ve izinleri atama-Azure Marketi
description: Iş Ortağı Merkezi 'nin ticari Market programındaki kullanıcılara roller ve izinler atamayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 5a395205662e6c9ab0bde0e227f045ca46cfed45
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228274"
---
# <a name="assign-user-roles-and-permissions"></a>Kullanıcı rollerini ve izinlerini atama

**Uygun roller**

- Sahip
- Yönetici

Market veya geliştirici hesabınızla ilgili olanaklarla erişebilmek için, kendinize atanmış aşağıdaki rollerden birine veya daha fazlasına sahip olmanız gerekir. Her rolün, hesap içinde belirli işlevleri gerçekleştirmek için belirli bir izinler kümesi vardır.

| Rol | Açıklama |
| ------------ | ------------- |
| Sahip | Bir yayımcı hesabı oluşturulduğunda, ortağa bir sahiplik rolü atanır.<br><br>İzin ayrıntıları:<br><br>Hesabın tüm sahipliğini &#10004;&#160;&#160;.<br>&#10004;&#160;&#160;Kullanıcı türü ekleme<br>&#10004;&#160;&#160;, ödeme ve vergi bilgilerini tamamlayabilir<br>&#10004;&#160;&#160;geri bildirim raporlarını ve özetlerini görüntüleme<br>&#10004;&#160;&#160;yayımcı hesabını diğer geliştirici programlarına Kaydet |
| Yönetici | Yayımcı hesabı kullanıcılarını, rollerini ve izinlerini yönetmek &#10004;&#160;&#160;<br>Kiracılar &#10004;&#160;&#160;Yönet<br>&#x2718;&#160;&#160;erişim vergisi ve ödeme ayarları |
| Geliştirici | Paketi karşıya yükleme &#10004;&#160;&#160;<br>&#10004;&#160;&#160;uygulamaları ve eklentileri gönder<br>Telemetri ayrıntıları için kullanım raporlarını görüntüleme &#10004;&#160;&#160;<br>Finansal bilgilere veya hesap ayarlarına erişim &#x2718;&#160;&#160; |
| İş Katılımcısı | &#10004;&#160;&#160;finansal bilgilere erişin<br>&#10004;&#160;&#160;fiyatlandırma ayrıntılarını ayarlama<br>Yeni uygulamalar ve eklentiler oluşturma veya gönderme &#x2718;&#160;&#160; |
| Mali katkıda bulunan | &#10004;&#160;&#160;ödeyen raporlarını görüntüleme<br>&#10004;&#160;&#160;ödeme ve vergi profillerini yönetme<br>&#x2718;&#160;&#160;uygulamalarda veya ayarlarda değişiklik yapma |
| Pazarlamacısıdır | &#10004;&#160;&#160;müşteri incelemelerine yanıt verin<br>&#10004;&#160;&#160;finansal olmayan raporları görüntüleme<br>&#x2718;&#160;&#160;uygulamalarda veya ayarlarda değişiklik yapma |
|||

> [!NOTE]
> Ticari Market programı için genel yönetici, Iş katılımcısı, mali katkı ve Pazar layıcı rolleri kullanılmaz. Bu rollerin kullanıcılara atanması etkisizdir. Yalnızca yönetici ve geliştirici rolleri kullanıcılara izinler verir.

Azure Active Directory (AD), bulut çözümü sağlayıcısı (CSP), Denetim Masası satıcısı (CPV), Konuk kullanıcılar veya Microsoft İş Ortağı Ağı (MPN) gibi Iş Ortağı Merkezi alanlarında rol ve izinleri yönetme hakkında daha fazla bilgi için bkz. [Iş Ortağı Merkezi 'nde Kullanıcı rolleri ve Izinleri atama](/partner-center/permissions-overview).

> [!NOTE]
> Tüm Kullanıcı yönetimi, bu satırlarda gerçekleştirilen rol atama etkinlikleri, açık olduğunuz hesap bağlamında olacaktır. Farklı bir hesap yönetmeniz gerekiyorsa, satıcı arasında geçiş yapma bölümündeki bölümüne başvurun.
