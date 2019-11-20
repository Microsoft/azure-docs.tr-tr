---
title: Kullanım koşulları-Azure Active Directory | Microsoft Docs
description: Erişim almadan önce çalışanlara veya konuklara bilgi sunmak için kullanım koşulları Azure Active Directory kullanmaya başlayın.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31d84d5bf43bac55769a6479917794a51c1ccd0c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999115"
---
# <a name="azure-active-directory-terms-of-use"></a>Kullanım koşulları Azure Active Directory

Azure AD kullanım koşulları, kuruluşların son kullanıcılara bilgi sunmak için kullanabileceği basit bir yöntem sağlar. Bu sunum, kullanıcıların yasal gereksinimler veya uyumluluk gereksinimleriyle ilgili bildirimleri görmesi sağlar. Bu makalede kullanım koşulları ile çalışmaya başlama konusu açıklanmaktadır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Genel Bakış videoları

Aşağıdaki videoda kullanım koşullarına hızlı bir genel bakış sunulmaktadır.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Videolar için bkz:
- [Azure Active Directory kullanım koşullarını dağıtma](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory kullanım koşullarını alma](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Kullanım koşulları ile ne yapabilirim?

Azure AD kullanım koşulları aşağıdaki yeteneklere sahiptir:

- Çalışanların veya konukların erişim koşullarına girmeden önce kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların veya konukların erişim almadan önce her cihazda kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların veya konukların yinelenen bir zamanlamaya göre kullanım koşullarınızı kabul etmesini gerektir.
- Azure Multi-Factor Authentication 'de (MFA) güvenlik bilgilerini kaydetmeden önce çalışanların veya konukların kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların Azure AD self servis parola sıfırlama (SSPR) içinde güvenlik bilgilerini kaydetmeden önce kullanım koşullarınızı kabul etmesini gerektir.
- Kuruluşunuzdaki tüm kullanıcılar için genel kullanım koşullarını sunun.
- Kullanıcı özniteliklerine (örn.) göre belirli kullanım koşulları sunun. doktorlarla hemşirelere ya da yurtiçi ve uluslararası çalışanlara, [dinamik grupları](../users-groups-roles/groups-dynamic-membership.md) kullanarak) sunun.
- Salesforce gibi yüksek iş etkisi uygulamalarına erişirken belirli kullanım koşullarını sunun.
- Farklı dillerdeki kullanım koşullarını sunun.
- Kullanım koşullarınızı kimlerin sahip olduğunu veya kabul etmemiş listesini listeleyin.
- Gizlilik düzenlemeleriyle Karşılama konusunda yardımcı olur.
- Uyumluluk ve denetim için kullanım koşulları etkinlik günlüğünü görüntüleyin.
- [Microsoft Graph API 'leri](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) kullanarak kullanım koşulları oluşturun ve yönetin (Şu anda önizleme aşamasındadır).

## <a name="prerequisites"></a>Önkoşullar

Azure AD kullanım koşulları 'nı kullanmak ve yapılandırmak için şunları yapmanız gerekir:

- Azure AD Premium P1, P2, EMS E3 veya EMS E5 aboneliği.
   - Bu aboneliklerden birine sahip değilseniz [Azure AD Premium'u alabilir](../fundamentals/active-directory-get-started-premium.md) veya [Azure AD Premium deneme sürümünü etkinleştirebilirsiniz](https://azure.microsoft.com/trial/get-started-active-directory/).
- Yapılandırmak istediğiniz dizin için aşağıdaki yönetici hesaplarından biri:
   - Genel Yönetici
   - Güvenlik Yöneticisi
   - Koşullu Erişim Yöneticisi

## <a name="terms-of-use-document"></a>Kullanım koşulları belgesi

Azure AD kullanım koşulları, içerik sunmak için PDF biçimini kullanır. Bu PDF dosyası, kullanıcıların oturum açtığı sırada son kullanıcı sözleşmelerini toplamanıza olanak sağlayan herhangi bir içerik (örneğin mevcut sözleşme belgeleri) olabilir. Kullanıcıların mobil aygıtları desteklemek için PDF olarak önerilen yazı tipi boyutu 24 noktasıdır.

## <a name="add-terms-of-use"></a>Kullanım koşulları ekleme

Kullanım koşullarınızı bir kez daha doldurduktan sonra eklemek için aşağıdaki yordamı kullanın.

1. Azure'da oturum aç bir genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi.
1. **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.

   ![Koşullu erişim-Kullanım koşulları dikey pencere](./media/terms-of-use/tou-blade.png)

1. **Yeni koşullar**'a tıklayın.

   ![Kullanım koşullarınızın ayarlarını belirtmek için yeni kullanım şartı bölmesi](./media/terms-of-use/new-tou.png)

1. **Ad** kutusuna, Azure Portal kullanılacak kullanım koşulları için bir ad girin.
1. İçinde **görünen ad** kutusunda, oturum kullanıcıların gördüğü başlık girin.
1. **Kullanım koşulları belge**için, son kullanılan kullanım koşullarınıza gidin ve bu PDF 'yi seçin.
1. Kullanım koşullarınızın belgesi için dili seçin. Dil seçeneğini kullanarak her biri farklı dilde olan birden fazla kullanım koşulunu karşıya yükleyebilirsiniz. Bir son kullanıcının göreceği kullanım koşulları sürümü, kullanıcının tarayıcı tercihlerine bağlıdır.
1. Son kullanıcıların, kabul etmeden önce kullanım koşullarını görüntülemesini gerektirmek için **kullanıcıların kullanım koşullarını genişletmesini iste** **seçeneğini belirleyin.**
1. Son kullanıcıların, eriştiği her cihazda kullanım koşullarınızı kabul etmesini gerektirmek için, **kullanıcıların her cihazda** **üzerinde**onay vermesini iste seçeneğini belirleyin. Daha fazla bilgi için bkz. [cihaz başına kullanım koşulları](#per-device-terms-of-use).
1. Kullanım koşullarını bir zamanlamaya göre sona erdirmek istiyorsanız, kullanım **süreleri sona erme** süresini **Açık**olarak ayarlayın. Üzerinde ayarlandığında, iki ek zamanlama ayarları görüntülenir.

   ![Başlangıç tarihi, sıklığı ve süreyi ayarlamak için onayları ayarlarının süresi doluyor](./media/terms-of-use/expire-consents.png)

1. Kullanım son tarihleri için zamanlamayı belirtmek üzere başlangıç ve **Sıklık** ayarlarını **sona erdir** ' i kullanın. Aşağıdaki tabloda birkaç örnek ayarlara yönelik sonuçları gösterir:

   | Süre sonu başlangıcı: | Sıklık | Sonuç |
   | --- | --- | --- |
   | Bugünün tarihi  | Aylık | Bugünden itibaren, kullanıcılar kullanım koşullarını kabul etmeli ve sonra her ay yeniden kabul etmelidir. |
   | Tarih gelecekte  | Aylık | Bugünden itibaren, kullanıcıların kullanım koşullarını kabul etmesi gerekir. Gelecekteki bir tarihi gerçekleştiğinde onayları sona erer ve kullanıcılar her ay sonra artırmasını gerekir.  |

   Örneğin, sona erme tarihi başlangıç ayarlarsanız **Oca 1** ve sıklığını **aylık**, işte süresinin sona ermesinin iki kullanıcı için nasıl oluşabilir:

   | Kullanıcı | İlk tarih kabul edin | Önce sona eren tarihi | İkinci sona tarihi | Üçüncü sona tarihi |
   | --- | --- | --- | --- | --- |
   | Alice | Ocak 1 | 1 Şubat ' | 1 Mart'ta | Apr 1 |
   | Bob | Dönemlerinizin 15 Ocak | 1 Şubat ' | 1 Mart'ta | Apr 1 |

1. Kullanıcı kullanım koşullarını yeniden kabul etmeden önce geçmesi gereken gün sayısını belirtmek için **yeniden kabul gerektiren süre (gün)** ayarını kullanın. Bu, kullanıcıların kendi zamanlamalarında izleyin olanak tanır. Örneğin, süresi ayarlayın **30** gün, işte süresinin sona ermesinin iki kullanıcı için nasıl oluşabilir:

   | Kullanıcı | İlk tarih kabul edin | Önce sona eren tarihi | İkinci sona tarihi | Üçüncü sona tarihi |
   | --- | --- | --- | --- | --- |
   | Alice | Ocak 1 | 31 Ocak'a kadar | Mar 2 | Apr 1 |
   | Bob | Dönemlerinizin 15 Ocak | 14 Şubat | 16 Mart | Apr 15 |

   Kullanmak mümkün mü **sona onayları** ve **süresi (gün) yeniden kabulü gerektiren önce** ayarları birlikte, ancak genellikle birini kullanın.

1. **Koşullu erişim**altında, Kullanım Koşullarını zorlayacağı şablonu seçmek Için **koşullu erişim ilkesi şablonunu Uygula** listesini kullanın.

   ![İlke şablonu seçmek için koşullu erişim açılan listesi](./media/terms-of-use/conditional-access-templates.png)

   | Şablon | Açıklama |
   | --- | --- |
   | **Tüm konuklar için bulut uygulamalarına erişim** | Tüm konuklar ve tüm bulut uygulamaları için bir koşullu erişim ilkesi oluşturulacaktır. Bu ilke, Azure portalını etkiler. Bu oluşturulduktan sonra oturum kapatma ve oturum açma için gerekli olabilir. |
   | **Bulut uygulamalarında tüm kullanıcılar için erişim** | Tüm kullanıcılar ve tüm bulut uygulamaları için bir koşullu erişim ilkesi oluşturulacaktır. Bu ilke, Azure portalını etkiler. Bu oluşturulduktan sonra oturum kapatma ve oturum açma için gerekli olacaktır. |
   | **Özel ilke** | Bu kullanım koşullarının uygulanacağı kullanıcıları, grupları ve uygulamaları seçin. |
   | **Koşullu erişim ilkesini daha sonra Oluştur** | Bu kullanım koşulları, koşullu erişim ilkesi oluştururken izin verme denetim listesinde görünür. |

   >[!IMPORTANT]
   >Koşullu Erişim İlkesi denetimleri (kullanım koşulları dahil) hizmet hesaplarında zorlamayı desteklemez. Tüm hizmet hesaplarının koşullu erişim ilkesinden dışlanmasını öneririz.

    Özel koşullu erişim ilkeleri, belirli bir bulut uygulamasına veya kullanıcı grubuna göre ayrıntılı kullanım koşullarını sağlar. Daha fazla bilgi için bkz [. hızlı başlangıç: Bulut uygulamalarına](require-tou.md)erişmeden önce kabul edilmesi gereken kullanım koşullarını gerektir.

1. **Oluştur**'a tıklayın.

   Özel bir koşullu erişim şablonu seçtiyseniz, özel koşullu erişim ilkesi oluşturmanıza olanak sağlayan yeni bir ekran görüntülenir.

   ![Özel koşullu erişim ilkesi şablonunu seçtiyseniz yeni koşullu erişim bölmesi](./media/terms-of-use/custom-policy.png)

   Şimdi yeni kullanım koşullarınızı görmeniz gerekir.

   ![Kullanım koşulları dikey penceresinde listelenen yeni kullanım koşulları](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Kimin kabul ve reddedilen, raporu görüntüle

Kullanım Koşulları dikey penceresinin kabul eden ve reddeden kullanıcı sayısını gösterdiğini fark edeceksiniz. Bu sayımlar ve kabul edilen/reddedilen, kullanım koşullarının ömrü boyunca depolanır.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.

   ![Kullanıcı sayısını gösteren Kullanım koşulları dikey pencere sayısı kabul edildi ve reddedildi](./media/terms-of-use/view-tou.png)

1. Kullanım koşulları için, kullanıcıların geçerli durumunu görüntülemek için **kabul edilen** veya **Reddedilen** sayılara tıklayın.

   ![Kabul etmiş olan kullanıcıları listelemek kullanım koşulları onayları bölmesi](./media/terms-of-use/accepted-tou.png)

1. Bireysel kullanıcı geçmişini görüntülemek için üç nokta simgesine tıklayın ( **...** ) ve ardından **geçmişi görüntüleyebilir**.

   ![Kullanıcı için geçmişi görüntüle bağlam menüsü](./media/terms-of-use/view-history-menu.png)

   Tüm geçmiş görünümü geçmişi bölmesinde gördüğünüz kabul eder, reddeder ve süre sonu.

   ![Geçmiş bölmesini görüntüle bir kullanıcı için geçmişi kabul eden, reddettiğinde ve süre sonlarını listeler](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Görünüm Azure AD denetim günlükleri

Ek etkinlikleri görüntülemek istiyorsanız, Azure AD kullanım koşulları denetim günlüklerini içerir. Her kullanıcı onayı tetikleyen bir olay için depolanan denetim günlüklerinde **30 gün**. Bu günlükleri portalda görüntüleyebilir veya .csv dosyası olarak indirebilirsiniz.

Azure AD ile kullanmaya başlamak için Denetim günlükleri, aşağıdaki yordamı kullanın:

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Kullanım koşullarını seçin.
1. **Denetim günlüklerini görüntüle**'ye tıklayın.

   ![Denetim günlüklerini görüntüle seçeneğinin vurgulandığı dikey pencere Kullanım koşulları](./media/terms-of-use/audit-tou.png)

1. Azure AD denetim günlükleri ekranında, sağlanan hedef belirli denetim günlüğü bilgilerini listelerine kullanarak bilgileri filtreleyebilirsiniz.

   Ayrıca **İndir**'e tıklayarak bilgileri yerel olarak kullanmak üzere bir .csv dosyasında indirebilirsiniz.

   ![Azure AD denetim günlükleri ekran listeleme tarihi, hedef ilkesi, tarafından başlatılan ve etkinlik](./media/terms-of-use/audit-logs-tou.png)

   Bir günlük tıklarsanız, bir bölmesi ile ek Etkinlik ayrıntıları görüntülenir.

   ![Etkinlik, etkinlik durumu, tarafından başlatılan hedef ilke gösteren günlük için etkinlik ayrıntıları](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Kullanıcılar için hangi kullanım koşulları gibi görünüyor

Kullanım koşulları oluşturulduktan ve zorlandıktan sonra, Kullanıcı oturum açma işlemi sırasında aşağıdaki ekranı görür.

![Kullanıcı oturum açtığında görüntülenen örnek kullanım koşulları](./media/terms-of-use/user-tou.png)

Kullanıcılar kullanım koşullarını görüntüleyebilir ve gerekirse, yakınlaştırmak ve uzaklaştırmak için düğmeleri kullanın.

![Yakınlaştırma düğmeleriyle kullanım koşullarını görüntüleme](./media/terms-of-use/zoom-buttons.png)

Aşağıdaki ekranda, kullanım koşullarının mobil cihazlarda nasıl göründüğü gösterilmektedir.

![Kullanıcı bir mobil cihazda oturum açtığında görüntülenen örnek kullanım koşulları](./media/terms-of-use/mobile-tou.png)

Kullanıcılar yalnızca bir kez kullanım koşullarını kabul etmek için gereklidir ve sonraki oturum açma işlemleri üzerinde yeniden kullanım koşulları 'nı görmez.

### <a name="how-users-can-review-their-terms-of-use"></a>Kullanıcıların kullanım koşullarını nasıl gözden geçirebilmeleri

Kullanıcılar, aşağıdaki yordamı kullanarak kabul ettikleri kullanım koşullarını gözden geçirebilir ve görebilirler.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) adresinde oturum açın.
1. Sağ üst köşede adınıza tıklayın ve seçin **profili**.

   ![Kullanıcının bölmesi açık olan Uygps sitesi](./media/terms-of-use/tou14.png)

1. Profil sayfanızda **Kullanım koşullarını gözden geçir**'e tıklayın.

   ![Kullanım koşullarını gözden geçirme bağlantısını gösteren bir kullanıcının profil sayfası](./media/terms-of-use/tou13a.png)

1. Buradan, kabul ettiğiniz kullanım koşullarını gözden geçirebilirsiniz.

## <a name="edit-terms-of-use-details"></a>Kullanım koşulları ayrıntılarını düzenleyin

Kullanım koşulları 'nın bazı ayrıntılarını düzenleyebilir, ancak var olan bir belgeyi değiştiremezsiniz. Aşağıdaki yordam ayrıntılarının nasıl düzenleneceğini açıklar.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Düzenlemek istediğiniz kullanım koşullarını seçin.
1. Tıklayın **koşulları Düzenle**.
1. Kullanım bölmesi düzenleme koşullarını adı, görünen ad veya kullanıcıların değerleri genişletmesini gerekli kıl.

   PDF belgesi gibi değiştirmek istediğiniz başka ayarlar varsa, kullanıcıların her cihazda onay vermesini, consents 'nin süresini, yeniden kabul etmeden önce süreyi veya koşullu erişim ilkesini, yeni bir kullanım koşulları oluşturmanız gerekir.

   ![Ad ve genişletme seçeneklerini gösteren kullanım koşulları bölmesini Düzenle](./media/terms-of-use/edit-tou.png)

1. Tıklayın **Kaydet** yaptığınız değişiklikleri kaydedin.

   Değişikliklerinizi kaydettikten sonra, kullanıcılar bu düzenlemeleri yeniden kabul etmek zorunda olmayacaktır.

## <a name="add-a-terms-of-use-language"></a>Kullanım koşulları dili ekleme

Aşağıdaki yordamda bir kullanım koşulları dilinin nasıl ekleneceği açıklanmaktadır.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Düzenlemek istediğiniz kullanım koşullarını seçin.
1. Ayrıntılar bölmesinden **dilleri** sekmesi.

   ![Kullanım koşulları seçili ve Ayrıntılar bölmesindeki diller sekmesi gösteriliyor](./media/terms-of-use/languages-tou.png)

1. Tıklayın **dil Ekle**.
1. Kullanım dil bölmesi ekleme koşullarını yerelleştirilmiş PDF'niz karşıya yükleme ve dil seçin.

   ![Yerelleştirilmiş PDF 'Leri karşıya yükleme seçenekleri ile dil bölmesi kullanım koşulları ekleme](./media/terms-of-use/language-add-tou.png)

1. Tıklayın **Ekle** dil eklemek için.

## <a name="per-device-terms-of-use"></a>Cihaz başına kullanım koşulları

**Her cihaz için kullanıcıların onay vermesini iste** ayarı, son kullanıcıların, eriştiği her cihazda kullanım koşullarınızı kabul etmesini zorunlu kılmanıza olanak sağlar. Son kullanıcı cihazlarını Azure AD'ye katılmak için gerekli olacaktır. Cihaz katıldığında, her cihazda kullanım koşullarını zorlamak için cihaz KIMLIĞI kullanılır.

Yazılım ve desteklenen platformlar listesi aşağıda verilmiştir.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Diğer |
> | --- | --- | --- | --- | --- |
> | **Yerel uygulama** | Evet | Evet | Evet |  |
> | **Microsoft Edge** | Evet | Evet | Evet |  |
> | **Internet Explorer** | Evet | Evet | Evet |  |
> | **Chrome (uzantısı ile)** | Evet | Evet | Evet |  |

Cihaz başına kullanım koşulları aşağıdaki kısıtlamalara sahiptir:

- Bir cihaz, yalnızca tek bir kiracı katılabilir.
- Bir kullanıcının kendi cihazı alanına katma izinleri olması gerekir.
- Intune kayıt uygulaması desteklenmez.
- Azure AD B2B kullanıcıları desteklenmez.

Kullanıcının cihazı alanına katılmamışsa, bunların cihazlarını katılmak için ihtiyaç duydukları bir ileti alırsınız. Deneyimlerini platform ve yazılım bağımlı olacaktır.

### <a name="join-a-windows-10-device"></a>Windows 10 cihazını ekleme

Bir kullanıcı Windows 10 ve Microsoft Edge kullanıyorsanız, aşağıdakine benzer bir ileti alırsınız [cihazını katılın](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 ve Microsoft Edge-cihazınızın kayıtlı olması gerektiğini belirten Ileti](./media/terms-of-use/per-device-win10-edge.png)

Chrome kullanıyorsanız, yüklemeniz istenir [Windows 10 hesapları uzantısı](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Tarayıcılar

Bir kullanıcı desteklenmeyen bir tarayıcı kullanıyorsanız, farklı bir tarayıcı kullanın istenir.

![Cihazınızın kayıtlı olması gerektiğini belirten ileti, ancak tarayıcı desteklenmez](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Kullanım koşullarını Sil

Aşağıdaki yordamı kullanarak eski kullanım koşullarını silebilirsiniz.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Kaldırmak istediğiniz kullanım koşullarını seçin.
1. **Koşulları sil**'e tıklayın.
1. Devam etmek isteyip istemediğinizi soran iletide **Evet**'e tıklayın.

   ![Kullanım koşullarını silme onayı isteyen ileti](./media/terms-of-use/delete-tou.png)

   Artık kullanım koşullarınızı görmemelisiniz.

## <a name="deleted-users-and-active-terms-of-use"></a>Silinen kullanıcılar ve etkin kullanım koşulları

Varsayılan olarak, silinmiş bir kullanıcı Azure AD'de 30 gün boyunca silinmiş durumda kalır ve bu süre boyunca gerekirse bir yönetici tarafından geri alınabilir. 30 gün sonra bu kullanıcı kalıcı olarak silinir. Ayrıca, bir Genel Yönetici bu süreye ulaşılmadan önce Azure Active Directory portalını kullanarak [kısa süre önce silinmiş bir kullanıcıyı kalıcı olarak silebilir](../fundamentals/active-directory-users-restore.md). Bir Kullanıcı kalıcı olarak silindi, bu kullanıcı hakkındaki sonraki veriler etkin kullanım koşullarından kaldırılacaktır. Silinmiş kullanıcılara ilişkin denetim bilgileri, denetim günlüğünde kalır.

## <a name="policy-changes"></a>İlke değişiklikleri

Koşullu erişim ilkeleri hemen etkili olur. Bu durumda, yönetici "Üzgün Bulutlar" veya "Azure AD belirteç sorunları" görmek başlatılır. Yönetici, oturumu kapatın ve yeni ilkeyi karşılamak için yeniden oturum açın.

> [!IMPORTANT]
> Aşağıdaki durumlarda kapsam dahilindeki kullanıcıların yeni bir ilkeyi karşılamak için oturumu kapatıp yeniden oturum açmaları gerekir:
>
> - Koşullu erişim ilkesi kullanım koşulları üzerinde etkinleştirilir
> - veya ikinci bir kullanım koşulları belgesi oluşturulduğunda

## <a name="b2b-guests-preview"></a>B2B Konukları (Önizleme)

Çoğu kuruluşta, çalışanları kuruluşun kullanım koşulları ve gizlilik bildirimleri onayı için bir işlem yapılır. Ancak nasıl zorunlu aynı bir onayları için Azure AD iş işletmeden işletmeye (B2B) konukların, SharePoint veya Teams eklenen? Koşullu erişimi ve kullanım koşullarını kullanarak, bir ilkeyi doğrudan B2B Konuk kullanıcıları doğrultusunda zorlayabilirsiniz. Davet teminat akışı sırasında, kullanıcıya kullanım koşulları sunulur. Bu destek, şu anda Önizleme aşamasındadır.

Kullanım koşulları, yalnızca kullanıcının Azure AD 'de bir Konuk hesabı olduğunda görüntülenir. SharePoint Online Şu anda bir belge veya kullanıcının bir Konuk hesabına sahip olmasını gerektirmeyen bir klasörü paylaşmak için geçici bir [dış paylaşım alıcısı deneyimine](/sharepoint/what-s-new-in-sharing-in-targeted-release) sahiptir. Bu durumda, kullanım koşulları görüntülenmez.

![Kullanıcılar ve gruplar bölmesi-tüm konuk kullanıcılar seçeneği işaretli sekmesini dahil et](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Bulut uygulamaları (Önizleme) desteği

Kullanım koşulları, Azure Information Protection ve Microsoft Intune gibi farklı bulut uygulamaları için kullanılabilir. Bu destek, şu anda Önizleme aşamasındadır.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection uygulaması için bir koşullu erişim ilkesi yapılandırabilir ve Kullanıcı korumalı bir belgeye eriştiğinde kullanım koşulları isteyebilirsiniz. Bu, bir kullanıcının korumalı bir belgeye ilk kez erişmeden önce kullanım koşullarını tetikler.

![Microsoft Azure Information Protection App ile bulut uygulamaları bölmesi seçildi](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune kaydı

Microsoft Intune kayıt uygulaması için bir koşullu erişim ilkesi yapılandırabilir ve Intune 'da cihaz kaydı öncesinde bir kullanım koşulları isteyebilirsiniz. Daha fazla bilgi için bkz: Okuma [koşulları, kuruluş blog gönderisi için çözüm seçme hakkını](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Microsoft Intune App ile bulut uygulamaları bölmesi seçildi](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune kayıt uygulaması, [cihaz başına kullanım koşulları](#per-device-terms-of-use)için desteklenmez.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: Nasıl yaparım?, bir kullanıcının kullanım koşullarını ne zaman/ne zaman kabul ettiğini öğrenmek mi istiyorsunuz?**<br />
Y: Kullanım koşulları dikey penceresinde **kabul edilen**sayıya tıklayın. Ayrıca görüntüleyebilir veya Azure AD'de arama kabul etkinliğine denetim günlükleri. Daha fazla bilgi için bkz. kimlerin kabul edildiğini ve reddettiğini görüntüleyen rapor görüntüleme ve [Azure AD denetim günlüklerini görüntüleme](#view-azure-ad-audit-logs).

**S: Bilgiler ne kadar süreyle depolanıyor?**<br />
Y: Kullanıcı kullanım koşulları raporunda ve kabul edilen/reddedilmiş olan kullanım koşullarının ömrü boyunca depolanır. Azure AD denetim günlükleri, 30 gün boyunca saklanır.

**S: Kullanım koşullarında neden farklı sayıda onay görüyorum? Azure AD denetim günlükleri?**<br />
Y: Kullanım koşulları raporu, bu kullanım koşullarının kullanım ömrü boyunca depolanır, ancak Azure AD denetim günlükleri 30 gün boyunca depolanır. Ayrıca, kullanım koşulları raporu yalnızca kullanıcının geçerli onay durumunu görüntüler. Örneğin, bir Kullanıcı reddederse ve kabul ediyorsa, kullanım koşulları raporu yalnızca kullanıcının kabul ettiği süreyi gösterir. Geçmişini görmek gerekirse, Azure AD kullanabilirsiniz. Denetim günlükleri.

**S: Kullanım koşulları 'nın ayrıntılarını düzenleyediğimde, kullanıcıların yeniden kabul etmesini ister misiniz?**<br />
Y: Hayır, bir yönetici kullanım koşullarının ayrıntılarını (ad, görünen ad, kullanıcıların genişletmesini veya bir dil eklemesini iste) düzenlerse, kullanıcıların yeni koşulları yeniden kabul etmesini gerektirmez.

**S: Mevcut bir kullanım koşulları belgesi güncelleştirebilir miyim?**<br />
Y: Şu anda mevcut bir kullanım koşulları belgesini güncelleştiremezsiniz. Kullanım koşullarını belge olarak değiştirmek için yeni bir kullanım koşulları örneği oluşturmanız gerekir.

**S: Köprüler PDF belgesi kullanım koşullarınızda yer alıyorsa, son kullanıcılar bu kullanıcılara tıklayabilir mi?**<br />
Y: Evet, son kullanıcılar ek sayfalara köprüler seçebiliyor, ancak belge içindeki bölümlerin bağlantıları desteklenmiyor.

**S: Kullanım koşulları birden çok dili destekleyebilir mi?**<br />
Y: Evet. Şu anda bir yöneticinin tek bir kullanım koşulları için yapılandırabilirler 108 farklı dil vardır. Bir yönetici birden çok PDF belgesini karşıya yükleyebilir ve ilgili bir dille (108 kadar) bu belgeleri etiketleyebilir. Son kullanıcılar oturum açtığında, tarayıcı dili tercihini inceliyoruz ve eşleşen belgeyi görüntüyoruz. Eşleşme yoksa, karşıya yüklenen ilk belge olan varsayılan belgeyi görüntüleriz.

**S: Kullanım koşulları ne zaman tetiklenir?**<br />
Y: Kullanım koşulları, oturum açma deneyimi sırasında tetiklenir.

**S: Kullanım koşullarını hangi uygulamalara hedefleyebilirsiniz?**<br />
Y: Modern kimlik doğrulaması kullanarak kurumsal uygulamalarda koşullu erişim ilkesi oluşturabilirsiniz. Daha fazla bilgi için bkz. [Kurumsal uygulamalar](./../manage-apps/view-applications-portal.md).

**S: Belirli bir kullanıcı veya uygulamaya birden çok kullanım koşulları ekleyebilir miyim?**<br />
Y: Evet, bu grupları veya uygulamaları hedefleyen birden fazla koşullu erişim ilkesi oluşturarak. Bir Kullanıcı birden çok kullanım koşulları kapsamında kalırsa, tek seferde bir kullanım koşulları kabul eder.

**S: Kullanıcı kullanım koşullarını reddederse ne olur?**<br />
Y: Kullanıcının uygulamaya erişimi engellenir. Kullanıcı yeniden oturum açın ve erişmek için koşulları kabul etmesi gerekir.

**S: Daha önce kabul edilen kullanım koşullarını kabul etmek mümkün midir?**<br />
Y: [Önceden kabul edilen kullanım koşullarını gözden](#how-users-can-review-their-terms-of-use)geçirebilirsiniz, ancak şu anda kabul etmenin bir yolu yoktur.

**S: Intune hüküm ve koşullarını de kullanıyorsam ne olur?**<br />
Y: Hem Azure AD kullanım koşulları 'nı hem de [Intune hüküm ve koşullarını](/intune/terms-and-conditions-create)yapılandırdıysanız, kullanıcının her ikisini de kabul etmesi gerekecektir. Daha fazla bilgi için [koşulları, kuruluş blog gönderisi için çözüm seçme hakkını](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**S: Kullanım koşulları hizmeti kimlik doğrulaması için hangi uç noktalar kullanır?**<br />
Y: Kullanım koşulları, kimlik doğrulaması için aşağıdaki uç noktalara https://tokenprovider.termsofuse.identitygovernance.azure.com yararlanır https://account.activedirectory.windowsazure.com: ve. Kuruluşunuzun kayıt için bir izin verilenler listesi varsa, bu uç noktaları, oturum açma için Azure AD uç noktaları ile birlikte izin verilenler listenize eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı Başlangıç: Bulut uygulamalarına erişmeden önce kabul edilmesi gereken kullanım koşullarını gerektir](require-tou.md)
- [Azure Active Directory 'de koşullu erişim için en iyi yöntemler](best-practices.md)
