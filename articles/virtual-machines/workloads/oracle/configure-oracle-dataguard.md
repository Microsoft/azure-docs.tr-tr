---
title: Azure Linux sanal makinesinde Oracle Data Guard 'ı uygulama | Microsoft Docs
description: Oracle Data Guard 'ı hızlı bir şekilde Azure ortamınızda çalışır duruma alın.
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
ms.openlocfilehash: 52723ca53b9156dd8e8183d92d8d4a350750c936
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100102"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Azure Linux sanal makinesinde Oracle Data Guard 'ı uygulama 

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını yönetmek veya bu kaynakları oluşturmak için kullanılır. Bu makalede, Azure Marketi 'nin Azure Marketi görüntüsünden bir Oracle Database 12c veritabanı dağıtmak için Azure CLı 'nin nasıl kullanılacağı açıklanır. Bu makalede, bir Azure sanal makinesine (VM) Data Guard 'ı nasıl yükleyeceğiniz ve yapılandırabileceğiniz adım adım gösterilir.

Başlamadan önce, Azure CLı 'nin yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Azure CLI yükleme kılavuzu](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Ortamı hazırlama
### <a name="assumptions"></a>Varsayımlar

Oracle Data Guard 'ı yüklemek için aynı Kullanılabilirlik kümesinde iki Azure VM oluşturmanız gerekir:

- Birincil VM (myVM1) çalışan bir Oracle örneğine sahiptir.
- Bekleme sanal makinesi (myVM2) yalnızca Oracle yazılımının yüklü olduğu bir.

VM 'Leri oluşturmak için kullandığınız Market görüntüsü Oracle: Oracle-Database-Ee: 12.1.0.2: Latest.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma 

[Az Login](/cli/azure/reference-index) komutunu kullanarak Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutunu kullanarak bir kaynak grubu oluşturun. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnek `westus` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluştur

