---
title: Öğretici-Azure Active Directory Domain Services için LDAPS 'yi yapılandırma | Microsoft Docs
description: Bu öğreticide, Azure Active Directory Domain Services yönetilen bir etki alanı için güvenli basit Dizin Erişimi Protokolü 'nü (LDAPS) nasıl yapılandıracağınızı öğreneceksiniz.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 5422298bf782944f10b60e98b5f251d8088f36ed
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172752"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Öğretici: Azure Active Directory Domain Services yönetilen bir etki alanı için Güvenli LDAP yapılandırma

Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanı ile iletişim kurmak için, Hafif Dizin Erişim Protokolü (LDAP) kullanılır. Varsayılan olarak, LDAP trafiği şifrelenmez, bu da birçok ortamda bir güvenlik konusudur. Azure AD DS ile, yönetilen etki alanını güvenli basit Dizin Erişim Protokolü (LDAPS) kullanacak şekilde yapılandırabilirsiniz. Güvenli LDAP kullandığınızda trafik şifrelenir. Güvenli LDAP, Güvenli Yuva Katmanı (SSL)/Aktarım Katmanı Güvenliği (TLS) üzerinden LDAP olarak da bilinir.

Bu öğreticide, Azure AD DS yönetilen bir etki alanı için LDAPS 'nin nasıl yapılandırılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD DS ile kullanmak için dijital bir sertifika oluşturun
> * Azure AD DS için Güvenli LDAP 'yi etkinleştirme
> * Genel internet üzerinden kullanılmak üzere Güvenli LDAP yapılandırma
> * Azure AD DS yönetilen bir etki alanı için Güvenli LDAP bağlama ve test etme

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Bilgisayarınızda yüklü olan *Ldp. exe* aracı.
    * Gerekirse, *Active Directory Domain Services ve LDAP*için [uzak sunucu yönetim araçları (RSAT) yüklemesini][rsat] yapın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak Azure AD DS yönetilen etki alanı için Güvenli LDAP 'yi yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="create-a-certificate-for-secure-ldap"></a>Güvenli LDAP için bir sertifika oluşturma

Güvenli LDAP kullanmak için, iletişimi şifrelemek için dijital bir sertifika kullanılır. Bu dijital sertifika Azure AD DS yönetilen etki alanına uygulanır ve *Ldp. exe* gibi araçların verileri sorgularken güvenli şifreli iletişim kullanmasına izin verir. Yönetilen etki alanına Güvenli LDAP erişimi için sertifika oluşturmanın iki yolu vardır:

* Bir genel sertifika yetkilisinden (CA) veya kuruluş CA 'dan bir sertifika.
    * Kuruluşunuz ortak bir CA 'dan sertifika alırsa, bu genel CA 'dan Güvenli LDAP sertifikası alın. Kuruluşunuzda kurumsal bir CA kullanıyorsanız, kurumsal CA 'dan Güvenli LDAP sertifikası alın.
    * Genel CA yalnızca Azure AD DS yönetilen etki alanı ile özel bir DNS adı kullandığınızda kullanılabilir. Yönetilen etki alanının DNS etki alanı adı *. onmicrosoft.com*ile biterse, bu varsayılan etki alanı ile bağlantıyı güvenli hale getirmek için dijital bir sertifika oluşturamazsınız. Microsoft, *. onmicrosoft.com* etki alanına sahipdir, bu nedenle ortak CA bir sertifika vermez. Bu senaryoda, otomatik olarak imzalanan bir sertifika oluşturun ve bunu güvenli LDAP 'yi yapılandırmak için kullanın.
* Kendi oluşturduğunuz otomatik olarak imzalanan bir sertifika.
    * Bu yaklaşım, test amaçları için uygundur ve Bu öğreticinin gösterdiği şeydir.

İsteğiniz veya oluşturduğunuz sertifikanın aşağıdaki gereksinimleri karşılaması gerekir. Güvenli LDAP 'yi geçersiz bir sertifikayla etkinleştirirseniz, yönetilen etki alanınız sorunlarla karşılaşır:

