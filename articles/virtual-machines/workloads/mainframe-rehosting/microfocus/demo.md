---
title: Azure sanal makinelerinde Micro Focus Enterprise Developer 4,0 için Micro Focus CICS BankDemo ayarlama
description: Micro Focus Enterprise Server ve Enterprise Developer 'ı kullanmayı öğrenmek için Azure sanal makinelerinde (VM 'Ler) Micro Focus BankDemo uygulamasını çalıştırın.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 9ef9dd27183b6c23762dcdc2d5a844123d35559a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045969"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure 'da Micro Focus Enterprise Developer 4,0 için Micro Focus CICS BankDemo ayarlama

Azure 'da Micro Focus Enterprise Server 4,0 ve Enterprise Developer 4,0 ' i ayarlarken IBM z/OS iş yüklerinin dağıtımlarını test edebilirsiniz. Bu makalede, Enterprise Developer ile birlikte gelen örnek bir uygulama olan CICS BankDemo 'in nasıl ayarlanacağı gösterilmektedir.

CICS, çevrimiçi ana bilgisayar uygulamalarının birçoğu tarafından kullanılan işlem platformu olan müşteri bilgileri denetim sistemi için temsil eder. BankDemo uygulaması, kurumsal sunucu ve kurumsal geliştiricinin nasıl çalıştığını ve yeşil ekran terminalleriyle gerçek bir uygulamanın nasıl yönetileceğini ve dağıtılacağını öğrenmek için harika bir uygulamadır.

> [!NOTE]
> Yakında: Azure VM 'lerinde [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) kurma yönergeleri.

## <a name="prerequisites"></a>Önkoşullar

- [Kurumsal Geliştirici](set-up-micro-focus-azure.md)IÇEREN bir VM. Enterprise Developer 'ın geliştirme ve test amaçlarıyla bunun üzerinde bir Enterprise Server örneği olduğunu aklınızda bulundurun. Bu örnek, demo için kullanılan kurumsal sunucu örneğidir.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Enterprise Developer VM üzerine indirin ve yükleyin. Enterprise Server, CICS Region yönetimi için bir veritabanı gerektirir ve BankDemo uygulaması da BANKDEMO adlı bir SQL Server veritabanı kullanır. Bu tanıtım, her iki veritabanı için SQL Server Express kullandığınızı varsayar. Yükleme sırasında temel yüklemeyi seçin.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). SSMS veritabanlarını yönetmek ve bir T-SQL betiği çalıştırmak için kullanılır. Enterprise Developer VM üzerine indirin ve yükleyin.

