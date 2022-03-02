<div align="center">
  <img height="60" src="https://static.wikia.nocookie.net/adobe/images/e/e2/Adobe_Experience_Manager_icon.svg/revision/latest/scale-to-width-down/512?cb=20200110101730" alt="AEM Logo">
  <h1>Useful Queries in AEM</h1>
  A collection of various AEM queries that I've found to be useful.
</div>

---

#### Find all instances of a string, excluding a particular path. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    NOT ISDESCENDANTNODE([/path/to/exclude]) AND
    CONTAINS(*, '"my string"')
```

---

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

#### Find all pages that use a particular template. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:template] = '/path/to/template'
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

#### Find all pages that were activated after a certain date. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[cq:lastReplicationAction] = 'Activate' AND
    s.[cq:lastReplicated] > '2022-02-25T00:00:00.000-05:00'
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

#### Find active PDFs in the DAM. (Type: XPath)

```
/jcr:root/content/dam//*[
    @jcr:primaryType = 'dam:AssetContent' and
    @cq:lastReplicationAction = 'Activate' and
    metadata@dc:format = 'application/pdf'
]
```

---

#### Find instances of the iframe component that uses "http:" in the target value. (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'relative/path/to/iframe' AND
    s.[target] LIKE 'http:%'
```

