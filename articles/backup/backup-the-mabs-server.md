---
title: MABS sunucusunu yedekleme
description: Microsoft Azure Backup sunucusunu (MABS) nasıl yedekleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519128"
---
# <a name="back-up-the-mabs-server"></a>MABS sunucusunu yedekleme

Microsoft Azure Backup sunucusu (MABS) başarısız olursa verilerin kurtarılabilmesini sağlamak için, MABS sunucusunu yedeklemeye yönelik bir stratejiniz olması gerekir. Yedeklenmemişse, bir hatadan sonra el ile yeniden oluşturmanız gerekir ve disk tabanlı kurtarma noktaları kurtarılamaz. Mabs sunucularını, MABS veritabanını yedekleyerek yedekleyebilirsiniz.

## <a name="back-up-the-mabs-database"></a>MABS veritabanını yedekleme

MABS yedekleme stratejinizin bir parçası olarak, MABS veritabanını yedeklemeniz gerekir. MABS veritabanının adı DPMDB 'dir. Bu veritabanı, mabs yedeklerinin yedekleri hakkındaki verilerle birlikte MABS yapılandırmasını içerir. Bir olağanüstü durum varsa, veritabanının son yedeklemesini kullanarak bir MABS sunucusunun işlevselliğinin çoğunu yeniden oluşturabilirsiniz. Veritabanını geri yükleyebildiğiniz varsayıldığında, bant tabanlı yedeklemeler erişilebilir ve tüm koruma grubu ayarlarını ve yedekleme zamanlamalarını korur. MABS depolama havuzu diskleri kesintiden etkilenmediyse, bir yeniden oluşturma işleminden sonra disk tabanlı yedeklemeler de kullanılabilir. Veritabanını yedeklemek için birbirinden farklı çeşitli yöntemler kullanabilirsiniz.

