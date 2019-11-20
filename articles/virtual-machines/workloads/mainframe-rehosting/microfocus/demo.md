---
title: Micro odak Kurumsal Geliştirici 4.0 Azure sanal Makineler'de Micro odak CICS BankDemo ' ayarlayın
description: Azure sanal makinelerinde (VM'ler) Micro odak Enterprise Server ve kurumsal Geliştirici kullanmayı öğrenmek için Micro odak BankDemo uygulamayı çalıştırın.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621328"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Micro odak Kurumsal Geliştirici 4.0 azure'da Micro odak CICS BankDemo ' ayarlayın

Micro odak Enterprise Server 4.0 ve Azure üzerinde Kurumsal Geliştirici 4.0 ayarladığınızda, IBM z/OS iş yüklerinin dağıtımı test edebilirsiniz. Bu makalede CICS BankDemo, Enterprise Developer ile birlikte gelen bir örnek uygulamanın nasıl gösterir.

CICS müşteri bilgileri, denetim sistemi birçok çevrimiçi ana bilgisayar uygulaması tarafından kullanılan işlem platformu anlamına gelir. BankDemo uygulama öğrenme Enterprise Server ve kurumsal Geliştirici nasıl çalışır ve yönetme ve yeşil ekran terminaller tam gerçek bir uygulamayı dağıtmak için idealdir.

## <a name="prerequisites"></a>Önkoşullar

- Bir VM ile [Kurumsal Geliştirici](set-up-micro-focus-azure.md). Enterprise Developer Enterprise Server Geliştirme ve test amaçları için eksiksiz bir örnek üzerindeki olduğunu aklınızda bulundurun. Kurumsal demo için kullanılan sunucu örneğinin örneğidir.

- [SQL Server Express 2017 sürümü](https://www.microsoft.com/sql-server/sql-server-editions-express). İndirin ve kurumsal Geliştirici VM'ye yükleyin. Kuruluş Sunucusu CICS bölgelerin yönetimi için bir veritabanı gerektirir ve BankDemo uygulama ayrıca BANKDEMO adlı bir SQL Server veritabanı kullanır. Bu Tanıtım, her iki veritabanı için SQL Server Express kullandığınızı varsayar. Yükleme sırasında temel Yükleme'yi seçin.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS, veritabanını yönetme ve T-SQL betiğini çalıştırmak için kullanılır. İndirin ve kurumsal Geliştirici VM'ye yükleyin.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) en son hizmet paketiyle veya [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), ücretsiz olarak indirebilirsiniz.

- Masaüstü veya başka bir rumba 3270 öykünücüsü.

## <a name="configure-the-windows-environment"></a>Windows ortamını yapılandırma

VM üzerinde Kurumsal Geliştirici 4.0 yükledikten sonra birlikte Enterprise Server örneğini yapılandırmanız gerekir. Bunu yapmak için birkaç ek Windows özelliklerini şu şekilde yüklemeniz gerekir.

1. Kurumsal Server 4.0 oluşturduğunuz VM'de oturum açma için RDP kullanın.

2. Tıklayın **arama** yanındaki simge **Başlat** düğmesi ve türü **Windows özellikleri**. Sunucu Yöneticisi Rol Ekle ve Özellik Ekleme Sihirbazı açılır.

3. Seçin **Web sunucusu (IIS) rolü**, ardından aşağıdaki seçeneklerini işaretleyin:

    - Web yönetimi araçları
    - IIS 6 Yönetim uyumluluğu (mevcut özelliklerin tümünü Seç)
    - IIS Yönetim Konsolu
    - IIS Yönetim betikleri ve araçları
    - IIS Yönetim hizmeti

4. Seçin **World Wide Web Hizmetleri**, aşağıdaki seçeneklerini işaretleyin:

     Uygulama geliştirme özellikleri:
    - .NET genişletilebilirliği
    - ASP.NET
    - Ortak HTTP özellikleri: Mevcut özelliklerin tümünü Ekle
    - Durum ve Tanılama: Mevcut özelliklerin tümünü Ekle
    - Güvenlik:
        - Temel Kimlik Doğrulaması
        - Windows Kimlik Doğrulaması

5. Seçin **Windows İşlem Etkinleştirme hizmeti** ve tüm alt öğeleri.

6. İçin **özellikleri**, kontrol **Microsoft .NET framework 3.5.1**, aşağıdaki seçeneklerini işaretleyin:

    - Windows Communication Foundation HTTP etkinleştirme
    - Windows Communication Foundation HTTP olmayan etkinleştirme

7. İçin **özellikleri**, kontrol **Microsoft .NET framework 4.6**, aşağıdaki seçeneklerini işaretleyin:

   - Adlandırılmış kanal etkinleştirmesi
   - TCP etkinleştirme
   - TCP bağlantı noktası paylaşma

     ![Rol ve Özellik Ekleme Sihirbazı'nı ekleyin: Rol Hizmetleri](media/01-demo-roles.png)

8. Tüm seçenekleri seçtikten sonra tıklayın **sonraki** yüklemek için.

9. Windows özellikleri sonra Git **Denetim Masası \> sistem ve güvenlik \> Yönetimsel Araçlar**seçip **Hizmetleri**. Ekranı aşağı kaydırın ve aşağıdaki Hizmetleri çalıştıran ve ayarlayın sağlayın **otomatik**:

    - **NetTcpPortSharing**
    - **Net.Pipe Dinleyici Bağdaştırıcısı**
    - **NET.TCP dinleyicisi bağdaştırıcısı**

10. IIS ve WAS'ta desteğini yapılandırmak için menüden bulun **Micro odak Kurumsal Geliştirici komut istemi (64-bit)** ve Farklı Çalıştır **yönetici**.

11. Tür **wassetup – i** basın **Enter**.

12. Betik çalıştıktan sonra pencereyi kapatabilirsiniz.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server için yerel sistem hesabını yapılandırın

Veritabanları ve diğer nesneleri oluşturma ve SQL Server'da oturum açabilmesi bazı kurumsal sunucu işlemlerini gerekir. Bu işlemler, yerel sistem hesabını kullanır, bu nedenle, bu hesaba sysadmin yetki vermeniz gerekir.

1. Başlatma **SSMS** tıklatıp **Connect** yerel SQLEXPRESS Windows kimlik doğrulaması kullanarak sunucuya bağlanma. Kullanılabilir olması **sunucu adı** listesi.

2. Sol tarafta, genişletin **güvenlik** klasörü ve select **oturumları**.

3. Seçin **NT AUTHORITY\\sistem** seçip **özellikleri**.

4. Seçin **sunucu rolleri** ve **sysadmin**.

     ![SSMS Nesne Gezgini penceresi: Oturum Açma Özellikleri](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo veritabanı ve tüm nesneleri oluşturma

1. Açık **Windows Explorer** gidin **C:\\kullanıcılar\\genel\\belgeleri\\Micro odak\\Kurumsal Geliştirici\\ Örnekleri\\anabilgisayar\\CICS\\DotNet\\BankDemo\\SQL**.

2. İçeriğini kopyalayın **BankDemoCreateAll.SQL** panonuza dosyasına.

3. Açık **SSMS**. Sağ tarafta tıklayın **sunucu** seçip **yeni sorgu**.

4. Panonun içindekileri yapıştırın **yeni sorgu** kutusu.

5. Tıklayarak SQL yürütme **yürütme** gelen **komut** sorgu yukarıda sekmesi.

Sorgu, herhangi bir hata ile çalıştırmanız gerekir. İşlem tamamlandığında BankDemo uygulama için örnek veritabanı vardır.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Veritabanı tabloları ve nesneleri oluşturulmuş olduğunu doğrulayın

1. Sağ **BANKDEMO** seçin ve veritabanı **Yenile**.

2. Genişletin **veritabanı** seçip **tabloları**. Aşağıdaki gibi görmeniz gerekir.

     ![Nesne Gezgini'nde genişletilmiş BANKDEMO tablo](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Kurumsal Geliştirici uygulama oluşturun

1. Visual Studio'yu açın ve oturum açın.

2. Altında **dosya** seçin menü seçeneği **proje/çözüm Aç**, gitmek **C:\\kullanıcılar\\genel\\belgeleri\\Micro Odak\\Kurumsal Geliştirici\\örnekleri\\anabilgisayar\\CICS\\DotNet\\BankDemo**seçip **sln**dosya.

3. Nesneleri incelemek için biraz zaman ayırın. Çözüm Gezgini'nde, yanı sıra CopyBooks (CPY) ve JCL CBL uzantılı COBOL programlar gösterilmektedir.

4. Sağ **BankDemo2** proje ve select **başlangıç projesi olarak ayarla**.

    > [!NOTE]
    > Bu Tanıtım için kullanılmayan HCOSS (SQL Server için konak Uyumluluk seçeneği), kullanım BankDemo proje yapar.

5. İçinde **Çözüm Gezgini**, sağ **BankDemo2** proje ve select **yapı**.

    > [!NOTE]
    > HCOSS yapılandırılmamış gibi yapı çözüm düzeyinde hatalarla sonuçlanır.

6. Proje derlenirken inceleyin **çıkış** penceresi. Bu işlem, aşağıdaki resimdeki gibi görünmelidir.

     ![Çıktı penceresi başarılı derleme gösterme](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Bölge veritabanı BankDemo uygulamasına dağıtma

1. Bir kurumsal Geliştirici komut istemi (64-bit) yönetici olarak açın.

2. Gidin **genel %\\belgeleri\\Micro odak\\Kurumsal Geliştirici\\örnekleri\\anabilgisayar\\CICS\\DotNet\\ BankDemo**.

3. Komut isteminde çalıştırmak **bankdemodbdeploy** ve, örneğin dağıtmak bir veritabanı için parametresini ekleyin:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Eğik çizgi (/) değil ters eğik çizgi kullandığınızdan emin olun (\\). Bu betik, bir süredir çalıştırır.

![Yönetim: Kurumsal Geliştirici komut istemi penceresi](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>.NET için kuruluş yöneticisi BankDemo bölge oluştur

1. Açık **.NET Yönetim kuruluş sunucusu** kullanıcı Arabirimi.

2. Windows MMC ek bileşenini başlatmak için **Başlat** menüsünde seçin **Micro odak Kurumsal Geliştirici \> yapılandırma \> .NET yönetici kuruluş sunucusu**. (Windows Server'de seçin **Micro odak Kurumsal Geliştirici \> .NET yönetici kuruluş sunucusu**).

3. Genişletin **bölgeleri** seçip sağ tıklayın ve sol bölmedeki kapsayıcısında **CICS**.

4. Seçin **tanımlamak bölge** adlı yeni bir CICS bölgesi oluşturmak için **BANKDEMO**, (yerel) veritabanında barındırılan.

5. Veritabanı sunucusu örneğine sağlayın, tıklayın **sonraki**ve ardından bölge adı girin **BANKDEMO**.

     ![Bölge iletişim kutusu tanımlayın](media/07-demo-cics.png)

6. Bölgeler arası veritabanı için bölge tanım dosyasını seçmek için bulun **bölge\_bankdemo\_db.config** içinde **C:\\kullanıcılar\\genel\\ Belgeler\\Micro odak\\Kurumsal Geliştirici\\örnekleri\\anabilgisayar\\CICS\\DotNet\\BankDemo**.

     ![Bölge - bölge adı tanımlayın: BANKDEMO](media/08-demo-cics.png)

7.           **Son**'a tıklayın.

## <a name="create-xa-resource-definitions"></a>XA Kaynak tanımları oluşturma

1. Sol bölmesinde **.NET Yönetim için Enterprise Server** kullanıcı Arabiriminde, genişletme **sistem**ve ardından **XA Kaynak tanımları**. Bu ayar, kuruluş sunucusu ve uygulama veritabanlarını ile bölgeye nasıl birlikte çalışır tanımlar.

2. Sağ **XA Kaynak tanımları** seçip **sunucuyu eklemek**.

3. Aşağı açılan kutusunda **veritabanı hizmet örneği**. Bu, yerel makine SQLEXPRESS olacaktır.

4. Örneği altından seçin **XA Kaynak tanımları (machinename\\sqlexpress)** kapsayıcı ve tıklatın **Ekle**.

5. Seçin **veritabanı XA kaynak tanımı** yazın **BANKDEMO** için **adı** ve **bölge**.

     ![Yeni veritabanı XA kaynak tanımı filtresi](media/09-demo-xa.png)

6. Üç noktaya tıklayın ( **...** ) için bağlantı dizesi sihirbazını getirir. İçin **sunucu adı**, türü **(yerel)\\SQLEXPRESS**. İçin **oturum açma**seçin **Windows kimlik doğrulaması**. Veritabanı adını yazın **BANKDEMO**

     ![Bağlantı dizesi ekran Düzenle](media/10-demo-string.png)

7. Bağlantıyı test edin.

## <a name="start-the-bankdemo-region"></a>BANKDEMO bölge Başlat

> [!NOTE]
> İlk adım büyük/küçük harf önemlidir: Yeni oluşturduğunuz XA kaynak tanımı bölge ayarlamanız gerekir.

1. Gidin **BANDEMO CICS bölge** altında **bölgeleri kapsayıcı**ve ardından **bölge başlangıç dosyası Düzenle** gelen **eylemleri** bölme. SQL özellikleri aşağı kaydırın ve girin **bankdemo** için **XA kaynak adı**, veya nokta seçmek için kullanın.

2. Tıklayın **Kaydet** yaptığınız değişiklikleri kaydetmek için simge.

3. Sağ **BANKDEMO CICS bölge** içinde **konsol** bölmesi ve seçin **başlatma/durdurma bölge**.

4. ' In en altındaki **başlatma/durdurma bölge** Ortadaki bölmeden seçin açılan kutusunda **Başlat**. Birkaç saniye sonra bölgeyi başlatır.

     ![SQL Başlat/Durdur kutusu](media/11-demo-sql.png)

     ![CICS bölge BANKDEMO - başlangıç ekranı](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Bir dinleyici oluşturun

BankDemo uygulamaya erişim TN3270 oturumlar için bir dinleyici oluşturun.

1. Sol bölmede genişletin **yapılandırma düzenleyicileri** seçip **dinleyici**.

2. Tıklayın **açık dosya** simgesini seçip alt **seelistener.exe.config** dosya. Bu dosya, düzenlenecek ve kuruluş sunucusu her başladığında yüklenir.

3. İki bölgeleri (ESDEMO ve JCLDEMO) önceden tanımlanmış dikkat edin.

4. BANKDEMO için yeni bir bölge oluşturmak için **bölgeleri**seçip **bölge ekleme**.

5. Seçin **BANKDEMO bölge**.

6. Sağ tıklayarak bir TN3270 kanalı eklemek **BANKDEMO bölge** seçerek **Ekle kanal**.

7. İçin **adı**, girin **TN3270**. İçin **bağlantı noktası**, girin **9024**. ESDEMO uygulama, bağlantı noktası 9230 kullanır, bu nedenle, farklı bir bağlantı noktası kullanmanız gerekir.

8. Dosyayı kaydetmek için tıklatın **Kaydet** simgesi veya **dosya** \> **Kaydet**.

9. Dinleyici başlatmak için tıklatın **dinleyicisi başlatın** simgesine ya da seçin **seçenekleri** \> **dinleyicisi başlatın**.

     ![Dinleyici yapılandırma Düzenleyicisi windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Rumba BankDemo uygulamaya erişmek için yapılandırma

Gerçekleştirmeniz gereken son Rumba, 3270 öykünücüsü kullanarak bir 3270 oturum yapılandırma şeydir. Bu adım, oluşturduğunuz dinleyicisi BankDemo uygulamaya erişim sağlar.

1. Windows gelen **Başlat** menüsünde Rumba Masaüstü başlatın.

2. Altında **bağlantıları** menü öğesi, select **TN3270**.

3. Tıklayın **Ekle** ve türü **127.0.0.1** IP adresi ve **9024** için kullanıcı tanımlı bir bağlantı noktası.

4. İletişim kutusunun altındaki tıklatın **Connect**. Siyah CICS ekran görünür.

5. Tür **banka** BankDemo uygulamanın ilk 3270 ekranı görüntülemek için.

6. Kullanıcı kimliği için tür **B0001** ve herhangi bir şey parolasını yazın. İlk ekrana BANK20 açılır.

![Ana görüntü Hoş Geldiniz ekranı](media/14-demo.png)
![anabilgisayar görüntüleme - Rumba - alt tanıtım ekran](media/15-demo.png)

Tebrikler! Micro odak kuruluş sunucusu kullanarak Azure'da bir CICS uygulaması şimdi çalışıyor.

## <a name="next-steps"></a>Sonraki adımlar

- [Kuruluş sunucusu, Azure üzerinde Docker kapsayıcılarında çalıştırın](run-enterprise-server-container.md)
- [Ana bilgisayar geçişi - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Sanal Makineler](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Sorun giderme](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Azure'a geçiş için ana bilgisayar demystifying](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
