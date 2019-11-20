---
title: Ağ Ilkesi sunucu uzantısı 'nı kullanarak VPN 'yi Azure MFA ile tümleştirme-Azure Active Directory
description: Microsoft Azure için ağ Ilkesi sunucusu uzantısını kullanarak VPN altyapınızı Azure MFA ile tümleştirin.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf841738296f0d23bec5d68a0ad1ca0401facfb
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812395"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Azure için ağ Ilkesi sunucusu uzantısını kullanarak VPN altyapınızı Azure MFA ile tümleştirin

Azure için ağ Ilkesi sunucusu (NPS) uzantısı, kuruluşların bulut tabanlı [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md)kullanarak uzaktan kimlik doğrulaması Içeri arama Kullanıcı HIZMETI (RADIUS) istemci kimlik doğrulamasını, iki adımlı bir şekilde korumanıza olanak sağlar. doğrulamayı.

Bu makalede, Azure için NPS uzantısını kullanarak, NPS altyapısını MFA ile tümleştirme yönergeleri sunulmaktadır. Bu işlem, ağınıza VPN kullanarak bağlanmaya çalışan kullanıcılar için güvenli iki aşamalı doğrulamayı sağlar.

Ağ Ilkesi ve erişim Hizmetleri, kuruluşlara şu olanakları sağlar:

* Şunları belirtmek için ağ isteklerinin yönetimi ve denetimi için merkezi bir konum atayın:

  * Kimler bağlanabilir

  * Gün bağlantılarına izin verilen süreler

  * Bağlantı süresi

  * İstemcilerin bağlanmak için kullanması gereken güvenlik düzeyi

    Her VPN veya Uzak Masaüstü Ağ Geçidi sunucusunda ilke belirtmek yerine, merkezi bir konumda olduktan sonra bunu yapın. RADIUS protokolü, merkezi kimlik doğrulaması, yetkilendirme ve hesap oluşturma (AAA) sağlamak için kullanılır.

* Kurmak ve cihazları Kısıtlanmamış veya kısıtlanmış ağ kaynaklarına erişim izni olup olmadığını belirleyen Ağ Erişim Koruması (NAP) istemci sistem durumu ilkeleri uygular.

