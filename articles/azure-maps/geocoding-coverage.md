---
title: Microsoft Azure Maps arama hizmetindeki coğrafi kodlama kapsamı
description: Azure haritalar arama 'nın hangi bölgelerde ele alınmaktadır olduğunu görün. Coğrafi kodlama kategorileri adres noktalarını, ev numaralarını, sokak düzeyini, şehir düzeyini ve ilgilendiğiniz noktaları içerir.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/28/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3cf27319fa84ff5d693e74e90664b7a86f4d934a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897220"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure haritalar coğrafi kodlama kapsamı

Azure haritalar [Arama hizmeti](/rest/api/maps/search) , coğrafi kodlamayı destekler, bu da API isteğinizin bir adres ya da bir konumun adı gibi arama koşullarına sahip olabileceği ve sonucu Enlem ve boylam koordinatları olarak döndürdüğü anlamına gelir. Örneğin, Azure Maps [alma arama adresi API 'si](/rest/api/maps/search/getsearchaddress) , konum bilgilerini içeren sorguları alır ve sonuçları Enlem ve boylam koordinatları olarak döndürür.

Ancak, Azure haritalar [Arama hizmeti](/rest/api/maps/search) tüm bölgeler ve ülkeler için aynı düzeyde bilgi ve doğruluk düzeyine sahip değildir. Her bölgede güvenilir bir şekilde arama yapmak istediğiniz konum türlerini belirlemek için bu makaleyi kullanın.

Bir ülkede/bölgede kodlamayı özelliği, coğrafi kodlama hizmetinin yol verileri kapsamına ve coğrafi kodlama duyarlığına bağımlıdır. Aşağıdaki sınıflandırmalar, her ülkede/bölgede coğrafi kodlama desteğinin düzeyini belirtmek için kullanılır.

* Adres **noktaları** -adres verileri, adres ortasının (özellik sınırı) içindeki Enlem/Boylam koordinatlarına çözümlenebilirler. Adres noktaları genellikle, adresler için kullanılabilir en yüksek doğruluk düzeyi olan ' Rooftop ' doğru olarak adlandırılır.
* **Ev numaraları** -adresler cadde üzerinde bir enlem/boylam koordinatı ile yapılır.
* **Cadde düzeyi** -adresler, adresi içeren sokak 'nin Enlem/Boylam koordinatı olarak çözümlenir. Evin numarası işlenemiyor olabilir.
* **Şehir düzeyi** -şehir yeri adları desteklenir.

## <a name="americas"></a>Kuzey ve Güney Amerika

| Ülke/Bölge                                       | Adres noktaları | Ev numaraları | Cadde düzeyi | Şehir düzeyi | İlgi çekici noktaları |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktika                                          |                 |                |              |      ✓     |          ✓         |
| Antigua ve Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Arjantin                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamalar                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivya                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius ve Saba                   |                 |                |              |      ✓     |          ✓         |
| Brezilya                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cayman Adaları                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Şili                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Kolombiya                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosta Rika                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Küba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekvador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falkland Adaları                                    |                 |                |              |      ✓     |          ✓         |
| Fransız Guyanası                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaika                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Meksika                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Riko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthelemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts ve Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre ve Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent ve Grenadinler                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Güney Georgia ve Güney Sandwich Adaları        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad ve Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Birleşik Devletler küçük harici Adaları                |                 |                |              |      ✓     |          ✓         |
| Amerika Birleşik Devletler                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Britanya Virjin Adaları                              |                 |                |              |      ✓     |          ✓         |
| ABD Virgin Adaları                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asya Pasifik

| Ülke/Bölge                                      | Adres noktaları |Ev numaraları | Cadde düzeyi | Şehir düzeyi | İlgi çekici noktaları |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Amerikan Samoası                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Avustralya                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladeş                                          |                 |                |              |      ✓     |          ✓         |
| Butan                                              |                 |                |              |      ✓     |          ✓         |
| Britanya Hint Okyanusu Toprakları                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kamboçya                                            |                 |                |              |      ✓     |          ✓         |
| Çin                                               |                 |                |              |      ✓     |          ✓         |
| Christmas Adası                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cocos (Keeling) Adaları                             |                 |                |              |      ✓     |          ✓         |
| Komorlar                                             |                 |                |              |      ✓     |          ✓         |
| Cook Adaları                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Fransız Polinezyası                                    |                 |                |              |      ✓     |          ✓         |
| Heard Adası ve McDonald Adaları                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong ÖİB                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Endonezya                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Hindistan                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japonya                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Güney Kore                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Makao ÖİB                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malezya                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronezya                                          |                 |                |              |      ✓     |          ✓         |
| Moğolistan                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Yeni Kaledonya                                       |                 |                |              |      ✓     |          ✓         |
| Yeni Zelanda                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk Adası                                      |                 |                |              |      ✓     |          ✓         |
| Kuzey Kore                                         |                 |                |              |      ✓     |          ✓         |
| Kuzey Mariana Adaları                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Yeni Gine                                    |                 |                |              |      ✓     |          ✓         |
| Filipinler                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku Adaları                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Solomon Adaları                                     |                 |                |              |      ✓     |          ✓         |
| Güney Küıls                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tayvan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tayland                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks ve Caicos Adaları                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis ve Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Avrupa

| Ülke/Bölge                                      | Adres noktaları |Ev numaraları | Cadde düzeyi | Şehir düzeyi | İlgi çekici noktaları |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Arnavutluk                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ermenistan                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Avusturya                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaycan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belçika                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosna Hersek                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgaristan                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Belarus                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hırvatistan                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kıbrıs                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Çek Cumhuriyeti                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danimarka                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonya                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Faroe Adaları                                       |                 |                |              |      ✓     |          ✓         |
| Finlandiya                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Fransa                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gürcistan                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Almanya                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cebelitarık                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Yunanistan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grönland                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Macaristan                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| İzlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| İrlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Man Adası                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| İtalya                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakistan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosova                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kırgızistan                                          |                 |                |              |      ✓     |          ✓         |
| Letonya                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litvanya                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lüksemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kuzey Makedonya                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Karadağ                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hollanda                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norveç                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polonya                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portekiz                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| + Azor Adaları ve Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Romanya                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rusya Federasyonu                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sırbistan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovakya                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovenya                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| İspanya                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| İsveç                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| İsviçre                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tacikistan                                          |                 |                |              |      ✓     |          ✓         |
| Türkiye                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Türkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukrayna                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Birleşik Krallık                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Özbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Vatikan                                        |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="middle-east-and-africa"></a>Orta Doğu ve Afrika

| Ülke/Bölge                                      | Adres noktaları |Ev numaraları | Cadde düzeyi | Şehir düzeyi | İlgi çekici noktaları |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afganistan                                         |                 |                |              |      ✓     |          ✓         |
| Cezayir                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahreyn                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsvana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet Adası                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Orta Afrika Cumhuriyeti                            |                 |                |       ✓      |      ✓     |          ✓         |
| Çad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo Cumhuriyeti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Fildişi Sahili (Côte d'Ivoire)                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratik Kongo Cumhuriyeti                    |                 |                |       ✓      |      ✓     |          ✓         |
| Cibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Mısır                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ekvator Ginesi, Cumhuriyeti                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritre                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiyopya                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Fransız Güney Toprakları|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambiya                                              |                 |                |              |      ✓     |          ✓         |
| Gana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gine                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Gine-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| İran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| İsrail                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Ürdün                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuveyt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lübnan                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberya                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malavi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivler                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall Adaları                                    |                 |                |              |      ✓     |          ✓         |
| Moritanya                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Fas                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibya                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Nijer                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nijerya                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Umman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Helena                                        |                 |                |              |      ✓     |          ✓         |
| Suudi Arabistan                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seyşeller                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somali                                             |                 |                |              |      ✓     |          ✓         |
| Güney Afrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Güney Sudan                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Svaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Suriye                                               |                 |                |              |      ✓     |          ✓         |
| Sao Tome ve Principe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanya                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunus                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Birleşik Arap Emirlikleri                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Yemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambiya                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabve                                            |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar coğrafi kodlama hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Azure Haritalar Arama hizmeti](/rest/api/maps/search)