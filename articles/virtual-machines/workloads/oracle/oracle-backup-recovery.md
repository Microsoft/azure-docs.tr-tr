---
title: Azure Linux sanal makinesinde Oracle Database 12c veritabanını yedekleme ve kurtarma | Microsoft Docs
description: Azure ortamınızda Oracle Database 12c veritabanını nasıl yedekleyeceğinizi ve kurtaracağınızı öğrenin.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c493f79a066f872be6b38d127622cc757ab3c1cc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100233"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Azure Linux sanal makinesinde Oracle Database 12c veritabanını yedekleme ve kurtarma

Azure CLı kullanarak bir komut isteminde Azure kaynaklarını oluşturabilir ve yönetebilir ya da betikleri kullanabilirsiniz. Bu makalede Azure CLı betiklerini kullanarak bir Azure Marketi Galeri görüntüsünden Oracle Database 12c veritabanı dağıtma.

Başlamadan önce, Azure CLı 'nin yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Azure CLI yükleme kılavuzu](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Ortamı hazırlama

### <a name="step-1-prerequisites"></a>1\. adım: Önkoşullar

*   Yedekleme ve kurtarma işlemini gerçekleştirmek için, önce yüklü bir Oracle Database 12 c örneğine sahip bir Linux sanal makinesi oluşturmanız gerekir. VM oluşturmak için kullandığınız Market görüntüsü *Oracle: Oracle-Database-Ee: 12.1.0.2: latest*olarak adlandırılır.

    Oracle veritabanı oluşturmayı öğrenmek için bkz. [Oracle create database hızlı](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)başlangıcı.


### <a name="step-2-connect-to-the-vm"></a>2\. adım: VM’ye bağlanma

*   VM ile Secure Shell (SSH) oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini ve ana bilgisayar adı birleşimini `publicIpAddress` , sanal makinenizin değeriyle değiştirin.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3\. adım: Veritabanını hazırlama

1.  Bu adım, *myvm*ADLı bir VM üzerinde çalışan bir Oracle örneğine (cdb1) sahip olduğunuzu varsayar.

    *Oracle* Süper Kullanıcı kökünü çalıştırın ve dinleyiciyi başlatın:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Seçim Veritabanının arşiv günlüğü modunda olduğundan emin olun:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  Seçim Yürütmeyi test etmek için bir tablo oluşturun:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Yedekleme dosyasının konumunu ve boyutunu doğrulayın veya değiştirin:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Veritabanını yedeklemek için Oracle kurtarma Yöneticisi 'ni (RMAN) kullanın:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4\. Adım: Linux VM 'Ler için uygulamayla tutarlı yedekleme

Uygulamayla tutarlı yedeklemeler Azure Backup yeni bir özelliktir. VM anlık görüntüsünden önce ve sonra yürütülecek betikler oluşturup seçebilirsiniz (anlık görüntü öncesi ve anlık görüntü sonrası).

1. JSON dosyasını indirin.

    VMSnapshotScriptPluginConfig. json ' dan https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig indirin. Dosya içerikleri aşağıdakine benzer şekilde görünür:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Sanal makinede makinelerdeki/etc/Azure klasörünü oluşturun:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. JSON dosyasını kopyalayın.

    VMSnapshotScriptPluginConfig. json dosyasını makinelerdeki/etc/Azure klasörüne kopyalayın.

