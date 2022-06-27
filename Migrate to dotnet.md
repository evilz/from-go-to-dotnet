---
title: from Go to Dotnet (c#)
theme: black
highlightTheme: github-dark-dimmed
enableMenu: false
customTheme : "go2dotnet"
controls: false

---

<!-- .slide: data-background="https://cdn.learnku.com//uploads/communities/sNljssWWQoW6J88O9G37.png" data-background-size="150px" data-background-repeat="no-repeat" data-background-position="85% 50%" -->

# From Go MicroServices <br/>to C# All in one

---

## Plan

- Why ?
- How ?
- Architecture changes
- Code changes
	- Strong Typing
	- Less loop
	- One liner
	- Asynchronous/Concurrent/Parallel
- GraphQL
- Data Access Layer
- Dependency injection
- Api Client
- Notification System


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