---
title: Azure Active Directory kiracınıza uygulama ekleme | Microsoft Docs
description: Bu hızlı başlangıçta Azure portal kullanılarak Azure Active Directory (Azure AD) kiracınıza bir galeri uygulaması eklenmektedir.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5ee40f6fcd77465b67a7c409493027fefa48ca9
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062891"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Hızlı başlangıç: Azure Active Directory kiracınıza uygulama ekleme

Azure Active Directory (Azure AD), önceden tümleştirilmiş binlerce uygulamanın yer aldığı bir galeriye sahiptir. Galeride kuruluşunuzun kullandığı uygulamaların bazıları da mevcuttur. Bu hızlı başlangıçta Azure portal kullanılarak Azure Active Directory (Azure AD) kiracınıza bir galeri uygulaması eklenmektedir.

Bir uygulamayı Azure AD kiracınıza ekledikten sonra aşağıdaki işlemleri gerçekleştirebilirsiniz:

- Koşullu erişim ilkesiyle uygulamaya yönelik Kullanıcı erişimini yönetin.
- Kullanıcıları Azure AD hesapları ile uygulamada çoklu oturum açma özelliğini kullanacak şekilde yapılandırma.

## <a name="before-you-begin"></a>Başlamadan önce

Kiracınıza uygulama eklemek için şunlara ihtiyacınız vardır:

- Azure AD aboneliği
- Uygulamanız için çoklu oturum açma özelliği etkinleştirilmiş abonelik

