---
title: Çok kiracılı uygulama örneği-Wingtip SaaS
description: Azure SQL veritabanı, Wingtip bilet SaaS örneği kullanan örnek çok kiracılı uygulamayı yüklemek ve çalıştırmak için adım ve kılavuz sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132302"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Wingtip bilet örnek SaaS uygulamaları ile çalışmaya yönelik genel kılavuz

Bu makale, Azure SQL veritabanı 'nı kullanan Wingtip bilet örnek SaaS uygulamalarını çalıştırmaya yönelik genel rehberlik içerir.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma

ZIP dosyaları bir dış kaynaktan indirilip ayıklandığında, yürütülebilir içerikler (betikler, dll 'ler) Windows tarafından engellenebilir. Bir zip dosyasından betikleri ayıkladığınızda, **Ayıklamadan önce. zip dosyasının engelini kaldırmak için aşağıdaki adımları izleyin**. Bu, betiklerin çalışmasına izin verilmesini sağlar.

1. Araştırmak istediğiniz veritabanı Kiracı deseninin Wingtip bilet SaaS GitHub deposuna göz atın:
    - [Wingtipbilet ssaas-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [Wingtipbilet ssaas-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. **Kopyala veya indir**' e tıklayın.
3. **ZIP 'ı indir** ' e tıklayın ve dosyayı kaydedin.
4. ZIP dosyasına sağ tıklayın ve **Özellikler**' i seçin. ZIP dosya adı, depo adına karşılık gelir. değerine. _WingtipTicketsSaaS-DbPerTenant-Master. zip_)
5. **Genel** sekmesinde **Engellemeyi kaldır**' ı seçin.
6. **OK (Tamam)** düğmesine tıklayın.
7. Dosyaları ayıklayın.

Betikler *..\\Learning modülleri* klasöründe bulunur.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Wingtip bilet PowerShell betikleri ile çalışma

En iyi şekilde yararlanmak için, belirtilen betiklere erişmeniz gerekir. Yürütme noktaları kullanın ve betikler üzerinde ilerleyin ve farklı SaaS desenlerinin nasıl uygulandığını inceleyin. En iyi şekilde anlamak için, sunulan betiklerin ve modüllerin kolayca ilerlemek için [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)'yi kullanmanızı öneririz.

### <a name="update-the-configuration-file-for-your-deployment"></a>Dağıtımınız için yapılandırma dosyasını güncelleştirme

**Userconfig. psm1** dosyasını, dağıtım sırasında ayarladığınız kaynak grubu ve Kullanıcı değeriyle düzenleyin:

1. *PowerShell ISE* ve Load...\\öğrenme modüllerini\\*userconfig. psm1* ile açın
2. *Resourcegroupname* ve *ad* 'yi dağıtımınızın belirli değerleriyle güncelleştirin (yalnızca 10 ve 11. satır).
3. Değişiklikleri kaydedin!

Burada bu değerler ayarlandığında, her bir betikte dağıtıma özgü değerleri güncelleştirmek zorunda kalmaktan devam edersiniz.

### <a name="execute-the-scripts-by-pressing-f5"></a>F5 'e basarak betikleri yürütün

Çeşitli betikler klasörlerde gezinmek için *$PSScriptRoot* kullanır ve *$PSScriptRoot* yalnızca **F5**'e basılarak betikler yürütüldüğünde değerlendirilir.  Bir seçimi vurgulama ve çalıştırma (**F8**) hatalara neden olabilir, bu nedenle betikleri çalıştırırken **F5** tuşuna basın.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Uygulamayı incelemek üzere betiklerde ilerleme

Betikleri anlamanın en iyi yolu, ne yaptığını görmek için onları adım adım ilerleyerek kullanmaktır. Daha kolay bir izleme iş akışını sunan dahil olan **tanıtım** amaçlı betiklerine göz atın. **Tanıtım** betikleri, her bir görevi gerçekleştirmek için gereken adımları gösterir, bu nedenle kesme noktaları ayarlayın ve farklı SaaS desenlerinin uygulama ayrıntılarını görmek için tek tek çağrılara daha ayrıntılı bir ayrıntıya gidin.

PowerShell komut dosyalarını keşfetme ve Adımlama ipuçları:

- PowerShell ıSE 'de **demo-** betikleri açın.
- **F5** ile yürütün veya devam edin (bir komut dosyasının seçimleri çalıştırılırken *$PSScriptRoot* değerlendirilmediği için **F8** kullanarak önerilmez).
- Bir çizgiye tıklayarak veya çizgiyi seçerek ve **F9**’a basarak kesme noktaları yerleştirin.
- **F10**’u kullanarak bir işlev veya betiği atlayın.
- **F11**’i kullanarak bir işlev veya betiğe gidin.
- **Shift + F11**’i kullanarak geçerli işlev veya betikten çıkın.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Veritabanı şemasını keşfetme ve SSMS kullanarak SQL sorguları yürütme

Uygulama sunucularına ve veritabanlarına bağlanmak ve bu dosyalara gitmek için [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) kullanın.

Dağıtım başlangıçta, bağlanılacak kiracılar ve Katalog SQL veritabanı sunucuları vardır. Sunucuların adlandırılması, veritabanı Kiracı düzenine bağlıdır (Ayrıntılar için aşağıya bakın).

   - **Tek başına uygulama:** her kiracı için sunucular (örn. *contosoconcerthall-&lt;kullanıcı&gt;* sunucusu) ve *Katalog-sa-&lt;Kullanıcı&gt;*
   - **Kiracı başına veritabanı:** *tenants1-dpt-&lt;Kullanıcı&gt;* ve *katalog-dpt-&lt;Kullanıcı&gt;* sunucuları
   - **Çok kiracılı veritabanı:** *tenants1-MT-&lt;Kullanıcı&gt;* ve *Katalog-MT-&lt;Kullanıcı&gt;* sunucuları

Başarılı bir tanıtım bağlantısı sağlamak için, tüm sunucuların tüm IP 'Leri aracılığıyla izin veren bir [güvenlik duvarı kuralı](sql-database-firewall-configure.md) vardır.


1. *SSMS* 'yi açın ve kiracılar 'a bağlanın. Sunucu adı, seçtiğiniz veritabanı Kiracı düzenine bağlıdır (Ayrıntılar için aşağıya bakın):
    - **Tek başına uygulama:** ayrı kiracıların sunucuları (örn. *contosoconcerthall-&lt;User&gt;. Database.Windows.net*)
    - **Kiracı başına veritabanı:** *tenants1-dpt-&lt;User&gt;. Database.Windows.net*
    - **Çok kiracılı veritabanı:** *tenants1-MT-&lt;User&gt;. Database.Windows.net*
2. **Bağlan** > **Veritabanı Altyapısı...** :

   ![katalog sunucusu seçeneğine tıklayın](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Tanıtım bilgileri şunlardır: Login = *Geliştirici*, Password = *P\@ssword1*

    Aşağıdaki görüntüde, *kiracı başına veritabanı* için oturum açma bilgisi gösterilmektedir.
    ![bağlantı](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. 2-3 adımlarını yineleyin ve katalog sunucusuna bağlanın (veritabanı Kiracı düzenine göre belirli sunucu adları için aşağıya bakın)
    - **Tek başına uygulama:** *Katalog-sa-&lt;User&gt;. Database.Windows.net*
    - **Kiracı başına veritabanı:** *Katalog-dpt-&lt;User&gt;. Database.Windows.net*
    - **Çok kiracılı veritabanı:** *Katalog-MT-&lt;User&gt;. Database.Windows.net*


Başarıyla bağlandıktan sonra tüm sunucuları görmeniz gerekir. Sağladığınız kiracılara bağlı olarak veritabanları listeniz farklı olabilir.

Aşağıdaki görüntüde, *kiracı başına veritabanı* için oturum açma deseninin günlüğü gösterilmektedir.

![nesne gezgini](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Sonraki adımlar
- [Wingtip bilet SaaS tek başına uygulamasını dağıtma](saas-standaloneapp-get-started-deploy.md)
- [Kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma](saas-dbpertenant-get-started-deploy.md)
- [Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını dağıtma](saas-multitenantdb-get-started-deploy.md)

