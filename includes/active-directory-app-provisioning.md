---
ms.openlocfilehash: c400856546142353a7294a03fce6bbff1c258cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564198"
---
Azure Active Directory (Azure AD) ' de, **uygulama sağlama** terimi, kullanıcıların erişmesi gereken bulut ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) uygulamalarında kullanıcı kimliklerinin ve rollerinin otomatik olarak oluşturulmasını ifade eder. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir. Yaygın senaryolar, bir Azure AD kullanıcısını [Dropbox](../articles/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../articles/active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../articles/active-directory/saas-apps/servicenow-provisioning-tutorial.md)gibi uygulamalarda sağlamayı içerir.

![Sağlama genel bakış Diyagramı](./media/active-directory-app-provisioning/provisioning-overview.png)

Bu özellik şunları yapmanızı sağlar:

- **Sağlama otomatik hale** getirme: takımınıza veya kuruluşunuza katılımlarsa yeni kişiler için doğru sistemlerde otomatik olarak yeni hesaplar oluşturun.
- **Sağlamayı kaldırmayı otomatikleştirme:** Kullanıcılar takımdan veya kuruluştan ayrıldığında doğru sistemlerdeki hesapları otomatik olarak devre dışı bırakır.
- **Sistemler arasında veri eşitlemesi:** Uygulama ve sistemlerinizdeki kimliklerin, dizin veya insan kaynakları sisteminizdeki değişikliklere göre güncel tutulduğundan emin olun.
- **Grupları sağla:** Grupları destekleyen uygulamalara gruplar sağlayın.
- **Erişimi yönetir:** Uygulamalarınıza kimin sağlandığını izleyin ve denetleyin.
- **Kahverengi alan senaryolarında sorunsuz bir şekilde dağıtın:** Kullanıcılar hedef sistemde zaten mevcut olsa bile, sistemler arasında varolan kimlikleri eşleştirin ve kolay tümleştirme için izin verin.
- **Zengin özelleştirme kullan:** Kaynak sistemden hedef sisteme hangi kullanıcı verilerinin akışı gerektiğini tanımlayan özelleştirilebilir öznitelik eşlemelerinden yararlanın.
- **Kritik olaylar için uyarı alın:** Sağlama hizmeti kritik olaylara yönelik uyarılar sağlar ve iş gereksinimlerinizi karşılamak için özel uyarılar tanımlayabileceğiniz Log Analytics tümleştirmeye olanak tanır.

## <a name="benefits-of-automatic-provisioning"></a>Otomatik sağlamanın avantajları

Modern kuruluşlarda kullanılan uygulama sayısı büyümeye devam ettiğinden, BT yöneticileri ölçeğinde erişim yönetimi ile birlikte yapılır. Güvenlik onayları biçimlendirme dili (SAML) veya açık KIMLIK Connect (OıDC) gibi standartlar, yöneticilerin çoklu oturum açmayı (SSO) hızla ayarlayabilmesine izin verir, ancak erişim, kullanıcıların uygulamaya sağlanmasını de gerektirir. Birçok yönetici için, sağlama her kullanıcı hesabını el ile oluşturma veya CSV dosyalarını her hafta karşıya yükleme anlamına gelir, ancak bu süreçler zaman alan, pahalı ve hataya açık bir işlemdir. SAML Just-In-Time (JıT) gibi çözümler sağlamayı otomatik hale getirmeye başlamıştır, ancak kuruluşların kuruluştan ayrıldıklarında veya rol değişikliğine göre belirli uygulamalara erişim gerektirdiklerinde kullanıcıların sağlamasını kaldırmak için bir çözüme ihtiyacı vardır.

Otomatik sağlamayı kullanmaya yönelik bazı yaygın lamalar şunlardır:

- Sağlama işlemlerinin verimliliğini ve doğruluğunu en üst düzeye çıkarın.
- Barındırma ve özel olarak geliştirilmiş sağlama çözümlerinin ve betiklerinin korunmasıyla ilişkili maliyetlere kaydetme.
- Kuruluştan ayrıldıklarında kullanıcıların kimliklerini anahtar SaaS uygulamalarından anında kaldırarak kuruluşunuzun güvenliğini sağlama.
- Çok sayıda kullanıcıyı belirli bir SaaS uygulamasına veya sistemine kolayca içeri aktarma.
- Kimin sağlandığını ve bir uygulamada kimin oturum açmasını belirleyebilen tek bir ilke kümesine sahip olma.

