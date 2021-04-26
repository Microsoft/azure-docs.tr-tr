---
title: H264 tekli bit hızı 1080p ses 5,1 | Microsoft Docs
description: Bu konu, **H264 tekli bit hızı 1080P ses 5,1** görevi önayarıyla genel bir bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: b42238de-2a3c-4683-ae7f-7ce19ad5162e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: cc15d0cdf397ac8d61f26d3e076662caa1dc2766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014961"
---
# <a name="h264-single-bitrate-1080p-audio-51"></a>H264 Single Bitrate 1080p Audio 5.1


[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` kodlama işleri oluştururken kullanabileceğiniz bir kodlama önayarları kümesi tanımlar. `preset name`Medya dosyanızı kodlamak istediğiniz biçimi belirtmek için bir kullanabilirsiniz. Ya da kendi JSON veya XML tabanlı önayarlarınızı oluşturabilirsiniz (UTF-8 veya UTF-16 kodlaması kullanarak). Daha sonra özel önayar kodlayıcıya geçirebilirsiniz. Bu kodlayıcı tarafından desteklenen tüm önceden ayarlanmış adların listesi için `Media Encoder Standard` bkz. [Media Encoder Standard Için görev önayarları](media-services-mes-presets-overview.md).  
  
 Bu konuda, `H264 Single Bitrate 1080p Audio 5.1` XML ve JSON biçimindeki ön ayar gösterilmektedir.  
  
 Bu önceden tanımlı, 6750 kbps ve AAC 5,1 ses hızında tek bir MP4 dosyası üretir. Bu önayar için profil, bit hızı, örnekleme oranı vb. hakkında ayrıntılı bilgi için, aşağıda tanımlanan XML veya JSON 'u inceleyin. Her öğenin anlamı ve her öğe için geçerli değerler hakkında açıklamalar için [Media Encoder Standard şemasına](media-services-mes-schema.md)bakın.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>6750</Bitrate>  
          <Width>1920</Width>  
          <Height>1080</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6750</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 6750,  
          "MaxBitrate": 6750,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1080,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