* **Güvenilir veren** -sertifika, Güvenli LDAP kullanılarak yönetilen etki alanına bağlanan bilgisayarlar tarafından güvenilen bir yetkili tarafından verilmelidir. Bu yetkili, genel bir CA veya bu bilgisayarlar tarafından güvenilen bir kuruluş CA 'sı olabilir.
* **Yaşam süresi** -sertifika en az sonraki 3-6 ay için geçerli olmalıdır. Sertifikanın süresi dolarsa, yönetilen etki alanınız için Güvenli LDAP erişimi bozulur.
* **Konu adı** -sertifikadaki Konu adı, yönetilen etki alanınız olmalıdır. Örneğin, etki alanınız *contoso.com*olarak adlandırılmışsa, sertifikanın konu adı * *. contoso.com*olmalıdır.
    * Güvenli LDAP 'nin Azure AD Domain Services ile düzgün şekilde çalıştığından emin olmak için sertifikanın DNS adı veya konu diğer adı bir joker sertifika olmalıdır. Etki alanı denetleyicileri rastgele adlar kullanır ve hizmetin kullanılabilir durumda kalmasını sağlamak için kaldırılabilir veya eklenebilir.
* **Anahtar kullanımı** -sertifika, *dijital imzalar* ve *anahtar şifrelemesi*için yapılandırılmış olmalıdır.
* **Sertifika amacı** -SERTIFIKA, SSL sunucusu kimlik doğrulaması için geçerli olmalıdır.

