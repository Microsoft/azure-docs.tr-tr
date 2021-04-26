---
title: Azure EA portalı yönetimi
description: Bu makalede, bir yöneticinin Azure EA portalında gerçekleştirdiği genel görevler açıklanmaktadır.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 787f902311437e6ca8a679299ef7a28b34cee4ab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728419"
---
# <a name="azure-ea-portal-administration"></a>Azure EA portalı yönetimi

Bu makalede, bir yöneticinin Azure EA portalında gerçekleştirdiği genel görevler açıklanmaktadır (https://ea.azure.com). Azure EA portalı, müşterilerin Azure EA hizmetlerinin maliyetini yönetmesine yardımcı olan bir çevrimiçi yönetim portalıdır. Azure EA portalı hakkında giriş bilgileri için [Azure EA portalı ile çalışmaya başlama](ea-portal-get-started.md) makalesini okuyun.

## <a name="activate-your-enrollment"></a>Kaydınızı etkinleştirme

Hizmetinizi etkinleştirmek için başlangıçtaki kuruluş yöneticisi [Azure Enterprise Portal](https://ea.azure.com)'ı açar ve davet e-postasındaki e-posta adresini kullanarak oturum açar.

Kuruluş yöneticisi olarak belirlendiyseniz etkinleştirme e-postasını almanız gerekmez. [Azure Enterprise Portal](https://ea.azure.com)'a giderek iş, okul veya Microsoft hesabı e-posta adresinizle ve parolanızla oturum açabilirsiniz.

Birden fazla kaydınız varsa etkinleştirilecek bir kayıt seçin. Varsayılan olarak, yalnızca etkin kayıtlar gösterilir. Kayıt geçmişini görüntülemek için, Azure Enterprise Portal'ın sağ üst köşesindeki **Etkin** seçeneğinin işaretini kaldırın.

**Kayıt** altında, durum **Etkin** olarak gösterilir.

![Etkin kaydı gösteren örnek](./media/ea-portal-administration/ea-enrollment-status.png)

Yalnızca mevcut Azure kuruluş yöneticileri, diğer kuruluş yöneticilerini oluşturabilir.

### <a name="create-another-enterprise-administrator"></a>Başka bir kuruluş yöneticisi oluşturma

Durumunuza göre aşağıdaki seçeneklerden birini kullanın.

#### <a name="if-youre-already-an-enterprise-administrator"></a>Zaten bir kuruluş yöneticisiyseniz

1. [Azure Enterprise Portal](https://ea.azure.com)'da oturum açın.
1. **Yönet** > **Kayıt Ayrıntıları**'na gidin.
1. Sağ üstten **+ Yönetici Ekle**'yi seçin.

Kullanıcının e-posta adresine ve iş, okul veya Microsoft hesabı gibi tercih edilen kimlik doğrulama yöntemine sahip olduğunuzdan emin olun.

#### <a name="if-youre-not-an-enterprise-administrator"></a>Kurumsal Yönetici değilseniz

Kurumsal Yönetici değilseniz, sizi bir kayıt için eklemesini istemek üzere bir kurumsal yöneticiye başvurun. Kurumsal Yönetici, sizi Kurumsal Yönetici olarak eklemek için önceki adımları kullanır. Bir kayda eklendikten sonra etkinleştirme e-postası alırsınız.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>Kuruluş yöneticiniz size yardımcı olamaz

Kuruluş yöneticiniz size yardımcı olamıyorsa bir [Azure Enterprise Portal destek isteği](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) oluşturun. Şu bilgileri belirtin:

- Kayıt numarası
- Eklenecek e-posta adresi ve kimlik doğrulama türü (iş, okul veya Microsoft hesabı)
- Mevcut bir kuruluş yöneticisinden e-posta onayı
  - Mevcut kuruluş yöneticisine ulaşılamıyorsa, Toplu Lisanslama Hizmeti Merkezi (VLSC) aracı üzerinden iletişim ayrıntılarını değiştirmesini istemek için iş ortağınız veya yazılım danışmanınızla iletişime geçin.

## <a name="create-an-azure-enterprise-department"></a>Azure Kurumsal departmanı oluşturma

Kuruluş yöneticileri ve departman yöneticileri, kurumsal Azure hizmetlerini ve kullanımı departman ve maliyet merkezine göre düzenleyip raporlamak için departmanları kullanır. Kuruluş yöneticisi şunları yapabilir:

- Departman ekleme veya kaldırma.
- Bir hesabı bir departmanla ilişkilendirme.
- Departman yöneticileri oluşturma.
- Departman yöneticilerinin fiyat ve maliyetleri görüntülemesine izin verme.

Departman yöneticisi, departmanına yeni hesaplar ekleyebilir. Departmanlarından hesapları kaldırabilir, ancak kayıttan kaldıramaz.

Bir departman eklemek için:

1. Azure Enterprise Portal'da oturum açın.
1. Sol bölmede **Yönet**’i seçin.
1. **Departman** sekmesinde **+ Departman Ekle**'yi seçin.
1. Bilgileri girin.
   Tek gerekli alan departman adıdır. En az 3 karakter uzunluğunda olmalıdır.
1. Bittiğinde **Ekle**’yi seçin.

## <a name="add-a-department-administrator"></a>Departman yöneticisi ekleme

Bir departman oluşturulduktan sonra, kuruluş yöneticisi departman yöneticileri ekleyebilir ve her birini bir departmanla ilişkilendirebilir. Departman yöneticileri, kendi departmanlarında aşağıdaki eylemleri gerçekleştirebilir:

- Diğer departman yöneticilerini oluşturma
- Ad veya maliyet merkezi gibi departman özelliklerini görüntüleme ve düzenleme
- Hesap ekleme
- Hesapları kaldırma
- Kullanım ayrıntılarını indirme
- Aylık kullanımı ve ücretleri görüntüleme <sup>1</sup>

> <sup>1</sup> Kuruluş yöneticisi bu izinleri vermiş olmalıdır. Departmanın aylık kullanım ve ücretlerini görüntülemenize izin verilmesine rağmen görüntüleyemiyorsanız iş ortağınızla iletişime geçin.

### <a name="to-add-a-department-administrator"></a>Departman yöneticisi eklemek için

Kuruluş yöneticisi olarak:

1. Azure Enterprise Portal'da oturum açın.
1. Sol bölmede **Yönet**’i seçin.
1. **Departman** sekmesini ve ardından istediğiniz departmanı seçin.
1. **+ Yönetici Ekle**’yi seçip gerekli bilgileri ekleyin.
1. Salt okuma erişimi için **Salt Okuma** seçeneğini **Evet** yapıp **Ekle**’yi seçin.

![Departman Yöneticisi Ekle iletişim kutusunu gösteren örnek](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Salt okuma erişimini ayarlamak için

Departman yöneticilerine salt okuma erişimi verebilirsiniz.

- Yeni bir departman yöneticisi oluşturduğunuzda salt okuma seçeneğini **Evet** olarak ayarlayın.

- Mevcut bir departman yöneticisini düzenlemek için:
   1. Bir departman seçin ve ardından düzenlemek istediğiniz **Departman Yöneticisinin** yanındaki kalem simgesini seçin.
   1. Salt okuma değerini **Evet** olarak ayarlayıp **Kaydet**'i seçin.

Kuruluş yöneticileri, departman yöneticisi izinlerini otomatik olarak alır.

## <a name="add-an-account"></a>Hesap ekleme

Hesapların ve aboneliklerin yapısı, bunların nasıl yönetildiğini ve faturalarınızda ve raporlarınızda nasıl göründüğünü etkiler. İşletme bölümlerine, görev ekiplerine ve coğrafi konumlara göre yapılandırma, tipik organizasyon örnekleridir.

Hesap eklemek için:

1. Azure Enterprise Portal'ın sol tarafındaki gezinti alanında **Yönet**'i seçin.
1. **Hesap** sekmesini seçin. **Hesap** sayfasında **+Hesap Ekle**'yi seçin.
1. Bir departman seçin veya atanmamış olarak bırakın ve sonra istediğiniz kimlik doğrulaması türünü seçin.
1. Raporlama sırasında hesabı tanımlamak için kullanılacak kolay bir ad girin.
1. Yeni hesapla ilişkilendirilecek **Hesap Sahibi E-posta** adresini girin.
1. E-posta adresini onaylayın ve **Ekle**'yi seçin.

![Hesapların listesini ve +Hesap Ekle seçeneğini gösteren örnek](./media/ea-portal-administration/create-ea-add-an-account.png)

**Başka Bir Hesap Ekle**’yi seçerek başka bir hesap ekleyebilir ya da soldaki araç çubuğunun sağ alt köşesindeki **Ekle**’yi seçebilirsiniz.

Hesap sahipliğini onaylamak için:

1. Azure Enterprise Portal'da oturum açın.
1. Durumu görüntüleyin.

   **Beklemede** olan durum **Başlangıç/Bitiş tarihi** olarak değişmelidir. Başlangıç/Bitiş tarihi, kullanıcının ilk kez oturum açtığı tarih ve anlaşma bitiş tarihidir.
1. Hesap sahibi Azure Enterprise Portal'da ilk kez oturum açtığında açılan **Uyarı** iletisinde **Devam**'ı seçerek hesabı etkinleştirmesi gerekir.

## <a name="change-azure-subscription-or-account-ownership"></a>Azure aboneliğini veya hesap sahipliğini değiştirme

Kurumsal yöneticiler, bir kayıtta seçilen veya tüm aboneliklerin hesap sahipliğini aktarmak için Azure Enterprise Portal'ı kullanabilir.

Abonelik veya hesap sahipliği aktarımını tamamladığınızda, Microsoft hesap sahibini güncelleştirir.

Sahiplik aktarımını gerçekleştirmeden önce şu Azure rol tabanlı erişim denetimi (Azure RBAC) ilkelerini anlamalısınız:

- Aynı kiracıdaki iki kuruluş kimliği arasında abonelik veya hesap sahipliği aktarımları gerçekleştirildiğinde, Azure RBAC ilkeleri, mevcut hizmet yöneticisi ve ortak yönetici rolleri korunur.
- Kiracılar arası abonelik veya hesap sahipliği aktarımları, Azure RBAC ilkelerinizin ve rol atamalarınızın kaybedilmesiyle sonuçlanır.
- İlkeler ve yönetici rolleri farklı dizinler arasında aktarılmaz. Hizmet yöneticileri hedef hesabın sahibiyle güncelleştirilir.
- Kiracılar arasında abonelik aktarımı yaparken Azure RBAC ilkelerinin ve rol atamalarının kaybını önlemek için **Abonelikleri alıcının Azure AD kiracısına taşı** onay kutusunun **işaretsiz** kaldığından emin olun. Bu sayede hizmetler, Azure rolleri ve ilkeler geçerli Azure AD kiracısında kalır ve yalnızca hesap için faturalama sahipliği aktarılır.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abonelikleri Azure AD kiracısına taşıma onay kutusunun işaretsiz olduğunu gösteren resim" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Hesap sahibini değiştirmeden önce:

1. Azure Enterprise Portal'da **Hesap** sekmesini görüntüleyin ve kaynak hesabı belirleyin. Kaynak hesap etkin olmalıdır.
1. Hedef hesabı belirleyin ve etkin olduğundan emin olun.

Tüm aboneliklerin hesap sahipliğini aktarmak için:

1. Azure Enterprise Portal'da oturum açın.
1. Sol gezinti alanında **Yönet**’i seçin.
1. **Hesap** sekmesini seçin ve bir hesabın üzerine gelin.
1. Sağ taraftaki hesap sahibini değiştir simgesini seçin. Simge bir kişi gibi görünür.  
    ![Hesap Sahibini Değiştir simgesini gösteren görüntü](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Aktarılacak hedef hesabı seçin ve sonra **İleri**’yi seçin.
1. Hesap sahipliğini Azure AD kiracıları arasında aktarmak istiyorsanız **Abonelikleri alıcının Azure AD kiracısına taşı** onay kutusunu işaretleyin.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abonelikleri Azure AD kiracısına taşıma onay kutusunun seçili olduğunu gösteren resim" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Aktarımı onaylayın ve **Gönder**'i seçin.

Tek bir aboneliğin hesap sahipliğini aktarmak için:

1. Azure Enterprise Portal'da oturum açın.
1. Sol gezinti alanında **Yönet**’i seçin.
1. **Hesap** sekmesini seçin ve bir hesabın üzerine gelin.
1. Sağdaki abonelikleri aktar simgesini seçin. Simge bir sayfa gibi görünür.  
    ![Abonelikleri Aktar simgesini gösteren görüntü](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Aboneliğin aktarılacağı hedef hesabı seçin ve sonra **İleri**’yi seçin.
1. Abonelik sahipliğini Azure AD kiracıları arasında aktarmak istiyorsanız **Abonelikleri alıcının Azure AD kiracısına taşı** onay kutusunu işaretleyin.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abonelikleri Azure AD kiracısına taşıma onay kutusunun seçili olduğunu gösteren resim" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Aktarımı onaylayın ve sonra **Gönder**'i seçin.


## <a name="associate-an-account-to-a-department"></a>Bir hesabı bir departmanla ilişkilendirme

Kuruluş Yöneticileri, mevcut hesapları kayıt kapsamındaki Bölümlerle ilişkilendirebilir.

### <a name="to-associate-an-account-to-a-department"></a>Bir hesabı bir departmanla ilişkilendirmek için

1. Azure EA Portal'da kuruluş yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’i seçin.
1. **Bölüm**'ü seçin.
1. Hesabın bulunduğu satırın üzerine gelin ve sağ taraftaki kalem simgesini seçin.
1. Açılan menüden bölümü seçin.
1. **Kaydet**’i seçin.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Mevcut bir hesabı Kullandıkça Öde aboneliğiyle ilişkilendirme

Azure portalında bir Microsoft Azure hesabınız varsa bunu Kurumsal Anlaşma kaydınızla ilişkilendirmek için ilgili okul, iş veya Microsoft hesabını girin.

### <a name="associate-an-existing-account"></a>Mevcut bir hesabı ilişkilendirme

1. Azure Enterprise Portal'da **Yönet**'i seçin.
1. **Hesap** sekmesini seçin.
1. **+Hesap ekle**'yi seçin.
1. Mevcut Azure hesabıyla ilişkilendirilmiş olan iş, okul veya Microsoft hesabını girin.
1. Mevcut Azure hesabıyla ilişkilendirilmiş olan hesabı onaylayın.
1. Bu hesabı raporlarda tanımlayabilmek için kullanmak istediğiniz adı yazın.
1. **Add (Ekle)** seçeneğini belirleyin.
1. **+Hesap Ekle** seçeneğini yeniden seçerek başka bir hesap ekleyebilir veya **Yönetim** düğmesini seçerek giriş sayfasına dönebilirsiniz.
1. **Hesap** sayfasını görüntülediğinizde yeni hesabın **Beklemede** olduğunu görürsünüz.

### <a name="confirm-account-ownership"></a>Hesap sahipliğini onaylama

1. Girdiğiniz iş, okul veya Microsoft hesabıyla ilişkilendirilmiş olan e-posta hesabında oturum açın.
1. _"Microsoft Toplu Lisanslama'dan Microsoft Azure Hizmeti Hesabınızı Etkinleştime Daveti"_ başlıklı e-posta bildirimini açın.
1. Davetin içindeki **Microsoft Azure Enterprise Portal'da oturum aç** bağlantısını seçin.
1. **Oturum aç**'ı seçin.
1. Oturum açmak ve hesabın sahibi olduğunuzu doğrulamak için iş, okul veya Microsoft hesabınızı ve parolanızı girin.

### <a name="azure-marketplace"></a>Azure Market

Çoğu abonelik Kullandıkça Öde ortamından Azure Kurumsal Anlaşma'ya dönüştürülebilir ancak Azure Market bunlardan biri değildir. Tüm abonelikleri ve ücretleri tek bir yerden görüntülemek için Azure Market hizmetlerini Azure Enterprise Portal'a eklemenizi öneririz.

1. Azure Enterprise Portal'da oturum açın.
1. Sol gezinti panelinde **Yönet**’i seçin.
1. **Kayıt** sekmesini seçin.
1. **Kayıt Ayrıntıları** bölümünü görüntüleyin.
1. Azure Market alanının sağ tarafında yer alan kalem simgesini seçerek hizmeti etkinleştirin. **Kaydet**’i seçin.

Hesap sahibi artık daha önce Kullandıkça Öde aboneliğiyle sahip olduğu Azure Market hizmetlerini satın alabilir.

Yeni Azure Market abonelikleri Azure EA kaydınızda etkinleştirildikten sonra Kullandıkça Öde ortamında oluşturulan Azure Market aboneliklerini iptal edin. Kullandıkça Öde ortamındaki ödeme aracınızın süresi dolduğunda Azure Market aboneliklerinizin kullanım dışı kalmaması için bu adım kritik öneme sahiptir.

### <a name="msdn"></a>MSDN

MSDN abonelikleri otomatik olarak MSDN Geliştirme ve Test aboneliğine dönüştürülür ve Azure EA teklifindeki mevcut parasal kredi silinir.

### <a name="azure-in-open"></a>Open ile Azure

Bir Open ile Azure aboneliğini bir Kurumsal Anlaşma ile ilişkilendirmek, tüketilmemiş tüm Open ile Azure kredilerinin kaybedilmesine yol açar. Bu nedenle Kurumsal Anlaşma'ya eklemeden önce Open ile Azure aboneliğinizdeki tüm krediyi kullanmanız önerilir.  

### <a name="accounts-with-support-subscriptions"></a>Destek aboneliğine sahip hesaplar

Kurumsal Anlaşma hesabınızda destek aboneliği yoksa ve Azure Enterprise Portal'a destek aboneliğine sahip bir hesap eklerseniz, MOSA destek aboneliğiniz otomatik olarak aktarılmaz. Kullanım süresi içinde (takip eden ayın sonuna kadar) Azure EA'da yeniden destek aboneliği satın almanız gerekir.

## <a name="department-spending-quotas"></a>Departman harcama kotaları

EA müşterileri, bir kayıt kapsamındaki bölümler için ayrı harcama kotaları belirleyebilir veya mevcut kotaları değiştirebilir. Harcama kotasının miktarı geçerli Ön Ödeme dönemini kapsayacak şekilde ayarlanır. Geçerli Ön Ödeme döneminin sonunda değerler güncelleştirilmediği sürece sistem, mevcut harcama kotasını takip eden Ön Ödeme dönemine uzatır.

Bölüm yöneticisi, harcama kotasını görüntüleyebilir ancak kota miktarı yalnızca kuruluş yöneticisi tarafından güncelleştirilebilir. Kota %50, %75, %90 ve %100 düzeyine ulaştığında kuruluş yöneticisine ve bölüm yöneticisine bildirim gönderilir.

### <a name="enterprise-administrator-to-set-the-quota"></a>Kuruluş yöneticisi olarak kotayı belirlemek için:

 1. Azure EA Portal'ı açın.
 1. Sol gezinti panelinde **Yönet**’i seçin.
 1. **Bölüm** sekmesini seçin.
 1. Bölümü seçin.
 1. Bölüm Ayrıntıları bölümündeki kalem simgesini seçin veya yeni bir bölüm ve harcama kotası eklemek için **+ Bölüm Ekle** simgesini seçin.
 1. Bölüm Ayrıntıları’nın altındaki Harcama Kotası $ kutusuna kaydın para birimini kullanarak bir harcama kotası girin (değer 0'dan büyük olmalıdır).
    - Bu adımda Bölüm Adı ve Maliyet Merkezi bilgilerini de düzenleyebilirsiniz.
 1. **Kaydet**’i seçin.

Bölüm harcama kotası, Bölüm sekmesindeki Bölüm Listesi görünümünde görünür duruma gelir. Azure EA Portal, geçerli Ön Ödeme döneminin sonunda harcama kotalarını bir sonraki Ön Ödeme dönemine aktarır.

Bölüm kotası tutarı, mevcut Azure Ön Ödemesinden bağımsızdır ve kota miktarı ile uyarılar yalnızca birinci taraf kullanımı için geçerlidir. Bölüm harcama kotası yalnızca bilgilendirme amaçlıdır ve harcama limiti uygulamaz.

### <a name="department-administrator-to-view-the-quota"></a>Bölüm yöneticisi olarak kotayı görüntülemek için:

1. Azure EA Portal'ı açın.
1. Sol gezinti panelinde **Yönet**’i seçin.
1. **Bölüm** sekmesini seçin ve harcama kotalarının yer aldığı Bölüm Listesi görünümünü açın.

Dolaylı müşteriyseniz ücret özelliklerinin kanal iş ortağınız tarafından etkinleştirilmesi gerekir.

## <a name="enterprise-user-roles"></a>Kurumsal kullanıcı rolleri

Azure EA portalı, Azure EA maliyetlerinizi ve kullanımınızı yönetmenize yardımcı olur. Azure EA portalında üç ana rol vardır:

- EA yöneticisi
- Departman yöneticisi
- Hesap sahibi

Her rolün farklı bir erişim düzeyi ve yetkisi vardır.

Kullanıcı rolleri hakkında daha fazla bilgi için bkz. [Kurumsal kullanıcı rolleri](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Azure EA hesabı ekleme

Azure EA hesabı, Azure EA portalındaki bir kuruluş birimidir. Hem abonelikleri yönetmek için hem de raporlama amacıyla kullanılır. Azure hizmetlerine erişmek için bir hesap oluşturmanız ve sizin için oluşturulmuş bir hesap olması gerekir.

Azure hesapları hakkında daha fazla bilgi için bkz. [Hesap ekleme](#add-an-account).

## <a name="enterprise-devtest-offer"></a>Kurumsal Geliştirme ve Test Teklifi

Azure kuruluş yöneticisi olarak kuruluşunuzdaki hesap sahiplerinin EA Geliştirme ve Test teklifini kullanarak abonelik oluşturmasını sağlayabilirsiniz. Bunu yapmak için Azure EA Portalında hesap sahibi için Geliştirme ve Test kutusunu seçin.

Geliştirme ve Test kutusunu işaretledikten sonra hesap sahibine Geliştirme ve Test aboneleri için ihtiyaç duyulan EA Geliştirme ve Test aboneliklerini oluşturabileceğini bildirin.

Bu teklif Visual Studio aboneleri, geliştirme ve test iş yüklerini Azure'da özel Geliştirme ve Test fiyatlarıyla çalıştırabilir. Windows 8.1 ve Windows 10 da dahil olmak üzere tüm Geliştirme ve Test görünümlerini içeren bir galeriye erişim sağlar.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Kurumsal Geliştirme ve Test teklifini ayarlamak için:

1. Kuruluş yöneticisi olarak oturum açın.
1. Sol gezinti panelinde **Yönet**’i seçin.
1. **Hesap** sekmesini seçin.
1. Geliştirme ve Test erişimini etkinleştirmek istediğiniz hesabın yer aldığı satırı seçin.
1. Satırın sağ tarafındaki kalem simgesini seçin.
1. Geliştirme ve Test onay kutusunu seçin.
1. **Kaydet**’i seçin.

Bir kullanıcı Azure EA Portal aracılığıyla hesap sahibi olarak eklendiğinde hesap sahibiyle ilişkilendirilmiş olan kullandıkça öde Geliştirme ve Test teklifini Visual Studio için aylık kredi tekliflerini temel alan abonelerle ilişkilendirilmiş olan Azure abonelikleri, EA Geliştirme ve Test teklifine dönüştürülür. Kullandıkça öde gibi Hesap Sahibiyle ilişkilendirilmiş olan diğer teklif türlerini temel alan abonelikler, Microsoft Azure Kurumsal tekliflerine dönüştürülür.

Geliştirme ve Test Teklifi şu an için Azure Kamu müşterilerinin kullanımına açık değildir.

## <a name="create-a-subscription"></a>Abonelik oluşturma

Hesap sahipleri, abonelikleri görüntüleyebilir ve yönetebilir. Kuruluşunuzdaki takımlara geliştirme ortamları ve projelere yönelik erişim izni vermek için abonelikleri kullanabilirsiniz. Örneğin: test, üretim, geliştirme ve hazırlama.

Her uygulama ortamı için farklı abonelikler oluşturduğunuzda her bir ortamın güvenliğini sağlamaya yardımcı olursunuz.

- Ayrıca, her abonelik için farklı bir hizmet yöneticisi hesabı da atayabilirsiniz.
- Abonelikleri dilediğiniz sayıda hizmet ile ilişkilendirebilirsiniz.
- Hesap sahibi, abonelikler oluşturur ve hesaplarındaki her aboneliğe bir hizmet yöneticisi hesabı atar.

### <a name="add-a-subscription"></a>Abonelik ekleme

Bir abonelik eklemek için aşağıdaki bilgileri kullanın.

Hesabınıza ilk kez bir abonelik eklediğinizde, Microsoft Çevrimiçi Abonelik Sözleşmesi'ni (MOSA) ve bir ücret planını kabul etmeniz istenir. Kurumsal Anlaşma müşterileri için geçerli olmasa da, aboneliğinizin oluşturulması için MOSA ve ücret planı gereklidir. Microsoft Azure Kurumsal Anlaşma Kaydı Değişikliği, yukarıdaki öğelerin yerini alır ve sözleşme ilişkiniz değişmez. Sorulduğunda, koşulları kabul ettiğinizi belirten kutuyu seçin.

_Microsoft Azure Enterprise_, oluşturulan abonelikler için seçilen varsayılan addır. Kaydınızdaki diğer aboneliklerden ayırmak ve kuruluş düzeyindeki raporlarda ayırt edilebilmesini sağlamak için abonelik adını değiştirebilirsiniz.

Abonelik eklemek için:

1. Azure Enterprise Portal'da hesap oturumu açın.
1. **Yönetici** sekmesini ve sonra sayfanın üst kısmındaki **Abonelik** girişini seçin.
1. Hesabın hesap sahibi olarak oturum açtığınızı doğrulayın.
1. **+Abonelik Ekle** ve ardından **Satın Al**’ı seçin.

   Bir hesaba ilk kez abonelik eklediğinizde iletişim bilgilerinizi sağlamanız gerekir. Başka abonelikler eklerken, iletişim bilgileriniz sizin için eklenir.

1. **Abonelikler**'i ve ardından oluşturduğunuz aboneliği seçin.
1. **Abonelik Ayrıntılarını Düzenle**'yi seçin.
1. **Abonelik Adı** ve **Hizmet Yöneticisi**’ni düzenleyip onay işaretini seçin.

   Abonelik adı raporlarda görünür. Geliştirme portalındaki abonelikle ilişkili projenin adıdır.

Yeni aboneliklerin abonelikler listesinde görünmesi 24 saate kadar sürebilir. Bir abonelik oluşturduktan sonra şunları yapabilirsiniz:

- [Abonelik ayrıntılarını düzenleme](https://portal.azure.com)
- [Abonelik hizmetlerini yönetme](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Aboneliği silme

Hesap sahibi olduğunuz bir aboneliği silmek için:

1. Hesabınızla ilişkilendirilmiş kimlik bilgileriyle Azure portalında oturum açın.
1. Hub menüsünde **Abonelikler**'i seçin.
1. Sayfanın sol üst köşesindeki abonelikler sekmesinde iptal etmek istediğiniz aboneliği seçin ve iptal sekmesini açmak için **Aboneliği iptal et**'i seçin.
1. Abonelik adını girip iptal için bir neden seçin ve ardından **Aboneliği iptal et** düğmesini seçin.

Abonelikleri yalnızca hesap yöneticileri iptal edebilir.

Daha fazla bilgi için bkz. [Aboneliğimi iptal edersem ne olur?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)

## <a name="delete-an-account"></a>Hesap silme

Hesap kaldırma işlemi yalnızca etkin aboneliği bulunmayan etkin hesaplarda gerçekleştirilebilir.

1. Enterprise Portal'ın sol tarafındaki gezintisinde **Yönet**'i seçin.
1. **Hesap** sekmesini seçin.
1. Hesaplar tablosundan silmek istediğiniz hesabı seçin.
1. Hesap satırının sağ tarafındaki X simgesini seçin.
1. Hesabın altında etkin abonelik kalmadığında Hesap satırının altındaki **Evet**’i seçerek Hesap kaldırma işlemini onaylayın.

## <a name="update-notification-settings"></a>Bildirim ayarlarını güncelleştirme

Kuruluş Yöneticileri, kayıtlarıyla ilişkili kullanım bildirimlerini almak için otomatik olarak kaydedilir. Her Kuruluş Yöneticisi, bildirimlerin aralığını tek tek değiştirebilir veya hepsini tamamen devre dışı bırakabilir.

Bildirim kişileri, Azure EA portalındaki **Bildirim Kişisi** bölümünde gösterilir. Bildirim kişilerinizi yönetmek, kuruluşunuzdaki doğru kişilerin Azure EA bildirimleri almasını sağlar.

Geçerli bildirim ayarlarını görüntülemek için:

1. Azure EA portalında **Yönet** > **Bildirim Kişisi**’ne gidin.
2. E-posta Adresi – Kuruluş Yöneticisinin bildirimleri alan Microsoft Hesabı, İş veya Okul Hesabı ile ilişkili e-posta adresi.
3. Faturalandırılmamış Bakiye Bildirimi Sıklığı – Bildirimlerin her bir Kuruluş Yöneticisine gönderilmek üzere ayarlandığı sıklık.

Kişi eklemek için:

1. **+Kişi Ekle**’yi seçin.
2. E-posta adresini girin ve sonra onaylayın.
3. **Kaydet**’i seçin.

Yeni bildirim kişisi **Bildirim Kişisi** bölümünde gösterilir. Bildirim sıklığını değiştirmek için, bildirim kişisini seçin ve seçili satırın sağ tarafındaki kalem simgesini seçin. Sıklığı **günlük**, **haftalık**, **aylık** veya **hiçbiri** olarak ayarlayın.

_Yaklaşan kapsama dönemi bitiş tarihi_ ve _yaklaşan tarih_ yaşam döngüsü bildirimlerini devre dışı bırakıp sağlamayı bırakabilirsiniz. Yaşam döngüsü bildirimlerini devre dışı bırakmak, kapsama dönemi ve anlaşma bitiş tarihi hakkındaki bildirimleri gizler.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorluğu Teklifi

Azure Sponsorluğu Teklifi, sınırlı ve sponsorlu bir Microsoft Azure hesabıdır. Yalnızca Microsoft tarafından seçilen sınırlı müşteriler tarafından e-posta daveti ile kullanılabilir. Microsoft Azure Sponsorluğu teklifine hak kazandıysanız hesap kimliğinize bir e-posta davetiyesi alırsınız.

Daha fazla bilgi için [sponsorluğu etkinleştirme amacıyla bir destek isteği oluşturun](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>İş veya okul hesabı kimlik doğrulamasına dönüştürme

Azure Kurumsal kullanıcıları, Microsoft hesabı (MSA veya Live Kimliği) yerine iş veya okul hesabı (Azure Active Directory kullanan) kimlik doğrulaması kullanmayı tercih edebilir.

Başlamak için:

1. İş veya okul hesabını Azure EA Portal'da istediğiniz rollere ekleyin.
1. Hata alırsanız, hesap geçerli bir Active Directory hesabı olmayabilir.  Azure, Kullanıcı Asıl Adı (UPN) değerini kullanır ve bu her zaman e-posta adresiyle aynı olmayabilir.
1. İş veya okul hesabını kullanarak Azure EA Portal'da kimliğinizi doğrulayın.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Aboneliklerde Microsoft hesabı yerine iş veya okul hesabını kullanmak için:

1. Aboneliklerin sahibi olan Microsoft hesabını kullanarak yönetim portalında oturum açın.
1. Yeni hesaba geçiş yapmak için hesap sahipliği aktarma işlevini kullanın.
1. Bunu yaptığınızda Microsoft hesabında silinebilecek etkin abonelik kalmamalıdır.
1. Silinen hesaplar geçmişe dönük faturalama nedeniyle portalda kalır ancak devre dışı durumda görünür.  Yalnızca etkin hesapların görüntülenmesini sağlayan onay kutusunu seçerek eski hesapları görünümden kaldırabilirsiniz.

## <a name="azure-ea-term-glossary"></a>Azure EA terimleri sözlüğü

- **Hesap**: Azure Enterprise Portal'daki kuruluş birimleridir. Abonelikleri yönetmek ve raporlama için kullanılır.
- **Hesap sahibi**: Azure'da abonelikleri ve hizmet yöneticilerini yöneten kişidir. Bu kullanıcılar hesapla ve ilişkilendirilmiş aboneliklerle ilgili kullanım verilerini görüntüleyebilir.
- **Değişiklik aboneliği**: Kayıt değişikliği kapsamında bir yıllık veya uyuşan dönem boyunca devam eden aboneliktir.
- **Ön Ödeme**: Azure hizmetlerinin ön ödeme karşılığında kullanılması için önceden ödenmiş indirimli ücretlerden karşılanması amacıyla gerçekleştirilen yıllık parasal tutar ön ödemedir.
- **Bölüm yöneticisi**: Bölümleri yöneten, yeni hesap ve hesap sahibi oluşturan, yönetilen bölümlerle ilgili kullanım ayrıntılarını görüntüleyen ve izin verildiğinde maliyetleri görüntüleyebilen kişidir.
- **Kayıt numarası**: Microsoft tarafından Kurumsal Anlaşma ile ilişkilendirilmiş olan kaydı tanımlamak için verilen benzersiz tanıtıcıdır.
- **Kuruluş yöneticisi**: Azure'daki departmanları, departman sahiplerini, hesapları ve hesap sahiplerini yöneten kişidir. Kuruluş yöneticileri, diğer kuruluş yöneticilerini de yönetmenin yanı sıra kurumsal kayıtla ilişkilendirilmiş olan tüm hesaplara ve aboneliklere ait olan kullanım verilerini, faturalandırılan miktarları ve faturalandırılmamış ücretleri görüntüleyebilir.
- **Kurumsal anlaşma**: Kuruluşlarının tamamında Microsoft teknolojilerini standart hale getirmek ve Microsoft yazılım standardını temel alan bir bilgi teknolojisi altyapısı kurmak isteyen, merkezi satın alma sistemine sahip olan müşterilere yönelik bir Microsoft lisans sözleşmesidir.
- **Kurumsal anlaşma kaydı**: Microsoft ürünlerini toplu olarak ve indirimli fiyatlarla sunan Kurumsal Anlaşma programı kaydıdır.
- **Microsoft hesabı**: Katılan sitelerin kullanıcının kimliğini tek bir kimlik bilgisi ile doğrulamasını sağlayan Web tabanlı bir hizmettir.
- **Microsoft Azure Kurumsal Kayıt Değişikliği (kayıt değişikliği)** : Bir kuruluş tarafından imzalanan ve kurumsal kayıt çerçevesinde Azure erişimi sağlayan değişikliktir.
- **Azure Enterprise Portal**: Kurumsal müşterilerimizin Azure hesaplarını ve ilişkili aboneliklerini yönetmek için kullandığı portaldır.
- **Kullanılan kaynak miktarı**: Bir ay içinde kullanılan tek bir Azure hizmetinin miktarıdır.
- **Hizmet yöneticisi**: Azure Enterprise Portal'daki aboneliklere ve geliştirme projelerine erişebilen ve bunları yönetebilen kişidir.
- **Abonelik**: Bir Azure Enterprise Portal aboneliğini temsil eder ve aynı hizmet yöneticisi tarafından yönetilen Azure hizmetlerini içeren bir kapsayıcıdır.
- **İş veya okul hesabı**: Buluta federasyon ile Azure Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlara yöneliktir.

### <a name="enrollment-statuses"></a>Kayıt durumları

- **Yeni**: Bu durum, 24 saat içinde oluşturulan bir kayda atanır ve 24 saat içinde Beklemede durumuna güncelleştirilir.
- **Beklemede**: Kayıt yöneticisinin Azure Enterprise Portal'da oturum açması gerekir. Oturum açıldıktan sonra kayıt "Etkin" duruma geçer.
- **Etkin**: Kayıt "Etkin" durumdadır, Azure Enterprise Portal'da hesaplar ve abonelikler oluşturulabilir. Kurumsal Anlaşma'nın bitiş tarihine kadar kayıt etkin durumda kalır.
- **Süresiz uzatılan süre**: Süresiz uzatılan süre durumu, Kurumsal Anlaşma bitiş tarihi geçtikten sonra gerçekleşir. Uzatılan süreyi kabul eden Azure EA müşterilerinin Azure hizmetlerini Kurumsal Anlaşmaları bitene kadar sınırsız bir şekilde kullanmalarını sağlar.

   Azure EA kaydı Kurumsal Anlaşma bitiş tarihine ulaşana kadar layıt yöneticisinin aşağıdaki seçeneklerden birini belirlemesi gerekir:

  - Azure Ön Ödemesi ekleyerek kaydı yenileyin.
  - Yeni bir kayda aktarma.
  - Microsoft Çevrimiçi Abonelik Programı'na (MOSP) geçme.
  - Kayıtla ilişkili tüm hizmetleri devre dışı bırakmayı onaylama.
- **Süresi Doldu**: Azure EA müşterisi uzatılan süreyi kabul etmemiş ve Azure EA kaydı Kurumsal Anlaşma bitiş tarihine gelmiştir. Kaydın süresi dolacak ve ilgili tüm hizmetler devre dışı bırakılacaktır.
- **Aktarıldı**: İlişkilendirilmiş hesapların ve hizmetlerin yeni bir kayda aktarıldığı kayıtların durumu "Aktarıldı" olarak görünür.
  >[!NOTE]
  > Yenileme sırasında yeni bir kayıt numarası oluşturulduysa kayıtlar otomatik olarak aktarılmaz. Otomatik aktarım için yenileme belgelerinize önceki kayıt numaranızı eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal makine rezervasyonlarının](ea-portal-vm-reservations.md) paradan tasarruf etmenize nasıl yardımcı olduğu hakkında bilgi edinin.
- Azure EA portalı sorunlarını gidermek için yardıma ihtiyacınız varsa [Azure EA portalı erişim sorunlarını giderme](ea-portal-troubleshoot.md) konusuna bakın.
- EA abonelik sahipliği hakkında sorularınız için [maliyet yönetimi + FATURALANDıRMA SSS](../cost-management-billing-faq.yml) makalesini okuyun.