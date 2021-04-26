---
title: Gruplar & uygulamalar için erişim gözden geçirmesi oluşturma-Azure AD
description: Azure Active Directory erişim gözden geçirmeleriyle grup üyelerinin veya uygulama erişiminin erişim incelemesini oluşturmayı öğrenin.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532262"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleriyle gruplar ve uygulamalar için erişim gözden geçirmesi oluşturma

Çalışanlar için gruplara ve uygulamalara erişim ve zaman içinde konuklar değişiklikleri. Yöneticiler, eski erişim atamalarıyla ilişkili riski azaltmak için Azure Active Directory (Azure AD) kullanarak Grup üyeleri veya uygulama erişimi için erişim gözden geçirmeleri oluşturabilir. Erişimi düzenli olarak gözden geçirmeniz gerekiyorsa yinelenen erişim İncelemeleri de oluşturabilirsiniz. Bu senaryolar hakkında daha fazla bilgi için bkz. [Kullanıcı erişimini yönetme](manage-user-access-with-access-reviews.md) ve [konuk erişimini yönetme](manage-guest-access-with-access-reviews.md).

Erişim Incelemelerini etkinleştirme hakkında hızlı bir video izleyebilirsiniz:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Bu makalede, Grup üyeleri veya uygulama erişimi için bir veya daha fazla erişim incelemesi oluşturma açıklanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure AD Premium P2
- Genel yönetici veya Kullanıcı Yöneticisi