4. JSON dosyasını düzenleyin.

    `PreScriptLocation` Ve`PostScriptlocation` parametrelerini dahil etmek için VMSnapshotScriptPluginConfig. json dosyasını düzenleyin. Örneğin:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Anlık görüntü öncesi ve anlık görüntü sonrası betik dosyalarını oluşturun.

    Aşağıda, "soğuk yedekleme" (kapalı ve yeniden başlatma ile çevrimdışı yedekleme) için anlık görüntü öncesi ve anlık görüntü sonrası betiklerine bir örnek verilmiştir:

    /Etc/Azure/pre_script.sh için:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    /Etc/Azure/post_script.sh için:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    "Sık erişimli yedekleme" (çevrimiçi yedekleme) için anlık görüntü öncesi ve anlık görüntü sonrası betiklerine bir örnek aşağıda verilmiştir:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /Etc/Azure/post_script.sh için:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /Etc/Azure/pre_script.SQL için dosya içeriğini gereksinimlerinize göre değiştirin:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /Etc/Azure/post_script.SQL için dosya içeriğini gereksinimlerinize göre değiştirin:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Dosya izinlerini değiştir:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Betikleri test edin.

    Betikleri test etmek için, önce kök olarak oturum açın. Ardından hata olmadığından emin olun:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Daha fazla bilgi için bkz. [Linux VM 'ler Için uygulamayla tutarlı yedekleme](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5\. Adım: VM 'yi yedeklemek için Azure kurtarma hizmetleri kasalarını kullanma

1.  Azure portal, **Kurtarma Hizmetleri kasalarını**arayın.

    ![Kurtarma Hizmetleri kasaları sayfası](./media/oracle-backup-recovery/recovery_service_01.png)

2.  **Kurtarma Hizmetleri kasaları** dikey penceresinde, yeni bir kasa eklemek için **Ekle**' ye tıklayın.

    ![Kurtarma Hizmetleri kasaları ekleme sayfası](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Devam etmek için, **Mykasa**' ya tıklayın.

    ![Kurtarma Hizmetleri kasaları ayrıntı sayfası](./media/oracle-backup-recovery/recovery_service_03.png)

4.  **Mykasa** dikey penceresinde **Yedekle**' ye tıklayın.

    ![Kurtarma Hizmetleri kasaları yedekleme sayfası](./media/oracle-backup-recovery/recovery_service_04.png)

5.  **Yedekleme hedefi** dikey penceresinde, varsayılan **Azure** ve **sanal makine**değerlerini kullanın. **Tamam**'ı tıklatın.

    ![Kurtarma Hizmetleri kasaları ayrıntı sayfası](./media/oracle-backup-recovery/recovery_service_05.png)

6.  **Yedekleme ilkesi**Için **DefaultPolicy**kullanın veya **Yeni ilke oluştur**' u seçin. **Tamam**'ı tıklatın.

    ![Kurtarma Hizmetleri kasaları yedekleme ilkesi ayrıntı sayfası](./media/oracle-backup-recovery/recovery_service_06.png)

7.  **Sanal makineler Seç** dikey penceresinde **myVM1** onay kutusunu işaretleyin ve ardından **Tamam**' a tıklayın. **Yedeklemeyi etkinleştir** düğmesine tıklayın.

    ![Kurtarma Hizmetleri kasaları öğeleri yedekleme ayrıntısı sayfasına](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > **Yedeklemeyi etkinleştir**' e tıkladıktan sonra, yedekleme işlemi zamanlanan sürenin süresi dolana kadar başlatılmaz. Anında yedekleme ayarlamak için bir sonraki adımı izleyin.

8.  **Mykasa-yedekleme öğeleri** dikey penceresinde, **yedekleme öğesi sayısı**altında, yedekleme öğesi sayısını seçin.

    ![Kurtarma Hizmetleri kasaları Mykasa ayrıntısı sayfası](./media/oracle-backup-recovery/recovery_service_08.png)

9.  **Yedekleme öğeleri (Azure sanal makine)** dikey penceresinde, sayfanın sağ tarafındaki üç nokta ( **...** ) düğmesine ve ardından **Şimdi Yedekle**' ye tıklayın.

    ![Kurtarma Hizmetleri kasaları Şimdi Yedekle komutu](./media/oracle-backup-recovery/recovery_service_09.png)

10. **Yedekle** düğmesine tıklayın. Yedekleme işleminin bitmesini bekleyin. Ardından, 6. [adıma gidin: Veritabanı dosyalarını](#step-6-remove-the-database-files)kaldırın.

    Yedekleme işinin durumunu görüntülemek için **işler**' e tıklayın.

    ![Kurtarma Hizmetleri kasaları iş sayfası](./media/oracle-backup-recovery/recovery_service_10.png)

    Yedekleme işinin durumu aşağıdaki görüntüde görünür:

    ![Kurtarma Hizmetleri Kasası iş sayfası durumu](./media/oracle-backup-recovery/recovery_service_11.png)

11. Uygulamayla tutarlı bir yedekleme için günlük dosyasındaki hataları çözün. Günlük dosyası/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0. adresinde bulunur

### <a name="step-6-remove-the-database-files"></a>6\. Adım: Veritabanı dosyalarını kaldır 
Bu makalenin ilerleyen kısımlarında, kurtarma sürecini test etme hakkında bilgi edineceksiniz. Kurtarma işlemini test etmeden önce, veritabanı dosyalarını kaldırmanız gerekir.

1.  Tablo alanını ve yedekleme dosyalarını kaldır:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Seçim Oracle örneğini kapat:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Silinen dosyaları kurtarma hizmetleri kasalarından geri yükleme
Silinen dosyaları geri yüklemek için aşağıdaki adımları izleyin:

1. Azure portal, *Mykasa* kurtarma hizmetleri kasaları öğesini arayın. **Genel bakış** dikey penceresinde, **yedekleme öğeleri**altında, öğe sayısını seçin.

    ![Kurtarma Hizmetleri kasaları Mykasa yedekleme öğeleri](./media/oracle-backup-recovery/recovery_service_12.png)

2. **Yedekleme Öğesı sayısı**altında öğe sayısını seçin.

    ![Kurtarma Hizmetleri kasaları Azure sanal makine yedekleme öğesi sayısı](./media/oracle-backup-recovery/recovery_service_13.png)

3. **Myvm1** dikey penceresinde **dosya kurtarma (Önizleme)** seçeneğine tıklayın.

    ![Kurtarma Hizmetleri Kasası dosya kurtarma sayfasının ekran görüntüsü](./media/oracle-backup-recovery/recovery_service_14.png)

4. **Dosya kurtarma (Önizleme)** bölmesinde **betiği indir**' e tıklayın. Ardından, download (. sh) dosyasını istemci bilgisayardaki bir klasöre kaydedin.

    ![Betik dosyası kaydetme seçeneklerini indir](./media/oracle-backup-recovery/recovery_service_15.png)

5. . Sh dosyasını VM 'ye kopyalayın.

    Aşağıdaki örnek, dosyayı VM 'ye taşımak için güvenli kopya (SCP) komutunu nasıl kullanacağınızı gösterir. Ayrıca içeriği panoya kopyalayabilir ve ardından içeriği VM üzerinde ayarlanan yeni bir dosyaya yapıştırabilirsiniz.

    > [!IMPORTANT]
    > Aşağıdaki örnekte, IP adresi ve klasör değerlerini güncelleştirdiğinizden emin olun. Değerlerin, dosyanın kaydedildiği klasöre eşlenmesi gerekir.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Dosyayı, köke ait olacak şekilde değiştirin.

    Aşağıdaki örnekte, dosyayı köke ait olacak şekilde değiştirin. Ardından, izinleri değiştirin.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    Aşağıdaki örnek, önceki betiği çalıştırdıktan sonra neleri görmeniz gerektiğini gösterir. Devam etmek isteyip istemediğiniz sorulduğunda **Y**girin.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Bağlanan birimlere erişim onaylanır.

    Çıkmak için, **q**girin ve ardından bağlı birimleri arayın. Eklenen birimlerin bir listesini oluşturmak için, komut isteminde **df-k**yazın.

    ![Df-k komutu](./media/oracle-backup-recovery/recovery_service_16.png)

8. Eksik dosyaları klasörlere geri kopyalamak için aşağıdaki betiği kullanın:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. Aşağıdaki betikte, veritabanını kurtarmak için RMAN kullanın:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Diski çıkarın.

    Azure portal, **dosya kurtarma (Önizleme)** dikey penceresinde **diskleri**çıkar ' a tıklayın.

    ![Diskleri çıkar komutu](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Tüm VM 'yi geri yükleme

Silinen dosyaları kurtarma hizmetleri kasalarından geri yüklemek yerine, sanal makinenin tamamını geri yükleyebilirsiniz.

### <a name="step-1-delete-myvm"></a>1\. adım: MyVM 'Yi Sil

*   Azure portal, **myVM1** kasasına gidin ve **Sil**' i seçin.

    ![Kasa silme komutu](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2\. adım: VM 'yi kurtarma

1.  **Kurtarma Hizmetleri kasaları**' na gidin ve ardından **mykasa**' yı seçin.

    ![Mykasa girişi](./media/oracle-backup-recovery/recover_vm_02.png)

2.  **Genel bakış** dikey penceresinde, **yedekleme öğeleri**altında, öğe sayısını seçin.

    ![Mykasa yedekleme öğeleri](./media/oracle-backup-recovery/recover_vm_03.png)

3.  **Yedekleme öğeleri (Azure sanal makine)** dikey penceresinde **myvm1**' yi seçin.

    ![Kurtarma VM 'si sayfası](./media/oracle-backup-recovery/recover_vm_04.png)

4.  **Myvm1** dikey penceresinde üç nokta ( **...** ) düğmesine ve ardından **VM 'yi geri yükle**' ye tıklayın.

    ![VM komutunu geri yükle](./media/oracle-backup-recovery/recover_vm_05.png)

5.  **Geri yükleme noktası seç** dikey penceresinde, geri yüklemek istediğiniz öğeyi seçin ve ardından **Tamam**' a tıklayın.

    ![Geri yükleme noktasını seçin](./media/oracle-backup-recovery/recover_vm_06.png)

    Uygulamayla tutarlı yedeklemeyi etkinleştirdiyseniz dikey mavi bir çubuk görüntülenir.

6.  **Yapılandırma geri yükleme** dikey penceresinde, sanal makine adını seçin, kaynak grubunu seçin ve ardından **Tamam**' a tıklayın.

    ![Yapılandırma değerlerini geri yükle](./media/oracle-backup-recovery/recover_vm_07.png)

7.  VM 'yi geri yüklemek için **geri yükle** düğmesine tıklayın.

8.  Geri yükleme işleminin durumunu görüntülemek için **işler**' e ve ardından **yedekleme işleri**' ne tıklayın.

    ![Yedekleme işleri durumu komutu](./media/oracle-backup-recovery/recover_vm_08.png)

    Aşağıdaki şekilde geri yükleme işleminin durumu gösterilmektedir:

    ![Geri yükleme işleminin durumu](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3\. adım: Genel IP adresini ayarlama
VM geri yüklendikten sonra genel IP adresini ayarlayın.

1.  Arama kutusuna **genel IP adresi**girin.

    ![Genel IP adresleri listesi](./media/oracle-backup-recovery/create_ip_00.png)

2.  **Genel IP adresleri** dikey penceresinde **Ekle**' ye tıklayın. **Genel IP adresi oluştur** dikey penceresinde, **ad**için genel IP adını seçin. **Kaynak grubu** olarak **Var olanı kullan**’ı seçin. Ardından, **Oluştur**'u tıklatın.

    ![IP adresi oluştur](./media/oracle-backup-recovery/create_ip_01.png)

3.  Genel IP adresini VM 'nin ağ arabirimiyle ilişkilendirmek için, **Myvmıp**öğesini arayın ve seçin. Ardından **ilişkilendir**' e tıklayın.

    ![IP adresini ilişkilendir](./media/oracle-backup-recovery/create_ip_02.png)

4.  **Kaynak türü**için **ağ arabirimi**' ni seçin. MyVM örneği tarafından kullanılan ağ arabirimini seçin ve ardından **Tamam**' a tıklayın.

    ![Kaynak türünü ve NIC değerlerini seçin](./media/oracle-backup-recovery/create_ip_03.png)

5.  Portaldan alınan myVM örneğini arayın ve açın. VM ile ilişkili IP adresi, myVM **genel bakış** dikey penceresinde görünür.

    ![IP adresi değeri](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4\. Adım: VM’ye bağlanma

*   SANAL makineye bağlanmak için aşağıdaki betiği kullanın:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5\. Adım: Veritabanına erişilebilir olup olmadığını test etme
*   Erişilebilirliği test etmek için aşağıdaki betiği kullanın:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Veritabanı **Başlangıç** komutu bir hata oluşturursa veritabanını kurtarmak için bkz [. 6. Adım: Veritabanını](#step-6-optional-use-rman-to-recover-the-database)kurtarmak için RMAN kullanın.

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>6\. Adım: Seçim Veritabanını kurtarmak için RMAN kullanma
*   Veritabanını kurtarmak için aşağıdaki betiği kullanın:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Azure Linux VM 'de Oracle Database 12c veritabanının yedeklenmesi ve kurtarılması artık tamamlanmıştır.

## <a name="delete-the-vm"></a>VM’yi silin

VM 'ye artık ihtiyacınız kalmadığında, kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Yüksek oranda kullanılabilir VM 'Ler oluşturma](../../linux/create-cli-complete.md)

[VM dağıtımı Azure CLı örneklerini keşfet](../../linux/cli-samples.md)



