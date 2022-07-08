---
logoImg: veepee.png
title: from Go to Dotnet (c#)
theme: robot-lung
customTheme: go2dotnet
highlightTheme: github-dark-dimmed
enableMenu: false
controls: false
margin: 0.09
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
<img class="avatar" alt="vincent" src="Vincent4.gif" width="250" height="250" />

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

--

## RISK
of migration is less than having nobody to maintain and make evolution on the code

---

## HOW ?
Fresh new team and 3 months

--

# The Team

<div class="grid" style="--cols-xl: 3;">

:::
<img class="avatar" alt="onur" src="onur.jpg" width="250" height="250" />
<b>Onur</b>
<br>The Junior 
<br/><i style="font-size:15px">"I have a question"</i>
:::

:::
<img class="avatar" alt="onur" src="eduardo.jpg" width="250" height="250" />
<b>Eduardo</b>
<br>The Senior
<br/><i style="font-size:15px">"It makes sense"</i>
:::

:::
<img class="avatar" alt="onur" src="vincent.jpg" width="250" height="250" />
<b>Vincent</b>
<br>The Lead
<br/><i style="font-size:15px">"I'm pretty sure"</i>
:::

</div>

--


# Where to start ?

The go code is not bad and we should be able to recode 1 for 1.

--

# Where to start ?

Bottom / up or Top / down ?  
By microservice ?  🤔


--

We plan to do it by service in a way bottom->up.

But we discovered a big **mess of services dependencies**

---

<!-- .slide: data-state="layout-bg50r" data-background-image="spagetti.jpg" -->

*Spaghetti* 
*Oriented* 
*Architecture* 

--

**After few time we have started everything and finished nothing.**

So I created a markdown file listing all functions to port.

![](migration-state.png) {.border .color-shadow-medium}

--

# How we test ?

**GraphQL as a contract**

**And Frontend as test interface.**
<br/>
<br/>
***

We add some tests : unit and integration and E2E

And we already have automated test from QA

*(thank to Christopher)*

---

# Architecture changes

--

## Make it Simple as stupid
- no more microservices 😮
- no more GRPC 🤨
- no more kafka (for internal notification) 😶
- Embeded frontend 

--

# All in one !
Some calls this "monolith", I call this **application**

![](mic-drop.gif)


---

# Less Waste

Each service has redundancy on two DC =>  4 pods by micro-service

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

<img src="Go_logo_aqua.png" height=25 style="margin: 0 15px">    to <img src="dotnet.png" height=25 style="margin: 0 15px"> 

--

# What about typing ?


--

<!-- .slide: data-state="layout-bg50r" data-background-image="dribbble-machucado1.webp" -->

**Avoid primitive obsession**

So many string everywhere 🥵

--

<img src="dotnet.png" height=25 style="margin: 0 15px">has **Guid**

TODO : put exemple here 

--

<img src="dotnet.png" height=25 style="margin: 0 15px">has **Enum**

`ENUM with JsonStringEnumConverter 💖`

```csharp
/// List of handled language
public enum Languages { da, de, en, es, fr, it, nl, pl }

/// List of handled countries
public enum CountryCode { AT, BE, CH, DE, DK, ES, FR, GB, IT, LU, NL, PL }
```

--

<img src="dotnet.png" height=25 style="margin: 0 15px">has **cultureinfo**

```csharp
var cultures = languages // string[]
    .Select(x => 
        x.ParseLanguage() // enum (validated input)
        .GetCulture() // cultureInfo
    );
```

--

<img src="Go_logo_aqua.png" height=25 style="margin: 0 15px"> Use a lot of empty interfaces

```go
interface{} // can be any thing. Really ?
```

--

<img src="dotnet.png" height=25 style="margin: 0 15px">has **generic**

TODO : put exemple here 


--

## Less loop for everything



<img src="Go_logo_aqua.png" height=25 style="margin: 0 15px" />

```go
emptyTmpl := true
for _, t := range template {
	if t.Sections != nil && len(t.Sections) > 0 {
		emptyTmpl = false
		break
	}
}
```

<img src="dotnet.png" height=25 style="margin: 0 15px">

```csharp
var emptyTmpl = !template.Any(t => t.Value.Any());
```

`(value is sections in the map)`

--

--

.NET has LINQ

TODO : put exemple here 

--

.NET has hasset 

TODO : put exemple here 

--

## One line is enaught

--

.NET has Lambda and Expression body

TODO : put exemple here 

--

## Asynchronous/Concurrent/Parallel

Is Go routine really easy ?
not so sure in long term.

--

Go go routine

--

C# task

Easy to make paralellism

- Code changes
	- Strong Typing
	- Less loop
	- One liner
	- Asynchronous/Concurrent/Parallel

---

###### C#
```csharp
var cultures = languages // string[]
    .Select(x => 
        x.ParseLanguage() // enum (validated input)
        .GetCulture() // cultureInfo
    );
```

---

## Primitive Obsession

A lot of `string`

.NET has  `Guid`

--

.NET has  `CultureInfo`

###### GoLang
```go
cultures := make([]string, len(languages)) // string[]
for i, lang := range languages {
    cultures[i] = commons_language.GetCulture(lang)
}
```

---

<!-- slide: data-background=#512bd4 -->

###### C#
```csharp
var cultures = languages // string[]
    .Select(x => 
        x.ParseLanguage() // enum (validated input)
        .GetCulture() // cultureInfo
    );
```


---


## No generic so everything is something

```go
interface{} // can be any thing. Really ?
```

---


# LINQ !

go
```go
// As PostgreSQL can't sort based on a custom order, do it in code
orderedSettings := make([]repository.SettingsRow, 0)
for _, code := range lineage {
    if setting, ok := settings[code]; ok {
        orderedSettings = append(orderedSettings, setting)
    }
}
```

c#
```csharp
// As PostgreSQL can't sort based on a custom order, do it in code
var orderedSettings = lineage.Select(x => settings[x]);
```

--

GO
```
emptyTmpl := true
	for _, t := range template {
		if t.Sections != nil && len(t.Sections) > 0 {
			emptyTmpl = false
			break
		}
	}
```

C#
```
var emptyTmpl = !template.Any(t => t.Value.Any());
```
(value is sections in the map)

--

GO
```
// Check if the list to set as group_by is empty
empty := len(characteristics) == 0
```

C#
```
// Check if the list to set as group_by is empty
var empty = characteristics.Any();
```

--

make declaration and instantiation on same line must of the time


---

# Expression body

GO
```

// DeleteNGPSettings deletes an NGP Settings from persistent DB
func (uc *Usecase) DeleteNGPSettings(ctx context.Context, ngpCode string) error {
	// Delete NGP Settings from persistent DB
	if err := uc.repo.DeleteNGPSettings(ctx, ngpCode); err != nil {
		common_log.Error(ctx, uc.logger, fmt.Sprintf("Error while deleting the ngpsettings %q from persistent database", ngpCode), err)
		return err
	}
	return nil
}

```

C#
```csharp
public Task DeleteNGPSettings(string ngpCode) => _repository.DeleteNGPSettings(ngpCode);
```


---

ENUM with JsonStringEnumConverter 💖

```csharp
/// List of handled language
public enum Languages { da, de, en, es, fr, it, nl, pl }

/// List of handled countries
public enum CountryCode { AT, BE, CH, DE, DK, ES, FR, GB, IT, LU, NL, PL }
```

---

# ASYNC all the thing

async for all IO  (db and HTTP)



---


GO
```
ngpSettings := resp.GetNgpsettings()
	res := []*model.Characteristic{}
	characteristicsMap := make(map[string]struct{})
	for ngp := range ngpSettings {
		// Loop through characteristics to prevent duplicates
		for _, c := range ngpSettings[ngp].GroupBy.Characteristics {
			if _, ok := characteristicsMap[c.Code]; !ok {
				characteristicsMap[c.Code] = struct{}{}
				res = append(res, model.NewCharacteristicFromNGPSettingsClient(c))
			}
		}
	}
```

C#
```csharp
var res =ngpSettings.Values
	.SelectMany(x => x.GroupBy)
	.DistinctBy(x => x.Code)
	.ToArray();
```

---


SWITCH

```go
var searchBy client_search.SearchCriteria_SearchBy
switch s := s.SearchBy; s {
	case SearchByName:
	searchBy = client_search.SearchCriteria_NAME
	case SearchByArticleID:
	searchBy = client_search.SearchCriteria_ARTICLEID
	case SearchBySku:
	searchBy = client_search.SearchCriteria_SKU
	default:
	searchBy = client_search.SearchCriteria_NONE
}
```
---

# Map is not a Set  ....

--

<!-- slide: data-background=PowderBlue  -->

# ![](go.png) {.logo}

```go
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

--

<!-- slide: data-background=#512bd4 -->

# ![](dotnet.png) {.logo}

```C#
public string[] GetDistinctNGPCodesFromArticles(Models.Article[] articles) => 
	articles
		.Select(x => x.NgpCode)
		.Distinct()
		.ToArray();
```


---

### named tuple item

```go
func (uc *usecase) GetLocalizedTemplates(/*..*/) 
 (map[string][]*model.TemplateSection, map[string][]*model.TemplateSection, error) 
 {}
```


---

get unique
```
// Get all the unique NGP codes from articles
ngpMap := make(map[string]struct{})
ngpCodes := []string{}
for _, a := range resp.Articles {
	ngpCode := substituteArticleNGPCode(a.CategoryCode, ngpCodeSubstitutions)
	ngpMap[ngpCode] = struct{}{}
}
for k := range ngpMap {
	ngpCodes = append(ngpCodes, k)
}
```

---

TODO

- GraphQL the subscriptions 
	- the code from go is totally weird
	- the channel, the observer, etcetc
	- in hotchocolate is, addsubscriptions , addwebsockets, and add the two methods for the - dynamic topic

- API CLient  Refit : client api


- Data Access -> repodb 
	- and the mappings, we do not have to get,set all the props, only make some custom handlers for especial types

- LINQ in go => https://godoc.org/github.com/ahmetb/go-linq

- Enum


- IOC
// Logs the execution time
       defer track.Time(ctx, uc.logger, time.Now(), fmt.Sprintf("Get group_by for %d NGP codes", len(ngpCodes)))

- Notofication



---

## Conclusion

--

### it's possible

-- 

## What's next

- we still need to do a lot of clean code
- Create a strong Domain model
- Continue to use (domain)Events
- Be more like Elm archi and Onion Archi
