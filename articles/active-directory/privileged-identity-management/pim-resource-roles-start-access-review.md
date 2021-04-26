---
title: PıM-Azure AD | Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturmayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c0ce72348f67c22759915a3a15c69193ad2f60
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552806"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma

Çalışanların zaman içinde değişiklik yaptığı ayrıcalıklı Azure Kaynak rollerine erişim gereksinimi. Eski rol atamalarıyla ilişkili riski azaltmak için, erişimi düzenli olarak gözden geçirmeniz gerekir. Azure Kaynak rollerine ayrıcalıklı erişim için erişim gözden geçirmeleri oluşturmak üzere Azure Active Directory (Azure AD) Privileged Identity Management (PıM) kullanabilirsiniz. Ayrıca, otomatik olarak gerçekleşen yinelenen erişim incelemelerini yapılandırabilirsiniz. Bu makalede bir veya daha fazla erişim incelemesi oluşturma açıklanır.

## <a name="prerequisite-license"></a>Önkoşul lisansı

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. PıM lisansları hakkında daha fazla bilgi için [Privileged Identity Management kullanmak üzere lisans gereksinimlerine](subscription-requirements.md)bakın.

> [!Note]
>  Şu anda, kiracınızda etkin bir Azure Active Directory Premium P2 sürümü ile Azure AD ve Azure Kaynak rolleri 'ne (Önizleme) erişimi olan hizmet sorumlularına yönelik bir erişim incelemesi tanımlayabilirsiniz. Hizmet sorumluları için lisanslama modeli, bu özelliğin genel kullanıma sunulmasıyla sonlandırılır ve ek lisanslar gerekebilir.

## <a name="prerequisite-role"></a>Önkoşul rolü

 Erişim İncelemeleri oluşturmak için, kaynak için [sahip](../../role-based-access-control/built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) Azure rolüne atanmış olmanız gerekir.

## <a name="open-access-reviews"></a>Açık erişim İncelemeleri

1. Önkoşul rollerinden birine atanan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) için oturum açın.

1. **Identity idare** seçin
 
1. Sol taraftaki menüden **Azure AD Privileged Identity Management** altında **Azure kaynakları** ' nı seçin.

1. Yönetmek istediğiniz kaynağı bir abonelik gibi seçin.

