---
title: Azure veritabanı güvenliğine genel bakış | Microsoft Docs
description: Bu makalede, Azure veritabanı güvenlik özelliklerine genel bir bakış sunulmaktadır.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906051"
---
# <a name="azure-database-security-overview"></a>Azure veritabanı güvenliğine genel bakış

Güvenlik, veritabanlarının yönetiminde önemli bir konudur ve Azure SQL veritabanı için her zaman bir önceliktir. Azure SQL veritabanı, güvenlik duvarı kurallarıyla bağlantı güvenliğini ve bağlantı şifrelemesini destekler. Azure Active Directory tarafından yönetilen kimlikleri kullanan Kullanıcı adı ve parola ile Azure Active Directory (Azure AD) kimlik doğrulaması ile kimlik doğrulamasını destekler. Yetkilendirme, rol tabanlı erişim denetimi kullanır.

Azure SQL veritabanı, uygulamada değişiklik gerektirmeden veritabanları, ilişkili yedeklemeler ve işlem günlük dosyaları için gerçek zamanlı şifreleme ve şifre çözme gerçekleştirerek şifrelemeyi destekler.

Microsoft, kurumsal verileri şifrelemek için ek yollar sağlar:

-   Hücre düzeyinde şifreleme, belirli sütunları veya hatta farklı şifreleme anahtarlarıyla veri hücrelerini şifrelemek için kullanılabilir.
-   Şifreleme anahtarı hiyerarşinizin bir donanım güvenlik modülüne veya merkezi yönetimine ihtiyacınız varsa, bir Azure sanal makinesinde (VM) SQL Server Azure Key Vault kullanmayı göz önünde bulundurun.
-   Always Encrypted (Şu anda önizlemede), şifrelemeyi uygulamalar için saydam hale getirir. Ayrıca, istemcilerin SQL veritabanı ile şifreleme anahtarlarını paylaşmadan önemli verileri istemci uygulamalar içinde şifrelemesine olanak tanır.

Azure SQL veritabanı denetimi, kuruluşların Azure Storage 'da bir denetim günlüğüne olay kaydetmesine olanak sağlar. SQL Veritabanı Denetimi ayrıca Microsoft Power BI ile tümleştirilerek ayrıntılı raporlar ve analizler oluşturulmasını sağlar.