Daha fazla bilgi için bkz. [Lisans gereksinimleri](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Bir veya daha fazla erişim incelemesi oluşturun

1. Azure portal oturum açın ve [Identity idare sayfasını](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)açın.

2. Sol menüde, **erişim gözden geçirmeleri**' na tıklayın.

3. Yeni erişim gözden geçirmesi oluşturmak için **Yeni erişim gözden geçirmesi** ' na tıklayın.

    ![Kimlik yönetimi 'nde erişim İncelemeleri bölmesi](./media/create-access-review/access-reviews.png)

4. **1. Adım: nelerin gözden** geçirilmesini istediğinizi seçin bölümünde hangi kaynağın gözden geçirilmesini istediğinizi seçin.

    ![Erişim incelemesi oluşturma-adı ve açıklamayı Gözden geçirme](./media/create-access-review/select-what-review.png)

5. 1. adımda **takımlar + gruplar** ' ı seçtiyseniz, adım 2 ' de iki seçeneğe sahip olursunuz.
   - **Konuk kullanıcıları olan tüm Microsoft 365 grupları.** Kuruluşunuzdaki tüm Microsoft ekipleriniz ve M365 grupları genelinde tüm konuk kullanıcılarınız için yinelenen incelemeler oluşturmak istiyorsanız bu seçeneği belirleyin. ' Hariç tutulacak grupları seç ' e tıklayarak belirli grupları dışarıda bırakmayı seçebilirsiniz.
   - **Takımlar + gruplar ' ı seçin.** Bunları gözden geçirmek için sınırlı bir takım ve/veya grup kümesi belirtmek istiyorsanız bu seçeneği belirleyin. Bu seçeneğe tıkladıktan sonra, seçim yapmak için sağdaki grupların bir listesini görürsünüz.

     ![Takımlar ve gruplar](./media/create-access-review/teams-groups.png)

     ![Kullanıcı arabiriminde seçilen takımlar ve gruplar](./media/create-access-review/teams-groups-detailed.png)

6. Adım 1 ' de **uygulamalar** ' ı seçtiyseniz, adım 2 ' de bir veya daha fazla uygulamayı seçebilirsiniz.

    >[!NOTE]
    > Birden çok grup ve/veya uygulama seçmek birden çok erişim incelemelerine neden olur. Örneğin, gözden geçirilecek 5 grup seçerseniz bu, 5 ayrı erişim incelemelerine neden olur

   ![Gruplar yerine uygulamalar seçerseniz, bu arabirim gösterilir](./media/create-access-review/select-application-detailed.png)

7. Ardından adım 3 ' te İnceleme için bir kapsam seçebilirsiniz. Seçenekleriniz şunlardır
   - **Yalnızca Konuk kullanıcılar.** Bu seçeneğin belirlenmesi, erişim incelemesini dizininizde yalnızca Azure AD B2B Konuk kullanıcılarına sınırlandırır.
   - **Anlamasına.** Bu seçeneğin belirlenmesi, erişim incelemesini kaynakla ilişkili tüm kullanıcı nesnelerine kapsamlar.

    >[!NOTE]
    > 2. adımda Konuk kullanıcıları olan tüm Microsoft 365 gruplarını seçtiyseniz tek seçeneğiniz, 3. adımdaki Konuk kullanıcıları gözden geçirmeniz

8. Ileri ' ye tıklayın: Incelemeler
9. **Gözden geçirenler Seç** bölümünde, erişim incelemelerini gerçekleştirmek için bir veya daha fazla kişi seçin. Şunlar arasından seçim yapabilirsiniz:
    - **Grup sahibi** (yalnızca bir takım veya grup üzerinde inceleme gerçekleştirirken kullanılabilir)
    - **Seçili kullanıcı veya gruplar**
    - **Kullanıcılar kendi erişimini gözden geçirir**
    - **Kullanıcı yöneticileri.**
    Kullanıcı veya **Grup sahiplerinin** **yöneticileri** ' ni seçerseniz, geri dönüş gözden geçireni belirleme seçeneğiniz de vardır. Kullanıcı, dizinde yönetici olmadığında veya grubun sahibi olmadığında, geri dönüşün gözden geçirilmesi istenir.

    ![Yeni erişim gözden geçirmesi](./media/create-access-review/new-access-review.png)

10. **Gözden geçirmeyi tekrarlama belirtin** bölümünde, **haftalık, aylık, üç aylık, yarı yıllık, yılda** bir sıklık belirtebilirsiniz. Daha sonra bir **süre** belirlersiniz. Bu, gözden geçirenlerin girişi için ne kadar süreyle açık olacağını tanımlar. Örneğin, aylık bir gözden geçirme için ayarlayabileceğiniz en uzun süre, çakışan incelemelerden kaçınmak için 27 gün olabilir. Gözden geçirenler girişinin daha önce uygulandığından emin olmak için süreyi kısaltmak isteyebilirsiniz. Ardından, bir **Başlangıç tarihi** ve **bitiş tarihi** seçebilirsiniz.

    ![İncelemenin ne sıklıkta olacağını seçin](./media/create-access-review/frequency.png)

11. Sayfanın alt kısmındaki **İleri: ayarlar** düğmesine tıklayın
12. **Tamamlanma ayarları** ' nda, gözden geçirme tamamlandıktan sonra ne olacağını belirtebilirsiniz

    ![Erişim gözden geçirmesi oluşturma-tamamlanma ayarları](./media/create-access-review/upon-completion-settings-new.png)

Engellenen kullanıcıların erişimini otomatik olarak kaldırmak istiyorsanız, etkinleştirmek için sonuçları otomatik Uygula ' yı ayarlayın. Gözden geçirme tamamlandığında sonuçları el ile uygulamak istiyorsanız, anahtarı devre dışı olarak ayarlayın.
Gözden geçirenler yanıt vermiyor listesini, gözden geçiren tarafından gözden geçirilmemiş kullanıcılar için gözden geçirme dönemi dahilinde ne olacağını belirlemek için kullanın. Bu ayar, gözden geçirenler tarafından el ile gözden geçirilmiş kullanıcıları etkilemez. Son Gözden geçirenin kararı reddederse, kullanıcının erişimi kaldırılır.

- **Değişiklik yok** -kullanıcının erişimini değiştirmeden bırak
- **Erişimi kaldır** -kullanıcının erişimini kaldır
- **Erişimi onayla** -kullanıcının erişimini Onayla
- **Öneriler alın** -kullanıcının devam eden erişimini reddetme veya onaylama konusunda sistemin önerisini alın

    ![Tamamlanma ayarları seçeneklerinin ardından](./media/create-access-review/upon-completion-settings-new.png)

Engellenen **Konuk** kullanıcılara, engellendiklerinde Konuk kullanıcılar için ne olacağını belirtmek Için bu eylemi kullanın.
- Kullanıcının kaynaktan üyeliğini kaldırma, reddedilen kullanıcının, incelenmekte olan grup veya uygulamaya erişimini kaldıracak, yine de kiracıya oturum açabiliyor.
- Kullanıcının 30 gün boyunca oturum açmasını engelleyin, sonra da kullanıcıyı kiracının içinden kaldırmak, diğer kaynaklara erişimleri olmasına bakılmaksızın reddedilen kullanıcıların kiracıya kaydolmasını engeller. Bir hata oluşursa veya bir yönetici, birinin erişimini yeniden etkinleştirmeye karar verirse, Kullanıcı devre dışı bırakıldıktan sonra 30 gün içinde bunu yapabilir. Devre dışı bırakılan kullanıcılar üzerinde herhangi bir eylem yoksa, kiracı 'dan silinir.

Kuruluşunuzdaki kaynaklara erişimi olmayan konuk kullanıcıları kaldırmak için en iyi uygulamalar hakkında daha fazla bilgi edinmek için, [artık kaynak erişimi olmayan dış kullanıcıları gözden geçirmek ve kaldırmak üzere Azure AD Identity Governance kullanın](access-reviews-external-users.md) başlıklı makaleyi okuyun.

   >[!NOTE]
   >Engellenen Konuk kullanıcılara uygulanacak eylem, Konuk kullanıcılardan daha fazla Kullanıcı kapsamındaki incelemelerde yapılandırılamaz. Ayrıca **Konuk kullanıcıları olan tüm M365 gruplarının** incelemeleri için yapılandırılamaz. Yapılandırılabilir olmadığında, kullanıcının kaynaktan üyeliğini kaldırma varsayılan seçeneği, reddedilen kullanıcılar üzerinde kullanılır.

13. **Gözden geçirmeyi etkinleştir karar sağlayanlar** bölümünde gözden geçirenin İnceleme işlemi sırasında öneriler almasını isteyip istemediğinizi seçin.

    ![Karar verme yardımcıları seçeneklerini etkinleştirin](./media/create-access-review/helpers.png)

14. **Gelişmiş ayarlar** bölümünde aşağıdakileri seçebilirsiniz
    - Gözden geçirenin onay için bir neden vermesini gerektirmesini **sağlamak** için **gerekçe ayarlama gerekiyor** .
    - Bir erişim incelemesi başladığında ve bir gözden geçirme tamamlandığında yöneticilere, Azure **ad 'nin gözden** geçirenlere e-posta bildirimleri göndermesini sağlamak için **e-posta bildirimleri** ayarlayın.
    - Azure AD 'nin, gözden geçirenleri tamamlamadıkları gözden geçirenler için sürmekte olan erişim gözden geçirmeleri gönderme anımsatıcıları **sağlamak üzere** **anımsatıcıları** ayarlayın. Bu anımsatıcılar, gözden geçirme süresince kendi yarı bir yoludur.
    - Gözden geçirenlere gönderilen e-postanın içeriği İnceleme adı, kaynak adı, son tarih vb. gözden geçirme ayrıntılarına göre otomatik olarak oluşturulur. Ek yönergeler veya iletişim bilgileri gibi ek bilgileri iletmek için bir yönteme ihtiyacınız varsa, bu ayrıntıları **Gözden geçiren Için ek içerik e-postası** bölümünde belirtebilirsiniz. Girdiğiniz bilgiler, davet ve anımsatıcı e-postalarına atanmış gözden geçirenler 'e dahildir. Aşağıdaki görüntüde vurgulanan bölümde bu bilgilerin görüntülendiği yer gösterilmektedir.


      ![Gözden geçiren için ek içerik](./media/create-access-review/additional-content-reviewer.png)

15. Ileri ' ye tıklayın: sonraki sayfaya geçmek için **+ Oluştur** ' u inceleyin
16. Erişim gözden geçirmesini adlandırın. İsteğe bağlı olarak, incelemeye bir açıklama verin. Ad ve açıklama gözden geçirenlere gösterilir.
17. Bilgileri gözden geçirin ve **Oluştur** ' u seçin.

       ![İnceleme ekranı oluştur](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Erişim gözden geçirmesini Başlat

Erişim gözden geçirmesi ayarlarını belirttikten sonra **Başlat**' a tıklayın. Erişim incelemesi, listenizde durumunun bir göstergesi olacak şekilde görünür.

![Erişim gözden geçirmeleri ve durumlarının listesi](./media/create-access-review/access-reviews-list.png)

Varsayılan olarak, Azure AD gözden geçirenlere İnceleme başladıktan kısa bir bir e-posta gönderir. Azure AD 'ye e-posta gönderme seçeneğini belirlerseniz, gözden geçirenlere bir erişim incelemesinin tamamlanmasını beklediğini bildirin. Bunlara, [gruplara veya uygulamalara erişimi gözden geçirme](perform-access-review.md)yönergelerini gösterebilirsiniz. Gözden geçirmeniz konuklarınız kendi erişimini gözden geçirmeleri için ise, bunları [gruplara veya uygulamalara yönelik erişimin nasıl incelendiğinin](review-your-access.md)talimatlarını gösterir.

Konukları gözden geçirenler olarak atadıysanız ve daveti kabul etmediyse, bu kullanıcılar, gözden geçirenlerden önce daveti kabul etmeleri gerektiğinden, erişim incelemelerinden e-posta almamalıdır.

## <a name="access-review-status-table"></a>Erişim gözden geçirmesi durum tablosu

| Durum | Tanım |
|--------|------------|
|NotStarted | İnceleme oluşturuldu, Kullanıcı keşfi başlamak için bekliyor. |
|Başlatılıyor   | Gözden geçirinin parçası olan tüm kullanıcıları tanımlamak için Kullanıcı keşfi devam ediyor. |
|Başlatılıyor | İnceleme başlatılıyor. E-posta bildirimleri etkinse, e-postalar gözden geçirenlere gönderilir. |
|Ediyor | İnceleme başlatıldı. E-posta bildirimlerinin etkin olduğu e-postalar gözden geçirenlere gönderilmiştir. Gözden geçirenler, son tarihe kadar karar gönderebilir. |
|Tamamlan | Gözden geçirme tamamlandı ve gözden geçirme sahibine e-postalar gönderiliyor. |
|Otomatik Gözden geçirme | İnceleme bir sistem inceleme aşamasında. Sistem, öneriler veya önceden yapılandırılmış kararlar temelinde incelenebilecek kullanıcılar için kararları kaydediyor. |
|Otomatik Gözden geçirme | Gözden geçirilmemiş tüm kullanıcılar için sistem tarafından kararlar kaydedilir. İnceleme, otomatik uygulama etkinse **uygulamaya** devam etmek için hazırlayın. |
|Uygulamayı | Onaylanan kullanıcılar için erişim değişikliği olmayacaktır. |
|Uygulandı | Varsa, reddedilen kullanıcılar kaynak veya dizinden kaldırılmıştır. |
|Başarısız | İnceleme ilerleme durumunu gerçekleştiremedi. Bu hata, kiracının silinme, lisanslarda değişiklik veya diğer iç kiracı değişiklikleri ile ilgili olabilir. |

## <a name="create-reviews-via-apis"></a>API 'Ler aracılığıyla incelemeler oluşturma

API 'Leri kullanarak erişim İncelemeleri de oluşturabilirsiniz. Azure portal grup ve uygulama kullanıcılarının erişim incelemelerini yönetmek için ne yapmanız gerekir Microsoft Graph API 'Leri kullanılarak da yapılabilir. Daha fazla bilgi için bkz. [Azure AD erişim gözden GEÇIRMELERI API başvurusu](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true). Kod örneği için bkz. [Microsoft Graph aracılığıyla Azure AD erişim gözden geçirmeleri alma örneği](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Sonraki adımlar

- [Gruplara veya uygulamalara erişimi gözden geçirme](perform-access-review.md)
- [Grupları veya uygulamaları kendiniz için erişimi gözden geçirin](review-your-access.md)
- [Grupların veya uygulamaların erişim incelemesini tamamlar](complete-access-review.md)