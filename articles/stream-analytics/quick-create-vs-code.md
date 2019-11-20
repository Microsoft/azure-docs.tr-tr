---
title: Visual Studio Code Azure Stream Analytics bir bulut işi oluşturun
description: Bu hızlı başlangıçta, bir Stream Analytics işi oluşturarak, girdileri, çıktıları yapılandırarak ve Visual Studio Code bir sorgu tanımlayarak nasıl başlacağınız gösterilmektedir.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 35e2711bbb3cd6dc0662146a566014dd65d879bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935096"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Hızlı başlangıç: Visual Studio Code Azure Stream Analytics bulut işi oluşturma (Önizleme)

Bu hızlı başlangıçta, Visual Studio Code için Azure Stream Analytics uzantısı kullanılarak bir Stream Analytics işi oluşturma ve çalıştırma işlemi gösterilmektedir. Örnek iş, bir IoT Hub cihazından akış verilerini okur. 27 ° üzerinde ortalama sıcaklığı hesaplayan ve sonuç çıktı olaylarını BLOB depolama alanındaki yeni bir dosyaya yazan bir iş tanımlarsınız.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* [Azure Portal](https://portal.azure.com/)’ında oturum açın.

* [Visual Studio Code](https://code.visualstudio.com/)'i yükler.

## <a name="install-the-azure-stream-analytics-extension"></a>Azure Stream Analytics uzantısını yükler

1. Visual Studio Code'u açın.

2. Sol bölmedeki **uzantılardan** **Stream Analytics** arayın ve **Azure Stream Analytics** uzantısı üzerine **Install** ' u seçin.

3. Uzantı yüklendikten sonra, **Azure Stream Analytics araçlarının** **etkin uzantılarınızın**görünür olduğunu doğrulayın.

   ![Visual Studio Code Azure Stream Analytics araçları etkinleştirilmiş Uzantılar altında](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Azure Stream Analytics uzantısını etkinleştirin

1. VS Code etkinlik çubuğunda **Azure** simgesini seçin. **Stream Analytics** yan çubukta görünür olur. **Stream Analytics**altında **Azure 'da oturum aç**' ı seçin. 

   ![Visual Studio Code 'de Azure 'da oturum açın](./media/quick-create-vs-code/azure-sign-in.png)

2. Oturum açtığınızda, Azure hesabınızın adı VS Code penceresinin sol alt köşesindeki durum çubuğunda görüntülenir.

> [!NOTE]
> Azure Stream Analytics araçlar bir sonraki oturum kapatma sırasında otomatik olarak oturum açacaktır. Hesabınızda iki öğeli kimlik doğrulaması varsa, PIN kullanmak yerine telefon kimlik doğrulaması kullanmanız önerilir.
> Kaynakları listelerken sorun yaşıyorsanız, oturumu kapatıp yeniden oturum açmak genellikle yardımcı olur. Oturumu kapatmak için `Azure: Sign Out`komutunu girin.

## <a name="prepare-the-input-data"></a>Girdi verilerini hazırlama

Stream Analytics işini tanımlamadan önce, daha sonra iş girişi olarak yapılandırılmış olan verileri hazırlamanız gerekir. İş için gereken giriş verilerini hazırlamak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

2. **Kaynak oluştur** > **Nesnelerin İnterneti** > **Iot Hub** seçeneğini belirleyin.

3. **IoT Hub** bölmesinde aşağıdaki bilgileri girin:
   
   |**Ayar**  |**Önerilen değer**  |**Açıklama**  |
   |---------|---------|---------|
   |Abonelik  | \<Aboneliğiniz\> |  Kullanmak istediğiniz Azure aboneliğini seçin. |
   |Kaynak grubu   |   asaquickstart-resourcegroup  |   **Yeni Oluştur**’u seçin ve hesabınız için yeni bir kaynak grubu adı girin. |
   |Bölge  |  \<Kullanıcılarınıza en yakın bölgeyi seçin\> | IoT Hub barındırabileceğiniz coğrafi bir konum seçin. Kullanıcılarınıza en yakın konumu kullanın. |
   |IoT Hub adı  | MyASAIoTHub  |   IoT Hub için bir ad seçin.   |

   ![IoT Hub'ı oluşturma](./media/quick-create-vs-code/create-iot-hub.png)

4. **İleri ' yi seçin: boyut ve ölçek ayarla**.

5. **Fiyatlandırma ve ölçek katmanınızı** seçin. Bu hızlı başlangıçta, aboneliğinizde hala kullanılabiliyorsa **F1-ücretsiz** katmanını seçin. Ücretsiz katman kullanılamıyorsa, kullanılabilir en düşük katmanı seçin. Daha fazla bilgi için bkz. [IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![IoT Hub boyutlandırın ve ölçeklendirin](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. **İncele ve oluştur**’u seçin. IoT Hub bilgilerinizi gözden geçirin ve **Oluştur**' a tıklayın. IoT Hub oluşturulması birkaç dakika sürebilir. İlerleme durumunu **Bildirimler** bölmesinden izleyebilirsiniz.

7. IoT Hub gezinti menüsünde **IoT cihazları**altında **Ekle** ' ye tıklayın. Bir **CIHAZ kimliği** ekleyin ve **Kaydet**' e tıklayın.

   ![IoT Hub bir cihaz ekleyin](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Cihaz oluşturulduktan sonra **IoT cihazları** listesinden cihazı açın. **Bağlantı dizesini kopyalayın--birincil anahtar** ve daha sonra kullanmak için bir not defteri 'ne kaydedin.

   ![IoT Hub cihaz bağlantı dizesini Kopyala](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>BLOB depolama oluştur

1. Azure portalının sol üst köşesinden **Kaynak oluştur** > **Depolama** > **Depolama hesabı**’nı seçin.

2. **Depolama hesabı oluştur** bölmesinde, bir depolama hesabı adı, konum ve kaynak grubu girin. Oluşturduğunuz IoT Hub aynı konum ve kaynak grubunu seçin. Ardından hesabı oluşturmak için **gözden geçir + oluştur** ' a tıklayın.

   ![Depolama hesabı oluştur](./media/quick-create-vs-code/create-storage-account.png)

3. Depolama Hesabınız oluşturulduktan sonra **genel bakış** bölmesinde **Bloblar** kutucuğunu seçin.

   ![Depolama hesabına genel bakış](./media/quick-create-vs-code/blob-storage.png)

4. **BLOB hizmeti** sayfasından **kapsayıcı** ' yı seçin ve Kapsayıcınız için *kapsayıcı1*gibi bir ad sağlayın. **Ortak erişim düzeyini** **özel (anonim erişim yok)** olarak bırakın ve **Tamam**' ı seçin.

   ![Blob kapsayıcısı oluşturma](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics projesi oluşturma

1. Visual Studio Code ' de, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın. Ardından **asa** yazın ve **asa: yeni proje oluştur**' u seçin.

   ![Yeni proje oluşturma](./media/quick-create-vs-code/create-new-project.png)

2. **MyASAproj** gibi proje adınızı girin ve projeniz için bir klasör seçin.

    ![Proje adı oluştur](./media/quick-create-vs-code/create-project-name.png)

3. Yeni proje çalışma alanınıza eklenecektir. Bir ASA projesi sorgu betiği **(*. aşama QL)** , bir **jobconfig. JSON** dosyası ve bir **asaproj. JSON** yapılandırma dosyasından oluşur.

   ![VS Code içinde proje dosyaları Stream Analytics](./media/quick-create-vs-code/asa-project-files.png)

4. **Asaproj. JSON** yapılandırma dosyası Stream Analytics işini Azure 'a göndermek için gereken girdileri, çıkışları ve iş yapılandırma dosyası bilgilerini içerir.

   ![VS Code Stream Analytics iş yapılandırma dosyası](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Komut paletinden giriş ve çıkış eklerken, ilgili yollar otomatik olarak **asaproj. JSON** ' a eklenir. Doğrudan disk üzerinde giriş veya çıkış ekler veya kaldırırsanız, bu uygulamaları **asaproj. JSON**' dan el ile eklemeniz veya kaldırmanız gerekir. Giriş ve çıkışları tek bir yere yerleştirmeyi ve sonra her bir **asaproj. JSON**içindeki yolları belirterek farklı işlere başvurmalarını seçebilirsiniz.

## <a name="define-the-transformation-query"></a>Dönüşüm sorgusunu tanımlama

1. Proje klasörünüzdeki **myASAproj. asaql** ' i açın.

2. Aşağıdaki sorguyu ekleyin:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Örnek verilerle test etme
Sorguyu bulutta çalıştırmadan önce sorgu mantığını doğrulamak için sorgunuzu yerel örnek verilerle test edebilirsiniz.

Daha fazla ayrıntı için [örnek verilerle test](vscode-local-run.md) içindeki yönergeleri izleyin. 

 ![VS Code örnek verilerle test etme](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Giriş tanımlama

1. **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın ve **asa: giriş Ekle**' yi girin.

   ![VS Code Stream Analytics girişi ekleme](./media/quick-create-vs-code/add-input.png)

2. Giriş türü için **IoT Hub** seçin.

   ![Giriş seçeneği olarak IoT Hub seçin](./media/quick-create-vs-code/iot-hub.png)

3. Girişi kullanacak ASA sorgu betiğini seçin. Otomatik olarak **myASAproj. aşama QL**dosya yoluyla doldurulur.

   ![Visual Studio Code bir ASA betiği seçin](./media/quick-create-vs-code/asa-script.png)

4. Giriş dosyası adını **ıothub**olarak girin.

5. **Iothub. JSON** öğesini aşağıdaki değerlerle düzenleyin. Aşağıda belirtilmeyen alanlar için varsayılan değerleri tutun. CodeLens 'i kullanarak bir dize girmenize, açılan listeden seçim yapmanıza veya metnin doğrudan dosyada değiştirilmesini sağlayabilirsiniz.

   |Ayar|Önerilen değer|Açıklama|
   |-------|---------------|-----------|
   |Adı|Girdi|İşin girdisini tanımlamak için bir ad girin.|
   |IotHubNamespace|MyASAIoTHub|IoT Hub adını seçin veya girin. IoT Hub adları, aynı abonelikte oluşturulduklarında otomatik olarak algılanır.|
   |sharedAccessPolicyName|iothubowner| |

   ![Visual Studio Code girişi yapılandırma](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Çıkış tanımlama

1. Komut paletini açmak için **CTRL + SHIFT + P** ' yi seçin. Ardından **asa: çıkış Ekle**yazın.

   ![VS Code Stream Analytics çıkış Ekle](./media/quick-create-vs-code/add-output.png)

2. Havuz türü için **BLOB depolama alanını** seçin.

3. Bu girişi kullanacak ASA sorgu betiğini seçin.

4. Çıkış dosyası adını **Blobstorage**olarak girin.

5. **Blobstorage** 'ı aşağıdaki değerlerle düzenleyin. Aşağıda belirtilmeyen alanlar için varsayılan değerleri tutun. Bir açılan listeden seçim yapmanıza yardımcı olması için CodeLens kullanın veya bir dize girin. 

   |Ayar|Önerilen değer|Açıklama|
   |-------|---------------|-----------|
   |Adı|Çıktı| İşin çıkışını tanımlamak için bir ad girin.|
   |Depolama Hesabı|asaquickstartstorage|Depolama hesabınızın adını seçin veya girin. Depolama hesabı adları aynı abonelikte oluşturulursa otomatik olarak algılanır.|
   |Kapsayıcı|kapsayıcı1|Depolama hesabınızda oluşturduğunuz mevcut kapsayıcıyı seçin.|
   |Yol Deseni|çıkış|Kapsayıcıda oluşturulacak dosya yolunun adını girin.|

 ![Visual Studio Code çıktıyı yapılandırma](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Betiği derle

Betik derlemesi iki şey yapar: sözdizimini denetleyin ve oto dağıtımı için Azure Resource Manager şablonları oluşturun.

Betik derlemesini tetiklemenin iki yolu vardır:

1. Çalışma alanından betiği seçin ve ardından komut paletinden derlemeyi tetikleyin. 

   ![Betiği derlemek için VS Code komut paletini kullanın](./media/quick-create-vs-code/compile-script1.png)

2. Betiğe sağ tıklayıp **asa: betiği derle**' yi seçin.

    ![Derlemek için ASA betiğine sağ tıklayın](./media/quick-create-vs-code/compile-script2.png)

3. Derlemeden sonra, projenizin **Deploy** klasöründe oluşturulan iki Azure Resource Manager şablonu bulabilirsiniz. Bu iki dosya, oto dağıtımı için kullanılır.

    ![Dosya Gezgini 'nde dağıtım şablonlarını Stream Analytics](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Stream Analytics işini Azure 'a gönderme

1. Visual Studio Code betik Düzenleyicisi penceresinde **aboneliklerinizden Seç**' i seçin.

   ![Betik düzenleyicisinde abonelik metinlerinizden seçim yapın](./media/quick-create-vs-code/select-subscription.png)

2. Açılır listeden aboneliğinizi seçin.

3. Bir iş seçin * *. Sonra yeni iş oluştur ' u seçin.

4. İş adınızı girin, **myASAjob** ve ardından kaynak grubunu ve konumunu seçmek için yönergeleri izleyin.

5. **Azure 'A gönder**' i seçin. Günlükler çıkış penceresinde bulunabilir. 

6. İşiniz oluşturulduğunda, **Stream Analytics Gezgini**'nde görebilirsiniz.

![Stream Analytics Explorer 'da liste işi](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>IoT simülatörü çalıştırma

1. Yeni bir tarayıcı sekmesinde veya penceresinde [Raspberry PI Azure IoT çevrimiçi simülatörünü](https://azure-samples.github.io/raspberry-pi-web-simulator/) açın.

2. 15. satırdaki yer tutucusunu, önceki bölümde kaydettiğiniz Azure IoT Hub cihaz bağlantı dizesiyle değiştirin.

3. **Çalıştır**’a tıklayın. Çıktıda, IoT Hub gönderilen algılayıcı verileri ve iletileri gösterilmelidir.

   ![Raspberry PI Azure IoT Çevrimiçi simülatör](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics işini başlatıp çıktıyı denetleyin

1. Visual Studio Code **Stream Analytics Gezginini** açın ve işinizi bulun, **myASAJob**.

2. İş adına sağ tıklayın. Ardından bağlam menüsünden **Başlat** ' ı seçin.

   ![VS Code Stream Analytics işi Başlat](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. İşi başlatmak için açılır pencerede **Şimdi** öğesini seçin.

4. İş durumunun **çalışıyor**olarak değiştirildiğini aklınızda edin. Giriş ve çıkış olayı ölçümlerini görmek için iş adına sağ tıklayın ve **portalda Iş görünümü aç** ' ı seçin. Bu işlem birkaç dakika sürebilir.

5. Sonuçları görüntülemek için Visual Studio Code uzantısında veya Azure portal BLOB depolama alanını açın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra gerektiğinde yeniden başlatabilirsiniz. Bu işi kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak bu hızlı başlangıçla oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'nı ve ardından oluşturduğunuz kaynağın adını seçin.  

2. Kaynak grubu sayfanızda, **Sil**'i seçin, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Visual Studio Code kullanarak basit bir Stream Analytics işi dağıttınız. Ayrıca, [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)ve Visual Studio (Stream-Analytics-Quick-Create-vs.MD) kullanarak Stream Analytics işleri dağıtabilirsiniz. 

Visual Studio Azure Stream Analytics araçları hakkında bilgi edinmek için aşağıdaki makaleye ilerleyin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics işleri görüntülemek için Visual Studio 'Yu kullanma](stream-analytics-vs-tools.md)
