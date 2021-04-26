---
title: Özel ayarları yapılandırma
description: Tüm Azure App Service ortamı için uygulanan ayarları yapılandırın. Azure Resource Manager şablonlarıyla nasıl yapılacağını öğrenin.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226397"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service ortamları için özel yapılandırma ayarları
## <a name="overview"></a>Genel Bakış
App Service ortamları (ASEs) tek bir müşteriye yalıtılmış olduğundan, yalnızca App Service ortamlara uygulanabilen belirli yapılandırma ayarları vardır. Bu makale App Service ortamları için kullanılabilen çeşitli özelleştirmeleri belgeler.

Bir App Service Ortamı yoksa, bkz. [nasıl App Service ortamı oluşturma](app-service-web-how-to-create-an-app-service-environment.md).

Yeni **Clustersettings** özniteliğinde bir dizi kullanarak, App Service ortamı özelleştirmelerini saklayabilirsiniz. Bu öznitelik, *Hostingenvironments* Azure Resource Manager varlığının "Özellikler" sözlüğünde bulunur.

Aşağıdaki kısaltılmış Kaynak Yöneticisi şablonu kod parçacığı **Clustersettings** özniteliğini gösterir:

```json
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
```

**Clustersettings** özniteliği, App Service ortamı güncelleştirmek için bir kaynak yöneticisi şablonuna dahil edilebilir.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Bir App Service Ortamı güncelleştirmek için Azure Kaynak Gezgini kullanma
Alternatif olarak, [Azure Kaynak Gezgini](https://resources.azure.com)kullanarak App Service ortamı güncelleştirebilirsiniz.  

1. Kaynak Gezgini ' de, App Service ortamı düğüme gidin (**abonelikler**  >  **ResourceGroups**  >  **providers**  >  **Microsoft. Web**  >  **hostingenvironments**). Sonra güncelleştirmek istediğiniz belirli App Service Ortamı tıklayın.
2. Sağ bölmede, Kaynak Gezgini ' de etkileşimli düzenlemelere izin vermek için üstteki araç çubuğundan **oku/yaz** ' a tıklayın.  
3. Kaynak Yöneticisi şablonunu düzenlenebilir hale getirmek için mavi **Düzenle** düğmesine tıklayın.
4. Sağ bölmenin en altına kaydırın. **Clustersettings** özniteliği en altta olduğundan, değerini girebilir veya güncelleştirebilirsiniz.
5. **Clustersettings** özniteliğinde istediğiniz yapılandırma değerlerinin dizisini yazın (veya kopyalayıp yapıştırın).  
6. Değişikliği App Service Ortamı uygulamak için sağ bölmenin en üstünde bulunan yeşil **PUT** düğmesine tıklayın.

Ancak değişikliği gönderdiğinizde, değişikliğin etkili olması için App Service Ortamı ön uçların sayısına göre yaklaşık 30 dakika çarpılır.
Örneğin, bir App Service Ortamı ön uçları varsa, yapılandırma güncelleştirmesinin tamamlanması yaklaşık olarak iki saat sürer. Yapılandırma değişikliği kullanıma alındığı sırada, App Service Ortamı başka ölçeklendirme işlemleri veya yapılandırma değişikliği işlemleri gerçekleşmiyor.

## <a name="enable-internal-encryption"></a>Iç şifrelemeyi etkinleştir

App Service Ortamı, iç bileşenleri veya sistem içindeki iletişimi görgeçirebileceğiniz bir siyah kutu sistemi olarak çalışır. Daha yüksek işleme sağlamak için, şifreleme, iç bileşenler arasında varsayılan olarak etkinleştirilmemiştir. Trafiğe izlenmekte veya erişilmek üzere erişilemediğinden, sistem güvenlidir. Veri yolunun uçtan uca tamamen şifrelenmesini gerektiren bir uyumluluk gereksinimine sahipseniz, bir clusterSetting ile tüm veri yolunun şifrelenmesini olanaklı hale getirmenin bir yolu vardır.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
InternalEncryption değerini true olarak ayarlamak, ASE 'de ön uçlar ve çalışanlar arasında iç ağ trafiğini şifreler, disk belleği şifreler ve ayrıca çalışan disklerini şifreler. InternalEncryption clusterSetting etkinleştirildikten sonra, sistem Performanslarınızın bir etkisi olabilir. InternalEncryption 'i etkinleştirmek için değişiklik yaptığınızda, Ao 'niz değişiklik tamamen yayılana kadar kararsız bir durumda olacaktır. Ao 'da kaç örneğe sahip olduğunuza bağlı olarak, değişikliğin tamamının yayılması birkaç saat sürebilir. Kullanımda iken bir AO 'da InternalEncryption ' i etkinleştirmemeniz önemle tavsiye ederiz. Etkin bir şekilde kullanılan bir AO 'da InternalEncryption etkinleştirmeniz gerekiyorsa, işlem tamamlanana kadar trafiği bir yedekleme ortamına eklemeniz önemle tavsiye ederiz. 


## <a name="disable-tls-10-and-tls-11"></a>TLS 1.0 ve TLS 1.1'i devre dışı bırakma

Uygulama temelinde bir uygulamadaki TLS ayarlarını yönetmek istiyorsanız, [TLS ayarlarını zorla](../configure-ssl-bindings.md#enforce-tls-versions) belgeleriyle verilen Kılavuzu kullanabilirsiniz. 

Ao 'daki tüm uygulamalar için tüm gelen TLS 1,0 ve TLS 1,1 trafiğini devre dışı bırakmak istiyorsanız, aşağıdaki **Clustersettings** girişini ayarlayabilirsiniz:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

Ayarın adı 1,0 diyor ancak yapılandırıldığında, hem TLS 1,0 hem de TLS 1,1 'yi devre dışı bırakır.

## <a name="change-tls-cipher-suite-order"></a>TLS şifre paketi sırasını değiştirme
ASE, şifre paketinin varsayılan olarak değiştirilmesini destekler. Varsayılan şifrelemeler kümesi, çok kiracılı hizmette kullanılan aynı kümesidir. Şifre paketlerinin değiştirilmesi tüm App Service dağıtımını etkiler ve yalnızca tek kiracılı Ao 'da mümkün hale getirir. Ao için gereken iki şifre paketi vardır; TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 ve TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. ASE 'nizi en güçlü ve en düşük şifreleme paketi kümesiyle çalıştırmak istiyorsanız, yalnızca iki gerekli şifre düzeyini kullanın. ATıCı 'nizi yalnızca gerektirdiği şifrelemeleri kullanacak şekilde yapılandırmak için, aşağıdaki gibi **kümesel ayarları** değiştirin. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> SChannel 'nin anlayamediği şifre paketi için yanlış değerler ayarlandıysa, sunucunuza yönelik tüm TLS iletişimleri çalışmayı durdurabilir. Böyle bir durumda, *Frontendsslciphersuiteorder* girişini **clustersettings** 'ten kaldırmalı ve varsayılan şifre paketi ayarlarına geri dönmek için güncelleştirilmiş Kaynak Yöneticisi şablonunu göndermeniz gerekir.  Lütfen bu işlevselliği dikkatle kullanın.

## <a name="get-started"></a>başlarken
Azure hızlı başlangıç Kaynak Yöneticisi şablonu sitesi, [bir App Service ortamı oluşturmak](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)için temel tanımına sahip bir şablon içerir.

<!-- LINKS -->

<!-- IMAGES -->
