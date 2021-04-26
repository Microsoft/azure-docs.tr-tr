---
title: RMAN ve Azure Storage ile Azure Linux sanal makinesinde Oracle Database 19c veritabanını yedekleme
description: Oracle Database 19c veritabanını Azure bulut depolama alanına nasıl yedekleyeceğinizi öğrenin.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670023"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Azure Storage kullanarak bir Azure Linux sanal makinesinde Oracle Database 19c veritabanını yedekleme ve kurtarma

Bu makalede, Azure depolama 'nın bir Azure VM üzerinde çalışan bir Oracle veritabanını yedeklemek ve geri yüklemek için bir medya olarak kullanılması gösterilmektedir. SMB protokolü kullanılarak VM 'ye bağlanan Azure dosya depolama ile Oracle RMAN kullanarak veritabanını yedekleyeceğiniz. Yedekleme medyası için Azure Storage 'ın kullanılması son derece maliyetlidir ve performans için geçerlidir. Ancak, çok büyük veritabanları için Azure Backup daha iyi bir çözüm sağlar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Yedekleme ve kurtarma işlemini gerçekleştirmek için, önce yüklü bir Oracle Database 19c örneğine sahip bir Linux sanal makinesi oluşturmanız gerekir. Şu anda VM 'yi oluşturmak için kullanılan Market görüntüsü  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: latest**. Bu öğreticiyi gerçekleştirmek için Oracle veritabanı [oluşturma hızlı](./oracle-database-quick-create.md) başlangıcı ' nda Oracle veritabanı oluşturmak için bu adımları izleyin.

## <a name="prepare-the-database-environment"></a>Veritabanı ortamını hazırlama

1. VM ile Secure Shell (SSH) oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini ve ana bilgisayar adı birleşimini, `publicIpAddress` sanal makinenizin değeriyle değiştirin.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. ***Kök*** kullanıcıya geç:
 
   ```bash
   sudo su -
   ```
    
3. Oracle kullanıcısını ***/etc/sudoers*** dosyasına ekleyin:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Bu adım, *vmoracle19c* ADLı bir VM üzerinde çalışan bir Oracle örneğine (test) sahip olduğunuzu varsayar.

   Kullanıcıyı *Oracle* kullanıcısına geçir:

   ```bash
   sudo su - oracle
   ```
    
5. Bağlanmadan önce, ORACLE_SID ortam değişkenini ayarlamanız gerekir:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Ayrıca, `oracle` `.bashrc` aşağıdaki komutu kullanarak gelecekteki oturum açma işlemleri için ORACLE_SID değişkenini Users dosyasına eklemeniz gerekir:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Zaten çalışmıyorsa Oracle dinleyicisini başlatın:
    
   ```bash
   $ lsnrctl start
   ```

    Çıkış aşağıdaki örneğe benzer görünmelidir:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Hızlı kurtarma alanı (FRA) konumu oluşturun:

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Veritabanına bağlan:

    ```bash
    sqlplus / as sysdba
    ```

9.  Zaten çalışmıyorsa veritabanını başlatın:

    ```bash
    SQL> startup
    ```

10. Hızlı kurtarma alanı için veritabanı ortam değişkenlerini ayarlayın:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Çevrimiçi yedeklemeleri etkinleştirmek için veritabanının arşiv günlüğü modunda olduğundan emin olun.
    Önce günlük Arşivi durumunu kontrol edin:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    NOARCHIVELOG modunda aşağıdaki komutları SQLplus içinde çalıştırın:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Yedekleme ve geri yükleme işlemlerini test etmek için bir tablo oluşturun:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Azure dosyalarına yedekleme

Azure dosyalarını yedeklemek için şu adımları izleyin:

1. Azure dosya depolamayı ayarlayın.
1. Azure depolama dosya paylaşımından sanal makinenize bağlayın.
1. Veritabanını yedekleyin.
1. Veritabanını geri yükleyin ve kurtarın.

### <a name="set-up-azure-file-storage"></a>Azure dosya depolamayı ayarlama

