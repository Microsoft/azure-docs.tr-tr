---
title: Azure Active Directory portalındaki oturum açma etkinlik raporları | Microsoft Docs
description: Azure Active Directory portalındaki oturum açma etkinlik raporlarına giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 778353621491f912d3237900785e6dee17bf975e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014484"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory portalındaki oturum açma etkinlik raporları

Azure Active Directory (Azure AD) içindeki raporlama mimarisi aşağıdaki bileşenlerden oluşur:

- **Etkinlik** 
    - **Oturum açma** bilgileri – yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgiler.
    - **Denetim günlükleri - denetim** [günlükleri](concept-audit-logs.md) , kullanıcılar ve Grup Yönetimi, yönetilen uygulamalar ve Dizin etkinlikleri hakkında sistem etkinliği bilgilerini sağlar.
- **Güvenlik** 
    - **Riskli oturum** açma işlemleri- [riskli oturum](concept-risky-sign-ins.md) açma, Kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilen oturum açma girişimine yönelik bir göstergedir.
    - **Risk için Işaretlenen kullanıcılar** - [riskli bir Kullanıcı](concept-user-at-risk.md) , tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir.

Bu makale, oturum açma raporuna genel bir bakış sunar.

## <a name="prerequisites"></a>Önkoşullar

### <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?

* Güvenlik Yöneticisi, güvenlik okuyucu ve rapor okuyucu rollerinin kullanıcıları
* Genel Yöneticiler
* Tüm kullanıcılar (yönetici olmayan) kendi oturum açma etkinliklerine erişebilirler 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Oturum açma etkinliğine erişebilmek için hangi Azure AD lisansınızın olması gerekir?

* Tüm oturum açma etkinliği raporunu görmek için kiracınızın kendisiyle ilişkili bir Azure AD Premium lisansı olması gerekir. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama. Yükseltmeden önce veri etkinliği olmayan bir Premium lisansa yükselttikten sonra, verilerin raporlarda gösterilmesi birkaç gün sürer.

## <a name="sign-ins-report"></a>Oturum açma işlemleri raporu

Kullanıcı oturum açma işlemleri raporu, aşağıdaki soruların yanıtlarını sağlar:

* Belirli bir kullanıcının oturum açma düzeni nedir?
* Bir hafta içerisinde kaç kullanıcı oturum açtı?
* Bu açılan oturumların durumu nedir?

[Azure Portal](https://portal.azure.com)başlatın. Oturum açma raporuna erişmek için oturum **açma**işlemlerini seçin, izlemeye devam edin **.** Bazı oturum açma kayıtlarının portalda gösterilmesi iki saate kadar sürebilir.

![Oturum açma etkinliği](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Oturum açma etkinliği")

> [!IMPORTANT]
> Oturum açma raporu yalnızca **etkileşimli** oturum açma işlemlerini, diğer bir deyişle kullanıcının Kullanıcı adı ve parolasını kullanarak el ile oturum açtığı oturum açma işlemlerini görüntüler. Hizmetten hizmete kimlik doğrulaması gibi etkileşimli olmayan oturum açma işlemleri raporda gösterilmez. 

Oturum açma günlüklerinin aşağıdakileri gösteren bir varsayılan liste görünümü vardır:

- Oturum açma tarihi
- İlgili kullanıcı
- Kullanıcının oturum açmış olduğu uygulama
- Oturum açma durumu
- Risk algılama durumu
- Çok faktörlü kimlik doğrulaması (MFA) gereksiniminin durumu

![Oturum açma etkinliği](./media/concept-sign-ins/sign-in-activity.png "Oturum açma etkinliği")

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Oturum açma etkinliği](./media/concept-sign-ins/19.png "Oturum açma etkinliği")

Ek alanları görüntüler veya zaten görüntülenen alanları kaldırır.

![Oturum açma etkinliği](./media/concept-sign-ins/02.png "Oturum açma etkinliği")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Oturum açma etkinliği](./media/concept-sign-ins/basic-sign-in.png "Oturum açma etkinliği")

