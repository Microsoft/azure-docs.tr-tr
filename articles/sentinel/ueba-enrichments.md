---
title: Azure Sentinel ueba zenginleştirmeleri başvurusu | Microsoft Docs
description: Bu makalede, Azure Sentinel 'in varlık davranışı Analizi tarafından oluşturulan varlık zenginleştirme gösterilmektedir.
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
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901793"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel ueba zenginleştirmeleri başvurusu

Bu tablolar, güvenlik olaylarınızın araştırılmasını odaklamak ve keskinleştirmek için kullanılabilen varlık zenginleştirmelerinin listesini ve bunları anlatmaktadır.

İlk iki tablo olan **Kullanıcı öngörüleri** ve **cihaz öngörüleri**, Active Directory/Azure AD ve Microsoft Threat Intelligence kaynaklarından gelen varlık bilgilerini içerir.

<a name="baseline-explained"></a>Tabloların geri kalanı, **Activity Insights tabloları** altında, Azure Sentinel 'in varlık davranışı Analizi tarafından oluşturulan davranış profillerine dayalı varlık bilgilerini içerir. Etkinlikleri, her kullanıldığı her seferinde dinamik olarak derlenen bir taban çizgisine göre çözümlenir. Her etkinlik, bu dinamik taban çizgisinin türetildiği, tanımlanan geriye doğru geri dönüş süresine sahiptir. Bu süre bu tablodaki [**taban çizgisi**](#activity-insights-tables) sütununda belirtilir.

> [!NOTE] 
> Üç tablodaki **zenginleştirme adı** alanı iki satırlık bilgi görüntüler. İlki, **kalın** olan, zenginleştirme 'nin "kolay adıdır". İkincisi *(italik ve parantezler)* , [**davranış analizi tablosunda**](identify-threats-with-entity-behavior-analytics.md#data-schema)depolanan zenginleştirme alan adıdır.

## <a name="user-insights-table"></a>Kullanıcı öngörüleri tablosu

| Zenginleştirme adı | Description | Örnek değer |
| --- | --- | --- | --- |
| **Hesap görünen adı**<br>*(AccountDisplayName)* | Kullanıcının hesap görünen adı. | Yönetici, Hayden Cook |
| **Hesap etki alanı**<br>*(AccountDomain)* | Kullanıcının hesap etki alanı adı. |  |
| **Hesap nesnesi KIMLIĞI**<br>*(Accountobjectıd)* | Kullanıcının hesap nesne KIMLIĞI. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Bir yarıçapı Bomb**<br>*(Blastraus)* | Elde eden yarıçap çeşitli faktörlere göre hesaplanır: kullanıcının kuruluş ağacındaki konumu ve Kullanıcı Azure Active Directory rolleri ve izinleri. | Düşük, orta, yüksek |
| **Etkin değil hesabı**<br>*(Ibağlantı noktası sayısı)* | Hesap, son 180 gün boyunca kullanılmadı. | Doğru, yanlış |
| **Yerel yönetici**<br>*(IsLocalAdmin)* | Hesabın yerel yönetici ayrıcalıkları vardır. | Doğru, yanlış |
| **Yeni hesap**<br>*(Inewaccount)* | Hesap son 30 gün içinde oluşturuldu. | Doğru, yanlış |
| **Şirket içi SID**<br>*(OnPremisesSID)* | Eylemle ilgili kullanıcının şirket içi SID 'SI. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Cihaz öngörüleri tablosu

| Zenginleştirme adı | Description | Örnek değer |
| --- | --- | --- | --- |
| **Tarayıcı**<br>*Tarayıcılarından* | Eylemde kullanılan tarayıcı. | Kenar, Chrome |
| **Cihaz ailesi**<br>*(DeviceFamily)* | Eylemde kullanılan cihaz ailesi. | Windows |
| **Cihaz Türü**<br>*DeviceType* | Eylemde kullanılan istemci cihaz türü | Masaüstü |
| **BAŞVURUN**<br>*BAŞVURUN* | Eylemde kullanılan Internet hizmeti sağlayıcısı. |  |
| **İşletim sistemi**<br>*OperatingSystem* | Eylemde kullanılan işletim sistemi. | Windows 10 |
| **Tehdit Intel gösterge açıklaması**<br>*(ThreatIntelIndicatorDescription)* | Eylemde kullanılan IP adresinden çözümlenen gözlemlenen tehdit göstergesinin açıklaması. | Ana bilgisayar botnet: azorult üyesidir |
| **Tehdit Intel gösterge türü**<br>*(ThreatIntelIndicatorType)* | Eylemde kullanılan IP adresinden çözümlenen tehdit göstergesinin türü. | Botnet, C2, Cryptoaraştırma, koyu ağ, DDoS, MaliciousUrl, kötü amaçlı yazılım, kimlik avı, proxy, PUA, listem |
| **Kullanıcı Aracısı**<br>*Kullanıcı* | Eylemde kullanılan Kullanıcı Aracısı. | Microsoft Azure Graph Istemci kitaplığı 1,0,<br>Swagger-CodeGen/1.4.0.0/CSharp,<br>EvoSTS |
| **Kullanıcı Aracısı ailesi**<br>*(UserAgentFamily)* | Eylemde kullanılan Kullanıcı Aracısı ailesi. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Etkinlik öngörüleri tabloları

