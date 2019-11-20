---
title: Azure Linux VM 'de Oracle altın kapısı uygulama | Microsoft Docs
description: Azure ortamınızda bir Oracle altın kapıdan yararlanın ve çalışır duruma hızla erişin.
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
ms.openlocfilehash: 31137bba8c9b6b88c6a8b9569c02ae887e73e8d0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309599"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Azure Linux VM 'de Oracle altın kapısı uygulama 

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu kılavuzda, Azure 'un Market Galerisi görüntüsünden bir Oracle 12c veritabanı dağıtmak için Azure CLı 'nin nasıl kullanılacağı açıklanır. 

Bu belgede, bir Azure VM üzerinde Oracle altın kapısı oluşturma, yüklemeyi ve yapılandırmayı adım adım gösterir. Bu öğreticide, iki sanal makine tek bir bölgedeki kullanılabilirlik kümesinde ayarlanır. Aynı öğretici, tek bir Azure bölgesindeki farklı Kullanılabilirlik Alanları veya iki farklı bölgede bulunan VM 'Ler için OracleGolden Gate 'i kurmak için kullanılabilir.

Başlamadan önce Azure CLI’nin yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Azure CLI yükleme kılavuzu](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Ortamı hazırlama

Oracle altın kapısı yüklemesini gerçekleştirmek için aynı Kullanılabilirlik kümesinde iki Azure VM oluşturmanız gerekir. VM 'Leri oluşturmak için kullandığınız Market görüntüsü **Oracle: Oracle-Database-Ee: 12.1.0.2: latest**.

Ayrıca, UNIX Düzenleyicisi VI hakkında bilgi sahibi olmanız ve X11 (X Windows) ile ilgili temel bilgiye sahip olmanız gerekir.

Aşağıda ortam yapılandırmasının bir özeti verilmiştir:
> 
> |  | **Birincil site** | **Siteyi Çoğalt** |
> | --- | --- | --- |
> | **Oracle sürümü** |Oracle 12c sürüm 2 – (12.1.0.2) |Oracle 12c sürüm 2 – (12.1.0.2)|
> | **Makine adı** |myVM1 |myVM2 |
> | **İşletim sistemi** |Oracle Linux 6. x |Oracle Linux 6. x |
> | **Oracle SID 'SI** |CDB1 |CDB1 |
> | **Çoğaltma şeması** |SINAMANIZ|SINAMANIZ |
> | **Altın Kapı sahibi/Çoğalt** |C##GGADMIN |REPUSER |
> | **Altın Kapı işlemi** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Azure'da oturum açma 

[Az Login](/cli/azure/reference-index) komutuyla Azure aboneliğinizde oturum açın. Ardından ekrandaki yönergeleri izleyin.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetilebilecekleri mantıksal bir kapsayıcıdır. 

Aşağıdaki örnek `westus` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluştur

Aşağıdaki adım isteğe bağlıdır, ancak önerilir. Daha fazla bilgi için bkz. [Azure kullanılabilirlik kümeleri Kılavuzu](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm) komutuyla bir sanal makine oluşturun. 

Aşağıdaki örnek, ve `myVM1` `myVM2`adlı iki sanal makine oluşturur. Varsayılan anahtar konumunda henüz yoksa SSH anahtarları oluşturun. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

#### <a name="create-myvm1-primary"></a>MyVM1 oluştur (birincil):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

VM oluşturulduktan sonra Azure CLı, aşağıdaki örneğe benzer bilgiler gösterir. (Öğesine göz `publicIpAddress`atın. Bu adres, sanal makineye erişmek için kullanılır.)

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>MyVM2 oluştur (Çoğalt):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Oluşturulduktan sonra, ve `publicIpAddress` ' i de bir yere göz atın.

### <a name="open-the-tcp-port-for-connectivity"></a>Bağlantı için TCP bağlantı noktasını açın

Bir sonraki adım, Oracle veritabanına uzaktan erişmenizi sağlayan dış uç noktaları yapılandırmaktır. Dış uç noktaları yapılandırmak için aşağıdaki komutları çalıştırın.

#### <a name="open-the-port-for-myvm1"></a>MyVM1 için bağlantı noktasını açın:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Sonuçlar aşağıdaki yanıta benzer görünmelidir:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>MyVM2 için bağlantı noktasını açın:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma

Sanal makine ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin `publicIpAddress` ile değiştirin.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Veritabanını myVM1 (birincil) üzerinde oluşturma

Oracle yazılımı Market görüntüsünde zaten yüklüdür, bu nedenle sonraki adım veritabanını yüklemektir. 

Yazılımı ' Oracle ' superuser olarak çalıştır:

```bash
sudo su - oracle
```

Veritabanını oluşturun:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Çıktılar aşağıdaki yanıta benzer görünmelidir:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

ORACLE_SID ve ORACLE_HOME değişkenlerini ayarlayın.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

İsteğe bağlı olarak, bu ayarların gelecekteki oturum açma işlemleri için kaydedilmesini sağlamak üzere. bashrc dosyasına ORACLE_HOME ve ORACLE_SID ekleyebilirsiniz:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle dinleyicisini Başlat
```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Veritabanını myVM2 üzerinde oluşturma (çoğaltma)

```bash
sudo su - oracle
```
Veritabanını oluşturun:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
ORACLE_SID ve ORACLE_HOME değişkenlerini ayarlayın.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

İsteğe bağlı olarak, bu ayarların gelecekteki oturum açma işlemleri için kaydedilmesi için, ORACLE_HOME ve ORACLE_SID. bashrc dosyasına ekleyebilirsiniz.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle dinleyicisini Başlat
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Altın kapısı yapılandırma 
Altın kapısı yapılandırmak için bu bölümdeki adımları uygulayın.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>MyVM1 (birincil) üzerinde arşiv günlüğü modunu etkinleştir

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
```
Günlüğe kaydetmeyi zorla ' yı etkinleştirin ve en az bir günlük dosyası bulunduğundan emin olun.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Altın Kapı yazılımını indirin
Oracle altın kapısı yazılımını indirmek ve hazırlamak için aşağıdaki adımları izleyin:

1. [Oracle altın kapısı indirme sayfasından](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html) **fbo_ggs_Linux_x64_shiphome. zip** dosyasını indirin. **Oracle Linux x86-64 Için Oracle GoldenGate 12. x. x. x**indirme başlığı altında, indirilecek bir. zip dosyaları kümesi olmalıdır.

2. . Zip dosyalarını istemci bilgisayarınıza indirdikten sonra, dosyaları sanal makinenize kopyalamak için güvenli kopyalama Protokolü 'Nü (SCP) kullanın:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. . Zip dosyalarını **/opt** klasörüne taşıyın. Sonra dosyaların sahibini aşağıdaki gibi değiştirin:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Dosyaları sıkıştırmayı açın (zaten yüklenmemişse Linux unzip yardımcı programını yükleme):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Değiştirme izni:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>İstemciyi ve VM 'yi X11 çalıştıracak şekilde hazırlama (yalnızca Windows istemcileri için)
Bu isteğe bağlı bir adımdır. Linux istemcisi kullanıyorsanız veya zaten X11 kurulumuna sahipseniz, bu adımı atlayabilirsiniz.

1. PuTTY ve Xsiteye Windows bilgisayarınıza indirin:

   * [PuTTY 'yi indirin](https://www.putty.org/)
   * [Xwebsite 'i indirin](https://xming.en.softonic.com/)

2. PuTTY ' i yükledikten sonra (örneğin, C:\Program Files\PuTTY), PuTTYgen. exe ' yi (PuTTY anahtar Oluşturucu) çalıştırın.

3. PuTTY anahtar Oluşturucu:

   - Bir anahtar oluşturmak için **Oluştur** düğmesini seçin.
   - Anahtarın içeriğini kopyalayın (**CTRL + C**).
   - **Özel anahtarı kaydet** düğmesini seçin.
   - Görüntülenen uyarıyı yoksayın ve sonra **Tamam**' ı seçin.

   ![PuTTY anahtar Oluşturucu sayfasının ekran görüntüsü](./media/oracle-golden-gate/puttykeygen.png)

4. SANAL makinenizde şu komutları çalıştırın:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. **Authorized_keys**adlı bir dosya oluşturun. Anahtarın içeriğini bu dosyaya yapıştırın ve dosyayı kaydedin.

   > [!NOTE]
   > Anahtarın dizeyi `ssh-rsa`içermesi gerekir. Ayrıca, anahtarın içeriği tek satırlık bir metin olmalıdır.
   >  

6. PuTTY’yi başlatın. **Kategori** bölmesinde **bağlantı** > SSHkimlik > **doğrulaması**' nı seçin. **Kimlik doğrulaması Için özel anahtar dosyası** kutusunda, daha önce oluşturduğunuz anahtara gidin.

   ![Özel anahtar ayarla sayfasının ekran görüntüsü](./media/oracle-golden-gate/setprivatekey.png)

7. **Kategori** bölmesinde **bağlantı** > SSHX11 > ' ı seçin. Ardından **X11 Iletmeyi etkinleştir** kutusunu seçin.

   ![Enable X11 sayfasının ekran görüntüsü](./media/oracle-golden-gate/enablex11.png)

8. **Kategori** bölmesinde **oturum**' a gidin. Ana bilgisayar bilgilerini girip **Aç**' ı seçin.

   ![Oturum sayfasının ekran görüntüsü](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Altın Kapı yazılımını yükler

Oracle altın kapısı yüklemek için aşağıdaki adımları izleyin:

1. Oracle olarak oturum açın. (Parola sorulmadan oturum açabiliyor olmanız gerekir.) Yüklemeye başlamadan önce, XTE 'ın çalıştığından emin olun.
 
   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```
2. ' Oracle Database 12c için Oracle GoldenGate ' seçeneğini belirleyin. Sonra devam etmek için **İleri** ' yi seçin.

   ![Yükleyici yükleme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Yazılım konumunu değiştirin. Ardından **Başlat Yöneticisi** kutusunu seçin ve veritabanı konumunu girin. Devam etmek için **İleri**’yi seçin.

   ![Yükleme Seçme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Envanter dizinini değiştirin ve ardından devam etmek için **İleri** ' yi seçin.

   ![Yükleme Seçme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_03.png)

5. **Özet** ekranında, devam etmek için **yüklemek** ' ı seçin.

   ![Yükleyici yükleme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Bir betiği ' root ' olarak çalıştırmanız istenebilir. Bu durumda, ayrı bir oturum açın, VM 'ye SSH yapın, kök için sudo ve sonra betiği çalıştırın. **Tamam** devam et ' i seçin.

   ![Yükleme Seçme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Yükleme tamamlandığında, işlemi gerçekleştirmek için **Kapat** ' ı seçin.

   ![Yükleme Seçme sayfasının ekran görüntüsü](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>MyVM1 'de hizmet ayarlama (birincil)

1. Tnsnames. ora dosyasını oluşturun veya güncelleştirin:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Altın Kapı sahibini ve Kullanıcı hesaplarını oluşturun.

   > [!NOTE]
   > Sahip hesabının C## prefix 'i olmalıdır.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Altın Kapı testi Kullanıcı hesabını oluşturma:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Ayıklama parametre dosyasını yapılandırın.

   Altın Kapı komut satırı arabirimini (ggscı) başlatın:

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```
5. EXTRACT parametre dosyasına aşağıdakini ekleyin (VI komutlarını kullanarak). ESC tuşu, ': WQ! ' tuşlarına basın dosyasını kaydedin. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```
6. Kayıt ayıklamayı kaydet--tümleşik Ayıkla:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```
7. Ayıklama denetim noktalarını ayarlama ve gerçek zamanlı ayıklamayı başlatma:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```
   Bu adımda, daha sonra farklı bir bölümde kullanılacak olan başlangıç SCN 'sini bulabilirsiniz:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>MyVM2 üzerinde hizmet ayarlama (çoğaltma)


1. Tnsnames. ora dosyasını oluşturun veya güncelleştirin:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Çoğaltma hesabı oluştur:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Altın kapıdan test Kullanıcı hesabı oluşturun:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. Değişiklikleri çoğaltmak için REPLICAT parametre dosyası: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```
   REPORA parametre dosyası içeriği:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Çoğaltma denetim noktası ayarlayın:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Çoğaltmayı ayarlama (myVM1 ve myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. MyVM2 üzerinde çoğaltmayı ayarlama (çoğaltma)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Dosyayı şu şekilde güncelleştirin:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Ardından Yönetici hizmetini yeniden başlatın:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. MyVM1 (birincil) üzerinde çoğaltmayı ayarlama

Başlangıç yükünü başlatın ve hata olup olmadığını denetleyin:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. MyVM2 üzerinde çoğaltmayı ayarlama (çoğaltma)

SCN numarasını, daha önce edindiğiniz numarayla değiştirin:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
Çoğaltma başlamış ve TEST tablolarına yeni kayıtlar ekleyerek test edebilirsiniz.


### <a name="view-job-status-and-troubleshooting"></a>İş durumunu ve sorun gidermeyi görüntüleme

#### <a name="view-reports"></a>Raporları görüntüle
MyVM1 hakkındaki raporları görüntülemek için aşağıdaki komutları çalıştırın:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
MyVM2 hakkındaki raporları görüntülemek için aşağıdaki komutları çalıştırın:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Durumu ve geçmişi görüntüleme
MyVM1 üzerinde durum ve geçmişi görüntülemek için aşağıdaki komutları çalıştırın:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

MyVM2 üzerinde durum ve geçmişi görüntülemek için aşağıdaki komutları çalıştırın:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Bu, Oracle Linux üzerinde altın kapı yükleme ve yapılandırmasını tamamlar.


## <a name="delete-the-virtual-machine"></a>Şu sanal makineyi silin:

Artık gerekli olmadığında, kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Yüksek oranda kullanılabilir sanal makine oluşturma öğreticisi](../../linux/create-cli-complete.md)

[VM dağıtımı CLI örneklerini keşfedin](../../linux/cli-samples.md)
