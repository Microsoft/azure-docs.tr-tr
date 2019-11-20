---
title: Azure depolama hesabına genel bakış | Microsoft Docs
description: Azure depolama hesabı oluşturma ve kullanma seçeneklerini anlayın.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f1b2bdcecac0aade21c6c770b2495a1e15ba9bc5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174024"
---
# <a name="azure-storage-account-overview"></a>Azure depolama hesabına genel bakış

Azure depolama hesabı; Bloblar, dosyalar, kuyruklar, tablolar ve diskler içeren tüm Azure depolama veri nesnelerinizi içerir. Depolama hesabı, Azure depolama verileriniz için HTTP veya HTTPS üzerinden dünyanın herhangi bir yerinden erişilebilen benzersiz bir ad alanı sağlar. Azure Depolama hesabınızdaki veriler dayanıklı ve yüksek oranda kullanılabilir, güvenli ve yüksek düzeyde ölçeklenebilir.

Azure depolama hesabı oluşturma hakkında bilgi edinmek için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Depolama hesabı türleri

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Genel amaçlı v2 hesapları

Genel amaçlı v2 depolama hesapları, en son Azure depolama özelliklerini destekler ve genel amaçlı v1 ve BLOB depolama hesaplarının tüm işlevlerini dahil edin. Genel amaçlı v2 hesapları, Azure depolama için en düşük gigabayt başına kapasite fiyatlarını ve sektör rekabetçi işlem fiyatlarını sunar. Genel amaçlı v2 depolama hesapları, bu Azure depolama hizmetlerini destekler:

- Blob 'lar (tüm türler: engelle, Ekle, sayfa)
- Data Lake Gen2
- Dosyalar
- Diskler
- Kuyruklar
- Tablolar

> [!NOTE]
> Microsoft, çoğu senaryo için genel amaçlı v2 depolama hesabı kullanılmasını önerir. Genel amaçlı v1 veya blob depolama hesabını, kapalı kalma süresi olmadan ve verileri kopyalamaya gerek kalmadan, genel amaçlı bir v2 hesabına kolayca yükseltebilirsiniz.
>
> Genel amaçlı v2 hesabına yükseltme hakkında daha fazla bilgi için bkz. [genel amaçlı v2 depolama hesabına yükseltme](storage-account-upgrade.md).

