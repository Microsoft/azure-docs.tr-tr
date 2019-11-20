---
title: Azure portalında bir depolama hesabı için aktarım hızı ve gecikme süresi ölçümlerini doğrulama | Microsoft Docs
description: Portalda bir depolama hesabı için aktarım hızı ve gecikme süresi metriklerinin nasıl doğrulanacağını öğrenin.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: dc0f8171e18598e4f805a03a4bc6d17de220fbe1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698985"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Bir depolama hesabı için aktarım hızı ve gecikme süresi ölçümlerini doğrulama

Bu öğretici, bir serinin dördüncü ve son kısmıdır. Önceki öğreticilerde, büyük miktarlarda rastgele verilerin Azure depolama hesabına nasıl yüklenip indirileceğini öğrendiniz. Bu öğretici, Azure portalında aktarım hızını ve gecikme süresini görüntülemek için ölçümleri nasıl kullanabileceğinizi gösterir.

Serinin dördüncü kısmında öğrenecekleriniz:

> [!div class="checklist"]
> * Azure portalındaki grafikleri yapılandırma
> * Aktarım hızı ve gecikme süresi ölçümlerini doğrulama

[Azure depolama ölçümleri](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), depolama hesabınızın performansına ve kullanılabilirliğine ilişkin birleşik bir görünüm sağlamak için Azure izleyiciyi kullanır.

## <a name="configure-metrics"></a>Ölçümleri yapılandırma

Depolama hesabınızdaki **AYARLAR** bölümünde **Ölçümler (önizleme)** seçeneğine gidin.

**ALT HİZMET** açılır listesinden Blob’u seçin.

**ÖLÇÜM** bölümünden, aşağıdaki tabloda bulunan ölçümlerden birini seçin:

Aşağıdaki ölçümler size uygulamanın gecikme süresi ve aktarım hızına dair bir fikir sunar. Portalda yapılandırdığınız ölçümler 1’er dakikalık ortalamalardır. Bir işlem bir dakikalık sürenin ortasında bittiyse, ortalama için o dakika verileri ikiye bölünür. Uygulamada, karşıya yükleme ve indirme işlemleri zamanlanmış ve size dosyaları karşıya yükleyip indirmenin gerçekte ne kadar sürdüğüne dair çıktı sağlanmıştır. Bu bilgiler, aktarım hızını tam olarak anlamak için portal ölçümleriyle birlikte kullanılabilir.

|Ölçüm|Tanım|
|---|---|
|**Başarı E2E Gecikme Süresi**|Bir depolama hizmetine yapılan başarılı isteklerin veya belirtilen API işleminin ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|
|**Başarı Sunucu Gecikme Süresi**|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez. |
|**İşlemler**|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız istekleri ve hata üreten istekleri içerir. Örnekte, blok boyutu 100 MB olarak ayarlanmıştır. Bu durumda her 100 MB’lık blok bir işlem olarak değerlendirilir.|
|**Giriş**|Giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir. |
|**Çıkış**|Çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz. |

**Saat**’in yanındaki **Son 24 saat (Otomatik)** seçeneğini belirleyin. **Zaman ayrıntı düzeyi** için **Son saat** ve **Dakika** seçeneğini belirleyin, sonra **Uygula**’ya tıklayın.

![Depolama hesabı ölçümleri](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Grafiklere birden fazla ölçüm atanmış olabilir, ancak birden fazla ölçüm atandığında boyutlara göre gruplama yeteneği devre dışı bırakılır.

## <a name="dimensions"></a>Boyutlar

Grafikleri daha ayrıntılı incelemek ve daha ayrıntılı bilgi edinmek için [Boyutlar](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) kullanılır. Farklı ölçümlerin farklı boyutları vardır. Kullanılabilir tek boyut, **API adı** boyutudur. Bu boyut, her bir ayrı API çağrısı bazında grafiğin dökümünü oluşturur. Aşağıdaki ilk görüntü, bir depolama hesabı için toplam işlemlerin örnek bir grafiğini gösterir. İkinci görüntü, API adı boyutu seçilmiş şekilde aynı grafiği gösterir. Gördüğünüz gibi her bir işlem listelenerek API adı tarafından kaç tane çağrı yapıldığına ilişkin daha fazla ayrıntı sunar.

![Depolama hesabı ölçümleri - boyut içermeyen işlemler](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Depolama hesabı ölçümleri - işlemler](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silin. Bunu yapmak için, sanal makinenin kaynak grubunu seçin ve Sil’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Serinin dördüncü kısmında, örnek çözüm için ölçümleri görüntüleme hakkında aşağıda örnekleri verilen işlemleri öğrendiniz:

> [!div class="checklist"]
> * Azure portalındaki grafikleri yapılandırma
> * Aktarım hızı ve gecikme süresi ölçümlerini doğrulama

Önceden oluşturulmuş depolama örneklerini görmek için bu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [Azure depolama betiği örnekleri](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md
