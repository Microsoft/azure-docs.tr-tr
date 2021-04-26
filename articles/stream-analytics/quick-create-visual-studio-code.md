---
title: Hızlı Başlangıç-Visual Studio Code Azure Stream Analytics iş oluşturma
description: Bu hızlı başlangıçta, Stream Analytics işi oluşturma, girdileri ve çıkışları yapılandırma ve Visual Studio Code bir sorgu tanımlama ile nasıl başlacağınız gösterilmektedir.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 104dfe7b2ce253d7ae42c7484532cfd5b2993aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014620"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Hızlı başlangıç: Visual Studio Code Azure Stream Analytics işi oluşturma (Önizleme)

Bu hızlı başlangıçta, Visual Studio Code için Azure Stream Analytics araçları uzantısını kullanarak bir Azure Stream Analytics işinin nasıl oluşturulacağı ve çalıştırılacağı gösterilmektedir. Örnek iş, bir Azure IoT Hub cihazından akış verilerini okur. 27 ° üzerinde ortalama sıcaklığı hesaplayan ve sonuç çıktı olaylarını BLOB depolama alanındaki yeni bir dosyaya yazan bir iş tanımlarsınız.

> [!NOTE]
> Visual Studio ve Visual Studio Code araçları, Çin Doğu, Çin Kuzey, Almanya Orta ve Almanya kuzeydoğu bölgelerinde bulunan işleri desteklemez.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* [Azure portalında](https://portal.azure.com/) oturum açın.

* [Visual Studio Code](https://code.visualstudio.com/)'i yükler.

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Azure Stream Analytics araçları uzantısını yükler

1. Visual Studio Code’u açın.

2. Sol bölmedeki **uzantılardan** **Stream Analytics** arayın ve **Azure Stream Analytics araçları** uzantısında **Install** ' u seçin.

3. Uzantı yüklendikten sonra, **Azure Stream Analytics araçlarının** **etkin uzantılar**' da görünür olduğunu doğrulayın.

   ![Visual Studio Code Azure Stream Analytics araçları etkinleştirilmiş Uzantılar altında](./media/quick-create-visual-studio-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Azure Stream Analytics araçları uzantısını etkinleştirin

1. Visual Studio Code etkinlik çubuğunda **Azure** simgesini seçin. Yan çubukta **Stream Analytics** altında **Azure 'da oturum aç**' ı seçin.

   ![Visual Studio Code’da Azure oturumu açma](./media/quick-create-visual-studio-code/azure-sign-in.png)

2. Oturum açtığınızda, Azure hesabınızın adı Visual Studio Code penceresinin sol alt köşesindeki durum çubuğunda görüntülenir.

> [!NOTE]
> Azure Stream Analytics araçları uzantısı, oturumu kapatıp bir sonraki sefer oturumunuzu otomatik olarak açacaktır. Hesabınızda iki öğeli kimlik doğrulaması varsa, PIN kullanmak yerine telefon kimlik doğrulaması kullanmanızı öneririz.
> Listeleme kaynaklarıyla ilgili sorunlarınız varsa oturumu kapatıp yeniden oturum açmak genellikle yardımcı olur. Oturumu kapatmak için komutunu girin `Azure: Sign Out` .

## <a name="prepare-the-input-data"></a>Girdi verilerini hazırlama

Stream Analytics işini tanımladıktan önce, daha sonra iş girişi olarak yapılandırılmış verileri hazırlamanız gerekir. İş için gereken giriş verilerini hazırlamak için aşağıdaki adımları uygulayın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. IoT Hub **nesnelerin interneti kaynak oluştur**' u seçin  >    >  .

3. **IoT Hub** bölmesinde aşağıdaki bilgileri girin:

   |**Ayar**  |**Önerilen değer**  |**Açıklama**  |
   |---------|---------|---------|
   |Abonelik  | \<Your subscription\> |  Kullanmak istediğiniz Azure aboneliğini seçin. |
   |Kaynak Grubu   |   asaquickstart-resourcegroup  |   **Yeni Oluştur**’u seçin ve hesabınız için yeni bir kaynak grubu adı girin. |
   |Region  |  \<Select the region that is closest to your users\> | IoT Hub 'ınızı barındırabileceğiniz bir coğrafi konum seçin. Kullanıcılarınıza en yakın konumu kullanın. |
   |IoT Hub adı  | MyASAIoTHub  |   IoT Hub 'ınız için bir ad seçin.   |

   ![IoT hub oluşturma](./media/quick-create-visual-studio-code/create-iot-hub.png)

4. **İleri ' yi seçin: boyut ve ölçek ayarla**.

5. **Fiyatlandırma ve ölçek katmanı** için bir seçim yapın. Bu hızlı başlangıçta, aboneliğinizde hala kullanılabiliyorsa **F1-ücretsiz** katmanını seçin. Ücretsiz katman kullanılamıyorsa, kullanılabilir en düşük katmanı seçin. Daha fazla bilgi için bkz. [Azure IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![IoT Hub 'ınızı boyutlandırma ve ölçeklendirme](./media/quick-create-visual-studio-code/iot-hub-size-and-scale.png)

6. **Gözden geçir ve oluştur**’u seçin. IoT Hub bilgilerinizi gözden geçirin ve **Oluştur**' u seçin. IoT hub’ınızın oluşturulması birkaç dakika sürebilir. İlerleme durumunu **Bildirimler** bölmesinde izleyebilirsiniz.

7. IoT Hub 'ının gezinti menüsünde **IoT cihazları** altında **Ekle** ' yi seçin. **CIHAZ kimliği** IÇIN bir kimlik ekleyin ve **Kaydet**' i seçin.

   ![IoT Hub 'ınıza cihaz ekleme](./media/quick-create-visual-studio-code/add-device-iot-hub.png)

8. Cihaz oluşturulduktan sonra, **IoT cihazları** listesinden cihazı açın. Dizeyi **bağlantı dizesinde (birincil anahtar)** kopyalayın ve daha sonra kullanmak için bir not defteri 'ne kaydedin.

   ![IoT Hub cihaz bağlantı dizesini Kopyala](./media/quick-create-visual-studio-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>IoT simülatörü çalıştırma

1. Yeni bir tarayıcı sekmesinde veya penceresinde [Raspberry PI Azure IoT çevrimiçi simülatörünü](https://azure-samples.github.io/raspberry-pi-web-simulator/) açın.

2. 15. satırdaki yer tutucusunu, daha önce kaydettiğiniz IoT Hub cihaz bağlantı dizesiyle değiştirin.

3. **Çalıştır**'ı seçin. Çıktıda, IoT Hub 'ınıza gönderilen algılayıcı verileri ve iletileri gösterilmelidir.

   ![Çıkış ile Raspberry PI Azure IoT çevrimiçi simülatörü](./media/quick-create-visual-studio-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>BLOB depolama oluştur

1. Azure Portal, **kaynak**  >  **depolama**  >  **depolama hesabı** oluştur ' u seçin.

2. **Depolama hesabı oluştur** bölmesinde, bir depolama hesabı adı, konum ve kaynak grubu girin. Oluşturduğunuz IoT Hub 'ı ile aynı konumu ve kaynak grubunu seçin. Ardından hesabı oluşturmak için **gözden geçir + oluştur** ' u seçin.

   ![Depolama hesabı oluştur](./media/quick-create-visual-studio-code/create-storage-account.png)

3. Depolama Hesabınız oluşturulduktan sonra **genel bakış** bölmesinde **Bloblar** kutucuğunu seçin.

   ![Depolama hesabına genel bakış](./media/quick-create-visual-studio-code/blob-storage.png)

4. **BLOB hizmeti** sayfasından **kapsayıcı** ' yı seçin ve Kapsayıcınız için **kapsayıcı1** gibi bir ad sağlayın. **Genel erişim düzeyini** **özel (anonim erişim yok)** olarak bırakın ve **Tamam**' ı seçin.

   ![Blob kapsayıcısı oluşturma](./media/quick-create-visual-studio-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics projesi oluşturma

1. Visual Studio Code ' de, komut paletini açmak için **CTRL + SHIFT + P** ' yi seçin. Ardından **asa** girin ve **asa: yeni proje oluştur**' u seçin.

   ![Yeni proje oluşturma](./media/quick-create-visual-studio-code/create-new-project.png)

2. **MyASAproj** gibi proje adınızı girin ve projeniz için bir klasör seçin.

    ![Proje adı oluştur](./media/quick-create-visual-studio-code/create-project-name.png)

3. Yeni proje çalışma alanınıza eklenir. Bir Stream Analytics projesi üç klasörden oluşur: **girişler**, **çıktılar** ve **işlevler**. Ayrıca sorgu betiği **(*. aşama QL)**, dosya **JobConfig.js** bir **asaproj.js** ve yapılandırma dosyasında bir de vardır.

    Yapılandırma dosyası **asaproj.js** , Stream Analytics işini Azure 'a göndermek için gereken girdileri, çıkışları ve iş yapılandırma dosyası bilgilerini içerir.

    ![Visual Studio Code içinde proje dosyaları Stream Analytics](./media/quick-create-visual-studio-code/asa-project-files.png)

> [!Note]
> Komut paletinden giriş ve çıkış eklerken, bunlara karşılık gelen yollar otomatik olarak **asaproj.js** eklenir. Doğrudan diske giriş veya çıkış ekler veya kaldırırsanız, bunları **üzerindeasaproj.js** el ile eklemeniz veya kaldırmanız gerekir. Giriş ve çıkışları tek bir yere yerleştirmeyi ve sonra dosyadaki her bir **asaproj.js** için yollar belirterek bunları farklı işlere başvurmalarını tercih edebilirsiniz.

## <a name="define-the-transformation-query"></a>Dönüşüm sorgusunu tanımlama

1. Proje klasörünüzdeki **myASAproj. asaql** ' i açın.

2. Aşağıdaki sorguyu ekleyin:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Canlı giriş tanımlama

1. Stream Analytics projenizdeki **girişler** klasörüne sağ tıklayın. Ardından **asa: bağlam menüsünden giriş Ekle** öğesini seçin.

    ![Girişler klasöründen giriş ekleme](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

    Ya da **CTRL + SHIFT + P** ' yi seçerek komut paletini açın ve **asa: giriş Ekle**' ye tıklayın.

   ![Visual Studio Code Stream Analytics girişi ekleme](./media/quick-create-visual-studio-code/add-input.png)

2. Giriş türü için **IoT Hub** seçin.

   ![Giriş seçeneği olarak IoT Hub 'ı seçin](./media/quick-create-visual-studio-code/iot-hub.png)

3. Girişi komut paletinden eklediyseniz, girişi kullanacak Stream Analytics sorgu betiğini seçin. **MyASAproj. aşama QL** dosya yoluyla otomatik olarak doldurulmalıdır.

   ![Visual Studio Code bir Stream Analytics betiği seçin](./media/quick-create-visual-studio-code/asa-script.png)

4. Açılır menüden **Azure aboneliklerinizden Seç ' i** seçin.

    ![Aboneliklerden Seç](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Dosyadaki yeni oluşturulan **IoTHub1.js** aşağıdaki değerlerle düzenleyin. Burada belirtilmeyen alanlar için varsayılan değerleri tutun.

   |Ayar|Önerilen değer|Açıklama|
   |-------|---------------|-----------|
   |Ad|Giriş|İşin girişini tanımlamak için bir ad girin.|
   |IotHubNamespace|MyASAIoTHub|IoT Hub 'ınızın adını seçin veya girin. IoT Hub adları, aynı abonelikte oluşturulduklarında otomatik olarak algılanır.|
   |SharedAccessPolicyName|iothubowner| |

   CodeLens özelliğini kullanarak bir dize girmenize, açılan listeden seçim yapmanıza veya metnin doğrudan dosyada değiştirilmesini sağlayabilirsiniz. Aşağıdaki ekran görüntüsünde bir örnek olarak **aboneliklerinizden seçim** gösterilmektedir. Kimlik bilgileri otomatik olarak listelenir ve yerel kimlik bilgileri Yöneticisi 'ne kaydedilir.

   ![Visual Studio Code girişi yapılandırma](./media/quick-create-visual-studio-code/configure-input.png)

   ![Visual Studio Code giriş değerini yapılandırma](./media/quick-create-visual-studio-code/configure-input-value.png)

## <a name="preview-input"></a>Önizleme girişi

En üstteki satırdaki **IoTHub1.js** **Önizleme verileri** ' ni seçin. Bazı giriş verileri IoT Hub 'ından alınacaktır ve önizleme penceresinde gösterilir. Bu işlem biraz zaman alabilir.

 ![Canlı giriş önizlemesi](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-an-output"></a>Çıkış tanımlama

1. Komut paletini açmak için **CTRL + SHIFT + P** ' yi seçin. Ardından **asa: çıkış Ekle** yazın.

   ![Visual Studio Code Stream Analytics çıkış Ekle](./media/quick-create-visual-studio-code/add-output.png)

2. Havuz türü için **BLOB depolama alanını** seçin.

3. Bu girişi kullanacak Stream Analytics sorgu betiğini seçin.

4. Çıkış dosyası adını **Blobstorage** olarak girin.

5. **Blobstorage** 'ı aşağıdaki değerleri kullanarak düzenleyin. Burada belirtilmeyen alanlar için varsayılan değerleri tutun. Bir açılan listeden seçim yapmanıza veya bir dize girmenize yardımcı olması için CodeLens özelliğini kullanın.

   |Ayar|Önerilen değer|Açıklama|
   |-------|---------------|-----------|
   |Ad|Çıktı| İşin çıkışını tanımlamak için bir ad girin.|
   |Depolama Hesabı|asaquickstartstorage|Depolama hesabınızın adını seçin veya girin. Depolama hesabı adları, aynı abonelikte oluşturulduklarında otomatik olarak algılanır.|
   |Kapsayıcı|kapsayıcı1|Depolama hesabınızda oluşturduğunuz mevcut kapsayıcıyı seçin.|
   |Yol Deseni|çıkış|Kapsayıcıda oluşturulacak dosya yolunun adını girin.|

   ![Visual Studio Code çıktıyı yapılandırma](./media/quick-create-visual-studio-code/configure-output.png)

## <a name="compile-the-script"></a>Betiği derle

Betik derlemesi, sözdizimini denetler ve otomatik dağıtım için Azure Resource Manager şablonları oluşturur.

Betik derlemesini tetiklemenin iki yolu vardır:

- Çalışma alanından betiği seçin ve ardından komut paletinden derleyin.

   ![Betiği derlemek için Visual Studio Code komut paletini kullanın](./media/quick-create-visual-studio-code/compile-script1.png)

- Betiğe sağ tıklayıp **asa: betiği derle**' yi seçin.

    ![Derlemek için Stream Analytics komut dosyasına sağ tıklayın](./media/quick-create-visual-studio-code/compile-script2.png)

Derlemeden sonra, oluşturulan iki Azure Resource Manager şablonunu projenizin **Deploy** klasöründe bulabilirsiniz. Bu iki dosya otomatik dağıtım için kullanılır.

![Dosya Gezgini 'nde dağıtım şablonlarını Stream Analytics](./media/quick-create-visual-studio-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Stream Analytics işini Azure 'a gönderme

1. Sorgu betiğinizin betik Düzenleyicisi penceresinde **Azure 'A gönder**' i seçin.

   ![Betik düzenleyicisinde abonelik metinlerinizden seçim yapın](./media/quick-create-visual-studio-code/submit-job.png)

2. Açılır listeden aboneliğinizi seçin.

3. **Iş Seç ' i** seçin. Sonra **Yeni Iş oluştur**' u seçin.

4. İş adınızı girin, **myASAjob**. Ardından kaynak grubunu ve konumunu seçmek için yönergeleri izleyin.

5. **Azure 'A gönder**' i seçin. Günlükleri çıkış penceresinde bulabilirsiniz. 

6. İşiniz oluşturulduğunda, **Stream Analytics Explorer**'da görebilirsiniz.

    ![Stream Analytics Gezgininde listelenen iş](./media/quick-create-visual-studio-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics işini başlatıp çıktıyı denetleyin

1. Visual Studio Code **Stream Analytics Gezginini** açın ve işinizi bulun, **myASAJob**.

2. İş adına sağ tıklayın. Ardından bağlam menüsünden **Başlat** ' ı seçin.

   ![Stream Analytics işi Visual Studio Code başlatın](./media/quick-create-visual-studio-code/start-asa-job-vs-code.png)

3. İşi başlatmak için açılır pencerede **Şimdi** öğesini seçin.

4. İş durumunun **çalışıyor** olarak değiştirildiğini unutmayın. Giriş ve çıkış olayı ölçümlerini görmek için iş adına sağ tıklayın ve **portalda Iş görünümü aç** ' ı seçin. Bu işlem birkaç dakika sürebilir.

5. Sonuçları görüntülemek için Visual Studio Code uzantısında veya Azure portal BLOB depolama alanını açın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşi silmek, işin kullandığı akış birimlerinin faturalandırmasını önler. 

İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra yeniden başlatabilirsiniz. Bu işi yeniden kullanacaksanız, bu hızlı başlangıçta oluşturduğunuz tüm kaynakları silmek için aşağıdaki adımları kullanın:

1. Azure portal sol menüden **kaynak grupları** ' nı seçin ve ardından oluşturduğunuz kaynağın adını seçin.  

2. Kaynak grubunuzun sayfasında **Sil**’i seçin. Metin kutusuna silinecek kaynağın adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Visual Studio Code kullanarak basit bir Stream Analytics işi dağıttınız. Ayrıca, [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)ve [Visual Studio](stream-analytics-quick-create-vs.md)kullanarak Stream Analytics işleri dağıtabilirsiniz.

Visual Studio Code için Azure Stream Analytics araçları hakkında bilgi edinmek için aşağıdaki makalelere geçin:

* [Visual Studio Code kullanarak örnek verilerle yerel olarak Stream Analytics sorguları test edin](visual-studio-code-local-run.md)

* [Visual Studio Code ile canlı girişte yerel olarak test Azure Stream Analytics işleri](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics işleri görüntülemek için Visual Studio Code kullanma](visual-studio-code-explore-jobs.md)

* [NPM paketini kullanarak CI/CD işlem hatlarını ayarlama](./cicd-overview.md)