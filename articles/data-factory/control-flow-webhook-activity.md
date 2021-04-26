---
title: Azure Data Factory Web kancası etkinliği
description: Web kancası etkinliği, eklenen veri kümesini kullanıcının belirttiği ölçütlere göre doğrulayıp işlem hattının yürütülmesine devam etmez.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4c3ff5d7139f4167769f78aa858c7d7a693539a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785946"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory Web kancası etkinliği

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Web kancası etkinliği, özel kodunuzla işlem hattı yürütülmesini denetleyebilir. Web kancası etkinliğiyle, müşterilerin kodu bir uç nokta çağırabilir ve geri çağırma URL 'SI geçirebilir. İşlem hattı çalıştırması, bir sonraki etkinliğe geçmeden önce geri çağırma çağrısını bekler.

> [!IMPORTANT]
> Web kancası etkinliği artık hata durumunu ve özel iletileri etkinme ve işlem hattına geri yüklemenize olanak tanır. _Reportstatusoncallback_ öğesini true olarak ayarlayın ve StatusCode ve geri çağırma yükünde _hata_ _kodu_ ekleyin. Daha fazla bilgi için bkz. [ek notlar](#additional-notes) bölümü.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
**name** | Web kancası etkinliğinin adı. | Dize | Yes |
**türüyle** | "Web kancası" olarak ayarlanmalıdır. | Dize | Yes |
**yöntemidir** | Hedef uç nokta için REST API yöntemi. | Dize. Desteklenen tür "POST" dır. | Yes |
**'deki** | Hedef uç nokta ve yol. | Bir dizenin **ResultType** değeri olan bir dize veya ifade. | Yes |
**bilgisinde** | İsteğe gönderilen üst bilgiler. İşte bir istek üzerinde dili ve türü ayarlayan bir örnek: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Bir dizenin **ResultType** değeri olan bir dize veya ifade. | Evet. `Content-Type`Gibi bir üst bilgi `"headers":{ "Content-Type":"application/json"}` gereklidir. |
**bölümü** | Uç noktaya gönderilen yükü temsil eder. | JSON **değeri JSON** olan geçerli JSON veya bir ifade. İstek yükünün şeması için bkz. [istek yük şeması](./control-flow-web-activity.md#request-payload-schema) . | Yes |
**yetkilendirmesi** | Uç noktayı çağırmak için kullanılan kimlik doğrulama yöntemi. Desteklenen türler şunlardır "temel" ve "ClientCertificate". Daha fazla bilgi için bkz. [Kimlik doğrulaması](./control-flow-web-activity.md#authentication). Kimlik doğrulaması gerekmiyorsa, bu özelliği dışlayın. | Bir dizenin **ResultType** değeri olan bir dize veya ifade. | No |
**timeout** | Etkinliğin **Callbackuri** tarafından çağrılması için belirtilen geri çağırma için bekleyeceği süre. Varsayılan değer 10 dakikadır ("00:10:00"). Değerler, *d* TimeSpan biçimine sahiptir. *SS*:*dd*:*SS*. | Dize | No |
**Geri aramada durum bildir** | Bir kullanıcının Web kancası etkinliğinin başarısız durumunu rapormasına olanak sağlar. | Boole | No |

## <a name="authentication"></a>Kimlik Doğrulaması

Web kancası etkinliği aşağıdaki kimlik doğrulama türlerini destekler.

### <a name="none"></a>Yok

Kimlik doğrulaması gerekmiyorsa, **kimlik doğrulama** özelliğini eklemeyin.

### <a name="basic"></a>Temel

Temel kimlik doğrulamasıyla kullanılacak kullanıcı adını ve parolayı belirtin.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>İstemci sertifikası

PFX dosyası ve parola için Base64 olarak kodlanmış içeriği belirtin.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Yönetilen kimlik

Erişim belirtecinin istendiği Kaynak URI 'sini belirtmek için Data Factory 'nin yönetilen kimliğini kullanın. Azure Kaynak yönetimi API 'sini çağırmak için kullanın `https://management.azure.com/` . Yönetilen kimliklerin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure kaynaklarına yönelik yönetilen kimlikler genel bakış](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Data Factory 'niz bir git deposu ile yapılandırıldıysa, temel veya istemci sertifikası kimlik doğrulamasını kullanmak için kimlik bilgilerinizi Azure Key Vault depolamanız gerekir. Azure Data Factory, parolaları git 'te depolamaz.

## <a name="additional-notes"></a>Ek notlar

Data Factory, URL uç noktasına gönderilen gövdede **Callbackuri** ek özelliğini geçirir. Data Factory, belirtilen zaman aşımı değerinden önce bu URI 'nin çağrılmasını bekliyor. URI çağrılmazsa, etkinlik "zaman aşımına uğradı" durumuyla başarısız olur.

Özel uç noktaya yapılan çağrı başarısız olursa Web kancası etkinliği başarısız olur. Herhangi bir hata iletisi geri çağırma gövdesine eklenebilir ve sonraki bir etkinlikte kullanılabilir.

Her REST API çağrısı için, uç nokta bir dakika içinde yanıt vermezse istemci zaman aşımına uğrar. Bu davranış, standart HTTP en iyi uygulamadır. Bu sorunu gidermek için 202 bir model uygulayın. Geçerli durumda, uç nokta 202 (kabul edildi) döndürür ve istemci yoklar.

İstekteki bir dakikalık zaman aşımı, etkinlik zaman aşımı ile hiçbir şey yapmaz. İkincisi, **Callbackuri** tarafından belirtilen geri çağırma işlemini beklemek için kullanılır.

Geri çağırma URI 'sine geri geçirilen gövde geçerli bir JSON olmalıdır. `Content-Type`Üstbilgiyi olarak ayarlayın `application/json` .

Geri çağırma özelliğinde **rapor durumunu** kullandığınızda, geri çağırma yaptığınızda gövdeye aşağıdaki kodu eklemeniz gerekir:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Sonraki adımlar

Data Factory tarafından desteklenen aşağıdaki denetim akışı etkinliklerine bakın:

- [If Condition Etkinliği](control-flow-if-condition-activity.md)
- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Her Bir Etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web Etkinliği](control-flow-web-activity.md)
- [Until Etkinliği](control-flow-until-activity.md)