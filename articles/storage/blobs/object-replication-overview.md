---
title: Nesne çoğaltmasına genel bakış
titleSuffix: Azure Storage
description: Nesne çoğaltma, blok bloblarını bir kaynak depolama hesabı ve hedef hesap arasında zaman uyumsuz olarak kopyalar. Okuma isteklerindeki gecikme süresini en aza indirmek için, işlem iş yüklerinin verimliliğini artırmak, veri dağıtımını iyileştirmek ve maliyetleri en aza indirmek için nesne çoğaltmasını kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 391c33e72f45e7c0c0b56128b32a8e73399e417a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834332"
---
# <a name="object-replication-for-block-blobs"></a>Blok Blobları için nesne çoğaltma

Nesne çoğaltma, blok bloblarını bir kaynak depolama hesabı ve hedef hesap arasında zaman uyumsuz olarak kopyalar. Nesne çoğaltma tarafından desteklenen bazı senaryolar şunlardır:

- **En aza indirme gecikmesi.** Nesne çoğaltma, istemcilerin daha yakın bir fiziksel yakınlık alanında bulunan bir bölgeden veri tüketmesini sağlayarak okuma istekleri için gecikme süresini azaltabilir.
- **İşlem iş yükleri için verimliliği artırın.** İşlem iş yükleri, nesne çoğaltma ile farklı bölgelerde aynı blok blob kümelerini işleyebilir.
- **Veri dağıtımını iyileştirme.** Verileri tek bir konumda işleyebilir veya analiz edebilir ve sonra yalnızca sonuçları ek bölgelere çoğaltabilirsiniz.
- **Maliyetleri iyileştirme.** Verileriniz çoğaltıldıktan sonra, yaşam döngüsü yönetimi ilkelerini kullanarak bunu arşiv katmanına taşıyarak maliyetleri azaltabilirsiniz.

Aşağıdaki diyagramda, nesne çoğaltmanın, blok bloblarını bir bölgedeki kaynak depolama hesabından iki farklı bölgedeki hedef hesaplara nasıl çoğaltacağını gösterir.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Nesne çoğaltmasının nasıl çalıştığını gösteren diyagram":::

