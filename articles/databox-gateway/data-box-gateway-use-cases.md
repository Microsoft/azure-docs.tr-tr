---
title: Microsoft Azure Data Box Gateway kullanım örnekleri | Microsoft Docs
description: Azure 'a veri aktarmanıza imkan tanıyan bir Sanal Gereç depolama çözümü olan Azure Data Box Gateway için kullanım örneklerini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786621"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway için kullanım örnekleri

Azure Data Box Gateway, şirket içinde bulunan ve görüntünüzü, medyayı ve diğer verilerinizi Azure 'a gönderen bir bulut depolama ağ geçidi aygıtıdır. Bu bulut depolama ağ geçidi, hiper yöneticiniz üzerinde sağlanan bir sanal makinedir. Bu sanal cihaza, daha sonra Azure 'a gönderdiği NFS ve SMB protokollerini kullanarak veri yazarsınız. Bu makalede, bu cihazı dağıtabileceğiniz senaryoların ayrıntılı bir açıklaması sağlanmaktadır.

Aşağıdaki senaryolar için Data Box Gateway kullanın:

- Büyük miktarlarda verileri sürekli alma.
- Güvenli ve etkili bir şekilde veri bulutu arşivi için.
- Data Box kullanılarak ilk toplu aktarım yapıldıktan sonra ağ üzerinden artımlı veri aktarımı için.

Bu senaryoların her biri, sonraki bölümlerde ayrıntılı olarak açıklanmıştır.


## <a name="continuous-data-ingestion"></a>Sürekli veri alımı

Data Box Gateway başlıca avantajlarından biri, veri boyutundan bağımsız olarak buluta kopyalamak için verileri cihaza sürekli olarak alma olanağıdır.

Veri ağ geçidi cihazına yazıldığı için cihaz, verileri Azure depolama 'ya yükler. Bu cihaz, belirli bir eşiğe ulaştığında meta verileri korurken, dosyaları yerel olarak kaldırarak depolamayı otomatik olarak yönetir. Meta verilerin yerel bir kopyasının tutulması, ağ geçidi cihazının yalnızca dosya güncelleştirilirken değişiklikleri karşıya yüklemesine olanak sağlar. Ağ geçidinize yüklenen veriler, [veri yükleme uyarıları](data-box-gateway-limits.md#data-upload-caveats)'ndaki yönergelere göre olmalıdır.

Cihaz verilerle doldurulduğunda, verilerin buluta yüklenme oranıyla eşleşecek şekilde giriş hızını azaltmayı (gerektiğinde) başlatır. Cihazdaki sürekli alımı izlemek için uyarıları kullanırsınız. Bu uyarılar, daraltma başladıktan sonra tetiklenir ve daraltma durdurulduktan sonra temizlenir.

## <a name="cloud-archival-of-data"></a>Veri bulutu Arşivi

Verilerinizi bulutta uzun süreli olarak sürdürmek istediğinizde Data Box Gateway kullanın. Depolamanın arşiv katmanını uzun süreli saklama için kullanabilirsiniz.

Arşiv katmanı, nadiren erişilen verileri en az 180 gün boyunca depolamak için iyileştirilmiştir. Arşiv katmanı en düşük depolama maliyetlerini sunar, ancak en yüksek erişim maliyetlerine sahiptir. Daha fazla bilgi için [Arşiv erişim katmanı](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)' na gidin.

### <a name="move-data-to-the-archive-tier"></a>Verileri arşiv katmanına taşıma

Başlamadan önce, çalışan bir Data Box Gateway cihazınıza sahip olduğunuzdan emin olun. Eğitim bölümünde açıklanan adımları izleyin. [Azure Data Box Gateway dağıtmaya hazırlanın](data-box-gateway-deploy-prep.md) ve bir işlem cihazına sahip olana kadar sonraki öğreticiye ilerletmeden devam edin.

- Verileri [Data Box Gateway aracılığıyla aktarma](data-box-gateway-deploy-add-shares.md)bölümünde açıklandığı gibi olağan aktarım yordamı aracılığıyla Azure 'a veri yüklemek için Data Box Gateway cihazını kullanın.
- Veriler karşıya yüklendikten sonra arşiv katmanına taşımanız gerekir. Blob katmanını iki şekilde ayarlayabilirsiniz: bir Azure PowerShell betiği veya Azure Storage yaşam döngüsü yönetim ilkesi kullanarak.  
    - Azure PowerShell kullanıyorsanız, verileri arşiv katmanına taşımak için aşağıdaki [adımları](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) izleyin.
    - Azure yaşam döngüsü yönetimi kullanıyorsanız, verileri arşiv katmanına taşımak için aşağıdaki adımları izleyin.
        - Arşiv katmanını kullanmak için blob yaşam döngüsü yönetim hizmetinin önizlemesine [kaydolun](../storage/blobs/storage-lifecycle-management-concepts.md) .
        - Alma [sırasında verileri arşivlemek](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest)için aşağıdaki ilkeyi kullanın.
