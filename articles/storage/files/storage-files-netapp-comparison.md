---
title: Azure dosyaları ve Azure NetApp Files karşılaştırması | Microsoft Docs
description: Azure dosyaları ve Azure NetApp Files karşılaştırması.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 381fe00e835f0e359a2347372cbc2544a2b211ca
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364235"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure dosyaları ve Azure NetApp Files karşılaştırması

Bu makalede, Azure dosyaları ve Azure NetApp Files karşılaştırması sunulmaktadır. 

Bulut dosya depolaması gerektiren çoğu iş yükü, Azure dosyalarında veya Azure NetApp Files iyi çalışır. İş yükünüze en iyi şekilde uyum sağlamak için, bu makalede sağlanan bilgileri gözden geçirin. Daha fazla bilgi için bkz. [Azure dosyaları](https://docs.microsoft.com/azure/storage/files/) ve [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) belgeleri.

## <a name="features"></a>Özellikler

| Kategori | Azure Dosyaları | Azure NetApp Files |
|---------|-------------------------|---------|
| Açıklama | [Azure dosyaları](https://azure.microsoft.com/services/storage/files/) , yerinde veri güncelleştirmeleriyle rastgele erişim iş yükleri için optimize edilmiş, tam olarak yönetilen, yüksek oranda kullanılabilir ve kurumsal düzeyde bir hizmettir.<br><br> Azure dosyaları, Azure Blobları gibi diğer hizmetlerle aynı Azure depolama platformunda oluşturulmuştur. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , gelişmiş veri yönetimi özelliklerine ihtiyaç duyan en zorlu, yüksek performanslı ve düşük gecikme süreli iş yüklerini işleyebilen, tam olarak yönetilen, yüksek oranda kullanılabilir, kurumsal düzeyde bir NAS hizmetidir. Bu, olmadan "un-geçirilip geçirilemeyeceğini denetleyin" kabul edilen iş yüklerinin geçirilmesini mümkün kılar.<br><br>  ANF, tutarlı bir Azure deneyimi ve bir şirket içi performans gibi Azure veri merkezi 'nde çalışan ONTAP depolama işletim sistemi ile NetApp 'in çıplak sürümüyle oluşturulmuştur. |
| Protokoller | Premium<br><ul><li>SMB 2,1, 3,0</li><li>NFS 4,1 (Önizleme)</li><li>REST</li></ul><br>Standart<br><ul><li>SMB 2,1, 3,0</li><li>REST</li></ul><br> Daha fazla bilgi için bkz. [kullanılabilir dosya paylaşma protokolleri](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols). | Tüm Katmanlar<br><ul><li>SMB 1, 2. x, 3. x</li><li>NFS 3,0, 4,1</li><li>Çift protokol erişimi (NFSv3/SMB)</li></ul><br> Daha fazla bilgi edinmek için bkz. [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes), [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)veya [çift protokol](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) birimleri oluşturma. |
| Bölge kullanılabilirliği | Premium<br><ul><li>30 + bölge</li></ul><br>Standart<br><ul><li>Tüm bölgeler</li></ul><br> Daha fazla bilgi edinmek için bkz. [Bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Tüm Katmanlar<br><ul><li>25 + bölge</li></ul><br> Daha fazla bilgi edinmek için bkz. [Bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Yedeklilik | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standart<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Daha fazla bilgi için bkz. [Artıklık](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy). | Tüm Katmanlar<br><ul><li>Yerleşik yerel HA</li><li>[Bölgeler arası çoğaltma](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level Sözleşmesi (SLA)<br><br> Azure dosyaları ve Azure NetApp Files SLA 'ların farklı hesaplandığını unutmayın. | [Azure dosyaları için SLA](https://azure.microsoft.com/support/legal/sla/storage/) | [Azure NetApp Files için SLA](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based kimlik doğrulaması ve yetkilendirme | SMB<br><ul><li>Active Directory Etki Alanı Hizmetleri (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Yalnızca SMB protokolü kullanılırken, tanımlandığına yönelik kimlik doğrulama desteklenir. Daha fazla bilgi için bkz. [SSS](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control). | SMB<br><ul><li>Active Directory Etki Alanı Hizmetleri (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB Dual Protocol<ul><li>/LDAP tümleştirmesi ekler</li></ul><br>NFSv3/NFSv 4.1<ul><li>/LDAP tümleştirmesi ekler (gelecek)</li><li>NFS genişletilmiş grupları (gelen)</li></ul><br> Daha fazla bilgi için bkz. [SSS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs). |
| Şifreleme | SMB<br><ul><li>Bekleyen şifreleme (AES 256) ile müşteri veya Microsoft tarafından yönetilen anahtarlar</li><li>AES 256 veya RC4 kullanarak Kerberos şifrelemesi-HMAC</li><li>Aktarım sırasında şifreleme</li></ul><br>NFS<br><ul><li>Bekleyen şifreleme (AES 256) ile müşteri veya Microsoft tarafından yönetilen anahtarlar</li></ul><br>REST<br><ul><li>Bekleyen şifreleme (AES 256) ile müşteri veya Microsoft tarafından yönetilen anahtarlar</li><li>Aktarım sırasında şifreleme</li></ul><br> Daha fazla bilgi için bkz. [güvenlik](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security). | Tüm protokoller<br><ul><li>Microsoft tarafından yönetilen anahtarlarla bekleyen şifreleme (AES 256) </li></ul><br>NFS 4,1<ul><li>AES 256 ile Kerberos kullanarak geçişte şifreleme</li></ul><br> Daha fazla bilgi için bkz. [GÜVENLIK SSS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs). |
| Erişim Seçenekleri | <ul><li>İnternet</li><li>Güvenli VNet erişimi</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure Dosya Eşitleme</li></ul><br> Daha fazla bilgi için bkz. [ağ değerlendirmeleri](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview). | <ul><li>Güvenli VNet erişimi</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Genel dosya önbelleği](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Önbelleği](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Daha fazla bilgi için bkz. [ağ değerlendirmeleri](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies). |
| Veri Koruma  | <ul><li>Artımlı anlık görüntüler</li><li>Dosya/Dizin Kullanıcı kendi kendine geri yükleme</li><li>Yeni konuma geri yükle</li><li>Yerinde tekrar çevir</li><li>Paylaşma düzeyi geçici silme</li><li>Azure Backup tümleştirme</li></ul><br> Daha fazla bilgi için bkz. [Azure dosyaları veri koruma yeteneklerini geliştirir](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Anlık görüntüler (255/birim)</li><li>Dosya/Dizin Kullanıcı kendi kendine geri yükleme</li><li>Yeni birime geri yükle</li><li>Yerinde tekrar çevir</li><li>[Bölgeler arası çoğaltma](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (Genel Önizleme)</li></ul><br> Daha fazla bilgi için bkz. [Azure NetApp Files anlık görüntüleri nasıl çalışır](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Geçiş araçları  | <ul><li>Azure Data Box</li><li>Azure Dosya Eşitleme</li><li>Depolama geçiş hizmeti</li><li>AzCopy</li><li>Robocopy</li></ul><br> Daha fazla bilgi için bkz. [Azure dosya paylaşımlarına geçiş](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview). | <ul><li>[Genel dosya önbelleği](https://cloud.netapp.com/global-file-cache/azure)</li><li>[Cloudsync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Depolama geçiş hizmeti</li><li>AzCopy</li><li>Robocopy</li><li>Uygulama tabanlı (örneğin, HSR, Data Guard, AOAG)</li></ul> |
| Katmanlar | <ul><li>Premium</li><li>İşlem için Iyileştirilmiş</li><li>Sık Erişimli</li><li>Seyrek Erişim</li></ul><br> Daha fazla bilgi için bkz. [depolama katmanları](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers). | <ul><li>Ultra</li><li>Premium</li><li>Standart</li></ul><br> Tüm Katmanlar alt-en düşük gecikme süresi sağlar.<br><br> Daha fazla bilgi için bkz. [hizmet düzeyleri](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) ve [performans konuları](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations). |
| Fiyatlandırma | [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files fiyatlandırması](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Ölçeklenebilirlik ve performans

| Kategori | Azure Dosyaları | Azure NetApp Files |
|---------|---------|---------|
| En düşük paylaşma/birim boyutu | Premium<br><ul><li>100 GiB</li></ul><br>Standart<br><ul><li>1 GiB</li></ul> | Tüm Katmanlar<br><ul><li>100 GiB (en düşük kapasite havuzu boyutu: 4 TiB)</li></ul> |
| En fazla paylaşma/birim boyutu | Premium<br><ul><li>100 TiB</li></ul><br>Standart<br><ul><li>100 TiB</li></ul> | Tüm Katmanlar<br><ul><li>100 TiB (500-TiB kapasite havuzu sınırı)</li></ul><br>Azure NetApp hesabı başına en fazla 12,5 PiB. |
| Maksimum Share/Volume ıOPS | Premium<br><ul><li>En fazla 100 k</li></ul><br>Standart<br><ul><li>En fazla 10.000</li></ul> | Ultra ve Premium<br><ul><li>450k 'a kadar </li></ul><br>Standart<br><ul><li>En fazla 320k</li></ul> |
| En fazla paylaşma/birim verimlilik | Premium<br><ul><li>En fazla 10 GiB/sn</li></ul><br>Standart<br><ul><li>En fazla 300 MIB/sn</li></ul> | Ultra ve Premium<br><ul><li>4,5 GiB/sn 'ye kadar</li></ul><br>Standart<br><ul><li>En çok 3.2 GiB/sn</li></ul> |
| En Büyük Dosya Boyutu | Premium<br><ul><li>4 TiB</li></ul><br>Standart<br><ul><li>1 TiB</li></ul> | Tüm Katmanlar<br><ul><li>16 TiB</li></ul> |
| Dosya başına en fazla ıOPS | Premium<br><ul><li>8.000 kadar</li></ul><br>Standart<br><ul><li>1.000</li></ul> | Tüm Katmanlar<br><ul><li>En fazla birim sınırı</li></ul> |
| Dosya başına en fazla aktarım hızı | Premium<br><ul><li>300 MIB/s (çok kanallı SMB ile 1 GiB/sn 'ye kadar)</li></ul><br>Standart<br><ul><li>60 MIB/sn</li></ul> | Tüm Katmanlar<br><ul><li>En fazla birim sınırı</li></ul> |
| SMB Çok Kanal | Evet ([Önizleme](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | Yes |
| Gecikme süresi | Tek milisaniyelik en düşük gecikme süresi (küçük GÇ için 2ms-3ms) | Alt milisaniyelik en düşük gecikme süresi (rastgele GÇ için <1 MS)<br><br>Daha fazla bilgi için bkz. [performans değerlendirmeleri](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux). |

Ölçeklenebilirlik ve performans hedefleri hakkında daha fazla bilgi için bkz. [Azure dosyaları](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) ve [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits).

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure Dosyalar belgeleri](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files belgeleri](https://docs.microsoft.com/azure/azure-netapp-files/)
