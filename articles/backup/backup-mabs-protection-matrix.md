---
title: MABS (Azure Backup Sunucusu) v3 UR1 koruma matrisi
description: Bu makalede, Azure Backup Sunucusu koruduğu tüm iş yükleri, veri türleri ve yüklemelerin listelendiği bir destek matrisi sunulmaktadır.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: cfdd227135a2124e22a604bad4bd41594a38fb37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561279"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Sunucusu) v3 UR1 koruma matrisi

Bu makalede, Azure Backup Sunucusu ile koruyabileceğiniz çeşitli sunucular ve iş yükleri listelenmektedir. Aşağıdaki matris Azure Backup Sunucusu ile nelerin korunabilecek olduğunu listeler.

MABS v3 UR1 için aşağıdaki matrisi kullanın:

* İş yükleri: teknolojinin iş yükü türü.

* Sürüm – iş yükleri için desteklenen MABS sürümü.

* MABS yüklemesi – MABS 'yi yüklemek istediğiniz bilgisayar/konum.

* Koruma ve kurtarma – desteklenen depolama kapsayıcısı veya desteklenen dağıtım gibi iş yükleriyle ilgili ayrıntılı bilgileri listeleyin.

>[!NOTE]
>32 bitlik koruma aracısına yönelik destek MABS v3 UR1 ile kullanım dışıdır. Bkz. [32-bit koruma aracısının kullanımdan kaldırılması](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Koruma destek matrisi

Aşağıdaki bölümlerde, MABS için koruma destek matrisi ayrıntıları verilmiştir:

* [Uygulamalar yedeklemesi](#applications-backup)
* [VM yedeklemesi](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Uygulamalar yedeklemesi

| **İş yükü**               | **Sürüm**                                                  | **Azure Backup Sunucusu yükleme**                       | **Desteklenen Azure Backup Sunucusu** | **Koruma ve kurtarma**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| İstemci bilgisayarlar (64-bit) | Windows 10                                                  | Fiziksel sunucu  <br><br>    Hyper-V sanal makine   <br><br>   VMware sanal makinesi | V3 UR1                            | Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler   <br><br>   Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.  <br><br>    Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir. |
| Sunucular (64-bit)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)  <br><br>    Fiziksel sunucu  <br><br>    Hyper-V sanal makine <br><br>     VMware sanal makinesi  <br><br>    Azure Stack | V3 UR1                            | Birim, paylaşma, klasör, dosya <br><br>    Yinelenenleri kaldırılmış birimler (yalnızca NTFS) <br><br>Windows Server 2019 ' de çalışan MABS v3 ile WS 2016 NTFS yinelenenleri kaldırılmış bir birimi korurken, kurtarma işlemleri etkilenebilir. Daha sonraki MABS sürümlerinin parçası olacak şekilde, yinelenenleri kaldırılmış bir şekilde kurtarma işlemi gerçekleştirmek için bir düzeltmemiz var. MABS v3 UR1 üzerinde bu düzeltmeyle karşılaşırsanız, MABS desteğiyle iletişim kurun.<br><br> Windows Server 2016 üzerinde MABS v3 ile WS 2019 NTFS yinelenenleri kaldırılmış bir birimi korurken, yedeklemeler ve geri yüklemeler yinelenenleri kaldırma işlemi geri alınamaz. Bu, yedeklemelerin MABS sunucusunda özgün NTFS yinelenenleri kaldırılmış birimden daha fazla alan tükettiği anlamına gelir.   <br><br>   Sistem durumu ve tam (iş yükü Azure sanal makinesi olarak çalışırken desteklenmez) |
| Sunucular (64-bit)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ( [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)yüklemeniz gerekir) | Fiziksel sunucu  <br><br>    Hyper-V sanal makine  <br><br>      VMware sanal makinesi  <br><br>   Azure Stack | V3 UR1                            | Birim, paylaşma, klasör, dosya, sistem durumu/tam kurtarma        |
| SQL Server                | SQL Server 2019, 2017, 2016 ve [desteklenen SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 ve desteklenen [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) 'ler | Fiziksel sunucu  <br><br>     Hyper-V sanal makine   <br><br>     VMware sanal makinesi  <br><br>   Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)  <br><br>     Azure Stack | V3 UR1                            | Tüm dağıtım senaryoları: veritabanı       <br><br>  MABS v3 UR1, ReFS birimleri üzerinden SQL veritabanlarının yedeklenmesini destekler     <br><br>     MABS, Windows Server 2012 Scale-Out dosya sunucularında (SOFS) barındırılan SQL Server veritabanlarını desteklemez. <br><br>   MABS, yük devretme kümesindeki rol adının SQL üzerindeki adlandırılmış AG 'den farklı olduğu SQL Server dağıtılmış kullanılabilirlik grubunu (DAG) veya kullanılabilirlik grubunu (AG) koruyamaz.       |
| Exchange                   | Exchange 2019, 2016                                         | Fiziksel sunucu   <br><br>   Hyper-V sanal makine  <br><br>      VMware sanal makinesi  <br><br>   Azure Stack  <br><br>    Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken) | V3 UR1                            | Koruma (tüm dağıtım senaryolarında): tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki veritabanı  <br><br>    Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları    <br><br>  MABS v3 UR1 ile ReFS üzerinden Exchange yedeklemesi desteklenir |
| SharePoint                 | SharePoint 2019, 2016 en son SPs                       | Fiziksel sunucu  <br><br>    Hyper-V sanal makine <br><br>    VMware sanal makinesi  <br><br>   Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)   <br><br>   Azure Stack | V3 UR1                            | Koruma (tüm dağıtım senaryolarında): Grup, ön uç Web sunucusu içeriği  <br><br>    Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, Web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç Web sunucusu  <br><br>    İçerik veritabanları için SQL Server 2012 AlwaysOn özelliğini kullanan bir SharePoint grubunu koruma desteklenmez. |

## <a name="vm-backup"></a>VM yedeklemesi

| **İş yükü**                                                 | **Sürüm**                                             | **Azure Backup Sunucusu yükleme**                      | **Desteklenen Azure Backup Sunucusu** | **Koruma ve kurtarma**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde | Windows Server 2019, 2016, 2012 R2, 2012               | Fiziksel sunucu  <br><br>    Hyper-V sanal makine <br><br>    VMware sanal makinesi | V3 UR1                             | Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)  <br><br>    Kurtarma: sanal makine, dosya ve klasörlerin öğe düzeyinde kurtarılması yalnızca Windows, birimler, sanal sabit sürücüler için kullanılabilir |
| VMware VM 'Leri                                                  | VMware Server 5,5, 6,0 veya 6,5, 6,7 (lisanslı sürüm) | Hyper-V sanal makinesi  <br><br>   VMware sanal makinesi         | V3 UR1                             | Koruma: Küme Paylaşılan birimlerinde (CSV), NFS ve SAN depolamada VMware VM 'Leri   <br><br>     Kurtarma: sanal makine, dosya ve klasörlerin öğe düzeyinde kurtarılması yalnızca Windows, birimler, sanal sabit sürücüler için kullanılabilir <br><br>    VMware vApps desteklenmez. |

