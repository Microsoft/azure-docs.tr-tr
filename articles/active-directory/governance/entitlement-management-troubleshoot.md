---
title: Azure AD yetkilendirme yönetimi sorunlarını giderme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi sorunlarını gidermenize yardımcı olması için denetlemeniz gereken bazı öğeler hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f7e5e6168e8b365a04040f595f30e68e4e82f0a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173325"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetimi sorunlarını giderme

Bu makalede, Azure Active Directory (Azure AD) yetkilendirme yönetimi sorunlarını gidermenize yardımcı olması için denetlemeniz gereken bazı öğeler açıklanmaktadır.

## <a name="administration"></a>Yönetim

* Yetkilendirme yönetimini yapılandırırken bir erişim reddedildi iletisi alırsanız ve genel yöneticiyseniz, dizininizde [Azure AD Premium P2 (veya EMS E5) lisansına](entitlement-management-overview.md#license-requirements)sahip olduğundan emin olun.

* Erişim paketleri oluştururken veya görüntülerken erişim engellendi iletisi alırsanız ve bir katalog Oluşturucu grubunun üyesiyseniz, ilk erişim paketinizi oluşturmadan önce [bir katalog oluşturmanız](entitlement-management-catalog-create.md) gerekir.

## <a name="resources"></a>Kaynaklar

* Uygulamalar için roller, uygulamanın kendisi tarafından tanımlanır ve Azure AD 'de yönetilir. Bir uygulamanın herhangi bir kaynak rolü yoksa, Yetkilendirme Yönetimi kullanıcıları **varsayılan erişim** rolüne atar.

    Azure portal Ayrıca, uygulamalar olarak seçilebilme Hizmetleri için hizmet sorumlularını da gösterebileceğini unutmayın.  Özellikle, **Exchange Online** ve **SharePoint Online** , dizinde kaynak rollerine sahip olan uygulamalar değil, bir erişim paketine dahil edilemez.  Bunun yerine, bu hizmetlere erişmesi gereken bir kullanıcıya uygun bir lisans oluşturmak için grup tabanlı lisanslama kullanın.

* Bir grubun bir erişim paketindeki kaynak olması için Azure AD 'de değiştirilebilir olması gerekir.  Şirket içi Active Directory kaynaklı gruplar, sahip veya üye öznitelikleri Azure AD 'de değiştirilemediğinden kaynak olarak atanamaz.   Exchange Online 'da dağıtım grupları olarak gelen gruplar, Azure AD 'de değiştirilemez. 

* SharePoint Online belge kitaplıkları ve bireysel belgeler, kaynak olarak eklenemez.  Bunun yerine, bir [Azure AD güvenlik grubu](../fundamentals/active-directory-groups-create-azure-portal.md)oluşturun, bu grubu ve bir site rolünü erişim paketine ekleyin ve SharePoint Online 'da belge kitaplığına veya belgeye erişimi denetlemek için bu grubu kullanın.

* Bir erişim paketiyle yönetmek istediğiniz bir kaynağa zaten atanmış kullanıcılar varsa, kullanıcıların erişim paketine uygun bir ilkeyle atandığından emin olun. Örneğin, grupta zaten kullanıcıları olan bir erişim paketine bir grup eklemek isteyebilirsiniz. Gruptaki bu kullanıcılar devam eden erişim gerektiriyorsa, gruba erişimleri kaybetmemesi için erişim paketleri için uygun bir ilkeye sahip olmaları gerekir. Erişim paketini, kullanıcıların söz konusu kaynağı içeren erişim paketini istemesini isteyerek veya doğrudan erişim paketine atayarak atayabilirsiniz. Daha fazla bilgi için bkz. [bir erişim paketi Için değişiklik isteği ve onay ayarları](entitlement-management-access-package-request-policy.md).

* Ekibin bir üyesini kaldırdığınızda, bunlar Office 365 grubundan da kaldırılır. Ekibin sohbet işlevlerinden kaldırma gecikebilir. Daha fazla bilgi için bkz. [Grup üyeliği](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Dizininizin çok coğrafi bölge için yapılandırılmadığından emin olun. Yetkilendirme Yönetimi Şu anda SharePoint Online için çoklu coğrafi konumları desteklemez. SharePoint Online siteleri, yetkilendirme yönetimiyle yönetilmelidir varsayılan coğrafi konumda olmalıdır. Daha fazla bilgi için bkz. [OneDrive ve SharePoint Online 'Daki çok coğrafi bölge özellikleri](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Dış kullanıcılar

* Bir dış Kullanıcı bir erişim paketine erişim istemek istediğinde, erişim paketi için erişim **portalı** ' nı kullandıklarından emin olun. Daha fazla bilgi için bkz. [erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md). Bir dış Kullanıcı yalnızca **myaccess.Microsoft.com** ' ı ziyaret ederse ve tam erişim portalı bağlantısını kullanmıyorsa, erişim paketlerini kuruluşunuzda değil kendi kuruluşlarındaki kullanıcılara görürler.

* Bir dış Kullanıcı erişim paketine erişim isteye, veya kaynaklara erişemedik, [dış kullanıcılar için ayarlarınızı](entitlement-management-external-users.md#settings-for-external-users)denetlediğinizden emin olun.

* Daha önce dizininizde oturum açmamış yeni bir dış Kullanıcı, SharePoint Online sitesi dahil bir erişim paketi alırsa, hesap SharePoint Online 'da sağlanıncaya kadar erişim paketleri tam olarak teslim edilmemiş olarak görünür. Paylaşım ayarları hakkında daha fazla bilgi için bkz. [SharePoint Online dış paylaşım ayarlarınızı gözden geçirme](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>İstekler

* Bir Kullanıcı bir erişim paketine erişim istemek istediğinde, erişim paketi için erişim **portalı** ' nı kullandıklarından emin olun. Daha fazla bilgi için bkz. [erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md).

* Tarayıcınızla birlikte özel veya ınbilito moduna ayarlanmış erişim portalı 'nı açarsanız, bu, oturum açma davranışıyla çakışabilir. Erişim Portalı 'nı ziyaret ettiğinizde tarayıcınız için özel veya geçersiz kılma modunu kullanmanızı öneririz.

* Dizininizde henüz olmayan bir Kullanıcı erişim paketi istemek için erişim portalı 'nda oturum açtığında, kurumsal hesaplarını kullanarak kimlik doğruladıklarından emin olun. Kuruluş hesabı, kaynak dizinindeki bir hesap ya da erişim paketinin ilkelerinden birine dahil olan bir dizin olabilir. Kullanıcının hesabı bir kurumsal hesap değilse veya kimlik doğrulaması yaptıkları Dizin ilkede yer alıyorsa, Kullanıcı erişim paketini görmez. Daha fazla bilgi için bkz. [bir erişim paketine erişim isteme](entitlement-management-request-access.md).

* Bir kullanıcının kaynak dizininde oturum açması engellenirse, bu kişiler erişim portalından erişim isteyemeyecektir. Kullanıcının erişim isteyebilmesi için, oturum açma bloğunu kullanıcının profilinden kaldırmanız gerekir. Oturum açma bloğunu kaldırmak için, Azure portal **Azure Active Directory**, **Kullanıcılar**' a, Kullanıcı ' ya ve ardından **profil**' e tıklayın. **Ayarlar** bölümünü düzenleyin ve blok olarak **oturum aç '** a **değiştirin.** Daha fazla bilgi için, bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../fundamentals/active-directory-users-profile-azure-portal.md).  Kullanıcının bir [kimlik koruma ilkesi](../identity-protection/howto-unblock-user.md)nedeniyle engellenip engellenmediğini da denetleyebilirsiniz.

* Erişim portalındaki bir kullanıcı hem istek sahibi hem de onaylayan ise, **onaylar** sayfasında erişim paketi isteklerini görmez. Bu davranış bilerek yapılır. bir Kullanıcı kendi isteklerini onaylayamaz. İstediği erişim paketinde, ilkede yapılandırılmış ek onaylayanlar bulunduğundan emin olun. Daha fazla bilgi için bkz. [bir erişim paketi Için değişiklik isteği ve onay ayarları](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Bir isteğin teslim hatalarını görüntüleme

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İstekler**' e tıklayın.

1. Görüntülemek istediğiniz isteği seçin.

    İstekte herhangi bir teslim hatası varsa, istek durumu **teslim** edilmemiş veya **kısmen teslim**edilir.

    Herhangi bir teslim hatası varsa, isteğin ayrıntı bölmesinde teslim hatalarının sayısı olacaktır.

1. Tüm isteğin teslim hatalarını görmek için sayıma tıklayın.

### <a name="reprocess-a-request"></a>İsteği yeniden işleme

Bir istek bir hatayla karşılaşırsa, yeniden denemek için isteği yeniden işleyebilirsiniz. Yalnızca **teslim durumu başarısız** veya **kısmen teslim** edildi olan bir isteği ve bir haftadan daha az tamamlanma tarihini yeniden işleyebilirsiniz.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İstekler**' e tıklayın.

1. Yeniden işlemek istediğiniz isteğe tıklayın.

1. İstek ayrıntıları bölmesinde, **Isteği yeniden işle**' ye tıklayın.

    ![Başarısız bir isteği yeniden işleme](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Bekleyen bir isteği iptal etme

Yalnızca teslim edilmemiş veya teslimi başarısız olan bekleyen bir isteği iptal edebilirsiniz.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İstekler**' e tıklayın.

1. İptal etmek istediğiniz isteğe tıklayın.

1. İstek ayrıntıları bölmesinde **Isteği Iptal et**' e tıklayın.

## <a name="multiple-policies"></a>Birden çok ilke

* Yetkilendirme Yönetimi en iyi ayrıcalık en iyi yöntemlerini izler. Bir Kullanıcı, uygulanan birden çok ilkeye sahip bir erişim paketine erişim istediğinde, Yetkilendirme Yönetimi, daha sıkı veya daha fazla belirli ilkelerin genel ilkelere göre önceliğinin sağlanmasına yardımcı olan mantığı içerir. Bir ilke genel ise, Yetkilendirme Yönetimi ilkeyi istek sahibine görüntülemeyebilir veya otomatik olarak daha sıkı bir ilke seçebilir.

* Örneğin, her iki ilkenin de istek sahibine uygulandığı dahili çalışanlar için iki ilkeye sahip bir erişim paketi düşünün. İlk ilke, istek sahibine dahil olan belirli kullanıcılara yöneliktir. İkinci ilke, bir dizindeki, istek sahibinin üyesi olduğu tüm kullanıcılar içindir. Bu senaryoda, ilk ilke daha sıkı olduğundan istek sahibi için otomatik olarak seçilir. İstek sahibine ikinci ilkeyi seçme seçeneği verilmemiş.

* Birden çok ilke uyguladığınızda, otomatik olarak seçilen ilke veya istek sahibine görüntülenen ilkeler aşağıdaki öncelik mantığına dayalıdır:

    | İlke önceliği | Kapsam |
    | --- | --- |
    | P1 | Dizininizde veya belirli bağlı kuruluşlardaki belirli kullanıcılar ve gruplar |
    | P2 | Dizininizdeki tüm Üyeler (konuklar hariç) |
    | P3 | Dizininizdeki tüm kullanıcılar (konuklar dahil) veya belirli bağlı kuruluşlar |
    | P4 | Tüm bağlı kuruluşlar veya tüm kullanıcılar (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar) |
    
    Herhangi bir ilke daha yüksek öncelikli bir kategoride ise, düşük öncelikli Kategoriler yoksayılır. İstek sahibine aynı önceliğe sahip birden çok ilke gösterilmesiyle ilgili bir örnek için bkz. [Ilke seçme](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Sonraki adımlar

- [Dış kullanıcılar için erişimi yönetir](entitlement-management-external-users.md)
- [Kullanıcıların yetkilendirme yönetimi 'nde nasıl erişimi olduğunu gösteren raporları görüntüleme](entitlement-management-reports.md)
