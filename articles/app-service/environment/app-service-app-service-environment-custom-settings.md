---
title: App Service ortamları için özel ayarlar-Azure
description: App Service ortamları için özel yapılandırma ayarları
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 768e354281aeb5e2143ddb0e190b0f27bf4f4e24
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470701"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service ortamları için özel yapılandırma ayarları
## <a name="overview"></a>Genel Bakış
App Service ortamları (ASEs) tek bir müşteriye yalıtılmış olduğundan, yalnızca App Service ortamlara uygulanabilen belirli yapılandırma ayarları vardır. Bu makale App Service ortamları için kullanılabilen çeşitli özelleştirmeleri belgeler.

Bir App Service Ortamı yoksa, bkz. [nasıl App Service ortamı oluşturma](app-service-web-how-to-create-an-app-service-environment.md).

Yeni **Clustersettings** özniteliğinde bir dizi kullanarak, App Service ortamı özelleştirmelerini saklayabilirsiniz. Bu öznitelik, *Hostingenvironments* Azure Resource Manager varlığının "Özellikler" sözlüğünde bulunur.

Aşağıdaki kısaltılmış Kaynak Yöneticisi şablonu kod parçacığı **Clustersettings** özniteliğini gösterir:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**Clustersettings** özniteliği, App Service ortamı güncelleştirmek için bir kaynak yöneticisi şablonuna dahil edilebilir.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Bir App Service Ortamı güncelleştirmek için Azure Kaynak Gezgini kullanma
Alternatif olarak, [Azure Kaynak Gezgini](https://resources.azure.com)kullanarak App Service ortamı güncelleştirebilirsiniz.  

1. Kaynak Gezgini, App Service Ortamı düğüme**gidin (** **Microsoft. Web** > **hostingenvironments** >  > **ResourceGroups** > **providers** . Sonra güncelleştirmek istediğiniz belirli App Service Ortamı tıklayın.
2. Sağ bölmede, Kaynak Gezgini ' de etkileşimli düzenlemelere izin vermek için üstteki araç çubuğundan **oku/yaz** ' a tıklayın.  
3. Kaynak Yöneticisi şablonunu düzenlenebilir hale getirmek için mavi **Düzenle** düğmesine tıklayın.
4. Sağ bölmenin en altına kaydırın. **Clustersettings** özniteliği en altta olduğundan, değerini girebilir veya güncelleştirebilirsiniz.
5. **Clustersettings** özniteliğinde istediğiniz yapılandırma değerlerinin dizisini yazın (veya kopyalayıp yapıştırın).  
6. Değişikliği App Service Ortamı uygulamak için sağ bölmenin en üstünde bulunan yeşil **PUT** düğmesine tıklayın.

Ancak değişikliği gönderdiğinizde, değişikliğin etkili olması için App Service Ortamı ön uçların sayısına göre yaklaşık 30 dakika çarpılır.
Örneğin, bir App Service Ortamı ön uçları varsa, yapılandırma güncelleştirmesinin tamamlanması yaklaşık olarak iki saat sürer. Yapılandırma değişikliği kullanıma alındığı sırada, App Service Ortamı başka ölçeklendirme işlemleri veya yapılandırma değişikliği işlemleri gerçekleşmiyor.

## <a name="disable-tls-10-and-tls-11"></a>TLS 1,0 ve TLS 1,1 'yi devre dışı bırakın

Uygulama temelinde bir uygulamadaki TLS ayarlarını yönetmek istiyorsanız, [TLS ayarlarını zorla](../configure-ssl-bindings.md#enforce-tls-versions) belgeleriyle verilen Kılavuzu kullanabilirsiniz. 

Ao 'daki tüm uygulamalar için tüm gelen TLS 1,0 ve TLS 1,1 trafiğini devre dışı bırakmak istiyorsanız, aşağıdaki **Clustersettings** girişini ayarlayabilirsiniz:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

Ayarın adı 1,0 diyor ancak yapılandırıldığında, hem TLS 1,0 hem de TLS 1,1 'yi devre dışı bırakır.

## <a name="change-tls-cipher-suite-order"></a>TLS şifre paketi sırasını değiştirme
Müşterilerden başka bir soru, sunucusu tarafından anlaşmalı şifre listesini değiştirebilir ve bu, aşağıda gösterildiği gibi **Clustersettings** değiştirilerek elde edilebilir. Kullanılabilir şifre paketlerinin listesi [Bu MSDN makalesinden](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)alınabilir.

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> SChannel 'nin anlayamediği şifre paketi için yanlış değerler ayarlandıysa, sunucunuza yönelik tüm TLS iletişimleri çalışmayı durdurabilir. Böyle bir durumda, *Frontendsslciphersuiteorder* girişini **clustersettings** 'ten kaldırmalı ve varsayılan şifre paketi ayarlarına geri dönmek için güncelleştirilmiş Kaynak Yöneticisi şablonunu göndermeniz gerekir.  Lütfen bu işlevselliği dikkatle kullanın.
> 
> 

## <a name="get-started"></a>Kullanmaya başlayın
Azure hızlı başlangıç Kaynak Yöneticisi şablonu sitesi, [bir App Service ortamı oluşturmak](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)için temel tanımına sahip bir şablon içerir.

<!-- LINKS -->

<!-- IMAGES -->
