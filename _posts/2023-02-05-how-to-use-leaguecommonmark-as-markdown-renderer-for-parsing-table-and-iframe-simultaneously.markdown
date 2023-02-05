---
title: How to use League/CommonMark as markdown renderer for parsing Table and Iframe simultaneously
layout: post
---

Today I have tried to use `League\CommonMark` as markdown parser. I have faced 2 issues. Basic `CommonMarkConverter` is not render table, task list & `GithubFlavoredMarkdownConverter` is not render `iframe` tag. 

In my case I need both features.    
* It should render table   
* It should render iframe 

In order to achieve that, I need to use `League\CommonMark` Extensions 

Here is my working code

```php
use League\CommonMark\MarkdownConverter;
use League\CommonMark\Environment\Environment;
use League\CommonMark\Extension\CommonMark\CommonMarkCoreExtensio;
use League\CommonMark\Extension\Autolink\AutolinkExtension;
use League\CommonMark\Extension\DisallowedRawHtml\DisallowedRawHtmlExtension;
use League\CommonMark\Extension\Strikethrough\StrikethroughExtension;
use League\CommonMark\Extension\Table\TableExtension;
use League\CommonMark\Extension\TaskList\TaskListExtension;

class Helpers {
  public static function markdown($string, array $options = [])
  {
    $environment = new Environment($options);
    $environment->addExtension(new CommonMarkCoreExtension());
    $environment->addExtension(new AutolinkExtension());
    $environment->addExtension(new StrikethroughExtension());
    $environment->addExtension(new TableExtension());
    $environment->addExtension(new TaskListExtension());
    $converter = new MarkdownConverter($environment);
    return $converter->convert($string);
  }
}
// Basic uses
echo Helpers::markdown('**Hello World!**');
// <p><strong>Hello World!</strong></p>

```

As I am using laravel framework, I can achieve by following code as well 

```php
public static function  markdown($string)
{
  $config = [
    'disallowed_raw_html' => [
        'disallowed_tags' => ['title', 'textarea', 'xmp', 'noembed', 'noframes', 'script', 'plaintext'],
    ],
  ];
  return Str::markdown($string, $config);
}
```

