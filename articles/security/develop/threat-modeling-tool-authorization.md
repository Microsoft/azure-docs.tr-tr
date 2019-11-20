---
title: Yetkilendirme-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728378"
---
# <a name="security-frame-authorization--mitigations"></a>Güvenlik çerçevesi: Yetkilendirme | Karşı 
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Makine güven sınırı** | <ul><li>[Uygun ACL 'Lerin cihazdaki verilere yetkisiz erişimi kısıtlamak için yapılandırıldığından emin olun](#acl-restricted-access)</li><li>[Kullanıcıya özgü gizli uygulama içeriğinin Kullanıcı profili dizininde depolandığından emin olun](#sensitive-directory)</li><li>[Dağıtılan uygulamaların en az ayrıcalıklarla çalıştığından emin olun](#deployed-privileges)</li></ul> |
| **Web uygulaması** | <ul><li>[İş mantığı akışlarını işlerken sıralı adım sırasını zorla](#sequential-logic)</li><li>[Numaralandırmayı engellemek için hız sınırlaması mekanizmasını uygulayın](#rate-enumeration)</li><li>[Doğru yetkilendirmenin yerinde olduğundan ve en az ayrıcalık ilkesinin izlendiğinden emin olun](#principle-least-privilege)</li><li>[İş mantığı ve kaynak erişimi yetkilendirme kararları gelen istek parametrelerine dayanmamalıdır](#logic-request-parameters)</li><li>[İçerik ve kaynakların numaralandırılıp, zorla göz atma aracılığıyla erişilebilir olmadığından emin olun](#enumerable-browsing)</li></ul> |
| **Veritabanı** | <ul><li>[Veritabanı sunucusuna bağlanmak için en az ayrıcalıklı hesapların kullanıldığından emin olun](#privileged-server)</li><li>[Kiracıların diğer verilerine erişmesini engellemek için satır düzeyinde güvenlik RLS uygulayın](#rls-tenants)</li><li>[Sysadmin rolü yalnızca geçerli gerekli kullanıcıları içermelidir](#sysadmin-users)</li></ul> |
| **IoT bulut ağ geçidi** | <ul><li>[En az ayrıcalıklı belirteçleri kullanarak bulut ağ geçidine bağlanma](#cloud-least-privileged)</li></ul> |
| **Azure Olay Hub 'ı** | <ul><li>[Cihaz belirteçleri oluşturmak için yalnızca bir salt-Gönder izin SAS anahtarı kullanın](#sendonly-sas)</li><li>[Olay Hub 'ına doğrudan erişim sağlayan erişim belirteçleri kullanmayın](#access-tokens-hub)</li><li>[Minimum izinleri olması gereken SAS anahtarlarını kullanarak Olay Hub 'ına bağlanma](#sas-minimum-permissions)</li></ul> |
| **Azure belge DB** | <ul><li>[Mümkün olduğunda Azure Cosmos DB bağlanmak için kaynak belirteçlerini kullanın](#resource-docdb)</li></ul> |
| **Azure Güven sınırı** | <ul><li>[RBAC kullanarak Azure aboneliğine ayrıntılı erişim yönetimini etkinleştirme](#grained-rbac)</li></ul> |
| **Service Fabric güven sınırı** | <ul><li>[RBAC kullanarak istemcinin küme işlemlerine erişimini kısıtlama](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Güvenlik modellemesini gerçekleştirin ve gereken yerlerde alan düzeyi güvenliği kullanın](#modeling-field)</li></ul> |
| **Dynamics CRM portalı** | <ul><li>[Portalın güvenlik modelinin CRM 'nin geri kalanından farklı olduğunu aklınızda tutarak Portal hesaplarının güvenlik modellemesini gerçekleştirin](#portal-security)</li></ul> |
| **Azure Depolama** | <ul><li>[Azure Tablo depolama alanındaki çeşitli varlıklar üzerinde hassas izinler verin](#permission-entities)</li><li>[Rol tabanlı Access Control (RBAC) Azure depolama hesabına Azure Resource Manager kullanarak etkinleştirin](#rbac-azure-manager)</li></ul> |
| **Mobil Istemci** | <ul><li>[Örtük jailbreak veya kök öğe algılamayı uygulama](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[WCF 'de zayıf sınıf başvurusu](#weak-class-wcf)</li><li>[WCF-Yetkilendirme denetimini uygulama](#wcf-authz)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API 'sinde doğru yetkilendirme mekanizmasını uygulama](#authz-aspnet)</li></ul> |
| **IoT cihazı** | <ul><li>[Farklı izin düzeyleri gerektiren çeşitli eylemleri destekliyorsa, cihazda yetkilendirme denetimleri gerçekleştirin](#device-permission)</li></ul> |
| **IoT alan ağ geçidi** | <ul><li>[Farklı izin düzeyleri gerektiren çeşitli eylemleri destekliyorsa, alan ağ geçidinde yetkilendirme denetimleri gerçekleştirin](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Uygun ACL 'Lerin cihazdaki verilere yetkisiz erişimi kısıtlamak için yapılandırıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Uygun ACL 'Lerin cihazdaki verilere yetkisiz erişimi kısıtlamak için yapılandırıldığından emin olun|

## <a id="sensitive-directory"></a>Kullanıcıya özgü gizli uygulama içeriğinin Kullanıcı profili dizininde depolandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Gizli kullanıcıya özgü uygulama içeriğinin Kullanıcı profili dizininde depolandığından emin olun. Bu, makinenin birden çok kullanıcının diğer verilerine erişmesini önlemektir.|

## <a id="deployed-privileges"></a>Dağıtılan uygulamaların en az ayrıcalıklarla çalıştığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Dağıtılan uygulamanın en az ayrıcalıklarla çalıştığından emin olun. |

## <a id="sequential-logic"></a>İş mantığı akışlarını işlerken sıralı adım sırasını zorla

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Bu aşamanın, orijinal bir kullanıcı tarafından çalıştırıldığını doğrulamak için, uygulamayı yalnızca sıralı adımda iş mantığı akışlarını işlemek üzere zorlamak isteyebilirsiniz, böylece tüm adımlar gerçekçi insan sürelerinden işlenir ve işlem sırasında işleme uygulanmaz, atlanan adımlar , başka bir kullanıcıdan işlenen adımları veya çok hızlı gönderilen işlemleri.|

## <a id="rate-enumeration"></a>Numaralandırmayı engellemek için hız sınırlaması mekanizmasını uygulayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Hassas tanımlayıcıların rastgele olduğundan emin olun. Anonim sayfalarda CAPTCHA denetimi uygulayın. Hata ve özel durumun belirli verileri açığa çıkarmadığından emin olun|

## <a id="principle-least-privilege"></a>Doğru yetkilendirmenin yerinde olduğundan ve en az ayrıcalık ilkesinin izlendiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Bu ilke, Kullanıcı hesabına yalnızca kullanıcıların çalışması için gerekli olan ayrıcalıkların verilmesi anlamına gelir. Örneğin, bir yedekleme kullanıcısının yazılım yüklemesi gerekmez: Bu nedenle, yedekleme kullanıcısının yalnızca yedekleme ve yedeklemeyle ilgili uygulamaları çalıştırma hakları vardır. Yeni yazılım yükleme gibi başka ayrıcalıklar engellenir. Bu ilke, genellikle normal bir kullanıcı hesabında çalışan bir kişisel bilgisayar kullanıcısına uygulanır ve ayrıcalıklı, parola korumalı bir hesabı (yani, Süper Kullanıcı) yalnızca durum size kesinlikle talep edildiğinde açar. </p><p>Bu ilke, Web uygulamalarınız için de uygulanabilir. Yalnızca oturumları kullanan rol tabanlı kimlik doğrulama yöntemlerine bağlı olarak, kullanıcılara bir veritabanı tabanlı kimlik doğrulama sistemi yoluyla ayrıcalık atamak istiyoruz. Kullanıcının doğru şekilde oturum açmış olup olmadığını belirlemek için oturumları hala kullandığımızda, bu kullanıcıyı belirli bir rol ile atamak yerine, sistem üzerinde hangi eylemlerin ayrıcalıklı olduğunu doğrulama ayrıcalıklarına sahip olduğunu belirlemek için de oturum kullanırız. Ayrıca, bu yöntemin büyük bir uzmanı olması, bir kullanıcıya daha az ayrıcalık atanması gerektiğinde değişiklikleriniz, aksi takdirde önce süresi dolacak olan oturuma bağlı olmadığından değişiklikler anında uygulanır.</p>|

## <a id="logic-request-parameters"></a>İş mantığı ve kaynak erişimi yetkilendirme kararları gelen istek parametrelerine dayanmamalıdır

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Bir kullanıcının belirli verileri incelemeye kısıtlı olup olmadığını kontrol ettiğinizde, erişim kısıtlamaları sunucu tarafında işlenmelidir. Kullanıcı kimliği, oturum açma sırasında bir oturum değişkeni içinde depolanmalıdır ve veritabanından kullanıcı verilerini almak için kullanılmalıdır |

### <a name="example"></a>Örnek
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Artık olası bir saldırgan, verileri alma tanımlayıcısı sunucu tarafında işlendiğinden, uygulama işlemini değiştiremeyebilir ve değiştiremezler.

## <a id="enumerable-browsing"></a>İçerik ve kaynakların numaralandırılıp, zorla göz atma aracılığıyla erişilebilir olmadığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Gizli statik ve yapılandırma dosyaları Web kökünde tutulmamalıdır. İçeriğin genel olması gerekmediği için, içeriğin kendisi için uygun erişim denetimlerinin uygulanması veya kaldırılması gerekir.</p><p>Ayrıca, zorla göz atma genellikle bir sunucudaki dizinleri ve dosyaları numaralandırmak için mümkün olduğunca fazla URL 'ye erişmeyi deneyerek, deneme yanılma teknikleriyle birleştirilir. Saldırganlar, yaygın olarak varolan dosyaların tüm çeşitlemelerini denetleyebilir. Örneğin, bir parola dosya araması psswd. txt, Password. htm, Password. dat ve diğer çeşitlemeler dahil olmak üzere dosyaları kapsayabilir.</p><p>Bunu azaltmak için, deneme yanılma girişimlerini algılama özellikleri dahil edilmelidir.</p>|

## <a id="privileged-server"></a>Veritabanı sunucusuna bağlanmak için en az ayrıcalıklı hesapların kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [SQL veritabanı izinleri hiyerarşisi](https://msdn.microsoft.com/library/ms191465), [SQL Veritabanı securables](https://msdn.microsoft.com/library/ms190401) |
| **Adımları** | Veritabanına bağlanmak için en az ayrıcalıklı hesaplar kullanılmalıdır. Uygulama oturum açma, veritabanında kısıtlamalı ve yalnızca seçili saklı yordamları yürütmelidir. Uygulamanın oturum açma, doğrudan tablo erişimi içermemelidir. |

## <a id="rls-tenants"></a>Kiracıların diğer verilerine erişmesini engellemek için satır düzeyinde güvenlik RLS uygulayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | SQL Azure, Onprea |
| **Öznitelikler**              | SQL sürümü-V12, SQL sürümü-MsSQL2016 |
| **Başvur**              | [Satır düzeyi güvenliği SQL Server (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Adımları** | <p>Satır Düzeyi Güvenlik, müşterilerin bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre (grup üyeliği veya yürütme bağlamı) denetlemesini sağlar.</p><p>Satır düzeyi güvenlik (RLS), uygulamanızda güvenliğin tasarımını ve kodlamasını basitleştirir. RLS, veri satırı erişiminde kısıtlama uygulamanızı sağlar. Örneğin çalışanlar yalnızca kendi bölümleriyle ilgili veri satırlarına erişmesi veya müşterilerin yalnızca kendi şirketleriyle ilgili verilere ulaşması sağlanabilir.</p><p>Erişim kısıtlama mantığı, başka bir uygulama katmanındaki verilerden uzakta değil, veritabanı katmanında bulunur. Veritabanı sistemi, herhangi bir katmanda veri erişimi her denendiğinde erişim kısıtlamalarını uygular. Bu, güvenlik sisteminin yüzey alanını azaltarak güvenlik sisteminin daha güvenilir ve sağlam olmasını sağlar.</p><p>|

Lütfen hazır olmayan bir veritabanı özelliği olarak RLS 'nin yalnızca 2016 ve Azure SQL veritabanı SQL Server için geçerli olduğunu unutmayın. Kullanıma hazır RLS özelliği uygulanmadığından, veri erişiminin görünümler ve yordamlar kullanılarak kısıtlanması gerekir

## <a id="sysadmin-users"></a>Sysadmin rolü yalnızca geçerli gerekli kullanıcıları içermelidir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [SQL veritabanı izinleri hiyerarşisi](https://msdn.microsoft.com/library/ms191465), [SQL Veritabanı securables](https://msdn.microsoft.com/library/ms190401) |
| **Adımları** | SysAdmin sabit sunucu rolünün üyeleri çok sınırlı olmalı ve hiçbir şekilde uygulamalar tarafından kullanılan hesapları içermemelidir.  Lütfen roldeki kullanıcıların listesini gözden geçirin ve gereksiz hesapları kaldırın|

## <a id="cloud-least-privileged"></a>En az ayrıcalıklı belirteçleri kullanarak bulut ağ geçidine bağlanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ağ Geçidi seçimi-Azure IoT Hub |
| **Başvur**              | [IoT Hub Access Control](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Adımları** | Bulut ağ geçidine (IoT Hub) bağlanan çeşitli bileşenlere en az ayrıcalık izinleri verin. Tipik örnek, – cihaz yönetimi/sağlama bileşeni registryread/Write, olay Işlemcisi (ASA) ise hizmet Connect kullanır. Tek tek cihazlar cihaz kimlik bilgilerini kullanarak bağlanır|

## <a id="sendonly-sas"></a>Cihaz belirteçleri oluşturmak için yalnızca bir salt-Gönder izin SAS anahtarı kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Olay Hub'ı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Event Hubs kimlik doğrulaması ve güvenlik modeline genel bakış](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Adımları** | Tek tek cihaz belirteçleri oluşturmak için SAS anahtarı kullanılır. Belirli bir yayımcının cihaz belirtecini oluştururken yalnızca bir salt-Send izin SAS anahtarı kullanın|

## <a id="access-tokens-hub"></a>Olay Hub 'ına doğrudan erişim sağlayan erişim belirteçleri kullanmayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Olay Hub'ı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Event Hubs kimlik doğrulaması ve güvenlik modeline genel bakış](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Adımları** | Olay Hub 'ına doğrudan erişim veren bir belirteç cihaza verilmemelidir. Yalnızca bir yayımcıya erişim sağlayan cihaz için en az ayrıcalıklı bir belirteç kullanılması, bir standart dışı veya güvenliği aşılmış bir cihaz olup olmadığı tespit etmenize yardımcı olur.|

## <a id="sas-minimum-permissions"></a>Minimum izinleri olması gereken SAS anahtarlarını kullanarak Olay Hub 'ına bağlanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Olay Hub'ı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Event Hubs kimlik doğrulaması ve güvenlik modeline genel bakış](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Adımları** | Olay Hub 'ına bağlanan çeşitli arka uç uygulamalarına en az ayrıcalık izinleri verin. Her bir arka uç uygulaması için ayrı SAS anahtarları oluşturun ve yalnızca gerekli izinleri (gönderme, alma veya yönetme) sağlayın.|

## <a id="resource-docdb"></a>Mümkün olduğunda Cosmos DB bağlanmak için kaynak belirteçlerini kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure belge DB | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Kaynak belirteci Azure Cosmos DB bir izin kaynağıyla ilişkilendirilir ve bir veritabanının kullanıcısı ile kullanıcının belirli bir Azure Cosmos DB uygulama kaynağına (örn. koleksiyon, belge) sahip olduğu izni yakalar. İstemci, bir mobil veya masaüstü istemcisi gibi son kullanıcı uygulaması gibi ana veya salt okunurdur anahtarlar ile güvenilemiyorsa Azure Cosmos DB erişmek için her zaman bir kaynak belirteci kullanın. Bu anahtarları güvenli bir şekilde depolayabilen arka uç uygulamalarından ana anahtar veya salt okuma anahtarları kullanın.|

## <a id="grained-rbac"></a>RBAC kullanarak Azure aboneliğine ayrıntılı erişim yönetimini etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Güven sınırı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanın](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Adımları** | Azure Rol Tabanlı Erişim Denetimi (RBAC), Azure için ayrıntılı erişim yönetimi sağlar. RBAC kullanarak, yalnızca kullanıcıların işlerini yapmak için gereksinim duyduğu erişim miktarını verebilirsiniz.|

## <a id="cluster-rbac"></a>RBAC kullanarak istemcinin küme işlemlerine erişimini kısıtlama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure |
| **Başvur**              | [Service Fabric istemcileri için rol tabanlı erişim denetimi](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Adımları** | <p>Azure Service Fabric, bir Service Fabric kümesine bağlı istemciler için iki farklı erişim denetimi türünü destekler: yönetici ve Kullanıcı. Erişim denetimi, küme yöneticisinin farklı Kullanıcı grupları için belirli küme işlemlerine erişimi sınırlandırıp kümenin daha güvenli olmasını sağlar.</p><p>Yöneticiler, yönetim özelliklerine (okuma/yazma özellikleri dahil) tam erişime sahiptir. Varsayılan olarak kullanıcılar, yönetim özelliklerine (örneğin, sorgu özellikleri) yalnızca okuma erişimine ve uygulama ve Hizmetleri çözme imkanına sahiptir.</p><p>Her biri için ayrı sertifikalar sağlayarak küme oluşturma sırasında iki istemci rolünü (yönetici ve istemci) belirtirsiniz.</p>|

## <a id="modeling-field"></a>Güvenlik modellemesini gerçekleştirin ve gereken yerlerde alan düzeyi güvenliği kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Güvenlik modellemesini gerçekleştirin ve gereken yerlerde alan düzeyi güvenliği kullanın|

## <a id="portal-security"></a>Portalın güvenlik modelinin CRM 'nin geri kalanından farklı olduğunu aklınızda tutarak Portal hesaplarının güvenlik modellemesini gerçekleştirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM portalı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Portalın güvenlik modelinin CRM 'nin geri kalanından farklı olduğunu aklınızda tutarak Portal hesaplarının güvenlik modellemesini gerçekleştirin|

## <a id="permission-entities"></a>Azure Tablo depolama alanındaki çeşitli varlıklar üzerinde hassas izinler verin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Storage | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | StorageType-Table |
| **Başvur**              | [SAS kullanarak Azure Depolama hesabınızdaki nesnelere erişim yetkisi verme](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Adımları** | Bazı iş senaryolarında, Azure Tablo depolaması, farklı taraflara göre hassas verileri depolamak için gerekli olabilir. Örneğin, farklı ülkeler/bölgelerle ilgili hassas veriler. Bu gibi durumlarda SAS imzaları, bir kullanıcının belirli bir ülkeye/bölgeye özgü verilere erişebileceği şekilde bölüm ve satır anahtar aralıkları belirtilerek oluşturulabilir.| 

## <a id="rbac-azure-manager"></a>Rol tabanlı Access Control (RBAC) Azure depolama hesabına Azure Resource Manager kullanarak etkinleştirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Storage | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Rol tabanlı Access Control (RBAC) ile depolama hesabınızı güvenli hale getirme](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Adımları** | <p>Yeni bir depolama hesabı oluşturduğunuzda, klasik veya Azure Resource Manager dağıtım modelini seçersiniz. Azure 'da kaynak oluşturmak için klasik model, yalnızca aboneliğe hiçbir şey erişimi ve depolama hesabını sırayla sağlar.</p><p>Azure Resource Manager modeliyle, depolama hesabını bir kaynak grubuna yerleştirip, bu belirli depolama hesabının yönetim düzlemine erişimi Azure Active Directory kullanarak kontrol edersiniz. Örneğin, belirli kullanıcılara depolama hesabı anahtarlarına erişme özelliği verebilirsiniz, ancak diğer kullanıcılar depolama hesabı hakkındaki bilgileri görüntüleyebilir, ancak depolama hesabı anahtarlarına erişemez.</p>|

## <a id="rooting-detection"></a>Örtük jailbreak veya kök öğe algılamayı uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Mobil Istemci | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Telefonun kökü veya daha kopuk olması durumunda uygulama kendi yapılandırmasını ve Kullanıcı verilerini korumaya almalıdır. Kök ayırma/JAIL bölünmesi, normal kullanıcıların kendi telefonlarında yapamayacağı yetkisiz erişim anlamına gelir. Bu nedenle uygulamanın, telefonun kökü olup olmadığını algılamak için uygulama başlangıcında örtük algılama mantığı olmalıdır.</p><p>Algılama mantığı yalnızca normal olarak yalnızca kök kullanıcının erişebileceği dosyalara erişebilir, örneğin:</p><ul><li>/System/App/superuser,APK</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/Data/Local/su</li></ul><p>Uygulama bu dosyalardan herhangi birine erişebildiğinden, uygulamanın kök kullanıcı olarak çalıştığını gösterir.</p>|

## <a id="weak-class-wcf"></a>WCF 'de zayıf sınıf başvurusu

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Adımları** | <p>Sistem zayıf bir sınıf başvurusu kullanır ve bu da bir saldırganın yetkisiz kod yürütmesine izin verebilir. Program, benzersiz şekilde tanımlanmayan Kullanıcı tanımlı bir sınıfa başvurur. .NET, bu zayıf tanımlı sınıfı yüklediğinde CLR tür yükleyicisi aşağıdaki konumlarda belirtilen sırada sınıfı arar:</p><ol><li>Türün derlemesi biliniyorsa, yükleyici yapılandırma dosyasının yeniden yönlendirme konumlarını, GAC 'yi, yapılandırma bilgilerini kullanarak geçerli derlemeyi ve uygulama temel dizinini arar</li><li>Derleme bilinmiyorsa, yükleyici geçerli derlemeyi, mscorlib 'yi ve TypeResolve olay işleyicisi tarafından döndürülen konumu arar</li><li>Bu CLR arama sırası tür Iletme mekanizması ve AppDomain. TypeResolve olayı gibi kancalarla değiştirilebilir</li></ol><p>Bir saldırgan aynı ada sahip alternatif bir sınıf oluşturup clr 'nin ilk yükleneceği alternatif bir konuma yerleştirilerek CLR arama sırasını kullanıyorsa, CLR istemeden saldırgan tarafından sağlanan kodu yürütür</p>|

### <a name="example"></a>Örnek
Aşağıdaki `<behaviorExtensions/>` WCF yapılandırma dosyasının öğesi, WCF 'yi belirli bir WCF uzantısına özel bir davranış sınıfı eklemeye yönlendirir.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Tam nitelikli (tanımlayıcı) adların kullanılması, bir türü benzersiz bir şekilde tanımlar ve sisteminizin güvenliğini artırır. Machine. config ve App. config dosyalarına türleri kaydederken tam nitelikli derleme adlarını kullanın.

### <a name="example"></a>Örnek
Aşağıdaki `<behaviorExtensions/>` WCF yapılandırma dosyasının öğesi, WCF 'ye, belirli bir WCF uzantısına kesin olarak başvurulan özel davranış sınıfı eklemesi için izin verir.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>WCF-Yetkilendirme denetimini uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Adımları** | <p>Bu hizmet bir yetkilendirme denetimi kullanmaz. İstemci belirli bir WCF hizmetini çağırdığında, arayanın sunucuda hizmet yöntemini yürütme iznine sahip olduğunu doğrulayan çeşitli yetkilendirme şemaları sağlar. Yetkilendirme denetimleri WCF Hizmetleri için etkinleştirilmemişse, kimliği doğrulanmış bir Kullanıcı ayrıcalık yükseltme elde edebilir.</p>|

### <a name="example"></a>Örnek
Aşağıdaki yapılandırma, hizmeti yürütürken WCF 'nin istemcinin yetkilendirme düzeyini denetmamasını sağlar:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Hizmet yöntemi çağıranın bu işlemi yapmaya yetkili olduğunu doğrulamak için bir hizmet yetkilendirme şeması kullanın. WCF iki mod sağlar ve özel bir yetkilendirme şeması tanımına izin verir. UseWindowsGroups modu Windows rollerini ve kullanıcılarını kullanır ve UseAspNetRoles modu, kimlik doğrulaması için SQL Server gibi bir ASP.NET rol sağlayıcısı kullanır.

### <a name="example"></a>Örnek
Aşağıdaki yapılandırma, Add hizmetini yürütmeden önce istemcinin Yöneticiler grubunun bir parçası olduğundan emin olmak için WCF 'ye bildirir:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Daha sonra hizmet aşağıdaki şekilde bildirilmiştir:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>ASP.NET Web API 'sinde doğru yetkilendirme mekanizmasını uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API'si | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, MVC5 |
| **Öznitelikler**              | Yok, kimlik sağlayıcısı-ADFS, kimlik sağlayıcısı-Azure AD |
| **Başvur**              | [ASP.NET Web API 'sinde kimlik doğrulama ve yetkilendirme](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Adımları** | <p>Uygulama kullanıcıları kimlik sağlayıcısı olarak bu uygulamaya dayanıyorsa veya uygulamanın kendisi tarafından sağlandıysa, uygulama kullanıcıları için rol bilgileri Azure AD 'den veya ADFS taleplerinden türetilebilir. Bu durumların hiçbirinde, özel yetkilendirme uygulamasının Kullanıcı rolü bilgilerini doğrulaması gerekir.</p><p>Uygulama kullanıcıları kimlik sağlayıcısı olarak bu uygulamaya dayanıyorsa veya uygulamanın kendisi tarafından sağlandıysa, uygulama kullanıcıları için rol bilgileri Azure AD 'den veya ADFS taleplerinden türetilebilir. Bu durumların hiçbirinde, özel yetkilendirme uygulamasının Kullanıcı rolü bilgilerini doğrulaması gerekir.</p>

### <a name="example"></a>Örnek
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Korunması gereken tüm denetleyiciler ve eylem yöntemlerinin, yukarıdaki özniteliğiyle birlikte tasarlanmalıdır.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Farklı izin düzeyleri gerektiren çeşitli eylemleri destekliyorsa, cihazda yetkilendirme denetimleri gerçekleştirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT Cihazı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Cihaz, çağıranın istenen eylemi gerçekleştirmek için gerekli izinlere sahip olup olmadığını denetlemek için çağrıyı yapana yetki almalıdır. Örneğin, cihazın buluttan izlenebilecek bir akıllı kapı kilidi olduğunu ve ayrıca, kapıyı uzaktan kilitleme gibi işlevler sağladığını varsayalım.</p><p>Akıllı kapı kilidi, yalnızca biri bir kartla kapıya yakın bir şekilde geldiğinde kilit açma işlevi sağlar. Bu durumda, uzak komut ve denetim uygulamasının uygulamanın, kapısının kilidini açmak için bir komut gönderme yetkisi olmadığı sürece, kapısının kilidini açmak için herhangi bir işlev sağlamacağından bu şekilde yapılması gerekir.</p>|

## <a id="field-permission"></a>Farklı izin düzeyleri gerektiren çeşitli eylemleri destekliyorsa, alan ağ geçidinde yetkilendirme denetimleri gerçekleştirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT alan ağ geçidi | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Alan ağ geçidi, çağıranın istenen eylemi gerçekleştirmek için gerekli izinlere sahip olup olmadığını denetlemek için çağrıyı yapana yetki verir. Örneğin, buna bağlanan bir alan ağ geçidi v/s cihazlarını yapılandırmak için kullanılan yönetici kullanıcı arabirimi/API 'SI için farklı izinler olmalıdır.|
