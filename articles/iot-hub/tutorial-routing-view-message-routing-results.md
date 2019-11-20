---
title: Azure IoT Hub ileti yönlendirme sonuçlarını görüntüleme (.NET) | Microsoft Docs
description: Öğreticinin 1. kısmını kullanarak tüm kaynakları ayarladıktan sonra, Azure Stream Analytics iletileri yönlendirme ve sonuçları PowerBI 'da görüntüleme özelliğini ekleyin.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bfee4e64070e5f37eaa3d63280409f00c0ed8672
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890403"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Öğretici: 2. Bölüm-yönlendirilmiş iletileri görüntüleme

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>İletileri yönlendirme kuralları

İleti yönlendirmenin kuralları şunlardır. Bunlar Bu öğreticinin 1. bölümünde ayarlanmıştır ve bu ikinci bölümde çalıştığını görürsünüz.

|Değer |Sonuç|
|------|------|
|level="storage" |Azure Depolama'ya yazın.|
|level="critical" |Service Bus kuyruğuna yazın. Mantıksal Uygulama iletiyi kuyruktan alır ve Office 365 kullanarak iletiyi e-postayla gönderir.|
|default |Power BI'ı kullanarak bu verileri görüntüleyin.|

Artık iletilerin yönlendirileceği kaynakları oluşturur, hub 'a ileti göndermek için bir uygulama çalıştırın ve yönlendirme eylemi ' ne bakın.

## <a name="create-a-logic-app"></a>Mantıksal Uygulama oluşturma  

Service Bus kuyruğu kritik olarak belirlenmiş iletileri almak için kullanılacaktır. Service Bus kuyruğunu izlemek ve kuyruğa ileti eklendiğinde bir e-posta göndermek için bir Mantıksal uygulama ayarlayın.

