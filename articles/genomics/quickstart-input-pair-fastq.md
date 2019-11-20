---
title: FASTQ dosya girişlerini kullanarak iş akışı gönderme
titleSuffix: Microsoft Genomics
description: Bu makalede, giriş dosyalarınız tek bir FASTQ dosyası çiftinde olduğunda Microsoft Genomiks hizmetine bir iş akışı gönderme gösterilmektedir.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248555"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Microsoft Genomiks 'de FASTQ dosya girişlerini kullanarak iş akışı gönderme

Bu makalede, giriş dosyalarınız tek bir FASTQ dosyası çiftinde olduğunda Microsoft Genomiks hizmetine bir iş akışı gönderme gösterilmektedir. Bu konu, `msgen` istemcisini zaten yüklediğinizi ve çalıştırdığınızı varsayar ve Azure Storage 'ı nasıl kullanacağınızı öğrenirsiniz. Belirtilen örnek verileri kullanarak bir iş akışını başarıyla gönderdiyseniz, bu makaleye devam etmeye hazırsınızdır. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Kurulum: FASTQ dosyalarınızı Azure depolama 'ya yükleme
*Reads_1. FQ. gz* ve *reads_2. FQ. gz*adlı iki dosyanız olduğunu varsayalım ve bunları Azure 'daki *myaccount* depolama hesabınıza **https://<span></span>myaccount. blob. Core<span></span>. Windows<span></span>.net/<span></span> girişler/reads_1<span></span>. FQ<span></span>. gz<span> </span>**  ve **https://<span></span>myaccount. blob. Core.<span> </span> Windows<span></span>.net/<span></span>girişler/<span></span>reads_2. FQ<span></span>. gz<span></span>** . API URL 'SI ve erişim anahtarınız vardır. **<span></span>Https://myaccount. blob. Core<span></span>. Windows<span></span>.net<span></span>/çıkışları<span></span>** içinde çıkış almak istiyorsunuz.


## <a name="submit-your-job-to-the-msgen-client"></a>İşinizi `msgen` istemcisine gönderme 

@No__t-0 istemcisine sağlamanız gereken en az bağımsız değişken kümesi aşağıda verilmiştir; açıklık için satır sonları eklenmiştir:

Windows için:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
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
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
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
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

@No__t-0 dosyasını bu çağrıdan gönder: `msgen submit -f config.txt`

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure depolama 'ya bir çift FASTQ dosyası yüklediniz ve `msgen` Python istemcisi üzerinden Microsoft Genomiks hizmetine bir iş akışı gönderdiniz. İş akışı gönderme ve Microsoft Genomiks hizmetiyle kullanabileceğiniz diğer komutlar hakkında daha fazla bilgi edinmek için bkz. [SSS](frequently-asked-questions-genomics.md). 
