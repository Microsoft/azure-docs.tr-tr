---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Workday yapılandırma | Microsoft Docs'
description: İş Workday 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63176c325fd42c46e988ab3798f46089a43e70bf
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326783"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlama için Workday yapılandırma

Bu öğreticinin amacı, iş Workday 'den çalışan profillerini, isteğe bağlı e-posta adresi ve Kullanıcı adı ile Workday 'ye geri yazma ile birlikte Active Directory ve Azure Active Directory içine aktarmak için gerçekleştirmeniz gereken adımları göstermelidir.

## <a name="overview"></a>Genel Bakış

[Azure Active Directory Kullanıcı sağlama hizmeti](../manage-apps/user-provisioning.md) , Kullanıcı hesaplarını sağlamak Için [Workday insan kaynakları API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 'siyle tümleştirilir. Azure AD bu bağlantıyı kullanarak aşağıdaki Kullanıcı sağlama iş akışlarını etkinleştirir:

* Kullanıcıların, Workday 'den bir veya daha fazla Active Directory etki alanında seçili kullanıcı kümelerini **Active Directory sağlama** .

* **Yalnızca bulutta** bulunan ve şirket içi Active Directory kullanılmayan Azure Active Directory senaryolarda, KULLANıCıLARıN Azure AD Kullanıcı sağlama hizmeti kullanılarak doğrudan Workday 'den Azure Active Directory 'e sağlaması sağlanabilir.

* **Workday 'e e-posta adresi ve Kullanıcı adı yazma** -Azure AD Kullanıcı sağlama hizmeti, Azure AD 'den e-posta adreslerini ve Kullanıcı adını yeniden Workday 'ye yazabilir.

### <a name="what-human-resources-scenarios-does-it-cover"></a>İnsan kaynakları senaryoları hangi şekilde ele alınmaktadır?

Azure AD Kullanıcı sağlama hizmeti tarafından desteklenen Workday Kullanıcı sağlama iş akışları aşağıdaki insan kaynakları ve kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu etkinleştirir:

* **Yeni çalışanların işe** Alım-Workday 'e yeni bir çalışan eklendiğinde, Active Directory, Azure Active Directory ve isteğe bağlı olarak Office 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../manage-apps/user-provisioning.md)(geri yazma ile) bir kullanıcı hesabı otomatik olarak oluşturulur. Workday 'e e-posta adresi.

* **Çalışan özniteliği ve profil güncelleştirmeleri** -iş günü içinde bir çalışan kaydı güncelleştirildiğinde (ad, başlık veya yönetici gibi), kullanıcı hesabı Active Directory, Azure Active Directory ve isteğe bağlı olarak Office 365 ' de otomatik olarak güncelleştirilir ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../manage-apps/user-provisioning.md).

* **Çalışan sonlandırmaları** -bir çalışan Workday 'de sonlandırıldığında, kullanıcı hesabı Active Directory, Azure Active Directory ve isteğe bağlı olarak Office 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../manage-apps/user-provisioning.md)otomatik olarak devre dışıdır.

* **Çalışan rehires** -bir çalışan Workday 'de yeniden hazırlandığında, eski hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Office 365 ve diğer SaaS için otomatik olarak yeniden etkinleştirilebilir veya yeniden sağlanabilir (tercihinize bağlı olarak). [ Azure AD tarafından desteklenen uygulamalar](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Bu Workday Kullanıcı sağlama çözümü idealdir:

* Workday Kullanıcı sağlaması için önceden oluşturulmuş, bulut tabanlı bir çözüm gerektiren kuruluşlar

* Workday 'den Active Directory veya Azure Active Directory doğrudan Kullanıcı sağlamasını gerektiren kuruluşlar

* Kullanıcıların Workday HCM modülünden elde edilen veriler kullanılarak sağlanması gereken kuruluşlar (bkz. [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Kullanıcılara katılım, taşıma ve kullanıcıların bir veya daha fazla Active Directory ormanlara, etki alanına ve yalnızca Workday HCM modülünde algılanan değişiklik bilgilerine dayalı olarak eşitlenmesini gerektiren kuruluşlar (bkz. [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* E-posta için Office 365 kullanan kuruluşlar

## <a name="solution-architecture"></a>Çözüm Mimarisi

Bu bölümde, yaygın karma ortamlar için uçtan uca Kullanıcı sağlama çözüm mimarisi açıklanmaktadır. İki ilişkili akış vardır:

* **Yetkili HR veri akışı – Workday 'den şirket içi Active Directory:** Bu akış çalışan olayları (örneğin, yeni alar, aktarımlar, sonlandırmalar), ilk olarak Cloud Workday HR kiracısında oluşur ve ardından olay verileri Azure AD ve sağlama Aracısı aracılığıyla şirket içi Active Directory akar. Olaya bağlı olarak, AD 'de oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerine yol açabilir.
* **E-posta ve Kullanıcı adı geri yazma akışı – şirket içi Active Directory için Workday 'e:** Active Directory hesap oluşturma işlemi tamamlandıktan sonra, Azure AD Connect ile Azure AD ile eşitlenir ve e-posta ve Kullanıcı adı özniteliği Workday 'e geri yazılabilir.

![Genel Bakış](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Uçtan uca Kullanıcı veri akışı

1. HR ekibi, iş günü HCM 'de çalışan işlemleri (Joiners/Taşımacılar/sızıntı veya yeni Hires/aktarımlar/sonlandırıcılar) gerçekleştirir
2. Azure AD sağlama hizmeti, iş günü HR 'dan zamanlanan kimlik eşitlemeleri çalıştırır ve şirket içi Active Directory eşitleme için işlenmesi gereken değişiklikleri tanımlar.
3. Azure AD sağlama hizmeti, şirket içi AAD Connect sağlama aracısını AD hesabı oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerini içeren bir istek yüküne göre çağırır.
4. Azure AD Connect sağlama Aracısı, AD hesabı verileri eklemek/güncelleştirmek için bir hizmet hesabı kullanır.
5. Azure AD Connect/AD Eşitleme altyapısı, AD 'de güncelleştirme çekmek için Delta eşitlemesi çalıştırır.
6. Active Directory güncelleştirmeleri Azure Active Directory ile eşitlenir.
7. Workday geri yazma Bağlayıcısı yapılandırılmışsa, kullanılan eşleşen özniteliğe göre e-posta özniteliği ve Kullanıcı adı ' nı Workday 'ye yazar.

## <a name="planning-your-deployment"></a>Dağıtımınızı planlama

Workday tümleştirmeye başlamadan önce aşağıdaki önkoşulları denetleyin ve geçerli Active Directory mimarinizi ve Kullanıcı sağlama gereksinimlerinizi Azure Active Directory tarafından sunulan çözümle ve Kullanıcı sağlama gereksinimlerinizin nasıl eşleşeceğini öğrenmek için aşağıdaki kılavuzu okuyun. İş günü tümleştirme ortağınızla ve ık paydaşlarla işbirliği yaparken size yardımcı olmak üzere planlama çalışma sayfaları içeren kapsamlı bir [dağıtım planı](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) da mevcuttur.

Bu bölüm, planlamanın aşağıdaki yönlerini anlatmaktadır:

* [Önkoşullar](#prerequisites)
* [Dağıtılacak bağlayıcı uygulamalarının sağlamasını seçme](#selecting-provisioning-connector-apps-to-deploy)
* [Azure AD Connect sağlama aracısının dağıtımını planlama](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Birden çok Active Directory etki alanı ile tümleştirme](#integrating-with-multiple-active-directory-domains)
* [Kullanıcı özniteliği eşleme ve dönüştürmelerini Active Directory için Workday planlama](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki öğelerin zaten olduğunu varsayar:

* Her bir kullanıcı için, Workday 'den kaynaklayacağı ve şirket içi Active Directory veya Azure Active Directory sağlanacak olan her kullanıcı için geçerli bir Azure AD Premium P1 veya daha yüksek abonelik lisansı.
* Sağlama aracısını yapılandırmak için Azure AD Genel yönetici erişimi
* Test ve tümleştirme amaçları için bir Workday uygulama kiracısı
* Bir sistem tümleştirme kullanıcısı oluşturmak için Workday 'de yönetici izinleri ve test etme amacıyla çalışan verilerinde değişiklik yapma
* Active Directory Kullanıcı sağlaması için, [Şirket içi sağlama aracısını](https://go.microsoft.com/fwlink/?linkid=847801) barındırmak üzere .NET 4.7.1 + Runtime Ile Windows Server 2012 veya üstünü çalıştıran bir sunucu gerekir
* Active Directory ve Azure AD arasında kullanıcıları eşitlemek için [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Dağıtılacak bağlayıcı uygulamalarının sağlamasını seçme

Workday ve Active Directory arasında sağlama iş akışlarının sağlanması için Azure AD, Azure AD uygulama galerisinden ekleyebileceğiniz çoklu sağlama bağlayıcı uygulamaları sağlar:

![AAD Uygulama Galerisi](./media/workday-inbound-tutorial/wd_gallery.png)

* **Kullanıcı sağlamayı Active Directory Için Workday** -bu uygulama, Workday 'den tek bir Active Directory etki alanına kullanıcı hesabı sağlamayı kolaylaştırır. Birden çok etki alanınız varsa, sağlamanız gereken her Active Directory etki alanı için Azure AD uygulama galerisinden bu uygulamanın bir örneğini ekleyebilirsiniz.

* **Azure AD Kullanıcı sağlaması Için Workday** -AAD Connect Active Directory Kullanıcıları Azure Active Directory eşitlemede kullanılması gereken bir araçtır, bu uygulama, Workday 'den yalnızca bulut kullanıcılarının tek bir Azure 'a sağlanmasını kolaylaştırmak için kullanılabilir Active Directory kiracı.

* **Workday geri yazma** -bu uygulama, kullanıcının e-posta adreslerini Azure Active Directory Workday 'den Workday 'e geri yazmayı kolaylaştırır.

> [!TIP]
> Normal "Workday" uygulaması, Workday ve Azure Active Directory arasında çoklu oturum açma ayarlamak için kullanılır.

Senaryolarınızla ilgili olan Workday sağlama uygulamalarını belirlemek için aşağıdaki karar akışı grafiğini kullanın.
    ![Karar akış çizelgesi](./media/workday-inbound-tutorial/wday_app_flowchart.png "karar akış çizelgesi")

Bu öğreticinin ilgili bölümüne gitmek için İçindekiler tablosunu kullanın.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Azure AD Connect sağlama aracısının dağıtımını planlama

> [!NOTE]
> Bu bölüm yalnızca Kullanıcı sağlama uygulamasını Active Directory için Workday 'i dağıtmayı planlıyorsanız geçerlidir. Workday geri yazma veya Workday 'yi Azure AD Kullanıcı sağlama uygulamasına dağıtıyorsanız bunu atlayabilirsiniz.

Workday 'den AD Kullanıcı sağlama çözümüne, en az 4 GB RAM ve .NET 4.7.1 + Runtime ile Windows 2012 R2 veya üzeri çalıştıran sunuculara bir veya daha fazla sağlama aracısının dağıtılmasını gerektirir. Sağlama Aracısı 'nı yüklemeden önce aşağıdaki noktalar göz önünde bulundurulmalıdır:

* Sağlama aracısının çalıştırıldığı ana bilgisayar sunucusunun hedef AD etki alanına ağ erişimi olduğundan emin olun
* Sağlama Aracısı Yapılandırma Sihirbazı aracıyı Azure AD kiracınızla kaydeder ve kayıt işlemi, SSL bağlantı noktası 443 üzerinden *. msappproxy.net erişimi gerektirir. Bu iletişimi etkinleştiren giden Güvenlik Duvarı kurallarının yerinde olduğundan emin olun. Aracı [gıden HTTPS proxy yapılandırmasını](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)destekler.
* Sağlama Aracısı, şirket içi AD etki alanı (ler) ile iletişim kurmak için bir hizmet hesabı kullanır. Aracının yüklenmesinden önce, etki alanı Yöneticisi izinlerine sahip bir hizmet hesabı ve süre sonu olmayan bir parola oluşturmanız önerilir.  
* Sağlama Aracısı yapılandırması sırasında, sağlama isteklerini işlemesi gereken etki alanı denetleyicileri seçebilirsiniz. Coğrafi olarak dağıtılmış birkaç etki alanı denetleyiciniz varsa, uçtan uca çözümün güvenilirliğini ve performansını geliştirmek için, tercih edilen etki alanı denetleyicileriniz ile aynı siteye sağlama aracısını yükler
* Yüksek kullanılabilirlik için birden fazla sağlama Aracısı dağıtabilir ve aynı şirket içi AD etki alanı kümesini işleyecek şekilde kaydolabilirsiniz.

> [!IMPORTANT]
> Üretim ortamlarında, Microsoft, yüksek kullanılabilirlik için Azure AD kiracınızla yapılandırılmış en az 3 sağlama aracısına sahip olmanızı önerir.

### <a name="integrating-with-multiple-active-directory-domains"></a>Birden çok Active Directory etki alanı ile tümleştirme

> [!NOTE]
> Bu bölüm yalnızca Kullanıcı sağlama uygulamasını Active Directory için Workday 'i dağıtmayı planlıyorsanız geçerlidir. Workday geri yazma veya Workday 'yi Azure AD Kullanıcı sağlama uygulamasına dağıtıyorsanız bunu atlayabilirsiniz.

Active Directory topolojinize bağlı olarak, Kullanıcı sağlama bağlayıcı uygulaması sayısına ve yapılandırılacak sağlama aracılarının sayısına karar vermeniz gerekir. Aşağıda, dağıtımınızı planlarken, başvurabileceğiniz bazı yaygın dağıtım desenleri verilmiştir.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Dağıtım senaryosu #1: Tek Workday kiracısı-> tek AD etki alanı

Bu senaryoda, bir Workday kiracınız var ve kullanıcıları tek bir hedef AD etki alanına sağlamak istiyorsunuz. Bu dağıtım için önerilen üretim yapılandırması aşağıda verilmiştir.

|   |   |
| - | - |
| Hayır. Şirket içinde dağıtılacak aracıların sağlanması | 3 (yüksek kullanılabilirlik ve yük devretme için) |
| Hayır. Azure portal ' de yapılandırılacak iş günü için AD Kullanıcı sağlama uygulamaları | 1\. |

  ![Senaryo 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Dağıtım senaryosu #2: Tek Workday kiracısı-birden çok alt AD etki alanı >

Bu senaryo, iş Workday 'den birden çok hedef AD alt etki alanına kullanıcı sağlamayı içerir. Bu dağıtım için önerilen üretim yapılandırması aşağıda verilmiştir.

|   |   |
| - | - |
| Hayır. Şirket içinde dağıtılacak aracıların sağlanması | 3 (yüksek kullanılabilirlik ve yük devretme için) |
| Hayır. Azure portal ' de yapılandırılacak iş günü için AD Kullanıcı sağlama uygulamaları | alt etki alanı başına bir uygulama |

  ![Senaryo 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Dağıtım senaryosu #3: Tek iş günü kiracısı-> kopuk AD ormanları

Bu senaryo, kullanıcıların Workday 'den ayrık AD ormanlarındaki etki alanlarına sağlamasını içerir. Bu dağıtım için önerilen üretim yapılandırması aşağıda verilmiştir.

|   |   |
| - | - |
| Hayır. Şirket içinde dağıtılacak aracıların sağlanması | 3 ayrık AD Ormanı başına |
| Hayır. Azure portal ' de yapılandırılacak iş günü için AD Kullanıcı sağlama uygulamaları | alt etki alanı başına bir uygulama |

  ![Senaryo 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Kullanıcı özniteliği eşleme ve dönüştürmelerini Active Directory için Workday planlama

> [!NOTE]
> Bu bölüm yalnızca Kullanıcı sağlama uygulamasını Active Directory için Workday 'i dağıtmayı planlıyorsanız geçerlidir. Workday geri yazma veya Workday 'yi Azure AD Kullanıcı sağlama uygulamasına dağıtıyorsanız bunu atlayabilirsiniz.

Kullanıcı sağlamayı bir Active Directory etki alanına yapılandırmadan önce aşağıdaki soruları göz önünde bulundurun. Bu soruların yanıtları, kapsam filtrelerinizin ve öznitelik eşleştirmelerinin ayarlanması gerektiğini belirlemektir.

* **Workday 'deki kullanıcıların bu Active Directory ormana sağlanması gerekir mi?**

  * *Örnek: Workday "Company" özniteliğinin "contoso" değerini içerdiği ve "Worker_Type" özniteliğinin "Regular"*  içermesi durumunda olan kullanıcılar

* **Kullanıcılar farklı kuruluş birimlerine (OU) nasıl yönlendirilir?**

  * *Örnek: Kullanıcılar, iş günü "Municipitesi" ve "Country_Region_Reference" özniteliklerinde tanımlanan bir Office konumuna karşılık gelen OU 'Lara yönlendirilir @ no__t-0

* **Active Directory aşağıdaki öznitelikler nasıl doldurulur?**

  * Ortak ad (CN)
    * *Örnek: Workday User_ID değerini insan kaynakları tarafından ayarlanan şekilde kullanın @ no__t-0

  * Çalışan KIMLIĞI (EmployeeID)
    * *Örnek: Workday Worker_ID değerini kullanın @ no__t-0

  * SAM hesap adı (sAMAccountName)
    * *Örnek: Geçersiz karakterleri kaldırmak için bir Azure AD sağlama ifadesiyle filtrelenmiş Workday User_ID değerini kullanın @ no__t-0

  * Kullanıcı asıl adı (userPrincipalName)
    * *Örnek: Bir etki alanı adı eklemek için bir Azure AD sağlama ifadesiyle Workday User_ID değerini kullanın @ no__t-0

* **Kullanıcıların Workday ve Active Directory arasında nasıl eşleşmesi gerekir?**

  * *Örnek: Belirli bir Workday "Worker_ID" değerine sahip olan kullanıcılar, "EmployeeID" değerinin aynı değere sahip olduğu Active Directory kullanıcılarla eşleştirilir. Active Directory Worker_ID değeri bulunmazsa yeni bir kullanıcı oluşturun.*
  
* **Active Directory ormanı, eşleşen mantığın çalışması için gereken kullanıcı kimliklerini zaten içeriyor mu?**

  * *Örnek: Bu kurulum yeni bir Workday dağıtımı ise, eşleşen mantığı mümkün olduğunca basit tutmak için doğru Workday Worker_ID değerlerinin (veya seçime özgü KIMLIK değeri) önceden doldurulduğundan Active Directory önerilir.*

Bu özel sağlama bağlayıcı uygulamalarını ayarlama ve yapılandırma, Bu öğreticinin kalan bölümlerinin konusudur. Yapılandırmak için seçtiğiniz uygulamalar hangi sistemlere sağlamanız gerektiği ve ortamınızda kaç tane Active Directory etki alanı ve Azure AD kiracılarının olduğunu gösterir.

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

    ![Kullanıcı oluştur](./media/workday-inbound-tutorial/wd_isu_01.png "Kullanıcı") oluşturma
2. Yeni bir tümleştirme sistemi kullanıcısı için bir Kullanıcı adı ve parola sağlayarak **tümleştirme sistemi kullanıcısı oluştur** görevini doldurun.  
  
* Bu Kullanıcı programlı olarak oturum **açacağından, bir sonraki oturum açma seçeneğinde yeni parola gerektir** seçeneğini işaretlenmemiş olarak bırakın.
* **Oturum zaman aşımı dakikalarını** varsayılan değeri olan 0 olarak bırakın, bu, kullanıcının oturumlarının zamanından önce tükenme süresini engeller.
* Tümleştirme sisteminin parolasının Workday 'de oturum açmasını engelleyen bir güvenlik katmanı sağladığından, Kullanıcı **arabirimi oturumlarına Izin verme** seçeneğini belirleyin.

    ![Tümleştirme sistemi kullanıcısı]oluşturma(./media/workday-inbound-tutorial/wd_isu_02.png "tümleştirme sistemi kullanıcısı") oluştur

### <a name="creating-an-integration-security-group"></a>Tümleştirme güvenlik grubu oluşturma

Bu adımda, Workday 'de kısıtlanmamış veya kısıtlanmış bir tümleştirme sistem güvenlik grubu oluşturacak ve önceki adımda oluşturulan Tümleştirme sistemi kullanıcısını bu gruba atayacaksınız.

**Bir güvenlik grubu oluşturmak için:**

1. Arama kutusuna güvenlik grubu oluştur yazın ve ardından **güvenlik grubu oluştur**' a tıklayın.

    ![Createsecurity grubu](./media/workday-inbound-tutorial/wd_isu_03.png "createsecurity grubu")
2. **Güvenlik grubu oluştur** görevini doldurun. 

   * Workday 'de iki tür güvenlik grubu vardır:
     * **Sınırlandırılmamış** Güvenlik grubunun tüm üyeleri güvenlik grubu tarafından güvenliği sağlanmış tüm veri örneklerine erişebilir.
     * **Kısıtlanmış** Tüm güvenlik grubu üyeleri, güvenlik grubunun erişebileceği veri örneklerinin (satırlar) bir alt kümesine bağlamsal erişime sahiptir.
   * Tümleştirme için uygun güvenlik grubu türünü seçmek için lütfen Workday Tümleştirme iş ortağınıza başvurun.
   * Grup türünü öğrendikten sonra, **Kiracı güvenlik grubu** açılır listesinden **tümleştirme sistem güvenlik grubu (Kısıtlanmamış)** veya **tümleştirme sistemi güvenlik grubu ' nu (kısıtlanmış)** seçin.

     ![Createsecurity grubu](./media/workday-inbound-tutorial/wd_isu_04.png "createsecurity grubu")

3. Güvenlik grubu oluşturma başarılı olduktan sonra güvenlik grubuna üye atayabileceğiniz bir sayfa görürsünüz. Önceki adımda oluşturulan yeni tümleştirme sistemi kullanıcısını bu güvenlik grubuna ekleyin. *Kısıtlanmış* güvenlik grubu kullanıyorsanız, uygun kuruluş kapsamını da seçmeniz gerekir.

    ![Güvenlik grubu](./media/workday-inbound-tutorial/wd_isu_05.png "düzenleme güvenlik grubunu") Düzenle

### <a name="configuring-domain-security-policy-permissions"></a>Etki alanı güvenlik ilkesi izinlerini yapılandırma

Bu adımda, güvenlik grubuna çalışan verileri için "etki alanı güvenliği" ilkesi izinleri verirsiniz.

**Etki alanı güvenlik ilkesi izinlerini yapılandırmak için:**

1. Arama kutusuna **etki alanı güvenlik yapılandırması** ' nı girin ve ardından **etki alanı güvenlik yapılandırması raporuna**tıklayın.  

    ![Etki alanı güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_06.png "etki alanı güvenlik ilkeleri")  
2. **Etki alanı** metin kutusunda, aşağıdaki etki alanlarını arayın ve tek bir filtreye tek tek ekleyin.  
   * *Dış hesap sağlama*
   * * çalışan verileri: Ortak çalışan raporları @ no__t-0
   * *Person verileri: İş Iletişim bilgileri @ no__t-0
   * * çalışan verileri: Tüm pozisyonlar @ no__t-0
   * * çalışan verileri: Geçerli personel bilgileri @ no__t-0
   * * çalışan verileri: Çalışan profili üzerinde iş unvanı @ no__t-0
   * *Workday hesapları*
   
     ![Etki alanı güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_07.png "etki alanı güvenlik ilkeleri")  

     ![Etki alanı güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_08.png "etki alanı güvenlik ilkeleri") 

     **Tamam**'ı tıklatın.

3. Görüntülenen raporda, **dış hesap sağlama** seçeneğinin yanında görüntülenen üç nokta (...) simgesini seçin ve **etki alanı-> güvenlik ilkesi izinlerini Düzenle** ' ye tıklayın.

    ![Etki alanı güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_09.png "etki alanı güvenlik ilkeleri")  

4. **Etki alanı Güvenlik Ilkesi Izinlerini Düzenle** sayfasında, bölüm **tümleştirme izinleri**bölümüne gidin. Tümleştirme sistem grubunu **Al** ve **Yerleştir** tümleştirme izinleri olan güvenlik grupları listesine eklemek için "+" işaretine tıklayın.

    ![İzin](./media/workday-inbound-tutorial/wd_isu_10.png "düzenleme iznini") Düzenle  

5. Tümleştirme sistem grubunu **Al** ve **Yerleştir** tümleştirme izinleri olan güvenlik grupları listesine eklemek için "+" işaretine tıklayın.

    ![İzin](./media/workday-inbound-tutorial/wd_isu_11.png "düzenleme iznini") Düzenle  

6. Bu kalan güvenlik ilkelerinin her biri için yukarıdaki 3-5 adımları yineleyin:

   | İşlem | Etki alanı Güvenlik Ilkesi |
   | ---------- | ---------- |
   | Al ve koy | Çalışan verileri: Ortak çalışan raporları |
   | Al ve koy | Kişi verileri: İş Iletişim bilgileri |
   | Al | Çalışan verileri: Tüm konumlar |
   | Al | Çalışan verileri: Geçerli personel bilgileri |
   | Al | Çalışan verileri: Çalışan profilinde iş unvanı |
   | Al ve koy | Workday hesapları |

### <a name="configuring-business-process-security-policy-permissions"></a>İş süreci güvenlik ilkesi izinlerini yapılandırma

Bu adımda, güvenlik grubuna çalışan verileri için "iş işlem güvenliği" ilkesi izinleri vereceksiniz. Bu adım, Workday geri yazma uygulaması bağlayıcısının kurulması için gereklidir.

**İş süreci güvenlik ilkesi izinlerini yapılandırmak için:**

1. Arama kutusuna **Iş süreci ilkesini** girin ve sonra **Iş sürecini Düzenle güvenlik ilkesi** görevi ' ne tıklayın.  

    ![Iş süreci güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_12.png "Iş süreci güvenlik ilkeleri")  

2. **Iş süreci türü** metin kutusunda, *iletişim* araması yapın ve **kişi Değiştir** iş işlemini seçin ve **Tamam**' a tıklayın.

    ![Iş süreci güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_13.png "Iş süreci güvenlik ilkeleri")  

3. **Iş sürecini Düzenle güvenlik ilkesi** sayfasında **kişi bilgilerini tut (Web hizmeti)** bölümüne gidin.

    ![Iş süreci güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_14.png "Iş süreci güvenlik ilkeleri")  

4. Web Hizmetleri isteğini başlatabilecek güvenlik grupları listesine yeni tümleştirme sistemi güvenlik grubunu seçin ve ekleyin. **Bitti**' ye tıklayın. 

    ![Iş süreci güvenlik ilkeleri](./media/workday-inbound-tutorial/wd_isu_15.png "Iş süreci güvenlik ilkeleri")  

### <a name="activating-security-policy-changes"></a>Güvenlik ilkesi değişikliklerini etkinleştirme

**Güvenlik ilkesi değişikliklerini etkinleştirmek için:**

1. Arama kutusuna etkinleştir ' i girin ve ardından **bekleyen Güvenlik Ilkesi değişikliklerini etkinleştir**' e tıklayın.

    ![](./media/workday-inbound-tutorial/wd_isu_16.png "Etkinleştirmeyi") etkinleştir

1. Denetim amaçlarıyla bir açıklama girerek bekleyen Güvenlik Ilkesi değişikliklerini etkinleştir görevini başlatın ve ardından **Tamam**' a tıklayın.
1. Onay kutusu **Onayla**' yı işaretleyerek görevi bir sonraki ekranda doldurun ve ardından **Tamam**' a tıklayın.

    ![Bekleyen güvenliği]etkinleştirme(./media/workday-inbound-tutorial/wd_isu_18.png "bekleyen güvenliği") etkinleştir  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday 'den Kullanıcı sağlamasını Active Directory olarak yapılandırma

Bu bölümde, tümleştirmenizi kapsamındaki her bir Active Directory etki alanı için Workday 'den Kullanıcı hesabı sağlama adımları sağlanır.

* [Şirket içi sağlama aracılarını yükleyip yapılandırın](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Sağlama bağlayıcı uygulaması ekleme ve iş günü bağlantısı oluşturma](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Öznitelik eşlemelerini yapılandırma](#part-3-configure-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>1\. Bölüm: Şirket içi sağlama aracılarını yükleyip yapılandırın

Şirket içi Active Directory sağlamak için, .NET 4.7.1 + Framework 'e sahip bir sunucuya ve istenen Active Directory etki alanına ağ erişimine sahip bir aracının yüklü olması gerekir.

> [!TIP]
> [Burada](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)belirtilen yönergeleri kullanarak sunucunuzdaki .NET Framework sürümünü kontrol edebilirsiniz.
> Sunucuda .NET 4.7.1 veya üzeri yüklü değilse [buradan](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)indirebilirsiniz.  

.NET 4.7.1 + dağıtıldıktan sonra şirket **[içi sağlama aracısını buraya](https://go.microsoft.com/fwlink/?linkid=847801)** indirebilir ve aracı yapılandırmasını gerçekleştirmek için aşağıda verilen adımları izleyebilirsiniz.

1. Yeni aracıyı yüklemek istediğiniz Windows Server 'da oturum açın.

1. Sağlama Aracısı yükleyicisini başlatın, koşulları kabul edin ve **yükleme** düğmesine tıklayın.

   ![Install Screen](./media/workday-inbound-tutorial/pa_install_screen_1.png "Install Screen")
   
1. Yükleme tamamlandıktan sonra, sihirbaz başlatılır ve **Azure AD 'ye Bağlan** ekranını görürsünüz. Azure AD örneğinize bağlanmak için **kimlik doğrula** düğmesine tıklayın.

   ![Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connect Azure AD") 'ye bağlanma
   
1. Genel yönetici kimlik bilgilerini kullanarak Azure AD örneğiniz için kimlik doğrulaması yapın.

   ![Yönetici kimlik doğrulama](./media/workday-inbound-tutorial/pa_install_screen_3.png "Yöneticisi kimlik doğrulaması")

   > [!NOTE]
   > Azure AD yönetici kimlik bilgileri yalnızca Azure AD kiracınıza bağlanmak için kullanılır. Aracı kimlik bilgilerini sunucuda yerel olarak depolamaz.

1. Azure AD ile başarılı bir kimlik doğrulamasından sonra, **Connect Active Directory** ekranını görürsünüz. Bu adımda, AD etki alanı adınızı girip **Dizin Ekle** düğmesine tıklayın.

   ![Dizin](./media/workday-inbound-tutorial/pa_install_screen_4.png "ekleme dizini") Ekle
  
1. Şimdi AD etki alanına bağlanmak için gereken kimlik bilgilerini girmeniz istenir. Aynı ekranda, aracının sağlama isteklerini göndermek için kullanması gereken etki alanı denetleyicilerini belirtmek için **etki alanı denetleyicisi önceliği Seç** ' i kullanabilirsiniz.

   ![Etki alanı kimlik bilgileri](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Etki alanı yapılandırıldıktan sonra yükleyici, yapılandırılmış etki alanlarının bir listesini görüntüler. Bu ekranda, daha fazla etki alanı eklemek için #5 ve #6 adımını yineleyebilirsiniz veya aracı kaydına devam etmek için **İleri** ' ye tıklayabilirsiniz.

   ![Yapılandırılmış etki alanı](./media/workday-inbound-tutorial/pa_install_screen_6.png "yapılandırılmış etki alanları")

   > [!NOTE]
   > Birden çok AD etki alanınız varsa (ör. na.contoso.com, emea.contoso.com), lütfen her bir etki alanını listeye ayrı olarak ekleyin.
   > Yalnızca üst etki alanı (örn. contoso.com) ekleme yeterli değildir. Her alt etki alanını aracıya kaydetmeniz gerekir.
   
1. Yapılandırma ayrıntılarını gözden geçirin ve aracıyı kaydetmek için **Onayla** ' ya tıklayın.
  
   ![Ekran](./media/workday-inbound-tutorial/pa_install_screen_7.png "silmeyi onaylayın ekranı")
   
1. Yapılandırma Sihirbazı aracı kaydının ilerlemesini görüntüler.
  
   ![Aracı kayıt](./media/workday-inbound-tutorial/pa_install_screen_8.png "Aracısı kaydı")
   
1. Aracı kaydı başarılı olduktan sonra sihirbazdan çıkmak için **Çıkış** ' a tıklayabilirsiniz.
  
   ![Çıkış ekranı](./media/workday-inbound-tutorial/pa_install_screen_9.png "Çıkış ekranı")
   
1. Aracının yüklenmesini doğrulayın ve "Hizmetler" eklentisini açıp "Microsoft Azure AD Connect sağlama Aracısı" adlı hizmeti arayın ve çalıştığından emin olun.
  
   ![Hizmetler](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>2\. Bölüm: Sağlama bağlayıcı uygulaması ekleme ve iş günü bağlantısı oluşturma

**İş Workday 'yi sağlamayı Active Directory üzere yapılandırmak için:**

1. Şuraya gidin: <https://portal.azure.com>

2. Sol gezinti çubuğunda **Azure Active Directory** ' yi seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve **Tüm** kategorisini seçin.

5. **Active Directory Için Workday sağlamasını**arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama** ' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirme

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şöyle görünmelidir: **kullanıcıadı @ no__t-1tenant_adı**

   * **Yönetici parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Kiracı URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. Bu değer şöyle görünmelidir: https://wd3-impl-services1.workday.com/ccx/service/contoso4, burada *contoso4* doğru kiracı adınızla değiştirilmiştir ve *WD3-Impl* doğru ortam dizesiyle değiştirilmiştir.

   * **Active Directory orman-** Active Directory etki alanının "adı", aracıyla birlikte kaydedilir. Sağlama için hedef etki alanını seçmek üzere açılan menüyü kullanın. Bu değer genellikle şöyle bir dizedir: *contoso.com*

   * **Active Directory kapsayıcı-** Aracının varsayılan olarak Kullanıcı hesapları oluşturması gereken kapsayıcı DN 'sini girin.
        Örnek: *OU = standart kullanıcılar, OU = kullanıcılar, DC = contoso, DC = test*
        
     > [!NOTE]
     > Bu ayar yalnızca *parentDistinguishedName* özniteliği öznitelik eşlemelerinde yapılandırılmamışsa, Kullanıcı hesabı oluşturmaları için oynatılır. Bu ayar Kullanıcı arama veya güncelleştirme işlemleri için kullanılmaz. Tüm etki alanı alt ağacının, arama işleminin kapsamında olması.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

     > [!NOTE]
     > Azure AD sağlama hizmeti, sağlama işi [karantina](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) durumuna geçtiğinde e-posta bildirimi gönderir.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, aracı kurulumunda yapılandırılan Workday kimlik bilgilerinin ve AD kimlik bilgilerinin geçerli olduğundan emin olun.

     ![Azure portal](./media/workday-inbound-tutorial/wd_1.png)

   * Kimlik bilgileri başarıyla kaydedildikten sonra, **eşlemeler** bölümü Iş **günü çalışanlarını şirket içi olarak eşitlemesini** varsayılan eşlemeyi gösterir Active Directory

### <a name="part-3-configure-attribute-mappings"></a>Bölüm 3: Öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin Workday 'den Active Directory 'e nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler**altındaki sağlama sekmesinde, Iş **günü çalışanlarını şirket Içi Active Directory ile senkronize**et ' e tıklayın.

1. **Kaynak nesne kapsamı** alanında, bir öznitelik tabanlı filtre kümesi tanımlayarak, Workday 'de hangi Kullanıcı kümelerinin ad sağlamak için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam "Workday 'deki tüm kullanıcılar" dır. Örnek filtreler:

   * Örnek: 1000000 ile 2000000 arasında çalışan kimlikleri olan kullanıcılara kapsam (2000000 hariç)

      * Özniteliğe Workerıd

      * İşlecinde REGEX eşleşmesi

      * Deeri (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Örnek: Yalnızca çalışanlar ve çalışanların çalışanları yoktur

      * Özniteliğe Çalışan

      * İşlecinde NULL DEĞIL

   > [!TIP]
   > Sağlama uygulamasını ilk kez yapılandırırken, istenen sonucu elde ettiğinizden emin olmak için öznitelik eşlemelerinizi ve ifadelerini test etmeniz ve doğrulamanız gerekir. Microsoft, iş Workday 'den birkaç test kullanıcısı ile eşlemelerinizi test etmek için **kaynak nesne kapsamı** kapsamındaki kapsam filtrelerinin kullanılmasını önerir. Eşlemelerin çalıştığını doğruladıktan sonra filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

   > [!CAUTION] 
   > Sağlama altyapısının varsayılan davranışı, kapsam dışına çıkan kullanıcıları devre dışı bırakmak/silmektir. Bu, Workday 'de AD tümleştirmesi için istenmeyebilir. Bu varsayılan davranışı geçersiz kılmak için, [kapsam dışı olan kullanıcı hesaplarını silmeyi atlama](../manage-apps/skip-out-of-scope-deletions.md) makalesine başvurun.
  
1. **Hedef nesne eylemleri** alanında Active Directory üzerinde gerçekleştirilen eylemleri genel olarak filtreleyebilirsiniz. **Oluşturma** ve **güncelleştirme** en yaygın olarak kullanılır.

1. **Öznitelik eşlemeleri** bölümünde, bağımsız Workday özniteliklerinin Active Directory özniteliklerle nasıl eşlendiğini tanımlayabilirsiniz.

1. Güncelleştirmek için varolan bir öznitelik eşlemesine tıklayın veya yeni eşlemeler eklemek için ekranın alt kısmındaki **Yeni eşleme Ekle** ' ye tıklayın. Tek bir öznitelik eşlemesi bu özellikleri destekler:

      * **Eşleme türü**

         * **Doğrudan** – Workday özniteliğinin değerini hiçbir DEĞIŞIKLIK olmadan ad özniteliğine yazar

         * **Sabit** -ad özniteliğine statik, sabit bir dize değeri yaz

         * **İfade** : bir veya daha fazla Workday özniteliğine göre, AD özniteliğine özel bir değer yazmanızı sağlar. [Daha fazla bilgi için ifadelerde bu makaleye bakın](../manage-apps/functions-for-customizing-application-data.md).

      * **Kaynak özniteliği** -Workday 'den Kullanıcı özniteliği. Aradığınız öznitelik yoksa, bkz. [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes).

      * **Varsayılan değer** : isteğe bağlı. Kaynak özniteliğinde boş bir değer varsa, eşleme bu değeri yazar.
            En yaygın yapılandırma bu boş bırakılmamalıdır.

      * **Target özniteliği** – Active Directory içindeki kullanıcı özniteliği.

      * **Bu özniteliği kullanarak nesneleri Eşleştir** – bu eşlemenin, Workday ve Active Directory arasındaki kullanıcıları benzersiz şekilde tanımlamak için kullanılıp kullanılmayacağını belirtir. Bu değer genellikle Active Directory iş günü için çalışan KIMLIĞI alanında ayarlanır. Bu, genellikle içindeki çalışan KIMLIĞI özniteliklerinden birine eşlenir.

      * **Eşleşen öncelik** – birden çok eşleşen öznitelik ayarlanabilir. Birden çok olduğunda, bu alan tarafından tanımlanan sıraya göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

      * **Bu eşlemeyi Uygula**

         * **Her zaman** : Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine Uygula

         * **Yalnızca oluşturma sırasında** -bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde Uygula

1. Eşlemelerinizi kaydetmek için öznitelik eşleme bölümünün en üstündeki **Kaydet** ' e tıklayın.

   ![Azure portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Aşağıda, bazı ortak ifadelerle Workday ve Active Directory arasındaki bazı örnek öznitelik eşlemeleri verilmiştir

* *ParentDistinguishedName* özniteliğiyle eşlenen ifade, bir veya daha fazla Workday kaynak özniteliğini temel alan farklı OU 'lara Kullanıcı sağlamak için kullanılır. Bu örnek, kullanıcıları farklı OU 'Lara, içinde oldukları şehirlere göre yerleştirirler.

* Active Directory ' deki *userPrincipalName* özniteliği, hedef ad etki alanında oluşturulan bir değerin varlığını denetleyen ve yalnızca benzersiz olduğunda ayarlayan, [Selectuniquevalue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) işlevi kullanılarak oluşturulur.  

* [İfadeleri buraya yazmak için bir belge vardır](../manage-apps/functions-for-customizing-application-data.md). Bu bölüm, özel karakterlerin nasıl kaldırılacağını gösteren örnekler içerir.

| WORKDAY ÖZNITELIĞI | ACTIVE DIRECTORY ÖZNITELIĞI |  EŞLEŞEN KIMLIK? | OLUŞTUR/GÜNCELLEŞTIR |
| ---------- | ---------- | ---------- | ---------- |
| **Workerıd**  |  Çalışan | **Evet** | Yalnızca oluşturma sırasında yazılmıştır |
| **PreferredNameData**    |  CN =    |   |   Yalnızca oluşturma sırasında yazılmıştır |
| **SelectUniqueValue (JOIN ("\@", JOIN ("." \[FirstName @ no__t-3, \[LastName @ no__t-5), "contoso.com"), JOIN ("\@", JOIN (".", Mid (\[FirstName @ no__t-8, 1, 1), \[LastName @ no__t-10), "contoso.com"), JOIN ("@no__ t-11 ", JOIN (". ", Mid (2FirstName @ no__t-13, 1, 2), 4LastName @ no__t-15)," contoso.com "))**   | userPrincipalName     |     | Yalnızca oluşturma sırasında yazılmıştır 
| **Değiştir(Orta(değiştirin(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Yalnızca oluşturma sırasında yazılmıştır |
| **Anahtar (\[Active @ no__t-2,, "0", "true", "1", "false")** |  accountDisabled      |     | Oluştur + güncelleştir |
| **FirstName**   | givenName       |     |    Oluştur + güncelleştir |
| **Soyadı**   |   sn   |     |  Oluştur + güncelleştir |
| **PreferredNameData**  |  displayName |     |   Oluştur + güncelleştir |
| **Şirketlerin**         | Şirket   |     |  Oluştur + güncelleştir |
| **Supervisorspganleştirme**  | Bölüm  |     |  Oluştur + güncelleştir |
| **ManagerReference**   | yönetici  |     |  Oluştur + güncelleştir |
| **Businestıtle**   |  title     |     |  Oluştur + güncelleştir | 
| **AddressLineData**    |  streetAddress  |     |   Oluştur + güncelleştir |
| **Belediye**   |   m   |     | Oluştur + güncelleştir |
| **CountryReferenceTwoLetter**      |   Ortak |     |   Oluştur + güncelleştir |
| **CountryReferenceTwoLetter**    |  c  |     |         Oluştur + güncelleştir |
| **CountryRegionReference** |  St     |     | Oluştur + güncelleştir |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Oluştur + güncelleştir |
| **PostalCode**  |   posta kodu  |     | Oluştur + güncelleştir |
| **PrimaryWorkTelephone**  |  telephoneNumber 'dır   |     | Oluştur + güncelleştir |
| **Faks**      | facsimileTelephoneNumber     |     |    Oluştur + güncelleştir |
| **Mo**  |    Mobil       |     |       Oluştur + güncelleştir |
| **LocalReference** |  preferredLanguage  |     |  Oluştur + güncelleştir |                                               
| **Anahtar (\[Municipitesi @ no__t-2 "OU = standart kullanıcılar, OU = kullanıcılar, OU = varsayılan, OU = konumlar, DC = contoso, DC = com", "Dallas", "OU = standart kullanıcılar, OU = kullanıcılar, OU = Dallas, OU = konumlar, DC = contoso, DC = com", "Austin", "OU = standart kullanıcılar, OU = kullanıcılar, OU = Austin, OU = konumlar, DC = contoso, DC = com "," Seattle "," OU = standart kullanıcılar, OU = kullanıcılar, OU = Seattle, OU = konumlar, DC = contoso, DC = com "," Londra "," OU = standart kullanıcılar, OU = kullanıcılar, OU = Londra, OU = konumlar, DC = contoso, DC = com ")**  | parentDistinguishedName     |     |  Oluştur + güncelleştir |

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, artık [Kullanıcı sağlama hizmetini etkinleştirebilir ve başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Azure AD 'de Kullanıcı sağlamayı yapılandırma

Aşağıdaki bölümlerde, yalnızca bulutta bulunan dağıtımlar için Workday 'den Azure AD 'ye Kullanıcı sağlamayı yapılandırmaya yönelik adımlar açıklanır.

* [Azure AD sağlama bağlayıcı uygulamasını ekleme ve Workday bağlantısı oluşturma](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday ve Azure AD öznitelik eşlemelerini yapılandırma](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Yalnızca, şirket içi Active Directory değil, Azure AD 'ye sağlanması gereken yalnızca bulutta bulunan kullanıcılarınız varsa aşağıdaki yordamı izleyin.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1\. Bölüm: Azure AD sağlama bağlayıcı uygulamasını ekleme ve Workday bağlantısı oluşturma

**Workday 'i yalnızca bulutta bulunan kullanıcıların sağlamasını Azure Active Directory üzere yapılandırmak için:**

1. <https://portal.azure.com> kısmına gidin.

2. Sol gezinti çubuğunda **Azure Active Directory** ' yi seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve ardından **Tüm** kategoriyi seçin.

5. **Azure AD sağlaması Için Workday**'i arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama** ' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirme

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şuna benzemelidir:username@contoso4

   * **Yönetici parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Kiracı URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. Bu değer şöyle görünmelidir: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, burada *contoso4* doğru kiracı adınızla değiştirilmiştir ve *WD3-Impl* doğru ortam dizesiyle değiştirilmiştir. Bu URL bilinmiyorsa, kullanılacak doğru URL 'YI öğrenmek için lütfen Workday Tümleştirme iş ortağınızdan veya destek temsilcisiyle çalışın.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

   * **Bağlantıyı Sına** düğmesine tıklayın.

   * Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, Workday URL 'SI ve kimlik bilgilerinin Workday 'de geçerli olduğundan emin olun.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2\. Bölüm: Workday ve Azure AD öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin Workday 'den yalnızca bulutta bulunan kullanıcılar için Azure Active Directory nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler**altındaki sağlama sekmesinde **çalışanları Azure AD ile senkronize**et ' e tıklayın.

2. **Kaynak nesne kapsamı** alanında, bir öznitelik tabanlı filtre kümesi tanımlayarak Workday 'deki hangi Kullanıcı KÜMELERININ Azure AD 'ye sağlanması için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam "Workday 'deki tüm kullanıcılar" dır. Örnek filtreler:

   * Örnek: 1000000 ile 2000000 arasında çalışan kimlikleri olan kullanıcılara kapsam

      * Özniteliğe Workerıd

      * İşlecinde REGEX eşleşmesi

      * Deeri (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Örnek: Yalnızca çalışanların çalışanları ve normal çalışanları değil

      * Özniteliğe Kıgentıd

      * İşlecinde NULL DEĞIL

3. **Hedef nesne eylemleri** alanında, Azure AD 'de gerçekleştirilen eylemlerin genel olarak filtrelemesini sağlayabilirsiniz. **Oluşturma** ve **güncelleştirme** en yaygın olarak kullanılır.

4. **Öznitelik eşlemeleri** bölümünde, bağımsız Workday özniteliklerinin Active Directory özniteliklerle nasıl eşlendiğini tanımlayabilirsiniz.

5. Güncelleştirmek için varolan bir öznitelik eşlemesine tıklayın veya yeni eşlemeler eklemek için ekranın alt kısmındaki **Yeni eşleme Ekle** ' ye tıklayın. Tek bir öznitelik eşlemesi bu özellikleri destekler:

   * **Eşleme türü**

      * **Doğrudan** – Workday özniteliğinin değerini hiçbir DEĞIŞIKLIK olmadan ad özniteliğine yazar

      * **Sabit** -ad özniteliğine statik, sabit bir dize değeri yaz

      * **İfade** : bir veya daha fazla Workday özniteliğine göre, AD özniteliğine özel bir değer yazmanızı sağlar. [Daha fazla bilgi için ifadelerde bu makaleye bakın](../manage-apps/functions-for-customizing-application-data.md).

   * **Kaynak özniteliği** -Workday 'den Kullanıcı özniteliği. Aradığınız öznitelik yoksa, bkz. [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes).

   * **Varsayılan değer** : isteğe bağlı. Kaynak özniteliğinde boş bir değer varsa, eşleme bu değeri yazar.
            En yaygın yapılandırma bu boş bırakılmamalıdır.

   * **Target özniteliği** – Azure AD 'deki Kullanıcı özniteliği.

   * **Bu özniteliği kullanarak nesneleri Eşleştir** – bu özniteliğin Workday ve Azure AD arasındaki kullanıcıları benzersiz şekilde tanımlamak için kullanılması gerekip gerekmediğini belirtir. Bu değer genellikle, genellikle çalışan KIMLIĞI özniteliğiyle (yeni) veya Azure AD 'de bir uzantı özniteliğiyle eşlenen Workday için çalışan KIMLIĞI alanında ayarlanır.

   * **Eşleşen öncelik** – birden çok eşleşen öznitelik ayarlanabilir. Birden çok olduğunda, bu alan tarafından tanımlanan sıraya göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

   * **Bu eşlemeyi Uygula**

     * **Her zaman** : Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine Uygula

     * **Yalnızca oluşturma sırasında** -bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde Uygula

6. Eşlemelerinizi kaydetmek için öznitelik eşleme bölümünün en üstündeki **Kaydet** ' e tıklayın.

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, artık [Kullanıcı sağlama hizmetini etkinleştirebilir ve başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Azure AD öznitelik geri yazma özelliğini Workday 'e yapılandırma

Kullanıcı e-posta adreslerinin ve Kullanıcı adının Azure Active Directory ' den Workday 'e geri yazmayı yapılandırmak için bu yönergeleri izleyin.

* [Geri yazma Bağlayıcısı uygulamasını ekleme ve Workday bağlantısı oluşturma](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Geri yazma öznitelik eşlemelerini yapılandırma](#part-2-configure-writeback-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1\. Bölüm: Geri yazma Bağlayıcısı uygulamasını ekleme ve Workday bağlantısı oluşturma

**Workday geri yazma bağlayıcısını yapılandırmak için:**

1. Şuraya gidin: <https://portal.azure.com>

2. Sol gezinti çubuğunda **Azure Active Directory** ' yi seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin, ardından **Tüm** kategorisini seçin.

5. **Workday geri yazma**araması yapın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama** ' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirme

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şöyle görünmelidir: *kullanıcıadı @ no__t-1contoso4*

   * **Yönetici parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Kiracı URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. Bu değer şöyle görünmelidir: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, burada *contoso4* doğru kiracı adınızla değiştirilmiştir ve *WD3-Impl* doğru ortam dizesiyle (gerekliyse) değiştirilmiştir.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, Workday URL 'SI ve kimlik bilgilerinin Workday 'de geçerli olduğundan emin olun.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2\. Bölüm: Geri yazma öznitelik eşlemelerini yapılandırma

Bu bölümde, geri yazma özniteliklerinin Azure AD 'den Workday 'e nasıl akabildiğini yapılandıracaksınız. Mevcut olduğunda, bağlayıcı yalnızca e-posta adresinin ve Kullanıcı adının yeniden iş Workday 'ye geri yazmayı destekler.

1. **Eşlemeler**altındaki sağlama sekmesinde **Azure Active Directory Kullanıcıları Iş günü ile eşitler**' ı tıklatın.

2. **Kaynak nesne kapsamı** alanında, Azure Active Directory ' deki Kullanıcı kümelerinin, e-posta adreslerinin Workday 'e geri yazılmasını sağlamak için isteğe bağlı olarak filtre uygulayabilirsiniz. Varsayılan kapsam, "Azure AD 'deki tüm kullanıcılar" dır.

3. **Öznitelik eşlemeleri** bölümünde, iş günü çalışan kimliği veya çalışan kimliğinin depolandığı Azure Active Directory özniteliğini belirtmek IÇIN eşleşen kimliği güncelleştirin. Popüler bir eşleştirme yöntemi, Workday çalışan KIMLIĞI 'ni veya çalışan KIMLIĞINI Azure AD 'de extensionAttribute1-15 ile eşitlemeniz ve ardından Azure AD 'de bu özniteliği kullanarak Workday 'de kullanıcıları eşleştirmek için kullanılır.

4. Genellikle Azure AD *userPrincipalName* özniteliğini Workday *KullanıcıKimliği* ÖZNITELIĞINE eşleyin ve Azure AD *posta* özniteliğini Workday *emadresi* özniteliğiyle eşleyin. Eşlemelerinizi kaydetmek için öznitelik eşleme bölümünün en üstündeki **Kaydet** ' e tıklayın.

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, artık [Kullanıcı sağlama hizmetini etkinleştirebilir ve başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Workday sağlama uygulama yapılandırması tamamlandıktan sonra, Azure portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşleme veya Workday veri sorunlarında hatalar varsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi test eden birkaç test kullanıcıyla test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** sekmesinde, **sağlama durumunu** **Açık**olarak ayarlayın.

2. **Kaydet**’e tıklayın.

3. Bu işlem, iş günü kiracısında kaç Kullanıcı olduğuna bağlı olarak değişken sayıda saat sürebilen ilk eşitlemeyi başlatacak. 

4. Herhangi bir zamanda, sağlama hizmeti 'nin gerçekleştirdiği işlemleri görmek için Azure portal **Denetim günlükleri** sekmesini kontrol edin. Denetim günlükleri, sağlama hizmeti tarafından gerçekleştirilen tüm bireysel eşitleme olaylarını listeler (örneğin, Workday 'den okuma ve daha sonra Active Directory için eklenen veya güncellenen). Denetim günlüklerinin incelenmesi ve sağlama hatalarının düzeltilmesi hakkındaki yönergeler için sorun giderme bölümüne bakın.

5. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesinde bir denetim Özeti raporu yazar.

   ![Azure portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Sık Sorulan Sorular (SSS)

* **Çözüm yeteneği soruları**
  * [Workday 'den yeni bir işe alma işlemi sırasında, çözüm Active Directory yeni kullanıcı hesabının parolasını nasıl ayarladı?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Çözüm, sağlama işlemleri tamamlandıktan sonra e-posta bildirimleri göndermeyi destekliyor mu?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Yeni işe alımlar için parolaların teslimini yönetmek ve parolasını sıfırlamak için güvenli bir mekanizma sağlamak nasıl yaparım? misiniz?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Çözüm, Azure AD bulutu 'nda veya sağlama Aracısı katmanında iş günü Kullanıcı profillerini önbelleğe ister misiniz?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Çözüm, kullanıcıya şirket içi AD grupları atamayı destekliyor mu?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Çözüm iş günü çalışan profillerini sorgulamak ve güncelleştirmek için hangi Workday API 'Leri kullanır?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Workday HCM kiracımı iki Azure AD kiracısıyla yapılandırabilir miyim?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Azure AD Connect dağıttıysanız, neden "Azure AD 'ye iş günü" Kullanıcı sağlama uygulaması desteklenmez?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Nasıl yaparım?, iş günü ve Azure AD tümleştirmesiyle ilgili iyileştirmeler önerin veya yeni özellikler ister misiniz?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Aracı sorularını sağlama**
  * [Sağlama aracısının GA sürümü nedir?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Nasıl yaparım? sağlama aracımın sürümü bilinmiyor mu?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft, sağlama Aracısı güncelleştirmelerini otomatik olarak gönderir mi?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Sağlama aracısını AAD Connect çalıştıran aynı sunucuya yükleyebilir miyim?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Nasıl yaparım? sağlama aracısını giden HTTP iletişimi için bir proxy sunucu kullanacak şekilde yapılandırmak istiyor musunuz?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Nasıl yaparım? sağlama aracısının Azure AD kiracısıyla iletişim kurabildiğinden ve aracı tarafından istenen bağlantı noktalarını engellemediğinden emin olun.](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Sağlama aracıma ilişkili etki alanını Nasıl yaparım? de kaydetmek istiyor musunuz?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Sağlama aracısını kaldırmak Nasıl yaparım? mı?](#how-do-i-uninstall-the-provisioning-agent)
  
* **AD özniteliği eşleme ve yapılandırma sorularına iş günü**
  * [Nasıl yaparım? Workday sağlama özniteliği eşleşmesinin ve şemadaki çalışma kopyasını yedekleyin veya dışarı aktarın.](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [I, Workday ve Active Directory özel özniteliklere sahip. Çözümü özel özniteliklerim ile çalışacak şekilde yapılandırmak Nasıl yaparım? ](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
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

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Yeni işe alımlar için parolaların teslimini yönetmek ve parolasını sıfırlamak için güvenli bir mekanizma sağlamak nasıl yaparım? misiniz?

Yeni AD hesabı sağlamaya yönelik son adımlardan biri, kullanıcının AD hesabına atanan geçici parolanın tesliminin yer aldığı durumdur. Birçok kuruluş hala Kullanıcı yöneticisine geçici parola sunmaya yönelik geleneksel yaklaşımı kullanır, bu da yeni işe alma/iş çalışanının parolasını ele alabilir. Bu işlem, bir güvenlik kusurunu içerir ve Azure AD yeteneklerini kullanarak daha iyi bir yaklaşım uygulamak için kullanabileceğiniz bir seçenek vardır.

İşe alma sürecinin bir parçası olarak, HR ekipleri genellikle bir arka plan denetimi çalıştırır ve yeni işe alım cep telefonu numarasını ister. Kullanıcı sağlama tümleştirmesi için Workday ile, bu olgunun üzerine derleyebilir ve Kullanıcı için bir self servis parola sıfırlama özelliğini 1. gün üzerinde dağıtabilirsiniz. Bu, yeni işe alma gününün "cep telefonu numarası" özniteliğini AD 'ye, sonra da AAD Connect kullanılarak AD 'den Azure AD 'ye yayılarak gerçekleştirilir. Azure AD 'de "cep telefonu numarası" mevcut olduğunda, Kullanıcı hesabı için [self servis parola sıfırlama (SSPR)](../authentication/howto-sspr-authenticationdata.md) özelliğini etkinleştirebilirsiniz. böylece, 1. gün, yeni bir işe kimlik doğrulama için kayıtlı ve onaylanmış mobil numarayı kullanabilir.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Çözüm, Azure AD bulutu 'nda veya sağlama Aracısı katmanında iş günü Kullanıcı profillerini önbelleğe ister misiniz?

Hayır, çözüm Kullanıcı profillerinin bir önbelleğini korumaz. Azure AD sağlama hizmeti yalnızca bir veri işlemcisi olarak davranır, Workday 'den verileri okur ve hedef Active Directory ya da Azure AD 'ye yazma. Kullanıcı gizliliği ve veri saklama ile ilgili ayrıntılar için [kişisel verileri yönetme](#managing-personal-data) bölümüne bakın.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Çözüm, kullanıcıya şirket içi AD grupları atamayı destekliyor mu?

Bu işlev şu anda desteklenmiyor. Önerilen geçici çözüm, denetim günlüğü verilerine yönelik Azure AD Graph API uç noktasını sorgulayan ve Grup atama gibi senaryoları tetiklemek için bunu kullanan bir PowerShell betiği dağıtmaktır. Bu PowerShell betiği bir görev zamanlayıcısına iliştirilebilir ve sağlama Aracısı 'nı çalıştıran aynı kutuya dağıtılabilir.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Çözüm iş günü çalışan profillerini sorgulamak ve güncelleştirmek için hangi Workday API 'Leri kullanır?

Çözüm şu anda aşağıdaki Workday API 'Lerini kullanmaktadır:

* Çalışan bilgilerini getirmek için Get_Workers (v 21.1)
* Iş e-postası geri yazma özelliği için Maintain_Contact_Information (v 26.1)
* Kullanıcı adı geri yazma özelliği için Update_Workday_Account (v 31.2)

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Workday HCM kiracımı iki Azure AD kiracısıyla yapılandırabilir miyim?

Evet, bu yapılandırma desteklenir. Bu senaryoyu yapılandırmak için üst düzey adımlar aşağıda verilmiştir:

* #1 sağlama aracısını dağıtın ve Azure AD kiracı #1 ile kaydedin.
* #2 sağlama aracısını dağıtın ve Azure AD kiracı #2 ile kaydedin.
* Her bir sağlama aracısının yöneteceği "alt etki alanları" temelinde, her aracıyı etki alanı (ler) ile yapılandırın. Bir aracı birden çok etki alanını işleyebilir.
* Azure portal, Workday 'yi her kiracıda AD Kullanıcı sağlama uygulaması olarak ayarlayın ve ilgili etki alanlarıyla yapılandırın.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Azure AD Connect dağıttıysanız, neden "Azure AD 'ye iş günü" Kullanıcı sağlama uygulaması desteklenmez?

Azure AD karma modda kullanıldığında (bulutta ve şirket içi kullanıcıların bir karışımını içerdiğinde), "yetki kaynağı" olarak açık bir tanım olması önemlidir. Genellikle Karma senaryolar Azure AD Connect dağıtımı gerektirir. Azure AD Connect dağıtıldığında şirket içi AD, yetkilinin kaynağıdır. Işgününde Azure AD Bağlayıcısı 'na giriş, Workday öznitelik değerlerinin Azure AD Connect tarafından ayarlanan değerlerin üzerine yazabileceği bir duruma yol açabilir. Bu nedenle, Azure AD Connect etkinleştirildiğinde "Workday for Azure AD" sağlama uygulamasının kullanılması desteklenmez. Bu gibi durumlarda, kullanıcıların şirket içi AD 'ye alınması ve Azure AD Connect kullanarak bunları Azure AD 'ye eşitlemesini sağlamak için "iş Workday for AD user" sağlama uygulamasının kullanılması önerilir.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Nasıl yaparım?, iş günü ve Azure AD tümleştirmesiyle ilgili iyileştirmeler önerin veya yeni özellikler ister misiniz?

Geri bildiriminiz, gelecekteki sürümlerin ve geliştirmelerin yönünü belirlememize yardımcı olduğu için oldukça önemlidir. Tüm geri bildirimlerinizi destekliyoruz ve [Azure AD 'nin geri bildirim forumuna](https://feedback.azure.com/forums/169401-azure-active-directory)fikir veya geliştirme önerilerinizi göndermenize önerilir. Workday tümleştirmesiyle ilgili belirli geri bildirimler için, iş Workday ile ilgili mevcut geri bildirimleri bulmak için Workday *uygulamalarını* seçin ve *Workday* anahtar sözcüklerini kullanarak arama yapın.

![UserVoice SaaS uygulamaları](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Yeni bir fikir önerirken, başka birinin zaten benzer bir özelliği önerdiğinden emin olun. Bu durumda, özelliği veya geliştirme isteğini oylayabilirsiniz. Ayrıca, fikir desteğiniz için destek göstermek ve özelliğin sizin için nasıl değerli olacağını göstermek için, özel kullanım durumu ile ilgili bir yorum bırakabilirsiniz.

### <a name="provisioning-agent-questions"></a>Aracı sorularını sağlama

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Sağlama aracısının GA sürümü nedir?

* Sağlama aracısının GA sürümü 1.1.30 ve üzeri.
* Aracı sürümünüz 1.1.30 'den küçükse, genel önizleme sürümünü çalıştırıyorsunuz ve aracıyı barındıran sunucuda .NET 4.7.1 çalışma zamanı varsa, bu sürüm otomatik olarak GA sürümüne güncelleştirilir.
  * Sunucunuzda yüklü [.NET sürümünü kontrol](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) edebilirsiniz. Sunucu .NET 4.7.1 çalıştırıyorsa, [.NET 4.7.1 indirebilir ve yükleyebilirsiniz](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). .NET 4.7.1 yükledikten sonra sağlama aracınız, otomatik olarak GA sürümüne güncelleştirilir.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Nasıl yaparım? sağlama aracımın sürümü bilinmiyor mu?

* Sağlama aracısının yüklendiği Windows Server 'da oturum açın.
* **Denetim masası** -> **bir program menüsünü kaldırma veya değiştirme**
* Girişe karşılık gelen sürümü ara **Microsoft Azure AD sağlama aracısına Bağlan**

  ![Azure portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft, sağlama Aracısı güncelleştirmelerini otomatik olarak gönderir mi?

Evet, Microsoft sağlama aracısını otomatik olarak güncelleştirir. Windows hizmeti **Microsoft Azure AD Connect Agent güncelleştiricisi**' ni durdurup otomatik güncelleştirmeleri devre dışı bırakabilirsiniz.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Sağlama aracısını AAD Connect çalıştıran aynı sunucuya yükleyebilir miyim?

Evet, sağlama aracısını AAD Connect çalıştıran aynı sunucuya yükleyebilirsiniz.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Yapılandırma sırasında, sağlama Aracısı Azure AD yöneticisi kimlik bilgilerini ister. Aracı kimlik bilgilerini sunucuda yerel olarak depolar mı?

Yapılandırma sırasında, sağlama Aracısı Azure AD yönetici kimlik bilgilerinin yalnızca Azure AD kiracınıza bağlanmasını ister. Kimlik bilgilerini sunucuda yerel olarak depolamaz. Ancak, yerel bir Windows parola kasasındaki *Şirket içi Active Directory etki alanına* bağlanmak için kullanılan kimlik bilgilerini korur.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Nasıl yaparım? sağlama aracısını giden HTTP iletişimi için bir proxy sunucu kullanacak şekilde yapılandırmak istiyor musunuz?

Sağlama Aracısı giden ara sunucu kullanımını destekler. Bunu, **C:\Program Files\Microsoft Azure AD Connect sağlama Agent\AADConnectProvisioningAgent.exe.config**aracı yapılandırma dosyasını düzenleyerek yapılandırabilirsiniz. Aşağıdaki satırları, kapanış `</configuration>` etiketinden hemen önce dosyanın sonuna doğru bir şekilde ekleyin.
[Proxy-Server] ve [proxy-port] değişkenlerini ara sunucu adı ve bağlantı noktası değerlerinizle değiştirin.

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

Ayrıca, şirket içi ağınızdan [bağlayıcı bağlantı noktaları test aracını](https://aadap-portcheck.connectorporttest.msappproxy.net/) açarak gerekli tüm bağlantı noktalarının açık olup olmadığını kontrol edebilirsiniz. Daha fazla yeşil onay işaretleri koyacağız büyük dayanıklılık anlamına gelir.

Aracı size doğru sonuçları emin olmak için emin olun:

* Aracı, sağlama aracısını yüklediğiniz sunucudan bir tarayıcıda açın.
* Sağlama aracılarınız için geçerli olan tüm proxy veya güvenlik duvarlarının da bu sayfaya uygulandığından emin olun. Bu, Internet Explorer 'da **Ayarlar-> Internet seçenekleri-> bağlantılar-> LAN ayarları**' na giderek yapılabilir. Bu sayfada, "LAN için bir proxy sunucu kullanın" alanını görürsünüz. Bu kutuyu seçin ve proxy adresini "adres" alanına yerleştirin.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Birden çok AD etki alanı sağlamak için bir sağlama Aracısı yapılandırılmış olabilir mi?

Evet, bir sağlama Aracısı, aracı ilgili etki alanı denetleyicilerine bir görüş satırı olduğu sürece birden çok AD etki alanını işleyecek şekilde yapılandırılabilir. Microsoft, yüksek kullanılabilirlik sağlamak ve yük devretme desteği sağlamak için aynı AD etki alanı kümesine hizmet veren 3 sağlama Aracısı grubu ayarlamayı önerir.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Sağlama aracıma ilişkili etki alanını Nasıl yaparım? de kaydetmek istiyor musunuz?

* Azure portal Azure AD kiracınızın *KIRACı kimliğini* alın.
* Sağlama Aracısı 'nı çalıştıran Windows Server 'da oturum açın.
* PowerShell 'i Windows Yöneticisi olarak açın.
* Kayıt betiklerini içeren dizine geçin ve Kiracı kimliği \[\] parametresini kiracı kimliğiniz değeriyle değiştirerek aşağıdaki komutları çalıştırın.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Görüntülenen aracılar listesinden, "ID" alanının değerini, *resourceName* değeri ad etki alanı adına eşit olan kaynaktan kopyalayın.
* KIMLIK değerini bu komuta yapıştırın ve PowerShell 'de komutunu yürütün.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Aracı yapılandırma sihirbazını yeniden çalıştırın.
* Daha önce bu etki alanına atanan diğer aracıların yeniden yapılandırılması gerekir.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Sağlama aracısını kaldırmak Nasıl yaparım? mı?

* Sağlama aracısının yüklendiği Windows Server 'da oturum açın.
* **Denetim masası** -> **bir program menüsünü kaldırma veya değiştirme**
* Aşağıdaki programları kaldırın:
  * Microsoft Azure AD sağlama aracısına bağlanma
  * Microsoft Azure AD Aracısı güncelleştiricisi bağlama
  * Microsoft Azure AD sağlama Aracısı paketini bağlama

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>AD özniteliği eşleme ve yapılandırma sorularına iş günü

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Nasıl yaparım? Workday sağlama özniteliği eşleşmesinin ve şemadaki çalışma kopyasını yedekleyin veya dışarı aktarın.

Microsoft Graph API 'yi kullanarak Workday Kullanıcı sağlama yapılandırmanızı dışarı aktarabilirsiniz. Ayrıntılar için [Workday Kullanıcı sağlama özniteliği eşleme yapılandırmanızı dışarı ve içeri](#exporting-and-importing-your-configuration) aktarma bölümündeki adımlara bakın.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Workday ve Active Directory özel öznitelikleri var. Nasıl yaparım? çözümü özel özniteliklerim ile çalışacak şekilde yapılandırmak mı istiyorsunuz?

Çözüm, özel Workday ve Active Directory özniteliklerini destekler. Özel öznitelerinizi eşleme şemasına eklemek için, **öznitelik eşleme** dikey penceresini açın ve aşağı kaydırarak **Gelişmiş seçenekleri göster**bölümünü genişletin. 

![Öznitelik Listesini Düzenle](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Özel Workday öznitelerinizi eklemek için, *Workday için öznitelik listesini düzenle* seçeneğini belirleyin ve özel ad öznitelerinizi eklemek Için, *Şirket içi Active Directory Için öznitelik listesini düzenle*seçeneğini belirleyin.

Ayrıca bkz:

* [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Nasıl yaparım?, iş günü değişikliklerine göre yalnızca AD içindeki öznitelikleri güncelleştirecek şekilde yapılandırmak ve yeni bir AD hesabı oluşturmamalıdır.

Bu yapılandırma, aşağıda gösterildiği gibi **öznitelik eşlemeleri** dikey penceresindeki **hedef nesne eylemleri** ayarlanarak elde edilebilir:

![Eylemi güncelleştir](./media/workday-inbound-tutorial/wd_target_update_only.png)

Yalnızca güncelleştirme işlemlerinin Workday 'den AD 'ye akmasını sağlamak için "Güncelleştir" onay kutusunu seçin. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>İş gününde Kullanıcı fotoğrafını Active Directory sağlayabilir miyim?

Çözüm şu anda Active Directory içindeki *thumbnailPhoto* ve *jpegPhoto* gibi ikili özniteliklerin ayarlanmasını desteklemez.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Nasıl yaparım? ortak kullanım için Kullanıcı onayı temelinde Workday 'den cep telefonu numaralarını eşitlemek mi istiyorsunuz?

* Workday sağlama uygulamanızın "sağlama" dikey penceresine gidin.
* Öznitelik eşlemelerine tıklayın 
* **Eşlemeler**altında, Iş **günü çalışanlarını şirket Içi Active Directory ile eşitlemeyi** (veya **Workday çalışanlarını Azure AD 'ye eşitlemeyi**) seçin.
* Öznitelik eşlemeleri sayfasında, aşağı kaydırın ve "Gelişmiş seçenekleri göster" kutusunu işaretleyin.  **Workday için öznitelik listesini düzenle** 'ye tıklayın
* Açılan dikey pencerede, "mobil" özniteliğini bulun ve **API ifadesini** düzenleyebilmek için ![MOBILE GDPR @ no__t-2 ' ye tıklayın.

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

* Her Workday özniteliği, **öznitelik eşleme-> Gelişmiş bölüm-> Iş günü için öznitelik listesini düzenle**' de yapılandırılabilir olan temel BIR XPath API ifadesi kullanılarak alınır. Workday *adı*, *preferredlastname*, *Company* ve *supervisorspganleştirme* öznitelikleri için varsayılan XPath API ifadesi aşağıda verilmiştir.

     | Workday özniteliği | API XPATH Ifadesi |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/Text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/Text () |
     | Şirket | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [@wd:type = ' Organization_Type_ID '] = ' Company '] /wd:Organization_Reference/@wd:Descriptor |
     | Supervisorspganleştirme | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [@wd:type = ' Organization_Type_ID '] = ' supervis, ']/WD: organi/Text () |
  
   Workday kiracınızda, yukarıdaki API ifadesinin Workday kiracı yapılandırmanız için geçerli olduğunu doğrulayın. Gerekirse, [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)bölümünde açıklandığı gibi düzenleyebilirsiniz.

* Benzer şekilde, Workday 'de bulunan ülke bilgileri şu XPATH kullanılarak alınır: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Workday öznitelik listesi bölümünde bulunan 5 ülkenin ilişkili özniteliği vardır.

     | Workday özniteliği | API XPATH Ifadesi |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type = ' ISO_3166-1_Alpha-3_Code ']/text () |
     | Countryreferencedostu | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type = ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type = ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Workday kiracınızda, yukarıdaki API ifadelerinin Workday kiracı yapılandırmanız için geçerli olduğunu doğrulayın. Gerekirse, [Workday kullanıcı özniteliklerinin listesini özelleştirme](#customizing-the-list-of-workday-user-attributes)bölümünde açıklandığı gibi düzenleyebilirsiniz.

* Doğru öznitelik eşleme ifadesini oluşturmak için, kullanıcının adı, soyadı, ülkeyi/bölgeyi ve departmanı temsil eden "yetkili" iş günü özniteliğini belirler. Öznitelikleri, sırasıyla *Preferredfirstname*, *preferredlastname*, *countryreferencetwoletter* ve *supervisoryorgansılet* olduğunu varsayalım. Bunu *, Smith, John (Marketing-US)* gibi bir ekran adı almak için AŞAĞıDAKI gibi ad *DisplayName* özniteliği için bir ifade oluşturmak üzere kullanabilirsiniz.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Doğru ifadeye sahip olduktan sonra, öznitelik eşlemeleri tablosunu düzenleyin ve *DisplayName* öznitelik eşlemesini aşağıda gösterildiği gibi değiştirin:   ![DisplayName eşlemesi @ no__t-1

* Yukarıdaki örneği genişlettikten sonra, Workday 'den gelen şehir adlarını stenen değerlere dönüştürmek ve sonra *Smith, John (çi)* veya *tikan (NYC)* gibi görünen adlar oluşturmak için kullanmak istediğinizi varsayalım, bu nedenle bu sonuç bir anahtar kullanılarak elde edilebilir ifade *Municipitesi* özniteliği, determinantı olan ifade.

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
  * [Işlev sözdizimini Değiştir](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [JOIN Işlevi sözdizimi](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Append Işlevi sözdizimi](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>SamAccountName özniteliği için benzersiz değerler oluşturmak üzere SelectUniqueValue özelliğini nasıl kullanabilirim?

Workday 'den *FirstName* ve *LastName* özniteliklerinin bir birleşimini kullanarak *sAMAccountName* özniteliği için benzersiz değerler oluşturmak istediğinizi varsayalım. Aşağıdakiler ile başlayabilmeniz için bir ifade aşağıda verilmiştir:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Yukarıdaki ifadenin çalışması: Kullanıcı John Smith ise, ilk olarak jsmith 'i oluşturmaya çalışır, JSmith zaten varsa, bu durumda JoSmith oluşturulur. Bu, varsa, Cansmith oluşturur. İfade Ayrıca, oluşturulan değerin, *sAMAccountName*ile ilişkili uzunluk kısıtlamasını ve özel karakter kısıtlamasını karşıladığından de emin olmanızı sağlar.

Ayrıca bkz:

* [PARÇAAL Işlevi sözdizimi](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Işlev sözdizimini Değiştir](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Işlev sözdizimi](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Nasıl yaparım? karakterleri aksanlarla kaldırıp normal Ingilizce alfaberine dönüştürür mi?

Kullanıcı için e-posta adresi veya CN değeri oluştururken, kullanıcının adı ve soyadı gibi özel karakterleri kaldırmak için [Normalizediactik](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) işlevini kullanın.

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Bu bölümde, Azure AD denetim günlükleri ve Windows Server Olay Görüntüleyicisi günlüklerini kullanarak Workday tümleştirmenizde sağlama sorunlarının nasıl giderileceği hakkında özel rehberlik sunulmaktadır. @No__t-0Öğreticisinde yakalanan genel sorun giderme adımlarının ve kavramların üzerine oluşturulur: Otomatik Kullanıcı hesabı sağlama üzerinde raporlama @ no__t-0

Bu bölüm, sorun gidermeye yönelik aşağıdaki noktaları ele almaktadır:

* [Windows Olay Görüntüleyicisi aracı sorunlarını giderme için ayarlama](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Hizmet sorunlarını giderme için Azure portal denetim günlüklerini ayarlama](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [AD Kullanıcı hesabı oluşturma işlemleri için günlükleri anlama](#understanding-logs-for-ad-user-account-create-operations)
* [Yönetici güncelleştirme işlemlerine yönelik günlükleri anlama](#understanding-logs-for-manager-update-operations)
* [Yaygın olarak karşılaşılan hatalar çözümleniyor](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Windows Olay Görüntüleyicisi aracı sorunlarını giderme için ayarlama

* Sağlama aracısının dağıtıldığı Windows Server makinesinde oturum açın
* **Windows Server Olay Görüntüleyicisi** masaüstü uygulamasını açın.
* **Windows günlükleri > uygulamasını**seçin.
* **Geçerli günlüğü filtrele...** öğesini kullanın Kaynak AAD altında günlüğe kaydedilen tüm olayları görüntüleme seçeneği **.** Aşağıda gösterildiği gibi "-5" filtresini belirterek. ProvisioningAgent 'a bağlanın ve olay kimliği "5" olan olayları dışlayın.

  ![Windows Olay Görüntüleyicisi](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* **Tamam** ' a tıklayın ve sonuç görünümünü **Tarih ve saat** sütununa göre sıralayın.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Hizmet sorunlarını giderme için Azure portal denetim günlüklerini ayarlama

* [Azure Portal](https://portal.azure.com)başlatın ve Workday sağlama uygulamanızın **Denetim günlükleri** bölümüne gidin.
* Yalnızca görünümdeki (Tarih, etkinlik, durum, durum nedeni) şu sütunları görüntülemek için denetim günlükleri sayfasındaki **sütunlar** düğmesini kullanın. Bu yapılandırma yalnızca sorun giderme için uygun olan verilere odaklanmanızı sağlar.

  ![Denetim günlüğü sütunları](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Görünümü filtrelemek için **hedef** ve **Tarih aralığı** sorgu parametrelerini kullanın. 
  * **Hedef** sorgu parametresini, Workday Worker nesnesinin "Worker ID" veya "Employee ID" olarak ayarlayın.
  * **Tarih aralığını** , sağlama ile ilgili hataları veya sorunları araştırmak istediğiniz uygun bir zaman dilimine ayarlayın.

  ![Denetim günlüğü filtreleri](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>AD Kullanıcı hesabı oluşturma işlemleri için günlükleri anlama

Workday 'de yeni bir işe algılandığında ( *21023*çalışan kimliğiyle birlikte), Azure AD sağlama hizmeti çalışan için yenı bir ad kullanıcı hesabı oluşturmaya çalışır ve işlem, aşağıda açıklandığı gibi 4 denetim günlüğü kaydı oluşturur:

  [![Denetim günlüğü oluşturma Ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Denetim günlüğü kayıtlarının herhangi birine tıkladığınızda, **etkinlik ayrıntıları** sayfası açılır. Her günlük kayıt türü için **etkinlik ayrıntıları** sayfası görüntülenir.

* **Workday Içeri aktarma** kaydı: Bu günlük kaydı, Workday 'den getirilen çalışan bilgilerini görüntüler. Workday 'den veri getirilirken oluşan sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Ad Içeri aktarma** kaydı: Bu günlük kaydı, AD 'den getirilen hesabın bilgilerini görüntüler. İlk kullanıcı oluştururken olduğu gibi, AD hesabı yoktur, *etkinlik durumu nedeni* ACTIVE DIRECTORY eşleşen kimliği özniteliği değeri olan hiçbir hesabın bulunamadığını gösterir. Workday 'den veri getirilirken oluşan sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

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

* **Eşitleme kuralı eylem** kaydı: Bu günlük kaydı, öznitelik eşleme kurallarının sonuçlarını ve yapılandırılan kapsam filtrelerini, gelen Workday olayını işlemek için gerçekleştirilecek sağlama eylemiyle birlikte görüntüler. Eşitleme eylemiyle ilgili sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Öznitelik eşleme ifadeleriniz ile ilgili sorunlar varsa veya gelen Workday verilerinde sorunlar varsa (örneğin: boş veya gerekli öznitelikler için null değer), bu aşamada hatanın ayrıntılarını sağlayan hata kodu ile bu aşamada bir hata gözlemleyeceksiniz.

* **Ad dışarı aktarma** kaydı: Bu günlük kaydı, AD hesabı oluşturma işleminin sonucunu, işlemde ayarlanan öznitelik değerleriyle birlikte görüntüler. Hesap oluşturma işlemiyle ilgili sorunları gidermek için günlük kaydının *ek ayrıntılar* bölümündeki bilgileri kullanın. Aşağıdaki örnek bir kayıt, her alanın nasıl yorumlanacağı işaretçilerle birlikte aşağıda gösterilmiştir. "Ek ayrıntılar" bölümünde "EventName" değeri "EntryExportAdd" olarak ayarlanır, "JoiningProperty" eşleşen KIMLIK özniteliğinin değerine ayarlanır, "Sourcetutturucu" kayıtla ilişkili Iş Dayıd (WıD) olarak ayarlanır ve "Targettutturucu" Şuna ayarlanır Yeni oluşturulan kullanıcının AD "Objectguıd" özniteliğinin değeri. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Bu AD dışarı aktarma işlemine karşılık gelen sağlama Aracısı günlük kayıtlarını bulmak için Windows Olay Görüntüleyicisi günlüklerini açın ve **bul...** ' u kullanın. Eşleşen KIMLIĞI/birleştirme özelliği özniteliği değerini içeren günlük girişlerini bulmak için menü seçeneği (Bu durumda *21023*).  

  *Olay kimliği = 2*ile dışarı aktarma işleminin zaman damgasına karşılık gelen BIR http post kaydı bulun. Bu kayıt sağlama aracısına sağlama hizmeti tarafından gönderilen öznitelik değerlerini içerecektir.

  [![SCıM Add](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Yukarıdaki olayın hemen ardından, AD hesabı oluşturma işleminin yanıtını yakalayan başka bir olay olması gerekir. Bu olay, AD içinde oluşturulan yeni objectGUID değerini döndürür ve sağlama hizmetinde Targettutturucu özniteliği olarak ayarlanır.

  [![SCıM Add](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Yönetici güncelleştirme işlemlerine yönelik günlükleri anlama

Yönetici özniteliği, AD 'deki bir başvuru özniteliğidir. Sağlama Hizmeti, Kullanıcı oluşturma işleminin parçası olarak Manager özniteliğini ayarladı. Bunun yerine, Kullanıcı için AD hesabı oluşturulduktan sonra, yönetici özniteliği bir *güncelleştirme* işleminin parçası olarak ayarlanır. Yukarıdaki örneği genişleterek, Workday 'de çalışan KIMLIĞI "21451" olan yeni bir işe giriş ve yeni işe alma yöneticisinin (*21023*) zaten bir ad hesabı var. Bu senaryoda, Kullanıcı 21451 için Denetim günlüklerini aramak 5 girişi gösterir.

  [![Manager güncelleştirmesi](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

İlk 4 kayıt, Kullanıcı oluşturma işleminin bir parçası olarak araştırdığımız gibidir. 5\. kayıt, yönetici öznitelik güncelleştirmesiyle ilişkili dışarı aktarma ' dır. Günlük kaydı, yöneticinin *Objectguıd* özniteliği KULLANıLARAK gerçekleştirilen ad Account Manager güncelleştirme işleminin sonucunu görüntüler.

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

|#|Hata senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Sağlama Aracısı şu hata iletisiyle yüklenirken hata oluştu:  *Service ' Microsoft Azure AD Connect sağlama Aracısı ' (AADConnectProvisioningAgent) başlatılamadı. Sistemi başlatmak için yeterli ayrıcalıklara sahip olduğunuzu doğrulayın.* | Bu hata genellikle, sağlama aracısını bir etki alanı denetleyicisine yüklemeye çalışıyorsanız ve Grup İlkesi hizmetin başlamasını engelliyorsa, genellikle görüntülenir.  Ayrıca, aracının çalıştığı önceki bir sürümüne sahipseniz ve yeni bir yükleme başlatmadan önce bu sürümü kaldırmadıysanız da görülür.| Sağlama aracısını DC olmayan bir sunucuya yükler. Yeni aracıyı yüklemeden önce aracının önceki sürümlerinin kaldırıldığından emin olun.|
|2.| ' Microsoft Azure AD Connect sağlama Aracısı ' Windows hizmeti *Başlangıç* durumunda ve *çalışan* durumuna geçmiyor. | Yüklemenin bir parçası olarak, aracı Sihirbazı sunucuda bir yerel hesap (**NT hizmeti @ no__t-1AADConnectProvisioningAgent**) oluşturur ve bu, hizmeti başlatmak Için kullanılan **oturum** açma hesabıdır. Windows sunucunuzdaki bir güvenlik ilkesi yerel hesapların Hizmetleri çalıştırmasını engelliyorsa, bu hatayla karşılaşırsınız. | *Hizmetler konsolunu*açın. ' Microsoft Azure AD sağlama aracısına Bağlan ' Windows hizmetine sağ tıklayın ve oturum aç sekmesinde, hizmeti çalıştırmak için bir etki alanı yöneticisinin hesabını belirtin. Hizmeti yeniden başlatın. |
|3.| Adım *bağlama Active Directory*, sağlama aracısını ad etki alanınız ile yapılandırırken, sihirbazın ad şemasını yüklemeye ve sonunda zaman aşımına uğramadan uzun bir süre sürer. | Bu hata genellikle, güvenlik duvarı sorunlarından dolayı sihirbaz AD etki alanı denetleyicisi sunucusuna bağlanamadığında gösterilir. | *Active Directory bağlanma* Sihirbazı EKRANıNDA, ad etki alanınız için kimlik bilgilerini sağlarken, *etki alanı denetleyicisi önceliği Seç*adlı bir seçenek vardır. Aracı sunucusuyla aynı sitede olan bir etki alanı denetleyicisi seçmek ve iletişimi engelleyen bir güvenlik duvarı kuralı olmadığından emin olmak için bu seçeneği kullanın. |

#### <a name="connectivity-errors"></a>Bağlantı hataları

Sağlama hizmeti Workday veya Active Directory 'e bağlanamıyorsa, sağlama durumunun karantinaya alınmış bir duruma geçmesine neden olabilir. Bağlantı sorunlarını gidermek için aşağıdaki tabloyu kullanın.

|#|Hata senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| **Bağlantıyı Sına**' ya tıkladığınızda şu hata iletisini alırsınız: *Active Directory bağlanırken bir hata oluştu. Lütfen şirket içi sağlama aracısının çalıştığından ve doğru Active Directory etki alanı ile yapılandırıldığından emin olun.* | Bu hata genellikle, sağlama aracısının çalışmadığı veya Azure AD ile sağlama Aracısı arasındaki iletişimi engelleyen bir güvenlik duvarı varsa görüntülenir. Etki alanı aracı sihirbazında yapılandırılmamışsa da bu hatayı görebilirsiniz. | Aracının çalıştığını onaylamak için Windows Server 'da *Hizmetler* konsolunu açın. Sağlama Aracısı sihirbazını açın ve doğru etki alanının aracıya kayıtlı olduğunu doğrulayın.  |
|2.| Sağlama işi, hafta sonları (Cum-Sat) üzerinden karantina durumuna geçer ve eşitlemede bir hata olduğunu belirten bir e-posta bildirimi alırız. | Bu hatanın yaygın nedenlerinden biri Workday'in planlı kapalı kalma süresidir. Workday uygulama kiracısını kullanıyorsanız, Workday'in uygulama kiracıları için hafta sonları zamanladığı kapalı kalma sürelerini (genellikle Cuma akşamından Cumartesi sabahına kadar) ve bu süre boyunca Workday sağlama uygulamalarının Workday'e bağlanamadığı için karantina durumuna geçebileceğini unutmayın. Workday uygulama kiracısı yeniden çevrimiçi olduğunda normal durumuna geri döner. Nadir durumlarda, kiracı yenilendiği veya hesap kilitlendiği ya da süresi dolduğu için Tümleştirme Sistemi Kullanıcısının parolası değiştirildiğinde de bu hatayı görebilirsiniz. | Workday'in kapalı kalma zamanlamasını öğrenmek için Workday yöneticinize veya iş ortağınıza danışın. Kapalı kalma süresince uyarı iletilerini yoksayın ve Workday örneği yeniden çevrimiçi olduğunda kullanılabilirliği onaylayın.  |


#### <a name="ad-user-account-creation-errors"></a>AD Kullanıcı hesabı oluşturma hataları

|#|Hata senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Denetim günlüğündeki *hatası iletisiyle dışarı aktarma işlemi hataları: OperationsError-SvcErr: İşlem hatası oluştu. Dizin hizmeti için üst başvuru yapılandırılmamış. Bu nedenle, dizin hizmeti bu ormanın dışındaki nesnelere başvuru yayınyapamıyor.* | Bu hata genellikle *Active Directory kapsayıcı* OU doğru ayarlanmamışsa veya *ParentDistinguishedName*için kullanılan ifade eşlemesinde sorunlar varsa görüntülenir. | Yazım hataları için *Active Directory kapsayıcı* OU parametresini denetleyin. Öznitelik eşlemesinde *parentDistinguishedName* kullanıyorsanız, bunun her zaman AD etki alanı içinde bilinen bir kapsayıcı olarak değerlendirildiğinden emin olun. Oluşturulan değeri görmek için denetim günlüklerinde *dışarı aktarma* olayını kontrol edin. |
|2.| Denetim günlüğündeki hata kodu ile dışarı aktarma işlemi hataları: *Systemforcrossdomainıdentitymanagementbadresponse* ve Message *Error: ConstraintViolation-AtrErr: İstekteki bir değer geçersiz. Öznitelik için bir değer, kabul edilebilir değerler aralığında değildi. \Nhata ayrıntıları: CONSTRAINT_ATT_TYPE-Company @ no__t-0. | Bu hata *Şirket* özniteliğine özgü olsa da, bu hatayı *CN* gibi diğer öznitelikler için de görebilirsiniz. Bu hata, AD Zorlanmış şema kısıtlaması nedeniyle görünür. Varsayılan olarak, *Şirket* ve *CN* gibi özniteliklerin üst sınırı 64 karakter olmalıdır. Workday 'den gelen değer 64 karakterden büyükse, bu hata iletisini görürsünüz. | Hata iletisinde bildirilen özniteliğin değerini görmek için denetim günlüklerinde *dışarı aktarma* olayını kontrol edin. [PARÇAAL](../manage-apps/functions-for-customizing-application-data.md#mid) Işlevini kullanarak Workday 'den gelen değeri kırpıp veya eşlemeleri benzer uzunluk kısıtlamalarına sahip olmayan bir ad özniteliğiyle değiştirmeyi göz önünde bulundurun.  |

#### <a name="ad-user-account-update-errors"></a>AD Kullanıcı hesabı güncelleştirme hataları

AD Kullanıcı hesabı güncelleştirme işlemi sırasında, sağlama hizmeti hem Workday hem de AD 'deki bilgileri okur, öznitelik eşleme kurallarını çalıştırır ve herhangi bir değişikliğin etkili olması gerekip gerekmediğini belirler. Bir güncelleştirme olayı da tetiklenir. Bu adımlardan herhangi biri bir hatayla karşılaştığında, denetim günlüklerinde günlüğe kaydedilir. Yaygın güncelleştirme hatalarını gidermek için aşağıdaki tabloyu kullanın.

|#|Hata senaryosu |Olası nedenler|Önerilen çözüm|
|--|---|---|---|
|1.| Denetim günlüğündeki *EventName = EntrySynchronizationError ve ErrorCode = EndpointUnavailable*iletisiyle eşitleme kuralı eylem hataları. | Bu hata, sağlama hizmeti, şirket içi sağlama Aracısı tarafından karşılaşılan bir işleme hatası nedeniyle Active Directory 'tan Kullanıcı profili verilerini alamadığında görüntülenir. | Okuma işlemiyle ilgili sorunları belirten hata olaylarını Olay Görüntüleyicisi için sağlama Aracısı 'nı denetleyin (olay KIMLIĞINE göre filtrele #2). |
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

2. Bu URL 'den Workday Human_Resources WSDL dosyasını indirin: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Workday Studio 'Yu başlatın.

4. Komut çubuğundan, **Test Web hizmeti ' ni sınayıcı seçeneğinde > Workday** ' i seçin.

5. **Dış**' i seçin ve adım 2 ' de INDIRDIĞINIZ Human_Resources WSDL dosyasını seçin.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. **Konum** alanını `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` olarak ayarlayın, ancak "ıMPL-CC" yi gerçek örnek yazınızla ve "Kiracı" öğesini gerçek kiracı adınızla değiştirin.

7. **Işlemi** **Get_Workers** olarak ayarla

8.  Workday kimlik bilgilerinizi ayarlamak için Istek/yanıt bölmelerindeki küçük **Yapılandır** bağlantısına tıklayın. **Kimlik doğrulamasını**denetleyin ve sonra Workday tümleştirme sistem hesabınız için Kullanıcı adını ve parolayı girin. Kullanıcı adını @ no__t-0tenant adı olarak biçimlendirdiğinizden emin olun ve **WS-Security UsernameToken** seçeneğini seçili bırakın.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. **Tamam**’ı seçin.

10. **İstek** bölmesinde, aşağıdaki XML 'e yapıştırın ve **Employee_ID** öğesini Workday kiracınızdaki gerçek bir kullanıcının çalışan kimliği olarak ayarlayın. Ayıklamak istediğiniz özniteliği olan bir kullanıcı seçin.

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

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Dosya ağacında **/env içinde ilerleyin: Zarf > env: Body > WD: Get_Workers_Response > WD: Response_Data > WD: Çalışan @ no__t-0, kullanıcının verilerini bulmak için.

15. @No__t-0wd altında: Worker @ no__t-0, eklemek istediğiniz özniteliği bulun ve seçin.

16. Seçili özniteeniz için XPath ifadesini **Belge yolu** alanından kopyalayın.

17. Kopyalanmış ifadeden **/env: Envelope/env: Body/WD: Get_Workers_Response/WD: Response_Data/** önekini kaldırın.

18. Kopyalanmış ifadedeki son öğe bir düğümse (örnek: "/WD: Birth_Date ") ve ardından ifadenin sonuna **/Text ()** ekleyin. Son öğe bir öznitelik ise bu gerekli değildir (örnek: "/@wd: tür").

19. Sonuç, şöyle bir şekilde `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`olmalıdır. Bu değer, Azure portal kopyalayacaksınız.

**Özel Workday Kullanıcı öznitelerinizi sağlama yapılandırmanıza eklemek için:**

1. [Azure Portal](https://portal.azure.com)başlatın ve bu öğreticide daha önce anlatıldığı gibi Workday sağlama uygulamanızın sağlama bölümüne gidin.

2. **Sağlama durumunu** **kapalı**olarak ayarlayın ve **Kaydet**' i seçin. Bu adım, değişikliklerinizin yalnızca siz hazırsanız etkili olacağını sağlamaya yardımcı olur.

3. **Eşlemeler**altında, Iş **günü çalışanlarını şirket Içi Active Directory ile eşitlemeyi** (veya **Workday çalışanlarını Azure AD 'ye eşitlemeyi**) seçin.

4. Sonraki ekranın alt kısmına ilerleyin ve **Gelişmiş seçenekleri göster**' i seçin.

5. **Workday için öznitelik listesini düzenle**' yi seçin.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Öznitelik listesinin en altına, giriş alanlarının bulunduğu yere ilerleyin.

7. **Ad**için, öznitemisiniz için bir görünen ad girin.

8. **Tür**için, özniteliğe uygun şekilde karşılık gelen türü seçin (**dize** en yaygın olarak kullanılır).

9. **API ifadesi**Için, Workday Studio 'Dan kopyaladığınız XPath ifadesini girin. Örnek: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **Öznitelik Ekle**' yi seçin.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Yukarıdaki **Kaydet** ' i ve ardından Iletişim kutusuna **Evet** ' i seçin. Hala açıksa öznitelik eşleme ekranını kapatın.

12. Ana **sağlama** sekmesine döndüğünüzde, Iş **günü çalışanlarını şirket içi Active Directory** (veya **çalışanları Azure AD ile eşitlemeyi**) yeniden eşitlemeyi seçin.

13. **Yeni eşleme Ekle**' yi seçin.

14. Yeni öznitebir öznitelik artık **kaynak öznitelik** listesinde görünmelidir.

15. Yeni özniteliğiyle istenen şekilde bir eşleme ekleyin.

16. İşiniz bittiğinde, **sağlama durumunu** **Açık** ve kayıt olarak ayarlamayı unutmayın.

### <a name="exporting-and-importing-your-configuration"></a>Yapılandırmanızı dışarı ve içeri aktarma

[Sağlama yapılandırmasını dışarı ve içeri aktarma](../manage-apps/export-import-provisioning-configuration.md) makalesine başvurun

## <a name="managing-personal-data"></a>Kişisel verileri yönetme

Active Directory için Workday sağlama çözümü, bir sağlama aracısının şirket içi Windows Server 'da yüklü olmasını gerektirir ve bu aracı Windows olay günlüğünde, Workday 'e AD özniteliğine bağlı olarak kişisel veriler içerebilen Günlükler oluşturur eşleştirmeleri. Kullanıcı gizliliği yükümlülüklerine uymak için, olay günlüğünü temizlemek üzere bir Windows zamanlanmış görevi ayarlayarak olay günlüklerinde 48 saatten daha fazla veri tutulmayacağından emin olabilirsiniz.

Azure AD sağlama hizmeti, GDPR sınıflandırmasının **veri işlemcisi** kategorisine denk gelir. Veri işlemcisi işlem hattı olarak hizmet, önemli iş ortaklarına ve son tüketicilere veri işleme hizmeti sağlar. Azure AD sağlama hizmeti, Kullanıcı verileri oluşturmaz ve kişisel verilerin toplandığı ve nasıl kullanıldığı hakkında hiç bağımsız denetime sahip değildir. Azure AD sağlama hizmeti 'nde veri alımı, toplama, analiz ve raporlama, mevcut kurumsal verileri temel alır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Azure AD sağlama hizmeti, veri bekletmeye göre rapor oluşturmaz, analiz uygulamaz veya 30 günden fazla öngörü sağlar. Bu nedenle, Azure AD sağlama hizmeti 30 günden daha fazla veri depolamaz, işlemez veya korumaz. Bu tasarım, GDPR yönetmelikleri, Microsoft gizliliği uyumluluk düzenlemeleri ve Azure AD veri saklama ilkeleriyle uyumludur.

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
* [Workday ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](workday-tutorial.md)
* [Diğer SaaS uygulamalarını Azure Active Directory ile tümleştirmeyi öğrenin](tutorial-list.md)
* [Sağlama yapılandırmalarının yönetimi için Microsoft Graph API 'Lerini nasıl kullanacağınızı öğrenin](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