Bu adımda, Oracle veritabanını Oracle kurtarma Yöneticisi (RMAN) kullanarak Azure dosya depolama 'ya yedekleyeceğiniz. Azure dosya paylaşımları, bulutta yaşayan tamamen yönetilen dosya paylaşımlardır. Bunlar, sunucu Ileti bloğu (SMB) protokolü veya ağ dosya sistemi (NFS) protokolü kullanılarak erişilebilirler. Bu adım, sanal makinenize bağlamak için SMB protokolünü kullanan bir dosya paylaşımının oluşturulmasını içerir. NFS kullanarak bağlama hakkında daha fazla bilgi için bkz. [nfs 3,0 protokolünü kullanarak blob depolamayı bağlama](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Azure dosyalarını bağlama sırasında, `cache=none` dosya paylaşma verilerinin önbelleğe alınmasını devre dışı bırakmak için öğesini kullanacağız. Ayrıca, paylaşımda oluşturulan dosyaların da Oracle kullanıcısı tarafından sahip olduğundan emin olmak için, `uid=oracle` ve `gid=oinstall` seçeneklerini ayarlayın. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

İlk olarak, depolama hesabınızı ayarlayın.

1. Azure portal dosya depolamayı yapılandırma

    Azure portal, ***+ kaynak oluştur** _ ' u seçin ve _ *_depolama hesabı_* ' nı arayıp seçin*
    
    ![Kaynağın nerede oluşturulacağını ve depolama hesabını seçmesinin nerede olduğunu gösteren ekran görüntüsü.](./media/oracle-backup-recovery/storage-1.png)
    
