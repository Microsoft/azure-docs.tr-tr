---
title: Azure Backup kullanarak bir Azure Linux sanal makinesinde Oracle Database 19c veritabanını yedekleme ve kurtarma
description: Azure Backup hizmetini kullanarak Oracle Database 19c veritabanını nasıl yedekleyeceğinizi ve kurtaracağınızı öğrenin.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673900"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Azure Backup kullanarak bir Azure Linux sanal makinesinde Oracle Database 19c veritabanını yedekleme ve kurtarma

Bu makalede, veritabanı dosyalarını ve hızlı kurtarma alanını içeren VM disklerinin disk anlık görüntülerini almak için Azure Backup kullanımı gösterilmektedir. Azure Backup kullanarak, [Kurtarma Hizmetleri kasasında](../../../backup/backup-azure-recovery-services-vault-overview.md)depolanan yedeklemeler olarak uygun tam disk anlık görüntülerini alabilirsiniz.  Azure Backup, verileri geri yüklemek için ek düzeltmelerin gerekli olmamasını sağlayan uygulamayla tutarlı yedeklemeler de sağlar. Uygulamayla tutarlı verilerin geri yüklenmesi, geri yükleme süresini azaltarak hizmetlerinizin kısa süre içinde çalışır hale gelmesini sağlar.

> [!div class="checklist"]
>
> * Uygulamayla tutarlı yedekleme ile veritabanını yedekleme
> * Veritabanını kurtarma noktasından geri yükleme ve kurtarma
> * VM 'yi bir kurtarma noktasından geri yükleme

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Yedekleme ve kurtarma işlemini gerçekleştirmek için, önce yüklü bir Oracle Database 19c örneğine sahip bir Linux sanal makinesi oluşturmanız gerekir. Şu anda VM 'yi oluşturmak için kullanılan Market görüntüsü  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: latest**. Bu öğreticiyi gerçekleştirmek için Oracle veritabanı [oluşturma hızlı](./oracle-database-quick-create.md) başlangıcı ' nda Oracle veritabanı oluşturmak için bu adımları izleyin.


## <a name="prepare-the-environment"></a>Ortamı hazırlama

Ortamı hazırlamak için şu adımları izleyin:

1. VM’ye bağlanın.
1. Veritabanını hazırlayın.

### <a name="connect-to-the-vm"></a>VM’ye bağlanma

1. VM ile Secure Shell (SSH) oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini ve ana bilgisayar adı birleşimini, `<publicIpAddress>` sanal makinenizin değeriyle değiştirin.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. *Kök* kullanıcıya geç:

   ```bash
   sudo su -
   ```
    
1. Oracle kullanıcısını */etc/sudoers* dosyasına ekleyin:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Veritabanını hazırlama

Bu adım, *vmoracle19c* ADLı bir VM üzerinde çalışan bir Oracle örneğine (*Test*) sahip olduğunuzu varsayar.

1. Kullanıcıyı *Oracle* kullanıcısına geçir:
 
   ```bash
    sudo su - oracle
    ```
    
