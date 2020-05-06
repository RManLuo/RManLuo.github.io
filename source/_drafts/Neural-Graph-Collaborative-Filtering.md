---
title: Neural Graph Collaborative Filtering
tags:
---

# Abstract：

传统的深度学习协同过滤，ID的嵌入是直接输入交互层，但是在NGCF中，其能捕获用户和物品之间的高阶关系。

![计算机生成了可选文字: User-itemInteractionGraph High-orderConnectivityfor榄1 Figure1：Anillustrationoftheuser-iteminteractiongraph andthehigh-orderconnectivity.Thenode屿isthetarget usertoproviderecommendationsfor.](http://image.rman.top/20200506161540.png)

 

# Method：

## 假设：

Intuitively, the interacted items provide direct evidence on a user’s preference [16, 38]; analogously, the users that consume an item can be treated as the item’s features and used to measure the collaborative similarity of two items.

 

## Message Construction:

![img](Neural-Graph-Collaborative-Filtering.assets/clip_image002.png)

![计算机生成了可选文字: MessageConstruction.Foraconnecteduser—itempair(http://image.rman.top/20200506161600.png),iWe definethemessagefrom》toas: （2） wheremue—iisthemessageembedding0．巳，theinformationtobe propagated).f(•)isthemessageencodingfunction,whichtakes embeddingseiandeuasInputandusesthecoeffcientpuito controlthedecayfactoroneachpropagationonedge》 Inthiswork,weImplement丆0as 1 Wiei+W2@@eu)), （3） d'xd where从了1，了2eRarethetrainableweightmatricestodistill usefulinformationforpropagation,and/isthetransformation size.Distinctfromconventionalgraphconvolutionnetworks[4，18， 29，41]thatconsiderthecontributionOfeionly,hereweadditionally encodetheinteractionbetweeneiandeuintothemessagebeing passedviaei@e，where@denotestheelement-wiseproduct. Thismakesthemessagedependentontheaffnitybetweeneiand e，巳g”passlngmoremessagesfromsimilaritems.Thisnot onlyIncreasesthemodelrepresentationability,butalsobooststhe performanceforrecommendation(evidencesInourexperiments Section4．4．2）．](file:///C:/Users/luoli/AppData/Local/Temp/msohtmlclip1/01/clip_image003.png)

## Message Aggregation:

邻居信息相加聚合在加到用户自身上和用户相加通过激活函数。

![计算机生成了可选文字: （1） (http://image.rman.top/20200506161604.png) =LeakyReLU国“一“+ （4） whereedenotestherepresentationofuserobtainedafter thefirstembeddingpropagationlayer.Theactivationfunction ofLeakyReLU[23]allowsmessagestoencodebothpositiveand smallnegativesignals.Notethatinadditiontothemessages propagatedfromneighborsNu,wetaketheself-connectionofu Intoconsideration:一榄 一从与ewhichretainstheinformation Oforiginalfeatures（从与istheweightmatrixsharedwiththeone usedinEquation（3））．Analogously,wecanobtaintherepresentation e，foritemibypropagatmginformationfromitsconnectedusers. Tosummarize,theadvantageoftheembeddingpropagationlayer liesInexplicitlyexploitmgthefirst-orderconnectivityInformation torelateuseranditemrepresentations.](file:///C:/Users/luoli/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

## 多层次卷积：

很典型的图卷积矩阵定义

![计算机生成了可选文字: PropagationRuleinMatrixForm.Toofferaholisticview Ofembeddingpropagationandfacilitatebatchimplementation, weprovidethematrixformOfthelayer-wisepropagationrule (http://image.rman.top/20200506161609.png)E(l-l)w）+LE一1）@E(l-l)w whereEeR(N+M)><山aretherepresentationsforusersanditems （0） obtainedafter/stepsOfembeddingpropagation.EissetasEat （0） （0） theinitialmessage-passingiterationthatiseu =euande andIdenoteanidentitymatrlux.representstheLaplacianmatrix fortheuser-itemgraphwhichisformulatedas: =D¯AD¯朰andA= 0R RT0 （8） NxM whereReR istheuser-iteminteractionmatrlxand0isall- zeromatrix•AistheadjacencymatrixandDisthediagonaldegree matrix，wherethet-thdiagonalelementDtt=INtl;assuch,the nonzerooff-diagonalentry丽=1/INullNll,whichisequalto “，usedInEquation（3）．](file:///C:/Users/luoli/AppData/Local/Temp/msohtmlclip1/01/clip_image005.png)

## Model prediction:

每一层都会得到用户u的表示，将其拼接得到用户最后的表示。

![计算机生成了可选文字: terpropagatmgwith layers,weObtalllmultiperepresentations (http://image.rman.top/20200506161614.png) forusernamely{eu eu}．Sincetherepresentations obtainedindifferentlayersemphasizethemessagespassedover differentconnections,theyhavedifferentcontributionsinreflecting userpreferenc巳Assuchweconcatenatethemtoconstitutethe finalembeddlngforauser;wedothesameoperationonitems， concatenatingtheitemrepresentations{e e}learnedby differentlayerstogetthefinalitemembedding: u （0） u （0） （9）](file:///C:/Users/luoli/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)

同理得到物品的表示，然后点积预测结果。

Loss采用BPR loss

Pairwise。对于implicit feedback（如是否购买），BPR对每一个user建立一个偏序关系 

 。如图所示，user-item矩阵中，+表示用户购买了该商品，?表示没有购买。比如，对user 1来说，他购买了 

，没有购买 

，则对于他来说，2和3比1和4好，但2、3之间，1、4之间无法比较

 

来自 <https://zhuanlan.zhihu.com/p/31841042> 

![计算机生成了可选文字: Theobjectivefunctionisasfollows 2 LOSS @，i，力GO (http://image.rman.top/20200506161618.png) where0 一{，i，丿月@，i)e+，@，力eR-}denotesthepairwise](file:///C:/Users/luoli/AppData/Local/Temp/msohtmlclip1/01/clip_image007.png)