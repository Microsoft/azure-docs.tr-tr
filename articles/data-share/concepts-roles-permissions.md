---
title: Azure Veri Paylaşımı rolleri ve gereksinimleri
description: Veri sağlayıcılarının ve veri tüketicilerinin Azure veri paylaşımında verileri paylaşması için erişim denetimi rolleri ve gereksinimleri hakkında bilgi edinin.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490587"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Veri Paylaşımı rolleri ve gereksinimleri 

Bu makalede, Azure veri paylaşımından verileri kabul etmek ve almak için gereken ve Azure veri paylaşımıyla verileri alacak olan roller açıklanmaktadır. 

## <a name="roles-and-requirements"></a>Roller ve gereksinimler

Azure veri paylaşımında, veri sağlayıcısı tarafından paylaşılacak verileri okuyabilmek ve veri tüketicisi olarak paylaşılan verileri alabilmesi için, Azure Hizmetleri (önceki adı Mssıs) için Yönetilen kimlikler kullanılır. Sonuç olarak, veri sağlayıcısı ve veri tüketicisi arasında kimlik bilgilerinin değişimi yoktur. 

Yönetilen Hizmet Kimliği, temel alınan depolama hesabına veya SQL veritabanına erişim verilmesi gerekir. Azure veri paylaşma hizmeti, verileri okumak ve yazmak için Azure veri paylaşımının kaynak Yönetilen Hizmet Kimliği kullanır. Azure veri paylaşımının kullanıcısına, verileri paylaştıkları depolama hesabına veya SQL veritabanına Yönetilen Hizmet Kimliği için bir rol ataması oluşturma olanağı gerekir. 

Depolama durumunda, rol atamaları oluşturma Izni **sahip** rolü, Kullanıcı erişimi yönetici rolü veya Microsoft. Authorization/role atamaları/yazma izni atanmış özel bir rol içinde bulunur. 

Söz konusu depolama hesabının sahibi değilseniz ve Azure veri paylaşımının kaynağının yönetilen kimliği için bir rol ataması oluşturmediğinizde, sizin adınıza rol ataması oluşturmak için bir Azure Yöneticisi isteyebilirsiniz. 

Veri paylaşımında kaynak tarafından yönetilen kimliğe atanan rollerin özeti aşağıda verilmiştir:

| |  |  |
|---|---|---|
|**Depolama türü**|**Veri Sağlayıcısı deposu**|**Veri tüketicisi hedef deposu**|
|Azure Blob Depolama| Depolama Blobu veri okuyucusu | Depolama Blobu veri Katılımcısı
|Azure Data Lake Gen1 | Sahip | Desteklenmiyor
|Azure Data Lake Gen2 | Depolama Blobu veri okuyucusu | Depolama Blobu veri Katılımcısı
|Azure SQL | dbo | dbo 
|

### <a name="data-providers"></a>Veri sağlayıcıları 
Bir Azure veri paylaşımında veri kümesi eklemek için veri sağlayıcılarının veri paylaşımında kaynak tarafından yönetilen kimliğin Depolama Blobu veri okuyucusu rolüne eklenmesi gerekir. Bu, Kullanıcı Azure aracılığıyla veri kümeleri ekliyor ve depolama hesabının sahibiyseniz ya da Microsoft. Authorization/role atama/yazma izni atanmış özel bir rolün üyesiyse Azure veri paylaşma hizmeti tarafından otomatik olarak yapılır. 

Alternatif olarak, kullanıcının bir Azure Yöneticisi, veri paylaşımının kaynak yönetimli kimliğini Depolama Blobu veri okuyucusu rolüne el ile eklemesini sağlayabilir. Bu rol atamasının yönetici tarafından el ile oluşturulması, depolama hesabının sahibi olma veya özel bir rol atamasının olması gereğini ortadan kaldırarak hükümsüz kılınacak. Bu, Azure Storage 'dan veya Azure Data Lake Gen2 paylaşılmakta olan veriler için geçerlidir. 

Azure Data Lake Gen1 'den veri paylaşıyorsanız, rol atamasının sahip rolüne yapılması gerekir. 

Veri paylaşımının yönetilen kimliği için bir rol ataması oluşturmak için aşağıdaki adımları izleyin:

