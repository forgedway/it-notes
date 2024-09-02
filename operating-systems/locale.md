# Locale
**A locale** is a set of language and country-specific conventions allowing to
adapt software to a user's preferences.

Locale categories are used to configure the localization behaviour. Each
category name can be used as an environment variable to set the corresponding
part of localization behaviour.

Name              | `C`     | `POSIX` | `Glibc` | Description
--                | --      | --      | --      | --
**`SPECIAL:`**    |         |         |         |
LC_ALL            | ✅      | ✅      | ✅      | **Forces** all other **LC_** categories to the set locale.
LANG              | ❌      | ✅      | ✅      | **It's not a category**. It's only an environment variable that is used to derive all other unspecified categories.
LC_IDENTIFICATION | ❌      | ❌      | ✅      | Contains information about the locale itself and is **rarely useful** for users or developers.
**`GENERAL:`**    |         |         |         |
LC_CTYPE          | ✅      | ✅      | ✅      | Affects classification and conversion of characters.
LC_COLLATE        | ✅      | ✅      | ✅      | Affects collation (sorting and comparison) of strings.
LC_TIME           | ✅      | ✅      | ✅      | Affects the formatting date and time variables.
LC_NUMERIC        | ✅      | ✅      | ✅      | Affects the formatting numeric variables that are not monetary.
LC_MONETARY       | ✅      | ✅      | ✅      | Affects the formatting monetary variables.
LC_MESSAGES       | ❌      | ✅      | ✅      | Sets the language of the application's messages.
LC_PAPER          | ❌      | ❌      | ✅      | Sets the paper size (A4 or US).
LC_MEASUREMENT    | ❌      | ❌      | ✅      | Sets the measurement system (metric or US)
LC_NAME           | ❌      | ❌      | ✅      | Affects the representation of a person's name and title.
LC_ADDRESS        | ❌      | ❌      | ✅      | Affects the representation of postal addresses, country and language names.
LC_TELEPHONE      | ❌      | ❌      | ✅      | Affects the representation of telephone numbers.

The categories can be set as the environment variables. For example, running of
a program with `en_US.UTF-8` locale can be achieved by the following:
```bash
LANG=en_US.UTF-8 ./program
```

## Shell examples
`LC_MESSAGES` (changes program messages):
```bash
$: export LC_MESSAGES=en_US.UTF-8
$: ls nonexistent
#  ls: cannot access 'nonexistent': No such file or directory

$: export LC_MESSAGES=ru_RU.UTF-8
$: ls nonexistent
#  ls: невозможно получить доступ к 'nonexistent': Нет такого файла или каталога
```

`LC_CTYPE`:
```bash
$: export LC_CTYPE=en_US.UTF-8
$: echo "hello привет" | grep --only-matching "[[:alpha:]]*"
#  hello
#  привет

$: export LC_CTYPE=C
$: echo "hello привет" | grep --only-matching "[[:alpha:]]*"
#  hello
```

`LC_COLLATE`:
```bash
$: export LC_COLLATE=en_US.UTF-8
$: echo "a\nA" | sort
#  a
#  A

$: export LC_COLLATE=C
$: echo "a\nA" | sort
#  A
#  a
```

`LC_NUMERIC`
```bash
$: export LC_NUMERIC=en_US.UTF-8
$: printf "%'d\n" 1000000
#  1,000,000

$: export LC_NUMERIC=ru_RU.UTF-8
$: printf "%'d\n" 1000000
#  1 000 000
```

## Locale names
Some valid locale names are:
- en_US.UTF-8
- ru_RU.KOI8-R
- de_DE.ISO-8859-1@euro
- zh_CN.GB18030

The locale name schema:  
`language[_territory[.codeset]][@modifier]`

> - `ISO 639-1` is used for language codes.
> - `ISO 3166-1 alpha-2`  is used for country codes.

For example`de_AT.ISO-8859-1@euro` denotes:
- `de` - German language.
- `AT` - Austria.
- `ISO-8859-1` - ISO-8859-1 encoding.
- `euro` - use Euro as the currency symbol.

