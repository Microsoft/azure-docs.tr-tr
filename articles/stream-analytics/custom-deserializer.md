---
title: Öğretici-Azure Stream Analytics bulut işleri için özel .NET seri hale getiriciler
description: Bu öğreticide, Visual Studio kullanarak Azure Stream Analytics bir bulut işi için özel bir .NET seri hale getirici oluşturma işlemi gösterilmektedir.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018216"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Öğretici: Azure Stream Analytics için özel .NET seri hale Getiricileri

Azure Stream Analytics [üç veri biçimi için yerleşik desteğe](stream-analytics-parsing-json.md)sahiptir: JSON, CSV ve avro. Özel .NET seri hale Getiricileri sayesinde, hem bulut hem de kenar işleri için [protokol arabelleği](https://developers.google.com/protocol-buffers/), [Bono](https://github.com/Microsoft/bond) ve diğer Kullanıcı tanımlı biçimler gibi diğer biçimlerden verileri okuyabilirsiniz.

Bu öğreticide, Visual Studio kullanarak Azure Stream Analytics bir bulut işi için özel bir .NET seri hale getirici oluşturma işlemi gösterilmektedir. Visual Studio Code .NET seri hale Getiricileri oluşturma hakkında bilgi edinmek için, bkz. [Visual Studio Code Azure Stream Analytics işleri için .net seri hale Getiricileri oluşturma](visual-studio-code-custom-deserializer.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Protokol arabelleği için özel bir seri hale getirici oluşturun.
> * Visual Studio 'da bir Azure Stream Analytics işi oluşturun.
> * Stream Analytics işinizi özel seri hale getirici 'yi kullanacak şekilde yapılandırın.
> * Özel seri hale getirici 'yi test etmek ve hatalarını ayıklamak için Stream Analytics işinizi yerel olarak çalıştırın.


## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* [Visual studio 2019 (önerilir)](https://www.visualstudio.com/downloads/) veya [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/)'yi yükler. Enterprise (Ultimate/Premium), Professional ve Community sürümleri desteklenir. Express sürümü desteklenmiyor. 

* [Visual Studio için Stream Analytics araçları 'nı](stream-analytics-tools-for-visual-studio-install.md) veya en son sürüme güncelleştirin. 

* Visual Studio 'da **bulut Gezgini** 'ni açın ve Azure aboneliğinizde oturum açın.

* Azure depolama hesabınızda bir kapsayıcı oluşturun.
Oluşturduğunuz kapsayıcı, Stream Analytics işle ilgili varlıkları depolamak için kullanılacaktır. Kapsayıcı bulunan bir depolama hesabınız varsa onu kullanabilirsiniz. Yoksa [yeni bir kapsayıcı oluşturmanız](../storage/blobs/storage-quickstart-blobs-portal.md) gerekir.

## <a name="create-a-custom-deserializer"></a>Özel seri hale getirici oluşturma

1. Visual Studio 'Yu açın ve **dosya > yeni > proje**' yi seçin. **Stream Analytics** arayın ve özel seri **hale getirici projesi (.net) Azure Stream Analytics** seçin. Projeye **prototipsiz seri hale getirici** gibi bir ad verin.

   ![Visual Studio DotNet standart sınıf kitaplığı projesi oluştur](./media/custom-deserializer/create-dotnet-library-project.png)

2. Çözüm Gezgini, **prototip seri hale getirici** projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. Ardından **Microsoft. Azure. StreamAnalytics** ve **Google. prototipi** NuGet paketlerini yükler.

3. [Messagebodyproto sınıfını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) ve [Messagebodydeserializer sınıfını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) projenize ekleyin.

4. **Protoseri hale getirici** projesi oluşturun.

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics projesi ekleme

1. Çözüm Gezgini, **prototip seri hale getirici** çözümüne sağ tıklayın ve **> yeni proje Ekle**' yi seçin. **Azure Stream Analytics > Stream Analytics** altında **Azure Stream Analytics Uygulama**' yı seçin. **Prototipbufcloudserializer** olarak adlandırın ve **Tamam**' ı seçin. 

2. **Prototipbufclouddeserializer** Azure Stream Analytics projesi altındaki **Başvurular** ' a sağ tıklayın. **Projeler** altında, **protoseri hale getirici** ekleyin. Sizin için otomatik olarak doldurulmalıdır.

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics işi yapılandırma

1. **ÜzerindeJobConfig.js** çift tıklayın. Aşağıdaki ayarlar dışında varsayılan konfigürasyonları kullanın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Genel depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Genel depolama ayarları aboneliği| Aboneliğinizi < >|
   |Genel depolama ayarları depolama hesabı| Depolama hesabınızı < >|
   |Özel kod depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Özel kod depolama ayarları depolama hesabı|Depolama hesabınızı < >|
   |Özel kod depolama ayarları kapsayıcısı|Depolama kapsayıcınızı < >|

2. **Girişler** altında **Input.jsaçık**' a çift tıklayın. Aşağıdaki ayarlar dışında varsayılan konfigürasyonları kullanın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Kaynak|Blob Depolama|
   |Kaynak|Geçerli hesaptaki veri kaynağını seçin|
   |Abonelik|Aboneliğinizi < >|
   |Depolama Hesabı|Depolama hesabınızı < >|
   |Kapsayıcı|Depolama kapsayıcınızı < >|
   |Olay serileştirme biçimi|Diğer (Protoarabellek, XML, özel...)|
   |Kaynak|ASA projesi başvurusundan veya CodeBehind 'ten yükleme|
   |CSharp derleme adı|ProtobufDeserializer.dll|
   |Sınıf Adı|MessageBodyProto. MessageBodyDeserializer|
   |Olay sıkıştırma türü|Yok|

3. Aşağıdaki sorguyu **Script. aşama QL** dosyasına ekleyin.

   ```sql
   SELECT * FROM Input
   ```

4. [Örnek prototip giriş dosyasını](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)indirin. **Girişler** klasöründe **Input.jsüzerinde** sağ tıklayın ve **yerel giriş Ekle**' yi seçin. Ardından, **üzerindelocal_Input.js** çift tıklayın ve aşağıdaki ayarları yapılandırın:

   |Ayar|Önerilen Değer|
   |-------|---------------|
   |Girdi Diğer Adı|Giriş|
   |Kaynak Türü|Veri Akışı|
   |Olay serileştirme biçimi|Diğer (Protoarabellek, XML, özel...)|
   |CSharp derleme adı|ProtobufDeserializer.dll|
   |Sınıf Adı|MessageBodyProto. MessageBodyDeserializer|
   |Yerel giriş dosyası yolu|İndirilen örnek prototipme giriş dosyasının dosya yolunu <>|

## <a name="execute-the-stream-analytics-job"></a>Stream Analytics işini yürütün

1. **Script. aşama QL** açın ve **yerel olarak çalıştır**' ı seçin.

2. **Stream Analytics yerel çalıştırma sonuçlarındaki** sonuçları gözlemleyin.

Stream Analytics işiniz için özel bir seri hale getirici başarıyla uyguladık! Bu öğreticide, özel seri hale getirici 'yi yerel olarak test edersiniz. Gerçek verileriniz için girişi ve çıktıyı doğru şekilde yapılandırırsınız. Ardından, yeni uyguladığınız özel seri hale getirici 'yi kullanarak işinizi bulutta çalıştırmak için Visual Studio 'dan Azure 'a gönderebilirsiniz.

## <a name="debug-your-deserializer"></a>Seri hale getiricide hata ayıklama

.NET seri hale getiricinizi standart .NET kodunda hata ayıklamanıza benzer şekilde yerel olarak hata ayıklaması yapabilirsiniz.

1. **Prototipbufclouddeserializer** proje adına sağ tıklayın ve başlangıç projesi olarak ayarlayın.

2. İşlevinizde kesme noktaları ekleyin.

3. Hata ayıklamaya başlamak için **F5**'e basın. Program beklendiği gibi kesme noktalarında durur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra gerektiğinde yeniden başlatabilirsiniz. Bu işi kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak bu öğreticiyle oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'nı ve ardından oluşturduğunuz kaynağın adını seçin.  

2. Kaynak grubu sayfanızda, **Sil**'i seçin, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, protokol arabelleği giriş serileştirme için özel bir .NET seri hale getiricisi nasıl uygulayacağınızı öğrendiniz. Özel seri hale getiriciler oluşturma hakkında daha fazla bilgi edinmek için aşağıdaki makaleye geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics işleri için farklı .NET seri hale Getiricileri oluşturma](custom-deserializer-examples.md)