---
title: Azure Backup 'ı depolama anlık görüntülerine göre SAP HANA | Microsoft Docs
description: Azure sanal makinelerinde SAP HANA için iki önemli yedekleme olasılıkları vardır. Bu makalede, depolama anlık görüntülerine göre SAP HANA yedekleme ele alınmaktadır
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8bcfdefa2ea9de12ca6029839a41c91111a5c61c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078593"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Depolama anlık görüntülerine dayalı SAP HANA yedeklemesi

## <a name="introduction"></a>Giriş

Bu, SAP HANA yedeğine ilişkin üç bölümlü ilgili makalelerin bir parçasıdır. [Azure sanal makineler 'de SAP HANA Için Yedekleme Kılavuzu](sap-hana-backup-guide.md) , kullanmaya başlama hakkında bir genel bakış ve bilgi sağlar ve [dosya düzeyinde SAP HANA Azure Backup](sap-hana-backup-file-level.md) dosya tabanlı yedekleme seçeneğini içerir.

Tek örnekli hepsi bir adet demo sistem için VM yedekleme özelliği kullanılırken, bir VM 'nin işletim sistemi düzeyinde HANA yedeklemelerini yönetmek yerine bir VM yedeklemesi gerçekleştirmeyi düşünmelidir. Diğer bir deyişle, bir sanal makineye bağlı tek tek sanal disklerin kopyalarını oluşturmak ve HANA veri dosyalarını tutmak için Azure Blob anlık görüntülerini alma işlemi bir alternatiftir. Ancak kritik bir nokta, sistem çalışır durumdayken bir VM yedeklemesi veya disk anlık görüntüsü oluşturulurken uygulama tutarlılığı olur. [Azure sanal makinelerinde SAP HANA ilgili makale yedekleme kılavuzunda](sap-hana-backup-guide.md) _depolama anlık görüntüleri alırken SAP HANA veri tutarlılığını_ görün. SAP HANA, bu tür depolama anlık görüntülerini destekleyen bir özelliğe sahiptir.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Anlık görüntüleri uygulamayla tutarlı yedeklemelerin merkezi bir parçası olarak SAP HANA

SAP HANA bir depolama anlık görüntüsü almayı destekleyen bir işlevsellik vardır. Tek Kapsayıcılı sistemlere yönelik bir kısıtlama vardır. Birden fazla kiracıya sahip SAP HANA MCS ile ilgili senaryolar, bu tür SAP HANA veritabanı anlık görüntülerini desteklemez (bkz. [depolama anlık görüntüsü oluşturma (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Şu şekilde çalışır:

- SAP HANA anlık görüntüsünü başlatarak bir depolama anlık görüntüsüne hazırlanma
- Depolama anlık görüntüsünü çalıştırın (örneğin, Azure Blob anlık görüntüsü)
- SAP HANA anlık görüntüsünü onaylayın

![Bu ekran görüntüsünde, bir SQL ifadesiyle SAP HANA veri anlık görüntüsünün oluşturulabileceği gösterilmektedir](media/sap-hana-backup-storage-snapshots/image011.png)

Bu ekran görüntüsünde, bir SQL ifadesiyle SAP HANA veri anlık görüntüsünün oluşturulabileceği gösterilmektedir.

![Ardından anlık görüntü SAP HANA Studio 'daki yedekleme kataloğunda de görüntülenir](media/sap-hana-backup-storage-snapshots/image012.png)

Ardından anlık görüntü SAP HANA Studio 'daki yedekleme kataloğunda de görüntülenir.

![Diskte, anlık görüntü SAP HANA veri dizininde görüntülenir](media/sap-hana-backup-storage-snapshots/image013.png)

Diskte, anlık görüntü SAP HANA veri dizininde görüntülenir.

Bunlardan biri, SAP HANA anlık görüntü hazırlama modundayken depolama anlık görüntüsünü çalıştırmadan önce dosya sistemi tutarlılığının de garanti edilmesini sağlamaktır. [Azure sanal makinelerinde SAP HANA ilgili makale yedekleme kılavuzunda](sap-hana-backup-guide.md) _depolama anlık görüntüleri alırken SAP HANA veri tutarlılığını_ görün.

Depolama anlık görüntüsü tamamlandığında SAP HANA anlık görüntüsünü doğrulamak önemlidir. Çalıştırmak için karşılık gelen bir SQL deyimidir: Yedekleme VERILERI anlık GÖRÜNTÜSÜNÜ kapat (bkz. [yedekleme VERILERI kapalı anlık görüntü ekstresi (yedekleme ve kurtarma)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> HANA anlık görüntüsünü onaylayın. Kopyalama-yazma nedeniyle,&quot; SAP HANA anlık görüntü hazırlama modunda ek disk alanı gerektirebilir ve SAP HANA anlık görüntü onaylanana kadar yeni yedeklemeler başlatmak mümkün değildir. &quot;

## <a name="hana-vm-backup-via-azure-backup-service"></a>Azure Backup hizmeti aracılığıyla HANA VM yedeklemesi

Azure Backup hizmetinin yedekleme Aracısı Linux sanal makineleri için kullanılamaz. Ayrıca, Windows 'un VSS ile aynı işlevselliğe sahip olduğu da Linux 'ta benzer işlevlere sahip değildir.  Dosya/dizin düzeyinde Azure Backup 'ı kullanmak için, biri SAP HANA yedekleme dosyalarını bir Windows sanal makinesine kopyalayabilir ve ardından yedekleme aracısını kullanır. 

Aksi halde, Azure Backup hizmeti aracılığıyla yalnızca tam bir Linux VM yedeklemesi mümkündür. Daha fazla bilgi edinmek için [Azure Backup özelliklere genel bakış](../../../backup/backup-introduction-to-azure-backup.md) bölümüne bakın.

Azure Backup hizmeti bir VM 'yi yedekleme ve geri yükleme seçeneği sunar. Bu hizmet ve nasıl çalıştığı hakkında daha fazla bilgi, [Azure 'DA VM yedekleme altyapınızı planlayın](../../../backup/backup-azure-vms-introduction.md)makalesinde bulunabilir.

Bu makaleye göre iki önemli nokta vardır:

_&quot;Linux sanal makineleri için, Linux 'un VSS 'ye denk bir platformu olmadığından, yalnızca dosya ile tutarlı yedeklemeler mümkündür.&quot;_

_&quot;Uygulamaların geri yüklenen veriler üzerinde kendi &quot;çözüm&quot; mekanizmasını uygulaması gerekir.&quot;_

Bu nedenle, yedekleme başladığında SAP HANA diskte tutarlı bir durumda olduğundan emin olmak gerekir. Daha önce belgede açıklanan _SAP HANA anlık görüntüleri_ görüntüleyin. Ancak SAP HANA bu anlık görüntü hazırlama modunda kaldığında olası bir sorun vardır. Daha fazla bilgi için bkz. [depolama anlık görüntüsü oluşturma (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) .

Bu makalede şu durumlar yer alan:

_&quot;Oluşturulduktan sonra, depolama anlık görüntüsünü onaylamanız veya iptal etmek önemle önerilir. Depolama anlık görüntüsü hazırlanmakta veya oluşturulurken, anlık görüntüyle ilgili veriler dondurulur. Anlık görüntü ile ilgili veriler dondurulmuş olmaya devam ederken, değişiklikler veritabanında yine de yapılabilir. Bu değişiklikler, dondurulmuş anlık görüntüyle ilgili verilerin değiştirilmesine neden olmaz. Bunun yerine, değişiklikler, depolama anlık görüntüsünden ayrı olan veri alanındaki konumlara yazılır. Ayrıca değişiklikler günlüğe yazılır. Bununla birlikte, anlık görüntüyle ilgili verilerin ne kadar uzun tutulması, veri hacmi büyümenin daha fazla genişleyebilmesi.&quot;_

Azure Backup, Azure VM uzantıları aracılığıyla dosya sistemi tutarlılığını üstlenir. Bu uzantılar tek başına kullanılamaz ve yalnızca Azure Backup hizmetiyle birlikte çalışır. Bununla birlikte, uygulama tutarlılığını güvence altına almak için bir SAP HANA anlık görüntüsü oluşturmak ve silmek üzere komut dosyaları sağlamaya yönelik bir gereksinim de vardır.

Azure Backup dört ana aşamaya sahiptir:

- Betik oluşturma-betik ihtiyaçlarını yürütme, SAP HANA bir anlık görüntü oluşturmak için
- Anlık görüntü al
- Anlık görüntü sonrası betiği yürütme-komut dosyası ihtiyaçları hazırlama betiği tarafından oluşturulan SAP HANA silmek için
- Verileri kasaya aktar

Azure Backup tam olarak nasıl çalıştığına ilişkin bu komut dosyalarını ve ayrıntıları nereye kopyalayabileceğiniz hakkında ayrıntılar için aşağıdaki makalelere bakın:

- [Azure’da VM yedekleme altyapınızı planlama](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM 'lerinin uygulamayla tutarlı yedeklemesi](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Bu noktada, Microsoft, SAP HANA için hazırlama betikleri ve anlık görüntü sonrası betikleri yayımmadı. Müşteri veya Sistem Tümleştirici, bu betikleri oluşturmanız ve yukarıda başvurulan belgelere göre yordamı yapılandırmanız gerekecektir.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Uygulama ile tutarlı yedekten bir VM 'ye karşı geri yükleme
Azure Backup tarafından alınan uygulamayla tutarlı yedeklemenin geri yükleme işlemi, [Azure sanal makine yedeğinden dosya kurtarma](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)makalesindeki makalede belgelenmiştir. 

> [!IMPORTANT]
> [Azure sanal makine yedeğinden dosyaları kurtar](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) bölümünde, disk şeritli kümeler kullanılırken listelenen özel durumların ve adımların bir listesi bulunur. Dizili diskler büyük olasılıkla SAP HANA için normal VM yapılandırmadır. Bu nedenle, makaleyi okumak ve bu gibi durumlarda geri yükleme işlemini makalede listelendiği gibi test etmek önemlidir. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Azure Backup hizmeti aracılığıyla HANA lisans anahtarı ve VM geri yükleme

Azure Backup hizmeti, geri yükleme sırasında yeni bir VM oluşturmak için tasarlanmıştır. Şimdi mevcut bir Azure VM 'nin yerinde &quot;&quot; geri yüklemesi yapmak için bir plan yoktur.

![Bu şekilde Azure portal Azure hizmetinin geri yükleme seçeneği gösterilmektedir](media/sap-hana-backup-storage-snapshots/image019.png)

Bu şekilde Azure portal Azure hizmetinin geri yükleme seçeneği gösterilmektedir. Bunlardan biri, geri yükleme veya diskleri geri yükleme sırasında VM oluşturma arasında seçim yapabilir. Diskleri geri yükledikten sonra, bunun üzerine yeni bir VM oluşturmak yine de gereklidir. Azure 'da her yeni VM oluşturulduğunda benzersiz VM KIMLIĞI değişiklikleri (bkz. [Azure VM BENZERSIZ kimliğini erişme ve kullanma](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Bu şekilde, Azure Backup hizmeti aracılığıyla geri yüklemeden önce ve sonra Azure VM benzersiz KIMLIĞI gösterilmektedir](media/sap-hana-backup-storage-snapshots/image020.png)

Bu şekilde, Azure Backup hizmeti aracılığıyla geri yüklemeden önce ve sonra Azure VM benzersiz KIMLIĞI gösterilmektedir. SAP lisanslama için kullanılan SAP donanım anahtarı, bu benzersiz VM KIMLIĞINI kullanıyor. Sonuç olarak, VM geri yüklendikten sonra yeni bir SAP lisansının yüklenmesi gerekir.

Bu yedekleme kılavuzunun oluşturulması sırasında önizleme modunda yeni bir Azure Backup özelliği sunulmuştur. VM yedeklemesi için alınmış VM anlık görüntüsüne bağlı olarak dosya düzeyinde geri yüklemeye izin verir. Bu, yeni bir sanal makine dağıtma gereksinimini ortadan kaldırır ve bu nedenle benzersiz VM KIMLIĞI aynı kalır ve yeni bir SAP HANA lisans anahtarı gerekli değildir. Bu özellik hakkında daha fazla belge, tam olarak sınandıktan sonra sağlanacaktır.

Azure Backup, Azure sanal disklerinin yanı sıra dosya ve dizinlerin VM 'nin içinden yedeklenmesini de sağlar. Azure Backup büyük bir avantajı, tüm yedeklemelerin yönetimisinin yönetimi ve müşteriyi bunu yapmak zorunda kalmadan kaydetmektir. Geri yükleme gerekli hale gelirse, Azure Backup kullanılacak doğru yedeği seçer.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>El ile disk anlık görüntüsü aracılığıyla VM yedeklemesi SAP HANA

Azure Backup hizmetini kullanmak yerine, tek bir yedekleme çözümünü Azure VHD 'lerinin blob anlık görüntülerini el ile PowerShell aracılığıyla oluşturarak yapılandırabilirsiniz. Adımların açıklaması için bkz. [PowerShell ile blob anlık görüntülerini kullanma](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) .

Daha fazla esneklik sağlar ancak bu belgede daha önce açıklanan sorunları çözmez:

- SAP HANA, SAP HANA anlık görüntü oluşturarak hala tutarlı bir durumda olduğundan emin olmanız gerekir
- VM 'nin bir kira olduğunu belirten bir hata nedeniyle serbest bırakılsa bile, işletim sistemi diskinin üzerine yazılamaz. Bu, yalnızca VM silindikten sonra çalışarak, yeni bir benzersiz VM KIMLIĞINE ve yeni bir SAP lisansı yüklemeye gerek oluşmasına neden olur.

![Yalnızca bir Azure VM 'nin veri disklerini geri yüklemek mümkündür](media/sap-hana-backup-storage-snapshots/image021.png)

Yalnızca bir Azure VM 'nin veri disklerini geri yüklemek, yeni bir benzersiz VM KIMLIĞI alma sorunu ve bu nedenle SAP lisansını geçersiz hale getirmek mümkündür:

- Test için, bir sanal makineye iki Azure veri diski iliştirilmiş ve yazılım RAID 'in üzerinde tanımlanması 
- SAP HANA SAP HANA anlık görüntü özelliği tarafından tutarlı bir durumda olduğunu doğrulamıştır
- Dosya sistemi donması ( [Azure sanal makinelerinde SAP HANA için ilgili makale yedekleme kılavuzunda](sap-hana-backup-guide.md) _depolama anlık görüntüleri alırken SAP HANA veri tutarlılığını_ görün)
- Blob anlık görüntüleri her iki veri disketinden alındı
- Dosya sistemi çöz
- SAP HANA anlık görüntü onayı
- Veri disklerini geri yüklemek için sanal makine kapatıldı ve her iki disk de ayrıldı
- Diskler ayrıldıktan sonra eski blob anlık görüntüleri ile üzerine yazılır
- Ardından, geri yüklenen sanal diskler VM 'ye yeniden eklendi
- VM 'yi başlattıktan sonra, yazılım RAID üzerindeki her şey sorunsuz çalıştık ve BLOB anlık görüntü zamanına geri ayarlanmış
- HANA, HANA anlık görüntüsüne geri ayarlandı

Blob anlık görüntülerinden önce SAP HANA kapatmak mümkün olduysa, yordam daha az karmaşık hale gelir. Bu durumda, bir, HANA anlık görüntüsünü atlayabilir ve sistemde başka hiçbir şey yoksa, dosya sistemi donmasını de atlar. Her şey çevrimiçi olduğunda anlık görüntü yapmanız gerektiğinde resme eklenen karmaşıklık resmi gelir. [Azure sanal makinelerinde SAP HANA ilgili makale yedekleme kılavuzunda](sap-hana-backup-guide.md) _depolama anlık görüntüleri alırken SAP HANA veri tutarlılığını_ görün.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure sanal makinelerinde SAP HANA Için Yedekleme Kılavuzu](sap-hana-backup-guide.md) , Başlarken hakkında genel bakış ve bilgiler sağlar.
* [Dosya düzeyine dayalı SAP HANA yedekleme](sap-hana-backup-file-level.md) dosya tabanlı yedekleme seçeneğini içerir.
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
