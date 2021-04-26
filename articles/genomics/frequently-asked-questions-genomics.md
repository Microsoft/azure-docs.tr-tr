---
title: Sık sorulan sorular-SSS
titleSuffix: Microsoft Genomics
description: Teknik bilgiler, SLA ve faturalandırma dahil olmak üzere Microsoft Genomiks hizmeti kullanımı ile ilgili sık sorulan soruların yanıtlarını alın.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 40fff49aa3ed46417cdd9a6190f928707e41accb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608621"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomiks: genel sorular

Bu makalede, Microsoft Genomiks ile ilgili olabilecek en iyi sorgular listelenmektedir. Microsoft Genomiks hizmeti hakkında daha fazla bilgi için bkz. [Microsoft Genomiks nedir?](overview-what-is-genomics.md). Sorun giderme kılavuzu hakkında daha fazla bilgi için [sorun giderme kılavuzumuzu](troubleshooting-guide-genomics.md)inceleyin. 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>GATK4 iş akışlarını Microsoft Genomiks üzerinde Nasıl yaparım? çalıştırmak istiyor musunuz?
Microsoft Genomiks hizmetinin config.txt dosyasında, process_name belirtin `gatk4` . Düzenli faturalandırma ücretleri üzerinden faturalandırılabileceğinizi unutmayın.

