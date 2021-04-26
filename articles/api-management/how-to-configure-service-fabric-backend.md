---
title: Azure API Management Service Fabric arka ucunu ayarlama | Microsoft Docs
description: Azure portal kullanarak Azure API Management Service Fabric hizmet arka ucu oluşturma
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815891"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Azure portal kullanarak API Management Service Fabric arka ucu ayarlama

Bu makalede, Azure portal kullanarak bir [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) HIZMETINI özel API arka ucu olarak yapılandırma gösterilmektedir. Tanıtım amacıyla, Service Fabric arka ucu olarak durum bilgisiz ASP.NET Core güvenilir bir hizmetin nasıl ayarlanacağını gösterir.

Arka plan için [API Management backends](backends.md)bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

Özel arka uç olarak Windows çalıştıran bir Service Fabric kümesinde örnek hizmeti yapılandırma önkoşulları:

* **Windows geliştirme ortamı** - [Visual Studio 2019](https://www.visualstudio.com) ve **Azure geliştirme**, **ASP.net ve Web geliştirme** ve **.NET Core platformlar arası geliştirme** iş yüklerini yükler. Ardından bir [.NET dağıtım ortamı](../service-fabric/service-fabric-get-started.md) ayarlayın.

* **Küme Service Fabric** -bkz. [öğretici: Windows çalıştıran bir Service Fabric kümesini Azure sanal ağına dağıtma](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Var olan bir X. 509.952 sertifikası veya test amaçları için yeni, otomatik olarak imzalanan bir sertifika oluşturmak için bir küme oluşturabilirsiniz. Küme bir sanal ağda oluşturulur.

* **Örnek Service Fabric uygulaması** -BIR Web API uygulaması oluşturun ve [Azure 'da Service Fabric ile API Management tümleştirme](../service-fabric/service-fabric-tutorial-deploy-api-management.md)' de açıklandığı gibi Service Fabric kümesine dağıtın.

    Bu adımlar, varsayılan Web API 'SI proje şablonunu kullanarak, durum bilgisiz ASP.NET Core güvenilir bir hizmet oluşturur. Daha sonra, bu hizmet için HTTP uç noktasını Azure API Management üzerinden kullanıma sunacaksınız.

    Örneğin, uygulama adını bir yere göz atın `fabric:/myApplication/myService` . 

* **API Management örneği** - **Premium** veya  **Geliştirici** katmanında ve Service Fabric kümesiyle aynı bölgede bulunan mevcut veya yeni bir API Management örneği. Gerekirse, [bir API Management örneği oluşturun](get-started-create-service-instance.md).

* **Sanal ağ** -API Management örneğinizi Service Fabric kümeniz için oluşturduğunuz sanal ağa ekleyin. API Management, sanal ağda ayrılmış bir alt ağ gerektirir.

  API Management örneği için sanal ağ bağlantısını etkinleştirme adımları için bkz. [Azure API Management sanal ağlarla kullanma](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Arka uç Oluşturma-Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>API Management Service Fabric küme sertifikası Ekle

Service Fabric küme sertifikası, kümeyle ilişkili bir Azure anahtar kasasında saklanır ve yönetilir. Bu sertifikayı API Management örneğinize bir istemci sertifikası olarak ekleyin.

API Management örneğine sertifika ekleme adımları için bkz. [Azure 'da istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini koruma API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Anahtar Kasası sertifikasına başvurarak API Management sertifikayı eklemeniz önerilir. 

### <a name="add-service-fabric-backend"></a>Service Fabric arka ucu Ekle

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **API 'ler** altında **backends**  >  **+ Ekle**' yi seçin.
1. Bir arka uç adı ve isteğe bağlı bir açıklama girin
1. **Tür**' de **Service Fabric**' yi seçin.
1. **Çalışma zamanı URL 'si** içinde, API Management istekleri iletecek Service Fabric arka uç hizmetinin adını girin. Örnek: `fabric:/myApplication/myService`. 
1. **En fazla bölüm çözünürlüğü yeniden deneme sayısı** bölümünde 0 ile 10 arasında bir sayı girin.
1. Service Fabric kümesinin yönetim uç noktasını girin. Bu uç nokta, bağlantı noktasındaki kümenin URL 'sidir ( `19080` Örneğin,) `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. **İstemci sertifikası**' nda, önceki bölümde API Management örneğinize eklediğiniz Service Fabric kümesi sertifikasını seçin.
1. **Yönetim uç noktası yetkilendirme yönteminde**, TLS iletişimi için Service Fabric kümesi yönetim hizmeti tarafından kullanılan bir sertifikanın parmak izini veya sunucu x509 adını girin.
1. **Sertifika zincirini doğrula** ve **sertifika adı ayarlarını doğrula** seçeneklerini etkinleştirin.
1. **Yetkilendirme kimlik bilgileri**' nde, Service Fabric ' deki yapılandırılmış arka uç hizmetine erişmek için gerekirse kimlik bilgilerini sağlayın. Bu senaryoda kullanılan örnek uygulama için, yetkilendirme kimlik bilgileri gerekli değildir.
1. **Oluştur**’u seçin.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service Fabric arka ucu oluşturma":::

## <a name="use-the-backend"></a>Arka ucu kullanma

Özel bir arka uç kullanmak için ilkeyi kullanarak ona başvurun [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) . Bu ilke, bir gelen API isteğinin varsayılan arka uç hizmeti taban URL 'sini belirtilen bir arka uca dönüştürür, bu durumda Service Fabric arka ucu. 

`set-backend-service`İlke, API ayarlarında belirtilenden farklı bir arka uca gelen bir isteği dönüştürmek için var olan BIR API ile yararlı olabilir. Bu makaledeki tanıtım amacıyla, bir test API 'SI oluşturun ve ilkeyi Service Fabric arka uca yönlendirmek için ilkeyi ayarlayın. 

### <a name="create-api"></a>API oluştur

Boş bir API oluşturmak için [El Ile API ekleme](add-api-manually.md) bölümündeki adımları izleyin.

* API ayarları ' nda, **Web hizmeti URL 'sini** boş bırakın.
* *Doku* gibi BIR **API URL soneki** ekleyin.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Boş API oluşturma":::

### <a name="add-get-operation-to-the-api"></a>API 'ye GET işlemi ekleme

[Service Fabric arka uç hizmeti dağıtma](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)bölümünde gösterildiği gibi, Service Fabric kümesine dağıtılan örnek ASP.NET Core HIZMETI, URL yolundaki tek BIR http get işlemini destekler `/api/values` .

Bu yoldaki varsayılan yanıt iki dizenin bir JSON dizisidir:

```json
["value1", "value2"]
```

API Management ile olan tümleştirmesini test etmek için, şu yolda bulunan API 'ye karşılık gelen GET işlemini ekleyin `/api/values` :

1. Önceki adımda oluşturduğunuz API’yi seçin.
1. **+ İşlem Ekle**’yi seçin.
1. **Ön uç** penceresinde, aşağıdaki değerleri girin ve **Kaydet**' i seçin.

     | Ayar             | Değer                             | 
    |---------------------|-----------------------------------|
    | **Görünen ad**    | *Test arka ucu*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="API 'ye GET işlemi Ekle":::

### <a name="configure-set-backend-policy"></a>`set-backend`İlkeyi Yapılandır

[`set-backend-service`](api-management-transformation-policies.md#SetBackendService)İlkeyi test API 'sine ekleyin.

1. **Tasarım** sekmesinde, **gelen işleme** bölümünde, kod Düzenleyicisi ( **</>** ) simgesini seçin. 
1. İmleci **&lt; gelen &gt;** öğesinin içine yerleştir
1. Aşağıdaki ilke ifadesini ekleyin. İçinde `backend-id` Service Fabric arka ucunuzun adını yerine koyun.

   , `sf-resolve-condition` Küme bölümü çözümlenmemişse bir yeniden deneme durumudur. Arka uç yapılandırılırken yeniden deneme sayısı ayarlandı.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. **Kaydet**’i seçin.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Set-arka uç hizmeti ilkesini yapılandırma":::

### <a name="test-backend-api"></a>Test arka uç API 'SI

1. **Test** sekmesinde, önceki bölümde oluşturduğunuz **Get** işlemini seçin.
1. **Gönder**’i seçin.

Düzgün yapılandırıldığında, HTTP yanıtı bir HTTP başarı kodu gösterir ve arka uç Service Fabric hizmetinden döndürülen JSON 'u görüntüler.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Arka uca test Service Fabric":::

## <a name="next-steps"></a>Sonraki adımlar

* İstekleri arka uca iletmek için [ilkeleri yapılandırmayı](api-management-advanced-policies.md) öğrenin
* Backends, API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)veya [Azure Resource Manager şablonları](../service-fabric/service-fabric-tutorial-deploy-api-management.md) kullanılarak da yapılandırılabilir