#### <a name="action-performed"></a>Gerçekleştirilen eylem

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **Kullanıcı ilk kez eylem gerçekleştirdi**<br>*(Firsttimeuserperformedadction)* | 180 | Eylem, Kullanıcı tarafından ilk kez gerçekleştirildi. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak gerçekleştirilen eylem**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Eylem Kullanıcı tarafından yaygın olarak gerçekleştirilmez. | Doğru, yanlış |
| **Eşler arasında yaygın olarak gerçekleştirilen eylem**<br>*(Actionuncommonlyperformedadmongpeers)* | 180 | Eylem, kullanıcının eşleri arasında yaygın olarak gerçekleştirilmez. | Doğru, yanlış |
| **Kiracıda ilk kez eylem gerçekleştirildi**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Eylem, kuruluştaki herkes tarafından ilk kez gerçekleştirildi. | Doğru, yanlış |
| **Kiracıda yaygın olarak gerçekleştirilen eylem**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | İşlem kuruluşta yaygın olarak gerçekleştirilmez. | Doğru, yanlış |
|

#### <a name="app-used"></a>Kullanılan uygulama

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **Kullanıcı ilk kez kullanılan uygulama**<br>*(FirstTimeUserUsedApp)* | 180 | Uygulama, Kullanıcı tarafından ilk kez kullanıldı. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak kullanılan uygulama**<br>*(AppUncommonlyUsedByUser)* | 10 | Uygulama, Kullanıcı tarafından yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Eşler arasında yaygın olarak kullanılmayan uygulama**<br>*(Appuncommonlyusedadmongpeers)* | 180 | Uygulama, kullanıcının eşleri arasında yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Kiracıda uygulama ilk kez gözlemlendi**<br>*(FirstTimeAppObservedInTenant)* | 180 | Uygulama, kuruluşta ilk kez gözlemlendi. | Doğru, yanlış |
| **Kiracıda yaygın olarak kullanılan uygulama**<br>*(AppUncommonlyUsedInTenant)* | 180 | Uygulama, kuruluşta yaygın olarak kullanılmaz. | Doğru, yanlış |
| 

#### <a name="browser-used"></a>Kullanılan tarayıcı

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **Kullanıcı tarayıcı ile ilk kez bağlandı**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Tarayıcı, Kullanıcı tarafından ilk kez gözlemlendi. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak kullanılan tarayıcı**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Tarayıcı Kullanıcı tarafından yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Eşler arasında yaygın olarak kullanılmayan tarayıcı**<br>*(Browseruncommonlyusedadmongpeers)* | 30 | Tarayıcı, kullanıcının eşleri arasında yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Kiracıda ilk tarayıcıda bir kez gözlemlendi**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Tarayıcı, kuruluşta ilk kez gözlemlendi. | Doğru, yanlış |
| **Kiracıda yaygın olarak kullanılmayan tarayıcı**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Tarayıcı kuruluşta yaygın olarak kullanılmaz. | Doğru, yanlış |
| 

#### <a name="country-connected-from"></a>Ülke bağlı

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **İlk kez Kullanıcı ülkeden bağlanır**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | IP adresinden çözülen coğrafi konum, Kullanıcı tarafından ilk kez bağlandı. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak bağlanan ülke**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | IP adresinden çözülen coğrafi konum, genellikle kullanıcı tarafından bağlı değildir. | Doğru, yanlış |
| **Eşler arasında yaygın olarak bağlanan ülke**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | IP adresinden çözülen coğrafi konum, genellikle kullanıcının eşleri arasında bağlı değildir. | Doğru, yanlış |
| **Kiracıdan gözlenen ülkeden ilk kez bağlantı**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Ülke, kuruluştaki herkes tarafından ilk kez bağlandı. | Doğru, yanlış |
| **Kiracıdan yaygın olarak bağlanan ülke**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | IP adresinden çözümlenen şekilde coğrafi konum, genellikle kuruluştan bağlı değildir. | Doğru, yanlış |
| 

#### <a name="device-used-to-connect"></a>Bağlanmak için kullanılan cihaz

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **Cihazdan ilk kez kullanıcı bağlandığında**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Kaynak cihaz, Kullanıcı tarafından ilk kez bağlandı. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak kullanılmayan cihaz**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Cihaz, Kullanıcı tarafından yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Eşler arasında yaygın olarak kullanılmayan cihaz**<br>*(Deviceuncommonlyusedadmongpeers)* | 180 | Cihaz, kullanıcının eşleri arasında yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Kiracıda cihaz ilk kez gözlemlendi**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Cihaz, kuruluşta ilk kez gözlemlendi. | Doğru, yanlış |
| **Kiracıda yaygın olarak kullanılmayan cihaz**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Cihaz kuruluşta yaygın olarak kullanılmaz. | Doğru, yanlış |
| 

