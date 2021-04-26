---
title: Azure Sentinel veri normalleştirme Şeması Başvurusu | Microsoft Docs
description: Bu makalede, Azure Sentinel veri normalleştirme şeması görüntülenir.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806944"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Azure Sentinel veri normalleştirme Şeması Başvurusu

## <a name="terminology"></a>Terminoloji

Aşağıdaki terminoloji Azure Sentinel şemaları 'nda kullanılır:

| Süre | Tanım |
| ---- | ---------- |
| Raporlama cihazı | Kayıtları Azure Sentinel 'e gönderen sistem. Kaydın Konu sistemi olmayabilir. |
| Kayıt | Raporlama aygıtından gönderilen veri birimi. Bu, genellikle "günlük", "olay" veya "uyarı" olarak adlandırılır, ancak bunlardan biri olması gerekmez. |
|

## <a name="data-types-and-formats"></a>Veri türleri ve biçimleri

Değerler aşağıdaki yönergelere göre normalleştirilmelidir. Bu, normalleştirilmiş alanlar için zorunludur ve diğer alanlar için önerilir. 

| Veri türü | Fiziksel tür | Biçim ve değer |
| --------- | ------------- | ---------------- |
| **Tarih/saat** | Alma yöntemi özelliğinin azalan öncelik olarak kullanımına bağlı olarak:<ul><li>Log Analytics yerleşik tarih saat türü</li><li>Log Analytics DateTime sayısal gösterimini kullanan bir tamsayı alanı</li><li>Log Analytics DateTime sayısal gösterimini kullanan bir dize alanı</li></ul> | DateTime gösterimini Log Analytics. <br></br>Log Analytics Tarih & zaman gösterimi doğası, ancak UNIX zaman gösteriminden farklı. Bu dönüştürme yönergelerine bakın. <br></br>Tarih & saat dilimi ayarlanmış olmalıdır. |
| **MAC adresi** | Dize | Colon-Hexadecimal gösterimi |
| **IP Adresi** | IP Adresi | Şemada ayrı IPv4 ve IPv6 adresleri yok. Herhangi bir IP adresi alanı, bir IPv4 adresi veya IPv6 adresi içerebilir:<ul><li>Bir nokta ondalık gösteriminde IPv4</li><li>8 onaltılı Tets gösteriminde IPv6, burada açıklanan kısa formlara izin verir.</li></ul> |
| **Kullanıcı** | Dize | Aşağıdaki 3 Kullanıcı alanı kullanılabilir:<ul><li>Kullanıcı adı</li><li>Kullanıcı UPN 'si</li><li>Kullanıcı etki alanı</li></ul> |
| **Kullanıcı Kimliği** | Dize | Şu 2 Kullanıcı kimliği şu anda destekleniyor:<ul><li>Kullanıcı SID 'SI</li><li>Azure Active Directory KIMLIĞI</li></ul> |
| **Cihaz** | Dize | Aşağıdaki 3 cihaz/konak sütunları desteklenir:<ul><li>ID</li><li>Name</li><li>Tam etki alanı adı (FQDN)</li></ul> |
| **Ülke** | Dize | Bu önceliğe göre ISO 3166-1 kullanan bir dize:<ul><li>Alfa-2 kodları (örneğin, Birleşik Devletler için ABD)</li><li>Alfa-3 kodları (örneğin, Birleşik Devletler için ABD)</li><li>Kısa ad</li></ul> |
| **Bölge** | Dize | ISO 3166-2 kullanan ülke alt bölüm adı |
| **Şehir** | Dize | |
| **MIN** | Çift | ISO 6709 koordinat temsili (işaretli ondalık) |
| **En** | Çift | ISO 6709 koordinat temsili (işaretli ondalık) |
| **Karma algoritması** | Dize | Aşağıdaki 4 karma sütunları desteklenir:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Dosya türü** | Dize | Dosya türü türü:<ul><li>Dahili numara</li><li>Sınıf</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Ağ oturumları tablosu şeması

