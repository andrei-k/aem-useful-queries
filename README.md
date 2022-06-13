<div align="center">
  <img height="60" src="https://static.wikia.nocookie.net/adobe/images/e/e2/Adobe_Experience_Manager_icon.svg/revision/latest/scale-to-width-down/512?cb=20200110101730" alt="AEM Logo">
  <h1>Useful Queries in AEM</h1>
  <p>A collection of various AEM queries that I've found to be useful</p>
</div>
<br>

### Table of Contents
#### Components
**[Find all instances of a particular component](#find-all-instances-of-a-particular-component-type-sql2)**<br>
**[Find all instances of a component where a property contains some string](#find-all-instances-of-a-component-where-a-property-contains-some-string-type-sql2)**<br>
**[Find all instances of a component where some property is not empty](#find-all-instances-of-a-component-where-some-property-is-not-empty-type-sql2)**<br>
**[Find all instances of a component on active pages only](#find-all-instances-of-a-component-on-active-pages-only-type-sql2)**<br>
**[Find all instances of a component on active pages only](##find-all-instances-of-a-component-that-are-descendants-of-a-particular-parent-node-name-type-sql2)**<br>

#### Pages
**[Count pages under a particular path](#count-pages-under-a-particular-path-type-sql2)**<br>
**[Find pages that use a particular template](#find-pages-that-use-a-particular-template-type-sql2)**<br>
**[Find pages that were activated after a certain date](#find-pages-that-were-activated-after-a-certain-date-type-sql2)**<br>
**[Find pages that are not active](#find-pages-that-are-not-active-this-query-will-return-pages-where-lastreplicationaction-is-either-blank-or-doesnt-equal-to-activate-type-sql2)**<br>
**[Find pages that have never been activated and are older than some specific date](#find-pages-that-have-never-been-activated-and-are-older-than-some-specific-date-this-provides-a-good-way-to-find-unused-pages-to-purge-type-sql2)**<br>
**[Find pages that were created within some date range](#find-pages-that-were-created-within-some-date-range-type-sql2)**<br>
**[Find all instances of a component that are descendants of a particular parent node name](#find-pages-that-were-last-modified-by-specific-user-type-sql2)**<br>

#### Strings and Files
**[Find all instances of a string, excluding a particular path](#find-all-instances-of-a-string-excluding-a-particular-path-type-sql2)**<br>
**[Find active PDFs in the DAM](#find-active-pdfs-in-the-dam-type-xpath)**<br>
**[Find nodes by name](#find-nodes-by-name-type-sql2)**<br>

<br>

## Components

#### Find all instances of a particular component (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'relative/path/to/component'
```

---

#### Find all instances of a component where a property contains some string (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND 
    s.[sling:resourceType] = 'relative/path/to/component' AND
    s.[property] LIKE '%string%'
```

---

#### Find all instances of a component where some property is not empty (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'relative/path/to/component' AND
    s.[property] IS NOT NULL
```

---

#### Find all instances of a component on active pages only (Type: SQL2)

```sql
SELECT * FROM [cq:PageContent] AS page
INNER JOIN [nt:base] AS component ON ISDESCENDANTNODE(component, page)
WHERE
    ISDESCENDANTNODE(page, [/content]) AND
    page.[cq:lastReplicationAction] = 'Activate' AND
    component.[sling:resourceType] = 'relative/path/to/component'
```

---

#### Find all instances of a component that are descendants of a particular parent node name (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS parent
INNER JOIN [nt:base] AS component ON ISDESCENDANTNODE(component, parent)
WHERE
    ISDESCENDANTNODE(parent, [/content]) AND
    NAME(parent) = 'node-name' AND
    component.[sling:resourceType] = 'relative/path/to/component'
```

---

<br>

## Pages

#### Count pages under a particular path (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:template] IS NOT NULL
```

#### Find pages that use a particular template (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:template] = '/path/to/template'
```

---

#### Find pages that were activated after a certain date (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:lastReplicationAction] = 'Activate' AND
    s.[cq:lastReplicated] > '2022-02-25T00:00:00.000-05:00'
```

---

#### Find pages that are not active (Type: SQL2)
This query will return pages where lastReplicationAction is either blank or doesn't equal to "Activate".

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:template] IS NOT NULL AND
    (
        s.[cq:lastReplicationAction] <> 'Activate' OR
        s.[cq:lastReplicationAction]  IS NULL
    )
```

---

#### Find pages that have never been activated and are older than some specific date (Type: SQL2)
This provides a good way to find unused pages to purge.

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:template] IS NOT NULL AND
    s.[cq:lastReplicationAction] IS NULL AND
    s.[cq:lastModified] < '2020-01-00T00:00:00.000-05:00'
```

---

#### Find pages that were created within some date range (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:template] = '/path/to/template' AND
    s.[jcr:created] > '2021-01-01T00:00:00.000-05:00' AND
    s.[jcr:created] < '2022-01-01T00:00:00.000-05:00'
```

---

#### Find pages that were last modified by specific user (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND 
    s.[cq:template] IS NOT NULL AND
    s.[jcr:primaryType] = 'cq:PageContent' AND
    s.[cq:lastModifiedBy] = 'user'
```

***
<br>

## Strings and Files

#### Find all instances of a string, excluding a particular path (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    NOT ISDESCENDANTNODE([/path/to/exclude]) AND
    CONTAINS(*, '"my string"')
```

---

#### Find active PDFs in the DAM (Type: XPath)

```
/jcr:root/content/dam//*[
    @jcr:primaryType = 'dam:AssetContent' and
    @cq:lastReplicationAction = 'Activate' and
    metadata@dc:format = 'application/pdf'
]
```

---

#### Find nodes by name (Type: SQL2)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    NAME() = 'node-name'
```

---

