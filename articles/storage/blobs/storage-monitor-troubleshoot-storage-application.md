---
title: Azure’da bir bulut depolama uygulamasını izleme ve sorunlarını giderme | Microsoft Docs
description: Bir bulut uygulamasını izlemek ve sorunlarını gidermek için tanılama araçlarını, ölçümleri ve uyarıları kullanın.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 3a2381302a378e7b2635b84c32a87e844e4605a7
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844889"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Bulut depolama uygulamasını izleme ve sorunlarını giderme

Bu öğretici, bir serinin dördüncü ve son kısmıdır. Bulut depolama uygulamasının nasıl izleneceğini ve sorunlarının nasıl giderileceğini öğreneceksiniz.

Serinin dördüncü kısmında öğrenecekleriniz:

> [!div class="checklist"]
> * Günlük kaydını ve ölçümleri açma
> * Yetkilendirme hataları için uyarıları etkinleştirme
> * Yanlış SAS belirteçleri ile trafik testi çalıştırma
> * Günlükleri indirme ve analiz etme

[Azure Depolama Analizi](../common/storage-analytics.md), bir depolama hesabı için günlük kaydı ve ölçüm verileri sağlar. Bu veriler, depolama hesabınızın durumuna ilişkin öngörüler sağlar. Azure depolama analizi verilerini almak için günlükleri, ölçümleri ve uyarıları yapılandırabilirsiniz. Bu işlem, günlük kaydının açılmasını, ölçümlerin yapılandırılmasını ve uyarıların etkinleştirilmesini içerir.

Depolama hesaplarından günlük kaydı ve ölçümler, Azure portalındaki **Tanılama** sekmesinden etkinleştirilir. Depolama günlüğü, depolama hesabınızdaki başarılı ve başarısız istekler için ayrıntıları kaydetmenize olanak sağlar. Bu günlükler, Azure tablolarınıza, kuyruklarınıza ve bloblarınıza karşı okuma, yazma ve silme işlemlerinin ayrıntılarını görmenize olanak sağlar. Bunlar ayrıca zaman aşımları, azaltma ve yetkilendirme hataları gibi başarısız isteklerin nedenlerini görmenize de olanak sağlar.

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma

[Azure portalı](https://portal.azure.com)’nda oturum açın

## <a name="turn-on-logging-and-metrics"></a>Günlük kaydını ve ölçümleri açma

Sol menüden **Kaynak Grupları**’nı seçin, **myResourceGroup** seçeneğini belirleyin ve sonra kaynak listesinden depolama hesabınızı seçin.

**Tanılama ayarları (klasik)** bölümünde **Durum**’u **Açık** olarak ayarlayın. **Blob özellikleri**’nin altındaki tüm seçeneklerin etkinleştirildiğinden emin olun.

İşlem tamamlandığında **Kaydet**’e tıklayın

![Tanılama bölmesi](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Uyarıları etkinleştirme

Uyarılar, bir eşiği ihlal eden bir ölçüm temelinde yöneticilere e-posta göndermenin veya bir web kancası tetiklemenin yolunu sağlar. Bu örnekte, `SASClientOtherError` ölçümü için bir uyarıyı etkinleştirirsiniz.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Azure portalında depolama hesabına gidin

**İzleme** bölümünde **Uyarılar (klasik)** öğesini seçin.

**Ölçüm uyarısı ekle (klasik)** öğesini seçin ve gerekli bilgileri kullanarak **Kural ekle** formunu doldurun. **Ölçüm** açılan menüsünden `SASClientOtherError` öğesini seçin. Uyarınızın ilk hatada tetiklenmesi için **Koşul** açılan menüsünden **Büyük veya eşit**'i seçin.

![Tanılama bölmesi](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Bir hatanın benzetimini yapma

Geçerli bir uyarının benzetimini yapmak için, depolama hesabınızdan mevcut olmayan bir blobu isteme girişiminde bulunabilirsiniz. Aşağıdaki komutta bir depolama kapsayıcısı adı kullanılması gerekir. Var olan bir kapsayıcının adını kullanabilir veya bu örnek için yeni bir kapsayıcı oluşturabilirsiniz.

Yer tutucuları gerçek değerlerle değiştirin (`<INCORRECT_BLOB_NAME>` öğesinin mevcut olmayan bir değere ayarlandığından emin olun) ve komutu çalıştırın.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

Aşağıdaki görüntü, önceki örnekle birlikte çalıştırılan benzetimi yapılmış bir hatayı temel alan örnek bir uyarıdır.

 ![Örnek uyarı](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Günlükleri indirme ve görüntüleme

Depolama günlükleri, verileri, depolama hesabınızdaki **$logs** adlı bir blob kapsayıcısında blob kümeleri halinde depolar. Hesabınızdaki tüm blob kapsayıcılarını listelerseniz bu kapsayıcı gösterilmez, ancak doğrudan erişirseniz içeriklerini görebilirsiniz.

Bu senaryoda, Azure depolama hesabınızla etkileşim kurmak için [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)’ı kullanırsınız.

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzer’ı indirme

[Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226)’ı indirip uygulamayı yükleyin.

Uygulamayı başlatın ve **Dosya** > **Aç** > **Diğer Dosya Kaynaklarından** seçeneklerini belirleyin.

**Dosya Seçici** iletişim kutusunda **+ Azure Bağlantısı Ekle**’yi seçin. **Depolama hesabı adı** ve **hesap anahtarı** bilgilerinizi girin ve **Tamam**’a tıklayın.

![Microsoft Message Analyzer - Azure Depolama Bağlantısı Ekle İletişim Kutusu](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Bağlandıktan sonra, günlük bloblarını görüntülemek için depolama ağacı görünümündeki kapsayıcıları genişletin. En son günlüğü seçin ve **Tamam**’a tıklayın.

![Microsoft Message Analyzer - Azure Depolama Bağlantısı Ekle İletişim Kutusu](media/storage-monitor-troubleshoot-storage-application/figure4.png)

**Yeni Oturum** iletişim kutusunda **Başlat**’a tıklayarak günlüğünüzü görüntüleyin.

Günlük açıldıktan sonra depolama olaylarını görüntüleyebilirsiniz. Aşağıdaki görüntüde gördüğünüz gibi, depolama hesabında tetiklenen bir `SASClientOtherError` oldu. Depolama günlük kaydı hakkında ek bilgi için [Depolama Analizi](../common/storage-analytics.md)’ni ziyaret edin.

![Microsoft Message Analyzer - Olayları görüntüleme](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/), **$logs** kapsayıcısı ve içinde bulunan günlükler de dahil olmak üzere depolama hesaplarınızla etkileşim kurmak için kullanılabilen başka bir araçtır.

## <a name="next-steps"></a>Sonraki adımlar

Serinin dördüncü ve son kısmında, aşağıdaki işlemleri kapsayacak şekilde depolama hesabınızın nasıl izleneceğini ve sorunlarının nasıl giderileceğini öğrendiniz:

> [!div class="checklist"]
> * Günlük kaydını ve ölçümleri açma
> * Yetkilendirme hataları için uyarıları etkinleştirme
> * Yanlış SAS belirteçleri ile trafik testi çalıştırma
> * Günlükleri indirme ve analiz etme

Önceden oluşturulmuş depolama örneklerini görmek için bu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [Azure depolama betiği örnekleri](storage-samples-blobs-cli.md)