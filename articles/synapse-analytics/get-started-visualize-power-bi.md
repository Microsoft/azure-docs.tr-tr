---
title: 'Öğretici: Azure SYNAPSE Analytics ile çalışmaya başlama-Power BI çalışma alanı verilerini görselleştirin'
description: Bu öğreticide, Power BI çalışma alanı oluşturmayı, Azure SYNAPSE çalışma alanınızı bağlamayı ve Azure SYNAPSE çalışma alanındaki verileri kullanan bir Power BI veri kümesi oluşturmayı öğreneceksiniz.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 03/25/2021
ms.openlocfilehash: 829daaa9c739657528a9085201c61c88635931d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608451"
---
# <a name="visualize-data-with-power-bi"></a>Power BI ile verileri görselleştirme

Bu öğreticide, Power BI çalışma alanı oluşturmayı, Azure SYNAPSE çalışma alanınızı bağlamayı ve Azure SYNAPSE çalışma alanınızdaki verileri kullanan bir Power BI veri kümesi oluşturmayı öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlayabilmeniz için [Power BI Desktop](https://aka.ms/pbidesktopstore)' yi yüklemelisiniz.

## <a name="overview"></a>Genel Bakış

NYC TAXI verilerinden, toplanan veri kümelerini iki tabloda oluşturduk:
- **nyctaxi. passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Power BI çalışma alanını Azure SYNAPSE çalışma alanınıza bağlayabilirsiniz. Bu özellik Power BI çalışma alanınıza kolayca veri almanıza olanak sağlar. Power BI raporlarınızı doğrudan Azure SYNAPSE çalışma alanınızda düzenleyebilirsiniz. 

### <a name="create-a-power-bi-workspace"></a>Power BI çalışma alanı oluşturma

1. [Powerbi.Microsoft.com](https://powerbi.microsoft.com/)'de oturum açın.
1. **Çalışma alanları**' na tıklayın ve ardından **çalışma alanı oluştur**' u seçin. Bu ad benzersiz olması gerektiğinden **NYCTaxiWorkspace1** veya benzeri adlı yeni bir Power BI çalışma alanı oluşturun.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Azure SYNAPSE çalışma alanınızı yeni Power BI çalışma alanınıza bağlama

1. SYNAPSE Studio 'da   >  **bağlı hizmetleri** Yönet ' e gidin.
1. Power BI **Yeni**  >  **Bağlan**' ı seçin.
1. **Adı** **NYCTaxiWorkspace1** olarak ayarlayın.
1. **Çalışma alanı adını** , yukarıda oluşturduğunuz Power BI çalışma alanına, **NYCTaxiWorkspace1** benzer şekilde ayarlayın.
1. **Oluştur**’u seçin.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Azure SYNAPSE çalışma alanınızdaki verileri kullanan bir Power BI veri kümesi oluşturma

1. SYNAPSE Studio 'da Power BI **geliştirme** bölümüne gidin  >  .
1. **NYCTaxiWorkspace1**  >  **Power BI veri kümelerine** gidin ve **Yeni Power BI veri kümesi ' ni** seçin. **Başlat**'a tıklayın.
1. **SQLPOOL1** veri kaynağını seçin ve **devam**' a tıklayın.
1. **NYCTaxiWorkspace1SQLPOOL1. pbıds** dosyanız için. pbıd dosyasını Indirmek için **İndir** ' e tıklayın. **Devam**’a tıklayın.
1. İndirilen **. pbıds** dosyasını açın. Power BI Desktop açılır ve Azure SYNAPSE çalışma alanınızda **SQLDB1** 'e otomatik olarak bağlanır.
1. **SQL Server veritabanı** olarak adlandırılan bir iletişim kutusu görürseniz:
    1. **Microsoft hesabı** seçin.
    1. **Oturum aç** ' ı seçin ve hesabınızda oturum açın.
    1. **Bağlan**’ı seçin.
1. **Gezgin** iletişim kutusu açıldıktan sonra, **Passengercountstats** tablosunu denetleyip **Yükle**' yi seçin.
1. **Bağlantı ayarları** iletişim kutusu göründükten sonra **DirectQuery**  >  **Tamam**' ı seçin.
1. Sol taraftaki **rapor** düğmesini seçin.
1. **Görselleştirmeler** altında, raporunuza **çizgi grafik** eklemek için çizgi grafik simgesine tıklayın.
    1. **Alanlar**' ın altında, **passengercount** sütununu **görsel öğeler**  >  **eksenine** sürükleyin.
    1. **Sumüçlü uzaklığı** ve **avgüçlü uzaklığı** sütunlarını **görselleştirmeler**  >  **değerlerine** sürükleyin.
1. **Giriş** sekmesinde **Yayımla**' yı seçin.
1. Değişikliklerinizi kaydetmek için **Kaydet** seçeneğini belirleyin.
1. **Passengeranalysis. pbix** dosya adını seçin ve ardından **Kaydet**' i seçin.
1. **Power BI Yayımla** penceresinde, **Hedef Seç**' in altında, **NYCTaxiWorkspace1**' yi seçin ve ardından **Seç**' e tıklayın.
1. Yayımlamanın bitmesini bekleyin. 

### <a name="configure-authentication-for-your-dataset"></a>Veri kümeniz için kimlik doğrulamasını yapılandırma

1. [Powerbi.Microsoft.com](https://powerbi.microsoft.com/) açın ve **oturum açın**.
1. Sol tarafta, **çalışma alanları** altında **NYCTaxiWorkspace1** çalışma alanını seçin.
1. Bu çalışma alanının içinde, **Passenger Analizi** adlı bir veri kümesini ve **Passenger Analizi** adlı bir raporu bulun.
1. **Passengeranalysis** veri kümesinin üzerine gelin, üç nokta (...) düğmesini seçin ve ardından **Ayarlar**' ı seçin.
1. **Veri kaynağı kimlik bilgileri**' nde, **Düzenle**' ye tıklayın, **kimlik doğrulama yöntemini** **OAuth2** olarak ayarlayın ve **oturum aç**' ı seçin.

### <a name="edit-a-report-in-synapse-studio"></a>SYNAPSE Studio 'da rapor düzenleme

1. SYNAPSE Studio 'ya dönün ve **Kapat ve Yenile '** yi seçin.
1. **Geliştirme** merkezine gidin.
1. **Power BI** katmanı, üç nokta (...) düğmesinin sağında ve **Power BI raporları** düğümünü yenilemek için **Yenile** ' ye tıklayın.
1. **Power BI** altında şunları görmeniz gerekir:
    * **NYCTaxiWorkspace1**  >  **Power BI veri kümelerinde**, **passengeranalysis** adlı yeni bir veri kümesi.
    * **NYCTaxiWorkspace1**  >  **Power BI raporlarında**, **passengeranalysis** adlı yeni bir rapor.
1. **Passengeranalysis** raporunu seçin. Rapor açılır ve doğrudan SYNAPSE Studio içinde düzenleyebilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İzleme](get-started-monitor.md)
                                 