- Ücretsiz olarak indirebileceğiniz en son hizmet paketi veya [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)Ile [Visual Studio 2019](https://azure.microsoft.com/downloads/) .

- Rumba masaüstü veya başka bir 3270 öykünücüsü.

## <a name="configure-the-windows-environment"></a>Windows ortamını yapılandırma

Kurumsal Geliştirici 4,0 'yi VM 'ye yükledikten sonra, onunla birlikte gelen kurumsal sunucu örneğini yapılandırmanız gerekir. Bunu yapmak için, aşağıdaki gibi birkaç ek Windows özelliği yüklemeniz gerekir.

1. Oluşturduğunuz Enterprise Server 4,0 VM 'de oturum açmak için RDP 'yi kullanın.

2. **Başlat** düğmesinin yanındaki **Ara** simgesine tıklayın ve **Windows özellikleri** yazın. Sunucu Yöneticisi rol ve Özellik Ekleme Sihirbazı açılır.

3. **Web sunucusu (IIS) rolünü** seçin ve ardından aşağıdaki seçenekleri kontrol edin:

    - Web yönetimi araçları
    - IIS 6 Yönetim uyumluluğu (tüm kullanılabilir özellikleri Seç)
    - IIS Yönetim Konsolu
    - IIS Yönetim betikleri ve araçları
    - IIS Yönetim hizmeti

4. **World Wide Web Hizmetleri**' ni seçin ve aşağıdaki seçenekleri kontrol edin:

     Uygulama geliştirme özellikleri:
    - .NET Genişletilebilirliği
    - ASP.NET
    - Ortak HTTP özellikleri: tüm kullanılabilir özellikleri ekleyin
    - Sistem durumu ve Tanılama: tüm kullanılabilir özellikleri ekleyin
    - Güven
        - Temel Kimlik Doğrulaması
        - Windows Kimlik Doğrulaması

5. **Windows Işlem etkinleştirme hizmeti** 'ni ve tüm alt öğelerini seçin.

6. **Özellikler** için **Microsoft .NET Framework 3.5.1**' i denetleyin ve aşağıdaki seçenekleri kontrol edin:

    - HTTP etkinleştirmesi Windows Communication Foundation
    - HTTP olmayan etkinleştirme Windows Communication Foundation

7. **Özellikler** için **Microsoft .NET Framework 4,6**' i işaretleyin ve aşağıdaki seçenekleri kontrol edin:

   - Adlandırılmış kanal etkinleştirmesi
   - TCP Etkinleştirme
   - TCP Bağlantı Noktası Paylaşma

     ![Rol ve Özellik Ekleme Sihirbazı: rol hizmetleri](media/01-demo-roles.png)

8. Tüm seçenekleri belirlediğinizde, yüklemek için **İleri** ' ye tıklayın.

9. Windows Özellikleri ' ne geçtikten sonra **Denetim Masası \> sistem ve güvenlik \> Yönetim Araçları**' na gidin ve **Hizmetler**' i seçin. Aşağı kaydırın ve aşağıdaki hizmetlerin çalıştığından ve **Otomatik** olarak ayarlandığından emin olun:

    - **NetTcpPortSharing**
    - **Net. pipe dinleyicisi bağdaştırıcısı**
    - **Net. TCP dinleyici bağdaştırıcısı**

10. IIS 'yi yapılandırmak ve desteği sağlamak için, menüden **Micro Focus Enterprise Geliştirici komut istemi (64 bit)** bulun ve **yönetici** olarak çalıştırın.

11. **Ayarla – ı** yazın ve **ENTER** tuşuna basın.

12. Komut dosyası çalıştıktan sonra pencereyi kapatabilirsiniz.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server için yerel sistem hesabını yapılandırın

Bazı kurumsal sunucu işlemlerinin SQL Server oturum açabilmeleri ve veritabanları ve diğer nesneleri oluşturması gerekir. Bu süreçler yerel sistem hesabını kullanır, bu nedenle bu hesaba sysadmin yetkilisi sağlamanız gerekir.

1. **SSMS** 'yi başlatın ve Windows kimlik doğrulaması kullanarak yerel SQLExpress sunucusuna bağlanmak için **Bağlan** ' a tıklayın. **Sunucu adı** listesinde kullanılabilir olmalıdır.

2. Sol tarafta **güvenlik** klasörünü genişletin ve **oturum açmalar**' ı seçin.

3. **NT Yetkilisi \\ sistemi** ' ni seçin ve **Özellikler**' i seçin.

4. **Sunucu rolleri** ' ni seçin ve **sysadmin**' i denetleyin.

     ![SSMS Nesne Gezgini penceresi: oturum açma özellikleri](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo veritabanını ve tüm nesnelerini oluşturma

1. **Windows Gezgini** 'ni açın ve **C: \\ Users \\ ortak \\ belgeler \\ mikro odaklı \\ Kurumsal Geliştirici \\ örnekleri \\ ana bilgisayar \\ CICS \\ DotNet \\ bankdemo \\ SQL**' e gidin.

2. **Bankdemocreateall. SQL** dosyasının içeriğini panonuza kopyalayın.

3. **SSMS**'yi açın. Sağ tarafta **sunucu** ' ya tıklayın ve **Yeni sorgu**' yı seçin.

4. Panonun içeriğini **Yeni sorgu** kutusuna yapıştırın.

5. Sorgunun üzerindeki **komut** sekmesinden **Çalıştır** ' a tıklayarak SQL 'i yürütün.

Sorgu hata olmadan çalıştırılmalıdır. Bu tamamlandığında, BankDemo uygulaması için örnek veritabanı vardır.

![SQLQuery1. SQL çıkışı](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Veritabanı tablolarının ve nesnelerinin oluşturulduğunu doğrulama

1. **Bankdemo** veritabanına sağ tıklayın ve **Yenile**' yi seçin.

2. **Veritabanını** genişletin ve **Tablolar**' ı seçin. Aşağıdakine benzer bir şey görmeniz gerekir.

     ![Nesne Gezgini genişletilmiş BANKDEMO tablosu](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Enterprise Developer 'da uygulamayı derleme

1. Visual Studio 'Yu açın ve oturum açın.

2. **Dosya** menüsü seçeneği altında **Proje/çözüm aç**' ı seçin, **C: \\ Users \\ ortak \\ belgeler \\ mikro odaklı \\ Kurumsal Geliştirici \\ örnekleri \\ ana bilgisayar \\ CICS \\ DotNet \\ bankdemo ' a** gidin ve **sln** dosyasını seçin.

3. Nesneleri incelemek için biraz zaman alın. COBOL programları, CBL uzantısıyla birlikte Çözüm Gezgini, CopyBooks (CPY) ve JCL ile birlikte gösterilir.

4. **BankDemo2** projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.

    > [!NOTE]
    > BankDemo projesi, bu demo için kullanılmayan HCOSS (SQL Server için konak Uyumluluk seçeneğini) kullanımını sağlar.

5. **Çözüm Gezgini**, **BankDemo2** projesine sağ tıklayın ve **Oluştur**' u seçin.

    > [!NOTE]
    > HCOSS yapılandırılmadığından çözüm düzeyinde derleme hatalara neden olur.

6. Proje oluşturulduğunda **Çıkış** penceresini inceleyin. Aşağıdaki resme benzer görünmesi gerekir.

     ![Başarılı derlemeyi gösteren çıkış penceresi](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>BankDemo uygulamasını bölge veritabanına dağıtma

1. Yönetici olarak bir Kurumsal Geliştirici komut istemi (64 bit) açın.

2. **% Genel% \\ Documents \\ mikro odak \\ Kurumsal Geliştirici \\ örnekleri \\ ana bilgisayar \\ CICS \\ DotNet \\ bankdemo** öğesine gidin.

3. Komut isteminde **bankdemodbdeploy** komutunu yürütün ve dağıtılacak veritabanının parametresini ekleyin, örneğin:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Eğik çizgi (/) eğik çizgi () kullanılmasını sağlayın \\ . Bu betik bir süre boyunca çalışır.

![Yönetim: Kurumsal Geliştirici Komut İstemi penceresi](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>.NET için Kuruluş Yöneticisi 'nde BankDemo bölgesi oluşturma

1. **.Net yönetim kullanıcı arabirimi Için Enterprise Server** 'ı açın.

2. MMC ek bileşenini başlatmak için Windows **Başlat** menüsünde, **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server for .net admin**' i seçin. (Windows Server için, **Micro Focus Enterprise Developer for \> .net admin**' i seçin).

3. Sol bölmedeki **bölgeler** kapsayıcısını genişletin ve ardından **CICS** öğesine sağ tıklayın.

4. (Yerel) veritabanında barındırılan **Bankdemo** adlı yenı bir CICS Region oluşturmak Için **bölge tanımla** ' yı seçin.

5. Veritabanı sunucusu örneğini sağlayın, **İleri**' ye tıklayın ve ardından **bankdemo** bölge adını girin.

     ![Bölge Tanımlama iletişim kutusu](media/07-demo-cics.png)

6. Bölgeler arası veritabanı için bölge tanım dosyasını seçmek için, **C: \\ Users \\ ortak \\ belgeler \\ mikro odaklı \\ Kurumsal Geliştirici \\ örnekleri \\ ana bilgisayar \\ cıcs \\ DotNet \\ bankdemo** bölümünde yer alan **\_ bankdemo \_db.configbölgesini** bulun.

     ![Bölge-bölge adı tanımlama: BANKDEMO](media/08-demo-cics.png)

7. **Finish (Son)** düğmesine tıklayın.

## <a name="create-xa-resource-definitions"></a>XA kaynak tanımları oluşturma

1. **.Net yönetim kullanıcı arabirimi Için Enterprise Server** ' ın sol bölmesinde **sistem**' i ve sonra **XA kaynak tanımları**' nı genişletin. Bu ayar, bölgenin kurumsal sunucu ve uygulama veritabanları ile nasıl birlikte çalıştığını tanımlar.

2. **XA kaynak tanımları** ' na sağ tıklayın ve **sunucu örneği Ekle**' yi seçin.

3. Açılan kutuda **veritabanı hizmeti örneği**' ni seçin. Bu, SQLEXPRESS yerel makinedir.

4. **XA kaynak tanımları (MachineName \\ SQLExpress)** kapsayıcısının altında örneği seçin ve **Ekle**' ye tıklayın.

5. **VERITABANı XA kaynak tanımı** ' nı seçin ve ardından **ad** ve **bölge** için **bankdemo** yazın.

     ![Yeni veritabanı XA kaynak tanımı ekranı](media/09-demo-xa.png)

6. Bağlantı dizesi Sihirbazı 'nı açmak için üç noktaya (**...**) tıklayın. **Sunucu adı** için **(yerel) \\ SQLExpress** yazın. **Oturum açmak** Için **Windows kimlik doğrulaması**' nı seçin. Veritabanı adı için **Bankdemo** yazın

     ![Bağlantı dizesi ekranını Düzenle](media/10-demo-string.png)

7. Bağlantıyı test edin.

## <a name="start-the-bankdemo-region"></a>BANKDEMO bölgesini başlatma

> [!NOTE]
> İlk adım önemlidir: bölgeyi, az önce oluşturduğunuz XA kaynak tanımını kullanacak şekilde ayarlamanız gerekir.

1. **Bölgeler kapsayıcısının** altındaki **Bandemo CICS bölgesine** gidin ve ardından **Eylemler** bölmesinden **bölge başlangıç dosyasını Düzenle** ' yi seçin. SQL özelliklerine ilerleyin ve **XA kaynak adı** için **bankdemo** girin ya da seçmek için üç noktayı kullanın.

2. Değişikliklerinizi kaydetmek için **Kaydet** simgesine tıklayın.

3. **Konsol** bölmesinde **Bankdemo CICS Region** ' a sağ tıklayın ve **Başlat/Durdur bölge**' yi seçin.

4. Orta bölmede görüntülenen **Başlat/Durdur bölge** kutusunun altında **Başlat**' ı seçin. Birkaç saniye sonra bölge başlar.

     ![SQL Başlat/Durdur kutusu](media/11-demo-sql.png)

     ![CICS Region BANKDEMO-başlatıldı ekranı](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Dinleyici oluşturma

BankDemo uygulamasına erişen TN3270 oturumları için bir dinleyici oluşturun.

1. Sol bölmede, **yapılandırma Düzenleyicileri** ' ni genişletin ve **dinleyicisi**' ı seçin.

2. **Dosya Aç** simgesine tıklayın ve **seelistener.exe.config** dosyasını seçin. Bu dosya düzenlenecektir ve Enterprise Server her başlatıldığında yüklenir.

3. Daha önce tanımlanan iki bölgenin (ESDEMO ve JCLDEMO) olduğuna dikkat edin.

4. BANKDEMO için yeni bir bölge oluşturmak için **bölgeler**' e sağ tıklayın ve **bölge Ekle**' yi seçin.

5. **Bankdemo bölgesini** seçin.

6. **Bankdemo bölgesine** sağ tıklayıp **Kanal ekle**' yi seçerek bir TN3270 kanalı ekleyin.

7. **Ad** için **TN3270** girin. **Bağlantı noktası** için **9024** girin. ESDEMO uygulaması 9230 numaralı bağlantı noktasını kullanarak farklı bir bağlantı noktası kullanmanız gerekir.

8. Dosyayı kaydetmek için **Kaydet** simgesine tıklayın veya **Dosya** \> **Kaydet**' i seçin.

9. Dinleyiciyi başlatmak için **dinleyiciyi Başlat** simgesine tıklayın veya **Seçenekler** \> **Start Listener**' ı seçin.

     ![Dinleyici yapılandırma Düzenleyicisi pencereleri](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>BankDemo uygulamasına erişmek için Rumba 'ı yapılandırma

Yapmanız gereken son şey, 3270 öykünücüsünü Rumba kullanarak bir 3270 oturumu yapılandırmaktır. Bu adım, oluşturduğunuz dinleyiciyle BankDemo uygulamasına erişmenizi sağlar.

1. Windows **Başlat** menüsünde Rumba Desktop ' ı başlatın.

2. **Bağlantılar** menü öğesi altında **TN3270** öğesini seçin.

3. **Ekle** ' ye tıklayın ve IP adresi için **127.0.0.1** , Kullanıcı tanımlı bağlantı noktası için **9024** yazın.

4. İletişim kutusunun alt tarafında, **Bağlan**' a tıklayın. Siyah CICS ekran görünür.

5. BankDemo uygulaması için ilk 3270 ekranını göstermek üzere **Bank** yazın.

6. Kullanıcı KIMLIĞI için **B0001** yazın ve parola için herhangi bir şey yazın. İlk ekran BANK20 açılır.

![Ana bilgisayar görünen hoş geldiniz ekranı ](media/14-demo.png)
 ![ ana bilgisayar Display-Rumba-Subsystem demo Screen](media/15-demo.png)

Tebrikler! Artık Azure 'da Micro Focus Enterprise Server kullanarak bir CICS Application çalıştırıyorsunuz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da Docker kapsayıcılarında kurumsal sunucu çalıştırma](run-enterprise-server-container.md)
- [Ana bilgisayar geçişi-Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Sanal Makineler](../../../linux/overview.md)
- [Sorun giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Azure geçişine Demystifying ana bilgisayar](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)