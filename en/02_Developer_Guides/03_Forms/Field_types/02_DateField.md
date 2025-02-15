---
title: DateField
summary: How to format and use the DateField class.
---

# DateField

This `FormField` subclass lets you display an editable date, in a single text input field.
It implements the [HTML5 input date type](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/date)
(with `type=date`). In supported browsers, this will cause a localised date picker to appear for users.
HTML5 date fields present and save ISO 8601 date formats (`y-MM-dd`),
since the browser takes care of converting to/from a localised presentation.
Browsers without support receive an `<input type=text>` based polyfill.

The following example will add a simple DateField to your Page, allowing you to enter a date manually.

```php
// app/src/PageType/MyPage.php
namespace App\PageType;

use Page;
use SilverStripe\Forms\DateField;

class MyPage extends Page
{
    private static $db = [
        'MyDate' => 'Date',
    ];

    public function getCMSFields()
    {
        $fields = parent::getCMSFields();

        $fields->addFieldToTab(
            'Root.Main',
            DateField::create('MyDate', 'Enter a date')
        );

        return $fields;
    }
}
```

## Custom date format

A custom date format for a [DateField](api:SilverStripe\Forms\DateField) can be provided through `setDateFormat`.
This is only necessary if you want to opt-out of the built-in browser localisation via `type=date`.

```php
use SilverStripe\Forms\DateField;

// will display a date in the following format: 31/06/2012
DateField::create('MyDate')
    ->setHTML5(false)
    ->setDateFormat('dd/MM/yyyy');
```

[info]
The formats are based on [ICU format](https://unicode-org.github.io/icu/userguide/format_parse/datetime/#simpledateformat).
[/info]

## Min and max dates

Sets the minimum and maximum allowed date values using the `min` and `max` configuration settings (in ISO format or
`strtotime()`).

```php
use SilverStripe\Forms\DateField;

DateField::create('MyDate')
    ->setMinDate('-7 days')
    ->setMaxDate('2012-12-31')
```

## Formatting hints

It's often not immediate apparent which format a field accepts, and showing the technical format (e.g. `HH:mm:ss`) is
of limited use to the average user. An alternative is to show the current date in the desired format alongside the
field description as an example.

```php
use SilverStripe\Forms\DateField;

$dateField = DateField::create('MyDate');

// Show long format as text below the field
$dateField->setDescription(_t(
    'FormField.Example',
    'e.g. {format}',
    [ 'format' => $dateField->getDateFormat() ]
));

// Alternatively, set short format as a placeholder in the field
$dateField->setAttribute('placeholder', $dateField->getDateFormat());
```

## API documentation

- [DateField](api:SilverStripe\Forms\DateField)
