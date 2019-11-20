---
title: Azure Linux sanal makinesinde Oracle ASM 'yi ayarlama | Microsoft Docs
description: Oracle ASM 'yi hızlı bir şekilde Azure ortamınızda çalışır duruma alın.
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
ms.openlocfilehash: 91150251140379c15d4ab3711ded571c9ad2c024
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101646"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Bir Azure Linux sanal makinesinde Oracle ASM ayarlayın  

Azure sanal makineleri tam olarak yapılandırılabilir ve esnek bir bilgi işlem ortamı sağlar. Bu öğreticide, Oracle otomatik depolama yönetimi (ASM) yükleme ve yapılandırmasıyla birlikte temel Azure sanal makine dağıtımı ele alınmaktadır.  Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Oracle Database VM oluşturma ve bu makineye bağlanma
> * Oracle otomatik depolama yönetimi 'ni yükleyip yapılandırma
> * Oracle Grid altyapısını yükleyip yapılandırma
> * Oracle ASM yüklemesi başlatma
> * ASM tarafından yönetilen Oracle DB oluşturma


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.4 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Ortamı hazırlama

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu oluşturmak için [az group create](/cli/azure/group) komutunu kullanın. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bu örnekte, *eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>VM oluşturma

Oracle Database görüntüsünü temel alan bir sanal makine oluşturmak ve Oracle ASM kullanmak üzere yapılandırmak için [az VM Create](/cli/azure/vm) komutunu kullanın. 

Aşağıdaki örnek, her biri 50 GB 'lık dört bağlı veri diskine sahip bir Standard_DS2_v2 boyutu olan myVM adlı bir VM oluşturur. Varsayılan anahtar konumunda zaten mevcut değilse, SSH anahtarları da oluşturur.  Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

VM 'yi oluşturduktan sonra, Azure CLı aşağıdaki örneğe benzer bilgiler görüntüler. Değerini aklınızda yapın `publicIpAddress`. Bu adresi sanal makineye erişmek için kullanırsınız.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>VM’ye bağlanma

VM ile bir SSH oturumu oluşturmak ve ek ayarları yapılandırmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin `publicIpAddress` değeri ile değiştirin.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM 'yi yükler

Oracle ASM 'yi yüklemek için aşağıdaki adımları izleyin. 

