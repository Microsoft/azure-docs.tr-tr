---
title: Kullanım koşulları-Azure Active Directory | Microsoft Docs
description: Erişim almadan önce çalışanlara veya konuklara bilgi sunmak için kullanım koşulları Azure Active Directory kullanmaya başlayın.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530252"
---
# <a name="azure-active-directory-terms-of-use"></a>Kullanım koşulları Azure Active Directory

Azure AD kullanım koşulları ilkeleri, kuruluşların son kullanıcılara bilgi sunmak için kullanabileceği basit bir yöntem sağlar. Bu sunum, kullanıcıların yasal gereksinimler veya uyumluluk gereksinimleriyle ilgili bildirimleri görmesi sağlar. Bu makalede, kullanım koşulları (ToU) ilkeleri ile çalışmaya başlama işlemi açıklanır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Genel Bakış videoları

Aşağıdaki videoda, ToU ilkelerine hızlı bir genel bakış sunulmaktadır.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Daha fazla video için bkz.:
- [Azure Active Directory bir kullanım koşulları İlkesi dağıtma](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory bir kullanım koşulları ilkesini kullanıma alma](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Kullanım koşulları ile ne yapabilirim?

Azure AD kullanım koşulları ilkeleri aşağıdaki yeteneklere sahiptir:

- Çalışanların veya konukların erişim almadan önce kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların veya konukların erişim almadan önce her cihazda kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların veya konukların kullanım koşullarınızı yinelenen bir zamanlamaya göre kabul etmesini gerektir.
- Azure AD Multi-Factor Authentication (MFA) ' de güvenlik bilgilerini kaydetmeden önce çalışanların veya konukların kullanım koşullarınızı kabul etmesini gerektir.
- Çalışanların Azure AD self servis parola sıfırlama (SSPR) içinde güvenlik bilgilerini kaydetmeden önce kullanım koşullarınızı kabul etmesini gerektir.
- Kuruluşunuzdaki tüm kullanıcılar için genel kullanım koşulları ilkesi sunun.
- Kullanıcı özniteliklerine (örn.) göre belirli kullanım koşulları kuralları sunma. doktorlarla hemşirelere ya da yurtiçi ve uluslararası çalışanlara, [dinamik grupları](../enterprise-users/groups-dynamic-membership.md) kullanarak) sunun.
- Salesforce gibi yüksek iş etkisi uygulamalarına erişirken belirli kullanım koşulları ilkelerini sunun.
- Farklı dillerdeki kullanım koşulları ilkelerini sunun.
- Kullanım koşullarınıza sahip olan veya kabul edilmeyen liste.
- Toplantı gizlilik düzenlemelerine yardımcı olun.
- Uyumluluk ve denetim için kullanım koşulları ilke etkinliği günlüğünü görüntüleyin.
- [Microsoft Graph API 'leri](/graph/api/resources/agreement) kullanarak kullanım koşulları ilkeleri oluşturun ve yönetin (Şu anda önizleme aşamasındadır).

## <a name="prerequisites"></a>Önkoşullar

Azure AD kullanım koşulları ilkelerini kullanmak ve yapılandırmak için, şunları yapmanız gerekir:

- Azure AD Premium P1, P2, EMS E3 veya EMS E5 aboneliği.
   - Bu aboneliklerden birine sahip değilseniz [Azure AD Premium'u alabilir](../fundamentals/active-directory-get-started-premium.md) veya [Azure AD Premium deneme sürümünü etkinleştirebilirsiniz](https://azure.microsoft.com/trial/get-started-active-directory/).
- Yapılandırmak istediğiniz dizin için aşağıdaki yönetici hesaplarından biri:
   - Genel Yönetici
   - Güvenlik Yöneticisi
   - Koşullu Erişim Yöneticisi

## <a name="terms-of-use-document"></a>Kullanım koşulları belgesi

Azure AD kullanım koşulları ilkeleri, içerik sunmak için PDF biçimini kullanır. Bu PDF dosyası, kullanıcıların oturum açtığı sırada son kullanıcı sözleşmelerini toplamanıza olanak sağlayan herhangi bir içerik (örneğin mevcut sözleşme belgeleri) olabilir. Mobil cihazlardaki kullanıcıları desteklemek için, PDF 'de önerilen yazı tipi boyutu 24 noktasıdır.

## <a name="add-terms-of-use"></a>Kullanım koşulları ekleme

Kullanım koşullarınızı ilke belgesi ile sonuçladıktan sonra, eklemek için aşağıdaki yordamı kullanın.

1. Azure 'da genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.

    ![Koşullu erişim-Kullanım koşulları dikey pencere](./media/terms-of-use/tou-blade.png)

1. **Yeni koşullar**'a tıklayın.

    ![Kullanım koşullarınızın ayarlarını belirtmek için yeni kullanım şartı bölmesi](./media/terms-of-use/new-tou.png)

1. **Ad** kutusuna, Azure Portal kullanılacak kullanım koşulları ilkesi için bir ad girin.
1. **Görünen ad** kutusuna kullanıcıların oturum açtıklarında göreceği bir başlık girin.
1. **Kullanım koşulları belge** için, son kullanılan kullanım koşulları ilke PDF 'nize gidin ve bunu seçin.
1. Kullanım koşullarınızın ilke belgesi için dili seçin. Dil seçeneği, her biri farklı bir dile sahip birden çok kullanım koşulları 'nı karşıya yüklemenize olanak sağlar. Son kullanıcının göreceği kullanım koşulları İlkesi sürümü, tarayıcı tercihlerine göre yapılır.
1. Son kullanıcıların, kabul etmeden önce kullanım koşullarını görüntülemesini gerektirmek için **kullanıcıların kullanım koşullarını genişletmesini iste** **seçeneğini belirleyin.**
1. Son kullanıcıların, eriştiği her cihazda kullanım koşullarınızı kabul etmesini gerektirmek için, **kullanıcıların her cihazda** **üzerinde** onay vermesini iste seçeneğini belirleyin. Bu seçenek etkinleştirilirse kullanıcıların ek uygulamalar yüklemesi gerekebilir. Daha fazla bilgi için bkz. [cihaz başına kullanım koşulları](#per-device-terms-of-use).
1. Kullanım koşulları ilke onayları bir zamanlamaya göre sona ermek istiyorsanız, **süre sonu onayları** 'yi **Açık** olarak ayarlayın. Açık olarak ayarlandığında, iki ek zamanlama ayarı görüntülenir.

    ![Başlangıç tarihi, sıklığı ve süreyi ayarlamak için onayları ayarlarının süresi doluyor](./media/terms-of-use/expire-consents.png)

1. Kullanım süresi sonu süre sonu ayarlarını belirtmek için **Başlangıç tarihi** ve **sıklığı** ayarlarını kullanın. Aşağıdaki tabloda, birkaç örnek ayar için sonuç gösterilmektedir:

   | Başlangıç tarihi | Sıklık | Sonuç |
   | --- | --- | --- |
   | Bugünün tarihi  | Aylık | Bugünden itibaren, kullanıcıların kullanım koşullarını kabul etmesi ve her ay yeniden kabul etmesi gerekir. |
   | Gelecekteki Tarih  | Aylık | Bugünden itibaren, kullanıcılar kullanım koşulları ilkesini kabul etmelidir. Gelecek tarih gerçekleştiğinde, yarışma süreleri sona erer ve kullanıcılar her ay yeniden kabul etmelidir.  |

   Örneğin, tarihinden itibaren süre sonu ' nu **1 Ocak** ve sıklık olarak **ayda** bir olarak ayarlarsanız, iki kullanıcı için süre sonu oluşma sıklığı aşağıda verilmiştir:

   | Kullanıcı | İlk kabul tarihi | İlk sona erme tarihi | İkinci sona erme tarihi | Üçüncü sona erme tarihi |
   | --- | --- | --- | --- | --- |
   | 'Ndaki | 1 Oca | 1 Şub | 1. Mar | 1 Nis |
   | Bob | 15 Oca | 1 Şub | 1. Mar | 1 Nis |

1. Kullanıcı kullanım koşulları ilkesini yeniden kabul etmeden önce geçmesi gereken gün sayısını belirtmek için **yeniden kabul gerektiren süre (gün)** ayarını kullanın. Bu, kullanıcıların kendi zamanlamalarını izlemesini sağlar. Örneğin, süreyi **30** gün olarak ayarlarsanız, iki kullanıcı için süre sonu oluşma sıklığı aşağıda verilmiştir:

   | Kullanıcı | İlk kabul tarihi | İlk sona erme tarihi | İkinci sona erme tarihi | Üçüncü sona erme tarihi |
   | --- | --- | --- | --- | --- |
   | 'Ndaki | 1 Oca | 31 Oca | 2. Mar | 1 Nis |
   | Bob | 15 Oca | 14 Şub | 16 Mar | 15 Nis |

   **Yeniden kabul etmeden önce** , **zaman süresi** ve süre sonu (gün) ayarlarını kullanmak mümkündür, ancak genellikle bir veya diğerini kullanırsınız.

1. **Koşullu erişim** altında, kullanım koşulları ilkesini zorlamak için şablonu seçmek üzere **koşullu erişim ilkesi şablonunu Uygula** listesini kullanın.

    ![İlke şablonu seçmek için koşullu erişim açılan listesi](./media/terms-of-use/conditional-access-templates.png)

   | Şablon | Açıklama |
   | --- | --- |
   | **Tüm konuklar için bulut uygulamalarına erişim** | Tüm konuklar ve tüm bulut uygulamaları için bir koşullu erişim ilkesi oluşturulacaktır. Bu ilke Azure portal etkiler. Bu oluşturulduktan sonra oturumunuzu kapatıp açmanız gerekebilir. |
   | **Tüm kullanıcılar için bulut uygulamalarına erişim** | Tüm kullanıcılar ve tüm bulut uygulamaları için bir koşullu erişim ilkesi oluşturulacaktır. Bu ilke Azure portal etkiler. Bu oluşturulduktan sonra oturumunuzu kapatıp açmanız gerekecektir. |
   | **Özel ilke** | Bu kullanım koşulları ilkesinin uygulanacağı kullanıcıları, grupları ve uygulamaları seçin. |
   | **Koşullu erişim ilkesini daha sonra Oluştur** | Bu kullanım koşulları ilkesi, koşullu erişim ilkesi oluştururken izin verme denetim listesinde görünür. |

   >[!IMPORTANT]
   >Koşullu Erişim İlkesi denetimleri (kullanım koşulları dahil olmak üzere) hizmet hesaplarında zorlamayı desteklemez. Tüm hizmet hesaplarının koşullu erişim ilkesinden dışlanmasını öneririz.

    Özel koşullu erişim ilkeleri, belirli bir bulut uygulamasına veya kullanıcı grubuna göre ayrıntılı kullanım ilkeleri sağlar. Daha fazla bilgi için bkz. [hızlı başlangıç: bulut uygulamalarına erişmeden önce kabul edilmesi gereken kullanım koşulları](require-tou.md).

1. **Oluştur**’a tıklayın.

    Özel bir koşullu erişim şablonu seçtiyseniz, özel koşullu erişim ilkesi oluşturmanıza olanak sağlayan yeni bir ekran görüntülenir.

   ![Özel koşullu erişim ilkesi şablonunu seçtiyseniz yeni koşullu erişim bölmesi](./media/terms-of-use/custom-policy.png)

   Şimdi yeni kullanım koşulları koşullarınızı görmeniz gerekir.

   ![Kullanım koşulları dikey penceresinde listelenen yeni kullanım koşulları](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Kimlerin kabul edildiğini ve reddedildiğini görüntüleyen rapor

Kullanım Koşulları dikey penceresinin kabul eden ve reddeden kullanıcı sayısını gösterdiğini fark edeceksiniz. Bu sayımlar ve kabul edilen/reddedilen, kullanım koşulları ilkesinin ömrü boyunca depolanır.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.

    ![Kullanıcı sayısını gösteren Kullanım koşulları dikey pencere sayısı kabul edildi ve reddedildi](./media/terms-of-use/view-tou.png)

1. Kullanım koşulları ilkesi için, kullanıcıların geçerli durumunu görüntülemek için **kabul edilen** veya **Reddedilen** sayılara tıklayın.

    ![Kabul etmiş olan kullanıcıları listelemek kullanım koşulları onayları bölmesi](./media/terms-of-use/accepted-tou.png)

1. Tek bir kullanıcının geçmişini görüntülemek için üç nokta (**...**) simgesine tıklayın ve ardından **geçmişi görüntüleyin**.

    ![Kullanıcı için geçmişi görüntüle bağlam menüsü](./media/terms-of-use/view-history-menu.png)

   Görünüm geçmişi bölmesinde, tüm kabul etme, reddetme ve süre sonları geçmişini görürsünüz.

   ![Geçmiş bölmesini görüntüle bir kullanıcı için geçmişi kabul eden, reddettiğinde ve süre sonlarını listeler](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD denetim günlüklerini görüntüleme

Ek etkinlikleri görüntülemek istiyorsanız, Azure AD kullanım koşulları ilkeleri denetim günlüklerini içerir. Her kullanıcı onayı, **30 gün** boyunca depolanan denetim günlüklerinde bir olayı tetikler. Bu günlükleri portalda görüntüleyebilir veya .csv dosyası olarak indirebilirsiniz.

Azure AD denetim günlükleri 'ni kullanmaya başlamak için aşağıdaki yordamı kullanın:

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Kullanım koşulları ilkesi seçin.
1. **Denetim günlüklerini görüntüle**' ye tıklayın.

    ![Denetim günlüklerini görüntüle seçeneğinin vurgulandığı dikey pencere Kullanım koşulları](./media/terms-of-use/audit-tou.png)

1. Azure AD denetim günlükleri ekranında, belirli denetim günlüğü bilgilerini hedeflemek için, belirtilen listeleri kullanarak bilgileri filtreleyebilirsiniz.

    Ayrıca **İndir**'e tıklayarak bilgileri yerel olarak kullanmak üzere bir .csv dosyasında indirebilirsiniz.

   ![Azure AD denetim günlükleri ekran listeleme tarihi, hedef ilkesi, tarafından başlatılan ve etkinlik](./media/terms-of-use/audit-logs-tou.png)

   Bir günlüğe tıkladığınızda, ek etkinlik ayrıntıları içeren bir bölme açılır.

   ![Etkinlik, etkinlik durumu, tarafından başlatılan hedef ilke gösteren günlük için etkinlik ayrıntıları](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Kullanıcılar için hangi kullanım koşulları gibi görünüyor

Bir ToU ilkesi oluşturulup zorlandıktan sonra, kapsamdaki kullanıcılar, oturum açma işlemi sırasında aşağıdaki ekranı görür.

![Kullanıcı oturum açtığında görüntülenen örnek kullanım koşulları](./media/terms-of-use/user-tou.png)

Kullanıcılar kullanım koşullarını görüntüleyebilir ve gerekirse, yakınlaştırmak ve uzaklaştırmak için düğmeleri kullanın.

![Yakınlaştırma düğmeleriyle kullanım koşullarını görüntüleme](./media/terms-of-use/zoom-buttons.png)

Aşağıdaki ekranda, bir ToU ilkesinin mobil cihazlarda nasıl göründüğü gösterilmektedir.

![Kullanıcı bir mobil cihazda oturum açtığında görüntülenen örnek kullanım koşulları](./media/terms-of-use/mobile-tou.png)

Kullanıcılar yalnızca kullanım koşulları ilkesini bir kez kabul etmek için gereklidir ve sonraki oturum açma işlemleri sırasında kullanım koşulları ilkesini tekrar görmez.

### <a name="how-users-can-review-their-terms-of-use"></a>Kullanıcıların kullanım koşullarını nasıl gözden geçirebilmeleri

Kullanıcılar, aşağıdaki yordamı kullanarak kabul ettikleri ilke kullanım koşullarını gözden geçirebilir ve görebilirler.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) adresinde oturum açın.
1. Sağ üst köşedeki ad ' a tıklayın ve **profil**' i seçin.

    ![Kullanıcının bölmesi açık olan Uygps sitesi](./media/terms-of-use/tou14.png)

1. Profil sayfanızda **Kullanım koşullarını gözden geçir**'e tıklayın.

    ![Kullanım koşullarını gözden geçirme bağlantısını gösteren bir kullanıcının profil sayfası](./media/terms-of-use/tou13a.png)

1. Buradan, kabul ettiğiniz kullanım koşullarını gözden geçirebilirsiniz.

## <a name="edit-terms-of-use-details"></a>Kullanım koşullarını düzenleme ayrıntıları

Kullanım koşullarının bazı ayrıntılarını düzenleyebilirsiniz, ancak var olan bir belgeyi değiştiremezsiniz. Aşağıdaki yordamda ayrıntıların nasıl düzenleneceği açıklanmaktadır.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Düzenlemek istediğiniz kullanım koşulları ilkesini seçin.
1. **Terimleri Düzenle**' ye tıklayın.
1. Kullanım koşullarını Düzenle bölmesinde, aşağıdakileri değiştirebilirsiniz:
    - **Ad** : Bu, son kullanıcılarla paylaşılmayan ToU 'ın iç adıdır
    - **Görünen ad** – bu, son kullanıcıların ToU 'yi görüntülerken görebilmesini sağlayan addır
    - **Kullanıcıların kullanım koşullarını genişletmesini gerektir** – bunu **Açık** olarak ayarlamak, son kullanımı, kabul etmeden önce ilke belgesi kullanım koşullarını genişletmeye zorlar.
    - Önizle **Mevcut bir kullanım koşulları belgesini güncelleştirebilirsiniz**
    - Mevcut bir ToU 'ya bir dil ekleyebilirsiniz

   PDF belgesi gibi değiştirmek istediğiniz başka ayarlar varsa, kullanıcıların her cihazda onay vermesini, consents 'nin süresini, yeniden kabul etmeden önce geçmesi veya koşullu erişim ilkesini zorunlu kılmak için yeni bir ToU ilkesi oluşturmanız gerekir.

    ![Farklı dil seçeneklerini göstermeyi Düzenle ](./media/terms-of-use/edit-terms-use.png)

1. İşiniz bittiğinde, değişikliklerinizi kaydetmek için **Kaydet** ' e tıklayın.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Mevcut kullanım koşullarının sürümünü veya PDF 'yi güncelleştirme

1.  Azure 'da oturum açın ve [kullanım koşulları](https://aka.ms/catou) gidin
2.  Düzenlemek istediğiniz kullanım koşulları ilkesini seçin.
3.  **Terimleri Düzenle**' ye tıklayın.
4.  Yeni bir sürümü güncelleştirmek istediğiniz dil için eylem sütununun altındaki **Güncelleştir** ' e tıklayın.

    ![Ad ve genişletme seçeneklerini gösteren kullanım koşulları bölmesini Düzenle](./media/terms-of-use/edit-terms-use.png)

5.  Sağ taraftaki bölmede, yeni sürüm için PDF 'yi karşıya yükleyin
6.  Burada, kullanıcılarınızın bir sonraki oturum açışlarında bu yeni sürümü kabul etmelerini gerektirmek istiyorsanız **yeniden kabul iste** ' de bir değiştirme seçeneği gerekir. Kullanıcılarınızın yeniden kabul etmesini istiyorsanız, koşullu erişim ilkenizde tanımlanan kaynağa bir dahaki sefer erişmeye çalıştıklarında, bu yeni sürümü kabul etmeleri istenir. Kullanıcılarınızın yeniden kabul etmesini gerekmiyorsa, önceki onay bilgileri güncel kalır ve yalnızca izin süresinin dolması veya süresi dolan yeni kullanıcılar yeni sürümü görür.

    ![Kullanım koşullarını düzenleme yeniden kabul et seçeneği vurgulanmış](./media/terms-of-use/re-accept.png)

7.  Yeni PDF 'nizi karşıya yükledikten ve yeniden kabul etmeye karar verdikten sonra bölmenin alt kısmındaki Ekle ' ye tıklayın.
8.  Artık belge sütununun altında en son sürümü görürsünüz.

## <a name="view-previous-versions-of-a-tou"></a>ToU 'un önceki sürümlerini görüntüleme

1.  Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için https://aka.ms/catou sayfasına gidin.
2.  Sürüm geçmişini görüntülemek istediğiniz kullanım koşulları ilke koşullarını seçin.
3.  **Diller ve sürüm geçmişine** tıklayın
4.  **Önceki sürümleri gör** ' e tıklayın.

    ![dil sürümleri dahil belge ayrıntıları](./media/terms-of-use/document-details.png)

5.  Bu sürümü indirmek için belgenin adına tıklayabilirsiniz

## <a name="see-who-has-accepted-each-version"></a>Her sürümü kimlerin kabul ettiğini görün

1.  Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için https://aka.ms/catou sayfasına gidin.
2.  Şu anda ToU 'yi kabul eden kişiyi görmek için, istediğiniz ToU için **kabul edilen** sütunun altındaki sayıya tıklayın.
3.  Varsayılan olarak, sonraki sayfada her bir kullanıcının geçerli durumu ToU 'ya kabul edilir
4.  Önceki onay olaylarını görmek isterseniz, **geçerli durum** açılan listesinden **Tümünü** seçebilirsiniz. Artık her bir sürüm hakkındaki ayrıntıları ve ne olduğunu görmek için her Kullanıcı olayını görebilirsiniz.
5.  Alternatif olarak, belirli sürümü kimlerin kabul ettiğini görmek için **Sürüm**  açılan listesinden belirli bir sürümü seçebilirsiniz.


## <a name="add-a-tou-language"></a>ToU dili Ekle

Aşağıdaki yordamda bir ToU dilinin nasıl ekleneceği açıklanmaktadır.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Düzenlemek istediğiniz kullanım koşulları ilkesini seçin.
1. **Terimleri Düzenle** öğesine tıklayın
1. Sayfanın alt kısmındaki **Dil ekle** ' ye tıklayın.
1. Kullanım koşulları Ekle dil bölmesinde yerelleştirilmiş PDF 'nizi karşıya yükleyin ve dili seçin.

    ![Kullanım koşulları seçili ve Ayrıntılar bölmesindeki diller sekmesi gösteriliyor](./media/terms-of-use/select-language.png)

1. **Dil ekle**' ye tıklayın.
1. **Kaydet**’e tıklayın

1. Dili eklemek için **Ekle** ' ye tıklayın.

## <a name="per-device-terms-of-use"></a>Cihaz başına kullanım koşulları

**Her cihaz için kullanıcıların onay vermesini iste** ayarı, son kullanıcıların, eriştiği her cihazda kullanım koşullarınızı kabul etmesini zorunlu kılmanıza olanak sağlar. Son kullanıcının, cihazlarını Azure AD 'ye kaydetmesi gerekecektir. Cihaz kaydedildiğinde, cihaz KIMLIĞI her cihazda kullanım koşullarını zorlamak için kullanılır.

Desteklenen platformların ve yazılımların listesi aşağıda verilmiştir.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Diğer |
> | --- | --- | --- | --- | --- |
> | **Yerel uygulama** | Yes | Yes | Yes |  |
> | **Microsoft Edge** | Yes | Yes | Yes |  |
> | **Internet Explorer** | Yes | Yes | Yes |  |
> | **Chrome (uzantısıyla)** | Yes | Yes | Yes |  |

Cihaz başına kullanım koşulları aşağıdaki kısıtlamalara sahiptir:

- Bir cihaz yalnızca bir kiracıya eklenebilir.
- Bir kullanıcının cihazına katılması için izinleri olması gerekir.
- Intune kayıt uygulaması desteklenmez. Kullanım koşulları gerektiren herhangi bir koşullu erişim ilkesinden dışlandığından emin olun.
- Azure AD B2B kullanıcıları desteklenmez.

Kullanıcının cihazı birleştirilmemişse, cihazlarına katılması gereken bir ileti alırlar. Bu deneyim, platforma ve yazılıma bağımlıdır.

### <a name="join-a-windows-10-device"></a>Bir Windows 10 cihazını ekleme

Bir Kullanıcı Windows 10 ve Microsoft Edge kullanıyorsa, [cihazına katılması](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)için aşağıdakine benzer bir ileti alırlar.

![Windows 10 ve Microsoft Edge-cihazınızın kayıtlı olması gerektiğini belirten Ileti](./media/terms-of-use/per-device-win10-edge.png)

Chrome kullanılıyorsa, [Windows 10 hesapları uzantısını](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)yüklemeleri istenir.

### <a name="register-an-ios-device"></a>İOS cihazı kaydetme

Bir Kullanıcı bir iOS cihazı kullanıyorsa, bu kullanıcılara [Microsoft Authenticator uygulamasını](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)yüklemeleri istenir.

### <a name="register-an-android-device"></a>Android cihazı kaydetme

Bir Kullanıcı bir Android cihaz kullanıyorsa, bu kullanıcıların [Microsoft Authenticator uygulamasını](https://play.google.com/store/apps/details?id=com.azure.authenticator)yüklemeleri istenir.

### <a name="browsers"></a>Browsers (Tarayıcılar)

Kullanıcı, desteklenmeyen bir tarayıcı kullanıyorsa, farklı bir tarayıcı kullanmaları istenir.

![Cihazınızın kayıtlı olması gerektiğini belirten ileti, ancak tarayıcı desteklenmez](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Kullanım koşullarını Sil

Aşağıdaki yordamı kullanarak eski kullanım koşulları ilkelerini silebilirsiniz.

1. Azure'da oturum açın ve **Kullanım Koşulları**'na erişmek için [https://aka.ms/catou](https://aka.ms/catou) sayfasına gidin.
1. Kaldırmak istediğiniz kullanım koşulları ilkesini seçin.
1. **Koşulları sil**'e tıklayın.
1. Devam etmek isteyip istemediğinizi soran iletide **Evet**'e tıklayın.

    ![Kullanım koşullarını silme onayı isteyen ileti](./media/terms-of-use/delete-tou.png)

   Kullanım koşullarınızı artık göremezsiniz.

## <a name="user-acceptance-record-deletion"></a>Kullanıcı kabul kaydı silme

Kullanıcı kabul kayıtları silinir:

- Yönetici ToU 'yi açıkça sildiğinde. Bu durumda, söz konusu ToU ile ilişkili tüm kabul kayıtları da silinir.
- Kiracı Azure Active Directory Premium lisansını kaybeder.
- Kiracı silindiğinde.

## <a name="policy-changes"></a>İlke değişiklikleri

Koşullu erişim ilkeleri hemen etkili olur. Bu durumda, yönetici "üzgün bulutlar" veya "Azure AD belirteç sorunları" ' nı görebilir. Yöneticinin yeni ilkeyi karşılayabilmesi için oturumu kapatıp yeniden oturum açması gerekir.

> [!IMPORTANT]
> Aşağıdaki durumlarda kapsam dahilindeki kullanıcıların yeni bir ilkeyi karşılamak için oturumu kapatıp yeniden oturum açmaları gerekir:
>
> - bir kullanım koşulları ilkesinde koşullu erişim ilkesi etkinleştirilir
> - veya ikinci bir kullanım koşulları ilkesi oluşturulur

## <a name="b2b-guests"></a>B2B konukları

Çoğu kuruluş, çalışanlarının kuruluşun kullanım koşulları ilkesini ve gizlilik bildirimlerini kabul etmek için bir işlem sağlar. Ancak SharePoint veya takımlar aracılığıyla eklendiklerinde Azure AD işletmeden işletmeye (B2B) konuklarına yönelik aynı yarışmaları nasıl zorunlu kılabilirsiniz? Koşullu erişim ve kullanım koşulları ilkelerini kullanarak, bir ilkeyi doğrudan B2B Konuk kullanıcıları doğrultusunda zorlayabilirsiniz. Davet teminat akışı sırasında, kullanıcıya kullanım koşulları ilkesi sunulur. Bu destek şu anda önizlemededir.

Kullanım koşulları ilkeler yalnızca kullanıcının Azure AD 'de bir Konuk hesabı olduğunda görüntülenir. SharePoint Online Şu anda bir belge veya kullanıcının bir Konuk hesabına sahip olmasını gerektirmeyen bir klasörü paylaşmak için geçici bir [dış paylaşım alıcısı deneyimine](/sharepoint/what-s-new-in-sharing-in-targeted-release) sahiptir. Bu durumda, kullanım koşulları ilkesi gösterilmez.

![Kullanıcılar ve gruplar bölmesi-tüm konuk kullanıcılar seçeneği işaretli sekmesini dahil et](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Bulut uygulamaları için destek

Kullanım koşulları ilkeleri Azure Information Protection ve Microsoft Intune gibi farklı bulut uygulamaları için kullanılabilir. Bu destek şu anda önizlemededir.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection uygulaması için bir koşullu erişim ilkesi yapılandırabilir ve Kullanıcı korumalı bir belgeye eriştiğinde bir kullanım koşulları ilkesi gerekir. Bu işlem, korunan bir belgeye ilk kez erişmeden önce bir kullanım koşulları ilkesi tetikler.

![Microsoft Azure Information Protection App ile bulut uygulamaları bölmesi seçildi](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Kayıt Microsoft Intune

Microsoft Intune kayıt uygulaması için bir koşullu erişim ilkesi yapılandırabilir ve Intune 'da cihaz kaydı öncesinde bir kullanım koşulları ilkesi gerekir. Daha fazla bilgi için bkz. [Kuruluşunuz için doğru terimleri seçme çözümü](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)okuma.

![Microsoft Intune App ile bulut uygulamaları bölmesi seçildi](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune kayıt uygulaması, [cihaz başına kullanım koşulları](#per-device-terms-of-use)için desteklenmez.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: kullanım koşulları etkinleştirildiğinde PowerShell 'i kullanarak oturum açamıyorum.**<br />
Y: Kullanım koşulları yalnızca etkileşimli olarak kimlik doğrulanırken kabul edilebilir.

**S: Kullanıcının kullanım koşullarını kabul edip etmediğini veya ne zaman kabul ettiğini nasıl görebilirim?**<br />
Y: Kullanım koşulları dikey penceresinde **kabul edilen** sayıya tıklayın. Ayrıca, kabul etme etkinliğini Azure AD denetim günlüklerinde görüntüleyebilir veya arayabilirsiniz. Daha fazla bilgi için bkz. kimlerin kabul edildiğini ve reddettiğini görüntüleyen rapor görüntüleme ve [Azure AD denetim günlüklerini görüntüleme](#view-azure-ad-audit-logs).

**S: Bilgiler ne kadar süreyle depolanır?**<br />
Y: Kullanıcı kullanım koşulları raporunda sayılır ve kabul edilen/reddedilmiş olan kullanım koşullarının ömrü boyunca depolanır. Azure AD denetim günlükleri 30 gün boyunca depolanır.

**S: kullanım koşullarında neden farklı sayıda onay görüyorum? Azure AD denetim günlükleri?**<br />
Y: kullanım koşulları raporu, bu kullanım koşulları ilkesinin kullanım ömrü boyunca depolanır, ancak Azure AD denetim günlükleri 30 gün boyunca depolanır. Ayrıca, kullanım koşulları raporu yalnızca kullanıcının geçerli onay durumunu görüntüler. Örneğin, bir Kullanıcı reddederse ve kabul ediyorsa, kullanım koşulları raporu yalnızca kullanıcının kabul ettiği süreyi gösterir. Geçmişi görmeniz gerekirse, Azure AD denetim günlükleri ' ni kullanabilirsiniz.

**S: kullanım koşulları ilkesinin ayrıntılarını düzenleyediğimde, kullanıcıların yeniden kabul etmesini ister misiniz?**<br />
Y: Hayır, bir yönetici bir kullanım koşulları ilkesi (ad, görünen ad, kullanıcıların genişletmesini veya bir dil eklemesini iste) ayrıntılarını düzenlerse, kullanıcıların yeni koşulları yeniden kabul etmesini gerektirmez.

**S: mevcut bir kullanım koşulları ilke belgesini güncelleştirebilir miyim?**<br />
Y: Şu anda, mevcut bir kullanım koşulları ilke belgesi güncelleştiremezsiniz. Kullanım koşullarını ilke belgesi olarak değiştirmek için yeni bir kullanım koşulları ilke örneği oluşturmanız gerekir.

**S: köprüler kullanım koşulları ilke PDF belgesi içinde yer alıyorsa son kullanıcılar bu kullanıcılara tıklayabilir mi?**<br />
Y: Evet, son kullanıcılar ek sayfalara köprüler seçebiliyor, ancak belge içindeki bölümlerin bağlantıları desteklenmez. Ayrıca, Azure AD Uygulamaps/MyAccount portalından erişildiğinde, kullanılan ilke PDF 'Lerinde köprüler çalışmaz.

**S: kullanım koşulları, birden çok dili destekleyebilir mi?**<br />
C: Evet. Şu anda bir yöneticinin tek bir kullanım koşulları ilkesi için yapılandırabilirler 108 farklı dil vardır. Bir yönetici birden çok PDF belgesini karşıya yükleyebilir ve ilgili bir dille (108 kadar) bu belgeleri etiketleyebilir. Son kullanıcılar oturum açtığında, tarayıcı dili tercihini inceliyoruz ve eşleşen belgeyi görüntüyoruz. Eşleşme yoksa, karşıya yüklenen ilk belge olan varsayılan belgeyi görüntüleriz.

**S: kullanım koşulları ilkesi ne zaman tetiklenir?**<br />
Y: kullanım koşulları ilkesi, oturum açma deneyimi sırasında tetiklenir.

**S: kullanım koşulları ilkesini hangi uygulamalara hedefleyebilirsiniz?**<br />
Y: Modern kimlik doğrulaması kullanarak kurumsal uygulamalarda koşullu erişim ilkesi oluşturabilirsiniz. Daha fazla bilgi için bkz. [Kurumsal uygulamalar](./../manage-apps/view-applications-portal.md).

**S: belirli bir kullanıcı veya uygulamaya birden çok kullanım koşulları ilkesi ekleyebilir miyim?**<br />
A: Evet, bu grupları veya uygulamaları hedefleyen birden fazla koşullu erişim ilkesi oluşturarak. Bir Kullanıcı birden çok kullanım koşulları ilkesinin kapsamında kalırsa, tek seferde bir kullanım koşulları ilkesi kabul eder.

**S: Kullanıcı kullanım koşulları ilkesini reddederse ne olur?**<br />
C: Kullanıcının uygulamaya erişimi engellenir. Kullanıcının erişim sağlamak için yeniden oturum açması ve koşulları kabul etmesi gerekir.

**S: daha önce kabul edilen bir kullanım koşulları ilkesi kabul edilemez mi?**<br />
Y: [önceden kabul edilen kullanım koşullarını gözden](#how-users-can-review-their-terms-of-use)geçirebilirsiniz, ancak şu anda kabul edilmeden önce bir yol yoktur.

**S: Intune hüküm ve koşullarını de kullanıyorsam ne olur?**<br />
Y: Azure AD kullanım koşulları 'nı ve [Intune hüküm ve koşullarını](/intune/terms-and-conditions-create)yapılandırdıysanız, kullanıcının her ikisini de kabul etmesi gerekecektir. Daha fazla bilgi için bkz. [Kuruluşunuz için doğru terimler çözümü seçme blog gönderisi](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**S: kullanım koşulları hizmeti hangi uç noktalar kimlik doğrulaması için kullanılır?**<br />
A: Kullanım koşulları kimlik doğrulaması için aşağıdaki uç noktaları kullanır: https://tokenprovider.termsofuse.identitygovernance.azure.com ve https://account.activedirectory.windowsazure.com . Kuruluşunuzun kayıt için bir izin verilenler listesi varsa, bu uç noktaları, oturum açma için Azure AD uç noktaları ile birlikte izin verilenler listenize eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: bulut uygulamalarına erişmeden önce kabul edilecek kullanım koşullarını gerektir](require-tou.md)
