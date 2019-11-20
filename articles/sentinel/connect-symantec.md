---
title: Symantec IDX verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Symantec IDX verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 0250780c85041c07fabf7d5ed268d1f3cdb63e18
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240642"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDX gerecinizi bağlama 



Symantec IDX Connector, tüm Symantec güvenlik çözüm günlüklerinizi Azure Sentinel ile kolayca bağlamanıza olanak tanır, panoları görüntüleyebilir, özel uyarılar oluşturabilir ve araştırmayı geliştirebilirsiniz. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir. Symantec IDX ve Azure Sentinel arasındaki tümleştirme REST API kullanır.


> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx'i yapılandırın ve bağlayın 

Symantec IDX, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Microsoft Azure Sentinel (Log Analytics) ileticileri eklemek için ICDX yönetim konsolunu açın.
2. ICDX gezinti çubuğunda **yapılandırma**' ya tıklayın. 
3. **Yapılandırma** ekranının üst kısmında **ileticiler**' e tıklayın.
4. **İleticiler**altında Microsoft Azure Sentinel (Log Analytics) seçeneğinin yanındaki **Ekle**' ye tıklayın. 
4. **Microsoft Azure Sentinel (Log Analytics)** penceresinde **Gelişmiş göster**' e tıklayın. 
5. Microsoft Azure Sentinel (Log Analytics) penceresinin en üstünde şunları yapın:
    -   **Ad**: İletici için 30 karakterden uzun olmayan bir ad yazın. Benzersiz ve anlamlı bir ad seçin. Bu ad, **yapılandırma** ekranındaki ileticiler listesinde ve **Pano** ekranındaki panolar ' de görünür. Örneğin: Microsoft Azure Log Analytics Doğu. Bu alan gereklidir.
    -   **Açıklama**: İletici için bir açıklama yazın. Bu açıklama Ayrıca **yapılandırma** ekranındaki ileticiler listesinde de görüntülenir. İletimekte olan olay türü ve verileri incelemesi gereken grup gibi ayrıntıları dahil edin.
    -   **Başlangıç türü**: İletici yapılandırması için başlatma yöntemini seçin. Seçenekleriniz el ile ve otomatiktir.<br>Varsayılan değer otomatiktir. 
6. **Olaylar**' ın altında şunları yapın: 
    - **Kaynak**: Olayları iletmek için bir veya daha fazla arşiv seçin. Etkin toplayıcı arşivleri (ortak arşiv dahil), yalnız bırakılmış toplayıcı arşivleri (yani sildiğiniz toplayıcıların Arşivi), ICDX alıcı arşivleri veya sistem Arşivi arasından seçim yapabilirsiniz. <br>Varsayılan arşiv ortak arşividir.
      > [!NOTE]
      > ICDX alıcı arşivleri, ada göre ayrı olarak listelenir. 
 
    - **Filtre**: İletilmek üzere olayların alt kümesini belirten bir filtre ekleyin. Aşağıdakilerden birini yapın:
        - Bir filtre koşulu seçmek için, bir tür, öznitelik, Işleç ve değer ' e tıklayın. 
        - Filtre alanında, filtre koşullarınızı gözden geçirin. Bunu doğrudan alanda düzenleyebilir veya gerektiğinde silebilirsiniz.
        - Filtre koşullarınızı eklemek için ve veya ya tıklayın.
        - Kaydedilmiş sorgu uygulamak için kaydedilmiş sorgular ' a de tıklayabilirsiniz.
    - **Dahil edilen öznitelikler**: İletilen verilere dahil edilecek özniteliklerin virgülle ayrılmış listesini yazın. Dahil edilen öznitelikler hariç tutulan özniteliklere göre önceliklidir.
    - **Dışlanan öznitelikler**: İletilen verilerden dışlanacak özniteliklerin virgülle ayrılmış listesini yazın.
    - **Toplu Iş boyutu**: Toplu iş başına gönderilmek üzere olay sayısını seçin. Seçenekleriniz 10, 50, 100, 500 ve 1000.<br>Varsayılan değer 100 ' dir. 
    - **Hız sınırı**: Olayların iletildiği oranı (saniye başına olay olarak ifade edilir) seçin. Seçenekleriniz sınırsız, 500, 1000, 5000, 10000. <br> Varsayılan değer 5000 ' dir. 
7. **Azure hedefi**altında aşağıdakileri yapın: 
    - **Çalışma alanı kimliği**: Çalışma alanı KIMLIĞINI aşağıdan yapıştırın. Bu alan gereklidir.
    - **Birincil anahtar**: Birincil anahtarı aşağıdan yapıştırın. Bu alan gereklidir.
    - **Özel günlük adı**: Olayları ileteceklerinizi Microsoft Azure portal Log Analytics çalışma alanına özel günlük adını yazın. Varsayılan değer SymantecICDx ' dir. Bu alan gereklidir.
8. İletici yapılandırmasını son vermek için *Kaydet* ' e tıklayın. 
9. İleticiye başlamak için, **Seçenekler**altında, **daha fazla** ve ardından **Başlat**' a tıklayın.
10. Symantec IDX olayları için Log Analytics ilgili şemayı kullanmak için, **SymantecICDx_CL**aratın.


## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünene kadar 20 dakikadan bu kadar bir zaman çıkabilir. 



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Symantec IDX 'i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

