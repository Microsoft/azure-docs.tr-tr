---
title: HEVC için Azure Media Services-Kesintisiz Akış Protocol (MS-SSTR) Düzeltme | Microsoft Docs
description: Bu belirtim, Azure Media Services 'de HEVC ile parçalanmış MP4 tabanlı canlı akış için protokolü ve biçimi açıklar. Bu, HEVC alma ve akış desteği dahil olmak üzere Kesintisiz Akış Protokolü belgelerinin (MS-SSTR) bir düzeltmesi. Bu makalede yalnızca HEVC sağlamak için gereken değişiklikler belirtilmiştir, yani "(değişiklik yok)", metnin yalnızca açıklama için kopyalanacağını gösterir.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: e0637b2a015a610f9c3f92809f63a442980b63b1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624811"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>HEVC için Kesintisiz Akış Protocol (MS-SSTR) Düzeltme 

## <a name="1-introduction"></a>1 giriş 

Bu makalede, HEVC kodlamalı videonun Kesintisiz Akış etkinleştirmek için, Kesintisiz Akış protokol belirtimi [MS-SSTR] uygulanacak ayrıntılı değişiklik işlemleri sunulmaktadır. Bu belirtimde, yalnızca HEVC video codec bileşenini sağlamak için gereken değişiklikleri özetler. Makale, [MS-SSTR] belirtimiyle aynı numaralandırma şemasını izler. Makale genelinde sunulan boş başlıklar, okuyucuyu [MS-SSTR] belirtimindeki konumlarına yönlendirmek için sağlanır.  "(Değişiklik yok)", metnin yalnızca açıklama amacıyla kopyalanacağını gösterir.

Bu makalede, kesintisiz akış bildiriminde HEVC video codec sinyal sinyali (' hev1 ' veya ' hvc1 ' biçim izleri kullanılarak) için teknik uygulama gereksinimleri sağlanır ve 1.5 başvuruları, geçerli MPEG standartlarına başvuracak şekilde güncelleştirilir. HEVC dahil, HEVC Common Encryption ve ISO temel medya dosyası biçimi için Box adları, en son belirtimlerle tutarlı olacak şekilde güncelleştirilmiştir. 

Başvurulan Kesintisiz Akış protokol belirtimi [MS-SSTR], ses ve video gibi canlı ve isteğe bağlı dijital medyayı (bir kodlayıcıdan bir Web sunucusuna, sunucudan başka bir sunucuya ve bir sunucudan bir HTTP istemcisine.
HTTP üzerinden MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787)tabanlı veri yapısı teslimi kullanımı, sıkıştırılmış medya içeriklerinin farklı kalite düzeyleri arasında neredeyse gerçek zamanlı olarak geçişe olanak tanır. İstemci bilgisayar veya cihaz için ağ ve video işleme koşulları değişse bile, HTTP istemcisi son kullanıcısına ilişkin sabit bir kayıttan yürütme deneyimi elde edersiniz.

## <a name="11-glossary"></a>1,1 sözlüğü 

Aşağıdaki terimler *[MS-GLOS]* içinde tanımlanmıştır:

>   **genel benzersiz tanımlayıcı (GUID) evrensel benzersiz tanımlayıcı (UUID)**

Aşağıdaki terimler bu belgeye özeldir:

