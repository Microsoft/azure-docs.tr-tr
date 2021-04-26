---
title: Azure Sentinel varlık türleri başvurusu | Microsoft Docs
description: Bu makalede, Azure Sentinel varlık türleri ve bunların gerekli tanımlayıcıları görüntülenir.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456343"
---
# <a name="azure-sentinel-entity-types-reference"></a>Azure Sentinel varlık türleri başvurusu

## <a name="entity-types-and-identifiers"></a>Varlık türleri ve tanımlayıcıları

Aşağıdaki tabloda, şu anda Azure Sentinel 'de eşleme için kullanılabilen **varlık türleri** ve [analiz kuralı sihirbazının](tutorial-detect-threats-custom.md) [varlık eşleme](map-data-fields-to-entities.md) bölümünde bulunan **tanımlayıcılar** açılan listesinde görünen her bir varlık türü için **tanımlayıcılar** olarak kullanılabilir **öznitelikler** gösterilmektedir.

**Gerekli tanımlayıcılar** sütunundaki tanımlayıcıların her biri, varlığını belirlemek için en az gereklidir. Ancak, gerekli bir tanımlayıcı kendi kendine *benzersiz* bir kimlik sağlamak için yeterli olmayabilir. Daha fazla tanımlayıcı kullanıldığında benzersiz kimlik oluşma olasılığı artar. Tek bir varlık eşlemesi için en fazla üç tanımlayıcı kullanabilirsiniz.

En iyi sonuçlar için-garantili benzersiz kimlik için, mümkün olduğunda **en güçlü tanımlayıcılar** sütunundan tanımlayıcıları kullanmanız gerekir. Birden çok güçlü tanımlayıcı kullanımı, değişen veri kaynaklarından ve şemalardan güçlü tanımlayıcılar arasında bağıntı imkanı sunar. Bu, Azure Sentinel 'in belirli bir varlık için daha kapsamlı öngörüler sağlamasına olanak tanır.

