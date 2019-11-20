---
title: Visual C# Studio 'da Azure Stream Analytics işleri için Kullanıcı tanımlı işlevler yazma (Önizleme)
description: Visual Studio 'da Stream Analytics işleri için c# Kullanıcı tanımlı işlevleri yazmayı öğrenin.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: d6cf420c8baceb243e8c4d70c8bcbc95ec626c3a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990253"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Öğretici: Azure Stream Analytics işi C# için Kullanıcı tanımlı bir işlev yazma (Önizleme)

Visual Studio'da oluşturulan C# kullanıcı tanımlı işlevler (UDF), Azure Stream Analytics sorgu dilini kendi işlevlerinizi kullanarak genişletmenizi sağlar. C# ile var olan kodu (DLL'ler dahil) yeniden kullanabilir, matematiksel veya karmaşık mantıklardan faydalanabilirsiniz. UDF uygulamak için kullanabileceğiniz üç yöntem vardır: Stream Analytics projesinde CodeBehind dosyaları kullanma, yerel C# projesindeki UDF'leri alma veya bir depolama hesabındaki pakette bulunan UDF'leri alma. Bu öğreticide CodeBehind yöntemi kullanılarak basit bir C# işlevi uygulanmaktadır. Stream Analytics işleri için UDF özelliği şu anda önizlemededir ve üretim iş yükleri içinde kullanılmamalıdır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * CodeBehind kullanarak C# ile kullanıcı tanımlı işlev oluşturma.
> * Stream Analytics işinizi yerel olarak test edin.
> * İşinizi Azure 'da yayımlayın.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdaki önkoşulları tamamladığınızdan emin olun:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [Visual Studio için Stream Analytics araçlarını](stream-analytics-tools-for-visual-studio-install.md) ve **Azure geliştirme** veya **Veri Depolama ve İşleme** iş yüklerini yükleyin.
* Bir IoT Edge işi (Stream-Analytics-Tools-for-Visual-Studio-Edge-jobs.md) oluşturuyorsanız var olan [Stream Analytics Edge geliştirme kılavuzuna] göz atın.

## <a name="create-a-container-in-your-azure-storage-account"></a>Azure Depolama Hesabınızda kapsayıcı oluşturma

Oluşturduğunuz kapsayıcı, derlenen C# paketi depolamak için kullanılacaktır. Bir Edge işi oluşturursanız bu depolama hesabı, paketi IoT Edge cihazınıza dağıtmak için de kullanılacaktır. Her Stream Analytics işi için ayrı bir kapsayıcı kullanın. Bir kapsayıcının birden fazla Stream Analytics Edge işi için kullanılması desteklenmez. Kapsayıcı bulunan bir depolama hesabınız varsa onu kullanabilirsiniz. Yoksa [yeni bir kapsayıcı oluşturmanız](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) gerekir. 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Visual Studio 'da Stream Analytics projesi oluşturma

1. Visual Studio’yu çalıştırın.

2. **Dosya > Yeni > Proje**'yi seçin.

3. Soldaki şablonlar listesinde **Stream Analytics**' yi seçin ve **Azure Stream Analytics Edge uygulaması** veya **Azure Stream Analytics uygulaması**' nı seçin.