> [!NOTE]
> Müşteriler artık tüm oturum açma raporları aracılığıyla koşullu erişim ilkelerini giderebilirler. Bir oturum açma kaydı için **koşullu erişim** sekmesine tıkladığınızda müşteriler koşullu erişim durumunu gözden geçirebilir ve oturum açma için uygulanan ilkelerin ayrıntılarını ve her bir ilkenin sonucunu görebilir.
> Daha fazla bilgi için, [tüm oturum açma IŞLEMLERININ CA bilgileri hakkında sık sorulan sorular](reports-faq.md#conditional-access)bölümüne bakın.



## <a name="filter-sign-in-activities"></a>Oturum açma etkinliklerini filtreleme

İlk olarak, bildirilen verileri sizin için uygun bir düzeye daraltın. İkinci olarak, tarih alanını kullanarak oturum açma verilerini varsayılan filtre olarak filtreleyin. Azure AD size ayarlayabileceğiniz çok çeşitli ek filtreler sağlar.

![Oturum açma etkinliği](./media/concept-sign-ins/04.png "Oturum açma etkinliği")

**Kullanıcı** filtresi, önem verdiğiniz kullanıcının adını veya kullanıcı asıl adını (UPN) belirtmenize imkan tanır.

**Uygulama** filtresi, önem verdiğiniz uygulamanın adını belirtmenize imkan tanır.

**Oturum açma durumu** filtresi aşağıdakilerden birini seçmenize imkan tanır:

- Tümü
- Başarılı
- Hata

**Koşullu erişim** filtresi, oturum açma için CA ilkesi durumunu seçmenizi sağlar:

- Tümü
- Uygulanmadı
- Başarılı
- Hata

**Tarih** filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar.  
Olası değerler şunlardır:

- Bir ay
- 7 gün
- 24 saat
- Özel zaman aralığı

Özel bir zaman çerçevesi seçerken başlangıç ve bitiş zamanını yapılandırabilirsiniz.

Oturum açma görünümüne başka alanlar eklerseniz bu alanlar filtre listesine otomatik olarak eklenir. Örneğin, listenize **İstemci Uygulama** alanını ekleyerek, aşağıdaki filtreleri ayarlamanıza olanak tanıyan başka bir filtre seçeneği de alırsınız:  
![Oturum açma etkinliği](./media/concept-sign-ins/12.png "Oturum açma etkinliği")

- **Tarayıcı**  
    Bu filtre, tarayıcı akışları kullanılarak oturum açma girişimlerinin denendiği tüm olayları gösterir.
- **Exchange ActiveSync (desteklenen)**  
    Bu filtre, Exchange ActiveSync (EAS) protokolünün iOS, Android ve Windows Phone gibi desteklenen platformlarda denendiği tüm oturum açma girişimlerini gösterir.
- **Exchange ActiveSync (desteklenmiyor)**  
    Bu filtre, EAS protokolünün Linux distros gibi desteklenmeyen platformlardan denendiği tüm oturum açma girişimlerini gösterir.
- **Mobile Apps ve Masaüstü istemcileri** Filtre, tarayıcı akışlarını kullanmayan tüm oturum açma girişimlerini gösterir. Örneğin, herhangi bir protokol veya Windows veya MacOS 'ta Office gibi masaüstü istemci uygulamalarından yararlanarak herhangi bir platformda bulunan mobil uygulamalar.
  
- **Diğer istemciler**
    - **IMAP**  
        E-posta almak için IMAP kullanan eski bir posta istemcisi.
    - **HATASı**  
        ADAL etkin olduğu ve MAPI kullanıldığı Office 2013.
    - **Eski Ofis istemcileri**  
        Office 2013, ADAL 'nin etkinleştirilmediği ve MAPI kullandığı ve ADAL 'nin devre dışı bırakıldığı Office 2016 ' nin varsayılan yapılandırmasında.
    - **CAĞıMıZ**  
        POP3 kullanarak e-posta almak için eski bir posta istemcisi.
    - **SMTP**  
        E-posta göndermek için SMTP kullanan eski bir posta istemcisi.

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

![Oturum açma etkinliği](./media/concept-sign-ins/06.png "Oturum açma etkinliği")

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
- IP adresi
- Tarih
- MFA Gerekli
- Oturum açma durumu

> [!NOTE]
> IP adresleri, bir IP adresi arasında kesin bir bağlantı olmaması ve bu adrese sahip bilgisayarın fiziksel olarak bulunduğu bir şekilde verilir. IP adreslerini eşleme, Mobil sağlayıcıların ve VPN 'lerin, istemci cihazının gerçekten kullanıldığı yerden çok büyük olan merkezi havuzlardan IP adresleri yayınlabildiği gerçeyle karmaşıktır. Şu anda Azure AD raporlarında, IP adresi fiziksel bir konuma dönüştürülürken izlemeler, kayıt defteri verileri, ters aramalar ve diğer bilgiler temel alınarak en iyi çaba vardır.

**Kullanıcılar** sayfasında, **Etkinlik** bölümündeki **Oturum açma** öğesine tıklayarak tüm kullanıcı oturum açma işlemlerine eksiksiz bir genel bakış elde edebilirsiniz.

![Oturum açma etkinliği](./media/concept-sign-ins/08.png "Oturum açma etkinliği")

## <a name="usage-of-managed-applications"></a>Yönetilen uygulamaların kullanımı

Oturum açma bilgilerinizin uygulama odaklı bir görünümüyle aşağıdakiler gibi sorular yanıtlanabilir:

* Uygulamalarımı kimler kullanıyor?
* Kuruluşunuzdaki en iyi üç uygulama nelerdir?
* En yeni Uygulamam nasıl yapıyor?

Bu verilere yönelik giriş noktası, kuruluşunuzdaki en üst üç uygulama olur. Veriler, **Kurumsal uygulamalar**altındaki **genel bakış** bölümünde yer alan son 30 gün içinde bulunur.

![Oturum açma etkinliği](./media/concept-sign-ins/10.png "Oturum açma etkinliği")

Uygulama kullanımı grafikleri, belirli bir dönemde en iyi üç uygulama için oturum açma işlemlerinin haftalık toplamalarını toplamalar. Zaman dönemi için varsayılan süre 30 gündür.

![Oturum açma etkinliği](./media/concept-sign-ins/graph-chart.png "Oturum açma etkinliği")

İsterseniz belirli bir uygulamaya odaklanabilirsiniz.

![Raporlama](./media/concept-sign-ins/single-app-usage-graph.png "Raporlama")

Uygulama kullanımı grafiğinde bir güne tıkladığınızda, oturum açma etkinliklerinin ayrıntılı bir listesini alırsınız.

**Oturum açma işlemleri** seçeneği, size tüm uygulamalarınıza ait oturum açma olaylarına genel bir bakış sunar.

## <a name="office-365-activity-logs"></a>Office 365 etkinlik günlükleri

[Microsoft 365 Yönetim merkezinden](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)Office 365 etkinlik günlüklerini görüntüleyebilirsiniz. Office 365 etkinliği ve Azure AD etkinlik günlüklerinin, dizin kaynaklarının önemli bir sayısını paylaştığı noktayı göz önünde bulundurun. Yalnızca Microsoft 365 Yönetim Merkezi, Office 365 etkinlik günlüklerinin tam görünümünü sağlar. 

Office [365 Yönetim API 'lerini](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)kullanarak da Office 365 etkinlik günlüklerine programlı bir şekilde erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma Etkinliği raporu hata kodları](reference-sign-ins-error-codes.md)
* [Azure AD veri saklama ilkeleri](reference-reports-data-retention.md)
* [Azure AD rapor gecikmeleri](reference-reports-latencies.md)

