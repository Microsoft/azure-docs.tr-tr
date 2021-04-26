---
title: PıM-Azure AD 'de Azure AD rolleri için erişim gözden geçirmesi oluşturma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rolleri için erişim gözden geçirmesi oluşturmayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 4/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aba8d9de5e068cd98675f67cb26b0eac8d1ad6d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552840"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rolleri için erişim gözden geçirmesi oluşturma

Eski rol atamalarıyla ilişkili riski azaltmak için, erişimi düzenli olarak gözden geçirmeniz gerekir. Ayrıcalıklı Azure AD rolleri için erişim gözden geçirmeleri oluşturmak üzere Azure AD Privileged Identity Management (PıM) kullanabilirsiniz. Ayrıca, otomatik olarak gerçekleşen yinelenen erişim incelemelerini yapılandırabilirsiniz.

Bu makalede, ayrıcalıklı Azure AD rolleri için bir veya daha fazla erişim incelemesi oluşturma açıklanır.

## <a name="prerequisite-license"></a>Önkoşul lisansı

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. PıM lisansları hakkında daha fazla bilgi için [Privileged Identity Management kullanmak üzere lisans gereksinimlerine](subscription-requirements.md)bakın.

> [!Note]
>  Şu anda, kiracınızda etkin bir Azure Active Directory Premium P2 sürümü ile Azure AD ve Azure Kaynak rolleri 'ne (Önizleme) erişimi olan hizmet sorumlularına yönelik bir erişim incelemesi tanımlayabilirsiniz. Hizmet sorumluları için lisanslama modeli, bu özelliğin genel kullanıma sunulmasıyla sonlandırılır ve ek lisanslar gerekebilir.

## <a name="prerequisites"></a>Önkoşullar

