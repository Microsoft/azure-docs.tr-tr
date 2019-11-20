---
title: Microsoft Müşteri Sözleşmesi için ödeme hesabınızı ayarlama - Azure
description: Microsoft Müşteri Sözleşmesi için ödeme hesabınızı ayarlama hakkında bilgi edinin.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: beaf04624cae1dfc970d513160fbeb7513bf30a4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376629"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi için ödeme hesabınızı ayarlama

Kurumsal Anlaşma kaydınızın süresi dolduysa veya dolmak üzereyse, kaydınız yenilemek için bir Microsoft Müşteri Sözleşmesi imzalayabilirsiniz. Bu makalede, ayarlama sonrasında mevcut faturanızda yapılan değişiklikler açıklanır ve yeni ödeme hesabınızın ayarlanması boyunca size kılavuzluk eder. Yenileme işlemi aşağıdaki adımlardan oluşur:

1. Yeni Microsoft Müşteri Sözleşmesi’ni kabul edin. Ayrıntıları anlamak ve yeni sözleşmeyi kabul etmek için Microsoft alan temsilcinizle birlikte çalışın.
2. Yeni Microsoft Müşteri Sözleşmesi için oluşturulan yeni ödeme hesabını ayarlayın.

Ödeme hesabını ayarlamak için Azure aboneliklerinin faturalama sürecini Kurumsal Anlaşma kaydınızdan yeni hesabınıza geçirmeniz gerekir. Ayarlama, aboneliklerinizde çalışan Azure hizmetlerini etkilemez. Ancak, abonelikleriniz için faturalandırmayı yönetme şeklinizi değiştirir.