|Veritabanı yedekleme yöntemi|Avantajlar|Dezavantajlar|
|--------------------------|--------------|-----------------|
|[Azure'a yedekleme](#back-up-to-azure)|<li>MABS 'de kolayca yapılandırılıp izlenir.<li>Yedekleme veritabanı dosyalarının birden çok konumu.<li>Bulut depolama, olağanüstü durum kurtarma için sağlam bir çözüm sağlar.<li>Veritabanı için çok güvenli depolama alanı.<li>120 çevrimiçi kurtarma noktasını destekler.|<li>Azure hesabı ve ek MABS yapılandırması gerektirir. Azure depolama için bazı maliyetler doğurur.<li> Azure yedekleme kasasında depolanan MABS yedeklemelerine erişim kazanmak için Azure aracısına sahip Windows Server tabanlı sistem 'in desteklenen bir sürümünü gerektirir. Bu, başka bir MABS sunucusu olamaz.<li>Veritabanı yerel olarak barındırılıyorsa ve ikincil korumayı etkinleştirmek istiyorsanız bu bir seçenek değildir. <li>Bazı ek hazırlıklar ve kurtarma zamanı oluşur.|
|[MABS depolama havuzunu yedekleyerek veritabanını yedekleme](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Yapılandırması ve izlemesi basit.<li>Yedekleme, MABS depolama havuzu disklerinde tutulur ve yerel olarak kolayca erişebilir.<li>MABS zamanlanan yedeklemeler 512 hızlı tam yedeklemeleri destekler. Saatlik olarak yedekleme yaptıysanız 21 gün tam koruma olacaktır.|<li>Olağanüstü durum kurtarma için iyi bir seçenek değildir. Bu çevrimiçi olarak, MABS sunucusu veya depolama havuzu diski başarısız olursa kurtarma beklendiği gibi çalışmayabilir.<li>Veritabanı yerel olarak barındırılıyorsa ve ikincil korumayı etkinleştirmek istiyorsanız bu bir seçenek değildir. <li>MABS hizmeti veya konsolu çalışmıyorsa veya çalışmıyorsa kurtarma noktalarına erişim kazanmak için bazı hazırlıklar ve özel adımlar gerekir.|
|[Yerel SQL Server yedekleme ile yerel bir diske yedekleme](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>SQL Server'da yerleşik.<li>Yedekleme, kolayca erişilebilen yerel bir diskte tutulur.<li>Dilediğiniz sıklıkta çalışacak şekilde zamanlanabilir.<li>MABS 'tan tamamen bağımsız.<li>Bir yedekleme dosyası temizlemesi zamanlayabilirsiniz.|<li>Yedeklemeler uzak bir konuma kopyalanmadığı sürece olağanüstü durum kurtarma için iyi bir seçenek değildir.<li>, Bekletme ve sıklığı sınırlayabilen yedeklemeler için yerel depolama gerektirir.|
|[Yerel SQL yedekleme ve MABS korumasıyla birlikte MABS tarafından korunan bir paylaşıma yedekleme](#back-up-to-a-share-protected-by-mabs)|<li>MABS 'de kolayca izlenir.<li>Yedekleme veritabanı dosyalarının birden çok konumu.<li>Ağ üzerindeki herhangi bir Windows makineden kolayca erişilebilir.<li>Potansiyel olarak en hızlı kurtarma yöntemi.|<li>Yalnızca 64 kurtarma noktasını destekler.<li>Site olağanüstü durum kurtarması için iyi bir seçenek değildir. MABS sunucu veya MABS depolama havuzu disk hatası, kurtarma çabalarını gösterebilir.<li>MABS DB yerel olarak barındırılıyorsa ve ikincil korumayı etkinleştirmek istiyorsanız bu bir seçenek değildir. <li>Yapılandırmak ve test etmek için bazı ek hazırlıklar gerekir.<li>MABS sunucusunun kendisi, MABS depolama havuzu diskleri iyi bir şekilde bir süre sonra olmalıdır.|

- Bir MABS koruma grubunu kullanarak yedekleme yaparsanız veritabanı için benzersiz bir koruma grubu kullanmanızı öneririz.

    > [!NOTE]
    > Geri yükleme amacıyla, MABS veritabanıyla geri yüklemek istediğiniz MABS yüklemesi, MABS veritabanının sürümüyle aynı olmalıdır.  Örneğin, kurtarmak istediğiniz veritabanı bir MABS v3 güncelleştirme paketi 1 yüklemesiyle çalışıyorsa, MABS sunucusunun güncelleştirme paketi 1 ile aynı sürümü çalıştırması gerekir. Bu, veritabanını geri yüklemeden önce, MABS 'yi, uyumlu bir sürümle kaldırmanız ve yeniden yüklemeniz gerekebilecek anlamına gelir.  Veritabanı sürümünü denetlemek için bunu bir geçici veritabanı adına el ile bağlamanız ve ardından birincil ve ikincil sürüme göre yüklenen son paketi denetlemek için veritabanında bir SQL sorgusu çalıştırmanız gerekebilir.

- MABS veritabanı sürümünü denetlemek için şu adımları izleyin:

    1. Sorguyu çalıştırmak için SQL Management Studio açın ve ardından MABS veritabanını çalıştıran SQL örneğine bağlanın.

    2. MABS veritabanını seçin ve ardından yeni bir sorgu başlatın.

    3. Aşağıdaki SQL sorgusunu sorgu bölmesine yapıştırın ve çalıştırın:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Sorgu sonuçlarında hiçbir şey döndürülmezse veya MABS sunucusu önceki sürümlerden yükseltilmişse, ancak bundan sonra yeni bir güncelleştirme paketi yüklenmemişse, MABS 'nin temel yüklemesi için birincil ve ikincil için bir giriş olmayacaktır. Güncelleştirme paketleri ile ilişkili MABS sürümlerini denetlemek için bkz. [mabs Için yapı numaraları listesi](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Azure'a yedekleme

1. Başlamadan önce, mabs çoğaltma birimi bağlama noktası yolunu almak için bir komut dosyası çalıştırmanız gerekir; böylece, hangi kurtarma noktasının MABS yedeklemesini içerdiğini bilirsiniz. Bu işlemi Azure Backup ile ilk çoğaltmadan sonra yapın. Komut dosyasında, öğesini `dplsqlservername%` MABS veritabanını barındıran SQL Server örneğinin adıyla değiştirin.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Azure kurtarma hizmetleri Aracısı yüklü olduğunda ve MABS sunucusu Azure Backup kasasında kaydettirilirse belirtilen geçiş koduna sahip olduğunuzdan emin olun. Yedeklemeyi geri yüklemek için bu geçiş koduna sahip olmanız gerekir.

2. Bir Azure Backup kasası oluşturun, Azure Backup Aracısı yükleme dosyasını ve kasa kimlik bilgilerini indirin. Aracıyı MABS sunucusuna yüklemek için yükleme dosyasını çalıştırın ve MABS sunucusunu kasaya kaydetmek için kasa kimlik bilgilerini kullanın. [Daha fazla bilgi edinin](backup-azure-microsoft-azure-backup.md).

3. Kasa yapılandırıldıktan sonra, MABS veritabanını içeren bir MABS koruma grubu ayarlayın. Bunu diske ve Azure 'a yedeklemek için seçin.

#### <a name="recover-the-mabs-database-from-azure"></a>MABS veritabanını Azure 'dan kurtarma

Azure Backup kasasında kayıtlı olan MABS sunucusunu kullanarak Azure 'dan veritabanını aşağıdaki şekilde kurtarabilirsiniz:

1. Mabs konsolunda, **Kurtarma**  >  **dış mabs Ekle**' yi seçin.

2. Kasa kimlik bilgilerini sağlayın (Azure Backup kasasından indirin). Kimlik bilgilerinin yalnızca iki gün için geçerli olduğunu unutmayın.

3. **Kurtarma Için dış Mabs Seç** bölümünde, veritabanını kurtarmak istediğiniz mabs sunucusunu seçin, şifreleme parolasını yazın ve Tamam ' ı seçin **.**

4. Kullanılabilir noktalar listesinden kullanmak istediğiniz kurtarma noktasını seçin. Yerel MABS görünümüne dönmek için **dış Mabs seçimini temizle** ' yi seçin.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>MABS veritabanını MABS depolama havuzuna yedekleme

> [!NOTE]  
> Bu seçenek Modern Yedekleme Alanı olan MABS için geçerlidir.

1. Mabs konsolunda **koruma**  >  **koruma grubu oluştur**' u seçin.
2. **Koruma Grubu Türünü Seçin** sayfasında **Sunucular**'ı seçin.
3. **Grup üyelerini seçin** sayfasında **DPM veritabanı**' yi seçin. MABS sunucusunu genişletin ve DPMDB ' yi seçin.
4. **Veri koruma yöntemini seçin** sayfasında **disk kullanarak kısa vadeli koruma istiyorum**' u seçin. Kısa vadeli koruma ilkesi seçeneklerini belirtin.
5. MABS veritabanının ilk çoğaltılmasının ardından, aşağıdaki SQL betiğini çalıştırın:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>MABS veritabanını kurtar

MABS 'leri aynı DB ile yeniden oluşturmak için, önce MABS veritabanını kurtarmanız ve en yeniden yüklenen MABS ile eşitlemeniz gerekir.

#### <a name="use-the-following-steps"></a>Aşağıdaki adımları kullanın

1. Bir yönetim komut istemi açın ve `psexec.exe -s powershell.exe` sistem bağlamında bir PowerShell penceresi başlatmak için komutunu çalıştırın.
2. Veritabanını kurtarmak istediğiniz konuma karar verin:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Veritabanını son yedeklemeden kopyalamak için

1. Çoğaltma VHD 'SI yoluna git `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Komutu kullanarak **Disk0. vhdx** ' i takın `mount-vhd disk0.vhdx` .
3. Çoğaltma VHD 'SI bağlandıktan sonra, `mountvol.exe` SQL betiği çıktısından fiziksel çoğaltma kimliği kullanarak çoğaltma birimine bir sürücü harfi atamak için kullanın. Örnek: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Veritabanını önceki bir kurtarma noktasından kopyalamak için

1. DPMDB kapsayıcı dizinine gidin  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . MABS DB için uygulanan ilgili kurtarma noktaları altında bazı benzersiz GUID tanımlayıcılarıyla birden çok dizin görürsünüz. Diğer dizinler bir PIT/kurtarma noktasını temsil eder.
2. Örneğin, herhangi bir PIT VHD yoluna gidin `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` ve komutu kullanarak **Disk0. vhdx** ' i takın `mount-vhd disk0.vhdx` .
3. Çoğaltma VHD 'SI bağlandıktan sonra, `mountvol.exe` SQL betiği çıktısından fiziksel çoğaltma kimliğini kullanarak çoğaltma birimine bir sürücü harfi atamak için kullanın. Örnek: `mountvol X: \?\Volume{}\`

   Yukarıdaki adımlarda yer alan angular ayraçları ile görüntülenen tüm terimler, yer tutucuları. Bunları aşağıdaki gibi uygun değerlerle değiştirin:
   - SQL betiği çıktısındaki **Refsvolume** -Access yolu
   - **Mabsserver FQDN** -mabs sunucusunun tam adı
   - **Physicalreplicaıd** -SQL betiğindeki FIZIKSEL çoğaltma kimliği
   - **Pitıd** -kapsayıcı dizinindeki FIZIKSEL çoğaltma kimliği dışında bir GUID tanımlayıcısı.
4. Başka bir yönetici komut istemi açın ve `psexec.exe -s cmd.exe` sistem bağlamında bir komut istemi başlatmak için komutunu çalıştırın.
5. Dizini X: Drive olarak değiştirin ve MABS veritabanı dosyalarının konumuna gidin.
6. Bunları kolayca geri yükleyebileceğiniz bir konuma kopyalayın. Kopyaladıktan sonra psexec cmd penceresinden çıkın.
7. Adım 1 ' de açılan PsExec PowerShell penceresine gidin, VHDX yoluna gidin ve komutu kullanarak VHDX 'i çıkarın `dismount-vhd disk0.vhdx` .
8. MABS sunucusunu yeniden yükledikten sonra komutunu çalıştırarak MABS sunucusuna eklemek için geri yüklenen DPMDB ' yi kullanabilirsiniz `DPMSYNC-RESTOREDB` .
9. `DPMSYNC-SYNC`Bir kez Çalıştır `DPMSYNC-RESTOREDB` işlemi tamamlanır.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>MABS depolama havuzunu yedekleyerek veritabanını yedekleme

> [!NOTE]
> Bu seçenek, eski depolama alanı olan MABS için geçerlidir.

Başlamadan önce, mabs çoğaltma birimi bağlama noktası yolunu almak için bir komut dosyası çalıştırmanız gerekir; böylece, hangi kurtarma noktasının MABS yedeklemesini içerdiğini bilirsiniz. Bu işlemi Azure Backup ile ilk çoğaltmadan sonra yapın. Komut dosyasında, öğesini `dplsqlservername%` MABS veritabanını barındıran SQL Server örneğinin adıyla değiştirin.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Mabs konsolunda **koruma**  >  **koruma grubu oluştur**' u seçin.

2. **Koruma grubu türünü seçin** sayfasında **sunucular**' ı seçin.

3. **Grup üyelerini seçin** sayfasında, mabs veritabanını seçin. MABS sunucu öğesini genişletin ve **DPMDB**' yi seçin.

4. **Veri koruma yöntemini seçin** sayfasında **disk kullanarak kısa vadeli koruma istiyorum**' u seçin. Kısa vadeli koruma ilkesi seçeneklerini belirtin. MABS veritabanları için iki haftalık bir bekletme aralığı önerilir.

#### <a name="recover-the-database"></a>Veritabanını kurtarma

MABS sunucusu çalışmaya devam ediyorsa ve depolama havuzu bozulmadan (örn. MABS hizmeti veya konsolu ile ilgili sorunlar gibi), veritabanını çoğaltma biriminden veya gölge kopyadan aşağıdaki şekilde kopyalayın:

1. Veritabanını kurtarmak istediğiniz yere karar verin.

    - Veritabanını doğrudan MABS çoğaltma biriminden alınan son yedeklemeden kopyalamak istiyorsanız, SQL betiği çıktısından GUID kullanarak çoğaltma birimine bir sürücü harfi atamak için **mountvol.exe** kullanın. Örnek: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Veritabanını önceki bir kurtarma noktasından (gölge kopya) kopyalamak istiyorsanız, SQL betiği çıktısından birim GUID 'sini kullanarak çoğaltma için tüm gölge kopyaları listeetmeniz gerekir. Bu komut, bu birim için gölge kopyaları listeler: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Kurtarmak istediğiniz oluşturma saatini ve gölge kopya KIMLIĞINI aklınızda yapın.

2. Daha sonra, gölge kopyayı bir X: kullanılmayan sürücü harfine bağlamak için **diskshadow.exe** kullanın. bu nedenle, veritabanı dosyalarını kopyalayabilirsiniz.

3. Bir yönetim komut istemi açın ve `psexec.exe -s cmd.exe` sistem bağlamında bir komut istemi başlatmak üzere çalıştırın, bu nedenle çoğaltma birimine (X:) gitme izninizin olması gerekir ve dosyalarını kopyalayın.

4. CD 'den X: sürücüsüne ve MABS veritabanı dosyalarının konumuna gidin. Bunları kolayca geri yükleyebileceğiniz bir konuma kopyalayın. Kopyalama tamamlandıktan sonra psexec cmd penceresini çalıştırın ve **diskshadow.exe** çalıştırıp X: birimini kullanıma al.

5. Artık SQL Management Studio kullanarak veya **DpmSync \- restoredb**'yi çalıştırarak veritabanı dosyalarını geri yükleyebilirsiniz.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Yerel SQL Server yedekleme ile yerel bir diske yedekleme

MABS veritabanını, MABS 'tan bağımsız olarak yerel SQL Server yedekleme ile yerel bir diske yedekleyebilirsiniz.

- SQL Server yedekleme ile ilgili [genel bilgi](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) edinin.

- SQL Server'ı buluta yedekleme hakkında[daha fazla bilgi edinin](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) .

## <a name="back-up-to-a-share-protected-by-mabs"></a>MABS tarafından korunan bir paylaşıma yedekleme

Bu yedekleme seçeneği, MABS veritabanını bir paylaşıma yedeklemek için yerel SQL kullanır, paylaşımın MABS ile korunmasını sağlar ve geri yüklemeyi kolaylaştırmak için Windows VSS önceki sürümlerini kullanır.

### <a name="before-you-start"></a>Başlamadan önce

1. SQL Server, bir yedeğin tek bir kopyasını tutmak için yeterli boş alana sahip bir sürücü üzerinde bir klasör oluşturun. Örneğin: `C:\MABSBACKUP`.

1. Klasörü paylaşın. Örneğin, `C:\MABSBACKUP` klasörünü *DPMBackup* olarak paylaşın.

1. Aşağıdaki OSQL komutunu kopyalayıp Not defteri 'ne yapıştırın ve adlı bir dosyaya kaydedin `C:\MABSACKUP\bkupdb.cmd` . . Txt uzantısının bulunmadığından emin olun. SQL_Instance_name ve DPMDB_NAME, MABS sunucunuz tarafından kullanılan örnek ve DPMDB adıyla eşleşecek şekilde değiştirin.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Not defteri 'ni kullanarak,  `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` mabs sunucusundaki klasörünün altında bulunanScriptingConfig.xmldosyasını açın.

1. **ScriptingConfig.xml** değiştirin ve **DATASOURCENAME =** değerini dpmdbbackup klasörünü/payını içeren sürücü harfi olacak şekilde değiştirin. PreBackupScript girişini, adım 3 ' te kaydedilen **BkUpDB. cmd** ' nin tam yolu ve adı olarak değiştirin.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Değişiklikleri **ScriptingConfig.xml** kaydedin.

1. C:\MABSBACKUP klasörünü veya `\sqlservername\MABSBACKUP` MABS kullanarak paylaşmayı koruyun ve ilk çoğaltmanın oluşturulmasını bekleyin. Daha sonra da MABS çoğaltmasına kopyalandığı yedekleme öncesi betiğin çalıştığı bir sonuç olarak C:\MABSBACKUP klasöründe bir **DPMDB. bak** olmalıdır.

1. Self Servis kurtarmayı etkinleştirmezseniz, çoğaltma üzerinde MABSBACKUP klasörünü paylaşmak için bazı ek adımlara ihtiyacınız olacaktır:

    1. MABS konsolundaki **koruma**>, MABSBACKUP veri kaynağını bulup seçin. Ayrıntılar bölümünde, çoğaltma yolunun bağlantısı üzerindeki **ayrıntıları görüntülemek Için tıklayın ' ı** seçin ve yolu not defteri 'ne kopyalayın. Kaynak yolunu kaldırın ve hedef yolunu tutun. Yol şuna benzer olmalıdır: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. **Mabsservername-DPMDB** adlı paylaşımın adını kullanarak bu yola bir paylaşma yapın. Bir yönetici komut isteminden aşağıdaki Net Share komutunu kullanabilirsiniz.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Yedeklemeyi yapılandırma

MABS veritabanını, SQL Server yerel yedekleme kullanarak diğer SQL Server veritabanı gibi yedekleyebilirsiniz.

- SQL Server yedekleme ile ilgili [genel bilgi](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) edinin.

- SQL Server'ı buluta yedekleme hakkında[daha fazla bilgi edinin](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) .

### <a name="recover-the-mabs-database"></a>MABS veritabanını kurtarma

1. `\\MABSServer\MABSSERVERNAME-dpmdb`Herhangi bir Windows bilgisayardan Explorer 'ı kullanarak paylaşıma bağlanın.

2. Özellikleri görüntülemek için **DPMDB. bak** dosyasını sağ tıklayın. **Önceki sürümler** sekmesinde, seçebileceğiniz ve kopyalayabileceğiniz tüm yedeklemeler bulunur. Ayrıca, daha da kolay erişilebilen C:\MABSBACKUP klasöründe hala bulunan son yedekleme de vardır.

3. Çoğaltma biriminden okuyabilmeniz için veya yerel olarak eklenmiş diskleri okumak üzere Windows 'u yeniden yüklemek için SAN iliştirilmiş bir MABS depolama havuzu diskini başka bir sunucuya taşımanız gerekiyorsa, veritabanı yedeklemesini hangi birimde bulundurduğunu bilmeniz için MABS çoğaltma birimi bağlama noktası yolunu veya birim GUID 'sini önceden bilmeniz gerekir. İlk korumadan sonra, geri yükleme gereksiniminden önce bu bilgileri ayıklamak için aşağıdaki SQL betiğini kullanabilirsiniz. Öğesini `%dpmsqlservername%` veritabanını barındıran SQL Server adıyla değiştirin.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. MABS depolama havuzu disklerini veya bir MABS sunucusunu taşıdıktan sonra kurtarmanız gerekirse:

    1. Birim GUID 'sine sahipsiniz, bu nedenle birimin başka bir Windows sunucusuna bağlanması gerekir veya bir MABS sunucusu yeniden oluşturulduktan sonra, SQL betiği çıktısından birim GUID 'sini kullanarak bir sürücü harfi atamak için **mountvol.exe** kullanın: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Klasör yapısını temsil eden çoğaltma yolunun sürücü harfini ve bölümünü kullanarak çoğaltma birimindeki MABSBACKUP klasörünü yeniden paylaşabilirsiniz.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. `\\SERVERNAME\MABSSERVERNAME-dpmdb`Herhangi bir Windows bilgisayardan Explorer 'ı kullanarak paylaşıma bağlanın.

    4. Özellikleri görüntülemek için **DPMDB. bak** dosyasını sağ tıklayın. **Önceki sürümler** sekmesinde, seçebileceğiniz ve kopyalayabileceğiniz tüm yedeklemeler bulunur.

## <a name="using-dpmsync"></a>DPMSync kullanma

**DpmSync** , mabs veritabanını depolama havuzundaki disklerin durumuyla ve yüklü koruma aracılarıyla eşitlemenize olanak sağlayan bir komut satırı aracıdır. DpmSync, MABS veritabanını geri yükler, MABS veritabanını depolama havuzundaki yinelemelerle eşitler, rapor veritabanını geri yükler ve eksik çoğaltmaları yeniden konumlandırır.

### <a name="parameters"></a>Parametreler

| Parametre      | Açıklama    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Bir MABS veritabanını belirtilen konumdan geri yükler.|
| **-Eşitleme**                            | Geri yüklenen veritabanlarını eşitler. Veritabanlarını geri yükledikten sonra **DpmSync – sync** ' i çalıştırmanız gerekir. **DpmSync – sync**' i çalıştırdıktan sonra bazı çoğaltmalar hala eksik olarak işaretlenmiş olabilir. |
| **-Dbloc** *konumu*                | MABS veritabanının yedeğinin konumunu tanımlar.|
| **-InstanceName** <br/>*Server\Instance*     | DPMDB 'nin geri yüklenmesi gereken örnek.|
| **-ReallocateReplica**         | Tüm eksik çoğaltma birimlerini eşitleme olmadan yeniden konumlandırır. |
| **-Datakopyalanmış**                      | Verileri yeni ayrılmış çoğaltma birimlerine yüklemeyi tamamladığınızı gösterir. Bu yalnızca istemci bilgisayarlar için geçerlidir. |

**Örnek 1:** Mabs veritabanını MABS sunucusundaki yerel yedekleme medyasından geri yüklemek için şu komutu çalıştırın:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

MABS veritabanını geri yükledikten sonra veritabanlarını eşitlemeye sonra aşağıdaki komutu çalıştırın:

```cmd
DpmSync -Sync
```

MABS veritabanını geri yükleyip eşitledikten ve çoğaltmayı geri yüklemeden önce, çoğaltma için disk alanını yeniden ayırmak üzere aşağıdaki komutu çalıştırın:

```cmd
DpmSync -ReallocateReplica
```

**Örnek 2:** MABS veritabanını uzak bir veritabanından geri yüklemek için, uzak bilgisayarda aşağıdaki komutu çalıştırın:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

MABS veritabanını geri yükledikten sonra veritabanlarını eşitlemeye sonra, MABS sunucusunda aşağıdaki komutu çalıştırın:

```cmd
DpmSync -Sync
```

MABS veritabanını geri yükleyip eşitledikten ve çoğaltmayı geri yüklemeden önce, çoğaltma için disk alanını yeniden ayırmak üzere MABS sunucusunda aşağıdaki komutu çalıştırın:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Sonraki adımlar

- [MABS destek matrisi](backup-support-matrix-mabs-dpm.md)
- [MABS SSS](backup-azure-dpm-azure-server-faq.yml)