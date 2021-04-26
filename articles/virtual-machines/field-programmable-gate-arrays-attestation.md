---
title: Azure FPGFA kanıtlama hizmeti
description: NP serisi VM 'Ler için kanıtlama hizmeti.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: a3408d30a9caa24355cf3976235c3a9b8061b95f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531229"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Azure NP-Series VM 'Ler için FPGA kanıtlama (Önizleme)

FPGA kanıtlama hizmeti, Xilinx araç takımı tarafından oluşturulan bir tasarım denetim noktası dosyasında ("netlist" olarak adlandırılır) bir dizi doğrulama gerçekleştirir ve, bir NP serisi sanal makinesinde Xilinx U250 FPGA kartına yüklenebilen doğrulanan görüntüyü ("Bitstream" olarak adlandırılır) içeren bir dosya oluşturur.  

## <a name="prerequisites"></a>Önkoşullar  

Bir Azure aboneliğine ve bir Azure depolama hesabına ihtiyacınız olacaktır. Abonelik, Azure 'a erişmenizi sağlar ve depolama hesabı, kanıtlama hizmetinin netlist ve Output dosyalarını tutmak için kullanılır.  

Kanıtlama istekleri göndermek için PowerShell ve Bash betikleri sağlıyoruz.   Betikler, Windows ve Linux üzerinde çalışabilen Azure CLı kullanır. PowerShell, Windows, Linux ve macOS üzerinde çalışabilir.  

Azure CLı indirmesi (gerekli):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Windows, Linux ve macOS indirme için PowerShell (yalnızca PowerShell betikleri için):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Kanıtlama hizmetine göndermek için kiracınızın ve abonelik KIMLIĞINIZIN yetkilendirilmiş olması gerekir. https://aka.ms/AzureFPGAAttestationPreviewErişim istemek için ziyaret edin. 

## <a name="building-your-design-for-attestation"></a>Kanıtlama için tasarımınızı oluşturma  

Tasarım oluşturmak için tercih edilen Xilinx araç takımı, 2020,2 ' dir. Araç takımının önceki bir sürümüyle oluşturulmuş ve hala 2020,2 ile uyumlu olan netlist dosyaları kullanılabilir. Derlemek için doğru kabuğu yüklediğinizden emin olun. Şu anda desteklenen sürüm xilinx_u250_gen3x16_xdma_2_1_202010_1. Destek dosyaları Xilinx Alveo Lounge 'dan indirilebilir. 

Bitstream yerine netlist içeren bir xclbin dosyası oluşturmak için, aşağıdaki bağımsız değişkeni (v + + cmd Line) de dahil etmeniz gerekir.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Azure 'da oturum açma  

Azure ile herhangi bir işlem gerçekleştirmeden önce Azure 'da oturum açmanız ve hizmeti çağırmak için yetkilendirilmiş aboneliği ayarlamanız gerekir. ```az login``` ```az account set –s <Sub ID or Name>``` Bu amaçla ve komutlarını kullanın. Bu işlemle ilgili daha fazla bilgi burada belgelenmiştir:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Komut satırında ' etkileşimli olarak oturum aç ' veya ' kimlik bilgileriyle oturum aç ' seçeneğini kullanın.  

## <a name="creating-a-storage-account-and-blob-container"></a>Depolama hesabı ve BLOB kapsayıcısı oluşturma  

Netlist dosyanızın kanıtlama hizmeti tarafından erişim için bir Azure Storage blob kapsayıcısına yüklenmesi gerekir.  

Hesabı, kapsayıcıyı oluşturma ve netlist dosyanızı bir blob olarak bu kapsayıcıya yükleme hakkında daha fazla bilgi için bu sayfaya bakın: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

Ayrıca, bunun için Azure portal de kullanabilirsiniz.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Netlist dosyanızı Azure Blob depolama alanına yükleme  

Dosyayı kopyalamanın birkaç yolu vardır; az Storage upload cmdlet 'ini kullanan bir örnek aşağıda gösterilmiştir. Az komutlar hem Linux hem de Windows üzerinde çalışır. "Blob" adı için herhangi bir ad seçebilirsiniz, ancak xclbin uzantısını sakladığınızdan emin olun. 

