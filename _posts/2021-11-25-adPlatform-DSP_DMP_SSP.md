---
layout: post
title:  "AD Tech 용어 정리"
subtitle:   "AD Tech 용어 정리"
categories: adPlatform
tags: DA DSP DMP SSP AD
comments: false
---
## AD NETWORK
디지털 시장에서 구매자(수요자)는 **광고주/광고에이전시**이고 판매자(공급자)는 **지면(인벤토리)을 소유하고 있는 매체**입니다.  
매체는 지면을 팔고, 광고를 원하는 광고주나 에이전시는 그 지면을 사들입니다.  
문제는 **디지털 시장이 빠르게 커지면서 광고 인벤토리를 가진 매체의 수 역시 폭발적으로 증가하게 된 것**입니다.  
광고주는 효율적인 성과를 보여주는 매체를 찾으려면 많은 수고가 필요했고, 인벤토리를 가진 매체 역시 수익을 극대화 시켜줄 수 있는 광고주를 만나는 것이 어려워진 상황이 발생하게 된 것입니다.  

![ad img](/assets/ad/1.JPG) 
이러한 혼선을 정리해주는, 중간다리 역할을 자처하며 나온 것이 **AD NETWORK**입니다.  
우리가 사용하고 있는 페이스북, 구글, 카카오 모두 다 애드네트워크에 속합니다.
DSP, DMP, SSP는 이러한 애드네트워크에 속해 있습니다.  

<br>
<hr>
### &#128204; DSP

#### 광고주가 광고 지면을 구매할 때 이용하는 애드네트워크 플랫폼입니다.  
> 디스플레이 광고를 진행하고 싶은데 일일이 내가 광고 지면을 찾고 그 매체 지면 관리자에게 연락해서 광고 타임을 사는게 사실상 불가능. 그래서 플랫폼을 이용해서 광고 지면을 사는 것  

DSP는 광고주의 원하는 타겟에게 광고를 송출하여 비용대비 광고 효율을 높이기 위한 플랫폼입니다.  
광고주 및 에이전시가 Digital Ad Marketplace(Ad exchanges, Ad networks, PMP, PG, etc.)상에서 디지털 광고 지면/인벤토리를 더 쉽게 구매할 수 있도록 도와주는 온라인 플랫폼입니다.  
*ex) Google, OpenX, AppNexus, FAN S2S, Criteo ...*  

### &#128204; SSP

#### 광고 지면을 판매할 때 쓰는 플랫폼입니다.  
> 홈페이지에 배너를 올리고 수익을 얻고 싶을 때 일일이 내가 광고를 원하는 광고주를 찾을 수 없기 때문에 플랫폼을 이용해서 광고주를 찾는 것  

매체 측에서 매체의 이익을 극대화 하기 위한 플랫폼입니다.  
SSP는 유저로 인해 매체에서 노출이 발생할때마다 비어있는 인벤토리를 RTB 경매장에 올리게 됩니다. 이를 확인한 DSP의 응답 중 가장 높은 입찰가를 부른 광고의 요청을 수락하고 해당 광고를 매체에 게시하게 됩니다.  

### &#128204; DMP

#### 광고를 송출할 타겟을 정하기 위해서 유저 데이터를 분석해주는 곳입니다.
관심사, 지역, 행동패턴 등 플랫폼 내에서만 식별가능하도록 만들어 놓은 뒤 DSP에서 요구하는 타겟과 유사한 유저 정보를 제공하고 그 유저가 해당 타겟인지 아닌지 구별하여 그 정보를 DSP에서 활용할 수 있도록 제공합니다. 
이렇게 DMP는 DSP 플랫폼에서 광고주에게 효율적인 타겟에게 광고를 내보낼 수 있게 하여 비용대비하여 광고 효율을 높일 수 있도록 합니다.  

> 사용자 데이터를 분석해서 이왕이면 이 광고에 흥미가 있을 것 같은 사용자를 선택하기 위함입니다!  

*ex) Oracle, Adobe, Data allience, LiveRamp*  

### &#128204; AD Exchange(ADX)
광고 거래 중개소라고 볼 수 있습니다.  
AD Networks의 규모가 커져서 만들어진 네트워크 묶음으로 여러 매체와 AD Networks 사이에서 입찰을 통해 광고를 구매할 수 있도록 해줍니다.  
AD Exchange는 일반적으로 거래의 효율성과 공정성을 위해 **RTB(Real Time Bidding)** 방식을 이용합니다.  
RTB란 유저가 광고에 노출될 때마다 해당 인벤토리를 경매에 부치는 방식으로 유저들의 노출이 발생할때마다 실시간으로 광고주(DSP)들에게 해당 인벤토리의 구매여부를 물어봅니다. 
이들은 해당 노출을 분석한 후 적학하다고 판단할 경우 경매에 참여하게 됩니다.  
이러한 과정은 실제적으로는 매우 짧은 시간에 일어납니다. 경매 하나에 소요되는 시간은 100ms로 매우 빠르기 때문에 AD Exchange는 짧은 시간 안에 수많은 트래픽을 처리할 수 있는 능력을 갖추어야 합니다.  

<hr/>
<br/>
## &#10024; 정리 &#10024;

![ad img](/assets/ad/5.JPG) 

### AD Network (애드 네트워크)
**애드 네트워크는 광고 인벤토리를 구매하길 바라는 광고주와 판매하고자 하는 매체사를 연결해주는 중개 플랫폼입니다.**  
애드 네트워크의 주요 기능은 매체사들의 수많은 인벤토리를 합치고 카테고리화 하여 광고주들의 요구와 매칭시켜 판매하는 것입니다. 애드 네트워크를 통해 광고주는 원하는 사용자에게 광고를 노출할 가능성을 높이고, 매체사는 보다 효율적으로 보유하고 있는 인벤토리를 판매할 수 있습니다.  

### DSP(Demand Side Platforms)
**DSP는 광고주(대행사)가 하나의 인터페이스를 통해 여러 애드 네트워크가 보유한 인벤토리를 관리하고 구매할 수 있도록 도와주는 플랫폼입니다.**  
DSP를 통해 광고주(대행사)는 성별, 연령, 지역, 관심사 등, 광고주가 원하는 타겟 정보에 따라 다양한 인벤토리를 효율적으로 구매할 수 있습니다.  

### SSP(Supply Side Platforms)
**SSP는 매체사가 보유하고 있는 인벤토리를 자동으로 판매하고 관리할 수 있도록 도와주는 시스템입니다.**  
SSP는 애드 익스체인지를 비롯해 매체사의 인벤토리를 구매할 가능성이 있는 모든 광고주에게 광고 인벤토리를 제공함으로써, 매체사들의 수익을 극대화할 수 있습니다.  

### AD Exchange (애드 익스체인지)
애드 익스체인지는 애드 네트워크에서 한단계 더 나아간 형태로 볼 수 있습니다. 애드 익스체인지는 수많은 애드 네트워크, DSP, SSP와 연결되어 있어, **대량의 광고 인벤토리를 구매하고 판매할 수 있게 도와줍니다.**  
이 때 가격이 바로 RTB를 통해 이뤄집니다.  
