---
title: Kişisel bilgiler için adlandırılmış varlıklar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 7484b49ed3c868a1ad3e0f97dffa346f350e127f
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097709"
---
> [!NOTE]
> Korumalı sistem durumu bilgilerini (FI) algılamak için, `domain=phi` parametresini ve model sürümünü `2020-04-01` veya üstünü kullanın.
>
> Örnek: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Uç noktaya istek gönderdiğinizde aşağıdaki varlık kategorileri döndürülür `/v3.1-preview.3/entities/recognition/pii` .


| Kategori   |  Açıklama                          |
|------------|-------------|
| [Kişi](#category-person)      |  Kişilerin adları.  |
| [Kişilik türü](#category-persontype) | Bir kişi tarafından tutulan iş türleri veya roller. |
| [Telefon numarası](#category-phonenumber) |Telefon numaraları (yalnızca ABD ve AB telefon numaraları). |
| [Kuruluş](#category-organization) |  Şirketler, gruplar, kamu gövdeleri ve diğer kuruluşlar.  |
| [Adres](#category-address) | Tam posta adresleri.  |
| [E-posta](#category-email) | E-posta adresleri.   |
| [URL](#category-url) | Web sitelerinin URL 'Leri.  |
| [IP](#category-ip) | Ağ IP adresleri.  |
| [Tarih Saat](#category-datetime) | Günün tarihleri ve saatleri. | 
| [Miktar](#category-quantity) | Sayılar ve sayısal miktarlar.  |
| [Azure bilgileri](#azure-information) | Kimlik doğrulama bilgileri gibi tanımlanabilir Azure bilgileri.  |
| [Kimlik](#identification) | Mali ve ülkeye özgü tanımlama.  |

### <a name="category-person"></a>Kategori: kişi

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kişi

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Kişilerin adları. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `Person` `pii-categories` . `Person` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategori: PersonType

Bu kategori aşağıdaki varlığı içerir:


:::row:::
    :::column span="":::
        **Varlık**

        Kişilik türü

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bir kişi tarafından tutulan iş türleri veya roller.

        Bu varlık kategorisini almak için parametresine ekleyin `PersonType` `pii-categories` . `PersonType` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategori: PhoneNumber

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Telefon numaraları (yalnızca ABD ve AB telefon numaraları). İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `PhoneNumber` `pii-categories` . `PhoneNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Kategori: kuruluş

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kuruluş

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Şirketler, siyatik gruplar, müzik bantları, spor sinek, kamu gövdeleri ve kamu kuruluşları. Bu varlık türünde ülke almallikleri ve dini dahil değildir. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `Organization` `pii-categories` . `Organization` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Birinin    

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Tıbbi şirketler ve gruplar.

        Bu varlık kategorisini almak için parametresine ekleyin `OrganizationMedical` `pii-categories` . `OrganizationMedical` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Stok alışverişi

    :::column-end:::
    :::column span="2":::

        Kambiyo senedi grupları. 

        Bu varlık kategorisini almak için parametresine ekleyin `OrganizationStockExchange` `pii-categories` . `OrganizationStockExchange` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Spor

    :::column-end:::
    :::column span="2":::

        Spor ile ilgili kuruluşlar.

        Bu varlık kategorisini almak için parametresine ekleyin `OrganizationSports` `pii-categories` . `OrganizationSports` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>Kategori: adres

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Adres

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Tam posta adresi. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `Address` `pii-categories` . `Address` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Kategori: e-posta

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        E-posta

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        E-posta adresleri. İle de döndürülür `domain=phi` .
      
        Bu varlık kategorisini almak için parametresine ekleyin `Email` `pii-categories` . `Email` algılanırsa API yanıtında döndürülür.

    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Kategori: URL

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        URL

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Web sitelerinin URL 'Leri. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `URL` `pii-categories` . `URL` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ip"></a>Kategori: IP

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        IP

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Ağ IP adresleri. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `IP` `pii-categories` . `IP` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategori: DateTime

Bu kategori aşağıdaki varlıkları içerir:

:::row:::
    :::column span="":::
        **Varlık**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Günün tarihleri ve saatleri. 

        Bu varlık kategorisini almak için parametresine ekleyin `DateTime` `pii-categories` . `DateTime` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
:::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Tarih

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Takvim tarihleri. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `Date` `pii-categories` . `Date` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategori: miktar

Bu kategori aşağıdaki varlıkları içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Miktar

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Sayılar ve sayısal miktarlar.

        Bu varlık kategorisini almak için parametresine ekleyin `Quantity` `pii-categories` . `Quantity` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Yaş

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Geçirir. 

        Bu varlık kategorisini almak için parametresine ekleyin `Age` `pii-categories` . `Age` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="2":::
        **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure bilgileri

Bu varlık kategorileri, kimlik doğrulama bilgileri ve bağlantı dizeleri dahil olmak üzere tanınabilir Azure bilgilerini içerir. `domain=phi`Parametresiyle döndürülmedi.

:::row:::
    :::column span="":::
        **Varlık**

        Azure DocumentDB kimlik doğrulama anahtarı 

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Azure Cosmos DB sunucusu için yetkilendirme anahtarı.   

        Bu varlık kategorisini almak için parametresine ekleyin `AzureDocumentDBAuthKey` `pii-categories` . `AzureDocumentDBAuthKey` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure ıAAS veritabanı bağlantı dizesi ve Azure SQL bağlantı dizesi.
        

    :::column-end:::
    :::column span="2":::

        Bir Azure hizmet olarak altyapı (IaaS) veritabanı ve SQL bağlantı dizesi için bağlantı dizesi.

        Bu varlık kategorisini almak için parametresine ekleyin `AzureIAASDatabaseConnectionAndSQLString` `pii-categories` . `AzureIAASDatabaseConnectionAndSQLString` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT bağlantı dizesi  

    :::column-end:::
    :::column span="2":::

        Azure IoT için bağlantı dizesi. 
      
        Bu varlık kategorisini almak için parametresine ekleyin `AzureIoTConnectionString` `pii-categories` . `AzureIoTConnectionString` algılanırsa API yanıtında döndürülür.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure yayımlama ayarı parolası  

    :::column-end:::
    :::column span="2":::

        Azure yayımlama ayarları için parola.

        Bu varlık kategorisini almak için parametresine ekleyin `AzurePublishSettingPassword` `pii-categories` . `AzurePublishSettingPassword` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache bağlantı dizesi 

    :::column-end:::
    :::column span="2":::

        Redsıs önbelleği için bağlantı dizesi.

        Bu varlık kategorisini almak için parametresine ekleyin `AzureRedisCacheString` `pii-categories` . `AzureRedisCacheString` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Azure hizmet olarak yazılım (SaaS) için bağlantı dizesi.

        Bu varlık kategorisini almak için parametresine ekleyin `AzureSAS` `pii-categories` . `AzureSAS` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus bağlantı dizesi

    :::column-end:::
    :::column span="2":::

        Azure Service Bus için bağlantı dizesi.

        Bu varlık kategorisini almak için parametresine ekleyin `AzureServiceBusString` `pii-categories` . `AzureServiceBusString` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure depolama hesabı anahtarı 

    :::column-end:::
    :::column span="2":::

        Bir Azure depolama hesabı için hesap anahtarı. 

        Bu varlık kategorisini almak için parametresine ekleyin `AzureStorageAccountKey` `pii-categories` . `AzureStorageAccountKey` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure depolama hesabı anahtarı (genel)

    :::column-end:::
    :::column span="2":::

        Bir Azure depolama hesabı için genel hesap anahtarı.

        Bu varlık kategorisini almak için parametresine ekleyin `AzureStorageAccountGeneric` `pii-categories` . `AzureStorageAccountGeneric` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server bağlantı dizesi 

    :::column-end:::
    :::column span="2":::

        SQL Server çalıştıran bir bilgisayar için bağlantı dizesi.

        Bu varlık kategorisini almak için parametresine ekleyin `SQLServerConnectionString` `pii-categories` . `SQLServerConnectionString` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Kimlik

[!INCLUDE [supported identification entities](./identification-entities.md)]