4.  Projenin **Ad**, **Konum** ve **Çözüm adı** değerlerini girip **Tamam**'ı seçin.

    ![Visual Studio’da Azure Stream Analytics Edge projesi oluşturma](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Derleme paketi yolunu yapılandırma

1. Visual Studio'yu açın ve **Çözüm Gezgini**'ne gidin.

2. İş yapılandırma dosyasına (`EdgeJobConfig.json`) çift tıklayın.

3. **Kullanıcı Tanımlı Kod Yapılandırması** bölümünü genişletin ve yapılandırmaya aşağıdaki önerilen değerleri ekleyin:

   |**Ayar**|**Önerilen Değer**|
   |-------|---------------|
   |Genel depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Genel depolama ayarları aboneliği| aboneliğinizi < >|
   |Genel depolama ayarları depolama hesabı| depolama hesabınızı < >|
   |Özel kod depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Özel kod depolama ayarları depolama hesabı|depolama hesabınızı < >|
   |Özel kod depolama ayarları kapsayıcısı|depolama kapsayıcınızı < >|


## <a name="write-a-c-udf-with-codebehind"></a>CodeBehind ile C# UDF yazma
CodeBehind dosyası, tek bir C# asa sorgu betiği ile ilişkili bir dosyadır. Gönderme işlemi sırasında Visual Studio araçları CodeBehind dosyasını otomatik olarak sıkıştırıp Azure depolama hesabınıza yükler. Tüm sınıfların *genel*, tüm nesnelerin de *statik genel* olarak tanımlanması gerekir.

1. **Çözüm Gezgini**'nde **Script.asql** dosyasını genişleterek **Script.asaql.cs** CodeBehind dosyasını bulun.

2. Kodu aşağıdaki örnekle değiştirin:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>UDF'yi uygulama

1. **Çözüm Gezgini**'nde **Script.asaql** dosyasını açın.

2. Var olan sorguyu aşağıdakiyle değiştirin:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Yerel ortamda test etme

1. [Sıcaklık simülatörü örnek veri dosyasını](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)indirin.

2. **Çözüm Gezgini**'nde **Girişler**'i genişletin, **Input.json** dosyasına sağ tıklayın ve **Yerel Giriş Ekle**'yi seçin.

   ![Visual Studio 'da Stream Analytics işe yerel giriş ekleme](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. İndirdiğiniz örnek verilerin yerel giriş dosyası yolunu belirtin ve **Kaydet**'i seçin.

    ![Visual Studio 'da Stream Analytics işi için yerel giriş yapılandırması](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Betik düzenleyicisinde **Yerel Olarak Çalıştır**'a tıklayın. Yerel çalıştırma çıkış sonuçlarını başarıyla kaydettikten sonra sonuçları tablo biçiminde görmek için herhangi bir tuşa basın. 

    ![Visual Studio'da Azure Stream Analytics işini yerel olarak çalıştırma](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. İsterseniz **Sonuç Klasörünü Aç**'ı seçerek JSON ve CSV biçimindeki ham dosyaları da görüntüleyebilirsiniz.

    ![Visual Studio'da Azure Stream Analytics işinin sonuçlarını görüntüleme](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>UDF'de hata ayıklama
C# UDF hatalarını yerel ortamda standart C# kodunda olduğu gibi ayıklayabilirsiniz. 

1. C# işlevinize kesme noktaları ekleyin.

    ![Visual Studio 'da Kullanıcı tanımlı Stream Analytics işleve kesme noktaları ekleme](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Hata ayıklamaya başlamak için **F5**'e basın. Program beklendiği gibi kesme noktalarında durur.

    ![Kullanıcı tanımlı Stream Analytics hata ayıklama sonuçlarını görüntüle](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>İşinizi Azure'da yayımlama
Sorgunuzu yerel ortamda test ettikten sonra işi Azure'da yayımlamak için betik düzenleyicisinde **Azure'a Gönder**'i seçin.

![Stream Analytics Edge işinizi Visual Studio'dan Azure'a gönderme](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>IoT Edge'e cihazlarına dağıtma
Stream Analytics Edge işi oluşturmayı seçerseniz, bu artık bir IoT Edge modülü olarak dağıtılabilir. [IoT Edge hızlı başlangıcını](https://docs.microsoft.com/azure/iot-edge/quickstart) izleyerek bir IoT Hub oluşturun, ona bir IoT Edge cihazını kaydedin ve cihazınızda IoT Edge çalışma zamanını çalıştırın. Ardından [işi dağıtma](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) öğreticisini izleyerek Stream Analytics işinizi IoT Edge modülü olarak dağıtın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, CodeBehind kullanarak basit C# bir Kullanıcı tanımlı işlev oluşturdunuz, işinizi Azure 'da yayımladınız ve işi Azure 'a veya IoT Edge cihaza dağıttınız. 

Stream Analytics işleri için Kullanıcı tanımlı işlevleri kullanmanın C# farklı yolları hakkında daha fazla bilgi edinmek için bu makaleye geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics C# için yazma işlevleri](stream-analytics-edge-csharp-udf-methods.md)
