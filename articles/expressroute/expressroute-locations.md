---
title: 'Fournisseurs de connectivité et emplacements : Azure ExpressRoute | Microsoft Docs'
description: Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure. Trié par fournisseur de connectivité.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 3f29b308f4d0f198d444d874a4f53cf660feb8f9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136176"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>Partenaires de connectivité ExpressRoute et localisations de peering

> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md)


Les tableaux de cet article fournissent des informations sur la couverture géographique et les emplacements ExpressRoute, les fournisseurs de connectivité ExpressRoute et les intégrateurs système ExpressRoute.

> [!Note]
> Les régions Azure et les emplacements ExpressRoute sont deux concepts distincts et différents, il est essentiel de comprendre ce qui les distingue pour explorer la connectivité réseau hybride Azure. 
>
>

## <a name="azure-regions"></a>Régions Azure
Les régions Azure sont des centres de données globaux où se trouvent des ressources de stockage, de mise en réseau et de calcul Azure. Lors de la création d’une ressource Azure, un client doit sélectionner un emplacement de ressource. L’emplacement de la ressource détermine le centre de ressources Azure (ou zone de disponibilité) dans lequel la ressource est créée.

## <a name="expressroute-locations"></a>Emplacements ExpressRoute
Les emplacements ExpressRoute (parfois appelés emplacements de peering ou emplacements de rencontre) sont des installations de colocalisation où se trouvent des appareils Microsoft Enterprise Edge (MSEE). Les emplacements ExpressRoute sont le point d’entrée du réseau de Microsoft, et sont distribués à l’échelle mondiale, ce qui offre aux clients la possibilité de se connecter au réseau Microsoft dans le monde entier. Ces emplacements sont là où les partenaires ExpressRoute et les clients ExpressRoute Direct émettent des connexions croisées au réseau de Microsoft. En général, l’emplacement ExpressRoute n’a pas besoin de correspondre à la région Azure. Par exemple, un client peut créer un circuit ExpressRoute avec l’emplacement de ressource *USA Est*, dans l’emplacement de peering de *Seattle*.

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique.

[!INCLUDE [expressroute-azure-regions-geopolitical-region](../../includes/expressroute-azure-regions-geopolitical-region.md)]

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Fournisseurs de connectivité ExpressRoute

