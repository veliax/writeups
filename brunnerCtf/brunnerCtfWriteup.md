# shakeAndBake 
## doughbot
This challenge is quite simple, It provides a zip file. Upon unzipping I recieved a single .bin file that displays the following content:

```
[BOOT] DoughBot 1.2.4
[INFO] Initializing sensor calibration...
[INFO] Sensor calibration complete.
[INFO] Establishing Wi-Fi connection...
[WARN] Wi-Fi unstable, retrying...
[INFO] Uploading diagnostics...
[DEBUG] Loading configuration from EEPROM...
[EEPROM CONFIG DUMP @ 0x2000]
    device_name     = "DoughBot"
    firmware_ver    = 1.2.4
    knead_duration  = 780
    mix_speed       = AUTO
    safety_timeout  = 300
    temp_unit       = "C"
    debug_enabled   = true
    log_mode        = FULL

// dev.note: bootlog_flag=YnJ1bm5lcnttMXgzZF9zMWduYWxzXzRfc3VyZX0=

[CRASH] Unexpected interrupt.
[REBOOT] Attempting recovery boot...
[WARN] ??>!!%0^ [RECV_ERR]  3499$
[WARN] 0x00ff @@@ERROR@:~:~
[BOOT] Safe Mode Active.
```
The flag can be found in the following line:
bootlog_flag=YnJ1bm5lcnttMXgzZF9zMWduYWxzXzRfc3VyZX0=


Its encoded in base64 so we need to decode it

echo YnJ1bm5lcnttMXgzZF9zMWduYWxzXzRfc3VyZX0= | base64 -d

returns
brunner{m1x3d_s1gnals_4_sure}


## where Robots cannot search 

This challenge provides us with an instance to a website. 

The website has a button
        <a href="#" class="button">Check out our location</a>
However this does nothing for us atm

Using the challenges name as a hint I researched about bot prevention techniques & protocols

Turns out that theres a robots exlusion protocol, Looking into it we find out that theres a robots.txt file thats meant to indicate to webcrawlers which portions of a website they are allowed / dissallowed to visit. 

searching for robots.txt within the website returns 

```
User-agent: *
Disallow: /admin
Disallow: /private
Disallow: /hidden
Disallow: /flag.txt
```

Going into /flag.txt returns 
brunner{r0bot5_sh0u1d_nOt_637_h3re_b0t_You_g07_h3re}


## theGreatMainFrameBakeOff
It provides a story about a group of developers encoding a piece of text and it reappearing from an ibm machine years later. 

Additionally the challenge hints on it being something older than ascii and hex. 

The following encoded string was provided:
d0-85-97-89-83-85-d9-6d-85-a2-99-85-a5-85-d9-6d-95-81-97-89-a9-99-81-d4-6d-85-88-e3-6d-a2-c9-6d-a2-89-88-e3-6d-84-95-c1-6d-a2-85-94-81-99-c6-95-89-81-d4-6d-95-d6-6d-f0-f7-f9-f1-6d-85-88-e3-6d-95-c9-6d-84-85-a2-e4-6d-a2-81-e6-6d-c3-c9-c4-c3-c2-c5-c0-99-85-95-95-a4-99-82