2. Depolama hesabı oluştur sayfasında, var olan kaynak grubunuzu seçin ***RG-Oracle** _, depolama hesabınızı _*_oracbkup1_*_ olarak adlandırın ve hesap türü için _*_depolama v2 'yi (generalamaç v2)_*_ seçin. Çoğaltmayı _*_yerel olarak yedekli depolama (LRS)_*_ olarak değiştirin ve performansı _ *_Standart_* * olarak ayarlayın. Konumun kaynak grubundaki diğer kaynaklarınızla aynı bölgeye ayarlandığından emin olun. 
    
    ![Mevcut kaynak grubunuzun nerede seçdiğinin gösterildiği ekran görüntüsü.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. ***Gelişmiş** _ sekmesine tıklayın ve Azure dosyaları ' nın altında, _*_büyük dosya paylaşımlarını_*_ _ *_etkin_* * olarak ayarlayın. Gözden geçir + oluştur ' a ve ardından Oluştur ' a tıklayın.
    
    ![Büyük dosya paylaşımlarının etkin olarak ayarlandığını gösteren ekran görüntüsü.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Depolama hesabı oluşturulduğunda kaynağa gidin ve ***dosya paylaşımları*** ' nı seçin.
    
    ![Dosya paylaşımlarının nerede seçdiğinin gösterildiği ekran görüntüsü.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. ***+ Dosya paylaşma** _ ' e tıklayın ve _*_yeni dosya paylaşma_*_ dikey penceresinde dosya paylaşımınızı adlandırın _*_orabkup1_*_. _*_Kotayı_*_ _*_10240_*_ gib olarak ayarlayın ve işlemi katman olarak _*_optimize_*_ edin. Kota, dosya paylaşımının büyüyebileceği üst sınırı yansıtır. Standart depolamayı kullanırken, kaynaklar PAYG olur ve sağlanmamıştır. bu nedenle, 10 TiB olarak ayarlanması, kullandığınız kadar ücretlendirilmeyecektir. Yedekleme stratejiniz daha fazla depolama gerektiriyorsa, tüm yedeklemeleri barındıracak şekilde kotayı uygun bir düzeye ayarlamanız gerekir.   Yeni dosya paylaşma dikey penceresini tamamladığınızda _ *_Oluştur_* * öğesine tıklayın.
    
    ![Yeni bir dosya paylaşımının nereye ekleneceğini gösteren ekran görüntüsü.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Oluşturulduğunda, dosya paylaşma ayarları sayfasında ***orabkup1*** ' a tıklayın. 
    Bağlantı dikey penceresini açmak için ***Bağlan** _ sekmesine tıklayın ve ardından _ *_Linux_** sekmesine tıklayın. SMB protokolünü kullanarak dosya paylaşımının bağlanması için sunulan komutları kopyalayın. 
    
    ![Depolama hesabı ekleme sayfası](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Depolama hesabınızı ve dosya paylaşımınızı ayarlamak için Azure CLı 'de aşağıdaki komutları çalıştırın.

1. Depolama hesabını VM 'niz ile aynı kaynak grubunda ve konumda oluşturun:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Depolama hesabında, 10 TiB kotasına sahip dosya paylaşma oluşturun:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Dosya paylaşımından sanal makinenize bağlanırken gerekli olan depolama hesabı birincil anahtarını (KEY1) alın:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Azure depolama dosya paylaşımından sanal makinenize bağlama

1. Bağlama noktasını oluşturma:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Kimlik bilgilerini ayarlama:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   `<Your Storage Account Key1>`Aşağıdaki komutu çalıştırmadan önce, daha önce alınan depolama hesabı anahtarını değiştirin:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Kimlik bilgileri dosyasındaki izinleri değiştir:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. /Etc/fstab 'e bağlama ekleyin:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. SMB protokolünü kullanarak Azure dosyaları depolama alanını bağlamak için komutları çalıştırın:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Şuna benzer bir hata alırsanız:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   ardından, CIFS paketi Linux ana bilgisayarınıza yüklenmemiş olabilir. 
   
   CIS 'nin yüklü olup olmadığını denetlemek için aşağıdaki komutu çalıştırın:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Komut hiçbir çıkış döndürürse, aşağıdaki komutu kullanarak CIFS paketini de yüklersiniz:

   ```bash 
   sudo yum install cifs-utils
   ```

   Azure dosya depolamayı bağlamak için yukarıdaki Mount komutunu yeniden çalıştırın.

6. Aşağıdaki komutla dosya paylaşımının düzgün şekilde bağlandığından emin olun:

   ```bash
   df -h
   ```

   Çıktı şuna benzer görünmelidir:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Veritabanını yedekleme

Bu bölümde, veritabanı ve arşiv günlüklerinin tam yedeklemesini yapmak ve yedeklemeyi daha önce takılan Azure dosya paylaşımında yedekleme kümesi olarak yazmak için Oracle kurtarma Yöneticisi (RMAN) kullanacağız. 

1. Azure dosyaları bağlama noktasına yedeklemek için RMAN 'ı yapılandırın:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Azure Standart dosya paylaşımları maksimum 1 TiB dosya boyutuna sahip olduğundan, RMAN yedekleme parçalarının boyutunu 1 TiB olarak sınırlayacağız. (Premium dosya paylaşımlarının maksimum dosya boyutu sınırı olan 4 TiB olduğunu unutmayın. Daha fazla bilgi için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Yapılandırma değişikliği ayrıntılarını onaylayın:

    ```bash
    RMAN> show all;
    ```

4. Şimdi yedeklemeyi çalıştırın. Aşağıdaki komut, sıkıştırılmış biçimde bir yedek kümesi olarak arşiv günlük dosyaları da dahil olmak üzere tam bir veritabanı yedeklemesini alacak:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Azure dosya depolama 'da bulunan yedeklemeyle birlikte Oracle RMAN kullanarak veritabanını çevrimiçi olarak yedeklemiş oldunuz. Bu yöntem, diğer VM 'lerden erişilebilen Azure dosya depolama 'da yedeklemeleri depolarken RMAN özelliklerinin avantajlarından yararlanarak, veritabanını kopyalamanız gerekiyorsa yararlıdır.  
    
Veritabanı yedeklemesi için RMAN ve Azure dosya depolama kullanılırken birçok avantaj bulunur. yedekleme ve geri yükleme süresi veritabanının boyutuna bağlıdır, bu nedenle çok büyük veritabanları için bu işlemler için önemli ölçüde zaman alabilir.  Uygulama ile tutarlı Azure Backup VM yedeklemeleri kullanan alternatif bir yedekleme mekanizması, veritabanı boyutundan bağımsız olarak çok hızlı yedeklemelerin avantajlarından yararlanan yedeklemeler gerçekleştirmek için anlık görüntü teknolojisini kullanır. Kurtarma Hizmetleri kasası ile tümleştirme, Azure Bulut depolamada diğer VM 'lerden ve Azure bölgelerinden erişilebilen Oracle Database yedeklemelerinizin güvenli bir şekilde depolanmasını sağlar. 

### <a name="restore-and-recover-the-database"></a>Veritabanını geri yükleme ve kurtarma

1.  Oracle örneğini kapat:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Veri dosyalarını ve yedeklemeleri kaldırın:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Aşağıdaki komutlar, eksik veri dosyalarını geri yüklemek ve veritabanını kurtarmak için RMAN kullanır:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Veritabanı içeriğinin tamamen kurtarıldığını denetleyin:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Azure Linux VM 'de Oracle Database 19c veritabanının yedeklenmesi ve kurtarılması artık tamamlanmıştır.

## <a name="delete-the-vm"></a>VM’yi silin

VM 'ye artık ihtiyacınız kalmadığında, kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: yüksek oranda kullanılabilir VM 'Ler oluşturma](../../linux/create-cli-complete.md)

[VM dağıtımı Azure CLı örneklerini keşfet](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)