>[!NOTE]
> MABS, sanal makinelerin geçiş disklerini veya uzak bir VHD kullanan sanal makinelerin yedeklenmesini desteklemez. Bu senaryolarda, MABS kullanarak Konuk düzeyinde yedekleme kullanmanızı ve verileri yedeklemek için sanal makineye bir aracı yüklemenizi öneririz.

## <a name="linux"></a>Linux

| **İş yükü** | **Sürüm**                               | **Azure Backup Sunucusu yükleme**                      | **Desteklenen Azure Backup Sunucusu** | **Koruma ve kurtarma**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) veya [VMware](backup-azure-backup-server-vmware.md) konuğu olarak çalışan Linux | Fiziksel sunucu, şirket içi Hyper-V VM, VMware 'de Windows VM | V3 UR1                             | Hyper-V Windows Server 2012 R2, Windows Server 2016 veya Windows Server 2019 üzerinde çalışıyor olmalıdır. Koruma: tüm sanal makine   <br><br>   Kurtarma: Tüm sanal makine   <br><br>    Yalnızca dosya tutarlı anlık görüntüler desteklenir.    <br><br>   Desteklenen Linux dağıtımları ve sürümlerinin tüm listesi için bkz. [Azure tarafından onaylanan dağıtımların Linux](../virtual-machines/linux/endorsed-distros.md)makalesi. |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute desteği

