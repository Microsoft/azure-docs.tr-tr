---
title: Azure VMware çözümü-Azure Data Box kullanarak geçiş
description: Verileri Azure VMware çözümüne toplu olarak geçirmek için Azure Data Box kullanma.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552899"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Azure Data Box kullanarak verileri Azure VMware çözümüne geçirme

Microsoft Azure Data Box bulut çözümü, Azure 'da terabaytlık (TBs) verileri hızlı, ucuz ve güvenilir bir şekilde göndermenize olanak tanır. Site özel bir Data Box depolama cihazı gönderilerek güvenli veri aktarımı hızlandırılır. Her depolama cihazının maksimum kullanılabilir depolama kapasitesi 80 TB 'tır ve bölgesel bir taşıyıcı tarafından veri merkezinize aktarılmıştır. Cihazda, aktarım sırasında verilerinizi korumak ve güvenliğini sağlamak için bir Rugged büyük/küçük harfe sahip olabilirsiniz.

Data Box kullanarak, VMware verilerinizi özel bulutunuzda toplu olarak geçirebilirsiniz. Şirket içi VMware vSphere ortamınızdan veriler ağ dosya sistemi (NFS) protokolü üzerinden Data Box kopyalanır. Toplu veri geçişi, sanal makinelerin, yapılandırmanın ve ilişkili verilerin bir zaman içindeki bir kopyasının Data Box ve Azure 'a el ile teslim edilmesi gerekir.

Bu makalede şunları öğreneceksiniz:

* Data Box ayarlanıyor.
* Şirket içi VMware ortamından Data Box, NFS aracılığıyla veri kopyalama.
* Data Box dönüşü için hazırlanıyor.
* Blob verileri Azure VMware çözümüne kopyalanmaya için hazırlanıyor.
* Verileri Azure 'dan özel buluta kopyalama.

## <a name="scenarios"></a>Senaryolar

Toplu veri geçişi için aşağıdaki senaryolarda Data Box kullanın:

* Şirket içinden Azure VMware çözümüne büyük miktarda veri geçirmek için. Bu yöntem, ağ üzerinden temel ve eşitlemeler farklarını belirler.
* Kapalı olan çok sayıda sanal makineyi geçirmek için (soğuk sanal makineler).
* Geliştirme ve test ortamlarını ayarlamak için sanal makine verilerini geçirme.
* Çok sayıda sanal makine şablonu, ISO dosyası ve sanal makine diski geçirmek için.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure portal aracılığıyla önkoşulları ve [sıra Data Box](../databox/data-box-deploy-ordered.md) denetleyin. Sipariş işlemi sırasında, blob depolamayı sağlayan bir depolama hesabı seçmeniz gerekir. Data Box cihazı aldıktan sonra şirket içi ağınıza bağlayın ve cihazı vSphere yönetim ağınızdan erişilebilen bir IP adresi ile [ayarlayın](../databox/data-box-deploy-set-up.md) .

* Azure VMware çözümünüzün sağlandığı bölgede bir sanal ağ ve depolama hesabı oluşturun.

