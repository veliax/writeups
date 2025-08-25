# forensics
## disko1
This challenge provides an image file. running strings and grepping for pico gets us the flag

picoCTF{1t5_ju5t_4_5tr1n9_be6031da}

## red
This challenge provides a png. 

Running zteg on it, A base64 encoded message can be found.

decoding it returns the flag.

picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}

## Phantom Intruder

This challenge provide a pcap file.

Some of the payloads are distinctly of length 12, looking into them you can see base64 encoded text, decoding it gives us our flag in a jumbled manner, we can very easily reconstruct it manually though. 

I didnt like the fact that I had to manually copy each string one by one from each packet, I also disliked the fact that I've been depending on web based tools for such simple tasks. 

So despite finding the flag I'd like to try and script my way through it by using grep and bash scripting.

tcpdump allows us to view the traffic from within our terminal. we can use filter for payload length however its easier to just grep the output. 

Running:
```bash
tcpdump -A -r myNetworkTraffic.pcap 
```

will display all of the packets & its contents out. 

Seeing as we want to extract all base64 content we need to look into base64's alphabet so that we can select all possible characters, looking into its definition by RFC 4648 we find out that its [A-Z], [a-z], [0-9], [+,/,], [=] where = is used for padding. Padding may or may not exist however for the sake of this challenge we'll assume there are alwayse two padding symbols within the strings we want.


So our regex expression should look like the following: '(([A-Z] | [a-z], | [0-9], | [+,/])*==$)' 

Running:
```bash
tcpdump -A -r myNetworkTraffic.pcap | grep -E -o '([a-z, A-Z, 0-9, +,\/]*==$)' > tmp.txt
```

Returns:
```
fQ==
cGljb0NURg==
ZTEwZTgzOQ==
XzM0c3lfdA==
bnRfdGg0dA==
YmhfNHJfOA==
ezF0X3c0cw==
```

Nice we have all of our strings, all we need to do now is decode them. we can use the util base64 for that.

However I dont want to manually do it, so lets store these values in tmp.txt and run a quick bash script on them.

The easiest way I know of would be to iterate through each line decode and append the results into a file.

Running:
```bash
while read line; do
    echo "$line" | base64 -d >> result.txt;
    echo "" >> result.txt;
done 
```

Returns:
```
}
picoCTF
e10e839
_34sy_t
nt_th4t
bh_4r_8
{1t_w4s

```

Its done! Now all we have to do is manually arrange the flag
picoCTF{1t_w4snt_th4t_34sy_tbh_4r_8e10e839}
