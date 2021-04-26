---
title: Azure Active Directory 'de Connect Health aracılarını yükler
description: Bu Azure AD Connect Health makalede, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ve eşitleme için aracı yüklemesi açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3aff7b99dad910a9691eef2004df856ca883789
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224393"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Aracı yüklemesini Azure AD Connect Health

Bu makalede, Azure Active Directory (Azure AD) Connect Health aracılarını yüklemeyi ve yapılandırmayı öğreneceksiniz. Aracıları indirmek için [Bu yönergelere](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)bakın.

## <a name="requirements"></a>Gereksinimler

Aşağıdaki tabloda Azure AD Connect Health kullanımı için gereksinimler listelenmektedir.

| Gereksinim | Açıklama |
| --- | --- |
| Azure AD Premium (P1 veya P2) alt Sciption vardır.  |Azure AD Connect Health, Azure AD Premium özelliğidir (P1 veya P2). Daha fazla bilgi için bkz. [Azure AD Premium kaydolma](../fundamentals/active-directory-get-started-premium.md). <br /><br />30 günlük ücretsiz denemeyi başlatmak için bkz. [deneme sürümü başlatma](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Azure AD 'de Genel yöneticuyoruz. |Varsayılan olarak, yalnızca genel Yöneticiler, sistem durumu aracılarını yükleyebilir ve yapılandırabilir, portala erişebilir ve Azure AD Connect Health içinde herhangi bir işlem yapabilir. Daha fazla bilgi için bkz. [Azure AD dizininizi yönetme](../fundamentals/active-directory-whatis.md). <br /><br /> Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak, kuruluşunuzdaki diğer kullanıcıların Azure AD Connect Health erişmesine izin verebilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Önemli**: aracıları yüklemek için bir iş veya okul hesabı kullanın. Microsoft hesabı kullanamazsınız. Daha fazla bilgi için bkz. [Azure 'a kuruluş olarak kaydolma](../fundamentals/sign-up-organization.md). |
| Azure AD Connect Health Aracısı hedeflenen her sunucuya yüklenir. | Sistem durumu aracılarının veri alabilmesi ve izleme ve analiz özellikleri sağlaması için hedeflenen sunucularda yüklü ve yapılandırılmış olması gerekir. <br /><br />Örneğin, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) altyapınızdan veri almak için aracıyı AD FS sunucusuna ve Web uygulaması proxy sunucusuna yüklemelisiniz. Benzer şekilde, şirket içi Azure AD Domain Services (Azure AD DS) altyapınızdan veri almak için aracıyı etki alanı denetleyicilerine yüklemelisiniz.  |
| Azure hizmet uç noktalarında giden bağlantı vardır. | Yükleme ve çalışma zamanı sırasında, aracı ile Azure AD Connect Health hizmet uç noktaları arasında bağlantı kurulması gerekir. Güvenlik duvarları giden bağlantıyı engel, [giden bağlantı uç noktalarını](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) izin verilenler listesine ekleyin. |
|Giden bağlantı, IP adreslerini temel alır. | IP adreslerine göre güvenlik duvarı filtrelemesi hakkında daha fazla bilgi için bkz. [Azure IP aralıkları](https://www.microsoft.com/download/details.aspx?id=56519).|
| Giden trafik için TLS denetlemesi filtrelenmiştir veya devre dışı bırakıldı. | Ağ katmanında giden trafik için TLS incelemesi veya sonlandırılması varsa, aracı kayıt adımı veya veri yükleme işlemleri başarısız olabilir. Daha fazla bilgi için bkz. [TLS Incelemesini ayarlama](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Sunucudaki güvenlik duvarı bağlantı noktaları aracıyı çalıştırıyor. |Aracı, Azure AD Connect Health hizmet uç noktaları ile iletişim kurabilmesi için aşağıdaki güvenlik duvarı bağlantı noktalarının açılmasını gerektirir: <br /><li>TCP bağlantı noktası 443</li><li>TCP bağlantı noktası 5671</li> <br />Aracının en son sürümü bağlantı noktası 5671 ' i gerektirmez. Yalnızca 443 numaralı bağlantı noktası gerekli olacak şekilde en son sürüme yükseltin. Daha fazla bilgi için bkz. [karma kimlik için gerekli bağlantı noktaları ve protokoller](./reference-connect-ports.md). |
| Internet Explorer Artırılmış Güvenlik etkinse, belirtilen Web sitelerine izin verin.  |Internet Explorer Artırılmış Güvenlik etkinse, aracıyı yüklediğiniz sunucuda aşağıdaki Web sitelerine izin verin:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Kuruluşunuzun Azure AD tarafından güvenilen Federasyon sunucusu (örneğin, https: \/ /STS.contoso.com)</li> <br />Daha fazla bilgi için bkz. [Internet Explorer 'ı yapılandırma](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Ağınızda bir ara sunucu varsa, bu tablonun sonunda görüntülenen nota bakın.|
| PowerShell sürüm 4,0 veya daha yeni bir sürümü yüklü. | Windows Server 2012, PowerShell sürüm 3,0 içerir. Bu sürüm, aracı için yeterli *değil* .</br></br> Windows Server 2012 R2 ve üzeri, PowerShell 'in yeterince yeni bir sürümünü içerir.|
|FIPS (Federal bilgi Işleme standardı) devre dışı.|Azure AD Connect Health aracıları FIPS 'yi desteklemez.|

> [!IMPORTANT]
> Windows Server Core, Azure AD Connect Health aracısının yüklenmesini desteklemez.


> [!NOTE]
> Son derece kilitlenmiş ve kısıtlı ortamınız varsa, Internet Explorer Artırılmış güvenlik için Tablo listesinden daha fazla URL eklemeniz gerekir. Ayrıca, sonraki bölümde yer alan tabloda listelenen URL 'Leri ekleyin.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure hizmet uç noktalarına giden bağlantı

Yükleme ve çalışma zamanı sırasında aracının hizmet uç noktalarına Azure AD Connect Health bağlantısı olması gerekir. Güvenlik duvarları giden bağlantıyı engelleiyorsa, aşağıdaki tablodaki URL 'Lerin varsayılan olarak engellenmediğinden emin olun. 

Bu URL 'Lerin güvenlik izlemesini veya denetimini devre dışı bırakın. Bunun yerine, diğer internet trafiğine izin verecek şekilde bunlara izin verin. 

Bu URL 'Ler Azure AD Connect Health hizmet uç noktaları ile iletişime izin verir. Bu makalenin ilerleyen kısımlarında, kullanarak [giden bağlantıyı nasıl denetleyeceğinizi](#test-connectivity-to-azure-ad-connect-health-service) öğreneceksiniz `Test-AzureADConnectHealthConnectivity` .

| Etki alanı ortamı | Gerekli Azure hizmet uç noktaları |
| --- | --- |
| Genel genel | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-Port: 5671 (aracının en son sürümünde bu uç nokta gerekli değildir.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (bu uç nokta yalnızca kayıt sırasında bulma amacıyla kullanılır.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Almanya | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (bu uç nokta yalnızca kayıt sırasında bulma amacıyla kullanılır.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Kamu | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (bu uç nokta yalnızca kayıt sırasında bulma amacıyla kullanılır.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Aracıyı yükleme

Azure AD Connect Health aracısını indirmek ve yüklemek için: 

* Azure AD Connect Health [gereksinimlerini](how-to-connect-health-agent-install.md#requirements) karşıladığınızdan emin olun.
* AD FS için Azure AD Connect Health kullanmaya başlayın:
    * [AD FS için Azure AD Connect Health aracısını indirin](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Bkz. [yükleme yönergeleri](#install-the-agent-for-ad-fs).
* Eşitleme için Azure AD Connect Health kullanmaya başlayın:
    * [Azure AD Connect'in en son sürümünü indirip yükleyin](https://go.microsoft.com/fwlink/?linkid=615771). Eşitleme için durum Aracısı, Azure AD Connect yüklemesinin bir parçası olarak yüklenir (sürüm 1.0.9125.0 veya üzeri).
* Azure AD DS için Azure AD Connect Health kullanmaya başlama:
    * [Azure AD DS için Azure AD Connect Health aracısını indirin](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Bkz. [yükleme yönergeleri](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>AD FS için aracıyı yükler

> [!NOTE]
> AD FS sunucunuz, eşitleme sunucusundan farklı olmalıdır. AD FS aracısını eşitleme sunucunuza yüklemeyin.
>

Aracıyı yüklemeden önce AD FS sunucusu ana bilgisayar adınızın benzersiz olduğundan ve AD FS hizmetinde olmadığından emin olun.
Aracı yüklemesini başlatmak için indirdiğiniz *. exe* dosyasına çift tıklayın. İlk pencerede, **yüklensin**' i seçin.

![Azure AD Connect Health AD FS aracısının yükleme penceresini gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/install1.png)

Yükleme bittikten sonra **Şimdi Yapılandır**' ı seçin.

![Azure AD Connect Health AD FS Aracısı yüklemesinin onay iletisini gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/install2.png)

Aracı kayıt işlemini başlatmak için bir PowerShell penceresi açılır. İstendiğinde, aracıyı kaydetme izinlerine sahip bir Azure AD hesabı kullanarak oturum açın. Varsayılan olarak, genel yönetici hesabının izinleri vardır.

![Azure AD Connect Health AD FS için oturum açma penceresini gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/install3.png)

Oturum açtıktan sonra, PowerShell devam eder. Tamamlandığında, PowerShell 'i kapatabilirsiniz. Yapılandırma tamamlanmıştır.

Bu noktada, aracının gerekli verileri bulut hizmetine güvenli bir şekilde yüklemesine izin vermek için aracı hizmetleri otomatik olarak başlamalıdır.

Tüm önkoşulları karşılamadıysanız, uyarılar PowerShell penceresinde görüntülenir. Aracıyı yüklemeden önce [gereksinimleri](how-to-connect-health-agent-install.md#requirements) tamamladığınızdan emin olun. Aşağıdaki ekran görüntüsünde Bu uyarıların bir örneği gösterilmektedir.

![Betiği yapılandırma AD FS Azure AD Connect Health gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/install4.png)

Aracının yüklendiğini doğrulamak için sunucuda aşağıdaki hizmetleri arayın. Yapılandırmayı tamamladıysanız bu hizmetlerin çalışır durumda olması gerekir. Aksi takdirde, yapılandırma tamamlanana kadar durdurulur.

* Azure AD Connect Health AD FS Tanılama Hizmeti
* Azure AD Connect Health AD FS Öngörü Hizmeti
* Azure AD Connect Health AD FS İzleme Hizmeti

![Azure AD Connect Health AD FS hizmetlerini gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>AD FS için denetimi etkinleştir

> [!NOTE]
> Bu bölüm yalnızca AD FS sunucuları için geçerlidir. Web uygulaması ara sunucuları üzerinde bu adımları izlemeniz gerekmez.
>

Kullanım Analizi özelliği, verileri toplayıp analiz etmek için gereklidir. Bu nedenle Azure AD Connect Health Aracısı AD FS denetim günlüklerindeki bilgilere ihtiyaç duyuyor. Bu günlükler varsayılan olarak etkinleştirilmemiştir. AD FS denetimini etkinleştirmek ve AD FS sunucularınızda AD FS denetim günlüklerini bulmak için aşağıdaki yordamları kullanın.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2'de AD FS için denetimi etkinleştirme

1. Başlangıç ekranında **Sunucu Yöneticisi**' i açın ve ardından **yerel güvenlik ilkesi**' ni açın. Veya görev çubuğunda **Sunucu Yöneticisi** açın ve ardından **Araçlar/yerel güvenlik ilkesi**' ni seçin.
2. *Güvenlik yerel ilkeler \ Kullanıcı hakları atama* klasörüne gidin. Ardından **güvenlik denetimleri oluştur**' a çift tıklayın.
3. **Yerel Güvenlik Ayarları** sekmesinde AD FS hizmet hesabının listelenmiş olduğunu doğrulayın. Listede yoksa, **Kullanıcı veya Grup Ekle**' yi seçin ve listeye ekleyin. Ardından **Tamam**’ı seçin.
4. Denetimi etkinleştirmek için yükseltilmiş ayrıcalıklarla bir komut Istemi penceresi açın. Sonra şu komutu çalıştırın: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **Yerel Güvenlik İlkesi**’ni kapatın.
    >[!Important]
    >Aşağıdaki adımlar yalnızca birincil AD FS sunucuları için gereklidir. 
1. **AD FS Yönetim** ek bileşenini açın. ( **Sunucu Yöneticisi**, **Araçlar**  >  ' ı seçin. **AD FS Yönetimi**.)
1. **Eylemler** bölmesinde **Federasyon Hizmeti Özellikleri Düzenle**' yi seçin.
1. **Federasyon Hizmeti özellikleri** Iletişim kutusunda **Olaylar** sekmesini seçin.
1. **Başarı denetimleri ve başarısızlık denetimleri** onay kutularını işaretleyin ve ardından **Tamam**' ı seçin.
1. PowerShell aracılığıyla ayrıntılı günlüğe kaydetmeyi etkinleştirmek için aşağıdaki komutu kullanın: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016'da AD FS için denetimi etkinleştirme

1. Başlangıç ekranında **Sunucu Yöneticisi**' i açın ve ardından **yerel güvenlik ilkesi**' ni açın. Veya görev çubuğunda **Sunucu Yöneticisi** açın ve ardından **Araçlar/yerel güvenlik ilkesi**' ni seçin.
2. *Güvenlik yerel ayarları \ Kullanıcı hakları ataması* klasörüne gidin ve **güvenlik denetimleri oluştur**' a çift tıklayın.
3. **Yerel Güvenlik Ayarları** sekmesinde AD FS hizmet hesabının listelenmiş olduğunu doğrulayın. Listede yoksa, **Kullanıcı veya Grup Ekle**' yi seçin ve AD FS hizmet hesabını listeye ekleyin. Ardından **Tamam**’ı seçin.
4. Denetimi etkinleştirmek için yükseltilmiş ayrıcalıklarla bir komut Istemi penceresi açın. Sonra şu komutu çalıştırın: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **Yerel Güvenlik İlkesi**’ni kapatın.
    >[!Important]
    >Aşağıdaki adımlar yalnızca birincil AD FS sunucuları için gereklidir.
1. **AD FS Yönetim** ek bileşenini açın. ( **Sunucu Yöneticisi**, **Araçlar**  >  ' ı seçin. **AD FS Yönetimi**.)
1. **Eylemler** bölmesinde **Federasyon Hizmeti Özellikleri Düzenle**' yi seçin.
1. **Federasyon Hizmeti özellikleri** Iletişim kutusunda **Olaylar** sekmesini seçin.
1. **Başarı denetimleri ve başarısızlık denetimleri** onay kutularını işaretleyin ve ardından **Tamam**' ı seçin. Başarı denetimleri ve hata denetimleri varsayılan olarak etkinleştirilmelidir.
1. Bir PowerShell penceresi açın ve aşağıdaki komutu çalıştırın: 

    `Set-AdfsProperties -AuditLevel Verbose`

"Temel" Denetim düzeyi varsayılan olarak etkindir. Daha fazla bilgi için bkz. [Windows Server 2016 ' de denetim geliştirmesi AD FS](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS denetim günlüklerini bulma

1. **Olay Görüntüleyicisi**'ni açın.
2. **Windows günlükleri**' ne gidin ve ardından **güvenlik**' i seçin.
3. Sağ tarafta **geçerli günlükleri filtrele**' yi seçin.
4. **Olay kaynakları** Için **AD FS denetleme**' yi seçin.

    Denetim günlükleri hakkında daha fazla bilgi için bkz. [işlemler soruları](reference-connect-health-faq.md#operations-questions).

    ![Geçerli günlük filtresi penceresini gösteren ekran görüntüsü. "Olay kaynakları" alanında "AD FS denetimi" seçilidir.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> AD FS denetimi bir grup ilkesiyle devre dışı bırakılabilir. AD FS denetim devre dışıysa, oturum açma etkinlikleri hakkında kullanım analizi kullanılamaz. AD FS denetimini devre dışı bırakan bir Grup ilkeniz olmadığından emin olun.
>


## <a name="install-the-agent-for-sync"></a>Eşitleme için aracıyı yükler

Eşitleme için Azure AD Connect Health Aracısı Azure AD Connect en son sürümüne otomatik olarak yüklenir. Eşitleme için Azure AD Connect kullanmak için [Azure AD Connect en son sürümünü indirin](https://www.microsoft.com/download/details.aspx?id=47594) ve yükleyin.

Aracının yüklü olduğunu doğrulamak için sunucuda aşağıdaki hizmetleri arayın. Yapılandırmayı tamamladıysanız hizmetlerin zaten çalışıyor olması gerekir. Aksi takdirde, yapılandırma tamamlanana kadar hizmetler durdurulur.

* Azure AD Connect Health Eşitleme Öngörü Hizmeti
* Azure AD Connect Health Eşitleme İzleme Hizmeti

![Sunucuda Eşitleme Hizmetleri için çalışan Azure AD Connect Health gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health kullanmak için Azure AD Premium (P1 veya P2) sahip olmanız gerektiğini unutmayın. Azure AD Premium yoksa, Azure portal yapılandırmayı tamamlayamazsınız. Daha fazla bilgi için [gereksinimlere](how-to-connect-health-agent-install.md#requirements)bakın.
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Eşitleme için Azure AD Connect Health el ile kaydet

Eşitleme Aracısı kaydı için Azure AD Connect Health Azure AD Connect başarıyla yükledikten sonra başarısız olursa aracıyı el ile kaydetmek için bir PowerShell komutu kullanabilirsiniz.

> [!IMPORTANT]
> Bu PowerShell komutunu yalnızca Azure AD Connect yükledikten sonra aracı kaydı başarısız olursa kullanın.
>
>

Aşağıdaki PowerShell komutunu kullanarak Azure AD Connect Health aracısını eşitleme için el ile kaydedin. Aracı başarılı bir şekilde kaydolduktan sonra Azure AD Connect Health hizmetleri başlatılır.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Komut için şu parametreler kullanılır:

* **Attributefiltering**: `$true` (varsayılan) Azure AD Connect varsayılan öznitelik kümesini eşitlemiyor ve filtrelenmiş bir öznitelik kümesi kullanmak üzere özelleştirildiyse. Aksi takdirde, kullanın `$false` .
* **Stagingmode**: `$false` (varsayılan) Azure AD Connect sunucusu hazırlama modunda *değilse* . Sunucu, hazırlama modunda olacak şekilde yapılandırıldıysa, kullanın `$true` .

Kimlik doğrulaması sorulduğunda, Azure AD Connect yapılandırmak için kullandığınız genel yönetici hesabını (gibi admin@domain.onmicrosoft.com ) kullanın.

## <a name="install-the-agent-for-azure-ad-ds"></a>Azure AD DS için aracıyı yükler

Aracı yüklemesini başlatmak için indirdiğiniz *. exe* dosyasına çift tıklayın. İlk pencerede, **yüklensin**' i seçin.

![AD DS yükleme penceresi için Azure AD Connect Health aracısını gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Yükleme tamamlandığında **Şimdi Yapılandır**' ı seçin.

![Azure AD DS için Azure AD Connect Health aracısının yüklenmesini izleyen pencereyi gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Bir komut Istemi penceresi açılır. PowerShell çalıştırmaları `Register-AzureADConnectHealthADDSAgent` . İstendiğinde Azure 'da oturum açın.

![Azure AD DS Azure AD Connect Health aracısına yönelik oturum açma penceresini gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Oturum açtıktan sonra, PowerShell devam eder. Tamamlandığında, PowerShell 'i kapatabilirsiniz. Yapılandırma tamamlanmıştır.

Bu noktada, hizmetler otomatik olarak başlatılmalıdır ve aracının verileri izleyip toplamasına izin verir. Önceki bölümlerde özetlenen tüm önkoşulları karşılamadıysanız, uyarılar PowerShell penceresinde görüntülenir. Aracıyı yüklemeden önce [gereksinimleri](how-to-connect-health-agent-install.md#requirements) tamamladığınızdan emin olun. Aşağıdaki ekran görüntüsünde Bu uyarıların bir örneği gösterilmektedir.

![Azure AD DS yapılandırmasına yönelik Azure AD Connect Health aracısına yönelik bir uyarı gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Aracının yüklendiğini doğrulamak için, etki alanı denetleyicisinde aşağıdaki hizmetleri arayın:

* Azure AD Connect Health AD DS Insights Hizmeti
* Azure AD Connect Health AD DS İzleme Hizmeti

Yapılandırmayı tamamladıysanız bu hizmetlerin çalışır durumda olması gerekir. Aksi takdirde, yapılandırma bitene kadar durdurulur.

![Etki alanı denetleyicisinde çalışan hizmetleri gösteren ekran görüntüsü.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Aracıyı birden çok sunucuya hızlı bir şekilde yükler

1. Azure AD 'de bir kullanıcı hesabı oluşturun. Parolayı kullanarak güvenli hale getirin.
2. Portalı kullanarak bu yerel Azure AD hesabına ait **sahip** rolü Azure AD Connect Health atayın. [Bu adımları](how-to-connect-health-operations.md#manage-access-with-azure-rbac)izleyin. Rolü tüm hizmet örneklerine atayın. 
3. Yükleme için yerel etki alanı denetleyicisindeki *. exe* MSI dosyasını indirin.
4. Aşağıdaki betiği çalıştırın. Parametreleri Yeni Kullanıcı hesabınızla ve parolasıyla değiştirin. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Bitirdiğinizde, aşağıdaki görevlerden birini veya birkaçını yaparak yerel hesap için erişimi kaldırabilirsiniz: 
* Azure AD Connect Health yerel hesabının rol atamasını kaldırın.
* Yerel hesabın parolasını döndürün. 
* Azure AD yerel hesabını devre dışı bırakın.
* Azure AD yerel hesabını silin.  

## <a name="register-the-agent-by-using-powershell"></a>Aracıyı PowerShell kullanarak kaydetme

Uygun aracı *setup.exe* dosyasını yükledikten sonra, rolüne bağlı olarak aşağıdaki PowerShell komutlarını kullanarak aracıyı kaydedebilirsiniz. Bir PowerShell penceresi açın ve uygun komutu çalıştırın:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> Bağımsız bulutlarına göre kaydolmak için aşağıdaki komut satırlarını kullanın:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


Bu komutlar `Credential` , kaydı etkileşimli olmayan bir şekilde tamamlamaya veya sunucu çekirdeği çalıştıran bir makinede kaydı tamamlamaya yönelik bir parametre olarak kabul eder. Şunları göz önünde bulundurun:
* `Credential`Bir parametre olarak geçirilmiş bir PowerShell değişkeninde yakalayabilirsiniz.
* Aracıları kaydetme izinleri olan ve çok faktörlü kimlik doğrulaması *etkinleştirilmemiş olan herhangi* BIR Azure ad kimliği sağlayabilirsiniz.
* Varsayılan olarak, genel Yöneticilerin aracıları kaydetme izinleri vardır. Ayrıca, bu adımı daha az ayrıcalıklı kimliklere izin verebilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Azure AD Connect Health aracılarını HTTP proxy kullanacak şekilde yapılandırma

Azure AD Connect Health aracılarını, bir HTTP proxy 'si ile çalışacak şekilde yapılandırabilirsiniz.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` desteklenmez. Aracı Web istekleri yapmak için Windows HTTP Hizmetleri yerine System.Net kullanır.
> * Yapılandırılan HTTP proxy adresi, şifrelenmiş HTTPS iletilerini geçirmek için kullanılır.
> * HTTPBasic kullanılarak kimliği doğrulanmış ara sunucular desteklenmez.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Aracı ara sunucu yapılandırmasını değiştirme

Azure AD Connect Health aracısını bir HTTP proxy 'si kullanacak şekilde yapılandırmak için şunları yapabilirsiniz:
* Mevcut proxy ayarlarını içeri aktarın.
* Proxy adreslerini el ile belirtin.
* Var olan ara sunucu yapılandırmasını temizleyin.

> [!NOTE]
> Proxy ayarlarını güncelleştirmek için tüm Azure AD Connect Health Aracısı hizmetlerini yeniden başlatmanız gerekir. Şu komutu çalıştırın:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Mevcut proxy ayarlarını içeri aktar

Azure AD Connect Health aracılarının ayarları kullanabilmesi için Internet Explorer HTTP proxy ayarlarını içeri aktarabilirsiniz. Sistem durumu aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

WinHTTP proxy ayarlarını, Azure AD Connect Health aracılarının bunları kullanabilmesi için içeri aktarabilirsiniz. Sistem durumu aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Proxy adreslerini el ile belirt

Bir proxy sunucusunu el ile belirtebilirsiniz. Sistem durumu aracısını çalıştıran sunucuların her birinde aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Aşağıda bir örnek verilmiştir: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

Bu örnekte:
* `address`Ayar, DNS ile çözülebilir bir sunucu adı veya bir IPv4 adresi olabilir.
* Çıkarabilirsiniz `port` . Bunu yaparsanız, 443 varsayılan bağlantı noktasıdır.

#### <a name="clear-the-existing-proxy-configuration"></a>Var olan ara sunucu yapılandırmasını temizle

Aşağıdaki komutu çalıştırarak var olan proxy sunucu yapılandırmasını silebilirsiniz:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Geçerli ara sunucu ayarlarını okuma

Şu komutu çalıştırarak geçerli proxy ayarlarını okuyabilirsiniz:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health hizmetine bağlantı sınama

Bazen Azure AD Connect Health Aracısı Azure AD Connect Health hizmetiyle bağlantıyı kaybedebilir. Bu bağlantı kaybından oluşan nedenler ağ sorunları, izin sorunları ve diğer çeşitli sorunlar içerebilir.

Aracı Azure AD Connect Health hizmetine iki saatten uzun süre veri gönderemez, portalda şu uyarı görünür: "Sistem Sağlığı Hizmeti verileri güncel değil." 

Etkilenen Azure AD Connect Health aracısının, aşağıdaki PowerShell komutunu çalıştırarak verileri Azure AD Connect Health hizmetine yükleyip yükleyemeyeceğini öğrenebilirsiniz:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Rol parametresi şu anda şu değerleri alır:

* ADFS
* Sync
* EKLER

> [!NOTE]
> Bağlantı aracını kullanmak için önce aracıyı kaydetmeniz gerekir. Aracı kaydını tamamlayamadıysanız Azure AD Connect Health için tüm [gereksinimleri](how-to-connect-health-agent-install.md#requirements) karşıladığınızdan emin olun. Bağlantı, aracı kaydı sırasında varsayılan olarak sınanır.
>
>

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ilgili makalelere göz atın:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health işlemler](how-to-connect-health-operations.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Eşitleme için Azure AD Connect Health'i kullanma](how-to-connect-health-sync.md)
* [Azure AD DS ile Azure AD Connect Health kullanma](how-to-connect-health-adds.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health sürüm geçmişi](reference-connect-health-version-history.md)