1. Depolama hesabına gidin.
1. **Access Control (IAM)** seçeneğini belirleyin.
1. **Rol ataması Ekle**' yi seçin.
1. *Rol*altında, *Depolama Blobu veri okuyucusu*' nu seçin.
1. *Seç*' in altında, Azure veri paylaşma hesabınızın adını yazın.
1. *Kaydet* düğmesine tıklayın.

SQL tabanlı kaynaklar için, bir kullanıcının SQL veritabanındaki bir dış sağlayıcıdan, verileri Azure veri paylaşım hesabıyla aynı adla paylaşmakta olması gerekir. SQL tabanlı paylaşıma yönelik diğer önkoşullara birlikte örnek bir betik, [verilerinizi paylaşma](share-your-data.md) öğreticisinde bulunabilir. 

### <a name="data-consumers"></a>Veri tüketicileri
Veri almak için veri tüketicilerinin veri paylaşımının kaynak tarafından yönetilen kimliği, bir SQL veritabanına veri alıyorsanız SQL veritabanının Depolama Blobu veri katılımcısı rolüne ve/veya dbo rolüne eklenmelidir. 

Depolama durumunda, Kullanıcı Azure aracılığıyla veri kümeleri ekliyor ve depolama hesabının sahibiyseniz ya da Microsoft. Authorization/role atama/yazma iznine sahip özel bir rolün üyesiyse, bu, Azure veri paylaşma hizmeti tarafından otomatik olarak gerçekleştirilir atanan. 

Alternatif olarak, kullanıcının bir Azure Yöneticisi, veri paylaşımının kaynak tarafından yönetilen kimliğini Depolama Blobu veri katılımcısı rolüne el ile ekleyebilir. Bu rol atamasının yönetici tarafından el ile oluşturulması, depolama hesabının sahibi olma veya özel bir rol atamasının olması gereğini ortadan kaldırarak hükümsüz kılınacak. Bu, Azure Storage veya Azure Data Lake Gen2 ile paylaşılmakta olan veriler için geçerli olduğunu unutmayın. Azure Data Lake Gen1 'e veri alma desteklenmiyor. 

Veri paylaşımının yönetilen kimliği için el ile bir rol ataması oluşturmak için aşağıdaki adımları izleyin:

1. Depolama hesabına gidin.
1. **Access Control (IAM)** seçeneğini belirleyin.
1. **Rol ataması Ekle**' yi seçin.
1. *Rol*altında, *Depolama Blobu verileri katılımcısı*' nı seçin. 
1. *Seç*' in altında, Azure veri paylaşma hesabınızın adını yazın.
1. *Kaydet* düğmesine tıklayın.

REST API 'lerimizi kullanarak verileri paylaşıyorsanız, içindeki veri paylaşımı hesabını uygun rollere ekleyerek bu rol atamalarını el ile oluşturmanız gerekecektir. 

SQL tabanlı bir kaynağa veri alıyorsanız, Azure veri paylaşma hesabınızla aynı ada sahip bir dış sağlayıcıdan yeni bir kullanıcının oluşturulduğundan emin olun. [Verileri kabul et ve Al](subscribe-to-data-share.md) öğreticisindeki önkoşullara bakın. 

Rol ataması ekleme hakkında daha fazla bilgi edinmek için, Azure kaynağına nasıl rol ataması ekleneceğini özetleyen [Bu belgeye](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) başvurun. 

## <a name="resource-provider-registration"></a>Kaynak sağlayıcısı kaydı 

Bir Azure Veri Paylaşma davetini kabul ettiğinizde, Microsoft. DataShare kaynak sağlayıcısını aboneliğinize el ile kaydetmeniz gerekir. Microsoft. DataShare kaynak sağlayıcısını Azure aboneliğinize kaydetmek için aşağıdaki adımları izleyin. 

1. Azure portal **abonelikler**' e gidin.
1. Azure veri paylaşımında kullandığınız aboneliği seçin.
1. **Kaynak sağlayıcıları**' na tıklayın.
1. Microsoft. DataShare için arama yapın.
1. **Kaydol**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da roller hakkında daha fazla bilgi edinin- [rol tanımlarını anlayın](../role-based-access-control/role-definitions.md)

