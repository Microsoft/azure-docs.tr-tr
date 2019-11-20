---
title: Azure EA portalı yönetimi
description: Bu makalede, bir yöneticinin Azure EA portalında gerçekleştirdiği genel görevler açıklanmaktadır.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 3c992274ba8ff82795e9a4f917c67e7ef379d13e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888439"
---
# <a name="azure-ea-portal-administration"></a>Azure EA portalı yönetimi

Bu makalede, bir yöneticinin Azure EA portalında gerçekleştirdiği genel görevler açıklanmaktadır (https://ea.azure.com). Azure EA portalı, müşterilerin Azure EA hizmetlerinin maliyetini yönetmesine yardımcı olan bir çevrimiçi yönetim portalıdır. Azure EA portalı hakkında giriş bilgileri için [Azure EA portalı ile çalışmaya başlama](billing-ea-portal-get-started.md) makalesini okuyun.

## <a name="add-a-new-enterprise-administrator"></a>Yeni bir kuruluş yöneticisi ekleme

Kuruluş yöneticileri bir Azure EA kaydını yönetirken en fazla ayrıcalığa sahip olan kullanıcılardır. İlk Azure EA yöneticisi, EA sözleşmesi ayarlanırken oluşturulmuştur. Ancak, dilediğiniz zaman yeni yöneticiler ekleyebilir veya kaldırabilirsiniz. Yeni yöneticiler yalnızca var olan yöneticiler tarafından eklenir. Ek kuruluş yöneticileri ekleme hakkında daha fazla bilgi için bkz. [Başka bir kuruluş yöneticisi oluşturma](billing-ea-portal-get-started.md#create-another-enterprise-admin). Faturalama profili rolleri ve görevleri hakkında daha fazla bilgi için bkz. [Faturalama profili rolleri ve görevleri](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Beklemede olan kullanıcı durumunu etkin olarak güncelleştirme

Bir Azure EA kaydına ilk kez yeni Hesap Sahipleri (AO) eklendiğinde durumları _beklemede_ olarak görünür. Yeni bir hesap sahibi etkinleştirme karşılama e-postasını aldığında, hesabını etkinleştirmek için oturum açabilir. Hesabını etkinleştirdiğinde, _beklemede_ olan hesap durumu _etkin_ olarak güncelleştirilir. Hesap sahibinin "Uyarı" iletisini okuması ve **Devam**'a tıklaması gerekir. Yeni kullanıcılardan Bir Ticari Hesap oluşturmak için ad ve soyadı girmeleri istenebilir. Bu durumda, devam etmek için gerekli bilgileri eklemesi gerekir. Bundan sonra hesap etkinleştirilir.

## <a name="add-a-department-admin"></a>Departman yöneticisi ekleme

Azure EA yöneticisi bir departman oluşturduktan sonra, Azure Kuruluş yöneticisi departman yöneticileri ekleyebilir ve her birini bir departmanla ilişkilendirebilir. Bir departman yöneticisi yeni hesaplar oluşturabilir. Azure EA aboneliklerinin oluşturulabilmesi için yeni hesaplar gereklidir.

Departman yöneticisi ekleme hakkında daha fazla bilgi için bkz. [Azure EA departman yöneticisi oluşturma](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="associate-an-account-to-a-department"></a>Bir hesabı bir departmanla ilişkilendirme

Kuruluş Yöneticileri, mevcut hesapları kayıt kapsamındaki Bölümlerle ilişkilendirebilir.

### <a name="to-associate-an-account-to-a-department"></a>Bir hesabı bir departmanla ilişkilendirmek için

1. Azure EA Portal'da kuruluş yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **Bölüm**'e tıklayın.
1. Seçmek istediğiniz hesabın olduğu satıra gelin ve sağ tarafındaki kalem simgesine tıklayın.
1. Açılan menüden istediğiniz Bölümü seçin.
1. **Kaydet**’e tıklayın.

## <a name="department-spending-quotas"></a>Departman harcama kotaları

EA müşterileri, bir kayıt kapsamındaki bölümler için ayrı harcama kotaları belirleyebilir veya mevcut kotaları değiştirebilir. Harcama kotasının miktarı geçerli taahhüt dönemini kapsayacak şekilde ayarlanır. Geçerli taahhüt döneminin sonunda değerler güncelleştirilmediği sürece sistem, mevcut harcama kotasını takip eden taahhüt dönemine uzatır.

Bölüm yöneticisi, harcama kotasını görüntüleyebilir ancak kota miktarı yalnızca kuruluş yöneticisi tarafından güncelleştirilebilir. Kota %50, %75, %90 ve %100 düzeyine ulaştığında kuruluş yöneticisine ve bölüm yöneticisine bildirim gönderilir.

### <a name="enterprise-administrator-to-set-the-quota"></a>Kuruluş yöneticisi olarak kotayı belirlemek için:

 1. Azure EA Portal'ı açın.
 1. Sol gezinti panelinde **Yönet**’e tıklayın.
 1. **Bölüm** sekmesine tıklayın.
 1. Seçmek istediğiniz Bölüme tıklayın.
 1. Bölüm Ayrıntıları bölümündeki kalem simgesine tıklayın veya yeni bir bölüm ve harcama kotası eklemek için **+ Bölüm Ekle** düğmesine tıklayın.
 1. Bölüm Ayrıntıları altındaki Harcama Kotası $ kutusuna kaydın para birimini kullanarak bir harcama kotası girin (değer 0'dan büyük olmalıdır).
    - Bu adımda Bölüm Adı ve Maliyet Merkezi bilgilerini de düzenleyebilirsiniz.
 1. **Kaydet**'e tıklayın.

Bölüm harcama kotası, Bölüm sekmesindeki Bölüm Listesi görünümünde görünür duruma gelir. Azure EA Portal, geçerli taahhüt döneminin sonunda harcama kotalarını bir sonraki taahhüt dönemine aktarır.

Bölüm kotası tutarı, mevcut parasal taahhütten bağımsızdır ve kota miktarı ile uyarılar yalnızca birinci taraf kullanımı için geçerlidir. Bölüm harcama kotası yalnızca bilgilendirme amaçlıdır ve harcama limiti uygulamaz.

### <a name="department-administrator-to-view-the-quota"></a>Bölüm yöneticisi olarak kotayı görüntülemek için:

1. Azure EA Portal'ı açın.
1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **Bölüm** sekmesine tıklayıp harcama kotalarının yer aldığı Bölüm Listesi görünümünü açın.

Dolaylı müşteriyseniz ücret özelliklerinin kanal iş ortağınız tarafından etkinleştirilmesi gerekir.

## <a name="enterprise-user-roles"></a>Kurumsal kullanıcı rolleri

Azure EA portalı, Azure EA maliyetlerinizi ve kullanımınızı yönetmenize yardımcı olur. Azure EA portalında üç ana rol vardır:

- EA yöneticisi
- Departman yöneticisi
- Hesap sahibi

Her rolün farklı bir erişim düzeyi ve yetkisi vardır.

Kullanıcı rolleri hakkında daha fazla bilgi için bkz. [Kurumsal kullanıcı rolleri](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Azure EA hesabı ekleme

Azure EA hesabı, Azure EA portalında abonelikleri yönetmek için kullanılan ve aynı zamanda raporlama için kullanılan bir kurumsal birimdir. Azure hizmetlerine erişmek için bir hesap oluşturmanız ve sizin için oluşturulmuş bir hesap olması gerekir.

Azure hesapları hakkında daha fazla bilgi için bkz. Hesap ekleme.

## <a name="enterprise-devtest-offer"></a>Kurumsal Geliştirme ve Test Teklifi

Azure kuruluş yöneticisi olarak kuruluşunuzdaki hesap sahiplerinin EA Geliştirme ve Test teklifini kullanarak abonelik oluşturmasını sağlayabilirsiniz. Bunun için Azure EA Portal'da ilgili hesap sahibi için Geliştirme ve Test kutusunu işaretlemeniz gerekir.

Geliştirme ve Test kutusunu işaretledikten sonra hesap sahibine Geliştirme ve Test aboneleri için ihtiyaç duyulan EA Geliştirme ve Test aboneliklerini oluşturabileceğini söyleyin.

Bu teklif, etkin Visual Studio abonelerinin Azure üzerinde özel Geliştirme ve Test ücretleriyle geliştirme ve test iş yükleri çalıştırmalarını Windows 8.1 ile Windows 10 gibi Geliştirme ve Test görüntülerinin de yer aldığı tam kapsamlı galeriye erişmelerini sağlar.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Kurumsal Geliştirme ve Test teklifini ayarlamak için:

1. Kuruluş yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **Hesap** sekmesine tıklayın.
1. Geliştirme ve Test erişimini etkinleştirmek istediğiniz hesabın yer aldığı satıra tıklayın.
1. Satırın sağ tarafındaki kalem simgesine tıklayın.
1. Geliştirme ve Test onay kutusunu seçin.
1. **Kaydet**'e tıklayın.

Bir kullanıcı Azure EA Portal aracılığıyla hesap sahibi olarak eklendiğinde hesap sahibiyle ilişkilendirilmiş olan kullandıkça öde Geliştirme ve Test teklifini Visual Studio için aylık kredi tekliflerini temel alan abonelerle ilişkilendirilmiş olan Azure abonelikleri, EA Geliştirme ve Test teklifine dönüştürülür. Kullandıkça öde gibi Hesap Sahibiyle ilişkilendirilmiş olan diğer teklif türlerini temel alan abonelikler, Microsoft Azure Kurumsal tekliflerine dönüştürülür.

Geliştirme ve Test Teklifi şu an için Azure Kamu müşterilerinin kullanımına açık değildir.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Kurumsal bir hesabı yeni bir kayda aktarma

Kurumsal bir hesabı yeni bir kayda aktarırken aşağıdaki noktaları aklınızda bulundurun:

- Yalnızca istekte belirtilen hesaplar aktarılır. Tüm hesaplar seçilirse hepsi aktarılır.
- Kaynak kaydının durumu etkin veya genişletilmiş olarak kalır. Kaydı süresi dolana kadar kullanmaya devam edebilirsiniz.

### <a name="effective-transfer-date"></a>Geçerli aktarım tarihi

Geçerli aktarım tarihi, aktarmak istediğiniz kaydın başlangıç tarihi veya sonrasındaki bir tarih olabilir. Aktarımı yapmakta olduğunuz kayıt _hedef kayıttır_. Hesap aktarımından sonra, hesapta geçerli aktarım tarihinden önceki tüm kullanım bilgileri aktardığınız kayıt içinde kalır. Aktarmakta olduğunuz kayıt _kaynak kayıttır_.  Kaynak kayıt kullanımı, parasal taahhüt karşılığında veya fazla kullanım olarak ücretlendirilir. Geçerli aktarım tarihinden sonra gerçekleşen kullanımlar yeni kayda aktarılır ve uygun şekilde ücretlendirilir.

Bir kayıt aktarımını, hedef kaydın başlangıç tarihine kadar geçerli olacak şekilde ayarlayabilirsiniz. Veya kaynak kaydının geçerli olduğu başlangıç tarihine kadar.

### <a name="monetary-commitment"></a>Parasal taahhüt

Parasal taahhüt, kayıtlar arasında aktarılamaz. Parasal taahhüt bakiyeleri, sipariş edildiği kayda sözleşmeyle bağlıdır. Parasal taahhüt, hesap veya kayıt aktarma işleminin bir parçası olarak aktarılmaz.

### <a name="services-affected"></a>Etkilenen hizmetler

Hesap aktarımı sırasında hizmet kesintisi olmaz. Gerekli tüm bilgiler sağlanırsa isteğiniz gün içinde tamamlanabilir.

### <a name="prerequisites"></a>Ön koşullar

Bir hesap aktarımı istediğinizde aşağıdaki bilgileri sağlayın:


- Aktarılacak hesabın hesap adı ve hesap sahibi kimliği
- Kaynak kaydı için, aktarılacak kayıt numarası ve hesap
- Kaynak kaydı için, aktarımın yapılacağı kayıt numarası
- Hesap aktarımı geçerlilik tarihi, hedef kaydın başlangıç tarihi veya sonrasındaki bir tarih olabilir

Hesap aktarımından önce göz önünde bulundurmanız gereken diğer noktalar:

- Hedef ve kaynak kaydı için bir EA yöneticisinden onay gerekir
- Hesap aktarımı gereksinimlerinizi karşılamıyorsa, kayıt aktarımı yapmayı düşünün.
- Hesap aktarımı, belirli hesaplarla ilgili tüm hizmetleri ve abonelikleri aktarır.
- Aktarım tamamlandıktan sonra, aktarılan hesap, kaynak kayıt altında etkin değil olarak görünür.
- Hesap aktarımı, hedef kayıt başlangıç tarihi içinde herhangi bir tarihe kadar geri alınabilir.
- Hesap, kaynak kayıtta etkin aktarım tarihine ve hedef kayıtta başlangıç tarihine karşılık gelen bitiş tarihini gösterir.
- Hesap için etkin aktarım tarihi öncesinde gerçekleştirilen tüm kullanımlar, kaynak kaydın altında kalır.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Kuruluş kaydını yeni bir kayda aktarma

Bir kuruluş kaydının tamamını bir kayda aktarmak istediğinizde aşağıdaki eylemler gerçekleşir:

- Tüm EA departman yöneticileri de dahil olmak üzere tüm Azure hizmetleri, abonelikleri, hesapları, departmanları ve kayıt yapısının tamamı aktarılır.
- Kayıt durumu _Aktarıldı_ olarak ayarlanır. Aktarılan kayıt yalnızca geçmiş kullanım raporlama amacıyla kullanılabilir.
- Aktarılan bir kayda rol veya abonelik ekleyemezsiniz. Aktarıldı durumu, kayda aykırı ek kullanım yapılmasını önler.
- Anlaşmadaki kalan parasal taahhüt bakiyeleri, geleceğe dönük hükümler de dahil olmak üzere kaybedilir.
-   Aktardığınız kayıtta satın alınmış ayrılmış örnekler varsa ayrılmış örnek satın alma ücreti kaynak kayıtta kalır ancak tüm ayrılmış örnek avantajları yeni kayıtta kullanılmak üzere aktarılır.
-   Market için tek seferlik satın alma ücreti ve eski kayıtta tahakkuk etmiş olan aylık sabit ücretler yeni kayda aktarılmaz. Tüketime dayalı market ücretleri aktarılır.
-   Süresi dolmuş olan kayıt aktarıldıktan sonra satın alma kaynaklı tek seferlik ücretler kaynak kayıtta kalır.

### <a name="effective-transfer-date"></a>Geçerli aktarım tarihi

Geçerli aktarım tarihi, hedef kayda aktarmak istediğiniz kaydın başlangıç tarihi veya sonrasındaki bir tarih olabilir.

Kaynak kayıt kullanımı, parasal taahhüt karşılığında veya fazla kullanım olarak ücretlendirilir. Geçerli aktarım tarihinden sonra gerçekleşen kullanımlar yeni kayda aktarılır ve uygun şekilde ücretlendirilir.

### <a name="effective-transfer-date-in-the-past"></a>Geçmişteki geçerli aktarım tarihi

Bir hesap aktarımını, hedef kaydın başlangıç tarihine kadar geçerli olacak şekilde ayarlayabilirsiniz. Veya kaynak kaydının geçerli olduğu başlangıç tarihine kadar.

### <a name="monetary-commitment"></a>Parasal taahhüt

Parasal taahhüt, kayıtlar arasında aktarılamaz. Parasal taahhüt bakiyeleri, sipariş edildiği kayda sözleşmeyle bağlıdır. Parasal taahhüt, hesap veya kayıt aktarma işleminin bir parçası olarak aktarılmaz.

### <a name="services-affected"></a>Etkilenen hizmetler

Hesap aktarımı sırasında hizmet kesintisi olmaz. Gerekli tüm bilgiler sağlanırsa isteğiniz gün içinde tamamlanabilir.

### <a name="prerequisites"></a>Ön koşullar

Bir kayıt aktarımı istediğinizde aşağıdaki bilgileri sağlayın:

- Kaynak kaydı için, aktarılacak kayıt numarası ve hesap.
- Hedef kaydı için, aktarımın yapılacağı kayıt numarası.
- Kayıt aktarımı geçerlilik tarihi, hedef kaydın başlangıç tarihi veya sonrasındaki bir tarih olabilir. Seçilen tarih, daha önce düzenlenmiş bir fazla kullanım faturası için kullanımı etkilemez.

Kayıt aktarımından önce göz önünde bulundurmanız gereken diğer noktalar:

- Hedef ve kaynak kaydı için bir EA yöneticisinden onay gerekir.
- Kayıt aktarımı gereksinimlerinizi karşılamıyorsa, hesap aktarımı yapmayı düşünün.
- Yalnızca belirttiğiniz hesaplar aktarılır. Tüm hesaplarınızı aktarmayı isteyebilirsiniz.
- Kaynak kaydının durumu etkin/genişletilmiş olarak kalır. Kaydı süresi dolana kadar kullanmaya devam edebilirsiniz.

## <a name="change-account-owner"></a>Hesap sahibini değiştirme

Azure EA portalı, abonelikleri bir hesap sahibinden diğerine aktarabilir. Daha fazla bilgi için bkz. [Hesap sahibini değiştirme](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Abonelik aktarımının etkileri

Bir Azure aboneliği aynı Azure Active Directory kiracısındaki bir hesaba aktarılırsa, abonelikteki kaynakları yönetmek için [rol tabanlı erişim denetimine (RBAC)](../role-based-access-control/overview.md) sahip olan tüm kullanıcılar, gruplar ve hizmet sorumluları aynı erişime sahip olmaya devam eder.

Aboneliğe RBAC erişimi olan kullanıcıları görüntülemek için:

1. Azure portalında **Abonelikler**’i açın.
2. Görüntülemek istediğiniz aboneliği seçin ve ardından **Erişim denetimi (IAM)** seçeneğini belirleyin.
3. **Rol atamaları**’nı seçin. Rol atamaları sayfasında, aboneliğe RBAC erişimi olan tüm kullanıcılar listelenir.

Abonelik farklı bir Azure AD kiracısındaki bir hesaba aktarılırsa, abonelikteki kaynakları yönetmek için [RBAC](../role-based-access-control/overview.md) iznine sahip olan tüm kullanıcılar, gruplar ve hizmet sorumluları _erişimi_ kaybeder. RBAC erişimi mevcut olmasa da aboneliğe aşağıdaki güvenlik mekanizmaları aracılığıyla erişilebilir:

- Abonelik kaynaklarına kullanıcı yöneticisi hakları veren yönetim sertifikaları. Daha fazla bilgi için bkz. [Azure için Yönetim Sertifikası Oluşturma ve Karşıya Yükleme](../cloud-services/cloud-services-certs-create.md).
- Depolama gibi hizmetler için erişim anahtarları. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md).
- Azure Sanal Makineleri gibi hizmetler için Uzaktan Erişim kimlik bilgileri.

Alıcının Azure kaynaklarına erişimi kısıtlaması gerekiyorsa, hizmetle ilişkili tüm gizli dizileri güncelleştirmeyi düşünmelidir. Çoğu kaynak aşağıdaki adımlar kullanılarak güncelleştirilebilir:

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. Merkez menüsünde **Tüm kaynaklar**'ı seçin.
3. Kaynağı seçin.
4. Kaynak sayfasında, mevcut gizli dizileri görüntülemek ve güncelleştirmek için **Ayarlar**’a tıklayın.

## <a name="delete-subscription"></a>Aboneliği silme

Hesap sahibi olduğunuz bir aboneliği silmek için:

1. Hesabınızla ilişkilendirilmiş kimlik bilgileriyle Azure portalında oturum açın.
1. Hub menüsünde **Abonelikler**'i seçin.
1. Sayfanın sol üst köşesindeki abonelikler sekmesinde iptal etmek istediğiniz aboneliği seçin ve iptal sekmesini açmak için **Aboneliği iptal et**'e tıklayın.
1. Abonelik adını girip iptal için bir neden seçin ve **Aboneliği iptal et** düğmesine tıklayın.

Abonelikleri yalnızca hesap yöneticilerinin iptal edebileceğini lütfen unutmayın.

## <a name="delete-an-account"></a>Hesap silme

Hesap kaldırma işlemi yalnızca etkin aboneliği bulunmayan etkin hesaplarda gerçekleştirilebilir.

1. Enterprise Portal'ın sol tarafındaki gezinti menüsünden **Yönet**'i seçin.
1. **Hesap** sekmesine tıklayın.
1. Hesaplar tablosundan silmek istediğiniz hesabı seçin.
1. Hesap satırının sağ tarafındaki X simgesine tıklayın.
1. Hesabın altında etkin abonelik kalmadığında Hesap satırının altındaki **Evet** düğmesine tıklayarak Hesap kaldırma işlemini onaylayın.

## <a name="update-notification-settings"></a>Bildirim ayarlarını güncelleştirme

Kuruluş Yöneticileri, kayıtlarıyla ilişkili kullanım bildirimlerini almak için otomatik olarak kaydedilir. Her Kuruluş Yöneticisi, bildirimlerin aralığını tek tek değiştirebilir veya hepsini tamamen devre dışı bırakabilir.

Bildirim kişileri, Azure EA portalındaki **Bildirim Kişisi** bölümünde gösterilir. Bildirim kişilerinizi yönetmek, kuruluşunuzdaki doğru kişilerin Azure EA bildirimleri almasını sağlar.

Geçerli bildirim ayarlarını görüntülemek için:

1. Azure EA portalında **Yönet** > **Bildirim Kişisi**’ne gidin.
2. E-posta Adresi – Kuruluş Yöneticisinin bildirimleri alan Microsoft Hesabı, İş veya Okul Hesabı ile ilişkili e-posta adresi.
3. Faturalandırılmamış Bakiye Bildirimi Sıklığı – Bildirimlerin her bir Kuruluş Yöneticisine gönderilmek üzere ayarlandığı sıklık.

Kişi eklemek için:

1. **+Kişi Ekle**’ye tıklayın.
2. E-posta adresini girin ve sonra onaylayın.
3. **Kaydet**’e tıklayın.

Yeni bildirim kişisi **Bildirim Kişisi** bölümünde gösterilir. Bildirim sıklığını değiştirmek için, bildirim kişisini seçin ve seçili satırın sağ tarafındaki kalem simgesine tıklayın. Sıklığı **günlük**, **haftalık**, **aylık** veya **hiçbiri** olarak ayarlayın.

_Yaklaşan kapsama dönemi bitiş tarihi_ ve _yaklaşan tarih_ yaşam döngüsü bildirimlerini devre dışı bırakıp sağlamayı bırakabilirsiniz. Yaşam döngüsü bildirimlerini devre dışı bırakmak, kapsama dönemi ve anlaşma bitiş tarihi hakkındaki bildirimleri gizler.

## <a name="manage-partner-administrators"></a>İş ortağı yöneticilerini yönetme

Azure EA Portal'daki iş ortağı yöneticileri, başka iş ortağı yöneticilerini ekleyebilir veya kaldırabilir. İş ortağı yöneticileri, dolaylı kayıtların iş ortağı kuruluşlarıyla ilişkilendirilir, doğrudan kayıtlarla ilişkilendirilmez.

### <a name="add-a-partner-administrator"></a>İş ortağı yöneticisi ekleme

Mevcut kullanıcıyla aynı iş ortağı kuruluşuyla ilişkilendirilmiş olan tüm kayıtların listesini görüntülemek için **Kayıt** sekmesine tıklayıp istediğiniz kayıt kutusunu seçin.

1. İş ortağı yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **İş ortağı** sekmesine tıklayın.
1. **+ Yönetici ekle**'ye tıklayıp e-posta adresini, bildirim için gerekli iletişim bilgilerini ve bildirim ayrıntılarını girin.
1. **Ekle**'ye tıklayın.

### <a name="remove-a-partner-administrator"></a>İş ortağı yöneticisini kaldırma

Mevcut kullanıcıyla aynı iş ortağı kuruluşuyla ilişkilendirilmiş olan tüm kayıtların listesini görüntülemek için **Kayıt** sekmesine tıklayıp istediğiniz kayıt kutusunu seçin.

1. İş ortağı yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **İş ortağı** sekmesine tıklayın.
1. Yönetici bölümünde, kaldırmak istediğiniz yöneticinin bulunduğu satırı seçin.
1. Sağdaki X simgesine tıklayın.
1. Silmek istediğinizi onaylayın.

## <a name="manage-partner-notifications"></a>İş ortağı bildirimlerini yönetme

İş Ortağı Yöneticileri, kayıtları için kullanım bildirimlerini ne sıklıkla alacaklarını yönetebilir. Bu yöneticiler, faturalandırılmamış bakiyeleriyle ilgili haftalık bildirimleri otomatik olarak alırlar. Bildirimlerin sıklığını aylık, haftalık, günlük olarak tek tek değiştirebilir veya tamamen devre dışı bırakabilirler.

Bir kullanıcı tarafından bildirim alınmadıysa, kullanıcının bildirim ayarlarının doğru olduğunu aşağıdaki adımlarla onaylayın.

1. Azure EA portalında İş Ortağı Yöneticisi olarak oturum açın.
2. **Yönet**’e ve sonra **İş Ortağı** sekmesine tıklayın.
3. Yönetici bölümünde yönetici listesini göreceksiniz.
4. Bildirim tercihlerini düzenlemek için uygun yöneticinin üzerine gelin ve kurşun kalem simgesine tıklayın.
5. Gerektiğinde bildirim sıklığını ve yaşam döngüsü bildirimlerini güncelleştirin.
6. Gerekirse bir kişi ekleyin ve **Ekle**'ye tıklayın.
7. **Kaydet**’e tıklayın.

![Kişi Ekle kutusunu gösteren örnek ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>İş ortağı yöneticileri için kayıtları görüntüleme

İş ortağı yöneticileri, Azure EA Portal'daki doğrudan ve dolaylı kayıtlarının listesini görüntüleyebilir. Her kayıtla ilgili kayıt numarası, kayıt adı, bakiye ve fazla kullanım miktarı gibi özet görüntüleri içeren kutular görüntülenir.

### <a name="view-a-list-of-enrollments"></a>Kayıt listesini görüntüleme

1. İş ortağı yöneticisi olarak oturum açın.
1. Sayfanın sol tarafındaki menüde **Yönet**'e tıklayın.
1. **Kayıt** sekmesine tıklayın.
1. Seçmek istediğiniz kayda ait olan kutuyu seçin.

Sayfanın en üst kısmında tüm kayıtları içeren ve her kaydın bir kutuyla gösterildiği bir görünüm yer alır. İsterseniz sayfanın sol tarafındaki gezinti menüsünden geçerli kayıt numarasına tıklayarak kayıtlar arasında geçiş yapabilirsiniz. Kayıtlar arasında arama yapmanızı sağlayan bir pencere açılır. Dilerseniz ilgili kutuya tıklayarak da farklı bir kaydı seçebilirsiniz.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorluğu Teklifi

Azure Sponsorluğu Teklifi, sınırlı ve sponsorlu bir Microsoft Azure hesabıdır. Yalnızca Microsoft tarafından seçilen sınırlı müşteriler tarafından e-posta daveti ile kullanılabilir. Microsoft Azure Sponsorluğu teklifine hak kazandıysanız hesap kimliğinize bir e-posta davetiyesi alırsınız.

Daha fazla bilgi için [sponsorluğu etkinleştirme amacıyla bir destek isteği oluşturun](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>İş veya okul hesabı kimlik doğrulamasına dönüştürme

Azure Kurumsal kullanıcıları, Microsoft hesabı (MSA veya Live Kimliği) yerine iş veya okul hesabı (Azure Active Directory kullanan) kullanmayı tercih edebilir.

Başlamak için:

1. İş veya okul hesabını Azure EA Portal'da istediğiniz rollere ekleyin.
1. Hata alırsanız, hesap geçerli bir Active Directory hesabı olmayabilir.  Azure, Kullanıcı Asıl Adı (UPN) değerini kullanır ve bu her zaman e-posta adresiyle aynı olmayabilir.
1. İş veya okul hesabını kullanarak Azure EA Portal'da kimliğinizi doğrulayın.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Aboneliklerde Microsoft hesabı yerine iş veya okul hesabını kullanmak için:

1. Aboneliklerin sahibi olan Microsoft hesabını kullanarak yönetim portalında oturum açın.
1. Yeni hesaba geçiş yapmak için hesap sahipliği aktarma işlevini kullanın.
1. Bunu yaptığınızda Microsoft hesabında silinebilecek etkin abonelik kalmamalıdır.
1. Silinen hesaplar geçmişe dönük faturalama nedeniyle portalda kalır ancak devre dışı durumda görünür.  Yalnızca etkin hesapların görüntülenmesini sağlayan onay kutusunu seçerek eski hesapları görünümden kaldırabilirsiniz.

## <a name="account-subscription-ownership-faq"></a>Hesap aboneliği sahipliği hakkında SSS

Bu belgede hesap aboneliği sahipliğiyle ilgili sık sorulan sorular ve yanıtları yer almaktadır.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Bir abonelikte kaç Azure hesabı sahibi olabilir?

Her abonelikte yalnızca bir tane hesap sahibi bulunabilir.  Rol Tabanlı Erişim veya Erişim Denetimi (IAM) işlevlerini kullanarak daha fazla rol ekleyebilirsiniz. Bunun için şu sayfanın sol üst köşesindeki abonelik sekmesini kullanın: [portal.azure.com]](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Bir Azure hesabı sahibi birden fazla bölümde listelenebilir mi?

Bir hesap sahibi yalnızca bir bölümle ilişkilendirilebilir.  Bunun nedeni bölümle ilişkilendirilmiş olan maliyetlerin/harcamaların Azure EA Portal'daki EA kaydı altında doğru şekilde izlenebilmesini ve ayrılabilmesini sağlamaktır.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Bir Azure hesabı sahibi güvenlik grubu olarak listelenebilir mi?

Hayır, abonelik sahibi benzersiz bir Microsoft hesabı (MSA) veya Azure Active Directory (AAD) kimlik doğrulamasına sahip olmalıdır. Kuruluşunuzdaki kullanıcı değişikliklerinden etkilenmemek için genel hesaplar oluşturup abonelik erişimini AAD ile yönetebilirsiniz.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Bir kullanıcı birden fazla aboneliğe sahip olabilir mi?

Bir Azure hesabı sahibi sınırsız sayıda abonelik oluşturup yönetebilir.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Kuruluşumun tüm aboneliklerine erişmek ve onları görüntülemek için ne yapabilirim?

Şu an için bu işlevin ilke yoluyla sağlanması gerekir. Başka bir deyişle hesabınızın oluşturulan her abonelikte rol tabanlı erişim kullanılarak bir abonelik rolüne eklenmesini sağlamanız gerekir.

### <a name="where-do-i-go-to-create-a-subscription"></a>Nereden abonelik oluşturabilirim?

Kurumsal Azure (EA) teklifi aboneliği oluşturabilmeniz için hesabınızın Azure EA Portal'da EA kayıt yöneticiniz tarafından hesap sahibi rolüne eklenmesi gerekir. Ardından EA teklif türü aboneliklerini oluşturma yetkisini elde etmek için Azure EA Portal'da oturum açmanız gerekir. İlk EA aboneliğinizin EA Portal'daki abonelik sekmesinde yer alan "+ Abonelik Ekle" bağlantısıyla oluşturulmasını öneririz.  Ancak hesabınız yetkilendirildikten sonra portal.azure.com adresindeki Abonelikler sekmesinin sol üst köşesinden hesap oluşturmak daha kolay olabilir. Burada tek adımda aboneliğinizi hem oluşturup hem de adını değiştirebilirsiniz.

### <a name="who-can-create-a-subscription"></a>Kimler abonelik oluşturabilir?

Kurumsal Azure teklifi türünde bir abonelik oluşturabilmek için [EA Portal](https://ea.azure.com)'da hesap sahibi rolüne sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal makine rezervasyonlarının](billing-ea-portal-vm-reservations.md) paradan tasarruf etmenize nasıl yardımcı olduğu hakkında bilgi edinin.
- Azure EA portalı sorunlarını gidermek için yardıma ihtiyacınız varsa [Azure EA portalı erişim sorunlarını giderme](billing-ea-portal-troubleshoot.md) konusuna bakın.
