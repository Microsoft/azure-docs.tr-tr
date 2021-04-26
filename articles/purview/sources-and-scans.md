---
title: Desteklenen veri kaynakları ve dosya türleri
description: Bu makalede, purview 'da desteklenen veri kaynakları ve dosya türleri hakkında kavramsal ayrıntılar verilmektedir.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677920"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure purview 'da desteklenen veri kaynakları ve dosya türleri

Bu makalede, purview 'da desteklenen veri kaynakları, dosya türleri ve tarama kavramları açıklanmaktadır.

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Azure takip görünümü aşağıdaki kaynakları destekler:

| Mağaza türü | Desteklenen kimlik doğrulama türü | UX/PowerShell aracılığıyla taramalar ayarlama |
| ---------- | ------------------- | ------------------------------ |
| Şirket içi SQL Server                   | SQL kimlik doğrulaması                        | UX                                |
| Azure Synapse Analytics (eski adı SQL DW)            | SQL kimlik doğrulaması, hizmet sorumlusu, MSI               | UX                             |
| Azure SQL veritabanı (DB)                  | SQL kimlik doğrulaması, hizmet sorumlusu, MSI               | UX |
| Azure SQL Veritabanı Yönetilen Örneği      | SQL kimlik doğrulaması, hizmet sorumlusu, MSI               | UX    |
| Azure Blob Depolama                       | Hesap anahtarı, hizmet sorumlusu, MSI | UX            |
| Azure Veri Gezgini                      | Hizmet Sorumlusu                              | UX            |
| Azure Data Lake Storage 1. (ADLS 1.) | Hizmet sorumlusu, MSI                              | UX            |
| Azure Data Lake Storage 2. (ADLS 2.) | Hesap anahtarı, hizmet sorumlusu, MSI            | UX            |
| Azure Cosmos DB                          | Hesap anahtarı                                    | UX            |


> [!Note]
> Azure Data Lake Storage 2. Nesil genel kullanıma sunuldu. Bugün kullanmaya başlamanızı öneririz. Daha fazla bilgi için bkz. [ürün sayfası](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Tarama için desteklenen dosya türleri

Aşağıdaki dosya türleri, şema ayıklama ve uygun olduğunda sınıflandırma için desteklenir:

- Uzantı tarafından desteklenen yapılandırılmış dosya biçimleri: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- Uzantı tarafından desteklenen belge dosyası biçimleri: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview özel dosya uzantılarını ve özel ayrıştırıcıları da destekler.
 
> [!Note]
> Her gzip dosyası içinde tek bir CSV dosyası ile eşlenmelidir. Gzip dosyaları sistem ve özel sınıflandırma kurallarına tabidir. Şu anda, içindeki birden çok dosyayla eşleştirilmiş bir gzip dosyasının veya CSV dışında herhangi bir dosya türünün taranmasını desteklemiyoruz. 

## <a name="sampling-within-a-file"></a>Bir dosya içinde örnekleme

Purview terimlerinde,
- L1 taraması: dosya adı, boyut ve tam nitelikli ad gibi temel bilgileri ve meta verileri ayıklar
- L2 tarama: yapılandırılmış dosya türleri ve veritabanı tabloları için şemayı ayıklar
- L3 tarama: uygulanabilir olduğunda şemayı ayıklar ve örneklenmiş dosyayı sistem ve özel sınıflandırma kurallarına göre listeler

Tüm yapılandırılmış dosya biçimleri için aşağıdaki şekilde tarayıcı örnek dosyalarını takip edin:

- Yapılandırılmış dosya türleri için, her sütunda veya 1 MB 'de 128 satır, hangisi daha düşüktür.
- Belge dosyası biçimleri için, her bir dosyanın 20 MB 'lık örnekler.
    - Bir belge dosyası 20 MB 'tan büyükse, derin bir taramaya (sınıflandırmaya tabi) tabi değildir. Bu durumda, purview yalnızca dosya adı ve tam nitelikli ad gibi temel meta verileri yakalar.

## <a name="resource-set-file-sampling"></a>Kaynak kümesi dosya örnekleme

Bir sistem kaynak kümesi ilkesiyle veya müşteri tanımlı kaynak kümesi ilkesiyle eşleşiyorsa, bir klasör veya bölüm dosyası grubu, purview içinde bir *kaynak kümesi* olarak algılanır. Bir kaynak kümesi algılanırsa, purview, içerdiği her bir klasörü örnekedecektir. [Buradan](concept-resource-sets.md)kaynak kümeleri hakkında daha fazla bilgi edinin.

Dosya türlerine göre kaynak kümeleri için dosya örnekleme:

- 100 dosyalardaki **sınırlandırılmış dosyalar (CSV, PSV, SSV, TSV)** -1, ' kaynak kümesi ' olarak kabul edilen bir klasör veya bölüm dosyaları grubu Içinde (L3 tarama) örneklenir
- 18446744073709551615 (uzun en fazla) dosyalarındaki **Data Lake dosya türleri (Parquet, avro, ORC)** -1, *kaynak kümesi* olarak kabul edilen bir klasör veya bölüm dosyaları grubu Içindeki örneklenir (L3 tarama)
- 100 dosyalarındaki **diğer yapılandırılmış dosya türleri (JSON, XML, txt)** -1, ' kaynak kümesi ' olarak kabul edilen bir klasör veya bölüm dosyaları grubu Içinde (L3 tarama) örneklenir
- **SQL Objects ve CosmosDB varlıkları** -her dosya L3 tarandı.
- **Belge dosyası türleri** -her dosya L3 tarandı. Kaynak kümesi desenleri bu dosya türleri için uygulanmaz.

## <a name="scan-regions"></a>Tarama bölgeleri
Aşağıda, purview tarayıcısının çalıştığı tüm Azure veri kaynağı (veri merkezi) bölgelerinin bir listesi verilmiştir. Azure veri kaynağınız bu listenin dışında bir bölgedeyse, tarayıcı, purview örneğinizin bölgesinde çalışacaktır.
 
### <a name="purview-scanner-regions"></a>Tarayıcı bölgelerini takip edin

- EastUs
- EastUs2 
- Güneydoğu ABD
- WestUs
- WestUs2
- Güneydoğu
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- Canadaorta
- BrazilSouth
- Merkezileştirme Hindistan
- JapanEast
- SouthAfricaNorth
- Francecna al

## <a name="classification"></a>Sınıflandırma

Tüm 105 sistem sınıflandırması kuralları yapılandırılmış dosya biçimleri için geçerlidir. Yalnızca MCE sınıflandırma kuralları belge dosya türleri için geçerlidir (veri taraması yerel Regex desenleri, Bloom filtre tabanlı algılama). Desteklenen sınıflandırmalar hakkında daha fazla bilgi için bkz. [Azure purview 'Da desteklenen sınıflandırmalar](supported-classifications.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: başlangıç paketini çalıştırma ve verileri tarama](tutorial-scan-data.md)
- [Azure purview 'da veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md)