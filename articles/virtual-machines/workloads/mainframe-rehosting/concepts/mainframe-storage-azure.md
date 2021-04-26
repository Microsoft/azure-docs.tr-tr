---
title: Ana bilgisayar depolama alanını Azure Storage 'a taşıma
description: Büyük oranda ölçeklenebilir Azure depolama kaynakları, ana bilgisayar tabanlı kuruluşların IBM Z14 uygulamalarına geçiş ve modernleştirin yardımcı olabilir.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954369"
---
# <a name="move-mainframe-storage-to-azure"></a>Ana bilgisayar depolama alanını Azure 'a taşıma

Microsoft Azure ana bilgisayar iş yüklerini çalıştırmak için, ana bilgisayarınızın yeteneklerini Azure ile nasıl karşılaştırılacağını bilmeniz gerekir. Yüksek düzeyde ölçeklenebilir depolama kaynakları, kuruluşların, kullandıkları uygulamaları bırakmadan modernleştirin olmaya başlamasını sağlayabilir.

Azure, IBM Z14 tabanlı sistemlerle karşılaştırılabilen ana bilgisayar benzeri özellikler ve depolama kapasitesi (Bu yazarken en güncel model) sağlar. Bu makale, Azure 'da nasıl karşılaştırılabilir sonuçlar elde etmek için size bildirir.

## <a name="mainframe-storage-at-a-glance"></a>Tek bakışta ana bilgisayar depolaması

IBM ana bilgisayar, depolamayı iki şekilde biçimlendirir. Birincisi, doğrudan erişimli bir depolama cihazdır (DAVSD). İkincisi sıralı bir depodır. Ana bilgisayar, depolamayı yönetmek için veri tesis depolama yönetimi alt sistemi (DFSMS) sağlar. Çeşitli depolama cihazlarına veri erişimini yönetir.

[Davsd](https://en.wikipedia.org/wiki/Direct-access_storage_device) , verilere doğrudan erişim için benzersiz bir adresin kullanılmasına izin veren ikincil (bellek içi olmayan) depolama alanı için ayrı bir cihaza başvurur. Başlangıçta, dönen disklere, manyetik kuruma veya veri hücrelerine uygulanan BASD terimi. Ancak artık terim, katı hal depolama cihazları (SSD), depolama alanı ağları (San 'Lar), ağa bağlı depolama (NAS) ve optik sürücüler için de uygulanabilir. Bu belgenin amaçları doğrultusunda, DAVSD, dönen diskler, San 'Lar ve SSD 'Ler anlamına gelir.

PASD depolamanın aksine, bir ana bilgisayar üzerindeki sıralı depolama, verilere bir başlangıç noktasından erişildiği ve sonra bir satıra okunan veya yazılan bant sürücüleri gibi cihazlara başvurur.

Depolama cihazları genellikle bir fiber bağlantı (FıCON) kullanılarak bağlanır veya bir sunucu üzerindeki, hiper yönetici ile bir sunucudaki bölümler arasındaki yüksek hızlı iletişimler için bir IBM teknolojisi olan [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)kullanarak doğrudan ana bilgisayarın g/ç veri yolunda erişilir.

Çoğu ana bilgisayar sistemi, depolamayı iki türe ayırır:

- Günlük işlemler için *çevrimiçi depolama* (etkin depolama olarak da bilinir) gereklidir. Bu amaçla genellikle BASD depolama kullanılır. Ancak, günlük bant yedeklemeleri (mantıksal veya fiziksel) gibi sıralı depolama da bu amaçla kullanılabilir.

- *Arşiv depolama* (soğuk depolama olarak da bilinir), belirli bir zamanda takılmak üzere garanti edilmez. Bunun yerine, bağlanır ve gerektiğinde erişilir. Arşiv depolama, genellikle depolama için sıralı bant yedeklemeleri (mantıksal veya fiziksel) kullanılarak uygulanır.

## <a name="mainframe-versus-io-latency-and-iops"></a>Ana bilgisayar ile GÇ gecikme süresi ve ıOPS

Ana bilgisayarlar genellikle yüksek performanslı GÇ ve düşük GÇ gecikme süresi gerektiren uygulamalar için kullanılır. Bu, GÇ cihazlarına ve HiperSockets FıCON bağlantılarını kullanarak bunu yapabilir. HiperSockets, uygulamaları ve cihazları doğrudan bir ana bilgisayarın GÇ kanalına bağlamak için kullanıldığında, mikrosaniye cinsinden gecikme elde edilebilir.

## <a name="azure-storage-at-a-glance"></a>Azure depolama bir bakışta

Depolama için Azure hizmet olarak altyapı ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) seçenekleri, benzer ana bilgisayar kapasitesi sağlar.

Microsoft, Azure 'da barındırılan uygulamalar için petabaytlarca değer depolama alanı sunar ve çeşitli depolama seçenekleriniz vardır. Bu, yığın depolama ve arşivler için yüksek performanslı düşük maliyetli blob depolamaya kadar SSD depolama alanından bu yana uzanır. Ayrıca, Azure, bir anabilgisayar ortamında ayarlanmakta daha fazla çaba alan bir depolama için veri artıklığı seçeneği sağlar.

