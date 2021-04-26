---
title: SaaS Multi-tenant içinde sağlama
description: Azure SQL veritabanı çok kiracılı SaaS uygulamasında yeni kiracılar sağlamayı ve kataloglarını öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793339"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Parçalı bir çok kiracılı Azure SQL veritabanı kullanarak bir SaaS uygulamasında yeni kiracılar sağlama ve kataloglandırın
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, yeni kiracıların *çok kiracılı bir parçalı veritabanı* modelinde veya düzeninde sağlanması ve kataloglandırılan ele alınmaktadır.

Bu makalede iki ana bölüm bulunur:

- Yeni kiracılar sağlama ve kataloglamanın [kavramsal tartışması](#goto_2_conceptual) .

- Sağlamayı ve kataloglamaları gerçekleştiren PowerShell betik kodunu vurgulayan [öğretici](#goto_1_tutorial) .
  - Öğretici, çok kiracılı parçalı veritabanı düzenine uyarlanan Wingtip bilet SaaS uygulamasını kullanır.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Veritabanı Kalıbı

Bu bölümde ve daha fazla bilgi için, çok kiracılı parçalı veritabanı deseninin kavramlarını tartışın.

Bu çok kiracılı parçalı bir modelde, her bir veritabanının içindeki tablo şemaları, kiracı verilerini depolayan tabloların birincil anahtarındaki bir kiracı anahtarı içerir. Kiracı anahtarı, her veritabanının 0, 1 veya çok sayıda kiracıyı depolamasına olanak sağlar. Parçalı veritabanlarının kullanımı, uygulama sisteminin çok sayıda kiracıyı desteklemesini kolaylaştırır. Herhangi bir kiracının tüm verileri tek bir veritabanında depolanır. Çok sayıda kiracı, parçalı veritabanları arasında dağıtılır. Bir Katalog veritabanı, her bir kiracının veritabanına eşlemesini depolar.

#### <a name="isolation-versus-lower-cost"></a>Yalıtım ve düşük maliyet karşılaştırması

Tek başına bir veritabanı olan kiracı, yalıtımın avantajlarından yararlanır. Kiracı, veritabanının diğer kiracılardaki etkiyle sınırlandırılmadan önceki bir tarihe geri yüklenmesini sağlayabilir. Veritabanı performansı, tek bir kiracının iyileştirilmesi için, diğer kiracılar ile tehlikeye girmeden yeniden iyileştirebilecek şekilde ayarlanabilir. Bu sorun, yalıtımın bir veritabanını diğer kiracılar ile paylaşması için BT maliyetlerinden daha fazla bilgi sağlar.

Yeni bir kiracı sağlandığında, başka kiracılar ile bir veritabanını paylaşabilir veya kendi yeni veritabanına yerleştirilebilir. Daha sonra fikrinizi değiştirebilir ve veritabanını başka bir duruma taşıyabilirsiniz.

Birden çok kiracılar ve tek kiracılar içeren veritabanları, her bir kiracının maliyetini veya yalıtımını iyileştirmek için aynı SaaS uygulamasında karıştırılır.

   ![Kiracı kataloğuna sahip çok kiracılı veritabanı uygulaması](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Kiracı kataloğu kalıbı

Her birinde en az bir kiracı içeren iki veya daha fazla veritabanınız olduğunda, uygulamanın hangi veritabanının geçerli ilgi alanını depoladığını bulacak bir yolu olmalıdır. Katalog veritabanı bu eşlemeyi depolar.

#### <a name="tenant-key"></a>Kiracı anahtarı

Her kiracı için, Wingtip uygulaması, kiracı anahtarı olan benzersiz bir anahtar türetilebilir. Uygulama, kiracı adını Web sayfası URL 'sinden ayıklar. Uygulama, anahtarı almak için adı karma hale getirir. Uygulama, kataloğa erişmek için anahtarı kullanır. Katalog, kiracının depolandığı veritabanıyla ilgili bilgileri çapraz başvurular. Uygulama, bağlanmak için veritabanı bilgilerini kullanır. Diğer kiracı anahtar düzenleri de kullanılabilir.

Bir kataloğun kullanılması, bir kiracı veritabanının adının veya konumunun uygulamayı kesintiye uğratmadan sağlamaktan sonra değiştirilmesini sağlar. Çok kiracılı bir veritabanı modelinde, Katalog bir kiracıyı veritabanları arasında taşımayı de karşılar.

#### <a name="tenant-metadata-beyond-location"></a>Konumun ötesinde kiracı meta verileri

Katalog, bir kiracının bakım veya diğer eylemler için çevrimdışı olup olmadığını da belirtebilir. Ve Katalog, aşağıdaki öğeler gibi ek kiracı veya veritabanı meta verilerini depolamak için Genişletilebilir:
- Bir veritabanının hizmet katmanı veya sürümü.
- Veritabanı şemasının sürümü.
- Kiracı adı ve SLA 'Sı (hizmet düzeyi sözleşmesi).
- Uygulama yönetimini, müşteri desteğini veya DevOps süreçlerini etkinleştirme bilgileri.

Katalog, analiz amaçları için çapraz kiracı raporlamayı, şema yönetimini ve veri ayıklamayı etkinleştirmek için de kullanılabilir.

### <a name="elastic-database-client-library"></a>Elastik veritabanı Istemci kitaplığı

Wingtip 'de, katalog, *tenantcatalog* veritabanında uygulanır. *Tenantcatalog* , [elastik veritabanı istemci kitaplığı 'nın (Edcl)](elastic-database-client-library.md)parça yönetimi özellikleri kullanılarak oluşturulur. Kitaplık, bir uygulamanın bir veritabanında depolanan parça *haritasını* oluşturmasını, yönetmesini ve kullanmasını sağlar. Parça Haritası, kiracı anahtarına çapraz başvuru, yani parçalı veritabanı.

Kiracı sağlama sırasında EDCL işlevleri, uygulama veya PowerShell komut dosyalarından, parça eşlemesinde giriş oluşturmak için kullanılabilir. Daha sonra EDCL işlevleri doğru veritabanına bağlanmak için kullanılabilir. EDCL, katalog veritabanındaki trafiği en aza indirmek ve bağlanma sürecini hızlandırmak için bağlantı bilgilerini önbelleğe alır.

> [!IMPORTANT]
> Katalog veritabanındaki verileri doğrudan erişimli *olarak düzenlemeyin!* Yüksek veri bozulması riski nedeniyle doğrudan güncelleştirmeler desteklenmez. Bunun yerine, yalnızca EDCL API 'Leri kullanarak eşleme verilerini düzenleyin.

## <a name="tenant-provisioning-pattern"></a>Kiracı sağlama kriteri

#### <a name="checklist"></a>Denetim Listesi

Paylaşılan veritabanının mevcut bir paylaşılan veritabanına yeni bir kiracı sağlamak istediğinizde aşağıdaki soruları sormanız gerekir:
- Yeni kiracı için yeterli alan kalmadı mu?
- Yeni kiracı için gerekli başvuru verilerini içeren tablolar var mı veya veriler eklenebilir mi?
- Yeni kiracı için temel şemanın uygun varyasyonuna sahip mi?
- Bu, uygun coğrafi konumda yeni kiracıya yakın midir?
- Bu, yeni kiracının doğru hizmet katmanında mi?

Yeni kiracının kendi veritabanında yalıtılması istediğinizde, kiracının belirtimlerini karşılayacak şekilde oluşturabilirsiniz.

Sağlama tamamlandıktan sonra, kiracıyı kataloğa kaydetmeniz gerekir. Son olarak, kiracı eşlemesi uygun parçaya başvuracak şekilde eklenebilir.

#### <a name="template-database"></a>Şablon veritabanı

SQL betikleri çalıştırarak, bacpac dağıtarak veya bir şablon veritabanını kopyalayarak veritabanını sağlayın. Wingtip Apps, yeni kiracı veritabanları oluşturmak için bir şablon veritabanını kopyalar.

Tüm uygulamalar gibi, Wingtip, zaman içinde gelişecektir. Her zaman, Wingtip veritabanında değişiklik yapılmasını gerektirir. Değişiklikler aşağıdaki öğeleri içerebilir:
- Yeni veya değiştirilmiş şema.
- Yeni veya değiştirilmiş başvuru verileri.
- En iyi uygulama performansını sağlamak için rutin veritabanı bakım görevleri.

SaaS uygulamasıyla, bu değişikliklerin oldukça büyük olabilecek bir kiracı veritabanı filosunda eşgüdümlü şekilde dağıtılması gerekir. Bu değişikliklerin gelecekteki kiracı veritabanlarında olması için, sağlama sürecine dahil olmaları gerekir. Bu zorluk, [şema yönetimi öğreticisinde](saas-tenancy-schema-management.md)daha fazla araştırılabilir.

#### <a name="scripts"></a>Betikler

Bu öğreticideki kiracı sağlama betikleri aşağıdaki senaryolardan her ikisini de destekler:
- Diğer kiracılar ile paylaşılan mevcut bir veritabanına kiracı sağlama.
- Kiracının kendi veritabanına sağlanması.

Kiracı verileri daha sonra, Katalog parça eşlemesinde başlatılır ve kaydedilir. Örnek uygulamada, birden fazla kiracı içeren veritabanlarına *tenants1* veya *tenants2* gibi genel bir ad verilir. Tek bir kiracı içeren veritabanlarına kiracının adı verilir. Bir kataloğun kullanımı, veritabanına herhangi bir adın atanmasına izin verdiğinden, örnekte kullanılan belirli adlandırma kuralları, düzenin kritik bir parçası değildir.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Öğretici başladı

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Çok kiracılı bir veritabanına kiracı sağlama
> * Tek kiracılı bir veritabanına kiracı sağlama
> * Birden çok kiracılı ve tek kiracılı veritabanlarına kiracı grubu sağlama
> * Bir katalogda bir veritabanını ve kiracı eşlemesini kaydetme

#### <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

- Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](/powershell/azure/get-started-azureps)

- Wingtip bilet SaaS çok kiracılı veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. Wingtip biletleri SaaS çok kiracılı veritabanı uygulaması dağıtma ve araştırma](./saas-multitenantdb-get-started-deploy.md)

