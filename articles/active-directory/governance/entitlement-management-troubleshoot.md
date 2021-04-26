---
title: Yetkilendirme yönetimi sorunlarını giderme-Azure AD
description: Azure Active Directory yetkilendirme yönetimi sorunlarını gidermenize yardımcı olması için denetlemeniz gereken bazı öğeler hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b01321c2cbb15be5526bbe9ed04f52238390574
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516898"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetimi sorunlarını giderme

Bu makalede, Azure Active Directory (Azure AD) yetkilendirme yönetimi sorunlarını gidermenize yardımcı olması için denetlemeniz gereken bazı öğeler açıklanmaktadır.

## <a name="administration"></a>Yönetim

* Yetkilendirme yönetimini yapılandırırken bir erişim reddedildi iletisi alırsanız ve genel yöneticiyseniz, dizininizde [Azure AD Premium P2 (veya EMS E5) lisansına](entitlement-management-overview.md#license-requirements)sahip olduğundan emin olun.

* Erişim paketleri oluştururken veya görüntülerken erişim engellendi iletisi alırsanız ve bir katalog Oluşturucu grubunun üyesiyseniz, ilk erişim paketinizi oluşturmadan önce [bir katalog oluşturmanız](entitlement-management-catalog-create.md) gerekir.

## <a name="resources"></a>Kaynaklar

* Uygulamalara yönelik roller uygulamanın kendisi tarafından tanımlanır ve Azure AD’de yönetilir. Bir uygulamanın herhangi bir kaynak rolü yoksa, Yetkilendirme Yönetimi kullanıcıları **varsayılan erişim** rolüne atar.

    Azure portal Ayrıca, uygulamalar olarak seçilebilme Hizmetleri için hizmet sorumlularını da gösterebileceğini unutmayın.  Özellikle, **Exchange Online** ve **SharePoint Online** , dizinde kaynak rollerine sahip olan uygulamalar değil, bir erişim paketine dahil edilemez.  Bunun yerine, söz konusu hizmetlere erişmesi gereken kullanıcıya uygun lisansı ayarlamak için grup tabanlı lisanslamayı kullanın.

* Grubun herhangi bir erişim paketinde bir kaynak olması için Azure AD’de değiştirilebilir olması gerekir.  Şirket içi Active Directory’den kaynaklanan gruplar kaynak olarak atanamaz çünkü onların sahip veya üye öznitelikleri Azure AD’de değiştirilemez.   Exchange Online’da Dağıtım grupları olarak oluşan gruplar da Azure AD’de değiştirilemez. 

* SharePoint Online belge kitaplıkları ve tek tek belgeleri kaynak olarak eklenemez.  Bunun yerine, bir [Azure AD güvenlik grubu](../fundamentals/active-directory-groups-create-azure-portal.md)oluşturun, bu grubu ve bir site rolünü erişim paketine ekleyin ve SharePoint Online 'da belge kitaplığına veya belgeye erişimi denetlemek için bu grubu kullanın.

* Bir erişim paketiyle yönetmek istediğiniz kaynağa önceden atanmış kullanıcılar varsa, kullanıcıların erişim paketine uygun ilkeyle atandığından emin olun. Örneğin zaten grupta kullanıcıları olan bir erişim paketine grup eklemek isteyebilirsiniz. Gruptaki söz konusu kullanıcılara sürekli erişim gerekiyorsa, gruba erişimi kaybetmemeleri için erişim paketlerine yönelik uygun bir ilkeye sahip olmaları gerekir. Erişim paketini atamak için kullanıcıların bu kaynağı içeren erişim paketini istemelerini sağlayabilir veya onları doğrudan erişim paketine atayabilirsiniz. Daha fazla bilgi için bkz. [bir erişim paketi Için değişiklik isteği ve onay ayarları](entitlement-management-access-package-request-policy.md).

* Ekibin bir üyesini kaldırdığınızda, Microsoft 365 gruptan da kaldırılır. Ekibin sohbet işlevinden kaldırma işlemi geciktirilebilir. Daha fazla bilgi için bkz. [Grup üyeliği](/microsoftteams/office-365-groups#group-membership).


## <a name="access-packages"></a>Erişim paketleri

* Bir erişim paketini veya ilkeyi silmeye çalışırsanız ve etkin atamalar olduğunu bildiren bir hata iletisi görürseniz, atamaları olan herhangi bir Kullanıcı görmüyorsanız, en son silinen kullanıcıların atamalara sahip olup olmadığını denetleyin. Kullanıcı silindikten sonra 30 günlük bir pencere sırasında Kullanıcı hesabı geri yüklenebilir.   

## <a name="external-users"></a>Dış kullanıcılar

* Bir dış Kullanıcı bir erişim paketine erişim istemek istediğinde, erişim paketi için erişim **portalı** ' nı kullandıklarından emin olun. Daha fazla bilgi için bkz. [erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md). Bir dış Kullanıcı yalnızca **myaccess.Microsoft.com** ' ı ziyaret ederse ve tam erişim portalı bağlantısını kullanmıyorsa, erişim paketlerini kuruluşunuzda değil kendi kuruluşlarındaki kullanıcılara görürler.

* Bir dış Kullanıcı erişim paketine erişim isteye, veya kaynaklara erişemedik, [dış kullanıcılar için ayarlarınızı](entitlement-management-external-users.md#settings-for-external-users)denetlediğinizden emin olun.

* Daha önce dizininizde oturum açmamış olan yeni bir dış kullanıcı SharePoint Online sitesini içeren bir erişim paketi alırsa, SharePoint Online’da hesabı sağlanana kadar erişim paketi tam olarak teslim edilmemiş olarak gösterilir. Paylaşım ayarları hakkında daha fazla bilgi için bkz. [SharePoint Online dış paylaşım ayarlarınızı gözden geçirme](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>İstekler

* Bir Kullanıcı bir erişim paketine erişim istemek istediğinde, erişim paketi için erişim **portalı** ' nı kullandıklarından emin olun. Daha fazla bilgi için bkz. [erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md).

* Gizli moda ayarlanmış tarayıcınızla Erişimim portalını açarsanız, bu durum oturum açma davranışıyla çakışabilir. Erişimim portalını ziyaret ederken tarayıcınızda gizli modu kullanmamanızı öneririz.

* Henüz dizininizde yer almayan bir kullanıcı erişim paketi istemek için Erişimim portalında oturum açarsa, kurumsal hesabını kullanarak kimliğini doğruladığından emin olun. Kurumsal hesap kaynak dizinindeki bir hesap olabileceği gibi, erişim paketinin ilkelerinden birine eklenmiş olan dizindeki bir hesap da olabilir. Kullanıcının hesabı kurumsal hesap değilse veya kimlik doğrulaması yaptığı dizin ilkeye eklenmemişse, kullanıcı erişim paketini görmez. Daha fazla bilgi için bkz. [bir erişim paketine erişim isteme](entitlement-management-request-access.md).

* Kullanıcının kaynak dizininde oturum açması engellenirse Erişimim portalında erişim isteyemez. Kullanıcının erişim isteyebilmesi için önce kullanıcının profilinden oturum açma engelini kaldırmanız gerekir. Oturum açma bloğunu kaldırmak için, Azure portal **Azure Active Directory**, **Kullanıcılar**' a, Kullanıcı ' ya ve ardından **profil**' e tıklayın. **Ayarlar** bölümünü düzenleyin ve blok olarak **oturum aç '** a **değiştirin.** Daha fazla bilgi için, bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../fundamentals/active-directory-users-profile-azure-portal.md).  Kullanıcının bir [kimlik koruma ilkesi](../identity-protection/howto-identity-protection-remediate-unblock.md)nedeniyle engellenip engellenmediğini da denetleyebilirsiniz.

* Erişim portalındaki bir kullanıcı hem istek sahibi hem de onaylayan ise, **onaylar** sayfasında erişim paketi isteklerini görmez. Bu davranış bilerek ayarlanmıştır çünkü kullanıcı kendi isteğini onaylayamaz. İstediği erişim paketinin ilkede yapılandırılmış ek onaylayanları bulunduğundan emin olun. Daha fazla bilgi için bkz. [bir erişim paketi Için değişiklik isteği ve onay ayarları](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Bir isteğin teslim hatalarını görüntüleme

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İstekler**' e tıklayın.

1. Görüntülemek istediğiniz isteği seçin.

    İstekte herhangi bir teslim hatası varsa, istek durumu **teslim** edilmemiş veya **kısmen teslim** edilir.

    Herhangi bir teslim hatası varsa, isteğin ayrıntı bölmesinde teslim hatalarının sayısı görüntülenir.

1. Tüm isteğin teslim hatalarını görmek için sayıma tıklayın.

### <a name="reprocess-a-request"></a>İsteği yeniden işleme

Bir erişim paketi yeniden işlem isteği tetiklendikten sonra bir hata karşılanıyorsa, sistem isteği yeniden işlerken beklemeniz gerekir. Sistem birkaç saat boyunca yeniden işlem gerçekleştirmeye çalışır, bu nedenle bu süre içinde yeniden işlemeye zorlarsınız. 

Yalnızca **teslim durumu başarısız** veya **kısmen teslim** edildi olan bir isteği ve bir haftadan daha az tamamlanma tarihini yeniden işleyebilirsiniz. **Yeniden işleme** düğmesi, aksi durumda gri renkte görünür.

![Düğme gri renkte yeniden işleme](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Deneme süresi boyunca hata düzeltildiğinde, istek durumu **teslim** olarak değişir. İstek, kullanıcıdan ek eylem olmadan yeniden işlenecek.

- Deneme süresi boyunca hata düzeltilmediyse, istek durumu **teslim başarısız** veya **kısmen teslim edilmiş** olabilir. Sonra **yeniden işle** düğmesini kullanabilirsiniz. İsteği yeniden işlemek için yedi gününüz olacak.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İstekler**' e tıklayın.

1. Yeniden işlemek istediğiniz isteğe tıklayın.

1. İstek ayrıntıları bölmesinde, **Isteği yeniden işle**' ye tıklayın.

    ![Başarısız bir isteği yeniden işleme](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Bekleyen bir isteği iptal etme

Yalnızca teslim edilmemiş veya teslimi başarısız olan bekleyen bir isteği iptal edebilirsiniz. **İptal** düğmesi, aksi durumda gri renkte görünür.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

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
    | P4 | Tüm yapılandırılmış bağlı kuruluşlar veya tüm kullanıcılar (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar) |
    
    Herhangi bir ilke daha yüksek öncelikli bir kategoride ise, düşük öncelikli Kategoriler yoksayılır. İstek sahibine aynı önceliğe sahip birden çok ilke gösterilmesiyle ilgili bir örnek için bkz. [Ilke seçme](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Sonraki adımlar

- [Dış kullanıcılara yönelik idare erişimi](entitlement-management-external-users.md)
- [Kullanıcıların yetkilendirme yönetimi 'nde nasıl erişimi olduğunu gösteren raporları görüntüleme](entitlement-management-reports.md)
