---
title: Azure Izleyici günlük sorgularının işlevleri
description: Bu makalede, Azure Izleyici 'de başka bir günlük sorgusundan bir sorgu çağırmak için işlevlerinin nasıl kullanılacağı açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752947"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularının işlevleri
Bir işlev, Azure Izleyici 'de, bir komut gibi diğer günlük sorgularında kullanılabilecek bir günlük sorgusudur. İşlevler, geliştiricilerin farklı müşterilere çözümler sağlamasına ve sorgu mantığını kendi ortamınızda yeniden kullanmanıza olanak tanır. Bu makalede, işlevlerinin nasıl kullanılacağı ve kendi oluşturabileceğiniz ayrıntılar verilmektedir.

## <a name="types-of-functions"></a>İşlev türleri
Azure Izleyici 'de iki tür işlev vardır:

- **Çözüm işlevi:** Azure Izleyici ile birlikte sunulan önceden oluşturulmuş işlevler. Bunlar tüm Log Analytics çalışma alanlarında kullanılabilir ve değiştirilemez.
- **Çalışma alanı işlevleri:** Belirli bir Log Analytics çalışma alanına yüklenen ve Kullanıcı tarafından değiştirilebilen ve denetlenebileceği işlevler.

## <a name="viewing-functions"></a>İşlevleri görüntüleme
Geçerli çalışma alanındaki çözüm işlevlerini ve çalışma alanı işlevlerini Log Analytics çalışma alanının sol bölmesindeki **işlevler** sekmesinden görüntüleyebilirsiniz. Gruplandırma **ölçütü** değiştirmek için, listede bulunan işlevleri filtrelemek için **filtre** düğmesini kullanın. Belirli bir işlevi bulmak için **arama** kutusuna bir dize yazın. Açıklama ve parametreler dahil onunla ilgili ayrıntıları görüntülemek için bir işlevin üzerine gelin.

:::image type="content" source="media/functions/view-functions.png" alt-text="İşlevi görüntüle" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Bir işlev kullanın
Bir komutta bir işlevi, bir komuta yazdığınız gibi parametre değerlerini içeren adını yazarak bir sorgu içinde kullanın. İşlevin çıktısı, sonuçlar olarak ya da başka bir komuta yöneltilen şekilde döndürülebilir.

Adına çift tıklayarak veya üzerine gelerek ve **düzenleyicide kullan**' ı seçerek geçerli sorguya bir işlev ekleyin. Bir sorgu yazarken, çalışma alanındaki işlevler IntelliSense 'e de dahil edilir. 

Bir sorgu için parametreler gerekiyorsa, şu sözdizimini kullanarak bunları sağlayın: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Bir işlev kullanın" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>İşlev oluşturma
Düzenleyicideki geçerli sorgudan bir işlev oluşturmak için **Kaydet** ' i ve ardından **farklı kaydet**' i seçin. 

:::image type="content" source="media/functions/function-save.png" alt-text="İşlev oluşturma" lightbox="media/functions/function-save.png":::

**Kaydet** ' e tıklayarak ve ardından aşağıdaki tablodaki bilgileri sağlayarak Azure Portal Log Analytics bir işlev oluşturun.

