---
title: API protokollerini Yapılandırma-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid tarafından açığa çıkarılan API protokollerini yapılandırın.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: f39968476f2fecf655e6c69bea2ff19304d2b465
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992528"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API protokollerini yapılandırma

Bu kılavuzda, bir Event Grid modülünün olası protokol yapılandırmalarının örnekleri verilmiştir. Event Grid modülü, yönetim ve çalışma zamanı işlemleri için API 'YI kullanıma sunar. Aşağıdaki tablo, protokolleri ve bağlantı noktalarını yakalar.

| Protokol | Bağlantı noktası | Açıklama |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Varsayılan olarak kapalıdır. Yalnızca sınama sırasında yararlı olur. Üretim iş yükleri için uygun değildir.
| HTTPS | 4438 | Varsayılan

Tüm olası yapılandırmalara yönelik [güvenlik ve kimlik doğrulama Kılavuzu '](security-authentication.md) na bakın.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Aynı kenar ağında HTTPS 'yi IoT modüllerine kullanıma sunma

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Diğer IoT modüllerine ve IoT olmayan iş yüklerine HTTPS 'yi etkinleştirme

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> **Portbindings** bölümü, iç bağlantı noktalarını kapsayıcı konağın bağlantı noktalarıyla eşlemenizi sağlar. Bu özellik, IoT Edge cihazının herkese açık olması halinde IoT Edge kapsayıcı ağı dışından Event Grid modülüne ulaşmak mümkün hale gelir.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Aynı uç ağdaki IoT modüllerine HTTP ve HTTPS 'yi kullanıma sunma

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Diğer IoT modüllerine ve IoT olmayan iş yüklerine HTTP ve HTTPS 'yi etkinleştirme

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Varsayılan olarak, her IoT modülü köprü ağı tarafından oluşturulan IoT Edge çalışma zamanının bir parçasıdır. Aynı ağ üzerinde bulunan farklı IoT modüllerinin birbirleriyle iletişim kurmasını sağlar. **Portbindings** , bir kapsayıcı iç bağlantı noktasını ana makine üzerinde eşleştirmenize olanak tanıyarak, herkesin Event Grid modülünün bağlantı noktasına dışarıdan erişebilmesini sağlar.

>[!IMPORTANT]
> Bağlantı noktaları IoT Edge ağı dışında erişilebilir hale getirilmese de, istemci kimlik doğrulaması, gerçekte modüle çağrı yapmasına izin verilen kişileri zorlar.
