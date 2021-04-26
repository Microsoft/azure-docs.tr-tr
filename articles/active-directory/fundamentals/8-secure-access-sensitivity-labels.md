---
title: Duyarlılık etiketleriyle Azure Active Directory kaynaklara yönelik dış erişimi denetleyin.
description: Dış erişim için genel güvenlik planınızın bir parçası olarak duyarlılık etiketlerini kullanın.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565181"
---
# <a name="control-access-with-sensitivity-labels"></a>Duyarlılık etiketleriyle erişimi denetleme 

[Duyarlılık etiketleri](/microsoft-365/compliance/sensitivity-labels) Office 365 uygulamalarındaki Içeriğinize ve Microsoft ekipleri, Microsoft 365 grupları ve SharePoint siteleri gibi kapsayıcılara erişimi denetlemenize yardımcı olur. Kullanıcılarınızın işbirliği ve üretim becerileriyle hindering gerek kalmadan içeriğinizi koruyabilirler. Duyarlılık etiketleri, kuruluşunuzun içeriğini cihazlar, uygulamalar ve hizmetler arasında göndermenizi sağlarken verilerinizi korurken ve uyumluluk ve güvenlik ilkelerinizi karşılamış olursunuz. 

Duyarlılık etiketleriyle şunları yapabilirsiniz:

* **Herhangi bir koruma ayarı eklemeden Içeriği sınıflandırın**. Bir içeriğe (bir etiket gibi) devam eden ve paylaşılan ve paylaşılan içeriklerinizi içeren bir sınıflandırma atayabilirsiniz. Bu sınıflandırmayı kullanarak kullanım raporları oluşturabilir ve hassas içeriğiniz için etkinlik verilerini görebilirsiniz.

* **Şifreleme, Filigranlar ve erişim kısıtlamaları gibi koruma ayarlarını zorlayın**. Örneğin, kullanıcılar bir belgeye veya e-postaya gizli Etiketler uygulayabilir ve bu etiket [içeriği şifreleyebilir](/microsoft-365/compliance/encryption-sensitivity-labels) ve "gizli" bir filigran ekleyebilir. Ayrıca, bir SharePoint sitesi gibi bir [kapsayıcıya duyarlılık etiketi uygulayabilir](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) ve dış kullanıcıların içerdiği içeriğe erişip erişemeyeceğini zorlayabilirsiniz.

E-posta ve diğer içeriklerdeki duyarlılık etiketleri içerikle birlikte seyahat ediyor. Kapsayıcılardaki duyarlılık etiketleri kapsayıcıya erişimi kısıtlayabilir, ancak kapsayıcıdaki içerik etiketi aktarılmaz. Örneğin, bir kullanıcı korumalı bir siteden içerik alabilir, indirebilir ve içerik aynı zamanda bir duyarlılık etiketine sahip olmadığı takdirde kısıtlama olmadan paylaşabilir.

 >[!NOTE]
>Duyarlılık etiketlerini uygulamak için kullanıcıların Microsoft iş veya okul hesabında imzalanması gerekir. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Duyarlılık düzeylerini oluşturmak ve yönetmek için gerekli izinler

Duyarlılık etiketleri oluşturacak uyumluluk takımınızın üyelerinin Microsoft 365 Uyumluluk Merkezi, Microsoft 365 güvenlik merkezi veya güvenlik & Uyumluluk Merkezi için izinleri olması gerekir.

Varsayılan olarak, kiracınız için genel Yöneticiler bu yönetici merkezlerine erişebilir ve bir kiracı yöneticisinin tüm izinlerini vermeden uyumluluk ofisleri ve diğer kişilere erişim sağlayabilir. Bu yetkilendirilmiş sınırlı yönetici erişimi için, uyumluluk verileri Yöneticisi, uyumluluk Yöneticisi veya güvenlik yöneticisi rol grubuna kullanıcı ekleyin.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Duyarlılık etiketi stratejinizi belirleme

İçeriğinize dış erişimi yönetirken göz önünde bulundurun. şunları yapın:

**Tüm içerik ve kapsayıcılar için**

* Yüksek, orta veya düşük Iş etkisi (HBı, MBı, LBI) ne olduğunu nasıl tanımlayacaksınız? Belirli içerik türleri uygun şekilde paylaşılmışsa kuruluşunuzun etkisini göz önünde bulundurun.

   * Kredi kartları veya Passport numaraları gibi belirli bir doğal olarak [hassas içerik](/microsoft-365/compliance/apply-sensitivity-label-automatically)türlerine sahip içerik

   * Belirli gruplar veya kişiler tarafından oluşturulan içerik (örneğin, uyumluluk ofisleri, finansal ofisler veya Yöneticiler)

   * Belirli kitaplıkların veya sitelerdeki içerik. Örneğin, kuruluş stratejisi veya özel finansal verileri barındıran kapsayıcılar

   * Diğer ölçütler

* İçerik kategorileri (örneğin, HBı içeriği) dış kullanıcılar tarafından erişime karşı kısıtlanması gerekir mi?

   * Kısıtlamalar, kapsayıcılara erişimi kısıtlama ve içeriği şifreleme gibi eylemleri içerebilir.

* HBı verileri, siteleri veya Microsoft 365 grupları için varsayılanlar ne gerekir?

* [Şifrelemeyi zorlamak](/microsoft-365/compliance/encryption-sensitivity-labels) veya [kapsayıcı erişim kısıtlamalarını zorlamak](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)için duyarlılık etiketlerini nerede [etiketleyip izlemek](/microsoft-365/compliance/sensitivity-labels)için kullanacaksınız?

