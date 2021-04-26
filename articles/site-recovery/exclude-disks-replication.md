---
title: Azure Site Recovery ile diskleri çoğaltmadan dışlama
description: Azure Site Recovery ile Azure 'a diskleri çoğaltmanın dışında tutma.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008268"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Diskleri olağanüstü durumdan kurtarma dışında tut

Bu makalede, şirket içinde [Azure Site Recovery](site-recovery-overview.md)ile Azure arasında olağanüstü durum kurtarma sırasında disklerin nasıl dışlanacağını açıklar. Birçok nedenden dolayı diskleri çoğaltmadan dışlayabilirsiniz:

- Bu nedenle, hariç tutulan disk üzerinde önemli verilerin kaldırılması çoğaltılmaz.
- Tüketilen çoğaltma bant genişliğini veya hedef tarafı kaynakları iyileştirmek için.
- İhtiyaç duymamakta olduğunuz verileri çoğaltmayana depolama ve ağ kaynaklarını kaydetmek için.
- Azure VM 'Leri çoğaltma sınırlarına Site Recovery ulaştı.


## <a name="supported-scenarios"></a>Desteklenen senaryolar

Diskleri, tabloda özetlenen şekilde çoğaltmanın dışında bırakabilirsiniz.

**Azure-Azure arası** | **Vmware’den Azure’a** | **Hyper-V’den Azure’a** | **Fiziksel sunucudan Azure 'a**
--- | --- | --- | ---
Yes | Yes | Yes | Yes

## <a name="exclude-limitations"></a>Dışlama sınırlamaları

