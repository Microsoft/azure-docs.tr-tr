---
title: Azure dosya paylaşımlarına geçirme
description: Azure dosya paylaşımlarına geçişler ve geçiş kılavuzlarınızı bulma hakkında bilgi edinin.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785226"
---
# <a name="migrate-to-azure-file-shares"></a>Azure dosya paylaşımlarına geçirme

Bu makalede, Azure dosya paylaşımlarına geçişin temel yönleri ele alınmaktadır.

Bu makale, geçiş temelleri ve geçiş kılavuzlarından oluşan bir tablo içerir. Bu kılavuzlar, dosyalarınızı Azure dosya paylaşımlarına taşımanıza yardımcı olur. Kılavuzlar, verilerinizin nerede olduğu ve taşıdığınız dağıtım modelinin (yalnızca bulut veya karma) temel alınarak düzenlenmiştir.

## <a name="migration-basics"></a>Geçiş temelleri

Azure 'da birden fazla kullanılabilir bulut depolama türü vardır. Azure 'a dosya geçişlerinin temel bir yönü, verilerinize yönelik olarak hangi Azure Storage seçeneğinin doğru olduğunu belirliyor.

[Azure dosya paylaşımları](storage-files-introduction.md) , genel amaçlı dosya verileri için uygundur. Bu veriler, için bir şirket içi SMB veya NFS paylaşımının kullanıldığı her şeyi içerir. [Azure dosya eşitleme](../file-sync/file-sync-planning.md)ile, şirket Içi Windows Server çalıştıran sunuculardaki çeşitli Azure dosya paylaşımlarının içeriğini önbelleğe alabilirsiniz.

Şu anda bir şirket içi sunucuda çalışan bir uygulama için, dosyaları bir Azure dosya paylaşımında depolamak iyi bir seçenek olabilir. Uygulamayı Azure 'a taşıyabilir ve Azure dosya paylaşımlarını paylaşılan depolama alanı olarak kullanabilirsiniz. Ayrıca, bu senaryo için [Azure disklerini](../../virtual-machines/managed-disks-overview.md) de göz önünde bulundurun.

