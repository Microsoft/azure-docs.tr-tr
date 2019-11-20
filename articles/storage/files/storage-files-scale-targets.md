---
title: Azure dosyaları ölçeklenebilirlik ve performans hedefleri | Microsoft Docs
description: Kapasite, istek hızı ve gelen ve giden bant genişliği sınırları dahil olmak üzere Azure dosyaları için ölçeklenebilirlik ve performans hedefleri hakkında bilgi edinin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 267a63eba90c74b79078a7c04c1d2d8929cf2a44
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615769"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure dosyaları ölçeklenebilirlik ve performans hedefleri

[Azure dosyaları](storage-files-introduction.md) , bulutta ENDÜSTRI standardı SMB protokolü aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Bu makalede, Azure dosyaları ve Azure Dosya Eşitleme için ölçeklenebilirlik ve performans hedefleri ele alınmaktadır.

Burada listelenen ölçeklenebilirlik ve performans hedefleri, yüksek kaliteli hedeflerdir, ancak dağıtımınızdaki diğer değişkenlerden etkilenebilir. Örneğin, bir dosya için üretilen iş, yalnızca Azure dosyaları hizmetini barındıran sunucular değil, kullanılabilir ağ bant genişliğiniz tarafından da sınırlanabilir. Azure dosyalarının ölçeklenebilirlik ve performansının gereksinimlerinizi karşılayıp karşılamadığını öğrenmek için kullanım modelinizi test etmenizi kesinlikle öneririz. Bu limitleri zaman içinde artırmayı de taahhüt ediyoruz. Lütfen aşağıdaki açıklamalarda veya [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)'ta, ne kadar yükseğine bakmak istediğinizi öğrenmek istediğiniz limitleri bize geri bildirimde bulunun.

## <a name="azure-storage-account-scale-targets"></a>Azure depolama hesabı ölçek hedefleri

Azure dosya paylaşımının üst kaynağı bir Azure Storage hesabıdır. Depolama hesabı, verileri depolamak için Azure dosyaları da dahil olmak üzere birden çok depolama hizmeti tarafından kullanılabilen Azure depolama havuzunu temsil eder. Depolama hesaplarında veri depolayan diğer hizmetler Azure Blob depolama, Azure kuyruk depolama ve Azure Tablo Depolama ' larıdır. Aşağıdaki hedefler, verileri depolama hesabında depolayan tüm depolama hizmetlerini uygular:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Diğer depolama hizmetlerinden genel amaçlı depolama hesabı kullanımı, Depolama hesabınızdaki Azure dosya paylaşımlarınızı etkiler. Örneğin, Azure Blob depolama ile en fazla depolama hesabı kapasitesine ulaştıysanız, Azure dosya paylaşımınız maksimum dosya boyutu altında olsa bile Azure dosya paylaşımınızda yeni dosyalar oluşturamazsınız.

## <a name="azure-files-scale-targets"></a>Azure dosyaları ölçek hedefleri

Azure dosyaları için göz önünde bulundurmanız gereken üç sınırlama kategorisi vardır: depolama hesapları, paylaşımlar ve dosyalar.

Örneğin: Premium dosya paylaşımlarında, tek bir paylaşım 100.000 ıOPS elde edebilir ve tek bir dosya 5.000 ıOPS 'ye kadar ölçeklendirebilir. Bu nedenle, bir paylaşımda üç dosya varsa, bu paylaşımdan alabileceğiniz maksimum ıOPS 15.000 ' dir.

### <a name="standard-storage-account-limits"></a>Standart depolama hesabı limitleri

