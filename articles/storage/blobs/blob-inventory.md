---
title: Blob verilerini yönetmek için Azure depolama envanterini kullanma (Önizleme)
description: Azure depolama envanteri, bir depolama hesabındaki tüm blob verilerinize genel bir bakış almaya yardımcı olan bir araçtır.
services: storage
author: twooley
ms.service: storage
ms.date: 04/01/2021
ms.topic: conceptual
ms.author: twooley
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 33d50d1a6b5e84d178b522851795bcc42f5fc169
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277275"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Blob verilerini yönetmek için Azure Storage blob envanterini kullanma (Önizleme)

Azure Depolama Blobu envanter özelliği, bir depolama hesabındaki blob verilerinize genel bir bakış sağlar. Toplam veri boyutu, yaşı, şifreleme durumu vb. anlamak için envanter raporunu kullanın. Rapor, iş ve uyumluluk gereksinimleri için verilerinize genel bir bakış sağlar. Etkinleştirildikten sonra, bir envanter raporu otomatik olarak günlük olarak oluşturulur.

## <a name="availability"></a>Kullanılabilirlik

Blob envanteri hem genel amaçlı sürüm 2 (GPv2) hem de Premium Blok Blob depolama hesapları için desteklenir. Bu özellik [hiyerarşik ad alanı](data-lake-storage-namespace.md) özelliği etkin olan veya olmadan desteklenir.

> [!IMPORTANT]
> Blob envanteri şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

### <a name="preview-regions"></a>Önizleme bölgeleri

Blob envanter önizlemesi, depolama hesaplarında aşağıdaki bölgelerde kullanılabilir:

- Orta Fransa
- Orta Kanada
- Doğu Kanada
- Doğu ABD
- Doğu ABD 2
- West Europe

### <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Envanter raporlarının ücreti, önizleme dönemi boyunca ücretlendirilmez. Fiyatlandırma, bu özellik genel kullanıma sunulduğunda belirlenir.

## <a name="enable-inventory-reports"></a>Envanter raporlarını etkinleştir