Genel amaçlı v2 depolama hesapları, kullanım modellerinize göre veri depolamak için birden çok erişim katmanı sunar. Daha fazla bilgi için bkz. [Blok Blobu verileri Için erişim katmanları](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Genel amaçlı v1 hesapları

Genel amaçlı v1 depolama hesapları, tüm Azure depolama hizmetlerine erişim sağlar, ancak en son özelliklere veya gigabayt başına en düşük fiyata sahip olmayabilir. Genel amaçlı v1 depolama hesapları, bu Azure depolama hizmetlerini destekler:

- Bloblar (tüm türler)
- Dosyalar
- Diskler
- Kuyruklar
- Tablolar

Genel amaçlı v2 hesapları çoğu durumda önerilse de, genel amaçlı v1 hesapları bu senaryolara en iyi şekilde uygundur:

* Uygulamalarınız Azure klasik dağıtım modelini gerektirir. Genel amaçlı v2 hesapları ve BLOB depolama hesapları yalnızca Azure Resource Manager dağıtım modelini destekler.

* Uygulamalarınız işlem açısından yoğun veya önemli coğrafi çoğaltma bant genişliği kullanır, ancak büyük kapasiteye gerek yoktur. Bu durumda, genel amaçlı v1 en ekonomik seçim olabilir.

* 2014-02-14 tarihinden önceki [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) sürümünü veya 4.x’ten düşük bir istemci kitaplığı sürümü ile kullanmanız ve uygulamanızı güncelleştirememeniz.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage hesapları

Bir blok Blobstorage hesabı, yapılandırılmamış nesne verilerini blok Blobları olarak depolamak ve Premium blok blob 'ları oluşturmak için kullanılan özel bir depolama hesabıdır. Bu depolama hesabı türü blok bloblarını ve ekleme bloblarını destekler, ancak sayfa Blobları, tablolar veya kuyruklar değildir.

Blok Blobstorage hesapları, genel amaçlı v2 ve BlobStorage hesaplarıyla karşılaştırıldığında düşük ve tutarlı gecikme süresi ve daha yüksek işlem ücretleri sağlar.

BlockBlobStorage hesapları Şu anda, sık erişimli, seyrek erişimli veya arşiv erişim katmanlarına yönelik katmanlamayı desteklememektedir.

### <a name="filestorage-accounts"></a>Dosya depolama hesapları

Dosya depolama hesabı, Premium dosya paylaşımlarını depolamak ve oluşturmak için kullanılan özel bir depolama hesabıdır. Bu depolama hesabı türü dosyaları destekler ancak blok Blobları, ekleme Blobları, sayfa Blobları, tablolar veya kuyruklar değildir. 

FileStorage hesapları, ıOPS patlaması gibi benzersiz performansa özel özellikler sunar. Bu özellikler hakkında daha fazla bilgi için dosyalar planlama kılavuzunun [dosya paylaşma performans katmanları](../files/storage-files-planning.md#file-share-performance-tiers) bölümüne bakın.

## <a name="naming-storage-accounts"></a>Depolama hesaplarını adlandırma

Depolama hesabınızı adlandırırken şu kuralları göz önünde bulundurun:

- Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir.
- Depolama hesabınızın adının Azure içinde benzersiz olması gerekir. İki depolama hesabı aynı ada sahip olamaz.

## <a name="performance-tiers"></a>Performans katmanları

Genel amaçlı depolama hesapları aşağıdaki performans katmanlarından biri için yapılandırılabilir:

* Blob, dosya, tablo, kuyruk ve Azure sanal makine disklerini depolamak için standart bir performans katmanı.
* Yalnızca yönetilmeyen sanal makine disklerini depolamak için Premium performans katmanı.

BlockBlobStorage Storage hesapları, blok bloblarını ve ekleme bloblarını depolamak için Premium bir performans katmanı sağlar.

FileStorage depolama hesapları, Azure dosya paylaşımları için Premium bir performans katmanı sağlar.

## <a name="access-tiers-for-block-blob-data"></a>Blok Blobu verileri için erişim katmanları

Azure depolama, Blok Blobu verilerine erişim için kullanım desenlerine göre farklı seçenekler sağlar. Azure Storage 'daki her erişim katmanı, belirli bir veri kullanımı düzeniyle iyileştirilmiştir. Gereksinimleriniz için doğru erişim katmanını seçerek, Blok Blobu verilerinizi en düşük maliyetli şekilde saklayabilirsiniz.

Kullanılabilir erişim katmanları şunlardır:

* Depolama hesabındaki nesnelere sık sık erişim için optimize edilmiş **etkin** erişim katmanı. Sık erişimli katmanda verilere erişmek, depolama maliyetlerinin daha yüksektir. Yeni depolama hesapları varsayılan olarak sık erişimli katmanda oluşturulur.
* Seyrek erişilen ve en az 30 gün içinde depolanan büyük miktarlarda veriyi depolamak için optimize **edilmiş, seyrek erişimli erişim** katmanı. Seyrek Erişimli katmanda verilerin depolanması daha uygun maliyetli bir işlemdir, ancak bu verilere erişilmesi, sık erişimli katmandaki verilere erişmenin daha pahalı olabilir.
* Yalnızca bireysel blok Blobları için kullanılabilen **Arşiv** katmanı. Arşiv katmanı, birkaç saatlik alma gecikmesini kabul eden veriler için iyileştirilmiştir ve arşiv katmanında en az 180 gün boyunca kalır. Arşiv katmanı, verilerin depolanması için en uygun maliyetli seçenektir, ancak bu verilere erişmek, sık veya seyrek katmanlardaki verilere erişmenin daha pahalıdır.

Verilerinizin kullanım düzeninde bir değişiklik varsa, bu erişim katmanları arasında istediğiniz zaman geçiş yapabilirsiniz. Erişim katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Mevcut bir depolama hesabı veya Blobun erişim katmanını değiştirmek ek ücretler elde edebilir. Daha fazla bilgi için [depolama hesabı faturalama bölümüne](#storage-account-billing)bakın.

## <a name="replication"></a>Çoğaltma

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Depolama çoğaltma hakkında daha fazla bilgi için bkz. [Azure Storage çoğaltma](storage-redundancy.md).

## <a name="encryption"></a>Şifreleme

Depolama hesabınızdaki tüm veriler hizmet tarafında şifrelenir. Şifreleme hakkında daha fazla bilgi için bkz. [bekleyen veri Için Azure depolama hizmeti şifrelemesi](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Depolama hesabı uç noktaları

Depolama hesabı, Azure 'da verileriniz için benzersiz bir ad alanı sağlar. Azure Storage 'da depoladığınız her nesnenin benzersiz hesap adınızı içeren bir adresi vardır. Hesap adı ve Azure depolama hizmeti uç noktası birleşimi, depolama hesabınız için uç noktaları oluşturur.

Örneğin, genel amaçlı depolama hesabınız *mystorageaccount*olarak adlandırılmışsa, bu hesaba ait varsayılan uç noktalar şunlardır:

* BLOB depolama: http://*mystorageaccount*. blob.Core.Windows.net
* Tablo Depolama: http://*mystorageaccount*. Table.Core.Windows.net
* Kuyruk depolama: http://*mystorageaccount*. Queue.Core.Windows.net
* Azure dosyaları: http://*mystorageaccount*. File.Core.Windows.net

> [!NOTE]
> Blok Blobu ve BLOB depolama hesapları yalnızca blob hizmeti uç noktasını kullanıma sunar.

Depolama hesabındaki bir nesneye erişim URL 'SI, nesnenin depolama hesabındaki konumu uç noktaya eklenerek oluşturulur. Örneğin bir blob adresi şu biçimde olabilir: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Ayrıca, depolama hesabınızı Bloblar için özel bir etki alanı kullanacak şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure depolama hesabınız için özel etki alanı adı yapılandırma](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Hesap verilerine erişimi denetleme

Varsayılan olarak, hesabınızdaki veriler yalnızca siz, yani hesap sahibi tarafından kullanılabilir. Verilerinize kimlerin erişebileceğini ve sahip oldukları izinleri denetleyin.

Depolama hesabınızda yapılan her isteği yetkilendirmelidir. Hizmet düzeyinde istek, hizmeti yürütmeden önce isteği doğrulamak için gereken tüm bilgileri içeren geçerli bir *Yetkilendirme* üst bilgisi içermelidir.

Aşağıdaki yaklaşımlardan herhangi birini kullanarak Depolama hesabınızdaki verilere erişim izni verebilirsiniz:

- **Azure Active Directory:** Blob ve kuyruk verilerine erişim için bir kullanıcının, grubun veya diğer kimliğin kimliğini doğrulamak üzere Azure Active Directory (Azure AD) kimlik bilgilerini kullanın. Kimlik doğrulaması başarılı olursa Azure AD, Azure Blob depolama veya kuyruk depolama için isteği yetkilendirmede kullanılacak bir belirteç döndürür. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure depolama 'ya erişim kimlik doğrulaması](storage-auth-aad.md).
- **Paylaşılan anahtar yetkilendirmesi:** Azure depolama 'ya erişmek için uygulamanızın çalışma zamanında kullandığı bir bağlantı dizesi oluşturmak için depolama hesabı erişim anahtarınızı kullanın. Bağlantı dizesindeki değerler, Azure depolama 'ya geçirilen *Yetkilendirme* üst bilgisini oluşturmak için kullanılır. Daha fazla bilgi için bkz. [Azure Storage bağlantı dizelerini yapılandırma](storage-configure-connection-string.md).
- **Paylaşılan erişim imzası:** Azure AD yetkilendirmesi kullanmıyorsanız, Depolama hesabınızdaki kaynaklara erişim yetkisi vermek için paylaşılan erişim imzası kullanın. Paylaşılan erişim imzası, URL 'de Azure depolama 'ya bir istek yetkilendirmek için gereken tüm bilgileri kapsülleyen bir belirteçtir. Depolama kaynağını, verilen izinleri ve izinlerin, paylaşılan erişim imzasının bir parçası olarak geçerli olduğu zaman aralığını belirtebilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](storage-sas-overview.md).

> [!NOTE]
> Azure AD kimlik bilgilerini kullanarak kullanıcıların veya uygulamaların kimliğini doğrulamak, diğer yetkilendirme yolları üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Uygulamalarınızla paylaşılan anahtar yetkilendirmesi kullanmaya devam edebilirsiniz, ancak Azure AD atlama 'yi kullanarak hesap erişim anahtarınızı kodunuzla depolama gereksinimini ortadan kaldırabilirsiniz. Ayrıca, Depolama hesabınızdaki kaynaklara ayrıntılı erişim sağlamak için paylaşılan erişim imzaları (SAS) kullanmaya devam edebilirsiniz, ancak Azure AD SAS belirteçlerini yönetmeye gerek kalmadan veya güvenliği aşılmış bir SAS iptal etme gereksinimi olmadan benzer yetenekler sunmaktadır. 
>
> Microsoft, mümkün olduğunda Azure Depolama Blobu ve kuyruk uygulamaları için Azure AD yetkilendirmesi kullanılmasını önerir.

## <a name="copying-data-into-a-storage-account"></a>Bir depolama hesabına veri kopyalama

Microsoft, verilerinizi şirket içi depolama cihazlarından veya üçüncü taraf bulut depolama sağlayıcılardan içeri aktarmaya yönelik yardımcı programlar ve kitaplıklar sağlar. Kullandığınız çözüm, aktardığınız veri miktarına bağlıdır. 

Genel amaçlı v1 veya blob depolama hesabından genel amaçlı bir v2 hesabına yükselttiğinizde, verileriniz otomatik olarak geçirilir. Microsoft, hesabınızı yükseltmek için bu patika önerir. Ancak, verileri genel amaçlı v1 hesabından bir BLOB depolama hesabına taşımaya karar verirseniz, aşağıda açıklanan araçları ve kitaplıkları kullanarak verilerinizi el ile geçirmeniz gerekir. 

### <a name="azcopy"></a>AzCopy

AzCopy, verilerin Azure Storage’a ve Azure Storage’dan yüksek performansla kopyalanması için tasarlanmış bir Windows komut satırı yardımcı programıdır. AzCopy komutunu, mevcut bir genel amaçlı depolama hesabından bir BLOB depolama hesabına kopyalamak veya şirket içi depolama cihazlarından verileri karşıya yüklemek için kullanabilirsiniz. Daha fazla bilgi için bkz. [AzCopy Komut Satırı Yardımcı Programı ile Veri Aktarma](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Veri hareketi kitaplığı

.NET için Azure Depolama veri taşıma kitaplığı, AzCopy’yi çalıştıran çekirdek veri taşıma altyapısını temel alır. Kitaplık, AzCopy’ye benzer yüksek performanslı, güvenilir ve kolay veri aktarımı işlemleri için tasarlanmıştır. Bunu kullanarak AzCopy’nin dış örneklerini çalıştırmanıza ve izlemenize gerek kalmadan, AzCopy tarafından uygulamanızda yerel olarak sağlanan özelliklerden tam olarak faydalanabilirsiniz. Daha fazla bilgi için bkz. [.Net için Azure Storage Veri Hareketi Kitaplığı](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API’si veya istemci kitaplığı

Azure istemci kitaplıklarından birini ya da Azure Storage hizmetleri REST API’sini kullanarak verilerinizi Blob Storage hesabına geçirmek için özel bir uygulama oluşturabilirsiniz. Azure Storage NET, Java, C++, Node.JS, PHP, Ruby ve Python gibi birden fazla dilde ve platformda zengin istemci kitaplıkları sağlar. İstemci kitaplıkları yeniden deneme mantığı, günlüğe kaydetme ve paralel karşıya yüklemeler gibi gelişmiş özellikler sunar. HTTP/HTTPS istekleri yapan herhangi bir dil tarafından çağrılabilen REST API’sine karşı doğrudan da geliştirebilirsiniz.

Azure depolama REST API hakkında daha fazla bilgi için bkz. [Azure Storage Services REST API başvurusu](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Bloblar, blobla birlikte istemci tarafı şifreleme depolama şifrelemesiyle ilgili meta veriler kullanılarak depolanır. İstemci tarafı şifreleme ile şifrelenmiş bir blobu kopyalarsanız, kopyalama işleminin başta şifreleme ile ilgili meta veriler olmak üzere blob meta verilerini koruduğundan emin olun. Bir blobu şifreleme meta verileri olmadan kopyalarsanız blob içeriği tekrar alınamaz. Şifrelemeyle ilgili meta veriler hakkında daha fazla bilgi için bkz. [Azure Depolama İstemci Tarafı Şifrelemesi](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Azure Içeri/dışarı aktarma hizmeti

Depolama hesabınıza aktarmak için büyük miktarda veriniz varsa Azure Içeri/dışarı aktarma hizmeti 'ni göz önünde bulundurun. Içeri/dışarı aktarma hizmeti, bir Azure veri merkezine disk sürücüleri göndererek büyük miktarlarda verileri Azure Blob depolama ve Azure dosyalarına güvenli bir şekilde aktarmak için kullanılır. 

Içeri/dışarı aktarma hizmeti ayrıca Azure Blob depolamadan disk sürücülerine veri aktarmak ve şirket içi sitelerinize göndermek için de kullanılabilir. Bir veya daha fazla disk sürücüsünden alınan veriler, Azure Blob depolama veya Azure dosyaları 'na aktarılabilir. Daha fazla bilgi için bkz. [Azure içeri/dışarı aktarma hizmeti nedir?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Depolama hesabı faturalama

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Genel amaçlı bir Azure depolama hesabı oluşturmayı öğrenmek için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md).
* BlockBlobStorage hesabı oluşturmayı öğrenmek için bkz. [Blok Blobu depolama hesabı oluşturma](../blobs/storage-blob-create-account-block-blob.md).
* Var olan bir depolama hesabını yönetmek veya silmek için bkz. [Azure depolama hesaplarını yönetme](storage-account-manage.md).
