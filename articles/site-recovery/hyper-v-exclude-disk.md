---
title: Azure Site Recovery ile olağanüstü durum kurtarma 'da diskleri çoğaltmanın dışında tutma
description: Azure 'a olağanüstü durum kurtarma sırasında VM disklerinin çoğaltma işleminden nasıl dışlanacağını açıklar.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mayg
ms.openlocfilehash: 12304067e1a92559c2313fd7382f271249a8c784
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961448"
---
# <a name="exclude-disks-from-replication"></a>Diskleri çoğaltmanın dışında tutma
Bu makalede, disklerin çoğaltmanın dışında nasıl tutulacağı açıklanmaktadır. Bu dışında tutma, kullanılan çoğaltma bant genişliğini iyileştirebilir veya bu gibi disklerin kullandığı hedef tarafı kaynakları iyileştirebilir.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

**Özelliği** | **Vmware’den Azure’a** | **Hyper-V'den Azure'a** | **Azure'dan Azure'a**| **Hyper-V'den Hyper-V'ye** 
--|--|--|--|--
Diski hariç tutma | Yes | Yes | Hayır | Hayır

## <a name="why-exclude-disks-from-replication"></a>Diskleri çoğaltmanın dışında tutma nedenleri nelerdir?
Disklerin çoğaltmanın dışında tutulması, çoğu zaman aşağıdaki nedenlerden dolayı gereklidir:

- Hariç tutulan diskteki değişen veriler önemli değildir veya çoğaltılmaları gerekmez.

- Bu dalgalanmayı çoğaltmayarak depolama ve ağ kaynaklarından tasarruf sağlamak istersiniz.

## <a name="what-are-the-typical-scenarios"></a>Tipik senaryolar nelerdir?
Dışarıda tutmak için çok iyi adaylar olan belirli veri değişim sıklığı örnekleri tanımlayabilirsiniz. Örnekler, disk belleği dosyasına (pagefile.sys) yazma ve Microsoft SQL Server tempdb dosyasına yazmayı içerebilir. İş yüküne ve depolama alt sistemine bağlı olarak, disk belleği dosyasında önemli miktarda dalgalanma kaydedebilir. Ancak bu verilerin birincil siteden Azure'a çoğaltılması yoğun bir kaynak kullanımına neden olacaktır. Bu nedenle, hem işletim sistemi hem de disk belleği dosyası bulunan tek bir sanal diske sahip bir sanal makinenin çoğaltılmasını iyileştirmek üzere aşağıdaki adımları kullanabilirsiniz:

1. Tek sanal diski iki sanal diske bölün. Bir sanal diskte işletim sistemi ve diğerinde disk belleği dosyası vardır.
2. Disk belleği dosyasını çoğaltmanın dışında tutun.

Benzer şekilde, Microsoft SQL Server tempdb dosyasına ve sistem veritabanı dosyasına sahip bir diski en iyi duruma getirmek için aşağıdaki adımlardan yararlanabilirsiniz:

1. Sistem veritabanını ve tempdb dosyasını iki farklı diskte tutabilirsiniz.
2. Tempdb diskini çoğaltmanın dışında tutabilirsiniz.

## <a name="how-to-exclude-disks"></a>Diskleri dışlama
Azure Site Recovery portalından bir sanal makineyi korumak için [Çoğaltmayı etkinleştir](site-recovery-hyper-v-site-to-azure.md) iş akışını izleyin. İş akışının dördüncü adımında, diskleri çoğaltmanın dışında tutmak için **DISK TO REPLICATE** sütununu kullanın. Varsayılan olarak, tüm diskler çoğaltma için seçilir. Çoğaltmanın dışında tutmak istediğiniz disklerin onay kutusunu temizleyin ve ardından çoğaltmayı etkinleştirme adımlarını tamamlayın.

