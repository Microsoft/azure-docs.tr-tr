---
title: Hesap şifreleme için müşteri tarafından yönetilen anahtarlar
titleSuffix: Azure Storage
description: Depolama hesabınızdaki verileri korumak için kendi şifreleme anahtarınızı kullanabilirsiniz. Müşteri tarafından yönetilen bir anahtar belirttiğinizde verilerinizi şifrelemek ve verilerinize erişimi denetlemek için bu anahtar kullanılır. Müşteri tarafından yönetilen anahtarlar erişim denetimlerini yönetmek için çok daha fazla esneklik sunar.
services: storage
author: tamram
ms.service: storage
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 07f8faf503bdea6be8263afa6240594956b61391
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059455"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar

Depolama hesabınızdaki verileri korumak için kendi şifreleme anahtarınızı kullanabilirsiniz. Müşteri tarafından yönetilen bir anahtar belirttiğinizde verilerinizi şifrelemek ve verilerinize erişimi denetlemek için bu anahtar kullanılır. Müşteri tarafından yönetilen anahtarlar erişim denetimlerini yönetmek için çok daha fazla esneklik sunar.

Müşteri tarafından yönetilen anahtarlarınızı depolamak için aşağıdaki Azure anahtar depolarından birini kullanmanız gerekir:

- [Azure Key Vault](../../key-vault/general/overview.md)
- [Azure Key Vault yönetilen donanım güvenlik modülü (HSM) (Önizleme)](../../key-vault/managed-hsm/overview.md)

Kendi anahtarlarınızı oluşturabilir ve bunları anahtar kasasında veya yönetilen HSM 'de saklayabilir ya da anahtarlar oluşturmak için Azure Key Vault API 'Lerini kullanabilirsiniz. Depolama hesabı ve Anahtar Kasası ya da yönetilen HSM aynı bölgede ve aynı Azure Active Directory (Azure AD) kiracısında olmalıdır, ancak farklı aboneliklerde olabilir.

> [!IMPORTANT]
>
> Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifreleme Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
>
> Azure Key Vault ve Azure Key Vault yönetilen HSM, yapılandırma için aynı API 'Leri ve yönetim arabirimlerini destekler.

## <a name="about-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlar hakkında

Aşağıdaki diyagramda, Azure depolama 'nın, müşteri tarafından yönetilen anahtarı kullanarak istek yapmak için Azure Active Directory ve bir anahtar kasası ya da yönetilen HSM kullanımı gösterilmektedir:

