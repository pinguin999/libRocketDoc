---
layout: page
title: Localisation
---

While libRocket fully supports localisation, there are a number of issues you will need to be aware of.

### String encoding

libRocket assumes all data it is given, whether read in from RML or provided procedurally, is in UTF-8 encoding. This means if you're using 8-bit ASCII you don't need to change anything, but allows you to specify multi-byte Unicode characters if required.

Once libRocket has parsed text (and run it through the translator, see below), the strings will be converted into UCS-2, strictly for performance reasons.

### Translation

All raw text that libRocket reads while parsing RML (i.e., everything other than XML tags) is sent through the TranslateString() function on the [system interface](cpp_manual/interfaces.html#the-system-interface). The function is given the raw string as read, and the application can make any modifications necessary before returning the translated string (and the number of substitutions made) back to libRocket.

A pass-through translator would do the following:

```cpp
#include <Rocket/Core/SystemInterface.h>

class SampleSystemInterface : public Rocket::Core::SystemInterface
{
	int TranslateString(Rocket::Core::String& translated, const Rocket::Core::String& input)
	{
		translated = input;
		return 0;
	}
```

#### String tables

The TranslateString() method can be used in conjunction with an application's string table to make text substitutions on a document's text. For example, take the pause.rml file in the Rocket Invaders sample:

```html
<rml>
	<head>
		<title>Quit?</title>
	</head>
	<body>
		<p>Are you sure you want to end this game?</p>
		<button>Yes</button>
		<button>No!</button>
	</body>
</rml>
```

If we were to localise Rocket Invaders, we'd want to move all of the English strings out from the RML and into a string table. The raw text in the RML would then be replaced with the string table tokens:

```html
<rml>
	<head>
		<title>[QUIT_TITLE]</title>
	</head>
	<body>
		<p>[QUIT_CONFIRM]</p>
		<button>[CONFIRM]</button>
		<button>[DENY]</button>
	</body>
</rml>
```

Assuming the appliation has a StringTable class that has loaded the appropriate string table for the language, our sample translator would then become:

```cpp
	int TranslateString(Rocket::Core::String& translated, const Rocket::Core::String& input)
	{
		// Attempt to find the translation in the string table.
		if (StringTable::GetString(translated, input))
			return 1;

		// No translation; return the raw input string.
		translated = input;
		return 0;
	}
```

Now the strings will be valid for whatever language we specify a string table for. In practice, you might need a more sophisticated translator that could replace multiple tokens within a string.

Note that you can place RML into the translated string, and it will be parsed appropriately. For example, you could replace a token with an <img> tag to render an icon for a controller button.

### Font charsets

libRocket's fonts have configurable character sets. By default, a font will support only the Basic Latin characters (codepoints 32 - 126) in order to save texture space. In order to support the character sets required by non-English languages, use the 'font-charset' property. For example, to specify a character set including the Latin-1 supplement (codepoints 0x80 - 0xFF), you would set the following RCSS property:

```
font-charset: U+0020-00FF;
```

If libRocket renders text using a font that does not include all the necessary characters, the missing characters will be ignored. For more information on the font-charset property, see the [documentation](rcss/fonts.html#font-charset-the-font-charset-property). For information on Unicode character sets, try [unicode.org](http://unicode.org/charts) or [J.R. Graphics](http://jrgraphix.net/research/unicode_blocks.php). 