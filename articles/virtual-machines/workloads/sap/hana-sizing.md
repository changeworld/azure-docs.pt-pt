---
title: Dimensionamento de SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Dimensionamento de SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616897"
---
# <a name="sizing"></a>Dimensionamento

Dimensionar para HANA Large Instance não é diferente do tamanho para HANA em geral. Para sistemas existentes e implantados que pretende mover de outros RDBMS para HANA, o SAP fornece uma série de relatórios que funcionam nos seus sistemas SAP existentes. Se a base de dados for transferida para HANA, estes relatórios verificam os dados e calculam os requisitos de memória para a instância HANA. Para obter mais informações sobre como executar estes relatórios e obter os seus patches ou versões mais recentes, leia as seguintes Notas SAP:

- [SAP Note #1793345 - Dimensionamento para Suíte SAP em HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Nota SAP #1872170 - Suite em HANA e Relatório de tamanho S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Nota SAP #2121330 - FAQ: SAP BW no relatório de tamanhos HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Nota #1736976 - Relatório de dimensionamento para a BW sobre HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Nota #2296290 - Novo relatório de tamanhos para a BW sobre HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde, o SAP Quick Sizer está disponível para calcular os requisitos de memória da implementação do software SAP em cima da HANA.

Os requisitos de memória para HANA aumentam à medida que o volume de dados aumenta. Esteja atento ao seu consumo de memória atual para ajudá-lo a prever o que vai ser no futuro. Com base nos requisitos de memória, pode então mapear a sua procura numa das SKUs de Grande Instância HANA.

**Passos seguintes**
- Consulte [os requisitos de embarque](hana-onboarding-requirements.md)