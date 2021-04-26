---
title: Hassas veri-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool hassas veri azaltma hakkında bilgi edinin. Bkz. risk azaltma bilgileri ve kod örneklerini görüntüleme.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0bcbe35fc6d9f104325bec8a3404ad57a6376cf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518133"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Güvenlik çerçevesi: hassas veriler | Karşı 
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Makine güven sınırı** | <ul><li>[Hassas bilgiler içeriyorsa ikili dosyaların gizlenmiş olduğundan emin olun](#binaries-info)</li><li>[Kullanıcıya özgü gizli verileri korumak için şifrelenmiş dosya sistemi (EFS) kullanmayı düşünün](#efs-user)</li><li>[Dosya sisteminde uygulama tarafından depolanan hassas verilerin şifrelendiğinden emin olun](#filesystem)</li></ul> | 
| **Web uygulaması** | <ul><li>[Gizli içeriğin tarayıcıda önbelleğe alınmadığından emin olun](#cache-browser)</li><li>[Gizli veriler içeren Web uygulamasının yapılandırma dosyalarının bölümlerini şifreleyin](#encrypt-data)</li><li>[Gizli formlarda ve girişlerde Otomatik Tamamla HTML özniteliğini açıkça devre dışı bırak](#autocomplete-input)</li><li>[Kullanıcı ekranında görüntülenen hassas verilerin maskelenmiş olduğundan emin olun](#data-mask)</li></ul> | 
| **Veritabanı** | <ul><li>[Ayrıcalıklı veri pozlaması olmayan kullanıcıları sınırlamak için dinamik veri maskeleme uygulama](#dynamic-users)</li><li>[Parolaların sallaştırılmış karma biçiminde depolandığından emin olun](#salted-hash)</li><li>[Veritabanı sütunlarındaki gizli verilerin şifrelendiğinden emin olun](#db-encrypted)</li><li>[Veritabanı düzeyinde şifrelemenin (TDE) etkinleştirildiğinden emin olun](#tde-enabled)</li><li>[Veritabanı yedeklerinin şifrelendiğinden emin olun](#backup)</li></ul> | 
| **Web API** | <ul><li>[Web API 'siyle ilgili hassas verilerin tarayıcının depolama alanında depolanmadığından emin olun](#api-browser)</li></ul> | 
| Azure belge DB | <ul><li>[Azure Cosmos DB depolanan hassas verileri şifreleyin](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM güven sınırı** | <ul><li>[Sanal makineler tarafından kullanılan diskleri şifrelemek için Azure disk şifrelemesini kullanma](#disk-vm)</li></ul> | 
| **Service Fabric güven sınırı** | <ul><li>[Service Fabric uygulamalarında gizli dizileri şifreleyin](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Güvenlik modellemesini gerçekleştirin ve gereken yerlerde Iş birimleri/takımlar kullanın](#modeling-teams)</li><li>[Kritik varlıklarda özelliği paylaşmak için erişimi en aza indirin](#entities)</li><li>[Dynamics CRM Share özelliği ve iyi güvenlik uygulamalarıyla ilişkili riskler üzerinde Kullanıcıları eğitme](#good-practices)</li><li>[Özel durum yönetiminde yapılandırma ayrıntılarını gösteren bir geliştirme standartları kuralı ekleyin](#exception-mgmt)</li></ul> | 
| **Azure Depolama** | <ul><li>[Bekleyen veriler için Azure Depolama Hizmeti Şifrelemesi (SSE) kullanma (Önizleme)](#sse-preview)</li><li>[Hassas verileri Azure depolama 'da depolamak için Client-Side şifrelemeyi kullanma](#client-storage)</li></ul> | 
| **Mobil Istemci** | <ul><li>[Telefon yerel depolama alanına yazılan hassas veya PII verilerini şifreleyin](#pii-phones)</li><li>[Son kullanıcılara dağıtmadan önce oluşturulan ikilileri gizleme](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ClientCredentialType 'ı sertifika veya Windows olarak ayarla](#cert)</li><li>[WCF-güvenlik modu etkin değil](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Hassas bilgiler içeriyorsa ikili dosyaların gizlenmiş olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | , Geri çevrilmemelidir, önemli iş mantığı gibi hassas bilgiler içeriyorsa ikili dosyaların belirsizleştirilmiş olduğundan emin olun. Bu, derlemelerin ters mühendislik işlemini durdurmaktır. Gibi Araçlar `CryptoObfuscator` da bu amaçla kullanılabilir. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Kullanıcıya özgü gizli verileri korumak için şifrelenmiş dosya sistemi (EFS) kullanmayı düşünün

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Şifrelenmiş dosya sistemi (EFS) kullanmayı göz önünde bulundurun. Bu, kullanıcıya özgü gizli verileri, bilgisayara fiziksel erişimi olan zarflara karşı korumak için kullanılır. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Dosya sisteminde uygulama tarafından depolanan hassas verilerin şifrelendiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | EFS zorlanmıyorsa, uygulama tarafından dosya sisteminde depolanan hassas verilerin şifrelendiğinden emin olun (ör. DPAPI kullanılarak) |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Gizli içeriğin tarayıcıda önbelleğe alınmadığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel, Web Forms, MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Tarayıcılar, önbelleğe alma ve geçmiş amaçları için bilgileri saklayabilir. Bu önbelleğe alınan dosyalar, Internet Explorer durumunda Temporary Internet Files klasörü gibi bir klasörde depolanır. Bu sayfalara yeniden bahsedildiğinde, tarayıcı onları önbellekten görüntüler. Gizli bilgiler kullanıcıya (adresler, kredi kartı ayrıntıları, sosyal güvenlik numarası veya Kullanıcı adı gibi) görüntüleniyorsa, bu bilgiler tarayıcının önbelleğinde depolanabilir ve bu nedenle tarayıcının önbelleğini inceleyerek veya tarayıcının "geri" düğmesine basılarak alınabilir. Cache-Control yanıt üst bilgisi değerini tüm sayfalar için "No-Store" olarak ayarlayın. |

### <a name="example"></a>Örnek
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-store" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Örnek
Bu, bir filtre aracılığıyla uygulanabilir. Aşağıdaki örnek kullanılabilir: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Gizli veriler içeren Web uygulamasının yapılandırma dosyalarının bölümlerini şifreleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Nasıl yapılır: ASP.NET 2,0 ' de yapılandırma bölümlerini](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [bir korumalı yapılandırma sağlayıcısı belirterek](/previous-versions/68ze1hb2(v=vs.140)), [uygulama gizli dizilerini korumak için Azure Key Vault kullanarak](/azure/architecture/multitenant-identity/web-api) şifreleme |
| **Adımlar** | Web.config, appsettings.jsgibi yapılandırma dosyaları genellikle kullanıcı adları, parolalar, veritabanı bağlantı dizeleri ve şifreleme anahtarları dahil olmak üzere hassas bilgileri tutmak için kullanılır. Bu bilgileri korumayın, uygulamanız saldırganların veya kötü niyetli kullanıcıların hesap Kullanıcı adları ve parolalar, veritabanı adları ve sunucu adları gibi hassas bilgileri elde etmesine açıktır. Dağıtım türüne (Azure/on-Prem) bağlı olarak, yapılandırma dosyalarının hassas bölümlerini, DPAPI veya Azure Key Vault gibi hizmetleri kullanarak şifreleyin. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Gizli formlarda ve girişlerde Otomatik Tamamla HTML özniteliğini açıkça devre dışı bırak

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [MSDN: AutoComplete özniteliği](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [HTML 'de otomatik tamamlama özelliğini kullanarak](/previous-versions/windows/internet-explorer/ie-developer/) [HTML temizleme güvenlik açığı](/security-updates/SecurityBulletins/2010/ms10-071), [otomatik tamamlama., tekrar?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Adımlar** | AutoComplete özniteliği bir formda Otomatik Tamamla 'nın açık veya kapalı olması gerekip gerekmediğini belirtir. Otomatik tamamlama açık olduğunda tarayıcı, kullanıcının daha önce girdiği değerlere göre değerleri otomatik olarak tamamlar. Örneğin, bir forma yeni bir ad ve parola girildiğinde ve form gönderildiğinde, tarayıcı parolanın kaydedilip kaydedilmediğini sorar. Daha sonra form görüntülendiğinde, ad ve parola otomatik olarak doldurulur veya ad girildiğinde tamamlanır. Yerel erişimi olan bir saldırgan, tarayıcı önbelleğinden şifresiz metin parolasını alabilir. Varsayılan olarak otomatik tamamlama etkindir ve açıkça devre dışı bırakılması gerekir. |

### <a name="example"></a>Örnek
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Kullanıcı ekranında görüntülenen hassas verilerin maskelenmiş olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Parola, kredi kartı numaraları, SSN vb. gibi hassas veriler ekranda görüntülendiğinde maskelenmelidir. Bu, yetkisiz personelin verilere erişmesini önlemektir (ör., kama bakıyor, destek personeli, SSN Kullanıcı sayısını görüntülüyor). Bu veri öğelerinin düz metinde görünür olmadığından ve uygun şekilde maskelendiğinden emin olun. Bu, giriş olarak kabul edilirken ele alınması gerekir (ör. Giriş türü = "Password") ve ekranda geri görüntüleme (ör. kredi kartı numarasının yalnızca son 4 basamağını görüntüleyin). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Ayrıcalıklı veri pozlaması olmayan kullanıcıları sınırlamak için dinamik veri maskeleme uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | SQL Azure, Onprea |
| **Öznitelikler**              | SQL sürümü-V12, SQL sürümü-MsSQL2016 |
| **Başvurular**              | [Dinamik Veri Maskeleme](/sql/relational-databases/security/dynamic-data-masking) |
| **Adımlar** | Dinamik veri maskeleme amacı, hassas verilerin açıklanmasını kısıtlayabilmelidir, bu da verileri görüntülemeden veriye erişimi olmayan kullanıcıları önler. Dinamik veri maskeleme, veritabanı kullanıcılarının veritabanına doğrudan bağlanmasını ve hassas verilerin parçalarını açığa çıkaran ayrıntılı sorgular çalıştırmasını önlemeye yönelik değildir. Dinamik veri maskeleme, diğer SQL Server güvenlik özellikleri (denetim, şifreleme, satır düzeyi güvenliği...) için tamamlayıcı hale gelir ve bu özellik, veritabanındaki hassas verileri daha iyi korumak için bunlara ek olarak, bu özelliğin birlikte kullanılması önemle önerilir. Bu özelliğin yalnızca 2016 ve Azure SQL veritabanı ile başlayan SQL Server desteklendiğinden emin olun. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Parolaların sallaştırılmış karma biçiminde depolandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [.NET şifre API 'Leri kullanarak parola karması](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Adımlar** | Parolalar özel kullanıcı deposu veritabanlarında depolanmamalıdır. Parola karmaları bunun yerine anahtar değerleriyle depolanmalıdır. Kullanıcının güvenlik düzeyini her zaman benzersiz olduğundan emin olun ve parolayı depolamadan önce, bir deneme yanılma olasılığını ortadan kaldırmak için en az bir iş faktörü yineleme sayısı olan 150.000 döngüsü ile b-Crypt, s-crypt veya PBKDF2 uygulayın.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Veritabanı sütunlarındaki gizli verilerin şifrelendiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | SQL sürümü-tümü |
| **Başvurular**              | [SQL Server 'da hassas verileri şifreleme](/previous-versions/sql/sql-server-2008-r2/ff848751(v=sql.105)), [nasıl yapılır: SQL Server bir veri sütununu](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)şifreleme, [sertifikayla şifreleme](/sql/t-sql/functions/encryptbycert-transact-sql) |
| **Adımlar** | Kredi kartı numaraları gibi hassas veriler veritabanında şifrelenmelidir. Veriler, sütun düzeyinde şifreleme kullanılarak veya şifreleme işlevleri kullanılarak bir uygulama işlevi tarafından şifrelenebilir. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Veritabanı düzeyinde şifrelemenin (TDE) etkinleştirildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [SQL Server Saydam Veri Şifrelemesi anlama (TDE)](/previous-versions/sql/sql-server-2008-r2/bb934049(v=sql.105)) |
| **Adımlar** | SQL Server 'daki Saydam Veri Şifrelemesi (TDE) özelliği, bir veritabanında hassas verileri şifrelemeye ve bir sertifikayla verileri şifrelemek için kullanılan anahtarları korumanıza yardımcı olur. Bu, anahtarları olmayan kişilerin verileri kullanmasını önler. TDE, verilerin ve günlük dosyalarının anlamı olan "bekleyen" verilerini korur. Çeşitli sektörlerde bulunan birçok yasalar, düzenleme ve kılavuzdan uyum sağlama olanağı sunar. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Veritabanı yedeklerinin şifrelendiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | SQL Azure, Onprea |
| **Öznitelikler**              | SQL sürümü-V12, SQL sürümü-MsSQL2014 |
| **Başvurular**              | [SQL veritabanı yedekleme şifrelemesi](/sql/relational-databases/backup-restore/backup-encryption) |
| **Adımlar** | SQL Server, bir yedekleme oluştururken verileri şifreleyebilme özelliğine sahiptir. Bir yedekleme oluştururken şifreleme algoritmasını ve Şifreleyici 'yi (sertifika veya asimetrik anahtar) belirterek, bir şifrelenmiş yedekleme dosyası oluşturabilir. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Web API 'siyle ilgili hassas verilerin tarayıcının depolama alanında depolanmadığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | MVC 5, MVC 6 |
| **Öznitelikler**              | Kimlik sağlayıcısı-ADFS, kimlik sağlayıcısı-Azure AD |
| **Başvurular**              | Yok  |
| **Adımlar** | <p>Belirli uygulamalarda, Web API 'sinin kimlik doğrulamasıyla ilgili hassas yapıtlar tarayıcının yerel depolama alanında depolanır. Örneğin, adal. ıdtoken, adal. nonce. ıdtoken, adal. Access. Token. Key, adal. Token. Keys, adal. State. Login, adal. Session. State, adal. expiration. Key vb. gibi Azure AD kimlik doğrulaması yapıtları.</p><p>Bu yapıtlar, oturum kapatma veya tarayıcı kapatıldıktan sonra bile kullanılabilir. Bir saldırgan bu yapıtlara erişim alırsa, korunan kaynaklara (API 'Ler) erişmek için bunları yeniden kullanabilir. Web API 'siyle ilgili tüm hassas yapıtların tarayıcı depolamada depolanmadığından emin olun. İstemci tarafı depolamanın kaçınılmaz olduğu durumlarda (ör. örtük Openıdconnect/OAuth akışlarının kullanıldığı tek sayfalı uygulamalar (SPA), erişim belirteçlerini yerel olarak depolaması gerekir), kalıcı olmayan depolama seçimlerini kullanın. Örneğin, LocalStorage 'a SessionStorage 'ı tercih edin.</p>| 

### <a name="example"></a>Örnek
Aşağıdaki JavaScript kod parçacığı, yerel depolamada kimlik doğrulama yapılarını depolayan özel bir kimlik doğrulama kitaplığıdır. Bu tür uygulamalar kaçınılmalıdır. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Cosmos DB depolanan hassas verileri şifreleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure belge DB | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Belge DB 'de depolamadan önce önemli verileri uygulama düzeyinde şifreleyin veya Azure Storage veya Azure SQL gibi diğer depolama çözümlerinde hassas verileri depolayın| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Sanal makineler tarafından kullanılan diskleri şifrelemek için Azure disk şifrelemesini kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure IaaS VM güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Sanal makineleriniz tarafından kullanılan diskleri şifrelemek için Azure disk şifrelemesini kullanma](../../storage/blobs/security-recommendations.md#data-protection) |
| **Adımlar** | <p>Azure disk şifrelemesi, şu anda önizleme aşamasında olan yeni bir özelliktir. Bu özellik, bir IaaS sanal makinesi tarafından kullanılan işletim sistemi disklerini ve veri disklerini şifrelemenizi sağlar. Windows için Sürücüler, sektör standardı BitLocker şifreleme teknolojisi kullanılarak şifrelenir. Linux için diskler DM-Crypt teknolojisi kullanılarak şifrelenir. Bu, disk şifreleme anahtarlarını denetlemenize ve yönetmenize olanak tanımak için Azure Key Vault ile tümleşiktir. Azure disk şifrelemesi çözümü, aşağıdaki üç müşteri şifreleme senaryosunu destekler:</p><ul><li>Müşteri tarafından şifrelenen VHD dosyalarından oluşturulan yeni IaaS sanal makinelerinde ve Azure Key Vault ' de depolanan müşteri tarafından sunulan şifreleme anahtarlarından şifrelemeyi etkinleştirin.</li><li>Azure Marketi 'nden oluşturulan yeni IaaS sanal makinelerinde şifrelemeyi etkinleştirin.</li><li>Azure 'da zaten çalışan mevcut IaaS VM 'lerinde şifrelemeyi etkinleştirin.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Service Fabric uygulamalarında gizli dizileri şifreleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure |
| **Başvurular**              | [Service Fabric uygulamalarında gizli dizileri yönetme](../../service-fabric/service-fabric-application-secret-management.md) |
| **Adımlar** | Gizlilikler, depolama bağlantı dizeleri, parolalar veya düz metin olarak işlenmemelidir diğer değerler gibi herhangi bir hassas bilgi olabilir. Service Fabric uygulamalarında anahtarları ve gizli dizileri yönetmek için Azure Key Vault kullanın. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Güvenlik modellemesini gerçekleştirin ve gereken yerlerde Iş birimleri/takımlar kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Güvenlik modellemesini gerçekleştirin ve gereken yerlerde Iş birimleri/takımlar kullanın |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Kritik varlıklarda özelliği paylaşmak için erişimi en aza indirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Kritik varlıklarda özelliği paylaşmak için erişimi en aza indirin |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Dynamics CRM Share özelliği ve iyi güvenlik uygulamalarıyla ilişkili riskler üzerinde Kullanıcıları eğitme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Dynamics CRM Share özelliği ve iyi güvenlik uygulamalarıyla ilişkili riskler üzerinde Kullanıcıları eğitme |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Özel durum yönetiminde yapılandırma ayrıntılarını gösteren bir geliştirme standartları kuralı ekleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Geliştirme dışında özel durum yönetiminde yapılandırma ayrıntılarını gösteren bir geliştirme standartları kuralı ekleyin. Kod incelemesinin veya düzenli denetlemenin bir parçası olarak bunu test edin.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Bekleyen veriler için Azure Depolama Hizmeti Şifrelemesi (SSE) kullanma (Önizleme)

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | StorageType-blob |
| **Başvurular**              | [Bekleyen veriler için Azure Depolama Hizmeti Şifrelemesi (Önizleme)](../../storage/common/storage-service-encryption.md) |
| **Adımlar** | <p>Rest 'teki veriler için Azure Depolama Hizmeti Şifrelemesi (SSE), kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak üzere verilerinizi korumanıza ve korumanıza yardımcı olur. Bu özellik ile Azure Depolama, verilerinizi depolama alanında kalıcı hale gelmeden önce otomatik olarak şifreler ve alınmadan önce bunların şifresini çözer. Şifreleme, şifre çözme ve anahtar yönetimi kullanıcılara tamamen saydamdır. SSE yalnızca blok Blobları, sayfa Blobları ve ekleme Blobları için geçerlidir. Tablolar, kuyruklar ve dosyalar dahil olmak üzere diğer veri türleri şifrelenmeyecektir.</p><p>Şifreleme ve şifre çözme Iş akışı:</p><ul><li>Müşteri, depolama hesabında şifrelemeyi mümkün</li><li>Müşteri yeni veri yazdığında (blob, PUT bloğu, PUT sayfası, vb.) BLOB depolama birimine; her yazma, 256 bit AES şifrelemesi kullanılarak şifrelenir, en güçlü blok şifrelemeleri mevcuttur</li><li>Müşterinin verilere erişmesi gerektiğinde (blob al, vb.), verilerin kullanıcıya döndürülmesinden önce otomatik olarak şifresi çözülür</li><li>Şifreleme devre dışıysa, yeni yazma işlemleri artık şifrelenmez ve mevcut şifrelenmiş veriler, Kullanıcı tarafından yeniden başvuruluncaya kadar şifrelenmeye devam eder. Şifreleme etkinken, blob depolamaya yazma işlemleri şifrelenir. Verilerin durumu, depolama hesabı için şifrelemeyi etkinleştirme/devre dışı bırakma arasında geçiş yapma arasında değişiklik yapmaz</li><li>Tüm şifreleme anahtarları Microsoft tarafından depolanır, şifrelenir ve yönetilir</li></ul><p>Lütfen şu anda şifreleme için kullanılan anahtarların Microsoft tarafından yönetildiğini unutmayın. Microsoft, anahtarları başlangıçta oluşturur ve anahtarların güvenli depolama alanını ve iç Microsoft ilkesi tarafından tanımlanan normal döndürmeyi yönetir. Gelecekte, müşteriler kendi >şifreleme anahtarlarını yönetebilir ve Microsoft tarafından yönetilen anahtarlardan müşteri tarafından yönetilen anahtarlara geçiş yolu sağlar.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Hassas verileri Azure depolama 'da depolamak için Client-Side şifrelemeyi kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Istemci tarafı şifreleme ve Microsoft Azure depolama için Azure Key Vault](../../storage/common/storage-client-side-encryption.md) [öğretici: Microsoft Azure depolama Azure Key Vault kullanarak blob 'ları şifreleme ve şifre çözme](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md), Azure [şifreleme uzantıları Ile Azure Blob depolamada verileri güvenli bir şekilde](/archive/blogs/partnercatalystteam/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions) depolama |
| **Adımlar** | <p>.NET NuGet paketi için Azure depolama Istemci kitaplığı, Azure depolama 'ya yüklemeden önce istemci uygulamalardaki verileri şifrelemeyi ve istemciye indirme sırasında verilerin şifresini çözmeyi destekler. Kitaplık ayrıca depolama hesabı anahtarı yönetimi için Azure Anahtar Kasası ile tümleştirmeyi destekler. İstemci tarafı şifrelemesinin nasıl çalıştığı hakkında kısa bir açıklama aşağıda verilmiştir:</p><ul><li>Azure depolama istemci SDK 'Sı, bir kerelik kullanılan simetrik anahtar olan bir içerik şifreleme anahtarı (CEK) oluşturur</li><li>Müşteri verileri bu CEK kullanılarak şifrelenir</li><li>CEK daha sonra anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanır (şifrelenir). KEK bir anahtar tanımlayıcısı tarafından tanımlanır ve asimetrik bir anahtar çifti veya simetrik anahtar olabilir ve yerel olarak yönetilebilir veya Azure Key Vault depolanabilir. Depolama istemcisinin, KEK hiç erişimi yoktur. Yalnızca Key Vault tarafından sağlanmış olan anahtar sarmalama algoritmasını çağırır. Müşteriler, tercih ettikleri durumlarda anahtar sarmalama/sarmalama için özel sağlayıcılar kullanmayı seçebilir</li><li>Şifrelenmiş veriler daha sonra Azure Storage Service 'e yüklenir. Alt düzey uygulama ayrıntıları için başvurular bölümündeki bağlantıları kontrol edin.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Telefon yerel depolama alanına yazılan hassas veya PII verilerini şifreleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Mobil Istemci | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel, Xamarin  |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Microsoft Intune ilkeleri ve [Anahtarlık Valet](https://components.xamarin.com/view/square.valet) [ile cihazlarınızda ayarları ve özellikleri yönetme](/mem/intune/configuration/) |
| **Adımlar** | <p>Uygulama, kullanıcının PII (e-posta, telefon numarası, ad, soyadı, Tercihler vb.) gibi gizli bilgiler yazıyorsa -Mobil dosya sisteminde, yerel dosya sistemine yazılmadan önce şifrelenmelidir. Uygulama bir kurumsal uygulama ise, Windows Intune kullanarak uygulama yayımlama olasılığa göz atın.</p>|

### <a name="example"></a>Örnek
Intune, hassas verileri korumak için aşağıdaki güvenlik ilkeleriyle yapılandırılabilir: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Örnek
Uygulama bir kurumsal uygulama değilse, dosya sisteminde hangi şifreleme işleminin gerçekleştirilebileceğini kullanarak şifreleme anahtarlarını depolamak için platform tarafından sunulan keystore ve keyzincirler ' i kullanın. Aşağıdaki kod parçacığında, Xamarin kullanarak anahtarlıktan anahtara nasıl erişebileceğiniz gösterilmektedir: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Son kullanıcılara dağıtmadan önce oluşturulan ikilileri gizleme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Mobil Istemci | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [.Net Için şifre gizleme](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Adımlar** | Oluşturulan ikili dosyalar (APK içindeki derlemeler), derlemelerin ters mühendislik işlemini durdurmak için karıştırılmış olmalıdır. Gibi Araçlar `CryptoObfuscator` da bu amaçla kullanılabilir. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>ClientCredentialType 'ı sertifika veya Windows olarak ayarla

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | .NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Adımlar** | Şifrelenmemiş bir kanal üzerinde düz metin parolasıyla bir UsernameToken kullanılması, SOAP iletilerini açığa çıkaran saldırganlar için parola gösterir. UsernameToken kullanan hizmet sağlayıcıları, düz metin olarak gönderilen parolaları kabul edebilir. Şifresiz bir kanal üzerinden düz metin parolalar göndermek, SOAP iletisini açığa çıkaran saldırganlar için kimlik bilgisini açığa çıkarır. | 

### <a name="example"></a>Örnek
Aşağıdaki WCF hizmet sağlayıcısı yapılandırması UsernameToken kullanır: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
ClientCredentialType 'ı sertifika veya Windows olarak ayarlayın. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF-güvenlik modu etkin değil

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel, .NET Framework 3 |
| **Öznitelikler**              | Güvenlik modu-taşıma, güvenlik modu-Ileti |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [WCF güvenlik kodu dergi temelleri](https://www.codemag.com/article/0611051) |
| **Adımlar** | Aktarım veya ileti güvenliği tanımlanmadı. Aktarım veya ileti güvenliği olmayan iletileri ileten uygulamalar, iletilerin bütünlüğünü veya gizliliğini garanti edemez. WCF güvenlik bağlaması None olarak ayarlandığında, hem aktarım hem de ileti güvenliği devre dışı bırakılır. |

### <a name="example"></a>Örnek
Aşağıdaki yapılandırma güvenlik modunu None olarak ayarlar. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Örnek
Tüm hizmet bağlamaları genelinde güvenlik modu, olası beş güvenlik modu vardır: 
* Yok. Güvenliği kapatır. 
* Aktarım. , Karşılıklı kimlik doğrulaması ve ileti koruması için taşıma güvenliği kullanır. 
* İleti. Karşılıklı kimlik doğrulama ve ileti koruması için ileti güvenliği kullanır. 
* İs. Aktarım ve ileti düzeyi güvenliği için ayarları sunmanıza olanak tanır (yalnızca MSMQ bunu destekler). 
* TransportWithMessageCredential. Kimlik bilgileri ileti ve ileti koruması ile geçirilir ve sunucu kimlik doğrulaması aktarım katmanı tarafından sağlanır. 
* Yalnızca Transportcredential. İstemci kimlik bilgileri aktarım katmanıyla geçirilir ve hiçbir ileti koruması uygulanmaz. İletilerin bütünlüğünü ve gizliliğini korumak için aktarım ve ileti güvenliği kullanın. Aşağıdaki yapılandırma, hizmetin ileti kimlik bilgileriyle aktarım güvenliği kullanmasını söyler.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```