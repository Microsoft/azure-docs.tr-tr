---
title: Azure EA portalı ile çalışmaya başlama
description: Bu makalede, Azure EA müşterilerinin Azure EA portalını nasıl kullanacağı açıklanmaktadır.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 567beb9de0c0a8039d774270bdf61a7db437091e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888355"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Azure EA portalı ile çalışmaya başlama

Bu makale, doğrudan ve dolaylı Azure EA müşterilerinin [Azure EA portalını](https://ea.azure.com) kullanmaya başlamasına yardımcı olur:

- Azure EA portalının yapılandırılması.
- Azure EA portalında kullanılan roller.
- Abonelikler oluşturmaya başlama.
- Azure EA portalında ve Azure portalında maliyetleri çözümleme.

Tam bir Azure EA portalı ekleme oturumu gösteren bir video aşağıda verilmiştir:

[Azure EA portalı ekleme videosu ](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Azure EA portalı hiyerarşisi

Azure EA portalı hiyerarşisi şunlardan oluşur:

**Microsoft Azure EA portalı** - Azure EA portalı, Azure EA hizmetleriniz için maliyetleri yönetmenize yardımcı olan bir çevrimiçi yönetim portalıdır. Departmanlar, hesaplar ve abonelikler içeren bir Azure EA hiyerarşisi oluşturmak için kullanılır. Ayrıca, tüketilen hizmetlerin maliyetlerini mutabık kılmak, kullanım raporlarını indirmek ve fiyat listelerini görüntülemek için de kullanabilirsiniz. Ayrıca, kaydınız için API anahtarları oluşturabilirsiniz.

**Departmanlar** - Maliyetleri mantıksal gruplar halinde segmentlere ayırmak ve sonra departman düzeyinde bir bütçe veya kota ayarlamak için departmanlar oluşturun.

**Hesaplar** – Hesaplar, Azure EA portalındaki kuruluş birimleridir ve abonelikleri yönetmek için kullanılır. Hesaplar raporlama için de kullanılır.

**Abonelikler** – Abonelikler, Azure EA portalındaki en küçük birimdir. Hizmet yöneticisi tarafından yönetilen Azure hizmetlerine yönelik kapsayıcılardır.

Aşağıdaki diyagramda basit Azure EA hiyerarşileri gösterilmektedir.

![Basit Azure EA hiyerarşileri diyagramı](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Kurumsal kullanıcı rolleri

Kaydınızdaki Azure hizmetlerini yönetmek için dört farklı kurumsal yönetici kullanıcı rolü vardır:

- Kuruluş yöneticisi
- Departman yöneticisi
- Hesap sahibi
- Hizmet yöneticisi

Roller iki farklı Microsoft Azure portalındaki görevleri gerçekleştirmek için kullanılır. Azure EA portalı (https://ea.azure.com) fatura ve maliyetleri yönetmenize yardımcı olmak için kullanılır. Azure portalı (https://portal.azure.com), Azure hizmetlerini yönetmek için kullanılır.

Kullanıcı rolleri bir kullanıcı hesabıyla ilişkilendirilir. Kullanıcının orijinalliğini doğrulamak için her kullanıcının geçerli bir İş, Okul veya Microsoft Hesabının olması gerekir. Her hesabın etkin bir şekilde izlenen bir e-posta adresiyle ilişkilendirildiğinden emin olun. Hesap bildirimleri e-posta adresine gönderilir.

Kullanıcıları ayarlarken, Kuruluş Yöneticisi rolüne birden fazla İş, Okul veya Microsoft hesabı atayabilirsiniz. Ancak, Hesap Sahibi rolüne yalnızca bir İş, Okul veya Microsoft hesabı atayabilirsiniz. Ayrıca, tek bir İş, Okul veya Microsoft hesabına hem Kuruluş Yöneticisi hem de Hesap Sahibi rolleri uygulanabilir.

### <a name="enterprise-administrator"></a>Kuruluş yöneticisi

Kuruluş yöneticisi rolü en yüksek erişim düzeyine sahiptir. Bu role sahip kullanıcılar şunları yapabilir:

- Hesapları ve hesap sahiplerini yönetme
- Diğer kuruluş yöneticilerini yönetme
- Departman yöneticilerini yönetme
- Bildirim kişilerini yönetme
- Tüm hesaplarda kullanımı görüntüleme
- Tüm hesaplarda faturalandırılmamış ücretleri görüntüleme

Bir kuruluş kaydında birden çok kuruluş yöneticiniz olabilir. Kurumsal yöneticilere salt okuma erişimi verebilirsiniz. Hepsi departman yöneticisi rolünü devralırlar.

### <a name="department-administrator"></a>Departman yöneticisi

Bu role sahip kullanıcılar şunları yapabilir:

- Departmanlar oluşturma ve yönetme
- Yeni hesap sahipleri oluşturma
- Yönettikleri departmanların kullanım ayrıntılarını görüntüleme
- Gerekli izinler verildiyse maliyetleri görüntüleme

Her kuruluş kaydı için birden çok departman yöneticiniz olabilir.

Departman yöneticilerine salt okuma erişimi verebilirsiniz. Salt okuma erişimi vermek için departman yöneticisini düzenleyin veya yeni bir tane oluşturun ve salt okunur seçeneğini **Evet** olarak ayarlayın.

### <a name="account-owner"></a>Hesap sahibi

Bu role sahip kullanıcılar şunları yapabilir:

- Abonelik oluşturma ve yönetme
- Hizmet yöneticilerini yönetme
- Aboneliklerin kullanımını görüntüleme

Her hesap için benzersiz bir İş, Okul veya Microsoft Hesabı gerekir. Azure EA Portalı yönetici rolleri hakkında daha fazla bilgi için bkz. [Azure’daki Azure Kurumsal Anlaşma yönetici rollerini anlama](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Hizmet yöneticisi

Hizmet yöneticisinin Azure portalındaki hizmetleri yönetme ve kullanıcıları eş yönetici rolüne atama izinleri vardır.

## <a name="activate-your-enrollment"></a>Kaydınızı etkinleştirme

Hizmetinizi etkinleştirmek için başlangıçtaki kuruluş yöneticisi [https://ea.azure.com](https://ea.azure.com) adresindeki Azure EA portalını açar ve davet e-postasındaki e-posta adresini kullanarak oturum açar.

Birden fazla kaydınız varsa etkinleştirilecek bir kayıt seçin. Varsayılan olarak, yalnızca etkin kayıtlar gösterilir. Kayıt geçmişini görüntülemek için, Azure EA portalının sağ üst köşesindeki **Etkin** seçeneğinin işaretini kaldırın.

Kayıt altında, durum **Etkin** olarak gösterilir.

![Etkin kaydı gösteren örnek](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Yalnızca mevcut Azure kuruluş yöneticileri, diğer kuruluş yöneticilerini oluşturabilir.

### <a name="create-another-enterprise-admin"></a>Başka bir kuruluş yöneticisi oluşturma

- [Azure EA portalında](https://ea.azure.com) oturum açıp **Yönet** > **Kayıt Ayrıntısı**’na gidin ve sayfanın sağ üst köşesindeki **+ Yönetici Ekle**’ye tıklayın.

Kullanıcının e-posta adreslerine ve İş veya Okul kimlik doğrulaması ya da Microsoft hesabı gibi tercih edilen kimlik doğrulama yöntemine sahip olduğunuzdan emin olun. Kullanıcı eklemek için bu bilgiler gereklidir.

EA yöneticisi değilseniz, sizi bir kayda eklemesini istemek için bir EA yöneticisiyle iletişime geçin. Bir kayda eklendikten sonra etkinleştirme e-postası alırsınız.

EA yöneticiniz size yardımcı olamıyorsa bir [Azure EA portalı destek isteği](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) oluşturun. Şu bilgileri belirtin:

- Kayıt numarası
- Eklenecek e-posta adresi ve kimlik doğrulama türü (İş, Okul veya Microsoft hesabı)
- Mevcut bir EA yöneticisinden e-posta onayı
  - Mevcut EA yöneticisine ulaşılamıyorsa, VLSC aracı üzerinden iletişim ayrıntılarını değiştirmesini istemek için iş ortağınız veya yazılım danışmanınızla iletişime geçin.

Kuruluş yöneticisi rolleri hakkında daha fazla bilgi için bkz. [Azure’daki Azure Kurumsal Anlaşma yönetici rollerini anlama](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Azure EA departmanı oluşturma

Kuruluş yöneticileri ve departman yöneticileri, kurumsal Azure hizmetlerini ve kullanımı departman ve maliyet merkezine göre düzenleyip raporlamak için departmanları kullanır. Kuruluş yöneticisi şunları yapabilir:

- Departman ekleme veya kaldırma
- Bir hesabı bir departmanla ilişkilendirme
- Departman yöneticileri oluşturma
- Departman yöneticilerinin fiyat ve maliyetleri görüntülemesine izin verme

Departman yöneticisi, departmanına yeni hesaplar ekleyebilir. Departmanlarından hesapları kaldırabilir, ancak kayıttan kaldıramaz.

Bir departman eklemek için:

1. Sol gezinti alanında **Yönet**'e tıklayın.
2. **Departman** sekmesine, ardından **+ Departman Ekle**’ye tıklayın ve sonra gerekli bilgileri girin.
3. Tek gerekli alan Departman adı’dır. En az 3 karakter uzunluğunda olmalıdır.
4. İşlem tamamlandığında **Ekle**’ye tıklayın.

## <a name="add-a-department-admin"></a>Departman yöneticisi ekleme

Bir departman oluşturulduktan sonra, Azure kuruluş yöneticisi departman yöneticileri ekleyebilir ve her birini bir departmanla ilişkilendirebilir. Departman yöneticisi şunları yapabilir:

- Diğer departman yöneticilerini oluşturma
- Ad veya maliyet merkezi gibi departman özelliklerini görüntüleme ve düzenleme
- Departmanları için hesap ekleme
- Departmanlarından hesap kaldırma
- Departmanlarının kullanım ayrıntılarını indirme
- Kuruluş yöneticisi tarafından izin verilmişse departmanlarına ilişkin aylık kullanım ve ücretleri görüntüleme <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Departman yöneticisi eklemek için

Kuruluş yöneticisi olarak:

1. Sol gezinti alanında **Yönet**'e tıklayın.
2. **Departman** sekmesine ve ardından departmana tıklayın.
3. **+ Yönetici Ekle**’ye tıklayıp gerekli bilgileri ekleyin.
4. Salt okuma erişimi için **Salt Okuma** seçeneğini **Evet** yapıp **Ekle**’ye tıklayın.

![Departman Yöneticisi Ekle iletişim kutusunu gösteren örnek](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Salt okuma erişimini ayarlamak için

Departman yöneticilerine salt okuma erişimi verebilirsiniz. Yeni bir departman yöneticisi oluştururken:

- Salt okuma seçeneğini **Evet** olarak ayarlayın.

Mevcut bir departman yöneticisini düzenlemek için:

1. Bir departman seçin ve ardından düzenlemek istediğiniz **Departman Yöneticisinin** yanındaki kurşun kalem simgesine tıklayın.
2. Salt okuma seçeneğini **Evet** olarak ayarlayın. ve sonra **Kaydet**'e tıklayın.

Kuruluş yöneticisi rolüne sahip kullanıcılar departman yöneticisi izinlerini otomatik olarak alır.

<sup>1</sup> Departmanın aylık kullanım ve ücretlerini görüntülemenize izin verilmesine rağmen görüntüleyemiyorsanız iş ortağınızla iletişime geçin.

## <a name="add-an-account"></a>Hesap ekleme

Hesap ve aboneliğin yapısı, bunların nasıl yönetildiğini ve faturalarınızda ve raporlarınızda nasıl göründüğünü etkiler. İşletme bölümlerine, görev ekiplerine ve coğrafi konumlara göre yapılandırma, tipik organizasyon örnekleridir.

Hesap eklemek için:

1. Azure EA portalında, sol gezinti alanındaki **Yönet**'e tıklayın.
2. **Hesap** sekmesine ve sonra **Hesap** sayfasındaki **+Hesap Ekle**’ye tıklayın.
3. Bir departman seçin veya atanmamış olarak bırakın ve sonra istediğiniz kimlik doğrulaması türünü seçin.
4. Raporlama sırasında hesabı tanımlamak için kullanılacak kolay bir ad yazın.
5. Yeni hesapla ilişkilendirilecek **Hesap Sahibi E-posta** adresini yazın.
6. E-posta adresini onaylayın ve **Ekle**'ye tıklayın.

![Hesapların listesini ve +Hesap Ekle seçeneğini gösteren örnek](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

**Başka Bir Hesap Ekle**’ye tıklayarak başka bir hesap ekleyebilir ya da soldaki araç çubuğunun sağ alt köşesindeki **Ekle**’ye tıklayabilirsiniz.

Hesap sahipliğini onaylamak için:

1. Azure EA portalında oturum açın.
1. Durumu görüntüleyerek hesap sahipliğini onaylayın. **Beklemede** olan durum **Başlangıç/Bitiş tarihi** olarak değişmelidir. Başlangıç/Bitiş tarihi, kullanıcının ilk kez oturum açtığı tarih ve anlaşma bitiş tarihidir.
1. Hesap sahibi Azure EA portalında ilk kez oturum açtığında açılan "Uyarı" iletisinde **Devam**'a tıklayarak hesabı etkinleştirmesi gerekir.


## <a name="change-account-owner"></a>Hesap sahibini değiştirme

Kuruluş yöneticileri bir kayıttaki abonelik hesap sahipliğini aktarmak için Azure EA portalını kullanabilir. Bu eylem, bir kaynak kullanıcı hesabındaki tüm abonelikleri hedef kullanıcı hesabına taşır.

Kullanıcı hesabı bilgilerini aktarma hakkında önemli noktalar:

- Bir İş veya Okul hesabından başka bir İş veya Okul hesabına yapılan aktarımlar desteklenir.
- Bir Microsoft hesabından İş veya Okul hesabına yapılan aktarımlar desteklenir.
- Bir İş veya Okul hesabından Microsoft hesabına yapılan aktarımlar desteklenmez.
- Microsoft hesabından Microsoft hesabına yapılan aktarımlar desteklenir. Hedef hesabın aktarımlara yönelik geçerli bir hedef olması için geçerli bir Azure Ticari hesabı olması gerekir. Yeni hesaplar için, Azure EA portalında oturum açarken bir Azure Ticari hesabı oluşturmanız istenir. Mevcut hesaplar için, hesabın uygun olabilmesi için yeni bir Azure aboneliği oluşturmanız gerekir.
- Abonelik aktarımını tamamladığınızda, Microsoft hesap sahibini güncelleştirir.

Rol tabanlı erişim denetimi ilkeleri:

- Mevcut Azure rol tabanlı erişim denetimi (RBAC) ilkelerini, hizmet yöneticisi rol atamalarını ve eş yönetici rol atamalarını yalnızca aynı kiracıdaki iki kuruluş kimliği arasında yapılan Azure aboneliği aktarımları korur. Diğer abonelik aktarımları, RBAC ilkelerinizin ve hizmet yöneticisi ile eş yönetici rol atamalarınızın kaybedilmesiyle sonuçlanır. İlkeler ve yönetici rolleri farklı dizinler arasında aktarılmaz. Hizmet yöneticileri hedef hesabın sahibiyle güncelleştirilir.
- Aynı kiracıdaki iki kuruluş kimliği arasında abonelik aktarımları gerçekleştirdiğinizde, RBAC ilkeleri ve mevcut hizmet yöneticisi ile eş yönetici rolleri korunur.

Hesap sahibini değiştirmeden önce:

1. **Hesap** sekmesini görüntüleyin ve kaynak hesabı belirleyin. Kaynak hesap etkin olmalıdır.
2. Hedef hesabı belirleyin. Bu hesap etkin olmalıdır.

Tüm aboneliklerin hesap sahipliğini aktarmak için:

1. Sol gezinti alanında **Yönet**'e tıklayın.
2. **Hesap** sekmesine tıklayın ve bir hesabın üzerine gelin.
3. Sağ taraftaki hesap sahibini değiştir simgesine tıklayın. Simge bir kişi gibi görünür.
4. Uygun bir hesap seçin ve ardından **İleri**'ye tıklayın.
5. Aktarımı onaylayın ve **Gönder**'e tıklayın.

![Hesap Sahibini Değiştir simgesini gösteren görüntü](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Tek bir aboneliğin hesap sahipliğini aktarmak için:

1. Sol gezinti alanında **Yönet**'e tıklayın.
2. **Hesap** sekmesine tıklayın ve bir hesabın üzerine gelin.
3. Sağdaki abonelikleri aktar simgesine tıklayın. Simge bir sayfa gibi görünür.
4. Uygun bir abonelik seçin ve ardından **İleri**'ye tıklayın.
5. Aktarımı onaylayın ve sonra **Gönder**'e tıklayın.

![Abonelikleri Aktar simgesini gösteren görüntü](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Azure EA portalı kullanıcı yönetimini gösteren bir video aşağıda verilmiştir:

[Azure EA portalı kullanıcı yönetimi videosu](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Abonelik oluşturma

Hesap sahipleri, abonelikleri görüntüleyebilir ve yönetebilir. Kuruluşunuzdaki takımlara geliştirme ortamları ve projelere yönelik erişim izni vermek için abonelikleri kullanabilirsiniz. Örneğin, test, üretim, geliştirme ve hazırlama. Her uygulama ortamı için farklı abonelikler oluşturduğunuzda her bir ortamın güvenliğini sağlamaya yardımcı olursunuz. Ayrıca, her abonelik için farklı bir hizmet yöneticisi hesabı da atayabilirsiniz. Abonelikleri dilediğiniz sayıda hizmet ile ilişkilendirebilirsiniz. Hesap sahibi, abonelikler oluşturur ve hesaplarındaki her aboneliğe bir hizmet yöneticisi hesabı atar.

### <a name="add-a-subscription"></a>Abonelik ekleme

Bir abonelik eklemek için aşağıdaki bilgileri kullanın.

Hesabınıza ilk kez bir abonelik eklediğinizde, MOSA sözleşmesini ve bir ücret planını kabul etmeniz istenir. Kurumsal Anlaşma müşterileri için geçerli olmasa da, aboneliğinizin oluşturulması için gerekir. Microsoft Azure Kurumsal Anlaşma Kaydı Değişikliği, yukarıdaki öğelerin yerini alır ve sözleşme ilişkiniz değişmez. Sorulduğunda, koşulları kabul ettiğinizi belirten kutuyu seçin.

Tüm yeni abonelikler _Microsoft Azure Kurumsal_ varsayılan abonelik adı ile oluşturulur. Abonelik adını, kaydınızdaki diğer aboneliklerden ayırt etmek için güncelleştirebilirsiniz. Ayrıca, kurumsal düzeydeki raporlarda ayırt edilebildiğinden emin olun.

Abonelik eklemek için:

1. Azure EA portalında hesabınızla oturum açın.
2. **Yönetici** sekmesine ve sonra sayfanın üst kısmındaki **Abonelik**’e tıklayın.
2. Hesabın hesap sahibi olarak oturum açtığınızı doğrulayın.
3. **+Abonelik Ekle** ve ardından **Satın Al**’a tıklayın.
  Bir hesaba ilk kez abonelik eklediğinizde iletişim bilgilerinizi sağlamanız gerekir. Başka abonelikler eklerken, iletişim bilgileriniz sizin için eklenir.
4. **Abonelikler**’e tıklayın, ardından oluşturduğunuz aboneliği seçin ve sonra **Abonelik Ayrıntılarını Düzenle**’ye tıklayın.
5. **Abonelik Adı** ve **Hizmet Yöneticisi**’ni güncelleştirip onay işaretini seçin.
  Abonelik adı raporlarda görünür ve geliştirme portalındaki abonelikle ilişkili projenin adıdır.

Yeni aboneliklerin abonelikler listesinde görünmesi 24 saate kadar sürebilir. Bir abonelik oluşturduktan sonra şunları yapabilirsiniz:

- [Abonelik ayrıntılarını düzenleme](https://account.azure.com/Subscriptions)
- [Abonelik hizmetlerini yönetme](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>EA aboneliğini kullandıkça öde aboneliğine aktarma

Bir EA aboneliğini kullandıkça öde fiyatlarına sahip bir aboneliğe aktarmak için Azure EA portalında yeni bir destek isteği oluşturmanız gerekir. Destek isteği oluşturmak için Yardım ve Destek alanındaki **+ Yeni destek isteği**'ne tıklayın.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Mevcut bir hesabı kullandıkça öde aboneliğiyle ilişkilendirme

Microsoft Azure portalında bir Microsoft Azure hesabınız varsa bunu Kurumsal Anlaşma kaydınızla ilişkilendirmek için ilgili Microsoft hesabını ya da iş veya okul hesabını girin.

### <a name="associate-an-existing-account"></a>Mevcut bir hesabı ilişkilendirme

1. Enterprise Portal'da **Yönet**'e tıklayın.
1. **Hesap** sekmesine tıklayın.
1. **+Hesap ekle**'ye tıklayın.
1. Mevcut hesapla ilişkilendirilmiş olan Microsoft hesabını ya da iş veya okul hesabını girin.
1. Mevcut hesapla ilişkilendirilmiş olan Microsoft hesabını ya da iş veya okul hesabını doğrulayın.
1. Bu hesabı raporlarda tanımlayabilmek için kullanmak istediğiniz adı yazın.
1. **Ekle**'ye tıklayın.
1. **+Hesap Ekle** seçeneğini kullanarak başka bir hesap ekleyebilir veya **Yönetim** düğmesini seçerek giriş sayfasına dönebilirsiniz.
1. **Hesap** sayfasını görüntülemek için tıkladığınızda yeni hesabın **Beklemede** olduğunu görürsünüz.

### <a name="confirm-account-ownership"></a>Hesap sahipliğini onaylama

1. Girdiğiniz Microsoft hesabı ya da iş veya okul hesabıyla ilişkilendirilmiş olan e-posta hesabında oturum açın.
1. _"Microsoft Toplu Lisanslama'dan Microsoft Azure Hizmeti Hesabınızı Etkinleştime Daveti"_ başlıklı e-posta bildirimini açın.
1. Davetin içindeki **Microsoft Azure Enterprise Portal'da oturum aç** bağlantısına tıklayın.
1. **Oturum aç**’a tıklayın.
1. Oturum açmak ve hesabın sahibi olduğunuzu doğrulamak için Microsoft hesabınızı ya da iş veya okul hesabınızı ve parolanızı girin.

### <a name="azure-marketplace"></a>Azure Market

Çoğu abonelik Kullandıkça Öde ortamından Enterprise Azure'a dönüştürülebilir ancak Azure Market bunlardan biri değildir. Tüm abonelikleri ve ücretleri tek bir yerden görüntülemek için Azure Market hizmetlerini Enterprise Portal'a eklemenizi öneririz:

1. Sol gezinti panelinde **Yönet**’e tıklayın.
1. **Kayıt** sekmesine tıklayın.
1. Kayıt Ayrıntıları bölümünü görüntüleyin.
1. Azure Market alanının sağ tarafında yer alan kalem simgesine tıklayıp hizmeti etkinleştirin ve **Kaydet**'e tıklayın.

Hesap sahibi artık daha önce kullandıkça öde modeliyle sahip olduğu Azure Market aboneliklerini satın alabilir.

Yeni Azure Market abonelikleri kaydınızda etkinleştirildikten sonra kullandıkça öde ortamında oluşturulan Market aboneliklerini iptal edin. Kullandıkça öde ortamındaki ödeme aracınızın süresi dolduğunda Market aboneliklerinizin kullanım dışı kalmaması için bu adım kritik öneme sahiptir.

### <a name="msdn"></a>MSDN

MSDN abonelikleri otomatik olarak MSDN Geliştirme ve Test aboneliğine dönüştürülür ve EA teklifindeki mevcut parasal kredi silinir.

### <a name="azure-in-open"></a>Open ile Azure

Bir Open ile Azure aboneliğini bir EA ile ilişkilendirmek, tüketilmemiş tüm Open ile Azure kredilerinin kaybedilmesine yol açar. Olası kredi kaybından kaçınmak için müşterilerin bir Open ile Azure aboneliğini EA’ya eklemeden önce hesaptaki tüm kredileri tüketmesi önerilir.  

### <a name="accounts-with-support-subscriptions"></a>Destek aboneliğine sahip hesaplar

Enterprise Portal'a destek aboneliği bulunan (ve EA Destek aboneliği bulunmayan) mevcut hesapları eklerken MOSA desteği aboneliğinin otomatik olarak aktarılmadığını ve destek hizmetinin EA ortamında yeniden satın alınması gerekeceğini lütfen unutmayın. Destek hizmetinin yeniden sipariş edilmesi için zaman tanıma amacıyla destek hizmetlerinin süresi sonraki ayın sonuna kadar uzatılacaktır.

## <a name="view-usage-summary-and-download-reports"></a>Kullanım özetini görüntüleme ve raporları indirme

Kuruluş yöneticileri Azure EA portalında kullanım verilerinin özetini, tüketilen parasal taahhüdü ve ek kullanımla ilişkili ücretleri görüntüleyebilir. Ücretler, tüm hesaplar ve abonelikler genelinde özet düzeyinde gösterilir.

Belirli hesapların ayrıntılı kullanımını görüntülemek için:

Kullanım Ayrıntısı raporunu indirin. **Raporlar** ve ardından **Kullanımı İndir** sekmesine tıklayın. Rapor listesinde, almak istediğiniz aylık rapor için **İndir**'e tıklayın.

Rapor hiçbir geçerli vergiyi içermez. Kullanımın tahakkuk etmesiyle rapora yansıtılması arasında sekiz saatlik bir gecikme olabilir.

Kullanım özeti raporlarını ve grafiklerini görüntülemek için:

1. Azure EA portalındaki sol gezinti alanında **Raporlar**’a tıklayın ve **Kullanım Özeti** sekmesini görüntüleyin.  
  ![Kullanım özetini oluşturup görüntüleme ve raporları indirme](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Bir taahhüt dönemi seçin.
3. Sayfanın sağ üst kısmındaki **A** (Aylık) ve **Ö** (Özel) seçenekleri arasında geçiş yaparak özel başlangıç ve bitiş tarihleri ile **Kullanım Özeti**’ni görüntüleyin.  
  ![Özel görünümde kullanım özetini oluşturup görüntüleme ve raporları indirme](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Ek ayrıntıları görüntülemek için grafikte bir dönem veya ay seçin.
5. Grafik, kullanılan kullanım, fazla servis ücreti, ayrı olarak faturalandırılan ücretler ve market ücretlerinin dökümü ile aylık kullanım üzerinden ayı gösterir.
6. Seçilen ay için grafiğin altından departmanlara, hesaplara ve aboneliklere göre filtreleyin.
7. **Hizmetlere Göre Ücret** ve **Hiyerarşiye Göre Ücret** arasında geçiş yapın.
8. Ayrıntıları görüntülemek için **Azure Hizmeti**, **Ayrı Olarak Faturalandırılan Ücretler** ve **Azure Market** seçenekleri genişletip daraltın.

Kullanımının nasıl görüntüleneceğini gösteren bir videoyu aşağıda bulabilirsiniz:

[Azure EA portalı kullanım videosu](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV raporlarını indirme

Kuruluş Yöneticileri, birkaç raporu CSV dosyası olarak indirmek için Aylık Rapor İndirme sayfasını kullanır. Şunları içerir:

- Bakiye ve Ücret
- Kullanım Ayrıntısı
- Market Ücretleri
- Fiyat Listesi

Raporları indirmek için:


1. Azure EA portalında **Raporlar**’a tıklayın.
2. Sayfanın üst kısmındaki **Kullanımı İndir**'e tıklayın.
3. Ay raporunun yanındaki **İndir**'i seçin.

Tahakkuk eden kullanım tarihi ile kullanımın raporlarda gösterilmesi arasında beş güne kadar gecikme olabilir.

Safari ile Excel'e CSV dosyalarını indiren kullanıcılar biçimlendirme hatalarıyla karşılaşabilir. Hataları önlemek için dosyayı bir metin düzenleyicisi kullanarak açın.

![Kullanımı İndir sayfasını gösteren örnek](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Kullanım bilgilerinin nasıl indirileceği ile ilgili bir video aşağıda verilmiştir:

[Azure EA portalı kullanım videosu](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Gelişmiş rapor indirme

Belirli tarih aralıklarına veya hesaplara göre rapor oluşturmak için gelişmiş rapor indirme işlevini kullanabilirsiniz. Daha geniş kayıt kümelerine yer verebilme amacıyla çıkış dosyasının biçimi 30 Ağustos 2016 tarihinden itibaren .xlsx yerine .csv olarak değiştirilmiştir.

1. **Gelişmiş Rapor İndirme**'yi seçin.
1. **Uygun Tarih Aralığı**'nı seçin.
1. **Uygun Hesaplar**'ı seçin.
1. **Kullanım Verilerini İste**'yi seçin.
1. Rapor durumu **İndir** olana kadar **Yenile** düğmesini seçin.
1. Raporu indirin.

## <a name="ea-term-glossary"></a>EA terimleri sözlüğü

- **Hesap**: Azure EA Portal'da abonelikleri yönetmek ve raporlama için kullanılan bir kuruluş birimidir.
- **Hesap sahibi**: Microsoft Azure'daki abonelikleri ve hizmet yöneticilerini yöneten kişidir. Bu kullanıcılar hesapla ve ilişkilendirilmiş aboneliklerle ilgili kullanım verilerini görüntüleyebilir.
- **Değişiklik aboneliği**: Kayıt değişikliği kapsamında bir yıllık veya uyuşan dönem boyunca devam eden aboneliktir.
- **Taahhüt**: Microsoft Azure hizmetlerinin ön ödeme karşılığında kullanılması için taahhüt edilmiş indirimli ücretlerden karşılanması amacıyla gerçekleştirilen yıllık parasal tutar taahhüttür.
- **Bölüm yöneticisi**: Bölümleri yönetmek, yeni hesap ve hesap sahibi oluşturmak, yönetilen bölümlerle ilgili kullanım ayrıntılarını görüntülemek ve izin verildiğinde maliyetleri görüntülemek için görevlendirilen kişidir.
- **Kayıt numarası**: Microsoft tarafından kurumsal anlaşma ile ilişkilendirilmiş olan kaydı tanımlamak için verilen benzersiz tanıtıcıdır.
- **Kuruluş yöneticisi**: Microsoft Azure'daki bölümler ve bölüm yöneticileri ile hesapları ve hesap yöneticilerini yönetmek için görevlendirilen kişidir. Kuruluş yöneticileri, diğer kuruluş yöneticilerini de yönetmenin yanı sıra kurumsal kayıtla ilişkilendirilmiş olan tüm hesaplara ve aboneliklere ait olan kullanım verilerini, faturalandırılan miktarları ve faturalandırılmamış ücretleri görüntüleyebilir.
- **Kurumsal anlaşma**: Kuruluşlarının tamamında Microsoft teknolojilerini standart hale getirmek ve Microsoft yazılım standardını temel alan bir bilgi teknolojisi altyapısı kurmak isteyen, merkezi satın alma sistemine sahip olan müşterilere yönelik bir Microsoft lisans sözleşmesidir.
- **Kurumsal anlaşma kaydı**: Microsoft ürünlerini toplu olarak ve indirimli fiyatlarla sunan kurumsal anlaşma programı kaydıdır.
- **Microsoft hesabı**: Katılan sitelerin kullanıcının kimliğini tek bir kimlik bilgisi ile doğrulamasını sağlayan web tabanlı bir hizmettir.
- **Microsoft Azure kurumsal kayıt değişikliği (kayıt değişikliği)** : Bir kuruluş tarafından imzalanan ve kurumsal kayıt çerçevesinde Microsoft Azure erişimi sağlayan değişikliktir.
- **Azure EA Portal**: Kurumsal müşterilerimizin Microsoft Azure hesaplarını ve ilişkili aboneliklerini yönetmek için kullandığı portaldır.
- **Kullanılan kaynak miktarı**: Bir ay içinde kullanılan tek bir Microsoft Azure hizmetinin miktarıdır.
- **Hizmet yöneticisi**: Azure EA Portal'daki aboneliklere ve geliştirme projelerine erişebilen ve bunları yönetebilen kişidir.
- **Abonelik**: Bir Azure EA Portal aboneliğini temsil eder ve aynı hizmet yöneticisi tarafından yönetilen Microsoft Azure hizmetlerini içeren bir kapsayıcıdır.
- **İş veya okul hesabı**: Buluta Federasyon ile Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlara yöneliktir.

### <a name="enrollment-statuses"></a>Kayıt durumları:

- **Beklemede**: Kayıt yöneticisinin Azure EA Portal'da oturum açması gerekir. Oturum açıldıktan sonra kayıt "Etkin" duruma geçer.
- **Etkin**: Kayıt "Etkin" durumdadır, Azure EA Portal'da hesaplar ve abonelikler oluşturulabilir. Kurumsal anlaşmanın bitiş tarihine kadar kayıt etkin durumda kalır.
- **Süresiz uzatılan süre**: Süresiz uzatılan süre durumu, kurumsal anlaşma bitiş tarihi geçtikten sonra gerçekleşir. Uzatılan süreyi kabul eden EA müşterilerinin Azure'ı kurumsal anlaşmaları bitene kadar sınırsız bir şekilde kullanmalarını sağlar. EA kaydı kurumsal anlaşma bitiş tarihine ulaşmadan önce kayıt yöneticisi ek parasal taahhüt ekleyerek kaydı yenileme, yeni bir kayda aktarma, Microsoft Çevrimiçi Abonelik Programı'na (MOSP) geçme veya kayıtla ilişkilendirilmiş olan tüm hizmetlerin devre dışı bırakılmasını onaylama seçeneklerinden birini tercih etmelidir.
- **Süresi Doldu**: EA müşterisi uzatılan süreyi kabul etmemiş ve EA kaydı kurumsal anlaşma bitiş tarihine gelmiştir, kaydın süresi dolacak ve ilgili tüm hizmetler devre dışı bırakılacaktır.
- **Aktarıldı**: İlişkilendirilmiş hesapların ve hizmetlerin yeni bir kayda aktarıldığı kayıtların durumu "Aktarıldı" olarak görünür. Yenileme sırasında yeni bir kayıt numarası oluşturulduysa kayıtların otomatik olarak aktarılmayacağını lütfen unutmayın. Otomatik aktarım için müşterinin yenileme belgelerine önceki kayıt numarasını eklemesi gerekir.

## <a name="get-started-on-azure-ea-faq"></a>Azure EA ile çalışmaya başlama hakkında SSS

Bu belgede, katılım sürecinde müşteriler tarafından sorulan tipik sorulara yer verilmiştir.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Mevcut Azure hesabımı Kurumsal Kaydımla ilişkilendirebilir miyim?

Evet, bunu yapabilirsiniz. Buradaki önemli nokta, hesap sahibi olduğunuz tüm Azure aboneliklerinin kurumsal sözleşmenize dönüştürülecek olmasıdır. Buna aylık kredi kullanan abonelikler (Visual Studio, AzurePass, MPN, BizSpark gibi) de dahildir ve bu geçiş sonrasında aylık kredileri kullanamazsınız.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Mevcut Azure hesabımı yanlışlıkla Kurumsal kayıtla ilişkilendirdim. Bunun sonucunda aylık kredimi kaybettim. Aylık kredimi geri alabilir miyim?

EA hesap sahibi olarak kimliğinizi doğruladıktan ve EA için Visual Studio aboneliğinizde kullandığınız oturum açma bilgilerini kullandıktan sonra bireysel Visual Studio aboneliği Azure avantajınızı geri almak için şunlardan birini yapmanız gerekir:
1. Sahip olduğu Azure aboneliklerini kaldırdıktan veya taşıdıktan sonra bu hesap sahibini EA Portal'dan silin ve Visual Studio Azure avantajlarına yeniden kaydolmasını isteyin.
 OR
1. VLSC'deki Yönetim sitesinden Visual Studio abonesini silin ve bu kez farklı oturum açma bilgileri kullanmasını isteyin. Bu kullanıcı Visual Studio Azure avantajları için yeniden kaydolabilir.

### <a name="what-type-of-subscription-should-i-create"></a>Ne tür bir abonelik oluşturmalıyım?

EA Portal, kurumsal müşteriler için iki tür abonelik sunar:

- Microsoft Azure Kurumsal; şu kullanımlar için idealdir:
  - Her türlü üretim amaçlı kullanımlar
  - Altyapı harcamasına göre en iyi fiyatlar
  - Daha fazla bilgi edinmek için bkz. https://azure.microsoft.com/pricing/enterprise-agreement/
- Kurumsal Geliştirme ve Test; şu kullanımlar için idealdir:
  - Ekiple yürütülen tüm geliştirme ve test iş yükleri
  - Orta ve ağır düzeyde bireysel geliştirme/test iş yükleri
  - Özel MSDN görüntülerine erişim ve tercihe bağlı hizmet ücretleri
  - Daha fazla bilgi edinmek için bkz. https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Aboneliğin sahipliği başka bir hesaba aktarılabilir mi?

Evet, aboneliğin sahipliği başka bir hesaba aktarılabilir. Örneğin A hesabında üç abonelik varsa kuruluş yöneticisi bu aboneliklerden birini B hesabına, birini C hesabına, diğerini de D hesabına veya hepsini birden E hesabına aktarabilir.

EA Portal'a gidip Yönet > Hesap yolunu izleyin, **Hesap** (sağ üst köşede) öğesinin üzerine gidin. Sahipliği Aktar (kafa simgesi) ve Aboneliği Aktar (liste simgesi) seçeneklerini göreceksiniz.

Bu seçenek yalnızca etkin hesaplarda görünür.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Abonelik adı, varsayılan olarak teklif adı şeklinde ayarlandı, aboneliğin adını kuruluşum için daha anlamlı olacak bir adla değiştirmeli miyim?

Oluşturulan abonelikler varsayılan olarak seçtiğiniz teklif türünün adını kullanır. Daha kolay takip edebilmeniz için aboneliğin adını daha anlamlı olacak şekilde değiştirmeniz önerilir.

**Adı değiştirmek için:**
1. [https://account.windowsazure.com](https://account.windowsazure.com) adresinde oturum açın.
1. Abonelik listesine tıklayın.
1. Aboneliği seçin.
1. **Aboneliği Yönet** simgesine tıklayın.
1. Abonelik ayrıntılarını düzenleyin.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Maliyetleri Maliyet Merkezine göre nasıl izleyebilirim?

Maliyetleri Maliyet Merkezine göre izlemek için şu düzeylerden birinde Maliyet Merkezi tanımlamanız gerekir:
- Bölüm
- Hesap
- Abonelik

Gereksinimlerinize bağlı olarak belirli bir Maliyet Merkezi ile ilişkilendirilmiş olan kullanımı ve maliyeti izlemek için aynı Maliyet Merkezini kullanabilirsiniz.

Örneğin birden fazla bölümün dahil olduğu özel bir projenin maliyetini izlemek için kullanım ve maliyet takibini abonelik düzeyinde Maliyet Merkezi kullanarak yapabilirsiniz.

Kullanımı hizmet düzeyinde izlemek istiyorsanız Maliyet Merkezini Hizmet düzeyinde tanımlayamazsınız ancak hizmet düzeyindeki "Etiket" özelliğini kullanabilirsiniz.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Kuruluşumun farklı bölümleri tarafından gerçekleştirilen kullanımları ve harcamaları nasıl izleyebilirim?

EA kaydı çerçevesinde istediğiniz kadar bölüm oluşturabilirsiniz. Kullanımı doğru şekilde izlemek için aboneliklerin birden fazla bölüm arasında paylaşılmadığından emin olmanız gerekir.

Bölüm ve abonelik oluşturma işlemlerini tamamladıktan sonra kullanım raporuna bölüm düzeyinde kullanımı izlemenize ve maliyet/harcama yönetimi yapmanıza yardımcı olacak bilgilerin eklendiğini görebilirsiniz.

Ayrıca kullanım verilerine API ile de erişebilirsiniz. Ayrıntılı bilgiler ve örnek kod için [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI) adresini inceleyin.

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Harcama kotası ayarlayabilir ve belirlediğim limite yaklaştığımda uyarı gönderilmesini sağlayabilir miyim?

Bölüm düzeyinde harcama kotası ayarlayabilirsiniz. Harcama limitleriniz tanımladığınız kotanın %50, %75, %90 ve %100'üne ulaştığında sistem otomatik bildirim gönderir.

Harcama kotanızı tanımlamak için harcama limiti eklemek istediğiniz bölüme ve ardından düzenleme simgesine tıklayın. Ayrıntıları kaydetmek için **Kaydet**'e tıklayın.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>RBAC uygulamak ve kullanımı izlemek için Kaynak Gruplarını (RG) kullandım, ilgili kullanım ayrıntılarını nasıl görüntüleyebilirim?

"Kaynak Grupları" ve "Etiket" gibi bilgiler kullanılması durumunda hizmet düzeyinde izlenir ve bilgiler, ayrıntılı kullanımı indirme (CSV) dosyasına yazılır. Bu dosyayı Azure EA Portal'da [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage) adresinden indirebilirsiniz.

Ayrıca kullanım verilerine API ile de erişebilirsiniz. Ayrıntılı bilgiler ve örnek kod için [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI) adresini inceleyin.

Etiketlerin yalnızca Resource Manager işlemlerini destekleyen kaynaklara uygulanabildiğini lütfen unutmayın. Klasik dağıtım modeli (klasik portal gibi) ile oluşturduğunuz sanal makine, sanal ağ veya depolama alanı gibi kaynaklara etiket uygulayamazsınız. Etiketleme desteği eklemek için bu kaynakları Resource Manager üzerinden yeniden dağıtmanız gerekir. Diğer tüm kaynaklar etiketlemeyi destekler.

### <a name="can-i-perform-analyses-using-power-bi"></a>Power BI kullanarak analiz yapabilir miyim?

Evet. Power BI için Microsoft Azure Kurumsal içerik paketi ile gerçekleştirmiş olduğunuz kurumsal kayıt anlaşmanıza ilişkin Azure kullanımınızı hızlı bir şekilde içeri aktarabilir ve çözümleyebilirsiniz. En çok kullanımı hangi departmanın, hesabın veya aboneliğin gerçekleştirdiğini ve kuruluşunuzun en çok hangi hizmeti kullandığını öğrenin veya harcama ve kullanım eğilimlerini izleyin.

**Power BI web sitesine gidin:**

 1. Geçerli bir iş veya okul hesabıyla oturum açın.
    - Bu iş veya okul hesabı, Azure EA Portal üzerinden kayda erişmek için kullanılan hesapla aynı veya ondan farklı olabilir.
 1. Hizmet panosunda şunları seçin:
    - Microsoft Azure Kurumsal kutucuğu.
    - **Bağlan**'a tıklayın.
 1. "Azure Enterprise'a Bağlan" ekranında şunu seçin:
    - Azure Ortamı URL'si: [https://ea.azure.com](https://ea.azure.com).
    - Ay Sayısı: 1 ile 36 arasında bir değer seçin.
    - Kayıt Numarası: Kayıt numarasını girin.
    - **İleri**’ye tıklayın.
 1. Kimlik Doğrulama Anahtarı kutusuna API Anahtarını girin. Buraya girmeniz gereken API anahtarını Azure EA Portal'ın "Kullanımı İndir" sekmesinde **API Erişim Anahtarı**na tıklayarak alabilirsiniz.
    - Anahtarı kopyalayıp "Hesap Anahtarı" kutusuna yapıştırın.
    - Veri kümesinin boyutuna bağlı olarak verilerin Power BI'a yüklenmesi 5-30 dakika arasında sürebilir.

Power BI raporlarını EA doğrudan, iş ortağı ve fatura bilgilerini görüntüleme yetkisine sahip olan dolaylı müşteriler kullanabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure EA portalı yöneticilerinin yaygın yönetim görevleri hakkında bilgi edinmek için [Azure EA portalı yönetimi](billing-ea-portal-administration.md) makalesini okumaları gerekir.
- Azure EA portalı sorunlarını gidermek için yardıma ihtiyacınız varsa [Azure EA portalı erişim sorunlarını giderme](billing-ea-portal-troubleshoot.md) konusuna bakın.
- Azure EA katılım kılavuzu için bkz. [Azure EA Katılım Kılavuzu](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
