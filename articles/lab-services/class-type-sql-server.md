---
title: Azure SQL veritabanı 'nı yönetmek ve geliştirmek için laboratuvar ayarlama | Azure Lab Services
description: Azure SQL veritabanı 'nı yönetmek ve geliştirmek için laboratuvar ayarlamayı öğrenin.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: fb1b9e3458d08b8387c7f3978ff83c097fad2375
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644023"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>SQL Server yönetmek ve geliştirmek için bir laboratuvar kurun

Bu makalede, Azure Lab Services bir temel SQL Server Yönetim ve geliştirme sınıfı için bir laboratuvarın nasıl ayarlanacağı açıklanır.  Veritabanı kavramları, üniversite içindeki bilgisayar bilimi departmanlarından birçoğu için tanıtım kurslarından biridir. Yapılandırılmış Sorgu Dili (SQL) uluslararası bir standarttır.  SQL, bir veritabanında içerik ekleme, erişme ve yönetme dahil olmak üzere, ilişki veritabanı yönetimine yönelik standart dildir.  Bu en çok, hızlı işleme, kanıtlanmış güvenilirlik, kolaylıklar ve kullanım esnekliği için belirtilmiştir.

Bu makalede, [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)ve [Azure Data Studio](https://github.com/microsoft/azuredatastudio)ile laboratuvarda bir sanal makine şablonu ayarlamayı göstereceğiz.  Bu laboratuvar için, tüm laboratuvarın tek bir paylaşılan [SQL Server veritabanı](../azure-sql/database/sql-database-paas-overview.md) kullanacağız. [Azure SQL veritabanı](../azure-sql/database/sql-database-paas-overview.md) , Azure 'Dan bir hizmet olarak platform (PaaS) veritabanı altyapısı sunuyor.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması

Bu Laboratuvarı ayarlamak için, başlamak üzere bir Azure aboneliğine ve laboratuvar hesabına sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilirsiniz. Yeni laboratuvar hesabı oluşturma hakkında daha fazla bilgi için bkz. [Laboratuvar hesabı kurmak için öğretici](./tutorial-setup-lab-account.md). Ayrıca var olan bir laboratuvar hesabı da kullanabilirsiniz.

### <a name="lab-account-settings"></a>Laboratuvar hesabı ayarları

Laboratuvar hesabı için aşağıdaki tabloda açıklanan ayarları etkinleştirin. Market görüntülerinin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme](specify-marketplace-images.md).

| Laboratuvar hesabı ayarı | Yönergeler |
| ------------------- | ------------ |
| Market görüntüsü | Laboratuvar hesabınızda kullanmak üzere Windows 10 Enterprise N (x64) ' deki ' Visual Studio 2019 Community (son sürüm) ' görüntüsünü etkinleştirin. |

### <a name="shared-resource-configuration"></a>Paylaşılan kaynak yapılandırması

Laboratuvar hizmetlerinde paylaşılan bir kaynak kullanmak için önce sanal ağı ve kaynakların kendisini oluşturmanız gerekir.  Sanal ağı oluşturmak ve bunu laboratuvara bağlamak için [Azure Lab Services paylaşılan kaynakla bir laboratuvar oluşturmayı](how-to-create-a-lab-with-shared-resource.md)izleyin.  Laboratuvar Hizmetleri dışındaki tüm kaynakların ayrı olarak faturalandırılacağını ve laboratuvar maliyeti tahminlerine dahil edilmeyeceğini unutmayın.

>[!WARNING]
>Laboratuvar oluşturulmadan önce bir laboratuvarın paylaşılan kaynakları kurulum olmalıdır.  Laboratuvar oluşturulmadan *önce* VNET [Laboratuvar hesabına](how-to-connect-peer-virtual-network.md) eşlenmez, laboratuvarın paylaşılan kaynağa erişimi olmayacaktır.

