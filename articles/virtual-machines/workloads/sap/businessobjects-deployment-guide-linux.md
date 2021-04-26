---
title: Linux için Azure 'da SAP BusinessObjects bı platformu dağıtımı | Microsoft Docs
description: Linux için Azure 'da SAP BusinessObjects bı platformunu dağıtma ve yapılandırma
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520029"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure’da Linux için SAP BusinessObjects İş Zekası platformu dağıtım kılavuzu

Bu makalede, Linux için Azure 'da SAP BOBI platformunu dağıtma stratejisi açıklanır. Bu örnekte, kendi install dizini olarak Premium SSD yönetilen diskleri olan iki sanal makine yapılandırılır. MySQL için Azure veritabanı, CMS veritabanı için kullanılır ve dosya deposu sunucusu için Azure NetApp Files her iki sunucu arasında paylaşılır. Varsayılan Tomcat Java Web uygulaması ve bı Platform uygulaması her iki sanal makinede birlikte yüklenir. Kullanıcı isteğinin yükünü dengelemek için, yerel TLS/SSL boşaltma özelliklerine sahip Application Gateway kullanılır.

Bu tür bir mimari, küçük dağıtım veya üretim dışı ortamlar için geçerlidir. Üretim veya büyük ölçekli dağıtım için, Web uygulaması için ayrı konaklara sahip olabilir ve ayrıca, sunucunun daha fazla bilgi işlemesini sağlayan birden çok BOBI uygulaması barındırır.

![Linux için Azure 'da SAP BOBI dağıtımı](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Bu örnekte, ürün sürümü ve dosya sistemi düzeninin altında kullanılır

- SAP BusinessObjects Platform 4,3
- SUSE Linux Enterprise Server 12 SP5
- MySQL için Azure veritabanı (sürüm: 8.0.15)
- MySQL C API Bağlayıcısı-libmysqlclient (sürüm: 6.1.11)

| Dosya Sistemi        | Açıklama                                                                                                               | Boyut (GB)             | Sahip  | Grup  | Depolama                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/SAP           | SAP BOBI örneği, varsayılan Tomcat Web uygulaması ve veritabanı sürücüleri (gerekliyse) yüklemek için dosya sistemi | SAP boyutlandırma yönergeleri | bl1adm | sapsys | Yönetilen Premium disk-SSD |
| /usr/SAP/frsinput  | Bağlama dizini, giriş dosyası deposu dizini olarak kullanılacak tüm BOBI konaklarındaki paylaşılan dosyalara yöneliktir  | İşletme gereksinimi         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/SAP/frsoutput | Bağlama dizini, çıkış dosyası depo dizini olarak kullanılacak tüm BOBI konaklarındaki paylaşılan dosyalara yöneliktir | İşletme gereksinimi         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux sanal makinesini Azure portal aracılığıyla dağıtma

Bu bölümde, SAP BOBI platformu için Linux Işletim sistemi (OS) görüntüsü ile iki sanal makine (VM) oluşturacağız. Sanal makineler oluşturmak için üst düzey adımlar aşağıdaki gibidir.

1. [Kaynak grubu](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) oluşturma

2. Bir [sanal ağ](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)oluşturun.

   - SAP BI platform dağıtımında tüm Azure hizmetleri için tek alt ağ kullanmayın. SAP BI platformu mimarisine bağlı olarak birden çok alt ağ oluşturmanız gerekir. Bu dağıtımda üç alt ağ oluşturacağız-uygulama alt ağı, dosya deposu deposu alt ağı ve Application Gateway alt ağ oluşturacağız.
   - Azure 'da Application Gateway ve Azure NetApp Files her zaman ayrı bir alt ağda olmalıdır. Daha fazla bilgi için [Azure NetApp Files ağ planlama makalesine yönelik](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) [Azure Application Gateway](../../../application-gateway/configuration-overview.md) ve yönergeleri inceleyin.

3. Bir kullanılabilirlik kümesi oluşturun.

   - Birden çok örnekli dağıtımda her bir katmanın yedekliğine ulaşmak için, her bir katman için sanal makineleri bir kullanılabilirlik kümesine yerleştirin. Mimariniz temelinde her katman için kullanılabilirlik kümelerini ayırdığınızdan emin olun.

4. Sanal makine oluştur 1 **(azusbosl1).**

   - Özel görüntü kullanabilir ya da Azure Marketi 'nden bir görüntü seçebilirsiniz. [SAP Için Azure Marketi 'nden BIR VM dağıtma](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) veya ihtiyaya göre [SAP için özel BIR görüntü ile VM dağıtma](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) bölümüne bakın.

5. Sanal makine oluştur 2 **(azusbosl2).**
6. Bir Premium SSD disk ekleyin. Bu, SAP BOBI yükleme dizini olarak kullanılacaktır.

## <a name="provision-azure-netapp-files"></a>Sağlama Azure NetApp Files

Azure NetApp Files kuruluma devam etmeden önce, Azure [NetApp Files belgelerini](../../../azure-netapp-files/azure-netapp-files-introduction.md)öğrenmeye çalışın.

Azure NetApp Files çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)kullanılabilir. Seçtiğiniz Azure bölgesinin Azure NetApp Files sunmadığını denetleyin.

Bölgeye göre Azure NetApp Files kullanılabilirliğini denetlemek için [Azure bölgesi tarafından Azure NetApp Files kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) sayfasını kullanın.

Azure NetApp Files dağıtmadan önce [Azure NetApp Files yönergeler Için kaydolun](../../../azure-netapp-files/azure-netapp-files-register.md) bölümüne giderek Azure NetApp Files ekleme isteği yapın.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files kaynaklarını dağıtma

Aşağıdaki yönergelerde, [Azure Sanal ağınızı](../../../virtual-network/virtual-networks-overview.md)zaten dağıttığınız varsayılmaktadır. Azure NetApp Files kaynakların dağıtılacağı Azure NetApp Files kaynakları ve VM 'Ler, aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında dağıtılmalıdır.

1. Kaynakları henüz dağıtmadıysanız [Azure NetApp Files ekleme](../../../azure-netapp-files/azure-netapp-files-register.md)isteyin.

2. [Bir NetApp hesabı oluşturma](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)bölümündeki yönergeleri Izleyerek seçtiğiniz Azure bölgesindeki bir NetApp hesabı oluşturun.

3. [Bir Azure NetApp Files kapasite havuzu ayarlama](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)bölümündeki yönergeleri izleyerek Azure NetApp Files bir kapasite havuzu ayarlayın.

   - Bu makalede sunulan SAP BI platformu mimarisi, *Premium* hizmet düzeyinde tek bir Azure NetApp Files kapasite havuzu kullanır. Azure 'da SAP BI dosya deposu sunucusu için Azure NetApp Files *Premium* veya *Ultra* [hizmet düzeyi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)kullanmanızı öneririz.