![Müşteri tarafından yönetilen anahtarların Azure Storage 'da nasıl çalıştığını gösteren diyagram](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

Aşağıdaki listede, diyagramdaki numaralandırılmış adımlar açıklanmaktadır:

1. Azure Key Vault Yöneticisi, depolama hesabıyla ilişkili yönetilen kimliğe şifreleme anahtarları için izinler verir.
2. Azure Depolama Yöneticisi, depolama hesabı için müşteri tarafından yönetilen bir anahtarla şifrelemeyi yapılandırır.
3. Azure depolama, Azure Active Directory üzerinden Azure Key Vault erişimin kimliğini doğrulamak için depolama hesabıyla ilişkili yönetilen kimliği kullanır.
4. Azure depolama, hesap şifreleme anahtarını Azure Key Vault müşteri anahtarıyla sarmalanmış.
5. Okuma/yazma işlemleri için, Azure depolama, şifreleme ve şifre çözme işlemleri gerçekleştirmek üzere hesap şifreleme anahtarını sarmalamak için Azure Key Vault istekleri gönderir.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Kuyruklar ve tablolar için müşteri tarafından yönetilen anahtarlar

Depolama hesabı için müşteri tarafından yönetilen anahtarlar etkinleştirildiğinde, kuyruk ve tablo depolaması 'nda depolanan veriler, müşteri tarafından yönetilen bir anahtarla otomatik olarak korunmaz. Bu hizmetleri isteğe bağlı olarak, depolama hesabı oluştururken bu korumaya dahil edilecek şekilde yapılandırabilirsiniz.

Kuyruklar ve tablolar için müşteri tarafından yönetilen anahtarları destekleyen bir depolama hesabı oluşturma hakkında daha fazla bilgi için, bkz. [Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap oluşturma](account-encryption-key-create.md).

Depolama hesabı için müşteri tarafından yönetilen anahtarlar yapılandırıldığında, BLOB depolama ve Azure dosyalarındaki veriler her zaman müşteri tarafından yönetilen anahtarlarla korunur.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Bir depolama hesabı için müşteri tarafından yönetilen anahtarları etkinleştirin

Müşteri tarafından yönetilen bir anahtar yapılandırdığınızda, Azure depolama, hesap için kök veri şifreleme anahtarını ilişkili anahtar kasasında veya yönetilen HSM 'de müşteri tarafından yönetilen anahtarla sarmalanmış. Müşteri tarafından yönetilen anahtarların etkinleştirilmesi performansı etkilemez ve hemen yürürlüğe girer.

Müşteri tarafından yönetilen anahtarları etkinleştirdiğinizde veya devre dışı bıraktığınızda veya anahtarı ya da anahtar sürümünü değiştirdiğinizde, kök şifreleme anahtarının korunması değişir, ancak Azure Depolama hesabınızdaki verilerin yeniden şifrelenmesi gerekmez.

Müşterinin yönettiği anahtarlar yalnızca mevcut depolama hesaplarında etkinleştirilebilir. Anahtar Kasası veya yönetilen HSM, depolama hesabıyla ilişkili yönetilen kimliğe izinler verecek şekilde yapılandırılmalıdır. Yönetilen kimlik yalnızca depolama hesabı oluşturulduktan sonra kullanılabilir.

Müşteri tarafından yönetilen anahtarlar ile Microsoft tarafından yönetilen anahtarlar arasında istediğiniz zaman geçiş yapabilirsiniz. Microsoft tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [şifreleme anahtarı yönetimi hakkında](storage-service-encryption.md#about-encryption-key-management).

Azure depolama şifrelemesini bir anahtar kasasındaki müşteri tarafından yönetilen anahtarlarla yapılandırma hakkında bilgi edinmek için bkz. [Azure Key Vault 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](customer-managed-keys-configure-key-vault.md). Yönetilen bir HSM 'de müşteri tarafından yönetilen anahtarları yapılandırmak için, bkz. [YÖNETILEN HSM 'de depolanan Azure Key Vault, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure AD 'nin bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Yönetilen kimlikler Şu anda çapraz dizin senaryolarını desteklemez. Azure portal müşteri tarafından yönetilen anahtarları yapılandırırken, yönetilen bir kimlik, kapakları altında depolama hesabınıza otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya depolama hesabını bir Azure AD dizininden diğerine taşırsanız, depolama hesabıyla ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. SSS 'de **Azure AD dizinleri arasında bir abonelik aktarma** [ve Azure kaynakları için yönetilen kimliklerle ilgili bilinen sorunlar](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

Azure depolama şifrelemesi, 2048, 3072 ve 4096 boyutlarının RSA ve RSA-HSM anahtarlarını destekler. Anahtarlar hakkında daha fazla bilgi için bkz. [anahtarlar hakkında](../../key-vault/keys/about-keys.md).

Anahtar kasasının veya yönetilen HSM 'nin kullanılması, ilişkili maliyetlere sahiptir. Daha fazla bilgi için bkz. [Key Vault fiyatlandırması](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Şifrelemeyi, müşteri tarafından yönetilen anahtarlarla yapılandırdığınızda, anahtar sürümünü güncelleştirmek için iki seçeneğiniz vardır:

- **Anahtar sürümünü otomatik olarak güncelleştir:** Yeni bir sürüm kullanılabilir olduğunda müşterinin yönettiği bir anahtarı otomatik olarak güncelleştirmek için, depolama hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi etkinleştirdiğinizde anahtar sürümünü atlayın. Anahtar sürümü atlanırsa Azure Storage, müşteri tarafından yönetilen bir anahtarın yeni bir sürümü için anahtar kasasını veya yönetilen HSM 'yi günlük olarak denetler. Azure depolama, anahtarın en son sürümünü otomatik olarak kullanır.
- **Anahtar sürümünü el ile güncelleştirin:** Azure depolama şifrelemesi için bir anahtarın belirli bir sürümünü kullanmak üzere, depolama hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi etkinleştirdiğinizde bu anahtar sürümünü belirtin. Anahtar sürümünü belirtirseniz, anahtar sürümünü el ile güncelleştirene kadar Azure Storage bu sürümü şifreleme için kullanır.

    Anahtar sürümü açıkça belirtildiğinde, yeni bir sürüm oluşturulduğunda depolama hesabını yeni anahtar sürümü URI 'sini kullanacak şekilde el ile güncelleştirmeniz gerekir. Depolama hesabını anahtarın yeni bir sürümünü kullanacak şekilde güncelleştirme hakkında bilgi edinmek için bkz. [Azure Key Vault depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](customer-managed-keys-configure-key-vault.md) veya [Azure Key Vault yönetilen HSM 'de (Önizleme) depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi](customer-managed-keys-configure-key-vault-hsm.md)yapılandırma.

Anahtar sürümünü güncelleştirdiğinizde, kök şifreleme anahtarının korunması değişir, ancak Azure Depolama hesabınızdaki veriler yeniden şifrelenmez. Kullanıcıdan başka bir eylem gerekli değildir.

> [!NOTE]
> Bir anahtarı döndürmek için, uyumluluk ilkelerinize göre anahtar kasasında veya yönetilen HSM 'de anahtarın yeni bir sürümünü oluşturun. Anahtarınızı el ile döndürebilir veya bir zamanlamaya göre döndürmek için bir işlev oluşturabilirsiniz.

## <a name="revoke-access-to-customer-managed-keys"></a>Müşterinin yönettiği anahtarlara erişimi iptal etme

Depolama hesabının müşterinin yönettiği anahtara erişimini istediğiniz zaman iptal edebilirsiniz. Müşteri tarafından yönetilen anahtarlara erişim iptal edildiğinde veya anahtar devre dışı bırakıldıktan veya silindikten sonra istemciler, bir Blobun veya meta verilerinden okuma veya yazma işlemlerini çağıramaz. Aşağıdaki işlemlerden herhangi birini çağırma girişimleri, tüm kullanıcılar için 403 (yasak) hata kodu ile başarısız olur:

- İstek URI 'sindeki parametresiyle çağrıldığında [Blobları Listele](/rest/api/storageservices/list-blobs) `include=metadata`
- [Blob al](/rest/api/storageservices/get-blob)
- [BLOB özelliklerini Al](/rest/api/storageservices/get-blob-properties)
- [Blob meta verilerini al](/rest/api/storageservices/get-blob-metadata)
- [Blob meta verilerini ayarla](/rest/api/storageservices/set-blob-metadata)
- İstek üstbilgisiyle çağrıldığında [anlık görüntü blobu](/rest/api/storageservices/snapshot-blob) `x-ms-meta-name`
- [İkili Büyük Nesneyi Kopyalama](/rest/api/storageservices/copy-blob)
- [Blob 'U URL 'Den Kopyala](/rest/api/storageservices/copy-blob-from-url)
- [Blob Katmanını Ayarla](/rest/api/storageservices/set-blob-tier)
- [Yerleştirme bloğu](/rest/api/storageservices/put-block)
- [Bloğu URL 'Den koy](/rest/api/storageservices/put-block-from-url)
- [Ekleme bloğu](/rest/api/storageservices/append-block)
- [URL 'Den ekleme bloğu](/rest/api/storageservices/append-block-from-url)
- [İkili Büyük Nesne Koyma](/rest/api/storageservices/put-blob)
- [Yerleştirme sayfası](/rest/api/storageservices/put-page)
- [URL 'Den sayfa koy](/rest/api/storageservices/put-page-from-url)
- [Artımlı kopya blobu](/rest/api/storageservices/incremental-copy-blob)

Bu işlemleri yeniden çağırmak için, müşteri tarafından yönetilen anahtara erişimi geri yükleyin.

Bu bölümde listelenmeyen tüm veri işlemleri, müşteri tarafından yönetilen anahtarlar iptal edildiğinde veya bir anahtar devre dışı bırakıldıktan veya silindikten sonra devam edebilir.

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) veya [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys)kullanın.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure yönetilen diskler için müşteri tarafından yönetilen anahtarlar

Azure yönetilen disklerin şifrelenmesini yönetmek için müşteri tarafından yönetilen anahtarlar da kullanılabilir. Müşteri tarafından yönetilen anahtarlar, yönetilen diskler için Azure depolama kaynaklarından farklı şekilde davranır. Daha fazla bilgi için bkz. Windows için [Azure yönetilen disklerinin sunucu tarafı şifrelemesi](../../virtual-machines/disk-encryption.md) veya Linux için [Azure yönetilen disklerinin sunucu tarafı şifrelemesi](../../virtual-machines/disk-encryption.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure Key Vault’ta depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](customer-managed-keys-configure-key-vault-hsm.md)
