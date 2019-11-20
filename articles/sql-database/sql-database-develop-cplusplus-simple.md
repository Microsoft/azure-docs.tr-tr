---
title: C ve kullanarak SQL veritabanı 'na bağlanmaC++
description: Azure SQL veritabanı ile bulutta güçlü bir ilişkisel veritabanı tarafından desteklenen C++ ve desteklenen modern bir uygulama oluşturmak için bu hızlı başlangıç içindeki örnek kodu kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: fb6094ec418d2b212759bddd2c4d49c7e6193849
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690709"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>C ve kullanarak SQL veritabanı 'na bağlanmaC++

Bu gönderi, C ve C++ GELIŞTIRICILERIN Azure SQL DB 'ye bağlanmasına çalışan geliştiricilere yönelik olarak tasarlanmıştır. En iyi şekilde yakalanan bölüme geçebilmeniz için bölümlere ayrılmıştır.

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ öğreticisi için Önkoşullar

Aşağıdaki öğelere sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [Ücretsiz Azure Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* [Visual Studio](https://www.visualstudio.com/downloads/). Bu örneği derlemek ve C++ çalıştırmak için dil bileşenlerini yüklemelisiniz.
* [Visual Studio Linux geliştirme](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Linux üzerinde geliştiriyorsanız, Visual Studio Linux uzantısını da yüklemelisiniz.

## <a id="AzureSQL"></a>Sanal makinelerde Azure SQL veritabanı ve SQL Server
Azure SQL Microsoft SQL Server kurulmuştur ve yüksek kullanılabilirliğe sahip, performanslı ve ölçeklenebilir bir hizmet sağlamak için tasarlanmıştır. Şirket içinde çalışan özel veritabanınız üzerinde SQL Azure kullanmanın birçok avantajı vardır. SQL Azure ile veritabanınızı yüklemenize, ayarlamanıza, sürdürmenize veya yönetmenize gerek kalmaz, ancak veritabanınızın yalnızca içeriğini ve yapısını oluşturmanız gerekmez. Hataya dayanıklılık ve artıklık gibi veritabanları hakkında kaygılandığımız tipik şeyler, yerleşik olarak bulunur.

Azure Şu anda SQL Server iş yüklerini barındırmak için iki seçeneğe sahiptir: Azure SQL veritabanı, hizmet olarak veritabanı ve sanal makinelerde (VM) SQL Server. Bulut hizmetlerinin sağladığı maliyet tasarruflarından ve performans iyileştirmesinden faydalanmak için, yeni bulut tabanlı uygulamalar için en iyi yöntem olan Azure SQL veritabanı, bu ikisi arasındaki farklılıklar hakkında ayrıntı getirmeyecektir. Şirket içi uygulamalarınızı buluta geçirmeyi veya genişletmeyi düşünüyorsanız, Azure sanal makinesindeki SQL Server sizin için daha iyi çalışabilir. Bu makaleye yönelik şeyleri basit tutmak için bir Azure SQL veritabanı oluşturalım.

## <a id="ODBC"></a>Veri erişimi teknolojileri: ODBC ve OLE DB
Azure SQL DB 'ye bağlanmak farklı değildir ve şu anda veritabanlarına bağlanmanın iki yolu vardır: ODBC (açık veritabanı bağlantısı) ve OLE DB (nesne bağlama ve katıştırma veritabanı). Son yıllarda, Microsoft [Yerel ilişkisel veri erişimi Için ODBC](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/)ile hizalanır. ODBC nispeten basittir ve OLE DB çok daha hızlıdır. Burada yalnızca desteklenmediği uyarısıyla, ODBC 'nin eski bir C stili API kullanması olur.

## <a id="Create"></a>1. Adım: Azure SQL veritabanınızı oluşturma
Örnek veritabanı oluşturmayı öğrenmek için [Başlarken sayfasına](sql-database-single-database-get-started.md) bakın.  Alternatif olarak, Azure portal kullanarak bir Azure SQL veritabanı oluşturmak için bu [kısa iki dakikalık videoyu](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) da izleyebilirsiniz.

## <a id="ConnectionString"></a>2. Adım: bağlantı dizesini al
Azure SQL veritabanınız sağlandıktan sonra bağlantı bilgilerini belirlemeniz ve güvenlik duvarı erişimi için istemci IP 'nizi eklemek üzere aşağıdaki adımları gerçekleştirmeniz gerekir.

[Azure Portal](https://portal.azure.com/)' de, veritabanınızın genel bakış bölümünün bir parçası olarak listelenen **veritabanı bağlantı dizelerini göster** ' i kullanarak Azure SQL veritabanı ODBC bağlantı dizeniz ' ne gidin:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

**ODBC içeriğini (node. js içerir) [SQL Authentication]** dizesini kopyalayın. Bu dizeyi daha sonra ODBC komut satırı yorumlayıcımızdan C++ bağlanmak için kullanıyoruz. Bu dize, sürücü, sunucu ve diğer veritabanı bağlantı parametreleri gibi ayrıntılar sağlar.

## <a id="Firewall"></a>3. Adım: IP 'nizi güvenlik duvarına ekleme
Başarılı bir bağlantı kurabildiğimiz için, veritabanı sunucunuzun güvenlik duvarı bölümüne gidin ve [aşağıdaki adımları kullanarak ISTEMCI IP 'nizi güvenlik duvarına](sql-database-configure-firewall-settings.md) ekleyin:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Bu noktada, Azure SQL DB 'nizi yapılandırdınız ve C++ kodınızdan bağlanmaya hazırsanız.

## <a id="Windows"></a>4. Adım: bir Windows C/C++ uygulamasından bağlanma
Visual Studio ile derleme yapan [Bu örneği kullanarak Windows ÜZERINDE ODBC kullanarak Azure SQL DB](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) 'nize kolayca bağlanabilirsiniz. Örnek, Azure SQL DB 'imize bağlanmak için kullanılabilecek bir ODBC komut satırı yorumlayıcısı uygular. Bu örnek, bir veritabanı kaynak adı dosyası (DSN) dosyasını bir komut satırı bağımsız değişkeni veya daha önce Azure portal kopyaladığımız ayrıntılı bağlantı dizesi olarak alır. Bu proje için özellik sayfasını açın ve bağlantı dizesini burada gösterildiği gibi bir komut bağımsız değişkeni olarak yapıştırın:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Veritabanınız için doğru kimlik doğrulama ayrıntılarını veritabanı bağlantı dizesinin bir parçası olarak sağladığınızdan emin olun.

Uygulamayı oluşturmak için başlatın. Başarılı bir bağlantıyı doğrulamak için aşağıdaki pencereyi görmeniz gerekir. Veritabanı bağlantınızı doğrulamak için **tablo oluştur** gibi bazı temel SQL komutlarını da çalıştırabilirsiniz:

![SQL komutları](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternatif olarak, bir komut bağımsız değişkeni sağlanmadıysa başlatılan Sihirbazı kullanarak bir DSN dosyası oluşturabilirsiniz. Bu seçeneği de denemeniz önerilir. Otomasyon için bu DSN dosyasını kullanabilir ve kimlik doğrulama ayarlarınızı koruyabilirsiniz:

![DSN dosyası oluştur](./media/sql-database-develop-cplusplus-simple/datasource.png)

Tebrikler! Artık Windows 'da C++ ve ODBC Ile Azure SQL 'e başarıyla bağlandınız. Okumayı, Linux platformu için de aynı şekilde yapmaya devam edebilirsiniz.

## <a id="Linux"></a>5. Adım: Linux C/C++ uygulamasından bağlantı kurma
Haberleri henüz duymadıysanız, Visual Studio artık Linux uygulaması geliştirmeye C++ de olanak tanır. Bu yeni senaryo hakkında daha fazla bilgi [Için Visual C++ Linux geliştirme](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogunu okuyun. Linux için derlemek için, Linux 'un çalıştığı bir uzak makineye ihtiyacınız vardır. Kullanılabilir bir hesabınız yoksa, [Linux Azure sanal makinelerini](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kullanarak bir hızla bir tane ayarlayabilirsiniz.

Bu öğreticide, bir Ubuntu 16,04 Linux dağıtımının ayarlanmış olduğunu varsayalım. Buradaki adımlar Ubuntu 15,10, Red hat 6 ve Red hat 7 için de geçerlidir.

Aşağıdaki adımlarda, uygulamanız için SQL ve ODBC için gereken kitaplıklar yüklenir:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Visual Studio 'Yu başlatın. Araçlar-> Seçenekler altında > platformlar arası > Bağlantı Yöneticisi ' ne, Linux kutuya bir bağlantı ekleyin:

![Araç seçenekleri](./media/sql-database-develop-cplusplus-simple/tools.png)

SSH üzerinden bağlantı kurulduktan sonra boş bir proje (Linux) şablonu oluşturun:

![Yeni proje şablonu](./media/sql-database-develop-cplusplus-simple/template.png)

Daha sonra [Yeni bir C kaynak dosyası ekleyebilir ve bu içeriği değiştirebilirsiniz](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). ODBC API 'Leri SQLAllocHandle, SQLSetConnectAttr ve SQLDriverConnect kullanarak, veritabanınıza bir bağlantı başlatabilir ve bir bağlantı kurabilirsiniz.
Windows ODBC örneğinde olduğu gibi, SQLDriverConnect çağrısını, daha önce Azure portal kopyaladığınız veritabanı bağlantı dizesi parametrelerinizin ayrıntıları ile değiştirmeniz gerekir.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Derleme öncesinde yapmanız gereken son şey, **ODBC** 'yi bir kitaplık bağımlılığı olarak eklemektir:

![Giriş kitaplığı olarak ODBC ekleme](./media/sql-database-develop-cplusplus-simple/lib.png)

Uygulamanızı başlatmak için **hata ayıklama** menüsünden Linux konsolunu getirin:

![Linux konsolu](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Bağlantınız başarılı olduysa, şimdi Linux konsolunda yazdırılmış geçerli veritabanı adını görmeniz gerekir:

![Linux konsol penceresi çıkışı](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Tebrikler! Eğitmeni başarıyla tamamladınız ve artık Windows ve Linux platformlarındaki Azure SQL DB C++ 'nize bağlanabilirsiniz.

## <a id="GetSolution"></a>Tüm C/C++ öğreticisi çözümünü edinme
GitHub 'da bu makaledeki tüm örnekleri içeren GetStarted çözümünü bulabilirsiniz:

* [ODBC C++ Windows örneği](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Azure SQL 'e C++ bağlanmak için Windows ODBC örneğini indirin
* [ODBC C++ Linux örneği](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Azure SQL 'e C++ bağlanmak için Linux ODBC örneğini indirin

## <a name="next-steps"></a>Sonraki adımlar
* [SQL veritabanı geliştirmeye genel bakış](sql-database-develop-overview.md) konusunu inceleyin
* [ODBC API başvurusu](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/) hakkında daha fazla bilgi

## <a name="additional-resources"></a>Ek kaynaklar
* [Azure SQL Veritabanı ile Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Veritabanının tüm özelliklerini](https://azure.microsoft.com/services/sql-database/) keşfedin