Oracle ASM yükleme hakkında daha fazla bilgi için bkz. [Oracle Linux 6 Için Oracle ASMLib İndirmeleri](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. ASM yüklemesine devam etmek için kök olarak oturum açmanız gerekir:

   ```bash
   sudo su -
   ```
   
2. Oracle ASM bileşenlerini yüklemek için şu ek komutları çalıştırın:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Oracle ASM 'nin yüklü olduğunu doğrulayın:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Bu komutun çıktısı aşağıdaki bileşenleri listelemelidir:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM, doğru bir şekilde çalışması için belirli kullanıcılar ve roller gerektirir. Aşağıdaki komutlarda önkoşul Kullanıcı hesapları ve grupları oluşturulur: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Kullanıcıların ve grupların doğru şekilde oluşturulduğunu doğrulayın:

   ```bash
   id grid
   ```

    Bu komutun çıktısı aşağıdaki kullanıcıları ve grupları listelemelidir:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Kullanıcı *Kılavuzu* için bir klasör oluşturun ve sahibi değiştirin:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM 'yi ayarlama

Bu öğretici için varsayılan kullanıcı *kılavuza* ve varsayılan grup *asmadmin*' dir. *Oracle* kullanıcısının asmadmin grubunun bir parçası olduğundan emin olun. Oracle ASM yüklemenizi ayarlamak için aşağıdaki adımları izleyin:

1. Oracle ASM kitaplık sürücüsünü ayarlamak, varsayılan kullanıcı (kılavuz) ve varsayılan grup (asmadmin) tanımlamayı ve sürücüyü önyüklemede başlatılmak üzere yapılandırmayı (y Seç) ve önyüklemede diskler için tarama yapmayı içerir (y Seç). Aşağıdaki komuttan komut istemlerini yanıtlamanız gerekir:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Bu komutun çıktısı, yanıtlanacak istemlerle durdurulduğunda aşağıdakine benzer görünmelidir.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Disk yapılandırmasını görüntüleyin:
   ```bash
   cat /proc/partitions
   ```

   Bu komutun çıktısı, kullanılabilir disklerin aşağıdaki listesine benzer görünmelidir

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Aşağıdaki komutu çalıştırarak ve istemlerle yanıt vererek disk */dev/SDC* biçimlendirin:
   - Yeni bölüm için *n*
   - birincil bölüm için *p*
   - *1* ilk bölümü seçmek için
   - Varsayılan `enter` ilk silindir için bas
   - Varsayılan `enter` son silindir için bas
   - bölüm tablosuna değişiklikleri yazmak için *w* tuşuna basın  

   ```bash
   fdisk /dev/sdc
   ```
   
   Yukarıda verilen yanıtları kullanarak, Fdisk komutu çıktısı aşağıdaki gibi görünmelidir:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. , `/dev/sdd` `/dev/sde`Ve içinöncekifdiskkomutunutekrarlayın.`/dev/sdf`

5. Disk yapılandırmasını denetleyin:

   ```bash
   cat /proc/partitions
   ```

   Komutun çıktısı aşağıdaki gibi görünmelidir:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Oracle ASM hizmeti durumunu denetleyin ve Oracle ASM hizmetini başlatın:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Komutun çıktısı aşağıdaki gibi görünmelidir:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM diskleri oluşturma:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   Komutun çıktısı aşağıdaki gibi görünmelidir:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM disklerini Listele:

   ```bash
   service oracleasm listdisks
   ```   

   Komutun çıktısı aşağıdaki Oracle ASM disklerini listelemelidir:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Kök, Oracle ve kılavuz kullanıcıları için parolaları değiştirin. **Bu yeni parolaları** yükleme sırasında daha sonra kullanırken aklınızda olun.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Klasör iznini değiştirin:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid altyapısını indirme ve hazırlama

Oracle Grid altyapı yazılımını indirmek ve hazırlamak için aşağıdaki adımları izleyin:

1. Oracle [asm indirme sayfasından](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)Oracle Grid altyapısını indirin. 

   **Linux x86-64 için Oracle Database 12c sürüm 1 Grid altyapısını (12.1.0.2.0)** başlıklı indirme altında, iki. zip dosyasını indirin.

2. . Zip dosyalarını istemci bilgisayarınıza indirdikten sonra, dosyaları sanal makinenize kopyalamak için güvenli kopyalama Protokolü 'Nü (SCP) kullanabilirsiniz:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. . Zip dosyalarını/opt klasörüne taşımak için Azure 'da Oracle sanal makinenize SSH ile geri dönün. Ardından, dosyaların sahibini değiştirin:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Dosyaları sıkıştırmayı açın. (Henüz yüklenmemişse Linux unzip aracını yükleme.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Değiştirme izni:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Yapılandırılmış değiştirme alanını güncelleştir. Oracle Grid bileşenlerinde, Grid 'i yüklemek için en az 6,8 GB takas alanı gerekir. Azure 'daki Oracle Linux görüntüleri için varsayılan takas dosyası boyutu yalnızca 20 48MB 'tır. Güncelleştirilmiş ayarların etkili olabilmesi `ResourceDisk.SwapSizeMB` için `/etc/waagent.conf` dosyada artırmanız ve walınuxagent hizmetini yeniden başlatmanız gerekir. Salt okunurdur bir dosya olduğundan, yazma erişimini etkinleştirmek için dosya izinlerini değiştirmeniz gerekir.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Değerini arayıp 8192 olarak değiştirin. `ResourceDisk.SwapSizeMB` Ekleme moduna girmek için tuşuna `insert` basmanız gerekir, **8192** değerini yazın ve ardından komut moduna dönmek için ' ye `esc` basın. Değişiklikleri yazmak ve dosyadan çıkmak için yazın `:wq` ve ENTER tuşuna basın. `enter`
   
   > [!NOTE]
   > En iyi performansı elde etmek için her `WALinuxAgent` zaman yerel geçici diskte (geçici disk) oluşturulan değiştirme alanını yapılandırmak için her zaman kullanmanızı öneririz. Hakkında daha fazla bilgi için bkz. [Linux Azure sanal makinelerinde takas dosyası ekleme](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>X11 çalıştırmak için yerel istemcinizi ve sanal makineyi hazırlama
Oracle ASM 'nin yapılandırılması, yüklemeyi ve yapılandırmayı tamamlamaya yönelik bir grafik arabirimi gerektirir. Bu yüklemeyi kolaylaştırmak için X11 protokolünü kullanıyoruz. Zaten X11 özellikleri etkinleştirilmiş ve yapılandırılmış olan bir istemci sistemi (Mac veya Linux) kullanıyorsanız, bu yapılandırmayı atlayıp Windows makinelerine özel olarak ayarlayabilirsiniz. 

1. [PuTTY 'Yi indirin](https://www.putty.org/) ve Windows bilgisayarınıza [indirin](https://xming.en.softonic.com/) . Devam etmeden önce bu uygulamaların her ikisinin yüklemesini varsayılan değerlerle doldurmanız gerekir.

2. Putty 'yı yükledikten sonra bir komut istemi açın, Putty klasörünü (örneğin, C:\Program files\putty) değiştirin ve bir anahtar oluşturmak için çalıştırın `puttygen.exe` .

3. PuTTY anahtar Oluşturucu:
   
   1. `Generate` Düğmeyi seçerek bir anahtar oluşturun.
   2. Anahtarın içeriğini kopyalayın (CTRL + C).
   3. `Save private key` düğmesini seçin.
   4. Anahtarı bir parola ile güvenli hale getirme uyarısını yoksayın ve sonra öğesini seçin `OK`.

   ![PuTTY anahtar oluşturucusunun ekran görüntüsü](./media/oracle-asm/puttykeygen.png)

4. SANAL makinenizde şu komutları çalıştırın:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. `authorized_keys` adlı bir dosya oluşturun. Anahtarın içeriğini bu dosyaya yapıştırın ve dosyayı kaydedin.

   > [!NOTE]
   > Anahtarın dizeyi `ssh-rsa`içermesi gerekir. Ayrıca, anahtarın içeriği tek satırlık bir metin olmalıdır.
   >  

6. İstemci sisteminizde PuTTY ' ı başlatın. **Kategori** bölmesinde **bağlantı** > SSHkimlik > **doğrulaması**' na gidin. **Kimlik doğrulaması Için özel anahtar dosyası** kutusunda, daha önce oluşturduğunuz anahtara gidin.

   ![SSH kimlik doğrulaması seçeneklerinin ekran görüntüsü](./media/oracle-asm/setprivatekey.png)

7. **Kategori** bölmesinde **bağlantı** > SSHX11 > ' a gidin. **X11 Iletmeyi etkinleştir** onay kutusunu seçin.

   ![SSH X11 iletme seçeneklerinin ekran görüntüsü](./media/oracle-asm/enablex11.png)

8. **Kategori** bölmesinde **oturum**' a gidin. Konak adı iletişim kutusuna Oracle `<publicIPaddress>` asm VM 'nizi girin, yeni `Saved Session` bir ad girin ve ardından açık `Save`' a tıklayın.  Kaydedildikten sonra, Oracle asm `open` sanal makinenize bağlanmak için üzerine tıklayın.  İlk bağlandığınız zaman, uzak sistem kayıt defterinizde önbelleğe alınmamış uyarılardır. `yes` Eklemek için tıklayın ve devam edin.

   ![PuTTY oturum seçeneklerinin ekran görüntüsü](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid altyapısını yükler

Oracle Grid altyapısını yüklemek için aşağıdaki adımları izleyin:

1. **Kılavuz**olarak oturum açın. (Parola sorulmadan oturum açabiliyor olmanız gerekir.) 

   > [!NOTE]
   > Windows çalıştırıyorsanız, yüklemeye başlamadan önce, kullanmaya başlayadığınızdan emin olun.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c sürüm 1 yükleyicisi açılır. (Yükleyicinin başlatılması birkaç dakika sürebilir.)

2. **Yükleme seçeneğini seçin** sayfasında, **tek başına bir sunucu Için Oracle Grid altyapısını yükleme ve yapılandırma**' yı seçin.

   ![Yükleyicinin yükleme seçeneklerini seçme sayfasının ekran görüntüsü](./media/oracle-asm/install01.png)

3. **Ürün dillerini seçin** sayfasında, **İngilizce** veya istediğiniz dilin seçili olduğundan emin olun.  Tıklatın `next`.

4. **Asm disk grubu oluştur** sayfasında:
   - Disk grubu için bir ad girin.
   - **Artıklık**altında **dış**' i seçin.
   - **Ayırma birimi boyutu**altında **4**' ü seçin.
   - **Disk Ekle**' nin altında **Orclasmsp**' yi seçin.
   - Tıklatın `next`.

5. **Asm parolasını belirtin** sayfasında, **Bu hesaplar Için aynı parolaları kullan** seçeneğini belirleyin ve bir parola girin.

   ![Yükleyicinin ASM parolasını belirt sayfasının ekran görüntüsü](./media/oracle-asm/install04.png)

6. **Yönetim seçeneklerini belirtin** sayfasında em bulut denetimini yapılandırma seçeneğiniz vardır. Bu seçeneği atlıyoruz ve devam etmek `next` için tıklayın. 

7. **Ayrıcalıklı Işletim sistemi grupları** sayfasında, varsayılan ayarları kullanın. Devam `next` etmek için tıklayın.

8. **Yükleme konumunu belirtin** sayfasında, varsayılan ayarları kullanın. Devam `next` etmek için tıklayın.

9. **Envanter oluştur** sayfasında, envanter dizinini olarak `/u01/app/grid/oraInventory`değiştirin. Devam `next` etmek için tıklayın.

   ![Yükleyicinin envanter Oluştur sayfasının ekran görüntüsü](./media/oracle-asm/install08.png)

10. **Kök betiği yürütme yapılandırması** sayfasında, **otomatik olarak yapılandırma betiklerini çalıştır** onay kutusunu seçin. Sonra, **"kök" Kullanıcı kimlik bilgileri** seçeneğini belirleyin ve kök kullanıcı parolasını girin.

    ![Yükleyicinin kök betiği yürütme yapılandırma sayfasının ekran görüntüsü](./media/oracle-asm/install09.png)

11. **Önkoşul denetimleri gerçekleştir** sayfasında, geçerli kurulum hatalarla başarısız olur. Bu beklenen bir davranıştır. `Fix & Check Again` öğesini seçin.

12. **Düzeltme betiği** iletişim kutusunda, öğesine tıklayın `OK`.

13. **Özet** sayfasında, seçtiğiniz ayarları gözden geçirin ve ardından öğesine tıklayın `Install`.

    ![Yükleyicinin özet sayfasının ekran görüntüsü](./media/oracle-asm/install12.png)

14. Yapılandırma komut dosyalarının ayrıcalıklı kullanıcı olarak çalıştırılması gerektiğini bildiren bir uyarı iletişim kutusu görüntülenir. Devam `Yes` etmek için tıklayın.

15. **Son** sayfasında, yüklemeyi sona bırakmak `Close` için ' ı tıklatın.

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM yüklemenizi ayarlama

Oracle ASM yüklemenizi ayarlamak için aşağıdaki adımları izleyin:

1. X11 oturumunuzda hala **kılavuz**olarak oturum açtığınızdan emin olun. Terminale bir daha dönmek `enter` için isabet etmeniz gerekebilir. Ardından Oracle otomatik depolama yönetimi yapılandırma yardımcısını başlatın:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM yapılandırma Yardımcısı açılır.

2. **Yapılandırma ASM: Disk grupları** iletişim kutusu `Create` , düğmesine ve ardından öğesine tıklayın `Show Advanced Options`.

3. **Disk grubu oluştur** iletişim kutusunda:

   - Disk grubu adı **verilerini**girin.
   - **Üye disklerini seçin**altında **ORCL_DATA** ve **ORCL_DATA1**öğesini seçin.
   - **Ayırma birimi boyutu**altında **4**' ü seçin.
   - Disk `ok` grubunu oluşturmak için tıklayın.
   - Onay `ok` penceresini kapatmak için tıklayın.

   ![Disk grubu oluştur iletişim kutusunun ekran görüntüsü](./media/oracle-asm/asm02.png)

4. **Yapılandırma ASM: Disk grupları** iletişim kutusu `Create` , düğmesine ve ardından öğesine tıklayın `Show Advanced Options`.

5. **Disk grubu oluştur** iletişim kutusunda:

   - **FRA**disk grubu adını girin.
   - **Artıklık**altında **dış (yok)** seçeneğini belirleyin.
   - **Üye disklerini seçin**altında **ORCL_FRA**öğesini seçin.
   - **Ayırma birimi boyutu**altında **4**' ü seçin.
   - Disk `ok` grubunu oluşturmak için tıklayın.
   - Onay `ok` penceresini kapatmak için tıklayın.

   ![Disk grubu oluştur iletişim kutusunun ekran görüntüsü](./media/oracle-asm/asm04.png)

6. ASM yapılandırma yardımcısını kapatmak için **Çıkış** ' ı seçin.

   ![ASM yapılandırma ekran görüntüsü: Çıkış düğmesi ile disk grupları iletişim kutusu](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Veritabanını oluşturma

Oracle veritabanı yazılımı zaten Azure Market görüntüsüne yüklenmiş. Bir veritabanı oluşturmak için aşağıdaki adımları izleyin:

1. Kullanıcıları Oracle superuser 'a geçirin ve ardından günlüğe kaydetmek için dinleyiciyi başlatın:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Veritabanı yapılandırma Yardımcısı açılır.

2. **Veritabanı işlemi** sayfasında, öğesine tıklayın `Create Database`.

3. **Oluşturma modu** sayfasında:

   - Veritabanı için bir ad girin.
   - **Depolama türü**Için **Otomatik depolama YÖNETIMI 'nin (asm)** seçili olduğundan emin olun.
   - **Veritabanı dosyaları konumu**IÇIN varsayılan ASM Önerilen konumunu kullanın.
   - **Hızlı kurtarma alanı**IÇIN varsayılan ASM Önerilen konumunu kullanın.
   - **yönetici parolası** yazın ve **parolayı onaylayın**.
   - emin `create as container database` olun.
   - bir `pluggable database name` değer yazın.

4. **Özet** sayfasında, seçtiğiniz ayarları gözden geçirin ve ardından veritabanını oluşturmak için öğesine `Finish` tıklayın.

   ![Özet sayfasının ekran görüntüsü](./media/oracle-asm/createdb03.png)

5. Veritabanı oluşturuldu. **Son** sayfasında, bu veritabanını kullanmak ve parolaları değiştirmek için ek hesapların kilidini açma seçeneğiniz vardır. Bunu yapmak istiyorsanız, **parola yönetimi** ' ni seçin, yoksa açık `close`' a tıklayın.

## <a name="delete-the-vm"></a>VM’yi silin

Oracle DB görüntüsünde Oracle otomatik depolama yönetimi 'ni Azure Marketi 'nden başarıyla yapılandırdınız.  Artık bu sanal makineye ihtiyacınız kalmadığında, kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Oracle DataGuard 'ı yapılandırma](configure-oracle-dataguard.md)

[Öğretici: Oracle GoldenGate 'i yapılandırma](Configure-oracle-golden-gate.md)

[Oracle DB mimarı](oracle-design.md) izleme