**E-posta ve içerik için**

* İçeriğe [duyarlılık etiketlerini otomatik olarak uygulamak](/microsoft-365/compliance/apply-sensitivity-label-automatically) mi yoksa el ile yapmak mi istiyorsunuz?

   * Bunu el ile yapmak istiyorsanız, [kullanıcıların bir etiket uygulaması önerilir](/microsoft-365/compliance/apply-sensitivity-label-automatically)mi?

**Kapsayıcılar için**

* M365 gruplarının, takımların veya SharePoint sitelerinin duyarlılık etiketleri kullanılarak kısıtlanması gerekip gerekmediğini hangi ölçütlere göre belirleyeceğini belirlemektir?

* Yalnızca bu kapsayıcıların içeriğini ileriye doğru etiketlemek mi istiyorsunuz, yoksa SharePoint ve OneDrive 'daki mevcut dosyaları [otomatik olarak etiketlemek](/microsoft-365/compliance/apply-sensitivity-label-automatically) mi istiyorsunuz?

Duyarlılık etiketlerini nasıl kullanabileceğiniz hakkında daha fazla fikir için [duyarlılık etiketleri için bu yaygın senaryolar](/microsoft-365/compliance/get-started-with-sensitivity-labels) bölümüne bakın.

### <a name="sensitivity-labels-on-email-and-content"></a>E-posta ve içeriklerdeki duyarlılık etiketleri

Bir belgeye veya e-postaya duyarlılık etiketi atadığınızda, özelleştirilebilir, şifresiz metin ve kalıcı içeriğe uygulanan bir damga gibi olur. 

* **Özelleştirilebilir** , kuruluşunuz için uygun Etiketler oluşturabileceğiniz ve uygulandıklarında ne olacağını belirleyebilecekleri anlamına gelir.

* **Düz metin** , öğenin meta verilerinin bir parçası olduğu ve kendi koruyucu eylemlerini uygulayabilmeleri için uygulama ve hizmetler tarafından okunabilen anlamına gelir.

* **Kalıcı** , etiketin ve tüm ilişkili korumaların içerikle dolaşımını ve ilkelerin uygulanması ve zorlanmasını temel hale gelmesini gösterir.

 

> [!NOTE]
> Her içerik öğesi için tek bir duyarlılık etiketi uygulanmış olabilir.


### <a name="sensitivity-labels-on-containers"></a>Kapsayıcılarda duyarlılık etiketleri

[Microsoft 365 grupları](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft ekipleri](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)ve [SharePoint siteleri](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)gibi kapsayıcılar için duyarlılık etiketleri uygulayabilirsiniz. Bu duyarlılık etiketini desteklenen bir kapsayıcıya uyguladığınızda, etiket otomatik olarak bağlı siteye veya gruba sınıflandırma ve koruma ayarlarını uygular. Bu kapsayıcılardaki duyarlılık etiketleri, kapsayıcıların aşağıdaki yönlerini denetleyebilir:

* **Gizlilik**. Siteyi kimlerin görebileceğini seçebilirsiniz: belirli kullanıcılar, tüm iç kullanıcılar veya herkes.

* **Dış Kullanıcı erişimi**. Grup sahibinin gruba Konuk ekleyip ekleyemeyeceğini denetler.

* **Yönetilmeyen cihazlardan erişim**. Yönetilmeyen cihazların içeriğe erişip erişemeyeceğini ve nasıl erişebileceğini belirler.

 

![Duyarlılık etiketlerinin düzenlenmesinin ekran görüntüsü](media/secure-external-access/8-edit-label.png)

 

Bir kapsayıcı etiketini SharePoint sitesi gibi bir kapsayıcıya uyguladığınızda, bu içeriğe uygulanmaz: kapsayıcılardaki duyarlılık etiketleri, kapsayıcıdaki içeriğe erişimi denetler. 

* Kapsayıcının içindeki içeriğe otomatik olarak Etiketler uygulamak isterseniz, bkz. [içeriğe duyarlılık için otomatik olarak duyarlılık uygulama](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Kullanıcıların bu içeriğe el ile Etiketler uygulayabilmesini istiyorsanız [SharePoint ve OneDrive 'Da Office dosyaları için duyarlılık etiketlerini](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)etkinleştirdiğinizden emin olun.

### <a name="plan-to-implement-sensitivity-labels"></a>Duyarlılık etiketlerini uygulamayı planlayın

Duyarlılık etiketlerini nasıl kullanmak istediğinizi belirledikten ve bunları uygulamak istediğiniz içerik ve sitelere sahip olduktan sonra, uygulamanızı gerçekleştirmenize yardımcı olması için aşağıdaki belgelere bakın.

1. [Duyarlılık etiketleriyle çalışmaya başlama](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Dağıtım stratejisi oluşturma](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Duyarlılık etiketleri oluşturma ve yayımlama](/microsoft-365/compliance/create-sensitivity-labels)

4. [Şifrelemeyi uygulamak için duyarlılık etiketlerini kullanarak içeriğe erişimi kısıtlama](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Takımlar, gruplar ve siteler ile duyarlılık etiketlerini kullanın](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [SharePoint ve OneDrive 'da Office dosyaları için duyarlılık etiketlerini etkinleştirin](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için istediğiniz güvenlik duruşunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md) (burada bulabilirsiniz.)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)