Ağ oturumları tablosunun, sürümlenmiş 1.0.0 şeması aşağıda verilmiştir

| Alan adı | Değer türü | Örnek | Description | İlişkili OSSEM varlıkları |
|-|-|-|-|-|
| Olay türü | Dize | Trafik | Toplanmakta olan olay türü | Olay |
| EventSubType | Dize | Kimlik Doğrulaması | Varsa tür ek açıklaması | Olay |
| EventCount | Tamsayı  | 10 | Uygulanabiliyorsa, toplanan olay sayısı. | Olay |
| EventEndTime | Tarih/Saat | Bkz. "veri türleri" | Etkinliğin bitiş zamanı | Olay |
| EventMessage | string |  erişim reddedildi | Genel bir ileti veya açıklama, ' da bulunan veya kayıttan oluşturulan | Olay |
| Dvşifredresi | IP Adresi |  23.21.23.34 | Kaydı üreten cihazın IP adresi | Aygıtların<br>IP |
| DvcMacAddr | Dize | 06:10:9F: EB: 8F: 14 | Olayın gönderildiği raporlama cihazının ağ arabiriminin MAC adresi. | Aygıtların<br>Mac |
| DvcHostname | Cihaz adı (dize) | syslogserver1.contoso.com | İletiyi üreten cihazın cihaz adı. | Cihaz |
| EventProduct | Dize | OfficeSharepoint | Olayı üreten ürün. | Olay |
| EventProductVersion | string | 9.0 |  Olayı üreten ürünün sürümü. | Olay |
| Eventresourceıd | Cihaz KIMLIĞI (dize) | /Subscriptions/3c1bb38c-82e3-4f8d-A115-a7110ba70d05/ResourceGroups/contoso77/Providers/Microsoft.COMPUTE/virtualmachines/syslogserver1 | İletiyi üreten cihazın kaynak KIMLIĞI. | Olay |
| EventReportUrl | Dize | https://192.168.1.1/repoerts/ae3-56.htm | Raporlama cihazı tarafından oluşturulan tam raporun bağlantısı | Olay |
| EventVendor | Dize | Microsoft | Olayı üreten ürünün satıcısı. | Olay |
| EventResult | Çoklu değer: başarılı, kısmi, hata, [boş] (dize) | Başarılı | Etkinlik için bildirilen sonuç. Geçerli olmadığında boş değer. | Olay |
| EventResultDetails | Dize | Yanlış parola | EventResult 'da bildirilen sonucun nedeni veya ayrıntıları | Olay |
| EventSchemaVersion | Gerçek | 0.1 | Azure Sentinel şema sürümü. Şu anda 0,1. | Olay |
| Eventönem derecesi | Dize | Düşük | Bildirilen etkinliğin bir güvenlik etkisi varsa, etkinin önem derecesini gösterir. | Olay |
| EventOriginalUid | Dize | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | Raporlama cihazından kayıt KIMLIĞI. | Olay |
| Olaybaşlangıçsaati | Tarih/Saat | Bkz. "veri türleri" | Olayın belirtilen zaman | Olay |
| TimeGenerated | Tarih/Saat | Bkz. "veri türleri" | Raporlama kaynağı tarafından raporlanarak olayın gerçekleştiği zaman. | Özel alan |
| Eventtimeingındı | Tarih/Saat | Bkz. "veri türleri" | Olayın Azure Sentinel 'e alındığı zaman. , Azure Sentinel tarafından eklenir. | Olay |
| Eventuıd | GUID (dize) | 516a64e3-8360-4F1E-a67c-d96b3d52df54 | Azure Sentinel tarafından bir satırı işaretlemek için kullanılan benzersiz tanımlayıcı. | Olay |
| NetworkApplicationProtocol | Dize | HTTPS | Bağlantı veya oturum tarafından kullanılan uygulama katmanı protokolü. | Ağ |
| DstBytes | int | 32455 | Bağlantı veya oturum için hedefin kaynağa gönderilen bayt sayısı. | Hedef |
| SrcBytes | int | 46536 | Bağlantı veya oturum için kaynaktan hedefe gönderilen bayt sayısı. | Kaynak |
| NetworkBytes | int | 78991 | Her iki yönde gönderilen bayt sayısı. Hem BytesReceived hem de BytesSent varsa, BytesTotal 'nin toplamına eşit olması gerekir. | Ağ |
| NetworkDirection | Çoklu değer: gelen, giden (dize) | Gelen | Bağlantının veya oturumun kuruluşun içine veya dışına yönü. | Ağ |
| DstGeoCity | Dize | Burpatton | Hedef IP adresiyle ilişkilendirilen şehir | Hedefine<br>Coğrafi Bölge |
| DstGeoCountry | Ülke (dize) | ABD | Kaynak IP adresiyle ilişkilendirilen ülke | Hedefine<br>Coğrafi Bölge |
| DstDvcHostname | Cihaz adı (dize) |  victim_pc | Hedef cihazın cihaz adı | Hedef<br>Cihaz |
| DstDvcFqdn | Dize | victim_pc. contoso. Local | Günlüğün oluşturulduğu konağın tam etki alanı adı | Hedefine<br>Cihaz |
| DstDomainHostname | string | 'NUN | Hedefin etki alanı, hedef konağın etki alanı (Web sitesi, etki alanı adı vb.), Örneğin DNS aramaları veya NS aramaları için | Hedef |
| Dstınterfacename | string | Microsoft Hyper-V ağ bağdaştırıcısı | Hedef cihaz tarafından bağlantı veya oturum için kullanılan ağ arabirimi. | Hedef |
| Dstınterfaceguid | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | Kimlik doğrulama isteği için kullanılan ağ arabiriminin GUID 'SI  | Hedef |
| Dstıpaddr | IP Adresi | 2001: db8:: FF00:42:8329 | Genellikle ağ paketinde hedef IP olarak adlandırılan bağlantının veya oturum hedefinin IP adresi | Hedefine<br>IP |
| Dstdvşifredresi | IP Adresi | 75.22.12.2 | Ağ paketiyle doğrudan ilişkili olmayan bir cihazın hedef IP adresi | Hedefine<br>Aygıtların<br>IP
| Dstmageolaneti | Enlem (Double) | 44,475833 | Hedef IP adresiyle ilişkili coğrafi koordinat enlem | Hedefine<br>Coğrafi Bölge |
| DstMacAddr | Dize | 06:10:9F: EB: 8F: 14 | Bağlantının veya oturumun sonlandırıldığı ağ arabiriminin MAC adresi, genellikle ağ paketindeki hedef MAC 'e denir | Hedefine<br>'INIZDE |
| DstDvcMacAddr | Dize | 06:10:9F: EB: 8F: 14 | Ağ paketiyle doğrudan ilişkili olmayan bir cihazın hedef MAC adresi. | Hedefine<br>Aygıtların<br>'INIZDE |
| DstDvcDomain | Dize | 'NUN | Hedef cihazın etki alanı. | Hedefine<br>Cihaz |
| DstPortNumber | Tamsayı | 443 | Hedef IP bağlantı noktası. | Hedefine<br>Bağlantı noktası |
| DstGeoRegion | Bölge (dize) | Vermont | Hedef IP adresiyle ilişkili bir ülkenin içindeki bölge | Hedefine<br>Coğrafi Bölge |
| Dstresourceıd | Cihaz KIMLIĞI (dize) |  /Subscriptions/3c1bb38c-82e3-4f8d-A115-a7110ba70d05/ResourceGroups/contoso77/Providers/Microsoft.COMPUTE/virtualmachines/kurbanı | Hedef cihazın kaynak kimliği. | Hedef |
| Dstnatıpaddr | IP Adresi | 2::1 | Güvenlik Duvarı gibi bir aracı NAT aygıtı tarafından bildirilirse, NAT aygıtı tarafından kaynakla iletişim için kullanılan IP adresi. | Hedef NAT,<br>IP |
| DstNatPortNumber | int | 443 | Bir güvenlik duvarı gibi bir aracı NAT aygıtı tarafından bildirilirse, NAT aygıtı tarafından kaynakla iletişim için kullanılan bağlantı noktası. | Hedef NAT,<br>Bağlantı noktası |
| DstUserSid | Kullanıcı SID 'SI |  S-12-1445 | Oturumun hedefi ile ilişkili kimliğin Kullanıcı KIMLIĞI. Genellikle, bir sunucunun kimliğini doğrulamak için kullanılan kimlik. Ayrıntılar için bkz. "veri türleri". | Hedefine<br>Kullanıcı |
| Dstuseraadıd | Dize (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | Oturumun hedef ucundaki kullanıcının Azure AD hesabı nesne KIMLIĞI | Hedefine<br>Kullanıcı |
| DstUserName | Kullanıcı adı (dize) | cannd | Oturumun hedefi ile ilişkili kimliğin Kullanıcı adı.  | Hedefine<br>Kullanıcı |
| DstUserUpn | string | johnd@anon.com | Oturumun hedefi ile ilişkili kimliğin UPN 'si. | Hedefine<br>Kullanıcı |
| DstUserDomain | string | Grub | Oturumun hedefindeki hesabın etki alanı veya bilgisayar adı | Hedefine<br>Kullanıcı |
| DstZone | Dize | DMZ | Raporlama cihazı tarafından tanımlanan, hedefin ağ bölgesi. | Hedef |
| DstGeoLongitude | Boylam (Double) | -73,211944 | Hedef IP adresiyle ilişkili coğrafi koordinat Boylam | Hedefine<br>Coğrafi Bölge |
| DvcAction | Çoklu değer: Izin ver, Reddet, bırak (dize) | İzin Ver | Bir güvenlik duvarı gibi bir aracı cihaz tarafından raporlandıysanız, cihaz tarafından gerçekleştirilen eylem. | Cihaz |
| Dvcinboundınterface | Dize | eth0 | Bir güvenlik duvarı gibi bir aracı cihaz tarafından bildirilirse, kaynak cihazla bağlantı için tarafından kullanılan ağ arabirimi. | Cihaz |
| Dvcoutboundınterface | Dize  | Ethernet bağdaştırıcısı Ethernet 4 | Bir güvenlik duvarı gibi bir aracı cihaz tarafından bildirilirse, hedef cihazla bağlantı için tarafından kullanılan ağ arabirimi. | Cihaz |
| NetworkDuration | Tamsayı | 1500 | Ağ oturumunun veya bağlantının tamamlanması için milisaniye cinsinden süre. | Ağ |
| NetworkIcmpCode | Tamsayı | 34 | ICMP iletisi için, ıCMP ileti türü sayısal değeri (RFC 2780 veya RFC 4443). | Ağ |
| NetworkIcmpType | Dize | Hedefe ulaşılamıyor | ICMP iletisi için, ıCMP ileti türü metin gösterimi (RFC 2780 veya RFC 4443). | Ağ |
| DstPackets | int  | 446 | Bağlantı veya oturum için hedefin kaynağına gönderilen paketlerin sayısı. Bir paketin anlamı raporlama cihazı tarafından tanımlanır. | Hedef |
| SrcPackets | int  | 6478 | Bağlantı veya oturum için kaynaktan hedefe gönderilen paket sayısı. Bir paketin anlamı raporlama cihazı tarafından tanımlanır. | Kaynak |
| NetworkPackets | int  | 0 | Her iki yönde gönderilen paket sayısı. Hem Packetsalal hem de PacketsSent varsa, BytesTotal 'nin toplamına eşit olması gerekir. | Ağ |
| HttpRequestTime | Tamsayı | 700 | Varsa, sunucuya isteğin gönderilmesi için geçen süre. | Http |
| HttpResponseTime | Tamsayı | 800 | Varsa, sunucuda yanıt almak için geçen sürenin miktarı. | Http |
| NetworkRuleName | Dize | Anyani bırakma | DeviceAction 'ın üzerinde karar verdiği kuralın adı veya KIMLIĞI | Ağ |
| Networkrulenumarası | int |  23 | Eşleşen kural numarası  | Ağ |
| Networksessionıd | string | 172_12_53_32_4322__123_64_207_1_80 | Raporlama cihazı tarafından raporlanan oturum tanımlayıcısı. Örneğin, kimlik doğrulamasından sonraki belirli uygulamalar için L7 oturum tanımlayıcısı | Ağ |
| SrcGeoCity | Dize | Burpatton | Kaynak IP adresiyle ilişkilendirilen şehir | Kaynaktaki<br>Coğrafi Bölge |
| SrcGeoCountry | Ülke (dize) | ABD | Kaynak IP adresiyle ilişkilendirilen ülke | Kaynaktaki<br>Coğrafi Bölge |
| SrcDvcHostname | Cihaz adı (dize) |  villaın | Kaynak cihazın cihaz adı | Kaynaktaki<br>Cihaz |
| SrcDvcFqdn | string | Villain.malicious.com | Günlüğün oluşturulduğu konağın tam etki alanı adı | Kaynaktaki<br>Cihaz |
| SrcDvcDomain | string | EVıLORG | Oturumun başlatıldığı cihazın etki alanı | Kaynaktaki<br>Cihaz |
| SrcDvcOs | Dize | iOS | Kaynak cihazın işletim sistemi | Kaynaktaki<br>Cihaz |
| SrcDvcModelName | Dize | Samsung Galaxy Note | Kaynak cihazın model adı | Kaynaktaki<br>Cihaz |
| SrcDvcModelNumber | Dize | 10.0 | Kaynak cihazın model numarası | Kaynaktaki<br>Cihaz |
| SrcDvcType | Dize | Mobil | Kaynak cihazın türü | Kaynaktaki<br> Cihaz |
| SrcIntefaceName | Dize | eth01 | Kaynak cihaz tarafından bağlantı veya oturum için kullanılan ağ arabirimi. | Kaynak |
| Srcınterfaceguid | Dize | 46ad544b-eaf0-47ef-827c-266030f545a6 | Kullanılan ağ arabiriminin GUID 'SI | Kaynak |
| Srcıdresi | IP Adresi | 77.138.103.108 | Bağlantının veya oturumun kaynaklandığı IP adresi. | Kaynaktaki<br>IP |
| Srcdvşifredresi | IP Adresi | 77.138.103.108 | Ağ paketiyle doğrudan ilişkili olmayan bir cihazın kaynak IP adresi (bir sağlayıcı tarafından toplanan veya açıkça hesaplanarak). | Kaynaktaki<br>Aygıtların<br>IP |
| Srcgeolanetnetme | Enlem (Double) | 44,475833 | Kaynak IP adresiyle ilişkili coğrafi koordinat enlem | Kaynaktaki<br>Coğrafi Bölge |
| SrcGeoLongitude | Boylam (Double) | -73,211944 | Kaynak IP adresiyle ilişkili coğrafi koordinat Boylam | Kaynaktaki<br>Coğrafi Bölge |
| SrcMacAddr | Dize | 06:10:9F: EB: 8F: 14 | Bağlantı od oturumunun kaynaklandığı ağ arabiriminin MAC adresi. | Kaynaktaki<br>Mac |
| SrcDvcMacAddr | Dize | 06:10:9F: EB: 8F: 14 | Doğrudan ağ paketiyle ilişkilendirilmemiş bir cihazın kaynak MAC adresi. | Kaynaktaki<br>Aygıtların<br>Mac |
| SrcPortNumber | Tamsayı | 2335 | Bağlantının kaynaklandığı IP bağlantı noktası. Birden çok bağlantıyı kapsayan bir oturum için uygun olmayabilir. | Kaynaktaki<br>Bağlantı noktası |
| SrcGeoRegion | Bölge (dize) | Vermont | Kaynak IP adresiyle ilişkili bir ülkenin içindeki bölge | Kaynaktaki<br>Coğrafi Bölge |
| Srcresourceıd | Dize | /Subscriptions/3c1bb38c-82e3-4f8d-A115-a7110ba70d05/ResourceGroups/contoso77/Providers/Microsoft.COMPUTE/virtualmachines/syslogserver1 | İletiyi üreten cihazın kaynak KIMLIĞI. | Kaynak |
| SrcNatIpAddr | IP Adresi | 4.3.2.1 | Güvenlik Duvarı gibi bir aracı NAT aygıtı tarafından bildirilirse, NAT aygıtı tarafından hedefle iletişim için kullanılan IP adresi. | Kaynak NAT,<br>IP |
| SrcNatPortNumber | Tamsayı | 345 | Güvenlik Duvarı gibi bir aracı NAT aygıtı tarafından bildirilirse, NAT aygıtı tarafından hedefle iletişim için kullanılan bağlantı noktası. | Kaynak NAT,<br>Bağlantı noktası |
| SrcUserSid | Kullanıcı KIMLIĞI (dize) | S-15-1445 | Oturumlar kaynağıyla ilişkili kimliğin Kullanıcı KIMLIĞI. Genellikle, Kullanıcı istemcide bir eylem gerçekleştiriyor. Ayrıntılar için bkz. "veri türleri". | Kaynaktaki<br>Kullanıcı |
| Srcuseraadıd | Dize (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | Oturumun kaynak sonundaki kullanıcının Azure AD hesabı nesne KIMLIĞI | Kaynaktaki<br>Kullanıcı |
| SrcUserName | Kullanıcı adı (dize) | olduğundan | Oturumlar kaynağıyla ilişkili kimliğin Kullanıcı adı. Genellikle, Kullanıcı istemcide bir eylem gerçekleştiriyor. Ayrıntılar için bkz. "veri türleri". | Kaynak<br>Kullanıcı |
| SrcUserUpn | string | bob@alice.com | Oturumu başlatan hesabın UPN 'si | Kaynaktaki<br>Kullanıcı |
| SrcUserDomain | string | MASAÜST | Oturumu başlatan hesabın etki alanı | Kaynaktaki<br>Kullanıcı |
| SrcZone | Dize | Dokunma | Raporlama cihazı tarafından tanımlanan, kaynağın ağ bölgesi. | Kaynak |
| NetworkProtocol | Dize | TCP | Bağlantı veya oturum tarafından kullanılan IP protokolü. Genellikle, TCP, UDP veya ıCMP | Ağ |
| CloudAppName | Dize | Facebook | Bir proxy tarafından tanımlanan bir HTTP uygulaması için hedef uygulamanın adı. | Bulut |
| Cloudappıd | Dize | 124 | Bir proxy tarafından tanımlanan bir HTTP uygulaması için hedef uygulamanın KIMLIĞI. Bu değer genellikle kullanılan proxy 'ye özeldir. | Bulut |
| CloudAppOperation | Dize | DeleteFile | Kullanıcının bir proxy tarafından tanımlanan bir HTTP uygulaması bağlamında gerçekleştirdiği işlem. Bu değer genellikle kullanılan proxy 'ye özeldir. | Bulut |
| Cloudapprklevel | Dize | 3 | Bir proxy tarafından tanımlanan bir HTTP uygulamasıyla ilişkili risk düzeyi. Bu değer genellikle kullanılan proxy 'ye özeldir. | Bulut |
| Kısaltın | Dize | ImNotMalicious.exe | Dosya adı bilgilerini sağlayan FTP ve HTTP gibi protokoller için ağ bağlantıları üzerinden aktarılan dosya adı. | Dosya |
| Null | Dize | C:\Malicious\ImNotMalicious.exe | Dosyanın dosya adı da dahil olmak üzere tam yol | Dosya |
| FileHashMd5 | Dize | 51BC68715FC7C109DGIZLENIR 406B42D9D78F | Protokoller için ağ bağlantıları üzerinden aktarılan dosyanın MD5 karma değeri. | Dosya |
| FileHashSha1 | Dize | 491AE3... C299821476F4 | Protokoller için ağ bağlantıları üzerinden aktarılan dosyanın SHA1 karma değeri. | Dosya |
| FileHashSha256 | Dize | 9B8F8EDB... C129976F03 | Protokoller için ağ bağlantıları üzerinden aktarılan dosyanın SHA256 karma değeri. | Dosya |
| FileHashSha512 | Dize | 5 E127D... F69F73F01F361 | Protokoller için ağ bağlantıları üzerinden aktarılan dosyanın SHA512 olur karma değeri. | Dosya |
| FileExtension |  Dize | exe | FTP ve HTTP gibi protokoller için ağ bağlantıları üzerinden aktarılan dosyanın türü. | Dosya
| FileMimeType | Dize | Uygulama/MSWord | FTP ve HTTP gibi protokoller için ağ bağlantıları üzerinden aktarılan dosyanın MIME türü | Dosya |
| İ | Tamsayı | 23500 | Protokollerin ağ bağlantıları üzerinden aktarılan dosyanın bayt cinsinden boyutu. | Dosya |
| HttpVersion | Dize | 2.0 | HTTP/HTTPS ağ bağlantıları için HTTP Isteği sürümü. | Http |
| HttpRequestMethod | Dize | GET | HTTP/HTTPS ağ oturumlarının HTTP yöntemi. | Http |
| HttpStatusCode | Dize | 404 | HTTP/HTTPS ağ oturumlarının HTTP durum kodu. | Http |
| HttpContentType | Dize | multipart/form-Data; sınır = bir şey | HTTP/HTTPS ağ oturumlarının HTTP yanıtı içerik türü üstbilgisi. | Http |
| Htttercih Errerorjinal | Dize | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS ağ oturumlarının HTTP başvuran üst bilgisi. | Http |
| Httpuseruo Toriginal | Dize | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, Gecko gibi) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS ağ oturumlarının HTTP Kullanıcı Aracısı üst bilgisi. | Http |
| HttpRequestXff | Dize | 120.12.41.1 | HTTP/HTTPS ağ oturumları için HTTP X-Iletilmiş-Için üst bilgi. | Http |
| URL kategorisi | Dize | Arama motorları | Bir URL 'nin tanımlı gruplandırması (veya URL 'deki etki alanını temel alabilir) (örneğin, Yetişkin, Haberler, reklam, park alanları vb.) | url |
| UrlOriginal | Dize | https://contoso.com/fo/?k=v&q = u # f | HTTP/HTTPS ağ oturumlarının HTTP isteği URL 'SI. | Url |
| URL ana bilgisayar adı | Dize | contoso.com | HTTP/HTTPS ağ oturumlarının bir HTTP isteği URL 'sinin etki alanı kısmı. | Url |
| ThreatCategory | Dize | Truva | Bir IP 'nin Web güvenlik ağ geçidi ve bu ağ oturumuyla ilişkili bir güvenlik sistemi tarafından tanımlanan bir tehdit kategorisi. | Tehdit |
| Threatıd | Dize | Tr. 124 | Bir IP 'nin web güvenliği ağ geçidi gibi bir güvenlik sistemi tarafından tanımlanan tehdit KIMLIĞI ve bu ağ oturumuyla ilişkili. | Tehdit |
| ThreatName | Dize | EICAR test dosyası | Tanınan tehdit veya kötü amaçlı yazılımın adı | Tehdit |
| AdditionalFields alanları | Dinamik (JSON paketi) | {<br>Property1: "val1",<br>Property2: "val2 & lt"<br>} | Şemada karşılık gelen bir sütun eşleşirken, ek alanlar bir JSON çantasında depolanabilir.<br>Sorgu süresi ayrıştırma için bu yöntemin kullanılması önerilir, çünkü bir JSON 'a paketleme verileri sorgu performansını düşürür. Bunun yerine, ek sütunları yükseltmeniz önerilir.<br>Gelecekteki alım zamanı ayrıştırma senaryolarında, bu JSON paket sütununa ek veriler toplanacaktır. | Özel alan |
| 
