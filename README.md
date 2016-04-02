mirrored from: https://www.unix-ag.uni-kl.de/~auerswal/ssocr/
please check that address for any updated version

Would happily transfer ownership to @auerswal.

<h1><em>S</em>even <em>S</em>egment <em>O</em>ptical <em>C</em>haracter
      <em>R</em>ecognition</h1>
<p><img src="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/six_digits.png" alt="image of six seven segment digits">
  <br> Use <code>ssocr -T</code> to recognize the above image.
</p>
<h2>Overview</h2>
<p><em>S</em>even <em>S</em>egment <em>O</em>ptical <em>C</em>haracter
  <em>R</em>ecognition or <em>ssocr</em> for short is a program to recognize digits of a seven segment display. An image of one row of digits is used for input and the recognized number is written to the standard output. The program runs on
  <a href="http://www.gnu.org/">GNU</a>/<a href="http://www.kernel.org">Linux</a>, Mac OS X (using
  <a href="http://brew.sh/">Homebrew</a> to install
  <a href="http://docs.enlightenment.org/api/imlib2/html/index.html">Imlib2</a>), and even on Windows (using
  <a href="http://cygwin.com">Cygwin</a>). It uses
  <a href="http://docs.enlightenment.org/api/imlib2/html/index.html">Imlib2</a> to access image data.</p>
<h2>Download</h2>
<p>Source code <a href="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/ssocr-2.16.3.tar.bz2">ssocr-2.16.3.tar.bz2</a> (licensed under the terms of the
  <a href="http://www.fsf.org/licensing/licenses/gpl.html" rel="license">GNU GPL</a> version 3 or later).
  <!-- no binary releases any more...
  <br>
  <a href="http://www.debian.org">Debian</a> binary package
  <a href="ssocr_2.9.7-1_i386.deb">ssocr_2.9.7-1_i386.deb</a>
  (built on
  <a href="http://www.debian.org/releases/unstable/">debian/unstable)</a>.
-->
</p>
<h2>Algorithm</h2>
<p>The image is optionally filtered and then transformed into a monochrome representation with the digits as foreground using some form of thresholding. This image is segmented to find the digits and then each digit is recognized individually.</p>
<h3>Segmentation</h3>
<p>Starting at the left margin a column containing some foreground pixels is searched, marking the start of the first digit. After that a column containing only background pixels is searched to find the horizontal stretch of the digit. This process is repeated
  to find the specified number of digits, or until no more digits are found.</p>
<p>The vertical segmentation works similar, but gaps in digits are allowed, because in some digits the middle segment is unset.</p>
<p>This segmentation technique works for a single row of digits only.</p>
<h3>Character Recognition</h3>
<p>Every digit found by the segmentation is classified as follows: A vertical scan is started in the center top pixel of the digit to find the three horizontal segments. Any foreground pixel in the upper third is counted as part of the top segment, those
  in the second third as part of the middle and those in the last third as part of the bottom segment.</p>
<p>To examine the vertical segments two horizontal scanlines starting on the left margin of the digit are used. The first starts a quarter of the digit height from the top, the other from a quarter of the digit height from the bottom. Foreground pixels in
  the left resp. right half represent left resp. right segments.</p>
<p>The recognized segments are then used to identify the displayed digit using a table lookup (implemented as a <code>switch</code> statement).</p>
<p>Since the above algorithm cannot recognize the digit one, a digit that has a width of less than one quarter of it's height is recognized as a one.
</p>
<p>To recognize a decimal point, e.g. of a digital scale, the size of each digit (that was not recognized as a one already) is compared with the maximum digit width and height. If a digit is significantly smaller than that, it is assumed to be a decimal
  point. The decimal point or thousands separators count towards the number of digits to recognize.