* [ExpressRoute kullanarak Azure sanal ağını CloudSimple 'A bağlama](virtual-network-connection.md)bölümündeki adımları izleyerek, özel bulutınızdan depolama hesabının oluşturulduğu sanal ağa bir [Azure sanal ağ bağlantısı](cloudsimple-azure-network-connection.md) oluşturun.

## <a name="set-up-data-box-for-nfs"></a>NFS Data Box ayarlama

Öğretici: kablo ' nin "cihazınıza bağlanma" bölümündeki adımları izleyerek Data Box yerel Web Kullanıcı arabirimine bağlanın [ve Azure Data Box bağlayın](../databox/data-box-deploy-set-up.md).  NFS istemcilerine erişime izin vermek için Data Box yapılandırın:

1. Yerel Web Kullanıcı arabiriminde **Bağlan ve Kopyala** sayfasına gidin. **NFS ayarları**altında **NFS istemci erişimi**' ni seçin. 

    ![NFS istemci erişimini yapılandırma 1](media/nfs-client-access.png)

2. VMware ESXi konaklarının IP adresini girin ve **Ekle**' yi seçin. Bu adımı yineleyerek vSphere kümenizdeki tüm konaklar için erişimi yapılandırabilirsiniz. **Tamam**’ı seçin.

    ![NFS istemci erişimini yapılandırma 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları doğrudan depolama hesabındaki *kök* klasöre kopyalayamazsınız.

Blok blobu ve sayfa blobu paylaşımlarının altında birinci düzeydeki varlıklar kapsayıcılar, ikinci düzeydeki varlıklar ise bloblardır. Azure dosyaları için paylaşımlar altında, ilk düzey varlıklar paylaşımlardır ve ikinci düzey varlıklar dosyalardır.

Aşağıdaki tabloda, Data Box üzerindeki paylaşımların UNC yolu ve verilerin karşıya yüklendiği Azure Depolama yolu URL'si gösterilmektedir. Son Azure depolama yolu URL'si, UNC paylaşım yolundan türetilebilir.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Blok blobları | <li>Paylaşımların UNC yolu: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure Sayfa blobları  | <li>Paylaşımların UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Dosyaları       |<li>Paylaşımların UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware verilerini kopyalamak için Azure blok bloblarını kullanın.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>NFS paylaşımından şirket içi vCenter kümenize bir veri deposu olarak bağlama ve verileri kopyalama

Data Box NFS paylaşımının, verileri NFS veri deposuna kopyalamak için şirket içi vCenter kümenize veya VMware ESXi konağa bir veri deposu olarak bağlanması gerekir:

1. Şirket içi vCenter sunucunuzda oturum açın.

2. **Veri merkezi**' ne sağ tıklayın, **depolama**' yı seçin, **Yeni veri deposu**' nu seçin ve ardından **İleri**

   ![Yeni veri deposu Ekle](media/databox-migration-add-datastore.png)

3. Veri deposu ekleme Sihirbazı 'nın adım 1 ' de, **tür**altında **NFS** ' yi seçin.

   ![Yeni veri deposu Ekle-tür](media/databox-migration-add-datastore-type.png)

4. Sihirbazın Adım 2 ' de NFS sürümü olarak **NFS 3** ' ü seçin ve ardından **İleri**' yi seçin.

   ![Yeni veri deposu ekleme-NFS sürümü](media/databox-migration-add-datastore-nfs-version.png)

5. Sihirbazın Adım 3 ' te, veri deposu, yol ve sunucu için adı belirtin. Sunucu için Data Box IP adresini kullanabilirsiniz. Klasör yolu `/<StorageAccountName_BlockBlob>/<ContainerName>/` biçiminde olacaktır.

   ![Yeni veri deposu ekleme-NFS yapılandırması](media/databox-migration-add-datastore-nfs-configuration.png)

6. Sihirbazın Adım 4 ' te, veri deposunun bağlanmasını istediğiniz ESXi konaklarınızı seçin ve ardından **İleri**' yi seçin.  Bir kümede, sanal makinelerin geçişini sağlamak için tüm Konaklar ' ı seçin.

   ![Yeni veri deposu Ekle-Konakları seçin](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Sihirbazın 5. adımında, Özeti gözden geçirin ve **son**' u seçin.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Data Box NFS veri deposuna veri kopyalama

Sanal makineler, yeni veri deposuna geçirilebilir veya kopyalanabilir.  Geçirmek istediğiniz kullanılmayan sanal makineler, **Depolama vMotion** seçeneği KULLANıLARAK Data Box NFS veri deposuna geçirilebilirler. Etkin sanal makineler Data Box NFS veri deposuna kopyalanabilir.

* **Taşınabilecek**sanal makineleri belirleyip listeleyin.
* **Kopyalanabilmesi**gereken sanal makineleri belirleyip listeleyin.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Bir sanal makineyi Data Box veri deposuna taşıma

1. Data Box veri deposuna taşımak istediğiniz sanal makineye sağ tıklayın ve ardından **geçir**' i seçin.

    ![Sanal makineyi geçirme](media/databox-migration-vm-migrate.png)

2. Yalnızca geçiş türü için **depolamayı Değiştir** ' i seçin ve ardından **İleri**' yi seçin.

    ![Sanal makineyi geçirme-yalnızca depolama](media/databox-migration-vm-migrate-change-storage.png)

3. Hedef olarak veri **kutusu-veri deposu** ' nu seçin ve ardından **İleri**' yi seçin.

    ![Sanal makineyi geçirme-veri deposu seçme](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Bilgileri gözden geçirin ve **son**' u seçin.

5. Ek sanal makineler için 1 ile 4 arasındaki adımları tekrarlayın.

> [!TIP]
> Aynı güç durumunda (açık veya kapalı) birden çok sanal makine seçebilir ve bunları toplu olarak geçirebilirsiniz.

Sanal makine, Data Box NFS veri deposuna geçirilir. Tüm sanal makineler geçirildikten sonra, Azure VMware çözümüne veri geçişi hazırlığı için etkin sanal makineleri devre dışı bırakabilirsiniz (kapatabilirsiniz).

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Bir sanal makineyi veya sanal makine şablonunu Data Box veri deposuna kopyalama

1. Kopyalamak istediğiniz bir sanal makineye veya sanal makine şablonuna sağ tıklayın. **Sanal makineye** **kopya  >  Kopyala** ' yı seçin.

    ![Sanal makine kopyası](media/databox-migration-vm-clone.png)

2. Kopyalanmış sanal makine veya sanal makine şablonu için bir ad seçin.

3. Klonlanan nesneyi yerleştirmek istediğiniz klasörü seçin ve ardından **İleri**' yi seçin.

4. Klonlanan nesneyi yerleştirmek istediğiniz kümeyi veya kaynak havuzunu seçin ve ardından **İleri**' yi seçin.

5. Depolama konumu olarak **veri kutusu-veri deposu** ' nu seçin ve ardından **İleri**' yi seçin.

    ![Sanal makine kopyalama-veri deposu seçme](media/databox-migration-vm-clone-select-datastore.png)

6. Klonlanan nesne için herhangi bir seçeneği özelleştirmek istiyorsanız, özelleştirme seçeneklerini belirleyin ve ardından **İleri**' yi seçin.

7. Konfigürasyonları gözden geçirin ve **son**' u seçin.

8. Ek sanal makineler veya sanal makine şablonları için 1 ile 7 arasındaki adımları yineleyin.

Sanal makineler Data Box ' den NFS veri deposuna kopyalanır ve depolanır. Sanal makineler klonlandığında, verilerin Azure VMware çözümüne geçirilmesine yönelik olarak Kapandıklarından emin olun.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO dosyalarını Data Box veri deposuna kopyalama

1. Şirket içi vCenter Web Kullanıcı arabiriminizden **depolama**sayfasına gidin.  **Veri kutusu-veri deposu** ' nu ve ardından **dosyalar**' ı seçin. ISO dosyalarını depolamak için yeni bir klasör oluşturun.

    ![ISO Kopyala-yeni klasör oluştur](media/databox-migration-create-folder.png)

2. ISO dosyalarının depolanacağı klasör için bir ad girin.

3. Yeni oluşturulan klasöre çift tıklayarak açın.

4. **Dosyaları karşıya yükle** ' yi seçin ve ardından karşıya yüklemek istediğiniz ISO dosyalarını seçin.
    
    ![ISO kopyalama-dosyaları karşıya yükleme](media/databox-migration-upload-iso.png)

> [!TIP]
> Şirket içi veri deposunda ISO dosyalarınız zaten varsa, dosyaları Data Box NFS veri deposuna kopyalamak için dosya ve **kopyalama** ' yı seçebilirsiniz.


## <a name="prepare-data-box-for-return"></a>Data Box Return için hazırla

Tüm sanal makine verileri, sanal makine şablonu verileri ve herhangi bir ISO dosyası Data Box NFS veri deposuna kopyalandıktan sonra, veri deposunun vCenter 'ınızdan bağlantısını kesebilirsiniz. Veri deposunun bağlantısını kesmeden önce tüm sanal makinelerin ve sanal makine şablonlarının envanterden kaldırılması gerekir.

### <a name="remove-objects-from-inventory"></a>Nesneleri envanterden kaldır

1. Şirket içi vCenter Web Kullanıcı arabiriminizden **depolama**sayfasına gidin. **Veri kutusu-veri deposu** ' nu seçip **VM 'ler**' i seçin.

    ![Sanal makineleri envanterden kaldır-kapalı](media/databox-migration-select-databox-vm.png)

2. Tüm sanal makinelerin kapatıldığından emin olun.

3. Tüm sanal makineler ' i seçin, sağ tıklayın ve ardından **envanterden kaldır**' ı seçin.

    ![Sanal makineleri envanterden kaldır](media/databox-migration-remove-vm-from-inventory.png)

4. **Klasörlerdeki VM şablonları** ' nı seçin ve 3. adımı yineleyin.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Data Box NFS veri deposunu vCenter 'dan kaldırma

Data Box NFS veri deposu, return için hazırlanmadan önce VMware ESXi konaklarından bağlantısı kesilmelidir.

1. Şirket içi vCenter Web Kullanıcı arabiriminizden **depolama**sayfasına gidin.

2. Veri **kutusu-veri deposu** öğesine sağ tıklayın ve **veri deposunu**çıkar ' ı seçin.

    ![Data Box veri deposu çıkarın](media/databox-migration-unmount-datastore.png)

3. Veri deposunun bağlı olduğu tüm ESXi Konakları ' nı seçin ve **Tamam ' ı**seçin.

    ![Data Box veri deposundan çıkarın-Konakları seçin](media/databox-migration-unmount-datastore-select-hosts.png)

4. Uyarıları gözden geçirin ve kabul edin ve **Tamam**' ı seçin.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Return için Data Box hazırlayın ve sonra döndürün

Data Box döndürmek için Azure Data Box geri dönüş makalesinde anlatılan adımları izleyin [ve verileri Azure 'a yükleyin](../databox/data-box-deploy-picked-up.md) . Azure depolama hesabınıza veri kopyalama durumunu kontrol edin. Durum tamamlandı olarak görüntülendikten sonra, Azure Depolama hesabınızdaki verileri doğrulayabilirsiniz.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Azure depolama 'dan Azure VMware çözümüne veri kopyalama

Data Box cihazınıza kopyalanmış veriler, Data Box sipariş durumu tamamlandı olarak gösterildikten sonra Azure depolama hesabınızda kullanılabilir olacaktır. Veriler artık Azure VMware çözümünüze kopyalanabilir. Depolama hesabındaki verilerin, NFS protokolü kullanılarak özel bulutunuzun vSAN veri deposuna kopyalanması gerekir. 

İlk olarak, **AzCopy**kullanarak Azure 'Daki bir Linux sanal makinesinde bulunan bir Linux sanal makinesindeki bir yönetilen diske BLOB depolama verilerini kopyalayın. Yönetilen diski NFS aracılığıyla kullanılabilir hale getirin, NFS paylaşımından özel bulutunuzda bir veri deposu olarak bağlayın ve verileri kopyalayın. Bu yöntem, özel bulutunuzda verilerin daha hızlı bir şekilde kopyalanmasını sağlar.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Bir Linux sanal makinesi ve yönetilen diskler kullanarak özel bulutunuzda verileri kopyalayın ve ardından NFS paylaşma olarak dışarı aktarın

1. Azure 'da depolama hesabınızın oluşturulduğu bölgedeki bir [Linux sanal makinesi](../virtual-machines/linux/quick-create-portal.md) oluşturun ve özel bulutunuz Için bir Azure sanal ağ bağlantısı vardır.

2. Depolama kapasitesi blob verilerinden daha büyük olan yönetilen bir disk oluşturun ve [Linux sanal makinenize ekleyin](../virtual-machines/linux/attach-disk-portal.md).  Blob verileri miktarı kullanılabilir en büyük yönetilen diskin kapasitesinden büyükse, verilerin birden çok adımda kopyalanması veya birden çok yönetilen disk kullanılması gerekir.

3. Linux sanal makinesine bağlanın ve yönetilen diski bağlayın.

4. [Linux sanal makinenize AzCopy 'i](../storage/common/storage-use-azcopy-v10.md)yükler.

5. AzCopy kullanarak Azure Blob depolamaınızdan verileri yönetilen diske indirin.  Komut sözdizimi: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  @No__t_0, Azure depolama hesabınızın adı ile `<container-name>` Data Box üzerinden kopyalanmış verileri tutan kapsayıcınle değiştirin.

6. Linux sanal makinenize NFS sunucusunu yükler:

    - Ubuntu/debir dağıtım üzerinde: `sudo apt install nfs-kernel-server`.
    - Enterprise Linux dağıtımında: `sudo yum install nfs-utils`.

7. Yönetilen diskinizde, Azure Blob depolama alanındaki verilerin kopyalandığı klasörün iznini değiştirin.  NFS paylaşma olarak dışarı aktarmak istediğiniz tüm klasörler için izinleri değiştirin.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. @No__t_0 dosyasını düzenleyerek, istemci IP adreslerinin NFS paylaşımıyla erişmesini sağlamak için izinler atayın.

    ```bash
    sudo vi /etc/exports
    ```
    
    Özel bulutunuzun her ESXi ana bilgisayar IP 'si için dosyada aşağıdaki satırları girin.  Birden çok klasör için paylaşımlar oluşturuyorsanız, tüm klasörleri ekleyin.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. @No__t_0 komutunu kullanarak NFS paylaşımlarını dışarı aktarın.

10. @No__t_0 komutunu kullanarak NFS çekirdek sunucusunu yeniden başlatın.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Linux sanal makinesi NFS paylaşımından bir özel bulut vCenter kümesine veri deposu olarak bağlama ve sonra verileri kopyalama

Linux sanal makinenizden NFS paylaşımının, özel bulut vCenter kümenize bir veri deposu olarak bağlanması gerekir. Bağlandıktan sonra, veriler NFS veri deposundan özel bulut vSAN veri deposuna kopyalanabilir.

1. Özel bulut vCenter sunucunuzda oturum açın.

2. **Veri merkezi**' ne sağ tıklayın, **depolama**' yı seçin, **Yeni veri deposu**' nu seçin ve ardından **İleri**

   ![Yeni veri deposu Ekle](media/databox-migration-add-datastore.png)

3. Veri deposu ekleme sihirbazının 1. adımında **NFS** türünü seçin.

   ![Yeni veri deposu Ekle-tür](media/databox-migration-add-datastore-type.png)

4. Sihirbazın Adım 2 ' de NFS sürümü olarak **NFS 3** ' ü seçin ve ardından **İleri**' yi seçin.

   ![Yeni veri deposu ekleme-NFS sürümü](media/databox-migration-add-datastore-nfs-version.png)

5. Sihirbazın Adım 3 ' te, veri deposu, yol ve sunucu için adı belirtin.  Sunucu için Linux sanal makinenizin IP adresini kullanabilirsiniz.  Klasör yolu `/<folder>/<subfolder>/` biçiminde olacaktır.

   ![Yeni veri deposu ekleme-NFS yapılandırması](media/databox-migration-add-datastore-nfs-configuration.png)

6. Sihirbazın Adım 4 ' te, veri deposunun bağlanmasını istediğiniz ESXi konaklarınızı seçin ve ardından **İleri**' yi seçin.  Bir kümede, sanal makinelerin geçişini sağlamak için tüm Konaklar ' ı seçin.

   ![Yeni veri deposu Ekle-Konakları seçin](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Sihirbazın 5. adımında, Özeti gözden geçirin ve ardından **son**' u seçin.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Bir NFS veri deposundan stoğa sanal makineler ve sanal makine şablonları ekleyin

1. Özel bulut vCenter Web Kullanıcı arabiriminizden **depolama**sayfasına gidin.  Bir Linux sanal makinesi NFS veri deposu seçin ve ardından **dosyalar**' ı seçin.

    ![NFS veri deposundaki dosyaları seçme](media/databox-migration-datastore-select-files.png)

2. Bir sanal makine veya sanal makine şablonu içeren bir klasör seçin.  Ayrıntılar bölmesinde, bir sanal makine için bir. vmx dosyası veya bir sanal makine şablonu için. vmtx dosyası seçin.

3. Sanal makineyi özel bulut vCenter 'unuzda kaydettirmek için **VM 'Yi kaydet** ' i seçin.

    ![Sanal makineyi Kaydet](media/databox-migration-datastore-register-vm.png)

4. Sanal makinenin kaydedilmesini istediğiniz veri merkezini, klasörü ve küme/kaynak havuzunu seçin.

4. Tüm sanal makineler ve sanal makine şablonları için 3 ve 4 numaralı adımları tekrarlayın.

5. ISO dosyalarını içeren klasöre gidin.  Dosyaları vSAN veri deposundaki bir klasöre kopyalamak için ISO dosyalarını seçin ve ardından **Kopyala** ' yı seçin.

Sanal makineler ve sanal makine şablonları artık özel bulut vCenter 'unuzda kullanılabilir. Bu sanal makinelerin, oturum açmadan önce NFS veri deposundan vSAN veri deposuna taşınması gerekir. **Depolama vMotion** seçeneğini kullanabilir ve sanal makinelerin hedefi olarak vSAN veri deposunu seçebilirsiniz.

Sanal makine şablonları, Linux sanal makine NFS veri deposundan vSAN veri deposuna kopyalanmalıdır.

### <a name="clean-up-your-linux-virtual-machine"></a>Linux sanal makinenizi Temizleme

Tüm veriler özel buluta kopyalandıktan sonra, özel bulutunuzda NFS veri deposunu kaldırabilirsiniz:

1. Tüm sanal makinelerin ve şablonların vSAN veri deposuna taşındığından ve kopyalandığından emin olun.

2. NFS veri deposundaki tüm sanal makine şablonlarından stoktan kaldır.

3. Linux sanal makine veri deposunu özel bulut vCenter 'ınızdan çıkarın.

4. Sanal makineyi ve yönetilen diski Azure 'dan silin.

5. Depolama hesabınızda Data Box tarafından aktarılan verileri tutmak istemiyorsanız, Azure Depolama hesabını silin.  
    


## <a name="next-steps"></a>Sonraki adımlar

* [Data Box](../databox/data-box-overview.md)hakkında daha fazla bilgi edinin.
* [Özel bulutunuzda iş yüklerini geçirmeye](migrate-workloads.md)yönelik farklı seçenekler hakkında daha fazla bilgi edinin.
