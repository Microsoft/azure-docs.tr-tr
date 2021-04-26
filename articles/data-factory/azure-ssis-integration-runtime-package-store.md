---
title: Paketleri Azure-SSIS Integration Runtime paket deposuyla yönetme
description: Azure-SSIS Integration Runtime paket deposu ile paketlerin nasıl yönetileceğini öğrenin.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393845"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Paketleri Azure-SSIS Integration Runtime paket deposuyla yönetme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Şirket içi SQL Server Integration Services (SSIS) iş yüklerinizi Buluta taşımak & için, Azure Data Factory (ADF) içinde Azure-SSIS Integration Runtime (IR) sağlayabilirsiniz. Daha fazla bilgi için bkz. [sağlama Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). Bir Azure-SSIS IR şunları destekler:

- Azure SQL veritabanı sunucusu/yönetilen örneği (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma
- Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtılan Paketleri çalıştırma

Paket dağıtım modelini kullanırken Azure-SSIS IR paket depolarıyla sağlamak isteyip istemediğinizi seçebilirsiniz. Bunlar, Azure SQL yönetilen örneği tarafından barındırılan dosya sisteminin, Azure dosyalarının veya MSDB 'nin üzerinde bir paket yönetim katmanı sağlar. Azure-SSIS IR paket deposu, paketleri içeri/dışarı/dışarı/dışarı/dışarı aktarmanıza/çalıştırmanıza ve [eskı SSIS paket deposuna](/sql/integration-services/service/package-management-ssis-service)benzer SQL Server Management Studio (SSMS) üzerinden çalışan paketlerin izlenmesini/durdurulmasına olanak tanır. 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR Bağlan

Azure-SSIS IR sağlandıktan sonra, SSMS 'de paket depolarına gözatabilmeniz için bu dosyaya bağlanabilirsiniz.

![Azure-SSIS IR Bağlan](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

SSMS **Nesne Gezgini** penceresinde, **bağlan** açılan menüsünde **Azure-SSIS Integration Runtime** ' i seçin. Ardından, Azure 'da oturum açın ve paket depolarıyla sağladığınız ilgili aboneliği, ADF 'yi ve Azure-SSIS IR seçin. Azure-SSIS IR, altında **çalışan paketler** ve **depolanmış paketler** düğümleri ile birlikte görüntülenir. Paket mağazalarınızı altında görmek için **saklı paketler** düğümünü genişletin. Altındaki klasörleri ve paketleri görmek için paket mağazalarınızı genişletin. SSMS bunlara otomatik olarak bağlanamıyorsa paket depolarınız için erişim kimlik bilgilerini girmeniz istenebilir. Örneğin, MSDB 'nin üzerinde bir paket deposu genişletirseniz, önce Azure SQL yönetilen örneğine bağlanmanız istenebilir.

![Azure SQL yönetilen örneği 'ne Bağlan](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Klasörleri ve paketleri yönetme

SSMS üzerinde Azure-SSIS IR bağlandıktan sonra, bir menü açmak ve **Yeni klasör**, **paketi Içeri** aktar, **paketi dışarı aktar**, **Sil** veya **Yenile**' yi seçmek için herhangi bir paket mağazalarına, klasöre veya pakete sağ tıklayabilirsiniz.

   ![Klasörleri ve paketleri yönetme](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  İçeri aktarılan paketler için yeni bir klasör oluşturmak üzere **Yeni klasör** ' ü seçin.

   *  Paketleri **dosya sisteminden**, **SQL Server** (msdb) veya eski **SSIS paketi deposundan** paket deponuza içeri aktarmak için **paketi içeri aktar** ' ı seçin.

      ![Paketi içeri aktar](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      İçinden içeri aktarılacak **paket konumuna** bağlı olarak, ilgili **sunucu** / **kimlik doğrulaması türünü** seçin, gerekirse erişim kimlik bilgilerini girin, **paket yolunu** seçin ve yeni **paket adını** girin. Paketler içeri aktarılırken, koruma düzeyi değiştirilemez. Bunu değiştirmek için SQL Server Veri Araçları (SSDT) veya `dtutil` komut satırı yardımcı programını kullanın.

      > [!NOTE]
      > SSIS paketlerinin Azure-SSIS IR paket depolarına aktarılması yalnızca bir tane tarafından yapılabilir ve SQL Server/SSIS sürümünü korurken bunları temel MSDB/dosya sistemine/Azure dosyalarına kopyalayacaktır. 
      >
      > Azure-SSIS IR Şu anda **SQL Server 2017**' i temel aldığı için, alt sürüm paketlerinin yürütülmesi çalışma zamanında ssıs 2017 paketlerine yükseltilir. Daha yüksek sürüm paketlerinin yürütülmesi desteklenmez.
      >
      > Ayrıca, eski SSIS paket depoları belirli SQL Server sürümüne bağlı ve yalnızca bu sürümde SSMS üzerinde erişilebilir olduğundan, eski SSIS paket mağazalarındaki alt sürüm paketlerinin SSMS 2019 veya sonraki sürümleri kullanılarak Azure-SSIS IR paket depolarına içeri aktarılbilmeleri için önce, belirtilen SSMS sürümü kullanılarak dosya sistemine aktarılması gerekir.
      >
      > Alternatif olarak, koruma düzeylerini değiştirirken birden çok SSIS paketini Azure-SSIS IR paket depolarına aktarmak için [dtutil](/sql/integration-services/dtutil-utility) komut satırı yardımcı programını kullanabilirsiniz. bkz. [dtutil ile birden çok paket dağıtma](#deploying-multiple-packages-with-dtutil).

   *  Paketleri paket deponuzdan **dosya sistemine**, **SQL Server** (msdb) veya eski **SSIS paket deposuna** dışarı aktarmak için **paketi dışarı aktar** ' ı seçin.

      ![Paketi dışarı aktar](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Uygulamasına verilecek **paket konumuna** bağlı olarak, ilgili **sunucu** / **kimlik doğrulaması türünü** seçin, gerekirse erişim kimlik bilgilerini girin ve **paket yolunu** seçin. Paketleri dışa aktarırken, önce şifrelerini çözmek için parolaları girin ve ardından, hassas verilerin depolanmasını veya Kullanıcı anahtarı ya da parola ile tüm verileri şifrelemeyi önlemek için koruma düzeyini değiştirebilirsiniz.

      > [!NOTE]
      > Azure-SSIS IR paket mağazalarından SSIS paketlerini dışa aktarma işlemi yalnızca bir tane tek tek yapılabilir ve bu sayede koruma düzeyini değiştirmeden bu, SQL Server dosyaları SSIS 2019 veya üzeri sürüm paketlerine yükseltecektir.
      >
      > Azure-SSIS IR Şu anda **SQL Server 2017**' i temel aldığı için, alt sürüm paketlerinin yürütülmesi çalışma zamanında ssıs 2017 paketlerine yükseltilir. Daha yüksek sürüm paketlerinin yürütülmesi desteklenmez.
      >
      > Alternatif olarak, koruma düzeylerini değiştirirken Azure-SSIS IR paket mağazalarından birden çok SSIS paketini dışarı aktarmak için [dtutil](/sql/integration-services/dtutil-utility) komut satırı yardımcı programını kullanabilirsiniz. bkz. [dtutil ile birden çok paket dağıtma](#deploying-multiple-packages-with-dtutil).

   *  Paket deponuzdan mevcut klasörleri/paketleri silmek için **Sil** ' i seçin.

   *  Paket deponuzda yeni eklenen klasörleri/paketleri görüntülemek için **Yenile** ' yi seçin.

## <a name="execute-packages"></a>Paketleri Yürüt

SSMS üzerinde Azure-SSIS IR bağlandıktan sonra, bir menü açmak ve **paketi Çalıştır**' ı seçmek için saklı paketlere sağ tıklayabilirsiniz.  Bu, ADF işlem hatlarında SSIS paket etkinliklerini yürütme olarak Azure-SSIS IR üzerinde paket yürütmelerini yapılandırabileceğiniz **paket yürütme yardımcı programı** iletişim kutusunu açar.

![Paket Yürütme Yardımcı Programı sayfaları 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Paket Yürütme Yardımcı Programı sayfaları 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**Paket yürütme yardımcı programı** Iletişim kutusunun **genel**, **yapılandırma**, **yürütme seçenekleri** ve **günlük** sayfaları, SSIS paketi yürütme etkinliğinin **Ayarlar** sekmesine karşılık gelir. Bu sayfalarda, paketinizin şifreleme parolasını ve paket yapılandırma dosyanız için erişim bilgilerini girebilirsiniz. Ayrıca, paket yürütme kimlik bilgilerinizi ve özelliklerini ve günlük klasörünüz için erişim bilgilerini de girebilirsiniz.  **Paket yürütme yardımcı programı** Iletişim kutusunun **değerleri ayarla** sayfası, geçersiz kılmak için mevcut paket özelliklerinizi girebileceğiniz SSIS paketi yürütme etkinliğinin **geçersiz kılmaları** sekmesine karşılık gelir. Daha fazla bilgi için bkz. [ADF işlem hatları içinde SSIS paketlerini yürütme SSIS paketi etkinlikleri](./how-to-invoke-ssis-package-ssis-activity.md).

**Yürüt** düğmesini SEÇTIĞINIZDE, SSIS paketi yürütme etkinliğine sahip yenı bir ADF işlem hattı otomatik olarak oluşturulup tetiklenir. Aynı ayarlara sahip bir ADF işlem hattı zaten varsa, yeniden çalıştırılır ve yeni bir işlem hattı oluşturulmaz. ADF işlem hattı ve SSIS paketi yürütme etkinliği `Pipeline_SSMS_YourPackageName_HashString` sırasıyla ve olarak adlandırılır `Activity_SSMS_YourPackageName` .

![Paket Yürütme Yardımcı Programı düğmesi](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS paketi yürütme etkinliği](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Çalışan paketleri izleme ve durdurma

SSMS üzerinde Azure-SSIS IR bağlandıktan sonra, çalışmakta olan paketleri görmek için **çalışan paketler** düğümünü genişletebilirsiniz.  Bir menü açmak için bunlardan birine sağ tıklayın ve **Durdur** veya **Yenile**' yi seçin.

   ![Çalışan paketleri izleme ve durdurma](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Paketi, SSIS paketi yürütme etkinliğini çalıştıran, çalışmakta olan ADF ardışık düzenini iptal etmek için **Durdur** ' u seçin.

   *  Paket mağazalarınızın yeni çalışan paketlerini göstermek için **Yenile** ' yi seçin.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR izleme ve paket depolarını düzenleme

SSMS üzerinde Azure-SSIS IR bağlandıktan sonra, bir menü açmak için sağ tıklayıp **Azure Data Factory portala git** veya **Yenile**' yi seçebilirsiniz.

   ![ADF portalına git](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Azure-SSIS IR izleyebileceğiniz ADF izleme hub 'ının **tümleştirme çalışma zamanları** sayfasını açmak için **Azure Data Factory portalına git** ' i seçin. **Paket depoları** kutucuğunda, Azure-SSIS IR bağlı paket deposunun sayısını görebilirsiniz.  Bu numarayı seçtiğinizde, paket depolarınız için erişim bilgilerini depolayan ADF bağlantılı hizmetleri düzenleyebileceğiniz bir pencere açılır.

      ![Paket depolarını Düzenle](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Paketinizdeki yeni eklenen klasörleri/paketleri göstermek için **Yenile** ' yi seçin ve paketleri paket mağazalarından depolar ve çalıştırın.

## <a name="deploying-multiple-packages-with-dtutil"></a>Dtutil ile birden çok paket dağıtma

Eski paket dağıtım modelini koruyarak, şirket içi SSIS iş yüklerinizi ADF 'de SSIS 'ye taşımak &, paketlerinizi dosya sisteminden dağıtmanız, SQL Server tarafından barındırılan MSDB veya eski SSIS paketi mağazalarının Azure dosyaları, Azure SQL yönetilen örneği tarafından barındırılan MSDB veya Azure-SSIS IR paket depoları ile dağıtılması gerekir. Aynı zamanda, daha önce yapmadıysanız, koruma düzeyini Kullanıcı anahtarına göre şifrelemeden veya parola ile şifrelemesine geçmeniz gerekir.

Birden çok paketi toplu halde dağıtmak için SQL Server/SSIS yüklemesiyle birlikte gelen [dtutil](/sql/integration-services/dtutil-utility) komut satırı yardımcı programını kullanabilirsiniz. Bu, belirli bir SSIS sürümüne bağlanır. bu nedenle, koruma düzeyini değiştirmeden alt sürüm paketlerini dağıtmak için kullanıyorsanız, SSIS sürümünü korurken yalnızca bunları kopyalayacaktır. Bunları dağıtmak ve koruma düzeyini aynı anda geçirmek için kullanırsanız, bunları SSIS sürümüne yükseltir.

 Azure-SSIS IR Şu anda **SQL Server 2017**' i temel aldığı için, alt sürüm paketlerinin yürütülmesi çalışma zamanında ssıs 2017 paketlerine yükseltilir. Daha yüksek sürüm paketlerinin yürütülmesi desteklenmez.

Sonuç olarak, çalışma zamanı yükseltmelerinden kaçınmak için paket dağıtım modelinde Azure-SSIS IR üzerinde çalıştırılacak paketlerin dağıtılması SQL Server/SSIS 2017 yüklemesiyle birlikte gelen dtutil 2017 kullanmalıdır. Ücretsiz [SQL Server/ssıs 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) 'ı bu amaçla indirip yükleyebilirsiniz. Yüklendikten sonra, bu klasörde dtutil 2017 bulabilirsiniz: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Dtutil ile şirket içi dosya sisteminden Azure dosyalarına birden çok paket dağıtma

 Dosya sisteminden Azure dosyalarına birden çok paket dağıtmak ve koruma düzeyini aynı anda geçirmek için bir komut isteminde aşağıdaki komutları çalıştırabilirsiniz. Lütfen özel duruma özgü tüm dizeleri değiştirin.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Yukarıdaki komutları bir toplu iş dosyasında çalıştırmak için `%f` ile değiştirin `%%f` .

Dosya sisteminin en üstündeki eski SSIS paket mağazalarından birden çok paketi Azure dosyalarına dağıtmak ve koruma düzeyini aynı anda geçirmek için aynı komutları kullanabilirsiniz, ancak `YourLocalDrive:\...\YourPackageFolder` eskı SSIS paket depoları tarafından kullanılan bir yerel klasörle değiştirebilirsiniz: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Örneğin, eski SSIS paket depkaladınız 2016 SQL Server bağlıysa, adresine gidin `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Değerini, `YourSQLServerDefaultCompatibilityLevel` [SQL Server varsayılan uyumluluk seviyeleri listesinden](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)bulabilirsiniz.

Azure dosyalarının en üstünde Azure-SSIS IR paket depoları yapılandırdıysanız, SSMS 2019 veya sonraki sürümlerde Azure-SSIS IR bağlandığınızda dağıtılan paketleriniz burada görünür.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Dtutil ile Azure 'da MSDB 'ye birden çok paket dağıtma

 Msdb 'den Azure SQL yönetilen örneği tarafından barındırılan ve koruma düzeyini aynı anda değiştiren SQL Server veya eski SSIS paketi depoları tarafından barındırılan MSDB 'den birden çok paket dağıtmak için, `Databases->System Databases->msdb` SSMS 'de SQL Server bir **Nesne Gezgini** düğüme sağ tıklayıp **Yeni bir sorgu** penceresi açın ve aşağıdaki T-SQL betiğini çalıştırın. Lütfen özel duruma özgü tüm dizeleri değiştirin:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Azure SQL yönetilen örneğinizin özel/genel uç noktasını kullanmak için `YourSQLManagedInstanceEndpoint` sırasıyla ile değiştirin `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` .

Betik, bir komut isteminde çoklu seçim yapmak, kopyalamak & yapıştırmak ve çalıştırmak üzere, MSDB 'deki tüm paketler için dtutil komut satırları oluşturur.

![Dtutil komut satırları oluştur](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

MSDB 'nin üzerinde Azure-SSIS IR paket depoları yapılandırdıysanız, SSMS 2019 veya sonraki sürümlerde Azure-SSIS IR bağlandığınızda dağıtılan paketleriniz burada görünür.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Dtutil ile, şirket içinde MSDB 'den Azure dosyalarına birden çok paket dağıtma

 Msdb 'den Azure dosyalarına MSDB 'nin üzerinde SQL Server veya eski SSIS paketi depoları tarafından barındırılan birden çok paket dağıtmak ve koruma düzeyini aynı anda geçirmek için, SSMS üzerinde SQL Server bağlanabilir, `Databases->System Databases->msdb` SSMS 'nin **Nesne Gezgini** düğüme sağ tıklayarak **Yeni bir sorgu** penceresi AÇABILIR ve aşağıdaki T-SQL betiğini çalıştırabilirsiniz. Lütfen özel duruma özgü tüm dizeleri değiştirin:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Betik, bir komut isteminde çoklu seçim yapmak, kopyalamak & yapıştırmak ve çalıştırmak üzere, MSDB 'deki tüm paketler için dtutil komut satırları oluşturur.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Azure dosyalarının en üstünde Azure-SSIS IR paket depoları yapılandırdıysanız, SSMS 2019 veya sonraki sürümlerde Azure-SSIS IR bağlandığınızda dağıtılan paketleriniz burada görünür.

## <a name="next-steps"></a>Sonraki adımlar

Otomatik olarak oluşturulan ADF işlem hatlarını, SSIS paketi etkinliklerini yürütün veya ADF portalında yeni bir tane oluşturabilirsiniz. Daha fazla bilgi için bkz. [ADF işlem hatları içinde SSIS paketlerini yürütme SSIS paketi etkinlikleri](./how-to-invoke-ssis-package-ssis-activity.md).