Azure AD Kullanıcı sağlama, bu zorlukları ele almanıza yardımcı olabilir. Müşterilerin Azure AD Kullanıcı sağlamasını nasıl kullandığı hakkında daha fazla bilgi edinmek için [Asos örnek](https://aka.ms/asoscasestudy)olay incelemesini okuyabilirsiniz. Aşağıdaki videoda, Azure AD 'de Kullanıcı sağlamaya yönelik bir genel bakış sunulmaktadır:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD otomatik Kullanıcı sağlama ile hangi uygulamaları ve sistemleri kullanabilirim?

Azure AD, birçok popüler SaaS uygulaması ve insan kaynakları sistemi için önceden tümleşik destek ve [SCIM 2,0 standardının](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)belirli kısımlarını uygulayan uygulamalar için genel destek.

* **Önceden tümleştirilmiş uygulamalar (Galeri SaaS uygulamaları)**. Azure AD 'nin, [Kullanıcı hazırlama için uygulama öğreticileri listesinde](../articles/active-directory/saas-apps/tutorial-list.md)önceden tümleştirilmiş sağlama bağlayıcısını desteklediği tüm uygulamaları bulabilirsiniz. Galeride listelenen önceden tümleştirilmiş uygulamalar, sağlama için genellikle SCıM 2,0 tabanlı kullanıcı yönetimi API 'Lerini kullanır. 

   ![Salesforce logosu](./media/active-directory-app-provisioning/gallery-app-logos.png)

   Sağlama için yeni bir uygulama istemek istiyorsanız [uygulamanızın uygulama galerimiz ile tümleştirmesini](../articles/active-directory/develop/v2-howto-app-gallery-listing.md)isteyebilirsiniz. Bir Kullanıcı sağlama isteği için uygulamanın SCıM ile uyumlu bir uç noktaya sahip olmasını gerektiririz. Uygulamayı platformumuza hızlı bir şekilde sunabilmeniz için lütfen uygulama satıcısının SCıM standardını izlediğinden emin olun.

* **SCıM 2,0 ' i destekleyen uygulamalar**. SCıM 2,0 tabanlı kullanıcı yönetimi API 'Leri uygulayan uygulamaları genel olarak bağlama hakkında daha fazla bilgi için bkz. [SCIM uç noktası oluşturma ve Kullanıcı sağlamayı yapılandırma](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Etki alanları arası kimlik yönetimi (SCıM) sistemi nedir?

Uygulamalar, sağlama ve sağlamayı kaldırma işlemlerini otomatik hale getirmeye yardımcı olmak için özel kullanıcı ve Grup API 'Leri sunar. Ancak, birden fazla uygulamadaki kullanıcıları yönetmeye çalışmış olan herkes, her uygulamanın kullanıcı oluşturma veya güncelleştirme, gruplara kullanıcı ekleme veya kullanıcıları sağlamayı kaldırma gibi aynı basit eylemleri gerçekleştirmeye çalıştığını söyler. Henüz, tüm bu basit eylemler, farklı uç nokta yolları, Kullanıcı bilgilerini belirtmek için farklı yöntemler ve her bilgi öğesini temsil eden farklı bir şema kullanılarak yalnızca biraz farklı bir şekilde uygulanır.

Bu güçlükleri ele almak için, SCıM belirtimi, kullanıcıların uygulamaları uygulamasına, dışına ve bu uygulamalarda hareket ettirmek için ortak bir Kullanıcı şeması sağlar. SCıM, sağlama için standart bir standart haline geliyor ve SAML veya OpenID Connect gibi Federasyon standartlarıyla birlikte kullanıldığında, yöneticilere erişim yönetimi için uçtan uca standartlara dayalı bir çözüm sunar.

Kullanıcıları ve grupları bir uygulamaya sağlamayı ve sağlamayı otomatik hale getirmeyi otomatikleştirmek için bir SCıM uç noktası geliştirmeye yönelik ayrıntılı yönergeler için, bkz. [SCIM uç noktası oluşturma ve Kullanıcı sağlamayı yapılandırma](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md). Galerideki önceden tümleştirilmiş uygulamalar (bolluk, Azure Databricks, kar tanesi vb.) için, geliştirici belgelerini atlayabilir ve [burada](../articles/active-directory/saas-apps/tutorial-list.md)verilen öğreticilerin kullanılmasını sağlayabilirsiniz.

## <a name="manual-vs-automatic-provisioning"></a>El ile - otomatik sağlama karşılaştırması

Azure AD galerisinde bulunan uygulamalar, iki sağlama modundan birini destekler:

* **El ile** sağlama, henüz uygulama Için OTOMATIK Azure AD sağlama Bağlayıcısı olmadığı anlamına gelir. Kullanıcı hesaplarının el ile oluşturulması gerekir. Örneğin, kullanıcıları doğrudan uygulamanın yönetim portalına ekleyerek veya Kullanıcı hesabı ayrıntısı olan bir elektronik tablo karşıya yükleyerek. Uygulama tarafından sağlanan belgelere başvurun veya hangi mekanizmaların kullanılabildiğini belirlemek için uygulama geliştiricisine başvurun.

* **Otomatik** , bu uygulama Için BIR Azure AD sağlama bağlayıcısının geliştirildiği anlamına gelir. Uygulama için sağlamayı ayarlamaya özgü kurulum öğreticisini izlemelisiniz. Uygulama öğreticileri, [SaaS uygulamalarını Azure Active Directory Ile tümleştirme hakkındaki öğreticiler listesinde](../articles/active-directory/saas-apps/tutorial-list.md)bulunabilir.

Azure AD galerisinde, otomatik sağlamayı destekleyen uygulamalar bir **sağlama** simgesiyle belirlenir. Yeni Galeri önizleme deneyimine geçiş yapın ( **Uygulama Ekle sayfasının** en üstündeki başlıkta, **yeni ve geliştirilmiş uygulama galerisini denemek için buraya tıklayın**) bağlantısını seçin.

![Uygulama galerisinde sağlama simgesi](./media/active-directory-app-provisioning/browse-gallery.png)

Uygulama tarafından desteklenen sağlama modu, uygulamayı **Kurumsal uygulamalarınıza** ekledikten sonra **sağlama** sekmesinde da görünür.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Nasıl yaparım? bir uygulamaya otomatik sağlama mi ayarlanacak?

Galeride listelenen önceden tümleştirilmiş uygulamalar için, otomatik sağlamayı ayarlamaya yönelik adım adım yönergeler sunulmaktadır. [Tümleşik Galeri uygulamaları için öğreticilerin listesine](../articles/active-directory/saas-apps/tutorial-list.md)bakın. Aşağıdaki videoda SalesForce için otomatik Kullanıcı sağlamayı ayarlama işlemi gösterilmektedir.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCıM 2,0 ' ı destekleyen diğer uygulamalar için, [SCIM uç noktası oluşturma ve Kullanıcı sağlamayı yapılandırma](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md)makalesindeki adımları izleyin.