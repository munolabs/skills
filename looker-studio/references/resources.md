# Learning Resources - Looker Studio

## Official Documentation

| Resource | URL |
|----------|-----|
| Main Documentation | https://docs.cloud.google.com/looker/docs/studio |
| Function List | https://docs.cloud.google.com/looker/docs/studio/function-list |
| Calculated Fields | https://docs.cloud.google.com/looker/docs/studio/about-calculated-fields |
| Connect to PostgreSQL | https://docs.cloud.google.com/looker/docs/studio/connect-to-postgresql |
| Data Blending | https://docs.cloud.google.com/looker/docs/studio/blended-data |
| Developer Portal | https://developers.google.com/looker-studio |
| Template Gallery | https://lookerstudio.google.com/gallery |

---

## Online Courses

### LinkedIn Learning (81 courses available)
https://www.linkedin.com/learning/search?keywords=looker%20studio

| Course | Duration | Level |
|--------|----------|-------|
| Looker Studio for Beginners | 46 min | Beginner |
| Looker Studio for Marketers | 1h 16m | Intermediate |
| Data Analytics with BigQuery and Looker Studio | 1h 5m | Intermediate |

### Google Skills / Cloud Skills Boost
https://www.skills.google/catalog?keywords=looker%20studio
- Interactive hands-on labs
- Google Cloud certifications

---

## Recommended YouTube Channels

| Channel | Focus |
|---------|-------|
| **MeasureSchool** | Analytics and Looker Studio tutorials |
| **Analytics Mania** | Advanced Data Studio/Looker Studio |
| **Google Analytics** (official) | Official tutorials |
| **Loves Data** | Google Analytics and Data Studio |
| **Jeff Sauer** | Marketing analytics and dashboards |
| **Supermetrics** | Connectors and advanced tutorials |

---

## Blogs and Tutorials

### OWOX Blog
https://www.owox.com/blog/articles/looker-studio-tutorial/
- Step-by-step tutorials
- Digital marketing tips

### Seer Interactive
https://www.seerinteractive.com/insights/google-data-studio-tutorial
- Visualization best practices
- SEO and marketing use cases

---

## Popular Connectors

### Google Native
- Google Analytics 4
- Google Ads
- Google Search Console
- Google BigQuery
- YouTube Analytics
- Google Sheets

### Databases
- MySQL
- PostgreSQL
- Microsoft SQL Server
- MariaDB

### Marketing & Advertising
- Facebook/Meta Ads
- LinkedIn Ads
- TikTok Ads
- Pinterest Ads
- X (Twitter) Ads

### CRM
- Salesforce
- HubSpot
- Zoho CRM

### E-commerce
- Shopify
- WooCommerce
- Magento

**Total:** 1000+ connectors available

---

## Use Cases by Industry

### Digital Marketing
- Ad campaign reports
- Website traffic analysis (GA4)
- SEO performance
- Social media reports

### E-commerce
- Conversion analysis
- Customer behavior
- Product performance
- Sales reports

### Finance
- Financial KPI dashboards
- Revenue analysis
- Budget reports

### Operations
- Operational metrics monitoring
- Inventory tracking
- Productivity analysis

---

## Best Practices

### Dashboard Design
1. **Define clear objective** before creating
2. **Limit metrics** to most important (5-7 max)
3. **Use visual hierarchy** (important at top/left)
4. **Color consistency** throughout report
5. **Descriptive titles** on each chart

### Performance
1. **Limit data** with date filters
2. **Use pre-aggregated views** in database
3. **Avoid complex calculated fields** when possible
4. **Create fields at source level** (not in charts)

### Calculated Fields
1. **Use IFNULL** to handle nulls
2. **Avoid division by zero** with NULLIF
3. **Test simple formulas** before complicating
4. **Document complex formulas** (comments)

### Collaboration
1. **Share with appropriate permissions**
2. **Use templates** for consistency
3. **Schedule report delivery** by email
4. **Version important changes**

---

## Community and Support

| Resource | URL |
|----------|-----|
| Stack Overflow | Tag: `looker-studio` |
| Google Issue Tracker | For reporting bugs |
| GitHub Samples | Connector examples |

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl + Z` | Undo |
| `Ctrl + Y` | Redo |
| `Ctrl + C` | Copy |
| `Ctrl + V` | Paste |
| `Ctrl + D` | Duplicate |
| `Delete` | Delete selection |
| `Ctrl + A` | Select all |
| `Ctrl + G` | Group |
| `Ctrl + Shift + G` | Ungroup |

---

## Quick Reference Card

### Most Common Formulas

```sql
-- Date: Subtract hours (timezone)
DATETIME_SUB(field, INTERVAL 5 HOUR)

-- Date: Format
FORMAT_DATETIME('%m/%d/%Y', date_field)

-- Text: Combine
CONCAT(field1, ' ', field2)

-- Logic: Conditional
CASE WHEN condition THEN 'Yes' ELSE 'No' END

-- Logic: Default for null
IFNULL(field, 'default')

-- Math: Avoid division by zero
field1 / NULLIF(field2, 0)

-- Aggregation: Unique count
COUNT_DISTINCT(field)
```

### Data Type Conversions

```sql
-- To text
CAST(number_field AS TEXT)

-- To number
CAST(text_field AS NUMBER)

-- To date
PARSE_DATE('%Y-%m-%d', text_field)
```
