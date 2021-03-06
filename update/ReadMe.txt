
OooLilyPond 0.4.2rc7 ReadMe  (February 2017)
====================================================

This new take on the OOoLilyPond project is hosted at:
https://github.com/openlilylib/LO-ly


Changes to OOoLilyPond 0.4.2rc7 since 0.4.0/0.4.1:
===================================================

* fixed: Runtime error - Variable not defined: line 40 CurserPosition=1 
* fixed: wrong size of vector graphics in Impress/Draw 
* additional include paths can be specified 
* added button to save the current code into temporary file and open it in external editor 
* added button to re-import code from temporary file back to editor window 
* added support for the use of SVG graphics format (some restrictions apply)
* size and crop settings of existing graphics can be preserved
* user now can choose between two different ways to import graphic into document 
* font name and font size for editor windows can be specified 


1.) fixed: Runtime error - Variable not defined: line 40 CurserPosition=1
--------------------------------------------------------------------------

Apparently, a small bug in OOoLilyPond prevented the correct error handling, if the expected graphics file (produced by LilyPond) could not be found. Instead, the macro stopped with the mentioned runtime error. This has now been fixed. 

Up to LilyPond 2.18, both an EPS file and a PNG file were present if LilyPond had been executed in PNG format. In LilyPond 2.19, this seems to have changed, only a PNG file is produced. 
Unfortunatly, OOoLilyPond always checked for the presence of an EPS file and stopped if there was none. 
This now has been changed as well: Whatever format is chosen (EPS, PNG or SVG), OOoLilyPond checks if the corresponding graphics file is present.


2.) Additional include paths can be specified 
----------------------------------------------

In the configuration dialog, there is a text field where you can specify statements for additional include paths. Its entire contents is literally passed to LilyPond as a parameter. 
It can simply be left empty, or it can contain one or multiple include path definitions (i.e. the -I parameter) as described here:
http://lilypond.org/doc/v2.18/Documentation/usage/command_002dline-usage#basic-command-line-options-for-lilypond

for example:
  -I"C:/Users/JohnDoe/" -I"C:/Program Files (x86)/"
(Even in Windows, LilyPond expects the paths given with forward slashes instead of backward slashes. OOoLilyPond will adapt the path statements, if necessary.)


3.) Save the current code into temporary file / re-import code from temporary file
---------------------------------------------------------------------------------

Working in the small editor window can be somewhat cumbersome. 
However, it only contains the portion of the code between the %{OOoLilyPondCode%} and %{OOoLilyPondEnd%} tags. 
Copying its contents into an external editor like Frescobaldi is possible, but compiling will not always lead to sensible results, because everything else from the template (including other fields, e.g. staff size) is missing. 

The "Open as temp. file in Ext. Editor" will therefore merge the contents of the editor fields into the template code and save the result to a temporary file "OOoLilyPondTemp.ly", which is now fully compilable. 
This file will be opened in the editor specified in the config dialog.
Your changes must be saved to that file.

The "Import from temp. file" button will read "OOoLilyPondTemp.ly" and update the contents of the code field and the "LineWidth", "Staff Size", "Custom1" and "Custom2" fields in the editor window.
Important: Only those five fields are updated. Changes to other parts of the template will be ignored! 
(If necessary, you can create your own templates or modify existing ones.) 


4.) Support for EPS/PNG/SVG graphics format, image size & crop settings, new templates 
---------------------------------------------------------------------------------------

In the config dialog, OOoLilyPond lets you choose whether to use EPS, PNG or SVG graphics format. 
However, there are some restrictions:

PNG
----
PNG should work fine with both office versions (OpenOffice, LibreOffice) on both platforms (Linux, Windows). But as PNG is a bitmap format, it does not look as good as EPS or SVG when printed on paper. 

