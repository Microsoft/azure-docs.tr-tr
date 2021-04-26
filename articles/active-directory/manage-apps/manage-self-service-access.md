---
title: Self Servis uygulama atamasını yapılandırma | Microsoft Docs
description: Kullanıcıların kendi uygulamalarını bulmasına izin vermek için self servis uygulama erişimini etkinleştirme
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373970"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Self Servis uygulama atamasını yapılandırma

Kullanıcılarınızın uygulamalarımı kendi uygulamalarından kendi kendine bulabilmesi için, kullanıcıların kendi kendine bulmasına ve erişim istemesine izin vermek istediğiniz uygulamalara **self servis uygulama erişimini** etkinleştirmeniz gerekir. Bu işlev, [Azure AD Galerisi](./add-application-portal.md)'nden eklenen, [Azure AD uygulama ara sunucusu](./application-proxy.md) veya [Kullanıcı ya da yönetici izni](../develop/application-consent-experience.md)aracılığıyla eklenen uygulamalar için kullanılabilir. 

Bu özellik, bir BT grubu olarak zaman ve para tasarrufu yapmanız için harika bir yoldur ve Azure Active Directory ile modern uygulamalar dağıtımının bir parçası olarak önemle önerilir.

Bu özelliği kullanarak şunları yapabilirsiniz:

-   Bothering BT grubu olmadan, kullanıcıların [uygulamalarımı](https://myapps.microsoft.com/) kendi kendine bulmasına izin verin.

-   Bu kullanıcıları önceden yapılandırılmış bir gruba ekleyerek kimin erişimi olduğunu, erişimi kaldırabileceğini görebilir ve bunlara atanan rolleri yönetebilirsiniz.

-   İsteğe bağlı olarak, bir iş onaylayanın uygulama erişim isteklerini onaylaması için BT grubunun olmaması gerekir.

-   İsteğe bağlı olarak, bu uygulamaya erişimi onaylayabilecek en fazla 10 kişi yapılandırın.

-   İsteğe bağlı olarak, bir iş onaylayanın, kullanıcıların uygulamada oturum açmak için kullanabileceği parolaları ayarlamaya olanak tanımak için, iş Onaylayandan [uygulamalarım](https://myapps.microsoft.com/)' a doğru.

-   İsteğe bağlı olarak otomatik olarak self servis atanan kullanıcıları doğrudan bir uygulama rolüne atar.

> [!NOTE]
> Kullanıcıların bir self servis uygulamasına katılmayı istemesi ve sahiplerin istekleri onaylaması veya reddetmesi için bir Azure Active Directory Premium (P1 veya P2) lisansı gerekir. Azure Active Directory Premium lisansı olmadan kullanıcılar self servis uygulamaları ekleyemez.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Kullanıcıların kendi uygulamalarını bulmasına izin vermek için self servis uygulama erişimini etkinleştirme

Self Servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine bulmasına imkan tanıyan harika bir yoldur ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasını sağlar. Parola çoklu oturum açma uygulamalarında, iş grubunun bu kullanıcılara atanan kimlik bilgilerini kendi uygulamalarım erişim panellerinden yönetmesine de izin verebilirsiniz.

Bir uygulamaya Self Servis uygulama erişimini etkinleştirmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Azure Active Directory** seçin. Sol gezinti menüsünde **Kurumsal uygulamalar**' ı seçin.

3. Listeden uygulamayı seçin. Uygulamayı görmüyorsanız, arama kutusuna adını yazmaya başlayın. Ya da uygulama türünü, durumunu veya görünürlüğünü seçmek için filtre denetimlerini kullanın ve ardından **Uygula**' yı seçin.

4. Sol gezinti menüsünde **Self-Service**' i seçin.

5. Bu uygulama için self servis uygulama erişimini etkinleştirmek üzere **kullanıcıların bu uygulamaya erişim Istemesine Izin ver** ' i açın **. Evet** ' e geçin.

6. **Hangi grubun Kullanıcı tarafından atanması gerekir?**, **Grup Seç**' e tıklayın. Bir grup seçin ve ardından **Seç**' e tıklayın. Bir kullanıcının isteği onaylandığında, bu gruba eklenir. Bu grubun üyeliği görüntülenirken, uygulamaya Self Servis erişimi aracılığıyla kimlerin erişim verildiğini görebilirsiniz.
  
    > [!NOTE]
    > Bu ayar Şirket içinden eşitlenen grupları desteklemez.

7. **Isteğe bağlı:** Kullanıcıların erişime izin verilmeden önce iş onayı gerektirmek için, **Bu uygulamaya erişim izni vermeden önce onay iste** seçeneğini belirleyin. **Evet**'e geçin.

8. **Isteğe bağlı: yalnızca parola çoklu oturum açma kullanan uygulamalar için,** iş onaylayanlara onaylanan kullanıcılar için bu uygulamaya gönderilen parolaları belirtmesini sağlamak için, **onaylayanlara bu uygulama için Kullanıcı parolalarını ayarlama izni ver** ' i ayarlayın. **Evet**' e geçin.

9. **Isteğe bağlı:** Bu uygulamaya erişimi onaylamaya izin verilen iş onaylayanlarını belirtmek için, **Bu uygulamaya erişimi onaylamaya izin verilen kişiler ' in** yanında, **onaylayanları Seç**' e tıklayın ve ardından 10 bireysel iş onaylayanlarını seçin. Ardından **Seç**'e tıklayın.

    >[!NOTE]
    >Gruplar desteklenmiyor. En fazla 10 bireysel iş onaylayı seçebilirsiniz. Birden çok onaylayan belirtirseniz, tek bir onaylayan bir erişim isteğini onaylayabilir.

10. **Isteğe bağlı:** **rolleri kullanıma sunan uygulamalar için**, bir role self servis onaylı kullanıcılar atama, **Bu uygulamada hangi rolün atanması gerekir? ' in** yanında, **Rol Seç**' e tıklayın ve ardından bu kullanıcıların atanması gereken rolü seçin. Ardından **Seç**'e tıklayın.

11. Son olarak bölmenin en üstündeki **Kaydet** düğmesine tıklayın.

Self Servis uygulama yapılandırmasını tamamladıktan sonra, kullanıcılar kendi [uygulamalarım](https://myapps.microsoft.com/) ' a gidebilir ve self servis erişimiyle etkinleştirilecek uygulamaları bulmak için **self servis uygulamaları Ekle** düğmesine tıklayabilir. İş onaylayanları Ayrıca [uygulamalarım](https://myapps.microsoft.com/)' da bir bildirim görür. Bir Kullanıcı onay gerektiren bir uygulamaya erişim isteğinde bulunduğunda bu e-postayı bildiren bir e-posta sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Self servis grup yönetimi için Azure Active Directory'yi ayarlama](../enterprise-users/groups-self-service-management.md)