Azure ExpressRoute üzerinden verilerinizi, genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi ile birlikte yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.

Ortak eşleme ile: aşağıdaki etki alanlarına/adreslere erişim sağlayın:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft eşlemesi ile aşağıdaki hizmetleri/bölgeleri ve ilgili topluluk değerlerini seçin:

* Azure Active Directory (12076:5060)
* Microsoft Azure bölgesi (Kurtarma Hizmetleri kasanızın konumuna göre)
* Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)

Daha fazla bilgi için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md).

>[!NOTE]
>Ortak eşleme, yeni devreler için kullanım dışıdır.

## <a name="cluster-support"></a>Küme desteği

Azure Backup Sunucusu, aşağıdaki kümelenmiş uygulamalardaki verileri koruyabilir:

* Dosya sunucuları

* SQL Server

* Hyper-V-ölçekli MABS koruma Aracısı kullanarak bir Hyper-V kümesini koruyorduysanız, korunan Hyper-V iş yükleri için ikincil koruma ekleyemezsiniz.

* Exchange Server-Azure Backup Sunucusu, desteklenen Exchange Server sürümleri (küme sürekli çoğaltma) için paylaşılmayan disk kümelerini koruyabilir ve ayrıca yerel sürekli çoğaltma için yapılandırılmış Exchange Server 'ı da koruyabilir.

* SQL Server-Azure Backup Sunucusu, Küme Paylaşılan birimlerinde (CSV) barındırılan SQL Server veritabanlarının yedeklenmesini desteklemez.

>[!NOTE]
>MABS yalnızca küme paylaşılan birimlerindeki (CSV) Hyper-V sanal makinelerinin korunmasını destekler. CSV'lerde barındırılan diğer iş yüklerinin korunması desteklenmiyor.

Azure Backup Sunucusu, MABS sunucusuyla aynı etki alanında ve alt veya güvenilen bir etki alanında bulunan küme iş yüklerini koruyabilir. Güvenilmeyen etki alanları veya çalışma gruplarındaki veri kaynaklarını korumak istiyorsanız, tek bir sunucu için NTLM veya sertifika kimlik doğrulaması ya da yalnızca bir küme için sertifika kimlik doğrulaması kullanın.

## <a name="data-protection-issues"></a>Veri koruma sorunları

* Hyper-V VSS yazıcısı paylaşılan VHD 'ler tarafından yedeklenen birimleri yedekleyemiyorsa, MABS, paylaşılan sürücüler (diğer VM 'lere bağlı olabilecek) kullanarak VM 'Leri yedeklemez.

* Paylaşılan bir klasörü koruduğunuzda, paylaşılan klasörün yolu birimdeki mantıksal yolu içerir. Paylaşılan klasörü taşırsanız koruma işlemi başarısız olur. Korumalı bir paylaşılan klasörü taşımanız gerekiyorsa, koruma grubundan kaldırın ve taşıdıktan sonra korumaya ekleyin.  Ayrıca, şifreleme dosya sistemi (EFS) kullanan bir birimdeki korumalı bir veri kaynağının yolunu değiştirirseniz ve yeni dosya yolu 5120 karakteri aşarsa veri koruması başarısız olur.

* Korumalı bir bilgisayarın etki alanını değiştiremez ve kesintiye uğramadan korumaya devam edebilirsiniz. Ayrıca, korumalı bir bilgisayarın etki alanını değiştirip, yeniden koruma sırasında mevcut çoğaltmaları ve kurtarma noktalarını bu bilgisayarla ilişkilendiremezsiniz. Korumalı bir bilgisayarın etki alanını değiştirmeniz gerekiyorsa, önce bilgisayardaki veri kaynaklarını korumadan kaldırın. Ardından, yeni bir etki alanı olduktan sonra bilgisayardaki veri kaynağını koruyun.

