---
title: Azure Veri Paylaşımı rolleri ve gereksinimleri
description: Azure veri paylaşımının kullanıldığı verileri paylaşmak ve almak için gereken izinler hakkında bilgi edinin.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644252"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Veri Paylaşımı rolleri ve gereksinimleri 

Bu makalede, Azure veri paylaşma hizmeti 'ni kullanarak veri paylaşmak ve almak için gereken roller ve izinler açıklanmaktadır. 

## <a name="roles-and-requirements"></a>Roller ve gereksinimler

Azure veri paylaşma hizmeti ile veri sağlayıcısı ile tüketici arasında kimlik bilgileri alışverişi yapmadan veri paylaşabilirsiniz. Azure veri paylaşımı hizmeti, anlık görüntü tabanlı paylaşım için Azure veri deposunda kimlik doğrulaması yapmak üzere yönetilen kimlikler (önceki adıyla MSI) kullanır. Azure veri paylaşımının kaynağına ait yönetilen kimliğe veri okumak veya yazmak için Azure veri deposuna erişim verilmesi gerekir.

Bir Azure veri deposundan veri paylaşmak veya almak için, kullanıcının en azından aşağıdaki izinlere ihtiyacı vardır. 

* Azure veri deposuna yazma izni. Genellikle, bu izin **katkıda** bulunan rolünde bulunur.

Depolama ve Data Lake anlık görüntü tabanlı paylaşım için, Azure veri deposunda rol ataması oluşturma izniniz de vardır. Genellikle, rol atamaları oluşturma izni **sahip** rolü, Kullanıcı erişimi yönetici rolü veya *Microsoft. Authorization/role atamaları/yazma* izni atanmış özel bir rol içinde bulunur. Veri paylaşımının yönetilen kimliği zaten Azure veri deposuna erişim izni verildiyse, bu izin gerekli değildir. Aşağıda, veri paylaşımının kaynak yönetimli kimliğine atanan rollerin özeti verilmiştir:

|**Veri deposu türü**|**Kaynak veri deposu Veri Sağlayıcısı**|**Veri tüketicisi hedef veri deposu**|
|---|---|---|
|Azure Blob Depolama| Depolama Blob Verileri Okuyucusu | Depolama Blob Verileri Katkıda Bulunanı
|Azure Data Lake Gen1 | Sahip | Desteklenmiyor
|Azure Data Lake Gen2 | Depolama Blob Verileri Okuyucusu | Depolama Blob Verileri Katkıda Bulunanı
|

SQL Snapshot tabanlı paylaşım için, Azure SQL veritabanı 'nda Azure veri paylaşımı kaynağıyla aynı ada sahip bir dış sağlayıcıdan bir SQL kullanıcısının oluşturulması gerekir. Bu kullanıcıyı oluşturmak için yönetici izni Azure Active Directory gereklidir. SQL kullanıcısının gerektirdiği iznin özeti aşağıda verilmiştir.

|**SQL veritabanı türü**|**Veri Sağlayıcısı SQL Kullanıcı Izni**|**Veri tüketicisi SQL Kullanıcı Izni**|
|---|---|---|
|Azure SQL Veritabanı | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Veri sağlayıcısı
Depolama ve Data Lake Snapshot tabanlı paylaşım için, Azure veri paylaşımında bir veri kümesi eklemek üzere, sağlayıcının veri paylaşımı kaynağına yönetilen kimliğin kaynak Azure veri deposuna erişim verilmesi gerekir. Örneğin, depolama hesabı durumunda, veri paylaşımının kaynak olarak yönetilen kimliği, *Depolama Blobu veri okuyucusu* rolüne sahiptir. Bu, Kullanıcı Azure portal aracılığıyla veri kümesi eklerken ve Kullanıcı uygun izne sahip olduğunda Azure veri paylaşma hizmeti tarafından otomatik olarak gerçekleştirilir. Örneğin, Kullanıcı Azure veri deposunun sahibidir veya *Microsoft. Authorization/role atama/yazma* izninin atandığı özel bir rolün üyesidir. 

Alternatif olarak, Kullanıcı Azure veri deposunun sahibine sahip olabilir. veri paylaşımının kaynak yönetilen kimliğini Azure veri deposuna el ile ekleyin. Bu eylemin veri paylaşma kaynağı başına yalnızca bir kez gerçekleştirilmesi gerekir. Veri paylaşımının yönetilen kimliği için el ile bir rol ataması oluşturmak için aşağıdaki adımları izleyin.  