#### <a name="other-device-related"></a>Cihazla ilgili diğer

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **İlk kez Kullanıcı cihazda oturum açtı**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Hedef cihaz, Kullanıcı tarafından ilk kez bağlandı. | Doğru, yanlış |
| **Kiracı içinde yaygın olarak kullanılmayan cihaz ailesi**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Cihaz ailesi kuruluşunuzda yaygın olarak kullanılmaz. | Doğru, yanlış |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Bağlanmak için kullanılan Internet hizmeti sağlayıcısı

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **İlk kez Kullanıcı ISS aracılığıyla bağlanır**<br>*(Firsttimeuserconnectedviaıss)* | 30 | ISS, Kullanıcı tarafından ilk kez gözlemlendi. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak kullanılmayan ISS**<br>*(Ipuncommonlyusedbyuser)* | 10 | ISS, Kullanıcı tarafından yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Eşler arasında yaygın olarak kullanılmayan ISS**<br>*(Ispuncommonlyusedadmongpeers)* | 30 | ISS, kullanıcının eşleri arasında yaygın olarak kullanılmaz. | Doğru, yanlış |
| **Kiracıda ISS aracılığıyla ilk kez bağlantı**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | ISS, kuruluşun ilk saatinde gözlemlendi. | Doğru, yanlış |
| **Kiracı içinde yaygın olarak kullanılmayan ISS**<br>*(Ipuncommonlyusedintenant)* | 30 | ISP, kuruluşta yaygın olarak kullanılmaz. | Doğru, yanlış |
| 

#### <a name="resource-accessed"></a>Erişilen kaynak

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **İlk kez Kullanıcı kaynağa erişti**<br>*(FirstTimeUserAccessedResource)* | 180 | Kaynağa Kullanıcı tarafından ilk kez erişildi. | Doğru, yanlış |
| **Kullanıcı tarafından yaygın olarak erişilemeyen kaynak**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | Kaynak, Kullanıcı tarafından yaygın olarak erişilmez. | Doğru, yanlış |
| **Eşler arasında yaygın olarak erişilemeyen kaynak**<br>*(Resourceuncommonlyaccessenemongpeers)* | 180 | Kaynağa, kullanıcının eşleri arasında genel olarak erişilemez. | Doğru, yanlış |
| **Kiracıda ilk kez kaynak erişildiği**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Kaynağa, kuruluştaki herkes tarafından ilk kez erişildi. | Doğru, yanlış |
| **Kiracıda genel olarak erişilemeyen kaynak**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Kaynak, kuruluşta yaygın olarak erişilmez. | Doğru, yanlış |
| 

#### <a name="miscellaneous"></a>Çeşitli

| Zenginleştirme adı | [Taban çizgisi](#baseline-explained) (gün) | Description | Örnek değer |
| --- | --- | --- | --- |
| **Kullanıcı eylemi son gerçekleştirmedi**<br>*(Lasttimeuserperformedadction)* | 180 | Kullanıcının aynı eylemi gerçekleştirdiği son zaman. | <Timestamp> |
| **Benzer eylem geçmişte gerçekleştirilmedi**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Kullanıcı tarafından aynı kaynak sağlayıcıda hiçbir eylem gerçekleştirilmedi. | Doğru, yanlış |
| **Kaynak IP konumu**<br>*(SourceIPLocation)* | *yok* | Ülke, eylemin kaynak IP 'sinden çözüldü. | [Surey, England] |
| **Yaygın olmayan yüksek işlem hacmi**<br>*(UncommonHighVolumeOfOperations)* | 7 | Kullanıcı aynı sağlayıcı içinde benzer işlemler için bir veri bloğu gerçekleştirdi | Doğru, yanlış |
| **Olağandışı sayıda Azure AD koşullu erişim başarısızlığı**<br>*(Unusualnumberofaadconditionalaccessarızaları)* | 5 | Koşullu erişim nedeniyle olağan dışı sayıda kullanıcı kimlik doğrulaması başarısız oldu | Doğru, yanlış |
| **Olağan dışı sayıda cihaz eklendi**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Kullanıcı olağan dışı sayıda cihaz ekledi. | Doğru, yanlış |
| **Olağan dışı sayıda cihaz silindi**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Kullanıcı olağan dışı sayıda cihazı sildi. | Doğru, yanlış |
| **Gruba eklenen olağan dışı sayıda Kullanıcı**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Kullanıcı bir gruba olağan dışı sayıda kullanıcı ekledi. | Doğru, yanlış |
|