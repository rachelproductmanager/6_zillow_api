# Extracting real estate data from the Zillow API

Zillow is a leading U.S. real estate and rental marketplace dedicated to empowering consumers with data, inspiration and knowledge around the place they call home, and connecting them with the best local professionals who can help.

## 1. The data

For the purposes of this exercise, I used the Quandl API to extract data from Zillow (which is freely available here: https://www.quandl.com/databases/ZILLOW/data).

The Zillow Real Estate data feed contains real estate market indicators, such as market indices, rental, sales, and inventories for thousands of geographical areas across the United States. This free dataset contains 10,000 family home sales in Washington state covering the period between 2005-2020. 

The data is organised into 3 separate tables, each containing different information, which can be joined to provide a fuller picture of the property region and sale prices.

As you'll see below, the tables comprise:

- regions (zip codes and other geo information)
- indicators (re the type of property sold)
- price data (value and date for each property sale)

I'll start by showing how I extracted the data via an API and converted it into data frames which were then joined.

I then performed some data cleaning and rehasping to transform 10,000 records into a more consolidated version which enabled me to focus on certain areas and time periods and run interesting claculations and visualisations to gain deeper insights from the data about the least and most affordable areas in Washington State and how prices have fared over 15 years and the past decade.

## 2. Importing API data


```python
import requests
import pandas as pd
```


```python
#import table 1

api_url = "https://www.quandl.com/api/v3/datatables/ZILLOW/REGIONS/?api_key=8zYqUX8TEUYYtbCkcPHq"

response = requests.get(api_url)
```


```python
response
```




    <Response [200]>




```python
data = response.json()
print(data)
```

    {'datatable': {'data': [['99999', 'zip', '98847; WA; Wenatchee; Chelan County; Peshastin'], ['99998', 'zip', '98846; WA; Okanogan County; Pateros'], ['99997', 'zip', '98845; WA; Wenatchee; Douglas County; Palisades'], ['99996', 'zip', '98844; WA; Okanogan County; Oroville'], ['99995', 'zip', '98843; WA; Wenatchee; Douglas County; Orondo'], ['99994', 'zip', '98841; WA; Okanogan County; Omak'], ['99993', 'zip', '98840; WA; Okanogan County; Okanogan'], ['99992', 'zip', '98837; WA; Moses Lake; Grant County'], ['99991', 'zip', '98836; WA; Wenatchee; Chelan County'], ['99990', 'zip', '98834; WA; Okanogan County; Methow'], ['9999', 'city', 'Carrsville; VA; Virginia Beach-Norfolk-Newport News; Isle of Wight County'], ['99989', 'zip', '98833; WA; Okanogan County; Mazama'], ['99988', 'zip', '98832; WA; Moses Lake; Grant County; Krupp'], ['99987', 'zip', '98831; WA; Wenatchee; Chelan County; Manson'], ['99986', 'zip', '98830; WA; Wenatchee; Douglas County; Mansfield'], ['99985', 'zip', '98829; WA; Okanogan County; Okanogan'], ['99984', 'zip', '98828; WA; Wenatchee; Chelan County; Malaga'], ['99983', 'zip', '98827; WA; Okanogan County; Loomis'], ['99982', 'zip', '98826; WA; Wenatchee; Chelan County; Leavenworth'], ['99980', 'zip', '98823; WA; Moses Lake; Grant County; Ephrata'], ['9998', 'city', 'Birchleaf; VA; Big Stone Gap; Dickenson County'], ['99979', 'zip', '98822; WA; Wenatchee; Chelan County; Entiat'], ['99978', 'zip', '98821; WA; Wenatchee; Chelan County; Cashmere'], ['99977', 'zip', '98819; WA; Okanogan County; Omak'], ['99976', 'zip', '98817; WA; Wenatchee; Chelan County; Chelan Falls'], ['99975', 'zip', '98816; WA; Wenatchee; Chelan County; Chelan'], ['99974', 'zip', '98815; WA; Wenatchee; Chelan County; Cashmere'], ['99973', 'zip', '98814; WA; Okanogan County; Carlton'], ['99972', 'zip', '98813; WA; Wenatchee; Douglas County; Bridgeport'], ['99971', 'zip', '98812; WA; Okanogan County; Brewster'], ['99970', 'zip', '98811; WA; Wenatchee; Chelan County; Entiat'], ['99968', 'zip', '98802; WA; Wenatchee; Douglas County; East Wenatchee Bench'], ['99967', 'zip', '98801; WA; Wenatchee; Chelan County'], ['99965', 'zip', '98686'], ['99964', 'zip', '98685'], ['99963', 'zip', '98684; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'], ['99962', 'zip', '98683'], ['99961', 'zip', '98682'], ['99960', 'zip', '98675; WA; Portland-Vancouver-Hillsboro; Clark County; Yacolt'], ['99959', 'zip', '98674; WA; Longview; Cowlitz County; Woodland'], ['99958', 'zip', '98673; WA; Klickitat County; Centerville'], ['99957', 'zip', '98672; WA; Klickitat County; White Salmon'], ['99956', 'zip', '98671'], ['99955', 'zip', '98670; WA; Klickitat County; Wahkiacus'], ['99951', 'zip', '98665; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'], ['99950', 'zip', '98664; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'], ['99949', 'zip', '98663; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'], ['99948', 'zip', '98662'], ['99947', 'zip', '98661'], ['99946', 'zip', '98660'], ['99945', 'zip', '98651; WA; Portland-Vancouver-Hillsboro; Skamania County; Underwood'], ['99944', 'zip', '98650; WA; Klickitat County; Trout Lake'], ['99943', 'zip', '98649; WA; Longview; Cowlitz County; Toutle'], ['99942', 'zip', '98648; WA; Portland-Vancouver-Hillsboro; Skamania County; Stevenson'], ['99941', 'zip', '98647; WA; Wahkiakum County; Skamokawa'], ['99940', 'zip', '98645; WA; Longview; Cowlitz County; Silverlake'], ['9994', 'city', 'Wright; KS; Dodge City; Ford County'], ['99939', 'zip', '98644; WA; Pacific County; Ilwaco'], ['99938', 'zip', '98643; WA; Wahkiakum County; Rosburg'], ['99937', 'zip', '98642'], ['99936', 'zip', '98641; WA; Pacific County; Ocean Park'], ['99935', 'zip', '98640; WA; Pacific County; Ocean Park'], ['99934', 'zip', '98639; WA; Portland-Vancouver-Hillsboro; Skamania County; North Bonneville'], ['99933', 'zip', '98638; WA; Pacific County; Naselle'], ['99931', 'zip', '98635; WA; Klickitat County; Lyle'], ['99930', 'zip', '98632; WA; Longview; Cowlitz County'], ['99929', 'zip', '98631; WA; Pacific County; Long Beach'], ['99928', 'zip', '98629; WA; Portland-Vancouver-Hillsboro; Clark County; La Center'], ['99927', 'zip', '98628; WA; Klickitat County; Lyle'], ['99926', 'zip', '98626; WA; Longview; Cowlitz County; Kelso'], ['99925', 'zip', '98625; WA; Longview; Cowlitz County; Kalama'], ['99924', 'zip', '98624; WA; Pacific County; Ilwaco'], ['99923', 'zip', '98623; WA; Klickitat County; White Salmon'], ['99921', 'zip', '98621; WA; Wahkiakum County; Grays River'], ['99920', 'zip', '98620; WA; Klickitat County; Goldendale'], ['99919', 'zip', '98619; WA; Klickitat County; Glenwood'], ['99918', 'zip', '98617; WA; Klickitat County; Dallesport'], ['99917', 'zip', '98616; WA; Longview; Cowlitz County; Cougar'], ['99916', 'zip', '98614; WA; Pacific County; Chinook'], ['99915', 'zip', '98613; WA; Klickitat County; Centerville'], ['99914', 'zip', '98612; WA; Wahkiakum County; Cathlamet'], ['99913', 'zip', '98611; WA; Longview; Cowlitz County; Castle Rock'], ['99912', 'zip', '98610; WA; Portland-Vancouver-Hillsboro; Skamania County; Carson'], ['99911', 'zip', '98609; WA; Longview; Cowlitz County; Kelso'], ['99910', 'zip', '98607'], ['99909', 'zip', '98606; WA; Portland-Vancouver-Hillsboro; Clark County; Brush Prairie'], ['99908', 'zip', '98605; WA; Portland-Vancouver-Hillsboro; Skamania County; Bingen'], ['99907', 'zip', '98604; WA; Portland-Vancouver-Hillsboro; Clark County; Battle Ground'], ['99906', 'zip', '98603; WA; Longview; Cowlitz County; Ariel'], ['99905', 'zip', '98602; WA; Klickitat County; Appleton'], ['99904', 'zip', '98601; WA; Portland-Vancouver-Hillsboro; Clark County; Amboy'], ['99902', 'zip', '98597; WA; Olympia-Tumwater; Thurston County; Yelm'], ['99901', 'zip', '98596; WA; Centralia; Lewis County; Winlock'], ['99900', 'zip', '98595; WA; Aberdeen; Grays Harbor County; Westport'], ['999', 'county', 'Durham County; NC; Durham-Chapel Hill'], ['99899', 'zip', '98593; WA; Centralia; Lewis County; Vader'], ['99898', 'zip', '98592; WA; Shelton; Mason County; Union'], ['99897', 'zip', '98591; WA; Centralia; Lewis County; Toledo'], ['99896', 'zip', '98590; WA; Pacific County; Tokeland'], ['99895', 'zip', '98589; WA; Olympia-Tumwater; Thurston County; Tenino'], ['99894', 'zip', '98588; WA; Shelton; Mason County; Tahuya'], ['99893', 'zip', '98587; WA; Aberdeen; Grays Harbor County; Moclips'], ['99892', 'zip', '98586; WA; Pacific County; South Bend'], ['99891', 'zip', '98585; WA; Centralia; Lewis County; Silver Creek'], ['99890', 'zip', '98584; WA; Shelton; Mason County'], ['99889', 'zip', '98583; WA; Aberdeen; Grays Harbor County; Elma'], ['99888', 'zip', '98582; WA; Centralia; Lewis County; Salkum'], ['99887', 'zip', '98581; WA; Longview; Cowlitz County; Ryderwood'], ['99886', 'zip', '98580; WA; Seattle-Tacoma-Bellevue; Pierce County; Roy'], ['99885', 'zip', '98579; WA; Olympia-Tumwater; Thurston County; Rochester'], ['99884', 'zip', '98577; WA; Pacific County; Raymond'], ['99883', 'zip', '98576; WA; Olympia-Tumwater; Thurston County; Rainier'], ['99882', 'zip', '98575; WA; Aberdeen; Grays Harbor County; Quinault'], ['99881', 'zip', '98572; WA; Centralia; Lewis County; Pe Ell'], ['99880', 'zip', '98571; WA; Aberdeen; Grays Harbor County; Pacific Beach'], ['99879', 'zip', '98570; WA; Centralia; Lewis County; Onalaska'], ['99878', 'zip', '98569; WA; Aberdeen; Grays Harbor County; Ocean Shores'], ['99877', 'zip', '98568; WA; Aberdeen; Grays Harbor County; Oakville'], ['99876', 'zip', '98566; WA; Aberdeen; Grays Harbor County; Humptulips'], ['99875', 'zip', '98565; WA; Centralia; Lewis County; Napavine'], ['99874', 'zip', '98564; WA; Centralia; Lewis County; Mossyrock'], ['99873', 'zip', '98563; WA; Aberdeen; Grays Harbor County; Montesano'], ['99872', 'zip', '98562; WA; Aberdeen; Grays Harbor County; Moclips'], ['99870', 'zip', '98560; WA; Shelton; Mason County; Matlock'], ['9987', 'city', 'Weston; CT; Bridgeport-Stamford-Norwalk; Fairfield County'], ['99868', 'zip', '98558; WA; Seattle-Tacoma-Bellevue; Pierce County; Roy'], ['99867', 'zip', '98557; WA; Aberdeen; Grays Harbor County; McCleary'], ['99865', 'zip', '98555; WA; Shelton; Mason County; Lilliwaup'], ['99863', 'zip', '98552; WA; Aberdeen; Grays Harbor County; Humptulips'], ['99862', 'zip', '98550; WA; Aberdeen; Grays Harbor County; Hoquiam'], ['99861', 'zip', '98548; WA; Shelton; Mason County; Hoodsport'], ['99860', 'zip', '98547; WA; Aberdeen; Grays Harbor County; Grayland'], ['99859', 'zip', '98546; WA; Shelton; Mason County; Grapeview'], ['99857', 'zip', '98542; WA; Centralia; Lewis County; Ethel'], ['99856', 'zip', '98541; WA; Aberdeen; Grays Harbor County; Elma'], ['99853', 'zip', '98538; WA; Centralia; Lewis County; Chehalis'], ['99852', 'zip', '98537; WA; Aberdeen; Grays Harbor County; Cosmopolis'], ['99851', 'zip', '98536; WA; Aberdeen; Grays Harbor County; Hoquiam'], ['99850', 'zip', '98535; WA; Aberdeen; Grays Harbor County; Copalis Beach'], ['99849', 'zip', '98533; WA; Centralia; Lewis County; Cinebar'], ['99848', 'zip', '98532; WA; Centralia; Lewis County; Chehalis'], ['99847', 'zip', '98531; WA; Centralia; Lewis County'], ['99846', 'zip', '98530; WA; Olympia-Tumwater; Thurston County; Bucoda'], ['99845', 'zip', '98528; WA; Shelton; Mason County; Belfair'], ['99844', 'zip', '98527; WA; Pacific County; South Bend'], ['99843', 'zip', '98526; WA; Aberdeen; Grays Harbor County; Amanda Park'], ['99842', 'zip', '98524; WA; Shelton; Mason County; Allyn'], ['99841', 'zip', '98522; WA; Centralia; Lewis County; Chehalis'], ['99840', 'zip', '98520; WA; Aberdeen; Grays Harbor County'], ['99839', 'zip', '98516; WA; Olympia-Tumwater; Thurston County; Lacey'], ['99838', 'zip', '98513; WA; Olympia-Tumwater; Thurston County; Lacey'], ['99837', 'zip', '98512; WA; Olympia-Tumwater; Thurston County; Tumwater'], ['99832', 'zip', '98506; WA; Olympia-Tumwater; Thurston County; Olympia'], ['99829', 'zip', '98503; WA; Olympia-Tumwater; Thurston County; Lacey'], ['99828', 'zip', '98502; WA; Olympia-Tumwater; Thurston County; Olympia'], ['99827', 'zip', '98501; WA; Olympia-Tumwater; Thurston County; Olympia'], ['99826', 'zip', '98499; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakewood'], ['99825', 'zip', '98498'], ['99818', 'zip', '98467'], ['99817', 'zip', '98466'], ['99816', 'zip', '98465; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'], ['99810', 'zip', '98446'], ['99809', 'zip', '98445'], ['99808', 'zip', '98444'], ['99807', 'zip', '98443; WA; Seattle-Tacoma-Bellevue; Pierce County; Waller'], ['99805', 'zip', '98439; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakewood'], ['99800', 'zip', '98424; WA; Seattle-Tacoma-Bellevue; Pierce County; Fife'], ['9980', 'city', 'South Wilmington; IL; Chicago-Naperville-Elgin; Grundy County'], ['998', 'county', 'Duplin County; NC'], ['99799', 'zip', '98422; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'], ['99798', 'zip', '98421; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'], ['99797', 'zip', '98418; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'], ['99791', 'zip', '98409'], ['99790', 'zip', '98408'], ['99789', 'zip', '98407'], ['99788', 'zip', '98406'], ['99787', 'zip', '98405'], ['99786', 'zip', '98404'], ['99785', 'zip', '98403'], ['99784', 'zip', '98402'], ['99780', 'zip', '98396; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'], ['9978', 'city', 'Waldo; FL; Gainesville; Alachua County'], ['99778', 'zip', '98394; WA; Seattle-Tacoma-Bellevue; Pierce County; Vaughn'], ['99776', 'zip', '98392; WA; Bremerton-Silverdale; Kitsap County; Suquamish'], ['99775', 'zip', '98391'], ['99774', 'zip', '98390; WA; Seattle-Tacoma-Bellevue; Pierce County; Sumner'], ['99773', 'zip', '98388; WA; Seattle-Tacoma-Bellevue; Pierce County; Steilacoom'], ['99772', 'zip', '98387'], ['99770', 'zip', '98385; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'], ['99768', 'zip', '98383; WA; Bremerton-Silverdale; Kitsap County; Silverdale'], ['99767', 'zip', '98382; WA; Port Angeles; Clallam County; Sequim'], ['99766', 'zip', '98381; WA; Port Angeles; Clallam County; Sekiu'], ['99765', 'zip', '98380; WA; Bremerton-Silverdale; Kitsap County; Seabeck'], ['99763', 'zip', '98377; WA; Centralia; Lewis County; Randle'], ['99762', 'zip', '98376; WA; Jefferson County; Quilcene'], ['99761', 'zip', '98375'], ['99760', 'zip', '98374'], ['99759', 'zip', '98373'], ['99758', 'zip', '98372'], ['99757', 'zip', '98371; WA; Seattle-Tacoma-Bellevue; Pierce County; Puyallup'], ['99756', 'zip', '98370; WA; Bremerton-Silverdale; Kitsap County; Poulsbo'], ['99755', 'zip', '98368; WA; Jefferson County; Port Townsend'], ['99754', 'zip', '98367; WA; Bremerton-Silverdale; Kitsap County; Port Orchard'], ['99753', 'zip', '98366; WA; Bremerton-Silverdale; Kitsap County; Port Orchard'], ['99752', 'zip', '98365; WA; Jefferson County; Port Ludlow'], ['99750', 'zip', '98363; WA; Port Angeles; Clallam County'], ['99749', 'zip', '98362; WA; Port Angeles; Clallam County'], ['99748', 'zip', '98361; WA; Centralia; Lewis County; Packwood'], ['99747', 'zip', '98360; WA; Seattle-Tacoma-Bellevue; Pierce County; Orting'], ['99746', 'zip', '98359; WA; Bremerton-Silverdale; Kitsap County; Olalla'], ['99745', 'zip', '98358; WA; Jefferson County; Nordland'], ['99743', 'zip', '98356; WA; Centralia; Lewis County; Morton'], ['99742', 'zip', '98355; WA; Centralia; Lewis County; Mineral'], ['99741', 'zip', '98354; WA; Seattle-Tacoma-Bellevue; Pierce County; Milton'], ['99738', 'zip', '98351; WA; Seattle-Tacoma-Bellevue; Pierce County; Longbranch'], ['99736', 'zip', '98349; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakebay'], ['99734', 'zip', '98346; WA; Bremerton-Silverdale; Kitsap County; Kingston'], ['99733', 'zip', '98345; WA; Bremerton-Silverdale; Kitsap County; Keyport'], ['99730', 'zip', '98342; WA; Bremerton-Silverdale; Kitsap County; Indianola'], ['9973', 'city', 'Vienna; MD; Cambridge; Dorchester County'], ['99729', 'zip', '98340; WA; Bremerton-Silverdale; Kitsap County; Hansville'], ['99728', 'zip', '98339; WA; Jefferson County; Port Hadlock'], ['99727', 'zip', '98338; WA; Seattle-Tacoma-Bellevue; Pierce County; Graham'], ['99726', 'zip', '98337; WA; Bremerton-Silverdale; Kitsap County; Bremerton'], ['99725', 'zip', '98336; WA; Centralia; Lewis County; Glenoma'], ['99724', 'zip', '98335'], ['99723', 'zip', '98333; WA; Seattle-Tacoma-Bellevue; Pierce County; Fox Island'], ['99722', 'zip', '98332; WA; Seattle-Tacoma-Bellevue; Pierce County; Gig Harbor'], ['99721', 'zip', '98331; WA; Port Angeles; Clallam County; Forks'], ['99720', 'zip', '98330; WA; Seattle-Tacoma-Bellevue; Pierce County; Eatonville'], ['99719', 'zip', '98329; WA; Seattle-Tacoma-Bellevue; Pierce County; Gig Harbor'], ['99718', 'zip', '98328; WA; Seattle-Tacoma-Bellevue; Pierce County; Eatonville'], ['99717', 'zip', '98327'], ['99716', 'zip', '98326; WA; Port Angeles; Clallam County; Clallam Bay'], ['99715', 'zip', '98325; WA; Jefferson County; Chimacum'], ['99713', 'zip', '98323; WA; Seattle-Tacoma-Bellevue; Pierce County; Carbonado'], ['99711', 'zip', '98321; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'], ['99710', 'zip', '98320; WA; Jefferson County; Brinnon'], ['99707', 'zip', '98312; WA; Bremerton-Silverdale; Kitsap County; Bremerton'], ['99706', 'zip', '98311; WA; Bremerton-Silverdale; Kitsap County; Bremerton'], ['99705', 'zip', '98310; WA; Bremerton-Silverdale; Kitsap County; Bremerton'], ['99704', 'zip', '98305; WA; Port Angeles; Clallam County; Beaver'], ['99703', 'zip', '98304; WA; Seattle-Tacoma-Bellevue; Pierce County; Ashford'], ['99702', 'zip', '98303; WA; Seattle-Tacoma-Bellevue; Pierce County; Graham'], ['99700', 'zip', '98296; WA; Seattle-Tacoma-Bellevue; Snohomish County; Snohomish'], ['997', 'county', 'Dubois County; IN; Jasper'], ['99699', 'zip', '98295; WA; Bellingham; Whatcom County; Sumas'], ['99698', 'zip', '98294; WA; Seattle-Tacoma-Bellevue; Snohomish County; Sultan'], ['99696', 'zip', '98292; WA; Seattle-Tacoma-Bellevue; Snohomish County; Stanwood'], ['99694', 'zip', '98290; WA; Seattle-Tacoma-Bellevue; Snohomish County; Snohomish'], ['99693', 'zip', '98288; WA; Seattle-Tacoma-Bellevue; King County; Skykomish'], ['99691', 'zip', '98286; WA; San Juan County; Eastsound'], ['99690', 'zip', '98284; WA; Mount Vernon-Anacortes; Skagit County; Sedro-Woolley'], ['9969', 'city', 'Tinley Park; IL; Chicago-Naperville-Elgin; Cook County'], ['99689', 'zip', '98283; WA; Mount Vernon-Anacortes; Skagit County; Rockport'], ['99688', 'zip', '98282; WA; Oak Harbor; Island County; Camano Island'], ['99687', 'zip', '98281; WA; Bellingham; Whatcom County; Point Roberts'], ['99686', 'zip', '98280; WA; San Juan County; Eastsound'], ['99685', 'zip', '98279; WA; San Juan County; Olga'], ['99683', 'zip', '98277; WA; Oak Harbor; Island County'], ['99682', 'zip', '98276; WA; Bellingham; Whatcom County; Nooksack'], ['99681', 'zip', '98275'], ['99680', 'zip', '98274; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'], ['99679', 'zip', '98273; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'], ['99678', 'zip', '98272; WA; Seattle-Tacoma-Bellevue; Snohomish County; Monroe'], ['99677', 'zip', '98271'], ['99676', 'zip', '98270'], ['99675', 'zip', '98267; WA; Mount Vernon-Anacortes; Skagit County; Marblemount'], ['99674', 'zip', '98266; WA; Bellingham; Whatcom County; Maple Falls'], ['99673', 'zip', '98264; WA; Bellingham; Whatcom County; Lynden'], ['99672', 'zip', '98263; WA; Mount Vernon-Anacortes; Skagit County; Lyman'], ['99671', 'zip', '98262; WA; Bellingham; Whatcom County; Lummi Island'], ['99670', 'zip', '98261; WA; San Juan County; Eastsound'], ['9967', 'city', 'Yorktown; TX; De Witt County'], ['99669', 'zip', '98260; WA; Oak Harbor; Island County; Langley'], ['99667', 'zip', '98258'], ['99666', 'zip', '98257; WA; Mount Vernon-Anacortes; Skagit County; La Conner'], ['99665', 'zip', '98256; WA; Seattle-Tacoma-Bellevue; Snohomish County; Index'], ['99664', 'zip', '98255; WA; Mount Vernon-Anacortes; Skagit County; Hamilton'], ['99663', 'zip', '98253; WA; Oak Harbor; Island County; Greenbank'], ['99662', 'zip', '98252; WA; Seattle-Tacoma-Bellevue; Snohomish County; Granite Falls'], ['99661', 'zip', '98251; WA; Seattle-Tacoma-Bellevue; Snohomish County; Gold Bar'], ['99660', 'zip', '98250; WA; San Juan County; Friday Harbor'], ['9966', 'city', 'Yachats; OR; Newport; Lincoln County'], ['99659', 'zip', '98249; WA; Oak Harbor; Island County; Freeland'], ['99658', 'zip', '98248; WA; Bellingham; Whatcom County; Ferndale'], ['99657', 'zip', '98247; WA; Bellingham; Whatcom County; Everson'], ['99656', 'zip', '98245; WA; San Juan County; Eastsound'], ['99655', 'zip', '98244; WA; Bellingham; Whatcom County; Deming'], ['99653', 'zip', '98241; WA; Seattle-Tacoma-Bellevue; Snohomish County; Darrington'], ['99652', 'zip', '98240; WA; Bellingham; Whatcom County; Custer'], ['99651', 'zip', '98239; WA; Oak Harbor; Island County; Coupeville'], ['99649', 'zip', '98237; WA; Mount Vernon-Anacortes; Skagit County; Concrete'], ['99648', 'zip', '98236; WA; Oak Harbor; Island County; Clinton'], ['99647', 'zip', '98235; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'], ['99646', 'zip', '98233; WA; Mount Vernon-Anacortes; Skagit County; Burlington'], ['99645', 'zip', '98232; WA; Mount Vernon-Anacortes; Skagit County; Bow'], ['99643', 'zip', '98230; WA; Bellingham; Whatcom County; Blaine'], ['99642', 'zip', '98229; WA; Bellingham; Whatcom County'], ['99639', 'zip', '98226; WA; Bellingham; Whatcom County'], ['99638', 'zip', '98225; WA; Bellingham; Whatcom County'], ['99637', 'zip', '98224; WA; Seattle-Tacoma-Bellevue; Snohomish County; Gold Bar'], ['99636', 'zip', '98223; WA; Seattle-Tacoma-Bellevue; Snohomish County; Arlington'], ['99635', 'zip', '98222; WA; San Juan County; Olga'], ['99634', 'zip', '98221; WA; Mount Vernon-Anacortes; Skagit County; Anacortes'], ['99633', 'zip', '98220; WA; Bellingham; Whatcom County; Acme'], ['99631', 'zip', '98208'], ['9963', 'city', 'Satartia; MS; Jackson; Yazoo County'], ['99629', 'zip', '98206; WA; Oak Harbor; Island County; Clinton'], ['99627', 'zip', '98204; WA; Seattle-Tacoma-Bellevue; Snohomish County; Paine Field-Lake Stickney'], ['99626', 'zip', '98203; WA; Seattle-Tacoma-Bellevue; Snohomish County; Everett'], ['99625', 'zip', '98201'], ['99624', 'zip', '98199'], ['99623', 'zip', '98198'], ['9962', 'city', 'Vale; SD; Butte County'], ['99618', 'zip', '98188; WA; Seattle-Tacoma-Bellevue; King County; Seatac'], ['99614', 'zip', '98178'], ['99613', 'zip', '98177'], ['9961', 'city', 'San Patricio; TX; Corpus Christi; San Patricio County'], ['99608', 'zip', '98168'], ['99607', 'zip', '98166; WA; Seattle-Tacoma-Bellevue; King County; Burien'], ['99605', 'zip', '98164; WA; Seattle-Tacoma-Bellevue; King County; Seattle'], ['99601', 'zip', '98155'], ['9960', 'city', 'Wildrose; ND; Williston; Williams County'], ['99598', 'zip', '98148; WA; Seattle-Tacoma-Bellevue; King County; Burien'], ['99597', 'zip', '98146'], ['99595', 'zip', '98144'], ['99591', 'zip', '98136; WA; Seattle-Tacoma-Bellevue; King County; Seattle'], ['99590', 'zip', '98134; WA; Seattle-Tacoma-Bellevue; King County; Seattle'], ['99589', 'zip', '98133'], ['99584', 'zip', '98126; WA; Seattle-Tacoma-Bellevue; King County; Seattle'], ['99583', 'zip', '98125'], ['99581', 'zip', '98122'], ['99580', 'zip', '98121'], ['9958', 'city', 'Spurlockville; WV; Huntington-Ashland; Lincoln County'], ['99579', 'zip', '98119'], ['99578', 'zip', '98118'], ['99577', 'zip', '98117'], ['99576', 'zip', '98116'], ['99575', 'zip', '98115'], ['99572', 'zip', '98112'], ['99570', 'zip', '98110; WA; Bremerton-Silverdale; Kitsap County; Bainbridge Island'], ['99569', 'zip', '98109'], ['99568', 'zip', '98108'], ['99567', 'zip', '98107'], ['99566', 'zip', '98106'], ['99565', 'zip', '98105; WA; Seattle-Tacoma-Bellevue; King County; Seattle'], ['99564', 'zip', '98104'], ['99563', 'zip', '98103'], ['99562', 'zip', '98102'], ['99561', 'zip', '98101'], ['9956', 'city', 'Prairie Rose; ND; Fargo; Cass County'], ['99559', 'zip', '98092; WA; Seattle-Tacoma-Bellevue; King County; Auburn'], ['99557', 'zip', '98087'], ['99554', 'zip', '98077; WA; Seattle-Tacoma-Bellevue; King County; Woodinville'], ['99553', 'zip', '98075'], ['99552', 'zip', '98074; WA; Seattle-Tacoma-Bellevue; King County; Sammamish'], ['99550', 'zip', '98072; WA; Seattle-Tacoma-Bellevue; King County; Woodinville'], ['99548', 'zip', '98070; WA; Seattle-Tacoma-Bellevue; King County; Vashon'], ['99547', 'zip', '98068; WA; Ellensburg; Kittitas County; Snoqualmie Pass'], ['99546', 'zip', '98065; WA; Seattle-Tacoma-Bellevue; King County; Snoqualmie'], ['99541', 'zip', '98059'], ['99540', 'zip', '98058'], ['99539', 'zip', '98057; WA; Seattle-Tacoma-Bellevue; King County; Renton'], ['99538', 'zip', '98056'], ['99537', 'zip', '98055'], ['99535', 'zip', '98053'], ['99534', 'zip', '98052'], ['99533', 'zip', '98051; WA; Seattle-Tacoma-Bellevue; King County; Ravensdale'], ['99532', 'zip', '98050; WA; Seattle-Tacoma-Bellevue; King County; Issaquah'], ['99531', 'zip', '98047; WA; Seattle-Tacoma-Bellevue; King County; Pacific'], ['9953', 'city', 'Logansport; IN; Cass County'], ['99529', 'zip', '98045; WA; Seattle-Tacoma-Bellevue; King County; North Bend'], ['99528', 'zip', '98043'], ['99527', 'zip', '98042'], ['99525', 'zip', '98040'], ['99524', 'zip', '98039; WA; Seattle-Tacoma-Bellevue; King County; Medina'], ['99523', 'zip', '98038'], ['99522', 'zip', '98037; WA; Seattle-Tacoma-Bellevue; Snohomish County; Lynnwood'], ['99521', 'zip', '98036; WA; Seattle-Tacoma-Bellevue; Snohomish County; Lynnwood'], ['99519', 'zip', '98034'], ['99518', 'zip', '98033'], ['99517', 'zip', '98032'], ['99516', 'zip', '98031'], ['99515', 'zip', '98030'], ['99514', 'zip', '98029'], ['99513', 'zip', '98028; WA; Seattle-Tacoma-Bellevue; King County; Kenmore'], ['99512', 'zip', '98027; WA; Seattle-Tacoma-Bellevue; King County; Issaquah'], ['99511', 'zip', '98026'], ['9951', 'city', 'Lizella; GA; Macon-Bibb County; Bibb County'], ['99509', 'zip', '98024; WA; Seattle-Tacoma-Bellevue; King County; Fall City'], ['99508', 'zip', '98023'], ['99507', 'zip', '98022; WA; Seattle-Tacoma-Bellevue; King County; Enumclaw'], ['99506', 'zip', '98021; WA; Seattle-Tacoma-Bellevue; Snohomish County; Bothell'], ['99505', 'zip', '98020'], ['99504', 'zip', '98019; WA; Seattle-Tacoma-Bellevue; King County; Duvall'], ['99502', 'zip', '98014; WA; Seattle-Tacoma-Bellevue; King County; Carnation'], ['99500', 'zip', '98012'], ['9950', 'city', 'Lincoln; VT; Addison County'], ['995', 'county', 'Donley County; TX'], ['99499', 'zip', '98011'], ['99498', 'zip', '98010; WA; Seattle-Tacoma-Bellevue; King County; Black Diamond'], ['99496', 'zip', '98008; WA; Seattle-Tacoma-Bellevue; King County; Bellevue'], ['99495', 'zip', '98007'], ['99494', 'zip', '98006'], ['99493', 'zip', '98005'], ['99492', 'zip', '98004'], ['99491', 'zip', '98003; WA; Seattle-Tacoma-Bellevue; King County; Federal Way'], ['99490', 'zip', '98002'], ['9949', 'city', 'Lewisburg; TN; Marshall County'], ['99489', 'zip', '98001; WA; Seattle-Tacoma-Bellevue; King County; Auburn'], ['99488', 'zip', '97920; OR; Ontario; Malheur County; Westfall'], ['99487', 'zip', '97918; OR; Ontario; Malheur County; Vale'], ['99485', 'zip', '97914; OR; Ontario; Malheur County'], ['99484', 'zip', '97913; OR; Ontario; Malheur County; Nyssa'], ['99483', 'zip', '97911; OR; Ontario; Malheur County; Juntura'], ['99482', 'zip', '97910; OR; Ontario; Malheur County; Jordan Valley'], ['99481', 'zip', '97909; OR; Ontario; Malheur County; Vale'], ['99480', 'zip', '97908; OR; Ontario; Malheur County; Ironside'], ['99479', 'zip', '97907; OR; Baker County; Huntington'], ['99478', 'zip', '97906; OR; Ontario; Malheur County; Harper'], ['99477', 'zip', '97905; OR; Baker County; Durkee'], ['99476', 'zip', '97904; OR; Harney County; Drewsey'], ['99475', 'zip', '97903; OR; Ontario; Malheur County; Brogan'], ['99474', 'zip', '97902; OR; Ontario; Malheur County; Jordan Valley'], ['99473', 'zip', '97901; OR; Ontario; Malheur County; Adrian'], ['99472', 'zip', '97886; OR; Hermiston-Pendleton; Umatilla County; Weston'], ['99471', 'zip', '97885; OR; Wallowa County; Wallowa'], ['99470', 'zip', '97884; OR; Baker County; Unity'], ['99469', 'zip', '97883; OR; La Grande; Union County; Union'], ['99468', 'zip', '97882; OR; Hermiston-Pendleton; Umatilla County; Umatilla'], ['99467', 'zip', '97880; OR; Hermiston-Pendleton; Umatilla County; Ukiah'], ['99466', 'zip', '97877; OR; Baker County; Sumpter'], ['99465', 'zip', '97876; OR; La Grande; Union County; Summerville'], ['99464', 'zip', '97875; OR; Hermiston-Pendleton; Umatilla County; Stanfield'], ['99463', 'zip', '97874; OR; Wheeler County; Spray'], ['99461', 'zip', '97870; OR; Baker County; Richland'], ['99460', 'zip', '97869; OR; Grant County; Prairie City'], ['99459', 'zip', '97868; OR; Hermiston-Pendleton; Umatilla County; Pilot Rock'], ['99458', 'zip', '97867; OR; La Grande; Union County; North Powder'], ['99457', 'zip', '97865; OR; Grant County; Mount Vernon'], ['99456', 'zip', '97864; OR; Grant County; Mount Vernon'], ['99455', 'zip', '97862; OR; Hermiston-Pendleton; Umatilla County; Milton Freewater'], ['99453', 'zip', '97859; OR; Hermiston-Pendleton; Umatilla County; Pendleton'], ['99452', 'zip', '97857; OR; Wallowa County; Lostine'], ['99450', 'zip', '97850; OR; La Grande; Union County'], ['99449', 'zip', '97848; OR; Grant County; Kimberly'], ['99448', 'zip', '97846; OR; Wallowa County; Joseph'], ['99447', 'zip', '97845; OR; Grant County; John Day'], ['99446', 'zip', '97844; OR; Hermiston-Pendleton; Morrow County; Irrigon'], ['99445', 'zip', '97843; OR; Hermiston-Pendleton; Morrow County; Ione'], ['99444', 'zip', '97842; OR; Wallowa County; Imnaha'], ['99443', 'zip', '97841; OR; La Grande; Union County; Imbler'], ['99442', 'zip', '97840; OR; Baker County; Halfway'], ['99441', 'zip', '97839; OR; Hermiston-Pendleton; Morrow County; Lexington'], ['99440', 'zip', '97838; OR; Hermiston-Pendleton; Umatilla County; Hermiston'], ['99439', 'zip', '97837; OR; Baker County; Hereford'], ['99438', 'zip', '97836; OR; Hermiston-Pendleton; Morrow County; Heppner'], ['99437', 'zip', '97835; OR; Hermiston-Pendleton; Umatilla County; Helix'], ['99436', 'zip', '97834; OR; Baker County; Halfway'], ['99435', 'zip', '97833; OR; Baker County; Haines'], ['99434', 'zip', '97830; OR; Wheeler County; Fossil'], ['99433', 'zip', '97828; OR; Wallowa County; Enterprise'], ['99432', 'zip', '97827; OR; La Grande; Union County; Elgin'], ['99431', 'zip', '97826; OR; Hermiston-Pendleton; Umatilla County; Echo'], ['99430', 'zip', '97825; OR; Grant County; Mount Vernon'], ['9943', 'city', 'Richville; MN; Fergus Falls; Otter Tail County'], ['99429', 'zip', '97824; OR; La Grande; Union County; Cove'], ['99428', 'zip', '97823; OR; Gilliam County; Condon'], ['99427', 'zip', '97820; OR; Grant County; Canyon City'], ['99426', 'zip', '97819; OR; Baker County; Bridgeport'], ['99425', 'zip', '97818; OR; Hermiston-Pendleton; Morrow County; Boardman'], ['99423', 'zip', '97814; OR; Baker County; Baker City'], ['99422', 'zip', '97813; OR; Hermiston-Pendleton; Umatilla County; Athena'], ['99421', 'zip', '97812; OR; Gilliam County; Arlington'], ['99420', 'zip', '97810; OR; Hermiston-Pendleton; Umatilla County; Adams'], ['99419', 'zip', '97801; OR; Hermiston-Pendleton; Umatilla County; Pendleton'], ['99417', 'zip', '97760; OR; Jefferson County; Terrebonne'], ['99416', 'zip', '97759; OR; Bend-Redmond; Deschutes County; Sisters'], ['99414', 'zip', '97756; OR; Bend-Redmond; Deschutes County; Redmond'], ['99413', 'zip', '97754; OR; Prineville; Crook County'], ['99412', 'zip', '97753; OR; Prineville; Crook County; Powell Butte'], ['99411', 'zip', '97752; OR; Prineville; Crook County; Post'], ['99410', 'zip', '97751; OR; Prineville; Crook County; Paulina'], ['9941', 'city', 'Parnell; IA; Iowa County'], ['99409', 'zip', '97750; OR; Wheeler County; Mitchell'], ['99408', 'zip', '97741; OR; Jefferson County; Madras'], ['99407', 'zip', '97739; OR; Bend-Redmond; Deschutes County; La Pine'], ['99406', 'zip', '97738; OR; Harney County; Hines'], ['99405', 'zip', '97737; OR; Klamath Falls; Klamath County; Gilchrist'], ['99404', 'zip', '97736; OR; Harney County; Frenchglen'], ['99402', 'zip', '97734; OR; Jefferson County; Culver'], ['99401', 'zip', '97733; OR; Klamath Falls; Klamath County; Crescent Lake'], ['99400', 'zip', '97732; OR; Harney County; Crane'], ['9940', 'city', 'Palo Cedro; CA; Redding; Shasta County'], ['99399', 'zip', '97731; OR; Klamath Falls; Klamath County; Chemult'], ['99398', 'zip', '97730; OR; Jefferson County; Camp Sherman'], ['99397', 'zip', '97722; OR; Harney County; Diamond'], ['99396', 'zip', '97721; OR; Harney County; Princeton'], ['99395', 'zip', '97720; OR; Harney County; Burns'], ['99394', 'zip', '97712; OR; Bend-Redmond; Deschutes County; Brothers'], ['9939', 'city', 'Muscoda; WI; Platteville; Grant County'], ['99389', 'zip', '97707; OR; Bend-Redmond; Deschutes County; Bend'], ['99388', 'zip', '97702; OR; Bend-Redmond; Deschutes County; Bend'], ['99387', 'zip', '97701; OR; Bend-Redmond; Deschutes County; Bend'], ['99386', 'zip', '97641; OR; Lake County; Christmas Valley'], ['99385', 'zip', '97640; OR; Lake County; Summer Lake'], ['99384', 'zip', '97639; OR; Klamath Falls; Klamath County; Sprague River'], ['99383', 'zip', '97638; OR; Lake County; Silver Lake'], ['99382', 'zip', '97637; OR; Lake County; Plush'], ['99381', 'zip', '97636; OR; Lake County; Paisley'], ['99380', 'zip', '97635; CA; Modoc County; New Pine Creek'], ['99379', 'zip', '97634; OR; Klamath Falls; Klamath County'], ['99378', 'zip', '97633; OR; Klamath Falls; Klamath County; Merrill'], ['99377', 'zip', '97632; OR; Klamath Falls; Klamath County; Malin'], ['99376', 'zip', '97630; OR; Lake County; Lakeview'], ['99375', 'zip', '97627; OR; Klamath Falls; Klamath County; Keno'], ['99374', 'zip', '97626; OR; Klamath Falls; Klamath County; Fort Klamath'], ['99373', 'zip', '97625; OR; Klamath Falls; Klamath County; Dairy'], ['99372', 'zip', '97624; OR; Klamath Falls; Klamath County; Chiloquin'], ['99371', 'zip', '97623; OR; Klamath Falls; Klamath County; Bonanza'], ['99370', 'zip', '97622; OR; Klamath Falls; Klamath County; Bly'], ['9937', 'city', 'Mooreton; ND; Wahpeton; Richland County'], ['99369', 'zip', '97621; OR; Klamath Falls; Klamath County; Beatty'], ['99366', 'zip', '97603; OR; Klamath Falls; Klamath County'], ['99364', 'zip', '97601; OR; Klamath Falls; Klamath County'], ['99363', 'zip', '97544; OR; Grants Pass; Josephine County; Williams'], ['99362', 'zip', '97543; OR; Grants Pass; Josephine County'], ['99361', 'zip', '97541; OR; Medford; Jackson County; Trail'], ['99360', 'zip', '97540; OR; Medford; Jackson County; Talent'], ['9936', 'city', 'Hartford; MI; Kalamazoo-Portage; Van Buren County'], ['99359', 'zip', '97539; OR; Medford; Jackson County; Shady Cove'], ['99358', 'zip', '97538; OR; Grants Pass; Josephine County; Selma'], ['99357', 'zip', '97537; OR; Medford; Jackson County; Rogue River'], ['99356', 'zip', '97536; OR; Medford; Jackson County; Prospect'], ['99355', 'zip', '97535; OR; Medford; Jackson County; Phoenix'], ['99354', 'zip', '97534; OR; Grants Pass; Josephine County; O Brien'], ['99352', 'zip', '97532; OR; Grants Pass; Josephine County; Merlin'], ['99351', 'zip', '97531; OR; Grants Pass; Josephine County; Cave Junction'], ['99350', 'zip', '97530; OR; Medford; Jackson County; Jacksonville'], ['99348', 'zip', '97527; OR; Grants Pass; Josephine County'], ['99347', 'zip', '97526; OR; Grants Pass; Josephine County'], ['99346', 'zip', '97525; OR; Medford; Jackson County; Gold Hill'], ['99345', 'zip', '97524; OR; Medford; Jackson County; Eagle Point'], ['99344', 'zip', '97523; OR; Grants Pass; Josephine County; Cave Junction'], ['99343', 'zip', '97522; OR; Medford; Jackson County; Butte Falls'], ['99342', 'zip', '97520; OR; Medford; Jackson County; Ashland'], ['99341', 'zip', '97504; OR; Medford; Jackson County'], ['99340', 'zip', '97503; OR; Medford; Jackson County; White City'], ['99339', 'zip', '97502; OR; Medford; Jackson County; Central Point'], ['99338', 'zip', '97501; OR; Medford; Jackson County'], ['99337', 'zip', '97499; OR; Roseburg; Douglas County; Yoncalla'], ['99336', 'zip', '97498; OR; Newport; Lincoln County; Yachats'], ['99335', 'zip', '97497; OR; Grants Pass; Josephine County; Wolf Creek'], ['99334', 'zip', '97496; OR; Roseburg; Douglas County; Winston'], ['99333', 'zip', '97495; OR; Roseburg; Douglas County; Winchester'], ['99331', 'zip', '97493; OR; Eugene; Lane County; Dunes City'], ['99330', 'zip', '97492; OR; Eugene; Lane County; Westfir'], ['99328', 'zip', '97490; OR; Eugene; Lane County; Walton'], ['99327', 'zip', '97489; OR; Eugene; Lane County; Walterville'], ['99326', 'zip', '97488; OR; Eugene; Lane County; Vida'], ['99325', 'zip', '97487; OR; Eugene; Lane County; Veneta'], ['99324', 'zip', '97486; OR; Roseburg; Douglas County; Umpqua'], ['99323', 'zip', '97484; OR; Roseburg; Douglas County; Tiller'], ['99321', 'zip', '97481; OR; Roseburg; Douglas County; Tenmile'], ['99320', 'zip', '97480; OR; Eugene; Lane County; Swisshome'], ['9932', 'city', 'Hancock; WI; Waushara County'], ['99319', 'zip', '97479; OR; Roseburg; Douglas County; Sutherlin'], ['99318', 'zip', '97478; OR; Eugene; Lane County; Springfield'], ['99317', 'zip', '97477; OR; Eugene; Lane County; Springfield'], ['99316', 'zip', '97476; OR; Brookings; Curry County; Sixes'], ['99315', 'zip', '97473; OR; Roseburg; Douglas County; Scottsburg'], ['99313', 'zip', '97470; OR; Roseburg; Douglas County'], ['99312', 'zip', '97469; OR; Roseburg; Douglas County; Riddle'], ['99311', 'zip', '97467; OR; Roseburg; Douglas County; Reedsport'], ['99310', 'zip', '97466; OR; Coos Bay; Coos County; Powers'], ['9931', 'city', 'Jacumba; CA; San Diego-Carlsbad; San Diego County'], ['99309', 'zip', '97465; OR; Brookings; Curry County; Port Orford'], ['99307', 'zip', '97463; OR; Eugene; Lane County; Oakridge'], ['99306', 'zip', '97462; OR; Roseburg; Douglas County; Oakland'], ['99305', 'zip', '97461; OR; Eugene; Lane County; Noti'], ['99304', 'zip', '97459; OR; Coos Bay; Coos County; North Bend'], ['99303', 'zip', '97458; OR; Coos Bay; Coos County; Myrtle Point'], ['99302', 'zip', '97457; OR; Roseburg; Douglas County; Myrtle Creek'], ['99301', 'zip', '97456; OR; Corvallis; Benton County; Monroe'], ['99300', 'zip', '97455; OR; Eugene; Lane County; Pleasant Hill'], ['993', 'county', 'Dimmit County; TX'], ['99299', 'zip', '97454; OR; Eugene; Lane County; Marcola'], ['99298', 'zip', '97453; OR; Eugene; Lane County; Mapleton'], ['99297', 'zip', '97452; OR; Eugene; Lane County; Lowell'], ['99296', 'zip', '97451; OR; Eugene; Lane County; Lorane'], ['99295', 'zip', '97450; OR; Brookings; Curry County; Langlois'], ['99294', 'zip', '97449; OR; Coos Bay; Coos County; Lakeside'], ['99293', 'zip', '97448; OR; Eugene; Lane County; Junction City'], ['99292', 'zip', '97447; OR; Roseburg; Douglas County; Idleyld Park'], ['99291', 'zip', '97446; OR; Albany; Linn County; Harrisburg'], ['99290', 'zip', '97444; OR; Brookings; Curry County; Gold Beach'], ['99289', 'zip', '97443; OR; Roseburg; Douglas County; Glide'], ['99288', 'zip', '97442; OR; Roseburg; Douglas County; Glendale'], ['99287', 'zip', '97441; OR; Roseburg; Douglas County; Gardiner'], ['99285', 'zip', '97439; OR; Eugene; Lane County; Florence'], ['99284', 'zip', '97438; OR; Eugene; Lane County; Fall Creek'], ['99283', 'zip', '97437; OR; Eugene; Lane County; Elmira'], ['99282', 'zip', '97436; OR; Roseburg; Douglas County; Elkton'], ['99281', 'zip', '97435; OR; Roseburg; Douglas County; Drain'], ['99280', 'zip', '97434; OR; Eugene; Lane County; Dorena'], ['99279', 'zip', '97432; OR; Roseburg; Douglas County; Winston'], ['99278', 'zip', '97431; OR; Eugene; Lane County; Dexter'], ['99277', 'zip', '97430; OR; Eugene; Lane County; Deadwood'], ['99276', 'zip', '97429; OR; Roseburg; Douglas County; Days Creek'], ['99273', 'zip', '97426; OR; Eugene; Lane County; Creswell'], ['99271', 'zip', '97424; OR; Eugene; Lane County; Cottage Grove'], ['99270', 'zip', '97423; OR; Coos Bay; Coos County; Coquille'], ['99269', 'zip', '97420; OR; Coos Bay; Coos County'], ['99268', 'zip', '97419; OR; Eugene; Lane County; Cheshire'], ['99267', 'zip', '97417; OR; Roseburg; Douglas County; Canyonville'], ['99266', 'zip', '97416; OR; Roseburg; Douglas County; Camas Valley'], ['99265', 'zip', '97415; OR; Brookings; Curry County'], ['99264', 'zip', '97414; OR; Coos Bay; Coos County; Broadbent'], ['99263', 'zip', '97413; OR; Eugene; Lane County; Blue River'], ['99262', 'zip', '97412; OR; Eugene; Lane County; Blachly'], ['99261', 'zip', '97411; OR; Coos Bay; Coos County; Bandon'], ['99260', 'zip', '97410; OR; Roseburg; Douglas County; Azalea'], ['9926', 'neigh', 'Hunter Army Airfield; GA; Savannah; Chatham County'], ['99258', 'zip', '97408; OR; Eugene; Lane County'], ['99256', 'zip', '97406; OR; Brookings; Curry County; Agness'], ['99255', 'zip', '97405; OR; Eugene; Lane County'], ['99254', 'zip', '97404; OR; Eugene; Lane County'], ['99253', 'zip', '97403; OR; Eugene; Lane County'], ['99252', 'zip', '97402; OR; Eugene; Lane County'], ['99251', 'zip', '97401; OR; Eugene; Lane County'], ['99250', 'zip', '97396; OR; Portland-Vancouver-Hillsboro; Yamhill County; Willamina'], ['99249', 'zip', '97394; OR; Newport; Lincoln County; Waldport'], ['99248', 'zip', '97392; OR; Salem; Marion County; Turner'], ['99247', 'zip', '97391; OR; Newport; Lincoln County; Toledo'], ['99246', 'zip', '97390; OR; Newport; Lincoln County; Tidewater'], ['99245', 'zip', '97389; OR; Albany; Linn County; Tangent'], ['99244', 'zip', '97388; OR; Newport; Lincoln County; Lincoln City'], ['99243', 'zip', '97386; OR; Albany; Linn County; Sweet Home'], ['99242', 'zip', '97385; OR; Salem; Marion County; Sublimity'], ['99240', 'zip', '97383; OR; Salem; Marion County; Stayton'], ['99239', 'zip', '97381; OR; Salem; Marion County; Silverton'], ['99238', 'zip', '97380; OR; Newport; Lincoln County; Siletz'], ['99237', 'zip', '97378; OR; Portland-Vancouver-Hillsboro; Yamhill County; Sheridan'], ['99236', 'zip', '97377; OR; Albany; Linn County; Shedd'], ['99235', 'zip', '97376; OR; Newport; Lincoln County; Seal Rock'], ['99234', 'zip', '97375; OR; Salem; Marion County; Scotts Mills'], ['99233', 'zip', '97374; OR; Albany; Linn County; Scio'], ['99230', 'zip', '97371; OR; Salem; Polk County; Rickreall'], ['99229', 'zip', '97370; OR; Corvallis; Benton County; Philomath'], ['99228', 'zip', '97369; OR; Newport; Lincoln County; Otter Rock'], ['99227', 'zip', '97368; OR; Newport; Lincoln County; Otis'], ['99226', 'zip', '97367; OR; Newport; Lincoln County; Lincoln City'], ['99225', 'zip', '97366; OR; Newport; Lincoln County'], ['99224', 'zip', '97365; OR; Newport; Lincoln County'], ['99223', 'zip', '97364; OR; Newport; Lincoln County; Neotsu'], ['99222', 'zip', '97362; OR; Salem; Marion County; Mount Angel'], ['99221', 'zip', '97361; OR; Salem; Polk County; Monmouth'], ['99220', 'zip', '97360; OR; Albany; Linn County; Mill City'], ['9922', 'city', 'Hamburg; PA; Reading; Berks County'], ['99219', 'zip', '97358; OR; Albany; Linn County; Lyons'], ['99218', 'zip', '97357; OR; Newport; Lincoln County; Logsden'], ['99217', 'zip', '97355; OR; Albany; Linn County; Lebanon'], ['99216', 'zip', '97352; OR; Salem; Marion County; Jefferson'], ['99215', 'zip', '97351; OR; Salem; Polk County; Independence'], ['99214', 'zip', '97350; OR; Albany; Linn County; Idanha'], ['99213', 'zip', '97348; OR; Albany; Linn County; Halsey'], ['99212', 'zip', '97347; OR; Salem; Polk County; Grand Ronde'], ['99211', 'zip', '97346; OR; Salem; Marion County; Gates'], ['99210', 'zip', '97345; OR; Albany; Linn County; Foster'], ['9921', 'city', 'Plumerville; AR; Conway County'], ['99209', 'zip', '97344; OR; Salem; Polk County; Falls City'], ['99208', 'zip', '97343; OR; Newport; Lincoln County; Eddyville'], ['99207', 'zip', '97342; OR; Salem; Marion County; Detroit'], ['99206', 'zip', '97341; OR; Newport; Lincoln County; Depoe Bay'], ['99204', 'zip', '97338; OR; Salem; Polk County; Dallas'], ['99203', 'zip', '97336; OR; Albany; Linn County; Sweet Home'], ['99202', 'zip', '97335; OR; Albany; Linn County; Scio'], ['99201', 'zip', '97333; OR; Corvallis; Benton County'], ['992', 'county', 'Dillon County; SC'], ['99199', 'zip', '97330; OR; Corvallis; Benton County'], ['99198', 'zip', '97329; OR; Albany; Linn County; Cascadia'], ['99197', 'zip', '97327; OR; Albany; Linn County; Brownsville'], ['99196', 'zip', '97326; OR; Corvallis; Benton County; Blodgett'], ['99195', 'zip', '97325; OR; Salem; Marion County; Aumsville'], ['99194', 'zip', '97324; OR; Corvallis; Benton County; Alsea'], ['99193', 'zip', '97322; OR; Albany; Linn County'], ['99192', 'zip', '97321; OR; Albany; Linn County'], ['9919', 'city', 'Franklin; AL; Macon County'], ['99183', 'zip', '97306; OR; Salem; Marion County'], ['99182', 'zip', '97305; OR; Salem; Marion County; Hayesville'], ['99181', 'zip', '97304; OR; Salem; Marion County'], ['99180', 'zip', '97303; OR; Salem; Marion County; Keizer'], ['99179', 'zip', '97302; OR; Salem; Marion County'], ['99178', 'zip', '97301; OR; Salem; Marion County'], ['99160', 'zip', '97267'], ['99159', 'zip', '97266'], ['9915', 'city', 'Melvern; KS; Topeka; Osage County'], ['99149', 'zip', '97239'], ['99147', 'zip', '97236'], ['99146', 'zip', '97233'], ['99145', 'zip', '97232; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99144', 'zip', '97231; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99143', 'zip', '97230; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99142', 'zip', '97229'], ['99140', 'zip', '97227; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99139', 'zip', '97225'], ['99138', 'zip', '97224'], ['99137', 'zip', '97223'], ['99136', 'zip', '97222'], ['99135', 'zip', '97221; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99134', 'zip', '97220'], ['99133', 'zip', '97219'], ['99132', 'zip', '97218; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99131', 'zip', '97217'], ['99130', 'zip', '97216'], ['99129', 'zip', '97215; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99128', 'zip', '97214'], ['99127', 'zip', '97213'], ['99126', 'zip', '97212'], ['99125', 'zip', '97211'], ['99124', 'zip', '97210'], ['99123', 'zip', '97209'], ['99120', 'zip', '97206'], ['99119', 'zip', '97205; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99118', 'zip', '97204; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'], ['99117', 'zip', '97203'], ['99116', 'zip', '97202'], ['99115', 'zip', '97201'], ['99114', 'zip', '97149; OR; Tillamook County; Neskowin'], ['99113', 'zip', '97148; OR; Portland-Vancouver-Hillsboro; Yamhill County; Yamhill'], ['99112', 'zip', '97147; OR; Tillamook County; Wheeler'], ['99111', 'zip', '97146; OR; Astoria; Clatsop County; Warrenton'], ['9911', 'city', 'Nachusa; IL; Dixon; Lee County'], ['99109', 'zip', '97144; OR; Portland-Vancouver-Hillsboro; Washington County; Timber'], ['99108', 'zip', '97143; OR; Tillamook County; Tillamook'], ['99107', 'zip', '97141; OR; Tillamook County; Tillamook'], ['99106', 'zip', '97140'], ['99105', 'zip', '97138; OR; Astoria; Clatsop County; Seaside'], ['99104', 'zip', '97137; OR; Salem; Marion County; Saint Paul'], ['99103', 'zip', '97136; OR; Tillamook County; Rockaway Beach'], ['99102', 'zip', '97135; OR; Tillamook County; Cloverdale'], ['99101', 'zip', '97134; OR; Tillamook County; Tillamook'], ['99100', 'zip', '97133; OR; Portland-Vancouver-Hillsboro; Washington County; North Plains'], ['99099', 'zip', '97132'], ['99098', 'zip', '97131; OR; Tillamook County; Nehalem'], ['99097', 'zip', '97130; OR; Tillamook County; Nehalem'], ['99096', 'zip', '97128; OR; Portland-Vancouver-Hillsboro; Yamhill County; McMinnville'], ['99095', 'zip', '97127; OR; Portland-Vancouver-Hillsboro; Yamhill County; Lafayette'], ['99094', 'zip', '97125; OR; Portland-Vancouver-Hillsboro; Washington County; Banks'], ['99093', 'zip', '97124'], ['99092', 'zip', '97123; OR; Portland-Vancouver-Hillsboro; Washington County; Hillsboro'], ['99091', 'zip', '97122; OR; Tillamook County; Hebo'], ['99089', 'zip', '97119; OR; Portland-Vancouver-Hillsboro; Washington County; Gaston'], ['99088', 'zip', '97118; OR; Tillamook County; Garibaldi'], ['99087', 'zip', '97117; OR; Portland-Vancouver-Hillsboro; Washington County; Gales Creek'], ['99086', 'zip', '97116; OR; Portland-Vancouver-Hillsboro; Washington County; Forest Grove'], ['99085', 'zip', '97115; OR; Portland-Vancouver-Hillsboro; Yamhill County; Dundee'], ['99084', 'zip', '97114; OR; Portland-Vancouver-Hillsboro; Yamhill County; Dayton'], ['99083', 'zip', '97113; OR; Portland-Vancouver-Hillsboro; Washington County; Cornelius'], ['99082', 'zip', '97112; OR; Tillamook County; Cloverdale'], ['99081', 'zip', '97111; OR; Portland-Vancouver-Hillsboro; Yamhill County; Carlton'], ['99080', 'zip', '97110; OR; Astoria; Clatsop County; Cannon Beach'], ['9908', 'city', 'Gleason; WI; Merrill; Lincoln County'], ['99079', 'zip', '97109; OR; Portland-Vancouver-Hillsboro; Washington County; Buxton'], ['99078', 'zip', '97108; OR; Tillamook County; Beaver'], ['99077', 'zip', '97107; OR; Tillamook County; Bay City'], ['99076', 'zip', '97106; OR; Portland-Vancouver-Hillsboro; Washington County; Banks'], ['99075', 'zip', '97103; OR; Astoria; Clatsop County'], ['99074', 'zip', '97102; OR; Astoria; Clatsop County; Arch Cape'], ['99073', 'zip', '97101; OR; Portland-Vancouver-Hillsboro; Yamhill County; Amity'], ['99072', 'zip', '97080'], ['99071', 'zip', '97078'], ['9907', 'city', 'Gilbert; WV; Mingo County'], ['99067', 'zip', '97071; OR; Salem; Marion County; Woodburn'], ['99066', 'zip', '97070; OR; Portland-Vancouver-Hillsboro; Clackamas County; Wilsonville'], ['99065', 'zip', '97068'], ['99064', 'zip', '97067; OR; Portland-Vancouver-Hillsboro; Clackamas County; Welches'], ['99063', 'zip', '97065; OR; Sherman County; Wasco'], ['99062', 'zip', '97064; OR; Portland-Vancouver-Hillsboro; Columbia County; Vernonia'], ['99061', 'zip', '97063; OR; The Dalles; Wasco County; Tygh Valley'], ['99060', 'zip', '97062; OR; Portland-Vancouver-Hillsboro; Washington County; Tualatin'], ['9906', 'city', 'Fulton; KS; Bourbon County'], ['99059', 'zip', '97060; OR; Portland-Vancouver-Hillsboro; Multnomah County; Troutdale'], ['99058', 'zip', '97058; OR; The Dalles; Wasco County'], ['99057', 'zip', '97057; OR; The Dalles; Wasco County; Shaniko'], ['99056', 'zip', '97056; OR; Portland-Vancouver-Hillsboro; Columbia County; Scappoose'], ['99055', 'zip', '97055; OR; Portland-Vancouver-Hillsboro; Clackamas County; Sandy'], ['99054', 'zip', '97054; OR; Portland-Vancouver-Hillsboro; Columbia County; Deer Island'], ['99053', 'zip', '97053; OR; Portland-Vancouver-Hillsboro; Columbia County; Warren'], ['99052', 'zip', '97051; OR; Portland-Vancouver-Hillsboro; Columbia County; Saint Helens'], ['99051', 'zip', '97050; OR; Sherman County; Rufus'], ['99050', 'zip', '97049; OR; Portland-Vancouver-Hillsboro; Clackamas County; Rhododendron'], ['99049', 'zip', '97048; OR; Portland-Vancouver-Hillsboro; Columbia County; Rainier'], ['99048', 'zip', '97045'], ['99046', 'zip', '97042; OR; Portland-Vancouver-Hillsboro; Clackamas County; Mulino'], ['99045', 'zip', '97041; OR; Hood River; Hood River County; Mount Hood Parkdale'], ['99044', 'zip', '97040; OR; The Dalles; Wasco County; Mosier'], ['99043', 'zip', '97039; OR; Sherman County; Moro'], ['99042', 'zip', '97038; OR; Portland-Vancouver-Hillsboro; Clackamas County; Molalla'], ['99041', 'zip', '97037; OR; The Dalles; Wasco County; Maupin'], ['9904', 'city', 'Fort Hall; ID; Pocatello; Bannock County'], ['99039', 'zip', '97035; OR; Portland-Vancouver-Hillsboro; Clackamas County; Lake Oswego'], ['99038', 'zip', '97034'], ['99036', 'zip', '97032; OR; Salem; Marion County; Hubbard'], ['99035', 'zip', '97031; OR; Hood River; Hood River County'], ['99034', 'zip', '97030'], ['99032', 'zip', '97028; OR; Portland-Vancouver-Hillsboro; Clackamas County; Rhododendron'], ['99031', 'zip', '97027; OR; Portland-Vancouver-Hillsboro; Clackamas County; Gladstone'], ['99030', 'zip', '97026; OR; Salem; Marion County; Gervais'], ['9903', 'city', 'Floydada; TX; Floyd County'], ['99029', 'zip', '97024; OR; Portland-Vancouver-Hillsboro; Multnomah County; Fairview'], ['99028', 'zip', '97023; OR; Portland-Vancouver-Hillsboro; Clackamas County; Estacada'], ['99027', 'zip', '97022; OR; Portland-Vancouver-Hillsboro; Clackamas County; Eagle Creek'], ['99026', 'zip', '97021; OR; The Dalles; Wasco County; Dufur'], ['99025', 'zip', '97020; OR; Salem; Marion County; Donald'], ['99024', 'zip', '97019; OR; Portland-Vancouver-Hillsboro; Multnomah County; Corbett'], ['99023', 'zip', '97018; OR; Portland-Vancouver-Hillsboro; Columbia County; Columbia City'], ['99022', 'zip', '97017; OR; Portland-Vancouver-Hillsboro; Clackamas County; Colton'], ['99021', 'zip', '97016; OR; Portland-Vancouver-Hillsboro; Columbia County; Clatskanie'], ['99020', 'zip', '97015; OR; Portland-Vancouver-Hillsboro; Clackamas County; Clackamas'], ['9902', 'city', 'Peck; KS; Wichita; Sumner County'], ['99019', 'zip', '97014; OR; Hood River; Hood River County; Cascade Locks'], ['99018', 'zip', '97013; OR; Portland-Vancouver-Hillsboro; Clackamas County; Canby'], ['99017', 'zip', '97011; OR; Portland-Vancouver-Hillsboro; Clackamas County; Brightwood'], ['99015', 'zip', '97009; OR; Portland-Vancouver-Hillsboro; Clackamas County; Boring'], ['99014', 'zip', '97008'], ['99013', 'zip', '97007'], ['99012', 'zip', '97006'], ['99011', 'zip', '97005'], ['99010', 'zip', '97004; OR; Portland-Vancouver-Hillsboro; Clackamas County; Beavercreek'], ['99009', 'zip', '97002; OR; Salem; Marion County; Aurora'], ['99008', 'zip', '97001; OR; The Dalles; Wasco County; Antelope'], ['990', 'county', 'Dewitt County; IL; Bloomington'], ['99', 'county', 'Bent County; CO'], ['9896', 'city', 'Fincastle; VA; Roanoke; Botetourt County'], ['98957', 'zip', '96826'], ['98956', 'zip', '96825; HI; Urban Honolulu; Honolulu County; Honolulu'], ['98953', 'zip', '96822'], ['98952', 'zip', '96821; HI; Urban Honolulu; Honolulu County; Honolulu'], ['98950', 'zip', '96819; HI; Urban Honolulu; Honolulu County; Honolulu'], ['98949', 'zip', '96818'], ['98948', 'zip', '96817'], ['98947', 'zip', '96816'], ['98946', 'zip', '96815'], ['98945', 'zip', '96814'], ['98944', 'zip', '96813'], ['98930', 'zip', '96797'], ['98929', 'zip', '96796; HI; Kapaa; Kauai County; Waimea'], ['98928', 'zip', '96795; HI; Urban Honolulu; Honolulu County; Waimanalo'], ['98927', 'zip', '96793; HI; Kahului-Wailuku-Lahaina; Maui County; Wailuku'], ['98926', 'zip', '96792'], ['98925', 'zip', '96791; HI; Urban Honolulu; Honolulu County; Waialua'], ['98924', 'zip', '96790; HI; Kahului-Wailuku-Lahaina; Maui County; Kihei'], ['98923', 'zip', '96789'], ['98921', 'zip', '96786'], ['98920', 'zip', '96785; HI; Hilo; Hawaii County; Volcano'], ['98918', 'zip', '96783; HI; Hilo; Hawaii County; Pepeekeo'], ['98917', 'zip', '96782; HI; Urban Honolulu; Honolulu County; Pearl City'], ['98916', 'zip', '96781; HI; Hilo; Hawaii County; Papaikou'], ['98915', 'zip', '96780; HI; Hilo; Hawaii County; Ninole'], ['98914', 'zip', '96779; HI; Kahului-Wailuku-Lahaina; Maui County; Paia'], ['98913', 'zip', '96778; HI; Hilo; Hawaii County; Pahoa'], ['98912', 'zip', '96777; HI; Hilo; Hawaii County; Pahala'], ['98911', 'zip', '96776; HI; Hilo; Hawaii County; Paauilo'], ['98910', 'zip', '96774; HI; Hilo; Hawaii County; Ookala'], ['9891', 'city', 'Moriarty; NM; Albuquerque; Torrance County'], ['98909', 'zip', '96773; HI; Hilo; Hawaii County; Ninole'], ['98908', 'zip', '96772; HI; Hilo; Hawaii County; Naalehu'], ['98907', 'zip', '96771; HI; Hilo; Hawaii County; Mountain View'], ['98906', 'zip', '96770; HI; Kahului-Wailuku-Lahaina; Maui County; Maunaloa'], ['98904', 'zip', '96768; HI; Kahului-Wailuku-Lahaina; Maui County; Makawao'], ['98902', 'zip', '96766; HI; Kapaa; Kauai County; Lihue'], ['98901', 'zip', '96765; HI; Kapaa; Kauai County; Koloa'], ['98900', 'zip', '96764; HI; Hilo; Hawaii County; Laupahoehoe'], ['989', 'county', 'Denver County; CO; Denver-Aurora-Lakewood'], ['98899', 'zip', '96763; HI; Kahului-Wailuku-Lahaina; Maui County; Lanai City'], ['98898', 'zip', '96762; HI; Urban Honolulu; Honolulu County; Laie'], ['98897', 'zip', '96761; HI; Kahului-Wailuku-Lahaina; Maui County; Lahaina'], ['98896', 'zip', '96760; HI; Hilo; Hawaii County; Keaau'], ['98894', 'zip', '96757; HI; Kahului-Wailuku-Lahaina; Maui County; Kaunakakai'], ['98893', 'zip', '96756; HI; Kapaa; Kauai County; Koloa'], ['98892', 'zip', '96755; HI; Hilo; Hawaii County; Kapaau'], ['98891', 'zip', '96754; HI; Kapaa; Kauai County; Kilauea'], ['98890', 'zip', '96753; HI; Kahului-Wailuku-Lahaina; Maui County; Kihei'], ['9889', 'city', 'Ducor; CA; Visalia-Porterville; Tulare County'], ['98889', 'zip', '96752; HI; Kapaa; Kauai County; Waimea'], ['98888', 'zip', '96751; HI; Kapaa; Kauai County'], ['98887', 'zip', '96750; HI; Hilo; Hawaii County; Kealakekua'], ['98886', 'zip', '96749; HI; Hilo; Hawaii County; Keaau'], ['98885', 'zip', '96748; HI; Kahului-Wailuku-Lahaina; Maui County; Kaunakakai'], ['98883', 'zip', '96746; HI; Kapaa; Kauai County'], ['98881', 'zip', '96744'], ['98880', 'zip', '96743; HI; Hilo; Hawaii County; Waimea'], ['98878', 'zip', '96741; HI; Kapaa; Kauai County; Kalaheo'], ['98877', 'zip', '96740; HI; Hilo; Hawaii County; Kailua Kona'], ['98875', 'zip', '96738; HI; Hilo; Hawaii County; Waikoloa'], ['98874', 'zip', '96737; HI; Hilo; Hawaii County; Ocean View'], ['98873', 'zip', '96734'], ['98871', 'zip', '96732; HI; Kahului-Wailuku-Lahaina; Maui County; Kahului'], ['98870', 'zip', '96731; HI; Urban Honolulu; Honolulu County; Kahuku'], ['9887', 'city', 'Doland; SD; Spink County'], ['98869', 'zip', '96730; HI; Urban Honolulu; Honolulu County; Kaaawa'], ['98868', 'zip', '96729; HI; Kahului-Wailuku-Lahaina; Maui County; Hoolehua'], ['98867', 'zip', '96728; HI; Hilo; Hawaii County; Honomu'], ['98866', 'zip', '96727; HI; Hilo; Hawaii County; Honokaa'], ['98864', 'zip', '96725; HI; Hilo; Hawaii County; Holualoa'], ['98863', 'zip', '96722; HI; Kapaa; Kauai County; Princeville'], ['98861', 'zip', '96720; HI; Hilo; Hawaii County'], ['98860', 'zip', '96719; HI; Hilo; Hawaii County; Kapaau'], ['98859', 'zip', '96718; HI; Hilo; Hawaii County; Volcano'], ['98858', 'zip', '96717; HI; Urban Honolulu; Honolulu County; Hauula'], ['98857', 'zip', '96716; HI; Kapaa; Kauai County; Hanapepe'], ['98855', 'zip', '96714; HI; Kapaa; Kauai County; Kilauea'], ['98854', 'zip', '96713; HI; Kahului-Wailuku-Lahaina; Maui County; Hana'], ['98853', 'zip', '96712; HI; Urban Honolulu; Honolulu County; Haleiwa'], ['98852', 'zip', '96710; HI; Hilo; Hawaii County; Honomu'], ['98850', 'zip', '96708; HI; Kahului-Wailuku-Lahaina; Maui County; Haiku'], ['98849', 'zip', '96707'], ['98848', 'zip', '96706'], ['98847', 'zip', '96705; HI; Kapaa; Kauai County; Hanapepe'], ['98846', 'zip', '96704; HI; Hilo; Hawaii County; Captain Cook'], ['98845', 'zip', '96703; HI; Kapaa; Kauai County; Kilauea'], ['98844', 'zip', '96701'], ['9882', 'city', 'Everton; AR; Harrison; Boone County'], ['9880', 'city', 'Eure; NC; Virginia Beach-Norfolk-Newport News; Gates County'], ['988', 'county', 'Denton County; TX; Dallas-Fort Worth-Arlington'], ['9878', 'city', 'Elk Creek; CA; Glenn County'], ['9876', 'city', 'Eastpoint; FL; Franklin County'], ['9875', 'city', 'Osmond; NE; Norfolk; Pierce County'], ['9872', 'city', 'Onley; VA; Accomack County'], ['9870', 'city', 'Oakfield; ME; Aroostook County'], ['98673', 'zip', '96162; CA; Truckee-Grass Valley; Nevada County; Truckee'], ['98672', 'zip', '96161; CA; Truckee-Grass Valley; Nevada County; Truckee'], ['9867', 'city', 'Ellis; ID; Custer County'], ['98663', 'zip', '96150'], ['98662', 'zip', '96148; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe Vista'], ['98661', 'zip', '96146; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe City'], ['98660', 'zip', '96145; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe City'], ['9866', 'city', 'Edisto Beach; SC; Colleton County'], ['98659', 'zip', '96143; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Kings Beach'], ['98658', 'zip', '96142; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Tahoma'], ['98657', 'zip', '96141; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Homewood'], ['98656', 'zip', '96140; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Carnelian Bay'], ['98655', 'zip', '96137; CA; Plumas County; Westwood'], ['98654', 'zip', '96136; CA; Susanville; Lassen County; Wendel'], ['98653', 'zip', '96135; CA; Plumas County; Chilcoot'], ['98652', 'zip', '96134; CA; Modoc County; Tulelake'], ['98651', 'zip', '96133; CA; Mono County; Coleville'], ['98650', 'zip', '96132; CA; Susanville; Lassen County; Termo'], ['98649', 'zip', '96130; CA; Susanville; Lassen County'], ['98648', 'zip', '96129; CA; Plumas County; Beckwourth'], ['98647', 'zip', '96128; CA; Susanville; Lassen County; Standish'], ['98646', 'zip', '96127; CA; Susanville; Lassen County; Janesville'], ['98645', 'zip', '96126; CA; Sierra County; Sierraville'], ['98644', 'zip', '96125; CA; Sierra County; Sierra City'], ['98643', 'zip', '96124; CA; Sierra County; Calpine'], ['98642', 'zip', '96123; CA; Susanville; Lassen County; Ravendale'], ['98641', 'zip', '96122; CA; Plumas County; Portola'], ['98640', 'zip', '96121; CA; Susanville; Lassen County; Milford'], ['9864', 'city', 'Lake Quivira; KS; Kansas City; Johnson County'], ['98639', 'zip', '96120; CA; Alpine County; Kirkwood'], ['98638', 'zip', '96119; CA; Susanville; Lassen County; Madeline'], ['98637', 'zip', '96118; CA; Sierra County; Loyalton'], ['98636', 'zip', '96117; CA; Susanville; Lassen County; Litchfield'], ['98635', 'zip', '96116; CA; Modoc County; Likely'], ['98634', 'zip', '96115; CA; Modoc County; Lake City'], ['98633', 'zip', '96114; CA; Susanville; Lassen County; Janesville'], ['98632', 'zip', '96113; CA; Susanville; Lassen County; Doyle'], ['98631', 'zip', '96112; CA; Modoc County; Fort Bidwell'], ['98630', 'zip', '96111; CA; Truckee-Grass Valley; Nevada County; Truckee'], ['98629', 'zip', '96110; CA; Modoc County; Cedarville'], ['98628', 'zip', '96109; CA; Susanville; Lassen County; Doyle'], ['98627', 'zip', '96108; CA; Modoc County; New Pine Creek'], ['98626', 'zip', '96107; CA; Mono County; Coleville'], ['98625', 'zip', '96106; CA; Plumas County; Clio'], ['98624', 'zip', '96105; CA; Plumas County; Chilcoot'], ['98623', 'zip', '96104; CA; Modoc County; Cedarville'], ['98622', 'zip', '96103; CA; Plumas County; Graeagle'], ['98621', 'zip', '96101; CA; Modoc County; Alturas'], ['98619', 'zip', '96097; CA; Siskiyou County; Yreka'], ['98618', 'zip', '96096; CA; Redding; Shasta County; Whitmore'], ['98616', 'zip', '96094; CA; Siskiyou County; Weed'], ['98615', 'zip', '96093; CA; Trinity County; Weaverville'], ['98614', 'zip', '96092; CA; Chico; Butte County'], ['98613', 'zip', '96091; CA; Trinity County; Trinity Center'], ['98612', 'zip', '96090; CA; Red Bluff; Tehama County; Tehama'], ['98611', 'zip', '96089; CA; Redding; Shasta County; Shasta Lake'], ['98610', 'zip', '96088; CA; Redding; Shasta County; Shingletown'], ['98609', 'zip', '96087; CA; Redding; Shasta County'], ['98608', 'zip', '96086; CA; Siskiyou County; Seiad Valley'], ['98607', 'zip', '96085; CA; Siskiyou County; Klamath River'], ['98606', 'zip', '96084; CA; Redding; Shasta County; Round Mountain'], ['98605', 'zip', '96080; CA; Red Bluff; Tehama County'], ['98602', 'zip', '96076; CA; Trinity County; Hayfork'], ['98601', 'zip', '96075; CA; Red Bluff; Tehama County; Paynes Creek'], ['9860', 'city', 'Frontenac; KS; Pittsburg; Crawford County'], ['986', 'county', 'Dekalb County; TN'], ['98599', 'zip', '96073; CA; Redding; Shasta County; Palo Cedro'], ['98598', 'zip', '96071; CA; Redding; Shasta County; Old Station'], ['98596', 'zip', '96069; CA; Redding; Shasta County; Oak Run'], ['98595', 'zip', '96068; CA; Susanville; Lassen County; Nubieber'], ['98594', 'zip', '96067; CA; Siskiyou County; Mount Shasta'], ['98593', 'zip', '96065; CA; Redding; Shasta County; Montgomery Creek'], ['98592', 'zip', '96064; CA; Siskiyou County; Montague'], ['98591', 'zip', '96063; CA; Red Bluff; Tehama County; Mill Creek'], ['98590', 'zip', '96062; CA; Redding; Shasta County; Millville'], ['9859', 'city', 'Edgewater; NJ; New York-Newark-Jersey City; Bergen County'], ['98589', 'zip', '96061; CA; Red Bluff; Tehama County; Mill Creek'], ['98588', 'zip', '96059; CA; Red Bluff; Tehama County; Manton'], ['98587', 'zip', '96058; CA; Siskiyou County; Macdoel'], ['98586', 'zip', '96057; CA; Siskiyou County; McCloud'], ['98585', 'zip', '96056; CA; Susanville; Lassen County; McArthur'], ['98584', 'zip', '96055; CA; Red Bluff; Tehama County; Los Molinos'], ['98583', 'zip', '96054; CA; Modoc County; Lookout'], ['98582', 'zip', '96052; CA; Trinity County; Lewiston'], ['98581', 'zip', '96051; CA; Redding; Shasta County; Lakehead'], ['98580', 'zip', '96050; CA; Siskiyou County; Klamath River'], ['98578', 'zip', '96048; CA; Trinity County; Junction City'], ['98577', 'zip', '96047; CA; Redding; Shasta County; Igo'], ['98576', 'zip', '96046; CA; Trinity County; Hyampom'], ['98575', 'zip', '96044; CA; Siskiyou County; Hornbrook'], ['98574', 'zip', '96041; CA; Trinity County; Hayfork'], ['98573', 'zip', '96040; CA; Redding; Shasta County; Hat Creek'], ['98572', 'zip', '96039; CA; Siskiyou County; Happy Camp'], ['98571', 'zip', '96038; CA; Siskiyou County; Grenada'], ['98570', 'zip', '96037; CA; Siskiyou County; Etna'], ['9857', 'city', 'De Queen; AR; Sevier County'], ['98569', 'zip', '96035; CA; Red Bluff; Tehama County; Gerber'], ['98568', 'zip', '96034; CA; Siskiyou County; Gazelle'], ['98567', 'zip', '96033; CA; Redding; Shasta County; French Gulch'], ['98566', 'zip', '96032; CA; Siskiyou County; Fort Jones'], ['98565', 'zip', '96031; CA; Siskiyou County; Etna'], ['98564', 'zip', '96029; CA; Red Bluff; Tehama County; Corning'], ['98563', 'zip', '96028; CA; Redding; Shasta County; Fall River Mills'], ['98562', 'zip', '96027; CA; Siskiyou County; Etna'], ['98561', 'zip', '96025; CA; Siskiyou County; Dunsmuir'], ['98560', 'zip', '96024; CA; Trinity County; Douglas City'], ['98559', 'zip', '96023; CA; Siskiyou County; Dorris'], ['98558', 'zip', '96022; CA; Red Bluff; Tehama County; Cottonwood'], ['98557', 'zip', '96021; CA; Red Bluff; Tehama County; Corning'], ['98556', 'zip', '96020; CA; Plumas County; Chester'], ['98555', 'zip', '96019; CA; Redding; Shasta County; Shasta Lake'], ['98554', 'zip', '96017; CA; Redding; Shasta County; Castella'], ['98553', 'zip', '96016; CA; Redding; Shasta County; Cassel'], ['98552', 'zip', '96015; CA; Modoc County; Canby'], ['98551', 'zip', '96014; CA; Siskiyou County; Callahan'], ['98550', 'zip', '96013; CA; Redding; Shasta County; Burney'], ['98549', 'zip', '96011; CA; Redding; Shasta County; Big Bend'], ['98548', 'zip', '96010; CA; Trinity County; Big Bar'], ['98547', 'zip', '96009; CA; Susanville; Lassen County; Bieber'], ['98546', 'zip', '96008; CA; Redding; Shasta County; Bella Vista'], ['98545', 'zip', '96007; CA; Redding; Shasta County; Anderson'], ['98544', 'zip', '96006; CA; Modoc County; Adin'], ['98543', 'zip', '96003; CA; Redding; Shasta County'], ['98542', 'zip', '96002; CA; Redding; Shasta County'], ['98541', 'zip', '96001; CA; Redding; Shasta County'], ['98540', 'zip', '95993; CA; Yuba City; Sutter County'], ['9854', 'city', 'Currie; NC; Wilmington; Pender County'], ['98538', 'zip', '95991; CA; Yuba City; Sutter County'], ['98537', 'zip', '95988; CA; Glenn County; Willows'], ['98536', 'zip', '95987; CA; Colusa County; Williams'], ['98535', 'zip', '95986; CA; Truckee-Grass Valley; Nevada County; Washington'], ['98534', 'zip', '95984; CA; Plumas County; Twain'], ['98533', 'zip', '95983; CA; Plumas County; Taylorsville'], ['98532', 'zip', '95982; CA; Yuba City; Sutter County; Sutter'], ['98531', 'zip', '95981; CA; Plumas County; Strawberry Valley'], ['98530', 'zip', '95980; CA; Plumas County; Tobin'], ['98529', 'zip', '95979; CA; Colusa County; Stonyford'], ['98528', 'zip', '95978; CA; Chico; Butte County; Stirling City'], ['98527', 'zip', '95977; CA; Truckee-Grass Valley; Nevada County; Smartsville'], ['98525', 'zip', '95975; CA; Truckee-Grass Valley; Nevada County; Rough and Ready'], ['98524', 'zip', '95974; CA; Chico; Butte County; Richvale'], ['98523', 'zip', '95973; CA; Chico; Butte County'], ['98522', 'zip', '95972; CA; Chico; Butte County; Bangor'], ['98521', 'zip', '95971; CA; Plumas County; Quincy'], ['98520', 'zip', '95970; CA; Colusa County; Colusa'], ['9852', 'city', 'Combes; TX; Brownsville-Harlingen; Cameron County'], ['98519', 'zip', '95969; CA; Chico; Butte County; Paradise'], ['98518', 'zip', '95968; CA; Chico; Butte County; Palermo'], ['98516', 'zip', '95966; CA; Chico; Butte County; Oroville'], ['98515', 'zip', '95965; CA; Chico; Butte County; Oroville'], ['98514', 'zip', '95963; CA; Glenn County; Orland'], ['98513', 'zip', '95962; CA; Yuba City; Yuba County; Oregon House'], ['98512', 'zip', '95961; CA; Yuba City; Yuba County; Olivehurst'], ['98511', 'zip', '95960; CA; Truckee-Grass Valley; Nevada County; North San Juan'], ['98510', 'zip', '95959; CA; Truckee-Grass Valley; Nevada County; Nevada City'], ['98509', 'zip', '95958; CA; Chico; Butte County; Nelson'], ['98508', 'zip', '95957; CA; Yuba City; Sutter County; Meridian'], ['98507', 'zip', '95956; CA; Plumas County; Meadow Valley'], ['98506', 'zip', '95955; CA; Colusa County; Maxwell'], ['98505', 'zip', '95954; CA; Chico; Butte County; Magalia'], ['98504', 'zip', '95953; CA; Yuba City; Sutter County; Live Oak'], ['98503', 'zip', '95951; CA; Glenn County; Hamilton City'], ['98502', 'zip', '95950; CA; Colusa County; Grimes'], ['98501', 'zip', '95949; CA; Truckee-Grass Valley; Nevada County; Grass Valley'], ['98500', 'zip', '95948; CA; Chico; Butte County; Gridley'], ['9850', 'city', 'Norwood; NY; Ogdensburg-Massena; Saint Lawrence County'], ['985', 'county', 'DeKalb County; MO; St. Joseph'], ['98499', 'zip', '95947; CA; Plumas County; Greenville'], ['98498', 'zip', '95946; CA; Truckee-Grass Valley; Nevada County; Penn Valley'], ['98497', 'zip', '95945; CA; Truckee-Grass Valley; Nevada County; Grass Valley'], ['98496', 'zip', '95944; CA; Sierra County; Downieville'], ['98495', 'zip', '95943; CA; Glenn County; Glenn'], ['98494', 'zip', '95942; CA; Chico; Butte County; Forest Ranch'], ['98493', 'zip', '95941; CA; Chico; Butte County; Forbestown'], ['98491', 'zip', '95939; CA; Glenn County; Elk Creek'], ['98490', 'zip', '95938; CA; Chico; Butte County; Durham'], ['98489', 'zip', '95937; CA; Colusa County; Arbuckle'], ['98488', 'zip', '95936; CA; Sierra County; Downieville'], ['98487', 'zip', '95935; CA; Yuba City; Yuba County; Dobbins'], ['98486', 'zip', '95934; CA; Plumas County; Greenville'], ['98485', 'zip', '95932; CA; Colusa County; Colusa'], ['98484', 'zip', '95930; CA; Chico; Butte County; Clipper Mills'], ['98482', 'zip', '95928; CA; Chico; Butte County'], ['98480', 'zip', '95926; CA; Chico; Butte County'], ['9848', 'city', 'North Londonderry Township; PA; Lebanon; Lebanon County'], ['98479', 'zip', '95925; CA; Chico; Butte County; Forbestown'], ['98477', 'zip', '95923; CA; Plumas County; Canyon Dam'], ['98476', 'zip', '95922; CA; Yuba City; Yuba County; Camptonville'], ['98475', 'zip', '95920; CA; Glenn County; Butte City'], ['98474', 'zip', '95919; CA; Yuba City; Yuba County; Brownsville'], ['98473', 'zip', '95918; CA; Yuba City; Yuba County; Browns Valley'], ['98472', 'zip', '95917; CA; Chico; Butte County; Biggs'], ['98471', 'zip', '95916; CA; Chico; Butte County; Berry Creek'], ['98469', 'zip', '95914; CA; Chico; Butte County; Bangor'], ['98468', 'zip', '95913; CA; Glenn County; Willows'], ['98467', 'zip', '95912; CA; Colusa County; Arbuckle'], ['98464', 'zip', '95901; CA; Yuba City; Yuba County; Marysville'], ['98457', 'zip', '95864; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Arden-Arcade'], ['98451', 'zip', '95843'], ['98450', 'zip', '95842'], ['9845', 'city', 'Duck; NC; Kill Devil Hills; Dare County'], ['98449', 'zip', '95841; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98447', 'zip', '95838; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98446', 'zip', '95837; CA; Yuba City; Sutter County; Nicolaus'], ['98444', 'zip', '95835'], ['98443', 'zip', '95834'], ['98442', 'zip', '95833'], ['98441', 'zip', '95832; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98440', 'zip', '95831'], ['9844', 'city', 'Driftwood; PA; Cameron County'], ['98439', 'zip', '95830; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Elk Grove'], ['98438', 'zip', '95829; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98437', 'zip', '95828'], ['98436', 'zip', '95827'], ['98435', 'zip', '95826'], ['98434', 'zip', '95825'], ['98433', 'zip', '95824; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98432', 'zip', '95823'], ['98431', 'zip', '95822'], ['98430', 'zip', '95821'], ['9843', 'city', 'Dola; OH; Hardin County'], ['98429', 'zip', '95820'], ['98428', 'zip', '95819'], ['98427', 'zip', '95818; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98426', 'zip', '95817'], ['98425', 'zip', '95816; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['98424', 'zip', '95815'], ['98423', 'zip', '95814; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sacramento'], ['9842', 'city', 'Town Of Dickinson; NY; Malone; Franklin County'], ['98418', 'zip', '95776; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Woodland'], ['98417', 'zip', '95765'], ['98415', 'zip', '95762'], ['98413', 'zip', '95758'], ['98412', 'zip', '95757'], ['98411', 'zip', '95747'], ['98410', 'zip', '95746'], ['98408', 'zip', '95742; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Rancho Cordova'], ['98406', 'zip', '95736; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Colfax'], ['98405', 'zip', '95735; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Twin Bridges'], ['98404', 'zip', '95728; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Soda Springs'], ['98403', 'zip', '95726; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Pollock Pines'], ['98402', 'zip', '95724; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Soda Springs'], ['98401', 'zip', '95722; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Meadow Vista'], ['98400', 'zip', '95721; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Twin Bridges'], ['9840', 'city', 'Agoura Hills; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County'], ['984', 'county', 'Dekalb County; GA; Atlanta-Sandy Springs-Roswell'], ['98399', 'zip', '95720; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Kyburz'], ['98398', 'zip', '95717; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Gold Run'], ['98397', 'zip', '95715; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Soda Springs'], ['98396', 'zip', '95714; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Dutch Flat'], ['98395', 'zip', '95713; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Colfax'], ['98393', 'zip', '95709; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Camino'], ['98392', 'zip', '95703; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Applegate'], ['98391', 'zip', '95701; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Alta'], ['98390', 'zip', '95699; CA; Amador County; Plymouth'], ['9839', 'city', 'Clear Lake; IA; Mason City; Cerro Gordo County'], ['98389', 'zip', '95698; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Woodland'], ['98388', 'zip', '95697; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Yolo'], ['98386', 'zip', '95695; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Woodland'], ['98385', 'zip', '95694; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Winters'], ['98384', 'zip', '95693; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Wilton'], ['98383', 'zip', '95692; CA; Yuba City; Yuba County; Wheatland'], ['98382', 'zip', '95691'], ['98381', 'zip', '95690; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Walnut Grove'], ['98380', 'zip', '95689; CA; Amador County; Volcano'], ['9838', 'city', 'Barnard; MO; Maryville; Nodaway County'], ['98379', 'zip', '95688; CA; Vallejo-Fairfield; Solano County; Vacaville'], ['98378', 'zip', '95687; CA; Vallejo-Fairfield; Solano County; Vacaville'], ['98377', 'zip', '95686; CA; Stockton-Lodi; San Joaquin County; Thornton'], ['98376', 'zip', '95685; CA; Amador County; Sutter Creek'], ['98375', 'zip', '95684; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Somerset'], ['98374', 'zip', '95683; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Sloughhouse'], ['98373', 'zip', '95682; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Shingle Springs'], ['98372', 'zip', '95681; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Sheridan'], ['98371', 'zip', '95680; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Walnut Grove'], ['98370', 'zip', '95679; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Brooks'], ['9837', 'city', 'Delta; IA; Keokuk County'], ['98369', 'zip', '95678'], ['98368', 'zip', '95677'], ['98367', 'zip', '95676; CA; Yuba City; Sutter County; Robbins'], ['98366', 'zip', '95675; CA; Amador County; River Pines'], ['98365', 'zip', '95674; CA; Yuba City; Sutter County; Rio Oso'], ['98364', 'zip', '95673; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Rio Linda'], ['98363', 'zip', '95672; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Rescue'], ['98361', 'zip', '95670'], ['98360', 'zip', '95669; CA; Amador County; Plymouth'], ['9836', 'city', 'Clifton; ME; Bangor; Penobscot County'], ['98359', 'zip', '95668; CA; Yuba City; Sutter County; Pleasant Grove'], ['98358', 'zip', '95667; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Placerville'], ['98357', 'zip', '95666; CA; Amador County; Pioneer'], ['98356', 'zip', '95665; CA; Amador County; Pine Grove'], ['98355', 'zip', '95664; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Pilot Hill'], ['98354', 'zip', '95663; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Penryn'], ['98353', 'zip', '95662'], ['98352', 'zip', '95661'], ['98351', 'zip', '95660'], ['98350', 'zip', '95659; CA; Yuba City; Sutter County; Nicolaus'], ['9835', 'city', 'Cleo Springs; OK; Major County'], ['98349', 'zip', '95658; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Newcastle'], ['98348', 'zip', '95656; CA; Amador County; Plymouth'], ['98347', 'zip', '95655; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Mather'], ['98345', 'zip', '95653; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Esparto'], ['98343', 'zip', '95651; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Lotus'], ['98342', 'zip', '95650; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Loomis'], ['98341', 'zip', '95648; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Lincoln'], ['98340', 'zip', '95646; CA; Alpine County; Kirkwood'], ['98339', 'zip', '95645; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Woodland'], ['98337', 'zip', '95642; CA; Amador County; Jackson'], ['98336', 'zip', '95641; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Isleton'], ['98335', 'zip', '95640; CA; Amador County; Ione'], ['98334', 'zip', '95639; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Clarksburg'], ['98333', 'zip', '95638; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Herald'], ['98332', 'zip', '95637; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Guinda'], ['98331', 'zip', '95636; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Somerset'], ['98330', 'zip', '95635; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Greenwood'], ['98329', 'zip', '95634; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Georgetown'], ['98328', 'zip', '95633; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Garden Valley'], ['98327', 'zip', '95632; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Galt'], ['98326', 'zip', '95631; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Foresthill'], ['98325', 'zip', '95630'], ['98324', 'zip', '95629; CA; Amador County; Volcano'], ['98323', 'zip', '95628'], ['98322', 'zip', '95627; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Esparto'], ['98321', 'zip', '95626; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Elverta'], ['98320', 'zip', '95625; CA; Vallejo-Fairfield; Solano County; Vacaville'], ['98319', 'zip', '95624'], ['98318', 'zip', '95623; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; El Dorado'], ['98317', 'zip', '95621'], ['98316', 'zip', '95620; CA; Vallejo-Fairfield; Solano County; Dixon'], ['98315', 'zip', '95619; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Diamond Springs'], ['98314', 'zip', '95618; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Davis'], ['98312', 'zip', '95616; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Davis'], ['98311', 'zip', '95615; CA; Sacramento--Roseville--Arden-Arcade; Sacramento County; Courtland'], ['98310', 'zip', '95614; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Cool'], ['9831', 'city', 'Catawba; NC; Hickory-Lenoir-Morganton; Catawba County'], ['98309', 'zip', '95613; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Garden Valley'], ['98308', 'zip', '95612; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Clarksburg'], ['98306', 'zip', '95610'], ['98304', 'zip', '95608'], ['98303', 'zip', '95607; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Capay'], ['98302', 'zip', '95606; CA; Sacramento--Roseville--Arden-Arcade; Yolo County; Brooks'], ['98301', 'zip', '95605'], ['983', 'county', 'DeSoto County; MS; Memphis'], ['98299', 'zip', '95603'], ['98298', 'zip', '95602; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Auburn'], ['98297', 'zip', '95601; CA; Amador County; Amador City'], ['98296', 'zip', '95595; CA; Trinity County; Zenia'], ['98295', 'zip', '95589; CA; Eureka-Arcata-Fortuna; Humboldt County; Whitethorn'], ['98293', 'zip', '95585; CA; Ukiah; Mendocino County; Leggett'], ['98292', 'zip', '95573; CA; Eureka-Arcata-Fortuna; Humboldt County; Willow Creek'], ['98291', 'zip', '95571; CA; Eureka-Arcata-Fortuna; Humboldt County; Myers Flat'], ['98290', 'zip', '95570; CA; Eureka-Arcata-Fortuna; Humboldt County; Trinidad'], ['9829', 'city', 'Carroll; IA; Carroll County'], ['98289', 'zip', '95569; CA; Eureka-Arcata-Fortuna; Humboldt County; Redcrest'], ['98288', 'zip', '95568; CA; Siskiyou County; Somes Bar'], ['98287', 'zip', '95567; CA; Crescent City; Del Norte County; Smith River'], ['98286', 'zip', '95565; CA; Eureka-Arcata-Fortuna; Humboldt County; Scotia'], ['98285', 'zip', '95564; CA; Eureka-Arcata-Fortuna; Humboldt County; Samoa'], ['98284', 'zip', '95563; CA; Trinity County; Salyer'], ['98283', 'zip', '95562; CA; Eureka-Arcata-Fortuna; Humboldt County; Rio Dell'], ['98282', 'zip', '95560; CA; Eureka-Arcata-Fortuna; Humboldt County; Redway'], ['98281', 'zip', '95559; CA; Eureka-Arcata-Fortuna; Humboldt County; Myers Flat'], ['98280', 'zip', '95558; CA; Eureka-Arcata-Fortuna; Humboldt County; Ferndale'], ['98279', 'zip', '95556; CA; Eureka-Arcata-Fortuna; Humboldt County; Orleans'], ['98278', 'zip', '95555; CA; Eureka-Arcata-Fortuna; Humboldt County; Orick'], ['98277', 'zip', '95554; CA; Eureka-Arcata-Fortuna; Humboldt County; Myers Flat'], ['98276', 'zip', '95553; CA; Eureka-Arcata-Fortuna; Humboldt County; Myers Flat'], ['98275', 'zip', '95552; CA; Trinity County; Mad River'], ['98274', 'zip', '95551; CA; Eureka-Arcata-Fortuna; Humboldt County; Loleta'], ['98273', 'zip', '95550; CA; Eureka-Arcata-Fortuna; Humboldt County; Korbel'], ['98272', 'zip', '95549; CA; Eureka-Arcata-Fortuna; Humboldt County; Kneeland'], ['98271', 'zip', '95548; CA; Crescent City; Del Norte County; Klamath'], ['98270', 'zip', '95547; CA; Eureka-Arcata-Fortuna; Humboldt County; Hydesville'], ['9827', 'city', 'California; MD; California-Lexington Park; Saint Marys County'], ['98268', 'zip', '95545; CA; Eureka-Arcata-Fortuna; Humboldt County; Garberville'], ['98267', 'zip', '95543; CA; Crescent City; Del Norte County; Gasquet'], ['98266', 'zip', '95542; CA; Eureka-Arcata-Fortuna; Humboldt County; Garberville'], ['98265', 'zip', '95540; CA; Eureka-Arcata-Fortuna; Humboldt County; Fortuna'], ['98263', 'zip', '95537; CA; Eureka-Arcata-Fortuna; Humboldt County; Fields Landing'], ['98262', 'zip', '95536; CA; Eureka-Arcata-Fortuna; Humboldt County; Ferndale'], ['9826', 'city', 'Cabot; AR; Little Rock-North Little Rock-Conway; Lonoke County'], ['98259', 'zip', '95531; CA; Crescent City; Del Norte County'], ['98258', 'zip', '95528; CA; Eureka-Arcata-Fortuna; Humboldt County; Carlotta'], ['98257', 'zip', '95527; CA; Trinity County; Burnt Ranch'], ['98256', 'zip', '95526; CA; Eureka-Arcata-Fortuna; Humboldt County; Bridgeville'], ['98255', 'zip', '95525; CA; Eureka-Arcata-Fortuna; Humboldt County; Blue Lake'], ['98254', 'zip', '95524; CA; Eureka-Arcata-Fortuna; Humboldt County; Bayside'], ['98253', 'zip', '95521; CA; Eureka-Arcata-Fortuna; Humboldt County; Arcata'], ['98252', 'zip', '95519; CA; Eureka-Arcata-Fortuna; Humboldt County; McKinleyville'], ['98250', 'zip', '95514; CA; Eureka-Arcata-Fortuna; Humboldt County; Blocksburg'], ['98249', 'zip', '95511; CA; Eureka-Arcata-Fortuna; Humboldt County; Alderpoint'], ['98248', 'zip', '95503; CA; Eureka-Arcata-Fortuna; Humboldt County; Eureka'], ['98246', 'zip', '95501; CA; Eureka-Arcata-Fortuna; Humboldt County; Eureka'], ['98245', 'zip', '95497; CA; Santa Rosa; Sonoma County; The Sea Ranch'], ['98244', 'zip', '95494; CA; Ukiah; Mendocino County; Yorkville'], ['98243', 'zip', '95493; CA; Clearlake; Lake County; Upper Lake'], ['98242', 'zip', '95492; CA; Santa Rosa; Sonoma County; Windsor'], ['98241', 'zip', '95490; CA; Ukiah; Mendocino County; Willits'], ['98240', 'zip', '95488; CA; Ukiah; Mendocino County; Westport'], ['9824', 'city', 'Burkettsville; OH; Greenville; Darke County'], ['98237', 'zip', '95485; CA; Clearlake; Lake County; Upper Lake'], ['98236', 'zip', '95482; CA; Ukiah; Mendocino County'], ['98235', 'zip', '95481; CA; Ukiah; Mendocino County'], ['98233', 'zip', '95476; CA; Santa Rosa; Sonoma County; Sonoma'], ['98231', 'zip', '95472; CA; Santa Rosa; Sonoma County; Sebastopol'], ['98230', 'zip', '95471; CA; Santa Rosa; Sonoma County; Guerneville'], ['98229', 'zip', '95470; CA; Ukiah; Mendocino County; Redwood Valley'], ['98228', 'zip', '95469; CA; Ukiah; Mendocino County; Potter Valley'], ['98227', 'zip', '95468; CA; Ukiah; Mendocino County; Point Arena'], ['98226', 'zip', '95467; CA; Clearlake; Lake County; Hidden Valley Lake'], ['98225', 'zip', '95466; CA; Ukiah; Mendocino County; Philo'], ['98224', 'zip', '95465; CA; Santa Rosa; Sonoma County; Occidental'], ['98223', 'zip', '95464; CA; Clearlake; Lake County; Nice'], ['98222', 'zip', '95463; CA; Ukiah; Mendocino County; Philo'], ['98221', 'zip', '95462; CA; Santa Rosa; Sonoma County; Monte Rio'], ['98220', 'zip', '95461; CA; Clearlake; Lake County; Middletown'], ['98219', 'zip', '95460; CA; Ukiah; Mendocino County; Mendocino'], ['98218', 'zip', '95459; CA; Ukiah; Mendocino County; Manchester'], ['98217', 'zip', '95458; CA; Clearlake; Lake County; Lucerne'], ['98216', 'zip', '95457; CA; Clearlake; Lake County; Lower Lake'], ['98215', 'zip', '95456; CA; Ukiah; Mendocino County; Mendocino'], ['98214', 'zip', '95454; CA; Ukiah; Mendocino County; Laytonville'], ['98213', 'zip', '95453; CA; Clearlake; Lake County; Lakeport'], ['98212', 'zip', '95452; CA; Santa Rosa; Sonoma County; Kenwood'], ['98211', 'zip', '95451; CA; Clearlake; Lake County; Kelseyville'], ['98210', 'zip', '95450; CA; Santa Rosa; Sonoma County; Jenner'], ['9821', 'city', 'Nanticoke; MD; Salisbury; Wicomico County'], ['98209', 'zip', '95449; CA; Ukiah; Mendocino County; Hopland'], ['98208', 'zip', '95448; CA; Santa Rosa; Sonoma County; Healdsburg'], ['98207', 'zip', '95446; CA; Santa Rosa; Sonoma County; Guerneville'], ['98206', 'zip', '95445; CA; Ukiah; Mendocino County; Gualala'], ['98205', 'zip', '95444; CA; Santa Rosa; Sonoma County; Graton'], ['98204', 'zip', '95443; CA; Clearlake; Lake County; Lucerne'], ['98203', 'zip', '95442; CA; Santa Rosa; Sonoma County; Glen Ellen'], ['98202', 'zip', '95441; CA; Santa Rosa; Sonoma County; Geyserville'], ['98201', 'zip', '95439; CA; Santa Rosa; Sonoma County; Fulton'], ['98200', 'zip', '95437; CA; Ukiah; Mendocino County; Fort Bragg'], ['982', 'county', 'Dawson County; TX; Lamesa'], ['98199', 'zip', '95436; CA; Santa Rosa; Sonoma County; Forestville'], ['98198', 'zip', '95435; CA; Clearlake; Lake County; Lakeport'], ['98197', 'zip', '95433; CA; Santa Rosa; Sonoma County; Sonoma'], ['98196', 'zip', '95432; CA; Ukiah; Mendocino County; Elk'], ['98194', 'zip', '95430; CA; Santa Rosa; Sonoma County; Duncans Mills'], ['98192', 'zip', '95428; CA; Ukiah; Mendocino County; Covelo'], ['98191', 'zip', '95427; CA; Ukiah; Mendocino County; Comptche'], ['98190', 'zip', '95426; CA; Clearlake; Lake County; Middletown'], ['98189', 'zip', '95425; CA; Santa Rosa; Sonoma County; Cloverdale'], ['98188', 'zip', '95424; CA; Clearlake; Lake County'], ['98187', 'zip', '95423; CA; Clearlake; Lake County; Clearlake Oaks'], ['98186', 'zip', '95422; CA; Clearlake; Lake County'], ['98185', 'zip', '95421; CA; Santa Rosa; Sonoma County; Cazadero'], ['98184', 'zip', '95420; CA; Ukiah; Mendocino County; Caspar'], ['98183', 'zip', '95419; CA; Santa Rosa; Sonoma County; Occidental'], ['98181', 'zip', '95417; CA; Ukiah; Mendocino County; Branscomb'], ['98180', 'zip', '95416; CA; Santa Rosa; Sonoma County; Sonoma'], ['9818', 'city', 'Mount Pleasant Mills; PA; Selinsgrove; Snyder County'], ['98179', 'zip', '95415; CA; Ukiah; Mendocino County; Boonville'], ['98178', 'zip', '95412; CA; Santa Rosa; Sonoma County; Annapolis'], ['98177', 'zip', '95410; CA; Ukiah; Mendocino County; Albion'], ['98176', 'zip', '95409; CA; Santa Rosa; Sonoma County'], ['98174', 'zip', '95407; CA; Santa Rosa; Sonoma County'], ['98172', 'zip', '95405; CA; Santa Rosa; Sonoma County'], ['98171', 'zip', '95404; CA; Santa Rosa; Sonoma County'], ['98170', 'zip', '95403; CA; Santa Rosa; Sonoma County'], ['9817', 'city', 'Morris; OK; Tulsa; Okmulgee County'], ['98168', 'zip', '95401; CA; Santa Rosa; Sonoma County'], ['98166', 'zip', '95391'], ['98165', 'zip', '95389; CA; Mariposa County; El Portal'], ['98164', 'zip', '95388; CA; Merced; Merced County; Winton'], ['98163', 'zip', '95387; CA; Modesto; Stanislaus County; Patterson'], ['98162', 'zip', '95386; CA; Modesto; Stanislaus County; Waterford'], ['98161', 'zip', '95385; CA; Modesto; Stanislaus County; Vernalis'], ['98160', 'zip', '95383; CA; Sonora; Tuolumne County; Twain Harte'], ['9816', 'city', 'Morgan; MN; Redwood County'], ['98159', 'zip', '95382; CA; Modesto; Stanislaus County; Turlock'], ['98157', 'zip', '95380; CA; Modesto; Stanislaus County; Turlock'], ['98156', 'zip', '95379; CA; Sonora; Tuolumne County; Tuolumne'], ['98154', 'zip', '95377'], ['98153', 'zip', '95376; CA; Stockton-Lodi; San Joaquin County; Tracy'], ['98152', 'zip', '95375; CA; Sonora; Tuolumne County; Strawberry'], ['98151', 'zip', '95374; CA; Merced; Merced County; Stevinson'], ['98149', 'zip', '95372; CA; Sonora; Tuolumne County; Soulsbyville'], ['98148', 'zip', '95370; CA; Sonora; Tuolumne County'], ['98147', 'zip', '95369; CA; Merced; Merced County; Snelling'], ['98146', 'zip', '95368; CA; Modesto; Stanislaus County; Salida'], ['98145', 'zip', '95367; CA; Modesto; Stanislaus County; Riverbank'], ['98144', 'zip', '95366; CA; Stockton-Lodi; San Joaquin County; Ripon'], ['98143', 'zip', '95365; CA; Merced; Merced County; Planada'], ['98142', 'zip', '95364; CA; Sonora; Tuolumne County; Pinecrest'], ['98141', 'zip', '95363; CA; Modesto; Stanislaus County; Patterson'], ['98140', 'zip', '95361; CA; Modesto; Stanislaus County; Oakdale'], ['9814', 'city', 'Montrose; MN; Minneapolis-St. Paul-Bloomington; Wright County'], ['98139', 'zip', '95360; CA; Modesto; Stanislaus County; Newman'], ['98138', 'zip', '95358; CA; Modesto; Stanislaus County'], ['98137', 'zip', '95357; CA; Modesto; Stanislaus County'], ['98136', 'zip', '95356; CA; Modesto; Stanislaus County'], ['98135', 'zip', '95355; CA; Modesto; Stanislaus County'], ['98134', 'zip', '95354; CA; Modesto; Stanislaus County'], ['98131', 'zip', '95351; CA; Modesto; Stanislaus County'], ['98130', 'zip', '95350; CA; Modesto; Stanislaus County'], ['9813', 'city', 'Deer River; MN; Grand Rapids; Itasca County'], ['98129', 'zip', '95348; CA; Merced; Merced County'], ['98128', 'zip', '95347; CA; Mariposa County; Coulterville'], ['98127', 'zip', '95346; CA; Sonora; Tuolumne County; Mi Wuk Village'], ['98126', 'zip', '95345; CA; Mariposa County; Midpines'], ['98122', 'zip', '95341; CA; Merced; Merced County'], ['98121', 'zip', '95340; CA; Merced; Merced County'], ['98120', 'zip', '95338; CA; Mariposa County; Mariposa'], ['9812', 'city', 'Deer Creek; IL; Peoria; Tazewell County'], ['98119', 'zip', '95337'], ['98118', 'zip', '95336'], ['98117', 'zip', '95335; CA; Sonora; Tuolumne County; Long Barn'], ['98116', 'zip', '95334; CA; Merced; Merced County; Livingston'], ['98115', 'zip', '95333; CA; Merced; Merced County; Le Grand'], ['98114', 'zip', '95330; CA; Stockton-Lodi; San Joaquin County; Lathrop'], ['98113', 'zip', '95329; CA; Mariposa County; La Grange'], ['98112', 'zip', '95328; CA; Modesto; Stanislaus County; Keyes'], ['98111', 'zip', '95327; CA; Sonora; Tuolumne County; Jamestown'], ['98110', 'zip', '95326; CA; Modesto; Stanislaus County; Hughson'], ['9811', 'city', 'De Kalb; TX; Texarkana; Bowie County'], ['98109', 'zip', '95325; CA; Mariposa County; Hornitos'], ['98108', 'zip', '95324; CA; Merced; Merced County; Hilmar'], ['98107', 'zip', '95323; CA; Modesto; Stanislaus County; Hickman'], ['98106', 'zip', '95322; CA; Merced; Merced County; Gustine'], ['98105', 'zip', '95321; CA; Sonora; Tuolumne County; Groveland'], ['98104', 'zip', '95320; CA; Stockton-Lodi; San Joaquin County; Escalon'], ['98103', 'zip', '95319; CA; Modesto; Stanislaus County'], ['98102', 'zip', '95318; CA; Mariposa County; El Portal'], ['98101', 'zip', '95317; CA; Merced; Merced County; El Nido'], ['98100', 'zip', '95316; CA; Modesto; Stanislaus County; Denair'], ['9810', 'city', 'Cumberland; WI; Barron County'], ['981', 'county', 'Dawson County; NE; Lexington'], ['98099', 'zip', '95315; CA; Merced; Merced County; Delhi'], ['98097', 'zip', '95313; CA; Modesto; Stanislaus County; Crows Landing'], ['98096', 'zip', '95312; CA; Merced; Merced County; Winton'], ['98095', 'zip', '95311; CA; Mariposa County; Coulterville'], ['98094', 'zip', '95310; CA; Sonora; Tuolumne County; Columbia'], ['98093', 'zip', '95309; CA; Sonora; Tuolumne County; Jamestown'], ['98092', 'zip', '95307; CA; Modesto; Stanislaus County; Ceres'], ['98091', 'zip', '95306; CA; Mariposa County; Catheys Valley'], ['98090', 'zip', '95305; CA; Sonora; Tuolumne County; Big Oak Flat'], ['98089', 'zip', '95304; CA; Stockton-Lodi; San Joaquin County; Tracy'], ['98088', 'zip', '95303; CA; Merced; Merced County; Ballico'], ['98087', 'zip', '95301; CA; Merced; Merced County; Atwater'], ['98082', 'zip', '95258; CA; Stockton-Lodi; San Joaquin County; Woodbridge'], ['98081', 'zip', '95257; CA; Calaveras County; Wilseyville'], ['98080', 'zip', '95255; CA; Calaveras County; West Point'], ['9808', 'city', 'Coward; SC; Florence; Florence County'], ['98079', 'zip', '95254; CA; Calaveras County; Valley Springs'], ['98078', 'zip', '95253; CA; Stockton-Lodi; San Joaquin County; Lodi'], ['98077', 'zip', '95252; CA; Calaveras County; Valley Springs'], ['98076', 'zip', '95251; CA; Calaveras County; Vallecito'], ['98074', 'zip', '95249; CA; Calaveras County; San Andreas'], ['98073', 'zip', '95248; CA; Calaveras County; Mokelumne Hill'], ['98072', 'zip', '95247; CA; Calaveras County; Murphys'], ['98071', 'zip', '95246; CA; Calaveras County; Mountain Ranch'], ['98070', 'zip', '95245; CA; Calaveras County; Mokelumne Hill'], ['98069', 'zip', '95242; CA; Stockton-Lodi; San Joaquin County; Lodi'], ['98067', 'zip', '95240; CA; Stockton-Lodi; San Joaquin County; Lodi'], ['98066', 'zip', '95237; CA; Stockton-Lodi; San Joaquin County; Lockeford'], ['98065', 'zip', '95236; CA; Stockton-Lodi; San Joaquin County; Linden'], ['98063', 'zip', '95233; CA; Calaveras County; Murphys'], ['98062', 'zip', '95232; CA; Calaveras County; Glencoe'], ['98061', 'zip', '95231; CA; Stockton-Lodi; San Joaquin County; French Camp'], ['98060', 'zip', '95230; CA; Stockton-Lodi; San Joaquin County; Farmington'], ['98059', 'zip', '95229; CA; Calaveras County; Vallecito'], ['98058', 'zip', '95228; CA; Calaveras County; Copperopolis'], ['98057', 'zip', '95227; CA; Stockton-Lodi; San Joaquin County; Lockeford'], ['98056', 'zip', '95226; CA; Calaveras County; Valley Springs'], ['98055', 'zip', '95225; CA; Calaveras County; Valley Springs'], ['98054', 'zip', '95224; CA; Calaveras County; Murphys'], ['98053', 'zip', '95223; CA; Calaveras County; Arnold'], ['98052', 'zip', '95222; CA; Calaveras County; Murphys'], ['98050', 'zip', '95220; CA; Stockton-Lodi; San Joaquin County; Acampo'], ['9805', 'city', 'White Water; CA; Riverside-San Bernardino-Ontario; Riverside County'], ['98049', 'zip', '95219'], ['98048', 'zip', '95215; CA; Stockton-Lodi; San Joaquin County; Garden Acres'], ['98046', 'zip', '95212; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98044', 'zip', '95210; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98043', 'zip', '95209'], ['98041', 'zip', '95207; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98040', 'zip', '95206; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['9804', 'city', 'Wheeler; OR; Tillamook County'], ['98039', 'zip', '95205; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98038', 'zip', '95204; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98037', 'zip', '95203; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98036', 'zip', '95202; CA; Stockton-Lodi; San Joaquin County; Stockton'], ['98011', 'zip', '95148; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['9801', 'city', 'West Grove; PA; Philadelphia-Camden-Wilmington; Chester County'], ['98009', 'zip', '95141; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['98008', 'zip', '95140; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; East Foothills'], ['98007', 'zip', '95139; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['98006', 'zip', '95138'], ['98004', 'zip', '95136; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['98003', 'zip', '95135; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['98002', 'zip', '95134'], ['98001', 'zip', '95133; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['98000', 'zip', '95132'], ['980', 'county', 'Dawson County; MT'], ['98', 'county', 'Bell County; TX; Killeen-Temple'], ['97999', 'zip', '95131; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97998', 'zip', '95130'], ['97997', 'zip', '95129'], ['97996', 'zip', '95128'], ['97995', 'zip', '95127'], ['97994', 'zip', '95126; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97993', 'zip', '95125'], ['97992', 'zip', '95124'], ['97991', 'zip', '95123'], ['97990', 'zip', '95122; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97989', 'zip', '95121; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97988', 'zip', '95120'], ['97987', 'zip', '95119; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97986', 'zip', '95118; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97985', 'zip', '95117; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97984', 'zip', '95116'], ['97981', 'zip', '95113; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97980', 'zip', '95112'], ['97979', 'zip', '95111'], ['97978', 'zip', '95110; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['97970', 'zip', '95076; CA; Santa Cruz-Watsonville; Santa Cruz County; Watsonville'], ['9797', 'city', 'Belfast; ME; Waldo County'], ['97969', 'zip', '95075; CA; San Jose-Sunnyvale-Santa Clara; San Benito County; Hollister'], ['97968', 'zip', '95073; CA; Santa Cruz-Watsonville; Santa Cruz County; Soquel'], ['97966', 'zip', '95070; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Saratoga'], ['97964', 'zip', '95066; CA; Santa Cruz-Watsonville; Santa Cruz County; Scotts Valley'], ['97963', 'zip', '95065; CA; Santa Cruz-Watsonville; Santa Cruz County; Santa Cruz'], ['97962', 'zip', '95064; CA; Santa Cruz-Watsonville; Santa Cruz County; Santa Cruz'], ['97960', 'zip', '95062; CA; Santa Cruz-Watsonville; Santa Cruz County; Santa Cruz'], ['97958', 'zip', '95060; CA; Santa Cruz-Watsonville; Santa Cruz County; Santa Cruz'], ['97955', 'zip', '95054'], ['97952', 'zip', '95051'], ['97951', 'zip', '95050'], ['97950', 'zip', '95046; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Martin'], ['97949', 'zip', '95045; CA; San Jose-Sunnyvale-Santa Clara; San Benito County; San Juan Bautista'], ['97947', 'zip', '95043; CA; San Jose-Sunnyvale-Santa Clara; San Benito County; Paicines'], ['97945', 'zip', '95041; CA; Santa Cruz-Watsonville; Santa Cruz County; Felton'], ['97944', 'zip', '95039; CA; Salinas; Monterey County; Moss Landing'], ['97942', 'zip', '95037'], ['97940', 'zip', '95035'], ['97939', 'zip', '95033; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Lexington Hills'], ['97938', 'zip', '95032; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Los Gatos'], ['97936', 'zip', '95030'], ['97933', 'zip', '95023; CA; San Jose-Sunnyvale-Santa Clara; San Benito County; Hollister'], ['97931', 'zip', '95020'], ['97930', 'zip', '95019; CA; Santa Cruz-Watsonville; Santa Cruz County; Freedom'], ['97929', 'zip', '95018; CA; Santa Cruz-Watsonville; Santa Cruz County; Felton'], ['97928', 'zip', '95017; CA; Santa Cruz-Watsonville; Santa Cruz County; Davenport'], ['97926', 'zip', '95014'], ['97924', 'zip', '95012; CA; Salinas; Monterey County; Castroville'], ['97922', 'zip', '95010; CA; Santa Cruz-Watsonville; Santa Cruz County; Capitola'], ['97920', 'zip', '95008'], ['9792', 'city', 'Middleton; WI; Madison; Dane County'], ['97919', 'zip', '95007; CA; Santa Cruz-Watsonville; Santa Cruz County; Boulder Creek'], ['97918', 'zip', '95006; CA; Santa Cruz-Watsonville; Santa Cruz County; Boulder Creek'], ['97917', 'zip', '95005; CA; Santa Cruz-Watsonville; Santa Cruz County; Ben Lomond'], ['97916', 'zip', '95004; CA; Salinas; Monterey County; Aromas'], ['97915', 'zip', '95003; CA; Santa Cruz-Watsonville; Santa Cruz County; Aptos'], ['97914', 'zip', '95002; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; San Jose'], ['9791', 'city', 'Merrimac; MA; Boston-Cambridge-Newton; Essex County'], ['97904', 'zip', '94973; CA; San Francisco-Oakland-Hayward; Marin County; Woodacre'], ['97903', 'zip', '94972; CA; Santa Rosa; Sonoma County; Sebastopol'], ['97902', 'zip', '94971; CA; San Francisco-Oakland-Hayward; Marin County; Tomales'], ['97901', 'zip', '94970; CA; San Francisco-Oakland-Hayward; Marin County; Stinson Beach'], ['979', 'county', 'Dawson County; GA; Atlanta-Sandy Springs-Roswell'], ['97899', 'zip', '94965'], ['97898', 'zip', '94964; CA; San Francisco-Oakland-Hayward; Marin County; San Quentin'], ['97897', 'zip', '94963; CA; San Francisco-Oakland-Hayward; Marin County; San Geronimo'], ['97896', 'zip', '94960; CA; San Francisco-Oakland-Hayward; Marin County; San Anselmo'], ['97895', 'zip', '94957; CA; San Francisco-Oakland-Hayward; Marin County; Ross'], ['97894', 'zip', '94956; CA; San Francisco-Oakland-Hayward; Marin County; Point Reyes Station'], ['97892', 'zip', '94954; CA; Santa Rosa; Sonoma County; Petaluma'], ['97890', 'zip', '94952; CA; Santa Rosa; Sonoma County; Petaluma'], ['97889', 'zip', '94951; CA; Santa Rosa; Sonoma County; Penngrove'], ['97888', 'zip', '94950; CA; San Francisco-Oakland-Hayward; Marin County; Point Reyes Station'], ['97887', 'zip', '94949; CA; San Francisco-Oakland-Hayward; Marin County; Novato'], ['97885', 'zip', '94947; CA; San Francisco-Oakland-Hayward; Marin County; Novato'], ['97884', 'zip', '94946; CA; San Francisco-Oakland-Hayward; Marin County; Nicasio'], ['97883', 'zip', '94945; CA; San Francisco-Oakland-Hayward; Marin County; Novato'], ['97881', 'zip', '94941'], ['97880', 'zip', '94940; CA; San Francisco-Oakland-Hayward; Marin County; Marshall'], ['9788', 'city', 'Columbia; MS; Marion County'], ['97879', 'zip', '94939; CA; San Francisco-Oakland-Hayward; Marin County; Larkspur'], ['97878', 'zip', '94938; CA; San Francisco-Oakland-Hayward; Marin County; Lagunitas'], ['97877', 'zip', '94937; CA; San Francisco-Oakland-Hayward; Marin County; Inverness'], ['97876', 'zip', '94933; CA; San Francisco-Oakland-Hayward; Marin County; Lagunitas'], ['97875', 'zip', '94931; CA; Santa Rosa; Sonoma County; Cotati'], ['97874', 'zip', '94930; CA; San Francisco-Oakland-Hayward; Marin County; Fairfax'], ['97873', 'zip', '94929; CA; San Francisco-Oakland-Hayward; Marin County; Dillon Beach'], ['97872', 'zip', '94928; CA; Santa Rosa; Sonoma County; Rohnert Park'], ['9787', 'city', 'Town Of Cochecton; NY; Sullivan County'], ['97869', 'zip', '94925; CA; San Francisco-Oakland-Hayward; Marin County; Corte Madera'], ['97868', 'zip', '94924; CA; San Francisco-Oakland-Hayward; Marin County; Bolinas'], ['97867', 'zip', '94923; CA; Santa Rosa; Sonoma County; Bodega Bay'], ['97866', 'zip', '94922; CA; Santa Rosa; Sonoma County; Occidental'], ['97865', 'zip', '94920'], ['97860', 'zip', '94904; CA; San Francisco-Oakland-Hayward; Marin County; Kentfield'], ['97859', 'zip', '94903; CA; San Francisco-Oakland-Hayward; Marin County; San Rafael'], ['97858', 'zip', '94901'], ['97852', 'zip', '94806; CA; San Francisco-Oakland-Hayward; Contra Costa County; San Pablo'], ['97851', 'zip', '94805; CA; San Francisco-Oakland-Hayward; Contra Costa County; Richmond'], ['97850', 'zip', '94804'], ['9785', 'city', 'Valmeyer; IL; St. Louis; Monroe County'], ['97849', 'zip', '94803; CA; San Francisco-Oakland-Hayward; Contra Costa County; El Sobrante'], ['97847', 'zip', '94801; CA; San Francisco-Oakland-Hayward; Contra Costa County; Richmond'], ['97844', 'zip', '94710; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97843', 'zip', '94709; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97842', 'zip', '94708; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97841', 'zip', '94707; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97840', 'zip', '94706; CA; San Francisco-Oakland-Hayward; Alameda County; Albany'], ['9784', 'city', 'Valier; MT; Pondera County'], ['97839', 'zip', '94705; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97838', 'zip', '94704'], ['97837', 'zip', '94703'], ['97836', 'zip', '94702; CA; San Francisco-Oakland-Hayward; Alameda County; Berkeley'], ['97822', 'zip', '94621; CA; San Francisco-Oakland-Hayward; Alameda County; Oakland'], ['97820', 'zip', '94619'], ['97819', 'zip', '94618; CA; San Francisco-Oakland-Hayward; Alameda County; Oakland'], ['97814', 'zip', '94612; CA; San Francisco-Oakland-Hayward; Alameda County; Oakland'], ['97813', 'zip', '94611'], ['97812', 'zip', '94610'], ['97811', 'zip', '94609'], ['97810', 'zip', '94608'], ['97809', 'zip', '94607'], ['97808', 'zip', '94606'], ['97807', 'zip', '94605'], ['97805', 'zip', '94603'], ['97804', 'zip', '94602'], ['97803', 'zip', '94601'], ['97802', 'zip', '94599; CA; Napa; Napa County; Yountville'], ['97801', 'zip', '94598'], ['97800', 'zip', '94597'], ['9780', 'city', 'Barnard; VT; Claremont-Lebanon; Windsor County'], ['978', 'county', 'Dallas County; TX; Dallas-Fort Worth-Arlington'], ['97799', 'zip', '94596'], ['97798', 'zip', '94595; CA; San Francisco-Oakland-Hayward; Contra Costa County; Walnut Creek'], ['97797', 'zip', '94592; CA; Vallejo-Fairfield; Solano County; Vallejo'], ['97796', 'zip', '94591; CA; Vallejo-Fairfield; Solano County; Vallejo'], ['97795', 'zip', '94590; CA; Vallejo-Fairfield; Solano County; Vallejo'], ['97794', 'zip', '94589; CA; Vallejo-Fairfield; Solano County; Vallejo'], ['97793', 'zip', '94588'], ['97792', 'zip', '94587'], ['97791', 'zip', '94586; CA; San Francisco-Oakland-Hayward; Alameda County; Sunol'], ['97790', 'zip', '94585; CA; Vallejo-Fairfield; Solano County; Suisun City'], ['97789', 'zip', '94583'], ['97788', 'zip', '94582'], ['97786', 'zip', '94580; CA; San Francisco-Oakland-Hayward; Alameda County; San Lorenzo'], ['97785', 'zip', '94579; CA; San Francisco-Oakland-Hayward; Alameda County; San Leandro'], ['97784', 'zip', '94578; CA; San Francisco-Oakland-Hayward; Alameda County; San Leandro'], ['97783', 'zip', '94577; CA; San Francisco-Oakland-Hayward; Alameda County; San Leandro'], ['97782', 'zip', '94576; CA; Napa; Napa County; Deer Park'], ['97780', 'zip', '94574; CA; Napa; Napa County; Saint Helena'], ['9778', 'city', 'Austwell; TX; Refugio County'], ['97778', 'zip', '94572; CA; San Francisco-Oakland-Hayward; Contra Costa County; Rodeo'], ['97777', 'zip', '94571; CA; Vallejo-Fairfield; Solano County; Rio Vista'], ['97775', 'zip', '94569; CA; San Francisco-Oakland-Hayward; Contra Costa County; Port Costa'], ['97774', 'zip', '94568'], ['97773', 'zip', '94567; CA; Napa; Napa County; Pope Valley'], ['97772', 'zip', '94566'], ['97771', 'zip', '94565'], ['97770', 'zip', '94564; CA; San Francisco-Oakland-Hayward; Contra Costa County; Pinole'], ['9777', 'city', 'Augusta; WV; Winchester; Hampshire County'], ['97769', 'zip', '94563; CA; San Francisco-Oakland-Hayward; Contra Costa County; Orinda'], ['97767', 'zip', '94561; CA; San Francisco-Oakland-Hayward; Contra Costa County; Oakley'], ['97766', 'zip', '94560; CA; San Francisco-Oakland-Hayward; Alameda County; Newark'], ['97765', 'zip', '94559; CA; Napa; Napa County'], ['97764', 'zip', '94558; CA; Napa; Napa County'], ['97762', 'zip', '94556; CA; San Francisco-Oakland-Hayward; Contra Costa County; Moraga'], ['97761', 'zip', '94555'], ['97760', 'zip', '94553'], ['97759', 'zip', '94552; CA; San Francisco-Oakland-Hayward; Alameda County; Castro Valley'], ['97758', 'zip', '94551'], ['97757', 'zip', '94550'], ['97756', 'zip', '94549; CA; San Francisco-Oakland-Hayward; Contra Costa County; Lafayette'], ['97755', 'zip', '94548; CA; San Francisco-Oakland-Hayward; Contra Costa County; Oakley'], ['97754', 'zip', '94547; CA; San Francisco-Oakland-Hayward; Contra Costa County; Hercules'], ['97753', 'zip', '94546'], ['97752', 'zip', '94545; CA; San Francisco-Oakland-Hayward; Alameda County; Hayward'], ['97751', 'zip', '94544'], ['9775', 'city', 'Albany; KY; Clinton County'], ['97749', 'zip', '94542; CA; San Francisco-Oakland-Hayward; Alameda County; Hayward'], ['97748', 'zip', '94541'], ['97746', 'zip', '94539'], ['97745', 'zip', '94538'], ['97743', 'zip', '94536'], ['97742', 'zip', '94535; CA; Vallejo-Fairfield; Solano County; Fairfield'], ['97741', 'zip', '94534; CA; Vallejo-Fairfield; Solano County; Fairfield'], ['97740', 'zip', '94533; CA; Vallejo-Fairfield; Solano County; Fairfield'], ['97739', 'zip', '94531'], ['97738', 'zip', '94530'], ['97736', 'zip', '94528; CA; San Francisco-Oakland-Hayward; Contra Costa County; Diablo'], ['97734', 'zip', '94526'], ['97733', 'zip', '94525; CA; San Francisco-Oakland-Hayward; Contra Costa County; Crockett'], ['97731', 'zip', '94523'], ['9773', 'city', 'Melber; KY; Mayfield; Graves County'], ['97729', 'zip', '94521'], ['97728', 'zip', '94520'], ['97727', 'zip', '94519; CA; San Francisco-Oakland-Hayward; Contra Costa County; Concord'], ['97726', 'zip', '94518; CA; San Francisco-Oakland-Hayward; Contra Costa County; Concord'], ['97725', 'zip', '94517; CA; San Francisco-Oakland-Hayward; Contra Costa County; Clayton'], ['97724', 'zip', '94516; CA; San Francisco-Oakland-Hayward; Contra Costa County; Canyon'], ['97723', 'zip', '94515; CA; Napa; Napa County; Calistoga'], ['97722', 'zip', '94514; CA; San Francisco-Oakland-Hayward; Contra Costa County; Byron'], ['97721', 'zip', '94513'], ['97720', 'zip', '94512; CA; Vallejo-Fairfield; Solano County; Birds Landing'], ['97719', 'zip', '94511; CA; San Francisco-Oakland-Hayward; Contra Costa County; Bethel Island'], ['97718', 'zip', '94510; CA; Vallejo-Fairfield; Solano County; Benicia'], ['97717', 'zip', '94509'], ['97716', 'zip', '94508; CA; Napa; Napa County; Angwin'], ['97715', 'zip', '94507; CA; San Francisco-Oakland-Hayward; Contra Costa County; Alamo'], ['97714', 'zip', '94506'], ['97713', 'zip', '94503; CA; Napa; Napa County; American Canyon'], ['97712', 'zip', '94502; CA; San Francisco-Oakland-Hayward; Alameda County; Alameda'], ['97711', 'zip', '94501'], ['97704', 'zip', '94404'], ['97703', 'zip', '94403'], ['97702', 'zip', '94402'], ['97701', 'zip', '94401; CA; San Francisco-Oakland-Hayward; San Mateo County; San Mateo'], ['9770', 'city', 'Maysville; MO; St. Joseph; DeKalb County'], ['977', 'county', 'Dallas County; MO; Springfield'], ['97696', 'zip', '94306'], ['97695', 'zip', '94305; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Stanford'], ['97694', 'zip', '94304; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Palo Alto'], ['97693', 'zip', '94303'], ['97691', 'zip', '94301; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Palo Alto'], ['9769', 'city', 'Matthews; MO; New Madrid County'], ['9768', 'city', 'Marshalltown; IA; Marshall County'], ['9767', 'city', 'Marriottsville; MD; Baltimore-Columbia-Towson; Howard County'], ['9766', 'city', 'Marion; WI; Waupaca County'], ['9765', 'city', 'Marathon; OH; Cincinnati; Clermont County'], ['9764', 'city', 'Manns Choice; PA; Bedford County'], ['9763', 'city', 'Clairfield; TN; Claiborne County'], ['97610', 'zip', '94158; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['9761', 'city', 'Charleston; TN; Cleveland; Bradley County'], ['976', 'county', 'Dallas County; IA; Des Moines-West Des Moines'], ['97588', 'zip', '94134; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97587', 'zip', '94133'], ['97586', 'zip', '94132'], ['97585', 'zip', '94131'], ['97583', 'zip', '94129; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97581', 'zip', '94127'], ['97578', 'zip', '94124; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97577', 'zip', '94123'], ['97576', 'zip', '94122'], ['97575', 'zip', '94121'], ['97572', 'zip', '94118'], ['97571', 'zip', '94117'], ['97570', 'zip', '94116'], ['97569', 'zip', '94115'], ['97568', 'zip', '94114'], ['97567', 'zip', '94112'], ['97566', 'zip', '94111; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97565', 'zip', '94110'], ['97564', 'zip', '94109'], ['97563', 'zip', '94108'], ['97562', 'zip', '94107'], ['97560', 'zip', '94105'], ['9756', 'city', 'Talking Rock; GA; Atlanta-Sandy Springs-Roswell; Pickens County'], ['97559', 'zip', '94104; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97558', 'zip', '94103'], ['97557', 'zip', '94102; CA; San Francisco-Oakland-Hayward; San Francisco County; San Francisco'], ['97551', 'zip', '94089; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Sunnyvale'], ['97549', 'zip', '94087'], ['97548', 'zip', '94086'], ['97547', 'zip', '94085; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Sunnyvale'], ['97545', 'zip', '94080; CA; San Francisco-Oakland-Hayward; San Mateo County; South San Francisco'], ['97544', 'zip', '94074; CA; San Francisco-Oakland-Hayward; San Mateo County; San Gregorio'], ['97542', 'zip', '94070'], ['97540', 'zip', '94066; CA; San Francisco-Oakland-Hayward; San Mateo County; San Bruno'], ['9754', 'city', 'Stockton; AL; Daphne-Fairhope-Foley; Baldwin County'], ['97539', 'zip', '94065; CA; San Francisco-Oakland-Hayward; San Mateo County; Redwood City'], ['97537', 'zip', '94063; CA; San Francisco-Oakland-Hayward; San Mateo County; Redwood City'], ['97536', 'zip', '94062'], ['97535', 'zip', '94061'], ['97534', 'zip', '94060; CA; San Francisco-Oakland-Hayward; San Mateo County; Pescadero'], ['97531', 'zip', '94044; CA; San Francisco-Oakland-Hayward; San Mateo County; Pacifica'], ['97530', 'zip', '94043'], ['97528', 'zip', '94041; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Mountain View'], ['97527', 'zip', '94040'], ['97525', 'zip', '94038; CA; San Francisco-Oakland-Hayward; San Mateo County; Moss Beach'], ['97524', 'zip', '94037; CA; San Francisco-Oakland-Hayward; San Mateo County; Montara'], ['97521', 'zip', '94030; CA; San Francisco-Oakland-Hayward; San Mateo County; Millbrae'], ['97519', 'zip', '94028; CA; San Francisco-Oakland-Hayward; San Mateo County; Portola Valley'], ['97518', 'zip', '94027; CA; San Francisco-Oakland-Hayward; San Mateo County; Atherton'], ['97516', 'zip', '94025'], ['97515', 'zip', '94024; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Los Altos'], ['97513', 'zip', '94022; CA; San Jose-Sunnyvale-Santa Clara; Santa Clara County; Los Altos'], ['97512', 'zip', '94021; CA; San Francisco-Oakland-Hayward; San Mateo County; Pescadero'], ['97511', 'zip', '94020; CA; San Francisco-Oakland-Hayward; San Mateo County; La Honda'], ['97510', 'zip', '94019; CA; San Francisco-Oakland-Hayward; San Mateo County; Half Moon Bay'], ['97509', 'zip', '94018; CA; San Francisco-Oakland-Hayward; San Mateo County; Half Moon Bay'], ['97506', 'zip', '94015'], ['97505', 'zip', '94014; CA; San Francisco-Oakland-Hayward; San Mateo County; Daly City'], ['97501', 'zip', '94010'], ['97500', 'zip', '94005; CA; San Francisco-Oakland-Hayward; San Mateo County; Brisbane'], ['975', 'county', 'Dallas County; AR'], ['97498', 'zip', '94002'], ['97497', 'zip', '93962; CA; Salinas; Monterey County'], ['97496', 'zip', '93960; CA; Salinas; Monterey County; Soledad'], ['97495', 'zip', '93955; CA; Salinas; Monterey County; Seaside'], ['97494', 'zip', '93954; CA; Salinas; Monterey County; San Lucas'], ['97493', 'zip', '93953; CA; Salinas; Monterey County; Del Monte Forest'], ['97492', 'zip', '93950; CA; Salinas; Monterey County; Pacific Grove'], ['97488', 'zip', '93940; CA; Salinas; Monterey County; Monterey'], ['97487', 'zip', '93933; CA; Salinas; Monterey County; Marina'], ['97486', 'zip', '93932; CA; Salinas; Monterey County; Lockwood'], ['97485', 'zip', '93930; CA; Salinas; Monterey County; King City'], ['97484', 'zip', '93928; CA; Salinas; Monterey County; King City'], ['97483', 'zip', '93927; CA; Salinas; Monterey County; Greenfield'], ['97482', 'zip', '93926; CA; Salinas; Monterey County; Gonzales'], ['97481', 'zip', '93925; CA; Salinas; Monterey County; Chualar'], ['97480', 'zip', '93924; CA; Salinas; Monterey County; Carmel Valley'], ['97479', 'zip', '93923; CA; Salinas; Monterey County; Carmel-by-the-Sea'], ['97477', 'zip', '93921; CA; Salinas; Monterey County; Carmel-by-the-Sea'], ['97476', 'zip', '93920; CA; Salinas; Monterey County; Big Sur'], ['97473', 'zip', '93908; CA; Salinas; Monterey County'], ['97472', 'zip', '93907; CA; Salinas; Monterey County'], ['97471', 'zip', '93906; CA; Salinas; Monterey County'], ['97470', 'zip', '93905; CA; Salinas; Monterey County'], ['97468', 'zip', '93901; CA; Salinas; Monterey County'], ['97436', 'zip', '93728; CA; Fresno; Fresno County'], ['97435', 'zip', '93727; CA; Fresno; Fresno County'], ['97434', 'zip', '93726; CA; Fresno; Fresno County'], ['97433', 'zip', '93725; CA; Fresno; Fresno County'], ['97431', 'zip', '93722'], ['97430', 'zip', '93721; CA; Fresno; Fresno County'], ['97429', 'zip', '93720; CA; Fresno; Fresno County'], ['97422', 'zip', '93711; CA; Fresno; Fresno County'], ['97421', 'zip', '93710'], ['97417', 'zip', '93706; CA; Fresno; Fresno County'], ['97416', 'zip', '93705; CA; Fresno; Fresno County'], ['97415', 'zip', '93704; CA; Fresno; Fresno County'], ['97414', 'zip', '93703; CA; Fresno; Fresno County'], ['97413', 'zip', '93702; CA; Fresno; Fresno County'], ['97412', 'zip', '93701; CA; Fresno; Fresno County'], ['97411', 'zip', '93675; CA; Fresno; Fresno County; Squaw Valley'], ['97410', 'zip', '93673; CA; Fresno; Fresno County; Kingsburg'], ['9741', 'city', 'Unadilla; GA; Dooly County'], ['97409', 'zip', '93670; CA; Visalia-Porterville; Tulare County; Visalia'], ['97408', 'zip', '93669; CA; Madera; Madera County; North Fork'], ['97407', 'zip', '93668; CA; Fresno; Fresno County; Tranquillity'], ['97406', 'zip', '93667; CA; Fresno; Fresno County; Tollhouse'], ['97405', 'zip', '93666; CA; Visalia-Porterville; Tulare County; Dinuba'], ['97404', 'zip', '93665; CA; Merced; Merced County; Dos Palos'], ['97403', 'zip', '93664; CA; Fresno; Fresno County; Shaver Lake'], ['97402', 'zip', '93662; CA; Fresno; Fresno County; Selma'], ['97400', 'zip', '93660; CA; Fresno; Fresno County; San Joaquin'], ['974', 'county', 'Dallas County; AL; Selma'], ['97399', 'zip', '93657; CA; Fresno; Fresno County; Sanger'], ['97398', 'zip', '93656; CA; Fresno; Fresno County; Riverdale'], ['97397', 'zip', '93654; CA; Fresno; Fresno County; Reedley'], ['97396', 'zip', '93653; CA; Madera; Madera County; Raymond'], ['97395', 'zip', '93652; CA; Fresno; Fresno County; Raisin City'], ['97394', 'zip', '93651; CA; Fresno; Fresno County; Prather'], ['97393', 'zip', '93650; CA; Fresno; Fresno County'], ['97391', 'zip', '93648; CA; Fresno; Fresno County; Parlier'], ['97390', 'zip', '93647; CA; Visalia-Porterville; Tulare County; Orosi'], ['97389', 'zip', '93646; CA; Fresno; Fresno County; Orange Cove'], ['97388', 'zip', '93645; CA; Madera; Madera County; O Neals'], ['97387', 'zip', '93644; CA; Madera; Madera County; Oakhurst'], ['97386', 'zip', '93643; CA; Madera; Madera County; North Fork'], ['97384', 'zip', '93641; CA; Fresno; Fresno County; Miramonte'], ['97383', 'zip', '93640; CA; Fresno; Fresno County; Mendota'], ['97381', 'zip', '93638; CA; Madera; Madera County'], ['97380', 'zip', '93637; CA; Madera; Madera County'], ['9738', 'city', 'Maggie Valley; NC; Asheville; Haywood County'], ['97379', 'zip', '93635; CA; Merced; Merced County; Los Banos'], ['97378', 'zip', '93634; CA; Fresno; Fresno County; Lakeshore'], ['97377', 'zip', '93633; CA; Fresno; Fresno County; Hume'], ['97376', 'zip', '93631; CA; Fresno; Fresno County; Kingsburg'], ['97375', 'zip', '93630; CA; Fresno; Fresno County; Kerman'], ['97374', 'zip', '93628; CA; Fresno; Fresno County; Hume'], ['97372', 'zip', '93626; CA; Fresno; Fresno County; Friant'], ['97371', 'zip', '93625; CA; Fresno; Fresno County; Fowler'], ['97370', 'zip', '93624; CA; Fresno; Fresno County; Five Points'], ['97369', 'zip', '93623; CA; Mariposa County; Fish Camp'], ['97368', 'zip', '93622; CA; Fresno; Fresno County; Firebaugh'], ['97367', 'zip', '93621; CA; Fresno; Fresno County; Dunlap'], ['97366', 'zip', '93620; CA; Merced; Merced County; Dos Palos'], ['97365', 'zip', '93619'], ['97364', 'zip', '93618; CA; Visalia-Porterville; Tulare County; Dinuba'], ['97363', 'zip', '93616; CA; Fresno; Fresno County; Del Rey'], ['97362', 'zip', '93615; CA; Visalia-Porterville; Tulare County; Cutler'], ['97361', 'zip', '93614; CA; Madera; Madera County; Coarsegold'], ['9736', 'city', 'Lower Augusta Township; PA; Sunbury; Northumberland County'], ['97359', 'zip', '93612; CA; Fresno; Fresno County; Clovis'], ['97358', 'zip', '93611'], ['97357', 'zip', '93610; CA; Madera; Madera County; Chowchilla'], ['97356', 'zip', '93609; CA; Fresno; Fresno County; Caruthers'], ['97355', 'zip', '93608; CA; Fresno; Fresno County; Cantua Creek'], ['97353', 'zip', '93606; CA; Fresno; Fresno County'], ['97352', 'zip', '93605; CA; Fresno; Fresno County; Big Creek'], ['97351', 'zip', '93604; CA; Madera; Madera County; Bass Lake'], ['97350', 'zip', '93603; CA; Visalia-Porterville; Tulare County; Badger'], ['9735', 'city', 'Los Molinos; CA; Red Bluff; Tehama County'], ['97349', 'zip', '93602; CA; Fresno; Fresno County; Auberry'], ['97348', 'zip', '93601; CA; Madera; Madera County; Ahwahnee'], ['97345', 'zip', '93592; CA; Inyo County; Homewood Canyon-Valley Wells'], ['97344', 'zip', '93591; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Palmdale'], ['97339', 'zip', '93563; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Valyermo'], ['97338', 'zip', '93562; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Trona'], ['97337', 'zip', '93561; CA; Bakersfield; Kern County; Tehachapi'], ['97336', 'zip', '93560; CA; Bakersfield; Kern County; Rosamond'], ['97333', 'zip', '93555; CA; Bakersfield; Kern County; Ridgecrest'], ['97332', 'zip', '93554; CA; Bakersfield; Kern County; Randsburg'], ['97331', 'zip', '93553; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Pearblossom'], ['97330', 'zip', '93552; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Palmdale'], ['97329', 'zip', '93551'], ['97328', 'zip', '93550; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Palmdale'], ['97327', 'zip', '93549; CA; Inyo County; Olancha'], ['97326', 'zip', '93546; CA; Mono County; Mammoth Lakes'], ['97325', 'zip', '93545; CA; Inyo County; Lone Pine'], ['97324', 'zip', '93544; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Llano'], ['97323', 'zip', '93543; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Littlerock'], ['97321', 'zip', '93541; CA; Mono County; Lee Vining'], ['97319', 'zip', '93536'], ['97318', 'zip', '93535'], ['97317', 'zip', '93534'], ['97316', 'zip', '93532; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Lake Hughes'], ['97315', 'zip', '93531; CA; Bakersfield; Kern County; Keene'], ['97314', 'zip', '93530; CA; Inyo County; Keeler'], ['97313', 'zip', '93529; CA; Mono County; June Lake'], ['97311', 'zip', '93527; CA; Bakersfield; Kern County; Inyokern'], ['97310', 'zip', '93526; CA; Inyo County; Independence'], ['9731', 'city', 'Cassel; CA; Redding; Shasta County'], ['97308', 'zip', '93523; CA; Bakersfield; Kern County; Edwards'], ['97307', 'zip', '93522; CA; Inyo County; Darwin'], ['97306', 'zip', '93519; CA; Bakersfield; Kern County; Mojave'], ['97305', 'zip', '93518; CA; Bakersfield; Kern County; Caliente'], ['97304', 'zip', '93517; CA; Mono County; Bridgeport'], ['97303', 'zip', '93516; CA; Bakersfield; Kern County; Boron'], ['97301', 'zip', '93514; CA; Inyo County; Bishop'], ['97300', 'zip', '93513; CA; Inyo County; Big Pine'], ['973', 'county', 'Dallam County; TX'], ['97299', 'zip', '93512; CA; Mono County; Benton'], ['97298', 'zip', '93510; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Acton'], ['97297', 'zip', '93505; CA; Bakersfield; Kern County; California City'], ['97294', 'zip', '93501; CA; Bakersfield; Kern County; Mojave'], ['97291', 'zip', '93465; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Templeton'], ['9729', 'city', 'Carleton; MI; Monroe; Monroe County'], ['97289', 'zip', '93463; CA; Santa Maria-Santa Barbara; Santa Barbara County; Solvang'], ['97288', 'zip', '93461; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Shandon'], ['97287', 'zip', '93460; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Ynez'], ['97286', 'zip', '93458; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Maria'], ['97283', 'zip', '93455; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Maria'], ['97282', 'zip', '93454; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Maria'], ['97281', 'zip', '93453; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Simmler'], ['97280', 'zip', '93452; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; San Simeon'], ['9728', 'city', 'Town Of Cameron; NY; Corning; Steuben County'], ['97279', 'zip', '93451; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; San Miguel'], ['97278', 'zip', '93450; CA; Salinas; Monterey County; San Ardo'], ['97277', 'zip', '93449; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Pismo Beach'], ['97274', 'zip', '93446; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Paso Robles'], ['97273', 'zip', '93445; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Oceano'], ['97272', 'zip', '93444; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Nipomo'], ['97270', 'zip', '93442; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Morro Bay'], ['97269', 'zip', '93441; CA; Santa Maria-Santa Barbara; Santa Barbara County; Los Olivos'], ['97268', 'zip', '93440; CA; Santa Maria-Santa Barbara; Santa Barbara County; Los Alamos'], ['97265', 'zip', '93436; CA; Santa Maria-Santa Barbara; Santa Barbara County; Lompoc'], ['97264', 'zip', '93435; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Harmony'], ['97263', 'zip', '93434; CA; Santa Maria-Santa Barbara; Santa Barbara County; Guadalupe'], ['97262', 'zip', '93433; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Grover Beach'], ['97261', 'zip', '93432; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Creston'], ['97260', 'zip', '93430; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Cayucos'], ['9726', 'city', 'Califon; NJ; New York-Newark-Jersey City; Hunterdon County'], ['97259', 'zip', '93429; CA; Santa Maria-Santa Barbara; Santa Barbara County; Casmalia'], ['97258', 'zip', '93428; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Cambria'], ['97257', 'zip', '93427; CA; Santa Maria-Santa Barbara; Santa Barbara County; Buellton'], ['97256', 'zip', '93426; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Bradley'], ['97255', 'zip', '93424; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Avila Beach'], ['97253', 'zip', '93422; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Atascadero'], ['97251', 'zip', '93420; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Arroyo Grande'], ['97244', 'zip', '93405; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; San Luis Obispo'], ['97242', 'zip', '93402; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; Los Osos'], ['97241', 'zip', '93401; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County; San Luis Obispo'], ['9723', 'city', 'Smithers; WV; Beckley; Fayette County'], ['97229', 'zip', '93314; CA; Bakersfield; Kern County; Rosedale'], ['97228', 'zip', '93313; CA; Bakersfield; Kern County'], ['97227', 'zip', '93312'], ['97226', 'zip', '93311; CA; Bakersfield; Kern County'], ['97225', 'zip', '93309; CA; Bakersfield; Kern County'], ['97224', 'zip', '93308; CA; Bakersfield; Kern County; Oildale'], ['97223', 'zip', '93307; CA; Bakersfield; Kern County'], ['97222', 'zip', '93306; CA; Bakersfield; Kern County'], ['97221', 'zip', '93305'], ['97220', 'zip', '93304; CA; Bakersfield; Kern County'], ['97217', 'zip', '93301; CA; Bakersfield; Kern County'], ['97216', 'zip', '93292; CA; Visalia-Porterville; Tulare County; Visalia'], ['97215', 'zip', '93291; CA; Visalia-Porterville; Tulare County; Visalia'], ['97213', 'zip', '93287; CA; Bakersfield; Kern County; Woody'], ['97212', 'zip', '93286; CA; Visalia-Porterville; Tulare County; Woodlake'], ['97211', 'zip', '93285; CA; Bakersfield; Kern County; Wofford Heights'], ['97210', 'zip', '93283; CA; Bakersfield; Kern County; Weldon'], ['9721', 'city', 'Saint Paul; IA; Fort Madison-Keokuk; Lee County'], ['97209', 'zip', '93282; CA; Visalia-Porterville; Tulare County; Tulare'], ['97208', 'zip', '93280; CA; Bakersfield; Kern County; Wasco'], ['97205', 'zip', '93277; CA; Visalia-Porterville; Tulare County; Visalia'], ['97204', 'zip', '93276; CA; Bakersfield; Kern County; Tupman'], ['97202', 'zip', '93274; CA; Visalia-Porterville; Tulare County; Tulare'], ['97201', 'zip', '93272; CA; Visalia-Porterville; Tulare County; Tipton'], ['97200', 'zip', '93271; CA; Visalia-Porterville; Tulare County; Three Rivers'], ['9720', 'city', 'Sac City; IA; Sac County'], ['972', 'county', 'Dakota County; NE; Sioux City'], ['97199', 'zip', '93270; CA; Visalia-Porterville; Tulare County; Terra Bella'], ['97198', 'zip', '93268; CA; Bakersfield; Kern County; Taft'], ['97197', 'zip', '93267; CA; Visalia-Porterville; Tulare County; Strathmore'], ['97196', 'zip', '93266; CA; Hanford-Corcoran; Kings County; Stratford'], ['97195', 'zip', '93265; CA; Visalia-Porterville; Tulare County; Springville'], ['97194', 'zip', '93263; CA; Bakersfield; Kern County; Shafter'], ['97192', 'zip', '93261; CA; Visalia-Porterville; Tulare County; Richgrove'], ['97191', 'zip', '93260; CA; Visalia-Porterville; Tulare County; Posey'], ['97190', 'zip', '93258; CA; Visalia-Porterville; Tulare County; Tulare'], ['9719', 'city', 'Tatums; OK; Ardmore; Carter County'], ['97189', 'zip', '93257; CA; Visalia-Porterville; Tulare County; Porterville'], ['97188', 'zip', '93256; CA; Visalia-Porterville; Tulare County; Pixley'], ['97187', 'zip', '93255; CA; Bakersfield; Kern County; Onyx'], ['97185', 'zip', '93252; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Maricopa'], ['97184', 'zip', '93251; CA; Bakersfield; Kern County; Mc Kittrick'], ['97183', 'zip', '93250; CA; Bakersfield; Kern County; Mc Farland'], ['97182', 'zip', '93249; CA; Bakersfield; Kern County; Lost Hills'], ['97181', 'zip', '93247; CA; Visalia-Porterville; Tulare County; Lindsay'], ['97179', 'zip', '93245; CA; Hanford-Corcoran; Kings County; Lemoore'], ['97178', 'zip', '93244; CA; Visalia-Porterville; Tulare County; Lemon Cove'], ['97177', 'zip', '93243; CA; Bakersfield; Kern County; Lebec'], ['97176', 'zip', '93242; CA; Fresno; Fresno County; Laton'], ['97175', 'zip', '93241; CA; Bakersfield; Kern County; Lamont'], ['97174', 'zip', '93240; CA; Bakersfield; Kern County; Lake Isabella'], ['97173', 'zip', '93239; CA; Hanford-Corcoran; Kings County; Kettleman City'], ['97172', 'zip', '93238; CA; Bakersfield; Kern County; Kernville'], ['97170', 'zip', '93235; CA; Visalia-Porterville; Tulare County; Ivanhoe'], ['9717', 'city', 'Swarthmore; PA; Philadelphia-Camden-Wilmington; Delaware County'], ['97169', 'zip', '93234; CA; Fresno; Fresno County; Huron'], ['97167', 'zip', '93230; CA; Hanford-Corcoran; Kings County; Hanford'], ['97166', 'zip', '93227; CA; Visalia-Porterville; Tulare County; Visalia'], ['97165', 'zip', '93226; CA; Bakersfield; Kern County; Glennville'], ['97164', 'zip', '93225; CA; Bakersfield; Kern County; Frazier Park'], ['97163', 'zip', '93224; CA; Bakersfield; Kern County; Fellows'], ['97162', 'zip', '93223; CA; Visalia-Porterville; Tulare County; Farmersville'], ['97161', 'zip', '93222; CA; Bakersfield; Kern County; Pine Mountain Club'], ['97160', 'zip', '93221; CA; Visalia-Porterville; Tulare County; Exeter'], ['97158', 'zip', '93219; CA; Visalia-Porterville; Tulare County; Earlimart'], ['97157', 'zip', '93218; CA; Visalia-Porterville; Tulare County; Ducor'], ['97155', 'zip', '93215; CA; Bakersfield; Kern County; Delano'], ['97154', 'zip', '93212; CA; Hanford-Corcoran; Kings County; Corcoran'], ['97153', 'zip', '93210; CA; Fresno; Fresno County; Coalinga'], ['97151', 'zip', '93207; CA; Visalia-Porterville; Tulare County; California Hot Springs'], ['97150', 'zip', '93206; CA; Bakersfield; Kern County; Buttonwillow'], ['97149', 'zip', '93205; CA; Bakersfield; Kern County; Bodfish'], ['97148', 'zip', '93204; CA; Hanford-Corcoran; Kings County; Avenal'], ['97147', 'zip', '93203; CA; Bakersfield; Kern County; Arvin'], ['97146', 'zip', '93202; CA; Hanford-Corcoran; Kings County; Armona'], ['97145', 'zip', '93201; CA; Visalia-Porterville; Tulare County; Alpaugh'], ['9714', 'city', 'Stockertown; PA; Allentown-Bethlehem-Easton; Northampton County'], ['97135', 'zip', '93117; CA; Santa Maria-Santa Barbara; Santa Barbara County; Goleta'], ['97133', 'zip', '93111; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97132', 'zip', '93110; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97131', 'zip', '93109; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97130', 'zip', '93108; CA; Santa Maria-Santa Barbara; Santa Barbara County; Montecito'], ['97127', 'zip', '93105; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97126', 'zip', '93103; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97124', 'zip', '93101; CA; Santa Maria-Santa Barbara; Santa Barbara County; Santa Barbara'], ['97120', 'zip', '93067; CA; Santa Maria-Santa Barbara; Santa Barbara County; Summerland'], ['97119', 'zip', '93066; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Somis'], ['97118', 'zip', '93065; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Simi Valley'], ['97116', 'zip', '93063'], ['97113', 'zip', '93060; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Santa Paula'], ['9711', 'city', 'Lempster; NH; Claremont-Lebanon; Sullivan County'], ['97109', 'zip', '93041'], ['97108', 'zip', '93040; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Fillmore'], ['97107', 'zip', '93036'], ['97106', 'zip', '93035'], ['97104', 'zip', '93033'], ['97101', 'zip', '93030; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Oxnard'], ['9710', 'city', 'Lee; NH; Boston-Cambridge-Newton; Strafford County'], ['971', 'county', 'Dakota County; MN; Minneapolis-St. Paul-Bloomington'], ['97099', 'zip', '93023; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Ojai'], ['97098', 'zip', '93022; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Oak View'], ['97097', 'zip', '93021'], ['97094', 'zip', '93015; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Fillmore'], ['97092', 'zip', '93013; CA; Santa Maria-Santa Barbara; Santa Barbara County; Carpinteria'], ['97091', 'zip', '93012'], ['97089', 'zip', '93010'], ['97084', 'zip', '93004; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Ventura'], ['97083', 'zip', '93003'], ['97081', 'zip', '93001'], ['97079', 'zip', '92887'], ['97078', 'zip', '92886'], ['97076', 'zip', '92883'], ['97075', 'zip', '92882'], ['97074', 'zip', '92881; CA; Riverside-San Bernardino-Ontario; Riverside County; Corona'], ['97073', 'zip', '92880'], ['97072', 'zip', '92879'], ['9707', 'city', 'Buchanan; VA; Roanoke; Botetourt County'], ['97068', 'zip', '92870'], ['97067', 'zip', '92869'], ['97066', 'zip', '92868'], ['97065', 'zip', '92867'], ['97064', 'zip', '92866'], ['97063', 'zip', '92865'], ['9706', 'city', 'Bryan; OH; Williams County'], ['97059', 'zip', '92861; CA; Los Angeles-Long Beach-Anaheim; Orange County; Villa Park'], ['97058', 'zip', '92860; CA; Riverside-San Bernardino-Ontario; Riverside County; Norco'], ['97052', 'zip', '92845; CA; Los Angeles-Long Beach-Anaheim; Orange County; Garden Grove'], ['97051', 'zip', '92844; CA; Los Angeles-Long Beach-Anaheim; Orange County; Garden Grove'], ['97050', 'zip', '92843; CA; Los Angeles-Long Beach-Anaheim; Orange County; Garden Grove'], ['97048', 'zip', '92841; CA; Los Angeles-Long Beach-Anaheim; Orange County; Garden Grove'], ['97047', 'zip', '92840'], ['97043', 'zip', '92835; CA; Los Angeles-Long Beach-Anaheim; Orange County; Fullerton'], ['97041', 'zip', '92833'], ['97040', 'zip', '92832'], ['97039', 'zip', '92831'], ['97037', 'zip', '92823; CA; Los Angeles-Long Beach-Anaheim; Orange County; Brea'], ['97035', 'zip', '92821'], ['97027', 'zip', '92808'], ['97026', 'zip', '92807'], ['97025', 'zip', '92806'], ['97024', 'zip', '92805'], ['97023', 'zip', '92804'], ['97021', 'zip', '92802'], ['97020', 'zip', '92801; CA; Los Angeles-Long Beach-Anaheim; Orange County; Anaheim'], ['97018', 'zip', '92782'], ['97016', 'zip', '92780'], ['97008', 'zip', '92708'], ['97007', 'zip', '92707; CA; Los Angeles-Long Beach-Anaheim; Orange County; Santa Ana'], ['97006', 'zip', '92706; CA; Los Angeles-Long Beach-Anaheim; Orange County; Santa Ana'], ['97005', 'zip', '92705'], ['97004', 'zip', '92704'], ['97003', 'zip', '92703; CA; Los Angeles-Long Beach-Anaheim; Orange County; Santa Ana'], ['97001', 'zip', '92701; CA; Los Angeles-Long Beach-Anaheim; Orange County; Santa Ana'], ['970', 'county', 'Custer County; SD; Rapid City'], ['97', 'county', 'Bell County; KY; Middlesborough'], ['96998', 'zip', '92694'], ['96996', 'zip', '92692'], ['96995', 'zip', '92691'], ['96993', 'zip', '92688'], ['96990', 'zip', '92683'], ['9699', 'city', 'Rockholds; KY; London; Whitley County'], ['96989', 'zip', '92679'], ['96987', 'zip', '92677'], ['96986', 'zip', '92676; CA; Los Angeles-Long Beach-Anaheim; Orange County; Silverado'], ['96985', 'zip', '92675'], ['96983', 'zip', '92673'], ['96982', 'zip', '92672'], ['96981', 'zip', '92663'], ['96980', 'zip', '92662; CA; Los Angeles-Long Beach-Anaheim; Orange County; Newport Beach'], ['96979', 'zip', '92661'], ['96978', 'zip', '92660'], ['96975', 'zip', '92657'], ['96974', 'zip', '92656'], ['96973', 'zip', '92655; CA; Los Angeles-Long Beach-Anaheim; Orange County; Westminster'], ['96971', 'zip', '92653'], ['9697', 'city', 'Railroad; PA; York-Hanover; York County'], ['96969', 'zip', '92651'], ['96967', 'zip', '92649'], ['96966', 'zip', '92648'], ['96965', 'zip', '92647'], ['96964', 'zip', '92646'], ['96963', 'zip', '92637'], ['96962', 'zip', '92630'], ['96961', 'zip', '92629'], ['9696', 'city', 'Queenstown; MD; Baltimore-Columbia-Towson; Queen Annes County'], ['96959', 'zip', '92627'], ['96958', 'zip', '92626'], ['96957', 'zip', '92625'], ['96956', 'zip', '92624'], ['96954', 'zip', '92620'], ['96952', 'zip', '92618'], ['96951', 'zip', '92617; CA; Los Angeles-Long Beach-Anaheim; Orange County; Irvine'], ['9695', 'city', 'Preston; GA; Webster County'], ['96948', 'zip', '92614'], ['96947', 'zip', '92612'], ['96946', 'zip', '92610'], ['96943', 'zip', '92606'], ['96941', 'zip', '92604'], ['96940', 'zip', '92603'], ['9694', 'city', 'Powersville; MO; Putnam County'], ['96939', 'zip', '92602'], ['96937', 'zip', '92596'], ['96936', 'zip', '92595'], ['96934', 'zip', '92592'], ['96933', 'zip', '92591'], ['96932', 'zip', '92590; CA; Riverside-San Bernardino-Ontario; Riverside County; Temecula'], ['96930', 'zip', '92587; CA; Riverside-San Bernardino-Ontario; Riverside County; Canyon Lake'], ['96929', 'zip', '92586'], ['96928', 'zip', '92585; CA; Riverside-San Bernardino-Ontario; Riverside County; Menifee'], ['96927', 'zip', '92584'], ['96926', 'zip', '92583; CA; Riverside-San Bernardino-Ontario; Riverside County; San Jacinto'], ['96925', 'zip', '92582; CA; Riverside-San Bernardino-Ontario; Riverside County; San Jacinto'], ['96922', 'zip', '92571'], ['96921', 'zip', '92570; CA; Riverside-San Bernardino-Ontario; Riverside County; Perris'], ['96920', 'zip', '92567; CA; Riverside-San Bernardino-Ontario; Riverside County; Nuevo'], ['96918', 'zip', '92563'], ['96917', 'zip', '92562'], ['96916', 'zip', '92561; CA; Riverside-San Bernardino-Ontario; Riverside County; Mountain Center'], ['96915', 'zip', '92557'], ['96913', 'zip', '92555'], ['96911', 'zip', '92553'], ['96909', 'zip', '92551; CA; Riverside-San Bernardino-Ontario; Riverside County; Moreno Valley'], ['96908', 'zip', '92549; CA; Riverside-San Bernardino-Ontario; Riverside County; Idyllwild'], ['96907', 'zip', '92548; CA; Riverside-San Bernardino-Ontario; Riverside County; Homeland'], ['96905', 'zip', '92545'], ['96904', 'zip', '92544; CA; Riverside-San Bernardino-Ontario; Riverside County; Hemet'], ['96903', 'zip', '92543; CA; Riverside-San Bernardino-Ontario; Riverside County; Hemet'], ['96902', 'zip', '92539; CA; Riverside-San Bernardino-Ontario; Riverside County; Anza'], ['96901', 'zip', '92536; CA; Riverside-San Bernardino-Ontario; Riverside County; Aguanga'], ['96900', 'zip', '92532'], ['969', 'county', 'Custer County; OK; Weatherford'], ['96898', 'zip', '92530'], ['9689', 'city', 'Severn; NC; Roanoke Rapids; Northampton County'], ['96888', 'zip', '92509; CA; Riverside-San Bernardino-Ontario; Riverside County; Riverside'], ['96887', 'zip', '92508'], ['96886', 'zip', '92507'], ['96885', 'zip', '92506; CA; Riverside-San Bernardino-Ontario; Riverside County; Riverside'], ['96884', 'zip', '92505'], ['96883', 'zip', '92504'], ['96882', 'zip', '92503'], ['96880', 'zip', '92501; CA; Riverside-San Bernardino-Ontario; Riverside County; Riverside'], ['96871', 'zip', '92411; CA; Riverside-San Bernardino-Ontario; San Bernardino County; San Bernardino'], ['96870', 'zip', '92410; CA; Riverside-San Bernardino-Ontario; San Bernardino County; San Bernardino'], ['9687', 'city', 'Scranton; SC; Florence; Florence County'], ['96869', 'zip', '92408; CA; Riverside-San Bernardino-Ontario; San Bernardino County; San Bernardino'], ['96868', 'zip', '92407'], ['96866', 'zip', '92405'], ['96865', 'zip', '92404'], ['96862', 'zip', '92401; CA; Riverside-San Bernardino-Ontario; San Bernardino County; San Bernardino'], ['96861', 'zip', '92399'], ['96860', 'zip', '92398; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Yermo'], ['9686', 'city', 'Saxapahaw; NC; Burlington; Alamance County'], ['96859', 'zip', '92397; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Wrightwood'], ['96858', 'zip', '92395'], ['96857', 'zip', '92394'], ['96855', 'zip', '92392; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Victorville'], ['96854', 'zip', '92391; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['96853', 'zip', '92389; CA; Inyo County; Shoshone'], ['96852', 'zip', '92386; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Big Bear City'], ['96851', 'zip', '92385; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['9685', 'city', 'Lancaster; KS; Atchison; Atchison County'], ['96849', 'zip', '92382; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Running Springs'], ['96848', 'zip', '92378; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['96847', 'zip', '92377; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Rialto'], ['96846', 'zip', '92376'], ['96844', 'zip', '92374'], ['96843', 'zip', '92373'], ['96842', 'zip', '92372; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Pinon Hills'], ['96841', 'zip', '92371; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Phelan'], ['9684', 'city', 'Lambert; MT; Richland County'], ['96839', 'zip', '92368; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Helendale'], ['96837', 'zip', '92365; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Newberry Springs'], ['96835', 'zip', '92363; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Needles'], ['96834', 'zip', '92359; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Mentone'], ['96833', 'zip', '92358; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lytle Creek'], ['96831', 'zip', '92356; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lucerne Valley'], ['96830', 'zip', '92354'], ['9683', 'city', 'Lakeview; OH; Bellefontaine; Logan County'], ['96829', 'zip', '92352; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['96827', 'zip', '92347; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Hinkley'], ['96826', 'zip', '92346; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Highland'], ['96825', 'zip', '92345'], ['96824', 'zip', '92344; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Hesperia'], ['96823', 'zip', '92342; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Helendale'], ['96822', 'zip', '92341; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Green Valley Lake'], ['96820', 'zip', '92339; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Forest Falls'], ['9682', 'neigh', 'Lakeshore; LA; Monroe; Ouachita Parish'], ['96818', 'zip', '92337'], ['96817', 'zip', '92336'], ['96816', 'zip', '92335'], ['96814', 'zip', '92333; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Fawnskin'], ['96813', 'zip', '92332; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Essex'], ['96810', 'zip', '92327; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Daggett'], ['9681', 'city', 'Lake Junaluska; NC; Asheville; Haywood County'], ['96808', 'zip', '92325; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Crestline'], ['96807', 'zip', '92324'], ['96805', 'zip', '92322; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Crestline'], ['96804', 'zip', '92321; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['96803', 'zip', '92320; CA; Riverside-San Bernardino-Ontario; Riverside County; Calimesa'], ['96802', 'zip', '92318; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Loma Linda'], ['96801', 'zip', '92317; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lake Arrowhead'], ['96800', 'zip', '92316; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Bloomington'], ['968', 'county', 'Custer County; NE'], ['96799', 'zip', '92315; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Big Bear Lake'], ['96798', 'zip', '92314; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Big Bear City'], ['96797', 'zip', '92313; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Grand Terrace'], ['96795', 'zip', '92311'], ['96793', 'zip', '92309; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Baker'], ['96792', 'zip', '92308; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Apple Valley'], ['96791', 'zip', '92307'], ['96790', 'zip', '92305; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Angelus Oaks'], ['96788', 'zip', '92301; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Adelanto'], ['96785', 'zip', '92285; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Landers'], ['96784', 'zip', '92284'], ['96782', 'zip', '92282; CA; Riverside-San Bernardino-Ontario; Riverside County; White Water'], ['96781', 'zip', '92281; CA; El Centro; Imperial County; Westmorland'], ['96778', 'zip', '92277'], ['96777', 'zip', '92276; CA; Riverside-San Bernardino-Ontario; Riverside County; Thousand Palms'], ['96776', 'zip', '92275; CA; El Centro; Imperial County; Thermal'], ['96775', 'zip', '92274; CA; El Centro; Imperial County; Thermal'], ['96774', 'zip', '92273; CA; El Centro; Imperial County'], ['96773', 'zip', '92270'], ['96772', 'zip', '92268; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Yucca Valley'], ['9677', 'city', 'Kingston Springs; TN; Nashville-Davidson--Murfreesboro--Franklin; Cheatham County'], ['96769', 'zip', '92264'], ['96767', 'zip', '92262'], ['96765', 'zip', '92260'], ['96763', 'zip', '92258; CA; Riverside-San Bernardino-Ontario; Riverside County; Desert Hot Springs'], ['96761', 'zip', '92256; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Morongo Valley'], ['96759', 'zip', '92254; CA; Riverside-San Bernardino-Ontario; Riverside County; Mecca'], ['96758', 'zip', '92253'], ['96757', 'zip', '92252; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Joshua Tree'], ['96756', 'zip', '92251; CA; El Centro; Imperial County; Imperial'], ['96755', 'zip', '92250; CA; El Centro; Imperial County; Holtville'], ['96754', 'zip', '92249; CA; El Centro; Imperial County; Heber'], ['96750', 'zip', '92243; CA; El Centro; Imperial County'], ['9675', 'city', 'Boardman; OR; Hermiston-Pendleton; Morrow County'], ['96749', 'zip', '92242; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Earp'], ['96748', 'zip', '92241; CA; Riverside-San Bernardino-Ontario; Riverside County; Desert Hot Springs'], ['96747', 'zip', '92240'], ['96746', 'zip', '92239; CA; Riverside-San Bernardino-Ontario; Riverside County; Desert Center'], ['96745', 'zip', '92236; CA; Riverside-San Bernardino-Ontario; Riverside County; Coachella'], ['96743', 'zip', '92234'], ['96742', 'zip', '92233; CA; El Centro; Imperial County; Calipatria'], ['96740', 'zip', '92231; CA; El Centro; Imperial County; Calexico'], ['96739', 'zip', '92230; CA; Riverside-San Bernardino-Ontario; Riverside County; Cabazon'], ['96738', 'zip', '92227; CA; El Centro; Imperial County; Brawley'], ['96736', 'zip', '92225; CA; Riverside-San Bernardino-Ontario; Riverside County; Blythe'], ['96735', 'zip', '92223'], ['96733', 'zip', '92220; CA; Riverside-San Bernardino-Ontario; Riverside County; Banning'], ['96732', 'zip', '92211'], ['96731', 'zip', '92210'], ['96730', 'zip', '92203'], ['96728', 'zip', '92201'], ['96707', 'zip', '92173; CA; San Diego-Carlsbad; San Diego County; San Diego'], ['9670', 'city', 'Berwick; LA; Morgan City; Saint Mary Parish'], ['967', 'county', 'Custer County; MT'], ['96690', 'zip', '92154'], ['9669', 'city', 'Belvidere; NJ; Allentown-Bethlehem-Easton; Warren County'], ['96680', 'zip', '92139'], ['9668', 'city', 'Pinetta; FL; Madison County'], ['96672', 'zip', '92131'], ['96671', 'zip', '92130'], ['96670', 'zip', '92129'], ['9667', 'city', 'Pawnee; IL; Springfield; Sangamon County'], ['96669', 'zip', '92128'], ['96668', 'zip', '92127'], ['96667', 'zip', '92126'], ['96666', 'zip', '92124'], ['96665', 'zip', '92123'], ['96664', 'zip', '92122'], ['96663', 'zip', '92121; CA; San Diego-Carlsbad; San Diego County; San Diego'], ['96662', 'zip', '92120'], ['96661', 'zip', '92119; CA; San Diego-Carlsbad; San Diego County; San Diego'], ['96660', 'zip', '92118'], ['96659', 'zip', '92117'], ['96658', 'zip', '92116'], ['96657', 'zip', '92115'], ['96656', 'zip', '92114'], ['96655', 'zip', '92113'], ['96653', 'zip', '92111'], ['96652', 'zip', '92110'], ['96651', 'zip', '92109'], ['96650', 'zip', '92108'], ['9665', 'city', 'Page; ND; Fargo; Cass County'], ['96649', 'zip', '92107'], ['96648', 'zip', '92106'], ['96647', 'zip', '92105'], ['96646', 'zip', '92104'], ['96645', 'zip', '92103'], ['96644', 'zip', '92102'], ['96643', 'zip', '92101'], ['9664', 'city', 'Oronoco; MN; Rochester; Olmsted County'], ['96639', 'zip', '92091; CA; San Diego-Carlsbad; San Diego County; Rancho Santa Fe'], ['96636', 'zip', '92086; CA; San Diego-Carlsbad; San Diego County; Warner Springs'], ['96634', 'zip', '92084'], ['96633', 'zip', '92083; CA; San Diego-Carlsbad; San Diego County; Vista'], ['96632', 'zip', '92082; CA; San Diego-Carlsbad; San Diego County; Valley Center'], ['96631', 'zip', '92081'], ['96629', 'zip', '92078'], ['96628', 'zip', '92075'], ['96625', 'zip', '92071'], ['96624', 'zip', '92070; CA; San Diego-Carlsbad; San Diego County; Santa Ysabel'], ['96623', 'zip', '92069'], ['96621', 'zip', '92067; CA; San Diego-Carlsbad; San Diego County; Rancho Santa Fe'], ['96620', 'zip', '92066; CA; San Diego-Carlsbad; San Diego County; Ranchita'], ['9662', 'city', 'Oregon City; OR; Portland-Vancouver-Hillsboro; Clackamas County'], ['96619', 'zip', '92065'], ['96618', 'zip', '92064'], ['96617', 'zip', '92061; CA; San Diego-Carlsbad; San Diego County; Pauma Valley'], ['96616', 'zip', '92060; CA; San Diego-Carlsbad; San Diego County; Palomar Mountain'], ['96615', 'zip', '92059; CA; San Diego-Carlsbad; San Diego County; Pala'], ['96614', 'zip', '92058'], ['96613', 'zip', '92057'], ['96612', 'zip', '92056'], ['96610', 'zip', '92054'], ['9661', 'city', 'Onyx; CA; Bakersfield; Kern County'], ['96605', 'zip', '92040'], ['96602', 'zip', '92037'], ['96601', 'zip', '92036; CA; San Diego-Carlsbad; San Diego County; Julian'], ['9660', 'city', 'Treynor; IA; Omaha-Council Bluffs; Pottawattamie County'], ['966', 'county', 'Custer County; ID'], ['96598', 'zip', '92029'], ['96597', 'zip', '92028'], ['96596', 'zip', '92027'], ['96595', 'zip', '92026'], ['96594', 'zip', '92025'], ['96593', 'zip', '92024'], ['96590', 'zip', '92021'], ['9659', 'city', 'Thorndale; PA; Philadelphia-Camden-Wilmington; Chester County'], ['96589', 'zip', '92020'], ['96588', 'zip', '92019; CA; San Diego-Carlsbad; San Diego County; El Cajon'], ['96586', 'zip', '92014'], ['96584', 'zip', '92011'], ['96583', 'zip', '92010'], ['96582', 'zip', '92009'], ['96581', 'zip', '92008'], ['96580', 'zip', '92007; CA; San Diego-Carlsbad; San Diego County; Encinitas'], ['96579', 'zip', '92004; CA; San Diego-Carlsbad; San Diego County; Borrego Springs'], ['96578', 'zip', '92003; CA; San Diego-Carlsbad; San Diego County; Bonsall'], ['96575', 'zip', '91980; CA; San Diego-Carlsbad; San Diego County; Tecate'], ['96573', 'zip', '91978; CA; San Diego-Carlsbad; San Diego County; Spring Valley'], ['96572', 'zip', '91977'], ['96570', 'zip', '91963; CA; San Diego-Carlsbad; San Diego County; Potrero'], ['9657', 'city', 'Kendrick; OK; Oklahoma City; Lincoln County'], ['96569', 'zip', '91962; CA; San Diego-Carlsbad; San Diego County; Descanso'], ['96567', 'zip', '91950; CA; San Diego-Carlsbad; San Diego County; National City'], ['96566', 'zip', '91948; CA; San Diego-Carlsbad; San Diego County; Mount Laguna'], ['96563', 'zip', '91945'], ['96560', 'zip', '91942'], ['9656', 'city', 'Keavy; KY; London; Laurel County'], ['96559', 'zip', '91941'], ['96558', 'zip', '91935; CA; San Diego-Carlsbad; San Diego County; Jamul'], ['96557', 'zip', '91934; CA; San Diego-Carlsbad; San Diego County; Jacumba'], ['96555', 'zip', '91932'], ['96554', 'zip', '91931; CA; San Diego-Carlsbad; San Diego County; Descanso'], ['96552', 'zip', '91917; CA; San Diego-Carlsbad; San Diego County; Dulzura'], ['96551', 'zip', '91916; CA; San Diego-Carlsbad; San Diego County; Descanso'], ['96550', 'zip', '91915'], ['9655', 'city', 'Jonesborough; TN; Johnson City; Washington County'], ['96549', 'zip', '91914; CA; San Diego-Carlsbad; San Diego County; Chula Vista'], ['96548', 'zip', '91913'], ['96546', 'zip', '91911'], ['96545', 'zip', '91910'], ['96542', 'zip', '91906; CA; San Diego-Carlsbad; San Diego County; Campo'], ['96541', 'zip', '91905; CA; San Diego-Carlsbad; San Diego County; Boulevard'], ['9654', 'city', 'Jarreau; LA; Baton Rouge; Pointe Coupee Parish'], ['96539', 'zip', '91902; CA; San Diego-Carlsbad; San Diego County; Bonita'], ['96538', 'zip', '91901; CA; San Diego-Carlsbad; San Diego County; Alpine'], ['96533', 'zip', '91803; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Alhambra'], ['96531', 'zip', '91801'], ['9653', 'city', 'Jacksons Gap; AL; Alexander City; Tallapoosa County'], ['96525', 'zip', '91792; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; West Covina'], ['96524', 'zip', '91791'], ['96523', 'zip', '91790'], ['96522', 'zip', '91789'], ['96520', 'zip', '91786'], ['96518', 'zip', '91784'], ['96517', 'zip', '91780'], ['96515', 'zip', '91776; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; San Gabriel'], ['96514', 'zip', '91775'], ['96513', 'zip', '91773'], ['96510', 'zip', '91770; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Rosemead'], ['9651', 'city', 'Beaconsfield; IA; Ringgold County'], ['96508', 'zip', '91768; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Pomona'], ['96507', 'zip', '91767'], ['96506', 'zip', '91766'], ['96505', 'zip', '91765'], ['96504', 'zip', '91764'], ['96503', 'zip', '91763; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Montclair'], ['96502', 'zip', '91762'], ['96501', 'zip', '91761'], ['96500', 'zip', '91759; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Lytle Creek'], ['9650', 'city', 'Bardolph; IL; Macomb; McDonough County'], ['965', 'county', 'Custer County; CO'], ['96497', 'zip', '91755; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Monterey Park'], ['96496', 'zip', '91754; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Monterey Park'], ['96495', 'zip', '91752; CA; Riverside-San Bernardino-Ontario; Riverside County; Eastvale'], ['96494', 'zip', '91750'], ['96492', 'zip', '91748; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Rowland Heights'], ['96490', 'zip', '91746; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; West Puente Valley'], ['96489', 'zip', '91745'], ['96488', 'zip', '91744; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; La Puente'], ['96486', 'zip', '91741'], ['96485', 'zip', '91740'], ['96484', 'zip', '91739'], ['96483', 'zip', '91737'], ['96480', 'zip', '91733; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; South El Monte'], ['9648', 'city', 'Oak Bluffs; MA; Vineyard Haven; Dukes County'], ['96479', 'zip', '91732; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; El Monte'], ['96478', 'zip', '91731; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; El Monte'], ['96477', 'zip', '91730'], ['96475', 'zip', '91724; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Covina'], ['96474', 'zip', '91723'], ['96473', 'zip', '91722'], ['96469', 'zip', '91711'], ['96468', 'zip', '91710'], ['96467', 'zip', '91709'], ['96466', 'zip', '91708; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Chino'], ['96465', 'zip', '91706'], ['96464', 'zip', '91702'], ['96463', 'zip', '91701; CA; Riverside-San Bernardino-Ontario; San Bernardino County; Rancho Cucamonga'], ['9646', 'neigh', 'North Grosvenordale; CT; Worcester; Windham County; Thompson'], ['96452', 'zip', '91607'], ['96451', 'zip', '91606'], ['96450', 'zip', '91605'], ['9645', 'city', 'Nekoma; KS; Rush County'], ['96449', 'zip', '91604'], ['96447', 'zip', '91602'], ['96446', 'zip', '91601'], ['9644', 'city', 'Mullens; WV; Wyoming County'], ['96438', 'zip', '91506; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Burbank'], ['96437', 'zip', '91505'], ['96436', 'zip', '91504'], ['96434', 'zip', '91502'], ['96433', 'zip', '91501'], ['96426', 'zip', '91436'], ['96424', 'zip', '91423'], ['96420', 'zip', '91411'], ['9642', 'city', 'Stanton; MI; Grand Rapids-Wyoming; Montcalm County'], ['96415', 'zip', '91406'], ['96414', 'zip', '91405'], ['96412', 'zip', '91403'], ['96411', 'zip', '91402; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96410', 'zip', '91401'], ['9641', 'city', 'Spring Valley; OH; Dayton; Greene County'], ['96403', 'zip', '91390; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Santa Clarita'], ['96401', 'zip', '91387'], ['964', 'county', 'Cuming County; NE'], ['96398', 'zip', '91384; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Castaic'], ['96395', 'zip', '91381'], ['96393', 'zip', '91377'], ['9639', 'city', 'South Webster; OH; Portsmouth; Scioto County'], ['96389', 'zip', '91367'], ['96387', 'zip', '91364'], ['96385', 'zip', '91362'], ['96384', 'zip', '91361; CA; Oxnard-Thousand Oaks-Ventura; Ventura County; Westlake Village'], ['96383', 'zip', '91360'], ['96379', 'zip', '91356'], ['96378', 'zip', '91355'], ['96377', 'zip', '91354'], ['96375', 'zip', '91352; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96374', 'zip', '91351'], ['96373', 'zip', '91350; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Santa Clarita'], ['96371', 'zip', '91345; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96370', 'zip', '91344'], ['96369', 'zip', '91343'], ['96368', 'zip', '91342'], ['96366', 'zip', '91340; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; San Fernando'], ['96364', 'zip', '91335'], ['96361', 'zip', '91331; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96356', 'zip', '91326'], ['96355', 'zip', '91325'], ['96354', 'zip', '91324'], ['96352', 'zip', '91321; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Santa Clarita'], ['96351', 'zip', '91320'], ['9635', 'city', 'Au Train; MI; Alger County'], ['96349', 'zip', '91316'], ['96346', 'zip', '91311'], ['96342', 'zip', '91307'], ['96341', 'zip', '91306'], ['96339', 'zip', '91304'], ['96338', 'zip', '91303'], ['96337', 'zip', '91302'], ['96336', 'zip', '91301'], ['96330', 'zip', '91214'], ['96329', 'zip', '91210; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Glendale'], ['96327', 'zip', '91208; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Glendale'], ['96326', 'zip', '91207; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Glendale'], ['96325', 'zip', '91206'], ['96324', 'zip', '91205'], ['96323', 'zip', '91204; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Glendale'], ['96322', 'zip', '91203; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Glendale'], ['96321', 'zip', '91202'], ['96320', 'zip', '91201'], ['9632', 'neigh', 'Mount Lebanon; PA; Pittsburgh; Allegheny County'], ['9630', 'city', 'Morganville; KS; Clay County'], ['963', 'county', 'Crosby County; TX; Lubbock'], ['96296', 'zip', '91108'], ['96295', 'zip', '91107'], ['96294', 'zip', '91106'], ['96293', 'zip', '91105; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Pasadena'], ['96292', 'zip', '91104'], ['96291', 'zip', '91103'], ['9629', 'city', 'Montgomery Creek; CA; Redding; Shasta County'], ['96289', 'zip', '91101'], ['96284', 'zip', '91042; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96282', 'zip', '91040; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96280', 'zip', '91030; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; South Pasadena'], ['9628', 'city', 'Minnesota Lake; MN; Faribault County'], ['96278', 'zip', '91024; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Sierra Madre'], ['96275', 'zip', '91020; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; La Crescenta-Montrose'], ['96273', 'zip', '91016'], ['96271', 'zip', '91011; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; La Canada Flintridge'], ['96270', 'zip', '91010'], ['96268', 'zip', '91007'], ['96267', 'zip', '91006'], ['96265', 'zip', '91001; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Altadena'], ['9625', 'city', 'Maywood; NE; Frontier County'], ['96247', 'zip', '90815'], ['96246', 'zip', '90814'], ['96245', 'zip', '90813'], ['96244', 'zip', '90810; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Long Beach'], ['96242', 'zip', '90808'], ['96241', 'zip', '90807'], ['96240', 'zip', '90806'], ['9624', 'city', 'Sandstone; MN; Pine County'], ['96239', 'zip', '90805'], ['96238', 'zip', '90804'], ['96237', 'zip', '90803'], ['96236', 'zip', '90802'], ['96234', 'zip', '90755'], ['96230', 'zip', '90746; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Carson'], ['96229', 'zip', '90745'], ['96228', 'zip', '90744; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96227', 'zip', '90743; CA; Los Angeles-Long Beach-Anaheim; Orange County; Seal Beach'], ['96226', 'zip', '90742; CA; Los Angeles-Long Beach-Anaheim; Orange County; Huntington Beach'], ['96225', 'zip', '90740'], ['96222', 'zip', '90732'], ['96221', 'zip', '90731'], ['96220', 'zip', '90723; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Paramount'], ['9622', 'city', 'Sallis; MS; Attala County'], ['96218', 'zip', '90720'], ['96217', 'zip', '90717'], ['96216', 'zip', '90716; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Hawaiian Gardens'], ['96215', 'zip', '90715; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Lakewood'], ['96213', 'zip', '90713'], ['96212', 'zip', '90712; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Lakewood'], ['96210', 'zip', '90710; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96208', 'zip', '90706'], ['96207', 'zip', '90704; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Avalon'], ['96206', 'zip', '90703'], ['96204', 'zip', '90701; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Artesia'], ['96203', 'zip', '90680'], ['96201', 'zip', '90670; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Santa Fe Springs'], ['962', 'county', 'Craven County; NC; New Bern'], ['96197', 'zip', '90660; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Pico Rivera'], ['96193', 'zip', '90650; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Norwalk'], ['96192', 'zip', '90640; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Montebello'], ['96190', 'zip', '90638'], ['9619', 'city', 'Helvetia; WV; Elkins; Randolph County'], ['96186', 'zip', '90631'], ['96185', 'zip', '90630'], ['96183', 'zip', '90623; CA; Los Angeles-Long Beach-Anaheim; Orange County; La Palma'], ['96181', 'zip', '90621'], ['96180', 'zip', '90620'], ['9618', 'city', 'Harrisville; NY; Ogdensburg-Massena; Saint Lawrence County'], ['96174', 'zip', '90606; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Whittier'], ['96173', 'zip', '90605; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Whittier'], ['96172', 'zip', '90604; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Whittier'], ['96171', 'zip', '90603; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Whittier'], ['96170', 'zip', '90602'], ['96169', 'zip', '90601'], ['96163', 'zip', '90505'], ['96162', 'zip', '90504'], ['96161', 'zip', '90503'], ['96160', 'zip', '90502; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; West Carson'], ['96159', 'zip', '90501'], ['96152', 'zip', '90405'], ['96151', 'zip', '90404'], ['96150', 'zip', '90403'], ['9615', 'city', 'Ansted; WV; Beckley; Fayette County'], ['96149', 'zip', '90402'], ['96148', 'zip', '90401'], ['96137', 'zip', '90305; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Inglewood'], ['96136', 'zip', '90304; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Inglewood'], ['96135', 'zip', '90303; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Inglewood'], ['96134', 'zip', '90302; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Inglewood'], ['96133', 'zip', '90301; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Inglewood'], ['96129', 'zip', '90293'], ['96128', 'zip', '90292'], ['96127', 'zip', '90291'], ['96126', 'zip', '90290; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Topanga'], ['96125', 'zip', '90280; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; South Gate'], ['96124', 'zip', '90278'], ['96123', 'zip', '90277'], ['96122', 'zip', '90275'], ['96121', 'zip', '90274'], ['96120', 'zip', '90272'], ['96119', 'zip', '90270; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Maywood'], ['96117', 'zip', '90266'], ['96116', 'zip', '90265'], ['96113', 'zip', '90262; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Lynwood'], ['96111', 'zip', '90260; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Lawndale'], ['96110', 'zip', '90255; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Huntington Park'], ['96109', 'zip', '90254'], ['96107', 'zip', '90250'], ['96106', 'zip', '90249; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Gardena'], ['96105', 'zip', '90248; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96104', 'zip', '90247; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Gardena'], ['96103', 'zip', '90245'], ['96102', 'zip', '90242'], ['96101', 'zip', '90241; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Downey'], ['96100', 'zip', '90240; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Downey'], ['9610', 'city', 'Algona; IA; Kossuth County'], ['961', 'county', 'Cowley County; KS; Arkansas City-Winfield'], ['96097', 'zip', '90232'], ['96095', 'zip', '90230'], ['96092', 'zip', '90222; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Compton'], ['96091', 'zip', '90221; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Compton'], ['96090', 'zip', '90220; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Compton'], ['96088', 'zip', '90212'], ['96087', 'zip', '90211'], ['96086', 'zip', '90210'], ['96083', 'zip', '90201'], ['96072', 'zip', '90094'], ['9607', 'city', 'Lutcher; LA; New Orleans-Metairie; Saint James Parish'], ['96058', 'zip', '90077'], ['96050', 'zip', '90069'], ['9605', 'city', 'Luana; IA; Clayton County'], ['96049', 'zip', '90068'], ['96048', 'zip', '90067; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96047', 'zip', '90066'], ['96046', 'zip', '90065'], ['96045', 'zip', '90064'], ['96044', 'zip', '90063; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; East Los Angeles'], ['96043', 'zip', '90062; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96042', 'zip', '90061; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96040', 'zip', '90059; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['9604', 'city', 'Long Branch; TX; Panola County'], ['96039', 'zip', '90058; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96038', 'zip', '90057; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96037', 'zip', '90056; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Ladera Heights'], ['96030', 'zip', '90049'], ['9603', 'city', 'Loganville; WI; Baraboo; Sauk County'], ['96029', 'zip', '90048'], ['96028', 'zip', '90047'], ['96027', 'zip', '90046'], ['96026', 'zip', '90045'], ['96025', 'zip', '90044; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96024', 'zip', '90043; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96023', 'zip', '90042'], ['96022', 'zip', '90041'], ['96021', 'zip', '90040; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Commerce'], ['96020', 'zip', '90039'], ['9602', 'city', 'Leander; TX; Austin-Round Rock; Williamson County'], ['96019', 'zip', '90038'], ['96018', 'zip', '90037'], ['96017', 'zip', '90036'], ['96016', 'zip', '90035'], ['96015', 'zip', '90034'], ['96014', 'zip', '90033; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96013', 'zip', '90032'], ['96012', 'zip', '90031; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96010', 'zip', '90029'], ['96009', 'zip', '90028'], ['96008', 'zip', '90027'], ['96007', 'zip', '90026'], ['96006', 'zip', '90025'], ['96005', 'zip', '90024'], ['96004', 'zip', '90023; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96003', 'zip', '90022; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; East Los Angeles'], ['96002', 'zip', '90021; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['96001', 'zip', '90020'], ['96000', 'zip', '90019'], ['960', 'county', 'Coweta County; GA; Atlanta-Sandy Springs-Roswell'], ['96', 'county', 'Bath County; VA'], ['95999', 'zip', '90018'], ['95998', 'zip', '90017'], ['95997', 'zip', '90016'], ['95996', 'zip', '90015'], ['95995', 'zip', '90014; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95994', 'zip', '90013'], ['95993', 'zip', '90012'], ['95992', 'zip', '90011; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95991', 'zip', '90010; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95989', 'zip', '90008; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95988', 'zip', '90007'], ['95987', 'zip', '90006'], ['95986', 'zip', '90005; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95985', 'zip', '90004'], ['95984', 'zip', '90003; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95983', 'zip', '90002; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Los Angeles'], ['95982', 'zip', '90001; CA; Los Angeles-Long Beach-Anaheim; Los Angeles County; Florence-Graham'], ['95981', 'zip', '89883; NV; Elko; Elko County; West Wendover'], ['95980', 'zip', '89835; NV; Elko; Elko County; Wells'], ['9598', 'city', 'Rowland; NC; Lumberton; Robeson County'], ['95979', 'zip', '89834; NV; Elko; Elko County; Tuscarora'], ['95978', 'zip', '89833; NV; Elko; Elko County; Ruby Valley'], ['95976', 'zip', '89831; NV; Elko; Elko County; Mountain City'], ['95974', 'zip', '89828; NV; Elko; Elko County; Lamoille'], ['95973', 'zip', '89826; NV; Elko; Elko County; Jarbidge'], ['95972', 'zip', '89825; NV; Elko; Elko County; Jackpot'], ['95970', 'zip', '89823; NV; Elko; Elko County; Halleck'], ['95969', 'zip', '89822; NV; Elko; Elko County; Carlin'], ['95968', 'zip', '89821; NV; Elko; Eureka County; Crescent Valley'], ['95967', 'zip', '89820; NV; Lander County; Battle Mountain'], ['95966', 'zip', '89815; NV; Elko; Elko County; Spring Creek'], ['95963', 'zip', '89801; NV; Elko; Elko County'], ['9596', 'city', 'Ronceverte; WV; Greenbrier County'], ['95957', 'zip', '89706; NV; Carson City'], ['95956', 'zip', '89705; NV; Carson City'], ['95955', 'zip', '89704; NV; Carson City'], ['95954', 'zip', '89703; NV; Carson City'], ['95952', 'zip', '89701; NV; Carson City'], ['95945', 'zip', '89523; NV; Reno; Washoe County'], ['95944', 'zip', '89521; NV; Reno; Washoe County'], ['95940', 'zip', '89512; NV; Reno; Washoe County'], ['9594', 'city', 'Rocky Ford; CO; Otero County'], ['95939', 'zip', '89511; NV; Reno; Washoe County'], ['95938', 'zip', '89510; NV; Reno; Washoe County; Sparks'], ['95937', 'zip', '89509; NV; Reno; Washoe County'], ['95935', 'zip', '89506; NV; Reno; Washoe County'], ['95932', 'zip', '89503; NV; Reno; Washoe County'], ['95931', 'zip', '89502; NV; Reno; Washoe County'], ['95930', 'zip', '89501; NV; Reno; Washoe County'], ['95928', 'zip', '89460; NV; Gardnerville Ranchos; Douglas County; Gardnerville'], ['95926', 'zip', '89451; NV; Reno; Washoe County; Incline Village'], ['95924', 'zip', '89449; NV; Gardnerville Ranchos; Douglas County; Stateline'], ['95923', 'zip', '89448; NV; Gardnerville Ranchos; Douglas County; Zephyr Cove'], ['95922', 'zip', '89447; NV; Fernley; Lyon County; Yerington'], ['95920', 'zip', '89445; NV; Winnemucca; Humboldt County'], ['9592', 'city', 'Roanoke; IL; Peoria; Woodford County'], ['95919', 'zip', '89444; NV; Gardnerville Ranchos; Douglas County; Wellington'], ['95918', 'zip', '89442; NV; Reno; Washoe County; Wadsworth'], ['95917', 'zip', '89440; NV; Fernley; Lyon County; Dayton'], ['95916', 'zip', '89439; NV; Reno; Washoe County; Verdi'], ['95914', 'zip', '89436; NV; Reno; Washoe County; Sparks'], ['95912', 'zip', '89434; NV; Reno; Washoe County; Sparks'], ['95911', 'zip', '89433; NV; Reno; Washoe County; Sun Valley'], ['95909', 'zip', '89431; NV; Reno; Washoe County; Sparks'], ['95908', 'zip', '89430; NV; Fernley; Lyon County; Smith'], ['95907', 'zip', '89429; NV; Fernley; Lyon County; Silver Springs'], ['95906', 'zip', '89428; NV; Fernley; Lyon County; Silver City'], ['95904', 'zip', '89426; NV; Winnemucca; Humboldt County; Paradise Valley'], ['95903', 'zip', '89425; NV; Winnemucca; Humboldt County; Orovada'], ['95901', 'zip', '89423; NV; Gardnerville Ranchos; Douglas County; Minden'], ['95900', 'zip', '89422; NV; Mineral County; Mina'], ['9590', 'city', 'Hamburg; MN; Minneapolis-St. Paul-Bloomington; Carver County'], ['959', 'county', 'Cotton County; OK; Lawton'], ['95899', 'zip', '89421; NV; Winnemucca; Humboldt County; McDermitt'], ['95898', 'zip', '89420; NV; Mineral County; Luning'], ['95897', 'zip', '89419; NV; Pershing County; Lovelock'], ['95896', 'zip', '89418; NV; Pershing County; Imlay'], ['95895', 'zip', '89415; NV; Mineral County; Hawthorne'], ['95894', 'zip', '89414; NV; Winnemucca; Humboldt County; Golconda'], ['95893', 'zip', '89413; NV; Gardnerville Ranchos; Douglas County; Glenbrook'], ['95891', 'zip', '89411; NV; Gardnerville Ranchos; Douglas County; Genoa'], ['95890', 'zip', '89410; NV; Gardnerville Ranchos; Douglas County; Gardnerville'], ['95888', 'zip', '89408; NV; Fernley; Lyon County'], ['95886', 'zip', '89406; NV; Fallon; Churchill County'], ['95883', 'zip', '89403; NV; Fernley; Lyon County; Dayton'], ['95882', 'zip', '89402; NV; Reno; Washoe County; Incline Village'], ['95881', 'zip', '89319; NV; White Pine County; Ruth'], ['95880', 'zip', '89318; NV; White Pine County; McGill'], ['95879', 'zip', '89317; NV; White Pine County; Lund'], ['95878', 'zip', '89316; NV; Elko; Eureka County; Eureka'], ['95875', 'zip', '89311; NV; White Pine County; Ely'], ['95874', 'zip', '89310; NV; Lander County; Austin'], ['95873', 'zip', '89301; NV; White Pine County; Ely'], ['95866', 'zip', '89179'], ['95865', 'zip', '89178'], ['95861', 'zip', '89166'], ['95856', 'zip', '89161; NV; Las Vegas-Henderson-Paradise; Clark County; Las Vegas'], ['95852', 'zip', '89156'], ['95851', 'zip', '89155; NV; Las Vegas-Henderson-Paradise; Clark County; Las Vegas'], ['9585', 'city', 'Sparta; IL; Randolph County'], ['95845', 'zip', '89149'], ['95844', 'zip', '89148'], ['95843', 'zip', '89147'], ['95842', 'zip', '89146'], ['95841', 'zip', '89145'], ['95840', 'zip', '89144'], ['9584', 'city', 'Snow Camp; NC; Burlington; Alamance County'], ['95839', 'zip', '89143'], ['95838', 'zip', '89142'], ['95837', 'zip', '89141'], ['95835', 'zip', '89139'], ['95834', 'zip', '89138'], ['95831', 'zip', '89135'], ['95830', 'zip', '89134'], ['95827', 'zip', '89131'], ['95826', 'zip', '89130'], ['95825', 'zip', '89129'], ['95824', 'zip', '89128'], ['95820', 'zip', '89124; NV; Las Vegas-Henderson-Paradise; Clark County; Mount Charleston'], ['9582', 'city', 'Washington; MA; Pittsfield; Berkshire County'], ['95819', 'zip', '89123'], ['95818', 'zip', '89122'], ['95817', 'zip', '89121'], ['95816', 'zip', '89120'], ['95815', 'zip', '89119'], ['95814', 'zip', '89118'], ['95813', 'zip', '89117'], ['95811', 'zip', '89115'], ['9581', 'city', 'Scottville; IL; St. Louis; Macoupin County'], ['95809', 'zip', '89113'], ['95806', 'zip', '89110'], ['95805', 'zip', '89109'], ['95804', 'zip', '89108'], ['95803', 'zip', '89107'], ['95802', 'zip', '89106'], ['95800', 'zip', '89104'], ['95799', 'zip', '89103'], ['95798', 'zip', '89102'], ['95797', 'zip', '89101'], ['95795', 'zip', '89086; NV; Las Vegas-Henderson-Paradise; Clark County; North Las Vegas'], ['95794', 'zip', '89085; NV; Las Vegas-Henderson-Paradise; Clark County; North Las Vegas'], ['95793', 'zip', '89084'], ['95792', 'zip', '89081'], ['95790', 'zip', '89074'], ['95788', 'zip', '89061; NV; Pahrump; Nye County'], ['95787', 'zip', '89060; NV; Pahrump; Nye County'], ['95785', 'zip', '89052'], ['95784', 'zip', '89049; NV; Pahrump; Nye County; Tonopah'], ['95783', 'zip', '89048; NV; Pahrump; Nye County'], ['95781', 'zip', '89046; NV; Las Vegas-Henderson-Paradise; Clark County; Searchlight'], ['95780', 'zip', '89045; NV; Pahrump; Nye County; Round Mountain'], ['95779', 'zip', '89044'], ['95778', 'zip', '89043; NV; Lincoln County; Pioche'], ['95777', 'zip', '89042; NV; Lincoln County; Panaca'], ['95776', 'zip', '89041; NV; Pahrump; Nye County'], ['95775', 'zip', '89040; NV; Las Vegas-Henderson-Paradise; Clark County; Overton'], ['95774', 'zip', '89039; NV; Las Vegas-Henderson-Paradise; Clark County; Cal Nev Ari'], ['95771', 'zip', '89032'], ['95770', 'zip', '89031'], ['95769', 'zip', '89030'], ['95768', 'zip', '89029; NV; Las Vegas-Henderson-Paradise; Clark County; Laughlin'], ['95766', 'zip', '89027; NV; Las Vegas-Henderson-Paradise; Clark County; Mesquite'], ['95764', 'zip', '89025; NV; Las Vegas-Henderson-Paradise; Clark County; Moapa'], ['95760', 'zip', '89021; NV; Las Vegas-Henderson-Paradise; Clark County; Logandale'], ['95759', 'zip', '89020; NV; Pahrump; Nye County; Amargosa Valley'], ['95758', 'zip', '89019; NV; Las Vegas-Henderson-Paradise; Clark County; Jean'], ['95757', 'zip', '89018; NV; Las Vegas-Henderson-Paradise; Clark County; Indian Springs'], ['95756', 'zip', '89017; NV; Lincoln County; Alamo'], ['95754', 'zip', '89015'], ['95753', 'zip', '89014'], ['95751', 'zip', '89012'], ['95750', 'zip', '89011'], ['95747', 'zip', '89008; NV; Lincoln County; Caliente'], ['95746', 'zip', '89007; NV; Las Vegas-Henderson-Paradise; Clark County; Bunkerville'], ['95744', 'zip', '89005'], ['95743', 'zip', '89004; NV; Las Vegas-Henderson-Paradise; Clark County; Las Vegas'], ['95742', 'zip', '89003; NV; Pahrump; Nye County; Beatty'], ['95741', 'zip', '89001; NV; Lincoln County; Alamo'], ['9574', 'city', 'Richland; IN; Spencer County'], ['9571', 'city', 'Reedsburg; WI; Baraboo; Sauk County'], ['9566', 'city', 'Goodland; IN; Chicago-Naperville-Elgin; Newton County'], ['95659', 'zip', '88435; NM; Guadalupe County; Santa Rosa'], ['95653', 'zip', '88426; NM; Quay County; Logan'], ['95651', 'zip', '88422; NM; Union County; Grenville'], ['95645', 'zip', '88415; NM; Union County; Clayton'], ['95641', 'zip', '88401; NM; Quay County; Tucumcari'], ['95640', 'zip', '88355; NM; Ruidoso; Lincoln County'], ['9564', 'city', 'Glendon; PA; Allentown-Bethlehem-Easton; Northampton County'], ['95639', 'zip', '88354; NM; Alamogordo; Otero County; Weed'], ['95637', 'zip', '88352; NM; Alamogordo; Otero County; Tularosa'], ['95636', 'zip', '88351; NM; Ruidoso; Lincoln County; Tinnie'], ['95635', 'zip', '88350; NM; Alamogordo; Otero County; Timberon'], ['95633', 'zip', '88348; NM; Ruidoso; Lincoln County; San Patricio'], ['95632', 'zip', '88347; NM; Alamogordo; Otero County; Sacramento'], ['95631', 'zip', '88346; NM; Ruidoso; Lincoln County; Ruidoso Downs'], ['95630', 'zip', '88345; NM; Ruidoso; Lincoln County'], ['9563', 'city', 'Given; WV; Jackson County'], ['95629', 'zip', '88344; NM; Alamogordo; Otero County; Pinon'], ['95626', 'zip', '88341; NM; Ruidoso; Lincoln County; Nogal'], ['95624', 'zip', '88339; NM; Alamogordo; Otero County; Cloudcroft'], ['95623', 'zip', '88338; NM; Ruidoso; Lincoln County; Capitan'], ['95622', 'zip', '88337; NM; Alamogordo; Otero County; La Luz'], ['9562', 'city', 'Gibson; LA; Houma-Thibodaux; Terrebonne Parish'], ['95619', 'zip', '88325; NM; Alamogordo; Otero County'], ['95615', 'zip', '88318; NM; Ruidoso; Lincoln County; Corona'], ['95614', 'zip', '88317; NM; Alamogordo; Otero County; Timberon'], ['95613', 'zip', '88316; NM; Ruidoso; Lincoln County'], ['95612', 'zip', '88314; NM; Alamogordo; Otero County; Bent'], ['95611', 'zip', '88312; NM; Ruidoso; Lincoln County; Alto'], ['95610', 'zip', '88311; NM; Alamogordo; Otero County'], ['9561', 'city', 'Georgetown; ID; Bear Lake County'], ['95609', 'zip', '88310; NM; Alamogordo; Otero County'], ['95608', 'zip', '88301; NM; Ruidoso; Lincoln County; Carrizozo'], ['95606', 'zip', '88267; NM; Hobbs; Lea County; Lovington'], ['95601', 'zip', '88260; NM; Hobbs; Lea County; Lovington'], ['9560', 'city', 'Garwin; IA; Tama County'], ['956', 'county', 'Copiah County; MS; Jackson'], ['95597', 'zip', '88253; NM; Roswell; Chaves County; Lake Arthur'], ['95596', 'zip', '88252; NM; Hobbs; Lea County; Jal'], ['95595', 'zip', '88250; NM; Carlsbad-Artesia; Eddy County; Hope'], ['95593', 'zip', '88242; NM; Hobbs; Lea County'], ['95591', 'zip', '88240; NM; Hobbs; Lea County'], ['95590', 'zip', '88232; NM; Roswell; Chaves County; Hagerman'], ['9559', 'city', 'Galesville; MD; Baltimore-Columbia-Towson; Anne Arundel County'], ['95589', 'zip', '88231; NM; Hobbs; Lea County; Eunice'], ['95588', 'zip', '88230; NM; Roswell; Chaves County; Dexter'], ['95582', 'zip', '88203; NM; Roswell; Chaves County'], ['95580', 'zip', '88201; NM; Roswell; Chaves County'], ['95578', 'zip', '88135; NM; Clovis; Curry County; Texico'], ['95575', 'zip', '88132; NM; Portales; Roosevelt County; Pep'], ['95574', 'zip', '88130; NM; Portales; Roosevelt County'], ['95571', 'zip', '88124; NM; Clovis; Curry County; Melrose'], ['95567', 'zip', '88120; NM; Clovis; Curry County; Grady'], ['9556', 'city', 'White Bear Lake; MN; Minneapolis-St. Paul-Bloomington; Ramsey County'], ['95557', 'zip', '88101; NM; Clovis; Curry County'], ['95556', 'zip', '88081; NM; Las Cruces; Dona Ana County; Chaparral'], ['95555', 'zip', '88072; NM; Las Cruces; Dona Ana County; Vado'], ['95554', 'zip', '88065; NM; Silver City; Grant County'], ['95553', 'zip', '88063; NM; Las Cruces; Dona Ana County; Sunland Park'], ['95551', 'zip', '88061; NM; Silver City; Grant County'], ['95550', 'zip', '88058; NM; Las Cruces; Dona Ana County; La Mesa'], ['95548', 'zip', '88055; NM; Silver City; Grant County'], ['95547', 'zip', '88054; NM; Las Cruces; Dona Ana County'], ['95546', 'zip', '88053; NM; Silver City; Grant County'], ['95545', 'zip', '88052; NM; Las Cruces; Dona Ana County; Organ'], ['95544', 'zip', '88051; NM; Silver City; Grant County; Mule Creek'], ['95543', 'zip', '88049; NM; Silver City; Grant County; Hanover'], ['95542', 'zip', '88048; NM; Las Cruces; Dona Ana County; Mesquite'], ['95541', 'zip', '88047; NM; Las Cruces; Dona Ana County; Mesilla Park'], ['95540', 'zip', '88046; NM; Las Cruces; Dona Ana County'], ['9554', 'city', 'Milbridge; ME; Washington County'], ['95538', 'zip', '88044; NM; Las Cruces; Dona Ana County; La Mesa'], ['95537', 'zip', '88043; NM; Silver City; Grant County; Hurley'], ['95536', 'zip', '88042; NM; Sierra County; Hillsboro'], ['95535', 'zip', '88041; NM; Silver City; Grant County; Hanover'], ['95534', 'zip', '88040; NM; Silver City; Grant County; Hachita'], ['95532', 'zip', '88038; NM; Silver City; Grant County'], ['95530', 'zip', '88034; NM; Silver City; Grant County; Hanover'], ['9553', 'city', 'Mendota; CA; Fresno; Fresno County'], ['95529', 'zip', '88033; NM; Las Cruces; Dona Ana County'], ['95528', 'zip', '88032; NM; Las Cruces; Dona Ana County'], ['95526', 'zip', '88030; NM; Deming; Luna County'], ['95525', 'zip', '88029; NM; Deming; Luna County; Columbus'], ['95524', 'zip', '88028; NM; Silver City; Grant County; Buckhorn'], ['95523', 'zip', '88027; NM; Las Cruces; Dona Ana County; Anthony'], ['95522', 'zip', '88026; NM; Silver City; Grant County; Santa Clara'], ['95521', 'zip', '88025; NM; Silver City; Grant County; Buckhorn'], ['95520', 'zip', '88024; NM; Las Cruces; Dona Ana County; Anthony'], ['95519', 'zip', '88023; NM; Silver City; Grant County; Bayard'], ['95518', 'zip', '88022; NM; Silver City; Grant County; Arenas Valley'], ['95517', 'zip', '88021; NM; Las Cruces; Dona Ana County; Anthony'], ['95515', 'zip', '88012; NM; Las Cruces; Dona Ana County'], ['95514', 'zip', '88011; NM; Las Cruces; Dona Ana County'], ['95512', 'zip', '88008; NM; Las Cruces; Dona Ana County; Santa Teresa'], ['95511', 'zip', '88007; NM; Las Cruces; Dona Ana County'], ['95509', 'zip', '88005; NM; Las Cruces; Dona Ana County'], ['95505', 'zip', '88001; NM; Las Cruces; Dona Ana County'], ['95504', 'zip', '87943; NM; Sierra County; Winston'], ['95503', 'zip', '87942; NM; Sierra County; Williamsburg'], ['95502', 'zip', '87941; NM; Las Cruces; Dona Ana County; Garfield'], ['95501', 'zip', '87940; NM; Las Cruces; Dona Ana County; Rincon'], ['95500', 'zip', '87939; NM; Sierra County; Monticello'], ['9550', 'city', 'Westwood; KS; Kansas City; Johnson County'], ['955', 'county', 'Cooper County; MO'], ['95499', 'zip', '87937; NM; Las Cruces; Dona Ana County'], ['95498', 'zip', '87936; NM; Las Cruces; Dona Ana County; Garfield'], ['95497', 'zip', '87935; NM; Sierra County; Elephant Butte'], ['95496', 'zip', '87933; NM; Las Cruces; Dona Ana County; Garfield'], ['95495', 'zip', '87931; NM; Sierra County; Caballo'], ['95494', 'zip', '87930; NM; Sierra County; Arrey'], ['95493', 'zip', '87901; NM; Sierra County; Truth or Consequences'], ['95492', 'zip', '87832; NM; Socorro County; Socorro'], ['95491', 'zip', '87831; NM; Socorro County; Socorro'], ['9549', 'city', 'Westvale; NY; Syracuse; Onondaga County'], ['95488', 'zip', '87828; NM; Socorro County; Socorro'], ['95486', 'zip', '87825; NM; Socorro County; Socorro'], ['95484', 'zip', '87823; NM; Socorro County; Socorro'], ['95481', 'zip', '87801; NM; Socorro County; Socorro'], ['9548', 'city', 'Weston; OH; Toledo; Wood County'], ['95477', 'zip', '87749; NM; Colfax County; Cimarron'], ['95476', 'zip', '87747; NM; Colfax County; Springer'], ['95474', 'zip', '87745; NM; Las Vegas; San Miguel County; Sapello'], ['95472', 'zip', '87742; NM; Las Vegas; San Miguel County; Rociada'], ['95471', 'zip', '87740; NM; Colfax County; Raton'], ['9547', 'city', 'Weston; CO; Las Animas County'], ['95463', 'zip', '87729; NM; Colfax County; Miami'], ['95462', 'zip', '87728; NM; Colfax County; Maxwell'], ['9546', 'city', 'Westmoreland; TN; Nashville-Davidson--Murfreesboro--Franklin; Sumner County'], ['95458', 'zip', '87718; NM; Colfax County; Eagle Nest'], ['95456', 'zip', '87714; NM; Colfax County; Cimarron'], ['95452', 'zip', '87710; NM; Colfax County; Angel Fire'], ['95451', 'zip', '87701; NM; Las Vegas; San Miguel County'], ['9545', 'neigh', 'Westmere; NY; Albany-Schenectady-Troy; Albany County; Guilderland'], ['95447', 'zip', '87582; NM; Espaola; Rio Arriba County; Velarde'], ['95446', 'zip', '87581; NM; Espaola; Rio Arriba County; La Madera'], ['95445', 'zip', '87580; NM; Taos; Taos County; Tres Piedras'], ['95444', 'zip', '87579; NM; Taos; Taos County; Vadito'], ['95443', 'zip', '87578; NM; Espaola; Rio Arriba County; Truchas'], ['95442', 'zip', '87577; NM; Taos; Taos County; Penasco'], ['95440', 'zip', '87575; NM; Espaola; Rio Arriba County; Tierra Amarilla'], ['9544', 'city', 'Westbrook; MN; Cottonwood County'], ['95438', 'zip', '87573; NM; Las Vegas; San Miguel County; Pecos'], ['95437', 'zip', '87571; NM; Taos; Taos County'], ['95435', 'zip', '87567; NM; Santa Fe; Santa Fe County; Santa Cruz'], ['95434', 'zip', '87566; NM; Espaola; Rio Arriba County; Ohkay Owingeh'], ['95433', 'zip', '87565; NM; Las Vegas; San Miguel County; San Jose'], ['95432', 'zip', '87564; NM; Taos; Taos County; El Prado'], ['95431', 'zip', '87562; NM; Las Vegas; San Miguel County; Glorieta'], ['95430', 'zip', '87560; NM; Las Vegas; San Miguel County; Ribera'], ['95429', 'zip', '87558; NM; Taos; Taos County; Red River'], ['95428', 'zip', '87557; NM; Taos; Taos County; Ranchos de Taos'], ['95427', 'zip', '87556; NM; Taos; Taos County; Questa'], ['95426', 'zip', '87554; NM; Espaola; Rio Arriba County; Ojo Caliente'], ['95425', 'zip', '87553; NM; Taos; Taos County; Penasco'], ['95424', 'zip', '87552; NM; Las Vegas; San Miguel County; Glorieta'], ['95423', 'zip', '87551; NM; Espaola; Rio Arriba County; Los Ojos'], ['95422', 'zip', '87549; NM; Espaola; Rio Arriba County; Medanales'], ['95421', 'zip', '87548; NM; Espaola; Rio Arriba County; Medanales'], ['95419', 'zip', '87544; NM; Los Alamos; Los Alamos County'], ['95417', 'zip', '87540; NM; Santa Fe; Santa Fe County; Lamy'], ['95416', 'zip', '87539; NM; Espaola; Rio Arriba County; La Madera'], ['95415', 'zip', '87538; NM; Las Vegas; San Miguel County; San Jose'], ['95414', 'zip', '87537; NM; Espaola; Rio Arriba County; Hernandez'], ['95413', 'zip', '87535; NM; Las Vegas; San Miguel County; Glorieta'], ['95411', 'zip', '87532; NM; Espaola; Rio Arriba County; Espanola'], ['95410', 'zip', '87531; NM; Espaola; Rio Arriba County; Dixon'], ['95409', 'zip', '87530; NM; Espaola; Rio Arriba County; El Rito'], ['95408', 'zip', '87529; NM; Taos; Taos County; El Prado'], ['95407', 'zip', '87528; NM; Espaola; Rio Arriba County; Dulce'], ['95406', 'zip', '87527; NM; Espaola; Rio Arriba County; Dixon'], ['95405', 'zip', '87525; NM; Taos; Taos County; Arroyo Seco'], ['95403', 'zip', '87523; NM; Espaola; Rio Arriba County; Chimayo'], ['95402', 'zip', '87522; NM; Espaola; Rio Arriba County; Chimayo'], ['95401', 'zip', '87521; NM; Taos; Taos County; Chamisal'], ['95400', 'zip', '87520; NM; Espaola; Rio Arriba County; Chama'], ['9540', 'city', 'Spring Grove; IL; Chicago-Naperville-Elgin; McHenry County'], ['954', 'county', 'Conway County; AR'], ['95398', 'zip', '87518; NM; Espaola; Rio Arriba County; Cebolla'], ['95397', 'zip', '87517; NM; Taos; Taos County; Carson'], ['95396', 'zip', '87516; NM; Espaola; Rio Arriba County; Chama'], ['95395', 'zip', '87515; NM; Espaola; Rio Arriba County; Canjilon'], ['95394', 'zip', '87514; NM; Taos; Taos County; Arroyo Seco'], ['95393', 'zip', '87513; NM; Taos; Taos County; Arroyo Hondo'], ['95392', 'zip', '87512; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['95391', 'zip', '87511; NM; Espaola; Rio Arriba County; Alcalde'], ['95390', 'zip', '87510; NM; Espaola; Rio Arriba County; Abiquiu'], ['95388', 'zip', '87508; NM; Santa Fe; Santa Fe County'], ['95387', 'zip', '87507; NM; Santa Fe; Santa Fe County'], ['95386', 'zip', '87506; NM; Santa Fe; Santa Fe County'], ['95385', 'zip', '87505; NM; Santa Fe; Santa Fe County'], ['95381', 'zip', '87501; NM; Santa Fe; Santa Fe County'], ['9538', 'city', 'Spencer; WV; Roane County'], ['95377', 'zip', '87421; NM; Farmington; San Juan County; Waterflow'], ['95375', 'zip', '87419; NM; Farmington; San Juan County; Navajo Dam'], ['95374', 'zip', '87418; NM; Farmington; San Juan County; La Plata'], ['95373', 'zip', '87417; NM; Farmington; San Juan County; Kirtland'], ['95372', 'zip', '87416; NM; Farmington; San Juan County; Fruitland'], ['95371', 'zip', '87415; NM; Farmington; San Juan County; Flora Vista'], ['95370', 'zip', '87413; NM; Farmington; San Juan County; Bloomfield'], ['9537', 'city', 'Speedwell; VA; Wythe County'], ['95369', 'zip', '87412; NM; Farmington; San Juan County; Blanco'], ['95368', 'zip', '87410; NM; Farmington; San Juan County; Aztec'], ['95367', 'zip', '87402; NM; Farmington; San Juan County'], ['95366', 'zip', '87401; NM; Farmington; San Juan County'], ['95365', 'zip', '87375; NM; Gallup; McKinley County'], ['95362', 'zip', '87357; NM; Grants; Cibola County; Ramah'], ['95356', 'zip', '87323; NM; Grants; Cibola County; Prewitt'], ['95354', 'zip', '87321; NM; Grants; Cibola County; Ramah'], ['95351', 'zip', '87317; NM; Gallup; McKinley County'], ['9535', 'city', 'Salem; WV; Clarksburg; Harrison County'], ['95349', 'zip', '87315; NM; Grants; Cibola County; Fence Lake'], ['95342', 'zip', '87301; NM; Gallup; McKinley County'], ['9534', 'city', 'Salem; MO; Dent County'], ['9533', 'city', 'Salem; AR; Little Rock-North Little Rock-Conway; Saline County'], ['95321', 'zip', '87144'], ['95318', 'zip', '87124'], ['95317', 'zip', '87123'], ['95316', 'zip', '87122; NM; Albuquerque; Bernalillo County'], ['95315', 'zip', '87121'], ['95314', 'zip', '87120'], ['95311', 'zip', '87116; NM; Albuquerque; Bernalillo County; South Valley'], ['95309', 'zip', '87114; NM; Albuquerque; Bernalillo County'], ['95308', 'zip', '87113; NM; Albuquerque; Bernalillo County'], ['95307', 'zip', '87112'], ['95306', 'zip', '87111; NM; Albuquerque; Bernalillo County'], ['95305', 'zip', '87110'], ['95304', 'zip', '87109'], ['95303', 'zip', '87108; NM; Albuquerque; Bernalillo County'], ['95302', 'zip', '87107'], ['95301', 'zip', '87106'], ['95300', 'zip', '87105; NM; Albuquerque; Bernalillo County; South Valley'], ['95299', 'zip', '87104; NM; Albuquerque; Bernalillo County'], ['95297', 'zip', '87102; NM; Albuquerque; Bernalillo County'], ['95295', 'zip', '87083; NM; Albuquerque; Sandoval County; Cochiti Lake'], ['95292', 'zip', '87068; NM; Albuquerque; Valencia County; Bosque Farms'], ['95291', 'zip', '87064; NM; Espaola; Rio Arriba County; Youngsville'], ['95289', 'zip', '87062; NM; Socorro County; Socorro'], ['95286', 'zip', '87059; NM; Albuquerque; Bernalillo County; Tijeras'], ['95285', 'zip', '87056; NM; Santa Fe; Santa Fe County; Stanley'], ['95284', 'zip', '87053; NM; Albuquerque; Sandoval County; San Ysidro'], ['95282', 'zip', '87051; NM; Grants; Cibola County'], ['95280', 'zip', '87048; NM; Albuquerque; Sandoval County; Corrales'], ['95279', 'zip', '87047; NM; Albuquerque; Bernalillo County; Sandia Park'], ['95278', 'zip', '87046; NM; Albuquerque; Sandoval County; La Jara'], ['95277', 'zip', '87045; NM; Grants; Cibola County; Prewitt'], ['95276', 'zip', '87044; NM; Albuquerque; Sandoval County; Ponderosa'], ['95275', 'zip', '87043; NM; Albuquerque; Sandoval County; Placitas'], ['95274', 'zip', '87042; NM; Albuquerque; Valencia County; Peralta'], ['95273', 'zip', '87041; NM; Albuquerque; Sandoval County; Pena Blanca'], ['9527', 'city', 'West Unity; OH; Williams County'], ['95269', 'zip', '87036; NM; Albuquerque; Torrance County; Mountainair'], ['95268', 'zip', '87035; NM; Albuquerque; Torrance County; Moriarty'], ['95265', 'zip', '87031'], ['95264', 'zip', '87029; NM; Espaola; Rio Arriba County; Lindrith'], ['95262', 'zip', '87027; NM; Albuquerque; Sandoval County; La Jara'], ['95261', 'zip', '87026; NM; Albuquerque; Valencia County; Laguna'], ['95260', 'zip', '87025; NM; Albuquerque; Sandoval County; Jemez Springs'], ['95259', 'zip', '87024; NM; Albuquerque; Sandoval County; Jemez Pueblo'], ['95258', 'zip', '87023; NM; Albuquerque; Valencia County; Jarales'], ['95256', 'zip', '87021; NM; Grants; Cibola County'], ['95255', 'zip', '87020; NM; Grants; Cibola County'], ['95253', 'zip', '87017; NM; Espaola; Rio Arriba County; Gallina'], ['95252', 'zip', '87016; NM; Albuquerque; Torrance County; Estancia'], ['95251', 'zip', '87015; NM; Santa Fe; Santa Fe County; Edgewood'], ['9525', 'city', 'West Stockbridge; MA; Pittsfield; Berkshire County'], ['95249', 'zip', '87013; NM; Albuquerque; Sandoval County; Cuba'], ['95248', 'zip', '87012; NM; Espaola; Rio Arriba County; Coyote'], ['95246', 'zip', '87010; NM; Santa Fe; Santa Fe County; Cerrillos'], ['95244', 'zip', '87008; NM; Albuquerque; Bernalillo County; Cedar Crest'], ['95242', 'zip', '87006; NM; Socorro County; Socorro'], ['95241', 'zip', '87005; NM; Grants; Cibola County; Bluewater'], ['95240', 'zip', '87004; NM; Albuquerque; Sandoval County; Bernalillo'], ['95239', 'zip', '87002; NM; Albuquerque; Valencia County; Belen'], ['95238', 'zip', '87001; NM; Albuquerque; Sandoval County; Algodones'], ['95227', 'zip', '86512; AZ; Apache County; Sanders'], ['95226', 'zip', '86511; AZ; Apache County; Saint Michaels'], ['9522', 'city', 'West Point; NE; Cuming County'], ['95216', 'zip', '86445; AZ; Lake Havasu City-Kingman; Mohave County; Willow Beach'], ['95215', 'zip', '86444; AZ; Lake Havasu City-Kingman; Mohave County; Meadview'], ['95213', 'zip', '86442; AZ; Lake Havasu City-Kingman; Mohave County; Bullhead City'], ['95212', 'zip', '86441; AZ; Lake Havasu City-Kingman; Mohave County; Dolan Springs'], ['95211', 'zip', '86440; AZ; Lake Havasu City-Kingman; Mohave County; Mohave Valley'], ['95209', 'zip', '86438; AZ; Lake Havasu City-Kingman; Mohave County; Yucca'], ['95208', 'zip', '86437; AZ; Lake Havasu City-Kingman; Mohave County; Valentine'], ['95207', 'zip', '86436; AZ; Lake Havasu City-Kingman; Mohave County; Mohave Valley'], ['95204', 'zip', '86433; AZ; Lake Havasu City-Kingman; Mohave County; Peach Springs'], ['95203', 'zip', '86432; AZ; Lake Havasu City-Kingman; Mohave County; Littlefield'], ['95202', 'zip', '86431; AZ; Lake Havasu City-Kingman; Mohave County; Chloride'], ['95200', 'zip', '86429; AZ; Lake Havasu City-Kingman; Mohave County; Bullhead City'], ['952', 'county', 'Colusa County; CA'], ['95198', 'zip', '86426; AZ; Lake Havasu City-Kingman; Mohave County; Mohave Valley'], ['95197', 'zip', '86413; AZ; Lake Havasu City-Kingman; Mohave County; Golden Valley'], ['95195', 'zip', '86411; AZ; Lake Havasu City-Kingman; Mohave County; Kingman'], ['95194', 'zip', '86409; AZ; Lake Havasu City-Kingman; Mohave County; Kingman'], ['95193', 'zip', '86406; AZ; Lake Havasu City-Kingman; Mohave County; Lake Havasu City'], ['95191', 'zip', '86404; AZ; Lake Havasu City-Kingman; Mohave County; Lake Havasu City'], ['95190', 'zip', '86403; AZ; Lake Havasu City-Kingman; Mohave County; Lake Havasu City'], ['9519', 'city', 'West Liberty; OH; Bellefontaine; Logan County'], ['95188', 'zip', '86401; AZ; Lake Havasu City-Kingman; Mohave County; Kingman'], ['95187', 'zip', '86351; AZ; Prescott; Yavapai County; Sedona'], ['95186', 'zip', '86343; AZ; Prescott; Yavapai County; Crown King'], ['95181', 'zip', '86338; AZ; Prescott; Yavapai County; Skull Valley'], ['95180', 'zip', '86337; AZ; Prescott; Yavapai County; Seligman'], ['9518', 'city', 'West Leechburg; PA; Pittsburgh; Westmoreland County'], ['95179', 'zip', '86336; AZ; Prescott; Yavapai County; Sedona'], ['95178', 'zip', '86335; AZ; Prescott; Yavapai County; Rimrock'], ['95177', 'zip', '86334; AZ; Prescott; Yavapai County; Paulden'], ['95176', 'zip', '86333; AZ; Prescott; Yavapai County; Mayer'], ['95175', 'zip', '86332; AZ; Prescott; Yavapai County; Kirkland'], ['95174', 'zip', '86331; AZ; Prescott; Yavapai County; Jerome'], ['95172', 'zip', '86329; AZ; Prescott; Yavapai County; Dewey-Humboldt'], ['95171', 'zip', '86327; AZ; Prescott; Yavapai County; Dewey-Humboldt'], ['95170', 'zip', '86326; AZ; Prescott; Yavapai County; Cottonwood'], ['9517', 'city', 'Union City; OK; Oklahoma City; Canadian County'], ['95169', 'zip', '86325; AZ; Prescott; Yavapai County; Cornville'], ['95168', 'zip', '86324; AZ; Prescott; Yavapai County; Clarkdale'], ['95167', 'zip', '86323; AZ; Prescott; Yavapai County; Chino Valley'], ['95166', 'zip', '86322; AZ; Prescott; Yavapai County; Camp Verde'], ['95165', 'zip', '86321; AZ; Prescott; Yavapai County; Bagdad'], ['95164', 'zip', '86320; AZ; Prescott; Yavapai County; Ash Fork'], ['95163', 'zip', '86314; AZ; Prescott; Yavapai County; Prescott Valley'], ['95160', 'zip', '86305; AZ; Prescott; Yavapai County'], ['95158', 'zip', '86303; AZ; Prescott; Yavapai County'], ['95156', 'zip', '86301; AZ; Prescott; Yavapai County'], ['95152', 'zip', '86047; AZ; Show Low; Navajo County; Winslow'], ['95151', 'zip', '86046; AZ; Flagstaff; Coconino County; Williams'], ['9515', 'city', 'Tyro; VA; Charlottesville; Nelson County'], ['95146', 'zip', '86040; AZ; Flagstaff; Coconino County; Page'], ['95144', 'zip', '86038; AZ; Flagstaff; Coconino County; Mormon Lake'], ['95143', 'zip', '86036; AZ; Flagstaff; Coconino County; Page'], ['9514', 'city', 'Two Rivers; WI; Manitowoc; Manitowoc County'], ['95139', 'zip', '86032; AZ; Show Low; Navajo County; Joseph City'], ['95134', 'zip', '86025; AZ; Show Low; Navajo County; Holbrook'], ['95133', 'zip', '86024; AZ; Flagstaff; Coconino County; Happy Jack'], ['95132', 'zip', '86023; AZ; Flagstaff; Coconino County; Grand Canyon'], ['95131', 'zip', '86022; AZ; Flagstaff; Coconino County; Fredonia'], ['95130', 'zip', '86021; AZ; Lake Havasu City-Kingman; Mohave County; Colorado City'], ['95128', 'zip', '86018; AZ; Flagstaff; Coconino County; Parks'], ['95127', 'zip', '86017; AZ; Flagstaff; Coconino County; Munds Park'], ['95125', 'zip', '86015; AZ; Flagstaff; Coconino County'], ['95123', 'zip', '86004; AZ; Flagstaff; Coconino County'], ['95120', 'zip', '86001; AZ; Flagstaff; Coconino County'], ['95117', 'zip', '85940; AZ; Apache County; Vernon'], ['95116', 'zip', '85939; AZ; Show Low; Navajo County; Taylor'], ['95115', 'zip', '85938; AZ; Apache County; Springerville'], ['95114', 'zip', '85937; AZ; Show Low; Navajo County; Snowflake'], ['95113', 'zip', '85936; AZ; Apache County; Saint Johns'], ['95112', 'zip', '85935; AZ; Show Low; Navajo County; Pinetop'], ['95111', 'zip', '85934; AZ; Show Low; Navajo County; Pinedale'], ['95110', 'zip', '85933; AZ; Show Low; Navajo County; Overgaard'], ['9511', 'city', 'Southampton; MA; Springfield; Hampshire County'], ['95109', 'zip', '85932; AZ; Apache County; Nutrioso'], ['95108', 'zip', '85931; AZ; Flagstaff; Coconino County; Forest Lakes'], ['95106', 'zip', '85929; AZ; Show Low; Navajo County; Lakeside'], ['95105', 'zip', '85928; AZ; Show Low; Navajo County; Heber'], ['95104', 'zip', '85927; AZ; Apache County; Greer'], ['95102', 'zip', '85925; AZ; Apache County; Eagar'], ['95101', 'zip', '85924; AZ; Apache County; Concho'], ['95100', 'zip', '85923; AZ; Show Low; Navajo County'], ['951', 'county', 'Collin County; TX; Dallas-Fort Worth-Arlington'], ['95099', 'zip', '85922; AZ; Greenlee County; Blue'], ['95098', 'zip', '85920; AZ; Apache County; Alpine'], ['95097', 'zip', '85912; AZ; Show Low; Navajo County'], ['95094', 'zip', '85901; AZ; Show Low; Navajo County'], ['95091', 'zip', '85757; AZ; Tucson; Pima County'], ['95090', 'zip', '85755; AZ; Tucson; Pima County; Oro Valley'], ['95086', 'zip', '85750'], ['95085', 'zip', '85749; AZ; Tucson; Pima County'], ['95084', 'zip', '85748'], ['95083', 'zip', '85747'], ['95082', 'zip', '85746; AZ; Tucson; Pima County'], ['95081', 'zip', '85745'], ['95079', 'zip', '85743'], ['95078', 'zip', '85742'], ['95077', 'zip', '85741'], ['95075', 'zip', '85739'], ['95073', 'zip', '85737'], ['95072', 'zip', '85736; AZ; Tucson; Pima County; Three Points'], ['95071', 'zip', '85735; AZ; Tucson; Pima County'], ['9507', 'city', 'Saint Libory; IL; St. Louis; Saint Clair County'], ['95066', 'zip', '85730'], ['95057', 'zip', '85719'], ['95056', 'zip', '85718'], ['95054', 'zip', '85716'], ['95053', 'zip', '85715; AZ; Tucson; Pima County'], ['95052', 'zip', '85714; AZ; Tucson; Pima County'], ['95051', 'zip', '85713'], ['95050', 'zip', '85712'], ['9505', 'city', 'Saint James; MN; Watonwan County'], ['95049', 'zip', '85711'], ['95048', 'zip', '85710'], ['95044', 'zip', '85706'], ['95043', 'zip', '85705'], ['95042', 'zip', '85704'], ['9504', 'city', 'Saint Clair; MI; Detroit-Warren-Dearborn; Saint Clair County'], ['95039', 'zip', '85701; AZ; Tucson; Pima County'], ['95033', 'zip', '85653; AZ; Tucson; Pima County; Marana'], ['95031', 'zip', '85650; AZ; Sierra Vista-Douglas; Cochise County; Sierra Vista'], ['95030', 'zip', '85648; AZ; Nogales; Santa Cruz County; Rio Rico'], ['95029', 'zip', '85646; AZ; Nogales; Santa Cruz County; Tubac'], ['95028', 'zip', '85645; AZ; Tucson; Pima County; Amado'], ['95026', 'zip', '85643; AZ; Sierra Vista-Douglas; Cochise County; Willcox'], ['95025', 'zip', '85641; AZ; Tucson; Pima County; Vail'], ['95024', 'zip', '85640; AZ; Nogales; Santa Cruz County; Tubac'], ['95022', 'zip', '85638; AZ; Sierra Vista-Douglas; Cochise County; Tombstone'], ['95021', 'zip', '85637; AZ; Nogales; Santa Cruz County; Sonoita'], ['95019', 'zip', '85635; AZ; Sierra Vista-Douglas; Cochise County; Sierra Vista'], ['95016', 'zip', '85632; AZ; Sierra Vista-Douglas; Cochise County; San Simon'], ['95015', 'zip', '85631; AZ; Phoenix-Mesa-Scottsdale; Pinal County; San Manuel'], ['95014', 'zip', '85630; AZ; Sierra Vista-Douglas; Cochise County; Saint David'], ['95013', 'zip', '85629'], ['95011', 'zip', '85627; AZ; Sierra Vista-Douglas; Cochise County; Benson'], ['95010', 'zip', '85626; AZ; Sierra Vista-Douglas; Cochise County; Douglas'], ['9501', 'city', 'Saddle River; NJ; New York-Newark-Jersey City; Bergen County'], ['95009', 'zip', '85625; AZ; Sierra Vista-Douglas; Cochise County; Pearce'], ['95008', 'zip', '85624; AZ; Nogales; Santa Cruz County; Patagonia'], ['95007', 'zip', '85623; AZ; Phoenix-Mesa-Scottsdale; Pinal County; Oracle'], ['95006', 'zip', '85622; AZ; Tucson; Pima County; Green Valley'], ['95005', 'zip', '85621; AZ; Nogales; Santa Cruz County'], ['95004', 'zip', '85620; AZ; Sierra Vista-Douglas; Cochise County; Bisbee'], ['95003', 'zip', '85619; AZ; Sierra Vista-Douglas; Cochise County; Benson'], ['95002', 'zip', '85618; AZ; Phoenix-Mesa-Scottsdale; Pinal County; Mammoth'], ['95001', 'zip', '85617; AZ; Sierra Vista-Douglas; Cochise County; Mc Neal'], ['95000', 'zip', '85616; AZ; Sierra Vista-Douglas; Cochise County; Huachuca City'], ['9500', 'city', 'West Hamlin; WV; Huntington-Ashland; Lincoln County'], ['950', 'county', 'Colfax County; NM'], ['95', 'county', 'Bath County; KY; Mount Sterling'], ['94999', 'zip', '85615; AZ; Sierra Vista-Douglas; Cochise County; Hereford'], ['94998', 'zip', '85614; AZ; Tucson; Pima County; Green Valley'], ['94996', 'zip', '85611; AZ; Nogales; Santa Cruz County; Elgin'], ['94995', 'zip', '85610; AZ; Sierra Vista-Douglas; Cochise County; Elfrida'], ['94994', 'zip', '85609; AZ; Sierra Vista-Douglas; Cochise County; Dragoon'], ['94992', 'zip', '85607; AZ; Sierra Vista-Douglas; Cochise County; Douglas'], ['94991', 'zip', '85606; AZ; Sierra Vista-Douglas; Cochise County; Cochise'], ['94989', 'zip', '85603; AZ; Sierra Vista-Douglas; Cochise County; Bisbee'], ['94988', 'zip', '85602; AZ; Sierra Vista-Douglas; Cochise County; Benson'], ['94987', 'zip', '85601; AZ; Tucson; Pima County; Arivaca'], ['94986', 'zip', '85554; AZ; Payson; Gila County; Young'], ['94985', 'zip', '85553; AZ; Payson; Gila County; Tonto Basin'], ['94984', 'zip', '85552; AZ; Safford; Graham County; Thatcher'], ['94979', 'zip', '85546; AZ; Safford; Graham County'], ['94978', 'zip', '85545; AZ; Payson; Gila County; Roosevelt'], ['94977', 'zip', '85544; AZ; Payson; Gila County; Pine'], ['94976', 'zip', '85543; AZ; Safford; Graham County; Pima'], ['94974', 'zip', '85541; AZ; Payson; Gila County'], ['94973', 'zip', '85540; AZ; Greenlee County; Morenci'], ['94972', 'zip', '85539; AZ; Payson; Gila County; Miami'], ['94971', 'zip', '85536; AZ; Safford; Graham County; Fort Thomas'], ['94969', 'zip', '85534; AZ; Greenlee County; Duncan'], ['94968', 'zip', '85533; AZ; Greenlee County; Clifton'], ['94967', 'zip', '85532; AZ; Payson; Gila County; Miami'], ['94966', 'zip', '85531; AZ; Safford; Graham County; Thatcher'], ['94963', 'zip', '85501; AZ; Payson; Gila County; Globe'], ['94962', 'zip', '85396'], ['94961', 'zip', '85390; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Wickenburg'], ['94960', 'zip', '85388'], ['94959', 'zip', '85387; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Surprise'], ['94957', 'zip', '85383'], ['94956', 'zip', '85382'], ['94955', 'zip', '85381'], ['94953', 'zip', '85379'], ['94952', 'zip', '85378; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Surprise'], ['94951', 'zip', '85377; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Carefree'], ['94949', 'zip', '85375'], ['94948', 'zip', '85374'], ['94947', 'zip', '85373'], ['94943', 'zip', '85367; AZ; Yuma; Yuma County'], ['94941', 'zip', '85365; AZ; Yuma; Yuma County'], ['94940', 'zip', '85364; AZ; Yuma; Yuma County'], ['94939', 'zip', '85363; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Youngtown'], ['94938', 'zip', '85362; AZ; Prescott; Yavapai County; Yarnell'], ['94937', 'zip', '85361; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Surprise'], ['94936', 'zip', '85360; AZ; Lake Havasu City-Kingman; Mohave County; Wikieup'], ['94934', 'zip', '85358; AZ; La Paz County; Salome'], ['94933', 'zip', '85357; AZ; La Paz County; Wenden'], ['94932', 'zip', '85356; AZ; Yuma; Yuma County; Wellton'], ['94931', 'zip', '85355; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Waddell'], ['94930', 'zip', '85354; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Tonopah'], ['94929', 'zip', '85353'], ['94928', 'zip', '85352; AZ; Yuma; Yuma County; Wellton'], ['94927', 'zip', '85351'], ['94926', 'zip', '85350; AZ; Yuma; Yuma County; Somerton'], ['94925', 'zip', '85349; AZ; Yuma; Yuma County; San Luis'], ['94924', 'zip', '85348; AZ; La Paz County; Salome'], ['94923', 'zip', '85347; AZ; Yuma; Yuma County; Roll'], ['94922', 'zip', '85346; AZ; La Paz County; Quartzsite'], ['94921', 'zip', '85345'], ['94920', 'zip', '85344; AZ; La Paz County; Parker'], ['9492', 'city', 'South Haven; MN; St. Cloud; Stearns County'], ['94919', 'zip', '85343; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Buckeye'], ['94918', 'zip', '85342; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Peoria'], ['94916', 'zip', '85340'], ['94915', 'zip', '85339'], ['94914', 'zip', '85338'], ['94913', 'zip', '85337; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Gila Bend'], ['94912', 'zip', '85336; AZ; Yuma; Yuma County; Somerton'], ['94911', 'zip', '85335'], ['94910', 'zip', '85334; AZ; La Paz County; Ehrenberg'], ['9491', 'city', 'South Greenfield; MO; Dade County'], ['94909', 'zip', '85333; AZ; Yuma; Yuma County; Dateland'], ['94908', 'zip', '85332; AZ; Prescott; Yavapai County; Congress'], ['94907', 'zip', '85331'], ['94905', 'zip', '85328; AZ; La Paz County; Cibola'], ['94903', 'zip', '85326'], ['94902', 'zip', '85325; AZ; La Paz County; Bouse'], ['94901', 'zip', '85324; AZ; Prescott; Yavapai County; Black Canyon City'], ['94900', 'zip', '85323'], ['949', 'county', 'Colfax County; NE'], ['94899', 'zip', '85322; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Arlington'], ['94898', 'zip', '85321; AZ; Tucson; Pima County; Ajo'], ['94897', 'zip', '85320; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Aguila'], ['94892', 'zip', '85310'], ['94890', 'zip', '85308'], ['94889', 'zip', '85307'], ['94888', 'zip', '85306'], ['94887', 'zip', '85305'], ['94886', 'zip', '85304'], ['94885', 'zip', '85303; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Glendale'], ['94884', 'zip', '85302'], ['94883', 'zip', '85301'], ['94881', 'zip', '85297'], ['94880', 'zip', '85296'], ['94873', 'zip', '85284'], ['94872', 'zip', '85283'], ['94871', 'zip', '85282'], ['94870', 'zip', '85281'], ['94859', 'zip', '85268'], ['94857', 'zip', '85266; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Scottsdale'], ['94856', 'zip', '85264; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Fort McDowell'], ['94855', 'zip', '85263; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Rio Verde'], ['94854', 'zip', '85262; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Scottsdale'], ['94852', 'zip', '85260'], ['94851', 'zip', '85259'], ['94850', 'zip', '85258'], ['9485', 'city', 'Sontag; MS; Lawrence County'], ['94849', 'zip', '85257'], ['94847', 'zip', '85255'], ['94846', 'zip', '85254'], ['94845', 'zip', '85253'], ['94843', 'zip', '85251'], ['94842', 'zip', '85250'], ['94841', 'zip', '85249'], ['94840', 'zip', '85248'], ['94835', 'zip', '85143'], ['94830', 'zip', '85236; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Gilbert'], ['94828', 'zip', '85234'], ['94827', 'zip', '85233'], ['94821', 'zip', '85226'], ['94820', 'zip', '85225'], ['9482', 'city', 'Ryegate; MT; Billings; Golden Valley County'], ['94819', 'zip', '85224'], ['94810', 'zip', '85215'], ['9481', 'city', 'Rutherford; TN; Gibson County'], ['94808', 'zip', '85213'], ['94807', 'zip', '85212'], ['94805', 'zip', '85210'], ['94804', 'zip', '85209'], ['94803', 'zip', '85208'], ['94802', 'zip', '85207'], ['94801', 'zip', '85206'], ['94800', 'zip', '85205'], ['9480', 'city', 'Ruth; NV; White Pine County'], ['948', 'county', 'Coffey County; KS'], ['94799', 'zip', '85204'], ['94798', 'zip', '85203'], ['94797', 'zip', '85202'], ['94796', 'zip', '85201'], ['94793', 'zip', '85087; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; New River'], ['94792', 'zip', '85086'], ['94791', 'zip', '85085'], ['9478', 'city', 'Russell; MN; Marshall; Lyon County'], ['94767', 'zip', '85054'], ['94766', 'zip', '85053'], ['94765', 'zip', '85051'], ['94764', 'zip', '85050'], ['94763', 'zip', '85048'], ['94761', 'zip', '85045; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Phoenix'], ['94760', 'zip', '85044'], ['94759', 'zip', '85043'], ['94758', 'zip', '85042'], ['94757', 'zip', '85041'], ['94756', 'zip', '85040'], ['94753', 'zip', '85037'], ['94751', 'zip', '85035'], ['94750', 'zip', '85034; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Phoenix'], ['9475', 'city', 'Wendover; UT; Salt Lake City; Tooele County'], ['94749', 'zip', '85033'], ['94748', 'zip', '85032'], ['94747', 'zip', '85031; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Phoenix'], ['94745', 'zip', '85029'], ['94744', 'zip', '85028'], ['94743', 'zip', '85027'], ['94740', 'zip', '85024'], ['94739', 'zip', '85023'], ['94738', 'zip', '85022'], ['94737', 'zip', '85021'], ['94736', 'zip', '85020'], ['94735', 'zip', '85019'], ['94734', 'zip', '85018'], ['94733', 'zip', '85017'], ['94732', 'zip', '85016'], ['94731', 'zip', '85015'], ['94730', 'zip', '85014'], ['94729', 'zip', '85013'], ['94728', 'zip', '85012'], ['94725', 'zip', '85009'], ['94724', 'zip', '85008'], ['94723', 'zip', '85007'], ['94722', 'zip', '85006'], ['94720', 'zip', '85004; AZ; Phoenix-Mesa-Scottsdale; Maricopa County; Phoenix'], ['94719', 'zip', '85003'], ['94715', 'zip', '84790; UT; St. George; Washington County; Saint George'], ['94714', 'zip', '84784; UT; St. George; Washington County; Hildale'], ['94713', 'zip', '84783; UT; St. George; Washington County; Saint George'], ['94712', 'zip', '84782; UT; St. George; Washington County; Veyo'], ['94711', 'zip', '84781; UT; St. George; Washington County; Pine Valley'], ['94710', 'zip', '84780; UT; St. George; Washington County; Washington'], ['9471', 'city', 'Weems; VA; Lancaster County'], ['94709', 'zip', '84779; UT; St. George; Washington County; Virgin'], ['94706', 'zip', '84774; UT; St. George; Washington County; Toquerville'], ['94704', 'zip', '84772; UT; Cedar City; Iron County; Summit'], ['94702', 'zip', '84770; UT; St. George; Washington County; Saint George'], ['94701', 'zip', '84767; UT; St. George; Washington County; Virgin'], ['94700', 'zip', '84766; UT; Piute County; Sevier'], ['947', 'county', 'Coffee County; TN; Tullahoma-Manchester'], ['94699', 'zip', '84765; UT; St. George; Washington County; Santa Clara'], ['94697', 'zip', '84763; UT; St. George; Washington County; Springdale'], ['94696', 'zip', '84762; UT; Kane County; Duck Creek Village'], ['94695', 'zip', '84761; UT; Cedar City; Iron County; Parowan'], ['94694', 'zip', '84760; UT; Cedar City; Iron County; Parowan'], ['94692', 'zip', '84758; UT; Kane County; Orderville'], ['94691', 'zip', '84757; UT; St. George; Washington County; New Harmony'], ['94690', 'zip', '84756; UT; Cedar City; Iron County; Beryl'], ['9469', 'city', 'Trumbauersville; PA; Philadelphia-Camden-Wilmington; Bucks County'], ['94688', 'zip', '84754; UT; Sevier County; Monroe'], ['94681', 'zip', '84746; UT; St. George; Washington County; Leeds'], ['94680', 'zip', '84745; UT; St. George; Washington County; La Verkin'], ['9468', 'city', 'Troy; VT; Orleans County'], ['94679', 'zip', '84744; UT; Sevier County; Koosharem'], ['94677', 'zip', '84742; UT; Cedar City; Iron County; Kanarraville'], ['94676', 'zip', '84741; UT; Kane County; Kanab'], ['94674', 'zip', '84739; UT; Sevier County; Joseph'], ['94673', 'zip', '84738; UT; St. George; Washington County; Ivins'], ['94672', 'zip', '84737; UT; St. George; Washington County; Hurricane'], ['9467', 'city', 'Troy; KS; St. Joseph; Doniphan County'], ['94668', 'zip', '84733; UT; St. George; Washington County; Veyo'], ['94665', 'zip', '84730; UT; Sevier County; Glenwood'], ['94664', 'zip', '84729; UT; Kane County; Glendale'], ['94661', 'zip', '84725; UT; St. George; Washington County; Pine Valley'], ['94660', 'zip', '84724; UT; Sevier County; Elsinore'], ['94658', 'zip', '84722; UT; St. George; Washington County; Central'], ['94657', 'zip', '84721; UT; Cedar City; Iron County'], ['94656', 'zip', '84720; UT; Cedar City; Iron County'], ['94655', 'zip', '84719; UT; Cedar City; Iron County; Brian Head'], ['94650', 'zip', '84714; UT; Cedar City; Iron County; Beryl'], ['94649', 'zip', '84713; UT; Beaver County; Beaver'], ['94647', 'zip', '84711; UT; Sevier County; Annabella'], ['94646', 'zip', '84710; UT; Kane County; Duck Creek Village'], ['94645', 'zip', '84701; UT; Sevier County; Richfield'], ['94644', 'zip', '84667; UT; Sanpete County; Wales'], ['94643', 'zip', '84665; UT; Sanpete County; Fairview'], ['94642', 'zip', '84664; UT; Provo-Orem; Utah County; Mapleton'], ['94641', 'zip', '84663; UT; Provo-Orem; Utah County; Springville'], ['94640', 'zip', '84662; UT; Sanpete County; Spring City'], ['94639', 'zip', '84660; UT; Provo-Orem; Utah County; Spanish Fork'], ['94638', 'zip', '84657; UT; Sevier County; Sigurd'], ['94637', 'zip', '84656; UT; Millard County; Scipio'], ['94636', 'zip', '84655; UT; Provo-Orem; Utah County; Santaquin'], ['94635', 'zip', '84654; UT; Sevier County; Salina'], ['94634', 'zip', '84653; UT; Provo-Orem; Utah County; Salem'], ['94633', 'zip', '84652; UT; Sevier County; Redmond'], ['94632', 'zip', '84651; UT; Provo-Orem; Utah County; Payson'], ['94631', 'zip', '84649; UT; Millard County; Oak City'], ['94630', 'zip', '84648; UT; Provo-Orem; Juab County; Nephi'], ['94629', 'zip', '84647; UT; Sanpete County; Mount Pleasant'], ['94628', 'zip', '84646; UT; Sanpete County; Moroni'], ['94627', 'zip', '84645; UT; Provo-Orem; Juab County; Mona'], ['94626', 'zip', '84644; UT; Millard County; Meadow'], ['94625', 'zip', '84643; UT; Sanpete County; Mayfield'], ['94624', 'zip', '84642; UT; Sanpete County; Manti'], ['94622', 'zip', '84639; UT; Provo-Orem; Juab County; Levan'], ['94621', 'zip', '84638; UT; Millard County; Leamington'], ['94620', 'zip', '84637; UT; Millard County; Kanosh'], ['94619', 'zip', '84636; UT; Millard County; Holden'], ['94618', 'zip', '84635; UT; Millard County; Hinckley'], ['94617', 'zip', '84634; UT; Sanpete County; Fairview'], ['94616', 'zip', '84633; UT; Provo-Orem; Utah County; Goshen'], ['94615', 'zip', '84632; UT; Sanpete County; Fountain Green'], ['94614', 'zip', '84631; UT; Millard County; Fillmore'], ['94613', 'zip', '84630; UT; Sanpete County; Fayette'], ['94612', 'zip', '84629; UT; Sanpete County; Fairview'], ['94611', 'zip', '84628; UT; Provo-Orem; Juab County; Eureka'], ['94610', 'zip', '84627; UT; Sanpete County; Ephraim'], ['9461', 'city', 'Smithville; AR; Sharp County'], ['94609', 'zip', '84626; UT; Provo-Orem; Utah County; Goshen'], ['94608', 'zip', '84624; UT; Millard County; Delta'], ['94606', 'zip', '84622; UT; Sanpete County; Centerfield'], ['94604', 'zip', '84620; UT; Sevier County; Aurora'], ['94603', 'zip', '84606'], ['94601', 'zip', '84604; UT; Provo-Orem; Utah County; Provo'], ['9460', 'city', 'Smithfield; ME; Somerset County'], ['946', 'county', 'Coffee County; GA; Douglas'], ['94598', 'zip', '84601; UT; Provo-Orem; Utah County; Provo'], ['94597', 'zip', '84542; UT; Price; Carbon County; Wellington'], ['94595', 'zip', '84539; UT; Price; Carbon County; East Carbon'], ['94592', 'zip', '84535; UT; San Juan County; Monticello'], ['94589', 'zip', '84532; UT; Grand County; Moab'], ['94587', 'zip', '84530; UT; San Juan County; Monticello'], ['94583', 'zip', '84526; UT; Price; Carbon County; Helper'], ['9458', 'city', 'Smackover; AR; El Dorado; Union County'], ['94578', 'zip', '84520; UT; Price; Carbon County; East Carbon'], ['94573', 'zip', '84512; UT; San Juan County; Monticello'], ['94572', 'zip', '84511; UT; San Juan County; Blanding'], ['94570', 'zip', '84501; UT; Price; Carbon County'], ['94568', 'zip', '84414; UT; Ogden-Clearfield; Weber County; North Ogden'], ['94563', 'zip', '84405; UT; Ogden-Clearfield; Weber County; Washington Terrace'], ['94562', 'zip', '84404'], ['94561', 'zip', '84403'], ['94559', 'zip', '84401; UT; Ogden-Clearfield; Weber County; Ogden'], ['94558', 'zip', '84341; UT; Logan; Cache County; North Logan'], ['94557', 'zip', '84340; UT; Ogden-Clearfield; Box Elder County; Willard'], ['94556', 'zip', '84339; UT; Logan; Cache County; Wellsville'], ['94555', 'zip', '84338; UT; Logan; Cache County; Trenton'], ['94554', 'zip', '84337; UT; Ogden-Clearfield; Box Elder County; Tremonton'], ['94552', 'zip', '84335; UT; Logan; Cache County; Smithfield'], ['94551', 'zip', '84334; UT; Ogden-Clearfield; Box Elder County; Riverside'], ['94550', 'zip', '84333; UT; Logan; Cache County; Richmond'], ['9455', 'city', 'Rumson; NJ; New York-Newark-Jersey City; Monmouth County'], ['94549', 'zip', '84332; UT; Logan; Cache County; Providence'], ['94548', 'zip', '84331; UT; Ogden-Clearfield; Box Elder County; Portage'], ['94547', 'zip', '84330; UT; Ogden-Clearfield; Box Elder County; Plymouth'], ['94546', 'zip', '84329; UT; Ogden-Clearfield; Box Elder County; Park Valley'], ['94545', 'zip', '84328; UT; Logan; Cache County; Paradise'], ['94544', 'zip', '84327; UT; Logan; Cache County; Newton'], ['94543', 'zip', '84326; UT; Logan; Cache County; Millville'], ['94542', 'zip', '84325; UT; Logan; Cache County; Mendon'], ['94541', 'zip', '84324; UT; Ogden-Clearfield; Box Elder County; Mantua'], ['9454', 'city', 'Ruffin; SC; Colleton County'], ['94539', 'zip', '84322; UT; Logan; Cache County'], ['94538', 'zip', '84321; UT; Logan; Cache County'], ['94537', 'zip', '84320; UT; Logan; Cache County; Lewiston'], ['94536', 'zip', '84319; UT; Logan; Cache County; Hyrum'], ['94535', 'zip', '84318; UT; Logan; Cache County; Hyde Park'], ['94534', 'zip', '84317; UT; Ogden-Clearfield; Weber County; Huntsville'], ['94533', 'zip', '84316; UT; Ogden-Clearfield; Box Elder County; Howell'], ['94532', 'zip', '84315; UT; Ogden-Clearfield; Weber County; Hooper'], ['94531', 'zip', '84314; UT; Ogden-Clearfield; Box Elder County; Honeyville'], ['94529', 'zip', '84312; UT; Ogden-Clearfield; Box Elder County; Garland'], ['94528', 'zip', '84311; UT; Ogden-Clearfield; Box Elder County; Fielding'], ['94527', 'zip', '84310; UT; Ogden-Clearfield; Weber County; Eden'], ['94526', 'zip', '84309; UT; Ogden-Clearfield; Box Elder County; Deweyville'], ['94525', 'zip', '84308; UT; Logan; Cache County; Cornish'], ['94524', 'zip', '84307; UT; Ogden-Clearfield; Box Elder County; Corinne'], ['94523', 'zip', '84306; UT; Ogden-Clearfield; Box Elder County; Deweyville'], ['94522', 'zip', '84305; UT; Logan; Cache County; Clarkston'], ['94521', 'zip', '84304; UT; Logan; Cache County; Cache Junction'], ['94520', 'zip', '84302; UT; Ogden-Clearfield; Box Elder County; Brigham City'], ['94519', 'zip', '84301; UT; Ogden-Clearfield; Box Elder County; Bear River City'], ['9451', 'city', 'Roxana; IL; St. Louis; Madison County'], ['9450', 'city', 'Rowesville; SC; Orangeburg; Orangeburg County'], ['945', 'county', 'Coffee County; AL; Enterprise'], ['94488', 'zip', '84128; UT; Salt Lake City; Salt Lake County; West Valley City'], ['94484', 'zip', '84124; UT; Salt Lake City; Salt Lake County; Holladay'], ['94483', 'zip', '84123; UT; Salt Lake City; Salt Lake County; Taylorsville'], ['94481', 'zip', '84121'], ['94480', 'zip', '84120; UT; Salt Lake City; Salt Lake County; West Valley City'], ['9448', 'city', 'Watson; OK; McCurtain County'], ['94479', 'zip', '84119; UT; Salt Lake City; Salt Lake County; West Valley City'], ['94478', 'zip', '84118; UT; Salt Lake City; Salt Lake County; Kearns'], ['94477', 'zip', '84117; UT; Salt Lake City; Salt Lake County; Holladay'], ['94476', 'zip', '84116; UT; Salt Lake City; Salt Lake County'], ['94475', 'zip', '84115'], ['94471', 'zip', '84111; UT; Salt Lake City; Salt Lake County'], ['9447', 'city', 'Trenton; MI; Detroit-Warren-Dearborn; Wayne County'], ['94469', 'zip', '84109; UT; Salt Lake City; Salt Lake County; Millcreek'], ['94468', 'zip', '84108; UT; Salt Lake City; Salt Lake County'], ['94467', 'zip', '84107; UT; Salt Lake City; Salt Lake County; Murray'], ['94466', 'zip', '84106; UT; Salt Lake City; Salt Lake County'], ['94465', 'zip', '84105; UT; Salt Lake City; Salt Lake County'], ['94464', 'zip', '84104'], ['94463', 'zip', '84103; UT; Salt Lake City; Salt Lake County'], ['94462', 'zip', '84102; UT; Salt Lake City; Salt Lake County'], ['94461', 'zip', '84101; UT; Salt Lake City; Salt Lake County'], ['94460', 'zip', '84098; UT; Summit Park; Summit County; Park City'], ['94459', 'zip', '84097; UT; Provo-Orem; Utah County; Orem'], ['94458', 'zip', '84095; UT; Salt Lake City; Salt Lake County; South Jordan'], ['94457', 'zip', '84094; UT; Salt Lake City; Salt Lake County; Sandy'], ['94456', 'zip', '84093; UT; Salt Lake City; Salt Lake County; Sandy'], ['94455', 'zip', '84092; UT; Salt Lake City; Salt Lake County; Sandy'], ['94451', 'zip', '84088; UT; Salt Lake City; Salt Lake County; West Jordan'], ['94450', 'zip', '84087; UT; Ogden-Clearfield; Davis County; Woods Cross'], ['94449', 'zip', '84086; UT; Rich County; Woodruff'], ['94448', 'zip', '84085; UT; Vernal; Uintah County; Whiterocks'], ['94447', 'zip', '84084; UT; Salt Lake City; Salt Lake County; West Jordan'], ['94445', 'zip', '84082; UT; Heber; Wasatch County; Wallsburg'], ['94444', 'zip', '84080; UT; Salt Lake City; Tooele County; Vernon'], ['94442', 'zip', '84078; UT; Vernal; Uintah County'], ['94441', 'zip', '84076; UT; Vernal; Uintah County; Lapoint'], ['94440', 'zip', '84075'], ['9444', 'city', 'Tower Hill; IL; Shelby County'], ['94439', 'zip', '84074; UT; Salt Lake City; Tooele County; Tooele'], ['94438', 'zip', '84073; UT; Duchesne County; Talmage'], ['94437', 'zip', '84072; UT; Duchesne County; Tabiona'], ['94436', 'zip', '84071; UT; Salt Lake City; Tooele County; Rush Valley'], ['94435', 'zip', '84070; UT; Salt Lake City; Salt Lake County; Sandy'], ['94434', 'zip', '84069; UT; Salt Lake City; Tooele County; Rush Valley'], ['94432', 'zip', '84067; UT; Ogden-Clearfield; Weber County; Roy'], ['94431', 'zip', '84066; UT; Duchesne County; Roosevelt'], ['94430', 'zip', '84065; UT; Salt Lake City; Salt Lake County; Riverton'], ['9443', 'city', 'Tower City; ND; Barnes County'], ['94429', 'zip', '84064; UT; Rich County; Randolph'], ['94428', 'zip', '84063; UT; Vernal; Uintah County; Randlett'], ['94427', 'zip', '84062; UT; Provo-Orem; Utah County; Pleasant Grove'], ['94426', 'zip', '84061; UT; Summit Park; Summit County; Peoa'], ['94425', 'zip', '84060; UT; Summit Park; Summit County; Park City'], ['94424', 'zip', '84059; UT; Provo-Orem; Utah County; Vineyard'], ['94423', 'zip', '84058; UT; Provo-Orem; Utah County; Orem'], ['94422', 'zip', '84057; UT; Provo-Orem; Utah County; Orem'], ['94420', 'zip', '84055; UT; Summit Park; Summit County; Oakley'], ['94419', 'zip', '84054; UT; Ogden-Clearfield; Davis County; North Salt Lake'], ['94418', 'zip', '84053; UT; Duchesne County; Neola'], ['94417', 'zip', '84052; UT; Duchesne County; Myton'], ['94416', 'zip', '84051; UT; Duchesne County; Duchesne'], ['94415', 'zip', '84050; UT; Ogden-Clearfield; Morgan County; Morgan'], ['94414', 'zip', '84049; UT; Heber; Wasatch County; Midway'], ['94413', 'zip', '84047; UT; Salt Lake City; Salt Lake County; Midvale'], ['94412', 'zip', '84046; UT; Daggett County; Manila'], ['94411', 'zip', '84044; UT; Salt Lake City; Salt Lake County; Magna'], ['94410', 'zip', '84043'], ['9441', 'city', 'Towanda; IL; Bloomington; McLean County'], ['94409', 'zip', '84042; UT; Provo-Orem; Utah County; Lindon'], ['94408', 'zip', '84041'], ['94407', 'zip', '84040; UT; Ogden-Clearfield; Davis County; Layton'], ['94406', 'zip', '84039; UT; Vernal; Uintah County; Lapoint'], ['94405', 'zip', '84038; UT; Rich County; Garden City'], ['94404', 'zip', '84037; UT; Ogden-Clearfield; Davis County; Kaysville'], ['94403', 'zip', '84036; UT; Summit Park; Summit County; Kamas'], ['94402', 'zip', '84035; UT; Vernal; Uintah County; Jensen'], ['94400', 'zip', '84033; UT; Summit Park; Summit County; Henefer'], ['944', 'county', 'Clinch County; GA'], ['94399', 'zip', '84032; UT; Heber; Wasatch County; Heber City'], ['94398', 'zip', '84031; UT; Duchesne County; Hanna'], ['94397', 'zip', '84029; UT; Salt Lake City; Tooele County; Grantsville'], ['94396', 'zip', '84028; UT; Rich County; Garden City'], ['94395', 'zip', '84027; UT; Duchesne County; Duchesne'], ['94394', 'zip', '84026; UT; Vernal; Uintah County; Fort Duchesne'], ['94393', 'zip', '84025; UT; Ogden-Clearfield; Davis County; Farmington'], ['94391', 'zip', '84023; UT; Daggett County; Dutch John'], ['94390', 'zip', '84022; UT; Salt Lake City; Tooele County; Dugway'], ['94389', 'zip', '84021; UT; Duchesne County; Duchesne'], ['94388', 'zip', '84020; UT; Salt Lake City; Salt Lake County; Draper'], ['94387', 'zip', '84018; UT; Ogden-Clearfield; Morgan County; Croydon'], ['94386', 'zip', '84017; UT; Summit Park; Summit County; Coalville'], ['94384', 'zip', '84015'], ['94383', 'zip', '84014; UT; Ogden-Clearfield; Davis County; Centerville'], ['94382', 'zip', '84013; UT; Provo-Orem; Utah County; Cedar Fort'], ['94380', 'zip', '84010; UT; Ogden-Clearfield; Davis County; Bountiful'], ['94378', 'zip', '84007; UT; Duchesne County; Bluebell'], ['94377', 'zip', '84006; UT; Salt Lake City; Salt Lake County; Bingham Canyon'], ['94376', 'zip', '84004; UT; Provo-Orem; Utah County; Alpine'], ['94375', 'zip', '84003; UT; Provo-Orem; Utah County; American Fork'], ['94374', 'zip', '84002; UT; Duchesne County; Altonah'], ['94373', 'zip', '84001; UT; Duchesne County; Altamont'], ['94370', 'zip', "83876; ID; Coeur d'Alene; Kootenai County; Worley"], ['94369', 'zip', '83874; ID; Shoshone County; Wallace'], ['94368', 'zip', '83873; ID; Shoshone County; Wallace'], ['94367', 'zip', '83872; ID; Moscow; Latah County; Viola'], ['94366', 'zip', '83871; ID; Moscow; Latah County; Troy'], ['94365', 'zip', '83870; ID; Benewah County; Tensed'], ['94364', 'zip', "83869; ID; Coeur d'Alene; Kootenai County; Spirit Lake"], ['94363', 'zip', '83868; ID; Shoshone County; Smelterville'], ['94362', 'zip', '83867; ID; Shoshone County; Wallace'], ['94361', 'zip', '83866; ID; Benewah County; Fernwood'], ['9436', 'city', 'Sinking Spring; PA; Reading; Berks County'], ['94359', 'zip', '83864; ID; Sandpoint; Bonner County'], ['94358', 'zip', '83861; ID; Benewah County; Saint Maries'], ['94357', 'zip', '83860; ID; Sandpoint; Bonner County; Sagle'], ['94356', 'zip', "83858; ID; Coeur d'Alene; Kootenai County; Rathdrum"], ['94355', 'zip', '83857; ID; Moscow; Latah County; Princeton'], ['94354', 'zip', '83856; ID; Sandpoint; Bonner County; Priest River'], ['94353', 'zip', '83855; ID; Moscow; Latah County; Potlatch'], ['94352', 'zip', "83854; ID; Coeur d'Alene; Kootenai County; Post Falls"], ['94350', 'zip', '83852; ID; Sandpoint; Bonner County; Ponderay'], ['9435', 'city', 'Sinclairville; NY; Jamestown-Dunkirk-Fredonia; Chautauqua County'], ['94349', 'zip', '83851; ID; Benewah County; Plummer'], ['94348', 'zip', '83850; ID; Shoshone County; Pinehurst'], ['94347', 'zip', '83849; ID; Shoshone County; Osburn'], ['94346', 'zip', '83848; ID; Sandpoint; Bonner County; Nordman'], ['94345', 'zip', '83847; ID; Boundary County; Bonners Ferry'], ['94344', 'zip', '83846; ID; Shoshone County; Wallace'], ['94343', 'zip', '83845; ID; Boundary County; Moyie Springs'], ['94341', 'zip', '83843; ID; Moscow; Latah County'], ['94340', 'zip', "83842; ID; Coeur d'Alene; Kootenai County; Medimont"], ['94339', 'zip', '83841; ID; Sandpoint; Bonner County; Laclede'], ['94338', 'zip', '83840; ID; Sandpoint; Bonner County; Kootenai'], ['94337', 'zip', '83839; ID; Shoshone County; Kingston'], ['94336', 'zip', '83837; ID; Shoshone County; Kellogg'], ['94335', 'zip', '83836; ID; Sandpoint; Bonner County; Hope'], ['94334', 'zip', "83835; ID; Coeur d'Alene; Kootenai County; Hayden"], ['94333', 'zip', '83834; ID; Moscow; Latah County; Harvard'], ['94332', 'zip', "83833; ID; Coeur d'Alene; Kootenai County; Harrison"], ['94331', 'zip', '83832; ID; Moscow; Latah County; Genesee'], ['94330', 'zip', '83830; ID; Benewah County; Fernwood'], ['94329', 'zip', '83827; ID; Clearwater County; Elk River'], ['94327', 'zip', '83825; ID; Sandpoint; Bonner County; Dover'], ['94326', 'zip', '83824; ID; Benewah County; De Smet'], ['94325', 'zip', '83823; ID; Moscow; Latah County; Deary'], ['94324', 'zip', '83822; ID; Sandpoint; Bonner County; Oldtown'], ['94323', 'zip', '83821; ID; Sandpoint; Bonner County; Coolin'], ['94321', 'zip', "83815; ID; Coeur d'Alene; Kootenai County"], ['94320', 'zip', "83814; ID; Coeur d'Alene; Kootenai County"], ['9432', 'city', 'Rossville; KS; Topeka; Shawnee County'], ['94319', 'zip', '83813; ID; Sandpoint; Bonner County; Cocolalla'], ['94317', 'zip', '83811; ID; Sandpoint; Bonner County; Clark Fork'], ['94316', 'zip', "83810; ID; Coeur d'Alene; Kootenai County; Cataldo"], ['94315', 'zip', "83809; ID; Coeur d'Alene; Kootenai County; Athol"], ['94314', 'zip', '83808; ID; Shoshone County; Calder'], ['94313', 'zip', '83806; ID; Moscow; Latah County; Bovill'], ['94312', 'zip', '83805; ID; Boundary County; Bonners Ferry'], ['94311', 'zip', '83804; ID; Sandpoint; Bonner County; Blanchard'], ['94310', 'zip', "83803; ID; Coeur d'Alene; Kootenai County; Bayview"], ['9431', 'city', 'Roseland; NE; Hastings; Adams County'], ['94309', 'zip', '83802; ID; Shoshone County; Avery'], ['94308', 'zip', "83801; ID; Coeur d'Alene; Kootenai County; Athol"], ['943', 'county', 'Clarke County; VA; Washington-Arlington-Alexandria'], ['94288', 'zip', '83716'], ['94286', 'zip', '83714'], ['94285', 'zip', '83713; ID; Boise City; Ada County; Boise'], ['94284', 'zip', '83712; ID; Boise City; Ada County; Boise'], ['94282', 'zip', '83709'], ['9428', 'city', 'Ronda; NC; North Wilkesboro; Wilkes County'], ['94279', 'zip', '83706'], ['94278', 'zip', '83705'], ['94277', 'zip', '83704'], ['94276', 'zip', '83703'], ['94275', 'zip', '83702'], ['94273', 'zip', '83687'], ['94272', 'zip', '83686'], ['94270', 'zip', '83677; ID; Valley County; Yellow Pine'], ['9427', 'city', 'Romney; WV; Winchester; Hampshire County'], ['94269', 'zip', '83676; ID; Boise City; Canyon County; Wilder'], ['94268', 'zip', '83672; ID; Washington County; Weiser'], ['94267', 'zip', '83671; ID; Idaho County; Warren'], ['94266', 'zip', '83670; ID; Boise City; Gem County; Sweet'], ['94265', 'zip', '83669; ID; Boise City; Ada County; Star'], ['94264', 'zip', '83666; ID; Boise City; Boise County; Placerville'], ['94263', 'zip', '83661; ID; Ontario; Payette County; Payette'], ['94262', 'zip', '83660; ID; Boise City; Canyon County; Parma'], ['94261', 'zip', '83657; ID; Boise City; Gem County; Ola'], ['94260', 'zip', '83656; ID; Boise City; Canyon County; Caldwell'], ['94259', 'zip', '83655; ID; Ontario; Payette County; New Plymouth'], ['94258', 'zip', '83654; ID; Adams County; New Meadows'], ['94255', 'zip', '83651'], ['94254', 'zip', '83650; ID; Boise City; Owyhee County; Murphy'], ['94252', 'zip', '83647; ID; Mountain Home; Elmore County'], ['94251', 'zip', '83645; ID; Washington County; Midvale'], ['94250', 'zip', '83644; ID; Boise City; Canyon County; Middleton'], ['94249', 'zip', '83643; ID; Adams County; Mesa'], ['94248', 'zip', '83642'], ['94247', 'zip', '83641; ID; Boise City; Canyon County; Melba'], ['94246', 'zip', '83639; ID; Boise City; Owyhee County; Marsing'], ['94245', 'zip', '83638; ID; Valley County; McCall'], ['94244', 'zip', '83637; ID; Boise City; Boise County; Lowman'], ['94243', 'zip', '83636; ID; Boise City; Gem County; Letha'], ['94241', 'zip', '83634'], ['94240', 'zip', '83633; ID; Mountain Home; Elmore County; King Hill'], ['9424', 'city', 'Washington; TX; Brenham; Washington County'], ['94239', 'zip', '83632; ID; Adams County; Indian Valley'], ['94238', 'zip', '83631; ID; Boise City; Boise County; Idaho City'], ['94236', 'zip', '83629; ID; Boise City; Boise County; Horseshoe Bend'], ['94235', 'zip', '83628; ID; Boise City; Owyhee County; Homedale'], ['94234', 'zip', '83627; ID; Mountain Home; Elmore County; Hammett'], ['94233', 'zip', '83626; ID; Boise City; Canyon County; Greenleaf'], ['94232', 'zip', '83624; ID; Boise City; Owyhee County; Grand View'], ['94231', 'zip', '83623; ID; Mountain Home; Elmore County; Glenns Ferry'], ['94230', 'zip', '83622; ID; Boise City; Boise County; Idaho City'], ['9423', 'city', 'Toomsboro; GA; Wilkinson County'], ['94229', 'zip', '83619; ID; Ontario; Payette County; Fruitland'], ['94228', 'zip', '83617; ID; Boise City; Gem County; Emmett'], ['94227', 'zip', '83616; ID; Boise City; Ada County; Eagle'], ['94226', 'zip', '83615; ID; Valley County; Donnelly'], ['94225', 'zip', '83612; ID; Adams County; Council'], ['94224', 'zip', '83611; ID; Valley County; Cascade'], ['94223', 'zip', '83610; ID; Washington County; Cambridge'], ['94222', 'zip', '83607; ID; Boise City; Canyon County; Caldwell'], ['94220', 'zip', '83605; ID; Boise City; Canyon County; Caldwell'], ['9422', 'city', 'Tonka Bay; MN; Minneapolis-St. Paul-Bloomington; Hennepin County'], ['94219', 'zip', '83604; ID; Boise City; Owyhee County; Bruneau'], ['94218', 'zip', '83602; ID; Boise City; Boise County; Horseshoe Bend'], ['94216', 'zip', '83555; ID; Lewis County; Winchester'], ['94215', 'zip', '83554; ID; Idaho County; White Bird'], ['94214', 'zip', '83553; ID; Clearwater County; Weippe'], ['94213', 'zip', '83552; ID; Idaho County; Kooskia'], ['94212', 'zip', '83549; ID; Idaho County; Riggins'], ['94211', 'zip', '83548; ID; Lewis County; Reubens'], ['94210', 'zip', '83547; ID; Idaho County; Pollock'], ['94209', 'zip', '83546; ID; Clearwater County; Pierce'], ['94208', 'zip', '83545; ID; Lewiston; Nez Perce County; Peck'], ['94207', 'zip', '83544; ID; Clearwater County; Orofino'], ['94206', 'zip', '83543; ID; Lewis County; Nezperce'], ['94205', 'zip', '83542; ID; Idaho County; Lucile'], ['94204', 'zip', '83541; ID; Clearwater County; Lenore'], ['94203', 'zip', '83540; ID; Lewiston; Nez Perce County; Lapwai'], ['94202', 'zip', '83539; ID; Idaho County; Kooskia'], ['94201', 'zip', '83537; ID; Moscow; Latah County; Kendrick'], ['94200', 'zip', '83536; ID; Idaho County; Kamiah'], ['94199', 'zip', '83535; ID; Moscow; Latah County; Juliaetta'], ['94198', 'zip', '83533; ID; Lewis County; Nezperce'], ['94196', 'zip', '83530; ID; Idaho County; Grangeville'], ['94195', 'zip', '83526; ID; Idaho County; Ferdinand'], ['94194', 'zip', '83525; ID; Idaho County; Elk City'], ['94193', 'zip', '83524; ID; Lewiston; Nez Perce County; Culdesac'], ['94192', 'zip', '83523; ID; Lewis County; Craigmont'], ['94191', 'zip', '83522; ID; Idaho County; Cottonwood'], ['94190', 'zip', '83520; ID; Clearwater County; Lenore'], ['9419', 'city', 'Toboyne Township; PA; Harrisburg-Carlisle; Perry County'], ['94189', 'zip', '83501; ID; Lewiston; Nez Perce County'], ['94188', 'zip', '83469; ID; Lemhi County; Shoup'], ['94187', 'zip', '83468; ID; Lemhi County; Tendoy'], ['94186', 'zip', '83467; ID; Lemhi County; Salmon'], ['94185', 'zip', '83466; ID; Lemhi County; North Fork'], ['94184', 'zip', '83465; ID; Lemhi County; Leadore'], ['94183', 'zip', '83464; ID; Lemhi County; Leadore'], ['94182', 'zip', '83463; ID; Lemhi County; Gibbonsville'], ['94181', 'zip', '83462; ID; Lemhi County; Carmen'], ['94179', 'zip', '83455; ID; Jackson; Teton County; Victor'], ['94178', 'zip', '83454; ID; Idaho Falls; Bonneville County; Ucon'], ['94177', 'zip', '83452; ID; Jackson; Teton County; Tetonia'], ['94176', 'zip', '83451; ID; Rexburg; Madison County; Sugar City'], ['94175', 'zip', '83450; ID; Idaho Falls; Jefferson County; Mud Lake'], ['94174', 'zip', '83449; ID; Idaho Falls; Bonneville County; Swan Valley'], ['94173', 'zip', '83448; ID; Rexburg; Madison County; Sugar City'], ['94170', 'zip', '83445; ID; Rexburg; Fremont County; Saint Anthony'], ['9417', 'city', 'Tintah; MN; Traverse County'], ['94169', 'zip', '83444; ID; Idaho Falls; Jefferson County; Roberts'], ['94168', 'zip', '83443; ID; Idaho Falls; Jefferson County; Rigby'], ['94167', 'zip', '83442; ID; Idaho Falls; Jefferson County; Rigby'], ['94165', 'zip', '83440; ID; Rexburg; Madison County'], ['94164', 'zip', '83438; ID; Rexburg; Fremont County; Parker'], ['94163', 'zip', '83436; ID; Jackson; Teton County; Newdale'], ['94162', 'zip', '83435; ID; Idaho Falls; Jefferson County; Monteview'], ['94161', 'zip', '83434; ID; Idaho Falls; Jefferson County; Menan'], ['94160', 'zip', '83433; ID; Rexburg; Fremont County; Island Park'], ['94159', 'zip', '83431; ID; Idaho Falls; Jefferson County; Lewisville'], ['94158', 'zip', '83429; ID; Rexburg; Fremont County; Island Park'], ['94157', 'zip', '83428; ID; Idaho Falls; Bonneville County; Irwin'], ['94156', 'zip', '83427; ID; Idaho Falls; Bonneville County; Iona'], ['94155', 'zip', '83425; ID; Idaho Falls; Jefferson County; Hamer'], ['94154', 'zip', '83424; ID; Jackson; Teton County; Felt'], ['94152', 'zip', '83422; ID; Jackson; Teton County; Driggs'], ['94151', 'zip', '83421; ID; Rexburg; Madison County; Sugar City'], ['94150', 'zip', '83420; ID; Rexburg; Fremont County; Ashton'], ['9415', 'city', 'Sibley; ND; Barnes County'], ['94148', 'zip', '83414; WY; Jackson; Teton County; Moose'], ['94147', 'zip', '83406; ID; Idaho Falls; Bonneville County; Ammon'], ['94145', 'zip', '83404; ID; Idaho Falls; Bonneville County'], ['94143', 'zip', '83402; ID; Idaho Falls; Bonneville County'], ['94142', 'zip', '83401; ID; Idaho Falls; Bonneville County'], ['94141', 'zip', '83355; ID; Gooding County; Wendell'], ['9414', 'city', 'Sibley; IA; Osceola County'], ['94139', 'zip', '83353; ID; Hailey; Blaine County; Sun Valley'], ['94138', 'zip', '83352; ID; Hailey; Lincoln County; Shoshone'], ['94137', 'zip', '83350; ID; Burley; Minidoka County; Rupert'], ['94136', 'zip', '83349; ID; Hailey; Lincoln County; Richfield'], ['94135', 'zip', '83348; ID; Hailey; Blaine County; Bellevue'], ['94134', 'zip', '83347; ID; Burley; Minidoka County; Paul'], ['94133', 'zip', '83346; ID; Burley; Cassia County; Oakley'], ['94132', 'zip', '83344; ID; Twin Falls; Twin Falls County; Murtaugh'], ['94131', 'zip', '83343; ID; Burley; Minidoka County; Minidoka'], ['94130', 'zip', '83342; ID; Burley; Cassia County; Malta'], ['94129', 'zip', '83341; ID; Twin Falls; Twin Falls County; Kimberly'], ['94128', 'zip', '83340; ID; Hailey; Blaine County; Ketchum'], ['94127', 'zip', '83338; ID; Twin Falls; Jerome County; Jerome'], ['94125', 'zip', '83336; ID; Burley; Minidoka County; Heyburn'], ['94124', 'zip', '83335; ID; Twin Falls; Jerome County; Hazelton'], ['94123', 'zip', '83334; ID; Twin Falls; Twin Falls County; Hansen'], ['94122', 'zip', '83333; ID; Hailey; Blaine County'], ['94121', 'zip', '83332; ID; Gooding County; Hagerman'], ['94120', 'zip', '83330; ID; Gooding County; Gooding'], ['9412', 'city', 'Ward; AR; Little Rock-North Little Rock-Conway; Lonoke County'], ['94119', 'zip', '83328; ID; Twin Falls; Twin Falls County; Filer'], ['94118', 'zip', '83327; ID; Hailey; Camas County; Fairfield'], ['94117', 'zip', '83325; ID; Twin Falls; Jerome County; Eden'], ['94116', 'zip', '83324; ID; Hailey; Lincoln County; Dietrich'], ['94115', 'zip', '83323; ID; Burley; Cassia County; Declo'], ['94113', 'zip', '83321; ID; Twin Falls; Twin Falls County; Castleford'], ['94112', 'zip', '83320; ID; Hailey; Blaine County; Carey'], ['94111', 'zip', '83318; ID; Burley; Cassia County'], ['94110', 'zip', '83316; ID; Twin Falls; Twin Falls County; Buhl'], ['9411', 'city', 'Wapella; IL; Bloomington; Dewitt County'], ['94109', 'zip', '83314; ID; Gooding County; Bliss'], ['94108', 'zip', '83313; ID; Hailey; Blaine County; Bellevue'], ['94107', 'zip', '83312; ID; Burley; Cassia County; Almo'], ['94106', 'zip', '83311; ID; Burley; Cassia County; Albion'], ['94104', 'zip', '83302; ID; Boise City; Owyhee County; Rogerson'], ['94103', 'zip', '83301; ID; Twin Falls; Twin Falls County'], ['94102', 'zip', '83287; ID; Bear Lake County; Fish Haven'], ['94101', 'zip', '83286; ID; Logan; Franklin County; Weston'], ['94100', 'zip', '83285; ID; Caribou County; Wayan'], ['941', 'county', 'Clarke County; IA'], ['94099', 'zip', '83283; ID; Logan; Franklin County; Thatcher'], ['94098', 'zip', '83281; ID; Pocatello; Bannock County; Downey'], ['94097', 'zip', '83278; ID; Custer County; Stanley'], ['94096', 'zip', '83277; ID; Blackfoot; Bingham County; Pingree'], ['94095', 'zip', '83276; ID; Caribou County; Soda Springs'], ['94094', 'zip', '83274; ID; Blackfoot; Bingham County; Shelley'], ['94093', 'zip', '83272; ID; Bear Lake County; Saint Charles'], ['94092', 'zip', '83271; ID; Power County; Rockland'], ['94091', 'zip', '83263; ID; Logan; Franklin County; Preston'], ['94090', 'zip', '83262; ID; Blackfoot; Bingham County; Pingree'], ['9409', 'city', 'Tilly; AR; Russellville; Pope County'], ['94089', 'zip', '83261; ID; Bear Lake County; Paris'], ['94087', 'zip', '83255; ID; Idaho Falls; Butte County; Howe'], ['94086', 'zip', '83254; ID; Bear Lake County; Montpelier'], ['94085', 'zip', '83253; ID; Lemhi County; May'], ['94084', 'zip', '83252; ID; Oneida County; Malad City'], ['94083', 'zip', '83251; ID; Custer County; Mackay'], ['94082', 'zip', '83250; ID; Pocatello; Bannock County; McCammon'], ['94081', 'zip', '83246; ID; Pocatello; Bannock County; Lava Hot Springs'], ['94080', 'zip', '83245; ID; Pocatello; Bannock County; Inkom'], ['94078', 'zip', '83243; ID; Oneida County; Holbrook'], ['94077', 'zip', '83241; ID; Caribou County; Grace'], ['94076', 'zip', '83239; ID; Bear Lake County; Georgetown'], ['94075', 'zip', '83238; ID; Bear Lake County; Geneva'], ['94074', 'zip', '83237; ID; Logan; Franklin County; Franklin'], ['94073', 'zip', '83236; ID; Blackfoot; Bingham County; Firth'], ['94072', 'zip', '83235; ID; Lemhi County; May'], ['94071', 'zip', '83234; ID; Pocatello; Bannock County; Downey'], ['94069', 'zip', '83232; ID; Logan; Franklin County; Dayton'], ['94066', 'zip', '83228; ID; Logan; Franklin County; Clifton'], ['94065', 'zip', '83227; ID; Custer County; Clayton'], ['94064', 'zip', '83226; ID; Custer County; Challis'], ['94062', 'zip', '83221; ID; Blackfoot; Bingham County'], ['94061', 'zip', '83220; ID; Bear Lake County; Bern'], ['94060', 'zip', '83218; ID; Blackfoot; Bingham County; Basalt'], ['9406', 'city', 'Tibbie; AL; Washington County'], ['94059', 'zip', '83217; ID; Caribou County; Bancroft'], ['94058', 'zip', '83215; ID; Blackfoot; Bingham County; Atomic City'], ['94057', 'zip', '83214; ID; Pocatello; Bannock County; Arimo'], ['94055', 'zip', '83212; ID; Power County; Arbon'], ['94054', 'zip', '83211; ID; Power County; American Falls'], ['94053', 'zip', '83210; ID; Blackfoot; Bingham County; Aberdeen'], ['94049', 'zip', '83204; ID; Pocatello; Bannock County'], ['94048', 'zip', '83203; ID; Pocatello; Bannock County; Fort Hall'], ['94047', 'zip', '83202; ID; Pocatello; Bannock County; Chubbuck'], ['94046', 'zip', '83201; ID; Pocatello; Bannock County'], ['94038', 'zip', '83120; WY; Lincoln County; Freedom'], ['94034', 'zip', '83115; WY; Sublette County; Daniel'], ['94032', 'zip', '83113; WY; Sublette County; Marbleton'], ['9403', 'city', 'Thousand Palms; CA; Riverside-San Bernardino-Ontario; Riverside County'], ['94027', 'zip', '83025; WY; Jackson; Teton County; Teton Village'], ['94026', 'zip', '83014; WY; Jackson; Teton County; Wilson'], ['94025', 'zip', '83013; WY; Jackson; Teton County; Moran'], ['94024', 'zip', '83012; WY; Jackson; Teton County'], ['94023', 'zip', '83011; WY; Jackson; Teton County'], ['94021', 'zip', '83001; WY; Jackson; Teton County'], ['94020', 'zip', '82945; WY; Rock Springs; Sweetwater County; Superior'], ['94019', 'zip', '82944; WY; Evanston; Uinta County; Fort Bridger'], ['94018', 'zip', '82943; WY; Rock Springs; Sweetwater County'], ['94016', 'zip', '82941; WY; Sublette County; Pinedale'], ['94015', 'zip', '82939; WY; Evanston; Uinta County; Mountain View'], ['94014', 'zip', '82938; WY; Rock Springs; Sweetwater County; McKinnon'], ['94013', 'zip', '82937; WY; Evanston; Uinta County; Lyman'], ['94012', 'zip', '82936; WY; Evanston; Uinta County; Lonetree'], ['94011', 'zip', '82935; WY; Rock Springs; Sweetwater County; Green River'], ['94010', 'zip', '82934; WY; Rock Springs; Sweetwater County; Granger'], ['9401', 'city', 'Thoreau; NM; Grants; Cibola County'], ['94009', 'zip', '82933; WY; Evanston; Uinta County; Fort Bridger'], ['94008', 'zip', '82932; WY; Rock Springs; Sweetwater County; Farson'], ['94006', 'zip', '82930; WY; Evanston; Uinta County'], ['94004', 'zip', '82925; WY; Sublette County; Cora'], ['94003', 'zip', '82923; WY; Sublette County; Pinedale'], ['94002', 'zip', '82922; WY; Sublette County; Bondurant'], ['94000', 'zip', '82901; WY; Rock Springs; Sweetwater County'], ['940', 'county', 'Clarke County; GA; Athens-Clarke County'], ['94', 'county', 'Baca County; CO'], ['93999', 'zip', '82845; WY; Sheridan; Sheridan County'], ['93997', 'zip', '82842; WY; Sheridan; Sheridan County; Banner'], ['93995', 'zip', '82839; WY; Sheridan; Sheridan County; Ranchester'], ['93994', 'zip', '82838; WY; Sheridan; Sheridan County; Parkman'], ['93993', 'zip', '82837; WY; Sheridan; Sheridan County; Clearmont'], ['93992', 'zip', '82836; WY; Sheridan; Sheridan County; Dayton'], ['93991', 'zip', '82835; WY; Sheridan; Sheridan County; Clearmont'], ['93990', 'zip', '82834; WY; Johnson County; Buffalo'], ['9399', 'city', 'Shiloh; NJ; Vineland-Bridgeton; Cumberland County'], ['93989', 'zip', '82833; WY; Sheridan; Sheridan County'], ['93988', 'zip', '82832; WY; Sheridan; Sheridan County; Banner'], ['93987', 'zip', '82831; WY; Sheridan; Sheridan County; Arvada'], ['93986', 'zip', '82801; WY; Sheridan; Sheridan County'], ['93985', 'zip', '82732; WY; Gillette; Campbell County; Wright'], ['93984', 'zip', '82731; WY; Gillette; Campbell County; Weston'], ['93983', 'zip', '82730; WY; Weston County; Upton'], ['93982', 'zip', '82729; WY; Crook County; Sundance'], ['93981', 'zip', '82727; WY; Gillette; Campbell County; Rozet'], ['93980', 'zip', '82725; WY; Gillette; Campbell County; Recluse'], ['9398', 'city', 'Sheridan; IN; Indianapolis-Carmel-Anderson; Hamilton County'], ['93979', 'zip', '82723; WY; Weston County; Osage'], ['93978', 'zip', '82721; WY; Crook County; Moorcroft'], ['93977', 'zip', '82720; WY; Crook County; Hulett'], ['93976', 'zip', '82718; WY; Gillette; Campbell County'], ['93974', 'zip', '82716; WY; Gillette; Campbell County'], ['93972', 'zip', '82714; WY; Crook County; Devils Tower'], ['93970', 'zip', '82712; WY; Crook County; Aladdin'], ['93969', 'zip', '82711; WY; Crook County; Alva'], ['93968', 'zip', '82710; WY; Crook County; Aladdin'], ['93967', 'zip', '82701; WY; Weston County; Newcastle'], ['93966', 'zip', '82649; WY; Riverton; Fremont County; Shoshoni'], ['93963', 'zip', '82644; WY; Casper; Natrona County'], ['93961', 'zip', '82642; WY; Riverton; Fremont County; Lysite'], ['93960', 'zip', '82640; WY; Johnson County; Kaycee'], ['9396', 'city', 'Shell Lake; WI; Washburn County'], ['93959', 'zip', '82639; WY; Johnson County; Kaycee'], ['93958', 'zip', '82638; WY; Casper; Natrona County'], ['93957', 'zip', '82637; WY; Converse County; Glenrock'], ['93956', 'zip', '82636; WY; Casper; Natrona County; Evansville'], ['93954', 'zip', '82633; WY; Converse County; Douglas'], ['93952', 'zip', '82620; WY; Casper; Natrona County; Alcova'], ['93950', 'zip', '82609; WY; Casper; Natrona County'], ['93948', 'zip', '82604; WY; Casper; Natrona County'], ['93946', 'zip', '82601; WY; Casper; Natrona County'], ['93944', 'zip', '82523; WY; Riverton; Fremont County; Pavillion'], ['93943', 'zip', '82520; WY; Riverton; Fremont County; Lander'], ['93942', 'zip', '82516; WY; Riverton; Fremont County; Kinnear'], ['93941', 'zip', '82515; WY; Riverton; Fremont County; Hudson'], ['93940', 'zip', '82514; WY; Riverton; Fremont County; Fort Washakie'], ['93939', 'zip', '82513; WY; Riverton; Fremont County; Dubois'], ['93938', 'zip', '82512; WY; Riverton; Fremont County; Crowheart'], ['93937', 'zip', '82510; WY; Riverton; Fremont County; Arapahoe'], ['93936', 'zip', '82501; WY; Riverton; Fremont County'], ['93934', 'zip', '82443; WY; Hot Springs County; Thermopolis'], ['93933', 'zip', '82442; WY; Washakie County; Ten Sleep'], ['93932', 'zip', '82441; WY; Big Horn County; Shell'], ['93930', 'zip', '82435; WY; Park County; Powell'], ['93929', 'zip', '82434; WY; Big Horn County; Otto'], ['93928', 'zip', '82433; WY; Park County; Meeteetse'], ['93927', 'zip', '82432; WY; Big Horn County; Manderson'], ['93926', 'zip', '82431; WY; Big Horn County; Lovell'], ['93925', 'zip', '82430; WY; Hot Springs County; Kirby'], ['93924', 'zip', '82428; WY; Big Horn County; Hyattville'], ['93923', 'zip', '82426; WY; Big Horn County; Greybull'], ['93922', 'zip', '82423; WY; Big Horn County; Deaver'], ['93921', 'zip', '82422; WY; Big Horn County; Greybull'], ['93920', 'zip', '82421; WY; Big Horn County; Deaver'], ['9392', 'city', 'Yucca; AZ; Lake Havasu City-Kingman; Mohave County'], ['93919', 'zip', '82420; WY; Big Horn County; Cowley'], ['93918', 'zip', '82414; WY; Park County; Cody'], ['93917', 'zip', '82412; WY; Big Horn County; Byron'], ['93916', 'zip', '82411; WY; Big Horn County; Burlington'], ['93915', 'zip', '82410; WY; Big Horn County; Basin'], ['93914', 'zip', '82401; WY; Washakie County; Worland'], ['93913', 'zip', '82336; WY; Rock Springs; Sweetwater County; Wamsutter'], ['93911', 'zip', '82334; WY; Carbon County; Sinclair'], ['93910', 'zip', '82332; WY; Carbon County; Savery'], ['9391', 'city', 'Yorktown; IA; Page County'], ['93909', 'zip', '82331; WY; Carbon County; Saratoga'], ['93908', 'zip', '82329; WY; Laramie; Albany County; Medicine Bow'], ['93907', 'zip', '82327; WY; Carbon County; Hanna'], ['93906', 'zip', '82325; WY; Carbon County; Grand Encampment'], ['93905', 'zip', '82324; WY; Carbon County; Elk Mountain'], ['93904', 'zip', '82323; WY; Carbon County; Dixon'], ['93903', 'zip', '82322; WY; Rock Springs; Sweetwater County; Bairoil'], ['93902', 'zip', '82321; WY; Carbon County; Baggs'], ['93901', 'zip', '82310; WY; Riverton; Fremont County; Lander'], ['93900', 'zip', '82301; WY; Carbon County; Rawlins'], ['939', 'county', 'Clarke County; AL'], ['93899', 'zip', '82244; WY; Goshen County; Yoder'], ['93898', 'zip', '82243; WY; Goshen County; Veteran'], ['93897', 'zip', '82242; WY; Niobrara County; Van Tassell'], ['93896', 'zip', '82240; WY; Goshen County; Torrington'], ['93895', 'zip', '82229; WY; Converse County; Shawnee'], ['93892', 'zip', '82224; WY; Niobrara County; Manville'], ['93891', 'zip', '82223; WY; Goshen County; Lingle'], ['9389', 'city', 'Yanceyville; NC; Caswell County'], ['93889', 'zip', '82221; WY; Goshen County; La Grange'], ['93888', 'zip', '82219; WY; Goshen County; Jay Em'], ['93886', 'zip', '82217; WY; Goshen County; Hawk Springs'], ['93885', 'zip', '82215; WY; Platte County; Hartville'], ['93884', 'zip', '82214; WY; Platte County; Guernsey'], ['93883', 'zip', '82213; WY; Platte County; Glendo'], ['93882', 'zip', '82212; WY; Goshen County; Fort Laramie'], ['93881', 'zip', '82210; WY; Platte County; Chugwater'], ['93880', 'zip', '82201; WY; Platte County; Wheatland'], ['93878', 'zip', '82084; WY; Laramie; Albany County; Tie Siding'], ['93877', 'zip', '82083; WY; Laramie; Albany County; Rock River'], ['93876', 'zip', '82082; WY; Cheyenne; Laramie County; Pine Bluffs'], ['93875', 'zip', '82081; WY; Cheyenne; Laramie County; Meriden'], ['93873', 'zip', '82072; WY; Laramie; Albany County'], ['93871', 'zip', '82070; WY; Laramie; Albany County'], ['93870', 'zip', '82063; WY; Laramie; Albany County; Jelm'], ['93868', 'zip', '82060; WY; Cheyenne; Laramie County'], ['93867', 'zip', '82059; WY; Cheyenne; Laramie County; Granite Canon'], ['93866', 'zip', '82058; WY; Laramie; Albany County; Garrett'], ['93865', 'zip', '82055; WY; Laramie; Albany County; Centennial'], ['93864', 'zip', '82054; WY; Cheyenne; Laramie County; Carpenter'], ['93863', 'zip', '82053; WY; Cheyenne; Laramie County; Burns'], ['93862', 'zip', '82052; WY; Laramie; Albany County; Buford'], ['93861', 'zip', '82051; WY; Laramie; Albany County; Bosler'], ['93860', 'zip', '82050; WY; Cheyenne; Laramie County; Albin'], ['93858', 'zip', '82009; WY; Cheyenne; Laramie County'], ['93856', 'zip', '82007; WY; Cheyenne; Laramie County'], ['93851', 'zip', '82001; WY; Cheyenne; Laramie County'], ['93849', 'zip', '81657; CO; Edwards; Eagle County; Vail'], ['93848', 'zip', '81656; CO; Glenwood Springs; Pitkin County; Aspen'], ['93847', 'zip', '81655; CO; Edwards; Eagle County; Wolcott'], ['93846', 'zip', '81654; CO; Glenwood Springs; Pitkin County; Aspen'], ['93845', 'zip', '81653; CO; Steamboat Springs; Routt County; Slater'], ['93844', 'zip', '81652; CO; Glenwood Springs; Garfield County; Silt'], ['93843', 'zip', '81650; CO; Glenwood Springs; Garfield County; Rifle'], ['93842', 'zip', '81649; CO; Edwards; Eagle County; Red Cliff'], ['93841', 'zip', '81648; CO; Rio Blanco County; Rangely'], ['93840', 'zip', '81647; CO; Glenwood Springs; Garfield County; New Castle'], ['9384', 'city', 'Walthourville; GA; Hinesville; Liberty County'], ['93839', 'zip', '81646; CO; Grand Junction; Mesa County; Mesa'], ['93838', 'zip', '81645; CO; Edwards; Eagle County; Minturn'], ['93837', 'zip', '81643; CO; Grand Junction; Mesa County; Mesa'], ['93836', 'zip', '81642; CO; Glenwood Springs; Pitkin County; Meredith'], ['93835', 'zip', '81641; CO; Rio Blanco County; Meeker'], ['93834', 'zip', '81640; CO; Craig; Moffat County; Maybell'], ['93833', 'zip', '81639; CO; Steamboat Springs; Routt County; Hayden'], ['93832', 'zip', '81638; CO; Craig; Moffat County; Hamilton'], ['93831', 'zip', '81637; CO; Edwards; Eagle County; Gypsum'], ['9383', 'city', 'Walnut Grove; MS; Leake County'], ['93829', 'zip', '81635; CO; Glenwood Springs; Garfield County; Parachute'], ['93827', 'zip', '81632; CO; Edwards; Eagle County'], ['93826', 'zip', '81631; CO; Edwards; Eagle County; Eagle'], ['93825', 'zip', '81630; CO; Grand Junction; Mesa County; De Beque'], ['93823', 'zip', '81625; CO; Craig; Moffat County'], ['93822', 'zip', '81624; CO; Grand Junction; Mesa County; Collbran'], ['93821', 'zip', '81623; CO; Glenwood Springs; Garfield County; Carbondale'], ['93820', 'zip', '81621; CO; Edwards; Eagle County; Basalt'], ['93819', 'zip', '81620; CO; Edwards; Eagle County; Avon'], ['93818', 'zip', '81615; CO; Glenwood Springs; Pitkin County; Snowmass Village'], ['93816', 'zip', '81611; CO; Glenwood Springs; Pitkin County; Aspen'], ['93815', 'zip', '81610; CO; Craig; Moffat County; Dinosaur'], ['93813', 'zip', '81601; CO; Glenwood Springs; Garfield County'], ['93812', 'zip', '81527; CO; Grand Junction; Mesa County; Whitewater'], ['93811', 'zip', '81526; CO; Grand Junction; Mesa County; Palisade'], ['93810', 'zip', '81525; CO; Grand Junction; Mesa County; Mack'], ['9381', 'city', 'Walnut Creek; NC; Goldsboro; Wayne County'], ['93809', 'zip', '81524; CO; Grand Junction; Mesa County; Loma'], ['93808', 'zip', '81523; CO; Grand Junction; Mesa County'], ['93807', 'zip', '81522; CO; Grand Junction; Mesa County; Whitewater'], ['93806', 'zip', '81521; CO; Grand Junction; Mesa County; Fruita'], ['93805', 'zip', '81520; CO; Grand Junction; Mesa County; Clifton'], ['93804', 'zip', '81506; CO; Grand Junction; Mesa County'], ['93803', 'zip', '81505; CO; Grand Junction; Mesa County'], ['93802', 'zip', '81504; CO; Grand Junction; Mesa County'], ['93801', 'zip', '81503; CO; Grand Junction; Mesa County'], ['9380', 'city', 'Wallagrass; ME; Aroostook County'], ['938', 'county', 'Citrus County; FL; Homosassa Springs'], ['93799', 'zip', '81501; CO; Grand Junction; Mesa County'], ['93798', 'zip', '81435; CO; San Miguel County; Mountain Village'], ['93797', 'zip', '81434; CO; Gunnison County; Somerset'], ['93796', 'zip', '81433; CO; San Juan County; Silverton'], ['93795', 'zip', '81432; CO; Ouray County; Ridgway'], ['93794', 'zip', '81431; CO; Montrose; Montrose County; Redvale'], ['93793', 'zip', '81430; CO; San Miguel County; Placerville'], ['93792', 'zip', '81429; CO; Montrose; Montrose County; Bedrock'], ['93791', 'zip', '81428; CO; Delta County; Paonia'], ['93790', 'zip', '81427; CO; Ouray County; Ouray'], ['9379', 'city', 'Walcott; IA; Davenport-Moline-Rock Island; Scott County'], ['93789', 'zip', '81426; CO; San Miguel County; Ophir'], ['93788', 'zip', '81425; CO; Montrose; Montrose County; Olathe'], ['93787', 'zip', '81424; CO; Montrose; Montrose County; Nucla'], ['93786', 'zip', '81423; CO; San Miguel County; Norwood'], ['93785', 'zip', '81422; CO; Montrose; Montrose County; Naturita'], ['93784', 'zip', '81420; CO; Delta County; Hotchkiss'], ['93783', 'zip', '81419; CO; Delta County; Hotchkiss'], ['93782', 'zip', '81418; CO; Delta County; Orchard City'], ['93781', 'zip', '81416; CO; Delta County; Delta'], ['93780', 'zip', '81415; CO; Delta County; Crawford'], ['93779', 'zip', '81414; CO; Delta County; Orchard City'], ['93778', 'zip', '81413; CO; Delta County; Cedaredge'], ['93777', 'zip', '81411; CO; Montrose; Montrose County; Bedrock'], ['93776', 'zip', '81410; CO; Delta County; Orchard City'], ['93774', 'zip', '81401; CO; Montrose; Montrose County'], ['93773', 'zip', '81335; CO; Montezuma County; Pleasant View'], ['93771', 'zip', '81332; CO; Dolores County; Rico'], ['93770', 'zip', '81331; CO; Montezuma County; Pleasant View'], ['93768', 'zip', '81329; CO; Durango; La Plata County; Hesperus'], ['93767', 'zip', '81328; CO; Montezuma County; Mancos'], ['93766', 'zip', '81327; CO; Montezuma County; Lewis'], ['93765', 'zip', '81326; CO; Durango; La Plata County; Hesperus'], ['93764', 'zip', '81325; CO; San Miguel County; Egnar'], ['93763', 'zip', '81324; CO; Dolores County; Dove Creek'], ['93762', 'zip', '81323; CO; Montezuma County; Dolores'], ['93761', 'zip', '81321; CO; Montezuma County; Cortez'], ['93760', 'zip', '81320; CO; Dolores County; Cahone'], ['9376', 'city', 'Tehama; CA; Red Bluff; Tehama County'], ['93759', 'zip', '81303; CO; Durango; La Plata County'], ['93757', 'zip', '81301; CO; Durango; La Plata County'], ['93755', 'zip', '81253; CO; Custer County; Wetmore'], ['93754', 'zip', '81252; CO; Custer County; Westcliffe'], ['93753', 'zip', '81251; CO; Lake County; Twin Lakes'], ['93752', 'zip', '81248; CO; Saguache County; Saguache'], ['93749', 'zip', '81244; CO; Caon City; Fremont County; Rockvale'], ['93748', 'zip', '81243; CO; Gunnison County; Powderhorn'], ['93747', 'zip', '81242; CO; Chaffee County; Poncha Springs'], ['93746', 'zip', '81241; CO; Gunnison County; Pitkin'], ['93745', 'zip', '81240; CO; Caon City; Fremont County; Penrose'], ['93744', 'zip', '81239; CO; Gunnison County; Parlin'], ['93743', 'zip', '81237; CO; Gunnison County; Ohio City'], ['93742', 'zip', '81236; CO; Chaffee County; Nathrop'], ['93741', 'zip', '81235; CO; Hinsdale County; Lake City'], ['93740', 'zip', '81233; CO; Caon City; Fremont County; Howard'], ['93737', 'zip', '81230; CO; Gunnison County; Gunnison'], ['93736', 'zip', '81228; CO; Chaffee County; Buena Vista'], ['93734', 'zip', '81226; CO; Caon City; Fremont County; Florence'], ['93733', 'zip', '81225; CO; Gunnison County; Mount Crested Butte'], ['93732', 'zip', '81224; CO; Gunnison County; Crested Butte'], ['93731', 'zip', '81223; CO; Caon City; Fremont County; Cotopaxi'], ['93730', 'zip', '81222; CO; Caon City; Fremont County; Cotopaxi'], ['93729', 'zip', '81221; CO; Caon City; Fremont County; Coal Creek'], ['93728', 'zip', '81220; CO; Gunnison County; Cimarron'], ['93726', 'zip', '81212; CO; Caon City; Fremont County; Canon City'], ['93725', 'zip', '81211; CO; Chaffee County; Buena Vista'], ['93724', 'zip', '81210; CO; Gunnison County; Almont'], ['93723', 'zip', '81201; CO; Chaffee County; Salida'], ['93721', 'zip', '81155; CO; Saguache County; Bonanza City'], ['93720', 'zip', '81154; CO; Rio Grande County; South Fork'], ['93718', 'zip', '81152; CO; Costilla County; San Luis'], ['93717', 'zip', '81151; CO; Costilla County; Sanford'], ['93716', 'zip', '81149; CO; Saguache County; Saguache'], ['93715', 'zip', '81148; CO; Conejos County; Romeo'], ['93714', 'zip', '81147; CO; Archuleta County; Pagosa Springs'], ['93713', 'zip', '81146; CO; Alamosa County; Mosca'], ['93712', 'zip', '81144; CO; Rio Grande County; Monte Vista'], ['93711', 'zip', '81143; CO; Saguache County; Moffat'], ['93710', 'zip', '81141; CO; Costilla County; Sanford'], ['93709', 'zip', '81140; CO; Conejos County; La Jara'], ['93707', 'zip', '81137; CO; Durango; La Plata County; Ignacio'], ['93706', 'zip', '81136; CO; Alamosa County; Hooper'], ['93703', 'zip', '81133; CO; Costilla County; Fort Garland'], ['93702', 'zip', '81132; CO; Rio Grande County; Del Norte'], ['93701', 'zip', '81131; CO; Saguache County; Crestone'], ['93700', 'zip', '81130; CO; Mineral County; Creede'], ['937', 'county', 'Cibola County; NM; Grants'], ['93698', 'zip', '81128; CO; Archuleta County; Chromo'], ['93696', 'zip', '81126; CO; Costilla County; San Luis'], ['93695', 'zip', '81125; CO; Saguache County; Center'], ['93693', 'zip', '81123; CO; Costilla County; Blanca'], ['93692', 'zip', '81122; CO; Durango; La Plata County; Bayfield'], ['93691', 'zip', '81121; CO; Archuleta County; Pagosa Springs'], ['93690', 'zip', '81120; CO; Conejos County; Antonito'], ['93688', 'zip', '81101; CO; Alamosa County; Alamosa'], ['93687', 'zip', '81092; CO; Prowers County; Wiley'], ['93686', 'zip', '81091; CO; Las Animas County; Weston'], ['93685', 'zip', '81090; CO; Baca County; Walsh'], ['93684', 'zip', '81089; CO; Huerfano County; Walsenburg'], ['93681', 'zip', '81082; CO; Las Animas County; Trinidad'], ['93680', 'zip', '81081; CO; Las Animas County; Trinchera'], ['93679', 'zip', '81077; CO; Otero County; La Junta'], ['93678', 'zip', '81076; CO; Crowley County; Sugar City'], ['93677', 'zip', '81073; CO; Baca County; Springfield'], ['93675', 'zip', '81069; CO; Pueblo; Pueblo County; Rye'], ['93674', 'zip', '81067; CO; Otero County; Rocky Ford'], ['93673', 'zip', '81064; CO; Baca County; Pritchett'], ['93672', 'zip', '81063; CO; Crowley County; Ordway'], ['93671', 'zip', '81062; CO; Crowley County; Olney Springs'], ['93670', 'zip', '81059; CO; Las Animas County; Model'], ['93669', 'zip', '81058; CO; Otero County; Manzanola'], ['93668', 'zip', '81057; CO; Bent County; McClave'], ['93667', 'zip', '81055; CO; Huerfano County; La Veta'], ['93666', 'zip', '81054; CO; Bent County; Las Animas'], ['93665', 'zip', '81052; CO; Prowers County; Lamar'], ['93664', 'zip', '81050; CO; Otero County; La Junta'], ['93663', 'zip', '81049; CO; Las Animas County; Kim'], ['93662', 'zip', '81047; CO; Prowers County; Holly'], ['93660', 'zip', '81045; CO; Kiowa County; Haswell'], ['9366', 'city', 'Seymour; TN; Sevierville; Sevier County'], ['93659', 'zip', '81044; CO; Bent County; Hasty'], ['93657', 'zip', '81041; CO; Prowers County; Granada'], ['93656', 'zip', '81040; CO; Huerfano County; Gardner'], ['93655', 'zip', '81039; CO; Otero County; Fowler'], ['93653', 'zip', '81036; CO; Kiowa County; Eads'], ['93651', 'zip', '81033; CO; Crowley County; Ordway'], ['93650', 'zip', '81030; CO; Otero County; La Junta'], ['93648', 'zip', '81027; CO; Las Animas County; Branson'], ['93647', 'zip', '81025; CO; Pueblo; Pueblo County; Boone'], ['93646', 'zip', '81024; CO; Las Animas County; Weston'], ['93645', 'zip', '81023; CO; Pueblo; Pueblo County; Beulah'], ['93644', 'zip', '81022; CO; Pueblo; Pueblo County; Avondale'], ['93643', 'zip', '81021; CO; Kiowa County; Arlington'], ['93642', 'zip', '81020; CO; Las Animas County; Aguilar'], ['93641', 'zip', '81019; CO; Pueblo; Pueblo County; Colorado City'], ['93636', 'zip', '81008; CO; Pueblo; Pueblo County'], ['93635', 'zip', '81007; CO; Pueblo; Pueblo County'], ['93634', 'zip', '81006; CO; Pueblo; Pueblo County'], ['93633', 'zip', '81005; CO; Pueblo; Pueblo County'], ['93632', 'zip', '81004; CO; Pueblo; Pueblo County'], ['93631', 'zip', '81003; CO; Pueblo; Pueblo County'], ['9363', 'city', 'Woodburn; IN; Fort Wayne; Allen County'], ['93629', 'zip', '81001; CO; Pueblo; Pueblo County'], ['9362', 'city', 'Wolf Creek; OR; Grants Pass; Josephine County'], ['9361', 'city', 'Wolcottville; IN; Lagrange County'], ['93605', 'zip', '80930; CO; Colorado Springs; El Paso County; Calhan'], ['93604', 'zip', '80929; CO; Colorado Springs; El Paso County'], ['93603', 'zip', '80928; CO; Colorado Springs; El Paso County; Calhan'], ['93602', 'zip', '80926; CO; Colorado Springs; El Paso County; Black Forest'], ['93601', 'zip', '80925'], ['93600', 'zip', '80922'], ['93599', 'zip', '80921; CO; Colorado Springs; El Paso County'], ['93598', 'zip', '80920'], ['93597', 'zip', '80919'], ['93596', 'zip', '80918'], ['93595', 'zip', '80917'], ['93594', 'zip', '80916'], ['93593', 'zip', '80915'], ['9359', 'city', 'Wittensville; KY; Johnson County'], ['93589', 'zip', '80911'], ['93588', 'zip', '80910'], ['93587', 'zip', '80909'], ['93586', 'zip', '80908; CO; Colorado Springs; El Paso County; Black Forest'], ['93585', 'zip', '80907'], ['93584', 'zip', '80906'], ['93583', 'zip', '80905'], ['93582', 'zip', '80904'], ['93581', 'zip', '80903'], ['93578', 'zip', '80864; CO; Colorado Springs; El Paso County; Yoder'], ['93577', 'zip', '80863; CO; Colorado Springs; Teller County; Woodland Park'], ['93576', 'zip', '80862; CO; Cheyenne County; Wild Horse'], ['93575', 'zip', '80861; CO; Kit Carson County; Vona'], ['93574', 'zip', '80860; CO; Colorado Springs; Teller County; Victor'], ['93571', 'zip', '80836; CO; Kit Carson County; Stratton'], ['93570', 'zip', '80835; CO; Denver-Aurora-Lakewood; Elbert County; Simla'], ['93569', 'zip', '80834; CO; Kit Carson County; Seibert'], ['93568', 'zip', '80833; CO; Colorado Springs; El Paso County; Rush'], ['93567', 'zip', '80832; CO; Colorado Springs; El Paso County; Ramah'], ['93566', 'zip', '80831; CO; Colorado Springs; El Paso County; Peyton'], ['93565', 'zip', '80830; CO; Denver-Aurora-Lakewood; Elbert County; Matheson'], ['93564', 'zip', '80829; CO; Colorado Springs; El Paso County; Manitou Springs'], ['93563', 'zip', '80828; CO; Lincoln County; Limon'], ['93562', 'zip', '80827; CO; Denver-Aurora-Lakewood; Park County; Lake George'], ['93560', 'zip', '80825; CO; Cheyenne County; Kit Carson'], ['93559', 'zip', '80824; CO; Yuma County; Kirk'], ['93558', 'zip', '80823; CO; Lincoln County; Karval'], ['93557', 'zip', '80822; CO; Yuma County; Joes'], ['93556', 'zip', '80821; CO; Lincoln County; Hugo'], ['93555', 'zip', '80820; CO; Denver-Aurora-Lakewood; Park County; Guffey'], ['93554', 'zip', '80819; CO; Colorado Springs; El Paso County; Green Mountain Falls'], ['93553', 'zip', '80818; CO; Lincoln County; Genoa'], ['93552', 'zip', '80817'], ['93551', 'zip', '80816; CO; Colorado Springs; Teller County; Florissant'], ['93550', 'zip', '80815; CO; Kit Carson County; Flagler'], ['9355', 'city', 'Winton; CA; Merced; Merced County'], ['93549', 'zip', '80814; CO; Colorado Springs; Teller County; Divide'], ['93548', 'zip', '80813; CO; Colorado Springs; Teller County; Cripple Creek'], ['93547', 'zip', '80812; CO; Washington County; Cope'], ['93546', 'zip', '80810; CO; Cheyenne County; Cheyenne Wells'], ['93545', 'zip', '80809; CO; Colorado Springs; El Paso County; Green Mountain Falls'], ['93544', 'zip', '80808; CO; Colorado Springs; El Paso County; Calhan'], ['93543', 'zip', '80807; CO; Kit Carson County; Burlington'], ['93542', 'zip', '80805; CO; Kit Carson County; Bethune'], ['93541', 'zip', '80804; CO; Lincoln County; Arriba'], ['93540', 'zip', '80802; CO; Cheyenne County; Arapahoe'], ['93539', 'zip', '80801; CO; Washington County; Anton'], ['93538', 'zip', '80759; CO; Yuma County; Yuma'], ['93537', 'zip', '80758; CO; Yuma County; Wray'], ['93536', 'zip', '80757; CO; Washington County; Woodrow'], ['93535', 'zip', '80755; CO; Yuma County; Vernon'], ['93534', 'zip', '80754; CO; Greeley; Weld County; Stoneham'], ['93533', 'zip', '80751; CO; Sterling; Logan County'], ['93532', 'zip', '80750; CO; Fort Morgan; Morgan County; Snyder'], ['93531', 'zip', '80749; CO; Sedgwick County; Sedgwick'], ['93530', 'zip', '80747; CO; Sterling; Logan County; Peetz'], ['9353', 'city', 'Waitsburg; WA; Walla Walla; Walla Walla County'], ['93528', 'zip', '80745; CO; Sterling; Logan County; Padroni'], ['93527', 'zip', '80744; CO; Sedgwick County; Ovid'], ['93526', 'zip', '80743; CO; Washington County; Otis'], ['93525', 'zip', '80742; CO; Greeley; Weld County; Raymer'], ['93524', 'zip', '80741; CO; Sterling; Logan County; Merino'], ['93523', 'zip', '80740; CO; Washington County; Lindon'], ['93522', 'zip', '80737; CO; Sedgwick County; Julesburg'], ['93521', 'zip', '80736; CO; Sterling; Logan County; Iliff'], ['93520', 'zip', '80735; CO; Yuma County; Idalia'], ['93519', 'zip', '80734; CO; Phillips County; Holyoke'], ['93518', 'zip', '80733; CO; Fort Morgan; Morgan County; Hillrose'], ['93516', 'zip', '80731; CO; Phillips County; Haxtun'], ['93515', 'zip', '80729; CO; Greeley; Weld County; Grover'], ['93514', 'zip', '80728; CO; Sterling; Logan County; Fleming'], ['93513', 'zip', '80727; CO; Yuma County; Eckley'], ['93512', 'zip', '80726; CO; Sterling; Logan County; Crook'], ['93511', 'zip', '80723; CO; Fort Morgan; Morgan County; Brush'], ['93510', 'zip', '80722; CO; Sterling; Logan County; Atwood'], ['9351', 'city', 'Volente; TX; Austin-Round Rock; Travis County'], ['93508', 'zip', '80720; CO; Washington County; Akron'], ['93507', 'zip', '80705; CO; Fort Morgan; Morgan County; Log Lane Village'], ['93506', 'zip', '80701; CO; Fort Morgan; Morgan County'], ['93505', 'zip', '80654; CO; Fort Morgan; Morgan County; Wiggins'], ['93504', 'zip', '80653; CO; Fort Morgan; Morgan County; Weldona'], ['93503', 'zip', '80652; CO; Greeley; Weld County; Roggen'], ['93502', 'zip', '80651; CO; Greeley; Weld County; Platteville'], ['93501', 'zip', '80650; CO; Greeley; Weld County; Pierce'], ['93500', 'zip', '80649; CO; Fort Morgan; Morgan County; Orchard'], ['935', 'county', 'Chowan County; NC'], ['93499', 'zip', '80648; CO; Greeley; Weld County; Nunn'], ['93497', 'zip', '80645; CO; Greeley; Weld County; La Salle'], ['93496', 'zip', '80644; CO; Greeley; Weld County; Kersey'], ['93495', 'zip', '80643; CO; Greeley; Weld County; Keenesburg'], ['93494', 'zip', '80642; CO; Greeley; Weld County; Hudson'], ['93493', 'zip', '80640; CO; Denver-Aurora-Lakewood; Adams County; Commerce City'], ['93490', 'zip', '80634'], ['93487', 'zip', '80631; CO; Greeley; Weld County'], ['93486', 'zip', '80624; CO; Greeley; Weld County; Gill'], ['93485', 'zip', '80623; CO; Greeley; Weld County; Platteville'], ['93484', 'zip', '80622; CO; Greeley; Weld County; Galeton'], ['93483', 'zip', '80621; CO; Greeley; Weld County; Fort Lupton'], ['93482', 'zip', '80620; CO; Greeley; Weld County; Evans'], ['93481', 'zip', '80615; CO; Greeley; Weld County; Eaton'], ['93479', 'zip', '80612; CO; Greeley; Weld County; Carr'], ['93478', 'zip', '80611; CO; Greeley; Weld County; Briggsdale'], ['93477', 'zip', '80610; CO; Greeley; Weld County; Ault'], ['93476', 'zip', '80603; CO; Greeley; Weld County; Lochbuie'], ['93475', 'zip', '80602; CO; Denver-Aurora-Lakewood; Adams County; Thornton'], ['93474', 'zip', '80601'], ['93471', 'zip', '80550; CO; Greeley; Weld County; Windsor'], ['93470', 'zip', '80549; CO; Fort Collins; Larimer County; Wellington'], ['93469', 'zip', '80547; CO; Fort Collins; Larimer County; Timnath'], ['93468', 'zip', '80546; CO; Greeley; Weld County; Severance'], ['93467', 'zip', '80545; CO; Fort Collins; Larimer County; Red Feather Lakes'], ['93465', 'zip', '80543; CO; Greeley; Weld County; Milliken'], ['93464', 'zip', '80542; CO; Greeley; Weld County; Mead'], ['93462', 'zip', '80540; CO; Boulder; Boulder County; Lyons'], ['93460', 'zip', '80538; CO; Fort Collins; Larimer County; Loveland'], ['9346', 'city', 'Taylorsville; CA; Plumas County'], ['93459', 'zip', '80537'], ['93458', 'zip', '80536; CO; Fort Collins; Larimer County; Livermore'], ['93457', 'zip', '80535; CO; Fort Collins; Larimer County; Laporte'], ['93456', 'zip', '80534; CO; Greeley; Weld County; Johnstown'], ['93454', 'zip', '80532; CO; Fort Collins; Larimer County; Drake'], ['93453', 'zip', '80530; CO; Greeley; Weld County; Frederick'], ['93452', 'zip', '80528; CO; Fort Collins; Larimer County'], ['93450', 'zip', '80526'], ['9345', 'city', 'Tangipahoa; LA; Hammond; Tangipahoa Parish'], ['93449', 'zip', '80525'], ['93448', 'zip', '80524; CO; Fort Collins; Larimer County'], ['93445', 'zip', '80521; CO; Fort Collins; Larimer County'], ['93444', 'zip', '80520; CO; Greeley; Weld County; Firestone'], ['93443', 'zip', '80517; CO; Fort Collins; Larimer County; Estes Park'], ['93442', 'zip', '80516; CO; Greeley; Weld County; Erie'], ['93441', 'zip', '80515; CO; Fort Collins; Larimer County; Drake'], ['93440', 'zip', '80514; CO; Greeley; Weld County; Dacono'], ['93439', 'zip', '80513; CO; Fort Collins; Larimer County; Berthoud'], ['93438', 'zip', '80512; CO; Fort Collins; Larimer County; Bellvue'], ['93437', 'zip', '80511; CO; Fort Collins; Larimer County; Estes Park'], ['93436', 'zip', '80510; CO; Boulder; Boulder County; Allenspark'], ['93435', 'zip', '80504'], ['93434', 'zip', '80503; CO; Boulder; Boulder County; Longmont'], ['93432', 'zip', '80501'], ['93431', 'zip', '80498; CO; Breckenridge; Summit County; Silverthorne'], ['93430', 'zip', '80497; CO; Breckenridge; Summit County; Silverthorne'], ['9343', 'city', 'Sesser; IL; Franklin County'], ['93428', 'zip', '80487; CO; Steamboat Springs; Routt County'], ['93427', 'zip', '80483; CO; Steamboat Springs; Routt County; Yampa'], ['93426', 'zip', '80482; CO; Grand County; Winter Park'], ['93425', 'zip', '80481; CO; Boulder; Boulder County; Ward'], ['93424', 'zip', '80480; CO; Jackson County; Walden'], ['93423', 'zip', '80479; CO; Steamboat Springs; Routt County; Mc Coy'], ['93422', 'zip', '80478; CO; Grand County; Tabernash'], ['93421', 'zip', '80477; CO; Steamboat Springs; Routt County'], ['93420', 'zip', '80476; CO; Denver-Aurora-Lakewood; Clear Creek County; Silver Plume'], ['9342', 'city', 'Senecaville; OH; Cambridge; Guernsey County'], ['93419', 'zip', '80475; CO; Denver-Aurora-Lakewood; Park County; Bailey'], ['93418', 'zip', '80474; CO; Denver-Aurora-Lakewood; Gilpin County; Rollinsville'], ['93416', 'zip', '80471; CO; Boulder; Boulder County; Nederland'], ['93415', 'zip', '80470; CO; Denver-Aurora-Lakewood; Jefferson County; Pine'], ['93414', 'zip', '80469; CO; Steamboat Springs; Routt County; Phippsburg'], ['93413', 'zip', '80468; CO; Grand County; Parshall'], ['93412', 'zip', '80467; CO; Steamboat Springs; Routt County; Oak Creek'], ['93411', 'zip', '80466; CO; Boulder; Boulder County; Nederland'], ['93410', 'zip', '80465; CO; Denver-Aurora-Lakewood; Jefferson County; Morrison'], ['93409', 'zip', '80463; CO; Steamboat Springs; Routt County; Mc Coy'], ['93408', 'zip', '80461; CO; Lake County; Leadville'], ['93407', 'zip', '80459; CO; Grand County; Kremmling'], ['93406', 'zip', '80457; CO; Denver-Aurora-Lakewood; Jefferson County; Kittredge'], ['93405', 'zip', '80456; CO; Denver-Aurora-Lakewood; Park County; Jefferson'], ['93404', 'zip', '80455; CO; Boulder; Boulder County; Jamestown'], ['93403', 'zip', '80454; CO; Denver-Aurora-Lakewood; Jefferson County; Indian Hills'], ['93402', 'zip', '80453; CO; Denver-Aurora-Lakewood; Jefferson County; Morrison'], ['93401', 'zip', '80452; CO; Denver-Aurora-Lakewood; Clear Creek County; Idaho Springs'], ['93400', 'zip', '80451; CO; Grand County; Hot Sulphur Springs'], ['9340', 'city', 'Sedan; MN; Pope County'], ['934', 'county', 'Chicot County; AR'], ['93399', 'zip', '80449; CO; Denver-Aurora-Lakewood; Park County; Hartsel'], ['93398', 'zip', '80448; CO; Denver-Aurora-Lakewood; Park County; Grant'], ['93397', 'zip', '80447; CO; Grand County; Grand Lake'], ['93396', 'zip', '80446; CO; Grand County; Granby'], ['93395', 'zip', '80444; CO; Denver-Aurora-Lakewood; Clear Creek County; Georgetown'], ['93394', 'zip', '80443; CO; Breckenridge; Summit County; Frisco'], ['93393', 'zip', '80442; CO; Grand County; Fraser'], ['93392', 'zip', '80440; CO; Denver-Aurora-Lakewood; Park County; Fairplay'], ['93391', 'zip', '80439'], ['93390', 'zip', '80438; CO; Denver-Aurora-Lakewood; Clear Creek County; Empire'], ['93388', 'zip', '80436; CO; Denver-Aurora-Lakewood; Clear Creek County; Downieville-Lawson-Dumont'], ['93387', 'zip', '80435; CO; Breckenridge; Summit County; Keystone'], ['93386', 'zip', '80434; CO; Jackson County; Cowdrey'], ['93385', 'zip', '80433; CO; Denver-Aurora-Lakewood; Jefferson County; Conifer'], ['93384', 'zip', '80432; CO; Denver-Aurora-Lakewood; Park County; Jefferson'], ['93381', 'zip', '80428; CO; Steamboat Springs; Routt County; Clark'], ['93380', 'zip', '80427; CO; Denver-Aurora-Lakewood; Gilpin County; Central City'], ['93378', 'zip', '80425; CO; Denver-Aurora-Lakewood; Jefferson County; Pine'], ['93377', 'zip', '80424; CO; Breckenridge; Summit County'], ['93376', 'zip', '80423; CO; Edwards; Eagle County; Bond'], ['93375', 'zip', '80422; CO; Denver-Aurora-Lakewood; Clear Creek County; Idaho Springs'], ['93374', 'zip', '80421; CO; Denver-Aurora-Lakewood; Park County; Bailey'], ['93373', 'zip', '80420; CO; Denver-Aurora-Lakewood; Park County; Alma'], ['93371', 'zip', '80403; CO; Denver-Aurora-Lakewood; Jefferson County; Golden'], ['93369', 'zip', '80401'], ['93357', 'zip', '80305'], ['93356', 'zip', '80304'], ['93355', 'zip', '80303'], ['93354', 'zip', '80302'], ['93353', 'zip', '80301; CO; Boulder; Boulder County'], ['93330', 'zip', '80260'], ['93322', 'zip', '80249'], ['93320', 'zip', '80247'], ['93319', 'zip', '80246; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93316', 'zip', '80241'], ['93315', 'zip', '80239'], ['93314', 'zip', '80238; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93313', 'zip', '80237; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93312', 'zip', '80236; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93311', 'zip', '80235; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93310', 'zip', '80234'], ['93309', 'zip', '80233; CO; Denver-Aurora-Lakewood; Adams County; Northglenn'], ['93308', 'zip', '80232'], ['93307', 'zip', '80231'], ['93306', 'zip', '80230; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93305', 'zip', '80229; CO; Denver-Aurora-Lakewood; Adams County; Thornton'], ['93304', 'zip', '80228'], ['93303', 'zip', '80227'], ['93302', 'zip', '80226'], ['93300', 'zip', '80224; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['9330', 'city', 'Talkeetna; AK; Anchorage; Matanuska Susitna Borough'], ['93299', 'zip', '80223; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93298', 'zip', '80222'], ['93297', 'zip', '80221'], ['93296', 'zip', '80220'], ['93295', 'zip', '80219'], ['93294', 'zip', '80218'], ['93292', 'zip', '80216; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93291', 'zip', '80215'], ['93290', 'zip', '80214'], ['93289', 'zip', '80212'], ['93288', 'zip', '80211'], ['93287', 'zip', '80210'], ['93286', 'zip', '80209'], ['93284', 'zip', '80207'], ['93283', 'zip', '80206'], ['93282', 'zip', '80205'], ['93281', 'zip', '80204'], ['93280', 'zip', '80203'], ['9328', 'city', 'Tabernash; CO; Grand County'], ['93279', 'zip', '80202'], ['93267', 'zip', '80138'], ['93266', 'zip', '80137; CO; Denver-Aurora-Lakewood; Arapahoe County; Watkins'], ['93265', 'zip', '80136; CO; Denver-Aurora-Lakewood; Adams County; Strasburg'], ['93264', 'zip', '80135; CO; Denver-Aurora-Lakewood; Douglas County; Sedalia'], ['93263', 'zip', '80134'], ['93262', 'zip', '80133; CO; Colorado Springs; El Paso County; Palmer Lake'], ['93261', 'zip', '80132'], ['93260', 'zip', '80131; CO; Denver-Aurora-Lakewood; Douglas County; Louviers'], ['9326', 'city', 'Scranton; NC; Hyde County'], ['93259', 'zip', '80130; CO; Denver-Aurora-Lakewood; Douglas County; Highlands Ranch'], ['93258', 'zip', '80129; CO; Denver-Aurora-Lakewood; Douglas County; Highlands Ranch'], ['93257', 'zip', '80128'], ['93256', 'zip', '80127'], ['93255', 'zip', '80126'], ['93254', 'zip', '80125; CO; Denver-Aurora-Lakewood; Douglas County; Roxborough Park'], ['93253', 'zip', '80124; CO; Denver-Aurora-Lakewood; Douglas County; Lone Tree'], ['93252', 'zip', '80123; CO; Denver-Aurora-Lakewood; Denver County; Denver'], ['93251', 'zip', '80122; CO; Denver-Aurora-Lakewood; Arapahoe County; Centennial'], ['93250', 'zip', '80121; CO; Denver-Aurora-Lakewood; Arapahoe County; Centennial'], ['9325', 'city', 'Scottsville; TX; Marshall; Harrison County'], ['93249', 'zip', '80120; CO; Denver-Aurora-Lakewood; Arapahoe County; Littleton'], ['93248', 'zip', '80118; CO; Denver-Aurora-Lakewood; Douglas County; Larkspur'], ['93247', 'zip', '80117; CO; Denver-Aurora-Lakewood; Elbert County; Kiowa'], ['93246', 'zip', '80116; CO; Denver-Aurora-Lakewood; Douglas County; Franktown'], ['93245', 'zip', '80113; CO; Denver-Aurora-Lakewood; Arapahoe County; Englewood'], ['93244', 'zip', '80112'], ['93243', 'zip', '80111'], ['93242', 'zip', '80110'], ['93241', 'zip', '80109'], ['93240', 'zip', '80108'], ['9324', 'city', 'Wynnewood; PA; Philadelphia-Camden-Wilmington; Montgomery County'], ['93239', 'zip', '80107; CO; Denver-Aurora-Lakewood; Elbert County; Elizabeth'], ['93238', 'zip', '80106; CO; Colorado Springs; El Paso County; Elbert'], ['93237', 'zip', '80105; CO; Denver-Aurora-Lakewood; Arapahoe County; Deer Trail'], ['93236', 'zip', '80104'], ['93235', 'zip', '80103; CO; Denver-Aurora-Lakewood; Arapahoe County; Byers'], ['93234', 'zip', '80102; CO; Denver-Aurora-Lakewood; Adams County; Bennett'], ['93233', 'zip', '80101; CO; Denver-Aurora-Lakewood; Elbert County; Agate'], ['93220', 'zip', '80033'], ['93219', 'zip', '80031'], ['93218', 'zip', '80030; CO; Denver-Aurora-Lakewood; Adams County; Westminster'], ['93216', 'zip', '80027'], ['93215', 'zip', '80026; CO; Boulder; Boulder County; Lafayette'], ['93214', 'zip', '80025; CO; Boulder; Boulder County; Eldorado Springs'], ['93213', 'zip', '80024; CO; Denver-Aurora-Lakewood; Adams County; Derby'], ['93212', 'zip', '80022'], ['93211', 'zip', '80021'], ['93210', 'zip', '80020'], ['9321', 'city', 'Wrightsville Beach; NC; Wilmington; New Hanover County'], ['93209', 'zip', '80019; CO; Denver-Aurora-Lakewood; Arapahoe County; Aurora'], ['93208', 'zip', '80018; CO; Denver-Aurora-Lakewood; Arapahoe County; Aurora'], ['93207', 'zip', '80017'], ['93206', 'zip', '80016'], ['93205', 'zip', '80015'], ['93204', 'zip', '80014'], ['93203', 'zip', '80013'], ['93202', 'zip', '80012'], ['93201', 'zip', '80011'], ['93200', 'zip', '80010'], ['932', 'county', 'Chelan County; WA; Wenatchee'], ['93199', 'zip', '80007; CO; Denver-Aurora-Lakewood; Jefferson County; Arvada'], ['93197', 'zip', '80005; CO; Denver-Aurora-Lakewood; Jefferson County; Arvada'], ['93196', 'zip', '80004; CO; Denver-Aurora-Lakewood; Jefferson County; Arvada'], ['93195', 'zip', '80003; CO; Denver-Aurora-Lakewood; Jefferson County; Arvada'], ['93194', 'zip', '80002; CO; Denver-Aurora-Lakewood; Jefferson County; Arvada'], ['9319', 'city', 'Woolsey; GA; Atlanta-Sandy Springs-Roswell; Fayette County'], ['9316', 'city', 'Vicksburg; MI; Kalamazoo-Portage; Kalamazoo County'], ['93146', 'zip', '79938; TX; El Paso; El Paso County'], ['93144', 'zip', '79936'], ['93143', 'zip', '79935; TX; El Paso; El Paso County'], ['93142', 'zip', '79934; TX; El Paso; El Paso County'], ['93141', 'zip', '79932; TX; El Paso; El Paso County'], ['9314', 'city', 'Vesuvius; VA; Rockbridge County'], ['93139', 'zip', '79930; TX; El Paso; El Paso County'], ['93137', 'zip', '79928'], ['93136', 'zip', '79927; TX; El Paso; El Paso County; Socorro'], ['93134', 'zip', '79925; TX; El Paso; El Paso County'], ['93133', 'zip', '79924'], ['93131', 'zip', '79922; TX; El Paso; El Paso County'], ['9313', 'city', 'Vestaburg; MI; Grand Rapids-Wyoming; Montcalm County'], ['93126', 'zip', '79915; TX; El Paso; El Paso County'], ['93123', 'zip', '79912'], ['93122', 'zip', '79911; TX; El Paso; El Paso County'], ['9312', 'city', 'Versailles; KY; Lexington-Fayette; Woodford County'], ['93119', 'zip', '79907; TX; El Paso; El Paso County'], ['93117', 'zip', '79905; TX; El Paso; El Paso County'], ['93116', 'zip', '79904'], ['93115', 'zip', '79903'], ['93114', 'zip', '79902'], ['93113', 'zip', '79901; TX; El Paso; El Paso County'], ['93110', 'zip', '79853; TX; El Paso; El Paso County; Tornillo'], ['9311', 'city', 'Verona; IL; Chicago-Naperville-Elgin; Grundy County'], ['93109', 'zip', '79852; TX; Brewster County; Terlingua'], ['93107', 'zip', '79849; TX; El Paso; El Paso County; San Elizario'], ['93101', 'zip', '79842; TX; Brewster County; Marathon'], ['9310', 'city', 'Vernonia; OR; Portland-Vancouver-Hillsboro; Columbia County'], ['931', 'county', 'Chaves County; NM; Roswell'], ['93099', 'zip', '79838; TX; El Paso; El Paso County; Fabens'], ['93097', 'zip', '79836; TX; El Paso; El Paso County'], ['93096', 'zip', '79835; TX; El Paso; El Paso County; Canutillo'], ['93093', 'zip', '79831; TX; Brewster County; Alpine'], ['93092', 'zip', '79830; TX; Brewster County; Alpine'], ['93091', 'zip', '79821; TX; El Paso; El Paso County; Anthony'], ['93090', 'zip', '79789; TX; Winkler County; Wink'], ['93089', 'zip', '79788; TX; Ward County; Wickett'], ['93086', 'zip', '79783; TX; Midland; Martin County; Tarzan'], ['93085', 'zip', '79782; TX; Midland; Martin County; Stanton'], ['93081', 'zip', '79777; TX; Ward County; Pyote'], ['93079', 'zip', '79772; TX; Pecos; Reeves County'], ['93075', 'zip', '79766; TX; Odessa; Ector County'], ['93074', 'zip', '79765; TX; Odessa; Ector County'], ['93073', 'zip', '79764; TX; Odessa; Ector County'], ['93072', 'zip', '79763; TX; Odessa; Ector County'], ['93071', 'zip', '79762; TX; Odessa; Ector County'], ['93070', 'zip', '79761; TX; Odessa; Ector County'], ['9307', 'city', 'Sweet Home; OR; Albany; Linn County'], ['93067', 'zip', '79758; TX; Odessa; Ector County; Gardendale'], ['93066', 'zip', '79756; TX; Ward County; Monahans'], ['93065', 'zip', '79755; TX; Upton County; Midkiff'], ['93062', 'zip', '79749; TX; Midland; Martin County; Tarzan'], ['93061', 'zip', '79748; TX; Big Spring; Howard County; Knott'], ['93060', 'zip', '79745; TX; Winkler County; Kermit'], ['93059', 'zip', '79744; TX; Pecos County; Iraan'], ['93058', 'zip', '79743; TX; Pecos County; Imperial'], ['93057', 'zip', '79742; TX; Ward County; Grandfalls'], ['93056', 'zip', '79741; TX; Odessa; Ector County; Goldsmith'], ['93052', 'zip', '79735; TX; Pecos County; Fort Stockton'], ['93050', 'zip', '79733; TX; Big Spring; Howard County; Forsan'], ['93049', 'zip', '79731; TX; Crane County; Crane'], ['93048', 'zip', '79730; TX; Pecos County; Coyanosa'], ['93046', 'zip', '79720; TX; Big Spring; Howard County'], ['93045', 'zip', '79719; TX; Ward County; Barstow'], ['93044', 'zip', '79718; TX; Pecos; Reeves County; Balmorhea'], ['93043', 'zip', '79714; TX; Andrews; Andrews County'], ['93042', 'zip', '79713; TX; Midland; Martin County; Ackerly'], ['9304', 'city', 'Swampscott; MA; Boston-Cambridge-Newton; Essex County'], ['93037', 'zip', '79707; TX; Midland; Midland County'], ['93036', 'zip', '79706; TX; Midland; Midland County'], ['93035', 'zip', '79705; TX; Midland; Midland County'], ['93033', 'zip', '79703; TX; Midland; Midland County'], ['93031', 'zip', '79701; TX; Midland; Midland County'], ['93026', 'zip', '79607; TX; Abilene; Taylor County'], ['93025', 'zip', '79606; TX; Abilene; Taylor County'], ['93024', 'zip', '79605; TX; Abilene; Taylor County'], ['93022', 'zip', '79603; TX; Abilene; Taylor County'], ['93021', 'zip', '79602; TX; Abilene; Taylor County'], ['93020', 'zip', '79601; TX; Abilene; Taylor County'], ['93019', 'zip', '79567; TX; Runnels County; Winters'], ['93018', 'zip', '79566; TX; Abilene; Taylor County; Wingate'], ['93017', 'zip', '79565; TX; Mitchell County; Colorado City'], ['93016', 'zip', '79563; TX; Abilene; Taylor County; Tye'], ['93015', 'zip', '79562; TX; Abilene; Taylor County; Tuscola'], ['93014', 'zip', '79561; TX; Abilene; Taylor County; Trent'], ['93013', 'zip', '79560; TX; Fisher County; Sylvester'], ['93012', 'zip', '79556; TX; Sweetwater; Nolan County'], ['93011', 'zip', '79553; TX; Abilene; Jones County; Stamford'], ['93009', 'zip', '79549; TX; Snyder; Scurry County'], ['93008', 'zip', '79548; TX; Haskell County; Rule'], ['93007', 'zip', '79547; TX; Haskell County; Rule'], ['93006', 'zip', '79546; TX; Fisher County; Rotan'], ['93005', 'zip', '79545; TX; Sweetwater; Nolan County; Roscoe'], ['93004', 'zip', '79544; TX; Haskell County; Rochester'], ['93003', 'zip', '79543; TX; Fisher County; Roby'], ['93002', 'zip', '79541; TX; Abilene; Taylor County; Ovalo'], ['93000', 'zip', "79539; TX; Haskell County; O'Brien"], ['9300', 'city', 'Sunshine; LA; Baton Rouge; Iberville Parish'], ['930', 'county', 'Centre County; PA; State College'], ['93', 'county', 'Ashe County; NC'], ['92999', 'zip', '79538; TX; Coleman County; Novice'], ['92998', 'zip', '79537; TX; Abilene; Taylor County; Merkel'], ['92997', 'zip', '79536; TX; Abilene; Taylor County; Merkel'], ['92996', 'zip', '79535; TX; Sweetwater; Nolan County; Maryneal'], ['92995', 'zip', '79534; TX; Fisher County; McCaulley'], ['92994', 'zip', '79533; TX; Abilene; Jones County; Lueders'], ['92993', 'zip', '79532; TX; Mitchell County; Loraine'], ['92992', 'zip', '79530; TX; Abilene; Taylor County; Lawn'], ['92989', 'zip', '79527; TX; Snyder; Scurry County; Ira'], ['92988', 'zip', '79526; TX; Snyder; Scurry County; Hermleigh'], ['92987', 'zip', '79525; TX; Abilene; Jones County; Hawley'], ['92986', 'zip', '79521; TX; Haskell County; Haskell'], ['92985', 'zip', '79520; TX; Abilene; Jones County; Hamlin'], ['92984', 'zip', '79519; TX; Coleman County; Goldsboro'], ['92982', 'zip', '79517; TX; Snyder; Scurry County; Fluvanna'], ['92980', 'zip', '79512; TX; Mitchell County; Colorado City'], ['9298', 'city', 'Schaller; IA; Sac County'], ['92979', 'zip', '79511; TX; Big Spring; Howard County; Coahoma'], ['92978', 'zip', '79510; TX; Abilene; Callahan County; Clyde'], ['92977', 'zip', '79508; TX; Abilene; Taylor County; Buffalo Gap'], ['92976', 'zip', '79506; TX; Sweetwater; Nolan County; Blackwell'], ['92974', 'zip', '79504; TX; Abilene; Callahan County; Baird'], ['92973', 'zip', '79503; TX; Abilene; Jones County; Avoca'], ['92971', 'zip', '79501; TX; Abilene; Jones County; Anson'], ['92961', 'zip', '79424; TX; Lubbock; Lubbock County'], ['92960', 'zip', '79423; TX; Lubbock; Lubbock County'], ['92959', 'zip', '79416; TX; Lubbock; Lubbock County'], ['92958', 'zip', '79415; TX; Lubbock; Lubbock County'], ['92957', 'zip', '79414; TX; Lubbock; Lubbock County'], ['92956', 'zip', '79413; TX; Lubbock; Lubbock County'], ['92955', 'zip', '79412; TX; Lubbock; Lubbock County'], ['92954', 'zip', '79411; TX; Lubbock; Lubbock County'], ['92953', 'zip', '79410; TX; Lubbock; Lubbock County'], ['92950', 'zip', '79407; TX; Lubbock; Lubbock County'], ['9295', 'city', 'Scandinavia; WI; Waupaca County'], ['92947', 'zip', '79404; TX; Lubbock; Lubbock County'], ['92946', 'zip', '79403; TX; Lubbock; Lubbock County'], ['92944', 'zip', '79401; TX; Lubbock; Lubbock County'], ['92943', 'zip', '79383; TX; Lubbock; Lynn County; New Home'], ['92942', 'zip', '79382; TX; Lubbock; Lubbock County; Wolfforth'], ['92941', 'zip', '79381; TX; Lubbock; Lynn County; Wilson'], ['92940', 'zip', '79380; TX; Lamb County; Littlefield'], ['9294', 'city', 'Savage; MT; Richland County'], ['92938', 'zip', '79378; TX; Terry County; Brownfield'], ['92937', 'zip', '79377; TX; Lamesa; Dawson County; Welch'], ['92936', 'zip', '79376; TX; Terry County; Brownfield'], ['92935', 'zip', '79373; TX; Lubbock; Lynn County; Tahoka'], ['92934', 'zip', '79372; TX; Levelland; Hockley County; Sundown'], ['92933', 'zip', '79371; TX; Lamb County; Sudan'], ['92932', 'zip', '79370; TX; Dickens County; Spur'], ['92931', 'zip', '79369; TX; Lamb County; Spade'], ['92930', 'zip', '79367; TX; Levelland; Hockley County; Smyer'], ['9293', 'city', 'Winslow; IL; Freeport; Stephenson County'], ['92929', 'zip', '79366; TX; Lubbock; Lubbock County; Ransom Canyon'], ['92928', 'zip', '79364; TX; Lubbock; Lubbock County; Slaton'], ['92927', 'zip', '79363; TX; Lubbock; Lubbock County; Shallowater'], ['92926', 'zip', '79360; TX; Gaines County; Seminole'], ['92925', 'zip', '79359; TX; Gaines County; Seagraves'], ['92924', 'zip', '79358; TX; Levelland; Hockley County; Ropesville'], ['92923', 'zip', '79357; TX; Lubbock; Crosby County; Ralls'], ['92922', 'zip', '79356; TX; Garza County; Post'], ['92921', 'zip', '79355; TX; Yoakum County; Plains'], ['9292', 'city', 'Winslow; AR; Fayetteville-Springdale-Rogers; Washington County'], ['92919', 'zip', "79351; TX; Lubbock; Lynn County; O'Donnell"], ['92918', 'zip', '79350; TX; Lubbock; Lubbock County; New Deal'], ['92917', 'zip', '79347; TX; Bailey County; Muleshoe'], ['92915', 'zip', '79345; TX; Terry County; Meadow'], ['92913', 'zip', '79343; TX; Lubbock; Crosby County; Lorenzo'], ['92912', 'zip', '79342; TX; Gaines County; Loop'], ['92911', 'zip', '79339; TX; Lamb County; Littlefield'], ['92909', 'zip', '79336; TX; Levelland; Hockley County'], ['92908', 'zip', '79331; TX; Lamesa; Dawson County'], ['92907', 'zip', '79330; TX; Garza County; Post'], ['92906', 'zip', '79329; TX; Lubbock; Lubbock County; Idalou'], ['92905', 'zip', '79326; TX; Lamb County; Fieldton'], ['92904', 'zip', '79325; TX; Parmer County; Farwell'], ['92902', 'zip', '79323; TX; Yoakum County; Denver City'], ['92901', 'zip', '79322; TX; Lubbock; Crosby County; Crosbyton'], ['9290', 'city', 'Windsor; KY; Casey County'], ['929', 'county', 'Cayuga County; NY; Auburn'], ['92899', 'zip', '79316; TX; Terry County; Brownfield'], ['92897', 'zip', '79313; TX; Levelland; Hockley County; Anton'], ['92896', 'zip', '79312; TX; Lamb County; Amherst'], ['92895', 'zip', '79311; TX; Plainview; Hale County; Abernathy'], ['92894', 'zip', '79261; TX; Hall County; Turkey'], ['92893', 'zip', '79259; TX; Childress County; Tell'], ['9289', 'city', 'Windber; PA; Somerset; Somerset County'], ['92886', 'zip', '79250; TX; Plainview; Hale County; Petersburg'], ['92884', 'zip', '79247; TX; Vernon; Wilbarger County'], ['92883', 'zip', '79245; TX; Hall County; Memphis'], ['92881', 'zip', '79243; TX; Dickens County; McAdoo'], ['92880', 'zip', '79241; TX; Floyd County; Lockney'], ['92878', 'zip', '79239; TX; Hall County; Lakeview'], ['92877', 'zip', '79237; TX; Donley County; Hedley'], ['92875', 'zip', '79235; TX; Floyd County; Floydada'], ['92873', 'zip', '79233; TX; Hall County; Estelline'], ['92871', 'zip', '79230; TX; Collingsworth County; Dodson'], ['9287', 'city', 'Vaughn; WA; Seattle-Tacoma-Bellevue; Pierce County'], ['92868', 'zip', '79226; TX; Donley County; Howardwick'], ['92867', 'zip', '79225; TX; Hardeman County; Chillicothe'], ['92863', 'zip', '79201; TX; Childress County; Childress'], ['92849', 'zip', '79124; TX; Amarillo; Potter County'], ['92847', 'zip', '79121; TX; Amarillo; Potter County'], ['92845', 'zip', '79119; TX; Amarillo; Potter County'], ['92844', 'zip', '79118; TX; Amarillo; Potter County'], ['92840', 'zip', '79111; TX; Amarillo; Potter County'], ['92839', 'zip', '79110; TX; Amarillo; Potter County'], ['92838', 'zip', '79109; TX; Amarillo; Potter County'], ['92837', 'zip', '79108; TX; Amarillo; Potter County'], ['92836', 'zip', '79107; TX; Amarillo; Potter County'], ['92835', 'zip', '79106; TX; Amarillo; Potter County'], ['92833', 'zip', '79104; TX; Amarillo; Potter County'], ['92832', 'zip', '79103; TX; Amarillo; Potter County'], ['92831', 'zip', '79102; TX; Amarillo; Potter County'], ['92830', 'zip', '79101; TX; Amarillo; Potter County'], ['92829', 'zip', '79098; TX; Amarillo; Oldham County; Wildorado'], ['92828', 'zip', '79097; TX; Amarillo; Carson County; White Deer'], ['92827', 'zip', '79096; TX; Wheeler County; Wheeler'], ['92825', 'zip', '79094; TX; Amarillo; Armstrong County; Wayside'], ['92821', 'zip', '79088; TX; Swisher County; Tulia'], ['92820', 'zip', '79087; TX; Dallam County; Texline'], ['92819', 'zip', '79086; TX; Dumas; Moore County; Sunray'], ['92818', 'zip', '79085; TX; Hereford; Deaf Smith County'], ['92816', 'zip', '79083; TX; Borger; Hutchinson County; Stinnett'], ['92815', 'zip', '79082; TX; Lamb County; Springlake'], ['92814', 'zip', '79081; TX; Hansford County; Spearman'], ['92813', 'zip', '79080; TX; Amarillo; Carson County; Skellytown'], ['92812', 'zip', '79079; TX; Wheeler County; Shamrock'], ['92811', 'zip', '79078; TX; Borger; Hutchinson County; Sanford'], ['9281', 'city', 'Town Of Sardinia; NY; Buffalo-Cheektowaga-Niagara Falls; Erie County'], ['92808', 'zip', '79072; TX; Plainview; Hale County'], ['92807', 'zip', '79070; TX; Ochiltree County; Perryton'], ['92806', 'zip', '79068; TX; Amarillo; Carson County; Panhandle'], ['92804', 'zip', '79065; TX; Pampa; Gray County'], ['92803', 'zip', '79064; TX; Lamb County; Olton'], ['92802', 'zip', '79063; TX; Castro County; Nazareth'], ['92801', 'zip', '79062; TX; Hansford County; Morse'], ['9280', 'city', 'Sapphire; NC; Cullowhee; Jackson County'], ['92799', 'zip', '79059; TX; Roberts County; Miami'], ['92797', 'zip', '79057; TX; Pampa; Gray County; McLean'], ['92793', 'zip', '79052; TX; Swisher County; Kress'], ['92790', 'zip', '79045; TX; Hereford; Deaf Smith County'], ['92788', 'zip', '79043; TX; Castro County; Hart'], ['92787', 'zip', '79042; TX; Swisher County; Happy'], ['92786', 'zip', '79041; TX; Plainview; Hale County; Hale Center'], ['92785', 'zip', '79040; TX; Hansford County; Gruver'], ['92784', 'zip', '79039; TX; Amarillo; Carson County; Groom'], ['92783', 'zip', '79036; TX; Borger; Hutchinson County; Fritch'], ['92782', 'zip', '79035; TX; Parmer County; Friona'], ['9278', 'city', 'Zirconia; NC; Asheville; Henderson County'], ['92779', 'zip', '79032; TX; Plainview; Hale County; Edmonson'], ['92778', 'zip', '79031; TX; Lamb County; Earth'], ['92777', 'zip', '79029; TX; Dumas; Moore County'], ['92776', 'zip', '79027; TX; Castro County; Dimmitt'], ['92773', 'zip', '79022; TX; Dallam County; Dalhart'], ['92772', 'zip', '79021; TX; Plainview; Hale County; Hale Center'], ['92771', 'zip', '79019; TX; Amarillo; Armstrong County; Claude'], ['92770', 'zip', '79018; TX; Hartley County; Channing'], ['92768', 'zip', '79015; TX; Amarillo; Randall County; Canyon'], ['92766', 'zip', '79013; TX; Dumas; Moore County; Cactus'], ['92765', 'zip', '79012; TX; Amarillo; Potter County; Boys Ranch'], ['92764', 'zip', '79011; TX; Wheeler County; Briscoe'], ['92763', 'zip', '79010; TX; Amarillo; Potter County; Boys Ranch'], ['92762', 'zip', '79009; TX; Parmer County; Bovina'], ['92760', 'zip', '79007; TX; Borger; Hutchinson County'], ['92759', 'zip', '79005; TX; Lipscomb County; Booker'], ['92758', 'zip', '79003; TX; Wheeler County; Allison'], ['92755', 'zip', '78963; TX; Fayette County; West Point'], ['92754', 'zip', '78962; TX; Colorado County; Weimar'], ['92752', 'zip', '78960; TX; Fayette County; La Grange'], ['92751', 'zip', '78959; TX; Gonzales County; Waelder'], ['92750', 'zip', '78957; TX; Austin-Round Rock; Bastrop County; Smithville'], ['9275', 'city', 'Valley Falls; KS; Topeka; Jefferson County'], ['92749', 'zip', '78956; TX; Fayette County; Schulenburg'], ['92748', 'zip', '78954; TX; Fayette County; Round Top'], ['92747', 'zip', '78953; TX; Austin-Round Rock; Bastrop County; Rosanky'], ['92746', 'zip', '78952; TX; Fayette County; La Grange'], ['92745', 'zip', '78951; TX; Houston-The Woodlands-Sugar Land; Chambers County; Anahuac'], ['92744', 'zip', '78950; TX; Houston-The Woodlands-Sugar Land; Austin County; New Ulm'], ['92743', 'zip', '78949; TX; Fayette County; Muldoon'], ['92742', 'zip', '78948; TX; Lee County; Lincoln'], ['92741', 'zip', '78947; TX; Lee County; Lexington'], ['92740', 'zip', '78946; TX; Lee County; Ledbetter'], ['9274', 'city', 'Valley City; ND; Barnes County'], ['92739', 'zip', '78945; TX; Fayette County; La Grange'], ['92738', 'zip', '78944; TX; Houston-The Woodlands-Sugar Land; Austin County; Industry'], ['92737', 'zip', '78943; TX; Colorado County; Glidden'], ['92736', 'zip', '78942; TX; Lee County; Giddings'], ['92735', 'zip', '78941; TX; Fayette County; Flatonia'], ['92734', 'zip', '78940; TX; Fayette County; Fayetteville'], ['92733', 'zip', '78938; TX; Fayette County; Ellinger'], ['92732', 'zip', '78935; TX; Colorado County; Alleyton'], ['92731', 'zip', '78934; TX; Colorado County; Columbus'], ['92730', 'zip', '78933; TX; Houston-The Woodlands-Sugar Land; Austin County; Cat Spring'], ['9273', 'city', 'Valley; AL; Chambers County'], ['92729', 'zip', '78932; TX; Fayette County; Carmine'], ['92728', 'zip', '78931; TX; Houston-The Woodlands-Sugar Land; Austin County; Bleiblerville'], ['92727', 'zip', '78886; TX; San Antonio-New Braunfels; Medina County; Yancey'], ['92725', 'zip', '78884; TX; Uvalde; Uvalde County; Utopia'], ['92724', 'zip', '78883; TX; San Antonio-New Braunfels; Bandera County; Tarpley'], ['92723', 'zip', '78881; TX; Uvalde; Uvalde County; Sabinal'], ['92722', 'zip', '78880; TX; Edwards County; Rocksprings'], ['92721', 'zip', '78879; TX; Real County; Rio Frio'], ['92720', 'zip', '78877; TX; Eagle Pass; Maverick County; Quemado'], ['92719', 'zip', '78873; TX; Real County; Leakey'], ['92718', 'zip', '78872; TX; Zavala County; La Pryor'], ['92716', 'zip', '78870; TX; Uvalde; Uvalde County; Knippa'], ['92715', 'zip', '78861; TX; San Antonio-New Braunfels; Medina County; Hondo'], ['92714', 'zip', '78860; TX; Eagle Pass; Maverick County; El Indio'], ['92713', 'zip', '78853; TX; Eagle Pass; Maverick County'], ['92712', 'zip', '78852; TX; Eagle Pass; Maverick County'], ['92710', 'zip', '78850; TX; San Antonio-New Braunfels; Medina County; D Hanis'], ['92705', 'zip', '78840; TX; Del Rio; Val Verde County'], ['92704', 'zip', '78839; TX; Zavala County; Crystal City'], ['92703', 'zip', '78838; TX; Uvalde; Uvalde County; Concan'], ['92702', 'zip', '78837; TX; Del Rio; Val Verde County; Comstock'], ['92701', 'zip', '78836; TX; Dimmit County; Catarina'], ['92700', 'zip', '78834; TX; Dimmit County; Carrizo Springs'], ['92699', 'zip', '78833; TX; Real County; Camp Wood'], ['92698', 'zip', '78832; TX; Kinney County; Brackettville'], ['92697', 'zip', '78830; TX; Dimmit County; Big Wells'], ['92696', 'zip', '78829; TX; Zavala County; Batesville'], ['92694', 'zip', '78827; TX; Dimmit County; Asherton'], ['92692', 'zip', '78801; TX; Uvalde; Uvalde County'], ['9269', 'city', 'Utopia; TX; Uvalde; Uvalde County'], ['9267', 'city', 'Sugar City; ID; Rexburg; Madison County'], ['92668', 'zip', '78759'], ['92667', 'zip', '78758'], ['92666', 'zip', '78757'], ['92665', 'zip', '78756'], ['92663', 'zip', '78754'], ['92662', 'zip', '78753'], ['92661', 'zip', '78752'], ['92660', 'zip', '78751'], ['92659', 'zip', '78750'], ['92658', 'zip', '78749'], ['92657', 'zip', '78748'], ['92656', 'zip', '78747'], ['92655', 'zip', '78746'], ['92654', 'zip', '78745'], ['92653', 'zip', '78744'], ['92652', 'zip', '78742; TX; Austin-Round Rock; Travis County; Austin'], ['92651', 'zip', '78741'], ['92650', 'zip', '78739'], ['92649', 'zip', '78738; TX; Austin-Round Rock; Travis County; Bee Cave'], ['92648', 'zip', '78737; TX; Austin-Round Rock; Travis County; Austin'], ['92647', 'zip', '78736; TX; Austin-Round Rock; Travis County; Austin'], ['92646', 'zip', '78735; TX; Austin-Round Rock; Travis County; Austin'], ['92645', 'zip', '78734'], ['92644', 'zip', '78733; TX; Austin-Round Rock; Travis County; Austin'], ['92643', 'zip', '78732'], ['92642', 'zip', '78731'], ['92641', 'zip', '78730; TX; Austin-Round Rock; Travis County; Austin'], ['92640', 'zip', '78729'], ['92639', 'zip', '78728'], ['92638', 'zip', '78727'], ['92637', 'zip', '78726'], ['92636', 'zip', '78725'], ['92635', 'zip', '78724'], ['92634', 'zip', '78723'], ['92633', 'zip', '78722; TX; Austin-Round Rock; Travis County; Austin'], ['92632', 'zip', '78721'], ['92630', 'zip', '78719; TX; Austin-Round Rock; Travis County; Austin'], ['9263', 'city', 'Stroud; OK; Oklahoma City; Lincoln County'], ['92628', 'zip', '78717'], ['92618', 'zip', '78705'], ['92617', 'zip', '78704'], ['92616', 'zip', '78703'], ['92615', 'zip', '78702'], ['92614', 'zip', '78701'], ['92610', 'zip', '78681'], ['9261', 'city', 'Stratford; CA; Hanford-Corcoran; Kings County'], ['92608', 'zip', '78677; TX; Gonzales County; Smiley'], ['92607', 'zip', '78676; TX; Austin-Round Rock; Hays County; Wimberley'], ['92606', 'zip', '78675; TX; Fredericksburg; Gillespie County; Willow City'], ['92605', 'zip', '78674; TX; Austin-Round Rock; Williamson County; Georgetown'], ['92603', 'zip', '78672; TX; Llano County; Tow'], ['92602', 'zip', '78671; TX; Fredericksburg; Gillespie County; Stonewall'], ['92601', 'zip', '78670; TX; Austin-Round Rock; Caldwell County; Martindale'], ['92600', 'zip', '78669; TX; Austin-Round Rock; Travis County; Spicewood'], ['926', 'county', 'Castro County; TX'], ['92598', 'zip', '78666'], ['92597', 'zip', '78664'], ['92596', 'zip', '78663; TX; Blanco County; Round Mountain'], ['92595', 'zip', '78662; TX; Austin-Round Rock; Bastrop County; Red Rock'], ['92594', 'zip', '78661; TX; Austin-Round Rock; Caldwell County; Luling'], ['92593', 'zip', '78660'], ['92592', 'zip', '78659; TX; Austin-Round Rock; Bastrop County; Paige'], ['92591', 'zip', '78658; TX; Gonzales County; Gonzales'], ['92590', 'zip', '78657; TX; Llano County; Horseshoe Bay'], ['92589', 'zip', '78656; TX; Austin-Round Rock; Caldwell County; Maxwell'], ['92588', 'zip', '78655; TX; Austin-Round Rock; Caldwell County; Martindale'], ['92587', 'zip', '78654; TX; Burnet County; Marble Falls'], ['92586', 'zip', '78653'], ['92585', 'zip', '78652; TX; Austin-Round Rock; Travis County; Austin'], ['92583', 'zip', '78650; TX; Austin-Round Rock; Bastrop County; McDade'], ['92582', 'zip', '78648; TX; Austin-Round Rock; Caldwell County; Luling'], ['92580', 'zip', '78645; TX; Austin-Round Rock; Travis County; Lago Vista'], ['92579', 'zip', '78644; TX; Austin-Round Rock; Caldwell County; Lockhart'], ['92578', 'zip', '78643; TX; Llano County; Llano'], ['92577', 'zip', '78642; TX; Austin-Round Rock; Williamson County; Liberty Hill'], ['92576', 'zip', '78641'], ['92575', 'zip', '78640'], ['92574', 'zip', '78639; TX; Llano County; Kingsland'], ['92573', 'zip', '78638; TX; San Antonio-New Braunfels; Guadalupe County; Kingsbury'], ['92572', 'zip', '78636; TX; Blanco County; Johnson City'], ['92571', 'zip', '78635; TX; Blanco County; Johnson City'], ['92570', 'zip', '78634'], ['92569', 'zip', '78632; TX; Gonzales County; Harwood'], ['92568', 'zip', '78631; TX; Fredericksburg; Gillespie County; Harper'], ['92566', 'zip', '78629; TX; Gonzales County; Gonzales'], ['92565', 'zip', '78628'], ['92563', 'zip', '78626'], ['92562', 'zip', '78624; TX; Fredericksburg; Gillespie County'], ['92561', 'zip', '78623; TX; San Antonio-New Braunfels; Comal County; Canyon Lake'], ['92560', 'zip', '78622; TX; Austin-Round Rock; Caldwell County; Luling'], ['92559', 'zip', '78621; TX; Austin-Round Rock; Bastrop County; Elgin'], ['92558', 'zip', '78620; TX; Austin-Round Rock; Hays County; Dripping Springs'], ['92557', 'zip', '78619; TX; Austin-Round Rock; Hays County; Driftwood'], ['92556', 'zip', '78618; TX; Fredericksburg; Gillespie County; Doss'], ['92555', 'zip', '78617; TX; Austin-Round Rock; Travis County; Austin'], ['92554', 'zip', '78616; TX; Austin-Round Rock; Bastrop County; Cedar Creek'], ['92553', 'zip', '78615; TX; Austin-Round Rock; Williamson County; Coupland'], ['92552', 'zip', '78614; TX; Gonzales County; Cost'], ['92551', 'zip', '78613'], ['92550', 'zip', '78612; TX; Austin-Round Rock; Bastrop County; Cedar Creek'], ['9255', 'city', 'San Pablo; CA; San Francisco-Oakland-Hayward; Contra Costa County'], ['92549', 'zip', '78611; TX; Burnet County; Burnet'], ['92548', 'zip', '78610; TX; Austin-Round Rock; Hays County; Buda'], ['92547', 'zip', '78609; TX; Llano County; Buchanan Dam'], ['92546', 'zip', '78608; TX; Burnet County; Briggs'], ['92545', 'zip', '78607; TX; Llano County; Bluffton'], ['92544', 'zip', '78606; TX; Blanco County; Blanco'], ['92543', 'zip', '78605; TX; Burnet County; Bertram'], ['92542', 'zip', '78604; TX; Gonzales County; Gonzales'], ['92541', 'zip', '78602; TX; Austin-Round Rock; Bastrop County; Bastrop'], ['92540', 'zip', '78599; TX; McAllen-Edinburg-Mission; Hidalgo County; Weslaco'], ['9254', 'city', 'Wilmot; SD; Roberts County'], ['92539', 'zip', '78598; TX; Raymondville; Willacy County; Port Mansfield'], ['92538', 'zip', '78597; TX; Brownsville-Harlingen; Cameron County; South Padre Island'], ['92537', 'zip', '78596; TX; McAllen-Edinburg-Mission; Hidalgo County; Weslaco'], ['92536', 'zip', '78595; TX; McAllen-Edinburg-Mission; Hidalgo County; Sullivan City'], ['92535', 'zip', '78594; TX; Raymondville; Willacy County; Sebastian'], ['92534', 'zip', '78593; TX; Brownsville-Harlingen; Cameron County; Santa Rosa'], ['92533', 'zip', '78592; TX; Brownsville-Harlingen; Cameron County; La Feria'], ['92532', 'zip', '78591; TX; Rio Grande City; Starr County; Santa Elena'], ['92531', 'zip', '78590; TX; Raymondville; Willacy County; San Perlita'], ['92530', 'zip', '78589; TX; McAllen-Edinburg-Mission; Hidalgo County; San Juan'], ['9253', 'city', 'Wilmot; NH; Concord; Merrimack County'], ['92529', 'zip', '78588; TX; Rio Grande City; Starr County; Santa Elena'], ['92528', 'zip', '78586; TX; Brownsville-Harlingen; Cameron County; San Benito'], ['92527', 'zip', '78585; TX; Rio Grande City; Starr County; Falcon Heights'], ['92526', 'zip', '78584; TX; Rio Grande City; Starr County; Roma'], ['92525', 'zip', '78583; TX; Brownsville-Harlingen; Cameron County; Rio Hondo'], ['92524', 'zip', '78582; TX; Rio Grande City; Starr County'], ['92523', 'zip', '78580; TX; Raymondville; Willacy County'], ['92522', 'zip', '78579; TX; McAllen-Edinburg-Mission; Hidalgo County; Progreso'], ['92521', 'zip', '78578; TX; Brownsville-Harlingen; Cameron County; Port Isabel'], ['92520', 'zip', '78577; TX; McAllen-Edinburg-Mission; Hidalgo County; Pharr'], ['92519', 'zip', '78576; TX; McAllen-Edinburg-Mission; Hidalgo County; Penitas'], ['92518', 'zip', '78575; TX; Brownsville-Harlingen; Cameron County; Rancho Viejo'], ['92517', 'zip', '78574; TX; McAllen-Edinburg-Mission; Hidalgo County; Mission'], ['92516', 'zip', '78573; TX; McAllen-Edinburg-Mission; Hidalgo County; Alton'], ['92515', 'zip', '78572; TX; McAllen-Edinburg-Mission; Hidalgo County; Mission'], ['92514', 'zip', '78570; TX; McAllen-Edinburg-Mission; Hidalgo County; Mercedes'], ['92513', 'zip', '78569; TX; Raymondville; Willacy County; Lyford'], ['92512', 'zip', '78568; TX; Brownsville-Harlingen; Cameron County; Lozano'], ['92511', 'zip', '78567; TX; Brownsville-Harlingen; Cameron County; Los Indios'], ['92510', 'zip', '78566; TX; Brownsville-Harlingen; Cameron County; Los Fresnos'], ['92509', 'zip', '78565; TX; McAllen-Edinburg-Mission; Hidalgo County; Los Ebanos'], ['92508', 'zip', '78564; TX; Zapata; Zapata County; New Falcon'], ['92507', 'zip', '78563; TX; McAllen-Edinburg-Mission; Hidalgo County; Linn'], ['92506', 'zip', '78562; TX; McAllen-Edinburg-Mission; Hidalgo County; La Villa'], ['92505', 'zip', '78561; TX; Raymondville; Willacy County'], ['92504', 'zip', '78560; TX; McAllen-Edinburg-Mission; Hidalgo County; La Joya'], ['92503', 'zip', '78559; TX; Brownsville-Harlingen; Cameron County; La Feria'], ['92502', 'zip', '78558; TX; McAllen-Edinburg-Mission; Hidalgo County; Donna'], ['92501', 'zip', '78557; TX; McAllen-Edinburg-Mission; Hidalgo County; Hidalgo'], ['925', 'county', 'Cassia County; ID; Burley'], ['92499', 'zip', '78552; TX; Brownsville-Harlingen; Cameron County; Harlingen'], ['92497', 'zip', '78550; TX; Brownsville-Harlingen; Cameron County; Harlingen'], ['92496', 'zip', '78549; TX; McAllen-Edinburg-Mission; Hidalgo County; Hargill'], ['92495', 'zip', '78548; TX; Rio Grande City; Starr County'], ['92494', 'zip', '78547; TX; Rio Grande City; Starr County; Las Lomas'], ['92493', 'zip', '78545; TX; Rio Grande City; Starr County; Falcon Heights'], ['92492', 'zip', '78543; TX; McAllen-Edinburg-Mission; Hidalgo County; Elsa'], ['92491', 'zip', '78541; TX; McAllen-Edinburg-Mission; Hidalgo County; Edinburg'], ['92489', 'zip', '78539; TX; McAllen-Edinburg-Mission; Hidalgo County; Edinburg'], ['92488', 'zip', '78538; TX; McAllen-Edinburg-Mission; Hidalgo County; Edcouch'], ['92487', 'zip', '78537; TX; McAllen-Edinburg-Mission; Hidalgo County; Donna'], ['92486', 'zip', '78536; TX; Rio Grande City; Starr County'], ['92485', 'zip', '78535; TX; Brownsville-Harlingen; Cameron County; Combes'], ['92484', 'zip', '78526; TX; Brownsville-Harlingen; Cameron County; Brownsville'], ['92481', 'zip', '78521; TX; Brownsville-Harlingen; Cameron County; Brownsville'], ['92480', 'zip', '78520; TX; Brownsville-Harlingen; Cameron County; Brownsville'], ['92479', 'zip', '78516; TX; McAllen-Edinburg-Mission; Hidalgo County; Alamo'], ['92477', 'zip', '78504; TX; McAllen-Edinburg-Mission; Hidalgo County; McAllen'], ['92476', 'zip', '78503; TX; McAllen-Edinburg-Mission; Hidalgo County; McAllen'], ['92474', 'zip', '78501; TX; McAllen-Edinburg-Mission; Hidalgo County; McAllen'], ['92452', 'zip', '78418; TX; Corpus Christi; Nueces County'], ['92451', 'zip', '78417; TX; Corpus Christi; Nueces County'], ['92450', 'zip', '78416; TX; Corpus Christi; Nueces County'], ['92449', 'zip', '78415; TX; Corpus Christi; Nueces County'], ['92448', 'zip', '78414; TX; Corpus Christi; Nueces County'], ['92447', 'zip', '78413; TX; Corpus Christi; Nueces County'], ['92446', 'zip', '78412; TX; Corpus Christi; Nueces County'], ['92445', 'zip', '78411; TX; Corpus Christi; Nueces County'], ['92444', 'zip', '78410; TX; Corpus Christi; Nueces County'], ['92443', 'zip', '78409; TX; Corpus Christi; Nueces County'], ['92442', 'zip', '78408; TX; Corpus Christi; Nueces County'], ['92441', 'zip', '78407; TX; Corpus Christi; Nueces County'], ['92440', 'zip', '78406; TX; Corpus Christi; Nueces County'], ['92439', 'zip', '78405; TX; Corpus Christi; Nueces County'], ['92438', 'zip', '78404; TX; Corpus Christi; Nueces County'], ['92436', 'zip', '78402; TX; Corpus Christi; Nueces County'], ['92435', 'zip', '78401; TX; Corpus Christi; Nueces County'], ['92434', 'zip', '78393; TX; Refugio County; Woodsboro'], ['92433', 'zip', '78391; TX; Beeville; Bee County; Tynan'], ['92432', 'zip', '78390; TX; Corpus Christi; San Patricio County; Taft'], ['92431', 'zip', '78389; TX; Beeville; Bee County; Skidmore'], ['92430', 'zip', '78387; TX; Corpus Christi; San Patricio County; Sinton'], ['92428', 'zip', '78384; TX; Duval County; San Diego'], ['92427', 'zip', '78383; TX; Live Oak County; Sandia'], ['92426', 'zip', '78382; TX; Corpus Christi; Aransas County; Rockport'], ['92424', 'zip', '78380; TX; Corpus Christi; Nueces County; Robstown'], ['92423', 'zip', '78379; TX; Kingsville; Kleberg County; Riviera'], ['92422', 'zip', '78377; TX; Refugio County; Refugio'], ['92420', 'zip', '78375; TX; Alice; Jim Wells County; Premont'], ['92419', 'zip', '78374; TX; Corpus Christi; San Patricio County; Portland'], ['92418', 'zip', '78373; TX; Corpus Christi; Nueces County; Port Aransas'], ['92417', 'zip', '78372; TX; Alice; Jim Wells County; Orange Grove'], ['92416', 'zip', '78371; TX; Laredo; Webb County; Oilton'], ['92415', 'zip', '78370; TX; Corpus Christi; San Patricio County; Odem'], ['92414', 'zip', '78369; TX; Laredo; Webb County; Mirando City'], ['92413', 'zip', '78368; TX; Corpus Christi; San Patricio County; Mathis'], ['92411', 'zip', '78363; TX; Kingsville; Kleberg County'], ['92410', 'zip', '78362; TX; Corpus Christi; San Patricio County; Ingleside'], ['92409', 'zip', '78361; TX; Jim Hogg County; Hebbronville'], ['92407', 'zip', '78359; TX; Corpus Christi; San Patricio County; Gregory'], ['92406', 'zip', '78358; TX; Corpus Christi; Aransas County; Rockport'], ['92404', 'zip', '78355; TX; Brooks County; Falfurrias'], ['92403', 'zip', '78353; TX; Brooks County; Encino'], ['92402', 'zip', '78352; TX; Corpus Christi; San Patricio County; Odem'], ['92401', 'zip', '78351; TX; Corpus Christi; Nueces County; Driscoll'], ['9240', 'city', 'Stoneville; NC; Greensboro-High Point; Rockingham County'], ['924', 'county', 'Carver County; MN; Minneapolis-St. Paul-Bloomington'], ['92397', 'zip', '78344; TX; Laredo; Webb County; Bruni'], ['92396', 'zip', '78343; TX; Corpus Christi; Nueces County; Bishop'], ['92394', 'zip', '78341; TX; Duval County; San Diego'], ['92393', 'zip', '78340; TX; Refugio County; Bayside'], ['92392', 'zip', '78339; TX; Corpus Christi; Nueces County; Banquete'], ['92390', 'zip', '78336; TX; Corpus Christi; San Patricio County; Aransas Pass'], ['92389', 'zip', '78335; TX; Corpus Christi; San Patricio County; Aransas Pass'], ['92387', 'zip', '78332; TX; Alice; Jim Wells County'], ['92386', 'zip', '78330; TX; Corpus Christi; Nueces County; Agua Dulce'], ['9238', 'city', 'Stone Park; IL; Chicago-Naperville-Elgin; Cook County'], ['92362', 'zip', '78266; TX; San Antonio-New Braunfels; Comal County; Garden Ridge'], ['92360', 'zip', '78264; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92359', 'zip', '78263; TX; San Antonio-New Braunfels; Bexar County; China Grove'], ['92357', 'zip', '78261'], ['92356', 'zip', '78260'], ['92355', 'zip', '78259'], ['92354', 'zip', '78258'], ['92353', 'zip', '78257; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92352', 'zip', '78256; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92351', 'zip', '78255; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92350', 'zip', '78254'], ['92349', 'zip', '78253'], ['92348', 'zip', '78252; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92347', 'zip', '78251'], ['92346', 'zip', '78250'], ['92345', 'zip', '78249'], ['92344', 'zip', '78248; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92343', 'zip', '78247'], ['92341', 'zip', '78245'], ['92340', 'zip', '78244'], ['92338', 'zip', '78242; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92336', 'zip', '78240'], ['92335', 'zip', '78239'], ['92334', 'zip', '78238'], ['92333', 'zip', '78237; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92330', 'zip', '78234; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92329', 'zip', '78233; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92328', 'zip', '78232; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92327', 'zip', '78231; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92326', 'zip', '78230; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92325', 'zip', '78229'], ['92324', 'zip', '78228'], ['92323', 'zip', '78227'], ['92322', 'zip', '78226; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92321', 'zip', '78225; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92320', 'zip', '78224; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92319', 'zip', '78223; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92318', 'zip', '78222; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92317', 'zip', '78221; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92316', 'zip', '78220; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92315', 'zip', '78219'], ['92314', 'zip', '78218'], ['92313', 'zip', '78217'], ['92312', 'zip', '78216'], ['92311', 'zip', '78215; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92310', 'zip', '78214; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92309', 'zip', '78213; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92308', 'zip', '78212'], ['92307', 'zip', '78211; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92306', 'zip', '78210; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92305', 'zip', '78209'], ['92304', 'zip', '78208; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92303', 'zip', '78207; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92301', 'zip', '78205; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92300', 'zip', '78204; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['923', 'county', 'Carter County; TN; Johnson City'], ['92299', 'zip', '78203; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92298', 'zip', '78202; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92297', 'zip', '78201'], ['92296', 'zip', '78164; TX; De Witt County; Yorktown'], ['92295', 'zip', '78163; TX; San Antonio-New Braunfels; Comal County; Bulverde'], ['92294', 'zip', '78162; TX; Beeville; Bee County'], ['92293', 'zip', '78161; TX; San Antonio-New Braunfels; Wilson County; Sutherland Springs'], ['92292', 'zip', '78160; TX; San Antonio-New Braunfels; Wilson County; Stockdale'], ['92291', 'zip', '78159; TX; Gonzales County; Smiley'], ['92289', 'zip', '78155; TX; San Antonio-New Braunfels; Guadalupe County; Seguin'], ['92288', 'zip', '78154'], ['92287', 'zip', '78152; TX; San Antonio-New Braunfels; Bexar County; Saint Hedwig'], ['92286', 'zip', '78151; TX; Karnes County; Runge'], ['92284', 'zip', '78148'], ['92283', 'zip', '78147; TX; San Antonio-New Braunfels; Wilson County; Poth'], ['92282', 'zip', '78146; TX; Beeville; Bee County; Pettus'], ['92280', 'zip', '78144; TX; Karnes County; Karnes City'], ['9228', 'city', 'Whitsett; NC; Greensboro-High Point; Guilford County'], ['92279', 'zip', '78143; TX; San Antonio-New Braunfels; Wilson County; Stockdale'], ['92278', 'zip', '78142; TX; Beeville; Bee County; Normanna'], ['92277', 'zip', '78141; TX; De Witt County; Nordheim'], ['92276', 'zip', '78140; TX; Gonzales County; Nixon'], ['92274', 'zip', '78133; TX; San Antonio-New Braunfels; Comal County; Canyon Lake'], ['92273', 'zip', '78132; TX; San Antonio-New Braunfels; Comal County; New Braunfels'], ['92271', 'zip', '78130'], ['92269', 'zip', '78124; TX; San Antonio-New Braunfels; Guadalupe County; Santa Clara'], ['92268', 'zip', '78123; TX; San Antonio-New Braunfels; Guadalupe County; McQueeney'], ['92267', 'zip', '78122; TX; Gonzales County; Leesville'], ['92266', 'zip', '78121; TX; San Antonio-New Braunfels; Wilson County; La Vernia'], ['92265', 'zip', '78119; TX; Karnes County; Kenedy'], ['92264', 'zip', '78118; TX; Karnes County; Karnes City'], ['92263', 'zip', '78117; TX; Karnes County; Hobson'], ['92262', 'zip', '78116; TX; Karnes County; Gillett'], ['92260', 'zip', '78114; TX; San Antonio-New Braunfels; Wilson County; Floresville'], ['92259', 'zip', '78113; TX; Karnes County; Falls City'], ['92258', 'zip', '78112; TX; San Antonio-New Braunfels; Bexar County; Elmendorf'], ['92256', 'zip', '78109'], ['92255', 'zip', '78108'], ['92253', 'zip', '78104; TX; Beeville; Bee County'], ['92252', 'zip', '78102; TX; Beeville; Bee County'], ['92251', 'zip', '78101; TX; San Antonio-New Braunfels; Bexar County; Adkins'], ['92250', 'zip', '78076; TX; Zapata; Zapata County'], ['9225', 'city', 'Whiting; ME; Washington County'], ['92249', 'zip', '78075; TX; Live Oak County; Whitsett'], ['92247', 'zip', '78073; TX; San Antonio-New Braunfels; Bexar County; San Antonio'], ['92245', 'zip', '78071; TX; Live Oak County; Three Rivers'], ['92244', 'zip', '78070; TX; San Antonio-New Braunfels; Comal County; Spring Branch'], ['92243', 'zip', '78069; TX; San Antonio-New Braunfels; Atascosa County; Somerset'], ['92242', 'zip', '78067; TX; Zapata; Zapata County; San Ygnacio'], ['92241', 'zip', '78066; TX; San Antonio-New Braunfels; Medina County; Riomedina'], ['92240', 'zip', '78065; TX; San Antonio-New Braunfels; Atascosa County; Poteet'], ['9224', 'city', 'Whiting; IA; Monona County'], ['92239', 'zip', '78064; TX; San Antonio-New Braunfels; Atascosa County; Pleasanton'], ['92238', 'zip', '78063; TX; San Antonio-New Braunfels; Bandera County; Pipe Creek'], ['92236', 'zip', '78061; TX; Frio County; Pearsall'], ['92235', 'zip', '78060; TX; Live Oak County; Three Rivers'], ['92234', 'zip', '78059; TX; San Antonio-New Braunfels; Medina County; Natalia'], ['92233', 'zip', '78058; TX; Kerrville; Kerr County; Mountain Home'], ['92232', 'zip', '78057; TX; Frio County; Moore'], ['92231', 'zip', '78056; TX; San Antonio-New Braunfels; Medina County; Mico'], ['92230', 'zip', '78055; TX; San Antonio-New Braunfels; Bandera County; Medina'], ['92228', 'zip', '78052; TX; San Antonio-New Braunfels; Atascosa County; Lytle'], ['92227', 'zip', '78050; TX; San Antonio-New Braunfels; Atascosa County; Pleasanton'], ['92225', 'zip', '78046; TX; Laredo; Webb County'], ['92224', 'zip', '78045; TX; Laredo; Webb County'], ['92222', 'zip', '78043; TX; Laredo; Webb County'], ['92220', 'zip', '78041; TX; Laredo; Webb County'], ['92219', 'zip', '78040; TX; Laredo; Webb County'], ['92218', 'zip', '78039; TX; San Antonio-New Braunfels; Medina County; La Coste'], ['92216', 'zip', '78028; TX; Kerrville; Kerr County'], ['92215', 'zip', '78027; TX; San Antonio-New Braunfels; Kendall County; Kendalia'], ['92214', 'zip', '78026; TX; San Antonio-New Braunfels; Atascosa County; Jourdanton'], ['92213', 'zip', '78025; TX; Kerrville; Kerr County; Ingram'], ['92212', 'zip', '78024; TX; Kerrville; Kerr County; Hunt'], ['92211', 'zip', '78023; TX; San Antonio-New Braunfels; Bexar County; Helotes'], ['92210', 'zip', '78022; TX; Live Oak County; George West'], ['92208', 'zip', '78019; TX; La Salle County; Encinal'], ['92207', 'zip', '78017; TX; Frio County; Dilley'], ['92206', 'zip', '78016; TX; San Antonio-New Braunfels; Medina County; Devine'], ['92205', 'zip', '78015; TX; San Antonio-New Braunfels; Kendall County; Boerne'], ['92203', 'zip', '78013; TX; San Antonio-New Braunfels; Kendall County; Comfort'], ['92202', 'zip', '78012; TX; San Antonio-New Braunfels; Atascosa County; Pleasanton'], ['92201', 'zip', '78011; TX; San Antonio-New Braunfels; Atascosa County; Charlotte'], ['92200', 'zip', '78010; TX; Kerrville; Kerr County; Center Point'], ['922', 'county', 'Carter County; OK; Ardmore'], ['92199', 'zip', '78009; TX; San Antonio-New Braunfels; Medina County; Castroville'], ['92198', 'zip', '78008; TX; San Antonio-New Braunfels; Atascosa County; Campbellton'], ['92196', 'zip', '78006'], ['92195', 'zip', '78005; TX; Frio County; Bigfoot'], ['92194', 'zip', '78004; TX; San Antonio-New Braunfels; Kendall County; Bergheim'], ['92193', 'zip', '78003; TX; San Antonio-New Braunfels; Bandera County; Bandera'], ['92192', 'zip', '78002; TX; San Antonio-New Braunfels; Bexar County; Atascosa'], ['92190', 'zip', '77995; TX; Lavaca County; Yoakum'], ['92189', 'zip', '77994; TX; De Witt County; Westhoff'], ['92187', 'zip', '77991; TX; Jackson County; Vanderbilt'], ['92186', 'zip', '77990; TX; Refugio County; Tivoli'], ['92185', 'zip', '77989; TX; De Witt County; Cuero'], ['92184', 'zip', '77988; TX; Victoria; Victoria County'], ['92183', 'zip', '77987; TX; Lavaca County; Yoakum'], ['92182', 'zip', '77986; TX; Lavaca County; Hallettsville'], ['92181', 'zip', '77984; TX; Lavaca County; Shiner'], ['92180', 'zip', '77983; TX; Port Lavaca; Calhoun County; Seadrift'], ['92179', 'zip', '77982; TX; Port Lavaca; Calhoun County; Port O Connor'], ['92178', 'zip', '77979; TX; Port Lavaca; Calhoun County'], ['92177', 'zip', '77978; TX; Port Lavaca; Calhoun County; Point Comfort'], ['92176', 'zip', '77977; TX; Victoria; Victoria County'], ['92175', 'zip', '77976; TX; Victoria; Victoria County'], ['92174', 'zip', '77975; TX; Lavaca County; Moulton'], ['92173', 'zip', '77974; TX; Victoria; Victoria County; Meyersville'], ['92171', 'zip', '77971; TX; Jackson County; Lolita'], ['92169', 'zip', '77969; TX; Jackson County; Edna'], ['92168', 'zip', '77968; TX; Victoria; Victoria County; Inez'], ['92166', 'zip', '77964; TX; Lavaca County; Hallettsville'], ['92165', 'zip', '77963; TX; Victoria; Goliad County; Goliad'], ['92164', 'zip', '77962; TX; Jackson County; Ganado'], ['92163', 'zip', '77961; TX; Bay City; Matagorda County; Palacios'], ['92162', 'zip', '77960; TX; Victoria; Victoria County'], ['92161', 'zip', '77957; TX; Jackson County; Edna'], ['92160', 'zip', '77954; TX; De Witt County; Cuero'], ['92159', 'zip', '77951; TX; Victoria; Victoria County'], ['92158', 'zip', '77950; TX; Refugio County; Austwell'], ['92157', 'zip', '77905; TX; Victoria; Victoria County'], ['92156', 'zip', '77904; TX; Victoria; Victoria County'], ['92153', 'zip', '77901; TX; Victoria; Victoria County'], ['92152', 'zip', '77882; TX; College Station-Bryan; Robertson County; Hearne'], ['92150', 'zip', '77880; TX; Brenham; Washington County; Washington'], ['9215', 'city', 'Star City; AR; Pine Bluff; Lincoln County'], ['92149', 'zip', '77879; TX; College Station-Bryan; Burleson County; Somerville'], ['92148', 'zip', '77878; TX; College Station-Bryan; Burleson County; Somerville'], ['92147', 'zip', '77876; TX; Grimes County; Richards'], ['92145', 'zip', '77873; TX; Grimes County; Richards'], ['92144', 'zip', '77872; TX; Madison County; North Zulch'], ['92143', 'zip', '77871; TX; Leon County; Normangee'], ['92140', 'zip', '77868; TX; Grimes County; Navasota'], ['92137', 'zip', '77865; TX; Leon County; Marquez'], ['92136', 'zip', '77864; TX; Madison County; Madisonville'], ['92135', 'zip', '77863; TX; College Station-Bryan; Burleson County; Somerville'], ['92133', 'zip', '77861; TX; Grimes County; Iola'], ['92132', 'zip', '77859; TX; College Station-Bryan; Robertson County; Hearne'], ['92131', 'zip', '77857; TX; Milam County; Cameron'], ['92130', 'zip', '77856; TX; College Station-Bryan; Robertson County; Franklin'], ['9213', 'city', 'Stanwood; IA; Cedar County'], ['92129', 'zip', '77855; TX; Leon County; Normangee'], ['92128', 'zip', '77853; TX; Lee County; Dime Box'], ['92127', 'zip', '77852; TX; College Station-Bryan; Burleson County; Caldwell'], ['92125', 'zip', '77845; TX; College Station-Bryan; Brazos County; College Station'], ['92120', 'zip', '77840; TX; College Station-Bryan; Brazos County; College Station'], ['9212', 'city', 'Saltillo; TN; Hardin County'], ['92119', 'zip', '77838; TX; College Station-Bryan; Burleson County; Caldwell'], ['92118', 'zip', '77837; TX; College Station-Bryan; Robertson County; Calvert'], ['92117', 'zip', '77836; TX; College Station-Bryan; Burleson County; Caldwell'], ['92116', 'zip', '77835; TX; Brenham; Washington County; Burton'], ['92114', 'zip', '77833; TX; Brenham; Washington County'], ['92113', 'zip', '77831; TX; Grimes County; Bedias'], ['92112', 'zip', '77830; TX; Grimes County; Anderson'], ['92111', 'zip', '77808; TX; College Station-Bryan; Brazos County; Bryan'], ['92110', 'zip', '77807; TX; College Station-Bryan; Brazos County; Bryan'], ['92107', 'zip', '77803; TX; College Station-Bryan; Brazos County; Bryan'], ['92106', 'zip', '77802; TX; College Station-Bryan; Brazos County; Bryan'], ['92105', 'zip', '77801; TX; College Station-Bryan; Brazos County; Bryan'], ['92101', 'zip', '77713; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['9210', 'city', 'Salley; SC; Augusta-Richmond County; Aiken County'], ['92098', 'zip', '77708; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92097', 'zip', '77707; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92096', 'zip', '77706; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92095', 'zip', '77705; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92093', 'zip', '77703; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92092', 'zip', '77702; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['92091', 'zip', '77701; TX; Beaumont-Port Arthur; Jefferson County; Beaumont'], ['9209', 'city', 'Salix; IA; Sioux City; Woodbury County'], ['92089', 'zip', '77665; TX; Houston-The Woodlands-Sugar Land; Chambers County; Winnie'], ['92088', 'zip', '77664; TX; Tyler County; Warren'], ['92087', 'zip', '77663; TX; Beaumont-Port Arthur; Hardin County; Kountze'], ['92086', 'zip', '77662; TX; Beaumont-Port Arthur; Orange County; Vidor'], ['92085', 'zip', '77661; TX; Houston-The Woodlands-Sugar Land; Chambers County; Winnie'], ['92084', 'zip', '77660; TX; Tyler County; Spurger'], ['92083', 'zip', '77659; TX; Beaumont-Port Arthur; Hardin County; Sour Lake'], ['92082', 'zip', '77657; TX; Beaumont-Port Arthur; Hardin County; Lumberton'], ['92081', 'zip', '77656; TX; Beaumont-Port Arthur; Hardin County; Silsbee'], ['92080', 'zip', '77655; TX; Beaumont-Port Arthur; Jefferson County; Sabine Pass'], ['9208', 'city', 'Salida; CA; Modesto; Stanislaus County'], ['92079', 'zip', '77651; TX; Beaumont-Port Arthur; Jefferson County; Port Neches'], ['92078', 'zip', '77650; TX; Houston-The Woodlands-Sugar Land; Galveston County; Port Bolivar'], ['92076', 'zip', '77642; TX; Beaumont-Port Arthur; Jefferson County; Port Arthur'], ['92074', 'zip', '77640; TX; Beaumont-Port Arthur; Jefferson County; Port Arthur'], ['92072', 'zip', '77632; TX; Beaumont-Port Arthur; Orange County; Mauriceville'], ['92070', 'zip', '77630; TX; Beaumont-Port Arthur; Orange County; Orange'], ['9207', 'city', 'Whiteside; MO; St. Louis; Lincoln County'], ['92069', 'zip', '77629; TX; Beaumont-Port Arthur; Jefferson County; Nome'], ['92068', 'zip', '77627; TX; Beaumont-Port Arthur; Jefferson County; Nederland'], ['92066', 'zip', '77625; TX; Beaumont-Port Arthur; Hardin County; Kountze'], ['92065', 'zip', '77624; TX; Tyler County; Woodville'], ['92064', 'zip', '77623; TX; Houston-The Woodlands-Sugar Land; Galveston County; High Island'], ['92063', 'zip', '77622; TX; Beaumont-Port Arthur; Jefferson County; Hamshire'], ['92062', 'zip', '77619; TX; Beaumont-Port Arthur; Jefferson County; Groves'], ['92061', 'zip', '77617; TX; Houston-The Woodlands-Sugar Land; Galveston County; Gilchrist'], ['92060', 'zip', '77616; TX; Tyler County; Fred'], ['9206', 'city', 'Whitesboro; NY; Utica-Rome; Oneida County'], ['92059', 'zip', '77615; TX; Beaumont-Port Arthur; Hardin County; Silsbee'], ['92058', 'zip', '77614; TX; Beaumont-Port Arthur; Newton County; Deweyville'], ['92057', 'zip', '77613; TX; Beaumont-Port Arthur; Jefferson County; China'], ['92056', 'zip', '77612; TX; Jasper County; Buna'], ['92055', 'zip', '77611; TX; Beaumont-Port Arthur; Orange County; Bridge City'], ['92054', 'zip', '77598'], ['92053', 'zip', '77597; TX; Houston-The Woodlands-Sugar Land; Chambers County; Wallisville'], ['92051', 'zip', '77591; TX; Houston-The Woodlands-Sugar Land; Galveston County; Texas City'], ['92050', 'zip', '77590; TX; Houston-The Woodlands-Sugar Land; Galveston County; Texas City'], ['92048', 'zip', '77587; TX; Houston-The Woodlands-Sugar Land; Harris County; South Houston'], ['92047', 'zip', '77586'], ['92046', 'zip', '77585; TX; Beaumont-Port Arthur; Hardin County; Saratoga'], ['92045', 'zip', '77584'], ['92044', 'zip', '77583; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Arcola'], ['92043', 'zip', '77582; TX; Houston-The Woodlands-Sugar Land; Liberty County; Devers'], ['92042', 'zip', '77581; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Pearland'], ['92041', 'zip', '77580; TX; Houston-The Woodlands-Sugar Land; Chambers County; Mont Belvieu'], ['92040', 'zip', '77578; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Manvel'], ['9204', 'neigh', 'Whites Creek; TN; Nashville-Davidson--Murfreesboro--Franklin; Davidson County; Nashville'], ['92039', 'zip', '77577; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Liverpool'], ['92038', 'zip', '77575; TX; Houston-The Woodlands-Sugar Land; Liberty County; Liberty'], ['92036', 'zip', '77573'], ['92034', 'zip', '77571; TX; Houston-The Woodlands-Sugar Land; Harris County; La Porte'], ['92033', 'zip', '77568; TX; Houston-The Woodlands-Sugar Land; Galveston County; La Marque'], ['92032', 'zip', '77566; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Lake Jackson'], ['92031', 'zip', '77565; TX; Houston-The Woodlands-Sugar Land; Galveston County; League City'], ['92030', 'zip', '77564; TX; Beaumont-Port Arthur; Hardin County; Batson'], ['9203', 'city', 'White Plains; VA; Brunswick County'], ['92029', 'zip', '77563; TX; Houston-The Woodlands-Sugar Land; Galveston County; Hitchcock'], ['92028', 'zip', '77562; TX; Houston-The Woodlands-Sugar Land; Harris County; Highlands'], ['92027', 'zip', '77561; TX; Houston-The Woodlands-Sugar Land; Liberty County; Hardin'], ['92026', 'zip', '77560; TX; Houston-The Woodlands-Sugar Land; Chambers County; Wallisville'], ['92024', 'zip', '77554; TX; Houston-The Woodlands-Sugar Land; Galveston County; Galveston'], ['92021', 'zip', '77551; TX; Houston-The Woodlands-Sugar Land; Galveston County; Galveston'], ['92020', 'zip', '77550'], ['92018', 'zip', '77547; TX; Houston-The Woodlands-Sugar Land; Harris County; Galena Park'], ['92017', 'zip', '77546'], ['92016', 'zip', '77545; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Fresno'], ['92014', 'zip', '77541; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Freeport'], ['92013', 'zip', '77539; TX; Houston-The Woodlands-Sugar Land; Galveston County; Dickinson'], ['92012', 'zip', '77538; TX; Houston-The Woodlands-Sugar Land; Liberty County; Devers'], ['92011', 'zip', '77536; TX; Houston-The Woodlands-Sugar Land; Harris County; Deer Park'], ['92010', 'zip', '77535; TX; Houston-The Woodlands-Sugar Land; Liberty County; Dayton'], ['92009', 'zip', '77534; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Danbury'], ['92008', 'zip', '77533; TX; Houston-The Woodlands-Sugar Land; Liberty County; Daisetta'], ['92007', 'zip', '77532; TX; Houston-The Woodlands-Sugar Land; Harris County; Crosby'], ['92006', 'zip', '77531; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Clute'], ['92005', 'zip', '77530; TX; Houston-The Woodlands-Sugar Land; Harris County; Channelview'], ['92003', 'zip', '77521; TX; Houston-The Woodlands-Sugar Land; Harris County; Baytown'], ['92002', 'zip', '77520; TX; Houston-The Woodlands-Sugar Land; Harris County; Baytown'], ['92001', 'zip', '77519; TX; Beaumont-Port Arthur; Hardin County; Batson'], ['92000', 'zip', '77518; TX; Houston-The Woodlands-Sugar Land; Galveston County; Bacliff'], ['92', 'county', 'Sac County; IA'], ['91999', 'zip', '77517; TX; Houston-The Woodlands-Sugar Land; Galveston County; Santa Fe'], ['91997', 'zip', '77515; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Angleton'], ['91996', 'zip', '77514; TX; Houston-The Woodlands-Sugar Land; Chambers County; Anahuac'], ['91994', 'zip', '77511; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Alvin'], ['91993', 'zip', '77510; TX; Houston-The Woodlands-Sugar Land; Galveston County; Santa Fe'], ['91990', 'zip', '77506; TX; Houston-The Woodlands-Sugar Land; Harris County; Pasadena'], ['91989', 'zip', '77505; TX; Houston-The Woodlands-Sugar Land; Harris County; Pasadena'], ['91988', 'zip', '77504; TX; Houston-The Woodlands-Sugar Land; Harris County; Pasadena'], ['91987', 'zip', '77503; TX; Houston-The Woodlands-Sugar Land; Harris County; Pasadena'], ['91986', 'zip', '77502; TX; Houston-The Woodlands-Sugar Land; Harris County; Pasadena'], ['91982', 'zip', '77494'], ['91981', 'zip', '77493'], ['91978', 'zip', '77489; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Missouri City'], ['91977', 'zip', '77488; TX; El Campo; Wharton County; Wharton'], ['91975', 'zip', '77486; TX; Houston-The Woodlands-Sugar Land; Brazoria County; West Columbia'], ['91974', 'zip', '77485; TX; Houston-The Woodlands-Sugar Land; Austin County; Wallis'], ['91973', 'zip', '77484; TX; Houston-The Woodlands-Sugar Land; Waller County; Waller'], ['91972', 'zip', '77483; TX; Bay City; Matagorda County'], ['91971', 'zip', '77482; TX; Bay City; Matagorda County; Van Vleck'], ['91970', 'zip', '77481; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Thompsons'], ['91969', 'zip', '77480; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Sweeny'], ['91968', 'zip', '77479'], ['91967', 'zip', '77478'], ['91966', 'zip', '77477; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Stafford'], ['91965', 'zip', '77476; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Fulshear'], ['91964', 'zip', '77475; TX; Lavaca County; Hallettsville'], ['91963', 'zip', '77474; TX; Houston-The Woodlands-Sugar Land; Austin County; Sealy'], ['91962', 'zip', '77473; TX; Houston-The Woodlands-Sugar Land; Austin County; San Felipe'], ['91961', 'zip', '77471; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Rosenberg'], ['91960', 'zip', '77470; TX; Colorado County; Columbus'], ['9196', 'city', 'Wheatcroft; KY; Webster County'], ['91959', 'zip', '77469'], ['91958', 'zip', '77468; TX; El Campo; Wharton County; Boling'], ['91956', 'zip', '77466; TX; Houston-The Woodlands-Sugar Land; Waller County; Pattison'], ['91955', 'zip', '77465; TX; Bay City; Matagorda County; Palacios'], ['91954', 'zip', '77464; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Orchard'], ['91952', 'zip', '77461; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Needville'], ['91951', 'zip', '77460; TX; El Campo; Wharton County; Wharton'], ['91950', 'zip', '77459'], ['9195', 'city', 'Wharton; NJ; New York-Newark-Jersey City; Morris County'], ['91949', 'zip', '77458; TX; Bay City; Matagorda County; Midfield'], ['91948', 'zip', '77457; TX; Bay City; Matagorda County; Matagorda'], ['91947', 'zip', '77456; TX; Bay City; Matagorda County; Markham'], ['91946', 'zip', '77455; TX; El Campo; Wharton County; Louise'], ['91945', 'zip', '77454; TX; El Campo; Wharton County; Lissie'], ['91944', 'zip', '77453; TX; El Campo; Wharton County; Wharton'], ['91943', 'zip', '77452; TX; Houston-The Woodlands-Sugar Land; Austin County; Bellville'], ['91942', 'zip', '77451; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Beasley'], ['91941', 'zip', '77450'], ['91940', 'zip', '77449'], ['91939', 'zip', '77448; TX; El Campo; Wharton County; East Bernard'], ['91938', 'zip', '77447; TX; Houston-The Woodlands-Sugar Land; Harris County; Hockley'], ['91937', 'zip', '77446; TX; Houston-The Woodlands-Sugar Land; Waller County; Prairie View'], ['91936', 'zip', '77445; TX; Houston-The Woodlands-Sugar Land; Waller County; Hempstead'], ['91935', 'zip', '77444; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Guy'], ['91934', 'zip', '77443; TX; El Campo; Wharton County; Wharton'], ['91933', 'zip', '77442; TX; Colorado County; Garwood'], ['91932', 'zip', '77441; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Fulshear'], ['91931', 'zip', '77440; TX; Bay City; Matagorda County; Palacios'], ['91930', 'zip', '77437; TX; El Campo; Wharton County'], ['91928', 'zip', '77435; TX; El Campo; Wharton County; East Bernard'], ['91927', 'zip', '77434; TX; Colorado County; Eagle Lake'], ['91926', 'zip', '77433'], ['91925', 'zip', '77432; TX; El Campo; Wharton County; Danevang'], ['91923', 'zip', '77430; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Damon'], ['91922', 'zip', '77429'], ['91921', 'zip', '77428; TX; Bay City; Matagorda County; Palacios'], ['91920', 'zip', '77426; TX; Brenham; Washington County; Chappell Hill'], ['91919', 'zip', '77423; TX; Houston-The Woodlands-Sugar Land; Waller County; Brookshire'], ['91918', 'zip', '77422; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Brazoria'], ['91917', 'zip', '77420; TX; El Campo; Wharton County; Boling'], ['91916', 'zip', '77419; TX; Bay City; Matagorda County; Blessing'], ['91915', 'zip', '77418; TX; Houston-The Woodlands-Sugar Land; Austin County; Bellville'], ['91914', 'zip', '77417; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Beasley'], ['91913', 'zip', '77415; TX; Houston-The Woodlands-Sugar Land; Brazoria County; Sweeny'], ['91912', 'zip', '77414; TX; Bay City; Matagorda County'], ['91910', 'zip', '77412; TX; Colorado County; Columbus'], ['91907', 'zip', '77406; TX; Houston-The Woodlands-Sugar Land; Fort Bend County; Richmond'], ['91904', 'zip', '77401; TX; Houston-The Woodlands-Sugar Land; Harris County; Bellaire'], ['91903', 'zip', '77399; TX; Polk County; Corrigan'], ['91902', 'zip', '77396'], ['9190', 'city', 'Squires; MO; Ozark County'], ['919', 'county', 'Carter County; KY'], ['91899', 'zip', '77389; TX; Houston-The Woodlands-Sugar Land; Montgomery County; The Woodlands'], ['91898', 'zip', '77388; TX; Houston-The Woodlands-Sugar Land; Harris County; Spring'], ['91896', 'zip', '77386'], ['91895', 'zip', '77385'], ['91894', 'zip', '77384'], ['91892', 'zip', '77382'], ['91891', 'zip', '77381'], ['91890', 'zip', '77380'], ['9189', 'city', 'Spruce Pine; AL; Franklin County'], ['91889', 'zip', '77379'], ['91888', 'zip', '77378; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Willis'], ['91887', 'zip', '77377'], ['91886', 'zip', '77376; TX; Beaumont-Port Arthur; Hardin County; Votaw'], ['91885', 'zip', '77375'], ['91884', 'zip', '77374; TX; Beaumont-Port Arthur; Hardin County; Thicket'], ['91883', 'zip', '77373'], ['91882', 'zip', '77372; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Splendora'], ['91881', 'zip', '77371; TX; San Jacinto County; Shepherd'], ['91880', 'zip', '77369; TX; Houston-The Woodlands-Sugar Land; Liberty County; Rye'], ['91879', 'zip', '77368; TX; Houston-The Woodlands-Sugar Land; Liberty County; Rye'], ['91878', 'zip', '77367; TX; Huntsville; Walker County; Riverside'], ['91877', 'zip', '77365; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Porter'], ['91876', 'zip', '77364; TX; San Jacinto County; Point Blank'], ['91875', 'zip', '77363; TX; Grimes County; Todd Mission'], ['91874', 'zip', '77362; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Pinehurst'], ['91873', 'zip', '77360; TX; Polk County; Onalaska'], ['91872', 'zip', '77359; TX; San Jacinto County; Oakhurst'], ['91871', 'zip', '77358; TX; Huntsville; Walker County; New Waverly'], ['91870', 'zip', '77357; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Roman Forest'], ['9187', 'city', 'Springville; IN; Bedford; Lawrence County'], ['91869', 'zip', '77356'], ['91868', 'zip', '77355; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Stagecoach'], ['91867', 'zip', '77354'], ['91865', 'zip', '77351; TX; Polk County; West Livingston'], ['91864', 'zip', '77350; TX; Polk County; Leggett'], ['91860', 'zip', '77346'], ['91859', 'zip', '77345'], ['91854', 'zip', '77340; TX; Huntsville; Walker County'], ['91853', 'zip', '77339'], ['91852', 'zip', '77338; TX; Houston-The Woodlands-Sugar Land; Harris County; Humble'], ['91850', 'zip', '77336; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91849', 'zip', '77335; TX; Polk County; Goodrich'], ['91848', 'zip', '77334; TX; Huntsville; Walker County'], ['91846', 'zip', '77332; TX; Polk County; Goodrich'], ['91845', 'zip', '77331; TX; San Jacinto County; Coldspring'], ['91844', 'zip', '77328; TX; Houston-The Woodlands-Sugar Land; Liberty County; Cleveland'], ['91843', 'zip', '77327; TX; Houston-The Woodlands-Sugar Land; Liberty County; Cleveland'], ['91842', 'zip', '77326; TX; San Jacinto County; Shepherd'], ['91840', 'zip', '77320; TX; Huntsville; Walker County'], ['91839', 'zip', '77318; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Willis'], ['91838', 'zip', '77316; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Montgomery'], ['91836', 'zip', '77306; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Conroe'], ['91834', 'zip', '77304'], ['91833', 'zip', '77303; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Conroe'], ['91832', 'zip', '77302; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Conroe'], ['91831', 'zip', '77301; TX; Houston-The Woodlands-Sugar Land; Montgomery County; Conroe'], ['9183', 'city', 'Monroe; SD; Sioux Falls; Turner County'], ['9182', 'city', 'Monroe; IN; Decatur; Adams County'], ['9181', 'city', 'Monmouth Junction; NJ; New York-Newark-Jersey City; Middlesex County'], ['918', 'county', 'Carson County; TX; Amarillo'], ['9178', 'city', 'Mohawk; MI; Houghton; Keweenaw County'], ['9177', 'city', 'Mohall; ND; Minot; Renville County'], ['91748', 'zip', '77099'], ['91747', 'zip', '77098'], ['91745', 'zip', '77096'], ['91744', 'zip', '77095'], ['91743', 'zip', '77094'], ['91742', 'zip', '77093; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91741', 'zip', '77092'], ['91740', 'zip', '77091; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['9174', 'city', 'Moclips; WA; Aberdeen; Grays Harbor County'], ['91739', 'zip', '77090'], ['91738', 'zip', '77089'], ['91737', 'zip', '77088'], ['91736', 'zip', '77087; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91735', 'zip', '77086; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91734', 'zip', '77085; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91733', 'zip', '77084'], ['91732', 'zip', '77083'], ['91731', 'zip', '77082'], ['91730', 'zip', '77081; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91729', 'zip', '77080; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91728', 'zip', '77079'], ['91727', 'zip', '77078; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91726', 'zip', '77077'], ['91725', 'zip', '77076; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91724', 'zip', '77075; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91723', 'zip', '77074'], ['91722', 'zip', '77073'], ['91721', 'zip', '77072'], ['91720', 'zip', '77071; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91719', 'zip', '77070'], ['91718', 'zip', '77069; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91717', 'zip', '77068; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91716', 'zip', '77067; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91715', 'zip', '77066'], ['91714', 'zip', '77065'], ['91713', 'zip', '77064'], ['91712', 'zip', '77063'], ['91711', 'zip', '77062; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91710', 'zip', '77061; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91709', 'zip', '77060; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91708', 'zip', '77059; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91707', 'zip', '77058; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91706', 'zip', '77057'], ['91705', 'zip', '77056'], ['91704', 'zip', '77055; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91703', 'zip', '77054'], ['91702', 'zip', '77053; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91700', 'zip', '77051; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['917', 'county', 'Carbon County; WY'], ['91699', 'zip', '77050; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91698', 'zip', '77049; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91697', 'zip', '77048; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91696', 'zip', '77047'], ['91695', 'zip', '77046; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91694', 'zip', '77045; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91693', 'zip', '77044; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91692', 'zip', '77043; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91691', 'zip', '77042; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91690', 'zip', '77041'], ['9169', 'city', 'Prairie View; TX; Houston-The Woodlands-Sugar Land; Waller County'], ['91689', 'zip', '77040'], ['91688', 'zip', '77039; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91687', 'zip', '77038; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91686', 'zip', '77037; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91685', 'zip', '77036; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91684', 'zip', '77035'], ['91683', 'zip', '77034; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91682', 'zip', '77033; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91681', 'zip', '77032; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91680', 'zip', '77031; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91679', 'zip', '77030'], ['91678', 'zip', '77029; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91677', 'zip', '77028; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91676', 'zip', '77027; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91675', 'zip', '77026; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91674', 'zip', '77025'], ['91673', 'zip', '77024; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91672', 'zip', '77023; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91671', 'zip', '77022; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91670', 'zip', '77021; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91669', 'zip', '77020; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91668', 'zip', '77019'], ['91667', 'zip', '77018'], ['91666', 'zip', '77017; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91665', 'zip', '77016; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91664', 'zip', '77015; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91663', 'zip', '77014'], ['91662', 'zip', '77013; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91661', 'zip', '77012; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91660', 'zip', '77011; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['9166', 'city', 'Power; MT; Teton County'], ['91659', 'zip', '77010; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['91658', 'zip', '77009'], ['91657', 'zip', '77008'], ['91656', 'zip', '77007'], ['91655', 'zip', '77006'], ['91654', 'zip', '77005'], ['91653', 'zip', '77004'], ['91652', 'zip', '77003'], ['91651', 'zip', '77002; TX; Houston-The Woodlands-Sugar Land; Harris County; Houston'], ['9165', 'city', 'Powderly; TX; Paris; Lamar County'], ['91649', 'zip', '76958; TX; San Angelo; Tom Green County; Water Valley'], ['91648', 'zip', '76957; TX; San Angelo; Tom Green County; Wall'], ['91647', 'zip', '76955; TX; Concho County; Eola'], ['91642', 'zip', '76945; TX; Coke County; Robert Lee'], ['91641', 'zip', '76943; TX; Crockett County; Ozona'], ['91639', 'zip', '76940; TX; San Angelo; Tom Green County; Mereta'], ['91637', 'zip', '76937; TX; Concho County; Eola'], ['91635', 'zip', '76935; TX; San Angelo; Tom Green County; Christoval'], ['91634', 'zip', '76934; TX; San Angelo; Tom Green County; Carlsbad'], ['91633', 'zip', '76933; TX; Coke County; Bronte'], ['91632', 'zip', '76932; TX; Reagan County; Big Lake'], ['9163', 'city', 'Old Mill Creek; IL; Chicago-Naperville-Elgin; Lake County'], ['91627', 'zip', '76905; TX; San Angelo; Tom Green County'], ['91626', 'zip', '76904; TX; San Angelo; Tom Green County'], ['91625', 'zip', '76903; TX; San Angelo; Tom Green County'], ['91623', 'zip', '76901; TX; San Angelo; Tom Green County'], ['91622', 'zip', '76890; TX; Brownwood; Brown County; Zephyr'], ['91621', 'zip', '76888; TX; Coleman County; Voss'], ['9162', 'city', 'Oil City; PA; Venango County'], ['91618', 'zip', '76885; TX; Llano County; Valley Spring'], ['91617', 'zip', '76884; TX; Coleman County; Talpa'], ['91615', 'zip', '76882; TX; Coleman County; Talpa'], ['91613', 'zip', '76878; TX; Coleman County; Santa Anna'], ['91612', 'zip', '76877; TX; San Saba County; San Saba'], ['91611', 'zip', '76875; TX; Runnels County; Rowena'], ['9161', 'city', 'Offerle; KS; Edwards County'], ['91609', 'zip', '76873; TX; Coleman County; Rockwood'], ['91608', 'zip', '76872; TX; McCulloch County; Rochelle'], ['91607', 'zip', '76871; TX; San Saba County; Richland Springs'], ['91606', 'zip', '76870; TX; Comanche County; Comanche'], ['91605', 'zip', '76869; TX; Mason County; Pontotoc'], ['91603', 'zip', '76865; TX; Runnels County; Norton'], ['91602', 'zip', '76864; TX; Mills County; Mullin'], ['91600', 'zip', '76861; TX; Runnels County; Miles'], ['916', 'county', 'Carbon County; UT; Price'], ['91599', 'zip', '76859; TX; Menard County; Menard'], ['91597', 'zip', '76857; TX; Brownwood; Brown County; May'], ['91594', 'zip', '76854; TX; Kimble County; London'], ['91593', 'zip', '76853; TX; Killeen-Temple; Lampasas County; Lometa'], ['91592', 'zip', '76852; TX; McCulloch County; Pear Valley'], ['91591', 'zip', '76849; TX; Kimble County; Junction'], ['9159', 'city', 'Minersville; PA; Pottsville; Schuylkill County'], ['91589', 'zip', '76845; TX; Coleman County; Gouldbusk'], ['91588', 'zip', '76844; TX; Mills County; Goldthwaite'], ['91587', 'zip', '76842; TX; Mason County; Fredonia'], ['91583', 'zip', '76834; TX; Coleman County; Coleman'], ['91582', 'zip', '76832; TX; San Saba County; Cherokee'], ['91581', 'zip', '76831; TX; Llano County; Valley Spring'], ['91580', 'zip', '76828; TX; Coleman County; Burkett'], ['91579', 'zip', '76827; TX; Brownwood; Brown County; Brookesmith'], ['91578', 'zip', '76825; TX; McCulloch County; Brady'], ['91577', 'zip', '76824; IN; South Bend-Mishawaka; Saint Joseph County; South Bend'], ['91576', 'zip', '76823; TX; Brownwood; Brown County; Bangs'], ['91575', 'zip', '76821; TX; Runnels County; Ballinger'], ['91571', 'zip', '76802; TX; Brownwood; Brown County; Early'], ['91570', 'zip', '76801; TX; Brownwood; Brown County'], ['91562', 'zip', '76712; TX; Waco; McLennan County; Woodway'], ['91561', 'zip', '76711; TX; Waco; McLennan County'], ['91560', 'zip', '76710; TX; Waco; McLennan County'], ['9156', 'city', 'Lookout; CA; Modoc County'], ['91559', 'zip', '76708; TX; Waco; McLennan County'], ['91558', 'zip', '76707; TX; Waco; McLennan County'], ['91557', 'zip', '76706; TX; Waco; McLennan County'], ['91556', 'zip', '76705; TX; Waco; McLennan County; Bellmead'], ['91555', 'zip', '76704; TX; Waco; McLennan County'], ['91552', 'zip', '76701; TX; Waco; McLennan County'], ['91551', 'zip', '76693; TX; Freestone County; Wortham'], ['91550', 'zip', '76692; TX; Hill County; Whitney'], ['9155', 'city', 'Longport; NJ; Atlantic City-Hammonton; Atlantic County'], ['91549', 'zip', '76691; TX; Waco; McLennan County; West'], ['91548', 'zip', '76690; TX; Bosque County; Walnut Springs'], ['91547', 'zip', '76689; TX; Bosque County; Valley Mills'], ['91546', 'zip', '76687; TX; Limestone County; Thornton'], ['91545', 'zip', '76686; TX; Limestone County; Tehuacana'], ['91544', 'zip', '76685; TX; Killeen-Temple; Bell County; Temple'], ['91542', 'zip', '76682; TX; Waco; McLennan County; Riesel'], ['91541', 'zip', '76681; TX; Corsicana; Navarro County; Richland'], ['91540', 'zip', '76680; TX; Waco; Falls County; Reagan'], ['91539', 'zip', '76679; TX; Corsicana; Navarro County; Purdon'], ['91538', 'zip', '76678; TX; Limestone County; Prairie Hill'], ['91537', 'zip', '76676; TX; Hill County; Penelope'], ['91536', 'zip', '76673; TX; Hill County; Mount Calm'], ['91535', 'zip', '76671; TX; Bosque County; Morgan'], ['91534', 'zip', '76670; TX; Dallas-Fort Worth-Arlington; Ellis County; Milford'], ['91533', 'zip', '76667; TX; Limestone County; Mexia'], ['91532', 'zip', '76666; TX; Hill County; Mertens'], ['91531', 'zip', '76665; TX; Bosque County; Meridian'], ['91530', 'zip', '76664; TX; Waco; McLennan County; Mart'], ['9153', 'city', 'Long Lane; MO; Springfield; Dallas County'], ['91529', 'zip', '76661; TX; Waco; Falls County; Marlin'], ['91528', 'zip', '76660; TX; Hill County; Malone'], ['91527', 'zip', '76657; TX; Waco; McLennan County; Mc Gregor'], ['91526', 'zip', '76656; TX; Waco; Falls County; Lott'], ['91525', 'zip', '76655; TX; Waco; McLennan County; Lorena'], ['91524', 'zip', '76654; TX; Waco; McLennan County; Leroy'], ['91523', 'zip', '76653; TX; Limestone County; Kosse'], ['91522', 'zip', '76652; TX; Bosque County; Kopperl'], ['91521', 'zip', '76651; TX; Dallas-Fort Worth-Arlington; Ellis County; Italy'], ['91520', 'zip', '76650; TX; Hill County; Irene'], ['9152', 'city', 'Town Of Long Lake; NY; Hamilton County'], ['91519', 'zip', '76649; TX; Bosque County; Iredell'], ['91518', 'zip', '76648; TX; Hill County; Hubbard'], ['91517', 'zip', '76645; TX; Hill County; Hillsboro'], ['91515', 'zip', '76643; TX; Waco; McLennan County; Hewitt'], ['91514', 'zip', '76642; TX; Limestone County; Groesbeck'], ['91513', 'zip', '76641; TX; Corsicana; Navarro County; Frost'], ['91512', 'zip', '76640; TX; Waco; McLennan County; Ross'], ['91511', 'zip', '76639; TX; Corsicana; Navarro County; Dawson'], ['91510', 'zip', '76638; TX; Waco; McLennan County; Crawford'], ['9151', 'city', 'Long Barn; CA; Sonora; Tuolumne County'], ['91509', 'zip', '76637; TX; Bosque County; Cranfills Gap'], ['91508', 'zip', '76636; TX; Hill County; Covington'], ['91507', 'zip', '76635; TX; Limestone County; Coolidge'], ['91506', 'zip', '76634; TX; Bosque County; Clifton'], ['91505', 'zip', '76633; TX; Waco; McLennan County; China Spring'], ['91504', 'zip', '76632; TX; Waco; Falls County; Lott'], ['91503', 'zip', '76631; TX; Hill County; Bynum'], ['91502', 'zip', '76630; TX; Waco; McLennan County; Bruceville-Eddy'], ['91501', 'zip', '76629; TX; College Station-Bryan; Robertson County; Bremond'], ['91500', 'zip', '76628; TX; Hill County; Brandon'], ['9150', 'city', 'Lonetree; WY; Evanston; Uinta County'], ['915', 'county', 'Carbon County; PA; Allentown-Bethlehem-Easton'], ['91499', 'zip', '76627; TX; Hill County; Blum'], ['91498', 'zip', '76626; TX; Corsicana; Navarro County; Blooming Grove'], ['91497', 'zip', '76624; TX; Waco; McLennan County; Hallsburg'], ['91496', 'zip', '76623; TX; Dallas-Fort Worth-Arlington; Ellis County; Italy'], ['91495', 'zip', '76622; TX; Hill County; Aquilla'], ['91494', 'zip', '76621; TX; Hill County; Abbott'], ['91489', 'zip', '76579; TX; Killeen-Temple; Bell County; Troy'], ['91488', 'zip', '76578; TX; Austin-Round Rock; Williamson County; Thrall'], ['91487', 'zip', '76577; TX; Milam County; Thorndale'], ['91486', 'zip', '76574; TX; Austin-Round Rock; Williamson County; Taylor'], ['91485', 'zip', '76573; TX; Austin-Round Rock; Williamson County; Bartlett'], ['91484', 'zip', '76571; TX; Killeen-Temple; Bell County; Salado'], ['91483', 'zip', '76570; TX; Waco; Falls County; Rosebud'], ['91482', 'zip', '76569; TX; Killeen-Temple; Bell County; Rogers'], ['91481', 'zip', '76567; TX; Milam County; Rockdale'], ['91480', 'zip', '76566; TX; Killeen-Temple; Coryell County; Purmela'], ['91478', 'zip', '76564; TX; Killeen-Temple; Bell County; Troy'], ['91477', 'zip', '76561; TX; Killeen-Temple; Coryell County; Oglesby'], ['91476', 'zip', '76559; TX; Killeen-Temple; Bell County; Nolanville'], ['91475', 'zip', '76558; TX; Killeen-Temple; Coryell County; Gatesville'], ['91474', 'zip', '76557; TX; Waco; McLennan County; Moody'], ['91473', 'zip', '76556; TX; Milam County; Milano'], ['91472', 'zip', '76554; TX; Killeen-Temple; Bell County; Little River Academy'], ['91471', 'zip', '76550; TX; Killeen-Temple; Lampasas County; Lampasas'], ['91470', 'zip', '76549; TX; Killeen-Temple; Bell County; Killeen'], ['9147', 'city', 'Port Jervis; NY; New York-Newark-Jersey City; Orange County'], ['91469', 'zip', '76548; TX; Killeen-Temple; Bell County; Harker Heights'], ['91465', 'zip', '76544; TX; Killeen-Temple; Bell County; Belton'], ['91464', 'zip', '76543; TX; Killeen-Temple; Bell County; Killeen'], ['91463', 'zip', '76542; TX; Killeen-Temple; Bell County; Killeen'], ['91462', 'zip', '76541; TX; Killeen-Temple; Bell County; Killeen'], ['91460', 'zip', '76539; TX; Killeen-Temple; Lampasas County; Kempner'], ['9146', 'city', 'Pontotoc; MS; Tupelo; Pontotoc County'], ['91459', 'zip', '76538; TX; Hamilton County; Jonesboro'], ['91458', 'zip', '76537; TX; Austin-Round Rock; Williamson County; Jarrell'], ['91457', 'zip', '76534; TX; Killeen-Temple; Bell County; Holland'], ['91456', 'zip', '76533; TX; Killeen-Temple; Bell County; Temple'], ['91455', 'zip', '76531; TX; Hamilton County; Hamilton'], ['91454', 'zip', '76530; TX; Austin-Round Rock; Williamson County; Granger'], ['91453', 'zip', '76528; TX; Killeen-Temple; Coryell County; Gatesville'], ['91452', 'zip', '76527; TX; Austin-Round Rock; Williamson County; Florence'], ['91451', 'zip', '76526; TX; Killeen-Temple; Coryell County; Gatesville'], ['91450', 'zip', '76525; TX; Hamilton County; Evant'], ['9145', 'city', 'Odell; IL; Pontiac; Livingston County'], ['91449', 'zip', '76524; TX; Waco; McLennan County; Bruceville-Eddy'], ['91448', 'zip', '76523; TX; Milam County; Rockdale'], ['91447', 'zip', '76522; TX; Killeen-Temple; Coryell County; Copperas Cove'], ['91446', 'zip', '76520; TX; Milam County; Cameron'], ['91445', 'zip', '76519; TX; Milam County; Burlington'], ['91444', 'zip', '76518; TX; Milam County; Buckholts'], ['91443', 'zip', '76513; TX; Killeen-Temple; Bell County; Belton'], ['91442', 'zip', '76511; TX; Austin-Round Rock; Williamson County; Bartlett'], ['91439', 'zip', '76504; TX; Killeen-Temple; Bell County; Temple'], ['91437', 'zip', '76502; TX; Killeen-Temple; Bell County; Temple'], ['91436', 'zip', '76501; TX; Killeen-Temple; Bell County; Temple'], ['91435', 'zip', '76491; TX; Throckmorton County; Woodson'], ['91433', 'zip', '76487; TX; Dallas-Fort Worth-Arlington; Parker County; Poolville'], ['91432', 'zip', '76486; TX; Jack County; Perrin'], ['91430', 'zip', '76484; TX; Mineral Wells; Palo Pinto County; Palo Pinto'], ['9143', 'city', 'Ochelata; OK; Bartlesville; Washington County'], ['91428', 'zip', '76481; TX; Stephens County; Breckenridge'], ['91427', 'zip', '76476; TX; Dallas-Fort Worth-Arlington; Hood County; Tolar'], ['91426', 'zip', '76475; TX; Mineral Wells; Palo Pinto County; Strawn'], ['91425', 'zip', '76474; TX; Comanche County; Sidney'], ['91424', 'zip', '76472; TX; Mineral Wells; Palo Pinto County; Santo'], ['91423', 'zip', '76471; TX; Eastland County; Rising Star'], ['91422', 'zip', '76470; TX; Eastland County; Ranger'], ['91419', 'zip', '76467; TX; Dallas-Fort Worth-Arlington; Hood County; Paluxy'], ['91418', 'zip', '76466; TX; Eastland County; Olden'], ['91415', 'zip', '76463; TX; Mineral Wells; Palo Pinto County; Mingus'], ['91414', 'zip', '76462; TX; Dallas-Fort Worth-Arlington; Hood County; Lipan'], ['91412', 'zip', '76460; TX; Young County; Loving'], ['91411', 'zip', '76459; TX; Jack County; Jermyn'], ['91410', 'zip', '76458; TX; Jack County; Jacksboro'], ['91409', 'zip', '76457; TX; Hamilton County; Hico'], ['91408', 'zip', '76455; TX; Comanche County; Gustine'], ['91407', 'zip', '76454; TX; Eastland County; Gorman'], ['91406', 'zip', '76453; TX; Mineral Wells; Palo Pinto County; Gordon'], ['91405', 'zip', '76452; TX; Comanche County; Energy'], ['91404', 'zip', '76450; TX; Young County; Graham'], ['91403', 'zip', '76449; TX; Mineral Wells; Palo Pinto County; Graford'], ['91402', 'zip', '76448; TX; Eastland County; Eastland'], ['91401', 'zip', '76446; TX; Stephenville; Erath County; Dublin'], ['91400', 'zip', '76445; TX; Eastland County; Desdemona'], ['9140', 'city', 'Oakwood; TX; Leon County'], ['914', 'county', 'Carbon County; MT; Billings'], ['91399', 'zip', '76444; TX; Comanche County; De Leon'], ['91398', 'zip', '76443; TX; Abilene; Callahan County; Cross Plains'], ['91397', 'zip', '76442; TX; Comanche County; Comanche'], ['91395', 'zip', '76437; TX; Eastland County; Cisco'], ['91394', 'zip', '76436; TX; Hamilton County; Carlton'], ['91393', 'zip', '76435; TX; Eastland County; Carbon'], ['91392', 'zip', '76433; TX; Stephenville; Erath County'], ['91391', 'zip', '76432; TX; Brownwood; Brown County; Blanket'], ['91390', 'zip', '76431; TX; Dallas-Fort Worth-Arlington; Wise County; Chico'], ['9139', 'city', 'Milo; MO; Vernon County'], ['91389', 'zip', '76430; TX; Shackelford County; Albany'], ['91388', 'zip', '76429; TX; Mineral Wells; Palo Pinto County; Caddo'], ['91387', 'zip', '76427; TX; Jack County; Bryson'], ['91386', 'zip', '76426; TX; Dallas-Fort Worth-Arlington; Wise County; Bridgeport'], ['91385', 'zip', '76424; TX; Stephens County; Breckenridge'], ['91383', 'zip', '76401; TX; Stephenville; Erath County'], ['91382', 'zip', '76389; TX; Wichita Falls; Archer County; Windthorst'], ['91381', 'zip', '76388; TX; Haskell County; Weinert'], ['91379', 'zip', '76384; TX; Vernon; Wilbarger County'], ['91377', 'zip', '76379; TX; Wichita Falls; Archer County; Scotland'], ['91376', 'zip', '76377; TX; Wichita Falls; Clay County; Petrolia'], ['91375', 'zip', '76374; TX; Young County; Olney'], ['91374', 'zip', '76373; TX; Vernon; Wilbarger County; Oklaunion'], ['91373', 'zip', '76372; TX; Young County; Newcastle'], ['91371', 'zip', '76370; TX; Wichita Falls; Archer County; Megargel'], ['91370', 'zip', '76369; TX; Wichita Falls; Wichita County; Electra'], ['91369', 'zip', '76367; TX; Wichita Falls; Wichita County; Iowa Park'], ['91368', 'zip', '76366; TX; Wichita Falls; Archer County; Holliday'], ['91367', 'zip', '76365; TX; Wichita Falls; Clay County; Henrietta'], ['91366', 'zip', '76364; TX; Vernon; Wilbarger County; Harrold'], ['91364', 'zip', '76360; TX; Wichita Falls; Wichita County; Electra'], ['91363', 'zip', '76357; TX; Wichita Falls; Clay County; Byers'], ['91362', 'zip', '76354; TX; Wichita Falls; Wichita County; Burkburnett'], ['91360', 'zip', '76351; TX; Wichita Falls; Archer County; Archer City'], ['91358', 'zip', '76310; TX; Wichita Falls; Wichita County'], ['91357', 'zip', '76309; TX; Wichita Falls; Wichita County'], ['91356', 'zip', '76308; TX; Wichita Falls; Wichita County'], ['91354', 'zip', '76306; TX; Wichita Falls; Wichita County'], ['91353', 'zip', '76305; TX; Wichita Falls; Wichita County'], ['91352', 'zip', '76302; TX; Wichita Falls; Wichita County'], ['91351', 'zip', '76301; TX; Wichita Falls; Wichita County'], ['91349', 'zip', '76273; TX; Sherman-Denison; Grayson County; Whitesboro'], ['91348', 'zip', '76272; TX; Gainesville; Cooke County; Valley View'], ['91347', 'zip', '76271; TX; Sherman-Denison; Grayson County; Tioga'], ['91346', 'zip', '76270; TX; Montague County; Sunset'], ['91345', 'zip', '76268; TX; Sherman-Denison; Grayson County; Whitesboro'], ['91343', 'zip', '76266; TX; Dallas-Fort Worth-Arlington; Denton County; Sanger'], ['91342', 'zip', '76265; TX; Montague County; Saint Jo'], ['91341', 'zip', '76264; TX; Sherman-Denison; Grayson County; Sadler'], ['91340', 'zip', '76263; TX; Montague County; Forestburg'], ['9134', 'city', 'Miller; NE; Kearney; Buffalo County'], ['91339', 'zip', '76262'], ['91338', 'zip', '76261; TX; Montague County; Ringgold'], ['91337', 'zip', '76259; TX; Dallas-Fort Worth-Arlington; Denton County; Ponder'], ['91336', 'zip', '76258; TX; Dallas-Fort Worth-Arlington; Denton County; Pilot Point'], ['91335', 'zip', '76255; TX; Montague County; Nocona'], ['91334', 'zip', '76253; TX; Gainesville; Cooke County; Myra'], ['91333', 'zip', '76252; TX; Gainesville; Cooke County; Muenster'], ['91332', 'zip', '76251; TX; Montague County; Montague'], ['91331', 'zip', '76250; TX; Gainesville; Cooke County; Lindsay'], ['91330', 'zip', '76249; TX; Dallas-Fort Worth-Arlington; Denton County; Krum'], ['91329', 'zip', '76248'], ['91328', 'zip', '76247; TX; Dallas-Fort Worth-Arlington; Denton County; Justin'], ['91326', 'zip', '76245; TX; Sherman-Denison; Grayson County; Gordonville'], ['91325', 'zip', '76244'], ['91323', 'zip', '76240; TX; Gainesville; Cooke County'], ['91322', 'zip', '76239; TX; Montague County; Forestburg'], ['91321', 'zip', '76238; TX; Gainesville; Cooke County; Era'], ['91320', 'zip', '76234; TX; Dallas-Fort Worth-Arlington; Wise County; Decatur'], ['91319', 'zip', '76233; TX; Sherman-Denison; Grayson County; Collinsville'], ['91318', 'zip', '76230; TX; Montague County; Bowie'], ['91317', 'zip', '76228; TX; Wichita Falls; Clay County; Bellevue'], ['91316', 'zip', '76227'], ['91315', 'zip', '76226'], ['91314', 'zip', '76225; TX; Dallas-Fort Worth-Arlington; Wise County; Alvord'], ['91313', 'zip', '76210'], ['91312', 'zip', '76209; TX; Dallas-Fort Worth-Arlington; Denton County; Denton'], ['91311', 'zip', '76208; TX; Dallas-Fort Worth-Arlington; Denton County; Denton'], ['91310', 'zip', '76207'], ['91308', 'zip', '76205; TX; Dallas-Fort Worth-Arlington; Denton County; Denton'], ['91304', 'zip', '76201; TX; Dallas-Fort Worth-Arlington; Denton County; Denton'], ['913', 'county', 'Canyon County; ID; Boise City'], ['91294', 'zip', '76182'], ['91292', 'zip', '76180; TX; Dallas-Fort Worth-Arlington; Tarrant County; North Richland Hills'], ['91291', 'zip', '76179'], ['91290', 'zip', '76177; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91289', 'zip', '76164; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91285', 'zip', '76155; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91283', 'zip', '76148'], ['91281', 'zip', '76140; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91280', 'zip', '76137'], ['9128', 'city', 'Loco; OK; Duncan; Stephens County'], ['91278', 'zip', '76135'], ['91277', 'zip', '76134'], ['91276', 'zip', '76133'], ['91275', 'zip', '76132'], ['91274', 'zip', '76131'], ['91270', 'zip', '76126'], ['91268', 'zip', '76123'], ['91265', 'zip', '76120'], ['91264', 'zip', '76119'], ['91263', 'zip', '76118'], ['91262', 'zip', '76117; TX; Dallas-Fort Worth-Arlington; Tarrant County; Haltom City'], ['91261', 'zip', '76116'], ['91260', 'zip', '76115; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['9126', 'city', 'Lockhart; SC; Spartanburg; Union County'], ['91259', 'zip', '76114'], ['91257', 'zip', '76112'], ['91256', 'zip', '76111; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91255', 'zip', '76110'], ['91254', 'zip', '76109'], ['91253', 'zip', '76108'], ['91252', 'zip', '76107'], ['91251', 'zip', '76106; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91250', 'zip', '76105; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91249', 'zip', '76104; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91248', 'zip', '76103; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91247', 'zip', '76102; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['9124', 'city', 'Littlefork; MN; Koochiching County'], ['91239', 'zip', '76093; TX; Dallas-Fort Worth-Arlington; Johnson County; Rio Vista'], ['91238', 'zip', '76092; TX; Dallas-Fort Worth-Arlington; Tarrant County; Southlake'], ['91237', 'zip', '76088; TX; Dallas-Fort Worth-Arlington; Parker County; Weatherford'], ['91236', 'zip', '76087; TX; Dallas-Fort Worth-Arlington; Parker County; Weatherford'], ['91235', 'zip', '76086; TX; Dallas-Fort Worth-Arlington; Parker County; Weatherford'], ['91234', 'zip', '76085; TX; Dallas-Fort Worth-Arlington; Parker County; Weatherford'], ['91233', 'zip', '76084; TX; Dallas-Fort Worth-Arlington; Johnson County; Venus'], ['91232', 'zip', '76082; TX; Dallas-Fort Worth-Arlington; Parker County; Springtown'], ['91231', 'zip', '76078; TX; Dallas-Fort Worth-Arlington; Wise County; New Fairview'], ['91230', 'zip', '76077; TX; Dallas-Fort Worth-Arlington; Somervell County; Rainbow'], ['91229', 'zip', '76073; TX; Dallas-Fort Worth-Arlington; Wise County; Paradise'], ['91228', 'zip', '76071; TX; Dallas-Fort Worth-Arlington; Wise County; Newark'], ['91227', 'zip', '76070; TX; Dallas-Fort Worth-Arlington; Somervell County; Nemo'], ['91225', 'zip', '76067; TX; Mineral Wells; Palo Pinto County'], ['91224', 'zip', '76066; TX; Dallas-Fort Worth-Arlington; Parker County; Millsap'], ['91223', 'zip', '76065'], ['91222', 'zip', '76064; TX; Dallas-Fort Worth-Arlington; Ellis County; Maypearl'], ['91221', 'zip', '76063'], ['91220', 'zip', '76061; LA; New Orleans-Metairie; Saint Tammany Parish; Lacombe'], ['91219', 'zip', '76060; TX; Dallas-Fort Worth-Arlington; Tarrant County; Kennedale'], ['91218', 'zip', '76059; TX; Dallas-Fort Worth-Arlington; Johnson County; Keene'], ['91217', 'zip', '76058; TX; Dallas-Fort Worth-Arlington; Johnson County; Joshua'], ['91216', 'zip', '76055; TX; Hill County; Itasca'], ['91215', 'zip', '76054; TX; Dallas-Fort Worth-Arlington; Tarrant County; Hurst'], ['91214', 'zip', '76053; TX; Dallas-Fort Worth-Arlington; Tarrant County; Hurst'], ['91213', 'zip', '76052; TX; Dallas-Fort Worth-Arlington; Tarrant County; Fort Worth'], ['91212', 'zip', '76051'], ['91211', 'zip', '76050; TX; Dallas-Fort Worth-Arlington; Johnson County; Grandview'], ['91210', 'zip', '76049; TX; Dallas-Fort Worth-Arlington; Hood County; Granbury'], ['9121', 'city', 'Ponce Inlet; FL; Deltona-Daytona Beach-Ormond Beach; Volusia County'], ['91209', 'zip', '76048; TX; Dallas-Fort Worth-Arlington; Hood County; Granbury'], ['91208', 'zip', '76044; TX; Dallas-Fort Worth-Arlington; Johnson County; Godley'], ['91207', 'zip', '76043; TX; Dallas-Fort Worth-Arlington; Somervell County; Glen Rose'], ['91206', 'zip', '76041; TX; Dallas-Fort Worth-Arlington; Ellis County; Forreston'], ['91205', 'zip', '76040'], ['91204', 'zip', '76039'], ['91203', 'zip', '76036'], ['91202', 'zip', '76035; TX; Dallas-Fort Worth-Arlington; Parker County; Cresson'], ['91201', 'zip', '76034'], ['91200', 'zip', '76033; TX; Dallas-Fort Worth-Arlington; Johnson County; Cleburne'], ['912', 'county', 'Cannon County; TN; Nashville-Davidson--Murfreesboro--Franklin'], ['91199', 'zip', '76031; TX; Dallas-Fort Worth-Arlington; Johnson County; Cleburne'], ['91198', 'zip', '76028'], ['91197', 'zip', '76023; TX; Dallas-Fort Worth-Arlington; Wise County; Boyd'], ['91196', 'zip', '76022; TX; Dallas-Fort Worth-Arlington; Tarrant County; Bedford'], ['91195', 'zip', '76021'], ['91194', 'zip', '76020'], ['91192', 'zip', '76018'], ['91191', 'zip', '76017'], ['91190', 'zip', '76016'], ['9119', 'city', 'Poland; OH; Youngstown-Warren-Boardman; Mahoning County'], ['91189', 'zip', '76015; TX; Dallas-Fort Worth-Arlington; Tarrant County; Arlington'], ['91188', 'zip', '76014'], ['91187', 'zip', '76013'], ['91186', 'zip', '76012'], ['91185', 'zip', '76011'], ['91184', 'zip', '76010'], ['91183', 'zip', '76009; TX; Dallas-Fort Worth-Arlington; Johnson County; Alvarado'], ['91182', 'zip', '76008; TX; Dallas-Fort Worth-Arlington; Parker County; Aledo'], ['91180', 'zip', '76006; TX; Dallas-Fort Worth-Arlington; Tarrant County; Arlington'], ['91179', 'zip', '76005; TX; Dallas-Fort Worth-Arlington; Tarrant County; Arlington'], ['91176', 'zip', '76002'], ['91175', 'zip', '76001'], ['91173', 'zip', '75980; TX; Lufkin; Angelina County; Zavalla'], ['91172', 'zip', '75979; TX; Tyler County; Woodville'], ['91171', 'zip', '75978; TX; Nacogdoches; Nacogdoches County; Chireno'], ['91170', 'zip', '75977; TX; Beaumont-Port Arthur; Newton County; Wiergate'], ['91169', 'zip', '75976; TX; Jacksonville; Cherokee County; Alto'], ['91168', 'zip', '75975; TX; Shelby County; Timpson'], ['91167', 'zip', '75974; TX; Shelby County; Tenaha'], ['91166', 'zip', '75973; TX; Shelby County; Center'], ['91165', 'zip', '75972; TX; San Augustine County; San Augustine'], ['91164', 'zip', '75969; TX; Lufkin; Angelina County; Pollok'], ['91163', 'zip', '75968; TX; Sabine County; Pineland'], ['91162', 'zip', '75966; TX; Beaumont-Port Arthur; Newton County; Newton'], ['91161', 'zip', '75965; TX; Nacogdoches; Nacogdoches County'], ['91160', 'zip', '75964; TX; Nacogdoches; Nacogdoches County'], ['91159', 'zip', '75963; TX; Nacogdoches; Nacogdoches County'], ['91157', 'zip', '75961; TX; Nacogdoches; Nacogdoches County'], ['91156', 'zip', '75960; TX; Polk County; Corrigan'], ['91155', 'zip', '75959; TX; Sabine County; Milam'], ['91153', 'zip', '75956; TX; Jasper County; Kirbyville'], ['91152', 'zip', '75954; TX; Shelby County; Joaquin'], ['91151', 'zip', '75951; TX; Jasper County; Jasper'], ['91150', 'zip', '75949; TX; Lufkin; Angelina County; Huntington'], ['9115', 'city', 'Oak Ridge; MO; Cape Girardeau; Cape Girardeau County'], ['91149', 'zip', '75948; TX; Sabine County; Hemphill'], ['91147', 'zip', '75946; TX; Nacogdoches; Nacogdoches County; Garrison'], ['91146', 'zip', '75944; TX; Nacogdoches; Nacogdoches County; Etoile'], ['91145', 'zip', '75943; TX; Nacogdoches; Nacogdoches County; Douglass'], ['91144', 'zip', '75942; TX; Tyler County; Woodville'], ['91143', 'zip', '75941; TX; Lufkin; Angelina County; Diboll'], ['91142', 'zip', '75939; TX; Polk County; Corrigan'], ['91141', 'zip', '75938; TX; Tyler County; Colmesneil'], ['91140', 'zip', '75937; TX; Nacogdoches; Nacogdoches County; Chireno'], ['91139', 'zip', '75936; TX; Tyler County; Chester'], ['91138', 'zip', '75935; TX; Shelby County; Center'], ['91137', 'zip', '75934; TX; Polk County; Camden'], ['91136', 'zip', '75933; TX; Beaumont-Port Arthur; Newton County; Call'], ['91135', 'zip', '75932; TX; Beaumont-Port Arthur; Newton County; Burkeville'], ['91134', 'zip', '75931; TX; Jasper County; Browndell'], ['91133', 'zip', '75930; TX; Sabine County; Bronson'], ['91132', 'zip', '75929; TX; San Augustine County; Broaddus'], ['91131', 'zip', '75928; TX; Beaumont-Port Arthur; Newton County; Bon Wier'], ['91130', 'zip', '75926; TX; Huntsville; Trinity County; Apple Springs'], ['9113', 'city', 'Oak Leaf; TX; Dallas-Fort Worth-Arlington; Ellis County'], ['91129', 'zip', '75925; TX; Jacksonville; Cherokee County; Alto'], ['91127', 'zip', '75904; TX; Lufkin; Angelina County'], ['91124', 'zip', '75901; TX; Lufkin; Angelina County'], ['9112', 'city', 'Oak Hill; FL; Deltona-Daytona Beach-Ormond Beach; Volusia County'], ['91119', 'zip', '75865; TX; Huntsville; Trinity County; Groveton'], ['91118', 'zip', '75862; TX; Huntsville; Trinity County; Trinity'], ['91117', 'zip', '75861; TX; Palestine; Anderson County; Tennessee Colony'], ['91116', 'zip', '75860; TX; Freestone County; Teague'], ['91115', 'zip', '75859; TX; Freestone County; Streetman'], ['91114', 'zip', '75858; TX; Houston County; Ratcliff'], ['91113', 'zip', '75856; TX; Houston County; Pennington'], ['91112', 'zip', '75855; TX; Leon County; Oakwood'], ['91111', 'zip', '75853; TX; Palestine; Anderson County; Montalba'], ['91110', 'zip', '75852; TX; Madison County; Midway'], ['9111', 'city', 'Oak Grove; LA; West Carroll Parish'], ['91109', 'zip', '75851; TX; Houston County; Lovelady'], ['91108', 'zip', '75850; TX; Leon County; Leona'], ['91107', 'zip', '75849; TX; Houston County; Crockett'], ['91106', 'zip', '75848; TX; Freestone County; Streetman'], ['91105', 'zip', '75847; TX; Houston County; Kennard'], ['91104', 'zip', '75846; TX; Leon County; Jewett'], ['91103', 'zip', '75845; TX; Huntsville; Trinity County; Groveton'], ['91102', 'zip', '75844; TX; Houston County; Grapeland'], ['91101', 'zip', '75840; TX; Freestone County; Fairfield'], ['91100', 'zip', '75839; TX; Palestine; Anderson County; Elkhart'], ['9110', 'city', 'Oak City; UT; Millard County'], ['911', 'county', 'Camden County; NJ; Philadelphia-Camden-Wilmington'], ['91099', 'zip', '75838; TX; Freestone County; Donie'], ['91098', 'zip', '75835; TX; Houston County; Crockett'], ['91096', 'zip', '75833; TX; Leon County; Centerville'], ['91094', 'zip', '75831; TX; Leon County; Buffalo'], ['91093', 'zip', '75803; TX; Palestine; Anderson County'], ['91091', 'zip', '75801; TX; Palestine; Anderson County'], ['91087', 'zip', '75792; TX; Tyler; Smith County; Winona'], ['91086', 'zip', '75791; TX; Tyler; Smith County; Whitehouse'], ['91085', 'zip', '75790; TX; Van Zandt County; Van'], ['91084', 'zip', '75789; TX; Tyler; Smith County; Troup'], ['91082', 'zip', '75785; TX; Jacksonville; Cherokee County; Rusk'], ['91081', 'zip', '75784; TX; Jacksonville; Cherokee County; Reklaw'], ['91080', 'zip', '75783; TX; Wood County; Quitman'], ['9108', 'city', 'Novice; TX; Coleman County'], ['91079', 'zip', '75782; TX; Athens; Henderson County; Poynor'], ['91078', 'zip', '75780; TX; Jacksonville; Cherokee County; New Summerfield'], ['91076', 'zip', '75778; TX; Van Zandt County; Murchison'], ['91075', 'zip', '75773; TX; Wood County; Mineola'], ['91074', 'zip', '75772; TX; Jacksonville; Cherokee County; Maydelle'], ['91073', 'zip', '75771; TX; Tyler; Smith County; Lindale'], ['91072', 'zip', '75770; TX; Athens; Henderson County; Poynor'], ['91071', 'zip', '75766; TX; Jacksonville; Cherokee County'], ['91070', 'zip', '75765; TX; Wood County; Hawkins'], ['9107', 'city', 'Nottingham; NH; Boston-Cambridge-Newton; Rockingham County'], ['91069', 'zip', '75764; TX; Jacksonville; Cherokee County; Gallatin'], ['91068', 'zip', '75763; TX; Athens; Henderson County; Coffee City'], ['91067', 'zip', '75762; TX; Tyler; Smith County'], ['91066', 'zip', '75760; TX; Nacogdoches; Nacogdoches County; Cushing'], ['91065', 'zip', '75759; TX; Jacksonville; Cherokee County; Cuney'], ['91064', 'zip', '75758; TX; Athens; Henderson County; Chandler'], ['91063', 'zip', '75757; TX; Tyler; Smith County; Bullard'], ['91062', 'zip', '75756; TX; Athens; Henderson County; Brownsboro'], ['91061', 'zip', '75755; TX; Longview; Upshur County; Big Sandy'], ['91060', 'zip', '75754; TX; Van Zandt County; Murchison'], ['91059', 'zip', '75752; TX; Athens; Henderson County'], ['91058', 'zip', '75751; TX; Athens; Henderson County'], ['91057', 'zip', '75750; TX; Tyler; Smith County; Arp'], ['91052', 'zip', '75709; TX; Tyler; Smith County'], ['91050', 'zip', '75707; TX; Tyler; Smith County'], ['91049', 'zip', '75706; TX; Tyler; Smith County'], ['91048', 'zip', '75705; TX; Tyler; Smith County'], ['91047', 'zip', '75704; TX; Tyler; Smith County'], ['91046', 'zip', '75703; TX; Tyler; Smith County'], ['91044', 'zip', '75701; TX; Tyler; Smith County'], ['91042', 'zip', '75693; TX; Longview; Gregg County; White Oak'], ['91041', 'zip', '75692; TX; Marshall; Harrison County; Waskom'], ['91040', 'zip', '75691; TX; Longview; Rusk County; Tatum'], ['91039', 'zip', '75689; TX; Longview; Rusk County; Overton'], ['91037', 'zip', '75687; TX; Brewster County; Alpine'], ['91036', 'zip', '75686; TX; Camp County; Pittsburg'], ['91034', 'zip', '75684; TX; Longview; Rusk County; Overton'], ['91033', 'zip', '75683; TX; Marion County; Ore City'], ['91032', 'zip', '75682; TX; Longview; Rusk County; New London'], ['91031', 'zip', '75681; TX; Longview; Rusk County; Mount Enterprise'], ['91029', 'zip', '75672; TX; Marshall; Harrison County'], ['91027', 'zip', '75670; TX; Marshall; Harrison County'], ['91026', 'zip', '75669; TX; Panola County; Long Branch'], ['91025', 'zip', '75668; TX; Morris County; Lone Star'], ['91024', 'zip', '75667; TX; Longview; Rusk County; Laneville'], ['91021', 'zip', '75662; TX; Longview; Gregg County; Kilgore'], ['91020', 'zip', '75661; TX; Marshall; Harrison County'], ['91017', 'zip', '75658; TX; Longview; Rusk County; Joinerville'], ['91016', 'zip', '75657; TX; Marion County; Jefferson'], ['91015', 'zip', '75656; TX; Cass County; Hughes Springs'], ['91014', 'zip', '75654; TX; Longview; Rusk County; Henderson'], ['91012', 'zip', '75652; TX; Longview; Rusk County; Henderson'], ['91011', 'zip', '75651; TX; Marshall; Harrison County; Harleton'], ['91010', 'zip', '75650; TX; Marshall; Harrison County; Hallsville'], ['9101', 'city', 'Little Orleans; MD; Cumberland; Allegany County'], ['91009', 'zip', '75647; TX; Longview; Gregg County; Gladewater'], ['91008', 'zip', '75645; TX; Longview; Upshur County; Gilmer'], ['91007', 'zip', '75644; TX; Longview; Upshur County; Gilmer'], ['91006', 'zip', '75643; TX; Panola County; Gary City'], ['91005', 'zip', '75642; TX; Marshall; Harrison County'], ['91003', 'zip', '75640; TX; Longview; Upshur County; Diana'], ['91002', 'zip', '75639; TX; Panola County; Carthage'], ['91001', 'zip', '75638; TX; Morris County; Daingerfield'], ['91000', 'zip', '75637; TX; Panola County; Carthage'], ['9100', 'city', 'Linn Valley; KS; Kansas City; Linn County'], ['910', 'county', 'Camden County; NC; Elizabeth City'], ['91', 'county', 'Ray County; MO; Kansas City'], ['90998', 'zip', '75633; TX; Panola County; Carthage'], ['90997', 'zip', '75631; TX; Panola County; Beckville'], ['90996', 'zip', '75630; TX; Marion County; Avinger'], ['90991', 'zip', '75605; TX; Longview; Gregg County'], ['90990', 'zip', '75604; TX; Longview; Gregg County'], ['9099', 'city', 'Linn Grove; IA; Storm Lake; Buena Vista County'], ['90989', 'zip', '75603; TX; Longview; Gregg County'], ['90988', 'zip', '75602; TX; Longview; Gregg County'], ['90987', 'zip', '75601; TX; Longview; Gregg County'], ['90986', 'zip', '75599; TX; Texarkana; Bowie County'], ['90985', 'zip', '75574; TX; Texarkana; Bowie County; Simms'], ['90984', 'zip', '75573; TX; Texarkana; Bowie County; Redwater'], ['90983', 'zip', '75572; TX; Cass County; Queen City'], ['90982', 'zip', '75571; TX; Morris County; Omaha'], ['90981', 'zip', '75570; TX; Texarkana; Bowie County; New Boston'], ['90980', 'zip', '75569; TX; Texarkana; Bowie County; Nash'], ['9098', 'city', 'Linkwood; MD; Cambridge; Dorchester County'], ['90979', 'zip', '75568; TX; Morris County; Naples'], ['90978', 'zip', '75567; TX; Texarkana; Bowie County; Maud'], ['90977', 'zip', '75566; TX; Cass County; Marietta'], ['90976', 'zip', '75565; TX; Cass County; McLeod'], ['90974', 'zip', '75563; TX; Cass County; Linden'], ['90973', 'zip', '75562; TX; Cass County; Kildare'], ['90972', 'zip', '75561; TX; Texarkana; Bowie County; Hooks'], ['90971', 'zip', '75560; TX; Cass County; Douglassville'], ['90970', 'zip', '75559; TX; Texarkana; Bowie County; De Kalb'], ['90969', 'zip', '75558; TX; Mount Pleasant; Titus County; Cookville'], ['90968', 'zip', '75556; TX; Cass County; Bloomburg'], ['90967', 'zip', '75555; TX; Cass County; Bivins'], ['90966', 'zip', '75554; TX; Red River County; Avery'], ['90965', 'zip', '75551; TX; Cass County; Atlanta'], ['90964', 'zip', '75550; TX; Red River County; Annona'], ['90960', 'zip', '75503; TX; Texarkana; Bowie County'], ['90959', 'zip', '75501; TX; Texarkana; Bowie County'], ['90958', 'zip', '75497; TX; Wood County; Yantis'], ['90957', 'zip', '75496; TX; Dallas-Fort Worth-Arlington; Hunt County; Wolfe City'], ['90956', 'zip', '75495; TX; Sherman-Denison; Grayson County; Van Alstyne'], ['90955', 'zip', '75494; TX; Wood County; Winnsboro'], ['90954', 'zip', '75493; TX; Mount Pleasant; Titus County; Winfield'], ['90953', 'zip', '75492; TX; Bonham; Fannin County; Windom'], ['90952', 'zip', '75491; TX; Sherman-Denison; Grayson County; Whitewright'], ['90951', 'zip', '75490; TX; Bonham; Fannin County; Trenton'], ['90950', 'zip', '75489; TX; Sherman-Denison; Grayson County; Whitewright'], ['90949', 'zip', '75488; TX; Bonham; Fannin County; Ivanhoe'], ['90948', 'zip', '75487; TX; Mount Pleasant; Titus County; Talco'], ['90947', 'zip', '75486; TX; Paris; Lamar County; Sumner'], ['90944', 'zip', '75482; TX; Sulphur Springs; Hopkins County'], ['90943', 'zip', '75481; TX; Sulphur Springs; Hopkins County; Sulphur Bluff'], ['90942', 'zip', '75480; TX; Franklin County; Scroggins'], ['90941', 'zip', '75479; TX; Bonham; Fannin County; Savoy'], ['90940', 'zip', '75478; TX; Sulphur Springs; Hopkins County; Saltillo'], ['9094', 'city', 'Pleasant View; CO; Montezuma County'], ['90939', 'zip', '75477; TX; Paris; Lamar County; Roxton'], ['90938', 'zip', '75476; TX; Bonham; Fannin County; Ravenna'], ['90937', 'zip', '75475; TX; Bonham; Fannin County'], ['90936', 'zip', '75474; TX; Dallas-Fort Worth-Arlington; Hunt County; West Tawakoni'], ['90935', 'zip', '75473; TX; Paris; Lamar County; Powderly'], ['90934', 'zip', '75472; TX; Rains County; East Tawakoni'], ['90933', 'zip', '75471; TX; Sulphur Springs; Hopkins County; Pickton'], ['90932', 'zip', '75470; TX; Paris; Lamar County; Petty'], ['90931', 'zip', '75469; TX; Delta County; Pecan Gap'], ['90930', 'zip', '75468; TX; Paris; Lamar County; Pattonville'], ['9093', 'city', 'Pleasant Hill; LA; Sabine Parish'], ['90929', 'zip', '75462; TX; Paris; Lamar County'], ['90927', 'zip', '75460; TX; Paris; Lamar County'], ['90926', 'zip', '75459; TX; Sherman-Denison; Grayson County; Howe'], ['90924', 'zip', '75457; TX; Franklin County; Mount Vernon'], ['90923', 'zip', '75456; TX; Mount Pleasant; Titus County'], ['90922', 'zip', '75455; TX; Mount Pleasant; Titus County'], ['90921', 'zip', '75454; TX; Dallas-Fort Worth-Arlington; Collin County; Melissa'], ['90920', 'zip', '75453; TX; Dallas-Fort Worth-Arlington; Hunt County; Lone Oak'], ['90919', 'zip', '75452; TX; Bonham; Fannin County; Leonard'], ['90918', 'zip', '75451; TX; Camp County; Leesburg'], ['90917', 'zip', '75450; TX; Delta County; Lake Creek'], ['90916', 'zip', '75449; TX; Bonham; Fannin County; Ladonia'], ['90915', 'zip', '75448; TX; Delta County; Klondike'], ['90914', 'zip', '75447; TX; Bonham; Fannin County; Ivanhoe'], ['90913', 'zip', '75446; TX; Bonham; Fannin County; Honey Grove'], ['90912', 'zip', '75444; TX; Wood County; Golden'], ['90911', 'zip', '75443; TX; Dallas-Fort Worth-Arlington; Hunt County; Wolfe City'], ['90910', 'zip', '75442; TX; Dallas-Fort Worth-Arlington; Collin County; Farmersville'], ['90909', 'zip', '75441; TX; Delta County; Enloe'], ['90908', 'zip', '75440; TX; Rains County; Emory'], ['90907', 'zip', '75439; TX; Bonham; Fannin County; Ector'], ['90906', 'zip', '75438; TX; Bonham; Fannin County; Dodd City'], ['90905', 'zip', '75437; TX; Sulphur Springs; Hopkins County; Dike'], ['90904', 'zip', '75436; TX; Red River County; Detroit'], ['90903', 'zip', '75435; TX; Paris; Lamar County; Deport'], ['90901', 'zip', '75433; TX; Sulphur Springs; Hopkins County; Cumby'], ['90900', 'zip', '75432; TX; Delta County; Cooper'], ['909', 'county', 'Camden County; MO'], ['90899', 'zip', '75431; TX; Sulphur Springs; Hopkins County; Como'], ['90897', 'zip', '75428; TX; Dallas-Fort Worth-Arlington; Hunt County; Commerce'], ['90896', 'zip', '75426; TX; Red River County; Clarksville'], ['90894', 'zip', '75424; TX; Dallas-Fort Worth-Arlington; Collin County; Blue Ridge'], ['90893', 'zip', '75423; TX; Dallas-Fort Worth-Arlington; Hunt County; Celeste'], ['90892', 'zip', '75422; TX; Dallas-Fort Worth-Arlington; Hunt County; Campbell'], ['90891', 'zip', '75421; TX; Paris; Lamar County'], ['90890', 'zip', '75420; TX; Sulphur Springs; Hopkins County; Brashear'], ['90889', 'zip', '75418; TX; Bonham; Fannin County'], ['90888', 'zip', '75417; TX; Red River County; Bogata'], ['90887', 'zip', '75416; TX; Paris; Lamar County; Blossom'], ['90886', 'zip', '75415; TX; Delta County; Pecan Gap'], ['90885', 'zip', '75414; TX; Sherman-Denison; Grayson County; Bells'], ['90884', 'zip', '75413; TX; Bonham; Fannin County; Leonard'], ['90883', 'zip', '75412; TX; Red River County; Bagwell'], ['90882', 'zip', '75411; TX; Paris; Lamar County; Arthur City'], ['90881', 'zip', '75410; TX; Wood County; Alba'], ['90880', 'zip', '75409; TX; Dallas-Fort Worth-Arlington; Collin County; Anna'], ['90879', 'zip', '75407; TX; Dallas-Fort Worth-Arlington; Collin County; Princeton'], ['90876', 'zip', '75402; TX; Dallas-Fort Worth-Arlington; Hunt County; Greenville'], ['90875', 'zip', '75401; TX; Dallas-Fort Worth-Arlington; Hunt County; Greenville'], ['9085', 'city', 'Northlake; IL; Chicago-Naperville-Elgin; Cook County'], ['9084', 'city', 'North Topsail Beach; NC; Jacksonville; Onslow County'], ['90823', 'zip', '75287; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90806', 'zip', '75254'], ['90805', 'zip', '75253'], ['90804', 'zip', '75252'], ['90801', 'zip', '75249; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90800', 'zip', '75248; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['9080', 'city', 'North Saint Paul; MN; Minneapolis-St. Paul-Bloomington; Ramsey County'], ['908', 'county', 'Camden County; GA; St. Marys'], ['90799', 'zip', '75247; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90798', 'zip', '75246; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90796', 'zip', '75244'], ['90795', 'zip', '75243'], ['90793', 'zip', '75241'], ['90792', 'zip', '75240'], ['90790', 'zip', '75238; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90789', 'zip', '75237; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90788', 'zip', '75236; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90787', 'zip', '75235'], ['90786', 'zip', '75234'], ['90785', 'zip', '75233; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90784', 'zip', '75232; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90783', 'zip', '75231'], ['90782', 'zip', '75230'], ['90781', 'zip', '75229'], ['90780', 'zip', '75228'], ['9078', 'city', 'Micro; NC; Raleigh; Johnston County'], ['90779', 'zip', '75227'], ['90778', 'zip', '75226; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90777', 'zip', '75225'], ['90776', 'zip', '75224; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90775', 'zip', '75223; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90772', 'zip', '75220'], ['90771', 'zip', '75219'], ['90770', 'zip', '75218'], ['90769', 'zip', '75217'], ['90768', 'zip', '75216; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90767', 'zip', '75215; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90766', 'zip', '75214'], ['90765', 'zip', '75212; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90764', 'zip', '75211; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90763', 'zip', '75210; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90762', 'zip', '75209'], ['90761', 'zip', '75208'], ['90760', 'zip', '75207; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['9076', 'city', 'Libby; MT; Lincoln County'], ['90759', 'zip', '75206'], ['90758', 'zip', '75205; TX; Dallas-Fort Worth-Arlington; Dallas County; University Park'], ['90757', 'zip', '75204'], ['90756', 'zip', '75203; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90755', 'zip', '75202; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90754', 'zip', '75201; TX; Dallas-Fort Worth-Arlington; Dallas County; Dallas'], ['90753', 'zip', '75189'], ['90750', 'zip', '75182; TX; Dallas-Fort Worth-Arlington; Dallas County; Sunnyvale'], ['9075', 'city', 'Pittsburg; IL; Carbondale-Marion; Williamson County'], ['90749', 'zip', '75181; TX; Dallas-Fort Worth-Arlington; Dallas County; Mesquite'], ['90748', 'zip', '75180; TX; Dallas-Fort Worth-Arlington; Dallas County; Balch Springs'], ['90747', 'zip', '75173; TX; Dallas-Fort Worth-Arlington; Collin County; Josephine'], ['90746', 'zip', '75172; TX; Dallas-Fort Worth-Arlington; Dallas County; Wilmer'], ['90745', 'zip', '75169; TX; Van Zandt County; Wills Point'], ['90743', 'zip', '75167; TX; Dallas-Fort Worth-Arlington; Ellis County; Waxahachie'], ['90742', 'zip', '75166; TX; Dallas-Fort Worth-Arlington; Collin County; Lavon'], ['90741', 'zip', '75165'], ['90740', 'zip', '75164; TX; Dallas-Fort Worth-Arlington; Collin County; Josephine'], ['9074', 'city', 'Pioneer; LA; West Carroll Parish'], ['90739', 'zip', '75163; TX; Athens; Henderson County; Trinidad'], ['90738', 'zip', '75161; TX; Dallas-Fort Worth-Arlington; Kaufman County; Terrell'], ['90737', 'zip', '75160; TX; Dallas-Fort Worth-Arlington; Kaufman County; Terrell'], ['90736', 'zip', '75159; TX; Dallas-Fort Worth-Arlington; Dallas County; Seagoville'], ['90735', 'zip', '75158; TX; Dallas-Fort Worth-Arlington; Kaufman County; Scurry'], ['90733', 'zip', '75156; TX; Athens; Henderson County; Gun Barrel City'], ['90732', 'zip', '75155; TX; Corsicana; Navarro County; Rice'], ['90731', 'zip', '75154; TX; Dallas-Fort Worth-Arlington; Ellis County; Red Oak'], ['90730', 'zip', '75153; TX; Corsicana; Navarro County; Powell'], ['90729', 'zip', '75152; TX; Dallas-Fort Worth-Arlington; Ellis County; Palmer'], ['90727', 'zip', '75150'], ['90726', 'zip', '75149'], ['90725', 'zip', '75148; TX; Athens; Henderson County; Malakoff'], ['90724', 'zip', '75147; TX; Dallas-Fort Worth-Arlington; Kaufman County; Mabank'], ['90723', 'zip', '75146'], ['90722', 'zip', '75144; TX; Corsicana; Navarro County; Kerens'], ['90721', 'zip', '75143; TX; Athens; Henderson County; Tool'], ['90720', 'zip', '75142; TX; Dallas-Fort Worth-Arlington; Kaufman County; Kaufman'], ['90719', 'zip', '75141; TX; Dallas-Fort Worth-Arlington; Dallas County; Hutchins'], ['90718', 'zip', '75140; TX; Van Zandt County; Grand Saline'], ['90716', 'zip', '75137; TX; Dallas-Fort Worth-Arlington; Dallas County; Duncanville'], ['90715', 'zip', '75135; TX; Dallas-Fort Worth-Arlington; Hunt County; Caddo Mills'], ['90714', 'zip', '75134; TX; Dallas-Fort Worth-Arlington; Dallas County; Lancaster'], ['90713', 'zip', '75132; TX; Dallas-Fort Worth-Arlington; Rockwall County; Fate'], ['90712', 'zip', '75127; TX; Van Zandt County; Fruitvale'], ['90711', 'zip', '75126'], ['90710', 'zip', '75125; TX; Dallas-Fort Worth-Arlington; Ellis County; Ferris'], ['9071', 'city', 'Pineville; KY; Middlesborough; Bell County'], ['90709', 'zip', '75124; TX; Athens; Henderson County; Eustace'], ['90705', 'zip', '75119; TX; Dallas-Fort Worth-Arlington; Ellis County; Ennis'], ['90703', 'zip', '75117; TX; Van Zandt County; Edgewood'], ['90702', 'zip', '75116; TX; Dallas-Fort Worth-Arlington; Dallas County; Duncanville'], ['90701', 'zip', '75115'], ['90700', 'zip', '75114; TX; Dallas-Fort Worth-Arlington; Kaufman County; Crandall'], ['90699', 'zip', '75110; TX; Corsicana; Navarro County'], ['90698', 'zip', '75109; TX; Corsicana; Navarro County; Mildred'], ['90696', 'zip', '75105; TX; Corsicana; Navarro County; Chatfield'], ['90695', 'zip', '75104'], ['90694', 'zip', '75103; TX; Van Zandt County; Canton'], ['90693', 'zip', '75102; TX; Corsicana; Navarro County; Barry'], ['90692', 'zip', '75101; TX; Dallas-Fort Worth-Arlington; Ellis County; Bardwell'], ['90690', 'zip', '75098'], ['90688', 'zip', '75094; TX; Dallas-Fort Worth-Arlington; Collin County; Murphy'], ['90687', 'zip', '75093'], ['90686', 'zip', '75092; TX; Sherman-Denison; Grayson County; Sherman'], ['90684', 'zip', '75090; TX; Sherman-Denison; Grayson County; Sherman'], ['90683', 'zip', '75089'], ['90682', 'zip', '75088'], ['90681', 'zip', '75087'], ['90677', 'zip', '75082'], ['90676', 'zip', '75081'], ['90675', 'zip', '75080'], ['90674', 'zip', '75078; TX; Dallas-Fort Worth-Arlington; Collin County; Prosper'], ['90673', 'zip', '75077; TX; Dallas-Fort Worth-Arlington; Denton County; Highland Village'], ['90672', 'zip', '75076; TX; Sherman-Denison; Grayson County; Pottsboro'], ['90671', 'zip', '75075'], ['90670', 'zip', '75074; TX; Dallas-Fort Worth-Arlington; Collin County; Plano'], ['90669', 'zip', '75071'], ['90668', 'zip', '75070'], ['90667', 'zip', '75069'], ['90666', 'zip', '75068'], ['90665', 'zip', '75067'], ['90664', 'zip', '75065; TX; Dallas-Fort Worth-Arlington; Denton County; Lake Dallas'], ['90663', 'zip', '75063'], ['90662', 'zip', '75062; TX; Dallas-Fort Worth-Arlington; Dallas County; Irving'], ['90661', 'zip', '75061; TX; Dallas-Fort Worth-Arlington; Dallas County; Irving'], ['90660', 'zip', '75060'], ['90659', 'zip', '75058; TX; Sherman-Denison; Grayson County; Gunter'], ['90658', 'zip', '75057; TX; Dallas-Fort Worth-Arlington; Denton County; Lewisville'], ['90657', 'zip', '75056'], ['90656', 'zip', '75054; TX; Dallas-Fort Worth-Arlington; Dallas County; Grand Prairie'], ['90654', 'zip', '75052'], ['90653', 'zip', '75051; TX; Dallas-Fort Worth-Arlington; Dallas County; Grand Prairie'], ['90652', 'zip', '75050; TX; Dallas-Fort Worth-Arlington; Dallas County; Grand Prairie'], ['90650', 'zip', '75048'], ['90646', 'zip', '75044'], ['90645', 'zip', '75043'], ['90644', 'zip', '75042'], ['90643', 'zip', '75041; TX; Dallas-Fort Worth-Arlington; Dallas County; Garland'], ['90642', 'zip', '75040'], ['90641', 'zip', '75039'], ['90640', 'zip', '75038'], ['90638', 'zip', '75035'], ['90637', 'zip', '75034'], ['90636', 'zip', '75032'], ['90633', 'zip', '75028'], ['90630', 'zip', '75025'], ['90629', 'zip', '75024; TX; Dallas-Fort Worth-Arlington; Collin County; Plano'], ['90628', 'zip', '75023'], ['90627', 'zip', '75022'], ['90626', 'zip', '75021; TX; Sherman-Denison; Grayson County; Denison'], ['90625', 'zip', '75020; TX; Sherman-Denison; Grayson County; Denison'], ['90624', 'zip', '75019'], ['90621', 'zip', '75015; IN; Terre Haute; Sullivan County; Carlisle'], ['90619', 'zip', '75013'], ['90617', 'zip', '75010'], ['90616', 'zip', '75009'], ['90614', 'zip', '75007'], ['90613', 'zip', '75006'], ['90612', 'zip', '75002'], ['90611', 'zip', '75001; TX; Dallas-Fort Worth-Arlington; Dallas County; Addison'], ['90610', 'zip', '74966; OK; Fort Smith; LeFlore County; Wister'], ['9061', 'city', 'Mesa; CO; Grand Junction; Mesa County'], ['90609', 'zip', '74965; OK; Adair County; Westville'], ['90608', 'zip', '74964; OK; Delaware County; West Siloam Springs'], ['90607', 'zip', '74963; OK; McCurtain County; Watson'], ['90606', 'zip', '74962; OK; Fort Smith; Sequoyah County; Vian'], ['90605', 'zip', '74960; OK; Adair County; Stilwell'], ['90604', 'zip', '74959; OK; Fort Smith; LeFlore County; Spiro'], ['90603', 'zip', '74957; OK; McCurtain County; Smithville'], ['90602', 'zip', '74956; OK; Fort Smith; LeFlore County; Shady Point'], ['90601', 'zip', '74955; OK; Fort Smith; Sequoyah County; Sallisaw'], ['90600', 'zip', '74954; OK; Fort Smith; Sequoyah County; Roland'], ['9060', 'city', 'Merritt; MI; Cadillac; Missaukee County'], ['906', 'county', 'Cabell County; WV; Huntington-Ashland'], ['90599', 'zip', '74953; OK; Fort Smith; LeFlore County; Poteau'], ['90598', 'zip', '74951; OK; Fort Smith; LeFlore County; Panama'], ['90597', 'zip', '74949; OK; Fort Smith; LeFlore County; Muse'], ['90596', 'zip', '74948; OK; Fort Smith; Sequoyah County; Muldrow'], ['90595', 'zip', '74947; OK; Fort Smith; LeFlore County; Monroe'], ['90593', 'zip', '74945; OK; Tahlequah; Cherokee County; Cookson'], ['90592', 'zip', '74944; OK; Haskell County; McCurtain'], ['90590', 'zip', '74942; OK; Fort Smith; LeFlore County; Leflore'], ['9059', 'city', 'Mercer; MO; Mercer County'], ['90589', 'zip', '74941; OK; Haskell County; Keota'], ['90588', 'zip', '74940; OK; Fort Smith; LeFlore County; Howe'], ['90587', 'zip', '74939; OK; Fort Smith; LeFlore County; Hodgen'], ['90586', 'zip', '74937; OK; Fort Smith; LeFlore County; Heavener'], ['90585', 'zip', '74936; OK; Fort Smith; Sequoyah County; Gans'], ['90584', 'zip', '74935; OK; Fort Smith; LeFlore County; Fanshawe'], ['90583', 'zip', '74932; OK; Fort Smith; LeFlore County; Rock Island'], ['90582', 'zip', '74931; OK; Adair County; Bunch'], ['90581', 'zip', '74930; OK; Fort Smith; LeFlore County; Bokoshe'], ['90580', 'zip', '74902; OK; Fort Smith; LeFlore County; Pocola'], ['9058', 'city', 'Mentone; CA; Riverside-San Bernardino-Ontario; San Bernardino County'], ['90579', 'zip', '74901; OK; Fort Smith; LeFlore County; Arkoma'], ['90578', 'zip', '74884; OK; Seminole County; Wewoka'], ['90577', 'zip', '74883; OK; Hughes County; Wetumka'], ['90576', 'zip', '74881; OK; Oklahoma City; Oklahoma County; Harrah'], ['90575', 'zip', '74880; OK; Okfuskee County; Weleetka'], ['90574', 'zip', '74878; OK; Shawnee; Pottawatomie County; Wanette'], ['90573', 'zip', '74875; OK; Oklahoma City; Lincoln County; Tryon'], ['90572', 'zip', '74873; OK; Shawnee; Pottawatomie County; Tecumseh'], ['90571', 'zip', '74872; OK; Garvin County; Stratford'], ['90570', 'zip', '74871; OK; Ada; Pontotoc County; Stonewall'], ['9057', 'city', 'Mendon; MA; Worcester; Worcester County'], ['90569', 'zip', '74869; OK; Oklahoma City; Lincoln County; Sparks'], ['90568', 'zip', '74868; OK; Seminole County; Seminole'], ['90567', 'zip', '74867; OK; Seminole County; Sasakwa'], ['90566', 'zip', '74866; OK; Shawnee; Pottawatomie County; Saint Louis'], ['90565', 'zip', '74865; OK; Ada; Pontotoc County; Roff'], ['90564', 'zip', '74864; OK; Oklahoma City; Lincoln County; Prague'], ['90563', 'zip', '74860; OK; Okfuskee County; Paden'], ['90562', 'zip', '74859; OK; Okfuskee County; Okemah'], ['90561', 'zip', '74857; OK; Oklahoma City; Cleveland County; Norman'], ['90560', 'zip', '74856; OK; Johnston County; Mill Creek'], ['9056', 'city', 'Leona; TX; Leon County'], ['90559', 'zip', '74855; OK; Oklahoma City; Lincoln County; Meeker'], ['90558', 'zip', '74854; OK; Shawnee; Pottawatomie County; Maud'], ['90557', 'zip', '74852; OK; Shawnee; Pottawatomie County; Tribbey'], ['90556', 'zip', '74851; OK; Shawnee; Pottawatomie County; McLoud'], ['90555', 'zip', '74850; OK; Hughes County; Lamar'], ['90554', 'zip', '74849; OK; Seminole County; Konawa'], ['90553', 'zip', '74848; OK; Hughes County; Holdenville'], ['90552', 'zip', '74845; OK; McIntosh County; Hanna'], ['90551', 'zip', '74844; OK; Ada; Pontotoc County; Francis'], ['90550', 'zip', '74843; OK; Ada; Pontotoc County; Fitzhugh'], ['9055', 'city', 'Leon; KS; Wichita; Butler County'], ['90548', 'zip', '74840; OK; Shawnee; Pottawatomie County; Earlsboro'], ['90547', 'zip', '74839; OK; Hughes County; Dustin'], ['90546', 'zip', '74837; OK; Seminole County; Cromwell'], ['90545', 'zip', '74836; OK; Ada; Pontotoc County; Stonewall'], ['90544', 'zip', '74834; OK; Oklahoma City; Lincoln County; Chandler'], ['90543', 'zip', '74833; OK; Okfuskee County; Castle'], ['90542', 'zip', '74832; OK; Oklahoma City; Lincoln County; Carney'], ['90541', 'zip', '74831; OK; Oklahoma City; McClain County; Byars'], ['90540', 'zip', '74830; OK; Seminole County; Bowlegs'], ['9054', 'city', 'Lenox; TN; Dyersburg; Dyer County'], ['90539', 'zip', '74829; OK; Okfuskee County; Paden'], ['90538', 'zip', '74827; OK; Hughes County; Atwood'], ['90537', 'zip', '74826; OK; Shawnee; Pottawatomie County; Asher'], ['90536', 'zip', '74825; OK; Ada; Pontotoc County; Allen'], ['90535', 'zip', '74824; OK; Oklahoma City; Lincoln County; Agra'], ['90533', 'zip', '74820; OK; Ada; Pontotoc County'], ['90532', 'zip', '74818; OK; Seminole County; Seminole'], ['90531', 'zip', '74804; OK; Shawnee; Pottawatomie County'], ['90530', 'zip', '74802; OK; Shawnee; Pottawatomie County'], ['90529', 'zip', '74801; OK; Shawnee; Pottawatomie County'], ['90528', 'zip', '74766; OK; McCurtain County; Wright City'], ['90527', 'zip', '74764; OK; McCurtain County; Valliant'], ['90526', 'zip', '74761; OK; Choctaw County; Fort Towson'], ['90525', 'zip', '74760; OK; Choctaw County; Spencerville'], ['90524', 'zip', '74759; OK; Choctaw County; Soper'], ['90523', 'zip', '74756; OK; Choctaw County; Sawyer'], ['90522', 'zip', '74755; OK; McCurtain County; Rufe'], ['90521', 'zip', '74754; OK; McCurtain County; Ringold'], ['90520', 'zip', '74753; OK; Durant; Bryan County; Cartwright'], ['90518', 'zip', '74750; OK; McCurtain County; Millerton'], ['90517', 'zip', '74748; OK; Durant; Bryan County; Kenefic'], ['90515', 'zip', '74745; OK; McCurtain County; Idabel'], ['90514', 'zip', '74743; OK; Choctaw County; Hugo'], ['90513', 'zip', '74741; OK; Durant; Bryan County; Kemp'], ['90512', 'zip', '74740; OK; McCurtain County; Haworth'], ['90511', 'zip', '74738; OK; Choctaw County; Hugo'], ['90509', 'zip', '74736; OK; McCurtain County; Garvin'], ['90508', 'zip', '74735; OK; Choctaw County; Fort Towson'], ['90507', 'zip', '74734; OK; McCurtain County; Eagletown'], ['90506', 'zip', '74733; OK; Durant; Bryan County; Colbert'], ['90505', 'zip', '74731; OK; Durant; Bryan County; Cartwright'], ['90504', 'zip', '74730; OK; Durant; Bryan County; Calera'], ['90503', 'zip', '74729; OK; Durant; Bryan County; Caddo'], ['90502', 'zip', '74728; OK; McCurtain County; Broken Bow'], ['90501', 'zip', '74727; OK; Choctaw County; Boswell'], ['90500', 'zip', '74726; OK; Durant; Bryan County; Bokchito'], ['9050', 'city', 'Pine Hill; AL; Wilcox County'], ['905', 'county', 'Butler County; PA; Pittsburgh'], ['90499', 'zip', '74724; OK; McCurtain County; Bethel'], ['90498', 'zip', '74723; OK; Durant; Bryan County; Bennington'], ['90497', 'zip', '74722; OK; McCurtain County; Battiest'], ['90496', 'zip', '74721; OK; Durant; Bryan County; Bokchito'], ['90495', 'zip', '74720; OK; Durant; Bryan County; Achille'], ['90493', 'zip', '74701; OK; Durant; Bryan County'], ['90492', 'zip', '74653; OK; Ponca City; Kay County; Tonkawa'], ['90491', 'zip', '74652; OK; Tulsa; Osage County; Shidler'], ['90490', 'zip', '74651; OK; Noble County; Red Rock'], ['90489', 'zip', '74650; OK; Tulsa; Pawnee County; Ralston'], ['90488', 'zip', '74647; OK; Ponca City; Kay County; Newkirk'], ['90487', 'zip', '74646; OK; Ponca City; Kay County; Nardin'], ['90486', 'zip', '74644; OK; Noble County; Marland'], ['90485', 'zip', '74643; OK; Grant County; Lamont'], ['90484', 'zip', '74641; OK; Ponca City; Kay County; Kaw City'], ['90483', 'zip', '74640; OK; Enid; Garfield County; Hunter'], ['90482', 'zip', '74637; OK; Tulsa; Osage County; Fairfax'], ['90481', 'zip', '74636; OK; Grant County; Deer Creek'], ['90480', 'zip', '74633; OK; Tulsa; Osage County; Burbank'], ['90479', 'zip', '74632; OK; Ponca City; Kay County; Braman'], ['90478', 'zip', '74631; OK; Ponca City; Kay County; Blackwell'], ['90477', 'zip', '74630; OK; Noble County; Billings'], ['90476', 'zip', '74604; OK; Ponca City; Kay County'], ['90474', 'zip', '74601; OK; Ponca City; Kay County'], ['90473', 'zip', '74578; OK; Latimer County; Wilburton'], ['90472', 'zip', '74577; OK; Fort Smith; LeFlore County; Whitesboro'], ['90471', 'zip', '74576; OK; Atoka County; Wardville'], ['90470', 'zip', '74574; OK; Pushmataha County; Tuskahoma'], ['90469', 'zip', '74572; OK; Coal County; Tupelo'], ['90468', 'zip', '74571; OK; Fort Smith; LeFlore County; Talihina'], ['90467', 'zip', '74570; OK; Hughes County; Stuart'], ['90466', 'zip', '74569; OK; Atoka County; Stringtown'], ['90465', 'zip', '74567; OK; Pushmataha County; Snow'], ['90464', 'zip', '74565; OK; McAlester; Pittsburg County; Savanna'], ['90463', 'zip', '74563; OK; Latimer County; Red Oak'], ['90462', 'zip', '74562; OK; Pushmataha County; Rattan'], ['90461', 'zip', '74561; OK; McAlester; Pittsburg County; Quinton'], ['90460', 'zip', '74560; OK; McAlester; Pittsburg County; Kiowa'], ['90459', 'zip', '74559; OK; Latimer County; Wilburton'], ['90458', 'zip', '74558; OK; Pushmataha County; Nashoba'], ['90457', 'zip', '74557; OK; Pushmataha County; Moyers'], ['90455', 'zip', '74555; OK; Atoka County; Lane'], ['90454', 'zip', '74554; OK; McAlester; Pittsburg County; Krebs'], ['90453', 'zip', '74553; OK; McAlester; Pittsburg County; Kiowa'], ['90452', 'zip', '74552; OK; Haskell County; Kinta'], ['90451', 'zip', '74549; OK; Pushmataha County; Honobia'], ['90450', 'zip', '74547; OK; McAlester; Pittsburg County; Hartshorne'], ['90449', 'zip', '74546; OK; McAlester; Pittsburg County; Hartshorne'], ['90448', 'zip', '74545; OK; Latimer County; Wilburton'], ['90447', 'zip', '74543; OK; Pushmataha County; Finley'], ['90445', 'zip', '74540; OK; Pushmataha County; Daisy'], ['90444', 'zip', '74538; OK; Coal County; Coalgate'], ['90443', 'zip', '74536; OK; Pushmataha County; Clayton'], ['90441', 'zip', '74534; OK; Coal County; Tupelo'], ['90440', 'zip', '74533; OK; Atoka County; Caney'], ['90439', 'zip', '74531; OK; Hughes County; Calvin'], ['90438', 'zip', '74530; OK; Johnston County; Bromide'], ['90436', 'zip', '74528; OK; McAlester; Pittsburg County; Blanco'], ['90435', 'zip', '74525; OK; Atoka County; Atoka'], ['90434', 'zip', '74523; OK; Pushmataha County; Antlers'], ['90433', 'zip', '74522; OK; McAlester; Pittsburg County; Alderson'], ['90431', 'zip', '74502; OK; McAlester; Pittsburg County'], ['90430', 'zip', '74501; OK; McAlester; Pittsburg County'], ['9043', 'city', 'North Babylon; NY; New York-Newark-Jersey City; Suffolk County'], ['90428', 'zip', '74472; OK; Haskell County; Whitefield'], ['90427', 'zip', '74471; OK; Tahlequah; Cherokee County; Welling'], ['90426', 'zip', '74470; OK; Muskogee; Muskogee County; Webbers Falls'], ['90425', 'zip', '74469; OK; Muskogee; Muskogee County; Warner'], ['90424', 'zip', '74468; OK; Muskogee; Muskogee County; Wainwright'], ['90423', 'zip', '74467; OK; Tulsa; Wagoner County; Wagoner'], ['90422', 'zip', '74465; OK; Tahlequah; Cherokee County'], ['90421', 'zip', '74464; OK; Tahlequah; Cherokee County'], ['90420', 'zip', '74463; OK; Muskogee; Muskogee County; Taft'], ['90419', 'zip', '74462; OK; McIntosh County; Eufaula'], ['90417', 'zip', '74460; OK; Tulsa; Okmulgee County; Schulter'], ['90414', 'zip', '74457; OK; Tahlequah; Cherokee County; Scraper'], ['90413', 'zip', '74456; OK; Tulsa; Okmulgee County; Okmulgee'], ['90412', 'zip', '74455; OK; McIntosh County; Porum'], ['90411', 'zip', '74454; OK; Tulsa; Wagoner County; Porter'], ['90410', 'zip', '74452; OK; Mayes County; Peggs'], ['90409', 'zip', '74451; OK; Tahlequah; Cherokee County; Pettit'], ['90408', 'zip', '74450; OK; Muskogee; Muskogee County; Oktaha'], ['90407', 'zip', '74447; OK; Tulsa; Okmulgee County; Okmulgee'], ['90405', 'zip', '74445; OK; Tulsa; Okmulgee County; Morris'], ['90404', 'zip', '74444; OK; Tahlequah; Cherokee County'], ['90403', 'zip', '74442; OK; McAlester; Pittsburg County; Indianola'], ['90402', 'zip', '74441; OK; Tahlequah; Cherokee County; Hulbert'], ['90400', 'zip', '74438; OK; McIntosh County; Checotah'], ['9040', 'city', 'Normandy; TN; Tullahoma-Manchester; Coffee County'], ['904', 'county', 'Butler County; OH; Cincinnati'], ['90399', 'zip', '74437; OK; Tulsa; Okmulgee County; Henryetta'], ['90398', 'zip', '74436; OK; Muskogee; Muskogee County; Haskell'], ['90397', 'zip', '74435; OK; Fort Smith; Sequoyah County; Gore'], ['90396', 'zip', '74434; OK; Muskogee; Muskogee County; Fort Gibson'], ['90395', 'zip', '74432; OK; McIntosh County; Eufaula'], ['90394', 'zip', '74431; OK; Tulsa; Okmulgee County; Dewar'], ['90393', 'zip', '74430; OK; McAlester; Pittsburg County; Crowder'], ['90392', 'zip', '74429; OK; Tulsa; Wagoner County; Coweta'], ['90391', 'zip', '74428; OK; McIntosh County; Council Hill'], ['90390', 'zip', '74427; OK; Tahlequah; Cherokee County; Cookson'], ['9039', 'city', 'Norfolk; MA; Boston-Cambridge-Newton; Norfolk County'], ['90389', 'zip', '74426; OK; McIntosh County; Checotah'], ['90388', 'zip', '74425; OK; McAlester; Pittsburg County; Canadian'], ['90387', 'zip', '74423; OK; Muskogee; Muskogee County; Braggs'], ['90386', 'zip', '74422; OK; Muskogee; Muskogee County'], ['90385', 'zip', '74421; OK; Tulsa; Okmulgee County; Beggs'], ['90384', 'zip', '74403; OK; Muskogee; Muskogee County'], ['90382', 'zip', '74401; OK; Muskogee; Muskogee County'], ['90381', 'zip', '74370; OK; Miami; Ottawa County; Wyandotte'], ['90380', 'zip', '74369; OK; Craig County; Welch'], ['90378', 'zip', '74367; OK; Mayes County; Strang'], ['90377', 'zip', '74366; OK; Mayes County; Spavinaw'], ['90376', 'zip', '74365; OK; Mayes County; Salina'], ['90375', 'zip', '74364; OK; Delaware County; Rose'], ['90374', 'zip', '74363; OK; Miami; Ottawa County; Quapaw'], ['90372', 'zip', '74361; OK; Mayes County; Pryor'], ['90371', 'zip', '74360; OK; Miami; Ottawa County; Picher'], ['90370', 'zip', '74359; OK; Delaware County; Oaks'], ['90369', 'zip', '74358; OK; Miami; Ottawa County'], ['90367', 'zip', '74354; OK; Miami; Ottawa County'], ['90366', 'zip', '74352; OK; Mayes County; Locust Grove'], ['90365', 'zip', '74350; OK; Mayes County; Langley'], ['90364', 'zip', '74349; OK; Craig County; Ketchum'], ['90363', 'zip', '74347; OK; Delaware County; Kansas'], ['90362', 'zip', '74346; OK; Delaware County; Jay'], ['90360', 'zip', '74344; OK; Delaware County; Grove'], ['9036', 'city', 'Norco; LA; New Orleans-Metairie; Saint Charles Parish'], ['90359', 'zip', '74343; OK; Miami; Ottawa County; Fairland'], ['90358', 'zip', '74342; OK; Delaware County; Eucha'], ['90357', 'zip', '74340; OK; Mayes County; Disney'], ['90356', 'zip', '74339; OK; Miami; Ottawa County; Commerce'], ['90355', 'zip', '74338; OK; Delaware County; Kansas'], ['90354', 'zip', '74337; OK; Mayes County; Chouteau'], ['90352', 'zip', '74333; OK; Craig County; Bluejacket'], ['90351', 'zip', '74332; OK; Mayes County; Big Cabin'], ['90350', 'zip', '74331; OK; Delaware County; Afton'], ['9035', 'city', 'Noma; FL; Holmes County'], ['90349', 'zip', '74330; OK; Mayes County; Adair'], ['90348', 'zip', '74301; OK; Craig County; Vinita'], ['9033', 'city', 'Melvin; KY; Floyd County'], ['90323', 'zip', '74146; OK; Tulsa; Tulsa County'], ['90322', 'zip', '74145; OK; Tulsa; Tulsa County'], ['90320', 'zip', '74137; OK; Tulsa; Tulsa County'], ['90319', 'zip', '74136; OK; Tulsa; Tulsa County'], ['90318', 'zip', '74135; OK; Tulsa; Tulsa County'], ['90317', 'zip', '74134; OK; Tulsa; Tulsa County'], ['90316', 'zip', '74133; OK; Tulsa; Tulsa County'], ['90315', 'zip', '74132; OK; Tulsa; Tulsa County'], ['90314', 'zip', '74131; OK; Tulsa; Creek County; Sapulpa'], ['90313', 'zip', '74130; OK; Tulsa; Tulsa County; Turley'], ['90312', 'zip', '74129; OK; Tulsa; Tulsa County'], ['90311', 'zip', '74128; OK; Tulsa; Tulsa County'], ['90310', 'zip', '74127; OK; Tulsa; Tulsa County'], ['9031', 'city', 'Medway; ME; Bangor; Penobscot County'], ['90309', 'zip', '74126; OK; Tulsa; Tulsa County'], ['90307', 'zip', '74120; OK; Tulsa; Tulsa County'], ['90306', 'zip', '74119; OK; Tulsa; Tulsa County'], ['90305', 'zip', '74117; OK; Tulsa; Tulsa County'], ['90304', 'zip', '74116; OK; Tulsa; Rogers County; Catoosa'], ['90303', 'zip', '74115; OK; Tulsa; Tulsa County'], ['90302', 'zip', '74114; OK; Tulsa; Tulsa County'], ['90301', 'zip', '74112'], ['90300', 'zip', '74110; OK; Tulsa; Tulsa County'], ['9030', 'city', 'Medusa; NY; Albany-Schenectady-Troy; Albany County'], ['903', 'county', 'Butler County; NE'], ['90299', 'zip', '74108; OK; Tulsa; Tulsa County'], ['90298', 'zip', '74107; OK; Tulsa; Tulsa County'], ['90297', 'zip', '74106; OK; Tulsa; Tulsa County'], ['90296', 'zip', '74105'], ['90295', 'zip', '74104; OK; Tulsa; Tulsa County'], ['90294', 'zip', '74103; OK; Tulsa; Tulsa County'], ['90291', 'zip', '74085; OK; Stillwater; Payne County; Yale'], ['90290', 'zip', '74084; OK; Tulsa; Osage County; Wynona'], ['9029', 'city', 'Medora; IN; Seymour; Jackson County'], ['90289', 'zip', '74083; OK; Nowata County; Wann'], ['90288', 'zip', '74082; OK; Bartlesville; Washington County; Vera'], ['90287', 'zip', '74081; OK; Tulsa; Pawnee County; Terlton'], ['90286', 'zip', '74080; OK; Tulsa; Rogers County; Talala'], ['90285', 'zip', '74079; OK; Oklahoma City; Lincoln County; Stroud'], ['90281', 'zip', '74075; OK; Stillwater; Payne County'], ['90280', 'zip', '74074; OK; Stillwater; Payne County'], ['90279', 'zip', '74073; OK; Tulsa; Tulsa County; Sperry'], ['90278', 'zip', '74072; OK; Nowata County; South Coffeyville'], ['90276', 'zip', '74070; OK; Tulsa; Osage County; Skiatook'], ['90273', 'zip', '74066; OK; Tulsa; Creek County; Sapulpa'], ['90272', 'zip', '74063; OK; Tulsa; Tulsa County; Sand Springs'], ['90271', 'zip', '74062; OK; Stillwater; Payne County; Ripley'], ['90270', 'zip', '74061; OK; Bartlesville; Washington County; Ramona'], ['9027', 'city', 'Ripley; WV; Jackson County'], ['90269', 'zip', '74060; OK; Tulsa; Osage County; Prue'], ['90268', 'zip', '74059; OK; Stillwater; Payne County; Perkins'], ['90267', 'zip', '74058; OK; Tulsa; Pawnee County; Pawnee'], ['90266', 'zip', '74056; OK; Tulsa; Osage County; Pawhuska'], ['90265', 'zip', '74055; OK; Tulsa; Tulsa County; Owasso'], ['90264', 'zip', '74054; OK; Tulsa; Osage County; Hominy'], ['90263', 'zip', '74053; OK; Tulsa; Rogers County; Oologah'], ['90262', 'zip', '74052; OK; Tulsa; Creek County; Oilton'], ['90261', 'zip', '74051; OK; Bartlesville; Washington County; Ochelata'], ['90259', 'zip', '74048; OK; Nowata County; Nowata'], ['90258', 'zip', '74047; OK; Tulsa; Creek County; Mounds'], ['90256', 'zip', '74045; OK; Tulsa; Pawnee County; Maramec'], ['90255', 'zip', '74044; OK; Tulsa; Creek County; Mannford'], ['90253', 'zip', '74042; OK; Nowata County; Lenapah'], ['90252', 'zip', '74041; OK; Tulsa; Creek County; Kiefer'], ['90251', 'zip', '74039; OK; Tulsa; Creek County; Kellyville'], ['90250', 'zip', '74038; OK; Tulsa; Pawnee County; Jennings'], ['9025', 'city', 'Ringold; OK; McCurtain County'], ['90249', 'zip', '74037'], ['90248', 'zip', '74036; OK; Tulsa; Rogers County; Inola'], ['90247', 'zip', '74035; OK; Tulsa; Osage County; Hominy'], ['90246', 'zip', '74034; OK; Tulsa; Pawnee County; Hallett'], ['90245', 'zip', '74033; OK; Tulsa; Tulsa County; Glenpool'], ['90244', 'zip', '74032; OK; Stillwater; Payne County; Glencoe'], ['90242', 'zip', '74030; OK; Tulsa; Creek County; Drumright'], ['90241', 'zip', '74029; OK; Bartlesville; Washington County; Dewey'], ['90240', 'zip', '74028; OK; Tulsa; Creek County; Depew'], ['9024', 'city', 'Pewee Valley; KY; Louisville/Jefferson County; Oldham County'], ['90239', 'zip', '74027; OK; Nowata County; Delaware'], ['90238', 'zip', '74026; OK; Oklahoma City; Lincoln County; Davenport'], ['90237', 'zip', '74023; OK; Stillwater; Payne County; Cushing'], ['90236', 'zip', '74022; OK; Bartlesville; Washington County; Copan'], ['90235', 'zip', '74021; OK; Tulsa; Tulsa County; Collinsville'], ['90234', 'zip', '74020; OK; Tulsa; Pawnee County; Cleveland'], ['90233', 'zip', '74019; OK; Tulsa; Rogers County; Claremore'], ['90231', 'zip', '74017; OK; Tulsa; Rogers County; Claremore'], ['90230', 'zip', '74016; OK; Tulsa; Rogers County; Chelsea'], ['9023', 'city', 'Pevely; MO; St. Louis; Jefferson County'], ['90229', 'zip', '74015; OK; Tulsa; Rogers County; Catoosa'], ['90228', 'zip', '74014'], ['90226', 'zip', '74012'], ['90225', 'zip', '74011; OK; Tulsa; Tulsa County; Broken Arrow'], ['90224', 'zip', '74010; OK; Tulsa; Creek County; Bristow'], ['90223', 'zip', '74008'], ['90222', 'zip', '74006; OK; Bartlesville; Washington County'], ['90219', 'zip', '74003; OK; Bartlesville; Washington County'], ['90218', 'zip', '74002; OK; Tulsa; Osage County; Barnsdall'], ['90217', 'zip', '74001; OK; Tulsa; Osage County; Avant'], ['90216', 'zip', '73951; OK; Guymon; Texas County; Tyrone'], ['90215', 'zip', '73950; OK; Beaver County; Turpin'], ['90214', 'zip', '73949; OK; Guymon; Texas County; Texhoma'], ['90211', 'zip', '73945; OK; Guymon; Texas County; Hooker'], ['90210', 'zip', '73944; OK; Guymon; Texas County; Hardesty'], ['90209', 'zip', '73942; OK; Guymon; Texas County'], ['90208', 'zip', '73939; OK; Guymon; Texas County; Goodwell'], ['90207', 'zip', '73938; OK; Beaver County; Forgan'], ['90204', 'zip', '73932; OK; Beaver County; Beaver'], ['90203', 'zip', '73931; OK; Beaver County; Balko'], ['90202', 'zip', '73901; OK; Guymon; Texas County; Adams'], ['90201', 'zip', '73860; OK; Woods County; Waynoka'], ['90200', 'zip', '73859; OK; Dewey County; Vici'], ['9020', 'city', 'Nipomo; CA; San Luis Obispo-Paso Robles-Arroyo Grande; San Luis Obispo County'], ['902', 'county', 'Butler County; MO; Poplar Bluff'], ['90199', 'zip', '73858; OK; Ellis County; Shattuck'], ['90198', 'zip', '73857; OK; Woodward; Woodward County; Sharon'], ['90197', 'zip', '73855; OK; Harper County; Rosston'], ['90196', 'zip', '73853; OK; Woodward; Woodward County; Mutual'], ['90195', 'zip', '73852; OK; Woodward; Woodward County; Mooreland'], ['90194', 'zip', '73851; OK; Harper County; May'], ['90193', 'zip', '73848; OK; Harper County; Laverne'], ['90192', 'zip', '73844; OK; Beaver County; Beaver'], ['90191', 'zip', '73843; OK; Ellis County; Gage'], ['90190', 'zip', '73842; OK; Woods County; Freedom'], ['9019', 'city', 'Nicolaus; CA; Yuba City; Sutter County'], ['90189', 'zip', '73841; OK; Woodward; Woodward County; Fort Supply'], ['90188', 'zip', '73840; OK; Ellis County; Fargo'], ['90187', 'zip', '73838; OK; Major County; Chester'], ['90185', 'zip', '73834; OK; Harper County; Buffalo'], ['90184', 'zip', '73832; OK; Ellis County; Arnett'], ['90182', 'zip', '73801; OK; Woodward; Woodward County'], ['90181', 'zip', '73773; OK; Enid; Garfield County; Waukomis'], ['90180', 'zip', '73772; OK; Blaine County; Watonga'], ['9018', 'city', 'Newton Hamilton; PA; Lewistown; Mifflin County'], ['90179', 'zip', '73771; OK; Grant County; Wakita'], ['90178', 'zip', '73770; OK; Blaine County; Longdale'], ['90177', 'zip', '73768; OK; Major County; Ringwood'], ['90176', 'zip', '73766; OK; Grant County; Pond Creek'], ['90175', 'zip', '73764; OK; Kingfisher County; Omega'], ['90174', 'zip', '73763; OK; Blaine County; Okeene'], ['90173', 'zip', '73762; OK; Kingfisher County; Okarche'], ['90172', 'zip', '73761; OK; Grant County; Nash'], ['90171', 'zip', '73760; OK; Major County; Meno'], ['90170', 'zip', '73759; OK; Grant County; Medford'], ['90169', 'zip', '73758; OK; Grant County; Manchester'], ['90168', 'zip', '73757; OK; Enid; Garfield County; Lucien'], ['90167', 'zip', '73756; OK; Kingfisher County; Loyal'], ['90166', 'zip', '73755; OK; Blaine County; Longdale'], ['90165', 'zip', '73754; OK; Enid; Garfield County; Lahoma'], ['90164', 'zip', '73753; OK; Enid; Garfield County; Kremlin'], ['90163', 'zip', '73750; OK; Kingfisher County; Kingfisher'], ['90162', 'zip', '73749; OK; Alfalfa County; Jet'], ['90161', 'zip', '73747; OK; Major County; Isabella'], ['90159', 'zip', '73744; OK; Blaine County; Hitchcock'], ['90158', 'zip', '73743; OK; Enid; Garfield County; Hillsdale'], ['90157', 'zip', '73742; OK; Kingfisher County; Hennessey'], ['90156', 'zip', '73741; OK; Alfalfa County; Helena'], ['90155', 'zip', '73739; OK; Alfalfa County; Goltry'], ['90154', 'zip', '73738; OK; Enid; Garfield County; Garber'], ['90153', 'zip', '73737; OK; Major County; Fairview'], ['90152', 'zip', '73736; OK; Enid; Garfield County; Fairmont'], ['90151', 'zip', '73735; OK; Enid; Garfield County; Drummond'], ['90150', 'zip', '73734; OK; Kingfisher County; Dover'], ['9015', 'city', 'Newport; DE; Philadelphia-Camden-Wilmington; New Castle County'], ['90149', 'zip', '73733; OK; Enid; Garfield County; Douglas'], ['90148', 'zip', '73731; OK; Woods County; Dacoma'], ['90147', 'zip', '73730; OK; Enid; Garfield County; Covington'], ['90146', 'zip', '73729; OK; Major County; Cleo Springs'], ['90145', 'zip', '73728; OK; Alfalfa County; Cherokee'], ['90144', 'zip', '73727; OK; Enid; Garfield County; Carrier'], ['90143', 'zip', '73726; OK; Alfalfa County; Carmen'], ['90142', 'zip', '73724; OK; Blaine County; Canton'], ['90141', 'zip', '73722; OK; Alfalfa County; Burlington'], ['90140', 'zip', '73720; OK; Kingfisher County; Hennessey'], ['9014', 'city', 'McCloud; CA; Siskiyou County'], ['90139', 'zip', '73719; OK; Alfalfa County; Amorita'], ['90138', 'zip', '73718; OK; Major County; Ames'], ['90137', 'zip', '73717; OK; Woods County; Alva'], ['90136', 'zip', '73716; OK; Alfalfa County; Aline'], ['90133', 'zip', '73703; OK; Enid; Garfield County'], ['90131', 'zip', '73701; OK; Enid; Garfield County'], ['90130', 'zip', '73673; OK; Greer County; Willow'], ['9013', 'city', 'McCammon; ID; Pocatello; Bannock County'], ['90129', 'zip', '73669; OK; Weatherford; Custer County; Thomas'], ['90128', 'zip', '73668; OK; Elk City; Beckham County; Texola'], ['90126', 'zip', '73666; OK; Roger Mills County; Sweetwater'], ['90125', 'zip', '73664; OK; Washita County; Sentinel'], ['90124', 'zip', '73663; OK; Dewey County; Seiling'], ['90123', 'zip', '73662; OK; Elk City; Beckham County; Sayre'], ['90122', 'zip', '73661; OK; Washita County; Rocky'], ['90118', 'zip', '73655; OK; Kiowa County; Lone Wolf'], ['90117', 'zip', '73654; OK; Dewey County; Leedey'], ['90116', 'zip', '73651; OK; Kiowa County; Hobart'], ['90115', 'zip', '73650; OK; Roger Mills County; Hammon'], ['90113', 'zip', '73647; OK; Washita County; Foss'], ['90112', 'zip', '73646; OK; Blaine County; Fay'], ['90111', 'zip', '73645; OK; Elk City; Beckham County; Erick'], ['90110', 'zip', '73644; OK; Elk City; Beckham County'], ['90108', 'zip', '73641; OK; Washita County; Dill City'], ['90107', 'zip', '73639; OK; Weatherford; Custer County; Custer City'], ['90105', 'zip', '73632; OK; Washita County; New Cordell'], ['90103', 'zip', '73627; OK; Elk City; Beckham County; Carter'], ['90102', 'zip', '73626; OK; Washita County; Canute'], ['90101', 'zip', '73625; OK; Weatherford; Custer County; Butler'], ['90100', 'zip', '73624; OK; Washita County; Burns Flat'], ['901', 'county', 'Butler County; KY; Bowling Green'], ['90099', 'zip', '73622; OK; Washita County; Bessie'], ['90098', 'zip', '73620; OK; Weatherford; Custer County; Arapaho'], ['90097', 'zip', '73601; OK; Weatherford; Custer County; Clinton'], ['90096', 'zip', '73573; OK; Jefferson County; Waurika'], ['90095', 'zip', '73572; OK; Lawton; Cotton County; Walters'], ['90094', 'zip', '73571; OK; Harmon County; Vinson'], ['90093', 'zip', '73570; OK; Tillman County; Tipton'], ['90092', 'zip', '73569; OK; Jefferson County; Terral'], ['90091', 'zip', '73568; OK; Lawton; Cotton County; Temple'], ['90090', 'zip', '73567; OK; Lawton; Comanche County; Sterling'], ['9009', 'city', 'Reynolds; ND; Traill County'], ['90089', 'zip', '73566; OK; Kiowa County; Snyder'], ['90088', 'zip', '73565; OK; Jefferson County; Ryan'], ['90087', 'zip', '73564; OK; Kiowa County; Roosevelt'], ['90086', 'zip', '73562; OK; Lawton; Cotton County; Randlett'], ['90084', 'zip', '73560; OK; Altus; Jackson County; Olustee'], ['90083', 'zip', '73559; OK; Kiowa County; Mountain Park'], ['90081', 'zip', '73557; OK; Lawton; Comanche County; Medicine Park'], ['90080', 'zip', '73556; OK; Altus; Jackson County; Martha'], ['90079', 'zip', '73555; OK; Tillman County; Manitou'], ['90078', 'zip', '73554; OK; Greer County; Mangum'], ['90077', 'zip', '73553; OK; Tillman County; Loveland'], ['90076', 'zip', '73552; OK; Lawton; Comanche County; Indiahoma'], ['90075', 'zip', '73551; OK; Tillman County; Hollister'], ['90074', 'zip', '73550; OK; Harmon County; Hollis'], ['90073', 'zip', '73549; OK; Altus; Jackson County; Headrick'], ['90072', 'zip', '73548; OK; Duncan; Stephens County; Hastings'], ['90071', 'zip', '73547; OK; Greer County; Granite'], ['90070', 'zip', '73546; OK; Tillman County; Grandfield'], ['9007', 'city', 'Rewey; WI; Madison; Iowa County'], ['90069', 'zip', '73544; OK; Harmon County; Gould'], ['90068', 'zip', '73543; OK; Lawton; Comanche County; Geronimo'], ['90067', 'zip', '73542; OK; Tillman County; Frederick'], ['90066', 'zip', '73541; OK; Lawton; Comanche County; Fletcher'], ['90065', 'zip', '73540; OK; Lawton; Comanche County; Faxon'], ['90064', 'zip', '73539; OK; Altus; Jackson County; Elmer'], ['90063', 'zip', '73538; OK; Lawton; Comanche County; Elgin'], ['90062', 'zip', '73537; OK; Altus; Jackson County; Eldorado'], ['90060', 'zip', '73534; OK; Duncan; Stephens County'], ['90059', 'zip', '73533; OK; Duncan; Stephens County'], ['90058', 'zip', '73532; OK; Altus; Jackson County; East Duke'], ['90057', 'zip', '73531; OK; Lawton; Cotton County; Devol'], ['90056', 'zip', '73530; OK; Tillman County; Davidson'], ['90055', 'zip', '73529; OK; Duncan; Stephens County; Comanche'], ['90054', 'zip', '73528; OK; Lawton; Comanche County; Chattanooga'], ['90053', 'zip', '73527; OK; Lawton; Comanche County; Cache'], ['90052', 'zip', '73526; OK; Altus; Jackson County; Blair'], ['9005', 'city', 'Republic; PA; Pittsburgh; Fayette County'], ['90049', 'zip', '73521; OK; Altus; Jackson County'], ['90048', 'zip', '73520; OK; Jefferson County; Addington'], ['90047', 'zip', '73507; OK; Lawton; Comanche County'], ['90045', 'zip', '73505; OK; Lawton; Comanche County'], ['90042', 'zip', '73501; OK; Lawton; Comanche County'], ['90041', 'zip', '73491; OK; Duncan; Stephens County; Velma'], ['90040', 'zip', '73488; OK; Ardmore; Carter County; Tussy'], ['90039', 'zip', '73487; OK; Ardmore; Carter County; Tatums'], ['90038', 'zip', '73481; OK; Ardmore; Carter County; Ratliff City'], ['90037', 'zip', '73463; OK; Ardmore; Carter County; Wilson'], ['90036', 'zip', '73461; OK; Johnston County; Wapanucka'], ['90035', 'zip', '73460; OK; Johnston County; Tishomingo'], ['90034', 'zip', '73459; OK; Love County; Thackerville'], ['90033', 'zip', '73458; OK; Ardmore; Carter County; Springer'], ['90032', 'zip', '73456; OK; Jefferson County; Ringling'], ['90031', 'zip', '73455; OK; Johnston County; Ravia'], ['90030', 'zip', '73453; OK; Love County; Overbrook'], ['90029', 'zip', '73450; OK; Johnston County; Milburn'], ['90028', 'zip', '73449; OK; Durant; Bryan County; Mead'], ['90027', 'zip', '73448; OK; Love County; Marietta'], ['90026', 'zip', '73447; OK; Johnston County; Mannsville'], ['90025', 'zip', '73446; OK; Marshall County; Madill'], ['90024', 'zip', '73444; OK; Garvin County; Hennepin'], ['90023', 'zip', '73443; OK; Ardmore; Carter County; Lone Grove'], ['90022', 'zip', '73442; OK; Duncan; Stephens County; Loco'], ['90021', 'zip', '73441; OK; Love County; Leon'], ['90020', 'zip', '73440; OK; Marshall County; Lebanon'], ['9002', 'city', 'Rensselaer; MO; Hannibal; Ralls County'], ['90019', 'zip', '73439; OK; Marshall County; Kingston'], ['90018', 'zip', '73438; OK; Ardmore; Carter County; Healdton'], ['90017', 'zip', '73437; OK; Ardmore; Carter County; Graham'], ['90016', 'zip', '73436; OK; Ardmore; Carter County; Gene Autry'], ['90015', 'zip', '73435; OK; Ardmore; Carter County; Fox'], ['90014', 'zip', '73434; OK; Garvin County; Foster'], ['90013', 'zip', '73433; OK; Garvin County; Elmore City'], ['90012', 'zip', '73432; OK; Johnston County; Coleman'], ['90011', 'zip', '73430; OK; Love County; Burneyville'], ['90007', 'zip', '73401; OK; Ardmore; Carter County'], ['900', 'county', 'Butler County; KS; Wichita'], ['90', 'county', 'Nye County; NV; Pahrump'], ['9', 'state', 'California; CA'], ['89993', 'zip', '73179; OK; Oklahoma City; Oklahoma County'], ['89991', 'zip', '73173; OK; Oklahoma City; Oklahoma County'], ['89989', 'zip', '73170; OK; Oklahoma City; Oklahoma County'], ['89988', 'zip', '73169; OK; Oklahoma City; Oklahoma County'], ['89986', 'zip', '73165; OK; Oklahoma City; Oklahoma County'], ['89983', 'zip', '73162'], ['89982', 'zip', '73160'], ['89981', 'zip', '73159; OK; Oklahoma City; Oklahoma County'], ['89974', 'zip', '73151; OK; Oklahoma City; Oklahoma County'], ['89973', 'zip', '73150; OK; Oklahoma City; Oklahoma County'], ['89972', 'zip', '73149; OK; Oklahoma City; Oklahoma County'], ['89965', 'zip', '73142; OK; Oklahoma City; Oklahoma County'], ['89964', 'zip', '73141; OK; Oklahoma City; Oklahoma County; Midwest City'], ['89962', 'zip', '73139; OK; Oklahoma City; Oklahoma County'], ['8996', 'city', 'Newcastle; CA; Sacramento--Roseville--Arden-Arcade; Placer County'], ['89959', 'zip', '73135'], ['89958', 'zip', '73134; OK; Oklahoma City; Oklahoma County'], ['89957', 'zip', '73132'], ['89956', 'zip', '73131; OK; Oklahoma City; Oklahoma County'], ['89955', 'zip', '73130; OK; Oklahoma City; Oklahoma County; Midwest City'], ['89954', 'zip', '73129; OK; Oklahoma City; Oklahoma County'], ['89953', 'zip', '73128; OK; Oklahoma City; Oklahoma County'], ['89952', 'zip', '73127'], ['89947', 'zip', '73122; OK; Oklahoma City; Oklahoma County; Warr Acres'], ['89946', 'zip', '73121; OK; Oklahoma City; Oklahoma County'], ['89945', 'zip', '73120; OK; Oklahoma City; Oklahoma County'], ['89944', 'zip', '73119; OK; Oklahoma City; Oklahoma County'], ['89943', 'zip', '73118; OK; Oklahoma City; Oklahoma County'], ['89942', 'zip', '73117; OK; Oklahoma City; Oklahoma County'], ['89941', 'zip', '73116; OK; Oklahoma City; Oklahoma County'], ['89940', 'zip', '73115'], ['89939', 'zip', '73114; OK; Oklahoma City; Oklahoma County'], ['89937', 'zip', '73112'], ['89936', 'zip', '73111; OK; Oklahoma City; Oklahoma County'], ['89935', 'zip', '73110; OK; Oklahoma City; Oklahoma County; Midwest City'], ['89934', 'zip', '73109; OK; Oklahoma City; Oklahoma County'], ['89933', 'zip', '73108; OK; Oklahoma City; Oklahoma County'], ['89932', 'zip', '73107; OK; Oklahoma City; Oklahoma County'], ['89931', 'zip', '73106'], ['89930', 'zip', '73105; OK; Oklahoma City; Oklahoma County'], ['89929', 'zip', '73104; OK; Oklahoma City; Oklahoma County'], ['89928', 'zip', '73103; OK; Oklahoma City; Oklahoma County'], ['89927', 'zip', '73102; OK; Oklahoma City; Oklahoma County'], ['89925', 'zip', '73099'], ['89924', 'zip', '73098; OK; Garvin County; Wynnewood'], ['89922', 'zip', '73096; OK; Weatherford; Custer County'], ['89921', 'zip', '73095; OK; Oklahoma City; McClain County; Wayne'], ['8992', 'city', 'Mc Connell; IL; Freeport; Stephenson County'], ['89919', 'zip', '73093; OK; Oklahoma City; McClain County; Goldsby'], ['89918', 'zip', '73092; OK; Oklahoma City; Grady County; Verden'], ['89917', 'zip', '73090; OK; Oklahoma City; Canadian County; Union City'], ['89916', 'zip', '73089; OK; Oklahoma City; Grady County; Tuttle'], ['89915', 'zip', '73086; OK; Murray County; Sulphur'], ['89913', 'zip', '73084; OK; Oklahoma City; Oklahoma County'], ['89911', 'zip', '73082; OK; Oklahoma City; Grady County; Rush Springs'], ['89910', 'zip', '73080; OK; Oklahoma City; McClain County; Purcell'], ['8991', 'city', 'Mc Clure; OH; Henry County'], ['89909', 'zip', '73079; OK; Oklahoma City; Grady County; Pocasset'], ['89908', 'zip', '73078; OK; Oklahoma City; Canadian County; Piedmont'], ['89907', 'zip', '73077; OK; Noble County; Perry'], ['89906', 'zip', '73075; OK; Garvin County; Pauls Valley'], ['89905', 'zip', '73074; OK; Garvin County; Paoli'], ['89904', 'zip', '73073; OK; Oklahoma City; Logan County; Orlando'], ['89903', 'zip', '73072'], ['89902', 'zip', '73071'], ['89900', 'zip', '73069; OK; Oklahoma City; Cleveland County; Norman'], ['899', 'county', 'Butler County; IA'], ['89899', 'zip', '73068; OK; Oklahoma City; Cleveland County; Noble'], ['89898', 'zip', '73067; OK; Oklahoma City; Grady County; Ninnekah'], ['89897', 'zip', '73066; OK; Oklahoma City; Oklahoma County; Nicoma Park'], ['89896', 'zip', '73065; OK; Oklahoma City; McClain County; Newcastle'], ['89895', 'zip', '73064; OK; Oklahoma City; Canadian County; Mustang'], ['89894', 'zip', '73063; OK; Oklahoma City; Logan County; Mulhall'], ['89893', 'zip', '73062; OK; Kiowa County; Mountain View'], ['89892', 'zip', '73061; OK; Noble County; Morrison'], ['89891', 'zip', '73059; OK; Oklahoma City; Grady County; Minco'], ['89890', 'zip', '73058; OK; Oklahoma City; Logan County; Meridian'], ['89889', 'zip', '73057; OK; Garvin County; Maysville'], ['89888', 'zip', '73056; OK; Oklahoma City; Logan County; Marshall'], ['89887', 'zip', '73055; OK; Duncan; Stephens County; Marlow'], ['89886', 'zip', '73054; OK; Oklahoma City; Oklahoma County; Luther'], ['89885', 'zip', '73053; OK; Caddo County; Lookeba'], ['89884', 'zip', '73052; OK; Garvin County; Lindsay'], ['89883', 'zip', '73051; OK; Oklahoma City; Cleveland County; Slaughterville'], ['89882', 'zip', '73050; OK; Oklahoma City; Logan County; Coyle'], ['89881', 'zip', '73049; OK; Oklahoma City; Oklahoma County'], ['89880', 'zip', '73048; OK; Caddo County; Hydro'], ['8988', 'city', 'Mayville; WI; Beaver Dam; Dodge County'], ['89879', 'zip', '73047; OK; Oklahoma City; Canadian County; Hinton'], ['89878', 'zip', '73045; OK; Oklahoma City; Oklahoma County; Harrah'], ['89877', 'zip', '73044; OK; Oklahoma City; Logan County; Guthrie'], ['89876', 'zip', '73043; OK; Blaine County; Greenfield'], ['89875', 'zip', '73042; OK; Caddo County; Gracemont'], ['89874', 'zip', '73041; OK; Kiowa County; Gotebo'], ['89873', 'zip', '73040; OK; Blaine County; Geary'], ['89872', 'zip', '73038; OK; Caddo County; Fort Cobb'], ['89871', 'zip', '73036; OK; Oklahoma City; Canadian County; El Reno'], ['89870', 'zip', '73034'], ['8987', 'city', 'Maypearl; TX; Dallas-Fort Worth-Arlington; Ellis County'], ['89869', 'zip', '73033; OK; Caddo County; Eakly'], ['89868', 'zip', '73032; OK; Murray County; Dougherty'], ['89867', 'zip', '73031; OK; Oklahoma City; McClain County; Dibble'], ['89866', 'zip', '73030; OK; Murray County; Davis'], ['89865', 'zip', '73029; OK; Caddo County; Cyril'], ['89864', 'zip', '73028; OK; Oklahoma City; Logan County; Crescent'], ['89863', 'zip', '73027; OK; Oklahoma City; Logan County; Coyle'], ['89862', 'zip', '73026; OK; Oklahoma City; Cleveland County; Norman'], ['89861', 'zip', '73024; OK; Washita County; Corn'], ['8986', 'city', 'Rollins; MT; Lake County'], ['89858', 'zip', '73021; OK; Washita County; Colony'], ['89857', 'zip', '73020; OK; Oklahoma City; Oklahoma County; Choctaw'], ['89856', 'zip', '73019; OK; Oklahoma City; Cleveland County; Norman'], ['89855', 'zip', '73018; OK; Oklahoma City; Grady County; Chickasha'], ['89854', 'zip', '73017; OK; Caddo County; Cement'], ['89853', 'zip', '73016; OK; Kingfisher County; Cashion'], ['89852', 'zip', '73015; OK; Caddo County; Carnegie'], ['89851', 'zip', '73014; OK; Oklahoma City; Canadian County; Calumet'], ['89850', 'zip', '73013'], ['8985', 'city', 'Rollingstone; MN; Winona; Winona County'], ['89849', 'zip', '73011; OK; Oklahoma City; Grady County; Bradley'], ['89848', 'zip', '73010; OK; Oklahoma City; McClain County; Blanchard'], ['89847', 'zip', '73009; OK; Caddo County; Binger'], ['89846', 'zip', '73008; OK; Oklahoma City; Oklahoma County; Bethany'], ['89845', 'zip', '73007; OK; Oklahoma City; Oklahoma County; Edmond'], ['89844', 'zip', '73006; OK; Caddo County; Apache'], ['89843', 'zip', '73005; OK; Caddo County; Anadarko'], ['89842', 'zip', '73004; OK; Oklahoma City; Grady County; Amber'], ['89841', 'zip', '73003'], ['89840', 'zip', '73002; OK; Oklahoma City; Grady County; Alex'], ['8984', 'city', 'Rolling Hills; WY; Converse County'], ['89838', 'zip', '72959; AR; Fayetteville-Springdale-Rogers; Washington County; Winslow'], ['89837', 'zip', '72958; AR; Scott County; Waldron'], ['89835', 'zip', '72956; AR; Fort Smith; Crawford County; Van Buren'], ['89834', 'zip', '72955; AR; Fort Smith; Crawford County; Uniontown'], ['89833', 'zip', '72952; AR; Fort Smith; Crawford County; Rudy'], ['89832', 'zip', '72951; AR; Logan County; Ratcliff'], ['89831', 'zip', '72950; AR; Scott County; Parks'], ['89830', 'zip', '72949; AR; Franklin County; Ozark'], ['8983', 'city', 'Rogersville; TN; Kingsport-Bristol-Bristol; Hawkins County'], ['89829', 'zip', '72948; AR; Fort Smith; Crawford County; Natural Dam'], ['89828', 'zip', '72947; AR; Fort Smith; Crawford County; Mulberry'], ['89827', 'zip', '72946; AR; Fort Smith; Crawford County; Mountainburg'], ['89825', 'zip', '72944; AR; Scott County; Mansfield'], ['89824', 'zip', '72943; AR; Logan County; Magazine'], ['89823', 'zip', '72941; AR; Fort Smith; Sebastian County; Lavaca'], ['89822', 'zip', '72940; AR; Fort Smith; Sebastian County; Huntington'], ['89821', 'zip', '72938; AR; Fort Smith; Sebastian County; Hartford'], ['89820', 'zip', '72937; AR; Fort Smith; Sebastian County; Hackett'], ['89819', 'zip', '72936; AR; Fort Smith; Sebastian County; Greenwood'], ['89818', 'zip', '72935; AR; Fort Smith; Crawford County; Dyer'], ['89817', 'zip', '72934; AR; Fort Smith; Crawford County; Chester'], ['89816', 'zip', '72933; AR; Franklin County; Charleston'], ['89815', 'zip', '72932; AR; Fort Smith; Crawford County; Cedarville'], ['89814', 'zip', '72930; AR; Franklin County; Cecil'], ['89813', 'zip', '72928; AR; Franklin County; Branch'], ['89812', 'zip', '72927; AR; Logan County; Booneville'], ['89811', 'zip', '72926; AR; Scott County; Boles'], ['89810', 'zip', '72923; AR; Fort Smith; Sebastian County; Barling'], ['8981', 'city', 'Rockmart; GA; Cedartown; Polk County'], ['89809', 'zip', '72921; AR; Fort Smith; Crawford County; Alma'], ['89805', 'zip', '72916; AR; Fort Smith; Sebastian County'], ['89802', 'zip', '72908; AR; Fort Smith; Sebastian County'], ['898', 'county', 'Butler County; AL'], ['89799', 'zip', '72904; AR; Fort Smith; Sebastian County'], ['89798', 'zip', '72903; AR; Fort Smith; Sebastian County'], ['89796', 'zip', '72901; AR; Fort Smith; Sebastian County'], ['89795', 'zip', '72865; AR; Logan County; Subiaco'], ['89794', 'zip', '72863; AR; Logan County; Scranton'], ['89792', 'zip', '72858; AR; Russellville; Pope County; Pottsville'], ['89791', 'zip', '72857; AR; Russellville; Yell County; Plainview'], ['89790', 'zip', '72856; AR; Russellville; Pope County; Dover'], ['89789', 'zip', '72855; AR; Logan County; Paris'], ['89788', 'zip', '72854; AR; Johnson County; Ozone'], ['89787', 'zip', '72853; AR; Russellville; Yell County; Ola'], ['89786', 'zip', '72852; AR; Johnson County; Ozone'], ['89785', 'zip', '72851; AR; Logan County; New Blaine'], ['89784', 'zip', '72847; AR; Russellville; Pope County; London'], ['89783', 'zip', '72846; AR; Johnson County; Lamar'], ['89782', 'zip', '72845; AR; Johnson County; Knoxville'], ['89781', 'zip', '72843; AR; Russellville; Pope County; Hector'], ['89780', 'zip', '72842; AR; Russellville; Yell County; Havana'], ['8978', 'city', 'Penn Yan; NY; Rochester; Yates County'], ['89779', 'zip', '72841; AR; Scott County; Harvey'], ['89778', 'zip', '72840; AR; Johnson County; Hartman'], ['89777', 'zip', '72839; AR; Johnson County; Hagarville'], ['89775', 'zip', '72837; AR; Russellville; Pope County; Dover'], ['89774', 'zip', '72835; AR; Logan County; Delaware'], ['89773', 'zip', '72834; AR; Russellville; Yell County; Dardanelle'], ['89772', 'zip', '72833; AR; Russellville; Yell County; Danville'], ['89771', 'zip', '72832; AR; Johnson County; Coal Hill'], ['89770', 'zip', '72830; AR; Johnson County; Clarksville'], ['89767', 'zip', '72827; AR; Russellville; Yell County; Bluffton'], ['89766', 'zip', '72826; AR; Logan County; Magazine'], ['89765', 'zip', '72824; AR; Russellville; Yell County; Belleville'], ['89764', 'zip', '72823; AR; Russellville; Pope County; Atkins'], ['89763', 'zip', '72821; AR; Franklin County; Altus'], ['89759', 'zip', '72802; AR; Russellville; Pope County'], ['89758', 'zip', '72801; AR; Russellville; Pope County'], ['89757', 'zip', '72776; AR; Fayetteville-Springdale-Rogers; Madison County; Witter'], ['89756', 'zip', '72774; AR; Fayetteville-Springdale-Rogers; Washington County; West Fork'], ['89755', 'zip', '72773; AR; Fayetteville-Springdale-Rogers; Madison County; Wesley'], ['89753', 'zip', '72769; AR; Fayetteville-Springdale-Rogers; Washington County; Summers'], ['89752', 'zip', '72768; AR; Fayetteville-Springdale-Rogers; Benton County; Sulphur Springs'], ['89749', 'zip', '72764; AR; Fayetteville-Springdale-Rogers; Washington County; Springdale'], ['89748', 'zip', '72762; AR; Fayetteville-Springdale-Rogers; Washington County; Springdale'], ['89747', 'zip', '72761; AR; Fayetteville-Springdale-Rogers; Benton County; Siloam Springs'], ['89746', 'zip', '72760; AR; Fayetteville-Springdale-Rogers; Madison County; Saint Paul'], ['89745', 'zip', '72758; AR; Fayetteville-Springdale-Rogers; Benton County; Rogers'], ['89743', 'zip', '72756; AR; Fayetteville-Springdale-Rogers; Benton County; Rogers'], ['89742', 'zip', '72753; AR; Fayetteville-Springdale-Rogers; Washington County; Prairie Grove'], ['89741', 'zip', '72752; AR; Fayetteville-Springdale-Rogers; Madison County; Pettigrew'], ['89740', 'zip', '72751; AR; Fayetteville-Springdale-Rogers; Benton County; Pea Ridge'], ['8974', 'city', 'Pecks Mill; WV; Logan; Logan County'], ['89739', 'zip', '72749; AR; Fayetteville-Springdale-Rogers; Washington County; Morrow'], ['89738', 'zip', '72747; AR; Fayetteville-Springdale-Rogers; Benton County; Maysville'], ['89737', 'zip', '72745; AR; Fayetteville-Springdale-Rogers; Benton County; Lowell'], ['89736', 'zip', '72744; AR; Fayetteville-Springdale-Rogers; Washington County; Lincoln'], ['89735', 'zip', '72742; AR; Harrison; Newton County; Kingston'], ['89733', 'zip', '72740; AR; Fayetteville-Springdale-Rogers; Madison County; Huntsville'], ['89732', 'zip', '72739; AR; Fayetteville-Springdale-Rogers; Benton County; Bella Vista'], ['89731', 'zip', '72738; AR; Fayetteville-Springdale-Rogers; Madison County; Hindsville'], ['89729', 'zip', '72736; AR; Fayetteville-Springdale-Rogers; Benton County; Gravette'], ['89728', 'zip', '72735; AR; Fayetteville-Springdale-Rogers; Washington County; Goshen'], ['89727', 'zip', '72734; AR; Fayetteville-Springdale-Rogers; Benton County; Gentry'], ['89725', 'zip', '72732; AR; Fayetteville-Springdale-Rogers; Benton County; Garfield'], ['89724', 'zip', '72730; AR; Fayetteville-Springdale-Rogers; Washington County; Farmington'], ['89723', 'zip', '72729; AR; Fayetteville-Springdale-Rogers; Washington County; Evansville'], ['89721', 'zip', '72727; AR; Fayetteville-Springdale-Rogers; Washington County; Elkins'], ['89720', 'zip', '72722; AR; Fayetteville-Springdale-Rogers; Benton County; Decatur'], ['89719', 'zip', '72721; AR; Fayetteville-Springdale-Rogers; Madison County; Combs'], ['89718', 'zip', '72719; AR; Fayetteville-Springdale-Rogers; Benton County; Centerton'], ['89717', 'zip', '72718; AR; Fayetteville-Springdale-Rogers; Benton County; Cave Springs'], ['89716', 'zip', '72717; AR; Fayetteville-Springdale-Rogers; Washington County; Canehill'], ['89714', 'zip', '72715; AR; Fayetteville-Springdale-Rogers; Benton County; Bella Vista'], ['89713', 'zip', '72714; AR; Fayetteville-Springdale-Rogers; Benton County; Bella Vista'], ['89712', 'zip', '72712; AR; Fayetteville-Springdale-Rogers; Benton County; Bentonville'], ['89710', 'zip', '72704; AR; Fayetteville-Springdale-Rogers; Washington County; Fayetteville'], ['89709', 'zip', '72703; AR; Fayetteville-Springdale-Rogers; Washington County; Fayetteville'], ['89707', 'zip', '72701; AR; Fayetteville-Springdale-Rogers; Washington County; Fayetteville'], ['89706', 'zip', '72687; AR; Marion County; Yellville'], ['89705', 'zip', '72686; AR; Searcy County; Witts Springs'], ['89704', 'zip', '72685; AR; Harrison; Newton County; Western Grove'], ['89703', 'zip', '72683; AR; Harrison; Newton County; Vendor'], ['89702', 'zip', '72682; AR; Marion County; Yellville'], ['89701', 'zip', '72680; AR; Searcy County; Leslie'], ['89700', 'zip', '72679; AR; Russellville; Pope County; Tilly'], ['897', 'county', 'Burnet County; TX'], ['89699', 'zip', '72677; AR; Marion County; Summit'], ['89698', 'zip', '72675; AR; Searcy County; Saint Joe'], ['89697', 'zip', '72672; AR; Marion County; Pyatt'], ['89696', 'zip', '72670; AR; Harrison; Newton County; Kingston'], ['89694', 'zip', '72668; AR; Marion County; Peel'], ['89693', 'zip', '72666; AR; Harrison; Newton County; Parthenon'], ['89692', 'zip', '72663; AR; Stone County; Onia'], ['89691', 'zip', '72662; AR; Harrison; Boone County; Omaha'], ['89690', 'zip', '72661; AR; Marion County; Oakland'], ['8969', 'neigh', 'Peaks Island; ME; Portland-South Portland; Cumberland County; Portland'], ['89689', 'zip', '72660; AR; Carroll County; Oak Grove'], ['89687', 'zip', '72658; AR; Mountain Home; Baxter County; Norfork'], ['89685', 'zip', '72655; AR; Harrison; Newton County; Mount Judea'], ['89683', 'zip', '72653; AR; Mountain Home; Baxter County'], ['89682', 'zip', '72651; AR; Mountain Home; Baxter County; Midway'], ['89681', 'zip', '72650; AR; Searcy County; Marshall'], ['89680', 'zip', '72648; AR; Harrison; Newton County; Jasper'], ['8968', 'city', 'New Washington; OH; Bucyrus; Crawford County'], ['89679', 'zip', '72645; AR; Searcy County; Leslie'], ['89678', 'zip', '72644; AR; Harrison; Boone County; Diamond City'], ['89677', 'zip', '72642; AR; Mountain Home; Baxter County; Lakeview'], ['89676', 'zip', '72641; AR; Harrison; Newton County; Jasper'], ['89675', 'zip', '72640; AR; Harrison; Newton County; Hasty'], ['89674', 'zip', '72639; AR; Searcy County; Harriet'], ['89673', 'zip', '72638; AR; Carroll County; Green Forest'], ['89672', 'zip', '72636; AR; Searcy County; Gilbert'], ['89671', 'zip', '72635; AR; Mountain Home; Baxter County; Gassville'], ['89670', 'zip', '72634; AR; Marion County; Flippin'], ['8967', 'city', 'New Vineyard; ME; Franklin County'], ['89669', 'zip', '72633; AR; Harrison; Boone County; Everton'], ['89668', 'zip', '72632; AR; Carroll County; Eureka Springs'], ['89667', 'zip', '72631; AR; Carroll County; Eureka Springs'], ['89666', 'zip', '72630; AR; Harrison; Boone County; Diamond City'], ['89665', 'zip', '72629; AR; Van Buren County; Dennard'], ['89664', 'zip', '72628; AR; Harrison; Newton County; Deer'], ['89663', 'zip', '72626; AR; Mountain Home; Baxter County; Cotter'], ['89662', 'zip', '72624; AR; Carroll County; Alpena'], ['89661', 'zip', '72623; AR; Mountain Home; Baxter County; Clarkridge'], ['89660', 'zip', '72619; AR; Marion County; Bull Shoals'], ['89659', 'zip', '72617; AR; Mountain Home; Baxter County; Big Flat'], ['89658', 'zip', '72616; AR; Carroll County; Berryville'], ['89657', 'zip', '72615; AR; Harrison; Boone County; Bergman'], ['89655', 'zip', '72611; AR; Carroll County; Alpena'], ['89654', 'zip', '72602; AR; Harrison; Boone County'], ['89653', 'zip', '72601; AR; Harrison; Boone County'], ['89652', 'zip', '72587; AR; Izard County; Horseshoe Bend'], ['89651', 'zip', '72585; AR; Izard County; Pineville'], ['89650', 'zip', '72584; AR; Izard County; Melbourne'], ['89649', 'zip', '72583; AR; Fulton County; Viola'], ['89648', 'zip', '72581; AR; Cleburne County; Tumbling Shoals'], ['89647', 'zip', '72579; AR; Batesville; Independence County; Sulphur Rock'], ['89646', 'zip', '72578; AR; Fulton County; Sturkie'], ['89645', 'zip', '72577; AR; Izard County; Sidney'], ['89644', 'zip', '72576; AR; Fulton County; Salem'], ['89642', 'zip', '72573; AR; Izard County; Melbourne'], ['89641', 'zip', '72572; AR; Lawrence County; Saffell'], ['89640', 'zip', '72571; AR; Batesville; Independence County; Rosie'], ['8964', 'city', 'Maurepas; LA; Baton Rouge; Livingston Parish'], ['89639', 'zip', '72569; AR; Sharp County; Poughkeepsie'], ['89638', 'zip', '72568; AR; Batesville; Independence County; Pleasant Plains'], ['89637', 'zip', '72567; AR; Stone County; Pleasant Grove'], ['89636', 'zip', '72566; AR; Izard County; Pineville'], ['89635', 'zip', '72565; AR; Fulton County; Salem'], ['89634', 'zip', '72564; AR; Batesville; Independence County; Oil Trough'], ['89633', 'zip', '72562; AR; Batesville; Independence County; Newark'], ['89632', 'zip', '72561; AR; Batesville; Independence County'], ['89631', 'zip', '72560; AR; Stone County; Mountain View'], ['89630', 'zip', '72556; AR; Izard County; Melbourne'], ['8963', 'city', 'Mauldin; SC; Greenville-Anderson-Mauldin; Greenville County'], ['89629', 'zip', '72555; AR; Stone County; Marcella'], ['89628', 'zip', '72554; AR; Fulton County; Mammoth Spring'], ['89627', 'zip', '72553; AR; Batesville; Independence County; Magness'], ['89626', 'zip', '72550; AR; Batesville; Independence County; Locust Grove'], ['89625', 'zip', '72546; AR; Cleburne County; Drasco'], ['89623', 'zip', '72544; AR; Mountain Home; Baxter County; Henderson'], ['89622', 'zip', '72543; AR; Cleburne County; Heber Springs'], ['89621', 'zip', '72542; AR; Sharp County; Highland'], ['89620', 'zip', '72540; AR; Izard County; Guion'], ['8962', 'city', 'Mauk; GA; Columbus; Marion County'], ['89619', 'zip', '72539; AR; Fulton County; Glencoe'], ['89618', 'zip', '72538; AR; Fulton County; Gepp'], ['89617', 'zip', '72537; AR; Mountain Home; Baxter County; Gamaliel'], ['89616', 'zip', '72536; AR; Izard County; Horseshoe Bend'], ['89615', 'zip', '72534; AR; Batesville; Independence County; Floral'], ['89614', 'zip', '72533; AR; Stone County; Fifty-Six'], ['89613', 'zip', '72532; AR; Sharp County; Evening Shade'], ['89612', 'zip', '72531; AR; Mountain Home; Baxter County; Elizabeth'], ['89611', 'zip', '72530; AR; Cleburne County; Drasco'], ['89610', 'zip', '72529; AR; Sharp County; Cherokee Village'], ['8961', 'city', 'Mattapoisett; MA; Boston-Cambridge-Newton; Plymouth County'], ['89609', 'zip', '72528; AR; Izard County; Calico Rock'], ['89608', 'zip', '72527; AR; Batesville; Independence County; Desha'], ['89605', 'zip', '72524; AR; Batesville; Independence County; Cord'], ['89604', 'zip', '72523; AR; Cleburne County; Concord'], ['89603', 'zip', '72522; AR; Batesville; Independence County; Charlotte'], ['89602', 'zip', '72521; AR; Sharp County; Cave City'], ['89601', 'zip', '72520; AR; Fulton County; Camp'], ['89600', 'zip', '72519; AR; Izard County; Calico Rock'], ['8960', 'city', 'Town Of Masonville; NY; Delaware County'], ['896', 'county', 'Bureau County; IL; Ottawa-Peru'], ['89599', 'zip', '72517; AR; Izard County; Melbourne'], ['89598', 'zip', '72515; AR; Izard County; Bexar'], ['89597', 'zip', '72513; AR; Sharp County; Ash Flat'], ['89596', 'zip', '72512; AR; Izard County; Horseshoe Bend'], ['89594', 'zip', '72501; AR; Batesville; Independence County'], ['89593', 'zip', '72482; AR; Sharp County; Williford'], ['89592', 'zip', '72479; AR; Jonesboro; Poinsett County; Weiner'], ['89591', 'zip', '72478; AR; Randolph County; Warm Springs'], ['89590', 'zip', '72476; AR; Lawrence County; Walnut Ridge'], ['8959', 'city', 'Town Of Maryland; NY; Oneonta; Otsego County'], ['89589', 'zip', '72475; AR; Jonesboro; Poinsett County; Waldenburg'], ['89588', 'zip', '72474; AR; Paragould; Greene County'], ['89587', 'zip', '72473; AR; Jackson County; Tuckerman'], ['89586', 'zip', '72472; AR; Jonesboro; Poinsett County; Trumann'], ['89585', 'zip', '72471; AR; Jackson County; Swifton'], ['89584', 'zip', '72470; AR; Clay County; Success'], ['89583', 'zip', '72469; AR; Lawrence County; Strawberry'], ['89581', 'zip', '72466; AR; Sharp County; Smithville'], ['8958', 'city', 'Marydel; MD; Caroline County'], ['89579', 'zip', '72464; AR; Clay County; Saint Francis'], ['89578', 'zip', '72462; AR; Randolph County; Biggers'], ['89577', 'zip', '72461; AR; Clay County; Rector'], ['89576', 'zip', '72460; AR; Randolph County; Ravenden Springs'], ['89575', 'zip', '72459; AR; Lawrence County; Ravenden'], ['89574', 'zip', '72458; AR; Lawrence County; Powhatan'], ['89573', 'zip', '72457; AR; Lawrence County; Hoxie'], ['89572', 'zip', '72456; AR; Clay County; Pollard'], ['89571', 'zip', '72455; AR; Randolph County; Pocahontas'], ['89570', 'zip', '72454; AR; Clay County; Piggott'], ['89569', 'zip', '72453; AR; Clay County; Peach Orchard'], ['89567', 'zip', '72450; AR; Paragould; Greene County'], ['89566', 'zip', "72449; AR; Randolph County; O'Kean"], ['89565', 'zip', '72447; AR; Jonesboro; Craighead County; Monette'], ['89563', 'zip', '72444; AR; Randolph County; Maynard'], ['89562', 'zip', '72443; AR; Paragould; Greene County; Marmaduke'], ['89561', 'zip', '72442; AR; Blytheville; Mississippi County; Manila'], ['89560', 'zip', '72441; AR; Clay County; McDougal'], ['8956', 'city', 'Martinsville; IL; Clark County'], ['89559', 'zip', '72440; AR; Lawrence County; Lynn'], ['89557', 'zip', '72438; AR; Blytheville; Mississippi County; Leachville'], ['89556', 'zip', '72437; AR; Jonesboro; Craighead County; Lake City'], ['89555', 'zip', '72436; AR; Paragould; Greene County; Lafe'], ['89554', 'zip', '72435; AR; Clay County; Knobel'], ['89553', 'zip', '72434; AR; Lawrence County; Imboden'], ['89552', 'zip', '72433; AR; Lawrence County; Hoxie'], ['89551', 'zip', '72432; AR; Jonesboro; Poinsett County; Harrisburg'], ['89550', 'zip', '72431; AR; Jackson County; Grubbs'], ['8955', 'city', 'Marsing; ID; Boise City; Owyhee County'], ['89549', 'zip', '72430; AR; Clay County; Greenway'], ['89548', 'zip', '72429; AR; Jonesboro; Poinsett County; Fisher'], ['89544', 'zip', "72425; AR; Randolph County; O'Kean"], ['89543', 'zip', '72424; AR; Clay County; Datto'], ['89542', 'zip', '72422; AR; Clay County; Corning'], ['89541', 'zip', '72421; AR; Jonesboro; Craighead County; Cash'], ['8954', 'city', 'Ridgeway; VA; Martinsville; Henry County'], ['89539', 'zip', '72417; AR; Jonesboro; Craighead County; Brookland'], ['89538', 'zip', '72416; AR; Jonesboro; Craighead County; Bono'], ['89537', 'zip', '72415; AR; Lawrence County; Black Rock'], ['89536', 'zip', '72414; AR; Jonesboro; Craighead County; Black Oak'], ['89535', 'zip', '72413; AR; Randolph County; Biggers'], ['89534', 'zip', '72412; AR; Paragould; Greene County; Beech Grove'], ['89533', 'zip', '72411; AR; Jonesboro; Craighead County; Bay'], ['89532', 'zip', '72410; AR; Lawrence County; Alicia'], ['89531', 'zip', '72404; AR; Jonesboro; Craighead County'], ['89528', 'zip', '72401; AR; Jonesboro; Craighead County'], ['89527', 'zip', '72396; AR; Cross County; Wynne'], ['89526', 'zip', '72395; AR; Blytheville; Mississippi County; Wilson'], ['89525', 'zip', '72394; AR; Forrest City; Saint Francis County; Widener'], ['89524', 'zip', '72392; AR; Forrest City; Saint Francis County; Wheatley'], ['89522', 'zip', '72390; AR; Helena-West Helena; Phillips County; Helena - West Helena'], ['89520', 'zip', '72387; AR; Cross County; Vanndale'], ['89519', 'zip', '72386; AR; Jonesboro; Poinsett County; Tyronza'], ['89518', 'zip', '72384; AR; Memphis; Crittenden County; Turrell'], ['89516', 'zip', '72379; AR; Desha County; Snow Lake'], ['89514', 'zip', '72376; AR; Memphis; Crittenden County; Anthonyville'], ['89513', 'zip', '72374; AR; Helena-West Helena; Phillips County; Poplar Grove'], ['89512', 'zip', '72373; AR; Cross County; Parkin'], ['89511', 'zip', '72372; AR; Forrest City; Saint Francis County; Palestine'], ['89510', 'zip', '72370; AR; Blytheville; Mississippi County; Osceola'], ['89508', 'zip', '72368; AR; Lee County; Moro'], ['89506', 'zip', '72366; AR; Helena-West Helena; Phillips County; Marvell'], ['89505', 'zip', '72365; AR; Jonesboro; Poinsett County; Marked Tree'], ['89504', 'zip', '72364; AR; Memphis; Crittenden County; Marion'], ['89503', 'zip', '72360; AR; Lee County; Marianna'], ['89502', 'zip', '72359; AR; Forrest City; Saint Francis County; Madison'], ['89501', 'zip', '72358; AR; Blytheville; Mississippi County; Luxora'], ['89500', 'zip', '72355; AR; Helena-West Helena; Phillips County; Lexa'], ['8950', 'city', 'New Cambria; MO; Macon County'], ['895', 'county', 'Broome County; NY; Binghamton'], ['89499', 'zip', '72354; AR; Jonesboro; Poinsett County; Lepanto'], ['89496', 'zip', '72351; AR; Blytheville; Mississippi County; Keiser'], ['89495', 'zip', '72350; AR; Blytheville; Mississippi County; Joiner'], ['89494', 'zip', '72348; AR; Forrest City; Saint Francis County; Hughes'], ['89493', 'zip', '72347; AR; Cross County; Hickory Ridge'], ['89492', 'zip', '72346; AR; Forrest City; Saint Francis County; Heth'], ['89491', 'zip', '72342; AR; Helena-West Helena; Phillips County; Helena - West Helena'], ['89490', 'zip', '72341; AR; Lee County; Haynes'], ['89489', 'zip', '72340; AR; Forrest City; Saint Francis County; Widener'], ['89488', 'zip', '72339; AR; Memphis; Crittenden County; Gilmore'], ['89485', 'zip', '72335; AR; Forrest City; Saint Francis County'], ['89484', 'zip', '72333; AR; Helena-West Helena; Phillips County; Elaine'], ['89483', 'zip', '72332; AR; Memphis; Crittenden County; Edmondson'], ['89482', 'zip', '72331; AR; Memphis; Crittenden County; Earle'], ['89479', 'zip', '72328; AR; Helena-West Helena; Phillips County; Mellwood'], ['89478', 'zip', '72327; AR; Memphis; Crittenden County; Crawfordsville'], ['89477', 'zip', '72326; AR; Forrest City; Saint Francis County; Colt'], ['89476', 'zip', '72325; AR; Memphis; Crittenden County; Clarkedale'], ['89475', 'zip', '72324; AR; Cross County; Cherry Valley'], ['89472', 'zip', '72320; AR; Lee County; Brickeys'], ['8947', 'city', 'Nevada; OH; Wyandot County'], ['89469', 'zip', '72315; AR; Blytheville; Mississippi County'], ['89463', 'zip', '72301; AR; Memphis; Crittenden County; West Memphis'], ['89459', 'zip', '72227; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89457', 'zip', '72223; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89449', 'zip', '72212; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89448', 'zip', '72211; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89447', 'zip', '72210; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89446', 'zip', '72209; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89445', 'zip', '72207; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89444', 'zip', '72206; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89443', 'zip', '72205'], ['89442', 'zip', '72204; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89440', 'zip', '72202; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89439', 'zip', '72201; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89432', 'zip', '72181; AR; Little Rock-North Little Rock-Conway; Faulkner County; Wooster'], ['89430', 'zip', '72179; AR; Cleburne County; Wilburn'], ['89429', 'zip', '72178; AR; Searcy; White County; West Point'], ['89428', 'zip', '72176; AR; Little Rock-North Little Rock-Conway; Lonoke County; Ward'], ['89427', 'zip', '72175; AR; Pine Bluff; Jefferson County; Wabbaseka'], ['89426', 'zip', '72173; AR; Little Rock-North Little Rock-Conway; Faulkner County; Vilonia'], ['89425', 'zip', '72170; AR; Prairie County; Ulm'], ['89423', 'zip', '72168; AR; Pine Bluff; Jefferson County; Tucker'], ['89422', 'zip', '72167; AR; Little Rock-North Little Rock-Conway; Saline County; Traskwood'], ['89421', 'zip', '72166; AR; Arkansas County; Tichnor'], ['89420', 'zip', '72165; AR; Searcy; White County; Bradford'], ['89418', 'zip', '72160; AR; Arkansas County; Stuttgart'], ['89416', 'zip', '72157; AR; Conway County; Springfield'], ['89415', 'zip', '72156; AR; Conway County; Solgohachia'], ['89414', 'zip', '72153; AR; Van Buren County; Shirley'], ['89413', 'zip', '72152; AR; Pine Bluff; Jefferson County; Sherrill'], ['89412', 'zip', '72150; AR; Little Rock-North Little Rock-Conway; Grant County; Sheridan'], ['8941', 'city', 'Maringouin; LA; Baton Rouge; Iberville Parish'], ['89409', 'zip', '72143; AR; Searcy; White County'], ['89408', 'zip', '72142; AR; Little Rock-North Little Rock-Conway; Lonoke County; Scott'], ['89407', 'zip', '72141; AR; Van Buren County; Scotland'], ['89406', 'zip', '72140; AR; Arkansas County; Saint Charles'], ['89405', 'zip', '72139; AR; Searcy; White County; Russell'], ['89404', 'zip', '72137; AR; Searcy; White County; Rose Bud'], ['89403', 'zip', '72136; AR; Searcy; White County; Romance'], ['89402', 'zip', '72135; AR; Little Rock-North Little Rock-Conway; Pulaski County; Roland'], ['89401', 'zip', '72134; AR; Monroe County; Roe'], ['89400', 'zip', '72133; AR; Arkansas County; Stuttgart'], ['8940', 'city', 'Marietta; IL; Canton; Fulton County'], ['894', 'county', 'Brooks County; TX'], ['89399', 'zip', '72132; AR; Pine Bluff; Jefferson County; Redfield'], ['89398', 'zip', '72131; AR; Cleburne County; Quitman'], ['89397', 'zip', '72130; AR; Cleburne County; Prim'], ['89395', 'zip', '72128; AR; Little Rock-North Little Rock-Conway; Grant County; Poyen'], ['89394', 'zip', '72127; AR; Conway County; Plumerville'], ['89393', 'zip', '72126; AR; Little Rock-North Little Rock-Conway; Perry County; Perryville'], ['89392', 'zip', '72125; AR; Little Rock-North Little Rock-Conway; Perry County; Perry'], ['89390', 'zip', '72123; AR; Woodruff County; Patterson'], ['89389', 'zip', '72122; AR; Little Rock-North Little Rock-Conway; Saline County; Paron'], ['89388', 'zip', '72121; AR; Searcy; White County; Pangburn'], ['89387', 'zip', '72120; AR; Little Rock-North Little Rock-Conway; Pulaski County; Sherwood'], ['89385', 'zip', '72118; AR; Little Rock-North Little Rock-Conway; Pulaski County; North Little Rock'], ['89384', 'zip', '72117; AR; Little Rock-North Little Rock-Conway; Pulaski County; North Little Rock'], ['89383', 'zip', '72116; AR; Little Rock-North Little Rock-Conway; Pulaski County; North Little Rock'], ['89381', 'zip', '72114; AR; Little Rock-North Little Rock-Conway; Pulaski County; North Little Rock'], ['89380', 'zip', '72113; AR; Little Rock-North Little Rock-Conway; Pulaski County; Maumelle'], ['89379', 'zip', '72112; AR; Jackson County; Newport'], ['89378', 'zip', '72111; AR; Little Rock-North Little Rock-Conway; Faulkner County; Mount Vernon'], ['89377', 'zip', '72110; AR; Conway County; Morrilton'], ['89375', 'zip', '72107; AR; Conway County; Plumerville'], ['89374', 'zip', '72106; AR; Little Rock-North Little Rock-Conway; Faulkner County; Mayflower'], ['89372', 'zip', '72104; AR; Malvern; Hot Spring County'], ['89371', 'zip', '72103; AR; Little Rock-North Little Rock-Conway; Pulaski County; Little Rock'], ['89370', 'zip', '72102; AR; Searcy; White County; McRae'], ['89369', 'zip', '72101; AR; Woodruff County; McCrory'], ['89366', 'zip', '72088; AR; Van Buren County; Fairfield Bay'], ['89365', 'zip', '72087; AR; Little Rock-North Little Rock-Conway; Saline County; Lonsdale'], ['89364', 'zip', '72086; AR; Little Rock-North Little Rock-Conway; Lonoke County; Lonoke'], ['89363', 'zip', '72085; AR; Searcy; White County; Letona'], ['89362', 'zip', '72084; AR; Little Rock-North Little Rock-Conway; Grant County; Leola'], ['89361', 'zip', '72083; AR; Little Rock-North Little Rock-Conway; Lonoke County; Keo'], ['89360', 'zip', '72082; AR; Searcy; White County; Kensett'], ['8936', 'city', 'Red Oak; OK; Latimer County'], ['89359', 'zip', '72081; AR; Searcy; White County; Judsonia'], ['89358', 'zip', '72080; AR; Conway County; Jerusalem'], ['89357', 'zip', '72079; AR; Pine Bluff; Jefferson County; Jefferson'], ['89355', 'zip', '72076; AR; Little Rock-North Little Rock-Conway; Pulaski County; Jacksonville'], ['89354', 'zip', '72075; AR; Jackson County; Jacksonport'], ['89352', 'zip', '72073; AR; Arkansas County; Humphrey'], ['89351', 'zip', '72072; AR; Little Rock-North Little Rock-Conway; Lonoke County; Humnoke'], ['89350', 'zip', '72070; AR; Little Rock-North Little Rock-Conway; Perry County; Houston'], ['8935', 'city', 'Parlier; CA; Fresno; Fresno County'], ['89349', 'zip', '72069; AR; Monroe County; Holly Grove'], ['89348', 'zip', '72068; AR; Searcy; White County'], ['89347', 'zip', '72067; AR; Cleburne County; Greers Ferry'], ['89346', 'zip', '72066; AR; Little Rock-North Little Rock-Conway; Lonoke County; Carlisle'], ['89345', 'zip', '72065; AR; Little Rock-North Little Rock-Conway; Saline County; Hensley'], ['89344', 'zip', '72064; AR; Prairie County; Hazen'], ['89343', 'zip', '72063; AR; Conway County; Hattieville'], ['89342', 'zip', '72061; AR; Cleburne County; Quitman'], ['89341', 'zip', '72060; AR; Searcy; White County; Griffithville'], ['89339', 'zip', '72058; AR; Little Rock-North Little Rock-Conway; Faulkner County; Greenbrier'], ['89337', 'zip', '72055; AR; Arkansas County; Gillett'], ['89336', 'zip', '72053; AR; Little Rock-North Little Rock-Conway; Pulaski County; Sweet Home'], ['89335', 'zip', '72052; AR; Searcy; White County; Garner'], ['89334', 'zip', '72051; AR; Stone County; Fox'], ['89333', 'zip', '72048; AR; Arkansas County; Ethel'], ['89332', 'zip', '72047; AR; Little Rock-North Little Rock-Conway; Faulkner County; Enola'], ['89331', 'zip', '72046; AR; Little Rock-North Little Rock-Conway; Lonoke County; England'], ['89330', 'zip', '72045; AR; Searcy; White County; El Paso'], ['89329', 'zip', '72044; AR; Cleburne County; Edgemont'], ['89328', 'zip', '72043; AR; Jackson County; Diaz'], ['89327', 'zip', '72042; AR; Arkansas County; De Witt'], ['89326', 'zip', '72041; AR; Prairie County; De Valls Bluff'], ['89325', 'zip', '72040; AR; Prairie County; Des Arc'], ['89324', 'zip', '72039; AR; Little Rock-North Little Rock-Conway; Faulkner County; Twin Groves'], ['89323', 'zip', '72038; AR; Arkansas County; Crocketts Bluff'], ['89322', 'zip', '72037; AR; Little Rock-North Little Rock-Conway; Lonoke County; Coy'], ['89321', 'zip', '72036; AR; Woodruff County; Cotton Plant'], ['89319', 'zip', '72034; AR; Little Rock-North Little Rock-Conway; Faulkner County; Conway'], ['89317', 'zip', '72032; AR; Little Rock-North Little Rock-Conway; Faulkner County; Conway'], ['89316', 'zip', '72031; AR; Van Buren County; Clinton'], ['89315', 'zip', '72030; AR; Conway County; Cleveland'], ['89314', 'zip', '72029; AR; Monroe County; Clarendon'], ['89312', 'zip', '72027; AR; Conway County; Center Ridge'], ['89311', 'zip', '72026; AR; Arkansas County; Casscoe'], ['89310', 'zip', '72025; AR; Russellville; Yell County; Casa'], ['89309', 'zip', '72024; AR; Little Rock-North Little Rock-Conway; Lonoke County; Carlisle'], ['89308', 'zip', '72023; AR; Little Rock-North Little Rock-Conway; Lonoke County; Cabot'], ['89307', 'zip', '72022; AR; Little Rock-North Little Rock-Conway; Saline County; Bryant'], ['89306', 'zip', '72021; AR; Monroe County; Brinkley'], ['89305', 'zip', '72020; AR; Searcy; White County; Bradford'], ['89303', 'zip', '72017; AR; Prairie County; Fredonia (Biscoe)'], ['89302', 'zip', '72016; AR; Little Rock-North Little Rock-Conway; Pulaski County; Roland'], ['89301', 'zip', '72015; AR; Little Rock-North Little Rock-Conway; Saline County; Benton'], ['89300', 'zip', '72014; AR; Jackson County; Beedeville'], ['893', 'county', 'Brooks County; GA; Valdosta'], ['89299', 'zip', '72013; AR; Van Buren County; Bee Branch'], ['89298', 'zip', '72012; AR; Searcy; White County; Beebe'], ['89297', 'zip', '72011; AR; Little Rock-North Little Rock-Conway; Saline County; Bauxite'], ['89296', 'zip', '72010; AR; Searcy; White County; Bald Knob'], ['89295', 'zip', '72007; AR; Little Rock-North Little Rock-Conway; Lonoke County; Austin'], ['89294', 'zip', '72006; AR; Woodruff County; Augusta'], ['89293', 'zip', '72005; AR; Jackson County; Amagon'], ['89292', 'zip', '72004; AR; Pine Bluff; Jefferson County; Altheimer'], ['89291', 'zip', '72003; AR; Arkansas County; Almyra'], ['89290', 'zip', '72002; AR; Little Rock-North Little Rock-Conway; Saline County; Shannon Hills'], ['8929', 'city', 'Nelson; NE; Nuckolls County'], ['89289', 'zip', '72001; AR; Little Rock-North Little Rock-Conway; Perry County; Adona'], ['89286', 'zip', '71973; AR; Polk County; Wickes'], ['89285', 'zip', '71972; AR; Polk County; Vandervoort'], ['89284', 'zip', '71971; AR; Howard County; Umpire'], ['89283', 'zip', '71970; AR; Montgomery County; Story'], ['89282', 'zip', '71969; AR; Montgomery County; Sims'], ['89281', 'zip', '71968; AR; Hot Springs; Garland County; Royal'], ['89279', 'zip', '71965; AR; Montgomery County; Pencil Bluff'], ['89278', 'zip', '71964; AR; Hot Springs; Garland County; Pearcy'], ['89277', 'zip', '71962; AR; Arkadelphia; Clark County; Okolona'], ['89276', 'zip', '71961; AR; Montgomery County; Oden'], ['89275', 'zip', '71960; AR; Montgomery County; Norman'], ['89274', 'zip', '71959; AR; Pike County; Newhope'], ['89273', 'zip', '71958; AR; Pike County; Murfreesboro'], ['89272', 'zip', '71957; AR; Montgomery County; Mount Ida'], ['89271', 'zip', '71956; AR; Hot Springs; Garland County; Mountain Pine'], ['89270', 'zip', '71953; AR; Polk County; Mena'], ['89269', 'zip', '71952; AR; Howard County; Umpire'], ['89267', 'zip', '71950; AR; Pike County; Daisy'], ['89266', 'zip', '71949; AR; Hot Springs; Garland County; Jessieville'], ['89265', 'zip', '71945; AR; Polk County; Hatfield'], ['89264', 'zip', '71944; AR; Polk County; Grannis'], ['89263', 'zip', '71943; AR; Pike County; Glenwood'], ['89261', 'zip', '71941; AR; Malvern; Hot Spring County; Donaldson'], ['89260', 'zip', '71940; AR; Pike County; Delight'], ['89259', 'zip', '71937; AR; Polk County; Cove'], ['89258', 'zip', '71935; AR; Montgomery County; Caddo Gap'], ['89257', 'zip', '71933; AR; Hot Springs; Garland County; Bonnerdale'], ['89255', 'zip', '71929; AR; Malvern; Hot Spring County; Bismarck'], ['89254', 'zip', '71923; AR; Arkadelphia; Clark County'], ['89253', 'zip', '71922; AR; Pike County; Antoine'], ['89252', 'zip', '71921; AR; Arkadelphia; Clark County; Amity'], ['8925', 'city', 'Naylor; GA; Valdosta; Lowndes County'], ['89249', 'zip', '71913; AR; Hot Springs; Garland County'], ['89247', 'zip', '71909; AR; Little Rock-North Little Rock-Conway; Saline County; Hot Springs Village'], ['89244', 'zip', '71901; AR; Hot Springs; Garland County'], ['89243', 'zip', '71866; AR; Texarkana; Little River County; Winthrop'], ['89242', 'zip', '71865; AR; Texarkana; Little River County; Wilton'], ['89241', 'zip', '71864; AR; Hope; Nevada County; Willisville'], ['89240', 'zip', '71862; AR; Hope; Hempstead County; Washington'], ['89239', 'zip', '71861; AR; Magnolia; Columbia County; Taylor'], ['89238', 'zip', '71860; AR; Lafayette County; Stamps'], ['89237', 'zip', '71859; AR; Hope; Hempstead County; Saratoga'], ['89236', 'zip', '71858; AR; Hope; Nevada County; Rosston'], ['89235', 'zip', '71857; AR; Hope; Nevada County; Prescott'], ['89234', 'zip', '71855; AR; Hope; Hempstead County; Ozan'], ['89233', 'zip', '71854; AR; Texarkana; Miller County'], ['89231', 'zip', '71852; AR; Howard County; Nashville'], ['89230', 'zip', '71851; AR; Howard County; Mineral Springs'], ['8923', 'city', 'Nauvoo; IL; Fort Madison-Keokuk; Hancock County'], ['89229', 'zip', '71847; AR; Hope; Hempstead County; McCaskill'], ['89228', 'zip', '71846; AR; Sevier County; Lockesburg'], ['89227', 'zip', '71845; AR; Lafayette County; Lewisville'], ['89225', 'zip', '71842; AR; Sevier County; Horatio'], ['89224', 'zip', '71841; AR; Sevier County; Gillham'], ['89222', 'zip', '71839; AR; Texarkana; Miller County; Garland'], ['89221', 'zip', '71838; AR; Hope; Hempstead County; Fulton'], ['89220', 'zip', '71837; AR; Texarkana; Miller County; Fouke'], ['89219', 'zip', '71836; AR; Texarkana; Little River County; Foreman'], ['89218', 'zip', '71835; AR; Hope; Nevada County; Emmet'], ['89217', 'zip', '71834; AR; Texarkana; Miller County; Doddridge'], ['89216', 'zip', '71833; AR; Howard County; Dierks'], ['89215', 'zip', '71832; AR; Sevier County; De Queen'], ['89214', 'zip', '71831; AR; Hope; Hempstead County; Saratoga'], ['89212', 'zip', '71827; AR; Lafayette County; Buckner'], ['89211', 'zip', '71826; AR; Lafayette County; Bradley'], ['8921', 'city', 'Marble; MN; Grand Rapids; Itasca County'], ['89209', 'zip', '71823; AR; Sevier County; Lockesburg'], ['89208', 'zip', '71822; AR; Texarkana; Little River County; Ashdown'], ['89207', 'zip', '71820; AR; Texarkana; Little River County; Ashdown'], ['89206', 'zip', '71802; AR; Hope; Hempstead County'], ['89205', 'zip', '71801; AR; Hope; Hempstead County'], ['89203', 'zip', '71770; AR; Magnolia; Columbia County; Waldo'], ['89201', 'zip', '71766; AR; Camden; Calhoun County; Thornton'], ['89200', 'zip', '71765; AR; El Dorado; Union County; Strong'], ['892', 'county', 'Brooke County; WV; Weirton-Steubenville'], ['89199', 'zip', '71764; AR; Camden; Ouachita County; Stephens'], ['89198', 'zip', '71763; AR; Dallas County; Sparkman'], ['89197', 'zip', '71762; AR; El Dorado; Union County; Smackover'], ['89196', 'zip', '71759; AR; El Dorado; Union County; Norphlet'], ['89195', 'zip', '71758; AR; El Dorado; Union County; Mount Holly'], ['89193', 'zip', '71753; AR; Magnolia; Columbia County'], ['89192', 'zip', '71752; AR; Magnolia; Columbia County; McNeil'], ['89191', 'zip', '71751; AR; Camden; Ouachita County; Louann'], ['89189', 'zip', '71749; AR; El Dorado; Union County; Junction City'], ['89188', 'zip', '71748; AR; Dallas County; Fordyce'], ['89187', 'zip', '71747; AR; El Dorado; Union County; Felsenthal'], ['89186', 'zip', '71745; AR; Camden; Calhoun County; Harrell'], ['89185', 'zip', '71744; AR; Camden; Calhoun County; Hampton'], ['89184', 'zip', '71743; AR; Arkadelphia; Clark County; Gurdon'], ['89183', 'zip', '71742; AR; Dallas County; Fordyce'], ['89182', 'zip', '71740; AR; Magnolia; Columbia County; Emerson'], ['89180', 'zip', '71730; AR; El Dorado; Union County'], ['8918', 'city', 'Maple Falls; WA; Bellingham; Whatcom County'], ['89178', 'zip', '71726; AR; Camden; Ouachita County; Chidester'], ['89177', 'zip', '71725; AR; Dallas County; Carthage'], ['89176', 'zip', '71724; AR; El Dorado; Union County; Calion'], ['89175', 'zip', '71722; AR; Hope; Nevada County; Bluff City'], ['89173', 'zip', '71720; AR; Camden; Ouachita County; Bearden'], ['89171', 'zip', '71701; AR; Camden; Ouachita County'], ['89169', 'zip', '71677; AR; Drew County; Winchester'], ['89168', 'zip', '71676; AR; Ashley County; Wilmot'], ['89167', 'zip', '71675; AR; Drew County; Wilmar'], ['89166', 'zip', '71674; AR; Desha County; Watson'], ['89165', 'zip', '71671; AR; Bradley County; Warren'], ['89164', 'zip', '71670; AR; Drew County; Tillar'], ['89163', 'zip', '71667; AR; Pine Bluff; Lincoln County; Star City'], ['89162', 'zip', '71666; AR; Desha County; Watson'], ['89161', 'zip', '71665; AR; Pine Bluff; Cleveland County; Rison'], ['89160', 'zip', '71663; AR; Ashley County; Portland'], ['89158', 'zip', '71661; AR; Ashley County; Parkdale'], ['89157', 'zip', '71660; AR; Pine Bluff; Cleveland County; New Edinburg'], ['89155', 'zip', '71658; AR; Ashley County; Montrose'], ['89152', 'zip', '71655; AR; Drew County; Monticello'], ['89151', 'zip', '71654; AR; Desha County; McGehee'], ['89150', 'zip', '71653; AR; Chicot County; Lake Village'], ['89149', 'zip', '71652; AR; Pine Bluff; Cleveland County; Kingsland'], ['89148', 'zip', '71651; AR; Bradley County; Jersey'], ['89147', 'zip', '71647; AR; Bradley County; Hermitage'], ['89146', 'zip', '71646; AR; Ashley County; Hamburg'], ['89145', 'zip', '71644; AR; Pine Bluff; Lincoln County; Grady'], ['89144', 'zip', '71643; AR; Pine Bluff; Lincoln County; Gould'], ['89143', 'zip', '71642; AR; Ashley County; Fountain Hill'], ['89142', 'zip', '71640; AR; Chicot County; Eudora'], ['89141', 'zip', '71639; AR; Desha County; Dumas'], ['89140', 'zip', '71638; AR; Chicot County; Dermott'], ['8914', 'city', 'Manorville; NY; New York-Newark-Jersey City; Suffolk County'], ['89139', 'zip', '71635; AR; Ashley County; Crossett'], ['89138', 'zip', '71631; AR; Bradley County; Banks'], ['89137', 'zip', '71630; AR; Desha County; Arkansas City'], ['89133', 'zip', '71603; AR; Pine Bluff; Jefferson County'], ['89132', 'zip', '71602; AR; Pine Bluff; Jefferson County; White Hall'], ['89131', 'zip', '71601; AR; Pine Bluff; Jefferson County'], ['89128', 'zip', '71486; LA; Sabine Parish; Zwolle'], ['89127', 'zip', '71485; LA; Alexandria; Rapides Parish; Woodworth'], ['89126', 'zip', '71483; LA; Winn Parish; Winnfield'], ['89124', 'zip', '71479; LA; LaSalle Parish; Tullos'], ['89120', 'zip', '71473; LA; Winn Parish; Sikes'], ['89119', 'zip', '71472; LA; Alexandria; Rapides Parish; Sieper'], ['89117', 'zip', '71469; LA; Natchitoches; Natchitoches Parish; Robeline'], ['89116', 'zip', '71468; LA; Natchitoches; Natchitoches Parish; Provencal'], ['89115', 'zip', '71467; LA; Alexandria; Grant Parish; Pollock'], ['89114', 'zip', '71466; LA; Alexandria; Rapides Parish; Otis'], ['89113', 'zip', '71465; LA; LaSalle Parish; Olla'], ['89112', 'zip', '71463; LA; Allen Parish; Oakdale'], ['89111', 'zip', '71462; LA; Sabine Parish; Noble'], ['89110', 'zip', '71461; LA; Fort Polk South; Vernon Parish; New Llano'], ['89106', 'zip', '71457; LA; Natchitoches; Natchitoches Parish'], ['89105', 'zip', '71456; LA; Natchitoches; Natchitoches Parish; Natchez'], ['89104', 'zip', '71455; LA; Alexandria; Rapides Parish; Mora'], ['89103', 'zip', '71454; LA; Alexandria; Grant Parish; Montgomery'], ['89101', 'zip', '71450; LA; Natchitoches; Natchitoches Parish; Marthaville'], ['89100', 'zip', '71449; LA; Sabine Parish; Many'], ['8910', 'city', 'Rochester; TX; Haskell County'], ['891', 'county', 'Bremer County; IA; Waterloo-Cedar Falls'], ['89098', 'zip', '71447; LA; Alexandria; Rapides Parish; Lena'], ['89097', 'zip', '71446; LA; Fort Polk South; Vernon Parish; Leesville'], ['89095', 'zip', '71441; LA; Caldwell Parish; Kelly'], ['89093', 'zip', '71439; LA; Fort Polk South; Vernon Parish; Hornbeck'], ['89092', 'zip', '71438; LA; Alexandria; Rapides Parish; Hineston'], ['89091', 'zip', '71435; LA; Caldwell Parish; Grayson'], ['8909', 'city', 'Roberts; IL; Champaign-Urbana; Ford County'], ['89089', 'zip', '71433; LA; Alexandria; Rapides Parish; Glenmora'], ['89088', 'zip', '71432; LA; Alexandria; Grant Parish; Georgetown'], ['89086', 'zip', '71430; LA; Alexandria; Rapides Parish; Forest Hill'], ['89085', 'zip', '71429; LA; Sabine Parish; Florien'], ['89083', 'zip', '71427; LA; Alexandria; Rapides Parish; Flatwoods'], ['89082', 'zip', '71426; LA; Sabine Parish; Fisher'], ['89080', 'zip', '71424; LA; Alexandria; Rapides Parish; Elmer'], ['8908', 'city', 'Robert; LA; Hammond; Tangipahoa Parish'], ['89079', 'zip', '71423; LA; Alexandria; Grant Parish; Dry Prong'], ['89078', 'zip', '71422; LA; Winn Parish; Dodson'], ['89077', 'zip', '71419; LA; Sabine Parish; Converse'], ['89076', 'zip', '71418; LA; Caldwell Parish; Columbia'], ['89075', 'zip', '71417; LA; Alexandria; Grant Parish; Colfax'], ['89073', 'zip', '71415; LA; Caldwell Parish; Clarks'], ['89071', 'zip', '71411; LA; Natchitoches; Natchitoches Parish; Campti'], ['8907', 'city', 'Robeline; LA; Natchitoches; Natchitoches Parish'], ['89069', 'zip', '71409; LA; Alexandria; Rapides Parish; Boyce'], ['89068', 'zip', '71407; LA; Alexandria; Grant Parish; Bentley'], ['89067', 'zip', '71406; LA; Sabine Parish; Belmont'], ['89066', 'zip', '71405; LA; Alexandria; Rapides Parish; Ball'], ['89065', 'zip', '71404; LA; Winn Parish; Atlanta'], ['89064', 'zip', '71403; LA; Fort Polk South; Vernon Parish; Anacoco'], ['89062', 'zip', '71378; LA; Franklin Parish; Wisner'], ['8906', 'city', 'Roachdale; IN; Indianapolis-Carmel-Anderson; Putnam County'], ['89059', 'zip', '71373; LA; Natchez; Concordia Parish; Vidalia'], ['89058', 'zip', '71371; LA; LaSalle Parish; Jena'], ['89057', 'zip', '71369; LA; Avoyelles Parish; Simmesport'], ['89055', 'zip', '71367; LA; Evangeline Parish; Saint Landry'], ['89054', 'zip', '71366; LA; Tensas Parish; Saint Joseph'], ['89051', 'zip', '71362; LA; Avoyelles Parish; Plaucheville'], ['89049', 'zip', '71360; LA; Alexandria; Rapides Parish; Pineville'], ['89047', 'zip', '71358; LA; Opelousas; Saint Landry Parish; Palmetto'], ['89046', 'zip', '71357; LA; Tensas Parish; Newellton'], ['89045', 'zip', '71356; LA; Opelousas; Saint Landry Parish; Morrow'], ['89044', 'zip', '71355; LA; Avoyelles Parish; Moreauville'], ['89043', 'zip', '71354; LA; Natchez; Concordia Parish; Monterey'], ['89042', 'zip', '71353; LA; Opelousas; Saint Landry Parish; Melville'], ['89041', 'zip', '71351; LA; Avoyelles Parish; Marksville'], ['89040', 'zip', '71350; LA; Avoyelles Parish; Mansura'], ['89038', 'zip', '71346; LA; Alexandria; Rapides Parish; Lecompte'], ['89036', 'zip', '71343; LA; Catahoula Parish; Jonesville'], ['89035', 'zip', '71342; LA; LaSalle Parish; Jena'], ['89034', 'zip', '71341; LA; Avoyelles Parish; Hessmer'], ['89031', 'zip', '71336; LA; Franklin Parish; Gilbert'], ['89030', 'zip', '71334; LA; Natchez; Concordia Parish; Ferriday'], ['89029', 'zip', '71333; LA; Avoyelles Parish; Evergreen'], ['89028', 'zip', '71331; LA; Avoyelles Parish; Effie'], ['89027', 'zip', '71330; LA; Avoyelles Parish; Marksville'], ['89025', 'zip', '71328; LA; Alexandria; Rapides Parish; Deville'], ['89024', 'zip', '71327; LA; Avoyelles Parish; Cottonport'], ['89023', 'zip', '71326; LA; Natchez; Concordia Parish; Clayton'], ['89022', 'zip', '71325; LA; Alexandria; Rapides Parish; Cheneyville'], ['89020', 'zip', '71323; LA; Avoyelles Parish; Center Point'], ['89019', 'zip', '71322; LA; Avoyelles Parish; Bunkie'], ['89012', 'zip', '71303; LA; Alexandria; Rapides Parish'], ['89011', 'zip', '71302; LA; Alexandria; Rapides Parish'], ['89010', 'zip', '71301; LA; Alexandria; Rapides Parish'], ['8901', 'city', 'Palmyra; MO; Hannibal; Marion County'], ['89009', 'zip', '71295; LA; Franklin Parish; Winnsboro'], ['89007', 'zip', '71292; LA; Monroe; Ouachita Parish; West Monroe'], ['89006', 'zip', '71291; LA; Monroe; Ouachita Parish; West Monroe'], ['89005', 'zip', '71286; LA; East Carroll Parish; Transylvania'], ['89001', 'zip', '71280; LA; Monroe; Ouachita Parish; Sterlington'], ['8900', 'city', 'Nashville; AR; Howard County'], ['890', 'county', 'Brazos County; TX; College Station-Bryan'], ['89', 'county', 'Lee County; VA'], ['88999', 'zip', '71277; LA; Monroe; Union Parish; Spearsville'], ['88998', 'zip', '71276; LA; East Carroll Parish; Sondheimer'], ['88997', 'zip', '71275; LA; Ruston; Lincoln Parish; Simsboro'], ['88994', 'zip', '71270; LA; Ruston; Lincoln Parish'], ['88993', 'zip', '71269; LA; Richland Parish; Rayville'], ['88992', 'zip', '71268; LA; Jackson Parish; North Hodge'], ['88991', 'zip', '71266; LA; West Carroll Parish; Pioneer'], ['88990', 'zip', '71264; LA; Bastrop; Morehouse Parish; Oak Ridge'], ['8899', 'city', 'Nashoba; OK; Pushmataha County'], ['88989', 'zip', '71263; LA; West Carroll Parish; Oak Grove'], ['88988', 'zip', '71261; LA; Bastrop; Morehouse Parish; Mer Rouge'], ['88987', 'zip', '71260; LA; Monroe; Union Parish; Marion'], ['88986', 'zip', '71259; LA; Richland Parish; Mangham'], ['88985', 'zip', '71256; LA; Monroe; Union Parish; Lillie'], ['88984', 'zip', '71254; LA; East Carroll Parish; Lake Providence'], ['88982', 'zip', '71251; LA; Jackson Parish; Jonesboro'], ['88981', 'zip', '71250; LA; Bastrop; Morehouse Parish; Jones'], ['88979', 'zip', '71247; LA; Jackson Parish; Hodge'], ['88978', 'zip', '71245; LA; Ruston; Lincoln Parish; Grambling'], ['88977', 'zip', '71243; LA; Catahoula Parish; Fort Necessity'], ['88975', 'zip', '71241; LA; Monroe; Union Parish; Farmerville'], ['88973', 'zip', '71238; LA; Monroe; Ouachita Parish; Eros'], ['88972', 'zip', '71237; LA; West Carroll Parish; Epps'], ['88971', 'zip', '71235; LA; Ruston; Lincoln Parish; Dubach'], ['88970', 'zip', '71234; LA; Monroe; Union Parish; Downsville'], ['88968', 'zip', '71232; LA; Richland Parish; Delhi'], ['88966', 'zip', '71229; LA; Bastrop; Morehouse Parish; Collinston'], ['88965', 'zip', '71227; LA; Ruston; Lincoln Parish; Choudrant'], ['88964', 'zip', '71226; LA; Jackson Parish; Chatham'], ['88963', 'zip', '71225; LA; Monroe; Ouachita Parish; Calhoun'], ['88962', 'zip', '71223; LA; Bastrop; Morehouse Parish; Bonita'], ['88961', 'zip', '71222; LA; Monroe; Union Parish; Bernice'], ['88959', 'zip', '71220; LA; Bastrop; Morehouse Parish'], ['88958', 'zip', '71219; LA; Franklin Parish; Baskin'], ['88949', 'zip', '71203; LA; Monroe; Ouachita Parish'], ['88948', 'zip', '71202; LA; Monroe; Ouachita Parish'], ['88947', 'zip', '71201; LA; Monroe; Ouachita Parish'], ['8893', 'city', 'Murrysville; PA; Pittsburgh; Westmoreland County'], ['88924', 'zip', '71129; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88922', 'zip', '71119; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88921', 'zip', '71118; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88920', 'zip', '71115; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88918', 'zip', '71112; LA; Shreveport-Bossier City; Bossier Parish; Bossier City'], ['88917', 'zip', '71111; LA; Shreveport-Bossier City; Bossier Parish; Bossier City'], ['88916', 'zip', '71110; LA; Shreveport-Bossier City; Bossier Parish; Benton'], ['88915', 'zip', '71109; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88914', 'zip', '71108; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88913', 'zip', '71107; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88912', 'zip', '71106; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88911', 'zip', '71105; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88910', 'zip', '71104; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['8891', 'city', 'Manning; SC; Clarendon County'], ['88909', 'zip', '71103; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88907', 'zip', '71101; LA; Shreveport-Bossier City; Caddo Parish; Shreveport'], ['88906', 'zip', '71082; LA; Shreveport-Bossier City; Caddo Parish; Vivian'], ['88903', 'zip', '71078; LA; Shreveport-Bossier City; De Soto Parish; Stonewall'], ['88902', 'zip', '71075; LA; Shreveport-Bossier City; Webster Parish; Springhill'], ['88901', 'zip', '71073; LA; Shreveport-Bossier City; Webster Parish; Sibley'], ['88900', 'zip', '71072; LA; Shreveport-Bossier City; Webster Parish; Shongaloo'], ['889', 'county', 'Branch County; MI; Coldwater'], ['88899', 'zip', '71071; LA; Shreveport-Bossier City; Webster Parish; Sarepta'], ['88898', 'zip', '71070; LA; Bienville Parish; Saline'], ['88896', 'zip', '71068; LA; Bienville Parish; Ringgold'], ['88895', 'zip', '71067; LA; Shreveport-Bossier City; Bossier Parish; Princeton'], ['88893', 'zip', '71065; LA; Sabine Parish; Pleasant Hill'], ['88892', 'zip', '71064; LA; Shreveport-Bossier City; Bossier Parish; Plain Dealing'], ['88890', 'zip', '71061; LA; Shreveport-Bossier City; Caddo Parish; Oil City'], ['8889', 'city', 'Mancelona; MI; Antrim County'], ['88889', 'zip', '71060; LA; Shreveport-Bossier City; Caddo Parish; Mooringsport'], ['88887', 'zip', '71055; LA; Shreveport-Bossier City; Webster Parish; Minden'], ['88886', 'zip', '71052; LA; Shreveport-Bossier City; De Soto Parish; Mansfield'], ['88885', 'zip', '71051; LA; Shreveport-Bossier City; Bossier Parish; Elm Grove'], ['88883', 'zip', '71049; LA; Shreveport-Bossier City; De Soto Parish; Logansport'], ['88881', 'zip', '71047; LA; Shreveport-Bossier City; Caddo Parish; Keithville'], ['88880', 'zip', '71046; LA; Shreveport-Bossier City; Caddo Parish; Keithville'], ['88879', 'zip', '71045; LA; Bienville Parish; Jamestown'], ['88878', 'zip', '71044; LA; Shreveport-Bossier City; Caddo Parish; Ida'], ['88877', 'zip', '71043; LA; Shreveport-Bossier City; Caddo Parish; Hosston'], ['88876', 'zip', '71040; LA; Claiborne Parish; Homer'], ['88875', 'zip', '71039; LA; Shreveport-Bossier City; Webster Parish; Heflin'], ['88874', 'zip', '71038; LA; Claiborne Parish; Haynesville'], ['88873', 'zip', '71037; LA; Shreveport-Bossier City; Bossier Parish; Haughton'], ['88872', 'zip', '71034; LA; Red River Parish; Hall Summit'], ['88871', 'zip', '71033; LA; Shreveport-Bossier City; Caddo Parish; Greenwood'], ['88870', 'zip', '71032; LA; Shreveport-Bossier City; De Soto Parish; Grand Cane'], ['88869', 'zip', '71031; LA; Natchitoches; Natchitoches Parish; Goldonna'], ['88868', 'zip', '71030; LA; Shreveport-Bossier City; De Soto Parish; Gloster'], ['88867', 'zip', '71029; LA; Shreveport-Bossier City; Caddo Parish; Gilliam'], ['88866', 'zip', '71028; LA; Bienville Parish; Gibsland'], ['88865', 'zip', '71027; LA; Shreveport-Bossier City; De Soto Parish; Frierson'], ['88864', 'zip', '71024; LA; Shreveport-Bossier City; Webster Parish; Dubberly'], ['88863', 'zip', '71023; LA; Shreveport-Bossier City; Webster Parish; Doyline'], ['88861', 'zip', '71019; LA; Red River Parish; Coushatta'], ['88860', 'zip', '71018; LA; Shreveport-Bossier City; Webster Parish; Cotton Valley'], ['8886', 'city', 'Raymond; IA; Waterloo-Cedar Falls; Black Hawk County'], ['88859', 'zip', '71016; LA; Bienville Parish; Castor'], ['88858', 'zip', '71009; LA; Shreveport-Bossier City; Caddo Parish; Blanchard'], ['88857', 'zip', '71008; LA; Bienville Parish; Bienville'], ['88856', 'zip', '71007; LA; Shreveport-Bossier City; Caddo Parish; Bethany'], ['88855', 'zip', '71006; LA; Shreveport-Bossier City; Bossier Parish; Benton'], ['88854', 'zip', '71004; LA; Shreveport-Bossier City; Caddo Parish; Belcher'], ['88853', 'zip', '71003; LA; Claiborne Parish; Athens'], ['88851', 'zip', '71001; LA; Bienville Parish; Arcadia'], ['8885', 'city', 'Rawlins; WY; Carbon County'], ['8884', 'city', 'Ravenswood; WV; Jackson County'], ['88829', 'zip', '70820; LA; Baton Rouge; East Baton Rouge Parish'], ['88828', 'zip', '70819; LA; Baton Rouge; East Baton Rouge Parish'], ['88827', 'zip', '70818; LA; Baton Rouge; East Baton Rouge Parish'], ['88826', 'zip', '70817; LA; Baton Rouge; East Baton Rouge Parish'], ['88825', 'zip', '70816; LA; Baton Rouge; East Baton Rouge Parish'], ['88824', 'zip', '70815; LA; Baton Rouge; East Baton Rouge Parish'], ['88823', 'zip', '70814; LA; Baton Rouge; East Baton Rouge Parish'], ['88821', 'zip', '70812; LA; Baton Rouge; East Baton Rouge Parish'], ['88820', 'zip', '70811; LA; Baton Rouge; East Baton Rouge Parish'], ['88819', 'zip', '70810; LA; Baton Rouge; East Baton Rouge Parish'], ['88818', 'zip', '70809'], ['88817', 'zip', '70808; LA; Baton Rouge; East Baton Rouge Parish'], ['88816', 'zip', '70807; LA; Baton Rouge; East Baton Rouge Parish'], ['88815', 'zip', '70806; LA; Baton Rouge; East Baton Rouge Parish'], ['88814', 'zip', '70805; LA; Baton Rouge; East Baton Rouge Parish'], ['88811', 'zip', '70802; LA; Baton Rouge; East Baton Rouge Parish'], ['88810', 'zip', '70801; LA; Baton Rouge; East Baton Rouge Parish'], ['8881', 'city', 'Randolph; NH; Berlin; Coos County'], ['88809', 'zip', '70792; LA; New Orleans-Metairie; Saint James Parish; Paulina'], ['88808', 'zip', '70791; LA; Baton Rouge; East Baton Rouge Parish; Zachary'], ['88807', 'zip', '70789; LA; Baton Rouge; East Feliciana Parish; Wilson'], ['88806', 'zip', '70788; LA; Baton Rouge; Iberville Parish; White Castle'], ['88803', 'zip', '70785; LA; Baton Rouge; Livingston Parish; Walker'], ['88801', 'zip', '70783; LA; Baton Rouge; Pointe Coupee Parish; Ventress'], ['88799', 'zip', '70780; LA; Baton Rouge; Iberville Parish; Sunshine'], ['88798', 'zip', '70778; LA; Baton Rouge; Ascension Parish; Sorrento'], ['88797', 'zip', '70777; LA; Baton Rouge; East Feliciana Parish; Slaughter'], ['88796', 'zip', '70776; LA; Baton Rouge; Iberville Parish; Saint Gabriel'], ['88795', 'zip', '70775; LA; Baton Rouge; West Feliciana Parish; Saint Francisville'], ['88794', 'zip', '70774; LA; Baton Rouge; Ascension Parish; Saint Amant'], ['88792', 'zip', '70772; LA; Baton Rouge; Iberville Parish; Rosedale'], ['88791', 'zip', '70770; LA; Baton Rouge; East Baton Rouge Parish; Pride'], ['88790', 'zip', '70769; LA; Baton Rouge; Ascension Parish; Prairieville'], ['88789', 'zip', '70767; LA; Baton Rouge; West Baton Rouge Parish; Port Allen'], ['88787', 'zip', '70764; LA; Baton Rouge; Iberville Parish; Plaquemine'], ['88786', 'zip', '70763; LA; New Orleans-Metairie; Saint James Parish; Paulina'], ['88785', 'zip', '70762; LA; Baton Rouge; Pointe Coupee Parish; Oscar'], ['88784', 'zip', '70761; LA; Baton Rouge; East Feliciana Parish; Norwood'], ['88783', 'zip', '70760; LA; Baton Rouge; Pointe Coupee Parish; New Roads'], ['88782', 'zip', '70759; LA; Baton Rouge; Pointe Coupee Parish; Morganza'], ['88781', 'zip', '70757; LA; Baton Rouge; Iberville Parish; Maringouin'], ['88780', 'zip', '70756; LA; Baton Rouge; Pointe Coupee Parish; Lottie'], ['8878', 'city', 'Ozark; MO; Springfield; Christian County'], ['88779', 'zip', '70755; LA; Baton Rouge; Pointe Coupee Parish; Livonia'], ['88778', 'zip', '70754; LA; Baton Rouge; Livingston Parish; Livingston'], ['88777', 'zip', '70753; LA; Baton Rouge; Pointe Coupee Parish; Lettsworth'], ['88776', 'zip', '70752; LA; Baton Rouge; Pointe Coupee Parish; Lakeland'], ['88775', 'zip', '70750; LA; Opelousas; Saint Landry Parish; Krotz Springs'], ['88774', 'zip', '70749; LA; Baton Rouge; Pointe Coupee Parish; Jarreau'], ['88773', 'zip', '70748; LA; Baton Rouge; East Feliciana Parish; Jackson'], ['88771', 'zip', '70744; LA; Baton Rouge; Livingston Parish; Holden'], ['88770', 'zip', '70743; LA; New Orleans-Metairie; Saint James Parish; Paulina'], ['8877', 'city', 'Oxford; GA; Atlanta-Sandy Springs-Roswell; Newton County'], ['88769', 'zip', '70740; LA; Baton Rouge; Iberville Parish; Grosse Tete'], ['88768', 'zip', '70739; LA; Baton Rouge; East Baton Rouge Parish; Greenwell Springs'], ['88766', 'zip', '70737; LA; Baton Rouge; Ascension Parish; Gonzales'], ['88765', 'zip', '70736; LA; Baton Rouge; Pointe Coupee Parish; Glynn'], ['88764', 'zip', '70734; LA; Baton Rouge; Ascension Parish; Geismar'], ['88763', 'zip', '70733; LA; Baton Rouge; Livingston Parish; French Settlement'], ['88762', 'zip', '70732; LA; Baton Rouge; Pointe Coupee Parish; Fordoche'], ['88761', 'zip', '70730; LA; Baton Rouge; East Feliciana Parish; Ethel'], ['88760', 'zip', '70729; LA; Baton Rouge; West Baton Rouge Parish; Erwinville'], ['8876', 'city', 'Munfordville; KY; Hart County'], ['88757', 'zip', '70726; LA; Baton Rouge; Livingston Parish; Denham Springs'], ['88756', 'zip', '70725; LA; Baton Rouge; Ascension Parish; Darrow'], ['88755', 'zip', '70723; LA; New Orleans-Metairie; Saint James Parish; Convent'], ['88754', 'zip', '70722; LA; Baton Rouge; East Feliciana Parish; Clinton'], ['88753', 'zip', '70721; LA; Baton Rouge; Iberville Parish; Carville'], ['88752', 'zip', '70719; LA; Baton Rouge; West Baton Rouge Parish; Brusly'], ['88750', 'zip', '70715; LA; Baton Rouge; Pointe Coupee Parish; Batchelor'], ['8875', 'city', 'Munds Park; AZ; Flagstaff; Coconino County'], ['88749', 'zip', '70714; LA; Baton Rouge; East Baton Rouge Parish; Baker'], ['88747', 'zip', '70711; LA; Baton Rouge; Livingston Parish; Albany'], ['88746', 'zip', '70710; LA; Baton Rouge; West Baton Rouge Parish; Addis'], ['88744', 'zip', '70706; LA; Baton Rouge; Livingston Parish; Denham Springs'], ['88742', 'zip', '70669; LA; Lake Charles; Calcasieu Parish; Westlake'], ['88741', 'zip', '70668; LA; Lake Charles; Calcasieu Parish; Vinton'], ['88740', 'zip', '70665; LA; Lake Charles; Calcasieu Parish; Sulphur'], ['8874', 'city', 'Munday; WV; Parkersburg-Vienna; Wirt County'], ['88738', 'zip', '70663; LA; Lake Charles; Calcasieu Parish; Sulphur'], ['88737', 'zip', '70662; LA; DeRidder; Beauregard Parish; Sugartown'], ['88736', 'zip', '70661; LA; Lake Charles; Calcasieu Parish; Starks'], ['88735', 'zip', '70660; LA; DeRidder; Beauregard Parish; Singer'], ['88734', 'zip', '70659; LA; Fort Polk South; Vernon Parish; Rosepine'], ['88733', 'zip', '70658; LA; Allen Parish; Reeves'], ['88732', 'zip', '70657; LA; DeRidder; Beauregard Parish; Ragley'], ['88731', 'zip', '70656; LA; Fort Polk South; Vernon Parish; Pitkin'], ['88730', 'zip', '70655; LA; Allen Parish; Oberlin'], ['8873', 'city', 'Mulino; OR; Portland-Vancouver-Hillsboro; Clackamas County'], ['88728', 'zip', '70653; LA; DeRidder; Beauregard Parish; Merryville'], ['88727', 'zip', '70652; LA; DeRidder; Beauregard Parish; Longville'], ['88725', 'zip', '70650; LA; Jennings; Jefferson Davis Parish; Lacassine'], ['88724', 'zip', '70648; LA; Allen Parish; Kinder'], ['88723', 'zip', '70647; LA; Lake Charles; Calcasieu Parish; Iowa'], ['88721', 'zip', '70645; LA; Lake Charles; Cameron Parish; Hackberry'], ['8872', 'city', 'Muenster; TX; Gainesville; Cooke County'], ['88719', 'zip', '70643; LA; Lake Charles; Cameron Parish; Grand Chenier'], ['88717', 'zip', '70639; LA; Fort Polk South; Vernon Parish; Evans'], ['88716', 'zip', '70638; LA; Allen Parish; Elizabeth'], ['88715', 'zip', '70637; LA; Allen Parish; Dry Creek'], ['88714', 'zip', '70634; LA; DeRidder; Beauregard Parish; Deridder'], ['88713', 'zip', '70633; LA; Lake Charles; Calcasieu Parish; Dequincy'], ['88712', 'zip', '70632; LA; Lake Charles; Cameron Parish; Creole'], ['88711', 'zip', '70631; LA; Lake Charles; Cameron Parish; Cameron'], ['88710', 'zip', '70630; LA; Lake Charles; Calcasieu Parish; Bell City'], ['88707', 'zip', '70615; LA; Lake Charles; Calcasieu Parish'], ['88705', 'zip', '70611; LA; Lake Charles; Calcasieu Parish'], ['88703', 'zip', '70607; LA; Lake Charles; Calcasieu Parish'], ['88701', 'zip', '70605; LA; Lake Charles; Calcasieu Parish'], ['887', 'county', 'Bosque County; TX'], ['88699', 'zip', '70601; LA; Lake Charles; Calcasieu Parish'], ['88695', 'zip', '70592; LA; Lafayette; Lafayette Parish; Youngsville'], ['88694', 'zip', '70591; LA; Jennings; Jefferson Davis Parish; Welsh'], ['88693', 'zip', '70589; LA; Opelousas; Saint Landry Parish; Washington'], ['88692', 'zip', '70586; LA; Evangeline Parish; Ville Platte'], ['88690', 'zip', '70584; LA; Opelousas; Saint Landry Parish; Sunset'], ['8869', 'city', 'Moville; IA; Sioux City; Woodbury County'], ['88689', 'zip', '70583; LA; Lafayette; Lafayette Parish; Scott'], ['88688', 'zip', '70582; LA; Lafayette; Saint Martin Parish; Saint Martinville'], ['88687', 'zip', '70581; LA; Jennings; Jefferson Davis Parish; Roanoke'], ['88685', 'zip', '70578; LA; Lafayette; Acadia Parish; Rayne'], ['88684', 'zip', '70577; LA; Opelousas; Saint Landry Parish; Port Barre'], ['88680', 'zip', '70570; LA; Opelousas; Saint Landry Parish'], ['88678', 'zip', '70563; LA; Lafayette; Iberia Parish; New Iberia'], ['88676', 'zip', '70560; LA; Lafayette; Iberia Parish; New Iberia'], ['88675', 'zip', '70559; LA; Lafayette; Acadia Parish; Morse'], ['88672', 'zip', '70555; LA; Lafayette; Vermilion Parish; Maurice'], ['88671', 'zip', '70554; LA; Evangeline Parish; Mamou'], ['88670', 'zip', '70552; LA; Lafayette; Iberia Parish; New Iberia'], ['8867', 'city', 'Mount Vernon; IA; Cedar Rapids; Linn County'], ['88667', 'zip', '70549; LA; Jennings; Jefferson Davis Parish; Lake Arthur'], ['88666', 'zip', '70548; LA; Lafayette; Vermilion Parish; Kaplan'], ['88665', 'zip', '70546; LA; Jennings; Jefferson Davis Parish'], ['88664', 'zip', '70544; LA; Morgan City; Saint Mary Parish; Jeanerette'], ['88663', 'zip', '70543; LA; Lafayette; Acadia Parish; Iota'], ['88662', 'zip', '70542; LA; Lafayette; Vermilion Parish; Gueydan'], ['88661', 'zip', '70541; LA; Opelousas; Saint Landry Parish; Grand Coteau'], ['88659', 'zip', '70538; LA; Morgan City; Saint Mary Parish; Franklin'], ['88658', 'zip', '70537; LA; Lafayette; Acadia Parish; Evangeline'], ['88657', 'zip', '70535; LA; Opelousas; Saint Landry Parish; Eunice'], ['88656', 'zip', '70534; LA; Lafayette; Acadia Parish; Crowley'], ['88655', 'zip', '70533; LA; Lafayette; Vermilion Parish; Erath'], ['88654', 'zip', '70532; LA; Jennings; Jefferson Davis Parish; Elton'], ['88652', 'zip', '70529; LA; Lafayette; Lafayette Parish; Duson'], ['88651', 'zip', '70528; LA; Lafayette; Vermilion Parish; Delcambre'], ['88649', 'zip', '70526; LA; Lafayette; Acadia Parish; Crowley'], ['88648', 'zip', '70525; LA; Lafayette; Acadia Parish; Church Point'], ['88644', 'zip', '70521; LA; Lafayette; Saint Martin Parish; Cecilia'], ['88643', 'zip', '70520; LA; Lafayette; Lafayette Parish; Carencro'], ['88642', 'zip', '70519; LA; Lafayette; Saint Martin Parish; Saint Martinville'], ['88641', 'zip', '70518; LA; Lafayette; Lafayette Parish; Broussard'], ['88640', 'zip', '70517; LA; Lafayette; Saint Martin Parish; Breaux Bridge'], ['88639', 'zip', '70516; LA; Lafayette; Acadia Parish; Branch'], ['88638', 'zip', '70515; LA; Evangeline Parish; Basile'], ['88637', 'zip', '70514; LA; Morgan City; Saint Mary Parish; Baldwin'], ['88635', 'zip', '70512; LA; Opelousas; Saint Landry Parish; Arnaudville'], ['88633', 'zip', '70510; LA; Lafayette; Vermilion Parish; Abbeville'], ['88631', 'zip', '70508; LA; Lafayette; Lafayette Parish'], ['88630', 'zip', '70507; LA; Lafayette; Lafayette Parish'], ['8863', 'city', 'Magnolia; NJ; Philadelphia-Camden-Wilmington; Camden County'], ['88629', 'zip', '70506; LA; Lafayette; Lafayette Parish'], ['88626', 'zip', '70503; LA; Lafayette; Lafayette Parish'], ['88624', 'zip', '70501; LA; Lafayette; Lafayette Parish'], ['88623', 'zip', '70471; LA; New Orleans-Metairie; Saint Tammany Parish; Mandeville'], ['88619', 'zip', '70466; LA; Hammond; Tangipahoa Parish; Tickfaw'], ['88617', 'zip', '70464; LA; New Orleans-Metairie; Saint Tammany Parish; Abita Springs'], ['88616', 'zip', '70463; LA; New Orleans-Metairie; Saint Tammany Parish; Sun'], ['88615', 'zip', '70462; LA; Baton Rouge; Livingston Parish; Killian'], ['88614', 'zip', '70461; LA; New Orleans-Metairie; Saint Tammany Parish; Slidell'], ['88613', 'zip', '70460; LA; New Orleans-Metairie; Saint Tammany Parish; Slidell'], ['88611', 'zip', '70458; LA; New Orleans-Metairie; Saint Tammany Parish; Slidell'], ['8861', 'city', 'Magee; MS; Jackson; Simpson County'], ['88609', 'zip', '70456; LA; Hammond; Tangipahoa Parish; Roseland'], ['88608', 'zip', '70455; LA; Hammond; Tangipahoa Parish; Robert'], ['88607', 'zip', '70454; LA; Hammond; Tangipahoa Parish; Ponchatoula'], ['88606', 'zip', '70453; LA; Baton Rouge; Saint Helena Parish; Pine Grove'], ['88605', 'zip', '70452; LA; New Orleans-Metairie; Saint Tammany Parish; Pearl River'], ['88604', 'zip', '70451; LA; Hammond; Tangipahoa Parish; Tickfaw'], ['88603', 'zip', '70450; LA; Bogalusa; Washington Parish; Mount Hermon'], ['88602', 'zip', '70449; LA; Baton Rouge; Livingston Parish; Maurepas'], ['88601', 'zip', '70448'], ['88600', 'zip', '70447; LA; New Orleans-Metairie; Saint Tammany Parish; Madisonville'], ['8860', 'city', 'Maeystown; IL; St. Louis; Monroe County'], ['88599', 'zip', '70446; LA; Hammond; Tangipahoa Parish; Loranger'], ['88598', 'zip', '70445; LA; New Orleans-Metairie; Saint Tammany Parish; Lacombe'], ['88597', 'zip', '70444; LA; Hammond; Tangipahoa Parish; Kentwood'], ['88596', 'zip', '70443; LA; Hammond; Tangipahoa Parish; Independence'], ['88595', 'zip', '70442; LA; Hammond; Tangipahoa Parish; Husser'], ['88594', 'zip', '70441; LA; Baton Rouge; Saint Helena Parish; Greensburg'], ['88593', 'zip', '70438; LA; Bogalusa; Washington Parish; Franklinton'], ['88592', 'zip', '70437; LA; New Orleans-Metairie; Saint Tammany Parish; Folsom'], ['88591', 'zip', '70436; LA; Hammond; Tangipahoa Parish; Roseland'], ['88590', 'zip', '70435; LA; New Orleans-Metairie; Saint Tammany Parish; Covington'], ['88588', 'zip', '70433'], ['88587', 'zip', '70431; LA; New Orleans-Metairie; Saint Tammany Parish; Bush'], ['88585', 'zip', '70427; LA; Bogalusa; Washington Parish'], ['88584', 'zip', '70426; LA; Bogalusa; Washington Parish; Varnado'], ['88583', 'zip', '70422; LA; Hammond; Tangipahoa Parish; Amite'], ['88582', 'zip', '70421; LA; Hammond; Tangipahoa Parish; Loranger'], ['88581', 'zip', '70420; LA; New Orleans-Metairie; Saint Tammany Parish; Abita Springs'], ['8858', 'city', 'Randall; IA; Hamilton County'], ['88579', 'zip', '70403; LA; Hammond; Tangipahoa Parish'], ['88577', 'zip', '70401; LA; Hammond; Tangipahoa Parish'], ['88576', 'zip', '70397; LA; Houma-Thibodaux; Terrebonne Parish; Theriot'], ['88575', 'zip', '70395; LA; Houma-Thibodaux; Terrebonne Parish; Schriever'], ['88574', 'zip', '70394; LA; Houma-Thibodaux; Lafourche Parish; Raceland'], ['88573', 'zip', '70393; LA; Assumption Parish; Plattenville'], ['88572', 'zip', '70392; LA; Morgan City; Saint Mary Parish; Patterson'], ['88571', 'zip', '70391; LA; Assumption Parish; Belle Rose'], ['88570', 'zip', '70390; LA; Assumption Parish; Napoleonville'], ['88568', 'zip', '70380; LA; Morgan City; Saint Mary Parish'], ['88567', 'zip', '70377; LA; Houma-Thibodaux; Terrebonne Parish; Montegut'], ['88566', 'zip', '70375; LA; Houma-Thibodaux; Lafourche Parish; Mathews'], ['88565', 'zip', '70374; LA; Houma-Thibodaux; Lafourche Parish; Lockport'], ['88564', 'zip', '70373; LA; Houma-Thibodaux; Lafourche Parish; Larose'], ['88563', 'zip', '70372; LA; Assumption Parish; Labadieville'], ['88561', 'zip', '70364; LA; Houma-Thibodaux; Terrebonne Parish; Houma'], ['88560', 'zip', '70363; LA; Houma-Thibodaux; Terrebonne Parish; Houma'], ['8856', 'city', 'Ralpho Township; PA; Sunbury; Northumberland County'], ['88558', 'zip', '70360; LA; Houma-Thibodaux; Terrebonne Parish; Houma'], ['88557', 'zip', '70359; LA; Houma-Thibodaux; Terrebonne Parish; Gray'], ['88556', 'zip', '70358; LA; New Orleans-Metairie; Jefferson Parish; Marrero'], ['88555', 'zip', '70357; LA; Houma-Thibodaux; Lafourche Parish; Golden Meadow'], ['88554', 'zip', '70356; LA; Houma-Thibodaux; Terrebonne Parish; Gibson'], ['88553', 'zip', '70355; LA; Houma-Thibodaux; Lafourche Parish; Gheens'], ['88552', 'zip', '70354; LA; Houma-Thibodaux; Lafourche Parish; Galliano'], ['88551', 'zip', '70353; LA; Houma-Thibodaux; Terrebonne Parish; Dulac'], ['88549', 'zip', '70346; LA; Baton Rouge; Ascension Parish; Donaldsonville'], ['88548', 'zip', '70345; LA; Houma-Thibodaux; Lafourche Parish; Cut Off'], ['88547', 'zip', '70344; LA; Houma-Thibodaux; Terrebonne Parish; Chauvin'], ['88546', 'zip', '70343; LA; Houma-Thibodaux; Terrebonne Parish; Bourg'], ['88545', 'zip', '70342; LA; Morgan City; Saint Mary Parish; Berwick'], ['88544', 'zip', '70341; LA; Assumption Parish; Belle Rose'], ['88542', 'zip', '70339; LA; Assumption Parish; Pierre Part'], ['8854', 'city', 'Rahway; NJ; New York-Newark-Jersey City; Union County'], ['88539', 'zip', '70301; LA; Houma-Thibodaux; Lafourche Parish; Thibodaux'], ['8850', 'city', 'Racine; WV; Charleston; Boone County'], ['885', 'county', 'Bonner County; ID; Sandpoint'], ['88494', 'zip', '70131; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88493', 'zip', '70130'], ['88492', 'zip', '70129; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88491', 'zip', '70128; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88490', 'zip', '70127; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88489', 'zip', '70126; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88488', 'zip', '70125'], ['88487', 'zip', '70124'], ['88486', 'zip', '70123; LA; New Orleans-Metairie; Jefferson Parish; River Ridge'], ['88485', 'zip', '70122; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88484', 'zip', '70121; LA; New Orleans-Metairie; Jefferson Parish; Jefferson'], ['88483', 'zip', '70119'], ['88482', 'zip', '70118'], ['88481', 'zip', '70117; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88480', 'zip', '70116'], ['8848', 'city', 'Otway; OH; Portsmouth; Scioto County'], ['88479', 'zip', '70115'], ['88478', 'zip', '70114; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88477', 'zip', '70113; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88476', 'zip', '70112; LA; New Orleans-Metairie; Orleans Parish; New Orleans'], ['88474', 'zip', '70094; LA; New Orleans-Metairie; Jefferson Parish; Westwego'], ['88473', 'zip', '70092; LA; New Orleans-Metairie; Saint Bernard Parish; Violet'], ['88471', 'zip', '70090; LA; New Orleans-Metairie; Saint James Parish; Vacherie'], ['88470', 'zip', '70087; LA; New Orleans-Metairie; Saint Charles Parish; Saint Rose'], ['88469', 'zip', '70086; LA; New Orleans-Metairie; Saint James Parish; Saint James'], ['88468', 'zip', '70085; LA; New Orleans-Metairie; Saint Bernard Parish; Saint Bernard'], ['88467', 'zip', '70084; LA; New Orleans-Metairie; Saint John the Baptist Parish; Reserve'], ['88466', 'zip', '70083; LA; New Orleans-Metairie; Plaquemines Parish; Port Sulphur'], ['88463', 'zip', '70080; LA; New Orleans-Metairie; Saint Charles Parish; Paradis'], ['88462', 'zip', '70079; LA; New Orleans-Metairie; Saint Charles Parish; Norco'], ['88460', 'zip', '70076; LA; New Orleans-Metairie; Saint John the Baptist Parish; Mount Airy'], ['8846', 'city', 'Town Of Otto; NY; Olean; Cattaraugus County'], ['88459', 'zip', '70075; LA; New Orleans-Metairie; Saint Bernard Parish; Meraux'], ['88457', 'zip', '70072; LA; New Orleans-Metairie; Jefferson Parish; Marrero'], ['88456', 'zip', '70071; LA; New Orleans-Metairie; Saint James Parish; Lutcher'], ['88455', 'zip', '70070; LA; New Orleans-Metairie; Saint Charles Parish; Luling'], ['88453', 'zip', '70068; LA; New Orleans-Metairie; Saint John the Baptist Parish; La Place'], ['88452', 'zip', '70067; LA; New Orleans-Metairie; Jefferson Parish; Marrero'], ['88451', 'zip', '70065; LA; New Orleans-Metairie; Jefferson Parish; Kenner'], ['88448', 'zip', '70062; LA; New Orleans-Metairie; Jefferson Parish; Kenner'], ['88445', 'zip', '70058; LA; New Orleans-Metairie; Jefferson Parish; Harvey'], ['88444', 'zip', '70057; LA; New Orleans-Metairie; Saint Charles Parish; Hahnville'], ['88443', 'zip', '70056; LA; New Orleans-Metairie; Jefferson Parish; Gretna'], ['88440', 'zip', '70053; LA; New Orleans-Metairie; Jefferson Parish; Gretna'], ['8844', 'city', 'Orwell; OH; Ashtabula; Ashtabula County'], ['88439', 'zip', '70052; LA; New Orleans-Metairie; Saint James Parish; Gramercy'], ['88438', 'zip', '70051; LA; New Orleans-Metairie; Saint John the Baptist Parish; Garyville'], ['88436', 'zip', '70049; LA; New Orleans-Metairie; Saint John the Baptist Parish; Edgard'], ['88435', 'zip', '70047; LA; New Orleans-Metairie; Saint Charles Parish; Destrehan'], ['88433', 'zip', '70043; LA; New Orleans-Metairie; Saint Bernard Parish; Chalmette'], ['88432', 'zip', '70041; LA; New Orleans-Metairie; Plaquemines Parish; Buras'], ['88431', 'zip', '70040; LA; New Orleans-Metairie; Plaquemines Parish; Braithwaite'], ['88430', 'zip', '70039; LA; New Orleans-Metairie; Saint Charles Parish; Boutte'], ['8843', 'city', 'Orrstown; PA; Chambersburg-Waynesboro; Franklin County'], ['88428', 'zip', '70037; LA; New Orleans-Metairie; Plaquemines Parish; Belle Chasse'], ['88427', 'zip', '70036; LA; New Orleans-Metairie; Jefferson Parish; Barataria'], ['88425', 'zip', '70032; LA; New Orleans-Metairie; Saint Bernard Parish; Arabi'], ['88424', 'zip', '70031; LA; New Orleans-Metairie; Saint Charles Parish; Ama'], ['88423', 'zip', '70030; LA; New Orleans-Metairie; Saint Charles Parish; Des Allemands'], ['8842', 'city', 'Mount Sterling; KY; Montgomery County'], ['88419', 'zip', '70006; LA; New Orleans-Metairie; Jefferson Parish; Metairie'], ['88418', 'zip', '70005; LA; New Orleans-Metairie; Jefferson Parish; Metairie'], ['88416', 'zip', '70003; LA; New Orleans-Metairie; Jefferson Parish; Metairie'], ['88415', 'zip', '70002; LA; New Orleans-Metairie; Jefferson Parish; Metairie'], ['88414', 'zip', '70001'], ['88413', 'zip', '69367; NE; Dawes County; Whitney'], ['88409', 'zip', '69361; NE; Scottsbluff; Scotts Bluff County'], ['88407', 'zip', '69358; NE; Scottsbluff; Scotts Bluff County; Morrill'], ['88406', 'zip', '69357; NE; Scottsbluff; Scotts Bluff County; Mitchell'], ['88405', 'zip', '69356; NE; Scottsbluff; Scotts Bluff County; Minatare'], ['88404', 'zip', '69355; NE; Scottsbluff; Scotts Bluff County; Gering'], ['88403', 'zip', '69354; NE; Dawes County; Marsland'], ['88401', 'zip', '69352; NE; Scottsbluff; Scotts Bluff County; Lyman'], ['884', 'county', 'Blount County; TN; Knoxville'], ['88398', 'zip', '69348; NE; Box Butte County; Hemingford'], ['88397', 'zip', '69347; NE; Sheridan County; Hay Springs'], ['88393', 'zip', '69341; NE; Scottsbluff; Scotts Bluff County; Gering'], ['88391', 'zip', '69339; NE; Dawes County; Crawford'], ['88390', 'zip', '69337; NE; Dawes County; Chadron'], ['88389', 'zip', '69336; NE; Morrill County; Bridgeport'], ['88387', 'zip', '69334; NE; Morrill County; Bayard'], ['88385', 'zip', '69331; NE; Morrill County; Angora'], ['88384', 'zip', '69301; NE; Box Butte County; Alliance'], ['88379', 'zip', '69217; NE; Brown County; Long Pine'], ['88377', 'zip', '69214; NE; Brown County; Johnstown'], ['88374', 'zip', '69210; NE; Brown County; Ainsworth'], ['88371', 'zip', '69171; NE; Lexington; Dawson County; Cozad'], ['88370', 'zip', '69170; NE; North Platte; Lincoln County; Wellfleet'], ['8837', 'city', 'Mount Hermon; KY; Monroe County'], ['88369', 'zip', '69169; NE; North Platte; Lincoln County; Wallace'], ['88365', 'zip', '69165; NE; North Platte; Lincoln County; Sutherland'], ['88364', 'zip', '69163; NE; Custer County; Arnold'], ['88363', 'zip', '69162; NE; Cheyenne County; Sidney'], ['88359', 'zip', '69156; NE; Cheyenne County; Potter'], ['88358', 'zip', '69155; NE; Keith County; Paxton'], ['88356', 'zip', '69153; NE; Keith County; Ogallala'], ['88354', 'zip', '69151; NE; North Platte; Lincoln County; Maxwell'], ['88352', 'zip', '69149; NE; Cheyenne County; Lodgepole'], ['88351', 'zip', '69148; NE; Garden County; Lisco'], ['88350', 'zip', '69147; NE; Keith County; Lewellen'], ['8835', 'city', 'Mount Enterprise; TX; Longview; Rusk County'], ['88349', 'zip', '69146; NE; Keith County; Lemoyne'], ['88347', 'zip', '69144; NE; Keith County; Keystone'], ['88346', 'zip', '69143; NE; North Platte; Lincoln County; Hershey'], ['88344', 'zip', '69141; NE; Cheyenne County; Gurley'], ['88342', 'zip', '69138; NE; Lexington; Dawson County; Gothenburg'], ['8834', 'city', 'Moultonborough; NH; Carroll County'], ['88338', 'zip', '69132; NE; North Platte; Lincoln County; Dickens'], ['88337', 'zip', '69131; NE; Cheyenne County; Dalton'], ['88336', 'zip', '69130; NE; Lexington; Dawson County; Cozad'], ['88333', 'zip', '69127; NE; Keith County; Brule'], ['88332', 'zip', '69125; NE; Morrill County; Broadwater'], ['88331', 'zip', '69123; NE; North Platte; Lincoln County; Brady'], ['88330', 'zip', '69122; NE; Deuel County; Big Springs'], ['88328', 'zip', '69120; NE; Custer County; Arnold'], ['88326', 'zip', '69101; NE; North Platte; Lincoln County'], ['88321', 'zip', '69042; NE; Frontier County; Stockville'], ['88318', 'zip', '69039; NE; Frontier County; Moorefield'], ['88317', 'zip', '69038; NE; Frontier County; Maywood'], ['88315', 'zip', '69036; NE; Red Willow County; Lebanon'], ['88314', 'zip', '69034; NE; Red Willow County; Indianola'], ['88310', 'zip', '69029; NE; Lexington; Dawson County; Farnam'], ['8831', 'city', 'Mosinee; WI; Wausau; Marathon County'], ['88309', 'zip', '69028; NE; Frontier County; Eustis'], ['88307', 'zip', '69026; NE; Red Willow County; Danbury'], ['88306', 'zip', '69025; NE; Frontier County; Curtis'], ['88305', 'zip', '69024; NE; Hitchcock County; Culbertson'], ['88303', 'zip', '69022; NE; Furnas County; Cambridge'], ['88301', 'zip', '69020; NE; Red Willow County; Bartley'], ['88300', 'zip', '69001; NE; Red Willow County; McCook'], ['8830', 'city', 'Mosheim; TN; Greeneville; Greene County'], ['883', 'county', 'Blount County; AL; Birmingham-Hoover'], ['88299', 'zip', '68982; NE; Kearney; Kearney County; Wilcox'], ['88298', 'zip', '68981; NE; Franklin County; Upland'], ['88297', 'zip', '68980; NE; Clay County; Trumbull'], ['88296', 'zip', '68979; NE; Clay County; Sutton'], ['88295', 'zip', '68978; NE; Nuckolls County; Superior'], ['88292', 'zip', '68975; NE; Clay County; Saronville'], ['88291', 'zip', '68974; NE; Nuckolls County; Ruskin'], ['88290', 'zip', '68973; NE; Hastings; Adams County; Roseland'], ['88287', 'zip', '68970; NE; Webster County; Red Cloud'], ['88283', 'zip', '68964; NE; Nuckolls County; Oak'], ['88282', 'zip', '68961; NE; Nuckolls County; Nelson'], ['88280', 'zip', '68959; NE; Kearney; Kearney County; Minden'], ['8828', 'city', 'Moscow; TX; Polk County'], ['88279', 'zip', '68958; NE; Phelps County; Loomis'], ['88278', 'zip', '68957; NE; Nuckolls County; Lawrence'], ['88277', 'zip', '68956; NE; Hastings; Adams County; Kenesaw'], ['88276', 'zip', '68955; NE; Hastings; Adams County; Juniata'], ['88275', 'zip', '68954; NE; Hastings; Adams County'], ['88274', 'zip', '68952; NE; Webster County; Inavale'], ['88273', 'zip', '68950; NE; Hastings; Adams County; Holstein'], ['88272', 'zip', '68949; NE; Phelps County; Holdrege'], ['88270', 'zip', '68947; NE; Franklin County; Hildreth'], ['8827', 'city', 'Town Of Lysander; NY; Syracuse; Onondaga County'], ['88268', 'zip', '68945; NE; Kearney; Kearney County; Heartwell'], ['88267', 'zip', '68944; NE; Clay County; Harvard'], ['88266', 'zip', '68943; NE; Nuckolls County; Hardy'], ['88265', 'zip', '68942; NE; Webster County; Guide Rock'], ['88264', 'zip', '68941; NE; Clay County; Glenvil'], ['88263', 'zip', '68940; NE; Phelps County; Funk'], ['88261', 'zip', '68938; NE; Clay County; Fairfield'], ['88260', 'zip', '68937; NE; Lexington; Gosper County; Elwood'], ['8826', 'city', 'Lyons; WI; Whitewater-Elkhorn; Walworth County'], ['88258', 'zip', '68935; NE; Clay County; Edgar'], ['88257', 'zip', '68934; NE; Clay County; Deweese'], ['88256', 'zip', '68933; NE; Clay County; Clay Center'], ['88255', 'zip', '68932; NE; Franklin County; Campbell'], ['88254', 'zip', '68930; NE; Webster County; Blue Hill'], ['88252', 'zip', '68928; NE; Webster County; Bladen'], ['88251', 'zip', '68927; NE; Phelps County; Bertrand'], ['8825', 'city', 'Lyndon; VT; Caledonia County'], ['88249', 'zip', '68925; NE; Hastings; Adams County; Ayr'], ['88248', 'zip', '68924; NE; Kearney; Kearney County; Axtell'], ['88247', 'zip', '68923; NE; Phelps County; Atlanta'], ['88243', 'zip', '68901; NE; Hastings; Adams County'], ['88242', 'zip', '68883; NE; Grand Island; Hall County; Wood River'], ['88241', 'zip', '68882; NE; Greeley County; Wolbach'], ['88240', 'zip', '68881; NE; Custer County; Westerville'], ['8824', 'city', 'Lyndon; IL; Sterling; Whiteside County'], ['88238', 'zip', '68878; NE; Lexington; Dawson County; Sumner'], ['88237', 'zip', '68876; NE; Kearney; Buffalo County; Shelton'], ['88235', 'zip', '68874; NE; Custer County; Sargent'], ['88234', 'zip', '68873; NE; Grand Island; Howard County; Saint Paul'], ['88233', 'zip', '68872; NE; Grand Island; Howard County; Saint Libory'], ['88231', 'zip', '68870; NE; Kearney; Buffalo County; Riverdale'], ['88230', 'zip', '68869; NE; Kearney; Buffalo County; Ravenna'], ['8823', 'city', 'Lyndhurst; OH; Cleveland-Elyria; Cuyahoga County'], ['88229', 'zip', '68866; NE; Kearney; Buffalo County; Pleasanton'], ['88228', 'zip', '68865; NE; Grand Island; Hamilton County; Phillips'], ['88227', 'zip', '68864; NE; Grand Island; Merrick County; Palmer'], ['88226', 'zip', '68863; NE; Lexington; Dawson County; Overton'], ['88224', 'zip', '68861; NE; Kearney; Buffalo County; Elm Creek'], ['88223', 'zip', '68860; NE; Custer County; Oconto'], ['88221', 'zip', '68858; NE; Kearney; Buffalo County; Miller'], ['88220', 'zip', '68856; NE; Custer County; Merna'], ['8822', 'city', 'Lynch Station; VA; Lynchburg; Campbell County'], ['88219', 'zip', '68855; NE; Custer County; Mason City'], ['88218', 'zip', '68854; NE; Grand Island; Hamilton County; Marquette'], ['88215', 'zip', '68850; NE; Lexington; Dawson County'], ['88212', 'zip', '68847; NE; Kearney; Buffalo County'], ['88211', 'zip', '68846; NE; Grand Island; Hamilton County; Hordville'], ['88210', 'zip', '68845; NE; Kearney; Buffalo County'], ['88209', 'zip', '68844; NE; Sherman County; Hazard'], ['88208', 'zip', '68843; NE; Grand Island; Hamilton County; Hampton'], ['88206', 'zip', '68841; NE; Grand Island; Hamilton County; Giltner'], ['88205', 'zip', '68840; NE; Kearney; Buffalo County; Gibbon'], ['88204', 'zip', '68838; NE; Grand Island; Howard County; Farwell'], ['88202', 'zip', '68836; NE; Kearney; Buffalo County; Elm Creek'], ['88201', 'zip', '68835; NE; Grand Island; Howard County; Elba'], ['88200', 'zip', '68834; NE; Lexington; Dawson County; Eddyville'], ['8820', 'city', 'Lyles; TN; Nashville-Davidson--Murfreesboro--Franklin; Hickman County'], ['882', 'county', 'Blanco County; TX'], ['88198', 'zip', '68832; NE; Grand Island; Hall County; Doniphan'], ['88197', 'zip', '68831; NE; Grand Island; Howard County; Dannebrog'], ['88196', 'zip', '68828; NE; Custer County; Comstock'], ['88195', 'zip', '68827; NE; Grand Island; Merrick County; Chapman'], ['88194', 'zip', '68826; NE; Grand Island; Merrick County; Central City'], ['88193', 'zip', '68825; NE; Custer County; Callaway'], ['88192', 'zip', '68824; NE; Grand Island; Hall County; Cairo'], ['88190', 'zip', '68822; NE; Custer County; Broken Bow'], ['88188', 'zip', '68820; NE; Grand Island; Howard County; Howard City'], ['88187', 'zip', '68818; NE; Grand Island; Hamilton County; Aurora'], ['88185', 'zip', '68816; NE; Grand Island; Merrick County; Archer'], ['88183', 'zip', '68814; NE; Custer County; Ansley'], ['88182', 'zip', '68813; NE; Custer County; Anselmo'], ['88181', 'zip', '68812; NE; Kearney; Buffalo County; Amherst'], ['88180', 'zip', '68810; NE; Grand Island; Hall County; Alda'], ['88179', 'zip', '68803; NE; Grand Island; Hall County'], ['88177', 'zip', '68801; NE; Grand Island; Hall County'], ['88176', 'zip', '68792; NE; Cedar County; Wynot'], ['88175', 'zip', '68791; NE; Cuming County; Wisner'], ['88174', 'zip', '68790; NE; Wayne County; Winside'], ['88173', 'zip', '68789; NE; Knox County; Winnetoon'], ['88172', 'zip', '68788; NE; Cuming County; West Point'], ['88171', 'zip', '68787; NE; Wayne County; Wayne'], ['88170', 'zip', '68786; NE; Knox County; Wausa'], ['8817', 'city', 'Lyburn; WV; Logan; Logan County'], ['88169', 'zip', '68785; NE; Sioux City; Dixon County; Waterbury'], ['88168', 'zip', '68784; NE; Sioux City; Dixon County; Wakefield'], ['88167', 'zip', '68783; NE; Knox County; Verdigre'], ['88166', 'zip', '68781; NE; Norfolk; Madison County; Tilden'], ['88165', 'zip', '68780; NE; Holt County; Stuart'], ['88164', 'zip', '68779; NE; Norfolk; Stanton County; Stanton'], ['88162', 'zip', '68777; NE; Boyd County; Spencer'], ['88161', 'zip', '68776; NE; Sioux City; Dakota County; South Sioux City'], ['88160', 'zip', '68774; NE; Cedar County; Saint Helena'], ['88159', 'zip', '68773; NE; Antelope County; Royal'], ['88158', 'zip', '68771; NE; Cedar County; Randolph'], ['88157', 'zip', '68770; NE; Sioux City; Dixon County; Ponca'], ['88156', 'zip', '68769; NE; Norfolk; Pierce County; Plainview'], ['88155', 'zip', '68768; NE; Norfolk; Stanton County; Pilger'], ['88154', 'zip', '68767; NE; Norfolk; Pierce County; Pierce'], ['88153', 'zip', '68766; NE; Holt County; Page'], ['88152', 'zip', '68765; NE; Norfolk; Pierce County; Osmond'], ['88151', 'zip', '68764; NE; Antelope County; Orchard'], ['88150', 'zip', "68763; NE; Holt County; O'' Neill"], ['88149', 'zip', '68761; NE; Antelope County; Oakdale'], ['88148', 'zip', '68760; NE; Knox County; Niobrara'], ['88146', 'zip', '68758; NE; Norfolk; Madison County; Newman Grove'], ['88145', 'zip', '68757; NE; Sioux City; Dixon County; Newcastle'], ['88144', 'zip', '68756; NE; Antelope County; Neligh'], ['88141', 'zip', '68752; NE; Norfolk; Madison County; Meadow Grove'], ['88140', 'zip', '68751; NE; Sioux City; Dixon County; Maskell'], ['8814', 'city', 'Queen Creek; AZ; Phoenix-Mesa-Scottsdale; Pinal County'], ['88139', 'zip', '68749; NE; Cedar County; Magnet'], ['88138', 'zip', '68748; NE; Norfolk; Madison County; Madison'], ['88137', 'zip', '68747; NE; Norfolk; Pierce County; McLean'], ['88136', 'zip', '68746; NE; Boyd County; Lynch'], ['88135', 'zip', '68745; NE; Cedar County; Laurel'], ['88134', 'zip', '68743; NE; Sioux City; Dakota County; Jackson'], ['88133', 'zip', '68742; NE; Holt County; Inman'], ['88132', 'zip', '68741; NE; Sioux City; Dakota County; Hubbard'], ['88131', 'zip', '68740; NE; Wayne County; Hoskins'], ['88130', 'zip', '68739; NE; Cedar County; Hartington'], ['88128', 'zip', '68736; NE; Cedar County; Fordyce'], ['88127', 'zip', '68735; NE; Holt County; Ewing'], ['88126', 'zip', '68734; NE; Holt County; Emmet'], ['88125', 'zip', '68733; NE; Thurston County; Emerson'], ['88124', 'zip', '68732; NE; Sioux City; Dixon County; Dixon'], ['88123', 'zip', '68731; NE; Sioux City; Dakota County; Dakota City'], ['88122', 'zip', '68730; NE; Knox County; Crofton'], ['88121', 'zip', '68729; NE; Knox County; Creighton'], ['88120', 'zip', '68728; NE; Sioux City; Dixon County; Concord'], ['88119', 'zip', '68727; NE; Cedar County; Coleridge'], ['88118', 'zip', '68726; NE; Antelope County; Clearwater'], ['88117', 'zip', '68725; NE; Holt County; Chambers'], ['88116', 'zip', '68724; NE; Knox County; Center'], ['88115', 'zip', '68723; NE; Wayne County; Carroll'], ['88113', 'zip', '68720; NE; Antelope County; Brunswick'], ['88111', 'zip', '68718; NE; Knox County; Bloomfield'], ['88110', 'zip', '68717; NE; Cedar County; Belden'], ['88109', 'zip', '68716; NE; Cuming County; Beemer'], ['88108', 'zip', '68715; NE; Norfolk; Madison County; Battle Creek'], ['88106', 'zip', '68713; NE; Holt County; Atkinson'], ['88105', 'zip', '68711; NE; Holt County; Amelia'], ['88104', 'zip', '68710; NE; Sioux City; Dixon County; Allen'], ['88102', 'zip', '68701; NE; Norfolk; Madison County'], ['88101', 'zip', '68669; NE; Butler County; Ulysses'], ['88100', 'zip', '68667; NE; Butler County; Surprise'], ['881', 'county', 'Blaine County; OK'], ['88099', 'zip', '68666; NE; Polk County; Stromsburg'], ['88097', 'zip', '68664; NE; Fremont; Dodge County; Dodge'], ['88096', 'zip', '68663; NE; Grand Island; Merrick County; Silver Creek'], ['88095', 'zip', '68662; NE; Polk County; Shelby'], ['88094', 'zip', '68661; NE; Colfax County; Schuyler'], ['88093', 'zip', '68660; NE; Boone County; Saint Edward'], ['88092', 'zip', '68659; NE; Colfax County; Rogers'], ['88091', 'zip', '68658; NE; Butler County; Rising City'], ['88090', 'zip', '68655; NE; Boone County; Primrose'], ['8809', 'city', 'Pylesville; MD; Baltimore-Columbia-Towson; Harford County'], ['88089', 'zip', '68654; NE; Polk County; Polk'], ['88088', 'zip', '68653; NE; Columbus; Platte County; Platte Center'], ['88087', 'zip', '68652; NE; Boone County; Petersburg'], ['88086', 'zip', '68651; NE; Polk County; Osceola'], ['88085', 'zip', '68649; NE; Fremont; Dodge County; North Bend'], ['88084', 'zip', '68648; NE; Omaha-Council Bluffs; Saunders County; Morse Bluff'], ['88083', 'zip', '68647; NE; Columbus; Platte County; Monroe'], ['88082', 'zip', '68644; NE; Columbus; Platte County; Lindsay'], ['88081', 'zip', '68643; NE; Colfax County; Leigh'], ['88080', 'zip', '68642; NE; Columbus; Platte County; Humphrey'], ['88079', 'zip', '68641; NE; Colfax County; Howells'], ['88078', 'zip', '68640; NE; Nance County; Genoa'], ['88077', 'zip', '68638; NE; Nance County; Fullerton'], ['88075', 'zip', '68636; NE; Antelope County; Elgin'], ['88074', 'zip', '68635; NE; Butler County; Dwight'], ['88073', 'zip', '68634; NE; Columbus; Platte County; Duncan'], ['88072', 'zip', '68633; NE; Fremont; Dodge County; Dodge'], ['88071', 'zip', '68632; NE; Butler County; David City'], ['88070', 'zip', '68631; NE; Columbus; Platte County; Creston'], ['88069', 'zip', '68629; NE; Colfax County; Clarkson'], ['88068', 'zip', '68628; NE; Grand Island; Merrick County; Clarks'], ['88067', 'zip', '68627; NE; Boone County; Cedar Rapids'], ['88066', 'zip', '68626; NE; Butler County; Brainard'], ['88065', 'zip', '68624; NE; Butler County; Bellwood'], ['88064', 'zip', '68623; NE; Nance County; Belgrade'], ['88062', 'zip', '68621; NE; Fremont; Dodge County; Ames'], ['88061', 'zip', '68620; NE; Boone County; Albion'], ['8806', 'city', 'Pulaski; WI; Green Bay; Brown County'], ['88059', 'zip', '68601; NE; Columbus; Platte County'], ['88055', 'zip', '68532; NE; Lincoln; Lancaster County'], ['88054', 'zip', '68531; NE; Lincoln; Lancaster County'], ['88052', 'zip', '68528; NE; Lincoln; Lancaster County'], ['88051', 'zip', '68527; NE; Lincoln; Lancaster County'], ['88050', 'zip', '68526; NE; Lincoln; Lancaster County'], ['8805', 'city', 'Orick; CA; Eureka-Arcata-Fortuna; Humboldt County'], ['88049', 'zip', '68524; NE; Lincoln; Lancaster County'], ['88048', 'zip', '68523; NE; Lincoln; Lancaster County'], ['88047', 'zip', '68522; NE; Lincoln; Lancaster County'], ['88046', 'zip', '68521; NE; Lincoln; Lancaster County'], ['88045', 'zip', '68520; NE; Lincoln; Lancaster County'], ['88044', 'zip', '68517; NE; Lincoln; Lancaster County'], ['88043', 'zip', '68516; NE; Lincoln; Lancaster County'], ['88042', 'zip', '68514; NE; Lincoln; Lancaster County'], ['88041', 'zip', '68512; NE; Lincoln; Lancaster County'], ['88040', 'zip', '68510; NE; Lincoln; Lancaster County'], ['88038', 'zip', '68508; NE; Lincoln; Lancaster County'], ['88037', 'zip', '68507; NE; Lincoln; Lancaster County'], ['88036', 'zip', '68506; NE; Lincoln; Lancaster County'], ['88035', 'zip', '68505; NE; Lincoln; Lancaster County'], ['88034', 'zip', '68504; NE; Lincoln; Lancaster County'], ['88033', 'zip', '68503; NE; Lincoln; Lancaster County'], ['88032', 'zip', '68502; NE; Lincoln; Lancaster County'], ['88030', 'zip', '68467; NE; York County; York'], ['88029', 'zip', '68466; NE; Beatrice; Gage County; Wymore'], ['88028', 'zip', '68465; NE; Saline County; Wilber'], ['88027', 'zip', '68464; NE; Saline County; Western'], ['88026', 'zip', '68463; NE; Omaha-Council Bluffs; Cass County; Weeping Water'], ['88025', 'zip', '68462; NE; Lincoln; Lancaster County; Waverly'], ['88024', 'zip', '68461; NE; Lincoln; Lancaster County; Walton'], ['88023', 'zip', '68460; NE; York County; Waco'], ['88022', 'zip', '68458; NE; Beatrice; Gage County; Virginia'], ['88021', 'zip', '68457; NE; Richardson County; Verdon'], ['88020', 'zip', '68456; NE; Lincoln; Seward County; Utica'], ['8802', 'city', 'Morocco; IN; Chicago-Naperville-Elgin; Newton County'], ['88019', 'zip', '68455; NE; Omaha-Council Bluffs; Cass County; Union'], ['88018', 'zip', '68454; NE; Otoe County; Unadilla'], ['88017', 'zip', '68453; NE; Saline County; Tobias'], ['88016', 'zip', '68452; NE; Clay County; Ong'], ['88015', 'zip', '68450; NE; Johnson County; Tecumseh'], ['88014', 'zip', '68448; NE; Otoe County; Talmage'], ['88013', 'zip', '68447; NE; Pawnee County; Table Rock'], ['88012', 'zip', '68446; NE; Otoe County; Syracuse'], ['88011', 'zip', '68445; NE; Saline County; Swanton'], ['88010', 'zip', '68444; NE; Fillmore County; Strang'], ['8801', 'city', 'Moro; OR; Sherman County'], ['88009', 'zip', '68443; NE; Johnson County; Sterling'], ['88008', 'zip', '68442; NE; Richardson County; Stella'], ['88007', 'zip', '68441; NE; Pawnee County; Steinauer'], ['88006', 'zip', '68440; NE; Jefferson County; Steele City'], ['88005', 'zip', '68439; NE; Lincoln; Seward County; Staplehurst'], ['88004', 'zip', '68438; NE; Lincoln; Lancaster County; Martell'], ['88003', 'zip', '68437; NE; Richardson County; Shubert'], ['88002', 'zip', '68436; NE; Fillmore County; Shickley'], ['88001', 'zip', '68434; NE; Lincoln; Seward County; Seward'], ['88000', 'zip', '68433; NE; Richardson County; Verdon'], ['88', 'county', 'Lee County; TX'], ['87999', 'zip', '68431; NE; Richardson County; Rulo'], ['87998', 'zip', '68430; NE; Lincoln; Lancaster County; Roca'], ['87997', 'zip', '68429; NE; Jefferson County; Reynolds'], ['87996', 'zip', '68428; NE; Lincoln; Lancaster County; Raymond'], ['87995', 'zip', '68424; NE; Jefferson County; Plymouth'], ['87994', 'zip', '68423; NE; Lincoln; Seward County; Pleasant Dale'], ['87993', 'zip', '68422; NE; Beatrice; Gage County; Pickrell'], ['87992', 'zip', '68421; NE; Nemaha County; Peru'], ['87991', 'zip', '68420; NE; Pawnee County; Pawnee City'], ['87990', 'zip', '68419; NE; Lincoln; Lancaster County; Panama'], ['87989', 'zip', '68418; NE; Otoe County; Palmyra'], ['87988', 'zip', '68417; NE; Otoe County; Otoe'], ['87987', 'zip', '68416; NE; Fillmore County; Ohiowa'], ['87986', 'zip', '68415; NE; Beatrice; Gage County; Odell'], ['87985', 'zip', '68414; NE; Nemaha County; Nemaha'], ['87984', 'zip', '68413; NE; Omaha-Council Bluffs; Cass County; Nehawka'], ['87983', 'zip', '68410; NE; Otoe County; Nebraska City'], ['87982', 'zip', '68409; NE; Omaha-Council Bluffs; Cass County; Murray'], ['87981', 'zip', '68407; NE; Omaha-Council Bluffs; Cass County; Murdock'], ['87980', 'zip', '68406; NE; Fillmore County; Milligan'], ['87979', 'zip', '68405; NE; Lincoln; Seward County; Milford'], ['87978', 'zip', '68404; NE; Lincoln; Lancaster County; Martell'], ['87977', 'zip', '68403; NE; Omaha-Council Bluffs; Cass County; Manley'], ['87976', 'zip', '68402; NE; Lincoln; Lancaster County; Malcolm'], ['87975', 'zip', '68401; NE; York County; McCool Junction'], ['87974', 'zip', '68382; NE; Otoe County; Lorton'], ['87973', 'zip', '68381; NE; Beatrice; Gage County; Liberty'], ['87972', 'zip', '68380; NE; Pawnee County; Lewiston'], ['87971', 'zip', '68378; NE; Nemaha County; Johnson'], ['87970', 'zip', '68377; NE; Jefferson County; Jansen'], ['8797', 'city', 'Moose Lake; MN; Duluth; Carlton County'], ['87969', 'zip', '68376; NE; Richardson County; Humboldt'], ['87967', 'zip', '68372; NE; Lincoln; Lancaster County; Hickman'], ['87966', 'zip', '68371; NE; York County; Henderson'], ['87964', 'zip', '68368; NE; Lincoln; Lancaster County; Hallam'], ['87963', 'zip', '68367; NE; York County; Gresham'], ['87962', 'zip', '68366; NE; Omaha-Council Bluffs; Cass County; Greenwood'], ['87961', 'zip', '68365; NE; Fillmore County; Grafton'], ['87960', 'zip', '68364; NE; Lincoln; Seward County; Goehner'], ['8796', 'city', 'Moorhead; IA; Monona County'], ['87958', 'zip', '68361; NE; Fillmore County; Geneva'], ['87957', 'zip', '68360; NE; Lincoln; Seward County; Garland'], ['87956', 'zip', '68359; NE; Saline County; Friend'], ['87955', 'zip', '68358; NE; Lincoln; Lancaster County; Firth'], ['87954', 'zip', '68357; NE; Beatrice; Gage County; Filley'], ['87953', 'zip', '68355; NE; Richardson County; Falls City'], ['87952', 'zip', '68354; NE; Fillmore County; Fairmont'], ['87951', 'zip', '68352; NE; Jefferson County; Fairbury'], ['87950', 'zip', '68351; NE; Fillmore County; Exeter'], ['8795', 'city', 'Moore Haven; FL; Glades County'], ['87949', 'zip', '68350; NE; Jefferson County; Endicott'], ['87948', 'zip', '68349; NE; Omaha-Council Bluffs; Cass County; Elmwood'], ['87947', 'zip', '68348; NE; Johnson County; Elk Creek'], ['87946', 'zip', '68347; NE; Omaha-Council Bluffs; Cass County; Eagle'], ['87945', 'zip', '68346; NE; Otoe County; Dunbar'], ['87944', 'zip', '68345; NE; Pawnee County; Du Bois'], ['87943', 'zip', '68344; NE; Otoe County; Douglas'], ['87942', 'zip', '68343; NE; Saline County; Dorchester'], ['87941', 'zip', '68342; NE; Jefferson County; Diller'], ['87940', 'zip', '68341; NE; Saline County; De Witt'], ['87938', 'zip', '68339; NE; Lincoln; Lancaster County; Denton'], ['87937', 'zip', '68338; NE; Jefferson County; Daykin'], ['87936', 'zip', '68337; NE; Richardson County; Dawson'], ['87935', 'zip', '68336; NE; Lincoln; Lancaster County; Davey'], ['87934', 'zip', '68335; NE; Thayer County; Davenport'], ['87933', 'zip', '68333; NE; Saline County; Crete'], ['87932', 'zip', '68332; NE; Johnson County; Crab Orchard'], ['87931', 'zip', '68331; NE; Beatrice; Gage County; Cortland'], ['87930', 'zip', '68330; NE; Lincoln; Seward County; Cordova'], ['87929', 'zip', '68329; NE; Johnson County; Cook'], ['87928', 'zip', '68328; NE; Beatrice; Gage County; Clatonia'], ['87924', 'zip', '68324; NE; Otoe County; Burr'], ['87923', 'zip', '68323; NE; Pawnee County; Burchard'], ['87922', 'zip', '68322; NE; Thayer County; Bruning'], ['87921', 'zip', '68321; NE; Nemaha County; Brownville'], ['87920', 'zip', '68320; NE; Nemaha County; Brock'], ['87919', 'zip', '68319; NE; York County; Bradshaw'], ['87918', 'zip', '68318; NE; Beatrice; Gage County; Blue Springs'], ['87917', 'zip', '68317; NE; Lincoln; Lancaster County; Bennet'], ['87916', 'zip', '68316; NE; York County; Benedict'], ['87914', 'zip', '68314; NE; Lincoln; Seward County; Bee'], ['87913', 'zip', '68313; NE; Lincoln; Seward County; Beaver Crossing'], ['87912', 'zip', '68310; NE; Beatrice; Gage County'], ['87911', 'zip', '68309; NE; Beatrice; Gage County; Barneston'], ['87910', 'zip', '68307; NE; Omaha-Council Bluffs; Cass County; Avoca'], ['87909', 'zip', '68305; NE; Nemaha County; Auburn'], ['87908', 'zip', '68304; NE; Omaha-Council Bluffs; Cass County; Alvo'], ['87906', 'zip', '68301; NE; Beatrice; Gage County; Adams'], ['87894', 'zip', '68164; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87893', 'zip', '68157; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87891', 'zip', '68154; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87890', 'zip', '68152; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87889', 'zip', '68147; NE; Omaha-Council Bluffs; Sarpy County; Bellevue'], ['87887', 'zip', '68144; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87886', 'zip', '68142; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87884', 'zip', '68138; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87883', 'zip', '68137; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87882', 'zip', '68136'], ['87881', 'zip', '68135; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87880', 'zip', '68134; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['8788', 'city', 'Lower Peach Tree; AL; Wilcox County'], ['87879', 'zip', '68133; NE; Omaha-Council Bluffs; Sarpy County; Papillion'], ['87878', 'zip', '68132'], ['87877', 'zip', '68131'], ['87876', 'zip', '68130; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87875', 'zip', '68128; NE; Omaha-Council Bluffs; Sarpy County; La Vista'], ['87874', 'zip', '68127; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87873', 'zip', '68124; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87872', 'zip', '68123'], ['87871', 'zip', '68122; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['8787', 'city', 'Lower Chichester Township; PA; Philadelphia-Camden-Wilmington; Delaware County'], ['87868', 'zip', '68118; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87867', 'zip', '68117; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87866', 'zip', '68116; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87865', 'zip', '68114; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87863', 'zip', '68112; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87862', 'zip', '68111'], ['87861', 'zip', '68110; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87859', 'zip', '68108; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87858', 'zip', '68107; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87857', 'zip', '68106'], ['87856', 'zip', '68105'], ['87855', 'zip', '68104'], ['87853', 'zip', '68102; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87851', 'zip', '68073; NE; Omaha-Council Bluffs; Saunders County; Yutan'], ['87850', 'zip', '68072; NE; Fremont; Dodge County; Hooper'], ['8785', 'city', 'Loveville; MD; California-Lexington Park; Saint Marys County'], ['87849', 'zip', '68071; NE; Thurston County; Winnebago'], ['87848', 'zip', '68070; NE; Omaha-Council Bluffs; Saunders County; Weston'], ['87847', 'zip', '68069; NE; Omaha-Council Bluffs; Douglas County; Waterloo'], ['87846', 'zip', '68068; NE; Omaha-Council Bluffs; Washington County; Washington'], ['87845', 'zip', '68067; NE; Thurston County; Walthill'], ['87844', 'zip', '68066; NE; Omaha-Council Bluffs; Saunders County; Wahoo'], ['87843', 'zip', '68065; NE; Omaha-Council Bluffs; Saunders County; Valparaiso'], ['87842', 'zip', '68064; NE; Omaha-Council Bluffs; Douglas County; Valley'], ['87841', 'zip', '68063; NE; Fremont; Dodge County; Hooper'], ['87840', 'zip', '68062; NE; Thurston County; Thurston'], ['87839', 'zip', '68061; NE; Burt County; Tekamah'], ['87838', 'zip', '68059; NE; Omaha-Council Bluffs; Sarpy County; Springfield'], ['87837', 'zip', '68058; NE; Omaha-Council Bluffs; Cass County; South Bend'], ['87836', 'zip', '68057; NE; Fremont; Dodge County; Scribner'], ['87834', 'zip', '68055; NE; Thurston County; Rosalie'], ['87833', 'zip', '68050; NE; Omaha-Council Bluffs; Saunders County; Prague'], ['87832', 'zip', '68048; NE; Omaha-Council Bluffs; Cass County; Plattsmouth'], ['87831', 'zip', '68047; NE; Thurston County; Pender'], ['87830', 'zip', '68046; NE; Omaha-Council Bluffs; Sarpy County; Papillion'], ['87829', 'zip', '68045; NE; Burt County; Oakland'], ['87828', 'zip', '68044; NE; Fremont; Dodge County; Nickerson'], ['87827', 'zip', '68042; NE; Omaha-Council Bluffs; Saunders County; Memphis'], ['87826', 'zip', '68041; NE; Omaha-Council Bluffs; Saunders County; Mead'], ['87825', 'zip', '68040; NE; Omaha-Council Bluffs; Saunders County; Malmo'], ['87824', 'zip', '68039; NE; Thurston County; Macy'], ['87823', 'zip', '68038; NE; Burt County; Lyons'], ['87822', 'zip', '68037; NE; Omaha-Council Bluffs; Cass County; Louisville'], ['87821', 'zip', '68036; NE; Butler County; Linwood'], ['87820', 'zip', '68034; NE; Omaha-Council Bluffs; Washington County; Kennard'], ['87819', 'zip', '68033; NE; Omaha-Council Bluffs; Saunders County; Ithaca'], ['87818', 'zip', '68031; NE; Fremont; Dodge County; Hooper'], ['87817', 'zip', '68030; NE; Sioux City; Dakota County; Homer'], ['87816', 'zip', '68029; NE; Omaha-Council Bluffs; Washington County; Herman'], ['87815', 'zip', '68028; NE; Omaha-Council Bluffs; Sarpy County; Gretna'], ['87813', 'zip', '68025; NE; Fremont; Dodge County'], ['87812', 'zip', '68023; NE; Omaha-Council Bluffs; Washington County; Fort Calhoun'], ['87811', 'zip', '68022; NE; Omaha-Council Bluffs; Douglas County; Omaha'], ['87810', 'zip', '68020; NE; Burt County; Decatur'], ['8781', 'city', 'Prinsburg; MN; Willmar; Kandiyohi County'], ['87809', 'zip', '68019; NE; Burt County; Craig'], ['87808', 'zip', '68018; NE; Omaha-Council Bluffs; Saunders County; Colon'], ['87807', 'zip', '68017; NE; Omaha-Council Bluffs; Saunders County; Ceresco'], ['87806', 'zip', '68016; NE; Omaha-Council Bluffs; Cass County; Cedar Creek'], ['87805', 'zip', '68015; NE; Omaha-Council Bluffs; Saunders County; Cedar Bluffs'], ['87804', 'zip', '68014; NE; Butler County; Bruno'], ['87801', 'zip', '68008; NE; Omaha-Council Bluffs; Washington County; Blair'], ['87800', 'zip', '68007; NE; Omaha-Council Bluffs; Douglas County; Bennington'], ['8780', 'city', 'Preston; IA; Jackson County'], ['878', 'county', 'Blaine County; ID; Hailey'], ['87799', 'zip', '68005'], ['87798', 'zip', '68004; NE; Cuming County; Bancroft'], ['87797', 'zip', '68003; NE; Omaha-Council Bluffs; Saunders County; Ashland'], ['87796', 'zip', '68002; NE; Omaha-Council Bluffs; Washington County; Arlington'], ['87794', 'zip', '67954; KS; Morton County; Rolla'], ['87792', 'zip', '67952; KS; Stevens County; Moscow'], ['87791', 'zip', '67951; KS; Stevens County; Hugoton'], ['87788', 'zip', '67901; KS; Liberal; Seward County'], ['87787', 'zip', '67882; KS; Dodge City; Ford County; Wright'], ['87786', 'zip', '67880; KS; Grant County; Ulysses'], ['87783', 'zip', '67877; KS; Haskell County; Sublette'], ['87782', 'zip', '67876; KS; Dodge City; Ford County; Spearville'], ['87781', 'zip', '67871; KS; Scott County; Scott City'], ['87780', 'zip', '67870; KS; Haskell County; Satanta'], ['8778', 'city', 'Ooltewah; TN; Chattanooga; Hamilton County'], ['87779', 'zip', '67869; KS; Meade County; Plains'], ['87778', 'zip', '67868; KS; Garden City; Finney County; Pierceville'], ['87777', 'zip', '67867; KS; Gray County; Montezuma'], ['87776', 'zip', '67865; KS; Clark County; Minneola'], ['87775', 'zip', '67864; KS; Meade County; Meade'], ['87771', 'zip', '67860; KS; Garden City; Kearny County; Lakin'], ['87770', 'zip', '67859; KS; Liberal; Seward County; Kismet'], ['8777', 'city', 'Omar; WV; Logan; Logan County'], ['87769', 'zip', '67857; KS; Hamilton County; Kendall'], ['87766', 'zip', '67853; KS; Gray County; Ingalls'], ['87765', 'zip', '67851; KS; Garden City; Finney County; Holcomb'], ['87762', 'zip', '67846; KS; Garden City; Finney County'], ['87761', 'zip', '67844; KS; Meade County; Fowler'], ['87759', 'zip', '67842; KS; Dodge City; Ford County; Ford'], ['87758', 'zip', '67841; KS; Gray County; Ensign'], ['87757', 'zip', '67840; KS; Clark County; Englewood'], ['87755', 'zip', '67838; KS; Garden City; Kearny County; Deerfield'], ['87754', 'zip', '67837; KS; Gray County; Copeland'], ['87752', 'zip', '67835; KS; Gray County; Cimarron'], ['87751', 'zip', '67834; KS; Dodge City; Ford County; Bucklin'], ['87750', 'zip', '67831; KS; Clark County; Ashland'], ['87749', 'zip', '67801; KS; Dodge City; Ford County'], ['87748', 'zip', '67764; KS; Logan County; Winona'], ['87742', 'zip', '67753; KS; Thomas County; Rexford'], ['87738', 'zip', '67748; KS; Logan County; Oakley'], ['87734', 'zip', '67743; KS; Thomas County; Levant'], ['87727', 'zip', '67735; KS; Sherman County; Goodland'], ['87726', 'zip', '67734; KS; Thomas County; Gem'], ['87725', 'zip', '67733; KS; Sherman County; Edson'], ['87724', 'zip', '67732; KS; Thomas County; Brewster'], ['87721', 'zip', '67701; KS; Thomas County; Colby'], ['87719', 'zip', '67674; KS; Hays; Ellis County; Walker'], ['87716', 'zip', '67671; KS; Hays; Ellis County; Victoria'], ['87714', 'zip', '67667; KS; Hays; Ellis County; Schoenchen'], ['87713', 'zip', '67665; KS; Russell County; Russell'], ['87711', 'zip', '67663; KS; Rooks County; Plainville'], ['8771', 'city', 'Monroeville; IN; Fort Wayne; Allen County'], ['87709', 'zip', '67660; KS; Hays; Ellis County; Pfeifer'], ['87702', 'zip', '67651; KS; Osborne County; Natoma'], ['87700', 'zip', '67649; KS; Russell County; Luray'], ['8770', 'city', 'Keezletown; VA; Harrisonburg; Rockingham County'], ['877', 'county', 'Bladen County; NC'], ['87699', 'zip', '67648; KS; Russell County; Lucas'], ['87692', 'zip', '67640; KS; Russell County; Gorham'], ['87690', 'zip', '67638; KS; Smith County; Gaylord'], ['87689', 'zip', '67637; KS; Hays; Ellis County; Ellis'], ['87682', 'zip', '67627; KS; Hays; Ellis County'], ['87676', 'zip', '67601; KS; Hays; Ellis County'], ['87673', 'zip', '67583; KS; Hutchinson; Reno County; Turon'], ['87672', 'zip', '67581; KS; Hutchinson; Reno County; Sylvia'], ['87671', 'zip', '67579; KS; Rice County; Sterling'], ['87670', 'zip', '67578; KS; Stafford County; Stafford'], ['8767', 'city', 'Harshaw; WI; Oneida County'], ['87669', 'zip', '67576; KS; Stafford County; St. John'], ['87668', 'zip', '67575; KS; Rush County; Rush Center'], ['87667', 'zip', '67574; KS; Pawnee County; Rozel'], ['87666', 'zip', '67573; KS; Rice County; Raymond'], ['87664', 'zip', '67570; KS; Hutchinson; Reno County; Pretty Prairie'], ['87663', 'zip', '67568; KS; Hutchinson; Reno County; Plevna'], ['87662', 'zip', '67567; KS; Great Bend; Barton County; Pawnee Rock'], ['87661', 'zip', '67566; KS; Hutchinson; Reno County; Partridge'], ['87660', 'zip', '67565; KS; Rush County; Otis'], ['8766', 'city', 'Harrisville; PA; Pittsburgh; Butler County'], ['87659', 'zip', '67564; KS; Great Bend; Barton County; Olmitz'], ['87658', 'zip', '67563; KS; Edwards County; Offerle'], ['87657', 'zip', '67561; KS; Hutchinson; Reno County; Nickerson'], ['87655', 'zip', '67559; KS; Rush County; Nekoma'], ['87654', 'zip', '67557; KS; Stafford County; Macksville'], ['87653', 'zip', '67556; KS; Rush County; McCracken'], ['87652', 'zip', '67554; KS; Rice County; Lyons'], ['87651', 'zip', '67553; KS; Rush County; Liebenthal'], ['8765', 'city', 'Harrisville; NH; Keene; Cheshire County'], ['87649', 'zip', '67550; KS; Pawnee County; Larned'], ['87648', 'zip', '67548; KS; Rush County; La Crosse'], ['87646', 'zip', '67546; KS; McPherson; McPherson County; Inman'], ['87645', 'zip', '67545; KS; Stafford County; Hudson'], ['87644', 'zip', '67544; KS; Great Bend; Barton County; Hoisington'], ['87643', 'zip', '67543; KS; Hutchinson; Reno County; Haven'], ['87642', 'zip', '67530; KS; Great Bend; Barton County'], ['87641', 'zip', '67529; KS; Pawnee County; Garfield'], ['87640', 'zip', '67526; KS; Great Bend; Barton County; Ellinwood'], ['8764', 'city', 'Harrison City; PA; Pittsburgh; Westmoreland County'], ['87639', 'zip', '67525; KS; Great Bend; Barton County; Claflin'], ['87638', 'zip', '67524; KS; Rice County; Chase'], ['87637', 'zip', '67523; KS; Pawnee County; Burdett'], ['87636', 'zip', '67522; KS; Hutchinson; Reno County; Buhler'], ['87634', 'zip', '67520; KS; Rush County; Bison'], ['87633', 'zip', '67519; KS; Edwards County; Belpre'], ['8763', 'city', 'Harrison; TN; Chattanooga; Hamilton County'], ['87629', 'zip', '67514; KS; Hutchinson; Reno County; Arlington'], ['87628', 'zip', '67513; KS; Rush County; Alexander'], ['87627', 'zip', '67512; KS; Rice County; Alden'], ['87626', 'zip', '67511; KS; Great Bend; Barton County; Albert'], ['87625', 'zip', '67510; KS; Hutchinson; Reno County; Abbyville'], ['87624', 'zip', '67505; KS; Hutchinson; Reno County; South Hutchinson'], ['87622', 'zip', '67502; KS; Hutchinson; Reno County'], ['87621', 'zip', '67501; KS; Hutchinson; Reno County'], ['87620', 'zip', '67492; KS; Dickinson County; Woodbine'], ['8762', 'city', 'Harrison; NJ; New York-Newark-Jersey City; Hudson County'], ['87619', 'zip', '67491; KS; McPherson; McPherson County; Windom'], ['87618', 'zip', '67490; KS; Ellsworth County; Wilson'], ['87617', 'zip', '67487; KS; Clay County; Wakefield'], ['87615', 'zip', '67484; KS; Salina; Ottawa County; Tescott'], ['87614', 'zip', '67483; KS; Marion County; Tampa'], ['87613', 'zip', '67482; KS; Dickinson County; Talmage'], ['87612', 'zip', '67481; KS; Lincoln County; Sylvan Grove'], ['87611', 'zip', '67480; KS; Dickinson County; Solomon'], ['87608', 'zip', '67475; KS; Marion County; Ramona'], ['87607', 'zip', '67474; KS; Osborne County; Portis'], ['87605', 'zip', '67470; KS; Salina; Saline County; New Cambria'], ['87604', 'zip', '67468; KS; Clay County; Morganville'], ['87603', 'zip', '67467; KS; Salina; Ottawa County; Minneapolis'], ['87601', 'zip', '67464; KS; McPherson; McPherson County; Marquette'], ['87600', 'zip', '67460; KS; McPherson; McPherson County'], ['8760', 'city', 'Fort Scott; KS; Bourbon County'], ['87599', 'zip', '67459; KS; Ellsworth County; Lorraine'], ['87598', 'zip', '67458; KS; Clay County; Longford'], ['87597', 'zip', '67457; KS; Rice County; Little River'], ['87596', 'zip', '67456; KS; McPherson; McPherson County; Lindsborg'], ['87594', 'zip', '67454; KS; Ellsworth County; Kanopolis'], ['87592', 'zip', '67451; KS; Dickinson County; Hope'], ['87591', 'zip', '67450; KS; Ellsworth County; Holyrood'], ['87590', 'zip', '67449; KS; Dickinson County; Herington'], ['8759', 'city', 'East Hanover Township; NJ; New York-Newark-Jersey City; Morris County'], ['87589', 'zip', '67448; KS; Salina; Saline County; Gypsum'], ['87588', 'zip', '67447; KS; Clay County; Green'], ['87585', 'zip', '67444; KS; Rice County; Geneseo'], ['87584', 'zip', '67443; KS; McPherson; McPherson County; Galva'], ['87583', 'zip', '67442; KS; Salina; Saline County; Falun'], ['87582', 'zip', '67441; KS; Dickinson County; Enterprise'], ['87581', 'zip', '67439; KS; Ellsworth County; Ellsworth'], ['87580', 'zip', '67438; KS; Marion County; Durham'], ['87579', 'zip', '67437; KS; Osborne County; Downs'], ['87578', 'zip', '67436; KS; Salina; Ottawa County; Delphos'], ['87577', 'zip', '67432; KS; Clay County; Clay Center'], ['87576', 'zip', '67431; KS; Dickinson County; Chapman'], ['87574', 'zip', '67428; KS; McPherson; McPherson County; Canton'], ['87573', 'zip', '67427; KS; Rice County; Bushton'], ['87572', 'zip', '67425; KS; Salina; Saline County; Brookville'], ['87570', 'zip', '67422; KS; Salina; Ottawa County; Bennington'], ['87566', 'zip', '67416; KS; Salina; Saline County; Assaria'], ['87565', 'zip', '67410; KS; Dickinson County; Abilene'], ['87563', 'zip', '67401; KS; Salina; Saline County'], ['87562', 'zip', '67364; KS; Coffeyville; Montgomery County; Tyro'], ['87561', 'zip', '67363; KS; Coffeyville; Montgomery County; Independence'], ['87558', 'zip', '67357; KS; Parsons; Labette County'], ['87557', 'zip', '67356; KS; Parsons; Labette County; Oswego'], ['87555', 'zip', '67354; KS; Parsons; Labette County; Mound Valley'], ['87552', 'zip', '67351; KS; Coffeyville; Montgomery County; Liberty'], ['87550', 'zip', '67347; KS; Coffeyville; Montgomery County; Havana'], ['87547', 'zip', '67344; KS; Coffeyville; Montgomery County; Elk City'], ['87546', 'zip', '67342; KS; Parsons; Labette County; Edna'], ['87545', 'zip', '67341; KS; Parsons; Labette County; Dennis'], ['87544', 'zip', '67340; KS; Coffeyville; Montgomery County; Dearing'], ['87543', 'zip', '67337; KS; Coffeyville; Montgomery County'], ['87542', 'zip', '67336; KS; Parsons; Labette County; Chetopa'], ['87541', 'zip', '67335; KS; Coffeyville; Montgomery County; Cherryvale'], ['8754', 'neigh', 'East Carver; MA; Boston-Cambridge-Newton; Plymouth County; Plymouth'], ['87539', 'zip', '67333; KS; Coffeyville; Montgomery County; Caney'], ['87538', 'zip', '67332; KS; Parsons; Labette County; Bartlett'], ['87537', 'zip', '67330; KS; Parsons; Labette County; Altamont'], ['87536', 'zip', '67301; KS; Coffeyville; Montgomery County; Independence'], ['8753', 'city', 'East Camden; AR; Camden; Ouachita County'], ['87525', 'zip', '67235; KS; Wichita; Sedgwick County'], ['87523', 'zip', '67232; KS; Wichita; Sedgwick County; Derby'], ['87521', 'zip', '67230; KS; Wichita; Sedgwick County'], ['87520', 'zip', '67228; KS; Wichita; Sedgwick County'], ['87519', 'zip', '67227; KS; Wichita; Sedgwick County; Clearwater'], ['87518', 'zip', '67226; KS; Wichita; Sedgwick County'], ['87517', 'zip', '67223; KS; Wichita; Sedgwick County'], ['87515', 'zip', '67220; KS; Wichita; Sedgwick County'], ['87514', 'zip', '67219; KS; Wichita; Sedgwick County; Park City'], ['87513', 'zip', '67218; KS; Wichita; Sedgwick County'], ['87512', 'zip', '67217'], ['87511', 'zip', '67216; KS; Wichita; Sedgwick County'], ['87510', 'zip', '67215; KS; Wichita; Sedgwick County'], ['8751', 'city', 'Eagleville; MO; Harrison County'], ['87509', 'zip', '67214; KS; Wichita; Sedgwick County'], ['87508', 'zip', '67213'], ['87507', 'zip', '67212'], ['87506', 'zip', '67211'], ['87505', 'zip', '67210; KS; Wichita; Sedgwick County'], ['87504', 'zip', '67209; KS; Wichita; Sedgwick County'], ['87503', 'zip', '67208; KS; Wichita; Sedgwick County'], ['87502', 'zip', '67207'], ['87501', 'zip', '67206; KS; Wichita; Sedgwick County'], ['87500', 'zip', '67205; KS; Wichita; Sedgwick County'], ['875', 'county', 'Bertie County; NC'], ['87499', 'zip', '67204; KS; Wichita; Sedgwick County'], ['87498', 'zip', '67203'], ['87497', 'zip', '67202; KS; Wichita; Sedgwick County'], ['87495', 'zip', '67159; KS; Wichita; Kingman County; Zenda'], ['87494', 'zip', '67156; KS; Arkansas City-Winfield; Cowley County; Winfield'], ['87492', 'zip', '67154; KS; Wichita; Butler County; Whitewater'], ['87491', 'zip', '67152; KS; Wichita; Sumner County; Wellington'], ['87490', 'zip', '67151; KS; Wichita; Harvey County; Walton'], ['8749', 'city', 'Eagle Bend; MN; Todd County'], ['87488', 'zip', '67149; KS; Wichita; Sedgwick County; Viola'], ['87487', 'zip', '67147; KS; Wichita; Sedgwick County; Valley Center'], ['87486', 'zip', '67146; KS; Arkansas City-Winfield; Cowley County; Udall'], ['87485', 'zip', '67144; KS; Wichita; Butler County; Towanda'], ['87483', 'zip', '67142; KS; Wichita; Kingman County; Spivey'], ['87482', 'zip', '67140; KS; Wichita; Sumner County; South Haven'], ['87480', 'zip', '67137; KS; Greenwood County; Severy'], ['87479', 'zip', '67135; KS; Wichita; Harvey County; Sedgwick'], ['87478', 'zip', '67134; KS; Pratt County; Sawyer'], ['87477', 'zip', '67133; KS; Wichita; Butler County; Rose Hill'], ['87476', 'zip', '67132; KS; Wichita; Butler County; Rosalia'], ['87475', 'zip', '67131; KS; Arkansas City-Winfield; Cowley County; Rock'], ['87473', 'zip', '67124; KS; Pratt County; Pratt'], ['87472', 'zip', '67123; KS; Wichita; Butler County; Potwin'], ['87471', 'zip', '67122; KS; Greenwood County; Piedmont'], ['87470', 'zip', '67120; KS; Wichita; Sumner County; Peck'], ['8747', 'city', 'Eagle; CO; Edwards; Eagle County'], ['87469', 'zip', '67119; KS; Wichita; Sumner County; Oxford'], ['87468', 'zip', '67118; KS; Wichita; Kingman County; Norwich'], ['87467', 'zip', '67117; KS; Wichita; Harvey County; North Newton'], ['87466', 'zip', '67114; KS; Wichita; Harvey County; Newton'], ['87465', 'zip', '67112; KS; Wichita; Kingman County; Nashville'], ['87464', 'zip', '67111; KS; Wichita; Kingman County; Murdock'], ['87463', 'zip', '67110; KS; Wichita; Sedgwick County; Mulvane'], ['87461', 'zip', '67108; KS; Wichita; Sedgwick County; Mount Hope'], ['87460', 'zip', '67107; KS; McPherson; McPherson County; Moundridge'], ['8746', 'city', 'Eads; CO; Kiowa County'], ['87459', 'zip', '67106; KS; Wichita; Sumner County; Milton'], ['87458', 'zip', '67105; KS; Wichita; Sumner County; Milan'], ['87456', 'zip', '67103; KS; Wichita; Sumner County; Mayfield'], ['87455', 'zip', '67102; KS; Arkansas City-Winfield; Cowley County; Dexter'], ['87454', 'zip', '67101; KS; Wichita; Sedgwick County; Maize'], ['87453', 'zip', '67074; KS; Wichita; Butler County; Leon'], ['87452', 'zip', '67073; KS; Marion County; Lehigh'], ['87451', 'zip', '67072; KS; Wichita; Butler County; Latham'], ['87448', 'zip', '67068; KS; Wichita; Kingman County; Kingman'], ['87447', 'zip', '67067; KS; Wichita; Sedgwick County; Kechi'], ['87446', 'zip', '67066; KS; Pratt County; Iuka'], ['87445', 'zip', '67065; KS; Barber County; Isabel'], ['87444', 'zip', '67063; KS; Marion County; Hillsboro'], ['87443', 'zip', '67062; KS; Wichita; Harvey County; Hesston'], ['87442', 'zip', '67061; KS; Barber County; Hazelton'], ['87441', 'zip', '67060; KS; Wichita; Sedgwick County; Haysville'], ['87440', 'zip', '67059; KS; Kiowa County; Haviland'], ['87439', 'zip', '67058; KS; Harper County; Harper'], ['87437', 'zip', '67056; KS; Wichita; Harvey County; Halstead'], ['87436', 'zip', '67055; KS; Wichita; Sedgwick County; Greenwich'], ['87434', 'zip', '67053; KS; Marion County; Goessel'], ['87433', 'zip', '67052; KS; Wichita; Sedgwick County; Goddard'], ['87432', 'zip', '67051; KS; Wichita; Sumner County; Geuda Springs'], ['87431', 'zip', '67050; KS; Wichita; Sedgwick County; Garden Plain'], ['87430', 'zip', '67049; KS; Harper County; Freeport'], ['87429', 'zip', '67047; KS; Greenwood County; Fall River'], ['87428', 'zip', '67045; KS; Greenwood County; Eureka'], ['87427', 'zip', '67042; KS; Wichita; Butler County; El Dorado'], ['87426', 'zip', '67041; KS; Wichita; Butler County; Elbing'], ['87425', 'zip', '67039; KS; Wichita; Butler County; Douglass'], ['87424', 'zip', '67038; KS; Arkansas City-Winfield; Cowley County; Dexter'], ['87423', 'zip', '67037; KS; Wichita; Sedgwick County; Derby'], ['87421', 'zip', '67035; KS; Wichita; Kingman County; Cunningham'], ['87420', 'zip', '67031; KS; Wichita; Sumner County; Conway Springs'], ['87419', 'zip', '67030; KS; Wichita; Sedgwick County; Colwich'], ['87417', 'zip', '67028; KS; Pratt County; Coats'], ['87416', 'zip', '67026; KS; Wichita; Sedgwick County; Clearwater'], ['87415', 'zip', '67025; KS; Wichita; Sedgwick County; Cheney'], ['87414', 'zip', '67024; KS; Chautauqua County; Cedar Vale'], ['87413', 'zip', '67023; KS; Arkansas City-Winfield; Cowley County; Cambridge'], ['87412', 'zip', '67022; KS; Wichita; Sumner County; Caldwell'], ['87411', 'zip', '67021; KS; Pratt County; Byers'], ['87410', 'zip', '67020; KS; Wichita; Harvey County; Burrton'], ['87409', 'zip', '67019; KS; Arkansas City-Winfield; Cowley County; Burden'], ['87407', 'zip', '67017; KS; Wichita; Butler County; Benton'], ['87406', 'zip', '67016; KS; Wichita; Sedgwick County; Bentley'], ['87405', 'zip', '67013; KS; Wichita; Sumner County; Belle Plaine'], ['87404', 'zip', '67012; KS; Wichita; Butler County; Beaumont'], ['87403', 'zip', '67010; KS; Wichita; Butler County; Augusta'], ['87402', 'zip', '67009; KS; Harper County; Attica'], ['87401', 'zip', '67008; KS; Arkansas City-Winfield; Cowley County; Atlanta'], ['87400', 'zip', '67005; KS; Arkansas City-Winfield; Cowley County; Arkansas City'], ['874', 'county', 'Bergen County; NJ; New York-Newark-Jersey City'], ['87399', 'zip', '67004; KS; Wichita; Sumner County; Argonia'], ['87398', 'zip', '67003; KS; Harper County; Anthony'], ['87397', 'zip', '67002; KS; Wichita; Butler County; Andover'], ['87396', 'zip', '67001; KS; Wichita; Sedgwick County; Andale'], ['87393', 'zip', '66967; KS; Smith County; Smith Center'], ['87389', 'zip', '66962; KS; Washington County; Palmer'], ['87381', 'zip', '66952; KS; Smith County; Lebanon'], ['87380', 'zip', '66951; KS; Smith County; Kensington'], ['87378', 'zip', '66948; KS; Cloud County; Jamestown'], ['87368', 'zip', '66937; KS; Washington County; Clifton'], ['87361', 'zip', '66873; KS; Morris County; Wilsey'], ['87360', 'zip', '66872; KS; Morris County; White City'], ['87359', 'zip', '66871; KS; Coffey County; Waverly'], ['87358', 'zip', '66870; KS; Greenwood County; Virgil'], ['87357', 'zip', '66869; KS; Chase County; Strong City'], ['87356', 'zip', '66868; KS; Emporia; Lyon County; Reading'], ['87355', 'zip', '66866; KS; Marion County; Peabody'], ['87354', 'zip', '66865; KS; Emporia; Lyon County; Olpe'], ['87353', 'zip', '66864; KS; Emporia; Lyon County; Neosho Rapids'], ['87352', 'zip', '66863; KS; Greenwood County; Neal'], ['87351', 'zip', '66862; KS; Chase County; Matfield Green'], ['87350', 'zip', '66861; KS; Marion County; Marion'], ['8735', 'city', 'Hardeeville; SC; Hilton Head Island-Bluffton-Beaufort; Jasper County'], ['87349', 'zip', '66860; KS; Greenwood County; Madison'], ['87348', 'zip', '66859; KS; Marion County; Lost Springs'], ['87347', 'zip', '66858; KS; Marion County; Lincolnville'], ['87346', 'zip', '66857; KS; Coffey County; Le Roy'], ['87345', 'zip', '66856; KS; Coffey County; Lebo'], ['87344', 'zip', '66855; KS; Emporia; Lyon County; Hartford'], ['87343', 'zip', '66854; KS; Emporia; Lyon County; Hartford'], ['87342', 'zip', '66853; KS; Greenwood County; Hamilton'], ['87341', 'zip', '66852; KS; Coffey County; Gridley'], ['87340', 'zip', '66851; KS; Marion County; Florence'], ['87339', 'zip', '66850; KS; Chase County; Elmdale'], ['87338', 'zip', '66849; KS; Morris County; Dwight'], ['87337', 'zip', '66846; KS; Morris County; Council Grove'], ['87336', 'zip', '66845; KS; Chase County; Cottonwood Falls'], ['87335', 'zip', '66843; KS; Chase County; Cedar Point'], ['87334', 'zip', '66842; KS; Wichita; Butler County; Cassoday'], ['87333', 'zip', '66840; KS; Marion County; Burns'], ['87332', 'zip', '66839; KS; Coffey County; Burlington'], ['87331', 'zip', '66838; KS; Morris County; Burdick'], ['87330', 'zip', '66835; KS; Emporia; Lyon County; Americus'], ['87329', 'zip', '66834; KS; Topeka; Wabaunsee County; Alta Vista'], ['87328', 'zip', '66833; KS; Emporia; Lyon County; Allen'], ['87327', 'zip', '66830; KS; Emporia; Lyon County; Admire'], ['87326', 'zip', '66801; KS; Emporia; Lyon County'], ['87325', 'zip', '66783; KS; Woodson County; Yates Center'], ['87324', 'zip', '66782; KS; Cherokee County; Scammon'], ['87323', 'zip', '66781; KS; Cherokee County; Weir'], ['87322', 'zip', '66780; KS; Pittsburg; Crawford County; Walnut'], ['87321', 'zip', '66779; KS; Bourbon County; Uniontown'], ['8732', 'city', 'Fort Klamath; OR; Klamath Falls; Klamath County'], ['87319', 'zip', '66777; KS; Woodson County; Toronto'], ['87318', 'zip', '66776; KS; Neosho County; Thayer'], ['87317', 'zip', '66775; KS; Neosho County; Stark'], ['87316', 'zip', '66773; KS; Cherokee County; Scammon'], ['87315', 'zip', '66772; KS; Allen County; Savonburg'], ['87314', 'zip', '66771; KS; Neosho County; Saint Paul'], ['87313', 'zip', '66770; KS; Cherokee County; Riverton'], ['87312', 'zip', '66769; KS; Bourbon County; Redfield'], ['87311', 'zip', '66767; KS; Kansas City; Linn County; Prescott'], ['87310', 'zip', '66763; KS; Pittsburg; Crawford County; Frontenac'], ['8731', 'neigh', 'Fort Hamilton; NY; New York-Newark-Jersey City; Kings County; New York'], ['87309', 'zip', '66762; KS; Pittsburg; Crawford County'], ['87308', 'zip', '66761; KS; Woodson County; Piqua'], ['87307', 'zip', '66760; KS; Pittsburg; Crawford County; Opolis'], ['87305', 'zip', '66758; KS; Woodson County; Neosho Falls'], ['87304', 'zip', '66757; KS; Wilson County; Neodesha'], ['87303', 'zip', '66756; KS; Pittsburg; Crawford County; Mulberry'], ['87302', 'zip', '66755; KS; Allen County; Moran'], ['87301', 'zip', '66754; KS; Bourbon County; Mapleton'], ['87300', 'zip', '66753; KS; Pittsburg; Crawford County; McCune'], ['873', 'county', 'Benzie County; MI; Traverse City'], ['87299', 'zip', '66751; KS; Allen County; La Harpe'], ['87298', 'zip', '66749; KS; Allen County; Iola'], ['87297', 'zip', '66748; KS; Allen County; Humboldt'], ['87296', 'zip', '66746; KS; Pittsburg; Crawford County; Hepler'], ['87295', 'zip', '66743; KS; Pittsburg; Crawford County; Girard'], ['87294', 'zip', '66742; KS; Allen County; Gas'], ['87293', 'zip', '66741; KS; Bourbon County; Garland'], ['87292', 'zip', '66740; KS; Neosho County; Galesburg'], ['87291', 'zip', '66739; KS; Cherokee County; Galena'], ['87290', 'zip', '66738; KS; Bourbon County; Fulton'], ['87289', 'zip', '66736; KS; Wilson County; Fredonia'], ['87288', 'zip', '66735; KS; Pittsburg; Crawford County; Franklin'], ['87287', 'zip', '66734; KS; Pittsburg; Crawford County; Farlington'], ['87286', 'zip', '66733; KS; Neosho County; Erie'], ['87285', 'zip', '66732; KS; Allen County; Elsmore'], ['87284', 'zip', '66728; KS; Cherokee County; Galena'], ['87283', 'zip', '66725; KS; Cherokee County; Columbus'], ['87282', 'zip', '66724; KS; Pittsburg; Crawford County; Cherokee'], ['87281', 'zip', '66720; KS; Neosho County; Chanute'], ['87280', 'zip', '66717; KS; Wilson County; Buffalo'], ['8728', 'city', 'Duquesne; MO; Joplin; Jasper County'], ['87279', 'zip', '66716; KS; Bourbon County; Bronson'], ['87278', 'zip', '66714; KS; Wilson County; Benedict'], ['87277', 'zip', '66713; KS; Cherokee County; Baxter Springs'], ['87276', 'zip', '66712; KS; Pittsburg; Crawford County; Arma'], ['87275', 'zip', '66711; KS; Pittsburg; Crawford County; Arcadia'], ['87274', 'zip', '66710; KS; Wilson County; Altoona'], ['87273', 'zip', '66701; KS; Bourbon County; Fort Scott'], ['87249', 'zip', '66619; KS; Topeka; Shawnee County'], ['87248', 'zip', '66618; KS; Topeka; Shawnee County'], ['87247', 'zip', '66617; KS; Topeka; Shawnee County'], ['87246', 'zip', '66616; KS; Topeka; Shawnee County'], ['87245', 'zip', '66615; KS; Topeka; Shawnee County'], ['87244', 'zip', '66614; KS; Topeka; Shawnee County'], ['87243', 'zip', '66612; KS; Topeka; Shawnee County'], ['87242', 'zip', '66611; KS; Topeka; Shawnee County'], ['87241', 'zip', '66610; KS; Topeka; Shawnee County'], ['87240', 'zip', '66609; KS; Topeka; Shawnee County'], ['8724', 'city', 'Johnson; AR; Fayetteville-Springdale-Rogers; Washington County'], ['87239', 'zip', '66608; KS; Topeka; Shawnee County'], ['87238', 'zip', '66607; KS; Topeka; Shawnee County'], ['87237', 'zip', '66606; KS; Topeka; Shawnee County'], ['87236', 'zip', '66605; KS; Topeka; Shawnee County'], ['87235', 'zip', '66604; KS; Topeka; Shawnee County'], ['87234', 'zip', '66603; KS; Topeka; Shawnee County'], ['87232', 'zip', '66554; KS; Manhattan; Riley County; Randolph'], ['87231', 'zip', '66552; KS; Topeka; Jackson County; Whiting'], ['87230', 'zip', '66550; KS; Nemaha County; Wetmore'], ['87229', 'zip', '66549; KS; Manhattan; Pottawatomie County; Westmoreland'], ['87228', 'zip', '66548; KS; Marshall County; Waterville'], ['87227', 'zip', '66547; KS; Manhattan; Pottawatomie County; Wamego'], ['87226', 'zip', '66546; KS; Topeka; Shawnee County; Wakarusa'], ['87225', 'zip', '66544; KS; Marshall County; Vermillion'], ['87224', 'zip', '66543; KS; Topeka; Osage County; Vassar'], ['87223', 'zip', '66542; KS; Topeka; Shawnee County; Tecumseh'], ['87222', 'zip', '66541; KS; Marshall County; Summerfield'], ['87220', 'zip', '66539; KS; Topeka; Shawnee County; Silver Lake'], ['87218', 'zip', '66537; KS; Topeka; Osage County; Scranton'], ['87217', 'zip', '66536; KS; Manhattan; Pottawatomie County; Saint Marys'], ['87216', 'zip', '66535; KS; Manhattan; Pottawatomie County; Saint George'], ['87215', 'zip', '66534; KS; Nemaha County; Sabetha'], ['87214', 'zip', '66533; KS; Topeka; Shawnee County; Rossville'], ['87213', 'zip', '66532; KS; Brown County; Robinson'], ['87212', 'zip', '66531; KS; Manhattan; Riley County; Riley'], ['87211', 'zip', '66528; KS; Topeka; Osage County; Quenemo'], ['87210', 'zip', '66527; KS; Brown County; Powhattan'], ['8721', 'city', 'Jewell Ridge; VA; Buchanan County'], ['87209', 'zip', '66526; KS; Topeka; Wabaunsee County; Paxico'], ['87208', 'zip', '66524; KS; Topeka; Osage County; Overbrook'], ['87207', 'zip', '66523; KS; Topeka; Osage County; Osage City'], ['87205', 'zip', '66521; KS; Manhattan; Pottawatomie County; Onaga'], ['87204', 'zip', '66520; KS; Manhattan; Pottawatomie County; Olsburg'], ['87202', 'zip', '66517; KS; Manhattan; Riley County; Ogden'], ['87200', 'zip', '66515; KS; Brown County; Morrill'], ['8720', 'city', 'Jewell; IA; Hamilton County'], ['872', 'county', 'Benton County; WA; Kennewick-Richland'], ['87199', 'zip', '66514; KS; Junction City; Geary County; Milford'], ['87198', 'zip', '66512; KS; Topeka; Jefferson County; Meriden'], ['87197', 'zip', '66510; KS; Topeka; Osage County; Melvern'], ['87195', 'zip', '66508; KS; Marshall County; Marysville'], ['87194', 'zip', '66507; KS; Topeka; Wabaunsee County; Maple Hill'], ['87191', 'zip', '66503; KS; Manhattan; Riley County'], ['87190', 'zip', '66502; KS; Manhattan; Riley County'], ['8719', 'city', 'Jenkinsburg; GA; Atlanta-Sandy Springs-Roswell; Butts County'], ['87189', 'zip', '66501; KS; Topeka; Wabaunsee County; McFarland'], ['87188', 'zip', '66451; KS; Topeka; Osage County; Lyndon'], ['87187', 'zip', '66449; KS; Manhattan; Riley County; Leonardville'], ['87185', 'zip', '66441; KS; Junction City; Geary County'], ['87183', 'zip', '66439; KS; Brown County; Horton'], ['87182', 'zip', '66438; KS; Marshall County; Home'], ['87181', 'zip', '66436; KS; Topeka; Jackson County; Holton'], ['87180', 'zip', '66434; KS; Brown County; Hiawatha'], ['8718', 'city', 'Handley; WV; Charleston; Kanawha County'], ['87179', 'zip', '66432; KS; Manhattan; Pottawatomie County; Havensville'], ['87178', 'zip', '66431; KS; Topeka; Wabaunsee County; Harveyville'], ['87177', 'zip', '66429; KS; Topeka; Jefferson County; Grantville'], ['87176', 'zip', '66428; KS; Nemaha County; Goff'], ['87175', 'zip', '66427; KS; Marshall County; Frankfort'], ['87173', 'zip', '66425; KS; Brown County; Fairview'], ['87172', 'zip', '66424; KS; Brown County; Everest'], ['87171', 'zip', '66423; KS; Topeka; Wabaunsee County; Eskridge'], ['87170', 'zip', '66422; KS; Manhattan; Pottawatomie County; Emmett'], ['8717', 'city', 'Hamilton; MS; Monroe County'], ['87168', 'zip', '66419; KS; Topeka; Jackson County; Denison'], ['87166', 'zip', '66417; KS; Nemaha County; Corning'], ['87164', 'zip', '66415; KS; Nemaha County; Centralia'], ['87163', 'zip', '66414; KS; Topeka; Osage County; Carbondale'], ['87162', 'zip', '66413; KS; Topeka; Osage County; Burlingame'], ['87160', 'zip', '66411; KS; Marshall County; Blue Rapids'], ['8716', 'city', 'Hamer; ID; Idaho Falls; Jefferson County'], ['87159', 'zip', '66409; KS; Topeka; Shawnee County; Berryton'], ['87157', 'zip', '66407; KS; Manhattan; Pottawatomie County; Belvue'], ['87156', 'zip', '66406; KS; Marshall County; Beattie'], ['87154', 'zip', '66403; KS; Marshall County; Axtell'], ['87153', 'zip', '66402; KS; Topeka; Shawnee County; Auburn'], ['87152', 'zip', '66401; KS; Topeka; Wabaunsee County; Alma'], ['8715', 'city', 'Forest City; IA; Winnebago County'], ['87143', 'zip', '66227; KS; Kansas City; Johnson County; Lenexa'], ['87142', 'zip', '66226; KS; Kansas City; Johnson County; Shawnee'], ['87140', 'zip', '66224; KS; Kansas City; Johnson County; Leawood'], ['8714', 'city', 'Foreman; AR; Texarkana; Little River County'], ['87139', 'zip', '66223'], ['87137', 'zip', '66221; KS; Kansas City; Johnson County; Overland Park'], ['87136', 'zip', '66220; KS; Kansas City; Johnson County; Lenexa'], ['87135', 'zip', '66219; KS; Kansas City; Johnson County; Lenexa'], ['87134', 'zip', '66218; KS; Kansas City; Johnson County; Shawnee'], ['87133', 'zip', '66217; KS; Kansas City; Johnson County; Shawnee'], ['87132', 'zip', '66216; KS; Kansas City; Johnson County; Shawnee'], ['87131', 'zip', '66215; KS; Kansas City; Johnson County; Lenexa'], ['87130', 'zip', '66214; KS; Kansas City; Johnson County; Overland Park'], ['8713', 'city', 'Flowery Branch; GA; Gainesville; Hall County'], ['87129', 'zip', '66213; KS; Kansas City; Johnson County; Overland Park'], ['87128', 'zip', '66212; KS; Kansas City; Johnson County; Overland Park'], ['87127', 'zip', '66211; KS; Kansas City; Johnson County; Leawood'], ['87126', 'zip', '66210; KS; Kansas City; Johnson County; Overland Park'], ['87125', 'zip', '66209; KS; Kansas City; Johnson County; Leawood'], ['87124', 'zip', '66208'], ['87123', 'zip', '66207; KS; Kansas City; Johnson County; Overland Park'], ['87122', 'zip', '66206; KS; Kansas City; Johnson County; Leawood'], ['87121', 'zip', '66205'], ['87120', 'zip', '66204; KS; Kansas City; Johnson County; Overland Park'], ['8712', 'city', 'Flordell Hills; MO; St. Louis; Saint Louis County'], ['87119', 'zip', '66203; KS; Kansas City; Johnson County; Shawnee'], ['87118', 'zip', '66202; KS; Kansas City; Johnson County; Mission'], ['87110', 'zip', '66112; KS; Kansas City; Wyandotte County'], ['87109', 'zip', '66111; KS; Kansas City; Wyandotte County'], ['87107', 'zip', '66109; KS; Kansas City; Wyandotte County'], ['87106', 'zip', '66106; KS; Kansas City; Wyandotte County'], ['87105', 'zip', '66105; KS; Kansas City; Wyandotte County'], ['87104', 'zip', '66104; KS; Kansas City; Wyandotte County'], ['87103', 'zip', '66103; KS; Kansas City; Wyandotte County'], ['87102', 'zip', '66102; KS; Kansas City; Wyandotte County'], ['87101', 'zip', '66101; KS; Kansas City; Wyandotte County'], ['87100', 'zip', '66097; KS; Topeka; Jefferson County; Winchester'], ['8710', 'city', 'Duffield; VA; Kingsport-Bristol-Bristol; Scott County'], ['871', 'county', 'Benton County; TN'], ['87099', 'zip', '66095; KS; Ottawa; Franklin County; Williamsburg'], ['87098', 'zip', '66094; KS; St. Joseph; Doniphan County; White Cloud'], ['87097', 'zip', '66093; KS; Anderson County; Westphalia'], ['87096', 'zip', '66092; KS; Ottawa; Franklin County; Wellsville'], ['87095', 'zip', '66091; KS; Anderson County; Welda'], ['87094', 'zip', '66090; KS; St. Joseph; Doniphan County; Wathena'], ['87093', 'zip', '66088; KS; Topeka; Jefferson County; Valley Falls'], ['87092', 'zip', '66087; KS; St. Joseph; Doniphan County; Troy'], ['87091', 'zip', '66086; KS; Kansas City; Leavenworth County; Tonganoxie'], ['87090', 'zip', '66085; KS; Kansas City; Johnson County; Overland Park'], ['87089', 'zip', '66083; KS; Kansas City; Johnson County; Spring Hill'], ['87088', 'zip', '66080; KS; Ottawa; Franklin County; Richmond'], ['87087', 'zip', '66079; KS; Ottawa; Franklin County; Rantoul'], ['87086', 'zip', '66078; KS; Ottawa; Franklin County; Princeton'], ['87084', 'zip', '66076; KS; Ottawa; Franklin County; Pomona'], ['87083', 'zip', '66075; KS; Kansas City; Linn County; Pleasanton'], ['87082', 'zip', '66073; KS; Topeka; Jefferson County; Perry'], ['87081', 'zip', '66072; KS; Kansas City; Linn County; Parker'], ['87080', 'zip', '66071; KS; Kansas City; Miami County; Paola'], ['8708', 'city', 'Dubois; IN; Jasper; Dubois County'], ['87079', 'zip', '66070; KS; Topeka; Jefferson County; Ozawkie'], ['87078', 'zip', '66067; KS; Ottawa; Franklin County'], ['87077', 'zip', '66066; KS; Topeka; Jefferson County; Oskaloosa'], ['87076', 'zip', '66064; KS; Kansas City; Miami County; Osawatomie'], ['87074', 'zip', '66062'], ['87073', 'zip', '66061'], ['87072', 'zip', '66060; KS; Topeka; Jefferson County; Nortonville'], ['87071', 'zip', '66058; KS; Atchison; Atchison County; Muscotah'], ['87070', 'zip', '66056; KS; Kansas City; Linn County; Mound City'], ['8707', 'city', 'Dry Branch; GA; Macon-Bibb County; Twiggs County'], ['87069', 'zip', '66054; KS; Topeka; Jefferson County; McLouth'], ['87068', 'zip', '66053; KS; Kansas City; Miami County; Louisburg'], ['87067', 'zip', '66052; KS; Kansas City; Leavenworth County; Linwood'], ['87065', 'zip', '66050; KS; Lawrence; Douglas County; Lecompton'], ['87064', 'zip', '66049; KS; Lawrence; Douglas County'], ['87063', 'zip', '66048; KS; Kansas City; Leavenworth County; Leavenworth'], ['87062', 'zip', '66047; KS; Lawrence; Douglas County'], ['87061', 'zip', '66046; KS; Lawrence; Douglas County'], ['8706', 'city', 'Drexel; MO; Kansas City; Cass County'], ['87059', 'zip', '66044; KS; Lawrence; Douglas County'], ['87058', 'zip', '66043; KS; Kansas City; Leavenworth County; Lansing'], ['87057', 'zip', '66042; KS; Ottawa; Franklin County; Lane'], ['87056', 'zip', '66041; KS; Atchison; Atchison County; Lancaster'], ['87055', 'zip', '66040; KS; Kansas City; Linn County; Linn Valley'], ['87054', 'zip', '66039; KS; Anderson County; Kincaid'], ['87053', 'zip', '66036; KS; Kansas City; Miami County; Paola'], ['87052', 'zip', '66035; KS; St. Joseph; Doniphan County; Highland'], ['87051', 'zip', '66033; KS; Anderson County; Greeley'], ['87050', 'zip', '66032; KS; Anderson County; Garnett'], ['8705', 'city', 'Drewryville; VA; Southampton County'], ['87048', 'zip', '66030; KS; Kansas City; Johnson County; Gardner'], ['87046', 'zip', '66026; KS; Kansas City; Miami County; Fontana'], ['87045', 'zip', '66025; KS; Lawrence; Douglas County; Eudora'], ['87044', 'zip', '66024; KS; St. Joseph; Doniphan County; Wathena'], ['87043', 'zip', '66023; KS; Atchison; Atchison County; Effingham'], ['87042', 'zip', '66021; KS; Kansas City; Johnson County; Edgerton'], ['87041', 'zip', '66020; KS; Kansas City; Leavenworth County; Easton'], ['8704', 'city', 'Drayden; MD; California-Lexington Park; Saint Marys County'], ['87039', 'zip', '66018; KS; Kansas City; Johnson County; De Soto'], ['87038', 'zip', '66017; KS; St. Joseph; Doniphan County; Denton'], ['87037', 'zip', '66016; KS; Atchison; Atchison County; Cummings'], ['87036', 'zip', '66015; KS; Anderson County; Colony'], ['87035', 'zip', '66014; KS; Kansas City; Linn County; Centerville'], ['87034', 'zip', '66013; KS; Kansas City; Miami County; Bucyrus'], ['87033', 'zip', '66012; KS; Kansas City; Wyandotte County; Bonner Springs'], ['87032', 'zip', '66010; KS; Kansas City; Linn County; Blue Mound'], ['87031', 'zip', '66008; KS; St. Joseph; Doniphan County; Bendena'], ['87030', 'zip', '66007; KS; Kansas City; Leavenworth County; Basehor'], ['87029', 'zip', '66006; KS; Lawrence; Douglas County; Baldwin City'], ['87028', 'zip', '66002; KS; Atchison; Atchison County'], ['87022', 'zip', '65810; MO; Springfield; Greene County'], ['87021', 'zip', '65809; MO; Springfield; Greene County'], ['8702', 'city', 'Jeanerette; LA; Morgan City; Saint Mary Parish'], ['87019', 'zip', '65807; MO; Springfield; Greene County'], ['87018', 'zip', '65806; MO; Springfield; Greene County'], ['87016', 'zip', '65804; MO; Springfield; Greene County'], ['87015', 'zip', '65803; MO; Springfield; Greene County'], ['87014', 'zip', '65802; MO; Springfield; Greene County'], ['87012', 'zip', '65793; MO; West Plains; Howell County; Willow Springs'], ['87011', 'zip', '65791; MO; Oregon County; Thayer'], ['87010', 'zip', '65790; MO; Ozark County; Pottersville'], ['87009', 'zip', '65789; MO; West Plains; Howell County; Pomona'], ['87008', 'zip', '65788; MO; West Plains; Howell County; Peace Valley'], ['87007', 'zip', '65787; MO; Camden County; Roach'], ['87006', 'zip', '65786; MO; Camden County; Macks Creek'], ['87005', 'zip', '65785; MO; Cedar County; Stockton'], ['87004', 'zip', '65784; MO; Ozark County; Zanoni'], ['87003', 'zip', '65783; MO; Springfield; Dallas County; Windyville'], ['87002', 'zip', '65781; MO; Springfield; Greene County; Willard'], ['87001', 'zip', '65779; MO; Hickory County; Flemington'], ['87000', 'zip', '65778; MO; Oregon County; Myrtle'], ['870', 'county', 'Benton County; OR; Corvallis'], ['87', 'county', 'Lee County; SC'], ['86999', 'zip', '65777; MO; West Plains; Howell County; Moody'], ['86997', 'zip', '65775; MO; West Plains; Howell County'], ['86996', 'zip', '65774; MO; Hickory County; Weaubleau'], ['86995', 'zip', '65773; MO; Ozark County; Wasola'], ['86994', 'zip', '65772; MO; Barry County; Washburn'], ['86993', 'zip', '65771; MO; Branson; Taney County'], ['86992', 'zip', '65770; MO; Springfield; Greene County; Walnut Grove'], ['86991', 'zip', '65769; MO; Lawrence County; Verona'], ['86989', 'zip', '65767; MO; Springfield; Dallas County; Urbana'], ['86988', 'zip', '65766; MO; Ozark County; Udall'], ['86986', 'zip', '65764; MO; Camden County; Macks Creek'], ['86985', 'zip', '65762; MO; Ozark County; Thornfield'], ['86984', 'zip', '65761; MO; Ozark County; Sundown'], ['86983', 'zip', '65760; MO; Ozark County; Tecumseh'], ['86982', 'zip', '65759; MO; Branson; Taney County; Taneyville'], ['86981', 'zip', '65757; MO; Springfield; Greene County; Strafford'], ['86980', 'zip', '65756; MO; Lawrence County; Stotts City'], ['86979', 'zip', '65755; MO; Ozark County; Squires'], ['86978', 'zip', '65754; MO; Springfield; Christian County; Spokane'], ['86977', 'zip', '65753; MO; Springfield; Christian County; Sparta'], ['86976', 'zip', '65752; MO; Dade County; Everton'], ['86975', 'zip', '65747; MO; Branson; Stone County; Shell Knob'], ['86974', 'zip', '65746; MO; Springfield; Webster County; Seymour'], ['86973', 'zip', '65745; MO; Barry County; Seligman'], ['86972', 'zip', '65744; MO; Branson; Taney County; Rueter'], ['86971', 'zip', '65742; MO; Springfield; Greene County; Rogersville'], ['86969', 'zip', '65740; MO; Branson; Taney County; Merriam Woods Village'], ['86968', 'zip', '65739; MO; Branson; Taney County; Ridgedale'], ['86967', 'zip', '65738; MO; Springfield; Greene County; Republic'], ['86966', 'zip', '65737; MO; Branson; Stone County; Reeds Spring'], ['86965', 'zip', '65735; MO; Hickory County; Wheatland'], ['86964', 'zip', '65734; MO; Barry County; Purdy'], ['86963', 'zip', '65733; MO; Branson; Taney County; Protem'], ['86962', 'zip', '65732; MO; Hickory County; Preston'], ['86961', 'zip', '65731; MO; Branson; Taney County; Powersite'], ['86960', 'zip', '65730; MO; Fayetteville-Springdale-Rogers; McDonald County; Powell'], ['86959', 'zip', '65729; MO; Ozark County; Pontiac'], ['86958', 'zip', '65728; MO; Springfield; Christian County; Highlandville'], ['86957', 'zip', '65727; MO; Springfield; Polk County; Bolivar'], ['86955', 'zip', '65725; MO; Springfield; Polk County; Pleasant Hope'], ['86954', 'zip', '65724; MO; Springfield; Polk County; Bolivar'], ['86953', 'zip', '65723; MO; Lawrence County; Pierce City'], ['86952', 'zip', '65722; MO; Lebanon; Laclede County; Phillipsburg'], ['86951', 'zip', '65721; MO; Springfield; Christian County; Ozark'], ['86950', 'zip', '65720; MO; Springfield; Christian County; Oldfield'], ['8695', 'city', 'Jarales; NM; Albuquerque; Valencia County'], ['86949', 'zip', '65717; MO; Wright County; Norwood'], ['86948', 'zip', '65715; MO; Ozark County; Noble'], ['86947', 'zip', '65714; MO; Springfield; Christian County; Nixa'], ['86946', 'zip', '65713; MO; Springfield; Webster County; Niangua'], ['86945', 'zip', '65712; MO; Lawrence County; Mount Vernon'], ['86944', 'zip', '65711; MO; Wright County; Mountain Grove'], ['86943', 'zip', '65710; MO; Springfield; Polk County; Morrisville'], ['86942', 'zip', '65708; MO; Barry County; Monett'], ['86941', 'zip', '65707; MO; Lawrence County; Miller'], ['86940', 'zip', '65706; MO; Springfield; Webster County; Marshfield'], ['8694', 'city', 'Jamaica; IA; Des Moines-West Des Moines; Guthrie County'], ['86939', 'zip', '65705; MO; Lawrence County; Marionville'], ['86938', 'zip', '65704; MO; Wright County; Mansfield'], ['86937', 'zip', '65702; MO; Wright County; Macomb'], ['86935', 'zip', '65692; MO; Oregon County; Koshkonong'], ['86934', 'zip', '65690; MO; Oregon County; Couch'], ['86933', 'zip', '65689; MO; Texas County; Cabool'], ['86931', 'zip', '65686; MO; Branson; Stone County; Kimberling City'], ['86930', 'zip', '65685; MO; Springfield; Dallas County; Louisburg'], ['8693', 'city', 'Hall Summit; LA; Red River Parish'], ['86929', 'zip', '65682; MO; Dade County; Lockwood'], ['86928', 'zip', '65681; MO; Branson; Stone County; Lampe'], ['86927', 'zip', '65680; MO; Branson; Taney County; Kissee Mills'], ['86926', 'zip', '65679; MO; Branson; Taney County; Kirbyville'], ['86925', 'zip', '65676; MO; Ozark County; Isabella'], ['86924', 'zip', '65675; MO; Branson; Stone County; Hurley'], ['86923', 'zip', '65674; MO; Springfield; Polk County; Humansville'], ['86921', 'zip', '65672; MO; Branson; Taney County; Hollister'], ['86920', 'zip', '65669; MO; Springfield; Christian County; Highlandville'], ['86919', 'zip', '65668; MO; Hickory County; Hermitage'], ['86918', 'zip', '65667; MO; Wright County; Hartville'], ['86917', 'zip', '65666; MO; Ozark County; Gainesville'], ['86916', 'zip', '65664; MO; Springfield; Greene County; Ash Grove'], ['86915', 'zip', '65663; MO; Springfield; Polk County; Bolivar'], ['86914', 'zip', '65662; MO; Wright County; Hartville'], ['86913', 'zip', '65661; MO; Dade County; Greenfield'], ['86912', 'zip', '65660; MO; Wright County; Graff'], ['86911', 'zip', '65658; MO; Barry County; Golden'], ['86910', 'zip', '65657; MO; Springfield; Christian County; Garrison'], ['8691', 'city', 'Halifax; NC; Roanoke Rapids; Halifax County'], ['86909', 'zip', '65656; MO; Branson; Stone County; Galena'], ['86908', 'zip', '65655; MO; Ozark County; Gainesville'], ['86907', 'zip', '65654; MO; Lawrence County; Freistatt'], ['86906', 'zip', '65653; MO; Branson; Taney County; Forsyth'], ['86905', 'zip', '65652; MO; Springfield; Webster County; Fordland'], ['86904', 'zip', '65650; MO; Hickory County; Flemington'], ['86903', 'zip', '65649; MO; Springfield; Polk County; Fair Play'], ['86902', 'zip', '65648; MO; Springfield; Greene County; Fair Grove'], ['86901', 'zip', '65647; MO; Barry County; Exeter'], ['86900', 'zip', '65646; MO; Dade County; Everton'], ['8690', 'city', 'Hale; MO; Carroll County'], ['869', 'county', 'Benton County; MS; Memphis'], ['86898', 'zip', '65644; MO; Springfield; Dallas County; Elkland'], ['86897', 'zip', '65641; MO; Barry County; Chain-O-Lakes'], ['86896', 'zip', '65640; MO; Springfield; Polk County; Dunnegan'], ['86895', 'zip', '65638; MO; Ozark County; Drury'], ['86894', 'zip', '65637; MO; Ozark County; Dora'], ['86892', 'zip', '65635; MO; Cedar County; Stockton'], ['86891', 'zip', '65634; MO; Hickory County; Cross Timbers'], ['86890', 'zip', '65633; MO; Branson; Stone County; Crane'], ['86889', 'zip', '65632; MO; Lebanon; Laclede County; Conway'], ['86888', 'zip', '65631; MO; Springfield; Christian County; Clever'], ['86887', 'zip', '65630; MO; Springfield; Christian County; Saddlebrooke'], ['86886', 'zip', '65629; MO; Springfield; Christian County; Chadwick'], ['86885', 'zip', '65627; MO; Branson; Taney County; Cedarcreek'], ['86884', 'zip', '65626; MO; Ozark County; Bakersfield'], ['86883', 'zip', '65625; MO; Barry County; Cassville'], ['86882', 'zip', '65624; MO; Branson; Stone County; Cape Fair'], ['86880', 'zip', '65622; MO; Springfield; Dallas County; Buffalo'], ['86879', 'zip', '65620; MO; Springfield; Christian County; Bruner'], ['86878', 'zip', '65619; MO; Springfield; Greene County; Battlefield'], ['86877', 'zip', '65618; MO; Ozark County; Brixey'], ['86876', 'zip', '65617; MO; Springfield; Polk County; Brighton'], ['86875', 'zip', '65616; MO; Branson; Taney County'], ['86873', 'zip', '65614; MO; Branson; Taney County; Bradleyville'], ['86872', 'zip', '65613; MO; Springfield; Polk County; Bolivar'], ['86871', 'zip', '65612; MO; Springfield; Greene County; Bois D Arc'], ['86870', 'zip', '65611; MO; Branson; Stone County; Blue Eye'], ['86869', 'zip', '65610; MO; Springfield; Christian County; Billings'], ['86868', 'zip', '65609; MO; Ozark County; Bakersfield'], ['86867', 'zip', '65608; MO; Douglas County; Ava'], ['86865', 'zip', '65606; MO; Oregon County; Alton'], ['86864', 'zip', '65605; MO; Lawrence County; Aurora'], ['86863', 'zip', '65604; MO; Springfield; Greene County; Ash Grove'], ['86862', 'zip', '65603; MO; Cedar County; Stockton'], ['86861', 'zip', '65601; MO; Springfield; Polk County; Aldrich'], ['86860', 'zip', '65591; MO; Camden County; Montreal'], ['86859', 'zip', '65590; MO; Springfield; Dallas County; Long Lane'], ['86858', 'zip', '65589; MO; Texas County; Yukon'], ['86857', 'zip', '65588; MO; Shannon County; Winona'], ['86855', 'zip', '65584; MO; Fort Leonard Wood; Pulaski County; Saint Robert'], ['86854', 'zip', '65583; MO; Fort Leonard Wood; Pulaski County; Waynesville'], ['86853', 'zip', '65582; MO; Maries County; Vienna'], ['86852', 'zip', '65580; MO; Maries County; Vichy'], ['86851', 'zip', '65571; MO; Texas County; Summersville'], ['86850', 'zip', '65570; MO; Texas County; Success'], ['8685', 'city', 'Fletcher; MO; St. Louis; Jefferson County'], ['86849', 'zip', '65567; MO; Camden County; Stoutland'], ['86848', 'zip', '65566; MO; Iron County; Viburnum'], ['86847', 'zip', '65565; MO; Crawford County; Steelville'], ['86846', 'zip', '65564; MO; Texas County; Solo'], ['86845', 'zip', '65560; MO; Dent County; Salem'], ['86844', 'zip', '65559; MO; Rolla; Phelps County; Saint James'], ['86843', 'zip', '65557; MO; Texas County; Roby'], ['86842', 'zip', '65556; MO; Fort Leonard Wood; Pulaski County; Richland'], ['86841', 'zip', '65555; MO; Texas County; Raymondville'], ['86840', 'zip', '65552; MO; Texas County; Plato'], ['8684', 'city', 'Flemington; WV; Clarksburg; Taylor County'], ['86839', 'zip', '65550; MO; Rolla; Phelps County; Newburg'], ['86838', 'zip', '65548; MO; West Plains; Howell County; Mountain View'], ['86836', 'zip', '65543; MO; Lebanon; Laclede County; Lynchburg'], ['86835', 'zip', '65542; MO; Texas County; Licking'], ['86834', 'zip', '65541; MO; Rolla; Phelps County; Edgar Springs'], ['86833', 'zip', '65536; MO; Lebanon; Laclede County'], ['86832', 'zip', '65535; MO; Crawford County; Leasburg'], ['86831', 'zip', '65534; MO; Fort Leonard Wood; Pulaski County; Laquey'], ['86830', 'zip', '65532; MO; Rolla; Phelps County'], ['8683', 'city', 'Flat Rock; IL; Crawford County'], ['86829', 'zip', '65529; MO; Rolla; Phelps County; Jerome'], ['86828', 'zip', '65501; MO; Dent County; Jadwin'], ['86827', 'zip', '65486; MO; Miller County; Iberia'], ['86826', 'zip', '65484; MO; Texas County; Huggins'], ['86825', 'zip', '65483; MO; Texas County; Houston'], ['86824', 'zip', '65479; MO; Shannon County; Hartshorn'], ['86823', 'zip', '65473; MO; Fort Leonard Wood; Pulaski County; Saint Robert'], ['86822', 'zip', '65470; MO; Lebanon; Laclede County; Falcon'], ['86821', 'zip', '65468; MO; Texas County; Eunice'], ['86820', 'zip', '65466; MO; Shannon County; Eminence'], ['8682', 'city', 'Five Points; CA; Fresno; Fresno County'], ['86819', 'zip', '65464; MO; Texas County; Elk Creek'], ['86818', 'zip', '65463; MO; Lebanon; Laclede County; Eldridge'], ['86817', 'zip', '65462; MO; Rolla; Phelps County; Edgar Springs'], ['86816', 'zip', '65461; MO; Fort Leonard Wood; Pulaski County; Duke'], ['86815', 'zip', '65459; MO; Fort Leonard Wood; Pulaski County; Dixon'], ['86814', 'zip', '65457; MO; Fort Leonard Wood; Pulaski County; Devils Elbow'], ['86813', 'zip', '65456; MO; Crawford County; Davisville'], ['86812', 'zip', '65453; MO; Crawford County; Cuba'], ['86811', 'zip', '65452; MO; Fort Leonard Wood; Pulaski County; Crocker'], ['86810', 'zip', '65449; MO; Crawford County; Cook Station'], ['8681', 'city', 'Firth; NE; Lincoln; Lancaster County'], ['86809', 'zip', '65446; MO; Crawford County; Cherryville'], ['86808', 'zip', '65444; MO; Texas County; Bucyrus'], ['86807', 'zip', '65443; MO; Maries County; Brinktown'], ['86806', 'zip', '65441; MO; Washington County; Sullivan'], ['86805', 'zip', '65440; MO; Crawford County; Davisville'], ['86804', 'zip', '65439; MO; Iron County; Viburnum'], ['86803', 'zip', '65438; MO; Shannon County; Birch Tree'], ['86802', 'zip', '65436; MO; Rolla; Phelps County; Beulah'], ['868', 'county', 'Benton County; MO'], ['86799', 'zip', '65401; MO; Rolla; Phelps County'], ['86798', 'zip', '65360; MO; Henry County; Windsor'], ['86797', 'zip', '65355; MO; Benton County; Warsaw'], ['86796', 'zip', '65354; MO; Morgan County; Syracuse'], ['86795', 'zip', '65351; MO; Marshall; Saline County; Sweet Springs'], ['86794', 'zip', '65350; MO; Sedalia; Pettis County; Smithton'], ['86793', 'zip', '65349; MO; Marshall; Saline County; Slater'], ['86792', 'zip', '65348; MO; Cooper County; Otterville'], ['86791', 'zip', '65347; MO; Marshall; Saline County; Nelson'], ['86790', 'zip', '65345; MO; Sedalia; Pettis County; Mora'], ['8679', 'city', 'Finksburg; MD; Baltimore-Columbia-Towson; Carroll County'], ['86788', 'zip', '65340; MO; Marshall; Saline County'], ['86787', 'zip', '65339; MO; Marshall; Saline County; Malta Bend'], ['86786', 'zip', '65338; MO; Benton County; Warsaw'], ['86785', 'zip', '65337; MO; Sedalia; Pettis County; La Monte'], ['86784', 'zip', '65336; MO; Warrensburg; Johnson County; Knob Noster'], ['86783', 'zip', '65335; MO; Benton County; Ionia'], ['86782', 'zip', '65334; MO; Sedalia; Pettis County; Hughesville'], ['86781', 'zip', '65333; MO; Sedalia; Pettis County; Houstonia'], ['86780', 'zip', '65332; MO; Sedalia; Pettis County; Green Ridge'], ['8678', 'city', 'Finger; TN; McNairy County'], ['86777', 'zip', '65327; MO; Kansas City; Lafayette County; Emma'], ['86776', 'zip', '65326; MO; Camden County; Edwards'], ['86775', 'zip', '65325; MO; Benton County; Cole Camp'], ['86774', 'zip', '65324; MO; Camden County; Climax Springs'], ['86773', 'zip', '65323; MO; Henry County; Calhoun'], ['86772', 'zip', '65322; MO; Cooper County; Blackwater'], ['86771', 'zip', '65321; MO; Marshall; Saline County; Blackburn'], ['86770', 'zip', '65320; MO; Cooper County; Blackwater'], ['8677', 'city', 'Ferrysburg; MI; Grand Rapids-Wyoming; Ottawa County'], ['86767', 'zip', '65301; MO; Sedalia; Pettis County'], ['86765', 'zip', '65287; MO; Cooper County; Wooldridge'], ['86763', 'zip', '65285; MO; Mexico; Audrain County; Thompson'], ['86762', 'zip', '65284; MO; Columbia; Boone County; Sturgeon'], ['86761', 'zip', '65283; MO; Monroe County; Stoutsville'], ['86760', 'zip', '65282; MO; Monroe County; Santa Fe'], ['86759', 'zip', '65281; MO; Chariton County; Salisbury'], ['86758', 'zip', '65280; MO; Mexico; Audrain County; Rush Hill'], ['86757', 'zip', '65279; MO; Columbia; Boone County; Rocheport'], ['86756', 'zip', '65278; MO; Moberly; Randolph County; Renick'], ['86755', 'zip', '65276; MO; Cooper County; Pilot Grove'], ['86754', 'zip', '65275; MO; Monroe County; Paris'], ['86753', 'zip', '65274; MO; Howard County; New Franklin'], ['86752', 'zip', '65270; MO; Moberly; Randolph County'], ['86751', 'zip', '65265; MO; Mexico; Audrain County'], ['86750', 'zip', '65264; MO; Mexico; Audrain County; Martinsburg'], ['86749', 'zip', '65263; MO; Monroe County; Madison'], ['86748', 'zip', '65262; MO; Jefferson City; Callaway County; Kingdom City'], ['86747', 'zip', '65261; MO; Chariton County; Marceline'], ['86746', 'zip', '65260; MO; Moberly; Randolph County; Jacksonville'], ['86745', 'zip', '65259; MO; Moberly; Randolph County; Huntsville'], ['86744', 'zip', '65258; MO; Monroe County; Holliday'], ['86743', 'zip', '65257; MO; Moberly; Randolph County; Higbee'], ['86742', 'zip', '65256; MO; Columbia; Boone County; Harrisburg'], ['86741', 'zip', '65255; MO; Columbia; Boone County; Hallsville'], ['86740', 'zip', '65254; MO; Howard County; Glasgow'], ['8674', 'city', 'Double Oak; TX; Dallas-Fort Worth-Arlington; Denton County'], ['86739', 'zip', '65251; MO; Jefferson City; Callaway County; Fulton'], ['86738', 'zip', '65250; MO; Howard County; Franklin'], ['86737', 'zip', '65248; MO; Howard County; Fayette'], ['86736', 'zip', '65247; MO; Macon County; Excello'], ['86735', 'zip', '65246; MO; Chariton County; Keytesville'], ['86734', 'zip', '65244; MO; Moberly; Randolph County; Clifton Hill'], ['86733', 'zip', '65243; MO; Moberly; Randolph County; Clark'], ['86732', 'zip', '65240; MO; Columbia; Boone County; Centralia'], ['86731', 'zip', '65239; MO; Moberly; Randolph County; Cairo'], ['86730', 'zip', '65237; MO; Cooper County; Bunceton'], ['86729', 'zip', '65236; MO; Chariton County; Brunswick'], ['86728', 'zip', '65233; MO; Cooper County; Boonville'], ['86727', 'zip', '65232; MO; Mexico; Audrain County; Benton City'], ['86726', 'zip', '65231; MO; Jefferson City; Callaway County; Auxvasse'], ['86725', 'zip', '65230; MO; Howard County; Armstrong'], ['86717', 'zip', '65203; MO; Columbia; Boone County'], ['86716', 'zip', '65202; MO; Columbia; Boone County'], ['86715', 'zip', '65201; MO; Columbia; Boone County'], ['86712', 'zip', '65109; MO; Jefferson City; Cole County'], ['8671', 'city', 'Dolton; IL; Chicago-Naperville-Elgin; Cook County'], ['86704', 'zip', '65101; MO; Jefferson City; Cole County'], ['86703', 'zip', '65085; MO; Jefferson City; Osage County; Westphalia'], ['86701', 'zip', '65083; MO; Miller County; Ulman'], ['86700', 'zip', '65082; MO; Miller County; Tuscumbia'], ['867', 'county', 'Benton County; MN; St. Cloud'], ['86699', 'zip', '65081; MO; Jefferson City; Moniteau County; Tipton'], ['86698', 'zip', '65080; MO; Jefferson City; Callaway County; Tebbetts'], ['86697', 'zip', '65079; MO; Camden County; Sunrise Beach'], ['86696', 'zip', '65078; MO; Morgan County; Stover'], ['86695', 'zip', '65077; MO; Jefferson City; Callaway County; Steedman'], ['86694', 'zip', '65076; MO; Jefferson City; Cole County; Saint Thomas'], ['86693', 'zip', '65075; MO; Miller County; Saint Elizabeth'], ['86692', 'zip', '65074; MO; Jefferson City; Cole County; Russellville'], ['86691', 'zip', '65072; MO; Morgan County; Rocky Mount'], ['86690', 'zip', '65069; MO; Montgomery County; Rhineland'], ['86689', 'zip', '65068; MO; Cooper County; Prairie Home'], ['86688', 'zip', '65067; MO; Jefferson City; Callaway County; Portland'], ['86687', 'zip', '65066; MO; Gasconade County; Owensville'], ['86686', 'zip', '65065; MO; Camden County; Osage Beach'], ['86685', 'zip', '65064; MO; Miller County; Olean'], ['86684', 'zip', '65063; MO; Jefferson City; Callaway County; New Bloomfield'], ['86683', 'zip', '65062; MO; Gasconade County; Mount Sterling'], ['86682', 'zip', '65061; MO; Gasconade County; Morrison'], ['86681', 'zip', '65059; MO; Jefferson City; Callaway County; Mokane'], ['86680', 'zip', '65058; MO; Jefferson City; Osage County; Meta'], ['86679', 'zip', '65055; MO; Jefferson City; Moniteau County; California'], ['86678', 'zip', '65054; MO; Jefferson City; Cole County'], ['86677', 'zip', '65053; MO; Jefferson City; Cole County; Lohman'], ['86676', 'zip', '65052; MO; Camden County; Linn Creek'], ['86675', 'zip', '65051; MO; Jefferson City; Osage County; Linn'], ['86674', 'zip', '65050; MO; Jefferson City; Moniteau County; Latham'], ['86673', 'zip', '65049; MO; Camden County; Village of Four Seasons'], ['86672', 'zip', '65048; MO; Jefferson City; Osage County; Westphalia'], ['86671', 'zip', '65047; MO; Miller County; Kaiser'], ['86670', 'zip', '65046; MO; Jefferson City; Moniteau County; Jamestown'], ['8667', 'city', 'Island Pond; VT; Berlin; Essex County'], ['86669', 'zip', '65043; MO; Jefferson City; Callaway County; Holts Summit'], ['86668', 'zip', '65042; MO; Jefferson City; Moniteau County; California'], ['86667', 'zip', '65041; MO; Gasconade County; Hermann'], ['86666', 'zip', '65040; MO; Jefferson City; Cole County; Henley'], ['86665', 'zip', '65039; MO; Columbia; Boone County; Hartsburg'], ['86663', 'zip', '65037; MO; Morgan County; Stover'], ['86661', 'zip', '65035; MO; Jefferson City; Osage County; Freeburg'], ['86660', 'zip', '65034; MO; Jefferson City; Moniteau County; Fortuna'], ['86659', 'zip', '65032; MO; Miller County; Eugene'], ['86658', 'zip', '65026; MO; Miller County; Eldon'], ['86657', 'zip', '65025; MO; Jefferson City; Moniteau County; Clarksburg'], ['86656', 'zip', '65024; MO; Jefferson City; Osage County; Chamois'], ['86655', 'zip', '65023; MO; Jefferson City; Cole County; Centertown'], ['86654', 'zip', '65020; MO; Camden County; Camdenton'], ['86653', 'zip', '65018; MO; Jefferson City; Moniteau County; California'], ['86652', 'zip', '65017; MO; Miller County; Brumley'], ['86651', 'zip', '65016; MO; Jefferson City; Callaway County; Tebbetts'], ['86650', 'zip', '65014; MO; Gasconade County; Bland'], ['8665', 'city', 'Groveland; MA; Boston-Cambridge-Newton; Essex County'], ['86649', 'zip', '65013; MO; Maries County; Belle'], ['86648', 'zip', '65011; MO; Morgan County; Barnett'], ['86647', 'zip', '65010; MO; Columbia; Boone County; Ashland'], ['86646', 'zip', '65001; MO; Jefferson City; Osage County; Argyle'], ['86643', 'zip', '64874; MO; Barry County; Wheaton'], ['86642', 'zip', '64873; MO; Joplin; Newton County; Wentworth'], ['86641', 'zip', '64870; MO; Joplin; Jasper County; Webb City'], ['86638', 'zip', '64867; MO; Fayetteville-Springdale-Rogers; McDonald County; Stella'], ['86637', 'zip', '64866; MO; Joplin; Newton County; Newtonia'], ['86636', 'zip', '64865; MO; Joplin; Newton County; Seneca'], ['86634', 'zip', '64863; MO; Fayetteville-Springdale-Rogers; McDonald County; South West City'], ['86633', 'zip', '64862; MO; Joplin; Jasper County; Sarcoxie'], ['86632', 'zip', '64861; MO; Fayetteville-Springdale-Rogers; McDonald County; Rocky Comfort'], ['86631', 'zip', '64859; MO; Joplin; Jasper County; Reeds'], ['86630', 'zip', '64858; MO; Joplin; Newton County; Seneca'], ['86629', 'zip', '64857; MO; Joplin; Jasper County; Purcell'], ['86628', 'zip', '64856; MO; Fayetteville-Springdale-Rogers; McDonald County; Pineville'], ['86627', 'zip', '64855; MO; Joplin; Jasper County; Oronogo'], ['86626', 'zip', '64854; MO; Fayetteville-Springdale-Rogers; McDonald County; Noel'], ['86624', 'zip', '64850; MO; Joplin; Newton County; Neosho'], ['86622', 'zip', '64848; MO; Lawrence County; La Russell'], ['86621', 'zip', '64847; MO; Fayetteville-Springdale-Rogers; McDonald County; Lanagan'], ['86620', 'zip', '64844; MO; Joplin; Newton County; Granby'], ['8662', 'city', 'Gridley; IL; Bloomington; McLean County'], ['86619', 'zip', '64843; MO; Fayetteville-Springdale-Rogers; McDonald County; Goodman'], ['86618', 'zip', '64842; MO; Joplin; Newton County; Fairview'], ['86616', 'zip', '64840; MO; Joplin; Newton County; Diamond'], ['86615', 'zip', '64836; MO; Joplin; Jasper County; Carthage'], ['86614', 'zip', '64835; MO; Joplin; Jasper County; Carterville'], ['86613', 'zip', '64834; MO; Joplin; Jasper County; Carl Junction'], ['86611', 'zip', '64832; MO; Joplin; Jasper County; Asbury'], ['86610', 'zip', '64831; MO; Fayetteville-Springdale-Rogers; McDonald County; Anderson'], ['86609', 'zip', '64830; MO; Joplin; Jasper County; Alba'], ['86608', 'zip', '64804; MO; Joplin; Jasper County'], ['86605', 'zip', '64801; MO; Joplin; Jasper County'], ['86604', 'zip', '64790; MO; Vernon County; Walker'], ['86602', 'zip', '64788; MO; Henry County; Urich'], ['86601', 'zip', '64784; MO; Vernon County; Sheldon'], ['86600', 'zip', '64783; MO; Vernon County; Schell City'], ['8660', 'city', 'Gresham; WI; Shawano; Shawano County'], ['866', 'county', 'Benton County; IN; Lafayette-West Lafayette'], ['86598', 'zip', '64780; MO; Saint Clair County; Rockville'], ['86597', 'zip', '64779; MO; Kansas City; Bates County; Rich Hill'], ['86596', 'zip', '64778; MO; Vernon County; Horton'], ['86595', 'zip', '64776; MO; Saint Clair County; Osceola'], ['86594', 'zip', '64772; MO; Vernon County; Nevada'], ['86593', 'zip', '64771; MO; Vernon County; Nevada'], ['86592', 'zip', '64770; MO; Henry County; Montrose'], ['86591', 'zip', '64769; MO; Barton County; Mindenmines'], ['86590', 'zip', '64767; MO; Vernon County; Nevada'], ['8659', 'city', 'Felton; MN; Fargo; Clay County']], 'columns': [{'name': 'region_id', 'type': 'text'}, {'name': 'region_type', 'type': 'text'}, {'name': 'region', 'type': 'text'}]}, 'meta': {'next_cursor_id': 'djFfODc1MjZfMTYxMDMzMjgwOA=='}}



```python
regions = pd.DataFrame(data)
print(regions.head())
```

                                                            datatable  \
    data            [[99999, zip, 98847; WA; Wenatchee; Chelan Cou...   
    columns         [{'name': 'region_id', 'type': 'text'}, {'name...   
    next_cursor_id                                                NaN   
    
                                            meta  
    data                                     NaN  
    columns                                  NaN  
    next_cursor_id  djFfODc1MjZfMTYxMDMzMjgwOA==  



```python
regions
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datatable</th>
      <th>meta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>data</th>
      <td>[[99999, zip, 98847; WA; Wenatchee; Chelan Cou...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>columns</th>
      <td>[{'name': 'region_id', 'type': 'text'}, {'name...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>next_cursor_id</th>
      <td>NaN</td>
      <td>djFfODc1MjZfMTYxMDMzMjgwOA==</td>
    </tr>
  </tbody>
</table>
</div>




```python
reg = regions['datatable']['data']
reg
```




    [['99999', 'zip', '98847; WA; Wenatchee; Chelan County; Peshastin'],
     ['99998', 'zip', '98846; WA; Okanogan County; Pateros'],
     ['99997', 'zip', '98845; WA; Wenatchee; Douglas County; Palisades'],
     ['99996', 'zip', '98844; WA; Okanogan County; Oroville'],
     ['99995', 'zip', '98843; WA; Wenatchee; Douglas County; Orondo'],
     ['99994', 'zip', '98841; WA; Okanogan County; Omak'],
     ['99993', 'zip', '98840; WA; Okanogan County; Okanogan'],
     ['99992', 'zip', '98837; WA; Moses Lake; Grant County'],
     ['99991', 'zip', '98836; WA; Wenatchee; Chelan County'],
     ['99990', 'zip', '98834; WA; Okanogan County; Methow'],
     ['9999',
      'city',
      'Carrsville; VA; Virginia Beach-Norfolk-Newport News; Isle of Wight County'],
     ['99989', 'zip', '98833; WA; Okanogan County; Mazama'],
     ['99988', 'zip', '98832; WA; Moses Lake; Grant County; Krupp'],
     ['99987', 'zip', '98831; WA; Wenatchee; Chelan County; Manson'],
     ['99986', 'zip', '98830; WA; Wenatchee; Douglas County; Mansfield'],
     ['99985', 'zip', '98829; WA; Okanogan County; Okanogan'],
     ['99984', 'zip', '98828; WA; Wenatchee; Chelan County; Malaga'],
     ['99983', 'zip', '98827; WA; Okanogan County; Loomis'],
     ['99982', 'zip', '98826; WA; Wenatchee; Chelan County; Leavenworth'],
     ['99980', 'zip', '98823; WA; Moses Lake; Grant County; Ephrata'],
     ['9998', 'city', 'Birchleaf; VA; Big Stone Gap; Dickenson County'],
     ['99979', 'zip', '98822; WA; Wenatchee; Chelan County; Entiat'],
     ['99978', 'zip', '98821; WA; Wenatchee; Chelan County; Cashmere'],
     ['99977', 'zip', '98819; WA; Okanogan County; Omak'],
     ['99976', 'zip', '98817; WA; Wenatchee; Chelan County; Chelan Falls'],
     ['99975', 'zip', '98816; WA; Wenatchee; Chelan County; Chelan'],
     ['99974', 'zip', '98815; WA; Wenatchee; Chelan County; Cashmere'],
     ['99973', 'zip', '98814; WA; Okanogan County; Carlton'],
     ['99972', 'zip', '98813; WA; Wenatchee; Douglas County; Bridgeport'],
     ['99971', 'zip', '98812; WA; Okanogan County; Brewster'],
     ['99970', 'zip', '98811; WA; Wenatchee; Chelan County; Entiat'],
     ['99968',
      'zip',
      '98802; WA; Wenatchee; Douglas County; East Wenatchee Bench'],
     ['99967', 'zip', '98801; WA; Wenatchee; Chelan County'],
     ['99965', 'zip', '98686'],
     ['99964', 'zip', '98685'],
     ['99963',
      'zip',
      '98684; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'],
     ['99962', 'zip', '98683'],
     ['99961', 'zip', '98682'],
     ['99960',
      'zip',
      '98675; WA; Portland-Vancouver-Hillsboro; Clark County; Yacolt'],
     ['99959', 'zip', '98674; WA; Longview; Cowlitz County; Woodland'],
     ['99958', 'zip', '98673; WA; Klickitat County; Centerville'],
     ['99957', 'zip', '98672; WA; Klickitat County; White Salmon'],
     ['99956', 'zip', '98671'],
     ['99955', 'zip', '98670; WA; Klickitat County; Wahkiacus'],
     ['99951',
      'zip',
      '98665; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'],
     ['99950',
      'zip',
      '98664; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'],
     ['99949',
      'zip',
      '98663; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'],
     ['99948', 'zip', '98662'],
     ['99947', 'zip', '98661'],
     ['99946', 'zip', '98660'],
     ['99945',
      'zip',
      '98651; WA; Portland-Vancouver-Hillsboro; Skamania County; Underwood'],
     ['99944', 'zip', '98650; WA; Klickitat County; Trout Lake'],
     ['99943', 'zip', '98649; WA; Longview; Cowlitz County; Toutle'],
     ['99942',
      'zip',
      '98648; WA; Portland-Vancouver-Hillsboro; Skamania County; Stevenson'],
     ['99941', 'zip', '98647; WA; Wahkiakum County; Skamokawa'],
     ['99940', 'zip', '98645; WA; Longview; Cowlitz County; Silverlake'],
     ['9994', 'city', 'Wright; KS; Dodge City; Ford County'],
     ['99939', 'zip', '98644; WA; Pacific County; Ilwaco'],
     ['99938', 'zip', '98643; WA; Wahkiakum County; Rosburg'],
     ['99937', 'zip', '98642'],
     ['99936', 'zip', '98641; WA; Pacific County; Ocean Park'],
     ['99935', 'zip', '98640; WA; Pacific County; Ocean Park'],
     ['99934',
      'zip',
      '98639; WA; Portland-Vancouver-Hillsboro; Skamania County; North Bonneville'],
     ['99933', 'zip', '98638; WA; Pacific County; Naselle'],
     ['99931', 'zip', '98635; WA; Klickitat County; Lyle'],
     ['99930', 'zip', '98632; WA; Longview; Cowlitz County'],
     ['99929', 'zip', '98631; WA; Pacific County; Long Beach'],
     ['99928',
      'zip',
      '98629; WA; Portland-Vancouver-Hillsboro; Clark County; La Center'],
     ['99927', 'zip', '98628; WA; Klickitat County; Lyle'],
     ['99926', 'zip', '98626; WA; Longview; Cowlitz County; Kelso'],
     ['99925', 'zip', '98625; WA; Longview; Cowlitz County; Kalama'],
     ['99924', 'zip', '98624; WA; Pacific County; Ilwaco'],
     ['99923', 'zip', '98623; WA; Klickitat County; White Salmon'],
     ['99921', 'zip', '98621; WA; Wahkiakum County; Grays River'],
     ['99920', 'zip', '98620; WA; Klickitat County; Goldendale'],
     ['99919', 'zip', '98619; WA; Klickitat County; Glenwood'],
     ['99918', 'zip', '98617; WA; Klickitat County; Dallesport'],
     ['99917', 'zip', '98616; WA; Longview; Cowlitz County; Cougar'],
     ['99916', 'zip', '98614; WA; Pacific County; Chinook'],
     ['99915', 'zip', '98613; WA; Klickitat County; Centerville'],
     ['99914', 'zip', '98612; WA; Wahkiakum County; Cathlamet'],
     ['99913', 'zip', '98611; WA; Longview; Cowlitz County; Castle Rock'],
     ['99912',
      'zip',
      '98610; WA; Portland-Vancouver-Hillsboro; Skamania County; Carson'],
     ['99911', 'zip', '98609; WA; Longview; Cowlitz County; Kelso'],
     ['99910', 'zip', '98607'],
     ['99909',
      'zip',
      '98606; WA; Portland-Vancouver-Hillsboro; Clark County; Brush Prairie'],
     ['99908',
      'zip',
      '98605; WA; Portland-Vancouver-Hillsboro; Skamania County; Bingen'],
     ['99907',
      'zip',
      '98604; WA; Portland-Vancouver-Hillsboro; Clark County; Battle Ground'],
     ['99906', 'zip', '98603; WA; Longview; Cowlitz County; Ariel'],
     ['99905', 'zip', '98602; WA; Klickitat County; Appleton'],
     ['99904',
      'zip',
      '98601; WA; Portland-Vancouver-Hillsboro; Clark County; Amboy'],
     ['99902', 'zip', '98597; WA; Olympia-Tumwater; Thurston County; Yelm'],
     ['99901', 'zip', '98596; WA; Centralia; Lewis County; Winlock'],
     ['99900', 'zip', '98595; WA; Aberdeen; Grays Harbor County; Westport'],
     ['999', 'county', 'Durham County; NC; Durham-Chapel Hill'],
     ['99899', 'zip', '98593; WA; Centralia; Lewis County; Vader'],
     ['99898', 'zip', '98592; WA; Shelton; Mason County; Union'],
     ['99897', 'zip', '98591; WA; Centralia; Lewis County; Toledo'],
     ['99896', 'zip', '98590; WA; Pacific County; Tokeland'],
     ['99895', 'zip', '98589; WA; Olympia-Tumwater; Thurston County; Tenino'],
     ['99894', 'zip', '98588; WA; Shelton; Mason County; Tahuya'],
     ['99893', 'zip', '98587; WA; Aberdeen; Grays Harbor County; Moclips'],
     ['99892', 'zip', '98586; WA; Pacific County; South Bend'],
     ['99891', 'zip', '98585; WA; Centralia; Lewis County; Silver Creek'],
     ['99890', 'zip', '98584; WA; Shelton; Mason County'],
     ['99889', 'zip', '98583; WA; Aberdeen; Grays Harbor County; Elma'],
     ['99888', 'zip', '98582; WA; Centralia; Lewis County; Salkum'],
     ['99887', 'zip', '98581; WA; Longview; Cowlitz County; Ryderwood'],
     ['99886', 'zip', '98580; WA; Seattle-Tacoma-Bellevue; Pierce County; Roy'],
     ['99885', 'zip', '98579; WA; Olympia-Tumwater; Thurston County; Rochester'],
     ['99884', 'zip', '98577; WA; Pacific County; Raymond'],
     ['99883', 'zip', '98576; WA; Olympia-Tumwater; Thurston County; Rainier'],
     ['99882', 'zip', '98575; WA; Aberdeen; Grays Harbor County; Quinault'],
     ['99881', 'zip', '98572; WA; Centralia; Lewis County; Pe Ell'],
     ['99880', 'zip', '98571; WA; Aberdeen; Grays Harbor County; Pacific Beach'],
     ['99879', 'zip', '98570; WA; Centralia; Lewis County; Onalaska'],
     ['99878', 'zip', '98569; WA; Aberdeen; Grays Harbor County; Ocean Shores'],
     ['99877', 'zip', '98568; WA; Aberdeen; Grays Harbor County; Oakville'],
     ['99876', 'zip', '98566; WA; Aberdeen; Grays Harbor County; Humptulips'],
     ['99875', 'zip', '98565; WA; Centralia; Lewis County; Napavine'],
     ['99874', 'zip', '98564; WA; Centralia; Lewis County; Mossyrock'],
     ['99873', 'zip', '98563; WA; Aberdeen; Grays Harbor County; Montesano'],
     ['99872', 'zip', '98562; WA; Aberdeen; Grays Harbor County; Moclips'],
     ['99870', 'zip', '98560; WA; Shelton; Mason County; Matlock'],
     ['9987', 'city', 'Weston; CT; Bridgeport-Stamford-Norwalk; Fairfield County'],
     ['99868', 'zip', '98558; WA; Seattle-Tacoma-Bellevue; Pierce County; Roy'],
     ['99867', 'zip', '98557; WA; Aberdeen; Grays Harbor County; McCleary'],
     ['99865', 'zip', '98555; WA; Shelton; Mason County; Lilliwaup'],
     ['99863', 'zip', '98552; WA; Aberdeen; Grays Harbor County; Humptulips'],
     ['99862', 'zip', '98550; WA; Aberdeen; Grays Harbor County; Hoquiam'],
     ['99861', 'zip', '98548; WA; Shelton; Mason County; Hoodsport'],
     ['99860', 'zip', '98547; WA; Aberdeen; Grays Harbor County; Grayland'],
     ['99859', 'zip', '98546; WA; Shelton; Mason County; Grapeview'],
     ['99857', 'zip', '98542; WA; Centralia; Lewis County; Ethel'],
     ['99856', 'zip', '98541; WA; Aberdeen; Grays Harbor County; Elma'],
     ['99853', 'zip', '98538; WA; Centralia; Lewis County; Chehalis'],
     ['99852', 'zip', '98537; WA; Aberdeen; Grays Harbor County; Cosmopolis'],
     ['99851', 'zip', '98536; WA; Aberdeen; Grays Harbor County; Hoquiam'],
     ['99850', 'zip', '98535; WA; Aberdeen; Grays Harbor County; Copalis Beach'],
     ['99849', 'zip', '98533; WA; Centralia; Lewis County; Cinebar'],
     ['99848', 'zip', '98532; WA; Centralia; Lewis County; Chehalis'],
     ['99847', 'zip', '98531; WA; Centralia; Lewis County'],
     ['99846', 'zip', '98530; WA; Olympia-Tumwater; Thurston County; Bucoda'],
     ['99845', 'zip', '98528; WA; Shelton; Mason County; Belfair'],
     ['99844', 'zip', '98527; WA; Pacific County; South Bend'],
     ['99843', 'zip', '98526; WA; Aberdeen; Grays Harbor County; Amanda Park'],
     ['99842', 'zip', '98524; WA; Shelton; Mason County; Allyn'],
     ['99841', 'zip', '98522; WA; Centralia; Lewis County; Chehalis'],
     ['99840', 'zip', '98520; WA; Aberdeen; Grays Harbor County'],
     ['99839', 'zip', '98516; WA; Olympia-Tumwater; Thurston County; Lacey'],
     ['99838', 'zip', '98513; WA; Olympia-Tumwater; Thurston County; Lacey'],
     ['99837', 'zip', '98512; WA; Olympia-Tumwater; Thurston County; Tumwater'],
     ['99832', 'zip', '98506; WA; Olympia-Tumwater; Thurston County; Olympia'],
     ['99829', 'zip', '98503; WA; Olympia-Tumwater; Thurston County; Lacey'],
     ['99828', 'zip', '98502; WA; Olympia-Tumwater; Thurston County; Olympia'],
     ['99827', 'zip', '98501; WA; Olympia-Tumwater; Thurston County; Olympia'],
     ['99826',
      'zip',
      '98499; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakewood'],
     ['99825', 'zip', '98498'],
     ['99818', 'zip', '98467'],
     ['99817', 'zip', '98466'],
     ['99816', 'zip', '98465; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'],
     ['99810', 'zip', '98446'],
     ['99809', 'zip', '98445'],
     ['99808', 'zip', '98444'],
     ['99807', 'zip', '98443; WA; Seattle-Tacoma-Bellevue; Pierce County; Waller'],
     ['99805',
      'zip',
      '98439; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakewood'],
     ['99800', 'zip', '98424; WA; Seattle-Tacoma-Bellevue; Pierce County; Fife'],
     ['9980',
      'city',
      'South Wilmington; IL; Chicago-Naperville-Elgin; Grundy County'],
     ['998', 'county', 'Duplin County; NC'],
     ['99799', 'zip', '98422; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'],
     ['99798', 'zip', '98421; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'],
     ['99797', 'zip', '98418; WA; Seattle-Tacoma-Bellevue; Pierce County; Tacoma'],
     ['99791', 'zip', '98409'],
     ['99790', 'zip', '98408'],
     ['99789', 'zip', '98407'],
     ['99788', 'zip', '98406'],
     ['99787', 'zip', '98405'],
     ['99786', 'zip', '98404'],
     ['99785', 'zip', '98403'],
     ['99784', 'zip', '98402'],
     ['99780',
      'zip',
      '98396; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'],
     ['9978', 'city', 'Waldo; FL; Gainesville; Alachua County'],
     ['99778', 'zip', '98394; WA; Seattle-Tacoma-Bellevue; Pierce County; Vaughn'],
     ['99776', 'zip', '98392; WA; Bremerton-Silverdale; Kitsap County; Suquamish'],
     ['99775', 'zip', '98391'],
     ['99774', 'zip', '98390; WA; Seattle-Tacoma-Bellevue; Pierce County; Sumner'],
     ['99773',
      'zip',
      '98388; WA; Seattle-Tacoma-Bellevue; Pierce County; Steilacoom'],
     ['99772', 'zip', '98387'],
     ['99770',
      'zip',
      '98385; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'],
     ['99768',
      'zip',
      '98383; WA; Bremerton-Silverdale; Kitsap County; Silverdale'],
     ['99767', 'zip', '98382; WA; Port Angeles; Clallam County; Sequim'],
     ['99766', 'zip', '98381; WA; Port Angeles; Clallam County; Sekiu'],
     ['99765', 'zip', '98380; WA; Bremerton-Silverdale; Kitsap County; Seabeck'],
     ['99763', 'zip', '98377; WA; Centralia; Lewis County; Randle'],
     ['99762', 'zip', '98376; WA; Jefferson County; Quilcene'],
     ['99761', 'zip', '98375'],
     ['99760', 'zip', '98374'],
     ['99759', 'zip', '98373'],
     ['99758', 'zip', '98372'],
     ['99757',
      'zip',
      '98371; WA; Seattle-Tacoma-Bellevue; Pierce County; Puyallup'],
     ['99756', 'zip', '98370; WA; Bremerton-Silverdale; Kitsap County; Poulsbo'],
     ['99755', 'zip', '98368; WA; Jefferson County; Port Townsend'],
     ['99754',
      'zip',
      '98367; WA; Bremerton-Silverdale; Kitsap County; Port Orchard'],
     ['99753',
      'zip',
      '98366; WA; Bremerton-Silverdale; Kitsap County; Port Orchard'],
     ['99752', 'zip', '98365; WA; Jefferson County; Port Ludlow'],
     ['99750', 'zip', '98363; WA; Port Angeles; Clallam County'],
     ['99749', 'zip', '98362; WA; Port Angeles; Clallam County'],
     ['99748', 'zip', '98361; WA; Centralia; Lewis County; Packwood'],
     ['99747', 'zip', '98360; WA; Seattle-Tacoma-Bellevue; Pierce County; Orting'],
     ['99746', 'zip', '98359; WA; Bremerton-Silverdale; Kitsap County; Olalla'],
     ['99745', 'zip', '98358; WA; Jefferson County; Nordland'],
     ['99743', 'zip', '98356; WA; Centralia; Lewis County; Morton'],
     ['99742', 'zip', '98355; WA; Centralia; Lewis County; Mineral'],
     ['99741', 'zip', '98354; WA; Seattle-Tacoma-Bellevue; Pierce County; Milton'],
     ['99738',
      'zip',
      '98351; WA; Seattle-Tacoma-Bellevue; Pierce County; Longbranch'],
     ['99736',
      'zip',
      '98349; WA; Seattle-Tacoma-Bellevue; Pierce County; Lakebay'],
     ['99734', 'zip', '98346; WA; Bremerton-Silverdale; Kitsap County; Kingston'],
     ['99733', 'zip', '98345; WA; Bremerton-Silverdale; Kitsap County; Keyport'],
     ['99730', 'zip', '98342; WA; Bremerton-Silverdale; Kitsap County; Indianola'],
     ['9973', 'city', 'Vienna; MD; Cambridge; Dorchester County'],
     ['99729', 'zip', '98340; WA; Bremerton-Silverdale; Kitsap County; Hansville'],
     ['99728', 'zip', '98339; WA; Jefferson County; Port Hadlock'],
     ['99727', 'zip', '98338; WA; Seattle-Tacoma-Bellevue; Pierce County; Graham'],
     ['99726', 'zip', '98337; WA; Bremerton-Silverdale; Kitsap County; Bremerton'],
     ['99725', 'zip', '98336; WA; Centralia; Lewis County; Glenoma'],
     ['99724', 'zip', '98335'],
     ['99723',
      'zip',
      '98333; WA; Seattle-Tacoma-Bellevue; Pierce County; Fox Island'],
     ['99722',
      'zip',
      '98332; WA; Seattle-Tacoma-Bellevue; Pierce County; Gig Harbor'],
     ['99721', 'zip', '98331; WA; Port Angeles; Clallam County; Forks'],
     ['99720',
      'zip',
      '98330; WA; Seattle-Tacoma-Bellevue; Pierce County; Eatonville'],
     ['99719',
      'zip',
      '98329; WA; Seattle-Tacoma-Bellevue; Pierce County; Gig Harbor'],
     ['99718',
      'zip',
      '98328; WA; Seattle-Tacoma-Bellevue; Pierce County; Eatonville'],
     ['99717', 'zip', '98327'],
     ['99716', 'zip', '98326; WA; Port Angeles; Clallam County; Clallam Bay'],
     ['99715', 'zip', '98325; WA; Jefferson County; Chimacum'],
     ['99713',
      'zip',
      '98323; WA; Seattle-Tacoma-Bellevue; Pierce County; Carbonado'],
     ['99711',
      'zip',
      '98321; WA; Seattle-Tacoma-Bellevue; Pierce County; Buckley'],
     ['99710', 'zip', '98320; WA; Jefferson County; Brinnon'],
     ['99707', 'zip', '98312; WA; Bremerton-Silverdale; Kitsap County; Bremerton'],
     ['99706', 'zip', '98311; WA; Bremerton-Silverdale; Kitsap County; Bremerton'],
     ['99705', 'zip', '98310; WA; Bremerton-Silverdale; Kitsap County; Bremerton'],
     ['99704', 'zip', '98305; WA; Port Angeles; Clallam County; Beaver'],
     ['99703',
      'zip',
      '98304; WA; Seattle-Tacoma-Bellevue; Pierce County; Ashford'],
     ['99702', 'zip', '98303; WA; Seattle-Tacoma-Bellevue; Pierce County; Graham'],
     ['99700',
      'zip',
      '98296; WA; Seattle-Tacoma-Bellevue; Snohomish County; Snohomish'],
     ['997', 'county', 'Dubois County; IN; Jasper'],
     ['99699', 'zip', '98295; WA; Bellingham; Whatcom County; Sumas'],
     ['99698',
      'zip',
      '98294; WA; Seattle-Tacoma-Bellevue; Snohomish County; Sultan'],
     ['99696',
      'zip',
      '98292; WA; Seattle-Tacoma-Bellevue; Snohomish County; Stanwood'],
     ['99694',
      'zip',
      '98290; WA; Seattle-Tacoma-Bellevue; Snohomish County; Snohomish'],
     ['99693',
      'zip',
      '98288; WA; Seattle-Tacoma-Bellevue; King County; Skykomish'],
     ['99691', 'zip', '98286; WA; San Juan County; Eastsound'],
     ['99690',
      'zip',
      '98284; WA; Mount Vernon-Anacortes; Skagit County; Sedro-Woolley'],
     ['9969', 'city', 'Tinley Park; IL; Chicago-Naperville-Elgin; Cook County'],
     ['99689',
      'zip',
      '98283; WA; Mount Vernon-Anacortes; Skagit County; Rockport'],
     ['99688', 'zip', '98282; WA; Oak Harbor; Island County; Camano Island'],
     ['99687', 'zip', '98281; WA; Bellingham; Whatcom County; Point Roberts'],
     ['99686', 'zip', '98280; WA; San Juan County; Eastsound'],
     ['99685', 'zip', '98279; WA; San Juan County; Olga'],
     ['99683', 'zip', '98277; WA; Oak Harbor; Island County'],
     ['99682', 'zip', '98276; WA; Bellingham; Whatcom County; Nooksack'],
     ['99681', 'zip', '98275'],
     ['99680',
      'zip',
      '98274; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'],
     ['99679',
      'zip',
      '98273; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'],
     ['99678',
      'zip',
      '98272; WA; Seattle-Tacoma-Bellevue; Snohomish County; Monroe'],
     ['99677', 'zip', '98271'],
     ['99676', 'zip', '98270'],
     ['99675',
      'zip',
      '98267; WA; Mount Vernon-Anacortes; Skagit County; Marblemount'],
     ['99674', 'zip', '98266; WA; Bellingham; Whatcom County; Maple Falls'],
     ['99673', 'zip', '98264; WA; Bellingham; Whatcom County; Lynden'],
     ['99672', 'zip', '98263; WA; Mount Vernon-Anacortes; Skagit County; Lyman'],
     ['99671', 'zip', '98262; WA; Bellingham; Whatcom County; Lummi Island'],
     ['99670', 'zip', '98261; WA; San Juan County; Eastsound'],
     ['9967', 'city', 'Yorktown; TX; De Witt County'],
     ['99669', 'zip', '98260; WA; Oak Harbor; Island County; Langley'],
     ['99667', 'zip', '98258'],
     ['99666',
      'zip',
      '98257; WA; Mount Vernon-Anacortes; Skagit County; La Conner'],
     ['99665',
      'zip',
      '98256; WA; Seattle-Tacoma-Bellevue; Snohomish County; Index'],
     ['99664',
      'zip',
      '98255; WA; Mount Vernon-Anacortes; Skagit County; Hamilton'],
     ['99663', 'zip', '98253; WA; Oak Harbor; Island County; Greenbank'],
     ['99662',
      'zip',
      '98252; WA; Seattle-Tacoma-Bellevue; Snohomish County; Granite Falls'],
     ['99661',
      'zip',
      '98251; WA; Seattle-Tacoma-Bellevue; Snohomish County; Gold Bar'],
     ['99660', 'zip', '98250; WA; San Juan County; Friday Harbor'],
     ['9966', 'city', 'Yachats; OR; Newport; Lincoln County'],
     ['99659', 'zip', '98249; WA; Oak Harbor; Island County; Freeland'],
     ['99658', 'zip', '98248; WA; Bellingham; Whatcom County; Ferndale'],
     ['99657', 'zip', '98247; WA; Bellingham; Whatcom County; Everson'],
     ['99656', 'zip', '98245; WA; San Juan County; Eastsound'],
     ['99655', 'zip', '98244; WA; Bellingham; Whatcom County; Deming'],
     ['99653',
      'zip',
      '98241; WA; Seattle-Tacoma-Bellevue; Snohomish County; Darrington'],
     ['99652', 'zip', '98240; WA; Bellingham; Whatcom County; Custer'],
     ['99651', 'zip', '98239; WA; Oak Harbor; Island County; Coupeville'],
     ['99649',
      'zip',
      '98237; WA; Mount Vernon-Anacortes; Skagit County; Concrete'],
     ['99648', 'zip', '98236; WA; Oak Harbor; Island County; Clinton'],
     ['99647',
      'zip',
      '98235; WA; Mount Vernon-Anacortes; Skagit County; Mount Vernon'],
     ['99646',
      'zip',
      '98233; WA; Mount Vernon-Anacortes; Skagit County; Burlington'],
     ['99645', 'zip', '98232; WA; Mount Vernon-Anacortes; Skagit County; Bow'],
     ['99643', 'zip', '98230; WA; Bellingham; Whatcom County; Blaine'],
     ['99642', 'zip', '98229; WA; Bellingham; Whatcom County'],
     ['99639', 'zip', '98226; WA; Bellingham; Whatcom County'],
     ['99638', 'zip', '98225; WA; Bellingham; Whatcom County'],
     ['99637',
      'zip',
      '98224; WA; Seattle-Tacoma-Bellevue; Snohomish County; Gold Bar'],
     ['99636',
      'zip',
      '98223; WA; Seattle-Tacoma-Bellevue; Snohomish County; Arlington'],
     ['99635', 'zip', '98222; WA; San Juan County; Olga'],
     ['99634',
      'zip',
      '98221; WA; Mount Vernon-Anacortes; Skagit County; Anacortes'],
     ['99633', 'zip', '98220; WA; Bellingham; Whatcom County; Acme'],
     ['99631', 'zip', '98208'],
     ['9963', 'city', 'Satartia; MS; Jackson; Yazoo County'],
     ['99629', 'zip', '98206; WA; Oak Harbor; Island County; Clinton'],
     ['99627',
      'zip',
      '98204; WA; Seattle-Tacoma-Bellevue; Snohomish County; Paine Field-Lake Stickney'],
     ['99626',
      'zip',
      '98203; WA; Seattle-Tacoma-Bellevue; Snohomish County; Everett'],
     ['99625', 'zip', '98201'],
     ['99624', 'zip', '98199'],
     ['99623', 'zip', '98198'],
     ['9962', 'city', 'Vale; SD; Butte County'],
     ['99618', 'zip', '98188; WA; Seattle-Tacoma-Bellevue; King County; Seatac'],
     ['99614', 'zip', '98178'],
     ['99613', 'zip', '98177'],
     ['9961', 'city', 'San Patricio; TX; Corpus Christi; San Patricio County'],
     ['99608', 'zip', '98168'],
     ['99607', 'zip', '98166; WA; Seattle-Tacoma-Bellevue; King County; Burien'],
     ['99605', 'zip', '98164; WA; Seattle-Tacoma-Bellevue; King County; Seattle'],
     ['99601', 'zip', '98155'],
     ['9960', 'city', 'Wildrose; ND; Williston; Williams County'],
     ['99598', 'zip', '98148; WA; Seattle-Tacoma-Bellevue; King County; Burien'],
     ['99597', 'zip', '98146'],
     ['99595', 'zip', '98144'],
     ['99591', 'zip', '98136; WA; Seattle-Tacoma-Bellevue; King County; Seattle'],
     ['99590', 'zip', '98134; WA; Seattle-Tacoma-Bellevue; King County; Seattle'],
     ['99589', 'zip', '98133'],
     ['99584', 'zip', '98126; WA; Seattle-Tacoma-Bellevue; King County; Seattle'],
     ['99583', 'zip', '98125'],
     ['99581', 'zip', '98122'],
     ['99580', 'zip', '98121'],
     ['9958', 'city', 'Spurlockville; WV; Huntington-Ashland; Lincoln County'],
     ['99579', 'zip', '98119'],
     ['99578', 'zip', '98118'],
     ['99577', 'zip', '98117'],
     ['99576', 'zip', '98116'],
     ['99575', 'zip', '98115'],
     ['99572', 'zip', '98112'],
     ['99570',
      'zip',
      '98110; WA; Bremerton-Silverdale; Kitsap County; Bainbridge Island'],
     ['99569', 'zip', '98109'],
     ['99568', 'zip', '98108'],
     ['99567', 'zip', '98107'],
     ['99566', 'zip', '98106'],
     ['99565', 'zip', '98105; WA; Seattle-Tacoma-Bellevue; King County; Seattle'],
     ['99564', 'zip', '98104'],
     ['99563', 'zip', '98103'],
     ['99562', 'zip', '98102'],
     ['99561', 'zip', '98101'],
     ['9956', 'city', 'Prairie Rose; ND; Fargo; Cass County'],
     ['99559', 'zip', '98092; WA; Seattle-Tacoma-Bellevue; King County; Auburn'],
     ['99557', 'zip', '98087'],
     ['99554',
      'zip',
      '98077; WA; Seattle-Tacoma-Bellevue; King County; Woodinville'],
     ['99553', 'zip', '98075'],
     ['99552',
      'zip',
      '98074; WA; Seattle-Tacoma-Bellevue; King County; Sammamish'],
     ['99550',
      'zip',
      '98072; WA; Seattle-Tacoma-Bellevue; King County; Woodinville'],
     ['99548', 'zip', '98070; WA; Seattle-Tacoma-Bellevue; King County; Vashon'],
     ['99547', 'zip', '98068; WA; Ellensburg; Kittitas County; Snoqualmie Pass'],
     ['99546',
      'zip',
      '98065; WA; Seattle-Tacoma-Bellevue; King County; Snoqualmie'],
     ['99541', 'zip', '98059'],
     ['99540', 'zip', '98058'],
     ['99539', 'zip', '98057; WA; Seattle-Tacoma-Bellevue; King County; Renton'],
     ['99538', 'zip', '98056'],
     ['99537', 'zip', '98055'],
     ['99535', 'zip', '98053'],
     ['99534', 'zip', '98052'],
     ['99533',
      'zip',
      '98051; WA; Seattle-Tacoma-Bellevue; King County; Ravensdale'],
     ['99532', 'zip', '98050; WA; Seattle-Tacoma-Bellevue; King County; Issaquah'],
     ['99531', 'zip', '98047; WA; Seattle-Tacoma-Bellevue; King County; Pacific'],
     ['9953', 'city', 'Logansport; IN; Cass County'],
     ['99529',
      'zip',
      '98045; WA; Seattle-Tacoma-Bellevue; King County; North Bend'],
     ['99528', 'zip', '98043'],
     ['99527', 'zip', '98042'],
     ['99525', 'zip', '98040'],
     ['99524', 'zip', '98039; WA; Seattle-Tacoma-Bellevue; King County; Medina'],
     ['99523', 'zip', '98038'],
     ['99522',
      'zip',
      '98037; WA; Seattle-Tacoma-Bellevue; Snohomish County; Lynnwood'],
     ['99521',
      'zip',
      '98036; WA; Seattle-Tacoma-Bellevue; Snohomish County; Lynnwood'],
     ['99519', 'zip', '98034'],
     ['99518', 'zip', '98033'],
     ['99517', 'zip', '98032'],
     ['99516', 'zip', '98031'],
     ['99515', 'zip', '98030'],
     ['99514', 'zip', '98029'],
     ['99513', 'zip', '98028; WA; Seattle-Tacoma-Bellevue; King County; Kenmore'],
     ['99512', 'zip', '98027; WA; Seattle-Tacoma-Bellevue; King County; Issaquah'],
     ['99511', 'zip', '98026'],
     ['9951', 'city', 'Lizella; GA; Macon-Bibb County; Bibb County'],
     ['99509',
      'zip',
      '98024; WA; Seattle-Tacoma-Bellevue; King County; Fall City'],
     ['99508', 'zip', '98023'],
     ['99507', 'zip', '98022; WA; Seattle-Tacoma-Bellevue; King County; Enumclaw'],
     ['99506',
      'zip',
      '98021; WA; Seattle-Tacoma-Bellevue; Snohomish County; Bothell'],
     ['99505', 'zip', '98020'],
     ['99504', 'zip', '98019; WA; Seattle-Tacoma-Bellevue; King County; Duvall'],
     ['99502',
      'zip',
      '98014; WA; Seattle-Tacoma-Bellevue; King County; Carnation'],
     ['99500', 'zip', '98012'],
     ['9950', 'city', 'Lincoln; VT; Addison County'],
     ['995', 'county', 'Donley County; TX'],
     ['99499', 'zip', '98011'],
     ['99498',
      'zip',
      '98010; WA; Seattle-Tacoma-Bellevue; King County; Black Diamond'],
     ['99496', 'zip', '98008; WA; Seattle-Tacoma-Bellevue; King County; Bellevue'],
     ['99495', 'zip', '98007'],
     ['99494', 'zip', '98006'],
     ['99493', 'zip', '98005'],
     ['99492', 'zip', '98004'],
     ['99491',
      'zip',
      '98003; WA; Seattle-Tacoma-Bellevue; King County; Federal Way'],
     ['99490', 'zip', '98002'],
     ['9949', 'city', 'Lewisburg; TN; Marshall County'],
     ['99489', 'zip', '98001; WA; Seattle-Tacoma-Bellevue; King County; Auburn'],
     ['99488', 'zip', '97920; OR; Ontario; Malheur County; Westfall'],
     ['99487', 'zip', '97918; OR; Ontario; Malheur County; Vale'],
     ['99485', 'zip', '97914; OR; Ontario; Malheur County'],
     ['99484', 'zip', '97913; OR; Ontario; Malheur County; Nyssa'],
     ['99483', 'zip', '97911; OR; Ontario; Malheur County; Juntura'],
     ['99482', 'zip', '97910; OR; Ontario; Malheur County; Jordan Valley'],
     ['99481', 'zip', '97909; OR; Ontario; Malheur County; Vale'],
     ['99480', 'zip', '97908; OR; Ontario; Malheur County; Ironside'],
     ['99479', 'zip', '97907; OR; Baker County; Huntington'],
     ['99478', 'zip', '97906; OR; Ontario; Malheur County; Harper'],
     ['99477', 'zip', '97905; OR; Baker County; Durkee'],
     ['99476', 'zip', '97904; OR; Harney County; Drewsey'],
     ['99475', 'zip', '97903; OR; Ontario; Malheur County; Brogan'],
     ['99474', 'zip', '97902; OR; Ontario; Malheur County; Jordan Valley'],
     ['99473', 'zip', '97901; OR; Ontario; Malheur County; Adrian'],
     ['99472', 'zip', '97886; OR; Hermiston-Pendleton; Umatilla County; Weston'],
     ['99471', 'zip', '97885; OR; Wallowa County; Wallowa'],
     ['99470', 'zip', '97884; OR; Baker County; Unity'],
     ['99469', 'zip', '97883; OR; La Grande; Union County; Union'],
     ['99468', 'zip', '97882; OR; Hermiston-Pendleton; Umatilla County; Umatilla'],
     ['99467', 'zip', '97880; OR; Hermiston-Pendleton; Umatilla County; Ukiah'],
     ['99466', 'zip', '97877; OR; Baker County; Sumpter'],
     ['99465', 'zip', '97876; OR; La Grande; Union County; Summerville'],
     ['99464',
      'zip',
      '97875; OR; Hermiston-Pendleton; Umatilla County; Stanfield'],
     ['99463', 'zip', '97874; OR; Wheeler County; Spray'],
     ['99461', 'zip', '97870; OR; Baker County; Richland'],
     ['99460', 'zip', '97869; OR; Grant County; Prairie City'],
     ['99459',
      'zip',
      '97868; OR; Hermiston-Pendleton; Umatilla County; Pilot Rock'],
     ['99458', 'zip', '97867; OR; La Grande; Union County; North Powder'],
     ['99457', 'zip', '97865; OR; Grant County; Mount Vernon'],
     ['99456', 'zip', '97864; OR; Grant County; Mount Vernon'],
     ['99455',
      'zip',
      '97862; OR; Hermiston-Pendleton; Umatilla County; Milton Freewater'],
     ['99453',
      'zip',
      '97859; OR; Hermiston-Pendleton; Umatilla County; Pendleton'],
     ['99452', 'zip', '97857; OR; Wallowa County; Lostine'],
     ['99450', 'zip', '97850; OR; La Grande; Union County'],
     ['99449', 'zip', '97848; OR; Grant County; Kimberly'],
     ['99448', 'zip', '97846; OR; Wallowa County; Joseph'],
     ['99447', 'zip', '97845; OR; Grant County; John Day'],
     ['99446', 'zip', '97844; OR; Hermiston-Pendleton; Morrow County; Irrigon'],
     ['99445', 'zip', '97843; OR; Hermiston-Pendleton; Morrow County; Ione'],
     ['99444', 'zip', '97842; OR; Wallowa County; Imnaha'],
     ['99443', 'zip', '97841; OR; La Grande; Union County; Imbler'],
     ['99442', 'zip', '97840; OR; Baker County; Halfway'],
     ['99441', 'zip', '97839; OR; Hermiston-Pendleton; Morrow County; Lexington'],
     ['99440',
      'zip',
      '97838; OR; Hermiston-Pendleton; Umatilla County; Hermiston'],
     ['99439', 'zip', '97837; OR; Baker County; Hereford'],
     ['99438', 'zip', '97836; OR; Hermiston-Pendleton; Morrow County; Heppner'],
     ['99437', 'zip', '97835; OR; Hermiston-Pendleton; Umatilla County; Helix'],
     ['99436', 'zip', '97834; OR; Baker County; Halfway'],
     ['99435', 'zip', '97833; OR; Baker County; Haines'],
     ['99434', 'zip', '97830; OR; Wheeler County; Fossil'],
     ['99433', 'zip', '97828; OR; Wallowa County; Enterprise'],
     ['99432', 'zip', '97827; OR; La Grande; Union County; Elgin'],
     ['99431', 'zip', '97826; OR; Hermiston-Pendleton; Umatilla County; Echo'],
     ['99430', 'zip', '97825; OR; Grant County; Mount Vernon'],
     ['9943', 'city', 'Richville; MN; Fergus Falls; Otter Tail County'],
     ['99429', 'zip', '97824; OR; La Grande; Union County; Cove'],
     ['99428', 'zip', '97823; OR; Gilliam County; Condon'],
     ['99427', 'zip', '97820; OR; Grant County; Canyon City'],
     ['99426', 'zip', '97819; OR; Baker County; Bridgeport'],
     ['99425', 'zip', '97818; OR; Hermiston-Pendleton; Morrow County; Boardman'],
     ['99423', 'zip', '97814; OR; Baker County; Baker City'],
     ['99422', 'zip', '97813; OR; Hermiston-Pendleton; Umatilla County; Athena'],
     ['99421', 'zip', '97812; OR; Gilliam County; Arlington'],
     ['99420', 'zip', '97810; OR; Hermiston-Pendleton; Umatilla County; Adams'],
     ['99419',
      'zip',
      '97801; OR; Hermiston-Pendleton; Umatilla County; Pendleton'],
     ['99417', 'zip', '97760; OR; Jefferson County; Terrebonne'],
     ['99416', 'zip', '97759; OR; Bend-Redmond; Deschutes County; Sisters'],
     ['99414', 'zip', '97756; OR; Bend-Redmond; Deschutes County; Redmond'],
     ['99413', 'zip', '97754; OR; Prineville; Crook County'],
     ['99412', 'zip', '97753; OR; Prineville; Crook County; Powell Butte'],
     ['99411', 'zip', '97752; OR; Prineville; Crook County; Post'],
     ['99410', 'zip', '97751; OR; Prineville; Crook County; Paulina'],
     ['9941', 'city', 'Parnell; IA; Iowa County'],
     ['99409', 'zip', '97750; OR; Wheeler County; Mitchell'],
     ['99408', 'zip', '97741; OR; Jefferson County; Madras'],
     ['99407', 'zip', '97739; OR; Bend-Redmond; Deschutes County; La Pine'],
     ['99406', 'zip', '97738; OR; Harney County; Hines'],
     ['99405', 'zip', '97737; OR; Klamath Falls; Klamath County; Gilchrist'],
     ['99404', 'zip', '97736; OR; Harney County; Frenchglen'],
     ['99402', 'zip', '97734; OR; Jefferson County; Culver'],
     ['99401', 'zip', '97733; OR; Klamath Falls; Klamath County; Crescent Lake'],
     ['99400', 'zip', '97732; OR; Harney County; Crane'],
     ['9940', 'city', 'Palo Cedro; CA; Redding; Shasta County'],
     ['99399', 'zip', '97731; OR; Klamath Falls; Klamath County; Chemult'],
     ['99398', 'zip', '97730; OR; Jefferson County; Camp Sherman'],
     ['99397', 'zip', '97722; OR; Harney County; Diamond'],
     ['99396', 'zip', '97721; OR; Harney County; Princeton'],
     ['99395', 'zip', '97720; OR; Harney County; Burns'],
     ['99394', 'zip', '97712; OR; Bend-Redmond; Deschutes County; Brothers'],
     ['9939', 'city', 'Muscoda; WI; Platteville; Grant County'],
     ['99389', 'zip', '97707; OR; Bend-Redmond; Deschutes County; Bend'],
     ['99388', 'zip', '97702; OR; Bend-Redmond; Deschutes County; Bend'],
     ['99387', 'zip', '97701; OR; Bend-Redmond; Deschutes County; Bend'],
     ['99386', 'zip', '97641; OR; Lake County; Christmas Valley'],
     ['99385', 'zip', '97640; OR; Lake County; Summer Lake'],
     ['99384', 'zip', '97639; OR; Klamath Falls; Klamath County; Sprague River'],
     ['99383', 'zip', '97638; OR; Lake County; Silver Lake'],
     ['99382', 'zip', '97637; OR; Lake County; Plush'],
     ['99381', 'zip', '97636; OR; Lake County; Paisley'],
     ['99380', 'zip', '97635; CA; Modoc County; New Pine Creek'],
     ['99379', 'zip', '97634; OR; Klamath Falls; Klamath County'],
     ['99378', 'zip', '97633; OR; Klamath Falls; Klamath County; Merrill'],
     ['99377', 'zip', '97632; OR; Klamath Falls; Klamath County; Malin'],
     ['99376', 'zip', '97630; OR; Lake County; Lakeview'],
     ['99375', 'zip', '97627; OR; Klamath Falls; Klamath County; Keno'],
     ['99374', 'zip', '97626; OR; Klamath Falls; Klamath County; Fort Klamath'],
     ['99373', 'zip', '97625; OR; Klamath Falls; Klamath County; Dairy'],
     ['99372', 'zip', '97624; OR; Klamath Falls; Klamath County; Chiloquin'],
     ['99371', 'zip', '97623; OR; Klamath Falls; Klamath County; Bonanza'],
     ['99370', 'zip', '97622; OR; Klamath Falls; Klamath County; Bly'],
     ['9937', 'city', 'Mooreton; ND; Wahpeton; Richland County'],
     ['99369', 'zip', '97621; OR; Klamath Falls; Klamath County; Beatty'],
     ['99366', 'zip', '97603; OR; Klamath Falls; Klamath County'],
     ['99364', 'zip', '97601; OR; Klamath Falls; Klamath County'],
     ['99363', 'zip', '97544; OR; Grants Pass; Josephine County; Williams'],
     ['99362', 'zip', '97543; OR; Grants Pass; Josephine County'],
     ['99361', 'zip', '97541; OR; Medford; Jackson County; Trail'],
     ['99360', 'zip', '97540; OR; Medford; Jackson County; Talent'],
     ['9936', 'city', 'Hartford; MI; Kalamazoo-Portage; Van Buren County'],
     ['99359', 'zip', '97539; OR; Medford; Jackson County; Shady Cove'],
     ['99358', 'zip', '97538; OR; Grants Pass; Josephine County; Selma'],
     ['99357', 'zip', '97537; OR; Medford; Jackson County; Rogue River'],
     ['99356', 'zip', '97536; OR; Medford; Jackson County; Prospect'],
     ['99355', 'zip', '97535; OR; Medford; Jackson County; Phoenix'],
     ['99354', 'zip', '97534; OR; Grants Pass; Josephine County; O Brien'],
     ['99352', 'zip', '97532; OR; Grants Pass; Josephine County; Merlin'],
     ['99351', 'zip', '97531; OR; Grants Pass; Josephine County; Cave Junction'],
     ['99350', 'zip', '97530; OR; Medford; Jackson County; Jacksonville'],
     ['99348', 'zip', '97527; OR; Grants Pass; Josephine County'],
     ['99347', 'zip', '97526; OR; Grants Pass; Josephine County'],
     ['99346', 'zip', '97525; OR; Medford; Jackson County; Gold Hill'],
     ['99345', 'zip', '97524; OR; Medford; Jackson County; Eagle Point'],
     ['99344', 'zip', '97523; OR; Grants Pass; Josephine County; Cave Junction'],
     ['99343', 'zip', '97522; OR; Medford; Jackson County; Butte Falls'],
     ['99342', 'zip', '97520; OR; Medford; Jackson County; Ashland'],
     ['99341', 'zip', '97504; OR; Medford; Jackson County'],
     ['99340', 'zip', '97503; OR; Medford; Jackson County; White City'],
     ['99339', 'zip', '97502; OR; Medford; Jackson County; Central Point'],
     ['99338', 'zip', '97501; OR; Medford; Jackson County'],
     ['99337', 'zip', '97499; OR; Roseburg; Douglas County; Yoncalla'],
     ['99336', 'zip', '97498; OR; Newport; Lincoln County; Yachats'],
     ['99335', 'zip', '97497; OR; Grants Pass; Josephine County; Wolf Creek'],
     ['99334', 'zip', '97496; OR; Roseburg; Douglas County; Winston'],
     ['99333', 'zip', '97495; OR; Roseburg; Douglas County; Winchester'],
     ['99331', 'zip', '97493; OR; Eugene; Lane County; Dunes City'],
     ['99330', 'zip', '97492; OR; Eugene; Lane County; Westfir'],
     ['99328', 'zip', '97490; OR; Eugene; Lane County; Walton'],
     ['99327', 'zip', '97489; OR; Eugene; Lane County; Walterville'],
     ['99326', 'zip', '97488; OR; Eugene; Lane County; Vida'],
     ['99325', 'zip', '97487; OR; Eugene; Lane County; Veneta'],
     ['99324', 'zip', '97486; OR; Roseburg; Douglas County; Umpqua'],
     ['99323', 'zip', '97484; OR; Roseburg; Douglas County; Tiller'],
     ['99321', 'zip', '97481; OR; Roseburg; Douglas County; Tenmile'],
     ['99320', 'zip', '97480; OR; Eugene; Lane County; Swisshome'],
     ['9932', 'city', 'Hancock; WI; Waushara County'],
     ['99319', 'zip', '97479; OR; Roseburg; Douglas County; Sutherlin'],
     ['99318', 'zip', '97478; OR; Eugene; Lane County; Springfield'],
     ['99317', 'zip', '97477; OR; Eugene; Lane County; Springfield'],
     ['99316', 'zip', '97476; OR; Brookings; Curry County; Sixes'],
     ['99315', 'zip', '97473; OR; Roseburg; Douglas County; Scottsburg'],
     ['99313', 'zip', '97470; OR; Roseburg; Douglas County'],
     ['99312', 'zip', '97469; OR; Roseburg; Douglas County; Riddle'],
     ['99311', 'zip', '97467; OR; Roseburg; Douglas County; Reedsport'],
     ['99310', 'zip', '97466; OR; Coos Bay; Coos County; Powers'],
     ['9931', 'city', 'Jacumba; CA; San Diego-Carlsbad; San Diego County'],
     ['99309', 'zip', '97465; OR; Brookings; Curry County; Port Orford'],
     ['99307', 'zip', '97463; OR; Eugene; Lane County; Oakridge'],
     ['99306', 'zip', '97462; OR; Roseburg; Douglas County; Oakland'],
     ['99305', 'zip', '97461; OR; Eugene; Lane County; Noti'],
     ['99304', 'zip', '97459; OR; Coos Bay; Coos County; North Bend'],
     ['99303', 'zip', '97458; OR; Coos Bay; Coos County; Myrtle Point'],
     ['99302', 'zip', '97457; OR; Roseburg; Douglas County; Myrtle Creek'],
     ['99301', 'zip', '97456; OR; Corvallis; Benton County; Monroe'],
     ['99300', 'zip', '97455; OR; Eugene; Lane County; Pleasant Hill'],
     ['993', 'county', 'Dimmit County; TX'],
     ['99299', 'zip', '97454; OR; Eugene; Lane County; Marcola'],
     ['99298', 'zip', '97453; OR; Eugene; Lane County; Mapleton'],
     ['99297', 'zip', '97452; OR; Eugene; Lane County; Lowell'],
     ['99296', 'zip', '97451; OR; Eugene; Lane County; Lorane'],
     ['99295', 'zip', '97450; OR; Brookings; Curry County; Langlois'],
     ['99294', 'zip', '97449; OR; Coos Bay; Coos County; Lakeside'],
     ['99293', 'zip', '97448; OR; Eugene; Lane County; Junction City'],
     ['99292', 'zip', '97447; OR; Roseburg; Douglas County; Idleyld Park'],
     ['99291', 'zip', '97446; OR; Albany; Linn County; Harrisburg'],
     ['99290', 'zip', '97444; OR; Brookings; Curry County; Gold Beach'],
     ['99289', 'zip', '97443; OR; Roseburg; Douglas County; Glide'],
     ['99288', 'zip', '97442; OR; Roseburg; Douglas County; Glendale'],
     ['99287', 'zip', '97441; OR; Roseburg; Douglas County; Gardiner'],
     ['99285', 'zip', '97439; OR; Eugene; Lane County; Florence'],
     ['99284', 'zip', '97438; OR; Eugene; Lane County; Fall Creek'],
     ['99283', 'zip', '97437; OR; Eugene; Lane County; Elmira'],
     ['99282', 'zip', '97436; OR; Roseburg; Douglas County; Elkton'],
     ['99281', 'zip', '97435; OR; Roseburg; Douglas County; Drain'],
     ['99280', 'zip', '97434; OR; Eugene; Lane County; Dorena'],
     ['99279', 'zip', '97432; OR; Roseburg; Douglas County; Winston'],
     ['99278', 'zip', '97431; OR; Eugene; Lane County; Dexter'],
     ['99277', 'zip', '97430; OR; Eugene; Lane County; Deadwood'],
     ['99276', 'zip', '97429; OR; Roseburg; Douglas County; Days Creek'],
     ['99273', 'zip', '97426; OR; Eugene; Lane County; Creswell'],
     ['99271', 'zip', '97424; OR; Eugene; Lane County; Cottage Grove'],
     ['99270', 'zip', '97423; OR; Coos Bay; Coos County; Coquille'],
     ['99269', 'zip', '97420; OR; Coos Bay; Coos County'],
     ['99268', 'zip', '97419; OR; Eugene; Lane County; Cheshire'],
     ['99267', 'zip', '97417; OR; Roseburg; Douglas County; Canyonville'],
     ['99266', 'zip', '97416; OR; Roseburg; Douglas County; Camas Valley'],
     ['99265', 'zip', '97415; OR; Brookings; Curry County'],
     ['99264', 'zip', '97414; OR; Coos Bay; Coos County; Broadbent'],
     ['99263', 'zip', '97413; OR; Eugene; Lane County; Blue River'],
     ['99262', 'zip', '97412; OR; Eugene; Lane County; Blachly'],
     ['99261', 'zip', '97411; OR; Coos Bay; Coos County; Bandon'],
     ['99260', 'zip', '97410; OR; Roseburg; Douglas County; Azalea'],
     ['9926', 'neigh', 'Hunter Army Airfield; GA; Savannah; Chatham County'],
     ['99258', 'zip', '97408; OR; Eugene; Lane County'],
     ['99256', 'zip', '97406; OR; Brookings; Curry County; Agness'],
     ['99255', 'zip', '97405; OR; Eugene; Lane County'],
     ['99254', 'zip', '97404; OR; Eugene; Lane County'],
     ['99253', 'zip', '97403; OR; Eugene; Lane County'],
     ['99252', 'zip', '97402; OR; Eugene; Lane County'],
     ['99251', 'zip', '97401; OR; Eugene; Lane County'],
     ['99250',
      'zip',
      '97396; OR; Portland-Vancouver-Hillsboro; Yamhill County; Willamina'],
     ['99249', 'zip', '97394; OR; Newport; Lincoln County; Waldport'],
     ['99248', 'zip', '97392; OR; Salem; Marion County; Turner'],
     ['99247', 'zip', '97391; OR; Newport; Lincoln County; Toledo'],
     ['99246', 'zip', '97390; OR; Newport; Lincoln County; Tidewater'],
     ['99245', 'zip', '97389; OR; Albany; Linn County; Tangent'],
     ['99244', 'zip', '97388; OR; Newport; Lincoln County; Lincoln City'],
     ['99243', 'zip', '97386; OR; Albany; Linn County; Sweet Home'],
     ['99242', 'zip', '97385; OR; Salem; Marion County; Sublimity'],
     ['99240', 'zip', '97383; OR; Salem; Marion County; Stayton'],
     ['99239', 'zip', '97381; OR; Salem; Marion County; Silverton'],
     ['99238', 'zip', '97380; OR; Newport; Lincoln County; Siletz'],
     ['99237',
      'zip',
      '97378; OR; Portland-Vancouver-Hillsboro; Yamhill County; Sheridan'],
     ['99236', 'zip', '97377; OR; Albany; Linn County; Shedd'],
     ['99235', 'zip', '97376; OR; Newport; Lincoln County; Seal Rock'],
     ['99234', 'zip', '97375; OR; Salem; Marion County; Scotts Mills'],
     ['99233', 'zip', '97374; OR; Albany; Linn County; Scio'],
     ['99230', 'zip', '97371; OR; Salem; Polk County; Rickreall'],
     ['99229', 'zip', '97370; OR; Corvallis; Benton County; Philomath'],
     ['99228', 'zip', '97369; OR; Newport; Lincoln County; Otter Rock'],
     ['99227', 'zip', '97368; OR; Newport; Lincoln County; Otis'],
     ['99226', 'zip', '97367; OR; Newport; Lincoln County; Lincoln City'],
     ['99225', 'zip', '97366; OR; Newport; Lincoln County'],
     ['99224', 'zip', '97365; OR; Newport; Lincoln County'],
     ['99223', 'zip', '97364; OR; Newport; Lincoln County; Neotsu'],
     ['99222', 'zip', '97362; OR; Salem; Marion County; Mount Angel'],
     ['99221', 'zip', '97361; OR; Salem; Polk County; Monmouth'],
     ['99220', 'zip', '97360; OR; Albany; Linn County; Mill City'],
     ['9922', 'city', 'Hamburg; PA; Reading; Berks County'],
     ['99219', 'zip', '97358; OR; Albany; Linn County; Lyons'],
     ['99218', 'zip', '97357; OR; Newport; Lincoln County; Logsden'],
     ['99217', 'zip', '97355; OR; Albany; Linn County; Lebanon'],
     ['99216', 'zip', '97352; OR; Salem; Marion County; Jefferson'],
     ['99215', 'zip', '97351; OR; Salem; Polk County; Independence'],
     ['99214', 'zip', '97350; OR; Albany; Linn County; Idanha'],
     ['99213', 'zip', '97348; OR; Albany; Linn County; Halsey'],
     ['99212', 'zip', '97347; OR; Salem; Polk County; Grand Ronde'],
     ['99211', 'zip', '97346; OR; Salem; Marion County; Gates'],
     ['99210', 'zip', '97345; OR; Albany; Linn County; Foster'],
     ['9921', 'city', 'Plumerville; AR; Conway County'],
     ['99209', 'zip', '97344; OR; Salem; Polk County; Falls City'],
     ['99208', 'zip', '97343; OR; Newport; Lincoln County; Eddyville'],
     ['99207', 'zip', '97342; OR; Salem; Marion County; Detroit'],
     ['99206', 'zip', '97341; OR; Newport; Lincoln County; Depoe Bay'],
     ['99204', 'zip', '97338; OR; Salem; Polk County; Dallas'],
     ['99203', 'zip', '97336; OR; Albany; Linn County; Sweet Home'],
     ['99202', 'zip', '97335; OR; Albany; Linn County; Scio'],
     ['99201', 'zip', '97333; OR; Corvallis; Benton County'],
     ['992', 'county', 'Dillon County; SC'],
     ['99199', 'zip', '97330; OR; Corvallis; Benton County'],
     ['99198', 'zip', '97329; OR; Albany; Linn County; Cascadia'],
     ['99197', 'zip', '97327; OR; Albany; Linn County; Brownsville'],
     ['99196', 'zip', '97326; OR; Corvallis; Benton County; Blodgett'],
     ['99195', 'zip', '97325; OR; Salem; Marion County; Aumsville'],
     ['99194', 'zip', '97324; OR; Corvallis; Benton County; Alsea'],
     ['99193', 'zip', '97322; OR; Albany; Linn County'],
     ['99192', 'zip', '97321; OR; Albany; Linn County'],
     ['9919', 'city', 'Franklin; AL; Macon County'],
     ['99183', 'zip', '97306; OR; Salem; Marion County'],
     ['99182', 'zip', '97305; OR; Salem; Marion County; Hayesville'],
     ['99181', 'zip', '97304; OR; Salem; Marion County'],
     ['99180', 'zip', '97303; OR; Salem; Marion County; Keizer'],
     ['99179', 'zip', '97302; OR; Salem; Marion County'],
     ['99178', 'zip', '97301; OR; Salem; Marion County'],
     ['99160', 'zip', '97267'],
     ['99159', 'zip', '97266'],
     ['9915', 'city', 'Melvern; KS; Topeka; Osage County'],
     ['99149', 'zip', '97239'],
     ['99147', 'zip', '97236'],
     ['99146', 'zip', '97233'],
     ['99145',
      'zip',
      '97232; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99144',
      'zip',
      '97231; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99143',
      'zip',
      '97230; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99142', 'zip', '97229'],
     ['99140',
      'zip',
      '97227; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99139', 'zip', '97225'],
     ['99138', 'zip', '97224'],
     ['99137', 'zip', '97223'],
     ['99136', 'zip', '97222'],
     ['99135',
      'zip',
      '97221; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99134', 'zip', '97220'],
     ['99133', 'zip', '97219'],
     ['99132',
      'zip',
      '97218; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99131', 'zip', '97217'],
     ['99130', 'zip', '97216'],
     ['99129',
      'zip',
      '97215; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99128', 'zip', '97214'],
     ['99127', 'zip', '97213'],
     ['99126', 'zip', '97212'],
     ['99125', 'zip', '97211'],
     ['99124', 'zip', '97210'],
     ['99123', 'zip', '97209'],
     ['99120', 'zip', '97206'],
     ['99119',
      'zip',
      '97205; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99118',
      'zip',
      '97204; OR; Portland-Vancouver-Hillsboro; Multnomah County; Portland'],
     ['99117', 'zip', '97203'],
     ['99116', 'zip', '97202'],
     ['99115', 'zip', '97201'],
     ['99114', 'zip', '97149; OR; Tillamook County; Neskowin'],
     ['99113',
      'zip',
      '97148; OR; Portland-Vancouver-Hillsboro; Yamhill County; Yamhill'],
     ['99112', 'zip', '97147; OR; Tillamook County; Wheeler'],
     ['99111', 'zip', '97146; OR; Astoria; Clatsop County; Warrenton'],
     ['9911', 'city', 'Nachusa; IL; Dixon; Lee County'],
     ['99109',
      'zip',
      '97144; OR; Portland-Vancouver-Hillsboro; Washington County; Timber'],
     ['99108', 'zip', '97143; OR; Tillamook County; Tillamook'],
     ['99107', 'zip', '97141; OR; Tillamook County; Tillamook'],
     ['99106', 'zip', '97140'],
     ['99105', 'zip', '97138; OR; Astoria; Clatsop County; Seaside'],
     ['99104', 'zip', '97137; OR; Salem; Marion County; Saint Paul'],
     ['99103', 'zip', '97136; OR; Tillamook County; Rockaway Beach'],
     ['99102', 'zip', '97135; OR; Tillamook County; Cloverdale'],
     ['99101', 'zip', '97134; OR; Tillamook County; Tillamook'],
     ['99100',
      'zip',
      '97133; OR; Portland-Vancouver-Hillsboro; Washington County; North Plains'],
     ['99099', 'zip', '97132'],
     ['99098', 'zip', '97131; OR; Tillamook County; Nehalem'],
     ['99097', 'zip', '97130; OR; Tillamook County; Nehalem'],
     ['99096',
      'zip',
      '97128; OR; Portland-Vancouver-Hillsboro; Yamhill County; McMinnville'],
     ['99095',
      'zip',
      '97127; OR; Portland-Vancouver-Hillsboro; Yamhill County; Lafayette'],
     ['99094',
      'zip',
      '97125; OR; Portland-Vancouver-Hillsboro; Washington County; Banks'],
     ['99093', 'zip', '97124'],
     ['99092',
      'zip',
      '97123; OR; Portland-Vancouver-Hillsboro; Washington County; Hillsboro'],
     ['99091', 'zip', '97122; OR; Tillamook County; Hebo'],
     ['99089',
      'zip',
      '97119; OR; Portland-Vancouver-Hillsboro; Washington County; Gaston'],
     ['99088', 'zip', '97118; OR; Tillamook County; Garibaldi'],
     ['99087',
      'zip',
      '97117; OR; Portland-Vancouver-Hillsboro; Washington County; Gales Creek'],
     ['99086',
      'zip',
      '97116; OR; Portland-Vancouver-Hillsboro; Washington County; Forest Grove'],
     ['99085',
      'zip',
      '97115; OR; Portland-Vancouver-Hillsboro; Yamhill County; Dundee'],
     ['99084',
      'zip',
      '97114; OR; Portland-Vancouver-Hillsboro; Yamhill County; Dayton'],
     ['99083',
      'zip',
      '97113; OR; Portland-Vancouver-Hillsboro; Washington County; Cornelius'],
     ['99082', 'zip', '97112; OR; Tillamook County; Cloverdale'],
     ['99081',
      'zip',
      '97111; OR; Portland-Vancouver-Hillsboro; Yamhill County; Carlton'],
     ['99080', 'zip', '97110; OR; Astoria; Clatsop County; Cannon Beach'],
     ['9908', 'city', 'Gleason; WI; Merrill; Lincoln County'],
     ['99079',
      'zip',
      '97109; OR; Portland-Vancouver-Hillsboro; Washington County; Buxton'],
     ['99078', 'zip', '97108; OR; Tillamook County; Beaver'],
     ['99077', 'zip', '97107; OR; Tillamook County; Bay City'],
     ['99076',
      'zip',
      '97106; OR; Portland-Vancouver-Hillsboro; Washington County; Banks'],
     ['99075', 'zip', '97103; OR; Astoria; Clatsop County'],
     ['99074', 'zip', '97102; OR; Astoria; Clatsop County; Arch Cape'],
     ['99073',
      'zip',
      '97101; OR; Portland-Vancouver-Hillsboro; Yamhill County; Amity'],
     ['99072', 'zip', '97080'],
     ['99071', 'zip', '97078'],
     ['9907', 'city', 'Gilbert; WV; Mingo County'],
     ['99067', 'zip', '97071; OR; Salem; Marion County; Woodburn'],
     ['99066',
      'zip',
      '97070; OR; Portland-Vancouver-Hillsboro; Clackamas County; Wilsonville'],
     ['99065', 'zip', '97068'],
     ['99064',
      'zip',
      '97067; OR; Portland-Vancouver-Hillsboro; Clackamas County; Welches'],
     ['99063', 'zip', '97065; OR; Sherman County; Wasco'],
     ['99062',
      'zip',
      '97064; OR; Portland-Vancouver-Hillsboro; Columbia County; Vernonia'],
     ['99061', 'zip', '97063; OR; The Dalles; Wasco County; Tygh Valley'],
     ['99060',
      'zip',
      '97062; OR; Portland-Vancouver-Hillsboro; Washington County; Tualatin'],
     ['9906', 'city', 'Fulton; KS; Bourbon County'],
     ['99059',
      'zip',
      '97060; OR; Portland-Vancouver-Hillsboro; Multnomah County; Troutdale'],
     ['99058', 'zip', '97058; OR; The Dalles; Wasco County'],
     ['99057', 'zip', '97057; OR; The Dalles; Wasco County; Shaniko'],
     ['99056',
      'zip',
      '97056; OR; Portland-Vancouver-Hillsboro; Columbia County; Scappoose'],
     ['99055',
      'zip',
      '97055; OR; Portland-Vancouver-Hillsboro; Clackamas County; Sandy'],
     ['99054',
      'zip',
      '97054; OR; Portland-Vancouver-Hillsboro; Columbia County; Deer Island'],
     ['99053',
      'zip',
      '97053; OR; Portland-Vancouver-Hillsboro; Columbia County; Warren'],
     ['99052',
      'zip',
      '97051; OR; Portland-Vancouver-Hillsboro; Columbia County; Saint Helens'],
     ['99051', 'zip', '97050; OR; Sherman County; Rufus'],
     ['99050',
      'zip',
      '97049; OR; Portland-Vancouver-Hillsboro; Clackamas County; Rhododendron'],
     ['99049',
      'zip',
      '97048; OR; Portland-Vancouver-Hillsboro; Columbia County; Rainier'],
     ['99048', 'zip', '97045'],
     ['99046',
      'zip',
      '97042; OR; Portland-Vancouver-Hillsboro; Clackamas County; Mulino'],
     ['99045',
      'zip',
      '97041; OR; Hood River; Hood River County; Mount Hood Parkdale'],
     ['99044', 'zip', '97040; OR; The Dalles; Wasco County; Mosier'],
     ['99043', 'zip', '97039; OR; Sherman County; Moro'],
     ['99042',
      'zip',
      '97038; OR; Portland-Vancouver-Hillsboro; Clackamas County; Molalla'],
     ['99041', 'zip', '97037; OR; The Dalles; Wasco County; Maupin'],
     ['9904', 'city', 'Fort Hall; ID; Pocatello; Bannock County'],
     ['99039',
      'zip',
      '97035; OR; Portland-Vancouver-Hillsboro; Clackamas County; Lake Oswego'],
     ['99038', 'zip', '97034'],
     ['99036', 'zip', '97032; OR; Salem; Marion County; Hubbard'],
     ['99035', 'zip', '97031; OR; Hood River; Hood River County'],
     ['99034', 'zip', '97030'],
     ['99032',
      'zip',
      '97028; OR; Portland-Vancouver-Hillsboro; Clackamas County; Rhododendron'],
     ['99031',
      'zip',
      '97027; OR; Portland-Vancouver-Hillsboro; Clackamas County; Gladstone'],
     ['99030', 'zip', '97026; OR; Salem; Marion County; Gervais'],
     ['9903', 'city', 'Floydada; TX; Floyd County'],
     ['99029',
      'zip',
      '97024; OR; Portland-Vancouver-Hillsboro; Multnomah County; Fairview'],
     ['99028',
      'zip',
      '97023; OR; Portland-Vancouver-Hillsboro; Clackamas County; Estacada'],
     ['99027',
      'zip',
      '97022; OR; Portland-Vancouver-Hillsboro; Clackamas County; Eagle Creek'],
     ['99026', 'zip', '97021; OR; The Dalles; Wasco County; Dufur'],
     ['99025', 'zip', '97020; OR; Salem; Marion County; Donald'],
     ['99024',
      'zip',
      '97019; OR; Portland-Vancouver-Hillsboro; Multnomah County; Corbett'],
     ['99023',
      'zip',
      '97018; OR; Portland-Vancouver-Hillsboro; Columbia County; Columbia City'],
     ['99022',
      'zip',
      '97017; OR; Portland-Vancouver-Hillsboro; Clackamas County; Colton'],
     ['99021',
      'zip',
      '97016; OR; Portland-Vancouver-Hillsboro; Columbia County; Clatskanie'],
     ['99020',
      'zip',
      '97015; OR; Portland-Vancouver-Hillsboro; Clackamas County; Clackamas'],
     ['9902', 'city', 'Peck; KS; Wichita; Sumner County'],
     ['99019', 'zip', '97014; OR; Hood River; Hood River County; Cascade Locks'],
     ['99018',
      'zip',
      '97013; OR; Portland-Vancouver-Hillsboro; Clackamas County; Canby'],
     ['99017',
      'zip',
      '97011; OR; Portland-Vancouver-Hillsboro; Clackamas County; Brightwood'],
     ['99015',
      'zip',
      '97009; OR; Portland-Vancouver-Hillsboro; Clackamas County; Boring'],
     ['99014', 'zip', '97008'],
     ['99013', 'zip', '97007'],
     ['99012', 'zip', '97006'],
     ['99011', 'zip', '97005'],
     ['99010',
      'zip',
      '97004; OR; Portland-Vancouver-Hillsboro; Clackamas County; Beavercreek'],
     ['99009', 'zip', '97002; OR; Salem; Marion County; Aurora'],
     ['99008', 'zip', '97001; OR; The Dalles; Wasco County; Antelope'],
     ['990', 'county', 'Dewitt County; IL; Bloomington'],
     ['99', 'county', 'Bent County; CO'],
     ['9896', 'city', 'Fincastle; VA; Roanoke; Botetourt County'],
     ['98957', 'zip', '96826'],
     ['98956', 'zip', '96825; HI; Urban Honolulu; Honolulu County; Honolulu'],
     ['98953', 'zip', '96822'],
     ['98952', 'zip', '96821; HI; Urban Honolulu; Honolulu County; Honolulu'],
     ['98950', 'zip', '96819; HI; Urban Honolulu; Honolulu County; Honolulu'],
     ['98949', 'zip', '96818'],
     ['98948', 'zip', '96817'],
     ['98947', 'zip', '96816'],
     ['98946', 'zip', '96815'],
     ['98945', 'zip', '96814'],
     ['98944', 'zip', '96813'],
     ['98930', 'zip', '96797'],
     ['98929', 'zip', '96796; HI; Kapaa; Kauai County; Waimea'],
     ['98928', 'zip', '96795; HI; Urban Honolulu; Honolulu County; Waimanalo'],
     ['98927', 'zip', '96793; HI; Kahului-Wailuku-Lahaina; Maui County; Wailuku'],
     ['98926', 'zip', '96792'],
     ['98925', 'zip', '96791; HI; Urban Honolulu; Honolulu County; Waialua'],
     ['98924', 'zip', '96790; HI; Kahului-Wailuku-Lahaina; Maui County; Kihei'],
     ['98923', 'zip', '96789'],
     ['98921', 'zip', '96786'],
     ['98920', 'zip', '96785; HI; Hilo; Hawaii County; Volcano'],
     ['98918', 'zip', '96783; HI; Hilo; Hawaii County; Pepeekeo'],
     ['98917', 'zip', '96782; HI; Urban Honolulu; Honolulu County; Pearl City'],
     ['98916', 'zip', '96781; HI; Hilo; Hawaii County; Papaikou'],
     ['98915', 'zip', '96780; HI; Hilo; Hawaii County; Ninole'],
     ['98914', 'zip', '96779; HI; Kahului-Wailuku-Lahaina; Maui County; Paia'],
     ['98913', 'zip', '96778; HI; Hilo; Hawaii County; Pahoa'],
     ['98912', 'zip', '96777; HI; Hilo; Hawaii County; Pahala'],
     ['98911', 'zip', '96776; HI; Hilo; Hawaii County; Paauilo'],
     ['98910', 'zip', '96774; HI; Hilo; Hawaii County; Ookala'],
     ['9891', 'city', 'Moriarty; NM; Albuquerque; Torrance County'],
     ['98909', 'zip', '96773; HI; Hilo; Hawaii County; Ninole'],
     ['98908', 'zip', '96772; HI; Hilo; Hawaii County; Naalehu'],
     ['98907', 'zip', '96771; HI; Hilo; Hawaii County; Mountain View'],
     ['98906', 'zip', '96770; HI; Kahului-Wailuku-Lahaina; Maui County; Maunaloa'],
     ['98904', 'zip', '96768; HI; Kahului-Wailuku-Lahaina; Maui County; Makawao'],
     ['98902', 'zip', '96766; HI; Kapaa; Kauai County; Lihue'],
     ['98901', 'zip', '96765; HI; Kapaa; Kauai County; Koloa'],
     ['98900', 'zip', '96764; HI; Hilo; Hawaii County; Laupahoehoe'],
     ['989', 'county', 'Denver County; CO; Denver-Aurora-Lakewood'],
     ['98899',
      'zip',
      '96763; HI; Kahului-Wailuku-Lahaina; Maui County; Lanai City'],
     ['98898', 'zip', '96762; HI; Urban Honolulu; Honolulu County; Laie'],
     ['98897', 'zip', '96761; HI; Kahului-Wailuku-Lahaina; Maui County; Lahaina'],
     ['98896', 'zip', '96760; HI; Hilo; Hawaii County; Keaau'],
     ['98894',
      'zip',
      '96757; HI; Kahului-Wailuku-Lahaina; Maui County; Kaunakakai'],
     ['98893', 'zip', '96756; HI; Kapaa; Kauai County; Koloa'],
     ['98892', 'zip', '96755; HI; Hilo; Hawaii County; Kapaau'],
     ['98891', 'zip', '96754; HI; Kapaa; Kauai County; Kilauea'],
     ['98890', 'zip', '96753; HI; Kahului-Wailuku-Lahaina; Maui County; Kihei'],
     ['9889', 'city', 'Ducor; CA; Visalia-Porterville; Tulare County'],
     ['98889', 'zip', '96752; HI; Kapaa; Kauai County; Waimea'],
     ['98888', 'zip', '96751; HI; Kapaa; Kauai County'],
     ['98887', 'zip', '96750; HI; Hilo; Hawaii County; Kealakekua'],
     ['98886', 'zip', '96749; HI; Hilo; Hawaii County; Keaau'],
     ['98885',
      'zip',
      '96748; HI; Kahului-Wailuku-Lahaina; Maui County; Kaunakakai'],
     ['98883', 'zip', '96746; HI; Kapaa; Kauai County'],
     ['98881', 'zip', '96744'],
     ['98880', 'zip', '96743; HI; Hilo; Hawaii County; Waimea'],
     ['98878', 'zip', '96741; HI; Kapaa; Kauai County; Kalaheo'],
     ['98877', 'zip', '96740; HI; Hilo; Hawaii County; Kailua Kona'],
     ['98875', 'zip', '96738; HI; Hilo; Hawaii County; Waikoloa'],
     ['98874', 'zip', '96737; HI; Hilo; Hawaii County; Ocean View'],
     ['98873', 'zip', '96734'],
     ['98871', 'zip', '96732; HI; Kahului-Wailuku-Lahaina; Maui County; Kahului'],
     ['98870', 'zip', '96731; HI; Urban Honolulu; Honolulu County; Kahuku'],
     ['9887', 'city', 'Doland; SD; Spink County'],
     ['98869', 'zip', '96730; HI; Urban Honolulu; Honolulu County; Kaaawa'],
     ['98868', 'zip', '96729; HI; Kahului-Wailuku-Lahaina; Maui County; Hoolehua'],
     ['98867', 'zip', '96728; HI; Hilo; Hawaii County; Honomu'],
     ['98866', 'zip', '96727; HI; Hilo; Hawaii County; Honokaa'],
     ['98864', 'zip', '96725; HI; Hilo; Hawaii County; Holualoa'],
     ['98863', 'zip', '96722; HI; Kapaa; Kauai County; Princeville'],
     ['98861', 'zip', '96720; HI; Hilo; Hawaii County'],
     ['98860', 'zip', '96719; HI; Hilo; Hawaii County; Kapaau'],
     ['98859', 'zip', '96718; HI; Hilo; Hawaii County; Volcano'],
     ['98858', 'zip', '96717; HI; Urban Honolulu; Honolulu County; Hauula'],
     ['98857', 'zip', '96716; HI; Kapaa; Kauai County; Hanapepe'],
     ['98855', 'zip', '96714; HI; Kapaa; Kauai County; Kilauea'],
     ['98854', 'zip', '96713; HI; Kahului-Wailuku-Lahaina; Maui County; Hana'],
     ['98853', 'zip', '96712; HI; Urban Honolulu; Honolulu County; Haleiwa'],
     ['98852', 'zip', '96710; HI; Hilo; Hawaii County; Honomu'],
     ['98850', 'zip', '96708; HI; Kahului-Wailuku-Lahaina; Maui County; Haiku'],
     ['98849', 'zip', '96707'],
     ['98848', 'zip', '96706'],
     ['98847', 'zip', '96705; HI; Kapaa; Kauai County; Hanapepe'],
     ['98846', 'zip', '96704; HI; Hilo; Hawaii County; Captain Cook'],
     ['98845', 'zip', '96703; HI; Kapaa; Kauai County; Kilauea'],
     ['98844', 'zip', '96701'],
     ['9882', 'city', 'Everton; AR; Harrison; Boone County'],
     ['9880',
      'city',
      'Eure; NC; Virginia Beach-Norfolk-Newport News; Gates County'],
     ['988', 'county', 'Denton County; TX; Dallas-Fort Worth-Arlington'],
     ['9878', 'city', 'Elk Creek; CA; Glenn County'],
     ['9876', 'city', 'Eastpoint; FL; Franklin County'],
     ['9875', 'city', 'Osmond; NE; Norfolk; Pierce County'],
     ['9872', 'city', 'Onley; VA; Accomack County'],
     ['9870', 'city', 'Oakfield; ME; Aroostook County'],
     ['98673', 'zip', '96162; CA; Truckee-Grass Valley; Nevada County; Truckee'],
     ['98672', 'zip', '96161; CA; Truckee-Grass Valley; Nevada County; Truckee'],
     ['9867', 'city', 'Ellis; ID; Custer County'],
     ['98663', 'zip', '96150'],
     ['98662',
      'zip',
      '96148; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe Vista'],
     ['98661',
      'zip',
      '96146; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe City'],
     ['98660',
      'zip',
      '96145; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Tahoe City'],
     ['9866', 'city', 'Edisto Beach; SC; Colleton County'],
     ['98659',
      'zip',
      '96143; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Kings Beach'],
     ['98658',
      'zip',
      '96142; CA; Sacramento--Roseville--Arden-Arcade; El Dorado County; Tahoma'],
     ['98657',
      'zip',
      '96141; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Homewood'],
     ['98656',
      'zip',
      '96140; CA; Sacramento--Roseville--Arden-Arcade; Placer County; Carnelian Bay'],
     ['98655', 'zip', '96137; CA; Plumas County; Westwood'],
     ['98654', 'zip', '96136; CA; Susanville; Lassen County; Wendel'],
     ['98653', 'zip', '96135; CA; Plumas County; Chilcoot'],
     ['98652', 'zip', '96134; CA; Modoc County; Tulelake'],
     ['98651', 'zip', '96133; CA; Mono County; Coleville'],
     ['98650', 'zip', '96132; CA; Susanville; Lassen County; Termo'],
     ['98649', 'zip', '96130; CA; Susanville; Lassen County'],
     ['98648', 'zip', '96129; CA; Plumas County; Beckwourth'],
     ['98647', 'zip', '96128; CA; Susanville; Lassen County; Standish'],
     ['98646', 'zip', '96127; CA; Susanville; Lassen County; Janesville'],
     ['98645', 'zip', '96126; CA; Sierra County; Sierraville'],
     ['98644', 'zip', '96125; CA; Sierra County; Sierra City'],
     ['98643', 'zip', '96124; CA; Sierra County; Calpine'],
     ['98642', 'zip', '96123; CA; Susanville; Lassen County; Ravendale'],
     ['98641', 'zip', '96122; CA; Plumas County; Portola'],
     ['98640', 'zip', '96121; CA; Susanville; Lassen County; Milford'],
     ['9864', 'city', 'Lake Quivira; KS; Kansas City; Johnson County'],
     ['98639', 'zip', '96120; CA; Alpine County; Kirkwood'],
     ['98638', 'zip', '96119; CA; Susanville; Lassen County; Madeline'],
     ['98637', 'zip', '96118; CA; Sierra County; Loyalton'],
     ['98636', 'zip', '96117; CA; Susanville; Lassen County; Litchfield'],
     ['98635', 'zip', '96116; CA; Modoc County; Likely'],
     ['98634', 'zip', '96115; CA; Modoc County; Lake City'],
     ['98633', 'zip', '96114; CA; Susanville; Lassen County; Janesville'],
     ['98632', 'zip', '96113; CA; Susanville; Lassen County; Doyle'],
     ['98631', 'zip', '96112; CA; Modoc County; Fort Bidwell'],
     ['98630', 'zip', '96111; CA; Truckee-Grass Valley; Nevada County; Truckee'],
     ['98629', 'zip', '96110; CA; Modoc County; Cedarville'],
     ['98628', 'zip', '96109; CA; Susanville; Lassen County; Doyle'],
     ['98627', 'zip', '96108; CA; Modoc County; New Pine Creek'],
     ['98626', 'zip', '96107; CA; Mono County; Coleville'],
     ['98625', 'zip', '96106; CA; Plumas County; Clio'],
     ['98624', 'zip', '96105; CA; Plumas County; Chilcoot'],
     ['98623', 'zip', '96104; CA; Modoc County; Cedarville'],
     ['98622', 'zip', '96103; CA; Plumas County; Graeagle'],
     ['98621', 'zip', '96101; CA; Modoc County; Alturas'],
     ['98619', 'zip', '96097; CA; Siskiyou County; Yreka'],
     ['98618', 'zip', '96096; CA; Redding; Shasta County; Whitmore'],
     ['98616', 'zip', '96094; CA; Siskiyou County; Weed'],
     ['98615', 'zip', '96093; CA; Trinity County; Weaverville'],
     ['98614', 'zip', '96092; CA; Chico; Butte County'],
     ['98613', 'zip', '96091; CA; Trinity County; Trinity Center'],
     ['98612', 'zip', '96090; CA; Red Bluff; Tehama County; Tehama'],
     ['98611', 'zip', '96089; CA; Redding; Shasta County; Shasta Lake'],
     ['98610', 'zip', '96088; CA; Redding; Shasta County; Shingletown'],
     ['98609', 'zip', '96087; CA; Redding; Shasta County'],
     ['98608', 'zip', '96086; CA; Siskiyou County; Seiad Valley'],
     ['98607', 'zip', '96085; CA; Siskiyou County; Klamath River'],
     ['98606', 'zip', '96084; CA; Redding; Shasta County; Round Mountain'],
     ['98605', 'zip', '96080; CA; Red Bluff; Tehama County'],
     ['98602', 'zip', '96076; CA; Trinity County; Hayfork'],
     ['98601', 'zip', '96075; CA; Red Bluff; Tehama County; Paynes Creek'],
     ['9860', 'city', 'Frontenac; KS; Pittsburg; Crawford County'],
     ['986', 'county', 'Dekalb County; TN'],
     ['98599', 'zip', '96073; CA; Redding; Shasta County; Palo Cedro'],
     ['98598', 'zip', '96071; CA; Redding; Shasta County; Old Station'],
     ...]




```python
columns = ['region_id', 'region_type', 'region']

reg_df = pd.DataFrame(reg, columns=columns)
```


```python
reg_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>region_id</th>
      <th>region_type</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>99999</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>1</th>
      <td>99998</td>
      <td>zip</td>
      <td>98846; WA; Okanogan County; Pateros</td>
    </tr>
    <tr>
      <th>2</th>
      <td>99997</td>
      <td>zip</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
    </tr>
    <tr>
      <th>3</th>
      <td>99996</td>
      <td>zip</td>
      <td>98844; WA; Okanogan County; Oroville</td>
    </tr>
    <tr>
      <th>4</th>
      <td>99995</td>
      <td>zip</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
  </tbody>
</table>
</div>




```python
reg_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10000 entries, 0 to 9999
    Data columns (total 3 columns):
     #   Column       Non-Null Count  Dtype 
    ---  ------       --------------  ----- 
     0   region_id    10000 non-null  object
     1   region_type  10000 non-null  object
     2   region       10000 non-null  object
    dtypes: object(3)
    memory usage: 234.5+ KB



```python
#import table 2

api_url = "https://www.quandl.com/api/v3/datatables/ZILLOW/INDICATORS/?api_key=8zYqUX8TEUYYtbCkcPHq"

response2 = requests.get(api_url)
```


```python
data2 = response2.json()
data2
```




    {'datatable': {'data': [['ZSFH',
        'ZHVI Single-Family Homes Time Series ($)',
        'Home values'],
       ['ZCON', 'ZHVI Condo/Co-op Time Series ($)', 'Home values'],
       ['ZATT', 'ZHVI All Homes- Top Tier Time Series ($)', 'Home values'],
       ['ZALL',
        'ZHVI All Homes (SFR, Condo/Co-op) Time Series ($)',
        'Home values'],
       ['ZABT', 'ZHVI All Homes- Bottom Tier Time Series ($)', 'Home values'],
       ['Z5BR', 'ZHVI 5+ Bedroom Time Series ($)', 'Home values'],
       ['Z4BR', 'ZHVI 4-Bedroom Time Series ($)', 'Home values'],
       ['Z3BR', 'ZHVI 3-Bedroom Time Series ($)', 'Home values'],
       ['Z2BR', 'ZHVI 2-Bedroom Time Series ($)', 'Home values'],
       ['Z1BR', 'ZHVI 1-Bedroom Time Series ($)', 'Home values'],
       ['SSSW',
        'Median Sale Price (Smooth, SFR only, Weekly View)',
        'Inventory and sales'],
       ['SSSM',
        'Median Sale Price (Smooth, SFR only, Monthly)',
        'Inventory and sales'],
       ['SSAW',
        'Median Sale Price (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['SSAM',
        'Median Sale Price (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['SRSW',
        'Median Sale Price (Raw, SFR only, Weekly View)',
        'Inventory and sales'],
       ['SRSM',
        'Median Sale Price (Raw, SFR only, Monthly)',
        'Inventory and sales'],
       ['SRAW',
        'Median Sale Price (Raw, All Homes, Weekly View)',
        'Inventory and sales'],
       ['SRAM',
        'Median Sale Price (Raw, All Homes, Monthly)',
        'Inventory and sales'],
       ['SASW',
        'Median Sale Price (Smooth & Seasonally Adjusted, SFR only, Weekly View)',
        'Inventory and sales'],
       ['SASM',
        'Median Sale Price (Smooth & Seasonally Adjusted, SFR only, Monthly)',
        'Inventory and sales'],
       ['SAAW',
        'Median Sale Price (Smooth & Seasonally Adjusted, All Homes, Weekly View)',
        'Inventory and sales'],
       ['SAAM',
        'Median Sale Price (Smooth & Seasonally Adjusted, All Homes, Monthly)',
        'Inventory and sales'],
       ['RSSA',
        'ZORI (Smoothed, Seasonally Adjusted): All Homes Plus Multifamily Time Series ($)',
        'Rentals'],
       ['RSNA',
        'ZORI (Smoothed): All Homes Plus Multifamily Time Series ($)',
        'Rentals'],
       ['NSAW',
        'Median Days to Pending (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['NSAM',
        'Median Days to Pending (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['NRAW',
        'Median Days to Pending (Raw, All Homes, Weekly View)',
        'Inventory and sales'],
       ['NRAM',
        'Median Days to Pending (Raw, All Homes, Monthly)',
        'Inventory and sales'],
       ['MSAW',
        'Mean Days to Pending (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['MSAM',
        'Mean Days to Pending (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['MRAW',
        'Mean Days to Pending (Raw, All Homes, Weekly View) ',
        'Inventory and sales'],
       ['MRAM',
        'Mean Days to Pending (Raw, All Homes, Monthly)',
        'Inventory and sales'],
       ['LSSW',
        'Median List Price (Smooth, SFR Only, Weekly View)',
        'Inventory and sales'],
       ['LSSM',
        'Median List Price (Smooth, SFR Only, Monthly)',
        'Inventory and sales'],
       ['LSAW',
        'Median List Price (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['LSAM',
        'Median List Price (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['LRSW',
        'Median List Price (Raw, SFR Only, Weekly View)',
        'Inventory and sales'],
       ['LRSM',
        'Median List Price (Raw, SFR Only, Monthly)',
        'Inventory and sales'],
       ['LRAW',
        'Median List Price (Raw, All Homes, Weekly View)',
        'Inventory and sales'],
       ['LRAM',
        'Median List Price (Raw, All Homes, Monthly)',
        'Inventory and sales'],
       ['ISSW',
        'For-Sale Inventory (Smooth, SFR only, Weekly View)',
        'Inventory and sales'],
       ['ISSM',
        'For-Sale Inventory (Smooth, SFR only, Monthly)',
        'Inventory and sales'],
       ['ISAW',
        'For-Sale Inventory (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['ISAM',
        'For-Sale Inventory (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['IRSW',
        'For-sale Inventory (Raw, SFR only, Weekly View)',
        'Inventory and sales'],
       ['IRSM',
        'For-sale Inventory (Raw, SFR only, Monthly)',
        'Inventory and sales'],
       ['IRAW',
        'For-Sale Inventory (Raw, All Homes, Weekly View)',
        'Inventory and sales'],
       ['IRAM',
        'For-Sale Inventory (Raw, All Homes, Monthly)',
        'Inventory and sales'],
       ['CSSW',
        'Share of Listings With a Price Cut (Smooth, SFR Only, Weekly View)',
        'Inventory and sales'],
       ['CSSM',
        'Share of Listings With a Price Cut (Smooth, SFR Only, Monthly)',
        'Inventory and sales'],
       ['CSAW',
        'Share of Listings With a Price Cut (Smooth, All Homes, Weekly View)',
        'Inventory and sales'],
       ['CSAM',
        'Share of Listings With a Price Cut (Smooth, All Homes, Monthly)',
        'Inventory and sales'],
       ['CRSW',
        'Share of Listings With a Price Cut (Raw, SFR Only, Weekly View)',
        'Inventory and sales'],
       ['CRSM',
        'Share of Listings With a Price Cut (Raw, SFR Only, Monthly)',
        'Inventory and sales'],
       ['CRAW',
        'Share of Listings With a Price Cut (Raw, All Homes, Weekly View)',
        'Inventory and sales'],
       ['CRAM',
        'Share of Listings With a Price Cut (Raw, All Homes, Monthly)',
        'Inventory and sales']],
      'columns': [{'name': 'indicator_id', 'type': 'text'},
       {'name': 'indicator', 'type': 'text'},
       {'name': 'category', 'type': 'text'}]},
     'meta': {'next_cursor_id': None}}




```python
indicators = pd.DataFrame(data2)
indicators
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datatable</th>
      <th>meta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>data</th>
      <td>[[ZSFH, ZHVI Single-Family Homes Time Series (...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>columns</th>
      <td>[{'name': 'indicator_id', 'type': 'text'}, {'n...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>next_cursor_id</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
ind = indicators['datatable']['data']

columns = ['indicator_id', 'indicator', 'category']

ind_df = pd.DataFrame(ind, columns=columns)

ind_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>indicator</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZCON</td>
      <td>ZHVI Condo/Co-op Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZATT</td>
      <td>ZHVI All Homes- Top Tier Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZALL</td>
      <td>ZHVI All Homes (SFR, Condo/Co-op) Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZABT</td>
      <td>ZHVI All Homes- Bottom Tier Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Z5BR</td>
      <td>ZHVI 5+ Bedroom Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Z4BR</td>
      <td>ZHVI 4-Bedroom Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Z3BR</td>
      <td>ZHVI 3-Bedroom Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Z2BR</td>
      <td>ZHVI 2-Bedroom Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Z1BR</td>
      <td>ZHVI 1-Bedroom Time Series ($)</td>
      <td>Home values</td>
    </tr>
    <tr>
      <th>10</th>
      <td>SSSW</td>
      <td>Median Sale Price (Smooth, SFR only, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>11</th>
      <td>SSSM</td>
      <td>Median Sale Price (Smooth, SFR only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>12</th>
      <td>SSAW</td>
      <td>Median Sale Price (Smooth, All Homes, Weekly V...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>13</th>
      <td>SSAM</td>
      <td>Median Sale Price (Smooth, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>14</th>
      <td>SRSW</td>
      <td>Median Sale Price (Raw, SFR only, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>15</th>
      <td>SRSM</td>
      <td>Median Sale Price (Raw, SFR only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>16</th>
      <td>SRAW</td>
      <td>Median Sale Price (Raw, All Homes, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>17</th>
      <td>SRAM</td>
      <td>Median Sale Price (Raw, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>18</th>
      <td>SASW</td>
      <td>Median Sale Price (Smooth &amp; Seasonally Adjuste...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>19</th>
      <td>SASM</td>
      <td>Median Sale Price (Smooth &amp; Seasonally Adjuste...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>20</th>
      <td>SAAW</td>
      <td>Median Sale Price (Smooth &amp; Seasonally Adjuste...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>21</th>
      <td>SAAM</td>
      <td>Median Sale Price (Smooth &amp; Seasonally Adjuste...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>22</th>
      <td>RSSA</td>
      <td>ZORI (Smoothed, Seasonally Adjusted): All Home...</td>
      <td>Rentals</td>
    </tr>
    <tr>
      <th>23</th>
      <td>RSNA</td>
      <td>ZORI (Smoothed): All Homes Plus Multifamily Ti...</td>
      <td>Rentals</td>
    </tr>
    <tr>
      <th>24</th>
      <td>NSAW</td>
      <td>Median Days to Pending (Smooth, All Homes, Wee...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>25</th>
      <td>NSAM</td>
      <td>Median Days to Pending (Smooth, All Homes, Mon...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>26</th>
      <td>NRAW</td>
      <td>Median Days to Pending (Raw, All Homes, Weekly...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>27</th>
      <td>NRAM</td>
      <td>Median Days to Pending (Raw, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>28</th>
      <td>MSAW</td>
      <td>Mean Days to Pending (Smooth, All Homes, Weekl...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>29</th>
      <td>MSAM</td>
      <td>Mean Days to Pending (Smooth, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>30</th>
      <td>MRAW</td>
      <td>Mean Days to Pending (Raw, All Homes, Weekly V...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>31</th>
      <td>MRAM</td>
      <td>Mean Days to Pending (Raw, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>32</th>
      <td>LSSW</td>
      <td>Median List Price (Smooth, SFR Only, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>33</th>
      <td>LSSM</td>
      <td>Median List Price (Smooth, SFR Only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>34</th>
      <td>LSAW</td>
      <td>Median List Price (Smooth, All Homes, Weekly V...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>35</th>
      <td>LSAM</td>
      <td>Median List Price (Smooth, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>36</th>
      <td>LRSW</td>
      <td>Median List Price (Raw, SFR Only, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>37</th>
      <td>LRSM</td>
      <td>Median List Price (Raw, SFR Only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>38</th>
      <td>LRAW</td>
      <td>Median List Price (Raw, All Homes, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>39</th>
      <td>LRAM</td>
      <td>Median List Price (Raw, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>40</th>
      <td>ISSW</td>
      <td>For-Sale Inventory (Smooth, SFR only, Weekly V...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>41</th>
      <td>ISSM</td>
      <td>For-Sale Inventory (Smooth, SFR only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>42</th>
      <td>ISAW</td>
      <td>For-Sale Inventory (Smooth, All Homes, Weekly ...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>43</th>
      <td>ISAM</td>
      <td>For-Sale Inventory (Smooth, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>44</th>
      <td>IRSW</td>
      <td>For-sale Inventory (Raw, SFR only, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>45</th>
      <td>IRSM</td>
      <td>For-sale Inventory (Raw, SFR only, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>46</th>
      <td>IRAW</td>
      <td>For-Sale Inventory (Raw, All Homes, Weekly View)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>47</th>
      <td>IRAM</td>
      <td>For-Sale Inventory (Raw, All Homes, Monthly)</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>48</th>
      <td>CSSW</td>
      <td>Share of Listings With a Price Cut (Smooth, SF...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>49</th>
      <td>CSSM</td>
      <td>Share of Listings With a Price Cut (Smooth, SF...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>50</th>
      <td>CSAW</td>
      <td>Share of Listings With a Price Cut (Smooth, Al...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>51</th>
      <td>CSAM</td>
      <td>Share of Listings With a Price Cut (Smooth, Al...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>52</th>
      <td>CRSW</td>
      <td>Share of Listings With a Price Cut (Raw, SFR O...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>53</th>
      <td>CRSM</td>
      <td>Share of Listings With a Price Cut (Raw, SFR O...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>54</th>
      <td>CRAW</td>
      <td>Share of Listings With a Price Cut (Raw, All H...</td>
      <td>Inventory and sales</td>
    </tr>
    <tr>
      <th>55</th>
      <td>CRAM</td>
      <td>Share of Listings With a Price Cut (Raw, All H...</td>
      <td>Inventory and sales</td>
    </tr>
  </tbody>
</table>
</div>




```python
ind_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 56 entries, 0 to 55
    Data columns (total 3 columns):
     #   Column        Non-Null Count  Dtype 
    ---  ------        --------------  ----- 
     0   indicator_id  56 non-null     object
     1   indicator     56 non-null     object
     2   category      56 non-null     object
    dtypes: object(3)
    memory usage: 1.4+ KB



```python
#import table 3

api_url = "https://www.quandl.com/api/v3/datatables/ZILLOW/DATA/?api_key=8zYqUX8TEUYYtbCkcPHq"

response3 = requests.get(api_url)

data3 = response3.json()

zillow_data = pd.DataFrame(data3)

zillow_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datatable</th>
      <th>meta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>data</th>
      <td>[[ZSFH, 99999, 2020-11-30, 377249.0], [ZSFH, 9...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>columns</th>
      <td>[{'name': 'indicator_id', 'type': 'text'}, {'n...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>next_cursor_id</th>
      <td>NaN</td>
      <td>djFfMjk1NTYxMjdfMTYxMDMzMjgxOA==</td>
    </tr>
  </tbody>
</table>
</div>




```python
zillow_data = zillow_data['datatable']['data']

columns = ['indicator_id', 'region_id', 'date', 'value']

z_df = pd.DataFrame(zillow_data, columns = columns)

z_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-11-30</td>
      <td>377249.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-10-31</td>
      <td>374582.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-09-30</td>
      <td>369733.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-08-31</td>
      <td>366760.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-07-31</td>
      <td>336538.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-06-30</td>
      <td>168443.0</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-05-31</td>
      <td>167465.0</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-04-30</td>
      <td>165644.0</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-03-31</td>
      <td>164264.0</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-02-29</td>
      <td>163415.0</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>




```python
z_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10000 entries, 0 to 9999
    Data columns (total 4 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   indicator_id  10000 non-null  object 
     1   region_id     10000 non-null  object 
     2   date          10000 non-null  object 
     3   value         10000 non-null  float64
    dtypes: float64(1), object(3)
    memory usage: 312.6+ KB



```python
z_df.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-11-30</td>
      <td>377249.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-10-31</td>
      <td>374582.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-09-30</td>
      <td>369733.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-08-31</td>
      <td>366760.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-07-31</td>
      <td>336538.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-06-30</td>
      <td>367111.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-05-31</td>
      <td>360642.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-04-30</td>
      <td>358251.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-03-31</td>
      <td>357318.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-02-29</td>
      <td>356443.0</td>
    </tr>
  </tbody>
</table>
</div>



## 3. Merging the tables


```python
z_merged = z_df.merge(ind_df, on='indicator_id', how='inner').merge(reg_df, on='region_id', how='inner')
```


```python
z_merged.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 10000 entries, 0 to 9999
    Data columns (total 8 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   indicator_id  10000 non-null  object 
     1   region_id     10000 non-null  object 
     2   date          10000 non-null  object 
     3   value         10000 non-null  float64
     4   indicator     10000 non-null  object 
     5   category      10000 non-null  object 
     6   region_type   10000 non-null  object 
     7   region        10000 non-null  object 
    dtypes: float64(1), object(7)
    memory usage: 703.1+ KB



```python
z_merged.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-11-30</td>
      <td>377249.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-10-31</td>
      <td>374582.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-09-30</td>
      <td>369733.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-08-31</td>
      <td>366760.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-07-31</td>
      <td>336538.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
  </tbody>
</table>
</div>




```python
z_merged['region_id'].nunique()
```




    46




```python
z_merged['region'].unique()
```




    array(['98847; WA; Wenatchee; Chelan County; Peshastin',
           '98846; WA; Okanogan County; Pateros',
           '98845; WA; Wenatchee; Douglas County; Palisades',
           '98844; WA; Okanogan County; Oroville',
           '98843; WA; Wenatchee; Douglas County; Orondo',
           '98841; WA; Okanogan County; Omak',
           '98840; WA; Okanogan County; Okanogan',
           '98837; WA; Moses Lake; Grant County',
           '98836; WA; Wenatchee; Chelan County',
           '98834; WA; Okanogan County; Methow',
           'Carrsville; VA; Virginia Beach-Norfolk-Newport News; Isle of Wight County',
           '98833; WA; Okanogan County; Mazama',
           '98832; WA; Moses Lake; Grant County; Krupp',
           '98831; WA; Wenatchee; Chelan County; Manson',
           '98830; WA; Wenatchee; Douglas County; Mansfield',
           '98829; WA; Okanogan County; Okanogan',
           '98828; WA; Wenatchee; Chelan County; Malaga',
           '98827; WA; Okanogan County; Loomis',
           '98826; WA; Wenatchee; Chelan County; Leavenworth',
           '98823; WA; Moses Lake; Grant County; Ephrata',
           'Birchleaf; VA; Big Stone Gap; Dickenson County',
           '98822; WA; Wenatchee; Chelan County; Entiat',
           '98821; WA; Wenatchee; Chelan County; Cashmere',
           '98819; WA; Okanogan County; Omak',
           '98817; WA; Wenatchee; Chelan County; Chelan Falls',
           '98816; WA; Wenatchee; Chelan County; Chelan',
           '98815; WA; Wenatchee; Chelan County; Cashmere',
           '98814; WA; Okanogan County; Carlton',
           '98813; WA; Wenatchee; Douglas County; Bridgeport',
           '98812; WA; Okanogan County; Brewster',
           '98811; WA; Wenatchee; Chelan County; Entiat',
           '98802; WA; Wenatchee; Douglas County; East Wenatchee Bench',
           '98801; WA; Wenatchee; Chelan County', '98686', '98685',
           '98684; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver',
           '98683', '98682',
           '98675; WA; Portland-Vancouver-Hillsboro; Clark County; Yacolt',
           '98674; WA; Longview; Cowlitz County; Woodland',
           '98673; WA; Klickitat County; Centerville',
           '98672; WA; Klickitat County; White Salmon', '98671',
           '98670; WA; Klickitat County; Wahkiacus',
           '98665; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver',
           '98664; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver'],
          dtype=object)




```python
z_merged['zip'] = z_merged['region'].str.slice(stop=5)
```


```python
z_merged['zip']
```




    0       98847
    1       98847
    2       98847
    3       98847
    4       98847
            ...  
    9995    98664
    9996    98664
    9997    98664
    9998    98664
    9999    98664
    Name: zip, Length: 10000, dtype: object




```python
z_merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-11-30</td>
      <td>377249.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-10-31</td>
      <td>374582.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-09-30</td>
      <td>369733.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-08-31</td>
      <td>366760.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-07-31</td>
      <td>336538.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-06-30</td>
      <td>168443.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-05-31</td>
      <td>167465.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-04-30</td>
      <td>165644.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-03-31</td>
      <td>164264.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-02-29</td>
      <td>163415.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 9 columns</p>
</div>



## 4. Grouping by zip and calculating the median house price per zip


```python
zip = z_merged.groupby(['zip', 'region'])['value'].median().sort_values(ascending=False)
```


```python
zip.to_frame()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>zip</th>
      <th>region</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>98816</th>
      <th>98816; WA; Wenatchee; Chelan County; Chelan</th>
      <td>361263.5</td>
    </tr>
    <tr>
      <th>98826</th>
      <th>98826; WA; Wenatchee; Chelan County; Leavenworth</th>
      <td>333323.0</td>
    </tr>
    <tr>
      <th>98831</th>
      <th>98831; WA; Wenatchee; Chelan County; Manson</th>
      <td>317479.0</td>
    </tr>
    <tr>
      <th>98843</th>
      <th>98843; WA; Wenatchee; Douglas County; Orondo</th>
      <td>288216.0</td>
    </tr>
    <tr>
      <th>98672</th>
      <th>98672; WA; Klickitat County; White Salmon</th>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>98833</th>
      <th>98833; WA; Okanogan County; Mazama</th>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>98675</th>
      <th>98675; WA; Portland-Vancouver-Hillsboro; Clark County; Yacolt</th>
      <td>269505.0</td>
    </tr>
    <tr>
      <th>98664</th>
      <th>98664; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>264737.0</td>
    </tr>
    <tr>
      <th>98686</th>
      <th>98686</th>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>98685</th>
      <th>98685</th>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>98671</th>
      <th>98671</th>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>98683</th>
      <th>98683</th>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>98674</th>
      <th>98674; WA; Longview; Cowlitz County; Woodland</th>
      <td>239860.0</td>
    </tr>
    <tr>
      <th>98814</th>
      <th>98814; WA; Okanogan County; Carlton</th>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>98836</th>
      <th>98836; WA; Wenatchee; Chelan County</th>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>98801</th>
      <th>98801; WA; Wenatchee; Chelan County</th>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>98815</th>
      <th>98815; WA; Wenatchee; Chelan County; Cashmere</th>
      <td>225056.0</td>
    </tr>
    <tr>
      <th>98847</th>
      <th>98847; WA; Wenatchee; Chelan County; Peshastin</th>
      <td>221684.0</td>
    </tr>
    <tr>
      <th>98665</th>
      <th>98665; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>214661.0</td>
    </tr>
    <tr>
      <th>98802</th>
      <th>98802; WA; Wenatchee; Douglas County; East Wenatchee Bench</th>
      <td>209714.0</td>
    </tr>
    <tr>
      <th>98684</th>
      <th>98684; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>209519.0</td>
    </tr>
    <tr>
      <th>98682</th>
      <th>98682</th>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>98828</th>
      <th>98828; WA; Wenatchee; Chelan County; Malaga</th>
      <td>199534.0</td>
    </tr>
    <tr>
      <th>98821</th>
      <th>98821; WA; Wenatchee; Chelan County; Cashmere</th>
      <td>193236.0</td>
    </tr>
    <tr>
      <th>98846</th>
      <th>98846; WA; Okanogan County; Pateros</th>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>98822</th>
      <th>98822; WA; Wenatchee; Chelan County; Entiat</th>
      <td>188713.0</td>
    </tr>
    <tr>
      <th>98834</th>
      <th>98834; WA; Okanogan County; Methow</th>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>Carrs</th>
      <th>Carrsville; VA; Virginia Beach-Norfolk-Newport News; Isle of Wight County</th>
      <td>164069.0</td>
    </tr>
    <tr>
      <th>98837</th>
      <th>98837; WA; Moses Lake; Grant County</th>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>98670</th>
      <th>98670; WA; Klickitat County; Wahkiacus</th>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>98811</th>
      <th>98811; WA; Wenatchee; Chelan County; Entiat</th>
      <td>157458.0</td>
    </tr>
    <tr>
      <th>98845</th>
      <th>98845; WA; Wenatchee; Douglas County; Palisades</th>
      <td>155664.0</td>
    </tr>
    <tr>
      <th>98817</th>
      <th>98817; WA; Wenatchee; Chelan County; Chelan Falls</th>
      <td>150035.5</td>
    </tr>
    <tr>
      <th>98844</th>
      <th>98844; WA; Okanogan County; Oroville</th>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>98827</th>
      <th>98827; WA; Okanogan County; Loomis</th>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>98840</th>
      <th>98840; WA; Okanogan County; Okanogan</th>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>98823</th>
      <th>98823; WA; Moses Lake; Grant County; Ephrata</th>
      <td>130578.0</td>
    </tr>
    <tr>
      <th>98841</th>
      <th>98841; WA; Okanogan County; Omak</th>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>98812</th>
      <th>98812; WA; Okanogan County; Brewster</th>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>98819</th>
      <th>98819; WA; Okanogan County; Omak</th>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>98829</th>
      <th>98829; WA; Okanogan County; Okanogan</th>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>98832</th>
      <th>98832; WA; Moses Lake; Grant County; Krupp</th>
      <td>105315.0</td>
    </tr>
    <tr>
      <th>98830</th>
      <th>98830; WA; Wenatchee; Douglas County; Mansfield</th>
      <td>104139.0</td>
    </tr>
    <tr>
      <th>98813</th>
      <th>98813; WA; Wenatchee; Douglas County; Bridgeport</th>
      <td>85479.5</td>
    </tr>
    <tr>
      <th>98673</th>
      <th>98673; WA; Klickitat County; Centerville</th>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>Birch</th>
      <th>Birchleaf; VA; Big Stone Gap; Dickenson County</th>
      <td>56042.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
zip_df = zip.to_frame()
```


```python
zip_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>zip</th>
      <th>region</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>98816</th>
      <th>98816; WA; Wenatchee; Chelan County; Chelan</th>
      <td>361263.5</td>
    </tr>
    <tr>
      <th>98826</th>
      <th>98826; WA; Wenatchee; Chelan County; Leavenworth</th>
      <td>333323.0</td>
    </tr>
    <tr>
      <th>98831</th>
      <th>98831; WA; Wenatchee; Chelan County; Manson</th>
      <td>317479.0</td>
    </tr>
    <tr>
      <th>98843</th>
      <th>98843; WA; Wenatchee; Douglas County; Orondo</th>
      <td>288216.0</td>
    </tr>
    <tr>
      <th>98672</th>
      <th>98672; WA; Klickitat County; White Salmon</th>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>98833</th>
      <th>98833; WA; Okanogan County; Mazama</th>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>98675</th>
      <th>98675; WA; Portland-Vancouver-Hillsboro; Clark County; Yacolt</th>
      <td>269505.0</td>
    </tr>
    <tr>
      <th>98664</th>
      <th>98664; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>264737.0</td>
    </tr>
    <tr>
      <th>98686</th>
      <th>98686</th>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>98685</th>
      <th>98685</th>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>98671</th>
      <th>98671</th>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>98683</th>
      <th>98683</th>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>98674</th>
      <th>98674; WA; Longview; Cowlitz County; Woodland</th>
      <td>239860.0</td>
    </tr>
    <tr>
      <th>98814</th>
      <th>98814; WA; Okanogan County; Carlton</th>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>98836</th>
      <th>98836; WA; Wenatchee; Chelan County</th>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>98801</th>
      <th>98801; WA; Wenatchee; Chelan County</th>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>98815</th>
      <th>98815; WA; Wenatchee; Chelan County; Cashmere</th>
      <td>225056.0</td>
    </tr>
    <tr>
      <th>98847</th>
      <th>98847; WA; Wenatchee; Chelan County; Peshastin</th>
      <td>221684.0</td>
    </tr>
    <tr>
      <th>98665</th>
      <th>98665; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>214661.0</td>
    </tr>
    <tr>
      <th>98802</th>
      <th>98802; WA; Wenatchee; Douglas County; East Wenatchee Bench</th>
      <td>209714.0</td>
    </tr>
    <tr>
      <th>98684</th>
      <th>98684; WA; Portland-Vancouver-Hillsboro; Clark County; Vancouver</th>
      <td>209519.0</td>
    </tr>
    <tr>
      <th>98682</th>
      <th>98682</th>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>98828</th>
      <th>98828; WA; Wenatchee; Chelan County; Malaga</th>
      <td>199534.0</td>
    </tr>
    <tr>
      <th>98821</th>
      <th>98821; WA; Wenatchee; Chelan County; Cashmere</th>
      <td>193236.0</td>
    </tr>
    <tr>
      <th>98846</th>
      <th>98846; WA; Okanogan County; Pateros</th>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>98822</th>
      <th>98822; WA; Wenatchee; Chelan County; Entiat</th>
      <td>188713.0</td>
    </tr>
    <tr>
      <th>98834</th>
      <th>98834; WA; Okanogan County; Methow</th>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>Carrs</th>
      <th>Carrsville; VA; Virginia Beach-Norfolk-Newport News; Isle of Wight County</th>
      <td>164069.0</td>
    </tr>
    <tr>
      <th>98837</th>
      <th>98837; WA; Moses Lake; Grant County</th>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>98670</th>
      <th>98670; WA; Klickitat County; Wahkiacus</th>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>98811</th>
      <th>98811; WA; Wenatchee; Chelan County; Entiat</th>
      <td>157458.0</td>
    </tr>
    <tr>
      <th>98845</th>
      <th>98845; WA; Wenatchee; Douglas County; Palisades</th>
      <td>155664.0</td>
    </tr>
    <tr>
      <th>98817</th>
      <th>98817; WA; Wenatchee; Chelan County; Chelan Falls</th>
      <td>150035.5</td>
    </tr>
    <tr>
      <th>98844</th>
      <th>98844; WA; Okanogan County; Oroville</th>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>98827</th>
      <th>98827; WA; Okanogan County; Loomis</th>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>98840</th>
      <th>98840; WA; Okanogan County; Okanogan</th>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>98823</th>
      <th>98823; WA; Moses Lake; Grant County; Ephrata</th>
      <td>130578.0</td>
    </tr>
    <tr>
      <th>98841</th>
      <th>98841; WA; Okanogan County; Omak</th>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>98812</th>
      <th>98812; WA; Okanogan County; Brewster</th>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>98819</th>
      <th>98819; WA; Okanogan County; Omak</th>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>98829</th>
      <th>98829; WA; Okanogan County; Okanogan</th>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>98832</th>
      <th>98832; WA; Moses Lake; Grant County; Krupp</th>
      <td>105315.0</td>
    </tr>
    <tr>
      <th>98830</th>
      <th>98830; WA; Wenatchee; Douglas County; Mansfield</th>
      <td>104139.0</td>
    </tr>
    <tr>
      <th>98813</th>
      <th>98813; WA; Wenatchee; Douglas County; Bridgeport</th>
      <td>85479.5</td>
    </tr>
    <tr>
      <th>98673</th>
      <th>98673; WA; Klickitat County; Centerville</th>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>Birch</th>
      <th>Birchleaf; VA; Big Stone Gap; Dickenson County</th>
      <td>56042.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
zip_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    MultiIndex: 46 entries, ('98816', '98816; WA; Wenatchee; Chelan County; Chelan') to ('Birch', 'Birchleaf; VA; Big Stone Gap; Dickenson County')
    Data columns (total 1 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   value   46 non-null     float64
    dtypes: float64(1)
    memory usage: 1.3+ KB



```python
zip_df.reset_index(inplace=True)
```


```python
zip_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 46 entries, 0 to 45
    Data columns (total 3 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   zip     46 non-null     object 
     1   region  46 non-null     object 
     2   value   46 non-null     float64
    dtypes: float64(1), object(2)
    memory usage: 1.2+ KB



```python
zip_df.rename(columns={'value': 'median_val'}, inplace=True)
```


```python
zip_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>361263.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>333323.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>317479.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>288216.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>269505.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>264737.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98671</td>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98674</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>239860.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98814</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98836</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98815</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>225056.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98847</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>221684.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98665</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>214661.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98802</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>209714.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98684</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>209519.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98682</td>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98828</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>199534.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>193236.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98846</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98822</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>188713.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98834</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Carrs</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98837</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98670</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98811</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>157458.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98845</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>155664.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98817</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>150035.5</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98844</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98827</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98840</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98823</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>130578.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98841</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98812</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98829</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>105315.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>104139.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>85479.5</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Birch</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
zip_df.sort_values(['median_val'], ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>361263.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>333323.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>317479.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>288216.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>269505.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>264737.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98671</td>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98674</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>239860.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98814</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98836</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98815</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>225056.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98847</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>221684.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98665</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>214661.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98802</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>209714.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98684</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>209519.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98682</td>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98828</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>199534.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>193236.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98846</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98822</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>188713.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98834</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Carrs</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98837</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98670</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98811</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>157458.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98845</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>155664.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98817</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>150035.5</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98844</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98827</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98840</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98823</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>130578.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98841</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98812</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98829</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>105315.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>104139.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>85479.5</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Birch</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
zip_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>361263.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>333323.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>317479.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>288216.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>269505.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>264737.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98671</td>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98674</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>239860.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98814</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98836</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98815</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>225056.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98847</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>221684.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98665</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>214661.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98802</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>209714.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98684</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>209519.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98682</td>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98828</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>199534.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>193236.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98846</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98822</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>188713.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98834</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Carrs</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98837</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98670</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98811</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>157458.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98845</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>155664.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98817</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>150035.5</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98844</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98827</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98840</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98823</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>130578.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98841</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98812</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98829</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>105315.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>104139.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>85479.5</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Birch</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
    </tr>
  </tbody>
</table>
</div>



## 5. Data cleaning & reshaping - counting the number of elements in the region column

When we examine the 'region' column above, we can see that each row in this column contains several elements separated by ";". These elements seem to correspond to: zip code; state; city; county; suburb for each zip code. 

However not all rows seem to have the same number of elements (i.e. in some rows it seems one or more of the above details are missing). 

To analyse the data (for eg if we'd like to see which counties or suburbs have the highest or lowest median sale prce for a family home), we'd need to break the region column into its elements first, so that we have a separate column for state,county, city, suburb etc.

If we simply go ahead and split the rows as they currently are into 5 new columns (zip code; state; city; county; suburb), some columns will contain the wrong type of data (eg where rows do not contain all 5 elements, the suburb name might wrongly appear in the "county" column for records with less than 5 elements in the region column). This would then be tricky to tidy up as it would reuqire a row-by-row manual review which could be quite time consuming and potentially inaccurate.

So, the first step, before splitting each row into columns, would be to isolate those rows where there are less than 5 elements and sorting those into the correct columns. Then, we can join/merge the resulting table into our main table.

To get the number of elements in each row's string (in the region column), we'll use the str.count() function on the ";" in the region column, add 1 to the count (as there are n+1 elements when n number of ";" are counted), and group by the zip. 


```python
#count the number of ; in each row in the region column
counts = zip_df['region'].str.count(';').add(1).groupby(zip_df.zip).sum()
```


```python
#view the results
counts
```




    zip
    98664    5
    98665    5
    98670    4
    98671    1
    98672    4
    98673    4
    98674    5
    98675    5
    98682    1
    98683    1
    98684    5
    98685    1
    98686    1
    98801    4
    98802    5
    98811    5
    98812    4
    98813    5
    98814    4
    98815    5
    98816    5
    98817    5
    98819    4
    98821    5
    98822    5
    98823    5
    98826    5
    98827    4
    98828    5
    98829    4
    98830    5
    98831    5
    98832    5
    98833    4
    98834    4
    98836    4
    98837    4
    98840    4
    98841    4
    98843    5
    98844    4
    98845    5
    98846    4
    98847    5
    Birch    4
    Carrs    4
    Name: region, dtype: int64



As we can see above, while some zip codes have 5 elements, others have 4 or 1 elemnts which means some of the elements (city, county or suburb) might be missing in those rows.

First, let's drop the "complete" rows which currently have 5 elemnts from the counts table as those don't need any further input.


```python
#convert series object into DataFrame
counts = counts.to_frame()
```


```python
#drop rows where count equals 5
counts = counts.drop(counts[counts.region == 5].index)
```

### Merging the counts with the region rows


```python
#sort the counts in descending order
counts.sort_values(by=['region'], ascending=False)

#merge 'counts' DF with the 'zip_df' DF on zip 
merged_df = counts.merge(zip_df, on='zip', how='left')
```


```python
#view resulting table
merged_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region_x</th>
      <th>region_y</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>4</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98671</td>
      <td>1</td>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98672</td>
      <td>4</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98673</td>
      <td>4</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98682</td>
      <td>1</td>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98683</td>
      <td>1</td>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98685</td>
      <td>1</td>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98686</td>
      <td>1</td>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98801</td>
      <td>4</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98812</td>
      <td>4</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98814</td>
      <td>4</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98819</td>
      <td>4</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98827</td>
      <td>4</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98829</td>
      <td>4</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98833</td>
      <td>4</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98834</td>
      <td>4</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98836</td>
      <td>4</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98837</td>
      <td>4</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98840</td>
      <td>4</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98841</td>
      <td>4</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98844</td>
      <td>4</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98846</td>
      <td>4</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Birch</td>
      <td>4</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Carrs</td>
      <td>4</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
    </tr>
  </tbody>
</table>
</div>



As we can see above, the remaining rows either have 4 or 1 elements.

Next, let's isolate the rows with 4 elements so we can tidy those up further.

### Extracting the relevant rows into a new table 


```python
#create new table named reg_4 extracting the rows where the elements' count equals 4
reg_4 = merged_df[merged_df.region_x == 4].reset_index(drop=True)
#view results
reg_4
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region_x</th>
      <th>region_y</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>4</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>4</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>4</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98801</td>
      <td>4</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>4</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>4</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>4</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>4</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>4</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>4</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>4</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836</td>
      <td>4</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837</td>
      <td>4</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>4</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>4</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>4</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>4</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Birch</td>
      <td>4</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Carrs</td>
      <td>4</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
    </tr>
  </tbody>
</table>
</div>



### Splitting the string in each row into separate columns

We now have a new DataFrame only containing the 19 rows that have 4 elements. 

To analyse which elements are missing in each row, let's split those rows first using pandas' str.split() function again. 


```python
#split each row under the region_y column into a new DF: reg_4_split
reg_4_split = reg_4.region_y.str.split(";", expand=True)
```


```python
#view the DF's info to checj=k for null values and data types
reg_4_split.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 19 entries, 0 to 18
    Data columns (total 4 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   0       19 non-null     object
     1   1       19 non-null     object
     2   2       19 non-null     object
     3   3       19 non-null     object
    dtypes: object(4)
    memory usage: 736.0+ bytes



```python
#view the results of the split
reg_4_split
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Birchleaf</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Carrsville</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
    </tr>
  </tbody>
</table>
</div>



### Data rehsaping using functions 

As we can see in the above table, some of the new columns seem to contain a few 'wrong' entries. For example:

- row 17 & 18 in column 0 should be a zip code but instead contain the area name; and
- rows 3,11,12,17 & 18 in column 2 ('county') seem to contain the city name rather than the county and in column 3('suburb') seem to contain the county name instead of suburb.

we'll need to do some further reshaping of these rows to tidy up the wrong entries and ensure they appear in the correct column.

First, we want to identify the rows in column 2 that don't have a county name. We can do this using a function that would loop through each row in column 2 and flag where the row doesn't contain a county name as 'city'.


```python
#define a function

def is_county(col):
   if 'County' in col:
    return 'county'
   else: 
        return 'city' 
```


```python
#create a new column (not_county) for the function results and apply the function to column 2

reg_4_split['not_county'] = reg_4_split[2].apply(is_county)
```


```python
#show results

reg_4_split
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>not_county</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>county</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>county</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>county</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>county</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>county</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>county</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>county</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>county</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>county</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>county</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>county</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>county</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>county</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>county</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Birchleaf</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Carrsville</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>city</td>
    </tr>
  </tbody>
</table>
</div>



### Splitting the results into two tables for further tidying up

We now have a new column (not_county) which highlights the rows in column 2 where the entry isn't a county but rather a city. 

Next, let's extract the city rows from the above table using pandas' str.contains() function and place those in a new DataFrame named 'city'.


```python
#extract rows containing 'city' into a new DF: 'city'
city = reg_4_split[reg_4_split['not_county'].str.contains('city')]

#view results
city
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>not_county</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Birchleaf</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>city</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Carrsville</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>city</td>
    </tr>
  </tbody>
</table>
</div>




```python
#drop the above rows from the reg_4_split table and create a new DF: 'county'

county = reg_4_split.drop([3,11,12,17,18])
```


```python
#view results

county
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>not_county</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>county</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>county</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>county</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>county</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>county</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>county</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>county</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>county</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>county</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>county</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>county</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>county</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>county</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>county</td>
    </tr>
  </tbody>
</table>
</div>



### Reshaping data by dropping columns, renaming columns and merging with main table

We now have two separate DFs - one named "city" for all rows with state, county and city data (but no suburb data) and one named "county" for all rows with state, county and suburb data (but no city data).

Next, we'll reshape each of these DFs to rename the columns so they reflect the correct data, before we merge these into our original table.


```python
#drop the not_county column from the county table

county.drop(['not_county'], axis=1, inplace=True)

#view results

county
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
    </tr>
  </tbody>
</table>
</div>




```python
# rename columns

county.columns = ['zip','state', 'county', 'suburb']

#validate name change has taken place

print(county.columns)
```

    Index(['zip', 'state', 'county', 'suburb'], dtype='object')



```python
#view results

county
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98670</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98672</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98673</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98812</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98814</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98819</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98827</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98829</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98833</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98834</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98840</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98841</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98844</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98846</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
    </tr>
  </tbody>
</table>
</div>



The county table is now ready to be merged with the zip_df table (as you may recall the latter contains zip, region and median sale price data). By merging the two DFs, we'll start a "master" table that contains all the details we'd be interested in to perform further statistical analysis on the data. 


```python
#merge county with zip_df into new table:county_marge

county_merge = zip_df.merge(county, on='zip', how='left', copy=False)
```


```python
#view results

county_merge
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region</th>
      <th>median_val</th>
      <th>state</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>361263.5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>333323.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>317479.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>288216.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>286706.0</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>284704.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>269505.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>264737.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>98686</td>
      <td>262738.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>98685</td>
      <td>262531.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98671</td>
      <td>98671</td>
      <td>259673.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>98683</td>
      <td>246936.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98674</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>239860.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98814</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>233316.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98836</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>230441.5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>229814.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98815</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>225056.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98847</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>221684.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98665</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>214661.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98802</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>209714.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98684</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>209519.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98682</td>
      <td>98682</td>
      <td>202697.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98828</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>199534.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>193236.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98846</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>193119.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98822</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>188713.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98834</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>166287.5</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Carrs</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>164069.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98837</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>163048.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98670</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>157999.0</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98811</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>157458.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98845</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>155664.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98817</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>150035.5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98844</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>140695.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98827</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>136080.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98840</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>133589.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98823</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>130578.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98841</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>128936.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98812</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>126280.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>124513.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98829</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>108277.0</td>
      <td>WA</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>105315.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>104139.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>85479.5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>73255.0</td>
      <td>WA</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Birch</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>56042.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### Repeating the data cleaning process for rows with only 1 element in the 'region' column

The resulting table above is a good start as it now contains the county data in the correct columns but we still have to incorporate data for those remaining rows with 1 elements, identified earlier (and populate the columns for the rows with 5 elements in the 'region' col). We'll simply repeat the same process above. 


```python
#extract rows with only one region element into a new DF:reg_1

reg_1 = merged_df[merged_df.region_x == 1].reset_index(drop=True)

#view results
reg_1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>region_x</th>
      <th>region_y</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98671</td>
      <td>1</td>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98682</td>
      <td>1</td>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98683</td>
      <td>1</td>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98685</td>
      <td>1</td>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98686</td>
      <td>1</td>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#drop the unnecessary cols

reg_1.drop(labels=['region_x', 'region_y'], axis=1, inplace=True)

#view result

reg_1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>median_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98671</td>
      <td>259673.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98682</td>
      <td>202697.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98683</td>
      <td>246936.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98685</td>
      <td>262531.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98686</td>
      <td>262738.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#drop median_val as this already exists in the master DF

reg_1.drop('median_val', axis=1, inplace=True)
```


```python
#view result

reg_1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98671</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98682</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98683</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98685</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98686</td>
    </tr>
  </tbody>
</table>
</div>




```python
#check the table's info
reg_1.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5 entries, 0 to 4
    Data columns (total 1 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   zip     5 non-null      object
    dtypes: object(1)
    memory usage: 168.0+ bytes



```python
#insert new columns and values (col number, name & value)

reg_1.insert(1,'state','WA')
reg_1.insert(2, 'city', 'Portland')
reg_1.insert(3, 'county', 'Clark County')
reg_1.insert(4, 'suburb', 'NaN')

#view result
reg_1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#insert correct suburb name for each row/zip

reg_1.iloc[0,4] = 'Washougal'

reg_1.iloc[1,4] = 'Vancounver'

reg_1.iloc[2,4] = 'Vancounver'

reg_1.iloc[3,4] = 'Felida'

reg_1.iloc[4,4] = 'Mount Vista'


#view results
reg_1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
    </tr>
  </tbody>
</table>
</div>



The reg_1 DF is now complete and ready to be merged with the master table (county_merge). However, a simple merge won't work here as it'll simply append the rows in the above table to the master. 

In fact, we want the above values to be inserted/combined into the existing table where current values are NaN. We can do so unsing pandas' clever combine_first() function and combine the 2 tables on 'zip' as follows:

### combine_first()


```python
merged = reg_1.set_index('zip').combine_first(county_merge.set_index('zip')).reset_index()
```


```python
#view result

merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>city</th>
      <th>county</th>
      <th>median_val</th>
      <th>region</th>
      <th>state</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98665</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98670</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>WA</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98671</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>259673.0</td>
      <td>98671</td>
      <td>WA</td>
      <td>Washougal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>WA</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98673</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>WA</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98674</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98675</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98682</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>202697.0</td>
      <td>98682</td>
      <td>WA</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98683</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>246936.0</td>
      <td>98683</td>
      <td>WA</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98684</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98685</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>262531.0</td>
      <td>98685</td>
      <td>WA</td>
      <td>Felida</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98686</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>262738.0</td>
      <td>98686</td>
      <td>WA</td>
      <td>Mount Vista</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98801</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98802</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98811</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98812</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>WA</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98813</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98814</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>WA</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98815</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98816</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98817</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98819</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>WA</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98822</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98823</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98826</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98827</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>WA</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98828</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98829</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>WA</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98830</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98831</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98832</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98833</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>WA</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98834</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>WA</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98836</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98837</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98840</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>WA</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98841</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>WA</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98843</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98844</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>WA</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98845</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98846</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>WA</td>
      <td>Pateros</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98847</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Birch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Carrs</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



We now have a merged table with the relevant data from the reg_1 table neatly inserted into the relevant columns for each row based on the zip.


```python
#re-order the columns

merged = merged.reindex(['zip', 'state', 'city', 'county', 'suburb', 'median_val', 'region'], axis=1)
```


```python
#view re-ordered DF

merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98665</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98674</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98675</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98684</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98801</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98802</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98811</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98813</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98815</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98816</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98817</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98822</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98823</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98826</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98828</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98830</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98831</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98832</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98836</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98837</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98843</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98845</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98847</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Birch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Carrs</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
  </tbody>
</table>
</div>



### Populating remaining NaN values

Next we want to populate the remaining null values in the table. To do this, we'll first identify rows where there are more than 1 null values, using the pandas' isnull() and sum() functions.


```python
#count nulls in each rows and place result in new col:'nulls'

merged['nulls'] = merged.isnull().sum(axis=1)
```


```python
#view result

merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
      <th>nulls</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98665</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98674</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>4</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98675</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98684</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98801</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98802</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98811</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>4</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>1</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98813</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>4</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>1</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98815</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>4</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98816</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>4</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98817</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>4</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>1</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>4</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98822</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>4</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98823</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>4</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98826</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>4</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98828</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>4</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98830</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>4</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98831</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>4</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98832</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>4</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>1</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>1</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98836</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98837</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>1</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98843</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>4</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>1</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98845</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>4</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>1</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98847</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>4</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Birchleaf</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Carrsville</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




```python
#sort nulls count in descending order
merged = merged.sort_values(by=['nulls'], ascending=False).reset_index(drop=True)
```


```python
#view result
merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
      <th>nulls</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98815</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98817</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98822</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98823</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>4</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98826</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>4</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98828</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>4</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98830</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>4</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98831</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>4</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98832</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>4</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98843</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>4</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98845</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>4</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98847</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>4</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Birch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98816</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>4</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98821</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>4</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Carrs</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98675</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98811</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>4</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98802</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98801</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>4</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98684</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98813</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>4</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98674</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>4</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>1</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>1</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>1</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>1</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>1</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>1</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>1</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>1</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>1</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>1</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>1</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
      <td>0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
      <td>0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
      <td>0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
      <td>0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



The above table indicates that rows 0-26 have 4 missing values each and rows 27-40 have 1 missing value each.

We know that the 1 missing value can be left as NaN as the relevant zip codes don't have a city value (they're rural areas) so let's address the missing values in rows 0-26 next.


```python
#extract releant rows from the region column
s = merged.loc[0:26, 'region']

s
```




    0     98664; WA; Portland-Vancouver-Hillsboro; Clark...
    1         98815; WA; Wenatchee; Chelan County; Cashmere
    2     98817; WA; Wenatchee; Chelan County; Chelan Falls
    3     98665; WA; Portland-Vancouver-Hillsboro; Clark...
    4           98822; WA; Wenatchee; Chelan County; Entiat
    5          98823; WA; Moses Lake; Grant County; Ephrata
    6      98826; WA; Wenatchee; Chelan County; Leavenworth
    7           98828; WA; Wenatchee; Chelan County; Malaga
    8       98830; WA; Wenatchee; Douglas County; Mansfield
    9           98831; WA; Wenatchee; Chelan County; Manson
    10           98832; WA; Moses Lake; Grant County; Krupp
    11                  98836; WA; Wenatchee; Chelan County
    12                  98837; WA; Moses Lake; Grant County
    13         98843; WA; Wenatchee; Douglas County; Orondo
    14      98845; WA; Wenatchee; Douglas County; Palisades
    15       98847; WA; Wenatchee; Chelan County; Peshastin
    16       Birchleaf; VA; Big Stone Gap; Dickenson County
    17          98816; WA; Wenatchee; Chelan County; Chelan
    18        98821; WA; Wenatchee; Chelan County; Cashmere
    19    Carrsville; VA; Virginia Beach-Norfolk-Newport...
    20    98675; WA; Portland-Vancouver-Hillsboro; Clark...
    21          98811; WA; Wenatchee; Chelan County; Entiat
    22    98802; WA; Wenatchee; Douglas County; East Wen...
    23                  98801; WA; Wenatchee; Chelan County
    24    98684; WA; Portland-Vancouver-Hillsboro; Clark...
    25     98813; WA; Wenatchee; Douglas County; Bridgeport
    26        98674; WA; Longview; Cowlitz County; Woodland
    Name: region, dtype: object




```python
#convert series to DF:df

df = s.to_frame()
```


```python
#view columns
df.columns
```




    Index(['region'], dtype='object')




```python
#view info
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 27 entries, 0 to 26
    Data columns (total 1 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   region  27 non-null     object
    dtypes: object(1)
    memory usage: 344.0+ bytes



```python
#split region column in df into 5 columns

df[['zip', 'state', 'city', 'county', 'suburb']] = df['region'].str.split(';', expand=True)
```


```python
#view results

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>region</th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98815</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>98817</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan Falls</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98665</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98822</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>98823</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Ephrata</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>98828</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Malaga</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>98845</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Palisades</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Peshastin</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>Birchleaf</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98821</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>Carrsville</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98811</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>98802</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>East Wenatchee Bench</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98684</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>98674</td>
      <td>WA</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>Woodland</td>
    </tr>
  </tbody>
</table>
</div>




```python
#amend values in rows 16 & 19 of the zip col

df.loc[16,'zip'] = '24220'

df.loc[19, 'zip'] = '23315'

#view result

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>region</th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98815</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>98817</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan Falls</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98665</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98822</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>98823</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Ephrata</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>98828</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Malaga</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>98845</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Palisades</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Peshastin</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>24220</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98821</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98811</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>98802</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>East Wenatchee Bench</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>None</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98684</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>98674</td>
      <td>WA</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>Woodland</td>
    </tr>
  </tbody>
</table>
</div>



### Using combine_first to create a final master table

df is now complete and ready to be merged into our master table, again using the combine_first function which enables us to insert the correct values into the NaN values in the master table.


```python
#combine tables on 'zip' into new master table: data

data = df.set_index('zip').combine_first(merged.set_index('zip')).reset_index()
```


```python
#view result

data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>city</th>
      <th>county</th>
      <th>median_val</th>
      <th>nulls</th>
      <th>region</th>
      <th>state</th>
      <th>suburb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>23315</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>VA</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>24220</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>VA</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98664</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>264737.0</td>
      <td>4.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>WA</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>214661.0</td>
      <td>4.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>WA</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98670</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>157999.0</td>
      <td>1.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>WA</td>
      <td>Wahkiacus</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98671</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>259673.0</td>
      <td>0.0</td>
      <td>98671</td>
      <td>WA</td>
      <td>Washougal</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98672</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>286706.0</td>
      <td>1.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>WA</td>
      <td>White Salmon</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98673</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>73255.0</td>
      <td>1.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>WA</td>
      <td>Centerville</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98674</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>239860.0</td>
      <td>4.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>WA</td>
      <td>Woodland</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98675</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>269505.0</td>
      <td>4.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>WA</td>
      <td>Yacolt</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98682</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>202697.0</td>
      <td>0.0</td>
      <td>98682</td>
      <td>WA</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>246936.0</td>
      <td>0.0</td>
      <td>98683</td>
      <td>WA</td>
      <td>Vancounver</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98684</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>209519.0</td>
      <td>4.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>WA</td>
      <td>Vancouver</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98685</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>262531.0</td>
      <td>0.0</td>
      <td>98685</td>
      <td>WA</td>
      <td>Felida</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98686</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>262738.0</td>
      <td>0.0</td>
      <td>98686</td>
      <td>WA</td>
      <td>Mount Vista</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>229814.0</td>
      <td>4.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>WA</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98802</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>209714.0</td>
      <td>4.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>WA</td>
      <td>East Wenatchee Bench</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98811</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>157458.0</td>
      <td>4.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>WA</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98812</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>126280.0</td>
      <td>1.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>WA</td>
      <td>Brewster</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98813</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>85479.5</td>
      <td>4.0</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>WA</td>
      <td>Bridgeport</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98814</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>233316.0</td>
      <td>1.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>WA</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98815</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>225056.0</td>
      <td>4.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>WA</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98816</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>361263.5</td>
      <td>4.0</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>WA</td>
      <td>Chelan</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98817</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>150035.5</td>
      <td>4.0</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>WA</td>
      <td>Chelan Falls</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98819</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>124513.0</td>
      <td>1.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>WA</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98821</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>193236.0</td>
      <td>4.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>WA</td>
      <td>Cashmere</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98822</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>188713.0</td>
      <td>4.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>WA</td>
      <td>Entiat</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98823</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>130578.0</td>
      <td>4.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>WA</td>
      <td>Ephrata</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98826</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>333323.0</td>
      <td>4.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>WA</td>
      <td>Leavenworth</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98827</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>136080.0</td>
      <td>1.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>WA</td>
      <td>Loomis</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98828</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>199534.0</td>
      <td>4.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>WA</td>
      <td>Malaga</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98829</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>108277.0</td>
      <td>1.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>WA</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98830</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>104139.0</td>
      <td>4.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>WA</td>
      <td>Mansfield</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98831</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>317479.0</td>
      <td>4.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>WA</td>
      <td>Manson</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98832</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>105315.0</td>
      <td>4.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>WA</td>
      <td>Krupp</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98833</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>284704.0</td>
      <td>1.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>WA</td>
      <td>Mazama</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98834</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>166287.5</td>
      <td>1.0</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>WA</td>
      <td>Methow</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98836</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>230441.5</td>
      <td>4.0</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>WA</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98837</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>163048.0</td>
      <td>4.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>WA</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98840</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>133589.0</td>
      <td>1.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>WA</td>
      <td>Okanogan</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98841</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>128936.0</td>
      <td>1.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>WA</td>
      <td>Omak</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98843</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>288216.0</td>
      <td>4.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>WA</td>
      <td>Orondo</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98844</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>140695.0</td>
      <td>1.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>WA</td>
      <td>Oroville</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98845</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>155664.0</td>
      <td>4.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>WA</td>
      <td>Palisades</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98846</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>193119.0</td>
      <td>1.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>WA</td>
      <td>Pateros</td>
    </tr>
    <tr>
      <th>45</th>
      <td>98847</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>221684.0</td>
      <td>4.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>WA</td>
      <td>Peshastin</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Birch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>4.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Carrs</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>4.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#re-arrange cols order using reindex

final_data = data.reindex(['zip', 'state', 'city', 'county', 'suburb', 'median_val', 'region'], axis=1)
```


```python
#view result

final_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>24220</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98674</td>
      <td>WA</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>Woodland</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98684</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98802</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>East Wenatchee Bench</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98811</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98815</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98817</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan Falls</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98821</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98822</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98823</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Ephrata</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98828</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Malaga</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98845</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Palisades</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
    </tr>
    <tr>
      <th>45</th>
      <td>98847</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Peshastin</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
  </tbody>
</table>
</div>



Now that the master table is ready, let's run a few final tidy-up tests to ensure here aren't any loose ends by checking for duplicates and dealing with any remaining nulls.


```python
#check for duplicates in the region row

final_data.duplicated(subset=['region'], keep=False)
```




    0     False
    1     False
    2     False
    3     False
    4     False
    5     False
    6     False
    7     False
    8     False
    9     False
    10    False
    11    False
    12    False
    13    False
    14    False
    15    False
    16    False
    17    False
    18    False
    19    False
    20    False
    21    False
    22    False
    23    False
    24    False
    25    False
    26    False
    27    False
    28    False
    29    False
    30    False
    31    False
    32    False
    33    False
    34    False
    35    False
    36    False
    37    False
    38    False
    39    False
    40    False
    41    False
    42    False
    43    False
    44    False
    45    False
    dtype: bool




```python
#insert missing data in rows 0 & 1 in median_val col

final_data.loc[0, 'median_val'] = 56042.0

final_data.loc[1, 'median_val'] = 164069.0
```


```python
#check values are now in place 

final_data.iloc[0:2]

#drop unwanted rows

final_data.drop([46,47], axis=0, inplace=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>24220</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
    </tr>
  </tbody>
</table>
</div>




```python
#view result

final_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>24220</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98665</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98674</td>
      <td>WA</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>Woodland</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98684</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98802</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>East Wenatchee Bench</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98811</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98815</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98817</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan Falls</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98821</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98822</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>27</th>
      <td>98823</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Ephrata</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98828</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Malaga</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98845</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Palisades</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
    </tr>
    <tr>
      <th>45</th>
      <td>98847</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Peshastin</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
  </tbody>
</table>
</div>



Our final data is now ready for analysis. We've managed to extract data from an API and reshape 10,000 rows across 3 different tables into one table which consolidates the data by zip code. Let's see what insights might be hidden in the data.

## 6. Exploratory data analysis & summary statistics 

Some of the commercial questions we might want to ask of the data might be:

- what is the average house price in Washington State?

- which are the most expensive counties or suburbs based on median family home sale price?

- which are the least expensive/most affordable counties or suburbs?

- how have house prices in the most/least expensive areas changed over time - has there been a growth trend? If so, by how much have prices risen year on year?

We can start with simple sorting and move on to a couple of visualisations.


```python
#sort table by median sale price, high to low

final_data = final_data.sort_values(by='median_val', ascending=False).reset_index(drop=True)
```


```python
#sort the index

final_data.sort_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
    </tr>
    <tr>
      <th>10</th>
      <td>98671</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Washougal</td>
      <td>259673.0</td>
      <td>98671</td>
    </tr>
    <tr>
      <th>11</th>
      <td>98683</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>246936.0</td>
      <td>98683</td>
    </tr>
    <tr>
      <th>12</th>
      <td>98674</td>
      <td>WA</td>
      <td>Longview</td>
      <td>Cowlitz County</td>
      <td>Woodland</td>
      <td>239860.0</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98814</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Carlton</td>
      <td>233316.0</td>
      <td>98814; WA; Okanogan County; Carlton</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98836</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>230441.5</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98801</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>NaN</td>
      <td>229814.0</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98815</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>225056.0</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98847</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Peshastin</td>
      <td>221684.0</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98665</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>214661.0</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>98802</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>East Wenatchee Bench</td>
      <td>209714.0</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>98684</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>209519.0</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>98682</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Vancounver</td>
      <td>202697.0</td>
      <td>98682</td>
    </tr>
    <tr>
      <th>22</th>
      <td>98828</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Malaga</td>
      <td>199534.0</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
    </tr>
    <tr>
      <th>23</th>
      <td>98821</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Cashmere</td>
      <td>193236.0</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
    </tr>
    <tr>
      <th>24</th>
      <td>98846</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Pateros</td>
      <td>193119.0</td>
      <td>98846; WA; Okanogan County; Pateros</td>
    </tr>
    <tr>
      <th>25</th>
      <td>98822</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>188713.0</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>26</th>
      <td>98834</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Methow</td>
      <td>166287.5</td>
      <td>98834; WA; Okanogan County; Methow</td>
    </tr>
    <tr>
      <th>27</th>
      <td>24220</td>
      <td>VA</td>
      <td>Big Stone Gap</td>
      <td>Dickenson County</td>
      <td>NaN</td>
      <td>164069.0</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
    </tr>
    <tr>
      <th>28</th>
      <td>98837</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>NaN</td>
      <td>163048.0</td>
      <td>98837; WA; Moses Lake; Grant County</td>
    </tr>
    <tr>
      <th>29</th>
      <td>98670</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Wahkiacus</td>
      <td>157999.0</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
    </tr>
    <tr>
      <th>30</th>
      <td>98811</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Entiat</td>
      <td>157458.0</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
    </tr>
    <tr>
      <th>31</th>
      <td>98845</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Palisades</td>
      <td>155664.0</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
    </tr>
    <tr>
      <th>32</th>
      <td>98817</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan Falls</td>
      <td>150035.5</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
    </tr>
    <tr>
      <th>33</th>
      <td>98844</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Oroville</td>
      <td>140695.0</td>
      <td>98844; WA; Okanogan County; Oroville</td>
    </tr>
    <tr>
      <th>34</th>
      <td>98827</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Loomis</td>
      <td>136080.0</td>
      <td>98827; WA; Okanogan County; Loomis</td>
    </tr>
    <tr>
      <th>35</th>
      <td>98840</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>133589.0</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>36</th>
      <td>98823</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Ephrata</td>
      <td>130578.0</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
    </tr>
    <tr>
      <th>37</th>
      <td>98841</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>128936.0</td>
      <td>98841; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>38</th>
      <td>98812</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Brewster</td>
      <td>126280.0</td>
      <td>98812; WA; Okanogan County; Brewster</td>
    </tr>
    <tr>
      <th>39</th>
      <td>98819</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Omak</td>
      <td>124513.0</td>
      <td>98819; WA; Okanogan County; Omak</td>
    </tr>
    <tr>
      <th>40</th>
      <td>98829</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Okanogan</td>
      <td>108277.0</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
    </tr>
    <tr>
      <th>45</th>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>NaN</td>
      <td>56042.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#extract the top 10 most expensive zip codes

top_10 = final_data.head(10)
top_10
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>98816</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Chelan</td>
      <td>361263.5</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98826</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Leavenworth</td>
      <td>333323.0</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
    </tr>
    <tr>
      <th>2</th>
      <td>98831</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Chelan County</td>
      <td>Manson</td>
      <td>317479.0</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
    </tr>
    <tr>
      <th>3</th>
      <td>98843</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Orondo</td>
      <td>288216.0</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
    </tr>
    <tr>
      <th>4</th>
      <td>98672</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>White Salmon</td>
      <td>286706.0</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
    </tr>
    <tr>
      <th>5</th>
      <td>98833</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Okanogan County</td>
      <td>Mazama</td>
      <td>284704.0</td>
      <td>98833; WA; Okanogan County; Mazama</td>
    </tr>
    <tr>
      <th>6</th>
      <td>98675</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Yacolt</td>
      <td>269505.0</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>98664</td>
      <td>WA</td>
      <td>Portland-Vancouver-Hillsboro</td>
      <td>Clark County</td>
      <td>Vancouver</td>
      <td>264737.0</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>98686</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Mount Vista</td>
      <td>262738.0</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>9</th>
      <td>98685</td>
      <td>WA</td>
      <td>Portland</td>
      <td>Clark County</td>
      <td>Felida</td>
      <td>262531.0</td>
      <td>98685</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_10.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10 entries, 0 to 9
    Data columns (total 7 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   zip         10 non-null     object 
     1   state       10 non-null     object 
     2   city        8 non-null      object 
     3   county      10 non-null     object 
     4   suburb      10 non-null     object 
     5   median_val  10 non-null     float64
     6   region      10 non-null     object 
    dtypes: float64(1), object(6)
    memory usage: 688.0+ bytes


### Basic statistical calculations


```python
#what's the avg house price in WA?

final_data.median_val.mean()
```




    195585.11956521738




```python
#what's the average house price amongst the top 10 suburbs?

top_10.median_val.mean()
```




    293120.25




```python
#what are the 5 least expensive zip codes & suburbs?

final_data.median_val.nsmallest()
```




    45     56042.0
    44     73255.0
    43     85479.5
    42    104139.0
    41    105315.0
    Name: median_val, dtype: float64




```python
#extract relevant rows for least expensive zip codes

least_exp = final_data.iloc[41:46]
```


```python
least_exp.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5 entries, 41 to 45
    Data columns (total 7 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   zip         5 non-null      object 
     1   state       5 non-null      object 
     2   city        4 non-null      object 
     3   county      5 non-null      object 
     4   suburb      4 non-null      object 
     5   median_val  5 non-null      float64
     6   region      5 non-null      object 
    dtypes: float64(1), object(6)
    memory usage: 412.0+ bytes



```python
#show results

least_exp
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>zip</th>
      <th>state</th>
      <th>city</th>
      <th>county</th>
      <th>suburb</th>
      <th>median_val</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>41</th>
      <td>98832</td>
      <td>WA</td>
      <td>Moses Lake</td>
      <td>Grant County</td>
      <td>Krupp</td>
      <td>105315.0</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
    </tr>
    <tr>
      <th>42</th>
      <td>98830</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Mansfield</td>
      <td>104139.0</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
    </tr>
    <tr>
      <th>43</th>
      <td>98813</td>
      <td>WA</td>
      <td>Wenatchee</td>
      <td>Douglas County</td>
      <td>Bridgeport</td>
      <td>85479.5</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
    </tr>
    <tr>
      <th>44</th>
      <td>98673</td>
      <td>WA</td>
      <td>NaN</td>
      <td>Klickitat County</td>
      <td>Centerville</td>
      <td>73255.0</td>
      <td>98673; WA; Klickitat County; Centerville</td>
    </tr>
    <tr>
      <th>45</th>
      <td>23315</td>
      <td>VA</td>
      <td>Virginia Beach-Norfolk-Newport News</td>
      <td>Isle of Wight County</td>
      <td>Isle of Wight County</td>
      <td>56042.0</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#what's the average house price per county?

counties=final_data.groupby(['county'])['median_val'].mean().sort_values(ascending=False)
```


```python
#view result

counties
```




    county
    Clark County             246915.000000
     Cowlitz County          239860.000000
     Clark County            239605.500000
     Chelan County           234003.125000
     Klickitat County        172653.333333
     Douglas County          168642.500000
     Dickenson County        164069.000000
     Okanogan County         161436.045455
     Grant County            132980.333333
     Isle of Wight County     56042.000000
    Name: median_val, dtype: float64



Looking at the above summary it seems Clark County appears twice due to a spacing error. let's use str.strip() to remedy that.


```python
#check unique values in the county col

final_data['county'].unique()
```




    array([' Chelan County', ' Douglas County', ' Klickitat County',
           ' Okanogan County', ' Clark County', 'Clark County',
           ' Cowlitz County', ' Dickenson County', ' Grant County',
           ' Isle of Wight County'], dtype=object)




```python
#strip spaces in the relevant column

final_data['county'] = final_data['county'].str.strip()
```


```python
#check the issue is fixed

final_data['county'].unique()
```




    array(['Chelan County', 'Douglas County', 'Klickitat County',
           'Okanogan County', 'Clark County', 'Cowlitz County',
           'Dickenson County', 'Grant County', 'Isle of Wight County'],
          dtype=object)




```python
#re-run the calculation per county and show high to low

counties_2=final_data.groupby(['county'])['median_val'].mean().sort_values(ascending=False)
```


```python
#view results

counties_2
```




    county
    Clark County            243666.333333
    Cowlitz County          239860.000000
    Chelan County           234003.125000
    Klickitat County        172653.333333
    Douglas County          168642.500000
    Dickenson County        164069.000000
    Okanogan County         161436.045455
    Grant County            132980.333333
    Isle of Wight County     56042.000000
    Name: median_val, dtype: float64




```python
#convert to DF

county_df = counties_2.to_frame()

county_df.sort_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>median_val</th>
    </tr>
    <tr>
      <th>county</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Chelan County</th>
      <td>234003.125000</td>
    </tr>
    <tr>
      <th>Clark County</th>
      <td>243666.333333</td>
    </tr>
    <tr>
      <th>Cowlitz County</th>
      <td>239860.000000</td>
    </tr>
    <tr>
      <th>Dickenson County</th>
      <td>164069.000000</td>
    </tr>
    <tr>
      <th>Douglas County</th>
      <td>168642.500000</td>
    </tr>
    <tr>
      <th>Grant County</th>
      <td>132980.333333</td>
    </tr>
    <tr>
      <th>Isle of Wight County</th>
      <td>56042.000000</td>
    </tr>
    <tr>
      <th>Klickitat County</th>
      <td>172653.333333</td>
    </tr>
    <tr>
      <th>Okanogan County</th>
      <td>161436.045455</td>
    </tr>
  </tbody>
</table>
</div>




```python
county_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 9 entries, Clark County to Isle of Wight County
    Data columns (total 1 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   median_val  9 non-null      float64
    dtypes: float64(1)
    memory usage: 464.0+ bytes



```python
county_df = county_df.reset_index()
```


```python
county_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 9 entries, 0 to 8
    Data columns (total 2 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   county      9 non-null      object 
     1   median_val  9 non-null      float64
    dtypes: float64(1), object(1)
    memory usage: 272.0+ bytes


## 7. Visualisation in matplotlib


```python
#plot most expensive suburbs

import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(15,8))

ax.bar(top_10['suburb'], top_10['median_val']) 

ax.set_ylabel('Median sale price - Single family home (USD)')

ax.set_title('Washington State - most expensive suburbs 2020')

plt.show()
```


![png](output_138_0.png)


Plotting the most expensive suburbs shows that the median house price in the top 3 suburbs is over $300K (although Chelan (at number 1) is over $350K and Manson (at number 3) is just over $300K. 

The next three suburbs (Orondo, White Salmon and Mazama) are almost identical in price and so are the bottom 3 suburbs in this group (Vancouver, Mount Vista and Felida) all around he $250K mark.

Let's also check the general price distribution across all 46 zip codes in our data. 

It looks like most houses in the data are in the $200K-$250K mark, although there are also quite a few in the $120K-$170K category.


```python
final_data['median_val'].hist(bins=10, figsize=(15,8))
plt.show()
```


![png](output_140_0.png)


Next let's examine which counties had the most and least expensive properties in 2020.


```python
fig, ax = plt.subplots(figsize=(15,8))

ax.bar(county_df['county'], county_df['median_val'], color='m')

ax.set_title('WA counties by average single family home sale price')

ax.set_ylabel('Sale price (USD)')

plt.show()
```


![png](output_142_0.png)


## 8. Treating date data

In order to explore how house prices in our data have changed over time, we can visualise a time series given the data contains the sale date for each of the 10,000 properties in the data:


```python
z_merged
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-11-30</td>
      <td>377249.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-10-31</td>
      <td>374582.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-09-30</td>
      <td>369733.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-08-31</td>
      <td>366760.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>2020-07-31</td>
      <td>336538.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-06-30</td>
      <td>168443.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-05-31</td>
      <td>167465.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-04-30</td>
      <td>165644.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-03-31</td>
      <td>164264.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>2012-02-29</td>
      <td>163415.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 9 columns</p>
</div>




```python
#output data types
z_merged.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 10000 entries, 0 to 9999
    Data columns (total 9 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   indicator_id  10000 non-null  object 
     1   region_id     10000 non-null  object 
     2   date          10000 non-null  object 
     3   value         10000 non-null  float64
     4   indicator     10000 non-null  object 
     5   category      10000 non-null  object 
     6   region_type   10000 non-null  object 
     7   region        10000 non-null  object 
     8   zip           10000 non-null  object 
    dtypes: float64(1), object(8)
    memory usage: 781.2+ KB


The above check shows us the date column is a string/object type so let's convert it into datetime first.


```python
import datetime as dt

#convert date col to datetime
z_merged['date'] = pd.to_datetime(z_merged['date'])
```


```python
#assert that conversion took place
assert z_merged['date'].dtype == 'datetime64[ns]'
```


```python
#verify column is now datetime type

z_merged.date
```




    0      2020-11-30
    1      2020-10-31
    2      2020-09-30
    3      2020-08-31
    4      2020-07-31
              ...    
    9995   2012-06-30
    9996   2012-05-31
    9997   2012-04-30
    9998   2012-03-31
    9999   2012-02-29
    Name: date, Length: 10000, dtype: datetime64[ns]



It looks like the date format could be made clearer as it's currently year-month-date so let's change the format to date-month-year instead.


```python
z_merged['date'] = z_merged['date'].dt.strftime("%d-%m-%Y")

z_merged.date
```




    0       30-11-2020
    1       31-10-2020
    2       30-09-2020
    3       31-08-2020
    4       31-07-2020
               ...    
    9995    30-06-2012
    9996    31-05-2012
    9997    30-04-2012
    9998    31-03-2012
    9999    29-02-2012
    Name: date, Length: 10000, dtype: object




```python
#set the date col as index so it's easier to analyse

date_2 = z_merged.set_index(pd.DatetimeIndex(z_merged['date']))

date_2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-11-30</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>30-11-2020</td>
      <td>377249.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2020-10-31</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>31-10-2020</td>
      <td>374582.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2020-09-30</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>30-09-2020</td>
      <td>369733.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2020-08-31</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>31-08-2020</td>
      <td>366760.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>2020-07-31</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>31-07-2020</td>
      <td>336538.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2012-06-30</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>30-06-2012</td>
      <td>168443.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>2012-05-31</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>31-05-2012</td>
      <td>167465.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>2012-04-30</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>30-04-2012</td>
      <td>165644.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>2012-03-31</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>31-03-2012</td>
      <td>164264.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
    <tr>
      <th>2012-02-29</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>29-02-2012</td>
      <td>163415.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 9 columns</p>
</div>




```python
date_2.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 10000 entries, 2020-11-30 to 2012-02-29
    Data columns (total 9 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   indicator_id  10000 non-null  object 
     1   region_id     10000 non-null  object 
     2   date          10000 non-null  object 
     3   value         10000 non-null  float64
     4   indicator     10000 non-null  object 
     5   category      10000 non-null  object 
     6   region_type   10000 non-null  object 
     7   region        10000 non-null  object 
     8   zip           10000 non-null  object 
    dtypes: float64(1), object(8)
    memory usage: 781.2+ KB



```python
date_2.index = date_2.index.strftime("%d-%m-%Y")
```


```python
#verify index is now in correct format

date_2.index
```




    Index(['30-11-2020', '31-10-2020', '30-09-2020', '31-08-2020', '31-07-2020',
           '30-06-2020', '31-05-2020', '30-04-2020', '31-03-2020', '29-02-2020',
           ...
           '30-11-2012', '31-10-2012', '30-09-2012', '31-08-2012', '31-07-2012',
           '30-06-2012', '31-05-2012', '30-04-2012', '31-03-2012', '29-02-2012'],
          dtype='object', name='date', length=10000)



Now that the date format and type is sorted, we can run various queries, such as filtering for all sales that took place on a particular date (using .loc), if we'd like to drill down into this specific data.


```python
date_2.loc["30-11-2020"]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99999</td>
      <td>30-11-2020</td>
      <td>377249.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98847; WA; Wenatchee; Chelan County; Peshastin</td>
      <td>98847</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99998</td>
      <td>30-11-2020</td>
      <td>298425.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98846; WA; Okanogan County; Pateros</td>
      <td>98846</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99997</td>
      <td>30-11-2020</td>
      <td>301830.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98845; WA; Wenatchee; Douglas County; Palisades</td>
      <td>98845</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99996</td>
      <td>30-11-2020</td>
      <td>209527.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98844; WA; Okanogan County; Oroville</td>
      <td>98844</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99995</td>
      <td>30-11-2020</td>
      <td>469238.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98843; WA; Wenatchee; Douglas County; Orondo</td>
      <td>98843</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99994</td>
      <td>30-11-2020</td>
      <td>199263.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98841; WA; Okanogan County; Omak</td>
      <td>98841</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99993</td>
      <td>30-11-2020</td>
      <td>196253.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98840; WA; Okanogan County; Okanogan</td>
      <td>98840</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99992</td>
      <td>30-11-2020</td>
      <td>249989.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98837; WA; Moses Lake; Grant County</td>
      <td>98837</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99991</td>
      <td>30-11-2020</td>
      <td>403538.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98836; WA; Wenatchee; Chelan County</td>
      <td>98836</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99990</td>
      <td>30-11-2020</td>
      <td>245795.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98834; WA; Okanogan County; Methow</td>
      <td>98834</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>9999</td>
      <td>30-11-2020</td>
      <td>203287.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>city</td>
      <td>Carrsville; VA; Virginia Beach-Norfolk-Newport...</td>
      <td>Carrs</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99989</td>
      <td>30-11-2020</td>
      <td>431157.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98833; WA; Okanogan County; Mazama</td>
      <td>98833</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99988</td>
      <td>30-11-2020</td>
      <td>131935.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98832; WA; Moses Lake; Grant County; Krupp</td>
      <td>98832</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99987</td>
      <td>30-11-2020</td>
      <td>501804.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98831; WA; Wenatchee; Chelan County; Manson</td>
      <td>98831</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99986</td>
      <td>30-11-2020</td>
      <td>179250.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98830; WA; Wenatchee; Douglas County; Mansfield</td>
      <td>98830</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99985</td>
      <td>30-11-2020</td>
      <td>166424.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98829; WA; Okanogan County; Okanogan</td>
      <td>98829</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99984</td>
      <td>30-11-2020</td>
      <td>342974.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98828; WA; Wenatchee; Chelan County; Malaga</td>
      <td>98828</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99983</td>
      <td>30-11-2020</td>
      <td>204983.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98827; WA; Okanogan County; Loomis</td>
      <td>98827</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99982</td>
      <td>30-11-2020</td>
      <td>587733.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98826; WA; Wenatchee; Chelan County; Leavenworth</td>
      <td>98826</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99980</td>
      <td>30-11-2020</td>
      <td>213399.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98823; WA; Moses Lake; Grant County; Ephrata</td>
      <td>98823</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>9998</td>
      <td>30-11-2020</td>
      <td>55048.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>city</td>
      <td>Birchleaf; VA; Big Stone Gap; Dickenson County</td>
      <td>Birch</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99979</td>
      <td>30-11-2020</td>
      <td>332764.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98822; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98822</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99978</td>
      <td>30-11-2020</td>
      <td>324140.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98821; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98821</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99977</td>
      <td>30-11-2020</td>
      <td>175416.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98819; WA; Okanogan County; Omak</td>
      <td>98819</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99976</td>
      <td>30-11-2020</td>
      <td>282882.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98817; WA; Wenatchee; Chelan County; Chelan Falls</td>
      <td>98817</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-11-2020</td>
      <td>546571.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99974</td>
      <td>30-11-2020</td>
      <td>386607.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98815; WA; Wenatchee; Chelan County; Cashmere</td>
      <td>98815</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99973</td>
      <td>30-11-2020</td>
      <td>345671.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98814; WA; Okanogan County; Carlton</td>
      <td>98814</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99972</td>
      <td>30-11-2020</td>
      <td>176869.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98813; WA; Wenatchee; Douglas County; Bridgeport</td>
      <td>98813</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99971</td>
      <td>30-11-2020</td>
      <td>233105.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98812; WA; Okanogan County; Brewster</td>
      <td>98812</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99970</td>
      <td>30-11-2020</td>
      <td>236420.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98811; WA; Wenatchee; Chelan County; Entiat</td>
      <td>98811</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99968</td>
      <td>30-11-2020</td>
      <td>391524.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98802; WA; Wenatchee; Douglas County; East Wen...</td>
      <td>98802</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99967</td>
      <td>30-11-2020</td>
      <td>357296.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98801; WA; Wenatchee; Chelan County</td>
      <td>98801</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99965</td>
      <td>30-11-2020</td>
      <td>457770.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98686</td>
      <td>98686</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99964</td>
      <td>30-11-2020</td>
      <td>454736.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98685</td>
      <td>98685</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99963</td>
      <td>30-11-2020</td>
      <td>383128.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98684; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98684</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99962</td>
      <td>30-11-2020</td>
      <td>429451.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98683</td>
      <td>98683</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99961</td>
      <td>30-11-2020</td>
      <td>381835.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98682</td>
      <td>98682</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99960</td>
      <td>30-11-2020</td>
      <td>479090.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98675; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98675</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99959</td>
      <td>30-11-2020</td>
      <td>430624.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98674; WA; Longview; Cowlitz County; Woodland</td>
      <td>98674</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99958</td>
      <td>30-11-2020</td>
      <td>105554.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98673; WA; Klickitat County; Centerville</td>
      <td>98673</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99957</td>
      <td>30-11-2020</td>
      <td>476848.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98672; WA; Klickitat County; White Salmon</td>
      <td>98672</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99956</td>
      <td>30-11-2020</td>
      <td>459556.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98671</td>
      <td>98671</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99955</td>
      <td>30-11-2020</td>
      <td>281811.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98670; WA; Klickitat County; Wahkiacus</td>
      <td>98670</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99951</td>
      <td>30-11-2020</td>
      <td>389742.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98665; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98665</td>
    </tr>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99950</td>
      <td>30-11-2020</td>
      <td>373270.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98664; WA; Portland-Vancouver-Hillsboro; Clark...</td>
      <td>98664</td>
    </tr>
  </tbody>
</table>
</div>



## 9. Zooming in on the most expensive suburb


```python
#filter all rows per specific zipcode and place in new DF: chelan_sub

chelan_sub = date_2[date_2['zip'] == '98816']
```


```python
chelan_sub
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-11-2020</td>
      <td>546571.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-10-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-10-2020</td>
      <td>537783.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>30-09-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-09-2020</td>
      <td>528608.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-08-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-08-2020</td>
      <td>484191.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-07-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-07-2020</td>
      <td>463524.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>31-10-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-10-2005</td>
      <td>276391.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>30-09-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-09-2005</td>
      <td>266615.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-08-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-08-2005</td>
      <td>257738.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-07-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-07-2005</td>
      <td>253343.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>30-06-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-06-2005</td>
      <td>250625.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
  </tbody>
</table>
<p>186 rows × 9 columns</p>
</div>




```python
#verify index is now sorted in ascending order, oldest to newest

chelan_sub.index
```




    Index(['30-11-2020', '31-10-2020', '30-09-2020', '31-08-2020', '31-07-2020',
           '30-06-2020', '31-05-2020', '30-04-2020', '31-03-2020', '29-02-2020',
           ...
           '31-03-2006', '28-02-2006', '31-01-2006', '31-12-2005', '30-11-2005',
           '31-10-2005', '30-09-2005', '31-08-2005', '31-07-2005', '30-06-2005'],
          dtype='object', name='date', length=186)




```python
chelan_sub.sort_index(ascending=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>28-02-2006</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>28-02-2006</td>
      <td>306668.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>28-02-2007</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>28-02-2007</td>
      <td>345143.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>28-02-2009</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>28-02-2009</td>
      <td>361419.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>28-02-2010</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>28-02-2010</td>
      <td>340829.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>28-02-2011</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>28-02-2011</td>
      <td>319612.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>31-12-2015</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-12-2015</td>
      <td>413351.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-12-2016</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-12-2016</td>
      <td>419667.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-12-2017</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-12-2017</td>
      <td>451802.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-12-2018</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-12-2018</td>
      <td>488644.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
    <tr>
      <th>31-12-2019</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-12-2019</td>
      <td>509772.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
    </tr>
  </tbody>
</table>
<p>186 rows × 9 columns</p>
</div>




```python
chelan_sub.index.unique()
```




    Index(['28-02-2006', '28-02-2007', '28-02-2009', '28-02-2010', '28-02-2011',
           '28-02-2013', '28-02-2014', '28-02-2015', '28-02-2017', '28-02-2018',
           ...
           '31-12-2010', '31-12-2011', '31-12-2012', '31-12-2013', '31-12-2014',
           '31-12-2015', '31-12-2016', '31-12-2017', '31-12-2018', '31-12-2019'],
          dtype='object', name='date', length=186)



### Extracting year from date value 

Given we want to plot the year-on-year change in house prices, we need to extract the year from each row, then group values by year.


```python
#extract year from index column and insert into new 'year' column

chelan_sub['year'] = pd.DatetimeIndex(chelan_sub.index).year
chelan_sub
```


```python

```

    <ipython-input-228-fb256ab476fb>:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      chelan_sub['year'] = pd.DatetimeIndex(chelan_sub.index).year





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>indicator_id</th>
      <th>region_id</th>
      <th>date</th>
      <th>value</th>
      <th>indicator</th>
      <th>category</th>
      <th>region_type</th>
      <th>region</th>
      <th>zip</th>
      <th>year</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30-11-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-11-2020</td>
      <td>546571.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>31-10-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-10-2020</td>
      <td>537783.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>30-09-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-09-2020</td>
      <td>528608.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>31-08-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-08-2020</td>
      <td>484191.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>31-07-2020</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-07-2020</td>
      <td>463524.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>31-10-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-10-2005</td>
      <td>276391.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2005</td>
    </tr>
    <tr>
      <th>30-09-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-09-2005</td>
      <td>266615.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2005</td>
    </tr>
    <tr>
      <th>31-08-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-08-2005</td>
      <td>257738.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2005</td>
    </tr>
    <tr>
      <th>31-07-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>31-07-2005</td>
      <td>253343.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2005</td>
    </tr>
    <tr>
      <th>30-06-2005</th>
      <td>ZSFH</td>
      <td>99975</td>
      <td>30-06-2005</td>
      <td>250625.0</td>
      <td>ZHVI Single-Family Homes Time Series ($)</td>
      <td>Home values</td>
      <td>zip</td>
      <td>98816; WA; Wenatchee; Chelan County; Chelan</td>
      <td>98816</td>
      <td>2005</td>
    </tr>
  </tbody>
</table>
<p>186 rows × 10 columns</p>
</div>



### Calculating average values per year


```python
#group data by year then calculate avg house price per year

chelan = chelan_sub.groupby(['year'])['value'].mean()
```


```python
#view results

chelan
```




    year
    2005    269083.428571
    2006    326465.916667
    2007    355777.750000
    2008    378137.500000
    2009    350567.083333
    2010    334128.750000
    2011    315240.500000
    2012    311768.500000
    2013    331951.500000
    2014    358883.333333
    2015    391428.666667
    2016    414788.416667
    2017    440305.250000
    2018    470456.000000
    2019    496356.833333
    2020    520419.454545
    Name: value, dtype: float64




```python
#convert to DF

chelandf = chelan.to_frame()

chelandf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2005</th>
      <td>269083.428571</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>326465.916667</td>
    </tr>
    <tr>
      <th>2007</th>
      <td>355777.750000</td>
    </tr>
    <tr>
      <th>2008</th>
      <td>378137.500000</td>
    </tr>
    <tr>
      <th>2009</th>
      <td>350567.083333</td>
    </tr>
    <tr>
      <th>2010</th>
      <td>334128.750000</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>315240.500000</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>311768.500000</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>331951.500000</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>358883.333333</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>391428.666667</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>414788.416667</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>440305.250000</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>470456.000000</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>496356.833333</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>520419.454545</td>
    </tr>
  </tbody>
</table>
</div>




```python
chelandf.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 16 entries, 2005 to 2020
    Data columns (total 1 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   value   16 non-null     float64
    dtypes: float64(1)
    memory usage: 256.0 bytes


### Plotting a time series

Now that we have the avg house price by year for the Chelan suburb from 2005 to 2020 we can plot the data to see how it's been trending.


```python
fig,ax = plt.subplots(figsize=(15,8))

ax.plot(chelandf.index, chelandf['value'], marker='o')

ax.set_title('Chelan Wenatchee WA single-family home sale prices 2005-2020')

ax.set_ylabel('Avg sale price (USD)')

plt.show()
```


![png](output_175_0.png)


### Calculating percentage change year on year 

We can see a general trend upwards over the past 15 years with an almost 25% dip between 2008-2012 (presumably due to the financial crisis). Prices have however gone consistently up since 2012. If you wanted to see the actual year-on-year percentage change, pandas pct_change() function is super helpful.


```python
#add new column for pct change year on year

chelandf['pct_change_yr'] = chelandf['value'].pct_change()
```


```python
#view results
chelandf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
      <th>pct_change_yr</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2005</th>
      <td>269083.428571</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>326465.916667</td>
      <td>0.213252</td>
    </tr>
    <tr>
      <th>2007</th>
      <td>355777.750000</td>
      <td>0.089785</td>
    </tr>
    <tr>
      <th>2008</th>
      <td>378137.500000</td>
      <td>0.062848</td>
    </tr>
    <tr>
      <th>2009</th>
      <td>350567.083333</td>
      <td>-0.072911</td>
    </tr>
    <tr>
      <th>2010</th>
      <td>334128.750000</td>
      <td>-0.046891</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>315240.500000</td>
      <td>-0.056530</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>311768.500000</td>
      <td>-0.011014</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>331951.500000</td>
      <td>0.064737</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>358883.333333</td>
      <td>0.081132</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>391428.666667</td>
      <td>0.090685</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>414788.416667</td>
      <td>0.059678</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>440305.250000</td>
      <td>0.061518</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>470456.000000</td>
      <td>0.068477</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>496356.833333</td>
      <td>0.055055</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>520419.454545</td>
      <td>0.048478</td>
    </tr>
  </tbody>
</table>
</div>



The above table neatly sets out the year on year price change. We can of course plot this!


```python
fig,ax = plt.subplots(figsize=(15,8))

ax.plot(chelandf.index, chelandf['pct_change_yr'], marker='v', color='m')

ax.set_title('Chelan Wenatchee WA - % change in single-family home sale prices 2005-2020')

ax.set_ylabel('% change')

plt.show()
```


![png](output_180_0.png)


Interesting to see a sharp drop of around 25% between 2006-2009, a slight rise in 2010 and full recovery from 2011 onwards with prices rising almost 15% to 2015. Since then, prices have slightly dropped over the past 5 years by around 5% and are still around 15% lower than pre-2006 levels.

If we wanted to see, for example, the cumulative price change over the past 15 years we can run the samle calculation as above with cumsum() at the end to show us the cumulative number. Overall, it looks like despite the downturn in 2008 which lasted a number of years, prices in this suburb have gone up by a total of 70% over 15 years.


```python
#add column for pct change cumulative

chelandf['pct_change_cumulative'] = chelandf['value'].pct_change().cumsum()

chelandf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
      <th>pct_change_yr</th>
      <th>pct_change_cumulative</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2005</th>
      <td>269083.428571</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>326465.916667</td>
      <td>0.213252</td>
      <td>0.213252</td>
    </tr>
    <tr>
      <th>2007</th>
      <td>355777.750000</td>
      <td>0.089785</td>
      <td>0.303037</td>
    </tr>
    <tr>
      <th>2008</th>
      <td>378137.500000</td>
      <td>0.062848</td>
      <td>0.365884</td>
    </tr>
    <tr>
      <th>2009</th>
      <td>350567.083333</td>
      <td>-0.072911</td>
      <td>0.292973</td>
    </tr>
    <tr>
      <th>2010</th>
      <td>334128.750000</td>
      <td>-0.046891</td>
      <td>0.246083</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>315240.500000</td>
      <td>-0.056530</td>
      <td>0.189553</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>311768.500000</td>
      <td>-0.011014</td>
      <td>0.178539</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>331951.500000</td>
      <td>0.064737</td>
      <td>0.243276</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>358883.333333</td>
      <td>0.081132</td>
      <td>0.324408</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>391428.666667</td>
      <td>0.090685</td>
      <td>0.415093</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>414788.416667</td>
      <td>0.059678</td>
      <td>0.474771</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>440305.250000</td>
      <td>0.061518</td>
      <td>0.536289</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>470456.000000</td>
      <td>0.068477</td>
      <td>0.604766</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>496356.833333</td>
      <td>0.055055</td>
      <td>0.659821</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>520419.454545</td>
      <td>0.048478</td>
      <td>0.708299</td>
    </tr>
  </tbody>
</table>
</div>



## 10. Zooming in on a decade

If we wanted to zoom in on the past decade we could filter our data as shown below using loc to select a specific year range and then adjust the pct change and cumulative pct change columns to only reflect the relevant period. 

As we can see below, while 2010-2012 experienced a price drop, overall, house prices in Chelan have risen by 46% over the past decade. 


```python
#zooming in on a decade
#subset the suburb DF by specific dates

last_decade = chelandf.loc['2010':'2020']

#view results
last_decade
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
      <th>pct_change_yr</th>
      <th>pct_change_cumulative</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010</th>
      <td>334128.750000</td>
      <td>-0.046891</td>
      <td>0.246083</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>315240.500000</td>
      <td>-0.056530</td>
      <td>0.189553</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>311768.500000</td>
      <td>-0.011014</td>
      <td>0.178539</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>331951.500000</td>
      <td>0.064737</td>
      <td>0.243276</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>358883.333333</td>
      <td>0.081132</td>
      <td>0.324408</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>391428.666667</td>
      <td>0.090685</td>
      <td>0.415093</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>414788.416667</td>
      <td>0.059678</td>
      <td>0.474771</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>440305.250000</td>
      <td>0.061518</td>
      <td>0.536289</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>470456.000000</td>
      <td>0.068477</td>
      <td>0.604766</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>496356.833333</td>
      <td>0.055055</td>
      <td>0.659821</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>520419.454545</td>
      <td>0.048478</td>
      <td>0.708299</td>
    </tr>
  </tbody>
</table>
</div>




```python
#edit the pct change columns to only reflect the last decade

last_decade['pct_change_yr'] = last_decade['value'].pct_change()

last_decade['pct_change_cumulative'] = last_decade['value'].pct_change().cumsum()

last_decade
```

    <ipython-input-238-62b9f5a71178>:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      last_decade['pct_change_yr'] = last_decade['value'].pct_change()
    <ipython-input-238-62b9f5a71178>:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      last_decade['pct_change_cumulative'] = last_decade['value'].pct_change().cumsum()





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
      <th>pct_change_yr</th>
      <th>pct_change_cumulative</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010</th>
      <td>334128.750000</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>315240.500000</td>
      <td>-0.056530</td>
      <td>-0.056530</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>311768.500000</td>
      <td>-0.011014</td>
      <td>-0.067544</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>331951.500000</td>
      <td>0.064737</td>
      <td>-0.002807</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>358883.333333</td>
      <td>0.081132</td>
      <td>0.078325</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>391428.666667</td>
      <td>0.090685</td>
      <td>0.169010</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>414788.416667</td>
      <td>0.059678</td>
      <td>0.228688</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>440305.250000</td>
      <td>0.061518</td>
      <td>0.290206</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>470456.000000</td>
      <td>0.068477</td>
      <td>0.358683</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>496356.833333</td>
      <td>0.055055</td>
      <td>0.413738</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>520419.454545</td>
      <td>0.048478</td>
      <td>0.462216</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig,ax = plt.subplots(figsize=(15,8))

ax.plot(last_decade.index, last_decade['value'], color='g', marker='*')

ax.set_title('Chelan Wenatchee WA single-family home sale price 2010-2020')

ax.set_ylabel('Sale price (USD)')

plt.show()
```


![png](output_187_0.png)


## 10. Final thoughts

This has been a really interesting exercise in extracting a large dataset of 10,00 records from an API, cleaning and reshaping messy data using several complex methods to arrive at a clean, consolidated data frame. We were then able to analyse and visualise this data to extract a few interesting insights, that could help potential property buyers in Washington State make better, more informed decisions about their potential investments. 
