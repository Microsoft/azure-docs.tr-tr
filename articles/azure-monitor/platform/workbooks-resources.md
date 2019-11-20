---
title: Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma | Kaynak parametreleri | Microsoft docs
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2cd170fdc3bed04a81c66d83b9e75ed77e3c0a5a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165855"
---
# <a name="workbook-resource-parameters"></a>Çalışma kitabı kaynak parametreleri

Kaynak parametreleri çalışma kitaplarında kaynak seçmeye izin verir. Bu, verilerin alınacağı kapsamı ayarlamak için yararlıdır. Bir örnek, kullanıcıların daha sonra verileri sunarken kullanacağı VM 'Lerin kümesini seçmesini sağlar.

Kaynak seçicileri değerleri, çalışma kitabı bağlamından, statik listeden veya Azure Kaynak Grafiği sorgularından gelebilir.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Kaynak parametresi oluşturma (çalışma kitabı kaynakları)
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `Applications`
    2. Parametre türü: `Resource picker`
    3. Gerekli: `checked`
    4. Çoklu seçimlere izin ver: `checked`
5. Veri al: `Workbook Resources`
6. Yalnızca kaynak türlerini dahil et: `Application Insights`
7. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

![Çalışma kitabı kaynaklarını kullanarak bir kaynak parametresinin oluşturulmasını gösteren resim](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Kaynak parametresi oluşturma (Azure Kaynak Grafiği)
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `Applications`
    2. Parametre türü: `Resource picker`
    3. Gerekli: `checked`
    4. Çoklu seçimlere izin ver: `checked`
5. Veri al: `Query`
    1. Sorgu türü: `Azure Resource Graph`
    2. Abonelikler: `Use default subscriptions`
    3. Sorgu denetiminde, bu kod parçacığını ekleyin
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

![Azure Kaynak Grafiği 'ni kullanarak bir kaynak parametresinin oluşturulmasını gösteren resim](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Kaynak Grafiği henüz tüm bulutlarda kullanılamaz. Bu yaklaşımı seçerseniz hedef bulutunuzda desteklendiğinden emin olun.

[Azure Kaynak Grafiği belgeleri](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Kaynak parametresi oluşturma (JSON listesi)
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `Applications`
    2. Parametre türü: `Resource picker`
    3. Gerekli: `checked`
    4. Çoklu seçimlere izin ver: `checked`
5. Veri al: `JSON`
    1. İçerik denetiminde, bu JSON kod parçacığını ekleyin
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Mavi _güncelleştirme_ düğmesine basın.
6. İsteğe bağlı olarak `Include only resource types` _Application Insights_ olarak ayarlayın
7. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

## <a name="referencing-a-resource-parameter"></a>Kaynak parametresine başvurma
1. Çalışma kitabına bir sorgu denetimi ekleyin ve bir Application Insights kaynağı seçin.
2. Parametresini denetime bağlamak için _Application Insights_ açılır öğesini kullanın. Bunu yapmak sorgunun kapsamını, çalışma zamanında parametresi tarafından döndürülen kaynaklara ayarlar.
4. KQL denetiminde, bu kod parçacığını ekleyin
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Sonuçları görmek için sorguyu çalıştırın. 

![Sorgu denetiminde başvurulan kaynak parametresini gösteren resim](./media/workbooks-resources/resource-reference.png)

> Bu yaklaşım, kaynakları ölçümler gibi diğer denetimlere bağlamak için kullanılabilir.

## <a name="resource-parameter-options"></a>Kaynak parametre seçenekleri
| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `{Applications}` | Seçilen kaynak KIMLIĞI | _/Subscriptions/< alt kimlik >/resourceGroups/< kaynak-grubu >/Providers/< kaynak türü >/acmeauthentication_ |
| `{Applications:label}` | Seçili kaynağın etiketi | `acmefrontend` |
| `{Applications:value}` | Seçili kaynağın değeri | _'/Subscriptions/< alt kimliği >/resourceGroups/< kaynak-grubu >/Providers/< kaynak türü >/acmeauthentication '_ |
| `{Applications:name}` | Seçilen kaynağın adı | `acmefrontend` |
| `{Applications:resourceGroup}` | Seçilen kaynağın kaynak grubu | `acmegroup` |
| `{Applications:resourceType}` | Seçilen kaynağın türü | _Microsoft. Insights/bileşenler_ |
| `{Applications:subscription}` | Seçili kaynağın aboneliği |  |
| `{Applications:grid}` | Kaynak özelliklerini gösteren bir kılavuz. Hata ayıklarken bir metin bloğunda işlemek yararlı  |  |

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](workbooks-visualizations.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](workbooks-access-control.md) ve paylaşma.