1. Azure veri deposuna gidin.
1. **Access Control (IAM)** seçeneğini belirleyin.
1. **Rol ataması Ekle**' yi seçin.
1. *Rol* altında, yukarıdaki rol atama tablosunda bulunan rolü seçin (örneğin, depolama hesabı Için, *Depolama Blobu veri okuyucusu*' nu seçin).
1. *Seç*' in altında, Azure veri paylaşma kaynağınızın adını yazın.
1. *Kaydet*’e tıklayın.

Rol atama hakkında daha fazla bilgi edinmek için [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)bölümüne bakın. REST API 'Leri kullanarak verileri paylaşıyorsanız, [REST API kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-rest.md)' ya başvurarak API kullanarak rol ataması oluşturabilirsiniz. 

SQL Snapshot tabanlı paylaşım için SQL kullanıcısının SQL veritabanı 'nda Azure Active Directory kimlik doğrulaması kullanılarak SQL veritabanı 'na bağlanırken Azure veri paylaşımı kaynağıyla aynı ada sahip bir dış sağlayıcıdan oluşturulması gerekir. Bu kullanıcıya *db_datareader* izni verilmesi gerekir. SQL tabanlı paylaşıma yönelik diğer önkoşullara birlikte örnek bir betik, [Azure SQL veritabanı veya Azure SYNAPSE Analytics](how-to-share-from-sql.md) öğreticisinde bulunabilir. 

### <a name="data-consumer"></a>Veri tüketicisi
Depolama hesabına veri almak için, tüketici veri paylaşımının kaynağına ait yönetilen kimliğe, hedef depolama hesabına erişim verilmesi gerekir. Veri paylaşımının kaynağına ait yönetilen kimliğe, *Depolama Blobu veri katılımcısı* rolü verilmelidir. Kullanıcı Azure portal aracılığıyla bir hedef depolama hesabı belirtiyorsa ve Kullanıcı uygun izinlere sahipse, bu, Azure veri paylaşma hizmeti tarafından otomatik olarak gerçekleştirilir. Örneğin, Kullanıcı depolama hesabının sahibidir veya *Microsoft. Authorization/role atama/yazma* izninin atandığı özel bir rolün üyesidir. 

Alternatif olarak, Kullanıcı depolama hesabına sahip olabilir ve veri paylaşımının kaynak yönetilen kimliğini depolama hesabına el ile ekleyin. Bu eylemin veri paylaşma kaynağı başına yalnızca bir kez gerçekleştirilmesi gerekir. Veri paylaşımının yönetilen kimliği için el ile bir rol ataması oluşturmak için aşağıdaki adımları izleyin. 

1. Azure veri deposuna gidin.
1. **Access Control (IAM)** seçeneğini belirleyin.
1. **Rol ataması Ekle**' yi seçin.
1. *Rol* altında, yukarıdaki rol atama tablosunda bulunan rolü seçin (örneğin, depolama hesabı Için, *Depolama Blobu veri okuyucusu*' nu seçin).
1. *Seç*' in altında, Azure veri paylaşma kaynağınızın adını yazın.
1. *Kaydet*’e tıklayın.

Rol atama hakkında daha fazla bilgi edinmek için [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)bölümüne bakın. REST API 'Lerini kullanarak veri alıyorsanız, [REST API kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-rest.md)' ya başvurarak API kullanarak rol ataması oluşturabilirsiniz. 

SQL tabanlı hedef için, SQL veritabanı Azure Active Directory kimlik doğrulaması kullanılarak SQL veritabanı 'na bağlanırken Azure veri paylaşma kaynağıyla aynı ada sahip SQL veritabanı 'nda bir dış sağlayıcıdan oluşturulması gerekir. Bu kullanıcıya *db_datareader, db_datawriter db_ddladmin* izin verilmesi gerekir. SQL tabanlı paylaşıma yönelik diğer önkoşullara birlikte örnek bir betik, [Azure SQL veritabanı veya Azure SYNAPSE Analytics](how-to-share-from-sql.md) öğreticisinde bulunabilir. 

## <a name="resource-provider-registration"></a>Kaynak sağlayıcısı kaydı 

Aşağıdaki senaryolarda Microsoft. DataShare kaynak sağlayıcısını Azure aboneliğinize el ile kaydetmeniz gerekebilir: 

* Azure kiracınızda Azure Veri Paylaşma davetini ilk kez görüntüleyin
* Azure veri paylaşımından farklı bir Azure aboneliğindeki verileri Azure veri deposundan paylaşma
* Azure Data Share kaynağından farklı bir Azure aboneliğindeki verileri Azure veri deposuna alma

Microsoft. DataShare kaynak sağlayıcısını Azure aboneliğinize kaydetmek için aşağıdaki adımları izleyin. Kaynak sağlayıcısını kaydetmek için Azure aboneliğine *katkıda bulunan* erişime ihtiyacınız vardır.

1. Azure portal **abonelikler**' e gidin.
1. Azure veri paylaşımında kullandığınız aboneliği seçin.
1. **Kaynak sağlayıcıları**' na tıklayın.
1. Microsoft. DataShare için arama yapın.
1. **Kaydet**’e tıklayın.
 
Kaynak sağlayıcısı hakkında daha fazla bilgi edinmek için [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da roller hakkında daha fazla bilgi edinin- [Azure rol tanımlarını anlayın](../role-based-access-control/role-definitions.md)