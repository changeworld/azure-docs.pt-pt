---
title: Configure webhook autenticação de assinante - Azure Event Grid IoT Edge [ Microsoft Docs
description: Configurar a autenticação de subscritor do webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841735"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurar a autenticação de subscritor do webhook

Este guia dá exemplos das possíveis configurações de subscritores do webhook para um módulo De Rede de Eventos. Por predefinição, apenas são aceites pontos finais HTTPS para assinantes webhook. O módulo Event Grid rejeitará se o assinante apresentar um certificado auto-assinado.

## <a name="allow-only-https-subscriber"></a>Permitir apenas subscritor HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Permitir assinante HTTPS com certificado auto-assinado

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
>Desloque a propriedade `outbound__webhook__allowUnknownCA` apenas em `true` ambientes de teste, uma vez que normalmente pode usar certificados auto-assinados. Para cargas de trabalho de produção recomendamos que sejam configuradas como **falsas**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Permitir o assinante HTTPS, mas não a validação de certificados de salto

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
>Desloque a propriedade `outbound__webhook__skipServerCertValidation` apenas em `true` ambientes de teste, uma vez que poderá não apresentar um certificado que precisa de ser autenticado. Para cargas de trabalho de produção recomendamos que sejam definidos para **falsos**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Permitir http e HTTPS com certificados auto-assinados

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
>Detete `outbound__webhook__httpsOnly` `false` a propriedade apenas em ambientes de teste, pois você pode querer trazer um assinante HTTP primeiro. Para cargas de trabalho de produção recomendamos que sejam definidos como **verdadeiros**
