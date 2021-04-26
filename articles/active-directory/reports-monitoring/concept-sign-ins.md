---
title: Azure Active Directory portalındaki oturum açma etkinlik raporları | Microsoft Docs
description: Azure Active Directory portalındaki oturum açma etkinlik raporlarına giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a4cd69224daa05a3e5931fbde1c85bb8dc9e46a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719016"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory portalındaki oturum açma etkinlik raporları

BT Yöneticisi olarak BT ortamınızın nasıl çalıştığını öğrenmek istersiniz. Sisteminizin sistem durumu hakkındaki bilgiler, olası sorunlara yanıt vermek isteyip istemediğinizi değerlendirmenizi sağlar. 

Bu hedefle ilgili sizi desteklemek için Azure Active Directory portalı, üç etkinlik günlüğüne erişmenizi sağlar:

- **Oturum açma** bilgileri – oturum açma işlemleri ve kaynaklarınızın kullanıcılarınız tarafından nasıl kullanıldığı hakkında bilgiler.
- **[Denetim](concept-audit-logs.md)** – kiracınıza uygulanan kullanıcılar ve Grup Yönetimi veya kiracınızın kaynaklarına uygulanan güncelleştirmeler gibi değişiklikler hakkında bilgiler.
- **[Sağlama](concept-provisioning-logs.md)** : ServiceNow 'da bir grup oluşturma veya Workday 'den içeri aktarılan bir kullanıcı gibi sağlama hizmeti tarafından gerçekleştirilen etkinlikler.

Bu makale, oturum açma raporuna genel bir bakış sunar.

## <a name="prerequisites"></a>Önkoşullar

### <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?

* Güvenlik Yöneticisi, güvenlik okuyucu, genel okuyucu ve rapor okuyucu rolleri içindeki kullanıcılar
* Genel Yöneticiler
* Tüm kullanıcılar (yönetici olmayan) kendi oturum açma etkinliklerine erişebilirler 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Oturum açma etkinliğine erişebilmek için hangi Azure AD lisansınızın olması gerekir?

