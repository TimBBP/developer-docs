---
title: Customise site reports
summary: Creating your own custom data or content reports.
---

# Customise site reports

## Introduction

Reports are a useful feature in the CMS designed to provide a view of your data or content. You can access
the site reports by clicking *Reports* in the left hand side bar and selecting the report you wish to view.

![a list of reports in the CMS](../../../_images/sitereport.png)

## Counts in `ReportAdmin`

For large datasets, the reports section may take a long time to load, since each report is getting a count of the items it contains to display next to the title.

To mitigate this issue, there is a cap on the number of items that will be counted per report. This is set at 10,000 items by default, but can be configured using the `limit_count_in_overview` configuration variable. Setting this to `null` will result in showing the actual count regardless of how many items there are.

```yml
SilverStripe\Reports\Report:
  limit_count_in_overview: 500
```

[notice]
Note that some reports may have overridden the `getCount` method, and for those reports this may not apply.
[/notice]

## Default reports

By default the CMS ships with several basic reports:

- VirtualPages pointing to deleted pages
- RedirectorPages pointing to deleted pages
- Pages with broken files
- Pages with broken links
- Broken links report
- Pages with no content
- Pages edited in the last 2 weeks

Modules may come with their own additional reports.

## Creating custom reports

Custom reports can be created quickly and easily. A general knowledge of Silverstripe CMS's
[datamodel and ORM](../../model/data_model_and_orm) is useful before creating a custom report.

Inside the `app/code/` folder create a file called `CustomSiteReport.php`. Inside this file we can add our site reports.

The following example will create a report to list every page on the current site.

```php
// app/src/Report/CustomSiteReport.php
namespace App\Report;

use Page;
use SilverStripe\Reports\Report;

class CustomSiteReport extends Report
{
    // the name of the report
    public function title()
    {
        return 'All Pages';
    }

    // what we want the report to return
    public function sourceRecords($params = null)
    {
        return Page::get()->sort('Title');
    }

    // which fields on that object we want to show
    public function columns()
    {
        $fields = [
            'Title' => 'Title',
        ];

        return $fields;
    }
}
```

More useful reports can be created by changing the `DataList` returned in the `sourceRecords` function.

## Notes

- `CustomSiteReport` must extend `Report`
- It is recommended to place all custom reports in the 1 file.
- Create a `CustomSiteReport.php` file and add classes as you need them inside for each report

## TODO

- How to format and make advanced reports.
- More examples

## API documentation

[ReportAdmin](api:SilverStripe\Reports\ReportAdmin)
