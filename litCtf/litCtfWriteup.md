# Rev
## litNewsApp
A file is provided, looking into its magic numbers we see elf. running it as an executable returns a prompt asking for username & password. I used ghidra to check out the variables and found:

"d0nt_57r1ngs_m3_3b775884"

I should've ran strings xc.

# Web
## lexMaxSecrets
very easy challenge the flag is shown within the html code how ever each flag is wrapped around countless divs. 

Since it's all on one line we cant do :%s/<div>/ within vim because it'll only delete the first instance,
so I simply recorded a macro of that and ran it 200 times.
