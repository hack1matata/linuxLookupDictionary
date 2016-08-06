#***********************************************************
# How to setup lookup dictionary on a linux environment

#***********************************************************
# Solution 1
I have recently setup my linux to display a notification for any word I select. The solution wasn't strateforward but I worked it out.

Here are the steps I followed to get it done: 

1. I started with http://hackerspace.kinja.com/highlight-and-google-translate-any-text-in-linux-1648824665
	(by the way a really helpfull tutorial)
2. You will find soon that google services are not free anymore. A solution would be to create an account for yourself. 
	In my case I consider it a bit cumbersome so I started to search for other free APIs.
3. I found this one that is free and quite easy to use: http://developer.pearson.com/apis/dictionaries
	it also offers a poisibility to test it directly from the page(click on Get icon to fill in the request params)

The only thing that I changed from the tutorial above is the call to api: 

from

notify-send -u critical "$(xsel -o)" "$(wget -U "Mozilla/5.0" -qO - "http://translate.google.com/translate_a/t?client=t&text=$(xsel -o | sed "s/[\"'<>]//g")&sl=auto&tl=eng" | sed 's/\[\[\[\"//' | cut -d \" -f 1)"

to 

notify-send -u critical "$(xsel -o)" "$(wget -U "Mozilla/5.0" -qO - "http://api.pearson.com/v2/dictionaries/ldoce5/entries?search=$(xsel -o)" | grep -Po '"definition":.*?[^\\]",' | sed 's/"].*//g' | sed 's/.*\["/\\-/g' | sed -e 's/.\{50\}/&-\n/g' )"

or even better

zenity --timeout=50 --width=600 --height=200 --info --title="$(xsel -o)" --text="$(wget -U "Mozilla/5.0" -qO - "http://api.pearson.com/v2/dictionaries/ldoce5/entries?&search=$(xsel -o)" | grep -Po '("definition"|"ipa"|"headword"):.*?[^\\]",' | sed 's/"].*//g' | sed 's/"}.*/=====/g' | sed 's/",.*/=====/g' | sed 's/.*\["/\\- /g' | sed 's/.*:"/=====/g')"

Personaly I recomend using zenity it is much better and easier to configure. I found it difficult to configure notify-send dialog to print more than 6 lines. This is why I started to use zenity.

#***********************************************************
 It does not display the translation into a different language but it prints the definition of the selected words one might want to understand better. 

 It is also posible to translate to different languages but for this you may need to create an account API that might offer this.

#***********************************************************
# Solution 2
While I was writting this wiki a new sollution struck into my mind. If it is possible to open a browser on a spefiic URL then it is possible to use any dictionary be that free or non free. 

This solution frees your hands from having to open you usual translation page(Goodle for example) and copy paste the word to translate. It will instantly open the page with the word already filled. 

It follows the same principle as in solution 1. The only difference is in the script. 

I usually use two dictionaries while reading, one Romanian(definitions) and one English -> Romanian. Below is the code used in each:

romanian_dict: 
xdg-open https://dexonline.ro/definitie/$(xsel -o)

english_dict:
xdg-open https://translate.google.com/#en/ro/$(xsel -o)

It is possible to use it with any configurations,  just take the url in the english_dict to mathc the languages you like: 
https://translate.google.com/#fr/no/test -- this is French -> Norwegian