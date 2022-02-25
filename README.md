<div align="center">
  <img height="60" src="https://static.wikia.nocookie.net/adobe/images/e/e2/Adobe_Experience_Manager_icon.svg/revision/latest/scale-to-width-down/512?cb=20200110101730" alt="AEM">
  <h1>Useful Queries in AEM</h1>
</div>

<span>A collection of various AEM queries that I've found to be useful.</span>

---

###### Find pages that were last modified by specific user

```sql
SELECT * FROM [nt:base] AS s
WHERE
    ISDESCENDANTNODE([/content]) AND 
    s.[cq:template] IS NOT NULL AND
    s.[cq:lastModifiedBy] = 'user'
```

---

###### Find active PDFs in the DAM (Type: XPath)

```
/jcr:root/content/dam//*[
    @jcr:primaryType = 'dam:AssetContent' and
    @cq:lastReplicationAction = 'Activate' and
    metadata@dc:format = 'application/pdf'
]
```

---

###### Find instances of the iframe component that uses "http:" (Type: SQL)

```sql
SELECT * FROM [nt:base] AS s 
WHERE
    ISDESCENDANTNODE([/content]) AND
    s.[sling:resourceType] = 'path/to/iframe' AND
    s.[target] LIKE 'http:%'
```

