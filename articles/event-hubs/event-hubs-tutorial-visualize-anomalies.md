---
title: 'Öğretici: gerçek zamanlı olaylarda veri anormalilerini görselleştirme-Azure Event Hubs'
description: 'Öğretici: Microsoft Azure gönderilen gerçek zamanlı olaylarda veri bozuklukilerini görselleştirin Event Hubs'
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.author: shvija
ms.topic: tutorial
ms.service: event-hubs
ms.custom: seodec18
ms.date: 11/05/2019
ms.openlocfilehash: 0cfff0196ebc20b9b01bc966b3590470d349e86e
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718002"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Öğretici: Azure Event Hubs'a gönderilen gerçek zamanlı olaylardaki veri anomalilerini görselleştirme

Azure Event Hubs ile Azure Stream Analytics'i kullanarak gelen verileri denetleyebilir, anomalileri ayırabilir ve Power BI'da görselleştirebilirsiniz. Bir olay hub'ına sürekli olarak gerçek zamanlı veriler göndererek saniyede milyonlarca olay ekleyen binlerce cihaza sahip olduğunuzu düşünelim. Bu kadar fazla verideki anomalileri veya hataları nasıl denetleyebilirsiniz? Örneğin, cihazlar kredi kartı işlemleri gönderiyor ve 5 saniyelik bir zaman aralığı içinde birden çok ülkede/bölgede birden çok işlem olması gereken her yerde yakalamanız gerekiyorsa ne yapmalı? Bu durum birisi kredi kartı bilgilerini çalıp dünyanın farklı yerlerinden aynı anda alışveriş yapmak için kullandığında ortaya çıkabilir. 