Bu öğreticide, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet 'ini kullanarak Güvenli LDAP için otomatik olarak imzalanan bir sertifika oluşturalım. **Yönetici** olarak bir PowerShell penceresi açın ve aşağıdaki komutları çalıştırın. *$DnsName* değişkenini, *contoso.com*gibi kendi yönetilen etkı alanınız tarafından kullanılan DNS adıyla değiştirin:

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Aşağıdaki örnek çıktı, sertifikanın başarıyla oluşturulduğunu ve yerel sertifika deposunda (*Localmachine\)* depolandığını gösterir:

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=contoso.com
```

## <a name="understand-and-export-required-certificates"></a>Gerekli sertifikaları anlama ve dışarı aktarma

Güvenli LDAP kullanmak için ağ trafiği, ortak anahtar altyapısı (PKI) kullanılarak şifrelenir.

* Azure AD DS yönetilen etki alanına **özel** bir anahtar uygulanır.
    * Bu özel anahtar, Güvenli LDAP trafiğinin *şifresini çözmek* için kullanılır. Özel anahtar yalnızca Azure AD DS tarafından yönetilen etki alanına uygulanmalıdır ve istemci bilgisayarlarına yaygın olarak dağıtılmamalıdır.
    * Özel anahtarı içeren bir sertifika ' nı kullanır *. PFX* dosya biçimi.
* İstemci bilgisayarlara **ortak** anahtar uygulanır.
    * Bu ortak anahtar, Güvenli LDAP trafiğini *şifrelemek* için kullanılır. Ortak anahtar istemci bilgisayarlara dağıtılabilir.
    * Özel anahtarı olmayan Sertifikalar ' i kullanır *. CER* dosya biçimi.

Bu iki anahtar, *özel* ve *genel* anahtarlar, yalnızca ilgili bilgisayarların birbirleriyle başarıyla iletişim kurabildiğinden emin olun. Ortak bir CA veya kuruluş CA 'sı kullanıyorsanız, özel anahtarı içeren bir sertifikayla ve Azure AD DS yönetilen bir etki alanına uygulanabilen bir sertifika verilir. Ortak anahtar, istemci bilgisayarlar tarafından zaten bilinen ve güvenilen olmalıdır. Bu öğreticide, özel anahtarla kendinden imzalı bir sertifika oluşturdunuz, bu nedenle uygun özel ve ortak bileşenleri dışarı aktarmanız gerekir.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Azure AD DS için bir sertifika dışarı aktarma

Önceki adımda oluşturulan dijital sertifikayı Azure AD DS yönetilen etki alanınız ile kullanabilmeniz için, sertifikayı bir öğesine dışarı aktarın *.* Özel anahtarı IÇEREN PFX Sertifika dosyası.

1. *Çalıştır* iletişim kutusunu açmak için **Windows** ve **R** tuşlarını seçin.
1. *Çalıştır* iletişim kutusuna **MMC** girerek Microsoft YÖNETIM konsolu 'nu (MMC) açın ve **Tamam**' ı seçin.
1. **Kullanıcı hesabı denetimi** ISTEMINDE, MMC 'yi yönetici olarak başlatmak için **Evet** ' e tıklayın.
1. **Dosya** menüsünde, **ek bileşen Ekle/Kaldır** ' a tıklayın...
1. **Sertifikalar ek bileşeni** sihirbazında **bilgisayar hesabı**' nı seçin ve ardından **İleri**' yi seçin.
1. **Bilgisayar Seç** sayfasında, **Yerel bilgisayar: (bu konsolun üzerinde çalıştığı bilgisayar)** öğesini seçin ve ardından **son**' u seçin.
1. **Ek bileşenleri Ekle veya Kaldır** iletişim kutusunda, MMC 'ye Sertifikalar ek bileşenini eklemek için **Tamam** ' a tıklayın.
1. MMC penceresinde **konsol kökü**' ni genişletin. **Sertifikalar (yerel bilgisayar)** ' ı seçin, ardından **Kişisel** düğümünü ve ardından **Sertifikalar** düğümünü genişletin.

    ![Microsoft Yönetim Konsolu 'nda kişisel sertifikalar deposunu açın](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Önceki adımda oluşturulan otomatik olarak imzalanan sertifika *contoso.com*gibi gösterilir. Bu sertifikayı sağ seçin ve ardından **dışarı aktar > tüm görevler ' i seçin...**

    ![Microsoft Yönetim Konsolu 'nda sertifikayı dışarı aktarma](./media/tutorial-configure-ldaps/export-cert.png)

1. **Sertifika dışarı aktarma sihirbazında** **İleri**' yi seçin.
1. Sertifika için özel anahtar verilmelidir. Özel anahtar, dışarıya aktarılmış sertifikaya dahil edilmediğinde, yönetilen etki alanınız için Güvenli LDAP 'yi etkinleştirme eylemi başarısız olur.

    **Özel anahtarı dışarı aktar** sayfasında **Evet, özel anahtarı dışarı aktar**' ı seçin ve ardından **İleri**' yi seçin.
1. Azure AD DS yönetilen etki alanları yalnızca ' i destekler *.* Özel anahtarı IÇEREN PFX Sertifika dosyası biçimi. Sertifikayı olarak dışarı aktarmayın *.* Özel anahtar olmadan cer sertifika dosyası biçimi.

    **Dışarı aktarma dosyası biçimi** sayfasında **Kişisel BILGI değişimi-PKCS #12 (. PFX)** , dışarıya aktarılmış sertifikanın dosya biçimi olarak. *Mümkünse sertifika yolundaki tüm sertifikaları Ekle*onay kutusunu işaretleyin:

    ![Sertifikayı PKCS 12 ' de dışarı aktarma seçeneğini belirleyin (. PFX) dosya biçimi](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Bu sertifika verilerin şifresini çözmek için kullanıldığından, erişimi dikkatle kontrol etmeniz gerekir. Sertifika kullanımını korumak için bir parola kullanılabilir. Doğru parola olmadan sertifika bir hizmete uygulanamaz.

    **Güvenlik** sayfasında, korumak için **parola** seçeneğini belirleyin *. PFX* sertifika dosyası. Bir parola girin ve onaylayın, ardından **İleri**' yi seçin. Bu parola, Azure AD DS yönetilen etki alanınız için Güvenli LDAP özelliğini etkinleştirmek üzere bir sonraki bölümde kullanılır.
1. **Dışarı aktarılacak dosya** sayfasında, sertifikayı dışarı aktarmak istediğiniz dosya adını ve konumunu belirtin, örneğin *C:\Users\accountname\azure-AD-DS.pfx*.
1. Gözden geçirme sayfasında, sertifikayı bir öğesine aktarmak için **son** ' u seçin *. PFX* sertifika dosyası. Sertifika başarıyla verildiğinde bir onay iletişim kutusu görüntülenir.
1. Aşağıdaki bölümde MMC 'YI kullanılmak üzere açık bırakın.

### <a name="export-a-certificate-for-client-computers"></a>İstemci bilgisayarlar için bir sertifika dışarı aktarma

İstemci bilgisayarların, LDAPS kullanarak yönetilen etki alanına başarıyla bağlanabilmek için Güvenli LDAP sertifikasını verene güvenmesi gerekir. İstemci bilgisayarların, Azure AD DS tarafından şifresi çözülen verileri başarıyla şifreleyebilmesi için bir sertifikaya ihtiyacı vardır. Ortak CA kullanıyorsanız, bilgisayar bu sertifika verenler için otomatik olarak güvenmeli ve karşılık gelen bir sertifikaya sahip olmalıdır. Bu öğreticide, kendinden imzalı bir sertifika kullanırsınız ve önceki adımda özel anahtarı içeren bir sertifika oluşturmuş olursunuz. Şimdi, otomatik olarak imzalanan sertifikayı dışarı aktarıp istemci bilgisayardaki güvenilir sertifika deposuna yükleyelim:

1. *Sertifikalar (yerel bilgisayar) IÇIN MMC ' > kişisel > sertifikaları* deposu ' na geri dönün. Önceki adımda oluşturulan otomatik olarak imzalanan sertifika *contoso.com*gibi gösterilir. Bu sertifikayı sağ seçin ve ardından **dışarı aktar > tüm görevler ' i seçin...**
1. **Sertifika dışarı aktarma sihirbazında** **İleri**' yi seçin.
1. İstemciler için özel anahtara ihtiyacınız olmadığı için, **özel anahtarı dışarı aktar** sayfasında **Hayır, özel anahtarı dışarı aktarma**' yı seçin ve ardından **İleri**' yi seçin.
1. **Dışarı aktarma dosyası biçimi** sayfasında **Base-64 Encoded X. 509.440 (. CER)** , şu sertifika için dosya biçimi olarak:

    ![Sertifikayı dışarı aktarma seçeneğini belirleyin-64 Encoded X. 509.440 (. CER) dosya biçimi](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. **Dışarı aktarılacak dosya** sayfasında, sertifikayı dışarı aktarmak istediğiniz dosya adını ve konumunu belirtin, örneğin *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Gözden geçirme sayfasında, sertifikayı bir öğesine aktarmak için **son** ' u seçin *. CER* sertifika dosyası. Sertifika başarıyla verildiğinde bir onay iletişim kutusu görüntülenir.

*. CER* sertifika dosyası artık Azure AD DS yönetilen etki alanı ile GÜVENLI LDAP bağlantısına güvenmesi gereken istemci bilgisayarlara dağıtılabilir. Sertifikayı yerel bilgisayara yükleyelim.

1. Dosya Gezgini 'ni açın ve kaydettiğiniz konuma gidin *.*  *C:\USERS\ACCOUNTNAME\AZURE-AD-DS-CLIENT.cer*gibi cer sertifika dosyası.
1. Sağ seçin *. CER* sertifika dosyası, ardından **sertifikayı yükler**' i seçin.
1. **Sertifika Içeri aktarma sihirbazında**, sertifikayı *Yerel makinede*depolamayı seçin ve ardından **İleri**' yi seçin:

    ![Sertifikayı yerel makine deposuna aktarma seçeneğini belirleyin](./media/tutorial-configure-ldaps/import-cer-file.png)

1. İstendiğinde, bilgisayarın değişiklik yapmasına izin vermek için **Evet** ' i seçin.
1. Sertifika **deposunu sertifika türüne göre otomatik olarak seçip**seçin ve ardından **İleri**' yi seçin.
1. İnceleme sayfasında, öğesini içeri aktarmak için **son** ' u seçin *. CER* sertifikası. sertifika başarıyla içeri aktarıldığında, dosya onay iletişim kutusu görüntülenir.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Azure AD DS için Güvenli LDAP 'yi etkinleştirme

Özel anahtarı içeren ve istemci bilgisayar bağlantıya güvenmek üzere ayarlanmış olan dijital bir sertifika oluşturulup verildiğinde, Azure AD DS yönetilen etki alanında güvenli LDAP 'yi etkinleştirin. Azure AD DS yönetilen bir etki alanında güvenli LDAP özelliğini etkinleştirmek için aşağıdaki yapılandırma adımlarını gerçekleştirin:

1. [Azure Portal](https://portal.azure.com), **kaynakları ara** kutusuna *etki alanı Hizmetleri* ' ni girin. Arama sonuçlarından **Azure AD Domain Services** seçin.

    ![Azure portal Azure AD DS yönetilen etki alanınızı arayın ve seçin](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. *Contoso.com*gibi yönetilen etki alanınızı seçin.
1. Azure AD DS penceresinin sol tarafında **Güvenli LDAP**' i seçin.
1. Varsayılan olarak, yönetilen etki alanınız için Güvenli LDAP erişimi devre dışı bırakılır. **Etkinleştirmek**için **Güvenli LDAP** değiştirin.
1. Internet üzerinden yönetilen etki alanınız Güvenli LDAP erişimi varsayılan olarak devre dışıdır. Genel Güvenli LDAP erişimini etkinleştirdiğinizde, etki alanınız Internet üzerinden parola deneme yanılma saldırılarına açıktır. Bir sonraki adımda, ağ güvenlik grubu, erişimi yalnızca gerekli kaynak IP adresi aralıklarına kilitleyecek şekilde yapılandırılmıştır.

    **Internet üzerinden GÜVENLI LDAP erişimine Izin ver** ' i **etkinleştirmek**için değiştirin.

1. Yanındaki klasör simgesini seçin **. Güvenli LDAP sertifikası olan PFX dosyası**. Yoluna gidin *. PFX* dosyası, ardından özel anahtarı içeren önceki bir adımda oluşturulan sertifikayı seçin.

    Sertifika gereksinimlerinin önceki bölümünde belirtildiği gibi, varsayılan *. onmicrosoft.com* etki alanı ile genel bir CA 'dan bir sertifika kullanamazsınız. Microsoft, *. onmicrosoft.com* etki alanına sahipdir, bu nedenle ortak CA bir sertifika vermez. Sertifikanızın uygun biçimde olduğundan emin olun. Aksi takdirde, Azure platformu, Güvenli LDAP 'yi etkinleştirdiğinizde sertifika doğrulama hataları oluşturur.

1. **Şifresini çözmek Için parolayı girin.** Sertifika bir ' a aktarıldığında önceki bir adımda ayarlanan pfx dosyası *. PFX* dosyası.
1. Güvenli LDAP özelliğini etkinleştirmek için **Kaydet** ' i seçin.

    ![Azure portal Azure AD DS yönetilen etki alanı için Güvenli LDAP 'yi etkinleştirme](./media/tutorial-configure-ldaps/enable-ldaps.png)

Yönetilen etki alanı için Güvenli LDAP 'nin yapılandırılmakta olduğu bir bildirim görüntülenir. Bu işlem tamamlanana kadar, yönetilen etki alanının diğer ayarlarını değiştiremezsiniz.

Yönetilen etki alanınız için Güvenli LDAP 'nin etkinleştirilmesi birkaç dakika sürer. Sağladığınız Güvenli LDAP sertifikası gerekli ölçütlere uymuyorsa, yönetilen etki alanı için Güvenli LDAP 'yi etkinleştirme eylemi başarısız olur. Hata için bazı yaygın nedenler, etki alanı adı yanlış veya sertifikanın süresi yakında dolar veya zaten süresi dolmuşsa olur. Sertifikayı geçerli parametrelerle yeniden oluşturabilir ve ardından bu güncelleştirilmiş sertifikayı kullanarak Güvenli LDAP 'yi etkinleştirebilirsiniz.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>İnternet üzerinden güvenli LDAP erişimini kilitleme

Azure AD DS yönetilen etki alanınızı Internet üzerinden güvenli LDAP erişimini etkinleştirdiğinizde bu, bir güvenlik tehdidi oluşturur. Yönetilen etki alanına TCP bağlantı noktası 636 ' deki internet üzerinden erişilebilir. Yönetilen etki alanına erişimi, ortamınız için belirli bilinen IP adresleriyle kısıtlamanız önerilir. Azure ağ güvenlik grubu kuralı, erişimi Güvenli LDAP ile sınırlamak için kullanılabilir.

Belirli bir IP adresi kümesinden TCP bağlantı noktası 636 üzerinden gelen güvenli LDAP erişimine izin vermek için bir kural oluşturalım. Varsayılan bir *denyall* kuralı, internet 'ten gelen diğer tüm trafik için geçerlidir. böylece, yalnızca belirtilen adresler Azure AD DS yönetilen etki ALANıNıZı Güvenli LDAP kullanarak alabilir.

1. Azure portal sol taraftaki gezinmede *kaynak grupları* ' nı seçin.
1. Kaynak grubunu, *Myresourcegroup*gibi seçin ve ardından *aaads-NSG*gibi ağ güvenlik grubunuzu seçin.
1. Mevcut gelen ve giden güvenlik kurallarının listesi görüntülenir. Ağ güvenlik grubu pencerelerinin sol tarafında **güvenlik > gelen güvenlik kuralları**' nı seçin.
1. **Ekle**' yi seçin ve *TCP* bağlantı noktası *636*' e izin vermek için bir kural oluşturun Gelişmiş güvenlik için, kaynağı *IP adresleri* olarak seçin ve ardından KURULUŞUNUZUN geçerli IP adresini veya aralığını belirtin.

    | Ayar                           | Değer        |
    |-----------------------------------|--------------|
    | Kaynak                            | IP Adresleri |
    | Kaynak IP adresleri/CıDR aralıkları | Ortamınız için geçerli bir IP adresi veya aralığı |
    | Kaynak bağlantı noktası aralıkları                | *            |
    | Hedef                       | Herhangi biri          |
    | Hedef bağlantı noktası aralıkları           | 636          |
    | Protokol                          | TCP          |
    | Eylem                            | Allow        |
    | Öncelik                          | 401          |
    | Adı                              | AllowLDAPS   |

1. Hazırsanız, kuralı kaydetmek ve uygulamak için **Ekle** ' yi seçin.

    ![Internet üzerinden LDAPS erişimini güvenli hale getirmek için bir ağ güvenlik grubu kuralı oluşturma](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Dış erişim için DNS bölgesini yapılandırma

Internet üzerinden güvenli LDAP erişimi etkinken, istemci bilgisayarlarının bu yönetilen etki alanını bulabilmesi için DNS bölgesini güncelleştirin. *Güvenli LDAP dış IP adresi* , Azure AD DS yönetilen etki alanınız için **Özellikler** sekmesinde listelenir:

![Azure portal Azure AD DS yönetilen etki alanınız için Güvenli LDAP dış IP adresini görüntüleyin](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Dış DNS sağlayıcınızı, bu dış IP adresine çözümlemek üzere *LDAPS*gibi bir konak kaydı oluşturacak şekilde yapılandırın. Öncelikle makinenizde yerel olarak test etmek için Windows Konakları dosyasında bir giriş oluşturabilirsiniz. Yerel makinenizde konaklar dosyasını başarıyla düzenlemek için *Not defteri 'ni* yönetici olarak açın, sonra dosyayı açın *C:\Windows\System32\drivers\etc*

Aşağıdaki örnek DNS girişi, dış DNS sağlayıcınız veya yerel konaklar dosyasında, *LDAPS.contoso.com* trafiğini *40.121.19.239*dış IP adresine çözümler:

```
40.121.19.239    ldaps.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Yönetilen etki alanına sorguları test etme