Nesne çoğaltmasını yapılandırma hakkında bilgi edinmek için bkz. [nesne çoğaltmasını yapılandırma](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Nesne çoğaltma için önkoşullar

Nesne çoğaltma, aşağıdaki Azure depolama özelliklerinin de etkinleştirilmesini gerektirir:

- [Değişiklik akışı](storage-blob-change-feed.md): kaynak hesapta etkin olmalıdır. Değişiklik akışını etkinleştirmeyi öğrenmek için bkz. [değişiklik akışını etkinleştirme ve devre dışı bırakma](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [BLOB sürümü oluşturma](versioning-overview.md): hem kaynak hem de hedef hesaplarda etkinleştirilmelidir. Sürüm oluşturmayı nasıl etkinleştireceğinizi öğrenmek için bkz. [BLOB sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md).

Değişiklik akışı ve BLOB sürümü oluşturma özelliğinin etkinleştirilmesi ek ücret ödemeniz gerekebilir. Daha fazla ayrıntı için [Azure Depolama fiyatlandırması sayfasına](https://azure.microsoft.com/pricing/details/storage/)bakın.

Nesne çoğaltma yalnızca genel amaçlı v2 depolama hesapları için desteklenir. Kaynak ve hedef hesapların her ikisi de genel amaçlı v2 olmalıdır. 

## <a name="how-object-replication-works"></a>Nesne çoğaltma nasıl çalışacaktır?

Nesne çoğaltma, yapılandırdığınız kurallara göre kapsayıcıda blok bloblarını zaman uyumsuz olarak kopyalar. Blob içerikleri, blob ile ilişkili tüm sürümler ve Blobun meta verileri ve özellikleri, kaynak kapsayıcıdan hedef kapsayıcıya kopyalanır.

> [!IMPORTANT]
> Blok Blobu verileri zaman uyumsuz olarak çoğaltıldığından, kaynak hesap ve hedef hesap hemen eşitlenmiş durumda değildir. Şu anda, hedef hesaba veri çoğaltmak için ne kadar süreceğine ilişkin bir SLA yoktur. Çoğaltmanın tamamlanıp tamamlanmadığını öğrenmek için kaynak Blobun çoğaltma durumunu kontrol edebilirsiniz. Daha fazla bilgi için bkz. [bir Blobun çoğaltma durumunu denetleme](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Blob sürümü oluşturma

Nesne çoğaltma, hem kaynak hem de hedef hesaplarda blob sürümü oluşturma özelliğinin etkinleştirilmesini gerektirir. Kaynak hesapta çoğaltılan bir blob değiştirildiğinde, kaynak hesapta, blob 'un önceki durumunu yansıtan önce, değişiklik yapmadan önce blob 'un yeni bir sürümü oluşturulur. Kaynak hesaptaki geçerli sürüm (veya temel blob) en son güncelleştirmeleri yansıtır. Hem güncelleştirilmiş güncel sürüm hem de yeni önceki sürüm hedef hesaba çoğaltılır. Yazma işlemlerinin blob sürümlerini nasıl etkilediği hakkında daha fazla bilgi için bkz. [yazma Işlemlerinde sürüm oluşturma](versioning-overview.md#versioning-on-write-operations).

Kaynak hesaptaki bir blob silindiğinde, Blobun geçerli sürümü önceki bir sürümde yakalanır ve sonra silinir. Blob 'un önceki tüm sürümleri, geçerli sürüm silindikten sonra bile devam ederse. Bu durum hedef hesaba çoğaltılır. Silme işlemlerinin blob sürümlerini nasıl etkilediği hakkında daha fazla bilgi için bkz. [Delete Işlemlerinde sürüm oluşturma](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Anlık Görüntüler

Nesne çoğaltma, blob anlık görüntülerini desteklemez. Kaynak hesaptaki bir blobdaki tüm anlık görüntüler hedef hesaba çoğaltılmaz.

### <a name="blob-tiering"></a>Blob katmanlama

Kaynak ve hedef hesaplar sık erişimli veya seyrek erişimli katmanda olduğunda nesne çoğaltma desteklenir. Kaynak ve hedef hesaplar farklı katmanlarda olabilir. Ancak, kaynak veya hedef hesaptaki bir blob arşiv katmanına taşınmışsa nesne çoğaltma başarısız olur. Blob katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama Için erişim katmanları-sık erişimli, seyrek erişimli ve arşiv](storage-blob-storage-tiers.md).

### <a name="immutable-blobs"></a>Sabit bloblar

Nesne çoğaltma, sabit Blobları desteklemez. Kaynak veya hedef kapsayıcıda zaman tabanlı bir bekletme ilkesi veya yasal tutma varsa, nesne çoğaltma başarısız olur. Sabit Bloblar hakkında daha fazla bilgi için bkz. [sabit depolamayla iş açısından kritik blob verilerini depolama](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Nesne çoğaltma ilkeleri ve kuralları

Nesne çoğaltmasını yapılandırdığınızda, kaynak depolama hesabı ve hedef hesap belirten bir çoğaltma ilkesi oluşturursunuz. Çoğaltma İlkesi, kaynak kapsayıcısını ve hedef kapsayıcıyı belirten bir veya daha fazla kural içerir ve kaynak kapsayıcısındaki hangi blok bloblarının çoğaltılacağı anlamına gelir.

Nesne çoğaltmasını yapılandırdıktan sonra Azure depolama, kaynak hesabın değişiklik akışını düzenli aralıklarla denetler ve yazma veya silme işlemlerini zaman uyumsuz olarak hedef hesaba çoğaltır. Çoğaltma gecikmesi, çoğaltılan blok Blobun boyutuna bağlıdır.

### <a name="replication-policies"></a>Çoğaltma ilkeleri

Nesne çoğaltmasını yapılandırdığınızda, Azure depolama kaynak sağlayıcısı aracılığıyla hem kaynak hesapta hem de hedef hesapta bir çoğaltma ilkesi oluşturulur. Çoğaltma İlkesi bir ilke KIMLIĞI tarafından tanımlanır. Çoğaltmanın gerçekleşmesi için kaynak ve hedef hesapların ilke KIMLIĞI aynı olmalıdır.

Bir depolama hesabı, en fazla iki hedef hesap için kaynak hesap olarak görev yapabilir. Kaynak ve hedef hesaplar aynı bölgede veya farklı bölgelerde olabilir. Farklı aboneliklerde ve farklı Azure Active Directory (Azure AD) kiracılarında da bulunabilir. Her kaynak hesap/hedef hesap çifti için yalnızca bir çoğaltma ilkesi oluşturulabilir.

### <a name="replication-rules"></a>Çoğaltma kuralları

Çoğaltma kuralları, Azure depolama 'nın blob 'ları bir kaynak kapsayıcısından hedef kapsayıcıya nasıl çoğaltacağınızı belirtir. Her çoğaltma ilkesi için en fazla 10 çoğaltma kuralı belirtebilirsiniz. Her çoğaltma kuralı tek bir kaynak ve hedef kapsayıcı tanımlar ve her kaynak ve hedef kapsayıcı yalnızca bir kuralda kullanılabilir.

Bir çoğaltma kuralı oluşturduğunuzda, varsayılan olarak yalnızca kaynak kapsayıcıya daha sonra eklenen yeni blok Blobları kopyalanır. Hem yeni hem de var olan blok bloblarının kopyalanmasını belirtebilir veya belirli bir zamandan itibaren oluşturulan blok bloblarını kopyalayan özel bir kopya kapsamı tanımlayabilirsiniz.

Ayrıca, blok bloblarını ön eke göre filtrelemek için bir çoğaltma kuralının bir parçası olarak bir veya daha fazla filtre belirtebilirsiniz. Bir ön ek belirttiğinizde, yalnızca kaynak kapsayıcıda bu önekle eşleşen Bloblar hedef kapsayıcıya kopyalanacaktır.

Kaynak ve hedef kapsayıcıların her ikisi de bir kuralda belirtmeleri için mevcut olmalıdır. Çoğaltma ilkesini oluşturduktan sonra, hedef kapsayıcıya yazma işlemlerine izin verilmez. Hedef kapsayıcıya yazma girişimleri hata kodu 409 (Çakışma) vererek başarısız olur. Çoğaltma kuralının yapılandırıldığı bir hedef kapsayıcıya yazmak için, bu kapsayıcı için yapılandırılmış kuralı silmeniz veya çoğaltma ilkesini kaldırmanız gerekir. Çoğaltma ilkesi etkin olduğunda hedef kapsayıcıya yönelik okuma ve silme işlemlerine izin verilir.

[BLOB katmanını ayarla](/rest/api/storageservices/set-blob-tier) işlemini arşiv katmanına taşımak için hedef kapsayıcıdaki bir blob üzerinde çağırabilirsiniz. Arşiv katmanı hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="replication-status"></a>Çoğaltma durumu

Kaynak hesaptaki bir Blobun çoğaltma durumunu kontrol edebilirsiniz. Daha fazla bilgi için bkz. [bir Blobun çoğaltma durumunu denetleme](object-replication-configure.md#check-the-replication-status-of-a-blob).

Kaynak hesaptaki bir Blobun çoğaltma durumu hata gösteriyorsa, aşağıdaki olası nedenleri araştırın:

- Hedef hesapta nesne çoğaltma ilkesinin yapılandırıldığından emin olun.
- Hedef kapsayıcının hala mevcut olduğunu doğrulayın.
- Kaynak blobu, yazma işleminin bir parçası olarak müşteri tarafından sağlanmış bir anahtarla şifrelendiyse, nesne çoğaltma başarısız olur. Müşteri tarafından sunulan anahtarlar hakkında daha fazla bilgi için bkz. [BLOB depolama için istekte bir şifreleme anahtarı sağlama](encryption-customer-provided-keys.md).

## <a name="billing"></a>Faturalandırma

Nesne çoğaltma, kaynak ve hedef hesaplara göre okuma ve yazma işlemlerinde ek maliyetler ve kaynak hesaptan hedef hesaba veri çoğaltma ve değişiklik akışını işlemek için okuma ücretleri için çıkış ücretlerine yol açar.

## <a name="next-steps"></a>Sonraki adımlar

- [Nesne çoğaltmayı yapılandırma](object-replication-configure.md)
- [Azure Blob depolamada akış desteğini değiştirme](storage-blob-change-feed.md)
- [Blob sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md)
