---
title: Sorun Giderme Kılavuzu
titleSuffix: Microsoft Genomics
description: Hata iletileri ve bunları çözme gibi Microsoft Genomiks kullanımı için sorun giderme stratejileri hakkında bilgi edinin.
keywords: sorun giderme, hata, hata ayıklama
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486006"
---
# <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

Aşağıda, Microsoft Genomiks hizmeti, MSGEN kullanılırken olabilecek bazı yaygın sorunlardan bazıları için birkaç sorun giderme ipucu verilmiştir.

 Sorun giderme hakkında bilgi için, bkz. [genel sorular](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>1\. Adım: iş akışıyla ilişkili hata kodlarını bulma

İş akışıyla ilişkili hata iletilerini şu şekilde bulabilirsiniz:

1. Komut satırını kullanma ve `msgen status` yazma
2. StandardOutput. txt içeriğini İnceleme.

### <a name="1-using-the-command-line-msgen-status"></a>1. komut satırı `msgen status` kullanma

```bash
msgen status -u URL -k KEY -w ID 
```




Gerekli üç bağımsız değişken vardır:

* URL-API için temel URI
* ANAHTAR-Genomiks hesabınız için erişim anahtarı
    * URL 'nizi ve ANAHTARıNıZı bulmak için Azure portal adresine gidin ve Microsoft Genomiks hesabınızı açın. **Yönetim** başlığı altında **erişim tuşları**' nı seçin. Burada, API URL 'SI ve erişim anahtarlarınız bulunur.

  
* KIMLIK-iş akışı KIMLIĞI
    * `msgen list` komutunda iş akışı KIMLIK türünü bulmak için. Yapılandırma dosyanızın URL 'YI ve erişim anahtarlarınızı içerdiğini ve bulunduğu varsayılırsa, msgen 'i exe 'niz ile aynı konumda bulunur, komut şöyle görünür: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Bu komutun çıktısı şöyle görünür:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Alternatif olarak, URL ve anahtarı doğrudan girmek yerine yapılandırma dosyasının yolunu dahil edebilirsiniz. Bu bağımsız değişkenleri komut satırına ve yapılandırma dosyasına eklerseniz, komut satırı bağımsız değişkenleri öncelikli olur.  

İş akışı KIMLIĞI 1001 ve config. txt dosyası msgen 'i yürütülebiliri ile aynı yola yerleştirildiğinde, komut şöyle görünür:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. StandardOutput. txt içeriğini inceleyin 
Söz konusu iş akışının çıkış kapsayıcısını bulun. MSGEN, her iş akışı yürütmeden sonra bir `[workflowfilename].logs.zip` klasörü oluşturur. İçeriğini görüntülemek için klasörü sıkıştırmayı açın:

* outputFileList. txt-iş akışı sırasında oluşturulan çıkış dosyalarının listesi
* standartem. txt-Bu dosya boş.
* StandardOutput. txt-iş akışı çalıştırılırken oluşan hatalar da dahil olmak üzere tüm üst düzey durum iletilerini günlüğe kaydeder.
* GATK günlük dosyaları-`logs` klasördeki diğer tüm dosyalar

Sorun giderme için, StandardOutput. txt içeriğini inceleyin ve görüntülenen tüm hata iletilerini unutmayın.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>2\. Adım: sık karşılaşılan hatalar için önerilen adımları deneyin

Bu bölüm, Microsoft Genomiks hizmeti (msgen) tarafından karşılaşılan genel hataları ve bunları çözümlemek için kullanabileceğiniz stratejileri kısaca vurgular. 

Microsoft Genomiks hizmeti (msgen) aşağıdaki iki hata türünü oluşturabilir:

1. İç hizmet hataları: parametreler veya giriş dosyaları düzeltilerek çözümlenemeyebilir, hizmette iç hatalar. Bazen iş akışını yeniden gönderme bu hataları çözebilir.
2. Giriş hataları: doğru bağımsız değişkenler kullanılarak çözülebileceğiniz veya dosya biçimlerini düzelterek hatalar.

### <a name="1-internal-service-errors"></a>1. iç hizmet hataları

Bir Iç hizmet hatası kullanıcıya eylem uygulanabilir değil. İş akışını yeniden gönderebilirsiniz ancak bu işe çalışmazsa Microsoft Genomiks desteğiyle iletişim kurun

| Hata iletisi                                                                                                                            | Önerilen sorun giderme adımları                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bir iç hata oluştu. İş akışını yeniden göndermeyi deneyin. Bu hatayı bir daha görürseniz, yardım için Microsoft Genomiks desteğiyle iletişime geçin | İş akışını yeniden gönder. Bir destek [bileti](file-support-ticket-genomics.md )oluşturarak sorun devam ederse yardım Için Microsoft Genomiks desteğine başvurun. |

### <a name="2-input-errors"></a>2. Giriş hataları

Bu hatalar Kullanıcı tarafından işlem yapılabilir. Dosya türüne ve hata koduna göre, Microsoft Genomiks hizmeti ayrı hata kodları verir. Aşağıda listelenen önerilen sorun giderme adımlarını izleyin.

| Dosya türü | Hata kodu | Hata iletisi                                                                           | Önerilen sorun giderme adımları                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Herhangi biri          | 701        | Read [Readıd], [Numberoftabansayısı] temellerine sahip ancak sınır [maxReadLength]           | Bu hatanın en yaygın nedeni, iki okuma birleştirmesi için dosya bozulması 'nın lideri olur. Giriş dosyalarınızı denetleyin. |
| BAM          | 200        |   ' [YourFileName] ' dosyası okunamıyor.                                                                                       | Baa dosyasının biçimini denetleyin. Düzgün şekilde biçimlendirilen bir dosyayla iş akışını yeniden gönderebilirsiniz.                                                                           |
| BAM          | 201        |  Bad dosyası [dosya_adı] okunamıyor.                                                                                      |Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                            |
| BAM          | 202        | Bad dosyası [dosya_adı] okunamıyor. Dosya çok küçük ve üst bilgi eksik.                                                                                        | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                            |
| BAM          | 203        |   Bad dosyası [dosya_adı] okunamıyor. Dosya üstbilgisi bozuk.                                                                                      |Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                           |
| BAM          | 204        |    Bad dosyası [dosya_adı] okunamıyor. Dosya üstbilgisi bozuk.                                                                                     | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                           |
| BAM          | 205        |    Bad dosyası [dosya_adı] okunamıyor. Dosya üstbilgisi bozuk.                                                                                     | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                            |
| BAM          | 206        |   Bad dosyası [dosya_adı] okunamıyor. Dosya üstbilgisi bozuk.                                                                                      | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                            |
| BAM          | 207        |  Bad dosyası [dosya_adı] okunamıyor. Dosya, [fark] yakınında kesildi.                                                                                       | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                            |
| BAM          | 208        |   Geçersiz Bad dosyası. Readıd [Read_Id], [dosya_adı] dosyasında sıraya sahip değil.                                                                                      | Baa dosyasının biçimini denetleyin.  Doğru biçimli bir dosyayla iş akışını gönder.                                                                             |
| FASTQ        | 300        |  FASTQ dosyası okunamıyor. [Dosya_adı] bir yeni satır ile bitmiyor.                                                                                     | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                           |
| FASTQ        | 301        |   FASTQ dosyası [dosya_adı] okunamıyor. FASTQ kaydı, uzaklığında arabellek boyutundan büyük: [_fark]                                                                                      | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                         |
| FASTQ        | 302        |     FASTQ sözdizimi hatası. [Dosya_adı] dosyası boş bir satıra sahip.                                                                                    | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                         |
| FASTQ        | 303        |       FASTQ sözdizimi hatası. [Dosya_adı] dosyasının uzaklığında geçersiz bir başlangıç karakteri vardır: [_uzaklığında], satır türü: [line_type], karakter: [_Char]                                                                                  | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                         |
| FASTQ        | 304      |  FASTQ sözdizimi hatası: Readıd [_Readıd].  Toplu işin ilk okuışı, [dosya_adı] dosyasında/1 içinde hiç bitmedi.                                                                                       | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                         |
| FASTQ        | 305        |  FASTQ sözdizimi hatası: Readıd [_Readıd]. Toplu işin ikinci okuışı dosyada/2 ' de bir                                                                                      | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                          |
| FASTQ        | 306        |  FASTQ sözdizimi hatası: Readıd [_Readıd]. İkiliden ilk okuma, [dosya_adı] dosyasında/1 ile biten bir KIMLIĞE sahip değil                                                                                       | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                          |
| FASTQ        | 307        |   FASTQ sözdizimi hatası: Readıd [_Readıd]. Readıd/1 veya/2 ile bitmedi. [Dosya_adı] dosyası eşleştirilmiş bir FASTQ dosyası olarak kullanılamaz.                                                                                      |FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                          |
| FASTQ        | 308        |  FASTQ okuma hatası. Her iki ucu da farklı şekilde yanıt verdi. Doğru FASTQ dosyalarını mı seçdunuz?                                                                                       | FASTQ dosyasının biçimini düzeltip iş akışını yeniden gönderebilirsiniz.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>3\. Adım: Microsoft Genomiks desteğiyle Iletişim kurun

İş hatalarıyla karşılaşmaya devam ederseniz veya başka sorularınız varsa, Azure portal Microsoft Genomiks desteğiyle iletişime geçin. Destek isteği gönderme hakkında daha fazla bilgi [burada](file-support-ticket-genomics.md)bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Microsoft Genomiks hizmetiyle ilgili sık karşılaşılan sorunları nasıl giderebileceğiniz ve giderebileceğiniz hakkında daha fazla öğrendiniz. Daha fazla bilgi ve genel SSS hakkında daha fazla bilgi için bkz. [genel sorular](frequently-asked-questions-genomics.md). 
