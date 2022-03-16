<div align="center">
  <img height="60" src="https://static.wikia.nocookie.net/adobe/images/e/e2/Adobe_Experience_Manager_icon.svg/revision/latest/scale-to-width-down/512?cb=20200110101730" alt="AEM Logo">
  <h1>Useful Queries in AEM</h1>
  <p>A collection of various AEM queries that I've found to be useful.</p>
</div>
<br>

### Table of Contents
#### Components
**[Find all instances of a particular component](#find-all-instances-of-a-particular-component-type-sql)**<br>
**[Find all instances of a component where a property contains some string](#find-all-instances-of-a-component-where-a-property-contains-some-string-type-sql)**<br>
**[Find all instances of a component where some property is not empty](#find-all-instances-of-a-component-where-some-property-is-not-empty-type-sql)**<br>

#### Pages
**[Find all pages that use a particular template](#find-all-pages-that-use-a-particular-template-type-sql)**<br>
**[Find all pages that were activated after a certain date](#find-all-pages-that-were-activated-after-a-certain-date-type-sql)**<br>
**[Find all pages that are not active](#find-all-pages-that-are-not-active-this-query-will-return-pages-where-lastreplicationaction-is-either-blank-or-doesnt-equal-to-activate-type-sql)**<br>
**[Find all pages that have never been activated and are older than some specific date](#find-all-pages-that-have-never-been-activated-and-are-older-than-some-specific-date-this-provides-a-good-way-to-find-unused-pages-to-purge-type-sql)**<br>
**[Find all pages that were created within some date range](#find-all-pages-that-were-created-within-some-date-range-type-sql)**<br>
**[Find pages that were last modified by specific user](#find-pages-that-were-last-modified-by-specific-user-type-sql)**<br>

#### Strings and Files
**[Find all instances of a string, excluding a particular path](#find-all-instances-of-a-string-excluding-a-particular-path-type-sql)**<br>
**[Find active PDFs in the DAM](#find-active-pdfs-in-the-dam-type-xpath)**<br>
**[Find nodes by name](#find-nodes-by-name-type-sql2)**<br>

<br>

## Components

#### Find all instances of a particular component. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'relative/path/to/component'
```

---

#### Find all instances of a component where a property contains some string. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND 
    s.[sling:resourceType] = 'relative/path/to/component' AND
    s.[property] LIKE '%string%'
```

---

#### Find all instances of a component where some property is not empty. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'relative/path/to/component' AND
    s.[property] IS NOT NULL
```

---

<br>

## Pages

#### Find all pages that use a particular template. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:template] = '/path/to/template'
```

---

#### Find all pages that were activated after a certain date. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:lastReplicationAction] = 'Activate' AND
    s.[cq:lastReplicated] > '2022-02-25T00:00:00.000-05:00'
```

---

#### Find all pages that are not active. (Type: SQL)
This query will return pages where lastReplicationAction is either blank or doesn't equal to "Activate".

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:template] IS NOT NULL AND
    (
        s.[cq:lastReplicationAction] <> 'Activate' OR
        s.[cq:lastReplicationAction]  IS NULL
    )
```

---

#### Find pages that were last modified by specific user. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND 
    s.[cq:template] IS NOT NULL AND
    s.[cq:lastModifiedBy] = 'user'
```

---

#### Find all pages that have never been activated and are older than some specific date. (Type: SQL)
This provides a good way to find unused pages to purge.

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:lastReplicationAction] IS NULL AND
    s.[cq:template] IS NOT NULL AND
    s.[cq:lastModified] < '2020-01-00T00:00:00.000-05:00'
```

---

#### Find all pages that were created within some date range. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:template] = '/path/to/template' AND
    s.[jcr:created] > '2021-01-01T00:00:00.000-05:00' AND
    s.[jcr:created] < '2022-01-01T00:00:00.000-05:00'
```

***
<br>

## Strings and Files

#### Find all instances of a string, excluding a particular path. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    NOT ISDESCENDANTNODE([/path/to/exclude]) AND
    CONTAINS(*, '"my string"')
```

---

#### Find active PDFs in the DAM. (Type: XPath)

```
/jcr:root/content/dam//*[
    @jcr:primaryType = 'dam:AssetContent' and
    @cq:lastReplicationAction = 'Activate' and
    metadata@dc:format = 'application/pdf'
]
```

---

#### Find nodes by name. (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    NAME() = 'name'
```

---