Oturum açma Etkinliği raporu [Azure AD 'nin tüm sürümlerinde](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) kullanılabilir ve ayrıca Microsoft Graph API aracılığıyla da erişilebilir.

## <a name="sign-ins-report"></a>Oturum açma işlemleri raporu

Kullanıcı oturum açma işlemleri raporu, aşağıdaki soruların yanıtlarını sağlar:

* Belirli bir kullanıcının oturum açma düzeni nedir?
* Bir hafta içerisinde kaç kullanıcı oturum açtı?
* Bu açılan oturumların durumu nedir?

[Azure Portal](https://portal.azure.com) menüsünde **Azure Active Directory**' i seçin veya herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.

![Azure Active Directory'yi seçin](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

**İzleme** bölümünde oturum **açma** işlemlerini seçerek [oturum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)açma işlemlerini açın.

![Ekran görüntüsü, Izleme menüsünden seçilen oturum açma işlemlerini gösterir.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Oturum açma etkinliği")

Bazı oturum açma kayıtlarının portalda gösterilmesi iki saate kadar sürebilir.

> [!IMPORTANT]
> Oturum açma raporu yalnızca **etkileşimli** oturum açma işlemlerini, diğer bir deyişle kullanıcının Kullanıcı adı ve parolasını kullanarak el ile oturum açtığı oturum açma işlemlerini görüntüler. Hizmetten hizmete kimlik doğrulaması gibi etkileşimli olmayan oturum açma işlemleri raporda gösterilmez. 

Oturum açma günlüklerinin aşağıdakileri gösteren bir varsayılan liste görünümü vardır:

- Oturum açma tarihi
- İlgili kullanıcı
- Kullanıcının oturum açmış olduğu uygulama
- Oturum açma durumu
- Risk algılama durumu
- Çok faktörlü kimlik doğrulaması (MFA) gereksiniminin durumu

![Ekran görüntüsünde Office 365 SharePoint Online oturum açma işlemleri gösterilir.](./media/concept-sign-ins/sign-in-activity.png "Oturum açma etkinliği")

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Ekran görüntüsü, oturum açma sayfasındaki sütunlar seçeneğini gösterir.](./media/concept-sign-ins/19.png "Oturum açma etkinliği")

**Sütunlar** iletişim kutusu seçilebilir özniteliklere erişmenizi sağlar. Bir oturum açma raporunda, belirli bir oturum açma isteği için sütun olarak birden fazla değere sahip olan alanlar olamaz. Bu, örneğin, kimlik doğrulama ayrıntıları, koşullu erişim verileri ve ağ konumu için geçerlidir.   

![Ekran görüntüsünde, öznitelikleri seçebileceğiniz sütunlar iletişim kutusu gösterilir.](./media/concept-sign-ins/columns.png "Oturum açma etkinliği")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Ekran görüntüsü ayrıntılı bir bilgi görünümünü gösterir.](./media/concept-sign-ins/basic-sign-in.png "Oturum açma etkinliği")

> [!NOTE]
> Müşteriler artık tüm oturum açma raporları aracılığıyla koşullu erişim ilkelerini giderebilirler. Bir oturum açma kaydı için **koşullu erişim** sekmesine tıkladığınızda müşteriler koşullu erişim durumunu gözden geçirebilir ve oturum açma için uygulanan ilkelerin ayrıntılarını ve her bir ilkenin sonucunu görebilir.
> Daha fazla bilgi için, [tüm oturum açma IŞLEMLERININ CA bilgileri hakkında sık sorulan sorular](reports-faq.md#conditional-access)bölümüne bakın.


## <a name="sign-in-error-code"></a>Oturum açma hata kodu

Bir oturum açma başarısız olursa, ilgili günlük öğesinin **temel bilgi** bölümünde nedeni hakkında daha fazla bilgi edinebilirsiniz. 

![oturum açma hata kodu](./media/concept-all-sign-ins/error-code.png)
 
Günlük öğesi size bir hata nedeni sağladığından, [oturum açma hatası arama aracını](https://login.microsoftonline.com/error)kullanarak daha fazla bilgi alabileceğiniz durumlar vardır. Örneğin, varsa, bu araç size düzeltme adımları sağlar.  

![Hata kodu arama aracı](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>Oturum açma etkinliklerini filtreleme

İlk olarak, bildirilen verileri sizin için uygun bir düzeye daraltın. İkinci olarak, tarih alanını kullanarak oturum açma verilerini varsayılan filtre olarak filtreleyin. Azure AD size ayarlayabileceğiniz çok çeşitli ek filtreler sağlar:

![Ekran görüntüsü, Filtre Ekle seçeneğini gösterir.](./media/concept-sign-ins/04.png &quot;Oturum açma etkinliği")

**Istek kimliği** -ILGILENDIĞINIZ isteğin kimliği.

**Kullanıcı** -ilgilendiğiniz kullanıcının adı veya Kullanıcı asıl adı (UPN).

**Uygulama** -hedef uygulamanın adı.
 
**Durum** -ilgilendiğiniz oturum açma durumu:

- Başarılı

- Hata

- Mazsınız


**IP adresi** -kiracınıza bağlanmak için kullanılan cihazın IP adresi.

**Konum** -bağlantının başlatıldığı konum:

- Şehir

- Eyalet/Il

- Ülke/Bölge


**Kaynak** -oturum açma için kullanılan hizmetin adı.


**Kaynak kimliği** -oturum açma için kullanılan hizmetin kimliği.


**İstemci uygulaması** -kiracınıza bağlanmak için kullanılan istemci uygulamanın türü:

![İstemci uygulama filtresi](./media/concept-sign-ins/client-app-filter.png)


|Name|Modern kimlik doğrulaması|Description|
|---|:-:|---|
|Kimliği doğrulanmış SMTP| |POP ve IMAP istemci tarafından e-posta iletileri göndermek için kullanılır.|
|Otomatik bulma| |Outlook ve EAS istemcileri tarafından Exchange Online 'daki posta kutularını bulmak ve bağlamak için kullanılır.|
|Exchange ActiveSync| |Bu filtre, EAS protokolünün denendiği tüm oturum açma girişimlerini gösterir.|
|Tarayıcı|![Mavi onay işareti.](./media/concept-sign-ins/check.png)|Web tarayıcıları kullanan kullanıcıların tüm oturum açma girişimlerini gösterir|
|Exchange ActiveSync| | Exchange Online 'a bağlanmak için Exchange ActiveSync kullanan kullanıcılardan istemci uygulamalarına yönelik tüm oturum açma girişimlerini gösterir|
|Exchange Online PowerShell| |Exchange Online 'a uzak PowerShell ile bağlanmak için kullanılır. Exchange Online PowerShell için temel kimlik doğrulamasını engellerseniz, bağlanmak için Exchange Online PowerShell modülünü kullanmanız gerekir. Yönergeler için bkz. [Multi-Factor Authentication kullanarak Exchange Online PowerShell 'e bağlanma](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange Web Hizmetleri| |Outlook, Mac için Outlook ve üçüncü taraf uygulamalar tarafından kullanılan bir programlama arabirimi.|
|IMAP4| |E-posta almak için IMAP kullanan eski bir posta istemcisi.|
|HTTP üzerinden MAPI| |Outlook 2010 ve üzeri tarafından kullanılır.|
|Mobil uygulamalar ve Masaüstü istemcileri|![Mavi onay işareti.](./media/concept-sign-ins/check.png)|Mobil uygulamalar ve Masaüstü istemcileri kullanan kullanıcılardan gelen tüm oturum açma girişimlerini gösterir.|
|Çevrimdışı adres defteri| |Outlook tarafından indirilen ve kullanılan adres listesi koleksiyonlarının bir kopyası.|
|Her yerde Outlook (HTTP üzerinden RPC)| |Outlook 2016 ve öncesi tarafından kullanılır.|
|Outlook hizmeti| |Windows 10 için posta ve takvim uygulaması tarafından kullanılır.|
|POP3| |POP3 kullanarak e-posta almak için eski bir posta istemcisi.|
|Raporlama Web Hizmetleri| |Exchange Online 'daki rapor verilerini almak için kullanılır.|
|Diğer istemciler| |İstemci uygulamasının dahil edildiği veya bilinmediği kullanıcılardan gelen tüm oturum açma girişimlerini gösterir.|



**İşletim sistemi** -cihazda çalışan ve kiracınızda oturum açmayı kullanmış olan işletim sistemi. 


**Cihaz tarayıcısı** -bağlantı bir tarayıcıdan başlatılmışsa, bu alan tarayıcı adına göre filtrelemenize olanak sağlar.


**BAĞıNTı kimliği** -ETKINLIĞIN bağıntı kimliği.




**Koşullu erişim** -uygulanan koşullu erişim kurallarının durumu

- **Uygulanmadı**: oturum açma sırasında Kullanıcı ve uygulamaya ilke uygulanmaz.

- **Başarılı**: oturum açma sırasında Kullanıcı ve uygulamaya (diğer koşullar olması gerekmez) uygulanan bir veya daha fazla koşullu erişim ilkesi. 

- **Hata**: oturum açma, en az bir koşullu erişim ilkesinin Kullanıcı ve uygulama koşulunu karşıladı ve izin verme denetimleri karşılanmadı ya da erişimi engelle olarak ayarlı değil.









## <a name="download-sign-in-activities"></a>Oturum açma etkinliklerini indirme

En son 250.000 kayıtlarının CSV veya JSON dosyasını oluşturmak için **İndir** seçeneğine tıklayın. Azure portal dışında çalışmak istiyorsanız [, oturum açma verilerini indirme](quickstart-download-sign-in-report.md) ile başlayın.  

![İndir](./media/concept-sign-ins/71.png "İndir")

> [!IMPORTANT]
> İndirebileceğiniz kayıt sayısı [Azure Active Directory rapor bekletme ilkeleri](reference-reports-data-retention.md)tarafından sınırlandırılır.  


## <a name="sign-ins-data-shortcuts"></a>Oturum açma verileri kısayolları

Azure AD ve Azure portal, oturum açma verilerine yönelik ek giriş noktaları sağlar:

- Kimlik güvenliği korumasına genel bakış
- Kullanıcılar
- Gruplar
- Kurumsal uygulamalar

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Kimlik güvenliği koruması 'nda Kullanıcı oturum açma verileri

**Kimlik güvenlik koruması** Genel Bakış sayfasındaki Kullanıcı oturum açma grafiğinde, oturum açma işlemlerinin haftalık toplamaları gösterilmektedir. Zaman aralığı için varsayılan değer 30 gündür.

![Ekran görüntüsü, bir ay boyunca oturum açma işlemlerinin bir grafiğini gösterir.](./media/concept-sign-ins/06.png "Oturum açma etkinliği")

Oturum açma grafiğinde bir güne tıkladığınızda, o güne ait oturum açma etkinliklerinin genel bir açıklamasını alırsınız.

Oturum açma etkinlikleri listesinin her satırında aşağıdakiler gösterilir:

* Kim oturum açtı?
* Oturum açmanın hedefi hangi uygulamaydı?
* Oturum açma durumu nedir?
* Oturum açma MFA durumu nedir?

Bir öğeye tıklayarak oturum açma işlemi hakkında daha fazla bilgi alabilirsiniz:

- Kullanıcı Kimliği
- Kullanıcı
- Kullanıcı adı
- Uygulama Kimliği
- Uygulama
- İstemci
- Konum
- IP Adresi
- Tarih
- MFA Gerekli
- Oturum açma durumu

> [!NOTE]
> IP adresleri, bir IP adresi arasında kesin bir bağlantı olmaması ve bu adrese sahip bilgisayarın fiziksel olarak bulunduğu bir şekilde verilir. IP adreslerini eşleme, Mobil sağlayıcıların ve VPN 'lerin, istemci cihazının gerçekten kullanıldığı yerden çok büyük olan merkezi havuzlardan IP adresleri yayınlabildiği gerçeyle karmaşıktır. Şu anda Azure AD raporlarında, IP adresi fiziksel bir konuma dönüştürülürken izlemeler, kayıt defteri verileri, ters aramalar ve diğer bilgiler temel alınarak en iyi çaba vardır.

**Kullanıcılar** sayfasında, **Etkinlik** bölümündeki **Oturum açma** öğesine tıklayarak tüm kullanıcı oturum açma işlemlerine eksiksiz bir genel bakış elde edebilirsiniz.

![Ekran görüntüsü, oturum açma işlemlerini seçebileceğiniz etkinlik bölümünü gösterir.](./media/concept-sign-ins/08.png "Oturum açma etkinliği")

## <a name="usage-of-managed-applications"></a>Yönetilen uygulamaların kullanımı

Oturum açma bilgilerinizin uygulama odaklı bir görünümüyle aşağıdakiler gibi sorular yanıtlanabilir:

* Uygulamalarımı kimler kullanıyor?
* Kuruluşunuzdaki en iyi üç uygulama nelerdir?
* En yeni Uygulamam nasıl yapıyor?

Bu verilere yönelik giriş noktası, kuruluşunuzdaki en üst üç uygulama olur. Veriler, **Kurumsal uygulamalar** altındaki **genel bakış** bölümünde yer alan son 30 gün içinde bulunur.

![Ekran görüntüsü, genel bakış ' ı seçebileceğiniz yeri gösterir.](./media/concept-sign-ins/10.png "Oturum açma etkinliği")

Uygulama kullanımı grafikleri, belirli bir dönemde en iyi üç uygulama için oturum açma işlemlerinin haftalık toplamalarını toplamalar. Zaman dönemi için varsayılan süre 30 gündür.

![Ekran görüntüsü, bir aylık dönem için uygulama kullanımını gösterir.](./media/concept-sign-ins/graph-chart.png "Oturum açma etkinliği")

İsterseniz belirli bir uygulamaya odaklanabilirsiniz.

![Raporlama](./media/concept-sign-ins/single-app-usage-graph.png "Raporlama")

Uygulama kullanımı grafiğinde bir güne tıkladığınızda, oturum açma etkinliklerinin ayrıntılı bir listesini alırsınız.

**Oturum açma işlemleri** seçeneği, size tüm uygulamalarınıza ait oturum açma olaylarına genel bir bakış sunar.

## <a name="microsoft-365-activity-logs"></a>Etkinlik günlüklerini Microsoft 365

[Microsoft 365 Yönetim merkezinden](/office365/admin/admin-overview/about-the-admin-center)Microsoft 365 etkinlik günlüklerini görüntüleyebilirsiniz. Microsoft 365 etkinlik ve Azure AD etkinlik günlüklerinin çok sayıda dizin kaynağını paylaştığı noktayı göz önünde bulundurun. Yalnızca Microsoft 365 Yönetim Merkezi Microsoft 365 etkinlik günlüklerinin tam görünümünü sağlar. 

Ayrıca, [Office 365 Yönetim API 'lerini](/office/office-365-management-api/office-365-management-apis-overview)kullanarak Microsoft 365 etkinlik günlüklerine programlı bir şekilde erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma Etkinliği raporu hata kodları](reference-sign-ins-error-codes.md)
* [Azure AD veri saklama ilkeleri](reference-reports-data-retention.md)
* [Azure AD rapor gecikmeleri](reference-reports-latencies.md)