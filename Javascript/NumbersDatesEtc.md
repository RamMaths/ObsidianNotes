# Numbers, Dates, Intl and Timers

## Numbers
- In javascript all numbers are represented internally as floating point numbers, always as decimals no matter if we actually write them as integers or as deciamls.
-  Numbers are represented internally in a 64 base 2 format, this means that numbers are always stored ni a binary format.

# Internalization API

To work with this API you need to create an object of the Intl class then access to the different methods that are available.

## Formating dates

to format dates you need to call the  ``DateTimeFormat``, this method needs an iso language code, and as a second argument could recive an object with different options of what you want to format like the hour, minutes, the day, etc. then you call `format` which recives a `Date` Object.

```js
const now = new Date();
const options = {
    hour: 'numeric',
    minute: 'numeric',
    day: 'numeric',
    month: 'long',
    year: 'numeric',
};
const locale = navigator.language;

labelDate.textContent = new Intl.DateTimeFormat(locale, options).format(now);
```