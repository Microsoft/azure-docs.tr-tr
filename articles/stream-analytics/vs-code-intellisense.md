---
title: Visual Studio Code için Azure Stream Analytics araçlarında IntelliSense
description: Bu makalede, Visual Studio Code için Azure Stream Analytics araçlarında IntelliSense özelliklerinin nasıl kullanılacağı açıklanır.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491579"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Visual Studio Code için Azure Stream Analytics araçlarında IntelliSense

IntelliSense, [vs Code için Azure Stream Analytics araçlarında](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) [Stream Analytics sorgu dili](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) için kullanılabilir. IntelliSense, bir dizi özelliği içeren bir kod tamamlama yardımıdır: liste üyeleri, parametre bilgileri, hızlı bilgi ve tam sözcük. IntelliSense özellikleri bazen "kod tamamlama", "içerik Yardımcısı" ve "kod ipucu" gibi diğer adlarla çağrılır.

![IntelliSense tanıtımı](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense özellikleri

VS Code için Stream Analytics araçlarındaki IntelliSense özellikleri bir dil hizmeti tarafından desteklenir. Bir dil hizmeti, kaynak kodunuzu analiz eder ve dil semantiğine göre akıllı kod tamamlama sağlar. Bir dil hizmetinin olası tamamlamalar varsa, IntelliSense önerileri siz yazarken açılır. Yazmaya devam ederseniz, değişkenler ve yöntemler gibi üyelerin listesi yalnızca yazdığınız karakterleri içeren üyeleri içerecek şekilde filtrelenir. Veya tuşlarına bastığınızda `Tab` `Enter` , IntelliSense seçtiğiniz üyeyi ekler.

Nokta karakteri gibi bir tetikleyici karakteri yazarak herhangi bir düzenleyici penceresinde IntelliSense 'i tetikleyebilirsiniz `.` .

![IntelliSense otomatik tamamlama](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Öneriler pencere öğesi CamelCase filtrelemesini destekler. Önerileri sınırlandırmak için bir yöntem adında üst sınır olan harfleri yazabilirsiniz. Örneğin, "CRA" hızla "createApplication" öğesini getirecek.

### <a name="types-of-completions"></a>Tamamlama türleri

VS Code IntelliSense için Stream Analytics araçları, dil sunucusu önerileri, kod parçacıkları ve basit sözcük tabanlı metinsel tamamlama gibi farklı türlerde tamamlama sağlar.

|Tamamlama     |  Tür       |
| ----- | ------- |
| Anahtar sözcükler | `keyword`
| İşlevler | `build-in function`, `user defined function`  |
| Veri kümesi adı| `input`, `output`, `intermediate result set`|
| Veri kümesi sütun adı|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Ad tamamlama

Otomatik tamamlama anahtar sözcüğünün yanı sıra, VS Code için Stream Analytics araçları, yapılandırma sırasında veri kaynaklarınızda bulunan sütunların adlarını ve iş giriş ve çıkış adlarının listesini okur. Uzantı, birkaç tuş vuruşu içeren deyimleri girerken yararlı olan ad tamamlama özellikleri sağlamak için bu bilgileri anımsar:

Kodlama sırasında, iş girişi adları, çıkış adı ve sütun adlarında arama gerçekleştirmek için düzenleyiciyi bırakmanız gerekmez. İçeriğinizi koruyabilir, ihtiyacınız olan bilgileri bulabilir, doğrudan kodunuza öğe ekleyebilir ve IntelliSense 'i sizin için yazdıklarınızı tamamda tamamlayabilirsiniz.

Yerel girişi veya canlı girişi yapılandırmanız ve ad tamamlamayı kullanabilmeniz için yapılandırma dosyasını kaydetmeniz gerektiğini unutmayın.

![ad tamamlama](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Parametre Bilgisi

IntelliSense **parametre bilgisi** seçeneği, bir işlevin gerektirdiği parametrelerin sayısı, adları ve türleri hakkında bilgi sağlayan bir parametre listesi açar. Kalın yazı parametresi, bir işlev yazarken gerekli olan sonraki parametreyi gösterir.

Parametre listesi, iç içe geçmiş işlevler için de görüntülenir. Başka bir işleve parametre olarak bir işlev yazarsanız, parametre listesinde iç işlevin parametreleri görüntülenir. Ardından, iç işlev parametre listesi tamamlandığında, parametre listesi dış işlev parametrelerini görüntülemeye geri döner.

![parametre bilgisi](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Hızlı Bilgi

Dil hizmeti tarafından sağlandığı gibi, kodunuzdaki her tanımlayıcı için **hızlı bilgi** görebilirsiniz. Bazı tanımlayıcılar örnekleri girdi, çıktı, ara sonuç kümesi veya işlevdir. Fare işaretçisini bir tanımlayıcının üzerine getirdiğinizde, bildirimi açılır pencerede görüntülenir. Yapılandırıldıysa, ve ara veri kümesi, girdileri için özellikler ve veri şemaları gösterilir.

![hızlı bilgi](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense sorunlarını giderme

Bu sorun, veri sağlayan eksik giriş yapılandırması nedeniyle oluşur. [Yerel bir girdinin](visual-studio-code-local-run.md#define-a-local-input) veya [canlı girişin](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) doğru yapılandırılıp yapılandırılmadığını denetleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Visual Studio Code Azure Stream Analytics iş oluşturma](quick-create-visual-studio-code.md)
* [Visual Studio Code kullanarak örnek verilerle yerel olarak Stream Analytics sorguları test edin](visual-studio-code-local-run.md)
* [Visual Studio Code kullanarak canlı akış girişine göre Stream Analytics sorguları yerel olarak test edin](visual-studio-code-local-run-live-input.md)