1. [Azure Portal](https://portal.azure.com) **+ kaynak oluştur**' u seçin. Arama kutusuna **mantıksal uygulama** yazın ve Enter tuşuna basın. Görüntülenecek arama sonuçlarından Logic App ' i seçin, sonra **mantıksal uygulama oluştur** bölmesine devam etmek için **Oluştur** ' u seçin. Alanları doldurun.

   **Ad**: bu alan, mantıksal uygulamanın adıdır. Bu öğreticide **ContosoLogicApp** kullanılır.

   **Abonelik**: Azure aboneliğinizi seçin.

   **Kaynak grubu**: **mevcut olanı kullan** ' ı seçin ve kaynak grubunuzu seçin. Bu öğreticide **ContosoResources** kullanılır.

   **Konum**: Konumunuzu kullanın. Bu öğreticide **West US** kullanılır.

   **Log Analytics**: Bu iki durumlu düğme kapalı konuma getirilmelidir.

   ![Mantıksal uygulama oluşturma ekranı](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   **Oluştur**'u seçin. Uygulamanın dağıtılması birkaç dakika sürebilir.

2. Şimdi Mantıksal Uygulama'ya gidin. Mantıksal uygulamayı almanın en kolay yolu **kaynak grupları**seçmek, kaynak grubunuzu seçmek (Bu öğreticinin **contosoresources**kullanması) ve ardından kaynak listesinden mantıksal uygulamayı seçmeniz gerekir. 

    Logic Apps Tasarımcısı sayfası gösterilir (sayfanın tamamını görmek için ekranı sağa doğru kaydırmanız gerekebilir). Logic Apps tasarımcı sayfasında, **boş Logic App +** belirten kutucuğu görene kadar aşağı kaydırın ve bunu seçin. Varsayılan sekme "sizin Için" dir. Bu bölme boş ise, tüm bağlayıcıları ve Tetikleyicileri görmek için **Tümü** ' nü seçin.

3. Bağlayıcılar listesinden **Service Bus** ' yi seçin.

   ![Bağlayıcıların listesi](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Tetikleyici listesi görüntülenir. **Kuyrukta bir ileti alındığında (otomatik tamamlamayı)/Service Bus**seçin.

   ![Service Bus tetikleyicilerinin listesi](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Sonraki ekranda, Bağlantı Adı alanını doldurun. Bu öğreticide **ContosoConnection** kullanılır.

   ![Service Bus kuyruğu için bağlantı kuruluyor](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Service Bus ad alanını seçin. Bu öğreticide **ContosoSBNamespace** kullanılır. Ad alanını seçtiğinizde, portal anahtarları almak için Service Bus ad alanını sorgular. **RootManageSharedAccessKey** ' ı seçin ve **Oluştur**' u seçin.

   ![Bağlantıyı kurma işlemi sonlandırılıyor](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Sonraki ekranda, açılan listeden kuyruğun adını seçin (bu öğreticide **contososbqueue** kullanılır). Kalan alanlar için varsayılan değerleri kullanabilirsiniz.

   ![Sıra seçenekleri](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Şimdi kuyrukta bir ileti alındığında e-posta gönderme eylemini ayarlayın. Logic Apps tasarımcısında, bir adım eklemek için **+ yeni adım** ' ı seçin ve ardından tüm seçenekleri görmek için **Tümü** ' nü seçin. **Eylem seçin** bölmesinde **Office 365 Outlook**' u bulun ve seçin. Eylemler ekranında, **e-posta/Office 365 Outlook gönder**' i seçin.  

   ![Office365 seçenekleri](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Bağlantıyı kurmak için Office 365 hesabınızda oturum açın. Bu zaman aşımına uğrarsa, yeniden denemeniz yeterlidir. E-postaların alıcıları için e-posta adreslerini belirtin. Ayrıca konuyu da belirtin ve alıcının gövdede görmesini istediğiniz iletiyi yazın. Test amacıyla, alıcı olarak kendi e-posta adresinizi girin.

   Dahil etmek istediğiniz iletiden içerik göstermek için **dinamik Içerik Ekle** ' yi seçin. **İçerik** öğesini seçin; e-postadaki iletiyi içerecektir.

   ![Mantıksal uygulama için e-posta seçenekleri](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. **Kaydet**’i seçin. Sonra Logic App Tasarımcısı'nı kapatın.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics'i ayarlama

Verileri Power BI görselleştirmesinde görmek için, önce bir Stream Analytics işi ayarlayarak verileri alın. Yalnızca **düzeyi** **normal** olan iletilerin varsayılan uç noktaya gönderildiğini ve Power BI görselleştirmesi için Stream Analytics işi tarafından alınacağını unutmayın.

### <a name="create-the-stream-analytics-job"></a>Stream Analytics işi oluşturma

1. [Azure Portal](https://portal.azure.com) **bir kaynak oluştur** > **nesnelerin interneti** > **Stream Analytics işi**seçin.

2. İş için aşağıdaki bilgileri girin.

   **İş adı**: İşin adı. Adın genel olarak benzersiz olması gerekir. Bu öğreticide **contosoJob** kullanılır.

   **Abonelik**: öğretici Için kullandığınız Azure aboneliği.

   **Kaynak grubu**: IoT hub'ınız tarafından kullanılan kaynak grubunun aynısını kullanın. Bu öğreticide **ContosoResources** kullanılır.

   **Konum**: Kurulum betiğinde kullanılan konumun aynısını kullanın. Bu öğreticide **West US** kullanılır.

   ![Stream Analytics işini oluşturma](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. İşi oluşturmak için **Oluştur** ' u seçin. Dağıtım birkaç dakika sürebilir.

    İşe dönmek için **kaynak grupları**' nı seçin. Bu öğreticide **ContosoResources** kullanılır. Kaynak grubunu seçin ve ardından kaynak listesinden Stream Analytics işi seçin.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics işine giriş ekleme

1. **Iş topolojisi**altında **girişler**' i seçin.

2. **Girişler** bölmesinde **akış girişi Ekle** ' yi seçin ve IoT Hub ' ı seçin. Görüntülenen ekranda aşağıdaki alanları doldurun:

   **Giriş diğer adı**: Bu öğreticide **contosoinputs** kullanılır.

   **Aboneliğinizden IoT Hub seçin**: Bu radyo düğmesi seçeneğini belirleyin.

   **Abonelik**: Bu öğretici için kullanmakta olduğunuz Azure aboneliğini seçin.

   **IoT Hub**: IoT Hub 'ı seçin. Bu öğreticide **ContosoTestHub** kullanılır.

   **Uç nokta**: **Mesajlaşma**'yı seçin. (İşlem İzleme'yi seçerseniz, gönderdiğiniz veriler yerine IoT hub'ı hakkındaki telemetri verilerini alırsınız.) 

   **Paylaşılan erişim ilkesi adı**: **hizmeti**seçin. Paylaşılan Erişim İlkesi Anahtarı'nı portal sizin için doldurur.

   **Tüketici grubu**: Bu öğreticinin 1. bölümünde ayarlanan tüketici grubunu seçin. Bu öğreticide **contosoconsumers** kullanılır.
   
   Kalan alanlar için varsayılan değerleri kabul edin. 

   ![Stream Analytics işi için girişleri ayarlama](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. **Kaydet**’i seçin.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics işine çıkış ekleme

1. **Iş topolojisi**altında, **çıktılar**' i seçin.

2. **Çıktılar** bölmesinde **Ekle**' yi ve ardından **Power BI**' yi seçin. Görüntülenen ekranda aşağıdaki alanları doldurun:

   **Çıkış diğer adı**: Çıkışın benzersiz diğer adı. Bu öğreticide **contosooutputs** kullanılır. 

   **Veri kümesi adı**: Power BI'da kullanılacak veri kümesinin adı. Bu öğreticide **contosodataset** kullanılır. 

   **Tablo adı**: Power BI'da kullanılacak tablonun adı. Bu öğreticide **contosotable** kullanılır.

   Kalan alanlarda varsayılan değerleri kabul edin.

3. **Yetkilendir**' i seçin ve Power BI hesabınızda oturum açın. (Bu, birden çok denemeyi alabilir).

   ![Stream Analytics işi için çıkışları ayarlama](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. **Kaydet**’i seçin.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu yapılandırma

1. **İş Topolojisi**'nin altında **Sorgu**'yu seçin.

2. `[YourInputAlias]` değerini işin giriş diğer adıyla değiştirin. Bu öğreticide **contosoinputs** kullanılır.

3. `[YourOutputAlias]` değerini işin çıkış diğer adıyla değiştirin. Bu öğreticide **contosooutputs** kullanılır.

   ![Stream Analytics işi için sorgu ayarlama](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. **Kaydet**’i seçin.

5. Sorgu bölmesini kapatın. Kaynak grubundaki kaynakların görünümüne geri dönersiniz. Stream Analytics işi seçin. Bu öğreticide **contosoJob** olarak adlandırılmıştır.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

Stream Analytics işinde, **Şimdi** **Başlat >  > ** **Başlat** ' ı seçin. İş düzgün bir şekilde başlatıldıktan sonra, **Durduruldu** olan iş durumu **Çalışıyor** olarak değiştirilir.

Power BI raporunu ayarlamak için verilere ihtiyacınız vardır; bu nedenle, cihazı oluşturup cihaz simülasyon uygulamasını çalıştırdıktan sonra Power BI'ı ayarlarsınız.

## <a name="run-simulated-device-app"></a>Sanal cihaz uygulamasını Çalıştır

Bu öğreticinin 1. bölümünde, IoT cihazı kullanarak benzetim yapılacak bir cihaz ayarlarsınız. Bu bölümde, bu cihazın bir IoT Hub 'ına cihazdan buluta iletileri göndermesini sağlayan .NET konsol uygulamasını indirdiniz (uygulama ve kaynakları Bölüm 1 ' de henüz indirmediğiniz varsayılarak).

Bu uygulama, farklı ileti yönlendirme yöntemlerinin her biri için iletiler gönderir. Ayrıca, indirme sırasında, tüm Azure Resource Manager şablon ve parametre dosyalarının yanı sıra Azure CLı ve PowerShell betikleri içeren bir klasör de vardır.

Bu öğreticinin 1. bölümünde yer almayan dosyaları depodan indirmediyseniz, şimdi [IoT cihaz benzetimine](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)giderek indirin. Bu bağlantının seçilmesi, içindeki çeşitli uygulamalarla bir depoyu indirir; Aradığınız çözüm IoT-Hub/öğreticiler/yönlendirme/IoT_SimulatedDevice. sln ' dir. 

Kodu Visual Studio 'da açmak için çözüm dosyasına (IoT_SimulatedDevice. sln) çift tıklayın ve ardından Program.cs ' yi açın. `{your hub name}` değerini IoT hub'ı konak adıyla değiştirin. IoT hub'ı konak adı **{iot-hub-adı}.azure-devices.net** biçimindedid. bu öğreticide, hub konak adı olarak **ContosoTestHub.azure-devices.net** kullanılır. Ardından, `{your device key}` değerini daha önce simülasyon cihazını ayarlarken kaydettiğiniz cihaz anahtarıyla değiştirin. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Çalıştırma ve test etme

Konsol uygulamasını çalıştırın. Birkaç dakika bekleyin. Uygulamanın konsol ekranında iletilerin gönderildiğini görebilirsiniz.

Uygulama, IoT hub'ına her saniye yeni bir cihazdan buluta iletisi gönderir. İleti, cihaz kimliği, sıcaklık, nem düzeyi ve ileti düzeyi (varsayılan `normal` değeriyle) bilgileriyle bir JSON seri hale getirilmiş nesnesi içerir. Buna rastgele olarak `critical` veya `storage` düzeyi atanır; bu, iletinin depolama hesabına veya Service Bus kuyruğuna (Mantıksal Uygulamanızı e-posta göndermesi için tetikler) yönlendirilmesine neden olur. Varsayılan (`normal`) okumalar, bundan sonra ayarlayacağınız BI raporunda görüntülenir.

Her şey düzgün ayarlandıysa, bu noktada aşağıdaki sonuçları görüyor olmalısınız:

1. Kritik iletilerle ilgili e-postalar almaya başlarsınız.

   ![Elde edilen e-postalar](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Bu sonuç aşağıdaki deyimlerin doğru olduğu anlamına gelir. 

   * Service Bus kuyruğunda yönlendirme düzgün çalışıyor.
   * İletiyi Service Bus kuyruğundan alan Mantıksal Uygulama düzgün çalışıyor.
   * Mantıksal Uygulama'nın Outlook bağlayıcısı düzgün çalışıyor. 

2. [Azure Portal](https://portal.azure.com), **kaynak grupları** ' nı seçin ve kaynak grubunuzu seçin. Bu öğreticide **ContosoResources** kullanılır. 

    Depolama hesabını seçin, **kapsayıcılar**' ı seçin ve kapsayıcıyı seçin. Bu öğreticide **contosoresults** kullanılır. Bir klasör görüyor olmalısınız ve bir veya birden çok dosya görünceye kadar dizinlerde ayrıntıya gidebilirsiniz. Bu dosyalardan birini açın; depolama hesabına yönlendirilen girdileri içerir. 

   ![Depolama alanındaki sonuç dosyaları](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Bu sonuç, aşağıdaki deyimin doğru olduğu anlamına gelir.

   * Depolama hesabına yapılan yönlendirme düzgün çalışıyor.

Artık uygulama çalışmaya devam ettiğinden, varsayılan yönlendirmede gelen iletileri görmek için Power BI görselleştirmesini ayarlayın.

## <a name="set-up-the-power-bi-visualizations"></a>Power BI görselleştirmelerini ayarlama

1. [Power BI](https://powerbi.microsoft.com/) hesabınızda oturum açın.

2. **Çalışma Alanları**'na gidin ve Stream Analytics işi için çıkış oluştururken ayarladığını çalışma alanını seçin. Bu öğreticide **My Workspace** kullanılır. 

3. **Veri kümelerini**seçin. Herhangi bir veri kümeniz yoksa birkaç dakika bekleyip tekrar kontrol edin.

   Stream Analytics işi için çıkış oluştururken belirttiğiniz veri kümesinin listelendiğini görüyor olmalısınız. Bu öğreticide **contosodataset** kullanılır. (Veri kümesinin ilk kez görüntülenmesi 5-10 dakika kadar sürebilir.)

4. **Eylemler**' in altında, bir rapor oluşturmak için ilk simgeyi seçin.

   ![Eylemler ve rapor simgesiyle Power BI çalışma alanı vurgulandı](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Zamanla değişen gerçek zamanlı sıcaklığı göstermek için bir çizgi grafik oluşturun.

   * Rapor oluşturma sayfasında, çizgi grafik simgesini seçerek bir çizgi grafik ekleyin.

     ![Görselleştirmeler ve alanlar](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * **Alanlar** bölmesinde, Stream Analytics işi için çıkış oluştururken belirttiğiniz tabloyu genişletin. Bu öğreticide **contosotable** kullanılır.

   * **Görselleştirmeler** bölmesinde **EventEnqueuedUtcTime** öğesini **Eksen** üzerine sürükleyin.

   * **temperature** öğesini **Değerler** üzerine sürükleyin.

   Bir çizgi grafik oluşturulur. Grafiğin x ekseninde UTC saat dilimine göre tarih ve saat görüntülenir. Grafiğin y ekseninde algılayıcıdan alınan sıcaklık görüntülenir.

6. Zamanla değişen gerçek zamanlı nem oranını göstermek için bir çizgi grafik daha oluşturun. İkinci grafiği ayarlamak için, ilk grafik için aynı süreci izleyin ve x ekseninde **Eventenqueuedutctime** ve y**ekseni (** değerler) hakkında **nem** **değeri**koyarak.

   ![İki grafik içeren son Power BI raporu](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Raporu kaydetmek için **Kaydet** ' i seçin, istenirse rapor için bir ad girersiniz.

Her iki grafikteki verileri de görüyor olmalısınız. Bu sonuç aşağıdaki deyimlerin doğru olduğu anlamına gelir:

   * Varsayılan uç noktaya yapılan yönlendirme düzgün çalışıyor.
   * Azure Stream Analytics işi doğru akış yapıyor.
   * Power BI Görselleştirmesi doğru ayarlanmış.

Power BI penceresinin en üstündeki Yenile düğmesini seçerek en son verileri görmek için grafikleri yenileyebilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bu öğreticinin her iki bölümünde da oluşturduğunuz tüm Azure kaynaklarını kaldırmak istiyorsanız, kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda IoT hub'ını, Service Bus ad alanıyla kuyruğunu, Mantıksal Uygulamayı, depolama hesabını ve kaynak grubunun kendisini kaldırır. Ayrıca, Power BI kaynaklarını kaldırabilir ve öğretici sırasında gönderilen e-postaları temizleyebilirsiniz.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Power BI görselleştirmesinde kaynakları temizleme

[Power BI](https://powerbi.microsoft.com/) hesabınızda oturum açın. Çalışma alanınıza gidin. Bu öğreticide **My Workspace** kullanılır. Power BI görselleştirmesini kaldırmak için veri kümeleri ' ne gidin ve veri kümesini silmek için çöp kutusu simgesini seçin. Bu öğreticide **contosodataset** kullanılır. Veri kümesini kaldırdığınızda, rapor da kaldırılır.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLı kullanarak kaynakları Temizleme

Kaynak grubunu kaldırmak için [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu kullanın. `$resourceGroup`, Bu öğreticinin başlangıcında **Contosoresources** olarak ayarlanmıştır.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Kaynakları temizlemek için PowerShell 'i kullanma

Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın. `$resourceGroup`, Bu öğreticinin başlangıcında **Contosoresources** olarak ayarlanmıştır.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Sınama e-postalarını temizle

Ayrıca, gelen kutunuzda, cihaz uygulaması çalışırken mantıksal uygulama aracılığıyla oluşturulan e-posta miktarını da silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu 2 parçalı öğreticide, aşağıdaki görevleri gerçekleştirerek farklı hedeflere IoT Hub iletileri yönlendirmek üzere ileti yönlendirmeyi nasıl kullanacağınızı öğrendiniz.  

**Bölüm ı: kaynak oluşturma, ileti yönlendirmeyi ayarlama**
> [!div class="checklist"]
> * Kaynakları oluşturma--bir IoT Hub, bir depolama hesabı, Service Bus kuyruğu ve sanal cihaz.
> * Depolama hesabı ve Service Bus kuyruğu için IoT Hub uç noktaları ve ileti yollarını yapılandırın.

**Bölüm II: hub 'a ileti gönderme, yönlendirilmiş sonuçları görüntüleme**
> [!div class="checklist"]
> * Service Bus kuyruğuna ileti eklendiğinde tetiklenen ve e-posta gönderen bir Mantıksal Uygulama oluşturma.
> * Farklı yönlendirme seçenekleri için hub'a iletiler gönderen bir IoT Cihazının simülasyonu olan bir uygulama indirme ve çalıştırma.
> * Varsayılan uç noktaya gönderilen veriler için bir Power BI görselleştirmesi oluşturun.
> * Service Bus kuyruğunda ve e-postalarda ...
> * Depolama hesabında ...
> * PowerBI görselleştirmesinde ...
> * ...sonuçları görüntüleyin.

IoT cihazı durumunun nasıl yönetileceğini öğrenmek için sonraki öğreticiye geçin. 

> [!div class="nextstepaction"]
> [IoT Hub ölçümleri ve tanılamayı ayarlama ve kullanma](tutorial-use-metrics-and-diags.md)