| Ayar | Açıklama |
|:---|:---|
| İşlev Adı  | İşlevin adı. Bu, bir boşluk veya özel karakter içeremez. Bu karakter çözüm işlevleri için ayrıldığından da bir alt çizgi (_) ile başlayamayabilir. |
| Eski kategori | Filtre ve grup işlevlerine yardım eden Kullanıcı tanımlı kategori.   |
| Bilgisayar grubu olarak kaydet | Sorguyu bir [bilgisayar grubu](computer-groups.md)olarak kaydedin.  |
| Parametreler | İşlevinde, kullanıldığında bir değer gerektiren her değişken için bir parametre ekleyin. Ayrıntılar için bkz. [işlev parametreleri](#function-parameters) . |

:::image type="content" source="media/functions/function-details.png" alt-text="İşlev ayrıntıları" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>İşlev parametreleri 
Bir işleve parametreler ekleyerek belirli değişkenler için değer sağlayabilmenizi sağlayabilirsiniz. Bu, her biri parametreler için farklı değerler sağlayan farklı sorgularda aynı işlevin kullanılmasına izin verir. Parametreleri aşağıdaki özellikler tarafından tanımlanır.

| Ayar | Açıklama |
|:---|:---|
| Tür  | Değer için veri türü. |
| Name  | Parametrenin adı. Bu, parametre değeri ile değiştirmek için sorgusunda kullanılması gereken addır.  |
| Varsayılan değer | Değer sağlanmazsa parametre için kullanılacak değer. |

Parametreler, varsayılan değere sahip olanların önünde hiçbir varsayılan değere sahip olmayan herhangi bir parametre ile oluşturulduklarında sıralanır.

## <a name="working-with-function-code"></a>İşlev koduyla çalışma
Bir işlev kodunu, nasıl çalıştığına ilişkin Öngörüler elde etmek veya bir çalışma alanı işlevinin kodunu değiştirmek için görüntüleyebilirsiniz. İşlev kodunu düzenleyicideki geçerli sorguya eklemek için **işlev kodunu yükle** ' yi seçin. Boş bir sorguya veya varolan bir sorgunun ilk satırına eklerseniz, bu durumda işlev adı sekmeye eklenir. Bu bir çalışma alanı işleviniz ise, işlev ayrıntılarını düzenleme seçeneğini sağlar.

:::image type="content" source="media/functions/function-code.png" alt-text="İşlev kodunu yükle" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Bir işlevi düzenleme
Yeni bir sorgu oluşturarak işlevin özelliklerini veya kodunu düzenleyin, sonra işlevin adının üzerine gelin ve **işlev kodunu yükle**' yi seçin. Koda istediğiniz değişiklikleri yapın ve **Kaydet** ' i ve ardından **işlev ayrıntılarını Düzenle**' yi seçin. **Kaydet**' e tıklamadan önce işlevin özelliklerine ve parametrelerine istediğiniz değişiklikleri yapın.

:::image type="content" source="media/functions/function-edit.png" alt-text="İşlevi Düzenle" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Örnek
Aşağıdaki örnek işlev, belirli bir tarihten bu yana ve belirli bir kategoriyle eşleşen Azure etkinlik günlüğündeki tüm olayları döndürür. 

Sabit kodlanmış değerleri kullanarak aşağıdaki sorguyla başlayın. Bu, sorgunun beklendiği gibi çalıştığını doğrular.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Örnek işlev-ilk sorgu" lightbox="media/functions/example-query.png":::

Ardından, sabit kodlanmış değerleri parametre adlarıyla değiştirin ve ardından **Kaydet** ' i ve ardından **farklı kaydet**' i seçerek işlevi kaydedin.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Örnek işlev-Save işlevi" lightbox="media/functions/example-save-function.png":::

 İşlev özellikleri için aşağıdaki değerleri sağlayın.

| Özellik | Değer |
|:---|:---|
| İşlev adı | AzureActivityByCategory |
| Eski kategori | Demo işlevleri |

İşlevi kaydetmeden önce aşağıdaki parametreleri tanımlayın.

| Tür | Name | Varsayılan değer |
|:---|:---|:---|
| string   | CategoryParam | Yönet |
| datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Örnek işlev-işlev özellikleri" lightbox="media/functions/example-function-properties.png":::

Yeni bir sorgu oluşturun ve yeni işlevi üzerine gelerek görüntüleyin. İşlevi kullandığınızda parametrelerin sırasını göz önünde bir şekilde belirtmeleri gerekir.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Örnek işlev-ayrıntıları görüntüle" lightbox="media/functions/example-view-details.png":::

Yeni işlevi bir sorguya eklemek ve ardından parametreler için değerler eklemek için **düzenleyicide kullan** ' ı seçin. Varsayılan bir değere sahip olduğundan, CategoryParam için bir değer belirtmeniz gerekmediğini unutmayın.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Örnek işlev-Use işlevi" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Sonraki adımlar
Bkz. Azure Izleyici günlük sorgularını yazmak için diğer dersler:

- [Dize işlemleri](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

