---
title: 2. x-Azure Izleyici Application Insights Java 'dan yükseltme
description: Azure Izleyici 'den yükseltme Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 342c535cadb1a2d3f2d18478d8941d9ea61bdf72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448976"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Java 2. x SDK Application Insights yükseltme

Uygulamanızda zaten Java 2. x SDK Application Insights kullanıyorsanız, bunu kaldırmanız gerekmez.
Java 3,0 Aracısı bunu algılar ve 2. x SDK 'Sı aracılığıyla gönderdiğiniz herhangi bir özel Telemetriyi yakalayıp, yinelenen telemetrinin önlenmesi için 2. x SDK tarafından gerçekleştirilen herhangi bir otomatik koleksiyonu engeller.

Application Insights 2. x Aracısı kullanıyorsanız, `-javaagent:` 2. x aracısına işaret eden JVM bağımsız değişken 'i kaldırmanız gerekir.

Bu belgenin geri kalanında, 2. x 'ten 3,0 sürümüne yükseltirken karşılaşabileceğiniz sınırlamalar ve değişiklikler açıklanmakta ve yararlı bulabileceğiniz bazı geçici çözümler açıklanmaktadır.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers ve TelemetryProcessors

3,0 Aracısı kullanılırken 2. x SDK TelemetryInitializers ve TelemetryProcessors çalıştırılmayacak.
Bu, daha önce gerekli olan kullanım örneklerinin birçoğu, [özel boyutları](./java-standalone-config.md#custom-dimensions) yapılandırarak veya [telemetri işlemcileri](./java-standalone-telemetry-processors.md)yapılandırarak 3,0 'de çözülebilir.

## <a name="multiple-applications-in-a-single-jvm"></a>Tek bir JVM 'de birden çok uygulama

Şu anda 3,0, çalışan işlem başına yalnızca tek bir [bağlantı dizesini ve rol adını](./java-standalone-config.md#connection-string-and-role-name) destekler. Özellikle, farklı bağlantı dizelerini veya farklı rol adlarını kullanan aynı Tomcat dağıtımında birden çok Tomcat Web uygulamanız olamaz.

## <a name="operation-names"></a>İşlem adları

3,0 içindeki işlem adları genellikle Application Insights Portal U/X ' te daha iyi bir toplu görünüm sağlayacak şekilde değiştirilmiştir.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3,0 içindeki işlem adları":::

Ancak bazı uygulamalarda, önceki işlem adları tarafından sağlanmış olan U/X ' de toplanmış görünümü tercih edebilirsiniz. Bu durumda, önceki davranışı çoğaltmak için 3,0 içindeki [telemetri işlemcileri](./java-standalone-telemetry-processors.md) (Önizleme) özelliğini kullanabilirsiniz.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Http yöntemiyle işlem adına önek ekleyin ( `GET` , `POST` , vb.)

2. x SDK 'sında, işlem adları http yöntemi ( `GET` , `POST` , vb.) tarafından önekli, ör.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Http yöntemi tarafından önekli işlem adları":::

3.0.3 ' den başlayarak, bu 2. x davranışını kullanarak geri getirebilirsiniz.

```json
{
  "preview": {
    "httpMethodInOperationName": true
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>İşlem adını tam yola ayarla

Ayrıca, 2. x SDK 'sında, bazı durumlarda işlem adları tam yolu içeriyordu, örn.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Tam yol içeren işlem adları":::

Aşağıdaki kod parçacığı, önceki davranışı çoğaltmak için birleştiren 4 telemetri işlemciyi yapılandırır.
Telemetri işlemcileri aşağıdaki eylemleri gerçekleştirir (sırasıyla):

1. İlk telemetri işlemcisi, bir yayma işlemcisidir (türüne sahiptir `span` ), yani ve için geçerli olur `requests` `dependencies` .

   Adlı bir özniteliğe sahip olan herhangi bir yayılma eşleşmesi gerekecektir `http.url` .

   Ardından, Aralık adını `http.url` öznitelik değeriyle güncelleştirir.

   Bu, bunun sonu olur, ancak bu, `http.url` gibi bir şey gibi görünür `http://host:port/path` ve büyük olasılıkla yalnızca parçayı istemeniz olasıdır `/path` .

2. İkinci telemetri işlemcisi de bir yayma işlemcisidir.

   Adlı bir özniteliğe `http.url` (diğer bir deyişle, ilk işlemcinin eşleştiği tüm yayılmasına) sahip olan tüm yayılımla eşleşir.

   Ardından, yayılma adının yol bölümünü adlı bir özniteliğe ayıklar `tempName` .

3. Üçüncü telemetri işlemcisi de bir yayma işlemcisidir.

   Adlı bir özniteliğe sahip olan herhangi bir yayılma eşleşmesi gerekecektir `tempPath` .

   Sonra, öznitelik adını öznitelikten güncellecektir `tempPath` .

4. Son telemetri işlemcisi, öznitelik işlemcisidir (türü vardır `attribute` ), bu, öznitelikleri olan tüm telemetri için geçerlidir (Şu anda `requests` `dependencies` ve `traces` ).

   Adlı bir özniteliğe sahip olan herhangi bir Telemetriyi eşleştirecektir `tempPath` .

   Daha sonra, adlı özniteliği `tempPath` , özel bir boyut olarak raporlanmayacak şekilde silecektir.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Bağımlılık adları

3,0 içindeki bağımlılık adları da değiştirilmiştir, Ayrıca, Application Insights Portal U/X ' de genellikle daha iyi bir toplu görünüm sağlar.

Yine, bazı uygulamalar için, önceki bağımlılık adları tarafından sağlanmış olan U/X ' de toplanmış görünümü tercih edebilirsiniz. Bu durumda, önceki davranışı çoğaltmak için yukarıdaki gibi benzer teknikleri kullanabilirsiniz.

## <a name="operation-name-on-dependencies"></a>Bağımlılıklarda işlem adı

Daha önce 2. x SDK 'sında, istek telemetride işlem adı bağımlılık telemetrisi üzerinde de ayarlanmıştır.
Application Insights Java 3,0, bağımlılık telemetrisi üzerinde işlem adını artık doldurmayacak.
Bağımlılık telemetrinin üst öğesi olan istek için işlem adını görmek isterseniz, bağımlılık tablosundan istek tablosuna katmak üzere bir günlük (kusto) sorgusu yazabilirsiniz, örn.

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2. x SDK günlüğü uygulama

3,0 Aracısı, günlük kaydını yapılandırmaya gerek kalmadan [günlüğü otomatik olarak toplar](./java-standalone-config.md#auto-collected-logging) .
2. x SDK günlük kaydı kullanıyorsanız, bunlar yine de 3,0 Aracısı tarafından bastırılacak şekilde kaldırılabilirler.

## <a name="2x-sdk-spring-boot-starter"></a>2. x SDK Spring Boot Starter

3,0 Spring Boot Starter yok.
3,0 aracı kurulumu ve yapılandırması, Spring Boot ' ı kullanıp kullanmayacağınızı de aynı [basit adımları](./java-in-process-agent.md#quickstart) izler.

2. x SDK Spring Boot Starter 'dan yükseltirken, bulut rolü adının artık varsayılan olarak olmadığını unutmayın `spring.application.name` .
JSON yapılandırması veya ortam değişkeni aracılığıyla 3,0 ' de bulut rolü adını ayarlamak için [3,0 yapılandırma belgelerine](./java-standalone-config.md#cloud-role-name) bakın.
