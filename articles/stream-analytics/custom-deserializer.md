---
title: Azure Stream Analytics bulut işleri için özel .NET seri hale getiriciler
description: Bu öğreticide, Visual Studio kullanarak Azure Stream Analytics bir bulut işi için özel bir .NET seri hale getirici oluşturma işlemi gösterilmektedir.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 56d0927bff928929ca55433a812dfe6c1405a738
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991921"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Öğretici: Azure Stream Analytics için özel .NET seri hale Getiricileri

Azure Stream Analytics [üç veri biçimi için yerleşik desteğe](stream-analytics-parsing-json.md)sahiptir: JSON, CSV ve avro. Özel .NET seri hale Getiricileri sayesinde, hem bulut hem de kenar işleri için [protokol arabelleği](https://developers.google.com/protocol-buffers/), [Bono](https://github.com/Microsoft/bond) ve diğer Kullanıcı tanımlı biçimler gibi diğer biçimlerden verileri okuyabilirsiniz.

Bu öğreticide, Visual Studio kullanarak Azure Stream Analytics bir bulut işi için özel bir .NET seri hale getirici oluşturma işlemi gösterilmektedir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Protokol arabelleği için özel bir seri hale getirici oluşturun.
> * Visual Studio 'da bir Azure Stream Analytics işi oluşturun.
> * Stream Analytics işinizi özel seri hale getirici 'yi kullanacak şekilde yapılandırın.
> * Özel seri hale getirici 'yi test etmek için Stream Analytics işinizi yerel olarak çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* [Visual studio 2017](https://www.visualstudio.com/downloads/) veya [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)' ü yükler. Enterprise (Ultimate/Premium), Professional ve Community sürümleri desteklenir. Express sürümü desteklenmiyor.

* [Visual Studio için Stream Analytics araçları 'nı](stream-analytics-tools-for-visual-studio-install.md) veya en son sürüme güncelleştirin. Aşağıdaki Visual Studio sürümleri desteklenir:
   * Visual Studio 2015
   * Visual Studio 2017

* Visual Studio 'da **bulut Gezgini** 'ni açın ve Azure aboneliğinizde oturum açın.

* Azure depolama hesabınızda bir kapsayıcı oluşturun.
Oluşturduğunuz kapsayıcı, Stream Analytics işle ilgili varlıkları depolamak için kullanılacaktır. Kapsayıcı bulunan bir depolama hesabınız varsa onu kullanabilirsiniz. Yoksa [yeni bir kapsayıcı oluşturmanız](../storage/blobs/storage-quickstart-blobs-portal.md) gerekir.

## <a name="create-a-custom-deserializer"></a>Özel seri hale getirici oluşturma

1. Visual Studio 'Yu açın ve **dosya > yeni > proje**' yi seçin. **Stream Analytics** arayın ve özel seri **hale getirici projesi (.net) Azure Stream Analytics**seçin. Projeye **prototipsiz seri hale getirici**gibi bir ad verin.

   ![Visual Studio DotNet standart sınıf kitaplığı projesi oluştur](./media/custom-deserializer/create-dotnet-library-project.png)

2. Çözüm Gezgini, **prototip seri hale getirici** projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. Ardından **Microsoft. Azure. StreamAnalytics** ve **Google. prototipi** NuGet paketlerini yükler.

3. [Messagebodyproto sınıfını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) ve [Messagebodydeserializer sınıfını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) projenize ekleyin.

4. **Protoseri hale getirici** projesi oluşturun.

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics projesi ekleme

1. Çözüm Gezgini, **prototip seri hale getirici** çözümüne sağ tıklayın ve **> yeni proje Ekle**' yi seçin. **Azure Stream Analytics > Stream Analytics**altında **Azure Stream Analytics Uygulama**' yı seçin. **Prototipbufcloudserializer** olarak adlandırın ve **Tamam**' ı seçin. 

2. **Prototipbufclouddeserializer** Azure Stream Analytics projesi altındaki **Başvurular** ' a sağ tıklayın. **Projeler**altında, **protoseri hale getirici**ekleyin. Sizin için otomatik olarak doldurulmalıdır.

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics işi yapılandırma

1. **Jobconfig. JSON**öğesine çift tıklayın. Aşağıdaki ayarlar dışında varsayılan konfigürasyonları kullanın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Genel depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Genel depolama ayarları aboneliği| aboneliğinizi < >|
   |Genel depolama ayarları depolama hesabı| depolama hesabınızı < >|
   |Özel kod depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Özel kod depolama ayarları depolama hesabı|depolama hesabınızı < >|
   |Özel kod depolama ayarları kapsayıcısı|depolama kapsayıcınızı < >|

2. **Girişler**altında **Input. JSON**öğesine çift tıklayın. Aşağıdaki ayarlar dışında varsayılan konfigürasyonları kullanın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Kaynak|Blob Depolama|
   |Kaynak|Geçerli hesaptaki veri kaynağını seçin|
   |Abonelik|aboneliğinizi < >|
   |Depolama Hesabı|depolama hesabınızı < >|
   |Kapsayıcı|depolama kapsayıcınızı < >|
   |Olay serileştirme biçimi|Diğer (Protoarabellek, XML, özel...)|
   |Kaynak|ASA projesi başvurusundan veya CodeBehind 'ten yükleme|
   |CSharp derleme adı|Prototipbufdeserializer. dll|
   |Sınıf adı|MessageBodyProto. MessageBodyDeserializer|
   |Olay sıkıştırma türü|Hiçbiri|

3. Aşağıdaki sorguyu **Script. aşama QL** dosyasına ekleyin.

   ```sql
   SELECT * FROM Input
   ```

4. [Örnek prototip giriş dosyasını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)indirin. **Girişler** klasöründe, **Input. JSON** öğesine sağ tıklayın ve **yerel giriş Ekle**' yi seçin. Ardından, **local_Input. JSON** öğesine çift tıklayın ve aşağıdaki ayarları yapılandırın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Girdi Diğer Adı|Girdi|
   |Kaynak Türü|Veri Akışı|
   |Olay serileştirme biçimi|Diğer (Protoarabellek, XML, özel...)|
   |CSharp derleme adı|Prototipbufdeserializer. dll|
   |Sınıf adı|MessageBodyProto. MessageBodyDeserializer|
   |Yerel giriş dosyası yolu|indirilen örnek prototipme giriş dosyasının dosya yolunu < >|

## <a name="execute-the-stream-analytics-job"></a>Stream Analytics işini yürütün

1. **Script. aşama QL** açın ve **yerel olarak çalıştır**' ı seçin.

2. **Stream Analytics yerel çalıştırma sonuçlarındaki**sonuçları gözlemleyin.

Stream Analytics işiniz için özel bir seri hale getirici başarıyla uyguladık! Bu öğreticide, özel seri hale getirici 'yi yerel olarak test edersiniz. Gerçek verileriniz için girişi ve çıktıyı doğru şekilde yapılandırırsınız. Ardından, yeni uyguladığınız özel seri hale getirici 'yi kullanarak işinizi bulutta çalıştırmak için Visual Studio 'dan Azure 'a gönderebilirsiniz.

## <a name="debug-your-deserializer"></a>Seri hale getiricide hata ayıklama

.NET seri hale getiricinizi standart .NET kodunda hata ayıklamanıza benzer şekilde yerel olarak hata ayıklaması yapabilirsiniz. 

1. İşlevinizde kesme noktaları ekleyin.

2. Hata ayıklamaya başlamak için **F5**'e basın. Program beklendiği gibi kesme noktalarında durur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra gerektiğinde yeniden başlatabilirsiniz. Bu işi kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak bu öğreticiyle oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'nı ve ardından oluşturduğunuz kaynağın adını seçin.  

2. Kaynak grubu sayfanızda, **Sil**'i seçin, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, protokol arabelleği giriş serileştirme için özel bir .NET seri hale getiricisi nasıl uygulayacağınızı öğrendiniz. Özel seri hale getiriciler oluşturma hakkında daha fazla bilgi edinmek için aşağıdaki makaleye geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics işleri için farklı .NET seri hale Getiricileri oluşturma](custom-deserializer-examples.md)