### C locale
**C locale** (same as **POSIX locale**) is a valid locale that sets the same
behaviour as the default `C` runtime. It's often used as a default locale in
Unix-like operating systems. There is also the `C.UTF-8` locale that allows
programs (e.g. `grep`) use characters from the Unicode character set.

The `C` locale is useful for programming when you want predictable behaviour
that is not affected by the user's preferences.

For example, it might be useful to start a `bash` script with:
```bash
#!/usr/bin/env bash

set -euo pipefail
export LC_ALL=C

...
```

## Setting precedence
> - `LANG` - It is an environment variable that contains a default locale for
> all categories that aren't explicitely set.
> - `LC_ALL` - It is a category that forcibly sets all other categories to the
> given locale.

So the priorities are in the following order:
1. **LANG**
2. **LC_*** variables.
3. **LC_ALL**

For example, we can use the following to make all messages in the Russian
language and leave everything else in English:
```bash
export LC_ALL=
export LANG=en_US.UTF-8
export LC_MESSAGES=ru_RU.UTF-8
# It will result in
#
# LC_MESSAGES == ru_RU.UTF-8
# LC_* == en_US.UTF-8
```

We can use `LC_ALL` to rewrite all previous settings:
```bash
# Whatever
export LANG=en_US.UTF-8
export LC_MESSAGES=ru_RU.UTF-8

# Set everything to `C` locale.
export LC_ALL=C
# It will result in
#
# LC_* == C
```

## POSIX
In the context of locales, `POSIX` standard describes:
- `POSIX` locale which tends to be the same as `C` locale.
- `C API` that extends `ISO C` standard library (see the other sections).
- Utilities:
  - `locale` - Gets locale-specific information
  - `localedef` - Compiles a locale from source files.

### Locale utility examples:
**Lists the current locale settings:**
<details><summary>locale</summary>

```bash
$: locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```
</details>

**Lists available locales in the system:**
<details><summary>locale -a</summary>

```bash
$: locale -a
C
C.utf8
de_DE.utf8
en_US.utf8
POSIX
ru_RU.utf8
zh_CN.gb18030
```
</details>

**Lists values of the current locale:**
<details><summary>locale &ltkeyword | category&gt</summary>

```bash
$: locale yesexpr
^[+1yY]

$: locale noexpr
^[-0nN]

$: locale LC_MESSAGES
^[+1yY]
^[-0nN]
yes
no
UTF-8
```
</details>

### Localedef utility
`localedef` utility is used to compile and make locales available for futher
use.

Locale compilation example:
```bash
localedef -f UTF-8 -i fi_FI fi_FI.UTF-8
# |-f UTF-8|    - setting encoding.
# |-i fi_FI|    - setting the input file that defines locale.
# |fi_FI.UTF-8| - The target output locale.
```
> The file search locations and the target output locale handling are
> implementation specific.

## Glibc and Gnu/Linux implementation
`Glibc` distribution implements and extends `POSIX` standard in the context of
locales.

> Note: The implementation may vary between different Linux distributions.

Files and directories:
- `/usr/lib/locale/locale-archive`
  - It's the archive which contains all `Glibc` available (compiled) locales in
    the system.
  - `localedef` is used to compile and automatically add locales in the archive.
  - `locale` utility gets information from the `locale-archive`.
  - Applications wihch use locale features by `Glibc` maps parts of the archive
    into the internal memory space.
- `/usr/share/i18n/` - The place where glibc keeps locale specific files such as
    locale definitions and charmaps. The `localedef` utility uses these files to
    compile locales.
- `/etc/locale.gen` and `locale-gen`
  - `/etc/locale.gen` is a text file that contains all locales that the user
    wants to have available on the system.
  - `locale-gen` is an executable that reads `/etc/locale.gen` and compiles them
    with `localedef` utility into the `locale-archive`.
- `/etc/locale.conf` is a file that `systemd` sources early during the boot.
  Only locale specific environment variables may be set by the file.

## ISO C
By default `C` uses its own "C locale".

`ISO C` defines header `<locale.h>` with:
- Functions:
  - `setlocale`:
    - Sets and gets the current locale.
    - `char *setlocale(int category, const char *locale)`
  - `localeconv`:
    - Gets locale conventions (variables).
    - `struct lconv *localeconv(void)`
