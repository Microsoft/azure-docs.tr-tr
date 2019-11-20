---
title: Self servis parola sıfırlama raporları-Azure Active Directory
description: Azure AD self servis parola sıfırlama olaylarında raporlama
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ef15bd1fe922aa44227d2fea3d882d4320146f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786722"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Azure AD parola yönetimi için raporlama seçenekleri

Dağıtımdan sonra, birçok kuruluş, kendi kendine parola sıfırlama (SSPR) hizmetinin nasıl veya ne sıklıkta kullanıldığını öğrenmek ister. Azure Active Directory (Azure AD) tarafından sağlanan raporlama özelliği, önceden oluşturulmuş raporları kullanarak soruları yanıtlamanıza yardımcı olur. Uygun şekilde lisanslandıysanız özel sorgular da oluşturabilirsiniz.

![Azure AD 'de denetim günlüklerini kullanarak SSPR üzerinde raporlama][Reporting]

Aşağıdaki sorular [Azure Portal](https://portal.azure.com/)var olan raporlar tarafından yanıtlanabilmelidir:

> [!NOTE]
> [Genel yönetici](../users-groups-roles/directory-assign-admin-roles.md)olmanız ve bu verilerin kuruluşunuz adına toplanması için kabul etmeniz gerekir. Kabul etmek için **Raporlama** sekmesini veya denetim günlüklerini en az bir kez ziyaret etmeniz gerekir. Bu durumda, veriler kuruluşunuz için toplanmaz.
>

* Parola sıfırlama için kaç kişi kaydettirildi?
* Parola sıfırlama için kaydoldu?
* Kişiler hangi verileri kaydediyor?
* Son yedi gün içinde kaç kişi parolalarını sıfırlıyor?
* Kullanıcıların veya yöneticilerin parolalarını sıfırlamak için kullandığı en yaygın yöntemler nelerdir?
* Parola sıfırlamayı kullanmaya çalışırken kullanıcıların veya yöneticilerin karşılaştığı yaygın sorunlar nelerdir?
* Yöneticiler kendi parolalarını sıklıkla sıfırlıyor mu?
* Parola sıfırlama ile ilgili şüpheli bir etkinlik var mı?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Azure portal parola yönetimi raporlarını görüntüleme

Azure portal deneyiminde, parola sıfırlama ve parola sıfırlama kayıt etkinliğini görüntüleme yönteminizi geliştirdik. Parola sıfırlama ve parola sıfırlama kaydı olaylarını bulmak için aşağıdaki adımları kullanın:

1. [Azure portala](https://portal.azure.com) gidin.
2. Sol bölmedeki **tüm hizmetler** ' i seçin.
3. Hizmetler listesinde **Azure Active Directory** arayın ve seçin.
4. Yönet bölümünden **Kullanıcılar** ' ı seçin.
5. **Kullanıcılar** dikey penceresinden **Denetim günlükleri** ' ni seçin. Bu, dizininizdeki tüm kullanıcılara karşı gerçekleşen tüm denetim olaylarını gösterir. Parolayla ilgili tüm olayları görmek için bu görünüme filtre uygulayabilirsiniz.
6. Bölmenin üst kısmındaki **filtre** menüsünde, **hizmet** açılır listesini seçin ve **self servis parola yönetimi** hizmet türüyle değiştirin.
7. İsteğe bağlı olarak, ilgilendiğiniz belirli **etkinlikleri** seçerek listeye daha fazla filtre uygulayın.

### <a name="converged-registration-preview"></a>Yakınsama kaydı (Önizleme)

Yakınsama kaydının genel önizlemesine katılımını yapıyorsanız, denetim günlüklerinde Kullanıcı etkinliğiyle ilgili bilgiler hizmet **kimlik doğrulama yöntemleri**altında bulunur.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Azure portal rapor sütunlarının açıklaması

Aşağıdaki listede, Azure portal rapor sütunlarının her biri ayrıntılı olarak açıklanmaktadır:

* **Kullanıcı**: parola sıfırlama kayıt işlemini deneyen kullanıcı.
* **Rol**: dizindeki kullanıcının rolü.
* **Tarih ve saat**: girişim tarihi ve saati.
* **Kayıtlı veri**: parola sıfırlama kaydı sırasında kullanıcının verdiği kimlik doğrulama verileri.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Azure portal rapor değerlerinin açıklaması

Aşağıdaki tabloda, Azure portal her bir sütun için ayarlayabileceğiniz farklı değerler açıklanmaktadır:

| Sütun | İzin verilen değerler ve anlamları |
| --- | --- |
| Kayıtlı veriler |**Alternatif e-posta**: Kullanıcı, kimlik doğrulamak için alternatif bir e-posta veya kimlik doğrulama e<p><p>**Ofis telefonu**: Kullanıcı, kimlik doğrulamak için bir Office telefonu kullandık.<p>**Cep telefonu**: Kullanıcı, kimlik doğrulamak için bir cep telefonu veya kimlik doğrulama telefonu kullandık.<p>**Güvenlik soruları**: Kullanıcı, kimlik doğrulaması için güvenlik soruları kullandı.<p>**Önceki yöntemlerin herhangi bir birleşimi (örneğin, alternatif e-posta + cep telefonu**), iki geçit bir ilke belirtildiğinde oluşur ve kullanıcının parola sıfırlama isteğinde kimlik doğrulaması için kullandığı iki yöntemi gösterir. |

## <a name="self-service-password-management-activity-types"></a>Self servis parola yönetimi etkinlik türleri

Aşağıdaki etkinlik türleri **self servis parola yönetimi** denetim olay kategorisinde görünür:

* [Self servis parola sıfırlaması engellendi](#activity-type-blocked-from-self-service-password-reset): bir kullanıcının bir parolayı sıfırlamasını, belirli bir ağ geçidini kullanmasını veya 24 saat içinde beş ' dan fazla kez bir telefon numarası doğrulamasını denediğini belirtir.
* [Parolayı değiştirme (self servis)](#activity-type-change-password-self-service): kullanıcının gönüllü olarak veya zorlamalı (süre sonu) parola değişikliğini yaptığını gösterir.
* [Parolayı Sıfırla (yöneticiye göre)](#activity-type-reset-password-by-admin): bir yöneticinin Azure Portal Kullanıcı adına bir parola sıfırlaması yaptığını belirtir.
* [Parola sıfırlama (self servis)](#activity-type-reset-password-self-service): kullanıcının [Azure AD parola sıfırlama portalından](https://passwordreset.microsoftonline.com)parolasını başarıyla sıfırladığı anlamına gelir.
* [Self servis parola sıfırlama akışı etkinlik ilerlemesi](#activity-type-self-serve-password-reset-flow-activity-progress): bir kullanıcının, parola sıfırlama işleminin bir parçası olarak belirli bir parola sıfırlama kimlik doğrulama kapısını geçirme gibi ilerlemesinin devam ettiğini gösterir.
* [Kullanıcı hesabının kilidini açma (self servis)](#activity-type-unlock-a-user-account-self-service)): hesabın kilit açma özelliğinin Active Directory özelliğini kullanarak, kullanıcının [Azure AD parola sıfırlama portalından](https://passwordreset.microsoftonline.com) parolalarını sıfırlamadan Active Directory hesabının kilidini başarıyla kilitdiğini belirtir sıfırlama olmadan.
* [Self servis parola sıfırlama için kayıtlı Kullanıcı](#activity-type-user-registered-for-self-service-password-reset): bir kullanıcının, geçerli olarak belirtilen kiracı parolası sıfırlama ilkesine uygun olarak parolasını sıfırlayabilmesi için gerekli tüm bilgileri kaydetmediğini belirtir.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Etkinlik türü: Self servis parola sıfırlaması engellendi

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının bir parolayı sıfırlamasını, belirli bir kapısı kullanmasını veya 24 saat içinde beş ' dan fazla kez bir telefon numarasını doğrulamasını denediğini belirtir.
* **Etkinlik aktör**: ek sıfırlama işlemleri gerçekleştirmeyi azaltılan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: ek sıfırlama işlemleri gerçekleştirmeyi azaltılan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik durumu**:
  * _Başarı_: bir kullanıcının herhangi bir ek sıfırlama işlemi gerçekleştirmesini, ek kimlik doğrulama yöntemleri gerçekleştirmeye veya sonraki 24 saat boyunca ek telefon numaralarını doğrulamaya yönelik olduğunu gösterir.
* **Etkinlik durumu hata nedeni**: uygulanamaz.

### <a name="activity-type-change-password-self-service"></a>Etkinlik türü: parolayı değiştirme (self servis)

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının gönüllü olarak veya zorlamalı (süre sonu) parola değişikliğini yaptığını gösterir.
* **Etkinlik aktör**: parolasını değiştiren kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: parolasını değiştiren kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik durumları**:
  * _Başarılı_: bir kullanıcının parolasını başarıyla değiştirdiği anlamına gelir.
  * _Hata_: bir kullanıcının parolasını değiştiremediğini belirtir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.
* **Etkinlik durumu hata nedeni**:
  * _FuzzyPolicyViolationInvalidPassword_: Kullanıcı, Microsoft tarafından yasaklanmış parola algılama özellikleri çok yaygın veya özellikle zayıf bir şekilde bulduğu için otomatik olarak yasaklanmış bir parolayı seçti.

### <a name="activity-type-reset-password-by-admin"></a>Etkinlik türü: parolayı sıfırlama (yönetici tarafından)

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir yöneticinin Azure Portal Kullanıcı adına bir parola sıfırlaması yaptığını gösterir.
* **Etkinlik aktör**: başka bir son kullanıcı veya yönetici adına parola sıfırlamayı gerçekleştiren yönetici. Parola Yöneticisi, Kullanıcı Yöneticisi veya Yardım Masası Yöneticisi olmalıdır.
* **Etkinlik hedefi**: parolası sıfırlanan Kullanıcı. Kullanıcı bir son kullanıcı veya farklı bir yönetici olabilir.
* **Etkinlik durumları**:
  * _Başarılı_: bir yöneticinin bir kullanıcının parolasını başarıyla sıfırladığını gösterir.
  * _Hata_: bir yöneticinin bir kullanıcının parolasını değiştiremediğini belirtir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.

### <a name="activity-type-reset-password-self-service"></a>Etkinlik türü: parolayı sıfırlama (self servis)

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının [Azure AD parola sıfırlama portalından](https://passwordreset.microsoftonline.com)parolasını başarıyla sıfırladığını gösterir.
* **Etkinlik aktör**: parolasını sıfırlayan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: parolasını sıfırlayan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik durumları**:
  * _Başarı_: bir kullanıcının kendi parolasını başarıyla sıfırlamasını belirtir.
  * _Hata_: bir kullanıcının kendi parolasını sıfırlamasının başarısız olduğunu gösterir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.
* **Etkinlik durumu hata nedeni**:
  * _FuzzyPolicyViolationInvalidPassword_: yönetici, Microsoft yasaklanmış parola algılama özellikleri çok yaygın veya özellikle zayıf olduğunu bulduğundan, otomatik olarak yasaklanmış bir parola seçti.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Etkinlik türü: Self servis parola sıfırlama akış etkinliği ilerleme durumu

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının (belirli bir parola sıfırlama kimlik doğrulama kapısını geçirme gibi) parola sıfırlama işleminin bir parçası olarak devam eden her bir adımı gösterir.
* **Etkinlik aktör**: parola sıfırlama akışının bir parçasını gerçekleştiren kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: parola sıfırlama akışının bir parçasını gerçekleştiren kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik durumları**:
  * _Başarılı_: bir kullanıcının parola sıfırlama akışının belirli bir adımını başarıyla tamamladığını gösterir.
  * _Hata_: parola sıfırlama akışının belirli bir adımının başarısız olduğunu gösterir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.
* **Etkinlik durumu nedenleri**: [tüm izin verilen sıfırlama etkinliği durum nedenleri](#description-of-the-report-columns-in-the-azure-portal)için aşağıdaki tabloya bakın.

### <a name="activity-type-unlock-a-user-account-self-service"></a>Etkinlik türü: Kullanıcı hesabının kilidini açma (self servis)

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının, sıfırlama olmadan hesap kilit açma özelliğinin Active Directory özelliğini kullanarak Active Directory HESABıNı [Azure AD parola sıfırlama portalından](https://passwordreset.microsoftonline.com) başarıyla kilitleyip kilitdiğini gösterir.
* **Etkinlik aktör**: kendi parolasını sıfırlamadan hesaplarının kilidini açan kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: kendi parolasını sıfırlamadan hesaplarının kilidini açan kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Izin verilen etkinlik durumları**:
  * Başarılı: bir kullanıcının kendi hesabını başarıyla _Kilitduğunu_belirtir.
  * _Hata_: bir kullanıcının hesabının kilidini açmadığını gösterir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Etkinlik türü: Kullanıcı self servis parola sıfırlama için kaydedildi

Aşağıdaki listede bu etkinlik ayrıntılı olarak açıklanmaktadır:

* **Etkinlik açıklaması**: bir kullanıcının şu anda belirtilen kiracı parolası sıfırlama ilkesine uygun olarak parolasını sıfırlayabilmesi için gerekli tüm bilgileri kaydettirdiğini belirtir. 
* **Etkinlik aktör**: parola sıfırlama için kaydolan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Etkinlik hedefi**: parola sıfırlama için kaydolan Kullanıcı. Kullanıcı bir son kullanıcı veya yönetici olabilir.
* **Izin verilen etkinlik durumları**:
  * _Başarılı_: bir kullanıcının geçerli ilkeye uygun olarak parola sıfırlama için başarıyla kaydedildiğini belirtir. 
  * _Hata_: bir kullanıcının parola sıfırlama için kaydolmadığını gösterir. Hatanın neden gerçekleştiği hakkında daha fazla bilgi edinmek için **etkinlik durumu nedeni** kategorisini görmek üzere satırı seçebilirsiniz.

     >[!NOTE]
     >Hata, bir kullanıcının kendi parolasını sıfırlayamadıkları anlamına gelmez. Bu, kayıt işlemini bitiremediği anlamına gelir. Hesabında, doğrulanmamış bir telefon numarası gibi, bu telefon numarasını doğrulamasa bile, doğru olmayan bir şekilde hesaba yönelik doğrulanmamış veriler varsa, bu numarayı, parolasını sıfırlamak için kullanmaya devam edebilirler.

## <a name="next-steps"></a>Sonraki adımlar

* [SSPR ve MFA kullanımı ve öngörüleri raporlama](howto-authentication-methods-usage-insights.md)
* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md).
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md).
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir şeyin bozuk olduğunu düşünüyorum. SSPR sorunlarını gidermek Nasıl yaparım??](active-directory-passwords-troubleshoot.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Azure AD 'de SSPR etkinlik denetim günlükleri örneği"