2. Bağlanmadan önce, ORACLE_SID ortam değişkenini ayarlamanız gerekir:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Ayrıca, `oracle` `.bashrc` aşağıdaki komutu kullanarak gelecekteki oturum açma işlemleri için ORACLE_SID değişkenini Users dosyasına eklemeniz gerekir:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Zaten çalışmıyorsa Oracle dinleyicisini başlatın:

    ```output
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

4.  Hızlı kurtarma alanı (FRA) konumu oluşturun:

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Veritabanına bağlan:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Zaten çalışmıyorsa veritabanını Başlat:

    ```bash
    SQL> startup
    ```

7.  Hızlı kurtarma alanı için veritabanı ortam değişkenlerini ayarlayın:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Çevrimiçi yedeklemeleri etkinleştirmek için veritabanının arşiv günlüğü modunda olduğundan emin olun.

    Önce günlük Arşivi durumunu kontrol edin:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    NOARCHIVELOG modundaysa aşağıdaki komutları çalıştırın:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Yedekleme ve geri yükleme işlemlerini test etmek için bir tablo oluşturun:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. RMAN 'ı VM diskinde bulunan hızlı kurtarma alanına yedeklemek için yapılandırın:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Yapılandırma değişikliği ayrıntılarını onaylayın:

    ```bash
    RMAN> show all;
    ```    

12.  Şimdi yedeklemeyi çalıştırın. Aşağıdaki komut, sıkıştırılmış biçimde bir yedek kümesi olarak arşiv günlük dosyaları da dahil olmak üzere tam bir veritabanı yedeklemesini alacak:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Azure Backup kullanma (Önizleme)

Azure Backup hizmeti, Microsoft Azure bulutundaki verilerinizi yedekleyip kurtarmaya yönelik basit, güvenli ve uygun maliyetli çözümler sunar. Azure Backup, özgün verilerin yanlışlıkla yok edilmesiyle karşı koruma sağlamak için bağımsız ve yalıtılmış yedeklemeler sağlar. Yedekler, yerleşik kurtarma noktası yönetim özelliklerine sahip Kurtarma Hizmetleri kasasında depolanır. Yapılandırma ve ölçeklenebilirlik basittir, yedeklemeler en iyi duruma getirilir ve gerektiğinde kolayca geri yükleyebilirsiniz.

Azure Backup hizmeti, Oracle, MySQL, Mongo DB ve PostGreSQL gibi çeşitli uygulamalar için Windows ve Linux VM 'Leri yedeklemeleri sırasında uygulama tutarlılığı elde etmek için bir [çerçeve](../../../backup/backup-azure-linux-app-consistent.md) sağlar. Bu, disklerin anlık görüntüsünü almadan önce bir ön komut dosyası (uygulamaları sessiz duruma getirmek için) ve anlık görüntü tamamlandıktan sonra, uygulamaları normal moda döndürmek için betik sonrası (uygulamaları çözmek için komutlar) çağırmasını içerir. Örnek ön betikler ve son betikler, GitHub 'da sağlandığında, Bu betiklerin oluşturulması ve bakımı sizin sorumluluğunuzdadır.

Artık Azure Backup, Azure Backup hizmetin seçili uygulamalar için paketlenmiş ön betikler ve komut dosyaları sağladığını sağlayan gelişmiş bir ön betik ve (**Şu anda önizleme aşamasında olan**) bir sonrası altyapı sağlar. Azure Backup kullanıcıların uygulamayı yalnızca adı olması gerekir ve Azure VM yedeklemesi ilgili ön Post komut dosyalarını otomatik olarak çağırır. Paketlenmiş betikler ve son betikler Azure Backup ekibi tarafından korunur ve böylece kullanıcılar Bu betiklerin destek, sahiplik ve geçerliliğini garanti edebilir. Şu anda, geliştirilmiş Framework için desteklenen uygulamalar *Oracle* ve *MySQL*' dir.

Bu bölümde, çalışan VM 'niz ve Oracle veritabanınızın uygulamayla tutarlı anlık görüntülerini almak için Azure Backup gelişmiş çatı kullanacaksınız. Veritabanı, Azure Backup VM disklerinin anlık görüntüsünü alırken işlemsel olarak tutarlı çevrimiçi yedeklemenin oluşmasına izin vererek yedekleme moduna alınacaktır. Anlık görüntü, depolama alanının tam bir kopyası olur ve yazma anlık görüntüsüne artımlı veya kopya değil, veritabanınızı geri yüklemek için etkili bir ortamdır. Uygulamayla tutarlı anlık görüntüler Azure Backup kullanmanın avantajı, veritabanınızın ne kadar büyük olduğuna ve geri yükleme işlemleri için bir anlık görüntünün, kurtarma hizmetleri kasasına aktarılmasını beklemek zorunda kalmadan çok daha hızlı bir şekilde kullanılabileceği avantajlardır.

Veritabanını yedeklemek için Azure Backup kullanmak için şu adımları izleyin:

1. Ortamı uygulamayla tutarlı bir yedekleme için hazırlayın.
1. Uygulamayla tutarlı yedeklemeler ayarlayın.
1. VM 'nin uygulamayla tutarlı bir yedeklemesini tetikleyin.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Uygulamayı uygulamayla tutarlı bir yedekleme için hazırlama

1. *Kök* kullanıcıya geç:

   ```bash
   sudo su -
   ```

1. Yeni yedek kullanıcı oluştur:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Yedekleme Kullanıcı ortamını ayarlama:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Yeni yedekleme kullanıcısı için dış kimlik doğrulamasını ayarlayın. Yedekleme kullanıcısının, dış kimlik doğrulaması kullanarak veritabanına erişebilmesi gerekir, bu nedenle bir parola ile bu şekilde bir parola ile karşı

   İlk olarak, *Oracle* kullanıcısına geri dönün:

   ```bash
   su - oracle
   ```

   SQLplus kullanarak veritabanında oturum açın ve dış kimlik doğrulama için varsayılan ayarları denetleyin:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Çıktı aşağıdaki örnekteki gibi görünmelidir: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Şimdi, kimliği doğrulanmış bir veritabanı kullanıcısı oluşturun ve *sysbackup ayrıcalığı* verin:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > İfadesini çalıştırdığınızda hata alırsanız `ORA-46953: The password file is not in the 12.2 format.` `GRANT` , ORAPWD dosyasını 12,2 biçimine geçirmek için şu adımları izleyin:
   >
   > 1. SQLplus 'den çık.
   > 1. Parola dosyasını eski biçime yeni bir adla taşıyın.
   > 1. Parola dosyasını geçirin.
   > 1. Eski dosyayı kaldırın.
   > 1. Şu komutu çalıştırın:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. `GRANT`İşlemi SQLplus içinde yeniden çalıştırın.
   >
   
4. Yedekleme iletilerini veritabanı uyarı günlüğüne kaydetmek için bir saklı yordam oluşturun:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Uygulamayla tutarlı yedeklemeler ayarlama  

1. *Kök* kullanıcıya geç:

   ```bash
   sudo su -
   ```

2. "Etc/Azure" klasörünü denetleyin. Bu yoksa, uygulamayla tutarlı yedekleme çalışma dizinini oluşturun:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Klasörü içinde "iş yükü. conf" olup olmadığını denetleyin. Bu yoksa, *makinelerdeki/etc/Azure* dizininde *iş yükü. conf* adlı, ile başlaması gereken aşağıdaki içerikleri içeren bir dosya oluşturun `[workload]` . Dosya zaten mevcutsa, alanları aşağıdaki içerikle eşleşecek şekilde düzenlemeniz yeterlidir. Aksi takdirde, aşağıdaki komut dosyayı oluşturur ve içeriği doldurur:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>VM 'nin uygulamayla tutarlı bir yedeklemesini tetikleyin

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Azure portal, **RG-Oracle** kaynak grubunuza gidin ve sanal makinenize **vmoracle19c** tıklayın.

2.  **Yedekleme** dikey penceresinde, kaynak grubunda **RG-Oracle** adlı yeni bir **Kurtarma Hizmetleri Kasası** **oluşturun.**
    **Yedekleme Ilkesi Seç** için **(yeni) dailypolicy** kullanın. Yedekleme sıklığını veya bekletme aralığını değiştirmek istiyorsanız, bunun yerine **Yeni Ilke oluştur** ' u seçin.

    ![Kurtarma Hizmetleri kasaları ekleme sayfası](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Devam etmek için **yedeklemeyi etkinleştir**' e tıklayın.

    > [!IMPORTANT]
    > **Yedeklemeyi etkinleştir**' e tıkladıktan sonra, yedekleme işlemi zamanlanan sürenin süresi dolana kadar başlatılmaz. Anında yedekleme ayarlamak için bir sonraki adımı izleyin.

4. Kaynak grubu sayfasında, yeni oluşturduğunuz kurtarma hizmetleri Kasası **adlı** kasaya tıklayın. İpucu: görmek için sayfayı yenilemeniz gerekebilir.

5.  **Mykasa-yedekleme öğeleri** dikey penceresinde, **yedekleme öğesi sayısı** altında, yedekleme öğesi sayısını seçin.

    ![Kurtarma Hizmetleri kasaları Mykasa ayrıntısı sayfası](./media/oracle-backup-recovery/recovery-service-02.png)

6.  **Yedekleme öğeleri (Azure sanal makine)** dikey penceresinde, sayfanın sağ tarafındaki üç nokta (**...**) düğmesine ve ardından **Şimdi Yedekle**' ye tıklayın.

    ![Kurtarma Hizmetleri kasaları Şimdi Yedekle komutu](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Varsayılan yedeklemeyi sakla değerini kabul edin ve **Tamam** düğmesine tıklayın. Yedekleme işleminin bitmesini bekleyin. 

    Yedekleme işinin durumunu görüntülemek için **yedekleme işleri**' ne tıklayın.

    ![Kurtarma Hizmetleri kasaları iş sayfası](./media/oracle-backup-recovery/recovery-service-04.png)

    Yedekleme işinin durumu aşağıdaki görüntüde görünür:

    ![Kurtarma Hizmetleri Kasası iş sayfası durumu](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Ancak, anlık görüntüyü yürütmek için yalnızca saniyeler sürer. Bu, kasanın kasaya aktarılması biraz zaman alabilir ve aktarım tamamlanana kadar yedekleme işi tamamlanmaz.

8. Uygulamayla tutarlı bir yedekleme için günlük dosyasındaki hataları çözün. Günlük dosyası/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log. adresinde bulunur

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Kurtarma Hizmetleri Kasası oluşturma:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. VM için yedekleme korumasını etkinleştirin:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Yedeklemenin varsayılan zamanlamaya göre tetiklenmesi için beklemek yerine bir yedeklemeyi şimdi çalışacak şekilde tetikleyin (5 saat UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Ve kullanarak yedekleme işinin ilerlemesini izleyebilirsiniz `az backup job list` `az backup job show` .

---

## <a name="recovery"></a>Kurtarma

Veritabanını kurtarmak için şu adımları izleyin:

1. Veritabanı dosyalarını kaldırın.
1. Kurtarma Hizmetleri kasasından geri yükleme betiği oluşturun.
1. Geri yükleme noktasını bağlayın.
1. Kurtarma gerçekleştirin.

### <a name="remove-the-database-files"></a>Veritabanı dosyalarını kaldır 

Bu makalenin ilerleyen kısımlarında, kurtarma sürecini test etme hakkında bilgi edineceksiniz. Kurtarma işlemini test etmeden önce, veritabanı dosyalarını kaldırmanız gerekir.

1.  Oracle örneğini kapat:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Veri dosyalarını ve yedeklemeleri kaldırın:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Kurtarma Hizmetleri kasasından geri yükleme betiği oluşturma

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal, *Mykasa* kurtarma hizmetleri kasaları öğesini arayın ve seçin.

    ![Kurtarma Hizmetleri kasaları Mykasa yedekleme öğeleri](./media/oracle-backup-recovery/recovery-service-06.png)

2. **Genel bakış** dikey penceresinde, **yedekleme öğeleri** ve **Azure sanal makinesi** seçin ' i seçin. Bu, Anon sıfır yedekleme öğesi sayısı listelenmiş olmalıdır.

    ![Kurtarma Hizmetleri kasaları Azure sanal makine yedekleme öğesi sayısı](./media/oracle-backup-recovery/recovery-service-07.png)

3. Yedekler öğeleri (Azure sanal makineler) sayfasında, sanal makine **vmoracle19c** listelenir. Sağ taraftaki üç nokta simgesine tıklayarak menüyü açın ve **dosya kurtarma**' yı seçin.

    ![Kurtarma Hizmetleri Kasası dosya kurtarma sayfasının ekran görüntüsü](./media/oracle-backup-recovery/recovery-service-08.png)

4. **Dosya kurtarma (Önizleme)** bölmesinde **betiği indir**' e tıklayın. Ardından, download (. Kopyala) dosyasını istemci bilgisayardaki bir klasöre kaydedin. Betiği çalıştırmak için bir parola oluşturulur. Parolayı daha sonra kullanmak üzere bir dosyaya kopyalayın. 

    ![Betik dosyası kaydetme seçeneklerini indir](./media/oracle-backup-recovery/recovery-service-09.png)

5. . Kopyala dosyasını VM 'ye kopyalayın.

    Aşağıdaki örnek, dosyayı VM 'ye taşımak için güvenli kopya (SCP) komutunu nasıl kullanacağınızı gösterir. Ayrıca içeriği panoya kopyalayabilir ve ardından içeriği VM üzerinde ayarlanan yeni bir dosyaya yapıştırabilirsiniz.

    > [!IMPORTANT]
    > Aşağıdaki örnekte, IP adresi ve klasör değerlerini güncelleştirdiğinizden emin olun. Değerlerin, dosyanın kaydedildiği klasöre eşlenmesi gerekir.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

SANAL makinenizin kurtarma noktalarını listelemek için az Backup Recovery Point List kullanın. Bu örnekte, Myrecoveryserviceskasasında korunan myVM adlı sanal makine için en son kurtarma noktasını seçtik:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Kurtarma noktasını sanal makinenize bağlayan veya takan betiği almak için az backup restore files mount-rp komutunu kullanın. Aşağıdaki örnek, Myrecoveryserviceskasasında korunan myVM adlı sanal makine için betiği edinir.

myRecoveryPointName değerini, önceki komutta aldığınız kurtarma noktasının adıyla değiştirin:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Aşağıdaki örnekte olduğu gibi betik indirilir ve parola görüntülenir:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

. Kopyala dosyasını VM 'ye kopyalayın.

Aşağıdaki örnek, dosyayı VM 'ye taşımak için güvenli kopya (SCP) komutunu nasıl kullanacağınızı gösterir. Ayrıca içeriği panoya kopyalayabilir ve ardından içeriği VM üzerinde ayarlanan yeni bir dosyaya yapıştırabilirsiniz.

> [!IMPORTANT]
> Aşağıdaki örnekte, IP adresi ve klasör değerlerini güncelleştirdiğinizden emin olun. Değerlerin, dosyanın kaydedildiği klasöre eşlenmesi gerekir.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Geri yükleme noktasını bağlama

1. Bir geri yükleme bağlama noktası oluşturun ve betiği buna kopyalayın.

    Aşağıdaki örnekte, bir anlık görüntünün bağlanması için bir */restore* dizini oluşturun, dosyayı dizine taşıyın ve dosyayı kök kullanıcıya ait olacak şekilde değiştirin ve çalıştırılabilir hale gelir.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Şimdi yedeklemeyi geri yüklemek için betiği yürütün. Azure portal oluşturulan parolayı sağlamanız istenecektir. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Aşağıdaki örnek, önceki betiği çalıştırdıktan sonra neleri görmeniz gerektiğini gösterir. Devam etmek isteyip istemediğiniz sorulduğunda **Y** girin.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Bağlanan birimlere erişim onaylanır.

    Çıkmak için, **q** girin ve ardından bağlı birimleri arayın. Eklenen birimlerin bir listesini oluşturmak için, komut isteminde **df-h** girin.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Kurtarma gerçekleştirme

1. Eksik yedekleme dosyalarını hızlı kurtarma alanına geri kopyalayın:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Aşağıdaki komutlar, eksik veri dosyalarını geri yüklemek ve veritabanını kurtarmak için RMAN kullanır:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Veritabanı içeriğinin tamamen kurtarıldığını denetleyin:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Geri yükleme noktasını çıkarın.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Azure portal, **dosya kurtarma (Önizleme)** dikey penceresinde **diskleri** çıkar ' a tıklayın.

    ![Diskleri çıkar komutu](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Ayrıca, **-Clean** seçeneğiyle Python betiğini yeniden çalıştırarak kurtarma birimlerinin bağlantısını kaldırabilirsiniz.

## <a name="restore-the-entire-vm"></a>Tüm VM 'yi geri yükleme

Silinen dosyaları kurtarma hizmetleri kasalarından geri yüklemek yerine, sanal makinenin tamamını geri yükleyebilirsiniz.

Tüm VM 'yi geri yüklemek için şu adımları izleyin:

1. Vmoracle19c 'i durdurun ve silin.
1. VM 'yi kurtarın.
1. Genel IP adresini ayarlayın.
1. VM’ye bağlanın.
1. Aşamayı bağlamak ve kurtarma gerçekleştirmek için veritabanını başlatın.

### <a name="stop-and-delete-vmoracle19c"></a>Vmoracle19c 'i Durdur ve Sil

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal, **vmoracle19c** sanal makinesine gidin ve ardından **Durdur**' u seçin.

1. Sanal makine artık çalışır durumda değilse **Sil** ' i ve ardından **Evet**' i seçin.

   ![Kasa silme komutu](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM 'yi durdurun ve serbest bırakın:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. VM 'yi silin. Sorulduğunda ' y ' girin:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>VM 'yi kurtarma

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal hazırlama için bir depolama hesabı oluşturun.

   1. Azure portal **+ kaynak oluştur** ' u seçin ve **depolama hesabı**' nı arayıp seçin.
    
      ![Kaynağın nerede oluşturulacağını gösteren ekran görüntüsü.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Depolama hesabı oluştur sayfasında, mevcut kaynak grubunuzu **RG-Oracle**' ı seçin, depolama hesabınızı **oracrestore** olarak adlandırın ve hesap türü için **depolama v2 'yi (generalamaç v2)** seçin. Çoğaltmayı **yerel olarak yedekli depolama (LRS)** olarak değiştirin ve performansı **Standart** olarak ayarlayın. Konumun kaynak grubundaki diğer kaynaklarınızla aynı bölgeye ayarlandığından emin olun. 
    
      ![Depolama hesabı ekleme sayfası](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Gözden geçir + oluştur ' a ve ardından Oluştur ' a tıklayın.

2. Azure portal, *Mykasa* kurtarma hizmetleri kasaları öğesini arayın ve üzerine tıklayın.

    ![Kurtarma Hizmetleri kasaları Mykasa yedekleme öğeleri](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  **Genel bakış** dikey penceresinde, **yedekleme öğeleri** ve **Azure sanal makinesi** seçin ' i seçin. Bu, Anon sıfır yedekleme öğesi sayısı listelenmiş olmalıdır.

    ![Kurtarma Hizmetleri kasaları Azure sanal makine yedekleme öğesi sayısı](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Yedekler öğeleri (Azure sanal makineler) üzerinde, sanal makinenizin **vmoracle19c** bulunduğu sayfa listelenir. VM adına tıklayın.

    ![Kurtarma VM 'si sayfası](./media/oracle-backup-recovery/recover-vm-02.png)

5.  **Vmoracle19c** dikey penceresinde, **tutarlı bir uygulama** türü olan bir geri yükleme noktası seçin ve menüyü açmak için sağdaki üç nokta (**...**) simgesine tıklayın.  Menüden **VM 'Yi geri yükle**' ye tıklayın.

    ![VM komutunu geri yükle](./media/oracle-backup-recovery/recover-vm-03.png)

6.  **Sanal makineyi geri yükle** dikey penceresinde **Yeni oluştur** ve **Yeni sanal makine oluştur**' u seçin. **Vmoracle19c** sanal makine adını girip VNET **vmoracle19cVNET**' i seçin, alt ağ, VNET seçiminize bağlı olarak sizin için otomatik olarak doldurulur. VM geri yükleme işlemi, aynı kaynak grubunda ve bölgede bir Azure depolama hesabı gerektirir. Daha önce kurulumunu yaptığınız **depolama hesabı ' nı seçebilirsiniz.**

    ![Yapılandırma değerlerini geri yükle](./media/oracle-backup-recovery/recover-vm-04.png)

7.  VM 'yi geri yüklemek için **geri yükle** düğmesine tıklayın.

8.  Geri yükleme işleminin durumunu görüntülemek için **işler**' e ve ardından **yedekleme işleri**' ne tıklayın.

    ![Yedekleme işleri durumu komutu](./media/oracle-backup-recovery/recover-vm-05.png)

    Geri yükleme işleminin durumunu göstermek için **devam eden** geri yükleme işlemine tıklayın:

    ![Geri yükleme işleminin durumu](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Depolama hesabınızı ve dosya paylaşımınızı ayarlamak için, Azure CLı 'de aşağıdaki komutları çalıştırın.

1. Depolama hesabını VM 'niz ile aynı kaynak grubunda ve konumda oluşturun:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Kullanılabilir kurtarma noktalarının listesini alın. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Kurtarma noktasını depolama hesabına geri yükleyin. `<myRecoveryPointName>`Önceki adımda oluşturulan listeden bir kurtarma noktasıyla değiştirin:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Geri yükleme işi ayrıntılarını alın. Aşağıdaki komut, şablon URI 'sini almak için gereken adı da dahil olmak üzere tetiklenen geri yüklenen iş için daha fazla ayrıntı alır. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Çıktı şuna benzer olacaktır `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. VM 'yi yeniden oluşturmak için kullanılacak URI 'nin ayrıntılarını alın. Önceki adımdan geri yükleme işi adını yerine koyun `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Çıkış şuna benzer:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Bu örnekte olduğu gibi, şablon blob URI 'sinin sonundaki şablon adı `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` ve listelenen blob kapsayıcısı adı `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` . 

   VM oluşturmak için hazırlık bölümünde değişkenleri atamak için aşağıdaki komutta bu değerleri kullanın. 30 dakikalık süreli depolama kapsayıcısı için bir SAS anahtarı oluşturulur.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Şimdi VM 'yi oluşturmak için şablonu dağıtın.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   VM için bir ad girmeniz istenir.

---

### <a name="set-the-public-ip-address"></a>Genel IP adresini ayarlama

VM geri yüklendikten sonra özgün IP adresini yeni VM 'ye yeniden atamalısınız.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Azure portal sanal makinenize **vmoracle19c** gidin. Buna vmoracle19c-NIC-XXXXXXXXXXXX gibi yeni bir genel IP ve NIC atandığını, ancak bir DNS adresi olduğunu fark edeceksiniz. Özgün VM ortak IP 'si silindiğinde ve NIC tutulur ve sonraki adımlar bunları yeni VM 'ye yeniden iliştirmeye çalışır.

    ![Genel IP adresleri listesi](./media/oracle-backup-recovery/create-ip-01.png)

2.  VM’yi durdurma

    ![IP adresi oluştur](./media/oracle-backup-recovery/create-ip-02.png)

3.  **Ağa** git

    ![IP adresini ilişkilendir](./media/oracle-backup-recovery/create-ip-03.png)

4.  **Ağ arabirimi Ekle**' ye tıklayın, özgün genel IP adresinin hala ILIŞKILENDIRILDIĞI orijinal NIC * * vmoracle19cVMNic seçin ve **Tamam** ' a tıklayın.

    ![Kaynak türünü ve NIC değerlerini seçin](./media/oracle-backup-recovery/create-ip-04.png)

5.  Artık, birincil arabirim olarak yapılandırıldığından VM geri yükleme işlemiyle oluşturulan NIC 'ı ayırmanız gerekir. **Ağ arabirimini ayır ' a** tıklayın ve **vmoracle19c-NIC-xxxxxxxxxxxx** gibi yeni NIC 'yi seçip **Tamam** ' a tıklayın.

    ![Ağ arabiriminin nereden ayrılın seçdiğinin gösterildiği ekran görüntüsü.](./media/oracle-backup-recovery/create-ip-05.png)
    
    Yeniden oluşturulmuş VM 'niz artık özgün IP adresi ve ağ güvenlik grubu kurallarıyla ilişkili olan özgün NIC 'e sahip olacaktır
    
    ![IP adresi değeri](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  **Genel bakışa** dönün ve **Başlat** 'a tıklayın 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM 'yi durdurun ve serbest bırakın:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Geçerli, geri yükleme oluşturulan VM NIC 'i listeleyin

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Çıktı, geri yükleme tarafından oluşturulan NIC adını şu şekilde listeleyen şuna benzer. `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Bu durumda, adı olması gereken orijinal NIC 'i ekleyin `<VMName>VMNic` `vmoracle19cVMNic` . Özgün genel IP adresi hala bu NIC 'ye iliştirilmiş ve NIC yeniden eklendiğinde sanal makineye geri yüklenecek. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Geri yükleme tarafından üretilen NIC 'yi ayır

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. VM 'yi başlatın:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>VM’ye bağlanma

SANAL makineye bağlanmak için aşağıdaki betiği kullanın:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Aşamayı bağlama ve kurtarma işlemi gerçekleştirmek için veritabanını başlatma

1. Otomatik başlatma, veritabanını VM önyüklemesi üzerinde başlatmayı denediği için örneğin çalıştığını fark edebilirsiniz. Ancak veritabanı kurtarma gerektirir ve muhtemelen yalnızca takma aşamasında olabilir, bu nedenle ilk olarak hazırlık kapatması çalıştırılır.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Veritabanı içeriğinin kurtarıldığı kontrol edin:

    ```bash
    SQL> select * from scott.scott_table;
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