</p>
<p>To recognize a minus sign a method similar to recognizing the digit one is used. If a digit is less high than 1/3 of its width, it is considered a minus sign.
</p>
<h3>Visualization of the Algorithm</h3>
<p><img src="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/illustrate_algo.png" alt="grafic illustration of algorithm">
  <br> Image created with <code>ssocr -D</code></p>
<p>In this image the left border of a digit is represented by a red column, the right border as a blue column. Horizontal green lines of digit width show connected vertical digit parts. The gray rectangles represent the digit dimensions.</p>
<p>Pixels found by the vertical scanline are shown in red, green and blue for the top, middle and bottom third. Those found by the horizontal scanlines are shown in red and green for the left and right half of the digit. No scanlines are used to recognize
  a one.</p>
<hr>
<h2>Manual</h2>
<h3>Usage</h3>
<pre>Seven Segment Optical Character Recognition Version 2.16.3
Copyright (C) 2004-2015 by Erik Auerswald &lt;auerswal@unix-ag.uni-kl.de&gt;
This program comes with ABSOLUTELY NO WARRANTY
This is free software, and you are welcome to redistribute it under the terms
of the GNU GPL (version 3 or later)

Usage: ssocr [OPTION]... [COMMAND]... IMAGE

Options: -h, --help               print this message
         -v, --verbose            talk about program execution
         -V, --version            print version information
         -t, --threshold=THRESH   use THRESH (in percent) to distinguish black
                                  from white
         -a, --absolute-threshold don't adjust threshold to image
         -T, --iter-threshold     use iterative thresholding method
         -n, --number-pixels=#    number of pixels needed to recognize a segment
         -i, --ignore-pixels=#    number of pixels ignored when searching digit
                                  boundaries
         -d, --number-digits=#    number of digits in image (-1 for auto)
         -r, --one-ratio=#        height/width ratio to recognize a 'one'
         -m, --minus-ratio=#      width/height ratio to recognize a minus sign
         -o, --output-image=FILE  write processed image to FILE
         -O, --output-format=FMT  use output format FMT (Imlib2 formats)
         -p, --process-only       do image processing only, no OCR
         -D, --debug-image[=FILE] write a debug image to FILE or testbild.png
         -P, --debug-output       print debug information
         -f, --foreground=COLOR   set foreground color (black or white)
         -b, --background=COLOR   set foreground color (black or white)
         -I, --print-info         print image dimensions and used lum values
         -g, --adjust-gray        use T1 and T2 as percentages of used values
         -l, --luminance=KEYWORD  compute luminance using formula KEYWORD
                                  use -l help for list of KEYWORDS

Commands: dilation                dilation algorithm (with mask of 1 pixel)
          erosion                 erosion algorithm (with mask of 9 pixels)
          closing [N]             closing algorithm
                                  ([N times] dilation then [N times] erosion)
          opening [N]             opening algorithm
                                  ([N times] erosion then [N times] dilation)
          remove_isolated         remove isolated pixels
          make_mono               make image monochrome
          grayscale               transform image to grayscale
          invert                  make inverted monochrome image
          gray_stretch T1 T2      stretch luminance values
                                  from [T1,T2] to [0,255]
          dynamic_threshold W H   make image monochrome w. dynamic thresholding
                                  with a window of width W and height H
          rgb_threshold           make image monochrome by setting every pixel
                                  with any values of red, green or blue below
                                  below the threshold to black
          r_threshold             make image monochrome using only red channel
          g_threshold             make image monochrome using only green channel
          b_threshold             make image monochrome using only blue channel
          white_border [WIDTH]    make border of WIDTH (or 1) of image have
                                  background color
          shear OFFSET            shear image OFFSET pixels (at bottom) to the
                                  right
          rotate THETA            rotate image by THETA degrees
          mirror {horiz|vert}     mirror image horizontally or vertically
          crop X Y W H            crop image with upper left corner (X,Y) with
                                  width W and height H
          set_pixels_filter MASK  set pixels that have at least MASK neighbor
                                  pixels set (including checked position)
          keep_pixels_filter MASK keeps pixels that have at least MASK neighbor
                                  pixels set (not counting the checked pixel)

