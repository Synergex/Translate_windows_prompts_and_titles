# Translate_windows_prompts_and_titles<br />
**Created Date:** 1/8/2004<br />
**Last Updated:** 5/22/2008<br />
**Description:** Translate.dbl is a replacement routine for I_LDINP that translates (at runtime) field prompts, window titles, and field selection windows to a specified language (English, French, and Spanish currently supported).<br />
**Platforms:** Windows; Unix; OpenVMS<br />
**Products:** Synergy DBL<br />
**Minimum Version:** 6<br />
**Author:** Ken Beers
<hr>

**Additional Information:**
TRANSLATING WINDOWS

----------------------------------------------------------------------
About the Program
----------------------------------------------------------------------
Name: translate.dbl
Description: A test program for the translation of windows prompts
and titles
Author: Kenneth J. Beers, Synergex
Date: 1/5/1999 (10:36am)

----------------------------------------------------------------------
1. The problem
----------------------------------------------------------------------
One of the more annoying and time consuming tasks a growing number of
customers have encountered is maintaining separate script files for
the windows in their applications. The need to have windows displayed
in different languages depending on the deployment site has required
multiple sources (script files) for each different language. Since
one of the tenets of software engineering is to NEVER have things
defined in different locations, this paradigm seems to fly in the face
of sound practice. But what is to be done? Until nirvana is upon us,
and automatic translations become a transparent function of the
low-level architecture, we have to put up with this "minor" inconvenience.
Or do we?

----------------------------------------------------------------------
2. The proposal
----------------------------------------------------------------------
Submitted for your approval is a replacement for the Toolkit routine
I_LDINP. It is named TKP_ILDINP and has exactly the same argumentation
as I_LDINP. To replace it in your routines you may:

* rename it (to I_LDINP) and link it in prior to WND:tklib.elb, thus
replacing the original version
* .define I_LDINP, "TKP_ILDINP" in a globally included file and
recompile your sources
* use some "super" editor to globally replace I_ldinp with tkp_ildinp
in your sources

What does it do, and why would you want to use it? Simply put, it
translates, at runtime, prompts, window titles, and text(not really,
but we will get into that later!) that is visible on the window. In
addition, it changes any selection windows associated with fields to
the correct language version. The change is based on the contents of
an environment variable (LANGUAGE), so that each individual can have
their own preference, at the same site, yet still be running the same
software.

The included version supports three languages, English, French, and
Spanish, with English being the default. Changing the routine to support
other languages should be trivial for any toolkit programmer.

----------------------------------------------------------------------
3. The price
----------------------------------------------------------------------

Nothing is free. To utilize this routine there are some standards
that must be followed, and some limitations on the size of translated
text. The translatable text for prompts and window text is kept in the
user_text field(A80), sothat is the upper limit for text. The
translatable text for the title is stored in the title field itself(A80),
so that is the upper limit for titles. Selection window names must be 12
characters or less to allow for the translation suffix. And, of course,
you still have to provide the translated text.

But there is an up side to it all. Now all of your information for a
given window can reside with that window. All of the information about a
field is kept with that field definition in the repository, which makes
it globally available and changeable.

----------------------------------------------------------------------
4. The paradigm
----------------------------------------------------------------------
To take advantage of this methodology, there are several conventions that
must be followed

4.1. Prompts

The translatable text for prompts must be stored in the user_text field
and be delimited as defined. The current routine requires the French
version to be enclosed in square brackets ([]), and the Spanish version
to be enclosed in squiggly brackets ({}). One caveat remains, be sure
that the size of the English prompt is large enough to accommodate the
other translations.

4.2. Titles

The translatable text for titles is put directly in the title and
delimited as defined above.

4.3. Selection windows

Each selection window must be named with the last three characters an
underscore followed by the first two letters of the language. A separate
selection window must be created for each language. If a single selection
window will suffice (only numbers?) the original version can be used. If
the replacement selection window is not found in the same library as the
input window, the field is left unmodified.

4.4. Window Text

And here is where the Twilight Zone emerges. As was previously noted,
there is no way to really change the window text at runtime, nor is there
a place to store the translated text. To get around this limitation, do
not use window text, but use a local field. There are a couple of simple
rules:

* Create a local field on your window (A1 is sufficient)
* Use the prompt field to be your English version
* Store the French and/or Spanish version in the user_text field (it is
found in the long properties list)
* Position the prompt where required on the screen
* Position the input frame under the first field on the window
* Using the Object Manager, move the field to the beginning of the window
fields definition, thereby having the succeeding fields cover the input
portion of the field. Positional placing of objects will change the
display order, making later defined objects visible.

----------------------------------------------------------------------
5. The proof
----------------------------------------------------------------------
There are several files necessary to demonstrate the concept. They are:
* Translate.dbl - this file contains a test .MAIN program and the
TKP_ILDINP .subroutine It can be compiled as a standalone program. It
must be linked against WND:tklib.elb.
* Translate.wsc - this file contains the definition of a simple window
that has English, French and Spanish prompts, titles, text, and selection
windows. It should be scripted into WINLIB.ISM
* Readme.txt - a plain text version of this file.
