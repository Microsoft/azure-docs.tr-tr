---
title: Azure Data Factory kopyalama etkinliğini kullanarak meta verileri ve ACL 'Leri koruma
description: Azure Data Factory kopyalama etkinliğini kullanarak kopyalama sırasında meta verileri ve ACL 'Leri koruma hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: 396a598d143e85687f9dfbf765b3c18736627e41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387725"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Azure Data Factory kopyalama etkinliğini kullanarak meta verileri ve ACL 'Leri koruma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verileri kaynaktan havuza kopyalamak için Azure Data Factory kopyalama etkinliğini kullandığınızda, aşağıdaki senaryolarda meta verileri ve ACL 'Leri de koruyabilirsiniz.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Lake geçişi için meta verileri koruma

Bir Data Lake 'tan [Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md)ve [Azure dosya depolama](connector-azure-file-storage.md)dahil olmak üzere başka bir veri geçirdiğinizde, dosya meta verilerini verilerle birlikte korumayı seçebilirsiniz.

Kopyalama etkinliği, veri kopyalama sırasında aşağıdaki özniteliklerin korunması destekler:

- **Müşterinin belirttiği tüm meta veriler** 
- Ve aşağıdaki **beş veri deposu yerleşik sistem özellikleri**: `contentType` , `contentLanguage` (Amazon S3 hariç),,, `contentEncoding` `contentDisposition` , `cacheControl` .

**Meta verilerde farkları işle:** Amazon S3 ve Azure Storage, müşteri tarafından belirtilen meta veriler anahtarlarındaki farklı karakter kümelerine izin veriyor. Kopyalama etkinliğini kullanarak meta verileri korumayı seçtiğinizde, ADF otomatik olarak geçersiz karakterleri ' _ ' ile değiştirir.

Dosyaları Amazon S3/Azure Data Lake Storage 2./Azure Blob/Azure dosya depolama alanından olduğu gibi, ikili biçimdeki Azure Data Lake Storage 2./Azure Blob/Azure dosya depolama alanına kopyaladığınızda,  etkinlik yazma veya   >  veri kopyalama aracındaki **Ayarlar** sayfası için etkinlik **ayarlarını** Kopyala sekmesinde Koru seçeneğini bulabilirsiniz.

![Kopyalama etkinliği meta verilerini koru](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Kopyalama etkinliği JSON yapılandırmasına bir örnek (bkz `preserve` .): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Data Lake Storage 1./Gen2 ile Gen2 arasında ACL 'Leri koru

Azure Data Lake Storage 1. 'den Gen2 'e yükselttiğinizde veya ADLS 2. arasında veri kopyaladığınızda, POSIX erişim denetim listelerini (ACL 'Ler) veri dosyalarıyla birlikte korumayı seçebilirsiniz. Access Control hakkında daha fazla bilgi için Azure Data Lake Storage 2. Azure Data Lake Storage 1. ve [erişim denetimi](../storage/blobs/data-lake-storage-access-control.md) [' nde erişim denetimi](../data-lake-store/data-lake-store-access-control.md) ' ne bakın.

Kopyalama etkinliği, veri kopyalama sırasında aşağıdaki ACL türlerinin korunması destekler. Bir veya daha fazla tür seçebilirsiniz:

- **ACL**: dosya ve dizinlerde POSIX erişim denetim listelerini kopyalayın ve koruyun. Var olan tüm ACL 'Leri kaynaktan havuza kopyalar. 
- **Sahip**: dosya ve dizinlerin sahip olduğu kullanıcıyı kopyalayın ve koruyun. Data Lake Storage 2. havuza Süper Kullanıcı erişimi gerekiyor.
- **Grup**: sahip olan dosya ve dizinlerin grubunu kopyalayın ve koruyun. Havuz Data Lake Storage 2. veya sahip olan kullanıcıya yönelik süper kullanıcı erişimi (sahip olan kullanıcı aynı zamanda hedef grubun üyesiyse) gerekli.

Bir klasörden kopyalamayı belirtirseniz Data Factory, söz konusu klasör için ACL 'Leri ve altındaki dosyaları ve dizinleri çoğaltır, `recursive` true olarak ayarlanır. Tek bir dosyadan kopyalamayı belirtirseniz, bu dosyadaki ACL 'Ler kopyalanır.

>[!NOTE]
>Data Lake Storage 1./Gen2 ACL 'Lerini Gen2 'e korumak için ADF kullandığınızda, havuz Gen2's ilgili klasör/dosya üzerindeki mevcut ACL 'Lerin üzerine yazılır.

>[!IMPORTANT]
>ACL 'Leri korumayı seçtiğinizde, Data Factory havuz Data Lake Storage 2. hesabınızda çalışması için yeterince yüksek izinler verdiğinizden emin olun. Örneğin, hesap anahtarı kimlik doğrulamasını kullanın veya Depolama Blobu veri sahibi rolünü hizmet sorumlusu veya yönetilen kimliğe atayın.

Kaynağı ikili biçimi veya ikili kopyalama seçeneği ile Data Lake Storage 2. olarak Data Lake Storage 1./Gen2 olarak yapılandırdığınızda ve havuz, ikili biçimi veya ikili kopya seçeneği ile olarak, veri kopyalama aracında **Ayarlar** sayfasında veya etkinlik yazma için **etkinlik ayarlarını Kopyala** sekmesinde bulunan **koru** seçeneğini bulabilirsiniz  >   .

![Data Lake Storage 1./Gen2 to Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Kopyalama etkinliği JSON yapılandırmasına bir örnek (bkz `preserve` .): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği performansı](copy-activity-performance.md)