1. Yönet altında **erişim gözden geçirmeleri**' nı seçin.

    ![Azure kaynakları-tüm incelemelerinin durumunu gösteren erişim gözden geçirmeleri listesi](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Yeni erişim gözden geçirmesi oluşturmak için **Yeni** ' ye tıklayın.

1. Erişim gözden geçirmesini adlandırın. İsteğe bağlı olarak, incelemeye bir açıklama verin. Ad ve açıklama gözden geçirenlere gösterilir.

    ![Erişim incelemesi oluşturma-adı ve açıklamayı Gözden geçirme](./media/pim-resource-roles-start-access-review/name-description.png)

1. **Başlangıç tarihini** ayarlayın. Varsayılan olarak, bir erişim gözden geçirmesi bir kez gerçekleşir, oluşturulduğu zaman başlatılır ve bir ayda sonlanır. Başlangıç ve bitiş tarihlerini, gelecekte bir erişim incelemesi başlatacak şekilde ve son olarak istediğiniz sayıda güne dönüştürebilirsiniz.

    ![Başlangıç tarihi, sıklık, süre, bitiş, zaman sayısı ve bitiş tarihi](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Erişim gözden geçirmesini yinelenen hale getirmek için **Sıklık** ayarını **bir saatten** **haftalık**, **aylık**, **üç aylık**, **yıllık** veya **yarı yıllık** olarak değiştirin. Her bir yinelenen serinin gözden geçiricilerin giriş için kaç gün sonra açık olacağını tanımlamak için **süre** kaydırıcısını veya metin kutusunu kullanın. Örneğin, aylık bir gözden geçirme için ayarlayabileceğiniz en uzun süre, çakışan incelemelerden kaçınmak için 27 gün olabilir.

1. Yinelenen erişim gözden geçirme serisinin nasıl sonlandıralınacağını belirtmek için **bitiş** ayarını kullanın. Seriler üç şekilde bitemez: sürekli olarak gözden geçirmeler başlatacak şekilde, belirli bir tarihe kadar veya tanımlanan sayıda oluşumdan sonra sürekli olarak çalışır. Siz, başka bir Kullanıcı Yöneticisi veya başka bir genel yönetici, **ayarları**, bu tarihte sona erecek şekilde değiştirerek, oluşturulduktan sonra seriyi durdurabilir.

1. **Kullanıcılar** bölümünde, gözden geçirme kapsamını seçin. Kullanıcıları gözden geçirmek için, Azure rolüne erişimi olan makine hesaplarını gözden geçirmek üzere **kullanıcıları seçin veya hizmet sorumlularını seçin (Önizleme)** .   

    ![Rol üyeliğini gözden geçirmek için kullanıcılara kapsam](./media/pim-resource-roles-start-access-review/users.png)


1. **Rol üyeliğini gözden geçir** altında, gözden geçirmek Için ayrıcalıklı Azure rolleri ' ni seçin. 

    > [!NOTE]
    > - Burada seçilen roller hem [kalıcı hem de uygun rolleri](../privileged-identity-management/pim-how-to-add-role-to-user.md)içerir.
    > - Birden fazla rol seçilmesi birden çok erişim incelemesi oluşturacaktır. Örneğin beş rol seçilmesi beş ayrı erişim incelemesi oluşturacaktır.
    **Azure AD rolleri** için erişim gözden geçirmesi oluşturuyorsanız, aşağıda gözden geçirme üyeliği listesini gösteren bir örnek gösterilir.

    ![Üyelik bölmesini İnceleme Azure AD rollerini listeleme](./media/pim-resource-roles-start-access-review/review-membership.png)

    **Azure Kaynak rolleri** için erişim gözden geçirmesi oluşturuyorsanız aşağıdaki görüntüde üyelik İnceleme listesinin bir örneği gösterilir.

    ![Üyelik bölmesini İnceleme Azure Kaynak rollerini listeleme](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. **Gözden geçirenler** bölümünde, tüm kullanıcıları gözden geçirmek için bir veya daha fazla kişi seçin. Ya da üyelerin kendi erişimini incelemesini seçebilirsiniz.

    ![Seçili kullanıcıların veya üyelerin gözden geçirenler listesi (self)](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Seçili kullanıcılar** -bu seçeneği, gözden geçirmeyi tamamlayacak belirli bir kullanıcıyı belirlemek için kullanın. Bu seçenek, gözden geçirme kapsamlarından bağımsız olarak kullanılabilir ve seçili gözden geçirenler kullanıcıları ve hizmet sorumlularını gözden geçirebilir. 
    - **Üyeler (self)** -bu seçeneği, kullanıcıların kendi rol atamalarını gözden geçirmesini sağlamak için kullanın. Bu seçenek yalnızca gözden geçirme **kullanıcıların** kapsamına eklendiğinde kullanılabilir.
    - **Yönetici** – Kullanıcı yöneticisinin rol atamasını gözden geçirmesini sağlamak için bu seçeneği kullanın. Bu seçenek yalnızca gözden geçirme **kullanıcıların** kapsamına eklendiğinde kullanılabilir. Yönetici ' yi seçtikten sonra, bir geri dönüş gözden geçireni belirtme seçeneğine de sahip olursunuz. Kullanıcı, dizinde hiçbir yönetici belirtilmediğinde, geri dönüş gözden geçiricilerin Kullanıcı tarafından incelenmesi istenir. 

### <a name="upon-completion-settings"></a>Tamamlama ayarlarından sonra

1. Bir gözden geçirme tamamlandıktan sonra ne olacağını belirtmek için **Tamamlama Ayarları** bölümüne bakın.

    ![Otomatik olarak uygulanacak ve gözden geçirmemelidir](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Reddedilen kullanıcılar için erişimi otomatik olarak kaldırmak istiyorsanız, **etkinleştirilecek** **sonuçları kaynağa otomatik uygula** ' yı ayarlayın. Gözden geçirme tamamlandığında sonuçları el ile uygulamak istiyorsanız, anahtarı **devre dışı** olarak ayarlayın.

1. Gözden geçiren tarafından gözden geçirilmemiş kullanıcılar için gözden geçirme süresi içinde gözden **geçirmeli yanıt** verme listesini kullanın. Bu ayar, gözden geçirenler tarafından el ile gözden geçirilmiş kullanıcıları etkilemez. Son Gözden geçirenin kararı reddederse, kullanıcının erişimi kaldırılır.

    - **Değişiklik yok** -kullanıcının erişimini değiştirmeden bırak
    - **Erişimi kaldır** -kullanıcının erişimini kaldır
    - **Erişimi onayla** -kullanıcının erişimini Onayla
    - **Öneriler alın** -kullanıcının devam eden erişimini reddetme veya onaylama konusunda sistemin önerisini alın

### <a name="advanced-settings"></a>Gelişmiş ayarlar

1. Ek ayarları belirtmek için **Gelişmiş ayarlar** bölümünü genişletin.

    ![Önerileri göster, onay için neden iste, posta bildirimleri ve anımsatıcılar için Gelişmiş ayarlar](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Kullanıcının erişim bilgilerini temel alarak sistem önerilerini gözden geçirenlere göstermek üzere **etkinleştirilecek** **önerileri göster** ' i ayarlayın.

1. Gözden geçirenin onay için bir neden vermesini gerektirmesini **sağlamak** için onay için bir **neden gerektir '** i ayarlayın.

1. Bir erişim incelemesi başladığında ve bir gözden geçirme tamamlandığında yöneticilere, Azure **ad 'nin gözden** geçirenlere e-posta bildirimleri göndermesini sağlamak için **posta bildirimleri** ayarlayın.

1. Azure AD 'nin, gözden geçirenleri tamamlamadıkları gözden geçirenler için sürmekte olan erişim gözden geçirmeleri gönderme anımsatıcıları **sağlamak üzere** **anımsatıcıları** ayarlayın.
1. Gözden geçirenlere gönderilen e-postanın içeriği İnceleme adı, kaynak adı, son tarih vb. gözden geçirme ayrıntılarına göre otomatik olarak oluşturulur. Ek yönergeler veya iletişim bilgileri gibi ek bilgilere iletişim kurmak için bir yönteme ihtiyacınız varsa, bu ayrıntıları **Gözden geçiren Için ek içerikte** , davet ve anımsatıcı e-postalarına gönderilen gözden geçirenler e-postalarına dahil edilecek şekilde belirtebilirsiniz. Aşağıdaki Vurgulanan bölümde bu bilgilerin görüntüleneceği yer verilmiştir.

    ![İnceleyenler ile gözden geçirenlere gönderilen e-postanın içeriği](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>Erişim gözden geçirmesini Başlat

Erişim gözden geçirmesi ayarlarını belirttikten sonra **Başlat**' a tıklayın. Erişim incelemesi, listenizde durumunun bir göstergesi olacak şekilde görünür.

![Başlangıç incelemesi durumunu gösteren erişim gözden geçirmeleri listesi](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Varsayılan olarak, Azure AD gözden geçirenlere İnceleme başladıktan kısa bir bir e-posta gönderir. Azure AD 'ye e-posta gönderme seçeneğini belirlerseniz, gözden geçirenlere bir erişim incelemesinin tamamlanmasını beklediğini bildirin. [Azure Kaynak rollerine erişimi gözden geçirme](pim-resource-roles-perform-access-review.md)yönergelerini görüntüleyebilirsiniz.

## <a name="manage-the-access-review"></a>Erişim gözden geçirmesini yönetme

Gözden geçirenler, erişim incelemesinin **genel bakış** sayfasında İncelemeleri tamamlamalarını izleyerek ilerlemeyi izleyebilirsiniz. [İnceleme tamamlanana](pim-resource-roles-complete-access-review.md)kadar dizinde erişim hakkı değiştirilmez.

![İnceleme ayrıntılarını gösteren erişim gözden geçirmeleri genel bakış sayfası](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Bu bir kerelik bir gözden geçirime sahip olursa, erişim gözden geçirme süresi dolduktan sonra veya yönetici erişim gözden geçirmesini durdurduktan sonra, sonuçları görmek ve uygulamak için [Azure Kaynak rollerinin erişim Incelemesini tamamlayın](pim-resource-roles-complete-access-review.md) .  

Bir dizi erişim incelemesini yönetmek için, erişim gözden geçirmelerine gidin ve zamanlanan incelemelerde yaklaşan örnekleri bulacaksınız ve son tarihi düzenleyin ya da gözden geçirenleri buna uygun olarak ekleyin/kaldırın.

**Tamamlanma ayarları sırasında** yaptığınız seçimlere bağlı olarak, otomatik uygulama İnceleme bitiş tarihinden sonra veya gözden geçirmeyi el ile durdurduğunuzda yürütülür. İncelemesinin durumu, **uygulama** ve son durum **uygulandı** gibi ara durumlar aracılığıyla **tamamlandı** olarak değişir. Birkaç dakika içinde rollerden kaldırılan reddedilen kullanıcıları görmeyi beklemelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Kaynak rollerine erişimi gözden geçirme](pim-resource-roles-perform-access-review.md)
- [Azure Kaynak rolleri için erişim gözden geçirmesini doldurun](pim-resource-roles-complete-access-review.md)
- [Azure AD rolleri için erişim gözden geçirmesi oluşturma](pim-how-to-start-security-review.md)
