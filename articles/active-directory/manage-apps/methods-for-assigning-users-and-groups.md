---
title: Bir uygulamaya Kullanıcı ve Grup atama | Microsoft Docs
description: Erişim vermek için uygulamaya Kullanıcı atama
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
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd66df381e11582b30ded5e3b529070357f35ad2
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720283"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Azure Active Directory bir uygulamaya Kullanıcı ve Grup atama
Bu makalede, Azure Active Directory (Azure AD) içindeki bir uygulamaya Kullanıcı veya Grup atama işlemlerinin nasıl yapılacağı gösterilir. Bir yöneticinin aşağıdakileri yapması için erişim izni verebilmesi için önce kullanıcılardan bir uygulamaya atanması gerekir:

-   **UYGULAMANıN URL 'sine doğrudan** (SP tarafından başlatılan oturum açma olarak da bilinir) giderek uygulamaya erişin.

-   Uygulamanın **Özellikler** sayfasında (IDP tarafından başlatılan oturum açma olarak da bilinir) **Kullanıcı erişim URL** 'sini kullanarak bir uygulamaya erişin.

-   Uygulama [erişim panelinde](https://myapps.microsoft.com/) veya mobil uygulamada bir uygulamanın göründüğünü görün.

-   Bir uygulamanın [Office 365 uygulama başlatıcısı](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)'nda göründüğünü görün.

Grup tabanlı atamanın kullanılabilirliği lisans sözleşmeniz tarafından belirlenir. Grup tabanlı atama yalnızca güvenlik grupları için desteklenir. İç içe grup üyelikleri ve O365 grupları Şu anda desteklenmemektedir.

## <a name="prerequisites"></a>Önkoşullar
Bir uygulamaya Kullanıcı ve grup atamadan önce, Kullanıcı ataması yapmanız gerekir. Kullanıcı Ataması istemek için:

1. Azure portal bir yönetici hesabıyla oturum açın.
2. Ana menüdeki **tüm hizmetler** öğesine tıklayın.
3. Uygulama için kullanmakta olduğunuz dizini seçin.
4. **Kurumsal uygulamalar** sekmesine tıklayın.
5. Bu dizinle ilişkili uygulamalar listesinden uygulamayı seçin.
6. **Özellikler** sekmesine tıklayın.
7. **Gerekli Kullanıcı atamasını değiştirin mi?** Evet 'e geçin.
8. Ekranın üst kısmındaki **Kaydet** düğmesine tıklayın.

## <a name="assign-users"></a>Kullanıcıları atama

Bir uygulamaya doğrudan bir veya daha fazla kullanıcı atamak için aşağıdaki adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici olarak ya da yönetici olmayan bir uygulama sahibi olarak** oturum açın.

2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.

    * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6.  Listeden Kullanıcı atamak istediğiniz uygulamayı seçin.

7.  Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Kullanıcılar ve gruplar** ' a tıklayın.

8.  **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin üstündeki **Ekle** düğmesine tıklayın.

9.  **atama Ekle** bölmesinden **Kullanıcılar ve gruplar** seçicisini tıklatın.

10. **Ada veya e-posta adresine göre ara** arama kutusuna, ilgilendiğiniz kullanıcının **tam adını** veya **e-posta adresini** yazın.

11. Bir **onay kutusunu**açığa çıkarmak için listedeki **kullanıcının** üzerine gelin. Kullanıcıyı **Seçili** listeye eklemek için kullanıcının profil fotoğrafı veya logosu yanındaki onay kutusuna tıklayın.

12. **Isteğe bağlı:** Birden **fazla kullanıcı eklemek**istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir **tam ad** veya **e-posta adresi** yazın ve bu kullanıcıyı **Seçili** listeye eklemek için onay kutusuna tıklayın.

13. Kullanıcıları seçmeyi tamamladığınızda, uygulamayı atanacak kullanıcılar ve gruplar listesine eklemek için **Seç** düğmesine tıklayın.

14. **Isteğe bağlı:** seçtiğiniz kullanıcılara atanacak bir rol seçmek Için **atama Ekle** bölmesinde **rol seçicisini Seç** ' e tıklayın.

15. Uygulamayı seçili kullanıcılara atamak için **ata** düğmesine tıklayın.

Kısa bir süre sonra, seçtiğiniz kullanıcılar çözüm açıklaması bölümünde açıklanan yöntemleri kullanarak bu uygulamaları başlatabilecektir.

## <a name="assign-groups"></a>Grupları ata

Bir uygulamaya doğrudan bir veya daha fazla grup atamak için aşağıdaki adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve bir **genel yönetici** olarak veya bir Azure AD Premium lisansı atanmış yönetici olmayan bir uygulama sahibi olarak oturum açın.

2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.

    * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6.  Listeden Kullanıcı atamak istediğiniz uygulamayı seçin.

7.  Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Kullanıcılar ve gruplar** ' a tıklayın.

8.  **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin üstündeki **Ekle** düğmesine tıklayın.

9.  **atama Ekle** bölmesinden **Kullanıcılar ve gruplar** seçicisini tıklatın.

10. **Ada veya e-posta adresine göre ara** arama kutusuna, ilgilendiğiniz grubun **tam grup adını** yazın.

11. Bir **onay kutusunu**açığa çıkarmak için listedeki **grubun** üzerine gelin. Kullanıcıyı **Seçili** listeye eklemek için grubun profil fotoğrafı veya logosu yanındaki onay kutusuna tıklayın.

12. **Isteğe bağlı:** Birden **fazla grup eklemek**istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir **tam grup adı** yazın ve bu grubu **Seçili** listeye eklemek için onay kutusuna tıklayın.

13. Grupları seçmeyi tamamladığınızda, uygulamayı atanacak kullanıcılar ve gruplar listesine eklemek için **Seç** düğmesine tıklayın.

14. **Isteğe bağlı:** seçtiğiniz gruplara atanacak bir rol seçmek Için **atama Ekle** bölmesinde **rol seçicisini Seç** ' e tıklayın.

15. Uygulamayı seçili gruplara atamak için **ata** düğmesine tıklayın.

Kısa bir süre sonra, seçtiğiniz gruplar içindeki kullanıcılar, çözüm açıklaması bölümünde açıklanan yöntemleri kullanarak bu uygulamaları başlatabilir. Bunlar dinamik gruplardır, bu atamalarda bu atanan gruplardaki kullanıcılar için görüntülenen bazı ek işlem gecikmesi olabilir.

## <a name="enable-self-service-application-access"></a>Self Servis uygulama erişimini etkinleştir

Self Servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine bulmasına izin vermek için harika bir yoldur ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasını sağlar. İş grubunun, erişim panellerinden, parola çoklu oturum açma uygulamalarında bu kullanıcılara atanan kimlik bilgilerini yönetmesine izin verebilirsiniz.

Bir uygulamaya Self Servis uygulama erişimini etkinleştirmek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.

   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6. Self servis erişimini listeden etkinleştirmek istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Self-Service** ' e tıklayın.

8. Bu uygulama için self servis uygulama erişimini etkinleştirmek üzere **kullanıcıların bu uygulamaya erişim Istemesine Izin ver** ' i açın **. Evet** ' e geçin.

9. Ardından, bu uygulamaya erişim isteyen kullanıcıların eklenmesi gereken grubu seçmek için, **grubun atanması** gereken etiketin yanındaki seçiciye tıklayın ve bir grup seçin.

10. **Isteğe bağlı:** Kullanıcıların erişimine izin verilmeden önce bir iş onayı gerektirmek istiyorsanız, **Bu uygulamaya erişim izni vermeden önce onay iste** seçeneğini belirleyin. **Evet**' e geçin.

11. **Isteğe bağlı: yalnızca parola çoklu oturum açma kullanan uygulamalar için,** bu iş onaylayanlara onaylanan kullanıcılar için bu uygulamaya gönderilen parolaları belirtmesini sağlamak istiyorsanız, **onaylayanlara bunun için Kullanıcı parolalarını ayarlama izni ver ' i ayarlayın Uygulama?** **Evet**olarak değiştirin.

12. **Isteğe bağlı:** Bu uygulamaya erişimi onaylamaya izin verilen iş onaylayanlarını belirtmek için, **Bu uygulamaya erişimi onaylamaya izin verilen** etiketin yanındaki seçiciye tıklayın. en fazla 10 bireysel iş onaylayı seçmek için.

    >[!NOTE]
    >Gruplar desteklenmiyor.
    >
    >

13. **Isteğe bağlı:** **rolleri kullanıma sunan uygulamalar için**, bir role self servis onaylı kullanıcılar atamak istiyorsanız, **Bu uygulamada kullanıcıların hangi rolün atanması gerektiğini seçin?** seçeneğinin yanındaki seçiciye tıklayın. bu kullanıcıların atanmalıdır.

14. Son olarak bölmenin en üstündeki **Kaydet** düğmesine tıklayın.

Self Servis uygulama yapılandırmasını tamamladıktan sonra, kullanıcılar kendi [uygulama erişim paneline](https://myapps.microsoft.com/) gidebilir ve self servis erişimini etkinleştirdiğiniz uygulamaları bulmak Için **+ Ekle** düğmesine tıklamanıza izin verebilir. İş onaylayanları Ayrıca [uygulama erişimi panelinde](https://myapps.microsoft.com/)bir bildirim görür. Bir Kullanıcı onay gerektiren bir uygulamaya erişim isteğinde bulunduğunda bu e-postayı bildiren bir e-posta sağlayabilirsiniz. 

Bu onaylar yalnızca tek onay iş akışlarını destekler, yani birden çok onaylayan belirtirseniz, tek bir onaylayanın uygulamaya erişimi onaylayabileceği anlamına gelir.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama proxy 'Si ile uygulamalarınıza çoklu oturum açma sağlama](application-proxy-configure-single-sign-on-with-kcd.md)