* Korumalı bir bilgisayarın adını değiştiremezsiniz ve kesintiye uğramadan korumaya devam edebilirsiniz. Ayrıca, korunan bir bilgisayarın adını değiştirip, yeniden koruma sırasında mevcut çoğaltmaları ve kurtarma noktalarını bu bilgisayarla ilişkilendiremezsiniz. Korumalı bilgisayarın adını değiştirmeniz gerekiyorsa, önce bilgisayardaki veri kaynaklarının korumasını kaldırın. Bilgisayarı yeniden adlandırdıktan sonra bilgisayardaki veri kaynağını korumaya alın.

* MABS, koruma aracısının yüklenmesi sırasında korumalı bilgisayarın saat dilimini otomatik olarak belirler. Korumalı bir bilgisayar koruma yapılandırıldıktan sonra farklı bir saat dilimine taşınırsa Denetim Masası'nda bilgisayar saatini değiştirdiğinizden emin olun. Ardından, MABS veritabanındaki saat dilimini güncelleştirin.

* MABS, MABS sunucusuyla aynı etki alanındaki veya alt ve güvenilen etki alanlarındaki iş yüklerini koruyabilir. Ayrıca, NTLM veya sertifika kimlik doğrulaması kullanarak çalışma gruplarındaki ve güvenilmeyen etki alanlarındaki aşağıdaki iş yüklerini koruyabilirsiniz:

  * SQL Server
  * Dosya Sunucusu
  * Hyper-V

  Bu iş yükleri, tek bir sunucuda veya küme yapılandırmasında çalışıyor olabilir. Güvenilen bir etki alanında olmayan bir iş yükünü korumak için, nelerin desteklendiklerinin ve hangi kimlik doğrulamasının gerektiği hakkında tam Ayrıntılar için bkz. [çalışma gruplarında ve güvenilmeyen etki alanlarında bilgisayarları hazırlama](/system-center/dpm/prepare-environment-for-dpm) .

## <a name="unsupported-data-types"></a>Desteklenmeyen veri türleri

MABS, aşağıdaki veri türlerini korumayı desteklemez:

* Sabit bağlantılar

* DFS bağlantıları ve birleşim noktaları da dahil olmak üzere yeniden ayrıştırma noktaları

* Bağlama noktası meta verileri - Bir koruma grubu bağlama noktaları içeren verilere sahip olabilir. Bu durumda DPM, bağlama noktasının hedefi olan bağlı birimi korur, ancak bağlama noktası meta verilerini korumaz. Bağlama noktaları içeren verileri kurtardığınızda, bağlama noktası hiyerarşinizi el ile yeniden oluşturmanız gerekir.

* Bağlı birimler içindeki bağlı birimlerdeki veriler

* Geri Dönüşüm Kutusu

* Disk belleği dosyaları

* Sistem Birimi Bilgileri klasörü. Bir bilgisayarın sistem bilgilerini korumak için bilgisayarın sistem durumunu koruma grubu üyesi olarak seçmeniz gerekir.

* NTFS olmayan birimler

* Windows Vista'dan sabit bağlantılar veya sembolik bağlantılar içeren dosyalar.

* DS barındıran dosya paylaşımlarındaki veriler (Kullanıcı profili diskleri)

* Aşağıdaki öznitelik bileşimlerine sahip dosyalar:

  * Şifreleme ve yeniden ayrıştırma

  * Şifreleme ve Tek Örnekli Depo (SIS)

  * Şifreleme ve büyük küçük harf duyarlılığı

  * Şifreleme ve seyrek

  * Büyük küçük harf duyarlılığı ve SIS

  * Sıkıştırma ve SIS

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Azure Backup Server veya System Center DPM ile yedekleme için destek matrisi](backup-support-matrix-mabs-dpm.md)