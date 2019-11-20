---
title: Galeri uygulaması ekleme-Azure Active Directory | Microsoft Docs
description: Azure AD galerisinden Azure Kurumsal uygulamalarınıza uygulama eklemeyi öğrenin.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062634"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Azure AD kuruluşunuza Galeri uygulaması ekleme

Azure Active Directory (Azure AD), Kurumsal Çoklu oturum açma ile etkinleştirilen binlerce önceden tümleştirilmiş uygulamayı içeren bir galersahiptir. Bu makalede, Galeriden Azure AD kuruluşunuza bir uygulama eklemeye yönelik genel adımlar açıklanmaktadır.

> [!IMPORTANT]
> İlk olarak, [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesinden](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)uygulamanızı kontrol edin. Büyük olasılıkla eklemek istediğiniz Galeri uygulamasını eklemek ve yapılandırmak için adım adım yönergeler bulacaksınız.

## <a name="add-a-gallery-application"></a>Galeri uygulaması ekleme

1. [Azure portalda](https://portal.azure.com) Azure AD kiracınızın genel yönetici, bulut uygulaması yöneticisi veya uygulama yöneticisi hesabıyla oturum açın.

1. [Azure Portal](https://portal.azure.com)sol gezinti panelinde **Azure Active Directory**' i seçin.

1. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin.

    ![Kurumsal uygulamaları açın](media/add-gallery-app/open-enterprise-apps.png)


3. Kiracınıza bir galeri uygulaması eklemek için **Yeni uygulama**' yı seçin.

    ![Kiracınıza Galeri uygulaması eklemek için yeni uygulama ' yı seçin](media/add-gallery-app/new-application.png)

 4. Yeni Galeri önizleme deneyimine geç: **Uygulama Ekle sayfasının**en üstündeki başlıkta, **yeni ve geliştirilmiş uygulama galerisini denemek için buraya tıklayın**bağlantısını seçin.

5. **Azure AD galerisine gözatamıyorum** bölmesi açılır ve bulut platformları, şirket içi uygulamalar ve öne çıkan uygulamalar için kutucukları görüntüler. **Öne çıkan uygulamalar** bölümünde listelenen uygulamaların, federe çoklu oturum açma (SSO) ve sağlamayı destekleyip desteklemediğini gösteren simgeler olduğunu unutmayın.

    ![Bir uygulamayı ada veya kategoriye göre ara](media/add-gallery-app/browse-gallery.png)

6. Eklemek istediğiniz uygulamanın galerisine gidin veya arama kutusuna adını girerek uygulamayı arayın. Sonra sonuçlardan uygulamayı seçin. Seçim Formunda, kuruluşunuzun gereksinimlerini karşılayacak şekilde uygulama adını düzenleyebilirsiniz.

    ![Galeriden bir uygulamanın nasıl ekleneceğini gösterir](media/add-gallery-app/create-application.png)

7. **Oluştur**'u seçin. Uygulamayı kuruluşunuza göre yapılandırmak için kullanabileceğiniz seçeneklerin bulunduğu bir başlangıç sayfası açılır.

## <a name="configure-user-sign-in-properties"></a>Kullanıcı oturum açma özelliklerini yapılandırma

1. Özellikler bölmesini düzenlenmek üzere açmak için **Özellikler** ' i seçin.

    ![Özellikler bölmesini Düzenle](media/add-gallery-app/edit-properties.png)

1. Uygulamanın atandığı veya atanmamış olduğu kullanıcıların uygulamada oturum açıp görebilmesine ve bir kullanıcının erişim panelinde uygulamayı görebilmesi için aşağıdaki seçenekleri ayarlayın.

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

1. Özel bir logo kullanmak için 215 piksel 215 ile arasında bir logo oluşturun ve bunu PNG biçiminde kaydedin. Ardından logonuzu inceleyin ve karşıya yükleyin.

    ![Logoyu değiştirme](media/add-gallery-app/change-logo.png)

1. İşiniz bittiğinde **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık uygulamayı Azure AD kuruluşunuza eklemişseniz, kullanmak istediğiniz [tek bir oturum açma yöntemi seçin](what-is-single-sign-on.md#choosing-a-single-sign-on-method) ve aşağıdaki ilgili makaleye başvurun:

- [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md)
- [Parola çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md)
- [Bağlantılı oturum açmayı Yapılandır](configure-linked-sign-on.md)

