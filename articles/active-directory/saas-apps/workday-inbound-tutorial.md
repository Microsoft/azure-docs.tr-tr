---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Workday yapılandırma | Microsoft Docs'
description: İş Workday 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954234"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Workday yapılandırma

Bu öğreticinin amacı, Workday 'den çalışan profillerini şirket içi Active Directory (AD) olarak sağlamak için gerçekleştirmeniz gereken adımları gösteriyoruz.

>[!NOTE]
>Workday 'den sağlamak istediğiniz kullanıcıların şirket içi AD hesabına ve bir Azure AD hesabına ihtiyacı varsa, bu öğreticiyi kullanın. 
>* Workday 'deki kullanıcıların yalnızca Azure AD hesabına ihtiyacı varsa (yalnızca bulutta bulunan kullanıcılar), lütfen [Workday 'Yi Azure AD Kullanıcı sağlaması için yapılandırma](workday-inbound-cloud-only-tutorial.md) hakkında öğreticiye bakın. 
>* Azure AD 'den Workday 'e e-posta adresi, Kullanıcı adı ve telefon numarası gibi özniteliklerin geri yazmayı yapılandırmak için lütfen [Workday geri yazmayı yapılandırma](workday-writeback-tutorial.md)hakkında öğreticiye bakın.

## <a name="overview"></a>Genel Bakış

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , Kullanıcı hesaplarını sağlamak Için [Workday insan kaynakları API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 'siyle tümleştirilir. Azure AD Kullanıcı sağlama hizmeti tarafından desteklenen Workday Kullanıcı sağlama iş akışları aşağıdaki insan kaynakları ve kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu etkinleştirir:

* **Yeni çalışanların işe** Alım-Workday 'e yeni bir çalışan eklendiğinde, Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../app-provisioning/user-provisioning.md)otomatik olarak bir kullanıcı hesabı oluşturulur.

* **Çalışan özniteliği ve profil güncelleştirmeleri** -bir çalışan kaydı Workday 'de (ad, başlık veya yönetici gibi) güncelleştirildiğinde, kullanıcı hesabı Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../app-provisioning/user-provisioning.md)otomatik olarak güncelleştirilir.

* **Çalışan sonlandırmaları** -bir çalışan Workday 'de sonlandırıldığında, kullanıcı hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak devre dışıdır.

* **Çalışan rehires** -bir çalışan Workday 'de yeniden hazırlandığında, eski hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak yeniden etkinleştirilebilir veya yeniden sağlanabilir.

### <a name="whats-new"></a>Yenilikler
Bu bölüm, son Workday tümleştirme geliştirmelerini yakalar. Kapsamlı güncelleştirmeler, planlanan değişiklikler ve arşivlerin bir listesi için, lütfen [Azure Active Directory](../fundamentals/whats-new.md) yenilikler sayfasını ziyaret edin. 

* **On 2020-etkin Iş günü için isteğe bağlı sağlama:** [İsteğe bağlı sağlama](../app-provisioning/provision-on-demand.md) özelliğini kullanarak öznitelik eşlemenizi ve ifade mantığınızı doğrulamak üzere Workday 'deki belirli bir kullanıcı profili için uçtan uca sağlamayı test edebilirsiniz.   

* **2020 Mayıs-Workday 'e telefon numarası geri yazma özelliği:** E-posta ve Kullanıcı adının yanı sıra, artık iş telefonu numarasını ve cep telefonu numarasını Azure AD 'den Workday 'e geri yazabilirsiniz. Daha fazla ayrıntı için [geri yazma uygulaması öğreticisine](workday-writeback-tutorial.md)bakın.

* **2020 Nisan-Workday Web Hizmetleri (WWS) API 'sinin en son sürümü Için destek:** Mart ve Eylül 'de yılda iki kez Workday, iş hedeflerinizi karşılamanıza ve iş gücü taleplerini değiştirmenize yardımcı olan özellik açısından zengin güncelleştirmeler sunar. Workday tarafından sunulan yeni özelliklerle çalışmaya devam etmek için bağlantı URL 'sinde kullanmak istediğiniz WWS API sürümünü doğrudan belirtebilirsiniz. Workday API sürümünün nasıl belirtilme hakkındaki ayrıntılar için, [Workday bağlantısını yapılandırma](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)konusunun bölümüne bakın. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Bu Workday Kullanıcı sağlama çözümü idealdir:

* Workday Kullanıcı sağlaması için önceden oluşturulmuş, bulut tabanlı bir çözüm gerektiren kuruluşlar

* Workday 'den Active Directory veya Azure Active Directory doğrudan Kullanıcı sağlamasını gerektiren kuruluşlar

* Kullanıcıların Workday HCM modülünden elde edilen veriler kullanılarak sağlanması gereken kuruluşlar (bkz. [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Kullanıcılara katılım, taşıma ve kullanıcıların bir veya daha fazla Active Directory ormanlara, etki alanına ve yalnızca Workday HCM modülünde algılanan değişiklik bilgilerine dayalı olarak eşitlenmesini gerektiren kuruluşlar (bkz. [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* E-posta için Microsoft 365 kullanan kuruluşlar

## <a name="solution-architecture"></a>Çözüm Mimarisi

Bu bölümde, yaygın karma ortamlar için uçtan uca Kullanıcı sağlama çözüm mimarisi açıklanmaktadır. İki ilişkili akış vardır:

* **Yetkılı HR veri akışı – Workday 'den şirket içi Active Directory:** Bu akış çalışan olayları (örneğin, yeni alar, aktarımlar, sonlandırmalar), ilk olarak Cloud Workday HR kiracısında oluşur ve ardından olay verileri Azure AD ve sağlama Aracısı aracılığıyla şirket içi Active Directory akar. Olaya bağlı olarak, AD 'de oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerine yol açabilir.
* **Geri yazma akışı – şirket içi Active Directory 'Den Workday 'e:** Active Directory hesap oluşturma işlemi tamamlandıktan sonra, Azure AD Connect aracılığıyla Azure AD ile eşitlenir ve e-posta, Kullanıcı adı ve telefon numarası gibi bilgiler Workday 'e geri yazılabilir.

![Genel Bakış](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Uçtan uca Kullanıcı veri akışı

1. HR ekibi, iş günü HCM 'de çalışan işlemleri (Joiners/Taşımacılar/sızıntı veya yeni Hires/aktarımlar/sonlandırıcılar) gerçekleştirir
2. Azure AD sağlama hizmeti, iş günü HR 'dan zamanlanan kimlik eşitlemeleri çalıştırır ve şirket içi Active Directory eşitleme için işlenmesi gereken değişiklikleri tanımlar.
3. Azure AD sağlama hizmeti, AD hesabı oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerini içeren bir istek yüküne sahip şirket içi Azure AD Connect sağlama aracısını çağırır.
4. Azure AD Connect sağlama Aracısı, AD hesabı verileri eklemek/güncelleştirmek için bir hizmet hesabı kullanır.
5. Azure AD Connect/AD Eşitleme altyapısı, AD 'de güncelleştirme çekmek için Delta eşitlemesi çalıştırır.
6. Active Directory güncelleştirmeleri Azure Active Directory ile eşitlenir.
7. [Workday geri yazma](workday-writeback-tutorial.md) uygulaması yapılandırılırsa, Workday 'e e-posta, Kullanıcı adı ve telefon numarası gibi geri öznitelikler yazar.

## <a name="planning-your-deployment"></a>Dağıtımınızı planlama

İş Workday 'i Active Directory Kullanıcı hazırlama için yapılandırmak, gibi farklı yönleri kapsayan önemli bir planlama gerektirir:
* Azure AD Connect sağlama aracısının kurulumu 
* Dağıtılacak AD Kullanıcı sağlama uygulamalarının iş günü sayısı
* Doğru eşleşen tanımlayıcı, öznitelik eşleme, dönüşüm ve kapsam filtrelerini seçme

Kapsamlı yönergeler ve önerilen en iyi uygulamalar için lütfen [bulut HR dağıtım planına](../app-provisioning/plan-cloud-hr-provision.md) bakın. 

## <a name="configure-integration-system-user-in-workday"></a>Workday 'de tümleştirme sistemi kullanıcısını yapılandırma

Tüm Workday sağlama bağlayıcılarının ortak bir gereksinimi, Workday Insan kaynakları API 'sine bağlanmak için bir Workday tümleştirme sistem kullanıcısının kimlik bilgilerini gerektirmesidir. Bu bölümde, Workday 'de bir tümleştirme sistem kullanıcısının nasıl oluşturulduğu ve aşağıdaki bölümlere sahip olduğu açıklanmaktadır:

* [Tümleştirme sistemi kullanıcısı oluşturma](#creating-an-integration-system-user)
* [Tümleştirme güvenlik grubu oluşturma](#creating-an-integration-security-group)
* [Etki alanı güvenlik ilkesi izinlerini yapılandırma](#configuring-domain-security-policy-permissions)
* [İş süreci güvenlik ilkesi izinlerini yapılandırma](#configuring-business-process-security-policy-permissions)
* [Güvenlik ilkesi değişikliklerini etkinleştirme](#activating-security-policy-changes)

> [!NOTE]
> Bu yordamı atlamak ve bunun yerine sistem tümleştirme hesabı olarak bir Workday küresel yönetici hesabı kullanmak mümkündür. Bu, tanıtımlar için sorunsuz çalışabilir, ancak üretim dağıtımları için önerilmez.

### <a name="creating-an-integration-system-user"></a>Tümleştirme sistemi kullanıcısı oluşturma

**Tümleştirme sistemi kullanıcısı oluşturmak için:**

1. Bir yönetici hesabı kullanarak Workday kiracınızda oturum açın. **Workday uygulamasında**, arama kutusuna kullanıcı oluştur yazın ve ardından **tümleştirme sistemi kullanıcısı oluştur**' a tıklayın.

   >[!div class="mx-imgBorder"] 
   >![Kullanıcı Oluştur](./media/workday-inbound-tutorial/wd_isu_01.png "Kullanıcı oluştur")
2. Yeni bir tümleştirme sistemi kullanıcısı için bir Kullanıcı adı ve parola sağlayarak **tümleştirme sistemi kullanıcısı oluştur** görevini doldurun.  

   * Bu Kullanıcı programlı olarak oturum **açacağından, bir sonraki oturum açma seçeneğinde yeni parola gerektir** seçeneğini işaretlenmemiş olarak bırakın.
   * **Oturum zaman aşımı dakikalarını** varsayılan değeri olan 0 olarak bırakın, bu, kullanıcının oturumlarının zamanından önce tükenme süresini engeller.
   * Tümleştirme sisteminin parolasının Workday 'de oturum açmasını engelleyen bir güvenlik katmanı sağladığından, Kullanıcı **arabirimi oturumlarına Izin verme** seçeneğini belirleyin.

   > [!div class="mx-imgBorder"]
   > ![Tümleştirme sistemi kullanıcısı oluştur](./media/workday-inbound-tutorial/wd_isu_02.png "Tümleştirme sistemi kullanıcısı oluştur")

### <a name="creating-an-integration-security-group"></a>Tümleştirme güvenlik grubu oluşturma

Bu adımda, Workday 'de kısıtlanmamış veya kısıtlanmış bir tümleştirme sistem güvenlik grubu oluşturacak ve önceki adımda oluşturulan Tümleştirme sistemi kullanıcısını bu gruba atayacaksınız.

**Bir güvenlik grubu oluşturmak için:**

1. Arama kutusuna güvenlik grubu oluştur yazın ve ardından **güvenlik grubu oluştur**' a tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Arama kutusuna "güvenlik grubu oluştur" ve arama sonuçlarında görüntülenen "güvenlik grubu oluştur-görev" i gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. **Güvenlik grubu oluştur** görevini doldurun. 

   * Workday 'de iki tür güvenlik grubu vardır:
     * **Kısıtlanmış değil:** Güvenlik grubunun tüm üyeleri güvenlik grubu tarafından güvenliği sağlanmış tüm veri örneklerine erişebilir.
     * **Kısıtlanmış:** Tüm güvenlik grubu üyeleri, güvenlik grubunun erişebileceği veri örneklerinin (satırlar) bir alt kümesine bağlamsal erişime sahiptir.
   * Tümleştirme için uygun güvenlik grubu türünü seçmek için lütfen Workday Tümleştirme iş ortağınıza başvurun.
   * Grup türünü öğrendikten sonra, **Kiracı güvenlik grubu** açılır listesinden **tümleştirme sistem güvenlik grubu (Kısıtlanmamış)** veya **tümleştirme sistemi güvenlik grubu ' nu (kısıtlanmış)** seçin.

     > [!div class="mx-imgBorder"]
     >![CreateSecurity grubu](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grubu")

3. Güvenlik grubu oluşturma başarılı olduktan sonra güvenlik grubuna üye atayabileceğiniz bir sayfa görürsünüz. Önceki adımda oluşturulan yeni tümleştirme sistemi kullanıcısını bu güvenlik grubuna ekleyin. *Kısıtlanmış* güvenlik grubu kullanıyorsanız, uygun kuruluş kapsamını da seçmeniz gerekir.

   >[!div class="mx-imgBorder"]
   >![Güvenlik grubunu Düzenle](./media/workday-inbound-tutorial/wd_isu_05.png "Güvenlik grubunu Düzenle")

### <a name="configuring-domain-security-policy-permissions"></a>Etki alanı güvenlik ilkesi izinlerini yapılandırma

Bu adımda, güvenlik grubuna çalışan verileri için "etki alanı güvenliği" ilkesi izinleri verirsiniz.

**Etki alanı güvenlik ilkesi izinlerini yapılandırmak için:**

1. Arama kutusuna **güvenlik grubu üyeliği ve erişim** girin ve rapor bağlantısına tıklayın.
   >[!div class="mx-imgBorder"]
   >![Güvenlik grubu üyeliğini ara](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. Önceki adımda oluşturulan güvenlik grubunu arayın ve seçin. 
   >[!div class="mx-imgBorder"]
   >![Güvenlik grubu seç](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. Grup adının yanındaki üç nokta (...) simgesine tıklayın ve menüden güvenlik grubu **Için etki alanı Izinlerini koru > güvenlik** grubu ' nu seçin.
   >[!div class="mx-imgBorder"]
   >![Etki alanı Izinlerini koru seçeneğini belirleyin](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. **Tümleştirme izinleri** altında, **yerleştirme erişimi Izni veren etki alanı güvenlik ilkelerine** aşağıdaki etki alanlarını ekleyin
   * *Dış hesap sağlama*
   * *Çalışan verileri: ortak çalışan raporları* 
   * *Kişi verileri: Iş Iletişim bilgileri* (Azure AD 'den Workday 'ye yönelik iletişim verilerini geri yazmayı planlıyorsanız gereklidir)
   * *Workday hesapları* (Azure AD 'den Workday 'e geri yazmayı planlıyorsanız)

1. **Tümleştirme izinleri** altında, **erişim Izni vermek Için etki alanı güvenlik ilkelerine** aşağıdaki etki alanlarını ekleyin
   * *Çalışan verileri: çalışanlar*
   * *Çalışan verileri: tüm pozisyonlar*
   * *Çalışan verileri: geçerli personel bilgileri*
   * *Çalışan verileri: çalışan profilinde Iş unvanı*
   * *Çalışan verileri: nitelikli çalışanlar* (isteğe bağlı-sağlama için çalışan nitelik verilerini almak üzere bunu ekleyin)
   * *Çalışan verileri: yetenekler ve deneyim* (isteğe bağlı-sağlama için çalışan becerileri verilerini almak üzere bunu ekleyin)

1. Yukarıdaki adımları tamamladıktan sonra, izinler ekranı aşağıda gösterildiği gibi görünür:
   >[!div class="mx-imgBorder"]
   >![Tüm etki alanı güvenlik Izinleri](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. Yapılandırmayı gerçekleştirmek için **Tamam** ' a ve sonraki ekranda **bitti** ' ye tıklayın. 

### <a name="configuring-business-process-security-policy-permissions"></a>İş süreci güvenlik ilkesi izinlerini yapılandırma

Bu adımda, güvenlik grubuna çalışan verileri için "iş işlem güvenliği" ilkesi izinleri vereceksiniz. 

> [!NOTE]
> Bu adım yalnızca Workday geri yazma uygulama bağlayıcısının kurulması için gereklidir.

**İş süreci güvenlik ilkesi izinlerini yapılandırmak için:**

1. Arama kutusuna **Iş süreci ilkesini** girin ve sonra **Iş sürecini Düzenle güvenlik ilkesi** görevi ' ne tıklayın.  

   >[!div class="mx-imgBorder"]
   >![Arama kutusunda "Iş Işlem Ilkesi" ve "Iş sürecini Düzenle Güvenlik Ilkesi-görev" nin seçili olduğu ekran görüntüsü.](./media/workday-inbound-tutorial/wd_isu_12.png "İş süreci güvenlik Ilkeleri")  

2. **Iş süreci türü** metin kutusunda, *iletişim* araması yapın ve **iş iletişim değişikliği değiştirme** iş işlemini seçin ve **Tamam**' a tıklayın.

   >[!div class="mx-imgBorder"]
   >!["İş süreci türü" menüsünde "iş süreci güvenlik Ilkesini Düzenle" sayfasını ve "Iş Iletişim değişikliğini Düzenle" öğesini gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wd_isu_13.png "İş süreci güvenlik Ilkeleri")  

3. **Iş süreci güvenlik Ilkesini Düzenle** sayfasında, **Iş iletişim bilgilerini değiştir (Web hizmeti)** bölümüne gidin.


4. Web Hizmetleri isteğini başlatabilecek güvenlik grupları listesine yeni tümleştirme sistemi güvenlik grubunu seçin ve ekleyin. 

   >[!div class="mx-imgBorder"]
   >![İş süreci güvenlik Ilkeleri](./media/workday-inbound-tutorial/wd_isu_15.png "İş süreci güvenlik Ilkeleri")  

5. **Bitti**' ye tıklayın. 

### <a name="activating-security-policy-changes"></a>Güvenlik ilkesi değişikliklerini etkinleştirme

**Güvenlik ilkesi değişikliklerini etkinleştirmek için:**

1. Arama kutusuna etkinleştir ' i girin ve ardından **bekleyen Güvenlik Ilkesi değişikliklerini etkinleştir**' e tıklayın.
   >[!div class="mx-imgBorder"]
   >![Etkinleştir](./media/workday-inbound-tutorial/wd_isu_16.png "Etkinleştir")

1. Denetim amaçlarıyla bir açıklama girerek bekleyen Güvenlik Ilkesi değişikliklerini etkinleştir görevini başlatın ve ardından **Tamam**' a tıklayın.
1. Onay kutusu **Onayla**' yı işaretleyerek görevi bir sonraki ekranda doldurun ve ardından **Tamam**' a tıklayın.

   >[!div class="mx-imgBorder"]
   >![Bekleyen güvenliği etkinleştir](./media/workday-inbound-tutorial/wd_isu_18.png "Bekleyen güvenliği etkinleştir")  

## <a name="provisioning-agent-installation-prerequisites"></a>Aracı yükleme önkoşullarını sağlama

Sonraki bölüme geçmeden önce [sağlama Aracısı yükleme önkoşullarını](../cloud-sync/how-to-prerequisites.md) gözden geçirin. 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday 'den Kullanıcı sağlamasını Active Directory olarak yapılandırma

Bu bölümde, tümleştirmenizi kapsamındaki her bir Active Directory etki alanı için Workday 'den Kullanıcı hesabı sağlama adımları sağlanır.

* [Sağlama bağlayıcı uygulamasını ekleyin ve sağlama aracısını indirin](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Şirket içi sağlama aracılarını yükleyip yapılandırın](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Workday ve Active Directory bağlantısını yapılandırma](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Öznitelik eşlemelerini yapılandırma](#part-4-configure-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1. kısım: sağlama bağlayıcı uygulamasını ekleme ve sağlama aracısını indirme

**İş Workday 'yi sağlamayı Active Directory üzere yapılandırmak için:**

1. <https://portal.azure.com> öğesine gidin.

2. Azure portal, araması yapın ve **Azure Active Directory** seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve **Tüm** kategorisini seçin.

5. **Kullanıcı sağlamayı Active Directory Için Workday**'i arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama**' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirin.

8. Sağlama aracısını indirmek için görüntülenecek bilgi başlığına tıklayın. 

   >[!div class="mx-imgBorder"]
   >![Aracıyı indir](./media/workday-inbound-tutorial/pa-download-agent.png "Aracı ekranını indir")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2. Bölüm: şirket içi sağlama aracılarını yükleyip yapılandırın

Şirket içi Active Directory sağlamak için, sağlama aracısının, istenen Active Directory etki alanına ağ erişimi olan, etki alanına katılmış bir sunucuya yüklenmesi gerekir.

İndirilen Aracı yükleyicisini sunucu konağına aktarın ve aracı yapılandırmasını gerçekleştirmek için [ **aracıyı yükleme** bölümünde](../cloud-sync/how-to-install.md) listelenen adımları izleyin.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>3. kısım: sağlama uygulamasında, Workday ve Active Directory bağlantısını yapılandırın
Bu adımda, Workday ve Azure portal Active Directory ile bağlantı kuruyoruz. 

1. Azure portal, [1. bölümde](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) oluşturulan kullanıcı sağlama uygulamasının Active Directory için Workday 'e geri dönün

1. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Workday Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şu şekilde görünmelidir: **Kullanıcı adı \@ tenant_name**

   * **İşgünü parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Workday Web HIZMETLERI API URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. URL, bağlayıcı tarafından kullanılan Workday Web Hizmetleri API 'sinin sürümünü belirler. 

     | URL biçimi | WWS API sürümü kullanıldı | XPATH değişiklikleri gerekiyor |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > URL 'de sürüm bilgisi belirtilmemişse, uygulama Workday Web Hizmetleri (WWS) v 21.1 kullanır ve uygulamayla birlikte gelen varsayılan XPATH API ifadelerinde hiçbir değişiklik yapılması gerekmez. Belirli bir WWS API sürümünü kullanmak için, URL 'de sürüm numarasını belirtin <br>
     > Örnek: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> WWS API v 30.0 + kullanıyorsanız, sağlama işini açmadan önce lütfen **öznitelik eşlemesi-> gelişmiş seçenekler** altında **XPath apı deyimlerini** güncelleştirin > yapılandırma ve [Workday öznitelik başvurusunu](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [yönetme](#managing-your-configuration) bölümüne başvuran Workday için öznitelik listesini düzenleyin.  

   * **Active Directory orman-** Active Directory etki alanının "adı", aracıyla birlikte kaydedilir. Sağlama için hedef etki alanını seçmek üzere açılan menüyü kullanın. Bu değer genellikle şöyle bir dizedir: *contoso.com*

   * **Active Directory kapsayıcı-** Aracının varsayılan olarak Kullanıcı hesapları oluşturması gereken kapsayıcı DN 'sini girin.
        Örnek: *OU = standart kullanıcılar, OU = kullanıcılar, DC = contoso, DC = test*

     > [!NOTE]
     > Bu ayar yalnızca *parentDistinguishedName* özniteliği öznitelik eşlemelerinde yapılandırılmamışsa, Kullanıcı hesabı oluşturmaları için oynatılır. Bu ayar Kullanıcı arama veya güncelleştirme işlemleri için kullanılmaz. Tüm etki alanı alt ağacının, arama işleminin kapsamında olması.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

     > [!NOTE]
     > Azure AD sağlama hizmeti, sağlama işi [karantina](../app-provisioning/application-provisioning-quarantine-status.md) durumuna geçtiğinde e-posta bildirimi gönderir.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, aracı kurulumunda yapılandırılan Workday kimlik bilgilerinin ve AD kimlik bilgilerinin geçerli olduğundan emin olun.

     >[!div class="mx-imgBorder"]
     >![Girilen kimlik bilgilerine sahip "sağlama" sayfasını gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wd_1.png)

   * Kimlik bilgileri başarıyla kaydedildikten sonra, **eşlemeler** bölümü Iş **günü çalışanlarını şirket içi olarak eşitlemesini** varsayılan eşlemeyi gösterir Active Directory

### <a name="part-4-configure-attribute-mappings"></a>Bölüm 4: öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin Workday 'den Active Directory 'e nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler** altındaki sağlama sekmesinde, Iş **günü çalışanlarını şirket Içi Active Directory ile senkronize** et ' e tıklayın.

1. **Kaynak nesne kapsamı** alanında, bir öznitelik tabanlı filtre kümesi tanımlayarak, Workday 'de hangi Kullanıcı kümelerinin ad sağlamak için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam "Workday 'deki tüm kullanıcılar" dır. Örnek filtreler:

   * Örnek: 1000000 ve 2000000 arasında çalışan kimlikleri olan kullanıcılara kapsam (2000000 hariç)

      * Öznitelik: Workerıd

      * İşleç: REGEX eşleşmesi

      * Değer: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Örnek: yalnızca çalışanlar ve çalışanların çalışanları yoktur

      * Öznitelik: EmployeeID

      * İşleç: NULL DEĞIL

   > [!TIP]
   > Sağlama uygulamasını ilk kez yapılandırırken, istenen sonucu elde ettiğinizden emin olmak için öznitelik eşlemelerinizi ve ifadelerini test etmeniz ve doğrulamanız gerekir. Microsoft, iş Workday 'den birkaç test kullanıcısı ile eşlemelerinizi test etmek için **kaynak nesne kapsamı** ve [isteğe bağlı sağlama](../app-provisioning/provision-on-demand.md) kapsamında [kapsam filtrelerin](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) kullanılmasını önerir. Eşlemelerin çalıştığını doğruladıktan sonra filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

   > [!CAUTION] 
   > Sağlama altyapısının varsayılan davranışı, kapsam dışına çıkan kullanıcıları devre dışı bırakmak/silmektir. Bu, Workday 'de AD tümleştirmesi için istenmeyebilir. Bu varsayılan davranışı geçersiz kılmak için, [kapsam dışı olan kullanıcı hesaplarını silmeyi atlama](../app-provisioning/skip-out-of-scope-deletions.md) makalesine başvurun.

1. **Hedef nesne eylemleri** alanında Active Directory üzerinde gerçekleştirilen eylemleri genel olarak filtreleyebilirsiniz. **Oluşturma** ve **güncelleştirme** en yaygın olarak kullanılır.

1. **Öznitelik eşlemeleri** bölümünde, bağımsız Workday özniteliklerinin Active Directory özniteliklerle nasıl eşlendiğini tanımlayabilirsiniz.

1. Güncelleştirmek için varolan bir öznitelik eşlemesine tıklayın veya yeni eşlemeler eklemek için ekranın alt kısmındaki **Yeni eşleme Ekle** ' ye tıklayın. Tek bir öznitelik eşlemesi bu özellikleri destekler:

      * **Eşleme türü**

         * **Doğrudan** – Workday özniteliğinin değerini hiçbir DEĞIŞIKLIK olmadan ad özniteliğine yazar

         * **Sabit** -ad özniteliğine statik, sabit bir dize değeri yaz

         * **İfade** : bir veya daha fazla Workday özniteliğine göre, AD özniteliğine özel bir değer yazmanızı sağlar. [Daha fazla bilgi için ifadelerde bu makaleye bakın](../app-provisioning/functions-for-customizing-application-data.md).

      * **Kaynak özniteliği** -Workday 'den Kullanıcı özniteliği. Aradığınız öznitelik yoksa, bkz. [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes).

      * **Varsayılan değer** : isteğe bağlı. Kaynak özniteliğinde boş bir değer varsa, eşleme bu değeri yazar.
            En yaygın yapılandırma bu boş bırakılmamalıdır.

      * **Target özniteliği** – Active Directory içindeki kullanıcı özniteliği.

      * **Bu özniteliği kullanarak nesneleri Eşleştir** – bu eşlemenin, Workday ve Active Directory arasındaki kullanıcıları benzersiz şekilde tanımlamak için kullanılıp kullanılmayacağını belirtir. Bu değer genellikle Active Directory iş günü için çalışan KIMLIĞI alanında ayarlanır. Bu, genellikle içindeki çalışan KIMLIĞI özniteliklerinden birine eşlenir.

      * **Eşleşen öncelik** – birden çok eşleşen öznitelik ayarlanabilir. Birden çok olduğunda, bu alan tarafından tanımlanan sıraya göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

      * **Bu eşlemeyi Uygula**

         * **Her zaman** : Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine Uygula

         * **Yalnızca oluşturma sırasında** -bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde Uygula

1. Eşlemelerinizi kaydetmek için Attribute-Mapping bölümünün en üstündeki **Kaydet** ' e tıklayın.
   >[!div class="mx-imgBorder"]
   >!["Kaydet" eyleminin seçildiği "öznitelik eşleme" sayfasını gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Aşağıda, bazı ortak ifadelerle Workday ve Active Directory arasındaki bazı örnek öznitelik eşlemeleri verilmiştir

* *ParentDistinguishedName* özniteliğiyle eşlenen ifade, bir veya daha fazla Workday kaynak özniteliğini temel alan farklı OU 'lara Kullanıcı sağlamak için kullanılır. Bu örnek, kullanıcıları farklı OU 'Lara, içinde oldukları şehirlere göre yerleştirirler.

* Active Directory ' deki *userPrincipalName* özniteliği, hedef ad etki alanında oluşturulan bir değerin varlığını denetleyen ve yalnızca benzersiz olduğunda ayarlayan, [Selectuniquevalue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) işlevi kullanılarak oluşturulur.  

* [İfadeleri buraya yazmak için bir belge vardır](../app-provisioning/functions-for-customizing-application-data.md). Bu bölüm, özel karakterlerin nasıl kaldırılacağını gösteren örnekler içerir.

| WORKDAY ÖZNITELIĞI | ACTIVE DIRECTORY ÖZNITELIĞI |  EŞLEŞEN KIMLIK? | OLUŞTUR/GÜNCELLEŞTIR |
| ---------- | ---------- | ---------- | ---------- |
| **Workerıd**  |  Çalışan Kimliği | **Evet** | Yalnızca oluşturma sırasında yazılmıştır |
| **PreferredNameData**    |  ,    |   |   Yalnızca oluşturma sırasında yazılmıştır |
| **SelectUniqueValue (JOIN (" \@ ", JOIN (".",  \[ FirstName \] , \[ LastName \] ), "contoso.com"), JOIN (" \@ ", JOIN (".", Mid ( \[ FirstName \] , 1, 1), \[ LastName \] ), "contoso.com"), JOIN (bir \@ "", JOIN (".", Mid ( \[ FirstName \] , 1, 2), \[ LastName \] ), "contoso.com"))**   | userPrincipalName     |     | Yalnızca oluşturma sırasında yazılmıştır 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Yalnızca oluşturma sırasında yazılmıştır |
| **Anahtar ( \[ etkin \] ,, "0", "true", "1", "false")** |  accountDisabled      |     | Oluştur + güncelleştir |
| **FirstName**   | givenName       |     |    Oluştur + güncelleştir |
| **LastName**   |   sn   |     |  Oluştur + güncelleştir |
| **PreferredNameData**  |  displayName |     |   Oluştur + güncelleştir |
| **Şirket**         | şirket   |     |  Oluştur + güncelleştir |
| **Supervisorspganleştirme**  | bölüm  |     |  Oluştur + güncelleştir |
| **ManagerReference**   | manager  |     |  Oluştur + güncelleştir |
| **Businestıtle**   |  başlık     |     |  Oluştur + güncelleştir | 
| **AddressLineData**    |  streetAddress  |     |   Oluştur + güncelleştir |
| **Belediye**   |   l   |     | Oluştur + güncelleştir |
| **CountryReferenceTwoLetter**      |   co |     |   Oluştur + güncelleştir |
| **CountryReferenceTwoLetter**    |  c  |     |         Oluştur + güncelleştir |
| **CountryRegionReference** |  st     |     | Oluştur + güncelleştir |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Oluştur + güncelleştir |
| **PostalCode**  |   postalCode  |     | Oluştur + güncelleştir |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Oluştur + güncelleştir |
| **Faks**      | facsimileTelephoneNumber 'dir     |     |    Oluştur + güncelleştir |
| **Cep**  |    mobil       |     |       Oluştur + güncelleştir |
| **LocalReference** |  preferredLanguage  |     |  Oluştur + güncelleştir |                                               
| **Switch ( \[ municipitesi \] , "OU = varsayılan KULLANıCıLAR, DC = contoso, DC = com", "Dallas", "OU = Dallas, OU = KULLANıCıLAR, DC = contoso, DC = com", "Austin", "OU = AUSTIN, OU = KULLANıCıLAR, DC = contoso, DC = com", "Seattle", "OU = Seattle, OU = KULLANıCıLAR, DC = contoso, DC = com", "Londra", "OU = Londra, OU = KULLANıCıLAR, DC = contoso, DC = com")**  | parentDistinguishedName     |     |  Oluştur + güncelleştir |

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, [isteğe bağlı sağlamayı](../app-provisioning/provision-on-demand.md) kullanarak tek bir kullanıcı için sağlamayı test edebilir ve ardından [Kullanıcı sağlama hizmetini etkinleştirip başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Workday sağlama uygulama yapılandırması tamamlandıktan sonra ve [isteğe bağlı sağlama](../app-provisioning/provision-on-demand.md)ile tek bir kullanıcı için sağlamayı doğruladıktan sonra, Azure Portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşleme veya Workday veri sorunlarında hatalar varsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi, [isteğe bağlı sağlama](../app-provisioning/provision-on-demand.md) kullanarak birkaç test kullanıcısı ile test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** dikey penceresine gidin ve **sağlamayı Başlat**' a tıklayın.

1. Bu işlem, iş günü kiracısında kaç Kullanıcı olduğuna bağlı olarak değişken sayıda saat sürebilen ilk eşitlemeyi başlatacak. İlerleme çubuğunu, eşitleme döngüsünün ilerlemesini izlemek için kontrol edebilirsiniz. 

1. Herhangi bir zamanda, sağlama hizmeti 'nin gerçekleştirdiği işlemleri görmek için Azure portal **Denetim günlükleri** sekmesini kontrol edin. Denetim günlükleri, sağlama hizmeti tarafından gerçekleştirilen tüm bireysel eşitleme olaylarını listeler (örneğin, Workday 'den okuma ve daha sonra Active Directory için eklenen veya güncellenen). Denetim günlüklerinin incelenmesi ve sağlama hatalarının düzeltilmesi hakkındaki yönergeler için sorun giderme bölümüne bakın.

1. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesinde bir denetim Özeti raporu yazar.
   > [!div class="mx-imgBorder"]
   > ![Sağlama ilerleme çubuğu](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>Sık Sorulan Sorular (SSS)

* **Çözüm yeteneği soruları**
  * [Workday 'den yeni bir işe alma işlemi sırasında, çözüm Active Directory yeni kullanıcı hesabının parolasını nasıl ayarladı?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Çözüm, sağlama işlemleri tamamlandıktan sonra e-posta bildirimleri göndermeyi destekliyor mu?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Çözüm, Azure AD bulutu 'nda veya sağlama Aracısı katmanında iş günü Kullanıcı profillerini önbelleğe ister misiniz?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Çözüm, kullanıcıya şirket içi AD grupları atamayı destekliyor mu?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Çözüm iş günü çalışan profillerini sorgulamak ve güncelleştirmek için hangi Workday API 'Leri kullanır?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Workday HCM kiracımı iki Azure AD kiracısıyla yapılandırabilir miyim?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Nasıl yaparım?, iş günü ve Azure AD tümleştirmesiyle ilgili iyileştirmeler önerin veya yeni özellikler ister misiniz?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Aracı sorularını sağlama**
  * [Sağlama aracısının GA sürümü nedir?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Nasıl yaparım? sağlama aracımın sürümü bilinmiyor mu?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft, sağlama Aracısı güncelleştirmelerini otomatik olarak gönderir mi?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Sağlama aracısını Azure AD Connect çalıştıran aynı sunucuya yükleyebilir miyim?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Nasıl yaparım? sağlama aracısını giden HTTP iletişimi için bir proxy sunucu kullanacak şekilde yapılandırmak istiyor musunuz?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Nasıl yaparım? sağlama aracısının Azure AD kiracısıyla iletişim kurabildiğinden ve aracı tarafından istenen bağlantı noktalarını engellemediğinden emin olun.](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Sağlama aracıma ilişkili etki alanını Nasıl yaparım? de kaydetmek istiyor musunuz?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Sağlama aracısını kaldırmak Nasıl yaparım? mı?](#how-do-i-uninstall-the-provisioning-agent)

* **AD özniteliği eşleme ve yapılandırma sorularına iş günü**
  * [Nasıl yaparım? Workday sağlama özniteliği eşleşmesinin ve şemadaki çalışma kopyasını yedekleyin veya dışarı aktarın.](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Workday ve Active Directory özel öznitelikleri var. Nasıl yaparım? çözümü özel özniteliklerim ile çalışacak şekilde yapılandırmak mı istiyorsunuz?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [İş gününde Kullanıcı fotoğrafını Active Directory sağlayabilir miyim?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Nasıl yaparım? ortak kullanım için Kullanıcı onayı temelinde Workday 'den cep telefonu numaralarını eşitlemek mi istiyorsunuz?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Nasıl yaparım?, kullanıcının departmanı/ülke/şehir özniteliklerine göre AD biçiminde görünen adları ve bölgesel sapmaları idare ediyor mu?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [SamAccountName özniteliği için benzersiz değerler oluşturmak üzere SelectUniqueValue özelliğini nasıl kullanabilirim?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Nasıl yaparım? karakterleri aksanlarla kaldırıp normal Ingilizce alfaberine dönüştürür mi?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Çözüm yeteneği soruları

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Workday 'den yeni bir işe alma işlemi sırasında, çözüm Active Directory yeni kullanıcı hesabının parolasını nasıl ayarladı?

Şirket içi sağlama Aracısı yeni bir AD hesabı oluşturmak için bir istek aldığında, otomatik olarak AD sunucusu tarafından tanımlanan parola karmaşıklığı gereksinimlerini karşılayacak şekilde tasarlanan karmaşık bir rastgele parola oluşturur ve bunu Kullanıcı nesnesi üzerinde ayarlar. Bu parola her yerde günlüğe kaydedilmez.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Çözüm, sağlama işlemleri tamamlandıktan sonra e-posta bildirimleri göndermeyi destekliyor mu?

Hayır, sağlama işlemleri tamamlandıktan sonra e-posta bildirimleri göndermek geçerli sürümde desteklenmez.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Çözüm, Azure AD bulutu 'nda veya sağlama Aracısı katmanında iş günü Kullanıcı profillerini önbelleğe ister misiniz?

Hayır, çözüm Kullanıcı profillerinin bir önbelleğini korumaz. Azure AD sağlama hizmeti yalnızca bir veri işlemcisi olarak davranır, Workday 'den verileri okur ve hedef Active Directory ya da Azure AD 'ye yazma. Kullanıcı gizliliği ve veri saklama ile ilgili ayrıntılar için [kişisel verileri yönetme](#managing-personal-data) bölümüne bakın.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Çözüm, kullanıcıya şirket içi AD grupları atamayı destekliyor mu?

Bu işlev şu anda desteklenmiyor. Önerilen geçici çözüm, [Denetim günlüğü verilerine](/graph/api/resources/azure-ad-auditlog-overview) YÖNELIK Microsoft Graph API uç noktasını sorgulayan ve Grup atama gibi senaryoları tetiklemek için bunu kullanan bir PowerShell betiği dağıtmaktır. Bu PowerShell betiği bir görev zamanlayıcısına iliştirilebilir ve sağlama Aracısı 'nı çalıştıran aynı kutuya dağıtılabilir.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Çözüm iş günü çalışan profillerini sorgulamak ve güncelleştirmek için hangi Workday API 'Leri kullanır?

Çözüm şu anda aşağıdaki Workday API 'Lerini kullanmaktadır:

* **Yönetici kimlik bilgileri** bölümünde kullanılan **Workday Web Hizmetleri API URL 'si** BIÇIMI Get_Workers için kullanılan API sürümünü belirler
  * URL biçimi: https:// \# \# \# \# \. Workday \. com/CCX/Service/TenantName ise, API v 21.1 kullanılır. 
  * URL biçimi: https:// \# \# \# \# \. Workday \. com/CCX/Service/TenantName/insan \_ kaynakları ise, API v 21.1 kullanılır 
  * URL biçimi: https:// \# \# \# \# \. Workday \. com/CCX/Service/TenantName/insan \_ kaynakları/v ise \# \# \. \# , belirtilen API sürümü kullanılır. (Örnek: v 34.0 belirtilmişse, kullanılır.)  

* Workday e-posta geri yazma özelliği Change_Work_Contact_Information kullanır (v 30.0) 
* İşgünü Kullanıcı adı geri yazma özelliği Update_Workday_Account kullanır (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Workday HCM kiracımı iki Azure AD kiracısıyla yapılandırabilir miyim?

Evet, bu yapılandırma desteklenir. Bu senaryoyu yapılandırmak için üst düzey adımlar aşağıda verilmiştir:

* #1 sağlama aracısını dağıtın ve Azure AD kiracı #1 ile kaydedin.
* #2 sağlama aracısını dağıtın ve Azure AD kiracı #2 ile kaydedin.
* Her bir sağlama aracısının yöneteceği "alt etki alanları" temelinde, her aracıyı etki alanı (ler) ile yapılandırın. Bir aracı birden çok etki alanını işleyebilir.
* Azure portal, Workday 'yi her kiracıda AD Kullanıcı sağlama uygulaması olarak ayarlayın ve ilgili etki alanlarıyla yapılandırın.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Nasıl yaparım?, iş günü ve Azure AD tümleştirmesiyle ilgili iyileştirmeler önerin veya yeni özellikler ister misiniz?

Geri bildiriminiz, gelecekteki sürümlerin ve geliştirmelerin yönünü belirlememize yardımcı olduğu için oldukça önemlidir. Tüm geri bildirimlerinizi destekliyoruz ve [Azure AD 'nin geri bildirim forumuna](https://feedback.azure.com/forums/169401-azure-active-directory)fikir veya geliştirme önerilerinizi göndermenize önerilir. Workday tümleştirmesiyle ilgili belirli geri bildirimler için, iş Workday ile ilgili mevcut geri bildirimleri bulmak için Workday *uygulamalarını* seçin ve *Workday* anahtar sözcüklerini kullanarak arama yapın.

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS uygulamaları](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Yeni bir fikir önerirken, başka birinin zaten benzer bir özelliği önerdiğinden emin olun. Bu durumda, özelliği veya geliştirme isteğini oylayabilirsiniz. Ayrıca, fikir desteğiniz için destek göstermek ve özelliğin sizin için nasıl değerli olacağını göstermek için, özel kullanım durumu ile ilgili bir yorum bırakabilirsiniz.

### <a name="provisioning-agent-questions"></a>Aracı sorularını sağlama

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Sağlama aracısının GA sürümü nedir?

Sağlama aracısının en son GA sürümü için [Azure AD Connect sağlama aracısına bakın: sürüm yayınlama geçmişi](../app-provisioning/provisioning-agent-release-version-history.md) .  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Nasıl yaparım? sağlama aracımın sürümü bilinmiyor mu?

* Sağlama aracısının yüklendiği Windows Server 'da oturum açın.
* **Denetim Masası**'na git  ->  **bir program menüsünü kaldırma veya değiştirme**
* Girişe karşılık gelen sürümü ara **Microsoft Azure AD sağlama aracısına Bağlan**

  >[!div class="mx-imgBorder"]
  >![Azure portalda](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft, sağlama Aracısı güncelleştirmelerini otomatik olarak gönderir mi?

Evet, Windows Service **Microsoft Azure AD Connect Agent güncelleştiricisi** çalışır duruma ayarlanırsa Microsoft sağlama aracısını otomatik olarak güncelleştirir.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Sağlama aracısını Azure AD Connect çalıştıran aynı sunucuya yükleyebilir miyim?

Evet, sağlama aracısını Azure AD Connect çalıştıran aynı sunucuya yükleyebilirsiniz.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Yapılandırma sırasında, sağlama Aracısı Azure AD yöneticisi kimlik bilgilerini ister. Aracı kimlik bilgilerini sunucuda yerel olarak depolar mı?

Yapılandırma sırasında, sağlama Aracısı Azure AD yönetici kimlik bilgilerinin yalnızca Azure AD kiracınıza bağlanmasını ister. Kimlik bilgilerini sunucuda yerel olarak depolamaz. Ancak, yerel bir Windows parola kasasındaki *Şirket içi Active Directory etki alanına* bağlanmak için kullanılan kimlik bilgilerini korur.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Nasıl yaparım? sağlama aracısını giden HTTP iletişimi için bir proxy sunucu kullanacak şekilde yapılandırmak istiyor musunuz?

Sağlama Aracısı giden ara sunucu kullanımını destekler. Bunu, **C:\Program Files\Microsoft Azure AD Connect sağlama Agent\AADConnectProvisioningAgent.exe.config** aracı yapılandırma dosyasını düzenleyerek yapılandırabilirsiniz. Aşağıdaki satırları, kapanış etiketinden hemen önceki dosyanın sonuna doğru bir şekilde ekleyin `</configuration>` .
[proxy-server] ve [proxy-port] değişkenlerini ara sunucunuzun adı ve bağlantı noktası değerleriyle değiştirin.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Nasıl yaparım? sağlama aracısının Azure AD kiracısıyla iletişim kurabildiğinden ve aracı tarafından istenen bağlantı noktalarını engellemediğinden emin olun.

Ayrıca, [gerekli tüm bağlantı noktalarının](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) açık olup olmadığını da denetleyebilirsiniz.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Birden çok AD etki alanı sağlamak için bir sağlama Aracısı yapılandırılmış olabilir mi?

Evet, bir sağlama Aracısı, aracı ilgili etki alanı denetleyicilerine bir görüş satırı olduğu sürece birden çok AD etki alanını işleyecek şekilde yapılandırılabilir. Microsoft, yüksek kullanılabilirlik sağlamak ve yük devretme desteği sağlamak için aynı AD etki alanı kümesine hizmet veren 3 sağlama Aracısı grubu ayarlamayı önerir.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Sağlama aracıma ilişkili etki alanını Nasıl yaparım? de kaydetmek istiyor musunuz?

* Azure portal Azure AD kiracınızın *KIRACı kimliğini* alın.
* Sağlama Aracısı 'nı çalıştıran Windows Server 'da oturum açın.
* PowerShell 'i Windows Yöneticisi olarak açın.
* Kayıt betiklerini içeren dizine geçin ve kiracı \[ kimliği \] PARAMETRESINI kiracı kimliğiniz değeriyle değiştirerek aşağıdaki komutları çalıştırın.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Görüntülenen aracılar listesinden, `id` alanı *RESOURCENAME* değerini ad etki alanı adına eşit olan kaynaktaki bu kaynaktan kopyalayın.
* KIMLIK değerini bu komuta yapıştırın ve PowerShell 'de komutunu yürütün.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Aracı yapılandırma sihirbazını yeniden çalıştırın.
* Daha önce bu etki alanına atanan diğer aracıların yeniden yapılandırılması gerekir.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Sağlama aracısını kaldırmak Nasıl yaparım? mı?

* Sağlama aracısının yüklendiği Windows Server 'da oturum açın.
* **Denetim Masası**'na git  ->  **bir program menüsünü kaldırma veya değiştirme**
* Aşağıdaki programları kaldırın:
  * Microsoft Azure AD sağlama aracısına bağlanma
  * Microsoft Azure AD Aracısı güncelleştiricisi bağlama
  * Microsoft Azure AD sağlama Aracısı paketini bağlama

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>AD özniteliği eşleme ve yapılandırma sorularına iş günü

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Nasıl yaparım? Workday sağlama özniteliği eşleşmesinin ve şemadaki çalışma kopyasını yedekleyin veya dışarı aktarın.

Microsoft Graph API 'yi kullanarak Workday Kullanıcı sağlama yapılandırmanızı dışarı aktarabilirsiniz. Ayrıntılar için [Workday Kullanıcı sağlama özniteliği eşleme yapılandırmanızı dışarı ve içeri](#exporting-and-importing-your-configuration) aktarma bölümündeki adımlara bakın.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Workday ve Active Directory özel öznitelikleri var. Nasıl yaparım? çözümü özel özniteliklerim ile çalışacak şekilde yapılandırmak mı istiyorsunuz?

Çözüm, özel Workday ve Active Directory özniteliklerini destekler. Özel öznitelerinizi eşleme şemasına eklemek için, **öznitelik eşleme** dikey penceresini açın ve aşağı kaydırarak **Gelişmiş seçenekleri göster** bölümünü genişletin. 

![Öznitelik listesini düzenle](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Özel Workday öznitelerinizi eklemek için, *Workday için öznitelik listesini düzenle* seçeneğini belirleyin ve özel ad öznitelerinizi eklemek Için, *Şirket içi Active Directory Için öznitelik listesini düzenle* seçeneğini belirleyin.

Ayrıca bkz:

* [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Nasıl yaparım?, iş günü değişikliklerine göre yalnızca AD içindeki öznitelikleri güncelleştirecek şekilde yapılandırmak ve yeni bir AD hesabı oluşturmamalıdır.

Bu yapılandırma, aşağıda gösterildiği gibi **öznitelik eşlemeleri** dikey penceresindeki **hedef nesne eylemleri** ayarlanarak elde edilebilir:

![Eylemi Güncelleştir](./media/workday-inbound-tutorial/wd_target_update_only.png)

Yalnızca güncelleştirme işlemlerinin Workday 'den AD 'ye akmasını sağlamak için "Güncelleştir" onay kutusunu seçin. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>İş gününde Kullanıcı fotoğrafını Active Directory sağlayabilir miyim?

Çözüm şu anda Active Directory içindeki *thumbnailPhoto* ve *jpegPhoto* gibi ikili özniteliklerin ayarlanmasını desteklemez.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Nasıl yaparım? ortak kullanım için Kullanıcı onayı temelinde Workday 'den cep telefonu numaralarını eşitlemek mi istiyorsunuz?

* Workday sağlama uygulamanızın "sağlama" dikey penceresine gidin.
* Öznitelik eşlemelerine tıklayın 
* **Eşlemeler** altında, Iş **günü çalışanlarını şirket Içi Active Directory ile eşitlemeyi** (veya **Workday çalışanlarını Azure AD 'ye eşitlemeyi**) seçin.
* Öznitelik eşlemeleri sayfasında, aşağı kaydırın ve "Gelişmiş seçenekleri göster" kutusunu işaretleyin.  **Workday için öznitelik listesini düzenle** 'ye tıklayın
* Açılan dikey pencerede, "mobil" özniteliğini bulun ve ardından mobil GDPR **API ifadesini** düzenleyebilmek için satıra tıklayın. ![](./media/workday-inbound-tutorial/mobile_gdpr.png)

* **API ifadesini** , yalnızca "genel kullanım bayrağı" Workday 'de "true" olarak ayarlandıysa iş cep telefonu numarasını alan aşağıdaki yeni ifadeyle değiştirin.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Öznitelik listesini kaydedin.
* Öznitelik eşlemesini kaydedin.
* Geçerli durumu temizleyin ve tam eşitlemeyi yeniden başlatın.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Nasıl yaparım?, kullanıcının departmanı/ülke/şehir özniteliklerine göre AD biçiminde görünen adları ve bölgesel sapmaları idare ediyor mu?

Kullanıcının departmanı ve ülkesi/bölgesi hakkında bilgi de sağlamak için AD 'de *DisplayName* özniteliğini yapılandırmak yaygın bir gereksinimdir. Örneğin, John Smith ABD 'deki pazarlama bölümünde çalışıyorsa, *DisplayName* 'in *John (pazarlama-US) kadar Smith*'i göstermesini isteyebilirsiniz.

Bu tür gereksinimleri, örneğin, Şirket, iş birimi, şehir veya ülke/bölge gibi öznitelikleri içerecek şekilde *CN* veya *DisplayName* oluşturmak için nasıl işleyebileceğini açıklamaktadır.

* Her Workday özniteliği,  **öznitelik eşleme-> Gelişmiş bölüm-> Iş günü için öznitelik listesini düzenle**' de yapılandırılabilir olan temel BIR XPath API ifadesi kullanılarak alınır. Workday *adı*, *preferredlastname*, *Company* ve *supervisorspganleştirme* öznitelikleri için varsayılan XPath API ifadesi aşağıda verilmiştir.

     | Workday özniteliği | API XPATH Ifadesi |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/Text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/Text () |
     | Şirket | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' Company ']/wd:Organization_Reference/@wd:Descriptor |
     | Supervisorspganleştirme | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' supervis, ']/WD: Organization_Name/Text () |

   Workday kiracınızda, yukarıdaki API ifadesinin Workday kiracı yapılandırmanız için geçerli olduğunu doğrulayın. Gerekirse, [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)bölümünde açıklandığı gibi düzenleyebilirsiniz.

* Benzer şekilde, Workday 'de bulunan ülke/bölge bilgileri şu XPATH kullanılarak alınır: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Workday öznitelik listesi bölümünde bulunan 5 ülke/bölge ile ilgili öznitelikler vardır.

     | Workday özniteliği | API XPATH Ifadesi |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-3_Code ']/text () |
     | Countryreferencedostu | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Workday kiracınızda, yukarıdaki API ifadelerinin Workday kiracı yapılandırmanız için geçerli olduğunu doğrulayın. Gerekirse, [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)bölümünde açıklandığı gibi düzenleyebilirsiniz.

* Doğru öznitelik eşleme ifadesini oluşturmak için, kullanıcının adı, soyadı, ülkeyi/bölgeyi ve departmanı temsil eden "yetkili" iş günü özniteliğini belirler. Öznitelikleri, sırasıyla *Preferredfirstname*, *preferredlastname*, *countryreferencetwoletter* ve *supervisoryorgansılet* olduğunu varsayalım. Bunu *, Smith, John (Marketing-US)* gibi bir ekran adı almak için AŞAĞıDAKI gibi ad *DisplayName* özniteliği için bir ifade oluşturmak üzere kullanabilirsiniz.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Doğru ifadeye sahip olduktan sonra, öznitelik eşlemeleri tablosunu düzenleyin ve *DisplayName* öznitelik eşlemesini aşağıda gösterildiği gibi değiştirin:   ![ DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Yukarıdaki örneği genişleterek, Workday 'den gelen şehir adlarını bir toplu değer değerlerine dönüştürüp daha sonra *Smith, John (çi)* veya *tikan, Gamze (NYC)* gibi görünen adlar oluşturmak için bunu kullanın. Bu sonuç, genlik değişkeni olarak Workday *municipitesi* özniteliği ile bir switch ifadesi kullanılarak elde edilebilir.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Ayrıca bkz:
  * [Işlev sözdizimini Değiştir](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [JOIN Işlevi sözdizimi](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Append Işlevi sözdizimi](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>SamAccountName özniteliği için benzersiz değerler oluşturmak üzere SelectUniqueValue özelliğini nasıl kullanabilirim?

Workday 'den *FirstName* ve *LastName* özniteliklerinin bir birleşimini kullanarak *sAMAccountName* özniteliği için benzersiz değerler oluşturmak istediğinizi varsayalım. Aşağıdakiler ile başlayabilmeniz için bir ifade aşağıda verilmiştir:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Yukarıdaki ifade nasıl çalışır: Kullanıcı John Smith ise, ilk olarak jsmith 'i oluşturmaya çalışır, JSmith zaten mevcutsa, bu durumda JoSmith oluşturulur. Bu, varsa, Cansmith oluşturur. İfade Ayrıca, oluşturulan değerin, *sAMAccountName* ile ilişkili uzunluk kısıtlamasını ve özel karakter kısıtlamasını karşıladığından de emin olmanızı sağlar.

Ayrıca bkz:

* [PARÇAAL Işlevi sözdizimi](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Işlev sözdizimini Değiştir](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Işlev sözdizimi](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Nasıl yaparım? karakterleri aksanlarla kaldırıp normal Ingilizce alfaberine dönüştürür mi?

Kullanıcı için e-posta adresi veya CN değeri oluştururken, kullanıcının adı ve soyadı gibi özel karakterleri kaldırmak için [Normalizediactik](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) işlevini kullanın.

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Bu bölümde, Azure AD denetim günlükleri ve Windows Server Olay Görüntüleyicisi günlüklerini kullanarak Workday tümleştirmenizde sağlama sorunlarının nasıl giderileceği hakkında özel rehberlik sunulmaktadır. Genel sorun giderme adımları ve öğreticide yakalanan kavramların en üstünde oluşturulur [: otomatik Kullanıcı hesabı sağlama hakkında raporlama](../app-provisioning/check-status-user-account-provisioning.md)

Bu bölüm, sorun gidermeye yönelik aşağıdaki noktaları ele almaktadır:

* [Olay Görüntüleyicisi günlüklerini göstermek için sağlama aracısını yapılandırma](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [Windows Olay Görüntüleyicisi aracı sorunlarını giderme için ayarlama](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Hizmet sorunlarını giderme için Azure portal denetim günlüklerini ayarlama](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [AD Kullanıcı hesabı oluşturma işlemleri için günlükleri anlama](#understanding-logs-for-ad-user-account-create-operations)
* [Yönetici güncelleştirme işlemlerine yönelik günlükleri anlama](#understanding-logs-for-manager-update-operations)
* [Yaygın olarak karşılaşılan hatalar çözümleniyor](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>Olay Görüntüleyicisi günlüklerini göstermek için sağlama aracısını yapılandırma
1. Sağlama aracısının dağıtıldığı Windows Server makinesinde oturum açın
1. Hizmeti durdurma **Microsoft Azure AD sağlama aracısını bağlama**.
1. Özgün yapılandırma dosyasının bir kopyasını oluşturun: *C:\Program Files\Microsoft Azure AD Connect sağlama Agent\AADConnectProvisioningAgent.exe.config*.
1. Var olan `<system.diagnostics>` bölümü aşağıdaki kodla değiştirin. 
   * Dinleyici yapılandırması **ETW** , Iletileri eventviewer günlüklerine yayar
   * Listener config **textWriterListener** , Izleme iletilerini *ProvAgentTrace. log* dosyasına gönderir. Yalnızca gelişmiş sorun giderme için textWriterListener ile ilgili satırların açıklamasını kaldırın. 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. Hizmeti başlatın **Microsoft Azure AD sağlama aracısına bağlanın**.

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Windows Olay Görüntüleyicisi aracı sorunlarını giderme için ayarlama

1. Sağlama aracısının dağıtıldığı Windows Server makinesinde oturum açın
1. **Windows Server Olay Görüntüleyicisi** masaüstü uygulamasını açın.
1. **Windows günlükleri > uygulamasını** seçin.
1. **Geçerli günlüğü filtrele...** öğesini kullanın kaynak **Azure AD Connect sağlama Aracısı** altında günlüğe kaydedilen tüm olayları görüntüleme ve aşağıda gösterildiği gibi "-5" filtresini BELIRTEREK olay kimliği "5" olan olayları dışarıda bırakma seçeneği.
   > [!NOTE]
   > Olay KIMLIĞI 5, aracı önyükleme iletilerini Azure AD bulut hizmetine yakalar ve bu nedenle günlük dosyalarını analiz ederken filtreliyoruz. 

   ![Windows Olay Görüntüleyicisi](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. **Tamam** ' a tıklayın ve sonuç görünümünü **Tarih ve saat** sütununa göre sıralayın.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Hizmet sorunlarını giderme için Azure portal denetim günlüklerini ayarlama

1. [Azure Portal](https://portal.azure.com)başlatın ve Workday sağlama uygulamanızın **Denetim günlükleri** bölümüne gidin.
1. Yalnızca görünümdeki (Tarih, etkinlik, durum, durum nedeni) şu sütunları görüntülemek için denetim günlükleri sayfasındaki **sütunlar** düğmesini kullanın. Bu yapılandırma yalnızca sorun giderme için uygun olan verilere odaklanmanızı sağlar.

   ![Denetim günlüğü sütunları](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. Görünümü filtrelemek için **hedef** ve **Tarih aralığı** sorgu parametrelerini kullanın. 
   * **Hedef** sorgu parametresini, Workday Worker nesnesinin "Worker ID" veya "Employee ID" olarak ayarlayın.
   * **Tarih aralığını** , sağlama ile ilgili hataları veya sorunları araştırmak istediğiniz uygun bir zaman dilimine ayarlayın.

   ![Denetim günlüğü filtreleri](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>AD Kullanıcı hesabı oluşturma işlemleri için günlükleri anlama

Workday 'de yeni bir işe algılandığında ( *21023* çalışan kimliğiyle birlikte), Azure AD sağlama hizmeti çalışan için yenı bir ad kullanıcı hesabı oluşturmaya çalışır ve işlem, aşağıda açıklandığı gibi 4 denetim günlüğü kaydı oluşturur:

  [![Denetim günlüğü oluşturma Ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Denetim günlüğü kayıtlarının herhangi birine tıkladığınızda, **etkinlik ayrıntıları** sayfası açılır. Her günlük kayıt türü için **etkinlik ayrıntıları** sayfası görüntülenir.

* **Workday Içeri aktarma** kaydı: Bu günlük kaydı, Workday 'den getirilen çalışan bilgilerini görüntüler. Workday 'den veri getirilirken oluşan sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Ad Içeri aktarma** kaydı: Bu günlük kaydı, ad 'den getirilen hesabın bilgilerini görüntüler. İlk kullanıcı oluştururken olduğu gibi, AD hesabı yoktur, *etkinlik durumu nedeni* ACTIVE DIRECTORY eşleşen kimliği özniteliği değeri olan hiçbir hesabın bulunamadığını gösterir. Workday 'den veri getirilirken oluşan sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Bu AD içeri aktarma işlemine karşılık gelen sağlama Aracısı günlük kayıtlarını bulmak için Windows Olay Görüntüleyicisi günlüklerini açın ve **bul...** ' u kullanın. Eşleşen KIMLIĞI/birleştirme özelliği özniteliği değerini içeren günlük girişlerini bulmak için menü seçeneği (Bu durumda *21023*).

  ![Bul](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  AD hesabını almak için aracı tarafından kullanılan LDAP arama filtresini sağlayacak olan *olay kimliği = 9*' un bulunduğu girişi bulun. Benzersiz kullanıcı girişlerini almak için bunun doğru arama filtresi olup olmadığını doğrulayabilirsiniz.

  ![LDAP arama](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  *Olay kimliği = 2* ile hemen takip eden kayıt, arama işleminin sonucunu ve herhangi bir Sonuç döndürmeyeceğini yakalar.

  ![LDAP sonuçları](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Eşitleme kuralı eylem** kaydı: Bu günlük kaydı, öznitelik eşleme kurallarının sonuçlarını ve gelen iş günü olayını işlemek için gerçekleştirilecek sağlama eylemiyle birlikte yapılandırılmış kapsam filtrelerini görüntüler. Eşitleme eylemiyle ilgili sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Öznitelik eşleme ifadeleriniz ile ilgili sorunlar varsa veya gelen Workday verilerinde sorunlar varsa (örneğin: boş veya gerekli öznitelikler için null değer), bu aşamada hatanın ayrıntılarını sağlayan hata kodu ile bu aşamada bir hata gözlemleyeceksiniz.

* **Ad dışarı aktarma** kaydı: Bu günlük kaydı, ad hesabı oluşturma işleminin sonucunu, işlemde ayarlanan öznitelik değerleriyle birlikte görüntüler. Hesap oluşturma işlemiyle ilgili sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir. "Ek ayrıntılar" bölümünde, "EventName" değeri "EntryExportAdd" olarak ayarlanır, "JoiningProperty" eşleşen KIMLIK özniteliğinin değerine ayarlanır, "Sourcetutturucu" kayıtla ilişkili Iş Dayıd (WıD) olarak ayarlanır ve "Targettutturucu" yeni oluşturulan kullanıcının AD "Objectguıd" özniteliğinin değerine ayarlanır. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Bu AD dışarı aktarma işlemine karşılık gelen sağlama Aracısı günlük kayıtlarını bulmak için Windows Olay Görüntüleyicisi günlüklerini açın ve **bul...** ' u kullanın. Eşleşen KIMLIĞI/birleştirme özelliği özniteliği değerini içeren günlük girişlerini bulmak için menü seçeneği (Bu durumda *21023*).  

  *Olay kimliği = 2* ile dışarı aktarma işleminin zaman damgasına karşılık gelen BIR http post kaydı bulun. Bu kayıt sağlama aracısına sağlama hizmeti tarafından gönderilen öznitelik değerlerini içerecektir.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="' Sağlama Aracısı ' günlüğündeki ' HTTP POST ' kaydını gösteren ekran görüntüsü." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Yukarıdaki olayın hemen ardından, AD hesabı oluşturma işleminin yanıtını yakalayan başka bir olay olması gerekir. Bu olay, AD içinde oluşturulan yeni objectGUID değerini döndürür ve sağlama hizmetinde Targettutturucu özniteliği olarak ayarlanır.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="AD vurgulanmış olarak oluşturulan Objectguıd ile ' sağlama Aracısı ' günlüğünü gösteren ekran görüntüsü." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Yönetici güncelleştirme işlemlerine yönelik günlükleri anlama

Yönetici özniteliği, AD 'deki bir başvuru özniteliğidir. Sağlama Hizmeti, Kullanıcı oluşturma işleminin parçası olarak Manager özniteliğini ayarladı. Bunun yerine, Kullanıcı için AD hesabı oluşturulduktan sonra, yönetici özniteliği bir *güncelleştirme* işleminin parçası olarak ayarlanır. Yukarıdaki örneği genişleterek, Workday 'de çalışan KIMLIĞI "21451" olan yeni bir işe giriş ve yeni işe alma yöneticisinin (*21023*) zaten bir ad hesabı var. Bu senaryoda, Kullanıcı 21451 için Denetim günlüklerini aramak 5 girişi gösterir.

  [![Yönetici Güncelleştirmesi](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

İlk 4 kayıt, Kullanıcı oluşturma işleminin bir parçası olarak araştırdığımız gibidir. 5. kayıt, yönetici öznitelik güncelleştirmesiyle ilişkili dışarı aktarma ' dır. Günlük kaydı, yöneticinin *Objectguıd* özniteliği KULLANıLARAK gerçekleştirilen ad Account Manager güncelleştirme işleminin sonucunu görüntüler.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Yaygın olarak karşılaşılan hatalar çözümleniyor

Bu bölüm, Workday Kullanıcı sağlama ve bu sorunu çözme ile ilgili sık görülen hataları ele almaktadır. Hatalar şu şekilde gruplandırılır:

* [Aracı hatalarını sağlama](#provisioning-agent-errors)
* [Bağlantı hataları](#connectivity-errors)
* [AD Kullanıcı hesabı oluşturma hataları](#ad-user-account-creation-errors)
* [AD Kullanıcı hesabı güncelleştirme hataları](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Aracı hatalarını sağlama

|#|Hata Senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Sağlama Aracısı şu hata iletisiyle yüklenirken hata iletisi:  *' Microsoft Azure AD Connect sağlama Aracısı ' (AADConnectProvisioningAgent) hizmeti başlatılamadı. Sistemi başlatmak için yeterli ayrıcalıklara sahip olduğunuzu doğrulayın.* | Bu hata genellikle, sağlama aracısını bir etki alanı denetleyicisine yüklemeye çalışıyorsanız ve Grup İlkesi hizmetin başlamasını engelliyorsa, genellikle görüntülenir.  Ayrıca, aracının çalıştığı önceki bir sürümüne sahipseniz ve yeni bir yükleme başlatmadan önce bu sürümü kaldırmadıysanız da görülür.| Sağlama aracısını DC olmayan bir sunucuya yükler. Yeni aracıyı yüklemeden önce aracının önceki sürümlerinin kaldırıldığından emin olun.|
|2.| ' Microsoft Azure AD Connect sağlama Aracısı ' Windows hizmeti *Başlangıç* durumunda ve *çalışan* durumuna geçmiyor. | Yüklemenin bir parçası olarak, aracı Sihirbazı sunucuda bir yerel hesap (**NT Service \\ aadconnectprovisioningagent**) oluşturur ve bu, hizmeti başlatmak için kullanılan oturum açma hesabıdır. Windows sunucunuzdaki bir güvenlik ilkesi yerel hesapların Hizmetleri çalıştırmasını engelliyorsa, bu hatayla karşılaşırsınız. | *Hizmetler konsolunu* açın. ' Microsoft Azure AD sağlama aracısına Bağlan ' Windows hizmetine sağ tıklayın ve oturum aç sekmesinde, hizmeti çalıştırmak için bir etki alanı yöneticisinin hesabını belirtin. Hizmeti yeniden başlatın. |
|3.| Adım *bağlama Active Directory*, sağlama aracısını ad etki alanınız ile yapılandırırken, sihirbazın ad şemasını yüklemeye ve sonunda zaman aşımına uğramadan uzun bir süre sürer. | Bu hata genellikle, güvenlik duvarı sorunlarından dolayı sihirbaz AD etki alanı denetleyicisi sunucusuna bağlanamadığında gösterilir. | *Active Directory bağlanma* Sihirbazı EKRANıNDA, ad etki alanınız için kimlik bilgilerini sağlarken, *etki alanı denetleyicisi önceliği Seç* adlı bir seçenek vardır. Aracı sunucusuyla aynı sitede olan bir etki alanı denetleyicisi seçmek ve iletişimi engelleyen bir güvenlik duvarı kuralı olmadığından emin olmak için bu seçeneği kullanın. |

#### <a name="connectivity-errors"></a>Bağlantı hataları

Sağlama hizmeti Workday veya Active Directory 'e bağlanamıyorsa, sağlama durumunun karantinaya alınmış bir duruma geçmesine neden olabilir. Bağlantı sorunlarını gidermek için aşağıdaki tabloyu kullanın.

|#|Hata Senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| **Bağlantıyı Sına**' ya tıkladığınızda, hata iletisini alırsınız: *Active Directory bağlantısında bir hata oluştu. Lütfen şirket içi sağlama aracısının çalıştığından ve doğru Active Directory etki alanı ile yapılandırıldığından emin olun.* | Bu hata genellikle, sağlama aracısının çalışmadığı veya Azure AD ile sağlama Aracısı arasındaki iletişimi engelleyen bir güvenlik duvarı varsa görüntülenir. Etki alanı aracı sihirbazında yapılandırılmamışsa da bu hatayı görebilirsiniz. | Aracının çalıştığını onaylamak için Windows Server 'da *Hizmetler* konsolunu açın. Sağlama Aracısı sihirbazını açın ve doğru etki alanının aracıya kayıtlı olduğunu doğrulayın.  |
|2.| Sağlama işi, hafta sonları (Cum-Sat) üzerinden karantina durumuna geçer ve eşitlemede bir hata olduğunu belirten bir e-posta bildirimi alırız. | Bu hatanın yaygın nedenlerinden biri Workday'in planlı kapalı kalma süresidir. Workday uygulama kiracısını kullanıyorsanız, Workday'in uygulama kiracıları için hafta sonları zamanladığı kapalı kalma sürelerini (genellikle Cuma akşamından Cumartesi sabahına kadar) ve bu süre boyunca Workday sağlama uygulamalarının Workday'e bağlanamadığı için karantina durumuna geçebileceğini unutmayın. Workday uygulama kiracısı yeniden çevrimiçi olduğunda normal durumuna geri döner. Nadir durumlarda, kiracı yenilendiği veya hesap kilitlendiği ya da süresi dolduğu için Tümleştirme Sistemi Kullanıcısının parolası değiştirildiğinde de bu hatayı görebilirsiniz. | Workday'in kapalı kalma zamanlamasını öğrenmek için Workday yöneticinize veya iş ortağınıza danışın. Kapalı kalma süresince uyarı iletilerini yoksayın ve Workday örneği yeniden çevrimiçi olduğunda kullanılabilirliği onaylayın.  |

#### <a name="ad-user-account-creation-errors"></a>AD Kullanıcı hesabı oluşturma hataları

|#|Hata Senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Denetim günlüğündeki işlem hataları şu iletiyle dışarı aktarma *hatası: OperationsError-SvcErr: bir işlem hatası oluştu. Dizin hizmeti için üst başvuru yapılandırılmadı. Bu nedenle, dizin hizmeti bu ormanın dışındaki nesnelere başvuru yayınyapamıyor.* | Bu hata genellikle *Active Directory kapsayıcı* OU doğru ayarlanmamışsa veya *ParentDistinguishedName* için kullanılan ifade eşlemesinde sorunlar varsa görüntülenir. | Yazım hataları için *Active Directory kapsayıcı* OU parametresini denetleyin. Öznitelik eşlemesinde *parentDistinguishedName* kullanıyorsanız, bunun her zaman AD etki alanı içinde bilinen bir kapsayıcı olarak değerlendirildiğinden emin olun. Oluşturulan değeri görmek için denetim günlüklerinde *dışarı aktarma* olayını kontrol edin. |
|2.| Denetim günlüğündeki hata kodu ile dışarı aktarma işlemi hataları: *Systemforcrossdomainıdentitymanagementbadresponse* ve ileti *hatası: ConstraintViolation-AtrErr: istekteki bir değer geçersiz. Öznitelik için bir değer, kabul edilebilir değerler aralığında değildi. \Nhata ayrıntıları: Şirket CONSTRAINT_ATT_TYPE*. | Bu hata *Şirket* özniteliğine özgü olsa da, bu hatayı *CN* gibi diğer öznitelikler için de görebilirsiniz. Bu hata, AD Zorlanmış şema kısıtlaması nedeniyle görünür. Varsayılan olarak, *Şirket* ve *CN* gibi özniteliklerin üst sınırı 64 karakter olmalıdır. Workday 'den gelen değer 64 karakterden büyükse, bu hata iletisini görürsünüz. | Hata iletisinde bildirilen özniteliğin değerini görmek için denetim günlüklerinde *dışarı aktarma* olayını kontrol edin. [PARÇAAL](../app-provisioning/functions-for-customizing-application-data.md#mid) Işlevini kullanarak Workday 'den gelen değeri kırpıp veya eşlemeleri benzer uzunluk kısıtlamalarına sahip olmayan bir ad özniteliğiyle değiştirmeyi göz önünde bulundurun.  |

#### <a name="ad-user-account-update-errors"></a>AD Kullanıcı hesabı güncelleştirme hataları

AD Kullanıcı hesabı güncelleştirme işlemi sırasında, sağlama hizmeti hem Workday hem de AD 'deki bilgileri okur, öznitelik eşleme kurallarını çalıştırır ve herhangi bir değişikliğin etkili olması gerekip gerekmediğini belirler. Bir güncelleştirme olayı da tetiklenir. Bu adımlardan herhangi biri bir hatayla karşılaştığında, denetim günlüklerinde günlüğe kaydedilir. Yaygın güncelleştirme hatalarını gidermek için aşağıdaki tabloyu kullanın.

|#|Hata Senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Denetim günlüğündeki *EventName = EntrySynchronizationError ve ErrorCode = EndpointUnavailable* iletisiyle eşitleme kuralı eylem hataları. | Bu hata, sağlama hizmeti, şirket içi sağlama Aracısı tarafından karşılaşılan bir işleme hatası nedeniyle Active Directory 'tan Kullanıcı profili verilerini alamadığında görüntülenir. | Okuma işlemiyle ilgili sorunları belirten hata olaylarını Olay Görüntüleyicisi için sağlama Aracısı 'nı denetleyin (olay KIMLIĞINE göre filtrele #2). |
|2.| AD 'deki Manager özniteliği, AD 'deki belirli kullanıcılar için güncelleştirilmedi. | Bu hatanın en olası nedeni, kapsam kuralları kullanıyorsanız ve kullanıcının Yöneticisi kapsamın bir parçası değilse oluşur. Ayrıca, yöneticinin eşleşen KIMLIĞI özniteliği (ör. EmployeeID) hedef AD etki alanında bulunmazsa veya doğru değere ayarlanmamışsa bu sorunla karşılaşabilirsiniz. | Kapsam filtresini gözden geçirin ve yönetici kullanıcıyı kapsamına ekleyin. Eşleşen ID özniteliği için bir değer olduğundan emin olmak için, AD 'deki yöneticinin profilini denetleyin. |

## <a name="managing-your-configuration"></a>Yapılandırmanızı yönetme

Bu bölümde, Workday odaklı Kullanıcı sağlama yapılandırmanızı nasıl daha fazla genişletebilirsiniz, özelleştirebilir ve yönetebilirsiniz. Aşağıdaki konuları ele almaktadır:

* [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)  
* [Yapılandırmanızı dışarı ve içeri aktarma](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Workday kullanıcı özniteliklerinin listesini özelleştirme

Active Directory ve Azure AD için Workday sağlama uygulamaları, seçebileceğiniz Workday kullanıcı özniteliklerinin varsayılan bir listesini içerir. Ancak, bu listeler kapsamlı değildir. Workday, Workday kiracınızda standart ya da benzersiz olabilen birçok yüzlerce Kullanıcı özniteliğini destekler.

Azure AD sağlama hizmeti, sizin listenizi veya Workday öznitelerinizi, Insan kaynakları API 'sinin [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) işleminde kullanıma sunulan tüm öznitelikleri içerecek şekilde özelleştirme yeteneğini destekler.

Bu değişikliği yapmak için, kullanmak istediğiniz öznitelikleri temsil eden XPath ifadelerini ayıklamak ve ardından Azure portal Gelişmiş öznitelik düzenleyicisini kullanarak bunları sağlama yapılandırmanıza eklemek için [Workday Studio 'yu](https://community.workday.com/studio-download) kullanmanız gerekir.

**Bir Workday Kullanıcı özniteliği için bir XPath ifadesi almak için:**

1. [Workday Studio 'yu](https://community.workday.com/studio-download)indirin ve yükleyin. Yükleyiciye erişmeniz için bir Workday Community hesabına ihtiyacınız olacaktır.

2. [Workday Web Hizmetleri dizininden](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html) kullanmayı planladığınız WWS API sürümüne özel iş günü **Human_Resources** WSDL dosyasını indirin

3. Workday Studio 'Yu başlatın.

4. Komut çubuğundan,  **Test Web hizmeti ' ni sınayıcı seçeneğinde > Workday** ' i seçin.

5. **Dış**' i seçin ve adım 2 ' de INDIRDIĞINIZ Human_Resources WSDL dosyasını seçin.

    !["Human_Resources" dosyasının Workday Studio 'da açık olduğunu gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wdstudio1.png)

6. **Konum** alanını olarak ayarlayın `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` , ancak "Impl-CC" yi gerçek örnek YAZıNıZLA ve "Kiracı" öğesini gerçek kiracı adınızla değiştirin.

7. **İşlemi** **Get_Workers** ayarla

8.    Workday kimlik bilgilerinizi ayarlamak için Istek/yanıt bölmelerindeki küçük **Yapılandır** bağlantısına tıklayın. **Kimlik doğrulamasını** denetleyin ve sonra Workday tümleştirme sistem hesabınız için Kullanıcı adını ve parolayı girin. Kullanıcı adını kiracı adı olarak biçimlendirdiğinizden emin olun \@ ve **WS-Security UsernameToken** seçeneğini seçili bırakın.
   !["Kullanıcı adı" ve "parola" girilmiş ve "WS-Security Kullanıcı adı belirteci" seçiliyken "güvenlik" sekmesini gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wdstudio2.png)

9. **Tamam**’ı seçin.

10. **İstek** BÖLMESINDE aşağıdaki XML 'e yapıştırın. **Employee_ID** , Workday kiracınızdaki gerçek bir kullanıcının çalışan kimliğine ayarlayın. **WD: Version** öğesini kullanmayı planladığınız WWS sürümüne ayarlayın. Ayıklamak istediğiniz özniteliği olan bir kullanıcı seçin.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Komutu yürütmek için **gönderme isteğine** (yeşil ok) tıklayın. Başarılı olursa yanıt, **Yanıt** bölmesinde görünmelidir. Girdiğiniz kullanıcı KIMLIĞI verilerine sahip olduğundan emin olmak için yanıtı denetleyin ve bir hata değildir.

12. Başarılı olursa, XML 'yi **Yanıt** bölmesinden kopyalayın ve bir XML dosyası olarak kaydedin.

13. Workday Studio komut çubuğunda **dosya > dosya aç...** öğesini seçin ve kaydettiğiniz XML dosyasını açın. Bu eylem, dosyayı Workday Studio XML düzenleyicisinde açar.

    !["Workday Studio X M L Editor" içinde açık bir X M L dosyasının ekran görüntüsü.](./media/workday-inbound-tutorial/wdstudio3.png)

14. Dosya ağacında, **/env: Envelope > env: Body > WD: Get_Workers_Response > WD: Response_Data > WD: Worker** ' a giderek Kullanıcı verilerini bulun.

15. **WD: Worker** altında, eklemek istediğiniz özniteliği bulun ve seçin.

16. Seçili özniteeniz için XPath ifadesini **Belge yolu** alanından kopyalayın.

17. Kopyalanmış ifadeden **/env: Envelope/env: Body/WD: Get_Workers_Response/WD: Response_Data/** önekini kaldırın.

18. Kopyalanmış ifadedeki son öğe bir düğümse (örnek: "/WD: Birth_Date"), sonra ifadenin sonuna **/Text ()** ekleyin. Son öğe bir öznitelik ise bu gerekli değildir (örnek: " /@wd: tür").

19. Sonuç, şöyle bir şekilde olmalıdır `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` . Bu değer, Azure portal kopyalayacaksınız.

**Özel Workday Kullanıcı öznitelerinizi sağlama yapılandırmanıza eklemek için:**

1. [Azure Portal](https://portal.azure.com)başlatın ve bu öğreticide daha önce anlatıldığı gibi Workday sağlama uygulamanızın sağlama bölümüne gidin.

2. **Sağlama durumunu** **kapalı** olarak ayarlayın ve **Kaydet**' i seçin. Bu adım, değişikliklerinizin yalnızca siz hazırsanız etkili olacağını sağlamaya yardımcı olur.

3. **Eşlemeler** altında, Iş **günü çalışanlarını şirket Içi Active Directory ile eşitlemeyi** (veya **Workday çalışanlarını Azure AD 'ye eşitlemeyi**) seçin.

4. Sonraki ekranın alt kısmına ilerleyin ve **Gelişmiş seçenekleri göster**' i seçin.

5. **Workday için öznitelik listesini düzenle**' yi seçin.

    !["Workday için öznitelik listesini düzenle" eylemi vurgulanmış olan "Azure 'a bir D Kullanıcı sağlama-sağlama" sayfası gösteren ekran görüntüsü.](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Öznitelik listesinin en altına, giriş alanlarının bulunduğu yere ilerleyin.

7. **Ad** için, öznitemisiniz için bir görünen ad girin.

8. **Tür** için, özniteliğe uygun şekilde karşılık gelen türü seçin (**dize** en yaygın olarak kullanılır).

9. **API ifadesi** Için, Workday Studio 'Dan kopyaladığınız XPath ifadesini girin. Örnek: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **Öznitelik Ekle**' yi seçin.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Yukarıdaki **Kaydet** ' i ve ardından Iletişim kutusuna **Evet** ' i seçin. Hala açıksa Attribute-Mapping ekranını kapatın.

12. Ana **sağlama** sekmesine döndüğünüzde, Iş **günü çalışanlarını şirket içi Active Directory** (veya **çalışanları Azure AD ile eşitlemeyi**) yeniden eşitlemeyi seçin.

13. **Yeni eşleme Ekle**' yi seçin.

14. Yeni öznitebir öznitelik artık **kaynak öznitelik** listesinde görünmelidir.

15. Yeni özniteliğiyle istenen şekilde bir eşleme ekleyin.

16. İşiniz bittiğinde, **sağlama durumunu** **Açık** ve kayıt olarak ayarlamayı unutmayın.

### <a name="exporting-and-importing-your-configuration"></a>Yapılandırmanızı dışarı ve içeri aktarma

[Sağlama yapılandırmasını dışarı ve içeri aktarma](../app-provisioning/export-import-provisioning-configuration.md) makalesine başvurun

## <a name="managing-personal-data"></a>Kişisel verileri yönetme

Active Directory için Workday sağlama çözümü, bir sağlama aracısının şirket içi Windows Server 'da yüklü olmasını gerektirir ve bu aracı Windows olay günlüğünde, iş gününde iş gününüzün AD öznitelik eşlemelerine bağlı olarak kişisel veriler içerebilen Günlükler oluşturur. Kullanıcı gizliliği yükümlülüklerine uymak için, olay günlüğünü temizlemek üzere bir Windows zamanlanmış görevi ayarlayarak olay günlüklerinde 48 saatten daha fazla veri tutulmayacağından emin olabilirsiniz.

Azure AD sağlama hizmeti, GDPR sınıflandırmasının **veri işlemcisi** kategorisine denk gelir. Veri işlemcisi işlem hattı olarak hizmet, önemli iş ortaklarına ve son tüketicilere veri işleme hizmeti sağlar. Azure AD sağlama hizmeti, Kullanıcı verileri oluşturmaz ve kişisel verilerin toplandığı ve nasıl kullanıldığı hakkında hiç bağımsız denetime sahip değildir. Azure AD sağlama hizmeti 'nde veri alımı, toplama, analiz ve raporlama, mevcut kurumsal verileri temel alır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Azure AD sağlama hizmeti, veri bekletmeye göre rapor oluşturmaz, analiz uygulamaz veya 30 günden fazla öngörü sağlar. Bu nedenle, Azure AD sağlama hizmeti 30 günden daha fazla veri depolamaz, işlemez veya korumaz. Bu tasarım, GDPR yönetmelikleri, Microsoft gizliliği uyumluluk düzenlemeleri ve Azure AD veri saklama ilkeleriyle uyumludur.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD ve Workday tümleştirme senaryoları ve Web hizmeti çağrıları hakkında daha fazla bilgi edinin](../app-provisioning/workday-integration-reference.md)
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](workday-tutorial.md)
* [Workday geri yazmayı yapılandırma hakkında bilgi edinin](workday-writeback-tutorial.md)
* [Sağlama yapılandırmalarının yönetimi için Microsoft Graph API 'Lerini nasıl kullanacağınızı öğrenin](/graph/api/resources/synchronization-overview)