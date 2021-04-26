---
title: 'Hızlı başlangıç: Azure portal kullanarak FHIR için Azure IoT bağlayıcısını dağıtma (Önizleme)'
description: Bu hızlı başlangıçta, Azure portal kullanarak FHıR için Azure API 'SI için Azure IoT Bağlayıcısı özelliğini dağıtmayı, yapılandırmayı ve kullanmayı öğreneceksiniz.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: 0a382ad948e7fd2efc6ab59eb94da3d6cbb210d7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443789"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak FHIR için Azure IoT bağlayıcısını dağıtma (Önizleme)

Hızlı sağlık ile birlikte çalışabilirlik kaynakları için Azure IoT Bağlayıcısı (FHıR&#174;) *, Azure API 'nin, tıbbi nesnelerin Interneti (IoMT) cihazlarından veri alma özelliğini sağlayan, isteğe bağlı bir Azure API özelliğidir. Önizleme aşamasında FHıR için Azure IoT Bağlayıcısı özelliği ücretsiz olarak kullanılabilir. Bu hızlı başlangıçta şunları yapmayı öğreneceksiniz:
- Azure portal kullanarak FHıR için Azure IoT bağlayıcısını dağıtma ve yapılandırma
- Azure IoT Bağlayıcısı 'na veri göndermek için sanal cihaz kullanma
- FHıR için Azure API 'sindeki FHıR için Azure IoT Bağlayıcısı tarafından oluşturulan kaynakları görüntüleme

## <a name="prerequisites"></a>Önkoşullar

- Etkin bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- FHıR kaynağı için Azure API- [Azure Portal kullanarak fhır Için Azure API dağıtma](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>FHıR kaynağı için Azure API 'sine gidin

[Azure Portal](https://portal.azure.com) açın ve Fhır Için Azure IoT bağlayıcısını oluşturmak istediğiniz **fhır kaynağı için Azure API** 'sine gidin.

[![FHıR kaynağı için Azure API](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

Sol taraftaki gezinti menüsünde, **IoT bağlayıcıları** sayfasını açmak için **Eklentiler** bölümünde **IoT Bağlayıcısı (Önizleme)** seçeneğine tıklayın.

[![IoT bağlayıcı özelliği](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>FHıR için yeni Azure IoT Bağlayıcısı oluşturma (Önizleme)

**Ekle** düğmesine tıklayarak **IoT Bağlayıcısı oluştur** sayfasını açın.

[![IoT Bağlayıcısı Ekle](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

FHıR için yeni Azure IoT Bağlayıcısı ayarlarını girin. **Oluştur** düğmesine tıklayın ve fhır dağıtımı Için Azure IoT bağlayıcısını beklelt.

> [!NOTE]
> Bu yükleme için **çözüm türü** açılan değeri olarak **Oluştur** ' u seçmeniz gerekir. 

[![IoT Bağlayıcısı oluştur](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Ayar|Değer|Açıklama |
|---|---|---|
|Bağlayıcı adı|Benzersiz bir ad|Azure IoT bağlayıcınızı tanımlamak için bir ad girin. Bu ad, FHıR kaynağı için bir Azure API 'SI içinde benzersiz olmalıdır. Ad yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. Bir harf veya sayı ile başlamalı ve bitmeli ve 3-24 karakter uzunluğunda olmalıdır.|
|Çözüm türü|Ara veya oluştur|Bir bant dışı işleminiz varsa **Ara** [' yı SEÇIN ve Azure](https://www.hl7.org/fhir/device.html) API 'niz [için fhar](https://www.hl7.org/fhir/patient.html) kaynakları oluşturun. FHıR için Azure IoT Bağlayıcısı, cihaz verilerini temsil etmek [üzere bir izleme](https://www.hl7.org/fhir/observation.html) fhır kaynağı oluştururken bu kaynaklara başvuru kullanacaktır. FHıR için Azure IoT bağlayıcısının, cihaz verilerinde bulunan ilgili tanımlayıcı değerlerini kullanarak FHıR için Azure API 'niz üzerinde çıplak cihaz ve hasta kaynakları oluşturmasını istediğinizde **Oluştur** ' u seçin.|

Yükleme tamamlandıktan sonra, FHıR için yeni oluşturulan Azure IoT Bağlayıcısı, **IoT bağlayıcıları** sayfasında görünür.

[![IoT Bağlayıcısı oluşturuldu](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>FHıR için Azure IoT bağlayıcısını yapılandırma (Önizleme)

FHıR için Azure IoT Bağlayıcısı, cihaz iletilerini FHıR tabanlı gözlem kaynaklarına dönüştürmek için iki eşleme şablonuna ihtiyaç duyuyor: **cihaz eşleme** ve **fhır eşleme**. FHıR için Azure IoT Bağlayıcınız, Bu eşlemeler karşıya yüklenene kadar tam olarak çalışmaz.

[![IoT Bağlayıcısı 'nda eşlemeler yok](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Eşleme şablonlarını karşıya yüklemek için, FHıR için yeni dağıtılan Azure IoT Bağlayıcısı ' na tıklayarak **IoT Bağlayıcısı** sayfasına gidin.

[![IoT Bağlayıcısı tıklama](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Cihaz eşleme

Cihaz eşleme şablonu, cihaz verilerini Normalleştirilmemiş bir şemaya dönüştürür. **IoT Bağlayıcısı** sayfasında cihaz eşlemeyi **Yapılandır** düğmesine tıklayarak **cihaz eşleme** sayfasına gidin. 

[![IoT Bağlayıcısı cihaz eşlemesini yapılandır seçeneğine tıklayın](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

**Cihaz eşleme** SAYFASıNDA, JSON düzenleyicisine aşağıdaki betiği ekleyin ve **Kaydet**' e tıklayın.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.telemetry.HeartRate)]",
        "patientIdExpression": "$.Properties.iotcentral-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.telemetry.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT Bağlayıcısı cihaz eşleme](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHıR eşleme

FHıR eşleme şablonu, normalleştirilmiş bir iletiyi FHıR tabanlı bir gözlem kaynağına dönüştürür. **IoT Bağlayıcısı** sayfasında **Fhır eşlemeyi Yapılandır** düğmesine tıklayarak **fhır eşleme** sayfasına gidin.  

[![IoT Bağlayıcısı configure FHıR Mapping 'e tıklayın](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

**Fhır eşleme** SAYFASıNDA, JSON düzenleyicisine aşağıdaki betiği ekleyin ve **Kaydet**' e tıklayın.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT Bağlayıcısı FHıR eşleme](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Bağlantı dizesi oluşturma

IoMT cihazının, bağlantı kurmak ve FHıR için Azure IoT bağlayıcısına ileti göndermek için bir bağlantı dizesi gerekir. FHıR için yeni dağıtılan Azure IoT bağlayıcısının **IoT Bağlayıcısı** sayfasında **istemci bağlantılarını yönet** düğmesini seçin. 

[![IoT Bağlayıcısı istemci bağlantılarını yönet ' e tıklayın](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

**Bağlantılar** sayfasında, **Ekle** düğmesine tıklayarak yeni bir bağlantı oluşturun. 

[![IoT Bağlayıcısı bağlantıları](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Bu bağlantı için, kaplama penceresinde kolay bir ad sağlayın ve **Oluştur** düğmesini seçin.

[![IoT Bağlayıcısı yeni bağlantı](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

**Bağlantılar** sayfasından yeni oluşturulan bağlantıyı seçin ve **birincil bağlantı dizesi** alanının değerini sağdaki yer paylaşımı penceresinden kopyalayın.

[![IoT Bağlayıcısı bağlantı dizesi](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Daha sonraki bir adımda kullanılmak üzere bu bağlantı dizesini koru. 

## <a name="connect-your-devices-to-iot"></a>Cihazlarınızı IoT 'ye bağlama

Azure, IoT cihazlarınızı bağlamak ve yönetmek için kapsamlı bir IoT ürünleri paketi sunar. Azure IoT Hub kullanarak PaaS 'yi temel alarak kendi çözümünüzü oluşturabilir veya Azure IoT Central ile IoT uygulamalarını yönetme platformuyla başlayabilirsiniz. Bu öğreticide, başlamanıza yardımcı olması için sektör odaklı çözüm şablonlarına sahip Azure IoT Central 'dan yararlanacağız.

[Sürekli hasta izleme uygulaması şablonunu](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template)dağıtın. Bu şablon, kullanmaya başlamanıza yardımcı olmak için gerçek zamanlı veriler üreten iki sanal cihaz içerir: **akıllı Vintals yaması** ve **akıllı Knee ayracı**.

> [!NOTE]
> Gerçek cihazlarınız her seferinde, [cihazlarınızı](../../iot-central/core/howto-set-up-template.md) eklemek ve cihaz simülatörleri değiştirmek için aynı IoT Central uygulamasını kullanabilirsiniz. Cihaz verileriniz de otomatik olarak FHıR 'ye akar. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>IoT verilerinizi FHıR için Azure IoT Bağlayıcısı (Önizleme) ile bağlama

IoT Central Uygulamanızı dağıttıktan sonra, iki kullanıma hazır sanal aygıtınız telemetri oluşturmaya başlar. Bu öğreticide, FHıR için Azure IoT Bağlayıcısı aracılığıyla *akıllı Vintals düzeltme* simülatörü simülatisini fhır 'ye aktaracağız. IoT verilerinizi FHıR için Azure IoT bağlayıcısına aktarmak için [IoT Central içinde sürekli veri dışarı aktarma](../../iot-central/core/howto-export-data.md)kurmak istiyoruz. İlk olarak hedefle bir bağlantı oluşturmanız ve ardından sürekli olarak çalıştırılmak üzere bir veri dışa aktarma işi oluşturacağız: 

> [!NOTE]
> Bu bölüm için IoT Central uygulama ayarları içinde **veri dışa** aktarma ve **veri dışarı aktarma (eski)** seçeneğini belirlemek isteyeceksiniz.

[![IoT Central veri dışarı aktarma ayarları](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png)](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png#lightbox)

Yeni bir hedef oluşturun:
- **Hedefler** sekmesine gidin ve yeni bir hedef oluşturun.
- Hedefe benzersiz bir ad vererek başlayın.
- Hedef türü olarak *Azure Event Hubs* seçin.
- FHıR **'nin bağlantı dizesi alanı için** önceki bir adımda elde edilen bağlantı dizesi Için Azure IoT Bağlayıcısı 'nı sağlayın.

Yeni veri dışarı aktarma oluştur:
- Hedefini oluşturduktan sonra **dışarı aktarmalar** sekmesine gidin ve yeni bir veri dışa aktarma oluşturun. 
- Bunu, verileri dışarı aktarma, benzersiz bir ad vererek başlatın.
- **Veri** altında, *dışarı aktarılacak veri türü* olarak *telemetri* ' i seçin.
- **Hedef** altında, önceki adda oluşturduğunuz hedef adı seçin.

## <a name="view-device-data-in-azure-api-for-fhir"></a>FHıR için Azure API 'de cihaz verilerini görüntüleme

Postman kullanarak FHıR için Azure API 'sindeki fhır için Azure IoT Bağlayıcısı tarafından oluşturulan FHıR tabanlı gözlemler kaynağını görüntüleyebilirsiniz. [Fhır Için Azure API 'sine erişmek üzere Postman](access-fhir-postman-tutorial.md) 'nizi ayarlayın ve `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` sinyal oranı değeri olan gözehır kaynaklarını görüntülemek için bir istek yapın. 

> [!TIP]
> Kullanıcının FHıR veri düzlemi için Azure API 'sine uygun erişimi olduğundan emin olun. Gerekli veri düzlemi rollerini atamak için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](configure-azure-rbac.md) kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bir Azure IoT bağlayıcısının bir örneğini, ilişkili kaynak grubunu veya fhır hizmeti için ilişkili Azure API 'sini veya FHıR örneğinin kendisi için Azure IoT bağlayıcısını kaldırarak silebilirsiniz. 

FHıR örneği için bir Azure IoT bağlayıcısını doğrudan kaldırmak için **IoT bağlayıcıları** sayfasından örnek ' i seçerek **IoT Bağlayıcısı** sayfasına gidin ve **Sil** düğmesine tıklayın. Onay sorulduğunda **Evet** ' i seçin. 

[![IoT Bağlayıcısı örneğini Sil](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıç kılavuzunda fhır için Azure API 'niz için Azure IoT Bağlayıcısı özelliğini dağıttık. FHıR için Azure IoT Bağlayıcısı hakkında daha fazla bilgi edinmek için aşağıdaki adımları aşağıdan seçin:

Azure IoT Bağlayıcısı içindeki FHIR için farklı veri akışı aşamalarını anlayın.

>[!div class="nextstepaction"]
>[FHıR veri akışı için Azure IoT Bağlayıcısı](iot-data-flow.md)

Cihaz ve FHıR eşleme şablonlarını kullanarak IoT bağlayıcısını nasıl yapılandıracağınızı öğrenin.

>[!div class="nextstepaction"]
>[FHıR eşleme şablonları için Azure IoT Bağlayıcısı](iot-mapping-templates.md)

* Azure portal, FHıR için Azure IoT Bağlayıcısı, IoT Bağlayıcısı (Önizleme) olarak adlandırılır. FHıR, HL7 'in tescilli ticari markasıdır ve HL7 izniyle kullanılır.