- Categories (Macros to use with `setlocale`):
  - `LC_ALL`
  - `LC_COLLATE`
  - `LC_CTYPE`
  - `LC_MONETARY`
  - `LC_NUMERIC`
  - `LC_TIME`
- Structure `lconv` - Contains locale conventions (variables).

Example:
```C
#include <stdio.h>
#include <stdlib.h>
#include <locale.h>

int main(void) {
  // Shows the default C locale currency symbol.
  printf("'%s'\n", localeconv()->currency_symbol); // ''

  // Shows ru_RU.UTF-8 locale currency symbol.
  setlocale(LC_MONETARY, "ru_RU.UTF-8");
  printf("'%s'\n", localeconv()->currency_symbol); // '₽'

  // Shows user's default locale currency symbol.
  setlocale(LC_MONETARY, "");
  printf("'%s'\n", localeconv()->currency_symbol); // '$'

  // Resets all categories to be C locale again.
  setlocale(LC_ALL, "C");

  return EXIT_SUCCESS;
}
```
> :warning: The snippet's error handling is simplified.  
> :warning: `ru_RU.UTF-8` locale must be available in the system.  

## POSIX C API and Glibc C API implementation
`POSIX` and `Glibc` extend `ISO C` in the context of locale handling.

Extension:
- `locale_t` - A structure that allows to handle different category settings as
  one whole.
- Functions:
  - `newlocale`
    - **Creates** or **changes** existing locale.
    - `locale_t newlocale(int, const char *, locale_t);`
  - `freelocale`
    - `void freelocale(locale_t);`
  - `uselocale`
    - `locale_t uselocale (locale_t);`
  - `duplocale`
    - `locale_t duplocale(locale_t);`

- Categories (Macros):
  - `LC_MESSAGES`
  - `LC_NAME`
  - ... (see the table at the beginning).
- Category masks (Macros):
  - `LC_CTYPE_MASK`
  - `LC_COLLATE_MASK`
  - `...`
- `LC_GLOBAL_LOCALE` - The macro to reference the global locale that is used
  before `uselocale` is called.

<details><summary>Example (Long)</summary>

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>
#include <time.h>

void error_exit(const char* message) {
  perror(message);
  exit(EXIT_FAILURE);
}

char* get_local_time(void) {
  // Gets local time.
  time_t t = time(NULL);
  struct tm* local_time = localtime(&t);
  if (local_time == NULL) {
    error_exit("local_time");
  }

  // Converts local time to a string
  char buf[100];
  size_t return_string_size = strftime(buf, sizeof(buf), "%c", local_time);
  if (return_string_size == 0) {
    error_exit("strftime");
  }

  // Creates a return local time string.
  char* return_local_time = malloc(return_string_size + 1);
  if (return_local_time == NULL) {
    error_exit("malloc");
  }
  strcpy(return_local_time, buf);

  return return_local_time;
}

int main(void) {
  // Creates a new user's default locale.
  locale_t locale = newlocale(LC_ALL_MASK, "en_US.UTF-8", (locale_t)0);
  if (locale == (locale_t)0) {
    error_exit("LC_ALL locale");
  }

  // Update the locale's LC_TIME category to use C locale.
  locale_t new_locale = newlocale(LC_TIME_MASK, "C", locale);
  if (new_locale == (locale_t)0) {
    error_exit("LC_TIME locale");
  }
  locale = new_locale;

  uselocale(locale);

  printf("%s\n", "en_US.UTF-8 numbers:");
  printf("\t%'d\n", 1000000); // prints

  char* local_string = get_local_time();
  printf("%s\n", "C time:");
  printf("\t%s\n", local_string);

  free(local_string);
  // Note that we set default global locale BEFORE freeing our locale.
  uselocale(LC_GLOBAL_LOCALE);
  freelocale(locale);

  exit(EXIT_SUCCESS);
}
```

Output:
```txt
1,000,000
Sat Aug 31 17:18:45 2024
```
> :warning: Note: `en_US.UTF-8` locale must be available in the system.
</details>