**Sınırlama** | **Azure VM’leri** | **VMware Sanal Makineleri** | **Hyper-V Sanal Makineleri**
--- | --- | ---
**Disk türleri** | Temel diskleri çoğaltmanın dışında bırakabilirsiniz.<br/><br/> İşletim sistemi disklerini veya dinamik diskleri dışlayamazsınız. Geçici diskler varsayılan olarak dışlanır. | Temel diskleri çoğaltmanın dışında bırakabilirsiniz.<br/><br/> İşletim sistemi disklerini veya dinamik diskleri dışlayamazsınız. | Temel diskleri çoğaltmanın dışında bırakabilirsiniz.<br/><br/> İşletim sistemi diskleri dışarıda tutulamaz. Dinamik diskleri dışarıda tutmamanızı öneririz. Site Recovery, Konuk VM 'de hangi VHS 'ın temel veya dinamik olduğunu tanımlayamıyor. Bağımlı dinamik birim disklerinin tümü dışlanmazsa, korumalı dinamik disk yük devretme VM 'sinde başarısız olan bir disk haline gelir ve diskteki verilere erişilemez.
**Disk çoğaltılıyor** | Çoğaltılan bir diski dışlayamazsınız.<br/><br/> VM için çoğaltmayı devre dışı bırakıp yeniden etkinleştirin. |  Çoğaltılan bir diski dışlayamazsınız. |  Çoğaltılan bir diski dışlayamazsınız.
**Mobility hizmeti (VMware)** | İlgili değil | Diskleri yalnızca Mobility hizmetinin yüklü olduğu VM 'lerde hariç bırakabilirsiniz.<br/><br/> Bu, Mobility hizmetini diskleri dışlamak istediğiniz VM 'Lere el ile yüklemek zorunda olduğunuz anlamına gelir. İtme yükleme mekanizmasını, yalnızca çoğaltma etkinleştirildikten sonra Mobility hizmetini yüklediği için kullanamazsınız. | İlgili değil.
**Ekle/Kaldır** | Yönetilen diskler ile, çoğaltma özellikli Azure VM 'lerine yönetilen diskler ekleyebilirsiniz. Çoğaltma özellikli Azure VM 'lerinde disk kaldıramazsınız. | Çoğaltma etkinleştirildikten sonra disk ekleyemez veya kaldıramazsınız. Diski eklemek için çoğaltmayı devre dışı bırakıp yeniden etkinleştirin. | Çoğaltma etkinleştirildikten sonra disk ekleyemez veya kaldıramazsınız. Çoğaltmayı devre dışı bırakıp yeniden etkinleştirin.
**Yük devretme** | Uygulamanın dışlandığı bir diske ihtiyacı varsa, yük devretmeden sonra, çoğaltılan uygulamanın çalışabilmesi için diski el ile oluşturmanız gerekir.<br/><br/> Alternatif olarak, Azure Otomasyonu 'nu bir kurtarma planına tümleştirerek VM yük devretmesi sırasında diski oluşturabilirsiniz. | Bir uygulamanın ihtiyacı olan bir diski hariç tutdıysanız, yük devretmeden sonra Azure 'da el ile oluşturun. | Bir uygulamanın ihtiyacı olan bir diski hariç tutdıysanız, yük devretmeden sonra Azure 'da el ile oluşturun.
**Şirket içi yeniden çalışma-el ile oluşturulan diskler** | İlgili değil | **Windows VM 'leri**: Azure 'da el ile oluşturulan diskler yeniden başarısız oldu. Örneğin, üç disk üzerinde yük devretmek ve doğrudan bir Azure VM üzerinde iki disk oluşturursanız, yalnızca yük devredilen üç disk yeniden başarısız olur.<br/><br/> **Linux VM 'leri**: Azure 'da el ile oluşturulan diskler yeniden başarısız oldu. Örneğin, üç disk üzerinde yük devretmek ve bir Azure VM 'de iki disk oluşturursanız, tüm beş yeniden başarısız olur. El ile oluşturulmuş diskleri, yeniden çalışmanın dışında tutamazsınız. | Azure 'da el ile oluşturulan diskler yeniden başarısız oldu. Örneğin, üç disk üzerinde yük devretmek ve doğrudan bir Azure VM üzerinde iki disk oluşturursanız, yük devredilen yalnızca üç disk yeniden başarısız olur.
**Şirket içi yeniden çalışma-dışlanan diskler** | İlgili değil | Özgün makineye geri döndüğünüzde, yeniden çalışma VM disk yapılandırması dışlanan diskleri içermez. VMware 'den Azure 'a çoğaltmaya dışlanan diskler yeniden çalışma VM 'sinde kullanılamaz. | Yeniden çalışma özgün Hyper-V konumuna olduğunda, yeniden çalışma VM disk yapılandırması orijinal kaynak VM diskiyle aynı kalır. Hyper-V sitesinden Azure 'a çoğaltmaya dışlanan diskler yeniden çalışma VM 'sinde bulunabilir.



## <a name="typical-scenarios"></a>Tipik senaryolar

Dışlama için harika aday olan veri dalgalanmasına örnek olarak, bir sayfalama dosyasına (pagefile.sys) yazma ve Microsoft SQL Server tempdb dosyasına yazma dahildir. İş yüküne ve depolama alt sistemine bağlı olarak, disk belleği ve tempdb dosyaları önemli miktarda dalgalanma kaydedebilir. Bu tür verilerin Azure 'a çoğaltılması Kaynak yoğunluklu bir işlemdir.

- Hem işletim sistemini hem de disk belleği dosyasını içeren tek bir sanal diskle bir VM için çoğaltmayı iyileştirmek üzere şunları yapabilirsiniz:
    1. Tek sanal diski iki sanal diske bölün. Bir sanal diskte işletim sistemi ve diğerinde disk belleği dosyası vardır.
    2. Disk belleği dosyasını çoğaltmanın dışında tutun.

- Microsoft SQL Server tempdb dosyasını ve sistem veritabanı dosyasını içeren bir disk için çoğaltmayı iyileştirmek üzere şunları yapabilirsiniz:
    1. Sistem veritabanını ve tempdb dosyasını iki farklı diskte tutabilirsiniz.
    2. Tempdb diskini çoğaltmanın dışında tutabilirsiniz.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Örnek 1: SQL Server tempdb diskini dışlama

