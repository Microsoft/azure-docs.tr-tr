---
title: Azure VM 'de Oracle veritabanı oluşturma | Microsoft Docs
description: Oracle Database 12c veritabanını hızlıca Azure ortamınızda çalışır duruma alın.
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
ms.openlocfilehash: 6d43fa2621aa95bdcf18d5c033d1347e13dc3f67
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101483"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Azure VM 'de Oracle Database oluşturma

Bu kılavuzda, bir Oracle 12c veritabanı oluşturmak için [Oracle marketi Galeri görüntüsünden](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) bir Azure sanal makinesi dağıtmak üzere Azure CLI kullanılarak ayrıntılar bulunur. Sunucu dağıtıldıktan sonra, Oracle veritabanını yapılandırmak için SSH aracılığıyla bağlanırsınız. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı seçerseniz bu hızlı başlangıç için Azure CLI 2.0.4 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Sanal makine oluşturma

Bir sanal makine (VM) oluşturmak için [az VM Create](/cli/azure/vm) komutunu kullanın. 

Aşağıdaki örnekte `myVM` adlı bir VM oluşturulur. Varsayılan anahtar konumunda zaten mevcut değilse, SSH anahtarları da oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 'yi oluşturduktan sonra, Azure CLı aşağıdaki örneğe benzer bilgiler görüntüler. Değerini aklınızda yapın `publicIpAddress`. Bu adresi sanal makineye erişmek için kullanırsınız.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

VM ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin `publicIpAddress` değeri ile değiştirin.

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Veritabanını oluşturma

Oracle yazılımı Market görüntüsüne zaten yüklenmiş. Örnek bir veritabanını aşağıda gösterildiği gibi oluşturun. 

1.  *Oracle* superuser 'a geçip günlüğe kaydetme için dinleyiciyi başlatın:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Çıkış aşağıdakine benzer:

    ```bash
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

2.  Veritabanını oluşturun:

    ```bash
    dbca -silent \
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

    Veritabanının oluşturulması birkaç dakika sürer.

3. Oracle değişkenlerini ayarlama

Bağlanmadan önce iki ortam değişkeni ayarlamanız gerekir: *ORACLE_HOME* ve *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Ayrıca,. bashrc dosyasına ORACLE_HOME ve ORACLE_SID değişkenleri ekleyebilirsiniz. Bu, gelecekteki oturum açma işlemleri için ortam değişkenlerini kaydeder. Aşağıdaki deyimlerin, seçtiğiniz düzenleyiciyi kullanarak `~/.bashrc` dosyaya eklendiğinden emin olun.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express bağlantısı

Veritabanını araştırmak için kullanabileceğiniz bir GUI yönetim aracı için Oracle EM Express 'i ayarlayın. Oracle EM Express 'e bağlanmak için önce Oracle 'da bağlantı noktasını ayarlamanız gerekir. 

1. SQLplus kullanarak veritabanınıza bağlanın:

    ```bash
    sqlplus / as sysdba
    ```

2. Bağlandıktan sonra, EM Express için 5502 numaralı bağlantı noktasını ayarlayın

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Zaten açılmadıysa PDB1 kapsayıcısını açın, ancak önce durumu kontrol edin:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Çıkış aşağıdakine benzer:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. İçin `PDB1` OPEN_MODE okuma yazma değilse, PDB1 açmak için şu komutu çalıştırın:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

SQLplus oturumunu sonlandırın ve Oracle kullanıcısının oturumu kapatmak için `quit` yazmanız `exit` gerekir.

## <a name="automate-database-startup-and-shutdown"></a>Veritabanı başlangıcını ve kapatılmasını otomatikleştirin

Varsayılan olarak Oracle veritabanı, sanal makineyi yeniden başlattığınızda otomatik olarak başlatılmaz. Oracle veritabanını otomatik olarak başlayacak şekilde ayarlamak için ilk olarak kök olarak oturum açın. Ardından, bazı sistem dosyalarını oluşturun ve güncelleştirin.

1. Kök olarak oturum aç
    ```bash
    sudo su -
    ```

2.  En sevdiğiniz düzenleyiciyi kullanarak dosyayı `/etc/oratab` düzenleyin ve varsayılan `N` olarak `Y`değiştirin:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Adlı `/etc/init.d/dbora` bir dosya oluşturun ve aşağıdaki içeriği yapıştırın:

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  *Chmod* ile dosyalardaki izinleri aşağıdaki gibi değiştirin:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Başlatma ve kapanmaya yönelik sembolik bağlantıları aşağıdaki gibi oluşturun:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Değişikliklerinizi test etmek için VM 'yi yeniden başlatın:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Bağlantı noktalarını bağlantı için açma

Son görev, bazı dış uç noktaları yapılandırmaktır. VM 'yi koruyan Azure ağ güvenlik grubunu ayarlamak için, önce VM 'deki SSH oturumunuzla çıkış yapın (önceki adımda yeniden başlatıldığında SSH 'den çıkarılan olmalıdır). 

1.  Oracle veritabanına uzaktan erişmek için kullandığınız uç noktayı açmak için [az Network NSG Rule Create](/cli/azure/network/nsg/rule) komutuyla aşağıdaki gibi bir ağ güvenlik grubu kuralı oluşturun: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Oracle EM Express 'e uzaktan erişmek için kullandığınız uç noktayı açmak için [az Network NSG Rule Create](/cli/azure/network/nsg/rule) komutuyla aşağıdaki gibi bir ağ güvenlik grubu kuralı oluşturun:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Gerekirse, [az Network public-IP Show](/cli/azure/network/public-ip) komutuyla sanal MAKINENIZIN genel IP adresini aşağıda gösterildiği gibi edinin:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Tarayıcınızdan EM Express 'i bağlayın. Tarayıcınızın EM Express ile uyumlu olduğundan emin olun (Flash yüklemesi gereklidir): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

**Sys** hesabını kullanarak oturum açabilir ve **SYSDBA** onay kutusunu işaretleyebilirsiniz. Yükleme sırasında ayarladığınız Password **OraPasswd1** kullanın. 

![Oracle OEM Express oturum açma sayfasının ekran görüntüsü](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure 'da ilk Oracle veritabanınızı araştırmayı tamamladıktan sonra VM artık gerekli değilse, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure 'da diğer Oracle çözümleri](oracle-considerations.md)hakkında bilgi edinin. 

[Oracle otomatik depolama yönetimi öğreticisini yüklemeyi ve yapılandırmayı](configure-oracle-asm.md) deneyin.