Azure Storage, Azure [diskleri](../../../managed-disks-overview.md), [Azure dosyaları](../../../../storage/files/storage-files-introduction.md)ve [Azure Blob 'ları](../../../../storage/blobs/storage-blobs-overview.md) olarak kullanılabilir ve aşağıdaki tabloda özetlenmiştir. [Her birinin ne zaman kullanılacağı](../../../../storage/common/storage-introduction.md)hakkında daha fazla bilgi edinin.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Tür</th><th>Description</th><th>Şunları yapmak istediğinizde kullanın:</th></tr>
</thead>
<tbody>
<tr><td>Azure Dosyaları
</td>
<td>
Bir SMB arabirimi, istemci kitaplıkları ve her yerden depolanan dosyaların erişimine izin veren bir <a href="/rest/api/storageservices/file-service-rest-api">rest</a> arabirimi sağlar.
</td>
<td><ul>
<li>Uygulama, Azure 'da çalışan ve diğer uygulamalar arasında veri paylaşmak için yerel dosya sistemi API 'Lerini kullandığında uygulamayı buluta taşıyın ve taşıyın.</li>
<li>Birçok VM 'den erişilmesi gereken geliştirme ve hata ayıklama araçlarını depolayın.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobları
</td>
<td>Yapılandırılmamış veri depolanmasına ve Blok Bloblarında çok büyük ölçekte erişilmesine izin veren istemci kitaplıkları ve <a href="/rest/api/storageservices/blob-service-rest-api">rest</a> arabirimi sağlar. Ayrıca kurumsal büyük veri analizi çözümleri için <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage 2.</a> destekler.
</td>
<td><ul>
<li>Bir uygulamada akış ve rastgele erişim senaryolarını destekler.</li>
<li>Uygulama verilerine her yerden erişin.</li>
<li>Azure üzerinde bir kurumsal veri Gölü oluşturun ve büyük veri analizi gerçekleştirin.</li>
</ul></td>
</tr>
<tr><td>Azure Diskleri
</td>
<td>, Verilerin kalıcı olarak depolanmasına ve bağlı bir sanal sabit diskten erişilmesine izin veren istemci kitaplıkları ve <a href="/rest/api/compute/disks">rest</a> arabirimi sağlar.
</td>
<td><ul>
<li>Kalıcı disklere veri okumak ve yazmak için yerel dosya sistemi API 'Leri kullanan uygulamaları kaldırın ve taşıyın.</li>
<li>Diskin eklendiği VM dışından erişilmesi gerekmeyen verileri depolayın.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure sık erişimli (çevrimiçi) ve soğuk (Arşiv) depolama

Belirli bir sistem için depolama türü, depolama boyutu, üretilen iş ve ıOPS dahil olmak üzere sistemin gereksinimlerine bağlıdır. Bir ana bilgisayarda PASD türü depolama için, Azure 'daki uygulamalar genellikle Azure diskleri sürücü depolamayı kullanır. Ana bilgisayar arşiv depolaması için, Azure 'da BLOB depolama kullanılır.

SSD 'Ler, Azure 'da en yüksek depolama performansını sağlar. Aşağıdaki seçenekler kullanılabilir (Bu belgenin yazıldığı itibariyle):

| Tür         | Boyut           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB ila 64 TB  | 1.200-160.000 ıOPS |
| Premium SSD  | 32 GB ila 32 TB | 12-15.000 ıOPS     |
| Standart SSD | 32 GB ila 32 TB | 12-2.000 ıOPS      |

BLOB depolama, Azure 'da en büyük depolama birimini sağlar. Azure, depolama boyutuna ek olarak hem yönetilen hem de yönetilmeyen depolama alanı sunar. Yönetilen depolama ile Azure, temel alınan depolama hesaplarını yönetmeyi üstlenir. Yönetilmeyen depolama ile Kullanıcı, depolama gereksinimlerini karşılamak üzere uygun boyuttaki Azure depolama hesaplarını ayarlamanın sorumluluğunu alır.

## <a name="next-steps"></a>Sonraki adımlar

- [Ana bilgisayar geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure sanal makinelerinde Ana bilgisayar yeniden barındırma](../overview.md)
- [Anabilgisayar işlem hareketini Azure 'a taşıma](mainframe-compute-Azure.md)
- [Azure Blob 'Ları, Azure dosyalarını veya Azure disklerini ne zaman kullanacağınızı belirleme](../../../../storage/common/storage-introduction.md)
- [Azure VM iş yükleri için yönetilen diskleri Standart SSD](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM kaynakları

- [IBM Z üzerinde Parallel Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS ve kuponu: temel bilgilerin ötesinde](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [DB2 pureScale özelliği yüklemesi için gerekli kullanıcıları oluşturma](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-örnek Oluştur komutu](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 Porescale kümelenmiş veritabanı çözümü](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Kamu

- [Anabilgisayar uygulamaları için Microsoft Azure Kamu Bulutu](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft ve Fedrampa](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Daha fazla geçiş kaynağı

- [Azure sanal veri merkezi yükselt ve kaydırma Kılavuzu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS Iscsı](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)