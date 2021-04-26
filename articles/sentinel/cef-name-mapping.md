---
title: Ortak olay biçimi (CEF) anahtarı ve CommonSecurityLog alan eşleme
description: Bu makale, CEF anahtarlarını Azure Sentinel 'teki CommonSecurityLog içindeki ilgili alan adlarına eşler.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311660"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF ve CommonSecurityLog alan eşleme

Aşağıdaki tablolar ortak olay biçimi (CEF) alanı adlarını Azure Sentinel 'in CommonSecurityLog içinde kullandıkları adlara eşler ve Azure Sentinel 'de bir CEF veri kaynağıyla çalışırken yararlı olabilir.

Daha fazla bilgi için bkz. [ortak olay biçimini kullanarak dış çözümünüzü bağlama](connect-common-event-format.md).

## <a name="a---c"></a>A - C

|CEF anahtar adı  |CommonSecurityLog alan adı  |Açıklama  |
|---------|---------|---------|
| çalışanlarının    |    <a name="deviceaction"></a> DeviceAction     |  Olayda bahsedilen eylem.       |
|   Uygulama  |    ApplicationProtocol     |  Uygulamada kullanılan protokol (HTTP, HTTPS, SSHv2, Telnet, POP, ıMPA, ıMAPS vb.).   |
| •    |    EventCount     |  Aynı olayın kaç kez gözlemlendiği gösteren olayla ilişkili bir sayı.       |
| | | |

## <a name="d"></a>D

|CEF anahtar adı  |CommonSecurityLog adı  |Açıklama  |
|---------|---------|---------|
|Cihaz satıcısı     |  Devicevençi       | Cihaz ürün ve sürüm tanımlarıyla birlikte, gönderen cihazın türünü benzersiz şekilde tanımlayan dize.       |
|Cihaz ürünü     |   DeviceProduct      |   Cihaz satıcısı ve sürüm tanımlarıyla birlikte, gönderen cihazın türünü benzersiz şekilde tanımlayan dize.        |
|Cihaz sürümü     |   DeviceVersion      |      Cihaz ürünü ve satıcı tanımlarına sahip olan dize, gönderme cihazının türünü benzersiz şekilde tanımlar.     |
| Hedef DNSDomain    | Hedef DNSDomain        |   Tam etki alanı adının (FQDN) DNS bölümü.      |
| destinationServiceName | DestinationServiceName | Olayın hedeflediği hizmet. Örneğin, `sshd`.|
| Destinationtranslatedadddress | Destinationtranslatedadddress | Bir IP ağındaki olay tarafından başvurulan, IPv4 IP adresi olarak çevrilmiş hedefi tanımlar. |
| destinationTranslatedPort | DestinationTranslatedPort | Bir güvenlik duvarı gibi çevirinin ardından bağlantı noktası. <br>Geçerli bağlantı noktası numaraları: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Gözlemlenen iletişimin alındığı yönle ilgili herhangi bir bilgi. Geçerli değerler: <br>- `0` = Gelen <br>- `1` = Giden |
| deviceDnsDomain | DeviceDnsDomain | Tam nitelikli etki alanı adının (FQDN) DNS etki alanı bölümü |
|DeviceEventClassID     |   DeviceEventClassID     |   Olay türü başına benzersiz bir tanımlayıcı görevi gören dize veya tamsayı.      |
| Deviceexternalıd | Deviceexternalıd | Olayı oluşturan cihazı benzersiz şekilde tanımlayan bir ad. |
| Devicetesis | Devicetesis | Olayı oluşturan tesis.|
| Deviceınboundınterface | Deviceınboundınterface |Paketin veya verilerin cihaza girdiği arabirim.  |
| deviceNtDomain | DeviceNtDomain | Cihaz adresinin Windows etki alanı |
| Deviceoutboundınterface | Deviceoutboundınterface |Paketin veya verilerin cihazı bıraktı arabirim. |
| Devicepayloadıd |Devicepayloadıd |Olayla ilişkili yük için benzersiz tanımlayıcı. |
| deviceProcessName | ProcessName | Olayla ilişkili işlem adı. <br><br>Örneğin, UNIX 'de Syslog girdisini oluşturan işlem. |
| Devicetranslatedadddress | Devicetranslatedadddress | Bir IP ağında, olayın başvurduğu çevrilmiş cihaz adresini belirler. <br><br>Biçim bir IPv4 adresidir. |
| dhost |Hedef konak adı | Olayın bir IP ağında başvurduğu hedef.  <br>Biçim, bir düğüm kullanılabilir olduğunda hedef düğümle ilişkili bir FQDN olmalıdır. Örneğin `host.domain.com` veya `host` olabilir. |
| DMAC | DestinationMacAddress | Hedef MAC adresi (FQDN) |
| dntdom | Hedef Ntdomain | Hedef adresin Windows etki alanı adı.|
| DPID | Destinationprocessıd |Olayla ilişkili hedef işlemin KIMLIĞI.|
| dprıv | DestinationUserPrivileges | Hedef kullanım ayrıcalıklarını tanımlar. <br>Geçerli değerler: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Olay hedef işleminin adı, örneğin `telnetd` veya `sshd.` |
| DPT | DestinationPort | Hedef bağlantı noktası. <br>Geçerli değerler: `*0` - `65535` |
| HD | Hedef IP | Olayın bir IP ağında başvurduğu hedef IPv4 adresi. |
| DTZ | DeviceTimeZon | Olayı üreten cihazın saat dilimi |
| DUID |Destinationuserıd | Hedef kullanıcıyı KIMLIĞE göre tanımlar. |
| duser | Hedef Kullanıcı adı |Hedef kullanıcıyı ada göre tanımlar.|
| DVC | DeviceAddress | Olayı üreten cihazın IPv4 adresi. |
| dvchost | DeviceName | Bir düğüm kullanılabilir olduğunda cihaz düğümüyle ilişkili FQDN. Örneğin `host.domain.com` veya `host` olabilir.| 
| dvcmac | DeviceMacAddress | Olayı üreten cihazın MAC adresi. |
| dvcpıd | İşlem Kimliği | Olayı üreten cihazdaki işlemin KIMLIĞINI tanımlar. |