## <a name="how-do-i-enable-output-compression"></a>Çıkış sıkıştırması etkinleştirilsin mi Nasıl yaparım??
Çıkış sıkıştırması için isteğe bağlı bir bağımsız değişken kullanarak, çıkış VCF veya gvcf 'yi sıkıştırabilirsiniz. Bu, `-bgzip` `-tabix` `.gz` (bgzıp çıkışı) ve (tabx çıkışı) dosyalarını oluşturmak için, bu, VCF veya gvcf çıktısında çalıştırılan çalıştırmaya eşdeğerdir `.tbi` . `bgzip` VCF veya gvcf dosyasını sıkıştırır ve `tabix` Sıkıştırılmış dosya için bir dizin oluşturur. Bağımsız değişkeni, `false` Varsayılan olarak VCF çıktısı için ve varsayılan olarak gcvf çıktısı için olarak ayarlanan bir Boole değeri `true` . Komut satırında kullanmak için ya da olarak belirtin `-bz` `--bgzip-output` `true` (bgzip ve tabx 'i çalıştırın) veya `false` . Bu bağımsız değişkeni config.txt dosyasında kullanmak için `bgzip_output: true` `bgzip_output: false` dosya ekleyin.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Microsoft Genomiks için SLA nedir?
Microsoft Genomiks hizmeti 'nin iş akışı API 'SI isteklerini almak için kullanılabilir olduğu sürenin% 99,9 olacağını garanti ediyoruz. Daha fazla bilgi için bkz. [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Microsoft Genomiks kullanımı faturamda nasıl görünür?
Microsoft Genomiks, iş akışı başına işlenen gigabaz sayısına göre faturalandırılır. Daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>İstemci için tüm olası komutların ve bağımsız değişkenlerin listesini nerede bulabilirim `msgen` ?
Çalıştırarak, kullanılabilir komutların ve bağımsız değişkenlerin tam listesini alabilirsiniz `msgen help` . Başka bir bağımsız değişken sağlanmadıysa,,, ve için bir tane olmak üzere kullanılabilir yardım bölümlerinin bir listesini `submit` gösterir `list` `cancel` `status` . Belirli bir komut hakkında yardım almak için, yazın `msgen help command` ; Örneğin, `msgen help submit` tüm gönderme seçeneklerini listeler.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>İstemci için en sık kullanılan komutlar nelerdir `msgen` ?
En sık kullanılan komutlar, istemci için bağımsız değişkenlerdir `msgen` şunları içerir: 

 |**Komut**          |  **Alan açıklaması** |
 |:--------------------|:-------------         |
 |`list`               |Gönderdiğiniz işlerin listesini döndürür. Bağımsız değişkenler için bkz `msgen help list` ..  |
 |`submit`             |Hizmetine bir iş akışı isteği gönderir. Bağımsız değişkenler için bkz `msgen help submit` ..|
 |`status`             |Tarafından belirtilen iş akışının durumunu döndürür `--workflow-id` . Ayrıca bkz `msgen help status` .. |
 |`cancel`             |Tarafından belirtilen iş akışının işlenmesini iptal etmek için bir istek gönderir `--workflow-id` . Ayrıca bkz `msgen help cancel` .. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Değeri nereden alabilirim `--api-url-base` ?
Azure portal gidin ve Genomiks hesap sayfanızı açın. **Yönetim** başlığı altında **erişim tuşları**' nı seçin. Burada, API URL 'SI ve erişim anahtarlarınız bulunur.

## <a name="where-do-i-get-the-value-for---access-key"></a>Değeri nereden alabilirim `--access-key` ?
Azure portal gidin ve Genomiks hesap sayfanızı açın. **Yönetim** başlığı altında **erişim tuşları**' nı seçin. Burada, API URL 'SI ve erişim anahtarlarınız bulunur.

## <a name="why-do-i-need-two-access-keys"></a>Neden iki erişim anahtarına ihtiyacım var?
Hizmetin kullanımını kesintiye uğramadan güncelleştirmek (yeniden oluşturmak) istemeniz durumunda iki erişim anahtarınız olması gerekir. Örneğin, ilk anahtarı güncelleştirmek istiyorsanız, tüm yeni iş akışlarının ikinci anahtarı kullanması gerekir. Ardından, ilk anahtarı güncelleştirmeden önce, ilk anahtarı kullanarak tüm iş akışlarının bitmesini bekleyin.

## <a name="do-you-save-my-storage-account-keys"></a>Depolama hesabı anahtarlarımı Kaydet?
Depolama hesabı anahtarınız, giriş dosyalarınızı okumak ve çıktı dosyalarını yazmak üzere Microsoft Genomiks hizmeti için kısa süreli erişim belirteçleri oluşturmak üzere kullanılır. Varsayılan belirteç süresi 48 saattir. Belirteç süresi, `-sas/--sas-duration` Gönder komutu seçeneğiyle değiştirilebilir; değer saat cinsinden olur.

## <a name="does-microsoft-genomics-store-customer-data"></a>Microsoft, müşteri verilerini mi depolukoşsın?

Hayır. Microsoft Genomiks hiçbir müşteri verisi depolamaz.

## <a name="what-genome-references-can-i-use"></a>Hangi genom başvurularını kullanabilirim?

Bu başvurular desteklenir:

 |Başvuru              | Değeri `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (alt analiz yok) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Komut satırı bağımsız değişkenlerimin bir yapılandırma dosyası olarak biçimlendirilmesi Nasıl yaparım?? 

msgen 'i yapılandırma dosyalarını aşağıdaki biçimde anlamıştır:
* Tüm seçenekler, anahtar-değer çiftleri olarak anahtarlardan virgülle ayrılmış değerlerle birlikte sağlanır.
  Boşluk yoksayıldı.
* İle başlayan satırlar `#` yoksayıldı.
* Uzun biçimdeki herhangi bir komut satırı bağımsız değişkeni, önde gelen çizgileri gerçekleştirerek ve alt çizgi içeren kelimeler arasındaki tireleri değiştirerek bir anahtara dönüştürülebilir. Bazı dönüştürme örnekleri şunlardır:

  |Komut satırı bağımsız değişkeni            | Yapılandırma dosyası satırı |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key: anahtar*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Genomiks ile çalışmaya başlamak için aşağıdaki kaynakları kullanın:
- İlk iş akışınızı Microsoft Genomiks hizmeti aracılığıyla çalıştırarak başlayın. [Microsoft Genomiks hizmeti üzerinden iş akışı çalıştırma](quickstart-run-genomics-workflow-portal.md)
- Microsoft Genomiks hizmeti tarafından işlenmek üzere kendi verilerinizi gönderme: [eşleştirilmiş fastq](quickstart-input-pair-FASTQ.md)  |  [Bad](quickstart-input-BAM.md)  |  [birden çok fastq veya Baa](quickstart-input-multiple.md) 

