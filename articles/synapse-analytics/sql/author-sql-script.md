---
title: SYNAPSE Studio 'da SQL betikleri
description: Azure SYNAPSE Analytics 'te SYNAPSE Studio SQL betiklerine giriş.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98118702"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE Studio SQL betikleri 

SYNAPSE Studio, SQL sorguları yazmak için bir SQL komut dosyası Web arabirimi sağlar. 

## <a name="begin-authoring-in-sql-script"></a>SQL betikte yazmayı Başlat 

SQL komut dosyasında yazma deneyimini başlatmak için birkaç yol vardır. Aşağıdaki yöntemlerden birini kullanarak yeni bir SQL betiği oluşturabilirsiniz.

1. Geliştir menüsünde, **"+"** simgesini seçin ve **SQL betiği**' ni seçin.

2. **Eylemler** menüsünden **Yeni SQL betiği**' ni seçin.

3. SQL betikleri geliştirme altındaki **Eylemler** menüsünden **içeri aktar** ' ı seçin. Yerel depoınızdan mevcut bir SQL betiğini seçin.
![Yeni SQL betiği 3 eylemleri](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>SQL komut dosyanızı oluşturma

1. **Özellik** düğmesini seçerek ve SQL betiğine atanan varsayılan adı değiştirerek SQL komutlarınız için bir ad seçin. 
![Yeni SQL betiği yeniden adlandırma](media/author-sql-script/new-sql-script-rename.png)

2. **Bağlan** açılan menüsünden belırlı adanmış SQL havuzunu veya SUNUCUSUZ SQL havuzunu seçin. Ya da gerekirse veritabanını **kullan**' ı seçin. 
![Yeni SQL seçme havuzu](media/author-sql-script/new-sql-choose-pool.png)

3. IntelliSense özelliğini kullanarak SQL komut dosyanızı yazmaya başlayın.

## <a name="run-your-sql-script"></a>SQL komut dosyanızı çalıştırın

SQL betiğinizi yürütmek için **Çalıştır** düğmesini seçin. Sonuçlar bir tabloda varsayılan olarak görüntülenir.

![Yeni SQL betiği sonuçları tablosu](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Sonuçlarınızı dışarı aktarın

"Sonuçları dışarı aktar" seçeneğini belirleyerek ve uzantıyı seçerek, sonuçları yerel depolama verilerinize farklı biçimlerde (CSV, Excel, JSON, XML dahil) aktarabilirsiniz.

Ayrıca, **grafik** düğmesini seçerek BIR grafikteki SQL betiği sonuçlarını görselleştirebilirsiniz. "Grafik türü" ve **Kategori sütununu** seçin. Resim **olarak kaydet**' i seçerek grafiği bir resme dışarı aktarabilirsiniz. 

![Yeni SQL betiği sonuçları grafiği](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Bir Parquet dosyasından verileri araştırma

Dosya içeriğini önizlemek için SQL betiği kullanarak bir depolama hesabındaki Parquet dosyalarını inceleyebilirsiniz.

![Yeni betik sqlod Parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL tabloları, dış tablolar, görünümler

Veriler ' in altındaki **Eylemler** menüsünü seçerek, şöyle çeşitli eylemler seçebilirsiniz:

- Yeni SQL betiği
- En üstteki 1000 satırları seçin
- CREATE
- BıRAKMA ve oluşturma 
 
SQL veritabanlarının düğümlerine sağ tıklayarak kullanılabilir hareketi keşfedebilirsiniz.
 
![Yeni betik veritabanı](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Klasörler oluşturma ve SQL betiklerini bir klasöre taşıma

SQL betikleri geliştirme altındaki Eylemler menüsünde, SQL betikleri geliştirme altında "eylemler" menüsünden "yeni klasör" seçeneğini belirleyin. Ve açılır penceredeki yeni klasörün adını yazın. 

> [!div class="mx-imgBorder"] 
> ![' Yeni klasör ' seçiliyken bir SQL komut dosyası örneği gösteren ekran görüntüsü.](./media/author-sql-script/new-sql-script-create-folder.png)

Bir SQL betiğini bir klasöre taşımak için, SQL betiğini seçip Eylemler menüsünden "taşı" seçeneğini belirleyebilirsiniz. Ardından, yeni pencerede hedef klasörünü bulun ve SQL betiğini seçili klasöre taşıyın. Ayrıca, SQL betiğini hızlıca sürükleyip bir klasöre bırakabilirsiniz.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Sonraki adımlar

SQL betiği yazma hakkında daha fazla bilgi için bkz. [Azure SYNAPSE Analytics](../index.yml).