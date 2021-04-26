---
title: Azure HPC önbelleğini yönetme ve güncelleştirme
description: Azure portal veya Azure CLı kullanarak Azure HPC önbelleğini yönetme ve güncelleştirme
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862606"
---
# <a name="manage-your-cache"></a>Önbelleğinizi yönetme

Azure portal önbellekte Genel Bakış sayfasında, önbelleğiniz için proje ayrıntıları, önbellek durumu ve temel istatistikler gösterilmektedir. Ayrıca, önbelleği durdurmak veya başlatmak, önbelleği silmek, uzun süreli depolamaya verileri temizlemek ve yazılımı güncelleştirmek için denetimler de vardır.

Bu makalede ayrıca Azure CLı ile bu temel görevlerin nasıl yapılacağı açıklanır.

Genel Bakış sayfasını açmak için Azure portal ön belleği kaynağını seçin. Örneğin, **tüm kaynaklar** sayfasını yükleyin ve önbellek adına tıklayın.

![Azure HPC önbellek örneğinin genel bakış sayfasının ekran görüntüsü](media/hpc-cache-overview.png)

Sayfanın üst kısmındaki düğmeler, önbelleği yönetmenize yardımcı olabilir:

* **Başlatma** ve [**durdurma**](#stop-the-cache) -önbellek işlemini sürdürür veya askıya alır
* [**Flush**](#flush-cached-data) -değiştirilen verileri depolama hedeflerine yazar
* [**Yükseltme**](#upgrade-cache-software) -önbellek yazılımını güncelleştirir
* [**Tanılama topla**](#collect-diagnostics) -hata ayıklama bilgilerini karşıya yükler
* **Yenile** -genel bakış sayfasını yeniden yükler
* [**Sil**](#delete-the-cache) -önbelleği kalıcı olarak yok eder

Aşağıdaki seçenekler hakkında daha fazla bilgi edinin.

Önbellek yönetimi görevlerini gösteren bir [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) izlemek için aşağıdaki görüntüye tıklayın.

[![video küçük resmi: Azure HPC Cache: Yönet (video sayfasını ziyaret etmek için tıklayın)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Önbelleği durdur

Etkin olmayan bir süre boyunca maliyetleri azaltmak için önbelleğin durdurulmasını sağlayabilirsiniz. Önbellek durdurulduğunda çalışma süresi ücretsizdir, ancak önbelleğin ayrılan disk depolaması için ücretlendirilirsiniz. (Ayrıntılar için [fiyatlandırma](https://aka.ms/hpc-cache-pricing) sayfasına bakın.)

Durdurulmuş bir önbellek, istemci isteklerine yanıt vermez. Önbelleği durdurmadan önce istemcilerin bağlantısını çıkarmanız gerekir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

**Durdur** düğmesi etkin bir önbelleği askıya alır. Önbelleğin durumu **sağlıklı** veya **düşürülmüş** olduğunda **Durdur** düğmesi kullanılabilir.

![Durdur ile üst düğmelerin ve Durdur eylemini açıklayan ve ' devam etmek istiyor musunuz? ' adlı bir açılan iletinin ekran görüntüsü Evet (varsayılan) ve düğme yok](media/stop-cache.png)

Önbelleği durdurmayı onaylamak için Evet ' e tıkladıktan sonra önbellek, içeriğini depolama hedeflerine otomatik olarak temizler. Bu işlem biraz zaman alabilir, ancak veri tutarlılığını sağlar. Son olarak, önbellek durumu **durduruldu** olarak değişir.

Durdurulmuş bir önbelleği yeniden etkinleştirmek için **Başlat** düğmesine tıklayın. Onay gerekmez.

![vurgulanacak üst düğmelerin ekran görüntüsü](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

[Az HPC-Cache stop](/cli/azure/hpc-cache#az_hpc_cache_stop) komutuyla bir önbelleği geçici olarak askıya alın. Bu eylem yalnızca önbelleğin durumu **sağlıklı** veya **düşürülmüş** olduğunda geçerlidir.

Önbellek, durdurulmadan önce içeriğini depolama hedeflerine otomatik olarak temizler. Bu işlem biraz zaman alabilir, ancak veri tutarlılığını sağlar.

Eylem tamamlandığında, önbellek durumu **durduruldu** olarak değişir.

Durdurulmuş bir önbelleği [az HPC-Cache start](/cli/azure/hpc-cache#az_hpc_cache_start)ile yeniden etkinleştirin.

Başlat veya Durdur komutunu verdiğinizde, komut satırı işlem tamamlanana kadar bir "çalışıyor" durum iletisi gösterir.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Tamamlanma sırasında ileti "tamamlandı" olarak güncelleştirilir ve dönüş kodlarını ve diğer bilgileri gösterir.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Önbelleğe alınmış verileri temizleme

Genel Bakış sayfasındaki **Temizleme** düğmesi önbelleğin önbellekte depolanan tüm değiştirilen verileri arka uç depolama hedeflerine hemen yazmasını söyler. Önbellek verileri düzenli aralıklarla depolama hedeflerine kaydeder, bu nedenle arka uç depolama sisteminin güncel olduğundan emin olmak istemediğiniz takdirde bunu el ile yapmak gerekli değildir. Örneğin, bir depolama anlık görüntüsü almadan veya veri kümesi boyutunu kontrol etmeden önce **temizlemeyi** kullanabilirsiniz.

> [!NOTE]
> Temizleme işlemi sırasında önbellek, istemci isteklerine yönelik hizmeti veremez. Önbellek erişimi askıya alınır ve işlem bittikten sonra devam eder.

Önbellek temizleme işlemini başlattığınızda, önbellek istemci isteklerini kabul etmeyi durduruyor ve Genel Bakış sayfasındaki önbellek durumu **reçeteye göre** değişir.

Önbellekteki veriler uygun depolama hedeflerine kaydedilir. Ne kadar veri temizlenmesi gerektiğine bağlı olarak, işlem birkaç dakika sürebilir veya bir saat boyunca zaman alabilir.

Tüm veriler depolama hedeflerine kaydedildikten sonra, önbellek otomatik olarak istemci istekleri almaya başlar. Önbellek durumu **sağlıklı** olarak döner.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Önbelleği temizlemek için, **Temizleme** düğmesine tıklayın ve sonra eylemi doğrulamak için **Evet** ' e tıklayın.

![Flush vurgulanmış olan üst düğmelerin ve Temizleme eylemini açıklayan ve ' devam etmek istiyor musunuz? ' adlı bir açılan iletinin ekran görüntüsü Evet (varsayılan) ve düğme yok](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Önbelleğin tüm değiştirilen verileri depolama hedeflerine yazmasını zorlamak için [az HPC-Cache Flush](/cli/azure/hpc-cache#az_hpc_cache_flush) kullanın.

Örnek:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Temizleme tamamlandığında bir başarı iletisi döndürülür.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Yükseltme önbelleği yazılımı

Yeni bir yazılım sürümü varsa, **Yükselt** düğmesi etkin hale gelir. Ayrıca, sayfanın en üstünde yazılım güncelleştirme hakkında bir ileti de görmeniz gerekir.

![Yükseltme düğmesi etkin olan düğmelerin en üstteki satır görüntüsü](media/hpc-cache-upgrade-button.png)

İstemci erişimi, yazılım yükseltmesi sırasında kesintiye uğramaz, ancak önbellek performansı yavaşlar. Yoğun olmayan kullanım saatlerinde veya planlanan bir bakım döneminde yazılım yükseltmeyi planlayın.

Yazılım güncelleştirmesi birkaç saat sürebilir. Daha yüksek aktarım hızı ile yapılandırılan önbellekler, daha az yoğun üretilen iş değeri olan önbellekler daha uzun sürer

Bir yazılım yükseltmesi kullanılabilir olduğunda, bir hafta veya el ile uygulamanız gerekir. Son Tarih yükseltme iletisinde listelenir. Bu süre boyunca yükseltmezseniz Azure, güncelleştirmeyi otomatik olarak önbelleğe uygular. Otomatik yükseltmenin zamanlaması yapılandırılamaz. Önbellek performansı etkisi konusunda endişeleriniz varsa, zaman döneminin süresi dolmadan önce yazılımı kendiniz yükseltmeniz gerekir.

Bitiş tarihi geçtiğinde önbelleğiniz durdurulmuşsa, önbellek bir sonraki başlatılışında otomatik olarak yazılım yükseltir. (Güncelleştirme hemen başlatılamayabilir, ancak ilk saat içinde başlatılacaktır.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Yazılım güncelleştirmesini başlatmak için **Yükselt** düğmesine tıklayın. İşlem tamamlanana kadar önbellek durumu **yükseltme** olarak değişir.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Azure CLı 'da, önbellek durumu raporunun sonuna yeni yazılım bilgileri dahildir. (Denetlemek için [az HPC-Cache Show](/cli/azure/hpc-cache#az_hpc_cache_show) öğesini kullanın.) İletideki "upgradeStatus" dizesini arayın.

Varsa, güncelleştirmeyi uygulamak için [az HPC-Cache Upgrade-bellenim](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) kullanın.

Hiçbir güncelleştirme kullanılamıyorsa, bu işlemin bir etkisi yoktur.

Bu örnek, önbellek durumunu (güncelleştirme yok) ve Upgrade-bellenim komutunun sonuçlarını gösterir.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Tanılama toplama

**Tanılama topla** düğmesi, sistem bilgilerini toplama ve sorun giderme amacıyla Microsoft hizmetine yükleme sürecini el ile başlatır. Önemli bir önbellek sorunu oluşursa önbelleğiniz otomatik olarak aynı tanılama bilgilerini toplar ve yükler.

Microsoft hizmet ve destek tarafından istediğinde bu denetimi kullanın.

Düğmeye tıkladıktan sonra, karşıya yüklemeyi onaylamak için **Evet** ' e tıklayın.

![' tanılama koleksiyonu Başlat ' açılır onay iletisinin ekran görüntüsü. Varsayılan düğme ' Yes ' vurgulanır.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Önbelleği silme

**Sil** düğmesi önbelleği yok eder. Bir önbelleği sildiğinizde, tüm kaynakları yok edilir ve artık hesap ücretlerine tabi değildir.

Depolama hedefleri olarak kullanılan arka uç depolama birimleri, önbelleği sildiğinizde etkilenmez. Daha sonra gelecekteki bir önbelleğe ekleyebilir veya onları ayrı olarak yetkisini alabilirsiniz.

> [!NOTE]
> Azure HPC önbelleği, önbellek silinmeden önce, önbellekteki verileri otomatik olarak arka uç depolama sistemlerine yazar.
>
> Önbellekteki tüm verilerin uzun süreli depolamaya yazıldığından emin olmak için, silmeden önce [önbelleği durdurun](#stop-the-cache) . Silinmeden önce **durdurulan** durumu gösteriyor olduğundan emin olun.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Önbelleği durdurduktan sonra, önbelleği kalıcı olarak kaldırmak için **Sil** düğmesine tıklayın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Önbelleği kalıcı olarak kaldırmak için [az HPC-Cache Delete](/cli/azure/hpc-cache#az_hpc_cache_delete) Azure CLI komutunu kullanın.

Örnek:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Önbellek ölçümleri ve izleme

Genel Bakış sayfasında bazı temel önbellek istatistikleri için grafikler, önbellek aktarım hızı, saniye başına işlem ve gecikme gösterilmektedir.

![Örnek önbellek için yukarıda bahsedilen istatistikleri gösteren üç satırlık grafiklerin ekran görüntüsü](media/hpc-cache-overview-stats.png)

Bu grafikler, Azure 'un yerleşik izleme ve analiz araçlarının bir parçasıdır. Ek araçlar ve uyarılar, Portal kenar çubuğu 'ndaki **izleme** başlığı altındaki sayfalardan kullanılabilir. [Azure izleme belgelerinin](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)Portal bölümünde daha fazla bilgi edinin.

## <a name="view-warnings"></a>Uyarıları görüntüle

Önbellek sağlıksız bir duruma geçtiğinde, **Uyarılar** sayfasını kontrol edin. Bu sayfada, durumunu anlamanıza yardımcı olabilecek önbellek yazılımının bildirimleri gösterilir.

Bu bildirimler Azure portal tarafından denetlenmediğinden etkinlik günlüğünde görünmez. Bunlar genellikle yapmış olabileceğiniz özel ayarlarla ilişkilendirilir.

Burada görebileceğiniz Uyarı türleri şunlardır:

* Önbellek, NTP sunucusuna erişemiyor
* Önbellek, genişletilmiş gruplar Kullanıcı adı bilgilerini indiremedi
* Bir depolama hedefinde özel DNS ayarları değiştirildi

![Genişletilmiş gruplar Kullanıcı adlarının indirilebileceğini belirten bir ileti gösteren Izleme > uyarıları sayfasının ekran görüntüsü](media/warnings-page.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure ölçümleri ve istatistik araçları](../azure-monitor/index.yml) hakkında daha fazla bilgi edinin
* [Azure HPC önbelleğiniz ile ilgili yardım](hpc-cache-support-ticket.md) alın