[Azure portalda](https://portal.azure.com) Azure AD kiracınızın genel yönetici, bulut uygulaması yöneticisi veya uygulama yöneticisi hesabıyla oturum açın.

Bu öğreticideki adımları test etmek için üretim ortamı harici bir ortam kullanmanızı öneririz. Üretim ortamı dışında bir Azure AD ortamınız yoksa [bir aylık deneme](https://azure.microsoft.com/pricing/free-trial/) aboneliği oluşturabilirsiniz.

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Azure AD kiracınıza uygulama ekleme

Azure AD kiracınıza galeri uygulaması eklemek için:

1. [Azure Portal](https://portal.azure.com)sol gezinti panelinde **Azure Active Directory**' i seçin.

2. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin. **Tüm uygulamalar** bölmesi açılır ve Azure AD kiracınızdaki uygulamaların rastgele bir örneğini görüntüler.

3. Kiracınıza bir galeri uygulaması eklemek için **Yeni uygulama**' yı seçin. 

    ![Kiracınıza Galeri uygulaması eklemek için yeni uygulama ' yı seçin](media/add-application-portal/new-application.png)

 4. Yeni Galeri önizleme deneyimine geç: **Uygulama Ekle sayfasının**en üstündeki başlıkta, **yeni ve geliştirilmiş uygulama galerisini denemek için buraya tıklayın**bağlantısını seçin.

5. **Azure AD galerisine gözatıp (Önizleme)** bölmesi açılır ve bulut platformları, şirket içi uygulamalar ve öne çıkan uygulamalar için kutucukları görüntüler. **Öne çıkan uygulamalar** bölümünde listelenen uygulamaların, federe çoklu oturum açma (SSO) ve sağlamayı destekleyip desteklemediğini gösteren simgeler olduğunu unutmayın.

    ![Bir uygulamayı ada veya kategoriye göre ara](media/add-application-portal/browse-gallery.png)

6. Eklemek istediğiniz uygulamanın galerisine gözatıp, arama kutusuna adını girerek uygulamayı arayabilirsiniz. Sonra sonuçlardan uygulamayı seçin. Formunda, kuruluşunuzun gereksinimlerini karşılayacak şekilde uygulamanın adını düzenleyebilirsiniz. Bu örnekte, adı **GitHub-test**olarak değiştirdik.

    ![Galeriden bir uygulamanın nasıl ekleneceğini gösterir](media/add-application-portal/create-application.png)

7. **Oluştur**'u seçin. Uygulamayı kuruluşunuza göre yapılandırmak için kullanabileceğiniz seçeneklerin bulunduğu bir başlangıç sayfası açılır.

Uygulamanızı eklemeyi tamamladınız. Aşağıdaki bölümlerde uygulamanızın logosunu değiştirme ve diğer özelliklerini düzenleme adımları gösterilmektedir.

## <a name="find-your-azure-ad-tenant-application"></a>Azure AD kiracısı uygulamanızı bulma

İşlemi burada bırakmak zorunda kaldığınızı ve uygulamayı yapılandırmaya devam etmek için daha sonra tekrar geldiğinizi düşünelim. Yapmanız gereken ilk şey, uygulamanızı bulmledir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.
1. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin.
1. **Uygulama türü** açılan menüsünde, **tüm uygulamalar**' ı seçin ve ardından **Uygula**' yı seçin. Görüntüleme seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Kiracı uygulamalarını görüntüleme](view-applications-portal.md).
1. Burada Azure AD kiracınızdaki tüm uygulamaların listesini görebilirsiniz. Bu listedeki uygulamalar rastgele seçilmiştir. Daha fazla uygulama görmek için **daha fazla** bir veya daha fazla kez göster ' i seçin.
1. Kiracınızda bir uygulamayı hızlı bir şekilde bulmak için, arama kutusuna uygulamanın adını girin ve **Uygula**' yı seçin. Bu örnek, daha önce eklenen GitHub-test uygulamasını bulur.

    ![Arama kutusunu kullanarak bir uygulamanın nasıl bulunacağını gösterir](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Kullanıcı oturum açma özelliklerini yapılandırma

Uygulamayı buldığınıza göre, onu açabilir ve uygulama özelliklerini yapılandırabilirsiniz.

Uygulama özelliklerini düzenlemek için:

1. Açmak için uygulamayı seçin.
2. Özellikler bölmesini düzenlenmek üzere açmak için **Özellikler** ' i seçin.

    ![Özellikler ekranını ve düzenlenebilir uygulama özelliklerini gösterir](media/add-application-portal/edit-properties.png)

3. Birkaç dakikanızı ayırarak oturum açma seçeneklerini inceleyin. Seçenekler, uygulamaya atanan veya atanmamış kullanıcıların uygulamada oturum açıp atamalayamadığını belirleyebilir. Ayrıca, seçenekler de kullanıcının erişim panelinde uygulamayı görüp görmediğine de sahiptir.

    - **Kullanıcıların oturum açması Için etkinleştirildi mi?** uygulamaya atanan kullanıcıların oturum açıp açamayacağını belirler.
    - **Kullanıcı Ataması gerekli mi?** uygulamaya atanmamış kullanıcıların oturum açıp açamayacağını belirler.
    - **Kullanıcılara görünebilir mi?** bir uygulamaya atanan kullanıcıların onu erişim paneli ve O365 başlatıcısı 'nda göremeyeceğini belirler.

4. Gereksinimleriniz için en iyi seçenekleri seçmenize yardımcı olması için aşağıdaki tabloları kullanın.

   - **Atanan** kullanıcılar için davranış:

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

   - **Atanmayan** kullanıcılar için davranış:

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

## <a name="use-a-custom-logo"></a>Özel logo kullanma

Özel logo kullanmak için:

1. 215x215 piksel boyutunda bir logo oluşturun ve PNG biçiminde kaydedin.
1. Uygulamanızı zaten bulduğundan, uygulamayı seçin.
1. Sol bölmede **Özellikler**' i seçin.
1. Logoyu yükleyin.
1. İşiniz bittiğinde **Kaydet**' i seçin. 

    ![Uygulamanın Özellikler sayfasından logonun nasıl değiştirileceğini gösterir](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Bu **Özellikler** bölmesinde görünen küçük resim, hemen güncelleştirmez. Güncelleştirilmiş simgesini görmek için özellikleri kapatıp yeniden açabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık uygulamayı Azure AD kuruluşunuza eklemişseniz, kullanmak istediğiniz [tek bir oturum açma yöntemi seçin](what-is-single-sign-on.md#choosing-a-single-sign-on-method) ve aşağıdaki ilgili makaleye başvurun:

- [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md)
- [Parola çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md)
- [Bağlantılı oturum açmayı Yapılandır](configure-linked-sign-on.md)
