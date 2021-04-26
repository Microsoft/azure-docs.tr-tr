---
title: Konuşma fonetik kümeleri-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti fonetik alfabe haritalarını uluslararası fonetik alfabesinden (IPA) ve hangi küme ne zaman kullanacağınızı öğrenin.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 8e5480b7412291c427c5eaea94a63343f12c62cf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077525"
---
# <a name="speech-service-phonetic-sets"></a>Konuşma hizmeti fonetik kümeleri

Konuşma hizmeti, yedi dilden oluşan fonetik alfaberlerini (kısaca "telefon kümeleri") tanımlar. `en-US`, `fr-FR` , `de-DE` , `es-ES` , `ja-JP` , `zh-CN` ve `zh-TW` . Konuşma hizmeti telefon kümeleri genellikle <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Uluslararası Fonetik alfabesinden (IPA) </a>eşlenir. Konuşma hizmeti telefon kümeleri, metin okuma hizmeti sunumunun bir parçası olarak [konuşma sensumu biçimlendirme dili (SSML)](speech-synthesis-markup.md)ile birlikte kullanılır. Bu makalede, bu telefon kümelerinin nasıl eşleştirildiğini ve hangi telefon kümesinin ne zaman kullanılacağını öğreneceksiniz.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>İngilizce suprasegmentals

| Örnek 1 (ünsüz için OnSet, sesli harfler için ilk) | Örnek 2 (ünlüme için ıntersesli, Word, sesli harf için mus) | Örnek 3 (sessiz için Coda, sesli harf için son sözcük) | Yorumlar |
|--|--|--|--|
| Burger/b **1** r-g AX r/ | Falafel/f AX-l AA **1** -f AX l/ | gutar/g IH-t AA **1** r/ | Konuşma hizmeti telefon kümesi, Stle dili 'nin ünlüşinden sonra stres koy |
| inopportune/ih **2** -n AA-p AX r-t ODTÜ 1 n/ | debenzerlik/d IH-s ih **2**-m AX-l Eh 1-r AX-t iy/ | iş gücü/w 1 r k-f Ao **2** r s/ | Konuşma hizmeti telefon kümesi, alt-stler hecesi ünlübir noktadan sonra stres koy |

### <a name="english-vowels"></a>İngilizce sesli harfler

| `sapi` | `ipa` | Örnek 1     | Örnek 2 | Örnek 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **EA** t       | f **Ee** l  | Vall **Ey**                  |
| IH     | `ɪ`   | **i** f        | f **i** ll  |                             |
| Ey     | `eɪ`  | **bir** te       | g **a** y  | d **ay**                     |
| Eh     | `ɛ`   | **e** çok     | p **e** t   | d **Eh** (nadir sözcük finally) |
| AE     | `æ`   | **bir** Active alanı    | c **a** t   | n **Ah** (nadir sözcük finally) |
| aa     | `ɑ`   | **o** bstinate | p **o** PPY | r **Ah** (nadir sözcük finally) |
| Ao     | `ɔ`   | **o** aralığı    | c **au** | UT **Ah**                    |
| hata!     | `ʊ`   | b **Oo**      |           |                             |
| leştir     | `oʊ`  | **o** ld       | CL **&** lt | g **o**                      |
| ODTÜ     | `u`   | **U** ber      | b **Oo** | t **Oo**                     |
| Ah     | `ʌ`   | **u** NCLE     | c **u** t   |                             |
| h     | `aɪ`  | **i** CE       | b &**urum**  | fl **.**                     |
| Aw     | `aʊ`  | **OU** t       | s **OU** TH | c **ow**                     |
| oy     | `ɔɪ`  | **oI** l       | j **Oi** n  | t **oy**                     |
| y ODTÜ   | `ju`  | **Yu** ma      | h **u** Man | f **eni**                     |
| 'te     | `ə`   | **Go**       | Wok **a** n | **bir**                    |

### <a name="english-r-colored-vowels"></a>İngilizce R-renkli sesli harfler

