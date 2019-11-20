---
title: Microsoft Azure Data Box Gateway'e genel bakış | Microsoft Docs
description: Azure’a veri aktarabilmenizi sağlayan bir sanal gereç depolama çözümü olan Azure Data Box Gateway'i açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b8c6b4085f56bc12b67bf87177ba33b82c6a6db9
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900581"
---
# <a name="what-is-azure-data-box-gateway"></a>Azure Data Box Gateway nedir?

Azure Data Box Gateway, Azure rahatça veri göndermenize olanak tanıyan bir depolama çözümüdür. Bu makalede Azure Data Box Gateway çözümüne, avantajlarına, önemli özelliklerine ve bu cihazı dağıtabileceğiniz senaryolara genel bir bakış sağlanır.

Data Box Gateway, sanallaştırılmış ortamınızda veya hiper yöneticinizde sağlanan sanal makineye dayalı bir sanal cihazdır. Sanal cihaz sizde durur ve NFS ile SMB protokollerini kullanarak buna verileri yazarsınız. Ardından cihaz verilerinizi Azure blok blobuna veya Azure Dosyaları'na aktarır.

## <a name="use-cases"></a>Uygulama alanları

Bulutta arşivleme, olağanüstü durum kurtarma gibi durumlarda veya verilerinizin bulut ölçeğinde işlenmesi gerektiğinde, verileri buluta aktarmak için Data Box Gateway'den yararlanılabilir. Burada Data Box Gateway'in veri aktarımı için kullanılabileceği çeşitli senaryoları bulabilirsiniz.

- **Bulutta arşivleme** - Data Box Gateway'i kullanarak güvenli ve verimli bir yöntemle yüzlerce terabaytlık veriyi Azure depolamaya kopyalayın. Arşivleme senaryoları için veriler tek seferlik veya sürekli olarak alınabilir.

- **Sürekli veri** alımı-veri boyutundan bağımsız olarak buluta kopyalamak için cihaza verileri sürekli alma. Veri ağ geçidi cihazına yazıldığı için cihaz, verileri Azure depolama 'ya yükler.  

- **İlk toplu aktarım sonrasında artımlı aktarım** , bir çevrimdışı modda (ilk çekirdek) toplu aktarım için Data Box kullanın ve ağ üzerinden artımlı aktarımlar (devam eden akış) için Data Box Gateway.

Daha fazla bilgi için [Azure Data Box Gateway kullanım örneklerine](data-box-gateway-use-cases.md)gidin.

## <a name="benefits"></a>Avantajlar

Data Box Gateway'in şöyle avantajları vardır:

- **Kolay veri aktarımı**- Verileri Azure depolamasında içeri ve dışarı taşımayı, yerel ağ paylaşımıyla çalışma kadar kolay hale getirir.  
- **Yüksek performanslı** - Azure'a ve Azure'dan yüksek performanslı aktarımlarla ağda veri taşıma yükünü ortadan kaldırır.
- **İş saatlerinde hızlı erişim ve yüksek veri alma fiyatları** -Data Box Gateway, sanal cihaz sağlandığında yerel kapasite boyutu olarak tanımladığınız yerel bir önbelleğe sahiptir. Veri diski boyutu, [sanal cihaz en düşük gereksinimlerine](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)göre belirtilmelidir. Yerel önbellek aşağıdaki avantajları sağlar:
    - Yerel önbellek, yüksek bir hızda veri alımı sağlar. Yoğun iş saatlerinde yüksek miktarda veri yapıldığında, önbellek verileri tutabilir ve buluta yükleyebilir.
    - Yerel önbellek, belirli bir eşiğe kadar hızlı okuma erişimine izin verir. Cihaz% 50-60 dolu olana kadar cihazdan tüm okuma işlemlerini daha hızlı hale getiren önbellekten erişilir. Cihazdaki kullanılan alan bu eşiğin üzerine gittiğinde, cihaz yerel dosyaları kaldırmaya başlar.
 
- **Sınırlı bant genişliği kullanımı** - Yoğun iş saatlerinde kullanımı sınırlandırmak amacıyla ağ kısıtlandığında bile veriler Azure'a yazılabilir.  

## <a name="key-capabilities"></a>Temel işlevler

Data Box Gateway'in şöyle özellikleri vardır:

