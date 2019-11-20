---
title: Kopyalama etkinliğini kullanarak verileri taşıma
description: 'Data Factory işlem hatlarında veri taşıma hakkında bilgi edinin: bulut depoları arasında veri geçişi ve şirket içi bir mağaza ile bulut deposu arasında. Kopyalama etkinliğini kullanın.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f7771c55bfdc837921fb731b29e88c970b5d283
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682643"
---
# <a name="move-data-by-using-copy-activity"></a>Kopyalama etkinliğini kullanarak verileri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-data-movement-activities.md)
> * [Sürüm 2 (geçerli sürüm)](../copy-activity-overview.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de kopyalama etkinliği](../copy-activity-overview.md).

## <a name="overview"></a>Genel Bakış
Azure Data Factory, şirket içi ve bulut veri depoları arasında veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Veriler kopyalandıktan sonra, daha fazla dönüştürülebilir ve analiz edilebilir. Ayrıca, iş zekası (BI) ve uygulama tüketimi için dönüştürme ve analiz sonuçlarını yayımlamak için kopyalama etkinliği ' ni de kullanabilirsiniz.

![Kopyalama etkinliğinin rolü](media/data-factory-data-movement-activities/copy-activity.png)

Kopyalama etkinliği güvenli, güvenilir, ölçeklenebilir ve [küresel olarak kullanılabilir bir hizmet](#global)tarafından desteklenir. Bu makalede Data Factory ve kopyalama etkinliğinde veri taşıma hakkında ayrıntılı bilgi verilmektedir.

İlk olarak, iki bulut veri deposu arasında ve şirket içi veri deposu ile bulut veri deposu arasında veri geçişinin nasıl gerçekleşeceğini görelim.

> [!NOTE]
> Genel olarak etkinlikler hakkında bilgi edinmek için bkz. işlem [hatlarını ve etkinlikleri anlama](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>İki bulut veri deposu arasında veri kopyalama
Hem kaynak hem de havuz veri deposu bulutta olduğunda kopyalama etkinliği kaynağı kaynaktan havuza kopyalamak için aşağıdaki aşamalardan geçer. Kopyalama etkinliğini destekleyen hizmet:

1. Kaynak veri deposundan verileri okur.
2. Serileştirme/seriyi kaldırma, sıkıştırma/açma, sütun eşleme ve tür dönüştürme gerçekleştirir. Bu işlemler, giriş veri kümesi, çıkış veri kümesi ve kopyalama etkinliğinin yapılandırmalarına göre yapılır.
3. Verileri hedef veri deposuna yazar.

Hizmet, veri hareketini gerçekleştirmek için en uygun bölgeyi otomatik olarak seçer. Bu bölge genellikle havuz veri deposuna en yakın bir yoldur.

![Buluttan buluta kopyalama](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Şirket içi veri deposu ile bulut veri deposu arasında veri kopyalama
Şirket içi veri deposu ile bulut veri deposu arasında verileri güvenli bir şekilde taşımak için şirket içi makinenize Veri Yönetimi Ağ Geçidi ' ni yüklersiniz. Veri Yönetimi ağ geçidi, karma veri hareketini ve işlemeyi sağlayan bir aracıdır. Bunu veri deposunun kendisi ile aynı makineye veya veri deposuna erişimi olan ayrı bir makineye yükleyebilirsiniz.

Bu senaryoda Veri Yönetimi Gateway, serileştirme/seriyi kaldırma, sıkıştırma/açma, sütun eşleme ve tür dönüştürme işlemlerini gerçekleştirir. Veriler Azure Data Factory hizmeti üzerinden akış yapmaz. Bunun yerine, Veri Yönetimi ağ geçidi, verileri doğrudan hedef depoya yazar.

![Şirket içinden buluta kopyalama](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Giriş ve İzlenecek yol için bkz. Şirket [içi ve bulut veri depoları arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) . Bu aracı hakkında ayrıntılı bilgi için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) .

Ayrıca, Azure IaaS sanal makinelerinde (VM 'Ler) barındırılan desteklenen veri depolarından verileri de Veri Yönetimi ağ geçidi kullanarak taşıyabilirsiniz. Bu durumda, veri deposunun kendisi ile aynı VM 'ye veya veri deposuna erişimi olan ayrı bir VM 'ye Veri Yönetimi ağ geçidi yükleyebilirsiniz.

## <a name="supported-data-stores-and-formats"></a>Desteklenen veri depoları ve biçimleri
Data Factory’deki Kopyalama Etkinliği bir kaynak veri deposundan havuz veri deposuna verileri kopyalar. Data Factory aşağıdaki veri depolarını destekler. Herhangi bir kaynaktan gelen veriler herhangi bir havuza yazılabilir. Bir depoya veya depodan veri kopyalama hakkında bilgi edinmek için veri deposuna tıklayın.

> [!NOTE] 
> Kopyalama Etkinliğinin desteklemediği bir veri deposuna/veri deposundan veri taşımanız gerekirse Data Factory’de **özel bir etkinliği** kendi veri kopyalama/taşıma mantığınızla kullanın. Özel bir etkinlik oluşturma ve kullanma hakkında ayrıntılı bilgi için bkz. [Azure Data Factory işlem hattında özel etkinlikler kullanma](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* taşıyan veri depoları şirket içi veya Azure IaaS üzerinde olabilir ve bir şirket içi/Azure IaaS makinesine [Veri Yönetimi Ağ Geçidi](data-factory-data-management-gateway.md) yüklemenizi gerektirir.

### <a name="supported-file-formats"></a>Desteklenen dosya biçimleri
Kopyalama etkinliği 'ni kullanarak dosyaları iki dosya tabanlı veri deposu arasında **olduğu gibi kopyalayabilirsiniz** , hem giriş hem de çıkış veri kümesi tanımlarında [Biçim bölümünü](data-factory-create-datasets.md) atlayabilirsiniz. Veriler, herhangi bir serileştirme/seri durumdan çıkarma yapılmadan verimli bir şekilde kopyalanır.

Kopyalama etkinliği aynı zamanda belirtilen biçimlerdeki dosyaları okur ve yazar: **Text, JSON, avro, ORC ve Parquet**ve Compression codec **gzip, söndür, bzip2 ve zipsöndür** desteklenir. Ayrıntılar ile [Desteklenen dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

Örneğin, aşağıdaki kopyalama etkinliklerini yapabilirsiniz:

* Verileri şirket içi SQL Server kopyalayın ve Azure Data Lake Store ORC biçiminde yazın.
* Metin (CSV) biçimindeki dosyaları şirket içi dosya sisteminden kopyalayın ve avro biçiminde Azure Blob 'a yazın.
* ZIP dosyalarını şirket içi dosya sisteminden kopyalayın ve Azure Data Lake Store ' ye açıp açın.
* Verileri Azure Blob 'dan GZip sıkıştırılmış metin (CSV) biçiminde kopyalayın ve Azure SQL veritabanı 'na yazın.

## <a name="global"></a>Küresel olarak kullanılabilir veri taşıma
Azure Data Factory yalnızca Batı ABD, Doğu ABD ve Kuzey Avrupa bölgelerinde kullanılabilir. Ancak, kopyalama etkinliğini destekleyen hizmet aşağıdaki bölgelerde ve coğrafi bölgelerde genel olarak kullanılabilir. Genel olarak kullanılabilir topoloji, genellikle bölgeler arası atlamaları önleyen etkili veri hareketini sağlar. Bir bölgedeki Data Factory ve veri hareketinin kullanılabilirliği için bkz. [bölgeye göre hizmetler](https://azure.microsoft.com/regions/#services) .

### <a name="copy-data-between-cloud-data-stores"></a>Bulut veri depoları arasında veri kopyalama
Hem kaynak hem de havuz veri deposu bulutta olduğunda Data Factory, verileri taşımak için aynı coğrafya içindeki havuza en yakın bölgede bir hizmet dağıtımı kullanır. Eşleme için aşağıdaki tabloya bakın:

| Hedef veri depolarının Coğrafya | Hedef veri deposunun bölgesi | Veri taşıma için kullanılan bölge |
|:--- |:--- |:--- |
| Amerika Birleşik Devletleri | Doğu ABD | Doğu ABD |
| &nbsp; | Doğu ABD 2 | Doğu ABD 2 |
| &nbsp; | Orta ABD | Orta ABD |
| &nbsp; | Orta Kuzey ABD | Orta Kuzey ABD |
| &nbsp; | Orta Güney ABD | Orta Güney ABD |
| &nbsp; | Batı Orta ABD | Batı Orta ABD |
| &nbsp; | Batı ABD | Batı ABD |
| &nbsp; | Batı ABD 2 | Batı ABD 2 |
| Kanada | Doğu Kanada | Orta Kanada |
| &nbsp; | Orta Kanada | Orta Kanada |
| Brezilya | Güney Brezilya | Güney Brezilya |
| Avrupa | Kuzey Avrupa | Kuzey Avrupa |
| &nbsp; | Batı Avrupa | Batı Avrupa |
| Birleşik Krallık | Birleşik Krallık Batı | Birleşik Krallık Güney |
| &nbsp; | Birleşik Krallık Güney | Birleşik Krallık Güney |
| Asya Pasifik | Güneydoğu Asya | Güneydoğu Asya |
| &nbsp; | Doğu Asya | Güneydoğu Asya |
| Avustralya | Avustralya Doğu | Avustralya Doğu |
| &nbsp; | Avustralya Güneydoğu | Avustralya Güneydoğu |
| Hindistan | Orta Hindistan | Orta Hindistan |
| &nbsp; | Batı Hindistan | Orta Hindistan |
| &nbsp; | Güney Hindistan | Orta Hindistan |
| Japonya | Japonya Doğu | Japonya Doğu |
| &nbsp; | Japonya Batı | Japonya Doğu |
| Güney Kore | Kore Orta | Kore Orta |
| &nbsp; | Kore Güney | Kore Orta |

Alternatif olarak, kopyalama etkinliği `typeProperties`altında `executionLocation` özelliği belirterek kopyayı gerçekleştirmek için kullanılacak Data Factory hizmetin bölgesini açıkça belirtebilirsiniz. Bu özellik için desteklenen değerler, **veri taşıma sütunu için kullanılan yukarıdaki bölgede** listelenmiştir. Verileriniz, kopyalama sırasında, bu bölgeden gelen bölge üzerinden gider. Örneğin, Kore 'deki Azure mağazalarını kopyalamak için, Japonya bölgesi üzerinden yönlendirmek üzere `"executionLocation": "Japan East"` belirtebilirsiniz (bkz. başvuru olarak [örnek JSON](#by-using-json-scripts) ).

> [!NOTE]
> Hedef veri deposunun bölgesi önceki listede yoksa veya algılanamaz ise, `executionLocation` belirtilmediği takdirde, varsayılan olarak kopyalama etkinliği alternatif bir bölge üzerinden geçiş yapmak yerine başarısız olur. Desteklenen bölge listesi zaman içinde genişletilir.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Şirket içi veri deposu ile bulut veri deposu arasında veri kopyalama
Veriler şirket içi (veya Azure sanal makineleri/IaaS) ve bulut depoları arasında kopyalanırken [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) , şirket içi bir makinede veya sanal makinede veri hareketini gerçekleştirir. [Hazırlanmış kopyalama](data-factory-copy-activity-performance.md#staged-copy) özelliğini kullanmadığınız durumlar dışında veriler buluttaki hizmetten akamaz. Bu durumda, veriler havuz veri deposuna yazılmadan önce hazırlama Azure Blob depolama alanı üzerinden akar.

## <a name="create-a-pipeline-with-copy-activity"></a>Kopyalama etkinliği ile işlem hattı oluşturma
Kopyalama etkinliği içeren bir işlem hattını birkaç yolla oluşturabilirsiniz:

### <a name="by-using-the-copy-wizard"></a>Kopyalama Sihirbazı 'nı kullanarak
Data Factory kopyalama Sihirbazı, kopyalama etkinliğine sahip bir işlem hattı oluşturmanıza yardımcı olur. Bu işlem hattı, bağlı hizmetler, veri kümeleri ve işlem hatları için *JSON tanımları yazmadan* desteklenen kaynaklardaki verileri hedeflere kopyalamanızı sağlar. Sihirbazla ilgili ayrıntılar için bkz. [Data Factory kopyalama Sihirbazı](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>JSON betiklerini kullanarak
Visual Studio 'da Data Factory düzenleyicisini veya Azure PowerShell bir işlem hattı için JSON tanımı oluşturmak için (kopyalama etkinliğini kullanarak) kullanabilirsiniz. Daha sonra, Data Factory işlem hattını oluşturmak için dağıtabilirsiniz. Adım adım yönergeler için bkz. [öğretici: Azure Data Factory işlem hattındaki kopyalama etkinliği kullanma](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .    

JSON özellikleri (ad, açıklama, giriş ve çıkış tabloları ve ilkeler gibi) tüm etkinlik türleri için kullanılabilir. Etkinliğin `typeProperties` bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir.

Kopyalama etkinliği için `typeProperties` bölümü, kaynak ve havuz türlerine göre değişir. Bu veri deposu için kopyalama etkinliğinin desteklediği tür özellikleri hakkında bilgi edinmek için [desteklenen kaynaklar ve havuzlar](#supported-data-stores-and-formats) bölümünde bir kaynak/havuz öğesine tıklayın.

Örnek bir JSON tanımı aşağıda verilmiştir:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Çıktı veri kümesinde tanımlanan zamanlama, etkinliğin ne zaman çalışacağını belirler (örneğin: **günlük**, sıklık, **gün**ve Aralık **1**). Etkinlik, verileri bir giriş veri kümesinden (**kaynak**) çıkış veri kümesine (**Havuz**) kopyalar.

Aktiviteyi kopyalamak için birden fazla giriş veri kümesi belirtebilirsiniz. Bunlar, etkinlik çalıştırılmadan önce bağımlılıkları doğrulamak için kullanılır. Ancak, hedef veri kümesine yalnızca ilk veri kümesindeki veriler kopyalanır. Daha fazla bilgi için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Performans ve ayar
Azure Data Factory içindeki veri taşıma (kopyalama etkinliği) performansını etkileyen temel faktörleri açıklayan, [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md)' na bakın. Ayrıca, iç test sırasında gözlemlenen performansı listeler ve kopyalama etkinliğinin performansını iyileştirmek için çeşitli yollar tartışır.

## <a name="fault-tolerance"></a>Hataya dayanıklılık
Varsayılan olarak, kopyalama etkinliği veri kopyalamayı durdurur ve kaynak ve havuz arasında uyumsuz verilerle karşılaşmanız durumunda başarısız olur; uyumsuz satırları atlayıp günlüğe kaydetmek ve yalnızca bu uyumlu verileri kopyalamak için açıkça yapılandırabilmeniz gerekir. Daha fazla ayrıntı için [kopyalama etkinliği hata toleransını](data-factory-copy-activity-fault-tolerance.md) inceleyin.

## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler
Verilerinizin güvenliğini sağlamak için Azure Data Factory veri taşıma hizmetlerinin kullandığı güvenlik altyapısını açıklayan [güvenlik konularını](data-factory-data-movement-security-considerations.md)inceleyin.

## <a name="scheduling-and-sequential-copy"></a>Zamanlama ve sıralı kopya
Data Factory ' de zamanlama ve yürütmenin nasıl çalıştığına ilişkin ayrıntılı bilgi için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) . Birden çok kopyalama işlemini ardışık ve sıralı bir şekilde başka bir şekilde çalıştırmak mümkündür. [Sıralı kopyalama](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) bölümüne bakın.

## <a name="type-conversions"></a>Tür dönüştürmeleri
Farklı veri depolarında farklı yerel tür sistemleri vardır. Kopyalama etkinliği, aşağıdaki iki adımlı yaklaşımla kaynak türlerinden havuz türlerine otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden bir .NET türüne Dönüştür.
2. .NET türünden yerel havuz türüne dönüştürme.

Bir yerel tür sisteminden bir veri deposu için .NET türüne eşleme ilgili veri deposu makalesinde bulunur. (Desteklenen veri depoları tablosunda belirli bir bağlantıya tıklayın). Bu eşlemeleri, tablolarınızı oluştururken uygun türleri belirlemede kullanabilirsiniz, böylece kopyalama etkinliği doğru dönüştürmeleri gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar
* Kopyalama etkinliği hakkında daha fazla bilgi edinmek için bkz. [Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Verileri şirket içi veri deposundan bir bulut veri deposuna taşıma hakkında bilgi edinmek için bkz. [verileri Şirket içinden bulut veri depolarına taşıma](data-factory-move-data-between-onprem-and-cloud.md).
