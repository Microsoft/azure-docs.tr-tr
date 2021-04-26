---
title: Tetikleyici ve eylem türleri için şema başvurusu
description: Azure Logic Apps Iş akışı tanımlama dili tetikleyicisi ve eylem türleri için şema başvuru kılavuzu
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 95f2e47d3cf0b967f42b988b565da3643796534d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490769"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Azure Logic Apps tetikleyici ve eylem türleri için şema başvurusu Kılavuzu

Bu başvuru, mantıksal uygulamanızın [Iş akışı Tanım Dili](../logic-apps/logic-apps-workflow-definition-language.md)tarafından tanımlanan ve doğrulanan temel alınan iş akışı tanımındaki Tetikleyicileri ve eylemleri tanımlamak için kullanılan genel türleri açıklar. Mantıksal uygulamalarınızda kullanabileceğiniz belirli bağlayıcı tetikleyicilerini ve eylemlerini bulmak için, [Bağlayıcılar genel bakış](/connectors/)altındaki listeye bakın.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Tetikleyicilere genel bakış

Her iş akışı, iş akışını örnekleyip Başlatan çağrıları tanımlayan bir tetikleyici içerir. Genel tetikleyici kategorileri şunlardır:

* Düzenli aralıklarla bir hizmetin uç noktasını denetleyen bir *yoklama* tetikleyicisi

* Uç nokta için bir abonelik oluşturan ve bir geri çağırma URL 'si sağlayan bir *gönderme* tetikleyicisi, belirtilen olay gerçekleştiğinde veya veriler kullanılabilir olduğunda bitiş noktasının tetikleyiciyi bildirebilmesi için bir *geri arama URL 'si* sağlar. Tetikleyici daha sonra, başlamadan önce uç noktanın yanıtını bekler.