Bir kullanılabilirlik kümesi oluşturmak isteğe bağlıdır, ancak bunu yapmanızı öneririz. Daha fazla bilgi için bkz. [Azure kullanılabilirlik kümeleri yönergeleri](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[Az VM Create](/cli/azure/vm) komutunu kullanarak bir VM oluşturun. 

Aşağıdaki örnek, ve `myVM1` `myVM2`adlı iki sanal makine oluşturur. Varsayılan anahtar konumunda zaten mevcut değilse, SSH anahtarları da oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

MyVM1 oluştur (birincil):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

VM 'yi oluşturduktan sonra, Azure CLı aşağıdaki örneğe benzer bilgiler gösterir. Değerini aklınızda edin `publicIpAddress`. Bu adresi sanal makineye erişmek için kullanırsınız.

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

MyVM2 oluşturma (bekleme):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

MyVM2 `publicIpAddress` oluşturduktan sonra değerini aklınızda edin.

### <a name="open-the-tcp-port-for-connectivity"></a>Bağlantı için TCP bağlantı noktasını açın

Bu adım, Oracle veritabanına uzaktan erişime izin veren dış uç noktaları yapılandırır.

MyVM1 için bağlantı noktasını açın:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Sonuç aşağıdaki yanıta benzer görünmelidir:

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

MyVM2 için bağlantı noktasını açın:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma

Sanal makine ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin `publicIpAddress` değeri ile değiştirin.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Veritabanını myVM1 (birincil) üzerinde oluşturma

Oracle yazılımı Market görüntüsünde zaten yüklüdür, bu nedenle sonraki adım veritabanını yüklemektir. 

Oracle superuser 'a geçin:

```bash
$ sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

ORACLE_SID ve ORACLE_HOME değişkenlerini ayarlayın:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

İsteğe bağlı olarak, bu ayarların gelecekteki oturum açma işlemleri için kaydedilmesini sağlamak üzere/Home/Oracle/,bashrc dosyasına ORACLE_HOME ve ORACLE_SID ekleyebilirsiniz:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Data Guard 'ı yapılandırma

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
Günlüğe kaydetmeyi zorla ' yı etkinleştirin ve en az bir günlük dosyasının mevcut olduğundan emin olun:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Bekleme yineleme günlükleri oluştur:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Flashback 'ı etkinleştirin (Bu, kurtarmayı çok daha kolay hale getirir) ve\_bekleme\_dosya yönetimini otomatik olarak ayarlar. Bundan sonra SQL * Plus 'den çıkın.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>MyVM1 'de hizmet ayarlama (birincil)

$ORACLE _HOME \ Network \ admin klasöründe bulunan tnsnames. ora dosyasını düzenleyin veya oluşturun.

Aşağıdaki girdileri ekleyin:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

$ORACLE _HOME \ Network \ admin klasöründe bulunan Listener. ora dosyasını düzenleyin veya oluşturun.

Aşağıdaki girdileri ekleyin:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Data Guard Aracısı 'nı etkinleştir:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```
Dinleyiciyi başlatın:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>MyVM2 (bekleme) üzerinde hizmet ayarlama

SSH to myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Oracle olarak oturum açın:

```bash
$ sudo su - oracle
```

$ORACLE _HOME \ Network \ admin klasöründe bulunan tnsnames. ora dosyasını düzenleyin veya oluşturun.

Aşağıdaki girdileri ekleyin:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

$ORACLE _HOME \ Network \ admin klasöründe bulunan Listener. ora dosyasını düzenleyin veya oluşturun.

Aşağıdaki girdileri ekleyin:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Dinleyiciyi başlatın:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Veritabanını myVM2 'e geri yükleme (bekleme)

Aşağıdaki içeriklerle/tmp/initcdb1_stby.ora parametre dosyasını oluşturun:
```bash
*.db_name='cdb1'
```

Klasör oluştur:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Parola dosyası oluşturma:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
Veritabanını myVM2 üzerinde Başlat:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

RMAN aracını kullanarak veritabanını geri yükleyin:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

RMAN 'da aşağıdaki komutları çalıştırın:
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Komut tamamlandığında aşağıdakine benzer iletiler görmeniz gerekir. RMAN 'dan çıkın.
```bash
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17

RMAN> EXIT;
```

İsteğe bağlı olarak, bu ayarların gelecekteki oturum açma işlemleri için kaydedilmesini sağlamak üzere/Home/Oracle/,bashrc dosyasına ORACLE_HOME ve ORACLE_SID ekleyebilirsiniz:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Data Guard Aracısı 'nı etkinleştir:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>MyVM1 üzerinde Data Guard Broker 'ı yapılandırma (birincil)

Data Guard Manager 'ı başlatın ve SYS ve Password kullanarak oturum açın. (İşletim sistemi kimlik doğrulamasını kullanmayın.) Şunları yapın:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Yapılandırmayı gözden geçirin:
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Oracle Data Guard kurulumunu tamamladınız. Sonraki bölümde, bağlantının nasıl test yapılacağı ve üzerinde geçiş yapılacağı gösterilmektedir.

### <a name="connect-the-database-from-the-client-machine"></a>Veritabanını istemci makinesinden bağla

İstemci makinenizde tnsnames. ora dosyasını güncelleştirin veya oluşturun. Bu dosya genellikle $ORACLE _HOME \ Network \ admin ' dir.

MyVM1 ve myVM2 için IP adreslerini `publicIpAddress` değerlerinizle değiştirin:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

SQL * Plus 'ı başlatın:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-the-data-guard-configuration"></a>Data Guard yapılandırmasını test etme

### <a name="switch-over-the-database-on-myvm1-primary"></a>MyVM1 üzerinde veritabanı üzerinde geçiş (birincil)

Birincil bilgisayardan bekleme moduna geçmek için (cdb1):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Artık bekleyen veritabanına bağlanabilirsiniz.

SQL * Plus 'ı başlatın:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>MyVM2 (bekleme) üzerinde veritabanı üzerinde geçiş yapma

Üzerinde geçiş yapmak için, myVM2 üzerinde şunu çalıştırın:
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Artık, birincil veritabanına bağlanabilmelisiniz.

SQL * Plus 'ı başlatın:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Oracle Linux üzerinde Data Guard yükleme ve yapılandırmasını tamamladınız.


## <a name="delete-the-virtual-machine"></a>Şu sanal makineyi silin:

VM 'ye artık ihtiyacınız kalmadığında, kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Yüksek oranda kullanılabilir sanal makineler oluşturma](../../linux/create-cli-complete.md)

[VM dağıtımı Azure CLı örneklerini keşfet](../../linux/cli-samples.md)