Looking into the history of encoding and specifically into ibm encoding algorithms we find out about EBCDIC encoding. 
Using dcode to decode the text returns:
}epiceR_esreveR_napizraM_ehT_sI_sihT_dnA_semarFniaM_nO_0791_ehT_nI_desU_saW_CIDCBE{rennurb

If you look into it cloesly you can notice that its the flag but in reverse.
Piping the text into the rev util returns:
brunner{EBCDIC_Was_Used_In_The_1970_On_MainFrames_And_This_Is_The_Marzipan_Reverse_Recipe}


## insanity 
This challenge directs us towards the landing page for the ctf, within it theres a score that increases whenever you hover on particles moving across the screen. 
Looking at the html though we find this line:
<div class="fixed bottom-0 right-0 m-4 py-2 px-3 bg-black/15 text-white/40 rounded text-xs z-1000 pointer-events-none">Score: 500 / 500 </div>

Searching for score within the existing js file returns:
o.current > f ? c.current.textContent = `Score: ${ o.current }` : c.current.textContent = `Score: ${ o.current } / ${ f } `

o.current and f cannot be directly modified since they are out of scope. 
They must be modified when they are in scope. This can be done by placing breakpoints on the html line that contains the score and then hovering over a particle in order to call the function that increases the score. 

Now that they are in scope we can either decrease f(amount needed) or increase o.current to match f.current.

Doing so returns an alert containg the flag.

brunner{y0u_d1d_1t!!!_n0w_try_t0_4ctu4lly_pl4y_th3_ctf}


## cookieJar
This challenge is quite simple it provides an instance to a website and as the name hints this challenge has to do with cookies. 

curling the website with the verbose option will show us that the server returns the following cookie:

< set-cookie: isPremium=false; Path=/

If we set it to true and revisit the website we recieve:
brunner{C00k135_4R3_p0W3rFu1_4nD_D3l1c10u5!}

# Forensics
## forensicsMemoryLoss
This was my first time dealing memory forensics so This challenge took me a couple of hours to solve.
Initially from some reason I assumed that I'd be able to find the flag by opening the dump folder and looking for image headers and then simply extract it into a different file.

Once that didnt work I ran strings and got the following

    strings memoryloss.dmp | grep -E '(^.*CTF.*\.png$)'

C:\Users\CTF Player\Downloads\2EhbmtYE.png
C:\Users\CTF Player\Downloads\2EhbmtYE.png
C:\Users\CTF Player\Downloads\Untitled.png
9fa41445-7094-4649-89a7-239cfcbf693aC:\Users\CTF Player\Downloads\2EhbmtYE.pngC:\Users\CTF Player\Downloads\2EhbmtYE.png

After searching for a while on how I could extract these files I learned about a tool called volatility.

Running the command 
vol -f memoryloss.dmp windows.filescan.Filescan

Displays the files within memory however the ones we found using strings dont aren't displayed on there.

In short the files were a red herring that I wasted hours trying whilst trying to brute force my way into finding them. After a couple of hours I had to give up on brute forcing it, Instead I decided to read the help page for volatility and research more about the tool. 

If we check out the processes by running 
vol -f memoryloss.dmp windows.pslist

We get:
460     860     ScreenSketch.e  0xb207c45502c0  0       -       1       False   2025-06-07 21:28:26.000000 UTC  2025-06-07 21:28:32.000000 UTC  Disabled


I tried checking handles with pid 460 but got nothing in return.

So I tried displaying all existings handles and grepping the string "screensketch", I recieved the following:

7328    RuntimeBroker.  0xb207c3ab75a0  0x530   File    0x120089        \Device\HarddiskVolume2\Users\CTF Player\AppData\Local\Packages\Microsoft.ScreenSketch_8wekyb3d8bbwe\TempState\{798C16B5-BC0A-49FB-921E-AA0FEE767691}.png

Perfect! we got a hit.
Now all we have to do is hope that this screenshot displays the flag, which it did xd. 

brunner{0h_my_84d_17_w45_ju57_1n_my_m3m0ry}


## forensicsNewOrder
This challenge provides an excel file 

I ran strings and noticed the following 
A0}~zk
[Content_Types].xmlPK
_rels/.relsPK
word/document.xmlPK
word/_rels/document.xml.relsPK
word/vbaProject.binPK
word/media/image1.pngPK
word/media/hdphoto1.wdpPK
word/media/image2.pngPK
word/media/image3.pngPK
word/theme/theme1.xmlPK
word/_rels/vbaProject.bin.relsPK
imce
word/vbaData.xmlPK
word/settings.xmlPK
word/styles.xmlPK
word/webSettings.xmlPK
word/fontTable.xmlPK
docProps/core.xmlPK
docProps/app.xmlPK