[Ayrıcalıklı rol yöneticisi](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Açık erişim İncelemeleri

1. Ayrıcalıklı rol yöneticisi rolünün üyesi olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.

1. **Identity idare** seçin
 
1. **Azure AD Privileged Identity Management** altında **Azure AD rolleri** ' ni seçin.
 
1. **Yönet** altında **Azure AD rolleri** ' ni yeniden seçin.

1. Yönet altında, **erişim gözden geçirmeleri**' nı seçin ve ardından **Yeni**' yi seçin.

    ![Azure AD rolleri-tüm incelemeler durumunu gösteren erişim gözden geçirmeleri listesi](./media/pim-how-to-start-security-review/access-reviews.png)

Yeni erişim gözden geçirmesi oluşturmak için **Yeni** ' ye tıklayın.

1. Erişim gözden geçirmesini adlandırın. İsteğe bağlı olarak, incelemeye bir açıklama verin. Ad ve açıklama gözden geçirenlere gösterilir.

    ![Erişim incelemesi oluşturma-adı ve açıklamayı Gözden geçirme](./media/pim-how-to-start-security-review/name-description.png)

1. **Başlangıç tarihini** ayarlayın. Varsayılan olarak, bir erişim gözden geçirmesi bir kez gerçekleşir, oluşturulduğu zaman başlatılır ve bir ayda sonlanır. Başlangıç ve bitiş tarihlerini, gelecekte bir erişim incelemesi başlatacak şekilde ve son olarak istediğiniz sayıda güne dönüştürebilirsiniz.

    ![Başlangıç tarihi, sıklık, süre, bitiş, zaman sayısı ve bitiş tarihi](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Erişim gözden geçirmesini yinelenen hale getirmek için **Sıklık** ayarını **bir saatten** **haftalık**, **aylık**, **üç aylık**, **yıllık** veya **yarı yıllık** olarak değiştirin. Her bir yinelenen serinin gözden geçiricilerin giriş için kaç gün sonra açık olacağını tanımlamak için **süre** kaydırıcısını veya metin kutusunu kullanın. Örneğin, aylık bir gözden geçirme için ayarlayabileceğiniz en uzun süre, çakışan incelemelerden kaçınmak için 27 gün olabilir.

1. Yinelenen erişim gözden geçirme serisinin nasıl sonlandıralınacağını belirtmek için **bitiş** ayarını kullanın. Seriler üç şekilde bitemez: sürekli olarak gözden geçirmeler başlatacak şekilde, belirli bir tarihe kadar veya tanımlanan sayıda oluşumdan sonra sürekli olarak çalışır. Siz, başka bir Kullanıcı Yöneticisi veya başka bir genel yönetici, **ayarları**, bu tarihte sona erecek şekilde değiştirerek, oluşturulduktan sonra seriyi durdurabilir.

1. **Kullanıcılar kapsamı** bölümünde, gözden geçirme kapsamını seçin. Azure AD rolüne erişimi olan Kullanıcı ve grupları gözden geçirmek için **Kullanıcılar ve gruplar**' ı seçin ya da Azure AD rolüne erişimi olan makine hesaplarını gözden geçirmek Için **hizmet sorumlularını seçin (Önizleme)** .

    ![Rol üyeliğini gözden geçirmek için kullanıcılara kapsam](./media/pim-how-to-start-security-review/users.png)

1. **Rol üyeliğini gözden geçir** altında, gözden geçirilecek AYRıCALıKLı Azure AD rollerini seçin. 

    > [!NOTE]
    > - Burada seçilen roller hem [kalıcı hem de uygun rolleri](../privileged-identity-management/pim-how-to-add-role-to-user.md)içerir.
    > - Birden fazla rol seçilmesi birden çok erişim incelemesi oluşturacaktır. Örneğin beş rol seçilmesi beş ayrı erişim incelemesi oluşturacaktır.
    > - Gruplarına atanmış olan roller için, gözden geçirme altındaki rolle bağlantılı her bir grubun erişimi, erişim incelemesinin bir parçası olarak gözden alınacaktır.
    **Azure AD rolleri** için erişim gözden geçirmesi oluşturuyorsanız, aşağıda gözden geçirme üyeliği listesini gösteren bir örnek gösterilir.

    ![Üyelik bölmesini İnceleme Azure AD rollerini listeleme](./media/pim-how-to-start-security-review/review-membership.png)

    **Azure Kaynak rolleri** için erişim gözden geçirmesi oluşturuyorsanız aşağıdaki görüntüde üyelik İnceleme listesinin bir örneği gösterilir.

    ![Üyelik bölmesini İnceleme Azure Kaynak rollerini listeleme](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. **Gözden geçirenler** bölümünde, tüm kullanıcıları gözden geçirmek için bir veya daha fazla kişi seçin. Ya da üyelerin kendi erişimini incelemesini seçebilirsiniz.

    ![Seçili kullanıcıların veya üyelerin gözden geçirenler listesi (self)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Seçili kullanıcılar** -bu seçeneği, gözden geçirmeyi tamamlayacak belirli bir kullanıcıyı belirlemek için kullanın. Bu seçenek, gözden geçirme kapsamlarından bağımsız olarak kullanılabilir ve seçili gözden geçirenler Kullanıcı, Grup ve hizmet sorumlularını gözden geçirebilir. 
    - **Üyeler (self)** -bu seçeneği, kullanıcıların kendi rol atamalarını gözden geçirmesini sağlamak için kullanın. Role atanan gruplar, bu seçenek belirlendiğinde, gözden geçirme bir parçası olmayacaktır. Bu seçenek yalnızca İnceleme, **Kullanıcılar ve gruplar** kapsamalıyorsa kullanılabilir.
    - **Yönetici** – Kullanıcı yöneticisinin rol atamasını gözden geçirmesini sağlamak için bu seçeneği kullanın. Bu seçenek yalnızca İnceleme, **Kullanıcılar ve gruplar** kapsamalıyorsa kullanılabilir. Yönetici ' yi seçtikten sonra, bir geri dönüş gözden geçireni belirtme seçeneğine de sahip olursunuz. Kullanıcı, dizinde hiçbir yönetici belirtilmediğinde, geri dönüş gözden geçiricilerin Kullanıcı tarafından incelenmesi istenir. Role atanan gruplar, bir seçilirse, geri dönüş gözden geçireni tarafından incelenmeyecektir. 

### <a name="upon-completion-settings"></a>Tamamlama ayarlarından sonra

1. Bir gözden geçirme tamamlandıktan sonra ne olacağını belirtmek için **Tamamlama Ayarları** bölümüne bakın.

    ![Otomatik olarak uygulanacak ve gözden geçirmemelidir](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Reddedilen kullanıcılar için erişimi otomatik olarak kaldırmak istiyorsanız, **etkinleştirilecek** **sonuçları kaynağa otomatik uygula** ' yı ayarlayın. Gözden geçirme tamamlandığında sonuçları el ile uygulamak istiyorsanız, anahtarı **devre dışı** olarak ayarlayın.

1. Gözden geçiren tarafından gözden geçirilmemiş kullanıcılar için gözden geçirme süresi içinde gözden **geçirmeli yanıt** verme listesini kullanın. Bu ayar, gözden geçirenler tarafından el ile gözden geçirilmiş kullanıcıları etkilemez. Son Gözden geçirenin kararı reddederse, kullanıcının erişimi kaldırılır.

    - **Değişiklik yok** -kullanıcının erişimini değiştirmeden bırak
    - **Erişimi kaldır** -kullanıcının erişimini kaldır
    - **Erişimi onayla** -kullanıcının erişimini Onayla
    - **Öneriler alın** -kullanıcının devam eden erişimini reddetme veya onaylama konusunda sistemin önerisini alın

### <a name="advanced-settings"></a>Gelişmiş ayarlar

1. Ek ayarları belirtmek için **Gelişmiş ayarlar** bölümünü genişletin.

    ![Önerileri göster, onay için neden iste, posta bildirimleri ve anımsatıcılar için Gelişmiş ayarlar](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Kullanıcının erişim bilgilerini temel alarak sistem önerilerini gözden geçirenlere göstermek üzere **etkinleştirilecek** **önerileri göster** ' i ayarlayın.

1. Gözden geçirenin onay için bir neden vermesini gerektirmesini **sağlamak** için onay için bir **neden gerektir '** i ayarlayın.

1. Bir erişim incelemesi başladığında ve bir gözden geçirme tamamlandığında yöneticilere, Azure **ad 'nin gözden** geçirenlere e-posta bildirimleri göndermesini sağlamak için **posta bildirimleri** ayarlayın.

1. Azure AD 'nin, gözden geçirenleri tamamlamadıkları gözden geçirenler için sürmekte olan erişim gözden geçirmeleri gönderme anımsatıcıları **sağlamak üzere** **anımsatıcıları** ayarlayın.
1. Gözden geçirenlere gönderilen e-postanın içeriği İnceleme adı, kaynak adı, son tarih vb. gözden geçirme ayrıntılarına göre otomatik olarak oluşturulur. Ek yönergeler veya iletişim bilgileri gibi ek bilgilere iletişim kurmak için bir yönteme ihtiyacınız varsa, bu ayrıntıları **Gözden geçiren Için ek içerikte** , davet ve anımsatıcı e-postalarına gönderilen gözden geçirenler e-postalarına dahil edilecek şekilde belirtebilirsiniz. Aşağıdaki Vurgulanan bölümde bu bilgilerin görüntüleneceği yer verilmiştir.

    ![İnceleyenler ile gözden geçirenlere gönderilen e-postanın içeriği](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Erişim gözden geçirmesini Başlat

Erişim gözden geçirmesi ayarlarını belirttikten sonra **Başlat**' ı seçin. Erişim incelemesi, listenizde durumunun bir göstergesi olacak şekilde görünür.

![Başlangıç incelemelerinin durumunu gösteren erişim gözden geçirmeleri listesi](./media/pim-how-to-start-security-review/access-reviews-list.png)

Varsayılan olarak, Azure AD gözden geçirenlere İnceleme başladıktan kısa bir bir e-posta gönderir. Azure AD 'ye e-posta gönderme seçeneğini belirlerseniz, gözden geçirenlere bir erişim incelemesinin tamamlanmasını beklediğini bildirin. [Azure AD rollerine erişimin nasıl incelemesinin](pim-how-to-perform-security-review.md)yapılacağı yönergeleri gösterebilirsiniz.

## <a name="manage-the-access-review"></a>Erişim gözden geçirmesini yönetme

Gözden geçirenler, erişim incelemesinin **genel bakış** sayfasında İncelemeleri tamamlamalarını izleyerek ilerlemeyi izleyebilirsiniz. [İnceleme tamamlanana](pim-how-to-complete-review.md)kadar dizinde erişim hakkı değiştirilmez.

![İnceleme ayrıntılarını gösteren erişim gözden geçirmeleri genel bakış sayfası](./media/pim-how-to-start-security-review/access-review-overview.png)

Bu bir kerelik bir gözden geçirime sahip olursa, erişim gözden geçirme süresi dolduktan sonra veya yönetici erişim gözden geçirmesini durdurduktan sonra, sonuçları görmek ve uygulamak için [Azure AD rollerinin erişim gözden geçirmesini tamamlayın](pim-how-to-complete-review.md) ' daki adımları izleyin.  

Bir dizi erişim incelemesini yönetmek için, erişim gözden geçirmelerine gidin ve zamanlanan incelemelerde yaklaşan örnekleri bulacaksınız ve son tarihi düzenleyin ya da gözden geçirenleri buna uygun olarak ekleyin/kaldırın.

**Tamamlanma ayarları sırasında** yaptığınız seçimlere bağlı olarak, otomatik uygulama İnceleme bitiş tarihinden sonra veya gözden geçirmeyi el ile durdurduğunuzda yürütülür. İncelemesinin durumu, **uygulama** ve son durum **uygulandı** gibi ara durumlar aracılığıyla **tamamlandı** olarak değişir. Birkaç dakika içinde rollerden kaldırılan reddedilen kullanıcıları görmeyi beklemelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD rollerine erişimi gözden geçirme](pim-how-to-perform-security-review.md)
- [Azure AD rollerinin erişim incelemesini tamamlar](pim-how-to-complete-review.md)
- [Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](pim-resource-roles-start-access-review.md)