Tempdb 'yi dışlamak istediğimiz bir kaynak SQL Server Windows VM-* * SalesDB * * * için disk dışlama, yük devretme ve yük devretmeyi nasıl işleyeceğinizi inceleyelim. 

### <a name="exclude-disks-from-replication"></a>Diskleri çoğaltmanın dışında tutma

Bu disklere, kaynak Windows VM SalesDB 'de ihtiyacımız var.

**Disk adı** | **Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | İşletim sistemi diski.
DB-Disk1| Disk1 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1.
DB-Disk2 (Disk, korumanın dışında tutuldu) | Disk2 | E:\ | Geçici dosyalar.
DB-Disk3 (Disk, korumanın dışında tutuldu) | Disk3 | F:\ | SQL tempdb veritabanı.<br/><br/> Klasör yolu-F:\MSSQL\Data\. Yük devretmeden önce klasör yolunu bir yere göz önüne alın.
DB-Disk4 | Disk4 |G:\ | Kullanıcı Veritabanı2

1. SalesDB VM için çoğaltmayı etkinleştirdik.
2. Bu disklerdeki veri dalgalanması geçici olduğundan, disk2 ve disk3 çoğaltma işleminden hariç tutuyoruz. 


### <a name="handle-disks-during-failover"></a>Yük devretme sırasında diskleri işleme

Diskler çoğaltılmadığından Azure 'a yük devretmek için bu diskler, yük devretmeden sonra oluşturulan Azure VM 'de mevcut değildir. Azure VM 'nin bu tabloda özetlenen diskleri vardır.

**Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | ---
Disk0 | C:\ | İşletim sistemi diski.
Disk1 | E:\ | Geçici depolama<br/><br/>Azure bu diski ekler. Disk2 ve disk3, çoğaltmadan dışlandığından, E: kullanılabilir listedeki ilk sürücü harftir. Azure, geçici depolama birimine E: harfini atar. Çoğaltılan disklerin diğer sürücü harfleri aynı kalır.
Disk2 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1
Disk3 | G:\ | Kullanıcı Veritabanı2

Bizim örneğimizde, SQL tempdb diski disk3 'dan dışlanmıştır ve Azure VM 'de mevcut olmadığından, SQL hizmeti durdurulmuş durumda ve F:\MSSQL\Data yoluna ihtiyaç duyuyor. Bu yolu birkaç yolla oluşturabilirsiniz: 

- Yük devretmeden sonra yeni bir disk ekleyin ve tempdb klasör yolunu atayın.
- tempdb klasör yolu için var olan geçici bir depolama diski kullanın.

#### <a name="add-a-new-disk-after-failover"></a>Yük devretmeden sonra yeni bir disk Ekle

1. Yük devretme öncesinde SQL tempdb.mdf ve tempdb.ldf yollarını yazın.
2. Azure portal, yük devretme Azure VM 'sine yeni bir disk ekleyin. Disk, kaynak SQL tempdb diskiyle (disk3) aynı boyutta (veya daha büyük) olmalıdır.
3. Azure VM 'de oturum açın.
4. Disk yönetimi (diskmgmt.msc) konsolundan yeni eklenen diski başlatıp biçimlendirin.
5. SQL tempdb diski tarafından kullanılan aynı sürücü harfini ata (F:)
6. F: biriminde bir tempdb klasörü (F:\MSSQL\Data) oluşturun.
7. Hizmet konsolundan SQL hizmetini başlatın.

#### <a name="use-an-existing-temporary-storage-disk"></a>Var olan geçici depolama diskini kullan 

1. Bir komut istemi açın.
2. Komut isteminden kurtarma modunda SQL Server çalıştırın.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. tempdb yolunu yeni yola değiştirmek için aşağıdaki sqlcmd’yi çalıştırın.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Microsoft SQL Server hizmetini durdurun.

    ```console
    Net stop MSSQLSERVER
    ```