The fact that the files had PK at the end of their names caught my interest as they are associated with zip compression. 
Turns out that excel files are just compressed xml files. 

So If we turn the original excel file to .zip we should be able to extract it


# Osint 

## thereIsALovelyLand
This challenge is simple. It Provides an image of a bridge and tells you to submit its name in danish. 

Looking at the exif data:

```
"Exif
http://ns.adobe.com/xap/1.0/
<?xpacket begin="
" id="W5M0MpCehiHzreSzNTczkc9d"?>
<x:xmpmeta xmlns:x="adobe:ns:meta/">
 <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
  <rdf:Description rdf:about="DJI Meta Data"
    xmlns:tiff="http://ns.adobe.com/tiff/1.0/"
    xmlns:exif="http://ns.adobe.com/exif/1.0/"
    xmlns:xmp="http://ns.adobe.com/xap/1.0/"
    xmlns:xmpMM="http://ns.adobe.com/xap/1.0/mm/"
    xmlns:dc="http://purl.org/dc/elements/1.1/"
    xmlns:crs="http://ns.adobe.com/camera-raw-settings/1.0/"
    xmlns:drone-dji="http://www.dji.com/drone-dji/1.0/"
   xmp:ModifyDate="2025-06-13"
   xmp:CreateDate="2025-06-13"
   tiff:Make="DJI"
   tiff:Model="FC7703"
   dc:format="image/jpg"
   drone-dji:AbsoluteAltitude="-24.13"
   drone-dji:RelativeAltitude="+59.40"
   drone-dji:GimbalRollDegree="+0.00"
   drone-dji:GimbalYawDegree="+0.00"
   drone-dji:GimbalPitchDegree="+0.00"
   drone-dji:FlightRollDegree="-1.60"
   drone-dji:FlightYawDegree="+138.60"
   drone-dji:FlightPitchDegree="-17.40"
   drone-dji:CamReverse="0"
   drone-dji:GimbalReverse="0"
   crs:Version="7.0"
   crs:HasSettings="False"
   crs:HasCrop="False"
   crs:AlreadyApplied="False">
  </rdf:Description>
 </rdf:RDF>
</x:xmpmeta>
```                                                                                                    
Gives us nothing to go off of. The bridge in the image has some notable features, its over a river and has a building with a distinct shape next to it.
Since this is a danish ctf I started looking at bridges in denmark and I found a bridge with similar features called sallingsundbroen & It turned out to be the one! 

brunner{sallingsundbroen}


## trainMania
Another easy challenge, It provides a 4 second mp4 vid with blurry footage of a train. The flag is brunner{operator-model-maxspeed}

If we are able to find the operator then the rest should be simple. Theres no exif data shown.
Initially I thought that the train had a symbol with S1 written on it so I tried looking into that and found nothing.

Taking a pic & reverse image searching brings up similar looking trains. Turns out the symbol is SJ, SJ is a swedish train operator company. 

Looking into their train models on wikipedia we find out that its from the model X2000 with a max speed of 200km/h!!

brunner{SJ-X2000-200}


## ScandanaviaAndTheWorld
This challenge provides an image of a cabin surrounding by mountains with 45% of the screen being taken up by bushes. 

The exif data was of no help. Long story short I lucked out and completely sidelined what I believe the creators of this challenge wanted me to do. 

I took a screenshot of the cabin and reverse image searched it, which lead to me finding some guy's personal block showing the same mountains as the one's within the image. Within the blog he says the name of the valley repeatedly "Tromsdalen". going to that valley on google maps brings up the cabin. 

The flag is the google plus code of the cabin.

brunner{J34P+84}

On a sidenote I tried running steghide on the image and found out that its password protected. I suspected that it was since I ran strings initially and found the following:

%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz

Which apparently appears commonly with files that are modified with by steghide. 



