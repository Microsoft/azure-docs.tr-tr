---
title: Common Data Model biçimi
description: Ortak veri modeli meta veri sistemini kullanarak verileri dönüştürme
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 45f5334ebee3365c17bfa52c8d47ed75b82bdfa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387708"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure Data Factory ortak veri modeli biçimi
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model (CDM) meta veri sistemi, verilerin ve anlamının uygulamalar ve iş süreçlerinde kolayca paylaşılmasını olanaklı kılar. Daha fazla bilgi için bkz. [ortak veri modeline](/common-data-model/) genel bakış.

Azure Data Factory, kullanıcılar, CDM varlıklarındaki verileri, veri akışlarını eşleme kullanarak [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS 2.) içinde depolanan hem model.jshem de bildirim formunda dönüştürebilir. Ayrıca, verilerinizi CSV veya Parquet biçiminde bölümlenmiş klasörlerde barındıracak CDM varlık başvurularını kullanarak CDM biçiminde veri havuzu oluşturabilirsiniz. 

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Ortak veri modeli, kaynak ve havuz olarak eşleme verilerinde bir [satır içi](data-flow-source.md#inline-datasets) veri akışı olarak kullanılabilir.

> [!NOTE]
> CDM varlıklarını yazarken, zaten bir başvuru olarak kullanmak üzere tanımlanmış bir CDM varlık tanımına (meta veri şeması) sahip olmanız gerekir. ADF veri akışı havuzu bu CDM varlık dosyasını okur ve alan eşlemesi için şemayı havuza aktarır.

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda bir CDM kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Biçimlendir | Biçim olmalıdır `cdm` | evet | `cdm` | biçim |
| Meta veri biçimi | Verilerin Varlık başvurusunun bulunduğu yer. CDM sürüm 1,0 kullanıyorsanız, bildirim ' ı seçin. 1,0 öncesi bir CDM sürümü kullanıyorsanız, üzerinde model.js' yi seçin. | Yes | `'manifest'` veya `'model'` | manifestType |
| Kök konumu: kapsayıcı | CDM klasörünün kapsayıcı adı | evet | Dize | Biçimlendiri |
| Kök konumu: klasör yolu | CDM klasörünün kök klasör konumu | evet | Dize | folderPath |
| Bildirim dosyası: varlık yolu | Kök klasör içindeki varlığın klasör yolu | hayır | Dize | entityPath |
| Bildirim dosyası: bildirim adı | Bildirim dosyasının adı. Varsayılan değer ' default ' değeridir  | Hayır | Dize | manifestName |
| Son değiştirme ölçütü | En son değiştirildiklerinde dosyaları filtrelemek için seçin | hayır | Timestamp | Modıfıedafter <br> modifiedBefore | 
| Şemaya bağlı hizmet | Yapı 'nin bulunduğu bağlı hizmet | Evet, bildirim kullanılıyorsa | `'adlsgen2'` veya `'github'` | corpusStore | 
| Varlık başvurusu kapsayıcısı | Kapsayıcı yapı | Evet, bildirim ve yapı ADLS 2. kullanılıyorsa | Dize | adlsgen2_fileSystem |
| Varlık başvuru deposu | GitHub deposu adı | Evet, GitHub 'da bildirim ve yapı kullanılıyorsa | Dize | github_repository |
| Varlık başvuru dalı | GitHub depo dalı | Evet, GitHub 'da bildirim ve yapı kullanılıyorsa | Dize |  github_branch |
| Corpus klasörü | Yapı 'nin kök konumu | Evet, bildirim kullanılıyorsa | Dize | corpusPath |
| Corpus varlığı | Varlık başvurusunun yolu | evet | Dize | varlık |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

Kaynak ve havuz dönüşümlerindeki "varlık başvurusu" nı seçerken, varlık başvurunuz konumu için bu üç seçenekten birini seçebilirsiniz:

* Yerel, zaten ADF tarafından kullanılmakta olan bildirim dosyasında tanımlanan varlığı kullanır
* Özel, ADF 'nin kullandığı bildirim dosyasından farklı bir varlık bildirim dosyasını işaret etmesini ister
* Standart, içinde tutulan CDM varlıklarının standart kitaplığından bir varlık başvurusu kullanacaktır ```Github``` .

### <a name="sink-settings"></a>Havuz ayarları

* Yazmak istediğiniz varlığın tanımını içeren CDM varlık başvuru dosyasının üzerine gelin.

![varlık ayarları](media/data-flow/common-data-model-111.png "Varlık başvurusu")

* ADF 'nin varlıklarınızı yazmak için kullanmasını istediğiniz çıkış dosyalarının Bölüm yolunu ve biçimini tanımlayın.

![varlık biçimi](media/data-flow/common-data-model-222.png "Varlık biçimi")

* Çıktı dosyasının konumunu ve bildirim dosyasının konumunu ve adını ayarlayın.

![CDM konumu](media/data-flow/common-data-model-333.png "CDM konumu")


#### <a name="import-schema"></a>Şemayı içeri aktar

CDM yalnızca satır içi veri kümesi olarak kullanılabilir ve varsayılan olarak ilişkili bir şemaya sahip değildir. Sütun meta verilerini almak için **İzdüşüm** sekmesindeki **şemayı içeri aktar** düğmesine tıklayın. Bu, Corpus tarafından belirtilen sütun adlarına ve veri türlerine başvuruda bulunmak için izin verir. Şemayı içeri aktarmak için bir [veri akışı hata ayıklama oturumunun](concepts-data-flow-debug-mode.md) etkin olması ve işaret etmek için var olan bir CDM varlık tanımı dosyanızın olması gerekir.

Havuz dönüşümünde veri akışı sütunlarını varlık özellikleriyle eşlerken, "eşleme" sekmesine tıklayın ve "şemayı Içeri aktar" seçeneğini belirleyin. ADF, havuz seçeneklerinizde işaret ettiğiniz varlık başvurusunu okuyarak hedef CDM şemasına eşlemenizi sağlar.

![CDM havuz ayarları](media/data-flow/common-data-model-444.png "CDM eşleme")

> [!NOTE]
>  Power BI veya güç platformu veri akışlarından kaynaklanan kaynak türünde model.jskullanırken, kaynak dönüşümünde "Corpus yolu null veya boş" hatalarla karşılaşabilirsiniz. Bunun nedeni, dosyadaki model.jsbölüm konumu yolunun biçimlendirme sorunlarından kaynaklanıyor olabilir. Bu hatayı onarmak için aşağıdaki adımları izleyin: 

1. Dosyadaki model.jsmetin düzenleyicisinde aç
2. Bölümleri bulun. Location özelliği 
3. "Blob.core.windows.net" öğesini "dfs.core.windows.net" olarak değiştir
4. URL 'deki "% 2F" kodlamasını "/" olarak düzeltin
5. ADF veri akışları kullanılıyorsa, bölüm dosyası yolundaki özel karakterlerin alfa sayısal değerlerle değiştirilmelidir veya SYNAPSE veri akışlarına geçiş yapılmalıdır

### <a name="cdm-source-data-flow-script-example"></a>CDM kaynak veri akışı betiği örneği

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Havuz özellikleri

Aşağıdaki tabloda bir CDM havuzu tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **Ayarlar** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Biçimlendir | Biçim olmalıdır `cdm` | evet | `cdm` | biçim |
| Kök konumu: kapsayıcı | CDM klasörünün kapsayıcı adı | evet | Dize | Biçimlendiri |
| Kök konumu: klasör yolu | CDM klasörünün kök klasör konumu | evet | Dize | folderPath |
| Bildirim dosyası: varlık yolu | Kök klasör içindeki varlığın klasör yolu | hayır | Dize | entityPath |
| Bildirim dosyası: bildirim adı | Bildirim dosyasının adı. Varsayılan değer ' default ' değeridir | Hayır | Dize | manifestName |
| Şemaya bağlı hizmet | Yapı 'nin bulunduğu bağlı hizmet | evet | `'adlsgen2'` veya `'github'` | corpusStore | 
| Varlık başvurusu kapsayıcısı | Kapsayıcı yapı | Evet, yapı ADLS 2. | Dize | adlsgen2_fileSystem |
| Varlık başvuru deposu | GitHub deposu adı | Evet, GitHub 'da yapı | Dize | github_repository |
| Varlık başvuru dalı | GitHub depo dalı | Evet, GitHub 'da yapı | Dize |  github_branch |
| Corpus klasörü | Yapı 'nin kök konumu | evet | Dize | corpusPath |
| Corpus varlığı | Varlık başvurusunun yolu | evet | Dize | varlık |
| Bölüm yolu | Bölümün yazılacağı konum | hayır | Dize | partitionPath |
| Klasörü temizle | Hedef klasör, yazma işleminden önce silinirse | hayır | `true` veya `false` | kesilemedi |
| Biçim türü | Parquet biçimini belirtmeyi seçin | hayır | `parquet` belirtilmişse | alt biçim |
| Sütun sınırlayıcısı | DelimitedText 'e yazıyorsanız sütunları sınırlandırmak | Evet, DelimitedText 'e yazıyorsanız | Dize | columnDelimiter |
| Üst bilgi olarak ilk satır | DelimitedText kullanılıyorsa, sütun adlarının üst bilgi olarak eklenip eklenmeyeceğini belirtir | hayır | `true` veya `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM havuz veri akışı betiği örneği

İlişkili veri akışı betiği:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Sonraki adımlar

Eşleme veri akışında bir [kaynak dönüşümü](data-flow-source.md) oluşturun.