Azure AD DS yönetilen etki alanınızı bağlamak ve bağlanmak ve LDAP üzerinden aramak için *Ldp. exe* ' yi de kullanabilirsiniz. Bu araç Uzak Sunucu Yönetim Araçları (RSAT) paketine dahildir. Daha fazla bilgi için bkz. [ınstall uzak sunucu yönetim araçları][rsat].

1. *Ldp. exe* ' yi açın ve yönetilen etki alanına bağlanın. **Bağlantı**' yı ve ardından **Bağlan...** seçeneğini belirleyin.
1. Önceki adımda oluşturulan, *LDAPS.contoso.com*gibi yönetilen etki ALANıNıN GÜVENLI LDAP DNS etki alanı adını girin. Güvenli LDAP kullanmak için, **bağlantı noktasını** *636*olarak ayarlayın ve ardından **SSL**kutusunu işaretleyin.
1. Yönetilen etki alanına bağlanmak için **Tamam ' ı** seçin.

Ardından, Azure AD DS yönetilen etki alanınızı bağlayın. Azure AD DS örneğiniz üzerinde NTLM parola karması eşitlemesini devre dışı bırakmış kullanıcılar (ve hizmet hesapları) LDAP basit bağlamalar gerçekleştiremez. NTLM parola karma eşitlemesini devre dışı bırakma hakkında daha fazla bilgi için bkz. [Azure AD DS yönetilen etki alanınızı güvenceye][secure-domain]alma.