Defaults: needed pixels          =  1
          ignored pixels         =  0
          no. of digits          =  6
          threshold              = 50.00
          foreground             = black
          background             = white
          luminance              = Rec709
          height/width threshold =  3
          width/height threshold for minus sign =  2

Operation: The IMAGE is read, the COMMANDs are processed in the sequence
           they are given, in the resulting image the given number of digits
           are searched and recognized, after which the recognized number is
           written to STDOUT.
           The recognition algorithm works with set or unset pixels and uses
           the given THRESHOLD to decide if a pixel is set or not.
           Use - for IMAGE to read the image from STDIN.

Exit Codes:  0 if correct number of digits have been recognized
             1 if a different number of digits have been found
             2 if one of the digits could not be recognized
             3 if successful image processing only
            42 if -h, -V, or -l help
            99 otherwise
  </pre>
<h3>Options</h3>
<dl>
  <dt>-h, --help</dt>
  <dd>Prints the <em>usage</em> message, shows default values, describes program operation and shows possible exit codes.</dd>
  <dt>-v, --verbose</dt>
  <dd>Some messages regarding program execution are printed.</dd>
  <dt>-V, --version</dt>
  <dd>Prints version and copyright information.</dd>
  <dt>-t, --threshold=<em>THRESHOLD</em></dt>
  <dd>Set the luminance threshold used to distinguish black from white.
    <em>THRESHOLD</em> is interpreted as a percentage.</dd>
  <dt>-a, --absolute-threshold</dt>
  <dd>Use the <em>THRESHOLD</em> value without adjustment. Otherwise the
    <em>THRESHOLD</em> is adjusted to the luminance interval used in the image.</dd>
  <dt>-T, --iter-threshold</dt>
  <dd>Use an iterative method (one-dimensional k-means clustering) to determine the threshold used to distinguish black from white. A <em>THRESHOLD</em> value given via <em>-t THRESHOLD</em> sets the starting value.</dd>
  <dt>-n, --number-pixels=<em>NUMBER</em></dt>
  <dd>Set the number of foreground pixels that have to be found in a scanline to recognize a segment. Can be used to ignore some noise in the picture.</dd>
  <dt>-i, --ignore-pixels=<em>NUMBER</em></dt>
  <dd>Set the number of foreground pixels that are ignored when deciding if a column consists only of background or foreground pixels. Can be used to ignore some noise in the picture.</dd>
  <dt>-d, --number-digits=<em>NUMBER</em></dt>
  <dd>Specifies the number of digits shown in the image. If <em>NUMBER</em> is <em>-1</em>, the number of digits is detected automatically.
  </dd>
  <dt>-r, --one-ratio=<em>RATIO</em></dt>
  <dd>Set the height/width ratio threshold to recognize a digit as a
    <em>one</em>. <em>RATIO</em> takes integers only.</dd>
  <dt>-m, --minus-ratio=<em>RATIO</em></dt>
  <dd>Set the width/height ratio to recognize a minus sign.
    <em>RATIO</em> takes integers only.</dd>
  <dt>-o, --output-image=<em>FILE</em></dt>
  <dd>Write the processed image to <em>FILE</em>. Normally no image is written to disk. If a standard extension is used it is interpreted as the image format to use.</dd>
  <dt>-O, --output-format=<em>FORMAT</em></dt>
  <dd>Specify the image format. This format must be recognized by Imlib2, standard filename extensions are used.</dd>
  <dt>-p, --process-only</dt>
  <dd>Process the given commands only, no segmentation or character recognition. Should be used together with --output-image=
    <em>FILE</em>.</dd>
  <dt>-D, --debug-image[=<em>FILE</em>]</dt>
  <dd>Write a debug image showing the results of thresholding, segmentation and character recognition. The image is written to <em>testbild.png</em> unless a filename <em>FILE</em> is given.</dd>
  <dt>-P, --debug-output</dt>
  <dd>Print information helpful for debugging.</dd>
  <dt>-f, --foreground=<em>COLOR</em></dt>
  <dd>Specify the foreground color (either <em>black</em> or <em>white</em>). This automatically sets the background color as well.</dd>
  <dt>-b, --background=<em>COLOR</em></dt>
  <dd>Specify the background color (either <em>black</em> or <em>white</em>). This automatically sets the foreground color as well.</dd>
  <dt>-I, --print-info</dt>
  <dd>Prints image dimensions and range of used luminance values.</dd>
  <dt>-g, --adjust-gray</dt>
  <dd>Use values <em>T1</em> and <em>T2</em> given to command
    <em>gray_stretch</em> as percentages instead of absolut luminance values.
  </dd>
  <dt>-l, --luminance=<em>KEYWORD</em></dt>
  <dd>Controls the kind of luminace computation. Using <em>help</em> as <em>KEYWORD</em> prints the list of keywords with a short description of the used formula. The default should work well.
  </dd>
