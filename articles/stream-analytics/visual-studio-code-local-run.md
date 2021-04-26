---
title: Visual Studio Code kullanarak örnek verilerle Azure Stream Analytics işi yerel olarak test etme
description: Bu makalede, Visual Studio Code için Azure Stream Analytics araçları kullanılarak sorguların yerel olarak nasıl test edileceğini açıklar.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97708458"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Visual Studio Code kullanarak örnek verilerle yerel olarak Stream Analytics sorguları test edin

Visual Studio Code için Azure Stream Analytics araçları kullanarak, örnek verilerle Stream Analytics işlerinizi yerel olarak test edebilirsiniz. Sorgu sonuçlarını projenizin **Localrunçıktılar** klasöründe JSON dosyalarında bulabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [.NET Core SDK 'sını](https://dotnet.microsoft.com/download) yükleyip Visual Studio Code yeniden başlatın.

* Visual Studio Code kullanarak Stream Analytics işi oluşturmayı öğrenmek için bu [hızlı](quick-create-visual-studio-code.md) başlangıcı kullanın.

## <a name="prepare-sample-data"></a>Örnek verileri hazırlama

Önce örnek giriş veri dosyalarını hazırlamanız gerekir. Makinenizde zaten bir örnek veri dosyası varsa, bu adımı atlayabilir ve bir sonraki adıma geçebilirsiniz.

1. En üstteki satırdaki giriş yapılandırma dosyanızda bulunan **Verileri Önizle** ' ye tıklayın. Bazı giriş verileri IoT Hub alınır ve önizleme penceresinde gösterilir. Bu işlem biraz zaman alabilir.

2. Veriler gösterildiğinde, verileri yerel bir dosyaya kaydetmek için **farklı kaydet** ' e tıklayın.

 ![Canlı giriş önizlemesi](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Yerel giriş tanımlama

1. Stream Analytics projenizdeki girişler klasörü altında **input.js** ' a tıklayın. Ardından en üstteki satırdan **yerel giriş Ekle** ' yi seçin.

    ![Projeden yerel giriş Ekle](./media/quick-create-visual-studio-code/add-input-from-project.png)

    Ayrıca, **CTRL + SHIFT + P** tuşlarını kullanarak komut paletini açabilir ve **asa: giriş Ekle**' ye girebilirsiniz.

   ![VS Code Stream Analytics girişi ekleme](./media/quick-create-visual-studio-code/add-input.png)

2. **Yerel giriş**' i seçin.

    ![Visual Studio Code ASA yerel girişi ekleme](./media/vscode-local-run/add-local-input.png)

3. **+ Yeni yerel giriş**' i seçin.

    ![Visual Studio Code yeni bir ASA yerel girişi ekleme](./media/vscode-local-run/add-new-local-input.png)

4. Sorgunuzda kullandığınız giriş diğer adını girin.

    ![Yeni bir ASA yerel giriş diğer adı ekleme](./media/vscode-local-run/new-local-input-alias.png)

5. Dosya üzerinde yeni oluşturulan **LocalInput_Input.js** , yerel veri dosyanızın bulunduğu dosya yolunu girin.

    ![Visual Studio 'da yerel dosya yolunu girin](./media/vscode-local-run/local-file-path.png)

6. Giriş verilerinin önizlemesini görüntülemek için **Verileri Önizle** ' yi seçin. Verileriniz için serileştirme türü, JSON veya CSV olursa otomatik olarak algılanır. Verilerinizi **tablo** veya **Ham** biçimde görüntülemek için seçiciyi kullanın. Aşağıdaki tablo, **tablo biçimindeki** verilerin bir örneğidir:

     ![Tablo biçiminde yerel verileri Önizle](./media/vscode-local-run/local-file-preview-table.png)

    Aşağıdaki tablo, **Ham biçimdeki** verilerin bir örneğidir:

    ![Yerel verileri ham biçimde Önizle](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Sorguları yerel olarak çalıştır

Sorgu düzenleyicinize dönün ve **yerel olarak çalıştır**' ı seçin. Ardından açılan listeden **Yerel girişi kullan** ' ı seçin.

![Sorgu düzenleyicisinde yerel olarak Çalıştır ' ı seçin](./media/vscode-local-run/run-locally.png)

![Yerel giriş kullan](./media/vscode-local-run/run-locally-use-local-input.png)

Sonuç doğru pencerede gösterilir. Test etmek için **Çalıştır** ' a tıklayabilirsiniz. Ayrıca, dosya Gezgini 'nde sonuç dosyalarını görmek ve bunları diğer araçlarla açmak için **klasörü aç** ' ı da seçebilirsiniz. Sonuç dosyalarının yalnızca JSON biçiminde kullanılabilir olduğunu unutmayın.

![Yerel çalıştırma sonucunu görüntüle](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio Code kullanarak canlı giriş ile Azure Stream Analytics işleri yerel olarak test etme](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code Azure Stream Analytics işleri keşfet (Önizleme)](visual-studio-code-explore-jobs.md)