| `sapi` | `ipa` | Örnek 1    | Örnek 2      | Örnek 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **Ear**     | t **IR** amisu   | n **Ear**   |
| Eh r   | `ɛɹ`  | **hava** düzlemi | uygulama **ar** | SC **ar** e  |
| hata! r   | `ʊɹ`  |              |                | c **'niz**   |
| ay r   | `aɪɹ` | **IRE** Land  | f **IR** eğiştir  | ch **OIR**  |
| AW r   | `aʊɹ` | **saat** s    | p **PIN** dolusu   | s   |
| Ao r   | `ɔɹ`  | **veya** Değiştir   | k **veya** al      | s **OAR**   |
| aa r   | `ɑɹ`  | **ar** tist   | St &**zar**      | c **ar**    |
| er r   | `ɝ`   | **kulak**    | b **IR** d       | f **'niz**    |
| AX r   | `ɚ`   |              | Tüm **öğeler** GY    | Supp **er** |

### <a name="english-semivowels"></a>İngilizce Semivowels

| `sapi` | `ipa` | Örnek 1           | Örnek 2  | Örnek 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **b**.,**UE** de | Al |           |
| y      | `j`   | **y**, f **e** w   | **ben** açık  |           |

### <a name="english-aspirated-oral-stops"></a>İngilizce aspidereceli sözlü duraklarının

| `sapi` | `ipa` | Örnek 1 | Örnek 2   | Örnek 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** UT   | ha **PP** en  | FLA **p**   |
| b      | `b`   | **b** IG   | NUM **b** er  | CRA **b**   |
| t      | `t`   | **t** alk  | CAPI **Al** | sough **t** |
| d      | `d`   | **d** IG   | ran **d** OM  | ro **d**    |
| k      | `k`   | **c** UT   | SLA **CK** | Ira **q**   |
| g      | `g`   | **g** o    | a **g** o     | DRA **g**   |

### <a name="english-nasal-stops"></a>İngilizce nasal duraklar

| `sapi` | `ipa` | Örnek 1        | Örnek 2  | Örnek 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **a** ve, parçalı   | CA **b** dönemi | Roo **m**    |
| n      | `n`   | **n** o, s **n** ow | te **n** t   | tavren **n** |
| denetimini     | `ŋ`   |                  | li **n** k   | s    |

### <a name="english-fricatives"></a>İngilizce fricatlar

| `sapi` | `ipa` | Örnek 1   | Örnek 2        | Örnek 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | Le **f** t         | Hal **f**   |
| v      | `v`   | **v** Eğer   | e **v** ENT        | Lo **v** e   |
| 11     | `θ`   | **' deki**    | Empa **TH**      | Mon **TH**  |
| değişiminden     | `ð`   | **TH**    | Mo **TH**       | smoo **TH** |
| s      | `s`   | **s** It     | RI **s** k         | olgu **s**  |
| z      | `z`   | **z** AP     | Bu **s** y         | Çocuk **öğeleri**   |
| dak     | `ʃ`   | **sh** e    | abbrevia **ti** on | ru **sh**   |
| zh     | `ʒ`   | **J** tanışlar | Plea **s** ure     | Gara **g** e |
| h      | `h`   | **h** ELP    | en **h** ance      | a-**h** a!  |

### <a name="english-affricates"></a>İngilizce

| `sapi` | `ipa` | Örnek 1 | Örnek 2    | Örnek 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch** ın  | Fu   | Atta **ch** |
| JH     | `dʒ`  | **j** oy   | Ori **g** inal | oran **g** e |

### <a name="english-approximants"></a>İngilizce approkanlar

| `sapi` | `ipa` | Örnek 1          | Örnek 2  | Örnek 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** kimliği, g **l** ad  | PA **l** Ace | Chi **ll** |
| r      | `ɹ`   | **r** Ed, b **r** | Bo **RR** ow | ta **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Fransız suprasegmentals

Konuşma hizmeti telefon kümesi, devam eden harften sonra stres koyar; ancak `fr-FR` konuşma hizmeti telefon seti, IPA alt stres ' ˌ ' özelliğini desteklemiyor. IPA alt stres gerekliyse, IPA doğrudan kullanmanız gerekir.

### <a name="french-vowels"></a>Fransızca sesli harfler

