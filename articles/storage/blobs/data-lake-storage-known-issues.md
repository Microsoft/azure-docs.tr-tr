---
title: Azure Data Lake Storage 2. ile ilgili bilinen sorunlar | Microsoft Docs
description: Azure Data Lake Storage 2. kısıtlamaları ve bilinen sorunlar hakkında bilgi edinin.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: db55a29e53c53e26fbb04d96ddf1864946bafbfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581287"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ile ilgili bilinen sorunlar

Bu makalede, Azure Data Lake Storage 2. kısıtlamaları ve bilinen sorunlar açıklanmaktadır.

## <a name="supported-blob-storage-features"></a>Desteklenen Blob depolama özellikleri

Daha fazla sayıda BLOB depolama özelliği, hiyerarşik bir ad alanı olan hesaplarla çalışır. Tüm liste için, bkz. [Azure Data Lake Storage 2. bulunan BLOB Storage özellikleri](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Desteklenen Azure hizmeti tümleştirmeleri

Azure Data Lake Storage 2., verileri almak, analiz gerçekleştirmek ve görsel gösterimler oluşturmak için kullanabileceğiniz çeşitli Azure hizmetlerini destekler. Desteklenen Azure hizmetlerinin listesi için bkz. [Azure Data Lake Storage 2. destekleyen Azure hizmetleri](data-lake-storage-supported-azure-services.md).

[Azure Data Lake Storage 2. destekleyen Azure hizmetleri](data-lake-storage-supported-azure-services.md)bölümüne bakın.

## <a name="supported-open-source-platforms"></a>Desteklenen açık kaynaklı platformlar

Birçok açık kaynak platformu Data Lake Storage 2. destekler. Tüm liste için, bkz. [Azure Data Lake Storage 2. destekleyen açık kaynaklı platformlar](data-lake-storage-supported-open-source-platforms.md).

[Azure Data Lake Storage 2. destekleyen açık kaynaklı platformlar](data-lake-storage-supported-open-source-platforms.md)bölümüne bakın.

## <a name="blob-storage-apis"></a>BLOB depolama API 'Leri

Blob API 'Leri ve Data Lake Storage 2. API 'Leri aynı verilerde çalışabilir.

Bu bölümde, aynı verilerde çalışacak blob API 'Leri ve Data Lake Storage 2. API 'Leri kullanımıyla ilgili sorunlar ve sınırlamalar açıklanmaktadır.

* Blob API ve Data Lake Storage API 'Leri, bir dosyanın aynı örneğine yazmak için kullanamazsınız. Data Lake Storage 2. API 'Leri kullanarak bir dosyaya yazarsanız, bu dosyanın blokları [Get Block list](/rest/api/storageservices/get-block-list) blob API 'sine yapılan çağrılara görünmez. Tek özel durum, sizin kullandığınız zaman üzerine yazılır. API 'YI kullanarak bir dosyanın/Blobun üzerine yazabilirsiniz.

* Bir sınırlayıcı belirtmeden [Blobları Listele](/rest/api/storageservices/list-blobs) işlemini kullandığınızda, sonuçlar hem dizinleri hem de Blobları içerecektir. Bir sınırlayıcı kullanmayı seçerseniz yalnızca eğik çizgi ( `/` ) kullanın. Bu, desteklenen tek sınırlayıcıdır.

* Bir dizini silmek için [blobu silme](/rest/api/storageservices/delete-blob) API 'sini kullanırsanız, bu dizin yalnızca boşsa silinir. Bu, blob API 'SI silme dizinlerini yinelemeli olarak kullanamayacağı anlamına gelir.

Bu blob REST API 'Leri desteklenmez:

* [Blobu Yerleştir (sayfa)](/rest/api/storageservices/put-blob)
* [Yerleştirme sayfası](/rest/api/storageservices/put-page)
* [Sayfa aralıklarını al](/rest/api/storageservices/get-page-ranges)
* [Artımlı kopya blobu](/rest/api/storageservices/incremental-copy-blob)
* [URL 'den sayfa koy](/rest/api/storageservices/put-page-from-url)

Yönetilmeyen VM diskleri hiyerarşik bir ad alanına sahip hesaplarda desteklenmez. Bir depolama hesabında hiyerarşik bir ad alanı etkinleştirmek istiyorsanız, yönetilmeyen VM disklerini hiyerarşik ad alanı özelliği etkin olmayan bir depolama hesabına yerleştirin.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Erişim denetim listelerini (ACL 'Ler) yinelemeli olarak ayarlama desteği

ACL değişikliklerini üst dizinden alt öğelere yinelemeli olarak uygulama özelliği genel kullanıma sunulmuştur. Bu özelliğin geçerli sürümünde PowerShell, Azure CLı ve .NET, Java ve Python SDK kullanarak ACL değişikliklerini uygulayabilirsiniz. Azure portal veya Azure Depolama Gezgini için destek henüz kullanılamamaktadır.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

AzCopy 'in yalnızca en son sürümünü kullanın ([AzCopy ile v10 arasındaki](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). AzCopy v 8.1 gibi önceki AzCopy sürümleri desteklenmez.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Depolama Gezgini

Yalnızca sürümlerini `1.6.0` veya üstünü kullanın.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure portal Depolama Gezgini

ACL 'Ler henüz desteklenmiyor.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Üçüncü taraf uygulamalar

REST API 'leri kullanan üçüncü taraf uygulamalar, bunları blob API 'Leri çağıran Data Lake Storage 2. uygulamalarla birlikte kullandığınızda çalışmaya devam edecektir.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Erişim denetim listeleri (ACL) ve anonim okuma erişimi

Bir kapsayıcıya [anonim okuma erişimi](./anonymous-read-access-configure.md) verildiyse, ACL 'lerin bu kapsayıcıya veya o kapsayıcıdaki dosyalara hiçbir etkisi olmaz.

## <a name="diagnostic-logs"></a>Tanılama günlükleri

Saklama günlerinin ayarı henüz desteklenmiyor, ancak Azure Depolama Gezgini, REST veya SDK gibi desteklenen bir aracı kullanarak günlükleri el ile silebilirsiniz.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage için Premium katmanda yaşam döngüsü yönetim ilkeleri

Premium katmanda depolanan verileri sık erişimli, seyrek erişimli ve arşiv katmanları arasında taşıyamazsınız. Ancak, Premium katmandan verileri farklı bir hesaptaki sık erişimli erişim katmanına kopyalayabilirsiniz.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Premium performanslı blok Blobstorage depolama hesaplarıyla Dremio desteği

Dremio henüz hiyerarşik ad alanı özelliği etkinleştirilmiş olan bir BlockBlobStorage hesabına bağlanmıyor. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Depolama Blobu (. b) sürücüsü (Data Lake Storage 2. ile desteklenmez)

Şu anda, yalnızca blob API 'SI ile çalışmak üzere tasarlanan olan, çok sayıda yaygın senaryoda sorunlarla karşılaştığında. Özellikle, hiyerarşik bir ad alanı etkin depolama hesabına yönelik bir istemcudur. Data Lake Storage çoklu protokol erişimi, bu sorunları azaltmaz. 

Kullanım süresi (ve olasılı olma olasılığı yüksektir) için, bir istemci olarak, bir hiyerarşik ad alanı etkin depolama hesabına sahip olan müşterileri desteklemezler. Bunun yerine, Hadoop ortamınızda [Azure blob dosya sistemi (ABFS)](data-lake-storage-abfs-driver.md) sürücüsünü kullanmayı tercih etmenizi öneririz. Hadoop dalı-3 ' ten önceki bir sürüme sahip şirket içi Hadoop ortamından geçiş yapmaya çalışıyorsanız, sizin ve kuruluşunuz için doğru yolda sizinle iletişime geçebilmemiz için lütfen bir Azure destek bileti açın.