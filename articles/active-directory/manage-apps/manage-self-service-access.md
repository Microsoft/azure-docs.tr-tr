---
title: Self Servis uygulama atamasını yapılandırma | Microsoft Docs
description: Kullanıcıların kendi uygulamalarını bulmasına izin vermek için self servis uygulama erişimini etkinleştirme
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbeb25f1190754b4264cfbab9d8a03a6b65c4dff
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895961"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Self Servis uygulama atamasını yapılandırma

Kullanıcılarınızın erişim panelinden uygulamaları kendi kendine bulabilmesi için, kullanıcıların kendi kendine bulmasını ve erişim istemesini sağlamak istediğiniz uygulamalara **self servis uygulama erişimini** etkinleştirmeniz gerekir. Bu işlev, [Azure AD Galerisi](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)'nden eklenen, [Azure AD uygulama ara sunucusu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) veya [Kullanıcı ya da yönetici izni](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)aracılığıyla eklenen uygulamalar için kullanılabilir. 

Bu özellik, bir BT grubu olarak zaman ve para tasarrufu yapmanız için harika bir yoldur ve Azure Active Directory ile modern uygulamalar dağıtımının bir parçası olarak önemle önerilir.

Bu özelliği kullanarak şunları yapabilirsiniz:

-   Kullanıcıların [uygulama erişim panelinden](https://myapps.microsoft.com/) bothering BT grubu olmadan uygulamaları kendi kendine bulmasına izin verin.

-   Bu kullanıcıları önceden yapılandırılmış bir gruba ekleyerek kimin erişimi olduğunu, erişimi kaldırabileceğini görebilir ve bunlara atanan rolleri yönetebilirsiniz.

-   İsteğe bağlı olarak, bir iş onaylayanın uygulama erişim isteklerini onaylaması için BT grubunun olmaması gerekir.

-   İsteğe bağlı olarak, bu uygulamaya erişimi onaylayabilecek en fazla 10 kişi yapılandırın.

-   İsteğe bağlı olarak, bir iş onaylayanın, kullanıcıların uygulamada oturum açmak için kullanabileceği parolaları ayarlamaya, iş onaylayanın [uygulama erişim panelinden](https://myapps.microsoft.com/)doğrudan izin verin.

-   İsteğe bağlı olarak otomatik olarak self servis atanan kullanıcıları doğrudan bir uygulama rolüne atar.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Kullanıcıların kendi uygulamalarını bulmasına izin vermek için self servis uygulama erişimini etkinleştirme

Self Servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine bulmasına izin vermek için harika bir yoldur ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasını sağlar. İş grubunun, erişim panellerinden, parola çoklu oturum açma uygulamalarında bu kullanıcılara atanan kimlik bilgilerini yönetmesine izin verebilirsiniz.

Bir uygulamaya Self Servis uygulama erişimini etkinleştirmek için aşağıdaki adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.

    * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6.  Self servis erişimini listeden etkinleştirmek istediğiniz uygulamayı seçin.

7.  Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Self-Service** ' e tıklayın.

8.  Bu uygulama için self servis uygulama erişimini etkinleştirmek üzere **kullanıcıların bu uygulamaya erişim Istemesine Izin ver** ' i açın **. Evet** ' e geçin.

9.  Ardından, bu uygulamaya erişim isteyen kullanıcıların eklenmesi gereken grubu seçmek için, **grubun atanması** gereken etiketin yanındaki seçiciye tıklayın ve bir grup seçin.
  
    > [!NOTE]
    > Şirket içinden eşitlenen grupların, bu uygulamaya erişim isteyen kullanıcıların eklenmesi için kullanılması desteklenmez.
  
10. **Isteğe bağlı:** Kullanıcıların erişimine izin verilmeden önce bir iş onayı gerektirmek istiyorsanız, **Bu uygulamaya erişim izni vermeden önce onay iste** seçeneğini belirleyin. **Evet**' e geçin.

11. **Isteğe bağlı: yalnızca parola çoklu oturum açma kullanan uygulamalar için,** bu iş onaylayanlara onaylanan kullanıcılar için bu uygulamaya gönderilen parolaları belirtmesini sağlamak istiyorsanız, **onaylayanlara bunun için Kullanıcı parolalarını ayarlama izni ver ' i ayarlayın Uygulama?** **Evet**olarak değiştirin.

12. **Isteğe bağlı:** Bu uygulamaya erişimi onaylamaya izin verilen iş onaylayanlarını belirtmek için, **Bu uygulamaya erişimi onaylamaya izin verilen** etiketin yanındaki seçiciye tıklayın. en fazla 10 bireysel iş onaylayı seçmek için.

     > [!NOTE]
     > Gruplar desteklenmiyor.
     >
     >

13. **Isteğe bağlı:** **rolleri kullanıma sunan uygulamalar için**, bir role self servis onaylı kullanıcılar atamak istiyorsanız, **Bu uygulamada kullanıcıların hangi rolün atanması gerektiğini seçin?** seçeneğinin yanındaki seçiciye tıklayın. bu kullanıcıların atanmalıdır.

14. Son olarak dikey pencerenin en üstündeki **Kaydet** düğmesine tıklayın.

Self Servis uygulama yapılandırmasını tamamladıktan sonra, kullanıcılar kendi [uygulama erişim paneline](https://myapps.microsoft.com/) gidebilir ve self servis erişimini etkinleştirdiğiniz uygulamaları bulmak Için **+ Ekle** düğmesine tıklamanıza izin verebilir. İş onaylayanları Ayrıca [uygulama erişimi panelinde](https://myapps.microsoft.com/)bir bildirim görür. Bir Kullanıcı onay gerektiren bir uygulamaya erişim isteğinde bulunduğunda bu e-postayı bildiren bir e-posta sağlayabilirsiniz. 

Bu onaylar yalnızca tek onay iş akışlarını destekler, yani birden çok onaylayan belirtirseniz, tek bir onaylayanın uygulamaya ne kadar bir onaylayan erişimi de

## <a name="next-steps"></a>Sonraki adımlar
[Self Servis Grup yönetimi için Azure Active Directory ayarlama](../users-groups-roles/groups-self-service-management.md)
