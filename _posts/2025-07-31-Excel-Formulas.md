---
title: "Excel Formulas"
date: 2025-07-31 10:00:00 -700
categories: [misc]
tags: [snippet, troubleshooting]    #TAG names should always be lowercase
---

# Excel Formulas

Really, formulas that could be used in any Excel or Google Sheets


## Conditional Formulas

Compare info between two columns (unsorted) and highlight duplicates

```excel
=COUNTIF($B:$B,A1)>0
```

-eof-