1. **Bağlantı** menüsü seçeneğini belirleyin ve ardından **bağla...** seçeneğini belirleyin.
1. *Contosoadmin*gıbı *AAD DC Administrators* grubuna ait olan bir kullanıcı hesabının kimlik bilgilerini sağlayın. Kullanıcı hesabının parolasını girin ve etki alanınızı girin, örneğin *contoso.com*.
1. **Bağlama türü**için, *kimlik bilgileriyle bağlama*seçeneğini belirleyin.
1. Azure AD DS yönetilen etki alanınızı bağlamak için **Tamam ' ı** seçin.

Azure AD DS yönetilen etki alanında depolanan nesneleri görmek için:

1. **Görünüm** menü seçeneğini belirleyin ve ardından **ağaç**' ı seçin.
1. *BaseDN* alanını boş bırakın ve **Tamam**' ı seçin.
1. *Aaddc kullanıcıları*gibi bir kapsayıcı seçin, kapsayıcıyı sağ seçip **Ara**' yı seçin.
1. Önceden doldurulmuş alanları ayarlanmış bırakın ve **Çalıştır**' ı seçin. Sorgunun sonuçları sağ taraftaki pencerede gösterilir.

    ![LDP. exe kullanarak Azure AD DS yönetilen etki alanındaki nesneleri arama](./media/tutorial-configure-ldaps/ldp-query.png)

