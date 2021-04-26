---
title: Azure 'da SLES 'de Paceyapıcısı ayarlama | Microsoft Docs
description: Azure 'da SUSE Linux Enterprise Server Paceyapıcısı ayarlama
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: aa2006ecfad91e21ac13a1e63be23302b2a70399
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551042"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Azure 'da SUSE Linux Enterprise Server Paceyapıcısı ayarlama

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Azure 'da bir Paceoluşturucu kümesi kurmak için iki seçenek vardır. Azure API 'Leri aracılığıyla başarısız olan bir düğümü yeniden başlatma işlemini ele alan ve bir SBD cihazı kullanabileceğiniz bir düğüm oluşturma Aracısı kullanabilirsiniz.

SBD cihazı, Iscsı hedef sunucusu olarak davranan ve bir SBD cihazı sağlayan en az bir ek sanal makine gerektirir. Bu Iscsı hedef sunucuları, diğer Paceüreticisi kümeleriyle paylaşılabilir. Bir SBD cihaz kullanmanın avantajı, zaten şirket içinde SBD cihazları kullanıyorsanız, paceoluşturucu kümesini nasıl çalıştıracaksınız üzerinde herhangi bir değişiklik yapılmasını gerektirmez. Bir Paceoluşturucu kümesi için en fazla üç SBD cihazı kullanarak bir SBD cihazının kullanılamaz hale gelmesine izin verebilirsiniz (örneğin, Iscsı hedef sunucusu 'nda işletim sistemi düzeltme eki uygulama). Her Paceyapıcısı için birden fazla SBD cihazı kullanmak istiyorsanız, birden çok Iscsı hedef sunucusu dağıttığınızdan ve her bir Iscsı hedef sunucusundan bir SBD 'nin bağlanmasına emin olun. Tek bir SBD cihazı veya üçten birini kullanmanızı öneririz. Yalnızca iki SBD cihazı yapılandırırsanız ve bunlardan biri kullanılabilir değilse pacemaker bir küme düğümünü otomatik olarak dilimlayamaz. Bir Iscsı hedef sunucusu çalışırken sınır oluşturabilmek istiyorsanız, SBDs kullanırken en esnek yapılandırma olan üç SBD cihazı ve bu nedenle üç Iscsı hedef sunucusu kullanmanız gerekir.

Azure çit Aracısı ek sanal makinelerin dağıtılmasını gerektirmez.   

![SLES 'de pacemaker genel bakış](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Linux pacemaker kümelenmiş düğümlerini ve SBD cihazlarını planlarken ve dağıttığınızda, tüm küme yapılandırmasının genel güvenilirliği, dahil edilen VM 'Ler ve SBD cihazlarını barındıran VM 'Ler arasındaki yönlendirmenin [NVA 'lar](https://azure.microsoft.com/solutions/network-appliances/)gibi başka herhangi bir cihazdan geçirilmediğinden emin olmak için gereklidir. Aksi takdirde, NVA ile ilgili sorunlar ve bakım olayları, genel küme yapılandırmasının kararlılığı ve güvenilirliği üzerinde olumsuz bir etkiye sahip olabilir. Bu tür engelleri önlemek için, Linux Paceoluşturucu kümelenmiş düğümlerini ve SBD cihazlarını planlarken ve dağıttığınızda, NVA 'lar veya [Kullanıcı tanımlı yönlendirme kurallarının](../../../virtual-network/virtual-networks-udr-overview.md) , NVA 'lar ve benzer cihazlar aracılığıyla trafiği yönlendiren yönlendirme kurallarını tanımlamayın. 
>

## <a name="sbd-fencing"></a>SBD sınırlama

Bir SBD cihazını sınırlama için kullanmak istiyorsanız bu adımları izleyin.

### <a name="set-up-iscsi-target-servers"></a>Iscsı hedef sunucularını ayarlama

Önce Iscsı hedef sanal makinelerini oluşturmanız gerekir. Iscsı hedef sunucuları, birden çok Paceüreticisi kümesi ile paylaşılabilir.

1. Yeni SLES 12 SP1 veya üzeri sanal makineler dağıtın ve SSH aracılığıyla bunlara bağlanın. Makinelerin büyük olması gerekmez. Standard_E2s_v3 veya Standard_D2s_v3 gibi bir sanal makine boyutu yeterlidir. İşletim sistemi diski Premium Storage kullandığınızdan emin olun.

Tüm **iSCSI hedef sanal makinelerinde** aşağıdaki komutları çalıştırın.

1. SLES 'yi Güncelleştir

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > İşletim sistemini yükselttikten veya güncelleştirdikten sonra işletim sistemini yeniden başlatmanız gerekebilir. 

1. Paketleri kaldır

   Targetclı ve SLES 12 SP3 ile ilgili bilinen bir sorundan kaçınmak için aşağıdaki paketleri kaldırın. Bulunamayan paketlerle ilgili hataları yoksayabilirsiniz

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Iscsı hedef paketlerini yükler

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Iscsı hedef hizmetini etkinleştirme

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Iscsı hedef sunucusunda Iscsı cihazı oluşturma

SAP sistemleriniz tarafından kullanılan kümeler için Iscsı diskleri oluşturmak üzere tüm **iSCSI hedef sanal makinelerinde** aşağıdaki komutları çalıştırın. Aşağıdaki örnekte, birden çok küme için SBD cihazları oluşturulur. Birden çok küme için bir Iscsı hedef sunucusunu nasıl kullanacağınızı gösterir. SBD cihazları işletim sistemi diskine yerleştirilir. Yeterli alana sahip olduğunuzdan emin olun.

**`nfs`**, NFS kümesini tanımlamak için kullanılır, **ascsnw1** , **NW1** öğesinin ascs kümesini tanımlamak için kullanılır. **dbnw1** , NFS **-0** ve **NFS-1** **' in veritabanı** kümesini tanımlamak için kullanılır, NFS küme düğümlerinin ana bilgisayar adları, **NW1-xscs-0** ve **NW1-xscs-1** , **NW1** ascs küme düğümlerinin ana bilgisayar adları ve **NW1-DB-0** ve **NW1-DB-1** veritabanı kümesi düğümlerinin ana bilgisayar adı. Bunları, Küme düğümlerinizin ana bilgisayar adları ve SAP sisteminizin SID 'SI ile değiştirin.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Her şeyin doğru şekilde ayarlandığını kontrol edebilirsiniz

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD cihazı ayarlama

Kümeden son adımda oluşturulan Iscsı cihazına bağlanın.
Oluşturmak istediğiniz yeni kümenin düğümlerinde aşağıdaki komutları çalıştırın.
Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** iSCSI cihazlarına bağlanma

   İlk olarak, Iscsı ve SBD hizmetlerini etkinleştirin.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** ilk düğümde Başlatıcı adını değiştirin

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Dosya içeriğini Iscsı hedef sunucusunda iSCSI cihazı oluştururken kullandığınız ACL 'Lerle eşleşecek şekilde değiştirin (örneğin, NFS sunucusu için).

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** ikinci düğümdeki Başlatıcı adını değiştirin

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   İSCSI hedef sunucusunda Iscsı cihazını oluştururken kullandığınız ACL 'Lerle eşleşecek şekilde dosyanın içeriğini değiştirin

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** iSCSI hizmetini yeniden başlatın

   Şimdi değişikliği uygulamak için Iscsı hizmetini yeniden başlatın

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Iscsı cihazlarını bağlayın. Aşağıdaki örnekte, 10.0.0.17, Iscsı hedef sunucusunun IP adresidir ve 3260 varsayılan bağlantı noktasıdır. <b>IQN. 2006-04. NFS. Local: NFS</b> , aşağıda yer alan ilk komutu çalıştırdığınızda listelenen hedef adlarından biridir (ı adm-d bulma).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Iscsı cihazlarının kullanılabilir olduğundan ve cihaz adını (aşağıdaki örnekte/dev/SDE) göz önünde bulunduğundan emin olun

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Şimdi Iscsı cihazlarının kimliklerini alın.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Komut her bir SBD cihazı için üç cihaz kimliği listeler. Yukarıdaki örnekte, SCSI-3 ile başlayan KIMLIĞI kullanmanızı öneririz.

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4dad371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** SBD cihazı oluşturma

   İlk küme düğümünde yeni SBD cihazlarını oluşturmak için Iscsı cihazlarının cihaz KIMLIĞINI kullanın.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** SBD yapılandırmasını uyarlayın

   SBD yapılandırma dosyasını açın

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   SBD cihazının özelliğini değiştirin, pacemaker tümleştirmesini etkinleştirin ve SBD 'in başlangıç modunu değiştirin.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   `softdog`Yapılandırma dosyasını oluşturma

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Şimdi modülü yükleyin

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Küme yüklemesi

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A] bir** güncelleştirme SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** küme kaynakları için gerekli olan bileşeni yükler

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** küme kaynakları için gerekli olan Azure-lb bileşenini yükler

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Paket kaynak-aracıları sürümünü denetleyin ve en düşük sürüm gereksinimlerinin karşılandığından emin olun:  
   > - SLES 12 SP4/SP5 için sürüm en az Resource-Agents-4.3.018. a7fb5035-3.30.1 olmalıdır.  
   > - SLES 15/15 SP1 için sürüm en az Resource-Agents-4.3.0184.6 ee15eb2-4.13.1 olmalıdır.  

1. **[A]** işletim sistemini yapılandırma

   Bazı durumlarda, Paceyapıcısı birçok işlem oluşturur ve bu nedenle izin verilen işlem sayısını tüketmektedir. Böyle bir durumda, küme düğümleri arasında bir sinyal başarısız olabilir ve kaynaklarınızın yük devretmesine yol açabilir. Aşağıdaki parametreyi ayarlayarak izin verilen en fazla işlem sayısını artırmayı öneririz.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Kirli önbelleğin boyutunu küçültün. Daha fazla bilgi için bkz. [büyük RAM Ile SLES 11/12 sunucularında düşük yazma performansı](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Cloud-NETCONFIG-ha kümesi için Azure yapılandırma

   >[!NOTE]
   > **Zypper Info Cloud-netconfig-Azure**' ı çalıştırarak, yüklü paket **bulutu-netconfig-Azure** sürümünü denetleyin. Ortamınızdaki sürüm 1,3 veya üzeri ise, bulut ağı eklentisi tarafından ağ arabirimlerinin yönetimini bastırmak artık gerekli değildir. Sürüm 1,3 ' den düşükse, paket **bulutu-netconfig-Azure** 'ı kullanılabilir en son sürüme güncelleştirmeyi öneririz.  

   Bulut ağ eklentisinin sanal IP adresini kaldırmasını engellemek için ağ arabiriminin yapılandırma dosyasını aşağıda gösterildiği gibi değiştirin (pacemaker VIP atamasını denetlemektir). Daha fazla bilgi için bkz. [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH erişimini etkinleştirme

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH erişimini etkinleştirme

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH erişimini etkinleştirme

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Azure sınır aracısına göre STONITH cihazı kullanılıyorsa, sınır aracıları paketini yükler.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Paket **sınırı-aracıların** yüklü sürümü, bir küme düğümlerinin yeniden oluşturulması gerekiyorsa Azure sınır Aracısı ile daha hızlı yük devretme süreleriyle faydalanmak için en az **4.4.0**  olmalıdır. Daha düşük bir sürüm çalıştırıyorsa paketi güncelleştirmenizi öneririz.  


1. **[A]** Azure Python SDK 'sını yüklemeyi 
   - SLES 12 SP4 veya SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - SLES 15 ve üzeri sürümlerde 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >Sürümünüze ve görüntü türüne bağlı olarak, Azure Python SDK 'yı yükleyebilmeniz için, işletim sistemi sürümünüz için genel bulut uzantısını etkinleştirmeniz gerekebilir.
   >Uzantısı, SUSEConnect---List-Extensions ' ı çalıştırarak da denetleyebilirsiniz.  
   >Azure çit Aracısı ile daha hızlı yük devretme süreleri elde etmek için:
   > - SLES 12 SP4 veya SLES 12 SP5, sürüm **4.6.2** veya daha yüksek paket Python-Azure-MGMT-COMPUTE  
   > - SLES 15 paketinin sürüm **4.6.2** veya daha yüksek bir sürümünü yükler Python **3**-Azure-MGMT-COMPUTE 

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin.

   >[!IMPORTANT]
   > Küme yapılandırmasında ana bilgisayar adları kullanılıyorsa, güvenilir ana bilgisayar adı çözümlemenin olması çok önemlidir. Adlar kullanılabilir değilse ve küme yük devretme gecikmelerine yol açacağından, küme iletişimi başarısız olur.
   > /Etc/hosts kullanmanın avantajı, kümenizin DNS 'den bağımsız hale gelmesi ve tek bir başarısızlık noktası olması olabilir.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** küme yüklemesi
- balıklama için SBD cihazları kullanılıyorsa
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- balıklama için SBD cihazları *kullanmıyorsanız*
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** kümeye düğüm Ekle

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster parolasını aynı parolayla değiştirin

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Corosync ayarlarını ayarlayın.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Değerler yoksa veya farklıysa, dosyaya aşağıdaki kalın içeriği ekleyin. Bellek Bakımı bakımını sağlamak için belirteci 30000 olarak değiştirdiğinizden emin olun. Daha fazla bilgi için Linux veya [Windows][virtual-machines-windows-maintenance] [için bu makaleye][virtual-machines-linux-maintenance] bakın.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Ardından Corosync hizmetini yeniden başlatın

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD için varsayılan Paceoluşturucu yapılandırması

Bu bölümdeki yapılandırma yalnızca SBD STONITH kullanılıyorsa geçerlidir.  

1. **[1]** bir STONITH cihazının kullanımını etkinleştirme ve çit gecikmesini ayarlama

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure çit Aracısı STONITH cihazı oluşturma

Belgelerinin bu bölümü, Azure sınır aracısına göre yalnızca STONITH kullanılıyorsa geçerlidir.
STONITH cihazı Microsoft Azure karşı yetkilendirmek için bir hizmet sorumlusu kullanır. Hizmet sorumlusu oluşturmak için bu adımları izleyin.

1. Şuraya gidin: <https://portal.azure.com>
1. Azure Active Directory dikey penceresini açın  
   Özellikler ' e gidin ve Dizin KIMLIĞINI yazın. Bu, **KIRACı kimliğidir**.
1. Uygulama kayıtları tıklayın
1. Yeni kayıt öğesine tıklayın
1. Bir ad girin, "yalnızca bu kuruluş dizinindeki hesaplar" ı seçin 
2. "Web" uygulama türünü seçin, bir oturum açma URL 'SI girin (örneğin, http: \/ /localhost) ve Ekle ' ye tıklayın.  
   Oturum açma URL 'SI kullanılmıyor ve geçerli bir URL olabilir
1. Sertifikalar ve gizlilikler ' ı seçin ve ardından yeni istemci parolası ' na tıklayın
1. Yeni anahtar için bir açıklama girin, "süresiz Expires" öğesini seçin ve Ekle ' ye tıklayın.
1. Değeri yazın. Hizmet sorumlusu için **parola** olarak kullanılır
1. Genel bakış'ı seçin. Uygulama KIMLIĞINI yazın. Hizmet sorumlusunun Kullanıcı adı olarak kullanılır

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** çit Aracısı için özel bir rol oluşturma

Hizmet sorumlusu, varsayılan olarak Azure kaynaklarınıza erişme izinlerine sahip değildir. Kümenin tüm sanal makinelerini başlatmak ve durdurmak (serbest bırakmak) için hizmet sorumlusu izinleri vermeniz gerekir. Zaten özel rolü oluşturmadıysanız, [PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) veya [Azure CLI](../../../role-based-access-control/custom-roles-cli.md) kullanarak oluşturabilirsiniz

Giriş dosyası için aşağıdaki içeriği kullanın. İçeriği aboneliklerinize uyarlamanız gerekir, c276fc76-9cd4-44c9-99a7-4fd71546436e ve e91d47c4-76f3-4271-a796-21b4ecfe3624 ' i aboneliğinizin kimlikleriyle değiştirin. Yalnızca bir aboneliğiniz varsa, Astifblescopes ikinci girişi kaldırın.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** hizmet sorumlusuna özel rol atama

Son bölümde oluşturulan "Linux çit Aracısı rolü" özel rolünü hizmet sorumlusuna atayın. Sahip rolünü artık kullanmayın!

1. Şuraya gidin: [https://portal.azure.com](https://portal.azure.com)
1. Tüm kaynaklar dikey penceresini açın
1. İlk küme düğümünün sanal makinesini seçin
1. Erişim denetimi (ıAM) öğesine tıklayın
1. Rol ataması Ekle ' ye tıklayın
1. "Linux çit Aracısı rolü" rolünü seçin
1. Yukarıda oluşturduğunuz uygulamanın adını girin
1. Kaydet’e tıklayın.

İkinci küme düğümü için yukarıdaki adımları tekrarlayın.

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH cihazlarını oluşturma

Sanal makineler için izinleri düzenledikten sonra, kümedeki STONITH cihazlarını yapılandırabilirsiniz.

> [!NOTE]
> ' Pcmk_host_map ' seçeneği yalnızca komutta gereklidir, ana bilgisayar adları ve Azure VM adları aynı DEĞILDIR. Eşlemeyi **ana bilgisayar adı: VM-adı** biçiminde belirtin.
> Komutun kalın bölümüne bakın.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group of the VM, tenant ID, service principal application ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>application ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> İzleme ve sınırlama işlemleri de serileştirilir. Sonuç olarak, daha uzun süre çalışan bir izleme işlemi ve eşzamanlı geçiş olayı varsa, zaten çalışan izleme işlemi nedeniyle küme yük devretmesi için gecikme yoktur.

> [!TIP]
>Azure sınır Aracısı, [Standart ILB kullanan VM 'ler Için genel uç nokta bağlantısı](./high-availability-guide-standard-load-balancer-outbound-connections.md)' nda açıklandığı gibi ortak uç noktalara giden bağlantı kurulmasını gerektirir.  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Azure zamanlanan olaylar için paceoluşturucu yapılandırması

Azure, [Zamanlanmış olaylar](../../linux/scheduled-events.md)sunar. Zamanlanan olaylar, meta veri hizmeti aracılığıyla sağlanır ve uygulamanın VM kapatması, VM yeniden dağıtımı vb. gibi olaylara hazırlanması için zaman sağlar. Resource Agent **[Azure](https://github.com/ClusterLabs/resource-agents/pull/1161)** olayları, zamanlanan Azure olayları için izler. Olaylar algılanırsa, aracı etkilenen VM 'deki tüm kaynakları durdurmayı dener ve bunları kümedeki başka bir düğüme taşır. Ek pacemaker kaynaklarının yapılandırılması gerektiğini elde etmek için. 

1. **[A]** **Azure-Events** aracısının paketinin zaten yüklü olduğundan ve güncel olduğundan emin olun. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** pacemaker 'da kaynakları yapılandırın. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Azure-Events Aracısı için pacemaker kaynaklarını yapılandırdıktan sonra, kümeyi bakım moduna aldığınızda veya bu moddan çıkarak, şu şekilde uyarı iletileri alabilirsiniz:  
     Uyarı: CIB-Bootstrap-Options: bilinmeyen öznitelik ' hostName_ <strong> hostname</strong>'  
     Uyarı: CIB-Bootstrap-Options: bilinmeyen öznitelik ' Azure-events_globalPullState '  
     Uyarı: CIB-Bootstrap-Options: bilinmeyen öznitelik ' hostName_ <strong>hostname</strong>'  
   > Bu uyarı iletileri yoksayılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde NFS için yüksek kullanılabilirlik][sles-nfs-guide]
* [SAP uygulamaları için SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik][sles-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
