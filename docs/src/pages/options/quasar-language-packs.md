---
title: Quasar Language Packs
related:
  - /options/rtl-support
  - /options/app-internationalization
---
A Quasar Language Pack refers to the internationalization of Quasar's own components, some of which have labels.

::: warning
It should be noted that what is described below is the internationalization of Quasar components only. If you need to internationalize your own components, read [App Internationalization](/options/app-internationalization) documentation page.
:::

As mentioned above, some Quasar components have their own labels. When it comes to internationalization, one option is to configure labels through the label properties on each instance of Quasar components (like QTable). This is how you can customize the text to match the selected language. This however, takes time and adds unnecessary complexity to your website/app. **Instead**, you can use the Quasar Language Packs which have a number of standard label definitions translated for you, like "Cancel", "Clear", "Select", "Update", etc. No need to translate these again! And it comes out of the box.

::: tip
For a complete list of available Quasar Languages, check [Quasar Languages on Github](https://github.com/quasarframework/quasar/tree/dev/quasar/lang).
<br><br>**If your desired language is not on that list**, then feel free to submit a PR to add it. It takes from 5 to 10 minutes at most. We kindly welcome any language!
:::

## Configuring the default Language Pack

Unless configured otherwise (see below), Quasar uses the `en-us` Language Pack by default.

### Hardcoded Default Language Pack
If the default Quasar Language Pack is not dynamically determined (does not depends on cookies for example), then you can:

#### Quasar CLI
Edit `/quasar.conf.js`:
```js
framework: {
  lang: 'de'
}
```

#### Vue CLI
Edit your `main.js`:
```js
import langDe from 'quasar/lang/de'
// ...

// when not selecting to import all Quasar components:
import { Quasar } from 'quasar'
// OTHERWISE:
import Quasar from 'quasar'

// ...
Vue.use(Quasar, {
  // ...,
  lang: langDe
})
```

#### Quasar UMD
Include the language pack JS tag for your Quasar version and also tell Quasar to use it. Example:

```html
<!-- include this after Quasar JS tag -->
<script src="https://cdn.jsdelivr.net/npm/quasar@v1.0.0/dist/lang/de.umd.min.js"></script>
<script>
  Quasar.lang.set(Quasar.lang.de)
</script>
```

Check what tags you need to include in your HTML files by generating a sample with `$ quasar create <folder> --kit umd` and specifying a language code for Quasar Language Pack (other than the default "en-us").

### Dynamically Picking Default Language
Quasar CLI: If your desired Quasar Language Pack must be dynamically selected (example: depends on a cookie), then you need to create a boot file: `$ quasar new boot quasar-lang-pack`. This will create `/src/boot/quasar-lang-pack.js` file. Edit it to:

```js
// for when you don't specify quasar.conf.js > framework: 'all'
import { Quasar } from 'quasar'
// OTHERWISE:
import Quasar from 'quasar'

export default async () => {
  const langIso = 'de' // ... some logic to determine it (use Cookies Plugin?)

  try {
    await import(`quasar/lang/${langIso}`)
      .then(lang => {
        Quasar.lang.set(lang.default)
      })
  }
  catch (err) {
    // Requested Quasar Language Pack does not exist,
    // let's not break the app, so catching error
  }
}
```

Then register this boot file into `/quasar.conf.js`:
```js
boot: [
  'quasar-lang-pack'
]
```

## Change Quasar Language Pack at Runtime
Example with a QSelect to dynamically change the Quasar components language:
```html
<template>
  <q-select
    v-model="lang"
    :options="langOptions"
    label="Quasar Language"
    dense
    borderless
    emit-value
    map-options
    options-dense
    style="min-width: 150px"
  />
</template>

<script>
import languages from 'quasar/lang/index.json'

export default {
  data () {
    return {
      lang: this.$q.lang.isoName
    }
  },

  watch: {
    lang (lang) {
      // dynamic import, so loading on demand only
      import(`quasar/lang/${lang}`).then(lang => {
        this.$q.lang.set(lang.default)
      })
    }
  },

  created () {
    this.langOptions = languages.map(lang => ({
      label: lang.nativeName, value: lang.isoName
    }))
  }
}
</script>
```

## Using Quasar Language Pack in App Space
Although the Quasar Language Packs **are designed only for Quasar components internal usage**, you can still use their labels for your own website/app components too.

```html
"Close" label in current Quasar Language Pack is:
{{ $q.lang.label.close }}
```

Check a Quasar Language Pack on [Github](https://github.com/quasarframework/quasar/tree/dev/quasar/lang) to see the structure of `$q.lang`.

## Detecting Locale
There's also a method to determine user locale which is supplied by Quasar out of the box:
```js
// outside of a Vue file

// for when you don't specify quasar.conf.js > framework: 'all'
import { Quasar } from 'quasar'
// OTHERWISE:
import Quasar from 'quasar'

Quasar.lang.getLocale() // returns a string

// inside of a Vue file
this.$q.lang.getLocale() // returns a string
```