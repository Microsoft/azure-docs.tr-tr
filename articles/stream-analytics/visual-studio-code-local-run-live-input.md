---
title: Visual Studio Code kullanarak canlı akış girişine göre Azure Stream Analytics sorguları yerel olarak test edin
description: Bu makalede, Visual Studio Code için Azure Stream Analytics araçları kullanarak canlı akış girişine karşı sorguları yerel olarak test etmek açıklanır.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93122903"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Visual Studio Code kullanarak canlı akış girişine göre Stream Analytics sorguları yerel olarak test edin

Stream Analytics işlerinizi canlı akış girişine karşı yerel olarak test etmek için Visual Studio Code Azure Stream Analytics araçları kullanabilirsiniz. Giriş, Azure Event Hubs veya Azure IoT Hub gibi bir kaynaktan gelebilir. Çıktı sonuçları, projenizdeki **Localrunçıktılar** adlı BIR klasöre JSON dosyaları olarak gönderilir.

## <a name="prerequisites"></a>Önkoşullar

* [.NET Core SDK](https://dotnet.microsoft.com/download) yükleyip Visual Studio Code yeniden başlatın.

* Visual Studio Code kullanarak Stream Analytics işi oluşturmayı öğrenmek için [Bu hızlı](quick-create-visual-studio-code.md) başlangıcı kullanın.

## <a name="define-a-live-stream-input"></a>Canlı akış girişi tanımlama

1. Stream Analytics projenizdeki **girişler** klasörüne sağ tıklayın. Ardından **asa: bağlam menüsünden giriş Ekle** öğesini seçin.

   ![Girişler klasöründen giriş ekleme](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Ayrıca, **CTRL + SHIFT + P** ' yi seçerek komut paletini açabilir ve **asa: giriş Ekle**' yi girebilirsiniz.

   ![Visual Studio Code Stream Analytics girişi ekleme](./media/quick-create-visual-studio-code/add-input.png)

2. Açılan listeden bir giriş kaynak türü seçin.

   ![Giriş seçeneği olarak IoT Hub 'ı seçin](./media/quick-create-visual-studio-code/iot-hub.png)

3. Girişi komut paletinden eklediyseniz, girişi kullanacak Stream Analytics sorgu betiğini seçin. **MyASAproj. aşama QL** dosya yoluyla otomatik olarak doldurulmalıdır.

   ![Visual Studio Code bir Stream Analytics betiği seçin](./media/quick-create-visual-studio-code/asa-script.png)

4. Açılır menüden **Azure aboneliklerinizden Seç ' i** seçin.

    ![Aboneliklerden Seç](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Yeni oluşturulan JSON dosyasını yapılandırın. CodeLens özelliğini kullanarak bir dize girmenize, açılan listeden seçim yapmanıza veya metnin doğrudan dosyada değiştirilmesini sağlayabilirsiniz. Aşağıdaki ekran görüntüsünde bir örnek olarak **aboneliklerinizden seçim** gösterilmektedir.

   ![Visual Studio Code girişi yapılandırma](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Önizleme girişi

Giriş verilerinin geldiğinden emin olmak için, en üstteki satırdaki canlı giriş yapılandırma dosyanızda **Verileri Önizle** ' yi seçin. Bazı giriş verileri bir IoT Hub 'ından gelir ve önizleme penceresinde gösterilir. Önizlemenin görünmesi birkaç saniye sürebilir.

 ![Canlı giriş önizlemesi](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Sorguları yerel olarak çalıştır

Sorgu düzenleyicinize dönün ve **yerel olarak çalıştır**' ı seçin. Ardından açılan listeden **canlı girişi kullan** ' ı seçin.

![Sorgu Düzenleyicisi 'nde "yerel olarak çalıştır" seçeneğini belirleyin](./media/vscode-local-run/run-locally.png)

!["Canlı giriş kullan" ı seçin](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Sonuç doğru pencerede gösterilir ve her 3 saniyede bir yenilenir. Yeniden test etmek için **Çalıştır** ' ı seçebilirsiniz. Dosya Gezgini 'nde sonuç dosyalarını görmek ve Visual Studio Code veya Excel gibi bir araçla açmak için **klasörü aç** ' ı da seçebilirsiniz. Sonuç dosyalarının yalnızca JSON biçiminde kullanılabilir olduğunu unutmayın.

İşin çıkış oluşturmaya başlaması için varsayılan zaman, **Şimdi** olarak ayarlanır. Sonuç penceresinde **Çıkış başlangıç zamanı** düğmesini seçerek saati özelleştirebilirsiniz.

![Yerel çalıştırma sonucunu görüntüle](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio Code Azure Stream Analytics işleri keşfet (Önizleme)](visual-studio-code-explore-jobs.md)

* [NPM paketini kullanarak CI/CD işlem hatlarını ayarlama](./cicd-overview.md)