5. Microsoft SQL Server hizmetini başlatın.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM 'Leri: özgün konuma yeniden çalışma sırasında diskler

Şimdi, özgün şirket içi konumunuza geri döndüğünüzde VMware VM 'lerinde disklerin nasıl işleneceğini görelim.

- **Azure 'da oluşturulan diskler**: örneğimiz bir Windows sanal makinesi kullandığından, Azure 'da el ile oluşturduğunuz diskler, yeniden bağlanma veya bir VM 'yi yeniden koruma işlemleri yaptığınızda sitenize geri çoğaltılmaz.
- **Azure 'Da geçici depolama diski**: geçici depolama diski, şirket içi konaklara geri çoğaltılmaz.
- **Çıkarılan diskler**: VMware 'den Azure 'a çoğaltma dışında bırakılan diskler, yeniden çalışma sonrasında ŞIRKET içi VM 'de kullanılamaz.

VMware VM 'lerini özgün konuma geri yüklemeden önce, Azure VM disk ayarları aşağıdaki gibidir.

**Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | ---
Disk0 | C:\ | İşletim sistemi diski.
Disk1 | E:\ | Geçici depolama.
Disk2 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1.
Disk3 | G:\ | Kullanıcı Veritabanı2.

Yeniden çalışma sonrasında, özgün konumdaki VMware VM 'si tabloda özetlenen diskler içerir.

**Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | ---
Disk0 | C:\ | İşletim sistemi diski.
Disk1 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1.
Disk2 | G:\ | Kullanıcı Veritabanı2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V VM 'Leri: özgün konuma yeniden çalışma sırasında diskler

Şimdi, orijinal şirket içi konumunuza geri döndüğünüzde Hyper-V VM 'lerinde disklerin nasıl işleneceğini görelim.

- **Azure 'da oluşturulan diskler**: Azure 'da el ile oluşturduğunuz diskler, BIR sanal makineyi yeniden uyguladığınızda veya yeniden koruduğunuzda sitenize geri çoğaltılmaz.
- **Azure 'Da geçici depolama diski**: geçici depolama diski, şirket içi konaklara geri çoğaltılmaz.
- **Çıkarılan diskler**: yeniden çalışma sonrasında VM disk yapılandırması, özgün VM disk yapılandırmasıyla aynıdır. Hyper-V ' d e çoğaltmadan Azure 'a çoğaltmanın dışında tutulan diskler yeniden çalışma VM 'sinde bulunabilir.

Hyper-V VM 'lerini özgün konuma geri yüklemeden önce, Azure VM disk ayarları aşağıdaki gibidir.

**Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | ---
Disk0 | C:\ | İşletim sistemi diski.
Disk1 | E:\ | Geçici depolama.
Disk2 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1.
Disk3 | G:\ | Kullanıcı Veritabanı2.

Azure 'dan şirket içi Hyper-V ' d e planlı yük devretme (yeniden çalışma) sonrasında, özgün konumdaki Hyper-V VM 'si tabloda özetlenen diskler içerir.

**Disk Adı** | **Konuk işletim sistemi disk#** | **Sürücü harfi** | **Disk veri türü**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | İşletim sistemi diski.
DB-Disk1 | Disk1 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1.
DB-Disk2 (Dışlanan disk) | Disk2 | E:\ | Geçici dosyalar.
DB-Disk3 (Dışlanan disk) | Disk3 | F:\ | SQL tempdb veritabanı<br/><br/> Klasör yolu (F:\MSSQL\Data \) .
DB-Disk4 | Disk4 | G:\ | Kullanıcı Veritabanı2


## <a name="example-2-exclude-the-paging-file-disk"></a>Örnek 2: disk belleği dosyası diskini hariç tutma

Bir kaynak Windows sanal makinesi için disk dışlama, yük devretme ve yük devretme işlemlerinin nasıl yapılacağını, her iki sürücüde de pagefile.sys dosya diskini dışlamak istiyoruz ve alternatif bir sürücü.