Depolama hesabınıza bir ilke ekleyerek blob envanter raporlarını etkinleştirin. [Azure Portal](https://portal.azure.com/)kullanarak ilke ekleyin, düzenleyin veya kaldırın.

1. [Azure Portal](https://portal.azure.com/) gidin
1. Depolama hesaplarınızdan birini seçin
1. **BLOB hizmeti** altında **BLOB stoğu** ' nu seçin.
1. **BLOB envanterinin etkinleştirildiğinden** emin olun
1. **Kural Ekle** ' yi seçin
1. Yeni kuralınızı adlandırın
1. Envanter raporunuzun **BLOB türlerini** seçin
1. Envanter raporunuzu filtrelemek için bir önek eşleşmesi ekleyin
1. **BLOB sürümlerinin** eklenip eklenmeyeceğini ve envanter raporunuza **anlık görüntü ekleme** seçeneğini belirleyin. İlgili seçeneği etkinken yeni bir kural kaydetmek için hesapta sürümler ve anlık görüntülerin etkinleştirilmesi gerekir.
1. **Kaydet**’i seçin

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Azure portal kullanarak blob envanteri kuralının nasıl ekleneceğini gösteren ekran görüntüsü":::

Envanter ilkeleri okundu veya tam olarak yazılmıştır. Kısmi güncelleştirmeler desteklenmiyor.

> [!IMPORTANT]
> Depolama hesabınızda güvenlik duvarı kurallarını etkinleştirirseniz envanter istekleri engellenebilir. Güvenilen Microsoft hizmetleri için özel durumlar sağlayarak bu isteklerin engellemesini kaldırabilirsiniz. Daha fazla bilgi için bkz. [güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md#exceptions)Içindeki özel durumlar bölümü.

Blob stoku çalıştırması her gün otomatik olarak zamanlanır. Bir envanter çalıştırmasının tamamlanması 24 saate kadar sürebilir. Bir envanter raporu, bir veya daha fazla kuralla bir envanter ilkesi eklenerek yapılandırılır.

## <a name="inventory-policy"></a>Envanter ilkesi

Envanter ilkesi, bir JSON belgesindeki kuralların koleksiyonudur.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Azure portal **BLOB stoğu** bölümünde **kod görünümü** sekmesini seçerek bir envanter ilkesinin JSON 'unu görüntüleyin.

| Parametre adı | Parametre türü        | Notlar | Gerekli mi? |
|----------------|-----------------------|-------|-----------|
| hedef    | Dize                | Tüm envanter dosyalarının üretilebileceği hedef kapsayıcı. Hedef kapsayıcının zaten mevcut olması gerekir. | Yes |
| enabled        | Boole               | Tüm ilkeyi devre dışı bırakmak için kullanılır. **True** olarak ayarlandığında, kural düzeyi etkinleştirilmiş alan bu parametreyi geçersiz kılar. Devre dışı bırakıldığında, tüm kurallar için stok devre dışı bırakılır. | Yes |
| rules          | Kural nesneleri dizisi | İlkede en az bir kural gereklidir. En fazla 10 kural desteklenir. | Yes |

## <a name="inventory-rules"></a>Envanter kuralları

Bir kural, bir envanter raporu oluşturmak için filtreleme koşullarını ve çıkış parametrelerini yakalar. Her kural bir envanter raporu oluşturur. Kuralların çakışan önekleri olabilir. Blob, kural tanımlarına bağlı olarak birden fazla envanterde görünebilir.

İlke içindeki her bir kural birkaç parametreye sahiptir:

| Parametre adı | Parametre türü                 | Notlar | Gerekli mi? |
|----------------|--------------------------------|-------|-----------|
| name           | Dize                         | Bir kural adı en fazla 256 büyük harfe duyarlı alfasayısal karakter içerebilir. Ad, ilke içinde benzersiz olmalıdır. | Yes |
| enabled        | Boole                        | Kuralın etkinleştirilmesini veya devre dışı bırakılacağını veren bir bayrak. Varsayılan değer **true**'dur. | Yes |
| tanım     | JSON envanter kuralı tanımı | Her tanım bir kural filtresi kümesinden oluşur. | Yes |

Genel **BLOB stoğu etkin** bayrağı, bir kuraldaki *etkin* parametreye göre önceliklidir.

### <a name="rule-filters"></a>Kural filtreleri

Blob envanter raporunu özelleştirmek için çeşitli filtreler mevcuttur:

| Filtre adı         | Filtre türü                     | Notlar | Gerekli mi? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Öntanımlı Enum değerlerinin dizisi | Geçerli değerler `blockBlob` ve `appendBlob` hiyerarşik ad alanı etkin hesaplar için ve `blockBlob` , `appendBlob` ve `pageBlob` diğer hesaplar için. | Yes |
| prefixMatch         | Öneklerin eşleşmesi için 10 ' a kadar dize dizisi. Ön ek bir kapsayıcı adıyla başlamalıdır, örneğin "kapsayıcı1/foo" | *PrefixMatch* tanımlayamazsınız veya boş bir ön ek sağlamazsanız, kural depolama hesabındaki tüm Bloblar için geçerlidir. | Hayır |
| ıncludesnapshots    | Boole                         | Envanterin anlık görüntüleri içerip içermediğini belirtir. Varsayılan değer **false**'dur. | Hayır |
| ıncludeblobversions | Boole                         | Envanterin blob sürümlerini içerip içermediğini belirtir. Varsayılan değer **false**'dur. | Hayır |

Azure portal **BLOB stoğu** bölümünde **kod görünümü** sekmesini seçerek envanter kuralları için JSON kurallarını görüntüleyin. Filtreler bir kural tanımı içinde belirtilmiştir.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Envanter çıkışı

Her envanter çalıştırması, belirtilen envanter hedef kapsayıcısında bir CSV biçimli dosya kümesi oluşturur. Envanter çıktısı şu yol altında oluşturulur: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` burada:

- *AccountName* , Azure Blob depolama hesabınızın adıdır
- *Envanter-hedef-kapsayıcı* , envanter ilkesinde belirttiğiniz hedef kapsayıcıdır
- *Yyyy/aa/gg/hh-aa-ss* , stokun çalıştırılmaya başladığı süredir

### <a name="inventory-files"></a>Envanter dosyaları

Her envanter çalıştırması aşağıdaki dosyaları oluşturur:

- **INVENTORY CSV dosyası**: her bir envanter kuralı için virgülle ayrılmış değerler (CSV) dosyası. Her dosya eşleşen nesneler ve bunların meta verileri içerir. Her CSV biçimli dosyanın ilk satırı her zaman şema satırıdır. Aşağıdaki görüntüde, Microsoft Excel 'de açılan bir Inventory CSV dosyası gösterilmektedir.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel 'de açılan bir Inventory CSV dosyasının ekran görüntüsü":::

- **Bildirim dosyası**: ' de çalıştırılan her kural için oluşturulan envanter dosyalarının ayrıntılarını içeren bir manifest.jsdosyası. Bildirim dosyası ayrıca Kullanıcı tarafından belirtilen kural tanımını ve bu kural için envanter yolunu da yakalar.

- **Sağlama toplamı dosyası**: dosyadaki manifest.jsiçeriğin MD5 sağlama toplamını içeren bir manifest. Checksum dosyası. Manifest. Checksum dosyası oluşturma bir envanter çalıştırmasının tamamlandığını işaretler.

## <a name="inventory-completed-event"></a>Envanter tamamlandı olayı

Envanter çalıştırması tamamlandığında bildirim almak için envanter tamamlandı olayına abone olun. Bu olay, bildirim sağlama toplamı dosyası oluşturulduğunda oluşturulur. Envanter tamamlandı olayı, stok çalıştırması çalışmaya başlamadan önce Kullanıcı hatası verdiğinde da meydana gelir. Örneğin, geçersiz bir ilke veya hedef kapsayıcı mevcut değil hatası olayı tetikler. Olay blob envanteri konusuna yayımlanır.

Örnek olay:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="known-issues"></a>Bilinen sorunlar

Bu bölümde, Azure Depolama Blobu envanter özelliğinin sınırlamaları ve bilinen sorunları açıklanmaktadır.

### <a name="inventory-job-fails-to-complete"></a>Envanter işi tamamlanamazsa

Milyonlarca blob ve hiyerarşik ad alanı etkinleştirilmiş bir hesap için stok işi 24 saat içinde tamamlanmayabilir. Bu durumda, envanter dosyası oluşturulmaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Kapsayıcı başına Blobların sayısını ve toplam boyutunu hesaplama](calculate-blob-count-size.md)
- [Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)
