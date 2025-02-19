---
title: Overview
---
import AutoScreenshot from "@components/AutoScreenshot.astro"

## Overview

Entry classes can be found in the `Filament\Infolists\Components` namespace. You can put them inside the `$infolist->schema()` method:

```php
use Filament\Schemas\Schema;

public function infolist(Schema $infolist): Schema
{
    return $infolist
        ->schema([
            // ...
        ]);
}
```

If you're inside a [panel builder resource](../../panels/resources), the `infolist()` method should be static:

```php
use Filament\Infolists\Infolist;

public static function infolist(Infolist $infolist): Infolist
{
    return $infolist
        ->schema([
            // ...
        ]);
}
```

Entries may be created using the static `make()` method, passing its unique name. You may use "dot notation" to access entries within relationships.

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')

TextEntry::make('author.name')
```

<AutoScreenshot name="infolists/entries/simple" alt="Entries in an infolist" version="4.x" />

## Available entries

- [Text entry](text)
- [Icon entry](icon)
- [Image entry](image)
- [Color entry](color)
- [Key-value entry](key-value)
- [Repeatable entry](repeatable)

You may also [create your own custom entries](custom) to display data however you wish.

## Setting a label

By default, the label of the entry, which is displayed in the header of the infolist, is generated from the name of the entry. You may customize this using the `label()` method:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->label('Post title')
```

Optionally, you can have the label automatically translated [using Laravel's localization features](https://laravel.com/docs/localization) with the `translateLabel()` method:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->translateLabel() // Equivalent to `label(__('Title'))`
```

## Entry URLs

When an entry is clicked, you may open a URL.

### Opening URLs

To open a URL, you may use the `url()` method, passing a callback or static URL to open. Callbacks accept a `$record` parameter which you may use to customize the URL:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->url(fn (Post $record): string => route('posts.edit', ['post' => $record]))
```

You may also choose to open the URL in a new tab:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->url(fn (Post $record): string => route('posts.edit', ['post' => $record]))
    ->openUrlInNewTab()
```

## Setting a default value

To set a default value for entries with an empty state, you may use the `default()` method. This method will treat the default state as if it were real, so entries like [image](image) or [color](color) will display the default image or color.

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->default('Untitled')
```

## Adding placeholder text if an entry is empty

Sometimes you may want to display placeholder text for entries with an empty state, which is styled as a lighter gray text. This differs from the [default value](#setting-a-default-value), as the placeholder is always text and not treated as if it were real state.

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->placeholder('Untitled')
```

<AutoScreenshot name="infolists/entries/placeholder" alt="Entry with a placeholder for empty state" version="4.x" />

## Hiding entries

To hide an entry conditionally, you may use the `hidden()` and `visible()` methods, whichever you prefer:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('role')
    ->hidden(! auth()->user()->isAdmin())
// or
TextEntry::make('role')
    ->visible(auth()->user()->isAdmin())
```

## Calculated state

Sometimes you need to calculate the state of an entry, instead of directly reading it from a database entry.

By passing a callback function to the `state()` method, you can customize the returned state for that entry:

```php
Infolists\Components\TextEntry::make('amount_including_vat')
    ->state(function (Model $record): float {
        return $record->amount * (1 + $record->vat_rate);
    })
```

## Tooltips

You may specify a tooltip to display when you hover over an entry:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('title')
    ->tooltip('Shown at the top of the page')
```

<AutoScreenshot name="infolists/entries/tooltips" alt="Entry with tooltip" version="4.x" />

This method also accepts a closure that can access the current infolist record:

```php
use Filament\Infolists\Components\TextEntry;
use Illuminate\Database\Eloquent\Model;

TextEntry::make('title')
    ->tooltip(fn (Model $record): string => "By {$record->author->name}")
```

## Custom attributes

The HTML of entries can be customized, by passing an array of `extraAttributes()`:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('slug')
    ->extraAttributes(['class' => 'bg-gray-200'])
```

These get merged onto the outer `<div>` element of each entry in that entry.

You can also pass extra HTML attributes to the entry wrapper which surrounds the label, entry, and any other text:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('slug')
    ->extraEntryWrapperAttributes(['class' => 'entry-locked'])
```

## Global settings

If you wish to change the default behavior of all entries globally, then you can call the static `configureUsing()` method inside a service provider's `boot()` method, to which you pass a Closure to modify the entries using. For example, if you wish to make all `TextEntry` components [`words(10)`](text#limiting-word-count), you can do it like so:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::configureUsing(function (TextEntry $entry): void {
    $entry
        ->words(10);
});
```

Of course, you are still able to overwrite this on each entry individually:

```php
use Filament\Infolists\Components\TextEntry;

TextEntry::make('name')
    ->words(null)
```
