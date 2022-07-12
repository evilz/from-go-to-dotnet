---
logoImg: veepee.png
title: from Go to Dotnet (c#)
theme: robot-lung
customTheme: go2dotnet
highlightTheme: github-dark-dimmed
enableMenu: true
controls: false
margin: 0.09
width: 1080
css:
 - https://unpkg.com/@primer/css@^19.0.0/dist/primer.css

---

# From GO MicroServices <br/>to C# All in one

<div class="grid" style="--cols-xl: 2;">

![](PngItem_4242405.png){style=height:180px}

![](dotnet-surf.png){style=height:200px}

<grid>

---

# About me

<div class="grid" style="--cols-xl: 2;">
<div>

**Vincent Bourdon**

Lead dev SMAC

Dev .NET depuis 2001

</div>

<div class="text-center" >
<img class="avatar" alt="vincent" src="Vincent4.gif" width="250" height="250" />
<div>
<grid>

---

# Plan

<div class="grid" style="--cols-xl: 2;">
<div>

- Why ?
- How ?
- Architecture changes
- Code changes

</div>

<div>

- GraphQL
- Data Access Layer
- Dependency injection
- Api Client
- Notification System
- Conclusion

</div> 
</div>

---

<!-- .slide: data-state="layout-bg50r" data-background-image="https://www.meme-arsenal.com/memes/d144a9109c5f5d18e37788f768fc6f95.jpg" -->

# Why ?
#### CHANGE SOMETHING THAT WORKS 
---

<!-- .slide: data-state="layout-bg50r" data-background-image="gopher10th-large.jpg" -->

- The app is coded in Golang

- But all the original team members are gone

- We tried to hire new Go developers

---

# Guess what ! {.r-fit-text}

---

## no candidate in 9 months ! {.r-fit-text}


---

<!-- .slide: data-background-image="https://i.kym-cdn.com/photos/images/original/001/042/619/4ea.jpg" -->

---

**Then I took the decision to migrate all the code to C#**


I'm so much better dev in .NET

Veepee has a lot good .NET dev that can ask for mobility

---

## RISK
of migration is less than having nobody to maintain and make evolution on the code

---

<!-- .slide: data-state="layout-bg50r" data-background-image="old-agence_tous_risques.0.jpg" -->

## HOW ?
Fresh new team and 3 months

---

# The Team

<div class="grid" style="--cols-xl: 3;">

::: {.text-center}
<img class="avatar" alt="onur" src="onur.jpg" width="250" height="250" /><br>
<b>Onur</b>
<br>The Junior 
<br/><i style="font-size:15px">"I have a question"</i>
:::

::: {.text-center}
<img class="avatar" alt="onur" src="eduardo.jpg" width="250" height="250" /><br>
<b>Eduardo</b>
<br>The Senior
<br/><i style="font-size:15px">"It makes sense"</i>
:::

::: {.text-center}
<img class="avatar" alt="onur" src="vincent.jpg" width="250" height="250" /><br>
<b>Vincent</b>
<br>The Lead
<br/><i style="font-size:15px">"I'm pretty sure"</i>
:::

</div>

---

# Current state ?

The go code is not bad and we should be able to recode 1 for 1.

---

# Where to start ?

Bottom / up or Top / down ?{.fragment}

By microservice ?  🤔{.fragment}

We plan to do it by **service** in **bottom /up** way.{.fragment}

But we discovered a big **mess of services dependencies** {.fragment}

---

<!-- .slide: data-state="layout-bg50r" data-background-image="spagetti.jpg" -->

### *Spaghetti* 
### *Oriented* 
### *Architecture* 

---

**After few time we have started everything and finished nothing.**

So I created a markdown file listing all functions to port.

![](migration-state.png) {.border .color-shadow-medium}

---

# How we test ?

**GraphQL as a contract**{.fragment}

**And Frontend as test interface.**{.fragment}

::: {.fragment}
<br/>

***

We add some tests : unit and integration and E2E

And we already have automated test from QA

*(thank to Christopher)*
:::

---

<!-- .slide: data-state="layout-bg50r" data-background-image="archi.webp" -->

## Architecture changes

---

## Make it Simple as stupid
- no more microservices 😮{.fragment}
- no more GRPC 🤨{.fragment}
- no more kafka (for internal notification) 😶{.fragment}
- Embeded frontend{.fragment} 

---

# All in one !
Some calls this "monolith", I call this **application**

![](mic-drop.gif)


---

# Less Waste

Each service must be Resilient and have high availability.

So we deploy on two DC twice =>  4 pods by micro-service

Then need load balancing, configuration, monitoring, alerting, logging ...

---

# C2 model

![](SMAC%20dependency%20graph%20-%20C2%20model.png){style=height:15em}

---

# Kub infra

![](Smac-prod-old.png){style=width:81em}


---

# Waste infra

![](waste-before.png)
---

SCREESHOT OF ARCHI NOW


---

SCREESHOT OF WASTE NOW


---

# Code Changes

# <img src="Go_logo_aqua.png" class="v-align-text-bottom" style="height:1em;margin: 0 15px"> to <img src="dotnet.png" class="v-align-text-bottom" style="height:1em;margin: 0 15px"> 

---

# What about typing ?


---

<!-- .slide: data-state="layout-bg50r" data-background-image="dribbble-machucado1.webp" -->

**Avoid primitive obsession**

So many string everywhere 🥵

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use string

```go
// Usecase represents the sale's usecase interface
type Usecase interface {
	CheckRules(ctx context.Context, saleVersionID string, checkTypes []model.CheckRuleType) ([]*model.CheckRule, error)
	CreateSale(ctx context.Context, sale *model.Sale) (*model.Sale, error)
	CreateSaleVersion(ctx context.Context, saleID, name, fromVersionID string, isPublish bool) (*model.Version, error)
	GetSale(ctx context.Context, id string) (*model.Sale, error)
	GetSaleByVersionID(ctx context.Context, versionID string) (*model.Sale, error)
	UpdateSale(ctx context.Context, sale *model.Sale) error
	PublishSaleVersion(ctx context.Context, versionID string) (bool, error)
	ResetSaleVersion(ctx context.Context, versionID string, scopes []model.ResetScope) (*model.Version, error)
	NotifyIfSaleReady(ctx context.Context, versionID string) error
	HandleCampaignDateChanges(ctx context.Context, campaignCode string, channels []common_kafka.CampaignChannel) error
}

```

---


# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got Guid

```csharp
public interface ISaleUsecase
{
    Task<CheckRule[]> CheckRules(Guid saleVersionID, CheckRuleType[] checkTypes);
    Task<Models.Sale> CreateSale(Models.Sale sale);
    Task<DomainVersion> CreateSaleVersion(string saleID, string name, Guid? fromVersionId, bool isPublish);
    Task<Models.Sale?> GetSale(string saleID);
    Task<Models.Sale[]?> GetSales(string[] saleIDs);
    Task<Models.Sale?> GetSaleByVersionId(Guid versionId);
    Task<int> UpdateSale(Models.Sale sale);
    Task<DomainVersion> ResetSaleVersion(Guid versionID, ResetScope[] scopes);
    Task PublishSaleVersion(Guid versionID);
    Task NotifyIfSaleReady(Guid versionID);
    Task NotifyOnSaleCreated(string id, Guid versionId, string name, string campaignCode, CountryCode countryCode);
    Task HandleCampaignDateChanges(string campaignCode, Channel[] channels);
}
```

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use more string !
```go
languageByCountryCode = map[string][]string{
	"at": {"de"},
	"be": {"en", "nl", "fr"},
	"ch": {"fr", "de"},
	"de": {"de"},
	"dk": {"da"},
	"es": {"es"},
	"fr": {"fr"},
	"gb": {"en"},
	"it": {"it"},
	"lu": {"en", "fr"},
	"nl": {"en", "nl"},
	"pl": {"pl"},
}
```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got Enum

ENUM with JsonStringEnumConverter 💖

```csharp
/// List of handled language
public enum Languages { da, de, en, es, fr, it, nl, pl }

/// List of handled countries
public enum CountryCode { AT, BE, CH, DE, DK, ES, FR, GB, IT, LU, NL, PL }

public static readonly IDictionary<Languages, string> CultureByLanguage =
	new Dictionary<Languages, string>
	{
		{ Languages.da, "da-DK" },
		{ Languages.de, "de-DE" },
		{ Languages.en, "en-GB" },
		{ Languages.es, "es-ES" },
		{ Languages.fr, "fr-FR" },
		{ Languages.it, "it-IT" },
		{ Languages.nl, "nl-NL" },
		{ Languages.pl, "pl-PL" }
	};

```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got cultureinfo

But we don't use it anymore for this usecase

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use interface{}
```go {data-trim data-line-numbers="5"}
jsonStruct := struct {
		Code             string            `json:"code"`
		Name             string            `json:"name"`
		NameTranslations map[string]string `json:"translations"`
		Value            interface{}       `json:"value"`  // can be any thing. Really ?
		ValueCode        string            `json:"valueCode"`
		Properties       []*Property       `json:"properties"`
	}{}
```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px">  use generic

```csharp  {data-trim data-line-numbers="|4|14|12"}
public record Property(
    string Code,
    string Name,
    I18nString NameTranslations,
    string? Value,
    I18nString? ValueTranslations,
    string ValueCode,
    bool AllowMultiplicity,
    Property[] Properties
);

public class I18NObject<T>{}

public class I18nString : I18NObject<string>{}
```

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use map with empty value 🤐

```go
distinctMediaIDs := make(map[string]struct{})
//....
distinctMediaIDs[m.ID] = struct{}{}
```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> use HashSet

```csharp
HashSet<string> distinctMediaIDs = new();
//....
distinctMediaIDs.Add(m.Id);
```

---

<!-- .slide: data-state="layout-bg50r" data-background-image="gophercises_jumping.gif" -->
# loop for ever


---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use for loop everywhere
```go {data-trim data-line-numbers="|3|9|"}
func (uc *usecase) GetDistinctNGPCodesFromArticles(ctx context.Context, articles []*model.Article) []string {
	distinctNGPCodes := make(map[string]struct{})   // <-- struct {}  ???
	for _, aa := range articles {
		distinctNGPCodes[aa.NgpCode] = struct{}{}  // <--  WHAT ??
	}

	// Slice of NGP codes
	ngpCodes := []string{}
	for ngpCode := range distinctNGPCodes {    // <-- one more loop
		ngpCodes = append(ngpCodes, ngpCode)
	}

	return ngpCodes
}
```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got linq

```csharp
public string[] GetDistinctNGPCodesFromArticles(Models.Article[] articles) => 
	articles
		.Select(x => x.NgpCode)
		.Distinct()
		.ToArray();
```

*LINQ in go => https://godoc.org/github.com/ahmetb/go-linq* 

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> use Tuple
```go
func (uc *usecase) GetLocalizedTemplates(/*..*/) 
 (map[string][]*model.TemplateSection, map[string][]*model.TemplateSection, error) 
 {}
```

---


# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got named tuple !

```csharp
(Dictionary<string, TemplateSection[]> sizeLabelTemplates,
 Dictionary<string, TemplateSection[]> nameTemplates) =
            await _ngpSettingsUsecase.GetLocalizedTemplates(aggregationNgpCodes, countryCode);
```

---


# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> got Expression body

Easy to reduce code to one line

```csharp
public Task<IEnumerable<Models.Sale>> GetAllSalesAsync() => QueryAllAsync<Models.Sale>();
```

---

<!-- .slide: data-state="layout-bg50r" data-background-image="gorun2.png" -->

# Async
# Concurrent
# Parallel


---

> Is Go routine really easy ?
>
> not so sure in long term.

---

# <img src="Go_logo_aqua.png"  style="height:.7em; margin: 0 15px"> goroutine for paralism
```go
pc := make(chan *Pair, len(inputs))
// Generate identifier for each input
for k, i := range inputs {

	// One goroutine per input in order to speed it up
	go func(key string, meta map[string]string, isRandom bool) {
		identifier, err := uc.generateIdentifier(ctx, meta, isRandom)
		if err != nil {
			ec <- err
			return
		}

		pc <- &Pair{
			Key:   key,
			Value: identifier,
		}
	}(k, i.GetMeta(), i.GetIsRandom())
}
```

---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> use Task and TPL

```csharp
// System.Threading.Tasks.Parallel
Parallel.ForEachAsync(inputs, async (k, i) => { .... });

// PLINQ
inputs.AsParallel().Select(x => {....} );

// or create list of task and wait
await Task.WhenAll(tasks);
```


---

# <img src="dotnet.png"  style="height:.7em; margin: 0 15px"> use Task for all I/O
```csharp
// DATABASE
public interface ISaleRepository
{
    Task<string> CreateSaleAsync(Models.Sale sale);
    Task<Models.Sale?> FindSaleAsync(Expression<Func<Models.Sale, bool>> where);
}

// WEB CLIENT
public interface IPimClient
{
    Task<Product[]?> GetProductsByOperationCodeAsync(string operationCode, string language);
    Task<Article[]?> GetArticlesAsync(string[] articleIDs);
    Task<Article[]> BulkGetArticlesAsync(Guid[] articleIDs, bool withTranslations);
}
```

---

# Lines of  code

| Language      | Lines of code |
| ----------- | -----------: |
| C#      | 18 008      |
| Go   | 133 768        |

> We save **115 760** LOC it's almost **86.5%** less
> <br/>*test included

---

# What do we use ?

---

<!-- .slide: data-state="layout-bg50r" data-background-image="hotchocolate.svg" -->

# GraphQL

We should HotChocolate

- Very performent
- Easy to use 
- Annotation-based / Code-first / Schema-first 
- Subscription with WebSocket
- Awesome with EF core 
- Banana cupcake

note:
GraphQL the subscriptions 
<br/>- the code from go is totally weird
<br/>- the channel, the observer, etcetc
<br/>- in hotchocolate is, addsubscriptions , addwebsockets, and add the two methods for the - dynamic topic

---

<!-- .slide: data-fullscreen data-background-image="bananacupcake.png" -->


---

# Data Access Layer

We use a ORM : RepoDB

<div class="grid" style="--cols-xl: 3;--cols-md: 3;--cols-xs: 3;">

<div>
<ul>
<li>Performant</li>
<li>easy to use</li>
<li>Work on many DBMS</li>
<li>A lot of feature (Bulk/Batch/Caching)</li>
</ul>
</div>

<div style="--col-xl: span 2;--col-md: span 2;--col-xs span 2">
<img src="repodb.svg" >
</div>

</grid>

---

### I also think about using **EF core**

Cause it is more flexible and have great feature on PG jsonb

![](efcore-pgjson.png)

---

# API client

We use **Refit**

- automatic type-safe REST library

*(Heavily inspired by Square's Retrofit library)*

```csharp
public record TechnicalSheetsByFamily(int FamilyId, string Ft, string Subject);

public interface ISpotApi
{
    [Get("/api/v2/campaigns/events/{site}/technicalSheets/{familyId}")]
    Task<ApiResponse<TechnicalSheetsByFamily[]>>GetTechnicalSheetsByFamilyIdAndSite(string site, int familyId);
}
```

```csharp
// Program.cs
builder.Services.AddRefitClient<ISpotApi>(refitSettings).ConfigureHttpClient(c=>
{
    c.BaseAddress = new Uri(configuration.GetValue<string>("Spot:Host"));
    c.DefaultRequestHeaders.Add("apikey", configuration.GetValue<string>("Spot:Apikey"));
});


```

---

# Refit

- Only defined interface
- Use Source Generator
- Use Httpclient
- Easy to configure, add headers and bearer

---

# Notification System


We replace **Kafka** by **MediatR**

- In-process messaging with no dependencies.
- Use generics
- Very easy to migrate using same message

---

# MediatR 

```csharp
public class SaleVersionCreatedHandler : INotificationHandler<SaleVersionCreated>
{
    private readonly ICatalogUsecase _catalogUsecase;

    public SaleVersionCreatedHandler(ICatalogUsecase catalogUsecase) =>
		_catalogUsecase = catalogUsecase;

    public Task Handle(SaleVersionCreated notification, ct cancellationToken) =>
	    _catalogUsecase.CloneCatalogTree(
			notification.VersionId, 
			notification.FromVersionId, 
			notification.IsPublish);
}
```


---

## Conclusion

- it's possible 
- It take times ! 3 months here
- We know better the code and how SMAC is working
- Evolution and support should be easier now

---

## What's next

- we still need to do a lot of clean code
- Create a strong Domain model
- Continue to use (domain)Events
- Be more like Elm archi and Onion Archi

---

# QA 

# 🙋🏻‍♂️🙋🏻‍♀️❔ {.text-center}

---

# THANK