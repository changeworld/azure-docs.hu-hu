---
title: Webhook-előfizető hitelesítésének konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook-előfizetők hitelesítésének konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841730"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook-előfizetők hitelesítésének konfigurálása

Ez az útmutató példákat mutat be egy Event Grid modul lehetséges webhook-előfizetői konfigurációjában. Alapértelmezés szerint csak HTTPS-végpontok fogadhatók el a webhook-előfizetők számára. A Event Grid modul elutasítja, ha az előfizető önaláírt tanúsítványt ad meg.

## <a name="allow-only-https-subscriber"></a>Csak HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Saját aláírású tanúsítvánnyal rendelkező HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa be úgy a `outbound__webhook__allowUnknownCA` tulajdonságot, hogy csak tesztelési környezetben `true`, mivel általában önaláírt tanúsítványokat használ. Éles számítási feladatokhoz azt javasoljuk, hogy **hamis**értékre állítsa őket.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-előfizető engedélyezése, de tanúsítvány-ellenőrzés kihagyása

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Állítsa a `outbound__webhook__skipServerCertValidation` tulajdonságot úgy, hogy csak tesztelési környezetekben `true`, mivel előfordulhat, hogy nem fog hitelesíteni egy tanúsítványt. Éles számítási feladatokhoz ajánlott **Hamis értéket** beállítani

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>HTTP és HTTPS engedélyezése önaláírt tanúsítványokkal

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa be úgy a `outbound__webhook__httpsOnly` tulajdonságot, hogy csak tesztelési környezetekben `false`, mert előbb létre kell hoznia egy HTTP-előfizetőt. Éles számítási feladatokhoz ajánlott **igaz** értéket beállítani
