---
title: Veri Yönetimi Ağ Geçidi için sürüm notları
description: Veri Yönetimi ağ geçidi, sürüm notları
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: bb89ed08b05f9a8f271ceee400bc609881e7048d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682579"
---
# <a name="release-notes-for-data-management-gateway"></a>Veri Yönetimi Ağ Geçidi için sürüm notları
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de şirket içinde barındırılan tümleştirme çalışma zamanı](../create-self-hosted-integration-runtime.md).

Modern veri tümleştirmesinin güçlüklerinden biri, verileri Şirket içinden buluta ve buluta taşımadır. Data Factory, bu tümleştirmeyi, karma veri hareketini etkinleştirmek üzere şirket içinde yükleyebileceğiniz bir aracı olan Veri Yönetimi ağ geçidiyle sağlar.

Veri Yönetimi ağ geçidi ve nasıl kullanılacağı hakkında ayrıntılı bilgi için aşağıdaki makalelere bakın:

*  [Veri Yönetimi Ağ Geçidi](data-factory-data-management-gateway.md)
*  [Azure Data Factory kullanarak şirket içi ve bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>GÜNCEL SÜRÜM 
Burada sürüm notlarını daha fazla sürdürmedik. En son sürüm notlarını [buraya](https://go.microsoft.com/fwlink/?linkid=853077) alın




## <a name="earlier-versions"></a>Önceki sürümler
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Gelişmeleri
- Tüm Azure IP adreslerini güvenlik duvarınızdan (gerekirse) daha fazla listelemek yerine, hizmet veri yoluna beyaz listeye DNS girdileri ekleyebilirsiniz. İlgili DNS girişini Azure portal (Data Factory-> ' Author ve Deploy '-> ' Gateway '-> "serviceUrls" (JSON) ile bulabilirsiniz
- Bu, artık SSL doğrulamasını atlamanıza izin vererek otomatik olarak imzalanan ortak sertifikayı desteklemektedir.
- Düzeltildi: güncelleştirme sırasında ağ geçidiyle çevrimdışı sorun (saat sapması nedeniyle)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Gelişmeleri
-   Güvenlik duvarınızdan tüm Azure IP adreslerini beyaz listeye almak yerine, Service Bus beyaz listeye DNS girişleri ekleyebilirsiniz (gerekirse). Burada daha fazla ayrıntı bulabilirsiniz.
-   Artık Blok Blobu için desteklenen en büyük boyut olan 4,75 TB 'a kadar tek bir blok blobundan veri kopyalayabilirsiniz. (önceki sınır 195 GB idi).
-   Düzeltildi: kopyalama etkinliği sırasında birkaç küçük dosyanın sıkıştırması kaldırılırken yetersiz bellek sorunu.
-   Düzeltildi: belge DB 'den şirket içi SQL Server, ıdempottik özelliğiyle kopyalarken Dizin aralık dışında bir sorun oluştu.
-   Düzeltildi: SQL Temizleme betiği, kopyalama sihirbazından şirket içi SQL Server birlikte çalışmıyor.
-   Düzeltildi: sonunda boşluk olan sütun adı kopyalama etkinliğinde çalışmıyor.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Gelişmeleri
- Düzeltildi: ağ geçidi makinesi yeniden başlatıldığında eksik kimlik bilgileriyle sorun.
- Düzeltildi: yedekleme dosyası kullanılarak ağ geçidi geri yükleme sırasında kayıt ile sorun.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Gelişmeleri
- Düzeltildi: Oracle 'dan kaynak olarak yanlış ondalık null değeri okuması.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Yenilikler
- Müşteriler, ağ geçidi kayıt deneyimi hakkında geri bildirim sağlayabilir.
- Yeni bir sıkıştırma biçimini destekler: ZIP (söndür)

### <a name="enhancements-"></a>Gelişmeleri
- Oracle havuzu, bir kaynak için performans iyileştirmesi.
- Ağ Geçidi otomatik güncelleştirmesi, ağ geçidi paralel işleme kapasitesi için hata düzeltildi.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Gelişmeleri
- Geliştirilmiş ve daha sağlam ağ geçidi kayıt deneyimi-artık, ağ geçidi kayıt işlemi sırasında ilerleme durumunu izleyebilir ve bu da kayıt deneyimini daha hızlı bir şekilde gerçekleştirir.
- Ağ Geçidi geri yükleme Işleminde geliştirme-Bu güncelleştirme ile ağ geçidi yedekleme dosyasına sahip olmasanız bile ağ geçidini yine de kurtarabilirsiniz. Bu, portalda bağlı hizmet kimlik bilgilerini sıfırlamanıza gerek duyar.
- Hata düzeltildi.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Yenilikler

- Artık veri kaynağı kimlik bilgilerini yerel olarak kaydedebilirsiniz. Kimlik bilgileri şifrelenir. Veri kaynağı kimlik bilgileri kurtarılabilir ve mevcut ağ geçidinden (Şirket içi) verilebileceği yedekleme dosyası kullanılarak geri yüklenebilir.

### <a name="enhancements-"></a>Gelişmeleri

- Geliştirilmiş ve daha sağlam ağ geçidi kayıt deneyimi.
- Kopyalama sihirbazında metin biçimi için QuoteChar yapılandırması otomatik algılamayı destekler ve genel biçim algılama doğruluğunu geliştirir.

## <a name="2361002"></a>2.3.6100.2

- Şirket içi dosya sistemi ve 1 ' deki metin dosyaları için kopyalama sihirbazında firstRowAsHeader ve SkipLineCount otomatik algılamayı destekler.
- Ağ Geçidi ile Service Bus arasındaki ağ bağlantısının kararlılığını artırma
- Birkaç hata düzeltmesi


## <a name="2260721"></a>2.2.6072.1

*  Ağ Geçidi Configuration Manager kullanarak ağ geçidi için HTTP proxy ayarlamayı destekler. Yapılandırıldıysa, Azure blob, Azure tablosu, Azure Data Lake ve belge DB ile HTTP proxy üzerinden erişilir.
*  , Azure blob, Azure Data Lake Store, şirket içi dosya sistemi ve şirket içi olarak verileri kopyalarken TextFormat için üst bilgi işlemeyi destekler.
*  , Önceden desteklenen Blok Blobu ile birlikte ekleme blobu ve sayfa Blobundan veri kopyalamayı destekler.
*  , Yeni bir ağ geçidi durumu **(sınırlı)** sunarak ağ geçidinin ana Işlevselliğinin, kopyalama Sihirbazı için etkileşimli işlem desteği hariç çalışıp çalışmadığını gösterir.
*  Kayıt anahtarı kullanılarak ağ geçidi kaydının sağlamlık düzeyini geliştirir.

## <a name="216040"></a>2.1.6040.

*  DB2 sürücüsü, ağ geçidi yükleme paketine şimdi dahildir. Ayrıca yüklemeniz gerekmez.
*  DB2 sürücüsü artık desteklenen platformlar (Linux, Unix ve Windows) ile birlikte ı (AS/400) için z/OS ve DB2 'yi desteklemektedir.
*  Şirket içi veri depoları için kaynak veya hedef olarak Azure Cosmos DB kullanmayı destekler
*  , Zaten desteklenen genel amaçlı depolama hesabıyla ve soğuk/etkin blob depolamadan veri kopyalamayı destekler.
*  Uzaktan oturum açma ayrıcalıklarıyla ağ geçidi aracılığıyla şirket içi SQL Server bağlanmanızı sağlar.  

## <a name="2060131"></a>2.0.6013.1

*  El ile yükleme sırasında bir ağ geçidi tarafından kullanılacak dili/kültürü seçebilirsiniz.

*  Ağ Geçidi beklendiği gibi çalışmazsa, sorun gidermeyi kolaylaştırmak için son yedi günün ağ geçidi günlüklerini Microsoft 'a göndermenizi seçebilirsiniz. Ağ Geçidi, bulut hizmetine bağlı değilse, ağ geçidi günlüklerini kaydetmeyi ve arşivlemeyi seçebilirsiniz.  

*  Ağ Geçidi yapılandırma yöneticisi için Kullanıcı arabirimi geliştirmeleri:

    *  Giriş sekmesinde ağ geçidi durumunu daha görünür hale getirin.

    *  Yeniden düzenlenen ve Basitleştirilmiş denetimler.

    *  [Kod içermeyen kopyalama aracını](data-factory-copy-data-wizard-tutorial.md)kullanarak bir depolamadan veri kopyalayabilirsiniz. Bu özellik hakkında genel bilgi için bkz. [aşamalı kopya](data-factory-copy-activity-performance.md#staged-copy) .
*  Veri Yönetimi ağ geçidini, verileri doğrudan şirket içi SQL Server veritabanından Azure Machine Learning 'e almak için kullanabilirsiniz.

*  Performans iyileştirmeleri

    * Kod içermeyen kopyalama aracında SQL Server göre şemayı/önizlemeyi görüntüleme performansını artırabilirsiniz.

## <a name="11259531"></a>1.12.5953.1

*  Hata düzeltmeleri

## <a name="11159181"></a>1.11.5918.1

*  Ağ Geçidi olay günlüğünün en büyük boyutu 1 MB 'den 40 MB 'ye yükselmiştir.

*  Ağ geçidini otomatik güncelleştirme sırasında yeniden başlatmanın gerekli olması durumunda bir uyarı iletişim kutusu görüntülenir. Sağ veya sonraki bir sürümü yeniden başlatmayı seçebilirsiniz.

*  Otomatik güncelleştirme başarısız olursa, ağ geçidi yükleyicisi en yüksek üç kez otomatik güncellemeyi yeniden dener.

*  Performans iyileştirmeleri

    * Kod içermeyen kopyalama senaryosunda şirket içi sunucudan büyük tabloları yükleme performansını geliştirir.

*  Hata düzeltmeleri

## <a name="11058921"></a>1.10.5892.1

*  Performans iyileştirmeleri

*  Hata düzeltmeleri

## <a name="1958652"></a>1.9.5865.2

*  Sıfır dokunma otomatik güncelleştirme özelliği
*  Ağ Geçidi durum göstergeleri ile yeni tepsi simgesi
*  İstemciden "Şimdi Güncelleştir" özelliği
*  Güncelleştirme zamanlama süresini ayarlama yeteneği
*  Otomatik güncelleştirmeyi değiştirme/kapatma için PowerShell betiği
*  JSON biçimi desteği  
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

## <a name="1858221"></a>1.8.5822.1

*  Sorun giderme deneyimini geliştirme
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1757951"></a>1.7.5795.1

*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1757641"></a>1.7.5764.1

*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1657351"></a>1.6.5735.1

*  Şirket içi bir kaynak/havuz desteği
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1656961"></a>1.6.5696.1

*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1656761"></a>1.6.5676.1

*  Configuration Manager tanılama araçlarını destekleme
*  Azure Data Factory için tablo veri kaynakları için destek tablosu sütunları
*  Azure Data Factory için SQL DW 'yi destekleme
*  Azure Data Factory için BlobSource ve FileSource 'da yeniden destek
*  Azure Data Factory için Ikili kopya ile birlikte, BlobSink ve FileSink içindeki CopyBehavior, bir MergeFiles, PreserveHierarchy ve düzet hiyerarşisi desteği
*  Azure Data Factory için etkinlik raporu kopyalama ilerlemesini destekleme
*  Azure Data Factory için veri kaynağı bağlantısı doğrulamasını destekleme
*  Hata düzeltmeleri

### <a name="1656721"></a>1.6.5672.1

*  Azure Data Factory için ODBC veri kaynağı destek tablosu adı
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1656581"></a>1.6.5658.1

*  Azure Data Factory için dosya havuzunu destekleme
*  Azure Data Factory için ikili kopyada hiyerarşiyi koruma desteği
*  Azure Data Factory için kopyalama etkinliğini destekler
*  Hata düzeltmeleri

### <a name="1656401"></a>1.6.5640.1

*  Azure Data Factory için destek 3 daha fazla veri kaynağı (ODBC, OData, bir)
*  Azure Data Factory için CSV ayrıştırıcısında teklif karakteri desteği
*  Sıkıştırma desteği (BZip2)
*  Hata düzeltmeleri

### <a name="1556121"></a>1.5.5612.1

*  Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata ve Sybase) için beş ilişkisel veritabanını destekleme
*  Sıkıştırma desteği (gzip ve söndür)
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1455491"></a>1.4.5549.1

*  Azure Data Factory için Oracle veri kaynağı desteği ekle
*  Performans iyileştirmeleri
*  Hata düzeltmeleri

### <a name="1454921"></a>1.4.5492.1

*  Hem Microsoft Azure Data Factory hem de Office 365 Power BI hizmetlerini destekleyen Birleşik ikili
*  Yapılandırma Kullanıcı arabirimini ve kayıt işlemini daraltın
*  Azure Data Factory: SQL Server veri kaynağı için Azure giriş ve çıkış desteği

### <a name="1253031"></a>1.2.5303.1

*  Daha fazla zaman alan veri kaynağı bağlantısını desteklemek için zaman aşımı sorununu düzeltir.

### <a name="1155268"></a>1.1.5526.8

*  Kurulum sırasında önkoşul olarak .NET Framework 4.5.1 gerektirir.

### <a name="1051442"></a>1.0.5144.2

*  Azure Data Factory senaryoları etkileyen değişiklik yok.