| `sapi` | `ipa` | Örnek 1     | Örnek 2       | Örnek 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **bir** rbre     | p **a** TTE       | **a** IR   |
| aa     | `ɑ`   |               | p **â** te        | p **a** s   |
| aa ~   | `ɑ̃`  | **en** Fant    | ENF verilmeyecektir **en** t      | t **em** PS |
| 'te     | `ə`   |               | p **e** Tite      | l **e**    |
| Eh     | `ɛ`   | **e** lle      | p **e** RDU       | éT **AI** t |
| yapılan     | `ø`   | Her **u** FS      | CR **AB** ser     | Qu **AB**  |
| Ey     | `e`   | ému           | crétın          | ôté       |
| Eh ~   | `ɛ̃`  | **anlık ileti** bağlantı noktası | p **Ein** ture    | Vade |
| iy     | `i`   | **i** dée      | Evcil **hayvan**      | **i**   |
| gezi     | `œ`   | \ **u** f       | p **AB** r        |           |
| Tamamdır     | `ɔ`   | **o** bstacle  | c **o** RPS       |           |
| Oh ~   | `ɔ̃`  | **ze**      | deur **üzerinde** r     | b **Açık**   |
| leştir     | `o`   | **au** diteur  | b **Eau** Coup    | p **ô**    |
| OE ~   | `œ̃ ` | **alma**        | l **un** di       | br **Kaldır**  |
| ODTÜ     | `u`   | **OU** Trage   | INTR **OU** kaydedilebilir | **'sunu**    |
| uy     | `y`   | **u** ne       | p **u** nir       | él **u**   |

### <a name="french-consonants"></a>Fransız ünsüzler

| `sapi` | `ipa` | Örnek 1   | Örnek 2     | Örnek 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | ha **b** ille   | ro **b** e                         |
| d      | `d`   | **d** ire    | Ron **d** EUR   | Chau **d** e                       |
| f      | `f`   | **f** emme   | su **FF** IXE   | Bo **f**                          |
| g      | `g`   | **g** auche  | é **g** Ale     | ba **Gu** e                        |
| denetimini     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)Park **etme** |
| hy     | `ɥ`   | h **u** ile   | n **u** ire     |                                  |
| k      | `k`   | **c** Arte   | é **c** aille   | **c** olarak                          |
| l      | `l`   | **l** Ong    | é **l** ire     | ba **l**                          |
| m      | `m`   | **b** adama  | AI bir **a**     | Po **mm** e                        |
| n      | `n`   | **n** OU    | te **n** IR     | Bo **nn** e                        |
| NJ     | `ɲ`   |             |               | Pei **GN** e                       |
| p      | `p`   | **p** Atte   | **p** olarak yeniden dene     | CA **p**                          |
| r      | `ʁ`   | **r** at     | Cha **r** IoT   | senti **r**                       |
| s      | `s`   | **s** ourir  | bir **SS** EZ     | PA **SS** e                        |
| dak     | `ʃ`   | **ch** anter | Ma **ch**   | Po **ch**                        |
| t      | `t`   | **t** ête    | ô **t** er      | ne **s**                          |
| v      | `v`   | **v** ENT    | **v**'ye gir  | Rê **v** e                         |
| w      | `w`   | **OU** ı     | f **OU 'su**    |                                  |
| y      | `j`   | **y** od     | p **ekibi**  | Maro **ille**                    |
| z      | `z`   | * * z * * éro   | çi **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** ardın  | Man **g** a    | piè **g** e                        |
|        | `n‿`  |             |               | u **n** Arbre                     |
|        | `t‿`  |             |               | qua **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *yalnızca bazı yabancı sözcükler için.*

> [!TIP]
> `fr-FR`Konuşma hizmeti telefon seti, aşağıdaki Fransızca,, ve ' ı desteklemez `n‿` `t‿` `z‿` . Gerekirse, IPA doğrudan kullanmayı göz önünde bulundurmanız gerekir.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Almanya suprasegmentals

