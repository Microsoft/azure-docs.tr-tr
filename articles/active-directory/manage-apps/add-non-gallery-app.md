---
title: Galeri dışı bir uygulama ekleme-Microsoft Identity platform | Microsoft Docs
description: Azure AD kiracınıza Galeri olmayan bir uygulama ekleyin.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6656361fd4634c46cd5216b57eb8465536319f09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062795"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Azure AD kuruluşunuza listelenmemiş (Galeri olmayan) bir uygulama ekleme

[Azure AD uygulama galerisindeki](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)seçeneklere ek olarak, **Galeri dışı bir uygulama**ekleme seçeneğiniz vardır. Kuruluşunuzda zaten var olan herhangi bir uygulamayı veya Azure AD Galerisi 'nin parçası olmayan bir satıcıdan herhangi bir üçüncü taraf uygulamayı ekleyebilirsiniz. [Lisans sözleşmenize](https://azure.microsoft.com/pricing/details/active-directory/)bağlı olarak aşağıdaki yetenekler mevcuttur:

- [Security assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) kimlik sağlayıcılarını destekleyen tüm uygulamaların self servis TÜMLEŞTIRMESI (SP tarafından başlatılan veya IDP-başlatıldı)
- [Parola tabanlı SSO](what-is-single-sign-on.md#password-based-sso) kullanarak HTML tabanlı bir oturum açma sayfasına sahip herhangi bir Web uygulamasının self servis tümleştirmesi
- [Kullanıcı sağlaması Için etki alanları arası kimlik yönetimi (SCıM) protokolü Için sistemi](use-scim-to-provision-users-and-groups.md) kullanan uygulamaların Self Servis bağlantısı
- [Office 365 uygulama başlatıcısı](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) veya [Azure AD erişim paneli](what-is-single-sign-on.md#linked-sign-on) 'nde herhangi bir uygulamaya bağlantı ekleme özelliği

Bu makalede, kod yazmadan Azure portal Galeri olmayan bir uygulamanın **kurumsal uygulamalara** nasıl ekleneceği açıklanmaktadır. Bunun yerine, özel uygulamaları Azure AD ile tümleştirme hakkında Geliştirici Kılavuzu arıyorsanız, bkz. [Azure AD Için kimlik doğrulama senaryoları](../develop/authentication-scenarios.md). Kullanıcıların kimliğini doğrulamak için [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) gibi modern bir protokol kullanan bir uygulama geliştirirken, Azure Portal [uygulama kayıtları](../develop/quickstart-register-app.md) deneyimini kullanarak Microsoft Identity platformuna kaydedebilirsiniz.

## <a name="add-a-non-gallery-application"></a>Galeri dışı bir uygulama ekleme

1. Microsoft Identity Platform yönetici hesabınızı kullanarak [Azure Active Directory portalında](https://aad.portal.azure.com/) oturum açın.

2. **Yeni uygulama** > **Kurumsal uygulamalar** ' ı seçin.

3. (İsteğe bağlı ancak önerilir) **Azure AD galerisine gözatıp** arama kutusuna uygulamanın görünen adını girin. 

4. **Kendi uygulamanızı oluştur ' u**seçin. **Kendi uygulamanızı oluşturma** sayfası görüntülenir.

   ![Uygulama ekleme](media/add-non-gallery-app/create-your-own-application.png)

5. Yeni uygulamanızın görünen adını yazmaya başlayın. Benzer adlara sahip Galeri uygulamaları varsa, bunlar arama sonuçları listesinde görünür.

   > [!NOTE]
   > Mümkün olduğunda uygulamanızın Galeri sürümünü kullanmanızı öneririz. Eklemek istediğiniz uygulama arama sonuçlarında görünürse, uygulamayı seçin ve bu yordamın geri kalanını atlayın.

6. **Uygulamanızla ne yapmak istiyorsunuz? bölümünde,** **galeride bulamadıysanız herhangi bir uygulamayı tümleştirin '** ı seçin. Bu seçenek genellikle SAML ve WS-beslik uygulamalar için kullanılır.

   > [!NOTE]
   > Diğer iki seçenek aşağıdaki senaryolarda kullanılır:
   >* **Uygulama ara sunucusunu şirket içi bir uygulamaya güvenli uzaktan erişim için** yapılandırma, Azure AD uygulama ara sunucusu ve bağlayıcıları için yapılandırma sayfasını açar.
   >* **Azure AD ile tümleştirmek üzere çalıştığınız bir uygulamayı kaydedin** **uygulama kayıtları** sayfasını açar. Bu seçenek genellikle OpenID Connect uygulamaları için kullanılır.

7. **Oluştur**'u seçin. Uygulamaya **genel bakış** sayfası açılır.

## <a name="configure-user-sign-in-properties"></a>Kullanıcı oturum açma özelliklerini yapılandırma

1. Özellikler bölmesini düzenlenmek üzere açmak için **Özellikler** ' i seçin.

    ![Özellikler bölmesini Düzenle](media/add-non-gallery-app/edit-properties.png)

2. Uygulamanın atandığı veya atanmamış olduğu kullanıcıların uygulamada oturum açıp görebilmesine ve bir kullanıcının erişim panelinde uygulamayı görebilmesi için aşağıdaki seçenekleri ayarlayın.

    - **Kullanıcıların oturum açması için etkinleştirildi**, uygulamaya atanan kullanıcıların oturum açıp açamayacağını belirler.
    - **Gerekli Kullanıcı Ataması** , uygulamaya atanmamış kullanıcıların oturum açıp açamayacağını belirler.
    - **Kullanıcıya görünür**, uygulamaya atanan kullanıcıların uygulamayı erişim panelinde ve O365 başlatıcısında görüp göremeyeceğini belirler.

      **Atanan** kullanıcılar için davranış:

       | Uygulama özelliği ayarları | | | Atanan kullanıcı deneyimi | |
       |---|---|---|---|---|
       | Kullanıcıların oturum açması için etkinleştirildi mi? | Kullanıcı ataması gerekli mi? | Kullanıcılara görünür mü? | Atanan kullanıcılar oturum açabilir mi? | Atanan kullanıcılar uygulamayı görebilir mi?* |
       | evet | evet | evet | evet | evet  |
       | evet | evet | hayır  | evet | hayır   |
       | evet | hayır  | evet | evet | evet  |
       | evet | hayır  | hayır  | evet | hayır   |
       | hayır  | evet | evet | hayır  | hayır   |
       | hayır  | evet | hayır  | hayır  | hayır   |
       | hayır  | hayır  | evet | hayır  | hayır   |
       | hayır  | hayır  | hayır  | hayır  | hayır   |

      **Atanmayan** kullanıcılar için davranış:

       | Uygulama özelliği ayarları | | | Atanmayan kullanıcı deneyimi | |
       |---|---|---|---|---|
       | Kullanıcıların oturum açması için etkinleştirildi mi? | Kullanıcı ataması gerekli mi? | Kullanıcılara görünür mü? | Atanmayan kullanıcılar oturum açabilir mi? | Atanmayan kullanıcılar uygulamayı görebilir mi?* |
       | evet | evet | evet | hayır  | hayır   |
       | evet | evet | hayır  | hayır  | hayır   |
       | evet | hayır  | evet | evet | hayır   |
       | evet | hayır  | hayır  | evet | hayır   |
       | hayır  | evet | evet | hayır  | hayır   |
       | hayır  | evet | hayır  | hayır  | hayır   |
       | hayır  | hayır  | evet | hayır  | hayır   |
       | hayır  | hayır  | hayır  | hayır  | hayır   |

     *Kullanıcı uygulamayı erişim panelinde ve Office 365 uygulama başlatıcıda görebilir mi?

3. Özel bir logo kullanmak için 215 piksel 215 ile arasında bir logo oluşturun ve bunu PNG biçiminde kaydedin. Ardından logonuzu inceleyin ve karşıya yükleyin.

    ![Logoyu değiştirme](media/add-non-gallery-app/change-logo.png)

4. İşiniz bittiğinde **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık uygulamayı Azure AD kuruluşunuza eklemişseniz, kullanmak istediğiniz [tek bir oturum açma yöntemi seçin](what-is-single-sign-on.md#choosing-a-single-sign-on-method) ve aşağıdaki ilgili makaleye başvurun:

- [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md)
- [Parola çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md)
- [Bağlantılı oturum açmayı Yapılandır](configure-linked-sign-on.md)
