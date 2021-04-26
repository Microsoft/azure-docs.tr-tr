---
title: Azure Data Factory UX sorunlarını giderme
description: Azure Data Factory UX sorunlarını giderme hakkında bilgi edinin.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382846"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Azure Data Factory UX sorunlarını giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Data Factory UX için genel sorun giderme yöntemleri incelenmektedir.

## <a name="adf-ux-not-loading"></a>ADF UX yüklenmiyor

> [!NOTE]
> Azure Data Factory UX resmi, Microsoft Edge ve Google Chrome 'ı destekler. Diğer Web tarayıcılarının kullanılması beklenmeyen veya belgelenmemiş davranışa neden olabilir.

### <a name="third-party-cookies-blocked"></a>Üçüncü taraf tanımlama bilgileri engellendi

ADF UX Kullanıcı oturumunu kalıcı hale getirmek ve etkileşimli geliştirme ve izleme deneyimlerini etkinleştirmek için tarayıcı tanımlama bilgilerini kullanır. Bir ınbilito oturumu kullandığınız veya bir ad engelleyicisinin etkinleştirildiğinden, tarayıcınız üçüncü taraf tanımlama bilgilerini engelliyor olabilir. Üçüncü taraf tanımlama bilgilerinin engellenmesi, Portal yüklenirken, boş bir sayfaya ' ' yönlendirilmekte https://adf.azure.com/accesstoken.html veya üçüncü taraf tanımlama bilgilerinin engellendiğini bildiren bir uyarı iletisi almaya çalışırken soruna neden olabilir. Bu sorunu çözmek için, aşağıdaki adımları kullanarak tarayıcınızda üçüncü taraf tanımlama bilgileri seçeneklerini etkinleştirin:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Tüm tanımlama bilgilerine izin ver