</dl>
<h4>Luminance Keywords</h4>
<dl>
  <dt>rec601</dt>
  <dd>use gamma corrected RGB values according to ITU-R Rec. BT.601-4:
    <br> 0.299*R + 0.587*G + 0.114*B</dd>
  <dt>rec709</dt>
  <dd>use linear (or gamma corrected, I found both claims and don't have the spec) RGB values according to ITU-R Rec. BT.709:
    <br> 0.2125*R + 0.7154*G + 0.0721*B</dd>
  <dt>linear</dt>
  <dd>use (R+G+B)/3 as done by
    <a href="http://cvtool.sourceforge.net/">cvtool</a> version 0.0.1</dd>
  <dt>minimum</dt>
  <dd>use min(R,G,B) as done by
    <a href="http://www.gnu.org/software/ocrad/ocrad.html">GNU Ocrad</a> 0.14
  </dd>
  <dt>maximum</dt>
  <dd>use max(R,G,B)</dd>
  <dt>red</dt>
  <dd>use red value</dd>
  <dt>green</dt>
  <dd>use green value</dd>
  <dt>blue</dt>
  <dd>use blue value</dd>
</dl>
<h3>Commands</h3>
<dl>
  <dt>dilation</dt>
  <dd>Filter image using <em>dilation</em> algorithm. Any pixel with at least one neighbor pixel set in the source image will be set in the filtered image.</dd>
  <dt>erosion</dt>
  <dd>Filter image using <em>erosion</em> algorithm. Any pixel with every neighbor pixel set in the source image will be set in the filtered image.
  </dd>
  <dt>closing [<em>N</em>]</dt>
  <dd>Filter image using closing algorithm, i.e. <em>erosion</em> and then
    <em>dilation</em>. If a number <em>N</em>&gt;1 is specified, <em>N</em> times <em>dilation</em> and then <em>N</em> times <em>erosion</em> is executed.</dd>
  <dt>opening [<em>N</em>]</dt>
  <dd>Filter image using opening algorithm, i.e. <em>dilation</em> and then
    <em>erosion</em>. If a number <em>N</em>&gt;1 is specified, <em>N</em> times <em>dilation</em> and then <em>N</em> times <em>erosion</em> is executed.</dd>
  <dt>remove_isolated</dt>
  <dd>Remove any foreground pixels without neighboring foreground pixels.</dd>
  <dt>make_mono</dt>
  <dd>Convert the image to monochrome using thresholding. The threshold can be specified with option --threshold and is adjusted to the used luminance interval of the image unless option --absolute-threshold is specified.
  </dd>
  <dt>grayscale</dt>
  <dd>Transform image to gray values using luminance. The formula to compute luminance can be specified using option --luminance.</dd>
  <dt>invert</dt>
  <dd>Set every foreground pixel to background color and vice versa. It is faster to set the foreground color to white than to invert the image.</dd>
  <dt>gray_stretch <em>T1</em> <em>T2</em></dt>
  <dd>Transform image so that the luminance interval [<em>T1</em>,<em>T2</em>] is projected to [0,255] with any value below <em>T1</em> set to 0 and any value above <em>T2</em> set to 255.</dd>
  <dt>dynamic_threshold <em>W</em> <em>H</em></dt>
  <dd>Convert the image to monochrome using <em>dynamic thresholding</em> a.k.a
    <em>local adaptive thresholding</em>. A window of width <em>W</em> and height <em>H</em> around the current pixel is used.</dd>
  <dt>rgb_threshold</dt>
  <dd>Convert the image to monochrome using simple thresholding for every color channel. If any of the red, green or blue values is below the threshold, the pixel is set to black. You should use <em>--luminance=minimum</em> and <em>make_mono</em> or <em>dynamic_threshold</em>    instead.</dd>
  <dt>r_threshold</dt>
  <dd>Convert the image to monochrome using simple thresholding. Only the red color channel is used. You should use <em>--luminance=red</em> and
    <em>make_mono</em> or <em>dynamic_threshold</em> instead.
  </dd>
  <dt>g_threshold</dt>
  <dd>Convert the image to monochrome using simple thresholding. Only the green color channel is used. You should use <em>--luminance=green</em> and
    <em>make_mono</em> or <em>dynamic_threshold</em> instead.
  </dd>
  <dt>b_threshold</dt>
  <dd>Convert the image to monochrome using simple thresholding. Only the blue color channel is used. You should use <em>--luminance=blue</em> and
    <em>make_mono</em> or <em>dynamic_threshold</em> instead.
  </dd>
  <dt>white_border [<em>WIDTH</em>]</dt>
  <dd>The border of the image is set to the foreground color. This border is one pixel wide unless a <em>WIDTH</em>&gt;1 is specified.</dd>
  <dt>shear <em>OFFSET</em></dt>
  <dd><em>Shear</em> the image <em>OFFSET</em> pixels to the right. The
    <em>OFFSET</em> is used at the bottom. Image dimensions do not change, pixels in background color are used for pixels that are outside the image and shifted inside. Pixels shifted out of the image are dropped. Many seven segment displays use slightly
    skewed digits, this command can be used to compensate this.
  </dd>
  <dt>rotate <em>THETA</em></dt>
  <dd><em>Rotate</em> the image <em>THETA</em> degrees clockwise around the center of the image. Image dimensions do not change, pixels rotated out of the image area are dropped, pixels from outside the image rotated into the new image are set to the background
    color.
  </dd>
  <dt>mirror { horiz | vert }</dt>
  <dd><em>Mirror</em> the image either horizontally (<em>horiz</em>) or vertically (<em>vert</em>).
  </dd><dt>crop <em>X</em> <em>Y</em> <em>W</em> <em>H</em></dt>
  <dd>Use only the subpicture with upper left corner (<em>X</em>,<em>Y</em>), width <em>W</em> and height <em>H</em>.</dd>
  <dt>set_pixels_filter <em>MASK</em></dt>
  <dd>Set every pixel in the filtered image that has at least <em>MASK</em> neighbor pixels set in the source image.</dd>
  <dt>keep_pixels_filter <em>MASK</em></dt>
  <dd>Keep only those foreground pixels in the filtered image that have at least <em>MASK</em> neighbor pixels set in the source image (not counting the checked pixel itself).</dd>
</dl>
<h3>Bugs</h3>
<p>Imlib2 (and therefore ssocr) does not work well with Netpbm images.</p>
<h3>Manual Page</h3> Since version 2.8.1 ssocr has a
<a href="http://en.wikipedia.org/wiki/Manual_page_(Unix)">manual page</a>. You can read read it
<a href="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/ssocr-manpage.html">online</a> as well.
<hr>
<h2>History</h2>
<p>This program was developed as a proof of concept to test the recognition algorithm (this still shows in the source code...).</p>
<h3>RSA SecurID Token</h3>
<p><img src="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/inside_box.png" alt="SecurID token inside a box">
  <br> Use <code>ssocr crop 230 195 220 60 -t 20</code> to get the token from the image above.
</p>
<p>Once upon a time a fellow member of the
  <a href="http://www.unix-ag.uni-kl.de/">UNIX-AG</a> got issued an
  <a href="http://www.rsasecurity.com/">RSA SecurID 600</a> token, but did not want to carry it around all the time. The available general OCR software was not able to recognize the digits, mainly because the segments are not connected. This gap was filled
  by ssocr.</p>
<p>Since then, a usb camera points to the token inside a cookie box and ssocr is used to get the number into the computer. A script using this info and a password is then used for login.</p>
<h3>Security Implications</h3>
<p>
  This setup means that the user has no need to carry the token with him and it can even be easily shared with co-workers. The complicated login procedure requiring a password and in this case two token numbers (which means a one minute wait for the next
  number) was incentive enough to replace the two factor authentication by traditional authentication measures. The security of the system is determined by the weakest link, which is not the one-time passcode provided by the token.
</p>
<h3>Version History</h3>
<h4>Versions 1.x.x</h4>
<p>
  The first versions of ssocr did not contain the image manipulation algorithms. A seperate program called ssocrpp (<em>s</em>even
  <em>s</em>egment <em>OCR</em> <em>p</em>re<em>p</em>rocessor) was used instead. Since this program used Imlib2 as well, an intermediate image file had to be used. To overcome this, versions 2.x.x of ssocr include all functionality of ssocrpp.
</p>
<h4>Versions 2.x.x</h4>
<p>
  The second major version of ssocr integrated all functionality in one binary. This was the first publicly released ssocr version. Development concentrated on adding image manipulation functions. No external image manipulation programs were needed any
  more, thus easing use of ssocr on differing <a href="http://www.kernel.org/">Linux</a>
  <a href="http://en.wikipedia.org/wiki/Linux_distribution">distributions</a>.
  <br>Since version 2.9.0 the image can be read from a pipe, easing the use of external image manipulation programs.
  <br>Since version 2.11.0 a decimal point can be detected.
  <br>Since version 2.12.0 hexadecimal digits can be detected.
  <br>Since version 2.13.0 the number of digits can be determined automatically. Recognition of a decimal point and an arbitrary number of digits has been added to read the display of digital scales.
  <br>Version 2.14.0 introduces an alternative version of the digit <em>9</em>, where the lower horizontal segment is not set.
  <br>Version 2.15.0 adds detection of minus signs, thanks to a patch by Cristiano Fontana.
  <br>Version 2.16.0 adds a command to mirror the image horizontally or vertically.
</p>
<h3>Lessons Learned</h3>
<ul>
  <li>
    <a href="http://www.opengroup.org/onlinepubs/009695399/functions/pipe.html">Pipes</a> are
    <a href="http://catb.org/esr/writings/taoup/html/ch01s06.html#id2877684">important</a>.
  </li>
  <li>Sometimes it is easier to implement an image manipulation algorithm yourself than to figure out how to invoke
    <a href="http://www.imagemagick.org/">ImageMagick</a> to perform the task.
  </li>
  <li>Creating a grayscale image is the most important step on the way from a color image to a useful bitmap.
  </li>
</ul>
<hr>
<h2>Links</h2>
<p>A
  <a href="http://www.linuxmagazin.de/heft_abo/ausgaben/2007/05/zufall_unter_beobachtung?category=0">similar Project</a> in <a href="http://www.perl.org/">Perl</a>, published by the German
  <a href="http://www.linux-magazin.de/">Linux Magazin</a>.</p>
<p><a href="http://limid.sitadella.com/">LimID</a>, another project using specialized OCR to read a seven segment display. This actually includes some hardware to push a button on the token.</p>
<p>
</p>
<p><a href="https://github.com/arturaugusto/display_ocr">Display OCR</a>, based on
  <a href="http://opencv.org/">OpenCV-Python</a> and
  <a href="https://code.google.com/p/python-tesseract/">python-tesseract</a>.
</p>
<a href="http://homepage.ntlworld.com/green_bean/coffee/roastlogger/roastlogger.htm">RoastLogger</a>, another project that uses OCR to read seven segment displays (non-
<a href="http://www.gnu.org/philosophy/free-sw.html">free</a>).
<p></p>
<p>Another seven-segment OCR solution is
  <a href="http://diga.me.uk/sevenSegDecode.html">sevenSegDecode</a>. It uses a very simple algorithm that requires precise camera positioning. My more complex algorithm tries to solve the positioning problem in the segmentation step, which attempts to
  locate the individual seven segment displays.
</p>
<p><a href="http://samm.kiev.ua/">Alex Samorukov</a>
  <a href="https://smallhacks.wordpress.com/2012/11/11/reading-codes-from-rsa-secureid-token/">blogged</a> about using ssocr for the original use case of reading the number shown by an RSA SecurID token. :-)</p>