>  **oluşturma zamanı:** [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)bölümünde tanımlandığı gibi, istemcide bir örnek gösterilir.
> 
>   **CENC**: [ISO/ıEC 23001-7] Second Edition içinde tanımlandığı şekilde Common Encryption.
> 
>   **kod çözme Zamanı:** Örneğin, [[ISO/ıec 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695)bölümünde tanımlandığı gibi, istemcide kodu çözülecek zaman.

**parçada** Bir veya daha fazla **örnek**içeren, karşıdan yüklenebilen bir **medya** birimi.

>   **HEVC:** Yüksek verimlilik video kodlama, [ISO/ıEC 23008-2] içinde tanımlandığı şekilde
> 
>   **bildirim** Bir istemcinin **medya**isteği yapmasına izin veren **sunuyla** ilgili meta veriler. **medyasını** Bir **sunuyu**oynatmak için istemci tarafından kullanılan sıkıştırılmış ses, video ve metin verileri. **medya biçimi:** Ses veya videoyu sıkıştırılmış bir **örnek**olarak göstermek için iyi tanımlanmış bir biçim.
> 
>   **Sununuzda** Tek bir filmi oynatmak için gereken tüm **akışlar** ve ilgili meta veriler kümesi. **isteyen** [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **yanıtında** tanımlandığı şekılde istemciden sunucuya gönderilen HTTP iletisi: [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) içinde tanımlandığı şekilde sunucudan ISTEMCIYE gönderilen HTTP iletisi
> 
>   **örnekli** **Medyanın** depolandığı ve işlendiği en küçük temel birim (örneğin, bir çerçeve).
> 
>   **ŞUNLARI YAPMANIZ GEREKIR:** Bu terimler (tüm büyük harfler), [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) içinde açıklandığı şekilde kullanılır; isteğe bağlı davranışın tüm deyimleri,

## <a name="12-references"></a>1,2 başvuru

>   Bağlantılar belgelerin en son sürümüne ait olduğundan, Microsoft açık özellikler belgelerine yönelik başvurular yayımlama yılı içermez. Diğer belgelere yapılan başvurular, kullanılabilir bir yayımlama yılı içerir.

### <a name="121-normative-references"></a>1.2.1 normative başvuruları 

>  [MS-SSTR] Kesintisiz Akış Protocol *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/ıEC 14496-12] Uluslararası Standardizasyon Teşkilatı, "bilgi teknolojisi--ses-görsel nesnelerinin kodlaması--Bölüm 12: ISO temel medya dosyası biçimi ", ISO/ıEC 14496-12:2014, sürüm 4, artı corrigendum 1, 1 & 2 ' yi düzeltme.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/ıEC 14496-15] Uluslararası Standardizasyon Teşkilatı, "bilgi teknolojisi--ses-görsel nesnelerinin kodlaması--15. Bölüm: ", ISO 14496-15:2015, sürüm 3" ISO tabanlı medya dosyası biçimindeki NAL birimi yapılandırılmış videonun sonu.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/ıEC 23008-2] Bilgi teknolojisi--heterojen ortamlarda yüksek verimlilik kodlaması ve medya teslimi--2. Bölüm: Yüksek verimlilik video kodlama: 2013 veya en yeni sürüm<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/ıEC 23001-7] Bilgi teknolojisi — MPEG Sistemleri Teknolojileri — Bölüm 7: ISO tabanlı medya dosyası biçim dosyalarında ortak şifreleme, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "" Bucket "medya türleri için ' codec bileşenleri ' ve ' profiles ' parametreleri"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] MP4 kayıt yetkilisi, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "gereksinim düzeylerini belirtmek için RFC 'lerde kullanım için anahtar sözcükler", BCP 14, RFC 2119, 1997 Mart[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 bilgilendirici başvurular 

>   [MS-GLOS] Microsoft Corporation, "*Windows protokolleri ana sözlüğü*."
> 
>   [RFC3548] Josefsson, S., Ed., "Base16, Base32 ve Base64 veri kodlamaları", RFC 3548, 2003 Temmuz[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Şu sözdizimi için Crocker, D., Ed. ve Overell, P., "genişletilmiş BNF: ABNF ", STD 68, RFC 5234, Ocak 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1,3 genel bakış 

>   Yalnızca HEVC 'nin teslimi için gereken Kesintisiz Akış belirtiminde yapılan değişiklikler aşağıda belirtilmiştir. Değiştirilmeyen bölüm üstbilgileri başvurulan Kesintisiz Akış belirtiminde [MS-SSTR] konumu korumak için listelenir.

## <a name="14-relationship-to-other-protocols"></a>1,4 diğer protokollerle ilişki 

## <a name="15-prerequisitespreconditions"></a>1,5 önkoşul/ön koşullar 

## <a name="16-applicability-statement"></a>1,6 uygulanabilirlik ekstresi 

## <a name="17-versioning-and-capability-negotiation"></a>1,7 sürüm oluşturma ve yetenek anlaşması 

## <a name="18-vendor-extensible-fields"></a>1,8 satıcı-Genişletilebilir alanlar 

>   Aşağıdaki yöntem, HEVC video biçimini kullanarak akışları tanımlamak için kullanılır:
> 
>   * **Medya biçimleri için özel açıklayıcı kodlar:** Bu özellik, *2.2.2.5*bölümünde belirtildiği gibi **FourCC** alanı tarafından sağlanır.
>   Uygulayıcılar, uzantı kodlarını MPEG4-RA ile kaydederek ( [ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) içinde belirtilen) uzantıların çakışmadığından emin olabilir

## <a name="19-standards-assignments"></a>1,9 standartları atamaları 

## <a name="2-messages"></a>2 ileti 

## <a name="21-transport"></a>2,1 taşıması

## <a name="22-message-syntax"></a>2,2 ileti sözdizimi 

### <a name="221-manifest-request"></a>2.2.1 manifest Isteği 

### <a name="222-manifest-response"></a>2.2.2 bildirim yanıtı 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 bir düzgün şekilde streamingmedia 

>   **MinorVersion (değişken):** Bildirim yanıtı iletisinin ikincil sürümü. 2 olarak ayarlanmalıdır. (Değişiklik yok)
> 
>   **Zaman ölçeği (değişken):** Duration özniteliğinin zaman ölçeği, bir saniyenin artıklarının sayısı olarak belirtilir. Varsayılan değer
> 1. (Değişiklik yok)
> 
>    Önerilen değer, video çerçevelerinin tam süresini ve kesirli kare hızına sahip parçaları (örneğin, 30/1.001 Hz) temsil eden 90000 ' dir.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

Video veya ses akışlarına Common Encryption (CENC) uygulandığında, ProtectionElement mevcut olacaktır. HEVC şifreli akışları Common Encryption 2. sürüm [ISO/ıEC 23001-7] ile uyumlu OLACAKTıR. Yalnızca VCL NAL birimlerindeki dilim verileri şifrelenir.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **Streamzaman ölçeği (değişken):** Bu akıştaki süre ve saat değerleri için bir saniye cinsinden artan sayı olarak belirtilen zaman ölçeği. HEVC akışları için 90000 değeri önerilir. Ses akışları için Waveform örnek sıklığıyla eşleşen bir değer (örneğin, 48000 veya 44100) önerilir.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 Urlmodel 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (değişken):** Her örnek için hangi medya biçiminin kullanıldığını belirleyen dört karakterli bir kod. Aşağıdaki değer aralığı aşağıdaki anlam anlamları ile ayrılmıştır:
> 
> * "hev1": Bu izleme için video örnekleri, [ISO/ıEC-14496-15] içinde belirtilen ' hev1 ' örnek açıklama biçimini kullanarak HEVC videosunu kullanır.
>
> * "hvc1": Bu izleme için video örnekleri, [ISO/ıEC-14496-15] içinde belirtilen ' hvc1 ' örnek açıklama biçimini kullanarak HEVC videosunu kullanır.
> 
>   **CodecPrivateData (değişken):** Medya biçimine özgü parametreleri ve izlemedeki tüm örneklerde ortak olan ve onaltılık kodlanmış bayt dizesi olarak temsil edilen veriler. Bayt dizisinin biçimi ve anlam anlamı, **FourCC** alanının değeri ile aşağıdaki gibi değişir:
> 
>   * Bir TrackElement HEVC videosunu açıkladığı zaman, **FourCC** alanı **"hev1"** veya **"hvc1"** değerine eşit olacaktır
> 
>   **CodecPrivateData** alanı, aşağıdaki bayt dizisinin onaltılık kodlu bir dize gösterimini içerir. Bu, abnf [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (MS-SSTR üzerinde değişiklik yok) içinde belirtilir
> 
>   * % x00% x00% x00% x01 SPSField% x00% x00% x00% x01 PPSField
> 
>   * SPSField, dizi parametre kümesini (SPS) içerir.
> 
>   * PPSField, dilim parametre kümesini (PPS) içerir.
> 
>   Not: Video parametresi kümesi (VPS) CodecPrivateData içinde yer alır, ancak ' hvcC ' kutusundaki depolanan dosyaların dosya üstbilgisinde yer almalıdır. Kesintisiz Akış Protokolü kullanan sistemler, "codec bileşenleri" özel özniteliğini kullanarak ek kod çözme parametrelerine (örneğin, HEVC katmanı) sinyal vermelidir.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **Düzgün şekilde Streamingmedyanın MajorVersion** alanı 2 olarak ayarlanmalıdır ve **MinorVersion** alanı 2 olarak ayarlanmalıdır. (Değişiklik yok)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Fragment Isteği 

>   **Not**: **MinorVersion** 2 ve ' hev1 ' veya ' hvc1 ' için istenen varsayılan medya biçimi, [ISO/IEC 14496-12] ISO taban medya dosyası biçimi dördüncü sürüm ve [ISO/IEC 23001-7] Common Encryption Second Edition içinde belirtilen ' iso8 ' marka ISO taban medya dosyası biçimidir.

### <a name="224-fragment-response"></a>2.2.4 Fragment yanıtı 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **Tfxdbox** kullanım dışıdır ve işlevi, [ISO/IEC 14496-12] bölümünde belirtilen parça kod çözme zaman kutusu (' tfdt ') ile değiştirilmiştir.
> 
>   **Not**: İstemci, çalışmayı Izle kutusunda (' Trun ') listelenen örnek süreleri toplayarak veya örneklerin sayısını varsayılan örnek süre ile çarparak bir parçanın süresini hesaplayabilir. ' Tfdt ' ve parça süresi içinde baseMediaDecodeTime, sonraki parçanın URL zamanı parametresine eşit.
> 
>   Bir üretici başvurusu zaman kutusu (' prft '), film parçası kutusu tarafından başvurulan ilk örnek için, [ISO/ıEC 14496 ' de belirtildiği gibi, film parçası kod çözme zamanına karşılık gelen UTC saatini belirtmek için gereken bir film parçası kutusu (' Moof ') öncesinde eklenmelidir -12] Bölüm 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **Tfrfbox** kullanım dışıdır ve işlevi, [ISO/IEC 14496-12] bölümünde belirtilen parça kod çözme zaman kutusu (' tfdt ') ile değiştirilmiştir.
> 
>   **Not**: İstemci, çalışmayı Izle kutusunda (' Trun ') listelenen örnek süreleri toplayarak veya örneklerin sayısını varsayılan örnek süre ile çarparak bir parçanın süresini hesaplayabilir. ' Tfdt ' ve parça süresi içinde baseMediaDecodeTime, sonraki parçanın URL zamanı parametresine eşit. Canlı akışı geciktiğinden, ileri git adresleri kullanım dışı bırakılmıştır.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **Tfhdbox** ve ilgili alanlar, parçada örnek meta veriler için varsayılan değerleri kapsülleyin. **Tfhdbox** alanının sözdizimi, [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) Bölüm 8.8.7 ' de tanımlanan parça parça üst bilgi kutusunun sözdiziminin katı bir alt kümesidir.
> 
>   **Basedatakayması (8 bayt):** **Mdatbox** alanının başından Itibaren **mdatbox** alanındaki örnek alana kadar bayt cinsinden fark. Bu kısıtlamayı bildirmek için, varsayılan-Base-Moof bayrağının (0x020000) ayarlanması gerekir.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** ve ilgili alanlar, istenen parça için örnek meta veriler başına kapsülleyin. **TrunBox** sözdizimi, [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* Bölüm 8.8.8 ' de tanımlanan sürüm 1 parça çalışma kutusunun katı bir alt kümesidir.
> 
>   **Samplekompozisyontiontimekayması (4 bayt):** Örnekteki ilk sunulan örneğin sunum zamanının, ilk çözülen örnekteki kod çözme zamanına eşit olması için, her bir örneğin örnek oluşturma süresi fark değeri ayarlandı. Negatif video örnek oluşturma uzaklıkları kullanılacaktır,
> 
>   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) içinde tanımlandığı gibi.
> 
>   Not: Bu, video eşleştirme sesinin, en büyük kodu çözülen resim arabelleği kaldırma gecikmesine eşit olmasından kaynaklanan ve farklı kaldırma gecikmeleri olabilecek alternatif parçalar arasında sunum zamanlamasını koruyan bir video eşitleme hatasını önler.
> 
>   Bu bölümde tanımlanan, ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) içinde belirtilen alanların sözdizimi, aşağıdakiler dışında, aynı kalır:
> 
>   Samplekompozisyontiontimekayması = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Fragment yanıt ortak alanları 

### <a name="225-sparse-stream-pointer"></a>2.2.5 seyrek akış Işaretçisi 

### <a name="226-fragment-not-yet-available"></a>2.2.6 parçası henüz kullanılabilir değil 

### <a name="227-live-ingest"></a>2.2.7 canlı alma 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   Dosya türü **(değişken):** MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787) dosyasının ve üst düzey özniteliklerin kullanım amacını ve amaçlanan kullanımını belirtir.
> 
>   **Majormarka (değişken):** Medya dosyasının ana markası. "İsml" olarak ayarlanmalıdır.
> 
>   **MinorVersion (değişken):** Medya dosyasının ikincil sürümü. 1 olarak ayarlanmalıdır.
> 
>   **Compatiblemarkalar (değişken):** Desteklenen MPEG-4 markalarını belirtir.
>   "Ccff" ve "iso8" içermelidir.
> 
>   Bu bölümde tanımlanan, ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) içinde belirtilen alanların sözdizimi aşağıdaki gibidir:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Not**: ' Ccff ' ve ' iso8 ' uyumluluk markalarına parçaların "ortak kapsayıcı dosya biçimi" ve Common Encryption [ISO/ıEC 23001-7] ve ISO temel medya dosyası biçimi sürüm 4 [ISO/ıEC 14496-12] ile uyumlu olduğunu gösterir.

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 Streamsmıl 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 parçası 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 izleme parçası genişletilmiş üst bilgi 

### <a name="228-server-to-server-ingest"></a>2.2.8 sunucudan sunucuya alma 

## <a name="3-protocol-details"></a>3 protokol ayrıntıları 


## <a name="31-client-details"></a>3,1 istemci ayrıntıları 

### <a name="311-abstract-data-model"></a>3.1.1 soyut veri modeli 

#### <a name="3111-presentation-description"></a>3.1.1.1 sunum açıklaması 

>   Sunum açıklaması veri öğesi sununun tüm meta verilerini saklar.
> 
>   Sunum meta verileri: Sunudaki tüm akışlar için ortak olan meta veri kümesi. Sunum meta verileri, bölüm *2.2.2.1*içinde belirtilen aşağıdaki alanları içerir:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **Zaman**
> * **Süresi**
> * **Ilive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   HEVC akışlarını içeren sunular şu şekilde ayarlanır:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Note: Kullanım dışı kutular)
> 
>   Sunular de ayarlanmalıdır:

    TimeScale = 90000

>   Akış koleksiyonu: *3.1.1.1.2*bölümünde belirtildiği gibi Stream Description veri öğeleri koleksiyonu.
> 
>   Koruma açıklaması: Bölüm *3.1.1.1.1*' de belirtildiği gibi, koruma sistemi meta verileri açıklama verisi öğeleri koleksiyonu.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 koruma sistemi meta verisi açıklaması 

>   Koruma sistemi meta verileri açıklama verisi öğesi, tek bir Content Protection sistemine özgü meta verileri saklar. (Değişiklik yok)
> 
>   Koruma üst bilgisi açıklaması: Tek bir Content Protection sistemiyle ilgili içerik koruma meta verileri. Koruma üst bilgisi açıklaması, bölüm *2.2.2.2*içinde belirtilen aşağıdaki alanları içerir:
> 
>   * **SystemId**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Stream açıklaması 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 iz açıklaması 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 özel öznitelik açıklaması 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Fragment başvurusu açıklaması 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Track 'e özgü parça başvurusu açıklaması 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragment açıklaması 

##### <a name="31121-sample-description"></a>3.1.1.2.1 örnek açıklaması 

### <a name="312-timers"></a>3.1.2 zamanlayıcılar 

### <a name="313-initialization"></a>3.1.3 başlatma 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 üst düzey tetiklenen olayları 

#### <a name="3141-open-presentation"></a>3.1.4.1 açık sunu 

#### <a name="3142-get-fragment"></a>3.1.4.2 Al parçası 

#### <a name="3143-close-presentation"></a>3.1.4.3 sunuyu kapat 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 işleme olayları ve sıralama kuralları 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest Isteği ve bildirim yanıtı 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragment Isteği ve parça yanıtı

## <a name="32-server-details"></a>3,2 sunucusu ayrıntıları

## <a name="33-live-encoder-details"></a>3,3 Live Encoder ayrıntıları 

## <a name="4-protocol-examples"></a>4 protokol örnekleri 

## <a name="5-security"></a>5 güvenlik 

## <a name="51-security-considerations-for-implementers"></a>Implementers 5,1 güvenlik konuları

>   Bu protokol kullanılarak taşınan içeriğin yüksek ticari değeri varsa, içeriğin yetkisiz kullanımını engellemek için bir Content Protection sistemi kullanılmalıdır. **Protectionelement** , bir Content Protection sisteminin kullanımıyla ilgili meta verileri yürütmek için kullanılabilir. Korumalı ses ve video içeriği, MPEG Common Encryption Second Edition tarafından belirtilen şekilde şifrelenir: 2015 [ISO/IEC 23001-7].
> 
>   **Not**: HEVC videosu için, yalnızca VCL NALs içindeki dilim verileri şifrelenir. Dilimmadan önce, dilim üst bilgileri ve diğer NALs, sunum uygulamaları tarafından erişilebilir. güvenli bir video yolunda, şifrelenmiş bilgiler sunum uygulamaları için kullanılamaz.

## <a name="52-index-of-security-parameters"></a>5,2 güvenlik parametrelerinin dizini 


| **Güvenlik parametresi**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption kutuları | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 Common Encryption kutuları

Common Encryption uygulandığında ve [ISO/ıEC 23001-7] veya [ISO/ıEC 14496-12] içinde belirtildiğinde, parça yanıtlarında aşağıdaki kutular bulunabilir:

1.  Koruma sistemine özgü üst bilgi kutusu (' pssh ')

2.  Örnek şifreleme kutusu (' senc ')

3.  Örnek yardımcı bilgi fark kutusu (' saıo ')

4.  Örnek yardımcı bilgileri Boyut kutusu (' Saiz ')

5.  Örnek grup açıklama kutusu (' sgpd ')

6.  Gruba örnek kutusu (' sbgp ')

---

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