Tetikleyiciler, bazı isteğe bağlı olsa da, bu en üst düzey öğelere sahiptir:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Tetikleyici-adı*> | Dize | Tetikleyicinin adı | 
| <*tetikleyici-tür*> | Dize | "Http" veya "ApiConnection" gibi tetikleyici türü | 
| <*tetikleyici-girişler*> | JSON Nesnesi | Tetikleyicinin davranışını tanımlayan girişler | 
| <*zaman birimi*> | Dize | Tetikleyicinin ne sıklıkla çalıştığını açıklayan zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay" | 
| <*zaman birimi sayısı*> | Tamsayı | Tetikleyicinin yeniden tetiklenmesi için bekleyeceği zaman birimi sayısı olan sıklık temelinde tetikleyicinin ne sıklıkta tetikleyeceğini belirten bir değer <p>En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay </br>Gün: 1-500 gün </br>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6, sıklık ise "month" ise, yineleme 6 aydır. | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*dizi-koşul*> | Dizi | İş akışının çalıştırılıp çalıştırılmayacağını tespit eden bir veya daha fazla [koşul](#trigger-conditions) içeren bir dizi. Yalnızca Tetikleyiciler için kullanılabilir. | 
| <*çalışma zamanı-yapılandırma-seçenekler*> | JSON Nesnesi | Özellikleri ayarlayarak tetikleyici çalışma zamanı davranışını değiştirebilirsiniz `runtimeConfiguration` . Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options). | 
| <*Spton-ifadesi*> | Dize | Dizi döndüren Tetikleyiciler için, dizi öğelerini işlemek üzere birden çok iş akışı örneğine [ayıran  veya](#split-on-debatch) içermeyen bir ifade belirtebilirsiniz. | 
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Tetikleyici türleri listesi

Her tetikleyici türünün, tetikleyicisinin davranışını tanımlayan farklı bir arabirimi ve girişleri vardır. 

### <a name="built-in-triggers"></a>Yerleşik Tetikleyiciler

| Tetikleyici türü | Açıklama | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Tüm uç noktaları denetler veya *yoklar* . Bu uç noktanın, `202` zaman uyumsuz bir model kullanılarak veya bir dizi döndürerek belirli bir tetikleyici sözleşmesine uyması gerekir. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Mantıksal uygulamanız için çağrılabilir bir uç nokta oluşturur, ancak kayıt veya kayıt silme için belirtilen URL 'YI çağırır. |
| [**Yineleme**](#recurrence-trigger) | Tanımlı bir zamanlamaya göre ateşlenir. Bu tetikleyiciyi tetikleyebilmeniz için gelecek bir tarih ve saat ayarlayabilirsiniz. Sıklık temelinde, iş akışınızı çalıştırmak için saatler ve günler de belirtebilirsiniz. | 
| [**İstek**](#request-trigger)  | Mantıksal uygulamanız için çağrılabilir bir uç nokta oluşturur ve "el ile" tetikleyicisi olarak da bilinir. Örneğin, bkz. [http uç noktaları Ile çağrı, tetikleyici veya iç içe iş akışları](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Yönetilen API Tetikleyicileri

| Tetikleyici türü | Açıklama | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Bir uç noktayı [Microsoft tarafından yönetilen API 'ler](../connectors/apis-list.md)kullanarak denetler veya *yoklar* . | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Abone ol ve abonelik kaldırma için [Microsoft tarafından yönetilen API 'ler](../connectors/apis-list.md) çağırarak mantıksal uygulamanız için çağrılabilir bir uç nokta oluşturur. | 
||| 

## <a name="triggers---detailed-reference"></a>Tetikleyiciler - Ayrıntılı başvuru

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection tetikleyicisi  

Bu tetikleyici, [Microsoft tarafından yönetilen API 'ler](../connectors/apis-list.md) kullanarak bir uç noktayı denetler veya *yoklar* ve bu tetikleyicinin parametreleri uç noktaya göre farklılık gösterebilir. Bu tetikleyici tanımındaki birçok bölüm isteğe bağlıdır. Tetikleyicinin davranışı bölümlerin dahil edilip edilmeyeceğini gösterir.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Gerekli*

| Değer | Tür | Açıklama |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Dize | Tetikleyicinin adı |
| <*bağlantı adı*> | Dize | İş akışının kullandığı yönetilen API bağlantısı için ad |
| <*Yöntem-tür*> | Dize | Yönetilen API ile iletişim için HTTP yöntemi: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-işlem*> | Dize | Çağrılacak API işlemi |
| <*zaman birimi*> | Dize | Tetikleyicinin ne sıklıkla çalıştığını açıklayan zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay" |
| <*zaman birimi sayısı*> | Tamsayı | Tetikleyicinin yeniden tetiklenmesi için bekleyeceği zaman birimi sayısı olan sıklık temelinde tetikleyicinin ne sıklıkta tetikleyeceğini belirten bir değer <p>En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay </br>Gün: 1-500 gün </br>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6, sıklık ise "month" ise, yineleme 6 aydır. |
||||

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*sorgu-Parametreler*> | JSON Nesnesi | API çağrısıyla birlikte içerilecek herhangi bir sorgu parametresi. Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. | 
| <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örnekleri [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar (eşzamanlı veya paralel) aynı anda çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). | 
| <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | 
| <*Spton-ifadesi*> | Dize | Bu ifade, diziler döndüren Tetikleyiciler için, "for each" döngüsünü kullanmak yerine her bir dizi öğesi için bir iş akışı örneği oluşturup çalıştırabilmeniz için kullanılacak diziye başvurur. <p>Örneğin, bu ifade, tetikleyicinin gövde içeriği içinde döndürülen dizideki bir öğeyi temsil eder: `@triggerbody()?['value']` |
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). |
||||

*Çıkışlar*
 
| Öğe | Tür | Açıklama |
|---------|------|-------------|
| bilgisinde | JSON Nesnesi | Yanıttaki üstbilgiler |
| body | JSON Nesnesi | Yanıttaki gövde |
| durum kodu | Tamsayı | Yanıttaki durum kodu |
|||| 

*Örnek*

Bu tetikleyici tanımı, bir iş veya okul hesabı için gelen kutusu içindeki her gün e-postayı denetler:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Apiconnectionweb kancası tetikleyicisi

Bu tetikleyici, [Microsoft tarafından yönetilen BIR API](../connectors/apis-list.md)kullanarak bir uç noktaya bir abonelik isteği gönderir, uç noktanın yanıt gönderebildiği ve bitiş noktasının yanıt vermesini beklediği bir *geri çağırma URL 'si* sağlar. Daha fazla bilgi için bkz. [Endpoint abonelikleri](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*bağlantı adı*> | Dize | İş akışının kullandığı yönetilen API bağlantısı için ad | 
| <*gövde-içerik*> | JSON Nesnesi | Yönetilen API 'ye yük olarak göndermek için herhangi bir ileti içeriği | 
||||

*İsteğe bağlı*

| Değer | Tür | Açıklama |
|-------|------|-------------|
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*sorgu-Parametreler*> | JSON Nesnesi | API çağrısıyla birlikte içerilecek sorgu parametreleri <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. |
| <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örnekleri [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar (eşzamanlı veya paralel) aynı anda çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). |
| <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | 
| <*Spton-ifadesi*> | Dize | Bu ifade, diziler döndüren Tetikleyiciler için, "for each" döngüsünü kullanmak yerine her bir dizi öğesi için bir iş akışı örneği oluşturup çalıştırabilmeniz için kullanılacak diziye başvurur. <p>Örneğin, bu ifade, tetikleyicinin gövde içeriği içinde döndürülen dizideki bir öğeyi temsil eder: `@triggerbody()?['value']` |
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

*Örnek*

Bu tetikleyici tanımı Office 365 Outlook API 'sine abone olur, API uç noktası için bir geri çağırma URL 'SI sağlar ve yeni bir e-posta geldiğinde bitiş noktasının yanıt vermesini bekler.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP tetikleyicisi

Bu tetikleyici belirtilen yineleme zamanlaması temelinde belirtilen HTTP veya HTTPS uç noktasına bir istek gönderir. Tetikleyici daha sonra iş akışının çalışıp çalışmadığını belirleme yanıtını denetler. Daha fazla bilgi için bkz. [http veya https üzerinden hizmet uç noktalarını çağırma Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Gerekli*

| Özellik | Değer | Tür | Açıklama |
|----------|-------|------|-------------|
| `method` | <*Yöntem-tür*> | Dize | Giden isteği göndermek için kullanılacak yöntem: "GET", "PUT", "POST", "PATCH" veya "DELETE" |
| `uri` | <*HTTP-veya-HTTPS uç noktası-URL*> | Dize | Giden isteği göndermek istediğiniz HTTP veya HTTPS uç noktası URL 'SI. En büyük dize boyutu: 2 KB <p>Bir Azure hizmeti veya kaynağı için, bu URI söz dizimi kaynak KIMLIĞI ve erişmek istediğiniz kaynağın yolunu içerir. |
| `frequency` | <*zaman birimi*> | Dize | Tetikleyicinin ne sıklıkla çalıştığını açıklayan zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay" |
| `interval` | <*zaman birimi sayısı*> | Tamsayı | Tetikleyicinin yeniden tetiklenmesi için bekleyeceği zaman birimi sayısı olan sıklık temelinde tetikleyicinin ne sıklıkta tetikleyeceğini belirten bir değer <p>En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay </br>Gün: 1-500 gün </br>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6, sıklık ise "month" ise, yineleme 6 aydır. |
|||||

*İsteğe bağlı*

| Özellik | Değer | Tür | Açıklama |
|----------|-------|------|-------------|
| `headers` | <*üst bilgi-içerik*> | JSON Nesnesi | İsteğe dahil etmeniz gereken tüm üstbilgiler <p>Örneğin, dili ve türünü ayarlamak için: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*sorgu-Parametreler*> | JSON Nesnesi | İstekte kullanmanız gereken herhangi bir sorgu parametresi <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesne `?api-version=2018-01-01` isteğe ekler. |
| `body` | <*gövde-içerik*> | JSON Nesnesi | İstekle birlikte yük olarak gönderilmek üzere ileti içeriği |
| `authentication` | <*kimlik doğrulama-tür-ve-özellik-değerler*> | JSON Nesnesi | İsteğin giden isteklerin kimliğini doğrulamak için kullandığı kimlik doğrulama modeli. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Zamanlayıcı ötesinde, `authority` özellik desteklenir. Belirtilmediğinde, varsayılan değer olur `https://management.azure.com/` , ancak farklı bir değer kullanabilirsiniz. |
| `retryPolicy` > `type` | <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örnekleri [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar (eşzamanlı veya paralel) aynı anda çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). |
| `operationOptions` | <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). |
|||||

*Çıkışlar*

| Öğe | Tür | Açıklama |
|---------|------|-------------|
| `headers` | JSON Nesnesi | Yanıttaki üstbilgiler |
| `body` | JSON Nesnesi | Yanıttaki gövde |
| `status code` | Tamsayı | Yanıttaki durum kodu |
||||

*Gelen istekler için gereksinimler*

Mantıksal uygulamanızla iyi bir şekilde çalışmak için uç noktanın belirli bir tetikleyici düzenine veya sözleşmesine uyması ve bu yanıt özelliklerini tanıması gerekir:

| Özellik | Gerekli | Açıklama |
|----------|----------|-------------|
| Durum kodu | Yes | "200 OK" durum kodu bir çalıştırma başlatır. Başka herhangi bir durum kodu çalıştırma başlamaz. |
| Retry-After üst bilgisi | Hayır | Mantıksal uygulamanız bitiş noktasını yeniden yokladığı saniye sayısı |
| Konum üst bilgisi | Hayır | Sonraki yoklama aralığında çağrılacak URL. Belirtilmemişse, özgün URL kullanılır. |
|||| 

*Farklı istekler için örnek davranışlar*

| Durum kodu | Sonra yeniden dene | Davranış | 
|-------------|-------------|----------|
| 200 | seçim | İş akışını çalıştırın, sonra tanımlanan tekrardan sonra daha fazla veri için yeniden denetleyin. | 
| 200 | 10 saniye | İş akışını çalıştırın, sonra 10 saniye sonra daha fazla veri için yeniden kontrol edin. |  
| 202 | 60 saniye | İş akışını tetiklemez. Sonraki deneme bir dakika içinde gerçekleşir ve tanımlanan tekrarya tabidir. Tanımlanan yinelenme bir dakikadan azsa, yeniden deneme üst bilgisi önceliklidir. Aksi takdirde, tanımlanan yinelenme kullanılır. | 
| 400 | seçim | Hatalı istek, iş akışını çalıştırmayın. Eğer `retryPolicy` tanımlanmamışsa, varsayılan ilke kullanılır. Yeniden deneme sayısına ulaşıldığında, tetikleyici tanımlanan tekrardan sonra verileri yeniden denetler. | 
| 500 | seçim| Sunucu hatası, iş akışını çalıştırmayın. Eğer `retryPolicy` tanımlanmamışsa, varsayılan ilke kullanılır. Yeniden deneme sayısına ulaşıldığında, tetikleyici tanımlanan tekrardan sonra verileri yeniden denetler. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Httpweb kancası tetikleyicisi  

Bu tetikleyici, belirtilen uç nokta URL 'sini çağırarak bir aboneliği kaydedebilmesi için bir uç nokta oluşturarak mantıksal uygulamanızı çağrılabilir hale getirir. Bu tetikleyiciyi iş akışınızda oluşturduğunuzda, giden bir istek aboneliği kaydetme çağrısını yapar. Bu şekilde, tetikleyici olayları dinlemeye başlayabilir. Bir işlem bu tetikleyiciyi geçersiz yaptığında, bir giden istek otomatik olarak aboneliğini iptal etme çağrısını yapar. Daha fazla bilgi için bkz. [Endpoint abonelikleri](#subscribe-unsubscribe).

Ayrıca, bir **Httpweb kancası** tetikleyicisi üzerinde [zaman uyumsuz sınırlar](#asynchronous-limits) belirtebilirsiniz. Tetikleyicinin davranışı, kullandığınız veya atladığınızda bölümlere bağlıdır.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

<*Method-type*> gibi bazı değerler hem hem de nesneleri için kullanılabilir `"subscribe"` `"unsubscribe"` .

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Yöntem-tür*> | Dize | Abonelik isteği için kullanılacak HTTP yöntemi: "GET", "PUT", "POST", "PATCH" veya "DELETE" | 
| <*uç nokta-abone ol-URL*> | Dize | Abonelik isteğinin gönderileceği uç nokta URL 'SI | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Yöntem-tür*> | Dize | İptal isteği için kullanılacak HTTP yöntemi: "GET", "PUT", "POST", "PATCH" veya "DELETE" | 
| <*uç nokta-abonelik-URL*> | Dize | İptal isteğinin gönderileceği uç nokta URL 'SI | 
| <*gövde-içerik*> | Dize | Abonelik veya iptal isteğinde göndermek için herhangi bir ileti içeriği | 
| <*kimlik doğrulama türü*> | JSON Nesnesi | İsteğin giden isteklerin kimliğini doğrulamak için kullandığı kimlik doğrulama modeli. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örneklerinin hepsi aynı anda (aynı anda veya paralel) [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). | 
| <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | 
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

*Çıkışlar* 

| Öğe | Tür | Açıklama |
|---------|------|-------------| 
| bilgisinde | JSON Nesnesi | Yanıttaki üstbilgiler | 
| body | JSON Nesnesi | Yanıttaki gövde | 
| durum kodu | Tamsayı | Yanıttaki durum kodu | 
|||| 

*Örnek*

Bu tetikleyici, belirtilen uç noktaya bir abonelik oluşturur, benzersiz bir geri çağırma URL 'SI sağlar ve yeni yayımlanmış teknoloji makalelerini bekler.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Yinelenme tetikleyicisi  

Bu tetikleyici, belirtilen yinelenme zamanlaması temelinde çalışır ve düzenli olarak çalışan bir iş akışı oluşturmak için kolay bir yol sağlar.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*zaman birimi*> | Dize | Tetikleyicinin ne sıklıkla çalıştığını açıklayan zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay" | 
| <*zaman birimi sayısı*> | Tamsayı | Tetikleyicinin yeniden tetiklenmesi için bekleyeceği zaman birimi sayısı olan sıklık temelinde tetikleyicinin ne sıklıkta tetikleyeceğini belirten bir değer <p>En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay </br>Gün: 1-500 gün </br>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6, sıklık ise "month" ise, yineleme 6 aydır. | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*başlangıç-tarih-saat--biçimlendirme-YYYY-MM-DDThh: mm: ss*> | Dize | Bu biçimdeki başlangıç tarihi ve saati: <p>YYYY-MM-DDThh: mm: ss saat dilimi belirtirseniz <p>-veya- <p>YYYY-MM-DDThh: mm: ssZ saat dilimi belirtmezseniz <p>Örneğin, 18 Eylül 2017, 2:00 PM üzerinde istiyorsanız, "2017-09-18T14:00:00" belirtin ve "Pasifik Standart Saati" gibi bir saat dilimi belirtin ya da saat dilimi olmadan "2017-09-18T14:00:00Z" belirtin. <p>**Note:** Bu başlangıç saati, gelecekte en fazla 49 yıla sahiptir ve UTC [8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) UTC [Tarih saat biçiminde](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)izlemelidir, ancak [UTC bir fark](https://en.wikipedia.org/wiki/UTC_offset)olmadan gelmelidir. Saat dilimi belirtmezseniz, sonunda boşluk olmadan "Z" harfini eklemeniz gerekir. Bu "Z", eşdeğer [nadeniz saati](https://en.wikipedia.org/wiki/Nautical_time)anlamına gelir. <p>Basit zamanlamalar için başlangıç zamanı ilk oluşumdır, ancak karmaşık zamanlamalar için tetikleyici başlangıç zamanından daha önce harekete geçmez. Başlangıç tarihleri ve zamanları hakkında daha fazla bilgi için bkz. [düzenli olarak çalışan görevler oluşturma ve zamanlama](../connectors/connectors-native-recurrence.md). | 
| <*Saat dilimi*> | Dize | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici [UTC sapmasını](https://en.wikipedia.org/wiki/UTC_offset)kabul etmez. Uygulamak istediğiniz saat dilimini belirtin. | 
| <*bir veya daha çok saat-işareti*> | Tamsayı veya tamsayı dizisi | İçin "gün" veya "hafta" belirtirseniz `frequency` , iş akışını çalıştırmak istediğiniz günün saatleri olarak virgülle ayırarak 0 ile 23 arasında bir veya daha fazla tamsayı belirtebilirsiniz. <p>Örneğin, "10", "12" ve "14" belirtirseniz, saat işaretleri olarak 10 har, 12 PM ve 2 PM alırsınız. | 
| <*bir veya daha fazla-dakika-işareti*> | Tamsayı veya tamsayı dizisi | İçin "gün" veya "hafta" belirtirseniz `frequency` , bir veya daha fazla tamsayı belirterek, iş akışını çalıştırmak istediğiniz saatin dakikası olarak virgülle ayırarak 0 ile 59 arasında bir veya daha fazla tamsayı belirtebilirsiniz. <p>Örneğin, "30" öğesini dakika işareti olarak belirtebilir ve günün saati için önceki örneği kullanarak 10:30, 12:30 PM ve 2:30 PM kazanın. | 
| weekDays | Dize veya dize dizisi | İçin "Week" belirtirseniz `frequency` , iş akışını çalıştırmak istediğinizde, virgülle ayırarak bir veya daha fazla gün belirtebilirsiniz: "Pazartesi", "Salı", "Çarşamba", "Perşembe", "Cuma", "Cumartesi" ve "Pazar" | 
| <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örneklerinin hepsi aynı anda (aynı anda veya paralel) [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). | 
| <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | 
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

*Örnek 1*

Bu temel yinelenme tetikleyicisi her gün çalışır:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Örnek 2*

Tetikleyicinin tetiklenmesi için bir başlangıç tarihi ve saati belirtebilirsiniz. Bu yineleme tetikleyicisi belirtilen tarihte başlar ve sonra günlük olarak başlatılır:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Örnek 3*

Bu yineleme tetikleyicisi, 9 Eylül 2017 ' de 2:00 PM tarihinde başlar ve her Pazartesi, 10:30, 12:30 ve 2:30 Pasifik standart saati ile haftada bir saatte bir ateşlenir:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Daha fazla bilgi ve bu tetikleyicinin örnekleri için bkz. [düzenli olarak çalışan görevler oluşturma ve zamanlama](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>İstek tetikleyicisi

Bu tetikleyici, gelen istekleri kabul edebilecek bir uç nokta oluşturarak mantıksal uygulamanızı çağrılabilir hale getirir. Bu tetikleyici için, tetikleyicinin gelen istekten aldığı yük veya girişleri açıklayan ve doğrulayan bir JSON şeması sağlayın. Şema ayrıca tetikleyici özelliklerini iş akışındaki sonraki eylemlerden daha kolay başvuruya de olanak sağlar.

Bu tetikleyiciyi çağırmak için, `listCallbackUrl` [Iş akışı hizmeti REST API](/rest/api/logic/workflows)açıklanan API 'yi kullanmanız gerekir. Bu tetikleyiciyi bir HTTP uç noktası olarak kullanmayı öğrenmek için bkz. [http uç noktaları Ile çağrı, tetikleyici veya iç içe iş akışları](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Özellik adı*> | Dize | Yükü açıklayan JSON şemasında bir özelliğin adı | 
| <*Özellik türü*> | Dize | Özelliğin türü | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Yöntem-tür*> | Dize | Gelen isteklerin mantıksal uygulamanızı çağırmak için kullanması gereken Yöntem: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*göreli-yol-for-kabul parametresi*> | Dize | Endpoint URL 'nizin kabul edebileceği parametrenin göreli yolu | 
| <*gerekli-özellikler*> | Dizi | Değer gerektiren bir veya daha fazla özellik | 
| <*en fazla çalıştırma*> | Tamsayı | Varsayılan olarak, iş akışı örneklerinin hepsi aynı anda (aynı anda veya paralel) [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık değiştirme](#change-trigger-concurrency). | 
| <*en fazla çalıştırma-kuyruk*> | Tamsayı | İş akışınız, özelliği temel alarak değiştirebileceğiniz en fazla örnek sayısını zaten çalıştırıyorsa, `runtimeConfiguration.concurrency.runs` tüm yeni çalıştırmalar bu kuyruğa [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar yerleştirilir. Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | 
| <*işlem-seçenek*> | Dize | Özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

*Örnek*

Bu tetikleyici, gelen bir isteğin tetikleyiciyi çağırmak için HTTP POST metodunu kullanması gerektiğini ve gelen istekten gelen girişi doğrulayan bir şema içerdiğini belirtir:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Tetikleyici koşulları

Herhangi bir tetikleyici ve yalnızca Tetikleyiciler için, iş akışının çalıştırılıp çalıştırılmayacağını tespit eden koşullar için bir veya daha fazla ifade içeren bir dizi ekleyebilirsiniz. `conditions`Özelliği iş akışınızda bir tetikleyiciye eklemek için, kod görünümü düzenleyicisinde mantıksal uygulamanızı açın.

Örneğin, bir tetikleyicinin yalnızca bir Web sitesi bir iç sunucu hatası döndürdüğünde, tetikleyicisinin özelliğindeki durum koduna başvurarak bunu tetikleyebelirtebilirsiniz `conditions` :

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Varsayılan olarak, bir tetikleyici yalnızca bir "200 Tamam" yanıtı alındıktan sonra ateşlenir. Bir ifade bir tetikleyicinin durum koduna başvurduğunda, tetikleyicinin varsayılan davranışı değiştirilmiştir. Bu nedenle, tetikleyicinin "200" ve "201" durum kodu gibi birden fazla durum kodu için tetiklemeyi istiyorsanız, bu ifadeyi koşulunuz olarak dahil etmeniz gerekir:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Birden çok çalıştırmayı tetikleme

Tetikleyiciniz mantıksal uygulamanızın işlemesi için bir dizi döndürürse, bazen "for each" döngüsünün her bir dizi öğesini işlemesi çok uzun sürebilir. Bunun yerine, diziyi *toplu* olarak bırakmak için Tetikleyicinizdeki **spton** özelliğini kullanabilirsiniz. Toplu işleme, dizi öğelerini böler ve her dizi öğesi için çalışan yeni bir iş akışı örneği başlatır. Bu yaklaşım, örneğin yoklama aralıkları arasında birden çok yeni öğe döndürebilecek bir uç noktayı yoklamak istediğinizde yararlıdır. Tek bir mantıksal uygulama çalıştırmasında, **Spton** 'un işleyebilmesine izin verilen en fazla dizi öğesi sayısı için bkz. [sınırlar ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Zaman uyumlu bir yanıt düzeniyle **Spton** kullanamazsınız. Tüm iş **akışları ve bir** yanıt eylemi, zaman uyumsuz olarak çalışır ve anında yanıt gönderir `202 ACCEPTED` .
>
> Eşzamanlılık tetikleme etkin olduğunda, [üst sınır](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) önemli ölçüde azalır. Öğe sayısı bu sınırı aşarsa, Spton özelliği devre dışıdır.
 
Tetikleyicinizin Swagger dosyasında bir dizi olan bir yük varsa, tetikleyicisine otomatik olarak **Spton** özelliği eklenir. Aksi takdirde, bu özelliği, toplu işlem yapmak istediğiniz diziyi içeren yanıt yükünün içine ekleyin.

*Örnek*

Bu yanıtı döndüren bir API olduğunu varsayalım: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Mantıksal uygulamanız yalnızca içindeki dizideki içeriğe ihtiyaç duyuyor `Rows` , bu nedenle aşağıdaki örnekte olduğu gibi bir tetikleyici oluşturabilirsiniz:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> `SplitOn`Komutunu kullanırsanız, dizi dışındaki özellikleri alamazsınız. Bu nedenle bu örnek için, `status` API 'den döndürülen yanıtta özelliği alamazsınız.
> 
> Özelliği yoksa, bir hatadan kaçınmak için `Rows` , bu örnek `?` işlecini kullanır.

İş akışı tanımınız `@triggerBody().name` `name` , `"customer-name-one"` ilk çalıştırandan ve ikinci çalıştırandan itibaren olan değerleri almak için artık kullanılabilir `"customer-name-two"` . Bu nedenle, tetikleyicinizin çıkışları şu örneklere benzer şekilde görünür:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Eylemlere genel bakış

Azure Logic Apps, her biri bir eylemin benzersiz davranışını tanımlayan farklı girişlerle birlikte çeşitli eylem türleri sağlar. Eylemler bu üst düzey öğelere sahiptir, ancak bazıları isteğe bağlıdır:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------|
| <*eylem-ad*> | Dize | Eylemin adı | 
| <*eylem-tür*> | Dize | Eylem türü, örneğin, "http" veya "ApiConnection"| 
| <*giriş adı*> | Dize | Eylemin davranışını tanımlayan bir girdinin adı | 
| <*giriş değeri*> | Türlerini | Dize, tamsayı, JSON nesnesi vb. olabilen giriş değeri | 
| <*önceki-tetikleyici-veya-eylem-durum*> | JSON Nesnesi | Bu geçerli eylem çalıştırılmadan hemen önce çalışması gereken tetikleyici veya eylemin adı ve sonuç durumu | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------|
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. yeniden deneme ilkeleri. | 
| <*çalışma zamanı-yapılandırma-seçenekler*> | JSON Nesnesi | Bazı eylemler için, özellikleri ayarlayarak eylemin çalışma zamanında davranışını değiştirebilirsiniz `runtimeConfiguration` . Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options). | 
| <*işlem-seçenek*> | Dize | Bazı eylemler için, özelliği ayarlayarak varsayılan davranışı değiştirebilirsiniz `operationOptions` . Daha fazla bilgi için bkz. [işlem seçenekleri](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Eylem türleri listesi

Yaygın olarak kullanılan bazı eylem türleri şunlardır: 

* Bu örnekler ve daha fazlası gibi [yerleşik eylem türleri](#built-in-actions) : 

  * HTTP veya HTTPS üzerinden uç noktaları çağırmak için [**http**](#http-action)

  * İsteklere yanıt verme [**yanıtı**](#response-action)

  * JavaScript kod parçacıklarını çalıştırmak için [**JavaScript kodunu yürütme**](#run-javascript-code)

  * Azure Işlevleri çağırma [**işlevi**](#function-action)

  * Farklı girdilerden veri oluşturan veya dönüştüren, [**JOIN**](#join-action), [**Compose**](#compose-action), [**Table**](#table-action), [**Select**](#select-action)ve diğerleri gibi veri işleme eylemleri

  * Başka bir mantıksal uygulama iş akışını çağırmak için [**Iş akışı**](#workflow-action)

* Microsoft tarafından yönetilen çeşitli bağlayıcıları ve API 'Leri çağıran [**Apiconnection**](#apiconnection-action) ve [**Apiconnectionweb KANCASı**](#apiconnectionwebhook-action) gibi [yönetilen API eylem türleri](#managed-api-actions) ; Örneğin, Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob depolama, OneDrive, GitHub ve daha fazlası

* Diğer eylemleri içeren ve iş akışı yürütmeyi düzenlemenize yardımcı olan [**IF**](#if-action), [**foreach**](#foreach-action), [**Switch**](#switch-action), [**scope**](#scope-action)ve [**until**](#until-action)gibi [iş akışı eylem türlerini denetleyin](#control-workflow-actions)

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Yerleşik eylemler

| Eylem türü | Açıklama | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Girdilerden farklı türlere sahip olabilen tek bir çıktı oluşturur. | 
| [**JavaScript kodunu Yürüt**](#run-javascript-code) | Belirli ölçütlere uyan JavaScript kod parçacıklarını çalıştırın. Kod gereksinimleri ve daha fazla bilgi için bkz. [satır içi kod ile kod parçacıkları ekleme ve çalıştırma](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Çalışmayacaktır**](#function-action) | Bir Azure Işlevi çağırır. | 
| [**HTTP**](#http-action) | Bir HTTP uç noktası çağırır. | 
| [**Katılın**](#join-action) | Dizideki tüm öğelerden bir dize oluşturur ve bu öğeleri belirli bir sınırlayıcı karakterle ayırır. | 
| [**JSON Ayrıştır**](#parse-json-action) | JSON içeriğindeki özelliklerden Kullanıcı dostu belirteçler oluşturur. Daha sonra, mantıksal uygulamanıza belirteçleri ekleyerek bu özelliklere başvurabilirsiniz. | 
| [**Sorgulayamadı**](#query-action) | Bir koşula veya filtreye bağlı olarak başka bir dizideki öğelerden bir dizi oluşturur. | 
| [**Yanıt**](#response-action) | Gelen çağrıya veya isteğe yanıt oluşturur. | 
| [**Şunu seçin:**](#select-action) | Belirtilen haritaya göre başka bir diziden öğeleri dönüştürerek JSON nesneleriyle bir dizi oluşturur. | 
| [**Tablo**](#table-action) | Bir diziden CSV veya HTML tablosu oluşturur. | 
| [**Terminate**](#terminate-action) | Etkin şekilde çalışan bir iş akışını sonlandırır. | 
| [**Wait**](#wait-action) | İş akışınızı belirtilen süre veya belirtilen tarih ve saate kadar duraklatır. | 
| [**İş akışı**](#workflow-action) | Bir iş akışını başka bir iş akışı içinde bir şekilde alır. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Yönetilen API eylemleri

| Eylem türü | Açıklama | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | [Microsoft tarafından yönetilen BIR API](../connectors/apis-list.md)kullanarak bir HTTP uç noktası çağırır. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | HTTP Web kancası gibi çalışarak [Microsoft tarafından yönetilen BIR API](../connectors/apis-list.md)kullanır. | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Denetim iş akışı eylemleri

Bu eylemler, iş akışı yürütmeyi denetlemenize ve diğer eylemleri dahil etmenize yardımcı olur. Denetim iş akışı eyleminin dışından, bu denetim iş akışı eyleminin içindeki eylemlere doğrudan başvurabilirsiniz. Örneğin, `Http` bir kapsam içinde bir eyleminiz varsa, `@body('Http')` ifadeye iş akışındaki herhangi bir yerden başvurabilirsiniz. Ancak, bir denetim iş akışı eyleminde bulunan eylemler yalnızca aynı denetim iş akışı yapısında yer alan diğer eylemlerden "daha sonra çalıştırılabilir".

| Eylem türü | Açıklama | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Bir dizideki her öğe için aynı eylemleri döngüde çalıştırın. | 
| [**Eğer**](#if-action) | Belirtilen koşulun doğru veya yanlış olduğunu temel alarak eylemleri çalıştırın. | 
| [**Kapsam**](#scope-action) | Eylemler kümesinden grup durumuna göre eylemleri çalıştırın. | 
| [**Anahtar**](#switch-action) | Deyimlerden, nesnelerden veya belirteçlerdeki değerlerin her bir durum tarafından belirtilen değerlerle eşleşmesi durumunda durumları düzenlenmiş eylemler çalıştırın. | 
| [**Until**](#until-action) | Belirtilen koşul true olana kadar eylemleri bir döngüde çalıştırın. | 
|||  

## <a name="actions---detailed-reference"></a>Eylemler-ayrıntılı başvuru

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection eylemi

Bu eylem, [Microsoft tarafından yönetilen BIR API](../connectors/apis-list.md) 'ye bir http isteği GÖNDERIR ve API ve parametrelerle ilgili bilgilerin yanı sıra geçerli bir bağlantıya yönelik bir başvuru gerektirir. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*eylem-ad*> | Dize | Bağlayıcı tarafından belirtilen eylemin adı | 
| <*api adı*> | Dize | Bağlantı için kullanılan Microsoft tarafından yönetilen API 'nin adı | 
| <*Yöntem-tür*> | Dize | API 'YI çağırmak için HTTP yöntemi: "GET", "PUT", "POST", "PATCH" veya "DELETE" | 
| <*api-işlem*> | Dize | Çağrılacak API işlemi | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*diğer eyleme özgü-giriş-Özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer tüm giriş özellikleri | 
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*sorgu-Parametreler*> | JSON Nesnesi | API çağrısıyla birlikte içerilecek herhangi bir sorgu parametresi. <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. | 
| <*diğer eyleme özgü özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer özellikler | 
|||| 

*Örnek*

Bu tanım, Microsoft tarafından yönetilen bir API olan Office 365 Outlook Bağlayıcısı için **e-posta gönder** eylemini açıklar: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Apiconnectionweb kancası eylemi

Bu eylem, [Microsoft tarafından yönetilen BIR API](../connectors/apis-list.md)kullanarak bir uç noktaya http üzerinden bir abonelik isteği gönderir, uç noktanın yanıt gönderebildiği konuma bir *geri çağırma URL 'si* sağlar ve uç noktanın yanıt vermesini bekler. Daha fazla bilgi için bkz. [Endpoint abonelikleri](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

<*Method-type*> gibi bazı değerler hem hem de nesneleri için kullanılabilir `"subscribe"` `"unsubscribe"` .

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*eylem-ad*> | Dize | Bağlayıcı tarafından belirtilen eylemin adı | 
| <*Yöntem-tür*> | Dize | Bir uç noktadan abone olmak veya aboneliği kaldırmak için kullanılacak HTTP yöntemi: "GET", "PUT", "POST", "PATCH" veya "DELETE" | 
| <*api-Subscribe-URL*> | Dize | API 'ye abone olmak için kullanılacak URI | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*API-abonelikten çıkma-URL*> | Dize | API 'den aboneliği kaldırma için kullanılacak URI | 
| <*üst bilgi-içerik*> | JSON Nesnesi | İstekte gönderilen tüm üstbilgiler <p>Örneğin, bir istek için dili ve türü ayarlamak için: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*gövde-içerik*> | JSON Nesnesi | İstekte göndermek için herhangi bir ileti içeriği | 
| <*kimlik doğrulama türü*> | JSON Nesnesi | İsteğin giden isteklerin kimliğini doğrulamak için kullandığı kimlik doğrulama modeli. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*sorgu-Parametreler*> | JSON Nesnesi | API çağrısıyla birlikte içerilecek sorgu parametreleri <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. | 
| <*diğer eyleme özgü-giriş-Özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer tüm giriş özellikleri | 
| <*diğer eyleme özgü özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer özellikler | 
|||| 

Ayrıca, bir **Apiconnectionweb kancası** eyleminde, [http zaman uyumsuz limitleriyle](#asynchronous-limits)aynı şekilde sınırlar belirtebilirsiniz.

<a name="compose-action"></a>

### <a name="compose-action"></a>Oluşturma eylemi

Bu eylem, ifadeler de dahil olmak üzere birden çok girişin tek bir çıkışını oluşturur. Hem çıktı hem de girişler, Azure Logic Apps yerel olarak desteklenen, diziler, JSON nesneleri, XML ve ikili gibi herhangi bir türde olabilir. Daha sonra eylemin çıkışını diğer eylemlerdeki bir şekilde kullanabilirsiniz. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Gerekli* 

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*oluşturma girişleri*> | Herhangi biri | Tek bir çıkış oluşturmak için girişler | 
|||| 

*Örnek 1*

<!-- markdownlint-disable MD038 -->
Bu eylem tanımı `abcdefg ` bir sonundaki boşluk ve değer ile birleştirir `1234` :
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Bu eylemin oluşturduğu çıktı aşağıda verilmiştir:

`abcdefg 1234`

*Örnek 2*

Bu eylem tanımı, içeren bir dize değişkenini ve içeren bir `abcdefg` tamsayı değişkenini birleştirir `1234` :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Bu eylemin oluşturduğu çıktı aşağıda verilmiştir:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>JavaScript kodu yürütme eylemi

Bu eylem bir JavaScript kod parçacığı çalıştırır ve sonuçları `Result` daha sonra başvurabilebir belirteç aracılığıyla döndürür.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama |
|-------|------|-------------|
| <*JavaScript-Code-kod parçacığı*> | Değişir | Çalıştırmak istediğiniz JavaScript kodu. Kod gereksinimleri ve daha fazla bilgi için bkz. [satır içi kod ile kod parçacıkları ekleme ve çalıştırma](../logic-apps/logic-apps-add-run-inline-code.md). <p>`code`Özniteliğinde, kod parçacığınızı `workflowContext` giriş olarak salt okunurdur. Bu nesne, kodunuzu tetikleyiciden gelen sonuçlara ve iş akışınızdan önceki eylemlere erişim sağlayan alt özellikler içerir. Nesnesi hakkında daha fazla bilgi için `workflowContext` bkz. [kodunuzda başvuru tetikleyicisi ve eylem sonuçları](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Bazı durumlarda gereklidir*

`explicitDependencies`Özniteliği tetikleyiciden, önceki eylemlerden veya her ikisinin de kod parçacığınızı bağımlılıkları olarak açıkça dahil etmek istediğinizi belirtir. Bu bağımlılıkları ekleme hakkında daha fazla bilgi için bkz. [satır içi kod için parametre ekleme](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Özniteliği için `includeTrigger` , `true` veya `false` değerlerini belirtebilirsiniz.

| Değer | Tür | Açıklama |
|-------|------|-------------|
| <*önceki eylemler*> | Dize dizisi | Belirtilen eylem isimlerinizi içeren bir dizi. İş akışı tanımınızda görünen eylem adlarını, eylem adlarında boşluk ("") değil, alt çizgi (_) kullanıldığı durumlarda kullanın. |
||||

*Örnek 1*

Bu eylem, mantıksal uygulamanızın adını alan ve sonuç olarak "Hello World from" metnini döndüren kodu çalıştırır \<logic-app-name> . Bu örnekte, kod `workflowContext.workflow.name` salt okunurdur nesnesi aracılığıyla özelliğe erişerek iş akışının adına başvurur `workflowContext` . Nesnesini kullanma hakkında daha fazla bilgi için `workflowContext` bkz. [kodunuzda başvuru tetikleyicisi ve eylem sonuçları](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Örnek 2*

Bu eylem, bir iş veya okul hesabına yeni bir e-posta geldiğinde tetiklenen bir mantıksal uygulamadaki kodu çalıştırır. Mantıksal uygulama Ayrıca, alınan e-postadaki içeriği onay isteğiyle birlikte ileten onay e-postası gönder eylemini kullanır.

Kod, tetikleyici özelliğinden e-posta adreslerini ayıklar `Body` ve `SelectedOption` onay eyleminden özellik değeriyle birlikte adresleri döndürür. Eylem, özniteliğe bağımlılık olarak onay e-postası gönder eylemini açıkça içerir `explicitDependencies`  >  `actions` .

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>İşlev eylemi

Bu eylem, önceden oluşturulmuş bir [Azure işlevini](../azure-functions/functions-get-started.md)çağırır.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------|  
| <*Azure-işlev KIMLIĞI*> | Dize | Çağırmak istediğiniz Azure işlevinin kaynak KIMLIĞI. Bu değerin biçimi aşağıda verilmiştir:<p>"/Subscriptions/<*Azure-Subscription-ıd*>/resourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/Sites/<*Azure-function-app-name*>/Functions/<*Azure-işlev-adı*>" | 
| <*Yöntem-tür*> | Dize | İşlevi çağırmak için kullanılacak HTTP yöntemi: "GET", "PUT", "POST", "PATCH" veya "DELETE" <p>Belirtilmemişse, varsayılan "POST" yöntemidir. | 
||||

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------|  
| <*üst bilgi-içerik*> | JSON Nesnesi | Çağrıya gönderecek tüm üstbilgiler <p>Örneğin, bir istek için dili ve türü ayarlamak için: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*gövde-içerik*> | JSON Nesnesi | İstekte göndermek için herhangi bir ileti içeriği | 
| <*sorgu-Parametreler*> | JSON Nesnesi | API çağrısıyla birlikte içerilecek sorgu parametreleri <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. | 
| <*diğer eyleme özgü-giriş-Özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer tüm giriş özellikleri | 
| <*diğer eyleme özgü özellikler*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer özellikler | 
||||

Mantıksal uygulamanızı kaydettiğinizde, Logic Apps altyapısı başvurulan işlev üzerinde bu denetimleri gerçekleştirir:

* İş akışınızın işleve erişimi olmalıdır.

* İş akışınız yalnızca standart bir HTTP tetikleyicisi veya genel JSON Web kancası tetikleyicisi kullanabilir. 

  Logic Apps altyapısı, çalışma zamanında kullanılan tetikleyicisinin URL 'sini alır ve önbelleğe alır. Ancak, herhangi bir işlem önbelleğe alınmış URL 'YI geçersiz kılar, **işlev** eylemi çalışma zamanında başarısız olur. Bu sorunu düzeltemedi, mantıksal uygulamanın tetikleyici URL 'sini yeniden alması ve önbelleğe almak için mantıksal uygulamayı yeniden kaydedin.

* İşlevin herhangi bir yolu tanımlı olamaz.

* Yalnızca "function" ve "Anonymous" yetkilendirme düzeylerine izin verilir. 

*Örnek*

Bu eylem tanımı, önceden oluşturulan "Getproductıd" işlevini çağırır:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP eylemi

Bu eylem, belirtilen HTTP veya HTTPS uç noktasına bir istek gönderir ve iş akışının çalışıp çalışmadığını belirleme yanıtını denetler. Daha fazla bilgi için bkz. [http veya https üzerinden hizmet uç noktalarını çağırma Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Gerekli*

| Özellik | Değer | Tür | Açıklama |
|----------|-------|------|-------------|
| `method` | <*Yöntem-tür*> | Dize | Giden isteği göndermek için kullanılacak yöntem: "GET", "PUT", "POST", "PATCH" veya "DELETE" |
| `uri` | <*HTTP-veya-HTTPS uç noktası-URL*> | Dize | Giden isteği göndermek istediğiniz HTTP veya HTTPS uç noktası URL 'SI. En büyük dize boyutu: 2 KB <p>Bir Azure hizmeti veya kaynağı için, bu URI söz dizimi kaynak KIMLIĞI ve erişmek istediğiniz kaynağın yolunu içerir. |
|||||

*İsteğe bağlı*

| Özellik | Değer | Tür | Açıklama |
|----------|-------|------|-------------|
| `headers` | <*üst bilgi-içerik*> | JSON Nesnesi | İsteğe dahil etmeniz gereken tüm üstbilgiler <p>Örneğin, dili ve türünü ayarlamak için: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*sorgu-Parametreler*> | JSON Nesnesi | İstekte kullanmanız gereken herhangi bir sorgu parametresi <p>Örneğin, `"queries": { "api-version": "2018-01-01" }` nesnesi `?api-version=2018-01-01` çağrıya ekler. |
| `body` | <*gövde-içerik*> | JSON Nesnesi | İstekle birlikte yük olarak gönderilmek üzere ileti içeriği |
| `authentication` | <*kimlik doğrulama-tür-ve-özellik-değerler*> | JSON Nesnesi | İsteğin giden isteklerin kimliğini doğrulamak için kullandığı kimlik doğrulama modeli. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Zamanlayıcı ötesinde, `authority` özellik desteklenir. Belirtilmediğinde, varsayılan değer olur `https://management.azure.com/` , ancak farklı bir değer kullanabilirsiniz. |
| `retryPolicy` > `type` | <*yeniden deneme davranışı*> | JSON Nesnesi | 408, 429 ve 5XX durum koduna ve tüm bağlantı özel durumlarına sahip aralıklı olmayan hatalara yönelik yeniden deneme davranışını özelleştirir. Daha fazla bilgi için bkz. [yeniden deneme ilkeleri](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*diğer eyleme özgü-giriş-Özellikler*> | <*input özelliği*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer tüm giriş özellikleri |
| <*diğer eyleme özgü özellikler*> | <*özellik-değer*> | JSON Nesnesi | Bu özel eylem için uygulanan diğer özellikler |
|||||

*Örnek*

Bu eylem tanımı, belirtilen uç noktaya bir istek göndererek en son haberleri alır:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>JOIN eylemi

Bu eylem bir dizideki tüm öğelerden bir dize oluşturur ve bu öğeleri belirtilen sınırlayıcı karakterle ayırır. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*dizide*> | Dizi | Kaynak öğeleri sağlayan dizi veya ifade. Bir ifade belirtirseniz, bu ifadeyi çift tırnak içine alın. | 
| <*ayırıcı*> | Tek karakter dizesi | Dizedeki her öğeyi ayıran karakter | 
|||| 

*Örnek*

Bu tamsayı dizisini içeren daha önce oluşturulmuş bir "myIntegerArray" değişkenine sahip olduğunuzu varsayalım: 

`[1,2,3,4]` 

Bu eylem tanımı, bir ifadede işlevini kullanarak değişkenden değerleri alır `variables()` ve bu dizeyi, virgülle ayrılmış şekilde bu değerlerle oluşturur: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>JSON eylemini Ayrıştır

Bu eylem, JSON içeriğindeki özelliklerden Kullanıcı dostu alanları veya *belirteçleri* oluşturur. Bunun yerine belirteçleri kullanarak mantıksal uygulamanızdaki bu özelliklere erişebilirsiniz. Örneğin, Azure Service Bus ve Azure Cosmos DB gibi hizmetlerden JSON çıktısı kullanmak istiyorsanız bu eylemi mantıksal uygulamanıza ekleyebilirsiniz, böylece söz konusu çıktıdaki verilere daha kolay başvurabilirsiniz.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*JSON kaynağı*> | JSON Nesnesi | Ayrıştırmak istediğiniz JSON içeriği | 
| <*JSON şeması*> | JSON Nesnesi | İşlemin, kaynak JSON içeriğini ayrıştırmak için kullandığı JSON içeriğini tanımlayan JSON şeması. <p>**İpucu**: Logic Apps tasarımcısında, bu şemayı sağlayabilir veya işlemin şemayı oluşturabilmesi için bir örnek yük sağlayabilirsiniz. | 
|||| 

*Örnek*

Bu eylem tanımı, iş akışınızda kullanabileceğiniz, ancak yalnızca **JSON ayrıştırması** eylemini izleyen eylemlerde çalıştırılan bu belirteçleri oluşturur:

`FirstName`, `LastName` ve `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

Bu örnekte, "content" özelliği ayrıştırılacak eylemin JSON içeriğini belirtir. Şemayı oluşturmak için örnek yük olarak bu JSON içeriğini de sağlayabilirsiniz.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

"Schema" özelliği, JSON içeriğini açıklamak için kullanılan JSON şemasını belirtir:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Sorgu eylemi

Bu eylem, belirtilen koşula veya filtreye bağlı olarak başka bir dizideki öğelerden bir dizi oluşturur.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*dizide*> | Dizi | Kaynak öğeleri sağlayan dizi veya ifade. Bir ifade belirtirseniz, bu ifadeyi çift tırnak içine alın. |
| <*koşul-or-filtre*> | Dize | Kaynak dizideki öğeleri filtrelemek için kullanılan koşul <p>**Note**: koşula uyan bir değer yoksa, eylem boş bir dizi oluşturur. |
|||| 

*Örnek*

Bu eylem tanımı, belirtilen değerden daha büyük değerlere sahip bir dizi oluşturur, bu iki:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Yanıt eylemi  

Bu eylem bir HTTP isteğine yanıt için yük oluşturur. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Yanıt-durum kodu*> | Tamsayı | Gelen isteğe gönderilen HTTP durum kodu. Varsayılan kod "200 Tamam" dır, ancak kod, 2xx, 4xx veya 5xx ile başlayan ancak 3xxx ile birlikte olmayan geçerli bir durum kodu olabilir. | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*Yanıt-üst bilgiler*> | JSON Nesnesi | Yanıtla birlikte içerilecek bir veya daha fazla üst bilgi | 
| <*yanıt gövdesi*> | Türlerini | Önceki bir eylemden dize, JSON nesnesi, hatta ikili içerik olabilen yanıt gövdesi | 
|||| 

*Örnek*

Bu eylem tanımı, belirtilen durum kodu, ileti gövdesi ve ileti üst bilgilerine sahip bir HTTP isteğine yanıt oluşturur:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Kısıtlamalar*

Diğer eylemlerin aksine, **Yanıt** eyleminde özel kısıtlamalar vardır: 

* İş akışınız, **Yanıt** eylemini yalnızca iş akışı bir http isteği tetikleyicisi ile başladığında kullanabilir, yani iş AKıŞıNıZıN bir http isteği tarafından tetiklenmesi gerekir.

* İş akışınız, **sıralı döngüler ve** paralel dallar dahil olmak üzere döngülerin  Içinde *olan her yerde* **Yanıt** eylemini kullanabilir. 

* Özgün istek, yalnızca **Yanıt** eylemi için gereken tüm eylemler [http zaman aşımı sınırı](../logic-apps/logic-apps-limits-and-config.md#http-limits)içinde bittiğinde iş akışınızın yanıtını alır.

  Ancak, iş akışınız, iç içe geçmiş bir iş akışı olarak başka bir mantıksal uygulamayı çağırırsa, iç iş akışı, iç içe geçmiş iş akışı tamamlanmadan önce ne kadar zaman geçene olursa olsun, iç içe geçmiş iş

* İş akışınız **Yanıt** eylemini ve zaman uyumlu bir yanıt modelini kullandığında, bu komut birden çok çalıştırma oluşturduğundan, iş akışı tetikleyici tanımında de **spton** komutunu kullanamaz. PUT yöntemi kullanıldığında bu durumu denetleyin ve true ise "Hatalı istek" yanıtı döndürür.

  Aksi takdirde, iş akışınız **Spton** komutunu ve bir **Yanıt** eylemini kullanıyorsa, iş akışı zaman uyumsuz olarak çalışır ve anında "202 kabul edildi" yanıtını döndürür.

* İş akışınızın yürütmesi **Yanıt** eylemine ulaştığında, ancak gelen istek zaten bir yanıt aldıysa, çakışma nedeniyle **Yanıt** eylemi "başarısız" olarak işaretlenir. Sonuç olarak, mantıksal uygulamanızın çalışması da "başarısız" durumu ile işaretlenir.

<a name="select-action"></a>

### <a name="select-action"></a>Eylem Seç

Bu eylem, belirtilen haritaya göre başka bir diziden öğeleri dönüştürerek JSON nesnelerine sahip bir dizi oluşturur. Çıkış dizisi ve kaynak dizisi her zaman aynı sayıda öğe içermelidir. Çıktı dizisindeki nesne sayısını değiştiremeseniz de, bu nesneler arasında özellikleri ve değerlerini ekleyebilir veya kaldırabilirsiniz. `select`Özelliği, kaynak dizideki öğeleri dönüştürmek için Haritayı tanımlayan en az bir anahtar-değer çifti belirtir. Anahtar-değer çifti, çıkış dizisindeki tüm nesneler genelinde bir özelliği ve değerini temsil eder.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Gerekli* 

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*dizide*> | Dizi | Kaynak öğeleri sağlayan dizi veya ifade. Bir ifadeyi çift tırnak işareti içine aldığınızdan emin olun. <p>**Note**: Kaynak dizisi boşsa, eylem boş bir dizi oluşturur. | 
| <*anahtar adı*> | Dize | <*ifadeden* sonuca atanan özellik adı> <p>Çıkış dizisindeki tüm nesneler arasında yeni bir özellik eklemek için, bu özellik için bir <*anahtar adı*> ve özellik değeri için> <*ifadesi* sağlayın. <p>Dizideki tüm nesnelerden bir özelliği kaldırmak için, bu özellik için <*anahtar adı*> atlayın. | 
| <*ifadesini*> | Dize | Kaynak dizideki öğeyi dönüştüren ve sonucu <*anahtar adına* atayan ifade> | 
|||| 

**Select** eylemi bir diziyi çıkış olarak oluşturur, bu nedenle bu çıktıyı kullanmak isteyen herhangi bir eylemin bir diziyi kabul etmesi ya da diziyi, tüketici eyleminin kabul ettiği türe dönüştürmeniz gerekir. Örneğin, çıkış dizisini bir dizeye dönüştürmek için, bu diziyi **oluşturma** eylemine geçirebilir ve sonra diğer eylemlerdeki **oluşturma** eyleminin çıktısına başvurabilirsiniz.

*Örnek*

Bu eylem tanımı bir tamsayı dizisinden JSON nesnesi dizisi oluşturur. Eylem, kaynak dizi boyunca yinelenir, ifadeyi kullanarak her bir tamsayı değeri alır ve her bir `@item()` değeri `number` her bir JSON nesnesinde "" özelliğine atar:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Bu eylemin oluşturduğu dizi aşağıdadır:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Bu dizi çıkışını başka eylemlerde kullanmak için, bu çıktıyı bir **oluşturma** eylemine geçirin:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Daha sonra, diğer eylemlerdeki **oluşturma** eyleminden çıktıyı kullanabilirsiniz; Örneğin, **Office 365 Outlook-e-posta gönder** eylemi:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Tablo eylemi

Bu eylem bir diziden CSV veya HTML tablosu oluşturur. JSON nesnelerine sahip diziler için bu eylem, nesnelerin özellik adlarından sütun üstbilgilerini otomatik olarak oluşturur. Diğer veri türlerine sahip diziler için, sütun üst bilgilerini ve değerlerini belirtmeniz gerekir. Örneğin bu dizi, bu eylemin sütun başlığı adları için kullanabileceği "ID" ve "Product_Name" özelliklerini içerir:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Gerekli* 

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| \<CSV *or* HTML>| Dize | Oluşturmak istediğiniz tablo için biçim | 
| <*dizide*> | Dizi | Tablo için kaynak öğeleri sağlayan dizi veya ifade <p>**Note**: Kaynak dizisi boşsa, eylem boş bir tablo oluşturur. | 
|||| 

*İsteğe bağlı*

Sütun üst bilgilerini ve değerlerini belirtmek veya özelleştirmek için, `columns` diziyi kullanın. `header-value`Çiftler aynı üstbilgi adına sahip olduğunda, değerleri bu üst bilgi adı altındaki aynı sütunda görünür. Aksi takdirde, benzersiz üst bilgi benzersiz bir sütun tanımlar.

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*sütun-adı*> | Dize | Bir sütunun üst bilgi adı | 
| <*sütun-değer*> | Herhangi biri | Bu sütundaki değer | 
|||| 

*Örnek 1*

Şu anda bu diziyi içeren daha önce oluşturulmuş bir "myItemArray" değişkenine sahip olduğunuzu varsayalım:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Bu eylem tanımı, "myItemArray" değişkeninden bir CSV tablosu oluşturur. Özelliği tarafından kullanılan ifade, `from` işlevi kullanılarak "myItemArray" öğesinden diziyi alır `variables()` :

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Bu eylemin oluşturduğu CSV tablosu aşağıda verilmiştir: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Örnek 2*

Bu eylem tanımı, "myItemArray" değişkeninden bir HTML tablosu oluşturur. Özelliği tarafından kullanılan ifade, `from` işlevi kullanılarak "myItemArray" öğesinden diziyi alır `variables()` :

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Bu eylemin oluşturduğu HTML tablosu aşağıda verilmiştir: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Elma</td></tr><tr><td>1</td><td>Portakallar</td></tr></tbody></table>

*Örnek 3*

Bu eylem tanımı, "myItemArray" değişkeninden bir HTML tablosu oluşturur. Ancak bu örnek, "Stock_ID" ve "Description" ile varsayılan sütun başlığı adlarını geçersiz kılar ve "Organic" sözcüğünü "Description" sütunundaki değerlere ekler.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Bu eylemin oluşturduğu HTML tablosu aşağıda verilmiştir: 

<table><thead><tr><th>Stock_ID</th><th>Açıklama</th></tr></thead><tbody><tr><td>0</td><td>Organik elmalar</td></tr><tr><td>1</td><td>Organik Portages</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Sonlandırma eylemi

Bu eylem bir iş akışı örneği için çalışmayı durduruyor, devam eden tüm işlemleri iptal eder, kalan eylemleri atlar ve belirtilen durumu döndürür. Örneğin, mantıksal uygulamanız bir hata durumundan tamamen çıkış yapması gerektiğinde **Sonlandır** eylemini kullanabilirsiniz. Bu eylem, zaten tamamlanmış eylemleri etkilemez ve sıralı döngüler dahil olmak üzere **ForEach** ve **until** döngüleri içinde yer alamaz.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*durumlarına*> | Dize | Çalıştırma için döndürülecek durum: "başarısız", "Iptal edildi" veya "başarılı" |
|||| 

*İsteğe bağlı*

"RunStatus" nesnesinin özellikleri yalnızca "runStatus" özelliği "başarısız" durumuna ayarlandığında geçerlidir.

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*hata-kod veya-ad*> | Dize | Hata için kod veya ad |
| <*hata-ileti*> | Dize | Hatayı açıklayan ileti veya metin ve uygulama kullanıcısının gerçekleştirebileceği eylemler | 
|||| 

*Örnek*

Bu eylem tanımı bir iş akışı çalıştırmasını durduruyor, çalışma durumunu "başarısız" olarak ayarlar ve durum, hata kodu ve bir hata iletisi döndürür:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Bekleme eylemi

Bu eylem, belirtilen zaman aralığı veya belirtilen saate kadar iş akışı yürütmeyi duraklatır, ancak her ikisine birden değil.

*Belirtilen Aralık*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Belirtilen zaman*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*birim sayısı*> | Tamsayı | **Gecikme** eylemi için beklenecek birim sayısı | 
| <*aralığında*> | Dize | **Gecikme** eylemi için beklenecek Aralık: "saniye", "dakika", "saat", "gün", "hafta", "ay" | 
| <*Tarih-saat damgası*> | Dize | **Tamamlanana kadar geciktir** , yürütmenin sürdürüleceği tarih ve saat. Bu değer [UTC Tarih saat biçimini](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kullanmalıdır. | 
|||| 

*Örnek 1*

Bu eylem tanımı, 15 dakika boyunca iş akışını duraklatır:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Örnek 2*

Bu eylem tanımı, iş akışını belirtilen saate kadar duraklatır:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>İş akışı eylemi

Bu eylem, daha önce oluşturulmuş başka bir mantıksal uygulamayı çağırır, bu da diğer mantıksal uygulama iş akışlarını dahil edebilir ve yeniden kullanabilirsiniz. Alt mantıksal uygulamanın bir yanıt döndürmesi kaydıyla, iç içe mantıksal uygulamayı izleyen Eylemler bölümünde alt veya *iç içe* mantıksal uygulamadaki çıkışları da kullanabilirsiniz.

Logic Apps motor çağırmak istediğiniz tetikleyiciye erişimi denetler, bu nedenle bu tetikleyiciye erişebildiğinizden emin olun. Ayrıca, iç içe mantıksal uygulamanın şu ölçütlere uyması gerekir:

* Tetikleyici, iç içe geçmiş mantıksal uygulamayı bir [istek](#request-trigger) veya [http](#http-trigger) tetikleyicisi gibi çağrılabilir hale getirir

* Ana mantıksal uygulamanız ile aynı Azure aboneliği

* Ana mantıksal uygulamanızdaki iç içe mantıksal uygulamadaki çıktıları kullanmak için, iç içe mantıksal uygulamanın bir [Yanıt](#response-action) eylemi olması gerekir

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*iç içe mantık-uygulama adı*> | Dize | Çağırmak istediğiniz mantıksal uygulamanın adı | 
| <*Tetikleyici-adı*> | Dize | Çağırmak istediğiniz iç içe mantıksal uygulamadaki tetikleyicinin adı | 
| <*Azure-abonelik-KIMLIĞI*> | Dize | İç içe mantıksal uygulama için Azure abonelik KIMLIĞI |
| <*Azure-Resource-Group*> | Dize | İç içe mantıksal uygulama için Azure Kaynak grubu adı |
| <*iç içe mantık-uygulama adı*> | Dize | Çağırmak istediğiniz mantıksal uygulamanın adı |
||||

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------|  
| <*üst bilgi-içerik*> | JSON Nesnesi | Çağrıya gönderecek tüm üstbilgiler | 
| <*gövde-içerik*> | JSON Nesnesi | Çağrıyla gönderecek ileti içeriği | 
||||

*Çıkışlar*

Bu eylemin çıkışları, iç içe mantıksal uygulamanın yanıt eylemine göre farklılık gösterir. İç içe mantıksal uygulama bir yanıt eylemi içermiyorsa, çıktılar boştur.

*Örnek*

"Start_search" eylemi başarıyla tamamlandıktan sonra, bu iş akışı eylemi tanımı, belirtilen girdileri geçen "Get_product_information" adlı başka bir mantıksal uygulamayı çağırır:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Denetim iş akışı eylem ayrıntıları

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach eylemi

Bu döngü eylemi bir dizi boyunca yinelenir ve her dizi öğesinde eylemler gerçekleştirir. Varsayılan olarak, "for each" döngüsü, en fazla döngü sayısına paralel olarak çalışır. Bu en büyük için bkz. [Limit ve config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). ["For each" döngülerini oluşturmayı](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)öğrenin.

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Gerekli* 

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*eylem-1... No*> | Dize | Her dizi öğesinde çalışan eylemlerin adları | 
| <*eylem-tanım-1... No*> | JSON Nesnesi | Çalışan eylemlerin tanımları | 
| <*-for-ifadesi*> | Dize | Belirtilen dizideki her öğeye başvuran ifade | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*biriktirme*> | Tamsayı | Varsayılan olarak, "for each" döngüsü yinelemeleri [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar (eşzamanlı veya paralel) aynı anda çalışır. Yeni bir <*sayı*> değeri ayarlayarak bu sınırı değiştirmek için, bkz. ["for each" döngüsü eşzamanlılık](#change-for-each-concurrency). | 
| <*işlem-seçenek*> | Dize | Paralel yerine "for each" döngüsünü sırayla çalıştırmak için, <*Operation-option*> olarak ayarlayın `Sequential` veya <*Count*> `1` , her ikisini birden değil. Daha fazla bilgi için bkz. ["for each" döngülerini sırayla çalıştırma](#sequential-for-each). | 
|||| 

*Örnek*

Bu "for each" döngüsü dizideki her öğe için bir e-posta göndererek gelen bir e-postanın eklerini içerir. Döngü, Eki inceleyen bir kişiye ek dahil bir e-posta gönderir.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Yalnızca tetikleyiciden çıkış olarak geçirilen bir diziyi belirtmek için, bu ifade tetikleyici gövdesinden <*dizi adı*> dizisini alır. Dizi yoksa, bir hatadan kaçınmak için, ifadesi `?` işlecini kullanır:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Eğer eylem

*Koşullu bir deyim* olan bu eylem, bir koşulu temsil eden bir ifadeyi değerlendirir ve koşulun doğru ya da yanlış olduğunu temel alarak farklı bir dal çalıştırır. Koşul doğru ise, koşul "başarılı" durumla işaretlenir. [Koşullu deyimler oluşturmayı](../logic-apps/logic-apps-control-flow-conditional-statement.md)öğrenin.

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*koşul*> | JSON Nesnesi | Değerlendirmek için bir ifade olabilen koşul | 
| <*eylem-1*> | JSON Nesnesi | <*koşul*> doğru olarak değerlendirildiğinde çalıştırılacak eylem | 
| <*eylem-tanım*> | JSON Nesnesi | Eylemin tanımı | 
| <*eylem-2*> | JSON Nesnesi | <*koşul*> yanlış olarak değerlendirildiğinde çalıştırılacak eylem | 
|||| 

`actions`Veya `else` nesnelerindeki eylemler şu durumları alır:

* Çalıştırıldığında ve başarılı olduğunda "başarılı oldu"
* Çalıştırıldığında ve başarısız olduğunda "başarısız oldu"
* İlgili dal çalıştırılmazsa "atlandı"

*Örnek*

Bu koşul, tamsayı değişkeni sıfırdan büyük bir değere sahip olduğunda, iş akışının bir Web sitesini denetlediğini belirtir. Değişken sıfır veya daha azsa, iş akışı farklı bir Web sitesini denetler.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Koşulların ifadeleri nasıl kullanır

İfadeleri koşullarda nasıl kullanabileceğinizi gösteren bazı örnekler şunlardır:
  
| JSON | Sonuç | 
|------|--------| 
| "Expression": " @parameters (' <*hasSpecialAction*> ')" | Yalnızca Boole ifadeleri için, koşul doğru değerlendirilen herhangi bir değer için geçirilir. <p>Diğer türleri Boolean 'a dönüştürmek için şu işlevleri kullanın: `empty()` veya `equals()` . | 
| "Expression": " @greater (Eylemler (' <*eylem*> '). Output. Value, parameters (' <*Threshold*> '))" | Karşılaştırma işlevleri için, eylem yalnızca <*eylem*> çıktısı <*eşik*> değerinden daha büyükse çalışır. | 
| "Expression": " @or (daha fazla (Eylemler (' <*eylem*> '). çıktı. değer, parametreler (' <*eşiği*> ')), daha az (Eylemler (' <*aynı eylem*> '). çıkış. değer, 100))" | Mantıksal işlevler ve iç içe geçmiş Boole ifadeleri oluşturmak için eylem, <*eylem*> çıktısı <*eşik*> değerinden veya 100 ' den fazla olduğunda çalışır. | 
| "Expression": " @equals (length (Eylemler (' <*Action*> '). çıkışlar. Errors), 0))" | Dizide herhangi bir öğe olup olmadığını denetlemek için dizi işlevlerini kullanabilirsiniz. İşlem, `errors` dizi boş olduğunda çalışır. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Kapsam eylemi

Bu eylem, bu kapsamdaki eylemlerin çalışması bittikten sonra kendi durumlarını alan, işlemleri mantıksal olarak *kapsamlar* halinde gruplandırır. Daha sonra, diğer eylemlerin çalıştırılıp çalıştırılmadığını anlamak için kapsamın durumunu kullanabilirsiniz. [Kapsamları oluşturmayı](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)öğrenin.

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------|  
| <*iç eylem-1... No*> | JSON Nesnesi | Kapsam içinde çalışan bir veya daha fazla eylem |
| <*eylem-girişler*> | JSON Nesnesi | Her eyleme ait girişler |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch eylemi

Bu eylem, *Switch deyimleri* olarak da bilinen, diğer eylemleri *durumlar* halinde düzenler ve bir varsa, varsayılan durum dışında her bir durum için bir değer atar. İş akışınız çalıştığında, **anahtar** eylemi bir ifade, nesne veya belirteçten değeri her bir durum için belirtilen değerlere göre karşılaştırır. **Switch** eylemi eşleşen bir durum bulursa, iş akışınız yalnızca söz konusu servis talebiyle ilgili eylemleri çalıştırır. **Anahtar** eylemi her çalıştığında yalnızca bir eşleşen durum var veya eşleşme yok. Hiçbir eşleşme yoksa, **anahtar** eylemi varsayılan eylemleri çalıştırır. [Switch deyimlerini oluşturmayı](../logic-apps/logic-apps-control-flow-switch-statement.md)öğrenin.

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*ifade-veya-belirteç*> | Değişir | Değerlendirilecek ifade, JSON nesnesi veya belirteç | 
| <*eylem-ad*> | Dize | Eşleşen durum için çalıştırılacak eylemin adı | 
| <*eylem-tanım*> | JSON Nesnesi | Eşleşen durum için çalıştırılacak eylemin tanımı | 
| <*eşleşen değer*> | Değişir | Değerlendirilen sonuçla karşılaştırılacak değer | 
|||| 

*İsteğe bağlı*

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*varsayılan eylem-adı*> | Dize | Eşleşen bir durum yoksa çalıştırılacak varsayılan eylemin adı | 
| <*varsayılan eylem-tanım*> | JSON Nesnesi | Eşleşen bir durum mevcut olmadığında çalıştırılacak eylemin tanımı | 
|||| 

*Örnek*

Bu eylem tanımı, kişinin onay isteği e-postasına yanıt verip vermediğini "Onayla" seçeneğini veya "Reddet" seçeneğini seçili olarak değerlendirir. Bu seçime bağlı olarak, **Switch** eylemi, farklı bir e-posta göndermek, ancak her durumda farklı bir e-posta göndermek için, eşleşen durum için eylemleri çalıştırır. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Until eylemi

Bu döngü eylemi, belirtilen koşul doğru olana kadar çalışan eylemleri içerir. Döngü, tüm diğer eylemler çalıştırıldıktan sonra koşulu son adım olarak denetler. Nesnesine birden fazla eylem ekleyebilirsiniz `"actions"` ve eylem en az bir sınır tanımlamalıdır. ["Until" döngülerini oluşturmayı](../logic-apps/logic-apps-control-flow-loops.md#until-loop)öğrenin. 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Değer | Tür | Açıklama | 
|-------|------|-------------| 
| <*eylem-ad*> | Dize | Döngü içinde çalıştırmak istediğiniz eylemin adı | 
| <*eylem-tür*> | Dize | Çalıştırmak istediğiniz eylem türü | 
| <*eylem-girişler*> | Türlerini | Çalıştırılacak eylemin girişleri | 
| <*koşul*> | Dize | Döngüdeki tüm eylemlerin çalışmasını bitirdikten sonra değerlendirilecek koşul veya ifade | 
| <*döngü sayısı*> | Tamsayı | Eylemin çalışacağı en çok döngü sayısı sınırı. Varsayılan sınır ve en yüksek sınır hakkında daha fazla bilgi için bkz. [Limit ve Configuration for Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*döngü zaman aşımı*> | Dize | Döngünün en uzun sürede çalışacağı sınır. Varsayılan `timeout` değer `PT1H` , gereken [ISO 8601 biçimidir](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> İfade, until döngüsü içindeki herhangi bir eylemin çıktısına bağımlıysa, bu eylemden kaynaklanan herhangi bir hata için hesap seçtiğinizden emin olun.

*Örnek*

Bu döngü eylemi tanımı, bu koşullardan biri karşılanana kadar belirtilen URL 'ye bir HTTP isteği gönderir:

* İstek, "200 OK" durum kodu ile bir yanıt alır.
* Döngü 60 kez çalışır.
* Döngü bir saat için çalıştırıldı.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Web kancaları ve abonelikler

Web kancası tabanlı tetikleyiciler ve eylemler uç noktaları düzenli olarak denetlemez, ancak bunun yerine bu uç noktalarda belirli olayları veya verileri bekler. Bu Tetikleyiciler ve Eylemler, uç noktaların yanıt gönderebileceği bir *geri çağırma URL 'si* sağlayarak uç noktalara *abone* olur.

Bu `subscribe` çağrı, iş akışı herhangi bir şekilde değiştirildiğinde (örneğin, kimlik bilgileri yenilendiğinde veya bir tetikleyici veya eylem için giriş parametreleri değiştiğinde) gerçekleşir. Bu çağrı, standart HTTP eylemleri ile aynı parametreleri kullanır. 

`unsubscribe`Bir işlem tetikleyiciyi veya eylemi geçersiz yaptığında çağrı otomatik olarak gerçekleşir, örneğin:

* Tetikleyiciyi silme veya devre dışı bırakma. 
* İş akışını silme veya devre dışı bırakma. 
* Aboneliği silme veya devre dışı bırakma. 

Bu çağrıları desteklemek için, `@listCallbackUrl()` ifade tetikleyici veya eylem için benzersiz bir "geri arama URL 'si" döndürür. Bu URL, hizmetin REST API kullanan uç noktalar için benzersiz tanımlayıcıyı temsil eder. Bu işlevin parametreleri Web kancası tetikleyicisi veya eylemiyle aynıdır.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Zaman uyumsuz süreyi değiştirme

Her iki tetikleyici ve eylem için, özelliği ekleyerek zaman uyumsuz düzenin süresini belirli bir zaman aralığına sınırlayabilirsiniz `limit.timeout` . Bu şekilde, zaman aralığı üzerinde işlem bitmemişse, eylemin durumu kodla olarak işaretlenir `Cancelled` `ActionTimedOut` . `timeout`Özelliği [ISO 8601 biçimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kullanır.

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Çalışma zamanı yapılandırma ayarları

Bu `runtimeConfiguration` özellikleri tetikleyici veya eylem tanımına ekleyerek Tetikleyiciler ve eylemler için varsayılan çalışma zamanı davranışını değiştirebilirsiniz.

| Özellik | Tür | Açıklama | Tetikleyici veya eylem | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Tamsayı | Aynı anda çalışabilecek iş akışı örnekleri sayısında [*varsayılan sınırı*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) değiştirin (eşzamanlı veya paralel). Bu değeri ayarlamak, arka uç sistemlerinin aldığı istek sayısını sınırlamanıza yardımcı olabilir. <p>`runs`Özelliği `1` özelliği olarak ayarlama ile aynı şekilde çalışacak şekilde ayarlamak `operationOptions` `SingleInstance` . Her iki özelliği de ayarlayabilirsiniz, ancak ikisini birden kullanamazsınız. <p>Varsayılan sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık](#change-trigger-concurrency) veya [tetikleyici örneklerini sırayla](#sequential-trigger)değiştirme. | Tüm tetikleyiciler | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Tamsayı | Mantıksal uygulamanız zaten en fazla eşzamanlı örnekleri çalıştırıyorsa, çalıştırılacak şekilde beklemesi gereken iş akışı örneklerinin sayısı için [*varsayılan sınırı*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) değiştirin. <p>Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](#change-waiting-runs). | Tüm tetikleyiciler | 
| `runtimeConfiguration.concurrency.repetitions` | Tamsayı | Aynı anda çalışabilecek "for each" döngüsü yinelemelerinin sayısı için [*varsayılan sınırı*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) değiştirin (eşzamanlı veya paralel). <p>`repetitions`Özelliği `1` özelliği olarak ayarlama ile aynı şekilde çalışacak şekilde ayarlamak `operationOptions` `SingleInstance` . Her iki özelliği de ayarlayabilirsiniz, ancak ikisini birden kullanamazsınız. <p>Varsayılan sınırı değiştirmek için, " [her bir eşzamanlılık için" değiştirme](#change-for-each-concurrency) veya "her bir" [for each "döngüsü sırayla](#sequential-for-each)' ne bakın. | Eylem: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Tamsayı | Tarafından desteklenen ve sayfalama özelliği açık olan belirli eylemler için, bu değer alınacak *en az* sonuç sayısını belirtir. <p>Sayfalandırmayı açmak için bkz. [sayfalama kullanarak toplu veri, öğe veya sonuç edinme](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Eylem: farklılaştırın |
| `runtimeConfiguration.secureData.properties` | Dizi | Birçok tetikleyici ve eylem üzerinde, bu ayarlar mantıksal uygulamanın çalıştırma geçmişinden girdileri, çıkışları veya her ikisini de gizler. <p>Bu verileri koruma hakkında daha fazla bilgi edinmek için bkz. [çalıştırma geçmişinden girişleri ve çıkışları gizleme](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Birçok tetikleyici ve eylem |
| `runtimeConfiguration.staticResult` | JSON Nesnesi | ' Yi destekleyen ve [statik sonuç](../logic-apps/test-logic-apps-mock-data-static-results.md) ayarı açık olan eylemler için, `staticResult` nesne şu özniteliklere sahiptir: <p>- `name`, `staticResults` mantıksal uygulama iş akışınızın özniteliğinde özniteliği içinde görüntülenen geçerli eylemin statik sonuç tanımı adına başvuran `definition` . Daha fazla bilgi için bkz. [statik sonuçlar-Iş akışı tanımlama dili Için şema başvurusu](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`Bu, statik sonuçların `Enabled` geçerli eylem için olup olmadığını belirtir. <p>Statik sonuçları açmak için, bkz. [statik sonuçlar ayarlayarak, veri ve sahte verilerle test mantığı uygulamaları](../logic-apps/test-logic-apps-mock-data-static-results.md) | Eylem: farklılaştırın |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>İşlem seçenekleri

Tetikleyiciler ve eylemler için varsayılan davranışı `operationOptions` tetikleyici veya eylem tanımındaki özelliği ile değiştirebilirsiniz.

| İşlem seçeneği | Tür | Açıklama | Tetikleyici veya eylem | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Dize | HTTP tabanlı eylemleri zaman uyumsuz yerine eşzamanlı olarak çalıştırın. <p><p>Bu seçeneği ayarlamak için bkz. [eylemleri zaman uyumlu olarak çalıştırma](#disable-asynchronous-pattern). | Eylem <p>[Apiconnection](#apiconnection-action), <br>[Http](#http-action), <br>[Response](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | Dize | İstek tabanlı bir tetikleyici uç noktasına gelen çağrılar için erişim yetkisi vermek üzere [Azure Active Directory açık kimlik doğrulamasını (Azure AD OAuth) etkinleştiren](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) Logic Apps için, `Authorization` tetikleyici çıkışlarındaki OAuth erişim belirtecinden üstbilgiyi ekleyin. Daha fazla bilgi için bkz. [istek tetikleme çıktılarına ' yetkilendirme ' üst bilgisini ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Tetikleyiciler <p>[İstek](#request-trigger), <br>[HTTP Web kancası](#http-webhook-trigger) | 
| `Sequential` | Dize | "Her" for each "döngüsü yinelemelerini, paralel olarak aynı anda değil, tek bir kez çalıştırın. <p>Bu seçenek özelliği olarak ayarlamayla aynı şekilde çalışmaktadır `runtimeConfiguration.concurrency.repetitions` `1` . Her iki özelliği de ayarlayabilirsiniz, ancak ikisini birden kullanamazsınız. <p><p>Bu seçeneği ayarlamak için, bkz. ["for each" döngülerini sırayla çalıştır](#sequential-for-each).| Eylem: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Dize | Her mantıksal uygulama örneği için tetikleyiciyi sırayla çalıştırın ve sonraki mantıksal uygulama örneğini tetiklemeden önce daha önce etkin çalıştırmanın bitmesini bekleyin. <p><p>Bu seçenek özelliği olarak ayarlamayla aynı şekilde çalışmaktadır `runtimeConfiguration.concurrency.runs` `1` . Her iki özelliği de ayarlayabilirsiniz, ancak ikisini birden kullanamazsınız. <p>Bu seçeneği ayarlamak için bkz. [tetikleyici örnekleri ardışık](#sequential-trigger)olarak. | Tüm tetikleyiciler | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Tetikleyici eşzamanlılığını değiştirme

Varsayılan olarak, mantıksal uygulama iş akışı örneklerinin hepsi aynı anda (eşzamanlı veya paralel) çalışır. Bu davranış, her tetikleyici örneğinin, daha önce etkin iş akışı örneğinin çalışmasını bitmeden önce tetiklendiği anlamına gelir. Ancak, eşzamanlı çalışan örneklerin sayısı [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)sahiptir. Eşzamanlı çalışan iş akışı örneklerinin sayısı bu sınıra ulaştığında, diğer tüm yeni örneklerin çalıştırılmasını beklemesi gerekir. Bu sınır, arka uç sistemlerinin aldığı istek sayısını denetlemeye yardımcı olur.

Tetikleyicinin eşzamanlılık denetimini açtığınızda, tetikleyici örnekleri [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar paralel olarak çalışır. Bu varsayılan eşzamanlılık sınırını değiştirmek için, kod görünümü Düzenleyicisi 'ni veya Logic Apps tasarımcısını kullanarak eşzamanlılık ayarını tasarımcı aracılığıyla değiştirmek, temel tetikleyici tanımındaki özelliği ekler veya günceller, ya da `runtimeConfiguration.concurrency.runs` tam tersi. Bu özellik, paralel çalışabilecek en fazla yeni iş akışı örneği sayısını denetler.

Bir tetikleyici üzerinde eşzamanlılık etkinleştirmeden önce gözden geçirmeniz gereken bazı noktalar şunlardır:

* Eşzamanlılık denetimini etkinleştirdikten sonra eşzamanlılık devre dışı bırakılamıyor.

* Eşzamanlılık etkinleştirildiğinde, [toplu işleme dizileri](#split-on-debatch)için [üst sınır](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) önemli ölçüde azaltılır. Öğe sayısı bu sınırı aşarsa, Spton özelliği devre dışıdır.

* Eşzamanlılık etkinleştirildiğinde uzun süre çalışan bir mantıksal uygulama örneği, yeni mantıksal uygulama örneklerinin bekleme durumuna girmesine neden olabilir. Bu durum Azure Logic Apps yeni örnekler oluşturmasını engeller ve eşzamanlı çalışma sayısı belirtilen en fazla eşzamanlı çalışma sayısından az olduğunda bile gerçekleşir.

  * Bu durumu kesmek için, *hala çalışmakta* olan en erken örnekleri iptal edin.

    1. Mantıksal uygulamanızın menüsünde **genel bakış**' ı seçin.

    1. Çalıştırma **geçmişi** bölümünde, hala çalışmakta olan en erken örneği seçin, örneğin:

       ![En erken çalışan örneği Seç](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Yalnızca çalışmaya devam eden örnekleri görüntülemek için, **Tümü** listesini açın ve **çalışıyor**' ı seçin.

    1. **Mantıksal uygulama çalıştırması** altında, **çalıştırmayı iptal et**' i seçin.

       ![En erken çalışan örneği bul](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Bu olasılığa geçici bir çözüm bulmak için, bu çalışmaları tutabilecek herhangi bir eyleme bir zaman aşımı ekleyin. Kod Düzenleyicisi 'nde çalışıyorsanız, bkz. [zaman uyumsuz süreyi değiştirme](#asynchronous-limits). Aksi takdirde, tasarımcıyı kullanıyorsanız şu adımları izleyin:

    1. Mantıksal uygulamanızda, zaman aşımı eklemek istediğiniz eylemde, sağ üst köşede üç nokta (**...**) düğmesini seçin ve ardından **Ayarlar**' ı seçin.

       ![Eylem ayarlarını aç](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. **Zaman aşımı** altında [ISO 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)zaman aşımı süresini belirtin.

       ![Zaman aşımı süresini belirtin](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Mantıksal uygulamanızı sırayla çalıştırmak için tetikleyicinin eşzamanlılık kümesini `1` kod görünümü Düzenleyicisi veya Tasarımcı kullanarak yapın. Ayrıca, tetikleyici `operationOptions` özelliğini `SingleInstance` kod görünümü düzenleyicisinde olarak ayarladığınızdan emin olun. Aksi takdirde, doğrulama hatası alırsınız. Daha fazla bilgi için bkz. [örnekleri sırayla tetikleme](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Kod görünümünde Düzenle 

Temel tetikleyici tanımında `runtimeConfiguration.concurrency.runs` özelliği ekleyin ve değeri [tetikleyici eşzamanlılık sınırlarına](logic-apps-limits-and-config.md#concurrency-debatching)göre ayarlayın. İş akışınızı ardışık olarak çalıştırmak için özellik değerini olarak ayarlayın `1` .

Bu örnek, eşzamanlı çalıştırmaları 10 örneğe kısıtlar:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps tasarımcısında Düzenle

1. Tetikleyicinin sağ üst köşesinde üç nokta (**...**) düğmesini ve ardından **Ayarlar**' ı seçin.

1. **Eşzamanlılık denetimi** altında **sınırı** **Açık** olarak ayarlayın. 

1. **Paralellik** sürgüsünün derecesini istediğiniz değere sürükleyin. Mantıksal uygulamanızı sırayla çalıştırmak için kaydırıcı değerini **1** olarak sürükleyin.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Eşzamanlılık "for each"

Varsayılan olarak, "for each" döngüsü yinelemelerinin hepsi aynı anda (eşzamanlı veya paralel) çalışır. Bu davranış, önceki yineleme çalışmadan önce her yinelemenin çalışmaya başladığı anlamına gelir. Ancak, eşzamanlı çalışan yinelemelerin sayısının [varsayılan sınırı](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)vardır. Eşzamanlı çalışan yinelemeler sayısı bu sınıra ulaştığında, diğer tüm yinelemelerin çalıştırılması beklenmelidir.

Varsayılan sınırı değiştirmek için, kod görünümü Düzenleyicisi 'ni veya Logic Apps tasarımcısını kullanarak eşzamanlılık ayarını tasarımcı aracılığıyla değiştirmeniz, `runtimeConfiguration.concurrency.repetitions` temel alınan "her" eylem tanımında özelliği ekler veya günceller, ya da tam tersi. Bu özellik paralel çalışabilecek en fazla yineleme sayısını denetler.

> [!NOTE] 
> "For each" eylemini tasarımcı veya kod görünümü Düzenleyicisi kullanarak sırayla çalışacak şekilde ayarlarsanız, eylemin `operationOptions` özelliğini `Sequential` kod görünümü düzenleyicisinde olarak ayarlamayın. Aksi takdirde, doğrulama hatası alırsınız. Daha fazla bilgi için bkz. ["for each" döngülerini sırayla çalıştırma](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Kod görünümünde Düzenle 

Temel alınan "for each" tanımında, `runtimeConfiguration.concurrency.repetitions` ve ' den değişen bir değere sahip olabilen özelliği ekleyin veya güncelleştirin `1` `50` .

Eşzamanlı çalıştırmaları 10 yineleme ile sınırlayan bir örnek aşağıda verilmiştir:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps tasarımcısında Düzenle

1. Her eylem **için** sağ üst köşedeki üç nokta (**...**) düğmesini ve ardından **Ayarlar**' ı seçin.

1. **Eşzamanlılık denetimi** altında **eşzamanlılık denetimini** **Açık** olarak ayarlayın.

1. **Paralellik** sürgüsünün derecesini istediğiniz değere sürükleyin. Mantıksal uygulamanızı sırayla çalıştırmak için kaydırıcı değerini **1** olarak sürükleyin.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Değişiklik bekleyen çalışma sınırı

Varsayılan olarak, mantıksal uygulama iş akışı örneklerinin hepsi aynı anda (eşzamanlı veya paralel) çalışır. Bu davranış, her tetikleyici örneğinin, daha önce etkin iş akışı örneğinin çalışmasını bitmeden önce tetiklendiği anlamına gelir. Ancak, eşzamanlı çalışan örneklerin sayısı [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)sahiptir. Eşzamanlı çalışan iş akışı örneklerinin sayısı bu sınıra ulaştığında, diğer tüm yeni örneklerin çalıştırılmasını beklemesi gerekir.

Bekleme çalışmalarının sayısı aynı zamanda [varsayılan bir sınıra](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)sahiptir. Bekleyen çalışma sayısı bu sınıra ulaştığında, Logic Apps altyapısı artık yeni çalıştırmalar kabul etmez. İstek ve Web kancası Tetikleyicileri 429 hata döndürüyor ve yinelenen Tetikleyiciler yoklama girişimlerini atmaya başladı.

Yalnızca [tetikleyici eşzamanlılık için varsayılan sınırı değiştiremeyebilir](#change-trigger-concurrency), ancak bekleyen çalıştırmalar için varsayılan sınırı da değiştirebilirsiniz. Temel tetikleyici tanımında, `runtimeConfiguration.concurrency.maximumWaitingRuns` ile arasında değişen bir değere sahip olabilen özelliğini ekleyin `1` `100` .

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Örnekleri sıralı olarak Tetikle

Her mantıksal uygulama iş akışı örneğini yalnızca önceki örnek çalışmayı tamamladığında çalıştırmak için tetikleyiciyi sıralı olarak çalışacak şekilde ayarlayın. Aynı zamanda tasarımcı aracılığıyla eşzamanlılık ayarını değiştirmek, temel tetikleyici tanımındaki özelliği de ekler veya günceller, ya da `runtimeConfiguration.concurrency.runs` tam tersi de geçerlidir. Logic Apps

> [!NOTE] 
> Tasarımcıyı veya kod görünümü düzenleyicisini kullanarak sırayla çalışacak bir tetikleyici ayarladığınızda, tetikleyicisinin `operationOptions` özelliğini `Sequential` kod görünümü düzenleyicisinde olarak ayarlamayın. Aksi takdirde, doğrulama hatası alırsınız. 

#### <a name="edit-in-code-view"></a>Kod görünümünde Düzenle

Tetikleyici tanımında, bu özelliklerden birini ayarlayın, ancak ikisini birden kullanmayın. 

Özelliği şu `runtimeConfiguration.concurrency.runs` şekilde ayarlayın `1` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*veya*

Özelliği şu `operationOptions` şekilde ayarlayın `SingleInstance` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options) ve [işlem seçenekleri](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps tasarımcısında Düzenle

1. Tetikleyicinin sağ üst köşesinde üç nokta (**...**) düğmesini ve ardından **Ayarlar**' ı seçin.

1. **Eşzamanlılık denetimi** altında **sınırı** **Açık** olarak ayarlayın. 

1. **Paralellik** sürgüsünün derecesini sayıya sürükleyin `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>"Her" for each "döngülerini sırayla çalıştır

Yalnızca önceki yineleme çalıştıktan sonra "for each" döngüsü yinelemesi çalıştırmak için, "for each" eylemini sıralı olarak çalışacak şekilde ayarlayın. Eylemin eşzamanlılık ' i tasarımcı aracılığıyla değiştirmek Ayrıca temel eylem tanımındaki özelliği de ekler veya güncelleştirir, ya da `runtimeConfiguration.concurrency.repetitions` tam tersi de geçerlidir. Logic Apps

> [!NOTE] 
> Tasarımcı veya kod görünümü Düzenleyicisi 'ni kullanarak sırayla çalışacak bir "for each" eylemini ayarladığınızda, `operationOptions` kod görünümü düzenleyicisinde eylemin özelliğini olarak ayarlamayın `Sequential` . Aksi takdirde, doğrulama hatası alırsınız. 

#### <a name="edit-in-code-view"></a>Kod görünümünde Düzenle

Eylem tanımında, bu özelliklerden birini ayarlayın, ancak ikisini birden kullanmayın. 

Özelliği şu `runtimeConfiguration.concurrency.repetitions` şekilde ayarlayın `1` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*veya*

Özelliği şu `operationOptions` şekilde ayarlayın `Sequential` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](#runtime-config-options) ve [işlem seçenekleri](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps tasarımcısında Düzenle

1. **Her** bir eylemin sağ üst köşesinde, üç nokta (**...**) düğmesini ve ardından **Ayarlar**' ı seçin.

1. **Eşzamanlılık denetimi** altında **eşzamanlılık denetimini** **Açık** olarak ayarlayın.

1. **Paralellik** sürgüsünün derecesini sayıya sürükleyin `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>İşlemleri zaman uyumlu bir işlem düzeninde çalıştırma

Varsayılan olarak, Azure Logic Apps HTTP eylemi ve APIConnection eylemleri standart [*zaman uyumsuz işlem modelini*](/azure/architecture/patterns/async-request-reply)izler, ancak yanıt eylemi *zaman uyumlu işlem modelini* izler. Zaman uyumsuz model, bir eylem belirtilen uç nokta, hizmet, sistem veya API 'ye bir istek çağırdığında veya gönderdiğinde, alıcı hemen ["202 kabul edildi"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) yanıtı döndürdüğünü belirtir. Bu kod, alıcının isteği kabul ettiğini ancak işlemeyi bitirmediğini onaylar. Yanıt, `location` alıcı işlemeyi durdurmadan ve ["200 Tamam"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) başarı yanıtını ya da 202 olmayan diğer yanıtı döndürünceye kadar, çağıranın, zaman uyumsuz isteğin durumunu sürekli yoklamak veya denetlemek için kullanabileceği, URL 'YI ve yenileme kimliğini belirten bir üst bilgi içerebilir. Daha fazla bilgi için bkz. [zaman uyumsuz mikro hizmet tümleştirmesi mikro hizmet bağımsız çalışma sınırı zorlar](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* Mantıksal uygulama tasarımcısında, HTTP eylemi, APIConnection eylemleri ve yanıt eylemi **zaman uyumsuz model** ayarına sahiptir. Bu ayar etkinleştirildiğinde, çağıranın işlemin bitmesini beketmediğini ve bir sonraki eyleme geçebilir ancak işlem duraklarına kadar durumu denetlemeye devam edebilir. Devre dışı bırakılırsa, bu ayar çağıranın bir sonraki eyleme geçmeden önce işlemin tamamlanmasını beklediğini belirtir. Bu ayarı bulmak için şu adımları izleyin:

  1. HTTP eyleminin başlık çubuğunda, eylemin ayarlarını açan üç nokta (**...**) düğmesini seçin.

  1. **Zaman uyumsuz model** ayarını bulun.

     !["Zaman uyumsuz model" ayarı](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* Eylemin temel alınan JavaScript Nesne Gösterimi (JSON) tanımında, HTTP eylemi ve APIConnection eylemleri, zaman uyumsuz işlem modelini örtülü olarak izler.

Bazı senaryolarda, bunun yerine zaman uyumlu bir eylemin izlemesini isteyebilirsiniz. Örneğin, HTTP eylemini kullandığınızda şunları yapmak isteyebilirsiniz:

* [Uzun süre çalışan görevler için HTTP zaman aşımlarını önleyin](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Konum üst bilgilerini denetlemeyi devre dışı bırak](../connectors/connectors-native-http.md#disable-location-header-check)

Bu durumlarda, aşağıdaki seçenekleri kullanarak bir eylemi zaman uyumlu bir şekilde çalıştırabilirsiniz:

* Varsa, bu eylemin yoklama sürümünü, varsa bir Web kancası sürümü ile değiştirin.

* İki seçenekten birini izleyerek eylemin zaman uyumsuz davranışını devre dışı bırakın:

  * Mantıksal uygulama tasarımcısında, [ **zaman uyumsuz model** ayarını](#turn-off-asynchronous-pattern-setting)kapatın.

  * Eylemin temel alınan JSON tanımında, [ `"DisableAsyncPattern"` işlem seçeneğini ekleyin](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>**Zaman uyumsuz model** ayarını kapat

1. Mantıksal uygulama Tasarımcısı ' nda, eylemin başlık çubuğunda, eylemin ayarlarını açan üç nokta (**...**) düğmesini seçin.

1. **Zaman uyumsuz model** ayarını bulun, etkinleştirilirse ayarı **devre dışı** bırakın ve **bitti**' yi seçin.

   !["Zaman uyumsuz model" ayarını kapat](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Eylemin JSON tanımında zaman uyumsuz kalıbı devre dışı bırak

Eylemin temel alınan JSON tanımında, ["Operationoptions" özelliğini](#operation-options) eylem bölümünün altında öğesine ekleyin ve ayarlayın `"DisableAsyncPattern"` `"inputs"` , örneğin:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Tetikleyiciler ve eylemlerin kimliğini doğrulama

HTTP ve HTTPS uç noktaları farklı kimlik doğrulama türlerini destekler. Bu uç noktalara erişmek üzere giden çağrı veya istek yapmak için kullandığınız tetikleyici veya eyleme göre farklı kimlik doğrulama türleri aralığından seçim yapabilirsiniz. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Sonraki adımlar

* [Iş akışı tanımlama dili](../logic-apps/logic-apps-workflow-definition-language.md) hakkında daha fazla bilgi edinin