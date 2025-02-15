---
title: WYSIWYG Styles
summary: Add custom CSS properties to the rich-text editor.
icon: text-width
---

# WYSIWYG styles

Silverstripe CMS lets you customise the style of content in the CMS. This is done by setting up a CSS file called
`editor.css` in either your theme or in your `app/` folder. This is set through YAML config:

```yml
---
name: MyCSS
---
SilverStripe\Forms\HTMLEditor\TinyMCEConfig:
  editor_css:
    - 'app/css/editor.css'
```

Will load the `app/css/editor.css` file.

Alternatively, you can set this on a specific `TinyMCEConfig` instance via `setContentCSS` method.

```php
$config = new TinyMCEConfig();
$config->setContentCSS([ '/app/client/css/editor.css' ]);
```

[notice]
`silverstripe/admin` adds a small CSS file to `editor_css` which highlights broken links - you'll
probably want to include that in the array you pass to `setContentCSS()`, either by first calling
`getContentCSS()` and merging that array with your new one (and passing the result to `setContentCSS()`)
or by adding `'/_resources/vendor/silverstripe/admin/client/dist/styles/editor.css'` to the array you pass
to `setContentCSS()`
[/notice]

## Custom style dropdown

The custom style dropdown can be enabled via the `importcss` plugin bundled with admin module. ([Doc](https://www.tiny.cloud/docs/tinymce/6/importcss/))
Use the below code in `app/_config.php`:

```php
use SilverStripe\Forms\HTMLEditor\TinyMCEConfig;

TinyMCEConfig::get('cms')
    ->addButtonsToLine(1, 'styles')
    ->setOption('importcss_append', true);
```

Any CSS classes within this file will be automatically added to the `WYSIWYG` editors 'style' dropdown.
For instance, to
add the color 'red' as an option within the `WYSIWYG` add the following to the `editor.css`

```css
.red {
    color: red;
}
```

Adding a tag to the selector will automatically wrap with this tag. For example:

```css
h4.red {
    color: red;
}
```

will add an `h4` tag to the selected block.

For further customisation, customize the `style_formats` option.
`style_formats` won't be applied if you do not enable `importcss_append`.
Here is a working example to get you started.  
See related [TinyMCE doc](https://www.tiny.cloud/docs/tinymce/6/user-formatting-options/#style_formats).

```php
use SilverStripe\Forms\HTMLEditor\TinyMCEConfig;

$formats = [
    [ 'title' => 'Headings', 'items' => [
            ['title' => 'Heading 1', 'block' => 'h1' ],
            ['title' => 'Heading 2', 'block' => 'h2' ],
            ['title' => 'Heading 3', 'block' => 'h3' ],
            ['title' => 'Heading 4', 'block' => 'h4' ],
            ['title' => 'Heading 5', 'block' => 'h5' ],
            ['title' => 'Heading 6', 'block' => 'h6' ],
            [
                'title' => 'Subtitle',
                'selector' => 'p',
                'classes' => 'title-sub',
            ],
        ],
    ],
    [
        'title' => 'Misc Styles', 'items' => [
            [
                'title' => 'Style 1',
                'selector' => 'ul',
                'classes' => 'style1',
                'wrapper' => true,
                'merge_siblings' => false,
            ],
            [
                'title' => 'Button red',
                'inline' => 'span',
                'classes' => 'btn-red',
                'merge_siblings' => true,
            ],
        ],
    ],
];

TinyMCEConfig::get('cms')
    ->addButtonsToLine(1, 'styles')
    ->setOptions([
        'importcss_append' => true,
        'style_formats' => $formats,
    ]);
```

## API documentation

- [`HtmlEditorConfig`](api:SilverStripe\Forms\HTMLEditor\HtmlEditorConfig)
- [`TinyMCEConfig`](api:SilverStripe\Forms\HTMLEditor\TinyMCEConfig)
