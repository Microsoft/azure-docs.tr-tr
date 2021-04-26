---
title: Azure AD 'de Self Servis uygulama erişimini kullanma
description: Kullanıcıların Azure AD 'de uygulama bulabilmesi için Self-Service 'i etkinleştirin
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377624"
---
# <a name="how-to-use-self-service-application-access"></a>Self Servis uygulama erişimini kullanma

Kullanıcılarınızın uygulamaları kendi uygulamalarım sayfasından kendi kendine bulabilmesi için, kullanıcıların kendi kendine bulmasını ve erişim istemesini sağlamak istediğiniz uygulamalara **self servis uygulama erişimini** etkinleştirmeniz gerekir.

Bu özellik, bir BT grubu olarak zaman ve para tasarrufu yapmanız için harika bir yoldur ve Azure Active Directory ile modern uygulamalar dağıtımının bir parçası olarak önemle önerilir.

Son Kullanıcı perspektifinden uygulamalarımı kullanma hakkında bilgi edinmek için bkz. [Apps Portal yardımı](../user-help/my-apps-portal-end-user-access.md).

Bu özelliği kullanarak şunları yapabilirsiniz:

-   Kullanıcıların bothering BT grubu olmadan [uygulamalarımın](https://myapps.microsoft.com/) uygulamalarını kendi kendine bulmasına izin verin.
-   Bu kullanıcıları önceden yapılandırılmış bir gruba ekleyerek kimin erişimi olduğunu, erişimi kaldırabileceğini görebilir ve bunlara atanan rolleri yönetebilirsiniz.
-   İsteğe bağlı olarak, bir kişinin uygulama erişim isteklerini onaylamasını sağlamak için BT grubunun olması gerekir.
-   İsteğe bağlı olarak, bu uygulamaya erişimi onaylayabilecek en fazla 10 kişi yapılandırın.
-   İsteğe bağlı olarak, kullanıcıların uygulamada oturum açmak için kullanabileceği parolaları ayarlamaya izin verir.
-   İsteğe bağlı olarak otomatik olarak self servis atanan kullanıcıları doğrudan bir uygulama rolüne atar.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Kullanıcıların kendi uygulamalarını bulmasına izin vermek için self servis uygulama erişimini etkinleştirme

Self Servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine bulmasına izin vermek için harika bir yoldur ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasını sağlar. İş grubunun, Uygulamalarım sayfasından uygulamalar üzerinde parola Single-Sign için bu kullanıcılara atanan kimlik bilgilerini yönetmesine izin verebilirsiniz.

Bir uygulamaya Self Servis uygulama erişimini etkinleştirmek için aşağıdaki adımları izleyin:
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Azure Active Directory sol gezinti menüsünde **Kurumsal Uygulamalar**’ı seçin.
5. Tüm uygulamalarınızın listesini görüntülemek için **Tüm Uygulamalar**'ı seçin.
   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
6. Self servis erişimini listeden etkinleştirmek istediğiniz uygulamayı seçin.
7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Self-Service** ' i seçin.
8. Bu uygulama için self servis uygulama erişimini etkinleştirmek üzere **kullanıcıların bu uygulamaya erişim Istemesine Izin ver** ' i açın **. Evet** ' e geçin.
9. Ardından, bu uygulamaya erişim isteyen kullanıcıların eklenmesi gereken grubu seçmek için, **gruba atanan kullanıcıların** ekleneceği etiketin yanındaki seçiciyi seçin ve bir grup seçin.
10. **Isteğe bağlı:** Kullanıcıların erişimine izin verilmeden önce bir iş onayı gerektirmek istiyorsanız, **Bu uygulamaya erişim izni vermeden önce onay iste** seçeneğini belirleyin. **Evet**' e geçin.
11. **Isteğe bağlı: yalnızca parola çoklu oturum açma kullanan uygulamalar için,** bu iş onaylayanlara onaylanan kullanıcılar için bu uygulamaya gönderilen parolaları belirtmesini sağlamak istiyorsanız, **onaylayanlara bu uygulama için Kullanıcı parolalarını ayarlamaya izin ver** ' i ayarlayın. **Evet**' e geçin.
12. **Isteğe bağlı:** Bu uygulamaya erişimi onaylamaya izin verilen iş onaylayanlarını belirtin. **Bu uygulamaya erişimi kimlerin onaylamasını izin verileceğini** seçin. Ardından en fazla 10 bireysel iş onaylayan seçin.
    * Gruplar desteklenmiyor.
13. **Isteğe bağlı:** **rolleri kullanıma sunan uygulamalar için**, bir role self servis onaylı kullanıcılar atamak istiyorsanız, **Bu uygulamada hangi rolün atanması gerektiğini belirtin?** seçeneğinin yanındaki seçiciyi seçin. bu kullanıcıların atanması gereken rolü seçin.
14. En üstteki **Kaydet** düğmesini seçin.

Self Servis uygulama yapılandırmasını tamamladıktan sonra, kullanıcılar [uygulamalarıma](https://myapps.microsoft.com/) gidebilir ve self servis erişimini etkinleştirdiğiniz uygulamaları bulmak Için **+ Ekle** düğmesini seçebilir. İş onaylayanları Ayrıca [uygulamalarım](https://myapps.microsoft.com/) sayfasında da bir bildirim görür. Bir Kullanıcı onay gerektiren bir uygulamaya erişim isteğinde bulunduğunda bu e-postayı bildiren bir e-posta sağlayabilirsiniz. 

Bu onaylar yalnızca tek onay iş akışlarını destekler, yani birden çok onaylayan belirtirseniz, tek bir onaylayanın uygulamaya erişimi onaylayabileceği anlamına gelir.

## <a name="things-to-check-if-self-service-isnt-working"></a>Self Servis çalışıp çalışmadığını kontrol etmek için gerekenler
-   Kullanıcının veya grubun Self Servis uygulama erişimi istemek için etkinleştirildiğinden emin olun.
-   Kullanıcının Self Servis uygulama erişimi için doğru yeri ziyaret ettiğini doğrulayın. Kullanıcılar kendi [uygulamalarım](https://myapps.microsoft.com/) sayfasına gidebilir ve self servis erişimini etkinleştirdiğiniz uygulamaları bulmak Için **+ Ekle** düğmesini seçebilir.
-   Self Servis uygulama erişimi son zamanlarda yapılandırıldıysa, self servis erişim değişikliklerinin görünmediği anlamak için birkaç dakika sonra kullanıcının uygulamalarıma yeniden oturum açmayı deneyin.

## <a name="next-steps"></a>Sonraki adımlar
[Self servis grup yönetimi için Azure Active Directory'yi ayarlama](../enterprise-users/groups-self-service-management.md)
