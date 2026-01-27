#### INTRO

> Chaos has hit the North Pole. Candy canes litter the floor, elves are tangled in tinsel, and the reindeer have found Santa’s naughty list, the master record of who gets coal. They’re plotting to rewrite it, and if they succeed, Christmas is doomed.
>
> You rush to Santa, half-buried under ornaments and snoring into his eggnog. “Santa! We need you!” you shout. He mumbles, “Little… thumb… tiny… key…” and hands you a USB stick before collapsing, snoring, into the ornaments again.
>
> Stored on the USB is a text file with a hidden secret. The ancient PC guarding the naughty list won’t accept any commands unless you log in as Santa. Can you uncover the secret before the reindeer rewrite Christmas?

#### Attachment
In the mission, there is an attachment: "christmas.txt"

First lets investigate the file:

`> file christmas.txt
christmas.txt: ASCII text`

`> cat christmas.txt`
This looks like a Base64 code, lets put it in cyberchef.

It reveals something containing exif-data... images?
Trying the extract files in cyberchef reveals two images:


<img src="ctf-reindeer1.png" width="200"> and <img src="ctf-reindeer2-passwd.png" width="200">

Now what?
Then i remember a challenge at picoCTF using steghide to extract data from image:
`steghide extract -sf extracted_at_0x0.jpg -p C@ndyC4n3`

The second image containing the word "C@ndyC4n3" looks like a password so i tried it first.

And look at that, success!!
Extracted another file named "credentials.txt"

`❯ cat credentials.txt
username: claus.santa
password: O24{l0V3_m1Lk_4nD_c00k13S}`

And there it is!