### <a name="paging-file-on-the-d-drive"></a>D sürücüsündeki disk belleği dosyası

Kaynak VM 'de bu disklere ihtiyacımız var.

**Disk adı** | **Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | İşletim sistemi diski
DB-Disk1 (çoğaltmadan hariç tut) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Kaynak VM 'deki disk belleği dosyası ayarları şu şekildedir:

![Disk belleği dosyası boyutu (MB) olan 3000-7000 ' i gösteren D: Drive [pagefile Volume] satırı ile sanal bellek iletişim kutusunun ekran görüntüsü.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. VM için çoğaltmayı etkinleştirdik.
2. DB-Disk1 çoğaltmadan hariç tutuyoruz.

#### <a name="disks-after-failover"></a>Yük devretmeden sonra diskler

Yük devretmeden sonra Azure VM, tabloda özetlenen diskler içerir.

**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | İşletim sistemi diski
DB-Disk1 | Disk1 | D:\ | Geçici depolama/pagefile.sys <br/><br/> DB-Disk1 (D:) çıkarıldı, D: kullanılabilir listedeki ilk sürücü harftir.<br/><br/> Azure, geçici depolama birimine D: harfini atar.<br/><br/> D: kullanılabilir olduğundan, VM disk belleği dosyası ayarı aynı kalır.
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Azure VM 'deki disk belleği dosyası ayarları şu şekildedir:

![Azure sanal makinesindeki disk belleği dosyası ayarları](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Başka bir sürücüdeki disk belleği dosyası (D:)

Disk belleği dosyasının D sürücüsünde olmadığı örneğe bakalım.  

Kaynak VM 'de bu disklere ihtiyacımız var.

**Disk adı** | **Konuk işletim sistemi diski** | **Sürücü harfi** | **Disk veri türü**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | İşletim sistemi diski
DB-Disk1 (çoğaltmadan hariç tut) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Şirket içi VM 'deki disk belleği dosyası ayarları şu şekildedir:

![Şirket içi sanal makinedeki disk belleği dosyası ayarları](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. VM için çoğaltmayı etkinleştirdik.
2. DB-Disk1 çoğaltmadan hariç tutuyoruz.

#### <a name="disks-after-failover"></a>Yük devretmeden sonra diskler

Yük devretmeden sonra Azure VM, tabloda özetlenen diskler içerir.

**Disk adı** | **Konuk işletim sistemi disk#** | **Sürücü harfi** | **Disk veri türü**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | İşletim sistemi diski
DB-Disk1 | Disk1 | D:\ | Geçici depolama<br/><br/> D: kullanılabilir sürücü harfleri listesindeki ilk harf olduğundan, Azure geçici depolama birimine D: harfini atar.<br/><br/> Tüm çoğaltılan diskler için, sürücü harfi aynı kalır.<br/><br/> G: diski kullanılamadığından, sistem disk belleği dosyası için C: sürücüsünü kullanır.
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Azure VM 'deki disk belleği dosyası ayarları şu şekildedir:

!["Sistem tarafından yönetilen" bir disk belleği dosyası boyutu ayarını gösteren, C: sürücü satırı vurgulanmış olan sanal bellek iletişim kutusunun ekran görüntüsü.](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Sonraki adımlar

- Geçici depolama diski için yönergeler hakkında daha fazla bilgi edinin:
    - SQL Server TempDB ve arabellek havuzu uzantıları depolamak için Azure VM 'lerinde SSDs kullanma [hakkında bilgi edinin](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
    - Azure VM 'lerinde SQL Server yönelik performans en iyi yöntemlerini [gözden geçirin](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) .
- Dağıtımınız ayarlandıktan ve çalışmaya başladıktan sonra farklı türdeki yük devretmeler hakkında [daha fazla bilgi edinebilirsiniz](failover-failback-overview.md).