1. Tarayıcınızda **Chrome://Settings/Cookies** adresini ziyaret edin.
1. **Tüm tanımlama bilgilerine Izin ver** seçeneğini belirleyin 

    ![Chrome 'daki tüm tanımlama bilgilerine izin ver](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. ADF UX 'i yenileyip yeniden deneyin.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Yalnızca ADF UX 'in tanımlama bilgilerini kullanmasına izin ver
Tüm tanımlama bilgilerine izin vermek istemiyorsanız, isteğe bağlı olarak yalnızca ADF UX 'e izin verebilirsiniz:
1. **Chrome://Settings/Cookies** adresini ziyaret edin.
1. **Tanımlama bilgilerini her zaman kullanan siteler** altında **Ekle** seçeneğini belirleyin 

    ![Chrome 'daki izin verilen sitelere ADF UX ekleme](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. **ADF.Azure.com** sitesi ekleyin, **Tüm tanımlama bilgilerini** denetleyin ve kaydedin. 

    ![ADF UX sitesinden tüm tanımlama bilgilerine izin ver](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. ADF UX 'i yenileyip yeniden deneyin.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Tarayıcınızda **Edge://settings/content/Cookies** adresini ziyaret edin.
1. **Sitelerin tanımlama bilgisi verilerini kaydetmesine ve okumasına Izin ver** özelliğinin etkinleştirildiğinden ve **üçüncü taraf tanımlama bilgilerini engelleme** seçeneğinin devre dışı olduğundan emin olun 

    ![Kenarda tüm tanımlama bilgilerine izin ver](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. ADF UX 'i yenileyip yeniden deneyin.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Yalnızca ADF UX 'in tanımlama bilgilerini kullanmasına izin ver

Tüm tanımlama bilgilerine izin vermek istemiyorsanız, isteğe bağlı olarak yalnızca ADF UX 'e izin verebilirsiniz:

1. **Edge://settings/content/Cookies** adresini ziyaret edin.
1. **Izin ver** bölümünde, **Ekle** ve **ADF.Azure.com** sitesi Ekle ' yi seçin. 

    ![Kenarda izin verilen sitelere ADF UX ekleme](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. ADF UX 'i yenileyip yeniden deneyin.

## <a name="connection-failed-on-adf-ux"></a>ADF UX üzerinde bağlantı başarısız oldu

Bazen, **bağlantı sınama**, **Önizleme**, vb. tıkladıktan sonra AŞAĞıDAKI ekran görüntüsüne benzer ADF UX üzerinde "bağlantı başarısız oldu" hataları görürsünüz.

![Bağlantı başarısız oldu](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Bu durumda, önce tarayıcınızda InPrivate Gözatma moduyla aynı işlemi deneyebilirsiniz.

Hala çalışmıyorsa, tarayıcıda **Geliştirici Araçları** açmak için F12 tuşuna basın. **Ağ** sekmesine gidin, **önbelleği devre dışı bırak**' ı işaretleyin, başarısız olan işlemi yeniden deneyin ve başarısız isteği bulun (kırmızı).

![Başarısız istek](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Ardından, başarısız isteğin **Istek URL** 'sinden **ana bilgisayar adını** (Bu örnekte **dpnortheurope.svc.DataFactory.Azure.com**) bulun.

Tarayıcınızın adres çubuğuna doğrudan **ana bilgisayar adını** yazın. Tarayıcıda 404 görürseniz, bu genellikle istemci tarafın Tamam olduğu ve sorun ADF hizmeti tarafında olduğu anlamına gelir. ADF UX hata iletisindeki **etkınlık kimliği** ile bir destek bileti dosyası.

![Kaynak bulunamadı](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Aksi takdirde, tarayıcıda aşağıda benzer bir hata görürseniz, bu genellikle bir istemci tarafı sorunu olduğu anlamına gelir. Sorun giderme adımlarını izleyin.

![İstemci tarafı hatası](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

**Komut istemi** ' ni açın ve **nslookup dpnortheurope.svc.DataFactory.Azure.com** yazın. Normal bir yanıt aşağıdaki gibi görünmelidir:

![Komut yanıtı 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Normal bir DNS yanıtı görürseniz, bu ana bilgisayar adına yönelik HTTPS bağlantısının engellenip engellenmeyeceğini öğrenmek için güvenlik duvarı ayarlarını kontrol etmek üzere yerel BT destek ekibiyle iletişime geçin. Sorun çözülemezse, ADF UX hata iletisindeki **etkınlık kimliği** ile bir destek bileti dosyası verin.

Bundan başka bir şey görürseniz, bu genellikle DNS adı çözümlenirken DNS sunucunuzda bir sorun olduğu anlamına gelir. Genellikle ISS (Internet servis sağlayıcısı) veya DNS (örneğin, Google DNS 8.8.8.8 'e) olarak değişen bir geçici çözüm olabilir. Sorun devam ederse, DNS çözümlemenizin başarısız olduğunu görmek için **nslookup DataFactory.Azure.com** ve **nslookup Azure.com** ' yi daha da deneyebilirsiniz ve sorun giderme IÇIN tüm bilgileri yerel BT desteğe veya ISS 'nize gönderebilirsiniz. Sorunun hala Microsoft tarafında olduğuna inanıyorsanız, ADF UX hata iletisindeki **etkınlık kimliği** ile bir destek bileti dosyası verin.

![Komut yanıtı 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Veri kümelerinde bağlı hizmet türünü değiştirme

Dosya biçimi veri kümesi, tüm dosya tabanlı bağlayıcılar ile kullanılabilir. Örneğin, Azure Blob veya Azure Data Lake Storage 2. bir Parquet veri kümesi yapılandırabilirsiniz. Her bağlayıcının etkinlikteki farklı veri deposu ve farklı uygulama modeliyle ilgili ayarları desteklemesi gerektiğini göz önünde saklayın. 

ADF yazma Kullanıcı arabiriminde, kopyalama, arama, GetMetadata, silme etkinlikleri gibi bir etkinlikte bir dosya biçimi veri kümesi kullandığınızda ve geçerli bir türdeki (örneğin, dosya sisteminden ADLS 2. geçiş) bağlı bir hizmete işaret etmek istiyorsanız aşağıdaki uyarı iletisini görürsünüz. Bu veri kümesine başvuran işlem hatları ve Etkinlikler, bir temiz anahtar olduğundan emin olmak için yeni türü de kullanacak şekilde değiştirilecek ve yeni tür ile uyumlu olmayan mevcut tüm veri deposu ayarları artık geçerli olmadığı için temizlenir.

Her bir bağlayıcının desteklenen veri deposu ayarlarının daha fazla bilgi edinmek için, ayrıntılı Özellik listesini görmek üzere ilgili bağlayıcı makalesi-> kopyalama etkinliği özellikleri ' ne gidebilirsiniz. [Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md)'ye bakın.

![Uyarı iletisi](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

* [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
* [Azure videoları](https://azure.microsoft.com/resources/videos/index/)
* [Soru sayfası Microsoft Q&](/answers/topics/azure-data-factory.html)