4. [Azure NetApp Files için bir alt ağ devretmek](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)içindeki yönergelerde açıklandığı gibi Azure NetApp Files için bir alt ağ atayın.

5. [Azure NetApp Files IÇIN NFS birimi oluşturma](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)bölümündeki yönergeleri izleyerek Azure NetApp Files birimleri dağıtın.

   Her iki protokol SAP BOBI platformunda desteklendiğinden, ANF birimi NFSv3 ve NFSv 4.1 olarak dağıtılabilir. Birimleri ilgili Azure NetApp Files alt ağında dağıtın. Azure NetApp birimlerinin IP adresleri otomatik olarak atanır.

Azure NetApp Files kaynaklarının ve Azure VM 'lerinin aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında olması gerektiğini unutmayın. Örneğin, azusbobi-frsinput, azusbobi-frsoutput birim adları ve nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput Azure NetApp Files birimlerinin dosya yollarıdır.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Önemli noktalar

SAP BOBI platformu dosya deposu sunucusu için Azure NetApp Files oluştururken, aşağıdaki dikkat edin:

1. En düşük kapasite havuzu 4 tebibayt (Tib).
2. En küçük birim boyutu 100 Gibibyte (gib).
3. Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](../../../virtual-network/virtual-network-peering-overview.md) olmalıdır. Aynı bölgedeki VNET eşlemesi üzerinden Azure NetApp Files erişim artık desteklenir. Azure NetApp erişimi genel eşleme üzerinden henüz desteklenmiyor.
4. Seçili sanal ağın Azure NetApp Files için temsilci atanmış bir alt ağı olmalıdır.
5. Azure NetApp Files [dışarı aktarma ilkesiyle](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)izin verilen istemcileri, erişim türünü (okuma-yazma, salt okuma vb.) denetleyebilirsiniz.
6. Azure NetApp Files özelliği henüz bölge duyarlı değildir. Şu anda, özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun.
7. Azure NetApp Files birimler, NFSv3 veya NFSv 4.1 birimleri olarak dağıtılabilir. Her iki protokol de SAP BI platformu uygulamaları için desteklenir.

## <a name="configure-file-systems-on-linux-servers"></a>Linux sunucularındaki dosya sistemlerini yapılandırma

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

**[A]**: adım tüm konaklar için geçerlidir

### <a name="format-and-mount-sap-file-system"></a>SAP dosya sistemi biçimlendirme ve bağlama

1. **[A]** tüm ekli Diskleri Listele

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** /usr/SAP için biçim blok cihazı

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** bağlama dizini oluşturma

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** blok cihazının UUID 'sini alın

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab 'de dosya sistemi bağlama girişi Bakımı

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** dosya sistemi bağlama

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Azure NetApp Files birimi bağlama

1. **[A]** bağlama dizinleri oluşturma

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** istemci Işletim sistemini Nfsv 4.1 Mount ' i destekleyecek şekilde yapılandırma **(yalnızca nfsv 4.1 kullanılıyorsa geçerlidir)**

   NFSv 4.1 protokolüyle Azure NetApp Files birimleri kullanıyorsanız, tüm VM 'lerde aşağıdaki yapılandırmayı yürütün; burada, Azure NetApp Files NFSv 4.1 birimlerinin bağlanması gerekir.

   **NFS etki alanı ayarlarını doğrulama**

   Etki alanının varsayılan Azure NetApp Files etki alanı olarak yapılandırıldığından, **defaultv4iddomain.com** ve eşlemenin hiçbir bir şekilde ayarlandığından emin **olun.**

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > SANAL makinede bulunan/etc/ıdmapd.exe içindeki NFS etki alanını Azure NetApp Files: **defaultv4iddomain.com** konumundaki varsayılan etki alanı yapılandırmasıyla eşleşecek şekilde ayarladığınızdan emin olun. NFS istemcisindeki (yani, VM) ve NFS sunucusunun etki alanı yapılandırması arasında uyuşmazlık varsa (örneğin, Azure NetApp yapılandırması), VM 'Lere bağlı Azure NetApp birimlerinde dosya izinleri hiç kimse tarafından görüntülenir.

   Doğrulayın `nfs4_disable_idmapping` . **Y** olarak ayarlanmalıdır. Bulunduğu dizin yapısını oluşturmak için `nfs4_disable_idmapping` Mount komutunu yürütün. Erişim çekirdek/sürücü için ayrıldığından,/sys/modules altında dizini el ile oluşturamazsınız.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** bağlama girdileri ekleme

   NFSv3 kullanıyorsanız

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv 4.1 kullanılıyorsa

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS birimleri bağlama

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS veritabanını yapılandırma-MySQL için Azure veritabanı

Bu bölümde Azure portal kullanarak MySQL için Azure veritabanı sağlama hakkında ayrıntılı bilgi verilmektedir. Ayrıca, SAP BOBI platformu için CMS ve denetim veritabanlarının ve veritabanına erişmek için bir kullanıcı hesabının nasıl oluşturulacağı hakkında yönergeler de sağlar.

Yönergeler yalnızca MySQL için Azure DB kullanıyorsanız geçerlidir. Diğer veritabanları için, yönergeler için SAP veya veritabanına özel belgelere başvurun.

### <a name="create-an-azure-database-for-mysql"></a>MySQL için Azure veritabanı oluşturma