Belirli bir kapsayıcıyı doğrudan sorgulamak için, **görünüm > ağaç** menüsünden *OU = Aaddc kullanıcıları, DC = contoso, DC = com* veya *OU = AADDC Computers, DC = contoso, DC = com*gibi bir **BaseDN** belirtebilirsiniz. Sorguları biçimlendirme ve oluşturma hakkında daha fazla bilgi için bkz. [LDAP sorgusu temelleri][ldap-query-basics].

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin bağlantısını test etmek için bilgisayarınızın yerel Hosts dosyasına bir DNS girişi eklediyseniz, bu girişi kaldırın ve DNS bölgenize bir resmi kayıt ekleyin. Girişi yerel hosts dosyasından kaldırmak için aşağıdaki adımları izleyin:

1. Yerel makinenizde, yönetici olarak *Not defteri* 'ni açın
1. *C:\Windows\System32\drivers\etc* dosyasına gidin ve dosyayı açın
1. Eklediğiniz kaydın satırını silin, örneğin `40.121.19.239    ldaps.contoso.com`

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure AD DS ile kullanmak için dijital bir sertifika oluşturun
> * Azure AD DS için Güvenli LDAP 'yi etkinleştirme
> * Genel internet üzerinden kullanılmak üzere Güvenli LDAP yapılandırma
> * Azure AD DS yönetilen bir etki alanı için Güvenli LDAP bağlama ve test etme

> [!div class="nextstepaction"]
> [Karma Azure AD ortamı için Parola karması eşitlemesini yapılandırma](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