| Örnek 1 (ünsüz için OnSet, sesli harfler için ilk) | Örnek 2 (ünlüme için ıntersesli, Word, sesli harf için mus) | Örnek 3 (sessiz için Coda, sesli harf için son sözcük) | Yorumlar |
|--|--|--|--|
| Anders/a **1** n-b AX r s/ | Multiplikationszeichen/m hata! l-t iy-p l iy-k a-TS y ow **1** n s-TS ay-c n/ | Biologie/b iy-ow-l ow-g Iy **1**/ | Konuşma hizmeti telefon kümesi, Stle dili 'nin ünlüşinden sonra stres koy |
| Allgemeınwısen/a **2** l-g AX-m ay 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ Eh n t-z Oh **2** AX r-g hata! ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y ODTÜ 1-t AX r-t ow-m ow-g r a-f iy **2**/ | Konuşma hizmeti telefon kümesi, alt-stler hecesi ünlübir noktadan sonra stres koy |

### <a name="german-vowels"></a>Almanca sesli harfler

| `sapi` | `ipa`     | Örnek 1                             | Örnek 2     | Örnek 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a     | `aː`      | **Ber**                              | B Maßst **a**   | **Bir a** düzeni                         |
| a      | `a`       | **Bir** Bfall                            | B **a** ch      | Agath **a**                         |
| Tamamdır     | `ɔ`       | **O** Sten                             | PF **o** Sten   |                                    |
| Eh    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) Fasci **AE** |
| Eh     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | Amygdal **AE**                      |
| 'te     | `ə`       | [<sup>2</sup>](#de-v-2)' v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **Çalıştırdım**                              | ABB **ie** gt   | Relativitätstheor **ie**            |
| IH     | `ɪ`       | **I** nnung                            | dakikada **bir** Ngen    | Wood **y**                          |
| yapılan     | `øː`      | **Ö** sen                              | ABL **ö** Sten  | Mala **ö**                          |
| leştir     | `o`, `oː` | **o** hnE dili                              | Balk **o** n    | Tyinele **ow**                        |
| gezi     | `œ`       | **Ö** ffnung                           | BEF **ö** rdern |                                    |
| Ey     | `e`, `eː` | **E** bersert                          | ABF **e** gt    | b                                  |
| ODTÜ     | `uː`      | **U** do                               | H **u** t       | Akk **u**                           |
| hata!     | `ʊ`       | **U** nterschıedes                     | b **u** NT      |                                    |
| noktası     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Men **ü**                           |
| uy     | `ʏ`       | **ü** ppıg                             | S **y** gövdesi    |                                    |

<a id="de-v-1"></a>
**1** *yalnızca yabancı kaynak kelimelerde, örneğin: fasci **AE**.*<br>
<a id="de-v-2"></a>
**2** *sözcük-yalnızca ppointe gibi yabancı kaynak kelimelerde kullanıma açılır. İlk başta: ' v **e** rstauen dili.*

### <a name="german-diphthong"></a>Almanya diphthong

| `sapi` | `ipa`       | Örnek 1    | Örnek 2          | Örnek 3 |
|--------|-------------|--------------|--------------------|-----------|
| h     | `ai`        | **ei** nsam   | Unabhängigk **eç** | Abt **ei** |
| Aw     | `au`        | **au** ßen    | ABB **au** St        | St **au**  |
| oy     | `ɔy`, `ɔʏ̯` | **AB** phorie | tr **äu** MT         | SCH **AB** |

### <a name="german-semivowels"></a>Almanya semivowels

| `sapi` | `ipa` | Örnek 1 | Örnek 2    | Örnek 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **er** n | kilidi **Kilitle** |

### <a name="german-consonants"></a>Almanya ünsüzler