İşlerin ağ tarafı işlendiğinin ardından, SQL Server veritabanı oluşturmasına izin verir.  Azure SQL veritabanı için en hızlı dağıtım seçeneği olduğu için [tek bir veritabanı](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) oluşturacağız.  Diğer dağıtım seçenekleri için [elastik havuz](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [yönetilen örnek](../azure-sql/managed-instance/instance-create-quickstart.md)veya [SQL sanal makinesi](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)oluşturun.

1. Azure portal menüsünden **Yeni kaynak oluştur**' u seçin.
2. **SQL veritabanı** ' nı seçin ve **Oluştur** düğmesine tıklayın.
3. **SQL veritabanı oluştur** formunun **temel bilgiler** sekmesinde, veritabanı için kaynak grubunu seçin.  *SQLDB-RG* kullanacağız.
4. **Veritabanı adı** için *classlabdb* girin.
5. **Sunucu** ayarı altında **Yeni oluştur** ' a tıklayarak veritabanını tutacak yeni bir sunucu oluşturun.
6. **Yeni sunucu** açılır çubuğunda sunucu adını girin.  *Classlabdbserver* kullanacağız.  Sunucu adı genel olarak benzersiz olmalıdır.
7. **Sunucu Yöneticisi oturum açma** için *azureuser* girin.
8. Hatırlayabileceğiniz bir parola girin.  Parola en az sekiz karakter uzunluğunda olmalı ve özel karakterler içermelidir.
9. **Konum** için bölge seçin.  Mümkünse, gecikme süresini en aza indirmek için laboratuvar hesabı ve eşlenmiş VNET ile aynı konumu girin.
10. **SQL veritabanı oluştur** formuna geri dönmek için **Tamam** ' ı tıklatın.
11. **İşlem + depolama** ayarı altındaki **veritabanı yapılandırma** bağlantısını tıklatın.
12. Sınıf için gerektiğinde veritabanı ayarlarını değiştirin.  Sağlanan ve sunucusuz seçenekler arasında seçim yapabilirsiniz.  Bu örnek için, en fazla 4, en düşük sanal çekirdekler olan 1. En az 1 saat içinde, oto duraklama ayarını saklayacağız. **Uygula**’ya tıklayın.
13. **İleri: ağ** düğmesine tıklayın.
14. Ağ sekmesinde, **bağlantı yöntemi** için özel uç nokta ' ı seçin.
15. **Özel uç noktalar** bölümünde **Özel uç nokta Ekle**' ye tıklayın.
16. **Özel uç nokta oluştur** açılır çubuğunda, sanal ağınızla aynı kaynak grubunu laboratuvar hesabına eşit olarak seçin.
17. **Konum** için, sanal ağla aynı konumu seçin.
18. **Ad** için, *labsql-Endpt* yazın.
19. Hedef alt kaynağı SqlServer olarak ayarlı bırakın.
20. **Sanal ağ** için, laboratuvar hesabıyla eşlenmiş sanal ağı seçin.
21. **Alt ağ** için, uç noktanın barındırılmasını istediğiniz alt ağ ' ı seçin.  Uç noktaya atanan IP, bu alt ağa atanan aralıktan olacaktır.
22. **Özel DNS Ile tümleştir** 'ı **Hayır** olarak ayarlayın. Kolaylık olması için, Azure 'un DNS DNS bölgesi veya kendi DNS sunucularımız üzerinden DNS 'i kullanacağız.
23. **Tamam**'a tıklayın.
24. **İleri**' ye tıklayın.
25. **Varolan verileri kullan** ayarını için **örnek**' i seçin.  AdventureWorksLT veritabanının verileri veritabanı oluşturulduğunda kullanılacaktır.
26. **Gözden geçir ve oluştur**’a tıklayın.
27. **Oluştur**’a tıklayın.

SQL veritabanı dağıtımı başarıyla tamamlandıktan sonra Laboratuvarı oluşturabilir ve laboratuvar şablonu makinesine yazılım yükleyebilirsiniz.

### <a name="lab-settings"></a>Laboratuvar ayarları

Bir sınıf Laboratuvarı ayarlarken aşağıdaki tablodaki ayarları kullanın. Sınıf Laboratuvarı oluşturma hakkında daha fazla bilgi için bkz. [bir derslik Laboratuvarı ayarlama öğreticisi](tutorial-setup-classroom-lab.md).

| Laboratuvar ayarları | Değer/yönergeler |
| ------------ | ------------------ |
| Sanal makine boyutu | Orta. Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir. |
| Sanal makine görüntüsü | Windows 10 Enterprise N (x64) üzerinde Visual Studio 2019 Community (en son sürüm) |

Artık laboratuvarımız oluşturduğumuza göre, şablon makinesini ihtiyaç duyduğumuz yazılımlarla değiştirelim.

## <a name="visual-studio"></a>Visual Studio

Yukarıda seçilen görüntü, [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/)içerir.  Tüm iş yükleri ve araç kümeleri görüntüde zaten yüklü.  İstediğiniz [isteğe bağlı araçları yüklemek](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) için Visual Studio yükleyicisi kullanın.  Community sürümünün kilidini açmak için [Visual Studio 'Da oturum açın](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019&preserve-view=true#how-to-sign-in-to-visual-studio) .

Visual Studio, SQL Server Veri Araçları (SSDT) içeren **veri depolama ve işleme** aracı kümesini içerir.  SSDT 'nin özellikleri hakkında daha fazla bilgi için bkz. [SQL Server veri araçları genel bakış](/sql/ssdt/sql-server-data-tools).  Sınıfın paylaşılan SQL Server bağlantısını doğrulamak için, bkz. [bir veritabanına bağlanma ve var olan nesnelere gözatmaya](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects)yönelik. İstenirse, SQL Server örneğinize bağlanabilecek [izin verilen bilgisayarlar listesine](../azure-sql/database/firewall-configure.md) şablon makinesini ekleyin.

Visual Studio, mobil iş yükleri & **Web & bulut** ve **Masaüstü** gibi birçok iş yükünü destekler.  Bu iş yüklerinin her ikisi de veri kaynağı olarak SQL Server destekler. SQL Server ASP.NET Core kullanma hakkında daha fazla bilgi için bkz. Azure App Service öğreticide [ASP.NET Core ve SQL veritabanı uygulaması oluşturma](../app-service/tutorial-dotnetcore-sqldb-app.md) .  [Xamarin](/xamarin) UYGULAMASıNDAN bir SQL veritabanına bağlanmak için [System. Data. SqlClient](/dotnet/api/system.data.sqlclient) kitaplığını kullanın.

## <a name="install-azure-data-studio"></a>Azure Data Studio yüklensin

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) , Windows, MacOS ve Linux 'ta şirket içi ve bulut veri platformlarını kullanan veri uzmanlarına yönelik çoklu veritabanı, platformlar arası masaüstü ortamıdır.

1. [Windows için Azure Data Studio *sistem* yükleyicisini](https://go.microsoft.com/fwlink/?linkid=2127432)indirin. Desteklenen diğer işletim sistemlerine yönelik yükleyicileri bulmak için [Azure Data Studio](/sql/azure-data-studio/download) indirme sayfasına gidin.
2. **Lisans Sözleşmesi** sayfasında **sözleşmeyi kabul ediyorum**' u seçin. **İleri**’ye tıklayın.
3. **Hedef Konumu Seç** sayfasında **İleri**'ye tıklayın.
4. **Başlat Menüsü Klasörü Seç** sayfasında **İleri**'ye tıklayın.
5. **Ek görevler seçin** sayfasında masaüstü simgesini istiyorsanız **Masaüstü simgesi oluştur** ' u işaretleyin.  **İleri**’ye tıklayın.
6. Yüklenmeye **hazırsanız** **İleri**' ye tıklayın.
7. Yükleyicinin çalışmasını bekleyin.  **Finish (Son)** düğmesine tıklayın.

Artık Azure Data Studio yüklediğimiz için Azure SQL veritabanı bağlantısını kuralım.

1. Azure Data Studio için **hoş geldiniz** sayfasında, **Yeni bağlantı** bağlantısına tıklayın.
2. **Bağlantı ayrıntıları** kutusunda gerekli bilgileri girin.
    - **Sunucuyu** *classlabdbserver.Database.Windows.net* olarak ayarla
    - **Kullanıcı** adını *azureuser* olarak ayarla
    - Veritabanını **oluşturmak için kullanılan parolayı belirleyin.**
    - **Parolayı anımsa**' yı denetleyin.
    - **Veritabanı** için *classlabdb*' yi seçin.
3. **Bağlan**'a tıklayın.

## <a name="install-sql-server-management-studio"></a>SQL Server Management Studio yüklensin

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) , HERHANGI bir SQL altyapısını yönetmeye yönelik tümleşik bir ortamdır.  SSMS, veritabanı yöneticilerinin veri altyapısını dağıtmak, izlemek ve yükseltmek için kullanılan bir araçtır.

1. [SQL Server Management Studio indirin](https://aka.ms/ssmsfullsetup). İndirildikten sonra yükleyiciyi başlatın.
2. **Hoş geldiniz** sayfasında, **yükler**' e tıklayın.
3. **Kurulum tamamlandı** sayfasında **Kapat**' a tıklayın.
4. SQL Server Management Studio başlatın.  
5. **Bağımlılık yapılandırma işlemi** sayfasında **Kapat**' a tıklayın.

SSMS yüklü değil, [bir SQL Server bağlanıp sorgulama](/sql/ssms/tutorials/connect-query-sql-server)yapabilirsiniz. Bağlantıyı ayarlarken aşağıdaki değerleri kullanın:

- Sunucu türü: veritabanı altyapısı
- Sunucu adı: *classlabdbserver.Database.Windows.net*
- Kimlik doğrulaması: SQL Server kimlik doğrulaması
- Oturum aç: *azureuser*
- Parola: veritabanını oluşturmak için kullanılan parola.

## <a name="cost-estimate"></a>Maliyet tahmini

Bu sınıf için olası bir maliyet tahminini ele alalım. Tahmin, SQL Server çalıştırmanın maliyetini içermez.  Veritabanı fiyatlandırmasıyla ilgili geçerli Ayrıntılar için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database) .

25 öğrencilerden oluşan bir sınıf kullanacağız. 20 saatlik zamanlanan sınıf zamanı vardır. Ayrıca, her öğrenci, zamanlanan sınıf zamanı dışında ev ödevleri veya atamalar için 10 saatlik kota alır. Seçtiğiniz sanal makine boyutu, 42 laboratuvar birimi olan orta idi.

Bu sınıf için olası bir maliyet tahmini örneği aşağıda verilmiştir:

25 öğrenci \* (20 zamanlanan saat \+ 10 kota saati) * 0,42 ABD doları/315,00 ABD Doları

>[!IMPORTANT]
>Maliyet tahmini yalnızca örnek amaçlıdır. Fiyatlandırma hakkındaki güncel Ayrıntılar için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma, yönetme ve yayımlama](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)