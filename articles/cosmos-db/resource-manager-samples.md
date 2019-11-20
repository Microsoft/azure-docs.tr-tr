---
title: Azure Cosmos DB için Azure Resource Manager Şablonlar
description: Azure Cosmos DB oluşturmak ve yapılandırmak için Azure Resource Manager şablonları kullanın.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961848"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Resource Manager Şablonlar

Aşağıdaki tablolar Azure Cosmos DB için Azure Resource Manager şablonlarının bağlantılarını içerir:

## <a name="sql-core-api"></a>SQL (Core) API

|**Şablon**|**Açıklama**|
|---|---|
|[Azure Cosmos hesabı, veritabanı, kapsayıcı oluşturma](manage-sql-with-resource-manager.md#create-resource) | Bu şablon, paylaşılan veritabanı işleme ve adanmış aktarım hızı olan bir kapsayıcı olmak üzere iki kapsayıcı ile iki bölgede bir SQL (Core) API hesabı oluşturur. Verimlilik, güncelleştirilmiş işleme özelliği değeri olan şablonu yeniden göndererek güncelleştirilemeyebilir. |
|[Saklı yordam, tetikleyici ve UDF ile bir Azure Cosmos hesabı, veritabanı ve kapsayıcısı oluşturma](manage-sql-with-resource-manager.md#create-sproc) | Bu şablon, bir kapsayıcı için bir saklı yordam, tetikleyici ve UDF ile iki bölgede bir SQL (Core) API hesabı oluşturur. |

## <a name="mongodb-api"></a>MongoDB API’si

|**Şablon**|**Açıklama**|
|---| ---|
|[Azure Cosmos hesabı, veritabanı, koleksiyon oluşturma](manage-mongodb-with-resource-manager.md#create-resource) | Bu şablon, çoklu yönetici özellikli iki bölgede MongoDB için Azure Cosmos DB API kullanarak bir hesap oluşturur. Azure Cosmos hesabının, veritabanı düzeyinde üretilen işi paylaşan iki kapsayıcısı olacaktır. |

## <a name="cassandra-api"></a>Cassandra API’si

|**Şablon**|**Açıklama**|
|---| ---|
|[Azure Cosmos hesabı, keyspace, tablo oluşturma](manage-cassandra-with-resource-manager.md#create-resource) | Bu şablon, çoklu yönetici özellikli iki bölgede bir Cassandra API hesabı oluşturur. Azure Cosmos hesabının, anahtar alanı düzeyinde üretilen işi paylaşan iki tablosu olacaktır. |

## <a name="gremlin-api"></a>Gremlin API

|**Şablon**|**Açıklama**|
|---| ---|
|[Azure Cosmos hesabı, veritabanı, grafik oluşturma](manage-gremlin-with-resource-manager.md#create-resource) | Bu şablon, çoklu yönetici özellikli iki bölgede Gremlin API hesabı oluşturur. Azure Cosmos hesabında, veritabanı düzeyinde üretilen işi paylaşan iki grafik olacaktır. |

## <a name="table-api"></a>Tablo API’si

|**Şablon**|**Açıklama**|
|---| ---|
|[Azure Cosmos hesabı, tablosu oluşturma](manage-table-with-resource-manager.md#create-resource) | Bu şablon, çoklu yönetici özellikli iki bölgede bir Tablo API'si hesabı oluşturur. Azure Cosmos hesabının tek bir tablosu olacaktır. |

> [!TIP]
> Tablo API'si kullanırken paylaşılan aktarım hızını etkinleştirmek için, Azure portalında hesap düzeyinde aktarım hızını etkinleştirin.

Başvuru belgelerinin Azure Cosmos DB sayfasına yönelik [Azure Resource Manager başvuru](/azure/templates/microsoft.documentdb/allversions) bölümüne bakın.
