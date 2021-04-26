---
title: Depolama hesabına genel bakış
titleSuffix: Azure Storage
description: Azure depolama 'da farklı depolama hesabı türleri hakkında bilgi edinin. Hesap adlandırma, performans katmanları, erişim katmanları, artıklık, şifreleme, uç noktalar ve daha fazlasını inceleyin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714209"
---
# <a name="storage-account-overview"></a>Depolama hesabına genel bakış

Azure depolama hesabı; Bloblar, dosyalar, kuyruklar, tablolar ve diskler içeren tüm Azure depolama veri nesnelerinizi içerir. Depolama hesabı, Azure depolama verileriniz için HTTP veya HTTPS üzerinden dünyanın herhangi bir yerinden erişilebilen benzersiz bir ad alanı sağlar. Azure Depolama hesabınızdaki veriler dayanıklı ve yüksek oranda kullanılabilir, güvenli ve yüksek düzeyde ölçeklenebilir.

Azure depolama hesabı oluşturma hakkında bilgi edinmek için bkz. [Depolama hesabı oluşturma](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Depolama hesabı türleri

Azure depolama, birkaç depolama hesabı türü sunar. Her tür farklı özellikleri destekler ve kendi fiyatlandırma modeline sahiptir. Uygulamalarınız için en iyi hesap türünü belirleyebilmek üzere bir depolama hesabı oluşturmadan önce bu farklılıkları göz önünde bulundurun.

Aşağıdaki tabloda çoğu senaryo için Microsoft tarafından önerilen depolama hesabı türleri açıklanmaktadır:

| Depolama hesabı türü | Desteklenen hizmetler | Artıklık seçenekleri | Dağıtım modeli | Kullanım |
|--|--|--|--|--|
| Standart genel amaçlı v2 | Blob, dosya, kuyruk, tablo ve Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Kaynak Yöneticisi<sup>3</sup> | Bloblar, dosyalar, kuyruklar ve tablolar için temel depolama hesabı türü. Azure Storage kullanan çoğu senaryo için önerilir. |
| Premium blok Blobları<sup>4</sup> | Yalnızca blok Blobları | LRS<br /><br />ZRS<sup>2</sup> | Kaynak Yöneticisi<sup>3</sup> | Blok Blobları ve ekleme Blobları için Premium performans özelliklerine sahip depolama hesapları. Yüksek işlem ücretleri olan senaryolar veya daha küçük nesneler kullanan veya tutarlı depolama gecikme süresi gerektiren senaryolar için önerilir.<br />[Daha fazla bilgi edinin...](../blobs/storage-blob-performance-tiers.md) |
| Premium dosya paylaşımları<sup>4</sup> | Yalnızca dosya paylaşımları | LRS<br /><br />ZRS<sup>2</sup> | Kaynak Yöneticisi<sup>3</sup> | Yalnızca dosyalar, Premium performans özelliklerine sahip depolama hesaplarıdır. Kurumsal veya yüksek performanslı ölçekli uygulamalar için önerilir.<br />[Daha fazla bilgi edinin...](../files/storage-files-planning.md#management-concepts) |
| Premium sayfa Blobları<sup>4</sup> | Yalnızca sayfa Blobları | LRS | Kaynak Yöneticisi<sup>3</sup> | Yalnızca sayfa Blobları için Premium depolama hesabı türü.<br />[Daha fazla bilgi edinin...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage, Azure Blob depolama üzerinde oluşturulmuş, büyük veri analizi için adanmış bir yetenekler kümesidir. Data Lake Storage yalnızca hiyerarşik bir ad alanı etkin olan genel amaçlı v2 depolama hesaplarında desteklenir. Data Lake Storage 2. hakkında daha fazla bilgi için bkz. [Data Lake Storage 2. giriş](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> bölgesel olarak yedekli depolama (ZRS) ve coğrafi bölge yedekli depolama (GZRS/ra-GZRS) yalnızca belirli bölgelerde standart genel amaçlı v2, Premium Blok Blobu ve Premium dosya paylaşma hesapları için kullanılabilir. Azure depolama artıklığı seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](storage-redundancy.md).

<sup>3</sup> Azure Resource Manager, depolama hesapları da dahil olmak üzere Azure kaynakları için önerilen dağıtım modelidir. Daha fazla bilgi için bkz. [Kaynak Yöneticisi genel bakış](../../azure-resource-manager/management/overview.md).

Premium performans katmanındaki <sup>4</sup> depolama hesabı, düşük gecikme süresi ve yüksek aktarım hızı için katı hal diskleri (SSD 'ler) kullanır.

Eski depolama hesapları da desteklenir. Daha fazla bilgi için bkz. [eski depolama hesabı türleri](#legacy-storage-account-types).

## <a name="storage-account-endpoints"></a>Depolama hesabı uç noktaları

Depolama hesabı, Azure 'da verileriniz için benzersiz bir ad alanı sağlar. Azure Storage 'da depoladığınız her nesnenin benzersiz hesap adınızı içeren bir adresi vardır. Hesap adı ve Azure depolama hizmeti uç noktası birleşimi, depolama hesabınız için uç noktaları oluşturur.

Depolama hesabınızı adlandırırken şu kuralları göz önünde bulundurun:

- Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir.
- Depolama hesabınızın adının Azure içinde benzersiz olması gerekir. İki depolama hesabı aynı ada sahip olamaz.

Aşağıdaki tabloda, Azure Depolama hizmetlerinin her biri için uç noktanın biçimi listelenmektedir.

| Depolama hizmeti | Uç Nokta |
|--|--|
| Blob depolama | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage 2. Nesil | `https://<storage-account>.dfs.core.windows.net` |
| Azure Dosyaları | `https://<storage-account>.file.core.windows.net` |
| Kuyruk depolama | `https://<storage-account>.queue.core.windows.net` |
| Tablo depolama | `https://<storage-account>.table.core.windows.net` |

Depolama hesabındaki nesnenin konumunu uç noktaya ekleyerek bir depolama hesabındaki bir nesneye erişmek için URL oluşturun. Örneğin, bir Blobun URL 'SI şuna benzer olacaktır:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Ayrıca, depolama hesabınızı Bloblar için özel bir etki alanı kullanacak şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure depolama hesabınız için özel etki alanı adı yapılandırma](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Depolama hesabını geçirme

Aşağıdaki tablo, bir depolama hesabını taşıma, yükseltme veya geçirme ile ilgili yönergeler özetler ve işaret eder:

| Geçiş senaryosu | Ayrıntılar |
|--|--|
| Bir depolama hesabını farklı bir aboneliğe taşıma | Azure Resource Manager bir kaynağı farklı bir aboneliğe taşımaya yönelik seçenekler sağlar. Daha fazla bilgi için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Bir depolama hesabını farklı bir kaynak grubuna taşıma | Azure Resource Manager bir kaynağı farklı bir kaynak grubuna taşımaya yönelik seçenekler sağlar. Daha fazla bilgi için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Bir depolama hesabını farklı bir bölgeye taşıma | Bir depolama hesabını taşımak için diğer bölgede depolama hesabının bir kopyasını oluşturun. Daha sonra, AzCopy veya seçtiğiniz başka bir aracı kullanarak verilerinizi bu hesaba taşıyın. Daha fazla bilgi için bkz. [Azure Storage hesabını başka bir bölgeye taşıma](storage-account-move.md). |
| Genel amaçlı v2 depolama hesabına yükseltme | Genel amaçlı v1 depolama hesabını veya blob depolama hesabını genel amaçlı v2 hesabına yükseltebilirsiniz. Bu eylemin geri alınamayacağı unutulmamalıdır. Daha fazla bilgi için bkz. [genel amaçlı v2 depolama hesabına yükseltme](storage-account-upgrade.md). |
| Klasik bir depolama hesabını Azure Resource Manager geçirme | Azure Resource Manager dağıtım modeli, işlevsellik, ölçeklenebilirlik ve güvenlik bakımından klasik dağıtım modeline göre belirlenir. Klasik bir depolama hesabını Azure Resource Manager geçirme hakkında daha fazla bilgi için, bkz. [depolama hesaplarını](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) **platform tarafından desteklenen IaaS kaynaklarının Azure Resource Manager klasik sürümüne** geçişi. |

## <a name="transferring-data-into-a-storage-account"></a>Verileri bir depolama hesabına aktarma

Microsoft, verilerinizi şirket içi depolama cihazlarından veya üçüncü taraf bulut depolama sağlayıcılardan içeri aktarmaya yönelik hizmetler ve yardımcı programlar sağlar. Kullandığınız çözüm, aktardığınız veri miktarına bağlıdır. Daha fazla bilgi için bkz. [Azure depolama geçişine genel bakış](storage-migration-overview.md).

## <a name="storage-account-encryption"></a>Depolama hesabı şifrelemesi

Depolama hesabınızdaki tüm veriler hizmet tarafında otomatik olarak şifrelenir. Şifreleme ve anahtar yönetimi hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Depolama hesabı faturalama

Azure depolama, depolama hesabı kullanımınıza göre faturalandırılır. Depolama hesabındaki tüm nesneler bir grup halinde faturalandırılır. Depolama maliyetleri aşağıdaki etkenlere göre hesaplanır:

- **Bölge** , hesabınızın dayandığı coğrafi bölgeyi ifade eder.
- **Hesap türü** , kullanmakta olduğunuz depolama hesabının türünü belirtir.
- **Erişim katmanı** , genel amaçlı v2 veya blob depolama hesabınız için belirttiğiniz veri kullanımı modelini ifade eder.
- **Kapasite** , veri depolamak için kullandığınız depolama hesabı servis biriminden ne kadarının olduğunu ifade eder.
- **Artıklık** , verilerinizin kaç kopyasının aynı anda ve hangi konumlarda tutulmasını belirler.
- **İşlemler** , Azure depolama 'ya yönelik tüm okuma ve yazma işlemlerine başvurur.
- **Veri** çıkışı, bir Azure bölgesinden aktarılan tüm verileri ifade eder. Depolama hesabınızdaki verilere aynı bölgede çalışmayan bir uygulama tarafından erişildiğinde, veri çıkışı için ücretlendirilirsiniz. Kaynak gruplarını, çıkış ücretlerini sınırlandırmak için aynı bölgedeki veri ve hizmetlerinizi gruplandırmak üzere kullanma hakkında bilgi için bkz. [Azure Kaynak grubu nedir?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

[Azure depolama fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/) , hesap türü, depolama kapasitesi, çoğaltma ve işlemlere göre ayrıntılı fiyatlandırma bilgileri sağlar. [Veri aktarımları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/data-transfers/) , veri çıkışı için ayrıntılı fiyatlandırma bilgileri sağlar. Maliyetlerinizi tahmin etmeye yardımcı olması için [Azure depolama Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) ' yı kullanabilirsiniz.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Eski depolama hesabı türleri

Aşağıdaki tabloda, eski depolama hesabı türleri açıklanmaktadır. Bu hesap türleri Microsoft tarafından önerilmez, ancak belirli senaryolarda kullanılabilir:

| Eski depolama hesabı türü | Desteklenen hizmetler | Artıklık seçenekleri | Dağıtım modeli | Kullanım |
|--|--|--|--|--|
| Standart genel amaçlı v1 | Blob, dosya, kuyruk, tablo ve Data Lake Storage | LRS/GRS/RA-GRS | Kaynak Yöneticisi, klasik | Genel amaçlı v1 hesapları en son özelliklere veya gigabayt başına en düşük fiyata sahip olmayabilir. Bu senaryolar için kullanmayı göz önünde bulundurun:<br /><ul><li>Uygulamalarınız Azure klasik dağıtım modelini gerektirir.</li><li>Uygulamalarınız işlem açısından yoğun veya önemli coğrafi çoğaltma bant genişliği kullanır, ancak büyük kapasiteye gerek kalmaz. Bu durumda, genel amaçlı v1 en ekonomik seçim olabilir.</li><li>2014-02-14 'den önceki bir Azure Storage REST API sürümünü veya 4. x sürümünden daha düşük bir sürümü olan bir istemci kitaplığını kullanırsınız ve uygulamanızı yükseltemezsiniz.</li></ul> |
| Standart BLOB depolama | Blob (yalnızca blok Blobları ve ekleme Blobları) | LRS/GRS/RA-GRS | Resource Manager | Microsoft, mümkün olduğunda bunun yerine standart genel amaçlı v2 hesaplarının kullanılmasını önerir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabı oluşturma](storage-account-create.md)
- [Genel amaçlı v2 depolama hesabına yükseltme](storage-account-upgrade.md)
- [Silinmiş bir depolama hesabını kurtarma](storage-account-recover.md)