EPS
----
EPS does *not* work with LibreOffice. (Maybe this can be fixed in LibreOffice versions later than 5.2.4.2 - who knows...) 
On Windows, you will see no graphic at all. On Linux, in your document a graphic will show up which, however, has the same quality drawbacks as a PNG graphic. 
EPS works fine with OpenOffice 4.1.3, even if the graphics are not visible in the OpenOffice window. If you "print" your document with a suitable pdf printer, e.g. FreePDF ( http://freepdfxp.de/index_en.html ), the resulting pdf output file will be correct. 

SVG
----
SVG will only work with LibreOffice and *not* with OpenOffice. 
For SVG use, some more things have to be considered: 

* OOoLilyPond templates from versions 0.4.0 and 0.4.1 use "lilypond-book-preamble.ly" which *only* works with EPS or PNG format, and *not* with SVG (in that case, no graphics output file is produced). 

* For the use with SVG format, you will find [SVG] versions of all templates which you can use instead. They don't use "lilypond-book.preamble.ly". (Such a template change should work fine, even when editing existing snippets. Just be sure not to have your code replaced by default code.)
However, without lilypond-book-preamble.ly, there is no automatic cropping of the resulting image. 
Therefore, you must specify the "paper" size for your LilyPond snippet before compiling. As line-width must be given anyway, only the paper height would have to be found by trial-and-error. So maybe this isn't as bad as is sounds... 
By the way: in LibreOffice, you can manually crop the image after inserting. 

* If you edit an existing snippet, you can now choose to keep its size and crop settings. The default behaviour for that can be adjusted in the config dialog (independent settings for Writer and Impress/Draw). 
(By some unknown magic, png graphics appear auto-cropped even without lilypond-book-preamble. Therefore, switching between different graphic formats can mess up the image size when keeping size&crop settings. If that happened, de-select this option and compile your snippet again.)

* If LilyPond uses fonts that are not installed on your system, they won't show up correctly in the resulting SVG image. Therefore, the [SVG] templates have some preparation to replace LilyPond's default fonts. 
When editing the templates, in the \paper block you will find some code with a "define fonts" statement. Remove the block comment sign (opening brace followed by a percent sign) and adjust the font name according to your needs.


By the way: Up to 0.4.2rc4, when importing vector graphics (EPS, SVG) into OpenOffice Draw or LibreOffice Draw, images had the wrong size. This has been fixed now.


5.) Two different ways to import graphic into document 
-------------------------------------------------------

Up to Version 0.4.0, graphics were imported to the clipboard and then pasted into the document. In LibreOffice, this can cause problems, e.g. when inserting as SVG into Draw. 
For 0.4.1, the developer provided a new method for direct inserting without clipboard use. In OpenOffice it can happen that temporary graphic files are not released after use and therefore cannot be deleted. In LibreOffice it works fine. 
In 0.4.2 both methods are offered. The option dialog lets you define which method to use in Writer and which one for Draw/Impress.



What if I get an error "There is no EPS/PNG/SVG output"?
---------------------------------------------------------

For further information about what has occured, you can read LilyPond's compiler messages by clicking the "Ly Output" button. 

* Do you have severe errors in your LilyPond code that prevented successful compiling?
You can also jump to the reported errors with the "arrow" buttons at the bottom left of the editor window.

* Did you try to produce SVG output, and your template uses "lilypond-book-preamble.ly" (see above)? The preamble prevents the creation of a SVG file.

* In SVG mode, if your "paper" size for the music snippet is too small, LilyPond will split up its output into multiple images named "OOoLilyPond-page-1.svg", "OOoLilyPond-page-2.svg" etc. Those are not recognized, hence the error message. 

You can also specify "page-count = 1" to prevent the creation of multiple images. But if your paper size is to small, there's no image at all. 
Check Ly output for messages like "tried to space systems on a bad number of pages" or "number of pages is out of bounds".


Sources
========

This release is based on the two most recent versions that have been published by the original developer Samuel Hartmann.

Version 0.4.0:
---------------
http://ooolilypond.sourceforge.net/
https://sourceforge.net/projects/ooolilypond/
http://extensions.openoffice.org/de/project/ooolilypond
(direct download:)
https://downloads.sourceforge.net/project/ooolilypond/ooolilypond/OOoLilyPond-0.4.0/OOoLilyPond-0.4.0.oxt?r=&ts=1484832348&use_mirror=netix

Version 0.4.1rc1:
------------------
https://sourceforge.net/p/ooolilypond/discussion/614285/thread/d200563d/#52fd/5aef
(direct download:)
http://www.hartmann-weine.ch/OOoLilyPond-0.4.1rc1.oxt


Credits
========

Finally: A big big THANK YOU to Samuel Hartmann, the original developer of OOoLilyPond, and to everyone else who has contributed to the project. This is a phantastic tool that has simplified the daily workflow of so many people. 
I am one of them. 


Klaus Blum, February 2017
