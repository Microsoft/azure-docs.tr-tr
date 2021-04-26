---
title: SQL Data Sync için veri eşitleme Aracısı
description: SQL Server veritabanları ile verileri eşitlemek için Azure 'daki SQL Data Sync veri eşitleme aracısını yüklemeyi ve çalıştırmayı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791469"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>SQL Data Sync için veri eşitleme Aracısı
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure 'da SQL Data Sync için veri eşitleme aracısını yükleyip yapılandırarak SQL Server veritabanlarıyla verileri eşitleyin. SQL Data Sync hakkında daha fazla bilgi için bkz. [SQL Data Sync ile birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync Şu anda Azure SQL yönetilen **örneğini desteklemez.**

## <a name="download-and-install"></a>İndirme ve yükleme

Veri eşitleme aracısını indirmek için [SQL Data Sync aracı](https://www.microsoft.com/download/details.aspx?id=27693)' na gidin. Veri eşitleme aracısını yükseltmek için, aracıyı eski aracıyla aynı konuma yükler ve özgün olanı geçersiz kılar.

### <a name="install-silently"></a>Sessizce Install

Veri eşitleme aracısını komut isteminden sessizce yüklemek için aşağıdaki örneğe benzer bir komut girin. İndirilen. msi dosyasının dosya adını denetleyin ve **targetı** ve **SERVICEACCOUNT** bağımsız değişkenleri için kendi değerlerinizi sağlayın.

- **Targetı** için bir değer sağlamazsanız, varsayılan değer olur `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0` .

- `LocalSystem` **SERVICEACCOUNT** değeri olarak sağlarsanız, aracıyı SQL Server bağlanacak şekilde yapılandırırken SQL Server kimlik doğrulaması kullanın.

- **SERVICEACCOUNT** değeri olarak bir etki alanı kullanıcı hesabı veya yerel bir kullanıcı hesabı sağlarsanız, **ServicePassword** bağımsız değişkeni ile de parola sağlamanız gerekir. Örneğin, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>SQL Server veritabanı ile verileri eşitleme

Verileri bir veya daha fazla SQL Server veritabanıyla eşitleyebilmeniz için veri eşitleme aracısını yapılandırmak için, bkz. [SQL Server veritabanı ekleme](sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Veri eşitleme Aracısı hakkında SSS

### <a name="why-do-i-need-a-client-agent"></a>Neden bir istemci aracısına ihtiyacım var?

SQL Data Sync hizmeti istemci Aracısı aracılığıyla SQL Server veritabanlarıyla iletişim kurar. Bu güvenlik özelliği, bir güvenlik duvarının arkasındaki veritabanlarıyla doğrudan iletişimi önler. SQL Data Sync hizmeti aracıyla iletişim kurduğunda, bu, şifrelenmiş bağlantıları ve benzersiz bir belirteç ya da *Aracı anahtarını* kullanmayı da ister. SQL Server veritabanları, bağlantı dizesini ve aracı anahtarını kullanarak aracının kimliğini doğrular. Bu tasarım, verileriniz için yüksek düzeyde güvenlik sağlar.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Yerel Aracı kullanıcı arabirimine ait kaç örnek çalıştırılabilir

Kullanıcı arabiriminin yalnızca bir örneği çalıştırılabilir.

### <a name="how-can-i-change-my-service-account"></a>Hizmet hesabımı nasıl değiştirebilirim?

Bir istemci Aracısı yükledikten sonra, hizmet hesabını değiştirmek için tek yol bunu kaldırmalıdır ve yeni hizmet hesabıyla yeni bir istemci Aracısı yükler.

### <a name="how-do-i-change-my-agent-key"></a>Aracı anahtarımı Değiştir Nasıl yaparım?

Bir aracı anahtarı, bir aracı tarafından yalnızca bir kez kullanılabilir. Yeni bir aracıyı kaldırdığınızda veya birden çok aracı tarafından kullanılabilir hale geldiğinde yeniden kullanılamaz. Var olan bir aracı için yeni bir anahtar oluşturmanız gerekiyorsa, aynı anahtarın istemci aracısına ve SQL Data Sync hizmetine kaydedildiğinden emin olmanız gerekir.

### <a name="how-do-i-retire-a-client-agent"></a>Nasıl yaparım? istemci aracısını devre dışı bırakma

Bir aracıyı hemen geçersiz kılmak veya devre dışı bırakmak için, portalda anahtarını yeniden oluşturun ancak aracı Kullanıcı arabiriminde gönderemezsiniz. İlgili aracı çevrimiçi veya çevrimdışı olduğundan, bir anahtarın yeniden oluşturulması önceki anahtarı geçersiz kılar.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>İstemci aracısını başka bir bilgisayara taşımak Nasıl yaparım?

Yerel aracıyı Şu anda açık olandan farklı bir bilgisayardan çalıştırmak istiyorsanız, aşağıdakileri yapın:

1. Aracıyı istenen bilgisayara yükler.
2. SQL Data Sync portalında oturum açın ve yeni aracı için bir aracı anahtarı oluşturun.
3. Yeni Aracı anahtarını göndermek için yeni aracının Kullanıcı arabirimini kullanın.
4. İstemci Aracısı daha önce kaydedilmiş şirket içi veritabanlarının listesini indirirken bekleyin.
5. Ulaşılamaz olarak görüntülenen tüm veritabanları için veritabanı kimlik bilgilerini sağlayın. Bu veritabanlarına, aracının yüklü olduğu yeni bilgisayardan erişilebilir olması gerekir.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Veri eşitleme Aracısı sorunlarını giderme

- [İstemci Aracısı yükleme, kaldırma veya onarma başarısız oluyor](#agent-install)

- [Kaldırma işlemini iptal ettikten sonra istemci Aracısı çalışmıyor](#agent-uninstall)

- [Veritabanım, aracı listesinde listelenmiyor](#agent-list)

- [İstemci Aracısı başlamıyor (hata 1069)](#agent-start)

- [Aracı anahtarını gönderemiyor](#agent-key)

- [İlişkili şirket içi veritabanı ulaşılamaz durumdaysa, portaldan istemci Aracısı silinemez](#agent-delete)

- [Yerel eşitleme Aracısı uygulaması yerel Eşitleme hizmetine bağlanamıyor](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> İstemci Aracısı yükleme, kaldırma veya onarma başarısız oluyor

- **Neden**. Birçok senaryo bu hataya neden olabilir. Bu hatanın belirli nedenini öğrenmek için günlüklere bakın.

- **Çözümleme**. Hatanın belirli nedenini bulmak için Windows Installer günlüklerini oluşturun ve bu günlüklere bakın. Günlüğe kaydetmeyi bir komut isteminde açabilirsiniz. Örneğin, indirilen yükleme dosyası ise, `SQLDataSyncAgent-2.0-x86-ENU.msi` aşağıdaki komut satırlarını kullanarak günlük dosyaları oluşturun ve inceleyin:

  - Yüklemeler için: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Kaldırma için: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Ayrıca, Windows Installer tarafından gerçekleştirilen tüm yüklemeler için günlük kaydını da etkinleştirebilirsiniz. Microsoft Bilgi Bankası makalesi [Windows Installer günlük kaydını etkinleştirme](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) , Windows Installer için günlük kaydını açmak üzere tek tıklamayla bir çözüm sağlar. Ayrıca günlüklerin konumunu da sağlar.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> Kaldırma işlemini iptal ettikten sonra istemci Aracısı çalışmıyor

Kaldırma işlemini iptal ettikten sonra bile istemci Aracısı çalışmaz.

- **Neden**. Bu durum SQL Data Sync istemci Aracısı kimlik bilgilerini depolamadığından oluşur.

- **Çözümleme**. Bu iki çözümü deneyebilirsiniz:

    -   İstemci aracısının kimlik bilgilerini yeniden girmek için Services. msc kullanın.
    -   Bu istemci aracısını kaldırın ve yeni bir tane yükleme. En son istemci aracısını [Indirme merkezinden](https://www.microsoft.com/download/details.aspx?id=27693)indirin ve yükleyin.

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> Veritabanım, aracı listesinde listelenmiyor

Var olan bir SQL Server veritabanını bir eşitleme grubuna eklemeye çalıştığınızda, veritabanı aracılar listesinde görünmez.

Bu senaryolar bu soruna neden olabilir:

- **Neden**. İstemci Aracısı ve eşitleme grubu farklı veri merkezlerinde.

- **Çözümleme**. İstemci Aracısı ve eşitleme grubu aynı veri merkezinde olmalıdır. Bu ayarı yapmak için iki seçeneğiniz vardır:

    -   Veri merkezinde eşitleme grubunun bulunduğu yeni bir aracı oluşturun. Ardından, veritabanını bu aracıya kaydedin.
    -   Geçerli eşitleme grubunu silin. Daha sonra, aracının bulunduğu veri merkezinde eşitleme grubunu yeniden oluşturun.

- **Neden**. İstemci aracısının veritabanı listesi geçerli değil.

- **Çözümleme**. İstemci Aracısı hizmetini durdurup yeniden başlatın.

    Yerel Aracı, ilişkili veritabanlarının listesini yalnızca aracı anahtarının ilk gönderiminde indirir. Bu, sonraki aracı anahtar gönderimlerinin ilişkili veritabanlarının listesini indirmez. Bir aracı taşıma sırasında kaydedilen veritabanları özgün aracı örneğinde gösterilmez.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> İstemci Aracısı başlamıyor (hata 1069)

Aracının SQL Server barındıran bir bilgisayarda çalışmadığını fark edersiniz. Aracıyı el ile başlatmaya çalıştığınızda, "hata 1069: hizmet bir oturum açma hatası nedeniyle başlamadı" iletisini görüntüleyen bir iletişim kutusu görürsünüz.

![Veri eşitleme hatası 1069 iletişim kutusu](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **Neden**. Bu hatanın olası nedeni, aracıyı ve Aracı parolasını oluşturduktan sonra yerel sunucu üzerindeki parolanın değiştiği bir nedendir.

- **Çözümleme**. Aracının parolasını geçerli sunucu parolanızla güncelleştirin:

  1. SQL Data Sync Client Agent hizmetini bulun.  
    a. **Başlat**'ı seçin.  
    b. Arama kutusuna **Services. msc** yazın.  
    c. Arama sonuçlarında **Hizmetler**' i seçin.  
    d. **Hizmetler** penceresinde, **SQL Data Sync Aracısı** için girişe kaydırın.  
  1. **SQL Data Sync Aracısı**' na sağ tıklayın ve ardından **Durdur**' u seçin.
  1. **SQL Data Sync Aracısı**' na sağ tıklayın ve ardından **Özellikler**' i seçin.
  1. **SQL Data Sync Aracısı özellikleri** üzerinde **oturum aç** sekmesini seçin.
  1. **Parola** kutusuna parolanızı girin.
  1. **Parolayı Onayla** kutusuna parolanızı yeniden girin.
  1. **Uygula**’yı ve sonra **Tamam**’ı seçin.
  1. **Hizmetler** penceresinde, **SQL Data Sync Aracısı** hizmetine sağ tıklayın ve ardından **Başlat**' a tıklayın.
  1. **Hizmetler** penceresini kapatın.

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> Aracı anahtarını gönderemiyor

Bir aracı için anahtar oluşturup yeniden oluşturduktan sonra, anahtarı SqlAzureDataSyncAgent uygulaması aracılığıyla göndermeye çalışırsınız. Gönderim tamamlanamamalıdır.

![Eşitleme hatası iletişim kutusu-aracı anahtarı gönderilemiyor](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Ön koşullar**. Devam etmeden önce aşağıdaki önkoşulları kontrol edin:

  - SQL Data Sync Windows hizmeti çalışıyor.

  - SQL Data Sync Windows hizmeti için hizmet hesabı 'nda ağ erişimi vardır.

  - Giden 1433 bağlantı noktası yerel güvenlik duvarı kuralınızdan açıktır.

  - Yerel IP, eşitleme meta verileri veritabanı için sunucu veya veritabanı güvenlik duvarı kuralına eklenir.

- **Neden**. Aracı anahtarı her yerel aracıyı benzersiz şekilde tanımlar. Anahtarın iki koşulu karşılaması gerekir:

  -   SQL Data Sync sunucusundaki istemci Aracısı anahtarı ve yerel bilgisayar aynı olmalıdır.
  -   İstemci Aracısı anahtarı yalnızca bir kez kullanılabilir.

- **Çözümleme**. Aracınız çalışmıyorsa, bu koşulların biri veya her ikisi karşılanmaz. Aracının yeniden çalışmasını sağlamak için:

  1. Yeni bir anahtar oluşturun.
  1. Yeni anahtarı aracıya uygulayın.

  Yeni anahtarı aracıya uygulamak için:

  1. Dosya Gezgini 'nde, aracı yükleme dizininize gidin. Varsayılan yükleme dizini C: \\ Program Files (x86) \\ Microsoft SQL Data Sync.
  1. Bin alt dizinine çift tıklayın.
  1. SqlAzureDataSyncAgent uygulamasını açın.
  1. **Aracı anahtarını gönder**' i seçin.
  1. Girilen alana anahtarı panodan yapıştırın.
  1. **Tamam**’ı seçin.
  1. Programı kapatın.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> İlişkili şirket içi veritabanı ulaşılamaz durumdaysa, portaldan istemci Aracısı silinemez

Bir SQL Data Sync istemci aracısıyla kayıtlı yerel bir uç nokta (bir veritabanı) ulaşılamaz hale gelirse, istemci Aracısı silinemez.

- **Neden**. Erişilemeyen veritabanı hala aracıya kaydedildiğinden, yerel aracı silinemiyor. Aracıyı silmeye çalıştığınızda, silme işlemi veritabanına ulaşmaya çalışır, bu da başarısız olur.

- **Çözümleme**. Erişilemeyen veritabanını silmek için "silmeyi zorla" yı kullanın.

> [!NOTE]
> Eşitleme meta verileri tabloları bir "silmeyi zorla" sonra kalırsa, `deprovisioningutil.exe` bunları temizlemek için kullanın.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> Yerel eşitleme Aracısı uygulaması yerel Eşitleme hizmetine bağlanamıyor

- **Çözümleme**. Aşağıdaki adımları deneyin:

  1. Uygulamadan çıkın.  
  1. Bileşen Hizmetleri panelini açın.  
    a. Görev çubuğundaki arama kutusuna **Services. msc** yazın.  
    b. Arama sonuçlarında **Hizmetler**' e çift tıklayın.  
  1. **SQL Data Sync** hizmetini durdurun.
  1. **SQL Data Sync** hizmetini yeniden başlatın.  
  1. Uygulamayı yeniden açmak.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Veri eşitleme aracısını komut isteminden çalıştırma

Komut isteminden aşağıdaki veri eşitleme Aracısı komutlarını çalıştırabilirsiniz:

### <a name="ping-the-service"></a>Hizmete ping gönder

#### <a name="usage"></a>Kullanım

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Örnek

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Kayıtlı veritabanlarını görüntüle

#### <a name="usage"></a>Kullanım

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Örnek

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Aracı anahtarını gönder

#### <a name="usage"></a>Kullanım

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Örnek

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Veritabanını kaydetme

#### <a name="usage"></a>Kullanım

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Örnekler

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Bir veritabanının kaydını silme

Bu komutu bir veritabanının kaydını silmek için kullandığınızda, veritabanının sağlamasını tamamen kaldırır. Veritabanı diğer eşitleme gruplarına katılıyorsa, bu işlem diğer eşitleme gruplarını keser.

#### <a name="usage"></a>Kullanım

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Örnek

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Kimlik bilgilerini güncelleştirme

#### <a name="usage"></a>Kullanım

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Örnekler

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Sonraki adımlar

SQL Data Sync hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

-   Genel Bakış- [Azure 'da SQL Data Sync birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](sql-data-sync-data-sql-server-sql-database.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğretici: Azure SQL veritabanı ile SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-data-sync-sql-server-configure.md)
    - PowerShell ile
        -  [Azure SQL veritabanı 'nda birden çok veritabanı arasında eşitleme yapmak için PowerShell 'i kullanma](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak Azure SQL veritabanındaki bir veritabanı ile SQL Server örneğindeki bir veritabanı arasında eşitleme](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   En iyi uygulamalar- [Azure SQL Data Sync Için en iyi yöntemler](sql-data-sync-best-practices.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](./monitor-tune-overview.md)
-   Sorun giderme-[Azure SQL Data Sync sorunları giderme] SQL-Data-Sync-troubleshoot.md)
-   Eşitleme şemasını güncelleştirme
    -   [Azure 'da SQL Data Sync şema değişikliklerinin](sql-data-sync-update-sync-schema.md) Transact-SQL Ile otomatikleştirilmesi ile
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/update-sync-schema-in-sync-group.md)