## <a name="e---i"></a>E-ı

|CEF anahtar adı  |CommonSecurityLog adı  |Açıklama  |
|---------|---------|---------|
|end     |  EndTime       | Olayla ilgili etkinliğin sona erdiği zaman.        |
|externalId    |   ExternalID      | Kaynak cihaz tarafından kullanılan bir KIMLIK. Genellikle, bu değerler bir olayla ilişkili değerleri artırır.        |
|fileCreateTime     |  FileCreateTime      | Dosyanın oluşturulduğu zaman.        |
|Dosya karması     |   Dosya karması      |   Bir dosyanın karması.      |
|File     |   File      |INode gibi bir dosyayla ilişkili bir KIMLIK.         |
| Dosya Modificationtime | Dosya Modificationtime |Dosyanın son değiştirilme zamanı. |
| Null | Null | Dosya adı da dahil olmak üzere dosyanın tam yolu. Örneğin: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` veya `/usr/bin/zip` .|
| Dosya Izni |Dosya Izni |Dosyanın izinleri. |
| fileType | FileType | Dosya türü (kanal, yuva vb.).|
|fname | Kısaltın| Yol olmadan dosyanın adı. |
| fsize | İ | Dosya boyutu. |
|Ana bilgisayar    |  Bilgisayar       | Ana bilgisayar, syslog 'dan        |
|in     |  ReceivedBytes      |Gelen aktarılan bayt sayısı.         |
| | | |

## <a name="m---p"></a>M-P

|CEF anahtar adı  |CommonSecurityLog adı  |Açıklama  |
|---------|---------|---------|
|msg   |  İleti       | Olayla ilgili daha fazla ayrıntı sağlayan bir ileti.        |
|Name     |  Etkinlik       |   Olayın okunabilir ve anlaşılır bir açıklamasını temsil eden bir dize.     |
|oldFileCreateTime     |  OldFileCreateTime       | Eski dosyanın oluşturulduğu zaman.        |
|oldFileHash     |   OldFileHash      |   Eski dosyanın karması.      |
|Oldfileıd     |   Oldfileıd     |   Ve eski dosyayla ilişkili inode gibi KIMLIK.      |
| oldFileModificationTime | OldFileModificationTime |Eski dosyanın son değiştirilme zamanı. |
| eskiDosya adı |  EskiDosya adı |Eski dosyanın adı. |
| oldFilePath | OldFilePath | Dosya adı da dahil olmak üzere eski dosyanın tam yolu. <br>Örneğin `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` veya `/usr/bin/zip` olabilir.|
| oldFilePermission | OldFilePermission |Eski dosyanın izinleri. |
|oldFileSize | OldFileSize | Eski dosyanın boyutu.|
| oldFileType | OldFileType | Eski dosyanın kanal, yuva vb. gibi dosya türü.|
| out | SentBytes | Giden aktarılan bayt sayısı. |
| Sonuç | Sonuç | Etkinliğin sonucu, örneğin `success` veya `failure` .|
|proto    |  Protokol       | Kullanılan katman-4 protokolünü tanımlayan Aktarım Protokolü. <br><br>Olası değerler, veya gibi protokol adlarını içerir `TCP` `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|CEF anahtar adı  |CommonSecurityLog adı  |Açıklama  |
|---------|---------|---------|
|Nedeni     |  Nedeni      |Bir denetim olayının oluşturulma nedeni. <br><br>Örneğin `Bad password` veya `Unknown user` olabilir.         |
|İstek     |   RequestURL      | Protokol dahil olmak üzere bir HTTP isteği için erişilen URL. Örneğin, `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   İstekle ilişkili Kullanıcı Aracısı.      |
| Öğesinden | Öğesinden | HTTP başvuran gibi, isteğin kaynaklandığı içeriği açıklar. |
| requestCookies | RequestCookies |İstekle ilişkili tanımlama bilgileri. |
| requestMethod | RequestMethod | Bir URL 'ye erişmek için kullanılan yöntem. <br><br>Geçerli değerler,, vb. gibi yöntemleri içerir `POST` `GET` . |
| ş | Dönem tSaati | Etkinlikle ilgili olayın alındığı zaman. |
|Önem derecesi     |  <a name="logseverity"></a> Logönem derecesi       |  Olayın önemini açıklayan bir dize veya tamsayı.<br><br> Geçerli dize değerleri: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Geçerli tamsayı değerleri şunlardır:<br> - `0`-`3` = Düşük <br>- `4`-`6` = Orta<br>- `7`-`8` = Yüksek<br>- `9`-`10` = Very-High |
| Shost    | SourceHostName        |Olayın başvurduğu kaynağı bir IP ağında tanımlar. Biçim, bir düğüm kullanılabilir olduğunda kaynak düğümle ilişkili tam etki alanı adı (DQDN) olmalıdır. Örneğin `host` veya `host.domain.com` olabilir. |
| SMAC | SourceMacAddress | Kaynak MAC adresi. |
| sntdom | SourceNTDomain | Kaynak adresi için Windows etki alanı adı. |
| sourceDnsDomain | SourceDnsDomain | Tam FQDN 'nin DNS etki alanı kısmı. |
| sourceServiceName | SourceServiceName | Olayı oluşturmadan sorumlu hizmet. |
| Sourcetranslatedaddresi | Sourcetranslatedaddresi | Bir IP ağında, olayın başvurduğu çevrilmiş kaynağı tanımlar. |
| sourceTranslatedPort | SourceTranslatedPort | Bir güvenlik duvarı gibi çeviri sonrasında kaynak bağlantı noktası. <br>Geçerli bağlantı noktası numaraları şunlardır `0`  -  `65535` . |
| tamamladığını | SourceProcessId | Olayla ilişkili kaynak işlemin KIMLIĞI.|
| spriv | SourceUserPrivileges | Kaynak kullanıcının ayrıcalıkları. <br><br>Geçerli değerler şunlardır: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | Olayın kaynak işleminin adı.|
| SPT | SourcePort | Kaynak bağlantı noktası numarası. <br>Geçerli bağlantı noktası numaraları şunlardır `0`  -  `65535` . |
| src | SourceIP |Bir olayın bir IP ağında başvurduğu kaynak, bir IPv4 adresi olarak. |
| start | StartTime | Olayın başvurduğu etkinliğin başladığı zaman. |
| SUID | Sourceuserıd | Kaynak kullanıcıyı KIMLIĞE göre tanımlar. |
| suser | Dosyasından | Kaynak kullanıcıyı ada göre tanımlar. |
| tür | Olay türü | Olay türü. Değer değerleri şunlardır: <br>- `0`: temel olay <br>- `1`: toplanmış <br>- `2`: bağıntı olayı <br>- `3`: eylem olayı <br><br>**Note**: Bu olay, temel olaylar için atlanabilir. |
| | | |

## <a name="custom-fields"></a>Özel alanlar

Aşağıdaki tablolar, müşteriler tarafından yerleşik alanların hiçbirine uygulanmayan veriler için kullanılabilecek CEF anahtarlarının ve CommonSecurityLog alanlarının adlarını eşler.

### <a name="custom-ipv6-address-fields"></a>Özel IPv6 adres alanları

Aşağıdaki tabloda, özel veriler için kullanılabilen *IPv6* adres alanları için CEF anahtarı ve CommonSecurityLog adları eşlenir.

|CEF anahtar adı  |CommonSecurityLog adı  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Özel sayı alanları

Aşağıdaki tabloda, özel veriler için kullanılabilen *sayı* alanları için CEF anahtarı ve CommonSecurityLog adları eşlenir.

|CEF anahtar adı  |CommonSecurityLog adı  |
|---------|---------|
|     CN1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Özel dize alanları

Aşağıdaki tabloda, özel veriler için kullanılabilen *dize* alanları için CEF anahtarı ve CommonSecurityLog adları eşlenir.

|CEF anahtar adı  |CommonSecurityLog adı  |
|---------|---------|
|     CS1    |     DeviceCustomString1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     cs2    |     DeviceCustomString2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     CS3    |     DeviceCustomString3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     cs4'te    |     DeviceCustomString4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup> [1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup> [1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> **Devicecustomstring** alanlarını gelişigüzel bir şekilde kullanmanızı ve mümkün olduğunda daha belirgin, yerleşik alanları kullanmanızı öneririz.
> 

### <a name="custom-timestamp-fields"></a>Özel zaman damgası alanları

Aşağıdaki tabloda, özel veriler için kullanılabilir *zaman damgası* alanları için CEF anahtarı ve CommonSecurityLog adları eşlenir.

|CEF anahtar adı  |CommonSecurityLog adı  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Özel tamsayı veri alanları

Aşağıdaki tabloda, özel veriler için kullanılabilen *tamsayı* alanlarına yönelik CEF anahtarı ve CommonSecurityLog adları eşlenir.

|CEF anahtar adı  |CommonSecurityLog adı  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Zenginleştirme alanları

Aşağıdaki **Commonsecuritylog** alanları, kaynak cihazlardan alınan özgün olayları zenginleştirmek Için Azure Sentinel tarafından eklenir ve CEF anahtarlarında eşlemeler yoktur:

### <a name="threat-intelligence-fields"></a>Tehdit bilgileri alanları

|CommonSecurityLog alan adı  |Açıklama  |
|---------|---------|
|   **Indicatorthreattype**  |  Tehdit bilgileri akışına göre [MaliciousIP](#MaliciousIP) tehdit türü.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | İletideki geçerli tehdit bilgileri akışından ilişkili olan tüm IP adreslerini listeler. |
|  **MaliciousIPCountry**   | [MaliciousIP](#MaliciousIP) ülkesi, kayıt alımı sırasında coğrafi bilgilere göre.        |
| **MaliciousIPLatitude**    |   Kayıt alma sırasında coğrafi bilgilere göre [MaliciousIP](#MaliciousIP) boylam.      |
| **MaliciousIPLongitude**    |  Kayıt alma sırasında coğrafi bilgilere göre [MaliciousIP](#MaliciousIP) boylam.       |
| **ReportReferenceLink**    |    Tehdit bilgileri raporuna bağlantı.     |
|  **Threatgüvenirlik**   |   Tehdit bilgileri akışına göre [MaliciousIP](#MaliciousIP) tehdit güveni.      |
| **ThreatDescription**    |   Tehdit bilgileri akışına göre [MaliciousIP](#MaliciousIP) tehdit açıklaması.      |
| **Threatönem derecesi** | Kayıt alma sırasında tehdit bilgileri akışına göre [MaliciousIP](#MaliciousIP)için tehdit önem derecesi. |
|     |         |

### <a name="additional-enrichment-fields"></a>Ek zenginleştirme alanları

|CommonSecurityLog alan adı  |Açıklama  |
|---------|---------|
|**Originallogönem derecesi**     |  Her zaman boş, Cıcoasa tümleştirmesi için desteklenir. <br>Günlük önem derecesi değerleri hakkında daha fazla bilgi için bkz. [Logönem derecesi](#logseverity) alanı.       |
|**RemoteIP**     |     Uzak IP adresi. <br>Bu değer, mümkünse, [Communicationdirection](#communicationdirection) alanını temel alır.     |
|**RemotePort**     |   Uzak bağlantı noktası. <br>Bu değer, mümkünse, [Communicationdirection](#communicationdirection) alanını temel alır.      |
|**SimplifiedDeviceAction**     |   [Devaction değerini bir](#deviceaction) statik değer kümesine basitleştirir, ancak özgün değeri [deviceaction](#deviceaction) alanında tutarken. <br>Örneğin: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Her zaman **Opsmanager** olarak tanımlanır.        |
|     |         |

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [ortak olay biçimini kullanarak dış çözümünüzü bağlama](connect-common-event-format.md).
