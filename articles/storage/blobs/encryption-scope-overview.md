---
title: BLOB depolama için şifreleme kapsamları
description: Şifreleme kapsamları, kapsayıcının veya ayrı bir blob düzeyinde şifrelemeyi yönetme yeteneği sağlar. Aynı depolama hesabında bulunan ancak farklı müşterilere ait olan veriler arasında güvenli sınırlar oluşturmak için şifreleme kapsamlarını kullanabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640469"
---
# <a name="encryption-scopes-for-blob-storage"></a>BLOB depolama için şifreleme kapsamları

Şifreleme kapsamları, bir kapsayıcı veya ayrı bir blob kapsamında olan bir anahtarla şifrelemeyi yönetmenizi sağlar. Aynı depolama hesabında bulunan ancak farklı müşterilere ait olan veriler arasında güvenli sınırlar oluşturmak için şifreleme kapsamlarını kullanabilirsiniz.

Şifreleme kapsamları ile çalışma hakkında daha fazla bilgi için bkz. [şifreleme kapsamları oluşturma ve yönetme](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Şifreleme kapsamları nasıl çalışır?

Varsayılan olarak, bir depolama hesabı tüm depolama hesabının kapsamına alınmış bir anahtarla şifrelenir. Bir şifreleme kapsamı tanımladığınızda bir kapsayıcı veya ayrı bir blob kapsamında olabilecek bir anahtar belirtirsiniz. Şifreleme kapsamı bir bloba uygulandığında, blob bu anahtarla şifrelenir. Şifreleme kapsamı bir kapsayıcıya uygulandığında, o kapsayıcıya yüklenen tüm Bloblar aynı anahtarla şifrelenebilmesi için, bu kapsayıcıdaki Blobların varsayılan kapsamı olarak görev yapar. Kapsayıcı, kapsayıcıdaki tüm Bloblar için varsayılan şifreleme kapsamını zorlamak üzere veya tek bir Blobun varsayılan dışında bir şifreleme kapsamı ile kapsayıcıya yüklenmesine izin vermek üzere yapılandırılabilir.

Şifreleme kapsamı devre dışı bırakılmadığı sürece, bir şifreleme kapsamıyla oluşturulmuş bir blob üzerinde okuma işlemleri saydam bir şekilde gerçekleşir.

### <a name="key-management"></a>Anahtar yönetimi

Bir şifreleme kapsamı tanımladığınızda, kapsamın Microsoft tarafından yönetilen bir anahtarla mi yoksa Azure Key Vault depolanan müşteri tarafından yönetilen bir anahtarla mi korunabileceğini belirtebilirsiniz. Aynı depolama hesabındaki farklı şifreleme kapsamları, Microsoft tarafından yönetilen veya müşteri tarafından yönetilen anahtarları kullanabilir. Ayrıca, bir şifreleme kapsamını müşteri tarafından yönetilen anahtardan Microsoft tarafından yönetilen bir anahtara veya tam tersi yönde korumak için kullanılan anahtar türünü istediğiniz zaman değiştirebilirsiniz. Müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [Azure depolama şifrelemesi Için müşteri tarafından yönetilen anahtarlar](../common/customer-managed-keys-overview.md). Microsoft tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [şifreleme anahtarı yönetimi hakkında](../common/storage-service-encryption.md#about-encryption-key-management).

Müşteri tarafından yönetilen bir anahtarla bir şifreleme kapsamı tanımlarsanız, anahtar sürümünü otomatik olarak veya el ile güncelleştirmeyi seçebilirsiniz. Anahtar sürümünü otomatik olarak güncelleştirmeyi seçerseniz, Azure depolama, müşteri tarafından yönetilen anahtarın yeni bir sürümü için anahtar kasasını veya yönetilen HSM 'yi her gün denetler ve anahtarı en son sürüme otomatik olarak güncelleştirir. Müşteri tarafından yönetilen bir anahtarın anahtar sürümünü güncelleştirme hakkında daha fazla bilgi için bkz. [anahtar sürümünü güncelleştirme](../common/customer-managed-keys-overview.md#update-the-key-version).

Bir depolama hesabında, anahtar sürümünün otomatik olarak güncelleştirildiği, müşteri tarafından yönetilen anahtarlarla korunan en fazla 10.000 şifreleme kapsamı olabilir. Depolama hesabınızda zaten, müşteri tarafından yönetilen anahtarlarla korunan 10.000 şifreleme kapsamı varsa, müşteri tarafından yönetilen anahtarlarla korunan ek şifreleme kapsamları için anahtar sürümünün el ile güncelleştirilmeleri gerekir.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Kapsayıcılar ve BLOB 'lar için şifreleme kapsamları

Bir kapsayıcı oluşturduğunuzda, daha sonra o kapsayıcıya yüklenen Bloblar için varsayılan bir şifreleme kapsamı belirtebilirsiniz. Bir kapsayıcı için varsayılan bir şifreleme kapsamı belirttiğinizde, varsayılan şifreleme kapsamının nasıl zorlanacağına karar verebilirsiniz:

- Kapsayıcıya yüklenmiş tüm Blobların varsayılan şifreleme kapsamını kullanmasını zorunlu kılabilirsiniz. Bu durumda, kapsayıcıdaki her blob aynı anahtarla şifrelenir.
- Bir blob 'un varsayılan kapsam dışında bir şifreleme kapsamıyla karşıya yüklenebilmesi için, bir istemcinin kapsayıcı için varsayılan şifreleme kapsamını geçersiz kılmasına izin verebilirsiniz. Bu durumda, kapsayıcıdaki Bloblar farklı anahtarlarla şifrelenmiş olabilir.

Aşağıdaki tablo, kapsayıcı için varsayılan şifreleme kapsamının nasıl yapılandırıldığına bağlı olarak bir blob karşıya yükleme işleminin davranışını özetler:

| Kapsayıcıda tanımlanan şifreleme kapsamı... | Varsayılan şifreleme kapsamıyla blob karşıya yükleniyor... | Bir blob, varsayılan kapsam dışında bir şifreleme kapsamıyla karşıya yükleniyor... |
|--|--|--|
| Geçersiz kılmaları olan varsayılan şifreleme kapsamı | Başarıyla | Başarıyla |
| Geçersiz kılmaları olan varsayılan bir şifreleme kapsamı | Başarıyla | Tilir |

Kapsayıcının oluşturulduğu sırada bir kapsayıcı için varsayılan bir şifreleme kapsamı belirtilmelidir.

Kapsayıcı için varsayılan şifreleme kapsamı belirtilmemişse, depolama hesabı için tanımladığınız herhangi bir şifreleme kapsamını kullanarak bir blobu karşıya yükleyebilirsiniz. Şifreleme kapsamı, blob karşıya yüklendiği sırada belirtilmelidir.

## <a name="disabling-an-encryption-scope"></a>Şifreleme kapsamını devre dışı bırakma

Bir şifreleme kapsamını devre dışı bıraktığınızda, şifreleme kapsamıyla gerçekleştirilen sonraki okuma veya yazma işlemleri, HTTP hata kodu 403 (yasak) ile başarısız olur. Şifreleme kapsamını yeniden etkinleştirirseniz okuma ve yazma işlemleri normal olarak devam eder.

Bir şifreleme kapsamı devre dışı bırakıldığında, artık bu için faturalandırılırsınız. Gereksiz ücretlerden kaçınmak için gerekli olmayan tüm şifreleme kapsamlarını devre dışı bırakın.

Şifreleme kapsamınız müşteri tarafından yönetilen bir anahtarla korunuyorsa ve anahtar kasasındaki anahtarı silerseniz, veriler erişilemez hale gelir. Bunun için ücretlendirmeden kaçınmak için şifreleme kapsamını da devre dışı bıraktığınızdan emin olun.

Müşteri tarafından yönetilen anahtarların, anahtar kasasındaki geçici silme ve Temizleme koruması ile korunduğunu ve silinen bir anahtarın bu özellikler tarafından tanımlanan davranışa tabi olduğunu aklınızda bulundurun. Daha fazla bilgi için Azure Key Vault belgelerinde aşağıdaki konulardan birine bakın:

- [PowerShell ile geçici silmeyi kullanma](../../key-vault/general/key-vault-recovery.md)
- [CLı ile geçici silmeyi kullanma](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Bir şifreleme kapsamının silinmesi mümkün değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](../common/storage-service-encryption.md)
- [Şifreleme kapsamları oluşturma ve yönetme](encryption-scope-manage.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](../common/customer-managed-keys-overview.md)
- [Azure Key Vault nedir?](../../key-vault/general/overview.md)