fuzz cheat sheet

ffuf -u 'url+FUZZ' -w wordlist_here

-u 'url+FUZZ'

-w wordlist.txt

-t how many threads

-mc all -> match all status codes, means report every response code

-ic -> means ignore case, fuzzing case-insensitive

-fw 23 -> filter out any responses that are 23 bytes in length
