---
title: Birden çok giriş kullanarak iş akışı gönderme
titleSuffix: Microsoft Genomics
description: Bu makalede, giriş dosyanız aynı örnekten birden fazla FASTQ veya Baa dosyası olduğunda Microsoft Genomiks hizmetine bir iş akışı gönderme işlemi gösterilmektedir.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b426015906a8e17674123c0c3ad2fccb9c43798f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248561"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Aynı örnekten birden çok giriş kullanarak iş akışı gönderme

Bu makalede, giriş dosyanız **aynı örnekten gelen**bırden çok fastq veya Baa dosyası olduğunda Microsoft Genomiks hizmetine bir iş akışı gönderme işlemi gösterilmektedir. Örneğin, Sıralayıcı 'daki birden fazla kulvarda **aynı örneği** çalıştırdıysanız, Sıralayıcı her Lane için BIR ÇIFT fastq dosyası çıktısı verebilir. Bu FASTQ dosyalarını hizalama ve varyant çağrılmadan önce eklemek yerine, tüm bu girdileri `msgen` istemcisine doğrudan gönderebilirsiniz. @No__t-0 istemcisinden alınan çıkış,. Bad,. Bai,. vcf dosyası dahil olmak üzere tek bir dosya **kümesi** olacaktır. 

Bununla birlikte, aynı gönderim içinde FASTQ ve BAI dosyalarını karıştırmayacağınızı unutmayın. Ayrıca, birden çok bireden birden fazla FASTQ veya Baa **dosyası gönderemezsiniz.** 

Bu makalede, `msgen` istemcisini zaten yüklediğinizi ve çalıştırdığınız ve Azure depolama 'yı kullanma hakkında bilgi sahibi olduğunuz varsayılır. Belirtilen örnek verileri kullanarak bir iş akışını başarıyla gönderdiyseniz, bu makaleye devam etmeye hazırsınızdır. 


## <a name="multiple-bam-files"></a>Birden çok Baa dosyası

### <a name="upload-your-input-files-to-azure-storage"></a>Giriş dosyalarınızı Azure Storage 'a yükleme
Giriş olarak birden çok BAM dosyası olduğunu, *. bam*, *additional_reads. bam*ve *yet_more_reads. bam*dosyalarını okuduğunu ve Azure 'daki *myaccount* depolama hesabınıza yüklediğinizi varsayalım. API URL 'SI ve erişim anahtarınız vardır. **<span></span>Https://myaccount. blob. Core<span></span>. Windows<span></span>.net<span></span>/çıkışları<span></span>** içinde çıkış almak istiyorsunuz.


### <a name="submit-your-job-to-the-msgen-client"></a>İşinizi `msgen` istemcisine gönderme 

Tüm adlarını--input-blob-Name-1 bağımsız değişkenine geçirerek birden çok Bad dosyası gönderebilirsiniz. Tüm dosyaların aynı örnekten gelmesi gerektiğini, ancak sırası önemli olmadığına dikkat edin. Aşağıdaki bölümde, Windows 'daki bir komut satırından, Unix 'de ve bir yapılandırma dosyası kullanılırken örnek teslimleri ayrıntılı olarak verilmiştir. Açıklık için satır sonları eklenmiştir:


Windows için:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


UNIX için

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Bir yapılandırma dosyası kullanmayı tercih ediyorsanız, şunları içerir:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

@No__t-0 dosyasını bu çağrıdan gönder: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Birden çok eşleştirilmiş FASTQ dosyası

### <a name="upload-your-input-files-to-azure-storage"></a>Giriş dosyalarınızı Azure Storage 'a yükleme
Giriş, *reads_1. FQ. gz* ve *reads_2. FQ. gz*, *additional_reads_1. FQ. gz* ve *additional_reads_2. FQ. gz*ve *yet_more_reads_1. FQ. gz* ve yet_more_reads_ olarak birden fazla eşleştirilmiş fastq dosyanız olduğunu varsayalım  *2. FQ. gz*. Bunları Azure 'daki *myaccount* depolama hesabınıza yüklediniz ve siz de KULLANABILIRSINIZ. API URL 'si ve erişim anahtarınız vardır. **<span></span>Https://myaccount. blob. Core<span></span>. Windows<span></span>.net<span></span>/çıkışları<span></span>** içinde çıkış almak istiyorsunuz.


### <a name="submit-your-job-to-the-msgen-client"></a>İşinizi `msgen` istemcisine gönderme 

Eşleştirilmiş FASTQ dosyalarının yalnızca aynı örnekten gelmesi gerekmez, ancak aynı zamanda birlikte işlenmeleri gerekir.  Dosya adlarının sırası--input-blob-Name-1 ve--input-blob-Name-2 ' ye bağımsız değişken olarak geçirildiğinde önemlidir. 

Aşağıdaki bölümde, Windows 'daki bir komut satırından, Unix 'de ve bir yapılandırma dosyası kullanılırken örnek teslimleri ayrıntılı olarak verilmiştir. Açıklık için satır sonları eklenmiştir:


Windows için:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


UNIX için:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Bir yapılandırma dosyası kullanmayı tercih ediyorsanız, şunları içerir:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

@No__t-0 dosyasını bu çağrıdan gönder: `msgen submit -f config.txt`

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, birden çok BAı dosyasını veya eşleştirilmiş FASTQ dosyalarını Azure depolama 'ya yüklediniz ve `msgen` Python istemcisi üzerinden Microsoft Genomiks hizmetine bir iş akışı gönderdiniz. İş akışı gönderme ve Microsoft Genomiks hizmetiyle kullanabileceğiniz diğer komutlar hakkında daha fazla bilgi için bkz. [SSS](frequently-asked-questions-genomics.md). 