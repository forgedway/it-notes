# Unicode
**Unicode** is a text encoding standard that provides a coded character set (a
map between code points and a character set). It also specifies rules,
algorithms and properties necessary to achieve interoperability between
different platforms and languages. It's designed to support the use of text in
all of the world's writing systems that can be digitized.

> Code point - a number or a position which corresponds to a character.

Main points:
- It can have up to 1.1 million characters (20 bits for a code point).
- It can be encoded by `UTF-8`, `UTF-16`, `GB18030` and other encodings.
- Code points are usually represented in `U+XXXX` form, like `U+00D8` for `Ø`
glyph.
- It uses `BOM` character to ensure byte order and to mark an encoding format.
- It provides precomposed characters and allows combine characters manually:
  - **Precomposed:** `Å`(`U+00C5`) or `Ê`(`U+00CA`)
  - **Manually:** `Å`(`U+0041 + U+030A`) or `Ê`(`U+0045 + U+0302`)
  - **Combining Diacritical Marks** range is `U+0300` - `U+036F`
- The first 128 (256) characters are `ASCII` (`ISO-8859-1`) characters.
- It has emoji characters, like `😎` and `👍`.

Additionally:
- To search or compare strings they might be normalized because of the combined
symbols and precomposed characters.
- To check if Unicode strings are visually similar the standard provides
`confusables.txt` and `confusablesSummary.txt`
- The coded character set can be seen by
[gucharmap](https://en.wikipedia.org/wiki/GNOME_Character_Map) application.
- To convert or normalize Unicode strings in bash `uconv` utility might be used.

## Comparison to UCS
**UCS (The Universal Coded Character Set)** is a coded character set defined by
the `ISO/IEC 10646`. Originally, it was an unrelated standard to Unicode, but in
1990 it was unified with Unicode.

The UCS standard also have two fixed-length encodings: `UCS-2` and `UCS-4`.
These encodings are considered outdated and have been superseded by `UTF-16`
and `UTF-32` respectively.

Comparison of `UCS` and `Unicode`:
- They have the same character set (repertoire) and code points (assigned
numbers to characters).
- `Unicode` has the additional rules, algorithms and character properties.
- `Unicode` is updated more frequently.

## Unicode encodings
Unicode doesn't encode characters on its own, it defines and uses `UTF-8`,
`UTF-16` and `UTF-32`. There are also other non-standard encodings that are
in use for Unicode like `GB18030` (Chinese government standard).

Topic                                               | UTF-8   | UCS-2 | UTF-16  | UTF-32 (aka UCS-4)
:--                                                 | :--:    | :--:  | :--:    | :--:
Byte length per code point                          | 1 - 4   | 2     | 2 or 4  | 4
Can access to Nth character by its index            | -       | +     | -       | +
Endiant independent (doesn't use BOM)               | +       | -     | -       | -
Can represent code points after 65536 (over U+FFFF) | +       | -     | +       | +
Backward campatible with                            | `ASCII` | -     | `UCS-2` | -

**UTF-16 surrogates ranges:**
- High surrogates (`U+D800-U+DBFF`) and low surrogates (`U+DC00-U+DFFF`) are used
  to represent code points that are not in the Basic Multilingual Plane
  (`U+0000-U+FFFF`).
- It provides `1024 * 1024 = 1048576` additional code points.
- It uses high then low surrogate character order so that an application could
  determine if it's at the start or in the middle of a character.

## BOM
BOM (The Byte-Order Mark) is a special Unicode character code `U+FEFF` that is
placed at the start of a text stream and serves several purposes.

It signals:
- That the text stream is using Unicode.
  - `true` or `false`
- The byte order, or endianness, of the text stream in the cases of 16-bit and
32-bit encodings.
  - `big-endian` or `little-endian`
- Which encoding is used
  - `UTF-8`, `UTF-16`, `GB18030`, ...

`BOM` with different encodings:
Hex number  | Signals
--          | --
EF BB BF    | `UTF-8`
FE FF       | `UTF-16`, `Big-endian`
FF FE       | `UTF-16`, `Little-endian`
00 00 FE FF | `UTF-32`, `Big-endian`
FF FE 00 00 | `UTF-32`, `Little-endian`
84 31 95 33 | `GB18030`

> `UTF-8` with `BOM` marker can be also called as `utf-8-sig`.

> The standard doesn't recomend using a `BOM` with `UTF-8` due to compatibility
issues with `ASCII`.

## Character properties
Each character has a set of properties, some of them are:
- Name and alias:
  - U+0471 CYRILLIC SMALL LETTER PSI (`ѱ` `U+0471`)
- General category:
  - Letter; uppercase (`U` `U+0055`)
  - Number; decimal digit (`5` `U+0035`)
- Combining class:
  - 0 = spacing letter, symbol or modifier (`d` `U+0064`)
  - 1 = overlay (not attached `^` `U+0302`)
  - 230 = above (not attached `^` `U+0302`)
- Numeric values and types:
  - 7, decimal (`7` `U+0037`)
  - 6, numeric (`Ⅵ` `U+2165`)
- Block:
  - Basic Latin (`I` `U+0049`)
  - Cyrillic (`Ц` `U+0426`)
- Normalization properties:
  - Decompression type: canonical, mapping: Е U+0415 +  ̈ U+0308 (`Ё` `U+0401`)
  - Decompression type: none, mapping: Ψ U+03A8 (`Ψ` `U+03A8`)
- Age:
  - 1.1 (`Ā` `U+0100`)
  - 5.2 (`⅐` `U+2150`)
- ...

[Codepoints](https://codepoints.net/) is a resource that allows to explore
Unicode character properties.

## Normalization
Normalization is the process of converting a Unicode string to a normal form.
It's a necessity for text processing software to perform string search,
comparison and sometimes editing. It's especially important when working with
combining marks.

Unicode has 4 forms and algorithms respectively:
- **NFD (Normalization Form Canonical Decomposition):**
  - Decomposes combining characters (e.g. diacritical) and places them in a
    specific order.
- **NFC (Normalization Form Canonical Composition):**
  - Recomposes by canonical equivalence.
- **NFKD (Normalization Form Compatibility Decomposition):**
  - Same as **NFD**, but also replaces characters by meaning.
- **NFKC (Normalization Form Compatibility Composition):**
  - Same as **NFKC**, but also replaces characters by meaning.

**Composition / Decomposition** is needed for string comparison. Meanwhile, the
**Compatibility** form is crucial for such things as search by a string (e.g. by
a search engine).

<!-- Some fonts don't show diacritical marks with the dotted circle -->
<!-- Dotted circle: U+25CC: ◌ -->
Composition / Decomposition examples:
Source                             | NFD <br> (Decomposition) | NFC <br> (No decomposition) | NFKD <br> (Decomposition) | NFKC <br> (No decomposition)
:--:                               | :--:                     | :--:                        | :--:                      | :--:
Ў <br> (or У + ◌̆)                  | У + ◌̆                    | Ў                           | У + ◌̆                     | Ў
`U+040E` <br> (or `U+0423 U+0306`) | `U+0423 U+0306`          | `U+040E`                    | `U+0423 U+0306`           | `U+040E`

Compatibility / No compatibility examples:
Source   | NFD <br> (No compatibility) | NFC <br> (No compatibility) | NFKD <br> (Compatibility) | NFKC <br> (Compatibility)
:--:     | :--:                        | :--:                        | :--:                      | :--:
⅖        | ⅖                           | ⅖                           | 2 ⁄ 5                     | 2 ⁄ 5
`U+2156` | `U+2156`                    | `U+2156`                    | `U+0032 U+2044 U+0035`    | `U+0032 U+2044 U+0035`

Decomposition example via `uconv -x Any-NFD`:
```bash
# Uconv input: |Ё|
# - U+0401
# - D0 81 (UTF-8)
#
# Uconv output: |Е + ◌̈|
# - U+0415 U+0308
# - 00 00 04 15 00 00 03 08 (UTF-32BE)
echo -n "Ё" | uconv -x Any-NFD | uconv --to-code UTF-32BE | hexdump -C
```

Compatibility example via `uconv -x Any-NFKC`:
```bash
# Uconv input: |Ⅳ|
# - U+2163
# - E2 85 A3 (UTF-8)
#
# Uconv output: |I + V|
# - U+0049 U+0056
# - 00 00 00 49 00 00 00 56 (UTF-32BE)
echo -n "Ⅳ" | uconv -x Any-NFKC | uconv --to-code UTF-32BE | hexdump -C
```
