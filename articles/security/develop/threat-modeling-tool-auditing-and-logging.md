---
title: Denetleme ve günlüğe kaydetme-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool kullanıma sunulan tehditler için azaltmaları
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728407"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Güvenlik çerçevesi: Denetleme ve günlüğe kaydetme | Karşı 

| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Çözümünüzde hassas varlıkları tanımla ve değişiklik denetimini Uygula](#sensitive-entities)</li></ul> |
| **Web uygulaması** | <ul><li>[Uygulama üzerinde denetim ve günlük uygulanmasını sağlayın](#auditing)</li><li>[Günlük dönüşü ve ayrımı olduğundan emin olun](#log-rotation)</li><li>[Uygulamanın hassas kullanıcı verilerini günlüğe içermediğinden emin olun](#log-sensitive-data)</li><li>[Denetim ve günlük dosyalarının kısıtlı erişime sahip olduğundan emin olun](#log-restricted-access)</li><li>[Kullanıcı yönetimi olaylarının günlüğe kaydedildiğinden emin olun](#user-management)</li><li>[Sistemin kötüye kullanılmasına karşı savunmaları olmadığından emin olun](#inbuilt-defenses)</li><li>[Azure App Service'te web apps için tanılama günlüğünü etkinleştirme](#diagnostics-logging)</li></ul> |
| **Veritabanı** | <ul><li>[SQL Server oturum açma denetiminin etkinleştirildiğinden emin olun](#identify-sensitive-entities)</li><li>[Azure SQL 'de tehdit algılamayı etkinleştirme](#threat-detection)</li></ul> |
| **Azure Depolama** | <ul><li>[Azure Storage erişimini denetlemek için Azure Depolama Analizi kullanma](#analytics)</li></ul> |
| **WCF** | <ul><li>[Yeterli günlüğü uygulama](#sufficient-logging)</li><li>[Yeterli denetim hatası Işleme uygulama](#audit-failure-handling)</li></ul> |
| **Web API** | <ul><li>[Web API 'sinde denetim ve günlük uygulanmasını sağlayın](#logging-web-api)</li></ul> |
| **IoT alan ağ geçidi** | <ul><li>[Alan ağ geçidinde uygun denetim ve günlüğün uygulanmasını sağlayın](#logging-field-gateway)</li></ul> |
| **IoT bulut ağ geçidi** | <ul><li>[Bulut ağ geçidinde uygun denetim ve günlüğün zorlandığından emin olun](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Çözümünüzde hassas varlıkları tanımla ve değişiklik denetimini Uygula

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | Çözümünüzde hassas verileri içeren varlıkları ve bu varlıklar ve alanlara değişiklik denetimi uygulamayı tanımla |

## <a id="auditing"></a>Uygulama üzerinde denetim ve günlük uygulanmasını sağlayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | Tüm bileşenlerde denetim ve günlüğe kaydetmeyi etkinleştirin. Denetim günlükleri kullanıcı bağlamını yakalemelidir. Tüm önemli olayları belirleyip bu olayları günlüğe kaydedin. Merkezi günlük uygulama |

## <a id="log-rotation"></a>Günlük dönüşü ve ayrımı olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | <p>Günlük döndürme, sistem yönetiminde kullanılan ve oluşturulan günlük dosyalarının arşivlendiği otomatikleştirilmiş bir işlemdir. Büyük uygulamaları çalıştıran sunucular genellikle her isteği günlüğe kaydeder: bulkulu günlüklerin yanında, günlük döndürme, son olayların analizine izin verirken günlüklerin toplam boyutunu sınırlamak için bir yoldur. </p><p>Günlük ayrımı temel olarak, bir hizmet reddi saldırısı veya uygulamanızın performansını daha eski sürüme düşürme amacıyla, işletim sistemi/uygulamanızın üzerinde çalıştığı farklı bir bölüme günlük dosyaları depolamanız gerektiği anlamına gelir</p>|

## <a id="log-sensitive-data"></a>Uygulamanın hassas kullanıcı verilerini günlüğe içermediğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | <p>Bir kullanıcının sitenize gönderdiği gizli verileri günlüğe kaydetme izniniz olup olmadığını denetleyin. Kasıtlı olarak günlüğe kaydetme işleminin yanı sıra tasarım sorunlarından kaynaklanan yan etkileri da kontrol edin. Hassas verilere örnek olarak şunlar verilebilir:</p><ul><li>Kullanıcı Kimlik Bilgileri</li><li>Sosyal güvenlik numarası veya diğer tanımlama bilgileri</li><li>Kredi kartı numaraları veya diğer finansal bilgiler</li><li>Sağlık bilgileri</li><li>Şifrelenmiş bilgilerin şifresini çözmek için kullanılabilecek özel anahtarlar veya diğer veriler</li><li>Uygulamaya daha etkin bir şekilde saldırmak için kullanılabilen sistem veya uygulama bilgileri</li></ul>|

## <a id="log-restricted-access"></a>Denetim ve günlük dosyalarının kısıtlı erişime sahip olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | <p>Günlük dosyalarına erişim haklarının uygun şekilde ayarlandığından emin olun. Uygulama hesapları salt yazılır erişime ve işleçlere sahip olmalıdır ve destek personeli gerektiğinde salt okuma erişimine sahip olmalıdır.</p><p>Yönetici hesapları, tam erişimi olması gereken tek hesaplardır. Doğru kısıtlandıklarından emin olmak için günlük dosyalarında Windows ACL 'sini kontrol edin:</p><ul><li>Uygulama hesaplarının salt yazılır erişimi olmalıdır</li><li>Operatörler ve destek personeli gerektiğinde salt okuma erişimine sahip olmalıdır</li><li>Yöneticiler, tam erişimi olması gereken tek hesaplardır</li></ul>|

## <a id="user-management"></a>Kullanıcı yönetimi olaylarının günlüğe kaydedildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | <p>Uygulamanın başarılı ve başarısız Kullanıcı oturum açmaları, parola sıfırlama, parola değişiklikleri, hesap kilitleme, Kullanıcı kaydı gibi Kullanıcı yönetimi olaylarını izlemediğinden emin olun. Bunun yapılması, potansiyel olarak şüpheli davranışları algılamaya ve bunlara tepki sağlanmasına yardımcı olur. İşlem verilerinin toplanmasını da sağlar; Örneğin, uygulamaya kimlerin eriştiğini izlemek için</p>|

## <a id="inbuilt-defenses"></a>Sistemin kötüye kullanılmasına karşı savunmaları olmadığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları**                   | <p>Uygulamanın kötüye kullanılması durumunda güvenlik özel durumu oluşturan denetimler yerinde olmalıdır. Örneğin, giriş doğrulama gerçekleşiyorsa ve bir saldırgan, Regex ile eşleşmeyen kötü amaçlı kod eklemeye çalışırsa, sistem kötüye kullanımı için bir ifade olabilecek bir güvenlik özel durumu oluşabilir</p><p>Örneğin, güvenlik özel durumlarının günlüğe kaydedilmesini ve aşağıdaki sorunlar için gerçekleştirilecek eylemleri yapmanız önerilir:</p><ul><li>Giriş doğrulaması</li><li>CSRF ihlalleri</li><li>Deneme yanılma (her kaynak için Kullanıcı başına istek sayısı üst sınırı)</li><li>Karşıya dosya yükleme ihlalleri</li><ul>|

## <a id="diagnostics-logging"></a>Azure App Service Web Apps için tanılama günlüğünü etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | EnvironmentType-Azure |
| **Başvur**              | Yok  |
| **Adımları** | <p>Azure, bir App Service web uygulamasının hatalarını ayıklamaya yardımcı olmak üzere yerleşik tanılama sağlar. Ayrıca API uygulamaları ve mobil uygulamalar için de geçerlidir. App Service Web Apps, hem Web sunucusundan hem de Web uygulamasından günlüğe bilgi kaydetmeye yönelik Tanılama işlevleri sağlar.</p><p>Bunlar, Web sunucusu tanılama ve Uygulama Tanılama 'ya mantıksal olarak ayrılmıştır</p>|

## <a id="identify-sensitive-entities"></a>SQL Server oturum açma denetiminin etkinleştirildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Oturum açma denetimini yapılandırma](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Adımları** | <p>Parola tahmin saldırılarını algılamak/onaylamak için veritabanı sunucusu oturum açma denetiminin etkinleştirilmesi gerekir. Başarısız oturum açma girişimlerini yakalamak önemlidir. Hem başarılı hem de başarısız oturum açma girişimlerinin yakalanması, Forli araştırmalar sırasında ek avantaj sağlar</p>|

## <a id="threat-detection"></a>Azure SQL 'de tehdit algılamayı etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | SQL Azure |
| **Öznitelikler**              | SQL sürümü-V12 |
| **Başvur**              | [SQL veritabanı tehdit algılamayı kullanmaya başlama](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Adımları** |<p>Tehdit algılama, veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar. Müşterilerin anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamasına ve yanıt vermesine olanak tanıyan yeni bir güvenlik katmanı sağlar.</p><p>Kullanıcılar, Azure SQL veritabanı denetimini kullanarak kuşkulu olayları keşfedebilir, bu da veritabanındaki verilere erişme, ihlalin veya açıktan yararlanma denemesinden kaynaklanan verileri tespit edebilir.</p><p>Tehdit algılama, bir güvenlik uzmanı olması veya gelişmiş güvenlik izleme sistemlerini yönetmek zorunda kalmadan, olası tehditleri veritabanına yönelik olarak ele almanızı kolaylaştırır</p>|

## <a id="analytics"></a>Azure Storage erişimini denetlemek için Azure Depolama Analizi kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Storage | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok |
| **Başvur**              | [Yetkilendirme türünü izlemek için Depolama Analizi kullanma](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Adımları** | <p>Her bir depolama hesabı için, Azure Depolama Analizi günlüğe kaydetme ve ölçüm verilerini depolama işlemleri gerçekleştirebilir. Depolama Analizi günlükleri, depolama erişimi olduğunda birisi tarafından kullanılan kimlik doğrulama yöntemi gibi önemli bilgileri sağlar.</p><p>Bu, depolamaya erişimi sıkı bir şekilde koruyarak yararlı olabilir. Örneğin, BLOB depolama alanında tüm kapsayıcıları özel olarak ayarlayabilir ve uygulamalarınızın tamamında bir SAS hizmetinin kullanımını uygulayabilirsiniz. Daha sonra, bloblarınızın depolama hesabı anahtarları kullanılarak erişilip erişilmeyeceğini, güvenliğin ihlal olabileceğini veya Blobların genel olduğunu, ancak bunların olmaması gerektiğini görmek için günlükleri düzenli olarak kontrol edebilirsiniz.</p>|

## <a id="sufficient-logging"></a>Yeterli günlüğü uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | .NET Framework |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Adımları** | <p>Bir güvenlik olayından sonra uygun bir denetim izinin olmaması, yasaklama çabalarına engel olabilir. Windows Communication Foundation (WCF), başarılı ve/veya başarısız kimlik doğrulama girişimlerini günlüğe kaydetme olanağı sunar.</p><p>Başarısız kimlik doğrulama denemeleri, olası deneme yanılma saldırılarının yöneticilerini uyarabilir. Benzer şekilde, başarılı kimlik doğrulama olaylarını günlüğe kaydetme, meşru bir hesap tehlikeye düştüğünde yararlı bir denetim izi sağlayabilir. WCF 'nin hizmet güvenliği denetim özelliğini etkinleştir |

### <a name="example"></a>Örnek
Aşağıda, denetim etkin olan örnek bir yapılandırma verilmiştir
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Yeterli denetim hatası Işleme uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | .NET Framework |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Adımları** | <p>Geliştirilmiş çözüm, bir denetim günlüğüne yazamazsa bir özel durum oluşturmamakta değil. WCF bir denetim günlüğüne yazamayan bir özel durum oluşturmayacak şekilde yapılandırıldıysa, programa hata bildirilmez ve kritik güvenlik olaylarının denetimi gerçekleşmeyebilir.</p>|

### <a name="example"></a>Örnek
Aşağıdaki `<behavior/>` WCF yapılandırma dosyasının öğesi, WCF bir denetim günlüğüne yazamazsa, WCF 'yi uygulamayı bilgilendirmez.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
WCF 'yi, bir denetim günlüğüne yazamadığında programa bildirmek için yapılandırın. Programın, denetim izlerini yapılmadığından emin olmak için alternatif bir bildirim şeması olması gerekir. 

## <a id="logging-web-api"></a>Web API 'sinde denetim ve günlük uygulanmasını sağlayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API'si | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Web API 'Lerinde denetim ve günlüğe kaydetmeyi etkinleştirin. Denetim günlükleri kullanıcı bağlamını yakalemelidir. Tüm önemli olayları belirleyip bu olayları günlüğe kaydedin. Merkezi günlük uygulama |

## <a id="logging-field-gateway"></a>Alan ağ geçidinde uygun denetim ve günlüğün uygulanmasını sağlayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT alan ağ geçidi | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Bir alan ağ geçidine birden çok cihaz bağlandığında, tek tek cihazlar için bağlantı girişimlerinin ve kimlik doğrulama durumunun (başarı veya başarısızlık), alan ağ geçidinde günlüğe kaydedildiğinden ve korunduğundan emin olun.</p><p>Ayrıca, alan ağ geçidinin tek tek cihazların IoT Hub kimlik bilgilerini sürdürme durumlarında, bu kimlik bilgileri alındığında denetimin gerçekleştirildiğinden emin olun. Uzun süreli saklama için günlükleri Azure IoT Hub/depolama 'ya düzenli aralıklarla yüklemek için bir işlem geliştirin.</p> |

## <a id="logging-cloud-gateway"></a>Bulut ağ geçidinde uygun denetim ve günlüğün zorlandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [IoT Hub işlemler izlemeye giriş](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Adımları** | <p>IoT Hub Işlemleri Izlemeye göre toplanan denetim verilerinin toplanması ve depolanması için tasarım. Aşağıdaki izleme kategorilerini etkinleştirin:</p><ul><li>Cihaz kimliği işlemleri</li><li>Cihazdan buluta iletişimler</li><li>Buluttan cihaza iletişimler</li><li>Bağlantılar</li><li>Dosya yüklemeleri</li></ul>|