Bazı bulut uygulamaları SMB 'ye veya makine yerel veri erişimi veya paylaşılan erişim 'e bağlı değildir. Bu uygulamalar için, [Azure Blob 'ları](../blobs/storage-blobs-overview.md) gibi nesne depolama genellikle en iyi seçenektir.

Herhangi bir geçişte anahtar, dosyalarınızı geçerli depolama konumlarından Azure 'a taşırken uygun olan tüm dosya uygunluğunu yakalamamaktır. Azure Storage seçeneğinin ne kadar aslına göre desteklediği ve senaryonuzun ne kadar uygunluk, doğru Azure depolama alanını seçmenize yardımcı olur. Genel amaçlı dosya verileri, geleneksel olarak dosya meta verilerine bağlıdır. Uygulama verileri çalışmayabilir.

Bir dosyanın iki temel bileşeni aşağıda verilmiştir:

- **Veri akışı**: dosya içeriğini depolayan bir dosyanın veri akışı.
- **Dosya meta verileri**: dosya meta verileri aşağıdaki alt bileşenlere sahiptir:
   * Salt okuma gibi dosya öznitelikleri
   * *NTFS izinleri* veya *dosya ve klasör ACL 'leri* olarak başvurulabilen dosya izinleri
   * Zaman damgaları, en önemlisi oluşturma ve son değiştirilme zaman damgaları
   * Daha büyük miktarlarda standart olmayan özellikleri depolamak için bir alan olan alternatif bir veri akışı

Bir geçişte dosya uygunluk özelliği şu şekilde tanımlanabilir:

- Kaynak üzerinde geçerli tüm dosya bilgilerini depolayın.
- Geçiş Aracı ile dosyaları aktarın.
- Dosyaları geçişin hedef deposunda depolayın.

Geçişinizin sorunsuz bir şekilde yapıldığından emin olmak için [gereksinimlerinize uygun en iyi kopyalama aracını](#migration-toolbox) belirleyip bir depolama hedefini kaynağınıza eşleştirin.

Önceki bilgilerin hesaba alınması, Azure 'daki genel amaçlı dosyalar için hedef depolamanın [Azure dosya paylaşımları](storage-files-introduction.md)olduğunu görebilirsiniz.

Azure Bloblarındaki nesne depolamadan farklı olarak, bir Azure dosya paylaşımının dosya meta verilerini yerel olarak depolayabileceği. Azure dosya paylaşımları dosya ve klasör hiyerarşisini, özniteliklerini ve izinlerini de korur. NTFS izinleri, şirket içinde olduklarından dosyalar ve klasörler üzerinde depolanabilir.

Active Directory bir kullanıcısı, şirket içi etki alanı denetleyicisi olan bir Azure dosya paylaşımında yerel olarak erişebilir. Bu nedenle Azure Active Directory Domain Services (Azure AD DS) kullanıcısı olabilir. Her biri, paylaşma izinlerine ve dosya ve klasör ACL 'Lerine göre erişim sağlamak için geçerli kimliğini kullanır. Bu davranış, şirket içi dosya paylaşımıyla bağlantı kurma kullanıcısına benzer.

Alternatif veri akışı, şu anda Azure dosya paylaşımındaki bir dosyada depolanmayan dosya uygunlukta birincil yönüdür. Azure Dosya Eşitleme kullanıldığında şirket içinde korunur.

Azure [ad kimlik doğrulaması](storage-files-identity-auth-active-directory-enable.md) ve Azure [AD DS kimlik doğrulaması](storage-files-identity-auth-active-directory-domain-service-enable.md) hakkında daha fazla bilgi edinmek için Azure dosya paylaşımları.

## <a name="migration-guides"></a>Geçiş kılavuzları

Aşağıdaki tabloda, ayrıntılı geçiş kılavuzlarını listelenmektedir.

Tabloyu kullanma:

1. Dosyalarınızın Şu anda depolandığı kaynak sistem için satırı bulun.

1. Şu hedeflerden birini seçin:

   - Şirket içinde Azure dosya paylaşımlarının içeriğini önbelleğe almak için Azure Dosya Eşitleme kullanan karma dağıtım
   - Buluttaki Azure dosya paylaşımları

   Seçiminizden eşleşen hedef sütunu seçin.

1. Kaynak ve hedefin kesişimi içinde, bir tablo hücresinde kullanılabilir geçiş senaryoları listelenir. Ayrıntılı geçiş kılavuzuna doğrudan bağlanacak bir tane seçin.

Bağlantı olmadan bir senaryoda, henüz yayımlanmış bir geçiş kılavuzu yoktur. Bu tabloyu güncelleştirmeler için zaman içinde işaretleyin. Yeni Kılavuzlar, kullanılabilir olduğunda yayımlanacak.

| Kaynak | Hedef: </br>Karma dağıtım | Hedef: </br>Yalnızca bulutta dağıtım |
|:---|:--|:--|
| | Araç birleşimi:| Araç birleşimi: |
| Windows Server 2012 R2 ve üzeri | <ul><li>[Azure Dosya Eşitleme](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure Dosya Eşitleme ve Azure veri kutusu](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>RoboCopy aracılığıyla bağlı bir Azure dosya paylaşımında</li><li>Azure Dosya Eşitleme aracılığıyla</li></ul> |
| Windows Server 2012 ve öncesi | <ul><li>Veri kutusu aracılığıyla ve son sunucu işletim sistemine Azure Dosya Eşitleme</li><li>Depolama geçiş hizmeti 'ni Azure Dosya Eşitleme ile son sunucuya kullanarak karşıya yükleyin</li></ul> | <ul><li>Azure Dosya Eşitleme ile son sunucuya depolama geçiş hizmeti üzerinden</li><li>RoboCopy aracılığıyla bağlı bir Azure dosya paylaşımında</li></ul> |
| Ağa bağlı depolama (NAS) | <ul><li>[Azure Dosya Eşitleme karşıya yükleme yoluyla](storage-files-migration-nas-hybrid.md)</li><li>[Veri kutusu + Azure Dosya Eşitleme aracılığıyla](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Veri kutusu aracılığıyla](storage-files-migration-nas-cloud-databox.md)</li><li>RoboCopy aracılığıyla bağlı bir Azure dosya paylaşımında</li></ul> |
| Linux/Samba | <ul><li>[Azure Dosya Eşitleme ve RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy aracılığıyla bağlı bir Azure dosya paylaşımında</li></ul> |
| Microsoft Azure StorSimple Cloud gereç 8100 veya StorSimple Cloud Appliance 8600 | <ul><li>[Adanmış veri geçişi bulut hizmeti aracılığıyla](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure Dosya Eşitleme aracılığıyla](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Geçiş Araç kutusu

### <a name="file-copy-tools"></a>Dosya kopyalama araçları

Microsoft ve diğerleri tarafından kullanılabilen birkaç dosya kopyalama aracı vardır. Geçiş senaryonuz için doğru aracı seçmek üzere şu temel soruları göz önünde bulundurmanız gerekir:

* Araç, dosya kopyanız için kaynak ve hedef konumları destekliyor mu?

* Araç, kaynak ve hedef depolama konumları arasında ağ yolunuzu veya kullanılabilir protokollerinizi (REST, SMB veya NFS) destekliyor mu?

* Araç, kaynak ve hedef konumlarınız tarafından desteklenen gerekli dosya kalitesini korusun mu?

    Bazı durumlarda, hedef depolama alanı, kaynağınıza göre aynı uygunluğu desteklemez. Hedef depolama gereksinimleriniz için yeterliyse, araç yalnızca hedefin dosya uygunluğu özelliklerine uymalıdır.

* Aracın geçiş stratejinize sığdırmasına izin veren özellikleri var mı?

    Örneğin, aracın kapalı kalma süresini en aza indirmenize izin verip etmediğini düşünün.
    
    Bir araç, kaynağı bir hedefe yansıtmaya yönelik bir seçeneği desteklediğinde, kaynak erişilebilir durumda kaldığında genellikle aynı kaynak ve hedefte birden çok kez çalıştırabilirsiniz.

    Aracı ilk kez çalıştırdığınızda verilerin toplu bölümünü kopyalar. Bu ilk çalıştırma en son bir kez sürebilir. Bu, genellikle veri kaynağını iş işlemleriniz için çevrimdışı duruma almak istediğinizden daha uzun sürer.

    Kaynağı bir hedefe yansıtarak ( **Robocopy/MIR**'de olduğu gibi), aracı aynı kaynak ve hedefte bir kez daha çalıştırabilirsiniz. Yalnızca önceki çalıştırdıktan sonra gerçekleşen kaynak değişikliklerini taşıması gerektiğinden, çalıştırma çok daha hızlıdır. Kopyalama aracını bu şekilde yeniden çalıştırmak, kapalı kalma süresini önemli ölçüde azaltabilir.

Aşağıdaki tabloda, Microsoft araçları ve Azure dosya paylaşımları için geçerli uygunluğu sınıflandırmaktadır:

| Önerilen | Araç | Azure dosya paylaşımları için destek | Dosya uygunluk koruması |
| :-: | :-- | :---- | :---- |
|![Evet, önerilir](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Destekleniyor. Azure dosya paylaşımları, ağ sürücüleri olarak takılabilir. | Tam doğruluk. * |
|![Evet, önerilir](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure Dosya Eşitleme | Azure dosya paylaşımları ile yerel olarak tümleşiktir. | Tam doğruluk. * |
|![Evet, önerilir](media/storage-files-migration-overview/circle-green-checkmark.png)| Depolama geçiş hizmeti | Dolaylı olarak desteklenir. Azure dosya paylaşımları, SMS hedef sunucularına ağ sürücüleri olarak takılabilir. | Tam doğruluk. * |
|![Evet, önerilir](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>sürüm 10,6 | Destekleniyor. | Kaynak kök ACL 'nin kopyasını desteklemez, aksi takdirde tam doğruluk. * </br>[Azure dosya paylaşımları ile AzCopy kullanmayı öğrenin](../common/storage-use-azcopy-files.md) |
|![Evet, önerilir](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Destekleniyor. | DataBox meta verileri tam olarak destekler. |
|![Tam olarak önerilmez](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Depolama Gezgini </br>sürüm 1,14 | Destekleniyor. | ACL 'Leri kopyalamaz. Zaman damgalarını destekler.  |
|![Önerilmez](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Destekleniyor. | Meta verileri kopyalamaz. |
|||||

*\* Tam doğruluk: Azure dosya paylaşma yeteneklerini karşılar veya aşar.*

### <a name="migration-helper-tools"></a>Geçiş Yardımcısı araçları

Bu bölümde, geçişleri planlayıp çalıştırmanıza yardımcı olan araçlar açıklanmaktadır.

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation 'ın RoboCopy

RoboCopy, dosya geçişleri için en uygun araçlardan biridir. Windows 'un bir parçası olarak gelir. Ana [Robocopy belgeleri](/windows-server/administration/windows-commands/robocopy) , bu aracın birçok seçeneği için yararlı bir kaynaktır.

#### <a name="treesize-from-jam-software-gmbh"></a>SıKıŞTı Software GmbH TreeSize

Azure Dosya Eşitleme, birincil olarak öğe sayısıyla (dosyalar ve klasörler), toplam depolama miktarı ile değil, ölçeklendirir. TreeSize Aracı, Windows Server birimlerinizde bulunan öğelerin sayısını belirlemenizi sağlar.

[Azure dosya eşitleme dağıtımından](../file-sync/file-sync-deployment-guide.md)önce perspektif oluşturmak için aracını kullanabilirsiniz. Ayrıca, bulut katmanlaması dağıtımdan sonra kullanıldığında de kullanabilirsiniz. Bu senaryoda, öğe sayısını ve hangi dizinlerin sunucu önbelleğinizi en iyi şekilde kullanmasını görürsünüz.

Aracın sınanan sürümü 4.4.1 sürümüdür. Bulut katmanlı dosyalarla uyumludur. Araç, normal işlemleri sırasında katmanlı dosyaların geri çekmesine neden olmaz.

## <a name="next-steps"></a>Sonraki adımlar

1. İstediğiniz Azure dosya paylaşımları (yalnızca bulut veya karma) dağıtımı için bir plan oluşturun.
1. Azure dosya paylaşımlarınızın kaynağı ve dağıtımı ile eşleşen ayrıntılı Kılavuzu bulmak için kullanılabilir geçiş kılavuzlarından oluşan listeyi gözden geçirin.

Bu makalede bahsedilen Azure dosyaları teknolojileri hakkında daha fazla bilgi:

* [Azure dosya paylaşımında genel bakış](storage-files-introduction.md)
* [Azure Dosya Eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md)
* [Azure Dosya Eşitleme: bulut katmanlaması](../file-sync/file-sync-cloud-tiering-overview.md)