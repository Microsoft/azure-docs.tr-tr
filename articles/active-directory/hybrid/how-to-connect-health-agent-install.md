---
title: Azure AD Connect Health Aracısı yüklemesi | Microsoft Belgeleri
description: Bu Azure AD Connect Health sayfasında, AD FS ve Eşitleme için aracı yükleme işlemi açıklanmıştır.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68249fc9a599ab49e8d5fd231fa63e91a6e3a21f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330099"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health Aracısı Yüklemesi

Bu belge, Azure AD Connect Health Aracılarını yüklemenize ve yapılandırmanıza yardımcı olur. Aracıları [buradan](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent) indirebilirsiniz.

## <a name="requirements"></a>Gereksinimler

Aşağıdaki tabloda Azure AD Connect Health kullanımına ilişkin gereksinimler listelenmiştir.

| Gereksinim | Açıklama |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health, bir Azure AD Premium özelliği olup Azure AD Premium gerektirir. <br /><br />Daha fazla bilgi için bkz. [Azure AD Premium ile Çalışmaya Başlama](../fundamentals/active-directory-get-started-premium.md) <br />30 günlük ücretsiz denemeyi başlatmak için bkz. [Denemeyi başlatma.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Azure AD Connect Health ile çalışmaya başlamak için Azure AD'nizin genel yöneticisi olmanız gerekir. |Varsayılan olarak yalnızca genel yöneticiler; çalışmaya başlamak üzere durum aracılarını yükleyip yapılandırabilir, portala erişebilir ve Azure AD Connect Health'te işlem gerçekleştirebilir. Daha fazla bilgi için bkz. [Azure AD dizininizi yönetme](../fundamentals/active-directory-administer.md). <br /><br /> Rol Tabanlı Erişim Denetimini kullanarak kuruluşunuzdaki diğer kullanıcılara Azure AD Connect Health erişim izni verebilirsiniz. Daha fazla bilgi için bkz. [Azure AD Connect Health için Rol Tabanlı Erişim Denetimi.](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) <br /><br />**Önemli:** Aracıları yüklerken kullanılan hesap bir iş veya okul hesabı olmalıdır. Bir Microsoft hesabı olamaz. Daha fazla bilgi için bkz. [Azure'a kuruluş olarak kaydolma](../fundamentals/sign-up-organization.md) |
| Azure AD Connect Health Aracısı, hedeflenen tüm sunucularda yüklüdür | Azure AD Connect Health, veri almak ve İzleme ve Analiz özelliklerini sağlamak için hedeflenen sunucularda Sistem Durumu Aracılarının yüklü ve yapılandırılmış olmasını gerektirir. <br /><br />Örneğin, AD FS altyapınızdan veri alabilmek için AD FS sunucularında ve Web Uygulaması Proxy sunucularında aracının yüklü olması gerekir. Benzer şekilde, şirket içi AD DS altyapınızdaki verileri almak için aracının etki alanı denetleyicilerine yüklenmesi gerekir. <br /><br /> |
| Azure hizmet uç noktalarına giden bağlantı | Yükleme ve çalışma zamanı sırasında, aracı ile Azure AD Connect Health hizmet uç noktaları arasında bağlantı kurulması gerekir. Giden bağlantı Güvenlik Duvarları kullanılarak engellenirse aşağıdaki uç noktaların izin verilenler listesine eklendiğinden emin olun. Bkz. [giden bağlantı uç noktaları](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|IP Adreslerini temel alan giden bağlantı | Güvenlik duvarlarında IP adresine göre filtreleme için bkz. [Azure IP Aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).|
| Giden trafik için SSL İncelemesi filtrelenmiş ya da devre dışı | Ağ katmanında giden trafik için SSL incelemesi veya sonlandırması mevcutsa aracı kaydı adımı veya veri yükleme işlemleri başarısız olabilir. [SSL denetimi kurulumu](https://technet.microsoft.com/library/ee796230.aspx) hakkında daha fazla bilgi edinin |
| Aracıyı çalıştıran sunucudaki güvenlik duvarı bağlantı noktaları |Aracının Azure AD Health hizmet uç noktaları ile iletişim kurabilmesi için aşağıdaki güvenlik duvarı bağlantı noktalarının açık olması gerekir.<br /><br /><li>TCP bağlantı noktası 443</li><li>TCP bağlantı noktası 5671</li> <br />Bağlantı noktası 5671 ' nin, aracının en son sürümü için artık gerekli olmadığını unutmayın. Yalnızca 443 numaralı bağlantı noktası gerekli olacak şekilde en son sürüme yükseltin. [güvenlik duvarı bağlantı noktalarını etkinleştirme](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) hakkında daha fazla bilgi edinin |
| IE Artırılmış Güvenlik etkinse aşağıdaki web sitelerine izin verin |IE Artırılmış Güvenlik etkinse aracının yükleneceği sunucuda aşağıdaki web sitelerine izin verilmesi gerekir.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \//aadcdn. msftauth. net</li><li>Kuruluşunuz için Azure Active Directory tarafından güvenilen federasyon sunucusu. Örneğin: https:\//sts.contoso.com</li> [IE 'yi yapılandırma](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)hakkında daha fazla bilgi edinin. Ağınız içindeki bir proxy 'niz varsa lütfen aşağıdaki nota bakın.|
| PowerShell v4.0 veya üzerinin yüklü olduğundan emin olun | <li>Windows Server 2008 R2, aracı için yeterli olmayan PowerShell v2.0 sürümüne sahiptir. PowerShell'i [Windows Server 2008 R2 Sunucularında aracı yüklemesi](#agent-installation-on-windows-server-2008-r2-servers) belgesine göre güncelleştirin.</li><li>Windows Server 2012, aracı için yeterli olmayan PowerShell v3.0 sürümüne sahiptir.  Windows Management Framework'ü [güncelleştirin](https://www.microsoft.com/download/details.aspx?id=40855).</li><li>Windows Server 2012 R2 ve üzeri ile gelen PowerShell sürümü yeterli olacaktır.</li>|
|FIPS’yi devre dışı bırakma|FIPS, Azure AD Connect Health aracıları tarafından desteklenmez.|


> [!NOTE]
> Yüksek oranda kilitli ve son derece kısıtlı ortamınız varsa, yukarıda verilen IE artırılmış güvenlik yapılandırmasında listelenenlere ek olarak aşağıdaki hizmet uç noktası listelerinde belirtilen URL 'Leri beyaz listeye almanız gerekir. 
>

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure hizmet uç noktalarına giden bağlantı

 Yükleme ve çalışma zamanı sırasında, aracı ile Azure AD Connect Health hizmet uç noktaları arasında bağlantı kurulması gerekir. Giden bağlantı güvenlik duvarları kullanılarak engellenirse, aşağıdaki URL 'Lerin varsayılan olarak engellenmediğinden emin olun. Bu URL 'Lerin güvenlik izlemesini veya denetimini devre dışı bırakmayın, ancak diğer internet trafiğinden yaptığınız gibi bunlara izin verin. Azure AD Connect Health hizmet uç noktaları ile iletişime izin verir. [Test-AzureADConnectHealthConnectivity ile giden bağlantıyı nasıl denetleyeceğinizi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#test-connectivity-to-azure-ad-connect-health-service)öğrenin.

| Etki Alanı Ortamı | Gerekli Azure hizmet uç noktaları |
| --- | --- |
| Genel Kullanıma Açık | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - Bağlantı Noktası: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *bu uç nokta yalnızca kayıt sırasında keşif amacıyla kullanılır.</li> |
| Azure Almanya | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *bu uç nokta yalnızca kayıt sırasında keşif amacıyla kullanılır.</li> |
| Azure Devlet Kurumları | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *bu uç nokta yalnızca kayıt sırasında keşif amacıyla kullanılır.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Azure AD Connect Health Aracısını indirme ve yükleme

* Azure AD Connect Health [gereksinimlerini yerine getirdiğinizden](how-to-connect-health-agent-install.md#requirements) emin olun.
* AD FS için Azure AD Connect Health kullanmaya başlama
    * [AD FS için Azure AD Connect Health Aracısını indirin.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Yükleme talimatlarına bakın](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Eşitleme için Azure AD Connect Health kullanmaya başlama
    * [Azure AD Connect'in en son sürümünü indirip yükleyin](https://go.microsoft.com/fwlink/?linkid=615771). Eşitleme için Durum Aracısı, Azure AD Connect yüklemesinin bir parçası olarak yüklenir (sürüm 1.0.9125.0 veya daha yeni bir sürüm).
* AD DS için Azure AD Connect Health kullanmaya başlama
    * [AD DS için Azure AD Connect Health Aracısını indirin](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Yükleme talimatlarına bakın](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>AD FS için Azure AD Connect Health Aracısını yükleme

> [!NOTE]
> AD FS sunucusunun Eşitleme sunucunuzdan farklı olması gerekir. AD FS aracısını Eşitleme sunucunuza yüklemeyin.
>

Yükleme öncesinde AD FS sunucunuzun ana bilgisayar adının benzersiz olduğundan ve AD FS hizmetinde bulunmadığından emin olun.
Aracı yüklemesini başlatmak için indirdiğiniz .exe dosyasına çift tıklayın. İlk ekranda Yükle'ye tıklayın.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install1.png)

Yükleme tamamlandıktan sonra Şimdi Yapılandır'a tıklayın.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install2.png)

Bu durum, aracı kaydı işlemini başlatmak üzere bir PowerShell penceresi açar. İstendiğinde, aracı kaydını gerçekleştirme erişimi olan bir Azure AD hesabıyla oturum açın. Varsayılan olarak, Genel Yönetici hesabı erişime sahiptir.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install3.png)

Oturum açtıktan sonra PowerShell işleme devam eder. İşlem tamamlandıktan sonra PowerShell'i kapatabilirsiniz ve yapılandırma tamamlanmış olur.

Bu noktada, aracı hizmetleri, aracının gerekli verileri bulut hizmetine güvenli bir şekilde yüklemesine izin vermek üzere otomatik olarak başlatılmalıdır.

Önceki bölümlerde belirtilen önkoşulların tümünü yerine getirmediyseniz PowerShell penceresinde uyarılar görüntülenir. Aracıyı yüklemeden önce [buradaki](how-to-connect-health-agent-install.md#requirements) gereksinimleri karşıladığınızdan emin olun. Bu hataların bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install4.png)

Aracının yüklü olduğunu doğrulamak için sunucuda aşağıdaki hizmetleri arayın. Yapılandırmayı tamamladıysanız bu hizmetlerin çalışır durumda olması gerekir. Aksi halde bu hizmetler yapılandırma tamamlanıncaya kadar durdurulur.

* Azure AD Connect Health AD FS Tanılama Hizmeti
* Azure AD Connect Health AD FS Öngörü Hizmeti
* Azure AD Connect Health AD FS İzleme Hizmeti

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Windows Server 2008 R2 Sunucularında aracı yüklemesi

Windows Server 2008 R2 sunucuları için adımlar:

1. Sunucunun Hizmet Paketi 1 veya daha yüksek bir sürümde çalıştığından emin olun.
2. Aracı yüklemesi için IE ESC'yi devre dışı bırakın:
3. AD Health aracısını yüklemeden önce her bir sunucuya Windows PowerShell 4.0 sürümünü yükleyin. Windows PowerShell 4.0 sürümünü yüklemek için:
   * Çevrimdışı yükleyiciyi indirmek için aşağıdaki bağlantıyı kullanarak [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) sürümünü yükleyin.
   * PowerShell ISE'yi yükleme (Windows Özelliklerinden)
   * [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) sürümünü yükleyin.
   * Sunucuda Internet Explorer 10 veya sonraki bir sürümünü yükleyin. (Sistem Sağlığı Hizmeti tarafından Azure Yönetici kimlik bilgileriniz kullanılarak kimliğinizin doğrulanması için gereklidir.)
4. Windows Server 2008 R2'ye Windows PowerShell 4.0 sürümünü yüklemek üzere daha fazla bilgi almak için [buradaki](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx) wiki makalesine bakın.

### <a name="enable-auditing-for-ad-fs"></a>AD FS için Denetimi Etkinleştirme

> [!NOTE]
> Bu bölüm yalnızca AD FS sunucuları için geçerlidir. Web Uygulaması Ara Sunucularında bu adımları izlemeniz gerekmez.
>

Kullanım Analizi özelliğinin verileri toplaması ve analiz edebilmesi için, Azure AD Connect Health aracılarının AD FS Denetim Günlüklerindeki bilgilere ihtiyacı vardır. Bu günlükler varsayılan olarak etkin değildir. AD FS denetimini etkinleştirmek ve AD FS denetim günlüklerini bulmak için AD FS sunucularınızda aşağıdaki yordamları kullanın.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Windows Server 2008 R2'de AD FS için denetimi etkinleştirme

1. **Başlat** menüsüne tıklayın, **Programlar** ve daha sonra **Yönetim Araçları**'nın üzerine gidin ve ardından **Yerel Güvenlik İlkesi**'ne tıklayın.
2. **Güvenlik Ayarları\Yerel İlkeler\Kullanıcı Hakları Ataması** klasörüne gidin ve **Güvenlik denetimleri oluştur**'a çift tıklayın.
3. **Yerel Güvenlik Ayarları** sekmesinde AD FS 2.0 hizmet hesabının listelenmiş olduğunu doğrulayın. Listelenmediyse **Kullanıcı veya Grup Ekle**'ye tıklayın, hizmet hesabını listeye ekleyin ve ardından **Tamam**'a tıklayın.
4. Denetimi etkinleştirmek için yükseltilmiş ayrıcalıklara sahip bir Komut İstemi açın ve şu komutu çalıştırın: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. **Yerel Güvenlik İlkesi**’ni kapatın.
<br />   -- **Aşağıdaki adımlar, yalnızca birincil AD FS sunucuları için gereklidir.** -- <br />
6. **AD FS Yönetim** ek bileşenini açın. AD FS Yönetim ek bileşenini açmak için **Başlat**'a tıklayın, **Programlar** ve **Yönetim Araçları**'nın üzerine gidin ve ardından **AD FS 2.0 Yönetimi**'ne tıklayın.
7. **Eylemler** bölmesinde **Federasyon Hizmeti Özelliklerini Düzenle**'ye tıklayın.
8. **Federation Service Properties (Federasyon Hizmeti Özellikleri)** iletişim kutusunda **Events (Olaylar)** sekmesine tıklayın.
9. **Success audits (Başarı denetimleri)** ve **Failure audits (Hata denetimleri)** onay kutularını seçin.
10. **Tamam**’a tıklayın.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2'de AD FS için denetimi etkinleştirme

1. Başlat ekranından **Sunucu Yöneticisi**'ni açarak **Yerel Güvenlik İlkesi**'ni veya masaüstünde bulunan görev çubuğundan Sunucu Yöneticisi'ni açıp **Araçlar/Yerel Güvenlik İlkesi**'ne tıklayın.
2. **Güvenlik Ayarları\Yerel İlkeler\Kullanıcı Hakları Ataması** klasörüne gidin ve **Güvenlik denetimleri oluştur**'a çift tıklayın.
3. **Yerel Güvenlik Ayarları** sekmesinde AD FS hizmet hesabının listelenmiş olduğunu doğrulayın. Listelenmediyse **Kullanıcı veya Grup Ekle**'ye tıklayın, hizmet hesabını listeye ekleyin ve ardından **Tamam**'a tıklayın.
4. Denetimi etkinleştirmek için yükseltilmiş ayrıcalıklara sahip bir komut istemi açın ve şu komutu çalıştırın: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```
5. **Yerel Güvenlik İlkesi**’ni kapatın.
<br />   -- **Aşağıdaki adımlar, yalnızca birincil AD FS sunucuları için gereklidir.** -- <br />
6. **AD FS Yönetimi** ek bileşenini açın. (Sunucu Yöneticisi'nde Araçlar'a tıklayın ve AD FS Yönetimi'ni seçin).
7. **Eylemler** bölmesinde **Federasyon Hizmeti Özelliklerini Düzenle**'ye tıklayın.
8. **Federation Service Properties (Federasyon Hizmeti Özellikleri)** iletişim kutusunda **Events (Olaylar)** sekmesine tıklayın.
9. **(Success audits and Failure audits) Başarı denetimleri ve Hata denetimleri** onay kutularını seçin ve **OK (Tamam)** düğmesine tıklayın.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016'da AD FS için denetimi etkinleştirme

1. Başlat ekranından **Sunucu Yöneticisi**'ni açarak **Yerel Güvenlik İlkesi**'ni veya masaüstünde bulunan görev çubuğundan Sunucu Yöneticisi'ni açıp **Araçlar/Yerel Güvenlik İlkesi**'ne tıklayın.
2. **Güvenlik Ayarları\Yerel İlkeler\Kullanıcı Hakları Ataması** klasörüne gidin ve **Güvenlik denetimleri oluştur**'a çift tıklayın.
3. **Yerel Güvenlik Ayarları** sekmesinde AD FS hizmet hesabının listelenmiş olduğunu doğrulayın. Mevcut değilse, **Kullanıcı veya Grup Ekle**'ye tıklayın, AD FS hizmet hesabını listeye ekleyin ve ardından **Tamam**'a tıklayın.
4. Denetimi etkinleştirmek için yükseltilmiş ayrıcalıklara sahip bir komut istemi açın ve şu komutu çalıştırın: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. **Yerel Güvenlik İlkesi**’ni kapatın.
<br />   -- **Aşağıdaki adımlar, yalnızca birincil AD FS sunucuları için gereklidir.** -- <br />
6. **AD FS Yönetimi** ek bileşenini açın. (Sunucu Yöneticisi'nde Araçlar'a tıklayın ve AD FS Yönetimi'ni seçin).
7. **Eylemler** bölmesinde **Federasyon Hizmeti Özelliklerini Düzenle**'ye tıklayın.
8. **Federation Service Properties (Federasyon Hizmeti Özellikleri)** iletişim kutusunda **Events (Olaylar)** sekmesine tıklayın.
9. **(Success audits and Failure audits) Başarı denetimleri ve Hata denetimleri** onay kutularını seçin ve **OK (Tamam)** düğmesine tıklayın. Bu seçenek varsayılan olarak etkindir.
10. Bir PowerShell penceresi açın ve şu komutu çalıştırın: ```Set-AdfsProperties -AuditLevel Verbose```.

"Temel" denetim düzeyi varsayılan olarak etkindir. [Windows Server 2016’da AD FS Denetimini geliştirme](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server) hakkında daha fazla bilgi edinin


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS denetim günlüklerini bulma

1. **Olay Görüntüleyicisi**'ni açın.
2. Windows Günlüklerine gidin ve **Security (Güvenlik)** seçeneğini belirleyin.
3. Sağ tarafta bulunan **Filter Current Logs (Geçerli Günlükleri Filtrele)** seçeneğine tıklayın.
4. Event Source (Olay Kaynağı) altındaki **AD FS Auditing (AD FS Denetimi)** seçeneğini belirleyin.

    Ve Denetim günlükleri için hızlı [SSS notu](reference-connect-health-faq.md#operations-questions).

![AD FS denetim günlükleri](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> AD FS denetimi bir grup ilkesiyle devre dışı bırakılabilir. AD FS denetimi devre dışı bırakılırsa, oturum açma etkinlikleriyle ilgili kullanım analizi mevcut olmaz. AD FS denetimini devre dışı bırakan bir grup ilkenizin olmadığından emin olun. >
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Eşitleme için Azure AD Connect Health aracısını yükleme

Eşitleme için Azure AD Connect Health aracısı, Azure AD Connect'in en son derlemesinde otomatik olarak yüklenir. Azure AD Connect'i eşitleme için kullanmak üzere, Azure AD Connect'in en son sürümünü indirip yüklemeniz gerekir. En son sürümü [buradan](https://www.microsoft.com/download/details.aspx?id=47594) indirebilirsiniz.

Aracının yüklü olduğunu doğrulamak için sunucuda aşağıdaki hizmetleri arayın. Yapılandırmayı tamamladıysanız bu hizmetlerin çalışır durumda olması gerekir. Aksi halde bu hizmetler yapılandırma tamamlanıncaya kadar durdurulur.

* Azure AD Connect Health Eşitleme Öngörü Hizmeti
* Azure AD Connect Health Eşitleme İzleme Hizmeti

![Eşitleme için Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health'in kullanılabilmesi için Azure AD Premium'un gerekli olduğunu unutmayın. Azure AD Premium'unuz yoksa Azure portalında yapılandırmayı tamamlayamazsınız. Daha fazla bilgi için [gereksinim sayfasına](how-to-connect-health-agent-install.md#requirements) bakın.
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>El ile Eşitleme için Azure AD Connect Health kaydı

Azure AD Connect'i başarıyla yükledikten sonra Eşitleme aracısı kaydı için Azure AD Connect Health başarısız olursa aracıyı el ile kaydetmek için aşağıdaki PowerShell komutunu kullanabilirsiniz.

> [!IMPORTANT]
> Bu PowerShell komutunun, yalnızca Azure AD Connect yüklendikten sonra aracı kaydının başarısız olması halinde kullanılması gerekir.
>
>

Aşağıdaki PowerShell komutu, YALNIZCA Azure AD Connect'in başarılı bir şekilde yüklenip yapılandırılmasına rağmen durum aracısı kaydının başarısız olması halinde gereklidir. Aracı başarılı bir şekilde kaydolduktan sonra Azure AD Connect Health hizmetleri başlatılır.

Eşitleme için Azure AD Connect Health aracısını, aşağıdaki PowerShell komutunu kullanarak el ile kaydedebilirsiniz:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Komut için şu parametreler kullanılır:

* AttributeFiltering: Azure AD Connect varsayılan özniteliği eşitlemiyor ve filtrelenmiş bir öznitelik kümesini kullanmak üzere özelleştirildiyse $true (varsayılan). Aksi halde $false değerini alır.
* StagingMode: Azure AD Connect sunucusu, hazırlama modunda DEĞİLSE $false (varsayılan) değerini; sunucu hazırlama moduna yapılandırıldıysa $true değerini alır.

Kimliğinizi doğrulamanız istendiğinde, Azure AD Connect'in yapılandırılması için kullanılan genel yönetici hesabını (örneğin, admin@domain.onmicrosoft.com) kullanmanız gerekir.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>AD DS için Azure AD Connect Health Aracısını yükleme

Aracı yüklemesini başlatmak için indirdiğiniz .exe dosyasına çift tıklayın. İlk ekranda Yükle'ye tıklayın.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Yükleme tamamlandıktan sonra Şimdi Yapılandır'a tıklayın.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Bir komut istemi başlatılır ve ardından bazı PowerShell uygulamaları tarafından Register-AzureADConnectHealthADDSAgent komutu yürütülür. Azure’da oturum açmanız istendiğinde devam edin ve oturum açın.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Oturum açtıktan sonra PowerShell işleme devam eder. İşlem tamamlandıktan sonra PowerShell'i kapatabilirsiniz ve yapılandırma tamamlanmış olur.

Bu noktada, hizmetler otomatik olarak başlatılarak verilerin aracı tarafından izlenip toplanmasına izin verilir. Önceki bölümlerde belirtilen önkoşulların tümünü yerine getirmediyseniz PowerShell penceresinde uyarılar görüntülenir. Aracıyı yüklemeden önce [buradaki](how-to-connect-health-agent-install.md#requirements) gereksinimleri karşıladığınızdan emin olun. Bu hataların bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz.

![AD DS için Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Aracının yüklü olduğunu doğrulamak için etki alanı denetleyicisinde aşağıdaki hizmetleri arayın.

* Azure AD Connect Health AD DS Insights Hizmeti
* Azure AD Connect Health AD DS İzleme Hizmeti

Yapılandırmayı tamamladıysanız bu hizmetlerin çalışır durumda olması gerekir. Aksi halde bu hizmetler yapılandırma tamamlanıncaya kadar durdurulur.

![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Birden çok sunucuda hızlı aracı yüklemesi

1. Azure AD 'de parola ile bir kullanıcı hesabı oluşturun.
2. Bu yerel AAD hesabına ait **sahip** rolünü portal üzerinden Azure AD Connect Health atayın. [Buradaki](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)adımları izleyin. Rolü tüm hizmet örneklerine atayın. 
3. Yükleme için yerel etki alanı denetleyicisindeki. exe MSI dosyasını indirin.
4. Kaydolmak için aşağıdaki betiği çalıştırın. Parametreleri, oluşturulan yeni kullanıcı hesabı ve parolası ile değiştirin. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -UserPrincipalName $USERNAME -Credential $myCreds

```

1. İşiniz bittiğinde, aşağıdakilerden birini veya birkaçını yaparak yerel hesap için erişimi kaldırabilirsiniz: 
    * AAD Connect Health için yerel hesap için rol atamasını kaldırma
    * Yerel hesabın parolasını döndürün. 
    * AAD yerel hesabını devre dışı bırak
    * AAD yerel hesabını silme  

## <a name="agent-registration-using-powershell"></a>PowerShell kullanarak Aracı Kaydı

Uygun aracı setup.exe dosyasını yükledikten sonra role bağlı olarak aşağıdaki PowerShell komutlarını kullanarak aracı kaydı adımını gerçekleştirebilirsiniz. Bir PowerShell penceresi açın ve uygun komutu yürütün:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Bu komutlar "Credential"’ı, kayıt işlemini etkileşimli olmayan bir şekilde veya bir Sunucu Çekirdeği makinesinde tamamlamaya yönelik bir parametre olarak kabul eder.
* Credential, parametre olarak geçirilen bir PowerShell değişkeninde yakalanabilir.
* Aracıları kaydetme erişimi olan ve MFA özelliği etkin olmayan herhangi bir Azure AD Kimliği sağlayabilirsiniz.
* Varsayılan olarak, Genel Yöneticiler aracı kaydını gerçekleştirme erişimine sahiptir. Ayrıca, daha az ayrıcalıklı diğer kimliklerin bu adımı uygulamasına izin verebilirsiniz. [Rol Tabanlı Access Control](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) hakkında daha fazla bilgi edinin.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Azure AD Connect Health Aracılarını HTTP Ara Sunucusunu kullanacak şekilde yapılandırma

Azure AD Connect Health Aracılarını bir HTTP Ara Sunucusunu kullanacak şekilde yapılandırabilirsiniz.

> [!NOTE]
> * Aracı, web isteklerinde bulunmak için Microsoft Windows HTTP Hizmetlerinin yerine System.Net hizmetini kullandığından "Netsh WinHttp set ProxyServerAddress" seçeneğini kullanmanız desteklenmez.
> * Şifrelenmiş Https iletilerinin geçilmesi için, yapılandırılan Http Ara Sunucu adresi kullanılır.
> * HTTPBasic kullanılarak kimliği doğrulanmış ara sunucular desteklenmez.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Durum Aracısı Ara Sunucu Yapılandırmasını Değiştirme

Azure AD Connect Health Aracısını bir HTTP Ara Sunucusunu kullanacak şekilde yapılandırmak üzere aşağıdaki seçeneklere sahipsiniz.

> [!NOTE]
> Proxy ayarlarının güncelleştirilmesi için tüm Azure AD Connect Health Aracısı hizmetlerinin yeniden başlatılması gerekir. Şu komutu çalıştırın:<br />
> Restart-Service Azureadconnecthegizli *
>
>

#### <a name="import-existing-proxy-settings"></a>Var olan ara sunucu Ayarlarını içeri aktarma

##### <a name="import-from-internet-explorer"></a>Internet Explorer'dan içeri aktarma

Internet Explorer HTTP proxy ayarları Azure AD Connect Health Aracıları tarafından kullanılmak üzere içeri aktarılabilir. Health aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu yürütün:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>WinHTTP'den içeri aktarma

WinHTTP proxy ayarları Azure AD Connect Health Aracıları tarafından kullanılmak üzere içeri aktarılabilir. Health aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu yürütün:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Ara sunucu adreslerini el ile belirtme

Health Aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu yürüterek bir proxy sunucusunu elle belirtebilirsiniz:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Örnek: *Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver: 443*

* "adres", DNS'nin çözümlenebileceği bir sunucu adı veya bir IPv4 adresi olabilir.
* "bağlantı noktası" atlanabilir. Atlanması durumunda varsayılan bağlantı noktası olarak 443 seçilir.

#### <a name="clear-existing-proxy-configuration"></a>Var olan ara sunucu yapılandırmasını silme

Aşağıdaki komutu çalıştırarak var olan proxy sunucu yapılandırmasını silebilirsiniz:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Geçerli ara sunucu ayarlarını okuma

Aşağıdaki komutu çalıştırarak geçerli olarak yapılandırılmış olan proxy sunucu ayarlarını okuyabilirsiniz:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health Hizmeti için Bağlantı Testi

Azure AD Connect Health aracısıyla Azure AD Connect Health hizmeti arasındaki bağlantının kesilmesine neden olabilecek sorunlar meydana gelebilir. Ağ sorunları, izin sorunları veya diğer çeşitli nedenler bunlara dahildir.

Aracı iki saatten uzun bir süre boyunca Azure AD Connect Health hizmetine veri gönderemezse portalda şu uyarı ile gösterilir: "Health Hizmeti verileri güncel değil." Aşağıdaki PowerShell komutunu çalıştırarak, etkilenen Azure AD Connect Health aracısının Azure AD Connect Health hizmetine veri yükleyebildiğini onaylayabilirsiniz:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Rol parametresi şu anda şu değerleri alır:

* ADFS
* Eşitleme
* EKLER

> [!NOTE]
> Bağlantı aracını kullanabilmek için öncelikle aracı kaydını tamamlamanız gerekir. Aracı kaydını tamamlayamıyorsanız Azure AD Connect Health için tüm [gereksinimleri](how-to-connect-health-agent-install.md#requirements) karşıladığınızdan emin olun. Bu bağlantı testi, aracı kaydı sırasında varsayılan olarak gerçekleştirilir.
>
>

## <a name="related-links"></a>İlgili bağlantılar

* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health İşlemleri](how-to-connect-health-operations.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Eşitleme için Azure AD Connect Health'i kullanma](how-to-connect-health-sync.md)
* [Azure AD Connect Health'i AD DS ile Kullanma](how-to-connect-health-adds.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health Sürüm Geçmişi](reference-connect-health-version-history.md)
