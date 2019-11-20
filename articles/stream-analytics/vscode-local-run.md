---
title: Visual Studio Code ile test Azure Stream Analytics sorguları
description: Bu makalede, Visual Studio Code için Azure Stream Analytics araçlarıyla sorguların yerel olarak nasıl test edileceğini açıklar.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924967"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Sorguları Visual Studio Code ile yerel olarak test Stream Analytics

Visual Studio Code için Azure Stream Analytics araçları kullanarak, örnek verilerle Stream Analytics işlerinizi yerel olarak test edebilirsiniz.

Visual Studio Code kullanarak Stream Analytics işi oluşturmayı öğrenmek için bu [hızlı](quick-create-vs-code.md) başlangıcı kullanın.

## <a name="prerequisites"></a>Önkoşullar
* [.NET Core SDK 'sını](https://dotnet.microsoft.com/download)yükler.
* Visual Studio Code yeniden başlatın.
 
## <a name="run-queries-locally"></a>Sorguları yerel olarak çalıştır

Visual Studio Code için Azure Stream Analytics uzantısını kullanarak, örnek verilerle Stream Analytics işlerinizi yerel olarak test edebilirsiniz.

1. Stream Analytics işinizi oluşturduktan sonra, komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın. Ardından asa yazın ve seçin **: giriş Ekle**.

    ![Visual Studio Code 'da ASA girişi ekleme](./media/vscode-local-run/add-input.png)

2. **Yerel giriş**' i seçin.

    ![Visual Studio Code 'a ASA yerel girişi ekleme](./media/vscode-local-run/add-local-input.png)

3. **+ Yeni yerel giriş**' i seçin.

    ![Visual Studio Code 'da yeni bir ASA yerel girişi ekleme](./media/vscode-local-run/add-new-local-input.png)

4. Sorgunuzda kullandığınız giriş diğer adını girin.

    ![Yeni bir ASA yerel giriş diğer adı ekleme](./media/vscode-local-run/new-local-input-alias.png)

5. **LocalInput_DefaultLocalStream. JSON** dosyasında, yerel veri dosyanızın bulunduğu dosya yolunu girin.

    ![Visual Studio 'da yerel dosya yolunu girin](./media/vscode-local-run/local-file-path.png)

6. Sorgu düzenleyicinize dönün ve **yerel olarak çalıştır**' ı seçin.

    ![Sorgu düzenleyicisinde yerel olarak Çalıştır ' ı seçin](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio Code Azure Stream Analytics bulut işi oluşturma (Önizleme)](quick-create-vs-code.md)

* [Visual Studio Code Azure Stream Analytics işleri keşfet (Önizleme)](vscode-explore-jobs.md)
