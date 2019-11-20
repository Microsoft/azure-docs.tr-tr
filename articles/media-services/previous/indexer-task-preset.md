---
title: Azure Media Indexer için görev önayarı
description: Bu konu, Azure Media Indexer için görev önayara genel bir bakış sunar.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: a9a47f970f0f934e0953bd5e2d6e5575758a9c1c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873496"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure Media Indexer için görev önayarı 

Azure Media Indexer, aşağıdaki görevleri gerçekleştirmek için kullandığınız bir medya Işlemcisidir: medya dosyalarını ve içeriği aranabilir yapın, Kapalı açıklamalı altyazı parçaları ve anahtar sözcükleri oluşturun, varlığınızın bir parçası olan varlık dosyalarını dizine alın.

Bu konuda, dizin oluşturma işinize geçirmeniz gereken görev ön ayarı açıklanmaktadır. Tüm örnek için bkz. [Azure Media Indexer medya dosyalarını dizine alma](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer yapılandırması XML

Aşağıdaki tabloda, yapılandırma XML öğelerinin ve öznitelikleri açıklanmaktadır.

|Ad|Gerektirme|Açıklama|
|---|---|---|
|Girdi|true|Dizin eklemek istediğiniz varlık dosyaları.<br/>Azure Media Indexer aşağıdaki medya dosyası biçimlerini destekler: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Dosya adlarını **giriş** öğesinin **ad** veya **liste** özniteliğinde (aşağıda gösterildiği gibi) belirtebilirsiniz. Hangi varlık dosyasının dizine alınmayı belirtmezseniz, birincil dosya çekilir. Birincil varlık dosyası ayarlanmamışsa, giriş varlığının ilk dosyası dizine alınır.<br/><br/>Varlık dosya adını açıkça belirtmek için şunu yapın:<br/>```<input name="TestFile.wmv" />```<br/><br/>Aynı zamanda birden çok varlık dosyasını aynı anda dizinde (en fazla 10 dosya). Bunu yapmak için:<br/>-Bir metin dosyası (bildirim dosyası) oluşturun ve bir. lst uzantısı verin.<br/>-Bu bildirim dosyasına giriş varlığınızın tüm varlık dosya adlarının listesini ekleyin.<br/>-Bildirim dosyasını varlığa ekleyin (karşıya yükleyin).<br/>-Girişin liste özniteliğinde bildirim dosyasının adını belirtin.<br/>```<input list="input.lst">```<br/><br/>**Not:** Bildirim dosyasına 10 ' dan fazla dosya eklerseniz, dizin oluşturma işi 2006 hata koduyla başarısız olur.|
|meta veriler|false|Belirtilen varlık dosyalarının meta verileri.<br/>```<metadata key="..." value="..." />```<br/><br/>Önceden tanımlanmış anahtarlar için değerler sağlayabilirsiniz. <br/><br/>Şu anda aşağıdaki anahtarlar desteklenir:<br/><br/>**başlık** ve **Açıklama** -konuşma tanıma doğruluğunu artırmak için dil modelini güncelleştirmek üzere kullanılır.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**Kullanıcı adı** ve **parola** -http veya HTTPS aracılığıyla Internet dosyaları indirirken kimlik doğrulaması için kullanılır.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Kullanıcı adı ve parola değerleri, giriş bildirimindeki tüm medya URL 'Leri için geçerlidir.|
|SaaS Uygulamaları Geliştirme<br/><br/>Sürüm 1,2 ' ye eklenmiştir. Şu anda desteklenen tek özellik konuşma tanıma ("ASR") ' dir.|false|Konuşma tanıma özelliği aşağıdaki ayarlar anahtarlarına sahiptir:<br/><br/>Dil:<br/>-Multimedya dosyasında tanınmak için doğal dil.<br/>-İngilizce, Ispanyolca<br/><br/>CaptionFormats:<br/>-istenen çıkış resim yazısı biçimlerinin noktalı virgülle ayrılmış listesi (varsa)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>-Bir AıB dosyasının gerekli olup olmadığını belirten bir Boole bayrağı (SQL Server ve müşteri Dizin Oluşturucu IFilter ile kullanım için). Daha fazla bilgi için bkz. Azure Media Indexer ve SQL Server AıB dosyalarını kullanma.<br/>Değeri Yanlýþ<br/><br/>GenerateKeywords:<br/>-Anahtar sözcüğünün XML dosyası gerekip gerekmediğini belirten bir Boole bayrağı.<br/>Değeri Yanlýþ.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer Configuration XML örneği

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Sonraki adımlar

Bkz. [Azure Media Indexer Ile dizin oluşturma medya dosyaları](media-services-index-content.md).

