## TODO

* [ ] Import dataset
* [ ] Inspect columns and data types
* [ ] Clean and transform data in Power Query
* [ ] Handle missing values and duplicates
* [ ] Create `RestaurantID`
* [ ] Finalize `Restaurants` table
* [ ] Create `Orders` table
* [ ] Create `Customers` table
* [ ] Create `Date` table
* [ ] Build data model and relationships
* [ ] Create calculated columns
* [ ] Create base measures
* [ ] Create advanced measures
* [ ] Build Page 1 – Overview
* [ ] Build Page 2 – Restaurant Analysis
* [ ] Build Page 3 – Customer Analysis
* [ ] Build Page 4 – Order & Delivery Analysis
* [ ] Build Page 5 – Time & Revenue Analysis
* [ ] Add slicers and filters
* [ ] Add visual interactions
* [ ] Configure drill-through and tooltips
* [ ] Apply final formatting
* [ ] Optimize dashboard layout and design
* [ ] Validate KPIs and DAX calculations
* [ ] Final testing
* [ ] Add dashboard screenshots
* [ ] Update README
* [ ] Publish final project to GitHub


# DAX Calculated Columns & Measures

## Calculated Columns

### Customer

**FilterCity**

```DAX
FilterCity =
VAR val = 'Customer'[city]
VAR wordCount = LEN(val) - LEN(SUBSTITUTE(val, " ", "")) + 1
RETURN
IF(
    ISBLANK(val) || wordCount > 4 || LEN(val) > 50,
    BLANK(),
    val
)
```

### Restaurants

**Cost Bucket**

```DAX
Cost Bucket =
SWITCH(
    TRUE(),
    Restaurants[CostForTwo] < 500, "Budget",
    Restaurants[CostForTwo] < 1000, "Mid Range",
    "Premium"
)
```

**Rating Bucket**

```DAX
Rating Bucket =
SWITCH(
    TRUE(),
    Restaurants[Rating] >= 4.5, "Excellent",
    Restaurants[Rating] >= 4.0, "Good",
    Restaurants[Rating] >= 3.0, "Average",
    "Low"
)
```

### Orders

**Delivery Status**

```DAX
Delivery Status =
IF(
    Orders[DeliveryTimeMins] > 45,
    "Late",
    "On Time"
)
```

**Order Month**

```DAX
Order Month =
FORMAT(
    Orders[OrderDate],
    "MMM YYYY"
)
```

---

# Core Measures

## KPI Measures

```DAX
Total Orders =
COUNT(Orders[OrderID])
```

```DAX
Total Revenue =
SUM(Orders[OrderValue])
```

```DAX
Avg Order Value =
DIVIDE(
    [Total Revenue],
    [Total Orders]
)
```

```DAX
Avg Delivery Time =
AVERAGE(Orders[DeliveryTimeMins])
```

```DAX
Total Customers =
DISTINCTCOUNT(Orders[CustomerID])
```

```DAX
Total Restaurants =
DISTINCTCOUNT(Restaurants[RestaurantID])
```

## Status Measures

```DAX
Delivered Orders =
CALCULATE(
    [Total Orders],
    Orders[OrderStatus] = "Delivered"
)
```

```DAX
Cancelled Orders =
CALCULATE(
    [Total Orders],
    Orders[OrderStatus] = "Cancelled"
)
```

```DAX
Cancellation % =
DIVIDE(
    [Cancelled Orders],
    [Total Orders]
)
```

```DAX
Late Orders =
CALCULATE(
    [Total Orders],
    Orders[Delivery Status] = "Late"
)
```

```DAX
Late Delivery % =
DIVIDE(
    [Late Orders],
    [Total Orders]
)
```

## Customer Measures

```DAX
Repeat Customers =
COUNTROWS(
    FILTER(
        VALUES(Orders[CustomerID]),
        CALCULATE(
            COUNT(Orders[OrderID])
        ) > 1
    )
)
```

```DAX
Repeat Customer % =
DIVIDE(
    [Repeat Customers],
    [Total Customers]
)
```

```DAX
Orders Per Customer =
DIVIDE(
    [Total Orders],
    [Total Customers]
)
```

## Restaurant Performance Measures

```DAX
Revenue per Restaurant =
DIVIDE(
    [Total Revenue],
    [Total Restaurants]
)
```

```DAX
Avg Rating =
AVERAGE(Restaurants[Rating])
```

```DAX
Avg Votes =
AVERAGE(Restaurants[Votes])
```

## Time Intelligence Measures

```DAX
Revenue YTD =
TOTALYTD(
    [Total Revenue],
    DateTable[Date]
)
```

```DAX
Orders YTD =
TOTALYTD(
    [Total Orders],
    DateTable[Date]
)
```

```DAX
Revenue Previous Month =
CALCULATE(
    [Total Revenue],
    DATEADD(
        DateTable[Date],
        -1,
        MONTH
    )
)
```

```DAX
Revenue Growth % =
DIVIDE(
    [Total Revenue] - [Revenue Previous Month],
    [Revenue Previous Month]
)
```