- Wingtip betikleri ve kaynak kodunu alın:
    - Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub deposunda mevcuttur.
    - Wingtip betikleri indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) bakın.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Diğer kiracılar ile *paylaşılan* bir veritabanına kiracı sağlama

Bu bölümde, sağlama için PowerShell betikleri tarafından gerçekleştirilen başlıca eylemlerin bir listesini görürsünüz. Daha sonra koddaki eylemleri görmek için betiklerin içinde ilerlemek üzere PowerShell ıSE hata ayıklayıcısını kullanırsınız.

#### <a name="major-actions-of-provisioning"></a>Sağlamanın ana eylemleri

Aşağıda adım adım iş akışının temel öğeleri verilmiştir:

- **Yeni kiracı anahtarını hesapla**: kiracı adından kiracı anahtarı oluşturmak için bir karma işlev kullanılır.
- **Kiracı anahtarının zaten mevcut olup olmadığını denetleyin**: anahtarın zaten kayıtlı olmadığından emin olmak için Katalog denetlenir.
- **Varsayılan kiracı veritabanında kiracıyı Başlat**: Kiracı veritabanı yeni kiracı bilgilerini eklemek üzere güncelleştirilir.
- **Kiracıyı katalogda kaydet**: yeni kiracı anahtarı ile mevcut tenants1 veritabanı arasındaki eşleme kataloğa eklenir.
- **Kiracı adını bir katalog uzantısı tablosuna ekleyin**: mekan adı katalogdaki kiracılar tablosuna eklenir.  Bu ek, Katalog veritabanının uygulamaya özgü ek verileri destekleyecek şekilde nasıl uzatımda olduğunu gösterir.
- **Yeni kiracı Için olayları aç sayfası**: *Bushsollow maves* olayları sayfası tarayıcıda açılır.

   ![Yeni bir kiracının Olaylar sayfasını gösteren ekran görüntüsü.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Hata ayıklayıcı adımları

Wingtip uygulamasının paylaşılan bir veritabanında yeni kiracı sağlamayı nasıl uyguladığını anlamak için, bir kesme noktası ekleyin ve iş akışında adım adım ilerleyin:

1. *PowerShell ISE*'de açın... \\ Öğrenme modülleri \\ provisionkiracılar \\ *Demo-ProvisionTenants.ps1* ve aşağıdaki parametreleri ayarladı:
   - **$TenantName**  =  **Bushwillow Maves**, yeni bir mekanın adı.
   - **$VenueType**  =  **mavilere**, önceden tanımlanmış yer türlerinden biri: maves, classicalmusic, dans, CAI, judo, otobisiklet, çok amaçlı, Opera, rockmusic, futbol (küçük harf, boşluk yok).
   - **$DemoScenario**  =  **1**, diğer kiracılarla paylaşılan bir veritabanında kiracı sağlamak için.

2. İmlecinizi satır 38 ' de istediğiniz yere yerleştirerek bir kesme noktası ekleyin: *New-Tenant '* ve ardından **F9** tuşuna basın.

   ![Yeni kiracı içeren çizgiyi vurgulayan ekran görüntüsü.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. **F5** tuşuna basarak betiği çalıştırın.

4. Komut dosyası yürütme kesme noktasında durduktan sonra, koda dönmek için **F11** tuşuna basın.

   ![Ekran görüntüsü, hata ayıklama menüsü açık ve seçili öğesine adımla birlikte Windows PowerShell ISE gösterir.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Komut dosyasının yürütülmesini, **hata ayıklama** menü seçenekleri, **F10** ve **F11** kullanarak, çağrılan işlevleri üzerinde veya içine adımla izleyin.

PowerShell betiklerinde hata ayıklama hakkında daha fazla bilgi için bkz. [PowerShell betiklerinde çalışma ve hata ayıklama hakkında ipuçları](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>*Kendi* veritabanında kiracı sağlama

#### <a name="major-actions-of-provisioning"></a>Sağlamanın ana eylemleri

Aşağıda, betiği izlerken adım adım iş akışının temel öğeleri verilmiştir:

- **Yeni kiracı anahtarını hesapla**: kiracı adından kiracı anahtarı oluşturmak için bir karma işlev kullanılır.
- **Kiracı anahtarının zaten mevcut olup olmadığını denetleyin**: anahtarın zaten kayıtlı olmadığından emin olmak için Katalog denetlenir.
- **Yeni bir kiracı veritabanı oluşturun**: veritabanı, Kaynak Yöneticisi şablonu kullanılarak *basetenantdb* veritabanı kopyalanarak oluşturulur.  Yeni veritabanı adı, kiracının adına göre belirlenir.
- **Kataloğa veritabanı ekleme**: yeni kiracı veritabanı katalogda parça olarak kaydedilir.
- **Varsayılan kiracı veritabanında kiracıyı Başlat**: Kiracı veritabanı yeni kiracı bilgilerini eklemek üzere güncelleştirilir.
- **Kiracıyı katalogda kaydet**: yeni kiracı anahtarı ve *sequoıafutbol* veritabanı arasındaki eşleme kataloğa eklenir.
- **Kiracı adı kataloğa eklenir**: mekan adı, katalogdaki kiracılar uzantı tablosuna eklenir.
- **Yeni kiracı Için olayları aç sayfası**: *Sequoia futbol* olayları sayfası tarayıcıda açılır.

   ![etkinlikler](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Hata ayıklayıcı adımları

Şimdi kendi veritabanında bir kiracı oluştururken betik sürecini gözden geçir:

1. Hala devam ediyor... \\ Öğrenme modülleri \\ provisionkiracılar \\ *Demo-ProvisionTenants.ps1* aşağıdaki parametreleri ayarladı:
   - **$TenantName**  =  **Sequoia futbol**, yeni bir mekanın adı.
   - **$VenueType**  =  **futbol**, önceden tanımlanmış yer türlerinden biri: maves, classicalmusic, dans, CAI, judo, otobisiklet, çok amaçlı, Opera, rockmusic, futbol (küçük harf, boşluk yok).
   - **$DemoScenario**  =  **2**, bir kiracıyı kendi veritabanına sağlamak için.

2. İmlecinizi satır 57 ' de istediğiniz yere yerleştirerek yeni bir kesme noktası ekleyin: *& &nbsp; $PSScriptRoot \new-tenantanddatabase '* ve **F9** tuşuna basın.

   ![kesme noktası](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. **F5** tuşuna basarak betiği çalıştırın.

4. Komut dosyası yürütme kesme noktasında durduktan sonra, koda dönmek için **F11** tuşuna basın.  Çalıştırmayı izlemek için **F10** ve **F11** tuşlarını kullanın ve işlevleri adım adım içine alarak adımları izleyin.

## <a name="provision-a-batch-of-tenants"></a>Kiracılar toplu iş sağlama

Bu alıştırma, 17 kiracılar toplu işi sağlar. Diğer Wingtip bilet öğreticilerini başlatmadan önce bu toplu iş topluluğunu sağlamanız önerilir. bu nedenle, birlikte çalışmak için daha fazla veritabanı vardır.

1. *PowerShell ISE*'de açın... \\ Öğrenme modülleri \\ provisionkiracılar \\ *Demo-ProvisionTenants.ps1* ve *$DemoScenario* parametresini 4 olarak değiştirir:
   - **$DemoScenario**  =  **4**, paylaşılan bir veritabanına kiracıların bir toplu iş sağlamasını sağlamak.

2. **F5** tuşuna basıp betiği çalıştırın.

### <a name="verify-the-deployed-set-of-tenants"></a>Dağıtılan kiracılar kümesini doğrulama

Bu aşamada, paylaşılan bir veritabanına ve kendi veritabanlarına dağıtılan kiracılara dağıtılan kiracılar karışımı vardır. Azure portal oluşturulan veritabanlarını denetlemek için kullanılabilir. [Azure Portal](https://portal.azure.com), SQL Server listesine giderek **tenants1-MT- \<USER\>** Server ' ı açın.  **SQL veritabanları** listesi, paylaşılan **tenants1** veritabanını ve kendi veritabanında bulunan kiracılar için veritabanlarını içermelidir:

   ![veritabanı listesi öğesine tıklayın](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Azure portal Kiracı veritabanlarını gösterirken, bu, paylaşılan veritabanının *içindeki* kiracılar ' ı görmenizi sağlar. Kiracıların tam listesi, Wingtip 'nin **Olaylar hub** 'ı Web sayfasında ve kataloğa göz atarak görülebilir.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Wingtip bilet olayları hub 'ı kullanma sayfası

Tarayıcıda Olay Hub 'ı sayfasını açın (http: Events. Wingtip-mt. \<USER\> . trafficmanager.net)

#### <a name="using-catalog-database"></a>Katalog veritabanını kullanma

Kiracıların tam listesi ve her biri için karşılık gelen veritabanı katalogda bulunur. Kiracı adını veritabanı adına birleştiren bir SQL görünümü sağlanır. Görünüm, katalogda depolanan meta verileri genişletmenin değerini gösterir.
- SQL görünümü, tenantcatalog veritabanında kullanılabilir.
- Kiracı adı kiracılar tablosunda depolanır.
- Veritabanı adı parça yönetim tablolarında depolanır.

1. SQL Server Management Studio (SSMS) içinde, Katalog-MT konumundaki kiracılar sunucusuna bağlanın **. \<USER\> database.windows.net**, Login = **Developer** ve Password = **P \@ ssword1**

    ![SSMS bağlantı iletişim kutusu](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. SSMS Nesne Gezgini, *tenantcatalog* veritabanındaki görünümlere gidin.

3. Sık *kullanılan görünüm '* e sağ tıklayın ve **en üstteki 1000 satırı seç**' i seçin. Farklı kiracılar için kiracı adı ve veritabanı arasındaki eşlemeyi aklınızda yapın.

    ![SSMS 'de Extendedkiracılar görünümü](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Diğer sağlama düzenleri

Bu bölümde, diğer ilginç sağlama desenleri ele alınmaktadır.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Elastik havuzlarda veritabanlarını önceden sağlama

Ön sağlama işlemi, elastik havuzlar kullanılırken, havuzun veritabanları için değil, faturalandırmayla ilgili olgusuna yararlanır. Bu nedenle, veritabanları ek maliyet ödemeden gerekmeden önce elastik bir havuza eklenebilir. Bu ön visioning, bir kiracının veritabanına sağlanması için geçen süreyi önemli ölçüde azaltır. Önceden sağlanan veritabanlarının sayısı, bir arabelleğin beklenen sağlama oranına uygun tutulması için gerektiği şekilde ayarlanabilir.

#### <a name="auto-provisioning"></a>Otomatik sağlama

Otomatik sağlama modelinde, gerektiğinde sunucu, havuz ve veritabanlarını otomatik olarak sağlamak için adanmış bir sağlama hizmeti kullanılır. Bu Otomasyon, elastik havuzlardaki veritabanlarının ön sağlamasını içerir. Veritabanları kullanımdan çıkarıldı ve silinirse, bu, elastik havuzlarda oluşturulan boşluklar, sağlama hizmeti tarafından istenildiği gibi doldurulabilir.

Bu otomatik hizmet türü basit veya karmaşık olabilir. Örneğin, Otomasyon birden çok coğrafi bölge genelinde sağlamayı işleyebilir ve olağanüstü durum kurtarma için coğrafi çoğaltma ayarlayabilir. Otomatik sağlama düzeniyle, bir istemci uygulaması veya betiği, bir sağlama hizmeti tarafından işlenmek üzere bir sıraya sağlama isteği gönderir. Betik daha sonra tamamlamayı tespit etmek üzere yoklama yapılır. Ön sağlama kullanılıyorsa, istekler hızla işlenir, ancak arka plan hizmeti bir değiştirme veritabanının sağlamasını yönetir.

## <a name="additional-resources"></a>Ek kaynaklar

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastik veritabanı istemci kitaplığı](elastic-database-client-library.md)
- [Windows PowerShell ISE betiklerin hatalarını ayıklama](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Paylaşılan çok kiracılı bir veritabanına ve kendi veritabanına tek bir yeni kiracı sağlama
> * Ek kiracı grubu sağlama
> * Kiracılar sağlama ve kataloğa kaydetme ayrıntılarının ayrıntılarını adım adım yapın

[Performans izleme öğreticisini](./saas-multitenantdb-performance-monitoring.md)deneyin.