| `sapi` | `ipa` | Örnek 1 | Örnek 2 | Örnek 3 |
|--|--|--|--|--|
| b | `b` | **B** ank | | [<sup>1</sup>](#de-c-1) PU **b** | 
| c | `ç` | **Ch** Emie | mögli **ch** St | [<sup>2</sup>](#de-c-2)ı **ch** |
| d | `d` | **d** anken | [<sup>3</sup>](#de-c-3) Len **d** l | [<sup>4</sup>](#de-c-4) Clau **d** e | 
| JH | `ʤ` | **J** eff | gemana **g** t | [<sup>5</sup>](#de-c-5) **G**/ç |
| f | `f` | **F** ahrtdauer | Angri **FF** slustig | abbruchrei **f** |  
| g | `g` | **g**. |  [<sup>6</sup>](#de-c-6) GRE **g** |  |
| h | `h` | **H** ausanbau |  |  | 
| y | `j` | **J** od | Reakt | Hu **ı** | 
| k | `k` | **K** OMA | Aspe **k** t | Flec **k** | 
| l | `l` | **l** au | ähne **l** n | zugörüntüle **l** | 
| m | `m` | **M** UT | Bir **a** t | Tah **m** | 
| n | `n` | **n** kaldır | u **n** d | Kuh **n** | 
| denetimini | `ŋ` | [<sup>7</sup>](#de-c-7)**ng** Uygurca | Schwa **NK** | R **ing** | 
| p | `p` | **P** artner | abru **p** t | TI **p** | 
| PF | `pf` | **PF** erd | Dam **PF** t | **PF** 'ye |
| r | `ʀ`, `r`, `ʁ` | **R** eIse | KNU **RR** t | Haa **r** | 
| s | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | bı **s** t | MIE **s** | 
| dak | `ʃ` | **SCH** Ule | mı **SCH** t | Lappi **SCH** | 
| t | `t` | **T** oyum | S **t** raße | Mu **t** | 
| Talar | `ts` | **Z** UG | AR **z** t | WIT **z** | 
| ch | `tʃ` | **TSch** echien | aufgepu **TSch** t | bdesdeu **TSch** | 
| v | `v` | **w** Inken | Q **u** Alle | [<sup>9</sup>](#de-c-9) Gr **Oo** ve | 
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba **ch** silinebilir | Ma **mögli****ch** St | Schma **ch** ' ı **ch** |
| z | `z` | **s** UPA |  |  | 
| zh | `ʒ` | **G** enre | B **Re** ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *yalnızca yabancı kaynak kelimelerde, örneğin: PU **b**.*<br>
<a id="de-c-2"></a>
 *"e" ve "i" sonra 2 yumuşak "CH"*<br>
<a id="de-c-3"></a>
**3** *yalnızca yabancı kaynak sözcüklerde, örneğin: Len **d** l.*<br>
<a id="de-c-4"></a>
**4** *yalnızca yabancı kaynak kelimelerde: Clau **d** e gibi.*<br>
<a id="de-c-5"></a>
**5** *yalnızca yabancı kaynak sözcüklerde, örneğin: Kanal.*<br>
<a id="de-c-6"></a>
**6** *sözcük-yalnızca GRE **g** gibi yabancı kaynak sözcüklerde sonlandırın.*<br>
<a id="de-c-7"></a>
**7** *yalnızca yabancı kaynak kelimelerde: **ng** Uygurca.*<br>
<a id="de-c-8"></a>
**8** *yalnızca yabancı kaynak kelimelerde, örneğin: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *yalnızca yabancı kaynak sözcüklerde, örneğin: gr **Oo** ve.*<br>
<a id="de-c-10"></a>
**10** *`x` ön uç olmayan tüm sesli harfler (a, aa, Oh, ow, uh, ODTÜ ve diphthong AW) sonrasında IPA sabit bir "CH" dır.*<br>
<a id="de-c-11"></a>
**11** *IPA, `ç` ön sesli bir ' CH ' (IH, iy, Eh, AE, uy, UE, OE, eu de Diphthongs ay, oy) ve ünsüzler*<br>
<a id="de-c-12"></a>
**12** *sözcük başlangıçta yalnızca yabancı kaynak kelimesiyle, örneğin: **J** Uan. Dili-başlangıçta da şu şekilde kelimeyle: ba **ch** silinebilir.*<br>

### <a name="german-oral-consonants"></a>Alman sözlü ünsüzler

| `sapi` | `ipa` | Örnek 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b ax-^ a 1 x t-l IH c/ |

> [!NOTE]
> İki \] sesli harfler orijinal bir diphthong dışında iki ayrı sesli bir [GS telefonu eklememiz gerekiyor. Bu sözlü, daha fazla bilgi için bkz <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> . gırtstop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[ES-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>İspanyolca sesli harfler

| `sapi` | `ipa` | Örnek 1    | Örnek 2     | Örnek 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **bir** LTO     | c **a** ntar    | CA **a**     |
| ı      | `i`   | **ben** bérika  | Av **ı** Spa    | Vergi **ı**     |
| e      | `e`   | **e** Solante | **e-** nsaat    | Elefant **e** |
| o      | `o`   | **o** Caso    | ENC **o** ntrar | ocasenc **o** |
| u      | `u`   | **Artırılmış**    | p **u** NTA     | Juanl **u**   |

### <a name="spanish-consonants"></a>İspanyolca ünsüzler

| `sapi` | `ipa`      | Örnek 1  | Örnek 2      | Örnek 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | **b** .        |
|        | `β`        |            | Bao **b** AB     | baoba **b**     |
| ch     | `tʃ`       | **ch** eque | ortak **ch**      | Markik **ch** |
| d      | `d`        | **d** Edo dili   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | Verda **d**     |
| f      | `f`        | **f** ácil  | ele **f** Ante   | PU **f**        |
| g      | `g`        | **g** angası  |                | dópin **g**     |
|        | `ɣ`        |            | a **g** UA       | Tug **g**     |
| uygulanmaz      | `j`        | **g** Odo   | Cal **'yi** oluştur   | yeniden **y**        |
| JJ     | `j.j` `jj` |            | Vi **a**      |                |
| k      | `k`        | **c** Oche  | Bo **c** a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | a **l** a        | Corde **l**     |
| ceğiz     | `ʎ`        | **Tümünü** Ave  | desarro **ll** o |                |
| m      | `m`        | **a** sırası | bir **a** ar       | álbu      |
| n      | `n`        | **n** ada   | CE **n** a       | rató **n**      |
| NJ     | `ɲ`        | **ñ** abir   | Ara **ñ** azo    |                |
| p      | `p`        | **p** Oca   | **p** o 'a       | STO **p**       |
| r      | `ɾ`        |            | CA **r** a       | ABRI **r**      |
| RR     | `r`        | **r** Adio  | ortak **RR** e      | PU **RR**       |
| s      | `s`        | **s** ACO   | VA **s** o       | pelo **'lar**      |
| t      | `t`        | **t** oldo  | **t** ar       | deque    |
| 11     | `θ`        | **z** Ebra  | bir **z** ul       | lápi **z**      |
| w      | `w`        | h **u** ESO  | AG **u** a       | şapkası **u**       |
| x      | `x`        | **j** OTA   | bir **j** o        | rElo **j**      |

> [!TIP]
> `es-ES`Konuşma hizmeti telefon seti, aşağıdaki Ispanyolca IPA,, ve ' yi desteklemez `β` `ð` `ɣ` . Gerekirse, IPA doğrudan kullanmayı göz önünde bulundurmanız gerekir.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

İçin konuşma hizmeti telefon kümesi, `zh-CN` yerel telefon <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin </a> kümesini temel alır.

### <a name="tone"></a>Ton

| Pinyin tonu | `sapi` | Karakter örneği |
|-------------|--------|-------------------|
| mā          | MA 1  | 妈                 |
| má          | MA 2  | 麻                 |
| mǎ          | Ma 3  | 马                 |
| mà          | Ma 4  | 骂                 |
| hareketli          | Ma 5  | 嘛                 |

#### <a name="example"></a>Örnek

| Karakter | Konuşma hizmeti                |
|-----------|-------------------------------|
| 组织关系      | Zu 3-Zhi 1-Gua 1-Xi 5 |
| 累进        | TAI 3-Jin 4                 |
| 西宅巷       | Xi 1-Zhai 2-Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

İçin konuşma hizmeti telefon kümesi, `zh-TW` Yerel telefonun <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo </a> kümesini temel alır.

### <a name="tone"></a>Ton

| Konuşma hizmeti tonu | Bopomofo tonu | Örnek (sözcük) | Konuşma hizmeti telefonları | Bopomofo | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | yǐng Zi    |

#### <a name="example"></a>Örnek

| Karakter | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

İçin konuşma hizmeti telefon kümesi, `ja-JP` yerel telefon <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">kana </a> kümesini temel alır.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` Alt stres  |

#### <a name="example"></a>Örnek

| Karakter | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Go ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***