|Özellik |Açıklama  |
|---------|---------|
|Hız     | Tümüyle otomatik ve son derece iyileştirilmiş veri aktarımı ve bant genişliği.|
|Desteklenen protokoller     | Veri alımında standart SMB ve NFS protokolleri için destek. <br> Desteklenen sürümler hakkında daha fazla bilgi için bkz. [Data Box Gateway sistem gereksinimleri](data-box-gateway-system-requirements.md).|
|Veri erişimi     | Cihaz tarafından gönderilen veriler bulutta olduğunda, bulut API 'Lerine doğrudan erişerek daha fazla değişiklik yapılabilir.|
|Hızlı erişim     | En son kullanılan dosyalara hızlı erişim için cihazda yerel önbellek.|
|Çevrimdışı karşıya yükleme     | Bağlantısız mod, çevrimdışı karşıya yükleme senaryolarını destekler.|
|Veri yenileme     | Yerel dosyaları buluttaki en son sürümle yenileme olanağı.|
|Dayanıklılık     | Yerleşik ağ dayanıklılığı        |


## <a name="specifications"></a>Belirtimler

Data Box Gateway sanal cihazının belirtimleri şöyledir:

| Belirtimler                                          | Açıklama              |
|---------------------------------------------------------|--------------------------|
| Sanal işlemciler (çekirdekler)   | En az 4 |
| Bellek  |En az 8 GB|
| Kullanılabilirlik|Tek düğüm|
| Diskler|İşletim sistemi diski: 250 GB <br> Veri diski: 2 TB en düşük, ölçülü kaynak ve SSD 'Ler tarafından desteklenen olmalıdır|
| Ağ arabirimleri |1 veya daha çok sanal ağ arabirimi|
| Yerel dosya paylaşımı protokolleri|SMB ve NFS  |
| Güvenlik|Cihaza ve verilere erişimin kilidini açmak için kimlik doğrulaması <br> Kullanım halindeki veriler AES-256 bit şifrelemesi kullanılarak şifrelenir|
| Yönetim|Yerel web kullanıcı arabirimi - Cihazın ilk kurulumu, tanılaması ve güç yönetimi <br> Azure portalı - Data Box Gateway cihazlarının gündelik yönetimi       |

## <a name="components"></a>Bileşenler

Data Box Gateway çözümü Data Box Gateway kaynağından, Data Box Gateway sanal cihazından ve yerel bir web kullanıcı arabiriminden oluşur.

- **Data Box Gateway sanal cihazı** - Sanallaştırılmış ortamınızda veya hiper yöneticinizde sağlanan sanal makineye dayalı olan ve Azure'a veri göndermenizi sağlayan cihaz.
    
- **Data Box Gateway kaynağı** – Azure portalında, farklı coğrafi konumlardan erişebildiğiniz bir web arabiriminde Data Box Gateway cihazını yönetmenize olanak tanıyan bir kaynak. Cihaz, paylaşım, Kullanıcı ve uyarıları görüntülemek ve yönetmek için Data Box Gateway kaynağını kullanın. Daha fazla bilgi için bkz. [Azure Portal kullanarak yönetme](data-box-gateway-manage-shares.md).

- **Yerel Web Kullanıcı arabirimini Data Box** -tanılamayı çalıştırmak için yerel Web Kullanıcı arabirimini kullanın, cihazı kapatın ve yeniden başlatın, bir destek paketi oluşturun veya bir hizmet isteği dosyası için Microsoft desteği başvurun. Daha fazla bilgi için bkz. [Yerel Web Kullanıcı arabirimini kullanarak yönetme](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Bölge kullanılabilirliği

Verilerin aktarılacağı fiziksel cihaz, Azure kaynağı ve hedef depolama hesabının tümünün aynı bölgede olması gerekmez. Data Box Gateway

- **Kaynak kullanılabilirliği** -Data Box Edge kaynağın kullanılabildiği tüm bölgelerin listesi için, [bölgeye göre kullanılabilir Azure ürünlerine](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox)gidin. Data Box Gateway, Azure Kamu Bulutu 'nda da dağıtılabilir. Daha fazla bilgi için bkz. [Azure Kamu nedir?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, tüm Azure bölgelerinde sağlanır.

    En iyi performansı elde etmek için, depolama hesaplarının Data Box verilerini depoladığı bölgeler cihazın bulunduğu yere yakın konumlandırılmalıdır. Cihazdan uzağa konumlandırılan depolama hesabı uzun gecikme sürelerine ve daha yavaş bir performansa yol açar.


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box Gateway sistem gereksinimlerini](data-box-gateway-system-requirements.md) gözden geçirin.
- [Data Box Gateway sınırlarını](data-box-gateway-limits.md) anlayın.
- Azure portalında [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)’i dağıtın.

