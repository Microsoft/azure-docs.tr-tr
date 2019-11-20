---
title: Azure AD'de Federasyon sertifikalarını yönetme | Microsoft Docs
description: Federasyon sertifikalarınızı sona erme tarihini özelleştirme ve yakında sona erecek sertifikaları yenile öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 416e72f7e9e8622f044f7bb6430fbb36010b164e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701981"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Federasyon çoklu oturum açma için Azure Active Directory'de sertifikaları yönetme

Bu makalede, yaygın soruları ve Azure Active Directory (Azure AD), yazılım için Federasyon çoklu oturum açma (SSO) bir hizmet (SaaS) uygulamaları kurmak için oluşturduğu sertifikalar ilgili bilgileri kapsar. Uygulamaları Azure AD uygulama galerisinde veya galeri dışı uygulama şablonu kullanarak ekleyin. Federasyon SSO seçeneği kullanarak uygulamayı yapılandırın.

Bu makalede, Azure AD SSO ile kullanacak şekilde yapılandırılan uygulamalar için uygundur [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) Federasyon.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Galeri ve galeri dışı uygulamalar için otomatik olarak oluşturulan sertifika

Ne zaman galeriden yeni bir uygulama eklemek ve bir SAML tabanlı oturum açmayı yapılandırın (seçerek **çoklu oturum açma** > **SAML** uygulama genel bakış sayfasında), Azure AD oluşturur bir üç yıl boyunca geçerli uygulama için sertifika. Güvenlik sertifikası etkin sertifikayı indirmek için ( **.cer**) dosyası, bu sayfaya dönün (**SAML tabanlı oturum açma**) ve içinde bir indirme bağlantısı seçin **SAML imzalama sertifikası** başlığı. Ham (ikili) sertifikası veya Base64 (taban 64 ile kodlanmış metin) arasında seçim yapabilirsiniz. Galeri uygulamaları için bu bölümde Ayrıca sertifikayı Federasyon meta verileri XML dosyası indirmek için bir bağlantı gösterebilir (bir **.xml** dosyası), uygulamanın gereksinim bağlı olarak.