```az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Kanıtlama betiklerini indirin  

Doğrulama betikleri aşağıdaki Azure Storage blob kapsayıcısından indirilebilir:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

ZIP dosyasında iki PowerShell betiği bulunur, biri gönderilir ve diğeri, üçüncü dosya her iki işlevi de gerçekleştiren bir bash betiğiyle izlenir.  

## <a name="running-the-attestation-scripts"></a>Kanıtlama betikleri çalıştırılıyor  

Betikleri çalıştırmak için, depolama hesabınızın adını, netlist dosyasının saklandığı blob kapsayıcısının adını ve netlist dosyasının adını sağlamanız gerekir. Ayrıca, kapsayıcınıza (netlist değil) okuma/yazma erişimi veren bir hizmet paylaşılan erişim imzası (SAS) oluşturmanız gerekir. Bu SAS, kanıtlama hizmeti tarafından netlist dosyanızın yerel bir kopyasını oluşturmak ve doğrulama işleminin sonuç çıktı dosyalarını kapsayıcınıza geri yazmak için kullanılır.  

Burada kullanılabilen hizmet sa 'ları hakkında belirli bilgiler ile paylaşılan erişim imzaları 'na genel bakış sunulmaktadır. Hizmet SAS sayfası, oluşturulan SAS koruması hakkında önemli bir uyarı içerir.  SAS 'ın kötü amaçlı veya istenmeyen bir kullanıma karşı korunmasını sağlama gereksinimini anlamak için dikkat edin.  

Az Storage Container Generate-SAS cmdlet 'ini kullanarak Kapsayıcınız için bir SAS oluşturabilirsiniz. Zaman aşımı süresini UTC biçiminde, gönderim zamanından en az birkaç saat geçti; 6 saat içinde, yeterli sayıda olmalıdır.  

Sanal dizinleri kullanmak istiyorsanız, kapsayıcı bağımsız değişkeninin parçası olarak dizin hiyerarşisini dahil etmeniz gerekir. Örneğin, "netlists" adlı bir kapsayıcınız varsa ve netlist blobu içeren "image1" adlı bir sanal dizine sahipseniz, kapsayıcı adı olarak "netlists/image1" belirtmeniz gerekir. Daha derin bir hiyerarşi belirtmek için ek dizin adlarını ekleyin. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Gönderiminizin durumu denetleniyor  

Kanıtlama hizmeti, gönderiminizin düzenleme KIMLIĞINI döndürür. Gönderim betikleri, tamamlama için yoklama yaparak gönderimi izlemeye otomatik olarak başlar. Düzenleme KIMLIĞI, Gönderiminize ne olduğunu incelemesinin asıl yoludur, böylece bir sorun olması durumunda bunu yapın. Başvuru noktaları olarak, kanıtlama küçük bir netlist dosyası (300MB boyutunda) için yaklaşık 30 dakika sürer; 1.6 GB dosyası bir saat sürdü. 

Bir bağımsız değişken olarak Orchestration ID 'sini sağlayarak durum ve kanıtlama sonuçlarını almak için Monitor-Validation.ps1 betiğini dilediğiniz zaman çağırabilirsiniz:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Alternatif olarak, kanıtlama hizmeti uç noktasına HTTP POST isteği gönderebilirsiniz:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

İstek gövdesi, kanıtlama isteğinizin abonelik KIMLIĞINI, kiracı KIMLIĞINI ve düzenleme KIMLIĞINI içermelidir:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Doğrulama sonrası adımlar

Hizmet çıktıyı kapsayıcınıza geri yazar. Doğrulama başarılı olursa, Kapsayıcınız orijinal netlist dosyasına (ABC) sahip olur. xclbin) bir bit akışa sahip bir dosya (ABC. bit. xclbin), depolanan Bitstream (ABC. Azure. xclbin) ve dört günlük dosyasının özel konumunu tanımlayan bir dosya: biri başlangıç işlemi (abc-log.txt) ve her biri için doğrulama gerçekleştiren üç paralel aşama için. Bunlar, X 'in aşama için bir sayı olduğu * logPhaseX.txt olarak adlandırılır. Azure. xclbin, sanal makinenizde doğrulanan görüntünün U250 'e yüklenmesini bildirmek için kullanılır. 

Doğrulama başarısız olursa, hangi adımın başarısız olduğunu gösteren bir Error-*. txt dosyası yazılır. Hata günlüğü kanıtlama başarısız olduğunu gösteriyorsa günlük dosyalarını da kontrol edin. Destek için bize başvururken, lütfen tüm bu dosyaları destek isteğinin bir parçası olarak Orchestration KIMLIĞIYLE birlikte eklediğinizden emin olun.  

Azure portal kullanarak, kapsayıcınızı oluşturabilir ve ağ listenizi karşıya yükleyebilir ve Bitstream ve günlük dosyalarını indirmeli. Bir kanıtlama isteği göndermek ve bunun ilerlemesini Portal üzerinden izlemek, şu anda desteklenmez ve yukarıda açıklandığı gibi betikler aracılığıyla yapılmalıdır. 