- Blob 'lar arşiv olarak işaretlendikten sonra, sık veya soğuk katmana taşınmadığı sürece, bunlar ağ geçidi tarafından artık değiştirilemez. Dosya yerel depodadır, yerel kopyada yapılan değişiklikler (silme dahil) arşiv katmanına yüklenmeyecektir.
- Arşiv depolamadaki verileri okumak için blob katmanını sık erişimli veya seyrek erişimli olarak değiştirerek verileri yeniden yazmanız gerekir. Ağ geçidinde [paylaşımın yenilenmesi](data-box-gateway-manage-shares.md#refresh-shares) blob 'u yeniden göstermez.

Daha fazla bilgi için [Azure Blob depolama yaşam döngüsünü yönetme](../storage/blobs/storage-lifecycle-management-concepts.md)hakkında daha fazla bilgi edinin.

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>İlk toplu aktarım ve ardından artımlı aktarım

Büyük miktarda verileri toplu karşıya yüklemeyi ve ardından artımlı aktarımları yapmak istediğinizde Data Box ve Data Box Gateway birlikte kullanın. Bir çevrimdışı modda (ilk çekirdek) ve ağ üzerinden artımlı aktarımlar (devam eden akış) için Data Box Gateway toplu aktarım için Data Box kullanın.

### <a name="seed-the-data-with-data-box"></a>Data Box ile verileri tohum

Data Box verileri kopyalamak ve Azure depolama alanına yüklemek için bu adımları izleyin.

1. [Data Box sıralayın](../databox/data-box-deploy-ordered.md).
2. [Data Box ayarlayın](../databox/data-box-deploy-set-up.md).
3. [SMB aracılığıyla Data Box verileri kopyalayın](../databox/data-box-deploy-copy-data.md).
4. [Data Box döndürün, verilerin Azure 'a yüklenmesini doğrulayın](../databox/data-box-deploy-picked-up.md).
5. Verileri Azure 'a yükleme işlemi tamamlandıktan sonra tüm veriler Azure depolama kapsayıcıları 'nda olmalıdır. Data Box için depolama hesabında, tüm verilerin kopyalandığından emin olmak için blob (ve dosya) kapsayıcısına gidin. Bu adı daha sonra kullanacağınız için kapsayıcı adı ' nı bir yere getirin. Örneğin, aşağıdaki ekran görüntüsünde, `databox` kapsayıcı artımlı aktarım için kullanılacaktır.

    ![Data Box veri içeren kapsayıcı](media/data-box-gateway-use-cases/data-container.png)

Bu toplu aktarım, ilk dengeli dağıtım aşamasını tamamlar.

### <a name="ongoing-feed-with-data-box-gateway"></a>Data Box Gateway ile devam eden akış

Data Box Gateway ile ilgili devam eden adımları izleyin. 

1. Data Box Gateway üzerinde bir bulut paylaşma oluşturun. Bu paylaşımın tüm verileri Azure depolama hesabına otomatik olarak yükler. Data Box Gateway kaynağınızın **Paylaşımlar** bölümüne gidin ve **+ Paylaşım Ekle**' ye tıklayın.

    ![+ Paylaşma Ekle ' ye tıklayın.](media/data-box-gateway-use-cases/add-share.png)

2. Bu paylaşımın, sağlanan verileri içeren kapsayıcınıza eşlendiğinden emin olun. **BLOB kapsayıcısı Seç** Için **Varolanı kullan** ' ı seçin ve Data Box verilerin aktarıldığı kapsayıcıya gidin.

    ![Ayarları paylaşma](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Paylaşma oluşturulduktan sonra, paylaşma 'yı yenileyin. Bu işlem şirket içi paylaşma işlemini Azure 'daki içerikle yeniler.

    ![Paylaşma Yenile](media/data-box-gateway-use-cases/refresh-share.png)

    Paylaşma eşitlendiğinde, dosyalar istemcide değiştirilmişse, artımlı değişiklikleri karşıya yükler Data Box Gateway.

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box Gateway sistem gereksinimlerini](data-box-gateway-system-requirements.md) gözden geçirin.
- [Data Box Gateway sınırlarını](data-box-gateway-limits.md) anlayın.
- Azure portalında [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)’i dağıtın.