Bu öğreticide bu örneğin simülasyonunu yapacaksınız. Kredi kartı işlemleri oluşturup bunları bir olay hub'ına gönderen bir uygulama çalıştıracaksınız. Ardından Azure Stream Analytics ile veri akışını gerçek zamanlı olarak okuyacak, burada geçerli işlemlerle geçersiz işlemleri ayıracak ve Power BI'ı kullanarak geçersiz olarak etiketlenmiş olan işlemlerin görsel olarak tanımlanmasını sağlayacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Olay hub’ı oluşturma
> * Kredi kartı işlemleri gönderen uygulamayı çalıştırma
> * Bu verileri işlemek için bir Stream Analytics işi yapılandırma
> * Sonuçları göstermek için bir Power BI görselleştirmesi yapılandırma

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun][].

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- [Visual Studio](https://www.visualstudio.com/)’yu yükleyin. 
- Stream Analytics işinin çıktısını analiz edebilmek için bir Power BI hesabınızın olması gerekir. [Power BI'ı ücretsiz deneyebilirsiniz](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="set-up-resources"></a>Kaynakları ayarlama

Bu öğretici için bir Event Hubs ad alanına ve bir olay hub’ına ihtiyacınız olacak. Bu kaynakları Azure CLI veya Azure PowerShell kullanarak oluşturabilirsiniz. Tüm kaynaklar için aynı kaynak grubunu ve konumunu kullanın. Sonunda kaynak grubunu silerek her şeyi tek adımda kaldırabilirsiniz.

Aşağıdaki bölümlerde bu gerekli adımların nasıl uygulanacağı açıklanır. CLI *veya* the PowerShell yönergelerini uygulayarak aşağıdaki adımları gerçekleştirin:

1. Bir [kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. 

2. Bir Event Hubs ad alanı oluşturun. 

3. Bir olay hub'ı oluşturun.

> [!NOTE]
> Her betikte öğreticinin ilerleyen bölümlerinde ihtiyacınız olacak değişkenler bulunur. Bunlar kaynak grubu adı ($resourceGroup), olay hub'ı ad alanı ( **$eventHubNamespace**) ve olay hub'ı adıdır ( **$eventHubName**). Bunlar makalenin ilerleyen bölümlerinde dolar işareti ($) ön ekiyle belirtildiğinden betikte ayarlanmış olduğunu anlayabilirsiniz.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Azure CLI kullanarak kaynaklarınızı ayarlama

Bu betiği kopyalayıp Cloud Shell'e yapıştırın. Zaten oturum açmış olduğunuzu varsayarak, betiği bir kerede bir satır olmak üzere çalıştırır.

Genel olarak benzersiz olması gereken değişkenlere `$RANDOM` eklenmiştir. Betik çalıştırıldığında ve değişkenler ayarlandığında, rastgele bir sayısal dize oluşturulur ve sabit dizenin sonuna eklenerek benzersiz hale getirilmesi sağlanır.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Azure PowerShell kullanarak kaynaklarınızı ayarlama

Bu betiği kopyalayıp Cloud Shell'e yapıştırın. Zaten oturum açmış olduğunuzu varsayarak, betiği bir kerede bir satır olmak üzere çalıştırır.

Genel olarak benzersiz olması gereken değişkenlere `$(Get-Random)` eklenmiştir. Betik çalıştırıldığında ve değişkenler ayarlandığında, rastgele bir sayısal dize oluşturulur ve sabit dizenin sonuna eklenerek benzersiz hale getirilmesi sağlanır.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Test amaçlı olay verilerini üretmek için uygulamayı çalıştırma

[GitHub üzerindeki Event Hubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) arasında test verilerini üretecek bir [anomali algılama uygulaması](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/AnomalyDetector) bulunur. Kredi kartı kullanım simülasyonu yapan bu uygulama olay hub'ına kredi kartı işlemlerini yazar ve arada anomali olarak etiketlenmeleri için birden fazla konumda aynı kredi kartına ait işlemler yazar. Bu uygulamayı çalıştırmak için aşağıdaki adımları uygulayın: 

1. GitHub'dan [Azure Event Hubs örneklerini](https://github.com/Azure/azure-event-hubs/archive/master.zip) indirin ve dosyaları ayıklayın.

2. \azure-event-hubs-master\samples\DotNet\AnomalyDetector\ klasörüne gidin ve AnomalyDetector.sln dosyasına çift tıklayarak çözümü Visual Studio'da açın. 

3. Program.cs dosyasını açın ve **Event Hubs connection string** yerine betiği çalıştırırken kaydettiğiniz bağlantı dizesini yazın. 

4. **Event Hub name** yerine olay hub'ınızın adını yazın. Uygulamayı çalıştırmak için F5'e basın. Olay hub'ınıza olay göndermeye başlar ve 1000 olaya ulaşana kadar göndermeye devam eder. Veri almak için uygulamanın çalışır durumda olmasını gerektiren birkaç durum vardır. Bu durumlar gerekli noktalarda aşağıdaki yönergelerde belirtilmiştir.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics'i ayarlama

Artık olay hub'ınıza veri akışını başlatabilirsiniz. Bu verileri bir Power BI görselleştirmesinde kullanmak için verileri alan ve ardından Power BI görselleştirmesine ileten bir Stream Analytics işi ayarlamakla başlayın.

### <a name="create-the-stream-analytics-job"></a>Stream Analytics işi oluşturma

1. Azure portalında **Kaynak oluştur**’a tıklayın. Arama kutusuna **stream analytics** yazın ve **Enter** tuşuna basın. **Stream Analytics İşi**'ni seçin. Stream Analytics işi bölmesinde **Oluştur**'a tıklayın. 

2. İş için aşağıdaki bilgileri girin:

   **İş adı**: **contosoEHjob** yazın. Bu alan işin adıdır ve genel olarak benzersiz olmalıdır.

   **Abonelik**: Aboneliğinizi seçin.

   **Kaynak grubu**: Olay hub'ınız (**ContosoResourcesEH**) tarafından kullanılan kaynak grubunun aynısını kullanın.

   **Konum**: Kurulum betiğinde kullanılan konumun (**Batı ABD**) aynısını kullanın.

   ![Yeni bir Azure Stream Analytics işi oluşturma işleminin gösterildiği ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Kalan alanlarda varsayılan değerleri kabul edin. **Oluştur**'a tıklayın. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics işine giriş ekleme

Portalın **Stream Analytics İşi** bölmesinde değilseniz portalda **Kaynak Grupları**'na tıklayıp kaynak grubunuzu (**ContosoResourcesEH**) seçerek Stream Analytics işinize ulaşabilirsiniz. Bu eylem gruptaki tüm kaynakları gösterir ve bu listeden Stream Analytics işinizi seçebilirsiniz. 

Steam Analytics işinin girişleri, olay hub'ından gelen kredi kartı işlemleridir.

> [!NOTE]
> Dolar işaretiyle ($) başlayan değişkenlerin değerleri önceki bölümlerde başlangıç betiklerinde ayarlanmıştır. Bu alanları belirtirken aynı Event Hubs ad alanı ve olay hub'ı adı değerlerini kullanmanız gerekir.

1. **İş Topolojisi**'nin altında **Girişler**'e tıklayın.

2. **Girişler** bölmesinde **Akış girişi ekle**'ye tıklayın ve Event Hubs'ı seçin. Açılan ekranda aşağıdaki alanları doldurun:

   **Giriş diğer adı**: **contosoinputs** yazın. Bu alan, veri sorgusu tanımlanırken kullanılan giriş akışının adıdır.

   **Abonelik**: Aboneliğinizi seçin.

   **Event Hubs ad alanı**: Event Hub ad alanınızı ($**eventHubNamespace**) seçin. 

   **Olay Hub'ı adı**: **Var olanı kullan**'a tıklayıp olay hub'ınızı ($**eventHubName**) seçin.

   **Event Hubs ilkesi adı**: **RootManageSharedAccessKey**'i seçin.

   **Event Hubs tüketici grubu**: Varsayılan tüketici grubunu kullanmak için bu alanı boş bırakın.

   Kalan alanlarda varsayılan değerleri kabul edin.

   ![Stream Analytics işine giriş akışı ekleme adımlarını gösteren ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. **Kaydet** düğmesine tıklayın.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics işine çıkış ekleme

1. **İş Topolojisi**'nin altında **Çıkışlar**'a tıklayın. Bu alan, veri sorgusu tanımlanırken kullanılan çıkış akışının adıdır.

2. **Çıkışlar** bölmesinde **Ekle**'ye tıklayın ve **Power BI**'ı seçin. Açılan ekranda aşağıdaki alanları doldurun:

   **Çıkış diğer adı**: **contosooutputs** yazın. Bu alan çıkışın benzersiz diğer adıdır. 

   **Veri kümesi adı**: **contosoehdataset** yazın. Bu alan Power BI'da kullanılacak veri kümesinin adıdır. 

   **Tablo adı**: **contosoehtable** yazın. Bu alan Power BI'da kullanılacak tablonun adıdır. 

   Kalan alanlarda varsayılan değerleri kabul edin.

   ![Stream Analytics işi için çıkışın ayarlanmasını gösteren ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. **Yetkilendir**'e tıklayın ve Power BI hesabınızda oturum açın.

4. Kalan alanlarda varsayılan değerleri kabul edin.

5. **Kaydet** düğmesine tıklayın.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu yapılandırma

Bu sorgu, Power BI görselleştirmesine gönderilecek verilerin alınması için kullanılır. İşi ayarlarken tanımladığınız **contosoinputs** ve **contosooutputs** adlarını kullanır. Bu sorgu sahte görünen kredi kartı işlemlerini alır. Bu işlemler aynı kredi kartı numarasının beş saniyelik aralıklarla farklı konumlarda birden fazla işlemde kullanıldığı durumlardır.

1. **İş Topolojisi**'nin altında **Sorgu**'ya tıklayın.

2. Sorguyu aşağıdakiyle değiştirin: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. **Kaydet** düğmesine tıklayın.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu test etme 

1. Anomali Algılama uygulamasını çalıştırarak testi ayarlama ve çalıştırma sırasında olay hub'ına veri göndermesini sağlayın. 

2. Sorgu bölmesinde **contosoinputs** girişinin yanındaki noktaları seçin ve sonra **Girişten alınan örnek veriler** seçeneğini belirleyin.

3. Üç dakikalık veri istediğinizi belirtin ve **Tamam**'a tıklayın. Veri örneğinin alındığını belirten bildirim gelene kadar bekleyin.

4. **Test**'e tıklayın ve sonuçların geldiğinden emin olun. Sonuçlar sorgunun hemen altındaki bölmenin **Sonuçlar** bölmesinde görüntülenir.

5. Sorgu bölmesini kapatın.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

Stream Analytics işinde **Çalıştır**'a, **Şimdi**'ye ve ardından **Çalıştır**'a tıklayın. İş düzgün bir şekilde başlatıldıktan sonra, **Durduruldu** olan iş durumu **Çalışıyor** olarak değiştirilir.

## <a name="set-up-the-power-bi-visualizations"></a>Power BI görselleştirmelerini ayarlama

1. Anomali Algılama uygulamasını çalıştırarak Power BI görselleştirmesini ayarlama sırasında olay hub'ına veri göndermesini sağlayın. Her seferinde yalnızca 1000 işlem ürettiğinden birden fazla kez çalıştırmanız gerekebilir.

2. [Power BI](https://powerbi.microsoft.com/) hesabınızda oturum açın.

3. **Çalışma Alanım**'a gidin.

4. **Veri kümeleri**'ne tıklayın.

   Stream Analytics işi için çıkış oluştururken belirttiğiniz veri kümesini (**contosoehdataset**) görüyor olmalısınız. Veri kümesinin ilk kez görüntülenmesi 5-10 dakika kadar sürebilir.

5. **Panolar**'a ve ardından **Oluştur**'a tıklayıp **Pano**'yu seçin.

   ![Panolar ve Oluştur düğmelerinin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Panonun adını belirtip **Oluştur**'a tıklayın. **Kredi Kartı Anomalileri** yazın.

   ![Pano adı belirtme işleminin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Pano sayfasında **Kutucuk ekle**'ye tıklayın, **GERÇEK ZAMANLI VERİ** bölümünden **Özel Akış Verileri**'ni seçin ve **İleri**'ye tıklayın.

   ![Kutucuk için kaynak belirtme işleminin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Veri kümenizi (**contosoehdataset**) seçin ve **İleri**'ye tıklayın.

   ![Veri kümesini belirtme işleminin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Görselleştirme türü olarak **Kart** seçin. **Alanlar** bölümünde **Değer ekle**'ye tıklayıp **fraudulentuses** öğesini seçin.

   ![Görselleştirme türü ve alan belirtme işleminin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   **İleri**’ye tıklayın.

10. Başlığı **Sahtekarlık amaçlı kullanımlar**, alt başlığı da **Son birkaç dakikadaki toplam veriler** yapın. **Apply (Uygula)** düğmesine tıklayın. Kutucuk, panonuza kaydedilir.

    ![Pano kutucuğu için başlık ve alt başlık belirtme işleminin ekran görüntüsü.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Örnek uygulamayı ve veri akışını Olay Hub 'ına çalıştırdığınızda, bu kutucukta bulunan sayı hızlı bir şekilde değişir (her saniye). Bunun nedeni, Stream Analytics sorgusunun değeri **her saniye**güncelleyen şeydir. Son birkaç dakika içinde toplamı görmek için sorguyu 3 dakikalık bir pencereye güncelleştirin. 
11. Başka bir görselleştirme ekleyin. İlk birkaç adımı tekrarlayın:

    * **Kutucuk Ekle**'ye tıklayın.
    * **Özel Akış Verileri**'ni seçin. 
    * **İleri**’ye tıklayın.
    * Veri kümenizi seçin ve **İleri**'ye tıklayın. 

12. **Görselleştirme Türü** olarak **Çizgi grafik** türünü seçin.

13. **Eksen** bölümünde **Değer Ekle**'ye tıklayıp **windowend** değerini seçin. 

14. **Değerler** bölümünde **Değer ekle**'ye tıklayıp **fraudulentuses** değerini seçin.

15. **Görüntülenecek zaman penceresi** için son beş dakikayı seçin. **İleri**’ye tıklayın.

16. Başlık olarak **Zaman içindeki sahtekarlık amaçlı kullanımları göster** yazın, alt başlığı boş bırakın ve **Uygula**'ya tıklayın. Panonuz açılır.

17. Anomali Algılama uygulamasını tekrar çalıştırarak olay hub'ına veri göndermesini sağlayın. Verileri analiz ettiğinden **Sahtekarlık amaçlı kullanımlar** kutucuğunun değiştiğini ve çizgi grafikte verilerin gösterildiğini göreceksiniz. 

    ![Power BI sonuçlarını gösteren ekran görüntüsü](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz tüm kaynakları kaldırmak istiyorsanız, Power BI görselleştirme verilerini kaldırın ve ardından kaynak grubunu silin. Kaynak grubunu sildiğinizde o grupta bulunan tüm kaynaklar da silinir. Bu örnekte olay hub'ını, Event Hub ad alanını, Stream Analytics işini ve kaynak grubunun kendisini kaldırır. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Power BI görselleştirmesinde kaynakları temizleme

Power BI hesabınızda oturum açın. **Çalışma Alanım**'a gidin. Pano adınızın bulunduğu satırdaki çöp kutusu simgesine tıklayın. Daha sonra **Veri Kümeleri**'ne gidin ve çöp kutusu simgesine tıklayarak veri kümesini (**contosoehdataset**) silin.

### <a name="clean-up-resources-using-azure-cli"></a>Azure CLI kullanarak kaynakları temizleme

Kaynak grubunu kaldırmak için [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu kullanın.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>PowerShell kullanarak kaynakları temizleme

Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Olay hub’ı oluşturma
> * Olay simülasyonu gerçekleştiren sonuçları olay hub'ına gönderen uygulamayı çalıştırma
> * Hub'a gönderilen olayları işlemek için bir Stream Analytics işi yapılandırma
> * Sonuçları göstermek için bir Power BI görselleştirmesi yapılandırma

Azure Event Hubs hakkında daha fazla bilgi edinmek için bir sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [.NET Standard'da Azure Event Hubs'a ileti göndermeye başlama](event-hubs-dotnet-standard-getstarted-send.md)

[ücretsiz bir hesap oluşturun]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