Azure portal oturum açın ve [MySQL Için Azure veritabanı 'nın bu hızlı başlangıç kılavuzunda](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)bahsedilen adımları izleyin. MySQL için Azure veritabanı sağlanırken birkaç noktaya göz önünde

1. SAP BI platformu uygulama sunucularınızın çalıştığı MySQL için Azure veritabanı 'nın aynı bölgesini seçin.

2. SAP BOBI sürümüne özel [sap bı Için ürün kullanılabilirliği matrisini (Pam)](https://support.sap.com/pam) temel alan desteklenen bir DB sürümü seçin. SAP PAM 'de MySQL AB için aynı uyumluluk kılavuzlarını izleyin

3. "İşlem + depolama" bölümünde, **sunucuyu Yapılandır** ' ı seçin ve çıktıyı boyutlandırmanıza bağlı olarak uygun fiyatlandırma katmanını seçin.

4. **Depolama otomatik büyüme** varsayılan olarak etkindir. [Depolamanın](../../../mysql/concepts-pricing-tiers.md#storage) yalnızca ölçeklendiğini, aşağı doğru değil olduğunu aklınızda bulundurun.

5. Varsayılan olarak, **yedekleme saklama süresi** yedi gündür, ancak [isteğe bağlı olarak](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 35 güne kadar yapılandırabilirsiniz.

6. MySQL için Azure veritabanı yedeklemeleri, varsayılan olarak yerel olarak yedekli olduğundan, coğrafi olarak yedekli depolamada sunucu yedeklemeleri istiyorsanız, **yedek artıklığı seçeneklerinde** **coğrafi olarak yedekli** ' ı seçin.

> [!NOTE]
> Sunucu oluşturulduktan sonra [yedekleme artıklığı seçeneklerini](../../../mysql/concepts-backup.md#backup-redundancy-options) değiştirme desteklenmiyor.

### <a name="configure-connection-security"></a>Bağlantı güvenliğini yapılandırma

Varsayılan olarak, oluşturulan sunucu bir güvenlik duvarıyla korunur ve herkese açık bir şekilde erişemez. SAP BI platformu uygulama sunucularının çalıştığı sanal ağa erişim sağlamak için aşağıdaki adımları izleyin.  

1. Azure portal sunucu kaynaklarına gidin ve sunucu kaynağınız için sol taraftaki menüden **bağlantı güvenliği** ' ni seçin.
2. **Azure hizmetlerine erişime Izin vermek** için **Evet** ' i seçin.
3. VNET kuralları altında **var olan sanal ağı ekleme**' yi seçin. SAP BI platformu uygulama sunucusunun sanal ağını ve alt ağını seçin. Ayrıca, [MySQL](../../../mysql/connect-workbench.md) çalışma sunucusunu MySQL Için Azure veritabanı 'na bağlayabileceğiniz bağlantı kutusu veya diğer sunuculara erişim sağlamanız gerekir. MySQL çalışma ekranı, CMS ve denetim veritabanı oluşturmak için kullanılacak
4. Sanal ağlar eklendikten sonra **Kaydet**' i seçin.

### <a name="create-cms-and-audit-database"></a>CMS ve denetim veritabanı oluşturma

1. MySQL [Web sitesinden](https://dev.mysql.com/downloads/workbench/)MySQL çalışma ekranı indirin ve yükleyin. MySQL için Azure veritabanı 'nın erişebileceği sunucuya MySQL çalışma ekranı 'nı yüklediğinizden emin olun.

2. MySQL çalışma ekranı kullanarak sunucuya bağlanın. Bu [makalede](../../../mysql/connect-workbench.md#get-connection-information)bahsedilen yönergeyi izleyin. Bağlantı testi başarılı olursa şu iletiyi alırsınız-

   ![SQL çalışma ekranı bağlantısı](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. SQL sorgu sekmesinde, CMS ve denetim veritabanı için şema oluşturmak üzere aşağıdaki sorguyu çalıştırın.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Şemaya bağlanmak için Kullanıcı hesabı oluştur

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. MySQL Kullanıcı hesabının ayrıcalıklarını ve rollerini denetlemek için

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Linux sunucusuna MySQL C API bağlayıcısını (libmysqlclient) yükler

SAP BOBı uygulama sunucusunun veritabanına erişmesi için veritabanı istemcisi/sürücüleri gerekir. Linux için MySQL C API Bağlayıcısı, CMS ve denetim veritabanlarına erişmek için kullanılmalıdır. CMS veritabanına ODBC bağlantısı desteklenmiyor. Bu bölüm, Linux üzerinde MySQL C API bağlayıcısını ayarlama hakkında yönergeler sağlar.

1. MySQL için Azure veritabanı ile uyumlu sürücüleri açıklayan MySQL [sürücüleri ve MySQL Için Azure veritabanı makalesi ile uyumlu yönetim araçları](../../../mysql/concepts-compatibility.md) makalesine başvurun. Makalesindeki **MySQL Connector/C (libmysqlclient)** sürücüsünü denetleyin.

2. Sürücüleri indirmek için bu [bağlantıya](https://downloads.mysql.com/archives/c-c/) başvurun.

3. İşletim sistemini seçin ve MySQL bağlayıcısının paylaşılan bileşen RPM paketini indirin. Bu örnekte, MySQL-Connector-c-Shared-6.1.11 Connector sürümü kullanılır.

4. Bağlayıcıyı tüm SAP BOBI uygulama örneğine yükler.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Libmysqlclient.so yolunu denetleyin

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. LD_LIBRARY_PATH `/usr/lib64` , yükleme için kullanılacak kullanıcı hesabının dizinine işaret edecek şekilde ayarlayın.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Sunucu hazırlığı

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

**[A]**: adım tüm konaklar için geçerlidir.

1. **[A]** LINUX (SLES veya RHEL) özelliğine dayalı olarak çekirdek parametreleri ayarlamanız ve gerekli kitaplıkları yüklemeniz gerekir. [UNIX Için Iş zekası platformu Yükleme Kılavuzu](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)'ndaki **sistem gereksinimleri** bölümüne bakın.

2. **[A]** makinenizde saat diliminin doğru ayarlandığından emin olun. Yükleme Kılavuzu 'ndaki [diğer UNIX ve Linux gereksinimleri bölümüne](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) bakın.

3. **[A]** yazılımın arka plan işlemlerinin çalışacağı kullanıcı hesabı (**BL1** adm) ve grup (sapsys) oluşturun. Yüklemeyi yürütmek ve yazılımı çalıştırmak için bu hesabı kullanın. Hesap, kök ayrıcalık gerektirmez.

4. **[A]** desteklenen bir UTF-8 yerel ayarı kullanmak için Kullanıcı hesabı (**BL1** adm) ortamı ayarlama ve konsol yazılımınızın UTF-8 karakter kümelerini desteklediğinden emin olun. İşletim sisteminizin doğru yerel ayarı kullandığından emin olmak için, LC_ALL ve LANG ortam değişkenlerini (**BL1** adm) Kullanıcı ortamınızdaki tercih ettiğiniz yerel ayara ayarlayın.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Kullanıcı hesabını yapılandırın (**BL1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. SAP hizmeti marketi 'nden SAP BusinessObjects bı platformu için medyayı indirin ve ayıklayın.

## <a name="installation"></a>Yükleme

Sunucuda kullanıcı hesabı **BL1** adm için yerel ayarı denetle

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

SAP BusinessObjects bı platformunun medyasına gidin ve **BL1** adm kullanıcısı ile aşağıdaki komutu çalıştırın

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Sürümünüze özgü olan UNIX için [SAP BOBI platformu](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) yükleme kılavuzunu izleyin. SAP BOBI platformu yüklenirken birkaç noktaya göz önünde yer vardır.

- **Ürün kaydını yapılandır** ekranında, SAP 'Daki sap BusinessObjects [1288121](https://launchpad.support.sap.com/#/notes/1288121) Solutions için geçici LISANS anahtarını kullanabilir veya SAP hizmeti marketi 'nde lisans anahtarı oluşturabilirsiniz

- **Yükleme türünü seçin** ekranında, diğer sunucu için ilk sunucuda (Azusbosl1) **tam** yükleme ' yi seçin (azusbosl2), var olan bobi kurulumunu genişletmek için **özel/Genişlet** ' i seçin.

- **Varsayılan veya var olan veritabanını Seç** ekranında, **var olan bir veritabanını Yapılandır**' ı SEÇIN, bu, CMS ve denetim veritabanı ' nı seçmenizi ister. CMS veritabanı türü ve denetim veritabanı türü için **MySQL** ' i seçin.

  Yükleme sırasında denetim yapılandırmak istemiyorsanız, Denetim veritabanı yok ' u da seçebilirsiniz.

- SAP BOBI mimarisine bağlı olarak **Java Web uygulaması sunucu ekranında** uygun seçenekleri belirleyin. Bu örnekte, aynı SAP BOBI platformuna Tomcat sunucusu yüklenen 1 seçeneğini belirledik.

- CMS veritabanı bilgilerini **CONFIGURE CMS Repository Database-MySQL**' de girin. Linux yüklemesinde CMS veritabanı bilgileri için örnek giriş. MySQL için Azure veritabanı varsayılan bağlantı noktası 3306 ' de kullanılır
  
  ![Linux-CMS veritabanında SAP BOBI dağıtımı](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Seçim Denetim **deposu veritabanını yapılandırma-MySQL**' de denetim veritabanı bilgilerini girin. Linux yüklemesinde veritabanı bilgilerini denetle için örnek giriş.

  ![Linux üzerinde SAP BOBI dağıtımı-denetim veritabanı](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Yönergeleri izleyin ve yüklemeyi gerçekleştirmek için gerekli girişleri girin.

Çok örnekli dağıtım için, yükleme kurulumunu ikinci konakta (azusbosl2) çalıştırın. **Yükleme türünü seçin** ekranında, var olan bobi kurulumunu genişletmek için **özel/Genişlet** ' i seçin.

MySQL için Azure veritabanı teklifi 'nde sunucu örneklerine bağlantıları yeniden yönlendirmek için bir ağ geçidi kullanılır. Bağlantı kurulduktan sonra, MySQL istemcisi MySQL sunucu örneğinizde çalışan gerçek sürümü değil ağ geçidinde belirlenen MySQL sürümünü görüntüler. MySQL sunucu örneğinizin sürümünü öğrenmek için MySQL komut isteminde `SELECT VERSION();` komutunu kullanın. Bu nedenle, Merkezi Yönetim Konsolu 'nda (CMC), ana ağ geçidinde temel olarak ayarlanan farklı bir veritabanı sürümü bulacaksınız. Daha fazla ayrıntı için [MySQL Için Azure veritabanı sunucu sürümlerini](../../../mysql/concepts-supported-versions.md) denetleyin.

![Linux 'ta SAP BOBI dağıtımı-CMC ayarları](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Yükleme sonrası

### <a name="tomcat-clustering---session-replication"></a>Tomcat Kümelemesi-oturum çoğaltma

Tomcat, oturum çoğaltma ve yük devretme için iki veya daha fazla uygulama sunucusunun kümelemesini destekler. SAP BOBı platformu oturumları serileştirilir; bir Kullanıcı oturumu, bir uygulama sunucusu başarısız olsa bile diğer bir Tomcat örneğine sorunsuz bir şekilde yük devreder.

Örneğin, Kullanıcı, SAP BI uygulamasında bir klasör hiyerarşisinde gezinirken başarısız olan bir Web sunucusuna bağlıysa. Doğru yapılandırılmış bir kümeyle Kullanıcı, oturum açma sayfasına yönlendirilmeden klasör hiyerarşisine gidilmeye devam edebilir.

SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)' de, Tomcat Kümelemesi yapılandırma adımları çok noktaya yayın kullanılarak sağlanır. Ancak Azure 'da çok noktaya yayın desteklenmez. Bu nedenle, Azure 'da Tomcat kümesi çalışmasını sağlamak için [Staticmembershipyakalayıcısı](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)) kullanmanız gerekir. Azure 'da Tomcat kümesi ayarlamak için SAP web sitesinde [SAP BUSINESSOBJECTS bı platformu Için statik üyelik kullanarak Tomcat kümelemesini](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) denetleyin.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI platformunun Yük Dengeleme Web katmanı

SAP BOBI çok örnekli dağıtım sürümünde, Java Web uygulaması sunucuları (Web Katmanı) iki veya daha fazla konakta çalışır. Kullanıcı yükünü Web sunucuları arasında eşit bir şekilde dağıtmak için, son kullanıcılar ve Web sunucuları arasında yük dengeleyici kullanabilirsiniz. Azure 'da, Web uygulaması sunucularınız için trafiği yönetmek üzere Azure Load Balancer veya Azure Application Gateway kullanabilirsiniz. Her teklif hakkındaki ayrıntılar aşağıdaki bölümde açıklanmıştır.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure yük dengeleyici (ağ tabanlı yük dengeleyici)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) , trafiği sağlıklı sanal makineler arasında dağıtan yüksek performanslı, düşük gecikmeli bir katman 4 (TCP, UDP) yük dengeleyicidir. Yük dengeleyici durum araştırması, her VM 'de verilen bir bağlantı noktasını izler ve yalnızca bir işletimsel sanal makineye trafik dağıtır. SAP BI platformunun internet 'ten erişilebilir olmasına bağlı olarak, bir genel yük dengeleyici veya iç yük dengeleyici seçebilirsiniz. Kullanılabilirlik Alanları genelinde yüksek kullanılabilirlik sağlamak için bölgesi yedekli.

Web uygulaması sunucusunun, 8080 numaralı bağlantı noktası, varsayılan Tomcat HTTP bağlantı noktası üzerinde çalıştığı ve sistem durumu araştırması tarafından izlenecek şekilde aşağıdaki şekilde, Iç Load Balancer bölümüne bakın. Böylece, son kullanıcılardan gelen tüm gelen istekler, arka uç havuzundaki Web uygulaması sunucularına (azusbosl1 veya azusbosl2) yönlendirilir. Yük dengeleyici TLS/SSL sonlandırmasını (TLS/SSL boşaltma olarak da bilinir) desteklemiyor. Trafiği Web sunucuları arasında dağıtmak için Azure yük dengeleyici kullanıyorsanız, Standart Load Balancer kullanmanızı öneririz.

> [!NOTE]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Web sunucuları genelinde trafiği dengelemek için Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (Web uygulaması yük dengeleyici)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) , uygulamanın kullanıcı trafiğini bir veya daha fazla Web uygulaması sunucusuna yönlendirmesiyle yardımcı olmak için kullanılan bir hizmet olarak uygulama teslim DENETLEYICISI (ADC) sağlar. TLS/SSL boşaltma, Web uygulaması güvenlik duvarı (WAF), tanımlama bilgisi tabanlı oturum benzeşimi ve diğer uygulamalar için farklı katman 7 Yük Dengeleme özelliği sunar.

Application Gateway, SAP BI platformunda uygulama Web trafiğini, arka uç havuzundaki belirtilen kaynaklara yönlendirir-azusbosl1 veya azusbos2. Bağlantı noktasına bir dinleyici atar, kurallar oluşturur ve bir arka uç havuzuna kaynak eklersiniz. Aşağıdaki şekilde, özel ön uç IP adresi (10.31.3.20) ile uygulama ağ geçidi, kullanıcılar için giriş noktası olarak davranır, gelen TLS/SSL (HTTPS-TCP/443) bağlantılarını işler, TLS/SSL şifresini çözer ve şifrelenmemiş istek (HTTP-TCP/8080) ile arka uç havuzundaki sunuculara geçer. Yerleşik TLS/SSL sonlandırma özelliği sayesinde, uygulama ağ geçidinde yalnızca bir TLS/SSL sertifikası sürdürmemiz gerekir ve bu işlem işlemleri basitleştirir.

![Web sunucuları genelinde trafiği dengelemek için Application Gateway](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

SAP BOBI Web sunucusu için Application Gateway yapılandırmak için, SAP blogu üzerinde [Azure Application Gateway kullanarak SAP BOBı Web sunucularının yükünü dengelemeye](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) bakabilirsiniz.

> [!NOTE]
> SSL yük boşaltma gibi özellikler sunan Azure Application Gateway Web sunucusuna trafiği dengelemek, SSL yönetimini sunucu üzerindeki şifreleme ve şifre çözme yükünü azaltmak üzere merkezileştirmek, trafiği dağıtmak için Round-Robin algoritmayı, Web uygulaması güvenlik duvarı (WAF) yeteneklerini, yüksek kullanılabilirlik ve benzeri işlemleri yapmak için kullanmanız önerilir.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects bı platformu-yedekleme ve geri yükleme

Yedekleme ve geri yükleme, ayrı konuma verilerin ve uygulamaların düzenli kopyalarını oluşturma sürecidir. Özgün veriler veya uygulamalar kaybedilmişse veya hasar görmüşse önceki duruma geri yüklenebilir veya kurtarılabilir. Ayrıca, herhangi bir iş olağanüstü durum kurtarma stratejisinin temel bir bileşenidir.

SAP BOBI platformu için kapsamlı yedekleme ve geri yükleme stratejisi geliştirmek için, uygulamada sistem kapalı kalma süresine veya kesintiye neden olan bileşenleri belirleme. SAP BOBI platformunda, uygulamayı korumak için aşağıdaki bileşenlerin yedeklenmesi çok önemlidir.

- SAP BOBI yükleme dizini (yönetilen Premium diskler)
- Dosya deposu sunucusu (Azure NetApp Files veya Azure Premium dosyaları)
- CMS veritabanı (MySQL için Azure veritabanı veya Azure VM 'de veritabanı)

Aşağıdaki bölümde, SAP BOBI platformunda her bir bileşen için yedekleme ve geri yükleme stratejisinin nasıl uygulanacağı açıklanmaktadır.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI yükleme dizini için yedekleme & geri yükleme

Azure 'da, uygulama sunucularını ve tüm eklenen diskleri yedeklemenin en kolay yolu [Azure Backup](../../../backup/backup-overview.md) hizmetini kullanmaktır. Sanal makinelerinizdeki verilerin istenmeden bozmasını korumak için bağımsız ve yalıtılmış yedeklemeler sağlar. Yedekler, yerleşik kurtarma noktası yönetim özelliklerine sahip Kurtarma Hizmetleri kasasında depolanır. Yapılandırma ve ölçekleme basittir, yedeklemeler en iyi duruma getirilir ve gerektiğinde kolayca geri yüklenebilir.

Yedekleme işleminin bir parçası olarak, anlık görüntü alınır ve veriler, üretim iş yükleri üzerinde hiçbir etkilenmeden kurtarma hizmeti kasasına aktarılır. Anlık görüntü, [anlık görüntü tutarlılığı](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) makalesinde açıklandığı gibi farklı düzeyde tutarlılık sağlar. Ayrıca, seçmeli diskler Yedekleme ve geri yükleme işlevini kullanarak VM 'deki veri disklerinin alt kümesini yedeklemeyi seçebilirsiniz. Daha fazla bilgi için bkz. [Azure VM yedekleme](../../../backup/backup-azure-vms-introduction.md) belgesi ve [SSS-Azure VM 'leri yedekleme](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Dosya deposu sunucusu için yedekleme & geri yükleme

**Azure NetApp Files** için, anlık görüntü ilkeleri kullanarak isteğe bağlı anlık görüntüler oluşturabilir ve otomatik anlık görüntü zamanlayabilirsiniz. Anlık görüntü kopyaları, ANF biriminizin bir zaman içinde kopyasını sağlar. Daha fazla bilgi için bkz. [Azure NetApp Files kullanarak anlık görüntüleri yönetme](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure dosya** yedeklemesi, VM yedeklemesinden birlikte yedekleme ve geri yükleme işlevini merkezileştiren ve işlem çalışmasını kolaylaştıran yerel [Azure Backup](../../../backup/backup-overview.md) hizmetiyle tümleşiktir. Daha fazla bilgi için bkz. [Azure dosya paylaşma yedekleme](../../../backup/azure-file-share-backup-overview.md) ve [SSS-Azure dosyalarını yedekleme](../../../backup/backup-azure-files-faq.yml).

#### <a name="backup--restore-for-cms-database"></a>CMS veritabanı için yedekleme & geri yükleme

MySQL Azure veritabanı, Azure 'da otomatik olarak sunucu yedeklemeleri oluşturan ve bunları Kullanıcı tarafından yerel olarak yedekli veya coğrafi olarak yedekli depolama alanı yapılandırılmış şekilde depolayan Azure 'daki tekliftir. MySQL Azure veritabanı, veri dosyalarının ve işlem günlüğünün yedeklerini alır. Desteklenen maksimum depolama boyutuna bağlı olarak, tam ve fark yedeklemeleri (4 TB maksimum depolama sunucusu) veya anlık görüntü yedeklemesi (16 TB 'a kadar maksimum depolama sunucusuna kadar) alır. Bu yedeklemeler, yapılandırılmış yedekleme saklama süresi içinde herhangi bir zamanda bir sunucuyu geri yüklemenize olanak tanır. Varsayılan yedekleme saklama süresi yedi gündür ve [isteğe bağlı olarak](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) üç güne kadar yapılandırabilirsiniz. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Bu yedekleme dosyaları Kullanıcı tarafından sunulmamış ve verilemez. Bu yedeklemeler, yalnızca MySQL için Azure veritabanı 'nda geri yükleme işlemleri için kullanılabilir. Bir veritabanını kopyalamak için [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) kullanabilirsiniz. Daha fazla bilgi için bkz. [MySQL Için Azure veritabanı 'Nda yedekleme ve geri yükleme](../../../mysql/concepts-backup.md).

Sanal makinelerde yüklü veritabanı için standart yedekleme araçları veya [Azure Backup](../../../backup/sap-hana-db-about.md) Hana veritabanı için kullanabilirsiniz. Ayrıca, Azure hizmetleri ve araçları gereksinimlerinizi karşılamıyorsa, disk yedeklemesi oluşturmak için diğer yedekleme araçlarını veya komut dosyasını kullanabilirsiniz.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects bı platform güvenilirliği

SAP BusinessObjects bı platformu, belirli görevler ve işlemler için optimize edilmiş farklı katmanlar içerir. Bir katman bir bileşen kullanılamaz hale geldiğinde, SAP BOBI uygulaması erişilemez duruma gelir veya uygulamanın belirli bir işlevi çalışmaz. Bu nedenle, her katmanın bir iş kesintisi olmadan uygulamanın çalışır durumda tutulması için güvenilir olacak şekilde tasarlandığından emin olun.

Bu bölüm SAP BOBI platformu için aşağıdaki seçeneklere odaklanır-

- **Yüksek kullanılabilirlik:** Yüksek oranda kullanılabilir bir platformda, sunuculardan biri kullanılamaz hale gelirse uygulamanın çalışır durumda tutulması için Azure bölgesindeki her şeyin en az ikisi bulunur.
- **Olağanüstü durum kurtarma:** Herhangi bir doğal olağanüstü durum nedeniyle tüm Azure bölgesi gibi çok zararlı bir kayıp varsa, uygulama işlevlerinden geri yükleme işlemidir.

Bu çözümün uygulanması, Azure 'daki sistem kurulumunun doğasına göre farklılık gösterir. Böylece müşterinin, iş gereksinimlerine bağlı olarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümünü uyarması gerekir.

### <a name="high-availability"></a>Yüksek kullanılabilirlik

Yüksek kullanılabilirlik, aynı veri merkezinde yer alan yedekli, hataya dayanıklı veya yük devretme korumalı bileşenler aracılığıyla uygulama/hizmet iş sürekliliği sağlayarak BT kesintilerini en aza indirecek bir teknoloji kümesini ifade eder. Bu durumda, veri merkezleri bir Azure bölgesi içindedir. [SAP Için yüksek kullanılabilirlik mimarisi ve senaryoları](sap-high-availability-architecture-scenarios.md) , bu bölümdeki yönergeleri KARMAŞıŞMAK üzere SAP uygulamaları için Azure 'da sunulan farklı yüksek kullanılabilirliğe sahip tekniklerin ve öneriyle ilgili bir başlangıç öngörüleri sağlar.

SAP BOBI platformunun boyutlandırma sonucuna bağlı olarak, yatay tasarlamanızı ve bı bileşenlerinin Azure sanal makineleri ve alt ağları arasında dağıtımını belirlemeniz gerekir. Dağıtılmış mimarideki artıklık düzeyi, iş için gerekli kurtarma süresi hedefi (RTO) ve kurtarma noktası hedefine (RPO) bağlıdır. SAP BOBI platformu, her katmanda farklı katmanlar ve bileşenler içerir ve bu da yedeklilik sağlamak için tasarlanmalıdır. Bir bileşen başarısız olursa, SAP BOBI uygulamanızda bir kesinti olmaz. Örneğin,

- Bı uygulama sunucuları ve Web sunucusu gibi yedekli uygulama sunucuları
- CMS veritabanı, dosya deposu sunucusu, Load Balancer gibi benzersiz bileşenler

Aşağıdaki bölümde, SAP BOBI platformunun her bir bileşeni için yüksek kullanılabilirlik elde etme açıklanmaktadır.

#### <a name="high-availability-for-application-servers"></a>Uygulama sunucuları için yüksek kullanılabilirlik

Bı ve Web uygulaması sunucularında ayrı olarak mı yoksa birlikte mı yüklenebileceğine bakılmaksızın, belirli bir yüksek kullanılabilirlik çözümüne gerek kalmaz. Çeşitli Azure sanal makinelerinde birçok bı ve Web sunucusu örneğini yapılandırarak yedeklilik ile yüksek kullanılabilirlik elde edebilirsiniz.

Bir veya daha fazla olay nedeniyle kapalı kalma süresinin etkilerini azaltmak için, birden çok sanal makinede çalışan uygulama sunucuları için yüksek kullanılabilirlik uygulaması 'nın altında izlenmesi önerilir.

- Veri merkezi başarısızlıklarını korumak için Kullanılabilirlik Alanları kullanın.
- Bir kullanılabilirlik kümesindeki birden fazla sanal makineyi artıklık için yapılandırın.
- Bir kullanılabilirlik kümesindeki VM 'Ler için yönetilen diskleri kullanın.
- Her uygulama katmanını ayrı kullanılabilirlik kümeleri olarak yapılandırın.

Daha fazla bilgi için [Linux sanal makinelerinin kullanılabilirliğini yönetme](../../availability.md) ' yi inceleyin

#### <a name="high-availability-for-cms-database"></a>CMS veritabanı için yüksek kullanılabilirlik

CMS veritabanı için Azure veritabanı hizmet olarak veritabanı (DBaaS) hizmetini kullanıyorsanız, yüksek kullanılabilirlik çerçevesi varsayılan olarak sağlanır. Tüm ek bileşenleri yapılandırmanıza gerek kalmadan, yüksek kullanılabilirlik, artıklık ve dayanıklılık özelliklerine sahip bölgeyi ve hizmeti seçmeniz yeterlidir. Azure 'da desteklenen DBaaS sunumunun SLA 'Sı hakkında daha fazla bilgi için, [MySQL Için Azure veritabanı](../../../mysql/concepts-high-availability.md) ve [Azure SQL veritabanı için yüksek kullanılabilirlik](../../../azure-sql/database/high-availability-sla.md) için yüksek kullanılabilirlik denetimi yapın

CMS veritabanına yönelik diğer DBMS dağıtımı için, farklı DBMS dağıtımıyla ilgili Öngörüler ve yüksek kullanılabilirliğe ulaşmak için yaklaşımı sağlayan [SAP Iş yükü Için DBMS dağıtım kılavuzlarını](dbms_guide_general.md)inceleyin.

#### <a name="high-availability-for-file-repository-server"></a>Dosya deposu sunucusu için yüksek kullanılabilirlik

Dosya deposu sunucusu (FRS), raporlar, evrenler ve bağlantılar gibi içeriklerin depolandığı disk dizinlerine başvurur. Bu, bu sistemin tüm uygulama sunucuları arasında paylaşılıyor. Bu nedenle, yüksek oranda kullanılabilir olduğundan emin olmanız gerekir.

Azure 'da, yüksek oranda kullanılabilir ve yüksek oranda dayanıklı olacak şekilde tasarlanan dosya paylaşımının [Azure Premium dosyalarını](../../../storage/files/storage-files-introduction.md) veya [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) seçebilirsiniz. Daha fazla bilgi için bkz. Azure dosyaları için [Artıklık](../../../storage/files/storage-files-planning.md#redundancy) bölümü.

> [!NOTE]
> Azure dosyaları için SMB protokolü genel kullanıma sunulmuştur, ancak Azure dosyaları için NFS protokol desteği şu anda önizleme aşamasındadır. Daha fazla bilgi için bkz. [Azure dosyaları Için NFS 4,1 desteği artık önizlemededir](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Bu dosya paylaşma hizmeti tüm bölgelerde kullanılamadığından, güncel bilgileri öğrenmek için [bölge sitesi tarafından kullanılabilen ürünlere](https://azure.microsoft.com/en-us/global-infrastructure/services/) başvurduğunuzdan emin olun. Hizmet bölgenizde yoksa, dosya sistemini SAP BOBı uygulamasına paylaşabileceğiniz NFS sunucusu oluşturabilirsiniz. Ancak, yüksek oranda kullanılabilirliğini de göz önünde bulundurmanız gerekir.

#### <a name="high-availability-for-load-balancer"></a>Yük Dengeleyici için yüksek kullanılabilirlik

Trafiği Web sunucusu arasında dağıtmak için Azure Load Balancer ya da Azure Application Gateway kullanabilirsiniz. Yük dengeleyiciden birinin artıklığı, dağıtım için seçtiğiniz SKU 'ya göre elde edilebilir.

- Azure Load Balancer için yedeklilik, bölgesel olarak yedekli Standart Load Balancer olarak yapılandırılarak elde edilebilir. Daha fazla bilgi için bkz. [Standart Load Balancer ve kullanılabilirlik alanları](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Application Gateway için, dağıtım sırasında seçilen katman türüne göre yüksek kullanılabilirlik elde edilebilir.
  - iki veya daha fazla örnek dağıttığınız zaman v1 SKU 'SU yüksek kullanılabilirlik senaryolarını destekler. Azure, örnekleri aynı anda başarısız olmamasını sağlamak için bu örnekleri güncelleştirme ve hata etki alanları arasında dağıtır. Bu SKU ile, bölge içinde yedeklilik elde edilebilir
  - v2 SKU 'SU otomatik olarak, yeni örneklerin hata etki alanlarına ve güncelleştirme etki alanlarına yayılmasını sağlar. Bölge artıklığı ' nı seçerseniz, en yeni örnekler Ayrıca, Kullanılabilirlik alanlarına göre yayarak hata dayanıklılığı sağlar. Daha ayrıntılı bilgi için bkz. [Otomatik ölçeklendirme ve bölge yedekli Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects bı platformu için yüksek kullanılabilirlik mimarisine başvur

Aşağıdaki başvuru mimarisi, kullanılabilirlik kümesi kullanılarak SAP BOBI platformunun kurulumunu açıklar ve bu da bölgede VM artıklığı ve kullanılabilirliği sağlar. Showarchitecture, farklı yüksek kullanılabilirlik çözümlerinin yönetilmesine yönelik karmaşıklığın azaltılabileceği, yerleşik artıklık sunan SAP BOBI platformu için Azure Application Gateway, Azure NetApp Files ve Azure veritabanı gibi farklı Azure Hizmetleri kullanımını sağlar.

Aşağıdaki şekilde, gelen trafik (HTTPS-TCP/443), iki veya daha fazla örneğe dağıtıldığında yüksek oranda kullanılabilir olan Azure Application Gateway v1 SKU 'SU kullanılarak yük dengelemesi yapılır. Web sunucusu, yönetim sunucusu ve işleme sunucularının birden çok örneği, artıklık sağlamak için ayrı sanal makinelere dağıtılır ve her katman ayrı kullanılabilirlik kümelerinde dağıtılır. Azure NetApp Files, veri merkezi içinde yerleşik artıklığa sahiptir, bu nedenle dosya deposu sunucusu için ANF birimleriniz yüksek oranda kullanılabilir olacaktır. CMS veritabanı, yüksek kullanılabilirliğe sahip MySQL için Azure veritabanı (DBaaS) üzerinde sağlanır. Daha fazla bilgi için bkz. [MySQL Için Azure veritabanı Kılavuzu 'Nda yüksek kullanılabilirlik](../../../mysql/concepts-high-availability.md) .

![Kullanılabilirlik kümelerini kullanarak SAP BusinessObjects bı platformu yedekliliği](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Yukarıdaki mimari, Azure 'da SAP BOBı dağıtımının nasıl yapılabileceği hakkında öngörü sağlar. Ancak Azure 'da SAP BOBI platformu için tüm olası yapılandırma seçeneklerini kapsamaz. Müşteri, Load Balancer, dosya deposu sunucusu ve DBMS gibi farklı bileşenler için farklı ürünler/hizmetler seçerek kendi dağıtımını kendi iş gereksinimlerine göre uyarlayabilirsiniz.

Birçok Azure bölgesinde, bağımsız olarak güç kaynağı, soğutma ve ağ sağlama anlamına gelen Kullanılabilirlik Alanları sunulur. Müşterinin uygulamayı iki veya üç kullanılabilirlik alanı arasında dağıtmasını sağlar. Azlar genelinde yüksek kullanılabilirlik elde etmek isteyen müşteri için kullanılabilirlik alanları genelinde SAP BOBI platformu dağıtabilir, böylece uygulamadaki her bileşenin bölge yedekli olduğundan emin olabilirsiniz.

### <a name="disaster-recovery"></a>Olağanüstü durum kurtarma

Bu bölümdeki yönerge, SAP BOBI platformu için olağanüstü durum kurtarma koruması sağlama stratejisini açıklamaktadır. Genel SAP olağanüstü durum kurtarma yaklaşımının birincil kaynaklarını temsil eden [SAP belgesi Için olağanüstü durum kurtarmayı](../../../site-recovery/site-recovery-sap.md) tamamlar.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects bı platformu için başvuru olağanüstü durum kurtarma mimarisi

Bu başvuru mimarisi, yedekli uygulama sunucularıyla SAP BOBI platformunun çok örnekli dağıtımını çalıştırıyor. Olağanüstü durum kurtarma için tüm katmanın yükünü ikincil bir bölgeye devreder. Her katman olağanüstü durum kurtarma koruması sağlamak için farklı bir strateji kullanır.

![SAP BusinessObjects bı platformu olağanüstü durum kurtarma](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Yük dengeleyici

Load Balancer, trafiği SAP BOBI platformunun Web uygulaması sunucuları arasında dağıtmak için kullanılır. Azure Application Gateway için DR sağlamak üzere, ikincil bölgede Application Gateway 'in paralel kurulumunu uygulayın.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Web ve bı uygulama sunucularını çalıştıran sanal makineler

Azure Site Recovery hizmeti, ikincil bölgede Web ve bı uygulama sunucularını çalıştıran sanal makineleri çoğaltmak için kullanılabilir. Olağanüstü durum ve kesintiler gerçekleştiğinde, çoğaltılan ortamınıza kolayca yük devretmek ve çalışmaya devam etmek için ikincil bölgedeki sunucuları çoğaltır

#### <a name="file-repository-servers"></a>Dosya deposu sunucuları

- **Azure NetApp FILES** NFS ve SMB birimleri sağlar; bu nedenle, Azure bölgeleri arasında veri çoğaltmak için herhangi bir dosya tabanlı kopyalama aracı kullanılabilir. Başka bir bölgedeki bir ANF birimini kopyalama hakkında daha fazla bilgi için bkz. [SSS hakkında Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Şu anda NetApp anlık görüntü® teknolojisini kullanan [önizlemede](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) olan Azure NetApp Files çapraz bölge çoğaltmasını kullanabilirsiniz. Bu nedenle, yalnızca değiştirilen bloklar ağ üzerinden sıkıştırılmış, etkili bir biçimde gönderilir. Bu özel teknoloji, bölge genelinde çoğaltmak için gereken veri miktarını en aza indirir ve veri aktarımı maliyetlerini kaydeder. Ayrıca, daha küçük bir geri yükleme noktası hedefine (RPO) ulaşmak için çoğaltma süresini kısaltır. Daha fazla bilgi için [çapraz bölge çoğaltmasını kullanma gereksinimleri ve konuları](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) bölümüne bakın.

- **Azure Premium dosyaları** yalnızca yerel olarak yedekli (LRS) ve bölgesel olarak yedekli depolamayı (ZRS) destekler. Azure Premium dosyaları DR stratejisi için [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) veya [Azure PowerShell](/powershell/module/az.storage/) kullanarak dosyalarınızı farklı bir bölgedeki başka bir depolama hesabına kopyalayabilirsiniz. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma ve depolama hesabı yük devretme](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS veritabanı

MySQL için Azure veritabanı, herhangi bir olağanüstü durum varsa veritabanını kurtarmak için birden çok seçenek sağlar. İşletmeniz için uygun seçeneği belirleyin.

- İş sürekliliği ve olağanüstü durum kurtarma planlamasını iyileştirmek için bölgeler arası okuma çoğaltmalarını etkinleştirin. Kaynak sunucudan en fazla beş çoğaltmaya çoğaltabilirsiniz. Okuma çoğaltmaları, MySQL 'in ikili günlük çoğaltma teknolojisi kullanılarak zaman uyumsuz olarak güncelleştirilir. Çoğaltmalar, yönettiğiniz yeni sunuculardan MySQL için normal Azure veritabanı sunucularına benzer. Okuma çoğaltmaları, kullanılabilir bölgeler, kısıtlamalar ve [okuma çoğaltmaları kavramları makalesindeki](../../../mysql/concepts-read-replicas.md)yük devretme hakkında daha fazla bilgi edinin.

- Alıcının coğrafi geri yükleme özelliği olan ve coğrafi olarak yedekli yedeklemeleri kullanarak sunucuyu geri yükleyen Azure veritabanı 'nı kullanın. Bu yedeklemeler, sunucunuzun barındırıldığı bölge çevrimdışı olduğunda bile erişilebilir. Bu yedeklerden başka bir bölgeye geri yükleme yapabilir ve sunucunuzu yeniden çevrimiçi hale getirebilirsiniz.

  > [!NOTE]
  > Coğrafi geri yükleme yalnızca, sunucuyu coğrafi olarak yedekli yedekleme depolama alanı ile sağladıysanız mümkündür. Sunucu oluşturulduktan sonra **yedekleme artıklığı seçeneklerini** değiştirme desteklenmiyor. Daha fazla bilgi için bkz. [yedek artıklık](../../../mysql/concepts-backup.md#backup-redundancy-options) makalesi.

Bu örnekte kullanılan her bir katmanın olağanüstü durum kurtarma önerisi aşağıda verilmiştir.

| SAP BOBı platform katmanları   | Öneri                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Ikincil bölgede Application Gateway paralel kurulumu                                                |
| Web uygulaması sunucuları   | Site Recovery kullanarak çoğaltma                                                                         |
| Bı uygulama sunucuları    | Site Recovery kullanarak çoğaltma                                                                         |
| Azure NetApp Files        | Verileri Ikincil bölgeye **veya** ANF çapraz bölge çoğaltmasına (Önizleme) çoğaltmak için dosya tabanlı kopyalama aracı |
| MySQL için Azure Veritabanı  | Bölgeler arası okuma çoğaltmaları **veya** coğrafi olarak yedekli yedeklerden yedekleme geri yükleme.                             |

## <a name="next-steps"></a>Sonraki adımlar

- [Çok katmanlı SAP uygulaması dağıtımı için olağanüstü durum kurtarmayı ayarlama](../../../site-recovery/site-recovery-sap.md)
- [SAP için Azure sanal makineleri planlama ve uygulama](planning-guide.md)
- [SAP için Azure sanal makineleri dağıtımı](deployment-guide.md)
- [SAP için Azure sanal makineleri DBMS dağıtımı](./dbms_guide_general.md)