* 802.1 x özellikli kablosuz erişim noktalarına ve Ethernet anahtarlarına erişim için kimlik doğrulama ve yetkilendirmeyi zorlamak için bir yol sağlar.
  Daha fazla bilgi için bkz. [ağ Ilkesi sunucusu](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Kuruluşlar, güvenliği artırmak ve yüksek düzeyde uyumluluk sağlamak için, kullanıcıların VPN sunucusundaki sanal bağlantı noktasına bağlanmak üzere iki aşamalı doğrulama kullanmasını sağlamak üzere NPS 'YI Azure Multi-Factor Authentication ile tümleştirilebilir. Kullanıcılara erişim izni verilmesi için, bunların Kullanıcı adı ve parola birleşimini ve denetdukları diğer bilgileri sağlaması gerekir. Bu bilgiler güvenilir olmalı ve kolayca çoğaltılmamalıdır. Bir cep telefonu numarası, bir yer çizgisi numarası ya da bir mobil cihazda uygulama içerebilir.

Azure için NPS uzantısının kullanılabilirliğine başlamadan önce, tümleşik NPS ve MFA ortamları için iki aşamalı doğrulama uygulamak isteyen müşteriler, şirket içi bir ortamda ayrı bir MFA sunucusu yapılandırmak ve sürdürmek zorunda kalmıştı. Bu tür bir kimlik doğrulaması, RADIUS kullanılarak Uzak Masaüstü Ağ Geçidi ve Azure Multi-Factor Authentication sunucusu tarafından sunulur.

Azure için NPS Uzantısı ile kuruluşlar, şirket içi tabanlı MFA çözümünü ya da bulut tabanlı MFA çözümünü dağıtarak RADIUS istemci kimlik doğrulamasını güvenli hale getirbilirler.

## <a name="authentication-flow"></a>Kimlik doğrulama akışı

Kullanıcılar VPN sunucusundaki bir sanal bağlantı noktasına bağlandıklarında, önce çeşitli protokoller kullanarak kimlik doğrulaması yapılmalıdır. Protokoller, Kullanıcı adı ve parola ve sertifika tabanlı kimlik doğrulama yöntemlerinin bir birleşiminin kullanılmasına izin verir.

Kimlik doğrulama ve kimlik doğrulama özelliklerine ek olarak, kullanıcıların uygun arama izinleri olması gerekir. Basit uygulamalarda, erişime izin veren arama izinleri doğrudan Active Directory Kullanıcı nesneleri üzerinde ayarlanır.

![Active Directory Kullanıcıları ve bilgisayarları Kullanıcı özelliklerindeki arama sekmesi](./media/howto-mfa-nps-extension-vpn/image1.png)

Basit uygulamalarda her VPN sunucusu, her yerel VPN sunucusunda tanımlı ilkelere göre erişim verir veya reddeder.

Daha büyük ve daha ölçeklenebilir uygulamalarda, VPN erişimi veren veya reddeden ilkeler RADIUS sunucularında merkezi hale getirilmiş olur. Bu durumlarda, VPN sunucusu bağlantı isteklerini ve hesap iletilerini bir RADIUS sunucusuna ileten bir erişim sunucusu (RADIUS istemcisi) görevi görür. VPN sunucusundaki sanal bağlantı noktasına bağlanmak için, kullanıcıların kimliğinin doğrulanması ve RADIUS sunucularında merkezi olarak tanımlanan koşulları karşılaması gerekir.

Azure için NPS Uzantısı NPS ile tümleştirildiğinde, başarılı bir kimlik doğrulama akışı sonuçları aşağıdaki gibi olur:

1. VPN sunucusu, Uzak Masaüstü oturumu gibi bir kaynağa bağlanmak için Kullanıcı adını ve parolayı içeren bir VPN kullanıcısının kimlik doğrulama isteğini alır.
2. Bir RADIUS istemcisi görevi gören VPN sunucusu, isteği bir RADIUS *erişim-istek* iletisine dönüştürür ve NPS UZANTıSıNıN yüklendiği RADIUS sunucusuna (şifrelenmiş bir parolayla) gönderir.
3. Kullanıcı adı ve parola birleşimi Active Directory doğrulanır. Kullanıcı adı veya parola yanlış ise, RADIUS sunucusu bir *Erişim reddi* iletisi gönderir.
4. NPS bağlantı Isteği ve ağ Ilkelerinde belirtilen tüm koşullar karşılanıyorsa (örneğin, günün saati veya grup üyeliği kısıtlamaları), NPS uzantısı, Azure Multi-Factor Authentication ile ikincil kimlik doğrulama isteğini tetikler.
5. Azure Multi-Factor Authentication Azure Active Directory ile iletişim kurar, kullanıcının ayrıntılarını alır ve Kullanıcı tarafından yapılandırılan yöntemi (cep telefonu araması, SMS mesajı veya mobil uygulama) kullanarak ikincil kimlik doğrulamasını gerçekleştirir.
6. MFA sınaması başarılı olduğunda Azure Multi-Factor Authentication, sonucu NPS Uzantısı ile iletişim kurar.
7. Bağlantı girişiminden hem kimlik doğrulaması hem de yetkilendirildikten sonra, uzantının yüklendiği NPS, VPN sunucusuna (RADIUS istemcisi) bir RADIUS *erişim-kabul* iletisi gönderir.
8. Kullanıcıya VPN sunucusundaki sanal bağlantı noktasına erişim verilir ve şifreli bir VPN tüneli oluşturur.

## <a name="prerequisites"></a>Önkoşullar

Bu bölümde, MFA 'yı VPN ile tümleştirebilmeniz için tamamlanması gereken önkoşulların ayrıntıları yer almalıdır. Başlamadan önce, aşağıdaki önkoşulların yerine gelmelidir:

* VPN altyapısı
* Ağ Ilkesi ve erişim Hizmetleri rolü
* Azure Multi-Factor Authentication lisansı
* Windows Server yazılımı
* Kitaplıklar
* Azure Active Directory (Azure AD) Şirket içi Active Directory eşitlendi
* Azure Active Directory GUID kimliği

### <a name="vpn-infrastructure"></a>VPN altyapısı

Bu makalede, Microsoft Windows Server 2016 kullanan ve VPN sunucunuzun bağlantı isteklerini bir RADIUS sunucusuna iletecek şekilde yapılandırılmadığı varsayılmaktadır. Makalesinde, VPN altyapısını merkezi bir RADIUS sunucusu kullanacak şekilde yapılandırırsınız.

Çalışır durumda çalışan bir VPN altyapınız yoksa, Microsoft ve üçüncü taraf sitelerinde bulabileceğiniz çok sayıda VPN Kurulumu öğreticilerinde bulunan kılavuzu izleyerek hızlı bir şekilde bir tane oluşturabilirsiniz.

### <a name="the-network-policy-and-access-services-role"></a>Ağ Ilkesi ve erişim Hizmetleri rolü

Ağ Ilkesi ve erişim Hizmetleri, RADIUS sunucusu ve istemci işlevselliği sağlar. Bu makalede, ortamınızdaki bir üye sunucusuna veya etki alanı denetleyicisine ağ Ilkesi ve erişim Hizmetleri rolü yüklediğinizi varsayılmaktadır. Bu kılavuzda, bir VPN yapılandırması için RADIUS yapılandırırsınız. Ağ Ilkesi ve erişim Hizmetleri rolünü VPN sunucunuz dışında bir sunucuya yükler.

Ağ Ilkesi ve erişim Hizmetleri rol hizmeti Windows Server 2012 veya sonraki bir sürümü yükleme hakkında bilgi için bkz. [NAP sistem durumu Ilkesi sunucusu yükleme](https://technet.microsoft.com/library/dd296890.aspx). Windows Server 2016 ' de NAP kullanımdan kaldırılmıştır. NPS 'nin bir etki alanı denetleyicisine yüklenmesi önerisi dahil olmak üzere en iyi NPS yöntemlerinin açıklaması için bkz. [NPS Için en iyi uygulamalar](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA lisans

Azure Multi-Factor Authentication için bir lisans gerekir ve bir Azure AD Premium, Enterprise Mobility + Security veya Multi-Factor Authentication bağımsız lisansı aracılığıyla kullanılabilir. Azure MFA için Kullanıcı başına veya kimlik doğrulama lisansı başına tüketim tabanlı lisanslar, NPS uzantısıyla uyumlu değildir. Daha fazla bilgi için [Azure multi-Factor Authentication'ı alma](concept-mfa-licensing.md). Sınama amacıyla bir deneme aboneliğini kullanabilirsiniz.

### <a name="windows-server-software"></a>Windows Server yazılımı

NPS uzantısı, ağ Ilkesi ve erişim Hizmetleri rolü yüklüyken Windows Server 2008 R2 SP1 veya sonraki bir sürümü gerektirir. Bu kılavuzdaki tüm adımlar Windows Server 2016 ile gerçekleştirildi.

### <a name="libraries"></a>Kitaplıklar

Aşağıdaki kitaplıklar NPS Uzantısı ile otomatik olarak yüklenir:

-   [Visual Studio 2013 C++ için görsel yeniden dağıtılabilir paketler (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Windows PowerShell için Microsoft Azure Active Directory Modülü Version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Microsoft Azure Active Directory PowerShell modülü zaten mevcut değilse, kurulum sürecinin bir parçası olarak çalıştırdığınız bir yapılandırma betiği ile yüklenir. Henüz yüklü değilse modülün bir süre önce yüklenmesi gerekmez.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory Şirket içi Active Directory eşitlendi

NPS uzantısını kullanmak için şirket içi kullanıcıların Azure Active Directory ile eşitlenmesi ve MFA için etkinleştirilmesi gerekir. Bu kılavuzda, şirket içi kullanıcıların Azure AD Connect aracılığıyla Azure Active Directory eşitlendiği varsayılmaktadır. MFA için kullanıcıları etkinleştirme talimatları aşağıda verilmiştir.

Azure AD Connect hakkında bilgi için bkz. Şirket [içi dizinlerinizi Azure Active Directory Ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID kimliği

NPS uzantısını yüklemek için Azure Active Directory GUID 'sini bilmeniz gerekir. Azure Active Directory GUID 'sini bulmaya yönelik yönergeler sonraki bölümde verilmiştir.

## <a name="configure-radius-for-vpn-connections"></a>VPN bağlantıları için RADIUS yapılandırma

NPS rolünü bir üye sunucuya yüklediyseniz, VPN bağlantısı isteyen VPN istemcisinin kimliğini doğrulamak ve yetkilendirmek için yapılandırmanız gerekir. 

Bu bölüm, ağ Ilkesi ve erişim Hizmetleri rolünü yüklediğinizi ancak altyapınızda kullanılmak üzere yapılandırdığınızı varsayar.

> [!NOTE]
> Kimlik doğrulaması için merkezi bir RADIUS sunucusu kullanan çalışan bir VPN sunucunuz zaten varsa, bu bölümü atlayabilirsiniz.
>

### <a name="register-server-in-active-directory"></a>Sunucusu Active Directory'de Kaydettir

Bu senaryoda düzgün çalışması için, NPS sunucusunun Active Directory kayıtlı olması gerekir.

1. Sunucu Yöneticisi açın.

2. Sunucu Yöneticisi ' de **Araçlar**' ı seçin ve ardından **ağ ilkesi sunucusu**' nu seçin.

3. Ağ Ilkesi sunucu konsolunda **NPS (yerel)** öğesine sağ tıklayın ve ardından **sunucuyu Active Directory kaydet**' i seçin. **Tamam ' ı** iki kez seçin.

    ![Sunucuyu Active Directory menü seçeneğinde Kaydet](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Konsolunu sonraki yordam için açık bırakın.

### <a name="use-wizard-to-configure-the-radius-server"></a>RADIUS sunucusunu yapılandırmak için Sihirbazı kullanma

RADIUS sunucusunu yapılandırmak için standart (sihirbaz tabanlı) veya gelişmiş yapılandırma seçeneğini kullanabilirsiniz. Bu bölüm, sihirbaz tabanlı standart yapılandırma seçeneğini kullandığınızı varsayar.

1. Ağ Ilkesi sunucu konsolunda **NPS (yerel)** öğesini seçin.

2. **Standart yapılandırma**altında, **Çevirmeli veya VPN bağlantıları için RADIUS sunucusu**' nu seçin ve ardından **VPN veya çevirmeli bağlantı Yapılandır**' ı seçin.

    ![RADIUS sunucusunu çevirmeli veya VPN bağlantıları için yapılandırma](./media/howto-mfa-nps-extension-vpn/image3.png)

3. **Çevirmeli veya sanal özel ağ bağlantısı türü seç** penceresinde, **sanal özel ağ bağlantıları**' nı seçin ve ardından **İleri**' yi seçin.

    ![Sanal özel ağ bağlantılarını yapılandırma](./media/howto-mfa-nps-extension-vpn/image4.png)

4. **Çevirmeli veya VPN sunucusu belirtin** penceresinde **Ekle**' yi seçin.

5. **Yenı RADIUS istemcisi** penceresinde, kolay bir ad SAĞLAYıN, VPN sunucusunun çözümlenebilir adını veya IP adresini girin ve ardından paylaşılan gizli dizi parolasını girin. Paylaşılan gizli dizi parolasını uzun ve karmaşık yapın. Bir sonraki bölümde ihtiyacınız olacak şekilde kaydedin.

    ![Yeni bir RADIUS istemci penceresi oluştur](./media/howto-mfa-nps-extension-vpn/image5.png)

6. **Tamam**' ı ve ardından **İleri**' yi seçin.

7. **Kimlik doğrulama yöntemlerini Yapılandır** penceresinde, varsayılan seçimi (**Microsoft şifreli kimlik doğrulama sürüm 2 [MS-CHAPv2])** kabul edin veya başka bir seçenek belirleyin ve **İleri**' yi seçin.

    > [!NOTE]
    > Genişletilebilir Kimlik Doğrulama Protokolü (EAP) yapılandırırsanız, Microsoft Karşılıklı kimlik doğrulama protokolü (CHAPv2) veya Korumalı Genişletilebilir Kimlik Doğrulama Protokolü (PEAP) kullanmanız gerekir. Başka EAP desteklenmez.

8. **Kullanıcı gruplarını belirtin** penceresinde **Ekle**' yi seçin ve ardından uygun bir grup seçin. Grup yoksa, tüm kullanıcılara erişim vermek için seçimi boş bırakın.

    ![Erişime izin vermek veya erişimi reddetmek için Kullanıcı Grupları penceresini belirtin](./media/howto-mfa-nps-extension-vpn/image7.png)

9. **İleri**’yi seçin.

10. **IP filtrelerini belirtin** penceresinde, **İleri**' yi seçin.

11. **Şifreleme ayarlarını belirtin** penceresinde, varsayılan ayarları kabul edin ve ardından **İleri**' yi seçin.

    ![Şifreleme ayarlarını belirtin penceresi](./media/howto-mfa-nps-extension-vpn/image8.png)

12. **Bölge adı belirtin** penceresinde, bölge adını boş bırakın, varsayılan ayarı kabul edin ve ardından **İleri**' yi seçin.

    ![Bölge adı belirt penceresi](./media/howto-mfa-nps-extension-vpn/image9.png)

13. **Yeni Çevirmeli veya sanal özel ağ bağlantıları ve RADIUS Istemcileri Tamamlanıyor** penceresinde **son**' u seçin.

    ![Yapılandırma penceresi tamamlandı](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>RADIUS yapılandırmasını doğrulama

Bu bölüm, Sihirbazı kullanarak oluşturduğunuz yapılandırmayı ayrıntılı olarak ayrıntılardır.

1. Ağ Ilkesi sunucusunda, NPS (yerel) konsolunda, **RADIUS istemcileri**' ni genişletin ve ardından **RADIUS istemcileri**' ni seçin.

2. Ayrıntılar bölmesinde, oluşturduğunuz RADIUS istemcisine sağ tıklayın ve ardından **Özellikler**' i seçin. RADIUS istemcinizin özellikleri (VPN sunucusu) burada gösterilenler gibi olmalıdır:

    ![VPN özelliklerini ve yapılandırmasını doğrulama](./media/howto-mfa-nps-extension-vpn/image11.png)

3. **İptal**' i seçin.

4. Ağ Ilkesi sunucusunda, NPS (yerel) konsolunda, **ilkeler**' i genişletin ve **bağlantı isteği ilkeleri**' ni seçin. VPN bağlantıları ilkesi, aşağıdaki görüntüde gösterildiği gibi görüntülenir:

    ![VPN bağlantı ilkesini gösteren bağlantı isteği ilkesi](./media/howto-mfa-nps-extension-vpn/image12.png)

5. **İlkeler**altında **ağ ilkeleri**' ni seçin. Aşağıdaki görüntüde gösterilen ilkeye benzer bir sanal özel ağ (VPN) bağlantı ilkesi görmeniz gerekir:

    ![Sanal özel ağ bağlantıları ilkesini gösteren ağ Ilkeleri](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>VPN sunucunuzu RADIUS kimlik doğrulaması kullanacak şekilde yapılandırma

Bu bölümde, VPN sunucunuzu RADIUS kimlik doğrulaması kullanacak şekilde yapılandırırsınız. Yönergeler, VPN sunucusu üzerinde çalışan bir yapılandırmaya sahip olduğunu ancak RADIUS kimlik doğrulaması kullanacak şekilde yapılandırmadığınızı varsayar. VPN sunucusunu yapılandırdıktan sonra, yapılandırmanızın beklendiği gibi çalıştığını doğrulayın.

> [!NOTE]
> RADIUS kimlik doğrulaması kullanan çalışır durumda bir VPN sunucu yapılandırmanız varsa, bu bölümü atlayabilirsiniz.
>

### <a name="configure-authentication-provider"></a>Kimlik doğrulama sağlayıcısını Yapılandır

1. VPN sunucusunda Sunucu Yöneticisi açın.

2. Sunucu Yöneticisi **Araçlar**' ı seçin ve ardından **Yönlendirme ve uzaktan erişim**' i seçin.

3. **Yönlendirme ve uzaktan erişim** penceresinde,  **\<sunucu adı > (yerel)** ' e sağ tıklayın ve ardından **Özellikler**' i seçin.

4. **Sunucu adı > (yerel) Özellikler penceresinde Güvenlik sekmesini seçin. \<**

5. **Güvenlik** sekmesinde, **kimlik doğrulama sağlayıcısı**altında, **RADIUS kimlik doğrulaması**' nı seçin ve ardından **Yapılandır**' ı seçin.

    ![RADIUS kimlik doğrulama sağlayıcısını yapılandırma](./media/howto-mfa-nps-extension-vpn/image15.png)

6. **RADIUS kimlik doğrulaması** penceresinde **Ekle**' yi seçin.

7. **RADIUS sunucusu Ekle** penceresinde şunları yapın:

    a. **Sunucu adı** kutusuna, önceki bölümde yapılandırdığınız RADIUS sunucusunun adını veya IP adresini girin.

    b. **Paylaşılan gizlilik**Için, **Değiştir**' i seçin ve daha önce oluşturduğunuz ve kaydettiğiniz paylaşılan gizli parolayı girin.

    c. **Zaman aşımı (saniye)** kutusuna **30**değerini girin.  
    İkinci kimlik doğrulama faktörünü tamamlamaya yetecek sürenin tamamlanmasına izin vermek için zaman aşımı değeri gereklidir.

    ![Zaman aşımını yapılandıran RADIUS sunucusu penceresi ekleme](./media/howto-mfa-nps-extension-vpn/image16.png)

8. **Tamam**’ı seçin.

### <a name="test-vpn-connectivity"></a>VPN bağlantısını sına

Bu bölümde VPN istemcisinin, VPN Sanal bağlantı noktasına bağlanmaya çalıştığınızda RADIUS sunucusu tarafından doğrulandığını ve yetkilendirileceğini onaylamanız gerekir. Yönergeler, Windows 10 ' un bir VPN istemcisi olarak kullandığınızı varsayar.

> [!NOTE]
> VPN sunucusuna bağlanmak için bir VPN istemcisini zaten yapılandırdıysanız ve ayarları kaydettiyseniz, bir VPN bağlantısı nesnesini yapılandırma ve kaydetme ile ilgili adımları atlayabilirsiniz.
>

1. VPN istemci bilgisayarınızda, **Başlat** düğmesini seçin ve ardından **Ayarlar** düğmesini seçin.

2. **Windows ayarları** penceresinde **Ağ & Internet**' i seçin.

3. **VPN**' yi seçin.

4. **VPN bağlantısı ekle**' yi seçin.

5. **VPN bağlantısı ekle** penceresinde, **VPN sağlayıcısı** kutusunda **Windows (yerleşik)** ' i seçin, kalan alanları uygun şekilde doldurun ve ardından **Kaydet**' i seçin.

    !["VPN bağlantısı ekleme" penceresi](./media/howto-mfa-nps-extension-vpn/image17.png)

6. **Denetim Masası**' na gidin ve ardından **Ağ ve Paylaşım Merkezi**' ni seçin.

7. **Bağdaştırıcı ayarlarını değiştir**' i seçin.

    ![Ağ ve Paylaşım Merkezi-bağdaştırıcı ayarlarını değiştir](./media/howto-mfa-nps-extension-vpn/image18.png)

8. VPN ağ bağlantısına sağ tıklayın ve ardından **Özellikler**' i seçin.

9. VPN özellikleri penceresinde **güvenlik** sekmesini seçin.

10. **Güvenlik** sekmesinde, yalnızca **Microsoft CHAP sürüm 2 ' nin (MS-CHAP v2)** seçili olduğundan emin olun ve ardından **Tamam**' ı seçin.

    !["Bu protokollere Izin ver" seçeneği](./media/howto-mfa-nps-extension-vpn/image20.png)

11. VPN bağlantısına sağ tıklayın ve sonra **Bağlan**' ı seçin.

12. **Ayarlar** penceresinde **Bağlan**' ı seçin.  
    RADIUS sunucusundaki güvenlik günlüğünde, burada gösterildiği gibi, olay KIMLIĞI 6272 olarak başarılı bir bağlantı görüntülenir:

    ![Başarılı bir bağlantıyı gösteren olay Özellikler penceresi](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS sorunlarını giderme

VPN sunucusunu kimlik doğrulama ve yetkilendirme için merkezi bir RADIUS sunucusu kullanacak şekilde yapılandırmadan önce VPN yapılandırmanızın çalıştığını varsayın. Yapılandırma çalışıyorsa, sorun büyük olasılıkla RADIUS sunucusunun yanlış yapılandırılması veya geçersiz Kullanıcı adı ya da parola kullanımı nedeniyle oluşur. Örneğin, Kullanıcı adında alternatif UPN sonekini kullanırsanız, oturum açma girişimi başarısız olabilir. En iyi sonuçlar için aynı hesap adını kullanın.

Bu sorunları gidermek için, başlamak için ideal bir yer, RADIUS sunucusundaki güvenlik olay günlüklerini incelemektir. Olaylara yönelik zaman aramayı kaydetmek için, burada gösterildiği gibi rol tabanlı ağ Ilkesi ve erişim sunucusu özel görünümünü Olay Görüntüleyicisi kullanabilirsiniz. "Olay KIMLIĞI 6273", NPS 'nin bir kullanıcıya erişimi reddedildiği olayları gösterir.

![NPAS olaylarını gösteren Olay Görüntüleyicisi](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Çok faktörlü kimlik doğrulamasını yapılandırma

Multi-Factor Authentication için kullanıcıları yapılandırma konusunda yardım için, [bulut tabanlı Azure Multi-Factor Authentication dağıtımını planlama](howto-mfa-getstarted.md#create-conditional-access-policy) ve [hesabımı iki adımlı doğrulama için ayarlama](../user-help/multi-factor-authentication-end-user-first-time.md) makalelerine bakın

## <a name="install-and-configure-the-nps-extension"></a>NPS uzantısını yükleyip yapılandırma

Bu bölüm VPN sunucusu ile istemci kimlik doğrulaması için MFA 'yı kullanmak üzere VPN yapılandırmaya ilişkin yönergeler sağlar.

NPS uzantısını yükleyip yapılandırdıktan sonra, MFA kullanmak için bu sunucu tarafından işlenen tüm RADIUS tabanlı istemci kimlik doğrulaması gerekir. Tüm VPN kullanıcılarınız Azure Multi-Factor Authentication 'a kaydolmadıysa, aşağıdakilerden birini yapabilirsiniz:

* MFA 'yı kullanacak şekilde yapılandırılmamış kullanıcıların kimliğini doğrulamak için başka bir RADIUS sunucusu ayarlayın.

* Azure Multi-Factor Authentication 'a kaydolduklarında, öncelikli kullanıcıların ikinci bir kimlik doğrulama faktörü sağlamasına izin veren bir kayıt defteri girişi oluşturun.

_HKLM\SOFTWARE\Microsoft\AzureMfa Içinde REQUIRE_USER_MATCH_adlı yeni bir dize değeri oluşturun ve değeri *true* veya *false*olarak ayarlayın.

!["Kullanıcı eşleşmesi ıste" ayarı](./media/howto-mfa-nps-extension-vpn/image34.png)

Değer *true* olarak ayarlandıysa veya boşsa, tüm kimlik doğrulama istekleri MFA sınamasına tabidir. Değer *false*olarak ayarlandıysa, MFA zorlukları yalnızca Azure Multi-Factor Authentication 'a kaydolmuş kullanıcılar için verilir. Bir ekleme dönemi sırasında yalnızca test veya üretim ortamlarında *yanlış* ayarını kullanın.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Azure Active Directory GUID KIMLIĞINI edinin

NPS uzantısının yapılandırmasının bir parçası olarak, yönetici kimlik bilgilerini ve Azure AD kiracınızın KIMLIĞINI sağlamanız gerekir. Aşağıdakileri yaparak KIMLIĞI edinin:

1. Oturum [Azure portalında](https://portal.azure.com) Azure kiracısının genel Yöneticisi olarak.

2. Sol bölmede **Azure Active Directory** düğmesini seçin.

3. Seçin **özellikleri**.

4. Azure AD KIMLIĞINIZI kopyalamak için **Kopyala** düğmesini seçin.

    ![Azure portal Azure AD dizin KIMLIĞI](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>NPS uzantısını yükleme

NPS uzantısının, ağ Ilkesi ve erişim Hizmetleri rolünün yüklü olduğu ve tasarımınızda RADIUS sunucusu olarak işlev gören bir sunucuda yüklü olması gerekir. NPS uzantısını VPN sunucunuza yüklemeyin.

1. NPS uzantısını [Microsoft Indirme merkezi](https://aka.ms/npsmfa)' nden indirin.

2. Kurulum yürütülebilir dosyasını (*NpsExtnForAzureMfaInstaller. exe*) NPS sunucusuna kopyalayın.

3. NPS sunucusunda **NpsExtnForAzureMfaInstaller. exe** ' ye çift tıklayın ve Istenirse, **Çalıştır**' ı seçin.

4. **Azure MFA Için NPS uzantısı kurulum** penceresinde, yazılım lisans koşullarını gözden geçirin, **Lisans hüküm ve koşullarını kabul** ediyorum onay kutusunu işaretleyin ve ardından **yükleme**' yi seçin.

    !["Azure MFA kurulumu için NPS uzantısı" penceresi](./media/howto-mfa-nps-extension-vpn/image36.png)

5. **Azure MFA kurulumu Için NPS uzantısı** penceresinde **Kapat**' ı seçin.  

    !["Kurulum başarılı" onay penceresi](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>NPS Uzantısı ile kullanılacak sertifikaları bir PowerShell betiği kullanarak yapılandırma

Güvenli iletişim ve güvence sağlamak için sertifikaları NPS uzantısı tarafından kullanılmak üzere yapılandırın. NPS bileşenleri otomatik olarak imzalanan bir sertifika kullanmak için NPS ile yapılandıran bir Windows PowerShell Betiği içerir.

Komut aşağıdaki eylemleri gerçekleştirir:

* Kendinden imzalı bir sertifika oluşturur.
* Sertifikanın ortak anahtarını Azure AD 'deki hizmet sorumlusu ile ilişkilendirir.
* Sertifikayı yerel makine deposunda depolar.
* Ağ kullanıcısına sertifikanın özel anahtarına erişim izni verir.
* NPS hizmetini yeniden başlatır.

Kendi sertifikalarınızı kullanmak istiyorsanız, sertifikanızın ortak anahtarını Azure AD 'de hizmet sorumlusu ile ilişkilendirmeniz ve bu şekilde devam etmeniz gerekir.

Betiği kullanmak için, uzantıyı Azure Active Directory yönetici kimlik bilgilerinizle ve daha önce kopyaladığınız Azure Active Directory kiracı KIMLIĞIYLE birlikte sağlayın. Betiği, NPS uzantısını yüklediğiniz her NPS sunucusunda çalıştırın.

1. Windows PowerShell 'i yönetici olarak çalıştırın.

2. PowerShell komut isteminde **CD "C:\Program Files\Microsoft\AzureMfa\Config"** yazın ve ardından ENTER ' u seçin.

3. Sonraki komut isteminde **.\AzureMfaNpsExtnConfigSetup.ps1**girin ve ardından ENTER ' u seçin. Betik, Azure AD PowerShell modülünün yüklü olup olmadığını denetler. Yüklü değilse, betik modülü sizin için yüklenir.

    ![AzureMfsNpsExtnConfigSetup. ps1 yapılandırma betiği çalıştırılıyor](./media/howto-mfa-nps-extension-vpn/image38.png)

    Betik, PowerShell modülünün yüklemesini doğruladıktan sonra, PowerShell modülü oturum açma penceresini Azure Active Directory görüntüler.

4. Azure AD yönetici kimlik bilgilerinizi ve parolanızı girip **oturum aç**' ı seçin.

    ![Azure AD PowerShell 'de kimlik doğrulama](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Komut isteminde, daha önce kopyaladığınız kiracı KIMLIĞINI yapıştırın ve ENTER ' u seçin.

    ![Azure AD dizin KIMLIĞINI daha önce kopyalanmış şekilde girin](./media/howto-mfa-nps-extension-vpn/image40.png)

    Betik, otomatik olarak imzalanan bir sertifika oluşturur ve başka yapılandırma değişiklikleri gerçekleştirir. Çıktı aşağıdaki görüntüde olduğu gibidir:

    ![Otomatik olarak imzalanan sertifikayı gösteren PowerShell penceresi](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Sunucuyu yeniden başlatın.

### <a name="verify-the-configuration"></a>yapılandırıldığını doğrulayın

Yapılandırmayı doğrulamak için VPN sunucusuyla yeni bir VPN bağlantısı kurmanız gerekir. Birincil kimlik doğrulaması için kimlik bilgilerinizi başarıyla girdikten sonra, VPN bağlantısı aşağıda gösterildiği gibi, bağlantı kurumadan önce ikincil kimlik doğrulamasının başarılı olmasını bekler.

![Windows ayarları VPN penceresi](./media/howto-mfa-nps-extension-vpn/image42.png)

Azure MFA 'da daha önce yapılandırdığınız ikincil doğrulama yöntemiyle kimlik doğrulamasını başarıyla yaptıysanız kaynağa bağlanırsınız. Ancak, ikincil kimlik doğrulaması başarısız olursa kaynağa erişiminizi reddettiniz.

Aşağıdaki örnekte, bir Windows Phone Microsoft Authenticator uygulama ikincil kimlik doğrulamasını sağlar:

![Windows Phone için örnek MFA istemi](./media/howto-mfa-nps-extension-vpn/image43.png)

İkincil yöntemi kullanarak kimlik doğrulamasından geçtikten sonra, VPN sunucusundaki sanal bağlantı noktasına erişim vermiş olursunuz. Güvenilen bir cihazda bir mobil uygulama kullanarak ikincil bir kimlik doğrulama yöntemi kullanmanız gerektiğinden, oturum açma işlemi yalnızca bir Kullanıcı adı ve parola birleşimi kullandığından daha güvenlidir.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Başarılı oturum açma olaylarının Olay Görüntüleyicisi günlüklerini görüntüleme

Windows Olay Görüntüleyicisi günlüklerinde başarılı oturum açma olaylarını görüntülemek için aşağıdaki PowerShell komutunu girerek NPS sunucusundaki Windows Güvenlik günlüğünü sorgulayın:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell Güvenlik Olay Görüntüleyicisi](./media/howto-mfa-nps-extension-vpn/image44.png)

Ayrıca, burada gösterildiği gibi güvenlik günlüğünü veya ağ Ilkesi ve erişim Hizmetleri özel görünümünü de görebilirsiniz:

![Örnek ağ Ilkesi sunucusu günlüğü](./media/howto-mfa-nps-extension-vpn/image45.png)

Azure Multi-Factor Authentication için NPS uzantısını yüklediğiniz sunucuda, *uygulama ve hizmetler Logs\Microsoft\AzureMfa*' de uzantıya özgü Olay Görüntüleyicisi uygulama günlüklerini bulabilirsiniz.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Olay Görüntüleyicisi AuthZ logs bölmesi örneği](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

Yapılandırma beklendiği gibi çalışmıyorsa, kullanıcının MFA kullanacak şekilde yapılandırıldığını doğrulayarak sorun gidermeye başlayın. Bağlanma kullanıcının [Azure portalında](https://portal.azure.com). Kullanıcıdan ikincil kimlik doğrulaması istenirse ve kimlik doğrulaması başarılı olursa, bir sorun olarak MFA 'nın yanlış yapılandırmasını ortadan kaldırabilirsiniz.

MFA Kullanıcı için çalışıyorsa ilgili Olay Görüntüleyicisi günlüklerini gözden geçirin. Günlükler, önceki bölümde ele alınan güvenlik olayı, ağ geçidi işlem ve Azure Multi-Factor Authentication günlüklerini içerir.

Başarısız oturum açma olayını (olay KIMLIĞI 6273) görüntüleyen bir güvenlik günlüğü örneği burada gösterilmektedir:

![Başarısız oturum açma olayını gösteren güvenlik günlüğü](./media/howto-mfa-nps-extension-vpn/image47.png)

Azure Multi-Factor Authentication günlüğünden ilgili bir olay burada gösterilmektedir:

![Azure Multi-Factor Authentication günlükleri](./media/howto-mfa-nps-extension-vpn/image48.png)

Gelişmiş sorun giderme yapmak için, NPS hizmetinin yüklü olduğu NPS veritabanı biçim günlük dosyalarına başvurun. Günlük dosyaları, _%systemroot%\System32\Logs_ klasöründe virgülle ayrılmış metin dosyaları olarak oluşturulur. Günlük dosyalarının açıklaması için bkz. [NPS veritabanı biçimi günlük dosyalarını yorumlama](https://technet.microsoft.com/library/cc771748.aspx).

Bunları bir elektronik tabloya veya veritabanına aktarmadığınız takdirde, bu günlük dosyalarındaki girişlerin yorumlanması zordur. Günlük dosyalarını yorumlarken size yardımcı olmak için birçok Internet kimlik doğrulama hizmeti (IAS) ayrıştırma aracını çevrimiçi bulabilirsiniz. İndirilebilir bir [paylaşılan yazılım uygulamasının](https://www.deepsoftware.com/iasviewer) çıktısı burada gösterilmektedir:

![Örnek paylaşılan yazılım uygulaması IAS ayrıştırıcısı](./media/howto-mfa-nps-extension-vpn/image49.png)

Ek sorun giderme işlemleri yapmak için Wireshark veya [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)gibi bir protokol çözümleyici kullanabilirsiniz. Wireshark ' deki aşağıdaki resimde VPN sunucusu ile NPS arasındaki RADIUS iletileri gösterilmektedir.

![Filtrelenmiş trafiği gösteren Microsoft Ileti Çözümleyicisi](./media/howto-mfa-nps-extension-vpn/image50.png)

Daha fazla bilgi için bkz. [mevcut NPS altyapınızı Azure Multi-Factor Authentication Ile tümleştirme](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Multi-Factor Authentication 'ı edinme](concept-mfa-licensing.md)

[RADIUS kullanan Uzak Masaüstü Ağ Geçidi ve Azure Multi-Factor Authentication Sunucusu](howto-mfaserver-nps-rdg.md)

[Şirket içi dizinlerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md)