![SAML etkin imzalama sertifikası yükleme seçenekleri](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Etkin veya etkin olmayan bir sertifika seçerek de indirebilirsiniz **SAML imzalama sertifikası** başlık **Düzenle** görüntüler (bir kalem), simge **SAML imzalama sertifikası** sayfası. Öğesinin üç noktasını ( **...** ) indirin ve ardından hangi sertifika biçimi istediğiniz sertifikanın yanındaki istediğiniz. Sertifikayı gizliliği artırılmış posta (PEM) biçiminde indirmek için ek bir seçeneğiniz vardır. Base64 için bu biçim aynıdır ancak bir **.pem** dosya adı uzantısı, Windows Sertifika biçimi olarak tanınmıyor.

![SAML imzalama sertifikası yükleme seçenekleri (etkin ve etkin olmayan)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Federasyon sertifikanız için sona erme tarihi özelleştirebilir ve yeni bir sertifika atla

Varsayılan olarak, Azure sertifika sırasında SAML çoklu oturum açma yapılandırması ne zaman otomatik olarak oluşturulur üç yıl sonra süresi dolacak şekilde yapılandırır. Kaydedildikten sonra bir sertifika tarihi değiştirilemiyor çünkü için gerekenler:

1. İstenilen tarihte ile yeni bir sertifika oluşturun.
1. Yeni sertifikayı kaydeder.
1. Doğru biçimde yeni sertifikayı yükleyin.
1. Uygulamaya yeni sertifikayı yükleyin.
1. Azure Active Directory portalındaki yeni sertifikayı etkinleştirin.

Aşağıdaki iki bölümü adımları gerçekleştirmenize yardımcı olur.

### <a name="create-a-new-certificate"></a>Yeni bir sertifika oluşturun

İlk olarak, oluşturma ve bir farklı sona erme tarihi ile yeni sertifikayı kaydeder:

1. Oturum [Azure Active Directory portalında](https://aad.portal.azure.com/). **Azure Active Directory Yönetim Merkezi** sayfası görüntülenir.
1. Sol bölmede **Kurumsal uygulamalar**’ı seçin. Hesabınızdaki kurumsal uygulamalar listesi görüntülenir.
1. Etkilenen uygulaması'nı seçin. Uygulama için bir genel bakış sayfası görüntülenir.
1. Uygulama genel bakış sayfasının sol bölmesinde seçin **çoklu oturum açma**.
1. Varsa **tek bir oturum açma yönteminizi seçmeniz** sayfası görüntülenirse, seçin **SAML**.
1. İçinde **yukarı çoklu oturum açma SAML - Preview ile ayarlanmış** sayfasında, bulmak **SAML imzalama sertifikası** seçin ve başlık **Düzenle** simgesi (Kalem). **SAML imzalama sertifikası** sayfası görüntülenirse, durumunu görüntüleyen (**etkin** veya **Inactive**), sona erme tarihi ve her bir sertifikanın parmak izi (karma dize).
1. Seçin **yeni sertifika**. Yeni bir satır, burada sona erme tarihi geçerli tarihten sonra tam olarak üç yıl için varsayılan olarak sertifika listesi altında görünür. (Sona erme tarihi hala değiştirebilmesi değişikliklerinizi henüz kaydedilmemiş.)
1. Yeni sertifika satırda sona erme tarihi sütunların üzerine gelin ve seçin **tarihi seçin** simgesi (Takvim). Yeni satırın geçerli sona erme tarihi ayın günü görüntüleme bir Takvim denetimi görünür.
1. Yeni bir tarihi ayarlamak için takvim denetimini kullanın. Geçerli tarih ve geçerli tarihten sonra üç yıl arasında herhangi bir tarihi ayarlayabilirsiniz.
1. **Kaydet**’i seçin. Yeni sertifikayı durumuyla görünür **Inactive**, sona erme tarihi, belirttiğiniz, seçtiğiniz ve parmak izi.
1. Seçin **X** dönmek için **yukarı çoklu oturum açma SAML - Preview ile ayarlanmış** sayfası.

### <a name="upload-and-activate-a-certificate"></a>Karşıya yükleme ve sertifikayı etkinleştir

Ardından, doğru biçimde yeni sertifikayı indirip uygulamayı yükleyin ve Azure Active Directory'de etkin hale getirin:

1. Uygulamanın ek SAML oturum açma yapılandırma yönergeleri ile görüntüleyin:

   - seçme **Yapılandırma Kılavuzu** ayrı bir tarayıcı penceresi veya sekmesi içinde görüntülemek için bağlantıyı veya
   - gidip **ayarlanan** başlık ve seçerek **görüntülemek hakkında adım adım yönergeler** Kenar çubuğunda görüntülemek için.

1. Yönergelerde sertifika karşıya yükleme için gerekli kodlama biçimi unutmayın.
1. Bölümündeki yönergeleri [galeri ve galeri dışı uygulamalar için otomatik olarak oluşturulan sertifika](#auto-generated-certificate-for-gallery-and-non-gallery-applications) bölümüne. Bu adım, kodlama biçimi uygulama tarafından karşıya yükleme için gerekli sertifika indirir.
1. Yeni sertifikayı gece yarısında değil istediğiniz zaman geri dönüp **SAML imzalama sertifikası** sayfasında ve yeni kaydedilen sertifikayı satırda üç noktayı seçin ( **...** ) seçip **sertifika etkin hale getirin**. Yeni sertifika durumu değişiklikleri **etkin**, ve daha önce etkin sertifika için bir durumunun değiştiğini **Inactive**.
1. SAML imzalama yükleyebilirsiniz. böylece, daha önce görüntülenen uygulamanın SAML oturum açma yapılandırma yönergeleri kodlama biçimi doğru sertifika aşağıdaki devam edin.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Sertifika süre sonu için e-posta bildirim adresleri ekleyin

Azure AD, SAML sertifikanın süresi dolmadan önce bir e-posta bildirimi 60, 30 ve 7 gün gönderir. Bildirimleri almak için birden fazla e-posta adresi ekleyebilirsiniz. Bildirimlerin gönderilmesini istediğiniz e-posta adresleri belirtmek için:

1. İçinde **SAML imzalama sertifikası** sayfasında, Git **bildirim e-posta adreslerini** başlığı. Varsayılan olarak, bu başlığı, yalnızca uygulamaya eklenen yönetici e-posta adresini kullanır.
1. Aşağıdaki son e-posta adresi, sertifikanın süre sonu bildirimi alırsınız ve ardından Enter tuşuna basın e-posta adresini yazın.
1. Önceki adımı, eklemek istediğiniz her bir e-posta adresi için yineleyin.
1. Silmek istediğiniz her e-posta adresi için seçin **Sil** e-posta adresinin yanındaki simge (Çöp Kutusu).
1. **Kaydet**’i seçin.

Bildirim e-postası alırsınız aadnotification@microsoft.com. İstenmeyen posta konumunuza giden e-posta önlemek için bu e-posta kişilerinize ekleyin.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Süresi yakında dolacak bir sertifikayı yenileme

Süresi dolmak üzere bir sertifika ise, kullanıcılarınızın önemli kapalı kalma süresi sonuçlanır bir yordamı kullanarak yenileyebilirsiniz. Süresi dolacak bir sertifikayı yenilemek için:

1. Bölümündeki yönergeleri [yeni bir sertifika oluşturmak](#create-a-new-certificate) daha önce çakışan tarih olan mevcut sertifikayı kullanarak bölümü. Bu tarih ile sertifika sona erme neden olduğu çalışmama süresi miktarını sınırlar.
1. Uygulamayı otomatik olarak bir sertifika geri alabilirsiniz, yeni sertifikayı etkin olarak aşağıdaki adımları izleyerek ayarlayın:
   1. Geri Git **SAML imzalama sertifikası** sayfası.
   1. Yeni kaydedilen sertifikayı satırda üç noktayı seçin ( **...** ) ve ardından **sertifika etkin hale getirin**.
   1. Sonraki iki adımı atlayın.

1. Uygulamayı aynı anda yalnızca bir sertifika işleyebilir, bir sonraki adımı gerçekleştirmeniz için bir kapalı kalma süresi aralığı seçin. (Uygulama otomatik olarak yeni sertifikayı seçin değil, ancak birden fazla imzalama sertifikası işleyebilir, aksi halde, sonraki adıma dilediğiniz zaman gerçekleştirebilirsiniz.)
1. Eski sertifikayı süresi dolmadan önce yönergeleri [karşıya yükleme ve sertifikayı etkinleştirmek](#upload-and-activate-a-certificate) bölümüne.
1. Sertifika doğru şekilde çalıştığından emin olmak için uygulamaya oturum açın.

## <a name="related-articles"></a>İlgili makaleler

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler](../saas-apps/tutorial-list.md)
- [Azure Active Directory ile uygulama yönetimi](what-is-application-management.md)
- [Azure Active Directory'de uygulamalar için çoklu oturum açma](what-is-single-sign-on.md)
- [Azure Active Directory'de uygulamalar için SAML tabanlı çoklu oturum açma hata ayıklama](../develop/howto-v1-debug-saml-sso-issues.md)
