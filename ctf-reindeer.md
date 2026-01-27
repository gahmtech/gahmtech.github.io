#### INTRO

> Chaos has hit the North Pole. Candy canes litter the floor, elves are tangled in tinsel, and the reindeer have found Santa’s naughty list, the master record of who gets coal. They’re plotting to rewrite it, and if they succeed, Christmas is doomed.
>
> You rush to Santa, half-buried under ornaments and snoring into his eggnog. “Santa! We need you!” you shout. He mumbles, “Little… thumb… tiny… key…” and hands you a USB stick before collapsing, snoring, into the ornaments again.
>
> Stored on the USB is a text file with a hidden secret. The ancient PC guarding the naughty list won’t accept any commands unless you log in as Santa. Can you uncover the secret before the reindeer rewrite Christmas?

#### Attachment
In the mission, there is an attachment: "christmas.txt"

First lets investigate the file:

```
> file christmas.txt

christmas.txt: ASCII text
```

Seems like it is just some text in the file

`> cat christmas.txt`
```
--SNIP--
tFKoVug+aq2CL51dE8c3l55DLnNNsyaZRupIEdTG3zn79Zux0RjJrUjmuEm2so2OvVaVy4xcdC1a
+TGxL5/3VanYwnzMxtxZU46VkeiPZl2gjCtVEjVkbccMGoG4pAw+VcncvegaJNwjbA+7ihktCKr8
uAaECJrWEtJmTnNUjOcjSjjCtzEFxWiRxyY+T5lwIwlNELcngO5shiq99tUjKWhZhuo7aQuGHShM
xlBzVieW4JhUyoJFfptbpV3IjT5XoUJ2Qx7fvL1IqGbxTvdGbcLtjOys2dsHfcZFeSwn5CFxUJlu
nGRZs71pGLEs0taoxqU0kX2+aH7SWHJxt70zktZ8pVCm6XOD1oNrqBJJHL5mSu00EpoVnyVAPy0C
SInVSq7m2rjOFpXNUx5lkkj+dug4q+YnlVya2uNse3y9w9aZlKLZL56KwLxlVzzQRyMk8vc2UIkj
xQR8JGn+jDJ+Vc0bF/GzgvFWoHU9WOxNsSn7q149eo3Kx9jl1BQhcqMojUh4/m6hlrjS1PXl7qKL
YZv7q1qjjbFbcy1aJQK3lyCkVYYp2vlOtIBzKZmHPzGmCZFJvjzGaDRAu7bx0oJHbtzE0CBceUxJ
oH1sMbHUUANK+ppWLvcST5sUgWgnlleDU2DmF2Mi76fKO6JFupo+A7YpaBp0LMWs3EUZjFLQLsqL
Jjnua0RLROq7lZgKpIyZaCSSeXt+8eKpIybSNjT9ESe3Ekk6KR1TNaqJ59Su4uyRR1JrdZlig6Jn
L/3jUs6qak43YWqvI3lhvLXqTQhSS6mdfXTTXD5w2DWbOunTsrkbsjIuwc96kpXvqWQCxSRTtJFW
jJvoy80QjhdZPv8AZqs576lKZsyc/MAMVDNk9Cext/tMjHKx4/vVSIqOyI7i3aRmHGB/EtQyoSRn
LvjmBjO1lOd1Zs7VLQ1F8RSR2pt5olbniTuKzcTaFRosw6hpbQsvmzRyPj5mXjIqUjT2l9x0c0Sw
8TozqwapsyvaQkaa3T/YZZJRuSVhj5e9UmzOUYsz7e3khmfzYPkTna3cVblYiFLuKq2832iNIvlB
4bbg0KZM6F9ii2moqg+Z8zttq7mLhJaCXFjJDdC3cnpkelVcj2cl0JINKkkj8xJtpzgU+YSi5O1h
klrK2D5hZs4PFTzj9m09hFs3WYqZSuBk1POX7K4+TTI2hDpIWajnK9k0FnYJI2doXGc7qTZMYN7s
mhjg8xXfbyKnmNlTiiyzbfNkWRY3ZRsVaWpp7i2K0d9FuEkhXcKrluL2iWxRuNWG7KZ3ZzRyi57l
V9Tmb5aohtkU15NNjc9K6JSIvmbrTsw0Q/ytuKLCvcmVS6fJ1qrGTsnqWYblltXjxw/WqT0MpR96
5XI9OM1K3Nb9CPlQR60upZtW8LTWIeNApj6tXQtUcEnaepVmRN3yNupGiehLNbmC2TgMW530CUrs
rzEPGuOMdalo1iQXCxrjy8/8CqWjSLGKv940kNl2O3L23Dbu5WtEc7nZgqsSR3o6Et31AzPbzwyL
95SDQnysvlU4tM9Kt5jqNrFcgbYTg7q9aDufG1Y+zqNFm8a3jVPKLSLjDvVs54KTZUWbzlEZwvIo
--SNIP--
```

This looks like a Base64 code, lets put it in cyberchef and decode from base64.

It reveals something containing exif-data... images?
Trying the extract files in cyberchef reveals two images:


<img src="ctf-reindeer1.png" width="200"> and <img src="ctf-reindeer2-passwd.png" width="200">

Now what?
Then i remember a challenge at picoCTF using steghide to extract data from image:

`steghide extract -sf extracted_at_0x0.jpg -p C@ndyC4n3`

The second image containing the word "C@ndyC4n3" looks like, in CTF's, a password so i tried it first in command above.

And look at that, success!!
Extracted another file named "credentials.txt"

`❯ cat credentials.txt
username: claus.santa
password: O24{--REDACTED FLAG--}`

And there it is!