| Varlık türü | Tanımlayıcılar | Gerekli tanımlayıcılar | En güçlü tanımlayıcılar |
| - | - | - | - |
| [**Kullanıcı hesabı**](#user-account)<br>*Hesabı* | Name<br>FullName<br>NT etki alanı<br>DnsDomain<br>UPNSuffix<br>SID<br>Aadtenantıd<br>Aaduserıd<br>PUID<br>Idomainkatılmış<br>DisplayName<br>Objectguıd 'dir | FullName<br>SID<br>Name<br>Aaduserıd<br>PUID<br>Objectguıd 'dir | Ad + NTDomain<br>Ad + UPNSuffix<br>Aaduserıd<br>SID |
| [**Ana bilgisayar**](#host) | DnsDomain<br>NT etki alanı<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>Omsagentıd<br>OSFamily<br>OSVersion<br>Idomainkatılmış | FullName<br>HostName<br>NetBiosName<br>AzureID<br>Omsagentıd | Ana bilgisayar adı + NTDomain<br>Ana bilgisayar adı + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>Omsagentıd |
| [**IP adresi**](#ip-address)<br>*IP* | Adres | Adres | |
| [**Kötü amaçlı yazılım**](#malware) | Name<br>Kategori | Name | |
| [**Dosya**](#file) | Dizin<br>Name | Name | |
| [**İşleme**](#process) | Işlem<br>CommandLine<br>Yükseltme belirteci<br>CreationTimeUtc | CommandLine<br>Işlem | |
| [**Bulut uygulaması**](#cloud-application)<br>*(Cloudadpplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Etki alanı adı**](#domain-name)<br>*BKZ* | DomainName | DomainName | |
| [**Azure kaynağı**](#azure-resource) | ResourceId | ResourceId | |
| [**Dosya karması**](#file-hash)<br>*(FileHash)* | Algoritma<br>Değer | Algoritma + değer | |
| [**Kayıt defteri anahtarı**](#registry-key) | Hive<br>Anahtar | Hive<br>Anahtar | Hive + anahtar |
| [**Kayıt defteri değeri**](#registry-value) | Name<br>Değer<br>ValueType | Name | |
| [**Güvenlik grubu**](#security-group) | DistinguishedName 'dir<br>SID<br>Objectguıd 'dir | DistinguishedName 'dir<br>SID<br>Objectguıd 'dir | |
| [**URL**](#url) | Url | Url | |
| [**IoT cihazı**](#iot-device) | Iothub<br>DeviceId<br>DeviceName<br>Iotsecurityagentıd<br>DeviceType<br>Kaynak<br>SourceRef<br>Üretici<br>Modelleme<br>OperatingSystem<br>Belirlenemiyor<br>MacAddress<br>Protokoller<br>SerialNumber | Iothub<br>DeviceId | Iothub + DeviceID |
| [**Kutunuzu**](#mailbox) | Mailboxbir Yaddress<br>DisplayName<br>'Le<br>Externaldirectoryobjectıd<br>RiskLevel | Mailboxbir Yaddress | |
| [**Posta kümesi**](#mail-cluster) | Networkmessageıds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Tehditler<br>Sorgu<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Kaynak<br>Clustersourceıdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup listesinde | Sorgu<br>Kaynak | Sorgu + kaynak |
| [**Posta iletisi**](#mail-message) | Alıcı<br>Adresleri<br>Tehditler<br>Gönderen<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>Senderıp<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>Alınma tarihi<br>Networkmessageıd<br>Internetmessageıd<br>Konu<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Dil<br>ThreatDetectionMethods | Networkmessageıd<br>Alıcı | Networkmessageıd + alıcısı |
| [**Gönderim postası**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Gönderenin<br>Networkmessageıd<br>Timestamp<br>Alıcı<br>Gönderen<br>Senderıp<br>Konu<br>ReportType | SubmissionId<br>Networkmessageıd<br>Alıcı<br>Gönderenin |  |
|

## <a name="entity-type-schemas"></a>Varlık türü şemaları

Aşağıda, her bir varlık türünün tam şemalarına daha ayrıntılı bir bakış verilmiştir. Bu şemaların çoğunun diğer varlık türlerine yönelik bağlantıları içerdiğine dikkat edin. Örneğin, Kullanıcı hesabı şeması, bir kullanıcı hesabının bir özniteliği üzerinde tanımlanan ana bilgisayar olduğundan, ana bilgisayar varlık türünün bir bağlantısını içerir. Bu dışarıdan bağlantılı varlıklar, varlık eşleme için tanımlayıcı olarak kullanılamaz, ancak varlık sayfalarındaki ve araştırma grafiğinde varlıkların tam bir resmini vermek için çok faydalı olur.

> [!NOTE]
> **Tür** sütunundaki değeri izleyen bir soru işareti alanın null değer atanabilir olduğunu gösterir.

## <a name="user-account"></a>Kullanıcı hesabı

*Varlık adı: hesap*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | hesabı |
| Name | Dize | Hesabın adı. Bu alan, kendisine eklenen herhangi bir etki alanı olmadan yalnızca adı tutmalıdır. |
| *FullName* | *yok* | *Şemanın parçası değil, varlık eşlemesinin eski sürümü ile geriye dönük uyumluluk için dahil edilmiştir.*
| NT etki alanı | Dize | NetBIOS etki alanı adı uyarı biçiminde göründüğü gibi – etkialanı \ KullanıcıAdı Örnekler: finans, NT YETKILISI |
| DnsDomain | Dize | Tam etki alanı DNS adı. Örnekler: finance.contoso.com |
| UPNSuffix | Dize | Hesap için Kullanıcı asıl adı soneki. Bazı durumlarda, bu da etki alanı adıdır. Örnekler: contoso.com |
| Ana bilgisayar | Varlık | Hesabı içeren konak, yerel bir hesaptır. |
| SID | Dize | Hesap güvenlik tanımlayıcısı, örneğin S-1-5-18. |
| Aadtenantıd | 'İni? | Biliniyorsa, Azure AD kiracı KIMLIĞI. |
| Aaduserıd | 'İni? | Biliniyorsa, Azure AD hesabı nesne KIMLIĞI. |
| PUID | 'İni? | Biliniyorsa, Azure AD Passport Kullanıcı KIMLIĞI. |
| Idomainkatılmış | Bool? | Bunun bir etki alanı hesabı olup olmadığını belirler. |
| DisplayName | Dize | Hesabın görünen adı. |
| Objectguıd 'dir | 'İni? | Objectguıd özniteliği, Active Directory tarafından atanan nesnenin benzersiz tanımlayıcısı olan tek değerli bir özniteliktir. |
|

Bir hesap varlığının güçlü tanımlayıcıları:

- Ad + UPNSuffix
- Aaduserıd
- SID + ana bilgisayar (yerleşik hesapların SID 'Leri için gereklidir)
- SID (yerleşik hesapların SID 'Leri dışında)
- Ad + NTDomain (NT etki alanı bir yerleşik etki alanı değilse, örneğin "çalışma grubu")
- Ad + ana bilgisayar (NTDomain, yerleşik bir etki alanı ise "çalışma grubu" gibi)
- Ad + DnsDomain
- PUID
- Objectguıd 'dir

Bir hesap varlığının zayıf tanımlayıcıları:

- Name

## <a name="host"></a>Ana bilgisayar

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | konağının |
| DnsDomain | Dize | Bu konağın ait olduğu DNS etki alanı. Biliniyorsa, etki alanı için tüm DNS son ekini içermelidir. |
| NT etki alanı | Dize | Bu konağın ait olduğu NT etki alanı. |
| HostName | Dize | Etki alanı soneki olmayan ana bilgisayar adı. |
| *FullName* | *yok* | *Şemanın parçası değil, varlık eşlemesinin eski sürümü ile geriye dönük uyumluluk için dahil edilmiştir.*
| NetBiosName | Dize | Ana bilgisayar adı (Windows 2000 öncesi). |
| Iotdevice | Varlık | IoT cihaz varlığı (bu konak bir IoT cihazını temsil ediyorsa). |
| AzureID | Dize | Biliniyorsa VM 'nin Azure Kaynak KIMLIĞI. |
| Omsagentıd | Dize | Ana bilgisayarda OMS Aracısı yüklüyse OMS Aracısı KIMLIĞI. |
| OSFamily | Yardımının? | Aşağıdaki değerlerden biri: <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | Dize | İşletim sisteminin serbest metinli bir gösterimi.<br>Bu alan, belirli sürümleri, OSFamily ' den daha hassas veya OSFamily numaralandırması tarafından desteklenmeyen gelecek değerlerle tutmak için tasarlanmıştır. |
| Idomainkatılmış | Bool | Bu konağın bir etki alanına ait olup olmadığını belirler. |
|

Bir konak varlığının güçlü tanımlayıcıları:
- Ana bilgisayar adı + NTDomain
- Ana bilgisayar adı + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- Omsagentıd
- Iotdevice (varlık eşleme için desteklenmez)

Bir konak varlığının zayıf tanımlayıcıları:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP Adresi

*Varlık adı: IP*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | IP |
| Adres | Dize | Dize olarak IP adresi (örneğin, IPv4 veya IPv6). |
| Konum | Konumu | IP varlığına eklenen coğrafi konum bağlamı. |
|

Bir IP varlığının güçlü tanımlayıcıları:
- Adres

## <a name="malware"></a>Kötü Amaçlı Yazılımlar

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | tespit |
| Name | Dize | Satıcının (gibi) kötü amaçlı yazılım adı `Win32/Toga!rfn` . |
| Kategori | Dize | Satıcı tarafından kötü amaçlı yazılım kategorisi, ör. Truva atı. |
| Dosyalar | Liste\<Entity> | Kötü amaçlı yazılımın bulunduğu bağlı dosya varlıklarının listesi. , Satır içi veya başvuru olarak dosya varlıklarını içerebilir.<br>Yapıyla ilgili ek ayrıntılar için dosya varlığına bakın. |
| İşlemler | Liste\<Entity> | Kötü amaçlı yazılımın bulunduğu bağlantılı işlem varlıklarının listesi. Bu, genellikle uyarı dosya daha az etkinlik üzerinde tetiklendiğinde kullanılır.<br>Yapı hakkında daha fazla bilgi için [süreç](#process) varlığına bakın. |
|

Kötü amaçlı yazılım varlığının güçlü tanımlayıcıları:

- Ad + kategori

## <a name="file"></a>Dosya

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | dosyasýný |
| Dizin | Dize | Dosyanın tam yolu. |
| Name | Dize | Yol olmadan dosya adı (bazı uyarılar yol içermemelidir). |
| Ana bilgisayar | Varlık | Dosyanın depolandığı ana bilgisayar. |
| Dosya karmaları | &lt;Varlık listeleme&gt; | Bu dosyayla ilişkili dosya karmaları. |
|

Bir dosya varlığının güçlü tanımlayıcıları:
- Ad + Dizin
- Ad + dosya karması
- Ad + dizin + dosya karması

## <a name="process"></a>İşleme

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | işle |
| Işlem | Dize | İşlem KIMLIĞI. |
| CommandLine | Dize | İşlemi oluşturmak için kullanılan komut satırı. |
| Yükseltme belirteci | Yardımının? | İşlemle ilişkili yükseltme belirteci.<br>Olası değerler:<li>Tokenyükseltir Tiontypedefault<li>Tokenyükseltir Tiontypefull<li>Tokenyükseltir Tiontypelimited |
| CreationTimeUtc | Hem? | İşlemin çalışmaya başladığı zaman. |
| Özelliğini | Varlık (dosya) | Dosya varlığını satır içi veya başvuru olarak içerebilir.<br>Yapıyla ilgili ek ayrıntılar için dosya varlığına bakın. |
| Hesap | Varlık | İşlem çalıştıran hesap.<br>, Satır içi veya başvuru olarak [Hesap](#user-account) varlığını içerebilir.<br>Yapı hakkında daha fazla bilgi için bkz. [Hesap](#user-account) varlığı. |
| ParentProcess | Varlık (Işlem) | Üst işlem varlığı. <br>Kısmi veri içerebilir, yani yalnızca PID. |
| Ana bilgisayar | Varlık | İşlemin üzerinde çalıştığı konak. |
| LogonSession | Varlık (HostLogonSession) | İşlemin çalıştığı oturum. |
|

İşlem varlığının güçlü tanımlayıcıları:

- Ana bilgisayar + Işlemkimliği + CreationTimeUtc
- Ana bilgisayar + Parentişlemkimliği + CreationTimeUtc + CommandLine
- Ana bilgisayar + işlemkimliği + CreationTimeUtc + Image
- Ana bilgisayar + Işlemkimliği + CreationTimeUtc + Image. FileHash

Bir işlem varlığının zayıf tanımlayıcıları:

- Işlemkimliği + CreationTimeUtc + CommandLine (ve ana bilgisayar yok)
- Işlemkimliği + CreationTimeUtc + Image(ve ana bilgisayar yok)

## <a name="cloud-application"></a>Bulut uygulaması

*Varlık adı: Cloudadpplication*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' Cloud-Application ' |
| AppId | int | Uygulamanın teknik tanımlayıcısı. Bu, [bulut uygulaması tanımlayıcıları](#cloud-application-identifiers)listesinde tanımlanan değerlerden biri olmalıdır. AppID alanı için değer isteğe bağlıdır. |
| Name | Dize | İlgili bulut uygulamasının adı. Uygulama adının değeri isteğe bağlıdır. |
| InstanceName | Dize | Bulut uygulamasının Kullanıcı tanımlı örnek adı. Genellikle müşterinin sahip olduğu aynı türdeki birçok uygulamayı ayırt etmek için kullanılır. |
|

Bulut uygulaması varlığının güçlü tanımlayıcıları:
 - AppID (InstanceName olmadan)
 - Ad (InstanceName olmadan)
 - AppID + InstanceName
 - Ad + ÖrnekAdı

## <a name="domain-name"></a>Etki alanı adı

*Varlık adı: DNS*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | bkz |
| DomainName | Dize | Uyarıyla ilişkili DNS kaydının adı. |
| Belirlenemiyor | Liste &lt; varlığı (IP)&gt; | Çözümlenen IP adreslerine karşılık gelen varlıklar. |
| Dnssunucuıp | Varlık (IP) | İsteği çözümleyen DNS sunucusunu temsil eden bir varlık. |
| Hostıpaddress | Varlık (IP) | DNS istek istemcisini temsil eden bir varlık. |
|

Bir DNS varlığının güçlü tanımlayıcıları:
- DomainName + Dnssunucuıp + Hostıpaddress

Bir DNS varlığının zayıf tanımlayıcıları:
- DomainName + Hostıpaddress

## <a name="azure-resource"></a>Azure kaynağı

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' Azure-Resource ' |
| ResourceId | Dize | Kaynağın Azure Kaynak KIMLIĞI. |
| SubscriptionId | Dize | Kaynağın abonelik KIMLIĞI. |
| TryGetResourceGroup | Bool | Varsa kaynak grubu değeri. |
| TryGetProvider | Bool | Varsa sağlayıcı değeri. |
| TryGetName | Bool | Varsa ad değeri. |
|

Azure Kaynak varlığının güçlü tanımlayıcıları:
- ResourceId

## <a name="file-hash"></a>Dosya karması

*Varlık adı: dosya karması*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' FileHash ' |
| Algoritma | Sabit listesi | Karma algoritma türü. Olası değerler:<li>Bilinmiyor<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Değer | Dize | Karma değeri. |
|

Bir dosya karması varlığının güçlü tanımlayıcıları:
- Algoritma + değer

## <a name="registry-key"></a>Kayıt defteri anahtarı

*Varlık adı: RegistryKey*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' kayıt defteri-anahtar ' |
| Hive | Yardımının? | Aşağıdaki değerlerden biri:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Anahtar | Dize | Kayıt defteri anahtarı yolu. |
|

Kayıt defteri anahtarı varlığının güçlü tanımlayıcıları:
- Hive + anahtar

## <a name="registry-value"></a>Kayıt defteri değeri

*Varlık adı: RegistryValue*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' kayıt defteri-değeri ' |
| Anahtar | Varlık (RegistryKey) | Kayıt defteri anahtarı varlığı. |
| Name | Dize | Kayıt defteri değer adı. |
| Değer | Dize | Değer verisinin dize biçimli gösterimi. |
| ValueType | Yardımının? | Aşağıdaki değerlerden biri:<li>Dize<li>İkili<li>Ekleyebileceği<li>'Ün<li>Çoklu dize<li>ExpandString<li>Yok<li>Bilinmiyor<br>Değerler, Microsoft. Win32. RegistryValueKind numaralandırması ile uyumlu olmalıdır. |
|

Bir kayıt defteri değeri varlığının güçlü tanımlayıcıları:
- Anahtar + ad

Bir kayıt defteri değeri varlığının zayıf tanımlayıcıları:
- Ad (anahtar olmadan)

## <a name="security-group"></a>Güvenlik grubu

*Varlık adı: SecurityGroup*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' güvenlik-Grup ' |
| DistinguishedName 'dir | Dize | Grup ayırt edici adı. |
| SID | Dize | SID özniteliği, grubun güvenlik tanımlayıcısını (SID) belirten tek değerli bir özniteliktir. |
| Objectguıd 'dir | 'İni? | Objectguıd özniteliği, Active Directory tarafından atanan nesnenin benzersiz tanımlayıcısı olan tek değerli bir özniteliktir. |
|

Güvenlik grubu varlığının güçlü tanımlayıcıları:
 - DistinguishedName 'dir
 - SID
 - Objectguıd 'dir

## <a name="url"></a>URL

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | 'deki |
| Url | Kullanılmamışsa | Varlığın işaret ettiği tam URL. |
|

URL varlığının güçlü tanımlayıcıları:
- URL (mutlak URL olduğunda)

Bir URL varlığının zayıf tanımlayıcıları:
- URL (göreli bir URL olduğunda)

## <a name="iot-device"></a>IoT cihazı

*Varlık adı: ıotdevice*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' ıotdevice ' |
| Iothub | Varlık (AzureResource) | Cihazın ait olduğu IoT Hub temsil eden AzureResource varlığı. |
| DeviceId | Dize | IoT Hub bağlamında cihazın KIMLIĞI. |
| DeviceName | Dize | Cihazın kolay adı. |
| Iotsecurityagentıd | 'İni? | Cihazda çalışan IoT aracısının *Defender* kimliği. |
| DeviceType | Dize | Cihazın türü (' sıcaklık algılayıcısı ', ' Freezer ', ' Rüzgar Turbin ' vb.). |
| Kaynak | Dize | Cihaz varlığının kaynağı (Microsoft/satıcı). |
| SourceRef | Varlık (URL) | Cihazın yönetildiği kaynak öğeye yönelik bir URL başvurusu. |
| Üretici | Dize | Cihazın üreticisi. |
| Modelleme | Dize | Cihazın modeli. |
| OperatingSystem | Dize | Cihazın çalıştırdığı işletim sistemi. |
| Belirlenemiyor | Varlık (IP) | Cihazın geçerli IP adresi. |
| MacAddress | Dize | Cihazın MAC adresi. |
| Protokoller | Liste &lt; dizesi&gt; | Cihazın desteklediği protokollerin bir listesi. |
| SerialNumber | Dize | Cihazın seri numarası. |
|

IoT cihaz varlığının güçlü tanımlayıcıları:
- Iothub + DeviceID

IoT cihaz varlığının zayıf tanımlayıcıları:
- DeviceID (ıothub olmadan)

## <a name="mailbox"></a>Mailbox

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | kutunuzu |
| Mailboxbir Yaddress | Dize | Posta kutusunun birincil adresi. |
| DisplayName | Dize | Posta kutusunun görünen adı. |
| 'Le | Dize | Posta kutusunun UPN 'si. |
| RiskLevel | Yardımının? | Bu posta kutusunun risk düzeyi. Olası değerler:<li>Yok<li>Düşük<li>Orta<li>Yüksek |
| Externaldirectoryobjectıd | 'İni? | Posta kutusunun AzureAD tanımlayıcısı. Hesap varlığındaki Aaduserıd öğesine benzer, ancak bu özellik ofis tarafındaki posta kutusu nesnesine özgüdür. |
|

Bir posta kutusu varlığının güçlü tanımlayıcıları:
- Mailboxbir Yaddress

## <a name="mail-cluster"></a>Posta kümesi

*Varlık adı: MailCluster*

> [!NOTE]
> **MDO**  =  Office **365 Için Microsoft Defender**, daha önce Office 365 Gelişmiş tehdit koruması (O365 ATP) olarak biliniyordu.

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' Mail-Cluster ' |
| Networkmessageıds | IList &lt; dizesi&gt; | Posta kümesinin parçası olan posta iletisi kimlikleri. |
| CountByDeliveryStatus | IDictionary &lt; String, Int&gt; | DeliveryStatus dize gösterimine göre posta iletilerinin sayısı. |
| CountByThreatType | IDictionary &lt; String, Int&gt; | ThreatType dize gösterimine göre posta iletilerinin sayısı. |
| CountByProtectionStatus | IDictionary &lt; dizesi, Long&gt; | Tehdit koruması durumuna göre posta iletilerinin sayısı. |
| Tehditler | IList &lt; dizesi&gt; | Posta kümesinin bir parçası olan posta iletilerinin tehditleri. |
| Sorgu | Dize | Posta kümesinin iletilerini tanımlamak için kullanılan sorgu. |
| QueryTime | Hem? | Sorgu süresi. |
| MailCount | 'Tir? | Posta kümesinin parçası olan posta iletilerinin sayısı. |
| IsVolumeAnomaly | Bool? | Bunun bir birim anomali posta kümesi olup olmadığını belirler. |
| Kaynak | Dize | Posta kümesinin kaynağı (varsayılan değer ' O365 ATP '). |
| Clustersourceıdentifier | Dize | Bu posta kümesinin kaynağı olan postanın ağ ileti KIMLIĞI. |
| ClusterSourceType | Dize | Posta kümesinin kaynak türü. Bu, MDO MailClusterSourceType ayarıyla eşlenir (Yukarıdaki nota bakın). |
| ClusterQueryStartTime | Hem? | Küme başlangıç zamanı-küme sayısı sorgusu için başlangıç zamanı olarak kullanılır. Genellikle MDO 'dan bitiş saati eksi DaysToLookBack ayarı için tarihler (Yukarıdaki nota bakın). |
| ClusterQueryEndTime | Hem? | Küme bitiş zamanı-küme sayısı sorgusu için bitiş saati olarak kullanılır. Genellikle posta verilerinin alınma zamanı. |
| ClusterGroup listesinde | Dize | MDO 'da kullanılan kusto sorgu anahtarına karşılık gelir (Yukarıdaki nota bakın). |
|

Posta kümesi varlığının güçlü tanımlayıcıları:
- Sorgu + kaynak

## <a name="mail-message"></a>Posta iletisi

*Varlık adı: MailMessage*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' Mail-Message ' |
| Dosyalar | IList &lt; dosyası&gt; | Bu posta iletisi eklerinin dosya varlıkları. |
| Alıcı | Dize | Bu e-posta iletisinin alıcısı. Birden çok alıcı olması durumunda posta iletisi kopyalanır ve her kopyanın bir alıcısı vardır. |
| Adresleri | IList &lt; dizesi&gt; | Bu posta iletisinde bulunan URL 'Ler. |
| Tehditler | IList &lt; dizesi&gt; | Bu posta iletisinde bulunan tehditler. |
| Gönderen | Dize | Gönderenin e-posta adresi. |
| P1Sender | Dize | Bu e-posta adresi (temsilci) gönderen kullanıcının bu postayı "Şirket adına P2 (birincil) kullanıcısı". E-posta temsilci tarafından gönderilmezse, bu değer P2Sender değerine eşittir. |
| P1SenderDisplayName | Dize | Bu postayı P2 (birincil) Kullanıcı adına Gönderen (Temsilcili) kullanıcının görünen adı ". "OnbehalfofSenderDisplayName" özelliği tarafından e-posta üst bilgisinde temsil edilir. |
| P1SenderDomain | Dize | Bu postayı P2 (birincil) Kullanıcı adına Gönderen (Temsilcili) kullanıcının e-posta etki alanı. E-posta temsilci tarafından gönderilmezse, bu değer P2SenderDomain değerine eşittir. |
| P2Sender | Dize | Bu e-postanın gönderildiği adına (birincil) kullanıcının e-postası. |
| P2SenderDisplayName | Dize | Bu e-postanın gönderildiği adına (birincil) kullanıcının görünen adı. E-posta temsilci tarafından gönderilmezse, bu, gönderenin görünen adını temsil eder. |
| P2SenderDomain | Dize | Bu e-postanın gönderildiği adına (birincil) kullanıcının e-posta etki alanı. E-posta temsilci tarafından gönderilmezse, bu, gönderenin etki alanını temsil eder. |
| Senderıp | Dize | Gönderenin IP adresi. |
| Alınma tarihi | DateTime | Bu iletinin alınma tarihi. |
| Networkmessageıd | 'İni? | Bu posta iletisinin ağ ileti KIMLIĞI. |
| Internetmessageıd | Dize | Bu posta iletisinin Internet ileti KIMLIĞI. |
| Konu | Dize | Bu posta iletisinin konusu. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | U? | Office 365 için Microsoft Defender tarafından, eşleşen veya benzer posta iletileri bulmak için kullanılır. |
| AntispamDirection | Yardımının? | Bu posta iletisinin yönlülüğü. Olası değerler:<li>Bilinmiyor<li>Gelen<li>Giden<li>Kuruluş içi (iç) |
| DeliveryAction | Yardımının? | Bu posta iletisinin teslim eylemi. Olası değerler:<li>Bilinmiyor<li>Deliveredavsspam<li>Teslim Edildi<li>Engellendi<li>Silinmeyecek |
| DeliveryLocation | Yardımının? | Bu posta iletisinin teslim konumu. Olası değerler:<li>Bilinmiyor<li>Gelen Kutusu<li>JunkFolder<li>DeletedFolder<li>Karantina<li>Dış<li>Başarısız<li>Bırakılmış<li>Lirse |
| Dil | Dize | Posta içeriğinin yazıldığı dil. |
| ThreatDetectionMethods | IList &lt; dizesi&gt; | Bu e-postaya uygulanan tehdit algılama yöntemlerinin listesi. |
|

Posta iletisi varlığının güçlü tanımlayıcıları:
- Networkmessageıd + alıcısı

## <a name="submission-mail"></a>Gönderim postası

*Varlık adı: SubmissionMail*

| Alan | Tür | Açıklama |
| ----- | ---- | ----------- |
| Tür | Dize | ' SubmissionMail ' |
| SubmissionId | 'İni? | Gönderim KIMLIĞI. |
| SubmissionDate | Hem? | Bu gönderim için raporlanan tarih saat. |
| Gönderenin | Dize | Gönderenin e-posta adresi. |
| Networkmessageıd | 'İni? | Gönderimin ait olduğu e-postanın ağ ileti KIMLIĞI. |
| Timestamp | Hem? | İleti alındığında zaman damgası (posta). |
| Alıcı | Dize | Postanın alıcısı. |
| Gönderen | Dize | Postanın göndericisi. |
| Senderıp | Dize | Gönderenin IP 'si. |
| Konu | Dize | Gönderim postanın konusu. |
| ReportType | Dize | Verilen örnek için gönderim türü. Bu, önemsiz, Phsi, kötü amaçlı yazılım veya Notönemsiz ile eşlenir. |
|

Bir SubmissionMail varlığının güçlü tanımlayıcıları:
- SubmissionId, gönderenin, Networkmessageıd, Recipient

## <a name="cloud-application-identifiers"></a>Bulut uygulaması tanımlayıcıları

Aşağıdaki liste, bilinen bulut uygulamaları için tanımlayıcıları tanımlar. Uygulama KIMLIĞI değeri, bir [bulut uygulaması](#cloud-application) varlık tanımlayıcısı olarak kullanılır.

|Uygulama Kimliği|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive yazılımı|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Hizmetleri|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive İş|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Aaci|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallod CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion yaşam döngüsü|
|23043|Slack|
|23233|Çevrimiçi Microsoft Office|
|25275|Microsoft Skype Kurumsal|
|25988|Google Docs|
|26055|Microsoft Office 365 Yönetim Merkezi|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS proxy öykünücüsü|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Taödünmi|
|

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'de varlık yapısı, tanımlayıcılar ve şema hakkında bilgi edindiniz.

[Varlıklar](entities-in-azure-sentinel.md) ve [varlık eşlemesi](map-data-fields-to-entities.md)hakkında daha fazla bilgi edinin. 