Le tableau suivant présente les emplacements, en fonction de chaque fournisseur de services. Si vous souhaitez afficher les fournisseurs disponibles par emplacement, consultez la liste des [fournisseurs de services par emplacement](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Production Azure commerciale mondiale

| **Fournisseur de services** | **Microsoft Azure** | **Microsoft 365**  | **Emplacements** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Prise en charge |Prise en charge |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Prise en charge | Prise en charge | Chennai2, Mumbai2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Prise en charge | Prise en charge | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Prise en charge |Prise en charge |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapour, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Prise en charge |Pris en charge | Campinas, Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Prise en charge |Prise en charge |Amsterdam, Chicago, Dallas, Francfort, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **[À TOKYO](https://www.attokyo.com/connectivity/azure.html)** | Prise en charge | Prise en charge | Osaka, Tokyo2 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/bics-cloud-connect-an-official-microsoft-azure-technology-partner/)** | Prise en charge | Prise en charge | Amsterdam2, Londres2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Prise en charge | Prise en charge | Osaka, Tokyo |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Prise en charge |Prise en charge |Le Cap, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Prise en charge |Pris en charge |Montréal, Toronto, Québec, Vancouver |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Prise en charge |Prise en charge |Amsterdam, Amsterdam2, Chicago, Francfort, Hong Kong (R.A.S.), Johannesbourg, Londres, Londres2, Newport(Pays de Galles), Paris, Sao Paulo, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[BSNL](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Prise en charge |Prise en charge |Chennai, Mumbai |
| **[C3ntro](https://www.c3ntro.com/)** |Prise en charge |Prise en charge |Miami |
| **CDC** | Prise en charge | Prise en charge | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Prise en charge |Pris en charge |Amsterdam2, Chicago, Dublin, Francfort, Hong Kong, Las Vegas, Londres, Londres2, New York, Paris, San Antonio, Silicon Valley, Singapour2, Tokyo, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |Prise en charge |Prise en charge |Hong Kong (R.A.S.), Taipei |
| **China Mobile International** |Prise en charge |Prise en charge | Hong Kong (R.A.S.), Hong Kong2, Singapour |
| **China Telecom Global** |Prise en charge |Prise en charge |Hong Kong (R.A.S.), Hong Kong2 |
| **[China Unicom Global](https://cloudbond.chinaunicom.cn/home-en)** |Pris en charge |Pris en charge | Hong Kong, Singapore2, Tokyo2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Prise en charge |Prise en charge |Taipei |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Prise en charge |Prise en charge |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Prise en charge |Prise en charge |Chicago, Dallas, Minneapolis, Montréal, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Prise en charge |Pris en charge |Amsterdam, Amsterdam2, Berlin, Chicago, Dublin, Francfort, Genève, Hong Kong (RAS), Londres, Londres2, Marseille, Milan, Munich, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapour2, Tokyo, Washington DC, Zurich |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Prise en charge |Prise en charge |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Prise en charge |Pris en charge |Chicago, Chicago2, Denver, Los Angeles, New York, Silicon Valley, Silicon Chicago2, Washington DC, Washington Valley2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Prise en charge |Prise en charge |Amsterdam2, Dubai2, Frankfurt, Marseille, Mumbai, Munich, New York |
| **[Devoli](https://devoli.com/expressroute)** | Prise en charge |Prise en charge | Auckland, Melbourne, Sydney |
| **[Deutsche Telekom AG IntraSelect](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | Prise en charge |Prise en charge |Francfort |
| **[Deutsche Telekom](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | Prise en charge |Prise en charge |Frankfurt2 |
| **du datamena** |Prise en charge |Prise en charge | Dubaï2 |
| **eir** |Prise en charge |Prise en charge |Dublin|
| **[Communications globales EPSILON](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Prise en charge |Prise en charge |Singapour, Singapour2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Prise en charge |Pris en charge |Amsterdam, Amsterdam2, Atlanta, Berlin, Bogota, Canberra2, Chicago, Dallas, Dubaï2, Dublin, Francfort, Francfort2, Genève, Hong Kong (R.A.S), Londres, Londres2, Los Angeles*, Los Angeles2, Melbourne, Miami, Milan, New York, Osaka, Paris, Québec, Rio de Janeiro, São Paulo, Seattle, Séoul, Silicon Valley, Singapour, Singapour2, Stockholm, Sydney, Tokyo, Toronto, Washington DC, Zurich</br></br> **Les nouveaux circuits ExpressRoute ne sont plus pris en charge avec Equinix à Los Angeles. Créez de nouveaux circuits dans Los Angeles2.* |
| **Etisalat UAE** |Prise en charge |Prise en charge |Dubaï|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Prise en charge |Prise en charge |Amsterdam, Amsterdam2, Dublin, Francfort, Londres |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Prise en charge |Prise en charge |Taipei|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Prise en charge |Prise en charge |Milan|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Prise en charge |Prise en charge |Montréal|
| **[GBI](https://www.gbiinc.com/microsoft-azure/)** |Pris en charge |Pris en charge |Dubai2, Frankfurt|
| **[GÉANT](https://www.geant.org/Networks)** |Prise en charge |Prise en charge |Amsterdam, Amsterdam2, Dublin, Francfort, Marseille |
| **[GlobalConnect](https://www.globalconnect.no/tjenester/nettverk/cloud-access)** | Prise en charge |Prise en charge | Oslo, Stavanger | 
| **GTT** |Prise en charge |Prise en charge |Londres2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Prise en charge| Prise en charge | Chennai, Mumbai |
| **[iAdvantage](https://www.scx.sunevision.com/)** | Prise en charge | Prise en charge | Hong Kong2 |
| **Intelsat** | Prise en charge | Prise en charge | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Prise en charge |Prise en charge |Amsterdam, Chicago, Francfort, Hong Kong, Londres, New York, Paris, Silicon Valley, Singapour, Tokyo, Washington DC, Zurich |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Prise en charge |Prise en charge |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Prise en charge |Prise en charge |Osaka, Tokyo |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Prise en charge |Prise en charge |Le Cap, Johannesburg, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Prise en charge |Pris en charge |Amsterdam, Amsterdam2, Copenhague, Dublin, Dublin2, Francfort, Londres, Madrid, Marseille, Paris, Zurich |
| **[IRIDEOS](https://irideos.it/)** |Prise en charge |Prise en charge |Milan |
| **Iron Mountain** | Prise en charge |Prise en charge |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Prise en charge |Prise en charge | Amsterdam, London2, Silicon Valley, Toronto, Washington DC |
| **Jaguar Network** |Prise en charge |Prise en charge |Marseille, Paris |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Prise en charge |Prise en charge |Londres, Newport (Pays de Galles) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |Prise en charge |Prise en charge |Séoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Prise en charge |Prise en charge |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Prise en charge | Prise en charge | Amsterdam |
| **[KT](https://cloud.kt.com/)** | Prise en charge | Prise en charge | Séoul |
| **[Level 3 Communications](https://www.lumen.com/en-us/hybrid-it-cloud/cloud-connect.html)** |Prise en charge |Prise en charge |Amsterdam, Chicago, Dallas, Londres, Newport (Pays de Galles), Sao Paulo, Seattle, Silicon Valley, Singapour, Washington DC |
| **LG CNS** |Prise en charge |Prise en charge |Busan, Séoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Prise en charge |Prise en charge |Le Cap, Johannesburg |
| **[LGUplus](http://www.uplus.co.kr/)** |Pris en charge |Prise en charge |Séoul |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Prise en charge |Pris en charge |Amsterdam, Atlanta, Auckland, Chennai, Chicago, Dallas, Denver, Dubaï2, Dublin, Francfort, Genève, Hong Kong, Hong Kong2, Las Vegas, Londres, Londres2, Los Angeles, Madrid, Melbourne, Miami, Minneapolis, Montréal, Munich, New York, Osaka, Oslo, Paris, Perth, Phoenix, Québec, San Antonio, Seattle, Silicon Valley, Singapour, Singapour2, Stavanger, Stockholm, Sydney, Sydney2, Tokyo, Tokyo2, Toronto, Vancouver, Washington DC, Washington DC2, Zurich |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Prise en charge |Prise en charge |London |
| **MTN Global Connect** |Pris en charge |Pris en charge |Le Cap, Johannesburg|
| **[National Telecom](https://www.nc.ntplc.co.th/cat/category/264/855/CAT+Direct+Cloud+Connect+for+Microsoft+ExpressRoute?lang=en_EN)** |Prise en charge |Prise en charge |Bangkok |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Prise en charge |Prise en charge |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |Prise en charge |Prise en charge |Newport (Nouvelle-Galles du Sud) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Prise en charge |Prise en charge |Melbourne, Perth, Sydney, Sydney2 |
| **[NOS](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Prise en charge |Prise en charge |Amsterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Prise en charge |Prise en charge |Amsterdam, Hong Kong (R.A.S.), Londres, Los Angeles, Osaka, Singapour, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Prise en charge |Prise en charge |Tokyo |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Prise en charge |Pris en charge |Amsterdam2, Berlin, Francfort, Londres2 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Prise en charge |Prise en charge |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.com.kw/portal/en/b2bOffConnAzureExpressRoute)** |Prise en charge |Prise en charge |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Prise en charge |Prise en charge |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Prise en charge |Prise en charge |Amsterdam, Amsterdam2, Dubai2, Francfort, Hong Kong (R.A.S), Johannesbourg, Londres, Paris, São Paulo, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Prise en charge | Prise en charge | Dubaï2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Prise en charge |Prise en charge |Chicago, Dallas, Denver, Las Vegas, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Prise en charge |Prise en charge |Chicago, Hong Kong (R.A.S), Hong Kong2, Londres, Singapour2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Prise en charge | Prise en charge | Auckland |
| **[Dépendance Jio](https://www.jio.com/business/jio-cloud-connect)** | Pris en charge | Prise en charge | Mumbai |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Prise en charge | Prise en charge | Milan | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Prise en charge |Prise en charge |Séoul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Prise en charge |Prise en charge | Londres2, Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Prise en charge |Prise en charge |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Prise en charge |Prise en charge |Hong Kong2, Singapour, Singapour2 |
| **[SK Telecom](http://b2b.tworld.co.kr/bizts/solution/solutionTemplate.bs?solutionId=0085)** |Prise en charge |Prise en charge |Séoul |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Prise en charge |Prise en charge |Osaka, Tokyo |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Prise en charge |Prise en charge |Londres2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Prise en charge |Prise en charge |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Prise en charge |Prise en charge |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Prise en charge | Prise en charge | Genève, Zurich |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |Prise en charge |Prise en charge |Amsterdam, Chennai, Hong Kong (R.A.S.), Londres, Mumbaï, Sao Paulo, Silicon Valley, Singapour, Washington DC |
| **[Telefonica](https://www.telefonica.com/es/home)** |Prise en charge |Prise en charge |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Prise en charge |Pris en charge |London, London2, Singapore2, Tokyo |
| **Telenor** |Prise en charge |Prise en charge |Amsterdam, Londres, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Prise en charge | Prise en charge |Amsterdam, Chicago, Dallas, Francfort, Hong Kong, Londres, Oslo, Paris, Silicon Valley, Stockholm, Washington DC |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Prise en charge | Prise en charge |Jakarta |
| **Telmex Uninet**| Prise en charge | Prise en charge | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Prise en charge |Prise en charge |Melbourne, Singapour, Sydney |
| **[Telus](https://www.telus.com)** |Prise en charge |Prise en charge |Montréal, Seattle, Québec, Toronto, Vancouver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Prise en charge |Prise en charge |Le Cap, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Prise en charge | Prise en charge | Kuala Lumpur |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | Prise en charge | Prise en charge | Osaka, Tokyo2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Prise en charge |Prise en charge |Dallas, Queretaro (Mexique)|
| **[T-Systems](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |Prise en charge |Prise en charge |Francfort|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Prise en charge |Prise en charge |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Prise en charge | Prise en charge | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Prise en charge |Prise en charge |Amsterdam, Chicago, Dallas, Hong Kong (R.A.S), Londres, Mumbaï, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Prise en charge | Prise en charge | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Prise en charge | Prise en charge | Auckland, Sydney |
| **Vodacom** |Pris en charge |Prise en charge |Le Cap, Johannesburg|
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Prise en charge |Prise en charge |Amsterdam2, Londres, Singapour |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Prise en charge | Prise en charge | Mumbai2 |
| **[XL Axiata]** | Prise en charge | Prise en charge | Jakarta |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Prise en charge |Pris en charge |Amsterdam, Chicago, Dallas, Denver, Londres, Los Angeles, Montréal, New York, Paris, Phoenix, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** = bientôt disponible

### <a name="national-cloud-environment"></a>Environnement de cloud national

Les clouds nationaux Azure sont isolés les uns des autres et des productions Azure commerciales mondiales. ExpressRoute pour un cloud Azure ne peut pas se connecter aux régions Azure dans les autres clouds. 

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Prise en charge |Prise en charge |Chicago, Phoenix, Silicon Valley, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Prise en charge |Prise en charge |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Prise en charge |Prise en charge |Atlanta, Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Internet2]()** |Prise en charge |Prise en charge |Dallas |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Prise en charge |Prise en charge |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Prise en charge | Prise en charge | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Prise en charge |Prise en charge |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Chine

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **China Telecom** |Prise en charge |Non pris en charge |Beijing, Beijing2, Shanghai, Shanghai2 |
| **China Unicom** | Prise en charge | Non pris en charge | Beijing2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Prise en charge |Non pris en charge |Beijing2, Shanghai2 |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Allemagne

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Prise en charge |Non pris en charge |Francfort |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Prise en charge |Non pris en charge |Francfort |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Prise en charge |Non pris en charge |Berlin |
| **Interxion** |Prise en charge |Non pris en charge |Francfort |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Prise en charge  | Non pris en charge | Berlin |
| **[T-Systems](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |Prise en charge |Non pris en charge |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Connectivité via des fournisseurs Exchange

Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/interconnection-services/equinix-fabric)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement de peering de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Connectivité via des opérateurs satellite
Si vous êtes à distance et que vous n’avez pas de connectivité par fibre ou si vous voulez explorer d’autres options de connectivité, vous pouvez consulter les opérateurs satellite suivants. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivité via d’autres fournisseurs de services

| **Fournisseur de connectivité** | **Exchange** | **Emplacements** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Singapour |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montréal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montréal, Toronto |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://www.bics.com/services/capacity-solutions/cloud-connect/)** | Equinix | Amsterdam, Francfort, Londres, Singapour, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | Hong Kong (R.A.S.) |
| **[Cinia](https://www.cinia.fi/palvelutiedotteet)** | Equinix, Megaport | Frankfort, Hambourg |
| **[CloudXpress](https://www2.telenet.be/content/www-telenet-be/fr/business/sme-le/aanbod/internet/cloudxpress)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapour | 
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | Londres, Singapour, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Québec |
| **[FPT Telecom International](https://cloudconnect.vn/en)** |Equinix |Singapour|
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://gbiinc.com/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](https://ivedha.com/cloud-services)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapour|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong (R.A.S.) 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Le Cap, Johannesburg |
| **[NextGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfort |
| **[Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londres, Paris |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Francfort |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[Spectre Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Chennai, Mumbai |
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfort |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapour |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Niveau 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectivité via des fournisseurs de centres de données

| **Fournisseur** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivité via les réseaux NREN (National Research and Education Networks)

| **Fournisseur**|
| --- |
| **AARNET**| 
| **DeIC, via GÉANT**|
| **GARR, via GÉANT**|
| **GÉANT**|
| **HEAnet, via GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, via GÉANT**|
| **SINET**|
| **Surfnet, via GÉANT**|

* Si votre fournisseur de connectivité n’est pas répertorié ici, vérifiez s’il est en lien avec l’un des partenaires Exchange ExpressRoute répertoriés ci-dessus.

## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Intégrateur système** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europe |
| **[Avanade Inc.](https://www.avanade.com/)** | Asie, Europe, Amérique du Nord, Amérique du Sud |
| **[Bright Skies GmbH](https://www.rackspace.com/bright-skies)** | Europe
| **[Ensyst](https://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Amérique du Nord |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Amérique du Nord |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Amérique du Nord |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australie |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Australie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europe (Allemagne) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europe |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europe |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asia |
| **[Orange Networks](https://www.orange-networks.com/blog/88-azureexpressroute)** | Europe |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Amérique du Nord |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Amérique du Nord |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europe |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Amérique du Sud |
| **[Vigilant.IT](https://vigilant.it/networking-services/microsoft-azure-networking/)** | Australie |

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"