![Diskleri çoğaltmanın dışında tutun ve Hyper-V’den Azure’a yeniden çalışma için çoğaltmayı etkinleştirin](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Yalnızca temel diskler çoğaltma dışında bırakılabilir. İşletim sistemi diskleri dışarıda tutulamaz. Dinamik diskleri dışarıda tutmamanızı öneririz. Azure Site Recovery, konuk sanal makinede hangi sanal sabit diskin (VHD) temel veya dinamik olduğunu tanımlayamaz.  Bağımlı dinamik birim disklerin tümü dışlanmazsa, korumalı dinamik disk yük devredilen bir sanal makinede hatalı disk haline gelir ve bu diskteki verilere erişim sağlanamaz.
> * Çoğaltmayı etkinleştirdikten sonra, diskleri çoğaltma ekleyemez veya kaldıramazsınız. Disk eklemek veya dışarıda tutmak istiyorsanız, sanal makine için korumayı devre dışı bırakmanız ve ardından yeniden etkinleştirmeniz gerekir.
> * Bir uygulamanın çalışması için gerekli olan bir diski hariç tutarsanız, Azure'a yük devretme gerçekleştirildikten sonra, çoğaltılan uygulamanın çalışabilmesi için diski Azure'da el ile oluşturmanız gerekir. Alternatif olarak, makinenin yük devri esnasında diski oluşturmak için Azure otomasyonunu bir kurtarma planıyla tümleştirebilirsiniz.
> * Azure'da elle oluşturduğunuz diskler yeniden çalışır duruma getirilmez. Örneğin, üç disk için yük devretme gerçekleştirir ve iki diski doğrudan Azure Sanal Makinelerinde oluşturursanız, yalnızca yük devretme gerçekleştirilen üç disk Azure'dan Hyper-V'ye yeniden çalışır hale getirilir. Yeniden çalışır hale getirilirken veya Hyper-V’den Azure’a ters çoğaltmada elle oluşturulmuş diskler dahil edilemez.

## <a name="end-to-end-scenarios-of-exclude-disks"></a>Disk dışarıda bırakmaya ilişkin uçtan uca senaryolar
Disk dışarıda tutma özelliğini daha iyi anlamak için iki senaryoyu düşünelim:

- SQL Server tempdb diski
- Disk belleği dosyası (pagefile.sys) diski

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Örnek 1: SQL Server tempdb diskini dışlama
Dışlanabilecek bir tempdb’si olan bir SQL Server sanal makinesi düşünelim.

Sanal disk adı SalesDB şeklindedir.

Kaynak sanal makinedeki diskler aşağıdaki gibidir:


**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | İşletim sistemi diski
DB-Disk1| Disk1 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1
DB-Disk2 (Disk, korumanın dışında tutuldu) | Disk2 | E:\ | Geçici dosyalar
DB-Disk3 (Disk, korumanın dışında tutuldu) | Disk3 | F:\ | SQL tempdb veritabanı (klasör yolu (F:\MSSQL\Data\) <br /> <br />Yük devretmeden önce klasör yolunu yazın.
DB-Disk4 | Disk4 |G:\ |Kullanıcı Veritabanı2

Sanal makinenin iki diskindeki veri değişim sıklığı geçici olduğundan, SalesDB sanal makinesini korurken Disk2 ve Disk3’ü çoğaltmanın dışında tutun. Azure Site Recovery bu diskleri çoğaltmaz. Yük devretmede, bu diskler Azure’da yük devretme sanal makinesinde mevcut olmaz.

Yük devretme sonrasında Azure sanal makinesindeki diskler aşağıdaki gibidir:

**Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | ---
DISK0 | C:\ | İşletim sistemi diski
Disk1 | E:\ | Geçici depolama<br /> <br />Azure bu diski ekler ve kullanılabilir ilk sürücü harfini atar.
Disk2 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1
Disk3 | G:\ | Kullanıcı Veritabanı2

Disk2 ve Disk3, SalesDB sanal makinesinin dışında tutulduğundan, kullanılabilir sürücü harfleri listesindeki ilk harf E: olur. Azure, geçici depolama birimine E: harfini atar. Tüm çoğaltılan diskler için, sürücü harfleri aynı kalır.

SQL tempdb diski olan Disk3 (tempdb klasör yolu F:\MSSQL\Data\), çoğaltmadan dışlanmıştır. Disk yük devretme sanal makinesinde kullanılabilir değildir. Sonuç olarak, SQL hizmeti durdurulmuş durumdadır ve F:\MSSQL\Veri yolu gerekir.

Bu yolu oluşturmanın iki yöntemi vardır:

- Yeni bir disk ekleyin ve tempdb klasör yolunu atayın.
- tempdb klasör yolu için var olan geçici bir depolama diski kullanın.

### <a name="add-a-new-disk"></a>Yeni bir disk ekleme:

1. Yük devretme öncesinde SQL tempdb.mdf ve tempdb.ldf yollarını yazın.
2. Azure portalında, yük devretme sanal makinesine, kaynak SQL tempdb diskiyle (Disk3) aynı boyutta veya daha büyük yeni bir disk ekleyin.
3. Azure sanal makinesinde oturum açın. Disk yönetimi (diskmgmt.msc) konsolundan yeni eklenen diski başlatıp biçimlendirin.
4. SQL tempdb diski tarafından kullanılan sürücü harfinin aynısını (F:) atayın.
5. F: biriminde bir tempdb klasörü (F:\MSSQL\Data) oluşturun.
6. Hizmet konsolundan SQL hizmetini başlatın.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>SQL tempdb klasör yolu için var olan geçici bir depolama diski kullanın:

1. Bir komut istemi açın.
2. Komut isteminden kurtarma modunda SQL Server çalıştırın.

        Net start MSSQLSERVER /f / T3608

3. tempdb yolunu yeni yola değiştirmek için aşağıdaki sqlcmd’yi çalıştırın.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Microsoft SQL Server hizmetini durdurun.

        Net stop MSSQLSERVER
5. Microsoft SQL Server hizmetini başlatın.

        Net start MSSQLSERVER

Geçici depolama diski için aşağıdaki Azure kılavuzuna bakın:

* [SQL Server TempDB’yi ve Arabellek Havuzu Uzantılarını depolamak için Azure sanal makinelerde SSD kullanma](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Azure Sanal Makineler’de SQL Server için performansa yönelik en iyi uygulamalar](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Yeniden çalışır hale getirme (Azure'dan şirket içi bir ana bilgisayara)
Şimdi, Azure'dan şirket içi Hyper-V ana bilgisayarınıza yük devretme yaptığınızda çoğaltılan disklere bakalım. Azure'da elle oluşturduğunuz diskler çoğaltılmaz. Örneğin, üç disk için yük devretme gerçekleştirir ve iki diski doğrudan Azure Sanal Makinelerinde oluşturursanız, yalnızca yük devretme gerçekleştirilen üç disk yeniden çalışır hale getirilir. Elle oluşturduğunuz diskleri, yeniden çalışır duruma getirmeye veya şirket içinden Azure'a yeniden koruma uygulama işlemlerine dahil edemezsiniz. Ayrıca, geçici depolama diskini şirket içi ana bilgisayarlarına da çoğaltmaz.

### <a name="failback-to-original-location-recovery"></a>Özgün konum kurtarması için yeniden çalışma

Önceki örnekte, Azure sanal makinesinin disk yapılandırması aşağıdaki gibidir:

**Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | ---
DISK0 | C:\ | İşletim sistemi diski
Disk1 | E:\ | Geçici depolama<br /> <br />Azure bu diski ekler ve kullanılabilir ilk sürücü harfini atar.
Disk2 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1
Disk3 | G:\ | Kullanıcı Veritabanı2

Yeniden çalışma özgün konum için olduğunda, yeniden çalışma sanal makinesi disk yapılandırması, Hyper-V’ye ait özgün sanal makine disk yapılandırması ile aynı kalır. Hyper-V sitesinden Azure'a çoğaltmanın dışında tutulan diskler, yeniden çalışır hale getirme sanal makinesinde mevcut olacaktır.

Azure'dan şirket içi Hyper-V’ye planlı yük devretmenin ardından, Hyper-V sanal makinesindeki (özgün konum) diskler aşağıdaki gibidir:

**Disk Adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | İşletim sistemi diski
DB-Disk1 | Disk1 | D:\ | SQL sistem veritabanı ve Kullanıcı Veritabanı1
DB-Disk2 (Dışlanan disk) | Disk2 | E:\ | Geçici dosyalar
DB-Disk3 (Dışlanan disk) | Disk3 | F:\ | SQL tempdb veritabanı (klasör yolu (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Kullanıcı Veritabanı2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>Örnek 2: Disk belleği dosyası (pagefile.sys) diskini dışlama

Dışarıda tutulabilecek bir disk belleği dosyası diski olan bir sanal makine düşünelim.
İki durum vardır.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Durum 1: Disk belleği dosyası D: sürücüsünde yapılandırılmıştır
Disk yapılandırması aşağıdaki gibidir:

**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | İşletim sistemi diski
DB-Disk1 (Disk, korumanın dışında tutuldu) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Kaynak sanal makinedeki disk belleği dosyası ayarları şunlardır:

![Kaynak sanal makinedeki disk belleği dosyası ayarları](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

Sanal makinenin Hyper-V’den Azure’a yük devretmesi yapıldıktan sonra, Azure sanal makinesindeki diskler aşağıdaki gibi olur:

**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | İşletim sistemi diski
DB-Disk1 | Disk1 | D:\ | Geçici depolama<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Disk1 (D:) dışarıda tutulduğundan, D: kullanılabilir listedeki ilk sürücü harfidir. Azure, geçici depolama birimine D: harfini atar. D: Azure sanal makinesinde kullanılabilir olduğundan, sanal makinenin disk belleği dosyası ayarı aynı kalır.

Azure sanal makinesindeki disk belleği dosyası ayarları şunlardır:

![Azure sanal makinesindeki disk belleği dosyası ayarları](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Durum 2: Disk belleği dosyası başka bir sürücüde (D: sürücüsü dışında) yapılandırılmıştır

Kaynak sanal makine disk yapılandırması şöyledir:

**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | İşletim sistemi diski
DB-Disk1 (Disk, korumanın dışında tutuldu) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

Şirket içi sanal makinedeki disk belleği dosyası ayarları şunlardır:

![Şirket içi sanal makinedeki disk belleği dosyası ayarları](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Sanal makinenin Hyper-V’den Azure’a yük devretmesi yapıldıktan sonra, Azure sanal makinesindeki diskler aşağıdaki gibi olur:

**Disk adı** | **Konuk işletim sistemi disk no.** | **Sürücü harfi** | **Diskteki veri türü**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |İşletim sistemi diski
DB-Disk1 | Disk1 | D:\ | Geçici depolama<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Kullanıcı verileri 1
DB-Disk3 | Disk3 | F:\ | Kullanıcı verileri 2

D: kullanılabilir sürücü harfleri listesindeki ilk harf olduğundan, Azure geçici depolama birimine D: harfini atar. Tüm çoğaltılan diskler için, sürücü harfi aynı kalır. G: disk kullanılabilir olmadığından, sistem disk belleği dosyası için C: sürücüsünü kullanır.

Azure sanal makinesindeki disk belleği dosyası ayarları şunlardır:

![Azure sanal makinesindeki disk belleği dosyası ayarları](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Sonraki adımlar
Dağıtımınız ayarlandıktan ve çalışmaya başladıktan sonra farklı türdeki yük devretmeler hakkında [daha fazla bilgi edinebilirsiniz](site-recovery-failover.md).
