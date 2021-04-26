---
title: Oturum açtıktan sonra uygulama sayfasında hata iletisi görüntüleniyor | Microsoft Docs
description: Uygulama bir hata iletisi döndürdüğünde Azure AD oturum açma sorunlarını çözme.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379307"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Kullanıcı oturum açtıktan sonra bir uygulama sayfası hata iletisi gösteriyor

Bu senaryoda, Azure Active Directory (Azure AD) kullanıcıyı ' de imzalar. Ancak uygulama bir hata iletisi görüntüler ve kullanıcının oturum açma akışını bitirmesini izin vermez. Bu sorun, uygulamanın Azure AD 'nin verdiği yanıtı kabul etmiyordu.

Uygulamanın Azure AD 'den gelen yanıtı kabul etmemesinin birkaç olası nedeni vardır. Hata iletisi yanıtta eksik olanları açıkça tanımmazsa, aşağıdakileri deneyin:

-   Uygulama Azure AD Galerisi ise, [Azure AD 'de uygulamalar IÇIN SAML tabanlı çoklu oturum açma hatalarını ayıklama](./debug-saml-sso-issues.md)bölümündeki adımları izlemediğinizi doğrulayın.

-   SAML isteği, yanıtı ve belirtecini yakalamak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanın.

-   SAML yanıtını uygulama satıcısına gönderin ve eksik olanları sorun.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML yanıtında öznitelikler eksik

Azure AD yapılandırmasında Azure AD yanıtında gönderilecek bir öznitelik eklemek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Sol taraftaki Gezinti bölmesinin üst kısmında, **tüm hizmetler** ' i seçerek Azure ad uzantısını açın.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, gezinti bölmesinde **Çoklu oturum açma** seçeneğini belirleyin.

8. **Kullanıcı öznitelikleri** bölümünde, **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle**' yi seçin. Burada, kullanıcılar oturum açtığında SAML belirtecinde uygulamaya hangi özniteliklerin gönderileceğini değiştirebilirsiniz.

   Bir öznitelik eklemek için:

   1. **Öznitelik Ekle**' yi seçin. **Adı** girin ve açılan listeden **değeri** seçin.

   1.  **Kaydet**’i seçin. Tabloda yeni özniteliğini görürsünüz.

9. Yapılandırmayı kaydedin.

   Kullanıcı uygulamada bir sonraki sefer oturum açtığında Azure AD, SAML yanıtında yeni özniteliği gönderir.

## <a name="the-app-doesnt-identify-the-user"></a>Uygulama kullanıcıyı tanımlamıyor

SAML yanıtında rol gibi bir öznitelik eksik olduğundan uygulamada oturum açma başarısız olur. Ya da uygulama, **NameID** (Kullanıcı tanımlayıcısı) özniteliği için farklı bir biçim veya değer beklediği için başarısız oldu.

Uygulamada kullanıcıları oluşturmak, sürdürmek ve kaldırmak için [Azure AD otomatik Kullanıcı sağlama](../app-provisioning/user-provisioning.md) kullanıyorsanız, kullanıcının SaaS uygulamasına sağlandığını doğrulayın. Daha fazla bilgi için bkz. [Azure AD Galeri uygulamasına hiçbir Kullanıcı sağlanmıyor](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD uygulama yapılandırmasına öznitelik ekleme

Kullanıcı tanımlayıcısı değerini değiştirmek için şu adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti bölmesinin en üstünde bulunan **tüm hizmetler** ' i seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. SSO için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, gezinti bölmesinde **Çoklu oturum açma** seçeneğini belirleyin.

8. Kullanıcı **öznitelikleri** altında Kullanıcı **tanımlayıcısı** açılan listesinden kullanıcının benzersiz tanımlayıcısını seçin.

## <a name="change-the-nameid-format"></a>NameID biçimini değiştirme

Uygulama, **NameID** (Kullanıcı tanımlayıcısı) özniteliği için başka bir biçim beklediğinde, NameID biçimini değiştirmek Için [NameID 'yi düzenlemeyle](../develop/active-directory-saml-claims-customization.md#editing-nameid) bölümüne bakın.

Azure AD, seçilen değere göre **NameID** özniteliği (Kullanıcı tanımlayıcısı) BIÇIMINI veya SAML authrequest içinde uygulama tarafından istenen biçimi seçer. Daha fazla bilgi için [Çoklu oturum açma SAML protokolünün](../develop/single-sign-on-saml-protocol.md#nameidpolicy)"Nameıdpolicy" bölümüne bakın.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Uygulama, SAML yanıtı için farklı bir imza yöntemi bekliyor

SAML belirtecinin hangi bölümlerinin Azure AD tarafından dijital olarak imzalandığını değiştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti bölmesinin en üstünde bulunan **tüm hizmetler** ' i seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, gezinti bölmesinde **Çoklu oturum açma** seçeneğini belirleyin.

8. **SAML Imzalama sertifikası**' nın altında, **Gelişmiş sertifika imzalama ayarlarını göster**' i seçin.

9. Uygulamanın bu seçenekler arasında beklediği **Imzalama seçeneğini** seçin:

   * **SAML yanıtını imzala**
   * **SAML yanıtını ve onaylama işlemlerini imzala**
   * **SAML onaylama imzası**

   Kullanıcı uygulamada bir sonraki sefer oturum açtığında, Azure AD seçtiğiniz SAML yanıtının bölümünü imzalayacaktır.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Uygulama, SHA-1 imzalama algoritmasını bekliyor

Varsayılan olarak, Azure AD, SAML belirtecini en güvenli algoritmayı kullanarak imzalar. Uygulama SHA-1 gerektirmediği takdirde, imzalama algoritmasını *SHA-1* olarak değiştirmemenizi öneririz.

İmzalama algoritmasını değiştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti bölmesinin en üstünde bulunan **tüm hizmetler** ' i seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol tarafındaki gezinti bölmesinden **Çoklu oturum açma** seçeneğini belirleyin.

8. **SAML Imzalama sertifikası**' nın altında, **Gelişmiş sertifika imzalama ayarlarını göster**' i seçin.

9. **Imzalama algoritması** olarak **SHA-1** ' i seçin.

   Kullanıcı uygulamada bir sonraki sefer oturum açtığında, Azure AD, SAML belirtecini SHA-1 algoritmasını kullanarak imzalayacaktır.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD 'de uygulamalar IÇIN SAML tabanlı çoklu oturum açma hatalarını ayıklama](./debug-saml-sso-issues.md).