Bu sınırlar için [Azure depolama hesabı ölçek hedefleri](#azure-storage-account-scale-targets) bölümüne bakın.

### <a name="premium-filestorage-account-limits"></a>Premium FileStorage hesap limitleri

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Depolama hesabı sınırları tüm paylaşımlar için geçerlidir. FileStorage hesaplarının en büyük değerine kadar ölçeklendirilmesi yalnızca dosya depolama hesabı başına yalnızca bir paylaşımın olması durumunda ulaşılabilir.

### <a name="file-share-and-file-scale-targets"></a>Dosya paylaşma ve dosya ölçeği hedefleri

> [!NOTE]
> 5 TiB 'den büyük standart dosya paylaşımlarında bazı sınırlamalar ve bölgesel kısıtlamalar vardır.
> Sınırlamalar, bölgesel bilgiler ve bu büyük dosya paylaşımı boyutlarını etkinleştirmeye yönelik yönergelerin listesi için, planlama kılavuzunun [daha büyük dosya paylaşımlarına](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) ekleme bölümüne bakın.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure Dosya Eşitleme ölçek hedefleri

Azure Dosya Eşitleme sınırsız kullanım hedefi ile tasarlanmıştır, ancak sınırsız kullanım her zaman mümkün değildir. Aşağıdaki tabloda Microsoft 'un test olan sınırları ve hangi hedeflerin sabit sınırları olduğunu gösterilmektedir:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure Dosya Eşitleme performans ölçümleri

Azure Dosya Eşitleme Aracısı Azure dosya paylaşımlarına bağlanan bir Windows Server makinesinde çalıştığından, etkin eşitleme performansı altyapınızdaki bir dizi etkene bağlıdır: Windows Server ve temel disk yapılandırması, ağ bant genişliği sunucu ile Azure depolama, dosya boyutu, toplam veri kümesi boyutu ve veri kümesindeki etkinlik arasında. Azure Dosya Eşitleme dosya düzeyinde çalıştığından Azure Dosya Eşitleme tabanlı bir çözümün performans özellikleri, saniye başına işlenen nesne (dosya ve dizin) sayısında daha fazla ölçülür.

Azure Dosya Eşitleme için performans iki aşamada kritik öneme sahiptir:

1. **İlk bir kerelik sağlama**: ilk sağlama performansını iyileştirmek için en iyi dağıtım ayrıntıları için [Azure dosya eşitleme ile ekleme](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) bölümüne bakın.
2. **Devam eden eşitleme**: veriler başlangıçta Azure dosya paylaşımlarında oluşturulduktan sonra Azure dosya eşitleme birden çok uç noktayı eşitlenmiş halde tutar.

Bir aşamanın her biri için dağıtımınızı planlamaya yardımcı olmak üzere, bir sistem üzerinde yapılandırmaya sahip iç test sırasında gözlemlenen sonuçlar aşağıda verilmiştir

| Sistem Yapılandırması |  |
|-|-|
| CPU | 64 MiB L3 önbellek ile 64 sanal çekirdek |
| Bellek | 128 GiB |
| Disk | Pil gücüyle RAID 10 ile SAS diskleri |
| Ağ | 1 Gbps ağ |
| İş yükü | Genel Amaçlı dosya sunucusu|

| İlk bir kerelik sağlama  |  |
|-|-|
| Nesne sayısı | 25.000.000 nesneleri |
| Veri kümesi boyutu| ~ 4,7 TiB |
| Ortalama dosya boyutu | ~ 200 KiB (en büyük dosya: 100 GiB) |
| Aktarım hızını karşıya yükle | saniyede 20 nesne |
| Ad alanı Indirme üretilen Iş * | saniyede 400 nesne |

\* Yeni bir sunucu uç noktası oluşturulduğunda, Azure Dosya Eşitleme Aracısı dosya içeriğini indirmez. Önce tam ad alanını eşitler ve sonra, dosyaları tamamen veya bulut katmanlaması etkinse, sunucu uç noktasında ayarlanan bulut katmanlaması ilkesi için arka plan geri yüklemeyi tetikler.

| Devam eden eşitleme  |   |
|-|--|
| Eşitlenen nesne sayısı| 125.000 nesneleri (%1 karmaşıklık) |
| Veri kümesi boyutu| 50 GiB |
| Ortalama dosya boyutu | ~ 500 KiB |
| Aktarım hızını karşıya yükle | saniyede 20 nesne |
| Tam Indirme performansı * | saniyede 60 nesne |

\* Bulut katmanlaması etkinse, bazı dosya verilerinin yalnızca bir kısmı indirildiğinden daha iyi performans gözlemleyebilirsiniz. Azure Dosya Eşitleme yalnızca, uç noktaların hiçbirinde değiştirildiklerinde önbelleğe alınmış dosyaların verilerini indirir. Katmanlı veya yeni oluşturulan dosyalar için, aracı dosya verilerini indirmez ve bunun yerine yalnızca ad alanını tüm sunucu uç noktalarına eşitler. Aracı Ayrıca katmanlı dosyaların kullanıcı tarafından erişilen kısmi karşıdan yüklenmesini de destekler. 

> [!Note]  
> Yukarıdaki numaralar, deneyiminizle ilgili olarak bir performans göstergesi değildir. Gerçek performans, bu bölümün başlangıcında özetlenen şekilde, birden çok etkene bağlıdır.

Dağıtımınız için genel bir kılavuz olarak birkaç şeyi göz önünde bulundurmanız gerekir:

- Nesne aktarım hızı, sunucudaki eşitleme gruplarının sayısıyla orantılı olarak ölçeklendirilir. Sunucuda birden çok eşitleme grubuna veri bölmek, sunucu ve ağ tarafından da sınırlı olan daha iyi aktarım hızı verir.
- Nesne aktarım hızı, saniye başına işlem için MIB ile orantılı şekilde orantılıdır. Daha küçük dosyalar için, saniye başına işlenen nesne sayısı açısından daha yüksek aktarım hızı, saniye başına ise alt MIB ile karşılaşırsınız. Buna karşılık, daha büyük dosyalar için, saniye başına işlenen daha az sayıda nesne ve saniye başına daha fazla MIB alacaksınız. Saniyedeki MIB, Azure dosyaları ölçek hedefleri ile sınırlıdır.

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Dosyaları dağıtımını planlama](storage-files-planning.md)
- [Azure Dosya Eşitleme dağıtımı planlama](storage-sync-files-planning.md)
- [Diğer depolama hizmetleri için ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md)