- Azure hizmetleri ile faturalarını [EA portalı](https://ea.azure.com) yerine [Azure portalından](https://portal.azure.com) yönetirsiniz.
- Ücretlerinizin aylık, dijital bir faturasını alırsınız. Faturayı Azure Maliyet Yönetimi + Faturalama sayfasında görüntüleyip analiz edebilirsiniz.
- Kurumsal Anlaşma kaydınızdan departmanlar ve hesap yerine, faturalamanızı yönetmek ve düzenlemek için yeni hesaptaki faturalandırma yapısını ve kapsamları kullanacaksınız.

Ayarlamaya başlamadan önce aşağıdakileri yapmanızı öneririz:

- **Yeni ödeme hesabınızı anlayın**
  - Yeni hesabınız, kuruluşunuz için faturalandırmayı basitleştirir. [Yeni ödeme hesabınızın hızlı bir genel bakışını görün](billing-mca-overview.md)
- **Ayarlamayı tamamlamaya yönelik erişiminizi doğrulayın**
  - Yalnızca belirli yönetici izinlerine sahip kullanıcılar ayarlamayı tamamlayabilir. [Ayarlamayı tamamlamak için gereken erişime](#access-required-to-complete-the-setup) sahip olup olmadığınızı denetleyin.
- **Faturalandırma hiyerarşinizdeki değişiklikleri anlayın**
  - Yeni ödeme hesabınız, Kurumsal Anlaşma kaydınızdan farklı şekilde düzenlenir. [Yeni hesabınızın faturalandırma hiyerarşisindeki değişiklikleri anlayın](#understand-changes-to-your-billing-hierarchy).
- **Faturalandırma yöneticilerinizin erişim izinlerindeki değişikliklerini anlayın**
  - Kurumsal Anlaşma kaydınızdaki Yöneticiler, yeni hesaptaki faturalama kapsamlarına erişim elde ederler. [Erişim izinlerindeki değişiklikleri anlayın](#changes-to-billing-administrator-access).
- **Yeni hesapla değiştirilen Kurumsal Anlaşma özelliklerini görüntüleyin**
  - Yeni hesaptaki özelliklerle değiştirilmiş Kurumsal Anlaşma kaydının özelliklerini görüntüleyin.
- **En yaygın soruların yanıtlarını görüntüleyin**
  - Ayarlama hakkında daha fazla bilgi almak için [ek bilgileri](#additional-information) görüntüleyin.

## <a name="access-required-to-complete-the-setup"></a>Ayarlamayı tamamlamak için gereken erişim

Ayarlamayı tamamlamak için aşağıdaki erişim izinleri gerekir:

- Microsoft Müşteri Sözleşmesi imzalandığında oluşturulan faturalama profilinin sahibi. Faturalama profilleri hakkında daha fazla bilgi edinmek için bkz. [Faturalama profillerini anlama](billing-mca-overview.md#billing-profiles).

- Yenilenen kayıt üzerindeki kuruluş yöneticisi.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Kayıt üzerinde kuruluş yöneticisi değilseniz

Kaydın kuruluş yöneticilerinden ödeme hesabınızın ayarlanmasını tamamlamalarını isteyebilirsiniz.

1. Microsoft Müşteri Sözleşmesi’ni imzaladığınızda size gönderilen e-postadaki bağlantıyı kullanarak Azure portalında oturum açın.

2. E-postanız yoksa aşağıdaki bağlantıyı kullanarak oturum açın. `<enrollmentNumber>` değerini, yenilenen kurumsal anlaşmanızın kuruluş numarasıyla değiştirin.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. İsteği göndermek istediğiniz kuruluş yöneticilerini seçin.

   ![Kuruluş yöneticilerini davet etmeyi gösteren ekran görüntüsü](./media/mca-setup-account/ea-mca-invite-admins.png)

4. **İstek gönder**’i seçin.

   Yöneticiler, ayarlamayı tamamlamaya yönelik yönergeler içeren bir e-posta alır.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Faturalama profilinin sahibi değilseniz

Kuruluşunuzda Microsoft Müşteri Sözleşmesini imzalayan kullanıcı, faturalama profilinin sahibi olarak eklenir. Ayarlamayı tamamlayabilmeniz için kullanıcıdan sizi sahip olarak eklemesini isteyin.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Faturalandırma hiyerarşinizdeki değişiklikleri anlayın

Yeni faturalandırma hesabınız, gelişmiş faturalandırma ve maliyet yönetimi olanakları sağlarken kuruluşunuz için faturalandırmayı basitleştirir. Aşağıdaki diyagramda yeni ödeme hesabında faturalamanın nasıl düzenlendiği açıklanır.

![ea-mca-post-transition-hierarchy resmi](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Microsoft müşteri sözleşmeniz için faturalandırmayı yönetmek için ödeme hesabını kullanırsınız. Kuruluş yöneticileri, faturalama profilinin sahibi olur. Ödeme hesabı hakkında daha fazla bilgi edinmek için bkz. [Ödeme hesabını anlama](billing-mca-overview.md#your-billing-account).
2. Kuruluşunuzda faturalamayı yönetmek için Kurumsal Anlaşma kaydınıza benzer şekilde faturalama profilini kullanırsınız. Kuruluş yöneticileri faturalama profilinin sahibi olur. Faturalama profilleri hakkında daha fazla bilgi edinmek için bkz. [Faturalama profillerini anlama](billing-mca-overview.md#billing-profiles).
3. Gereksinimlerinize göre maliyetlerinizi düzenlemek için Kurumsal Anlaşma kaydınızdaki departmanlara benzer şekilde bir fatura bölümü oluşturun. Bölümler fatura bölümlerine ve bölüm yöneticileri de ilgili fatura bölümlerinin sahiplerine dönüşür. Fatura bölümleri hakkında daha fazla bilgi edinmek için [fatura bölümlerini anlama](billing-mca-overview.md#invoice-sections) konusuna bakın.
4. Kurumsal Anlaşmanızda oluşturulan hesaplar yeni ödeme hesabında desteklenmez. Hesabın abonelikleri, kendi bölümleri için ilgili fatura bölümüne aittir. Hesap sahipleri kendi fatura bölümleri için abonelikleri oluşturabilir ve yönetebilir.

## <a name="changes-to-billing-administrator-access"></a>Faturalandırma yöneticisi erişimindeki değişiklikler

Erişim izinlerine bağlı olarak, Kurumsal Anlaşma kaydınızdaki faturalandırma yöneticileri, yeni hesaptaki faturalama kapsamlarına erişim elde ederler. Aşağıdaki tabloda, ayarlama sırasında gerçekleşen erişim değişikliği açıklanmaktadır:

| Mevcut rol | Geçiş sonrası rol |
| --- | --- |
| **Kuruluş yöneticisi (Salt okuma = Hayır)** | **- Ödeme hesabı sahibi** </br> Ödeme hesabı üzerindeki her şeyi yönetme </br> **- Faturalama profili sahibi** </br> Faturalama profili üzerindeki her şeyi yönetme </br> **- Tüm fatura bölümlerindeki fatura bölümü sahibi** </br> Fatura bölümlerindeki her şeyi yönetme |
| **Kuruluş yöneticisi (Salt okuma = Evet)** | **- Faturalama hesabı okuyucusu** </br> Faturalandırma hesabındaki her şeyin salt okunur görünümü</br> **- Faturalama profili okuyucusu** </br> Faturalandırma profilindeki her şeyin salt okunur görünümü</br>**- Tüm fatura bölümlerindeki fatura bölümü okuyucusu**</br> Fatura bölümlerindeki her şeyin salt okunur görünümü|
| **Departman yöneticisi (Salt okuma = Hayır)** |**- İlgili departman için oluşturulan fatura bölümündeki fatura bölümü sahibi** </br>Fatura bölümündeki her şeyi yönetme|
| **Departman yöneticisi (Salt okuma = Evet)**|**- İlgili departman için oluşturulan fatura bölümündeki fatura bölümü okuyucusu**</br> Fatura bölümündeki her şeyin salt okunur görünümü|
| **Hesap sahibi** | **- İlgili departman için oluşturulan fatura bölümündeki Azure aboneliği oluşturucusu** </br>  Kendi fatura bölümü için Azure abonelikleri oluşturma|

Microsoft Müşteri Sözleşmesini kabul ettiğinizde yeni ödeme hesabı için bir Azure Active Directory (AD) kiracısı seçilir. Kuruluşunuz için bir kiracı yoksa, yeni bir kiracı oluşturulur. Kiracı, Azure Active Directory içinde kuruluşunuzu temsil eder. Kuruluşunuzdaki genel kiracı yöneticileri, kuruluşunuzdaki uygulamalara ve verilere erişimi yönetmek için kiracıyı kullanır.

Yeni hesabınız yalnızca Microsoft Müşteri Sözleşmesi imzalanırken seçilen kiracıdan gelen kullanıcıları destekler. Kurumsal Anlaşma üzerinde yönetici iznine sahip kullanıcılar kiracının parçasıysa, ayarlama sırasında yeni ödeme hesabına erişim elde ederler. Kiracının parçası olmadıkları takdirde, davet etmediğiniz sürece yeni ödeme hesabına erişemezler.

Kullanıcıları davet ettiğinizde konuk kullanıcı olarak kiracıya eklenirler ve ödeme hesabına erişime elde ederler. Kullanıcıları davet etmek için, kiracı için konuk erişimin açık olması gerekir. Daha fazla bilgi için bkz. [Azure Active Directory’de konuk erişimini denetleme](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Konuk erişimi kapalıysa, açmak için kiracınızın genel yöneticilerine başvurun. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Değiştirilmiş özellikleri görüntüleme

Aşağıdaki Kurumsal Anlaşma özellikleri, Microsoft Müşteri Sözleşmesi için ödeme hesabındaki yeni özelliklerle değiştirilir.

### <a name="enterprise-agreement-accounts"></a>Kurumsal Anlaşma hesapları

Kurumsal Anlaşma kaydınızda oluşturulan hesaplar yeni ödeme hesabında desteklenmez. Hesabın abonelikleri, kendi departmanları için oluşturulan fatura bölümüne aittir. Hesap sahipleri Azure abonelik oluşturucusu olabilir ve kendi fatura bölümleri için abonelikleri oluşturabilir ve yönetebilir.

### <a name="notification-contacts"></a>Bildirim kişileri

Bildirim ilgili kişilerine Azure Kurumsal Anlaşma ile ilgili e-posta iletişimleri gönderilir. Bunlar yeni ödeme hesabında desteklenmez. Azure kredileri ve faturalarıyla ilgili e-postalar, ödeme hesabınızdaki faturalama profillerine erişebilen kullanıcılara gönderilir.

### <a name="spending-quotas"></a>Harcama kotaları

Kurumsal Anlaşma kaydınızdaki departmanlar için ayarlanan harcama kotaları, yeni ödeme hesabındaki bütçelerle değiştirilir. Kaydınızda ayarlanmış her harcama kotası kümesi için bir bütçe oluşturulur. Bütçeler hakkında daha fazla bilgi için bkz. [Azure bütçeleri oluşturma ve yönetme](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Maliyet merkezleri

Kurumsal Anlaşma kaydınızdaki Azure abonelikleri üzerinde ayarlanmış maliyet merkezi, yeni ödeme hesabına taşınır. Ancak, departmanlar ve Kurumsal Anlaşma hesaplarına ait maliyet merkezleri desteklenmez.

## <a name="additional-information"></a>Ek bilgiler

Aşağıdaki bölümlerde ödeme hesabınızı ayarlama hakkında ek bilgiler sağlanmaktadır.

### <a name="no-service-downtime"></a>Hizmet kapalı kalma süresi yoktur

Aboneliğinizdeki Azure hizmetleri kesintisiz olarak çalışmaya devam eder. Yalnızca Azure aboneliklerinizin faturalama ilişkisini geçiririz. Mevcut kaynaklar, kaynak grupları veya yönetim grupları üzerinde herhangi bir etki söz konusu olmayacaktır.

### <a name="user-access-to-azure-resources"></a>Azure kaynaklarına kullanıcı erişimi

Azure RBAC (rol tabanlı erişim denetimi) kullanılarak ayarlanan Azure kaynaklarına erişim geçiş sırasında etkilenmez.

### <a name="azure-reservations"></a>Azure Rezervasyonlar

Kurumsal Anlaşma kaydınızdaki tüm Azure Rezervasyonları da yeni faturalama hesabınıza taşınır. Geçiş sırasında, aboneliklerinizde uygulanan rezervasyon indirimlerinde herhangi bir değişiklik olmaz.

### <a name="azure-marketplace-products"></a>Azure Market ürünleri

Kurumsal Anlaşma kaydınıza ait tüm Azure Market ürünleri, aboneliklerle birlikte taşınır. Geçiş sırasında Market ürünlerinin hizmet erişiminde herhangi bir değişiklik olmayacaktır.

### <a name="support-plan"></a>Destek planı

Destek avantajları geçişin bir parçası olarak aktarılmaz. Yeni ödeme hesabınızda Azure aboneliklerine yönelik avantajlardan yararlanmak için yeni bir destek planı satın alın.

### <a name="past-charges-and-balance"></a>Geçmiş ücretler ve bakiye

Geçiş işleminden önce ücretler ve kredi bakiyesi, Azure portalı üzerinden Kurumsal Anlaşma kaydınızda görüntülenebilir. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Ayarlamanın ne zaman tamamlanması gerekir?

Kurumsal Anlaşma kaydınız sona ermeden önce ödeme hesabınızın ayarlanmasını tamamlayın. Kaydınızın süresi dolarsa, Azure aboneliklerinizdeki hizmetler kesintiye uğramadan çalışmaya devam edecektir. Bununla birlikte, hizmetler için kullandıkça öde fiyatları üzerinden ücretlendirilirsiniz.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Ayarlama sonrasında Kurumsal Anlaşma kaydında yapılan değişiklikler

Geçişten sonra Kurumsal Anlaşma kaydı için oluşturulan Azure abonelikleri, yeni ödeme hesabına elle taşınabilir. Daha fazla bilgi için bkz. [Diğer kullanıcılardan Azure aboneliklerinin fatura sahipliğini alma](billing-mca-request-billing-ownership.md). Geçişten sonra satın alınan Azure Rezervasyonlarını taşımak için [Azure Desteği'ne başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Geçişten sonra ödeme hesabına kullanıcı erişimi de sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Geçişi geri döndürme

Geçiş geri döndürülemez. Azure aboneliklerinizin faturalama süreci yeni faturalama hesabınıza geçirildikten sonra bunu Kurumsal Anlaşma kaydınıza geri döndüremezsiniz.

### <a name="closing-your-browser-during-setup"></a>Ayarlama sırasında tarayıcınızı kapatma

**Geçişi başlat**’a tıklamadan önce tarayıcıyı kapatabilirsiniz. E-postada aldığınız bağlantıyı kullanarak ayarlama işlemine geri dönebilir ve geçişi başlatabilirsiniz. Geçişi başlattıktan sonra tarayıcıyı kapatırsanız geçişiniz çalışmaya devam edecektir. Geçişinizin en son durumunu izlemek için geçiş durumu sayfasına geri dönün. Geçiş tamamlandığında bir e-posta alırsınız.

## <a name="complete-the-setup-in-the-azure-portal"></a>Azure portalında ayarlamayı tamamlama

Ayarlamayı tamamlamak için hem yeni faturalama hesabına hem de Kurumsal Anlaşma kaydına erişiminizin olması gerekir. Daha fazla bilgi için bkz. [Ödeme hesabınızın ayarlanmasını tamamlamak için gereken erişim](#access-required-to-complete-the-setup).

1. Microsoft Müşteri Sözleşmesi’ni imzaladığınızda size gönderilen e-postadaki bağlantıyı kullanarak Azure portalında oturum açın.

2. E-postanız yoksa aşağıdaki bağlantıyı kullanarak oturum açın. `<enrollmentNumber>` değerini, yenilenen kurumsal anlaşmanızın kuruluş numarasıyla değiştirin.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Ayarlamanın son adımında **Geçişi Başlat**’ı seçin. Geçişi başlat'ı seçtikten sonra:

    ![Ayarlama sihirbazını gösteren ekran görüntüsü](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - Yeni ödeme hesabında, Kurumsal Anlaşma hiyerarşinize karşılık gelen bir faturalandırma hiyerarşisi oluşturulur. Daha fazla bilgi için bkz. [Faturalandırma hiyerarşinizdeki değişiklikleri anlayın](#understand-changes-to-your-billing-hierarchy).
    - Kurumsal Anlaşma kaydınızdaki yöneticilere, kuruluşunuzun faturalandırmasını yönetmeye devam edebilmeleri için yeni ödeme hesabına erişim izni verilir.
    - Azure aboneliklerinizin faturaları yeni hesaba geçirilir. **Bu geçiş sırasında Azure hizmetleriniz etkilenmez. Herhangi bir kesinti olmadan çalışmaya devam ederler.**
    - Azure Rezervasyonlarınız varsa avantajlar veya koşullar değişmeden yeni ödeme hesabınıza taşınır. 

4. Geçişinizin durumunu **Geçiş durumu** sayfasından izleyebilirsiniz.

   ![Geçiş durumunu gösteren ekran görüntüsü](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Ödeme hesabı ayarlarını doğrulama

 Yeni ödeme hesabınızın düzgün ayarlandığından emin olmak için aşağıdakileri doğrulayın:

### <a name="azure-subscriptions"></a>Azure abonelikleri

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/mca-setup-account/search-cmb.png)

3. Ödeme hesabını seçin. Ödeme hesabı, **Microsoft Müşteri Sözleşmesi** türünde olacaktır.

4. Sol taraftan **Azure abonelikleri**'ni seçin.

   ![Abonelik listesini gösteren ekran görüntüsü](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Kurumsal Anlaşma kaydınızdan yeni ödeme hesabına geçirilen Azure abonelikleri, Azure abonelikleri sayfasında gösterilir. Herhangi bir aboneliğin eksik olduğunu düşünüyorsanız, aboneliğin faturasını Azure portalında elle geçirin. Daha fazla bilgi için bkz. [Diğer kullanıcılardan Azure aboneliklerinin fatura sahipliğini alma](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure rezervasyonları

Kurumsal Anlaşma kaydınızdaki Azure rezervasyonları, avantaj veya koşullarda bir değişiklik olmadan yeni ödeme hesabınıza taşınır. Geçişten önce tamamlanan işlemler yeni ödeme hesabınızda görünmez. Bununla birlikte, [Azure rezervasyonları sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) ziyaret ederek, rezervasyonlarınızın avantajlarının aboneliklerinize uygulandığını doğrulayabilirsiniz.

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Kuruluş yöneticilerinin ödeme hesabına erişimi

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/mca-setup-account/search-cmb.png)

3. **Microsoft Müşteri Sözleşmesi** ödeme hesabınızı seçin.

4. Sol taraftan **Erişim denetimi (IAM)** öğesini seçin.

   ![Geçiş sonrasında kuruluş yöneticilerinin erişimini gösteren ekran görüntüsü](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Kuruluş yöneticileri ödeme hesabı sahipleri olarak listelenirken, salt okuma izinlerine sahip kuruluş yöneticileri ödeme hesabı okuyucuları olarak listelenir. Herhangi bir kuruluş yöneticisi erişiminin eksik olduğunu düşünüyorsanız, Azure portalından erişim izni verebilirsiniz. Daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Kuruluş yöneticilerinin faturalama profiline erişimi

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/mca-setup-account/search-cmb.png)

3. Kaydınız için faturalama profilini seçin. Erişiminize bağlı olarak, bir faturalama hesabı seçmeniz gerekebilir. Ödeme hesabında Faturalama profilleri'ni ve ardından faturalama profilini seçin.

4. Sol taraftan **Erişim denetimi (IAM)** öğesini seçin.

   ![Geçiş sonrasında kuruluş yöneticilerinin erişimini gösteren ekran görüntüsü](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Kuruluş yöneticileri faturalama profili sahipleri olarak listelenirken, salt okuma izinlerine sahip kuruluş yöneticileri faturalama profili okuyucuları olarak listelenir. Herhangi bir kuruluş yöneticisi erişiminin eksik olduğunu düşünüyorsanız, Azure portalından erişim izni verebilirsiniz. Daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Fatura bölümlerindeki kuruluş yöneticileri, departman yöneticileri ve hesap sahipleri erişimi

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/mca-setup-account/search-cmb.png)arasında yetersiz alanla karşılaştı.

3. Bir fatura bölümü seçin. Fatura bölümleri, Kurumsal Anlaşma kayıtlarındaki ilgili departmanlarla aynı ada sahiptir. Erişiminize bağlı olarak, bir faturalama hesabı seçmeniz gerekebilir. Ödeme hesabında **Faturalama profilleri**'ni ve ardından **Fatura bölümleri**’ni seçin. Fatura bölümleri listesinden bir fatura bölümü seçin.

   ![Geçiş sonrasındaki fatura bölümü listesini gösteren ekran görüntüsü](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Sol taraftan **Erişim denetimi (IAM)** öğesini seçin.

    ![Geçiş sonrasında departman ve hesap yöneticilerinin erişimini gösteren ekran görüntüsü](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Kuruluş yöneticileri ve departman yöneticileri fatura bölümü sahibi veya fatura bölümü okuyucusu olarak listelenirken, departmandaki hesap sahipleri Azure abonelik oluşturucusu olarak listelenir. Kurumsal Anlaşma kaydınızdaki tüm departmanlara ait erişim izinlerini denetlemek üzere tüm fatura bölümleri için bu adımı tekrarlayın. Herhangi bir departmana bağlı olmayan hesap sahipleri, **Varsayılan fatura bölümü** adlı bir fatura bölümünde izin elde eder. Herhangi bir yönetici erişiminin eksik olduğunu düşünüyorsanız, Azure portalından erişim izni verebilirsiniz. Daha fazla bilgi için bkz. [Azure portalındaki faturalandırma rollerini yönetme](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni ödeme hesabınızla çalışmaya başlama](billing-mca-overview.md)

- [Microsoft Müşteri Sözleşmesi için ödeme hesabınızda Kurumsal Anlaşma görevlerini tamamlama](billing-mca-enterprise-operations.md)

- [Ödeme hesabınıza erişimi yönetme](billing-understand-mca-roles.md)
