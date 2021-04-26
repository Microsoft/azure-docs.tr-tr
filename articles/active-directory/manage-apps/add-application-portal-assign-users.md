---
title: 'Hızlı başlangıç: bir kimlik sağlayıcısı olarak Azure Active Directory kullanan bir uygulamaya Kullanıcı atama'
description: Bu hızlı başlangıçta, kullanıcıların Azure AD 'yi kimlik sağlayıcısı olarak kullanmak üzere ayarlamış olduğunuz bir uygulamayı kullanmasına izin verme işlemi gösterilmektedir.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375347"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Hızlı başlangıç: Azure AD 'yi kimlik sağlayıcısı olarak kullanan bir uygulamaya Kullanıcı atama

Önceki hızlı başlangıçta bir uygulama için özellikleri yapılandırdınız. Hem atanan hem de atanmamış kullanıcılar için deneyimi yapılandırdığınız özellikleri ayarladığınızda. Bu hızlı başlangıç, uygulamayı uygulamaya atama sürecinde size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

Kullanıcıları Azure AD kiracınıza eklediğiniz bir uygulamaya atamak için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aşağıdaki rollerden biri: genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi.
- İsteğe bağlı: [uygulamalarınızı görüntülemeyi](view-applications-portal.md)tamamlama.
- İsteğe bağlı: [uygulama ekleme](add-application-portal.md)işleminin tamamlanması.
- İsteğe bağlı: [uygulama yapılandırma](add-application-portal-configure.md)işleminin tamamlanması.

>[!IMPORTANT]
>Bu hızlı başlangıçtaki adımları test etmek için üretim dışı bir ortam kullanın.

## <a name="assign-users-to-an-app"></a>Kullanıcıları bir role atama
1. Azure AD portalında **Kurumsal uygulamalar**' ı seçin. Ardından, yapılandırmak istediğiniz uygulamayı bulun ve seçin.
2. Sol gezinti menüsünde **Kullanıcılar ve gruplar**' ı seçin.
   > [!NOTE]
   > Microsoft 365 uygulamalardan bazıları PowerShell kullanımını gerektirir. 
3. **Kullanıcı Ekle** düğmesini seçin.
4. **Atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.
5. Uygulamaya atamak istediğiniz kullanıcıyı veya grubu seçin. Ayrıca arama kutusuna kullanıcı veya grup adını yazmaya başlayabilirsiniz. Birden çok kullanıcı ve grup seçebilirsiniz ve seçimleriniz **Seçili öğeler** altında görünür.
    > [!IMPORTANT]
    > Bir uygulamaya bir grup atadığınızda, yalnızca gruptaki kullanıcıların erişimi olur. Atama, iç içe gruplar olarak basamaklanmıyor.

    > [!NOTE]
    > Grup tabanlı atama Azure Active Directory Premium P1 veya P2 Edition gerektirir. Grup tabanlı atama yalnızca güvenlik grupları için desteklenir. İç içe grup üyelikleri ve Microsoft 365 grupları Şu anda desteklenmemektedir. Bu makalede ele alınan özelliklerin daha fazla lisanslama gereksinimi için [Azure Active Directory fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/active-directory)bakın. 
6. İşiniz bittiğinde **Seç**' i seçin.
   ![Uygulamaya Kullanıcı veya Grup atama](./media/assign-user-or-group-access-portal/assign-users.png)
7. **Kullanıcılar ve gruplar** bölmesinde, listeden bir veya daha fazla Kullanıcı veya grup seçin ve ardından bölmenin altındaki **Seç** düğmesini seçin.
8. Uygulama destekliyorsa, kullanıcıya veya gruba bir rol atayabilirsiniz. **Atama Ekle** bölmesinde **Rol Seç**' i seçin. Ardından, **Rol Seç** bölmesinde, seçili kullanıcılara veya gruplara uygulanacak bir rol seçin ve ardından bölmenin altındaki **Tamam** ' ı seçin. 
    > [!NOTE]
    > Uygulama, rol seçimini desteklemiyorsa, varsayılan erişim rolü atanır. Bu durumda, uygulama kullanıcıların sahip olduğu erişim düzeyini yönetir.
9. **Atama Ekle** bölmesinde, bölmenin altındaki **ata** düğmesini seçin.

Aynı yordamı kullanarak kullanıcıların veya grupların atamasını kaldırabilirsiniz. Atamasını kaldırmak istediğiniz kullanıcıyı veya grubu seçin ve ardından **Kaldır**' ı seçin. Microsoft 365 ve Office 365 uygulamalarından bazıları PowerShell kullanımını gerektirir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıç adımlarını tamamladıktan sonra uygulamayı silmeyi göz önünde bulundurun. Böylece test kiracınızı temizleyelim. Bu serinin son hızlı başlangıçta uygulamanın silinmesi, bkz. [uygulamayı silme](delete-application-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulama için çoklu oturum açmayı ayarlamayı öğrenmek için bir sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [SAML tabanlı çoklu oturum açmayı ayarlama](add-application-portal-setup-sso.md)

VEYA

> [!div class="nextstepaction"]
> [OIDC tabanlı çoklu oturum açmayı ayarlama](add-application-portal-setup-oidc-sso.md)