Azure SQL veritabanlarının, HIPAA, ISO 27001/27002 ve PCI DSS düzey 1 dahil olmak üzere çok sayıda yasal veya güvenlik gereksinimini karşılamak için sıkı bir şekilde güvenli hale getirilmiş olabilir. Güvenlik uyumluluğu sertifikalarının geçerli bir listesi [Microsoft Azure Güven Merkezi sitesinde](https://azure.microsoft.com/support/trust-center/services/)bulunabilir.

Bu makalede, yapılandırılmış, tablolu ve ilişkisel veriler için Microsoft Azure SQL veritabanlarını güvenli hale getirmenin temelleri anlatılmaktadır. Bu makalede özellikle veri koruma, erişim denetimi ve öngörülebilir izleme kaynakları için başlangıç bilgileri sunulmaktadır.

## <a name="protection-of-data"></a>Verilerin korunması

SQL veritabanı, şifreleme sağlayarak verilerinizin güvenliğinin sağlanmasına yardımcı olur:

- [Aktarım Katmanı Güvenliği (TLS)](https://support.microsoft.com/kb/3135244)aracılığıyla hareket halindeki veriler için.
- [Saydam veri şifrelemesi](https://go.microsoft.com/fwlink/?LinkId=526242)aracılığıyla bekleyen veriler için.
- [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)aracılığıyla kullanılan veriler için.

Verilerinizi şifrelemek için kullanabileceğiniz diğer yöntemler şunlardır:

-   [Hücre düzeyinde şifreleme](https://msdn.microsoft.com/library/ms179331.aspx): Belirli sütunları hatta veri hücrelerini farklı şifreleme anahtarlarıyla şifrelemenizi sağlar.
-   Bir [Azure sanal makinesinde SQL Server Azure Key Vault](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), bir donanım güvenlik modülüne veya şifreleme anahtarı hiyerarşinizin merkezi yönetimine ihtiyacınız varsa.

### <a name="encryption-in-motion"></a>Hareket halinde şifreleme

Tüm istemci/sunucu uygulamaları için sık karşılaşılan bir sorun, verilerin ortak ve özel ağlardan taşınması halinde gizlilik gereksinimidir. Bir ağ üzerinden taşınan veriler şifrelenmemişse, yetkisiz kullanıcılar tarafından yakalanıp çalınabileceğini bir şansınız vardır. Veritabanı hizmetleriyle ilgilenirken, verilerin veritabanı istemcisiyle sunucusu arasında şifrelendiğinden emin olun. Ayrıca, verilerin birbirleriyle ve orta katman uygulamalarla iletişim kuran veritabanı sunucuları arasında şifrelendiğinden emin olun.

Bir ağ yönetirken bir sorun, güvenilmeyen bir ağda bulunan uygulamalar arasında gönderilen verilerin güvenli hale getirilmesidir. Sunucularının ve istemcilerin kimliğini doğrulamak için [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) kullanabilirsiniz ve sonra kimliği doğrulanmış taraflar arasında iletileri şifrelemek için kullanabilirsiniz.

Kimlik doğrulama işleminde, bir TLS/SSL istemcisi bir TLS/SSL sunucusuna bir ileti gönderir. Sunucu, sunucunun kimliğini doğrulamak için gereken bilgilerle yanıt verir. İstemci ve sunucu, oturum anahtarlarının ek bir değişimini gerçekleştirir ve kimlik doğrulama iletişim kutusu sonlanır. Kimlik doğrulaması tamamlandığında, SSL güvenli iletişim, kimlik doğrulama işlemi sırasında kurulan simetrik şifreleme anahtarları aracılığıyla sunucu ve istemci arasında başlayabilir.

Tüm Azure SQL veritabanı bağlantıları, veriler "geçiş sırasında" ve veritabanından ve veritabanına her zaman şifreleme (TLS/SSL) gerektirir. SQL veritabanı, sunucu ve istemcilerin kimliğini doğrulamak için TLS/SSL kullanır ve sonra kimliği doğrulanmış taraflar arasındaki iletileri şifrelemek için kullanılır. 

Uygulamanızın bağlantı dizesinde, bağlantıyı şifrelemek ve sunucu sertifikasına güvenmediğiniz parametreleri belirtmeniz gerekir. (Bu, Bağlantı dizenizi Azure portal dışına kopyalarsanız sizin için yapılır.) Aksi halde bağlantı sunucunun kimliğini doğrulamaz ve "ortadaki adam" saldırılarına maruz kalır. Örneğin, ADO.net sürücüsü için, bu bağlantı dizesi parametreleri ve `Encrypt=True` `TrustServerCertificate=False`' dir.

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

Veritabanının güvenliğini sağlamaya yardımcı olmak için birkaç önlem alabilirsiniz. Örneğin, güvenli bir sistem tasarlayın, gizli varlıkları şifreleyin ve veritabanı sunucuları etrafında bir güvenlik duvarı oluşturun. Ancak, fiziksel medyanın (örneğin, sürücüler veya yedek bantların) çalındığı bir senaryoda, kötü amaçlı bir taraf veritabanını yalnızca geri yükleyebilir veya ekleyebilir ve verilere gözatabilir.

Bir çözüm, veritabanında hassas verileri şifrelemek ve bir sertifikayla verileri şifrelemek için kullanılan anahtarları korumak. Bu çözüm, anahtar içermeyen kişilerin verileri kullanmasını engeller, ancak bu tür bir koruma planlanmalıdır.

Bu sorunu çözmek için, SQL Server ve SQL veritabanı [Saydam veri şifrelemesini](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017)destekler. Saydam veri şifrelemesi, bekleyen şifreleme verileri olarak bilinen SQL Server ve SQL veritabanı veri dosyalarını şifreler.

Saydam veri şifrelemesi, kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur. Uygulamada değişiklik gerektirmeden, bekleyen veritabanı, ilişkili yedeklemeler ve işlem günlüğü dosyaları için gerçek zamanlı şifreleme ve şifre çözme işlemleri gerçekleştirir.  

Saydam veri şifrelemesi, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. SQL veritabanında, veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur. Yerleşik sunucu sertifikası her SQL veritabanı sunucusu için benzersizdir.

Bir veritabanı bir coğrafi-DR ilişkisinde ise, her bir sunucuda farklı bir anahtarla korunur. İki veritabanı aynı sunucuya bağlıysa, aynı yerleşik sertifikayı paylaşır. Microsoft bu sertifikaları en az 90 günde bir otomatik olarak döndürür. 

Daha fazla bilgi için bkz. [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kullanımdaki şifreleme (istemci)

Çoğu veri ihlal, kredi kartı numaraları veya kişisel olarak tanımlanabilir bilgiler gibi kritik verilerin hırsızlık ile ilgilidir. Veritabanları, hassas bilgilerin rahatlarını denetlediğinizden emin olabilir. Müşterilerin kişisel verilerini (Ulusal kimlik numaraları gibi), gizli rekabet bilgileri ve fikri mülkiyet özellikleri içerebilir. Kayıp veya çalınmış veriler, özellikle de müşteri verileri, marka hasarı, rekabet dezavantajları ve önemli ince ve hatta avlanır.

![Kilit ve anahtar olarak gösterilen Always Encrypted özelliği](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) , Azure SQL veritabanı veya SQL Server veritabanlarında depolanan hassas verileri korumak için tasarlanmış bir özelliktir. Always Encrypted istemcilerin istemci uygulamalarında hassas verileri şifrelemesine olanak tanır ve şifreleme anahtarlarını veritabanı altyapısına (SQL veritabanı veya SQL Server) hiç açığa çıkarmayın.

Always Encrypted, verileri sahibi olan kişiler (ve onu görüntüleyebilir) ile verileri yöneten kişiler (ancak erişimi olmayan) arasında bir ayrım sağlar. Şirket içi veritabanı yöneticilerinin, bulut veritabanı işleçlerinin veya diğer yüksek ayrıcalıklı ancak yetkisiz kullanıcıların şifrelenmiş verilere erişememesini sağlamaya yardımcı olur.

Ayrıca, Always Encrypted şifrelemeyi uygulamalara saydam hale getirir. İstemci bilgisayardaki hassas verileri otomatik olarak şifreleyip çözebilmesi için, istemci bilgisayara Always Encrypted özellikli bir sürücü yüklenir. Sürücü, verileri veritabanı altyapısına geçirmeden önce hassas sütunlardaki verileri şifreler. Sürücü, uygulamanın semantiğinin korunması için sorguları otomatik olarak yeniden yazar. Benzer şekilde, sürücü, sorgu sonuçlarında bulunan şifrelenmiş veritabanı sütunlarında depolanan verilerin saydam olarak şifresini çözer.

## <a name="access-control"></a>Erişim denetimi

Güvenlik sağlamak için, SQL veritabanı aşağıdakileri kullanarak erişimi denetler:

- Bağlantıyı IP adresine göre sınırlayan güvenlik duvarı kuralları.
- Kullanıcıların kimliklerini kanıtlamasını gerektiren kimlik doğrulama mekanizmaları.
- Kullanıcıları belirli eylemlerle ve verilerle sınırlayan Yetkilendirme mekanizmaları.

### <a name="database-access"></a>Veritabanı erişimi

Veri koruma, verilerinize erişimi denetlemeye başlar. Verilerinizi barındıran veri merkezi fiziksel erişimi yönetir. Ağ katmanında güvenliği yönetmek için bir güvenlik duvarı yapılandırabilirsiniz. Ayrıca, kimlik doğrulaması için oturum açma bilgilerini yapılandırarak ve sunucu ve veritabanı rolleri için izinleri tanımlayarak erişimi de denetleyebilirsiniz.

#### <a name="firewall-and-firewall-rules"></a>Güvenlik duvarı ve güvenlik duvarı kuralları

[Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) , Azure ve diğer internet tabanlı uygulamalar için bir ilişkisel veritabanı hizmeti sağlar. Güvenlik duvarları, verilerinizin korunmasına yardımcı olmak üzere, hangi bilgisayarların izinli olduğunu belirtmenize kadar veritabanı sunucunuza tüm erişimi engeller. Güvenlik duvarı, her bir isteğin kaynak IP adresine göre veritabanlarına erişim verir. Daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı güvenlik duvarı kurallarına genel bakış](/azure/sql-database/sql-database-firewall-configure).

Azure SQL veritabanı hizmeti yalnızca TCP bağlantı noktası 1433 üzerinden kullanılabilir. Bilgisayarınızdan bir SQL veritabanına erişmek için, istemci bilgisayar güvenlik duvarınızın 1433 numaralı TCP bağlantı noktasında giden TCP iletişimine izin verdiğinden emin olun. Gelen bağlantılar diğer uygulamalar için gerekmiyorsa, TCP bağlantı noktası 1433 ' de engelleyin.

#### <a name="authentication"></a>Authentication

Kimlik doğrulaması, veritabanına bağlanırken kimliğinizi nasıl kanıtlayacağınızı belirtir. SQL Veritabanı iki kimlik doğrulaması türünü destekler:

-   **SQL Server kimlik doğrulaması**: Mantıksal bir SQL örneği oluşturulduğunda SQL Veritabanı Abone Hesabı adıyla tek bir oturum açma hesabı oluşturulur. Bu hesap [SQL Server kimlik doğrulaması](/azure/sql-database/sql-database-security-overview) (Kullanıcı adı ve parola) kullanarak bağlanır. Bu hesap mantıksal sunucu örneğinde ve bu örneğe ekli tüm kullanıcı hesaplarında yöneticidir. Abone hesabının izinleri kısıtlanamaz. Bu hesaplardan yalnızca biri mevcut olabilir.
-   **Azure Active Directory kimlik doğrulaması**: Azure [ad kimlik doğrulaması](/azure/sql-database/sql-database-aad-authentication) , Azure AD 'de kimlikleri kullanarak Azure SQL veritabanı ve Azure SQL veri ambarı 'na bağlanma mekanizmasıdır. Bu veritabanını, veritabanı kullanıcılarının kimliklerini merkezi olarak yönetmek için kullanabilirsiniz.

![SQL veritabanı ile Azure AD kimlik doğrulaması](./media/database-security-overview/azure-database-fig2.png)

 Azure AD kimlik doğrulamasının avantajları şunlardır:
  - SQL Server kimlik doğrulaması için bir alternatif sağlar.
  - Veritabanı sunucuları genelinde Kullanıcı kimliklerinin kullanımını durdurmaya yardımcı olur ve tek bir yerde parola döndürmeye izin verir.
  - Dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilirsiniz.
  - Tümleşik Windows kimlik doğrulamasını ve Azure AD 'nin desteklediği diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırabilir.

#### <a name="authorization"></a>Authorization

[Yetkilendirme](/azure/sql-database/sql-database-manage-logins) , bir kullanıcının BIR Azure SQL veritabanı içinde neler yapabileceğini belirtir. Kullanıcı hesabınızın veritabanı [rolü üyelikleri](https://msdn.microsoft.com/library/ms189121) ve [nesne düzeyi izinleri](https://msdn.microsoft.com/library/ms191291.aspx)tarafından denetlenir. Yetkilendirme, bir sorumlu tarafından hangi güvenli kılınabilir kaynakların erişebileceğini ve bu kaynaklar için hangi işlemlere izin verileceğini belirleme işlemidir.

### <a name="application-access"></a>Uygulama erişimi

#### <a name="dynamic-data-masking"></a>Dinamik veri maskeleme

Bir çağrı merkezindeki hizmet temsilcisi, çağıranları sosyal güvenlik numarası veya kredi kartı numarasının birkaç basamağıyla tanımlayabilir. Ancak bu veri öğeleri hizmet temsilcisine tam olarak gösterilmemelidir.

Herhangi bir sorgunun sonuç kümesinde, bir sosyal güvenlik numarasının veya kredi kartı numarasının tüm dört basamağının, ancak bir maske kuralı tanımlayabilirsiniz.

![Bir SQL veritabanı ile iş uygulamaları arasında gönderilen bir sayı üzerinde maskeleme](./media/database-security-overview/azure-database-fig3.png)

Başka bir örnek olarak, kişisel olarak tanımlanabilen bilgileri korumak için uygun bir veri maskesi tanımlanabilir. Daha sonra bir geliştirici, uyumluluk düzenlemelerini ihlal etmeden üretim ortamlarını sorun giderme amacıyla sorgulayabilir.

[SQL Veritabanı dinamik veri maskeleme](/azure/sql-database/sql-database-dynamic-data-masking-get-started), hassas verilerin görünürlüğünü ayrıcalık sahibi olmayan kullanıcılardan gizleyerek sınırlar. Dinamik veri maskeleme, Azure SQL veritabanı 'nın V12 sürümü için desteklenir.

[Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking) , hassas verilerin uygulama katmanında en az etkiyle ne kadar açığa çıkarmasını belirleyebilmenizi sağlayarak gizli verilere yetkisiz erişimin engellenmesine yardımcı olur. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde gizleyen ancak veritabanındaki verileri değiştirmeyen ilke tabanlı bir güvenlik özelliğidir.

> [!Note]
> Dinamik veri maskeleme, Azure veritabanı yöneticisi, Sunucu Yöneticisi veya güvenlik müdürü rolleri tarafından yapılandırılabilir.

#### <a name="row-level-security"></a>Satır Düzeyinde Güvenlik

Çok kiracılı veritabanları için başka bir ortak güvenlik gereksinimi, [satır düzeyinde güvenlik](https://msdn.microsoft.com/library/dn765131.aspx). Bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemek için bu özelliği kullanabilirsiniz. (Örnek özellikler grup üyedir ve yürütme bağlamıdır.)

![Satır düzeyi güvenlik, bir kullanıcının bir istemci uygulaması aracılığıyla bir tablodaki satırlara erişmesini sağlar](./media/database-security-overview/azure-database-fig4.png)

Erişim kısıtlama mantığı, başka bir uygulama katmanındaki verilerden uzakta değil, veritabanı katmanında bulunur. Veritabanı sistemi, herhangi bir katmanda veri erişimi her denendiğinde erişim kısıtlamalarını uygular. Bu, güvenlik sisteminizin yüzey alanını azaltarak güvenlik sisteminizi daha güvenilir ve sağlam hale getirir.

Satır düzeyi güvenlik, koşula dayalı erişim denetimi tanıtır. Bu, yöneticinin uygun şekilde belirlediği önemli metaveri veya diğer ölçütlere uyan esnek, merkezi bir değerlendirmeyi ele alır. Koşul, kullanıcının Kullanıcı özniteliklerine göre verilere uygun erişime sahip olup olmadığını belirleme ölçütü olarak kullanılır. Koşul tabanlı erişim denetimi kullanarak etiket tabanlı erişim denetimi uygulayabilirsiniz.

## <a name="proactive-monitoring"></a>Öngörülebilir izleme

SQL veritabanı, *Denetim* ve *tehdit algılama* özellikleri sağlayarak verilerinizin güvenliğinin sağlanmasına yardımcı olur.

### <a name="auditing"></a>Denetim

[Azure SQL veritabanı denetimi](/azure/sql-database/sql-database-auditing-get-started) , veritabanı içinde gerçekleşen olaylar ve değişiklikler hakkında bilgi elde etme yeteneğinizi artırır. Örnekler, verilere göre güncelleştirmeler ve sorgulardır.

SQL veritabanı denetimi, veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar. Denetim, yasal uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve işle ilgili endişeleri veya şüpheli güvenlik ihlallerini gösteren tutarsızlıklar ve bozukluklar elde etmenize yardımcı olabilir. Denetim, uyumluluk standartlarına uygun bir şekilde olanak sağlar ve kolaylaştırır, ancak uyumluluğu garanti etmez.

SQL veritabanı denetimini kullanarak şunları yapabilirsiniz:

- Seçili olayların denetim izlerini **koruyun** . Denetlenecek veritabanı eylemi kategorilerini tanımlayabilirsiniz.
- Veritabanı etkinliğini **raporla** . Etkinlik ve olay raporlamasını hızlı bir şekilde kullanmaya başlamak için önceden yapılandırılmış raporları ve panoları kullanabilirsiniz.
- Raporları **analiz edin** . Şüpheli olayları, olağan dışı etkinlikleri ve eğilimleri bulabilirsiniz.

İki denetim yöntemi vardır:

-   **BLOB denetimi**: Günlükler Azure Blob depolamaya yazılır. Bu daha yeni bir denetim yöntemidir. Daha yüksek performans sağlar, daha yüksek ayrıntı düzeyi nesne düzeyinde denetimi destekler ve daha uygun maliyetli olur.
-   **Tablo denetimi**: Günlükler Azure Tablo depolama alanına yazılır.

### <a name="threat-detection"></a>Tehdit algılama

[Azure SQL veritabanı Için Gelişmiş tehdit koruması](/azure/sql-database/sql-advanced-threat-protection) , olası güvenlik tehditlerini gösteren şüpheli etkinlikleri algılar. Tehdit algılama 'yı, veritabanındaki SQL ınjler gibi şüpheli olaylara yanıt vermek için kullanabilirsiniz. Uyarı sağlar ve kuşkulu olayları araştırmak için Azure SQL veritabanı denetiminin kullanılmasına izin verir.

![Dış saldırgan ve kötü amaçlı bir Insider ile SQL veritabanı ve bir Web uygulaması için tehdit algılama](./media/database-security-overview/azure-database-fig5.jpg)

SQL Gelişmiş tehdit koruması (ATP), veri bulma & sınıflandırması, güvenlik açığı değerlendirmesi ve tehdit algılama gibi bir dizi gelişmiş SQL güvenlik özelliği sağlar. 

- [Veri bulma & sınıflandırması](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Güvenlik Açığı Değerlendirmesi](/azure/sql-database/sql-vulnerability-assessment)  
- [Tehdit algılama](/azure/sql-database/sql-database-threat-detection)

[PostgreSQL Için Azure veritabanı Gelişmiş tehdit koruması](/azure/postgresql/concepts-data-access-and-security-threat-protection) , anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamanıza ve bunlara yanıt vermenize imkan tanıyan yeni bir güvenlik katmanı sağlar. Kullanıcılar şüpheli veritabanı etkinliklerine ve potansiyel güvenlik açıklarına ek olarak anormal veritabanı erişimi ve sorgu düzenlerine bir uyarı alırlar. PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması, uyarıları Azure Güvenlik Merkezi ile tümleştirir. Uyarı türü şunları içerir:

- Olağan dışı konumdan erişim
- Olağandışı Azure veri merkezinden erişim 
- Bilmediğiniz sorumludan erişim 
- Zararlı olabilecek bir uygulamadan erişim 
- Deneme yanılma PostgreSQL için Azure veritabanı kimlik bilgilerini zorlama 

[MySQL Için Azure veritabanı Gelişmiş tehdit koruması](/azure/mysql/concepts-data-access-and-security-threat-protection) , PostgreSQL gelişmiş korumasına benzer bir koruma sağlar.  

## <a name="centralized-security-management"></a>Merkezi güvenlik yönetimi

[Azure Güvenlik Merkezi](https://azure.microsoft.com/documentation/services/security-center/), tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure abonelikleriniz genelinde tümleşik güvenlik izleme ve ilke yönetimi sağlar. Başka türlü fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

[Güvenlik Merkezi](../../security-center/security-center-alerts-data-services.md) , tüm sunucularınızın ve veritabanlarınızın güvenliğine ilişkin görünürlük sağlayarak SQL veritabanındaki verileri korumanıza yardımcı olur. Güvenlik Merkezi ile şunları yapabilirsiniz:

- SQL veritabanı şifreleme ve denetleme ilkelerini tanımlayın.
- Tüm aboneliklerinizde SQL veritabanı kaynaklarının güvenliğini izleyin.
- Güvenlik sorunlarını hızlıca belirleyip düzeltin.
- Uyarıları [Azure SQL veritabanı tehdit](/azure/sql-database/sql-database-threat-detection)algılamalarından tümleştirin.

Güvenlik Merkezi rol tabanlı erişimi destekler.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) , potansiyel olarak hassas verileri otomatik olarak bulur ve sınıflandırır, hassas verileri sınıflandırma öznitelikleriyle kalıcı olarak etiketlemek için etiketleme mekanizması sağlar ve şu şekilde gösterildiği ayrıntılı bir pano sağlar veritabanının sınıflandırma durumu.  

Ayrıca, hassas verileri çıkaran sorguların açıkça denetlenmesi ve verilerin korunabilmesi için SQL sorgularının sonuç kümesi duyarlılığını hesaplar. SQL Information Protection hakkında daha fazla bilgi için bkz. Azure SQL veritabanı veri bulma ve sınıflandırma.

Azure Güvenlik Merkezi 'nde [SQL Information Protection ilkelerini](/azure/security-center/security-center-info-protection-policy) yapılandırabilirsiniz.

## <a name="azure-marketplace"></a>Azure Market

Azure Marketi, yeni ve bağımsız yazılım satıcılarının (ISV'ler) çözümlerini dünyanın dört bir yanındaki Azure müşterilerine sunduğu çevrimiçi bir uygulama ve hizmet marketidir.
Azure Marketi, müşterilere ve iş ortaklarına daha iyi yardımcı olması için Microsoft Azure iş ortağı ekosistemlerini birleştirilmiş bir platformda birleştirir. Azure Marketi 'nde bulunan veritabanı güvenlik ürünlerini görüntülemek için [bir arama çalıştırabilirsiniz](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) .

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veritabanınızın güvenliğini sağlama](/azure/sql-database/sql-database-security-tutorial)
- [Azure Güvenlik Merkezi ve Azure SQL veritabanı hizmeti](/azure/security-center/security-center-sql-database)
- [SQL veritabanı tehdit algılama](/azure/sql-database/sql-database-threat-detection)
- [SQL veritabanı performansını geliştirme](/azure/sql-database/sql-database-performance-tutorial)