<p>A
  <a href="http://moodledome.org/ssocr/">7-segment data logger</a> Perl script written and
  <a href="http://scitation.aip.org/content/aapt/journal/tpt/53/9/10.1119/1.4935766">used</a> by Alan Bates.</p>
<p><a href="http://spectrum.ieee.org/semiconductors/devices/diy-data-capture-via-web-cam">DIY Data Capture via Web Cam</a> by Paul Wallich mentiones SSOCR, but it could not cope with lighting conditions he encountered. :-(
</p>
<p>Matt Kirchstein imported ssocr version 2.9.7 into a
  <a href="https://github.com/">github</a>
  <a href="https://github.com/mkirchstein/SSOCR">repository</a> and made two minor changes to compile ssocr on Mac OS X. Equivalent changes have been incorporated into ssocr version 2.13.3. I learned about this from someone trying to use ssocr from that
  site and having problems, not from Matt. :-(
  <br> If you have to change something to make ssocr work for you, please
  <a href="mailto:Erik Auerswald &lt;auerswal@unix-ag.uni-kl.de&gt;">tell me</a> so I can improve ssocr for everyone. Thanks.</p>
<p>The <a href="http://fobcam.webhop.net/">FobCAM</a> shows the RSA SecurID token of someone.
  <br> This is page is currently (2009-07-17) offline, but you can try the <a href="http://www.archive.org">Wayback Machine</a>.</p>
<p>My simple <a href="https://www.unix-ag.uni-kl.de/~auerswal/wcggrabber/">image grabber</a> for linux.</p>
<p>A <a href="https://www.unix-ag.uni-kl.de/~auerswal/ssocr/grayscale.html">comparison</a> of formulas to create grayscale images.
</p>
<p>Image processing can be done with
  <a href="http://netpbm.sourceforge.net/">Netpbm</a>,
  <a href="http://www.imagemagick.org/">ImageMagick</a>,
  <a href="http://gmic.sourceforge.net/">GREYC's Magic Image Converter</a>,
  <a href="http://www.graphicsmagick.org/">GraphicsMagick</a>,
  <a href="http://www.exactcode.de/oss/exact-image/">ExactImage</a>, or
  <a href="http://cvtool.sourceforge.net/">cvtool</a>, among others (consider
  <a href="http://www.gimp.org/">GIMP</a> or
  <a href="http://imagej.nih.gov/ij/">ImageJ</a> for interactive use).</p>
<p>The <code>leptonlib</code>, found at
  <a href="http://www.leptonica.com/">Leptonica</a>, is a C library for image processing and analysis. The web site contains some good reading besides library documentation as well. The <a href="http://www.boutell.com/gd/">GD Graphics Library</a> (
  <a href="http://www.libgd.org/">new site</a>) is a comprehensive graphics library.
  <a href="http://www.gegl.org/">GEGL</a> is a newcomer from the GIMP community.
  <a href="http://gfxprim.ucw.cz/">GFXprim</a> is a recent 2D bitmap graphics library with C and Python APIs. Anyway, I'd like a simple wrapper around the different librarys for working with image formats, to easily load an image into memory and access
  the individual pixels (and nothing else), but have not found this yet.
</p>
<p>There are too many C++ libraries for image processing and computer vision. Some of them are:
  <a href="http://libccv.org/">ccv</a>,
  <a href="http://cimg.sourceforge.net/">CImg</a>,
  <a href="http://freeimage.sourceforge.net/">FreeImage</a>,
  <a href="http://opensource.adobe.com/wiki/display/gil/Generic+Image+Library">GIL</a> (part of <a href="http://www.boost.org/">Boost</a>),
  <a href="http://www.itk.org/">Insight Segmentation and Registration Toolkit (ITK)</a>,
  <a href="http://mi.eng.cam.ac.uk/~er258/cvd/index.html">libCVD</a>,
  <a href="http://ltilib.sourceforge.net/doc/homepage/index.shtml">LTI-Lib</a>,
  <a href="http://mimas.sourceforge.net/">Mimas toolkit</a>,
  <a href="http://ti.arc.nasa.gov/tech/asr/intelligent-robotics/nasa-vision-workbench/">NASA Vision Workbench</a> (
  <a href="https://github.com/">github</a>
  <a href="https://github.com/nasa/visionworkbench">repository</a>),
  <a href="http://opencvlibrary.sourceforge.net/">OpenCV</a>,
  <a href="http://openimageio.org/">OpenImageIO</a>,
  <a href="http://smsc.cnes.fr/PLEIADES/lien3_vm.htm">ORFEO Tool Box</a> (
  <a href="http://www.orfeo-toolbox.org">alternative link</a>),
  <a href="http://www.vips.ecs.soton.ac.uk/">VIPS</a>,
  <a href="http://www.codesourcery.com/vsiplplusplus/">VSIPL++</a>,
  <a href="http://vxl.sourceforge.net/">VXL</a>.
</p>
<p><a href="http://marvinproject.sourceforge.net">Marvin</a> seems to be an interesting Java image processing framework.
</p>
<p>A few free OCR programs are
  <a href="http://www.geocities.com/claraocr/">Clara OCR</a>,
  <a href="http://www.corollarium.com/conjecture/">Conjecture</a>,
  <a href="http://www.cuneiform.ru/">Cuneiform</a> (with
  <a href="http://symmetrica.net/cuneiform-linux/yagf-en.html">YAGF</a> GUI),
  <a href="http://www.gnu.org/software/ocrad/ocrad.html">GNU Ocrad</a>,
  <a href="http://jocr.sourceforge.net/">GOCR</a>,
  <a href="http://lem.eui.upm.es/ocre.html">ocre</a>,
  <a href="http://live.gnome.org/OCRFeeder">OCRFeeder</a>,
  <a href="http://code.google.com/p/ocropus/">ocropus</a>,
  <a href="http://code.google.com/p/tesseract-ocr/">Tesseract OCR</a>,
  <a href="http://www.pattern-lab.de/">XPLAB</a>.
</p>
<p>
  <a href="http://unpaper.berlios.de/">unpaper</a> is an interesting program to process scanned paper sheets.
  <a href="http://scantailor.sourceforge.net/">Scan Tailor</a> is an interactive program to post-process scanned images.
</p>
<p>ssocr uses
  <a href="http://docs.enlightenment.org/api/imlib2/html/index.html">Imlib2</a> for image I/O.</p>
